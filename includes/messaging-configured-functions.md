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
ms.openlocfilehash: f4d7b9d272f097f5be332c8ff0acdf7cb30395ea
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570611"
---
Azure Functions를 사용하여 미리 작성된 진입점에 대한 구성 전용 복제 작업을 만들 수 있습니다. [Azure Functions에 대한 구성 기반 복제 샘플](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config)에서는 [미리 작성된 도우미](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication)를 사용자 코드에서 활용하거나 코드를 모두 처리하지 않고 구성을 사용하는 방법을 보여줍니다.

## <a name="create-a-replication-task"></a>복제 작업 만들기
이러한 복제 작업을 만들려면 먼저 프로젝트 폴더 아래에 새 폴더를 만듭니다. 새 폴더의 이름은 함수의 이름입니다(예: `europeToAsia` 또는 `telemetry`). 이 이름은 사용 중인 메시징 엔터티와의 직접적인 상관 관계가 없으며 이를 식별하는 데만 사용됩니다. 동일한 프로젝트에서 수십 개의 함수를 만들 수 있습니다.

다음으로, 폴더에 `function.json` 파일을 만듭니다. 파일이 함수를 구성합니다. 다음 콘텐츠로 시작합니다.

```json
{
    "configurationSource": "config",
    "bindings" : [
        {
            "direction": "in",
            "name": "input" 
        },
        {
            "direction": "out",
            "name": "output"
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": -1,
        "minimumInterval": "00:00:05",
        "maximumInterval": "00:05:00"
    },
    "disabled": false,
    "scriptFile": "../bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.*"
}
```

해당 파일에서 연결하려는 엔터티에 종속된 세 가지 구성 단계를 완료해야 합니다.

