---
title: 인덱스 개요
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 검색 인덱스가 무엇인지 설명하고 콘텐츠, 구성, 물리적 식 및 인덱스 스키마에 대해 설명합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: c119d8dbff91b04e292fa05985db93176adabdbf
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132756499"
---
# <a name="indexes-in-azure-cognitive-search"></a>Azure Cognitive Search 인덱스

Azure Cognitive Search *검색 인덱스는 검색* 엔진에서 인덱싱, 전체 텍스트 검색 및 필터링된 쿼리에 사용할 수 있는 검색 가능한 콘텐츠입니다. 인덱스는 스키마에 의해 정의되고 검색 서비스에 저장되며, 다음 데이터 가져오기는 두 번째 단계로 수행됩니다. 이 콘텐츠는 최신 애플리케이션에서 예상되는 밀리초 응답 시간에 필요한 기본 데이터 저장소와 별도로 검색 서비스 내에 존재합니다. 특정 인덱싱 시나리오를 제외하고 검색 서비스는 로컬 데이터에 연결하거나 쿼리하지 않습니다.

검색 인덱스 만들기 및 관리의 경우 이 문서는 다음을 이해하는 데 도움이 됩니다.

+ 콘텐츠(문서 및 스키마)
+ 물리적 표현
+ 기본 작업

바로 실습하기를 원하시나요? 대신 [검색 인덱스 만들기를](search-how-to-create-search-index.md) 참조하세요.

## <a name="content-of-a-search-index"></a>검색 인덱스의 내용

Cognitive Search 인덱스는 *검색 문서* 를 포함합니다. 개념상, 문서는 인덱스에서 검색 가능한 데이터의 단일 단위입니다. 예를 들어 소매점은 각 제품에 대한 문서를 가질 수 있고, 뉴스 조직에는 각 문서에 대한 문서가 있을 수 있으며, 여행 사이트에는 각 호텔과 목적지에 대한 문서가 있을 수 있습니다. 이러한 개념을 좀 더 익숙한 데이터베이스 대응 개념과 연관 지어 살펴보면 ‘검색 인덱스’는 ‘테이블’에 해당하고 ‘문서’는 테이블의 ‘행’과 거의 비슷합니다.   

문서의 구조는 아래 그림과 같이 인덱스 스키마에 의해 결정됩니다. "fields" 컬렉션은 일반적으로 인덱스의 가장 큰 부분으로, 각 필드의 이름이 [지정되고, 데이터 형식이](/rest/api/searchservice/Supported-data-types)할당되며, 사용 방법을 결정하는 허용 가능한 동작으로 특성이 지정됩니다.

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

다른 요소는 간결하게 축소되지만 다음 링크는 세부 정보를 제공할 수 있습니다. 

