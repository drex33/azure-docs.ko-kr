---
title: Azure Policy 상태 변경 이벤트에 대응
description: Azure Event Grid를 사용하여 앱 정책 이벤트를 구독하면 복잡한 코드 없이 애플리케이션이 상태 변경에 대응할 수 있습니다.
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 4cd443882b3d1f9ffdcf0c317b6012654e0780c2
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538855"
---
# <a name="reacting-to-azure-policy-state-change-events"></a>Azure Policy 상태 변경 이벤트에 대응

Azure Policy 이벤트를 사용하면 애플리케이션이 상태 변경에 대응할 수 있습니다. 이 통합은 복잡한 코드나 비용이 많이 들고 비효율적인 폴링 서비스가 없어도 가능합니다. 대신, 이벤트는 [Azure Event Grid](../../../event-grid/index.yml)를 통해 [Azure Functions](../../../azure-functions/index.yml), [Azure Logic Apps](../../../logic-apps/index.yml)와 같은 구독자 또는 자체 사용자 지정 HTTP 수신기로도 푸시됩니다.
중요한 것은 사용한 양만큼만 요금을 지불한다는 것입니다.

Azure Policy 이벤트는 다양한 다시 시도 정책 및 배달 못한 편지를 통해 애플리케이션에 신뢰할 수 있는 배달 서비스를 제공하는 Azure Event Grid 서비스에 전송됩니다. 자세한 내용은 [Event Grid 메시지 배달 및 다시 시도](../../../event-grid/delivery-and-retry.md)를 참조하세요.

일반적인 Azure Policy 이벤트 시나리오에서는 정책을 평가하는 동안 리소스의 준수 상태가 변경되는 시기를 추적합니다. 이벤트 기반 아키텍처는 고정된 일정에 따라 리소스의 준수 상태를 스캔하지 않고 해당 변경 사항에 대응하는 효율적인 방법입니다.

> [!NOTE]
> [평가 트리거](../how-to/get-compliance-data.md#evaluation-triggers)가 리소스 평가를 완료한 다음, Azure Policy 상태 변경 이벤트가 Event Grid로 전송됩니다.

전체 자습서는 [Azure CLI를 사용하여 정책 상태 변경 이벤트를 Event Grid로 라우팅](../tutorials/route-state-change-events.md)을 참조하세요.

:::image type="content" source="../../../event-grid/media/overview/functional-model.png" alt-text="원본 및 처리기의 Event Grid 모델" lightbox="../../../event-grid/media/overview/functional-model-big.png":::

## <a name="available-azure-policy-events"></a>사용 가능한 Azure Policy 이벤트

Event Grid는 [이벤트 구독](../../../event-grid/concepts.md#event-subscriptions)을 사용하여 이벤트 메시지를 구독자에게 라우팅합니다. Azure Policy 이벤트 구독은 다음과 같은 세 가지 형식의 이벤트를 포함할 수 있습니다.

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.PolicyInsights.PolicyStateCreated | 정책 준수 상태를 만들 때 발생합니다. |
| Microsoft.PolicyInsights.PolicyStateChanged | 정책 준수 상태를 변경할 때 발생합니다. |
| Microsoft.PolicyInsights.PolicyStateDeleted | 정책 준수 상태를 삭제할 때 발생합니다. |

## <a name="event-schema"></a>이벤트 스키마

Azure Policy 이벤트에는 데이터 변경에 대응하는 데 필요한 모든 정보가 포함되어 있습니다. `eventType` 속성이 “Microsoft.PolicyInsights”로 시작되면 Azure Policy 이벤트를 식별할 수 있습니다.
Event Grid 이벤트 속성 사용에 대한 추가 정보는 [Event Grid 이벤트 스키마](../../../event-grid/event-schema.md)에 설명되어 있습니다.

| 속성 | 형식 | Description |
| -------- | ---- | ----------- |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `topic` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 리소스 이름과 리소스 형식을 포함하여 준수 상태 변경이 적용되는 리소스의 정규화된 ID입니다. `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` 형식 사용 |
| `data` | object | Azure Policy 이벤트 데이터. |
| `data.timestamp` | 문자열 | Azure Policy에서 리소스를 스캔한 시간(UTC)입니다. 이벤트 순서 지정을 위해 최상위 수준 `eventTime`이나 `time` 속성이 대신 이 속성을 사용합니다. |
| `data.policyAssignmentId` | 문자열 | 정책 할당의 리소스 ID입니다. |
| `data.policyDefinitionId` | 문자열 | 정책 정의의 리소스 ID입니다. |
| `data.policyDefinitionReferenceId` | 문자열 | 이니셔티브의 정책 할당이면 이니셔티브 정의에 있는 정책 정의의 참조 ID입니다. 비어 있을 수 있습니다. |
| `data.complianceState` | 문자열 | 정책 할당과 관련된 리소스의 준수 상태입니다. |
| `data.subscriptionId` | 문자열 | 리소스의 구독 ID입니다. |
| `data.complianceReasonCode` | 문자열 | 준수 이유 코드입니다. 비어 있을 수 있습니다. |
| `eventType` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `eventTime` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `dataVersion` | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| `metadataVersion` | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

정책 상태 변경 이벤트의 예는 다음과 같습니다.

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateChanged",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```

자세한 내용은 [Azure Policy 이벤트 스키마](../../../event-grid/event-schema-policy.md)를 참조하세요.

## <a name="practices-for-consuming-events"></a>이벤트 사용에 관한 지침

Azure Policy 이벤트를 처리하는 애플리케이션은 다음과 같은 권장 사항을 따라야 합니다.

> [!div class="checklist"]
> - 이벤트를 같은 이벤트 처리기로 라우팅하도록 여러 구독을 구성할 수 있습니다. 따라서 이벤트가 특정 원본에서 제공되었다고 가정하지 마세요. 대신 메시지 토픽을 확인하여 정책 할당, 정책 정의 및 상태 변경 이벤트의 리소스를 확인합니다.
> - `eventType`을 확인하고 수신하는 모든 이벤트가 예상한 형식일 것이라고 가정하지 마세요.
> - 최상위 `eventTime` 또는 `time` 속성이 아니라 `data.timestamp`를 사용하여 Azure Policy에서 이벤트 순서를 결정합니다.
> - 주체 필드를 사용하여 정책 상태가 변경된 리소스에 액세스합니다.

## <a name="next-steps"></a>다음 단계

Event Grid에 관해 자세히 알아보고 Azure Policy 상태 변경 이벤트를 사용해 보세요.

- [Azure CLI를 사용하여 정책 상태 변경 이벤트를 Event Grid로 라우팅](../tutorials/route-state-change-events.md)
- [Event Grid에 대한 Azure Policy 스키마 세부 정보](../../../event-grid/event-schema-policy.md)
- [Event Grid 정보](../../../event-grid/overview.md)
