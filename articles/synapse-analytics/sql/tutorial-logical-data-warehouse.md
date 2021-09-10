---
title: '자습서: 서버리스 SQL 풀을 사용하여 Logical Data Warehouse 구축'
description: 이 자습서에서는 서버리스 SQL 풀을 사용하여 Azure 데이터 원본에서 Logical Data Warehouse를 쉽게 만드는 방법을 보여 줍니다.
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 08/20/2021
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: de0374f2ea26e3fa1dc7d25e7c837187f8914918
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123254734"
---
# <a name="tutorial-create-logical-data-warehouse-with-serverless-sql-pool"></a>자습서: 서버리스 SQL 풀을 사용하여 Logical Data Warehouse 만들기

이 자습서에서는 Azure Storage 및 Azure Cosmos DB를 기반으로 LDW(Logical Data Warehouse)를 만드는 방법에 대해 알아봅니다.

LDW는 ADLS(Azure Data Lake Storage), Azure Cosmos DB 분석 스토리지 또는 Azure Blob Storage와 같은 Azure 데이터 원본을 기반으로 구축된 관계형 레이어입니다.

## <a name="create-an-ldw-database"></a>LDW 데이터베이스 만들기

외부 데이터 원본을 참조하는 외부 테이블 및 뷰를 저장할 사용자 지정 데이터베이스를 만들어야 합니다.

```sql
CREATE DATABASE Ldw
      COLLATE Latin1_General_100_BIN2_UTF8;
```

이 데이터 정렬은 Parquet 및 Cosmos DB를 읽는 동안 최적의 성능을 제공합니다. 데이터베이스 데이터 정렬을 지정하지 않으려면 열 정의에 이 데이터 정렬을 지정해야 합니다.

## <a name="configure-data-sources-and-formats"></a>데이터 원본 및 형식 구성

첫 번째 단계로, 데이터 원본을 구성하고 원격으로 저장된 데이터의 파일 형식을 지정해야 합니다.

### <a name="create-data-source"></a>데이터 원본 만들기

데이터 원본은 데이터가 배치되는 위치 및 데이터 원본에 인증하는 방법을 설명하는 연결 문자열 정보를 나타냅니다.

다음 예제에는 공용 [ECDC COVID 19 Azure 개방형 데이터 세트](../../open-datasets/dataset-ecdc-covid-cases.md)를 참조하는 데이터 원본 정의의 한 가지 예가 나와 있습니다.

```sql
CREATE EXTERNAL DATA SOURCE ecdc_cases WITH (
    LOCATION = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/'
);
```

데이터 원본 소유자가 Azure AD ID에 대한 호출자의 익명 액세스를 허용했거나 명시적 액세스 권한을 부여하면 호출자는 자격 증명 없이 데이터 원본에 액세스할 수 있습니다.

외부 데이터 원본의 데이터에 액세스하는 동안 사용되는 사용자 지정 자격 증명을 명시적으로 정의할 수 있습니다.
- Synapse 작업 영역의 [관리 ID](develop-storage-files-storage-access-control.md?tabs=managed-identity)
- Azure Storage의 [공유 액세스 서명](develop-storage-files-storage-access-control.md?tabs=shared-access-signature)
- Cosmos DB 분석 스토리지를 읽을 수 있는 읽기 전용 Cosmos DB 계정 키입니다.

필수 조건으로, 데이터베이스에 마스터 키를 만들어야 합니다.
```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Setup you password - you need to create master key only once';
```

다음 외부 데이터 원본에서 Synapse SQL 풀은 작업 영역의 관리 ID를 사용하여 스토리지의 데이터에 액세스해야 합니다.

```sql
CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE ecdc_cases WITH (
    LOCATION = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/',
    CREDENTIAL = WorkspaceIdentity
);
```

Cosmos DB 분석 스토리지에 액세스하려면 읽기 전용 Cosmos DB 계정 키를 포함하는 자격 증명을 정의해야 합니다.

```sql
CREATE DATABASE SCOPED CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
```

Synapse 관리자 역할이 있는 모든 사용자는 이러한 자격 증명을 사용하여 Azure Data Lake Storage 또는 Cosmos DB 분석 스토리지에 액세스할 수 있습니다. Synapse 관리자 역할이 없는 낮은 권한의 사용자에게 이러한 데이터베이스 범위 자격 증명을 참조할 수 있는 명시적 권한을 부여해야 합니다.

