---
title: 가동 중지 시간이 0인 Durable Functions 배포
description: 가동 중지 시간이 0인 배포를 위해 Durable Functions 오케스트레이션을 사용하도록 설정하는 방법을 알아봅니다.
author: tsushi
ms.topic: conceptual
ms.date: 05/11/2021
ms.author: azfuncdf
ms.custom: fasttrack-edit
ms.openlocfilehash: 39f05737b84676fc4e993293c06a87d3b35a4ca5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132332443"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>가동 중지 시간이 0인 Durable Functions 배포

Durable Functions의 [안정적인 실행 모델](./durable-functions-orchestrations.md)을 사용하려면 오케스트레이션이 결정적이어야 합니다. 이 경우 업데이트를 배포할 때 고려해야 추가 당면 과제가 있습니다. 배포에 작업 함수 서명 또는 오케스트레이터 논리에 대한 변경 내용이 포함된 경우 진행 중인 오케스트레이션 인스턴스가 실패합니다. 이는 수 시간 또는 수일이 소요될 수 있는 장기 실행 오케스트레이션 인스턴스에서 특히 문제가 됩니다.

이러한 오류가 발생하지 않도록 하려면 다음 두 가지 옵션을 사용할 수 있습니다. 
- 실행 중인 모든 오케스트레이션 인스턴스가 완료될 때까지 배포를 연기합니다.
- 실행 중인 오케스트레이션 인스턴스에서 기존 버전의 함수를 사용하는지 확인합니다. 

다음 차트에서는 Durable Functions에 대해 가동 중지 시간이 0인 배포를 구현하는 세 가지 주요 전략을 비교합니다. 

