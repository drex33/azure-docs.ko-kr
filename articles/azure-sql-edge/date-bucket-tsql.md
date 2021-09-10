---
title: Date_Bucket(Transact-SQL) - Azure SQL Edge
description: Azure SQL Edge에서 Date_Bucket을 사용하는 방법 알아보기
keywords: Date_Bucket, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: c270d0541a3fda8c42ec42f48a666b3d0aff5220
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537098"
---
# <a name="date_bucket-transact-sql"></a>Date_Bucket(Transact-SQL)

이 함수는 origin 매개 변수가 지정되지 않은 경우 `origin` 매개 변수로 정의된 타임스탬프 또는 `1900-01-01 00:00:00.000`의 기본 origin 값에서 각 datetime 버킷의 시작에 해당하는 datetime 값을 반환합니다.

모든 Transact-SQL 날짜 및 시간 데이터 형식 및 함수에 대한 개요는 [날짜 및 시간 데이터 형식 및 함수(Transact-SQL)](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql/)를 참조하세요.

[Transact-SQL 구문 표기 규칙](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql/)

## <a name="syntax"></a>구문

```syntaxsql
DATE_BUCKET (datePart, number, date, origin)
```

## <a name="arguments"></a>인수

*datePart*

‘number’ 매개 변수와 함께 사용되는 *date* 부분입니다. 예: 년, 월, 분, 초 등

> [!NOTE]
> `DATE_BUCKET`은 *datePart* 인수에 해당하는 사용자 정의 변수 항목을 허용하지 않습니다.

|*datePart*|약어|
|---|---|
|**day**|**dd**, **d**|
|**week**|**wk**, **ww**|
|**month**|**mm**, **m**|
|**quarter**|**qq**, **q**|
|**year**|**yy**, **yyyy**|
|**hour**|**hh**|
|**minute**|**mi**, **n**|
|**second**|**ss**, **s**|
|**millisecond**|**ms**|

*number*

*datePart* 인수와 결합된 버킷의 너비를 결정하는 정수입니다. 원본 시간으로부터의 dataPart 버킷 너비를 나타냅니다. **`This argument cannot be a negative integer value`**.

*date*

다음 값 중 하나를 확인할 수 있는 식입니다.

+ **date**
+ **datetime**
+ **datetime2**
+ **datetimeoffset**
+ **smalldatetime**
+ **time**

*date* 의 경우 위에서 언급한 데이터 형식 중 하나를 확인하는 경우 `DATE_BUCKET`은 열 식, 식 또는 사용자 정의 변수를 허용합니다.

**원본**

다음 값 중 하나를 확인할 수 있는 선택적 식입니다.

+ **date**
+ **datetime**
+ **datetime2**
+ **datetimeoffset**
+ **smalldatetime**
+ **time**

`Origin`의 데이터 형식은 `Date` 매개 변수의 데이터 형식과 일치해야 합니다.

함수에 대해 origin 값이 지정되지 않은 경우 `DATE_BUCKET`은 `1900-01-01 00:00:00.000`의 기본 원본 날짜 값(1900년 1월 1일 월요일 오전 12:00)을 사용합니다.

## <a name="return-type"></a>반환 형식

이 메서드의 반환 값 데이터 형식은 동적입니다. 반환 형식은 `date`에 제공된 인수의 따라 달라집니다. `date`에 대해 유효한 입력 데이터 형식이 제공되면 `DATE_BUCKET`이 동일한 데이터 형식을 반환합니다. `DATE_BUCKET`은 `date` 매개 변수에 문자열 리터럴이 지정된 경우 오류를 발생시킵니다.

## <a name="return-values"></a>반환 값

### <a name="understanding-the-output-from-date_bucket"></a>`DATE_BUCKET`의 출력 이해

`Date_Bucket`은 datePart 및 number 매개 변수에 해당하는 최신 날짜 또는 시간 값을 반환합니다. 예를 들어 아래 식에서 `Date_Bucket`은 `1900-01-01 00:00:00.000`의 기본 원본 시간에서 1주 버킷을 기반으로 출력이 계산되기 때문에 `2020-04-13 00:00:00.0000000`의 출력 값을 반환합니다. `2020-04-13 00:00:00.0000000` 값은 `1900-01-01 00:00:00.000`의 원본 값으로부터 6276주입니다.

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 1, @date)
```

아래의 모든 식에서 `2020-04-13 00:00:00.0000000`의 동일한 출력 값이 반환됩니다. `2020-04-13 00:00:00.0000000`은 원본 날짜로부터 6276주이며 6276은 2, 3, 4, 6으로 나뉘기 때문입니다.

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 2, @date)
Select DATE_BUCKET(wk, 3, @date)
Select DATE_BUCKET(wk, 4, @date)
Select DATE_BUCKET(wk, 6, @date)
```

