---
title: Azure의 테넌트 간에 갤러리 이미지 공유
description: 공유 이미지 갤러리 및 PowerShell을 사용하여 Azure 테넌트 간에 VM 이미지를 공유하는 방법을 알아봅니다.
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/15/2019
ms.reviewer: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eb432b486815b9b2269eabb2631d4d4cb909df11
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225989"
---
# <a name="share-gallery-vm-images-across-azure-tenants-using-powershell"></a>PowerShell을 사용하여 Azure 테넌트 간에 갤러리 VM 이미지 공유

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

공유 이미지 갤러리를 사용하면 Azure RBAC를 사용하여 이미지를 공유할 수 있습니다. Azure RBAC를 사용하여 테넌트 내에서 이미지를 공유하고 테넌트 외부의 개인에게도 이미지를 공유할 수 있습니다. 이 간단한 공유 옵션에 대한 자세한 내용은 [갤러리 공유](./shared-images-portal.md#share-the-gallery)를 참조하세요.

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


> [!IMPORTANT]
> 포털을 사용하여 이미지의 VM을 다른 Azure 테넌트에 배포할 수 없습니다. 테넌트 간에 공유된 이미지에서 VM을 생성하려면 [Azure CLI](../linux/share-images-across-tenants.md) 또는 PowerShell을 사용해야 합니다.

## <a name="create-a-vm-using-powershell"></a>PowerShell을 사용하여 VM 만들기

애플리케이션 ID, 비밀 및 테넌트 ID를 사용하여 두 테넌트에 모두 로그인합니다. 

```azurepowershell-interactive
$applicationId = '<App ID>'
$secret = <Secret> | ConvertTo-SecureString -AsPlainText -Force
$tenant1 = "<Tenant 1 ID>"
$tenant2 = "<Tenant 2 ID>"
$cred = New-Object -TypeName PSCredential -ArgumentList $applicationId, $secret
Clear-AzContext
Connect-AzAccount -ServicePrincipal -Credential $cred  -Tenant "<Tenant 1 ID>"
Connect-AzAccount -ServicePrincipal -Credential $cred -Tenant "<Tenant 2 ID>"
```

애플리케이션 등록 권한이 있는 리소스 그룹에 VM을 생성합니다. 예시로 사용된 정보를 사용자의 정보로 바꿉니다.



```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Set a variable for the image version in Tenant 1 using the full image ID of the shared image version
$image = "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Networking pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using the $image variable to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $image | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="next-steps"></a>다음 단계

[Azure Portal](shared-images-portal.md)을 사용하여 Shared Image Gallery 리소스도 만들 수 있습니다.
