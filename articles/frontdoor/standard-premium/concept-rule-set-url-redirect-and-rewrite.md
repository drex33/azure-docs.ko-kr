---
title: Azure Front Door 표준/프리미엄(미리 보기)을 사용하여 URL 리디렉션 및 URL 다시 쓰기
description: 이 문서는 Azure Front Door가 Azure Front Door 규칙 집합을 사용하여 URL 리디렉션 및 URL 다시 쓰기를 지원하는 방법을 이해하는 데 도움이 됩니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 2e758560eed1ffb01117764f9399aa6f4f4b1395
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129857553"
---
# <a name="url-redirect-and-url-rewrite-with-azure-front-door-standardpremium-preview"></a>Azure Front Door 표준/프리미엄(미리 보기)을 사용하여 URL 리디렉션 및 URL 다시 쓰기

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

이 문서는 Azure Front Door 표준/프리미엄이 규칙 집합에 사용되는 URL 리디렉션 및 URL 다시 쓰기를 지원하는 방법을 이해하는 데 도움이 됩니다.

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="url-redirect"></a>URL 리디렉션

Azure Front Door는 프로토콜, 호스트 이름, 경로, 쿼리 문자열 및 조각 수준에서 트래픽을 리디렉션할 수 있습니다. 리디렉션은 경로 기반이므로 개별 마이크로 서비스에 대해 이러한 기능을 구성할 수 있습니다. URL 리디렉션을 사용하면 리소스 사용을 최적화하여 애플리케이션 구성을 간소화할 수 있고, 전역 및 경로 기반 리디렉션을 비롯한 새로운 리디렉션 시나리오가 지원됩니다.

규칙 집합을 통해 URL 리디렉션을 구성할 수 있습니다.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-redirect.png" alt-text="규칙 집합을 사용한 URL 리디렉션 만들기의 스크린샷" lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-redirect-expanded.png":::

### <a name="redirection-types"></a>리디렉션 유형
리디렉션 유형은 클라이언트가 리디렉션 용도를 파악할 수 있도록 응답 상태 코드를 설정합니다. 지원되는 리디렉션 유형은 다음과 같습니다.

* **301(영구적 이동)** : 대상 리소스가 새 영구 URI에 할당되었음을 나타냅니다. 이후 이 리소스에 대한 모든 참조는 포함된 URI 중 하나를 사용합니다. HTTP에서 HTTPS로 리디렉션에 301 상태 코드를 사용합니다.
* **302(찾음)** : 대상 리소스가 일시적으로 다른 URI 아래에 있음을 나타냅니다. 리디렉션은 수시로 변경될 수 있으므로 클라이언트는 이후 요청에 유효한 요청 URI를 계속 사용해야 합니다.
* **307(일시적 리디렉션)** : 대상 리소스가 일시적으로 다른 URI 아래에 있음을 나타냅니다. 사용자 에이전트는 해당 URI로 자동 리디렉션을 수행하는 경우 요청 메서드를 변경하지 않아야 합니다. 리디렉션은 시간이 지나면서 변경될 수 있으므로 클라이언트는 이후 요청에 원래의 유효한 요청 URI를 계속 사용해야 합니다.
* **308(영구적 리디렉션)** : 대상 리소스가 새 영구 URI에 할당되었음을 나타냅니다. 이후 이 리소스에 대한 모든 참조는 포함된 URI 중 하나를 사용해야 합니다.

### <a name="redirection-protocol"></a>리디렉션 프로토콜
리디렉션에 사용되는 프로토콜을 설정할 수 있습니다. 리디렉션 기능의 가장 일반적인 사용 사례는 HTTP에서 HTTPS로의 리디렉션을 설정하는 것입니다.

* **HTTPS만 사용**: HTTP에서 HTTPS로 트래픽을 리디렉션하는 경우 프로토콜을 HTTPS만 사용으로 설정합니다. Azure Front Door는 항상 리디렉션을 HTTPS로만 설정할 것을 권장합니다.
* **HTTP만 사용**: 들어오는 요청을 HTTP로 리디렉션합니다. 트래픽을 HTTP(암호화되지 않음)로 유지하려는 경우에만 이 값을 사용합니다.
* **요청 일치**: 이 옵션은 들어오는 요청에서 사용하는 프로토콜을 유지합니다. 따라서 리디렉션 후 HTTP 요청은 HTTP로 유지되고 HTTPS 요청은 HTTPS로 유지됩니다.

### <a name="destination-host"></a>대상 호스트
리디렉션 라우팅을 구성하는 과정에서 리디렉션 요청에 대한 호스트 이름 또는 도메인을 변경할 수도 있습니다. 이 필드를 리디렉션 URL의 호스트 이름으로 변경할 수도 있고, 들어오는 요청의 호스트 이름을 유지할 수도 있습니다. 따라서 이 필드를 사용하여 `https://www.contoso.com/*`으로 전송되는 모든 요청을 `https://www.fabrikam.com/*`으로 리디렉션할 수 있습니다.

