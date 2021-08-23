---
title: Azure Cosmos DB 쿼리 언어의 IS_BOOL
description: Azure Cosmos DB의 SQL 시스템 함수 IS_BOOL에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2d1a6029e5b6c2ccd3900c09501ddbb59efeaf10
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122537795"
---
# <a name="is_bool-azure-cosmos-db"></a>IS_BOOL(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 지정한 식의 형식이 부울인지 여부를 나타내는 부울 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
IS_BOOL(<expr>)  
```  
  
## <a name="arguments"></a>인수
  
*expr*  
   임의의 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  부울 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 `IS_BOOL` 함수를 사용하여 JSON 부울, 숫자, 문자열, Null, 개체, 배열 및 정의되지 않은 형식의 개체를 확인합니다.  
  
```sql
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  

## <a name="remarks"></a>설명

이 시스템 함수는 [범위 인덱스](../index-policy.md#includeexclude-strategy)의 이점을 받습니다.

## <a name="next-steps"></a>다음 단계

- [형식 검사 함수 Azure Cosmos DB](sql-query-type-checking-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