1. [입력 방향 구성](#configure-the-input-direction)
2. [출력 방향 구성](#configure-the-output-direction)
3. [진입점 구성](#configure-the-entry-point)

### <a name="configure-the-input-direction"></a>입력 방향 구성

#### <a name="event-hub-input"></a>이벤트 허브 입력

이벤트 허브에서 이벤트를 수신하려면 설정하는 "바인딩" 내의 상단 섹션에 구성 정보를 추가합니다.

* **type** - "eventHubTrigger" 형식입니다.
* **connection** - 이벤트 허브 연결 문자열에 대한 앱 설정 값의 이름입니다. 
* **eventHubName** - 연결 문자열로 식별되는 네임스페이스 내에 있는 이벤트 허브의 이름입니다.
* **consumerGroup** - 소비자 그룹의 이름입니다. 이름은 퍼센트 기호로 묶여 있으므로 앱 설정 값을 참조하기도 합니다.

```json
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "eventHubTrigger",
            "connection": "functionname-source-connection",
            "eventHubName": "eventHubA",
            "consumerGroup" : "%functionname-source-consumergroup%",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-queue-input"></a>Service Bus 큐 입력

Service Bus 큐에서 이벤트를 수신하려면 설정하는 "바인딩" 내의 상단 섹션에 구성 정보를 추가합니다.

* **type** - "serviceBusTrigger" 형식입니다.
* **connection** - Service Bus 연결 문자열에 대한 앱 설정 값의 이름입니다.
* **queueName** - 연결 문자열로 식별되는 네임스페이스 내에 있는 Service Bus 큐의 이름입니다.

```json
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "queueName": "queue-a",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-topic-input"></a>Service Bus 토픽 입력

Service Bus 토픽에서 이벤트를 수신하려면 설정하는 "바인딩" 내의 상단 섹션에 구성 정보를 추가합니다.

* **type** - "serviceBusTrigger" 형식입니다.
* **connection** - Service Bus 연결 문자열에 대한 앱 설정 값의 이름입니다. 제공된 스크립트를 사용하려면 이 값이 `{FunctionName}-source-connection`이어야 합니다.
* **topicName** - 연결 문자열로 식별되는 네임스페이스 내에 있는 Service Bus 토픽의 이름입니다.
* **subscriptionName** -연결 문자열로 식별되는 네임스페이스 내에 있는 지정된 토픽에 대한 Service Bus 구독의 이름입니다.

```json
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "topicName": "topic-x",
            "subscriptionName" : "sub-y",
            "name": "input" 
        }
    ...
```

### <a name="configure-the-output-direction"></a>출력 방향 구성

#### <a name="event-hub-output"></a>이벤트 허브 출력

이벤트 허브로 이벤트를 전달하려면 설정하는 "바인딩" 내의 하단 섹션에 구성 정보를 추가합니다.

* **type** - "eventHub" 형식입니다.
* **connection** - 이벤트 허브 연결 문자열에 대한 앱 설정 값의 이름입니다. 
* **eventHubName** - 연결 문자열로 식별되는 네임스페이스 내에 있는 이벤트 허브의 이름입니다.

```json
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "eventHub",
            "connection": "functionname-target-connection",
            "eventHubName": "eventHubB",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-queue-output"></a>Service Bus 큐 출력

Service Bus 큐로 이벤트를 전달하려면 설정하는 "바인딩" 내의 하단 섹션에 구성 정보를 추가합니다.

* **type** - "serviceBus" 형식입니다.
* **connection** - Service Bus 연결 문자열에 대한 앱 설정 값의 이름입니다. 
* **queueName** - 연결 문자열로 식별되는 네임스페이스 내에 있는 Service Bus 큐의 이름입니다.

```json
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "queueName": "queue-b",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-topic-output"></a>Service Bus 토픽 출력

Service Bus 토픽으로 이벤트를 전달하려면 설정하는 "바인딩" 내의 하단 섹션에 구성 정보를 추가합니다.

* **type** - "serviceBus" 형식입니다.
* **connection** - Service Bus 연결 문자열에 대한 앱 설정 값의 이름입니다. 
* **topicName** - 연결 문자열로 식별되는 네임스페이스 내에 있는 Service Bus 토픽의 이름입니다.

```json
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "topicName": "topic-b",
            "name": "output" 
        }
    ...
```

### <a name="configure-the-entry-point"></a>진입점 구성

진입점 구성은 표준 복제 작업 중 하나를 선택합니다. `Azure.Messaging.Replication` 프로젝트를 수정하는 경우 작업을 추가하고 여기에서 참조할 수도 있습니다. 예를 들면 다음과 같습니다.

```json
    ...
    "scriptFile": "../dotnet/bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub"
    ...
```

다음 표에서는 원본 및 대상의 조합에 대한 올바른 값을 제공합니다.

| 원본      | 대상      | 진입점 
|-------------|-------------|------------------------------------------------------------------------
| 이벤트 허브   | 이벤트 허브   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| 이벤트 허브   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | 이벤트 허브   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`

### <a name="retry-policy"></a>재시도 정책

[재시도에 대한 Azure Functions 문서](../articles/azure-functions/functions-bindings-error-pages.md)를 참조하여 재시도 정책을 구성합니다. 이 리포지토리의 프로젝트 전체에서 선택한 정책 설정은 데이터 손실을 방지하기 위해 재시도 간격을 무한 재시도로 5초에서 5분으로 설정하여 지수 백오프 전략을 구성합니다.

Service Bus의 경우 트리거의 상호 작용과 큐에 대해 정의된 최대 제공 횟수를 이해하려면 ["트리거 복원력 위에 재시도 지원 사용"](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) 섹션을 검토하세요.

### <a name="build-deploy-and-configure"></a>빌드, 배포, 구성

구성에 집중하는 동안 작업에는 배포 가능한 애플리케이션을 빌드하고, 지정된 엔드포인트에 연결하는 데 필요한 모든 정보를 포함하도록 Azure Functions 호스트를 구성해야 합니다. 

이는 [Azure Functions에 대한 구성 기반 복제 샘플](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config)에서 재사용 가능한 스크립트와 함께 설명됩니다.
