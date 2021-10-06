---
title: 서버리스 SQL 풀에 대한 모범 사례
description: 서버리스 SQL 풀을 사용하기 위한 권장 사항 및 모범 사례입니다.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 7611d9067422a0e2e342bbafeb315b5b1545a212
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545193"
---
# <a name="best-practices-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 서버리스 SQL 풀에 대한 모범 사례

이 문서에서는 서버리스 SQL 풀 사용에 대한 모범 사례의 컬렉션을 찾을 수 있습니다. 서버리스 SQL 풀은 Azure Synapse Analytics의 리소스입니다. 전용 SQL 풀을 사용 하는 경우 특정 지침은 [전용 SQL 풀에 대 한 모범 사례](best-practices-dedicated-sql-pool.md) 를 참조 하세요.

서버를 사용 하지 않는 SQL 풀을 사용 하면 Azure Storage 계정의 파일을 쿼리할 수 있습니다. SQL 주문형은 로컬 스토리지 또는 수집 기능이 없습니다. 쿼리가 대상으로 하는 모든 파일은 서버를 사용 하지 않는 SQL 풀의 외부에 있습니다. 저장소에서 파일 읽기와 관련 된 모든 작업은 쿼리 성능에 영향을 줄 수 있습니다.

몇 가지 일반적인 지침은 다음과 같습니다.

- 클라이언트 응용 프로그램이 서버를 사용 하지 않는 SQL 풀로 배치 된 확인 합니다.
  - Azure 외부에서 클라이언트 응용 프로그램을 사용 하는 경우 클라이언트 컴퓨터에 가까운 지역에서 서버 리스 SQL 풀을 사용 하 고 있는지 확인 합니다. 클라이언트 응용 프로그램 예제에는 Power BI Desktop, SQL Server Management Studio 및 Azure Data Studio 있습니다.
