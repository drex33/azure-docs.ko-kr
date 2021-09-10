---
title: 전용 SQL 풀을 위한 PolyBase 데이터 로드 전략 설계
description: ETL 대신 전용 SQL로 데이터 로드를 위한 ELT(추출, 변환 및 로드) 프로세스를 설계합니다.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.openlocfilehash: 79be8f9d98816690171476d9c5764884e1b4e0f7
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567467"
---
# <a name="design-a-polybase-data-loading-strategy-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 전용 SQL 풀을 위한 PolyBase 데이터 로드 전략 설계

기존의 SMP 데이터 웨어하우스는 데이터 로드를 위해 ETL(추출, 변환 및 로드) 프로세스를 사용합니다. Azure SQL 풀은 스케일링이 가능하고 유연한 컴퓨팅 및 스토리지 리소스를 활용하는 MPP(대량 병렬 처리) 아키텍처입니다. ELT(추출, 로드 및 변환) 프로세스를 사용하면 기본 제공된 분산 쿼리 처리 기능을 활용하고, 로드 전에 데이터를 변환하는 데 필요한 리소스를 제거할 수 있습니다.

SQL 풀은 BCP, SQL BulkCopy API 등 Polybase 이외 옵션을 비롯해 여러 로드 메서드를 지원하지만, 데이터를 로드하는 가장 빠르고 가장 스케일링 성능이 뛰어난 방법은 PolyBase를 사용하는 것입니다.  PolyBase는 T-SQL 언어를 통해 Azure Blob Storage 또는 Azure Data Lake Store에 저장된 외부 데이터에 액세스하는 기술입니다.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="extract-load-and-transform-elt"></a>ELT(추출, 로드 및 변환)

ELT(추출, 로드 및 변환)는 원본 시스템에서 데이터를 추출하여 데이터 웨어하우스로 로드한 다음 변환하는 프로세스입니다.

전용 SQL 풀에서 PolyBase ELT를 구현하는 기본적인 단계는 다음과 같습니다.

1. 원본 데이터를 텍스트 파일로 추출합니다.
2. Azure Blob Storage 또는 Azure Data Lake Store에 데이터를 둡니다.
3. 로드할 데이터를 준비합니다.
4. PolyBase를 사용하여 전용 SQL 풀 준비 테이블로 데이터를 로드합니다.
5. 데이터를 변환합니다.
6. 프로덕션 테이블에 데이터를 삽입합니다.

로드 자습서로는 [PolyBase를 사용하여 Azure Blob 스토리지에서 Azure Synapse Analytics로 데이터 로드](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)를 참조하세요.

자세한 정보는 [로드 패턴 블로그](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies)를 참조하세요.

## <a name="1-extract-the-source-data-into-text-files"></a>1. 텍스트 파일에 원본 데이터 추출

원본 시스템에서 데이터를 추출하는 방식은 스토리지 위치에 따라 달라집니다.  이 단계에서는 PolyBase를 지원하는 분리된 텍스트 파일로 데이터를 이동해야 합니다.

### <a name="polybase-external-file-formats"></a>PolyBase 외부 파일 형식

PolyBase는 UTF-8 및 UTF-16으로 인코딩된 구분된 텍스트 파일에서 데이터를 로드합니다. 그리고 분리된 텍스트 파일 외에 Hadoop 파일 형식 RC 파일, ORC 및 Parquet에서도 데이터를 로드합니다. 또한 Gzip 및 Snappy 압축 파일에서도 데이터를 로드할 수 있습니다. PolyBase는 현재 확장 ASCII, 고정 너비 형식 및 중첩된 형식(예: WinZip, JSON 및 XML)을 지원하지 않습니다.

SQL Server에서 내보내는 경우에는 [bcp 명령줄 도구](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true)를 사용하여 분리된 텍스트 파일로 데이터를 내보낼 수 있습니다. Parquet에서 Azure Synapse Analytics로의 데이터 형식 매핑은 다음과 같습니다.

