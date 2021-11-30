---
title: Azure Cosmos DB 인덱싱 정책
description: Azure Cosmos DB에서 자동 인덱싱 및 성능 향상을 위해 기본 인덱싱 정책을 구성 및 변경하는 방법을 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/13/2021
ms.author: tisande
ms.openlocfilehash: 31b107b4972b5ccc970660eb79e6f0eb8a618fa3
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133218694"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Azure Cosmos DB의 인덱싱 정책
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB에서 모든 컨테이너에는 컨테이너 항목을 인덱싱해야 하는 방법을 지정하는 인덱싱 정책이 있습니다. 새로 만든 컨테이너에 대한 기본 인덱싱 정책은 모든 항목의 모든 속성을 인덱싱하고 모든 문자열 또는 숫자에 대해 범위 인덱스를 적용합니다. 이를 통해 인덱싱 및 인덱스 관리를 사전에 고려하지 않고도 좋은 쿼리 성능을 얻을 수 있습니다.

사용자 요구 사항에 맞게 이 자동 동작을 재정의할 수 있는 상황이 있습니다. *인덱싱 모드* 를 설정하여 컨테이너의 인덱싱 정책을 사용자 지정하고, *속성 경로* 를 포함하거나 제외할 수 있습니다.

> [!NOTE]
> 이 문서에서 설명하는 인덱싱 정책 업데이트 방법은 Azure Cosmos DB의 SQL(Core) API에만 적용됩니다. [Azure Cosmos DB의 API for MongoDB](mongodb/mongodb-indexing.md)에서의 인덱싱에 대해 알아보세요.

## <a name="indexing-mode"></a>인덱싱 모드

Azure Cosmos DB는 다음 두 가지 연결 모드를 지원합니다.

