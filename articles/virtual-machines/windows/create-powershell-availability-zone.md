---
title: Azure PowerShell을 사용하여 영역 설정된 VM 만들기
description: Azure PowerShell을 사용하여 가용성 영역에서 가상 머신 만들기
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5e059cd4112b39565ba07b39d80d2db4a53c9d46
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693475"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-azure-powershell"></a>Azure PowerShell을 사용하여 가용성 영역에서 가상 머신 만들기

**적용 대상:** :heavy_check_mark: Windows VM 

이 문서는 Azure PowerShell을 사용하여 Azure 가용성 영역에서 Windows Server 2016을 실행 중인 Azure 가상 머신 만들기를 자세히 설명합니다. [가용성 영역](../../availability-zones/az-overview.md)은 Azure 지역에서 물리적으로 별도 영역입니다. 가용성 영역을 사용하여 가능성이 적은 실패 또는 전체 데이터 센터의 손실로부터 앱 및 데이터를 보호합니다.

가용성 영역을 사용하려면 [지원되는 Azure 지역](../../availability-zones/az-region.md)에 가상 머신을 만들어야 합니다.

 

## <a name="sign-in-to-azure"></a>Azure에 로그인

`Connect-AzAccount` 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Connect-AzAccount
```

## <a name="check-vm-sku-availability"></a>VM SKU 가용성 확인
VM 크기 또는 SKU의 가용성은 지역 및 영역에 따라 다를 수 있습니다. 가용성 영역 사용을 계획하는 데 도움이 되도록 Azure 지역 및 영역별로 사용 가능한 VM SKU를 나열할 수 있습니다. 이 기능을 사용하면 적절한 VM 크기를 선택하고 영역 간에 원하는 복원력을 얻을 수 있습니다. 다른 VM 유형 및 크기에 대한 자세한 내용은 [VM 크기 개요](../sizes.md)를 참조하세요.

[Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) 명령을 사용하여 사용 가능한 VM SKU를 볼 수 있습니다. 다음 예제에서는 *eastus2* 지역에서 사용 가능한 VM SKU를 나열합니다.

```powershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("eastus2")};
```

출력은 다음과 같이 압축된 예제와 비슷하며, 각 VM 크기를 사용할 수 있는 가용성 영역을 보여 줍니다.

```powershell
ResourceType                Name  Location      Zones   [...]
------------                ----  --------      -----
virtualMachines  Standard_DS1_v2   eastus2  {1, 2, 3}
virtualMachines  Standard_DS2_v2   eastus2  {1, 2, 3}
[...]
virtualMachines     Standard_F1s   eastus2  {1, 2, 3}
virtualMachines     Standard_F2s   eastus2  {1, 2, 3}
[...]
virtualMachines  Standard_D2s_v3   eastus2  {1, 2, 3}
virtualMachines  Standard_D4s_v3   eastus2  {1, 2, 3}
[...]
virtualMachines   Standard_E2_v3   eastus2  {1, 2, 3}
virtualMachines   Standard_E4_v3   eastus2  {1, 2, 3}
```


## <a name="create-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 이 예제에서는 *eastus2* 지역에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다. 

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS2
```

## <a name="create-networking-resources"></a>네트워킹 리소스 만들기

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>가상 네트워크, 서브넷 및 공용 IP 주소 만들기 
이러한 리소스는 가상 머신에 네트워크 연결을 제공하고 인터넷에 연결하는 데 사용됩니다. 가용성 영역에서 IP 주소를 만듭니다. 이 예에서는 *2* 입니다. 뒷부분에 나오는 단계에서는 IP 주소를 만드는 데 사용된 영역과 동일한 영역에 VM을 만듭니다.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myVNet -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address in an availability zone and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName myResourceGroup -Location eastus2 -Zone 2 `
    -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)" -Sku Standard
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>네트워크 보안 그룹 및 네트워크 보안 그룹 규칙 만들기 
네트워크 보안 그룹은 인바운드 및 아웃바운드 규칙을 사용하여 가상 머신을 보호합니다. 이 경우 포트 3389에 대해 들어오는 원격 데스크톱 연결을 허용하는 인바운드 규칙이 만들어집니다. 포트 80에 대한 인바운드 규칙을 만들어서 들어오는 웹 트래픽을 허용하려고 합니다.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
    -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>가상 머신에 대한 네트워크 카드 만들기 
[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)를 사용하여 가상 머신에 네트워크 카드를 만듭니다. 네트워크 카드는 서브넷, 네트워크 보안 그룹 및 공용 IP 주소에 가상 머신을 연결합니다.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location eastus2 `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>가상 머신 만들기

가상 머신 구성을 만듭니다. 이 구성은 가상 머신 이미지, 크기 및 인증 구성 등 가상 머신을 배포할 때 사용되는 설정을 포함합니다. 이 예제의 *Standard_DS1_v2* 크기는 가용성 영역에서 지원됩니다. 이 구성은 IP 주소를 만들 때 설정한 가용성 영역도 지정합니다. 이 단계를 실행할 때 자격 증명을 묻는 메시지가 나타납니다. 입력하는 값은 가상 머신에 대한 사용자 이름 및 암호로 구성됩니다.

```powershell
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS1_v2 -Zone 2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
```

[New-AzVM](/powershell/module/az.compute/new-azvm)을 사용하여 가상 머신을 만듭니다.

```powershell
New-AzVM -ResourceGroupName myResourceGroup -Location eastus2 -VM $vmConfig
```

## <a name="confirm-zone-for-managed-disk"></a>관리 디스크에 대한 영역 확인

VM과 동일한 가용성 영역에 VM의 IP 주소 리소스를 만들었습니다. VM에 대한 관리 디스크 리소스는 동일한 가용성 영역에 만들어집니다. [Get-AzDisk](/powershell/module/az.compute/get-azdisk)를 사용하여 이를 확인할 수 있습니다.

```powershell
Get-AzDisk -ResourceGroupName myResourceGroup
```

관리 디스크를 보여 주는 출력은 VM과 동일한 가용성 영역에 있습니다.

```powershell
ResourceGroupName  : myResourceGroup
AccountType        : PremiumLRS
OwnerId            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
ManagedBy          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx//resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              : {2}
TimeCreated        : 9/7/2017 6:57:26 PM
OsType             : Windows
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 127
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/disks/myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Name               : myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Type               : Microsoft.Compute/disks
Location           : eastus2
Tags               : {}
```


## <a name="next-steps"></a>다음 단계

이 문서에서는 가용성 영역에서 VM을 만드는 방법을 배웠습니다. Azure VM의 [가용성](../availability.md)에 대해 자세히 알아보세요.