| **Parquet 데이터 형식** |                      **SQL 데이터 형식**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        boolean        |                             bit                              |
|        double         |                            float                             |
|         float         |                             real                             |
|        double         |                            money                             |
|        double         |                          smallmoney                          |
|        문자열         |                            nchar                             |
|        문자열         |                           nvarchar                           |
|        문자열         |                             char                             |
|        문자열         |                           varchar                            |
|        binary         |                            binary                            |
|        binary         |                          varbinary                           |
|       timestamp       |                             date                             |
|       timestamp       |                        smalldatetime                         |
|       timestamp       |                          datetime2                           |
|       timestamp       |                           Datetime                           |
|       timestamp       |                             time                             |
|       date            |                             date                             |
|        decimal        |                            decimal                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Azure Blob Storage 또는 Azure Data Lake Store에 데이터 저장

Azure 스토리지에 데이터를 두려면 [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md) 또는 [Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md)로 이동할 수 있습니다. 어느 위치에서든 데이터를 텍스트 파일에 저장해야 합니다. Polybase는 두 위치 중 하나에서 데이터를 로드할 수 있습니다.

데이터를 Azure Storage로 이동하는 데 사용할 수 있는 도구와 서비스는 다음과 같습니다.

- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) 서비스는 네트워크 처리량, 성능 및 예측 가능성을 개선합니다. ExpressRoute는 Azure에 대한 전용 프라이빗 연결을 통해 데이터를 라우팅하는 서비스입니다. ExpressRoute 연결은 공용 인터넷을 통해 데이터를 라우팅하지 않습니다. 이 연결은 공용 인터넷을 통한 일반적인 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧고 보안성이 높습니다.
- [AzCopy 유틸리티](../../storage/common/storage-use-azcopy-v10.md)는 공용 인터넷을 통해 Azure Storage로 데이터를 이동합니다. 이는 데이터 크기가 10TB 미만인 경우에 작동합니다. AzCopy를 사용하여 정기적으로 로드를 수행하려면 네트워크 속도를 테스트하여 작업이 가능한지 확인하세요.
- [ADF(Azure Data Factory)](../../data-factory/introduction.md)에는 로컬 서버에 설치할 수 있는 게이트웨이가 있습니다. 그런 다음 파이프라인을 만들어 로컬 서버에서 Azure Storage로 데이터를 이동할 수 있습니다. 전용 SQL 풀과 함께 Data Factory를 사용하려면 [전용 SQL 풀로 데이터 로드](../../data-factory/load-azure-sql-data-warehouse.md)를 참조하세요.

## <a name="3-prepare-the-data-for-loading"></a>3. 로드할 데이터 준비

데이터를 전용 SQL 풀에 로드하기 전에 스토리지 계정에서 데이터를 준비하고 정리해야 할 수 있습니다. 데이터가 원본에 있는 동안, 데이터를 텍스트 파일로 내보낼 때 또는 Azure Storage로 이동한 후 데이터 준비를 수행할 수 있습니다.  가능한 경우 프로세스의 초기에 데이터로 작업하는 것이 가장 쉽습니다.  

### <a name="define-external-tables"></a>외부 테이블 정의

데이터를 로드하려면 먼저 데이터 웨어하우스에서 외부 테이블을 정의해야 합니다. PolyBase는 외부 테이블을 사용하여 Azure Storage의 데이터를 정의하고 액세스합니다. 데이터베이스 뷰와 비슷한 외부 테이블은 테이블 스키마를 포함하며 데이터 웨어하우스 외부에 저장된 데이터를 가리킵니다.

외부 테이블 정의에는 데이터 원본, 텍스트 파일 형식 및 테이블 정의를 지정하는 것이 포함됩니다. 다음은 필요한 T-SQL 구문 항목입니다.

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="format-text-files"></a>텍스트 파일 형식 지정

외부 개체가 정의되면 텍스트 파일의 행을 외부 테이블 및 파일 형식 정의와 정렬해야 합니다. 텍스트 파일의 각 행에 있는 데이터는 테이블 정의와 정렬되어야 합니다.
텍스트 파일의 형식을 지정하려면:

- 데이터가 비관계형 원본에서 오는 경우 행과 열로 변환해야 합니다. 데이터가 관계형 원본에서 오건 비관계형 원본에서 오건, 데이터를 로드하려고 하는 테이블에 대한 열 정의와 정렬되도록 데이터를 변환해야 합니다.
- SQL 풀 대상 테이블의 열 및 데이터 형식과 정렬되도록 텍스트 파일에서 데이터의 형식을 지정합니다. 외부 텍스트 파일의 데이터 형식과 데이터 웨어하우스 테이블의 데이터 형식이 정렬되지 않으면 로드 중에 행이 거부됩니다.
- 종결자로 텍스트 파일의 필드를 구분합니다.  원본 데이터에서 찾을 수 없는 문자 또는 문자 시퀀스를 사용해야 합니다. [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)으로 지정한 종결자를 사용하세요.