- 저장소 및 서버를 사용 하지 않는 SQL 풀이 동일한 지역에 있는지 확인 합니다. Storage 예제에는 Azure Data Lake Storage 및 Azure Cosmos DB가 포함 됩니다.
- 분할을 사용 하 여 [저장소 레이아웃을 최적화](#prepare-files-for-querying) 하 고 파일의 범위를 100에서 10gb로 유지 합니다.
- 많은 수의 결과를 반환 하는 경우 Azure Synapse Studio가 아닌 SQL Server Management Studio 또는 Azure Data Studio를 사용 하 고 있는지 확인 합니다. Azure Synapse Studio는 대량 결과 집합을 위해 설계 되지 않은 웹 도구입니다.
- 문자열 열을 기준으로 결과를 필터링 하는 경우 `BIN2_UTF8` 데이터 정렬을 사용 하십시오.
- Power BI 가져오기 모드나 Azure Analysis Services를 사용 하 여 클라이언트 쪽에서 결과를 캐시 하 고 주기적으로 새로 고치는 것이 좋습니다. 복잡 한 쿼리를 사용 하거나 많은 양의 데이터를 처리 하는 경우 서버를 사용 하지 않는 SQL 풀이 Power BI 직접 쿼리 모드에서 대화형 환경을 제공할 수 없습니다.

## <a name="client-applications-and-network-connections"></a>클라이언트 애플리케이션 및 네트워크 연결

최적의 연결을 사용 하 여 클라이언트 응용 프로그램이 가능한 가장 가까운 Azure Synapse 작업 영역에 연결 되어 있는지 확인 합니다.
- Azure Synapse 작업 영역을 사용 하 여 클라이언트 응용 프로그램을 찾습니다. Power BI 또는 azure Analysis Service와 같은 응용 프로그램을 사용 하는 경우 azure Synapse 작업 영역을 배치 하는 동일한 지역에 있는지 확인 합니다. 필요한 경우 클라이언트 애플리케이션과 쌍을 이루는 별도의 작업 영역을 만듭니다. 클라이언트 응용 프로그램과 Azure Synapse 작업 영역을 서로 다른 지역에 배치 하면 대기 시간이 커지고 결과 스트리밍이 느려질 수 있습니다.
- 온-프레미스 응용 프로그램에서 데이터를 읽는 경우 Azure Synapse 작업 영역이 사용자의 위치에 가까운 지역에 있는지 확인 합니다.
- 많은 양의 데이터를 읽는 동안 네트워크 대역폭 문제가 없는지 확인 합니다.
- Azure Synapse Studio를 사용 하 여 많은 양의 데이터를 반환 하지 마세요. Azure Synapse Studio는 HTTPS 프로토콜을 사용 하 여 데이터를 전송 하는 웹 도구입니다. Azure Data Studio 또는 SQL Server Management Studio를 사용하여 많은 양의 데이터를 읽습니다.

## <a name="storage-and-content-layout"></a>스토리지 및 콘텐츠 레이아웃

서버를 사용 하지 않는 SQL 풀의 저장소 및 콘텐츠 레이아웃에 대 한 모범 사례는 다음과 같습니다.

### <a name="colocate-your-storage-and-serverless-sql-pool"></a>스토리지 및 서버리스 SQL 풀 공동 배치

대기 시간을 최소화 하려면 Azure Storage 계정을 사용 하거나 분석 저장소 및 서버를 사용 하지 않는 SQL 풀 끝점을 Azure Cosmos DB 합니다. 작업 영역을 만드는 동안 프로비저닝된 스토리지 계정과 엔드포인트는 같은 지역에 있습니다.

성능을 최적화하려면 서버리스 SQL 풀에서 다른 스토리지 계정에 액세스할 때 스토리지 계정과 서버리스 SQL 풀이 같은 지역에 있어야 합니다. 서로 다른 지역에 있으면 원격 지역과 엔드포인트의 지역 간에 데이터의 네트워크 전송에 걸리는 대기 시간이 증가합니다.

### <a name="azure-storage-throttling"></a>Azure Storage 제한

여러 애플리케이션과 서비스가 스토리지 계정에 액세스할 수 있습니다. Storage 제한은 응용 프로그램, 서비스 및 서버를 사용 하지 않는 SQL 풀 워크 로드에서 생성 된 결합 IOPS 또는 처리량이 저장소 계정 제한을 초과 하는 경우에 발생 합니다. 결과적으로 쿼리 성능에 매우 부정적인 영향을 미치게 됩니다.

서버리스 SQL 풀은 제한이 감지되면 이러한 문제를 해결하는 기능을 기본적으로 갖추고 있습니다. 서버를 사용 하지 않는 SQL 풀이 제한이 해결 될 때까지 느린 속도로 저장소에 요청 합니다.

> [!TIP]
> 최적의 쿼리 실행을 위해, 쿼리를 실행하는 중에는 스토리지 계정에 다른 워크로드 부하를 추가해서는 안 됩니다.

### <a name="azure-ad-pass-through-authentication-performance"></a>Azure AD 통과 인증 성능

서버를 사용 하지 않는 SQL 풀을 사용 하면 Azure Active Directory (Azure AD) 통과 인증 또는 공유 액세스 서명 자격 증명을 사용 하 여 저장소의 파일에 액세스할 수 있습니다. Azure AD 통과 인증을 사용 하는 경우 공유 액세스 서명을 사용 하는 것 보다 성능이 저하 될 수 있습니다.

더 나은 성능이 필요한 경우 공유 액세스 서명 자격 증명을 사용 하 여 저장소에 액세스를 시도 합니다.

### <a name="prepare-files-for-querying"></a>쿼리할 파일 준비

가능하다면 성능 향상을 위한 파일을 준비해도 됩니다.

- Large CSV 및 JSON 파일을 Parquet로 변환 합니다. Parquet은 칼럼 형식입니다. 또한 압축되므로 동일한 데이터를 포함하는 CSV 또는 JSON 파일보다 파일 크기가 작습니다. 서버를 사용 하지 않는 SQL 풀은 Parquet 파일을 읽는 경우 쿼리에 필요 하지 않은 열 및 행을 건너뜁니다. 서버를 사용 하지 않는 SQL 풀에는 덜 시간이 소요 되 고 읽기에 필요한 저장소 요청이 줄어듭니다.
- 쿼리 대상이 대형 파일 하나인 경우 여러 개의 작은 파일로 분할하는 것이 유리합니다.
- CSV 파일 크기를 100MB에서 10GB로 유지하세요.
- 단일 OPENROWSET 경로 또는 외부 테이블 LOCATION에 대해 크기가 동일한 파일을 지정하는 것이 좋습니다.
- 파티션을 다른 폴더 또는 파일 이름에 저장하여 데이터를 분할합니다. [ 및 filepath 함수를 사용하여 특정 파티션을 대상으로 지정](#use-filename-and-filepath-functions-to-target-specific-partitions)을 참조하세요.

### <a name="colocate-your-azure-cosmos-db-analytical-storage-and-serverless-sql-pool"></a>Azure Cosmos DB 분석 저장소 및 서버를 사용 하지 않는 SQL 풀 공동 배치

Azure Cosmos DB 분석 저장소가 Azure Synapse 작업 영역과 동일한 지역에 배치 되어 있는지 확인 합니다. 영역 간 쿼리를 수행하면 대기 시간이 길어질 수 있습니다. 연결 문자열에서 region 속성을 사용 하 여 분석 저장소가 배치 되는 영역을 명시적으로 지정 합니다 ( [서버 리스 SQL 풀을 사용 하 여 Azure Cosmos DB 쿼리](query-cosmos-db-analytical-store.md#overview)참조).

```
'account=<database account name>;database=<database name>;region=<region name>'
```

## <a name="csv-optimizations"></a>CSV 최적화

서버 리스 SQL 풀에서 CSV 파일을 사용 하는 방법에 대 한 모범 사례는 다음과 같습니다.

### <a name="use-parser_version-20-to-query-csv-files"></a>PARSER_VERSION 2.0을 사용하여 CSV 파일 쿼리

CSV 파일을 쿼리할 때 성능 최적화 파서를 사용할 수 있습니다. 자세한 내용은 [PARSER_VERSION](develop-openrowset.md)을 참조하세요.

### <a name="manually-create-statistics-for-csv-files"></a>CSV 파일에 대한 통계 수동 작성

서버리스 SQL 풀은 통계를 기반으로 최적의 쿼리 실행 계획을 생성합니다. 필요한 경우 Parquet 파일의 열에 대해 통계가 자동으로 생성 됩니다. 지금은 CSV 파일의 열에 대해 통계가 자동으로 생성 되지 않습니다. 쿼리에서 사용 하는 열, 특히 DISTINCT, JOIN, WHERE, ORDER BY 및 GROUP BY에 사용 되는 열에 대해 통계를 수동으로 만듭니다. 자세한 내용은 [서버리스 SQL 풀의 통계](develop-tables-statistics.md#statistics-in-serverless-sql-pool)를 확인하세요.

## <a name="data-types"></a>데이터 형식

서버를 사용 하지 않는 SQL 풀에서 데이터 형식을 사용 하기 위한 모범 사례는 다음과 같습니다.

### <a name="use-appropriate-data-types"></a>적절한 데이터 형식 사용

쿼리에서 사용 하는 데이터 형식은 성능 및 동시성에 영향을 줍니다. 이러한 지침을 따르면 더 나은 성능을 얻을 수 있습니다. 

- 가능한 가장 큰 값을 수용할 수 있는 가장 작은 데이터 크기를 사용 합니다.
  - 최대 문자 값 길이가 30자인 경우 길이가 30인 문자 데이터 형식을 사용합니다.
  - 모든 문자 열 값의 크기가 고정 되어 있으면 **char** 또는 **nchar** 를 사용 합니다. 그렇지 않으면 **varchar** 또는 **nvarchar** 를 사용합니다.
  - 최대 정수 열 값이 500이면 이 값을 수용할 수 있는 가장 작은 데이터 형식인 **smallint** 를 사용합니다. 정수 데이터 형식 범위는 [이 문서](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true)에서 찾을 수 있습니다.
- 가능하다면 **nvarchar** 및 **nchar** 대신 **varchar** 및 **char** 을 사용합니다.
  - utf-8 인코딩을 사용 하 여 Parquet, Azure Cosmos DB, 델타 Lake 또는 CSV에서 데이터를 읽는 경우 일부 UTF8 데이터 정렬과 함께 **varchar** 형식을 사용 하세요.
  - CSV가 아닌 파일 (예: ASCII)에서 데이터를 읽는 경우 UTF8 데이터 정렬이 없는 **varchar** 형식을 사용 합니다.
  - CSV UTF-16 파일에서 데이터를 읽는 경우 **nvarchar** 형식을 사용 하세요.
- 가능하다면 정수 기반 데이터 형식을 사용합니다. SORT, JOIN 및 GROUP BY 작업은 문자 데이터보다 정수에서 더 빠르게 수행됩니다.
- 스키마 유추를 사용 하는 경우 [유추 된 데이터 형식을 확인](#check-inferred-data-types) 하 고 가능한 경우 더 작은 형식으로 명시적으로 재정의 합니다.

### <a name="check-inferred-data-types"></a>유추한 데이터 형식 확인

[스키마 유추](query-parquet-files.md#automatic-schema-inference)는 파일 스키마를 알지 못해도 신속하게 쿼리를 작성하고 데이터를 탐색하는 데 도움을 줍니다. 이 편의를 위해 유추 된 데이터 형식이 실제 데이터 형식 보다 클 수 있습니다. 이러한 불일치는 적절 한 데이터 형식이 사용 되는지 확인 하기 위해 소스 파일에 충분 한 정보가 없는 경우에 발생 합니다. 예를 들어 Parquet 파일에는 최대 문자 열 길이에 대한 메타데이터가 없습니다. 따라서 서버리스 SQL 풀은 이 길이를 varchar(8000)로 유추합니다.

[sp_describe_first_results_set](/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true)를 사용하여 쿼리의 결과 데이터 형식을 확인할 수 있습니다.

다음 예제에서는 유추한 데이터 형식을 최적화하는 방법을 보여줍니다. 이 프로시저는 유추한 데이터 형식을 보여 주는 데 사용됩니다.

```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

결과 세트:

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------|
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|int|4|

쿼리에 대해 유추한 데이터 형식을 알고 있으면 다음과 같이 적절한 데이터 형식을 지정할 수 있습니다.

```sql  
SELECT
    vendorID, tpepPickupDateTime, passengerCount
FROM 
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=2018/puMonth=*/*.snappy.parquet',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="filter-optimization"></a>필터 최적화

서버를 사용 하지 않는 SQL 풀에서 쿼리를 사용 하는 모범 사례는 다음과 같습니다.

### <a name="push-wildcards-to-lower-levels-in-the-path"></a>와일드카드를 푸시하여 경로에서 수준 내리기

경로에 와일드카드를 사용하여 [여러 파일 및 폴더를 쿼리](query-data-storage.md#query-multiple-files-or-folders)할 수 있습니다. 서버를 사용 하지 않는 SQL 풀은 저장소 계정에서 저장소 계정에 파일을 나열 합니다. 여기에는 저장소 API가 사용 됩니다. 지정된 경로와 일치하지 않는 파일은 제거됩니다. 초기 파일 목록을 줄이면 지정된 경로의 첫 번째 와일드카드까지 일치하는 파일이 여러 개 있을 때 성능을 높일 수 있습니다.

### <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>filename 및 filepath 함수를 사용하여 특정 파티션을 대상으로 지정

데이터는 종종 여러 파티션으로 구성됩니다. 서버리스 SQL 풀에 특정 폴더와 파일을 쿼리하도록 지시할 수 있습니다. 이렇게 하면 쿼리를 읽고 처리 해야 하는 데이터 양과 파일 수가 줄어듭니다. 그로 인한 보너스로 성능이 향상됩니다.

자세한 내용은 [filename](query-data-storage.md#filename-function) 및 [filepath](query-data-storage.md#filepath-function) 함수에 대해 읽어보고 [특정 파일 쿼리](query-specific-files.md) 예제를 참조하세요.

> [!TIP]
> 항상 filepath 및 filename 함수의 결과를 적절한 데이터 형식으로 캐스팅하세요. 문자 데이터 형식을 사용하는 경우 적절한 길이를 사용해야 합니다.

파티션 제거에 사용되는 filepath 및 filename 함수는 현재 Azure Synapse Analytics용 Apache Spark에서 생성된 테이블마다 자동으로 생성된 외부 테이블을 제외한 나머지 외부 테이블을 지원하지 않습니다.

저장된 데이터가 분할되지 않는 경우 분할하는 것이 좋습니다. 이러한 방식으로 이러한 함수를 사용하여 해당 파일을 대상으로 하는 쿼리를 최적화할 수 있습니다. 서버를 사용 하지 않는 SQL 풀에서 [Azure Synapse 테이블에 대 한 분할 된 Apache Spark를 쿼리하면](develop-storage-files-spark-tables.md) 쿼리가 필요한 파일만 자동으로 대상으로 지정 합니다.

### <a name="use-proper-collation-to-utilize-predicate-pushdown-for-character-columns"></a>적절한 데이터 정렬을 사용하여 문자 열에 대한 조건자 푸시다운 활용

Parquet 파일의 데이터는 행 그룹으로 구성 됩니다. 서버를 사용 하지 않는 SQL 풀이 WHERE 절의 지정 된 조건자를 기준으로 행 그룹을 건너뛰고 IO를 줄입니다. 결과적으로 쿼리 성능이 향상 됩니다.

Parquet 파일의 문자 열에 대 한 조건자 푸시 다운은 Latin1_General_100_BIN2_UTF8 데이터 정렬에만 지원 됩니다. WITH 절을 사용 하 여 특정 열의 데이터 정렬을 지정할 수 있습니다. WITH 절을 사용 하 여이 데이터 정렬을 지정 하지 않으면 데이터베이스 데이터 정렬이 사용 됩니다.

## <a name="optimize-repeating-queries"></a>반복 쿼리 최적화

서버를 사용 하지 않는 SQL 풀에서 CETAS 사용에 대 한 모범 사례는 다음과 같습니다.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>CETAS를 사용하여 쿼리 성능 및 조인 향상

[CETAS](develop-tables-cetas.md)는 서버리스 SQL 풀에서 제공하는 가장 중요한 기능 중 하나입니다. CETAS는 외부 테이블 메타데이터를 만들고 SELECT 쿼리 결과를 스토리지 계정의 파일 세트로 내보내는 병렬 작업입니다.

CETAS를 사용하면 조인된 참조 테이블처럼 쿼리에서 자주 사용하는 부분을 새 파일 세트에 구체화할 수 있습니다. 그러면 여러 쿼리에서 공통 조인을 반복하는 대신, 이 단일 외부 테이블에 조인할 수 있습니다.

CETAS에서 Parquet 파일을 생성 하면 첫 번째 쿼리가이 외부 테이블을 대상으로 하는 경우 통계가 자동으로 생성 됩니다. 그 결과 CETAS를 사용 하 여 생성 된 테이블을 대상으로 하는 후속 쿼리의 성능이 향상 됩니다.

## <a name="next-steps"></a>다음 단계

일반적인 문제에 대한 해결 방법은 [문제 해결](resources-self-help-sql-on-demand.md) 문서를 검토하세요. 서버를 사용 하지 않는 SQL 풀이 아닌 전용 SQL 풀로 작업 하는 경우 특정 지침은 [전용 SQL 풀에 대 한 모범 사례](best-practices-dedicated-sql-pool.md) 를 참조 하세요.
