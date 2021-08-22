---
title: Apache Kafka MirrorMaker 사용 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Kafka MirrorMaker를 사용하여 Azure Event Hubs에서 Kafka 클러스터를 미러링하는 방법에 대한 정보를 제공합니다.
ms.topic: how-to
ms.date: 01/04/2021
ms.openlocfilehash: beb48fc613ace6dd953c3fe773f09d5fb4c4c822
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/04/2021
ms.locfileid: "113286415"
---
# <a name="use-apache-kafka-mirrormaker-with-event-hubs"></a>Event Hubs를 사용하는 Apache Kafka MirrorMaker 사용

다음 자습서에서는 Kafka MirrorMaker를 사용하여 Azure 이벤틑 허브에 Kafka broker를 미러링하는 방법을 보여 줍니다. CNCF Strimzi 연산자를 사용하여 Kubernetes에서 Apache Kafka를 호스트하는 경우 [이 블로그 게시물](https://strimzi.io/blog/2020/06/09/mirror-maker-2-eventhub/)의 자습서를 참조하여 Strimzi 및 Mirror Maker 2를 사용하여 Kafka를 설정하는 방법을 알아볼 수 있습니다. 

   ![Event Hubs를 사용하는 Kafka MirrorMaker](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> 이 샘플은 [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)에서 사용할 수 있습니다.

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 *허용 목록* 용어에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * Event Hubs 네임스페이스 만들기
> * 프로젝트 예제 복제
> * Kafka 클러스터 설정
> * Kafka MirrorMaker 구성
> * Kafka MirrorMaker 실행

## <a name="introduction"></a>소개
이 자습서에서는 Event Hubs 서비스에서 Kafka 입력 스트림을 미러하여 이벤트 허브와 Kafka MirrorMaker가 기존 Kafka 파이프라인을 Azure에 통합할 수 있는 방법을 보여 줍니다. 이 방법을 사용하면 몇 가지 [페더레이션 패턴](event-hubs-federation-overview.md)을 사용하여 Apache Kafka 스트림을 통합할 수 있습니다. 

Azure Event Hubs Kafka 엔드포인트를 사용하면 Kafka 프로토콜(즉, Kafka 클라이언트)을 사용하여 Azure Event Hubs에 연결할 수 있습니다. Kafka 애플리케이션을 최소한으로 변경하면 Azure Event Hubs에 연결하여 Azure 에코시스템의 이점을 누릴 수 있습니다. Event Hubs는 현재 Apache Kafka 버전 1.0 이상의 프르토콜을 지원합니다.

Apache Kafka에서 Event Hubs로의 단방향으로 Apache Kafka의 MirrorMaker 1을 사용할 수 있습니다. MirrorMaker 2는 양방향으로 사용할 수 있지만 [MirrorMaker 2에서 구성할 수 있는 `MirrorCheckpointConnector` 및 `MirrorHeartbeatConnector`](https://cwiki.apache.org/confluence/display/KAFKA/KIP-382%3A+MirrorMaker+2.0)는 둘 다 Event Hubs가 아닌 Apache Kafka broker를 가리키도록 구성해야 합니다. 이 자습서에서는 MirrorMaker 1을 구성하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 설치되어 있어야 합니다.

* [Apache Kafka용 Event Hubs](event-hubs-for-kafka-ecosystem-overview.md) 문서를 참조하세요. 
* Azure 구독 구독이 없으면 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
* [JDK(Java Development Kit) 1.7+](/azure/developer/java/fundamentals/java-support-on-azure)
    * Ubuntu에서 `apt-get install default-jdk`를 실행하여 JDK를 설치합니다.
    * JAVA_HOME 환경 변수가 반드시 JDK가 설치된 폴더를 지정하도록 설정합니다.
* Maven 이진 보관 파일을 [다운로드](https://maven.apache.org/download.cgi)하여 [설치](https://maven.apache.org/install.html)합니다.
    * Ubuntu에서 `apt-get install maven`을 실행하여 Maven을 실행할 수 있습니다.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu에서 `sudo apt-get install git`를 실행하여 Git를 실행할 수 있습니다.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 생성

Event Hubs 서비스와 통신하려면 Event Hubs 네임스페이스가 필요합니다. 네임스페이스 및 이벤트 허브를 만드는 방법에 대한 지침은 [이벤트 허브 만들기](event-hubs-create.md)를 참조하세요. 나중에 사용하기 위해 Event Hubs 연결 문자열을 복사합니다.

## <a name="clone-the-example-project"></a>프로젝트 예제 복제

이제 Event Hubs 연결 문자열이 있으므로 Kafka용 Azure Event Hubs 리포지토리를 복제하고 `mirror-maker` 하위 폴더로 이동합니다.

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Kafka 클러스터 설정

[Kafka 빠른 시작 가이드](https://kafka.apache.org/quickstart)를 사용하여 원하는 설정으로 클러스터를 설정하거나 기존 Kafka 클러스터를 사용합니다.

## <a name="configure-kafka-mirrormaker"></a>Kafka MirrorMaker 구성

Kafka MirrorMaker를 사용하면 스트림을 "미러"할 수 있습니다. 원본 및 대상 Kafka 클러스터가 지정되면 MirrorMaker는 원본 클러스터에 보낸 모든 메시지를 원본 및 대상 클러스터 모두에서 받을 수 있도록 합니다. 다음 예제에서는 대상 이벤트 허브를 사용하여 원본 Kafka 클러스터를 미러하는 방법을 보여 줍니다. 이 시나리오는 데이터 흐름을 중단하지 않고 기존 Kafka 파이프라인에서 Event Hubs로 데이터를 보내는 데 사용할 수 있습니다. 

Kafka MirrorMaker에 대한 자세한 내용은 [Kafka 미러링/MirrorMaker 가이드](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)를 참조하세요.

Kafka MirrorMaker를 구성하려면 Kafka 클러스터를 소비자/원본으로, 그리고 이벤트 허브를 생산자/대상으로 지정합니다.

#### <a name="consumer-configuration"></a>소비자 구성

MirrorMaker에 원본 Kafka 클러스터의 속성을 알려주는 `source-kafka.config` 소비자 구성 파일을 업데이트합니다.

##### <a name="source-kafkaconfig"></a>source-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>생산자 구성

이제 MirrorMaker에서 복제된(또는 "미러된") 데이터를 Event Hubs 서비스에 보내도록 지시하는 `mirror-eventhub.config` 생산자 구성 파일을 업데이트합니다. 특히 Event Hubs Kafka 엔드포인트를 가리키도록 `bootstrap.servers`과 `sasl.jaas.config`를 변경합니다. Event Hubs 서비스에는 다음 구성에서 마지막 세 가지 속성을 설정하여 얻을 수 있는 보안 통신(SASL)이 필요합니다. 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> `{YOUR.EVENTHUBS.CONNECTION.STRING}`을 Event Hubs 네임스페이스의 연결 문자열로 바꿉니다. 연결 문자열을 가져오는 방법에 대한 지침은 [Event Hubs 연결 문자열 가져오기](event-hubs-get-connection-string.md)를 참조하세요. `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";` 구성의 예는 다음과 같습니다.

## <a name="run-kafka-mirrormaker"></a>Kafka MirrorMaker 실행

새로 업데이트된 구성 파일을 사용하여 루트 Kafka 디렉터리에서 Kafka MirrorMaker 스크립트를 실행합니다. 구성 파일을 루트 Kafka 디렉터리에 복사하거나 다음 명령에서 해당 경로를 업데이트해야 합니다.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

이벤트가 이벤트 허브에 도달하는지 확인하려면 [Azure Portal](https://azure.microsoft.com/features/azure-portal/)에서 수신 통계를 살펴보거나 이벤트 허브에 대해 소비자를 실행합니다.

MirrorMaker를 실행하면, Kafka 클러스터 및 미러된 이벤트 허브 모두에서 원본 Kafka 클러스터에 보낸 모든 이벤트를 받습니다. MirrorMaker 및 Event Hubs Kafka 엔드포인트를 사용하면, 기존 클러스터를 변경하거나 진행 중인 데이터 흐름을 중단하지 않고도 기존 Kafka 파이프라인을 관리되는 Azure Event Hubs 서비스로 마이그레이션할 수 있습니다.

## <a name="samples"></a>샘플
GitHub에서 다음 샘플을 참조하세요.

- [GitHub에 제공되는 이 자습서의 샘플 코드](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure Container Instance에서 실행되는 Azure Event Hubs Kafka MirrorMaker](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>다음 단계
Kafka용 Event Hubs에 대해 자세한 내용은 다음 문서를 참조하세요.  

- [이벤트 허브에 Apache Spark 연결](event-hubs-kafka-spark-tutorial.md)
- [이벤트 허브에 Apache Flink 연결](event-hubs-kafka-flink-tutorial.md)
- [이벤트 허브와 Kafka 연결 통합](event-hubs-kafka-connect-tutorial.md)
- [GitHub에서 더 많은 샘플 탐색](https://github.com/Azure/azure-event-hubs-for-kafka)
- [이벤트 허브에 Akka Streams 연결](event-hubs-kafka-akka-streams-tutorial.md)
- [Azure Event Hubs에 대한 Apache Kafka 개발자 가이드](apache-kafka-developer-guide.md)