---
title: Azure Front Door 표준/프리미엄(미리 보기) 로깅
description: 이 문서에서는 Azure Front Door 표준/프리미엄에서 로깅이 작동하는 방식을 설명합니다.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 08/26/2021
ms.author: duau
ms.openlocfilehash: 5446c240d1d07b5b99fe6f91a031617756a6018c
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967594"
---
# <a name="azure-front-door-standardpremium-preview-logging"></a>Azure Front Door 표준/프리미엄(미리 보기) 로깅

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

Azure Front Door는 Front Door를 추적, 모니터링 및 디버그하는 데 도움이 되는 다양한 로깅을 제공합니다. 

* 액세스 로그에는 AFD가 수신하는 모든 요청에 대한 자세한 정보가 포함되어 있으며 사용자가 액세스 패턴을 분석 및 모니터링하고 문제를 디버그하는 데 도움이 됩니다. 
* 활동 로그는 Azure 리소스에서 수행된 작업에 대한 가시성을 제공합니다.  
* 상태 프로브 로그는 원본에 대해 실패한 모든 프로브에 대한 로그를 제공합니다. 
* WAF(Web Application Firewall) 로그는 Azure Front Door 엔드포인트의 검색 또는 방지 모드를 통해 기록되는 요청에 대한 자세한 정보를 제공합니다. WAF로 구성된 사용자 지정 도메인은 이러한 로그를 통해 볼 수도 있습니다.

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

액세스 로그, 상태 프로브 로그 및 WAF 로그는 기본적으로 사용할 수 없습니다. 로깅을 사용하도록 설정하려면 아래 단계를 수행합니다. 활동 로그 항목은 기본적으로 수집되고 Azure Portal에서 볼 수 있습니다. 로그는 최대 몇 분까지 지연될 수 있습니다. 

로그 저장에는 세 가지 옵션이 있습니다. 

* **스토리지 계정** - 스토리지 계정은 로그를 장기간 저장하고 필요 시 검토하는 시나리오에 가장 적합합니다. 
* **Event Hubs**: Event Hubs는 다른 SIEM(보안 정보 및 이벤트 관리) 도구 또는 외부 데이터 저장소와 통합하는 데 유용한 옵션입니다. 예: Splunk/DataDog/Sumo 
* **Azure Log Analytics:** Azure Monitor의 Azure Log Analytics는 Azure Front Door 성능에 대한 일반적인 실시간 모니터링 및 분석에 가장 적합합니다.

## <a name="configure-logs"></a>메트릭 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Front Door 표준/프리미엄을 검색하고 Azure Front Door 프로필을 선택합니다.

1. 프로필에서 **모니터링** 으로 이동하고 **진단 설정** 을 선택합니다. **진단 설정 추가** 를 선택합니다.

   :::image type="content" source="../media/how-to-logging/front-door-logging-1.png" alt-text="진단 설정 방문 페이지의 스크린샷입니다.":::

1. **진단 설정** 의  **진단 설정 이름** 에 이름을 입력합니다.

1. **FrontDoorAccessLog**, **FrontDoorHealthProbeLog** 및 **FrontDoorWebApplicationFirewallLog** 에서  **로그** 를 선택합니다.

1.  **대상 세부 정보** 를 선택합니다. 대상 옵션은 다음과 같습니다. 

    * **Log Analytics에 보내기**
        * *구독* 및 *Log Analytics 작업 영역* 을 선택합니다.
    * **스토리지 계정에 보관**
        * *구독* 및 *스토리지 계정* 을 선택합니다. **보존 기간(일)** 을 설정합니다.
    * **이벤트 허브로 스트림**
        *  *구독, Event Hub 네임스페이스, Event Hub 이름(선택 사항)* 및  *Event Hub 정책 이름* 을 선택합니다. 

     :::image type="content" source="../media/how-to-logging/front-door-logging-2.png" alt-text="진단 설정 페이지의 스크린샷입니다.":::

1. **Save** 를 클릭합니다.

## <a name="access-log"></a>액세스 로그

Azure Front Door는 현재 다음과 같은 스키마가 있고 아래와 같이 JSON 형식으로 기록된 각 항목에 개별 API 요청을 제공합니다. 

