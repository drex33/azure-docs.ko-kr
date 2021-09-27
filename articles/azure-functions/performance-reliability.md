---
title: Azure Functions 성능 및 안정성 향상
description: Azure에서 함수를 실행 하는 성능 및 안정성을 가장 잘 개선 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 08/25/2021
ms.openlocfilehash: a554407b3d3b222507245823c1962071bbebe929
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128705703"
---
# <a name="improve-the-performance-and-reliability-of-azure-functions"></a>Azure Functions의 성능 및 안정성 향상

이 문서에서는 [서버를 사용하지 않는](https://azure.microsoft.com/solutions/serverless/) 함수 앱의 성능 및 안정성을 개선하기 위한 지침을 제공합니다. 보다 일반적인 Azure Functions 모범 사례 집합은 [Azure Functions 모범 사례](functions-best-practices.md)를 참조 하세요.

Azure Functions를 사용하여 서버가 없는 솔루션을 빌드하고 설계하는 방법의 모범 사례는 다음과 같습니다.

## <a name="avoid-long-running-functions"></a>장기 실행 함수 방지

큰 장기 실행 함수는 예기치 않은 시간 초과 문제를 발생시킬 수 있습니다. 지정된 호스팅 계획의 시간 제한에 대해 자세히 알아보려면 [함수 앱 시간 제한 기간](functions-scale.md#timeout)을 참조하세요.

함수는 많은 Node.js 종속성으로 인해 커질 수 있습니다. 또한 종속성을 가져올 때 로드 시간이 증가하여 예기치 않은 시간 초과가 발생할 수 있습니다. 종속성은 명시적 및 암시적으로 로드됩니다. 코드를 통해 로드되는 단일 모듈은 자체 추가 모듈을 로드할 수 있습니다.

큰 함수를 더 작은 함수 집합으로 리팩터링할 때마다 함께 작동하고 빠른 응답을 반환합니다. 예를 들어 webhook 또는 HTTP 트리거 함수가 특정 시간 제한 내의 승인 응답을 요구할 수 있습니다. webhook의 경우 즉각적인 응답을 요구하는 것이 일반적입니다. HTTP 트리거 페이로드를 큐 트리거 함수에 의해 처리되도록 큐에 전달할 수 있습니다. 이 방법으로 실제 작업을 연기하고 즉각적인 응답을 반환할 수 있습니다.

## <a name="cross-function-communication"></a>함수 통신 교차

[지속형 함수](durable/durable-functions-overview.md) 및 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)는 여러 함수 간에 상태 전환 및 통신을 관리하도록 빌드됩니다.

Durable Functions 및 Logic Apps를 사용하지 않고 여러 기능을 통합하는 경우 함수 통신 교차를 위해 스토리지 큐를 사용하는 것이 가장 좋습니다. 주된 이유는 스토리지 큐가 다른 스토리지 옵션보다 더 저렴하고 더 쉽게 프로비전할 수 있기 때문입니다.

스토리지 큐에 있는 개별 메시지 크기는 64KB로 제한됩니다. 함수 간에 더 큰 메시지를 전달해야 하는 경우 Azure Service Bus 큐를 사용하면 표준 계층에서는 최대 256KB, 프리미엄 계층에서는 최대 1MB의 메시지를 지원할 수 있습니다.

Service Bus 토픽은 메시지를 처리하기 전에 필터링해야 하는 경우에 유용합니다.

이벤트 허브는 고용량 통신을 지원하는 데 유용합니다.

## <a name="write-functions-to-be-stateless"></a>상태 비저장 함수 작성

함수는 가능하면 상태 비저장이며 idempotent여야 합니다. 필요한 상태 정보를 데이터와 연결합니다. 예를 들어 처리할 주문에 연결된 `state` 멤버가 있을 수 있습니다. 함수는 주문을 해당 상태에 따라 처리하며 함수 자체는 비저장 상태로 남아 있을 수 있습니다.

Idempotent 함수는 특히 타이머 트리거 사용이 권장됩니다. 예를 들어 반드시 하루에 한 번 실행해야 하는 항목이 있는 경우 하루 동안 언제든지 같은 결과로 실행할 수 있도록 작성합니다. 함수는 특정 날짜에 대한 작업이 없는 경우 종료될 수 있습니다. 또한 이전 실행이 완료되지 못한 경우 다음 실행은 중단되었던 부분으로 돌아가야 합니다.

## <a name="write-defensive-functions"></a>방어적 함수 작성

함수에는 언제든지 예외가 발생할 수 있다고 가정합니다. 다음 실행 동안 이전 실패 지점에서 계속할 수 있는 기능을 넣어 함수를 설계합니다. 다음과 같은 작업이 필요한 시나리오를 고려하세요.

1. 데이터베이스의 10,000개 행에 대해 쿼리합니다.
2. 앞으로 처리할 각 행에 대한 큐 메시지를 만듭니다.

시스템의 복잡성에 따라, 잘못 작동되는 관련된 다운스트림 서비스, 네트워킹 작동 중단 또는 할당량 제한 초과 등이 있을 수 있습니다. 이러한 항목은 모두 언제든지 함수에 영향을 줄 수 있습니다. 함수가 이에 대비할 수 있도록 설계해야 합니다.

이러한 항목 중 5,000개를 처리를 위해 큐에 삽입한 후 오류가 발생한다면 코드는 어떻게 반응할까요? 사용자가 완료한 집합에서 항목을 추적합니다. 다른 방법으로 다음 번에 해당 항목을 삽입할 수도 있습니다. 이러한 이중 삽입은 작업 흐름에 심각한 영향을 줄 수 있으므로 [함수를 idempotent로 만듭니다](functions-idempotent.md). 

큐 항목을 이미 처리한 경우 함수는 수행되지 않습니다.

Azure Functions 플랫폼에서 사용하는 구성 요소를 위해 이미 제공된 방어 수단을 활용하세요. 예를 들어 [Azure Storage 큐 트리거 및 바인딩](functions-bindings-storage-queue-trigger.md#poison-messages)을 위한 설명서에서 **포이즌 큐 메시지 처리** 를 참조하세요.

## <a name="function-organization-best-practices"></a>함수 구성 모범 사례

솔루션의 일부로 여러 함수를 개발하고 게시할 수 있습니다. 이러한 함수는 대개 단일 함수 앱으로 결합되지만 별도의 함수 앱에서 실행할 수도 있습니다. 프리미엄 및 전용(App Service) 호스팅 계획에서, 여러 함수 앱이 동일한 플랜으로 실행되어 동일한 리소스를 공유할 수도 있습니다. 함수 및 함수 앱을 그룹화하는 방법은 전체 솔루션의 성능, 스케일링, 구성, 배포, 보안에 영향을 줄 수 있습니다. 모든 시나리오에 적용되는 규칙은 없으므로 함수를 계획하고 개발하는 경우 이 섹션의 정보를 고려하세요.

### <a name="organize-functions-for-performance-and-scaling"></a>성능 및 스케일링을 위한 함수 구성

사용자가 만드는 각 함수에는 메모리 공간이 사용됩니다. 이 공간은 일반적으로 작지만 함수 앱 내에 너무 많은 함수가 있으면 새 인스턴스에서 앱을 시작하는 속도가 느려질 수 있습니다. 또한 함수 앱의 전체 메모리 사용량이 높은 것일 수도 있습니다. 특정 워크로드에 따라 단일 앱에 있어야 하는 함수 개수를 말하는 것은 어렵습니다. 그러나 함수에서 많은 양의 데이터를 메모리에 저장하는 경우 단일 앱에서 함수 개수를 줄이는 것이 좋습니다.

단일 Premium 계획 또는 전용 (App Service) 계획으로 여러 함수 앱을 실행 하는 경우 이러한 앱은 모두 계획에 할당 된 것과 동일한 리소스를 공유 합니다. 다른 함수 앱보다 메모리 요구 사항이 훨씬 더 높은 함수 앱이 있는 경우 앱이 배포되는 각 인스턴스에 대해 불균형하게 메모리 리소스 양을 사용합니다. 이로 인해 각 인스턴스의 다른 앱에 사용할 수 있는 메모리가 줄어들 수 있으므로 별도의 자체 호스팅 계획에서 이와 같이 메모리를 많이 사용하는 함수 앱을 실행하는 것이 좋습니다.

> [!NOTE]
> [소비 플랜](./functions-scale.md)을 사용하는 경우 앱이 독립적으로 스케일링되므로 항상 각 앱을 자체 플랜에 배치하는 것이 좋습니다.

다른 부하 프로필을 사용하여 함수를 그룹화할지 여부를 고려합니다. 예를 들어 몇 천 개에 달하는 많은 큐 메시지를 처리하는 함수가 있고 가끔 호출되지만 메모리 요구 사항이 높은 함수가 있는 경우에는 별도의 함수 앱에 배포하여 해당 함수들이 고유한 리소스 집합을 가지고 서로 독립적으로 스케일링하도록 할 수 있습니다.

### <a name="organize-functions-for-configuration-and-deployment"></a>구성 및 배포를 위한 함수 구성

함수 앱에는 함수 트리거의 고급 동작 및 Azure Functions 런타임을 구성하는 데 사용되는 `host.json` 파일이 있습니다. `host.json` 파일에 대한 변경 내용은 앱 내의 모든 함수에 적용됩니다. 사용자 지정 구성이 필요한 몇 가지 함수가 있는 경우 해당 함수를 자체 함수 앱으로 이동하는 것이 좋습니다.

로컬 프로젝트의 모든 함수는 Azure의 함수 앱에 파일 집합으로 함께 배포됩니다. 개별 함수를 별도로 배포하거나 일부 함수에 대해서는 [배포 슬롯](./functions-deployment-slots.md)과 같은 기능을 사용해야 할 수도 있습니다. 이러한 경우 개별 코드 프로젝트에서 이러한 함수를 다양한 함수 앱에 배포해야 합니다.

### <a name="organize-functions-by-privilege"></a>권한으로 함수 구성

애플리케이션 설정에 저장된 연결 문자열 및 기타 자격 증명은 함수 앱의 모든 함수에 연결된 리소스에 대한 동일한 권한 집합을 제공합니다. 이러한 자격 증명을 사용하지 않는 함수를 별도의 함수 앱으로 이동하여 특정 자격 증명에 액세스할 수 있는 함수의 수를 최소화하는 것이 좋습니다. 항상 [함수 체이닝](/learn/modules/chain-azure-functions-data-using-bindings/)과 같은 기술을 사용하여 다른 함수 앱에 있는 함수 사이에서 데이터를 전달할 수 있습니다.  

## <a name="scalability-best-practices"></a>확장성 모범 사례

함수 앱의 인스턴스 크기를 조정하는 방법에 영향을 주는 여러 가지 요인이 있습니다. 자세한 내용은 [함수 크기 조정](functions-scale.md)의 설명서에서 제공됩니다.  함수 앱에 최적의 확장성을 보장하는 몇 가지 모범 사례는 다음과 같습니다.

### <a name="share-and-manage-connections"></a>연결 공유 및 관리

가능하면 외부 리소스에 대한 연결을 다시 사용합니다. [Azure Functions에서 연결을 관리하는 방법](./manage-connections.md)을 참조하세요.

### <a name="avoid-sharing-storage-accounts"></a>스토리지 계정 공유 방지

함수 앱을 만들 때 이를 스토리지 계정과 연결해야 합니다. 스토리지 계정 연결은 [AzureWebJobsStorage 애플리케이션 설정](./functions-app-settings.md#azurewebjobsstorage)에서 유지 관리됩니다.

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>동일한 함수 앱에서 테스트와 프로덕션 코드의 혼합 금지

함수 앱 공유 리소스 내에서 작용합니다. 예를 들어 메모리는 공유됩니다. 프로덕션 환경에서 함수 앱을 사용하는 경우 테스트 관련 함수 및 리소스를 추가하지 마세요. 프로덕션 코드를 실행하는 동안 예기치 않은 오버 헤드가 발생할 수 있습니다.

프로덕션 함수 앱에서 로드하는 것에 주의하세요. 메모리는 앱에서 각 함수가 사용하는 것의 평균으로 계산됩니다.

여러 .NET 함수에서 참조되는 공유 어셈블리가 있는 경우 일반적인 공유 폴더에 저장합니다. 그러지 않으면 함수 간 다르게 작동하는 동일한 이진의 여러 테스트 버전을 실수로 배포할 수 있습니다.

성능에 부정적인 영향을 주는 프로덕션 코드에는 자세한 정보 로깅을 사용하지 마세요.

### <a name="use-async-code-but-avoid-blocking-calls"></a>비동기 코드는 사용, 호출 차단 방지

비동기 프로그래밍은 특히 I/O 작업을 차단할 때 권장되는 모범 사례입니다.

C#에서는 `Task` 인스턴스에 대한 `Wait` 메서드를 호출하거나 `Result` 속성을 참조하는 것은 항상 피하세요. 이 방법은 스레드를 소진시킬 수 있습니다.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>여러 작업자 프로세스 사용

기본적으로 함수에 대한 모든 호스트 인스턴스는 단일 작업자 프로세스를 사용합니다. 특히 Python과 같은 단일 스레드 런타임을 사용하여 성능을 향상시키려면 [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count)를 사용하여 호스트당 작업자 프로세스 수를 늘립니다(최대 10개). 그러면 Azure Functions는 이러한 작업자 사이에 동시 함수 호출을 균등하게 분산하려고 시도합니다.

요구 사항을 충족하기 위해 애플리케이션을 스케일 아웃할 때 Functions가 만드는 각 호스트에 FUNCTIONS_WORKER_PROCESS_COUNT가 적용됩니다.

### <a name="receive-messages-in-batch-whenever-possible"></a>가능하면 항상 일괄 처리로 메시지를 수신합니다.

Event Hub와 같은 일부 트리거는 단일 호출에서 일괄 처리 메시지를 수신할 수 있습니다.  메시지를 일괄 처리하면 성능이 향상됩니다.  [host.json 참조 설명서](functions-host-json.md)에 설명된 대로 `host.json` 파일에서 최대 일괄 처리 크기를 구성할 수 있습니다.

C# 함수의 경우 강력한 형식의 배열로 형식을 변경할 수 있습니다.  예를 들어 메서드 서명은 `EventData sensorEvent` 대신 `EventData[] sensorEvent`일 수 있습니다.  다른 언어의 경우 [여기 표시된 대로](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10) 일괄 처리를 사용할 수 있도록 하기 위해 `function.json`에서 명시적으로 카디널리티 속성을 `many`로 설정해야 합니다.

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>동시성을 처리하도록 호스트 동작 구성

호스트 런타임 및 트리거 동작의 구성에 함수 앱의 `host.json` 파일을 사용할 수 있습니다.  동작을 일괄 처리하는 것 외에도 여러 트리거에 대한 동시성을 관리할 수 있습니다. 이러한 옵션의 값을 조정하면 호출된 함수의 요구에 맞게 각 인스턴스의 크기를 조정할 수 있습니다.

host.json 파일의 설정은 함수의 *단일 인스턴스* 내에 있는 앱의 모든 기능에 적용됩니다. 예를 들어 2개의 HTTP 함수 및 [`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) 요청이 25로 설정된 함수 앱이 있는 경우 HTTP 트리거에 대한 요청은 25개의 공유 동시 요청에 대한 계산에 포함됩니다.  해당 함수 앱의 크기가 10개의 인스턴스로 스케일링된 경우 10개의 함수는 250개의 동시 요청을 효과적으로 허용합니다(10개 인스턴스 * 25개 인스턴스당 동시 요청). 

다른 호스트 구성 옵션은 [host.json 구성 문서](functions-host-json.md)에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 자료를 참조하세요.

* [Azure Functions에서 연결을 관리하는 방법](manage-connections.md)
* [Azure App Service 모범 사례](../app-service/app-service-best-practices.md)
