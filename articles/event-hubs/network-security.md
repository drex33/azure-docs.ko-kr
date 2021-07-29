---
title: Azure Event Hubs에 대한 네트워크 보안
description: 이 문서에서는 프라이빗 엔드포인트에서 액세스를 구성하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 05/04/2021
ms.openlocfilehash: 39bad6fabb0f8f639449cd20033b8b31139d98b1
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111413414"
---
# <a name="network-security-for-azure-event-hubs"></a>Azure Event Hubs에 대한 네트워크 보안 
이 문서에서는 Azure Event Hubs에서 다음 보안 기능을 사용하는 방법을 설명합니다. 

- 서비스 태그
- IP 방화벽 규칙
- 네트워크서비스 엔드포인트
- 프라이빗 엔드포인트


## <a name="service-tags"></a>서비스 태그
서비스 태그는 지정된 Azure 서비스의 IP 주소 접두사 그룹을 나타냅니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트하여 네트워크 보안 규칙을 자주 업데이트할 때 발생하는 복잡성을 최소화합니다. 서비스 태그에 대한 자세한 내용은 [서비스 태그 개요](../virtual-network/service-tags-overview.md)를 참조하세요.

서비스 태그를 사용하여 [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md#security-rules) 또는 [Azure Firewall](../firewall/service-tags.md)에서 네트워크 액세스 제어를 정의할 수 있습니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용합니다. 서비스 태그 이름(예: **EventHub**)을 규칙의 적절한 *원본* 또는 *대상* 필드에 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다.

| 서비스 태그 | 목적 | 인바운드 또는 아웃바운드를 사용할 수 있나요? | 지역 범위를 지원할 수 있나요? | Azure Firewall에서 사용할 수 있나요? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **EventHub** | Azure Event Hubs입니다. | 아웃바운드 | 예 | 예 |


## <a name="ip-firewall"></a>IP 방화벽 
기본적으로 요청에 유효한 인증 및 권한 부여가 제공되는 한 Event Hubs 네임스페이스는 인터넷에서 액세스할 수 있습니다. IP 방화벽을 사용하면 [CIDR(Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 표기법으로 IPv4 주소 또는 IPv4 주소 범위 세트로만 제한할 수 있습니다.

이 기능은 잘 알려진 특정 사이트에서만 Azure Event Hub에 액세스할 수 있는 시나리오에서 유용합니다. 방화벽 규칙을 사용하면 특정 IPv4 주소에서 발생하는 트래픽을 허용하도록 규칙을 구성할 수 있습니다. 예를 들어 [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services)와 함께 Event Hubs를 사용하는 경우 온-프레미스 인프라 IP 주소에서 오는 트래픽만 허용하도록 **방화벽 규칙** 을 만들 수 있습니다. 

IP 방화벽 규칙은 Event Hubs 네임스페이스 수준에 적용됩니다. 따라서 해당 규칙은 지원되는 모든 프로토콜을 사용하는 클라이언트의 모든 연결에 적용됩니다. Event Hubs 네임스페이스에서 허용된 IP 규칙과 일치하지 않는 IP 주소의 연결 시도는 권한이 없는 것으로 거부됩니다. 응답은 IP 규칙을 언급하지 않습니다. IP 필터 규칙은 순서대로 적용되며 IP 주소와 일치하는 첫 번째 규칙이 수락 또는 거부 작업을 결정합니다.

자세한 내용은 [이벤트 허브에 대한 IP 방화벽을 구성하는 방법](event-hubs-ip-filtering.md)을 참조하세요.

## <a name="network-service-endpoints"></a>네트워크서비스 엔드포인트
[VNet(Virtual Network) 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)와 Event Hubs를 통합하면 양쪽 엔드에서 네트워크 트래픽 경로를 보호하여 가상 네트워크에 바인딩된 가상 머신과 같은 워크로드의 메시징 기능에 대한 액세스를 보호할 수 있습니다.

적어도 하나의 가상 네트워크 서브넷 서비스 엔드포인트에 바인딩되도록 구성하면 해당하는 Event Hubs 네임스페이스는 권한이 부여된 가상 네트워크의 서브넷을 제외한 곳의 트래픽을 더는 허용하지 않습니다. 가상 네트워크 큐브 뷰에서 Event Hubs 네임스페이스를 서비스 엔드포인트에 바인딩하면 가상 네트워크 서브넷에서 메시징 서비스로 격리된 네트워킹 터널을 구성합니다. 

메시징 서비스 엔드포인트의 관찰 가능한 네트워크 주소가 공용 IP 범위에 있음에도 서브넷에 바인딩된 워크로드와 해당하는 Event Hubs 네임스페이스 간에 격리된 프라이빗 관계가 생성됩니다. 이 동작에는 예외가 있습니다. 기본적으로 서비스 엔드포인트를 사용하도록 설정하면 가상 네트워크와 연결된 [IP 방화벽](event-hubs-ip-filtering.md)에서 `denyall` 규칙을 사용할 수 있습니다. IP 방화벽의 특정 IP 주소를 추가하여 Event Hub 퍼블릭 엔드포인트에 액세스할 수 있습니다. 

> [!IMPORTANT]
> 가상 네트워크는 **기본** 및 **프리미엄** 계층에서 지원되지 않습니다.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet 통합에서 사용하도록 설정한 고급 보안 시나리오 

엄격하게 분류된 보안이 필요한 솔루션은 일반적으로 해당 구획에 위치한 서비스 간에 통신 경로가 필요합니다. 해당 솔루션에서 가상 네트워크 서브넷은 분류된 서비스를 서로 구분합니다.

TCP/IP에서 HTTPS를 수행하는 경로를 비롯한 구획 간의 즉시 IP 경로를 사용하면 네트워크 레이어에서 취약성을 악용할 위험이 있습니다. 메시징 서비스는 격리된 통신 경로를 제공합니다. 여기서 메시지는 항목을 전환할 때 디스크에도 작성됩니다. 동일한 Event Hubs 인스턴스에 바인딩된 두 개의 고유 가상 네트워크에 있는 워크로드는 메시지를 통해 효율적이고 안정적으로 통신할 수 있습니다. 이 동안에 해당 네트워크 격리 경계의 무결성은 지켜집니다.
 
즉, 보안이 중요한 클라우드 솔루션은 Azure에서 제공하는 업계 최고의 안정적이고 확장성 있는 비동기 메시징 기능에 액세스할 뿐만 아니라 메시징을 사용하여 HTTPS 및 기타 TLS 보안 소켓 프로토콜을 비롯한 보안 솔루션 구획(피어 간 통신 모드로 달성 가능한 수준보다 본질적으로 더 안전함) 간에 통신을 만들 수도 있습니다.

### <a name="bind-event-hubs-to-virtual-networks"></a>가상 네트워크에 이벤트 허브 바인딩

**가상 네트워크 규칙** 은 Azure Event Hubs 네임스페이스가 특정 가상 네트워크 서브넷의 연결을 수락할지 여부를 제어하는 방화벽 보안 기능입니다.

Virtual Networks에 Event Hubs를 바인딩하는 작업은 2단계 프로세스입니다. 먼저 가상 네트워크의 서브넷에서 **가상 네트워크 서비스 엔드포인트** 를 만들고 [서비스 엔드포인트 개요](../virtual-network/virtual-network-service-endpoints-overview.md) 문서에 설명된 대로 **Microsoft.EventHub** 에 사용하도록 설정해야 합니다. 서비스 엔드포인트를 추가했다면 여기에 **가상 네트워크 규칙** 을 사용하여 Event Hubs 네임스페이스를 바인딩합니다.

가상 네트워크 규칙은 가상 네트워크 서브넷을 사용하는 Event Hubs 네임스페이스의 연결입니다. 규칙이 있는 한 서브넷에 바인딩된 모든 워크로드는 Event Hubs 네임스페이스에 대한 액세스 권한이 부여됩니다. Event Hubs 자체는 아웃바운드 연결을 설정하지 않고, 액세스 권한을 가져올 필요도 없습니다. 따라서 이 규칙을 사용하여 서브넷에 대한 액세스 권한이 부여되지 않습니다.

자세한 내용은 [이벤트 허브에 대한 가상 네트워크 서비스 엔드포인트를 구성하는 방법](event-hubs-service-endpoints.md)을 참조하세요.

## <a name="private-endpoints"></a>프라이빗 엔드포인트

[Azure Private Link Service](../private-link/private-link-overview.md)를 사용하면 가상 네트워크의 **프라이빗 엔드포인트** 를 통해 Azure 서비스(예: Azure Event Hubs, Azure Storage 및 Azure Cosmos DB)와 Azure 호스팅 고객/파트너 서비스에 액세스할 수 있습니다.

프라이빗 엔드포인트는 Azure Private Link가 제공하는, 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져옵니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있으므로 게이트웨이, NAT 디바이스, ExpressRoute 또는 VPN 연결 또는 공용 IP 주소가 필요하지 않습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. Azure 리소스의 인스턴스에 연결하여 액세스 제어에서 가장 높은 수준의 세분성을 제공할 수 있습니다.

> [!IMPORTANT]
> 이 기능은 **기본** 및 **프리미엄** 계층에서 지원되지 않습니다. 


자세한 내용은 [이벤트 허브에 대한 프라이빗 엔드포인트를 구성하는 방법](private-link-service.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [이벤트 허브에 대한 IP 방화벽을 구성하는 방법](event-hubs-ip-filtering.md)
- [이벤트 허브에 대한 가상 네트워크 서비스 엔드포인트를 구성하는 방법](event-hubs-service-endpoints.md)
- [이벤트 허브에 대한 프라이빗 엔드포인트를 구성하는 방법](private-link-service.md)