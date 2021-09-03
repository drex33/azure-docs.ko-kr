---
title: 정책 예외 구조의 세부 정보
description: 이니셔티브 또는 정의 평가에서 리소스를 제외하기 위해 Azure Policy에서 사용하는 정책 예외 정의에 관해 설명합니다.
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 4c38f872a17869b957bce415204042582454dfd9
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122568011"
---
# <a name="azure-policy-exemption-structure"></a>Azure Policy 예외 구조

Azure Policy 예외(미리 보기) 기능을 사용하여 이니셔티브나 정의 평가에서 개별 리소스나 리소스 계층 구조를 _제외_ 합니다. _제외_ 된 리소스는 전체 규정 준수에 포함되지만, 평가하거나 임시 면제를 가질 수 없습니다. 자세한 내용은 [Azure Policy의 범위 이해](./scope.md)를 참조하세요. Azure Policy 예외는 [Resource Manager 모드](./definition-structure.md#resource-manager-modes)에서만 작동하며 [리소스 공급자 모드](./definition-structure.md#resource-provider-modes)에서는 작동하지 않습니다.

> [!IMPORTANT]
> 이 기능은 **미리 보기** 시에는 무료입니다. 가격 책정 관련 자세한 내용은 [Azure Policy 가격 책정](https://azure.microsoft.com/pricing/details/azure-policy/)을 참조하세요. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

JSON을 사용하여 정책 예외를 만듭니다. 정책 정의에는 다음 요소가 포함됩니다.

- 표시 이름
- description
- metadata
- 정책 할당
- 이니셔티브에서 정책 정의
- 예외 범주
- expiration

> [!NOTE]
> 정책 예외는 리소스 계층에서 자식 개체로 만들어지거나 개별 리소스에 예외가 부여되었으므로, 해당 대상이 예외 정의에 포함되지 않습니다.

예를 들어 다음 JSON은 `resourceShouldBeCompliantInit`라는 이니셔티브 할당에 대한 리소스의 **면제** 범주의 정책 예외를 보여 줍니다. 리소스는 이니셔티브의 두 정책 정의, `customOrgPolicy` 사용자 지정 정책 정의(참조 `requiredTags`), '허용된 위치' 기본 제공 정책 정의(ID: `e56962a6-4747-49cd-b67b-bf8b01975c4c`, 참조 _)에서만_ 제외`allowedLocations`됩니다.

```json
{
    "id": "/subscriptions/{subId}/resourceGroups/ExemptRG/providers/Microsoft.Authorization/policyExemptions/resourceIsNotApplicable",
    "name": "resourceIsNotApplicable",
    "type": "Microsoft.Authorization/policyExemptions",
    "properties": {
        "displayName": "This resource is scheduled for deletion",
        "description": "This resources is planned to be deleted by end of quarter and has been granted a waiver to the policy.",
        "metadata": {
            "requestedBy": "Storage team",
            "approvedBy": "IA",
            "approvedOn": "2020-07-26T08:02:32.0000000Z",
            "ticketRef": "4baf214c-8d54-4646-be3f-eb6ec7b9bc4f"
        },
        "policyAssignmentId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyAssignments/resourceShouldBeCompliantInit",
        "policyDefinitionReferenceIds": [
            "requiredTags",
            "allowedLocations"
        ],
        "exemptionCategory": "waiver",
        "expiresOn": "2020-12-31T23:59:00.0000000Z"
    }
}
```

정책 예외에 사용되는 `policyDefinitionReferenceIds` 일치 항목으로 관련 이니셔티브의 코드 조각입니다.

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/customOrgPolicy",
        "policyDefinitionReferenceId": "requiredTags",
        "parameters": {
            "reqTags": {
                "value": "[parameters('init_reqTags')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
        "policyDefinitionReferenceId": "allowedLocations",
        "parameters": {
            "listOfAllowedLocations": {
                "value": "[parameters('init_listOfAllowedLocations')]"
            }
        }
    }
]
```

## <a name="display-name-and-description"></a>표시 이름 및 설명

**displayName** 및 **description** 을 사용하여 정책 예외를 식별하고 특정 리소스와 함께 사용하기 위한 컨텍스트를 제공합니다. **displayName** 은 최대 길이가 _128_ 자이고 **description** 은 최대 길이가 _512_ 자입니다.

## <a name="metadata"></a>메타데이터

**메타데이터** 속성을 사용하면 관련 정보를 저장하는 데 필요한 자식 속성을 만들 수 있습니다. 위의 예제에서 **requestedBy**, **approvedBy**, **approvedOn**, **ticketRef** 속성은 예외 요청자, 승인자, 승인 시기, 요청에 대한 내부 추적 티켓 관련 정보를 제공하는 고객 값을 포함합니다. 해당 **메타데이터** 속성은 예제이지만 필요하지 않으며 **메타데이터** 는 자식 속성으로 제한되지 않습니다.

## <a name="policy-assignment-id"></a>정책 할당 ID

이 필드는 정책 할당 또는 이니셔티브 할당의 전체 경로 이름이어야 합니다.
`policyAssignmentId`는 문자열이며 배열이 아닙니다. 이 속성은 부모 리소스 계층 구조 또는 개별 리소스가 _제외_ 되는 할당을 정의합니다.

## <a name="policy-definition-ids"></a>정책 정의 ID

`policyAssignmentId`가 이니셔티브 할당용일 경우 `policyDefinitionReferenceIds` 속성을 사용하여 주체 리소스에 예외가 있는 이니셔티브의 정책 정의를 지정할 수 있습니다. 하나 이상 포함된 정책 정의에서 리소스가 제외될 수 있으므로 이 속성은 _배열_ 입니다. 값은 `policyDefinitions.policyDefinitionReferenceId` 필드에 있는 이니셔티브 정의의 값과 일치해야 합니다.

## <a name="exemption-category"></a>예외 범주

예외를 그룹화하는 데 사용되는 두 개의 예외 범주가 있습니다.

- **완화됨**: 정책 의도가 다른 메서드를 통해 충족되었으므로 예외가 부여됩니다.
- **면제**: 리소스의 비준수 상태가 일시적으로 수락되었으므로 예외가 부여됩니다. 이 범주를 사용하는 또 다른 이유는 이니셔티브에서 하나 이상의 정의에서 제외되어야 하지만 전체 이니셔티브에서 제외해서는 안 되는 리소스 또는 리소스 계층 구조를 위함입니다.

## <a name="expiration"></a>만료

리소스 계층 구조 또는 개별 리소스가 더 이상 할당에서 _제외_ 되지 않는 시기를 설정하려면 `expiresOn` 속성을 설정합니다. 이 선택적 속성은 유니버설 ISO 8601 날짜/시간 형식(`yyyy-MM-ddTHH:mm:ss.fffffffZ`)이어야 합니다.

> [!NOTE]
> `expiresOn` 날짜가 되면 정책 예외가 삭제되지 않습니다. 개체는 레코드 유지를 위해 유지되지만, 예외는 더 적용되지 않습니다.

## <a name="required-permissions"></a>필요한 사용 권한

정책 예외 개체를 관리하는 데 필요한 Azure RBAC 권한은 `Microsoft.Authorization/policyExemptions` 작업 그룹에 있습니다. 기본 제공 역할 [리소스 정책 기여자](../../../role-based-access-control/built-in-roles.md#resource-policy-contributor) 및 [보안 관리자](../../../role-based-access-control/built-in-roles.md#security-admin)는 모두 `read` 및 `write` 사용 권한을 가지며 [Policy Insights 데이터 쓰기 권한자(미리 보기)](../../../role-based-access-control/built-in-roles.md#policy-insights-data-writer-preview)에는 `read` 권한이 있습니다.

예외에는 예외를 부여할 때의 영향 때문에 추가 보안 조치가 있습니다. 리소스 계층 또는 개별 리소스에 대한 `Microsoft.Authorization/policyExemptions/write` 작업을 요구하는 것 이외에도 예외 작성자에게는 대상 할당 관련한 `exempt/Action` 동사가 있어야 합니다.

## <a name="next-steps"></a>다음 단계

- [정책 정의 구조](./definition-structure.md)에 대해 알아봅니다.
- [프로그래밍 방식으로 정책을 만드는](../how-to/programmatically-create.md) 방법을 이해합니다.
- [규정 준수 데이터를 가져오는](../how-to/get-compliance-data.md) 방법을 알아봅니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.