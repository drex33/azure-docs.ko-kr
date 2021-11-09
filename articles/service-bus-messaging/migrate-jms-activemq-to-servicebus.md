---
title: Apache ActiveMQ에서 Azure Service Bus로 JMS (Java Message Service) 애플리케이션 마이그레이션 | Microsoft Docs
description: 이 문서에서는 Azure Service Bus와 상호작용하기 위해 Apache ActiveMQ와 상호작용하는 기존 JMS 애플리케이션을 마이그레이션하는 방법을 설명합니다.
services: service-bus-messaging
documentationcenter: ''
author: spelluru
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2021
ms.author: spelluru
ms.custom: devx-track-java
ms.openlocfilehash: 6009d3ee7ce1ea55e9ccd3191e4d1bb2eef09ea4
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063499"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-apache-activemq-to-azure-service-bus"></a>Apache ActiveMQ에서 Azure Service Bus로 기존 JMS(Java Message Service) 2.0 애플리케이션 마이그레이션

이 문서에서는 Azure Service Bus와 상호작용하는 대신 JMS 브로커와 상호작용하는 기존 JMS(Java Message Service) 2.0 애플리케이션을 수정하는 방법에 대해 설명합니다. 특히 이 문서에서는 Apache ActiveMQ 또는 Amazon MQ에서 마이그레이션하는 방법에 대해 설명합니다.

Azure Service Bus는 AMQP (Advanced Message Queuing Protocol)를 통해 JMS 2.0 API를 사용하는 Java 2 Platform, Enterprise Edition 및 스프링 워크로드를 지원합니다.

## <a name="before-you-start"></a>시작하기 전에

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Azure Service Bus와 Apache ActiveMQ의 차이

Azure Service Bus와 Apache ActiveMQ는 모두 메시지 브로커이며, 클라이언트 애플리케이션에서 메시지를 보내고 받을 수 있는 JMS 공급자 역할을 합니다. 이러한 두 가지 모두 큐를 사용하여 지점 간 의미 체계를 사용하도록 설정하고, 토픽 및 구독과 함께 게시-구독 의미 체계를 사용하도록 설정합니다. 

그러나 다음 표에서 볼 수 있듯이 둘 사이 몇 가지 차이점도 있습니다.

| 범주 | ActiveMQ | Azure Service Bus |
| --- | --- | --- |
| 애플리케이션 계층화 | 클러스터된 거대한 단일 조직 | 2 계층 <br> (게이트웨이 + 백 엔드) |
| 프로토콜 지원 | <ul> <li>AMQP</li> <li> STOMP </li> <li> OpenWire </li> </ul> | AMQP |
| 프로비저닝 모드 | <ul> <li> 서비스형 인프라(IaaS), 온-프레미스 </li> <li> Amazon MQ (서비스형 관리 platform) </li> | 서비스형 관리 platform(PaaS) |
| 메시지 크기 | 고객 구성 가능 | 100MB(Premium 계층) |
| 고가용성 | 고객 관리 | 플랫폼 관리 |
| 재해 복구 | 고객 관리 | 플랫폼 관리 | 

### <a name="current-supported-and-unsupported-features"></a>지원되는/지원되지 않는 기능

[!INCLUDE [service-bus-jms-features-list](./includes/service-bus-jms-feature-list.md)]

### <a name="considerations"></a>고려 사항

Azure Service Bus의 2 계층 특성은 다양한 비즈니스 연속성 기능 (고가용성 및 재해 복구)을 제공합니다. 그러나 JMS 기능을 사용하는 경우 몇 가지 고려 사항이 있습니다.

#### <a name="service-upgrades"></a>서비스 업그레이드

Service bus를 업그레이드 및 다시 시작하는 경우 임시 큐 또는 항목이 삭제됩니다. 애플리케이션이 임시 큐 또는 토픽에서 데이터 손실에 민감한 경우 임시 큐 또는 토픽을 사용하지 마세요. 대신 내구성이 있는 큐, 토픽 및 구독을 사용하십시오.

#### <a name="data-migration"></a>데이터 마이그레이션

