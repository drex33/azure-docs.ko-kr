---
title: Azure Front Door에서 HTTP 헤더에 대한 프로토콜 지원 | Microsoft Docs
description: 이 문서에서는 Front Door가 지원하는 HTTP 헤더 프로토콜에 대해 설명합니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2021
ms.author: duau
ms.openlocfilehash: 807138187e37deef6f23121ce085e62f520ad335
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528720"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Azure Front Door의 HTTP 헤더에 대한 프로토콜 지원
이 문서에서는 Front Door가 호출 경로의 일부와 함께 지원하는 프로토콜에 대해 간략하게 설명합니다(이미지 참조). 다음 섹션에서는 Front Door가 지원하는 HTTP 헤더에 대해 자세히 설명합니다.

:::image type="content" source="./media/front-door-http-headers-protocol/front-door-protocol-summary.png" alt-text="Azure Front Door HTTP 헤더 프로토콜":::

>[!IMPORTANT]
>Front Door는 여기에 설명되지 않은 HTTP 헤더를 인증하지 않습니다.

## <a name="client-to-front-door"></a>클라이언트-Front Door
Front Door는 들어오는 요청을 수정하지 않고 대부분의 헤더를 허용합니다. 일부 예약된 헤더는 X-FD-* 접두사가 있는 헤더를 포함하여 전송되는 경우 들어오는 요청에서 제거됩니다.

## <a name="front-door-to-backend"></a>Front Door-백 엔드

Front Door에는 제한으로 인해 제거되지 않는 한 들어오는 요청에 대한 헤더가 포함되어 있습니다. Front Door는 또한 다음 헤더를 추가합니다.

