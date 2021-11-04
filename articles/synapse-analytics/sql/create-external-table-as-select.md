---
title: 서버리스 SQL 풀에서 쿼리 결과 저장
description: 이 문서에서는 서버리스 SQL 풀을 사용하여 쿼리 결과를 스토리지에 저장하는 방법을 알아봅니다.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: ba93588dc686a57c05371b86d8f9e058f2bd2cf2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131460036"
---
# <a name="store-query-results-to-storage-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 서버리스 SQL 풀을 사용하여 스토리지에 쿼리 결과 저장

이 문서에서는 서버리스 SQL 풀을 사용하여 쿼리 결과를 스토리지에 저장하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

첫 번째 단계는 쿼리를 실행할 **데이터베이스를 만드는** 것입니다. 그런 다음, 해당 데이터베이스에서 [설치 스크립트](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)를 실행하여 개체를 초기화합니다. 이 설치 스크립트는 이러한 샘플의 데이터를 읽는 데 사용되는 데이터 원본, 데이터베이스 범위 자격 증명 및 외부 파일 형식을 만듭니다.

이 문서의 지침에 따라 출력 스토리지에 데이터를 쓰는 데 사용되는 데이터 원본, 데이터베이스 범위 자격 증명 및 외부 파일 형식을 만듭니다.

## <a name="create-external-table-as-select"></a>선택 항목으로 외부 테이블 만들기

CREATE EXTERNAL TABLE AS SELECT(CETAS) 문을 사용하여 스토리지에 쿼리 결과를 저장할 수 있습니다.

> [!NOTE]
> 쿼리의 첫 번째 줄(예: [mydbname])을 변경하여 사용자가 만든 데이터베이스를 사용합니다.

```sql
USE [mydbname];
GO

CREATE DATABASE SCOPED CREDENTIAL [SasTokenWrite]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://<storage account name>.blob.core.windows.net/csv', CREDENTIAL = [SasTokenWrite]
);
GO

CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

CREATE EXTERNAL TABLE [dbo].[PopulationCETAS] WITH (
        LOCATION = 'populationParquet/',
        DATA_SOURCE = [MyDataSource],
        FILE_FORMAT = [ParquetFF]
) AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

> [!NOTE]
> 이 스크립트를 수정하고 대상 위치를 변경하여 다시 실행해야 합니다. 이미 일부 데이터가 있는 위치에는 외부 테이블을 만들 수 없습니다.

## <a name="use-the-external-table"></a>외부 테이블 사용

CETAS를 통해 생성된 외부 테이블을 일반 외부 테이블처럼 사용할 수 있습니다.

> [!NOTE]
> 쿼리의 첫 번째 줄(예: [mydbname])을 변경하여 사용자가 만든 데이터베이스를 사용합니다.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM PopulationCETAS
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="remarks"></a>설명

결과를 저장한 후에는 외부 테이블의 데이터를 수정할 수 없습니다. CETAS는 이전 실행에서 만든 기본 데이터를 덮어쓰지 않으므로 이 스크립트를 반복할 수 없습니다. 시나리오에서 이러한 항목 중 일부가 필요한 경우 다음 피드백 항목에 투표하거나 Azure 피드백 사이트에서 새 항목을 제안하세요.
- [외부 테이블에 새 데이터 삽입 사용](https://feedback.azure.com/d365community/forum/9b9ba8e4-0825-ec11-b6e6-000d3a4f07b8)
- [외부 테이블에서 데이터 삭제 사용](https://feedback.azure.com/d365community/idea/fb5a00c9-0a25-ec11-b6e6-000d3a4f07b8)
- [CETAS에서 파티션 지정](https://feedback.azure.com/d365community/idea/e28278db-0a25-ec11-b6e6-000d3a4f07b8)
- [파일 크기 및 개수 지정](https://feedback.azure.com/d365community/idea/262048b9-0925-ec11-b6e6-000d3a4f07b8)

유일하게 지원되는 출력 형식은 Parquet 및 CSV입니다. [Azure 피드백 사이트](https://feedback.azure.com/d365community/forum/9b9ba8e4-0825-ec11-b6e6-000d3a4f07b8)에서 다른 유형에 투표할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다른 파일 형식을 쿼리하는 방법에 대한 자세한 내용은 [단일 CSV 파일 쿼리](query-single-csv-file.md), [Parquet 파일 쿼리](query-parquet-files.md) 및 [JSON 파일 쿼리](query-json-files.md) 문서를 참조하세요.
