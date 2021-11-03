---
title: 서버리스 SQL 풀을 사용하여 폴더 및 여러 파일 쿼리
description: 서버리스 SQL 풀은 Windows OS에서 사용되는 와일드카드와 유사한 와일드카드를 사용하여 여러 파일/폴더 읽기를 지원합니다.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: ignite-fall-2021
ms.openlocfilehash: 960c13baea77fc8a6b900a5e68828af0377b0087
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018605"
---
# <a name="query-folders-and-multiple-files"></a>폴더 및 여러 파일 쿼리  

이 문서에서는 Azure Synapse Analytics에서 서버리스 SQL 풀을 사용하여 쿼리를 작성하는 방법을 알아봅니다.

서버리스 SQL 풀은 Windows OS에서 사용되는 와일드카드와 유사한 와일드카드를 사용하여 여러 파일/폴더 읽기를 지원합니다. 여러 와일드카드를 사용할 수 있으므로 유연성이 더 뛰어납니다.

## <a name="prerequisites"></a>사전 요구 사항

첫 번째 단계는 쿼리를 실행할 **데이터베이스를 만드는** 것입니다. 그런 다음, 해당 데이터베이스에서 [설치 스크립트](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)를 실행하여 개체를 초기화합니다. 이 설치 스크립트는 이러한 샘플에서 사용되는 데이터 원본, 데이터베이스 범위 자격 증명 및 외부 파일 형식을 만듭니다.

*csv/taxi* 폴더를 사용하여 샘플 쿼리를 따릅니다. 2016년 7월부터 2018년 6월까지 NYC Taxi - Yellow Taxi Trip Records 데이터가 포함됩니다. *csv/taxi* 의 파일은 yellow_tripdata_\<year>-\<month>.csv 패턴을 사용하여 연월에 따라 이름이 지정됩니다.

## <a name="read-all-files-in-folder"></a>폴더의 모든 파일 읽기

아래 예제는 *csv/taxi* 폴더의 모든 NYC Yellow Taxi 데이터 파일을 읽고 연간 총 승객 및 탑승 수를 반환합니다. 또한 집계 함수의 사용을 보여 줍니다.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        pickup_datetime DATETIME2 2, 
        passenger_count INT 4
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 단일 OPENROWSET를 통해 액세스되는 모든 파일은 동일한 구조체(즉, 열 개수 및 데이터 형식)를 갖추고 있어야 합니다.

### <a name="read-subset-of-files-in-folder"></a>폴더에 있는 파일의 하위 집합 읽기

아래 예제에서는 와일드카드를 사용하여 *csv/taxi* 폴더에서 2017 NYC Yellow Taxi 데이터 파일을 읽고 지급 유형별 총 요금을 반환합니다.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        payment_type INT 10,
        fare_amount FLOAT 11
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> 단일 OPENROWSET를 통해 액세스되는 모든 파일은 동일한 구조체(즉, 열 개수 및 데이터 형식)를 갖추고 있어야 합니다.

### <a name="read-subset-of-files-in-folder-using-multiple-file-paths"></a>여러 파일 경로를 사용하여 폴더의 파일 하위 집합 읽기

아래 예제는 csv/taxi 폴더에서 2개의 파일 경로를 사용하여 *csv/taxi* 폴더에서 2017 NYC Yellow Taxi 데이터 파일을 읽습니다. 첫 번째 경로는 1월 및 12월의 데이터를 포함하는 파일의 전체 경로와 결제 유형당 총 요금 금액을 반환하는 11월과 12월의 와일드카드를 포함합니다.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
        BULK (
            'csv/taxi/yellow_tripdata_2017-01.csv',
            'csv/taxi/yellow_tripdata_2017-1*.csv'
        ),
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        payment_type INT 10,
        fare_amount FLOAT 11
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> 단일 OPENROWSET를 통해 액세스되는 모든 파일은 동일한 구조체(즉, 열 개수 및 데이터 형식)를 갖추고 있어야 합니다.

## <a name="read-folders"></a>폴더 읽기

OPENROWSET에 제공하는 경로는 폴더에 대한 경로일 수도 있습니다. 다음 섹션에는 해당 쿼리 형식이 포함되어 있습니다.