| 헤더  | 예제 및 설명 |
| ------------- | ------------- |
| Via |  *Via: 1.1 Azure* </br> Front Door는 Via 헤더의 값으로 클라이언트의 HTTP 버전과 그 뒤의 *Azure* 를 추가합니다. 이 헤더는 클라이언트의 HTTP 버전을 나타내며 Front Door가 클라이언트와 백 엔드 간 요청에 대한 중간 수신자였습니다.  |
| X-Azure-ClientIP | *X-Azure-ClientIP: 127.0.0.1* </br> 처리 중인 요청과 관련된 클라이언트 IP 주소를 나타냅니다. 예를 들어 프록시에서 들어오는 요청은 원본 호출자의 IP 주소를 나타내기 위해 X-Forwarded-For 헤더를 추가할 수 있습니다. |
| X-Azure-SocketIP |  *X-Azure-SocketIP: 127.0.0.1* </br> 현재 요청이 시작된 TCP 연결과 관련된 소켓 IP 주소를 나타냅니다. 사용자가 클라이언트 IP를 임의로 덮어쓸 수 있으므로 요청의 클라이언트 IP 주소는 해당 소켓 IP 주소와 다를 수 있습니다.|
| X-Azure-Ref | *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Front Door에서 제공하는 요청을 식별하는 고유한 참조 문자열입니다. 액세스 로그를 검색하는 데 사용되며 문제 해결을 위해 중요합니다.|
| X-Azure-RequestChain | *X-Azure-RequestChain: hops=1* </br> Front Door에서 요청 루프를 검색하는 헤더이며 사용자는 이에 대한 종속성을 사용하지 않아야 합니다. |
| X-Azure-FDID | *X-Azure-FDID: 55ce4ed1-4b06-4bf1-b40e-4638452104da* <br/> 특정 Front Door 리소스에서 가져온 요청을 식별하는 참조 문자열입니다. 값은 Azure Portal에서 보거나 관리 API를 사용하여 검색할 수 있습니다. 해당 헤더를 IP ACL과 함께 사용하여 특정 Front Door 리소스의 요청만 수락하도록 엔드포인트를 잠글 수 있습니다. [자세한 내용](front-door-faq.yml#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-)은 FAQ를 참조하세요. |
| X-Forwarded-For | *X-Forwarded-For: 127.0.0.1* </br> XFF(X-Forwarded-For) HTTP 헤더 필드는 HTTP 프록시 또는 부하 분산 디바이스를 통해 웹 서버에 연결하는 클라이언트의 원래 IP 주소를 식별하는 경우가 많습니다. 기존 XFF 헤더가 있는 경우 Front Door가 클라이언트 소켓 IP를 추가하거나 클라이언트 소켓 IP를 사용하여 XFF 헤더를 추가합니다. |
| X-Forwarded-Host | *X-Forwarded-Host: contoso.azurefd.net* </br> X-Forwarded-Host HTTP 헤더 필드는 호스트 HTTP 요청 헤더에서 클라이언트가 요청한 원래 호스트를 식별하는 데 사용되는 일반적인 방법입니다. 이는 Front Door의 호스트 이름이 요청을 처리하는 백 엔드 서버와 다를 수 있기 때문입니다. 이전 값은 Front Door에 의해 재정의됩니다. |
| X-Forwarded-Proto | *X-Forwarded-Proto: http* </br> X-Forwarded-Proto HTTP 헤더 필드는 일반적으로 HTTP 요청의 원래 프로토콜을 식별하는 데 사용됩니다. 구성을 기반으로 하는 Front Door는 HTTPS를 사용하여 백 엔드와 통신할 수 있습니다. 역방향 프록시에 대한 요청이 HTTP인 경우에도 마찬가지입니다. 이전 값은 Front Door에 의해 재정의됩니다. |
| X-FD-HealthProbe | X-FD-HealthProbe HTTP 헤더 필드는 Front Door에서 상태 프로브를 식별하는 데 사용됩니다. 이 헤더가 1로 설정된 경우 요청은 상태 프로브에서 온 것입니다. X-Forwarded-Host 헤더 필드의 특정 값을 사용하여 Front Door에서 액세스를 제한하는 데 사용할 수 있습니다. |
| X-Azure-FDID | *X-Azure-FDID 헤더: 437c82cd-360a-4a54-94c3-5ff707647783* </br> 해당 필드에는 들어오는 요청을 보낸 Front Door를 식별하는 데 사용할 수 있는 frontdoorID가 포함되어 있습니다. 해당 필드는 Front Door 서비스에 의해 채워집니다. | 

## <a name="front-door-to-client"></a>Front Door-클라이언트

백 엔드에서 Front Door로 전송되는 모든 헤더는 클라이언트를 통과합니다. 다음은 Front Door에서 클라이언트로 전송되는 헤더입니다.

| 헤더  | 예제 및 설명 |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Front Door가 제공하는 요청을 식별하는 고유한 참조 문자열로 액세스 로그를 검색하는 데 사용되므로 문제 해결에 중요합니다.|
| X-Cache | *X-Cache:* 이 헤더는 요청의 캐싱 상태를 설명합니다. <br/> - *X-Cache: TCP_HIT*: 요청의 첫 번째 바이트는 Front Door 에지의 캐시 적중입니다. <br/> - *X-Cache: TCP_REMOTE_HIT*: 요청의 첫 번째 바이트는 지역 캐시(원본 보호 계층)의 캐시 적중이지만 에지 캐시에서 누락됩니다. <br/> - *X-Cache: TCP_MISS*: 요청의 첫 번째 바이트는 캐시 누락이며 콘텐츠는 원본에서 제공됩니다. <br/> - *X-Cache: PRIVATE_NOSTORE*: Cache-Control 응답 헤더가 private 또는 no-store로 설정되어 있으므로 요청을 캐시할 수 없습니다. <br/> - *X-Cache: CONFIG_NOCACHE*: 요청이 Front Door 프로필에 캐시되지 않도록 구성되어 있습니다. |

다음과 같은 선택적 응답 헤더를 사용하도록 설정하려면 "X-Azure-DebugInfo: 1" 요청 헤더를 전송해야 합니다.

| 헤더  | 예제 및 설명 |
| ------------- | ------------- |
| X-Azure-OriginStatusCode |  *X-Azure-OriginStatusCode: 503* </br> 해당 헤더는 백 엔드에서 반환한 HTTP 상태 코드를 포함합니다. 해당 헤더를 사용하여 백 엔드 로그를 거치지 않고 백 엔드에서 실행되는 애플리케이션에서 반환된 HTTP 상태 코드를 식별할 수 있습니다. 해당 상태 코드는 Front Door에서 클라이언트에 보낸 응답의 HTTP 상태 코드와 다를 수 있습니다. 해당 헤더를 사용하여 백 엔드가 오동작하는 것인지 또는 Front Door 서비스가 문제인 것인지 확인할 수 있습니다. |
| X-Azure-InternalError | 해당 헤더에는 요청을 처리할 때 Front Door에 발생할 수 있는 오류 코드가 포함됩니다. 이 오류는 Front Door 서비스/인프라의 내부적인 문제를 나타냅니다. 지원할 문제를 보고합니다.  |
| X-Azure-ExternalError | *X-Azure-ExternalError: 0x830c1011, 인증 기관에 대해 잘 모릅니다.* </br> 해당 헤더는 요청을 처리하기 위해 백 엔드 서버에서 연결을 설정하는 동안 Front Door 서버에서 발생하는 오류 코드를 보여 줍니다. 해당 헤더는 Front Door와 백 엔드 애플리케이션 간 연결 문제를 식별하는 데 도움이 됩니다. 해당 헤더에는 백 엔드에 대한 연결 문제를 식별하는 데 도움이 되는 자세한 오류 메시지(예: DNS 확인, 잘못된 인증서 등)가 포함됩니다. |

## <a name="next-steps"></a>다음 단계

- [Front Door 만들기](quickstart-create-front-door.md)
- [Front Door 작동 원리](front-door-routing-architecture.md)
