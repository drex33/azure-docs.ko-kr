---
title: Azure Cosmos DB에 대한 SQL JOIN 쿼리
description: Azure Cosmos DB의 여러 테이블을 조인하여 데이터를 쿼리하는 방법 알아보기
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/06/2021
ms.author: tisande
ms.openlocfilehash: e890f922c1c327ed51b8c320ef415f51ff43332f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535670"
---
# <a name="joins-in-azure-cosmos-db"></a>Azure Cosmos DB 조인
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

관계형 데이터베이스에서 테이블 간 조인은 정규화된 스키마를 디자인하는 논리적으로 결합된 것입니다. 반대로 SQL API는 스키마가 없는 항목의 비정규화된 데이터 모델을 사용합니다. 이는 *셀프 조인* 과 논리적으로 동일합니다.

> [!NOTE]
> Azure Cosmos DB에서 조인은 단일 항목으로 범위가 지정됩니다. 교차 항목 및 교차 컨테이너 조인은 지원되지 않습니다. Azure Cosmos DB와 같은 NoSQL 데이터베이스에서 좋은 [데이터 모델링](modeling-data.md)은 교차 항목 및 교차 컨테이너 조인이 필요하지 않도록 하는 데 도움이 될 수 있습니다.

조인을 통해 집합의 전체 교차곱이 조인에 참여합니다. N 방향 조인의 결과는 N 요소 튜플의 집합이며, 여기서 튜플의 각 값은 조인에 참여하는 별칭 지정된 집합과 연결되며 다른 절에서 해당 별칭을 참조하여 액세스할 수 있습니다.

## <a name="syntax"></a>구문

이 언어는 `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>` 구문을 지원합니다. 이 쿼리는 `N` 값이 있는 튜플 집합을 반환합니다. 각 튜플은 해당 집합에 모든 컨테이너 별칭을 반복하여 생성된 값을 포함합니다. 

`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>` FROM 절을 살펴보겠습니다.  
  
 각 원본에서 `input_alias1, input_alias2, …, input_aliasN`을 정의하도록 합니다. 이 FROM 절은 N 튜플(N개 값이 포함된 튜플)의 집합을 반환합니다. 각 튜플은 해당 집합에 모든 컨테이너 별칭을 반복하여 생성된 값을 포함합니다.  
  
**예제 1** - 2개 원본  
  
- 컨테이너 범위로 `<from_source1>`을 지정하고 집합 {A, B, C}를 나타냅니다.  
  
- input_alias1을 참조하는 문서 범위로 `<from_source2>`를 지정하고 다음 집합을 나타냅니다.  
  
    `input_alias1 = A,`의 경우 {1, 2}  
  
    `input_alias1 = B,`의 경우 {3}  
  
    `input_alias1 = C,`의 경우 {4, 5}  
  
- `<from_source1> JOIN <from_source2>` FROM 절은 다음과 같은 튜플을 만듭니다.  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**예제 2** - 3개 원본  
  
- 컨테이너 범위로 `<from_source1>`을 지정하고 집합 {A, B, C}를 나타냅니다.  
  
- `input_alias1`을 참조하는 문서 범위로 `<from_source2>`를 지정하고 다음 집합을 나타냅니다.  
  
    `input_alias1 = A,`의 경우 {1, 2}  
  
    `input_alias1 = B,`의 경우 {3}  
  
    `input_alias1 = C,`의 경우 {4, 5}  
  
- `input_alias2`을 참조하는 문서 범위로 `<from_source3>`를 지정하고 다음 집합을 나타냅니다.  
  
    `input_alias2 = 1,`의 경우 {100, 200}  
  
    `input_alias2 = 3,`의 경우 {300}  
  
- `<from_source1> JOIN <from_source2> JOIN <from_source3>` FROM 절은 다음과 같은 튜플을 만듭니다.  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > `input_alias1`, `input_alias2`의 다른 값에 대한 튜플이 없으므로 `<from_source3>`에서 값을 반환하지 않았습니다.  
  
**예제 3** - 3개 원본  
  
- 컨테이너 범위로 `<from_source1>`을 지정하고 집합 {A, B, C}를 나타냅니다.  
  
- `input_alias1`을 참조하는 문서 범위로 `<from_source2>`를 지정하고 다음 집합을 나타냅니다.  
  
    `input_alias1 = A,`의 경우 {1, 2}  
  
    `input_alias1 = B,`의 경우 {3}  
  
    `input_alias1 = C,`의 경우 {4, 5}  
  
