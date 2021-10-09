---
title: 프로그래밍 방식으로 Azure Service Bus 네임스페이스 및 엔터티 관리
description: 이 문서에서는 동적으로 또는 프로그래밍 방식으로 Service Bus 네임스페이스 및 엔터티를 프로비저닝하는 방법을 설명합니다.
ms.topic: article
ms.date: 08/06/2021
ms.openlocfilehash: 2962d5d8aebcf0a24535b7d9c1538dc69bd11e65
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129709140"
---
# <a name="dynamically-provision-service-bus-namespaces-and-entities"></a>Service Bus 네임스페이스 및 엔터티를 동적으로 프로비저닝 
Azure Service Bus는 Service Bus 네임스페이스 및 엔터티를 동적으로 프로비저닝하는 데 도움이 되는 라이브러리를 제공합니다. 따라서 복잡한 배포 및 메시지 시나리오를 사용할 수 있고 프로비저닝할 엔터티를 프로그래밍 방식으로 결정할 수 있습니다.

## <a name="overview"></a>개요
Azure Service Bus 리소스를 프로그래밍 방식으로 관리하려면 두 가지 방법을 사용할 수 있습니다. 첫 번째는 네임스페이스, 큐, 토픽, 구독, 규칙 및 SAS 정책을 관리할 수 있는 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 기반 라이브러리를 사용하는 것입니다. Azure Resource Manager 기반 라이브러리는 연결 문자열이 아닌 Azure Active Directory를 통한 인증을 지원합니다. 두 번째 방법은 메시지를 보내고 받는 데 사용하는 것과 동일한 Service Bus 클라이언트 라이브러리를 활용하는 것입니다. 또한 클라이언트 라이브러리는 ‘기존’ 네임스페이스에서 큐, 토픽, 구독, 규칙을 관리하는 데 도움이 되는 API를 제공합니다. 이러한 라이브러리는 연결 문자열을 사용한 인증을 지원합니다. 사용할 방법을 결정할 때는 다음 사항을 고려하세요. 

Azure Resource Manager 기반 라이브러리는 큐, 토픽, 구독 등의 Service Bus 네임스페이스 및 엔터티를 관리할 때 Azure Portal, CLI 및 PowerShell과 동일한 기능을 제공합니다. 관리 작업에 Azure Portal, CLI 또는 PowerShell을 사용하고 있고 동적 관리 방법을 원하는 경우에는 이러한 라이브러리를 선택하는 것이 더 적합합니다. 

그러나 메시지 보내기 및 받기와 같은 서비스 관련 작업에 Service Bus 클라이언트 라이브러리를 이미 사용하고 있고 Service Bus 엔터티도 관리해야 하는 경우에는 동일한 라이브러리를 사용하는 것이 더 편리할 수 있습니다. 클라이언트 라이브러리에는 Azure Resource Manager 기반 라이브러리에서 제공하는 관리 기능의 하위 집합을 제공하는 `ServiceBusAdministrationClient`(이전 라이브러리의 `ServiceBusManagementClient`)가 있습니다. Azure Resource Manager 기반 라이브러리를 사용하면 Service Bus 네임스페이스와 엔터티를 모두 관리할 수 있지만 클라이언트 라이브러리를 사용하면 기존 네임스페이스의 엔터티만 관리할 수 있고 네임스페이스 자체는 관리할 수 ‘없다’는 점을 강조해야 합니다.

## <a name="manage-using-azure-resource-manager-based-libraries"></a>Azure Resource Manager 기반 라이브러리를 사용하여 관리

Azure Resource Manager 기반 라이브러리를 사용하면 네임스페이스, 큐, 토픽, 구독, 규칙 및 SAS 정책을 관리할 수 있습니다.  이 라이브러리는 Azure AD(Azure Active Directory)를 사용한 인증‘만’ 지원하고 연결 문자열은 지원하지 않습니다. 

