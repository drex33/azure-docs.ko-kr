---
title: Azure Cosmos DB 쿼리 언어의 ST_ISVALIDDETAILED
description: Azure Cosmos DB의 SQL 시스템 함수 ST_ISVALIDDETAILED에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1a1ca571dd90f59cf5d4a88440dbc631fe2d4553
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647770"
---
# <a name="st_isvaliddetailed-azure-cosmos-db"></a>ST_ISVALIDDETAILED(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 지정된 GeoJSON Point, Polygon 또는 LineString 식이 유효한 경우 부울 값을 포함하는 JSON 값을 반환하고, 잘못된 경우 추가로 그 이유를 문자열 값으로 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*spatial_expr*  
   GeoJSON 꼭짓점 또는 다각형 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  지정된 GeoJSON 점 또는 다각형 식이 유효한 경우 부울 값을 포함하는 JSON 값을 반환하고, 잘못된 경우 추가로 그 이유를 문자열 값으로 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 `ST_ISVALIDDETAILED`를 사용하여 세부 정보로 유효성을 검사하는 방법을 보여 줍니다.  
  
```sql
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

> [!NOTE]
> GeoJSON 사양에서는 다각형 내의 요소를 시계 반대 방향 순서로 지정 해야 합니다. 시계 방향 순서로 지정된 다각형은 내부 영역의 반전을 나타냅니다.

## <a name="next-steps"></a>다음 단계

- [공간 함수 Azure Cosmos DB](sql-query-spatial-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
