---
title: 필터링, 패싯, 정렬에 대한 텍스트 정규화
titleSuffix: Azure Cognitive Search
description: 인덱스에서 텍스트 필드에 Normalizers를 지정하여 필터링, 패싯 및 정렬에서 엄격한 키워드 일치 동작을 사용자 지정합니다.
author: IshanSrivastava
manager: jlembicz
ms.author: ishansri
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/23/2021
ms.openlocfilehash: 9142f03f5f23e843018f7d9b702ef8da3d650758
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112578687"
---
# <a name="text-normalization-for-case-insensitive-filtering-faceting-and-sorting"></a>대/소문자를 구분하지 않는 필터링, 패싯 및 정렬에 대한 텍스트 정규화

> [!IMPORTANT] 
> 이 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 따라 공개 미리 보기로 제공됩니다. [미리 보기 REST API](/rest/api/searchservice/index-preview)에서 이 기능을 지원합니다.

Azure Cognitive Search 인덱스에서 문서를 검색하고 찾아오려면 문서의 내용과 쿼리가 일치해야 합니다. `search` 매개 변수가 사용되는 경우와 같이 일치하는 토큰을 생성하기 위해 콘텐츠를 분석하거나 `$filter`, `facets` 및 `$orderby`와 같이 엄격한 키워드 일치를 위해 그대로 사용할 수 있습니다. 이 양자택일식 접근 방식은 대부분의 시나리오에 적용되지만 전체 분석 체인을 거치지 않고 대소문자 구분, 액센트 제거, asciifolding 등과 같은 간단한 전처리가 필요한 경우에는 부족합니다.

다음 예제를 살펴보세요.

+ `$filter=City eq 'Las Vegas'`는 "Las Vegas"라는 정확한 텍스트가 포함된 문서만 반환되고 "LAS VEGAS"와 "las vegas"가 포함된 문서는 제외되며, 대소문자에 관계없이 모든 문서가 필요한 경우에는 부적합합니다.

+ `search=*&facet=City,count:5`는 동일한 도시임에도 불구하고 "Las Vegas", "LAS VEGAS" 및 "las vegas"를 고유 값으로 반환합니다.

+ `search=usa&$orderby=City`는 대소문자에 상관없이 동일한 도시를 함께 순서를 지정하려는 의도가 있더라도 "Las Vegas", "Seattle", "las vegas"와 같이 사전순으로 도시를 반환합니다. 

## <a name="normalizers"></a>Normalizers

*Normalizers* 는 키워드 일치를 위해 전처리를 담당하는 검색 엔진의 구성 요소입니다. Normalizers는 쿼리를 토큰화하지 않는다는 점을 제외하고 분석기와 유사합니다. Normalizers를 사용하여 수행할 수 있는 일부 변환은 다음과 같습니다.

+ 소문자 또는 대문자로 변환합니다.
+ Ö 또는 ê와 같은 악센트 및 분음 부호를 ASCII 해당 문자 "o" 및 "e"로 일반화합니다.
+ `-` 및 공백과 같은 문자를 사용자 지정 문자로 매핑합니다.

Normalizers는 인덱스의 텍스트 필드에 지정할 수 있으며 인덱싱 및 쿼리 실행 시 모두 적용됩니다.

## <a name="predefined-and-custom-normalizers"></a>미리 정의 및 사용자 지정된 Normalizers 

Azure Cognitive Search는 필요에 따라 사용자 지정하는 기능과 함께 일반적으로 사용되는 경우를 위해 미리 정의된 Normalizers를 지원합니다.

