---
title: Azure Cosmos DB를 사용하여 지리 공간적 데이터 쿼리
description: Azure Cosmos DB를 사용하여 공간 데이터 쿼리
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: bb9a0351b6f1de47f3687995c65060a23bdb2874
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336132"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Azure Cosmos DB를 사용하여 지리 공간적 데이터 쿼리
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

이 문서에서는 SQL 및 LINQ를 사용하여 Azure Cosmos DB에서 지리 공간적 데이터를 쿼리하는 방법을 다룹니다. 현재 지리 공간 데이터의 저장 및 액세스는 Azure Cosmos DB SQL API 계정에서만 지원됩니다. Azure Cosmos DB는 지리 공간 쿼리를 위해 다음과 같은 OGC(Open Geospatial Consortium) 기본 제공 함수를 지원합니다. SQL 언어의 전체 기본 제공 함수 집합에 대한 자세한 내용은 [Azure Cosmos DB에서 시스템 함수 쿼리](sql-query-system-functions.md)를 참조하세요.

## <a name="spatial-sql-built-in-functions"></a>공간 SQL 기본 제공 함수

다음은 Azure Cosmos DB에서 쿼리하는 데 유용한 지리 공간적 시스템 함수 목록입니다.

|**사용 현황**|**설명**|
|---|---|
| ST_DISTANCE(spatial_expr, spatial_expr) | 두 GeoJSON Point, Polygon 또는 LineString 식 사이의 거리를 반환합니다.|
|ST_WITHIN(spatial_expr, spatial_expr) | 첫 번째 GeoJSON 개체(Point, Polygon 또는 LineString)가 두 번째 GeoJSON 개체(Point, Polygon 또는 LineString) 내에 있는지를 나타내는 부울 식을 반환합니다.|
|ST_INTERSECTS(spatial_expr, spatial_expr)| 지정한 두 GeoJSON 개체(Point, Polygon 또는 LineString)가 교차하는지 여부를 나타내는 부울 식을 반환합니다.|
|ST_ISVALID| 지정된 GeoJSON Point, Polygon 또는 LineString 식이 유효한지 여부를 나타내는 부울 값을 반환합니다.|
| ST_ISVALIDDETAILED| 지정된 GeoJSON Point, Polygon 또는 LineString 식이 유효한지 여부를 나타내는 부울 값을 포함하는 JSON 값을 반환합니다. 유효하지 않은 경우 이유를 문자열 값으로 반환합니다.|

공간 함수를 사용하여 공간 데이터에 대한 근접 쿼리를 수행할 수 있습니다. 예를 들어 `ST_DISTANCE` 기본 제공 함수를 사용하여 지정된 위치에서 30km 이내에 있는 모든 제품군 문서를 반환하는 쿼리는 다음과 같습니다.

**쿼리**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {"type": "Point", "coordinates":[31.9, -4.8]}) < 30000
```

**결과**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

인덱싱 정책에 공간 인덱싱을 포함하면 "거리 쿼리"가 인덱스를 통해 효율적으로 처리됩니다. 공간 인덱싱에 대한 자세한 내용은 [지리 공간적 인덱싱](sql-query-geospatial-index.md)을 참조하세요. 지정된 경로에 대한 공간 인덱스가 없는 경우 쿼리는 컨테이너를 검색합니다.

`ST_WITHIN`을 사용하여 점이 다각형 내에 있는지 여부를 확인할 수 있습니다. 일반적으로 다각형은 우편 번호, 시/도 경계 또는 자연스러운 대형과 같은 경계를 나타내는 데 사용됩니다. 인덱싱 정책에 공간 인덱싱을 포함하면 "이내" 쿼리가 인덱스를 통해 효율적으로 처리됩니다.

`ST_WITHIN`의 다각형 인수에는 단일 링만 포함될 수 있습니다. 즉, 다각형에 구멍이 포함되지 않아야 합니다.

**쿼리**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**결과**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> Azure Cosmos DB 쿼리에서 일치하지 않는 형식이 작동하는 방식과 비슷하게, 인수에 지정된 위치 값이 잘못되었거나 형식이 잘못된 경우 **정의되지 않음** 으로 평가되고 평가된 문서는 쿼리 결과에서 생략됩니다. 쿼리에서 결과가 반환되지 않는 경우 `ST_ISVALIDDETAILED`를 실행하여 공간 형식이 잘못된 이유를 디버그합니다.
>
>

Azure Cosmos DB는 반전 쿼리 수행도 지원합니다. 즉, Azure Cosmos DB에서 다각형 또는 선을 인덱싱한 다음, 지정된 점이 포함된 영역을 쿼리할 수 있습니다. 이 패턴은 일반적으로 특정 시점(예: 지정된 영역에 트럭이 출입한 시점)을 식별하기 위해 물류에서 사용됩니다.

**쿼리**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({"type": "Point", "coordinates":[31.9, -4.8]}, a.location)
```

**결과**

```json
    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]
```

`ST_ISVALID` 및 `ST_ISVALIDDETAILED`를 사용하여 공간 개체가 유효한지 확인할 수 있습니다. 예를 들어 다음 쿼리는 위도 값(-132.8)이 범위를 벗어난 점의 유효성을 검사합니다. `ST_ISVALID`는 부울 값만 반환하고 `ST_ISVALIDDETAILED`는 부울 및 잘못된 것으로 간주된 이유를 포함하는 문자열을 반환합니다.

**쿼리**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**결과**

```json
    [{
      "$1": false
    }]
```

이러한 함수를 사용하여 다각형의 유효성을 검사할 수도 있습니다. 예를 들어 여기서는 `ST_ISVALIDDETAILED`를 사용하여 닫혀 있지 않은 다각형의 유효성을 검사합니다.

**쿼리**

```sql
    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})
```

**결과**

```json
    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]
```

## <a name="linq-querying-in-the-net-sdk"></a>.NET SDK의 LINQ 쿼리

SQL .NET SDK는 LINQ 식에서 사용하기 위한 스텁 메서드 `Distance()` 및 `Within()`도 제공합니다. SQL LINQ 공급자는 이 메서드 호출을 동등한 SQL 기본 제공 함수 호출(각각 ST_DISTANCE 및 ST_WITHIN)로 변환합니다.

LINQ를 사용하여 Azure Cosmos 컨테이너에서 `location` 값이 지정된 점에서 30km 반지름 내에 있는 모든 문서를 찾는 LINQ 쿼리의 예는 다음과 같습니다.

**거리에 대한 LINQ 쿼리**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

마찬가지로, `location`이 지정된 상자/다각형 내에 있는 모든 문서를 찾기 위한 쿼리는 다음과 같습니다.

**이내에 대한 LINQ 쿼리**

```csharp
    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
         .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }
```

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB에서 지리 공간 지원을 시작하는 방법을 배웠으므로 이제 다음 작업을 수행할 수 있습니다.

* [Azure Cosmos DB 쿼리](sql-query-getting-started.md)에 대해 알아보기
* [Azure Cosmos DB의 지리 공간적 및 GeoJSON 위치 데이터](sql-query-geospatial-intro.md)에 대해 알아보기
* [Azure Cosmos DB를 사용하여 공간 데이터 인덱싱](sql-query-geospatial-index.md)에 대해 알아보기
