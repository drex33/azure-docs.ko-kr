---
title: 포함 파일
description: 포함 파일
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 72c98f01224e70febb5ae6189682f1275ca811f6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132354063"
---
## <a name="what-is-a-replication-task"></a>복제 작업이란 무엇인가요?

복제 작업은 원본에서 이벤트를 수신하고 대상으로 전달합니다. 대부분의 복제 작업은 변경되지 않은 이벤트를 전달하고 원본 및 대상 프로토콜이 다른 경우 메타데이터 구조 간 매핑을 최대한으로 수행합니다.

복제 작업은 일반적으로 상태 비저장입니다. 즉, 작업의 순차적 또는 병렬 실행에 대한 상태 또는 기타 부작용을 공유하지 않습니다. 일괄 처리 및 연결의 경우에도 마찬가지입니다. 두 경우 모두 스트림의 기존 상태를 기반으로 구현될 수 있습니다.

따라서 복제 작업은 일반적으로 상태를 저장하는 집계 작업과 다르며 [Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md)와 같은 분석 프레임워크 및 서비스의 도메인입니다.

## <a name="replication-applications-and-tasks-in-azure-functions"></a>Azure Functions의 복제 애플리케이션 및 작업

Azure Functions에서 복제 태스크는 구성 된 원본에서 입력 메시지를 가져오는 [트리거와](../articles/azure-functions/functions-triggers-bindings.md) 원본에서 구성 된 대상으로 복사 된 메시지를 전달 하는 [출력 바인딩을](../articles/azure-functions/functions-triggers-bindings.md#binding-direction) 사용 하 여 구현 됩니다.

| 트리거  | 출력 |
|----------|--------|
| [Azure Event Hubs 트리거](../articles/azure-functions/functions-bindings-event-hubs-trigger.md?tabs=csharp) | [Azure Event Hubs 출력 바인딩](../articles/azure-functions/functions-bindings-event-hubs-output.md?tabs=csharp) |
| [Azure Service Bus 트리거](../articles/azure-functions/functions-bindings-service-bus-trigger.md?tabs=csharp) | [Azure Service Bus 출력 바인딩](../articles/azure-functions/functions-bindings-service-bus-output.md?tabs=csharp) |
| [Azure IoT Hub 트리거](../articles/azure-functions/functions-bindings-event-iot-trigger.md?tabs=csharp) | [Azure IoT Hub 출력 바인딩](../articles/azure-functions/functions-bindings-event-iot-output.md?tabs=csharp) |
| [Azure Event Grid 트리거](../articles/azure-functions/functions-bindings-event-grid-trigger.md?tabs=csharp) | [Azure Event Grid 출력 바인딩](../articles/azure-functions/functions-bindings-event-grid-output.md?tabs=csharp) |
| [Azure Queue Storage 트리거](../articles/azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp) | [Azure Queue Storage 출력 바인딩](../articles/azure-functions/functions-bindings-storage-queue-output.md?tabs=csharp) |
| [Apache Kafka 트리거](https://github.com/azure/azure-functions-kafka-extension) | [Apache Kafka 출력 바인딩](https://github.com/azure/azure-functions-kafka-extension) |
| [RabbitMQ 트리거](https://github.com/azure/azure-functions-rabbitmq-extension) | [RabbitMQ 출력 바인딩](https://github.com/azure/azure-functions-rabbitmq-extension) |
| | [Azure Notification Hubs 출력 바인딩](../articles/azure-functions/functions-bindings-notification-hubs.md) |
| | [Azure SignalR Service 출력 바인딩](../articles/azure-functions/functions-bindings-signalr-service-output.md?tabs=csharp) |
| | [Twilio SendGrid 출력 바인딩](../articles/azure-functions/functions-bindings-sendgrid.md?tabs=csharp) |

복제 태스크는 다른 Azure Functions 응용 프로그램과 동일한 배포 방법으로 복제 응용 프로그램에 배포 됩니다. 동일한 애플리케이션에 여러 작업을 구성할 수 있습니다.

Azure Functions 프리미엄을 사용하면 여러 복제 애플리케이션이 App Service 플랜이라는 동일한 기본 리소스 풀을 공유할 수 있습니다. 즉, 예를 들어 Java로 작성된 복제 작업을 사용하여 .NET으로 작성된 복제 작업을 쉽게 배치할 수 있습니다. Java에만 사용할 수 있는 Apache 낙 타와 같은 특정 라이브러리를 활용 하 고, 다른 복제 작업에 다른 언어와 런타임을 자주 선호 하는 경우에도 특정 통합 경로에 가장 적합 한 옵션을 사용 하는 것이 중요 합니다.

사용 가능하다면 개별 이벤트나 메시지를 제공하는 트리거를 통해 일괄 처리 지향 트리거를 사용하는 것이 좋습니다. 이 경우에는 항상 Azure Function의 [매개 변수 바인딩 식](../articles/azure-functions/functions-bindings-expressions-patterns.md)을 사용하는 대신 완전한 이벤트 또는 메시지 구조를 가져와야 합니다.

함수 이름에는 연결하려는 소스와 대상의 쌍이 반영되어야 하며, 이 이름을 사용하여 애플리케이션 구성 파일에서 연결 문자열 또는 기타 구성 요소에 대한 참조를 접두사로 사용해야 합니다.

### <a name="data-and-metadata-mapping"></a>데이터 및 메타데이터 매핑

한 쌍의 입력 트리거 및 출력 바인딩을 결정한 후에는 트리거와 출력이 동일한 경우를 제외하고는 다른 이벤트 또는 메시지 유형 간에 몇 가지 매핑을 수행해야 합니다.

Event Hubs와 Service Bus 간에 메시지를 복사하는 간단한 복제 작업의 경우 사용자 고유의 코드를 작성할 필요가 없지만 복제 샘플과 함께 제공되는 [유틸리티 라이브러리](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication)를 사용할 수 있습니다.

### <a name="retry-policy"></a>재시도 정책

복제 함수 어느 쪽에서든 가용성 이벤트 중 데이터 손실을 방지하려면 재시도 정책을 강력하게 구성해야 합니다. [재시도에 대한 Azure Functions 문서](../articles/azure-functions/functions-bindings-error-pages.md)를 참조하여 재시도 정책을 구성합니다.

[샘플 리포지토리](https://github.com/Azure-Samples/azure-messaging-replication-dotnet)의 예제 프로젝트에 대해 선택한 정책 설정은 데이터 손실을 방지하기 위해 재시도 간격을 5초에서 15분으로 설정하여 지수 백오프 전략을 구성합니다.

Service Bus의 경우 트리거의 상호 작용 및 큐에 대해 정의된 최대 제공 횟수를 이해하려면 ["트리거 복원력 위에 재시도 지원 사용"](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) 섹션을 검토하세요.

### <a name="setting-up-a-replication-application-host"></a>복제 애플리케이션 호스트 설정

복제 애플리케이션은 하나 이상의 복제 작업에 대한 실행 호스트입니다.

소비 플랜 또는 Azure Functions 프리미엄 플랜(권장)에서 실행되도록 구성된 Azure Functions 애플리케이션입니다. 모든 복제 응용 프로그램은 [시스템 할당 또는 사용자 할당 관리 id](../articles/app-service/overview-managed-identity.md)로 실행 해야 합니다.

연결된 ARM(Azure Resource Manager) 템플릿은 다음을 사용하여 복제 애플리케이션을 만들고 구성합니다.

* 복제 진행률 및 로그를 추적 하기 위한 Azure Storage 계정입니다.
* 시스템이 할당한 관리 ID.
* Azure Monitor 및 모니터링을 위한 통합 Application Insights.

Azure VNet(가상 네트워크)에 바인딩된 Event Hubs에 액세스해야 하는 복제 애플리케이션은 Azure Functions 프리미엄 플랜을 사용해야 하며, 사용 가능한 옵션 중 하나인 동일한 VNet에 연결하도록 구성해야 합니다.

| | 배포 | 시각화 |
|--|--|--|
| **Azure Functions 소비 계획** | [![Azure에 배포](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json)|[![시각화](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json) |
| **Azure Functions 프리미엄 플랜** |[![Azure에 배포](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) | [![시각화](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) |
| **VNet이 포함된 Azure Functions 프리미엄 플랜** | [![Azure에 배포](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)|[![시각화](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json) |

### <a name="examples"></a>예제

[샘플 리포지토리](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/)에는 Event Hubs 및/또는 Service Bus 엔터티 간에 이벤트를 복사하는 복제 작업의 몇 가지 예제가 포함되어 있습니다.

Event Hubs 간에 이벤트를 복사하기 위해서는 Event Hubs 출력 바인딩과 함께 Event Hubs 트리거를 사용합니다.

```csharp
[FunctionName("telemetry")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task Telemetry(
    [EventHubTrigger("telemetry", ConsumerGroup = "$USER_FUNCTIONS_APP_NAME.telemetry", Connection = "telemetry-source-connection")] EventData[] input,
    [EventHub("telemetry-copy", Connection = "telemetry-target-connection")] EventHubClient outputClient,
    ILogger log)
{
    return EventHubReplicationTasks.ForwardToEventHub(input, outputClient, log);
}
```

Service Bus 엔터티 간에 메시지를 복사하기 위해서는 Service Bus 트리거와 출력 바인딩을 사용합니다.

```csharp
[FunctionName("jobs-transfer")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task JobsTransfer(
    [ServiceBusTrigger("jobs-transfer", Connection = "jobs-transfer-source-connection")] Message[] input,
    [ServiceBus("jobs", Connection = "jobs-target-connection")] IAsyncCollector<Message> output,
    ILogger log)
{
    return ServiceBusReplicationTasks.ForwardToServiceBus(input, output, log);
}
```

도우미 메서드를 사용하면 Event Hubs와 Service Bus 간에 쉽게 복제할 수 있습니다.

| 원본      | 대상      | 진입점 |
|-------------|-------------|------------------------------------------------------------------------
| 이벤트 허브   | 이벤트 허브   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| 이벤트 허브   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | 이벤트 허브   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`

### <a name="monitoring"></a>모니터링

복제 앱을 모니터링하는 방법에 대한 자세한 내용은 Azure Functions 문서의 [모니터링 섹션](../articles/azure-functions/configure-monitoring.md)을 참조하세요.

복제 작업을 모니터링하는 데 특히 유용한 시각적 도구는 Application Insights [애플리케이션 맵](../articles/azure-monitor/app/app-map.md)입니다. 이 도구는 캡처된 모니터링 정보에서 자동으로 생성되고 복제 작업 원본 및 대상 전송의 안정성과 성능을 탐색할 수 있도록 합니다.

즉각적인 진단 정보를 위해서는 로그 세부 정보에 대한 낮은 대기 시간 시각화를 제공하는 [라이브 메트릭](../articles/azure-monitor/app/live-stream.md) 포털 도구를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Functions 배포](../articles/azure-functions/functions-deployment-technologies.md)
* [Azure Functions 진단](../articles/azure-functions/functions-diagnostics.md)
* [Azure Functions 네트워킹 옵션](../articles/azure-functions/functions-networking-options.md)
* [Azure Application Insights](../articles/azure-monitor/app/app-insights-overview.md)