- **일관성**: 항목을 만들거나 업데이트하거나 삭제할 때 인덱스가 동기적으로 업데이트됩니다. 즉, 읽기 쿼리의 일관성은 [계정에 대해 구성된 일관성](consistency-levels.md)이 됩니다.
- **없음**: 컨테이너에서 인덱싱을 사용하지 않습니다. 이 모드는 보조 인덱스 없이 컨테이너를 순수 키-값 저장소로 사용하는 경우에 일반적으로 사용됩니다. 이를 사용하여 대량 작업의 성능을 향상시킬 수도 있습니다. 대량 작업이 완료되면 인덱스 모드를 일관성으로 설정한 다음 완료될 때까지 [IndexTransformationProgress](how-to-manage-indexing-policy.md#dotnet-sdk)를 사용하여 모니터링할 수 있습니다.

> [!NOTE]
> Azure Cosmos DB는 지연 인덱싱 모드도 지원합니다. 지연 인덱싱은 엔진이 다른 작업을 수행하지 않을 때 훨씬 낮은 우선 순위 수준으로 인덱스 업데이트를 수행합니다. 이로 인해 **불일치 또는 불완전** 쿼리 결과가 발생할 수 있습니다. Cosmos 컨테이너를 쿼리하려면 지연 인덱스를 선택하지 않아야 합니다. 새 컨테이너는 지연 인덱싱을 선택할 수 없습니다. cosmoslazyindexing@microsoft.com에 문의하여 예외를 요청할 수 있습니다(지연 인덱싱을 지원하지 않는 [서버리스](serverless.md) 모드에서 Azure Cosmos 계정을 사용하는 경우 제외).

기본적으로 인덱싱 정책은 `automatic`으로 설정됩니다. 인덱싱 정책에서 `automatic` 속성을 `true`로 설정하면 됩니다. 이 속성을로 설정 `true` 하면 Azure Cosmos DB 작성 된 문서를 자동으로 인덱싱할 수 있습니다.

## <a name="index-size"></a><a id="index-size"></a>인덱스 크기

Azure Cosmos DB에서 사용된 총 스토리지는 데이터 크기와 인덱스 크기의 조합입니다. 인덱스 크기의 몇 가지 기능은 다음과 같습니다.

* 인덱스 크기는 인덱싱 정책에 따라 달라집니다. 모든 속성이 인덱싱되는 경우 인덱스 크기가 데이터 크기보다 클 수 있습니다.
* 데이터가 삭제되면 인덱스는 거의 지속적으로 압축됩니다. 그러나 작은 데이터 삭제의 경우 인덱스 크기의 감소가 즉시 보이지 않을 수 있습니다.
* 실제 파티션이 분할되면 인덱스 크기가 일시적으로 늘어날 수 있습니다. 파티션 분할이 완료되면 인덱스 공간이 해제됩니다.

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>속성 경로 포함 및 제외

사용자 지정 인덱싱 정책은 인덱싱에 명시적으로 포함되거나 인덱싱에서 명시적으로 제외되는 속성 경로를 지정할 수 있습니다. 인덱싱되는 경로 수를 최적화하면 쓰기 작업의 대기 시간 및 RU 요금을 크게 줄일 수 있습니다. 이러한 경로는 [인덱싱 개요 섹션에 설명된 방법](index-overview.md#from-trees-to-property-paths)에 따라 정의되며, 다음이 추가됩니다.

- 스칼라 값(문자열 또는 숫자)으로 연결되는 경로는 `/?`로 끝납니다
- 배열의 요소는 `/0`, `/1` 등 대신 `/[]` 표기법을 통해 함께 처리됩니다
- `/*` 와일드카드를 사용하여 노드 아래의 어떤 요소도 일치시킬 수 있습니다

같은 예를 다시 사용하면 다음과 같습니다.

```
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

- `headquarters`의 `employees` 경로는 `/headquarters/employees/?`입니다

- `locations`의 `country` 경로는 `/locations/[]/country/?`입니다

- `headquarters` 아래 있는 모든 항목의 경로는 `/headquarters/*`입니다

예를 들어 `/headquarters/employees/?` 경로를 포함할 수 있습니다. 이 경로는 employees 속성을 인덱싱하는 것을 보장하지만 이 속성 내에서 중첩된 추가 JSON은 인덱싱하지 않습니다.

## <a name="includeexclude-strategy"></a>포함/제외 전략

모든 인덱싱 정책은 루트 경로 `/*`를 포함된 경로 또는 제외된 경로로 포함해야 합니다.

- 인덱싱할 필요가 없는 경로를 선택적으로 제외하려면 루트 경로를 포함합니다. 이 방법은 모델에 추가할 수 있는 새 속성을 Azure Cosmos DB가 사전에 인덱싱할 수 있으므로 권장됩니다.
- 인덱싱해야 하는 경로를 선택적으로 포함하려면 루트 경로를 제외합니다.

- 영숫자 문자와 _(밑줄)을 포함하는 정규 문자가 있는 경로에서는 큰따옴표 앞뒤의 경로 문자열을 이스케이프할 필요가 없습니다(예: "/path/?"). 다른 특수 문자를 포함하는 경로의 경우 큰따옴표 앞뒤의 경로 문자열을 이스케이프해야 합니다(예: "/ \"path-abc\"/?"). 경로에 특수 문자가 필요한 경우 안전을 위해 모든 경로를 이스케이프할 수 있습니다. 모든 경로를 이스케이프하거나 특수 문자를 포함하는 경로만 이스케이프하거나 기능상 아무런 차이가 없습니다.

- 시스템 속성 `_etag`는 인덱싱에 포함되는 경로에 etag를 추가하지 않는 한 기본적으로 인덱싱에서 제외됩니다.

- 인덱싱 모드가 **일관성** 으로 설정된 경우 시스템 속성 `id` 및 `_ts`가 자동으로 인덱싱됩니다.

경로를 포함하거나 제외할 때 다음과 같은 특성이 발생할 수 있습니다.

- `kind`는 `range` 또는 `hash`일 수 있습니다. 해시 인덱스 지원은 같음 필터로 제한됩니다. 범위 인덱스 기능은 해시 인덱스의 모든 기능뿐만 아니라 효율적인 정렬, 범위 필터, 시스템 함수도 제공합니다. 항상 범위 인덱스를 사용하는 것이 좋습니다.

- `precision`은 포함된 경로의 인덱스 수준에서 정의된 숫자입니다. 값 `-1`은 최대 정밀도를 나타냅니다. 이 값을 항상 `-1`로 설정하는 것이 좋습니다.

- `dataType`은 `String` 또는 `Number`일 수 있습니다. 이는 인덱싱되는 JSON 속성의 형식을 나타냅니다.

이러한 속성 설정은 이제 필요하지 않습니다. 지정하지 않으면 이러한 속성은 다음과 같은 기본값을 갖습니다.

| **속성 이름**     | **기본값** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` 및 `Number` |

경로를 포함하고 제외하는 인덱싱 정책의 예는 [이 섹션](how-to-manage-indexing-policy.md#indexing-policy-examples)을 참조하세요.

## <a name="includeexclude-precedence"></a>포함/제외 우선 순위

포함된 경로와 제외된 경로에 충돌이 있는 경우 보다 정확한 경로가 우선 적용됩니다.

예를 들면 다음과 같습니다.

**포함된 경로**: `/food/ingredients/nutrition/*`

**제외된 경로**: `/food/ingredients/*`

이 경우 포함된 경로가 보다 정확하기 때문에 제외된 경로보다 우선 적용됩니다. 이러한 경로를 기반으로 하는 `food/ingredients` 경로에 있거나 안에서 중첩된 모든 데이터는 인덱스에서 제외됩니다. 포함된 경로인 `/food/ingredients/nutrition/*` 내의 데이터는 예외이며 인덱싱됩니다.

다음은 Azure Cosmos DB의 포함 및 제외 경로 우선 순위에 대한 몇 가지 규칙입니다.

- 더 깊은 경로는 더 좁은 경로보다 더 정확합니다. 예를 들어 `/a/b/?` 는 `/a/?`보다 더 정확합니다.

- `/?`가 `/*`보다 더 정확합니다. 예를 들어 `/a/?`가 `/a/*`보다 더 정확하므로 `/a/?`가 우선 적용됩니다.

- 경로 `/*`는 포함된 경로 또는 제외된 경로여야 합니다.

## <a name="spatial-indexes"></a>공간 인덱스

인덱싱 정책에 공간 경로를 정의하는 경우 해당 경로에 적용해야 하는 인덱스 ```type```을 정의해야 합니다. 공간 인덱스에 사용할 수 있는 형식은 다음과 같습니다.

* Point

* Polygon

* MultiPolygon

* LineString

기본적으로 Azure Cosmos DB는 공간 인덱스를 만들지 않습니다. 공간 SQL 기본 제공 함수를 사용하려면 필요한 속성에 공간 인덱스를 만들어야 합니다. 공간 인덱스를 추가하는 인덱싱 정책 예제는 [이 섹션](sql-query-geospatial-index.md)을 참조하세요.

## <a name="composite-indexes"></a>복합 인덱스

속성이 두 개 이상인 `ORDER BY` 절이 있는 쿼리에는 복합 인덱스가 필요합니다. 복합 인덱스를 정의하여 여러 같음 및 범위 쿼리의 성능을 향상시킬 수도 있습니다. 기본적으로 복합 인덱스는 정의되지 않으므로 필요에 따라 [복합 인덱스를 추가](how-to-manage-indexing-policy.md#composite-index)해야 합니다.

포함된 경로나 제외된 경로와 달리 `/*` 와일드카드를 사용하여 경로를 만들 수 없습니다. 모든 복합 경로의 끝에는 지정할 필요가 없는 암시적 `/?`가 있습니다. 복합 경로는 스칼라 값으로 연결되고, 이 값은 복합 인덱스에 포함되는 유일한 값입니다.

복합 인덱스를 정의할 때는 다음을 지정합니다.

- 두 개 이상의 속성 경로. 속성 경로가 정의되는 시퀀스가 중요합니다.

- 순서(오름차순 또는 내림차순).

> [!NOTE]
> 복합 인덱스를 추가하는 경우 쿼리는 새 복합 인덱스 추가가 완료될 때까지 기존 범위 인덱스를 활용합니다. 따라서 복합 인덱스를 추가하는 경우 성능 향상이 즉시 관찰되지 않을 수 있습니다. [SDK 중 하나를 사용](how-to-manage-indexing-policy.md)하여 인덱스 변환의 진행률을 추적할 수 있습니다.

### <a name="order-by-queries-on-multiple-properties"></a>여러 속성에 대한 쿼리 정렬 기준:

속성이 둘 이상인 `ORDER BY` 절이 있는 쿼리에 복합 인덱스를 사용하는 경우 다음 고려 사항을 사용합니다.

- 복합 인덱스 경로가 `ORDER BY` 절의 속성 시퀀스와 일치하지 않는 경우 복합 인덱스가 쿼리를 지원할 수 없습니다.

- 복합 인덱스 경로의 순서(오름차순 또는 내림차순)는 `ORDER BY` 절의 `order`와도 일치해야 합니다.

- 복합 인덱스는 모든 경로에서 순서가 반대인 `ORDER BY` 절도 지원합니다.

복합 인덱스가 속성 name, age, _ts에서 정의되는 다음 예를 살펴보세요.

| **복합 인덱스**     | **샘플 `ORDER BY` 쿼리**      | **복합 인덱스에서 지원되나요?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

필요한 모든 `ORDER BY` 쿼리를 제공할 수 있도록 인덱싱 정책을 사용자 지정해야 합니다.

### <a name="queries-with-filters-on-multiple-properties"></a>여러 속성에 대한 필터가 있는 쿼리

쿼리에 둘 이상의 속성에 대한 필터가 있는 경우 이러한 속성의 복합 인덱스를 만드는 것이 유용할 수 있습니다.

예를 들어 같음 필터와 범위 필터가 모두 있는 다음 쿼리를 생각해 보세요.

```sql
SELECT *
FROM c
WHERE c.name = "John" AND c.age > 18
```

이 쿼리는 `(name ASC, age ASC)`에서 복합 인덱스를 활용할 수 있는 경우 시간이 더 적게 걸리고 더 적은 RU를 사용하기 때문에 보다 효율적입니다.

여러 범위 필터가 있는 쿼리는 복합 인덱스를 사용하여 최적화할 수도 있습니다. 그러나 개별 복합 인덱스는 단일 범위 필터만 최적화할 수 있습니다. 범위 필터에는 `>`, `<`, `<=`, `>=` 및 `!=`가 포함됩니다. 범위 필터는 복합 인덱스에서 마지막으로 정의되어야 합니다.

같음 필터 하나와 범위 필터 두 개가 있는 다음 쿼리를 생각해 보세요.

```sql
SELECT *
FROM c
WHERE c.name = "John" AND c.age > 18 AND c._ts > 1612212188
```

이 쿼리는 `(name ASC, age ASC)` 및 `(name ASC, _ts ASC)`에 대한 복합 인덱스를 사용하여 더 효율적입니다. 하지만 같음 필터가 있는 속성이 복합 인덱스에서 먼저 정의되어야 하므로 이 쿼리는 `(age ASC, name ASC)`에서 복합 인덱스를 활용하지 않습니다. 각 복합 인덱스는 단일 범위 필터만 최적화할 수 있으므로 `(name ASC, age ASC, _ts ASC)`에서는 단일 복합 인덱스 대신 별도의 복합 인덱스 두 개가 필요합니다.

여러 속성에 대한 필터가 있는 쿼리를 위한 복합 인덱스를 만들 때는 다음 사항을 고려해야 합니다.

- 필터 식은 여러 복합 인덱스를 사용할 수 있습니다.
- 쿼리의 필터에 있는 속성은 복합 인덱스의 속성과 일치해야 합니다. 속성이 복합 인덱스에 있지만 쿼리에 필터로 포함되지 않은 경우 쿼리는 복합 인덱스를 활용하지 않습니다.
- 쿼리의 필터에 복합 인덱스에서 정의되지 않은 추가 속성이 있는 경우 복합 인덱스와 범위 인덱스의 조합을 사용하여 쿼리를 평가합니다. 이 경우 범위 인덱스만을 사용할 때보다 적은 RU가 필요합니다.
- 속성에 범위 필터(`>`, `<`, `<=`, `>=` 또는 `!=`)가 있는 경우 이 속성은 복합 인덱스에서 마지막으로 정의되어야 합니다. 둘 이상의 범위 필터가 있는 쿼리는 여러 복합 인덱스를 활용할 수 있습니다.
- 여러 필터가 있는 쿼리를 최적화하기 위해 복합 인덱스를 만들 때 복합 인덱스의 `ORDER`는 결과에 영향을 주지 않습니다. 이 속성은 선택 사항입니다.

복합 인덱스가 속성 name, age, timestamp에서 정의되는 다음 예를 생각해 보세요.

| **복합 인덱스**     | **샘플 쿼리**      | **복합 인덱스에서 지원되나요?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name ASC, age ASC)```   | ```SELECT COUNT(1) FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |
| ```(name ASC, age ASC) and (name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp > 123049923``` | ```Yes```            |

### <a name="queries-with-a-filter-and-order-by"></a>필터 및 ORDER BY가 있는 쿼리

쿼리가 하나 이상의 속성에 대해 필터링하고 ORDER BY 절에서 다른 속성을 갖는 경우 필터의 속성을 `ORDER BY` 절에 추가하면 도움이 될 수 있습니다.

예를 들어 필터의 속성을 `ORDER BY` 절에 추가하면 다음 쿼리를 다시 작성하여 복합 인덱스를 활용할 수 있습니다.

범위 인덱스를 사용하여 쿼리:

```sql
SELECT *
FROM c 
WHERE c.name = "John" 
ORDER BY c.timestamp
```

복합 인덱스를 사용하여 쿼리:

```sql
SELECT * 
FROM c 
WHERE c.name = "John"
ORDER BY c.name, c.timestamp
```

개별 복합 인덱스는 최대 하나의 범위 필터만 지원할 수 있다는 점을 명심하면 필터를 사용하는 어떤 `ORDER BY` 쿼리에도 동일한 쿼리 최적화를 일반화할 수 있습니다.

범위 인덱스를 사용하여 쿼리:

```sql
SELECT * 
FROM c 
WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 1611947901 
ORDER BY c.timestamp
```

복합 인덱스를 사용하여 쿼리:

```sql
SELECT * 
FROM c 
WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 1611947901 
ORDER BY c.name, c.age, c.timestamp
```

또한 복합 인덱스를 사용하여 시스템 함수 및 ORDER BY가 있는 쿼리를 최적화할 수 있습니다.

범위 인덱스를 사용하여 쿼리:

```sql
SELECT * 
FROM c 
WHERE c.firstName = "John" AND Contains(c.lastName, "Smith", true) 
ORDER BY c.lastName
```

복합 인덱스를 사용하여 쿼리:

```sql
SELECT * 
FROM c 
WHERE c.firstName = "John" AND Contains(c.lastName, "Smith", true) 
ORDER BY c.firstName, c.lastName
```

필터 및 `ORDER BY` 절이 있는 쿼리를 최적화하기 위해 복합 인덱스를 만들 때는 다음 고려 사항이 적용됩니다.

* 한 속성에 대한 필터와 다른 속성을 사용 하는 별도의 `ORDER BY` 절이 있는 쿼리에 복합 인덱스를 정의하지 않아도 쿼리는 성공합니다. 하지만 복합 인덱스를 사용하면, 특히 `ORDER BY` 절의 속성에 높은 카디널리티가 있는 경우 쿼리의 RU 비용을 줄일 수 있습니다.
* 쿼리가 속성을 필터링하는 경우 이러한 속성은 `ORDER BY` 절에 먼저 포함되어야 합니다.
* 쿼리가 여러 속성을 필터링하는 경우 같음 필터가 `ORDER BY` 절의 첫 번째 속성이어야 합니다.
* 쿼리가 여러 속성을 필터링하는 경우 복합 인덱스당 최대 하나의 범위 필터 또는 시스템 함수를 사용할 수 있습니다. 범위 필터 또는 시스템 함수에 사용되는 속성은 복합 인덱스에서 마지막으로 정의되어야 합니다.
* 여러 속성에 대한 필터가 있는 쿼리뿐 아니라 여러 속성이 있는 `ORDER BY` 쿼리를 위해 복합 인덱스를 만들 때의 모든 고려 사항이 계속 적용됩니다.


| **복합 인덱스**                      | **샘플 `ORDER BY` 쿼리**                                  | **복합 인덱스에서 지원되나요?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" AND c.timestamp > 1589840355 ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(timestamp ASC, name ASC)```          | ```SELECT * FROM c WHERE c.timestamp > 1589840355 AND c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

### <a name="queries-with-a-filter-and-an-aggregate"></a>필터 하나와 집계 하나가 있는 쿼리 

쿼리가 하나 이상의 속성을 필터링하고 집계 시스템 함수를 포함하는 경우 필터 및 집계 시스템 함수의 속성에 대한 복합 인덱스를 만드는 것이 유용할 수 있습니다. 이 최적화는 [SUM](sql-query-aggregate-sum.md) 및 [AVG](sql-query-aggregate-avg.md) 시스템 함수에 적용됩니다.

필터 및 집계 시스템 함수가 있는 쿼리를 최적화하기 위해 복합 인덱스를 만들 때는 다음 고려 사항이 적용됩니다.

* 복합 인덱스는 집계가 있는 쿼리를 실행할 때 선택 사항입니다. 하지만 복합 인덱스를 사용하면 쿼리의 RU 비용을 크게 줄일 수 있는 경우가 많습니다.
* 쿼리가 여러 속성을 필터링하는 경우 같음 필터가 복합 인덱스의 첫 번째 속성이어야 합니다.
* 복합 인덱스당 최대 하나의 범위 필터를 사용할 수 있으며 범위 필터는 집계 시스템 함수의 속성에 있어야 합니다.
* 집계 시스템 함수의 속성은 복합 인덱스에서 마지막으로 정의되어야 합니다.
* `order`(`ASC` 또는 `DESC`)는 중요하지 않습니다.

| **복합 인덱스**                      | **샘플 쿼리**                                  | **복합 인덱스에서 지원되나요?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John"``` | `Yes` |
| ```(timestamp ASC, name ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John"``` | `No` |
| ```(name ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name > "John"``` | `No` |
| ```(name ASC, age ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John" AND c.age = 25``` | `Yes` |
| ```(age ASC, timestamp ASC)```          | ```SELECT AVG(c.timestamp) FROM c WHERE c.name = "John" AND c.age > 25``` | `No` |

## <a name="modifying-the-indexing-policy"></a><a id=index-transformation></a>인덱싱 정책 수정

컨테이너의 인덱싱 정책은 [Azure Portal 또는 지원되는 SDK 중 하나를 사용](how-to-manage-indexing-policy.md)하여 언제든지 업데이트할 수 있습니다. 인덱싱 정책에 대한 업데이트는 이전 인덱스에서 새 인덱스로의 변환을 트리거합니다. 이 변환은 온라인 및 현재 위치에서 수행되므로 작업 중에 추가 스토리지 공간이 사용되지 않습니다. 이전 인덱싱 정책은 컨테이너의 쓰기 가용성, 읽기 가용성 또는 프로비전된 처리량에 영향을 주지 않고 새 인덱싱 정책으로 효율적으로 변환됩니다. 인덱스 변환은 비동기 작업이며, 완료하는 데 걸리는 시간은 프로비전된 처리량, 항목 수 및 크기에 따라 달라집니다.

> [!IMPORTANT]
> 인덱스 변환은 [요청 단위](request-units.md)를 사용하는 작업입니다. 인덱스 변환에서 사용하는 요청 단위는 현재 [서버리스](serverless.md) 컨테이너를 사용하는 경우 요금이 청구되지 않습니다. 서버리스가 일반 공급되면 이러한 요청 단위에 요금이 청구됩니다.

> [!NOTE]
> Azure Portal에서 또는 [SDK 중 하나를 사용](how-to-manage-indexing-policy.md)하여 인덱스 변환의 진행률을 추적할 수 있습니다.

인덱스를 변환하는 동안에는 쓰기 가용성에 영향이 없습니다. 인덱스 변환은 프로비전된 RU를 사용하지만 CRUD 작업 또는 쿼리보다 낮은 우선 순위로 사용합니다.

새 인덱스 경로를 추가하는 경우 읽기 가용성에는 영향을 주지 않습니다. 인덱스 변환이 완료되면 쿼리에서 새 인덱스 경로만 활용합니다. 즉, 새 인덱싱된 경로를 추가할 때 해당 인덱싱된 경로를 활용하는 쿼리는 인덱스 변환 전과 도중에 동일한 성능을 갖습니다. 인덱스 변환이 완료된 후 쿼리 엔진은 새 인덱싱된 경로를 사용하기 시작합니다.

인덱싱된 경로를 제거할 때 모든 변경 사항을 하나의 인덱싱 정책 변환으로 그룹화해야 합니다. 여러 인덱스를 제거하고 하나의 단일 인덱싱 정책 변경에서 이를 수행하는 경우 쿼리 엔진은 인덱스 변환 전체에서 일관되고 완전한 결과를 제공합니다. 하지만 여러 인덱싱 정책 변경을 통해 인덱스를 제거하는 경우 쿼리 엔진은 모든 인덱스 변환이 완료될 때까지 일관되거나 완전한 결과를 제공하지 않습니다. 대부분의 개발자는 인덱스를 삭제한 다음 이러한 인덱스를 활용하는 쿼리를 즉시 실행하려 하지 않으므로 실제로 이 상황은 가능성이 낮습니다.

인덱싱된 경로를 삭제하면 쿼리 엔진이 즉시 사용을 중지하고 대신 전체 검사를 수행합니다.

> [!NOTE]
> 가능하면 항상 여러 인덱싱 변경 내용을 하나의 단일 인덱싱 정책 수정으로 그룹화해야 합니다.

## <a name="indexing-policies-and-ttl"></a>인덱싱 정책 및 TTL

[TTL(time-to-live) 기능](time-to-live.md)을 사용하려면 인덱싱이 필요합니다. 이는 다음을 의미합니다.

- 인덱싱 모드가 `none`으로 설정된 컨테이너에서는 TTL을 활성화할 수 없습니다.
- TTL이 활성화된 컨테이너에서는 인덱싱 모드를 None으로 설정할 수 없습니다.

인덱싱해야 하는 속성 경로는 없지만 TTL이 필요한 시나리오의 경우 인덱싱 모드가 `consistent`, 포함된 경로 없음, `/*`로 설정된 인덱싱 정책을 유일한 제외된 경로로 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서 인덱싱에 대해 자세히 알아보세요.

- [인덱싱 개요](index-overview.md)
- [인덱싱 정책을 관리하는 방법](how-to-manage-indexing-policy.md)
