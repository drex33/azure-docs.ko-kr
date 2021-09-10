---
title: Apache Kafka 앱에서 이벤트 허브 사용 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs에서 지원하는 Apache Kafka에 정보를 제공합니다.
ms.topic: article
ms.date: 08/30/2021
ms.openlocfilehash: f0ea6469d17248a0d37a7ababf767d494545fbbb
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123250650"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Apache Kafka 애플리케이션에서 Azure Event Hubs 사용
Event Hubs는 사용자 고유의 Apache Kafka 클러스터를 실행하는 대신 대부분의 기존 Apache Kafka 클라이언트 애플리케이션에서 사용할 수 있는 Apache Kafka® 생산자 및 소비자 API 호환 엔드포인트를 제공합니다. Event Hubs는 버전 1.0 이상에서 Apache Kafka의 생산자 및 소비자 API 클라이언트를 지원합니다.


## <a name="what-does-event-hubs-for-kafka-provide"></a>Kafka용 Event Hubs는 무엇을 제공하나요?

Apache Kafka용 Event Hubs 기능은 Apache Kafka 서버 버전 1.0 이상용으로 빌드된 Apache Kafka 클라이언트와 프로토콜이 호환되는 Azure Event Hubs 위에 프로토콜 헤드를 제공하고 Apache Kafka 항목에 해당하는 Event Hubs에서 읽기 및 쓰기를 모두 지원합니다. 

기존 Kafka 설정과 비교하여 코드 변경 없이 애플리케이션에서 Event Hubs Kafka 엔드포인트를 자주 사용하고 구성만 수정할 수 있습니다. Kafka 클러스터를 가리키는 대신 이벤트 허브에 의해 노출되는 Kafka 엔드포인트를 가리키도록 구성의 연결 문자열을 업데이트합니다. 그런 다음, Kafka 프로토콜을 사용하는 애플리케이션에서 Event Hubs로 이벤트 스트리밍을 시작할 수 있습니다. 

개념적으로 Kafka와 Event Hubs는 매우 유사합니다. 둘 다 스트리밍 데이터용으로 작성된 분할 로그이며, 클라이언트는 보관된 로그에서 읽을 부분을 제어합니다. 다음 표에서 Kafka 및 Event Hubs 간 개념을 매핑합니다.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka 및 Event Hubs의 개념적 매핑

| Kafka 개념 | Event Hubs 클러스터|
| --- | --- |
| 클러스터 | 네임스페이스 |
| 토픽 | 이벤트 허브 |
| 파티션 | 파티션|
| 소비자 그룹 | 소비자 그룹 |
| Offset | Offset|

### <a name="key-differences-between-apache-kafka-and-event-hubs"></a>Kafka와 Event Hubs의 주요 차이점

