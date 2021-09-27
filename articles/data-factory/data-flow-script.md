---
title: 매핑 데이터 흐름 스크립트
description: Data Factory의 데이터 흐름 스크립트 코드 숨김 언어 개요
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/22/2021
ms.openlocfilehash: 73fe862475b866e625d4bf2bdce3c044b6dcc87b
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061583"
---
# <a name="data-flow-script-dfs"></a>DFS(데이터 흐름 스크립트)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

DFS(데이터 흐름 스크립트)는 매핑 데이터 흐름에 포함된 변환을 실행하는 데 사용되는 코딩 언어와 유사한 기본 메타데이터입니다. 모든 변환은 작업을 적절하게 실행하는 데 필요한 정보를 제공하는 일련의 속성으로 표현됩니다. 스크립트는 브라우저 UI의 상단 리본에 있는 “스크립트” 단추를 클릭하여 ADF에서 보고 편집할 수 있습니다.

:::image type="content" source="media/data-flow/scriptbutton.png" alt-text="스크립트 단추":::

예를 들어 원본 변환의 `allowSchemaDrift: true,`는 원본 데이터 세트의 모든 열이 스키마 프로젝션에 포함되지 않은 경우에도 해당 데이터 흐름의 모든 열을 포함하도록 서비스에 지시합니다.

## <a name="use-cases"></a>사용 사례
DFS는 사용자 인터페이스에 의해 자동으로 생성됩니다. 스크립트 단추를 클릭하여 스크립트를 확인하고 사용자 지정할 수 있습니다. ADF UI 외부에서 스크립트를 생성한 다음 PowerShell cmdlet으로 전달할 수도 있습니다. 복잡한 데이터 흐름을 디버그할 때 흐름의 UI 그래프 표현을 검색하는 대신 스크립트 코드 숨김을 검색하는 것이 더 쉬울 수 있습니다.

다음은 몇 가지 사용 사례입니다.
- 매우 유사한 여러 데이터 흐름(예: '스탬프 아웃' 데이터 흐름)을 프로그래밍 방식으로 생성
- UI에서 관리하기 어렵거나 유효성 검사가 필요한 복잡한 식
- 실행 중에 반환되는 다양한 오류를 디버그하고 보다 잘 이해

PowerShell 또는 API와 함께 사용할 데이터 흐름 스크립트를 작성하는 경우 서식이 지정된 텍스트를 한 줄로 축소해야 합니다. 탭과 줄바꿈을 이스케이프 문자로 유지할 수 있습니다. 그러나 JSON 속성에 맞게 텍스트 서식을 지정해야 합니다. 스크립트 편집기 UI의 맨 아래에는 스크립트를 한 줄로 서식 지정하는 단추가 있습니다.

:::image type="content" source="media/data-flow/copybutton.png" alt-text="복사 단추":::

## <a name="how-to-add-transforms"></a>변환 추가 방법
변환 추가에는 핵심 변환 데이터를 추가하고, 입력 스트림을 다시 라우팅한 다음 출력 스트림 다시 라우팅하는 세 기본 단계가 필요합니다. 이는 예제를 통해 보는 살펴보는 것이 가장 쉽습니다.
다음과 같은 간단한 원본에서 싱크로의 데이터 흐름을 시작한다고 가정해 보겠습니다.

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

파생 변환을 추가하기로 결정한 경우 먼저 `upperCaseTitle`이라는 새 대문자 열을 추가하는 간단한 식이 있는 핵심 변환 텍스트를 만들어야 합니다.
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

그런 다음 기존 DFS를 사용하여 변환을 추가합니다.
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

이제 새 변환이 그 다음에 올 변환(이 경우 `source1`)을 식별하고 스트림의 이름을 새 변환에 복사하여 들어오는 스트림을 다시 라우팅합니다.
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

마지막으로 이 새 변환 다음에 올 변환을 식별하고 입력 스트림(이 경우 `sink1`)을 새 변환의 출력 스트림 이름으로 바꿉니다.
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>DFS 기본 사항
DFS는 원본, 싱크 및 기타 다양한 항목을 포함한 일련의 연결된 변형으로 구성되며 새 열을 추가하고, 데이터를 필터링하고, 데이터를 조인하는 등의 작업을 수행할 수 있습니다. 일반적으로 이 스크립트는 하나 이상의 원본으로 시작하고 많은 변환이 뒤따른 다음 하나 이상의 싱크로 끝납니다.

원본에는 모두 동일한 기본 구성이 있습니다.
```
source(
  source properties
) ~> source_name
```

예를 들어 다음은 세 개의 열(movieId, title, genres)이 있는 간단한 원본입니다.
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

원본 이외의 모든 변환에는 동일한 기본 구성이 있습니다.
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

예를 들어 다음은 열(title)을 가져와서 대문자 버전으로 덮어쓰는 간단한 파생 변환입니다.
```
source1 derive(
  title = upper(title)
) ~> derive1
```

스키마가 없는 싱크는 다음과 같이 간단합니다.
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>스크립트 코드 조각

