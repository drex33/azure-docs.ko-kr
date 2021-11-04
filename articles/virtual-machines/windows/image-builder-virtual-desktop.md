---
title: 이미지 작성기-Azure 가상 데스크톱 이미지 만들기
description: PowerShell에서 Azure 이미지 작성기를 사용 하 여 Azure 가상 데스크톱의 Azure VM 이미지를 만듭니다.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 05/12/2021
ms.topic: article
ms.service: virtual-machines
ms.collection: windows
ms.subservice: image-builder
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 503b2663d6de83d982e6f8e9c2538de3765c9294
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466659"
---
# <a name="create-a-azure-virtual-desktop-image-using-azure-vm-image-builder-and-powershell"></a>Azure VM 이미지 작성기 및 PowerShell을 사용 하 여 Azure 가상 데스크톱 이미지 만들기

**적용 대상:** :heavy_check_mark: Windows VM 

이 문서에서는 다음과 같은 사용자 지정으로 Azure 가상 데스크톱 이미지를 만드는 방법을 보여 줍니다.

* [Fslogix](https://github.com/DeanCefola/Azure-WVD/blob/master/PowerShell/FSLogixSetup.ps1) 설치
* 커뮤니티 리포지토리에서 [Azure 가상 데스크톱 최적화 스크립트](https://github.com/The-Virtual-Desktop-Team/Virtual-Desktop-Optimization-Tool) 를 실행 합니다.
* [Microsoft Teams](../../virtual-desktop/teams-on-avd.md) 설치
* [다시 시작](../linux/image-builder-json.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#windows-restart-customizer)
* [Windows 업데이트](../linux/image-builder-json.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#windows-update-customizer) 실행

Azure VM 이미지 작성기를 사용 하 여이를 자동화 하 고, [Azure 계산 갤러리](../shared-image-galleries.md) (이전의 공유 이미지 갤러리)에 이미지를 배포 하 여 다른 지역에 복제 하 고, 크기를 제어 하 고, 조직 내부 및 외부에서 이미지를 공유할 수 있는 방법을 보여 줍니다.


Image Builder 구성의 배포를 간소화하기 위해 이 예제에서는 Azure Resource Manager 템플릿과 중첩된 Image Builder 템플릿을 함께 사용합니다. 이를 통해 변수 및 매개 변수 입력과 같은 몇 가지 이점을 얻을 수 있습니다. 명령줄에서 매개 변수를 전달할 수도 있습니다.

이 문서는 복사 및 붙여넣기 연습을 위한 것입니다.

> [!NOTE]
> 앱을 설치하는 스크립트는 [GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/14_Building_Images_WVD)에 있습니다. 설명 및 테스트 전용이며 프로덕션 워크로드에는 적합하지 않습니다. 

## <a name="tips-for-building-windows-images"></a>Windows 이미지를 빌드하기 위한 팁 

- VM 크기 - 기본 VM 크기는 `Standard_D1_v2`로, Windows에는 적합하지 않습니다. `Standard_D2_v2` 이상을 사용합니다.
- 이 예제에서는 [PowerShell 사용자 지정자 스크립트](../linux/image-builder-json.md)를 사용합니다. 이러한 설정을 사용해야 하며, 그러지 않으면 빌드가 응답하지 않습니다.

    ```json
      "runElevated": true,
      "runAsSystem": true,
    ```

    예를 들면 다음과 같습니다.

    ```json
      {
          "type": "PowerShell",
          "name": "installFsLogix",
          "runElevated": true,
          "runAsSystem": true,
          "scriptUri": "https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/14_Building_Images_WVD/0_installConfFsLogix.ps1"
    ```
- 코드에 주석 지정 - AIB 빌드 로그(customization.log)는 매우 자세한 정보를 표시합니다. 'write-host'를 사용하여 스크립트를 주석으로 처리하면 로그에 전송되고 문제 해결이 더 쉬워집니다.

    ```PowerShell
     write-host 'AIB Customization: Starting OS Optimizations script'
    ```

- 종료 코드 - AIB는 모든 스크립트에서 0 종료 코드를 반환할 것으로 예상합니다. 0이 아닌 종료 코드를 사용하면 AIB에서 사용자 지정이 실패하고 빌드가 중지됩니다. 복잡한 스크립트가 있는 경우 계측을 추가하고 종료 코드를 내보내면 customization.log에 표시됩니다.

    ```PowerShell
     Write-Host "Exit code: " $LASTEXITCODE
    ```
- 테스트: 독립 실행형 VM에서 먼저 코드를 테스트하고, 사용자 프롬프트가 표시되지 않는지, 올바른 권한을 사용하고 있는지 확인합니다.

- 네트워킹 - `Set-NetAdapterAdvancedProperty`. 최적화 스크립트에서 설정되지만 AIB 빌드를 실패하게 합니다. 네트워크 연결이 끊어지므로 주석 처리됩니다. 현재 조사 중입니다.

## <a name="prerequisites"></a>필수 구성 요소

최신 Azure PowerShell CmdLet이 설치되어 있어야 합니다. 자세한 설치 세부 정보는 [Azure PowerShell 개요](/powershell/azure/overview)를 참조하세요.

```PowerShell
# check you are registered for the providers, ensure RegistrationState is set to 'Registered'.
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

# If they do not show as registered, run the commented out code below.

## Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
## Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
## Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
## Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="set-up-environment-and-variables"></a>환경 및 변수 설정

```azurepowershell-interactive
# Step 1: Import module
Import-Module Az.Accounts

# Step 2: get existing context
$currentAzContext = Get-AzContext

# destination image resource group
$imageResourceGroup="wvdImageDemoRg"

# location (see possible locations in main docs)
$location="westus2"

# your subscription, this will get your current subscription
$subscriptionID=$currentAzContext.Subscription.Id

# image template name
$imageTemplateName="wvd10ImageTemplate01"

# distribution properties object name (runOutput), i.e. this gives you the properties of the managed image on completion
$runOutputName="sigOutput"

# create resource group
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="permissions-user-identity-and-role"></a>권한, 사용자 ID 및 역할 


 사용자 ID를 만듭니다.

```azurepowershell-interactive
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$idenityName="aibIdentity"+$timeInt

## Add AZ PS modules to support AzUserAssignedIdentity and Az AIB
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName

$idenityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).Id
$idenityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).PrincipalId

```

ID에서 이미지를 배포하기 위한 권한을 할당합니다. 이 명령은 앞에서 지정한 매개 변수를 사용하여 템플릿을 다운로드하고 업데이트합니다.

```azurepowershell-interactive
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

> [!NOTE] 
> 오류 'New-AzRoleDefinition: 역할 정의 한도가 초과되었습니다. 역할 정의를 더 이상 만들 수 없습니다.'가 표시되면 [Azure RBAC 문제 해결](../../role-based-access-control/troubleshooting.md)을 참조하세요.


## <a name="create-the-azure-compute-gallery"></a>Azure 계산 갤러리 만들기 

Azure 계산 갤러리가 아직 없는 경우 새로 만들어야 합니다.

```azurepowershell-interactive
$sigGalleryName= "myaibsig01"
$imageDefName ="win10wvd"

# create gallery
New-AzGallery -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup  -Location $location

# create gallery definition
New-AzGalleryImageDefinition -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup -Location $location -Name $imageDefName -OsState generalized -OsType Windows -Publisher 'myCo' -Offer 'Windows' -Sku '10wvd'

```

## <a name="configure-the-image-template"></a>이미지 템플릿 구성

이 예제를 위해 앞에서 지정한 매개 변수를 사용하여 다운로드하고 업데이트할 템플릿이 준비되어 있습니다. 그러면 FsLogix, OS 최적화, Microsoft Teams가 설치되고 마지막에 Windows 업데이트가 실행됩니다.

템플릿을 열면 사용 중인 이미지를 원본 속성에서 볼 수 있습니다. 이 예제에서는 Win 10 다중 세션 이미지를 사용합니다. 

### <a name="windows-10-images"></a>Windows 10 이미지
사용자는 두 가지 주요 유형인 다중 세션 및 단일 세션에 대해 알고 있어야 합니다.

다중 세션 이미지는 풀링된 방식으로 사용됩니다. Azure의 이미지 세부 정보에 대한 예제는 다음과 같습니다.

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "20h2-evd",
"version": "latest"
```

단일 세션 이미지는 개별적으로 사용됩니다. Azure의 이미지 세부 정보에 대한 예제는 다음과 같습니다.

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "19h2-ent",
"version": "latest"
```

다음과 같이 사용 가능한 Win10 이미지를 변경할 수도 있습니다.

```azurepowershell-interactive
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsDesktop -Offer windows-10
```

## <a name="download-template-and-configure"></a>템플릿 다운로드 및 구성

이제 템플릿을 다운로드하고 용도에 맞게 구성해야 합니다.

```azurepowershell-interactive
$templateUrl="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json"
$templateFilePath = "armTemplateWVD.json"

Invoke-WebRequest -Uri $templateUrl -OutFile $templateFilePath -UseBasicParsing

((Get-Content -path $templateFilePath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<rgName>',$imageResourceGroup) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<runOutputName>',$runOutputName) | Set-Content -Path $templateFilePath

((Get-Content -path $templateFilePath -Raw) -replace '<imageDefName>',$imageDefName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<sharedImageGalName>',$sigGalleryName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region1>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$idenityNameResourceId) | Set-Content -Path $templateFilePath

```

언제든지 자유롭게 [템플릿](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json)을 볼 수 있으며 모든 코드를 볼 수 있습니다.


## <a name="submit-the-template"></a>템플릿 제출

템플릿을 서비스에 제출해야 합니다. 그러면 스크립트와 같은 모든 종속 아티팩트가 다운로드되고, 유효한지 검사되고, 사용 권한이 확인된 후 *IT_* 접두사가 추가된 상태로 스테이징 리소스 그룹에 저장됩니다.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName $imageResourceGroup -TemplateFile $templateFilePath -api-version "2020-02-14" -imageTemplateName $imageTemplateName -svclocation $location

# Optional - if you have any errors running the above, run:
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)
$getStatus.ProvisioningErrorCode 
$getStatus.ProvisioningErrorMessage
```
 
## <a name="build-the-image"></a>이미지 빌드
```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName -NoWait
```

> [!NOTE]
> 이 명령은 Image Builder 서비스가 이미지 빌드를 완료할 때까지 기다리지 않으며 사용자는 아래 상태를 쿼리할 수 있습니다.

```azurepowershell-interactive
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)

# this shows all the properties
$getStatus | Format-List -Property *

# these show the status the build
$getStatus.LastRunStatusRunState 
$getStatus.LastRunStatusMessage
$getStatus.LastRunStatusRunSubState
```
## <a name="create-a-vm"></a>VM 만들기
이제 빌드가 완료되었으므로 이미지에서 VM을 빌드할 수 있습니다. [New-AzVM(Az.Compute)](/powershell/module/az.compute/new-azvm#examples)의 예제를 사용하세요.

## <a name="clean-up"></a>정리

먼저 리소스 그룹 템플릿을 삭제합니다. 전체 리소스 그룹을 삭제하지는 않도록 합니다. 이렇게 하면 AIB에서 사용하는 스테이징 리소스 그룹(*IT_* )이 정리되지 않습니다.

이미지 템플릿을 제거합니다.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name wvd10ImageTemplate
```

역할 할당을 삭제합니다.

```azurepowershell-interactive
Remove-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## remove definitions
Remove-AzRoleDefinition -Name "$idenityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## delete identity
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName -Force
```

해당 리소스 그룹을 삭제합니다.

```azurepowershell-interactive
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

[GitHub](https://github.com/azure/azvmimagebuilder/tree/master/quickquickstarts)에 제공되는 추가 예제를 진행해볼 수 있습니다.

