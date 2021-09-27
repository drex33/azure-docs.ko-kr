---
title: Azure Front Door 표준/프리미엄 구성 문제 해결
description: 이 자습서에서는 Azure Front Door 표준/프리미엄 인스턴스에 발생할 수 있는 일반적인 문제 중 일부를 해결하는 방법에 대해 알아봅니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/08/2021
ms.author: duau
ms.openlocfilehash: b49d7d051b099c47fa6bfe65ed8c0ee8f4b03872
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124792385"
---
# <a name="troubleshooting-common-routing-problems-with-azure-front-door-standardpremium"></a>Azure Front Door 표준/프리미엄의 일반적인 라우팅 문제 해결

이 문서에서는 Azure Front Door 구성에서 발생할 수 있는 몇 가지 일반적인 라우팅 문제를 해결하는 방법을 설명합니다.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>몇 초 후에 Azure Front Door에서 503 응답

### <a name="symptom"></a>증상

* Azure Front Door를 거치지 않고 백 엔드로 전송되는 일반 요청이 성공합니다. Azure Front Door를 통해 전송하면 503 오류 응답이 발생합니다.
* Azure Front Door의 실패 알림은 일반적으로 약 30초 후에 표시됩니다.
* 로그가 인 간헐적인 503 `ErrorInfo: OriginInvalidResponse` 오류입니다.

### <a name="cause"></a>원인

이 문제의 원인은 다음 세 가지 중 하나일 수 있습니다.
 
* 원본이 Azure Front Door에서 요청을 받기 위해 구성된 시간 제한(기본값은 30초)보다 오래 걸리고 있습니다.
* Azure Front Door에서 요청에 대한 응답을 보내는 데 걸리는 시간이 제한 시간 값보다 오래 걸리고 있습니다.
* 클라이언트가 (압축 사용)를 사용하여 바이트 범위 요청을 `Accept-Encoding header` 보냈습니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계

* Azure Front Door를 거치지 않고 백 엔드에 직접 요청을 보냅니다. 백 엔드가 일반적으로 응답하는 데 걸리는 시간을 확인합니다.
* Azure Front Door를 통해 요청을 보내고 503 응답이 표시되는지 확인합니다. 그렇지 않은 경우에는 시간 제한 문제가 아닐 수 있습니다. 지원에 문의
* 요청이 Azure Front Door 503 오류 응답 코드가 발생하는 경우 엔드포인트에 대한 **원본 응답 제한 시간(초)을** 구성합니다. 기본 시간 제한은 최대 4분(240초)으로 연장할 수 있습니다. *엔드포인트 관리자로* 가서 **엔드포인트 편집** 을 선택하여 설정을 구성할 수 있습니다.

    :::image type="content" source="..\media\troubleshoot-route-issues\origin-response-timeout-1.png" alt-text="엔드포인트 관리자에서 엔드포인트 편집을 선택하는 스크린샷.":::

    그런 **다음, 엔드포인트 속성을** 선택하여 **원본 응답 시간 제한** 을 구성합니다.

    :::image type="content" source="..\media\troubleshoot-route-issues\origin-response-timeout-2.png" alt-text="엔드포인트 속성 선택 및 원본 응답 시간 제한 필드의 스크린샷." lightbox="..\media\troubleshoot-route-issues\origin-response-timeout-2-expanded.png":::

* 시간 제한으로 문제가 해결되지 않으면 Fiddler 또는 브라우저의 개발자 도구와 같은 도구를 사용하여 클라이언트가 Accept-Encoding 헤더를 사용하여 바이트 범위 요청을 보내고 있는지 확인하여 원본이 다른 콘텐츠 길이로 응답하도록 합니다. 그렇다면 원본/Azure Front Door 압축을 사용하지 않도록 설정하거나 바이트 범위 요청에 대한 요청에서 제거하는 규칙 집합 규칙을 만들 수 `accept-encoding` 있습니다.

    :::image type="content" source="..\media\troubleshoot-route-issues\remove-encoding-rule.png" alt-text="규칙 집합의 수락 인코딩 규칙 스크린샷.":::

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>사용자 지정 도메인에 전송된 요청이 400 상태 코드를 반환합니다.

