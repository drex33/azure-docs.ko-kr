---
title: 네트워크 연결 문제 해결 - Azure Event Grid | Microsoft Docs
description: 이 문서에서는 Azure Event Grid의 연결 문제 해결에 대한 정보를 제공합니다.
author: batrived
ms.topic: article
ms.date: 09/28/2021
ms.author: batrived
ms.openlocfilehash: ddfc0d177f9415807ec07c63dfedf5742c20178f
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132806385"
---
# <a name="troubleshoot-connectivity-issues---azure-event-grid"></a>연결 문제 해결 - Azure Event Grid

클라이언트 애플리케이션에서 Event Grid 토픽/도메인에 연결할 수 없는 다양한 이유가 있습니다. 발생하는 연결 문제는 영구적이거나 일시적일 수 있습니다. 문제가 항상 발생하는 경우(영구적), 조직의 방화벽 설정, IP 방화벽 설정, 서비스 태그, 프라이빗 엔드포인트 등을 확인하는 것이 좋습니다. 일시적인 문제의 경우 드롭된 패킷을 확인하는 명령을 실행하고 네트워크 추적을 가져오면 문제 해결에 도움이 될 수 있습니다.

이 문서에서는 Azure Event Grid 연결 문제 해결을 위한 팁을 제공합니다.

## <a name="troubleshoot-permanent-connectivity-issues"></a>영구 연결 문제 해결

애플리케이션에서 Event Grid에 연결할 수 없는 경우 이 섹션의 단계에 따라 문제를 해결합니다.

### <a name="check-if-theres-a-service-outage"></a>서비스 중단이 있는지 확인

