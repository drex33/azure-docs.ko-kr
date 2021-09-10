---
title: 점수 매기기 프로필을 추가하여 검색 점수 높이기
titleSuffix: Azure Cognitive Search
description: 검색 인덱스에 점수 매기기 프로필을 추가하여 Azure Cognitive Search 결과의 검색 관련성 점수를 높입니다.
manager: nitinme
author: shmed
ms.author: ramero
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/16/2021
ms.openlocfilehash: 186fa5fe490541ce465eca37cf21a6a63923e2ee
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112321885"
---
# <a name="add-scoring-profiles-to-a-search-index"></a>검색 인덱스에 점수 매기기 프로필 추가

전체 텍스트 검색 쿼리의 경우 검색 엔진은 일치하는 각 문서에 대한 검색 점수를 계산하며, 이 점수를 통해 결과의 순위를 낮음부터 높음까지 지정할 수 있습니다. Azure Cognitive Search에서는 기본 채점 알고리즘을 사용하여 초기 점수를 계산하지만, *점수 매기기 프로필* 을 통해 계산을 사용자 지정할 수 있습니다.

점수 매기기 프로필은 인덱스 정의에 포함되며 일치 항목의 점수를 높이는 속성을 갖고 있습니다. 프로필에 있는 추가 조건은 점수 상승 논리를 제공합니다. 예를 들어 잠재 수익을 기준으로 일치 항목의 점수를 높이거나, 새로운 품목을 프로모션하거나, 너무 오랫동안 재고에 포함되어 있는 품목의 점수를 높일 수 있습니다.  

관련성 개념에 익숙하지 않으신가요? 다음 비디오 세그먼트는 Azure Cognitive Search에서 점수 매기기 프로필이 어떻게 작동하는지 빠르게 보여주지만, 기본 개념에 대한 내용도 다룹니다. 더 많은 배경 지식을 원하신다면 [유사성 순위 지정 및 채점](index-similarity-and-scoring.md)도 검토하세요.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=463&end=970]

## <a name="what-is-a-scoring-profile"></a>점수 매기기 프로필이란?

점수 매기기 프로필은 가중 필드, 함수 및 매개 변수로 구성된 인덱스 정의의 일부입니다. 점수 매기기 프로필의 목적은 사용자가 제공하는 기준에 따라 일치하는 문서의 점수를 높이거나 강화하는 것입니다. 

다음 정의는 'geo'라는 간단한 프로필을 보여줍니다. 이 프로필은 hotelName 필드에 검색 용어가 포함된 항목의 점수를 높입니다. 또한 `distance` 함수를 사용하여 현재 위치에서 10km 이내에 있는 결과의 점수를 높입니다. ‘inn’이라는 용어를 검색하는 경우 ‘inn’이 호텔 이름의 일부분이라면 현재 위치의 10KM 반경 내에서 이름에 ‘inn’이 포함된 호텔을 포함하는 문서가 검색 결과에서 더 높은 순위로 표시됩니다.  

```json
"scoringProfiles": [
  {  
    "name":"geo",
    "text": {  
      "weights": {  
        "hotelName": 5
      }                              
    },
    "functions": [
      {  
        "type": "distance",
        "boost": 5,
        "fieldName": "location",
        "interpolation": "logarithmic",
        "distance": {
          "referencePointParameter": "currentLocation",
          "boostingDistance": 10
        }                        
      }                                      
    ]                     
  }            
]
```  

이 점수 매기기 프로필을 사용하기 위해 쿼리는 요청에 scoringProfile 매개 변수를 지정하도록 작성됩니다.

```http
POST /indexes/hotels/docs&api-version=2020-06-30
{
    "search": "inn",
    "scoringProfile": "geo",
    "scoringParameter": currentLocation--122.123,44.77233
}
```  

이 쿼리는 용어 "inn"을 검색하고 현재 위치를 전달합니다. 이 쿼리에는 scoringParameter 같은 다른 매개 변수도 포함되어 있습니다. 쿼리 매개 변수에 대한 내용은 [문서 검색(REST API)](/rest/api/searchservice/Search-Documents)에 설명되어 있습니다.  

