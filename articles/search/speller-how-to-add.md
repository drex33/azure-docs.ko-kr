---
title: 맞춤법 검사 추가
titleSuffix: Azure Cognitive Search
description: 쿼리를 실행하기 전에 쿼리 용어의 오타를 수정하려면 쿼리 파이프라인에 맞춤법 교정 기능을 연결합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/29/2021
ms.custom: references_regions
ms.openlocfilehash: 98aec7fc2a3857ac50125e7e49c5f9ab105a49d7
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535920"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Cognitive Search에서 쿼리에 대한 맞춤법 검사 추가

> [!IMPORTANT]
> 맞춤법 수정은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 따라 공개 미리 보기로 제공됩니다. Azure Portal 및 미리 보기 REST API를 통해서만 사용할 수 있습니다.

검색 엔진에 도달하기 전에 개별 검색 쿼리 용어의 맞춤법을 교정하여 재현율을 개선할 수 있습니다. **맞춤법 검사기** 매개 변수는 모든 쿼리 유형([단순](query-simple-syntax.md), [전체](query-lucene-syntax.md), 현재 공개 미리 보기로 제공되는 새로운 [의미 체계](semantic-how-to-query-request.md) 옵션)에서 지원됩니다.

맞춤법 검사기는 [의미 체계 검색 미리 보기](semantic-search-overview.md) 와 함께 릴리스 되었으며 queryLanguage 매개 변수를 공유 하지만, 그렇지 않은 경우에는 고유한 필수 구성 요소가 포함 된 독립적인 기능입니다. 이 기능을 사용 하기 위한 등록 또는 추가 요금은 없습니다.

## <a name="prerequisites"></a>필수 조건

맞춤법 검사를 사용 하려면 다음이 필요 합니다.

+ 기본 계층 이상, 모든 지역에 있는 검색 서비스입니다.

