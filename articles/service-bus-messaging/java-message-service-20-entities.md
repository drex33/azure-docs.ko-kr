---
title: Azure Service Bus 메시징-Java 메시지 서비스 엔터티
description: 이 문서에서는 Java 메시지 서비스 API를 통해 액세스할 수 있는 Azure Service Bus 메시징 엔터티에 대한 개요를 제공합니다.
ms.topic: article
ms.date: 09/27/2021
ms.openlocfilehash: 6900deff355f10e4274a5268bd21da189a9daf80
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154530"
---
# <a name="java-message-service-jms-20-entities"></a>JMS(Java Message Service) 2.0 엔터티

Azure Service Bus 프리미엄에 연결하고 [Azure Service Bus JMS 라이브러리](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)를 사용하는 클라이언트 애플리케이션은 다음 엔터티를 사용할 수 있습니다.

## <a name="queues"></a>큐

JMS의 큐는 기존 [Service Bus 큐](service-bus-queues-topics-subscriptions.md#queues)와 의미 체계적으로 유사합니다.

큐를 만들려면 `JMSContext` 클래스에서 아래 메서드를 사용합니다.

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>토픽

JMS의 토픽은 기존 [Service Bus 토픽](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)과 의미 체계적으로 유사합니다.

토픽을 만들려면 `JMSContext` 클래스에서 아래 메서드를 사용합니다.

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>임시 큐

클라이언트 애플리케이션에 애플리케이션 수명 동안 존재하는 임시 엔터티가 필요한 경우 임시 큐를 사용할 수 있습니다. 이러한 엔터티는 [요청-회신](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) 패턴에 사용됩니다.

임시 큐를 만들려면 `JMSContext` 클래스에서 아래 메서드를 사용합니다.

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>임시 토픽

임시 큐와 마찬가지로, 임시 토픽은 애플리케이션 수명 동안 존재하는 임시 엔터티를 통한 게시/구독을 사용하기 위해 존재합니다.

임시 토픽을 만들려면 `JMSContext` 클래스에서 아래 메서드를 사용합니다.

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>JMS(Java 메시지 서비스) 구독

이들은 [구독](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)과 의미 체계적으로 유사하지만(즉, 토픽에 존재하고 게시/구독 의미 체계를 사용) Java 메시지 서비스 사양에서는 특정 구독에 대한 **공유**, **공유되지 않음**, **지속형** 및 **비지속형** 특성의 개념이 도입되었습니다.

> [!NOTE]
> 아래 구독은 Azure Service Bus 프리미엄 계층에서 [Azure Service Bus JMS 라이브러리](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)를 사용하여 Azure Service Bus에 연결하는 클라이언트 애플리케이션에 사용할 수 있습니다.
>
> 지속형 구독은 Azure Portal을 사용해야만 만들 수 있습니다.
>

### <a name="shared-durable-subscriptions"></a>공유되는 지속형 구독

공유되는 지속형 구독은 애플리케이션에서 항상 적극적으로 구독을 사용하는지 여부에 관계없이 애플리케이션이 토픽에 게시된 모든 메시지를 수신하고 처리하는 경우에 사용됩니다.

Service Bus에서 수신하도록 인증된 모든 애플리케이션은 공유되는 지속형 구독에서 수신할 수 있습니다.

공유되는 지속형 구독을 만들려면 `JMSContext` 클래스에서 다음 메서드를 사용합니다.

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

`JMSContext` 클래스의 `unsubscribe` 메서드를 사용하여 삭제하지 않는 한 공유되는 지속형 구독은 계속 존재합니다.

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>공유되지 않는 지속형 구독

공유되는 지속형 구독과 마찬가지로, 공유되지 않는 지속형 구독은 애플리케이션에서 적극적으로 구독을 사용하는지 여부에 관계없이 애플리케이션이 토픽에 게시된 모든 메시지를 수신하고 처리하는 경우에 사용됩니다.

그러나 공유되지 않는 구독이므로 구독을 만든 애플리케이션만 이 구독에서 수신할 수 있습니다.

공유되지 않는 지속형 구독을 만들려면 `JMSContext` 클래스에서 다음 메서드를 사용합니다. 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal` 기능은 현재 지원되지 않으며 무시됩니다.
>

`JMSContext` 클래스의 `unsubscribe` 메서드를 사용하여 삭제하지 않는 한 공유되지 않는 지속형 구독은 계속 존재합니다.

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>공유되는 비지속형 구독

공유되는 비지속형구독은 여러 클라이언트 애플리케이션이 단일 구독에서 메시지를 수신하고 처리해야 하는 경우 애플리케이션이 적극적으로 해당 구독에서 소비/수신하는 동안만 사용됩니다.

이 구독은 비지속형이므로 유지되지 않습니다. 활성 소비자가 없는 경우에는 이 구독이 메시지를 수신하지 않습니다.

공유되는 비지속형 구독을 만들려면 `JmsConsumer` 클래스에서 아래 메서드와 같이 `JMSContext`를 만듭니다.

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

공유되는 비지속형 구독은 해당 구독에서 수신하는 활성 소비자가 있는 동안 계속 존재합니다.

### <a name="unshared-non-durable-subscriptions"></a>공유되지 않는 비지속형 구독

공유되지 않는 비지속형 구독은 클라이언트 애플리케이션이 구독에서 메시지를 수신하고 처리해야 하는 경우 애플리케이션이 적극적으로 해당 구독에서 소비하는 동안만 사용됩니다. 이 구독에는 하나의 소비자, 즉 구독을 만든 클라이언트만 있을 수 있습니다.

이 구독은 비지속형이므로 유지되지 않습니다. 활성 소비자가 없는 경우에는 이 구독이 메시지를 수신하지 않습니다.

공유되지 않는 비지속형 구독을 만들려면 `JMSContext` 클래스에서 아래 메서드와 같이 `JMSConsumer`를 만듭니다.

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal` 기능은 현재 지원되지 않으며 무시됩니다.
>

공유되지 않는 비지속형 구독은 해당 구독에서 수신하는 활성 소비자가 있는 동안 계속 존재합니다.

### <a name="message-selectors"></a>메시지 선택기

일반 Service Bus 구독에 **필터 및 작업** 이 있는 것과 마찬가지로, JMS 구독에는 **메시지 선택기** 가 있습니다.

메시지 선택기는 각 JMS 구독에서 설정할 수 있으며 메시지 헤더 속성에 필터 조건으로 존재합니다. 메시지 선택기 식과 일치하는 헤더 속성이 있는 메시지만 배달됩니다. Null 또는 빈 문자열 값은 JMS 구독/소비자에 대한 메시지 선택기가 없음을 나타냅니다.

## <a name="additional-concepts-for-java-message-service-jms-20-subscriptions"></a>JMS(Java 메시지 서비스) 2.0 구독 추가 개념

### <a name="client-scoping"></a>클라이언트 범위 지정

JMS(Java 메시지 서비스) 2.0 API에 지정된 구독은 특정 클라이언트 애플리케이션으로 범위가 지정될 수도 있고 안 될 수도 있습니다(`clientId`로 식별됨).

구독은 일단 범위가 지정되면 동일한 클라이언트 ID를 가진 클라이언트 애플리케이션에서만 **액세스할 수 있습니다**. 

특정 클라이언트 ID(예 clientId1)로 범위가 지정된 구독에 다른 클라이언트 ID(예 clientId2)를 사용하는 애플리케이션이 액세스하려고 하면 다른 클라이언트 ID(clientId2)로 범위가 지정된 다른 구독이 만들어집니다.

> [!NOTE]
> 클라이언트 ID는 Null이거나 비어 있을 수 있지만 JMS 클라이언트 애플리케이션에 설정된 클라이언트 ID와 일치해야 합니다. Azure Service Bus 관점에서 Null 클라이언트 ID와 빈 클라이언트 ID는 동작이 동일합니다.
>
> 클라이언트 ID가 Null 또는 빈 값으로 설정된 경우 클라이언트 ID가 Null 또는 빈 값으로 설정되어 있는 클라이언트 애플리케이션에만 액세스할 수 있습니다.
>

### <a name="shareability"></a>공유 가능성

**공유되는** 구독은 여러 클라이언트/소비자(즉, JMSConsumer 개체)가 메시지를 수신하도록 허용합니다.

>[!NOTE]
> 특정 클라이언트 ID로 범위가 지정된 공유되는 구독은 여러 클라이언트/소비자(예: JMSConsumer 개체)가 액세스할 수 있지만 각 클라이언트 애플리케이션은 클라이언트 ID가 동일해야 합니다.
>
 

**공유되지 않는** 구독은 단일 클라이언트/소비자(즉, JMSConsumer 개체)만 메시지를 수신하도록 허용합니다. 이미 활성 `JMSConsumer`가 해당 구독에서 메시지를 수신 대기하는 상태에서 공유되지 않은 구독에 대해 `JMSConsumer`를 만드는 경우 `JMSException`이 throw됩니다.


### <a name="durability"></a>내구성

**지속형** 구독은 애플리케이션(`JMSConsumer`)이 해당 구독에서 메시지를 소비하고 있는지 여부에 관계없이 유지되고 토픽에서 메시지를 계속 수집합니다.

**비지속형** 구독은 유지되지 않고 애플리케이션(`JMSConsumer`)이 해당 구독에서 메시지를 소비하는 동안만 토픽에서 메시지를 수집합니다. 

## <a name="representation-of-client-scoped-subscriptions"></a>클라이언트 범위 구독의 표현

클라이언트 범위(JMS) 구독을 기존 [구독](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)과 함께 사용해야 하는 경우 클라이언트 범위(JMS) 구독이 표현되는 방식은 아래 형식을 따릅니다.

   * **\<SUBSCRIPTION-NAME\>** $ **\<CLIENT-ID\>** $**D**(지속형 구독)
   * **\<SUBSCRIPTION-NAME\>** $ **\<CLIENT-ID\>** $**ND**(비지속형 구독)

여기서 **$** 는 구분 기호입니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징을 사용하는 방법에 대한 자세한 내용 및 예제는 다음에 나오는 고급 항목을 참조하세요.

* [Service Bus 메시징 개요](service-bus-messaging-overview.md)
* [Azure Service Bus 프리미엄에서 Java 메시지 서비스 2.0 API 사용](how-to-use-java-message-service-20.md)



