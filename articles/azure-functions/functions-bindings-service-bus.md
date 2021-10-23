---
title: Azure Functions의 Azure Service Bus 바인딩
description: Azure Functions에서 Azure Service Bus 트리거 및 바인딩을 보내는 방법을 배워봅니다.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 858e8ed781ce28406fed89d61be370c8eefdafd9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257065"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Functions의 Azure Service Bus 바인딩

Azure Functions는 [트리거 및 바인딩](./functions-triggers-bindings.md)을 통해 [Azure Service Bus](https://azure.microsoft.com/services/service-bus)와 통합됩니다. Service Bus와 통합하면 큐 또는 토픽 메시지에 응답하고 이를 전송하는 함수를 작성할 수 있습니다.

| 작업 | 유형 |
|---------|---------|
| Service Bus 큐 또는 토픽 메시지가 만들어지면 함수를 실행합니다. | [트리거](./functions-bindings-service-bus-trigger.md) |
| Azure Service Bus 메시지 전송 |[출력 바인딩](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Functions 앱 추가

### <a name="functions-2x-and-higher"></a>Functions 2.x 이상

트리거 및 바인딩을 사용하려면 적절한 패키지를 참조해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용되는 반면, 확장 번들은 다른 모든 애플리케이션 형식에 사용됩니다.

| 언어                                        | 추가 방법...                                   | 설명 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet 패키지], 버전 4.x 설치 | |
| C# Script, Java, JavaScript, Python, PowerShell | [확장 번들] 등록          | [Azure Tools 확장]은 Visual Studio Code와 함께 사용하는 것이 좋습니다. |
| C# 스크립트(Azure Portal에서 온라인으로만)         | 바인딩 추가                            | 함수 앱을 다시 게시하지 않고 기존 바인딩 확장을 업데이트하려면 [확장 업데이트]를 참조하세요. |

[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[확장 업데이트]: ./functions-bindings-register.md
[Azure Tools 확장]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

#### <a name="service-bus-extension-5x-and-higher"></a>Service Bus 확장 5.x 이상

새 버전의 Service Bus 바인딩 확장은 미리 보기에서 사용할 수 있습니다. [비밀 대신 id를 사용 하 여 연결](./functions-reference.md#configure-an-identity-based-connection)하는 기능이 도입 되었습니다. 관리 id를 사용 하 여 함수 앱을 구성 하는 방법에 대 한 자습서는 [id 기반 연결을 사용 하 여 함수 앱 만들기 자습서](./functions-identity-based-connections-tutorial.md)를 참조 하세요. .NET 응용 프로그램의 경우에는 새 확장 버전 에서도 바인딩할 수 있는 형식을 변경 하 여 `Microsoft.ServiceBus.Messaging` `Microsoft.Azure.ServiceBus` [ServiceBus](/dotnet/api/azure.messaging.servicebus)의 새 형식으로 형식을 바꿉니다.

이 확장 버전은 [미리 보기 NuGet 패키지로] 사용할 수 있거나, 파일에 다음을 추가 하 여 미리 보기 확장 번들 v3에서 추가할 수 있습니다 `host.json` .

```json
{
  "version": "2.0",
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle.Preview",
    "version": "[3.*, 4.0.0)"
  }
}
```

자세히 알아보려면 [확장 업데이트]를 참조 하세요.

[NuGet 패키지 미리 보기]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/5.0.0-beta.6
[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[확장 업데이트]: ./functions-bindings-register.md
[Azure Tools 확장]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x 앱은 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지 버전 2.x에 대한 참조를 자동으로 포함합니다.


<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 설정

[!INCLUDE [functions-host-json-section-intro](../../includes/functions-host-json-section-intro.md)]

> [!NOTE]
> Functions 1.x에서 host.json의 참조는 [Azure Functions 1.x에 대한 host.json 참조](functions-host-json-v1.md)를 참조하세요.

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": true,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:05:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            },
            "batchOptions": {
                "maxMessageCount": 1000,
                "operationTimeout": "00:01:00",
                "autoComplete": true
            }
        }
    }
}
```

`isSessionsEnabled`를 `true`로 설정한 경우 `sessionHandlerOptions`가 적용됩니다.  `isSessionsEnabled`를 `false`로 설정한 경우 `messageHandlerOptions`가 적용됩니다.

|속성  |기본값 | Description |
|---------|---------|---------|
|prefetchCount|0|메시지 수신자가 동시에 요청할 수 있는 메시지 수를 가져오거나 설정합니다.|
|messageHandlerOptions.maxAutoRenewDuration|00:05:00|메시지 잠금이 자동으로 갱신되는 최대 기간입니다.|
|messageHandlerOptions.autoComplete|true|트리거가 처리 후 자동으로 complete를 호출해야 하는지, 아니면 함수 코드가 수동으로 complete를 호출하는지 여부입니다.<br><br>`false`로 설정하는 것은 C#에서만 지원됩니다.<br><br>`true`로 설정한 경우 함수 실행이 성공적으로 완료되면 트리거가 자동으로 메시지를 완료하고 그렇지 않으면 메시지가 중단됩니다.<br><br>`false`로 설정한 경우 [MessageReceiver](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver) 메서드를 호출하여 메시지를 완료, 중단 또는 배달 못하도록 합니다. 예외가 발생하고 `MessageReceiver` 메서드가 호출되지 않으면 잠금이 유지됩니다. 잠금이 만료되면 `DeliveryCount`가 증가하여 메시지가 다시 큐에 추가되고 잠금이 자동으로 갱신됩니다.<br><br>C#이 아닌 함수에서 함수의 예외로 인해 백그라운드에서 런타임 호출 `abandonAsync`가 발생합니다. 예외가 발생하지 않으면 `completeAsync`가 백그라운드에서 호출됩니다. |
|messageHandlerOptions.maxConcurrentCalls|16|메시지 펌프가 크기 조정된 인스턴스별로 시작해야 하는 콜백에 대한 최대 동시 호출 수입니다. 기본적으로 함수 런타임은 여러 개의 메시지를 동시에 처리합니다.|
|sessionHandlerOptions.maxConcurrentSessions|2000|크기 조정된 인스턴스당 동시에 처리할 수 있는 최대 세션 수입니다.|
|batchOptions.maxMessageCount|1000| 트리거될 때 함수로 전송되는 최대 메시지 수입니다. |
|batchOptions.operationTimeout|00:01:00| `hh:mm:ss`로 표현된 시간 범위 값입니다. |
|batchOptions.autoComplete|true| `messageHandlerOptions.autoComplete`에 대한 위의 설명을 참조하세요. |

### <a name="additional-settings-for-version-5x"></a>버전 5.x 이상에 대한 추가 설정

아래 host.json 파일의 예에는 Service Bus 확장 버전 5.0.0 이상에 대한 설정만 포함되어 있습니다.

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "clientRetryOptions":{
                "mode": "exponential",
                "tryTimeout": "00:01:00",
                "delay": "00:00:00.80",
                "maxDelay": "00:01:00",
                "maxRetries": 3
            },
            "prefetchCount": 0,
            "autoCompleteMessages": true,
            "maxAutoLockRenewalDuration": "00:05:00",
            "maxConcurrentCalls": 16,
            "maxConcurrentSessions": 8,
            "maxMessages": 1000,
            "sessionIdleTimeout": "00:01:00"
        }
    }
}
```

Service Bus 확장 버전 5.x 이상을 사용하는 경우 `ServiceBusOptions`의 2.x 설정 외에 다음 글로벌 구성 설정이 지원됩니다.

|속성  |기본값 | Description |
|---------|---------|---------|
|prefetchCount|0|메시지 수신자가 동시에 요청할 수 있는 메시지 수를 가져오거나 설정합니다.|
|autoCompleteMessages|true|함수를 성공적으로 실행한 후 메시지를 자동으로 완료할지 여부를 결정하며 `autoComplete` 구성 설정 대신 사용해야 합니다.|
|maxAutoLockRenewalDuration|00:05:00|`maxAutoRenewDuration` 대신 사용해야 합니다.|
|maxConcurrentCalls|16|메시지 펌프가 크기 조정된 인스턴스별로 시작해야 하는 콜백에 대한 최대 동시 호출 수입니다. 기본적으로 함수 런타임은 여러 개의 메시지를 동시에 처리합니다.|
|maxConcurrentSessions|8|크기 조정된 인스턴스당 동시에 처리할 수 있는 최대 세션 수입니다.|
|maxMessages|1000|각 함수 호출에 전달될 최대 메시지 수입니다. 메시지 일괄 처리를 수신하는 함수에만 적용됩니다.|
|sessionIdleTimeout|해당 없음|현재 활성 세션에 대한 메시지가 수신될 때까지 대기하는 최대 시간입니다. 이 시간이 경과되면 프로세서에서 세션을 닫고 다른 세션을 처리하려고 합니다.|

### <a name="retry-settings"></a>다시 시도 설정

버전 5.x 이상의 Service Bus 확장을 사용할 때 위의 구성 특성 외에도 `ServiceBusOptions` 내에서 `RetryOptions`를 구성할 수도 있습니다. 이러한 설정은 실패한 작업을 다시 시도해야 하는지 여부를 결정하고, 그럴 경우 다시 시도 간의 대기 시간을 결정합니다. 이 옵션은 또한 Service Bus 서비스와의 메시지 수신 및 기타 상호 작용에 허용되는 시간을 제어합니다.

|속성  |기본값 | 설명 |
|---------|---------|---------|
|mode|지수|다시 시도 지연을 계산하는 데 사용하는 방법입니다. 기본 지수 모드는 다시 시도하기 전에 대기하는 기간을 증가시키는 백오프 전략에 따라 지연 시간으로 다시 시도합니다. `Fixed` 모드는 각 지연 시간이 일관된 고정 간격으로 다시 시도합니다.|
|tryTimeout|00:01:00|시도당 작업을 대기하는 최대 기간입니다.|
|delay|00:00:00.80|다시 시도 사이에 적용할 지연 또는 백오프 요소입니다.|
|maxDelay|00:01:00|다시 시도 사이에 허용되는 최대 지연|
|maxRetries|3|연결된 작업이 실패한 것으로 간주하기 전 최대 다시 시도 횟수입니다.|

---

## <a name="next-steps"></a>다음 단계

- [Service Bus 큐 또는 토픽 메시지가 생성(트리거) 될 때 함수 실행](./functions-bindings-service-bus-trigger.md)
- [Azure Functions에서 Azure Service Bus 메시지 전송(출력 바인딩)](./functions-bindings-service-bus-output.md)
