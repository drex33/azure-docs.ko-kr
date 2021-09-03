---
title: 지속성 함수의 타이머 - Azure
description: Azure Functions의 지속성 함수 확장에서 지속성 타이머를 구현하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: 4fcaf265af6eea6d9bef47858742192103777682
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537411"
---
# <a name="timers-in-durable-functions-azure-functions"></a>지속성 함수의 타이머(Azure Functions)

[지속성 함수](durable-functions-overview.md)는 지연을 구현하거나 비동기 작업에 대한 시간 제한을 설정하기 위해 오케스트레이터 함수에 사용할 *지속성 타이머* 를 제공합니다. 지속형 타이머는 `Thread.Sleep` 및 `Task.Delay`(C#), `setTimeout()` 및 `setInterval()`(JavaScript) 또는 `time.sleep()`(Python)가 아닌 오케스트레이터 함수에서 사용해야 합니다.

[오케스트레이션 트리거 바인딩](durable-functions-bindings.md#orchestration-trigger)의 [`CreateTimer`(.NET)](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationcontext.createtimer), [`createTimer`(JavaScript)](/javascript/api/durable-functions/durableorchestrationcontext#createTimer_Date_) 또는 [`create_timer`(Python)](/python/api/azure-functions-durable/azure.durable_functions.durableorchestrationcontext#create-timer-fire-at--datetime-datetime-----azure-durable-functions-models-task-task) 메서드를 호출하여 지속형 타이머를 만듭니다. 메서드는 지정된 날짜와 시간에 완료되는 작업을 반환합니다.

## <a name="timer-limitations"></a>타이머 제한 사항

오후 4시 30분에 만료되는 타이머를 만들면 기본 지속형 작업 프레임워크에서 오후 4시 30분에만 표시되는 메시지를 큐에 넣습니다. Azure Functions 사용량 과금제에서 실행될 때 새로 표시되는 타이머 메시지는 함수 앱이 적절한 VM에서 활성화되도록 합니다.

> [!NOTE]
> * 지속형 확장 [버전 2.3.0](https://github.com/Azure/azure-functions-durable-extension/releases/tag/v2.3.0)부터 지속형 타이머는 .NET 앱에 대해 무제한입니다. JavaScript, Python 및 PowerShell 앱과 이전 버전의 확장을 사용하는 .NET 앱의 경우 Durable 타이머는 7일로 제한됩니다. 이전 확장 버전 또는 .NET이 아닌 언어 런타임을 사용 중이고 7일 이상의 지연이 필요한 경우 `while` 루프에서 타이머 API를 사용하여 더 긴 지연을 시뮬레이션합니다.
> * 지속형 타이머의 발생 시간을 컴퓨팅할 때는 항상 .NET의 `DateTime.UtcNow` 대신 `CurrentUtcDateTime`이나 JavaScript의 `Date.now` 또는 `Date.UTC` 대신 `currentUtcDateTime`을 사용합니다. 자세한 내용은 [오케스트레이터 함수 코드 제약 조건](durable-functions-code-constraints.md) 문서를 참조하세요.

## <a name="usage-for-delay"></a>지연 사용

다음 예제에서는 실행 지연에 지속성 타이머를 사용하는 방법을 보여 줍니다. 예제에서는 청구 알림을 10일 동안 매일 실행합니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> 이전 C# 예제는 Durable Functions 2.x를 대상으로 합니다. Durable Functions 1.x의 경우 `IDurableOrchestrationContext` 대신 `DurableOrchestrationContext`를 사용해야 합니다. 버전 간 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const { DateTime } = require("luxon");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = DateTime.fromJSDate(context.df.currentUtcDateTime, {zone: 'utc'}).plus({ days: 1 });
        yield context.df.createTimer(deadline.toJSDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    for i in range(0, 9):
        deadline = context.current_utc_datetime + timedelta(days=1)
        yield context.create_timer(deadline)
        yield context.call_activity("SendBillingEvent")

main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
param($Context)

for ($num = 0 ; $num -le 9 ; $num++){    
    $expiryTime =  New-TimeSpan -Days 1
    $timerTask = Start-DurableTimer -Duration $expiryTime
    Invoke-DurableActivity -FunctionName 'SendBillingEvent'
}
```
---

> [!WARNING]
> 오케스트레이터 함수에는 무한 루프를 사용하지 않도록 방지하세요. 무한 루프 시나리오를 안전하고 효율적으로 구현하는 방법에 대한 자세한 내용은 [영구 오케스트레이션](durable-functions-eternal-orchestrations.md)을 참조하세요.

## <a name="usage-for-timeout"></a>시간 제한 사용

이 예제에서는 지속성 타이머를 사용하여 시간 제한을 구현하는 방법을 보여 줍니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> 이전 C# 예제는 Durable Functions 2.x를 대상으로 합니다. Durable Functions 1.x의 경우 `IDurableOrchestrationContext` 대신 `DurableOrchestrationContext`를 사용해야 합니다. 버전 간 차이점에 대한 자세한 내용은 [Durable Functions 버전](durable-functions-versions.md) 문서를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const { DateTime } = require("luxon");

module.exports = df.orchestrator(function*(context) {
    const deadline = DateTime.fromJSDate(context.df.currentUtcDateTime, {zone: 'utc'}).plus({ seconds: 30 });

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toJSDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    deadline = context.current_utc_datetime + timedelta(seconds=30)
    activity_task = context.call_activity("GetQuote")
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any([activity_task, timeout_task])
    if winner == activity_task:
        timeout_task.cancel()
        return True
    elif winner == timeout_task:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
param($Context)

$expiryTime =  New-TimeSpan -Seconds 30

$activityTask = Invoke-DurableActivity -FunctionName 'GetQuote'-NoWait
$timerTask = Start-DurableTimer -Duration $expiryTime -NoWait

$winner = Wait-DurableTask -Task @($activityTask, $timerTask) -Any

if ($winner -eq $activityTask) {
    Stop-DurableTaskTimer -Task $timerTask
    return $True
}
else {
    return $False
}
```

---

> [!WARNING]
> 코드가 완료될 때까지 기다리지 않는 경우 `CancellationTokenSource`를 사용(.NET)하거나 반환된 `TimerTask`에서 `cancel()`을 호출(JavaScript)하여 지속형 타이머를 취소합니다. 지속형 작업 프레임워크에서는 처리 중인 모든 작업이 완료되거나 취소될 때까지 오케스트레이션 상태가 “완료됨”으로 변경되지 않습니다.

이 취소 메커니즘은 진행 중인 작업 함수 또는 하위 오케스트레이션 실행을 종료하지 않습니다. 대신 단순히 오케스트레이터 함수를 사용하여 결과를 무시하고 계속 진행할 수 있습니다. 함수 앱에서 사용량 과금제를 사용하는 경우 중단된 작업 함수에서 사용된 시간과 메모리에 대한 요금이 계속 청구됩니다. 기본적으로 소비 계획에서 실행되는 함수의 시간 제한은 5분입니다. 이 제한을 초과하면 모든 실행을 중지하고 런어웨이 청구 상황을 방지하기 위해 Azure Functions 호스트가 재활용됩니다. [함수 시간 제한은 구성 가능](../functions-host-json.md#functiontimeout)합니다.

오케스트레이터 함수에서 시간 제한을 구현하는 방법에 대한 자세한 예제는 [사용자 조작 및 시간 제한 - 전화 확인](durable-functions-phone-verification.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [외부 이벤트 발생 및 처리](durable-functions-external-events.md)