아래 식의 출력은 `2020-04-06 00:00:00.0000000`이며, 기본 origin 시간인 `1900-01-01 00:00:00.000`으로부터 6275주입니다.

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 5, @date)
```

아래 식의 출력은 `2020-06-09 00:00:00.0000000`이며, 지정된 origin 시간인 `2019-01-01 00:00:00`으로부터 75주입니다.

```sql
declare @date datetime2 = '2020-06-15 21:22:11'
declare @origin datetime2 = '2019-01-01 00:00:00'
Select DATE_BUCKET(wk, 5, @date, @origin)
```

## <a name="datepart-argument"></a>datepart 인수

**dayofyear**, **day**, **weekday** 는 동일한 값을 반환합니다. 각 *datepart* 와 해당 약어는 동일한 값을 반환합니다.

## <a name="number-argument"></a>number 인수

*number* 인수는 양수 **int** 값의 범위를 초과할 수 없습니다. 다음 명령문에서 *number* 에 대한 인수는 **int** 범위를 1만큼 초과합니다. 다음 명령문은 “`Msg 8115, Level 16, State 2, Line 2. Arithmetic overflow error converting expression to data type int."`와 같은 오류 메시지를 반환합니다.

```sql
declare @date datetime2 = '2020-04-30 00:00:00'
Select DATE_BUCKET(dd, 2147483648, @date)
```

number에 대한 음수 값이 `Date_Bucket` 함수에 전달되면 다음과 같은 오류가 반환됩니다.

```txt
Msg 9834, Level 16, State 1, Line 1
Invalid bucket width value passed to date_bucket function. Only positive values are allowed.
````

## <a name="date-argument"></a>date 인수

`DATE_BUCKET`은 `date` 인수의 데이터 형식에 해당하는 기준 값을 반환합니다. 다음 예제에서는 datetime2 데이터 형식이 포함된 출력 값이 반환됩니다.

```sql
Select DATE_BUCKET(dd, 10, SYSUTCDATETIME())
```

## <a name="origin-argument"></a>origin 인수

`origin` 및 `date` 인수의 데이터 형식은 동일해야 합니다. 다른 데이터 형식을 사용하는 경우 오류가 생성됩니다.

## <a name="remarks"></a>설명

다음 절에서 `DATE_BUCKET`를 사용합니다.

+ GROUP BY
+ HAVING
+ ORDER BY
+ SELECT \<list>
+ WHERE

## <a name="examples"></a>예제

### <a name="a-calculating-date_bucket-with-a-bucket-width-of-1-from-the-origin-time"></a>A. 원본 시간으로부터 1의 버킷 너비로 Date_Bucket을 계산

이러한 각 명령문은 원본 시간으로부터 1의 버킷 너비로 *date_bucket* 을 증가시킵니다.

```sql
declare @date datetime2 = '2020-04-30 21:21:21'
Select 'Week',  DATE_BUCKET(wk, 1, @date)
Union All
Select 'Day',  DATE_BUCKET(dd, 1, @date)
Union All
Select 'Hour',  DATE_BUCKET(hh, 1, @date)
Union All
Select 'Minutes',  DATE_BUCKET(mi, 1, @date)
Union All
Select 'Seconds',  DATE_BUCKET(ss, 1, @date)
```

결과 집합은 다음과 같습니다.

```txt
Week    2020-04-27 00:00:00.0000000
Day     2020-04-30 00:00:00.0000000
Hour    2020-04-30 21:00:00.0000000
Minutes 2020-04-30 21:21:00.0000000
Seconds 2020-04-30 21:21:21.0000000
```

### <a name="b-using-expressions-as-arguments-for-the-number-and-date-parameters"></a>B. 식을 숫자 및 날짜 매개 변수에 대한 인수로 사용

이러한 예제에서는 여러 유형의 식을 *number* 및 *date* 매개 변수에 대한 인수로 사용합니다. 이러한 예제는 ‘AdventureWorksDW2017’ 데이터베이스를 사용하여 빌드됩니다.

#### <a name="specifying-user-defined-variables-as-number-and-date"></a>사용자 정의 변수를 숫자 및 날짜로 지정

이 예제에서는 사용자 정의 변수를 *number* 및 *date* 에 대한 인수로 지정합니다.

```sql
DECLARE @days int = 365,
        @datetime datetime2 = '2000-01-01 01:01:01.1110000'; /* 2000 was a leap year */;
