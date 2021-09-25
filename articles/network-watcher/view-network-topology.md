---
title: Azure 가상 네트워크 토폴로지 보기 | Microsoft Docs
description: 가상 네트워크의 리소스 및 리소스 간의 관계를 보는 방법을 알아봅니다.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: damendo
ms.openlocfilehash: 26d9b36ec6633f08acfbcc14c6a2fa5a93eb17ae
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128666253"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Azure 가상 네트워크의 토폴로지 보기

이 문서에서는 Microsoft Azure 가상 네트워크의 리소스 및 리소스 간의 관계를 보는 방법에 대해 알아봅니다. 예를 들어 가상 네트워크에는 서브넷이 포함됩니다. 서브넷에는 Azure Virtual Machines(VM)과 같은 리소스가 포함됩니다. VM에는 하나 이상의 네트워크 인터페이스가 있습니다. 각 서브넷에는 네트워크 보안 그룹과 이에 연결된 경로 테이블이 있을 수 있습니다. Azure Network Watcher의 토폴로지 기능을 사용하면 가상 네트워크의 리소스, 가상 네트워크의 리소스에 연결된 리소스 및 리소스 간의 관계를 모두 볼 수 있습니다.

[Azure Portal](#azure-portal), [Azure CLI](#azure-cli) 또는 [PowerShell](#powershell)을 사용하여 토폴로지를 볼 수 있습니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="view-topology---azure-portal"></a><a name = "azure-portal"></a>토폴로지 보기 - Azure Portal

1. 필요한 [권한](required-rbac-permissions.md)이 있는 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 포털의 왼쪽 위 모서리에서 **모든 서비스** 를 선택합니다.
3. **모든 서비스** 필터 상자에서 *Network Watcher* 를 입력합니다. 결과에 **Network Watcher** 가 표시되면 이를 선택합니다.
4. **토폴로지** 를 선택합니다. 토폴로지를 생성하려면 토폴로지를 생성하려는 가상 네트워크가 있는 동일한 지역에 네트워크 감시자가 있어야 합니다. 토폴로지를 생성하려는 가상 네트워크가 있는 지역에 사용하도록 설정된 네트워크 감시자가 없는 경우 네트워크 감시자가 모든 지역에 자동으로 만들어집니다. 네트워크 감시자는 **NetworkWatcherRG** 라는 리소스 그룹에 만들어집니다.
5. 토폴로지를 보려는 가상 네트워크의 리소스 그룹인 구독을 선택한 다음, 가상 네트워크를 선택합니다. 다음 그림에서는 *MyVnet* 이라는 가상 네트워크에 대한 토폴로지가 *MyResourceGroup* 이라는 리소스 그룹에 표시됩니다.

    ![토폴로지 보기](./media/view-network-topology/view-topology.png)

    앞의 그림에서 볼 수 있듯이 가상 네트워크에는 세 개의 서브넷이 있습니다. 한 서브넷에는 배포된 VM이 있습니다. VM에는 연결된 네트워크 인터페이스 및 공용 IP 주소가 하나씩 있습니다. 다른 두 서브넷에는 연결된 경로 테이블이 있습니다. 각 경로 테이블에는 두 개의 경로가 있습니다. 한 서브넷에는 연결된 네트워크 보안 그룹이 있습니다. 토폴로지 정보는 다음의 리소스에 대해서만 표시됩니다.
    
    - *myVnet* 가상 네트워크와 동일한 리소스 그룹 및 지역 내부에 있습니다. 예를 들어, *MyResourceGroup* 이 아닌 리소스 그룹에 있는 네트워크 보안 그룹은 *MyVnet* 가상 네트워크의 서브넷에 연결되어 있어도 표시되지 않습니다.
    - *myVnet* 가상 네트워크 내부에 있거나 내부의 리소스와 연결되어 있습니다. 예를 들어, *myVnet* 가상 네트워크의 서브넷 또는 네트워크 인터페이스와 연결되지 않은 네트워크 보안 그룹은 *MyResourceGroup* 리소스 그룹에 있어도 표시되지 않습니다.

   그림에 표시된 토폴로지는 [Azure CLI](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) 또는 [PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)을 사용하여 배포할 수 있는 **네트워크 가상 어플라이언스를 통해 트래픽 라우팅** 스크립트 샘플을 배포한 후 만들어지는 가상 네트워크에 대한 것입니다.

6. **토폴로지 다운로드** 를 선택하여 이미지를 편집 가능한 파일을 svg 형식으로 다운로드합니다.

다이어그램에 표시된 리소스는 가상 네트워크에 있는 네트워킹 구성 요소의 하위 집합입니다. 예를 들어, 다이어그램에 네트워크 보안 그룹은 표시되지만, 해당 그룹 내의 보안 규칙은 표시되지 않습니다. 다이어그램에서는 구별되지 않지만, 선은 두 관계(*포함* 또는 *연결*) 중 하나를 나타냅니다. 가상 네트워크의 리소스에 대한 전체 목록과 리소스 간의 관계 유형을 확인하려면 [PowerShell](#powershell) 또는 [Azure CLI](#azure-cli)를 사용하여 토폴로지를 생성합니다.

## <a name="view-topology---azure-cli"></a><a name = "azure-cli"></a>토폴로지 보기 - Azure CLI

다음에 나오는 단계에서 명령을 실행할 수 있습니다.
- Azure Cloud Shell에서 명령의 오른쪽 위에 있는 **사용해 보세요** 를 선택합니다. Azure Cloud Shell은 공통 Azure 도구가 사전 설치되고 계정으로 사용하도록 구성된 체험 대화형 셸입니다.
- 컴퓨터에서 CLI를 실행합니다. 컴퓨터에서 CLI를 실행하는 경우 이 문서의 단계에서는 Azure CLI 버전 2.0.31 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 또한 Azure CLI를 로컬로 실행하는 경우 `az login`를 실행하여 Azure와 연결해야 합니다.

사용하는 계정에 필요한 [권한](required-rbac-permissions.md)이 있어야 합니다.

1. 토폴로지를 만들려는 가상 네트워크와 같은 지역에 이미 네트워크 감시자가 있는 경우 3단계로 건너뜁니다. [az group create](/cli/azure/group)를 사용하여 네트워크 감시자가 포함될 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 지역에 리소스 그룹을 만듭니다.

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. [az network watcher configure](/cli/azure/network/watcher#az_network_watcher_configure)를 사용하여 네트워크 감시자를 만듭니다. 다음 예제에서는 *eastus* 지역에 네트워크 감시자를 만듭니다.

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. [az network watcher show-topology](/cli/azure/network/watcher#az_network_watcher_show_topology)를 사용하여 토폴로지를 표시합니다. 다음 예제에서는 *MyResourceGroup* 이라는 리소스 그룹에 대한 토폴로지를 표시합니다.

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    *MyResourceGroup* 리소스 그룹과 동일한 리소스 그룹 및 네트워크 감시자와 동일한 지역에 있는 리소스에 대한 토폴로지 정보만 반환됩니다. 예를 들어, *MyResourceGroup* 이 아닌 리소스 그룹에 있는 네트워크 보안 그룹은 *MyVnet* 가상 네트워크의 서브넷에 연결되어 있어도 표시되지 않습니다.

   반환된 결과에서 관계 및 [속성](#properties)에 대해 자세히 알아봅니다. 토폴로지를 볼 수 있는 기존 가상 네트워크가 없는 경우 [네트워크 가상 어플라이언스를 통해 트래픽 라우팅](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) 스크립트 샘플을 사용하여 가상 네트워크를 만들 수 있습니다. 토폴로지 다이어그램을 보고 편집 가능한 파일로 다운로드하려면 [포털](#azure-portal)을 사용합니다.

## <a name="view-topology---powershell"></a><a name = "powershell"></a>토폴로지 보기 - PowerShell

다음에 나오는 단계에서 명령을 실행할 수 있습니다.
- Azure Cloud Shell에서 명령의 오른쪽 위에 있는 **사용해 보세요** 를 선택합니다. Azure Cloud Shell은 공통 Azure 도구가 사전 설치되고 계정으로 사용하도록 구성된 체험 대화형 셸입니다.
- 컴퓨터에서 PowerShell을 실행합니다. 컴퓨터에서 PowerShell을 실행하는 경우 이 문서에서는 Azure PowerShell `Az` 모듈이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

사용하는 계정에 필요한 [권한](required-rbac-permissions.md)이 있어야 합니다.

1. 토폴로지를 만들려는 가상 네트워크와 같은 지역에 이미 네트워크 감시자가 있는 경우 3단계로 건너뜁니다. [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)을 사용하여 네트워크 감시자가 포함될 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 지역에 리소스 그룹을 만듭니다.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher)를 사용하여 네트워크 감시자를 만듭니다. 다음 예제에서는 eastus 지역에 네트워크 감시자를 만듭니다.

    ```azurepowershell-interactive
    New-AzNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher)를 사용하여 네트워크 감시자 인스턴스를 검색합니다. 다음 예제에서는 미국 동부 지역의 네트워크 감시자를 검색합니다.

    ```azurepowershell-interactive
    $nw = Get-AzResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. [Get-AzNetworkWatcherTopology](/powershell/module/az.network/get-aznetworkwatchertopology)를 사용하여 토폴로지를 검색합니다. 다음 예제에서는 *MyResourceGroup* 이라는 리소스 그룹의 가상 네트워크에 대한 토폴로지를 검색합니다.

    ```azurepowershell-interactive
    Get-AzNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   *MyResourceGroup* 리소스 그룹과 동일한 리소스 그룹 및 네트워크 감시자와 동일한 지역에 있는 리소스에 대한 토폴로지 정보만 반환됩니다. 예를 들어, *MyResourceGroup* 이 아닌 리소스 그룹에 있는 네트워크 보안 그룹은 *MyVnet* 가상 네트워크의 서브넷에 연결되어 있어도 표시되지 않습니다.

   반환된 결과에서 관계 및 [속성](#properties)에 대해 자세히 알아봅니다. 토폴로지를 볼 수 있는 기존 가상 네트워크가 없는 경우 [네트워크 가상 어플라이언스를 통해 트래픽 라우팅](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) 스크립트 샘플을 사용하여 가상 네트워크를 만들 수 있습니다. 토폴로지 다이어그램을 보고 편집 가능한 파일로 다운로드하려면 [포털](#azure-portal)을 사용합니다.

## <a name="relationships"></a>관계

토폴로지에서 반환되는 모든 리소스에는 다른 리소스에 대한 다음 유형의 관계 중 하나가 있습니다.

| 관계 유형 | 예제                                                                                                |
| ---               | ---                                                                                                    |
| Containment       | 가상 네트워크에 서브넷이 포함됩니다. 서브넷에 네트워크 인터페이스가 포함됩니다.                            |
| 연결        | 네트워크 인터페이스가 VM과 연결됩니다. 공용 IP 주소가 네트워크 인터페이스에 연결됩니다. |

## <a name="properties"></a>속성

토폴로지에서 반환되는 모든 리소스에는 다음과 같은 속성이 있습니다.

- **Name** - 리소스의 이름입니다.
- **Id**: 리소스 그룹의 URI입니다.
- **Location**: 리소스가 있는 Azure 지역입니다.
- **Associations**: 참조되는 개체에 대한 연결 목록입니다. 각 연결에는 다음과 같은 속성이 있습니다.
    - **AssociationType**: 자식 개체 및 부모 개체 간의 관계를 참조합니다. 유효한 값은 *포함* 또는 *관련됨* 입니다.
    - **Name**: 참조되는 리소스의 이름입니다.
    - **ResourceId** - 연결에서 참조되는 리소스의 URI입니다.

## <a name="supported-resources"></a>지원되는 리소스

Network Watcher 토폴로지는 제한 된 리소스 집합을 지원 합니다. Virtual Network, 서브넷, 네트워크 인터페이스, 네트워크 보안 그룹, Load Balancer, Load Balancer 상태 프로브, 공용 IP, Virtual Network 피어 링, 가상 네트워크 게이트웨이, VPN Gateway 연결, 가상 컴퓨터 및 가상 머신 확장 집합이 있습니다.

## <a name="next-steps"></a>다음 단계

- Network Watcher의 IP 흐름 확인 기능을 사용하여 [VM과의 네트워크 트래픽 필터 문제를 진단](diagnose-vm-network-traffic-filtering-problem.md)하는 방법을 알아봅니다.
- Network Watcher의 다음 홉 기능을 사용하여 [VM에서 네트워크 트래픽 라우팅 문제를 진단](diagnose-vm-network-routing-problem.md)하는 방법을 알아봅니다.
