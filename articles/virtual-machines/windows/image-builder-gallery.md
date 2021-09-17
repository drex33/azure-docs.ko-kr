---
title: Windows VM용 이미지 갤러리에서 Azure Image Builder 사용
description: Azure Image Builder 및 Azure PowerShell을 사용하여 Azure 공유 갤러리 이미지 버전을 만듭니다.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6206401d41662724e2c44851e930373b7f90030d
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436431"
---
# <a name="create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Windows 이미지를 만들어 공유 이미지 갤러리에 배포 

**적용 대상:** :heavy_check_mark: Windows VM 

이 문서에서는 Azure Image Builder 및 Azure PowerShell을 사용하여 [공유 이미지 갤러리](../shared-image-galleries.md)에서 이미지 버전을 만든 다음, 전체적으로 이미지를 배포하는 방법을 보여 줍니다. [Azure CLI](../linux/image-builder-gallery.md)를 사용하여 이 작업을 수행할 수도 있습니다.

이미지를 구성하는 데 .json 템플릿을 사용합니다. 사용 중인 .json 파일은 [armTemplateWinSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json)에 있습니다. 이 문서는 로컬 PowerShell 세션을 사용하여 작성되었으므로 템플릿의 로컬 버전을 다운로드하여 편집합니다.

공유 이미지 갤러리에 이미지를 배포하기 위해 이 템플릿에서는 [sharedImage](../linux/image-builder-json.md#distribute-sharedimage)를 템플릿의 `distribute` 섹션 값으로 사용합니다.

Azure Image Builder는 sysprep를 자동으로 실행하여 이미지를 일반화합니다. 이 명령은 필요한 경우 [재정의](../linux/image-builder-troubleshoot.md#vms-created-from-aib-images-do-not-create-successfully)할 수 있는 일반 sysprep 명령입니다. 

사용자 지정 레이어링 횟수를 확인합니다. 단일 Windows 이미지에서 Sysprep 명령을 제한된 숫자내에서 실행할 수 있습니다. Sysprep 제한에 도달한 후 Windows 이미지를 다시 만들어야 합니다. 자세한 내용은 [Sysprep을 실행할 수 있는 횟수 제한](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep)을 참조하세요. 


## <a name="register-the-features"></a>기능 등록
Azure Image Builder를 사용하려면 이 기능을 등록해야 합니다.

공급자 등록을 확인합니다. 각 공급자가 `Registered`를 반환하는지 확인합니다.

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Network | Format-table -Property ResourceTypes,RegistrationState
```

`Registered`를 반환하지 않는 경우 다음을 사용하여 공급자를 등록합니다.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

PowerShell 모듈 설치
```powerShell
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

## <a name="create-variables"></a>변수 만들기

일부 정보를 반복해서 사용하게 되며, 해당 정보를 저장하기 위해 몇 가지 변수를 만듭니다. `username` 및 `vmpassword`와 같은 변수의 값을 원하는 정보로 바꿉니다.

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"

# Create a resource group for Image Template and Shared Image Gallery
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
```


## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>사용자 할당 ID 만들기 및 리소스 그룹에 대한 사용 권한 설정
Image Builder는 제공된 [user-identity](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)를 사용하여 Azure SIG(Shared Image Gallery)에 이미지를 삽입합니다. 이 예제에서는 SIG에 이미지를 배포하는 세분화된 작업을 포함하는 Azure 역할 정의를 만듭니다. 그러면 역할 정의가 user-identity에 할당됩니다.

```powershell
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$identityName="aibIdentity"+$timeInt

## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName

$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```


### <a name="assign-permissions-for-identity-to-distribute-images"></a>ID에서 이미지를 배포하기 위한 권한 할당

해당 명령은 Azure 역할 정의 템플릿을 다운로드하여 해당 템플릿을 이전에 지정하였던 매개 변수로 업데이트합니다.

```powershell
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
New-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

### NOTE: If you see this error: 'New-AzRoleDefinition: Role definition limit exceeded. No more role definitions can be created.' See this article to resolve:
https://docs.microsoft.com/azure/role-based-access-control/troubleshooting
```


## <a name="create-the-shared-image-gallery"></a>Shared Image Gallery 만들기

공유 이미지 갤러리에서 Image Builder를 사용하려면 기존 이미지 갤러리 및 이미지 정의가 있어야 합니다. Image Builder는 이미지 갤러리 및 이미지 정의를 만들지 않습니다.

사용할 갤러리 및 이미지 정의가 아직 없는 경우 만들어야 합니다. 먼저 이미지 갤러리를 만듭니다.

```powershell
# Image gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>템플릿을 다운로드하고 구성합니다.

.json 템플릿을 다운로드하고 변수로 구성합니다.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$identityNameResourceId) | Set-Content -Path $templateFilePath
```


## <a name="create-the-image-version"></a>이미지 버전 만들기

템플릿을 서비스에 제출해야 합니다. 그러면 스크립트와 같은 모든 종속 아티팩트가 다운로드되고, *IT_* 접두사가 추가된 상태로 스테이징 리소스 그룹에 저장됩니다.

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -ApiVersion "2020-02-14" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

이미지를 작성하려면 템플릿에서 '실행'을 호출해야 합니다.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2020-02-14" `
   -Action Run
```

이미지를 만들고 두 지역에 복제하는 데 다소 시간이 걸릴 수 있습니다. VM 만들기를 계속 진행하기 전에 이 단계가 완료될 때까지 기다립니다.

해당 이미지 빌드 상태를 자동으로 가져오는 옵션에 대한 정보는 [추가 정보]를 참조하세요.
```powershell
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

## <a name="create-the-vm"></a>VM 만들기

Azure Image Builder에서 만든 이미지 버전에서 VM을 만듭니다.

만든 이미지 버전을 가져옵니다.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

이미지가 복제된 두 번째 지역에서 VM을 만듭니다.

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>사용자 지정 확인
VM을 만들 때 설정한 사용자 이름 및 암호를 사용하여 VM에 대한 원격 데스크톱 연결을 만듭니다. VM 내에서 cmd 프롬프트를 열고 다음을 입력합니다.

```console
dir c:\
```

이미지를 사용자 지정하는 동안 만들어진 `buildActions`라는 디렉터리가 표시됩니다.


## <a name="clean-up-resources"></a>리소스 정리
이제 이미지 버전을 다시 사용자 지정하여 동일한 이미지의 새 버전을 만들려면 **이 단계를 건너뛰고** [Azure Image Builder를 사용하여 다른 이미지 버전 만들기](image-builder-gallery-update-image-version.md)를 계속 진행합니다.


그러면 생성된 이미지와 다른 모든 리소스 파일이 삭제됩니다. 리소스를 삭제하기 전에 이 배포를 완료했는지 확인합니다.

먼저 리소스 그룹 템플릿을 삭제합니다. 그러지 않으면 AIB에서 사용하는 스테이징 리소스 그룹(*IT_* )이 정리되지 않습니다.

이미지 템플릿의 ResourceID를 가져옵니다. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14"
```

이미지 템플릿을 삭제합니다.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

역할 할당 삭제

```powerShell
Remove-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

정의 제거

```powerShell
Remove-AzRoleDefinition -Name "$identityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

ID 삭제

```powerShell
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName -Force
```

리소스 그룹 삭제

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

만든 이미지 버전을 업데이트하는 방법을 알아보려면 [Azure Image Builder를 사용하여 다른 이미지 버전 만들기](image-builder-gallery-update-image-version.md)를 참조하세요.
