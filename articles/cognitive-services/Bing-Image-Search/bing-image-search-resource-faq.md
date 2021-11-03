---
title: FAQ(질문과 대답) - Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Bing Image Search API와 관련된 개념, 코드 및 시나리오에 대한 일반적인 질문과 답변에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 560a93439618c320990484e89da0adc9d04206e7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082412"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Bing Image Search API에 관한 FAQ(질문과 대답)

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

Azure에서 Azure Cognitive Services용 Bing Image Search API와 관련된 개념, 코드 및 시나리오에 대한 일반적인 질문에 대한 대답을 찾습니다.

## <a name="response-headers-in-javascript"></a>JavaScript에서 응답 헤더

Bing Image Search API의 응답에 다음 헤더가 발생할 수 있습니다.

| attribute           | 설명   |
| ------------------- | ------------- |
| `X-MSEdge-ClientID` |Bing에서 사용자에게 할당한 고유한 ID |
| `BingAPIs-Market`   |요청을 이행하는 데 사용된 지역/국가 |
| `BingAPIs-TraceId`  |이 요청에 대한 Bing API 서버의 로그 항목(지원용) |

클라이언트 ID를 유지하고 후속 요청과 함께 반환하는 것이 특히 중요합니다. 이렇게 하면 검색은 검색 결과의 순위 지정에서 과거 컨텍스트를 사용하고 일관된 사용자 환경도 제공합니다.

그러나 JavaScript에서 Bing Image Search API를 호출하면 브라우저의 기본 제공 보안 기능(CORS)이 이러한 헤더 값으로의 액세스를 차단할 수 있습니다.

헤더에 액세스하기 위해 CORS 프록시를 통해 Bing Image Search API 요청을 만들 수 있습니다. 이러한 프록시의 응답에는 응답 헤더를 필터링하고 JavaScript에서 응답 헤더를 사용할 수 있게 해주는 `Access-Control-Expose-Headers` 헤더가 포함됩니다.

[자습서 앱](tutorial-bing-image-search-single-page-app.md)이 선택적 클라이언트 헤더에 액세스할 수 있도록 CORS 프록시를 쉽게 설치할 수 있습니다. 먼저 [Node.js가 없는 경우 설치](https://nodejs.org/en/download/)합니다. 그런 다음, 명령 프롬프트에서 다음 명령을 입력합니다.

```console
npm install -g cors-proxy-server
```

다음으로, HTML 파일에서 Bing Image Search API 엔드포인트를 다음으로 변경합니다.
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

마지막으로 다음 명령을 사용하여 CORS 프록시를 시작합니다.

```console
cors-proxy-server
```

자습서 앱을 사용하는 동안에는 명령 창을 열어 두세요. 창을 닫으면 프록시가 중지됩니다. 검색 결과 아래의 확장 가능한 HTTP 헤더 섹션에서 여러 `X-MSEdge-ClientID` 헤더를 볼 수 있으며 요청마다 동일한지 확인합니다.

## <a name="response-headers-in-production"></a>프로덕션에서 응답 헤더

이전 응답에서 설명한 CORS 프록시 방식은 개발, 테스트 및 학습에 적합합니다.

그러나 프로덕션 환경에서는 Bing Web Search API를 사용하는 웹 페이지와 동일한 도메인에서 서버 쪽 스크립트를 호스트해야 합니다. 이 스크립트는 실제로 웹 페이지 JavaScript의 요청에 따라 API 호출을 수행하고 헤더를 포함한 모든 결과를 다시 클라이언트로 전달해야 합니다. 두 리소스(페이지 및 스크립트)가 원본을 공유하므로 CORS는 작동하지 않으며 특수 헤더가 웹 페이지의 JavaScript에 액세스할 수 있습니다.

이 방법은 또한 API 키가 서버 쪽 스크립트에서만 필요하기 때문에 API 키가 공개적으로 노출되지 않도록 합니다. 이 스크립트는 다른 메서드(예: HTTP 참조 페이지)를 사용하여 요청이 승인되었는지 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

없는 기능 또는 특징에 대해 질문이 있으면 [피드백 도구](https://feedback.azure.com/d365community/forum/09041fae-0b25-ec11-b6e6-000d3a4f0858)를 사용하여 요청하거나 투표하는 것이 좋습니다.

## <a name="see-also"></a>참고 항목

 [Stack Overflow: Cognitive Services](https://stackoverflow.com/questions/tagged/bing-api)