Azure Service Bus와 상호작용하도록 클라이언트 애플리케이션을 마이그레이션 및 수정하는 과정에서 ActiveMQ에 저장된 데이터는 Service Bus로 마이그레이션되지 않습니다. ActiveMQ 큐, 토픽 및 구독을 빼낸 다음 Service Bus의 큐, 토픽 및 구독으로 메시지를 재생하려면 사용자 지정 애플리케이션이 필요할 수 있습니다.

#### <a name="authentication-and-authorization"></a>인증 및 권한 부여

Azure Active Directory에서 지원하는 Azure RBAC (Azure 역할 기반 액세스 제어)는 Service Bus에 대한 우선적인 인증 메커니즘입니다. Azure RBAC 또는 클레임 기반 인증은 현재 Apache QPID JMS에서 지원되지 않으므로 인증을 위해 SAS 키를 사용해야 합니다.

## <a name="pre-migration"></a>사전 마이그레이션

### <a name="version-check"></a>버전 확인

JMS 애플리케이션을 작성하는 동안 다음 구성 요소 및 버전을 사용합니다. 

| 구성 요소 | 버전 |
|---|---|
| Java Message Service (JMS) API | 1.1 또는 그 이상 |
| AMQP 프로토콜 | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>AMQP 포트가 열려있는지 확인합니다.

Service Bus는 AMQP 프로토콜을 통한 통신을 지원합니다. 이러한 목적을 위해 포트 5671 (AMQP) 및 443 (TCP)을 통한 통신을 사용하도록 설정합니다. 클라이언트 애플리케이션이 호스팅되는 위치에 따라 이러한 포트를 통한 통신을 허용하는 지원 티켓이 필요할 수 있습니다.

> [!IMPORTANT]
> Service Bus는 AMQP 1.0 프로토콜만 지원합니다.

### <a name="set-up-enterprise-configurations"></a>엔터프라이즈 구성 설정