[Apache Kafka](https://kafka.apache.org/)는 일반적으로 설치 및 운영에 필요한 소프트웨어이지만 Event Hubs는 완전 관리형 클라우드 네이티브 서비스입니다. 관리하고 모니터링할 서버, 디스크 또는 네트워크가 없으며 고려하거나 구성할 브로커가 없습니다. 정규화된 도메인 이름의 엔드포인트인 네임스페이스를 만든 다음, 해당 네임스페이스 내에 Event Hubs(토픽)를 만듭니다. 

Event Hubs 및 네임스페이스에 대한 자세한 내용은 [Event Hubs 기능](event-hubs-features.md#namespace)을 참조하세요. 클라우드 서비스로서 Event Hubs는 안정적인 단일 가상 IP 주소를 엔드포인트로 사용하므로 클라이언트는 클러스터 내에서 브로커 또는 컴퓨터에 대해 알 필요가 없습니다. Event Hubs가 동일한 프로토콜을 구현하더라도 이 차이점은 모든 파티션에 대한 모든 Kafka 트래픽이 클러스터의 모든 브로커에 대한 방화벽 액세스를 요구하지 않고 이 하나의 엔드포인트를 통해 예측 가능하게 라우팅됨을 의미합니다.   

Event Hubs 규모는 구매한 [처리량 단위(TU)](event-hubs-scalability.md#throughput-units) 또는 [처리 단위](event-hubs-scalability.md#processing-units) 수에 따라 제어됩니다. 표준 계층 네임스페이스에 대해 [자동 확장](event-hubs-auto-inflate.md) 기능을 사용하도록 설정하는 경우 처리량 제한에 도달하면 Event Hubs가 자동으로 TU를 확장합니다. 이 기능은 Apache Kafka 프로토콜 지원에서도 작동합니다. 프리미어 계층 네임스페이스의 경우 네임스페이스에 할당된 처리 단위 수를 늘릴 수 있습니다. 

### <a name="is-apache-kafka-the-right-solution-for-your-workload"></a>Apache Kafka가 워크로드에 적합한 솔루션인가요?

Apache Kafka를 사용하여 애플리케이션을 빌드할 때 Azure Event Hubs가 [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) 및 [Azure Event Grid](../event-grid/overview.md)를 포함하는 서비스 집합의 일부임을 이해하는 것도 유용합니다. 

Apache Kafka의 일부 상용 배포 공급자는 Apache Kafka가 모든 메시징 플랫폼 요구 사항을 위한 원 스톱 상점이라고 할 수 있지만, 실제로 Apache Kafka는 [경쟁 소비자](/azure/architecture/patterns/competing-consumers) 큐 패턴을 구현하지 않고, 구독자가 일반 오프셋 이외의 서버 평가 규칙을 기반으로 수신 메시지에 액세스할 수 있도록 허용하는 수준에서 [게시-구독](/azure/architecture/patterns/publisher-subscriber)을 지원하지 않으며, 메시지에 의해 시작된 작업의 수명 주기를 추적하거나 잘못된 메시지를 배달 실패 메시지 큐로 분류하는 기능이 없습니다. 이들 모두 많은 엔터프라이즈 메시징 시나리오의 기본입니다.

패턴 간 차이점과 어느 서비스에서 어느 패턴을 가장 잘 다루는지 이해하려면 [Azure의 비동기 메시징 옵션](/azure/architecture/guide/technology-choices/messaging) 참고 자료를 참조하세요. Apache Kafka 사용자는 지금까지 Kafka로 실현한 통신 경로를 훨씬 적은 기본 복잡성과 Event Grid 또는 Service Bus를 사용하는 더 강력한 기능으로 실현할 수 있습니다. 

Apache Kafka용 Event Hubs 인터페이스를 통해 사용할 수 없는 Apache Kafka의 특정 기능이 필요하거나 구현 패턴이 [Event Hubs 할당량](event-hubs-quotas.md)을 초과하는 경우 [Azure HDInsight에서 네이티브 Apache Kafka 클러스터](../hdinsight/kafka/apache-kafka-introduction.md)를 실행할 수도 있습니다.  

## <a name="security-and-authentication"></a>보안 및 인증
Kafka용 Event Hubs에서 이벤트를 게시하거나 이용할 때마다 클라이언트가 Event Hubs 리소스에 액세스하려고 합니다. 권한이 부여된 엔터티를 사용하여 리소스에 액세스할 수 있는지 확인하려고 합니다. 클라이언트에서 Apache Kafka 프로토콜을 사용하는 경우 SASL 메커니즘을 사용하여 인증 및 암호화에 대한 구성을 설정할 수 있습니다. Kafka에 Event Hubs를 사용하려면 TLS 암호화가 필요합니다(Event Hubs 전송 중인 모든 데이터는 TLS 암호화됨). 구성 파일에서 SASL_SSL 옵션을 지정하여 수행할 수 있습니다. 

Azure Event Hubs는 보안 리소스에 대한 액세스 권한을 부여하는 여러 옵션을 제공합니다. 

- OAuth 2.0
- 공유 액세스 서명(SAS)

### <a name="oauth-20"></a>OAuth 2.0
Event Hubs는 **OAuth 2.0** 규격 중앙 인증 서버를 제공하는 Azure AD(Azure Active Directory)와 통합됩니다. Azure AD를 사용하면 Azure RBAC(Azure 역할 기반 액세스 제어)로 클라이언트 ID에 세분화된 권한을 부여할 수 있습니다. 프로토콜에 대해 **SASL_SSL** 을 지정하고 메커니즘에 대해 **OAUTHBEARER** 를 지정하여 Kafka 클라이언트에서 이 기능을 사용할 수 있습니다. 액세스 범위 지정을 위한 Azure 역할 및 수준에 대한 자세한 내용은 [Azure AD로 액세스 권한 부여](authorize-access-azure-active-directory.md)를 참조하세요.

```properties
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler
```

### <a name="shared-access-signature-sas"></a>공유 액세스 서명(SAS)
Event Hubs는 Kafka용 Event Hubs 리소스에 대한 위임된 액세스를 위해 **SAS(공유 액세스 서명)** 도 제공합니다. OAuth 2.0 토큰 기반 메커니즘을 사용하여 액세스 권한을 부여하면 SAS보다 뛰어난 보안과 사용 편의성이 제공됩니다. 기본 제공 역할을 사용하면 사용자가 유지 및 관리해야 하는 ACL 기반 권한 부여도 필요 없습니다. 프로토콜에 대해 **SASL_SSL** 을 지정하고 메커니즘에 대해 **PLAIN** 을 지정하여 Kafka 클라이언트에서 이 기능을 사용할 수 있습니다. 

```properties
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> `{YOUR.EVENTHUBS.CONNECTION.STRING}`을 Event Hubs 네임스페이스의 연결 문자열로 바꿉니다. 연결 문자열을 가져오는 방법에 대한 지침은 [Event Hubs 연결 문자열 가져오기](event-hubs-get-connection-string.md)를 참조하세요. `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";` 구성의 예는 다음과 같습니다.

> [!NOTE]
> Kafka 클라이언트에서 SAS 인증을 사용하는 경우 SAS 키가 다시 생성될 때 설정된 연결이 끊어지지 않습니다. 


#### <a name="samples"></a>샘플 
이벤트 허브를 만들고 SAS 또는 OAuth를 사용하여 액세스하는 단계별 지침이 포함된 **자습서** 는 [빠른 시작: Kafka 프로토콜을 사용하여 Event Hubs로 데이터 스트리밍](event-hubs-quickstart-kafka-enabled-event-hubs.md)을 참조하세요.

Kafka용 Event Hubs에서 OAuth를 사용하는 방법을 보여주는 추가 **샘플** 은 [GitHub의 샘플](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)을 참조하세요.

## <a name="other-event-hubs-features"></a>기타 Event Hubs 기능 

Apache Kafka용 Event Hubs 기능은 Azure Event Hubs에서 동시에 사용 가능한 세 가지 프로토콜 중 하나로 HTTP와 AMQP를 보완합니다. 현재 Apache Kafka 생산자가 Apache Kafka를 통해 계속 게시할 수 있도록 이러한 프로토콜 중 하나로 쓰고 다른 프로토콜로 읽을 수 있지만 읽기 권한자는 Azure Stream Analytics 또는 Azure Functions와 같은 Event Hubs의 AMQP 인터페이스와 기본 통합의 이점을 제공합니다. 반대로 Azure Event Hubs를 대상 엔드포인트로 AMQP 라우팅 네트워크에 쉽게 통합할 수 있지만 Apache Kafka 통합을 통해 데이터를 읽을 수 있습니다.  

또한 Azure Blob Storage와 Azure Data Lake Storage를 통해 매우 비용 효율적인 장기 보관을 가능하게 하는 [캡처](event-hubs-capture-overview.md)와 같은 Event Hubs 기능과 [지리적 재해 복구](event-hubs-geo-dr.md)는 Kafka용 Event Hubs 기능에서도 작동합니다.

## <a name="apache-kafka-feature-differences"></a>Apache Kafka 기능 차이점 

Apache Kafka용 Event Hubs의 목표는 Apache Kafka API에 잠겨 있고 그렇지 않으면 Apache Kafka 클러스터에서 지원해야 하는 애플리케이션에 Azure Event Hub의 기능에 대한 액세스를 제공하는 것입니다. 

[위에서](#is-apache-kafka-the-right-solution-for-your-workload) 설명한 것처럼 Azure Messaging 집합은 다양한 메시징 시나리오에 대해 풍부하고 강력한 적용 범위를 제공합니다. 다음 기능은 현재 Apache Kafka API에 대한 Event Hubs의 지원을 통해 지원되지 않지만 원하는 기능을 사용할 수 있는 위치와 방법을 알려드립니다.

### <a name="transactions"></a>트랜잭션

[Azure Service Bus](../service-bus-messaging/service-bus-transactions.md)에는 트랜잭션의 일관성 보호하에 메시지 처리로 인한 아웃바운드 메시지를 여러 대상 엔터티로 보내는 동안 메시지와 세션을 송수신할 수 있는 강력한 트랜잭션 지원이 있습니다. 이 기능 세트는 시퀀스의 각 메시지를 정확히 한 번만 처리할 수 있을 뿐만 아니라 Apache Kafka의 경우와 같이 다른 소비자가 동일한 메시지를 실수로 다시 처리하는 위험을 방지합니다. Service Bus는 트랜잭션 메시지 워크로드에 권장되는 서비스입니다.

### <a name="compression"></a>압축

Apache Kafka의 클라이언트측 [압축](https://cwiki.apache.org/confluence/display/KAFKA/Compression) 기능은 여러 메시지의 배치를 생산자측의 단일 메시지로 압축하고 소비자측에서 해당 배치의 압축을 풉니다. Apache Kafka 브로커는 배치를 특수 메시지로 처리합니다.

이 기능은 기본적으로 Azure Event Hubs의 다중 프로토콜 모델과 상충되므로 배치로 전송된 메시지도 브로커와 모든 프로토콜에서 개별적으로 검색할 수 있습니다. 

Event Hub 이벤트의 페이로드는 바이트 스트림이며 선택한 알고리즘을 사용하여 콘텐츠를 압축할 수 있습니다. Apache Avro 인코딩 형식은 기본적으로 압축을 지원합니다.

### <a name="log-compaction"></a>로그 압축

Apache Kafka 로그 압축은 파티션에서 각 키의 마지막 레코드를 제외한 모든 항목을 제거할 수 있는 기능으로, Apache Kafka 항목을 마지막으로 추가된 값이 이전 값을 재정의하는 키-값 저장소로 효과적으로 변환합니다. 이 기능은 현재 Azure Event Hubs에 의해 구현되지 않습니다. 키-값 저장소 패턴은 자주 업데이트하더라도 [Azure Cosmos DB](../cosmos-db/introduction.md)와 같은 데이터베이스 서비스에서 훨씬 더 잘 지원됩니다. 자세한 내용은 [로그 프로젝션](event-hubs-federation-overview.md#log-projections)을 참조하세요. 

### <a name="kafka-streams"></a>Kafka 스트림

Kafka Streams는 Apache Kafka 오픈 소스 프로젝트의 일부이지만 Apache Kafka 이벤트 스트림 브로커와는 별개인 스트림 분석용 클라이언트 라이브러리입니다. 

Azure Event Hubs 고객이 Kafka Streams 지원을 요청하는 가장 일반적인 이유는 Confluent의 "ksqlDB" 제품에 관심이 있기 때문입니다. "ksqlDB"는 "서비스 제공 소프트웨어, 서비스 제공 플랫폼, 서비스 제공 인프라 또는 Confluent 제품이나 서비스와 경쟁하는 기타 유사한 온라인 서비스를 제공"하는 공급업체가 "ksqlDB" 지원을 사용하거나 제공할 수 없도록 [사용이 허가된](https://github.com/confluentinc/ksql/blob/master/LICENSE) 독점 공유 원본 프로젝트입니다. 실제로 ksqlDB를 사용하는 경우 Kafka를 직접 운영하거나 Confluent의 클라우드 제품을 사용해야합니다. 라이선스 조건은 라이선스에서 제외된 목적으로 서비스를 제공하는 Azure 고객에게도 영향을 줄 수 있습니다.

독립 실행형이며 ksqlDB가 없는 Kafka Streams는 많은 대체 프레임워크와 서비스보다 기능이 적으며 대부분은 기본 제공 스트리밍 SQL 인터페이스가 있으며 현재 모두 Azure Event Hubs와 통합됩니다.

- [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)
- [Azure Synapse Analytics(Event Hubs 캡처를 통해)](../event-grid/event-grid-event-hubs-integration.md)
- [Azure Databricks](/azure/databricks/scenarios/databricks-stream-from-eventhubs)
- [Apache Samza](https://samza.apache.org/learn/documentation/latest/connectors/eventhubs)
- [Apache Storm](event-hubs-storm-getstarted-receive.md)
- [Apache Spark](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink](event-hubs-kafka-flink-tutorial.md)
- [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)

나열된 서비스와 프레임워크는 일반적으로 어댑터를 통해 다양한 원본 세트에서 직접 이벤트 스트림과 참조 데이터를 가져올 수 있습니다. Kafka Streams는 Apache Kafka에서만 데이터를 가져올 수 있으므로 분석 프로젝트는 Apache Kafka에 고정됩니다. 다른 원본의 데이터를 사용하려면, 먼저 Kafka Connect 프레임워크를 사용하여 Apache Kafka로 데이터를 가져와야 합니다.

Azure에서 Kafka Streams 프레임워크를 사용해야 하는 경우 [HDInsight의 Apache Kafka](../hdinsight/kafka/apache-kafka-introduction.md)가 해당 옵션을 제공합니다. HDInsight의 Apache Kafka는 Apache Kafka의 모든 구성 측면에 대한 모든 권한을 제공하는 동시에 오류/업데이트 도메인 배치에서 네트워크 격리, 모니터링 통합에 이르는 Azure 플랫폼의 다양한 측면과 완벽하게 통합됩니다. 

## <a name="next-steps"></a>다음 단계
이 문서는 Kafka용 Event Hubs에 대한 소개를 제공합니다. 자세한 내용은 [Azure Event Hubs용 Apache Kafka 개발자 가이드](apache-kafka-developer-guide.md)를 참조하세요.
