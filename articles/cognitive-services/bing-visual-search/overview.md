---
title: Bing Visual Search API란?
titleSuffix: Azure Cognitive Services
description: Bing Visual Search는 유사한 이미지 또는 쇼핑 소스와 같은 이미지에 대한 세부 정보 또는 인사이트를 제공합니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 12/19/2019
ms.openlocfilehash: 8574a1272615cb72722e735373bc6609b2890d81
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669515"
---
# <a name="what-is-the-bing-visual-search-api"></a>Bing Visual Search API란?

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

Bing Visual Search API는 이미지에 대한 인사이트를 반환합니다. 이미지를 업로드하거나 이미지에 대한 URL을 제공할 수 있습니다. 인사이트는 시각적으로 비슷한 이미지, 쇼핑 소스, 이미지가 포함된 웹 페이지 등입니다. Bing Visual Search API가 반환하는 인사이트는 Bing.com/이미지에 표시되는 것과 유사합니다. 

[Bing Image Search API](../bing-image-search/overview.md)를 사용하는 경우 이미지를 업로드하는 대신 Bing Visual Search에 대한 API의 검색 결과의 인사이트 토큰을 사용할 수 있습니다.

> [!IMPORTANT]
> Bing Image Search API를 사용하여 이미지 인사이트를 가져오는 경우 더 포괄적인 인사이트를 제공하는 Bing Visual Search API로 전환하는 것이 좋습니다.

## <a name="insights"></a>자세한 정보

Bing Visual Search를 사용하여 다음과 같은 인사이트를 검색할 수 있습니다.

| 인사이트                              | Description |
|--------------------------------------|-------------|
| 시각적으로 유사한 이미지              | 입력 이미지와 시각적으로 유사한 이미지 목록 |
| 시각적으로 유사한 제품            | 표시된 제품과 시각적으로 유사한 제품            |
| 쇼핑 소스                     | 입력 이미지에 표시된 항목을 구입할 수 있는 장소 목록            |
| 관련 검색                     | 다른 사람이 수행했거나 이미지 콘텐츠를 기반으로 하는 관련 검색 목록            |
| 이미지를 포함하는 웹 페이지     | 입력 이미지를 포함하는 웹 페이지            |
| 레시피                              | 입력 이미지에 표시된 요리를 만드는 조리법이 포함된 웹 페이지            |
| 엔터티                             | 잘 알려진 사람, 장소 및 사물. |

이러한 인사이트 외에도 Bing Visual Search는 입력 이미지에서 파생된 다양한 용어(즉, 태그)도 반환합니다. 이러한 태그를 사용하여 이미지에서 찾은 개념을 살펴볼 수 있습니다. 예를 들어 입력 이미지가 유명 운동 선수인 경우 태그 중 하나는 운동 선수의 이름이고, 다른 태그는 스포츠일 수 있습니다. 또는 입력 이미지가 애플 파이인 경우 태그는 애플 파이, 파이, 디저트일 수 있습니다.

Bing Visual Search 결과에는 이미지의 관심 영역에 대한 경계 상자도 포함됩니다. 예를 들어 이미지에 유명인이 여러 명 포함된 경우 인식된 각 유명인의 경계 상자가 결과에 포함될 수 있습니다. 또는 Bing이 이미지에서 제품 또는 의류를 인식하는 경우, 결과에 인식된 항목의 경계 상자가 포함될 수 있습니다.

## <a name="workflow"></a>워크플로

Bing Visual Search API는 RESTful 웹 서비스이며, HTTP 요청을 수행하고 JSON을 구문 분석할 수 있는 프로그래밍 언어에서 쉽게 호출할 수 있습니다. 서비스에 REST API 또는 SDK를 사용할 수 있습니다.

1. Bing Search API에 액세스하기 위한 [Cognitive Services 계정](../cognitive-services-apis-create-account.md)을 만듭니다. Azure 구독이 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/cognitive-services/) 수 있습니다.
2. 유효한 검색 쿼리를 사용하여 API에 요청을 보냅니다.
3. 반환된 JSON 메시지를 구문 분석하여 API 응답을 처리합니다.

## <a name="next-steps"></a>다음 단계

먼저 Bing Visual Search API [대화형 데모](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/)를 사용해보세요.
데모에서는 검색 쿼리를 빠르게 사용자 지정하고 웹에서 이미지를 샅샅이 검색하는 방법을 보여줍니다.

첫 번째 요청으로 신속하게 시작하려면 빠른 시작:

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)

## <a name="see-also"></a>참고 항목

* [이미지 - Visual Search](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) 참조에는 이미지 기반 검색 결과를 요청하는 데 사용할 수 있는 엔드포인트, 요청, 헤더, 응답 및 쿼리 매개 변수에 대한 정의 및 정보가 설명되어 있습니다.

* [Bing Search API 사용 및 표시 요구 사항](../bing-web-search/use-display-requirements.md)에서는 Bing Search API를 통해 확보한 콘텐츠와 정보의 적합한 용도를 지정합니다.

* [Bing Search API 허브 페이지](../bing-web-search/overview.md)를 방문하여 사용 가능한 다른 API를 살펴보세요.