### <a name="symptom"></a>증상

* Azure Front Door 인스턴스를 만들었지만 도메인 또는 프런트 엔드 호스트에 대한 요청이 HTTP 400 상태 코드를 반환하고 있습니다.
* 구성한 사용자 지정 도메인에 프런트 엔드 호스트에 대한 DNS 매핑을 만들었습니다. 그러나 사용자 지정 도메인 호스트 이름에 요청을 보내면 HTTP 400 상태 코드가 반환됩니다. 구성된 백 엔드로 라우팅하는 것은 아닙니다.

### <a name="cause"></a>원인

이 문제는 프런트 엔드 호스트로 추가된 사용자 지정 도메인에 대한 회람 규칙을 구성하지 않은 경우 발생합니다. 해당 프런트 엔드 호스트에 대해 회람 규칙을 명시적으로 추가해야 합니다. Azure Front Door 하위 도메인(*.azurefd.net)의 프런트 엔드 호스트에 대해 회람 규칙이 이미 구성된 경우에도 마찬가지입니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계

사용자 지정 도메인에 대한 라우팅 규칙을 추가하여 선택한 원본 그룹으로 트래픽을 보냅니다.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azure Front Door가 HTTP를 HTTPS로 리디렉션하지 않습니다.

### <a name="symptom"></a>증상

Azure Front Door에는 HTTP를 HTTPS로 리디렉션하는 회람 규칙이 있지만 도메인에 액세스하면 HTTP가 프로토콜로 유지됩니다.

### <a name="cause"></a>원인

이 동작은 Azure Front Door에 대해 회람 규칙을 올바르게 구성하지 않은 경우에 발생할 수 있습니다. 기본적으로 현재 구성이 구체적이지 않아 충돌하는 규칙이 있을 수 있습니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계


## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>프런트 엔드 호스트 이름에 대한 요청은 411 상태 코드를 반환합니다.

### <a name="symptom"></a>증상

Azure Front Door 표준/프리미엄 인스턴스를 만들었고 프런트 엔드 호스트, 하나 이상의 원본이 있는 원본 그룹, 프런트 엔드 호스트를 원본 그룹에 연결하는 라우팅 규칙을 구성했습니다. HTTP 411 상태 코드가 반환된 것으로 보아 구성된 프런트 엔드 호스트로 요청을 보낼 때 콘텐츠가 보이지 않는 것 같습니다.

요청에 대한 응답에는 설명문을 포함하는 HTML 오류 페이지가 응답 본문에 포함될 수도 있습니다. 예: `HTTP Error 411. The request must be chunked or have a content length`

### <a name="cause"></a>원인

이 증상을 일으키는 원인은 여러가지가 있을 수 있습니다. 전반적인 이유는 HTTP 요청이 완전히 RFC 규격이 아니라는 것입니다. 

규격을 준수하지 않은 예는 `Content-Length` 또는 `Transfer-Encoding` 헤더 없이(예: `curl -X POST https://example-front-door.domain.com` 사용) 전송된 `POST` 요청이 있습니다. 이 요청은 [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2)에 명시된 요구 사항을 충족하지 않습니다. Azure Front Door는 HTTP 411 응답을 사용하여 차단합니다.

이 동작은 Azure Front Door의 WAF(웹 애플리케이션 방화벽) 기능과는 별개입니다. 현재 이 동작을 사용하지 않도록 설정할 수 있는 방법은 없습니다. WAF 기능이 사용되지 않는 경우에도 모든 HTTP 요청은 요구 사항을 충족해야 합니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계

- 요청이 필요한 RFC에 설정된 요구 사항을 준수하는지 확인합니다.

- 요청에 대한 응답으로 반환되는 HTML 메시지 본문을 기록해 둡니다. 메시지 본문은 요청이 정확히 *어떻게* 규격을 준수하지 않았는지 설명하는 경우가 많습니다.

## <a name="next-steps"></a>다음 단계

[Front Door 표준/프리미엄을 만드는](create-front-door-portal.md) 방법을 알아봅니다.
