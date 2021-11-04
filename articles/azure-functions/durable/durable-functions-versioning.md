---
title: 지속성 함수의 버전 관리 - Azure
description: Azure Functions의 지속성 함수 확장에서 버전 관리를 구현하는 방법을 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 05/12/2021
ms.author: azfuncdf
ms.openlocfilehash: 16de5f5950cf305115de4c2849b3ebbbc4cf2536
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507724"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>지속성 함수의 버전 관리(Azure Functions)

함수는 애플리케이션의 수명 주기 동안 반드시 추가, 제거 및 변경됩니다. [지속성 함수](durable-functions-overview.md)는 이전에 가능하지 않았던 방식으로 체이닝 함수를 함께 사용할 수 있으며, 이 체이닝은 버전 관리를 처리하는 방법에 영향을 줍니다.

## <a name="how-to-handle-breaking-changes"></a>주요 변경 내용을 처리하는 방법

주요 변경 내용을 인식할 수 있는 몇 가지 예제가 있습니다. 이 문서에서는 가장 일반적인 예제에 대해 설명합니다. 이들 모두의 기본 주제는 함수 코드의 변경으로 인해 새 함수와 기존 함수의 오케스트레이션이 모두 영향을 받는다는 것입니다.

### <a name="changing-activity-or-entity-function-signatures"></a>작업 또는 엔터티 함수 시그니처 변경

시그니처 변경은 함수의 이름, 입력 또는 출력의 변경을 나타냅니다. 이러한 종류의 변경이 작업 또는 엔터티 함수에 적용되면 이 함수에 종속된 오케스트레이터 함수가 손상될 수 있습니다. 이 변경에 따라 오케스트레이터 함수를 업데이트하면 진행 중인 기존 인스턴스를 손상시킬 수 있습니다.

예를 들어 다음과 같은 C# 오케스트레이터 함수가 있다고 가정합니다.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

이 간단한 함수는 **Foo** 의 결과를 받아 **Bar** 로 전달합니다. 더 다양한 결과 값을 지원하기 위해 **Foo** 의 반환 값을 `bool`에서 `string`로 변경할 필요가 있다고 가정해 보겠습니다. 결과는 다음과 같습니다.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string result = await context.CallActivityAsync<string>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

이 변경은 오케스트레이터 함수의 새 인스턴스 모두에서 정상적으로 작동하지만 진행 중인 모든 인스턴스를 중단시킵니다. 예를 들어 오케스트레이션 인스턴스에서 `Foo`라는 함수를 호출하고 부울 값과 검사점을 다시 가져오는 경우를 살펴보겠습니다. 이 시점에서 시그니처 변경이 배포되면 검사점이 설정된 인스턴스는 다시 시작되고 `Foo`에 대한 호출을 재생할 때 즉시 실패합니다. 이 실패는 기록 테이블의 결과가 `bool`이지만 새 코드가 이를 `string`으로 역직렬화하려고 시도하여 형식이 안전한 언어에 대한 런타임 예외가 생성되기 때문에 발생합니다.

이 예는 함수 시그니처 변경으로 인해 기존 인스턴스가 손상될 수 있는 여러 가지 방법 중 하나일 뿐입니다. 일반적으로 오케스트레이터에서 함수를 호출하는 방식을 변경해야 하는 경우 변경이 문제가 될 수 있습니다.

### <a name="changing-orchestrator-logic"></a>오케스트레이터 논리 변경

다른 종류의 버전 관리 문제는 진행 중인 인스턴스의 실행 경로를 변경시키는 방식으로 오케스트레이터 함수 코드를 변경하는 것입니다.

다음 C# 오케스트레이터 함수를 살펴보세요.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

이제 두 개의 기존 함수 호출 사이에 새 함수 호출을 추가하도록 변경하려고 한다고 가정해 보겠습니다.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

이 변경은 *Foo* 와 *Bar* 간의 *SendNotification* 에 새 함수 호출을 추가합니다. 시그니처 변경은 없습니다. 문제는 *Bar* 에 대한 호출에서 기존 인스턴스가 다시 시작될 때 발생합니다. 재생하는 동안 *Foo* 에 대한 원래 호출에서 `true`를 반환하는 경우 오케스트레이터 재생은 실행 기록에 없는 *SendNotification* 으로 호출됩니다. 지속성 작업 프레임워크는 이 불일치를 감지하고 *Bar* 에 대한 호출을 예상했을 때 *SendNotification* 에 대한 호출을 발견했기 때문에 `NonDeterministicOrchestrationException`을 throw합니다. 지속성 타이머 만들기, 외부 이벤트 대기, 하위 오케스트레이션 호출 등과 같이 다른 지속성 작업에 API 호출을 추가할 때 동일한 형식의 문제가 발생할 수 있습니다.

## <a name="mitigation-strategies"></a>완화 전략

버전 관리 문제를 처리하기 위한 몇 가지 전략은 다음과 같습니다.

