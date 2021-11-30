---
title: OData select 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리의 검색 결과에 반환할 필드를 명시적으로 선택하기 위한 구문 및 언어 참조입니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: reference
ms.date: 09/16/2021
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 77414b0c1e5b93abd4e57e17fe2c14afc2577105
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133268062"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search의 OData $select 구문

 [OData **$select** 매개 변수](query-odata-filter-orderby-syntax.md)를 사용하여 Azure Cognitive Search의 검색 결과에 포함할 필드를 선택할 수 있습니다. 이 문서에서는 **$select** 구문에 대해 자세히 설명합니다. 검색 결과를 표시할 때 **$select** 를 사용하는 방법에 대한 일반적인 내용은 [Azure Cognitive Search에서 검색 결과 작업 방법](search-pagination-page-layout.md)을 참조하세요.

## <a name="syntax"></a>구문

**$select** 매개 변수는 쿼리 결과 집합에 반환되는 각 문서의 필드를 결정합니다. 다음 EBNF([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))는 **$select** 매개 변수의 문법을 정의합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

다음과 같은 대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search의 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> 전체 EBNF는 [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)를 참조하세요.

**$select** 매개 변수는 다음 두 가지 형식으로 제공됩니다.

1. 검색 가능한 모든 필드가 반환되어야 함을 나타내는 단일 별표(`*`) 또는
1. 반환되어야 하는 필드를 식별하는 쉼표로 구분된 필드 경로 목록.

두 번째 형식을 사용하는 경우 목록에서 검색 가능한 필드만 지정할 수 있습니다.

하위 필드를 명시적으로 지정하지 않고 복합 필드를 나열하면 검색 가능한 모든 하위 필드가 쿼리 결과 집합에 포함됩니다. 예를 들어 인덱스에 `Address` 필드와 모두 검색 가능한 `Street`, `City`, `Country` 하위 필드가 있다고 가정합니다. **$select** 에서 `Address`를 지정하면 쿼리 결과에 세 개의 하위 필드가 모두 포함됩니다.

## <a name="examples"></a>예

`HotelId`, `HotelName`, `Rating` 최상위 필드와 `Address`의 `City` 하위 필드를 결과에 포함합니다.

```odata-filter-expr
    $select=HotelId, HotelName, Rating, Address/City
```

예제 결과는 다음과 같을 수 있습니다.

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

`HotelName` 최상위 필드 및 `Address`의 모든 하위 필드와 `Rooms` 컬렉션에 있는 각 개체의 `Type` 및 `BaseRate` 하위 필드를 결과에 포함합니다.

```odata-filter-expr
    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate
```

예제 결과는 다음과 같을 수 있습니다.

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search에서 검색 결과 작업 방법](search-pagination-page-layout.md)
- [Azure Cognitive Search의 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색&#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)