---
title: Azure Cosmos DB에서 LINQ to SQL 변환
description: 지원되는 LINQ 연산자 및 LINQ 쿼리가 Azure Cosmos DB의 SQL 쿼리에 매핑되는 방법을 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/06/2021
ms.author: tisande
ms.openlocfilehash: 16de66f4182c44b29f0ef3a8f86f413828473f74
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528316"
---
# <a name="linq-to-sql-translation"></a>LINQ to SQL 변환
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 쿼리 공급자는 LINQ 쿼리에서 Cosmos DB SQL 쿼리로 최선의 매핑을 수행합니다. LINQ에서 변환되는 SQL 쿼리를 가져오려면 생성된 `IQueryable` 개체에 대해 `ToString()`메서드를 사용합니다. 다음 설명은 사용자가 [LINQ](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries)에 대한 기본적인 지식이 있다고 가정합니다. LINQ 외에도 Azure Cosmos DB 또한 SQL API와 작동하는 [Entity Framework Core](/ef/core/providers/cosmos/?tabs=dotnet-core-cli)를 지원합니다.

> [!NOTE]
> 최신 [.NET SDK 버전](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.20.1)을 사용하는 것이 좋습니다.

쿼리 공급자 유형 시스템은 JSON 기본 유형인 숫자, 부울, 문자열 및 Null만 지원합니다.

쿼리 공급자는 다음과 같은 스칼라 식을 지원합니다.

- 쿼리가 평가될 때 기본 데이터 형식의 상수 값을 포함하는 상수 값.
  
- 개체 또는 배열 요소의 속성을 참조하는 속성/배열 인덱스 식. 예를 들면 다음과 같습니다.
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- 숫자 및 부울 값에 대한 일반 산술 식을 포함하는 산술 식. 전체 목록은 [Azure Cosmos DB SQL 사양](sql-query-aggregate-functions.md)을 참조하세요.
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- 문자열 값과 상수 문자열 값 비교를 포함하는 문자열 비교 식.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- 복합 값 형식이나 무명 형식의 개체 또는 이러한 개체의 배열을 반환하는 개체/배열 만들기 식. 이러한 값은 중첩할 수 있습니다.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="using-linq"></a>LINQ 사용

`GetItemLinqQueryable`을 사용하여 LINQ 쿼리를 만들 수 있습니다. 이 예제에서는 `FeedIterator`를 사용한 LINQ 쿼리 생성 및 비동기 실행을 보여 줍니다.

```csharp
using (FeedIterator<Book> setIterator = container.GetItemLinqQueryable<Book>()
                      .Where(b => b.Title == "War and Peace")
                      .ToFeedIterator<Book>())
 {
     //Asynchronous query execution
     while (setIterator.HasMoreResults)
     {
         foreach(var item in await setIterator.ReadNextAsync()){
         {
             Console.WriteLine(item.cost);
         }
       }
     }
 }
```

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>지원되는 LINQ 연산자

SQL .NET SDK에 포함된 LINQ 공급자는 다음과 같은 연산자를 지원합니다.

