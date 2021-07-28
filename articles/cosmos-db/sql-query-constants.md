---
title: Azure Cosmos DB의 SQL 상수
description: Azure Cosmos DB의 SQL 쿼리 상수를 사용하여 특정 데이터 값을 나타내는 방법을 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: b0973dca980b65b85ac66cc1b3c9ed45f0f9c779
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339208"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Azure Cosmos DB SQL 쿼리 상수  
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 상수는 리터럴 값 또는 스칼라 값이라고도 하며 특정 데이터 값을 나타내는 기호입니다. 상수의 형식은 나타내는 값의 데이터 형식에 따라 다릅니다.  
  
 **지원되는 스칼라 데이터 형식:**  
  
|**유형**|**값 순서**|  
|-|-|  
|**정의되지 않음**|단일 값: **undefined**|  
|**Null**|단일 값: **null**|  
|**Boolean**|값: **false**, **true**.|  
|**Number**|IEEE 754 표준의 두 자리 부동 소수점 숫자입니다.|  
|**String**|0개 이상의 유니코드 문자 시퀀스입니다. 문자열은 작은따옴표 또는 큰 따옴표로 묶어야 합니다.|  
|**배열**|0개 이상의 요소 시퀀스입니다. 각 요소는 **Undefined** 를 제외한 모든 스칼라 데이터 형식의 값일 수 있습니다.|  
|**개체**|순서가 지정되지 않은 0개 이상의 이름/값 쌍의 집합입니다. 이름은 유니코드 문자열이며, 값은 **Undefined** 를 제외한 모든 스칼라 데이터 형식이 될 수 있습니다.|  
  
## <a name="syntax"></a><a name="bk_syntax"></a>구문
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
##  <a name="arguments"></a><a name="bk_arguments"></a> 인수
  
* `<undefined_constant>; Undefined`  
  
  Undefined 형식의 undefined 값을 나타냅니다.  
  
* `<null_constant>; null`  
  
  **Null** 형식의 **null** 값을 나타냅니다.  
  
* `<boolean_constant>`  
  
  Boolean 형식의 상수를 나타냅니다.  
  
* `false`  
  
  Boolean 형식의 **false** 값을 나타냅니다.  
  
* `true`  
  
  Boolean 형식의 **true** 값을 나타냅니다.  
  
* `<number_constant>`  
  
  상수를 나타냅니다.  
  
* `decimal_literal`  
  
  10진 리터럴은 10진수 표기법 또는 과학적 표기법을 사용하여 표현되는 숫자입니다.  
  
* `hexadecimal_literal`  
  
  16진 리터럴은 '0x' 접두사 다음에 하나 이상의 16진수가 나오는 숫자입니다.  
  
* `<string_constant>`  
  
  String 형식의 상수를 나타냅니다.  
  
* `string _literal`  
  
  문자열 리터럴은 연속적인 0이상의 유니코드 문자 또는 이스케이프 시퀀스로 표현되는 유니코드 문자열입니다. 문자열 리터럴은 작은따옴표(아포스트로피: ') 또는 큰따옴표(따옴표: ")로 묶어야 합니다.  
  
  허용되는 이스케이프 시퀀스는 다음과 같습니다.  
  
|**이스케이프 시퀀스**|**설명**|**유니코드 문자**|  
|-|-|-|  
|\\'|아포스트로피(')|U+0027|  
|\\"|따옴표(")|U+0022|  
|\\\ |백슬래시(\\)|U+005C|  
|\\/|슬래시(/)|U+002F|  
|\b|백스페이스|U+0008|  
|\f|폼 피드|U+000C|  
|\n|줄 바꿈|U+000A|  
|\r|캐리지 리턴|U+000D|  
|\t|탭|U+0009|  
|\uXXXX|4자리 16진수로 정의된 유니코드 문자|U+XXXX|  

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [모델 문서 데이터](modeling-data.md)
