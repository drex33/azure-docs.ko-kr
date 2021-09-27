---
title: Azure Data Lake Storage Gen2와 관련된 데이터 시나리오 | Microsoft Docs
description: Data Lake Storage Gen2(이전의 Azure Data Lake Store)에서 수집, 처리, 다운로드 및 시각화될 수 있는 데이터를 사용하는 다양한 시나리오와 도구를 알아봅니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 7670430d112482addea26c9fc1bc97c2434bc2e2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555344"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>빅 데이터 요구 사항을 위한 Azure Data Lake Storage Gen2 사용

빅 데이터 처리에는 네 가지 주요 단계가 있습니다.

> [!div class="checklist"]
> - 실시간으로 또는 배치로 대량의 데이터를 데이터 저장소에 수집
> - 데이터 처리
> - 데이터 다운로드
> - 데이터 시각화

이 문서에서는 각 처리 단계에 대 한 옵션 및 도구를 중점적으로 설명 합니다.

Azure Data Lake Storage Gen2와 함께 사용할 수 있는 azure 서비스의 전체 목록은 [azure 서비스와 Azure Data Lake Storage 통합](./data-lake-storage-supported-azure-services.md) (영문)을 참조 하세요.

## <a name="ingest-the-data-into-data-lake-storage-gen2"></a>데이터를 Data Lake Storage Gen2에 수집

이 섹션에서는 다양한 데이터 원본과 해당 데이터를 Data Lake Storage Gen2 계정에 수집할 수 있는 여러 가지 방법을 중점적으로 설명합니다.

![Data Lake Storage Gen2에 데이터 수집](./media/data-lake-storage-data-scenarios/ingest-data.png "Data Lake Storage Gen2에 데이터 수집")

### <a name="ad-hoc-data"></a>임시 데이터

빅 데이터 애플리케이션의 프로토타입 제작에 사용되는 작은 데이터 집합을 나타냅니다. 데이터 원본에 따라 임시 데이터를 수집하는 여러 가지 방법이 있습니다.

다음은 임시 데이터를 수집하는 데 사용할 수 있는 도구 목록입니다.

