---
title: 쿼리 가속 SQL 언어 참조
titleSuffix: Azure Storage
description: 쿼리 가속 SQL 구문을 사용하는 방법에 관해 알아봅니다.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: b2a5a0f6f97d402c55cd47293ea668284e77363e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593138"
---
# <a name="query-acceleration-sql-language-reference"></a>쿼리 가속 SQL 언어 참조

쿼리 가속은 Blob 콘텐츠에 대한 쿼리를 표현하기 위해 ANSI SQL과 같은 언어를 지원합니다. 쿼리 가속 SQL 언어는 지원되는 데이터 형식, 연산자 등이 제한된 ANSI SQL의 하위 집합이지만, JSON과 같은 계층식 반구조형 데이터 형식에 대한 쿼리를 지원하기 위해 ANSI SQL에서도 확장됩니다.

## <a name="select-syntax"></a>SELECT 구문

쿼리 가속에서 지원되는 유일한 SQL 문은 SELECT 문입니다. 이 예에서는 식이 true를 반환하는 모든 행을 반환합니다.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

CSV 형식 데이터의 경우 *테이블* 은 `BlobStorage`여야 합니다. 즉, REST 호출에 지정된 blob에 대해 쿼리가 실행됩니다. JSON 형식 데이터의 경우 *테이블* 은 “테이블 설명자”입니다.   이 문서의 [테이블 설명자](#table-descriptors) 섹션을 참조하세요.

다음 예에서는 WHERE *식* 이 true를 반환하는 각 행에 대해 이 문에서 각 프로젝션 표현식을 평가하여 만든 새 행을 반환합니다.

```sql
SELECT expression [, expression ...] FROM table [WHERE expression] [LIMIT limit]
```

SELECT 식의 일부로 하나 이상의 특정 열을 지정할 수 있습니다 (예: `SELECT Title, Author, ISBN` ).

> [!NOTE]
> SELECT 식에서 사용할 수 있는 특정 열의 최대 수는 49입니다. 49개 이상의 열을 반환하기 위해 SELECT 문이 필요한 경우 SELECT 식에 와일드카드 문자(`*`)를 사용합니다(예: `SELECT *`).

다음 예에서는 *식* 이 true를 반환하는 각 행에 대해 집계 계산(예: 특정 열의 평균값)을 반환합니다.

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

다음 예에서는 CSV 형식 blob을 분할하는 데 적합한 오프셋을 반환합니다. 이 문서의 [Sys.Split](#sys-split) 섹션을 참조하세요.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types"></a>

## <a name="data-types"></a>데이터 형식

|데이터 형식|설명|
|---------|-------------------------------------------|
|INT      |부호 있는 64비트 정수.                     |
|FLOAT    |64비트(“배정밀도”) 부동 소수점입니다.|
|STRING   |가변 길이 유니코드 문자열입니다.            |
|timestamp|지정 시간입니다.                           |
|BOOLEAN  |True 또는 False입니다.                             |

CSV 형식 데이터에서 값을 읽을 때 모든 값을 문자열로 읽습니다. CAST 식을 사용하여 문자열 값을 다른 형식으로 변환할 수 있습니다. 컨텍스트에 따라 값이 암시적으로 다른 형식으로 캐스팅될 수 있습니다. 자세한 내용은 [데이터 형식 우선 순위(Transact-SQL)](/sql/t-sql/data-types/data-type-precedence-transact-sql)를 참조하세요.

## <a name="expressions"></a>식

### <a name="referencing-fields"></a>참조 필드

JSON 형식 데이터 또는 헤더 행이 있는 CSV 형식 데이터의 경우 이름으로 필드를 참조할 수 있습니다. 필드 이름에는 따옴표가 붙거나 붙지 않을 수 있습니다. 따옴표로 묶은 필드 이름은 큰따옴표 문자 ()로 묶여 있으며 `"` , 공백을 포함할 수 있고, 대/소문자를 구분 합니다. 따옴표가 붙지 않은 필드 이름은 대/소문자를 구분하지 않으며 특수 문자를 포함할 수 없습니다.

CSV 형식 데이터의 경우 필드를 서 수로 참조 하 고, 앞에 밑줄 () 문자를 붙일 수도 있습니다 `_` . 예를 들어 첫 번째 필드는로 참조 `_1` 되거나 11 번째 필드는로 참조 될 수 있습니다 `_11` . 서수로 필드를 참조하면 헤더 행을 포함하지 않는 CSV 형식 데이터에 유용합니다. 이 경우 특정 필드를 참조하는 유일한 방법은 서수를 사용하는 것입니다.

### <a name="operators"></a>연산자

지원되는 표준 SQL 연산자는 다음과 같습니다.

|연산자|설명|
|--|--|
|[`=`](/sql/t-sql/language-elements/equals-transact-sql)    |두 식이 같은지 비교합니다(비교 연산자).|
|[`!=`](/sql/t-sql/language-elements/not-equal-to-transact-sql-exclamation)    |식이 다른 식과 같지 않은지 테스트합니다(비교 연산자).|
|[`<>`](/sql/t-sql/language-elements/not-equal-to-transact-sql-traditional)    |두 식이 서로 같지 않은지 비교합니다(비교 연산자).|
|[`<`](/sql/t-sql/language-elements/less-than-transact-sql)    |두 식 중 한 식이 작은지 비교합니다(비교 연산자).|
|[`<=`](/sql/t-sql/language-elements/less-than-or-equal-to-transact-sql)    |두 식 중 한 식이 작거나 같은지 비교합니다(비교 연산자).|
|[`>`](/sql/t-sql/language-elements/greater-than-transact-sql)    |두 식 중 한 식이 큰지 비교합니다(비교 연산자). |
|[`>=`](/sql/t-sql/language-elements/greater-than-or-equal-to-transact-sql)    |한 식이 다른 한 식보다 크거나 같은지 비교합니다(비교 연산자).|
|[`+`](/sql/t-sql/language-elements/add-transact-sql)    |두 숫자를 더합니다. 이 더하기 산술 연산자를 사용하여 날짜에 일 수를 더할 수도 있습니다.|
|[`-`](/sql/t-sql/language-elements/subtract-transact-sql)    |두 숫자를 빼는 빼기 산술 연산자입니다. |
|[`/`](/sql/t-sql/language-elements/divide-transact-sql)    |숫자를 다른 숫자로 나눕니다(산술 나누기 연산자).|
|[`*`](/sql/t-sql/language-elements/multiply-transact-sql)    |두 식을 곱합니다(산술 곱하기 연산자).|
|[`%`](/sql/t-sql/language-elements/modulo-transact-sql)    |한 숫자를 다른 숫자로 나눈 나머지를 반환합니다.|
|[`AND`](/sql/t-sql/language-elements/bitwise-and-transact-sql)    |두 정수 값 간에 비트 논리 AND 연산을 수행합니다.|
|[`OR`](/sql/t-sql/language-elements/bitwise-or-transact-sql)    |Transact-SQL 문에서 이진 식으로 변환되는 두 개의 지정된 정수 값 간에 비트 논리 OR 연산을 수행합니다.|
|[`NOT`](/sql/t-sql/language-elements/not-transact-sql)    |부울 입력을 부정합니다.|
|[`CAST`](/sql/t-sql/functions/cast-and-convert-transact-sql)    |한 데이터 형식의 식을 다른 데이터 형식으로 변환합니다.|
|[`BETWEEN`](/sql/t-sql/language-elements/between-transact-sql)    |테스트할 범위를 지정합니다.|
|[`IN`](/sql/t-sql/language-elements/in-transact-sql)    |지정된 값과 일치하는 값이 하위 쿼리 또는 목록 내에 있는지 확인합니다.|
|[`NULLIF`](/sql/t-sql/language-elements/nullif-transact-sql)    |지정된 두 식이 같으면 Null 값을 반환합니다.|
|[`COALESCE`](/sql/t-sql/language-elements/coalesce-transact-sql)    |인수를 순서대로 평가하고 처음으로 NULL이 아닌 첫 번째 식의 현재 값을 반환합니다.|

연산자의 왼쪽과 오른쪽에 있는 데이터 형식이 다른 경우 [데이터 형식 우선 순위(Transact-SQL)](/sql/t-sql/data-types/data-type-precedence-transact-sql)에 지정된 규칙에 따라 자동 변환이 수행됩니다.

쿼리 가속 SQL 언어는 해당 문서에서 설명하는 데이터 형식의 매우 작은 하위 집합만 지원합니다. 이 문서의 [데이터 형식](#data-types) 섹션을 참조하세요.

### <a name="casts"></a>캐스팅

쿼리 가속 SQL 언어는 [데이터 형식 변환(데이터베이스 엔진)](/sql/t-sql/data-types/data-type-conversion-database-engine)의 규칙에 따라 CAST 연산자를 지원합니다.

쿼리 가속 SQL 언어는 해당 문서에서 설명하는 데이터 형식의 작은 하위 집합만 지원합니다. 이 문서의 [데이터 형식](#data-types) 섹션을 참조하세요.

### <a name="string-functions"></a>문자열 함수

쿼리 가속 SQL 언어는 다음과 같은 표준 SQL 문자열 함수를 지원합니다.

|함수|설명|
|--|--|
|CHAR_LENGTH    | 문자열 표현식이 문자 데이터 형식이면 문자열 표현식의 문자 길이를 반환합니다. 그렇지 않으면 문자열 표현식의 길이를 바이트 단위로 반환합니다(비트 수를 8로 나눈 값보다 작지 않은 가장 작은 정수). (이 함수는 CHARACTER_LENGTH 함수와 같습니다.)|
|CHARACTER_LENGTH    |문자열 표현식이 문자 데이터 형식이면 문자열 표현식의 문자 길이를 반환합니다. 그렇지 않으면 문자열 표현식의 길이를 바이트 단위로 반환합니다(비트 수를 8로 나눈 값보다 작지 않은 가장 작은 정수). 이 함수는 CHAR_LENGTH 함수와 같습니다.|
|[LOWER](/sql/t-sql/functions/lower-transact-sql)    |대문자 데이터를 소문자 데이터로 변환한 후에 문자 식을 반환합니다.|
|[UPPER](/sql/t-sql/functions/upper-transact-sql)    |소문자 데이터를 대문자로 변환한 문자 식을 반환합니다.|
|[SUBSTRING](/sql/t-sql/functions/substring-transact-sql)    |SQL Server에서 문자, 이진, 텍스트 또는 이미지 식의 일부를 반환합니다.|
|[TRIM](/sql/t-sql/functions/trim-transact-sql)    |문자열의 시작 또는 끝에서 공백 문자(32)나 기타 지정되지 않은 문자를 제거합니다.|
|LEADING    |문자열의 시작에서 공백 문자(32)나 기타 지정되지 않은 문자를 제거합니다.|
|TRAILING    |문자열의 끝에서 공백 문자(32)나 기타 지정되지 않은 문자를 제거합니다.|

다음은 몇 가지 예제입니다.

|기능|예제|결과|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

### <a name="date-functions"></a>날짜 함수

지원되는 표준 SQL 날짜 함수는 다음과 같습니다.

- `DATE_ADD`
- `DATE_DIFF`
- `EXTRACT`
- `TO_STRING`
- `TO_TIMESTAMP`

현재 [표준 IS08601의 모든 날짜 형식이](https://www.w3.org/TR/NOTE-datetime) 변환 됩니다.

#### <a name="date_add-function"></a>DATE_ADD 함수

쿼리 가속 SQL 언어는 `DATE_ADD` 함수용으로 년, 월, 일, 시, 분, 초를 지원합니다.

예:

```sql
DATE_ADD(datepart, quantity, timestamp)
DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### <a name="date_diff-function"></a>DATE_DIFF 함수

쿼리 가속 SQL 언어는 `DATE_DIFF` 함수용으로 년, 월, 일, 시, 분, 초를 지원합니다.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>EXTRACT 함수

`DATE_ADD` 함수에 지원되는 날짜 파트가 아닌 EXTRACT의 경우 쿼리 가속 SQL 언어는 timezone_hour와 timezone_minute를 날짜 파트로 지원합니다.

예:

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>TO_STRING 함수

예:

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

이 테이블에서는 `TO_STRING` 함수의 출력 형식을 지정하는 데 사용할 수 있는 문자열을 설명합니다.

|형식 문자열    |출력                               |
|-----------------|-------------------------------------|
|yy               |2 자리 형식의 연도-1999을 ' 99 '로|
|y                |4자리 형식의 연도               |
|yyyy             |4자리 형식의 연도               |
|M                |연간 월-1                    |
|MM               |0으로 채워진 월-01               |
|MMM              |약어 연간 월-1 월            |
|MMMM             |전체 월-5 월                      |
|일                |날짜(1-31)                  |
|dd               |0으로 채워진 날짜(01-31)     |
|a                |AM 또는 PM                             |
|h                |하루 중 시간(1-12)                   |
|hh               |0으로 채워진 하루 중 시간(01-12)     |
|H                |하루 중 시간(0-23)                   |
|HH               |0으로 채워진 하루 중 시간(00-23)      |
|분                |1시간 중 분(0-59)                |
|MM               |0으로 채워진 분(00-59)           |
|초                |1분 중 초(0-59)             |
|ss               |0으로 채워진 초(00-59)          |
|S                |초의 소수 부분(0.1-0.9)        |
|SS               |초의 소수 부분(0.01-0.99)      |
|SSS              |초의 소수 부분(0.001-0.999)    |
|X                |시간 단위의 오프셋                      |
|XX 또는 XXXX       |시간 및 분 단위의 오프셋(+0430)  |
|XXX 또는 XXXXX     |시간 및 분 단위의 오프셋(-07:00) |
|x                |시간 단위의 오프셋(7)                  |
|xx 또는 xxxx       |시간 및 분 단위의 오프셋(+0530)    |
|Xxx 또는 xxxxx     |시간 및 분 단위의 오프셋(+05:30)   |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP 함수

IS08601 형식만 지원됩니다.

예:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> `UTCNOW` 함수를 사용하여 시스템 시간도 얻을 수 있습니다.

## <a name="aggregate-expressions"></a>집계 식

SELECT 문에는 하나 이상의 프로젝션 식이나 단일 집계 식이 포함될 수 있습니다. 다음과 같은 집계 식이 지원됩니다.

|식|설명|
|--|--|
|[COUNT(\*)](/sql/t-sql/functions/count-transact-sql)    |조건자 식과 일치하는 레코드 수를 반환합니다.|
|[COUNT(expression)](/sql/t-sql/functions/count-transact-sql)    |식이 null이 아닌 레코드 수를 반환합니다.|
|[AVERAGE(expression)](/sql/t-sql/functions/avg-transact-sql)    |식의 null이 아닌 값의 평균을 반환합니다.|
|[MIN(expression)](/sql/t-sql/functions/min-transact-sql)    |식의 null이 아닌 최소값을 반환합니다.|
|[MAX(expression](/sql/t-sql/functions/max-transact-sql)    |식의 null이 아닌 최대값을 반환합니다.|
|[SUM(expression)](/sql/t-sql/functions/sum-transact-sql)    |식의 null이 아닌 모든 값의 합계를 반환합니다.|

### <a name="missing"></a>MISSING

`IS MISSING` 연산자는 쿼리 가속화 SQL 언어가 지원하는 유일한 비표준입니다. JSON 데이터의 경우 특정 입력 레코드에서 필드가 누락되면 표현식 필드 `IS MISSING`은 부울 값 true로 평가됩니다.

<a id="table-descriptors"></a>

## <a name="table-descriptors"></a>테이블 설명자

CSV 데이터의 테이블 이름은 항상 `BlobStorage`입니다. 예를 들면 다음과 같습니다.

```sql
SELECT * FROM BlobStorage
```

JSON 데이터의 경우 추가 옵션을 사용할 수 있습니다.

```sql
SELECT * FROM BlobStorage[*].path
```

그러면 JSON 데이터의 하위 집합을 쿼리할 수 있습니다.

JSON 쿼리의 경우 FROM 절의 파트에서 경로를 언급할 수 있습니다. 해당 경로는 JSON 데이터의 하위 집합을 구문 분석하는 데 도움이 됩니다. 해당 경로는 JSON 배열과 개체 값을 참조할 수 있습니다.

더 자세히 이해하기 위해 예를 살펴보겠습니다.

다음은 샘플 데이터입니다.

```json
{
  "id": 1,
  "name": "mouse",
  "price": 12.5,
  "tags": [
    "wireless",
    "accessory"
  ],
  "dimensions": {
    "length": 3,
    "width": 2,
    "height": 2
  },
  "weight": 0.2,
  "warehouses": [
    {
      "latitude": 41.8,
      "longitude": -87.6
    }
  ]
}
```

위 데이터의 `warehouses` JSON 개체에만 관심이 있을 수 있습니다. `warehouses` 개체는 JSON 배열 형식이므로 FROM 절에서 이를 언급할 수 있습니다. 샘플 쿼리는 다음과 같을 수 있습니다.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

쿼리를 통해 모든 필드를 가져오지만, 위도만 선택합니다.

`dimensions` JSON 개체 값에만 액세스하려는 경우 쿼리에서 해당 개체 참조를 사용할 수 있습니다. 예를 들면 다음과 같습니다.

```sql
SELECT length FROM BlobStorage[*].dimensions
```

또한 `dimensions` 개체의 멤버에 대한 액세스를 제한합니다. JSON 필드의 다른 멤버와 JSON 개체의 내부 값에 액세스하려면 다음 예제와 같은 쿼리를 사용할 수 있습니다.

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage와 BlobStorage[\*] 모두 전체 개체를 참조합니다. 그러나 FROM 절에 경로가 있으면 BlobStorage[\*].path를 사용해야 합니다.

<a id="sys-split"></a>

## <a name="syssplit"></a>Sys.Split

CSV 형식의 데이터에만 사용할 수 있는 SELECT 문의 특수 형식입니다.

```sql
SELECT sys.split(split_size) FROM BlobStorage
```

CSV 데이터 레코드를 일괄 처리로 다운로드하여 처리하려는 경우 이 문을 사용합니다. 그러면 한 번에 모든 레코드를 다운로드하는 대신 병렬로 레코드를 처리할 수 ​​있습니다. 이 문은 CSV 파일에서 레코드를 반환하지 않습니다. 대신 일괄 처리 크기의 컬렉션을 반환합니다. 그런 다음 각 일괄 처리 크기를 사용하여 데이터 레코드 일괄 처리를 검색할 수 있습니다.

*split_size* 매개 변수를 사용하여 각 일괄 처리에 포함할 바이트 수를 지정합니다. 예를 들어 한 번에 10MB의 데이터만 처리하려는 경우 10MB는 10,485,760바이트와 같기 때문에 `SELECT sys.split(10485760)FROM BlobStorage`와 같은 문입니다. 각 일괄 처리에는 해당 10MB에 들어갈 수 있는 레코드 수가 포함됩니다.

대부분 각 일괄 처리의 크기는 사용자가 지정하는 수보다 약간 큽니다. 일괄 처리에 부분 레코드를 포함할 수 없기 때문입니다. 일괄 처리의 마지막 레코드가 임계값이 끝나기 전에 시작되면 전체 레코드를 포함할 수 있도록 일괄 처리가 커집니다. 마지막 일괄 처리의 크기는 사용자가 지정하는 크기보다 작을 가능성이 큽니다.

> [!NOTE]
> split_size는 10MB(10485760) 이상이어야 합니다.

## <a name="see-also"></a>참고 항목

- [Azure Data Lake Storage 쿼리 가속](data-lake-storage-query-acceleration.md)
- [Azure Data Lake 스토리지 쿼리 가속을 사용하여 데이터 필터링](data-lake-storage-query-acceleration-how-to.md)
