---
title: Apache Kafka HDInsight 클러스터의 성능 최적화
description: Azure HDInsight에서 Apache Kafka 워크로드를 최적화하는 기술에 대한 개요를 제공합니다.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: b5c149499fe21940e58c795dbed2407c046ac43d
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130044424"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Apache Kafka HDInsight 클러스터의 성능 최적화

이 문서에서는 HDInsight에서 Apache Kafka 워크로드의 성능을 최적화하기 위한 몇 가지 제안 사항을 제공합니다. 초점은 생산자 및 브로커 구성을 조정하는 것입니다. 성능을 측정하는 방법에는 여러 가지가 있으며 적용하는 최적화는 비즈니스 요구 사항에 따라 다릅니다.

## <a name="architecture-overview"></a>아키텍처 개요

Kafka 토픽은 레코드를 구성하는 데 사용됩니다. 생산자에서 레코드를 생성하고, 소비자에서 이 레코드를 소비합니다. 생산자는 Kafka 브로커에 레코드를 보내고 데이터를 저장합니다. HDInsight 클러스터의 각 작업자 노드는 Kafka broker입니다.

토픽은 브로커 간에 레코드를 분할합니다. 레코드를 소비할 때 데이터의 병렬 처리를 위해 파티션당 최대 1개의 소비자를 사용할 수 있습니다.

복제는 노드 간에 파티션을 복제하는 데 사용됩니다. 이는 노드(브로커) 중단으로부터 보호합니다. 복제본 그룹 중 단일 파티션을 파티션 리더로 지정합니다. 생산자 트래픽은 ZooKeeper에서 관리하는 상태를 사용하여 각 노드의 선행부로 라우팅됩니다.

## <a name="identify-your-scenario"></a>시나리오 식별

Apache Kafka 성능에는 처리량과 대기 시간의 두 가지 주요 측면이 있습니다. 처리량은 데이터를 처리할 수 있는 최대 속도입니다. 일반적으로 처리량이 높을수록 좋습니다. 대기 시간은 데이터를 저장하거나 검색하는 데 걸리는 시간입니다. 일반적으로 대기 시간이 낮을수록 좋습니다. 처리량, 대기 시간 및 애플리케이션 인프라 비용 간의 적절한 균형을 찾는 것은 어려울 수 있습니다. 성능 요구 사항은 높은 처리량, 짧은 대기 시간 또는 둘 다 필요한지 여부에 따라 다음 세 가지 일반적인 상황 중 하나와 일치할 수 있습니다.

* 높은 처리량, 짧은 대기 시간. 이 시나리오에는 높은 처리량과 짧은 대기 시간(~100밀리초)이 모두 필요합니다. 이러한 유형의 애플리케이션의 예는 서비스 가용성 모니터링입니다.
* 높은 처리량, 높은 대기 시간. 이 시나리오는 높은 처리량(~1.5GBps)이 필요하지만 더 긴 대기 시간(< 250ms)을 견딜 수 있습니다. 이러한 유형의 애플리케이션의 예로는 보안 및 침입 탐지 애플리케이션과 같은 실시간에 가까운 프로세스를 위한 원격 분석 데이터 수집이 있습니다.
* 낮은 처리량, 짧은 대기 시간. 이 시나리오는 실시간 처리를 위해 짧은 대기 시간(< 10ms)이 필요하지만 더 낮은 처리량을 허용할 수 있습니다. 이러한 유형의 애플리케이션의 예로는 온라인 맞춤법 및 문법 검사가 있습니다.

## <a name="producer-configurations"></a>생산자 구성

