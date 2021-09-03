---
title: 지속성 오케스트레이터 코드 제약 조건 - Azure Functions
description: Azure Durable Functions에 대한 오케스트레이션 함수 재생 및 코드 제약 조건입니다.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: fade080e631385acec46fc59c41e6624280ae9e7
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529649"
---
# <a name="orchestrator-function-code-constraints"></a>오케스트레이터 함수 코드 제약 조건

Durable Functions는 상태 저장 앱을 빌드하도록 지원하는 [Azure Functions](../functions-overview.md)의 확장입니다. [오케스트레이터 함수](durable-functions-orchestrations.md)를 사용하여 함수 앱 내에서 다른 지속성 함수의 실행을 오케스트레이션할 수 있습니다. 오케스트레이터 함수는 상태를 저장하고, 신뢰할 수 있으며, 장기 실행될 수 있습니다.

## <a name="orchestrator-code-constraints"></a>오케스트레이터 코드 제약 조건

오케스트레이터 함수는 [이벤트 소싱](/azure/architecture/patterns/event-sourcing)을 사용하여 신뢰할 수 있는 실행을 보장하고 로컬 변수 상태를 유지합니다. 오케스트레이터 코드의 [재생 동작](durable-functions-orchestrations.md#reliability)은 오케스트레이터 함수에 작성될 수 있는 코드 형식에 대한 제약 조건을 만듭니다. 예를 들어 오케스트레이터 함수는 *결정적* 이어야 합니다. 오케스트레이터 함수는 여러 번 재생되며 매번 동일한 결과를 생성해야 합니다.

### <a name="using-deterministic-apis"></a>결정적 API 사용

이 섹션에서는 결정적 코드를 보장하는 데 도움이 되는 몇 가지 간단한 지침을 제공합니다.

오케스트레이터 함수는 대상 언어의 모든 API를 호출할 수 있습니다. 그러나 오케스트레이터 함수는 결정적 API만 호출해야 합니다. *결정적 API* 는 호출 시기 또는 빈도에 관계없이 동일한 입력에서 항상 동일한 값을 반환하는 API입니다.

다음 표에서는 *결정적이지 않기* 때문에 피해야 하는 API의 예를 보여 줍니다. 이러한 제한 사항은 오케스트레이터 함수에만 적용됩니다. 다른 함수 유형에는 이러한 제한 사항이 없습니다.

| API 범주 | 이유 | 해결 방법 |
| ------------ | ------ | ---------- |
| 날짜 및 시간  | 현재 날짜 또는 시간을 반환하는 API는 반환되는 값이 각 재생마다 다르므로 비결정적입니다. | 재생하기에 안전한 .NET의 [CurrentUtcDateTime](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationcontext.currentutcdatetime) 속성, JavaScript의 `currentUtcDateTime` API 또는 Python의 `current_utc_datetime` API를 사용합니다. 마찬가지로, "스톱워치" 유형 개체(예: [.NET의 스톱워치 클래스](/dotnet/api/system.diagnostics.stopwatch))를 피하세요. 경과 시간을 측정해야 하는 경우 실행 시작 시 `CurrentUtcDateTime` 값을 저장하고 실행 종료 시 `CurrentUtcDateTime`에서 해당 값을 뺍니다. |
| GUID 및 UUID  | 임의의 GUID 또는 UUID를 반환하는 API는 각 재생마다 다른 값이 생성되기 때문에 비결정적입니다. | .NET의 [NewGuid](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationcontext.newguid), JavaScript의 `newGuid`, Python의 `new_guid`를 사용하여 임의의 GUID를 안전하게 생성합니다. |
| 난수 | 난수를 반환하는 API는 각 재생마다 다른 값이 생성되기 때문에 비결정적입니다. | 작업 함수를 사용하여 오케스트레이션에 난수를 반환합니다. 작업 함수의 반환 값은 항상 재생하기에 안전합니다. |
| 바인딩 | 입력 및 출력 바인딩은 일반적으로 I/O를 수행하며 비결정적입니다. 오케스트레이터 함수는 [오케스트레이션 클라이언트](durable-functions-bindings.md#orchestration-client) 및 [엔터티 클라이언트](durable-functions-bindings.md#entity-client) 바인딩을 직접 사용하지 않아야 합니다. | 클라이언트 또는 작업 함수 내에서 입력 및 출력 바인딩을 사용합니다. |
| Network (네트워크) | 네트워크 호출은 외부 시스템과 관련이 있으며 비결정적입니다. | 작업 함수를 사용하여 네트워크 호출을 수행합니다. 오케스트레이터 함수에서 HTTP 호출을 수행해야 하는 경우 [지속성 HTTP API](durable-functions-http-features.md#consuming-http-apis)를 사용할 수도 있습니다. |
| 블로킹 API | .NET의 `Thread.Sleep` 같은 블로킹 API와 유사한 API는 오케스트레이터 함수에 성능 및 확장 문제를 유발할 수 있으므로 피해야 합니다. Azure Functions 사용 계획에서는 불필요한 런타임 요금이 발생할 수도 있습니다. | 블로킹 API를 사용할 수 있는 경우에도 대체 방법을 사용하세요. 예를 들어 오케스트레이션 실행을 지연시키려면 `CreateTimer`를 사용합니다. [지속성 타이머](durable-functions-timers.md) 지연은 오케스트레이터 함수의 실행 시간을 계산하지 않습니다. |
| 비동기 API | 오케스트레이터 코드는 `IDurableOrchestrationContext` API, JavaScript의 `context.df` API 또는 Python의 `context` API를 사용하는 경우를 제외하고 비동기 작업을 시작해서는 안 됩니다. 예를 들어 .NET의 `Task.Run`, `Task.Delay` 및 `HttpClient.SendAsync` 또는 JavaScript의 `setTimeout` 및 `setInterval`은 사용할 수 없습니다. Durable Task Framework는 단일 스레드에서 오케스트레이터 코드를 실행합니다. 다른 비동기 API에서 호출될 수 있는 다른 스레드와 상호 작용할 수 없습니다. | 오케스트레이터 함수는 지속성 비동기 호출만 수행해야 합니다. 작업 함수는 다른 비동기 API 호출을 수행해야 합니다. |
| 비동기 JavaScript 함수 | node.js 런타임은 비동기 함수가 결정적이도록 보장하지 않기 때문에 JavaScript 오케스트레이터 함수를 `async`로 선언할 수 없습니다. | JavaScript 오케스트레이터 함수를 동기 생성기 함수로 선언 |
| Python 코루틴 | Python 오케스트레이터 함수는 코루틴으로 선언할 수 없습니다. 즉, `async` 키를 사용하여 선언할 수 없습니다. 코루틴 의미 체계가 Durable Functions 재생 모델에 맞지 않기 때문입니다. | Python 오케스트레이터 함수를 생성기로 선언합니다. 즉, `context` API는 `await` 대신 `yield`를 사용해야 합니다.   |
| 스레딩 API | Durable Task Framework는 단일 스레드에서 오케스트레이터 코드를 실행하며 다른 모든 스레드와 상호 작용할 수 없습니다. 오케스트레이션의 실행에 새 스레드를 도입하면 비결정적 실행 또는 교착 상태가 발생할 수 있습니다. | 오케스트레이터 함수는 스레드 API를 거의 사용하지 않아야 합니다. 예를 들어 .NET에서는 `ConfigureAwait(continueOnCapturedContext: false)`를 사용하지 않도록 합니다. 이렇게 하면 오케스트레이터 함수의 원래 `SynchronizationContext`에서 태스크가 계속 실행됩니다. 이러한 API가 필요한 경우 작업 함수에만 사용되도록 제한합니다. |
| 정적 변수 | 오케스트레이터 함수에서 비상수 정적 변수는 사용하지 마세요. 시간이 지남에 따라 값이 변경되어 비결정적 런타임 동작이 발생할 수 있기 때문입니다. | 상수를 사용하거나 정적 변수를 작업 함수에만 사용되도록 제한합니다. |
| 환경 변수 | 오케스트레이터 함수에서 환경 변수를 사용하지 마세요. 해당 값은 시간이 지남에 따라 변경될 수 있으므로 비결정적 런타임 동작이 발생합니다. | 환경 변수는 클라이언트 함수 또는 작업 함수 내에서만 참조해야 합니다. |
| 무한 루프 | 오케스트레이터 함수에는 무한 루프를 사용하지 않도록 방지하세요. 지속성 작업 프레임워크는 오케스트레이션 함수가 진행됨에 따라 실행 기록을 저장하기 때문에 무한 루프로 인해 오케스트레이터 인스턴스의 메모리가 부족해질 수 있습니다. | 무한 루프 시나리오의 경우 .NET의 `ContinueAsNew`, JavaScript의 `continueAsNew` 또는 Python의 `continue_as_new` 같은 API를 사용하여 함수 실행을 다시 시작하고 이전 실행 기록을 버립니다. |

이러한 제약 조건을 적용하는 것이 처음에는 어려워 보일 수 있지만 실제로는 쉽게 따를 수 있습니다.

Durable Task Framework는 위의 규칙 위반을 감지하려고 합니다. 위반을 발견하면 프레임워크에서 **NonDeterministicOrchestrationException** 예외를 throw합니다. 그러나 이 검색 동작이 모든 위반을 catch하지는 않으므로 여기에 의존해서는 안 됩니다.

## <a name="versioning"></a>버전 관리

지속성 오케스트레이션은 수일, 수개월, 수년 동안 실행되거나 심지어 [계속](durable-functions-eternal-orchestrations.md) 실행될 수도 있습니다. 완료되지 않은 오케스트레이션에 영향을 주는 Durable Functions 앱에 대한 모든 코드 업데이트는 오케스트레이션의 재생 동작을 중단시킬 수 있습니다. 따라서 코드를 업데이트할 때 신중하게 계획하는 것이 중요합니다. 코드 버전 관리 방법에 대한 자세한 설명은 [버전 관리 문서](durable-functions-versioning.md)를 참조하세요.

## <a name="durable-tasks"></a>지속성 작업

> [!NOTE]
> 이 섹션에서는 지속성 작업 프레임워크의 내부 구현에 대해 자세히 설명합니다. 이 정보를 알지 못해도 지속성 함수를 사용할 수 있습니다. 여기서는 재생 동작을 이해하는 데에만 도움이 됩니다.

오케스트레이터 함수에서 안전하게 기다릴 수 있는 태스크를 종종 *지속성 태스크* 라고 합니다. Durable Task Framework는 이러한 태스크를 만들고 관리합니다. 예를 들어 .NET 오케스트레이터 함수에서 **CallActivityAsync**, **WaitForExternalEvent** 및 **CreateTimer** 가 반환하는 태스크가 있습니다.

이러한 지속성 태스크는 .NET에서 `TaskCompletionSource` 개체의 목록을 사용하여 내부적으로 관리됩니다. 이러한 태스크는 재생하는 동안 오케스트레이터 코드 실행의 일부로 만들어집니다. 디스패처에서 해당 기록 이벤트를 열거하면 완료됩니다.

모든 기록이 재생될 때까지 단일 스레드를 사용하여 태스크가 동기적으로 수행됩니다. 기록 재생이 끝날 때까지 완료되지 않은 지속성 태스크에서는 적절한 동작이 수행됩니다. 예를 들어 작업 함수를 호출하기 위해 메시지를 큐에 넣을 수 있습니다.

이 섹션의 런타임 동작에 대한 설명은 오케스트레이터 함수가 비지속성 태스크에서 `await` 또는 `yield`를 사용할 수 없는 이유를 이해하는 데 도움이 됩니다. 두 가지 이유는 다음과 같습니다. 디스패처 스레드는 태스크가 완료될 때까지 기다릴 수 없으며, 해당 태스크의 콜백은 오케스트레이터 함수의 추적 상태를 손상시킬 수 있습니다. 이러한 위반을 감지하는 데 도움이 되는 몇 가지 런타임 검사가 준비되어 있습니다.

Durable Task Framework에서 오케스트레이터 함수를 실행하는 방법에 대한 자세한 내용은 [GitHub의 Durable Task 소스 코드](https://github.com/Azure/durabletask)를 참조하세요. 특히 [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) 및 [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [하위 오케스트레이션을 호출하는 방법 알아보기](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [버전 관리 처리](durable-functions-versioning.md)
