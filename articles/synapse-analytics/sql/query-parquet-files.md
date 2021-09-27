---
title: 서버리스 SQL 풀을 사용하여 Parquet 파일 쿼리
description: 이 문서에서는 서버리스 SQL 풀을 사용하여 Parquet 파일을 쿼리하는 방법을 알아봅니다.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 6ed309637c03fc4701f2b973f64b2473f43b0d2a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124734880"
---
# <a name="query-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 서버리스 SQL 풀을 사용하여 Parquet 파일 쿼리

이 문서에서는 Parquet 파일을 읽는 서버리스 SQL 풀을 사용하여 쿼리를 작성하는 방법을 알아봅니다.

## <a name="quickstart-example"></a>빠른 시작 예제

`OPENROWSET` 함수를 사용하면 파일에 URL을 제공하여 Parquet 파일의 콘텐츠를 읽을 수 있습니다.

### <a name="read-parquet-file"></a>Parquet 파일 읽기

`PARQUET` 파일의 콘텐츠를 확인하는 가장 쉬운 방법은 `OPENROWSET` 함수에 파일 URL을 제공하고 parquet `FORMAT`을 지정하는 것입니다. 파일이 공개적으로 사용할 수 있거나 Azure AD ID로 이 파일에 액세스할 수 있다면 다음 예제와 같이 쿼리를 사용하여 파일의 콘텐츠를 볼 수 있습니다.

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet',
    format = 'parquet') as rows
```

이 파일에 액세스할 수 있는지 확인합니다. 파일이 SAS 키 또는 사용자 지정 Azure ID를 통해 보호되는 경우, [SQL 로그인에 대한 서버 수준 자격 증명](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential)을 설정해야 합니다.

> [!IMPORTANT]
> PARQUET 파일의 문자열 값이 UTF-8 인코딩을 사용하여 인코딩되므로 UTF-8 데이터베이스 데이터 정렬(예: `Latin1_General_100_BIN2_UTF8`)을 사용하고 있는지 확인합니다.
> PARQUET 파일의 텍스트 인코딩과 데이터 정렬이 일치하지 않으면 예기치 않은 변환 오류가 발생할 수 있습니다.
> 다음 T-SQL 문 `alter database current collate Latin1_General_100_BIN2_UTF8`'을 사용하여 현재 데이터베이스의 기본 데이터 정렬을 쉽게 변경할 수 있습니다.

데이터 정렬을 사용 하는 경우 `Latin1_General_100_BIN2_UTF8` 다른 데이터 정렬과 비교 하 여 추가적인 성능 향상을 얻을 수 있습니다. `Latin1_General_100_BIN2_UTF8`데이터 정렬은 parquet 문자열 정렬 규칙과 호환 됩니다. SQL 풀은 쿼리에 필요한 데이터 (파일/열 세그먼트 정리)를 포함 하지 않을 parquet 파일의 일부를 제거할 수 있습니다. 다른 데이터 정렬을 사용 하는 경우 parquet 파일의 모든 데이터가 Synapse SQL 로드 되 고 SQL 프로세스 내에서 필터링 됩니다. `Latin1_General_100_BIN2_UTF8`데이터 정렬에는 parquet 및 CosmosDB에 대해서만 작동 하는 추가 성능 최적화가 있습니다. 단점은 대/소문자 구분 안함과 같은 세분화된 비교 규칙을 잃게 된다는 것입니다.

### <a name="data-source-usage"></a>데이터 원본 사용

이전 예제에서는 파일에 전체 경로를 사용했습니다. 전체 경로 대신 스토리지의 루트 폴더를 가리키는 위치를 사용하여 외부 데이터 원본을 만들고 해당 데이터 원본 및 `OPENROWSET` 함수에서 파일의 상대 경로를 사용할 수 있습니다.

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) as rows
```

데이터 원본이 SAS 키 또는 사용자 지정 ID를 통해 보호되는 경우 [데이터베이스 범위 자격 증명을 사용하여 데이터 원본](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)을 구성할 수 있습니다.

### <a name="explicitly-specify-schema"></a>스키마를 명시적으로 지정

