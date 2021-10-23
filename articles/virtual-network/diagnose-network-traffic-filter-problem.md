---
title: 가상 머신 네트워크 트래픽 필터 문제 진단 | Microsoft Docs
description: 가상 머신에 대한 효과적인 보안 규칙을 확인하여 가상 머신 네트워크 트래픽 필터 문제를 진단하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ca7503127342a5eceac43a8f5a2bfce578eb21e6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257806"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>가상 머신 네트워크 트래픽 필터 문제 진단

이 문서에서는 VM(가상 머신)에 적용되는 NSG(네트워크 보안 그룹) 보안 규칙을 확인하여 네트워크 트래픽 필터 문제를 진단하는 방법을 알아봅니다.

NSG에서는 VM에서 들어오고 나가는 트래픽 유형을 제어할 수 있습니다. Azure 가상 네트워크의 서브넷, VM에 연결된 네트워크 인터페이스 또는 둘 다에 NSG를 연결할 수 있습니다. 네트워크 인터페이스에 적용되는 효과적인 보안 규칙은 네트워크 인터페이스 및 네트워크 인터페이스가 있는 서브넷에 연결된 NSG에 존재하는 규칙의 집계입니다. 때로는 서로 다른 NSG의 규칙이 서로 충돌하고 VM의 네트워크 연결에 영향을 줄 수 있습니다. VM의 네트워크 인터페이스에 적용된 NSG의 모든 효과적인 보안 규칙을 볼 수 있습니다. 가상 네트워크, 네트워크 인터페이스 또는 NSG 개념을 잘 모르는 경우 [가상 네트워크 개요](virtual-networks-overview.md), [네트워크 인터페이스](virtual-network-network-interface.md) 및 [네트워크 보안 그룹 개요](./network-security-groups-overview.md)를 참조하세요.

## <a name="scenario"></a>시나리오

