---
title: HDInsight 4.0 Apache Spark에 Azure HDInsight 3.6 Apache Storm 마이그레이션
description: Apache Storm 워크로드를 Spark Streaming 또는 Spark Structured Streaming으로 마이그레이션할 때의 차이점과 마이그레이션 흐름
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/16/2019
ms.openlocfilehash: c4f4156f80fac0c9e5eaae360aa937544d88aa9e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656800"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>HDInsight 4.0 Apache Spark에 Azure HDInsight 3.6 Apache Storm 마이그레이션

이 문서에서는 HDInsight 3.6의 Apache Storm 워크로드를 HDInsight 4.0으로 마이그레이션하는 방법에 대해 설명합니다. HDInsight 4.0은 Apache Storm 클러스터 유형을 지원하지 않으므로 다른 스트리밍 데이터 플랫폼으로 마이그레이션해야 합니다. 두 가지 적절한 옵션은 Apache Spark Streaming과 Spark Structured Streaming입니다. 이 문서에서는 이러한 플랫폼 간의 차이점을 설명하고 Apache Storm 워크로드를 마이그레이션하기 위한 워크플로를 권장합니다.

## <a name="storm-migration-paths-in-hdinsight"></a>HDInsight의 스톰 마이그레이션 경로

HDInsight 3.6의 Apache Storm에서 마이그레이션하려면 다음과 같이 여러 가지 옵션을 사용할 수 있습니다.

* HDInsight 4.0의 Spark Streaming
* HDInsight 4.0의 Spark Structured Streaming
* Azure Stream Analytics

이 문서에서는 Apache Storm에서 Spark Streaming 및 Spark Structured Streaming으로 마이그레이션하는 방법에 대한 지침을 제공합니다.

:::image type="content" source="./media/migrate-storm-to-spark/storm-migration-path.png" alt-text="HDInsight Storm 마이그레이션 경로" border="false":::

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Apache Storm과 Spark Streaming, Spark Structured Streaming 간의 비교

Apache Storm은 다양한 수준의 보장된 메시지 처리를 제공할 수 있습니다. 예를 들어 기본적인 Storm 애플리케이션은 최소한 한 번 처리를 보장할 수 있고, [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html)는 정확히 한 번 처리를 보장할 수 있습니다. Spark Streaming과 Spark Structured Streaming은 노드 실패가 발생하는 경우에도 입력 이벤트가 정확히 한 번 처리되도록 보장합니다. Storm에는 각 단일 이벤트를 처리하는 모델이 있으며 Trident에서 Micro Batch 모델을 사용할 수도 있습니다. Spark Streaming 및 Spark Structured Streaming은 Micro-Batch 처리 모델을 제공합니다.

