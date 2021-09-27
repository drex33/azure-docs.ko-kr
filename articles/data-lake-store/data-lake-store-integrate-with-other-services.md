---
title: Data Lake Storage Gen1과 다른 Azure 서비스 통합
description: 기타 Azure 서비스와 Azure Data Lake Storage Gen1을 통합하는 방법을 이해합니다.
author: normesta
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: normesta
ms.openlocfilehash: a5d5c67b89d35284a4c441ac87d9ae467edb5aa5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602843"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Azure Data Lake Storage Gen1을 다른 Azure 서비스와 통합
Azure Data Lake Storage Gen1을 다른 Azure 서비스와 함께 사용하여 광범위한 시나리오를 활용할 수 있습니다. 다음 문서에는 Data Lake Storage Gen1과 통합될 수 있는 서비스가 나열되어 있습니다.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Azure HDInsight와 함께 Data Lake Storage Gen1 사용
Data Lake Storage Gen1을 사용하는 [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) 클러스터를 HDFS 규격 스토리지로 프로비전할 수 있습니다. 이 릴리스에서 Windows 및 Linux의 Hadoop 및 Storm 클러스터의 경우 Data Lake Storage Gen1을 추가 스토리지로만 사용할 수 있습니다. 이러한 클러스트는 여전히 Azure Storage(WASB)를 기본 스토리지로 사용합니다. 그러나 Windows 및 Linux의 HBase 클러스터의 경우에는 Data Lake Storage Gen1을 기본 스토리지나 추가 스토리지 또는 둘 다로 사용할 수 있습니다.

Data Lake Storage Gen1을 사용하는 HDInsight 클러스터를 프로비전하는 방법에 대한 지침은 다음을 참조하세요.

