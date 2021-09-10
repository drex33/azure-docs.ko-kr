---
title: Azure Synapse Link에서 서버리스 SQL 풀을 사용하여 Azure Cosmos DB 데이터를 쿼리
description: 이 문서에서는 Azure Synapse Link에서 서버리스 SQL 풀을 사용하여 Azure Cosmos DB를 쿼리하는 방법을 알아봅니다.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 03/02/2021
ms.author: jovanpop
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: a0f3e5f707600933ce68e51634145cd3515c5d6a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122530103"
---
# <a name="query-azure-cosmos-db-data-with-a-serverless-sql-pool-in-azure-synapse-link"></a>Azure Synapse Link에서 서버리스 SQL 풀을 사용하여 Azure Cosmos DB 데이터를 쿼리

서버리스 SQL 풀을 사용하면 트랜잭션 워크로드의 성능에 영향을 주지 않고 [Azure Synapse Link](../../cosmos-db/synapse-link.md)를 통해 설정된 Azure Cosmos DB 컨테이너의 데이터를 거의 실시간으로 분석할 수 있습니다. [분석 저장소](../../cosmos-db/analytical-store-introduction.md)에서 데이터를 쿼리하는 친숙한 T-SQL 구문을 제공하고 T-SQL 인터페이스를 통해 광범위한 BI(비즈니스 인텔리전스) 및 임시 쿼리 도구에 대한 통합 연결을 제공합니다.

