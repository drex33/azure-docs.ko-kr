---
title: Azure VPN Gateway에서 NAT 구성
titleSuffix: Azure VPN Gateway
description: Azure VPN Gateway에서 NAT를 구성하는 방법을 알아봅니다.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/29/2021
ms.author: yushwang
ms.openlocfilehash: 66aceaeff2f226b7afa4ee16d4affd0584261bab
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271007"
---
# <a name="how-to-configure-nat-on-azure-vpn-gateways"></a>Azure VPN gateway에서 NAT를 구성 하는 방법

이 문서는 Azure Portal을 사용하여 Azure VPN Gateway에서 NAT(Network Address Translation)를 구성하는 데 도움이 됩니다.

## <a name="about-nat"></a><a name="about"></a>NAT 정보

NAT는 IP 패킷의 한 IP 주소를 다른 IP 주소로 번역하는 메커니즘을 정의합니다. 일반적으로 IP 주소 범위가 겹치는 네트워크를 연결하는 데 사용됩니다. 네트워크를 연결하는 게이트웨이 디바이스에 대한 NAT 규칙이나 정책은 네트워크에서 주소 변환에 사용되는 주소 매핑을 지정합니다.

Azure VPN 게이트웨이에서 NAT 지원에 대한 자세한 내용은 [Azure VPN Gateway의 NAT 정보](nat-overview.md)를 참조하세요.

> [!IMPORTANT] 
> * NAT는 VpnGw2~5, VpnGw2AZ~5AZ와 같은 SKU에서 지원됩니다.

## <a name="getting-started"></a>시작

이 문서의 각 부는 네트워크 연결에서 NAT를 구성하는 데 사용되는 기본 구성 요소를 형성하는 데 도움이 됩니다. 세 부를 모두 완료하면 다이어그램 1에 표시된 대로 토폴로지를 빌드합니다.

### <a name="diagram-1"></a><a name="diagram"></a>다이어그램 1

:::image type="content" source="./media/nat-overview/vpn-nat.png" alt-text="다이어그램 1 스크린샷" lightbox="./media/nat-overview/vpn-nat.png" border="false":::

### <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.

## <a name="part-1-create-vnet-and-gateways"></a><a name ="vnet"></a>1부: VNet 및 게이트웨이 만들기

