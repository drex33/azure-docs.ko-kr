---
title: Azure Data Lake Storage Gen2의 성능 최적화 | Microsoft Docs
description: 성능을 위해 Azure Data Lake Storage Gen2를 최적화하는 방법을 이해합니다. 데이터 수집, 데이터 집합 구조화, 그 외 기타.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 09/21/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3cb875f14fe3d9b18b5249a9d2cd9e1901000610
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609311"
---
# <a name="optimize-azure-data-lake-storage-gen2-for-performance"></a>성능을 위한 Azure Data Lake Storage Gen2 최적화

Azure Data Lake Storage Gen2는 I/O 집약적 분석 및 데이터 이동에 대한 높은 처리량을 지원합니다. 이 문서는 처리량 및 효율적인 데이터 액세스를 최적화 하는 데 도움이 됩니다.

> [!NOTE]
> 분석 파이프라인의 전반적인 성능은 분석 엔진과 관련 된 요인에 따라서도 달라 집니다. 워크 로드 성능 최적화에 대 한 최신 지침은 사용 하려는 각 시스템에 대 한 설명서를 참조 하세요.

## <a name="optimize-data-ingestion"></a>데이터 수집 최적화

원본 시스템의 데이터를 수집 하는 경우 원본 하드웨어, 원본 네트워크 하드웨어 또는 저장소 계정에 대 한 네트워크 연결에 병목 현상이 발생할 수 있습니다.