### <a name="destination-path"></a>대상 경로
리디렉션의 일부로 URL의 경로 세그먼트를 바꾸려는 경우 이 필드를 새 경로 값으로 설정할 수 있습니다. 그렇지 않으면 리디렉션의 일부로 경로 값을 유지하도록 선택할 수 있습니다. 따라서 이 필드를 사용하여 `https://www.contoso.com/\*`로 전송되는 모든 요청을 `https://www.contoso.com/redirected-site`로 리디렉션할 수 있습니다.

### <a name="query-string-parameters"></a>쿼리 문자열 매개 변수
리디렉션되는 URL의 쿼리 문자열 매개 변수를 바꿀 수도 있습니다. 들어오는 요청 URL에서 기존 쿼리 문자열을 바꾸려면 이 필드를 '바꾸기'로 설정하고 적절한 값을 설정합니다. 그렇지 않으면 이 필드를 '유지'로 설정하여 원래 쿼리 문자열 집합을 유지할 수 있습니다. 예를 들어 이 필드를 사용하여 `https://www.contoso.com/foo/bar`로 전송되는 모든 트래픽을 `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`로 리디렉션할 수 있습니다. 

### <a name="destination-fragment"></a>대상 조각
대상 조각은 브라우저가 웹 페이지의 특정 섹션을 표시하는 데 사용되는 URL의 '#' 이후 부분입니다. 이 필드를 설정하여 리디렉션 URL에 조각을 추가할 수 있습니다.

## <a name="url-rewrite"></a>URL 다시 쓰기

Azure Front Door는 원본으로 라우팅하는 요청의 경로를 다시 작성하는 URL 다시 쓰기를 지원합니다. URL 다시 쓰기에서는 특정 조건이 충족되는 경우에만 URL 또는 지정된 헤더를 다시 쓸 수 있도록 조건을 추가할 수 있습니다. 이러한 조건은 요청 및 응답 정보를 기반으로 합니다.

이 기능을 사용하면 시나리오, 디바이스 유형 및 요청된 파일 유형에 따라 사용자를 다른 원본으로 리디렉션할 수 있습니다.

규칙 집합을 통해 URL 리디렉션을 구성할 수 있습니다.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite.png" alt-text="규칙 집합을 사용한 URL 다시 쓰기 만들기의 스크린샷" lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite-expanded.png":::

### <a name="source-pattern"></a>원본 패턴

원본 패턴은 바꾸려는 원본 요청의 URL 경로입니다. 현재, 원본 패턴은 접두사 기반 일치를 사용합니다. 모든 URL 경로를 일치시키려면 원본 패턴 값으로 슬래시(/)를 사용합니다.

URL 재작성 원본 패턴의 경우 경로 구성 "패턴 일치" 후 경로만 고려 합니다. 예를 들어 다음과 같은 들어오는 URL 형식이 있는 경우 다시 `<Frontend-domain>/<route-patterns-to-match-path>/<Rule-URL-Rewrite-Source-pattern>` `/<Rule-URL-Rewrite-Source-pattern>` 작성할 원본 패턴으로 규칙 엔진에서 고려 됩니다. 따라서 소스 패턴 일치를 사용 하는 URL 재작성 규칙이 있는 경우 보내는 URL의 형식은이 됩니다 `<Frontend-domain>/<route-patterns-to-match-path>/<Rule-URL-Rewrite-destination>` .

URL 경로의 세그먼트를 제거 해야 하는 시나리오의 경우 `/<route-patterns-to-match-path` 경로 구성에서 원본 그룹의 원본 경로를로 설정 합니다 `/` .

### <a name="destination"></a>대상

다시 쓰기에 사용할 대상 경로를 정의할 수 있습니다. 대상 경로는 원본 패턴을 덮어씁니다.

### <a name="preserve-unmatched-path"></a>일치하지 않는 경로 유지

일치하지 않는 경로 유지를 사용하면 원본 패턴 이후의 나머지 경로를 새 경로에 추가할 수 있습니다.

**일치하지 않는 경로 유지를 '예'로 설정** 하는 경우
* 들어오는 요청이 `www.contoso.com/sub/1.jpg`인 경우 원본 패턴은 `/`로 설정되고, 대상은 `/foo/`로 설정되고, 콘텐츠는 원본의 `/foo/sub/1`.jpg로부터 제공됩니다.

* 들어오는 요청이 `www.contoso.com/sub/image/1.jpg`인 경우 원본 패턴은 `/sub/`로 설정되고, 대상은 `/foo/`로 설정되고, 콘텐츠는 원본의 `/foo/image/1.jpg`로부터 제공됩니다.

**일치하지 않는 경로 유지를 '아니요'로 설정** 하는 경우
* 들어오는 요청이 `www.contoso.com/sub/image/1.jpg`인 경우 원본 패턴은 `/sub/`로 설정되고, 대상은 `/foo/2.jpg`로 설정되고, 콘텐츠는 `wwww.contoso.com/sub/`에서 다음에 나오는 경로에 관계없이 항상 원본의 `/foo/2.jpg`로부터 제공됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Front Door 표준/프리미엄 규칙 집합](concept-rule-set.md)에 대해 자세히 알아봅니다.
