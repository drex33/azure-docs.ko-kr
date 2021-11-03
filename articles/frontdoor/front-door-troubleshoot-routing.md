---
title: Azure Front Door 구성 문제 해결
description: 이 자습서에서는 Azure Front Door 인스턴스에 발생할 수 있는 일반적인 문제 중 일부를 해결하는 방법에 대해 알아봅니다.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2021
ms.author: duau
ms.openlocfilehash: 3bae55a4a5b2a2b6ec5ae8ce7c63fb52b96fbd9f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464189"
---
# <a name="troubleshooting-common-routing-problems"></a>일반적인 라우팅 문제 해결

이 문서에서는 Azure Front Door 구성에서 발생할 수 있는 몇 가지 일반적인 라우팅 문제를 해결하는 방법을 설명합니다.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>몇 초 후에 Azure Front Door에서 503 응답

### <a name="symptom"></a>증상

* Azure Front Door를 거치지 않고 백 엔드로 전송되는 일반 요청이 성공합니다. Azure Front Door를 통해 전송하면 503 오류 응답이 발생합니다.
* Azure Front Door의 실패 알림은 일반적으로 약 30초 후에 표시됩니다.
* 로그가 인 간헐적인 503 `ErrorInfo: OriginInvalidResponse` 오류입니다.

### <a name="cause"></a>원인

이 문제의 원인은 다음 세 가지 중 하나일 수 있습니다.
 
* 백 엔드가 Azure Front Door에서 요청을 받기 위해 구성된 시간 제한(기본값은 30초)보다 오래 걸립니다.
* Azure Front Door에서 요청에 대한 응답을 보내는 데 걸리는 시간이 시간 제한 값보다 오래 걸립니다.
* 클라이언트가 (압축 사용)를 사용하여 바이트 범위 요청을 `Accept-Encoding header` 보냈습니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계

* Azure Front Door를 거치지 않고 백 엔드에 직접 요청을 보냅니다. 백 엔드가 일반적으로 응답하는 데 걸리는 시간을 확인합니다.
* Azure Front Door를 통해 요청을 보내고 503 응답이 표시되는지 확인합니다. 그렇지 않은 경우에는 시간 제한 문제가 아닐 수 있습니다. 지원에 문의
* 요청이 Azure Front Door 503 오류 응답 코드가 발생하는 경우 Azure Front Door 대한 **보내기/받기 시간 제한(초)**  설정을 구성합니다. 기본 시간 제한은 최대 4분(240초)으로 연장할 수 있습니다. Front Door *디자이너로* 가서 설정 선택하여 설정을 구성할 수 **있습니다.**

    :::image type="content" source=".\media\troubleshoot-route-issues\send-receive-timeout.png" alt-text="Front Door 디자이너의 전송/수신 시간 제한 필드 스크린샷.":::

* 시간 제한으로 문제가 해결되지 않으면 Fiddler 또는 브라우저의 개발자 도구와 같은 도구를 사용하여 클라이언트가 Accept-Encoding 헤더를 사용하여 바이트 범위 요청을 보내는지 확인하여 원본이 다른 콘텐츠 길이로 응답하도록 합니다. 그렇다면 원본/Azure Front Door 압축을 사용하지 않도록 설정하거나 바이트 범위 요청에 대한 요청에서 제거할 규칙 집합 규칙을 만들 수 `accept-encoding` 있습니다.

    :::image type="content" source=".\media\troubleshoot-route-issues\remove-encoding-rule.png" alt-text="규칙 엔진의 수락 인코딩 규칙 스크린샷.":::

## <a name="https-traffic-to-backend-fails"></a>백 엔드에 대한 HTTPS 트래픽 실패

### <a name="symptom"></a>증상

백 엔드 리소스에 대한 HTTPS 트래픽이 실패합니다.

### <a name="cause"></a>원인

* 주체 이름을 가진 인증서가 TLS 핸드셰이크 중에 백 엔드 호스트 이름과 일치하지 않습니다.
* 백 엔드 호스팅 인증서가 유효한 CA에서 온 것이 아닙니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계

