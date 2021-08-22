---
title: Azure Cosmos DB 쿼리 언어의 ATAN
description: Azure Cosmos DB의 아크탄젠트(ATAN) SQL 시스템 함수에서 탄젠트가 지정된 숫자 식인 각도를 라디안에서 반환하는 방법에 대해 알아봅니다
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d956a99b3ec615a7cc1d97e2efedade08681e47a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122537746"
---
# <a name="atan-azure-cosmos-db"></a>ATAN(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 탄젠트 값이 지정된 숫자 식인 라디안에서 각도를 반환합니다. 이를 아크탄젠트라고도 합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 지정된 값 중 `ATAN`을 반환합니다.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  
## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [수학 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
