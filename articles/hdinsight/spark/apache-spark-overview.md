---
title: Apache Spark란 - Azure HDInsight
description: 이 문서에서는 HDInsight의 Spark를 소개하고 HDInsight에서 Spark 클러스터를 사용할 수 있는 다양한 시나리오를 제공합니다.
ms.service: hdinsight
ms.custom: contperf-fy21q1
ms.topic: overview
ms.date: 11/17/2020
ms.openlocfilehash: c680a1079813b3963ec81d98e87c293054e35e3b
ms.sourcegitcommit: 7e5bfb91cc0a4f94fbe0399f900b0fc7cb59e009
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2021
ms.locfileid: "133187500"
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>Azure HDInsight에서 Apache Spark란

Apache Spark는 메모리 내 처리를 지원하여 빅 데이터 분석 애플리케이션의 성능을 향상하는 병렬 처리 프레임워크입니다. Azure HDInsight의 Apache Spark는 Microsoft가 Apache Spark를 클라우드에 구현한 것이며 Azure의 여러 Spark 제품 중 하나입니다.

* Azure HDInsight의 Apache Spark를 사용하면 Spark 클러스터를 쉽게 만들고 구성할 수 있으므로 전체 Spark 환경을 Azure 내에서 사용자 지정하고 사용할 수 있습니다.

* [Azure Synapse Analytics의 Spark 풀](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)에는 관리형 Spark 풀이 사용되며 Azure 내에서 분석 인사이트를 위해 데이터를 로드, 모델링, 처리 및 배포할 수 있습니다.

* [Azure Databricks의 Apache Spark](/azure/databricks/getting-started/spark)는 Spark 클러스터를 사용하여 사용자 간의 협업을 통해 여러 데이터 원본에서 데이터를 읽고 혁신적인 인사이트로 전환할 수 있는 대화형 작업 영역을 제공합니다.

* [Azure Data Factory의 Spark 활동](../../data-factory/transform-data-using-spark.md)을 사용하면 주문형 또는 기존 Spark 클러스터를 사용하여 데이터 파이프라인에서 Spark 분석을 사용할 수 있습니다.


Azure HDInsight의 Apache Spark를 사용하면 Azure 내에서 데이터를 모두 저장하고 처리할 수 있습니다. HDInsight의 Spark 클러스터는 [Azure Blob Storage](../../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md) 또는 [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md)와 호환되므로 기존 데이터 저장소에 Spark 처리를 적용할 수 있습니다.

:::image type="content" source="./media/apache-spark-overview/hdinsight-spark-overview.svg" alt-text="Spark: 통합된 프레임워크" lightbox="./media/apache-spark-overview/hdinsight-spark-overview.svg":::

Azure HDInsight에서 Apache Spark를 시작하려면 [HDInsight Spark 클러스터를 만드는 자습서](apache-spark-jupyter-spark-sql-use-portal.md)를 참조하세요.

Apache Spark에 대한 자세한 내용 및 Azure와 상호 작용하는 방법을 알아보려면 아래 문서를 계속 읽어보세요.

구성 요소 및 버전 관리 정보를 보려면 [Azure HDInsight의 Apache Hadoop 구성 요소 및 버전](../hdinsight-component-versioning.md)을 참조하세요.

## <a name="what-is-apache-spark"></a>Apache Spark란?

Spark는 메모리 내 클러스터 컴퓨팅을 위한 기본 형식을 제공합니다. Spark 작업은 메모리로 데이터를 로드하고 캐시하여 반복적으로 쿼리할 수 있습니다. 메모리 내 컴퓨팅은 HDFS(Hadoop 분산 파일 시스템)를 통해 데이터를 공유하는 Hadoop 같은 디스크 기반 애플리케이션보다 훨씬 빠릅니다. 또한 Spark는 지역 컬렉션과 같이 분산 데이터 집합을 조작할 수 있도록 Scala 프로그래밍 언어로 통합합니다. 매핑 및 reduce 작업으로 모든 것을 구조화하지 않아도 됩니다.

:::image type="content" source="./media/apache-spark-overview/map-reduce-vs-spark.svg" alt-text="기존 MapReduce와 Spark" lightbox="./media/apache-spark-overview/map-reduce-vs-spark.svg":::

