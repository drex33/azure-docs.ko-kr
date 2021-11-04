---
title: PowerShell을 사용하여 Azure Image Builder로 Windows VM 만들기
description: Azure Image Builder PowerShell 모듈을 사용하여 Windows VM을 만듭니다.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cb061e152e34cc83b210907cc2e43017d85e1c97
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449130"
---
# <a name="create-a-windows-vm-with-azure-image-builder-using-powershell"></a>PowerShell을 사용하여 Azure Image Builder로 Windows VM 만들기

**적용 대상:** :heavy_check_mark: Windows VM 

이 문서에서는 Azure VM Image Builder PowerShell 모듈을 사용하여 사용자 지정된 Windows 이미지를 만드는 방법을 보여 줍니다.


## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

PowerShell을 로컬로 사용하도록 선택하는 경우 이 문서에서는 Az PowerShell 모듈을 설치하고 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 Azure 계정에 연결해야 합니다. Az PowerShell 모듈을 설치하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

여러 Azure 구독이 있는 경우 리소스에 대한 요금이 청구되는 적절한 구독을 선택합니다. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet을 사용하여 특정 구독을 선택합니다.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

### <a name="register-features"></a>기능 등록

아직 등록되지 않은 경우 Azure 구독에 사용할 다음 리소스 공급자를 등록합니다.

- Microsoft.Compute
- Microsoft.KeyVault
- Microsoft.Storage
- Microsoft.Network
- Microsoft.VirtualMachineImages

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute, Microsoft.KeyVault, Microsoft.Storage, Microsoft.VirtualMachineImages, Microsoft.Network |
  Where-Object RegistrationState -ne Registered |
    Register-AzResourceProvider
```

## <a name="define-variables"></a>변수 정의

여러 가지 정보를 반복해서 사용하게 됩니다. 정보를 저장할 변수를 만듭니다.

```azurepowershell-interactive
# Destination image resource group name
$imageResourceGroup = 'myWinImgBuilderRG'

# Azure region
$location = 'WestUS2'

# Name of the image to be created
$imageTemplateName = 'myWinImage'

# Distribution properties of the managed image upon completion
$runOutputName = 'myDistResults'
```

Azure 구독 ID에 대한 변수를 만듭니다. `subscriptionID` 변수에 구독 ID가 포함되어 있는지 확인하기 위해 다음 예제에서 두 번째 행을 실행할 수 있습니다.

```azurepowershell-interactive
# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id
Write-Output $subscriptionID
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet을 사용하여 [Azure 리소스 그룹](../../azure-resource-manager/management/overview.md)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다.

다음 예제에서는 `$location` 변수에 지정된 지역의 `$imageResourceGroup` 변수 이름을 기반으로 리소스 그룹을 만듭니다. 해당 리소스 그룹은 이미지 구성 템플릿 아티팩트와 해당 이미지를 저장하는 데에 사용됩니다.

```azurepowershell-interactive
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="create-user-identity-and-set-role-permissions"></a>사용자 ID 만들기 및 역할 권한 설정

다음 예제를 사용하여 지정된 리소스 그룹에서 이미지를 만들 수 있는 Azure Image Builder 권한을 부여합니다. 이 권한이 없으면 이미지 빌드 프로세스가 성공적으로 완료되지 않습니다.

역할 정의 및 ID 이름에 대한 변수를 만듭니다. 이 기본값은 고유해야 합니다.

```azurepowershell-interactive
[int]$timeInt = $(Get-Date -UFormat '%s')
$imageRoleDefName = "Azure Image Builder Image Def $timeInt"
$identityName = "myIdentity$timeInt"
```

사용자 ID를 만듭니다.

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName
```

변수에 ID 리소스 및 보안 주체 ID를 저장합니다.

```azurepowershell-interactive
$identityNameResourceId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```

### <a name="assign-permissions-for-identity-to-distribute-images"></a>ID에서 이미지를 배포하기 위한 권한 할당

.json 구성 파일을 다운로드하고 이 문서에 정의된 설정에 따라 수정합니다.

```azurepowershell-interactive
$myRoleImageCreationUrl = 'https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json'
$myRoleImageCreationPath = "$env:TEMP\myRoleImageCreation.json"

Invoke-WebRequest -Uri $myRoleImageCreationUrl -OutFile $myRoleImageCreationPath -UseBasicParsing

$Content = Get-Content -Path $myRoleImageCreationPath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $imageResourceGroup
$Content = $Content -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName
$Content | Out-File -FilePath $myRoleImageCreationPath -Force
```