```sql
GRANT REFERENCES ON DATABASE SCOPED CREDENTIAL::WorkspaceIdentity TO <user>
GO
GRANT REFERENCES ON DATABASE SCOPED CREDENTIAL::MyCosmosDbAccountCredential TO <user>
GO
```

자세한 내용은 [DATABASE SCOPED CREDENTIAL 권한 부여](/sql/t-sql/statements/grant-database-scoped-credential-transact-sql) 페이지를 참조하세요.

### <a name="define-external-file-formats"></a>외부 파일 형식 정의

외부 파일 형식은 외부 데이터 원본에 저장된 파일의 구조를 정의합니다. Parquet 및 CSV 외부 파일 형식을 정의할 수 있습니다.

```sql
CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
GO
CREATE EXTERNAL FILE FORMAT CsvFormat WITH (  FORMAT_TYPE = DELIMITEDTEXT );
```

[이 문서](develop-tables-external-tables.md?tabs=native#syntax-for-create-external-file-format)에서 자세한 정보 찾기

## <a name="explore-your-data"></a>데이터 탐색

데이터 원본을 설정한 후 `OPENROWSET` 함수를 사용하여 데이터를 탐색할 수 있습니다. [OPENROWSET](develop-openrowset.md) 함수는 원격 데이터 원본의 콘텐츠(예: 파일)를 읽고 이 콘텐츠를 행 세트로 반환합니다.

```sql
select top 10  *
from openrowset(bulk 'latest/ecdc_cases.parquet',
                data_source = 'ecdc_cases',
                format='parquet') as a
```

`OPENROWSET` 함수는 외부 파일이나 컨테이너의 열에 대한 정보를 제공하고 외부 테이블 및 뷰의 스키마를 정의할 수 있도록 합니다.

## <a name="create-external-tables-on-azure-storage"></a>Azure Storage에 대한 외부 테이블 만들기

스키마를 검색한 후 외부 데이터 원본을 기반으로 외부 테이블 및 뷰를 만들 수 있습니다. 데이터베이스 스키마에 테이블과 뷰를 구성하는 것이 좋습니다. 다음 쿼리에서는 Azure Data Lake Storage에서 ECDC COVID 데이터 세트에 액세스하는 모든 개체를 배치하는 스키마를 만들 수 있습니다.

```sql
create schema ecdc_adls;
```

데이터베이스 스키마는 개체를 그룹화하고 스키마당 사용 권한을 정의하는 데 유용합니다. 

스키마를 정의한 후에는 파일을 참조하는 외부 테이블을 만들 수 있습니다.
다음 외부 테이블은 Azure Storage에 배치된 ECDC COVID parquet 파일을 참조합니다.

```sql
create external table ecdc_adls.cases (
    date_rep                   date,
    day                        smallint,
    month                      smallint,
    year                       smallint,
    cases                      smallint,
    deaths                     smallint,
    countries_and_territories  varchar(256),
    geo_id                     varchar(60),
    country_territory_code     varchar(16),
    pop_data_2018              int,
    continent_exp              varchar(32),
    load_date                  datetime2(7),
    iso_country                varchar(16)
) with (
    data_source= ecdc_cases,
    location = 'latest/ecdc_cases.parquet',
    file_format = ParquetFormat
);
```

쿼리 성능을 최적화하려면 문자열 및 숫자 열에 가능한 가장 작은 형식을 사용해야 합니다.

## <a name="create-views-on-azure-cosmos-db"></a>Azure Cosmos DB에서 뷰 만들기

외부 테이블에 대한 대안으로 외부 데이터를 기반으로 뷰를 만들 수 있습니다. 

이전 예제에 표시된 테이블과 마찬가지로, 뷰를 별도의 스키마에 배치해야 합니다.

```sql
create schema ecdc_cosmosdb;
```

이제 Cosmos DB 컨테이너를 참조하는 스키마에 뷰를 만들 수 있습니다.

```sql
CREATE OR ALTER VIEW ecdc_cosmosdb.Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) WITH
     ( date_rep varchar(20), 
       cases bigint,
       geo_id varchar(6) 
     ) as rows
```

성능을 최적화하려면 `WITH` 스키마 정의에 가능한 가장 작은 형식을 사용해야 합니다.

> [!NOTE]
> Azure Cosmos DB 계정 키를 별도의 자격 증명에 배치하고, `OPENROWSET` 함수에서 이 자격 증명을 참조해야 합니다.
> 계정 키를 뷰 정의에 저장하지 마세요.

## <a name="access-and-permissions"></a>액세스 및 사용 권한

마지막 단계로, LDW에 액세스하도록 해야 하는 데이터베이스 사용자를 만들고, 외부 테이블 및 뷰에서 데이터를 선택할 권한을 부여해야 합니다.
다음 스크립트에서는 Azure AD ID를 사용하여 인증할 새 사용자를 추가하는 방법을 확인할 수 있습니다.

```sql
CREATE USER [jovan@contoso.com] FROM EXTERNAL PROVIDER;
GO
```

Azure AD 보안 주체 대신, 로그인 이름 및 암호를 사용하여 인증을 받는 SQL 보안 주체를 만들 수 있습니다.

```sql
CREATE LOGIN [jovan] WITH PASSWORD = 'My Very strong Password ! 1234';
CREATE USER [jovan] FROM LOGIN [jovan];
```

두 경우 모두 사용자에게 권한을 할당할 수 있습니다.

```sql
DENY ADMINISTER DATABASE BULK OPERATIONS TO [jovan@contoso.com]
GO
GRANT SELECT ON SCHEMA::ecdc_adls TO [jovan@contoso.com]
GO
GRANT SELECT ON OBJECT::ecdc_cosmosDB.cases TO [jovan@contoso.com]
GO
GRANT REFERENCES ON DATABASE SCOPED CREDENTIAL::MyCosmosDbAccountCredential TO [jovan@contoso.com]
GO
```

보안 규칙은 보안 정책에 따라 달라집니다. 몇 가지 일반적인 지침은 다음과 같습니다.
- 새 사용자가 준비한 외부 테이블 및 뷰만 사용하여 데이터를 읽을 수 있도록 해야 하기 때문에 해당 사용자에 대한 `ADMINISTER DATABASE BULK OPERATIONS` 권한은 거부해야 합니다.
- 일부 사용자가 사용하도록 할 테이블에만 `SELECT` 권한을 제공해야 합니다.
- 뷰를 사용하여 데이터에 대한 액세스 권한을 제공하는 경우 외부 데이터 원본에 액세스하는 데 사용되는 자격 증명에 `REFERENCES` 권한을 부여해야 합니다.

이 사용자에게는 외부 데이터를 쿼리하는 데 필요한 최소한의 권한이 있습니다. 권한, 외부 테이블 및 뷰를 설정할 수 있는 고급 사용자를 만들려면 사용자에게 `CONTROL` 권한을 부여할 수 있습니다.

```sql
GRANT CONTROL TO [jovan@contoso.com]
```

### <a name="role-based-security"></a>역할 기반 보안

개별 사용자에게 사용 권한을 할당하는 대신, 역할 수준에서 사용자를 역할로 구성하고 권한을 관리하는 것이 좋습니다.
다음 코드 샘플에서는 코로나19 사례를 분석할 수 있는 사람을 나타내는 새 역할을 만들고 이 역할에 세 명의 사용자를 추가합니다.

```sql
CREATE ROLE CovidAnalyst;

ALTER ROLE CovidAnalyst ADD MEMBER [jovan@contoso.com];
ALTER ROLE CovidAnalyst ADD MEMBER [milan@contoso.com];
ALTER ROLE CovidAnalyst ADD MEMBER [petar@contoso.com];
```

그룹에 속한 모든 사용자에게 사용 권한을 할당할 수 있습니다.

```sql
GRANT SELECT ON SCHEMA::ecdc_cosmosdb TO [CovidAnalyst];
GO
DENY SELECT ON SCHEMA::ecdc_adls TO [CovidAnalyst];
GO
DENY ADMINISTER DATABASE BULK OPERATIONS TO [CovidAnalyst];
```

이 역할 기반 보안 액세스 제어는 보안 규칙의 관리를 간소화할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 서버리스 SQL 풀을 Power BI Desktop에 연결하고 보고서를 만드는 방법을 알아보려면 [서버리스 SQL 풀을 Power BI Desktop에 연결하고 보고서 만들기](tutorial-connect-power-bi-desktop.md)를 참조하세요.
- 서버리스 SQL 풀에서 외부 테이블을 사용하는 방법을 알아보려면 [Synapse SQL에서 외부 테이블 사용](develop-tables-external-tables.md?tabs=sql-pool)을 참조하세요.