[Azure 서비스 상태 사이트](https://azure.microsoft.com/status/)에서 Azure Event Grid 서비스 중단을 확인하세요.

### <a name="check-if-the-ports-required-to-communicate-with-event-grid-arent-blocked-by-organizations-firewall"></a>Event Grid와 통신하는 데 필요한 포트가 조직의 방화벽으로 차단되지 않았는지 확인합니다.

Azure Event Grid와 통신하는 데 사용되는 포트가 조직의 방화벽에서 차단되지 않는지 확인합니다. Azure Event Grid와 통신하기 위해 열어야 하는 아웃바운드 포트는 다음 표를 참조하세요.

| 프로토콜 | 포트 |
| -------- | ----- |
| HTTPS    | 443   |

다음은 443 포트가 차단되었는지 여부를 확인하는 샘플 명령입니다.

```powershell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

Linux에서:

```shell
telnet {sampletopicname}.{region}-{suffix}.eventgrid.azure.net 443
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>회사 방화벽에서 IP 주소가 허용되는지 확인합니다

Azure를 사용하는 경우 회사 방화벽 또는 프록시의 특정 IP 주소 범위 또는 URL에서 사용 중이거나 사용하려는 모든 Azure 서비스에 액세스하도록 허용해야 하는 경우가 있습니다. Event Grid에서 사용하는 IP 주소에서 트래픽이 허용되는지 확인합니다. Azure Event Grid에서 사용하는 IP 주소에 대해서는 [Azure IP 범위 및 서비스 태그 - 퍼블릭 클라우드](https://www.microsoft.com/download/details.aspx?id=56519) 및 [서비스 태그 - AzureEventGrid](network-security.md#service-tags)를 참조하세요.

[Azure IP 범위 및 서비스 태그 - 퍼블릭 클라우드](https://www.microsoft.com/download/details.aspx?id=56519) 문서에는 **지역별** IP 주소도 나열됩니다. 회사 방화벽 또는 프록시에서 **항목의 지역** 과 **쌍을 이루는 지역** 에 대한 주소 범위를 허용할 수 있습니다. 지역에 대해 쌍을 이루는 지역의 경우 [BCDR(비즈니스 연속성 및 재해 복구): Azure 쌍을 이루는 지역](../best-practices-availability-paired-regions.md)을 참조하세요. 

> [!NOTE]
> 일반적이지는 않지만, 새 IP 주소는 AzureEventGrid 서비스 태그에 추가할 수 있습니다. 따라서 서비스 태그를 매주 확인하는 것이 좋습니다.

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>네트워크 보안 그룹에서 AzureEventGrid 서비스 태그가 허용되는지 확인합니다.

애플리케이션이 서브넷 내에서 실행되고 있고 연결된 네트워크 보안 그룹이 있는 경우 인터넷 아웃바운드가 허용되는지 또는 AzureEventGrid 서비스 태그가 허용되는지 확인합니다. [서비스 태그](../virtual-network/service-tags-overview.md) 참조

### <a name="check-the-ip-firewall-settings-for-your-topicdomain"></a>토픽/도메인에 대한 IP 방화벽 설정을 확인합니다.

애플리케이션이 실행 중인 컴퓨터의 공용 IP 주소가 EventGrid 토픽/도메인 IP 방화벽에 의해 차단되지 않는지 확인합니다.

기본값으로, 요청에 유효한 인증 및 권한 부여가 제공되는 한 Event Grid 토픽/도메인은 인터넷에서 액세스할 수 있습니다. IP 방화벽을 사용하면 [CIDR(Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 표기법으로 IPv4 주소 또는 IPv4 주소 범위 세트로만 제한할 수 있습니다.

IP 방화벽 규칙은 Event Grid 토픽/도메인 수준에 적용됩니다. 따라서 해당 규칙은 지원되는 모든 프로토콜을 사용하는 클라이언트의 모든 연결에 적용됩니다. Event Grid 토픽/도메인에서 허용된 IP 규칙과 일치하지 않는 IP 주소의 연결 시도는 권한이 없는 것으로 거부됩니다. 응답은 IP 규칙을 언급하지 않습니다.

자세한 정보는 [Azure Event Grid 토픽/도메인에 대한 IP 방화벽 규칙 구성](configure-firewall.md)을 참조하세요.

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>IP 방화벽에 의해 차단된 IP 주소 찾기

Event Grid 항목/도메인 [진단 로그 사용](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-event-grid-topics-and-domains)에 대한 진단 로그를 사용합니다. 거부된 연결의 IP 주소가 표시됩니다.

```json
{
  "time": "2019-11-01T00:17:13.4389048Z",
  "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME",
  "category": "PublishFailures",
  "operationName": "Post",
  "message": "inputEventsCount=null, requestUri=https://SAMPLE-TOPIC-NAME.region-suffix.eventgrid.azure.net/api/events, publisherInfo=PublisherInfo(category=User, inputSchema=EventGridEvent, armResourceId=/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME), httpStatusCode=Forbidden, errorType=ClientIPRejected, errorMessage=Publishing to SAMPLE-TOPIC-NAME.{region}-{suffix}.EVENTGRID.AZURE.NET by client {clientIp} is rejected due to IpAddress filtering rules."
}
```

### <a name="check-if-the-eventgrid-topicdomain-can-be-accessed-using-only-a-private-endpoint"></a>프라이빗 엔드포인트만 사용하여 EventGrid 토픽/도메인에 액세스할 수 있는지 확인합니다.

Event Grid 토픽/도메인이 프라이빗 엔드포인트를 통해서만 액세스할 수 있도록 구성된 경우 클라이언트 애플리케이션이 프라이빗 엔드포인트를 통해 토픽/도메인에 액세스하는지 확인합니다. 이를 확인하려면 클라이언트 애플리케이션이 서브넷 내에서 실행되고 있고 해당 서브넷에 Event Grid 토픽/도메인에 대한 프라이빗 엔드포인트가 있는지 확인합니다.

[Azure Private Link 서비스](../private-link/private-link-overview.md)를 사용하면 가상 네트워크의 **프라이빗 엔드포인트** 를 통해 Azure Event Grid에 액세스할 수 있습니다. 프라이빗 엔드포인트는 Azure Private Link가 제공하는, 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져옵니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있으므로 게이트웨이, NAT 디바이스, ExpressRoute 또는 VPN 연결 또는 공용 IP 주소가 필요하지 않습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. Azure 리소스의 인스턴스에 연결하여 액세스 제어에서 가장 높은 수준의 세분성을 제공할 수 있습니다.

자세한 정보는 [프라이빗 엔드포인트 구성](configure-private-endpoints.md)을 참조하세요.

## <a name="troubleshoot-transient-connectivity-issues"></a>일시적인 연결 문제 해결

일시적인 연결 문제가 발생하는 경우 문제 해결 팁에 대한 다음 섹션을 참조하세요.

### <a name="run-the-command-to-check-dropped-packets"></a>명령을 실행하여 드롭된 패킷을 확인합니다

일시적인 연결 문제가 있는 경우 다음 명령을 실행하여 드롭된 패킷이 있는지 확인합니다. 이 명령은 1초마다 서비스와 25개의 서로 다른 TCP 연결을 구축하려고 시도합니다. 그 후 성공/실패 횟수를 확인하고 TCP 연결 대기 시간을 확인할 수도 있습니다. `psping` 도구를 [여기](/sysinternals/downloads/psping)에서 다운로드할 수 있습니다.

```shell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

`tcpping` [tcpping.exe](https://www.elifulkerson.com/projects/tcping.php)와 같은 기타 도구를 사용하는 경우 동일한 명령을 사용할 수 있습니다.

이전 단계가 도움이 되지 않는 경우 네트워크 추적을 가져와서 [Wireshark](https://www.wireshark.org/)와 같은 도구를 사용하여 분석합니다. 필요한 경우 [Microsoft 지원](https://support.microsoft.com/)에 문의하세요.

### <a name="service-upgradesrestarts"></a>서비스 업그레이드/다시 시작

백엔드 서비스 업그레이드 및 다시 시작으로 인해 일시적인 연결 문제가 발생할 수 있습니다. 문제가 발생하면 다음과 같은 증상이 나타날 수 있습니다.

- 들어오는 메시지/요청이 끊길 수 있습니다.
- 로그 파일에 오류 메시지가 포함될 수 있습니다.
- 몇 초 동안 서비스에서 애플리케이션의 연결이 끊어질 수 있습니다.
- 요청이 일시적으로 제한될 수 있습니다.

이러한 일시적인 오류를 포착하고 백오프한 다음 호출을 다시 시도하면 사용자의 코드가 이러한 일시적인 문제에 복원력이 있음을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

도움이 더 필요한 경우 [Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-eventgrid)에 문제를 게시하거나 [지원 티켓](https://azure.microsoft.com/support/options/)을 여세요.