---
title: Event Hubs에 대한 Apache Kafka 개발자 가이드
description: 이 문서에서는 Azure Event Hubs와 Kafka 애플리케이션을 통합하는 방법을 설명하는 문서의 링크를 제공합니다.
ms.date: 09/20/2021
ms.topic: article
ms.openlocfilehash: 1206d01a644545d1cbcc1d92a941fc4e29aa4d76
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128660883"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Azure Event Hubs에 대한 Apache Kafka 개발자 가이드
이 문서에서는 Azure Event Hubs와 Apache Kafka 애플리케이션을 통합하는 방법을 설명하는 문서의 링크를 제공합니다. 

## <a name="overview"></a>개요
Event Hubs는 기존 Kafka 기반 애플리케이션이 사용자 고유의 Kafka 클러스터 실행에 대한 대안으로 사용할 수 있는 Kafka 엔드포인트을 제공합니다. Event Hubs는 대부분의 기존 Kafka 애플리케이션에서 작동합니다. 자세한 내용은 [Apache Kafka용 Event Hubs](event-hubs-for-kafka-ecosystem-overview.md)를 참조하세요.

## <a name="quickstarts"></a>빠른 시작
GitHub와 이 콘텐츠 집합에서 Kafka용 Event Hubs에 더 빨리 익숙해지는 데 도움이 되는 빠른 시작을 사용할 수 있습니다.

### <a name="quickstarts-in-github"></a>GitHub의 빠른 시작
**azure-event-hubs-for-kafka** 리포지토리에서 다음 빠른 시작을 참조하세요. 

