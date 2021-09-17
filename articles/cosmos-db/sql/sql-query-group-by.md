---
title: Azure Cosmos DB의 GROUP BY 절
description: Azure Cosmos DB의 GROUP BY 절에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/01/2021
ms.author: tisande
ms.openlocfilehash: 50d30ac0636f74715b3472c35b2801ac6452b624
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123428815"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Azure Cosmos DB의 GROUP BY 절
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

GROUP BY 절은 하나 이상의 지정된 속성 값에 따라 쿼리의 결과를 나눕니다.

> [!NOTE]
> GROUP BY 절은 Azure Cosmos DB Python SDK에서 지원되지 않습니다.

## <a name="syntax"></a>구문

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>인수

- `<scalar_expression_list>`

   쿼리 결과를 나누는 데 사용되는 식을 지정합니다.

- `<scalar_expression>`
  
   스칼라 하위 쿼리 및 스칼라 집계를 제외한 모든 스칼라 식이 허용됩니다. 각 스칼라 식에는 적어도 하나 이상의 속성 참조가 포함되어야 합니다. 개별 식의 수나 각 식의 카디널리티에는 제한이 없습니다.

## <a name="remarks"></a>설명
  
  쿼리에서 GROUP BY 절을 사용하는 경우 SELECT 절은 GROUP BY 절에 포함된 속성 및 시스템 함수의 일부만 포함할 수 있습니다. 한 가지 예외는 [집계 함수](sql-query-aggregate-functions.md)이며, 이는 GROUP BY 절에 포함되지 않고 SELECT 절에 나타날 수 있습니다. SELECT 절에는 항상 리터럴 값을 포함할 수도 있습니다.

  GROUP BY 절은 SELECT, FROM 및 WHERE 절 뒤와 OFFSET LIMIT 절 앞에 와야 합니다. 현재, GROUP BY 절을 ORDER BY 절과 함께 사용할 수 없으며 이러한 특성은 계획된 것입니다.
  
  GROUP BY 절은 다음을 허용하지 않습니다.
  
- 속성 별칭 지정 또는 시스템 함수 별칭 지정(SELECT 절 내의 별칭 지정은 계속 허용됨)
- 하위 쿼리
- 집계 시스템 함수(SELECT 절에서만 허용됨)

집계 시스템 함수를 사용하는 쿼리와 `GROUP BY`를 사용하는 하위 쿼리는 지원되지 않습니다. 예를 들어 다음 쿼리는 지원되지 않습니다.

```sql
SELECT COUNT(UniqueLastNames)
FROM (
SELECT AVG(f.age)
FROM f
GROUP BY f.lastName
) AS UniqueLastNames
```

또한 파티션 간 `GROUP BY` 쿼리는 최대 21개의 [집계 시스템 함수](sql-query-aggregate-functions.md)를 포함할 수 있습니다. 

## <a name="examples"></a>예

이 예에서는 [영양 데이터 세트](https://github.com/AzureCosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) 샘플을 사용합니다.

각 foodGroup의 총 항목 수를 반환하는 쿼리는 다음과 같습니다.

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

일부 결과(결과를 제한하는 데 TOP 키워드 사용):

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta"
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products"
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes"
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs"
    }
]
```

이 쿼리에는 결과를 나누는 데 사용되는 두 개의 식이 있습니다.

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

결과는 다음과 같습니다.

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta",
        "version": 1
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products",
        "version": 1
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes",
        "version": 1
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs",
        "version": 1
    }
]
```

이 쿼리는 GROUP BY 절에 시스템 함수를 포함합니다.

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

결과는 다음과 같습니다.

```json
[
    {
        "foodGroupCount": 183,
        "upperFoodGroup": "CEREAL GRAINS AND PASTA"
    },
    {
        "foodGroupCount": 133,
        "upperFoodGroup": "NUT AND SEED PRODUCTS"
    },
    {
        "foodGroupCount": 113,
        "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
    },
    {
        "foodGroupCount": 64,
        "upperFoodGroup": "SPICES AND HERBS"
    }
]
```

이 쿼리는 item 속성 식에서 키워드 및 시스템 함수를 둘 다 사용합니다.

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

결과는 다음과 같습니다.

```json
[
    {
        "foodGroupCount": 10,
        "containsOrangeTag": true,
        "correctVersion": true
    },
    {
        "foodGroupCount": 8608,
        "containsOrangeTag": false,
        "correctVersion": true
    }
]
```

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [SELECT 절](sql-query-select.md)
- [집계 함수](sql-query-aggregate-functions.md)
