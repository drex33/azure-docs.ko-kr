---
title: Azure Cosmos DB 쿼리 언어의 ROUND
description: Azure Cosmos DB의 SQL 시스템 함수 ROUND에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 76543268d26970db0566040184807a7ead329a1e
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122537974"
---
# <a name="round-azure-cosmos-db"></a>ROUND(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 가장 가까운 정수 값으로 반올림한 숫자 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="remarks"></a>설명
  
수행되는 반올림 연산은 0에서 먼 쪽으로 중간점 반올림하는 방식을 따릅니다. 정확히 두 정수 사이에 있는 숫자 식으로 입력된 경우, 결과는 0에서 먼 쪽으로 가장 가까운 정수 값이 됩니다. 이 시스템 함수는 [범위 인덱스](../index-policy.md#includeexclude-strategy)의 이점을 받습니다.
  
|<numeric_expr>|반올림|
|-|-|
|-6.5000|-7|
|-0.5|-1|
|0.5|1|
|6.5000|7|
  
## <a name="examples"></a>예제
  
다음 예제에서는 첨부된 양수와 음수를 가장 가까운 정수로 반올림합니다.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
결과 집합은 다음과 같습니다.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="next-steps"></a>다음 단계

- [수학 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