## <a name="4-load-the-data-into-dedicated-sql-pool-staging-tables-using-polybase"></a>4. PolyBase를 사용하여 전용 SQL 풀 준비 테이블로 데이터를 로드

데이터는 준비 테이블로 로드하는 것이 가장 좋습니다. 준비 테이블을 사용하면 프로덕션 테이블에 영향을 주지 않고 오류를 처리할 수 있습니다. 또한 준비 테이블을 사용하면 데이터를 프로덕션 테이블에 삽입하기 전에 데이터를 변환하는 데 SQL 풀에서 기본 제공하는 분산 쿼리 처리 기능을 사용해 볼 수 있습니다.

### <a name="options-for-loading-with-polybase"></a>PolyBase로 로드하기 위한 옵션

PolyBase를 사용하여 데이터를 로드하려는 경우 다음 로드 옵션 중 하나를 사용할 수 있습니다.

- [T-SQL을 이용한 PolyBase](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json): Azure Blob Storage 또는 Azure Data Lake Store에 데이터가 있을 경우 효과적입니다. 로드 프로세스를 가장 잘 제어할 수 있지만, 외부 데이터 개체를 정의해야 합니다. 다른 방법에서는 원본 테이블을 대상 테이블에 매핑할 때 배후에서 이러한 개체를 정의합니다.  T-SQL 로드를 조정하려면 Azure Data Factory, SSIS 또는 Azure 함수를 사용할 수 있습니다.
- [PolyBase와 SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)는 원본 데이터가 SQL Server에 있을 때 원활하게 작동합니다. SSIS는 대상 테이블 매핑에 대해 원본을 정의하고 로드를 조정합니다. 이미 SSIS 패키지가 있는 경우 새 데이터 웨어하우스 대상으로 작업하도록 패키지를 수정할 수 있습니다.
- [ADF(Azure Data Factory)를 이용한 PolyBase](../../data-factory/load-azure-sql-data-warehouse.md): 또 다른 오케스트레이션 도구입니다.  파이프라인을 정의하고 작업을 예약합니다.
- [PolyBase와 Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)는 PolyBase를 사용하여 Azure Synapse Analytics 테이블에서 Databricks 데이터 프레임으로 데이터를 전송하거나 Databricks 데이터 프레임에서 Azure Synapse Analytics 테이블로 데이터를 씁니다.

### <a name="non-polybase-loading-options"></a>PolyBase 외 로드 옵션

데이터가 PolyBase와 호환되지 않으면 [bcp](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) 또는 [SQLBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy)를 사용할 수 있습니다. bcp는 Azure Blob Storage를 거치지 않고도 전용 SQL 풀에 직접 로드되며, 소규모 로드에만 사용됩니다. 이러한 옵션의 로드 성능은 PolyBase보다 훨씬 느립니다.

## <a name="5-transform-the-data"></a>5. 데이터 변환

데이터가 준비 테이블에 있는 동안, 워크로드에 필요한 변환을 수행하세요. 그런 다음 데이터를 프로덕션 테이블로 이동하세요.

## <a name="6-insert-the-data-into-production-tables"></a>6. 프로덕션 테이블에 데이터 삽입

INSERT INTO ... SELECT 문은 스테이징 테이블에서 영구 테이블로 데이터를 이동합니다.

ETL 프로세스를 설계할 때 작은 테스트 샘플에서 프로세스를 실행해 보세요. 테이블에서 파일로 1000개 행을 추출하여 Azure로 이동한 다음 준비 테이블에 로드해 보세요.

## <a name="partner-loading-solutions"></a>파트너 로드 솔루션

상당수의 파트너에게는 로드 솔루션이 있습니다. 자세한 내용을 알아보려면 [솔루션 파트너](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?context=/azure/synapse-analytics/context/context) 목록을 참조하세요.

## <a name="next-steps"></a>다음 단계

설명서를 로드하려면 [데이터를 로드하기 위한 지침](data-loading-best-practices.md)을 참조하세요.
