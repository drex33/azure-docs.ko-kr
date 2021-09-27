---
title: 언어 및 텍스트 처리용 분석기
titleSuffix: Azure Cognitive Search
description: 인덱스의 검색 가능한 텍스트 필드에 분석기를 할당하여 사용자 지정, 미리 정의 또는 언어 특정 대안으로 기본 표준 Lucene을 바꿉니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/08/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 68b6f6794a690313648dfaaaaf49fdd3150b6171
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124735316"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Azure Cognitive Search의 텍스트 처리용 분석기

*분석기는* 인덱싱 및 쿼리 실행 중에 문자열을 처리하는 [전체 텍스트 검색 엔진의](search-lucene-query-architecture.md) 구성 요소입니다. 텍스트 처리(어휘 분석이라고도 함)는 변환적이며 다음과 같은 작업을 통해 문자열을 수정합니다.

+ 필수적이지 않은 단어(중지 단어) 및 문장 부호를 제거합니다.
+ 구와 하이픈을 넣은 단어를 구성 요소 부분으로 분할합니다.
+ 단어를 소문자로 처리하거나 대문자로 처리합니다.
+ 스토리지 효율성을 유지하고 시제에 관계 없이 일치 항목을 찾을 수 있도록 하기 위해 단어를 기본 어근 형식으로 줄입니다.

전체 텍스트 검색을 나타내는 "검색 가능"으로 표시된 `Edm.String` 필드가 분석됩니다. 

이 구성의 필드에 대해 토큰을 만들 때 인덱싱하는 동안 분석이 수행된 다음 쿼리가 구문 분석되고 엔진이 일치하는 토큰을 검색할 때 쿼리 실행 중에 다시 분석이 수행됩니다. 동일한 분석기가 인덱싱 및 쿼리 둘 다에 사용되는 경우 일치할 가능성이 높지만 요구 사항에 따라 각 워크로드에 대한 분석기를 독립적으로 설정할 수 있습니다.

필터 또는 유사 항목 검색과 같은 전체 텍스트 검색이 *아닌* 쿼리 형식은 쿼리 쪽에서 분석 단계를 거치지 않습니다. 대신, 파서는 일치의 기준으로 제공하는 패턴을 사용하여 해당 문자열을 검색 엔진으로 직접 보냅니다. 일반적으로 이러한 쿼리 형식에는 패턴 일치가 작동하기 위해 전체 문자열 토큰이 필요합니다. 인덱싱 중에 전체 용어 토큰을 확인하려면 [사용자 지정 분석기](index-add-custom-analyzers.md)가 필요할 수 있습니다. 쿼리 용어를 분석하는 시기와 이유에 대한 자세한 내용은 [Azure Cognitive Search의 전체 텍스트 검색](search-lucene-query-architecture.md)을 참조하세요.

어휘 분석에 대한 추가 배경 정보는 다음 비디오 클립에서 확인할 수 있습니다.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=132&end=189]

## <a name="default-analyzer"></a>기본 분석기  

Azure Cognitive Search 분석기는 검색 가능으로 표시된 모든 문자열 필드에 대해 자동으로 호출됩니다. 

