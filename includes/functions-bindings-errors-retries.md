---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 740f1e3bd8c08ae7d1684613d1920cffd1bba619
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129237304"
---
Azure Functions에서 발생하는 오류는 다음 원본 중 하나에서 가져올 수 있습니다.

- Azure Functions [트리거 및 바인딩](..\articles\azure-functions\functions-triggers-bindings.md) 사용
- 기본 Azure 서비스의 API에 대한 호출
- REST 엔드포인트에 대한 호출
- 클라이언트 라이브러리, 패키지 또는 타사 API에 대한 호출

다음과 같은 좋은 오류 처리 방법을 사용하여 데이터 손실 또는 누락된 메시지를 방지하는 것이 중요합니다. 권장되는 오류 처리 방법에는 다음 작업이 포함됩니다.

- [Application Insights 사용](../articles/azure-functions/functions-monitoring.md)
- [구조적 오류 처리 사용](#use-structured-error-handling)
- [멱등원을 위한 디자인](../articles/azure-functions/functions-idempotent.md)
- [재시도 정책 구현](#retry-policies-preview)(해당하는 경우)

### <a name="use-structured-error-handling"></a>구조적 오류 처리 사용

오류 캡처 및 로깅은 애플리케이션의 상태를 모니터링하는 데 매우 중요합니다. 함수 코드의 최상위 수준에는 Try/Catch 블록이 포함되어야 합니다. Catch 블록에서 오류를 캡처 및 기록할 수 있습니다.

## <a name="retry-policies-preview"></a>재시도 정책(미리 보기)

재시도 정책은 함수 앱의 모든 트리거 형식에 대한 모든 함수에서 정의할 수 있습니다.  재시도 정책은 성공적으로 실행되거나 최대 재시도 횟수가 발생할 때까지 함수를 다시 실행합니다.  재시도 정책은 앱의 모든 함수 또는 개별 함수에 대해 정의할 수 있습니다.  기본적으로 함수 앱은 [트리거 소스에 대한 재시도 정책이 있는 특정 트리거](#using-retry-support-on-top-of-trigger-resilience)와는 별도로 메시지를 재시도하지 않습니다.  재시도 정책은 실행 결과로 catch되지 않는 예외가 발생할 때마다 평가됩니다.  모범 사례로는 코드에서 모든 예외를 catch하고 재시도해야 하는 오류를 rethrow해야 합니다.  실행에 대한 재시도 정책이 완료될 때까지 Event Hubs 및 Azure Cosmos DB 검사점이 작성되지 않습니다. 즉, 현재 일괄 처리가 완료될 때까지 해당 파티션의 진행이 일시 중지됩니다.

### <a name="retry-policy-options"></a>재시도 정책 옵션

재시도 정책을 정의하는 데 사용할 수 있는 옵션은 다음과 같습니다.

**최대 재시도 횟수** 는 최종 실패 전에 실행을 다시 시도하는 최대 횟수입니다. `-1` 값은 무기한으로 다시 시도하는 것을 의미합니다. 현재 재시도 횟수는 인스턴스의 메모리에 저장됩니다. 인스턴스는 재시도 사이에 오류가 있을 수 있습니다.  재시도 정책 중에 인스턴스가 실패하면 재시도 횟수가 손실됩니다.  인스턴스 오류가 발생하면 Event Hubs, Azure Cosmos DB 및 Queue Storage와 같은 트리거가 재시도 횟수를 0으로 다시 설정하여 새 인스턴스에서 처리를 다시 시작하고 일괄 처리를 다시 시도할 수 있습니다.  HTTP 및 타이머와 같은 다른 트리거는 새 인스턴스에서 다시 시작되지 않습니다.  이는 최대 재시도 횟수가 최선의 시도이며, 드문 경우 최댓값 이상으로 실행을 재시도하거나 HTTP 및 타이머와 같은 트리거를 최댓값 이하로 재시도할 수도 있음을 의미합니다.

**재시도 전략** 은 재시도 동작을 제어합니다.  다음은 두 가지 지원되는 재시도 옵션입니다.

| 옵션 | Description|
|---|---|
|**`fixedDelay`**| 각 재시도 사이에 지정된 시간이 경과할 수 있습니다.|
| **`exponentialBackoff`**| 첫 번째 재시도는 최소 지연 시간 동안 대기합니다. 이후 재시도 시 최대 지연 시간에 도달할 때까지 시간은 각 재시도 시 초기 지속 시간에 기하급수적으로 추가됩니다.  지수 백오프는 처리량이 많은 시나리오에서 재시도에 시차를 두기 위해 약간의 작은 불규칙을 지연에 추가합니다.|

#### <a name="app-level-configuration"></a>앱 수준 구성

재시도 정책은 [해당 `host.json` 파일을 사용하여](../articles/azure-functions/functions-host-json.md#retry) 앱의 모든 함수에 대해 정의할 수 있습니다. 

#### <a name="function-level-configuration"></a>함수 수준 구성

재시도 정책은 특정 함수에 대해 정의할 수 있습니다.  함수별 구성은 앱 수준 구성보다 우선합니다.

#### <a name="fixed-delay-retry"></a>고정된 지연 시간 재시도

# <a name="c"></a>[C#](#tab/csharp)

재시도를 위해서는 NuGet 패키지 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23이 필요합니다.

```csharp
[FunctionName("EventHubTrigger")]
[FixedDelayRetry(5, "00:00:10")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

*function.json* 파일의 재시도 정책은 다음과 같습니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
# <a name="javascript"></a>[JavaScript](#tab/javascript)

*function.json* 파일의 재시도 정책은 다음과 같습니다.


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

*function.json* 파일의 재시도 정책은 다음과 같습니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

*function.json* 파일의 재시도 정책은 다음과 같습니다.


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

*function.json* 파일의 재시도 정책은 다음과 같습니다.


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
---

#### <a name="exponential-backoff-retry"></a>지수 백오프 재시도

# <a name="c"></a>[C#](#tab/csharp)

재시도를 위해서는 NuGet 패키지 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23이 필요합니다.

```csharp
[FunctionName("EventHubTrigger")]
[ExponentialBackoffRetry(5, "00:00:04", "00:15:00")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

*function.json* 파일의 재시도 정책은 다음과 같습니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

*function.json* 파일의 재시도 정책은 다음과 같습니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

*function.json* 파일의 재시도 정책은 다음과 같습니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

*function.json* 파일의 재시도 정책은 다음과 같습니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

*function.json* 파일의 재시도 정책은 다음과 같습니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```
---

|function.json 속성  |특성 속성 | Description |
|---------|---------|---------| 
|전략|해당 없음|필수 요소. 사용하는 재시도 전략입니다. 유효한 값은 `fixedDelay` 또는 `exponentialBackoff`입니다.|
|maxRetryCount|해당 없음|필수 요소. 함수 실행당 허용되는 최대 재시도 횟수입니다. `-1`은 무기한으로 재시도하는 것을 의미합니다.|
|delayInterval|해당 없음|전략을 사용할 때 재시도 사이에 사용 되는 지연입니다 `fixedDelay` . 형식을 사용 하 여 문자열로 지정 `HH:mm:ss` 합니다.|
|minimumInterval|해당 없음|전략을 사용 하는 경우 최소 재시도 지연 `exponentialBackoff` 입니다. 형식을 사용 하 여 문자열로 지정 `HH:mm:ss` 합니다.|
|maximumInterval|해당 없음|`exponentialBackoff` 전략을 사용하는 경우 최대 재시도 지연 시간입니다. 형식을 사용 하 여 문자열로 지정 `HH:mm:ss` 합니다.| 

### <a name="retry-limitations-during-preview"></a>미리 보기 중 재시도 제한 사항

- .NET 프로젝트의 경우 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23 버전을 수동으로 가져와야 할 수도 있습니다.
- 사용 계획에서 앱은 큐에서 최종 메시지를 재시도하는 동안 0으로 스케일 다운될 수 있습니다.
- 사용 계획에서 재시도를 수행하는 동안 앱이 스케일 다운될 수 있습니다.  최상의 결과를 위해 재시도 간격 <= 00:01:00 및 <= 5회 재시도를 선택합니다.

## <a name="using-retry-support-on-top-of-trigger-resilience"></a>트리거 복원력 위에서 재시도 지원 사용

함수 앱 재시도 정책은 트리거에서 제공하는 재시도 또는 복원력과는 관계가 없습니다.  함수 재시도 정책은 트리거 복원력 재시도의 맨 위에서만 계층화됩니다.  예를 들어 Azure Service Bus를 사용하는 경우 기본적으로 큐의 메시지 배달 수는 10입니다.  기본 배달 수는 10번의 큐 메시지 배달 시도 후 Service Bus가 메시지를 배달하지 못한 것을 의미합니다.  Service Bus 트리거를 포함하는 함수에 대한 재시도 정책을 정의할 수 있지만, 재시도는 Service Bus 배달 시도의 맨 위에 계층화됩니다.  

예를 들어 기본 Service Bus 배달 수를 10으로 사용하고 함수 다시 재정책을 5로 정의한다면,  먼저 메시지가 큐에서 제거되고 Service Bus 배달 계정이 1로 증가합니다.  모든 실행이 실패하면 동일한 메시지를 트리거하기 위해 5번 시도 후에 해당 메시지는 중단된 것으로 표시됩니다.  Service Bus는 즉시 메시지를 다시 대기열에 추가하고 함수를 트리거하고 배달 수를 2로 늘립니다.  마지막으로, 50번의 최종 시도 후(10번의 Service Bus 배달 * 배달당 5번의 함수 재시도) 메시지는 중단되고 Service Bus에서 배달 못한 편지를 트리거합니다.

> [!WARNING]
> Service Bus 큐와 같은 트리거에 대한 배달 횟수를 1로 설정하면 단일 함수 재시도 주기 후에 메시지는 즉시 배달 못한 문자가 됩니다. 이는 권장되지 않습니다.  함수 재시도 정책이 최선이며 원하는 총 재시도 횟수보다 적을 수 있는 반면, 트리거는 재시도를 통해 복원력을 제공하기 때문입니다.

### <a name="triggers-with-additional-resiliency-or-retries"></a>추가 복원력 및 재시도를 포함하는 트리거

다음 트리거는 트리거 원본에서 재시도를 지원합니다.

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure Queue storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus(큐/토픽)](../articles/azure-functions/functions-bindings-service-bus.md)

기본적으로 대부분의 트리거는 요청을 최대 5회 재시도합니다. 5번째 재시도 후에는 Azure Queue Storage가 모두 [포이즌 큐](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages)에 메시지를 기록합니다.  기본 Service Bus 큐 및 토픽 정책은 10번의 시도 후 [배달 못한 편지 큐](../articles/service-bus-messaging/service-bus-dead-letter-queues.md)에 메시지를 기록합니다.