![원본 시스템에서 Data Lake Storage Gen2 데이터를 수집할 때 고려해야 할 요소를 보여주는 다이어그램.](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

### <a name="source-hardware"></a>원본 하드웨어

온-프레미스 컴퓨터를 사용 하 든, Azure에서 Vm (Virtual Machines)을 사용 하 든 관계 없이 적절 한 하드웨어를 신중 하 게 선택 해야 합니다. 디스크 하드웨어의 경우 SSD (반도체 드라이브)를 사용 하 고 더 빠른 스핀 들이 있는 디스크 하드웨어를 선택 하는 것이 좋습니다. 네트워크 하드웨어의 경우 가능한 한 가장 빠른 NIC (네트워크 인터페이스 컨트롤러)를 사용 합니다. Azure에서 적절 한 강력한 디스크 및 네트워킹 하드웨어를 포함 하는 Azure D14 Vm을 권장 합니다.

### <a name="network-connectivity-to-the-storage-account"></a>저장소 계정에 대 한 네트워크 연결

원본 데이터와 저장소 계정 간의 네트워크 연결에 병목 현상이 발생할 수 있습니다. 원본 데이터가 온-프레미스에 있는 경우 [Azure express](https://azure.microsoft.com/services/expressroute/)경로와 전용 링크를 사용 하는 것이 좋습니다. 원본 데이터가 azure에 있는 경우 데이터가 Data Lake Storage Gen2 사용 계정과 동일한 azure 지역에 있는 경우 성능이 가장 좋습니다.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>최대 병렬 처리를 위한 데이터 수집 도구 구성

최상의 성능을 얻으려면 가능한 한 많은 읽기와 쓰기를 병렬로 수행 하 여 사용 가능한 모든 처리량을 사용 합니다.

![Data Lake Storage Gen2 성능](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

다음 표에는 널리 사용 되는 여러 수집 도구에 대 한 주요 설정이 요약 되어 있습니다.

| 도구               | 설정 |
|--------------------|------------------------------------------------------|
| [DistCp](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)             | -m(mapper) |
| [Azure Data Factory](../../data-factory/copy-activity-performance.md) | parallelCopies    |
| [Sqoop](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)          | fs.azure.block.size, -m(mapper)   |

계정 크기를 조정 하 여 모든 분석 시나리오에 필요한 처리량을 제공할 수 있습니다. 기본적으로 사용 하도록 설정 된 Data Lake Storage Gen2 계정은 다양 한 사용 사례 범주에 대 한 요구 사항을 충족 하기 위해 기본 구성에서 충분 한 처리량을 제공 합니다. 기본 한도를 실행 하는 경우 [Azure 지원](https://azure.microsoft.com/support/faq/)에 문의 하 여 더 많은 처리량을 제공 하도록 계정을 구성할 수 있습니다.

## <a name="structure-your-data-set"></a>데이터 집합 구성

데이터가 Data Lake Storage Gen2 사용 가능한 저장소 계정에 저장 된 경우 파일 크기, 파일 수 및 폴더 구조는 성능에 영향을 줍니다.  다음 섹션에서는 이러한 영역의 모범 사례에 대해 설명합니다.

### <a name="file-size"></a>파일 크기

일반적으로 HDInsight와 같은 분석 엔진에는 나열, 액세스 확인, 다양 한 메타 데이터 작업 수행 등의 작업을 포함 하는 파일 당 오버 헤드가 있습니다. 데이터를 많은 작은 파일로 저장하는 경우 성능이 저하될 수 있습니다. 일반적으로 더 나은 성능을 얻으려면 데이터를 더 큰 크기의 파일(256MB ~ 100GB)로 구성합니다. 일부 엔진과 애플리케이션에서 100GB를 초과하는 파일을 효율적으로 처리하는 데 어려움을 겪을 수 있습니다.

때때로 작은 파일들이 많은 원시 데이터에 대해 데이터 파이프라인의 제어가 제한될 수 있습니다. 일반적으로 시스템에는 다운스트림 응용 프로그램에서 사용하기 위해 작은 파일을 더 큰 파일로 집계하는 일종의 프로세스를 갖추는 것이 좋습니다. 실시간으로 데이터를 처리 하는 경우 메시지 브로커 (예: [이벤트 허브](../../event-hubs/event-hubs-about.md) 또는 [Apache Kafka](https://kafka.apache.org/))와 함께 실시간 스트리밍 엔진 (예: [Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md) 또는 [Spark 스트리밍](https://databricks.com/glossary/what-is-spark-streaming))을 사용 하 여 데이터를 더 큰 파일로 저장할 수 있습니다.

작은 파일을 더 큰 값으로 집계할 때 다운스트림 처리를 위해 [Apache Parquet](https://parquet.apache.org/) 와 같은 읽기 최적화 형식으로 저장 하는 것이 좋습니다. Apache Parquet은 읽기 많은 분석 파이프라인에 대해 최적화 된 오픈 소스 파일 형식입니다. Parquet의 열 형식 저장소 구조를 사용 하면 관련 없는 데이터를 건너뛸 수 있습니다. 쿼리는 저장소에서 분석 엔진으로 전송할 데이터의 범위를 구체적으로 지정할 수 있기 때문에 훨씬 더 효율적입니다. 또한 열에 대 한 유사 데이터 형식이 함께 저장 되므로 Parquet는 데이터 저장소 비용을 낮출 수 있는 효율적인 데이터 압축 및 인코딩 스키마를 지원 합니다. [Azure Synapse Analytics](../../synapse-analytics/overview-what-is.md), [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) 및 [Azure Data Factory](../../data-factory/introduction.md) 와 같은 서비스에는 Parquet 파일 형식을 활용 하는 기본 기능이 있습니다.

데이터 형식에 대 한 자세한 내용은 [모범 사례 문서의 데이터 형식 섹션](data-lake-storage-best-practices.md)을 참조 하세요.

### <a name="organizing-time-series-data-in-folders"></a>폴더에 시계열 데이터 구성

Hive 작업의 경우 시계열 데이터의 파티션 정리를 통해 일부 쿼리가 데이터의 하위 집합만 읽을 수 있으므로 성능이 향상 됩니다.

시계열 데이터를 수집하는 이러한 파이프라인은 파일 및 폴더에 대해 구조적 명명을 사용하여 파일을 배치하는 경우가 많습니다. 다음은 날짜별로 구성된 데이터에서 매우 일반적으로 나타나는 예입니다.

*\DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv*

datetime 정보가 폴더 및 파일 이름 둘 다에 나타납니다.

날짜 및 시간의 일반적인 패턴은 다음과 같습니다.

*\DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv*

앞에서 설명했듯이, 더 큰 파일 크기와 각 폴더에 포함된 적절한 파일 수의 요건에 맞는 폴더 및 파일 구성을 선택해야 합니다.

다른 디렉터리 레이아웃 구조 제안 사항은 [디렉터리 구조](data-lake-storage-best-practices.md#directory-layout-considerations) 를 참조 하세요.

### <a name="access-data-efficiently-with-query-acceleration"></a>쿼리 가속을 사용 하 여 데이터에 효율적으로 액세스

쿼리 가속을 통해 애플리케이션 및 분석 프레임워크에서 지정된 작업을 수행하는 데 필요한 데이터만 검색하여 데이터 처리를 현저하게 최적화할 수 있습니다. 이렇게 하면 저장된 데이터에 대한 중요한 인사이트를 얻는 데 필요한 시간 및 처리 능력이 줄어듭니다.

쿼리 가속은 디스크에서 데이터를 읽을 때 애플리케이션이 행과 열을 필터링할 수 있도록 하는 필터링 조건자 및 열 프로젝션을 허용합니다. 조건자의 조건을 충족하는 데이터만 네트워크를 통해 애플리케이션으로 전송됩니다. 이렇게 하면 네트워크 대기 시간과 컴퓨팅 비용이 줄어듭니다.

자세히 알아보려면 [Azure Data Lake Storage 쿼리 가속](data-lake-storage-query-acceleration.md) 을 참조 하세요.

## <a name="optimize-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>HDInsight의 Hadoop 및 Spark 워크로드에 대한 I/O 집약적 작업 최적화

I/o를 많이 사용 하는 작업은 i/o를 수행 하는 데 대부분의 시간을 소비 합니다.  읽기 및 쓰기 작업만 수행하는 복사 작업이 일반적인 예입니다.  다른 예로, 많은 데이터를 읽고 일부 데이터 변환을 수행한 다음 데이터를 저장소에 다시 쓰는 데이터 준비 작업이 있습니다.  단일 작업에서 최대 100MB를 읽거나 쓰는 작업을 수행할 수 있지만 이러한 크기의 버퍼로 인해 성능이 저하될 수 있습니다. 성능을 최적화 하려면 4MB에서 16MB 사이의 i/o 작업 크기를 유지 하세요.

### <a name="io-intensive-jobs-with-hdinsight-clusters"></a>HDInsight 클러스터를 사용 하는 i/o 집약적인 작업

- **HDInsight 버전.** 최상의 성능을 얻으려면 HDInsight의 최신 릴리스를 사용합니다.
- **지역.** Data Lake Storage Gen2 계정을 HDInsight 클러스터와 동일한 지역에 배치합니다.

HDInsight 클러스터는 헤드 노드 두 개와 일부 작업자 노드로 구성됩니다. 각 작업자 노드는 VM 유형에 의해 결정되는 특정 개수의 코어와 메모리를 제공합니다.  작업을 실행할 때 YARN은 사용 가능한 메모리와 코어를 할당하여 컨테이너를 만드는 리소스 협상자입니다.  각 컨테이너는 작업을 완료하는 데 필요한 태스크를 실행합니다.  태스크를 신속하게 처리하기 위해 컨테이너가 병렬로 실행됩니다. 따라서 최대한 많은 병렬 컨테이너를 실행하여 성능을 향상합니다.

HDInsight 클러스터 내에 있는 3개의 계층을 튜닝하여 컨테이너 수를 늘리고 사용 가능한 모든 처리량을 이용할 수 있습니다.

- **물리적 계층**
- **YARN 계층**
- **워크로드 계층**

### <a name="physical-layer"></a>물리적 계층

**더 많은 노드 및/또는 더 큰 VM으로 클러스터를 실행합니다.**  더 큰 클러스터를 사용하면 아래 그림과 같이 더 많은 YARN 컨테이너를 실행할 수 있습니다.

![더 큰 클러스터를 사용하여 더 많은 YARN 컨테이너를 실행할 수 있는 방법을 보여주는 다이어그램.](./media/data-lake-storage-performance-tuning-guidance/VM.png)

**더 많은 네트워크 대역폭을 가진 VM을 사용합니다.**  네트워크 대역폭이 Data Lake Storage Gen2 처리량보다 작으면 네트워크 대역폭 크기로 인해 병목 상태가 발생할 수 있습니다.  VM마다 각기 다른 네트워크 대역폭 크기를 갖게 됩니다.  가능한 가장 큰 네트워크 대역폭을 가진 VM 유형을 선택합니다.

### <a name="yarn-layer"></a>YARN 계층

**더 작은 YARN 컨테이너를 사용합니다.**  각 YARN 컨테이너의 크기를 줄여 동일한 리소스 양으로 더 많은 컨테이너를 만듭니다.

![더 많은 컨테이너를 만들기 위해 각 YARN 컨테이너의 크기를 줄일 때의 결과를 보여주는 다이어그램.](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

워크로드에 따라 항상 필요한 최소 YARN 컨테이너 크기가 있습니다. 너무 작은 컨테이너를 선택하면 작업에서 메모리 부족 문제가 발생합니다. 일반적으로 YARN 컨테이너는 1GB 이상이어야 합니다. 3GB YARN 컨테이너도 흔히 볼 수 있습니다. 일부 워크로드의 경우 더 큰 YARN 컨테이너가 필요할 수도 있습니다.

**YARN 컨테이너당 코어 수를 늘립니다.**  각 컨테이너에 할당된 코어 수를 늘려 각 컨테이너에서 실행되는 병렬 태스크 수를 늘립니다.  이 방법은 컨테이너당 여러 태스크를 실행하는 Spark 등의 애플리케이션에 적합합니다.  각 컨테이너에서 단일 스레드를 실행하는 경우 Hive 등의 애플리케이션에서는 컨테이너당 코어 수보다 컨테이너 수를 늘리는 것이 좋습니다.

### <a name="workload-layer"></a>워크로드 계층

**사용 가능한 모든 컨테이너를 이용합니다.**  모든 리소스가 활용되도록 태스크 수를 사용 가능한 컨테이너 수보다 크거나 같도록 설정합니다.

![모든 컨테이너의 사용을 보여주는 다이어그램.](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

**실패한 태스크는 비용이 많이 듭니다.** 각 태스크에서 많은 양의 데이터를 처리하는 경우 태스크 실패 시 다시 시도하는 데 많은 비용이 듭니다.  따라서 각각 적은 양의 데이터를 처리하는 태스크를 더 많이 만드는 것이 좋습니다.

위의 일반 지침 외에도 각 분석 시스템 또는 프레임워크에는 최적의 성능을 위해 조정할 수 있는 다양한 매개 변수가 있습니다. 워크로드 성능 최적화에 대한 최신 지침은 사용하려는 각 시스템에 대한 설명서를 참조하세요.

## <a name="see-also"></a>참조

- [Azure Data Lake Storage Gen2 사용에 대한 모범 사례](data-lake-storage-best-practices.md)
- [Azure Data Lake Storage Gen2 개요](data-lake-storage-introduction.md)
