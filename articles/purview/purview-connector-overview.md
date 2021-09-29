---
title: Purview 커넥터 개요
description: 본 문서에서는 Purview에서 지원되는 다양한 데이터 저장소 및 기능을 간략하게 설명합니다.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 96a51e4ca3ab1f145b5d2820b43290f9a5f98d48
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214825"
---
# <a name="supported-data-stores"></a>지원되는 데이터 저장소

Purview는 다음의 데이터 저장소를 지원합니다. 각 데이터 저장소를 선택하여 지원되는 기능 및 해당 구성을 자세히 알아봅니다.

## <a name="purview-data-sources"></a>Purview 데이터 소스

|**범주**|  **데이터 저장소**  |**메타데이터 추출**|**전체 검사**|**증분 검사**|**범위 검사**|**분류**|**계보**|
|---|---|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| 예| 예| 예| 예| 예| 예|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|예| 예| 예| 예| 예| 예|
||[Azure Data Explorer](register-scan-azure-data-explorer.md)|예| 예| 예| 예| 예| 예|
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)|예| 예| 예| 예| 예| 예|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)|예| 예| 예| 예| 예| 예|
||[Azure 전용 SQL 풀(이전 SQL DW)](register-scan-azure-synapse-analytics.md)|예| 예| 아니요| 예| 예| 예|
||[Azure SQL Database](register-scan-azure-sql-database.md)|예| 예| 아니요| 예| 예| 예|
||[Azure SQL Database Managed Instance](register-scan-azure-sql-database-managed-instance.md)|예| 예| 아니요| 예| 예| 예|
||[Azure Synapse Analytics(작업 영역)](register-scan-synapse-workspace.md)|예| 예| 아니요| 예| 예| 예|
|데이터베이스|[Cassandra](register-scan-cassandra-source.md)|예| 예| 아니요| 아니요| 아니요| 예|
||[Google BigQuery](register-scan-google-bigquery-source.md)|예| 예| 아니요| 아니요| 아니요| 예|
||[Hive 메타스토어 DB](register-scan-oracle-source.md)|예| 예| 아니요| 아니요| 아니요| 예|
||[Oracle DB](register-scan-oracle-source.md)|예| 예| 예| 예| 아니요| 예|
||[SQL Server](register-scan-on-premises-sql-server.md)|예| 예| 아니요| 예| 예| 예|
||[Teradata](register-scan-teradata-source.md)|예| 예| 아니요| 아니요| 아니요| 예|
|파일|[Amazon S3](register-scan-amazon-s3.md)|예| 예| 예| 예| 예| 예|
|서비스 및 앱|[Erwin](register-scan-erwin-source.md)|예| 예| 아니요| 아니요| 아니요| 예|
||[Looker](register-scan-looker-source.md)|예| 예| 아니요| 아니요| 아니요| 예|
||[Power BI](register-scan-power-bi-tenant.md)|예| 예| 아니요| 아니요| 아니요| 예|
||[SAP ECC](register-scan-sapecc-source.md)|예| 예| 아니요| 아니요| 아니요| 예|
||[SAP S4HANA](register-scan-saps4hana-source.md)|예| 예| 아니요| 아니요| 아니요| 예|

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