* 아무 작업도 하지 않음(권장되지 않음)
* 진행 중인 모든 인스턴스 중지
* 병렬 배포

### <a name="do-nothing"></a>아무 작업도 수행하지 않음

경험이 없는 버전 관리 방식으로 인해 아무 작업도 수행되지 않고 진행 중인 오케스트레이션 인스턴스가 실패할 수 있습니다. 변경 유형에 따라 다음과 같은 유형의 오류가 발생할 수 있습니다.

* 오케스트레이션이 `NonDeterministicOrchestrationException` 오류와 함께 실패할 수 있습니다.
* 오케스트레이션이 무기한 중단되어 `Running` 상태를 보고할 수 있습니다.
* 함수가 제거되면 호출을 시도하는 모든 함수에서 오류가 발생하여 실패할 수 있습니다.
* 함수가 실행되도록 예약된 후 제거되면 앱이 지속성 작업 프레임워크 엔진에서 하위 수준 런타임 오류가 발생할 수 있으며, 이로 인해 심각하게 성능이 저하될 수 있습니다.

이러한 잠재적인 실패로 인해 "아무것도 하지 않음" 전략은 권장되지 않습니다.

### <a name="stop-all-in-flight-instances"></a>진행 중인 모든 인스턴스 중지

다른 옵션으로 진행 중인 모든 인스턴스를 중지하는 것입니다. 기본 [Durable Functions에 대한 Azure Storage 공급자](durable-functions-storage-providers.md#azure-storage)를 사용하는 경우 내부 **제어 큐** 및 **작업 항목 큐** 의 내용을 지워 모든 인스턴스를 중지할 수 있습니다. 또는 함수 앱을 중지하고, 이러한 큐를 삭제하고, 앱을 다시 시작할 수 있습니다. 앱이 다시 시작되면 큐가 자동으로 다시 만들어집니다. 이전 오케스트레이션 인스턴스는 "실행 중" 상태로 무기한으로 남아 있을 수 있지만 실패 메시지로 로그를 혼란스럽게 하거나 앱을 손상시키지 않습니다. 이 방법은 로컬 개발을 포함한 신속한 프로토타입 개발에 이상적입니다.

> [!NOTE]
> 이 방법을 사용하려면 기본 스토리지 리소스에 직접 액세스해야 하며, Durable Functions에서 지원하는 모든 스토리지 공급자에게는 적합하지 않습니다.

### <a name="side-by-side-deployments"></a>병렬 배포

주요 변경 내용을 안전하게 배포하는 가장 확실한 방법은 이전 버전과 함께 나란히 배포하는 것입니다. 이렇게 하려면 다음 방법 중 하나를 사용하면 됩니다.

* 기존 함수를 그대로 두고 완전히 새로운 함수로 모든 업데이트를 배포합니다. 이는 새 함수 버전의 호출자를 재귀적으로 업데이트하는 것과 관련된 복잡성 때문에 일반적으로 권장되지 않습니다.
* 다른 스토리지 계정을 사용하는 새 함수 앱으로 모든 업데이트를 배포합니다.
* 동일한 스토리지 계정을 사용하지만 업데이트된 [작업 허브](durable-functions-task-hubs.md) 이름으로 함수 앱의 새 복사본을 배포합니다. 그러면 새 버전의 앱에서 사용할 수 있는 새 스토리지 아티팩트가 만들어집니다. 이전 버전의 앱은 이전 스토리지 아티팩트 집합을 사용하여 계속 실행됩니다.

병렬 배포는 새 버전의 함수 앱을 배포하는 데 권장되는 기술입니다.

> [!NOTE]
> 병렬 배포 전략에 대한 이 지침은 Azure Storage 관련 용어를 사용하지만 일반적으로 지원되는 모든 [Durable Functions 스토리지 공급자](durable-functions-storage-providers.md)에 적용됩니다.

### <a name="deployment-slots"></a>배포 슬롯

Azure Functions 또는 Azure App Service에서 병렬 배포를 수행하는 경우 새 버전의 함수 앱을 새 [배포 슬롯](../functions-deployment-slots.md)에 배포하는 것이 좋습니다. 배포 슬롯을 사용하면 슬롯 중 하나만 활성 *프로덕션* 슬롯으로 사용하여 함수 앱의 여러 복사본을 병렬로 실행할 수 있습니다. 새 오케스트레이션 논리를 기존 인프라에 노출할 준비가 되면 새 버전을 프로덕션 슬롯으로 교환하는 것처럼 간단할 수 있습니다.

> [!NOTE]
> 이 전략은 오케스트레이터 함수에 HTTP와 웹후크 트리거를 사용할 때 가장 적합합니다. 큐 또는 Event Hubs와 같은 비 HTTP 트리거의 경우 트리거 정의는 교환 작업의 일부로 업데이트되는 [앱 설정에서 파생](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings)되어야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [성능 및 크기 조정 문제 처리](durable-functions-perf-and-scale.md)
