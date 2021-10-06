---
title: '빠른 시작: 서버리스 SQL 풀 사용'
description: 이 빠른 시작에서는 서버리스 SQL 풀을 사용하여 다양한 형식의 파일을 얼마나 쉽게 쿼리할 수 있는지 알아봅니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 8607355069bbae5983239ddbd3e8752143f31497
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129403210"
---
# <a name="quickstart-use-serverless-sql-pool"></a>빠른 시작: 서버리스 SQL 풀 사용

Synapse 서버리스 SQL 풀은 Azure Storage에 있는 파일에서 SQL 쿼리를 실행할 수 있는 서버리스 쿼리 서비스입니다. 이 빠른 시작에서는 서버리스 SQL 풀을 사용하여 다양한 형식의 파일을 쿼리하는 방법을 알아봅니다. 지원되는 형식은 [OPENROWSET](sql/develop-openrowset.md)에 나열되어 있습니다.

이 빠른 시작에서는 쿼리를 보여줍니다. CSV, Apache Parquet 및 JSON 파일.

## <a name="prerequisites"></a>필수 구성 요소

쿼리를 실행할 SQL 클라이언트를 선택합니다.

- [Azure Synapse Studio](./get-started-create-workspace.md)는 스토리지에서 파일을 검색하고 SQL 쿼리를 만드는 데 사용할 수 있는 웹 도구입니다.
- [Azure Data Studio](sql/get-started-azure-data-studio.md)는 주문형 데이터베이스에서 SQL 쿼리와 Notebook을 실행할 수 있는 클라이언트 도구입니다.
- [SQL Server Management Studio](sql/get-started-ssms.md)는 주문형 데이터베이스에서 SQL 쿼리를 실행할 수 있는 클라이언트 도구입니다.

이 빠른 시작의 매개 변수:

| 매개 변수                                 | 설명                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| 서버리스 SQL 풀 서비스 엔드포인트 주소    | 서버 이름으로 사용                                   |
| 서버리스 SQL 풀 서비스 엔드포인트 지역     | 샘플에 사용할 스토리지를 결정하는 데 사용 |
| 엔드포인트 액세스를 위한 사용자 이름 및 암호 | 엔드포인트 엑세스에 사용                               |
| 보기를 만드는 데 사용되는 데이터베이스         | 샘플에서 시작점으로 사용되는 데이터베이스       |

## <a name="first-time-setup"></a>처음 설정

샘플을 사용하기 전에 다음을 수행합니다.

- 보기에 대한 데이터베이스 만들기(보기를 사용하려는 경우)
- 서버리스 SQL 풀에서 스토리지의 파일에 액세스하는 데 사용할 자격 증명 만들기

### <a name="create-database"></a>데이터베이스 만들기

데모용 데이터베이스를 직접 만듭니다. 이 데이터베이스를 사용하여 보기 및 이 문서의 샘플 쿼리를 만듭니다.

> [!NOTE]
> 데이터베이스는 실제 데이터가 아닌 메타데이터를 보는 용도로만 사용됩니다.
>빠른 시작의 뒷부분에서 사용할 수 있도록 사용하는 데이터베이스 이름을 기록해 두세요.

다음 쿼리를 사용하여 `mydbname`을 원하는 이름으로 변경합니다.

```sql
CREATE DATABASE mydbname
```

### <a name="create-data-source"></a>데이터 원본 만들기

서버리스 SQL 풀을 사용하여 쿼리를 실행하려면 서버리스 SQL 풀이 스토리지의 파일에 액세스하는 데 사용할 수 있는 데이터 원본을 만듭니다.
다음 코드 조각을 실행하여 이 섹션의 샘플에 사용된 데이터 원본을 만듭니다.