HDInsight에서 Spark 클러스터는 완벽하게 관리되는 Spark 서비스를 제공합니다. HDInsight에서 Spark 클러스터를 만드는 이점은 다음과 같습니다.

| 기능 | Description |
| --- | --- |
| 쉽게 만들기 |Azure Portal, Azure PowerShell 또는 HDInsight .NET SDK를 사용하여 몇 분 만에 HDInsight에서 새 Spark 클러스터를 만들 수 있습니다. [HDInsight에서 Apache Spark 클러스터 시작](apache-spark-jupyter-spark-sql-use-portal.md)을 참조하세요. |
| 사용 편의성 |HDInsight의 Spark 클러스터에는 Jupyter Notebooks 및 Apache Zeppelin Notebooks가 포함되어 있습니다. 이러한 노트북을 대화형 데이터 처리 및 시각화에 사용할 수 있습니다. [Apache Spark에서 Apache Zeppelin Notebook 사용](apache-spark-zeppelin-notebook.md) 및 [Apache Spark 클러스터에서 데이터 로드 및 쿼리 실행](apache-spark-load-data-run-query.md)을 참조하세요.|
| REST API |HDInsight의 Spark 클러스터에는 원격으로 작업을 제출하고 모니터링하는 REST API 기반 Spark 작업 서버인 [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)가 포함되어 있습니다. [Apache Spark REST API를 사용하여 HDInsight Spark 클러스터에 원격 작업 제출](apache-spark-livy-rest-interface.md)을 참조하세요.|
| Azure Storage 지원 | HDInsight의 Spark 클러스터는 Azure Data Lake Storage Gen1/Gen2를 기본 스토리지 또는 추가 스토리지로 모두 사용할 수 있습니다. Data Lake Storage Gen1에 대한 자세한 내용은 [Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md)을 참조하세요. Data Lake Storage Gen2에 대한 자세한 내용은 [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md)를 참조하세요.|
| Azure 서비스와의 통합 |HDInsight에서 Spark 클러스터는 Azure Event Hubs에 대한 커넥터와 함께 제공됩니다. Event Hubs를 사용하여 스트리밍 애플리케이션을 빌드할 수 있습니다. 여기에는 Spark의 일부로 이미 제공되는 Apache Kafka가 포함됩니다. |
| 타사 IDE와의 통합 | HDInsight는 유용한 여러 IDE 플러그인을 제공하여 애플리케이션을 만들고 HDInsight Spark 클러스터에 제출합니다. 자세한 내용은 [Azure Toolkit for IntelliJ IDEA 사용](apache-spark-intellij-tool-plugin.md), [VSCode용 Spark & Hive Tools 사용](../hdinsight-for-vscode.md) 및 [Azure Toolkit for Eclipse 사용](apache-spark-eclipse-tool-plugin.md)을 참조하세요.|
| 동시 쿼리 |HDInsight의 Spark 클러스터는 동시 쿼리를 지원합니다. 이 기능을 통해 한 사용자의 여러 개 쿼리 또는 여러 사용자 및 애플리케이션의 여러 개 쿼리에서 동일한 클러스터 리소스를 공유할 수 있습니다. |
| SSD에서 캐시 |클러스터 노드에 연결된 메모리 또는 SSD에서 데이터를 캐시하도록 선택할 수 있습니다. 메모리에서 캐시하면 최고의 쿼리 성능을 제공하지만 리소스가 많이 들 수 있습니다. SSD에서 캐시하면 메모리에서 전체 데이터 세트에 맞게 필요한 크기의 클러스터를 만들 필요 없이 쿼리 성능 향상을 위한 훌륭한 옵션을 제공합니다. [Azure HDInsight IO 캐시를 사용하여 Apache Spark 워크로드의 성능 향상](apache-spark-improve-performance-iocache.md)을 참조하세요. |
| BI 도구와의 통합 |HDInsight에서 Spark 클러스터는 데이터 분석을 위해 Power BI와 같은 BI 도구용 커넥터를 제공합니다. |
| 미리 로드된 Anaconda 라이브러리 |HDInsight에서 Spark 클러스터는 미리 설치된 Anaconda 라이브러리와 함께 제공됩니다. [Anaconda](https://docs.continuum.io/anaconda/)는 기계 학습, 데이터 분석, 시각화 등을 위해 약 200개의 라이브러리를 제공합니다. |
| 적응성 | HDInsight를 사용하면 자동 크기 조정 기능을 사용하여 클러스터 노드 수를 동적으로 변경할 수 있습니다. [Azure HDInsight 클러스터 자동 크기 조정](../hdinsight-autoscale-clusters.md)을 참조하세요. 또한 모든 데이터가 Azure Blob 스토리지, [Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md) 또는 [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md)에 저장되므로 데이터 손실 없이 Spark 클러스터를 삭제할 수 있습니다. |
| SLA |HDInsight의 Spark 클러스터는 언제나 지원되며 99.9% 가동 시간 SLA와 함께 제공됩니다. |

HDInsight의 Apache Spark 클러스터에는 기본적으로 클러스터에서 사용할 수 있는 다음 구성 요소가 포함되어 있습니다.

* [Spark Core](https://spark.apache.org/docs/latest/). Spark Core, Spark SQL, Spark 스트리밍 API, GraphX 및 MLlib가 포함됩니다.
* [Anaconda](https://docs.continuum.io/anaconda/)
* [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter Notebook](https://jupyter.org)
* [Apache Zeppelin Notebook](http://zeppelin-project.org/)

HDInsight Spark는 Microsoft Power BI와 같은 BI 도구에서 연결하는 데 사용되는 [ODBC 드라이버](/sql/connect/odbc/download-odbc-driver-for-sql-server)를 클러스터링합니다.

## <a name="spark-cluster-architecture"></a>Spark 클러스터 아키텍처

:::image type="content" source="./media/apache-spark-overview/hdi-spark-architecture.svg" alt-text="HDInsight Spark의 아키텍처" lightbox="./media/apache-spark-overview/hdi-spark-architecture.svg":::

Spark가 HDInsight 클러스터에서 실행되는 방식을 이해하면 Spark의 구성 요소를 이해하는 것이 쉬워집니다.

Spark 애플리케이션은 클러스터의 독립적인 프로세스 세트로 실행됩니다. 주 프로그램(드라이버 프로그램이라고 함)의 SparkContext 개체에 의해 조정됩니다.

SparkContext는 애플리케이션 간에 리소스를 제공하는 여러 유형의 클러스터 관리자에 연결할 수 있습니다. 이러한 클러스터 관리자에는 Apache Mesos, Apache Hadoop YARN 또는 Spark 클러스터 관리자가 포함됩니다. HDInsight에서 Spark는 YARN 클러스터 관리자를 사용하여 실행합니다. 일단 연결되면 Spark는 클러스터의 작업자 노드에서 실행기를 얻습니다. 이 실행기는 계산을 실행하고 애플리케이션의 데이터를 저장하는 프로세스입니다. 그런 다음, 애플리케이션 코드(SparkContext에 전달된 JAR 또는 Python 파일에 의해 정의됨)를 실행기에 보냅니다. 마지막으로, SparkContext는 실행할 실행기로 작업을 전송합니다.

SparkContext는 사용자의 주 함수를 실행하고 작업자 노드에서 다양한 병렬 작업을 실행합니다. 그런 다음, SparkContext는 작업 결과를 수집합니다. 작업자 노드는 Hadoop 분산 파일 시스템 간에 데이터를 읽고 씁니다. 또한 작업자 노드는 RDD(Resilient Distributed Datasets)로 메모리 내 변환된 데이터를 캐시합니다.

SparkContext는 Spark 마스터에 연결되며 애플리케이션을 개별 작업의 방향성 그래프(DAG)로 변환하는 작업을 담당합니다. 작업자 노드의 실행기 프로세스 내에서 실행되는 작업입니다. 각 애플리케이션은 자체 실행기 프로세스를 가져옵니다. 전체 애플리케이션 동안 유지되고 여러 스레드에서 작업을 실행합니다.

## <a name="spark-in-hdinsight-use-cases"></a>HDInsight의 Spark 사용 사례

HDInsight의 Spark 클러스터는 다음과 같은 주요 시나리오를 사용합니다.

### <a name="interactive-data-analysis-and-bi"></a>대화형 데이터 분석 및 BI

HDInsight의 Apache Spark는 Azure Blob Storage, Azure Data Lake Gen1 또는 Azure Data Lake Storage Gen2에 데이터를 저장합니다. 비즈니스 전문가 및 주요 의사 결정권자는 해당 데이터에 대한 보고서를 분석하고 작성할 수 있습니다. Microsoft Power BI를 사용하여 분석된 데이터로 대화형 보고서를 작성할 수 있습니다. 분석자는 클러스터 스토리지의 비구조적/반구조적 데이터부터 시작하여 노트북으로 데이터에 대한 스키마를 정의한 다음 Microsoft Power BI를 사용하여 데이터 모델을 작성할 수 있습니다. 또한 HDInsight의 Spark 클러스터는 많은 타사 BI 도구를 지원합니다. 예를 들어 데이터 분석가, 비즈니스 전문가 및 주요 의사 결정권자가 더 쉽게 사용할 수 있는 Tableau가 있습니다.

* [자습서: Power BI를 사용하여 Spark 데이터 시각화](apache-spark-use-bi-tools.md)

### <a name="spark-machine-learning"></a>Spark Machine Learning

Apache Spark는 [MLlib](https://spark.apache.org/mllib/)와 함께 제공됩니다. MLlib는 HDInsight의 Spark 클러스터에서 사용할 수 있는 Spark를 기반으로 빌드된 기계 학습 라이브러리입니다. HDInsight의 Spark 클러스터에는 기계 학습을 위한 다양한 패키지가 포함된 Python 배포인 Anaconda도 있습니다. 그리고 Jupyter 및 Zeppelin Notebook에 대한 지원이 기본 제공되므로 기계 학습 애플리케이션 개발 환경을 갖출 수 있습니다.

* [자습서: HVAC 데이터를 사용하여 건물 온도 예측](apache-spark-ipython-notebook-machine-learning.md)  
* [자습서: 식품 검사 결과 예측](apache-spark-machine-learning-mllib-ipython.md)

### <a name="spark-streaming-and-real-time-data-analysis"></a>Spark 스트리밍 및 실시간 데이터 분석

HDInsight의 Spark 클러스터는 실시간 분석 솔루션을 빌드하기 위한 풍부한 지원을 제공합니다. Spark에는 Kafka, Flume, Twitter, ZeroMQ 또는 TCP 소켓과 같은 다양한 원본에서 데이터를 수집하는 커넥터가 이미 있습니다. HDInsight의 Spark는 Azure Event Hubs의 데이터 수집에 대한 최고 수준의 지원을 추가합니다. Event Hubs는 Azure에서 가장 널리 사용되는 큐 서비스입니다. Event Hubs에 대한 완벽한 지원이 가능하므로 HDInsight의 Spark 클러스터는 실시간 분석 파이프라인을 빌드하기 위한 이상적인 플랫폼이 됩니다.

* [Apache Spark Streaming 개요](apache-spark-streaming-overview.md)
* [Apache Spark 구조적 스트리밍 개요](apache-spark-structured-streaming-overview.md)

## <a name="next-steps"></a>다음 단계

이 개요에서는 Azure HDInsight의 Apache Spark에 대한 기본 지식을 알아보았습니다.  다음 문서를 사용하여 HDInsight의 Apache Spark에 대해 자세히 알아보고, HDInsight Spark 클러스터를 만들고, 몇 가지 샘플 Spark 쿼리를 추가로 실행할 수 있습니다.

* [빠른 시작: HDInsight에서 Apache Spark 클러스터 만들기 및 Jupyter를 사용하여 대화형 쿼리 실행](./apache-spark-jupyter-spark-sql-use-portal.md)
* [자습서: Jupyter를 사용하여 Apache Spark 작업에서 데이터 로드 및 쿼리 실행](./apache-spark-load-data-run-query.md)
* [자습서: Power BI를 사용하여 Spark 데이터 시각화](apache-spark-use-bi-tools.md)
* [자습서: HVAC 데이터를 사용하여 건물 온도 예측](apache-spark-ipython-notebook-machine-learning.md)
* [성능을 위한 Spark 작업 최적화](apache-spark-perf.md)
