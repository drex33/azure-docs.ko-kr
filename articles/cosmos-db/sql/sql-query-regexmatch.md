---
title: Azure Cosmos DB 쿼리 언어의 RegexMatch
description: Azure Cosmos DB의 SQL 시스템 함수 RegexMatch에 대해 알아보기
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/12/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: c4934b1d4c5b88212298dc0db2b28b678711d76a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122567624"
---
# <a name="regexmatch-azure-cosmos-db"></a>REGEXMATCH(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

정규식 기능을 제공합니다. 정규식은 텍스트 패턴을 검색하기 위한 간결하고 유연한 표기법입니다. Azure Cosmos DB는 [PCRE(PERL 호환 정규식)](http://www.pcre.org/)를 사용합니다. 

## <a name="syntax"></a>구문
  
```sql
RegexMatch(<str_expr1>, <str_expr2>, [, <str_expr3>])  
```  
  
## <a name="arguments"></a>인수
  
*str_expr1*  
   검색할 문자열 식입니다.  
  
*str_expr2*  
   정규식입니다.

*str_expr3*  
   정규식에 사용할 선택된 한정자의 문자열입니다. 이 문자열 값은 선택 사항입니다. 한정자 없이 RegexMatch를 실행하려면 빈 문자열을 추가하거나 전체를 생략할 수 있습니다. 

[Perl에서 정규식을 만들기 위한 구문](https://perldoc.perl.org/perlre)에 대해 알아볼 수 있습니다. 

Azure Cosmos DB는 다음과 같은 네 가지 한정자를 지원합니다.

| 한정자 | 설명 |
| ------ | ----------- |
| `m` | 여러 줄로 검색되도록 문자열 식을 처리합니다. 이 옵션이 없으면 "^" 및 "$"는 각 개별 줄이 아니라 문자열의 시작 또는 끝 부분에서 일치합니다. |
| `s` | 줄 바꿈 문자를 포함하여 모든 문자를 일치시키려면 "."를 사용합니다. | 
| `i` | 패턴 일치의 경우 대/소문자를 무시합니다. |
| `x` | 모든 공백 문자를 무시합니다. |

## <a name="return-types"></a>반환 형식
  
  부울 식을 반환합니다. 검색할 문자열 식, 정규식 또는 선택한 한정자가 잘못된 경우 정의되지 않은 값을 반환합니다.
  
## <a name="examples"></a>예
  
다음 간단한 RegexMatch 예제에서는 몇 가지 다른 한정자를 사용하여 정규식 일치에 대해 문자열 "abcd"를 확인합니다.
  
```sql
SELECT RegexMatch ("abcd", "ABC", "") AS NoModifiers, 
RegexMatch ("abcd", "ABC", "i") AS CaseInsensitive, 
RegexMatch ("abcd", "ab.", "") AS WildcardCharacter,
RegexMatch ("abcd", "ab c", "x") AS IgnoreWhiteSpace, 
RegexMatch ("abcd", "aB c", "ix") AS CaseInsensitiveAndIgnoreWhiteSpace 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[
    {
        "NoModifiers": false,
        "CaseInsensitive": true,
        "WildcardCharacter": true,
        "IgnoreWhiteSpace": true,
        "CaseInsensitiveAndIgnoreWhiteSpace": true
    }
]
```

RegexMatch를 사용하는 경우 StartsWith, EndsWith, Contains 또는 StringEquals 시스템 함수를 사용하여 다른 방법으로는 불가능한 보다 복잡한 문자열 검색을 수행하기 위해 메타문자를 사용할 수 있습니다. 다음은 몇 가지 추가 예제입니다.

> [!NOTE] 
> 정규식에서 메타문자를 사용해야 하고 특별한 의미가 없도록 하려면 `\`를 사용하여 메타문자를 이스케이프해야 합니다.

**"소금" 단어가 정확히 한 번 나오는 설명이 있는 항목을 확인합니다.**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "salt{1}","")
```

**0에서 99 사이의 숫자가 포함된 설명이 있는 항목을 확인합니다.**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "[0-99]","")
```

**"S" 또는 "s"로 시작하는 4문자 단어가 포함된 항목을 확인합니다.**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, " s... ","i")
```

## <a name="remarks"></a>설명

이 시스템 함수는 정규식을 StartsWith, EndsWith, Contains 또는 StringEquals 시스템 함수로 나눌 수 있는 경우 이 시스템 함수는 [범위 인덱스](../index-policy.md#includeexclude-strategy)를 활용합니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
