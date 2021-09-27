---
title: Azure Cosmos DB 쿼리 언어의 ST_ISVALID
description: Azure Cosmos DB의 SQL 시스템 함수 ST_ISVALID에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5719554a64ed7d028f09fd7263425d91bb221ba2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625281"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 지정된 GeoJSON Point, Polygon, MultiPolygon, LineString 식이 유효한지 여부를 나타내는 부울 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*spatial_expr*  
   GeoJSON Point, Polygon, LineString 개체 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  부울 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 ST_VALID를 사용하여 꼭짓점이 유효한지 확인하는 방법을 보여 줍니다.  
  
  예를 들어 유효 범위[-90, 90]에 없는 위도 값이 이 꼭짓점에 있으므로 쿼리에서 false를 반환합니다.  
  
  다각형의 경우 GeoJSON 사양에서는 닫힌 도형을 만들기 위해 제공된 마지막 좌표 쌍이 첫 번째 것과 같아야 합니다. 다각형 내의 점을 시계 반대 방향 순서로 지정해야 합니다. 시계 방향 순서로 지정된 다각형은 내부 영역의 반전을 나타냅니다.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{ "b": false }]  
```  
> [!NOTE]
> GeoJSON 사양을 사용하려면 Polygon 내의 점을 시계 반대 방향으로 지정해야 합니다. 시계 방향 순서로 지정된 다각형은 내부 영역의 반전을 나타냅니다.

## <a name="next-steps"></a>다음 단계

- [공간 함수 Azure Cosmos DB](sql-query-spatial-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
