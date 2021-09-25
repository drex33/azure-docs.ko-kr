---
title: Azure Cosmos DB 쿼리 언어의 TRIM
description: Azure Cosmos DB의 SQL 시스템 함수 TRIM에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/14/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ea47e95a3b47f50613c954c95f3f5fd306e9c729
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638827"
---
# <a name="trim-azure-cosmos-db"></a>TRIM(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

선행 및 후행 공백 또는 지정된 문자를 제거한 후 문자열 식을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
TRIM(<str_expr1>[, <str_expr2>])  
```  
  
## <a name="arguments"></a>인수
  
*str_expr1*  
   문자열 식입니다.

*str_expr2*  
   str_expr1 트리밍할 선택적 문자열 식입니다. 설정하지 않으면 기본값은 공백입니다.

## <a name="return-types"></a>반환 형식
  
  문자열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예에서는 쿼리 내에서 `TRIM`을 사용하는 방법을 보여 줍니다.  
  
```sql
SELECT TRIM("   abc") AS t1, 
TRIM("   abc   ") AS t2, 
TRIM("abc   ") AS t3, 
TRIM("abc") AS t4,
TRIM("abc", "ab") AS t5,
TRIM("abc", "abc") AS t6
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[
    {
        "t1": "abc",
        "t2": "abc",
        "t3": "abc",
        "t4": "abc",
        "t5": "c",
        "t6": ""
    }
]
``` 

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