기본적으로 Azure Cognitive Search는 [Apache Lucene 표준 분석기(표준 Lucene)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html)를 사용하며, ["유니코드 텍스트 구분"](https://unicode.org/reports/tr29/) 규칙에 따라 텍스트를 요소로 분리합니다. 또한 표준 분석기에서는 모든 문자를 소문자 형식으로 변환합니다. 인덱싱 및 쿼리 처리 중에는 인덱싱된 문서와 검색 용어 둘 다에 대해 분석을 수행합니다.  

필드별로 기본 분석기를 재정의할 수 있습니다. 언어 처리를 위한 [언어 분석기](index-add-language-analyzers.md), [사용자 지정 분석기](index-add-custom-analyzers.md) 또는 [사용 가능한 분석기 목록](index-add-custom-analyzers.md#built-in-analyzers)의 기본 제공 분석기를 대신 사용할 수 있습니다.

## <a name="types-of-analyzers"></a>분석기 유형

다음 목록에서는 Azure Cognitive Search에서 사용 가능한 분석기를 설명합니다.

| 범주 | Description |
|----------|-------------|
| [표준 Lucene 분석기](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | 기본값 사양 또는 구성이 필요하지 않습니다. 이 범용 분석기는 다양한 언어와 시나리오에서 잘 작동합니다.|
| 기본 제공 분석기 | 있는 그대로 사용되며 이름으로 참조됩니다. 언어 및 언어 중립적의 두 가지 유형이 있습니다. </br></br>[특수(언어 중립적) 분석기](index-add-custom-analyzers.md#built-in-analyzers)는 텍스트 입력에 특수 처리 또는 최소한의 처리가 필요할 때 사용됩니다. 이 범주에 속하는 분석기 예제에는 **Asciifolding**, **키워드**, **패턴**, **단순**, **중지**, **공백** 이 포함됩니다. </br></br>[언어 분석기](index-add-language-analyzers.md)는 개별 언어에 대해 풍부한 언어 지원이 필요할 때 사용됩니다. Azure Cognitive Search는 35개의 Lucene 언어 분석기 및 50개의 Microsoft 자연어 처리 분석기를 지원합니다. |
|[사용자 지정 분석기](/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | 기존 요소를 결합한 사용자 정의 구성을 말하며, 토크나이저(필수) 하나와 여러 선택적 필터(char 또는 토큰)로 구성됩니다.|

**패턴** 또는 **중지** 같은 일부 기본 제공 분석기는 제한된 구성 옵션 세트를 지원합니다. 이러한 옵션을 설정하려면 기본 제공 분석기와 [기본 제공 분석기](index-add-custom-analyzers.md#built-in-analyzers)에 설명된 대체 옵션 중 하나로 구성되는 사용자 지정 분석기를 만듭니다. 다른 사용자 지정 구성과 마찬가지로, Lucene 패턴 분석기와 구분할 수 있도록 구성의 이름(예: *myPatternAnalyzer*)을 입력합니다.

## <a name="how-to-specify-analyzers"></a>분석기를 지정하는 방법

분석기 설정은 선택 사항입니다. 일반적으로 기본 표준 Lucene 분석기를 사용하여 수행 방법을 확인하세요. 쿼리가 예상한 결과를 반환하지 못하는 경우 다른 분석기로 전환하는 것이 적합합니다.

1. [인덱스](/rest/api/searchservice/create-index)에서 필드 정의를 만들 때 "analyzer" 속성을 [기본 제공 분석기](index-add-custom-analyzers.md#built-in-analyzers)(예: **키워드**), [언어 분석기](index-add-language-analyzers.md)(예: `en.microsoft`) 또는 사용자 지정 분석기(동일한 인덱스 스키마에 정의) 중 하나로 설정합니다.  
 
   ```json
     "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
   ```

   [언어 분석기](index-add-language-analyzers.md)를 사용하는 경우 "analyzer" 속성을 사용하여 지정해야 합니다. "SearchAnalyzer" 및 "indexAnalyzer" 속성은 언어 분석기에 적용되지 않습니다.

1. 또는 워크로드마다 분석기를 다양하게 지정하려면 "indexAnalyzer" 및 "searchAnalyzer"를 설정합니다. 이러한 속성은 함께 설정되고 "analyzer" 속성을 대체합니다(이 속성은 null이어야 함). 이러한 작업 중 하나에서 다른 작업에는 필요 없는 특정 변환이 필요한 경우 인덱싱 및 쿼리에 다른 분석기를 사용할 수 있습니다.

   ```json
     "fields": [
    {
      "name": "ProductGroup",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "indexAnalyzer": "keyword",
      "searchAnalyzer": "standard"
    },
   ```

1. 단, 사용자 지정 분석기의 경우 인덱스의 **[분석기]** 섹션에 항목을 만든 다음, 앞의 두 단계 중 하나에 따라 사용자 지정 분석기를 필드 정의에 할당합니다. 자세한 내용은 [인덱스 만들기](/rest/api/searchservice/create-index) 및 [사용자 지정 분석기 추가](index-add-custom-analyzers.md)를 참조하세요.

## <a name="when-to-add-analyzers"></a>분석기를 추가하는 시기

분석기를 추가하고 할당하기에 가장 좋은 시기는 인덱스를 삭제하고 다시 만드는 작업이 일상적으로 진행되는 능동적 개발 단계입니다.

분석기는 용어를 토큰화하는 데 사용되므로 필드를 만들 때 분석기를 할당해야 합니다. 실제로 이미 생성된 필드에 analyzer 또는 indexAnalyzer를 할당하는 것은 허용되지 않습니다(그렇지만 인덱스에 영향을 주지 않고 언제든지 searchAnalyzer 속성을 변경할 수 있음).

기존 필드의 분석기를 변경하려면 전체 인덱스(개별 필드를 다시 작성할 수 없음)를 삭제하고 다시 만들어야 합니다. 프로덕션 환경의 인덱스인 경우 새 분석기 할당으로 새 필드를 만들어 다시 작성을 지연하고, 기존 필드 대신 사용할 수 있습니다. [인덱스 업데이트](/rest/api/searchservice/update-index)를 사용하여 새 필드를 통합하고 [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents)를 사용하여 새 필드를 채웁니다. 나중에 계획된 인덱스 서비스의 일환으로 인덱스를 정리하여 오래된 필드를 제거할 수 있습니다.

기존 인덱스에 새 필드를 추가하려면 [인덱스 업데이트](/rest/api/searchservice/update-index)를 호출하여 필드를 추가하고 [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents)를 호출하여 해당 필드를 채웁니다.

이 오류를 방지하려면 사용자 지정 분석기를 기존 인덱스에 추가하도록 [인덱스 업데이트](/rest/api/searchservice/update-index)에서 "allowIndexDowntime" 플래그를 전달합니다.

*"가동 중지 시간이 발생하므로 인덱스 업데이트가 허용되지 않습니다. 기존 인덱스에 새 분석기, 토크나이저, 토큰 필터 또는 문자 필터를 추가하려면 인덱스 업데이트 요청에서 'allowIndexDowntime' 쿼리 매개 변수를 'true'로 설정하세요. 이 작업 중에 인덱스가 몇 초 이상 오프라인 상태가 되면 인덱싱 및 쿼리 요청이 실패합니다. 인덱스의 성능 및 쓰기 가용성은 인덱스를 업데이트한 후 몇 분 동안, 인덱스가 아주 큰 경우에는 더 긴 시간 동안 제대로 작동하지 않을 수 있습니다."*

## <a name="recommendations-for-working-with-analyzers"></a>분석기 사용에 대한 추천

이 섹션에서는 분석기 작동 방식에 대한 조언을 제공합니다.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>특정 요구 사항이 없는 경우 읽기 쓰기에 대해 하나의 분석기

Azure Cognitive Search는 추가적인 indexAnalyzer 및 searchAnalyzer 필드 속성을 통해 인덱싱 및 검색에 서로 다른 분석기를 지정할 수 있습니다. 분석기를 지정하지 않으면 analyzer 속성을 통해 설정된 분석기가 인덱싱과 검색에 모두 사용됩니다. 분석기를 지정하지 않으면 기본 표준 Lucene 분석기가 사용됩니다.

특정 요구 사항이 따로 지정되지 않으면 인덱싱 및 쿼리 모두에 대해 동일한 분석기를 사용하는 것이 일반적인 규칙입니다. 철저하게 테스트해야 합니다. 검색 및 인덱싱 시간에서 텍스트 처리가 다른 경우 검색 및 인덱싱 분석기 구성이 일치하지 않으면 쿼리 용어와 인덱싱된 용어 사이에 불일치가 발생할 위험을 떠안게 됩니다.

### <a name="test-during-active-development"></a>활성 개발 중 테스트

표준 분석기를 재정의하려면 인덱스를 다시 빌드해야 합니다. 가능하면 프로덕션 환경에 인덱스를 이동하기 전에 활성 개발 중 사용할 분석기를 결정합니다.

### <a name="inspect-tokenized-terms"></a>토큰화된 용어 검사

검색에서 예상되는 결과가 반환되지 않은 경우, 쿼리에 입력된 용어와 인덱스의 토큰화된 용어의 토큰이 같지 않기 때문일 가능성이 큽니다. 토큰이 같지 않으면 일치하는 결과가 반환되지 않습니다. 토크나이저 출력을 검사할 때는 조사 도구로 [분석 API](/rest/api/searchservice/test-analyzer)를 사용하는 것이 좋습니다. 응답은 해당 분석기에 의해 생성되는 토큰으로 구성됩니다.

<a name="examples"></a>

## <a name="rest-examples"></a>REST 예제

다음 예제에서는 몇 가지 주요 시나리오에 대한 분석기 정의를 보여줍니다.

+ [사용자 지정 분석기 예제](#Custom-analyzer-example)
+ [필드에 분석기 할당 예제](#Per-field-analyzer-assignment-example)
+ [인덱싱 및 검색에 분석기 혼용](#Mixing-analyzers-for-indexing-and-search-operations)
+ [언어 분석기 예제](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>사용자 지정 분석기 예제

이 예제에서는 사용자 지정 옵션을 사용하는 분석기 정을 보여줍니다. char 필터, 토크나이저 및 토큰 필터에 대한 사용자 지정 옵션은 명명된 구문으로 개별 지정되고 분석기 정의에서 참조됩니다. 미리 정의된 요소도 그대로 사용되며 이름별로 참조하기만 하면 됩니다.

이 예제를 진행합니다.

+ 분석기는 검색 가능한 필드에 대한 필드 클래스의 속성입니다.

+ 사용자 지정 분석기는 인덱스 정의의 일부입니다. 가볍게 사용자 지정(예: 한 필터에서 단일 옵션 사용자 지정)되거나 여러 위치에서 사용자 지정될 수도 있습니다.

+ 이 경우에 사용자 지정 분석기는 "my_analyzer"이며, 사용자 지정된 표준 토크나이저 "my_standard_tokenizer" 및 소문자 필터와 사용자 지정된 asciifolding 필터 "my_asciifolding"라는 두 개의 토큰 필터를 차례로 사용합니다.

+ 또한 2개의 사용자 지정 문자 필터 "map_dash" 및 "remove_whitespace"를 정의합니다. 첫 번째 필터는 모든 대시를 밑줄로 바꾸고 두 번째 필터는 모든 공백을 제거합니다. 공백은 매핑 규칙에서 UTF-8로 인코딩되어야 합니다. 문자 필터는 토큰화 전에 적용되며 결과 토큰에 영향을 미칩니다(표준 토크나이저는 대시 및 공백으로 분리되지만 밑줄은 사용하지 않습니다).

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>필드별 분석기 할당 예제

표준 분석기는 기본값입니다. 패턴 분석기와 같은 다른 미리 정의된 분석기로 기본값을 바꾸려 한다고 가정합니다. 사용자 지정 옵션을 설정하지 않은 경우 필드 정의에서 이름으로 지정하기만 하면 됩니다.

"analyzer" 요소에서 필드 기준으로 표준 분석기를 재정의합니다. 전역 재정의가 없습니다. 이 예제에서 `text1`는 패턴 분석기를 사용하고 분석기를 지정하지 않는 `text2`는 기본값을 사용합니다.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
```

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>인덱싱 및 검색 작업에 분석기 혼용

API는 인덱싱 및 검색에 대해 다른 분석기를 지정하기 위한 추가 인덱스 특성을 포함합니다. searchAnalyzer 및 indexAnalyzer 특성은 쌍으로 지정해야 하며, 단일 analyzer 특성을 대체합니다.


```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
```

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>언어 분석기 예제

다른 언어의 문자열을 포함하는 필드는 언어 분석기를 사용할 수 있지만 다른 필드는 기본값을 그대로 둡니다(또는 다른 미리 정의되거나 사용자 지정된 분석기 사용). 언어 분석기를 사용하는 경우 인덱싱 및 검색 작업에 사용해야 합니다. 언어 분석기를 사용하는 필드는 인덱싱 및 검색에 다른 분석기를 가질 수 없습니다.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
```

## <a name="c-examples"></a>C# 예제

.NET SDK 코드 샘플을 사용하는 경우 다음 예제를 추가하여 분석기를 사용하거나 구성할 수 있습니다.

+ [기본 제공 분석기 할당](#Assign-a-language-analyzer)
+ [분석기 구성](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>언어 분석기 할당

구성 없이 있는 그대로 사용되는 모든 분석기는 필드 정의에 지정됩니다. 인덱스의 **[분석기]** 섹션에 항목을 만들 필요가 없습니다. 

언어 분석기는 있는 그대로 사용됩니다. 이 분석기를 사용하려면 [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer)를 호출하고 [LexicalAnalyzerName](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername) 유형을 지정하여 Azure Cognitive Search에서 지원되는 텍스트 분석기를 제공합니다.

사용자 지정 분석기는 필드 정의에 유사하게 지정되지만 이 작업을 수행하려면 다음 섹션에서 설명하는 것처럼 인덱스 정의에 분석기를 지정해야 합니다.

```csharp
    public partial class Hotel
    {
       . . . 
        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
        public string Description { get; set; }

        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
        [JsonPropertyName("Description_fr")]
        public string DescriptionFr { get; set; }

        [SearchableField(AnalyzerName = "url-analyze")]
        public string Url { get; set; }
      . . .
    }
```

<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>사용자 지정 분석기 정의

사용자 지정 또는 구성이 필요한 경우에는 분석기 구문을 인덱스에 추가합니다. 정의한 후에는 앞의 예제에서 설명한 대로 필드 정의에 추가할 수 있습니다.

[CustomAnalyzer](/dotnet/api/azure.search.documents.indexes.models.customanalyzer) 개체를 만듭니다. 사용자 지정 분석기는 알려진 토크나이저, 0개 이상의 토큰 필터 및 0개 이상의 문자 필터 이름으로 이루어진 사용자 정의 조합입니다.

+ [CustomAnalyzer.Tokenizer](/dotnet/api/microsoft.azure.search.models.customanalyzer.tokenizer)
+ [CustomAnalyzer.TokenFilters](/dotnet/api/microsoft.azure.search.models.customanalyzer.tokenfilters)
+ [CustomAnalyzer.CharFilters](/dotnet/api/microsoft.azure.search.models.customanalyzer.charfilters)

다음 예제에서는 [uax_url_email 토크나이저](/dotnet/api/microsoft.azure.search.models.customanalyzer.tokenizer) 및 [소문자 토큰 필터](/dotnet/api/microsoft.azure.search.models.tokenfiltername.lowercase)를 사용하는 "url-analyze"라는 사용자 지정 분석기를 만듭니다.

```csharp
private static void CreateIndex(string indexName, SearchIndexClient adminClient)
{
   FieldBuilder fieldBuilder = new FieldBuilder();
   var searchFields = fieldBuilder.Build(typeof(Hotel));

   var analyzer = new CustomAnalyzer("url-analyze", "uax_url_email")
   {
         TokenFilters = { TokenFilterName.Lowercase }
   };

   var definition = new SearchIndex(indexName, searchFields);

   definition.Analyzers.Add(analyzer);

   adminClient.CreateOrUpdateIndex(definition);
}
```

추가 예제는 [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs)를 참조하세요.

## <a name="next-steps"></a>다음 단계

쿼리 실행에 대한 자세한 설명은 [Azure Cognitive Search의 전체 텍스트 검색](search-lucene-query-architecture.md)에서 찾을 수 있습니다. 이 문서에서는 화면에서 직관적이지 않은 것처럼 보일 수 있는 동작을 설명하는 예제를 사용합니다.

분석기에 대한 자세한 내용은 다음 문서를 참조하세요.

+ [언어 분석기 추가](index-add-language-analyzers.md)
+ [사용자 지정 분석기 추가](index-add-custom-analyzers.md)
+ [검색 인덱스 만들기](search-what-is-an-index.md)
+ [다중 언어 인덱스 만들기](search-language-support.md)