* 규정 준수 관점에서는 권장되지 않지만 Front Door 대한 인증서 주체 이름 확인을 사용하지 않도록 하여 이 오류를 해결할 수 있습니다. 해당 설정은 Azure Portal의 설정 및 API의 BackendPoolsSettings 아래에 있습니다.
* 유효한 인증 [기관의](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT) 인증서만 백 엔드에서 Front Door 사용할 수 있습니다. 내부 CA 또는 자체 서명된 인증서의 인증서는 허용되지 않습니다. 인증서에는 리프 및 중간 인증서가 있는 완전한 인증서 체인이 있어야 하며 루트 CA는 [Microsoft 신뢰할 수 있는 CA 목록](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT)의 일부여야 합니다.

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>사용자 지정 도메인에 전송된 요청이 400 상태 코드를 반환합니다.

### <a name="symptom"></a>증상

* Azure Front Door 인스턴스를 만들었지만 도메인 또는 프런트 엔드 호스트에 대한 요청이 HTTP 400 상태 코드를 반환하고 있습니다.
* 구성한 사용자 지정 도메인에 프런트 엔드 호스트에 대한 DNS 매핑을 만들었습니다. 그러나 사용자 지정 도메인 호스트 이름에 요청을 보내면 HTTP 400 상태 코드가 반환됩니다. 구성된 백 엔드로 라우팅하는 것은 아닙니다.

### <a name="cause"></a>원인

이 문제는 프런트 엔드 호스트로 추가된 사용자 지정 도메인에 대한 회람 규칙을 구성하지 않은 경우 발생합니다. 해당 프런트 엔드 호스트에 대해 회람 규칙을 명시적으로 추가해야 합니다. Azure Front Door 하위 도메인(*.azurefd.net)의 프런트 엔드 호스트에 대해 회람 규칙이 이미 구성된 경우에도 마찬가지입니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계

사용자 지정 도메인에 대한 회람 규칙을 추가하여 선택한 백 엔드 풀로 트래픽을 보냅니다.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azure Front Door가 HTTP를 HTTPS로 리디렉션하지 않습니다.

### <a name="symptom"></a>증상

Azure Front Door에는 HTTP를 HTTPS로 리디렉션하는 회람 규칙이 있지만 도메인에 액세스하면 HTTP가 프로토콜로 유지됩니다.

### <a name="cause"></a>원인

이 동작은 Azure Front Door에 대해 회람 규칙을 올바르게 구성하지 않은 경우에 발생할 수 있습니다. 기본적으로 현재 구성이 구체적이지 않아 충돌하는 규칙이 있을 수 있습니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계

## <a name="request-to-a-frontend-host-name-returns-a-404-status-code"></a>프런트 엔드 호스트 이름에 대한 요청이 404 상태 코드를 반환합니다.

### <a name="symptom"></a>증상

프런트 엔드 호스트, 하나 이상의 백 엔드가 있는 백 엔드 풀, 프런트 엔드 호스트를 백 엔드 풀로 연결하는 회람 규칙을 구성하여 Azure Front Door 인스턴스를 만들었습니다. 하지만 구성된 프런트 엔드 호스트에 요청할 때 콘텐츠를 사용할 수 없습니다. 그 결과 요청이 HTTP 404 상태 코드를 반환합니다.

### <a name="cause"></a>원인

이 증상의 잠재적 원인에는 몇 가지가 있습니다.

* 백 엔드가 공용이 아니어서 Azure Front Door에 표시되지 않습니다.
* 백 엔드가 잘못 구성되어 Azure Front Door가 잘못된 요청을 보냅니다. 즉, 백 엔드는 HTTP만 수락하고 HTTPS는 허용하지 않습니다. 따라서 Azure Front Door가 HTTPS 요청을 전달하려고 합니다.
* 백 엔드가 백 엔드에 대한 요청으로 전달된 호스트 헤더를 거부하고 있습니다.
* 백 엔드 구성이 아직 완전히 배포되지 않았습니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계

* 배포 시간 확인:
   * 구성이 배포되도록 기다린 시간이 최소한 10분이 지났는지 확인하세요.

* 백 엔드 설정 확인:
    * 요청이 라우팅될 백 엔드 풀로 이동합니다. (이는 회람 규칙을 구성하는 방법에 따라 달라 집니다.) 백 엔드 호스트 유형 및 백 엔드 호스트 이름이 올바른지 확인합니다. 백 엔드가 사용자 지정 호스트일 경우 철자가 올바른지 확인합니다. 

    * HTTP 및 HTTPS 포트를 확인합니다. 대부분의 경우 각각 80 및 443이 올바르며, 변경 작업은 필요하지 않습니다. 하지만 백 엔드가 이런 식으로 구성되지 않아서 다른 포트에서 수신 대기 중일 가능성이 있습니다.

    * 프런트 엔드 호스트가 라우팅되어야 하는 백 엔드에 백 엔드 호스트 헤더가 구성되어 있는지 확인합니다. 대부분의 경우 이 헤더는 백 엔드 호스트 이름과 동일해야 합니다. 그러나 백 엔드에서 기대한 값과 다를 경우 다양한 HTTP 4xx 상태 코드가 반환될 수 있습니다. 백 엔드의 IP 주소를 입력하는 경우 백 엔드 호스트 헤더를 백 엔드의 호스트 이름으로 설정해야 할 수 있습니다.

