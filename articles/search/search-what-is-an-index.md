---
title: 인덱스 개요
titleSuffix: Azure Cognitive Search
description: 스키마 정의 및 물리적 데이터 구조를 포함하는 Azure Cognitive Search의 인덱싱 개념 및 도구를 소개합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: ab1106ef927829589934485c2022d353339d5089
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062817"
---
# <a name="search-indexes-in-azure-cognitive-search"></a>Azure Cognitive Search 인덱스 검색

Cognitive Search는 ‘검색 인덱스’에서 전체 텍스트 및 필터링 된 쿼리에 사용되는 검색 가능한 콘텐츠를 저장합니다. 인덱스는 스키마에 의해 정의되고 서비스에 저장되며, 데이터 가져오기는 두 번째 단계로 수행됩니다. 

이 문서에서는 검색 인덱스를 소개합니다. 시작하려고 합니까? [검색 인덱스 만들기를](search-how-to-create-search-index.md)참조하세요.

## <a name="whats-a-search-index"></a>검색 인덱스란?

Cognitive Search 인덱스는 *검색 문서* 를 포함합니다. 개념상, 문서는 인덱스에서 검색 가능한 데이터의 단일 단위입니다. 예를 들어 소매점은 각 제품에 대한 문서를 가질 수 있고, 뉴스 조직에는 각 문서에 대한 문서가 있을 수 있습니다. 이러한 개념을 좀 더 익숙한 데이터베이스 대응 개념과 연관 지어 살펴보면 ‘검색 인덱스’는 ‘테이블’에 해당하고 ‘문서’는 테이블의 ‘행’과 거의 비슷합니다.   

인덱스의 물리적 구조는 스키마에 의해 결정됩니다. 일반적으로 ‘필드’ 컬렉션이 인덱스의 가장 큰 파트이고, 각 필드에 이름과 [데이터 형식](/rest/api/searchservice/Supported-data-types)이 지정되며 사용 방법을 결정하는 허용 가능한 동작으로 특성이 지정됩니다.

```json
{
  "name": "name_of_index, unique across the service",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "encryptionKey":(optional){ }
  }
}
```