| 언어 | 패키지 | 설명서 | 샘플|
|-|-|-|-|
|.NET | [Microsoft.Azure.Management.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.Management.ServiceBus/) |[Microsoft.Azure.Management.ServiceBus에 대한 API 참조](/dotnet/api/microsoft.azure.management.servicebus)|[.NET](https://github.com/Azure-Samples/service-bus-dotnet-management/tree/a55185bef30d1763c1a8182a3361dbb548bad436) |
| Java | [azure-resourcemanager-servicebus](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-servicebus)|[com.azure.resourcemanager.servicebus에 대한 API 참조](/java/api/com.azure.resourcemanager.servicebus)|[Java](https://github.com/Azure-Samples/service-bus-java-manage-publish-subscribe-with-basic-features/tree/e4718a825e8fcfe58e5921770ff8084da67ccd89)|
| JavaScript |[@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus)|[@azure/arm-servicebus에 대한 API 참조](/javascript/api/@azure/arm-servicebus/)||
|Python|[azure-mgmt-servicebus](https://pypi.org/project/azure-mgmt-servicebus/)|[azure-mgmt-servicebus에 대한 API 참조](/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus)||


### <a name="fluent-net-and-java-libraries"></a>Fluent .NET 및 Java 라이브러리
Azure Resource Manager 기반 라이브러리의 Fluent 버전이 있습니다. 

|언어|패키지|설명서|
|-|-|-|
|.NET|[Microsoft.Azure.Management.ServiceBus.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.ServiceBus.Fluent/) |[Microsoft.Azure.Management.ServiceBus.Fluent에 대한 API 참조](/dotnet/api/microsoft.azure.management.servicebus.fluent) |
| Java|[azure-resourcemanager-servicebus](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-servicebus)|[com.azure.resourcemanager.servicebus.fluent에 대한 API 참조](/java/api/com.azure.resourcemanager.servicebus.fluent) |

## <a name="manage-using-service-bus-client-libraries"></a>Service Bus 클라이언트 라이브러리를 사용하여 관리 

메시지 보내기 및 받기와 같은 작업에 사용되는 Service Bus 클라이언트 라이브러리를 사용하여 ‘기존’ Service Bus 네임스페이스에서 큐, 토픽, 구독 및 규칙을 관리할 수도 있습니다. 이 기능은 최신 라이브러리의 `ServiceBusAdministrationClient`와 이전 라이브러리의 `ServiceBusManagementClient`를 통해서 사용할 수 있습니다. 최신 라이브러리를 사용하는 것이 좋습니다.

### <a name="latest-service-bus-libraries"></a>최신 Service Bus 라이브러리
|언어|패키지|설명서|샘플|
|-|-|-|-|
|.NET|  [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus)|[ServiceBusAdministrationClient](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient)|[.NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)|
|Java|[azure-messaging-servicebus](https://search.maven.org/artifact/com.azure/azure-messaging-servicebus)|[ServiceBusAdministrationAsyncClient](/java/api/com.azure.messaging.servicebus.administration.servicebusadministrationasyncclient), [ServiceBusAdministrationClient](/java/api/com.azure.messaging.servicebus.administration.servicebusadministrationclient)| [Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)|
|JavaScript|[@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus)|[ServiceBusAdministrationClient](/javascript/api/@azure/service-bus/servicebusadministrationclient)|[JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)/[TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)|
|Python|[azure-servicebus](https://pypi.org/project/azure-servicebus/)|[ServiceBusAdministrationClient](/python/api/azure-servicebus/azure.servicebus.management.servicebusadministrationclient)|[Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)|

### <a name="legacy-service-bus-libraries"></a>레거시 Service Bus 라이브러리
|언어|패키지|설명서|샘플|
|-|-|-|-|
|.NET|[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)|[ManagementClient](/dotnet/api/microsoft.azure.servicebus.management.managementclient)|[.NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus)|
|Java|[azure-mgmt-servicebus](https://search.maven.org/artifact/com.microsoft.azure/azure-mgmt-servicebus)|[ManagementClientAsync](/java/api/com.microsoft.azure.servicebus.management.managementclientasync), [ManagementClient](/java/api/com.microsoft.azure.servicebus.management.managementclient)|[Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java)|


## <a name="next-steps"></a>다음 단계
- 최신 Service Bus 라이브러리를 사용하여 큐에서 메시지를 보내고 받기: [.NET](./service-bus-dotnet-get-started-with-queues.md#send-messages-to-the-queue), [Java](./service-bus-java-how-to-use-queues.md), [JavaScript](./service-bus-nodejs-how-to-use-queues.md), [Python](./service-bus-python-how-to-use-queues.md)
- 최신 Service Bus 라이브러리를 사용하여 토픽에 메시지를 보내고 구독에서 메시지 받기: .[NET](./service-bus-dotnet-how-to-use-topics-subscriptions.md),  [Java](./service-bus-java-how-to-use-topics-subscriptions.md), [JavaScript](./service-bus-nodejs-how-to-use-topics-subscriptions.md), [Python](./service-bus-python-how-to-use-topics-subscriptions.md)