| 범주 | Description |
|----------|-------------|
| [미리 정의된 Normalizers](#predefined-normalizers) | 기본으로 제공되며 구성 없이 사용할 수 있습니다. |
|[사용자 지정 Normalizers](#add-custom-normalizers) | 고급 시나리오의 경우. char 및 token 필터로 구성된 기존 요소의 조합에 대한 사용자 정의 구성이 필요합니다.<sup>1</sup>|

<sup>(1)</sup> Normalizers는 항상 단일 토큰을 생성하므로 사용자 지정 Normalizers는 토크나이저를 지정하지 않습니다.

## <a name="how-to-specify-normalizers"></a>Normalizers 지정 방법

`filterable`, `sortable` 또는 `facetable` 속성 중 최소 한 개의 속성이 true로 설정된 텍스트 필드(`Edm.String` 및 `Collection(Edm.String)`)에서 필드 단위로 Normalizers를 지정할 수 있습니다. Normalizers 설정은 선택 사항이며 기본 설정은 `null`입니다. 편의를 위해 사용자 지정 항목을 구성하기 전에 미리 정의된 Normalizers를 평가하는 것이 좋습니다. 결과가 예상과 다른 경우 다른 Normalizers를 시도하세요.

새 필드가 인덱스에 추가된 경우에만 Normalizers를 지정할 수 있습니다. 인덱스를 삭제하고 다시 만드는 것이 루틴인 경우 개발 정규화 요구 사항을 사전에 평가하고 Normalizers를 초기 단계에 할당하는 것이 좋습니다. 이미 만들어진 필드에는 Normalizers를 지정할 수 없습니다.

1. [인덱스](/rest/api/searchservice/create-index)에서 필드 정의를 만드는 경우 **Normalizers** 속성을 `lowercase` 등의 [미리 정의된 Normalizers](#predefined-normalizers) 또는 사용자 지정 Normalizers(동일한 인덱스 스키마에 정의됨) 중 하나로 설정합니다.  
 
   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "filterable": true,
      "analyzer": "en.microsoft",
      "normalizer": "lowercase"
      ...
    },
   ```

2. 사용자 지정 Normalizers은 먼저 인덱스의 **[normalizers]** 섹션에서 정의한 후 이전 단계에 표시된 대로 필드 정의에 할당해야 합니다. 자세한 내용은 [인덱스 만들기](/rest/api/searchservice/create-index) 및 [사용자 지정 Normalizers 추가](#add-custom-normalizers)를 참조하세요.


   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "normalizer": "my_custom_normalizer"
    },
   ```

 
> [!NOTE]
> 기존 필드의 Normalizers를 변경하려면 인덱스를 완전히 다시 빌드해야 합니다(개별 필드는 다시 빌드할 수 없음).

인덱스를 다시 빌드하는 데 비용이 많이 드는 프로덕션 인덱스에 대한 좋은 해결 방법은 기존 필드와 동일하지만 새 Normalizers를 포함하는 새 필드를 만든 다음 기존 필드 대신 사용하는 것입니다. [인덱스 업데이트](/rest/api/searchservice/update-index)를 사용하여 새 필드를 통합하고 [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents)를 사용하여 새 필드를 채웁니다. 나중에 계획된 인덱스 서비스의 일환으로 인덱스를 정리하여 오래된 필드를 제거할 수 있습니다.

## <a name="add-custom-normalizers"></a>사용자 지정 Normalizers 추가

사용자 지정 Normalizers는 인덱스 스키마 내에 정의되며 필드 속성을 사용하여 지정할 수 있습니다. 사용자 지정 Normalizers의 정의에는 이름, 형식, 하나 이상의 문자 필터 및 토큰 필터가 포함됩니다. 문자 필터 및 토큰 필터는 사용자 지정 Normalizers에 대한 빌딩 블록이며 텍스트 처리를 담당합니다. 이러한 필터는 왼쪽에서 오른쪽으로 적용됩니다.

 `token_filter_name_1`은 토큰 필터의 이름이며 `char_filter_name_1` 및 `char_filter_name_2`는 문자 필터의 이름입니다(유효한 값은 아래 [지원되는 토큰 필터](#supported-token-filters) 및 문자 필터 표 참조).

Normalizers 정의는 더 큰 인덱스의 일부입니다. 나머지 인덱스에 대한 내용은 [인덱스 만들기 API](/rest/api/searchservice/create-index)를 참조하세요.

```
"normalizers":(optional)[
   {
      "name":"name of normalizer",
      "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenFilters":[
         "token_filter_name_1
      ]
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name_1",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name_1",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

사용자 지정 Normalizers는 인덱스를 만드는 중에 또는 나중에 기존 항목을 업데이트하여 추가할 수 있습니다. 기존 인덱스에 사용자 지정 Normalizers를 추가하려면 **allowIndexDowntime** 플래그가 [업데이트 인덱스](/rest/api/searchservice/update-index)에서 지정되어야 하며 이로 인해 해당 인덱스를 몇 초 동안 사용할 수 없게 됩니다.

## <a name="normalizers-reference"></a>Normalizers 참조

### <a name="predefined-normalizers"></a>미리 정의된 Normalizers
|**이름**|**설명 및 옵션**|  
|-|-|  
|표준| asciifolding으로 뒤에 이어지는 텍스트 소문자로 바꿉니다.|  
|소문자| 문자를 소문자로 변환합니다.|
|대문자| 문자를 대문자로 변환합니다.|
|asciifolding| 기본 라틴어 유니코드 블록에 없는 문자를 해당하는 ASCII 값으로 변환합니다(해당 ASCII 값이 있는 경우). 예를 들어 à을 a로 변경합니다.|
|elision| 토큰의 시작 부분에서 elision을 제거합니다.|

### <a name="supported-char-filters"></a>지원되는 문자 필터
문자 필터에 대한 자세한 내용은 [문자 필터 참조](index-add-custom-analyzers.md#CharFilter)를 참조하세요.
+ [mapping](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)  
+ [pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)

### <a name="supported-token-filters"></a>지원되는 토큰 필터
아래 목록은 Normalizers에 지원되는 토큰 필터를 보여주며 어휘 분석과 관련된 전체 토큰 필터의 하위 집합입니다. 필터에 대한 자세한 내용은 [토큰 필더 참조](index-add-custom-analyzers.md#TokenFilters)를 참조하세요.

+ [arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)
+ [asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)
+ [cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)  
+ [elision](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)  
+ [german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)
+ [hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)  
+ [indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)
+ [persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)
+ [scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)  
+ [scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)
+ [sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)  
+ [lowercase](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)
+ [uppercase](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)


## <a name="custom-normalizer-example"></a>사용자 지정 Normalizers 예제

아래 예제에서는 해당하는 문자 필터 및 토큰 필터를 사용하는 사용자 지정 Normalizers 정의를 보여 줍니다. 문자 필터 및 토큰 필더의 사용자 지정 옵션은 구문이라는 이름으로 별도로 지정된 다음 아래 설명된 대로 Normalizers 정의에서 참조됩니다.

* 사용자 지정 Normalizers "my_custom_normalizer"는 인덱스 정의의 `normalizers` 섹션에서 정의됩니다.
* Normalizers는 두 개의 문자 필터와 세 개의 토큰 필터(elision, 소문자 및 사용자 지정된 asciifolding 필터 "my_asciifolding")로 구성됩니다.
* 첫 번째 문자 필터 "map_dash"는 모든 대시를 밑줄로 바꾸고 두 번째 문자 "remove_whitespace"는 모든 공백을 제거합니다.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false,
        },
        {
           "name":"city",
           "type":"Edm.String",
           "filterable": true,
           "facetable": true,
           "normalizer": "my_custom_normalizer"
        }
     ],
     "normalizers":[
        {
           "name":"my_custom_normalizer",
           "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],,
           "tokenFilters":[              
              "my_asciifolding",
              "elision",
              "lowercase",
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
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

## <a name="see-also"></a>참고 항목
+ [언어 및 텍스트 처리용 분석기](search-analyzers.md)

+ [문서 검색 REST API](/rest/api/searchservice/search-documents) 