인터넷에서 포트 80을 통해 VM에 연결하려고 하지만 연결에 실패합니다. 인터넷에서 포트 80에 액세스할 수 없는 이유를 확인하기 위해 Azure [Portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell) 또는 [Azure CLI](#diagnose-using-azure-cli)를 사용하여 네트워크 인터페이스에 대한 효과적인 보안 규칙을 볼 수 있습니다.

다음 단계는 효과적인 보안 규칙을 볼 수 있는 기존 VM이 있다고 가정합니다. 기존 VM이 없는 경우 먼저 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM을 배포하여 이 아티클의 작업을 완료합니다. 이 아티클에 있는 예제는 *myVMVMNic* 라는 네트워크 인터페이스가 있는 *myVM* 이라는 VM에 대한 것입니다. VM 및 네트워크 인터페이스는 *myResourceGroup* 이라는 리소스 그룹에 있고, *미국 동부* 영역에 있습니다. 해당 단계에서 문제를 진단하는 VM에 대해 적절히 값을 변경합니다.

## <a name="diagnose-using-azure-portal"></a>Azure Portal을 사용하여 진단

1. [필요한 권한](virtual-network-network-interface.md#permissions)이 있는 Azure 계정으로 Azure [Portal](https://portal.azure.com)에 로그인합니다.
2. Azure Portal 맨 위에 있는 검색 상자에 VM의 이름을 입력합니다. 검색 결과에 VM의 이름이 나타나면 선택합니다.
3. **설정** 에서 다음 그림에 표시된 것처럼 **네트워킹** 을 선택합니다.

   ![사용자의 V M V M Nic에 대한 네트워킹 설정 Azure Portal을 보여 주는 스크린샷.](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   이전 그림에 나열된 규칙은 **myVMVMNic** 라는 네트워크 인터페이스에 대한 것입니다. 두 개의 서로 다른 네트워크 보안 그룹에서 네트워크 인터페이스에 대한 **인바운드 포트 규칙** 이 있습니다.
   
   - **mySubnetNSG**: 네트워크 인터페이스가 있는 서브넷에 연결됩니다.
   - **myVMNSG**: **myVMVMNic** 라는 VM에서 네트워크 인터페이스에 연결됩니다.

   **DenyAllInBound** 라는 규칙은 [시나리오](#scenario)에 설명된 대로 인터넷에서 포트 80을 통해 VM에 대한 인바운드 통신을 방지하는 규칙입니다. 규칙은 인터넷을 포함하는 **원본** 에 대한 *0.0.0.0/0* 을 나열합니다. 더 높은 우선 순위(낮은 숫자)가 있는 다른 규칙은 포트 80 인바운드를 허용하지 않습니다. 인터넷에서 VM에 대한 포트 80 인바운드를 허용하려면 [문제 해결](#resolve-a-problem)을 참조하세요. 보안 규칙 및 Azure에서 적용하는 방법에 대해 자세히 알아보려면 [네트워크 보안 그룹](./network-security-groups-overview.md)을 참조하세요.

   그림의 맨 아래에 **아웃바운드 포트 규칙** 이 표시됩니다. 아래는 네트워크 인터페이스에 대한 아웃바운드 포트 규칙입니다. 그림은 각 NSG에 대한 4개의 인바운드 규칙만을 표시하지만 NSG에는 4개 이상의 규칙이 있을 수 있습니다. 그림에서 **원본** 및 **대상** 아래에 **VirtualNetwork** 가 표시되고 **원본** 아래에 **AzureLoadBalancer** 가 표시됩니다. **VirtualNetwork** 및 **AzureLoadBalancer** 는 [서비스 태그](./network-security-groups-overview.md#service-tags)입니다. 서비스 태그는 보안 규칙 생성에 대한 복잡성을 최소화할 수 있는 IP 주소 접두사의 그룹을 나타냅니다.

4. VM이 실행 상태에 있는지 확인한 다음, 이전 그림에 표시된 것처럼 **효과적인 보안 규칙** 을 선택하여 다음 그림에 표시된 효과적인 보안 규칙을 봅니다.

   ![다운로드 및 AllowAzureLoadBalancerInbound 인바운드 규칙이 선택된 효과적인 보안 규칙 창을 보여 주는 스크린샷.](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   나열된 규칙은 3단계에서 표시된 것과 동일하지만 네트워크 인터페이스와 서브넷에 연결된 NSG에 대해 서로 다른 탭이 있습니다. 그림에서 볼 수 있듯이 처음 50개의 규칙만 표시됩니다. 모든 규칙을 포함하는 .csv 파일을 다운로드하려면 **다운로드** 를 선택합니다.

   각 서비스 태그가 나타내는 접두사를 확인하려면 규칙을 선택합니다(예: **AllowAzureLoadBalancerInbound** 라는 규칙). 다음 그림은 **AzureLoadBalancer** 서비스 태그에 대한 접두사를 보여줍니다.

   ![입력한 AllowAzureLoadBalancerInbound에 대한 주소 접두사를 보여 주는 스크린샷.](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   **AzureLoadBalancer** 서비스 태그는 하나의 접두사만을 나타내지만 다른 서비스 태그는 여러 개의 접두사를 나타냅니다.

5. 이전 단계는 **myVMVMNic** 라는 네트워크 인터페이스에 대한 보안 규칙을 보여줬지만 일부 이전 그림에서 **myVMVMNic2** 라는 네트워크 인터페이스도 확인했습니다. 이 예제의 VM에는 두 개의 연결된 네트워크 인터페이스가 있습니다. 효과적인 보안 규칙은 각 네트워크 인터페이스에 대해 다를 수 있습니다.

   **myVMVMNic2** 네트워크 인터페이스에 대한 규칙을 보려면 선택합니다. 다음 그림에 표시된 것처럼 네트워크 인터페이스에는 **myVMVMNic** 네트워크 인터페이스와 해당 서브넷에 연결된 동일한 규칙이 있습니다. 두 네트워크 인터페이스가 동일한 서브넷에 있기 때문입니다. 서브넷에 NSG를 연결하면 해당 규칙이 서브넷의 모든 네트워크 인터페이스에 적용됩니다.

   ![사용자의 V M V M Nic 2에 대한 네트워킹 설정 Azure Portal을 보여 주는 스크린샷.](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   **myVMVMNic** 네트워크 인터페이스와 달리 **myVMVMNic2** 네트워크 인터페이스에는 연결된 네트워크 보안 그룹이 없습니다. 각 네트워크 인터페이스와 서브넷은 0개 또는 하나의 연결된 NSG를 가질 수 있습니다. 각 네트워크 인터페이스 또는 서브넷에 연결된 NSG는 동일하거나 다를 수 있습니다. 동일한 네트워크 보안 그룹을 선택한 만큼의 네트워크 인터페이스 및 서브넷에 연결할 수 있습니다.

효과적인 보안 규칙은 VM을 통해 표시되지만 다음과 같은 개별 항목을 통해서도 효과적인 보안 규칙을 볼 수 있습니다.
- **네트워크 인터페이스**: [네트워크 인터페이스를 보는](virtual-network-network-interface.md#view-network-interface-settings) 방법을 알아봅니다.
- **NSG**: [NSG를 보는](manage-network-security-group.md#view-details-of-a-network-security-group) 방법을 알아봅니다.

## <a name="diagnose-using-powershell"></a>PowerShell을 사용하여 진단

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure Cloud Shell](https://shell.azure.com/powershell) 뒤에 오는 명령을 실행하거나 또는 컴퓨터에서 PowerShell을 실행합니다. Azure Cloud Shell은 무료 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 컴퓨터에서 PowerShell을 실행하는 경우 Azure PowerShell 모듈 버전 1.0.0이상이 필요합니다. 컴퓨터에서 `Get-Module -ListAvailable Az`을 실행하여 설치된 버전을 확인합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. PowerShell을 로컬로 실행 중인 경우 `Connect-AzAccount`를 실행하여 [필요한 권한](virtual-network-network-interface.md#permissions)을 가진 계정으로 Azure에 로그인해야 합니다.

[Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)을 사용하여 네트워크 인터페이스에 대한 효과적인 보안 규칙을 가져옵니다. 다음 예제에서는 *myResourceGroup* 이라는 리소스 그룹에 있는 *myVMVMNic* 라는 네트워크 인터페이스에 대한 효과적인 보안 규칙을 가져옵니다.

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

출력은 json 형식으로 반환됩니다. 출력을 이해하려면 [명령 출력 해석](#interpret-command-output)을 참조하세요.
출력은 NSG가 네트워크 인터페이스, 네트워크 인터페이스가 있는 서브넷 또는 둘 다와 연결된 경우에만 반환됩니다. VM은 실행 상태에 있어야 합니다. 하나의 VM에 다른 네트워크 인터페이스가 적용되는 여러 NIC가 있을 수 있습니다. 문제를 해결할 때 각 네트워크 인터페이스에 대해 해당 명령을 실행합니다.

여전히 연결 문제가 있는 경우 [추가 진단](#additional-diagnosis) 및 [고려 사항](#considerations)을 참조하세요.

네트워크 인터페이스의 이름을 알지 못하지만 네트워크 인터페이스가 연결된 VM의 이름을 아는 경우 다음 명령은 VM에 연결된 모든 네트워크 인터페이스의 ID를 반환합니다.

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

다음 예제와 유사한 출력이 표시됩니다.

```output
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

이전 출력에서 네트워크 인터페이스 이름은 *myVMVMNic* 입니다.

## <a name="diagnose-using-azure-cli"></a>Azure CLI를 사용하여 진단

이 문서의 작업을 완료하기 위해 Azure CLI(명령줄 인터페이스)를 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/bash)에서 명령을 실행하거나 컴퓨터에서 CLI를 실행합니다. 이 아티클에서는 Azure CLI 버전 2.0.32 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. Azure CLI를 로컬로 실행 중인 경우 `az login`를 실행하고 [필요한 권한](virtual-network-network-interface.md#permissions)을 가진 계정으로 Azure에 로그인해야 합니다.

[az network nic list-effective-nsg](/cli/azure/network/nic#az_network_nic_list_effective_nsg)를 사용하여 네트워크 인터페이스에 대한 효과적인 보안 규칙을 가져옵니다. 다음 예제에서는 *myResourceGroup* 이라는 리소스 그룹에 있는 *myVMVMNic* 라는 네트워크 인터페이스에 대한 효과적인 보안 규칙을 가져옵니다.

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

출력은 json 형식으로 반환됩니다. 출력을 이해하려면 [명령 출력 해석](#interpret-command-output)을 참조하세요.
출력은 NSG가 네트워크 인터페이스, 네트워크 인터페이스가 있는 서브넷 또는 둘 다와 연결된 경우에만 반환됩니다. VM은 실행 상태에 있어야 합니다. 하나의 VM에 다른 네트워크 인터페이스가 적용되는 여러 NIC가 있을 수 있습니다. 문제를 해결할 때 각 네트워크 인터페이스에 대해 해당 명령을 실행합니다.

여전히 연결 문제가 있는 경우 [추가 진단](#additional-diagnosis) 및 [고려 사항](#considerations)을 참조하세요.

네트워크 인터페이스의 이름을 알지 못하지만 네트워크 인터페이스가 연결된 VM의 이름을 아는 경우 다음 명령은 VM에 연결된 모든 네트워크 인터페이스의 ID를 반환합니다.

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

반환된 출력 내에 다음 예제와 비슷한 정보가 나타납니다.

```output
"networkProfile": {
    "additionalProperties": {},
    "networkInterfaces": [
      {
        "additionalProperties": {},
        "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
        "primary": true,
        "resourceGroup": "myResourceGroup"
      },
```

이전 출력에서 네트워크 인터페이스 이름은 *myVMVMNic 인터페이스* 입니다.

## <a name="interpret-command-output"></a>명령 출력 해석

문제를 진단하는 데 [PowerShell](#diagnose-using-powershell) 또는 [Azure CLI](#diagnose-using-azure-cli)를 사용했는지 여부에 관계 없이 다음 정보를 포함하는 출력을 받습니다.

- **NetworkSecurityGroup**: 네트워크 보안 그룹의 ID입니다.
- **연결**: 네트워크 보안 그룹이 *NetworkInterface* 또는 *서브넷* 에 연결되었는지 여부입니다. NSG가 둘 다에 연결된 경우 출력은 각 NSG에 대해 **NetworkSecurityGroup**, **Association** 및 **EffectiveSecurityRules** 로 반환됩니다. 효과적인 보안 규칙을 보기 위해 명령을 실행하기 직전에 NSG가 연결되거나 연결 해제되면 명령 출력에 변경 내용이 반영될 때까지 몇 초 정도 기다려야 할 수 있습니다.
- **EffectiveSecurityRules**: 각 속성의 설명은 [보안 규칙 만들기](manage-network-security-group.md#create-a-security-rule)에 자세히 설명되어 있습니다. *defaultSecurityRules/* 가 앞에 추가된 규칙 이름은 모든 NSG에 존재하는 기본 보안 규칙입니다. *securityRules/* 가 앞에 추가된 규칙 이름은 사용자가 만든 규칙입니다. **destinationAddressPrefix** 또는 **sourceAddressPrefix** 속성에 대해 **Internet**, **VirtualNetwork** 및 **AzureLoadBalancer** 와 같은 [서비스 태그](./network-security-groups-overview.md#service-tags)를 지정하는 규칙은 **expandedDestinationAddressPrefix** 속성에 대한 값도 갖습니다. **expandedDestinationAddressPrefix** 속성은 서비스 태그로 표시되는 모든 주소 접두사를 나열합니다.

출력에 중복 규칙이 나열되는 경우 NSG가 네트워크 인터페이스와 서브넷 모두에 연결되었기 때문입니다. 두 NSG는 동일한 기본 규칙을 가지며 두 NSG에서 동일한 사용자 고유의 규칙을 만든 경우 추가 중복 규칙을 가질 수 있습니다.

**defaultSecurityRules/DenyAllInBound** 라는 규칙은 [시나리오](#scenario)에 설명된 대로 인터넷에서 포트 80을 통해 VM에 대한 인바운드 통신을 방지하는 규칙입니다. 더 높은 우선 순위(낮은 숫자)가 있는 다른 규칙은 인터넷에서 포트 80 인바운드를 허용하지 않습니다.

## <a name="resolve-a-problem"></a>문제 해결

이 문서의 [시나리오](#scenario)에 제공된 문제를 진단하기 위해 Azure [Portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell) 또는 [Azure CLI](#diagnose-using-azure-cli)를 사용하는지 여부에 따라 솔루션은 다음 속성을 사용하여 네트워크 보안 규칙을 만드는 것입니다.

| 속성                | 값                                                                              |
|---------                |---------                                                                           |
| 원본                  | 모두                                                                                |
| 원본 포트 범위      | 모두                                                                                |
| 대상             | VM의 IP 주소, IP 주소의 범위 또는 서브넷에 있는 모든 주소입니다. |
| 대상 포트 범위 | 80                                                                                 |
| 프로토콜                | TCP                                                                                |
| 작업                  | 허용                                                                              |
| 우선 순위                | 100                                                                                |
| 속성                    | Allow-HTTP-All                                                                     |

규칙을 만든 후 규칙의 우선 순위는 트래픽을 거부하는 *DenyAllInBound* 라는 기본 보안 규칙보다 높기 때문에 포트 80은 인터넷에서 허용된 인바운드입니다. [보안 규칙을 만드는](manage-network-security-group.md#create-a-security-rule) 방법을 알아봅니다. 다른 NSG가 네트워크 인터페이스와 서브넷 모두에 연결되어 있는 경우 두 NSG에 동일한 규칙을 만들어야 합니다.

Azure에서 인바운드 트래픽을 처리할 때 서브넷에 연결된 NSG의 규칙을 처리한 다음(연결된 NSG가 있는 경우), 네트워크 인터페이스에 연결된 NSG의 규칙을 처리합니다. 네트워크 인터페이스와 서브넷에 연결된 NSG가 있는 경우 포트는 VM에 도달하는 트래픽에 대해 두 NSG에서 열려야 합니다. 관리 및 통신 문제를 완화하려면 개별 네트워크 인터페이스가 아닌 서브넷에 NSG를 연결하는 것이 좋습니다. 서브넷 내의 VM에 다른 보안 규칙이 필요한 경우 ASG(애플리케이션 보안 그룹)의 네트워크 인터페이스 멤버를 만들고, ASG를 보안 규칙의 원본 및 대상으로 지정할 수 있습니다. [애플리케이션 보안 그룹](./network-security-groups-overview.md#application-security-groups)에 대해 자세히 알아봅니다.

여전히 통신 문제가 있는 경우 [고려할 사항](#considerations) 및 추가 진단을 참조하세요.

## <a name="considerations"></a>고려 사항

연결 문제를 해결하는 경우 다음 사항을 고려합니다.

* 기본 보안 규칙은 인터넷의 인바운드 액세스를 차단하고, 가상 네트워크의 인바운드 트래픽만 허용합니다. 인터넷의 인바운드 트래픽을 허용하려면 기본 규칙보다 더 높은 우선 순위의 보안 규칙을 추가합니다. [기본 보안 규칙](./network-security-groups-overview.md#default-security-rules) 또는 [보안 규칙을 추가하는](manage-network-security-group.md#create-a-security-rule) 방법에 대해 자세히 알아봅니다.
* 가상 네트워크를 피어링한 경우 기본적으로, **VIRTUAL_NETWORK** 서비스 태그는 피러링된 가상 네트워크에 대한 접두사를 포함하도록 자동으로 확장됩니다. 가상 네트워크 피어링과 관련된 문제를 해결하기 위해 **ExpandedAddressPrefix** 목록에서 접두사를 볼 수 있습니다. [가상 네트워크 피어링](virtual-network-peering-overview.md) 및 [서비스 태그](./network-security-groups-overview.md#service-tags)에 대해 자세히 알아봅니다.
* VM의 네트워크 인터페이스 및 또는 서브넷과 연결된 NSG가 있고 VM이 실행 상태에 있는 경우 네트워크 인터페이스에 대한 효과적인 보안 규칙만 표시됩니다.
* 네트워크 인터페이스 또는 서브넷과 연결된 NSG가 없고 VM에 할당된 [공용 IP 주소](./ip-services/virtual-network-public-ip-address.md)가 있는 경우 모든 포트가 인바운드 및 아웃바운드 액세스를 위해 열립니다. VM에 공용 IP 주소가 있는 경우 서브넷 및 네트워크 인터페이스에 NSG를 적용하는 것이 좋습니다.

## <a name="additional-diagnosis"></a>추가 진단

* 트래픽이 VM 간에서 허용되는지를 확인하기 위해 빠른 테스트를 실행하려면 Azure Network Watcher의 [IP 흐름 확인](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) 기능을 사용합니다. IP 흐름 확인은 사용자에게 트래픽이 허용되거나 거부되는지를 알려줍니다. 거부되는 경우 IP 흐름 확인은 사용자에게 트래픽을 거부하는 보안 규칙을 알려줍니다.
* VM의 네트워크 연결이 실패하도록 하는 보안 규칙이 없는 경우 다음이 문제의 원인일 수 있습니다.
  * VM의 운영 체제 내에서 방화벽 소프트웨어가 실행되고 있습니다.
  * 가상 어플라이언스 또는 온-프레미스 트래픽에 대해 경로가 구성되었습니다. [강제 터널링](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 통해 인터넷 트래픽이 온-프레미스 네트워크로 리디렉션될 수 있습니다. 가상 어플라이언스 또는 온-프레미스로 인터넷 트래픽을 강제 터널링하는 경우 인터넷에서 VM에 연결할 수 없습니다. VM 외부의 트래픽 흐름을 방해할 수 있는 경로 문제를 진단하는 방법을 알아보려면 [가상 머신 네트워크 트래픽 라우팅 문제 진단](diagnose-network-routing-problem.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [네트워크 보안 그룹](manage-network-security-group.md#work-with-network-security-groups) 및 [보안 규칙](manage-network-security-group.md#work-with-security-rules)에 대한 모든 작업, 속성 및 설정에 대해 알아봅니다.
- VM에 대한 [기본 보안 규칙](./network-security-groups-overview.md#default-security-rules), [태그 서비스](./network-security-groups-overview.md#service-tags) 및 [Azure에서 인바운드 및 아웃바운드 트래픽에 대한 보안 규칙을 처리하는 방법](./network-security-groups-overview.md#network-security-groups)에 대해 알아봅니다.