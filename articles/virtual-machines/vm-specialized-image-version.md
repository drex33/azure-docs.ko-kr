---
title: 특수 이미지 버전에서 VM 만들기
description: Azure Compute 갤러리에서 특수 이미지 버전을 사용하여 VM을 만듭니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/05/2021
ms.author: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: b500a7e02167f5373eb2dbdeb4bdb708c5b69966
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474499"
---
# <a name="create-a-vm-using-a-specialized-image-version"></a>특수 이미지 버전을 사용하여 VM 만들기 

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM 

Azure Compute 갤러리에 저장된 [특수 이미지 버전(이전의](./shared-image-galleries.md#generalized-and-specialized-images) Shared Image Gallery)에서 VM을 만듭니다. 일반화된 이미지 버전에서 VM을 만들려는 경우 [일반화된 이미지 버전에서 VM 만들기](vm-generalized-image-version.md)를 참조하세요.

> [!IMPORTANT]
> 
> 특수 이미지에서 새 VM을 만들 때 새 VM은 원래 VM의 컴퓨터 이름을 유지합니다. 다른 컴퓨터 관련 정보(예: CMID)도 유지되며, 경우에 따라 이 중복된 정보로 인해 문제가 발생할 수 있습니다. VM을 복사할 때 애플리케이션이 어떤 유형의 컴퓨터 관련 정보에 의존하는지 알아야 합니다.  

이 예제에서는 필요에 따라 리소스 이름을 바꿉니다. 

### <a name="portal"></a>[포털](#tab/portal)

이제 새 VM을 한 개 이상 만들 수 있습니다. 이 예제에서는 ‘미국 동부’ 데이터 센터에서 *myResourceGroup* 에 *myVM* 이라는 VM을 만듭니다.

1. 이미지 정의로 이동합니다. 리소스 필터를 사용하여 사용 가능한 모든 이미지 정의를 표시할 수 있습니다.
1. 이미지 정의 페이지의 맨 위에 있는 메뉴에서 **VM 만들기** 를 선택합니다.
1. **리소스 그룹** 에 대해 **새로 만들기** 를 선택하고 *myResourceGroup* 을 이름으로 입력합니다.
1. **가상 머신 이름** 에 *myVM* 을 입력합니다.
1. **지역** 에 대해 *미국 동부* 를 선택합니다.
1. **가용성 옵션** 의 경우에는 ‘인프라 중복이 필요하지 않습니다’ 기본값을 그대로 둡니다.
1. 이미지 정의에 대한 페이지에서 시작한 경우 **이미지** 값이 `latest` 이미지 버전으로 자동으로 채워집니다.
1. **크기** 의 경우 사용 가능한 크기 목록에서 VM 크기를 고른 다음 **선택** 을 선택합니다.
1. **관리자 계정** 에서 원본 VM의 사용자 이름 및 자격 증명이 사용되므로 사용자 이름이 회색으로 표시됩니다.
1. VM에 대한 원격 액세스를 허용하려면 **공용 인바운드 포트** 에서 **선택한 포트 허용을** 선택한 다음, 드롭다운에서 **SSH(22)** 또는 **RDP(3389)를** 선택합니다. VM에 대한 원격 액세스를 허용하지 않으려면 **퍼블릭 인바운드 포트** 에 대해 **안 함** 을 선택한 상태로 둡니다.
1. 작업을 마치면 페이지 하단의 **검토 + 만들기** 단추를 선택합니다.
1. VM이 유효성 검사를 통과하면 페이지 하단에서 **만들기** 를 선택하여 배포를 시작합니다.


### <a name="cli"></a>[CLI](#tab/cli)

[az sig image-definition 목록](/cli/azure/sig/image-definition#az_sig_image_definition_list)으로 갤러리의 이미지 정의를 나열하여 정의의 이름과 ID를 확인합니다.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

이미지가 특수 이미지임을 나타내는 --specialized 매개 변수를 사용하는 [az vm create](/cli/azure/vm#az_vm_create)를 사용하여 VM을 만듭니다. 

이미지 정의 ID를 `--image`에 사용하여 사용 가능한 최신 버전의 이미지에서 VM을 만듭니다. 또한 `--image`에 대한 이미지 버전 ID를 제공하여 특정 버전에서 VM을 만들 수 있습니다. 

다음 예제에서는 최신 버전의 *myImageDefinition* 이미지에서 VM을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

특수화된 이미지 버전을 만든 후에 [New-AzVM](/powershell/module/az.compute/new-azvm) cmdlet을 사용하여 하나 이상의 새 VM을 만들 수 있습니다. 

이 예에서는 이미지 정의 ID를 사용하여 새 VM에서 최신 버전의 이미지를 사용할 수 있도록 합니다. `Set-AzVMSourceImage -Id`에 대한 이미지 버전 ID를 사용하여 특정 버전을 사용할 수도 있습니다. 예를 들어 이미지 버전 *1.0.0* 형식을 사용하려면 `Set-AzVMSourceImage -Id "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`을 입력합니다. 

특정 이미지 버전이 지역에서 삭제되거나 제거되어 사용할 수 없는 경우, 해당 이미지 버전을 사용하면 자동화가 실패할 수 있습니다. 특정 이미지 버전이 필요하지 않은 경우 이미지 정의 ID를 사용하여 새 VM을 만드는 것이 좋습니다.

이 예제에서 필요에 따라 리소스 이름을 바꿉니다. 


```azurepowershell-interactive

# Create some variables for the new VM.

$resourceGroup = "mySIGSpecializedRG"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition


# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.

$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
   -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name myNetworkSecurityGroup `
   -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name $vmName `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2 | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig

```

---

**다음 단계**

템플릿을 사용하여 Azure Compute 갤러리 리소스를 만들 수도 있습니다. 다음의 몇 가지 Azure 빠른 시작 템플릿을 사용할 수 있습니다. 

- [Azure Compute 갤러리 만들기](https://azure.microsoft.com/resources/templates/sig-create/)
- [Azure Compute 갤러리에서 이미지 정의 만들기](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Azure Compute 갤러리에서 이미지 버전 만들기](https://azure.microsoft.com/resources/templates/sig-image-version-create/)
