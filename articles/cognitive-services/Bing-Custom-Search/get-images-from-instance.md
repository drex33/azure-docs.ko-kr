---
title: 사용자 지정 보기에서 이미지 가져오기 - Bing Custom Search
titleSuffix: Azure Cognitive Services
description: Bing Custom Search를 사용하여 웹의 사용자 지정 보기에서 이미지를 가져오는 방법에 대한 간략한 개요입니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.openlocfilehash: 69e3e65155745d829b75640d2d342a1a24cabffd
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130036895"
---
# <a name="get-images-from-your-custom-view"></a>사용자 지정 보기에서 이미지 가져오기

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

Bing Custom Images Search를 통해 이미지를 사용하여 향상된 사용자 지정 검색 환경을 제공할 수 있습니다. 웹 결과와 마찬가지로 사용자 지정 검색은 인스턴스의 웹 사이트 목록에서 이미지 검색을 지원합니다. Bing 사용자 지정 이미지 검색 API를 사용하거나 호스트된 UI 기능을 통해 이미지를 얻을 수 있습니다. 호스트된 UI 기능은 간단하게 사용할 수 있고 검색 환경을 빨리 시작하고 실행할 수 있는 좋은 방법입니다.  이미지를 포함하도록 호스트된 UI를 구성하는 방법에 대한 자세한 내용은 [호스트된 UI 환경 구성](hosted-ui.md)을 참조하세요.

검색 결과 표시를 더 자세히 제어하려면 Bing 사용자 지정 이미지 검색 API를 사용할 수 있습니다. API 호출은 Bing Image Search API 호출과 유사하므로 API를 호출하는 예제를 보려면 [Bing Image Search](../Bing-Image-Search/overview.md)를 구매하세요. 하지만 구매하기 전에 [Custom Images Search API 참조](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) 콘텐츠를 숙지합니다. 기본 차이점은 지원되는 쿼리 매개 변수(customConfig 쿼리 매개 변수를 포함해야 함) 및 요청을 보낼 엔드포인트입니다.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->