이 섹션에서는 [다이어그램 1](#diagram)에 표시된 리소스에 해당하는 가상 네트워크, VPN 게이트웨이 및 로컬 네트워크 게이트웨이 리소스를 만듭니다.

이러한 리소스를 만들려면 [사이트 간 자습서](tutorial-site-to-site-portal.md) 문서의 단계를 수행합니다. 이 문서의 다음 섹션을 완료하지만 연결을 만들지는 않습니다.

* [VNet](tutorial-site-to-site-portal.md#CreatVNet)
* [VPN Gateway](tutorial-site-to-site-portal.md#VNetGateway)
* [로컬 네트워크 게이트웨이](tutorial-site-to-site-portal.md#LocalNetworkGateway)
* [VPN 디바이스 구성](tutorial-site-to-site-portal.md#VPNDevice)


>[!IMPORTANT]
>  다음 문서의 단계를 수행할 경우 문서의 **연결** 리소스를 만들지 마세요. IP 주소 공간이 VNet, 분기 1 및 분기 2 사이에서 동일하므로 작업이 실패합니다. 다음 섹션의 단계를 수행하여 NAT 규칙을 만든 다음, NAT 규칙을 사용하여 연결을 만듭니다.
>


다음 스크린샷에서는 만들 리소스의 예를 보여줍니다.

* **VNet**

   :::image type="content" source="./media/nat-howto/vnet.png" alt-text="VNet 주소 공간을 보여주는 스크린샷" lightbox="./media/nat-howto/vnet.png":::
* **VPN Gateway**

   :::image type="content" source="./media/nat-howto/vpn-gateway.png" alt-text="게이트웨이를 보여주는 스크린샷" lightbox="./media/nat-howto/vpn-gateway.png":::
* **분기 1 로컬 네트워크 게이트웨이**

   :::image type="content" source="./media/nat-howto/branch-1.png" alt-text="분기 1 로컬 네트워크 게이트웨이를 보여주는 스크린샷" lightbox="./media/nat-howto/branch-1.png" :::

* **분기 2 로컬 네트워크 게이트웨이**

   :::image type="content" source="./media/nat-howto/branch-2.png" alt-text="분기 2 로컬 네트워크 게이트웨이를 보여주는 스크린샷" lightbox="./media/nat-howto/branch-2.png":::


## <a name="part-2-create-nat-rules"></a><a name ="nat-rules"></a>2부: NAT 규칙 만들기

연결을 만들기 전에 VPN 게이트웨이에서 NAT 규칙을 만들고 저장해야 합니다. 다음 표에서는 필수 NAT 규칙을 보여줍니다. 토폴로지는 [다이어그램 1](#diagram)을 참조하세요.

**NAT 규칙 테이블**

| Name     | 유형   | 모드        | 내부    | 외부     | 연결          |
| ---      | ---    | ---         | ---         | ---          | ---                 |
| VNet     | 정적 | EgressSNAT  | 10.0.1.0/24 | 100.0.1.0/24 | 두 연결 모두    | 
| Branch_1 | 정적 | IngressSNAT | 10.0.1.0/24 | 100.0.2.0/24 | 분기 1 연결 |
| Branch_2 | 정적 | IngressSNAT | 10.0.1.0/24 | 100.0.3.0/24 | 분기 2 연결 |

다음 단계를 수행하여 VPN 게이트웨이에서 모든 NAT 규칙을 만듭니다.

1. Azure Portal에서 **Virtual Network 게이트웨이** 리소스 페이지로 이동 하 고 **NAT 규칙** 을 선택 합니다.
1. 위 **NAT 규칙 테이블** 을 사용하여 값을 채웁니다.

   :::image type="content" source="./media/nat-howto/nat-rules.png" alt-text="NAT 규칙을 보여주는 스크린샷" lightbox="./media/nat-howto/nat-rules.png":::
1. **저장** 을 클릭하여 NAT 규칙을 VPN 게이트웨이 리소스에 저장합니다. 이 작업을 완료하는 데 최대 10분이 걸릴 수 있습니다.

## <a name="part-3-create-connections-and-link-nat-rules"></a><a name ="connections"></a>3부: 연결 만들기 및 NAT 규칙 연결

이 섹션에서는 연결을 만든 다음, NAT 규칙을 연결에 연결하여 [다이어그램 1](#diagram)의 샘플 토폴로지를 구현합니다.

### <a name="1-create-connections"></a>1. 연결 만들기

[사이트 간 연결 만들기](tutorial-site-to-site-portal.md) 문서의 단계를 수행하여 다음과 같이 연결 2개를 만듭니다.

   :::image type="content" source="./media/nat-howto/connections.png" alt-text="연결 페이지를 보여주는 스크린샷" lightbox="./media/nat-howto/connections.png":::

### <a name="2-associate-nat-rules-with-the-connections"></a>2. 연결에 NAT 규칙 연결

이 단계에서는 각 연결 리소스에 NAT 규칙을 연결합니다.

1. Azure Portal에서 연결 리소스로 이동하고 **구성** 을 선택합니다.

1. 수신 NAT 규칙에서 이전에 만든 NAT 규칙을 선택합니다.

   :::image type="content" source="./media/nat-howto/config-nat.png" alt-text="구성된 NAT 규칙을 보여주는 스크린샷" lightbox="./media/nat-howto/config-nat.png":::

1. **저장** 을 클릭하여 구성을 연결 리소스에 적용합니다.

1. 단계를 반복하여 다른 연결 리소스에 NAT 규칙을 적용합니다.

1. BGP를 사용하는 경우 NAT 규칙 페이지에서 **BGP 경로 변환 사용** 을 선택하고 **저장** 을 클릭합니다. 이제 테이블에 각 NAT 규칙과 연결된 연결이 표시됩니다.

   :::image type="content" source="./media/nat-howto/nat-rules-linked.png" alt-text="BGP 사용을 보여주는 스크린샷" lightbox="./media/nat-howto/nat-rules-linked.png":::

이러한 단계를 완료한 후에는 [다이어그램 1](#diagram)에 표시된 토폴로지와 일치하는 설정을 갖게 됩니다.

## <a name="limitations"></a><a name ="limits"></a>제한 사항

> [!IMPORTANT] 
> NAT 기능에는 몇 가지 제약 조건이 있습니다.
>

* NAT는 VpnGw2~5, VpnGw2AZ~5AZ와 같은 SKU에서 지원됩니다.
* NAT는 IPsec/IKE 크로스-프레미스 연결에만 지원됩니다. VNet 간 연결이나 P2S 연결은 지원되지 않습니다.
* NAT 규칙은 연결 만들기 프로세스 중에 연결 리소스와 연결될 수 없습니다. 먼저 연결 리소스를 만든 다음, 연결 구성 페이지에서 NAT 규칙을 연결합니다.
* 다른 로컬 네트워크 게이트웨이(온-프레미스 네트워크 또는 분기)의 주소 공간은 [다이어그램 1](#diagram)과 같은 겹치지 않는 접두사와 매핑하도록 *IngressSNAT* 규칙과 동일할 수 있습니다.
* *정책 기반 트래픽 선택기 사용* 이 사용되는 연결에서는 NAT 규칙이 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

연결이 완료되면 가상 네트워크에 가상 머신을 추가할 수 있습니다. 단계는 [Virtual Machine 만들기](../virtual-machines/windows/quick-create-portal.md) 를 참조하세요.
