---
title: 고정 개인 IP 주소를 사용하여 VM 만들기 - Azure PowerShell
description: Azure PowerShell를 사용 하 여 고정 개인 IP 주소를 사용 하는 가상 머신을 만드는 방법에 대해 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: a98fab6f07e2616e8f3732f28446c24e9051bf09
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129372889"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 고정 개인 IP 주소를 사용 하는 가상 컴퓨터 만들기

VM (가상 컴퓨터)에는 사용자가 지정한 범위에서 개인 IP 주소가 자동으로 할당 됩니다. 이 범위는 VM이 배포 되는 서브넷을 기반으로 합니다. Vm은 VM이 삭제 될 때까지 주소를 유지 합니다. Azure는 VM을 만드는 서브넷에서 사용 가능한 다음 개인 IP 주소를 동적으로 할당합니다. 서브넷의 특정 IP 주소를 원하는 경우 VM에 고정 IP 주소를 할당 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 로컬로 설치된 Azure PowerShell 또는 Azure Cloud Shell

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

**eastus2** 위치에 **myResourceGroup** 이라는 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만듭니다.

```azurepowershell-interactive
$rg =@{
    Name = 'myResourceGroup'
    Location = 'eastus2'
}
New-AzResourceGroup @rg

```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

[New-azvm](/powershell/module/az.compute/new-azvm)를 사용 하 여 가상 머신을 만듭니다. 

다음 명령은 Windows Server 가상 컴퓨터를 만듭니다. 메시지가 표시 되 면 가상 컴퓨터에 대 한 자격 증명으로 사용할 사용자 이름 및 암호를 제공 합니다.

```azurepowershell-interactive
## Create virtual machine. ##
$vm = @{
    ResourceGroupName = 'myResourceGroup'
    Location = 'East US 2'
    Name = 'myVM'
    PublicIpAddressName = 'myPublicIP'
}
New-AzVM @vm
```

## <a name="change-private-ip-address-to-static"></a>개인 IP 주소를 고정으로 변경

이 섹션에서는 이전에 만든 가상 컴퓨터에 대 한 개인 IP 주소를 **동적** 에서 **정적** 으로 변경 합니다. 

[AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) 를 사용 하 여 가상 네트워크 구성을 변수에 넣습니다. [AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) 를 사용 하 여 서브넷 구성을 변수에 넣습니다. [AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) 를 사용 하 여 네트워크 인터페이스 구성을 가져와 변수에 넣습니다. [AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig) 를 사용 하 여 네트워크 인터페이스의 구성을 설정 합니다. 마지막으로 [AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface) 를 사용 하 여 가상 머신에 대 한 구성을 설정 합니다.

다음 명령은 가상 머신의 개인 IP 주소를 고정으로 변경 합니다.

```azurepowershell-interactive
## Place virtual network configuration into a variable. ##
$net = @{
    Name = 'myVM'
    ResourceGroupName = 'myResourceGroup'
}
$vnet = Get-AzVirtualNetwork @net

## Place subnet configuration into a variable. ##
$sub = @{
    Name = 'myVM'
    VirtualNetwork = $vnet
}
$subnet = Get-AzVirtualNetworkSubnetConfig @sub

## Get name of network interface and place into a variable ##
$int1 = @{
    Name = 'myVM'
    ResourceGroupName = 'myResourceGroup'
}
$vm = Get-AzVM @int1

## Place network interface configuration into a variable. ##
$nic = Get-AzNetworkInterface -ResourceId $vm.NetworkProfile.NetworkInterfaces.Id

## Set interface configuration. ##
$config =@{
    Name = 'myVM'
    PrivateIpAddress = '192.168.1.4'
    Subnet = $subnet
}
$nic | Set-AzNetworkInterfaceIpConfig @config -Primary

## Save interface configuration. ##
$nic | Set-AzNetworkInterface
```

> [!WARNING]
> 운영 체제에 개인 IP 주소 설정을 추가할 수 있지만 [운영 체제에 개인 IP 주소 추가](virtual-network-network-interface-addresses.md#private)를 읽기 전까지 추가하지 않는 것이 좋습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

- Azure에서 [공용 IP 주소](public-ip-addresses.md#public-ip-addresses)에 대해 자세히 알아봅니다.
- 모든 [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)에 대해 자세히 알아봅니다.
- [개인 IP 주소](private-ip-addresses.md), 및 Azure 가상 머신에 [고정 개인 IP 주소](virtual-network-network-interface-addresses.md#add-ip-addresses) 할당에 대해 알아봅니다.
- [Linux](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [Windows](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신 만들기에 대해 자세히 알아봅니다.