다른 요소는 간결하게 축소되지만 다음 링크를 통해 세부 정보를 제공할 수 있습니다. 분석기 및 [CORS(교차 원본 원격 스크립팅)](#corsoptions) 설정에서 지원하는 언어 규칙 또는 기타 특성에 따라 토큰에 문자열을 처리하는 데 사용되는 [제안기](index-add-suggesters.md), [점수 매기기 프로필](index-add-scoring-profiles.md), [분석기](search-analyzers.md).

## <a name="field-definitions"></a>필드 정의

검색 문서는 `fields` 컬렉션에 의해 정의됩니다. 문서 식별(키), 검색 가능한 텍스트 저장 및 필터, Facet 및 정렬을 지원하기 위한 필드가 필요합니다. 사용자에게 전혀 볼 수 없는 데이터에 대한 필드가 필요할 수도 있습니다. 예를 들어 검색 순위를 수정하는 데 사용할 수 있는 수익률 또는 마케팅 프로모션에 대한 필드를 원할 수 있습니다.

Edm.String 형식의 필드 하나는 문서 키로 지정해야 합니다. 각 검색 문서를 고유하게 식별하는 데 사용하며 대/소문자를 구분합니다. 키를 기준으로 문서를 검색하여 세부 정보 페이지를 채울 수 있습니다.

들어오는 데이터가 본질적으로 계층적이면 [복합 형식](search-howto-complex-data-types.md) 데이터 형식을 할당하여 중첩된 구조를 나타냅니다. 기본 제공 샘플 데이터 세트인 호텔은 각 호텔에 대해 일대일 관계를 갖는 주소(여러 개의 하위 필드 포함)를 사용하는 복합 형식을 보여 주며, 객실 복합 컬렉션은 여러 개의 객실이 각 호텔과 연결되어 있습니다. 

인덱스를 만들기 전에 문자열 필드에 분석기를 할당합니다. 특정 필드에서 자동 완성을 사용하려면 제안기에 대해 동일한 작업을 합니다.

<a name="index-attributes"></a>

### <a name="attributes"></a>특성

필드 특성에 따라 필드가 사용되는 방식(즉, 전체 텍스트 검색, 패싯 탐색, 정렬 작업 등)이 결정됩니다. 

문자열 필드는 보통 “검색 가능” 및 “조회 가능”으로 표시됩니다. 검색 결과 범위를 좁히는 데 사용되는 필드는 “정렬 가능”, “필터링 가능”, “패싯 가능”이 있습니다.

|attribute|Description|  
|---------------|-----------------|  
|“검색 가능” |전체 텍스트 검색 가능하며, 인덱싱 중에 단어 분리 등의 어휘 분석이 적용됩니다. 검색 가능 필드를 “sunny day” 등의 값으로 설정하면 내부적으로 해당 필드가 개별 토큰 “sunny”와 “day”로 분할됩니다. 자세한 내용은 [전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)을 참조하세요.|  
|“필터링 가능” |$filter 쿼리에서 참조됩니다. 형식이 `Edm.String` 또는 `Collection(Edm.String)`인 필터링 가능 필드의 경우 단어 분리가 수행되지 않으므로 정확하게 일치하는 항목만 비교합니다. 예를 들어 이러한 필드 f를 “sunny day”로 설정하면 `$filter=f eq 'sunny'`에서는 일치하는 항목이 발견되지 않지만 `$filter=f eq 'sunny day'`에서는 일치하는 항목이 발견됩니다. |  
|“정렬 가능” |기본적으로 시스템은 점수에 따라 결과를 정렬하지만 문서에 있는 필드를 기반으로 정렬하도록 구성할 수 있습니다. 형식이 `Collection(Edm.String)`인 필드는 “정렬 가능”으로 설정할 수 없습니다. |  
|“패싯 가능” |일반적으로 카테고리별 적중 횟수가 포함된 검색 결과를 표시하는 데 사용됩니다(예: 특정 도시의 호텔). 형식이 `Edm.GeographyPoint`인 필드에는 이 옵션을 사용할 수 없습니다. “필터링 가능”, “정렬 가능” 또는 “패싯 가능”하고 형식이 `Edm.String`인 필드는 길이가 최대 32KB일 수 있습니다. 자세한 내용은 [인덱스 만들기(REST API)](/rest/api/searchservice/create-index)를 참조하세요.|  
|“키” |인덱스 내의 문서에 대한 고유 식별자입니다. 정확히 하나의 필드를 키 필드로 선택해야 하며 해당 필드의 형식은 `Edm.String`이어야 합니다.|  
|“조회 가능” |검색 결과에서 필드를 반환할 수 있는지 여부를 결정합니다. *이익률* 등의 필드를 필터, 정렬 또는 점수 매기기 메커니즘으로 사용하며 최종 사용자에게는 필드를 표시하지 않으려는 경우 이 기능을 사용하면 유용합니다. `true` for `key` 로 설정해야 합니다.|  

언제든지 새 필드를 추가할 수 있지만 기존 필드 정의는 인덱스 수명 동안 잠겨 있습니다. 이러한 이유로 개발자는 일반적으로 포털을 사용하여 간단한 인덱스를 만들거나 아이디어를 테스트하거나 포털 페이지를 사용하여 설정을 조회합니다. 인덱스를 쉽게 다시 작성할 수 있도록 코드 기반 접근 방식을 따르는 경우 인덱스 대한 빈번한 반복 디자인이 보다 효율적입니다.

> [!NOTE]
> 인덱스를 빌드하는 데 사용하는 API에는 다양한 기본 동작이 있습니다. [REST API](/rest/api/searchservice/Create-Index)의 경우 대부분의 특성은 기본적으로 사용하도록 설정됩니다. 예를 들어 “검색 가능” 및 “조회 가능”은 문자열 필드에 대해 true이며, 이를 해제하려는 경우에만 설정이 필요한 경우가 많습니다. .NET SDK는 그 반대의 경우가 해당합니다. 명시적으로 설정하지 않은 속성의 경우 사용자가 특별히 사용하도록 설정하지 않는 한 기본적으로 해당 검색 동작을 사용 안 함으로 설정합니다.

<a name="index-size"></a>

## <a name="storage-implications-of-field-attributes"></a>필드 특성의 Storage 의미

인덱스의 크기는 업로드하는 문서의 크기와 인덱스 구성(예: 제안기 포함 여부 및 개별 필드에 특성을 설정하는 방법)에 따라 결정됩니다. 

다음 스크린샷은 다양한 특성을 조합한 인덱스 스토리지 패턴을 보여줍니다. 인덱스는 데이터 가져오기 마법사를 사용하여 쉽게 만들 수 있는 **부동산 샘플 인덱스** 를 기반으로 합니다. 인덱스 스키마는 표시되지 않지만, 인덱스 이름을 보고 특성을 유추할 수 있습니다. 예를 들어 *realestate-searchable* 인덱스에서는 “검색 가능” 특성만 선택되었고 그 외에는 아무것도 선택되지 않았으며, *realestate-retrievable* 인덱스에서는 “조회 가능” 특성만 선택되었고 그 외에는 아무것도 선택되지 않았습니다.

![특성 선택에 따른 인덱스 크기](./media/search-what-is-an-index/realestate-index-size.png "특성 선택에 따른 인덱스 크기")

이러한 인덱스 변형은 인위적이지만, 특성이 스토리지에 미치는 영향을 광범위하게 비교하는 데 참조할 수 있습니다. “조회 가능”을 설정하면 인덱스 크기가 커지나요? 아니요. **제안기** 에 필드를 추가하면 인덱스 크기가 커지나요? 예. 

필터링 필드 및 정렬된 필드가 토큰화되지 않아 문자 시퀀스를 축자와 일치시킬 수 있으므로, 필드를 필터링 또는 정렬 가능하도록 만들면 스토리지 소비도 증가합니다.

또한 [분석기](search-analyzers.md)의 영향은 위의 표에 반영되지 않습니다. edgeNgram 토크나이저를 사용하여 문자의 축자 시퀀스(a, ab, abc, abcd)를 저장하면 표준 분석기를 사용한 경우보다 인덱스 크기가 커지게 됩니다.

> [!Note]
> 스토리지 아키텍처는 Azure Cognitive Search의 구현 세부 정보로 간주되며 예고 없이 변경될 수 있습니다. 현재 동작이 나중에도 유지된다는 보장은 없습니다.

<a name="corsoptions"></a>

## <a name="about-corsoptions"></a>`corsOptions` 정보

인덱스 스키마에는 `corsOptions` 설정에 대한 섹션이 포함됩니다. 브라우저에서는 모든 원본 간 요청을 차단하므로 클라이언트 쪽 JavaScript는 기본적으로 API를 호출할 수 없습니다. 인덱스에 대한 원본 간 쿼리를 허용하려면 **corsOptions** 특성을 설정하여 CORS(원본 간 리소스 공유)를 사용하도록 설정합니다. 보안상의 이유로 쿼리 API에서만 CORS가 지원됩니다. 

CORS에 대해 설정할 수 있는 옵션은 다음과 같습니다.

+ **allowedOrigins**(필수): 인덱스에 대한 액세스 권한을 부여할 원본 목록입니다. 이 원본에서 제공되는 모든 JavaScript 코드는 올바른 API 키를 제공하는 경우 인덱스를 쿼리하도록 허용됩니다. 각 원본은 보통 `protocol://<fully-qualified-domain-name>:<port>` 형식이지만 `<port>`는 대개 생략됩니다. 자세한 내용은 [원본 간 리소스 공유(위키백과)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)를 참조하세요.

  모든 원본에 대한 액세스를 허용하려면 **allowedOrigins** 배열에서 `*`를 단일 항목으로 포함합니다. *프로덕션 검색 서비스에는 권장되지 않지만* 개발 및 디버깅에 유용한 경우가 많습니다.

+ **maxAgeInSeconds**(선택): 브라우저는 이 값을 사용하여 CORS 실행 전 응답을 캐시할 기간(초)을 결정합니다. 이 값은 음수가 아닌 정수여야 합니다. 이 값이 클수록 성능은 개선되지만 CORS 정책 변경 내용이 적용되는 시간은 더 오래 걸립니다. 이 값을 설정하지 않으면 기본 기간인 5분이 사용됩니다.

## <a name="next-steps"></a>다음 단계

Cognitive Search 대한 거의 모든 샘플 또는 연습을 사용하여 인덱스 만들기 실습 경험을 얻을 수 있습니다. 먼저 목차에서 빠른 시작 중 하나를 선택할 수 있습니다.

하지만 데이터로 인덱스를 로드하는 방법론에 대해서도 알고 싶을 것입니다. 인덱스 정의 및 데이터 가져오기 전략은 동시에 정의됩니다. 다음 문서는 인덱스를 만들고 로드 하는 방법에 대 한 자세한 정보를 제공 합니다.

+ [검색 인덱스 만들기](search-how-to-create-search-index.md)

+ [데이터 가져오기 개요](search-what-is-data-import.md)

+ [문서 추가, 업데이트 또는 삭제(REST)](/rest/api/searchservice/addupdate-or-delete-documents) 