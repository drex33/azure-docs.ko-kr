---
title: Azure Cosmos DB 쿼리 언어의 StartsWith
description: Azure Cosmos DB의 SQL 시스템 함수 STARTSWITH에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d850ae0a01a93e2214d4dc26df98234f54e28a8b
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122538122"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 첫 번째 문자열 식이 두 번째 문자열 식에서 시작하는지 여부를 나타내는 부울 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
STARTSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>인수
  
*str_expr1*  
   문자열 식입니다.
  
*str_expr2*  
   *str_expr1* 의 시작과 비교할 문자열 식입니다.

*bool_expr* 대/소문자를 무시하기 위한 선택적 값입니다. true로 설정하면 STARTSWITH는 대/소문자를 구분하지 않는 검색을 수행합니다. 지정되지 않은 경우 이 값은 false입니다.

## <a name="return-types"></a>반환 형식
  
  부울 식을 반환합니다.  
  
## <a name="examples"></a>예
  
다음 예제에서는 "abc" 문자열이 "b"와 "A"로 시작하는지 확인합니다.  
  
```sql
SELECT STARTSWITH("abc", "b", false) AS s1, STARTSWITH("abc", "A", false) AS s2, STARTSWITH("abc", "A", true) AS s3
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[
    {
        "s1": false,
        "s2": false,
        "s3": true
    }
]
```  

## <a name="remarks"></a>설명

이 [문자열 시스템 함수에서 인덱스를 사용하는 방법](sql-query-string-functions.md)을 알아봅니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