| 클라이언트 언어/프레임워크 | Description | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>이 빠른 시작에서는 .NET Core 2.0을 사용하여 C#으로 작성된 예제 생산자 및 소비자를 통해 Event Hubs Kafka 엔드포인트를 만들고 연결하는 방법을 보여 줍니다.</p><p>샘플은 Kafka용 Event Hubs에서 사용하기 위해 수정된 [Confluent의 Apache Kafka .NET 클라이언트](https://github.com/confluentinc/confluent-kafka-dotnet)를 기반으로 합니다.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | 이 빠른 시작에서는 Java로 작성된 예제 생산자 및 소비자를 통해 Event Hubs Kafka 엔드포인트를 만들고 연결하는 방법을 보여 줍니다. |
| [Node.JS](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>이 빠른 시작에서는 Node로 작성된 예제 생산자 및 소비자를 통해 Event Hubs Kafka 엔드포인트를 만들고 연결하는 방법을 보여 줍니다.</p><p>샘플은 [node-rdkafka](https://github.com/Blizzard/node-rdkafka) 라이브러리를 사용합니다. </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>이 빠른 시작에서는 Python으로 작성된 예제 생산자 및 소비자를 통해 Event Hubs Kafka 엔드포인트를 만들고 연결하는 방법을 보여 줍니다.</p><p>샘플은 Kafka용 Event Hubs에서 사용하기 위해 수정된 [Confluent의 Apache Kafka Python 클라이언트](https://github.com/confluentinc/confluent-kafka-python)를 기반으로 합니다.</p>|
| [Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>이 빠른 시작에서는 Go로 작성된 예제 생산자 및 소비자를 통해 Event Hubs Kafka 엔드포인트를 만들고 연결하는 방법을 보여 줍니다.</p><p>샘플은 Kafka용 Event Hubs에서 사용하기 위해 수정된 [Confluent의 Apache Kafka Golang 클라이언트](https://github.com/confluentinc/confluent-kafka-go)를 기반으로 합니다.</p>| 
| [Sarama kafka Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | 이 빠른 시작에서는 [Sarama Kafka 클라이언트](https://github.com/Shopify/sarama) 라이브러리를 사용하여 Go로 작성된 예제 생산자 및 소비자를 통해 Event Hubs Kafka 엔드포인트를 만들고 연결하는 방법을 보여 줍니다. |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | 이 빠른 시작에서는 Apache Kafka 배포와 함께 제공되는 CLI를 사용하여 Event Hubs Kafka 엔드포인트를 만들고 연결하는 방법을 보여 줍니다.| 
| [Kafkacat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat는 librdkafka를 기반으로 하는 비 JVM 명령줄 소비자 및 생산자로, 빠른 속도와 적은 메모리 공간 때문에 널리 사용됩니다. 이 빠른 시작에는 샘플 구성과 여러 개의 간단한 샘플 kafkacat 명령이 포함되어 있습니다. | 
 
### <a name="quickstarts-in-docs"></a>DOCS의 빠른 시작
이 콘텐츠 집합에서 [Kafka 프로토콜을 사용하여 Event Hubs로 데이터 스트리밍](event-hubs-quickstart-kafka-enabled-event-hubs.md) 빠른 시작을 참조하세요. Event Hubs로 스트리밍하는 방법에 대한 단계별 지침이 제공됩니다. 생산자와 소비자가 애플리케이션 구성을 간단하게 변경하여 Event Hubs로 대화하는 방법을 알아봅니다. 


## <a name="tutorials"></a>자습서 

### <a name="tutorials-in-github"></a>GitHub의 자습서
GitHub에서 다음 자습서를 참조하세요.

| 자습서 | Description | 
| ------------------------- | ----------- | 
| [Akka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | 이 자습서에서는 프로토콜 클라이언트를 변경하거나 사용자 고유의 클러스터를 실행하지 않고 Akka Streams를 Kafka 지원 Event Hubs에 연결하는 방법을 보여 줍니다. **Java** 및 **Scala** 프로그래밍 언어를 사용하는 두 가지 별도의 자습서가 있습니다. | 
| [연결](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | 이 문서에서는 Azure Event Hubs와 Kafka Connect를 통합하고 기본 FileStreamSource 및 FileStreamSink 커넥터를 배포하는 과정을 안내합니다. 프로덕션 용도로 작성된 것은 아니지만 두 커넥터는 Azure Event Hubs가 Kafka broker로 가장하는 엔드투엔드 Kafka Connect 시나리오를 보여줍니다.| 
| [Filebeat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | 이 문서에서는 Filebeat의 Kafka 출력을 통해 Filebeat와 Event Hubs를 통합하는 과정을 안내합니다. | 
| [Flink](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | 이 자습서에서는 프로토콜 클라이언트를 변경하거나 사용자 고유의 클러스터를 실행하지 않고 Apache Flink를 Kafka 지원 Event Hubs에 연결하는 방법을 보여 줍니다. | 
| [FluentD](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | 이 문서에서는 Fluentd용 `out_kafka` 출력 플러그 인을 사용하여 Fluentd와 Event Hubs를 통합하는 과정을 안내합니다. |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | 이 자습서에서는 다양한 프로토콜을 사용하여 소비자와 생산자 간에 이벤트를 교환하는 방법을 보여 줍니다. |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | 이 자습서에서는 Logstash Kafka 입출력 플러그 인을 사용하여 Kafka 지원 Event Hubs와 Logstash를 통합하는 과정을 안내합니다. | 
| [MirrorMaker](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | 이 자습서에서는 Event Hubs 서비스에서 Kafka 입력 스트림을 미러하여 이벤트 허브와 Kafka MirrorMaker가 기존 Kafka 파이프라인을 Azure에 통합할 수 있는 방법을 보여 줍니다. |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | 이 자습서에서는 Apache NiFi를 Event Hubs 네임스페이스에 연결하는 방법을 보여 줍니다. | 
| [OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | 빠른 시작에서는 Go 및 Java 프로그래밍 언어로 작성된 예제 생산자와 소비자를 사용하여 Event Hubs Kafka 엔드포인트를 만들고 연결하는 방법을 보여 줍니다. |
| [Confluent의 스키마 레지스트리](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | 이 자습서에서는 스키마 레지스트리와 Kafka용 Event Hubs를 통합하는 과정을 안내합니다. | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | 이 자습서에서는 프로토콜 클라이언트를 변경하거나 사용자 고유의 Kafka 클러스터를 실행하지 않고 Spark 애플리케이션을 이벤트 허브에 연결하는 방법을 보여 줍니다. | 

### <a name="tutorials-in-docs"></a>DOCS의 자습서
이 콘텐츠 집합에서 [Stream Analytics를 사용하여 Event Hubs 이벤트에 대해 Apache Kafka 처리](event-hubs-kafka-stream-analytics.md) 자습서도 참조하세요. 데이터를 Event Hubs로 스트리밍하고 Azure Stream Analytics로 처리하는 방법을 보여 줍니다.

## <a name="how-to-guides"></a>방법 가이드
설명서에서 다음과 같은 방법 가이드를 참조하세요.

| 아티클 | Description | 
| ------- | ----------- | 
| [이벤트 허브에서 Kafka broker 미러링](event-hubs-kafka-mirror-maker-tutorial.md) | Kafka MirrorMaker를 사용하여 이벤트 허브에서 Kafka broker를 미러하는 방법을 보여 줍니다. |
| [이벤트 허브에 Apache Spark 연결](event-hubs-kafka-spark-tutorial.md) | 실시간으로 스트리밍을 위해 Spark 애플리케이션을 Event Hubs에 연결하는 과정을 안내합니다. |
| [이벤트 허브에 Apache Flink 연결](event-hubs-kafka-flink-tutorial.md) | 프로토콜 클라이언트를 변경하거나 사용자 고유의 클러스터를 실행하지 않고 Apache Flink를 이벤트 허브에 연결하는 방법을 보여 줍니다. |
| [이벤트 허브와 Apache Kafka Connect 통합(미리 보기)](event-hubs-kafka-connect-tutorial.md) | 이벤트 허브와 Kafka Connect를 통합하고 기본 FileStreamSource 및 FileStreamSink 커넥터를 배포하는 과정을 안내합니다. |
| [이벤트 허브에 Akka Streams 연결](event-hubs-kafka-akka-streams-tutorial.md) | 프로토콜 클라이언트를 변경하거나 사용자 고유의 클러스터를 실행하지 않고 Akka Streams를 이벤트 허브에 연결하는 방법을 보여 줍니다. |
| [Spring Boot Starter를 통해 Azure Event Hubs와 함께 Apache Kafka 사용](/azure/developer/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Azure Event Hubs와 함께 Apache Kafka를 사용하도록 Spring Boot Initializer로 만든 Java 기반 Spring Cloud Stream Binder를 구성하는 방법을 보여 줍니다. |

## <a name="next-steps"></a>다음 단계
GitHub 리포지토리 [azure-event-hubs-for-kafka](https://github.com/Azure/azure-event-hubs-for-kafka)에서 빠른 시작 및 자습서 폴더 아래에 있는 샘플을 검토합니다.

다음 문서도 참조하세요.

- [Event Hubs에 대한 Apache Kafka 문제 해결 가이드](apache-kafka-troubleshooting-guide.md)
- [질문과 대답 - Apache Kafka용 Event Hubs](apache-kafka-frequently-asked-questions.yml)
- [Event Hubs에 대한 Apache Kafka 마이그레이션 가이드](apache-kafka-migration-guide.md)