Service Bus를 통해 다양한 엔터프라이즈 보안 및 고가용성 기능을 사용할 수 있습니다. 자세한 내용은 다음을 참조하세요. 

  * [가상 네트워크 서비스 엔드포인트](service-bus-service-endpoints.md)
  * [방화벽](service-bus-ip-filtering.md)
  * [고객 관리 키로 서버쪽 암호화 (BYOK)](configure-customer-managed-key.md)
  * [프라이빗 엔드포인트](private-link-service.md)
  * [인증 및 권한 부여](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>모니터링, 경고 및 추적

각 Service Bus 네임 스페이스에 대해 Azure Monitor에 메트릭을 게시합니다. 이러한 메트릭을 사용하여 네임 스페이스에 할당된 리소스의 경고 및 동적 크기 조정을 할 수 있습니다.

다른 메트릭과의 차이 및 그에 대한 경고를 설정하는 방법에 대한 자세한 내용은 [Azure Monitor에서 Service Bus 메트릭](monitor-service-bus-reference.md)을 참조하세요. [관리 작업에 대한 데이터 작업과 운영/진단 로깅](monitor-service-bus-reference.md#resource-logs)에 대한 [클라이언트쪽 추적](service-bus-end-to-end-tracing.md)에 대해 자세히 알아볼 수도 있습니다.

### <a name="metrics---new-relic"></a>메트릭-New Relic

ActiveMQ 맵의 메트릭에서 Azure Service Bus의 메트릭 까지의 상관관계를 보여줄 수 있습니다. New Relic 웹 사이트에서 다음 항목을 참조하세요.

  * [ActiveMQ/Amazon MQ New Relic 메트릭](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Azure Service Bus New Relic 메트릭](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> 현재 New Relic에는 ActiveMQ와의 직접적이고 원활한 통합이 없지만, Amazon MQ에 사용할 수 있는 메트릭이 있습니다. Amazon MQ는 ActiveMQ에서 파생되므로 다음 테이블 맵에서는 Amazon MQ의 New Relic 메트릭을 Azure Service Bus에 매핑합니다.
>

|메트릭 그룹화| Amazon MQ/ActiveMQ 메트릭 | Azure Service Bus 메트릭 |
|------------|---------------------------|--------------------------|
|Broker|`CpuUtilization`|`CPUXNS`|
|Broker|`MemoryUsage`|`WSXNS`|
|Broker|`CurrentConnectionsCount`|`activeConnections`|
|Broker|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Broker|`InactiveDurableTopicSubscribersCount`|구독 메트릭 사용하기|
|Broker|`TotalMessageCount`|queue/topic/subscription level `activeMessages` 사용|
|Queue/Topic|`EnqueueCount`|`incomingMessages`|
|Queue/Topic|`DequeueCount`|`outgoingMessages`|
|큐|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>마이그레이션

Service Bus와 상호 작용하도록 기존 JMS 2.0 애플리케이션을 마이그레이션하려면 다음 몇 개 섹션의 단계를 수행합니다.

### <a name="export-the-topology-from-activemq-and-create-the-entities-in-service-bus-optional"></a>ActiveMQ에서 토폴로지를 내보내고 Service Bus에서 엔터티를 만듭니다 (선택 사항).

클라이언트 애플리케이션이 Service Bus와 원활하게 연결할 수 있도록 하려면 Apache ActiveMQ에서 Service Bus로 토폴로지 (큐, 토픽 및 구독 포함)를 마이그레이션합니다.

> [!NOTE]
> JMS 애플리케이션의 경우 런타임 작업으로 큐, 토픽 및 구독을 만듭니다. 대부분의 JMS 공급자 (메시지 브로커)는 런타임에 이러한 공급자를 만드는 기능을 제공합니다. 이러한 이유 때문에 이 내보내기 단계가 선택 사항으로 간주됩니다. 애플리케이션에 런타임에 토폴로지를 만들 수 있는 권한이 있는지 확인하려면 SAS `Manage` 권한으로 연결 문자열을 사용합니다.

가상 하드 디스크 파일에 대한 중요 정보를 제공하려면

1. [ActiveMQ 명령 줄 도구](https://activemq.apache.org/activemq-command-line-tools-reference)를 사용하여 토폴로지를 내보냅니다.
1. [Azure Resource Manager 템플릿을](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)사용하여 동일한 토폴로지를 다시 만듭니다.
1. Azure Resource Manager 템플릿을 실행합니다.


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>Service Bus JMS 구현에 대한 maven 종속성 가져오기

Service Bus와 원활하게 연결되도록 하려면 다음과 같이 `azure-servicebus-jms` 패키지를 Maven `pom.xml` 파일에 대한 종속성으로 추가합니다.

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>애플리케이션 서버 구성 변경

이 파트는 ActiveMQ에 연결하는 클라이언트 애플리케이션을 호스팅하는 애플리케이션 서버에 맞게 사용자 지정됩니다.

#### <a name="spring-applications"></a>Spring 애플리케이션

##### <a name="update-the-applicationproperties-file"></a>`application.properties` 파일을 업데이트합니다.

Spring 부팅 애플리케이션을 사용하여 ActiveMQ에 연결하는 경우 `application.properties` 파일에서 ActiveMQ 별 속성을 제거하려고 합니다.

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

그런 다음 Service Bus 관련 속성을 `application.properties` 파일에 추가합니다.

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>`ActiveMQConnectionFactory`을 `ServiceBusJmsConnectionFactory`로 바꿉니다.

다음 단계는 `ActiveMQConnectionFactory` 인스턴스를 `ServiceBusJmsConnectionFactory`로 교체하는 것입니다.

> [!NOTE] 
> 실제 코드 변경은 애플리케이션에 따라 달라지며 의존성을 관리하는 방법을 제시하지만, 다음 샘플은 무엇이 변경되어야 하는지에 대한 참고 자료를 제공합니다.
>

이전에는 다음과 같이 `ActiveMQConnectionFactory`의 개체를 인스턴스화했을 수도 있습니다.

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

이제 다음과 같이 이를 변경하여 `ServiceBusJmsConnectionFactory`의 개체를 인스턴스화합니다.

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>마이그레이션 후 작업

이제 애플리케이션을 수정하여 Service Bus에서 메시지를 보내고 받기 시작 했으므로, 정상적으로 작동하는지 확인해야 합니다. 이 작업이 완료되면 계속해서 애플리케이션 스택을 개선하고 현대화할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Service Bus JMS용 Spring Boot Starter를](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) 사용하여 Service Bus와 매끄럽게 통합합니다.

Service Bus 메시지와 JMS에 대해 자세히 알아보려면 다음 링크를 참조하세요.

* [Service Bus JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