- `<from_source3>`을 `input_alias1`로 범위 지정하고 다음 집합을 나타냅니다.  
  
    `input_alias2 = A,`의 경우 {100, 200}  
  
    `input_alias2 = C,`의 경우 {300}  
  
- `<from_source1> JOIN <from_source2> JOIN <from_source3>` FROM 절은 다음과 같은 튜플을 만듭니다.  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300), (C, 5, 300)  
  
  > [!NOTE]
  > 이렇게 하면 `<from_source2>`와 `<from_source3>`의 범위를 모두 동일한 `<from_source1>`로 지정했기 때문에 두 원본의 교차곱을 만들고,  이로 인해 A 값이 있는 4개(2x2) 튜플, B 값이 있는 0개(1x0) 튜플 및 C 값이 있는 2개(2x1) 튜플을 만들었습니다.  
  
## <a name="examples"></a>예

다음 예제는 JOIN 절의 작동 방식을 보여 줍니다. 이 예를 실행하기 전에 샘플 [패밀리 데이터](sql-query-getting-started.md#upload-sample-data)를 업로드하세요. 다음 예에서는 소스의 각 항목과 빈 세트의 교차곱이 비어 있으므로 결과가 비어 있습니다.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

결과는 다음과 같습니다.

```json
    [{
    }]
```

다음 예에서 조인은 두 JSON 개체인 항목 루트 `id` 및 `children` 서브 루트 간의 교차 곱입니다. `children`이 배열이라는 사실은 `children` 배열인 단일 루트를 다루기 때문에 조인에 효과적이지 않습니다. 배열이 있는 각 항목의 교차곱은 정확히 하나의 항목만 생성하므로 결과에는 두 개의 결과만 포함됩니다.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

결과는 다음과 같습니다.

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

다음 예제에서는 보다 기본적인 조인을 보여 줍니다.

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

결과는 다음과 같습니다.

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

JOIN 절의 FROM 소스는 반복기입니다. 따라서 앞의 예에서 흐름은 다음과 같습니다.  

1. 배열에서 각 자식 요소 `c`를 확장합니다.
2. 첫 번째 단계에서 평면화한 각 자식 요소 `c`와 함께 항목 `f`의 루트가 있는 교차곱을 적용합니다.
3. 마지막으로, 루트 개체 `f` `id` 속성만 프로젝션합니다.

첫 번째 항목 `AndersenFamily`에는 하나의 `children` 요소만 포함되어 있으므로 결과 세트에는 단일 개체만 포함됩니다. 두 번째 항목 `WakefieldFamily`에는 두 개의 `children`이 포함되어 있으므로 교차곱은 각 `children` 요소에 대해 하나씩 두 개의 개체를 생성합니다. 교차곱에서 예상한 대로 두 항목의 루트 필드는 동일합니다.

JOIN 절의 진정한 유용성은 다른 방식으로 프로젝션하기 어려운 모양의 교차곱에서 튜플을 형성할 수 있다는 것입니다. 아래 예에서는 사용자가 전체적으로 튜플이 만족하는 조건을 선택할 수 있도록 튜플 조합을 필터링합니다.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

결과는 다음과 같습니다.

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

이전 예의 다음 확장은 이중 조인을 수행합니다. 교차곱을 다음 의사 코드로 볼 수 있습니다.

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily`에는 애완 동물 한 마리가 있는 자식이 한 명 있으므로 교차곱은 이 패밀리에서 1행(1\*1\*1)을 생성합니다. `WakefieldFamily`에는 두 명의 자식이 있으며 그중 한 명만 애완 동물이 있지만 그 자식에게는 두 마리의 애완 동물이 있습니다. 이 계열의 교차곱은 1\*1\*2 = 2행을 생성합니다.

다음 예에서는 `pet`에 대한 추가 필터가 있으며, 이는 애완 동물 이름이 `Shadow`가 아닌 모든 튜플을 제외합니다. 배열에서 튜플을 빌드하고, 튜플의 요소를 필터링하고, 요소의 조합을 프로젝션할 수 있습니다.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

결과는 다음과 같습니다.

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

쿼리에 JOIN 및 필터가 있는 경우 쿼리의 일부를 [하위 쿼리](sql-query-subquery.md#optimize-join-expressions)로 다시 작성하여 성능을 향상시킬 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmosdb-dotnet)
- [하위 쿼리](sql-query-subquery.md)
