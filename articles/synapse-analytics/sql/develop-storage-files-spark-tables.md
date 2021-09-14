---
title: 서버리스 SQL 풀에서 외부 테이블 정의에 대한 Apache Spark 동기화
description: 서버리스 SQL 풀을 사용하여 Spark 테이블 쿼리하는 방법 개요
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 642024d9554b51bc60df90cf3d5a7bdd799440b5
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432093"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-serverless-sql-pool"></a>서버리스 SQL 풀에서 Apache Spark for Azure Synapse 외부 테이블 정의 동기화

서버리스 SQL 풀은 Apache Spark에서 메타데이터를 자동으로 동기화할 수 있습니다. 서버리스 Apache Spark 풀에 있는 각 데이터베이스에 대해 서버리스 SQL 풀 데이터베이스가 생성됩니다. 

Parquet 또는 CSV를 기반으로 하고 Azure Storage에 있는 각 Spark 외부 테이블에 대해 서버리스 SQL 풀 데이터베이스에 외부 테이블이 생성됩니다. 따라서 Spark 풀을 종료하고 서버리스 SQL 풀에서 Spark 외부 테이블을 계속 쿼리할 수 있습니다.

테이블이 Spark에서 분할되면 스토리지의 파일은 폴더별로 구성됩니다. 서버리스 SQL 풀은 파티션 메타데이터를 사용하고 쿼리에 관련된 폴더와 파일만 대상으로 합니다.

Azure Synapse 작업 영역에서 프로비저닝된 각 서버리스 Apache Spark 풀에 대해 메타데이터 동기화가 자동으로 구성됩니다. Spark 외부 테이블을 즉시 쿼리할 수 있습니다.

Azure Storage에 있는 각 Spark Parquet 또는 CSV 외부 테이블은 서버리스 SQL 풀 데이터베이스에 해당하는 dbo 스키마의 외부 테이블로 표시됩니다. 

Spark 외부 테이블 쿼리의 경우 외부 [spark_table]을 대상으로 하는 쿼리를 실행합니다. 다음 예제를 실행하기 전에 파일이 있는 [스토리지 계정에 대한 액세스 권한](develop-storage-files-storage-access-control.md)이 올바른지 확인합니다.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Apache Spark 데이터 형식을 SQL 데이터 형식으로 매핑

| Spark 데이터 형식 | SQL 데이터 형식 | 주석 |
|---|---|---|
| `LongType`, `long`, `bigint`                | `bigint`              | **Spark**: *LongType* 은 8바이트 부호 있는 정수를 나타냅니다. [참조](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql) |
| `BooleanType`, `boolean`                    | `bit`(Parquet), `varchar(6)`(CSV)  | |
| `DecimalType`, `decimal`, `dec`, `numeric`  | `decimal`             | **Spark**: *DecimalType* 은 임의 전체 자릿수의 부호 있는 10진수를 나타냅니다. java.math.BigDecimal에 의해 내부적으로 지원됩니다. BigDecimal은 소수 자릿수가 아닌 임의 전체 자릿수의 정수 값과 32비트 정수 소수 자릿수로 구성됩니다. <br> **SQL**: 고정 전체 자릿수 및 소수 자릿수의 숫자입니다. 최대 전체 자릿수를 사용하는 경우 유효한 값은 - 10^38 + 1부터 10^38 - 1까지입니다. 국제 표준화 기구에서 정한 decimal의 동의어는 dec 및 dec(p, s) 입니다. numeric은 decimal과 기능적으로 동일합니다. [참조](/sql/t-sql/data-types/decimal-and-numeric-transact-sql]) |
| `IntegerType`, `Integer`, `int`             | `int`                 | **Spark**: *IntegerType* 은 4바이트 부호 있는 정수를 나타냅니다. [참조](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)|
| `ByteType`, `Byte`, `tinyint`               | `smallint`            | **Spark**: *ByteType* 은 1바이트 부호 있는 정수[-128~127]를 나타내고, ShortType은 2바이트 부호 있는 정수[-32768~32767]를 나타냅니다. <br> **SQL**: Tinyint는 1바이트 부호 있는 정수[0, 255]를 나타내고, smallint는 2바이트 부호 있는 정수[-32768, 32767]를 나타냅니다. [참조](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)|
| `ShortType`, `Short`, `smallint`            | `smallint`            | 위와 동일합니다. |
| `DoubleType`, `Double`                      | `float`               | **Spark**: *DoubleType* 은 8바이트 배정밀도 부동 소수점 숫자를 나타냅니다. **SQL**: [이 페이지를 방문](/sql/t-sql/data-types/float-and-real-transact-sql)하세요.|
| `FloatType`, `float`, `real`                | `real`                | **Spark**: *FloatType* 은 4바이트 배정밀도 부동 소수점 숫자를 나타냅니다. **SQL**: [이 페이지를 방문](/sql/t-sql/data-types/float-and-real-transact-sql)하세요.|
| `DateType`, `date`                          | `date`                | **Spark**: *DateType* 은 표준 시간대가 없는 연도, 월 및 일 필드의 값으로 구성된 값을 나타냅니다.|
| `TimestampType`, `timestamp`                | `datetime2`           | **Spark**: *TimestampType* 은 세션 현지 표준 시간대가 있는 연도, 월, 일, 시간, 분 및 초 필드의 값으로 구성된 값을 나타냅니다. 타임스탬프 값은 절대 시점을 나타냅니다.
| `char`                                      | `char`                |
| `StringType`, `String`, `varchar`           | `Varchar(n)`          | **Spark**: *StringType* 은 문자열 값을 나타냅니다. *VarcharType(n)* 은 길이 제한이 있는 StringType의 변형입니다. 입력 문자열이 길이 제한을 초과하면 데이터 쓰기가 실패합니다. 이 형식은 함수/연산자가 아닌 테이블 스키마에서만 사용할 수 있습니다.<br> *CharType(n)* 은 고정 길이인 *VarcharType(n)* 의 변형입니다. *CharType(n)* 형식의 열을 읽으면 항상 길이가 n인 문자열 값이 반환됩니다. Char 형식 열 비교는 짧은 열을 더 긴 길이로 채웁니다. <br> **SQL**: *Varchar(n)* 에서 n은 최대 8000일 수 있으며, 분할된 열인 경우 n은 최대 2048일 수 있습니다. <br> `Latin1_General_100_BIN2_UTF8` 데이터 정렬과 함께 사용합니다. |
| `BinaryType`, `binary`                      | `varbinary(n)`        | **SQL**: *Varbinary(n)* 에서 n은 최대 8000일 수 있으며, 분할된 열인 경우 n은 최대 2048일 수 있습니다. |
| `array`, `map`, `struct`                    | `varchar(max)`        | **SQL**: `Latin1_General_100_BIN2_UTF8` 데이터 정렬을 사용하여 JSON으로 직렬화합니다. |

\* 데이터베이스 수준 데이터 정렬은 Latin1_General_100_CI_AS_SC_UTF8입니다. \* 문자열 열 수준 데이터 정렬은 Latin1_General_100_BIN2_UTF8입니다.

\** ArrayType, MapType 및 StructType은 JSON으로 표시됩니다.

## <a name="next-steps"></a>다음 단계

스토리지 액세스 제어에 대해 자세히 알아보려면 [Storage Access Control](develop-storage-files-storage-access-control.md) 문서로 이동합니다.