다음 섹션에서는 Kafka 생산자의 성능을 최적화하기 위해 가장 중요한 구성 속성 중 일부를 강조 표시합니다. 모든 구성 속성에 대한 자세한 설명은 [생산자 구성에 대한 Apache Kafka 설명서](https://kafka.apache.org/documentation/#producerconfigs)를 참조하세요.

### <a name="batch-size"></a>Batch 크기

Apache Kafka 생산자는 단일 스토리지 파티션에 저장될 단위로 전송되는 메시지 그룹(일괄 처리라고 함)을 조합합니다. 일괄 처리의 크기는 해당 그룹이 전송되기 전에 있어야 하는 바이트 수를 의미합니다. `batch.size` 매개 변수를 늘리면 네트워크 및 IO 요청의 처리 오버헤드가 줄어들기 때문에 처리량이 증가할 수 있습니다. 가벼운 로드에서 일괄 처리 크기가 증가하면 생산자가 일괄 처리가 준비될 때까지 기다릴 때 Kafka 전송 대기 시간이 증가할 수 있습니다. 로드가 많은 경우 처리량과 대기 시간을 개선하기 위해 일괄 처리 크기를 늘리는 것이 좋습니다.

### <a name="producer-required-acknowledgments"></a>생산자 필수 승인

생산자 필수 `acks` 구성은 쓰기 요청이 완료된 것으로 간주되기 전에 파티션 리더에 필요한 승인 수를 결정합니다. 이 설정은 데이터 안정성에 영향을 미치며 `0`, `1` 또는 `-1` 값을 사용합니다. 값 `-1`은 쓰기가 완료되기 전에 모든 복제본에서 승인을 받아야 함을 의미합니다. `acks = -1`을 설정하면 데이터 손실에 대한 더 강력한 보장이 제공되지만 대기 시간이 길어지고 처리량이 낮아집니다. 애플리케이션 요구 사항이 더 높은 처리량을 요구하는 경우 `acks = 0` 또는 `acks = 1`을 설정해 보세요. 모든 복제본을 확인하지 않으면 데이터 안정성이 저하될 수 있습니다.

### <a name="compression"></a>압축

Kafka 생산자는 메시지를 브로커에 보내기 전에 압축하도록 구성할 수 있습니다. `compression.type` 설정은 사용할 압축 코덱을 지정합니다. 지원되는 압축 코덱은 “gzip”, “snappy” 및 “lz4”입니다. 압축은 유익하며 디스크 용량에 제한이 있는 경우 고려해야 합니다.

일반적으로 사용되는 두 가지 압축 코덱(`gzip` 및 `snappy`) 중에서 `gzip`은 압축률이 높기 때문에 CPU 로드가 높아지면서 디스크 사용량이 줄어듭니다. `snappy` 코덱은 더 적은 CPU 오버헤드로 더 적은 압축을 제공합니다. 브로커 디스크 또는 생산자 CPU 제한에 따라 사용할 코덱을 결정할 수 있습니다. `gzip`은 `snappy`보다 5배 빠른 속도로 데이터를 압축할 수 있습니다.

데이터 압축을 사용하면 디스크에 저장할 수 있는 레코드 수가 늘어납니다. 생산자와 브로커가 사용하는 압축 형식이 일치하지 않는 경우 CPU 오버헤드가 증가할 수도 있습니다. 데이터는 보내기 전에 압축해야 하며 처리하기 전에 압축을 풀어야 하기 때문입니다.

## <a name="broker-settings"></a>브로커 설정

다음 섹션에서는 Kafka 브로커의 성능을 최적화하기 위한 가장 중요한 설정 중 일부를 강조 표시합니다. 모든 broker 설정에 대한 자세한 설명은 [broker 구성에 대한 Apache Kafka 설명서를 참조하세요.](https://kafka.apache.org/documentation/#brokerconfigs)

### <a name="number-of-disks"></a>디스크 수

스토리지 디스크에는 IOPS(초당 입력/출력 작업) 및 초당 읽기/쓰기 바이트가 제한되어 있습니다. 새 파티션을 만들 때 Kafka는 기존 파티션이 가장 적은 디스크에 각각의 새 파티션을 저장하여 사용 가능한 다른 디스크와 균형을 맞춥니다. 스토리지 전략에도 불구하고 각 디스크에서 수백 개의 파티션 복제본을 처리할 때 Kafka는 사용 가능한 디스크 처리량을 쉽게 포화시킬 수 있습니다. 여기서 처리량과 비용 간의 트레이드 오프가 작용합니다. 애플리케이션에 더 많은 처리량이 필요한 경우 브로커당 더 많은 관리 디스크가 있는 클러스터를 만듭니다. HDInsight는 현재 실행 중인 클러스터에 관리 디스크 추가를 지원하지 않습니다. 관리 디스크 수를 구성하는 방법에 대한 자세한 내용은 [HDInsight에서 Apache Kafka의 스토리지 및 확장성 구성](apache-kafka-scalability.md)을 참조하세요. 클러스터의 노드에 대한 스토리지 공간 증가의 비용 면에서의 영향을 이해합니다.

### <a name="number-of-topics-and-partitions"></a>토픽 및 파티션 수

Kafka 생산자는 토픽에 글을 씁니다. Kafka 소비자는 토픽을 읽습니다. 토픽은 디스크의 데이터 구조인 로그와 연결됩니다. Kafka는 생산자의 레코드를 토픽 로그 끝에 추가합니다. 토픽 로그는 여러 파일에 분산된 많은 파티션으로 구성됩니다. 이러한 파일은 차례로 여러 Kafka 클러스터 노드에 분산됩니다. 소비자는 자신의 속도로 Kafka 토픽을 읽고 토픽 로그에서 위치(오프셋)를 선택할 수 있습니다.

각 Kafka 파티션은 시스템의 로그 파일이며 생산자 스레드는 동시에 여러 로그에 쓸 수 있습니다. 마찬가지로, 각 소비자 스레드는 하나의 파티션에서 메시지를 읽기 때문에 여러 파티션에서 소비하는 것도 병렬로 처리됩니다.

파티션 밀도(브로커당 파티션 수)를 늘리면 메타데이터 작업 및 파티션 리더와 해당 팔로워 간의 파티션 요청/응답당 오버헤드가 추가됩니다. 데이터 흐름이 없는 경우에도 파티션 복제본은 여전히 리더에서 데이터를 가져오므로 네트워크를 통한 전송 및 수신 요청에 대한 추가 처리가 발생합니다.

HDInsight의 Apache Kafka 클러스터 1.1 이상의 경우 복제본을 포함하여 브로커당 최대 1000개의 파티션을 설정하는 것이 좋습니다. 브로커당 파티션 수를 늘리면 처리량이 감소하고 토픽을 사용할 수 없게 될 수도 있습니다. Kafka 파티션 지원에 대한 자세한 내용은 [버전 1.1.0에서 지원되는 파티션 수 증가에 대한 공식 Apache Kafka 블로그 게시물](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions)을 참조하세요. 토픽 수정에 대한 자세한 내용은 [Apache Kafka: 토픽 수정](https://kafka.apache.org/documentation/#basic_ops_modify_topic)을 참조하세요.

### <a name="number-of-replicas"></a>복제본 수

복제 계수가 높을수록 파티션 리더와 팔로워 간에 추가 요청이 발생합니다. 결과적으로 복제 계수가 높을수록 추가 요청을 처리하기 위해 더 많은 디스크와 CPU를 사용하므로 쓰기 대기 시간이 늘어나고 처리량이 감소합니다.

Azure HDInsight에서 Kafka에 대해 3배 이상의 복제를 사용하는 것이 좋습니다. 대부분의 Azure 지역에는 3개의 장애 도메인이 있지만 2개의 장애 도메인만 있는 지역에서는 사용자가 4배 복제를 사용해야 합니다.

복제에 대한 자세한 내용은 [Apache Kafka: 복제](https://kafka.apache.org/documentation/#replication) 및 [Apache Kafka: 복제 계수 증가](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure에서 Apache Kafka를 사용하여 매일 엄청난 수의 이벤트 처리](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [HDInsight의 Apache Kafka란?](apache-kafka-introduction.md)
