---
title: Bing 인사이트의 예 - Bing Visual Search
titleSuffix: Azure Cognitive Services
description: 이 문서에는 Bing Visual Search가 사용하고 Bing.com에 이미지 인사이트를 표시할 수 있는 방법의 예제가 포함되어 있습니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 51248cb5e37e6f109873340cca74c46e4af6a721
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669667"
---
# <a name="examples-of-bing-insights-usage"></a>Bing 인사이트 사용 예

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

이 문서에는 Bing이 사용하고 Bing.com에 이미지 인사이트를 표시할 수 있는 방법의 예제가 포함되어 있습니다.

## <a name="pagesincluding-insight-example"></a>PagesIncluding 인사이트 예제

다음은 첫 번째 웹 페이지에 대한 링크를 표시하고, 사용자가 이미지를 포함하는 다른 웹 페이지 목록을 확장하고 축소할 수 있게 합니다.

![다음과 같은 확장된 페이지](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>ShoppingSources 인사이트 예제

다음은 Bing이 이미지에 표시된 제품에 대한 쇼핑 소스를 표시할 수 있는 방법을 보여줍니다.

![쇼핑 소스](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>VisualSearch 인사이트 예제

다음은 Bing이 시각적으로 유사한 이미지(예제의 **관련 이미지** 참조)를 표시할 수 있는 방법을 보여줍니다.

![시각적으로 유사한 이미지](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Recipes 인사이트 예제

다음은 Bing이 이미지에 표시된 음식의 조리법을 표시할 수 있는 방법을 보여 줍니다. 이 예제에서는 사용 가능한 조리법이 있음을 사용자에게 알립니다.

![다음과 같은 조리법 및 페이지](./media/recipes-pages-including.PNG)

 또한 사용자가 목록을 확장하면 조리법에 대한 링크를 제공합니다.

![다음과 같은 확장된 조리법 페이지](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>RelatedSearches 인사이트 예제

다음은 Bing이 다른 사용자가 수행한 이미지의 관련 검색을 표시할 수 있는 방법을 보여 줍니다. 사용자가 이미지를 클릭하면 해당 관련 쿼리에 대한 Bing.com/Images 검색 결과 페이지로 이동됩니다.

![이미지의 관련 검색](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Entity 인사이트 예제

다음은 Bing이 이미지에 표시된 엔터티(사람, 장소 또는 사물)에 대한 정보를 표시할 수 있는 방법을 보여 줍니다. 사용자가 엔터티 링크를 클릭하면 엔터티에 대한 Bing.com 검색 결과 페이지로 이동됩니다.

![이미지에 표시된 엔터티](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>사용자가 탐색할 수 있는 다른 인사이트 표시

다음은 Bing이 사용자가 탐색할 수 있는 이미지에 대한 기타 정보를 표시할 수 있는 방법을 보여 줍니다.

![이미지에 대한 다른 인사이트 탐색](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>경계 상자 및 핫 스폿

비기본 태그에는 태그가 적용되는 이미지의 관심 영역을 식별하는 경계 상자가 포함됩니다. 경계 상자가 전체 이미지를 식별하지 않는 경우, 경계 상자를 사용하여 이미지에 핫 스폿을 만듭니다. 사용자는 핫 스폿을 클릭하여 핫 스폿(또는 사각형)에서 찾은 콘텐츠와 관련된 정보를 가져올 수 있습니다. 예를 들어 이미지가 하이 패션 이미지인 경우 결과에는 지갑, 보석류, 스카프 등 이미지에 표시된 액세서리에 대한 태그(및 경계 상자)가 포함될 수 있습니다. 다음 예제는 이미지에 표시된 선글라스에 대한 핫 스폿 사각형을 보여줍니다.

![경계 상자 및 핫 스폿](./media/click-to-search.PNG)

## <a name="next-steps"></a>다음 단계

첫 번째 요청을 시작하려면 빠른 시작을 참조하세요.

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)