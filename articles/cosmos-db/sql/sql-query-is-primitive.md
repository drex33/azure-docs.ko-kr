---
title: Azure Cosmos DB 쿼리 언어의 IS_PRIMITIVE
description: Azure Cosmos DB의 SQL 시스템 함수 IS_PRIMITIVE에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a65dc0292841e55114d2c9ced429564a6f4381d1
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122537978"
---
# <a name="is_primitive-azure-cosmos-db"></a>IS_PRIMITIVE(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 지정한 식의 형식이 기본 형식(문자열, 부울, 숫자 또는 null)인지 여부를 나타내는 부울 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
IS_PRIMITIVE(<expr>)  
```  
  
## <a name="arguments"></a>인수
  
*expr*  
   임의의 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  부울 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 `IS_PRIMITIVE` 함수를 사용하여 JSON 부울, 숫자, 문자열, Null, 개체, 배열 및 정의되지 않은 형식의 개체를 확인합니다.  
  
```sql
SELECT   
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 [범위 인덱스](../index-policy.md#includeexclude-strategy)의 이점을 받습니다.

## <a name="next-steps"></a>다음 단계

- [형식 검사 함수 Azure Cosmos DB](sql-query-type-checking-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
