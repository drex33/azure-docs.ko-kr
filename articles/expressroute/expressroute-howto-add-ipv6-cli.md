---
title: 'Azure ExpressRoute: Azure CLI 사용하여 IPv6 지원 추가'
description: IPv6 지원을 추가하여 Azure CLI 사용하여 Azure 배포에 연결하는 방법을 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 09/27/2021
ms.author: duau
ms.openlocfilehash: 1c0b751d6a5107dcf9b7d04423b0b96e0036615d
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129730416"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-cli-preview"></a>Azure CLI 사용하여 프라이빗 피어링에 대한 IPv6 지원 추가(미리 보기)

이 문서에서는 Azure CLI 사용하여 ExpressRoute를 통해 Azure의 리소스에 연결하는 IPv6 지원을 추가하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md) 및 [워크플로](expressroute-workflows.md)를 검토합니다.
* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 최신 버전의 CLI 명령(2.0 이상)을 설치합니다. CLI 설치 명령에 대한 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli) 및 [Azure CLI 시작](/cli/azure/get-started-with-azure-cli)을 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>ExpressRoute 회로에 IPv6 개인 피어링 추가

1. [ExpressRoute 회로를 만들거나](howto-circuit-cli.md) 기존 회로를 사용합니다. 다음을 실행하여 회로 세부 정보를 봅니다.

    ```azurecli-interactive
    az network express-route show --resource-group "<ExpressRouteResourceGroup>" --name "<MyCircuit>"
    ```

2. 다음을 실행하여 회로에 대한 프라이빗 피어링 구성을 봅니다.

    ```azurecli-interactive
    az network express-route peering show -g "<ExpressRouteResourceGroup>" --circuit-name "<MyCircuit>" --name AzurePrivatePeering
    ```

3. 기존 IPv4 개인 피어링 구성에 IPv6 개인 피어링을 추가합니다. 기본 링크 및 보조 링크에 대해 소유하고 있는 /126 IPv6 서브넷 한 쌍을 제공합니다. Microsoft에서 사용 가능한 두 번째 IP를 라우터에 사용하므로, 이러한 각 서브넷에서는 사용 가능한 첫 번째 IP 주소를 라우터에 할당하겠습니다.

    ```azurecli-interactive
    az network express-route peering update -g "<ExpressRouteResourceGroup>" --circuit-name "<MyCircuit>" --name AzurePrivatePeering --ip-version ipv6 --primary-peer-subnet "<X:X:X:X/126>" --secondary-peer-subnet "<Y:Y:Y:Y/126>"
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>기존 가상 네트워크에 대한 연결 업데이트

IPv6 개인 피어링을 사용하려는 Azure 리소스의 기존 환경이 있는 경우 다음 단계를 수행합니다.

1. ExpressRoute 회로가 연결된 가상 네트워크에 IPv6 주소 공간을 추가합니다.

    ```azurecli-interactive
    az network vnet update -g "<MyResourceGroup>" -n "<MyVNet>" --address-prefixes "X:X:X:X::/64"
    ```

3. 게이트웨이 서브넷에 IPv6 주소 공간을 추가합니다. 게이트웨이 IPv6 서브넷은 /64 이상이어야 합니다.

    ```azurecli-interactive
    az network vnet subnet update -g "<MyResourceGroup>" -n "<MySubnet>" -vnet-name "<MyVNet>" --address-prefixes "10.0.0.0/26", "X:X:X:X::/64"
    ```

4. 기존 영역 중복 게이트웨이가 있는 경우 다음을 실행하여 IPv6 연결을 사용하도록 설정합니다(변경 사항이 반영되는 데 최대 1시간이 걸릴 수 있음). 그렇지 않으면 SKU를 사용하여 [가상 네트워크 게이트웨이를 만듭니다](expressroute-howto-add-gateway-resource-manager.md). FastPath를 사용하려는 경우 UltraPerformance 또는 ErGw3AZ를 사용합니다(이 옵션은 ExpressRoute Direct를 사용하는 회로에서만 사용 가능).

    ```azurecli-interactive
    az network vnet-gateway update --name "<GatewayName>" --resource-group "<MyResourceGroup>"
    ```
>[!NOTE]
> 영역 중복이 아닌 기존 게이트웨이(표준, 고성능 또는 울트라 성능 SKU)가 있고 기본 SKU의 공용 IP 주소를 사용하는 경우 SKU 및 표준, 고정 공용 IP 주소를 사용하여 [게이트웨이를](expressroute-howto-add-gateway-resource-manager.md#add-a-gateway) 삭제하고 다시 만들어야 합니다.

## <a name="create-a-connection-to-a-new-virtual-network"></a>새 가상 네트워크에 대한 연결 만들기

IPv6 프라이빗 피어링을 사용하여 새로운 Azure 리소스 집합에 연결하려는 경우 아래 단계를 따르세요.

1. IPv4/IPv6 주소 공간을 모두 사용하여 이중 스택 가상 네트워크를 만듭니다. 자세한 내용은 [가상 네트워크 만들기](../virtual-network/quick-create-cli.md)를 참조하세요.

2. [이중 스택 게이트웨이 서브넷을 만듭니다](expressroute-howto-add-gateway-resource-manager.md#add-a-gateway).

3. SKU를 사용하여 [가상 네트워크 게이트웨이를 만듭니다](expressroute-howto-add-gateway-resource-manager.md#add-a-gateway). FastPath를 사용하려는 경우 UltraPerformance 또는 ErGw3AZ를 사용합니다(이 옵션은 ExpressRoute Direct를 사용하는 회로에서만 사용 가능).

4. [가상 네트워크를 ExpressRoute 회로에 연결합니다](howto-linkvnet-cli.md).

## <a name="limitations"></a>제한 사항
IPv6 지원은 글로벌 Azure 지역의 배포에 연결할 수 있지만 다음 사용 사례는 지원하지 않습니다.

* 영역 중복이 아닌 *기존* ExpressRoute 게이트웨이에 대한 연결입니다. 표준 고정 IP 주소를 사용하는 모든 SKU(영역 중복 및 비)의 *새로* 만든 ExpressRoute 게이트웨이는 이중 스택 ExpressRoute 연결에 사용할 수 있습니다.
* ExpressRoute 회로 간의 Global Reach 연결
* 가상 WAN으로 ExpressRoute 사용
* 비ExpressRoute Direct 회로가 있는 FastPath
* 다음 피어링 위치, 즉 두바이에 회로가 있는 FastPath
* VPN Gateway와 함께 사용

## <a name="next-steps"></a>다음 단계

ExpressRoute 문제를 해결하려면 다음 문서를 참조하세요.

* [ExpressRoute 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
* [네트워크 성능 문제 해결](expressroute-troubleshooting-network-performance.md)
