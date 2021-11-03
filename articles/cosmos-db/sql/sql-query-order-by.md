---
title: Azure Cosmos DB의 ORDER BY 절
description: Azure Cosmos DB의 SQL ORDER BY 절에 대해 알아봅니다. SQL을 Azure Cosmos DB JSON 쿼리 언어로 사용합니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/25/2021
ms.author: tisande
ms.openlocfilehash: fc2772fe8435852fb8a6f6f33aa86cd0756df080
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005844"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Azure Cosmos DB의 ORDER BY 절
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

선택적인 `ORDER BY` 절은 쿼리에서 반환된 결과의 정렬 순서를 지정합니다.

## <a name="syntax"></a>구문
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>인수
  
- `<sort_specification>`  
  
   쿼리 결과 집합을 정렬할 속성이나 식을 지정합니다. 정렬 열은 이름 또는 속성 별칭으로 지정할 수 있습니다.  
  
   여러 속성을 지정할 수 있습니다. 속성 이름은 고유해야 합니다. `ORDER BY` 절에서 열 속성의 순서가 정렬된 결과 집합의 구성 방식을 정의합니다. 즉 결과 집합이 첫 번째 속성으로 정렬된 다음 정렬된 해당 목록이 두 번째 속성으로 정렬되는 등등입니다.  
  
   `ORDER BY` 절에서 참조되는 속성 이름은 선택 목록의 속성 또는 `FROM` 절에 지정된 컬렉션에 정의된 속성과 정확히 일치해야 합니다.  
  
- `<sort_expression>`  
  
   쿼리 결과 집합을 정렬할 하나 이상의 속성 또는 식을 지정합니다.  
  
- `<scalar_expression>`  
  
   자세한 내용은 [스칼라 식](sql-query-scalar-expressions.md) 섹션을 참조하세요.  
  
- `ASC | DESC`  
  
   지정된 열의 값이 오름차순으로 정렬되는지 내림차순으로 정렬되는지를 지정합니다. `ASC`는 오름차순으로 정렬하고, `DESC`는 내림차순으로 정렬합니다. `ASC`는 기본 정렬 순서입니다. Null 값은 가능한 가장 작은 값으로 취급됩니다.  
  