* [Azure Portal을 사용하여 Data Lake Storage Gen1을 사용하는 HDInsight 클러스터 프로비전](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Azure PowerShell을 사용하여 Data Lake Storage Gen1을 기본 스토리지로 사용하는 HDInsight 클러스터 프로비전](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Azure PowerShell을 사용하여 Data Lake Storage Gen1을 추가 스토리지로 사용하는 HDInsight 클러스터 프로비전](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Data Lake Storage Gen1을 Azure Data Lake Analytics와 함께 사용
[Azure 데이터 레이크 분석](../data-lake-analytics/data-lake-analytics-overview.md) 을 사용하면 클라우드 규모에서 빅 데이터 작업을 수행할 수 있습니다. 이를 통해 동적으로 리소스를 프로비전할 뿐만 아니라 지원되는 많은 데이터 원본(예: Data Lake Storage Gen1)에 저장될 수 있는 테라바이트 또는 심지어 엑사바이트의 데이터를 분석할 수 있습니다. Data Lake Analytics는 Data Lake Storage Gen1에서 작업할 때 특히 최적화되어 빅 데이터 워크로드에 대한 최상의 성능, 처리량 및 병렬화를 제공합니다.

Data Lake Storage Gen1과 함께 Data Lake Analytics를 사용하는 방법에 대한 지침은 [Data Lake Storage Gen1을 사용하여 Data Lake Analytics 시작](../data-lake-analytics/data-lake-analytics-get-started-portal.md)을 참조하세요.

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Azure Data Factory와 함께 Data Lake Storage Gen1 사용
[Azure Data Factory](https://azure.microsoft.com/services/data-factory/)를 사용하여 Azure 테이블, Azure SQL Database, Azure SQL Data Warehouse, Azure Storage Blob 및 온-프레미스 데이터베이스에서 데이터를 수집할 수 있습니다. Azure 에코시스템의 첫 번째 클래스가 된 Azure Data Factory를 사용하면 이러한 원본에서 Data Lake Storage Gen1로 데이터를 수집하는 작업을 오케스트레이션할 수 있습니다.

Data Lake Storage Gen1과 함께 Azure Data Factory를 사용하는 방법에 대한 지침은 [Data Factory를 사용하여 Data Lake Storage Gen1에서 데이터 이동](../data-factory/connector-azure-data-lake-store.md)을 참조하세요.

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Azure Storage Blob에서 Data Lake Storage Gen1로 데이터 복사
Azure Data Lake Storage Gen1에서는 Azure Blob Storage에서 Data Lake Storage Gen1 계정으로 데이터를 복사할 수 있는 명령줄 도구인 AdlCopy를 제공합니다. 자세한 내용은 [Azure Storage Blob에서 Data Lake Storage Gen1로 데이터 복사](data-lake-store-copy-data-azure-storage-blob.md)를 참조하세요.

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Azure SQL Database와 Data Lake Storage Gen1 간에 데이터 복사
Apache Sqoop을 사용하여 Azure SQL Database와 Data Lake Storage Gen1 간에 데이터를 가져오고 내보낼 수 있습니다. 자세한 정보는 [Sqoop을 사용한 Data Lake Storage Gen1과 Azure SQL Database 간 데이터 복사](data-lake-store-data-transfer-sql-sqoop.md)를 참조하세요.

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Stream Analytics와 함께 Data Lake Storage Gen1 사용
Azure Stream Analytics를 사용하여 스트리밍되는 데이터를 저장할 출력 중 하나로 Data Lake Storage Gen1을 사용할 수 있습니다. 자세한 내용은 [Azure Stream Analytics를 사용하여 Azure Storage Blob에서 Data Lake Storage Gen1로 데이터 스트리밍](data-lake-store-stream-analytics.md)을 참조하세요.

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Power BI와 함께 Data Lake Storage Gen1 사용
Power BI를 사용하여 Data Lake Storage Gen1 계정에서 데이터를 가져와서 데이터를 분석하고 시각화할 수 있습니다. 자세한 내용은 [Power BI를 사용하여 Data Lake Storage Gen1의 데이터 분석](data-lake-store-power-bi.md)을 참조하세요.

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Data Catalog와 함께 Data Lake Storage Gen1 사용
Data Lake Storage Gen1의 데이터를 Azure Data Catalog에 등록하면 조직 전체에서 데이터를 검색할 수 있습니다. 자세한 내용은 [Azure Data Catalog에 Data Lake Storage Gen1의 데이터 등록](data-lake-store-with-data-catalog.md)을 참조하세요.

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>SSIS(SQL Server Integration Services)와 함께 Data Lake Storage Gen1 사용
SSIS에서 Data Lake Storage Gen1 연결 관리자를 사용하여 SSIS 패키지를 Data Lake Storage Gen1에 연결할 수 있습니다. 자세한 내용은 [SSIS와 함께 Data Lake Storage Gen1 사용](/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager)을 참조하세요.

## <a name="use-data-lake-storage-gen1-with-azure-synapse-analytics"></a>Data Lake Storage Gen1을 Azure Synapse Analytics와 함께 사용
PolyBase를 사용하여 Data Lake Storage Gen1에서 Azure Synapse Analytics로 데이터를 로드할 수 있습니다. 자세한 정보는 [Azure Synapse Analytics와 함께 Data Lake Storage Gen1 사용](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)을 참조하세요.

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Azure Event Hubs와 함께 Data Lake Storage Gen1 사용
Azure Data Lake Storage Gen1을 사용하여 Azure Event Hubs에서 받은 데이터를 보관하고 캡처할 수 있습니다. 자세한 내용은 [Azure Event Hubs와 함께 Data Lake Storage Gen1 사용](data-lake-store-archive-eventhub-capture.md)을 참조하세요.

## <a name="see-also"></a>참고 항목
* [Azure Data Lake Storage Gen1 개요](data-lake-store-overview.md)
* [포털을 사용하여 Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)
* [PowerShell을 사용하여 Data Lake Storage Gen1 시작](data-lake-store-get-started-powershell.md)