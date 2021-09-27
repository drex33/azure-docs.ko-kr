---
title: Azure Cosmos DB 쿼리 언어의 ST_DISTANCE
description: Azure Cosmos DB의 SQL 시스템 함수 ST_DISTANCE에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b1cf5bdad567a82bd3936f2b2a187375259407ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625352"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 두 GeoJSON Point, Polygon, MultiPolygon 또는 LineString 식 사이의 거리를 반환합니다. 자세히 알아보려면 [Geospatial 및 GeoJSON 위치 데이터](sql-query-geospatial-intro.md) 문서를 참조하세요.
  
## <a name="syntax"></a>구문
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*spatial_expr*  
   유효한 GeoJSON Point, Polygon 또는 LineString 개체 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  거리가 포함된 숫자 식을 반환합니다. 기본 참조 시스템의 경우 미터 단위로 표현됩니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 `ST_DISTANCE` 기본 제공 함수를 사용하여 지정된 위치에서 30km 이내에 있는 모든 패밀리 문서를 반환하는 방법을 보여줍니다. .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>설명

이 시스템 함수는 집계가 있는 쿼리를 제외하고 [지리 공간적 인덱스](../index-policy.md#spatial-indexes)의 이점을 제공합니다.

> [!NOTE]
> GeoJSON 사양에서는 다각형 내의 요소를 시계 반대 방향 순서로 지정 해야 합니다. 시계 방향 순서로 지정된 다각형은 내부 영역의 반전을 나타냅니다.

## <a name="next-steps"></a>다음 단계

- [공간 함수 Azure Cosmos DB](sql-query-spatial-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
