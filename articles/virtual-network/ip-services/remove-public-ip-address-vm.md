---
title: Azure VM에서 퍼블릭 IP 주소 분리
titlesuffix: Azure Virtual Network
description: VM에서 퍼블릭 IP 주소를 분리하는 방법 알아보기
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: allensu
ms.openlocfilehash: e9bfadd3e2453f0241dc2f7b8bfa5c964333bcf5
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129372928"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Azure VM에서 퍼블릭 IP 주소 분리 

이 문서에서는 Azure VM(가상 머신)에서 퍼블릭 IP 주소를 분리하는 방법을 알아봅니다.

[Azure Portal](#azure-portal), Azure [CLI(명령줄 인터페이스)](#azure-cli) 또는 [PowerShell](#powershell)을 사용하여 VM에서 퍼블릭 IP 주소를 분리할 수 있습니다.

## <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 퍼블릭 IP 주소를 분리하려는 가상 머신을 찾아보거나 검색한 다음, 선택합니다.
3. VM 페이지에서 **개요** 를 선택하고 다음 그림과 같이 퍼블릭 IP 주소를 선택합니다.

   ![퍼블릭 IP 선택](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. 다음 그림에 표시된 것처럼 퍼블릭 IP 주소 페이지에서 **개요** 를 선택하고 **분리** 를 선택합니다.

    ![퍼블릭 IP 분리](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. **퍼블릭 IP 주소 분리** 에서 **예** 를 선택합니다.

## <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)를 설치하거나 Azure Cloud Shell을 사용합니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 다음에 나오는 CLI 명령에서 **사용해 보세요.** 단추를 선택합니다. **사용해 보세요.** 를 선택하면 Azure 계정에 로그인할 수 있는 Cloud Shell이 호출됩니다.

1. Bash에서 로컬로 CLI를 사용하는 경우 `az login`을 사용하여 Azure에 로그인합니다.
2. 퍼블릭 IP 주소는 VM에 연결된 네트워크 인터페이스의 IP 구성에 연결됩니다. [az network nic-ip-config update](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update) 명령을 사용하여 IP 구성에서 퍼블릭 IP 주소를 분리합니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에서 *myVM* 이라는 VM에 연결된 *myVMVMNic* 라는 기존 네트워크 인터페이스의 IP 구성 *ipconfigmyVM* 에서 퍼블릭 IP 주소 *myVMPublicIP* 를 분리합니다.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   VM에 연결된 네트워크 인터페이스의 이름을 모르는 경우 [az vm nic list](/cli/azure/vm/nic#az_vm_nic_list) 명령을 사용하여 볼 수 있습니다. 예를 들어 다음 명령은 *myResourceGroup* 리소스 그룹에서 *myVM* 이라는 VM에 연결된 네트워크 인터페이스의 이름을 나열합니다.

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     출력에는 다음 예제와 비슷한 줄이 하나 이상 포함되어 있습니다.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     이전 예제에서 *myVMVMNic* 는 네트워크 인터페이스의 이름입니다.

   - 네트워크 인터페이스에 대한 IP 구성의 이름을 모르는 경우 [az network nic ip-config list](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_list) 명령을 사용하여 검색합니다. 예를 들어, 다음 명령은 *myResourceGroup* 리소스 그룹에 *myVMVMNic* 라는 네트워크 인터페이스에 대한 퍼블릭 IP 구성의 이름을 나열합니다.

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - 네트워크 인터페이스에 대한 퍼블릭 IP 구성의 이름을 모르는 경우 [az network nic ip-config show](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_show) 명령을 사용하여 검색합니다. 예를 들어, 다음 명령은 *myResourceGroup* 리소스 그룹에 *myVMVMNic* 라는 네트워크 인터페이스에 대한 퍼블릭 IP 구성의 이름을 나열합니다.

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

[PowerShell](/powershell/azure/install-az-ps)을 설치하거나 Azure Cloud Shell을 사용합니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 셸입니다. PowerShell이 미리 설치되어 계정에서 사용하도록 구성되어 있습니다. 다음에 나오는 PowerShell 명령에서 **사용해 보세요.** 단추를 선택합니다. **사용해 보세요.** 를 선택하면 Azure 계정에 로그인할 수 있는 Cloud Shell이 호출됩니다.

1. PowerShell을 로컬로 사용하는 경우 `Connect-AzAccount`를 사용하여 Azure에 로그인합니다.
2. 퍼블릭 IP 주소는 VM에 연결된 네트워크 인터페이스의 IP 구성에 연결됩니다. [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 명령을 사용하여 네트워크 인터페이스를 가져옵니다. 퍼블릭 IP 주소 값을 null로 설정하고 [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) 명령을 사용하여 네트워크 인터페이스에 새 IP 구성을 씁니다.

   다음 예제에서는 *myVM* 이라는 VM에 연결된 *myVMVMNic* 라는 네트워크 인터페이스에서 *myVMPublicIP* 라는 퍼블릭 IP 주소를 분리합니다. 모든 리소스는 *myResourceGroup* 리소스 그룹에 있습니다.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - VM에 연결된 네트워크 인터페이스의 이름을 모르는 경우 [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) 명령을 사용하여 볼 수 있습니다. 예를 들어 다음 명령은 *myResourceGroup* 리소스 그룹에서 *myVM* 이라는 VM에 연결된 네트워크 인터페이스의 이름을 나열합니다.

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     출력에는 다음 예제와 비슷한 하나 이상의 줄이 포함됩니다. 예제 출력에서 *myVMVMNic* 는 네트워크 인터페이스의 이름입니다.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - 네트워크 인터페이스에 대한 IP 구성의 이름을 모르는 경우 [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) 명령을 사용하여 검색합니다. 예를 들어, 다음 명령은 *myResourceGroup* 리소스 그룹에 *myVMVMNic* 라는 네트워크 인터페이스에 대한 IP 구성의 이름을 나열합니다.

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     출력에는 다음 예제와 비슷한 하나 이상의 줄이 포함됩니다. 예제 출력에서 *ipconfigmyVM* 은 IP 구성의 이름입니다.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>다음 단계

- [퍼블릭 IP 주소를 VM에 연결](associate-public-ip-address-vm.md)하는 방법을 알아봅니다.