```sql
-- create master key that will protect the credentials:
CREATE MASTER KEY ENCRYPTION BY PASSWORD = <enter very strong password here>

-- create credentials for containers in our demo storage account
CREATE DATABASE SCOPED CREDENTIAL sqlondemand
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO
CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

## <a name="query-csv-files"></a>CSV 파일 쿼리

다음 이미지는 쿼리할 파일의 미리 보기입니다.

![헤더가 없는 CSV 파일의 처음 10개 행, Windows 스타일 새 줄.](./sql/media/query-single-csv-file/population.png)

다음 쿼리는 Windows 스타일 새 행과 쉼표로 구분된 열을 사용하여 헤더 행 없는 CSV 파일을 읽는 방법을 보여 줍니다.

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'csv/population/*.csv',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT = 'CSV', PARSER_VERSION = '2.0'
  )
WITH
  (
      country_code VARCHAR (5)
    , country_name VARCHAR (100)
    , year smallint
    , population bigint
  ) AS r
WHERE
  country_name = 'Luxembourg' AND year = 2017
```

쿼리 컴파일 시간에 스키마를 지정할 수 있습니다.
더 많은 예제는 [CSV 파일 쿼리](sql/query-single-csv-file.md)를 참조하세요.

## <a name="query-parquet-files"></a>Parquet 파일 쿼리

다음 샘플에서는 Parquet 파일을 쿼리하기 위한 자동 스키마 유추 기능을 보여줍니다. 이 샘플에서는 스키마를 지정하지 않고 2017년 9월의 행 수를 반환합니다.

> [!NOTE]
> Parquet 파일을 읽을 때 `OPENROWSET WITH` 절에서 열을 지정할 필요가 없습니다. 이 경우 서버리스 SQL 풀은 Parquet 파일의 메타데이터를 활용하여 열을 이름별로 바인딩합니다.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'parquet/taxi/year=2017/month=9/*.parquet',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT='PARQUET'
  ) AS nyc
```

[parquet 파일 쿼리](sql/query-parquet-files.md)에 대해 자세히 알아보세요.

## <a name="query-json-files"></a>JSON 파일 쿼리

### <a name="json-sample-file"></a>JSON 샘플 파일

파일은 *json* 컨테이너, *books* 폴더에 저장되며, 다음과 같은 구조의 단일 서적 항목을 포함합니다.

```json
{  
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[  
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

### <a name="query-json-files"></a>JSON 파일 쿼리

다음 쿼리는 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true)를 사용하여 *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected articles* 라는 제목의 서적에서 스칼라 값(제목, 게시자)을 검색하는 방법을 보여 줍니다.

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title
  , JSON_VALUE(jsonContent, '$.publisher') as publisher
  , jsonContent
FROM OPENROWSET
  (
      BULK 'json/books/*.json',
      DATA_SOURCE = 'SqlOnDemandDemo'
    , FORMAT='CSV'
    , FIELDTERMINATOR ='0x0b'
    , FIELDQUOTE = '0x0b'
    , ROWTERMINATOR = '0x0b'
  )
WITH
  ( jsonContent varchar(8000) ) AS [r]
WHERE
  JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics'
```

> [!IMPORTANT]
> 전체 JSON 파일을 단일 행/열로 읽고 있습니다. 따라서 파일에서 찾을 수 없는 FIELDTERMINATOR, FIELDQUOTE 및 ROWTERMINATOR가 0x0b로 설정됩니다.

## <a name="next-steps"></a>다음 단계

이제 다음 문서를 진행할 수 있습니다.

- [단일 CSV 파일 쿼리](sql/query-single-csv-file.md)
- [폴더 및 여러 CSV 파일 쿼리](sql/query-folders-multiple-csv-files.md)
- [특정 파일 쿼리](sql/query-specific-files.md)
- [Parquet 파일 쿼리](sql/query-parquet-files.md)
- [Parquet 중첩 형식 쿼리](sql/query-parquet-nested-types.md)
- [JSON 파일 쿼리](sql/query-json-files.md)
- [보기 만들기 및 사용](sql/create-use-views.md)
- [외부 테이블 만들기 및 사용](sql/create-use-external-tables.md)
- [Azure 스토리지에 쿼리 결과 유지](sql/create-external-table-as-select.md)
- [단일 CSV 파일 쿼리](sql/query-single-csv-file.md)