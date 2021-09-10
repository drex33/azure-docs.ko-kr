---
title: 의미 체계 쿼리 만들기
titleSuffix: Azure Cognitive Search
description: 의미 체계 쿼리 유형을 설정하여 딥 러닝 모델을 쿼리 처리에 연결하고, 의도와 컨텍스트를 검색 순위 및 관련성의 일부로 유추합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: e3ae63b202d826e48789bd8d15a197048d5566b7
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122567585"
---
# <a name="create-a-query-that-invokes-semantic-ranking-and-returns-semantic-captions"></a>의미론적 순위를 호출하고 의미론적 캡션을 반환하는 쿼리 만들기

> [!IMPORTANT]
> 의미 체계 검색은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 따라 공개 미리 보기로 제공됩니다. Azure Portal, 미리 보기 REST API, 베타 SDK를 통해 사용할 수 있습니다. 이러한 기능에는 비용이 청구될 수 있습니다. 자세한 내용은 [가용성 및 가격 책정](semantic-search-overview.md#availability-and-pricing)을 참조하세요.

의미 체계 검색은 결과 집합에 대한 의미 체계 순위 지정 알고리즘을 호출하고 의미 체계 캡션(및 선택 사항으로 [의미 체계 답변](semantic-answers.md))을 반환하고 가장 관련성이 큰 용어와 구를 강조 표시하는 Azure Cognitive Search의 프리미엄 기능입니다. ‘의미 체계’ 쿼리 유형을 사용하여 작성된 쿼리 요청에서 캡션과 답변이 모두 반환됩니다.

캡션과 답변은 검색 문서의 텍스트에서 그대로 추출됩니다. 의미 체계 하위 시스템은 콘텐츠의 어떤 부분에 캡션이나 답변의 특징이 포함될지 결정하지만 새 문장이나 구를 작성하지는 않습니다. 이러한 이유로 설명이나 정의가 포함된 내용은 의미 체계 검색에 가장 잘 맞습니다.

## <a name="prerequisites"></a>필수 구성 요소

+ 표준 계층(S1, S2, S3)의 Cognitive Search 서비스. 해당 서비스는 미국 중북부, 미국 서부, 미국 서부 2, 미국 동부 2, 북유럽, 서유럽 중 한 곳에서 사용해야 합니다. 이러한 지역 중 한 곳에서 기존에 S1 이상의 서비스를 사용하는 경우 새 서비스를 만들지 않고도 미리 보기에 등록할 수 있습니다.

+ [미리 보기에 등록하세요](https://aka.ms/SemanticSearchPreviewSignup). 예상 소요 시간은 영업일 기준 약 2일입니다.

+ [지원되는 언어](/rest/api/searchservice/preview-api/search-documents#queryLanguage)로 된 콘텐츠가 있는 기존 검색 인덱스 의미 체계 검색은 정보 또는 설명이 포함된 콘텐츠에서 가장 잘 작동합니다.

+ 쿼리 전송을 위한 검색 클라이언트

  검색 클라이언트는 쿼리 요청에 대한 미리 보기 REST API를 지원해야 합니다. 미리 보기 API에 대한 REST 호출을 수행하는 [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md) 또는 코드를 사용할 수 있습니다. Azure Portal에서 [검색 탐색기](search-explorer.md)를 사용하여 의미 체계 쿼리를 제출할 수도 있습니다. [Azure.Search.Documents 11.3.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.3.0-beta.2)를 사용할 수도 있습니다.

+ [쿼리 요청](/rest/api/searchservice/preview-api/search-documents)에는 `queryType=semantic` 및 이 문서에서 설명하는 기타 매개 변수가 포함되어 있어야 합니다.

## <a name="whats-a-semantic-query-type"></a>의미 체계 쿼리 형식이란?

Cognitive Search에서 쿼리는 쿼리 처리 및 응답의 형태를 결정하는 매개 변수가 있는 요청입니다. ‘의미 체계 쿼리’에는 일치 결과에 대한 컨텍스트와 의미를 평가하여 관련성이 더 높은 일치 항목을 맨 위로 승격하고 의미 체계 답변 및 캡션을 반환할 수 있는 순위 재지정 모델을 호출하는 [매개 변수가 있습니다](#query-using-rest).

다음 요청은 최소한의 의미 체계 쿼리(답변 제외)를 나타냅니다.

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

Cognitive Search의 모든 쿼리와 마찬가지로 요청은 단일 인덱스의 문서 컬렉션을 대상으로 합니다. 또한 의미 체계 쿼리는 구문 분석, 분석, 검색, 채점을 의미 없는 쿼리로 동일한 시퀀스로 수행합니다. 

차이점은 관련성 및 채점에 따릅니다. 이 미리 보기 릴리스에서 정의된 바와 같이 의미 체계 쿼리는 기본 유사성 순위 알고리즘에 따라 할당된 점수가 아닌 의미 체계 순위매기기에 따라 가장 관련성이 높은 것으로 간주되는 일치 항목을 표시하는 방법을 제공하는 의미 체계 언어 모델을 사용하여 다시 순위를 평가한 *결과* 입니다.

초기 결과의 상위 50개 일치 항목만 의미 체계 순위를 지정할 수 있으며, 모든 결과의 응답에는 캡션이 포함됩니다. 필요에 따라 요청에 대한 **`answer`** 매개 변수를 지정하여 잠재적인 답변을 추출할 수 있습니다. 자세한 내용은 [의미 체계 답변](semantic-answers.md)을 참조하십시오.

## <a name="query-in-azure-portal"></a>Azure Portal에서 쿼리

[검색 탐색기](search-explorer.md)가 의미 체계 쿼리 옵션을 포함하도록 업데이트되었습니다. 다음 단계를 완료하면 이러한 옵션이 포털에 표시됩니다.

1. 미리 보기가 활성화된 검색 서비스에서 `https://portal.azure.com/?feature.semanticSearch=true` 구문을 사용하여 포털을 엽니다.

1. 개요 페이지 상단의 **검색 탐색기** 를 클릭합니다.

1. [지원되는 언어](/rest/api/searchservice/preview-api/search-documents#queryLanguage)의 콘텐츠가 있는 인덱스를 선택합니다.

1. 검색 탐색기에서 의미 체계 쿼리, searchFields, 맞춤법 수정을 사용하도록 설정하는 쿼리 옵션을 설정합니다. 필요한 쿼리 매개 변수를 쿼리 문자열에 붙여넣을 수도 있습니다.

:::image type="content" source="./media/semantic-search-overview/search-explorer-semantic-query-options.png" alt-text="Search 탐색기에서 쿼리 옵션" border="true":::

## <a name="query-using-rest"></a>REST를 사용하는 쿼리

[문서 검색(REST 미리 보기)](/rest/api/searchservice/preview-api/search-documents)을 사용하여 프로그래매틱 방식으로 요청을 작성합니다. 응답에는 캡션 및 강조 표시가 자동으로 포함됩니다. 응답에서 맞춤법 수정 또는 답변을 원하는 경우 요청에 **`speller`** 또는 **`answers`** 를 추가합니다.

다음 예시에서는 [hotels-sample-index](search-get-started-portal.md)를 사용하여 맞춤법 검사, 의미 체계 답변, 캡션이 포함된 의미 체계 쿼리 요청을 만듭니다.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

다음 테이블은 의미 체계 쿼리에서 사용되는 매개 변수를 요약합니다. 요청 내 모든 매개 변수 목록은 [문서 검색(REST 미리 보기)](/rest/api/searchservice/preview-api/search-documents)을 참조하세요.

| 매개 변수 | 형식 | Description |
|-----------|-------|-------------|
| queryType | String | 유효한 값은 simple, full, semantic입니다. 의미 체계 쿼리에는 "semantic" 값이 필요합니다. |
| queryLanguage | String | 의미 체계 쿼리에 필요합니다. 지정한 어휘집은 의미 체계 순위 지정, 캡션, 답변, 맞춤법 검사에 동일하게 적용됩니다. 자세한 내용은 [지원되는 언어(REST API 참조)](/rest/api/searchservice/preview-api/search-documents#queryLanguage)를 참조하세요. |
| searchFields | String | 쉼표로 구분된 검색 가능한 필드입니다. 캡션 및 답변을 추출할 의미 체계 순위가 발생하는 필드를 지정합니다. </br></br>단순 및 전체 쿼리 유형과 달리 필드가 나열되는 순서에 따라 우선 순위가 결정됩니다. 자세한 사용 지침은 [2단계: searchFields 설정](#searchfields)을 참조하세요. |
| speller | String | 검색 엔진에 도달하기 전에 철자가 틀린 용어를 수정하는 선택적 매개 변수(의미 체계 쿼리에 한정되지 않음)입니다. 자세한 내용은 [쿼리에 맞춤법 수정 추가](speller-how-to-add.md)를 참조하세요. |
| answers |String | 의미 체계 답변이 결과에 포함될지 여부를 지정하는 선택적 매개 변수입니다. 현재 "extractive"만 구현되어 있습니다. 답변은 최대 10개를 반환하도록 구성할 수 있습니다. 기본값은 1개입니다. 이 예제에서는 `extractive\|count-3`의 세 가지 답변을 보여 줍니다. 자세한 내용은 [의미 체계 답변 반환](semantic-answers.md)을 참조하세요.|

### <a name="formulate-the-request"></a>요청 작성

이 섹션에서는 쿼리 작성을 단계별로 안내합니다.

#### <a name="step-1-set-querytype-and-querylanguage"></a>1단계: queryType 및 queryLanguage 설정

REST에 다음 매개 변수를 추가합니다. 두 매개 변수는 모두 필수입니다.

```json
"queryType": "semantic",
"queryLanguage": "en-us",
```

queryLanguage는 [지원되는 언어](/rest/api/searchservice/preview-api/search-documents#queryLanguage)여야 하며 인덱스 스키마의 필드 정의에 할당된 [언어 분석기](index-add-language-analyzers.md)와 일치해야 합니다. 예를 들어 프랑스어 분석기(예: ‘fr.microsoft’ 또는 ‘fr.lucene’)를 사용하여 프랑스어 문자열을 인덱싱했다면 queryLanguage도 프랑스어여야 합니다.

쿼리 요청에서 [맞춤법 수정](speller-how-to-add.md)도 사용하는 경우 queryLanguage 설정은 맞춤법 검사기, 답변, 캡션에 동일하게 적용됩니다. 개별 파트에 대해 재정의하지 않습니다. 맞춤법 검사에서는 [더 적은 언어](speller-how-to-add.md#supported-languages)를 지원하므로, 맞춤법 검사를 사용하는 경우 queryLanguage를 해당 목록 중 하나로 설정해야 합니다.

검색 인덱스의 콘텐츠는 여러 언어로 구성될 수 있지만, 쿼리 입력은 한 가지 언어로 작성될 가능성이 높습니다. 검색 엔진에서는 queryLanguage, 언어 분석기 및 콘텐츠가 구성된 언어의 호환성을 확인하지 않으므로 잘못된 결과가 생성되지 않도록 적절히 쿼리 범위를 지정해야 합니다.

<a name="searchfields"></a>

#### <a name="step-2-set-searchfields"></a>2단계: searchFields 설정

요청에 searchFields를 추가합니다. 선택 사항이지만 적극적으로 권장됩니다.

```json
"searchFields": "HotelName,Category,Description",
```

searchFields 매개 변수는 쿼리에 대해 "의미 유사성"을 평가할 구절을 식별하는 데 사용됩니다. 미리 보기의 경우, 모델에 어떤 필드의 처리가 가장 중요한지에 대한 힌트가 필요하기 때문에 searchFields를 비워 두지 않는 것이 좋습니다.

다른 매개 변수와 달리 searchFields는 새로운 것이 아닙니다. 단순 또는 전체 Lucene 쿼리를 위해 기존 코드에서 searchFields를 이미 사용하고 있을 수 있습니다. 이러한 경우에는 의미 체계 쿼리 유형으로 전환할 때 필드 순서를 확인할 수 있도록 매개 변수가 사용되는 방식을 확인합니다.

##### <a name="allowed-data-types"></a>허용되는 데이터 형식

SearchFields 설정 시, 다음의 [지원되는 데이터 형식](/rest/api/searchservice/supported-data-types)의 필드만 선택합니다. 잘못된 필드를 포함하는 경우에는 오류가 발생하지 않지만 이러한 필드는 의미 체계 순위 지정에 사용되지 않습니다.

| 데이터 형식 | hotels-sample-index의 예시 |
|-----------|----------------------------------|
| Edm.String | HotelName, Category, Description |
| Edm.ComplexType | Address.StreetNumber, Address.City, Address.StateProvince, Address.PostalCode |
| Collection(Edm.String) | 태그(쉼표로 구 된 문자열 목록) |

##### <a name="order-of-fields-in-searchfields"></a>SearchFields의 필드 순서

의미 체계 순위매기기는 적절한 응답 시간을 제공하면서 처리할 수 있는 콘텐츠의 양을 제한하므로 필드 순서가 중요합니다. 목록의 시작 부분에 있는 필드의 콘텐츠가 포함될 가능성이 큽니다. 최대한도에 도달하면 끝부분의 콘텐츠가 잘릴 수 있습니다. 자세한 내용은 [의미 체계 순위 지정 시 전처리](semantic-ranking.md#pre-processing)를 참조하세요.

+ 필드를 하나만 지정하는 경우 문서의 주요 콘텐츠와 같이 의미 체계 쿼리에 대한 답변을 찾을 수 있는 설명 필드를 선택합니다. 

+ searchFields 내 두 개 이상 필드의 경우:

  + 첫 번째 필드는 제목 또는 이름과 같이 반드시 간결하고 25개 단어보다 짧은 문자열이 좋습니다.

  + 인덱스에 `www.domain.com/name-of-the-document-and-other-details`와 같이 사용자가 판독할 수 있는(`www.domain.com/?id=23463&param=eis`와 같이 머신에 중점을 두지 않음) URL 필드가 있는 경우 목록의 두 번째(또는 간결한 제목 필드가 없는 경우 첫 번째)에 배치합니다.

  + 이러한 필드와 문서의 주요 콘텐츠와 같이 의미 체계 쿼리에 대한 답변을 찾을 수 있는 다른 설명 필드에 따릅니다.

#### <a name="step-3-remove-or-bracket-query-features-that-bypass-relevance-scoring"></a>3단계: 관련성 점수 매기기를 바이패스하는 쿼리 기능을 제거하거나 대괄호로 묶습니다.

Cognitive Search의 여러 쿼리 기능은 관련성 점수 매기기를 거치지 않으며 일부 쿼리 기능은 전체 텍스트 검색 엔진을 모두 무시합니다. 쿼리 논리에 다음 기능이 포함된 경우 결과에 대한 관련성 점수 또는 의미 체계 순위 지정을 받을 수 없습니다.

+ 필터, 모호성 검색 쿼리 및 정규식은 토큰화되지 않은 텍스트를 반복하여 콘텐츠에서 축자와 일치하는 항목을 검색합니다. 위의 모든 쿼리 형식에 대한 검색 점수는 균일한 1.0이며 의미 체계 순위 지정을 위한 의미 있는 입력을 제공하지 않습니다.

+ 특정 필드에 대한 정렬(orderBy 절)도 검색 점수 및 의미 체계 점수를 재정의합니다. 의미 체계 점수가 결과를 정렬하는 데 사용된다는 점을 고려할 때 명시적 정렬 논리를 포함하면 HTTP 400 오류가 반환됩니다.

#### <a name="step-4-add-answers"></a>4단계: 답변 추가

필요에 따라 답변을 제공하는 추가 처리를 포함하려면 "answers"을 추가합니다. 이 매개 변수에 대한 자세한 내용은 [의미 체계 답변을 지정하는 방법](semantic-answers.md)을 참조하세요.

```json
"answers": "extractive|count-3",
```

답변(및 캡션)은 searchFields에 나열된 필드에서 발견된 구절로부터 추출됩니다. 따라서 응답에서 최상의 답을 얻을 수 있도록 searchFields에 자세한 내용의 필드를 포함해야 합니다. 모든 요청에 대한 답변이 보장되지는 않습니다. 쿼리는 질문 형식으로 작성해야 하며, 내용에는 답변 형식의 텍스트가 포함되어야 합니다.

#### <a name="step-5-add-other-parameters"></a>5단계: 기타 매개 변수 추가

요청에 넣고자 하는 기타 매개 변수를 설정합니다. [speller](speller-how-to-add.md), [select](search-query-odata-select.md), count와 같은 매개 변수는 요청 및 응답의 가독성 품질을 개선합니다.

```json
"speller": "lexicon",
"select": "HotelId,HotelName,Description,Category",
"count": true,
"highlightPreTag": "<mark>",
"highlightPostTag": "</mark>",
```

응답의 캡션에 강조 표시 스타일이 적용됩니다. 기본 스타일을 사용하거나 필요에 따라 캡션에 적용되는 강조 스타일을 사용자 지정할 수 있습니다. 캡션을 사용하면 응답이 요약된 문서의 주요 구절에 강조 표시 서식이 적용됩니다. 기본값은 `<em>`입니다. 서식 유형(예: 노란색 배경)을 지정하려면 highlightPreTag와 highlightPostTag를 설정하면 됩니다.

## <a name="query-using-azure-sdks"></a>Azure SDK를 사용하여 쿼리

Azure SDK의 베타 버전에는 의미 체계 검색에 대한 지원이 포함되어 있습니다. SDK는 베타 버전이므로 설명서나 샘플은 없지만 위의 REST API 섹션에서 API 작동 방식에 대한 인사이트를 참조할 수 있습니다.

| Azure SDK | 패키지 |
|-----------|---------|
| .NET | [Azure.Search.Documents 패키지 11.3.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.3.0-beta.2)  |
| Java | [com.azure:azure-search-documents 11.4.0-beta.2](https://search.maven.org/artifact/com.azure/azure-search-documents/11.4.0-beta.2/jar)  |
| JavaScript | [azure/search-documents 11.2.0-beta.2](https://www.npmjs.com/package/@azure/search-documents/v/11.2.0-beta.2)|
| Python | [azure-search-documents 11.2.0b3](https://pypi.org/project/azure-search-documents/11.2.0b3/) |

## <a name="evaluate-the-response"></a>응답 평가

모든 쿼리와 마찬가지로 응답은 검색 가능으로 표시된 모든 필드 또는 select 매개 변수에 나열된 필드로 구성됩니다. 여기에는 원본 관련성 점수 및 요청을 작성한 방법에 따라 개수 또는 일괄 처리된 결과가 포함될 수 있습니다.

의미 체계 쿼리에서 응답에는 새 의미상 순위 관련성 점수, 일반 텍스트의 캡션 및 강조 표시, 필요에 따라 답변과 같은 추가 요소가 있습니다.

클라이언트 앱에서는 특정 필드의 전체 내용이 아니라 일치 항목에 대한 설명으로 캡션을 포함하도록 검색 페이지를 구성할 수 있습니다. 이는 검색 결과 페이지의 개별 필드가 너무 조밀한 경우 유용합니다.

위의 예제 쿼리에 대한 응답은 다음 일치 항목을 최상위 선택 항목으로 반환합니다. 캡션은 일반 텍스트 및 강조 표시된 버전과 함께 자동으로 반환됩니다. 이 특정 쿼리 및 모음에 대한 답변을 확인할 수 없기 때문에 이 예제에서 답변을 생략합니다.

```json
"@odata.count": 35,
"@search.answers": [],
"value": [
    {
        "@search.score": 1.8810667,
        "@search.rerankerScore": 1.1446577133610845,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Luxury. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Luxury. New Luxury Hotel. Be the first to stay.<strong> Bay</strong> views from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Luxury"
    },
```

## <a name="next-steps"></a>다음 단계

의미 체계 순위와 응답은 초기 결과 집합을 통해 구축됩니다. 초기 결과의 품질을 향상시키는 논리는 모두 의미 체계 검색으로 전달됩니다. 다음 단계로 문자열을 토큰화하는 방법, 결과를 조정할 수 있는 점수 매기기 프로필 및 기본 관련성 알고리즘에 영향을 주는 분석기를 포함하여 초기 결과에 기여하는 기능을 검토합니다.

+ [텍스트 처리를 위한 분석기](search-analyzers.md)
+ [유사성 순위 알고리즘](index-similarity-and-scoring.md)
+ [점수 매기기 프로필](index-add-scoring-profiles.md)
+ [의미 체계 검색 개요](semantic-search-overview.md)
+ [의미 체계 순위 지정 알고리즘](semantic-ranking.md)
