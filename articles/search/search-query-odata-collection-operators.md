---
title: OData 컬렉션 연산자 참조
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 쿼리에서 필터 식을 만들 때 컬렉션이나 복합 컬렉션 필드에 필터가 적용되는 경우에는 람다 식에 “any” 및 “all” 연산자를 사용합니다.
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
ms.openlocfilehash: 03b46b80e7e442082c31f69b6fd9f59666cb36f2
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133265939"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Azure Cognitive Search의 OData 컬렉션 연산자 - `any` 및 `all`

Azure Cognitive Search에서 사용할 [OData 필터 식](query-odata-filter-orderby-syntax.md)을 작성할 때 컬렉션 필드를 필터링하는 것이 유용한 경우가 많습니다. 이 용도로 `any` 및 `all` 연산자를 사용할 수 있습니다.

## <a name="syntax"></a>구문

다음 EBNF([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form))는 `any` 또는 `all`을 사용하는 OData 식의 문법을 정의합니다.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

다음과 같은 대화형 구문 다이어그램도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search의 OData 구문 다이어그램](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> 전체 EBNF는 [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)를 참조하세요.

컬렉션을 필터링하는 식에는 세 가지 형식이 있습니다.

- 처음 두 개는 컬렉션 필드를 반복하여 람다 식의 형식으로 제공된 조건자를 각 컬렉션 요소에 적용합니다.
  - `all`을 사용하는 식은 컬렉션의 모든 요소에 대해 조건자가 true인 경우 `true`를 반환합니다.
  - `any`를 사용하는 식은 컬렉션의 요소 중 하나에 대해 조건자가 true인 경우 `true`를 반환합니다.
- 세 번째 형식의 컬렉션 필터는 람다 식 없이 `any`를 사용하여 컬렉션 필드가 비어 있는지 여부를 테스트합니다. 컬렉션에 요소가 있으면 `true`를 반환합니다. 컬렉션이 비어 있으면 `false`를 반환합니다.

컬렉션 필터의 **람다 식** 은 프로그래밍 언어의 루프 본문과 같습니다. 반복하는 동안 컬렉션의 현재 요소가 포함되는 **범위 변수** 라는 변수를 정의합니다. 또한 각 컬렉션 요소의 범위 변수에 적용할 필터 기준이 되는 또 다른 부울 식을 정의합니다.

## <a name="examples"></a>예

`tags` 필드에 “wifi” 문자열이 정확히 포함된 문서를 찾습니다.

```text
tags/any(t: t eq 'wifi')
```

`ratings` 필드의 모든 요소가 3~5(포함) 사이인 문서를 찾습니다.

```text
ratings/all(r: r ge 3 and r le 5)
```

`locations` 필드의 지역 좌표 중 하나가 지정된 다각형 내에 있는 문서를 찾습니다.

```text
locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))
```

`rooms` 필드가 비어 있는 문서를 찾습니다.

```text
not rooms/any()
```

모든 객실에서 `rooms/amenities` 필드에 “tv”가 포함되어 있고 `rooms/baseRate`가 100보다 작은 문서를 찾습니다.

```text
rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)
```

## <a name="limitations"></a>제한 사항

필터 식의 모든 기능을 람다 식의 본문 내에서 사용할 수 있는 것은 아닙니다. 필터링하려는 컬렉션 필드의 데이터 형식에 따라 제한 사항이 다릅니다. 다음 표에는 제한 사항이 요약되어 있습니다.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

이 제한 사항에 대한 자세한 내용과 예제는 [Azure Cognitive Search에서 컬렉션 필터 문제 해결](search-query-troubleshoot-collection-filters.md)을 참조하세요. 제한 사항이 있는 이유에 대한 자세한 내용은 [Azure Cognitive Search의 컬렉션 필터 이해](search-query-understand-collection-filters.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계  

- [Azure Cognitive Search의 필터](search-filters.md)
- [Azure Cognitive Search의 OData 식 언어 개요](query-odata-filter-orderby-syntax.md)
- [Azure Cognitive Search의 OData 식 구문 참조](search-query-odata-syntax-reference.md)
- [문서 검색&#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)