점수 매기기 프로필에 대한 자세한 예제를 검토하려면 [확장된 예제](#bkmk_ex)를 참조하세요.  

<a name=what-is-default-scoring></a>

## <a name="how-scores-are-computed"></a>점수를 계산하는 방법

점수는 전체 텍스트 검색 쿼리에 대해 계산되며, 가장 관련성이 많은 일치 항목의 순위를 지정하여 응답의 맨 위에 반환하는 것이 목적입니다. 각 문서의 전체 점수는 각 필드의 개별 점수를 집계한 것입니다. 여기서 각 필드의 개별 점수는 해당 필드 내에서 검색된 용어 빈도 및 문서 빈도([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)(term frequency-inverse document frequency)라고도 함)를 기준으로 계산됩니다. 

> [!Tip]
> [featuresMode](index-similarity-and-scoring.md#featuresmode-parameter-preview) 매개 변수를 사용하여 검색 결과(필드 수준 점수 포함)와 함께 추가 채점 세부 정보를 요청할 수 있습니다.

## <a name="when-to-add-scoring-logic"></a>채점 논리를 추가하는 경우

기본 순위 지정 동작으로 비즈니스 목표를 충족할 수 없을 때는 점수 매기기 프로필을 하나 이상 만들어야 합니다. 새로 추가하는 항목의 검색 관련성을 높게 설정하려는 경우를 예로 들 수 있습니다. 이익률을 포함하는 필드나 잠재 수익을 나타내는 다른 필드의 경우에도 마찬가지입니다. 사용자 또는 기업에 더 많은 의미가 있는 결과의 점수를 높이는 것이 점수 매기기 프로필 채택의 결정 요소인 경우가 많습니다.

검색 페이지의 관련성 기반 정렬 역시 점수 매기기 프로필을 통해 구현됩니다. 이전에 사용했던 검색 결과 페이지에서는 가격, 날짜, 등급 또는 관련성을 기준으로 결과를 정렬했다면, Azure Cognitive Search에서는 점수 매기기 프로필을 사용하여 ‘관련성’ 옵션을 적용할 수 있습니다. 관련성의 정의는 제공하려는 검색 환경의 유형과 비즈니스 목표를 통해 직접 제어할 수 있습니다.  

<a name="bkmk_ex"></a>

## <a name="extended-example"></a>확장된 예제

아래 예제에서는 2개의 점수 매기기 프로필 `boostGenre` 및 `newAndHighlyRated`가 있는 인덱스의 스키마를 보여줍니다. 쿼리 매개 변수로 두 프로필 중 하나를 포함하는 쿼리를 이 인덱스에 대해 실행하는 경우 해당 프로필을 사용하여 결과 집합의 점수를 계산합니다. 

`boostGenre` 프로필은 가중치 텍스트 필드를 사용하여 albumTitle, genre 및 artistName 필드에서 검색된 일치 항목의 점수를 높입니다. 이러한 필드의 점수는 각각 1.5, 5, 2가 높아집니다. genre가 다른 필드보다 훨씬 크게 상승하는 이유는, musicstoreindex에서 ‘genre’의 경우처럼 비교적 비슷한 데이터를 검색하는 경우 상대 가중치의 편차가 더 커야 할 수 있기 때문입니다. 예를 들어 musicstoreindex에서 ‘rock’은 장르로도 표시되고 같은 구를 사용하는 장르 설명에도 표시됩니다. 이 경우 장르 설명보다 장르에 더 높은 가중치를 적용하려면 genre 필드에 훨씬 높은 상대 가중치를 적용해야 합니다.

```json
{  
  "name": "musicstoreindex",  
  "fields": [  
    { "name": "key", "type": "Edm.String", "key": true },  
    { "name": "albumTitle", "type": "Edm.String" },  
    { "name": "albumUrl", "type": "Edm.String", "filterable": false },  
    { "name": "genre", "type": "Edm.String" },  
    { "name": "genreDescription", "type": "Edm.String", "filterable": false },  
    { "name": "artistName", "type": "Edm.String" },  
    { "name": "orderableOnline", "type": "Edm.Boolean" },  
    { "name": "rating", "type": "Edm.Int32" },  
    { "name": "tags", "type": "Collection(Edm.String)" },  
    { "name": "price", "type": "Edm.Double", "filterable": false },  
    { "name": "margin", "type": "Edm.Int32", "retrievable": false },  
    { "name": "inventory", "type": "Edm.Int32" },  
    { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "boostGenre",  
      "text": {  
        "weights": {  
          "albumTitle": 1.5,  
          "genre": 5,  
          "artistName": 2  
        }  
      }  
    },  
    {  
      "name": "newAndHighlyRated",  
      "functions": [  
        {  
          "type": "freshness",  
          "fieldName": "lastUpdated",  
          "boost": 10,  
          "interpolation": "quadratic",  
          "freshness": {  
            "boostingDuration": "P365D"  
          }  
        },  
        {
          "type": "magnitude",  
          "fieldName": "rating",  
          "boost": 10,  
          "interpolation": "linear",  
          "magnitude": {  
            "boostingRangeStart": 1,  
            "boostingRangeEnd": 5,  
            "constantBoostBeyondRange": false  
          }  
        }  
      ]  
    }  
  ],  
  "suggesters": [  
    {  
      "name": "sg",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": [ "albumTitle", "artistName" ]  
    }  
  ]   
}  
```  

## <a name="steps-for-adding-a-scoring-profile"></a>점수 매기기 프로필을 추가하는 단계

사용자 지정 점수 매기기 동작을 구현하려면 인덱스를 정의하는 점수 매기기 프로필을 스키마에 추가합니다. 인덱스 하나에 최대 100개의 점수 매기기 프로필을 포함할 수는 있지만([서비스 한도](search-limits-quotas-capacity.md) 참조) 프로필은 지정된 쿼리에 한 번에 하나만 지정할 수 있습니다.

1. 인덱스 정의부터 시작합니다. 인덱스를 다시 작성하지 않고 기존 인덱스에서 점수 매기기 프로필을 추가하고 업데이트할 수 있습니다. [인덱스 업데이트](/rest/api/searchservice/update-index) 요청을 사용하여 수정 버전을 게시합니다.

1. 이 토픽에서 제공하는 [템플릿](#bkmk_template)에 붙여넣습니다.  

1. 이름을 입력합니다. 점수 매기기 프로필은 선택 사항이지만 추가하려는 경우 이름을 입력해야 합니다. 필드에 대한 Cognitive Search [명명 규칙](/rest/api/searchservice/naming-rules)에 따라 문자로 시작해야 하며 특수 문자와 예약어는 사용할 수 없습니다.  

1. 점수 상승 조건을 지정합니다. 단일 프로필에 [가중 필드](#weighted-fields), [함수](#functions) 또는 둘 다 포함될 수 있습니다. 

특정 프로필의 효율성을 증명하거나 반증하는 데 도움이 되는 데이터 세트를 사용하여 반복적으로 작업해야 합니다.

점수 매기기 프로필은 다음 스크린샷과 같이 Azure Portal에서 정의할 수도 있고, [REST API](/rest/api/searchservice/update-index) 또는 Azure SDK(예: .NET용 Azure SDK의 [ScoringProfile](/dotnet/api/azure.search.documents.indexes.models.scoringprofile) 클래스)를 통해 프로그래밍 방식으로 정의할 수도 있습니다.

   :::image type="content" source="media/scoring-profiles/portal-add-scoring-profile-small.png" alt-text="점수 매기기 프로필 추가 페이지" lightbox="media/scoring-profiles/portal-add-scoring-profile.png" border="true":::

<a name="weighted-fields"></a>

### <a name="using-weighted-fields"></a>가중 필드 사용

필드 컨텍스트가 중요하고 쿼리가 전체 텍스트 검색(자유 형식 텍스트 검색이라고도 함)인 경우 가중 필드를 사용합니다. 예를 들어 쿼리에 "airport"라는 용어가 포함된 경우 HotelName 필드보다 설명 필드의 "airport"에 더 많은 가중치를 부여하려 할 것입니다. 

가중 필드는 검색 가능한 필드와 승수로 사용되는 양수로 구성됩니다. HotelName의 원래 필드 점수가 3이면 이 필드의 상승된 점수는 6이 되어 부모 문서 자체의 전체적인 점수가 높아집니다.

```json
"scoringProfiles": [  
{  
  "name": "boostKeywords",  
  "text": {  
    "weights": {  
      "HotelName": 2,  
      "Description": 5 
    }  
  }  
}
```

<a name="functions"></a>

### <a name="using-functions"></a>함수 사용

숫자 데이터로 계산되는 거리 및 최신 여부의 사례처럼 단순 상대 가중치로는 충분하지 않거나 단순 상대 가중치가 적용되지 않는 경우에는 함수를 사용합니다. 점수 매기기 프로필마다 여러 함수를 지정할 수 있습니다.

| 함수 | 설명 |
|-|-|
| "freshness" | 날짜/시간 필드(Edm.DataTimeOffset)의 값만큼 점수를 높입니다. 이 함수에는 점수 상승이 발생하는 기간을 나타내는 값을 지정할 수 있도록 `boostingDuration` 특성이 포함되어 있습니다. | 
| "magnitude" | 숫자 값의 크기를 기준으로 점수를 높입니다. 이 함수를 사용해야 하는 시나리오에는 이익률, 최고 가격, 최저 가격 또는 다운로드 수를 기준으로 상승시키는 경우가 포함됩니다. 이 함수는 Edm.Double 및 Edm.Int 필드에만 사용할 수 있습니다. magnitude 함수의 경우 반전 패턴을 사용하려면(예: 비싼 품목보다 저렴한 품목의 점수를 높이기 위해) 범위를 높은 값에서 낮은 값 순서로 반전할 수 있습니다. 예를 들어 가격 범위가 $100~$1인 경우 "boostingRangeStart"를 100으로, "boostingRangeEnd"를 1로 설정하여 가격이 낮은 품목의 점수를 높입니다. | 
| "distance"  | 근접도 또는 지리적 위치를 기준으로 점수를 높입니다. 이 함수는 Edm.GeographyPoint 필드에만 사용할 수 있습니다. | 
| "tag"  | 검색 문서와 쿼리 문자열에 공통적으로 적용된 태그의 점수를 높입니다. 태그는 `tagsParameter`에 제공됩니다. 이 함수는 Edm.String 및 Collection(Edm.String) 필드에만 사용할 수 있습니다. | 

### <a name="rules-for-using-functions"></a>함수 사용 규칙

+ 함수는 필터링 가능한 필드에만 적용할 수 있습니다.
+ 함수 형식("freshness", "magnitude", "distance", "tag")은 소문자여야 합니다.
+ 함수는 null 또는 빈 값을 포함할 수 없습니다.

<a name="bkmk_template"></a>

## <a name="template"></a>템플릿

 이 섹션에서는 점수 매기기 프로필의 구문과 템플릿에 대해 설명합니다. 점수 매기기 프로필 특성에 대한 설명은 다음 섹션의 [속성 참조](#bkmk_indexref)를 확인하세요.  

```json
"scoringProfiles": [  
  {   
    "name": "name of scoring profile",   
    "text": (optional, only applies to searchable fields) {   
      "weights": {   
        "searchable_field_name": relative_weight_value (positive #'s),   
        ...   
      }   
    },   
    "functions": (optional) [  
      {   
        "type": "magnitude | freshness | distance | tag",   
        "boost": # (positive number used as multiplier for raw score != 1),   
        "fieldName": "(...)",   
        "interpolation": "constant | linear (default) | quadratic | logarithmic",   

        "magnitude": {
          "boostingRangeStart": #,   
          "boostingRangeEnd": #,   
          "constantBoostBeyondRange": true | false (default)
        }  

        // ( - or -)  

        "freshness": {
          "boostingDuration": "..." (value representing timespan over which boosting occurs)   
        }  

        // ( - or -)  

        "distance": {
          "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)   
          "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)   
        }   

        // ( - or -)  

        "tag": {
          "tagsParameter":  "..."(parameter to be passed in queries to specify a list of tags to compare against target field)   
        }
      }
    ],   
    "functionAggregation": (optional, applies only when functions are specified) "sum (default) | average | minimum | maximum | firstMatching"   
  }   
],   
"defaultScoringProfile": (optional) "...", 
```  

<a name="bkmk_indexref"></a> 

## <a name="property-reference"></a>속성 참조

|attribute|설명|  
|---------------|-----------------|  
| name | 필수 요소. 점수 매기기 프로필의 이름입니다. 필드와 동일한 이름 지정 규칙을 따릅니다. 즉, 이름은 문자로 시작해야 하고 마침표, 콜론 또는 @ 기호를 포함할 수 없으며 azureSearch 구(대/소문자 구분)로 시작할 수 없습니다.|  
| text | 가중치 속성을 포함합니다.|  
| weights | 선택 사항입니다. 검색 가능한 필드 및 필드 점수를 높이는 양의 정수 또는 부동 소수점 숫자를 지정하는 이름-값 쌍입니다. 양의 정수 또는 숫자는 순위 지정 알고리즘에 의해 생성된 원래 필드 점수의 승수가 됩니다. 예를 들어 필드 점수가 2이고 가중치 값이 3이면 필드의 상승된 점수는 6이 됩니다. 그런 다음, 개별 필드 점수를 집계하여 문서 필드 점수를 만들고, 이 점수를 사용하여 결과 세트의 문서 순위를 지정합니다. |  
| functions | 선택 사항입니다. 점수 매기기 함수는 필터링 가능한 필드에만 적용할 수 있습니다.|  
| functions > type | 점수 매기기 함수의 필수 항목으로, 점수 매기기 함수는 인덱스 필드 컬렉션의 일부이며 필터링 가능한 필드에만 적용할 수 있습니다. 사용할 함수의 유형을 나타냅니다. 유효한 값에는 magnitude, freshness, distance, tag 등이 있습니다. 각 점수 매기기 프로필에 둘 이상의 함수를 포함할 수 있습니다. 함수 이름은 소문자여야 합니다.|  
| functions > boost | 점수 매기기 함수의 필수 항목으로, 점수 매기기 함수는 인덱스 필드 컬렉션의 일부이며 필터링 가능한 필드에만 적용할 수 있습니다. 원점수의 승수로 사용되는 양수입니다. 값이 1일 수는 없습니다.|  
| functions > fieldname | 점수 매기기 함수의 필수 항목으로, 점수 매기기 함수는 인덱스 필드 컬렉션의 일부이며 필터링 가능한 필드에만 적용할 수 있습니다. 또한 각 함수 유형에서는 추가적인 제한이 적용됩니다. 또한 각 함수 형식마다 추가 제한 사항이 도입됩니다. 예를 들어 freshness는 datetime 필드에, magnitude는 integer/double 필드에, distance는 location 필드에 사용됩니다.다. 필드는 함수 정의당 하나만 지정할 수 있습니다. 예를 들어 같은 프로필에서 magnitude를 두 번 사용하려면 각 필드에 하나씩 두 개의 magnitude 정의를 포함해야 합니다.|  
| functions > interpolation | 점수 매기기 함수의 필수 항목으로, 점수 매기기 함수는 인덱스 필드 컬렉션의 일부이며 필터링 가능한 필드에만 적용할 수 있습니다. 범위 시작부터 범위 끝까지 점수 상승이 증가하는 기울기를 정의합니다. 유효한 값에는 Linear(기본값), Constant, Quadratic, Logarithmic 등이 있습니다. 자세한 내용은 [보간 설정](#bkmk_interpolation)을 참조하세요.|  
| functions > magnitude | magnitude 점수 매기기 함수는 숫자 필드의 값 범위를 기준으로 순위를 변경하는 데 사용됩니다. 이 함수를 사용하는 가장 일반적인 몇 가지 예는 다음과 같습니다. </br></br>"별 등급:" "별 등급" 필드 내의 값을 기준으로 채점을 변경합니다. 관련 항목이 두 개이면 등급이 높은 항목이 먼저 표시됩니다. </br>"이익": 관련 문서가 2개 있으면 소매업체는 이익이 더 많이 나는 문서의 점수를 높이려 할 것입니다. </br>"클릭 횟수:" 제품이나 페이지 관련 클릭 동작을 추적하는 애플리케이션의 경우 magnitude를 사용하여 가장 많은 트래픽을 받는 항목의 점수를 높일 수 있습니다. </br>"다운로드 횟수:" 다운로드를 추적하는 애플리케이션의 경우 magnitude 함수를 사용하여 다운로드 수가 가장 많은 항목의 점수를 높일 수 있습니다.|  
| functions > magnitude > boostingRangeStart | magnitude의 점수를 매길 범위의 시작 값을 설정합니다. 이 값은 정수이거나 부동 소수점 숫자여야 합니다. 별 등급 1~4에서 시작 값은 1이고 50%를 초과하는 이익의 경우에는 시작 값이 50입니다.|  
| functions > magnitude > boostingRangeEnd | magnitude의 점수를 매길 범위의 끝 값을 설정합니다. 이 값은 정수이거나 부동 소수점 숫자여야 합니다. 별 등급 1~4에서 끝 값은 4가 됩니다.|  
| functions > magnitude > constantBoostBeyondRange | 유효한 값은 true 또는 false(기본값)입니다. 값을 true로 설정하면 대상 필드의 값이 범위 상한보다 큰 문서에 완전 상승이 계속해서 적용됩니다. 값을 false로 설정하는 경우에는 대상 필드의 값이 범위를 벗어나는 문서에 이 함수의 상승이 적용되지 않습니다.|  
| functions > freshness | freshness 점수 매기기 함수는 DateTimeOffset 필드의 값을 기준으로 항목의 순위 점수를 변경하는 데 사용됩니다. 예를 들어 보다 최근 날짜의 항목에 오래된 날짜의 항목보다 더 높은 순위를 지정할 수 있습니다. </br></br>현재 날짜에 더 가까운 항목이 더 먼 미래의 항목보다 높은 순위가 지정되도록 미래 날짜의 일정 이벤트 같은 항목에 순위를 지정할 수도 있습니다. </br></br>현재 서비스 릴리스에서는 범위의 한쪽 끝이 현재 시간으로 고정됩니다. 반대쪽 끝은 boostingDuration을 기반으로 하는 과거의 시간입니다. 미래의 시간 범위에 더 높은 점수를 주려면 음수 boostingDuration을 사용합니다. </br></br>상승 기준이 최대 범위에서 최소 범위로 변경되는 비율은 점수 매기기 프로필에 적용되는 보간을 통해 결정됩니다(아래 그림 참조). 적용된 상승 계수의 방향을 바꾸려면 1보다 작은 상승 계수를 선택합니다.|  
| functions > freshness > boostingDuration | 특정 문서에 대해 상승을 중지할 만료 기간을 설정합니다. 구문 및 예제는 다음 섹션의 [boostingDuration 설정](#bkmk_boostdur)을 참조하세요.|  
| functions > distance | distance 점수 매기기 함수는 참조 지리적 위치와의 거리를 기준으로 문서 점수를 변경하는 데 사용됩니다. 참조 위치는 매개 변수에서 쿼리의 일부로 lon, lat 인수로 제공됩니다(scoringParameter 쿼리 매개 변수 사용).|  
|functions >  distance > referencePointParameter | 참조 위치로 사용하기 위해 쿼리에 전달되는 매개 변수입니다(scoringParameter 쿼리 매개 변수 사용). 쿼리 매개 변수에 대한 설명은 [문서 검색(REST API)](/rest/api/searchservice/Search-Documents)을 참조하세요.|  
| functions > distance > boostingDistance | 참조 위치로부터 상승 범위가 종료되는 거리(km)를 설정합니다.|  
| functions > tag | tag 점수 매기기 함수는 문서 및 검색 쿼리의 태그를 기반으로 문서 점수에 영향을 주는 데 사용됩니다. 검색 쿼리와 공통적인 태그가 있는 문서가 상승됩니다. 검색 쿼리에 대한 태그는 각 검색 요청에서 채점 매개 변수로 제공됩니다(scoringParameter 쿼리 매개 변수 사용). |  
| functions > tag > tagsParameter | 특정 요청에 대한 태그를 지정하기 위해 쿼리에 전달되는 매개 변수입니다(scoringParameter 쿼리 매개 변수 사용). 쿼리 매개 변수에 대한 설명은 [문서 검색(REST API)](/rest/api/searchservice/Search-Documents)을 참조하세요.|  
| functions > functionAggregation | 선택 사항입니다. 함수를 지정할 때만 적용됩니다. 유효한 값에는 sum(기본값), average, minimum, maximum, firstMatching 등이 있습니다. 검색 점수는 여러 함수를 비롯한 여러 변수에서 계산되는 단일 값입니다. 이 특성은 모든 함수의 순위 상승이 단일 집계 상승으로 결합된 다음, 기본 문서 점수에 적용되는 방식을 나타냅니다. 기본 점수는 문서 및 검색 쿼리에서 계산되는 [tf-idf](http://www.tfidf.com/) 값을 기준으로 합니다.|  
| defaultScoringProfile | 검색 요청을 실행할 때 점수 매기기 프로필이 지정되어 있지 않으면 기본 점수 매기기가 사용됩니다([tf-idf](http://www.tfidf.com/)만 사용). </br></br>사용자 지정 프로필을 검색 요청에서 특정 프로필이 지정되지 않았을 때 사용할 프로필로 대체하여 기본 제공 기본값을 재정의할 수 있습니다.|  

<a name="bkmk_interpolation"></a> 

## <a name="set-interpolations"></a>보간 설정

보간을 사용하면 채점에 사용되는 기울기의 모양을 설정할 수 있습니다. 채점은 높음에서 낮음으로 진행되기 때문에 기울기가 항상 감소하지만, 보간을 통해 하향 기울기의 곡선이 결정됩니다. 다음과 같은 보간을 사용할 수 있습니다.  

| 보간 | 설명 |  
|-|-|  
|`linear`|최댓값 및 최솟값 범위 내 항목의 경우 항목에 적용되는 순위 상승 값이 지속적으로 감소합니다. Linear는 점수 매기기 프로필의 기본 보간입니다.|  
|`constant`|시작 및 끝 범위 내 항목의 경우 일정한 순위 상승이 순위 결과에 적용됩니다.|  
|`quadratic`|순위 상승 값이 일정하게 감소하는 Linear 보간과는 달리 Quadratic 보간에서는 처음에 값이 조금씩 감소했다가 끝 범위가 가까워지면 값이 훨씬 큰 간격으로 감소합니다. 이 보간 옵션은 tag 점수 매기기 함수에서 허용되지 않습니다.|  
|`logarithmic`|순위 상승 값이 일정하게 감소하는 Linear 보간과는 달리 Logarithmic 보간에서는 처음에 값이 크게 감소했다가 끝 범위가 가까워질수록 값이 훨씬 작은 간격으로 감소합니다. 이 보간 옵션은 tag 점수 매기기 함수에서 허용되지 않습니다.|  

 ![그래프의 상수, 선형, 정방형, log10 선](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

<a name="bkmk_boostdur"></a> 

## <a name="set-boostingduration"></a>boostingDuration 설정

`boostingDuration`은 `freshness` 함수의 특성입니다. 이 특성을 사용하여 특정 문서에 대해 상승이 중지되는 만료 기간을 설정합니다. 예를 들어 프로모션 기간 10일 동안 특정 제품 라인이나 브랜드를 상승시키려는 경우 해당 문서에 대해 10일의 기간을 "P10D"로 지정합니다.  

`boostingDuration` 의 형식은 XSD "dayTimeDuration" 값(ISO 8601 기간 값의 제한된 하위 집합)으로 지정해야 합니다. 이 형식의 패턴은 "P[nD][T[nH][nM][nS]]"입니다.  

다음 표에 여러 예제가 나와 있습니다.  

|Duration|boostingDuration|  
|--------------|----------------------|  
|1일|"P1D"|  
|2일 12시간|"P2DT12H"|  
|15분|"PT15M"|  
|30일, 5시간, 10분, 6.334초|"P30DT5H10M6.334S"|  

더 많은 예제를 보려면 [XML 스키마: Datatypes(W3.org 웹 사이트)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)를 참조하세요.  

## <a name="see-also"></a>참고 항목

+ [Azure Cognitive Search의 유사성 순위 지정 및 채점](index-similarity-and-scoring.md)
+ [REST API 참조](/rest/api/searchservice/)
+ [인덱스 API 만들기](/rest/api/searchservice/create-index)
+ [Azure Cognitive Search .NET SDK](/dotnet/api/overview/azure/search?)
+ [점수 매기기 프로필이란?](https://social.technet.microsoft.com/wiki/contents/articles/26706.azure-search-what-are-scoring-profiles.aspx):