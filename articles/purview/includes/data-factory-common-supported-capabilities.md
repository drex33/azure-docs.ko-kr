---
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 08/10/2021
ms.openlocfilehash: 20a9b85c2d68e1c9b3c33362f393eaec0e255ce7
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122538215"
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
| Azure File Storage | 예 | 
| Azure SQL 데이터베이스 \* | Yes | 
| Azure SQL Managed Instance \* | Yes | 
| Azure Synapse Analytics \* | 예 | 
| Azure Table Storage | 예 |
| Amazon S3 | Yes | 
| Hive \* | Yes | 
| SAP 테이블‘(SAP ECC 또는 SAP S/4HANA에 연결하는 경우)’ | Yes |
| SQL Server \* | Yes | 
| Teradata \* | Yes |

*\* Azure Purview는 현재 계보 또는 스캔에 대한 쿼리 또는 저장 프로시저를 지원하지 않습니다. 계보는 테이블 및 뷰 원본으로만 제한됩니다.*

> [!Note]
> 계보 기능에는 복사 작업의 특정 성능 오버헤드가 있습니다. Purview에서 연결을 설정하는 사용자의 경우 완료하는 데 시간이 오래 걸리는 특정 복사 작업을 확인할 수 있습니다. 대부분의 영향은 무시할 수 없습니다. 복사 작업을 완료하는 데 평소보다 시간이 오래 걸리는 경우 지원 담당자에 게 문의하세요.

#### <a name="known-limitations-on-copy-activity-lineage"></a>복사 작업 계보에 대해 알려진 제한 사항

현재 다음과 같은 복사 작업 기능을 사용하는 경우 해당 계보는 아직 지원되지 않습니다.

- 이진 형식을 사용하여 Azure Data Lake Storage Gen1에 데이터를 복사합니다.
- PolyBase 또는 COPY 문을 사용하여 Azure Synapse Analytics에 데이터를 복사합니다.
- 이진 파일, 구분 기호로 분리된 텍스트, Excel, JSON 및 XML 파일에 대한 압축 설정입니다.
- Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, SQL Server 및 SAP 테이블의 원본 파티션 옵션입니다.
- 파일 기반 저장소에 대한 원본 파티션 검색 옵션입니다.
- 파일당 최대 행 수 설정을 사용하여 파일 기반 싱크로 데이터를 복사합니다.
- 복사 중에 열을 추가합니다.

추가 계보에는 다음 커넥터에 대한 데이터 자산 스키마 (자산-> 스키마 탭에 표시)가 보고됩니다.

- Azure Blob, Azure 파일 스토리지, ADLS Gen1, ADLS Gen2 및 Amazon S3의 CSV 및 Parquet 파일
- Azure Data Explorer, Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, SQL Server, Teradata
