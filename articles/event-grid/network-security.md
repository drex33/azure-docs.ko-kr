---
title: Azure Event Grid 리소스에 대한 네트워크 보안
description: 이 문서에서는 송신용 서비스 태그, 수신용 IP 방화벽 규칙 및 Azure Event Grid를 통한 수신용 프라이빗 엔드포인트를 사용하는 방법을 설명합니다.
author: VidyaKukke
ms.topic: conceptual
ms.date: 09/28/2021
ms.author: vkukke
ms.openlocfilehash: 041c09a23b25b2ffe487b6d13171c51f2d96e6e1
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212763"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Azure Event Grid 리소스에 대한 네트워크 보안
이 문서에서는 Azure Event Grid에서 다음 보안 기능을 사용하는 방법을 설명합니다. 

- 송신에 대한 서비스 태그
- 수신용 IP 방화벽 규칙
- 수신용 프라이빗 엔드포인트


## <a name="service-tags"></a>서비스 태그
서비스 태그는 지정된 Azure 서비스의 IP 주소 접두사 그룹을 나타냅니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트하여 네트워크 보안 규칙을 자주 업데이트할 때 발생하는 복잡성을 최소화합니다. 서비스 태그에 대한 자세한 내용은 [서비스 태그 개요](../virtual-network/service-tags-overview.md)를 참조하세요.