+ [제안기는](index-add-suggesters.md) 자동 완성과 같은 미리 입력 쿼리를 지원합니다.
+ [점수 매기기 프로필은](index-add-scoring-profiles.md) 관련성 튜닝에 사용됩니다.
+ [분석기는 분석기에서](search-analyzers.md) 지원하는 언어 규칙 또는 기타 특성에 따라 문자열을 토큰으로 처리하는 데 사용됩니다.
+ [CORS(원본 간 원격 스크립팅)는](search-how-to-create-search-index.md#corsoptions) 다른 도메인의 요청을 발행하는 앱에 사용됩니다.
+ [암호화 키는](search-security-manage-encryption-keys.md) 인덱스의 중요한 콘텐츠를 이중 암호화하는 데 사용됩니다.

### <a name="field-definitions"></a>필드 정의

검색 문서는 [인덱스 만들기 요청](/rest/api/searchservice/create-index)본문의 "fields" 컬렉션에 의해 정의됩니다. 문서 식별(키), 검색 가능한 텍스트 저장 및 필터, Facet 및 정렬을 지원하기 위한 필드가 필요합니다. 사용자에게 전혀 볼 수 없는 데이터에 대한 필드가 필요할 수도 있습니다. 예를 들어 검색 순위를 수정하는 데 사용할 수 있는 수익률 또는 마케팅 프로모션에 대한 필드를 원할 수 있습니다.

들어오는 데이터가 본질적으로 계층적인 경우 인덱스 내에서 중첩된 구조를 나타내는 데 사용되는 [복합 형식으로](search-howto-complex-data-types.md)나타낼 수 있습니다. 기본 제공 샘플 데이터 세트인 호텔은 각 호텔에 대해 일대일 관계를 갖는 주소(여러 개의 하위 필드 포함)를 사용하는 복합 형식을 보여 주며, 객실 복합 컬렉션은 여러 개의 객실이 각 호텔과 연결되어 있습니다. 

<a name="index-attributes"></a>

### <a name="field-attributes"></a>필드 특성

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

## <a name="physical-structure-and-size"></a>물리적 구조 및 크기

Azure Cognitive Search 인덱스의 물리적 구조는 주로 내부 구현입니다. 스키마에 액세스하고, 콘텐츠를 쿼리하고, 크기를 모니터링하고, 용량을 관리할 수 있지만 클러스터 자체(인덱스, [분할된 데이터베이스](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards)및 기타 파일 및 폴더)는 Microsoft에서 내부적으로 관리합니다.

인덱스의 크기는 다음을 통해 결정됩니다.

+ 문서의 수량 및 컴퍼지션
+ 인덱스 구성(특히, 제안기를 포함하는지 여부)
+ 개별 필드의 특성

Azure Portal 인덱스 탭에서 또는 검색 서비스에 대해 GET INDEX 요청을 발급하여 [인덱스](/rest/api/searchservice/get-index) 크기를 모니터링할 수 있습니다.

### <a name="factors-influencing-index-size"></a>인덱스 크기에 영향을 미치는 요소

문서 컴퍼지션 및 수량은 가져오도록 선택한 내용에 따라 결정됩니다. 검색 인덱스 는 검색 가능한 콘텐츠만 포함해야 합니다. 원본 문서에 이진 필드가 포함된 경우 AI 보강을 사용하여 콘텐츠를 해독하고 분석하여 텍스트 검색 가능한 정보를 만들지 않는 한 일반적으로 인덱스 스키마에서 해당 필드를 생략합니다.

인덱스 구성에는 제안기, 고객 분석기, 점수 매기기 프로필, CORS 설정 및 암호화 키 정보와 같은 문서 외에 다른 구성 요소가 포함될 수 있습니다. 위의 목록에서 인덱스 크기에 영향을 미칠 가능성이 있는 유일한 구성 요소는 제안기입니다. [**제안기는**](index-add-suggesters.md) 미리 입력 또는 자동 완성 쿼리를 지원하는 구문입니다. 따라서 제안기를 포함하면 인덱싱 프로세스에서 축자 문자 일치에 필요한 데이터 구조를 만듭니다. 제안기는 필드 수준에서 구현되므로 미리 입력에 적합한 필드만 선택합니다.

필드 특성은 인덱스 크기의 세 번째 고려 사항입니다. 특성에 따라 동작이 결정됩니다. 이러한 동작을 지원하기 위해 인덱싱 프로세스는 지원 데이터 구조를 만듭니다. 예를 들어 "searchable"은 토큰화된 용어에 대해 반전된 인덱스를 검색하는 [전체 텍스트 검색](search-lucene-query-architecture.md)를 호출합니다. 반면에 "필터링 가능" 또는 "정렬 가능" 특성은 수정되지 않은 문자열에 대한 반복을 지원합니다.

### <a name="example-demonstrating-the-storage-implications-of-attributes-and-suggesters"></a>특성 및 제안기의 스토리지 의미를 보여주는 예제

다음 스크린샷은 다양한 특성을 조합한 인덱스 스토리지 패턴을 보여줍니다. 인덱스는 데이터 가져오기 마법사 및 기본 제공 샘플 데이터를 사용하여 쉽게 만들 수 있는 부동산 샘플 **인덱스** 를 기반으로 합니다. 인덱스 스키마는 표시되지 않지만, 인덱스 이름을 보고 특성을 유추할 수 있습니다. 예를 들어 *realestate-searchable* 인덱스에서는 “검색 가능” 특성만 선택되었고 그 외에는 아무것도 선택되지 않았으며, *realestate-retrievable* 인덱스에서는 “조회 가능” 특성만 선택되었고 그 외에는 아무것도 선택되지 않았습니다.

![특성 선택에 따른 인덱스 크기](./media/search-what-is-an-index/realestate-index-size.png "특성 선택에 따른 인덱스 크기")

이러한 인덱스 변형은 다소 인공적이지만 특성이 스토리지에 미치는 영향에 대한 광범위한 비교를 참조할 수 있습니다.

+ "조회 가능"은 인덱스 크기에 영향을 미치지 않습니다.
+ "filterable", "sortable", "facetable"은 더 많은 스토리지를 소비합니다.
+ **확인 기** 의 인덱스 크기는 클 수 있지만, 스크린 샷에 표시 되는 만큼 많은 가능성이 있습니다. 확인 기 인식 될 수 있는 모든 필드가 선택 되었으므로 대부분의 인덱스에서 가능성이 높습니다.

또한 [분석기](search-analyzers.md)의 영향은 위의 표에 반영되지 않습니다. edgeNgram 토크나이저를 사용하여 문자의 축자 시퀀스(a, ab, abc, abcd)를 저장하면 표준 분석기를 사용한 경우보다 인덱스 크기가 커지게 됩니다.

## <a name="basic-operations-and-interaction"></a>기본 작업 및 상호 작용

인덱스의 용도를 더 잘 이해 했으므로이 섹션에서는 단일 인덱스에 연결 하 고 보안을 설정 하는 것을 포함 하 여 인덱스 런타임 작업을 소개 합니다.

> [!NOTE]
> 인덱스를 관리할 때는 인덱스를 이동 하거나 복사 하는 데 사용할 수 있는 포털 또는 API가 없다는 점에 유의 하십시오. 대신, 고객은 일반적으로 다른 검색 서비스 (동일한 인덱스 이름을 사용 하는 경우)에서 해당 응용 프로그램 배포 솔루션을 가리키거나 이름을 수정 하 여 현재 검색 서비스에서 복사본을 만든 다음 빌드합니다.

### <a name="index-isolation"></a>인덱스 격리
  
Cognitive Search에서 한 번에 하나의 인덱스를 사용 하 여 모든 인덱스 관련 작업은 단일 인덱스를 대상으로 합니다. 인덱싱 또는 쿼리 중에는 관련 인덱스 또는 독립 인덱스의 조인 개념이 없습니다. 

### <a name="continuously-available"></a>지속적으로 사용 가능

인덱스는 계속 사용할 수 있으며 일시 중지 하거나 오프 라인으로 전환할 수 없습니다. 연속 작업용으로 설계 되었기 때문에 콘텐츠를 업데이트 하거나 인덱스 자체에 대 한 추가 사항은 실시간으로 발생 합니다. 따라서 요청이 문서 업데이트와 일치 하는 경우 쿼리가 일시적으로 불완전 한 결과를 반환할 수 있습니다.

문서 작업 (새로 고침 또는 삭제)에 대 한 쿼리 연속성과 기존 인덱스의 기존 구조 및 무결성에 영향을 주지 않는 수정 사항 (예: 새 필드 추가)이 존재 합니다. 구조적 업데이트 (기존 필드 변경)를 수행 해야 하는 경우 일반적으로 개발 환경에서 삭제 후 다시 작성 워크플로를 사용 하거나 프로덕션 서비스에 새 인덱스 버전을 만들어 관리 합니다.

[인덱스 다시 작성](search-howto-reindex.md)을 방지 하기 위해 작은 변경을 수행 하는 일부 고객은 이전 버전과 함께 사용할 새 항목을 만들어 필드를 "버전"으로 선택 합니다. 시간이 지남에 따라이는 더 이상 사용 되지 않는 필드 또는 사용 되지 않는 사용자 지정 분석기 정의 형식으로 분리 된 콘텐츠를 생성 합니다. 특히 프로덕션 인덱스에서 복제 하는 데 비용이 많이 듭니다. 인덱스 수명 주기 관리의 일부로 인덱스에 대해 계획 된 업데이트에서 이러한 문제를 해결할 수 있습니다.

### <a name="endpoint-connection-and-security"></a>끝점 연결 및 보안

모든 인덱싱 및 쿼리 요청은 인덱스를 대상으로 합니다. 끝점은 일반적으로 다음 중 하나입니다.

| 엔드포인트 | 연결 및 액세스 제어 |
|----------|-------------------------------|
| `<your-service>.search.windows.net/indexes` | 인덱스 컬렉션을 대상으로 합니다. 인덱스를 만들거나, 나열 하거나, 삭제할 때 사용 됩니다. 이러한 작업에는 관리자 권한이 필요 하며, 관리자 [API 키](search-security-api-keys.md) 또는 [검색 참가자 역할](search-security-rbac.md#built-in-roles-used-in-search)을 통해 사용할 수 있습니다. |
| `<your-service>.search.windows.net/indexes/<your-index>/docs` | 단일 인덱스의 문서 컬렉션을 대상으로 합니다. 인덱스 또는 데이터 새로 고침을 쿼리할 때 사용 됩니다. 쿼리의 경우 읽기 권한이 충분 하며 쿼리 API 키 또는 데이터 판독기 역할을 통해 사용할 수 있습니다. 데이터 새로 고침을 위해 관리자 권한이 필요 합니다. |

## <a name="next-steps"></a>다음 단계

Cognitive Search에 대 한 거의 모든 샘플 또는 연습을 사용 하 여 인덱스를 만드는 실습 경험을 얻을 수 있습니다. 먼저 목차에서 빠른 시작 중 하나를 선택할 수 있습니다.

하지만 데이터로 인덱스를 로드하는 방법론에 대해서도 알고 싶을 것입니다. 인덱스 정의 및 데이터 가져오기 전략은 동시에 정의됩니다. 다음 문서는 인덱스를 만들고 로드 하는 방법에 대 한 자세한 정보를 제공 합니다.

+ [검색 인덱스 만들기](search-how-to-create-search-index.md)

+ [데이터 가져오기 개요](search-what-is-data-import.md)

+ [문서 추가, 업데이트 또는 삭제(REST)](/rest/api/searchservice/addupdate-or-delete-documents) 