| 데이터 원본 | 로컬 컴퓨터를 사용하여 |
| --- | --- |
| 수집 |[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)<br><br>[Azure CLI](data-lake-storage-directory-file-acl-cli.md)<br><br>[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[AzCopy 도구](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[AzCopy 도구](../common/storage-use-azcopy-v10.md)<br><br>[HDInsight 클러스터에서 실행되는 DistCp](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>스트리밍된 데이터

응용 프로그램, 장치, 센서 등의 다양 한 소스에서 생성할 수 있는 데이터를 나타냅니다. 이 데이터는 다양 한 도구를 통해 Data Lake Storage Gen2 수집 수 있습니다. 이러한 도구는 일반적으로 데이터를 이벤트별로 실시간으로 캡처하고 처리한 다음, 이벤트를 추가로 처리할 수 있도록 해당 이벤트를 Data Lake Storage Gen2에 일괄적으로 씁니다.

다음은 스트리밍된 데이터를 수집하는 데 사용할 수 있는 도구 목록입니다.

|도구 | 지침 |
|---|--|
|Azure Stream Analytics|[빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](../../stream-analytics/stream-analytics-quick-create-portal.md) <br> [Azure Data Lake Gen2로 송신](../../stream-analytics/stream-analytics-define-outputs.md)|
|Azure HDInsight Storm | [HDInsight의 Apache Storm에서 Apache Hadoop HDFS에 쓰기](../../hdinsight/storm/apache-storm-write-data-lake-store.md) |

### <a name="relational-data"></a>관계형 데이터

관계형 데이터베이스의 데이터를 원본으로 사용할 수도 있습니다. 관계형 데이터베이스는 일정 기간 동안 엄청난 양의 데이터를 수집합니다. 이 데이터를 빅 데이터 파이프라인을 통해 처리하면 중요한 정보를 얻을 수 있습니다. 다음 도구를 사용하여 이러한 데이터를 Data Lake Storage Gen2로 이동할 수 있습니다.

다음은 관계형 데이터를 수집하는 데 사용할 수 있는 도구 목록입니다.

|도구 | 지침 |
|---|--|
|Azure 데이터 팩터리 | [Azure Data Factory의 복사 작업](../../data-factory/copy-activity-overview.md) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>웹 서버 로그 데이터(사용자 지정 애플리케이션을 사용하여 업로드)

웹 서버 로그 데이터의 분석은 빅 데이터 애플리케이션에 대한 일반적인 사용 사례이며 대용량 로그 파일을 Data Lake Storage Gen2에 업로드해야 하므로 이러한 유형의 데이터 세트가 특별히 호출됩니다. 다음 중 아무 도구를 사용하여 이러한 데이터를 업로드하는 고유의 스크립트 또는 애플리케이션을 작성할 수 있습니다.

다음은 웹 서버 로그 데이터를 수집하는 데 사용할 수 있는 도구 목록입니다.

|도구 | 지침 |
|---|--|
|Azure 데이터 팩터리 | [Azure Data Factory의 복사 작업](../../data-factory/copy-activity-overview.md)  |
|Azure CLI|[Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|Azure PowerShell|[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)|

웹 서버 로그 데이터를 업로드하고 다른 종류의 데이터 (예: 소셜 정서 데이터)를 업로드하려는 경우 고유의 사용자 지정 스크립트/애플리케이션을 작성하는 것이 좋습니다. 데이터 업로드 구성 요소를 더 큰 빅 데이터 애플리케이션의 일부로 유연하게 포함할 수 있기 때문입니다. 어떤 경우에는 이 코드가 스크립트 형태 또는 간단한 명령줄 유틸리티의 형태를 취할 수 있습니다. 또 어떤 경우에는 빅 데이터 처리를 비즈니스 애플리케이션 또는 솔루션에 통합하는 데 이 코드가 사용될 수 있습니다.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터와 연결된 데이터

대부분의 HDInsight 클러스터 유형(Hadoop, HBase, Storm)은 Data Lake Storage Gen2를 데이터 스토리지 리포지토리로 지원합니다. HDInsight 클러스터는 Azure Storage Blob(WASB)에서 데이터에 액세스합니다. WASB의 데이터를 클러스터와 연결된 Data Lake Storage Gen2 계정에 복사하면 성능을 향상시킬 수 있습니다. 다음 도구를 사용하여 데이터를 복사할 수 있습니다.

다음은 HDInsight 클러스터와 연결된 데이터를 수집하는 데 사용할 수 있는 도구 목록입니다.

|도구 | 지침 |
|---|--|
|Apache DistCp | [DistCp를 사용하여 Azure Storage Blob과 Azure Data Lake Storage Gen2 간에 데이터 복사](./data-lake-storage-use-distcp.md) |
|AzCopy 도구 | [AzCopy를 사용하여 데이터 전송](../common/storage-use-azcopy-v10.md) |
|Azure 데이터 팩터리 | [Azure Data Factory를 사용 하 여 Azure Data Lake Storage Gen2 간에 데이터 복사](../../data-factory/load-azure-data-lake-storage-gen2.md) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>온-프레미스 또는 IaaS Hadoop 클러스터에 저장된 데이터

HDFS를 사용하여 로컬 컴퓨터의 기존 Hadoop 클러스터에 대량의 데이터를 저장할 수 있습니다. Hadoop 클러스터는 온-프레미스 배포 또는 Azure의 IaaS 클러스터에 있을 수 있습니다. 이러한 데이터를 Azure Data Lake Storage Gen2에 일회성 또는 반복적인 방식으로 복사해야 할 수도 있습니다. 이 작업을 수행하는 방법은 다양한 옵션이 있습니다. 다음은 이를 대체할 수 있는 방법 목록과 관련 절충 사항입니다.

| 접근 방식 | 세부 정보 | 장점 | 고려 사항 |
| --- | --- | --- | --- |
| ADF(Azure Data Factory)를 사용하여 데이터를 Hadoop 클러스터에서 Azure Data Lake Storage Gen2로 직접 복사합니다. |[ADF는 데이터 원본으로 HDFS 지원](../../data-factory/connector-hdfs.md) |ADF는 HDFS에 대한 기본 지원과 일등급 엔드투엔드 관리 및 모니터링을 제공합니다. |온-프레미스 또는 IaaS 클러스터에 배포하려면 데이터 관리 게이트웨이가 필요합니다. |
| Distcp를 사용하여 Hadoop에서 Azure Storage로 데이터를 복사합니다. 그런 다음, 적절한 메커니즘을 사용하여 데이터를 Azure Storage에서 Data Lake Storage Gen2로 복사합니다. |데이터를 Azure Storage에서 Data Lake Storage Gen2에 복사하는 데 사용할 수 있는 도구는 다음과 같습니다. <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[AzCopy 도구](../common/storage-use-azcopy-v10.md)</li><li>[HDInsight 클러스터에서 실행되는 Apache DistCp](data-lake-storage-use-distcp.md)</li></ul> |오픈 소스 도구를 사용할 수 있습니다. |여러 기술을 사용하는 다단계 절차입니다. |

### <a name="really-large-datasets"></a>매우 큰 데이터 세트

용량이 수 테라바이트에 달하는 데이터 세트를 업로드하는 경우 위에서 설명한 방법을 사용하면 속도가 느리고 비용이 많이 들 수 있습니다. 이러한 경우 Azure ExpressRoute를 사용할 수 있습니다.

Azure ExpressRoute를 사용하면 온-프레미스의 인프라와 Azure 데이터 센터 사이에 프라이빗 연결을 만들 수 있습니다. 이렇게 하면 대용량 데이터를 안전하게 전송할 수 있습니다. 자세한 내용은 [Azure ExpressRoute 설명서](../../expressroute/expressroute-introduction.md)를 참조하세요.

## <a name="process-the-data"></a>데이터 처리

Data Lake Storage Gen2의 데이터를 사용할 수 있게 되면 지원되는 빅 데이터 애플리케이션을 사용하여 해당 데이터에 대한 분석을 실행할 수 있습니다.

![Data Lake Storage Gen2에서 데이터 분석](./media/data-lake-storage-data-scenarios/analyze-data.png "Data Lake Storage Gen2에서 데이터 분석")

다음은 Data Lake Storage Gen2에 저장된 데이터에 대한 데이터 분석 작업을 실행하는 데 사용할 수 있는 도구 목록입니다.

|도구 | 지침 |
|---|--|
|Azure HDInsight | [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) |
|Azure Databricks | [Azure Data Lake Storage Gen2](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)<br><br>[빠른 시작: Azure Databricks 사용하여 Azure Data Lake Storage Gen2의 데이터 분석](./data-lake-storage-use-databricks-spark.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[자습서: Azure Databricks를 사용하여 데이터 추출, 변환 및 로드](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>데이터 시각화

Power BI 커넥터를 사용하여 Data Lake Storage Gen2에 저장된 데이터의 시각적 표현을 만듭니다. Power BI [사용하여 Azure Data Lake Storage Gen2에서 데이터 분석을](/power-query/connectors/datalakestorage)참조하세요.

## <a name="download-the-data"></a>데이터 다운로드

다음과 같은 시나리오를 위해 Azure Data Lake Storage Gen2에서 데이터를 다운로드하거나 이동할 수도 있습니다.

- 기존 데이터 처리 파이프라인과 상호 작용하기 위해 다른 리포지토리로 데이터를 이동합니다. 예를 들어 Data Lake Storage Gen2에서 Azure SQL Database 또는 SQL Server 인스턴스로 데이터를 이동할 수 있습니다.

- 애플리케이션 프로토타입을 빌드하는 동안 IDE 환경에서 데이터를 처리하기 위해 로컬 컴퓨터에 데이터를 다운로드 합니다.

![Data Lake Storage Gen2에서 데이터 Egress](./media/data-lake-storage-data-scenarios/egress-data.png "Data Lake Storage Gen2에서 데이터 Egress")

다음은 Data Lake Storage Gen2에서 데이터를 다운로드하는 데 사용할 수 있는 도구 목록입니다.

|도구 | 지침 |
|---|--|
|Azure 데이터 팩터리 | [Azure Data Factory의 복사 작업](../../data-factory/copy-activity-overview.md) |
|Apache DistCp | [DistCp를 사용하여 Azure Storage Blob과 Azure Data Lake Storage Gen2 간에 데이터 복사](./data-lake-storage-use-distcp.md) |
|Azure Storage Explorer|[Azure Storage 탐색기를 사용하여 Azure Data Lake Storage Gen2에서 디렉터리, 파일 및 ACL 관리](data-lake-storage-explorer.md)|
|AzCopy 도구|[AzCopy 및 Blob Storage를 사용하여 데이터 전송](../common/storage-use-azcopy-v10.md#transfer-data)|