역할 정의를 만듭니다.

```azurepowershell-interactive
New-AzRoleDefinition -InputFile $myRoleImageCreationPath
```

Image Builder 서비스 주체에 역할 정의를 부여합니다.

```azurepowershell-interactive
$RoleAssignParams = @{
  ObjectId = $identityNamePrincipalId
  RoleDefinitionName = $imageRoleDefName
  Scope = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
}
New-AzRoleAssignment @RoleAssignParams
```

> [!NOTE]
> "_AzRoleDefinition: 역할 정의 제한이 초과되었습니다. 더 이상 역할 정의를 만들 수 없습니다._ " 오류 메시지가 나타나면 [Azure RBAC 문제 해결](../../role-based-access-control/troubleshooting.md)을 참조하세요.

## <a name="create-an-azure-compute-gallery-formerly-known-as-shared-image-gallery"></a>Azure 계산 갤러리 만들기 (이전의 공유 이미지 갤러리)

갤러리를 만듭니다.

```azurepowershell-interactive
$myGalleryName = 'myImageGallery'
$imageDefName = 'winSvrImages'

New-AzGallery -GalleryName $myGalleryName -ResourceGroupName $imageResourceGroup -Location $location
```

갤러리 정의를 만듭니다.

```azurepowershell-interactive
$GalleryParams = @{
  GalleryName = $myGalleryName
  ResourceGroupName = $imageResourceGroup
  Location = $location
  Name = $imageDefName
  OsState = 'generalized'
  OsType = 'Windows'
  Publisher = 'myCo'
  Offer = 'Windows'
  Sku = 'Win2019'
}
New-AzGalleryImageDefinition @GalleryParams
```

## <a name="create-an-image"></a>이미지 만들기

Azure Image Builder 원본 개체를 만듭니다. 유효한 매개 변수 값은 [Azure PowerShell을 사용하여 Azure Marketplace에서 Windows VM 이미지 찾기](./cli-ps-findimage.md)를 참조하세요.

```azurepowershell-interactive
$SrcObjParams = @{
  SourceTypePlatformImage = $true
  Publisher = 'MicrosoftWindowsServer'
  Offer = 'WindowsServer'
  Sku = '2019-Datacenter'
  Version = 'latest'
}
$srcPlatform = New-AzImageBuilderSourceObject @SrcObjParams
```

Azure Image Builder 배포자 개체를 만듭니다.

```azurepowershell-interactive
$disObjParams = @{
  SharedImageDistributor = $true
  ArtifactTag = @{tag='dis-share'}
  GalleryImageId = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup/providers/Microsoft.Compute/galleries/$myGalleryName/images/$imageDefName"
  ReplicationRegion = $location
  RunOutputName = $runOutputName
  ExcludeFromLatest = $false
}
$disSharedImg = New-AzImageBuilderDistributorObject @disObjParams
```

Azure Image Builder 사용자 지정 개체를 만듭니다.

```azurepowershell-interactive
$ImgCustomParams01 = @{
  PowerShellCustomizer = $true
  CustomizerName = 'settingUpMgmtAgtPath'
  RunElevated = $false
  Inline = @("mkdir c:\\buildActions", "mkdir c:\\buildArtifacts", "echo Azure-Image-Builder-Was-Here  > c:\\buildActions\\buildActionsOutput.txt")
}
$Customizer01 = New-AzImageBuilderCustomizerObject @ImgCustomParams01
```

두 번째 Azure Image Builder 사용자 지정 개체를 만듭니다.

```azurepowershell-interactive
$ImgCustomParams02 = @{
  FileCustomizer = $true
  CustomizerName = 'downloadBuildArtifacts'
  Destination = 'c:\\buildArtifacts\\index.html'
  SourceUri = 'https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html'
}
$Customizer02 = New-AzImageBuilderCustomizerObject @ImgCustomParams02
```

Azure Image Builder 템플릿을 만듭니다.

```azurepowershell-interactive
$ImgTemplateParams = @{
  ImageTemplateName = $imageTemplateName
  ResourceGroupName = $imageResourceGroup
  Source = $srcPlatform
  Distribute = $disSharedImg
  Customize = $Customizer01, $Customizer02
  Location = $location
  UserAssignedIdentityId = $identityNameResourceId
}
New-AzImageBuilderTemplate @ImgTemplateParams
```

