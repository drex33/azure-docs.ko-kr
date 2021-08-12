---
title: Apache Spark 성능 - Azure HDInsight IO 캐시(미리 보기)
description: Azure HDInsight IO 캐시 및 이를 사용하여 Apache Spark 성능을 향상하는 방법에 대해 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/23/2019
ms.openlocfilehash: 9df585c102e2c7307e949e38b6b69147372c38dd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866304"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache"></a>Azure HDInsight IO 캐시를 사용하여 Apache Spark 워크로드의 성능 향상

IO 캐시는 Apache Spark 작업의 성능을 개선하는 Azure HDInsight에 대한 데이터 캐싱 서비스입니다. IO 캐시는 [Apache Spark](https://spark.apache.org/) 클러스터에서 실행할 수 있는 [Apache TEZ](https://tez.apache.org/) 및 [Apache Hive](https://hive.apache.org/) 워크로드에서도 작동합니다. IO 캐시는 RubiX라는 오픈 소스 캐싱 구성 요소를 사용합니다. RubiX는 클라우드 스토리지 시스템의 데이터에 액세스하는 빅 데이터 분석 엔진에 사용하기 위한 로컬 디스크 캐시입니다. RubiX는 캐싱 목적으로 작동 메모리를 예약하지 않고 SSD(반도체 드라이브)를 사용하므로 캐싱 시스템에 고유한 기능입니다. IO 캐시 서비스는 클러스터의 각 작업자 노드에서 RubiX 메타데이터 서버를 시작하고 관리합니다. 또한 클러스터의 모든 서비스를 RubiX 캐시의 투명한 사용에 적합하도록 구성합니다.

대부분의 SSD는 초당 1 GB를 초과하는 대역폭을 제공합니다. 이 대역폭은 운영 체제 메모리 내 파일 캐시에 의해 보완되며 Apache Spark 같은 빅 데이터 컴퓨팅 처리 엔진을 로드하기에 충분한 대역폭을 제공합니다. 작동 메모리는 Apache Spark가 메모리에 크게 의존하는 셔플과 같은 작업을 처리하는 데 사용할 수 있도록 남아 있습니다. 작동 메모리를 독점적으로 사용하면 Apache Spark가 최적의 리소스 사용을 달성할 수 있습니다.  

> [!Note]  
> IO 캐시는 현재 캐싱 구성 요소로 RubiX를 사용하지만 이는 서비스의 미래 버전에서 변경될 수 있습니다. IO 캐시 인터페이스를 사용하고 RubiX 구현에 직접 의존하지 마세요.
>IO 캐시는 현재 Azure BLOB Storage에서만 지원됩니다.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Azure HDInsight IO 캐시의 이점

IO 캐시 사용은 Azure Blob Storage에서 데이터를 읽는 작업에 대해 성능 향상을 제공합니다.

IO 캐시를 사용하면 성능 향상을 달성하기 위해 Spark 작업을 변경할 필요가 없습니다. IO 캐시가 비활성화된 경우 이 Spark 코드는 Azure Blob Storage에서 원격으로 데이터를 읽을 것입니다. `spark.read.load('wasbs:///myfolder/data.parquet').count()`. IO 캐시가 활성화된 경우 같은 코드 줄은 IO 캐시를 통해 캐시된 읽기를 실행하게 합니다. 읽은 후에는 데이터를 SSD에서 로컬로 읽습니다. HDInsight 클러스터의 작업자 노드에는 로컬로 연결된 전용 SSD 드라이브가 장착되어 있습니다. HDInsight IO 캐시는 이 로컬 SSD를 캐싱에 사용하여 가장 낮은 수준의 대기 시간을 제공하고 대역폭을 최대로 늘립니다.

## <a name="getting-started"></a>시작

Azure HDInsight IO 캐시는 기본적으로 미리 보기에서 비활성화됩니다. IO 캐시는 Apache Spark 2.3을 실행 하는 Azure HDInsight 3.6 이상 Spark 클러스터에서 사용할 수 있습니다.  HDInsight 4.0에서 IO 캐시를 활성화하려면 다음 단계를 수행합니다.

1. 웹 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net`로 이동합니다. 여기서 `CLUSTERNAME`은 클러스터의 이름입니다.

1. 왼쪽에서 **IO 캐시** 서비스를 선택합니다.

1. **작업**(**서비스 작업** - HDI 3.6의 경우) 및 **활성화** 를 선택합니다.

    :::image type="content" source="./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png " alt-text="Ambari에서 IO Cache service 활성화" border="true":::

1. 클러스터에 영향을 주는 모든 서비스를 다시 시작하는지 확인합니다.

> [!NOTE]  
> 진행률 표시줄에 활성화된 것으로 나타나지만 IO 캐시는 영향을 받는 다른 서비스를 다시 시작할 때까지 실제로 활성화되지 않습니다.

## <a name="troubleshooting"></a>문제 해결
  
IO 캐시를 사용하도록 설정한 후 Spark 작업을 실행하는 디스크 공간 오류가 발생할 수 있습니다. 이러한 오류는 Spark 작업이 순서 섞기 작업 중에 데이터를 저장하는 데 로컬 디스크 스토리지도 사용하기 때문에 발생합니다. Spark는 IO 캐시가 사용하도록 설정되고 Spark 스토리지 공간이 감소한 후 SSD 공간이 부족해질 수 있습니다. IO 캐시가 사용하는 공간의 양은 기본적으로 총 SSD 공간의 절반입니다. IO 캐시에 대한 디스크 공간 사용량은 Ambari에서 구성할 수 있습니다. 디스크 공간 오류가 발생하면 IO 캐시에 사용되는 SSD 공간의 양을 줄이고 서비스를 다시 시작합니다. IO 캐시에 대해 설정한 공간을 변경하려면 다음 단계를 수행합니다.

1. Apache Ambari에서 왼쪽의 **HDFS** 를 선택합니다.

1. **Configs** 및 **고급** 탭을 선택합니다.

    :::image type="content" source="./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png " alt-text="HDFS 고급 구성 편집" border="true":::

1. 아래로 스크롤하고 **사용자 지정 코어 사이트** 영역을 확장합니다.

1. **hadoop.cache.data.fullness.percentage** 속성을 찾습니다.

1. 상자의 값을 변경합니다.

    :::image type="content" source="./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png " alt-text="IO 캐시 완수율 편집" border="true":::

1. 오른쪽의 **저장** 을 선택합니다.

1. **다시 시작** > **영향을 받은 모든 항목 다시 시작** 을 선택합니다.

    :::image type="content" source="./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png " alt-text="Apache Ambari가 영향을 받는 모든 항목을 다시 시작합니다" border="true":::

1. **모두 다시 시작 확인** 을 선택합니다.

그래도 작동하지 않으면 IO 캐시를 사용하지 않도록 설정합니다.

## <a name="next-steps"></a>다음 단계

이 블로그 게시물의 성능 벤치마크를 포함하여 IO 캐시에 대해 더 자세히 읽기: [HDInsight IO 캐시를 사용할 경우 Apache Spark 작업의 속도가 최대 9배 향상](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)