## <a name="remarks"></a>설명  
  
   `ORDER BY` 절을 사용하려면 인덱싱 정책에 정렬되는 필드에 대한 인덱스가 포함되어야 합니다. Azure Cosmos DB 쿼리 런타임은 계산된 속성이 아닌 속성 이름에 대한 정렬을 지원합니다. Azure Cosmos DB는 여러 `ORDER BY` 속성을 지원합니다. 여러 ORDER BY 속성이 있는 쿼리를 실행하려면 정렬되는 필드에 [복합 인덱스](../index-policy.md#composite-indexes)를 정의해야 합니다.

> [!Note]
> 정렬되는 속성이 일부 문서에 대해 정의되지 않은 경우 이를 ORDER BY 쿼리에서 검색하려면 인덱스에 이 경로를 명시적으로 포함해야 합니다. 기본 인덱싱 정책은 정렬 속성이 정의되지 않은 문서의 검색을 허용하지 않습니다. [누락된 필드가 있는 문서에 대한 예제 쿼리를 검토합니다.](#documents-with-missing-fields)

## <a name="examples"></a>예

예를 들어 상주하는 도시 이름의 오름차순으로 가족을 검색하는 쿼리는 다음과 같습니다.

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

결과는 다음과 같습니다.

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

다음 쿼리는 항목 생성 날짜 순서로 패밀리 `id`를 검색합니다. 항목 `creationDate`는 *Epoch 시간* 또는 1970년 1월 1일 이후 경과된 시간(초)을 나타내는 숫자입니다.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

결과는 다음과 같습니다.

```json
    [
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      },
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      }
    ]
```

또한 여러 속성을 기준으로 순서를 정렬할 수 있습니다. 여러 속성으로 순서를 정렬하는 쿼리에는 [복합 인덱스](../index-policy.md#composite-indexes)가 필요합니다. 다음과 같은 쿼리를 고려해 보세요.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

이 쿼리는 도시 이름의 오름차순으로 패밀리 `id`를 검색합니다. 여러 항목의 도시 이름이 같은 경우 쿼리는 내림차순으로 `creationDate`를 정렬합니다.

## <a name="documents-with-missing-fields"></a>누락된 필드가 있는 문서

기본 인덱싱 정책을 사용하여 컨테이너에 대해 실행되는 `ORDER BY`가 있는 쿼리는 Sort 속성이 정의되지 않은 문서를 반환하지 않습니다. Sort 속성이 정의되지 않은 문서를 포함하려는 경우에는 인덱싱 정책에 이 속성을 명시적으로 포함해야 합니다.

예를 들어 다음은 `"/*"` 이외의 경로를 명시적으로 포함하지 않는 인덱싱 정책이 있는 컨테이너입니다.

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

`lastName` 절 `Order By`를 포함하는 쿼리를 실행하는 경우 `lastName` 속성을 정의하는 문서만 결과에 포함됩니다. `lastName`에 대한 명시적 포함 경로를 정의하지 않았으므로 `lastName`이 없는 모든 문서가 쿼리 결과에 표시되지 않습니다.

다음은 두 문서를 기준으로 `lastName`으로 정렬하는 쿼리로, 하나는 `lastName`이 정의되어 있지 않습니다.

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

결과는 다음과 같이 `lastName`가 정의된 문서만 포함합니다.

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

`lastName`에 대한 경로를 명시적으로 포함하도록 컨테이너의 인덱싱 정책을 업데이트하는 경우 쿼리 결과에 정의되지 않은 Sort 속성이 있는 문서를 포함합니다. 이 스칼라 값(및 이외는 해당하지 않음)으로 이어지는 경로를 명시적으로 정의해야 합니다. 인덱싱 정책의 경로 정의에 있는 `?` 문자를 사용하 여 `lastName` 속성을 명시적으로 인덱싱하고 그 밖의 중첩된 경로를 추가로 사용하지 않도록 해야 합니다. `Order By` 쿼리에서 [복합 인덱스](../index-policy.md#composite-indexes)를 사용하는 경우 결과는 항상 쿼리 결과에 정의되지 않은 Sort 속성이 있는 문서를 포함합니다.

다음은 정의되지 않은 `lastName`이 있는 문서를 쿼리 결과에 표시할 수 있는 샘플 인덱싱 정책입니다.

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

동일한 쿼리를 다시 실행하는 경우 `lastName`이 누락된 문서가 먼저 쿼리 결과에 표시됩니다.

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

결과는 다음과 같습니다.

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

정렬 순서를 `DESC`로 수정하는 경우 `lastName`이 누락된 문서는 쿼리 결과의 마지막에 표시됩니다.

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

결과는 다음과 같습니다.

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

> [!Note]
> .NET SDK 버전 3.4.0 이상에서만 혼합 형식의 ORDER BY를 지원합니다. 따라서 정의되지 않은 값 및 정의된 값을 조합하여 정렬하려면 이 버전 이상을 사용해야 합니다.

결과에 다른 형식이 표시되는 순서는 제어할 수 없습니다. 위의 예에서는 정의되지 않은 값이 문자열 값보다 먼저 정렬되는 방법을 보여주었습니다. 예를 들어 정의되지 않은 값의 정렬 순서를 더 자세히 제어하려는 경우 정의되지 않은 속성에 문자열 값 "aaaaaaaaa" 또는 "zzzzzzzz"를 할당하여 첫 번째 또는 마지막 값인지 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [Azure Cosmos DB의 인덱싱 정책](../index-policy.md)
- [OFFSET LIMIT 절](sql-query-offset-limit.md)
