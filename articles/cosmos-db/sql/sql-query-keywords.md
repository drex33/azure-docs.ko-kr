---
title: Azure Cosmos DB용 SQL 키워드
description: Azure Cosmos DB용 SQL 키워드에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/20/2021
ms.author: tisande
ms.openlocfilehash: 680b383cee86ec4233579b9305c47e60ad4d5ff4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625349"
---
# <a name="keywords-in-azure-cosmos-db"></a>Azure Cosmos DB의 키워드
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에서는 Azure Cosmos DB SQL 쿼리에서 사용할 수 있는 키워드에 대해 자세히 설명합니다.

## <a name="between"></a>BETWEEN

`BETWEEN` 키워드를 사용하면 문자열 또는 숫자 값 범위에 대한 쿼리를 표현할 수 있습니다. 예를 들어 다음 쿼리는 첫 번째 자식의 등급이 1~5(포함)인 항목을 모두 반환합니다.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

다음 예제와 같이 `SELECT` 절에서 `BETWEEN` 키워드를 사용할 수도 있습니다.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

SQL API에서는 ANSI SQL과 달리 혼합 형식의 속성에 대한 범위 쿼리를 표현할 수 있습니다. 예를 들어 `grade`는 항목에 따라 `5` 같은 숫자이거나 `grade4` 같은 문자열일 수 있습니다. 이 경우 JavaScript에서와 같이 서로 다른 두 형식을 비교할 때 `Undefined`가 발생하므로 항목을 건너뜁니다.

## <a name="distinct"></a>DISTINCT

`DISTINCT` 키워드는 쿼리의 프로젝션에서 중복 항목을 제거합니다.

이 예제에서 쿼리는 각 성의 값을 프로젝션합니다.

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

결과는 다음과 같습니다.

```json
[
    "Andersen"
]
```

고유한 개체를 프로젝트할 수도 있습니다. 이 경우 두 문서 중 하나에 lastName 필드가 없으므로 쿼리에서 빈 개체가 반환됩니다.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

결과는 다음과 같습니다.

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

하위 쿼리 내의 프로젝션에서 `DISTINCT`를 사용할 수도 있습니다.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

이 쿼리는 중복 항목을 제거하여 각 자식의 givenName이 포함된 배열을 프로젝션합니다. 배열에 ChildNames라는 별칭이 지정되고 외부 쿼리에 프로젝션됩니다.

결과는 다음과 같습니다.

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

집계 시스템 함수 및 가 있는 하위 쿼리가 있는 `DISTINCT` 쿼리는 특정 SDK 버전에서만 지원됩니다. 예를 들어 다음 셰이프의 쿼리는 아래의 특정 SDK 버전에서만 지원됩니다.

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

**지원되는 SDK 버전:**

|**SDK**|**지원되는 버전**|
|-------|----------------------|
|.NET SDK|3.18.0 이상|
|Java SDK|4.19.0 이상|
|Node.js SDK|지원되지 않음|
|Python SDK|지원되지 않음|

집계 시스템 함수 및 가 있는 하위 쿼리가 있는 쿼리에는 몇 가지 추가 제한 사항이 있습니다. `DISTINCT`