서비스 태그를 사용하여 [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md#security-rules) 또는 [Azure Firewall](../firewall/service-tags.md)에서 네트워크 액세스 제어를 정의할 수 있습니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용합니다. 서비스 태그 이름(예: **AzureEventGrid**)을 규칙의 적절한 *원본* 또는 *대상* 필드에 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다.

| 서비스 태그 | 목적 | 인바운드 또는 아웃바운드를 사용할 수 있나요? | 지역 범위를 지원할 수 있나요? | Azure Firewall에서 사용할 수 있나요? |
| --- | -------- |:---:|:---:|:---:|
| AzureEventGrid | Azure Event Grid. | 모두 | 예 | 예 |


## <a name="ip-firewall"></a>IP 방화벽 
Azure Event Grid는 항목 및 도메인에 게시하기 위한 IP 기반 액세스 제어를 지원합니다. IP 기반 제어를 사용하면 항목 또는 도메인에 대한 게시자를 승인된 컴퓨터 및 클라우드 서비스 집합으로만 제한할 수 있습니다. 이 기능은 Event Grid에서 지원하는 [인증 메커니즘](security-authentication.md)을 보완합니다.

기본적으로 요청에 유효한 인증 및 권한 부여가 제공되는 한 항목 및 도메인은 인터넷에서 액세스할 수 있습니다. IP 방화벽을 사용하면 [CIDR(Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 표기법으로 IP 주소 또는 IP 주소 범위 세트로만 제한할 수 있습니다. 다른 IP 주소에서 시작된 게시자는 거부되며 403(금지됨) 응답을 받게 됩니다.

항목 및 도메인에 대한 IP 방화벽을 구성하는 단계별 지침은 [IP 방화벽 구성](configure-firewall.md)을 참조하세요.

## <a name="private-endpoints"></a>프라이빗 엔드포인트
[프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 사용하여 퍼블릭 인터넷을 통해 이동하지 않고도 [프라이빗 링크](../private-link/private-link-overview.md)를 통해 안전하게 가상 네트워크에서 토픽 및 도메인으로 직접 이벤트를 수신할 수 있습니다. 프라이빗 엔드포인트는 VNet의 Azure 서비스에 대한 특수한 네트워크 인터페이스입니다. 항목 또는 도메인에 대한 프라이빗 엔드포인트를 생성하면 VNet의 클라이언트와 Event Grid 리소스 간에 보안 연결이 제공됩니다. 프라이빗 엔드포인트에는 VNet의 IP 주소 범위 내에서 IP 주소가 할당됩니다. 프라이빗 엔드포인트와 Event Grid 서비스 간의 연결은 보안 프라이빗 링크를 사용합니다.

![아키텍처 다이어그램](./media/network-security/architecture-diagram.png)

Event Grid 리소스에 대해 프라이빗 엔드포인트를 사용하면 다음 작업을 수행할 수 있습니다.

- 공용 인터넷이 아닌 Microsoft 백본 네트워크를 통해 VNet에서 항목 또는 도메인에 안전하게 액세스할 수 있습니다.
- 개인 피어링을 통해 VPN 또는 Express Routes를 사용하여 VNet에 연결하는 온-프레미스 네트워크에서 안전하게 연결합니다.

VNet에서 항목 또는 도메인에 대한 프라이빗 엔드포인트를 만들면 리소스 소유자에게 승인을 위해 동의 요청이 전송됩니다. 프라이빗 엔드포인트 만들기를 요청한 사용자가 리소스의 소유자인 경우 이 동의 요청이 자동으로 승인됩니다. 그렇지 않으면 승인될 때까지 연결이 **보류 중** 상태가 됩니다. VNet의 애플리케이션은 다른 방법으로 사용하는 것과 동일한 연결 문자열 및 권한 부여 메커니즘을 사용하여 프라이빗 엔드포인트를 통해 Event Grid 서비스에 원활하게 연결할 수 있습니다. 리소스 소유자는 Azure Portal의 리소스에 대한 **프라이빗 엔드포인트** 탭을 통해 동의 요청 및 프라이빗 엔드포인트를 관리할 수 있습니다.

### <a name="connect-to-private-endpoints"></a>프라이빗 엔드포인트에 연결
프라이빗 엔드포인트를 사용하는 VNet의 게시자는 퍼블릭 엔드포인트에 연결하는 클라이언트와 동일한 항목 또는 도메인의 연결 문자열을 사용해야 합니다. DNS 확인은 개인 링크를 통해 VNet에서 항목 또는 도메인으로의 연결을 자동으로 라우팅합니다. 기본적으로 Event Grid는 프라이빗 엔드포인트에 대한 필수 업데이트를 사용하여 VNet에 연결된 [프라이빗 DNS 영역](../dns/private-dns-overview.md)을 만듭니다. 그러나 사용자 고유의 DNS 서버를 사용하는 경우 DNS 구성을 추가로 변경해야 할 수 있습니다.

### <a name="dns-changes-for-private-endpoints"></a>프라이빗 엔드포인트용 DNS 변경
프라이빗 엔드포인트를 만들 때 리소스에 대한 DNS CNAME 레코드는 `privatelink` 접두사가 있는 하위 도메인의 별칭으로 업데이트됩니다. 기본적으로 개인 링크의 하위 도메인에 해당하는 개인 DNS 영역이 생성됩니다. 

프라이빗 엔드포인트를 사용하여 VNet 외부에서 항목 또는 도메인 엔드포인트 URL을 확인하면 서비스의 퍼블릭 엔드포인트로 확인됩니다. 프라이빗 엔드포인트를 호스트하는 **VNet 외부** 에서 확인되면'topicA'에 대한 DNS 리소스 레코드는 다음과 같습니다.

| 이름                                          | 유형      | 값                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<Azure traffic manager profile\>

[IP 방화벽](#ip-firewall)을 사용하여 퍼블릭 엔드포인트를 통해 VNet 외부의 클라이언트에 대한 액세스를 거부하거나 제어할 수 있습니다. 

프라이빗 엔드포인트를 호스트하는 VNet에서 확인되면 항목 또는 도메인 엔드포인트 URL은 프라이빗 엔드포인트의 IP 주소로 확인됩니다. 'topicA' 항목에 대한 DNS 리소스 레코드는 프라이빗 엔드포인트를 호스트하는 **VNet 내부** 에서 확인되는 경우 다음과 같습니다.

| 이름                                          | 유형      | 값                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

이 방법을 사용하면 프라이빗 엔드포인트를 호스트하는 VNet의 클라이언트와 VNet 외부의 클라이언트에 동일한 연결 문자열을 사용하여 항목 또는 도메인에 액세스할 수 있습니다.

네트워크에서 사용자 지정 DNS 서버를 사용하는 경우 클라이언트는 항목 또는 도메인 엔드포인트의 FQDN을 프라이빗 엔드포인트 IP 주소로 확인할 수 있습니다. 프라이빗 링크 하위 도메인을 VNet의 프라이빗 DNS 영역에 위임하도록 DNS 서버를 구성하거나 프라이빗 엔드포인트 IP 주소를 사용하여 `topicOrDomainName.regionName.privatelink.eventgrid.azure.net`에 대한 A 레코드를 구성합니다.

권장 DNS 영역 이름은 `privatelink.eventgrid.azure.net`입니다.

### <a name="private-endpoints-and-publishing"></a>프라이빗 엔드포인트 및 게시

다음 표에서는 프라이빗 엔드포인트 연결의 다양한 상태와 게시에 미치는 영향을 설명합니다.

| 연결 상태   |  게시함(예/아니요) |
| ------------------ | -------------------------------|
| 승인됨           | 예                            |
| 거부됨           | 예                             |
| Pending            | 예                             |
| 연결 끊김       | 예                             |

게시에 성공하려면 프라이빗 엔드포인트 연결 상태가 **승인됨** 이어야 합니다. 연결이 거부되면 Azure Portal을 사용하여 승인할 수 없습니다. 유일한 방법은 연결을 삭제하고 새 연결을 만드는 것입니다.

## <a name="pricing-and-quotas"></a>가격 책정 및 할당량
**프라이빗 엔드포인트** 는 Event Grid의 기본 및 프리미엄 계층에서 모두 사용할 수 있습니다. Event Grid는 항목 또는 도메인당 최대 64개의 프라이빗 엔드포인트 연결을 만들 수 있습니다. 

**IP 방화벽** 기능은 Event Grid의 기본 및 프리미엄 계층에서 모두 사용할 수 있습니다. 항목 또는 도메인당 최대 16개의 IP 방화벽 규칙을 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계
Event Grid 리소스에 대한 IP 방화벽을 구성하여 IP 주소 또는 IP 주소 범위의 선택 세트에서만 공용 인터넷을 통해 액세스를 제한할 수 있습니다. 단계별 지침은 [IP 방화벽 구성](configure-firewall.md)을 참조하세요.

선택한 가상 네트워크에서만 액세스를 제한하도록 프라이빗 엔드포인트를 구성할 수 있습니다. 단계별 지침에 대해서는 [프라이빗 엔드포인트 구성](configure-private-endpoints.md)을 참조하세요.

네트워크 연결 문제를 해결하려면 [네트워크 연결 문제 해결](troubleshoot-network-connectivity.md)을 참조하세요.
