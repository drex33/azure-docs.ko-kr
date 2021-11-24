---
title: Purview 커넥터 개요
description: 본 문서에서는 Purview에서 지원되는 다양한 데이터 저장소 및 기능을 간략하게 설명합니다.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 11/23/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 25bf0d1c304750866d9836e4d48a8b0e53c8c746
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133071111"
---
# <a name="supported-data-stores"></a>지원되는 데이터 저장소

Purview는 다음의 데이터 저장소를 지원합니다. 각 데이터 저장소를 선택 하 여 지원 되는 기능 및 해당 구성에 대해 자세히 알아보세요.

## <a name="purview-data-sources"></a>Purview 데이터 소스

|**범주**|  **데이터 저장소**  |**기술 메타 데이터** |**분류** |**계보** | **액세스 정책** |
|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| [예](register-scan-azure-blob-storage-source.md#register) | [예](register-scan-azure-blob-storage-source.md#scan)| 제한이 | [예](how-to-access-policies-storage.md) |
||    [Azure Cosmos DB](register-scan-azure-cosmos-database.md)| [예](register-scan-azure-cosmos-database.md#register) | [예](register-scan-azure-cosmos-database.md#scan)|아니요*|No|
||    [Azure Data Explorer](register-scan-azure-data-explorer.md)| [예](register-scan-azure-data-explorer.md#register) | [예](register-scan-azure-data-explorer.md#scan)| 아니요* | No |
||    [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)| [예](register-scan-adls-gen1.md#register) | [예](register-scan-adls-gen1.md#scan)| 제한이 | No |
||    [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)| [예](register-scan-adls-gen2.md#register) | [예](register-scan-adls-gen2.md#scan)| 제한이 | [예](how-to-access-policies-storage.md) |
|| [Azure Database for MySQL](register-scan-azure-mysql-database.md) | [예](register-scan-azure-mysql-database.md#register) | [예](register-scan-azure-mysql-database.md#scan) | 아니요* | No |
|| [Azure Database for PostgreSQL](register-scan-azure-postgresql.md) | [예](register-scan-azure-postgresql.md#register) | [예](register-scan-azure-postgresql.md#scan) | 아니요* | No |
||    [Azure 전용 SQL 풀(이전 SQL DW)](register-scan-azure-synapse-analytics.md)| [예](register-scan-azure-synapse-analytics.md#register) | [예](register-scan-azure-synapse-analytics.md#scan)| 아니요* | No |
||    [Azure 파일](register-scan-azure-files-storage-source.md)|[예](register-scan-azure-files-storage-source.md#register) | [예](register-scan-azure-files-storage-source.md#scan) | 제한이 |  No |
||    [Azure SQL Database](register-scan-azure-sql-database.md)| [예](register-scan-azure-sql-database.md#register) |[예](register-scan-azure-sql-database.md#scan)| 아니요* | No |
||    [Azure SQL Database Managed Instance](register-scan-azure-sql-database-managed-instance.md)|  [예](register-scan-azure-sql-database-managed-instance.md#scan) | [예](register-scan-azure-sql-database-managed-instance.md#scan) | 아니요* | No |
||    [Azure Synapse Analytics(작업 영역)](register-scan-synapse-workspace.md)| [예](register-scan-synapse-workspace.md#register) | [예](register-scan-synapse-workspace.md#scan)| [예-Synapse 파이프라인](how-to-lineage-azure-synapse-analytics.md)| No|
|데이터베이스| [Amazon RDS](register-scan-amazon-rds.md) | [예](register-scan-amazon-rds.md#register-an-amazon-rds-data-source) | [예](register-scan-amazon-rds.md#scan-an-amazon-rds-database) | 아니요 | 아니요 |
||    [Cassandra](register-scan-cassandra-source.md)|[예](register-scan-cassandra-source.md#register) | 아니요 | [예](how-to-lineage-cassandra.md)| 아니요|
|| [DB2](register-scan-db2.md) | [예](register-scan-db2.md#register) | 아니요 | [예](register-scan-db2.md#scan) | 아니요 |
||    [Google BigQuery](register-scan-google-bigquery-source.md)| [예](register-scan-google-bigquery-source.md#register)| 아니요 | [예](how-to-lineage-google-bigquery.md)| 아니요|
|| [Hive 메타스토어 데이터베이스](register-scan-hive-metastore-source.md) | [예](register-scan-hive-metastore-source.md#register) | 예 | 예* | No|
|| [MySQL](register-scan-mysql.md) | [예](register-scan-mysql.md#register) | 아니요 | [예](register-scan-mysql.md#scan) | 아니요 |
|| [Oracle](register-scan-oracle-source.md) | [예](register-scan-oracle-source.md#register)|  아니요 | [예*](how-to-lineage-oracle.md) | No|
|| [PostgreSQL](register-scan-postgresql.md) | [예](register-scan-postgresql.md#register) | 아니요 | [예](register-scan-postgresql.md#scan) | 아니요 |
|| [Snowflake](register-scan-snowflake.md) | [예](register-scan-snowflake.md#register) | 아니요 | [예](register-scan-snowflake.md#scan) | 아니요 |
||    [SQL Server](register-scan-on-premises-sql-server.md)| [예](register-scan-on-premises-sql-server.md#register) |[예](register-scan-on-premises-sql-server.md#scan) | 아니요* | No|
||    [Teradata](register-scan-teradata-source.md)| [예](register-scan-teradata-source.md#register)|  아니요 | [예*](how-to-lineage-teradata.md) | 아니요|
|파일|[Amazon S3](register-scan-amazon-s3.md)|[예](register-scan-amazon-s3.md)| [예](register-scan-amazon-s3.md)| 제한이 | No|
|서비스 및 앱|    [Erwin](register-scan-erwin-source.md)| [예](register-scan-erwin-source.md#register)| 아니요 | [예](how-to-lineage-erwin.md)| 아니요|
||    [Looker](register-scan-looker-source.md)| [예](register-scan-looker-source.md#register)| 아니요 | [예](how-to-lineage-looker.md)| 아니요|
||    [Power BI](register-scan-power-bi-tenant.md)| [예](register-scan-power-bi-tenant.md#register)| 아니요 | [예](how-to-lineage-powerbi.md)| 아니요|
|| [Salesforce](register-scan-salesforce.md) | [예](register-scan-salesforce.md#register) | 아니요 | 아니요 | 아니요 |
||    [SAP ECC](register-scan-sapecc-source.md)| [예](register-scan-sapecc-source.md#register) | 아니요 | [예*](how-to-lineage-sapecc.md) | No|
|| [SAP S/4HANA](register-scan-saps4hana-source.md) | [예](register-scan-saps4hana-source.md#register)| 아니요 | [예*](how-to-lineage-sapecc.md) | No|

\* 데이터 원본 내 자산의 계보 외에도 데이터 집합이 [Data Factory](how-to-link-azure-data-factory.md) 또는 [Synapse 파이프라인](how-to-lineage-azure-synapse-analytics.md)에서 원본/싱크로 사용 되는 경우에도 계보가 지원 됩니다.

> [!NOTE]
> 현재 부서의 범위는 `/` 이름에, 또는가 있는 자산을 검색할 수 없습니다 `\` `#` . 검색 범위를 제한 하 고 자산 이름에 해당 문자가 포함 된 자산을 검사 하지 않으려면 [Azure SQL Database 등록 및 검색](register-scan-azure-sql-database.md#creating-the-scan)의 예제를 사용 합니다.

## <a name="scan-regions"></a>지역 검사
다음은 Purview 검사기가 실행되는 모든 Azure 데이터 원본(데이터 센터) 지역의 목록입니다. Azure 데이터 원본이 이 목록 외부의 지역에 있는 경우 검사기는 Purview 인스턴스의 지역에서 실행됩니다.

### <a name="purview-scanner-regions"></a>Purview 검사기 지역

- 오스트레일리아 동부
- 오스트레일리아 남동부
- 브라질 남부
- 캐나다 중부
- 인도 중부
- 미국 중부
- 동아시아
- 미국 동부
- 미국 동부 2
- 프랑스 중부
- 일본 동부
- 한국 중부
- 미국 중북부
- 북유럽
- 남아프리카 북부
- 미국 중남부
- 동남아시아
- 아랍에미리트 북부
- 영국 남부
- 미국 중서부
- 서유럽
- 미국 서부
- 미국 서부 2

## <a name="next-steps"></a>다음 단계

- [Azure Blob Storage 소스 등록 및 검사](register-scan-azure-blob-storage-source.md)