`OPENROWSET`을 사용하면 `WITH` 절을 사용하여 파일에서 읽을 열을 명시적으로 지정할 수 있습니다.

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) with ( date_rep date, cases int, geo_id varchar(6) ) as rows
```

> [!IMPORTANT]
> `WITH` 절의 모든 문자열 열에 대해 UTF-8 데이터 정렬(예: `Latin1_General_100_BIN2_UTF8`)을 명시적으로 지정하거나 데이터베이스 수준에서 UTF-8 데이터 정렬을 설정합니다.
> 파일 및 문자열 열 데이터 정렬에서 텍스트 인코딩이 일치하지 않으면 예기치 않은 변환 오류가 발생할 수 있습니다.
> 다음 T-SQL 문 `alter database current collate Latin1_General_100_BIN2_UTF8`을 사용하여 현재 데이터베이스의 기본 데이터 정렬을 쉽게 변경할 수 있습니다.
> 다음 정의 `geo_id varchar(6) collate Latin1_General_100_BIN2_UTF8`을 사용하여 열 형식에 대한 데이터 정렬을 쉽게 설정할 수 있습니다.

다음 섹션에서는 다양한 유형의 PARQUET 파일을 쿼리하는 방법을 알아볼 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

첫 번째 단계는 [NYC Yellow Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) 스토리지 계정을 참조하는 데이터 원본을 통해 **데이터베이스를 만드는** 것입니다. 그런 다음, 해당 데이터베이스에서 [설치 스크립트](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)를 실행하여 개체를 초기화합니다. 이 설치 스크립트는 이러한 샘플에서 사용되는 데이터 원본, 데이터베이스 범위 자격 증명 및 외부 파일 형식을 만듭니다.

## <a name="dataset"></a>데이터 세트

이 샘플에는 [NYC Yellow Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) 데이터 세트가 사용됩니다. [CSV 파일을 읽는](query-parquet-files.md) 것과 동일한 방법으로 Parquet 파일을 쿼리할 수 있습니다. 유일한 차이점은 `FILEFORMAT` 매개 변수를 `PARQUET`로 설정해야 한다는 점입니다. 이 문서의 예제에서는 Parquet 파일 읽기의 세부 정보를 보여줍니다.

## <a name="query-set-of-parquet-files"></a>Parquet 파일의 쿼리 집합

Parquet 파일을 쿼리할 때 관심 있는 열만 지정할 수 있습니다.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) WITH (
        tpepPickupDateTime DATETIME2,
        passengerCount INT
    ) AS nyc
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="automatic-schema-inference"></a>자동 스키마 유추

Parquet 파일을 읽을 때 OPENROWSET WITH 절을 사용할 필요가 없습니다. 열 이름 및 데이터 형식은 Parquet 파일에서 자동으로 읽습니다.

아래 샘플에서는 Parquet 파일에 대한 자동 스키마 유추 기능을 보여줍니다. 스키마를 지정하지 않고 2018년 9월의 행 수를 반환합니다.

> [!NOTE]
> Parquet 파일을 읽을 때 OPENROWSET WITH 절에 열을 지정할 필요가 없습니다. 이 경우 서버리스 SQL 풀 쿼리 서비스는 Parquet 파일의 메타데이터를 활용하여 열을 이름별로 바인딩합니다.

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=9/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>분할된 데이터 쿼리

이 샘플에 제공된 데이터 세트는 별도의 하위 폴더로 분할(파티션)됩니다. filepath 함수를 사용하여 특정 파티션을 대상으로 지정할 수 있습니다. 이 예제에서는 2017년 첫 3개월 동안의 년, 월 및 payment_type별 요금을 보여줍니다.

> [!NOTE]
> 서버리스 SQL 풀 쿼리는 Hive/Hadoop 파티션 구성표와 호환됩니다.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=*/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND tpepPickupDateTime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="type-mapping"></a>형식 매핑

Parquet 형식에서 SQL 네이티브 형식으로의 매핑에 대해서는 [Parquet에 대한 형식 매핑](develop-openrowset.md#type-mapping-for-parquet)을 확인합니다.

## <a name="next-steps"></a>다음 단계

다음 문서로 진행하여 [Parquet 중첩 형식을 쿼리](query-parquet-nested-types.md)하는 방법을 알아봅니다.