* 회람 규칙 설정 확인:
    * 해당 프런트 엔드 호스트 이름에서 백 엔드 풀로 라우팅되어야 하는 회람 규칙으로 이동합니다. 요청이 전달될 때 허용된 프로토콜이 올바르게 구성되어 있는지 확인합니다. **허용되는 프로토콜** 필드는 Azure Front Door가 수락해야 하는 요청을 결정합니다. 전달 프로토콜은 요청을 백 엔드로 전달하기 위해 Azure Front Door가 사용해야 하는 프로토콜을 결정합니다.
      
      예를 들어 백 엔드가 HTTP 요청만 수락하는 경우 다음과 같은 구성이 유효합니다.
            
      * 허용되는 프로토콜은 HTTP 및 HTTPS입니다. 전달 프로토콜은 HTTP입니다. HTTPS가 허용되는 프로토콜이기 때문에 일치 요청이 작동하지 않습니다. 요청이 HTTPS로 제공되는 경우 Azure Front Door가 HTTPS를 사용하여 전달하려고 시도합니다.

      * 허용되는 프로토콜은 HTTP입니다. 전달 프로토콜은 일치 요청 또는 HTTP입니다.
    - **URL 다시 쓰기** 는 기본적으로 사용하지 않도록 설정됩니다. 이 필드는 사용 가능하도록 설정할 백 엔드 호스트 리소스의 범위를 좁히려는 경우에만 사용됩니다. 이 필드를 사용하지 않도록 설정하면 Azure Front Door가 수신하는 것과 동일한 요청 경로를 전달합니다. 그러면 필드를 잘못 구성할 수 있습니다. 따라서 사용할 수 없는 백 엔드에서 Azure Front Door가 리소스를 요청하는 경우 HTTP 404 상태 코드를 반환합니다.

## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>프런트 엔드 호스트 이름에 대한 요청은 411 상태 코드를 반환합니다.

### <a name="symptom"></a>증상

Azure Front Door 인스턴스를 만들고, 프런트 엔드 호스트, 하나 이상의 백 엔드가 있는 백 엔드 풀, 프런트 엔드 호스트를 백 엔드 풀로 연결하는 회람 규칙을 구성했습니다. HTTP 411 상태 코드가 반환된 것으로 보아 구성된 프런트 엔드 호스트로 요청을 보낼 때 콘텐츠가 보이지 않는 것 같습니다.

요청에 대한 응답에는 설명문을 포함하는 HTML 오류 페이지가 응답 본문에 포함될 수도 있습니다. 예: `HTTP Error 411. The request must be chunked or have a content length`

### <a name="cause"></a>원인

이 증상에는 몇 가지 잠재적 원인이 있습니다. 전반적인 이유는 HTTP 요청이 완전히 RFC 규격이 아니라는 것입니다. 

규격을 준수하지 않은 예로는 `Content-Length` 또는 `Transfer-Encoding` 헤더 없이(예: `curl -X POST https://example-front-door.domain.com` 사용) 전송된 `POST` 요청입니다. 이 요청은 [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2)에 명시된 요구 사항을 충족하지 않습니다. Azure Front Door는 HTTP 411 응답을 사용하여 차단합니다.

이 동작은 Azure Front Door의 WAF(웹 애플리케이션 방화벽) 기능과는 별개입니다. 현재 이 동작을 사용하지 않도록 설정하는 방법은 없습니다. WAF 기능을 사용하지 않는 경우에도 모든 HTTP 요청은 요구 사항을 충족해야 합니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계

- 요청이 필요한 RFC에 명시된 요구 사항을 준수하는지 확인합니다.

- 요청에 대한 응답으로 반환되는 HTML 메시지 본문을 기록해 둡니다. 메시지 본문은 요청이 정확히 *어떻게* 규격을 준수하지 않았는지 설명하는 경우가 많습니다.
