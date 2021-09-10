---
title: Azure Cosmos DB의 SQL 하위 쿼리
description: Azure Cosmos DB의 SQL 하위 쿼리와 일반적인 사용 사례 및 다양한 유형의 하위 쿼리에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/30/2021
ms.author: tisande
ms.openlocfilehash: 33413334540661736a0b16172f40acc73d76af07
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567419"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Azure Cosmos DB에 대한 SQL 하위 쿼리 예
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

하위 쿼리는 다른 쿼리 내에 중첩된 쿼리입니다. 또한 하위 쿼리는 내부 쿼리 또는 내부 선택이 호출됩니다. 하위 쿼리가 포함된 문을 일반적으로 외부 쿼리라고 합니다.

이 문서에서는 Azure Cosmos DB의 SQL 하위 쿼리 및 일반적인 사용 사례에 대해 설명합니다. 이 문서의 모든 샘플 쿼리는 [샘플 영양 데이터 세트](https://github.com/AzureCosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)에 대해 실행할 수 있습니다.

## <a name="types-of-subqueries"></a>하위 쿼리의 유형

하위 쿼리에는 두 가지 주요 유형이 있습니다.

* **상관**: 외부 쿼리의 값을 참조하는 하위 쿼리입니다. 하위 쿼리는 외부 쿼리가 처리하는 각 행에 대해 한 번씩 평가됩니다.
* **비상관**: 외부 쿼리와 독립적인 하위 쿼리입니다. 외부 쿼리에 의존하지 않고 자체적으로 실행할 수 있습니다.

> [!NOTE]
> Azure Cosmos DB는 상호 관련된 하위 쿼리만 지원합니다.

하위 쿼리는 반환하는 행과 열의 수를 기준으로 추가 분류할 수 있습니다. 다음 세 가지 유형이 있습니다.
* **테이블**: 여러 행과 여러 열을 반환합니다.
* **다중 값**: 여러 행과 단일 열을 반환합니다.
* **스칼라**: 단일 행과 단일 열을 반환합니다.

Azure Cosmos DB의 SQL 쿼리는 항상 단일 열(단순 값 또는 복잡한 문서)을 반환합니다. 따라서 Azure Cosmos DB에서는 다중 값 및 스칼라 하위 쿼리만 적용할 수 있습니다. FROM 절에서만 다중 값 하위 쿼리를 관계식으로 사용할 수 있습니다. 스칼라 하위 쿼리를 SELECT 또는 WHERE 절에서 스칼라 식으로 사용하거나 FROM 절에서 관계식으로 사용할 수 있습니다.

## <a name="multi-value-subqueries"></a>다중 값 하위 쿼리

다중 값 하위 쿼리는 문서 세트를 반환하고 항상 FROM 절 내에서 사용됩니다. 다음 용도로 사용됩니다.

* JOIN 식 최적화. 
* 비용이 많이 드는 식을 한 번 평가하고 여러 번 참조.

## <a name="optimize-join-expressions"></a>JOIN 식 최적화

다중 값 하위 쿼리는 WHERE 절의 모든 크로스 조인 뒤가 아니라 각 select-many 식 뒤에 조건자를 푸시하여 JOIN 식을 최적화할 수 있습니다.

다음과 같은 쿼리를 고려해 보세요.

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

이 쿼리의 경우 인덱스는 이름이 "infant formula"인 태그가 있는 모든 문서와 일치합니다. 0에서 10 사이의 값을 갖는 영양 항목이며 1보다 큰 값의 서비스 항목입니다. 여기에서 JOIN 식은 필터를 적용하기 전에 일치하는 각 문서에 대해 태그, 영양소 및 1회 용량 배열의 모든 항목에 대한 교차곱을 수행합니다. 

그러면 WHERE 절은 각 <c, t, n, s> 튜플에 필터 조건자를 적용합니다. 예를 들어, 일치하는 문서가 3개의 배열에 각각 10개의 항목이 있는 경우 이는 1 x 10 x 10 x 10(즉, 1,000) 튜플로 확장됩니다. 여기서 하위 쿼리를 사용하여 다음 식으로 조인하기 전에 조인된 배열 항목을 필터링할 수 있습니다.

다음 쿼리는 위의 쿼리와 동일하지만 하위 쿼리를 사용합니다.

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

태그 배열의 한 항목만 필터와 일치하고 영양소 및 1회 용량 배열 모두 5개 항목이 있다고 가정합니다. JOIN 식은 첫 번째 쿼리의 1,000개 항목과 달리 1 x 1 x 5 x 5 = 25개 항목으로 확장됩니다.

## <a name="evaluate-once-and-reference-many-times"></a>한 번 평가하고 여러 번 참조

하위 쿼리는 UDF(사용자 정의 함수), 복잡한 문자열 또는 산술 식과 같은 비용이 많이 드는 식으로 쿼리를 최적화하는 데 도움이 될 수 있습니다. JOIN 식과 함께 하위 쿼리를 사용하여 식을 한 번 평가하지만 여러 번 참조할 수 있습니다.

다음 쿼리는 UDF `GetMaxNutritionValue`를 두 번 실행합니다.

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

다음은 UDF를 한 번만 실행하는 동일한 쿼리입니다.

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> JOIN 식의 교차곱 동작에 유의합니다. UDF 식이 정의되지 않은 것으로 평가될 수 있는 경우 JOIN 식이 값을 직접 반환하지 않고 하위 쿼리에서 개체를 반환하여 항상 단일 행을 생성하도록 해야 합니다.
>

다음은 값이 아닌 개체를 반환하는 유사한 예입니다.

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

접근 방식은 UDF로 제한되지 않습니다. 잠재적으로 비용이 많이 드는 모든 식에 적용됩니다. 예를 들어 수학 함수 `avg`에 대해 동일한 접근 방식을 수행할 수 있습니다.

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>외부 참조 데이터를 사용한 모방 조인

측정 단위 또는 국가 코드와 같이 거의 변경되지 않는 정적 데이터를 참조해야 하는 경우가 종종 있습니다. 각 문서에 이러한 데이터를 복제하지 않는 것이 좋습니다. 이 중복을 피하면 스토리지가 절약되고 문서 크기를 더 작게 유지하여 쓰기 성능이 향상됩니다. 하위 쿼리를 사용하여 참조 데이터 컬렉션으로 내부 조인 의미 체계를 모방할 수 있습니다.

예를 들어 다음과 같은 참조 데이터 세트를 고려합니다.

| **단위** | **이름**            | **승수** | **기준 단위** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | 나노그램            | 1.00E-09       | 그램          |
| µg       | Microgram           | 1.00E-06       | 그램          |
| mg       | 밀리그램           | 1.00E-03       | 그램          |
| g        | 그램                | 1.00E+00       | 그램          |
| kg       | 킬로그램            | 1.00E+03       | 그램          |
| Mg       | 메가그램            | 1.00E+06       | 그램          |
| Gg       | 기가그램            | 1.00E+09       | 그램          |
| nJ       | 나노줄           | 1.00E-09       | 줄         |
| µJ       | 마이크로줄          | 1.00E-06       | 줄         |
| mJ       | 밀리줄          | 1.00E-03       | 줄         |
| J        | 줄               | 1.00E+00       | 줄         |
| kJ       | 킬로줄           | 1.00E+03       | 줄         |
| MJ       | 메가줄           | 1.00E+06       | 줄         |
| GJ       | 기가줄           | 1.00E+09       | 줄         |
| cal      | 칼로리             | 1.00E+00       | 칼로리       |
| kcal     | 칼로리             | 1.00E+03       | 칼로리       |
| IU       | 국제 단위 |                |               |


다음 쿼리는 이 데이터와의 조인을 모방하여 출력에 단위 이름을 추가합니다.

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>스칼라 하위 쿼리

스칼라 하위 쿼리 식은 단일 값으로 평가되는 하위 쿼리입니다. 스칼라 하위 쿼리 식의 값은 하위 쿼리의 프로젝션(SELECT 절) 값입니다.  스칼라 식이 유효한 여러 위치에서 스칼라 하위 쿼리 식을 사용할 수 있습니다. 예를 들어 SELECT 및 WHERE 절의 모든 식에서 스칼라 하위 쿼리를 사용할 수 있습니다.

하지만 스칼라 하위 쿼리를 사용하는 것이 항상 최적화에 도움이 되는 것은 아닙니다. 예를 들어 시스템 또는 사용자 정의 함수에 인수로 스칼라 하위 쿼리를 전달하면 RU(리소스 단위) 사용 또는 대기 시간에 이점이 없습니다.

스칼라 하위 쿼리는 다음과 같이 추가로 분류할 수 있습니다.
* 단순 식 스칼라 하위 쿼리
* 스칼라 하위 쿼리 집계

## <a name="simple-expression-scalar-subqueries"></a>단순 식 스칼라 하위 쿼리

단순 식 스칼라 하위 쿼리는 집계 식을 포함하지 않는 SELECT 절이 있는 상호 관련된 하위 쿼리입니다. 컴파일러가 하나의 더 큰 단순 식으로 변환하기 때문에 이러한 하위 쿼리는 최적화 이점을 제공하지 않습니다. 내부 쿼리와 외부 쿼리 간에 상호 관련된 컨텍스트가 없습니다.

다음은 몇 가지 예입니다.

**예 1**

```sql
SELECT 1 AS a, 2 AS b
```

단순 식 스칼라 하위 쿼리를 사용하여 이 쿼리를 다음과 같이 다시 작성할 수 있습니다.

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

두 쿼리 모두 다음 출력을 생성합니다.

```json
[
  { "a": 1, "b": 2 }
]
```

**예 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

단순 식 스칼라 하위 쿼리를 사용하여 이 쿼리를 다음과 같이 다시 작성할 수 있습니다.

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

쿼리 출력:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**예 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

단순 식 스칼라 하위 쿼리를 사용하여 이 쿼리를 다음과 같이 다시 작성할 수 있습니다.

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

쿼리 출력:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>스칼라 하위 쿼리 집계

집계 스칼라 하위 쿼리는 단일 값으로 평가되는 프로젝션 또는 필터에 집계 함수가 있는 하위 쿼리입니다.

**예제 1:**

다음은 프로젝션에 단일 집계 함수 식이 있는 하위 쿼리입니다.

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

쿼리 출력:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**예 2**

다음은 여러 집계 함수 식이 있는 하위 쿼리입니다.

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

쿼리 출력:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**예 3**

다음은 프로젝션과 필터 모두에서 집계 하위 쿼리가 있는 쿼리입니다.

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

쿼리 출력:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

이 쿼리를 작성하는 더 최적의 방법은 하위 쿼리에 조인하고 SELECT 및 WHERE 절 모두에서 하위 쿼리 별칭을 참조하는 것입니다. 이 쿼리는 프로젝션과 필터가 아닌 조인 문 내에서만 하위 쿼리를 실행해야 하므로 더 효율적입니다.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>EXISTS 식

Azure Cosmos DB는 EXISTS 식을 지원합니다. Azure Cosmos DB SQL API에 기본 제공되는 집계 스칼라 하위 쿼리입니다. EXISTS는 하위 쿼리 식을 사용하고 하위 쿼리가 행을 반환하는 경우 true를 반환하는 부울 식입니다. 그렇지 않으면 false를 반환합니다.

Azure Cosmos DB SQL API는 부울 식과 다른 스칼라 식을 구분하지 않으므로 SELECT 및 WHERE 절 모두에서 EXISTS를 사용할 수 있습니다. 이는 부울 식(예: EXISTS, BETWEEN 및 IN)이 필터로 제한되는 T-SQL과 다릅니다.

EXISTS 하위 쿼리가 정의되지 않은 단일 값을 반환하면 EXISTS는 false로 평가됩니다. 예를 들어 false로 평가되는 다음 쿼리를 고려합니다.
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


이전 하위 쿼리의 VALUE 키워드가 생략되면 쿼리는 true로 평가됩니다.
```sql
SELECT EXISTS (SELECT undefined) 
```

하위 쿼리는 개체의 선택한 목록에 있는 값 목록을 묶습니다. 선택한 목록에 값이 없으면 하위 쿼리는 단일 값 ‘{}’를 반환합니다. 이 값은 정의되어 있으므로 EXISTS는 true로 평가됩니다.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>예: ARRAY_CONTAINS 및 JOIN을 EXISTS로 다시 쓰기

ARRAY_CONTAINS의 일반적인 사용 사례는 배열에 있는 항목의 존재를 기준으로 문서를 필터링하는 것입니다. 이 경우 태그 배열에 "orange"라는 항목이 포함되어 있는지 확인하게 됩니다.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

EXISTS를 사용하도록 동일한 쿼리를 다시 작성할 수 있습니다.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

또한 ARRAY_CONTAINS는 값이 배열 내의 요소와 같은지 여부만 확인할 수 있습니다. 배열 속성에 대해 더 복잡한 필터가 필요한 경우 JOIN을 사용합니다.

배열의 단위 및 `nutritionValue` 속성을 기반으로 필터링하는 다음 쿼리를 고려합니다. 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

컬렉션의 각 문서에 대해 배열 요소를 사용하여 교차곱이 수행됩니다. 이 JOIN 작업을 통해 배열 내의 속성을 필터링할 수 있습니다. 그러나 이 쿼리 RU 사용은 상당할 것입니다. 예를 들어 1,000개의 문서에 각 배열에 100개의 항목이 있는 경우 1,000 x 100(즉, 100,000) 튜플로 확장됩니다.

EXISTS를 사용하면 비용이 많이 드는 이 교차곱을 피하는 데 도움이 될 수 있습니다.

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

이 경우 EXISTS 하위 쿼리 내의 배열 요소를 필터링합니다. 배열 요소가 필터와 일치하면 이를 프로젝션하고 EXISTS는 true로 평가됩니다.

EXISTS의 별칭을 지정하고 프로젝션에서 참조할 수도 있습니다.

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

쿼리 출력:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>ARRAY 식

ARRAY 식을 사용하여 쿼리 결과를 배열로 프로젝션할 수 있습니다. 이 식은 쿼리의 SELECT 절 내에서만 사용할 수 있습니다.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

쿼리 출력:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

다른 하위 쿼리와 마찬가지로 ARRAY 식이 있는 필터가 가능합니다.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

쿼리 출력:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

배열 식은 하위 쿼리에서 FROM 절 뒤에 올 수도 있습니다.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

쿼리 출력:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [모델 문서 데이터](modeling-data.md)
