---
title: Purview 커넥터 개요
description: 본 문서에서는 Purview에서 지원되는 다양한 데이터 저장소 및 기능을 간략하게 설명합니다.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 58b8ef4959c54e8c597e3f54864caae77dfd1fa8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131061298"
---
# <a name="supported-data-stores"></a>지원되는 데이터 저장소

Purview는 다음의 데이터 저장소를 지원합니다. 각 데이터 저장소를 선택 하 여 지원 되는 기능 및 해당 구성에 대해 자세히 알아보세요.

## <a name="purview-data-sources"></a>Purview 데이터 소스

|**범주**|  **데이터 저장소**  |**메타데이터 추출**|**전체 검사**|**증분 검사**|**범위 검사**|**분류**|**액세스 정책**|**계보**|
|---|---|---|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| [예](register-scan-azure-blob-storage-source.md#register) | [예](register-scan-azure-blob-storage-source.md#scan)|[예](register-scan-azure-blob-storage-source.md#scan) | [예](register-scan-azure-blob-storage-source.md#scan)|[예](register-scan-azure-blob-storage-source.md#scan)| 예 | 아니요|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)| [예](register-scan-azure-cosmos-database.md#register) | [예](register-scan-azure-cosmos-database.md#scan)|[예](register-scan-azure-cosmos-database.md#scan) | [예](register-scan-azure-cosmos-database.md#scan)|[예](register-scan-azure-cosmos-database.md#scan)|아니요|아니요|
||[Azure Data Explorer](register-scan-azure-data-explorer.md)| [예](register-scan-azure-data-explorer.md#register) | [예](register-scan-azure-data-explorer.md#scan) | [예](register-scan-azure-data-explorer.md#scan) | [예](register-scan-azure-data-explorer.md#scan)| [예](register-scan-azure-data-explorer.md#scan)| 아니요 | 아니요 |
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)| [예](register-scan-adls-gen1.md#register) | [예](register-scan-adls-gen1.md#scan)|[예](register-scan-adls-gen1.md#scan) | [예](register-scan-adls-gen1.md#scan)|[예](register-scan-adls-gen1.md#scan)| 아니요 |[Data Factory 계보](how-to-link-azure-data-factory.md) |
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)| [예](register-scan-adls-gen2.md#register) | [예](register-scan-adls-gen2.md#scan)|[예](register-scan-adls-gen2.md#scan) | [예](register-scan-adls-gen2.md#scan)|[예](register-scan-adls-gen2.md#scan)| 예 | [Data Factory 계보](how-to-link-azure-data-factory.md) |
||[Azure 전용 SQL 풀(이전 SQL DW)](register-scan-azure-synapse-analytics.md)| [예](register-scan-azure-synapse-analytics.md#register) | [예](register-scan-azure-synapse-analytics.md#scan)| [예](register-scan-azure-synapse-analytics.md#scan)| [예](register-scan-azure-synapse-analytics.md#scan)| [예](register-scan-azure-synapse-analytics.md#scan)| 아니요 | 아니요|
||[Azure 파일](register-scan-azure-files-storage-source.md)|[예](register-scan-azure-files-storage-source.md#register) | [예](register-scan-azure-files-storage-source.md#scan) | [예](register-scan-azure-files-storage-source.md#scan) | [예](register-scan-azure-files-storage-source.md#scan) | [예](register-scan-azure-files-storage-source.md#scan) | 아니요 | 아니요 |
||[Azure SQL Database](register-scan-azure-sql-database.md)| [예](register-scan-azure-sql-database.md#register) | [예](register-scan-azure-sql-database.md#scan)|[예](register-scan-azure-sql-database.md#scan) | [예](register-scan-azure-sql-database.md#scan)|[예](register-scan-azure-sql-database.md#scan)| 아니요 |[Data Factory 계보](how-to-link-azure-data-factory.md)|
||[Azure SQL Database Managed Instance](register-scan-azure-sql-database-managed-instance.md)| [예](register-scan-azure-sql-database-managed-instance.md#register) | [예](register-scan-azure-sql-database-managed-instance.md#scan)| [예](register-scan-azure-sql-database-managed-instance.md#scan) | [예](register-scan-azure-sql-database-managed-instance.md#scan) | [예](register-scan-azure-sql-database-managed-instance.md#scan) | 아니요 | [Data Factory 계보](how-to-link-azure-data-factory.md) |
||[Azure Synapse Analytics(작업 영역)](register-scan-synapse-workspace.md)| [예](register-scan-synapse-workspace.md#register) | [예](register-scan-synapse-workspace.md#scan)| [예](register-scan-synapse-workspace.md#scan) | [예](register-scan-synapse-workspace.md#scan)| [예](register-scan-synapse-workspace.md#scan)| 아니요| [예](how-to-lineage-azure-synapse-analytics.md)|
||[Azure Database for MySQL](register-scan-azure-mysql-database.md)| [예](register-scan-azure-mysql-database.md#register) | [예](register-scan-azure-mysql-database.md#scan)| [예로](register-scan-azure-mysql-database.md#scan) | [예](register-scan-azure-mysql-database.md#scan) | [예](register-scan-azure-mysql-database.md#scan) | 아니요 | [Data Factory 계보](how-to-link-azure-data-factory.md) |
||[Azure Database for PostgreSQL](register-scan-azure-postgresql.md)| [예](register-scan-azure-postgresql.md#register) | [예](register-scan-azure-postgresql.md#scan)| [예](register-scan-azure-postgresql.md#scan) | [예](register-scan-azure-postgresql.md#scan) | [예](register-scan-azure-postgresql.md#scan) | 아니요 | [Data Factory 계보](how-to-link-azure-data-factory.md) |
|데이터베이스|[Cassandra](register-scan-cassandra-source.md)|[예](register-scan-cassandra-source.md#register) | [예](register-scan-cassandra-source.md#scan)| 아니요 | 아니요 | 아니요 | 아니요| [예](how-to-lineage-cassandra.md)|
||[Google BigQuery](register-scan-google-bigquery-source.md)| [예](register-scan-google-bigquery-source.md#register)| [예](register-scan-google-bigquery-source.md#scan)| 아니요 | 아니요 | 아니요 | 아니요| [예](how-to-lineage-google-bigquery.md)|
||[Hive 메타스토어 DB](register-scan-hive-metastore-source.md)| [예](register-scan-hive-metastore-source.md#register)| [예](register-scan-hive-metastore-source.md#scan)| 아니요 | 아니요 | 아니요 | 아니요| 예 |
||[Oracle DB](register-scan-oracle-source.md)| [예](register-scan-oracle-source.md#register)| [예](register-scan-oracle-source.md#scan)| 아니요 | 아니요 | 아니요 | 아니요| [예](how-to-lineage-oracle.md)|
||[SQL Server](register-scan-on-premises-sql-server.md)| [예](register-scan-on-premises-sql-server.md#register) | [예](register-scan-on-premises-sql-server.md#scan) | [예](register-scan-on-premises-sql-server.md#scan) | [예](register-scan-on-premises-sql-server.md#scan) | [예](register-scan-on-premises-sql-server.md#scan) | 아니요| [Data Factory 계보](how-to-link-azure-data-factory.md) |
||[Teradata](register-scan-teradata-source.md)| [예](register-scan-teradata-source.md#register)| [예](register-scan-teradata-source.md#scan)| 아니요 | 아니요 | 아니요 | 아니요| [예](how-to-lineage-teradata.md)|
|파일|[Amazon S3](register-scan-amazon-s3.md)|[예](register-scan-amazon-s3.md)| [예](register-scan-amazon-s3.md)| [예](register-scan-amazon-s3.md)| [예](register-scan-amazon-s3.md)| [예](register-scan-amazon-s3.md)| 아니요| 예|
|서비스 및 앱|[Erwin](register-scan-erwin-source.md)| [예](register-scan-erwin-source.md#register)| [예](register-scan-erwin-source.md#scan)| 아니요 | 아니요 | 아니요 | 아니요| [예](how-to-lineage-erwin.md)|
||[Looker](register-scan-looker-source.md)| [예](register-scan-looker-source.md#register)| [예](register-scan-looker-source.md#scan)| 아니요 | 아니요 | 아니요 | 아니요| [예](how-to-lineage-looker.md)|
||[Power BI](register-scan-power-bi-tenant.md)| [예](register-scan-power-bi-tenant.md#register)| [예](register-scan-power-bi-tenant.md#scan)| 아니요 | 아니요 | 아니요 | 아니요| [예](how-to-lineage-powerbi.md)|
||[SAP ECC](register-scan-sapecc-source.md)| [예](register-scan-sapecc-source.md#register)| [예](register-scan-sapecc-source.md#scan)| 아니요 | 아니요 | 아니요 | 아니요| [예](how-to-lineage-sapecc.md)|
||[SAP S4HANA](register-scan-saps4hana-source.md)| [예](register-scan-saps4hana-source.md#register)| [예](register-scan-saps4hana-source.md#scan)| 아니요 | 아니요 | 아니요 | 아니요| [예](how-to-lineage-sapecc.md)|

\* 부서의 범위는 증분 검색을 위해 Azure Database for MySQL UPDATE_TIME 메타 데이터를 사용 합니다. 경우에 따라이 필드는 데이터베이스에 유지 되지 않을 수 있으며 전체 검색을 수행 합니다. 자세한 내용은 MySQL 용 [INFORMATION_SCHEMA 테이블 표](https://dev.mysql.com/doc/refman/5.7/en/information-schema-tables-table.html) 를 참조 하세요. 

> [!NOTE]
> 현재 부서의 범위는 `/` 이름에, 또는가 있는 자산을 검색할 수 없습니다 `\` `#` . 검색 범위를 제한 하 고 자산 이름에 해당 문자가 포함 된 자산을 검사 하지 않으려면 [Azure SQL Database 등록 및 검색](register-scan-azure-sql-database.md#creating-the-scan)의 예제를 사용 합니다.

\* 부서의 범위는 증분 검색을 위해 Azure Database for MySQL UPDATE_TIME 메타 데이터를 사용 합니다. 경우에 따라이 필드는 데이터베이스에 유지 되지 않을 수 있으며 전체 검색을 수행 합니다. 자세한 내용은 MySQL 용 [INFORMATION_SCHEMA 테이블 표](https://dev.mysql.com/doc/refman/5.7/en/information-schema-tables-table.html) 를 참조 하세요. 

> [!NOTE]
> 현재 부서의 범위는 `/` 이름에, 또는가 있는 자산을 검색할 수 없습니다 `\` `#` . 검색 범위를 제한 하 고 자산 이름에 해당 문자가 포함 된 자산을 검사 하지 않으려면 [Azure SQL Database 등록 및 검색](register-scan-azure-sql-database.md#creating-the-scan)의 예제를 사용 합니다.

## <a name="scan-regions"></a>지역 검사
다음은 Purview 검사기가 실행되는 모든 Azure 데이터 원본(데이터 센터) 지역의 목록입니다. Azure 데이터 원본이 이 목록 외부의 지역에 있는 경우 검사기는 Purview 인스턴스의 지역에서 실행됩니다.
 
### <a name="purview-scanner-regions"></a>Purview 검사기 지역

- EastUs
- EastUs2 
- SouthCentralUS
- WestUs
- WestUs2
- SoutheastAsia
- WestEurope
- NorthEurope
- UkSouth
- AustraliaEast
- CanadaCentral
- BrazilSouth
- CentralIndia
- JapanEast
- SouthAfricaNorth
- FranceCentral
- KoreaCentral
- CentralUS
- NorthCentralUS
- EastAsia
- WestCentralUS
- AustraliaSoutheast

## <a name="next-steps"></a>다음 단계

- [Azure Blob Storage 소스 등록 및 검사](register-scan-azure-blob-storage-source.md)
