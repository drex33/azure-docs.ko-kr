---
title: 공용 IP 주소를 가상 머신에 연결
titlesuffix: Azure Virtual Network
description: Azure Portal이나 Azure CLI를 사용하여 공용 IP 주소를 VM(가상 머신)에 연결하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: dfaaaf4239e5479b53ebc696f1efe72472b302ef
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129372931"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>공용 IP 주소를 가상 머신에 연결

이 문서에서는 공용 IP 주소를 기존 VM(가상 머신)에 연결하는 방법을 알아봅니다. 인터넷에서 VM에 연결하려면 VM에 공용 IP 주소가 연결되어 있어야 합니다. 공용 IP 주소를 사용하여 새 VM을 만들려면 [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md), [Azure CLI(명령줄 인터페이스)](virtual-network-deploy-static-pip-arm-cli.md), [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)을 사용하여 만들 수 있습니다. 공용 IP 주소에는 명목 요금이 부과됩니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/ip-addresses/)을 참조하세요. 구독당 사용할 수 있는 공용 IP 주소의 수는 제한되어 있습니다. 자세한 내용은 [limits](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address)(제한)를 참조하세요.

[Azure Portal](#azure-portal), Azure [CLI(명령줄 인터페이스)](#azure-cli), [PowerShell](#powershell)을 사용하여 공용 IP 주소를 VM에 연결할 수 있습니다.

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 공용 IP 주소를 추가하려는 가상 머신을 찾아보거나 검색한 다음 선택합니다.
3. 아래 그림과 같이 **설정** 에서 **네트워킹** 을 선택하고 공용 IP 주소를 추가할 네트워크 인터페이스를 선택합니다.

   ![네트워크 인터페이스 선택](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > 공용 IP 주소는 VM에 연결된 네트워크 인터페이스에 연결됩니다. 위 그림의 VM은 네트워크 인터페이스가 하나뿐입니다. VM에 여러 네트워크 인터페이스가 있으면 모두 표시되고 공용 IP 주소를 연결할 네트워크 인터페이스를 선택할 수 있습니다.

4. **IP 구성** 을 선택한 다음, 다음 그림과 같이 IP 구성을 선택합니다.

   ![IP 구성 선택](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > 공용 IP 주소는 네트워크 인터페이스의 IP 구성에 연결됩니다. 위 그림에서는 네트워크 인터페이스에 IP 구성이 하나 있습니다. 네트워크 인터페이스에 여러 IP 구성이 있으면 모두 목록에 표시되고 공용 IP 주소를 연결할 IP 구성을 선택할 수 있습니다.

5. **사용** 을 선택한 다음 **IP 주소(‘필수 설정 구성’)** 를 선택합니다. 기존 공용 IP 주소를 선택합니다. 그러면 **공용 IP 주소 선택** 상자가 자동으로 닫힙니다. 사용 가능한 공용 IP 주소가 표시되지 않으면 새로 만들어야 합니다. 방법을 알아보려면 [Create a public IP address](virtual-network-public-ip-address.md#create-a-public-ip-address)(공용 IP 주소 만들기)를 참조하세요. 다음 그림과 같이 **저장** 을 선택하고 IP 구성 상자를 닫습니다.

   ![공용 IP 주소 사용](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > 표시되는 공용 IP 주소는 VM과 같은 지역에 있는 주소입니다. 지역에서 여러 공용 IP 주소를 만든 경우 여기에 모두 표시됩니다. 회색으로 표시된 항목이 있으면 주소가 이미 다른 리소스에 연결되어 있기 때문입니다.

6. 다음 그림과 같이 IP 구성에 할당된 공용 IP 주소를 확인합니다. IP 주소가 표시되는 데 몇 초 정도 걸릴 수 있습니다.

   ![할당된 공용 IP 주소 보기](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > 주소는 각 Azure 지역에서 사용되는 주소 풀에서 할당됩니다. 각 지역에서 사용되는 주소 풀 목록을 보려면 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 참조하세요. 할당된 주소는 지역에 사용되는 풀에 있는 임의의 주소일 수 있습니다. 해당 지역의 특정 풀에서 주소를 할당해야 하는 경우 [공용 IP 주소 접두사](public-ip-address-prefix.md)를 사용합니다.

7. 네트워크 보안 그룹의 보안 규칙을 사용하여 [VM에 대한 네트워크 트래픽을 허용](#allow-network-traffic-to-the-vm)합니다.

## <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)를 설치하거나 Azure Cloud Shell을 사용합니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 다음에 나오는 CLI 명령에서 **사용해 보세요.** 단추를 선택합니다. **사용해 보세요.** 를 선택하면 Azure 계정에 로그인할 수 있는 Cloud Shell이 호출됩니다.

1. Bash에서 로컬로 CLI를 사용하는 경우 `az login`을 사용하여 Azure에 로그인합니다.
2. 퍼블릭 IP 주소는 VM에 연결된 네트워크 인터페이스의 IP 구성에 연결됩니다. [az network nic-ip-config update](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update) 명령을 사용하여 공용 IP 주소를 IP 구성에 연결합니다. 다음 예제에서는 기존 공용 IP 주소 *myVMPublicIP* 를 *myResourceGroup* 리소스 그룹에 있는 기존 네트워크 인터페이스 *myVMVMNic* 의 IP 구성 *ipconfigmyVM* 에 연결합니다.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - 기존 공용 IP 주소가 없는 경우 [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 명령을 사용하여 만듭니다. 예를 들어 다음 명령은 *myResourceGroup* 리소스 그룹에 *myVMPublicIP* 라는 공용 IP 주소를 만듭니다.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > 앞의 명령은 사용자 지정할 수 있는 여러 설정에 기본값을 사용하여 공용 IP 주소를 만듭니다. 모든 공용 IP 주소 설정에 대해 자세히 알아보려면 [Create a public IP address](virtual-network-public-ip-address.md#create-a-public-ip-address)(공용 IP 주소 만들기)를 참조하세요. 주소는 각 Azure 지역에서 사용되는 공용 IP 주소 풀에서 할당됩니다. 각 지역에서 사용되는 주소 풀 목록을 보려면 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 참조하세요.

   - VM에 연결된 네트워크 인터페이스의 이름을 모르는 경우 [az vm nic list](/cli/azure/vm/nic#az_vm_nic_list) 명령을 사용하여 볼 수 있습니다. 예를 들어 다음 명령은 *myResourceGroup* 리소스 그룹에서 *myVM* 이라는 VM에 연결된 네트워크 인터페이스의 이름을 나열합니다.

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     출력에는 다음 예제와 비슷한 줄이 하나 이상 포함되어 있습니다.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     이전 예제에서 *myVMVMNic* 는 네트워크 인터페이스의 이름입니다.

   - 네트워크 인터페이스에 대한 IP 구성의 이름을 모르는 경우 [az network nic ip-config list](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_list) 명령을 사용하여 검색합니다. 예를 들어, 다음 명령은 *myResourceGroup* 리소스 그룹에 *myVMVMNic* 라는 네트워크 인터페이스에 대한 IP 구성의 이름을 나열합니다.

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. [az vm list-ip-addresses](/cli/azure/vm#az_vm_list_ip_addresses) 명령을 사용하여 IP 구성에 할당된 공용 IP 주소를 확인합니다. 다음 예제에서는 *myResourceGroup* 이라는 리소스 그룹에서 기존 VM *myVM* 에 할당된 IP 주소를 보여 줍니다.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > 주소는 각 Azure 지역에서 사용되는 주소 풀에서 할당됩니다. 각 지역에서 사용되는 주소 풀 목록을 보려면 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 참조하세요. 할당된 주소는 지역에 사용되는 풀에 있는 임의의 주소일 수 있습니다. 해당 지역의 특정 풀에서 주소를 할당해야 하는 경우 [공용 IP 주소 접두사](public-ip-address-prefix.md)를 사용합니다.

4. 네트워크 보안 그룹의 보안 규칙을 사용하여 [VM에 대한 네트워크 트래픽을 허용](#allow-network-traffic-to-the-vm)합니다.

## <a name="powershell"></a>PowerShell

[PowerShell](/powershell/azure/install-az-ps)을 설치하거나 Azure Cloud Shell을 사용합니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 셸입니다. PowerShell이 미리 설치되어 계정에서 사용하도록 구성되어 있습니다. 다음에 나오는 PowerShell 명령에서 **사용해 보세요.** 단추를 선택합니다. **사용해 보세요.** 를 선택하면 Azure 계정에 로그인할 수 있는 Cloud Shell이 호출됩니다.

1. PowerShell을 로컬로 사용하는 경우 `Connect-AzAccount`를 사용하여 Azure에 로그인합니다.
2. 퍼블릭 IP 주소는 VM에 연결된 네트워크 인터페이스의 IP 구성에 연결됩니다. [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) 및 [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) 명령을 사용하여 네트워크 인터페이스가 있는 가상 네트워크 및 서브넷을 가져옵니다. 그런 다음 [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 명령을 사용하여 네트워크 인터페이스를 가져오고 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 명령을 사용하여 기존 공용 IP 주소를 가져옵니다. 그런 다음 [Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) 명령을 사용하여 공용 IP 주소를 IP 구성에 연결하고 [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) 명령을 사용하여 네트워크 인터페이스에 새 IP 구성을 씁니다.

   다음 예제에서는 기존 공용 IP 주소 *myVMPublicIP* 를 가상 네트워크 *myVMVNet* 의 서브넷 *myVMSubnet* 에 있는 기존 네트워크 인터페이스 *myVMVMNic* 의 IP 구성 *ipconfigmyVM* 에 연결합니다. 모든 리소스는 *myResourceGroup* 리소스 그룹에 있습니다.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - 기존 공용 IP 주소가 없는 경우 [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) 명령을 사용하여 만듭니다. 예를 들어 다음 명령은 *eastus* 지역의 *myResourceGroup* 리소스 그룹에 *myVMPublicIP* 라는 ‘동적’ 공용 IP 주소를 만듭니다.
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > 앞의 명령은 사용자 지정할 수 있는 여러 설정에 기본값을 사용하여 공용 IP 주소를 만듭니다. 모든 공용 IP 주소 설정에 대해 자세히 알아보려면 [Create a public IP address](virtual-network-public-ip-address.md#create-a-public-ip-address)(공용 IP 주소 만들기)를 참조하세요. 주소는 각 Azure 지역에서 사용되는 공용 IP 주소 풀에서 할당됩니다. 각 지역에서 사용되는 주소 풀 목록을 보려면 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 참조하세요.

   - VM에 연결된 네트워크 인터페이스의 이름을 모르는 경우 [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) 명령을 사용하여 볼 수 있습니다. 예를 들어 다음 명령은 *myResourceGroup* 리소스 그룹에서 *myVM* 이라는 VM에 연결된 네트워크 인터페이스의 이름을 나열합니다.

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     출력에는 다음 예제와 비슷한 하나 이상의 줄이 포함됩니다. 예제 출력에서 *myVMVMNic* 는 네트워크 인터페이스의 이름입니다.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - 네트워크 인터페이스가 있는 가상 네트워크 또는 서브넷의 이름을 모르는 경우 `Get-AzNetworkInterface` 명령을 사용하여 정보를 확인합니다. 예를 들어 다음 명령은 *myResourceGroup* 리소스 그룹에서 네트워크 인터페이스 *myVMVMNic* 의 가상 네트워크 및 서브넷 정보를 가져옵니다.

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     출력에는 다음 예제와 비슷한 하나 이상의 줄이 포함됩니다. 예제 출력에서 *myVMVNET* 은 가상 네트워크 이름이고 *myVMSubnet* 은 서브넷 이름입니다.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - 네트워크 인터페이스에 대한 IP 구성의 이름을 모르는 경우 [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 명령을 사용하여 검색합니다. 예를 들어, 다음 명령은 *myResourceGroup* 리소스 그룹에 *myVMVMNic* 라는 네트워크 인터페이스에 대한 IP 구성의 이름을 나열합니다.

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     출력에는 다음 예제와 비슷한 하나 이상의 줄이 포함됩니다. 예제 출력에서 *ipconfigmyVM* 은 IP 구성의 이름입니다.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 명령을 사용하여 IP 구성에 할당된 공용 IP 주소를 확인합니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에서 *myVMPublicIP* 공용 IP 주소에 할당된 주소를 보여 줍니다.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   IP 구성에 할당된 공용 IP 주소의 이름을 모르는 경우 다음 명령을 실행하여 가져옵니다.

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   출력에는 다음 예제와 비슷한 하나 이상의 줄이 포함됩니다. 예제 출력에서 *myVMPublicIP* 는 IP 구성에 할당된 공용 IP 주소의 이름입니다.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > 주소는 각 Azure 지역에서 사용되는 주소 풀에서 할당됩니다. 각 지역에서 사용되는 주소 풀 목록을 보려면 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 참조하세요. 할당된 주소는 지역에 사용되는 풀에 있는 임의의 주소일 수 있습니다. 해당 지역의 특정 풀에서 주소를 할당해야 하는 경우 [공용 IP 주소 접두사](public-ip-address-prefix.md)를 사용합니다.

4. 네트워크 보안 그룹의 보안 규칙을 사용하여 [VM에 대한 네트워크 트래픽을 허용](#allow-network-traffic-to-the-vm)합니다.

## <a name="allow-network-traffic-to-the-vm"></a>VM에 대한 네트워크 트래픽 허용

인터넷에서 공용 IP 주소에 연결하려면 먼저 네트워크 인터페이스, 네트워크 인터페이스가 있는 서브넷 또는 둘 다에 연결된 네트워크 보안 그룹에서 필요한 포트가 열려 있는지 확인합니다. 보안 그룹은 트래픽을 네트워크 인터페이스의 개인 IP 주소로 필터링하지만 인바운드 인터넷 트래픽이 공용 IP 주소에 도착하면 Azure에서 공용 주소를 개인 IP 주소로 변환하므로 네트워크 보안 그룹에서 트래픽 흐름을 차단하는 경우 공용 IP 주소와의 통신이 실패합니다. [포털](../../virtual-network/diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [CLI](../../virtual-network/diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli) 또는 [PowerShell](../../virtual-network/diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)을 사용하여 네트워크 인터페이스 및 서브넷에 대한 유효한 보안 규칙을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

네트워크 보안 그룹을 사용하여 VM에 대한 인바운드 인터넷 트래픽을 허용합니다. 네트워크 보안 그룹을 만드는 방법에 대한 자세한 내용은 [Work with network security groups](../../virtual-network/manage-network-security-group.md#work-with-network-security-groups)(네트워크 보안 그룹 사용)를 참조하세요. 네트워크 보안 그룹에 대한 자세한 내용은 [Security groups](../../virtual-network/network-security-groups-overview.md)(보안 그룹)를 참조하세요.