Azure Cosmos DB 쿼리를 위해 전체 [SELECT](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) 노출 영역은 대부분의 [SQL 함수 및 연산자](overview-features.md)를 포함하는 [OPENROWSET](develop-openrowset.md) 함수를 통해 지원됩니다. CETAS([Create External Table As Select](develop-tables-cetas.md#cetas-in-serverless-sql-pool))를 사용하여 Azure Blob Storage 또는 Azure Data Lake Storage의 데이터와 함께 Azure Cosmos DB에서 데이터를 읽는 쿼리 결과를 저장할 수도 있습니다. 현재는 CETAS를 사용하여 Azure Cosmos DB에 서버리스 SQL 풀 쿼리 결과를 저장할 수 없습니다.

이 문서에서는 Azure Synapse Link를 사용하여 사용하도록 설정된 Azure Cosmos DB 컨테이너에서 데이터를 쿼리하는 서버리스 SQL 풀로 쿼리를 작성하는 방법을 알아봅니다. 그런 다음, [이 자습서](./tutorial-data-analyst.md)에서 Azure Cosmos DB 컨테이너를 통해 서버리스 SQL 풀 뷰를 빌드하고 Power BI 모델에 연결하는 방법에 대해 자세히 알아볼 수 있습니다. 이 자습서에서는 [Azure Cosmos DB가 잘 정의된 스키마](../../cosmos-db/analytical-store-introduction.md#schema-representation)가 있는 컨테이너를 사용합니다. [Azure Synapse Analytics용 SQL 서버리스를 사용하여 Azure Cosmos DB를 쿼리](/learn/modules/query-azure-cosmos-db-with-sql-serverless-for-azure-synapse-analytics/)하는 방법에 대한 학습 모듈을 확인할 수도 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 분석 저장소를 준비했는지 확인합니다.
  - [Cosmos DB 컨테이너](../quickstart-connect-synapse-link-cosmos-db.md#enable-azure-cosmos-db-analytical-store)에서 분석 저장소를 사용하도록 설정합니다.
  - 분석 저장소를 쿼리하는 데 사용할 읽기 전용 키로 연결 문자열을 가져옵니다. 
  - [Cosmos DB 컨테이너에 액세스하는 데 사용되는 읽기 전용 키](../../cosmos-db/database-security.md#primary-keys)를 가져옵니다.
- 다음과 같은 [모범 사례](best-practices-serverless-sql-pool.md)를 모두 적용했는지 확인합니다.
  - Cosmos DB 분석 저장소가 서버리스 SQL 풀과 동일한 지역에 있는지 확인합니다.
  - 클라이언트 애플리케이션(Power BI, Analysis Services)이 서버리스 SQL 풀과 동일한 지역에 있는지 확인합니다.
  - 많은 양의 데이터(80GB보다 큼)를 반환하는 경우 Analysis Services와 같은 캐싱 계층을 사용하여 Analysis Services 모델에서 80GB보다 작은 파티션을 로드하는 것이 좋습니다.
  - 문자열 열을 사용하여 데이터를 필터링하는 경우 최대한 가장 작은 형식의 명시적 `WITH` 절과 함께 `OPENROWSET` 함수를 사용하고 있는지 확인합니다. 예를 들어 속성에 최대 5자가 있다는 것을 알고 있는 경우 VARCHAR(1000)을 사용하지 마세요.

## <a name="overview"></a>개요

서버리스 SQL 풀을 사용하면 `OPENROWSET` 함수를 사용하여 Azure Cosmos DB 분석 스토리지를 쿼리할 수 있습니다. 
- `OPENROWSET`에 인라인 키를 사용합니다. 이 구문을 사용하여 자격 증명을 준비할 필요없이 Azure Cosmos DB 컬렉션을 쿼리할 수 있습니다.
- `OPENROWSET`에 Cosmos DB 계정 키를 포함하는 참조된 자격 증명을 사용합니다. 이 구문을 사용하여 Azure Cosmos DB 컬렉션에 대한 뷰를 만들 수 있습니다.

### <a name="openrowset-with-key"></a>[키를 사용하는 OPENROWSET](#tab/openrowset-key)

Azure Cosmos DB 분석 저장소에서 데이터 쿼리 및 분석을 지원하기 위해 서버리스 SQL 풀이 사용됩니다. 서버리스 SQL 풀은 `OPENROWSET` SQL 구문을 사용하므로 먼저 Azure Cosmos DB 연결 문자열을 다음 형식으로 변환해야 합니다.

```sql
OPENROWSET( 
       'CosmosDB',
       '<SQL connection string for Azure Cosmos DB>',
       <Container name>
    )  [ < with clause > ] AS alias
```

Azure Cosmos DB에 대한 SQL 연결 문자열에는 Azure Cosmos DB 계정 이름, 데이터베이스 이름, 데이터베이스 계정 마스터 키, `OPENROWSET` 함수에 대한 선택적 지역 이름이 지정됩니다. 이 정보 중 일부는 표준 Azure Cosmos DB 연결 문자열에서 가져올 수 있습니다.

표준 Azure Cosmos DB 연결 문자열 형식에서 변환:

```
AccountEndpoint=https://<database account name>.documents.azure.com:443/;AccountKey=<database account master key>;
```

SQL 연결 문자열은 다음과 같은 형식입니다.

```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

지역은 선택 사항입니다. 생략한 경우 컨테이너의 주 지역이 사용됩니다.

`OPENROWSET` 구문에서 따옴표 없이 Azure Cosmos DB 컨테이너 이름을 지정합니다. 컨테이너 이름에 특수 문자(예: 대시(-))가 있는 경우 이 이름은 `OPENROWSET` 구문에서 대괄호(`[]`) 안에 래핑됩니다.

### <a name="openrowset-with-credential"></a>[자격 증명을 사용하는 OPENROWSET](#tab/openrowset-credential)

자격 증명을 참조하는 `OPENROWSET` 구문을 사용할 수 있습니다.

```sql
OPENROWSET( 
       PROVIDER = 'CosmosDB',
       CONNECTION = '<SQL connection string for Azure Cosmos DB without account key>',
       OBJECT = '<Container name>',
       [ CREDENTIAL | SERVER_CREDENTIAL ] = '<credential name>'
    )  [ < with clause > ] AS alias
```

이 경우 Azure Cosmos DB에 대한 SQL 연결 문자열에는 키가 포함되지 않습니다. 연결 문자열은 다음과 같은 형식입니다.

```sql
'account=<database account name>;database=<database name>;region=<region name>'
```

데이터베이스 계정 마스터 키가 서버 수준 자격 증명 또는 데이터베이스 범위 자격 증명에 배치됩니다. 

---

> [!IMPORTANT]
> Azure Cosmos DB 분석 저장소의 문자열 값은 UTF-8 텍스트로 인코딩되기 때문에 일부 UTF-8 데이터베이스 데이터 정렬(예: `Latin1_General_100_CI_AS_SC_UTF8`)을 사용하고 있는지 확인합니다.
> 파일 및 데이터 정렬에서 텍스트 인코딩이 일치하지 않으면 예기치 않은 텍스트 변환 오류가 발생할 수 있습니다.
> T-SQL 문 `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`을 사용하여 현재 데이터베이스의 기본 데이터 정렬을 쉽게 변경할 수 있습니다.

> [!NOTE]
> 서버리스 SQL 풀은 Azure Cosmos DB 트랜잭션 저장소 쿼리를 지원하지 않습니다.

## <a name="sample-dataset"></a>샘플 데이터 세트

이 문서의 예는 [유럽 질병 방지 및 제어(ECDC)센터 COVID-19사례](../../open-datasets/dataset-ecdc-covid-cases.md)와 [CORD-19(COVID-19 Open Research Dataset), doi:10.5281/zenodo.3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)의 데이터를 기반으로 합니다.

이러한 페이지에서 데이터의 라이선스 및 구조를 확인할 수 있습니다. [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) 및 [CORD-19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) 데이터 세트에 대한 샘플 데이터를 다운로드할 수도 있습니다.

이 문서의 내용을 따르려면 서버리스 SQL 풀을 사용하여 Azure Cosmos DB 데이터를 쿼리하는 방법을 보여주는 다음 리소스를 만들어야 합니다.

* [Azure Synapse Link를 사용](../../cosmos-db/configure-synapse-link.md)하는 Azure Cosmos DB 데이터베이스 계정.
* 이름이 `covid`인 Azure Cosmos DB 데이터베이스.
* 앞의 샘플 데이터 세트가 로드되었으며 이름이 `Ecdc` 및 `Cord19`로 지정된 Azure Cosmos DB 컨테이너.

테스트 목적으로 다음 연결 문자열을 사용할 수 있습니다. `Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==` 이 계정은 Synapse SQL 엔드포인트보다 먼 지역에 있을 수 있으므로 이 연결은 성능을 보장하지 않습니다.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>자동 스키마 유추를 사용하여 Azure Cosmos DB 데이터 탐색

Azure Cosmos DB에서 데이터를 탐색하는 가장 쉬운 방법은 자동 스키마 유추 기능을 사용하는 것입니다. `OPENROWSET` 문에서 `WITH` 절을 생략하면 서버리스 SQL 풀에서 Azure Cosmos DB 컨테이너의 분석 저장 스키마를 자동으로 검색(유추)하도록 지시할 수 있습니다.

### <a name="openrowset-with-key"></a>[키를 사용하는 OPENROWSET](#tab/openrowset-key)

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc) as documents
```

### <a name="openrowset-with-credential"></a>[자격 증명을 사용하는 OPENROWSET](#tab/openrowset-credential)

```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

---

앞의 예제에서는 서버리스 SQL 풀에서 Azure Cosmos DB 키(위 예제의 더미)를 사용하여 인증된 Azure Cosmos DB 계정 `MyCosmosDbAccount`의 `covid` 데이터베이스에 연결하도록 지시했습니다. 그런 다음 해당 `West US 2` 지역의 `Ecdc` 컨테이너 분석 저장소에 액세스합니다. 특정 속성의 프로젝션이 없으므로 `OPENROWSET` 함수는 Azure Cosmos DB 항목의 모든 속성을 반환합니다.

Azure Cosmos DB 컨테이너의 항목에 `date_rep`, `cases`, `geo_id` 속성이 있다고 가정하면 이 쿼리의 결과는 다음 표와 같습니다.

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

동일한 Azure Cosmos DB 데이터베이스의 다른 컨테이너에서 데이터를 탐색해야 하는 경우 동일한 연결 문자열을 사용하고 세 번째 매개 변수로 필요한 컨테이너를 참조할 수 있습니다.

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>스키마를 명시적으로 지정

`OPENROWSET`의 자동 스키마 유추 기능은 간단하고 사용하기 쉬운 쿼리를 제공하지만, 비즈니스 시나리오에는 Azure Cosmos DB 데이터의 관련 속성을 읽기 전용으로 지정하도록 스키마를 명시적으로 지정해야 할 수 있습니다.

`OPENROWSET` 함수를 사용하면 컨테이너의 데이터에서 읽을 속성을 명시적으로 지정하고 해당 데이터 형식을 지정할 수 있습니다.

다음 구조를 포함하는 [ECDC COVID 데이터 세트](../../open-datasets/dataset-ecdc-covid-cases.md)의 일부 데이터를 Azure Cosmos DB으로 가져온다고 가정하겠습니다.

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Azure Cosmos DB의 이러한 플랫 JSON 문서는 Synapse SQL의 행 및 열 집합으로 표시할 수 있습니다. `OPENROWSET` 함수를 사용하면 `WITH` 절에서 읽을 속성의 하위 집합 및 정확한 열 형식을 지정할 수 있습니다.

### <a name="openrowset-with-key"></a>[키를 사용하는 OPENROWSET](#tab/openrowset-key)

```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

### <a name="openrowset-with-credential"></a>[자격 증명을 사용하는 OPENROWSET](#tab/openrowset-credential)

```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
   
```

---
이 쿼리의 결과는 다음 표와 같습니다.

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Azure Cosmos DB 값에 사용해야 하는 SQL 형식에 대한 자세한 내용은 문서 끝 부분의 [SQL 형식 매핑 규칙](#azure-cosmos-db-to-sql-type-mappings)을 참조하세요.

## <a name="create-view"></a>뷰 만들기

Master 또는 기본 데이터베이스에 보기를 만드는 것은 권장되거나 지원되지 않습니다. 따라서 보기에 대한 사용자 데이터베이스를 만들어야 합니다.

스키마를 확인한 후 Azure Cosmos DB 데이터를 기반으로 보기를 준비할 수 있습니다. Azure Cosmos DB 계정 키를 별도의 자격 증명에 넣고 `OPENROWSET` 함수에서 이 자격 증명을 참조해야 합니다. 계정 키를 보기 정의에 보관하지 마십시오.

```sql
CREATE CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
GO
CREATE OR ALTER VIEW Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

성능에 영향을 줄 수 있으므로 명시적으로 정의하지 않은 스키마 없이 `OPENROWSET`을 사용하지 마십시오. 열에 사용할 수 있는 최소 크기를 사용해야 합니다(예: 기본 VARCHAR(8000) 대신 VARCHAR(100)). [UTF-8 변환 문제](../troubleshoot/reading-utf8-text.md)를 방지하려면 UTF-8 데이터 정렬을 기본 데이터베이스 데이터 정렬로 사용하거나 명시적 열 데이터 정렬로 설정해야 합니다. 데이터 정렬 `Latin1_General_100_BIN2_UTF8`은 yu에서 일부 문자열 열을 사용하여 데이터를 필터링할 때 최상의 성능을 제공합니다.

## <a name="query-nested-objects-and-arrays"></a>중첩된 개체 및 배열 쿼리

Azure Cosmos DB를 사용하면 중첩된 개체나 배열로 작성하여 더 복잡한 데이터 모델을 나타낼 수 있습니다. Azure Cosmos DB에 대한 Azure Synapse 링크의 자동 동기화 기능은 서버리스 SQL 풀에서 다양한 쿼리를 허용하는 중첩된 데이터 형식 처리를 포함하여 분석 저장소에서 스키마 표현을 관리합니다.

예를 들어, [CORD-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) 데이터 세트는 이 구조를 따르는 JSON 문서를 포함합니다.

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

Azure Cosmos DB의 중첩된 개체와 배열은 `OPENROWSET` 함수에서 이를 읽을 때 쿼리 결과에 JSON 문자열로 표시됩니다. `WITH` 절을 사용하는 경우 개체에서 중첩된 값의 경로를 지정할 수 있습니다.

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19)
WITH (  paper_id    varchar(8000),
        title        varchar(1000) '$.metadata.title',
        metadata     varchar(max),
        authors      varchar(max) '$.metadata.authors'
) AS docs;
```

이 쿼리의 결과는 다음 표와 같습니다.

| paper_id | title | metadata | authors |
| --- | --- | --- | --- |
| bb11206963e831f… | eco-epidemi에 대한 보충 정보… | `{"title":"Supplementary Informati…` | `[{"first":"Julien","last":"Mélade","suffix":"","af…`| 
| bb1206963e831f1… | Immune-E에서 Convalescent Sera의 사용… | `{"title":"The Use of Convalescent…` | `[{"first":"Antonio","last":"Lavazza","suffix":"", …` |
| bb378eca9aac649… | Tylosema esculentum(Marama) Tuber 및 B… | `{"title":"Tylosema esculentum (Ma…` | `[{"first":"Walter","last":"Chingwaru","suffix":"",…` | 

[Azure Synapse 링크 복합 데이터 형식](../how-to-analyze-complex-schema.md)과 [SQL 풀의 중첩된 구조](query-parquet-nested-types.md)를 분석하는 방법에 대해 자세히 알아보세요.

> [!IMPORTANT]
> `Mélade` 대신 `MÃƒÂ©lade`과 같은 텍스트에서 예기치 않은 문자가 표시되는 경우 데이터베이스 데이터 정렬은 [UTF-8](/sql/relational-databases/collations/collation-and-unicode-support#utf8) 데이터 정렬로 설정되지 않습니다.
> `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8`과 같은 SQL 문을 사용하여 [데이터베이스의 데이터 정렬을 UTF-8 데이터 정렬로 변경](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation)합니다.

## <a name="flatten-nested-arrays"></a>중첩된 배열 평면화

Azure Cosmos DB 데이터에는 [CORD-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) 데이터 세트에서 저자의 배열과 같이 중첩된 하위 배열 있을 수 있습니다.

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

경우에 따라 최상위 항목(메타데이터)의 속성을 배열(작성자)의 모든 요소와 "연결"해야 할 수도 있습니다. 서버리스 SQL 풀을 사용하면 중첩된 배열에 `OPENJSON` 함수를 적용하여 중첩된 구조를 평면화할 수 있습니다.

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

이 쿼리의 결과는 다음 표와 같습니다.

| title | authors | first | last | 소속 |
| --- | --- | --- | --- | --- |
| eco-epidemi에 대한 보충 정보… |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
eco-epidemi에 대한 보충 정보… | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 4# |`{"laboratory":"","institution":"U…` | 
| eco-epidemi에 대한 보충 정보… |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| eco-epidemi에 대한 보충 정보… |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> `Mélade` 대신 `MÃƒÂ©lade`과 같은 텍스트에서 예기치 않은 문자가 표시되는 경우 데이터베이스 데이터 정렬은 [UTF-8](/sql/relational-databases/collations/collation-and-unicode-support#utf8) 데이터 정렬로 설정되지 않습니다. `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8`과 같은 SQL 문을 사용하여 [데이터베이스의 데이터 정렬을 UTF-8 데이터 정렬로 변경](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation)합니다.

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Azure Cosmos DB를 SQL 형식으로 매핑

Azure Cosmos DB 트랜잭션 저장소는 스키마에 구애받지 않지만 분석 저장소는 분석 쿼리 성능을 최적화하기 위해 스키마화됩니다. Azure Cosmos DB에 대한 Azure Synapse 링크의 자동 동기화 기능은 중첩된 데이터 형식 처리를 포함하여 분석 저장소에서 스키마 표현을 관리합니다. 서버리스 SQL 풀은 분석 저장소를 쿼리하므로 Azure Cosmos DB 입력 데이터 형식을 SQL 데이터 형식에 매핑하는 방법을 이해하는 것이 중요합니다.

SQL(Core) API의 Azure Cosmos DB 계정은 숫자, 문자열, 부울, Null, 중첩된 개체 또는 배열의 JSON 속성 유형을 지원합니다. `OPENROWSET`에서 `WITH` 절을 사용하는 경우 이러한 JSON 유형과 일치하는 SQL 유형을 선택해야 합니다. 다음 표에서는 Azure Cosmos DB의 다양한 속성 유형에 사용해야 하는 SQL 열 유형을 보여 줍니다.

| Azure Cosmos DB 속성 유형 | SQL 열 형식 |
| --- | --- |
| 부울 | bit |
| 정수 | bigint |
| Decimal | float |
| String | varchar(UTF-8 데이터베이스 데이터 정렬) |
| 날짜 시간(ISO 형식 문자열) | varchar(30) |
| 날짜 시간(UNIX 타임스탬프) | bigint |
| Null | `any SQL type` 
| 개체 또는 배열 | varchar(max)(UTF-8 데이터베이스 데이터 정렬), JSON 텍스트로 직렬화됨 |

## <a name="full-fidelity-schema"></a>전체 충실도 스키마

Azure Cosmos DB 전체 충실도 스키마는 컨테이너의 모든 속성에 대해 값과 가장 일치하는 항목을 모두 기록합니다. 전체 충실도 스키마를 사용하는 컨테이너의 `OPENROWSET` 함수는 각 셀의 형식과 실제 값을 모두 제공합니다. 다음 쿼리가 전체 충실도 스키마를 사용하여 컨테이너에서 항목을 읽는다고 가정하겠습니다.

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) as rows
```

이 쿼리의 결과는 JSON 텍스트로 형식이 지정된 형식과 값을 반환합니다.

| date_rep | cases | geo_id |
| --- | --- | --- |
| {"date":"2020-08-13"} | {"int32":"254"} | {"string":"RS"} |
| {"date":"2020-08-12"} | {"int32":"235"}| {"string":"RS"} |
| {"date":"2020-08-11"} | {"int32":"316"} | {"string":"RS"} |
| {"date":"2020-08-10"} | {"int32":"281"} | {"string":"RS"} |
| {"date":"2020-08-09"} | {"int32":"295"} | {"string":"RS"} |
| {"string":"2020/08/08"} | {"int32":"312"} | {"string":"RS"} |
| {"date":"2020-08-07"} | {"float64":"339.0"} | {"string":"RS"} |

모든 값에 대해 Azure Cosmos DB 컨테이너 항목에서 식별된 형식을 확인할 수 있습니다. `date_rep` 속성 값의 대부분은 `date` 값을 포함하지만 이러한 값 중 일부는 Azure Cosmos DB의 문자열로 잘못 저장됩니다. 전체 충실도 스키마는 올바르게 형식화된 `date` 값과 잘못된 형식의 `string` 값을 모두 반환합니다.
사례 수는 `int32` 값으로 저장된 정보이지만 10진수로 입력된 값이 하나 있습니다. 값은 `float64` 형식입니다. 가장 큰 `int32` 값을 초과하는 값이 있는 경우 해당 값은 `int64` 형식으로 저장됩니다. 이 예제의 모든 `geo_id` 값은 `string` 형식으로 저장됩니다.

> [!IMPORTANT]
> `WITH` 절이 없는 `OPENROWSET` 함수를 사용하면 예상 형식이 있는 값과 형식이 잘못 입력된 값이 모두 노출됩니다. 이 함수는 보고가 아닌 데이터 탐색을 위해 설계되었습니다. 보고서를 빌드하기 위해 이 함수에서 반환된 JSON 값의 구문을 분석하지 마십시오. 명시적 [WITH 절](#query-items-with-full-fidelity-schema)을 사용하여 보고서를 만드십시오. 전체 충실도 분석 저장소의 수정 사항을 적용하려면 Azure Cosmos DB 컨테이너에서 잘못된 형식이 사용된 값을 정리해야 합니다.

Mongo DB API 종류의 Azure Cosmos DB 계정을 쿼리해야 하는 경우 분석 저장소에서 전체 충실도 스키마 표현과 [Azure Cosmos DB 분석 저장소란?](../../cosmos-db/analytical-store-introduction.md#analytical-schema)에 사용할 확장 속성 이름에 대해 자세히 알아볼 수 있습니다.

### <a name="query-items-with-full-fidelity-schema"></a>전체 충실도 스키마를 사용하는 쿼리 항목

전체 충실도 스키마를 쿼리하는 동안 `WITH` 절에서 SQL 유형 및 필요한 Azure Cosmos DB 속성 유형을 명시적으로 지정해야 합니다.

다음 예제에서는 `string`이 `geo_id` 속성의 올바른 형식이고 `int32`이 `cases` 속성의 올바른 형식이라고 가정합니다.

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

다른 형식이 있는 `geo_id`와 `cases`의 값은 `NULL` 값으로 반환됩니다. 이 쿼리는 식(`cases.int32`)의 지정된 형식의 `cases`만 참조합니다.

Azure Cosmos DB 컨테이너에서 정리할 수 없는 다른 형식(`cases.int64`, `cases.float64`)의 값이 있는 경우 `WITH` 절에서 명시적으로 참조하고 결과를 결합해야 합니다. 다음 쿼리는 `cases` 열에 저장된 `int32`, `int64`, `float64`를 집계합니다.

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

이 예에서는 사례 수가 `int32`, `int64` 또는 `float64` 값으로 저장됩니다. 국가 당 사례 수를 계산하려면 모든 값을 추출해야 합니다.

## <a name="troubleshooting"></a>문제 해결

[자체 도움말 페이지](resources-self-help-sql-on-demand.md#cosmos-db)를 검토하여 Cosmos DB 쿼리의 잠재적인 문제를 해결하는 데 도움이 되는 알려진 문제 또는 문제 해결 단계를 찾습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure Synapse Link를 사용하여 Power BI 및 서버리스 SQL 풀 사용](../../cosmos-db/synapse-link-power-bi.md)
- [서버리스 SQL 풀에서 뷰 만들기 및 사용](create-use-views.md)
- [Azure Cosmos DB에 대해 서버리스 SQL 풀 뷰를 빌드하고 DirectQuery를 통해 Power BI 모델에 연결하는 방법에 대한 자습서](./tutorial-data-analyst.md)
- 오류가 발생하거나 성능 문제가 있는 경우 [Cosmos DB 자체 도움말 페이지에 대한 Synapse 링크](resources-self-help-sql-on-demand.md#cosmos-db)를 방문하세요.
- [Azure Synapse Analytics용 SQL 서버리스를 사용하여 Azure Cosmos DB를 쿼리](/learn/modules/query-azure-cosmos-db-with-sql-serverless-for-azure-synapse-analytics/)하는 방법에 대한 학습 모듈을 확인하세요.