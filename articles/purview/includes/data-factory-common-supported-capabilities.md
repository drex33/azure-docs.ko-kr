---
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 09/27/2021
ms.openlocfilehash: 8c4baccdbfb0f6162e666d6901e0080202a3b7e9
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212348"
---
### <a name="copy-activity-support"></a>복사 작업 지원

| 데이터 저장소 | 지원됨 | 
| ------------------- | ------------------- | 
| Azure Blob Storage | 예 |
| Azure Cognitive Search | Yes | 
| Azure Cosmos DB (SQL API) \* | Yes | 
| Azure Cosmos DB의 API for MongoDB \* | Yes |
| Azure 데이터 탐색기 \* | 예 | 
| Azure Data Lake Storage Gen1 | 예 | 
| Azure Data Lake Storage Gen2 | 예 | 
| Azure Database for Maria DB \* | Yes | 
| Azure Database for MySQL \* | Yes | 
| Azure Database for PostgreSQL \* | 예 |
| Azure 파일 | 예 | 
| Azure SQL 데이터베이스 \* | Yes | 
| Azure SQL Managed Instance \* | Yes | 
| Azure Synapse Analytics \* | 예 | 
| Azure Table Storage | 예 |
| Amazon S3 | Yes | 
| Hive \* | Yes | 
| 오라클 \* | Yes |
| SAP 테이블‘(SAP ECC 또는 SAP S/4HANA에 연결하는 경우)’ | Yes |
| SQL Server \* | Yes | 
| Teradata \* | Yes |

*\* Azure Purview는 현재 계보 또는 스캔에 대한 쿼리 또는 저장 프로시저를 지원하지 않습니다. 계보는 테이블 및 뷰 원본으로만 제한됩니다.*

자체 호스팅 Integration Runtime 사용하는 경우 계보가 지원되는 최소 버전은 다음과 같습니다.
- Oracle에서 데이터 복사: 버전 5.10 이상
- COPY 명령 또는 PolyBase를 통해 Azure Synapse Analytics 데이터 복사: 버전 5.10 이상

#### <a name="limitations-on-copy-activity-lineage"></a>복사 작업 계보에 대한 제한 사항

현재 다음과 같은 복사 작업 기능을 사용하는 경우 해당 계보는 아직 지원되지 않습니다.

- 이진 형식을 사용하여 Azure Data Lake Storage Gen1에 데이터를 복사합니다.
- 이진 파일, 구분 기호로 분리된 텍스트, Excel, JSON 및 XML 파일에 대한 압축 설정입니다.
- Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, SQL Server 및 SAP 테이블의 원본 파티션 옵션입니다.
- 파일당 최대 행 수 설정을 사용하여 파일 기반 싱크로 데이터를 복사합니다.

추가 계보에는 다음 커넥터에 대한 데이터 자산 스키마 (자산-> 스키마 탭에 표시)가 보고됩니다.

- Azure Blob, Azure Files, ADLS Gen1, ADLS Gen2 및 Amazon S3의 CSV 및 Parquet 파일
- Azure Data Explorer, Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, SQL Server, Teradata

### <a name="data-flow-support"></a>Data Flow 지원

| 데이터 저장소 | 지원됨 |
| ------------------- | ------------------- | 
| Azure Blob Storage | Yes |
| Azure Cosmos DB (SQL API) \* | Yes | 
| Azure Data Lake Storage Gen1 | 예 |
| Azure Data Lake Storage Gen2 | Yes |
| Azure Database for MySQL \* | Yes | 
| Azure Database for PostgreSQL \* | 예 |
| Azure SQL 데이터베이스 \* | Yes |
| Azure SQL Managed Instance \* | Yes | 
| Azure Synapse Analytics \* | Yes |

*\* Azure Purview는 현재 계보 또는 스캔에 대한 쿼리 또는 저장 프로시저를 지원하지 않습니다. 계보는 테이블 및 뷰 원본으로만 제한됩니다.*

#### <a name="limitations-on-data-flow-lineage"></a>데이터 흐름 계보에 대한 제한 사항

현재 데이터 흐름 계보는 Purview [리소스 집합](../concept-resource-sets.md)과 통합되지 않습니다.