SELECT Date_Bucket(day, @days, @datetime);
```

결과 집합은 다음과 같습니다.

```txt
---------------------------
1999-12-08 00:00:00.0000000

(1 row affected)
```

#### <a name="specifying-a-column-as-date"></a>열을 날짜로 지정

아래 예제에서는 주간 날짜 버킷에 따라 그룹화된 OrderQuantity의 합계와 UnitPrice의 합계를 계산합니다.

```sql
SELECT
    Date_Bucket(week, 1 ,cast(Shipdate as datetime2)) AS ShippedDateBucket
    ,Sum(OrderQuantity)  As SumOrderQuantity
    ,Sum(UnitPrice) As SumUnitPrice
FROM dbo.FactInternetSales FIS
where Shipdate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
Group by Date_Bucket(week, 1 ,cast(Shipdate as datetime2))
order by 1
```

결과 집합은 다음과 같습니다.

```txt
ShippedDateBucket           SumOrderQuantity SumUnitPrice
--------------------------- ---------------- ---------------------
2011-01-03 00:00:00.0000000 21               65589.7546
2011-01-10 00:00:00.0000000 27               89938.5464
2011-01-17 00:00:00.0000000 31               104404.9064
2011-01-24 00:00:00.0000000 36               118525.6846
2011-01-31 00:00:00.0000000 39               123555.431
2011-02-07 00:00:00.0000000 35               109342.351
2011-02-14 00:00:00.0000000 32               107804.8964
2011-02-21 00:00:00.0000000 37               119456.3428
2011-02-28 00:00:00.0000000 9                28968.6982
```

#### <a name="specifying-scalar-system-function-as-date"></a>스칼라 시스템 함수를 날짜로 지정

이 예제에서는 *date* 에 대해 `SYSDATETIME`을 지정합니다. 반환되는 정확한 값은 명령문 실행의 날짜와 시간에 따라 달라집니다.

```sql
SELECT Date_Bucket(wk, 10, SYSDATETIME());
```

결과 집합은 다음과 같습니다.

```txt
---------------------------
2020-03-02 00:00:00.0000000

(1 row affected)
```

#### <a name="specifying-scalar-subqueries-and-scalar-functions-as-number-and-date"></a>스칼라 하위 쿼리 및 스칼라 함수를 숫자 및 날짜로 지정

이 예제에서는 스칼라 하위 쿼리인 `MAX(OrderDate)`를 *number* 및 *date* 에 대한 인수로 사용합니다. `(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100)`은 값 목록에서 *number* 인수를 선택하는 방법을 보여 주기 위하여 만든 숫자 매개 변수에 대한 인수로 작용합니다.

```sql
SELECT DATE_BUCKET(week,(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100),
    (SELECT MAX(OrderDate) FROM dbo.FactInternetSales));
```

#### <a name="specifying-numeric-expressions-and-scalar-system-functions-as-number-and-date"></a>숫자 식 및 스칼라 시스템 함수를 숫자 및 날짜로 지정

이 예제에서는 숫자 식((10/2))과 스칼라 시스템 함수(SYSDATETIME)를 number 및 date에 대한 인수로 사용합니다.

```sql
SELECT Date_Bucket(week,(10/2), SYSDATETIME());
```

#### <a name="specifying-an-aggregate-window-function-as-number"></a>집계 창 함수를 숫자로 지정

이 예제에서는 집계 창 함수를 *number* 인수로 사용합니다.

```sql
Select
    DISTINCT DATE_BUCKET(day, 30, Cast([shipdate] as datetime2)) as DateBucket,
    First_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as First_Value_In_Bucket,
    Last_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as Last_Value_In_Bucket
    from [dbo].[FactInternetSales]
Where ShipDate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
order by DateBucket
GO
```
### <a name="c-using-a-non-default-origin-value"></a>C. 기본 origin 값이 아닌 값 사용

이 예제에서는 기본 origin 값이 아닌 값을 사용하여 date 버킷을 생성합니다.

```sql
declare @date datetime2 = '2020-06-15 21:22:11'
declare @origin datetime2 = '2019-01-01 00:00:00'
Select DATE_BUCKET(hh, 2, @date, @origin)
```

## <a name="see-also"></a>참고 항목

[CAST 및 CONVERT&#40;Transact-SQL&#41;](/sql/t-sql/functions/cast-and-convert-transact-sql/)