완료되면 메시지가 반환되고 `$imageResourceGroup`에 Image Builder 구성 템플릿이 생성됩니다.

템플릿 생성 프로세스가 성공 하는지 확인하기 위해 다음 예제를 사용할 수 있습니다.

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

Image Builder는 또한 백그라운드에서 구독에 준비 리소스 그룹을 만듭니다. 해당 리소스 그룹은 이미지 빌드에 사용됩니다. `IT_<DestinationResourceGroup>_<TemplateName>` 형식으로 되어 있습니다.

> [!WARNING]
> 준비 리소스 그룹을 바로 삭제하지 마세요. 이미지 템플릿 아티팩트를 삭제하면 준비 리소스 그룹도 따라서 삭제됩니다.

이미지 구성 템플릿 제출 중에 서비스에서 오류를 보고하면 다음을 수행합니다.

- [AIB(Azure VM Image Build) 실패 문제 해결](../linux/image-builder-troubleshoot.md)을 참조하세요.
- 다음 예제를 사용하여 템플릿을 삭제한 후 다시 시도하세요.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup
```

## <a name="start-the-image-build"></a>이미지 빌드 시작

이미지 구성을 VM Image Builder 서비스에 제출합니다.

```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

이미지 빌드 프로세스가 완료될 때까지 기다립니다. 이 단계는 최대 1시간이 걸릴 수 있습니다.

오류가 발생하는 경우 [AIB(Azure VM Image Build) 실패 문제 해결](../linux/image-builder-troubleshoot.md)을 검토하세요.

## <a name="create-a-vm"></a>VM 만들기

VM의 로그인 자격 증명을 변수에 저장합니다. 암호는 복잡해야 합니다.

```azurepowershell-interactive
$Cred = Get-Credential
```

빌드한 이미지를 사용해 VM을 만듭니다.

```azurepowershell-interactive
$ArtifactId = (Get-AzImageBuilderRunOutput -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup).ArtifactId

New-AzVM -ResourceGroupName $imageResourceGroup -Image $ArtifactId -Name myWinVM01 -Credential $Cred
```

## <a name="verify-the-customizations"></a>사용자 지정 확인

VM을 만들 때 설정한 사용자 이름 및 암호를 사용하여 VM에 대한 원격 데스크톱 연결을 만듭니다. VM 내에서 PowerShell을 열고 다음 예제와 같이 `Get-Content`을(를) 실행합니다.

```azurepowershell-interactive
Get-Content -Path C:\buildActions\buildActionsOutput.txt
```

이미지 사용자 지정 프로세스 중에 만들어진 파일의 콘텐츠에 따라 출력이 표시되어야 합니다.

```Output
Azure-Image-Builder-Was-Here
```

동일한 PowerShell 세션에서 다음 예제와 같이 `c:\buildArtifacts\index.html` 파일이 있는지 확인하여 두 번째 사용자 지정이 성공적으로 완료되었는지 확인합니다.

```azurepowershell-interactive
Get-ChildItem c:\buildArtifacts\
```

결과는 이미지 사용자 지정 프로세스 중에 다운로드된 파일을 보여 주는 디렉터리 목록 이어야 합니다.

```Output
    Directory: C:\buildArtifacts

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---          29/01/2021    10:04            276 index.html
```


## <a name="clean-up-resources"></a>리소스 정리

이 문서에서 만든 리소스가 필요하지 않은 경우 다음 예제를 실행하여 삭제할 수 있습니다.

### <a name="delete-the-image-builder-template"></a>Image Builder 템플릿 삭제

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

### <a name="delete-the-image-resource-group"></a>이미지 리소스 그룹 삭제

> [!CAUTION]
> 다음 예제에서는 지정된 리소스 그룹과 해당 그룹에 포함된 모든 리소스를 삭제합니다.
> 지정된 리소스 그룹에 이 문서의 범위에 속하지 않는 리소스가 포함된 경우에도 해당 리소스가 삭제됩니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $imageResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 문서에서 사용한 .json 파일 구성 요소에 대한 자세한 내용은 [Image Builder 템플릿 참조](../linux/image-builder-json.md)를 확인하세요.