+ [지원되는 언어](#supported-languages)로 된 콘텐츠가 있는 기존 검색 인덱스

+ "맞춤법 검사기 = 어휘" 및 "queryLanguage"가 [지원 되는 언어로](#supported-languages)설정 된 [쿼리 요청](/rest/api/searchservice/preview-api/search-documents) 입니다. 맞춤법 검사는 "검색" 매개 변수에 전달 된 문자열에 대해 작동 합니다. 필터에는 지원 되지 않습니다.

쿼리 요청에서 미리 보기 Api를 지 원하는 검색 클라이언트를 사용 합니다. rest의 경우 미리 보기 api에 대 한 rest 호출을 수행 하기 위해 수정한 [postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)또는 코드를 사용할 수 있습니다. 또한 Azure Sdk의 베타 릴리스를 사용할 수 있습니다.

| 클라이언트 라이브러리 | 버전 |
|----------|----------|
| REST API | [2021-04-30-미리 보기](/rest/api/searchservice/index-preview) 또는 2020-06-30-미리 보기 |
| Azure SDK for .NET | [버전 11.3.0-beta. 2](https://www.nuget.org/packages/Azure.Search.Documents/11.3.0-beta.2) | 
| Java용 Azure SDK |  [버전 11.4.0-beta. 2](https://search.maven.org/artifact/com.azure/azure-search-documents/11.4.0-beta.2/jar) |
| JavaScript용 Azure SDK | [버전 11.2.0-beta. 2](https://www.npmjs.com/package/@azure/search-documents/v/11.2.0-beta.2) |
| Python용 Azure SDK | [버전 11.2.0 b3](https://pypi.org/project/azure-search-documents/11.2.0b3/) |

## <a name="spell-correction-with-simple-search"></a>단순 검색으로 맞춤법 수정

다음 예제에는 기본 제공 hotels-sample 인덱스를 사용하여 간단한 자유 형식 텍스트 쿼리에 대한 맞춤법을 수정하는 과정이 나와 있습니다. 맞춤법 수정 항목이 없으면 쿼리에서 결과를 0개 반환합니다. 수정 항목이 있으면 쿼리에서 Johnson의 가족 지향 재정렬 결과를 1개 반환합니다.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "famly acitvites",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "queryType": "simple",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="spell-correction-with-full-lucene"></a>전체 Lucene으로 맞춤법 수정

텍스트 분석을 거치는 개별 쿼리 용어에 대 한 맞춤법 수정이 발생 하므로 일부 Lucene 쿼리에서는 맞춤법 검사기 매개 변수를 사용할 수 있지만 다른 항목은 사용할 수 없습니다.

+ 텍스트 분석을 우회하는 비호환 쿼리 양식에는 와일드카드, regex, 퍼지가 포함됩니다.
+ 호환되는 쿼리 양식에는 필드 지정 검색, 근접, 용어 상승이 포함됩니다.

이 예제에서는 전체 Lucene 구문과 맞춤법이 틀린 쿼리 용어를 사용하여 범주 필드에 대한 필드 지정 검색을 사용합니다. 맞춤법 검사기를 포함하면 "Suiite"의 오타가 수정되고 쿼리에 성공합니다.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "Category:(Resort and Spa) OR Category:Suiite",
    "queryType": "full",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "select": "Category",
    "count": true
}
```

## <a name="spell-correction-with-semantic-search"></a>의미 체계 검색으로 맞춤법 수정

이 쿼리는 한 가지 용어를 제외한 모든 용어의 오타를 사용하여 관련 결과를 반환하도록 맞춤법을 수정합니다. 자세한 내용은 [의미 체계 쿼리 만들기](semantic-how-to-query-request.md)를 참조하세요.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview     
{
    "search": "hisotoric hotell wiht great restrant nad wiifi",
    "queryType": "semantic",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Tags,Description",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="supported-languages"></a>지원되는 언어

QueryLanguage에 유효한 값은 [지원 되는 언어 목록 (REST API 참조)](/rest/api/searchservice/preview-api/search-documents#queryLanguage)에서 복사한 다음 표에 나와 있습니다.

| 언어 | queryLanguage |
|----------|---------------|
| 영어[EN] | EN, EN-US(기본값) |
| 스페인어[ES] | ES, ES-ES(기본값)|
| 프랑스어[FR] | FR, FR-FR(기본값) |
| 독일어[DE] | DE, DE-DE(기본값) |
| 네덜란드어 [NL] | NL, NL, NL-NL (기본값) |

### <a name="querylanguage-considerations"></a>queryLanguage 고려 사항

다른 곳에서 언급 했 듯이, 쿼리 요청은 queryLanguage 매개 변수를 하나만 포함할 수 있지만이 매개 변수는 여러 기능에서 공유 되므로 각각 다른 코 호트의 언어를 지원 합니다. 맞춤법 검사만 사용 하는 경우 위의 표에 지원 되는 언어 목록이 전체 목록으로 표시 됩니다. 

### <a name="language-analyzer-considerations"></a>언어 분석기 고려 사항

영어가 아닌 콘텐츠를 포함 하는 인덱스는 영어가 아닌 필드에 [언어 분석기](index-add-language-analyzers.md) 를 사용 하 여 네이티브 언어의 언어 규칙을 적용 하는 경우가 많습니다.

이제 언어 분석을 수행 하는 콘텐츠에 맞춤법 검사를 추가 하는 경우 인덱싱 및 쿼리 처리의 모든 단계에서 동일한 언어를 사용 하면 더 나은 결과를 달성할 수 있습니다. 예를 들어 "fr-fr" 언어 분석기를 사용 하 여 필드의 콘텐츠를 인덱싱하고 나면 쿼리, 맞춤법 검사, 의미 체계 캡션 및 의미 체계 답변이 모든 형식의 프랑스어 어휘 또는 언어 라이브러리를 사용 해야 합니다.

Cognitive Search에서 언어 라이브러리를 사용하는 방법을 요약하면 다음과 같습니다.

+ 언어 분석기는 인덱싱 및 쿼리를 실행 하는 동안 호출 될 수 있으며 Apache Lucene (예: "de") 또는 Microsoft ("de. microsoft) 중 하나입니다.

+ Spell Check 중에 호출되는 언어 어휘집은 상단의 표에 있는 언어 코드 중 하나를 사용하여 지정됩니다.

쿼리 요청에서 queryLanguage에 할당 된 값은 맞춤법 검사기, [답변](semantic-answers.md)및 캡션에 동일 하 게 적용 됩니다. 

> [!NOTE]
> 언어 분석기를 사용하는 경우 다양한 속성 값의 언어 일관성은 매우 중요합니다. 언어와 관련 없는 분석기(키워드, 단순, 표준, 중지, 공백 또는 `standardasciifolding.lucene` 등)를 사용하는 경우 queryLanguage 값을 원하는 대로 지정할 수 있습니다.

검색 인덱스의 콘텐츠는 여러 언어로 구성될 수 있지만, 쿼리 입력은 한 가지 언어로 작성될 가능성이 높습니다. 검색 엔진에서는 queryLanguage, 언어 분석기 및 콘텐츠가 구성된 언어의 호환성을 확인하지 않으므로 잘못된 결과가 생성되지 않도록 적절히 쿼리 범위를 지정해야 합니다.

## <a name="next-steps"></a>다음 단계

+ [의미론적 순위 지정 및 캡션 호출](semantic-how-to-query-request.md)
+ [기본 쿼리 만들기](search-query-create.md)
+ [전체 Lucene 쿼리 구문 사용](query-Lucene-syntax.md)
+ [단순 쿼리 구문 사용](query-simple-syntax.md)
+ [의미 체계 검색 개요](semantic-search-overview.md)