스크립트 코드 조각은 데이터 흐름 전체에서 공유하는 데 사용할 수 있는 데이터 흐름 스크립트의 공유 가능한 코드입니다. 아래 동영상에서는 스크립트 코드 조각을 사용하고 데이터 흐름 스크립트를 활용하여 데이터 흐름 그래프 뒤에 스크립트의 일부를 복사하여 붙여넣는 방법을 설명합니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>집계 요약 통계
'SummaryStats'라는 데이터 흐름에 집계 변환을 추가한 다음 스크립트의 집계 함수에 아래 코드를 붙여넣어 기존의 SummaryStats를 바꿉니다. 이렇게 하면 데이터 프로필 요약 통계에 대한 일반 패턴을 제공합니다.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
아래 샘플을 사용하여 데이터의 고유 개수와 고유 행 수를 계산할 수도 있습니다. 아래 예제에서는 ValueDistAgg라는 집계 변환을 데이터 흐름에 붙여넣을 수 있습니다. 이 예제는 'title'이라는 열을 사용합니다. 'title'을 값 개수를 가져오는 데 사용하려는 데이터가 있는 문자열 열로 바꾸어야 합니다.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>집계에 모든 열 포함
이 패턴은 집계를 작성할 때 출력 메타데이터에 나머지 열을 유지할 수 있는 방법을 보여 주는 일반 집계 패턴입니다. 이 경우 ```first()``` 함수를 사용하여 이름이 'movie'가 아닌 모든 열의 첫 번째 값을 선택합니다. 이를 사용하려면 DistinctRows라는 집계 변환을 만든 다음, 이를 스크립트에서 기존 DistinctRows 집계 스크립트의 맨 위에 붙여넣습니다.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>행 해시 지문 만들기 
데이터 흐름 스크립트에서 이 코드를 사용하여 세 개의 열의 ```sha1``` 해시를 생성하는 ```DWhash```라는 새 파생 열을 만듭니다.

```
derive(DWhash = sha1(Name,ProductNumber,Color)) ~> DWHash
```

이 스크립트를 사용하여 각 열의 이름을 지정할 필요 없이 스트림에 있는 모든 열을 사용하여 행 해시를 생성할 수도 있습니다.

```
derive(DWhash = sha1(columns())) ~> DWHash
```

### <a name="string_agg-equivalent"></a>String_agg equivalent
이 코드는 T-SQL ```string_agg()``` 함수와 동일하게 동작하며 문자열 값을 배열로 집계합니다. 그런 다음 해당 배열을 SQL 대상과 함께 사용할 문자열로 캐스팅할 수 있습니다.

```
source1 aggregate(groupBy(year),
    string_agg = collect(title)) ~> Aggregate1
Aggregate1 derive(string_agg = toString(string_agg)) ~> StringAgg
```

### <a name="count-number-of-updates-upserts-inserts-deletes"></a>업데이트, upsert, 삽입, 삭제 수 계산
행 변경 변환을 사용하는 경우 행 변경 정책에서 발생하는 업데이트, upsert, 삽입, 삭제 수를 계산하는 것이 좋습니다. 행 변경 뒤에 집계 변환을 추가하고 이 데이터 흐름 스크립트를 해당 개수에 대한 집계 정의에 붙여넣습니다.

```
aggregate(updates = countIf(isUpdate(), 1),
        inserts = countIf(isInsert(), 1),
        upserts = countIf(isUpsert(), 1),
        deletes = countIf(isDelete(),1)) ~> RowCount
```

### <a name="distinct-row-using-all-columns"></a>모든 열을 사용하는 고유 행
이 코드 조각은 들어오는 모든 열을 사용하고, 그룹화에 사용되는 해시를 생성하여 중복 항목을 제거하고, 각 중복의 첫 번째 항목을 출력으로 제공하는 새 집계 변환을 데이터 흐름에 추가합니다. 열 이름을 명시적으로 지정할 필요는 없으며 들어오는 데이터 스트림에서 자동으로 생성됩니다.

```
aggregate(groupBy(mycols = sha2(256,columns())),
    each(match(true()), $$ = first($$))) ~> DistinctRows
```

### <a name="check-for-nulls-in-all-columns"></a>모든 열에서 Null 확인
이 코드 조각은 일반적으로 모든 열에서 NULL 값을 확인하기 위해 데이터 흐름에 붙여넣을 수 있는 코드 조각입니다. 이 기법은 스키마 드리프트를 활용하여 모든 행에서 모든 열을 확인하고 조건부 분할을 사용하여 NULL이 있는 행과 NULL이 없는 행을 구분합니다. 

```
split(contains(array(columns()),isNull(#item)),
    disjoint: false) ~> LookForNULLs@(hasNULLs, noNULLs)
```

