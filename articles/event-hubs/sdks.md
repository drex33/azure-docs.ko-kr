---
title: Azure Event Hubs-클라이언트 SDK | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs의 클라이언트 SDK에 대한 정보를 제공합니다.
ms.topic: article
ms.date: 12/01/2021
ms.openlocfilehash: a89fbfca98a0093fe800c157bf0bfd1db99790d5
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133481958"
---
# <a name="azure-event-hubs---client-sdks"></a>Azure Event Hubs - 클라이언트 SDK
이 문서에서는 Azure Event Hubs에서 지원하는 SDK에 대해 다음과 같은 정보를 제공합니다. 

- 애플리케이션에서 사용할 수 있는 패키지의 위치 
- 소스 코드, 샘플, 추가 정보, 변경 로그, 보고된 문제를 찾고 새 문제를 발생시킬 수 있는 GitHub 위치 
- 빠른 시작 자습서 링크 

## <a name="client-sdks"></a>클라이언트 SDK
다음 표에서는 사용 가능한 모든 최신 Azure Event Hubs 런타임 클라이언트에 대해 설명합니다. 이러한 라이브러리의 핵심적인 부분은 이벤트 허브에서 **메시지를 주고 받는** 것입니다.

| 언어 | 패키지 | 참조 | 
| -------- | ------- | --------------- | 
| . NET Standard | [Azure.Messaging.EventHubs](https://www.nuget.org/packages/Azure.Messaging.EventHubs/) |<ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs)</li><li>[자습서](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
|       | [Azure.Messaging.EventHubs.Processor](https://www.nuget.org/packages/Azure.Messaging.EventHubs.Processor/) | <ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor)</li><li>[자습서](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| Java | [azure-messaging-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs) | <ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs)</li><li>[자습서](event-hubs-java-get-started-send.md)</li></ul> |
|      | [azure-messaging-eventhubs-checkpointstore-blob](https://search.maven.org/search?q=a:azure-messaging-eventhubs-checkpointstore-blob) | <ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob)</li><li>[자습서](event-hubs-java-get-started-send.md)</li></ul> |
| Python |  [azure-eventhub](https://pypi.org/project/azure-eventhub/) | <ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub)</li><li>[자습서](event-hubs-python-get-started-send.md)</li></ul> |
|        | [azure-eventhub-checkpointstoreblob-aio](https://pypi.org/project/azure-eventhub-checkpointstoreblob-aio/) | <ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio)</li><li>[자습서](event-hubs-python-get-started-send.md)</li></ul> |
| JavaScript | [azure/event-hubs](https://www.npmjs.com/package/@azure/event-hubs) | <ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs)</li><li>[자습서](event-hubs-node-get-started-send.md)</li></ul> |
|            | [azure/eventhubs-checkpointstore-blob](https://www.npmjs.com/package/@azure/eventhubs-checkpointstore-blob) | <ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/eventhubs-checkpointstore-blob)</li><li>[자습서](event-hubs-node-get-started-send.md)</li></ul> |
| Go | [azure-event-hubs-go](https://github.com/Azure/azure-event-hubs-go) | <ul><li>[GitHub 위치](https://github.com/Azure/azure-event-hubs-go)</li><li>[자습서](event-hubs-go-get-started-send.md)</li></ul> |
| C | [azure-event-hubs-c](https://github.com/Azure/azure-event-hubs-c) | <ul><li>[GitHub 위치](https://github.com/Azure/azure-event-hubs-c)</li><li>[자습서](event-hubs-c-getstarted-send.md)</li></ul> |

아래 표에는 이전 Azure Event Hubs 런타임 클라이언트가 나와 있습니다. 이러한 패키지는 중요한 버그 수정을 받을 수 있지만 현재 개발 중이 아닙니다. 대신 위의 표에 나열된 최신 SDK를 사용하는 것이 좋습니다.

| 언어 | 패키지 | 참조 | 
| -------- | ------- | --------------- | 
| . NET Standard  | [Microsoft.Azure.EventHubs(](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) **레거시**) | <ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs)</li><li>[자습서](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> | 
|       | [Microsoft.Azure.EventHubs.Processor(](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor) **레거시**) | <ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.Processor)</li><li>[자습서](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET Framework | [WindowsAzure.Messaging(](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) **레거시**) | |
|   Java   | [azure-eventhubs](https://search.maven.org/search?q=a:azure-eventhubs) **(레거시)** | <ul><li>[GitHub 위치](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/microsoft-azure-eventhubs)</li><li>[자습서](event-hubs-java-get-started-send.md)</li></ul> |

## <a name="management-sdks"></a>관리 SDK
다음은 현재 사용 가능한 관리 관련 라이브러리 목록입니다. 이러한 라이브러리 중 런타임 작업을 포함하는 것은 없으며 **Event Hubs 엔터티를 관리** 하는 데만 사용됩니다.

- [.NET Standard](/dotnet/api/microsoft.azure.management.eventhub)
- [Java](/java/api/com.microsoft.azure.management.eventhub)
- [Python](/python/api/azure-mgmt-eventhub)
- [JavaScript](/javascript/api/@azure/arm-eventhub/)

## <a name="net-packages"></a>.NET 패키지

### <a name="client-libraries"></a>클라이언트 라이브러리

- **Azure.Messaging.EventHubs:** 통합된 Azure SDK 디자인 지침을 준수하고 새로운 기능에 대한 활성 개발 중인 라이브러리의 현재 버전입니다. 전체 .NET Framework .NET Core에서 사용할 수 있도록 NetStandard 플랫폼을 지원합니다.  Microsoft.Azure.EventHubs에서는 기능 패리티가 높으며 세부 정보 및 클라이언트 계층 구조가 다른 형식을 취합니다. 이 라이브러리는 사용하는 것이 좋습니다. 
- **Microsoft.Azure.EventHubs:** Event Hubs Service Bus 번들로 묶지 않은 전용 클라이언트로 분리하는 초기 라이브러리였습니다. 전체 .NET Framework .NET Core에서 사용할 수 있도록 NetStandard 플랫폼을 지원합니다. 사용 및 타사 블로그 항목, 확장 등과 관련하여 여전히 라이브러리의 주요 버전입니다. 기준 기능은 현재 라이브러리와 동일하지만, 한 라이브러리에서 제공하는 일부 부 비트와 그렇지 않은 비트가 있습니다. 현재 버그 수정 및 중요 업데이트를 받고 있지만 더 이상 새 기능을 받지 않습니다.
- **Windows. Azure.ServiceBus:** Event Hubs 여전히 Service Bus 얽혀 있을 때 원래 라이브러리였습니다. .NET Core보다 이전이므로 전체 .NET Framework 지원합니다. 이 라이브러리는 최신 라이브러리에서 지원하지 않는 몇 가지 동시 기능을 제공합니다.   

### <a name="management-libraries"></a>관리 라이브러리

- **Microsoft.Azure.Management.EventHub:** Event Hubs 위한 관리 라이브러리의 현재 GA 버전입니다. 전체 .NET Framework .NET Core에서 사용할 수 있도록 NetStandard 플랫폼을 지원합니다.  


## <a name="next-steps"></a>다음 단계

Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Event Hubs 개요](./event-hubs-about.md)
* [이벤트 허브 만들기](event-hubs-create.md)
* [Event Hubs FAQ](event-hubs-faq.yml)