|**제한 사항**|**예제**|
|-------|----------------------|
|외부 쿼리의 WHERE 절|SELECT COUNT(1) FROM (SELECT DISTINCT VALUE c.lastName FROM c) AS lastName WHERE lastName = "Smith"|
|외부 쿼리의 ORDER BY 절|SELECT VALUE COUNT(1) FROM (SELECT DISTINCT VALUE c.lastName FROM c) AS lastName ORDER BY lastName|
|외부 쿼리의 GROUP BY 절|SELECT COUNT(1) as annualCount, d.year FROM (SELECT DISTINCT c.year, c.id FROM c) AS d GROUP BY d.year|
|중첩된 하위query|SELECT COUNT(1) FROM (SELECT y FROM (SELECT VALUE StringToNumber(SUBSTRING(d.date, 0, 4 FROM (SELECT DISTINCT c.date FROM c) d) AS y WHERE y > 2012)|
|여러 집계|SELECT COUNT(1) as AnnualCount, SUM(d.sales) as TotalSales FROM (SELECT DISTINCT c.year, c.sales, c.id FROM c) AS d|

## <a name="like"></a>LIKE

특정 문자열이 지정한 패턴과 일치하는지 여부에 따라 부울 값을 반환합니다. 패턴은 일반 문자와 와일드카드 문자를 포함할 수 있습니다. `LIKE` 키워드나 [RegexMatch](sql-query-regexmatch.md) 시스템 함수를 사용하여 논리적으로 동일한 쿼리를 작성할 수 있습니다. 어떤 방법을 선택하든 동일한 인덱스 사용이 관찰됩니다. 따라서 정규식보다 LIKE 구문을 더 선호하는 경우 `LIKE`를 사용해야 합니다.

> [!NOTE]
> `LIKE`는 인덱스를 사용할 수 있기 때문에 `LIKE`를 사용하여 비교하는 속성에 대해 [범위 인덱스를 만들](./../index-policy.md)어야 합니다.

LIKE와 함께 다음 와일드카드 문자를 사용할 수 있습니다.

| 와일드카드 문자 | Description                                                  | 예                                     |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------- |
| %                    | 0개 이상의 문자로 이루어진 문자열                      | WHERE   c.description LIKE   “%SO%PS%”      |
| _   (밑줄)     | 단일 문자                                       | WHERE   c.description LIKE   “%SO_PS%”      |
| [ ]                  | 지정한 범위([a-f]) 또는 집합([abcdef])에 있는 단일 문자 | WHERE   c.description LIKE   “%SO[t-z]PS%”  |
| [^]                  | 지정한 범위([^a-f]) 또는 집합([^abcdef])에 없는 단일 문자 | WHERE   c.description LIKE   “%SO[^abc]PS%” |


### <a name="using-like-with-the--wildcard-character"></a>LIKE와 % 와일드카드 문자 사용

`%` 문자는 0개 이상의 문자로 이루어진 문자열과 일치합니다. 예를 들어 패턴의 시작과 끝에 `%`를 배치하면 다음 쿼리는 설명에 `fruit`가 포함된 항목을 모두 반환합니다.

```sql
SELECT *
FROM c
WHERE c.description LIKE "%fruit%"
```

패턴의 끝에만 `%` 문자를 사용한 경우 설명이 `fruit`로 시작하는 항목만 반환됩니다.

```sql
SELECT *
FROM c
WHERE c.description LIKE "fruit%"
```


### <a name="using-not-like"></a>NOT LIKE 사용

아래 예제에서는 설명에 `fruit`가 포함되지 않은 항목을 모두 반환합니다.

```sql
SELECT *
FROM c
WHERE c.description NOT LIKE "%fruit%"
```

### <a name="using-the-escape-clause"></a>ESCAPE 절 사용

ESCAPE 절을 사용하여 하나 이상의 와일드카드 문자가 포함된 패턴을 검색할 수 있습니다. 예를 들어 `20-30%` 문자열이 포함된 설명을 검색하려는 경우 `%`를 와일드카드 문자로 해석하지 않을 수 있습니다.

```sql
SELECT *
FROM c
WHERE c.description LIKE '%20-30!%%' ESCAPE '!'
```

### <a name="using-wildcard-characters-as-literals"></a>와일드카드 문자를 리터럴로 사용

와일드카드 문자를 대괄호로 묶어 리터럴 문자로 처리할 수 있습니다. 와일드카드 문자를 대괄호로 묶으면 특수 특성이 제거됩니다. 몇 가지 예제는 다음과 같습니다.

| 무늬           | 의미 |
| ----------------- | ------- |
| LIKE   “20-30[%]” | 20~30%  |
| LIKE   “[_]n”     | _n      |
| LIKE   “[ [ ]”    | [       |
| LIKE   “]”        | ]       |

## <a name="in"></a>IN

IN 키워드를 사용하여 지정한 값과 일치하는 값이 목록에 있는지 확인할 수 있습니다. 예를 들어 다음 쿼리는 `id`가 `WakefieldFamily` 또는 `AndersenFamily`인 패밀리 항목을 모두 반환합니다.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

다음 예제에서는 상태가 지정한 값과 일치하는 항목을 모두 반환합니다.

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

SQL API는 FROM 소스의 IN 키워드를 통해 추가된 새 구문을 사용하여 [JSON 배열 반복](sql-query-object-array.md#Iteration)을 지원합니다.

`IN` 필터에 파티션 키를 포함하면 쿼리가 관련 파티션으로만 자동으로 필터링됩니다.

## <a name="top"></a>맨 위로 이동

TOP 키워드는 처음 `N`개의 쿼리 결과를 정의되지 않은 순서로 반환합니다. TOP에 `ORDER BY` 절을 사용하여 결과를 처음 `N`개의 정렬된 값으로 제한하는 것이 좋습니다. 두 절을 결합해야만 TOP의 영향을 받는 행을 예측 가능한 방식으로 나타낼 수 있습니다.

다음 예제와 같이 TOP에 상수 값을 사용하거나 매개 변수가 있는 쿼리를 포함하는 변수 값을 사용할 수 있습니다.

```sql
    SELECT TOP 1 *
    FROM Families f
```

결과는 다음과 같습니다.

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [조인](sql-query-join.md)
- [하위 쿼리](sql-query-subquery.md)