|  |Storm |Spark 스트리밍 | Spark Structured Streaming|
|---|---|---|---|
|**이벤트 처리 보장**|최소 한 번 <br> 정확히 한 번(Trident) |[정확히 한 번](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[정확히 한 번](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**처리 모델**|실시간 <br> Micro Batch(Trident) |Micro Batch |Micro Batch |
|**이벤트 시간 지원**|[예](https://storm.apache.org/releases/2.0.0/Windowing.html)|아니요|[예](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**언어**|Java 등|Scala, Java, Python|Python, R, Scala, Java, SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Spark Streaming vs Spark Structured Streaming

Spark Structured Streaming은 Spark Streaming(DStreams)을 대체합니다. Structured Streaming은 향상 및 유지 관리를 계속해서 받고 있지만, DStreams은 유지 관리 모드에서만 그러합니다. Structured Streaming은 즉시 지원되는 원본 및 싱크에 대한 DStreams처럼 많은 기능을 지니지 않으므로, 요구 사항을 평가하여 적절한 Spark Stream 처리 옵션을 선택해야 합니다.

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>Streaming(단일 이벤트) 처리 vs Micro-Batch 처리

Storm은 각 단일 이벤트를 처리하는 모델을 제공합니다. 즉, 들어오는 모든 레코드는 도착하는 즉시 처리됩니다. Spark Streaming 애플리케이션은 처리를 위해 해당 일괄 처리를 보내기 전에 이벤트의 각 '마이크로 일괄 처리'를 수집하기 위해 잠시 기다려야 합니다. 반면, 이벤트 기반 애플리케이션은 각 이벤트를 즉시 처리합니다. Spark 스트리밍 대기 시간은 일반적으로 몇 초 이하입니다. 마이크로 일괄 처리 방법의 이점은 보다 효율적인 데이터 처리 및 간단한 집계 계산입니다.

:::image type="content" source="./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png" alt-text="Streaming 및 Micro-Batch 처리" border="false":::

## <a name="storm-architecture-and-components"></a>Storm 아키텍처 및 구성 요소

Storm 토폴로지는 DAG(방향성 비순환 그래프)에서 정렬된 여러 구성 요소로 구성됩니다. 그래프의 구성 요소 간에 데이터가 흐릅니다. 각 구성 요소는 하나 이상의 데이터 스트림을 사용하며, 선택적으로 하나 이상의 스트림을 내보낼 수 있습니다.

|구성 요소 |Description |
|---|---|
|Spout|데이터를 토폴로지로 가져옵니다. 하나 이상의 스트림을 토폴로지에 내보냅니다.|
|Bolt|Spout 또는 다른 Bolt에서 내보낸 스트림을 사용합니다. Bolt는 필요에 따라 스트림을 토폴로지로 내보낼 수 있습니다. 또한 Bolt는 HDFS, Kafka 또는 HBase와 같은 외부 서비스 또는 스토리지에 데이터를 쓰는 역할을 수행합니다.|

:::image type="content" source="./media/migrate-storm-to-spark/apache-storm-components.png" alt-text="Storm 구성 요소의 상호 작용" border="false":::

Storm은 다음 세 가지 디먼으로 구성되어 있으며,이로 통해 Storm 클러스터가 작동합니다.

|데몬 |Description |
|---|---|
|Nimbus|Hadoop JobTracker와 마찬가지로 클러스터 주위에 코드를 배포하고 컴퓨터에 작업을 할당하며 오류를 모니터링하는 일을 담당합니다.|
|Zookeeper|클러스터 조정에 사용됩니다.|
|감독자|해당 컴퓨터에 할당된 작업을 수신하고 Nimbus의 지시문에 따라 작업자 프로세스를 시작 및 중지합니다. 각 작업자 프로세스는 토폴로지의 하위 집합을 실행합니다. 여기서 사용자의 애플리케이션 논리(Spouts 및 Bolt)를 실행합니다.|

:::image type="content" source="./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png" alt-text="Nimbus, Zookeeper 및 감독자 디먼" border="false":::

## <a name="spark-streaming-architecture-and-components"></a>Spark Streaming 아키텍처 및 구성 요소

다음 단계는 Spark Streaming(DStreams) 및 Spark Structured Streaming에서 구성 요소가 함께 작동하는 방법을 요약합니다.

* Spark Streaming이 시작되면 드라이버는 실행기에서 작업을 시작합니다.
* 실행기는 스트리밍 데이터 원본에서 스트림을 받습니다.
* 실행기는 데이터 스트림을 받을 때 스트림을 블록으로 분할하고 메모리에 보관합니다.
* 데이터 블록이 다른 실행기에 복제됩니다.
* 처리된 데이터는 대상 데이터 저장소에 저장됩니다.

:::image type="content" source="./media/migrate-storm-to-spark/spark-streaming-to-output.png" alt-text="출력에 대한 Spark Streaming 경로" border="false":::

## <a name="spark-streaming-dstream-workflow"></a>Spark Streaming(DStreams) 워크플로

각 일괄 처리 간격이 지나면 해당 간격에서 모든 데이터를 포함하는 새 RDD가 생성됩니다. RDD의 연속 집합은 DStream으로 수집됩니다. 예를 들어 일괄 처리 간격이 1초 긴 경우 DStream에서 해당 초 동안 수집된 모든 데이터를 포함하는 하나의 RDD를 포함하는 일괄 처리를 매 초 내보냅니다. DStream을 처리할 때 이러한 일괄 처리 중 하나에 온도 이벤트가 나타납니다. Spark 스트리밍 애플리케이션은 이벤트를 포함하는 일괄 처리를 처리하고 각 RDD에 저장된 데이터에서 궁극적으로 작업을 수행합니다.

:::image type="content" source="./media/migrate-storm-to-spark/spark-streaming-batches.png" alt-text="Spark Streaming 처리의 일괄 처리" border="false":::

Spark Streaming에서 사용할 수 있는 다양한 변환에 대한 자세한 내용은 [DStreams에서의 변환](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams)을 참조하세요.

## <a name="spark-structured-streaming"></a>Spark Structured Streaming

Spark Structured Streaming은 데이터 스트림을 깊이 있게 바인딩되지 않는 테이블로 나타냅니다. 새 데이터가 도착할 때 테이블이 계속 증가합니다. 이 입력 테이블은 장기 실행 쿼리를 통해 지속적으로 처리되며, 결과는 출력 테이블로 보내집니다.

구조적 스트리밍에서 데이터는 시스템에 들어오는 즉시 입력 테이블로 수집됩니다. 이 입력 테이블에 대해 작업을 수행하는 쿼리를 작성합니다(데이터 프레임 및 데이터 세트 API 사용).

쿼리 출력은 쿼리 결과를 포함하는 *결과 테이블* 을 생성합니다. 관계형 데이터베이스와 같은 외부 데이터 저장소에 대한 결과 테이블에서 데이터를 그릴 수 있습니다.

데이터가 입력 테이블에서 처리되는 타이밍은 트리거 간격으로 제어됩니다. 트리거 간격은 기본적으로 0이며, 이 경우 데이터가 들어오는 즉시 구조적 스트리밍에서 처리하려고 시도합니다. 실제로 구조적 스트리밍은 이전 쿼리의 실행을 처리하는 즉시 새로 받은 데이터에 대해 또 다른 처리를 시작합니다. 트리거가 일정한 간격으로 실행되도록 구성하여 스트리밍 데이터를 시간 기반 일괄 처리로 처리할 수 있습니다.

:::image type="content" source="./media/migrate-storm-to-spark/structured-streaming-data-processing.png" alt-text="Structured Streaming의 데이터 처리" border="false":::

:::image type="content" source="./media/migrate-storm-to-spark/structured-streaming-model.png" alt-text="Structured Streaming을 위한 프로그래밍 모델" border="false":::

## <a name="general-migration-flow"></a>일반 마이그레이션 흐름

Storm에서 Spark로 권장되는 마이그레이션 흐름은 다음과 같은 초기 아키텍처를 가정합니다.

* Kafka은 스트리밍 데이터 원본으로 사용됩니다.
* Kafka 및 Storm이 동일한 가상 네트워크에 배포됩니다.
* Storm에서 처리하는 데이터는 Azure Storage 또는 Azure Data Lake Storage Gen2 같은 데이터 싱크에 기록됩니다.

   :::image type="content" source="./media/migrate-storm-to-spark/presumed-current-environment.png" alt-text="간주된 현재 환경의 다이어그램"  border="false":::

애플리케이션을 Storm에서 Spark Streaming API 중 하나로 마이그레이션하려면 다음을 수행합니다.

1. **새 클러스터를 배포합니다.** 동일한 가상 네트워크에 새 HDInsight 4.0 Spark 클러스터를 배포하고 Spark Streaming 또는 Spark Structured Streaming 애플리케이션을 배포하고 철저히 테스트합니다.

   :::image type="content" source="./media/migrate-storm-to-spark/new-spark-deployment.png" alt-text="HDInsight의 새 Spark 배포" border="false":::

1. **이전 Storm 클러스터에서 사용 중지** 기존 Storm에서 스트리밍 데이터 원본의 데이터 사용을 중지하고 데이터가 대상 싱크에 쓰기를 완료할 때까지 기다립니다.

   :::image type="content" source="./media/migrate-storm-to-spark/stop-consuming-current-cluster.png" alt-text="현재 클러스터에서 사용 중지" border="false":::

1. **새 Spark 클러스터에서 사용 시작** 새로 배포된 HDInsight 4.0 Spark 클러스터에서 데이터 스트리밍을 시작합니다. 이번에는 최신 Kafka 오프셋에서 사용하여 프로세스를 수행합니다.

   :::image type="content" source="./media/migrate-storm-to-spark/start-consuming-new-cluster.png" alt-text="새 클러스터에서 사용 시작" border="false":::

1. **필요에 따라 이전 클러스터 제거** 스위치가 완료되고 제대로 작동하는 경우 필요에 따라 이전 HDInsight 3.6 Storm 클러스터를 제거합니다.

   :::image type="content" source="./media/migrate-storm-to-spark/remove-old-clusters1.png" alt-text="필요에 따라 이전 HDInsight 클러스터 제거" border="false":::

## <a name="next-steps"></a>다음 단계

Storm, Spark Streaming 및 Spark Structured Streaming에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Apache Spark Streaming 개요](../spark/apache-spark-streaming-overview.md)
* [Apache Spark 구조적 스트리밍 개요](../spark/apache-spark-structured-streaming-overview.md)
