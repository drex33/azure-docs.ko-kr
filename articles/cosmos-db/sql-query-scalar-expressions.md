---
title: Azure Cosmos DB SQL 쿼리의 스칼라 식
description: Azure Cosmos DB의 스칼라 식 SQL 구문에 대해 알아봅니다. 이 문서에서는 연산자를 사용하여 스칼라 식을 복잡한 식으로 결합하는 방법도 설명합니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 34df2d2f4a2209cca93c3f7ac12dcd203bf4a089
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339634"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Azure Cosmos DB SQL 쿼리의 스칼라 식
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

[SELECT 절](sql-query-select.md)은 스칼라 식을 지원합니다. 스칼라 식은 단일 값을 얻기 위해 평가될 수 있는 기호와 연산자의 조합입니다. 스칼라 식의 예에는 상수, 속성 참조, 배열 요소 참조, 별칭 참조 또는 함수 호출이 포함되어 있습니다. 스칼라 식은 연산자를 사용하여 복잡한 식으로 결합될 수 있습니다.

## <a name="syntax"></a>구문
  
```sql  
<scalar_expression> ::=  
       <constant>
     | input_alias
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>
     | <create_array_expression>  
     | (<scalar_expression>)
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```

## <a name="arguments"></a>인수
  
- `<constant>`  
  
   상수 값을 나타냅니다. 세부 정보는 [상수](sql-query-constants.md) 섹션을 참조하세요.  
  
- `input_alias`  
  
   `FROM` 절에 도입된 `input_alias`에서 정의된 값을 나타냅니다.  
  이 값은 **undefined** 가 되지 않도록 보장되며, 입력에 있는 **undefined** 값은 건너뜁니다.  
  
- `<scalar_expression>.property_name`  
  
   개체의 속성 값을 나타냅니다. 속성이 존재하지 않거나 개체가 아닌 값에서 참조되면 식이 **undefined** 값으로 평가됩니다.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   이름이 `property_name`인 속성 값 또는 배열의 인덱스가 `array_index`인 배열 요소를 나타냅니다. 속성/배열 인덱스가 존재하지 않거나 속성/배열이 아닌 값에서 참조되면 식이 undefined 값으로 평가됩니다.  
  
- `unary_operator <scalar_expression>`  
  
   단일 값에 적용되는 연산자를 나타냅니다. 자세한 내용은 [연산자](sql-query-operators.md) 섹션을 참조하세요.  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   두 값에 적용되는 연산자를 나타냅니다. 자세한 내용은 [연산자](sql-query-operators.md) 섹션을 참조하세요.  
  
- `<scalar_function_expression>`  
  
   함수 호출의 결과로 정의되는 값을 나타냅니다.  
  
- `udf_scalar_function`  
  
   사용자 정의 스칼라 함수의 이름입니다.  
  
- `builtin_scalar_function`  
  
   기본 제공 스칼라 함수의 이름입니다.  
  
- `<create_object_expression>`  
  
   지정된 속성 및 해당 값이 포함된 새 개체를 만들어서 얻는 값을 나타냅니다.  
  
- `<create_array_expression>`  
  
   요소로 지정된 값이 포함된 새 배열을 만들어서 얻는 값을 나타냅니다.  
  
- `parameter_name`  
  
   지정된 매개 변수 이름의 값을 나타냅니다. 매개 변수 이름에는 첫 번째 문자로 \@가 하나 있어야 합니다.  
  
## <a name="remarks"></a>설명
  
  기본 제공 또는 사용자 정의 스칼라 함수를 호출할 때 모든 인수가 정의되어야 합니다. 인수 중 하나라도 정의되지 않으면 함수가 호출되지 않고 결과가 정의되지 않습니다.  
  
  개체를 만들 때 정의되지 않은 값이 할당된 속성은 건너뛰고 만든 개체에 포함되지 않습니다.  
  
  배열을 만들 때 **undefined** 값이 할당된 요소 값은 건너뛰고 만든 개체에 포함되지 않습니다. 이렇게 하면 건너뛴 인덱스가 만드는 배열에 포함되지 않는 방식으로 다음에 정의된 요소가 해당 위치로 이동합니다.  

## <a name="examples"></a>예

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

결과는 다음과 같습니다.

```json
    [{
      "$1": 1.33333
    }]
```

다음 쿼리에서 스칼라 식의 결과는 부울입니다.

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

결과는 다음과 같습니다.

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 소개](introduction.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [하위 쿼리](sql-query-subquery.md)