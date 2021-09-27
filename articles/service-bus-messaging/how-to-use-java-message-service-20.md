---
title: Azure Service Bus 프리미엄에서 Java 메시지 서비스 2.0 API 사용
description: Azure Service Bus로 JMS(Java 메시지 서비스) 사용
ms.topic: article
ms.date: 09/20/2021
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: df85a53652395e7ecdb55c1471ee81a8e49e048b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615642"
---
# <a name="use-java-message-service-20-api-with-azure-service-bus-premium"></a>Azure Service Bus 프리미엄에서 Java 메시지 서비스 2.0 API 사용

본 문서에서는 널리 사용되는 **JMS(Java 메시지 서비스) 2.0** API를 사용하여 AMQP(Advanced Message Queueing Protocol) 1.0 프로토콜을 거쳐 Azure Service Bus와 상호작용하는 방법을 설명합니다.

> [!NOTE]
> JMS(Java 메시지 서비스) 2.0 API는 **Azure Service Bus 프리미엄 계층** 에서만 이용할 수 있습니다.
>

## <a name="pre-requisites"></a>필수 구성 요소

### <a name="get-started-with-service-bus"></a>Service Bus 시작

본 가이드에서는 자신에게 이미 Service Bus 네임스페이스가 있는 것으로 가정합니다. 아직 없는 경우라면 [Azure Portal](https://portal.azure.com)을 사용하여 [네임스페이스 및 큐 만들기](service-bus-create-namespace-portal.md)가 가능합니다. 

Service Bus 네임스페이스와 큐를 만드는 방법에 대한 자세한 내용은 [Azure Portal을 통하여 Service Bus 큐 시작하기](service-bus-quickstart-portal.md)를 참조하세요.

### <a name="set-up-a-java-development-environment"></a>Java 개발 환경 설정하기

Java 애플리케이션을 개발하려면 적절한 개발 환경을 설정하여야 합니다. 
   * JDK(Java 개발 키트)나 JRE(Java Runtime Environment)를 설치합니다.
   * JDK나 JRE를 빌드 경로 및 적절한 시스템 변수에 추가합니다.
   * JDK나 JRE를 활용하기 위하여 Java IDE를 설치합니다. 예를 들면, Eclipse나 IntelliJ가 있습니다.

Azure에서 Java용 개발자 환경을 준비하는 방법에 대한 자세한 내용은, [본 가이드](/azure/developer/java/fundamentals/)를 활용하세요.

## <a name="what-jms-features-are-supported"></a>JMS 기능 중에는 어떤 것들을 지원하나요?

[!INCLUDE [service-bus-jms-features-list](./includes/service-bus-jms-feature-list.md)]

## <a name="downloading-the-java-message-service-jms-client-library"></a>JMS(Java 메시지 서비스) 클라이언트 라이브러리 다운로드하기

Azure Service Bus 프리미엄 계층에서 사용할 수 있는 기능을 모두 활용하려면 해당 프로젝트의 빌드 경로에 아래 라이브러리를 모두 추가하여야 합니다.

[Azure-servicebus-jms](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)

> [!NOTE]
> 빌드 경로에 [Azure-servicebus-jms](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)를 추가하려면, [Maven](https://maven.apache.org/)이나 [Gradle](https://gradle.org/) 같은 프로젝트용 기본 종속성 관리 도구를 활용합니다.
>

## <a name="coding-java-applications"></a>Java 애플리케이션 코딩

종속성을 가져온 뒤에는 Java 애플리케이션을 JMS 공급자에 관계 없이 작성할 수 있습니다.

### <a name="connecting-to-azure-service-bus-using-jms"></a>JMS를 사용하여 Azure Service Bus에 연결하기

JMS 클라이언트를 사용하여 Azure Service Bus에 연결하려면 **기본 연결 문자열** 에 [Azure Portal](https://portal.azure.com) 내의 ‘공유된 액세스 정책’에 사용할 수 있는 **연결 문자열** 이 필요합니다.

1. `ServiceBusJmsConnectionFactorySettings`를 인스턴스화합니다.

    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();
    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. 적절한 `ServiceBusConnectionString`을 통하여 `ServiceBusJmsConnectionFactory`를 인스턴스화합니다.

    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. `ConnectionFactory`를 사용하여 `Connection`과 `Session`을 순서대로 만들거나 

    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    `JMSContext`(JMS 2.0 클라이언트용)를 만듭니다.

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

    >[!IMPORTANT]
    > 이름은 비슷하지만 JMS '세션'과 Service Bus '세션'은 서로 완전히 독립적입니다.
    >
    > JMS 1.1의 Session은 MessageProducer, MessageConsumer와 메시지 자체를 만들 수 있도록 하는 API의 필수적인 빌드 블록입니다. 자세한 내용은 [JMS API 프로그래밍 모델](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html)을 검토하세요.
    >
    > Service Bus의 [세션](message-sessions.md)은 큐와 구독에서 FIFO 처리를 사용하도록 하는 서비스 및 클라이언트 측 구문입니다.
    >

### <a name="write-the-jms-application"></a>JMS 애플리케이션 작성

`Session` 또는 `JMSContext`가 인스턴스화되면, 애플리케이션이 익숙한 JMS API를 이용하여 관리 작업과 데이터 작업을 모두 수행할 수 있습니다.

지원하는 API를 확인하려면 [지원되는 JMS 기능](how-to-use-java-message-service-20.md#what-jms-features-are-supported) 목록을 참조하세요.

다음은 JMS를 시작하는 몇 가지 샘플 코드 조각입니다.

#### <a name="sending-messages-to-a-queue-and-topic"></a>큐와 토픽에 메시지 보내기

```java
// Create the queue and topic
Queue queue = jmsContext.createQueue("basicQueue");
Topic topic = jmsContext.createTopic("basicTopic");
// Create the message
Message msg = jmsContext.createMessage();

// Create the JMS message producer
JMSProducer producer = jmsContext.createProducer();

// send the message to the queue
producer.send(queue, msg);
// send the message to the topic
producer.send(topic, msg);
```

#### <a name="receiving-messages-from-a-queue"></a>큐에서 온 메시지 받기

```java
// Create the queue
Queue queue = jmsContext.createQueue("basicQueue");

// Create the message consumer
JMSConsumer consumer = jmsContext.createConsumer(queue);

// Receive the message
Message msg = (Message) consumer.receive();
```

#### <a name="receiving-messages-from-a-shared-durable-subscription-on-a-topic"></a>토픽에 대하여 공유 지속성 구독에서 온 메시지 받기

```java
// Create the topic
Topic topic = jmsContext.createTopic("basicTopic");

// Create a shared durable subscriber on the topic
JMSConsumer sharedDurableConsumer = jmsContext.createSharedDurableConsumer(topic, "sharedDurableConsumer");

// Receive the message
Message msg = (Message) sharedDurableConsumer.receive();
```

## <a name="summary"></a>요약

본 가이드는 AMQP 1.0을 통하여 JMS(Java 메시지 서비스)를 이용하는 Java 클라이언트 애플리케이션이 Azure Service Bus와 상호작용하는 방법을 보여 줍니다.

.NET, C, Python, PHP 등의 다른 언어에서도 Service Bus AMQP 1.0을 사용할 수 있습니다. 이러한 언어로 빌드한 구성 요소는 Service Bus의 AMQP 1.0 지원을 사용하여 안정적이며 완전히 신뢰할 수 있는 상태로 메시지를 교환할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Service Bus에 대한 자세한 내용과 JMS(Java Message Service) 엔터티 관련 세부 정보는 다음 링크를 확인하세요. 
* [Service Bus - 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus - Java Message Service 엔터티](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [Azure Service Bus의 AMQP 1.0 지원](service-bus-amqp-overview.md)
* [Service Bus AMQP 1.0 개발자 가이드](service-bus-amqp-dotnet.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Java Message Service API(외부 Oracle 문서)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [ActiveMQ에서 Service Bus로의 마이그레이션 방법 알아보기](migrate-jms-activemq-to-servicebus.md)