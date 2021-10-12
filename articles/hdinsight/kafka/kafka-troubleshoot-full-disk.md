---
title: Azure HDInsight 전체 디스크로 인해 Broker를 시작하지 못함
description: 디스크 전체 오류로 인해 시작할 수 없는 Apache Kafka Broker 프로세스에 대한 문제 해결 단계입니다.
ms.service: hdinsight
ms.topic: troubleshooting
author: Jacky-hdi
ms.author: linjzhu
ms.date: 10/11/2021
ms.openlocfilehash: 3e7d113da1c086c0a95916dd29c9149beb897862
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859537"
---
# <a name="scenario-brokers-are-unhealthy-or-cant-restart-due-to-disk-space-full-issue"></a>시나리오: 디스크 공간 전체 문제로 인해 브로커가 비정상 상태이거나 다시 시작할 수 없습니다.

이 문서에서는 Azure HDInsight 클러스터에서 Apache Kafka 사용하는 문제에 대한 문제 해결 단계 및 가능한 해결을 설명합니다.

## <a name="issue"></a>문제

Azure HDInsight 클러스터의 Apache Kafka broker에서 사용하는 데이터 디스크가 채워질 수 있습니다. 이 경우 Apache Kafka broker 프로세스를 시작할 수 없으며 디스크 전체 오류로 인해 실패합니다. 최근 구성을 변경한 경우 Kafka broker 프로세스가 시작되지 않으므로 해당 변경 내용이 적용되지 않습니다.

## <a name="cause"></a>원인

이 문제는 다음 시나리오 중 하나 이상으로 인해 발생할 수 있습니다.

- Kafka 클러스터를 만든 후에는 디스크 수를 늘리거나 디스크 크기를 늘릴 수 없습니다.
- 일반적으로 디스크 경고는 Apache Ambari UI에서 구성됩니다. 디스크 사용량이 60%를 초과할 때마다 Kafka 클러스터에 있는 로그 수를 확장하거나 줄여야 한다는 경고가 표시됩니다.
- 이 문제는 디스크 경고를 무시하는 경우에만 발생합니다. 클러스터를 확장하여 디스크 공간 경고에 응답하는 데 사용할 수 있는 공간을 더 많이 만들 수 있습니다.
- 보존 시간이 경과하더라도 메시지는 즉시 삭제되지 않습니다. 삭제할 메시지는 삭제하도록 표시됩니다. 나중에 백그라운드 정리 프로세스에서 메시지를 삭제합니다. 수동 세그먼트의 메시지만 삭제됩니다.

> [!IMPORTANT]
> 구성을 사용하여 로그 클리너 성능을 향상시킬 수 있지만 메시지 생성 또는 소비에 영향을 줄 수 있으므로 이 향상된 기능을 적용할 *때는 주의해야* 합니다.

## <a name="mitigation"></a>완화 방법

문제를 완화하려면:

1. 파일을 `server.properties` 확인하여 모든 항목의 보존 시간을 찾습니다. 경우에 따라 로그 보존 정책이 토픽 수준에서 설정됩니다. 토픽 수준에서 구성된 보존 시간을 찾으려면 다음 명령을 실행합니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --describe --zookeeper <zookeeper-list>
    ```

2. 해당 정보가 있는 경우 최대 디스크 공간을 차지하는 많은 파티션을 확인합니다.

    ```bash
    # Command to sort the directories based on size:
    du -hs * | sort -rh | head -5 
    ```

3. 새 보존 시간보다 오래된 파일을 백업합니다.

4. 사용 가능한 공간이 있는 경우 broker를 다시 시작하여 새 보존 시간 구성을 사용할 수 있습니다. brokers를 다시 시작하면 이전 로그가 정리되고 디스크의 공간이 확보됩니다.

   > [!IMPORTANT]
   > 경우에 따라 OS 디스크가 가득 차거나 다른 Kafka 디스크를 오버로드하기 때문에 백업을 하는 것은 옵션이 아닐 수 있습니다. 이 시나리오에서는 보존 시간보다 오래된 파일을 삭제해야 할 수 있습니다.

## <a name="resolution"></a>해결 방법

보존 시간을 줄일 수 있지만 클러스터에 항목을 더 추가하려는 경우 또는 수집된 데이터의 로드 또는 양이 증가하는 경우 구성을 확장할 수 없습니다.

이러한 시나리오를 방지하려면 다음 옵션 중 하나를 사용하는 것이 좋습니다.

- 파티션이 너무 크면 가장 많은 토픽에 대한 파티션 수를 늘림

  > [!NOTE]
  > 기존 토픽에서 파티션을 늘리면 토픽의 데이터가 다시 조직되지 않습니다. 먼저 이전의 낮은 파티션 토픽에서 새 더 높은 파티션 토픽으로 데이터를 수동으로 복사해야 합니다. 

- 클러스터를 확장하고 여러 디스크에서 모든 파티션의 균형을 다시 조정합니다.

- 더 큰 SKU와 더 많은 디스크가 연결된 클러스터를 만듭니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