| 속성 | Description |
|----------|-------------| 
| TrackingReference | AFD에서 제공하는 요청을 식별하는 고유한 참조 문자열로, 클라이언트에 X-Azure-Ref 헤더로 전송됩니다. 특정 요청의 액세스 로그에서 세부 정보를 검색하는 데 필요합니다. |
| 시간 | AFD 에지에서 요청된 콘텐츠를 클라이언트에 전달하는 날짜 및 시간(UTC)입니다. |
| HttpMethod | 요청에서 사용하는 HTTP 메서드이며 DELETE, GET, HEAD, OPTIONS, PATCH, POST 또는 PUT입니다. |
| HttpVersion | 뷰어에서 요청에 지정된 HTTP 버전입니다. |
| RequestUri | 받은 요청의 URI 이 필드는 전체 스키마, 포트, 도메인, 경로 및 쿼리 문자열입니다. |
| HostName | 클라이언트의 요청에 있는 호스트 이름입니다. 사용자 지정 도메인을 사용하도록 설정하고 와일드 카드 도메인(*. contoso.com)을 사용하는 경우 호스트 이름은 a.contoso.com입니다. Azure Front Door 도메인(contoso.azurefd.net)을 사용하는 경우 호스트 이름은 contoso.azurefd.net입니다. |
| RequestBytes | 요청 헤더 및 요청 본문을 포함하여 HTTP 요청 메시지의 크기(바이트)입니다. 보기 권한자가 헤더를 포함하여 요청에 포함한 데이터의 바이트 수입니다. |
| ResponseBytes | 백 엔드 서버에서 응답으로 보낸 바이트 수입니다. |
| UserAgent | 클라이언트에서 사용한 브라우저 유형입니다. |
| ClientIp | 원본에서 요청한 클라이언트의 IP 주소입니다. 요청에 X-Forwarded-For 헤더가 있는 경우 클라이언트 IP는 동일한 곳에서 선택됩니다. |
| SocketIp | AFD 에지에 대한 직접 연결의 IP 주소입니다. 클라이언트에서 HTTP 프록시 또는 부하 분산 장치를 사용하여 요청을 보낸 경우 SocketIp의 값은 프록시 또는 부하 분산 장치의 IP 주소입니다. |
| timeTaken | AFD 에지 서버가 클라이언트의 요청을 수신한 시간부터 AFD가 클라이언트에 마지막 응답 바이트를 보내는 시간까지의 시간(밀리초)입니다. 이 필드는 네트워크 대기 시간 및 TCP 버퍼링을 고려하지 않습니다. |
| RequestProtocol | 클라이언트가 요청에 지정한 프로토콜인 HTTP, HTTPS입니다 |
| SecurityProtocol | 요청에 사용되는 TLS/SSL 프로토콜 버전이거나, 암호화가 없는 경우 null입니다. 가능한 값은 SSLv3, TLSv1, TLSv1.1, TLSv1.2입니다. |
| SecurityCipher | 요청 프로토콜의 값이 HTTPS인 경우 이 필드는 클라이언트에서 협상한 TLS/SSL 암호화와 암호화를 위해 AFD를 나타냅니다. |
| 엔드포인트 | AFD 엔드포인트의 도메인 이름(예: contoso.z01.azurefd.net)입니다. |
| HttpStatusCode | AFD에서 반환된 HTTP 상태 코드입니다. |
| Pop | 사용자 요청에 응답한 에지 POP입니다.  |
| 캐시 상태 | 캐싱이 제공될 때 CDN 서비스에서 요청을 처리 하는 방법의 상태 코드를 제공합니다. 가능한 값은 HIT: HTTP 요청이 AFD 에지 POP 캐시에서 제공되었습니다. <br> **MISS**: HTTP 요청이 원본에서 제공되었습니다. <br/> **PARTIAL_HIT**: 요청의 일부 바이트는 AFD 에지 POP 캐시에서 제공되는 반면 일부 바이트는 개체 청크 시나리오의 원본에서 제공되었습니다. <br> **CACHE_NOCONFIG**: 바이패스 시나리오를 포함하여 캐싱을 설정하지 않고 요청을 전달합니다. <br/> **PRIVATE_NOSTORE**: 고객의 캐싱 설정에서 캐시가 구성되지 않았습니다. <br> **REMOTE_HIT**: 부모 노드 캐시에서 요청이 처리되었습니다. <br/> **N/A**: **서명 된 URL 및 규칙 집합에 의해 거부된 요청입니다. |
| MatchedRulesSetName | 처리된 규칙의 이름입니다. |
| RouteName | 요청에 일치하는 경로의 이름입니다. |
| ClientPort | 요청한 클라이언트의 IP 포트입니다. |
| Referrer | 요청을 시작한 사이트의 URL입니다. |
| TimetoFirstByte | AFD의 시간(밀리초)은 Azure Front Door에서 측정된 대로 첫 번째 바이트가 클라이언트로 전송되는 시간에 대한 요청을 받습니다. 이 속성은 클라이언트 데이터를 측정하지 않습니다. |
| 오류 정보 | 이 필드는 각 응답의 오류 토큰에 대한 자세한 정보를 제공합니다. <br> **Noerror**: 발견된 오류가 없음을 나타냅니다. <br> **CertificateError**: 제네릭 SSL 인증서 오류입니다. <br> **CertificateNameCheckFailed**: SSL 인증서의 호스트 이름이 잘못되었거나 일치하지 않습니다. <br> **Clientdisconnected**: 클라이언트 네트워크 연결 때문에 요청이 실패했습니다. <br> **Clientgeoblocked**: 클라이언트가 IP의 지리적 위치에 따라 차단되었습니다. <br> **UnspecifiedClientError**: 제네릭 클라이언트 오류입니다. <br> **InvalidRequest**: 요청이 잘못되었습니다. 이 오류는 잘못된 형식의 헤더, 본문 및 URL 때문에 발생할 수 있습니다. <br> **Dnsfailure**: DNS 오류가 발생했습니다. <br> **DNSTimeout**: 백 엔드를 확인하는 DNS 쿼리 시간이 초과되었습니다. <br> **DNSNameNotResolved**: 서버 이름이나 주소를 확인할 수 없습니다. <br> **OriginConnectionAborted**: 원본과의 연결이 비정상적으로 끊어졌습니다. <br> **OriginConnectionError**: 제네릭 원본 연결 오류입니다. <br> **OriginConnectionRefused**: 원본과의 연결이 설정되지 않았습니다. <br> **OriginError**: 제네릭 원본 오류입니다. <br> **OriginInvalidRequest**: 잘못된 요청을 원본으로 보냈습니다. <br> **Responseheadertoobig**: 원본에서 너무 많은 응답 헤더를 반환했습니다. <br> **OriginInvalidResponse**: **원본이 잘못되었거나 인식할 수 없는 응답을 반환했습니다. <br> **OriginTimeout**: 원본 요청에 대한 시간 제한이 만료되었습니다. <br> **Responseheadertoobig**: 원본에서 너무 많은 응답 헤더를 반환했습니다. <br> **RestrictedIP**: 제한된 IP로 인해 요청이 차단되었습니다. <br> **SSLHandshakeError**: SSL 핸드셰이크 오류로 인해 원본과의 연결을 설정할 수 없습니다. <br> **SSLInvalidRootCA**: RootCA가 잘못되었습니다. <br> **Sslininvalid cipher**: HTTPS 연결이 설정된 암호화가 잘못되었습니다. <br> **OriginConnectionAborted**: 원본과의 연결이 비정상적으로 끊어졌습니다. <br> **OriginConnectionRefused**: 원본과의 연결이 설정되지 않았습니다. <br> **UnspecifiedError**: 테이블의 오류에 맞지 않는 오류가 발생했습니다. |
| OriginURL | 요청이 전송되는 원본의 전체 URL입니다. 구성표, 호스트 헤더, 포트, 경로 및 쿼리 문자열로 구성됩니다. <br> **URL 다시 작성**: 규칙 집합에 URL 다시 작성 규칙이 있는 경우 경로가 다시 작성된 경로를 참조합니다. <br> **에지 POP의 캐시** 에지 POP에서 캐시가 적중하는 경우 원본은 N/A입니다. <br> **대량 요청** 요청된 콘텐츠가 원본으로 돌아가는 청크 요청이 여러 개 있는 경우 이 필드는 원본에 대한 첫 번째 요청에 해당합니다. 자세한 내용은 개체 청크를 참조하세요. |
| OriginIP | 요청을 처리한 원본 IP입니다. <br> **에지 POP의 캐시** 에지 POP에서 캐시가 적중하는 경우 원본은 N/A입니다. <br> **대량 요청** 요청된 콘텐츠가 원본으로 돌아가는 청크 요청이 여러 개 있는 경우 이 필드는 원본에 대한 첫 번째 요청에 해당합니다. 자세한 내용은 개체 청크를 참조하세요. |
| OriginName| 원본에 대한 전체 DNS 이름(원본 URL의 호스트 이름)입니다. <br> **에지 POP의 캐시** 에지 POP에서 캐시가 적중하는 경우 원본은 N/A입니다. <br> **대량 요청** 요청된 콘텐츠가 원본으로 돌아가는 청크 요청이 여러 개 있는 경우 이 필드는 원본에 대한 첫 번째 요청에 해당합니다. 자세한 내용은 개체 청크를 참조하세요. |