- **Select**: 프로젝션이 개체 생성을 포함하는 [SELECT](sql-query-select.md)로 변환합니다.
- **Where**: 필터는 [ WHERE](sql-query-where.md)로 변환하고 `&&`, `||` 및 `!` 간의 SQL 연산자로의 변환을 지원합니다.
- **SelectMany**: [JOIN](sql-query-join.md) 절에 대한 배열 해제를 허용합니다. 체인 또는 중첩 식을 사용하여 배열 요소를 필터링합니다.
- **OrderBy** 및 **OrderByDescending**: ASC 또는 DESC를 사용하여 [ORDER BY](sql-query-order-by.md)로 변환합니다.
- [집계](sql-query-aggregate-functions.md)를 위한 **Count**, **Sum**, **Min**, **Max** 및 **Average** 연산자와 해당 비동기 동급 연산자 **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** 및 **AverageAsync** 입니다.
- **CompareTo**: 범위 비교로 변환합니다. .NET에서 비교할 수 없으므로 일반적으로 문자열에 사용됩니다.
- **Skip** 및 **Take**: 쿼리에서 결과를 제한하고 페이지 매김을 수행하기 위한 [OFFSET 및 LIMIT](sql-query-offset-limit.md)으로 변환합니다.
- **수학 함수**: .NET `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`, `Cos`, `Exp`, `Floor`, `Log`, `Log10`, `Pow`, `Round`, `Sign`, `Sin`, `Sqrt`, `Tan` 및 `Truncate`에서 해당하는 [기본 제공 수학 함수](sql-query-mathematical-functions.md)로의 변환을 지원합니다.
- **문자열 함수**: .NET `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd` 및 `TrimStart`에서 해당하는 [기본 제공 문자열 함수](sql-query-string-functions.md)로의 변환을 지원합니다.
- **배열 함수**: .NET `Concat`, `Contains` 및 `Count`에서 해당하는 [기본 제공 배열 함수](sql-query-array-functions.md)로의 변환을 지원합니다.
- **지리 공간적 확장 함수**: 스텁 메서드 `Distance`, `IsValid`, `IsValidDetailed` 및 `Within`에서 해당하는 [기본 제공 지리 공간적 함수](sql-query-geospatial-query.md)로의 변환을 지원합니다.
- **사용자 정의 함수 확장 함수**: 스텁 메서드 [CosmosLinq.InvokeUserDefinedFunction](/dotnet/api/microsoft.azure.cosmos.linq.cosmoslinq.invokeuserdefinedfunction?view=azure-dotnet&preserve-view=true)에서 해당하는 [사용자 정의 함수](sql-query-udfs.md)로의 변환을 지원합니다.
- **기타**: `Coalesce` 및 조건부 [연산자](sql-query-operators.md)의 변환을 지원합니다. 컨텍스트에 따라 `Contains`는 문자열 CONTAINS, ARRAY_CONTAINS 또는 IN으로 변환할 수 있습니다.

## <a name="examples"></a>예

다음 예에서는 표준 LINQ 쿼리 연산자 중 일부가 Azure Cosmos DB의 쿼리로 변환하는 방법을 보여 줍니다.

### <a name="select-operator"></a>Select 연산자

구문은 `input.Select(x => f(x))`입니다. 여기서 `f`는 스칼라 식입니다. 이 경우, `input`은 `IQueryable` 개체입니다.

**Select 연산자, 예 1:**

- **LINQ 람다 식**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Select 연산자, 예 2:**

- **LINQ 람다 식**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Select 연산자, 예 3:**

- **LINQ 람다 식**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

### <a name="selectmany-operator"></a>SelectMany 연산자

구문은 `input.SelectMany(x => f(x))`입니다. 여기서 `f`는 컨테이너 형식을 반환하는 스칼라 식입니다.

- **LINQ 람다 식**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Where 연산자

구문은 `input.Where(x => f(x))`입니다. 여기서 `f`는 부울 값을 반환하는 스칼라 식입니다.

**Where 연산자, 예 1:**

- **LINQ 람다 식**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Where 연산자, 예 2:**

- **LINQ 람다 식**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

## <a name="composite-sql-queries"></a>복합 SQL 쿼리

위의 연산자를 작성하여 보다 강력한 쿼리를 만들 수 있습니다. Cosmos DB에서 중첩된 컨테이너를 지원하기 때문에 컴퍼지션을 연결하거나 중첩할 수 있습니다.

### <a name="concatenation"></a>연결

구문은 `input(.|.SelectMany())(.Select()|.Where())*`입니다. 연결된 쿼리는 선택적 `SelectMany` 쿼리로 시작하며 그 뒤에 여러 `Select` 또는 `Where` 연산자가 올 수 있습니다.

**연결, 예 1:**

- **LINQ 람다 식**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**연결, 예 2:**

- **LINQ 람다 식**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**연결, 예 3:**

- **LINQ 람다 식**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**연결, 예 4:**

- **LINQ 람다 식**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

### <a name="nesting"></a>중첩

구문은 `input.SelectMany(x=>x.Q())`입니다. 여기서 `Q`는 `Select`, `SelectMany` 또는 `Where` 연산자입니다.

중첩 쿼리는 외부 컨테이너의 각 요소에 내부 쿼리를 적용합니다. 한 가지 중요한 기능은 셀프 조인처럼 내부 쿼리가 외부 컨테이너의 요소 필드를 참조할 수 있다는 것입니다.

**중첩, 예 1:**

- **LINQ 람다 식**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**중첩, 예 2:**

- **LINQ 람다 식**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**중첩, 예 3:**

- **LINQ 람다 식**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [모델 문서 데이터](modeling-data.md)