| 전략 |  사용 시기 | 장점 | 단점 |
| -------- | ------------ | ---- | ---- |
| [버전 관리](#versioning) |  애플리케이션에 [호환성이 손상되는 변경](durable-functions-versioning.md)이 자주 발생하지 않습니다. | 구현이 간편합니다. |  메모리의 함수 앱 크기와 함수 개수가 증가합니다.<br/>코드가 중복됩니다. |
| [슬롯으로 상태 검사](#status-check-with-slot) | 시스템에 장기 실행 오케스트레이션이 24시간 넘게 지속되거나 자주 겹치는 오케스트레이션이 없습니다. | 코드 베이스가 간단합니다.<br/>추가적인 함수 앱 관리가 필요하지 않습니다. | 추가 스토리지 계정 또는 작업 허브 관리가 필요합니다.<br/>오케스트레이션이 실행되지 않는 기간이 필요합니다. |
| [애플리케이션 라우팅](#application-routing) | 시스템에 오케스트레이션이 실행되지 않는 기간(예: 오케스트레이션이 24시간 넘게 지속되거나 자주 겹치는 오케스트레이션이 있는 기간)이 없습니다. | 호환성이 손상되는 변경 사항이 있는 오케스트레이션을 지속적으로 실행하는 새 버전의 시스템을 처리합니다. | 지능형 애플리케이션 라우터가 필요합니다.<br/>구독에서 허용하는 함수 앱의 개수를 최대로 설정할 수 있습니다. 기본값은 100입니다. |

이 문서의 나머지 부분에서는 이러한 전략에 대해 자세히 설명합니다.

> [!NOTE]
> 이러한 가동 중지 시간 없는 배포 전략에 대한 설명에서는 Durable Functions 기본 Azure Storage 공급자를 사용한다고 가정합니다. 기본 Azure Storage 공급자 이외의 스토리지 공급자를 사용하는 경우 지침이 적절하지 않을 수 있습니다. 다양한 스토리지 공급자 옵션 및 비교 방법에 대한 자세한 내용은 [Durable Functions 스토리지 공급자](durable-functions-storage-providers.md) 설명서를 참조하세요.

## <a name="versioning"></a>버전 관리

함수의 새 버전을 정의하고 함수 앱에 이전 버전을 그대로 둡니다. 다이어그램에서 볼 수 있듯이 함수의 버전은 이름에 포함됩니다. 이전 버전의 함수가 유지되므로 진행 중인 오케스트레이션 인스턴스가 계속해서 참조할 수 있습니다. 한편, 새 오케스트레이션 인스턴스에 대한 요청은 최신 버전을 필요로 하며, 해당 버전은 오케스트레이션 클라이언트 함수가 앱 설정에서 참조할 수 있습니다.

![버전 관리 전략](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

이 전략에서는 모든 함수를 복사하고 다른 함수에 대한 참조를 업데이트해야 합니다. 스크립트를 작성하면 이 작업을 더 쉽게 수행할 수 있습니다. 마이그레이션 스크립트를 사용하는 [샘플 프로젝트](https://github.com/TsuyoshiUshio/DurableVersioning)는 다음과 같습니다.

> [!NOTE]
> 이 전략은 배포 중 가동 중지 시간을 방지하기 위해 배포 슬롯을 사용합니다. 새 배포 슬롯을 만들고 사용하는 방법에 대한 자세한 내용은 [Azure Functions 배포 슬롯](../functions-deployment-slots.md)을 참조하세요.

## <a name="status-check-with-slot"></a>슬롯으로 상태 검사

프로덕션 슬롯에서 현재 버전의 함수 앱이 실행되는 동안 새 버전의 함수 앱을 스테이징 슬롯에 배포합니다. 프로덕션과 스테이징 슬롯을 교환하기 전에 실행 중인 오케스트레이션 인스턴스가 있는지 확인하세요. 모든 오케스트레이션 인스턴스를 완료한 후에 교환을 수행할 수 있습니다. 이 전략은 실행 중인 오케스트레이션 인스턴스가 없을 때 예측 가능한 기간이 있는 경우에 적합합니다. 오케스트레이션이 장기 실행되지 않고 오케스트레이션 실행이 자주 겹치지 않는 경우에 가장 효과적인 방식입니다.

### <a name="function-app-configuration"></a>함수 앱 구성

다음 절차에 따라 이 시나리오를 설정합니다.

1. 스테이징 및 프로덕션을 위해 함수 앱에 [배포 슬롯을 추가](../functions-deployment-slots.md#add-a-slot)합니다.

1. 각 슬롯에 대해 [AzureWebJobsStorage 애플리케이션 설정](../functions-app-settings.md#azurewebjobsstorage)을 공유 스토리지 계정의 연결 문자열로 설정합니다. 이 스토리지 계정 연결 문자열은 Azure Functions 런타임에서 [함수의 액세스 키](../security-concepts.md#function-access-keys)를 안전하게 저장하는 데 사용됩니다.

1. 각 슬롯에 대해 새 앱 설정(예: `DurableManagementStorage`)을 만듭니다. 값을 여러 스토리지 계정의 연결 문자열로 설정합니다. 해당 스토리지 계정은 [안정적인 실행](./durable-functions-orchestrations.md)을 위해 Durable Functions 확장에서 사용됩니다. 각 슬롯에 대해 별도의 스토리지 계정을 사용합니다. 해당 설정을 배포 슬롯 설정으로 표시하지 마세요.

1. 함수 앱의 [host.json 파일의 durableTask 섹션](durable-functions-bindings.md#hostjson-settings)에서 `connectionStringName`(Durable 2.x) 또는 `azureStorageConnectionStringName`(Durable 1.x)를 3단계에서 만든 앱 설정의 이름으로 지정합니다.

다음 다이어그램에서는 배포 슬롯과 스토리지 계정의 구성에 대한 설명을 보여줍니다. 이 잠재적 배포 전 시나리오에서, 함수 앱의 버전 2는 프로덕션 슬롯에서 실행되고 버전 1은 스테이징 슬롯에 남아 있습니다.

![배포 슬롯 및 스토리지 계정](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>host.json 예제

다음 JSON 조각은 *host.js* 파일에 있는 연결 문자열 설정의 예제입니다.

#### <a name="functions-20"></a>함수 2.0

```json
{
  "version": 2.0,
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub",
      "storageProvider": {
        "connectionStringName": "DurableManagementStorage"
      }
    }
  }
}
```

#### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "DurableManagementStorage"
  }
}
```

### <a name="cicd-pipeline-configuration"></a>CI/CD 파이프라인 구성

함수 앱에 보류 중이거나 실행 중인 오케스트레이션 인스턴스가 없는 경우에만 배포하도록 CI/CD 파이프라인을 구성합니다. Azure Pipelines를 사용하는 경우 다음 예제와 같이 이러한 조건을 확인하는 함수를 만들 수 있습니다.

```csharp
[FunctionName("StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var result = await client.ListInstancesAsync(new OrchestrationStatusQueryCondition() { RuntimeStatus = runtimeStatus }, CancellationToken.None);
    return (ActionResult)new OkObjectResult(new { HasRunning = result.DurableOrchestrationState.Any() });
}
```

다음으로, 오케스트레이션이 실행되지 않을 때까지 대기하도록 스테이징 게이트를 구성합니다. 자세한 내용은 [게이트를 통한 릴리스 배포 제어](/azure/devops/pipelines/release/approvals/gates)를 참조하세요.

![배포 게이트](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure Pipelines는 배포를 시작하기 전에 함수 앱에 실행 중인 오케스트레이션 인스턴스가 있는지 확인합니다.

![배포 게이트(실행 중)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

이제 새 버전의 함수 앱을 스테이징 슬롯에 배포해야 합니다.

![스테이징 슬롯](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

마지막으로 슬롯을 교환합니다. 

배포 슬롯 설정으로 표시되지 않은 애플리케이션 설정도 교환되므로 버전 2 앱은 스토리지 계정 A에 대한 참조를 유지합니다. 오케스트레이션 상태는 스토리지 계정에서 추적되므로 버전 2 앱에서 실행되는 오케스트레이션은 중단 없이 새 슬롯에서 계속 실행됩니다.

![배포 슬롯](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

두 슬롯에 동일한 스토리지 계정을 사용하려면 작업 허브의 이름을 변경하면 됩니다. 이 경우 슬롯의 상태와 앱의 HubName 설정을 관리해야 합니다. 자세한 내용은 [Durable Functions의 작업 허브](durable-functions-task-hubs.md)를 참조하세요.

## <a name="application-routing"></a>애플리케이션 라우팅

이 전략은 가장 복잡합니다. 그러나 오케스트레이션 실행 사이에 시간이 없는 함수 앱에도 사용할 수 있습니다.

이 전략의 경우 Durable Functions 앞에 ‘애플리케이션 라우터’를 만들어야 합니다. Durable Functions를 사용하여 이 라우터를 구현할 수 있습니다. 라우터는 다음과 같은 역할을 합니다.

* 함수 앱을 배포합니다.
* Durable Functions 버전을 관리합니다. 
* 오케스트레이션 요청을 함수 앱으로 라우팅합니다.

오케스트레이션 요청이 처음 수신될 때 라우터는 다음 작업을 수행합니다.

1. Azure에서 새 함수 앱을 만듭니다.
2. 함수 앱의 코드를 Azure의 새 함수 앱에 배포합니다.
3. 오케스트레이션 요청을 새 앱으로 전달합니다.

라우터는 앱 코드의 어떤 버전이 Azure의 어떤 함수 앱에 배포되는지의 상태를 관리합니다.

![애플리케이션 라우팅(처음)](media/durable-functions-zero-downtime-deployment/application-routing.png)

라우터는 요청과 함께 전송된 버전에 따라 적절한 함수 앱에 배포 및 오케스트레이션 요청을 보냅니다. 패치 버전은 무시합니다.

호환성이 손상되는 변경 없이 새 버전의 앱을 배포하는 경우 패치 버전을 높일 수 있습니다. 라우터가 기존 함수 앱에 배포하고 코드의 이전 버전 및 새 버전에 대한 요청을 보내면 동일한 함수 앱으로 라우팅됩니다.

![애플리케이션 라우팅(호환성이 손상되는 변경 없음)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

호환성이 손상되는 변경으로 새 버전의 앱을 배포할 때 주 버전 또는 부 버전을 높일 수 있습니다. 그러면 애플리케이션 라우터는 Azure에서 새로운 함수 앱을 만들어 배포하고 앱의 새 버전에 대한 요청을 라우팅합니다. 다음 다이어그램에서는 1.0.1 버전의 앱에서 실행 중인 오케스트레이션은 계속 실행되지만 1.1.0 버전에 대한 요청은 새 함수 앱으로 라우팅됩니다.

![애플리케이션 라우팅(호환성이 손상되는 변경)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

라우터는 1.0.1 버전에서 오케스트레이션의 상태를 모니터링하고 모든 오케스트레이션이 완료되면 앱을 삭제합니다. 

### <a name="tracking-store-settings"></a>추적 저장 설정

각 함수 앱은 가급적 별도의 스토리지 계정으로 별도의 일정 큐를 사용해야 합니다. 모든 버전의 애플리케이션에서 모든 오케스트레이션 인스턴스를 쿼리하려면 함수 앱에서 인스턴스 및 기록 테이블을 공유할 수 있습니다. 테이블을 공유하려면 모두 동일한 값을 사용하도록 [host.json](durable-functions-bindings.md#host-json) 파일의 `trackingStoreConnectionStringName` 및 `trackingStoreNamePrefix` 설정을 구성하면 됩니다.

자세한 내용은 [Azure에서 Durable Functions 인스턴스 관리](durable-functions-instance-management.md)를 참조하세요.

![추적 저장 설정](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Durable Functions 버전 관리](durable-functions-versioning.md)