## <a name="health-probe-log"></a>상태 프로브 로그

상태 프로브 로그는 실패한 모든 프로브를 로깅하여 원본을 진단하는 데 도움을 줍니다.로그는 원본을 다시 가져오는 데 사용할 수 있는 정보를 제공합니다. 이 로그가 유용할 수 있는 몇 가지 시나리오는 다음과 같습니다.

* Azure Front Door 트래픽이 일부 원본에 전송되었음을 발견했습니다. 예를 들어 트래픽을 수신하는 네 가지 원본 중 3개만 있습니다. 원본에서 프로브를 수신하고 있는지와 실패 이유가 아닌지 확인하려고 합니다.  

* 원본 상태(%)가 예상보다 낮다는 것을 발견했으며 실패한 원본 및 실패 이유를 확인하려고 합니다.

### <a name="health-probe-log-properties"></a>상태 프로브 로그 속성

각 상태 프로브 로그에는 다음 스키마가 있습니다.

| 속성 | Description |
| --- | --- |
| HealthProbeId  | 요청을 식별하는 고유 ID입니다. |
| 시간 | 프로브 완료 시간 |
| HttpMethod | 상태 프로브 요청에 사용된 HTTP 메서드입니다. 값에는 상태 프로브 구성에 따라 GET 및 HEAD가 포함됩니다. |
| 결과 | 원본에 대한 상태 프로브의 상태, 값 포함 성공 및 기타 오류 텍스트입니다. |
| HttpStatusCode  | 원본에서 반환된 HTTP 상태 코드입니다. |
| ProbeURL(대상) | 요청이 전송되는 원본의 전체 URL입니다. 구성표, 호스트 헤더, 경로 및 쿼리 문자열로 구성됩니다. |
| OriginName  | 요청이 전송되는 원본입니다. 원본이 FDQN으로 구성된 경우 이 필드는 관심 원본을 찾는 데 도움이 됩니다. |
| POP | 프로브 요청을 보낸 에지 POP입니다. |
| 원본 IP | 대상 원본 IP입니다. 이 필드는 FQDN을 사용하여 원본을 구성하는 경우 관심 원본을 찾는 데 유용합니다. |
| TotolaLatency | AFDX 에지에서 요청을 원본으로 보내는 시간부터 원점에서 마지막 응답을 AFDX 에지로 보내는 시간까지입니다. |
| ConnectionLatency| HTTP 프로브 요청을 원본으로 보내기 위해 TCP 연결을 설정하는 데 소요된 시간입니다. | 
| DNSResolution 대기 시간 | 원본이 IP 대신 FDQN으로 구성된 경우 DNS 확인에 소요된 시간입니다. 원본이 IP로 구성된 경우 해당 없음 |

