---
title: 보안 태세를 강화하도록 Event Grid 리소스에 대한 사용자 지정 Azure 정책 구성
description: 이 문서는 보안 제어를 적용하는 사용자 지정 Azure 정책을 정의하는 데 도움이 됩니다.
ms.topic: how-to
author: jfggdl
ms.author: jafernan
ms.date: 06/24/2021
ms.openlocfilehash: a9018150d75556f729db593ec1a76fb6aa174798
ms.sourcegitcommit: 0beea0b1d8475672456da0b3a4485d133283c5ea
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2021
ms.locfileid: "112992339"
---
# <a name="use-custom-azure-policies-to-enforce-security-controls"></a>사용자 지정 Azure 정책을 사용하여 보안 제어 적용

이 문서에서는 Event Grid의 이벤트 구독에서 구성할 수 있는 대상을 제어하는 샘플 사용자 지정 Azure 정책을 제공합니다. [Azure Policy](../governance/policy/overview.md)는 보안, 비용, 리소스 일관성, 관리 등과 같은 다양한 문제에 대해 조직 표준 및 규정 준수를 적용하는 데 도움이 됩니다. 이러한 문제 중에서 눈에 띄는 것은 조직의 보안 태세를 유지하는 데 도움이 되는 보안 규정 준수 표준입니다. 이 문서에 나와 있는 정책은 보안 제어를 지원하기 위해 데이터 유출 또는 인증되지 않은 엔드포인트나 Azure 서비스로 이벤트 전송을 방지하는 데 도움이 됩니다.

> [!NOTE]
> Azure Event Grid는 여러 규정 준수 표준과 관련된 규정 준수 도메인 및 보안 제어에 대한 기본 제공 정책을 제공합니다. 이러한 기본 제공 정책은 Event Grid의 [Azure 보안 벤치마크](security-controls-policy.md#azure-security-benchmark)에서 찾을 수 있습니다.

데이터 유출을 방지하기 위해 조직은 Event Grid가 이벤트를 제공할 수 있는 대상을 제한하려고 할 수 있습니다. 이 작업은 정책에서 승인된 대상 중 하나를 대상으로 하는 [이벤트 구독](concepts.md#event-subscriptions)을 생성하거나 업데이트할 수 있는 정책을 할당하여 수행할 수 있습니다. 리소스 요청을 방지하는 데 사용되는 정책 효과는 [deny](../governance/policy/concepts/effects.md#deny)입니다.

다음 섹션에서는 허용된 대상 목록을 적용하는 샘플 정책 정의를 보여 줍니다. ```destination```이 포함된 [속성 별칭](../governance/policy/concepts/definition-structure.md#aliases)을 검색하여 ```field```로 사용하여 정책을 정의할 때 허용된 대상 목록과 비교하려고 합니다.

[이 문서 섹션](../governance/policy/concepts/definition-structure.md#aliases)에서 설명하는 CLI 또는 PowerShell 명령을 실행하여 Event Grid에 대해 정의된 속성 별칭을 찾을 수 있습니다(네임스페이스 ```Microsoft.EventGrid``` 사용).

정책 정의에 대한 자세한 내용은 [Azure Policy 정의 구조](../governance/policy/concepts/definition-structure.md) 문서를 참조하세요.

 
## <a name="define-an-azure-policy-with-a-list-of-allowed-destinations-for-a-webhook-destination"></a>웹후크 대상에 허용되는 대상 목록을 사용하여 Azure Policy 정의

다음 정책 정의는 시스템 토픽에 대한 이벤트 구독에 구성된 웹후크 엔드포인트 대상을 제한합니다.

```json
{
  "mode": "All",
  "policyRule": {
    "if": {
      "not": {
        "field": "Microsoft.EventGrid/systemTopics/eventSubscriptions/destination.WebHook.endpointUrl",
        "in": "[parameters('allowedDestinationEndpointURLs')]"
      }
    },
    "then": {
      "effect": "deny"
    }
  },
  "parameters": {
    "allowedDestinationEndpointURLs": {
      "type": "Array",
      "metadata": {
        "description": "Allowed event destination endpoint URLs.",
        "displayName": "The list of allowed webhook endpoint destinations to which send events"
      },
        "allowedValues": [
          "https://www.your-valid-destination-goes-here-1.com",
          "https://www.your-valid-destination-goes-here-2.com",
          "https://www.your-valid-destination-goes-here-3.com"
        ]
    }
  }
}
```

## <a name="define-an-azure-policy-with-a-list-of-allowed-azure-service-resource-destinations"></a>허용되는 Azure 서비스 리소스 대상 목록을 사용하여 Azure Policy 정의

다음 정책 정의는 사용자 지정 토픽에 대한 이벤트 구독에 구성된 특정 Event Hubs 대상을 제한합니다. 다른 유형의 [지원되는 Azure 서비스 대상](event-handlers.md)에 대해 비슷한 방법을 사용할 수 있습니다.

```json
{
  "mode": "All",
  "policyRule": {
    "if": {
      "not": {
        "field": "Microsoft.EventGrid/eventSubscriptions/destination.EventHub.resourceId",
        "in": "[parameters('allowedResourceDestinations')]"
      }
    },
    "then": {
      "effect": "deny"
    }
  },
  "parameters": {
    "allowedResourceDestinations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed event delivery destinations.",
        "displayName": "Allowed event delivery destinations"
      },
        "allowedValues": [
          "/subscriptions/<your-event-subscription>/resourceGroups/<your-resource-group>/providers/Microsoft.EventHub/namespaces/<event-hubs-namespace-name>/eventhubs/<your-event-hub-name>"
        ]
    }
  }
}
```

## <a name="next-steps"></a>다음 단계
- Azure Policy에 관한 자세한 내용은 다음 문서를 참조하세요. 
    - [Azure Policy란?](../governance/policy/overview.md)
    - [Azure Policy 정의 구조](../governance/policy/concepts/definition-structure.md).
    - [Azure Policy 효과 이해](../governance/policy/concepts/effects.md).
    - [Azure Policy의 범위 이해](../governance/policy/concepts/scope.md).
    - [Visual Studio Code용 Azure Policy 확장 사용](../governance/policy/how-to/extension-for-vscode.md).
    - [프로그래밍 방식으로 생성된 정책](../governance/policy/how-to/programmatically-create.md).
- Azure Event Grid에 대한 자세한 내용은 개념 섹션 아래에 있는 [Event Grid 용어](concepts.md)와 같은 문서를 참조하세요.