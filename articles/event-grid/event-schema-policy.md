---
title: Event Grid 원본으로서의 Azure Policy
description: 이 문서에서는 Event Grid 이벤트 원본으로 Azure Policy를 사용하는 방법을 설명합니다. 스키마와 자습서 및 방법 문서 링크를 제공합니다.
ms.topic: conceptual
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/15/2021
ms.openlocfilehash: 3b5c984ca1374c50a312665c3f5e3bb063ed8feb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589780"
---
# <a name="azure-policy-as-an-event-grid-source"></a>Event Grid 원본으로서의 Azure Policy

이 문서에서는 [Azure Policy](../governance/policy/index.yml) 이벤트의 속성과 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](./event-schema.md)를 참조하세요. 또한 이벤트 원본으로 Azure Policy를 사용하기 위한 빠른 시작 및 자습서의 목록을 제공합니다.

## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

Azure Policy는 다음과 같은 이벤트 형식을 내보냅니다.

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.PolicyInsights.PolicyStateCreated | 정책 준수 상태를 만들 때 발생합니다. |
| Microsoft.PolicyInsights.PolicyStateChanged | 정책 준수 상태를 변경할 때 발생합니다. |
| Microsoft.PolicyInsights.PolicyStateDeleted | 정책 준수 상태를 삭제할 때 발생합니다. |

## <a name="example-event"></a>예제 이벤트

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)
다음 예제는 정책 상태 생성 이벤트의 스키마를 보여 줍니다. 

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
    "eventType": "Microsoft.PolicyInsights.PolicyStateCreated",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```

정책 상태 변경 이벤트의 스키마는 다음과 비슷합니다. 

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
# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

다음 예제는 정책 상태 생성 이벤트의 스키마를 보여 줍니다. 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
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
    "type": "Microsoft.PolicyInsights.PolicyStateCreated",
    "time": "2021-03-27T18:37:42.5241536Z",
    "specversion": "1.0"
}]
```

정책 상태 변경 이벤트의 스키마는 다음과 비슷합니다. 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
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
    "type": "Microsoft.PolicyInsights.PolicyStateChanged",
    "time": "2021-03-27T18:37:42.5241536Z",
    "specversion": "1.0"
}]
```

---

## <a name="event-properties"></a>이벤트 속성

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| `topic` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 리소스 이름과 리소스 형식을 포함하여 준수 상태 변경이 적용되는 리소스의 정규화된 ID입니다. `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` 형식 사용 |
| `eventType` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `eventTime` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `data` | object | Azure Policy 이벤트 데이터. |
| `dataVersion` | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| `metadataVersion` | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| `source` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 리소스 이름과 리소스 형식을 포함하여 준수 상태 변경이 적용되는 리소스의 정규화된 ID입니다. `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` 형식 사용 |
| `type` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `time` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `data` | object | Azure Policy 이벤트 데이터. |
| `specversion` | 문자열 | CloudEvents 스키마 사양 버전입니다. |

---

데이터 개체의 속성은 다음과 같습니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| `timestamp` | 문자열 | Azure Policy에서 리소스를 스캔한 시간(UTC)입니다. 이벤트 순서 지정을 위해 최상위 수준 `eventTime`이나 `time` 속성이 아니라 이 속성을 사용합니다. |
| `policyAssignmentId` | 문자열 | 정책 할당의 리소스 ID입니다. |
| `policyDefinitionId` | 문자열 | 정책 정의의 리소스 ID입니다. |
| `policyDefinitionReferenceId` | 문자열 | 이니셔티브의 정책 할당이면 이니셔티브 정의에 있는 정책 정의의 참조 ID입니다. 비어 있을 수 있습니다. |
| `complianceState` | 문자열 | 정책 할당과 관련된 리소스의 준수 상태입니다. |
| `subscriptionId` | 문자열 | 리소스의 구독 ID입니다. |
| `complianceReasonCode` | 문자열 | 준수 이유 코드입니다. 비어 있을 수 있습니다. |

## <a name="next-steps"></a>다음 단계

- Azure Policy 상태 변경 이벤트를 라우팅하기 위한 연습은 [정책 상태 변경 알림에 Event Grid 사용](../governance/policy/tutorials/route-state-change-events.md)을 참조하세요.
- Event Grid와 Azure Policy를 통합하는 방법에 관한 개요는 [Event Grid를 사용하여 Azure Policy 이벤트에 대응](../governance/policy/concepts/event-overview.md)을 참조하세요.
- Azure Event Grid에 대한 소개는 [Event Grid란?](./overview.md)을 참조하세요.
- Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](./subscription-creation-schema.md)를 참조하세요.