### <a name="health-probe-log-sample-in-json"></a>JSON의 상태 프로브 로그 샘플

`{ "records": [ { "time": "2021-02-02T07:15:37.3640748Z",
      "resourceId": "/SUBSCRIPTIONS/27CAFCA8-B9A4-4264-B399-45D0C9CCA1AB/RESOURCEGROUPS/AFDXPRIVATEPREVIEW/PROVIDERS/MICROSOFT.CDN/PROFILES/AFDXPRIVATEPREVIEW-JESSIE",
      "category": "FrontDoorHealthProbeLog",
      "operationName": "Microsoft.Cdn/Profiles/FrontDoorHealthProbeLog/Write",
      "properties": { "healthProbeId": "9642AEA07BA64675A0A7AD214ACF746E",
        "POP": "MAA",
        "httpVerb": "HEAD",
        "result": "OriginError",
        "httpStatusCode": "400",
        "probeURL": "http://afdxprivatepreview.blob.core.windows.net:80/",
        "originName": "afdxprivatepreview.blob.core.windows.net",
        "originIP": "52.239.224.228:80",
        "totalLatencyMilliseconds": "141",
        "connectionLatencyMilliseconds": "68",
        "DNSLatencyMicroseconds": "1814" } } ]
} `

## <a name="activity-logs"></a>활동 로그

활동 로그는 Azure Front Door 표준/프리미엄에서 수행된 작업에 대한 정보를 제공합니다. 로그에는 Azure Front Door에서 쓰기 작업이 수행된 항목, 사용자, 시기에 대한 세부 정보가 포함됩니다. 

> [!NOTE]
> 활동 로그에는 GET 작업이 포함되지 않습니다. 또한 Azure Portal 또는 원래 관리 API를 사용하여 수행하는 작업은 포함되지 않습니다. 

Front Door의 활동 로그 또는 Azure Monitor에서 Azure 리소스의 모든 로그에 액세스합니다.

활동 로그를 보려면

1. Front Door 프로필을 선택합니다.

1. **활동 로그** 를 선택합니다.

1. 필터링 범위를 선택한 다음 **적용** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Front Door 표준/프리미엄(미리 보기) 보고서](how-to-reports.md)를 알아봅니다.
- [Azure Front Door 표준/프리미엄(미리 보기) 실시간 모니터링 메트릭](how-to-monitor-metrics.md)을 알아봅니다.
