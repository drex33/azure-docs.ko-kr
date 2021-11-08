---
title: Azure 프라이빗 엔드포인트를 통해 VNet과 Azure Web PubSub 서비스 간의 트래픽을 보호하는 방법
description: 가상 네트워크에서 Azure Web PubSub 서비스에 안전하게 액세스하기 위한 프라이빗 엔드포인트를 대략적으로 설명합니다.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: c05112ec35197f8a17220b31e5032ae5d2469cf8
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995051"
---
# <a name="use-private-endpoints-for-azure-web-pubsub-service"></a>Azure Web PubSub 서비스에 프라이빗 엔드포인트 사용

Azure Web PubSub 서비스에 [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 사용하면 VNet(가상 네트워크)의 클라이언트가 [프라이빗 링크](../private-link/private-link-overview.md)를 통해 데이터에 안전하게 액세스할 수 있습니다. 프라이빗 엔드포인트는 Azure Web PubSub 서비스에 대한 VNet 주소 공간의 IP 주소를 사용합니다. VNet과 Azure Web PubSub 서비스에 있는 클라이언트 사이의 네트워크 트래픽은 Microsoft 백본 네트워크의 프라이빗 링크를 가로지르며 공용 인터넷 노출을 방지합니다.

Azure Web PubSub 서비스에 대한 프라이빗 엔드포인트를 사용하면 다음을 수행할 수 있습니다.

- 네트워크 액세스 제어를 사용하여 Azure Web PubSub 서비스에 대한 퍼블릭 엔드포인트의 모든 연결을 차단함으로써 Azure Web PubSub 서비스를 보호합니다.
- VNet(가상 네트워크)에서의 데이터 반출을 차단하도록 하여 VNet 보안을 강화합니다.
- 개인 피어링을 통해 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [ExpressRoutes](../expressroute/expressroute-locations.md)를 사용하여 VNet에 연결하는 온-프레미스 네트워크에서 Azure Web PubSub 서비스에 안전하게 연결합니다.

## <a name="conceptual-overview"></a>개념적 개요

:::image type="content" source="./media/howto-secure-private-endpoints/private-endpoint-overview.png" alt-text="Azure Web PubSub 서비스의 프라이빗 엔드포인트 개요":::

프라이빗 엔드포인트는 VNet([Virtual Network](../virtual-network/virtual-networks-overview.md))의 Azure 서비스에 대한 특별 네트워크 인터페이스입니다. Azure Web PubSub 서비스에 프라이빗 엔드포인트를 만들 때 VNet 및 서비스의 클라이언트 간에 보안 연결을 제공합니다. 프라이빗 엔드포인트에는 VNet의 IP 주소 범위 내에서 IP 주소가 할당됩니다. 프라이빗 엔드포인트와 Azure Web PubSub 서비스 간의 연결은 보안 프라이빗 링크를 사용합니다.

VNet의 애플리케이션은 **다른 방법으로 사용하는 것과 동일한 연결 문자열 및 권한 부여 메커니즘을 사용하여** 프라이빗 엔드포인트를 통해 Azure Web PubSub 서비스에 원활하게 연결할 수 있습니다. 프라이빗 엔드포인트는 REST API를 포함하여 Azure Web PubSub 서비스에서 지원하는 모든 프로토콜과 함께 사용할 수 있습니다.

VNet에서 Azure Web PubSub 서비스에 대한 프라이빗 엔드포인트를 만드는 경우 승인을 위해 Azure Web PubSub 서비스 소유자에게 동의 요청이 전송됩니다. 프라이빗 엔드포인트 만들기를 요청한 사용자가 Azure Web PubSub 서비스의 소유자인 경우 이 동의 요청은 자동으로 승인됩니다.

Azure Web PubSub 서비스 소유자는 [Azure Portal](https://portal.azure.com)의 Azure Web PubSub 서비스에 대한 '*프라이빗 엔드포인트*' 탭을 통해 동의 요청 및 프라이빗 엔드포인트를 관리할 수 있습니다.

> [!TIP]
> 프라이빗 엔드포인트를 통해서만 Azure Web PubSub 서비스에 대한 액세스를 제한하려면 퍼블릭 엔드포인트를 통해 액세스를 거부하거나 제어하도록 [네트워크 액세스 제어를 구성](howto-secure-network-access-control.md)합니다.

### <a name="connecting-to-private-endpoints"></a>프라이빗 엔드포인트에 연결

프라이빗 엔드포인트를 사용하는 VNet의 클라이언트는 퍼블릭 엔드포인트에 연결하는 클라이언트와 동일한 연결 문자열을 Azure Web PubSub 서비스에 사용해야 합니다. DNS 확인에 의존하여 프라이빗 링크를 통해 VNet에서 Azure Web PubSub 서비스로의 연결을 자동으로 라우팅합니다.

> [!IMPORTANT]
> 다른 방법으로는 동일한 연결 문자열을 사용하여 프라이빗 엔드포인트를 통해 Azure Web PubSub 서비스에 연결합니다. `privatelink` 하위 도메인 URL을 사용하여 Azure Web PubSub 서비스에 연결하지 마세요.

기본적으로 프라이빗 엔드포인트에 대한 필수 업데이트를 사용하여 VNet에 연결된 [프라이빗 DNS 영역](../dns/private-dns-overview.md)을 만듭니다. 그러나 자체 DNS 서버를 사용하는 경우 DNS 구성을 추가로 변경해야 할 수 있습니다. 아래 [DNS 변경](#dns-changes-for-private-endpoints) 섹션에서는 프라이빗 엔드포인트에 필요한 업데이트에 대해 설명합니다.

## <a name="dns-changes-for-private-endpoints"></a>프라이빗 엔드포인트용 DNS 변경

프라이빗 엔드포인트를 만들 때 Azure Web PubSub 서비스에 대한 DNS CNAME 리소스 레코드는 `privatelink` 접두사가 있는 하위 도메인의 별칭으로 업데이트됩니다. 또한 기본적으로 프라이빗 엔드포인트에 대한 DNS A 리소스 레코드를 사용하여 `privatelink` 하위 도메인에 해당하는 [프라이빗 DNS 영역](../dns/private-dns-overview.md)을 만듭니다.

프라이빗 엔드포인트를 사용하여 VNet 외부에서 Azure Web PubSub 서비스 도메인 이름을 확인하면 Azure Web PubSub 서비스의 퍼블릭 엔드포인트로 확인됩니다. 프라이빗 엔드포인트를 호스트하는 VNet에서 확인되면 도메인 이름은 프라이빗 엔드포인트의 IP 주소로 확인됩니다.

위에 설명된 예시에서 프라이빗 엔드포인트를 호스트하는 VNet 외부에서 확인되는 경우 Azure Web PubSub 서비스 'foobar'에 대한 DNS 리소스 레코드는 다음과 같습니다.

| 이름                                                  | 유형  | 값                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.webpubsub.azure.com``                        | CNAME | ``foobar.privatelink.webpubsub.azure.com``            |
| ``foobar.privatelink.webpubsub.azure.com``            | A     | \<Azure Web PubSub service public IP address\>           |

앞에서 설명했듯이 네트워크 액세스 제어를 사용하여 퍼블릭 엔드포인트를 통해 VNet 외부의 클라이언트에 대한 액세스를 거부하거나 제어할 수 있습니다.

'foobar'에 대한 DNS 리소스 레코드는 프라이빗 엔드포인트를 호스트하는 VNet의 클라이언트에서 확인되는 경우 다음과 같습니다.

| 이름                                                  | 유형  | 값                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.webpubsub.azure.com``                        | CNAME | ``foobar.privatelink.webpubsub.azure.com``            |
| ``foobar.privatelink.webpubsub.azure.com``            | A     | 10.1.1.5                                              |

이 방법을 사용하면 프라이빗 엔드포인트를 호스트하는 VNet의 클라이언트와 VNet 외부의 클라이언트에 **동일한 연결 문자열을 사용** 하여 Azure Web PubSub 서비스에 액세스할 수 있습니다.

네트워크에서 사용자 지정 DNS 서버를 사용하는 경우 클라이언트는 프라이빗 엔드포인트 IP 주소에 대한 Azure Web PubSub 서비스 엔드포인트의 FQDN을 확인할 수 있어야 합니다. 프라이빗 링크 하위 도메인을 VNet의 프라이빗 DNS 영역에 위임하도록 DNS 서버를 구성하거나 프라이빗 엔드포인트 IP 주소를 사용하여 `foobar.privatelink.webpubsub.azure.com`에 대한 A 레코드를 구성해야 합니다.

> [!TIP]
> 사용자 지정 또는 온-프레미스 DNS 서버를 사용하는 경우 `privatelink` 하위 도메인의 Azure Web PubSub 서비스 이름을 프라이빗 엔드포인트 IP 주소로 확인하도록 DNS 서버를 구성해야 합니다. `privatelink` 하위 도메인을 VNet의 프라이빗 DNS 영역에 위임하거나 DNS 서버에서 DNS 영역을 구성하고 DNS A 레코드를 추가하여 이를 수행할 수 있습니다.

Azure Web PubSub 서비스에 대한 프라이빗 엔드포인트의 권장 DNS 영역 이름은 `privatelink.webpubsub.azure.com`입니다.

프라이빗 엔드포인트 지원 목적으로 자체 DNS 서버를 구성하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 가상 네트워크의 리소스 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [프라이빗 엔드포인트에 대한 DNS 구성](../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기

### <a name="create-a-private-endpoint-along-with-a-new-azure-web-pubsub-service-in-the-azure-portal"></a>Azure Portal에서 새 Azure Web PubSub 서비스와 함께 프라이빗 엔드포인트를 만듭니다.

1. 새 Azure Web PubSub 서비스를 만들 때 **네트워킹** 탭을 선택합니다. 연결 방법으로 **프라이빗 엔드포인트** 를 선택합니다.

    :::image type="content" source="./media/howto-secure-private-endpoints/portal-create-blade-networking-tab.png" alt-text="Azure Web PubSub 서비스 만들기 - 네트워킹 탭.":::

1. **추가** 를 선택합니다. 새 프라이빗 엔드포인트에 대한 구독, 리소스 그룹, 위치, 이름을 입력합니다. 가상 네트워크 및 서브넷을 선택합니다.

1. **검토 + 만들기** 를 선택합니다.

### <a name="create-a-private-endpoint-for-an-existing-azure-web-pubsub-service-in-the-azure-portal"></a>Azure Portal에서 기존 Azure Web PubSub 서비스에 대한 프라이빗 엔드포인트를 만듭니다.

1. Azure Web PubSub 서비스로 이동합니다.

1. **프라이빗 엔드포인트 연결** 이라는 설정 메뉴에서 선택합니다.

1. 위쪽의 **+ 프라이빗 엔드포인트** 단추를 선택합니다.

1. 새 프라이빗 엔드포인트에 대한 구독, 리소스 그룹, 리소스 이름 및 지역을 입력합니다.

1. 대상 Azure Web PubSub 서비스 리소스를 선택합니다.

1. 대상 가상 네트워크를 선택합니다.

1. **검토 + 만들기** 를 선택합니다.

## <a name="pricing"></a>가격 책정

가격 책정에 대한 자세한 내용은 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link)을 참조하세요.

## <a name="known-issues"></a>알려진 문제

Azure Web PubSub 서비스의 프라이빗 엔드포인트에 관련된 다음과 같은 알려진 문제에 유의합니다.

### <a name="free-tier"></a>무료 계층

Azure Web PubSub 서비스 무료 계층 인스턴스는 프라이빗 엔드포인트와 통합할 수 없습니다.

### <a name="access-constraints-for-clients-in-vnets-with-private-endpoints"></a>프라이빗 엔드포인트가 있는 VNet의 클라이언트에 대한 액세스 제약 조건

기존 프라이빗 엔드포인트를 사용하는 VNet의 클라이언트는 프라이빗 엔드포인트가 있는 다른 Azure Web PubSub 서비스 인스턴스에 액세스할 때 제약 조건이 걸립니다. 예를 들어, VNet N1에 Azure Web PubSub 서비스 인스턴스 W1에 대한 프라이빗 엔드포인트가 있다고 가정합니다. Azure Web PubSub 서비스 W2의 VNet N2에 프라이빗 엔드포인트가 있으면 VNet N1의 클라이언트는 프라이빗 엔드포인트를 사용하여 Azure Web PubSub 서비스 W2에도 액세스해야 합니다. Azure Web PubSub 서비스 W2에 프라이빗 엔드포인트가 없으면 VNet N1의 클라이언트는 프라이빗 엔드포인트 없이 해당 계정에서 Azure Web PubSub 서비스에 액세스할 수 있습니다.

이 제약 조건은 Azure Web PubSub 서비스 W2가 프라이빗 엔드포인트를 만들 때 적용되는 DNS 변경의 결과입니다.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>프라이빗 엔드포인트가 있는 서브넷의 네트워크 보안 그룹 규칙

현재 프라이빗 엔드포인트에 대한 NSG([네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md)) 규칙 및 사용자 정의 경로를 구성할 수 없습니다. 프라이빗 엔드포인트를 호스트하는 서브넷에 적용된 NSG 규칙은 프라이빗 엔드포인트에 적용됩니다. 이 문제를 부분적으로 해결하는 방법은 원본 서브넷의 프라이빗 엔드포인트에 대한 액세스 규칙 구현입니다. 단, 이 방법을 쓰면 더 높은 관리 오버헤드가 필요할 수 있습니다.

