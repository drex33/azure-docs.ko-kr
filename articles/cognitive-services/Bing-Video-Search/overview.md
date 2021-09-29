---
title: Bing Video Search API란?
titleSuffix: Azure Cognitive Services
description: Bing Video Search API를 사용하여 웹을 통해 비디오를 검색하는 방법을 알아봅니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 12/18/2019
ms.openlocfilehash: 5e1586ac15df484cd4110a63c922bc72785a406f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600744"
---
# <a name="what-is-the-bing-video-search-api"></a>Bing Video Search API란?

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

Bing Video Search API를 통해 쉽게 서비스 및 애플리케이션에 비디오 검색 기능을 추가할 수 있습니다. API를 사용하여 사용자 검색 쿼리를 보내면 [Bing Video](https://www.bing.com/video)와 비슷한 관련 고품질 비디오를 가져와서 표시할 수 있습니다. 비디오만 포함하는 검색 결과에 이 API를 사용합니다. [Bing Web Search API](../bing-web-search/overview.md)는 웹 페이지, 비디오, 뉴스 및 이미지를 비롯한 웹 콘텐츠의 다른 유형을 반환할 수 있습니다.

## <a name="bing-video-search-api-features"></a>Bing Video Search API 기능

| 기능                                                                                                                                                                                 | Description                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [실시간 검색 용어 제안](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md)를 통해 입력하는 대로 제안되는 검색 용어를 표시하여 응용 프로그램 환경을 향상시킵니다. |
| [비디오 결과 필터링 및 제한](concepts/get-videos.md#filtering-videos)                      | 쿼리 매개 변수를 편집하여 반환된 비디오를 필터링합니다.                                                                                                       |
| [썸네일 자르기, 크기 조정 및 표시](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Bing Video Search API에서 반환하는 비디오에 대한 썸네일 미리 보기를 편집하고 표시합니다.                                                                                      |
| [인기 비디오 가져오기](trending-videos.md) | 전 세계의 최신 비디오를 검색합니다.                                                                                                          |
| [비디오 인사이트 가져오기](video-insights.md) | 전 세계로부터의 최신 비디오 검색을 사용자 지정합니다.                                                                                                          |

## <a name="workflow"></a>워크플로

Bing Video Search API는 RESTful 웹 서비스이며, HTTP 요청을 수행하고 JSON을 구문 분석할 수 있는 프로그래밍 언어에서 쉽게 호출할 수 있습니다. [REST API](./quickstarts/csharp.md) 또는 [SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)를 사용하여 서비스를 사용할 수 있습니다.

1. Bing Search API에 대한 액세스 권한이 있는 [Cognitive Services API 계정](../cognitive-services-apis-create-account.md)을 만듭니다. Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/cognitive-services/)을 만들 수 있습니다.
2. 유효한 검색 쿼리를 사용하여 API에 요청을 보냅니다.
3. 반환된 JSON 메시지를 구문 분석하여 API 응답을 처리합니다.


## <a name="next-steps"></a>다음 단계

Bing Video Search API [대화형 데모](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/)는 검색 쿼리를 사용자 지정하고 비디오에 대해 웹을 검색하는 방법을 보여줍니다.

첫 번째 API 요청을 빠르게 시작하려면 [빠른 시작](./quickstarts/csharp.md)을 사용합니다.

## <a name="see-also"></a>참고 항목

* [Bing Video Search API v7](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) 참조 페이지에는 검색 결과를 요청하는 데 사용되는 엔드포인트, 헤더 및 쿼리 매개 변수의 목록이 포함되어 있습니다.

* [Bing 사용 및 표시 요구 사항](../bing-web-search/use-display-requirements.md)에서는 Bing 검색 API를 통해 획득한 콘텐츠와 정보의 허용 가능한 용도를 지정하고 있습니다.

* [Bing Search API 허브 페이지](../bing-web-search/overview.md)를 방문하여 사용 가능한 다른 API를 살펴보세요.