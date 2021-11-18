---
title: Azure Policy를 사용 하 여 Azure 웹 PubSub 서비스 준수
description: Azure Policy에서 기본 제공 정책을 할당 하 여 Azure 웹 PubSub 서비스 리소스의 준수를 감사 합니다.
author: JialinXin
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 10/25/2021
ms.author: jixin
ms.openlocfilehash: c2cc6b0a3b16d69a4ddeb94a8775ad5abc3afec8
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714811"
---
# <a name="audit-compliance-of-azure-web-pubsub-service-resources-using-azure-policy"></a>Azure Policy를 사용 하 여 Azure 웹 PubSub 서비스 리소스의 준수 감사

[Azure Policy](../governance/policy/overview.md)는 정책을 만들고 할당하고 관리하는 데 사용하는 Azure 서비스입니다. 정책은 리소스에 대해 다양한 규칙과 효과를 적용하여 리소스가 회사 표준 및 서비스 수준 약정을 준수하도록 유지합니다.

이 문서에서는 Azure 웹 PubSub 서비스에 대 한 기본 제공 정책을 소개 합니다. 이러한 정책을 사용 하 여 새 및 기존 웹 PubSub 리소스의 준수를 감사 합니다.

Azure Policy 사용 요금은 없습니다.

## <a name="built-in-policy-definitions"></a>기본 제공 정책 정의

다음 기본 제공 정책 정의는 Azure 웹 PubSub 서비스에만 적용 됩니다.

[!INCLUDE [azure-policy-reference-policies-web-pubsub](../../includes/policy/reference/bycat/policies-web-pubsub.md)]

## <a name="assign-policy-definitions"></a>정책 정의 할당

* [Azure Portal](../governance/policy/assign-policy-portal.md), [Azure CLI](../governance/policy/assign-policy-azurecli.md), [Resource Manager 템플릿](../governance/policy/assign-policy-template.md) 또는 Azure Policy SDK를 사용하여 정책 정의를 할당합니다.
* 정책 할당 범위를 리소스 그룹, 구독 또는 [Azure 관리 그룹](../governance/management-groups/overview.md)으로 지정합니다. 웹 PubSub 정책 할당은 범위 내의 기존 및 새 웹 PubSub 리소스에 적용 됩니다.
* 언제든지 [정책 시행](../governance/policy/concepts/assignment-structure.md#enforcement-mode)을 사용하거나 사용하지 않도록 설정할 수 있습니다.

> [!NOTE]
> 정책을 할당하거나 업데이트한 후 정의된 범위의 리소스에 할당이 적용되는 데 약간의 시간이 걸립니다. [정책 평가 트리거](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)에 대한 정보를 참조하세요.

## <a name="review-policy-compliance"></a>정책 준수 검토

Azure Portal, Azure 명령줄 도구 또는 Azure Policy SDK를 사용하여 정책 할당에서 생성된 준수 정보에 액세스합니다. 자세한 내용은 [Azure 리소스의 준수 데이터 가져오기](../governance/policy/how-to/get-compliance-data.md)를 참조하세요.

리소스가 규정 비준수인 경우 여러 가지 원인이 있을 수 있습니다. 이유를 확인하거나 원인이 된 변경을 찾으려면 [비준수 확인](../governance/policy/how-to/determine-non-compliance.md)을 참조하세요.

### <a name="policy-compliance-in-the-portal"></a>포털의 정책 준수:

1. **모든 서비스** 를 선택하고 **정책** 을 검색합니다.
1. **준수** 를 선택합니다.
1. 필터를 사용하여 준수 상태를 제한하거나 정책 검색
   
    [ ![포털의 정책 준수](./media/howto-monitor-azure-policy/azure-policy-compliance.png) ](./media/howto-monitor-azure-policy/azure-policy-compliance.png#lightbox)
2. 정책을 선택하여 준수 세부 정보 및 이벤트 집계를 검토합니다. 필요한 경우 리소스 준수를 위해 특정 웹 PubSub를 선택 합니다.

### <a name="policy-compliance-in-the-azure-cli"></a>Azure CLI에서 정책 준수

Azure CLI를 사용하여 준수 데이터를 가져올 수도 있습니다. 예를 들어 CLI에서 [az policy 할당 list](/cli/azure/policy/assignment#az_policy_assignment_list) 명령을 사용 하 여 적용 되는 Azure 웹 Pubsub 서비스 정책의 정책 id를 가져옵니다.

```azurecli
az policy assignment list --query "[?contains(displayName,'Web PubSub')].{name:displayName, ID:id}" --output table
```

샘플 출력:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Azure Web PubSub Service should use private links  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/policyAssignments/<assignmentId>
```

그런 다음 [az policy state list](/cli/azure/policy/state#az_policy_state_list)를 실행하여 특정 리소스 그룹의 모든 리소스에 대해 JSON 형식의 준수 상태를 반환합니다.

```azurecli
az policy state list --g <resourceGroup>
```

또는 특정 웹 PubSub 리소스의 JSON 형식 준수 상태를 반환 하려면 [az policy state list](/cli/azure/policy/state#az_policy_state_list) 를 실행 합니다.

```azurecli
az policy state list \
 --resource /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.SignalRService/WebPubSub/<resourceName> \
 --namespace Microsoft.SignalRService \
 --resource-group <resourceGroup>
```

## <a name="next-steps"></a>다음 단계

* Azure Policy [정의](../governance/policy/concepts/definition-structure.md) 및 [효과](../governance/policy/concepts/effects.md)에 대해 자세히 알아봅니다.

* [사용자 지정 정책 정의](../governance/policy/tutorials/create-custom-policy-definition.md)를 만듭니다.

* Azure의 [거버넌스 기능](../governance/index.yml)에 대해 자세히 알아봅니다.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/