### <a name="automap-schema-drift-with-a-select"></a>Select를 사용하여 스키마 드리프트 자동 매핑
들어오는 열을 알 수 없거나 동적 집합에서 기존 데이터베이스 스키마를 로드해야 하는 경우에는 싱크 변환의 오른쪽 열을 매핑해야 합니다. 이는 기존 테이블을 로드하는 경우에만 필요합니다. 열을 자동으로 매핑하는 Select를 만들려면 싱크 앞에 이 코드 조각을 추가합니다. 싱크 매핑을 자동 매핑으로 유지합니다.

```
select(mapColumn(
        each(match(true()))
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> automap
```

### <a name="persist-column-data-types"></a>열 데이터 형식 유지
데이터 흐름의 열 이름 및 데이터 형식을 싱크를 사용하여 영구 저장소에 저장하려면 파생 열 정의 내에 이 스크립트를 추가합니다.

```
derive(each(match(type=='string'), $$ = 'string'),
    each(match(type=='integer'), $$ = 'integer'),
    each(match(type=='short'), $$ = 'short'),
    each(match(type=='complex'), $$ = 'complex'),
    each(match(type=='array'), $$ = 'array'),
    each(match(type=='float'), $$ = 'float'),
    each(match(type=='date'), $$ = 'date'),
    each(match(type=='timestamp'), $$ = 'timestamp'),
    each(match(type=='boolean'), $$ = 'boolean'),
    each(match(type=='long'), $$ = 'long'),
    each(match(type=='double'), $$ = 'double')) ~> DerivedColumn1
```

### <a name="fill-down"></a>자동 채우기
다음은 NULL 값을 시퀀스에서 NULL이 아닌 이전 값으로 바꾸려는 경우 데이터 세트를 사용하여 일반적인 '아래로 채우기' 문제를 구현하는 방법입니다. 이 작업은 '더미' 범주 값을 사용하여 전체 데이터 세트에 대한 가상 창을 만들어야 하기 때문에 성능이 저하될 수 있습니다. 또한 값을 기준으로 정렬하여 NULL이 아닌 이전 값을 찾기 위한 적절한 데이터 시퀀스를 만들어야 합니다. 아래 코드 조각은 '더미'로 가상 범주를 만들고 서로게이트 키를 기준으로 정렬합니다. 서로게이트 키를 제거하고 고유한 데이터별 정렬 키를 사용할 수 있습니다. 이 코드 조각에서는 ```source1```이라는 소스 변환을 이미 추가했다고 가정합니다.

```
source1 derive(dummy = 1) ~> DerivedColumn
DerivedColumn keyGenerate(output(sk as long),
    startAt: 1L) ~> SurrogateKey
SurrogateKey window(over(dummy),
    asc(sk, true),
    Rating2 = coalesce(Rating, last(Rating, true()))) ~> Window1
```

### <a name="moving-average"></a>이동 평균
이동 평균은 창 변환을 사용하여 데이터 흐름에서 매우 쉽게 구현할 수 있습니다. 아래 예제에서는 Microsoft 주가의 15일 이동 평균을 만듭니다.

```
window(over(stocksymbol),
    asc(Date, true),
    startRowOffset: -7L,
    endRowOffset: 7L,
    FifteenDayMovingAvg = round(avg(Close),2)) ~> Window1
```

### <a name="distinct-count-of-all-column-values"></a>모든 열 값의 고유 개수
이 스크립트를 사용하여 키 열을 확인하고 단일 스크립트 조각으로 스트림에 있는 모든 열의 카디널리티를 볼 수 있습니다. 이 스크립트를 집계 변환으로 데이터 흐름에 추가하면 자동으로 모든 열의 고유 개수가 제공됩니다.

```
aggregate(each(match(true()), $$ = countDistinct($$))) ~> KeyPattern
```

### <a name="compare-previous-or-next-row-values"></a>이전 또는 다음 행 값 비교
이 샘플 코드 조각에서는 현재 행의 열 값과 현재 행의 열 값을 비교 하는 데 창 변환을 사용할 수 있는 방법을 보여 줍니다. 이 예에서는 파생 열을 사용 하 여 전체 데이터 집합에서 창 파티션을 사용할 수 있도록 더미 값을 생성 합니다. 서로게이트 키 변환은 각 행에 대 한 고유 키 값을 할당 하는 데 사용 됩니다. 데이터 변환에이 패턴을 적용 하는 경우 데이터를 분할 하는 데 사용할 열이 있는 경우 서로게이트 키를 제거 하 고, 데이터를 분할 하는 데 사용할 열이 있는 경우 파생 열을 제거할 수 있습니다.

```
source1 keyGenerate(output(sk as long),
    startAt: 1L) ~> SurrogateKey1
SurrogateKey1 derive(dummy = 1) ~> DerivedColumn1
DerivedColumn1 window(over(dummy),
    asc(sk, true),
    prevAndCurr = lag(title,1)+'-'+last(title),
        nextAndCurr = lead(title,1)+'-'+last(title)) ~> leadAndLag
```

## <a name="next-steps"></a>다음 단계

[데이터 흐름 개요 문서](concepts-data-flow-overview.md)부터 시작하여 데이터 흐름을 탐색합니다.
