---
title: Azure Cosmos DB 쿼리 언어의 LTRIM
description: 선행 공백을 제거한 후 문자열 식을 반환하는 Azure Cosmos DB의 LTRIM SQL 시스템 함수에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/14/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7dca2a6354c19031c2b55b7a61d69273a06c6f49
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661263"
---
# <a name="ltrim-azure-cosmos-db"></a>LTRIM(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 선행 공백 또는 지정 된 문자를 제거한 후 문자열 식을 반환 합니다.   
  
## <a name="syntax"></a>구문
  
```sql
LTRIM(<str_expr1>[, <str_expr2>])  
```  
  
## <a name="arguments"></a>인수
  
*str_expr1*  
   는 문자열 식입니다.

*str_expr2*  
   Str_expr1에서 잘라내는 선택적 문자열 식입니다. 설정 되지 않은 경우 기본값은 공백입니다.
  
## <a name="return-types"></a>반환 형식
  
  문자열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예에서는 쿼리 내에서 `LTRIM`을 사용하는 방법을 보여 줍니다.  
  
```sql
SELECT LTRIM("   abc") AS t1, 
LTRIM("   abc   ") AS t2, 
LTRIM("abc   ") AS t3, 
LTRIM("abc") AS t4,
LTRIM("abc", "ab") AS t5,
LTRIM("abc", "abc") AS t6
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[
    {
        "t1": "abc",
        "t2": "abc   ",
        "t3": "abc   ",
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
