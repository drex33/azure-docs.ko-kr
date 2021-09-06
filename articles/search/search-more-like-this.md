---
title: moreLikeThis(미리 보기) 쿼리 기능
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search REST API의 미리 보기 버전에서 제공되는 moreLikeThis(미리 보기) 기능에 관해 설명합니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 595884bf9d73e6e7af6663c051a2735908670eb4
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112578670"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>Azure Cognitive Search의 moreLikeThis(미리 보기)

> [!IMPORTANT] 
> 이 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 따라 공개 미리 보기로 제공됩니다. [미리 보기 REST API](/rest/api/searchservice/index-preview)에서 이 기능을 지원합니다.

`moreLikeThis=[key]`는 문서 키로 지정된 문서와 유사한 문서를 찾는 [문서 검색 API](/rest/api/searchservice/search-documents)의 쿼리 매개 변수입니다. 검색 요청이 `moreLikeThis`를 사용하여 설정될 경우 지정한 문서에서 추출된 검색 용어 중에서 해당 문서를 가장 잘 설명하는 용어를 사용해서 쿼리가 생성됩니다. 그런 후 생성된 쿼리를 사용해서 검색 요청이 수행됩니다. 기본적으로 `searchFields` 매개 변수를 사용하여 지정한 제한된 필드를 제외한 모든 검색 가능 필드의 콘텐츠를 고려합니다. `moreLikeThis` 매개 변수와 검색 매개 변수 `search=[string]`을 함께 사용할 수 없습니다.

기본적으로 모든 최상위 수준 검색 가능 필드의 콘텐츠를 고려합니다. 대신 특정 필드를 지정하려면 `searchFields` 매개 변수를 사용할 수 있습니다. 

[복합 형식](search-howto-complex-data-types.md)의 검색 가능한 하위 필드에는 `MoreLikeThis`를 사용할 수 없습니다.

## <a name="examples"></a>예

다음 예제는 모두 [빠른 시작: Azure Portal에서 검색 인덱스 만들기](search-get-started-portal.md)의 호텔 샘플을 사용합니다.

### <a name="simple-query"></a>단순 쿼리

다음 쿼리는 설명 필드가 `moreLikeThis` 매개 변수로 지정된 원본 문서의 필드와 가장 유사한 문서를 찾습니다.

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2020-06-30-Preview
```

이 예제에서 요청은 `HotelId` 29와 유사한 호텔을 검색합니다.
HTTP GET을 사용하는 대신 HTTP POST를 사용하여 `MoreLikeThis`를 호출할 수도 있습니다.

```
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>필터 적용

`MoreLikeThis`는 `$filter`와 같은 다른 일반적인 쿼리 매개 변수와 결합될 수 있습니다. 예를 들어, 쿼리는 범주가 ‘예산’이고 평점이 3.5보다 높은 호텔로만 제한될 수 있습니다.

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2020-06-30-Preview
```

### <a name="select-fields-and-limit-results"></a>필드 선택 및 결과 제한

`$top` 선택기를 사용하여 `MoreLikeThis` 쿼리에서 반환되어야 하는 결과 수를 제한할 수 있습니다. 또한 `$select`를 사용하여 필드를 선택할 수 있습니다. 여기에서 상위 3개 호텔이 ID, 이름, 등급과 함께 선택됩니다. 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2020-06-30-Preview
```

## <a name="next-steps"></a>다음 단계

모든 웹 테스트 도구를 사용하여 기능을 시험해 볼 수 있습니다.  이 연습에서는 Postman을 사용하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search REST API 살펴보기](search-get-started-rest.md)