### <a name="read-all-files-from-specific-folder"></a>특정 폴더의 모든 파일 읽기

[폴더의 모든 파일 읽기](#read-all-files-in-folder)에 표시된 것처럼 파일 수준 와일드카드를 사용하여 폴더의 모든 파일을 읽을 수 있습니다. 그러나 폴더를 쿼리하고 해당 폴더 내의 모든 파일을 이용하는 방법이 있습니다.

OPENROWSET에서 제공된 경로가 폴더를 가리키는 경우 해당 폴더의 모든 파일이 쿼리의 원본으로 사용됩니다. 다음 쿼리는 *csv/taxi* 폴더에 있는 모든 파일을 읽습니다.

> [!NOTE]
> 아래 쿼리의 경로 끝에 /가 있는지 확인합니다. 폴더를 나타냅니다. 만약 /이 누락되었다면 쿼리는 이를 대신해 *taxi* 라는 파일을 대상으로 합니다.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 단일 OPENROWSET를 통해 액세스되는 모든 파일은 동일한 구조체(즉, 열 개수 및 데이터 형식)를 갖추고 있어야 합니다.

### <a name="read-all-files-from-multiple-folders"></a>여러 폴더의 모든 파일 읽기

와일드카드를 사용하여 여러 폴더의 파일을 읽을 수 있습니다. 다음 쿼리는 *csv* 폴더에 있는 모든 폴더에서 이름이 *t* 로 시작하고 *i* 로 끝나는 파일을 읽습니다.

> [!NOTE]
> 아래 쿼리의 경로 끝에 /가 있는지 확인합니다. 폴더를 나타냅니다. 만약 /이 생략된 경우 쿼리는 대신 이름이 *t&ast;i* 인 파일을 대상으로 합니다.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 단일 OPENROWSET를 통해 액세스되는 모든 파일은 동일한 구조체(즉, 열 개수 및 데이터 형식)를 갖추고 있어야 합니다.

조건과 일치하는 폴더가 하나만 있으므로 쿼리 결과는 [폴더의 모든 파일 읽기](#read-all-files-in-folder)와 동일합니다.

## <a name="traverse-folders-recursively"></a>재귀적으로 폴더 트래버스

경로 끝에 /**를 지정하면 서버리스 SQL 풀이 폴더를 재귀적으로 트래버스할 수 있습니다. 다음 쿼리는 *csv* 폴더에 있는 모든 폴더 및 하위 폴더의 모든 파일을 읽습니다.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/**', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 단일 OPENROWSET를 통해 액세스되는 모든 파일은 동일한 구조체(즉, 열 개수 및 데이터 형식)를 갖추고 있어야 합니다.

## <a name="multiple-wildcards"></a>여러 와일드카드

여러 경로 수준에서 다양한 와일드카드를 사용할 수 있습니다. 예를 들어 이전 쿼리를 보강하여 이름이 *t* 로 시작하고 *i* 로 끝나는 모든 폴더에서 2017년 데이터만 있는 파일을 읽을 수 있습니다.

> [!NOTE]
> 아래 쿼리의 경로 끝에 /가 있는지 확인합니다. 폴더를 나타냅니다. 만약 /이 생략된 경우 쿼리는 대신 이름이 *t&ast;i* 인 파일을 대상으로 합니다.
> 쿼리당 최대 10개의 와일드카드 제한이 있습니다.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 단일 OPENROWSET를 통해 액세스되는 모든 파일은 동일한 구조체(즉, 열 개수 및 데이터 형식)를 갖추고 있어야 합니다.

조건과 일치하는 폴더가 하나만 있기 때문에 쿼리 결과는 [폴더의 파일 하위 집합 읽기](#read-subset-of-files-in-folder) 및 [특정 폴더에서 모든 파일 읽기](#read-all-files-from-specific-folder)와 동일합니다. 더 복잡한 와일드카드 사용 시나리오는 [Parquet 파일 쿼리](query-parquet-files.md)에서 다룹니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [특정 파일 쿼리](query-specific-files.md) 문서에서 찾을 수 있습니다.
