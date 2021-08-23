---
title: Azure Cosmos DB 쿼리 언어의 RTRIM
description: Azure Cosmos DB의 SQL 시스템 함수 RTRIM에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 46cb4a25e591e4664f326a04e7eb597e98f2c0b0
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122567605"
---
# <a name="rtrim-azure-cosmos-db"></a>RTRIM(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 후행 공백을 제거한 후에 문자열 식을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
RTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   유효한 문자열 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  문자열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예에서는 쿼리 내에서 `RTRIM`을 사용하는 방법을 보여 줍니다.  
  
```sql
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
