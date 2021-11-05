---
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 11/01/2021
ms.openlocfilehash: 238db4caa1bcb11e0f4c27b6e490b0859b1e536d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851492"
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
| Azure 전용 SQL 풀 (이전의 SQL DW)\* | 예 | 
| Azure Table Storage | 예 |
| Amazon S3 | Yes | 
| Hive \* | Yes | 
| Oracle \* | Yes |
| SAP 테이블‘(SAP ECC 또는 SAP S/4HANA에 연결하는 경우)’ | Yes |
| SQL Server \* | Yes | 
| Teradata \* | Yes |

*\* Azure Purview는 현재 계보 또는 스캔에 대한 쿼리 또는 저장 프로시저를 지원하지 않습니다. 계보는 테이블 및 뷰 원본으로만 제한됩니다.*

자체 호스팅 Integration Runtime 사용 하는 경우에 대 한 계보 지원으로 최소 버전을 확인 합니다.

- 모든 사용 사례: 버전 5.9.7885.3 이상
- Oracle에서 데이터 복사: 버전 5.10 이상
- COPY 명령 또는 PolyBase: 버전 5.10 이상을 통해 Azure Synapse Analytics로 데이터 복사

#### <a name="limitations-on-copy-activity-lineage"></a>복사 작업 계보에 대 한 제한 사항

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
| Azure Synapse Analytics \* | 예 |
| Azure 전용 SQL 풀 (이전의 SQL DW)\* | Yes | 

*\* Azure Purview는 현재 계보 또는 스캔에 대한 쿼리 또는 저장 프로시저를 지원하지 않습니다. 계보는 테이블 및 뷰 원본으로만 제한됩니다.*

#### <a name="limitations-on-data-flow-lineage"></a>데이터 흐름 계보에 대 한 제한 사항

현재 데이터 흐름 계보가 부서의 범위 [리소스 집합과](../concept-resource-sets.md)통합 되지 않습니다.
