---
title: 수정할 수 있는 정책 배포
description: Azure Lighthouse를 통해 수정 작업을 사용하는 정책을 배포하려면 고객 테넌트에서 관리 ID를 만들어야 합니다.
ms.date: 09/13/2021
ms.topic: how-to
ms.openlocfilehash: 2270644a2d3e841a40046743bd6092a3ba44105d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128611456"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>위임된 구독 내에서 수정할 수 있는 정책 배포

[Azure Lighthouse](../overview.md)를 사용하면 서비스 공급자가 위임된 구독 내에서 정책 정의를 만들고 편집할 수 있습니다. 하지만 [수정 작업](../../governance/policy/how-to/remediate-resources.md)(즉, [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) 또는 [modify](../../governance/policy/concepts/effects.md#modify) 효과를 사용하는 정책)을 사용하는 정책을 배포하려면 고객 테넌트에서 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 만들어야 합니다. Azure Policy에서 이 관리 ID를 사용하여 정책 내에서 템플릿을 배포할 수 있습니다. Azure Lighthouse에 고객을 등록하고 정책 자체를 배포할 때 이 시나리오를 활성화하는 데 필요한 단계가 있습니다.

> [!TIP]
> 이 항목에서는 서비스 공급자 및 고객만 언급하지만 [여러 테넌트를 관리하는 기업](../concepts/enterprise.md)에서도 동일한 프로세스를 사용할 수 있습니다.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>고객 테넌트에서 관리 ID에 역할을 할당할 수 있는 사용자 만들기

Azure Lighthouse에 고객을 등록할 때 매개 변수 파일과 함께 [Azure Resource Manager 템플릿](onboard-customer.md#create-an-azure-resource-manager-template)을 사용하여 고객 테넌트에서 위임된 리소스에 액세스할 수 있는 권한 부여를 정의할 수 있습니다. 각 권한 부여는 관리 테넌트에서 Azure AD 사용자, 그룹 또는 서비스 주체에 해당하는 **principalId** 및 권한을 받는 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)에 해당하는 **roleDefinitionId** 를 지정합니다.

**principalId** 가 고객 테넌트에서 관리 ID를 만들도록 허용하려면 해당 **roleDefinitionId** 를 **사용자 액세스 관리자** 로 설정해야 합니다. 이 역할은 일반적으로 지원되지 않지만 이 특정 시나리오에서 사용할 수 있습니다. 이 권한이 있는 사용자 계정은 관리 ID에 특정 기본 제공 역할을 하나 이상 할당할 수 있습니다. 이러한 역할은 **delegatedRoleDefinitionIds** 속성에서 정의되며 사용자 액세스 관리자 또는 소유자를 제외한 모든 [지원되는 Azure 기본 제공 역할](../concepts/tenants-users-roles.md#role-support-for-azure-lighthouse)을 포함할 수 있습니다.

고객이 등록된 후 이 권한 부여에서 만든 **principalId** 는 이러한 기본 제공 역할을 고객 테넌트의 관리 ID에 할당할 수 있습니다. 그러나 일반적으로 사용자 액세스 관리자 역할에 연결된 다른 사용 권한이 없습니다.

아래 예제에서는 사용자 액세스 관리자 역할을 보유하는 **principalId** 를 보여 줍니다. 이 사용자는 고객 테넌트에서 관리 ID에 두 개의 기본 제공 역할(참가자 및 Log Analytics 참가자)을 할당할 수 있습니다.

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>수정할 수 있는 정책 배포

위의 설명대로 필수 권한을 사용하여 사용자를 만든 후에는 해당 사용자가 위임된 고객 구독 내에서 수정 작업을 사용하는 정책을 배포할 수 있습니다.

예를 들어, 이 [샘플](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring)에 설명된 대로 고객 테넌트에서 Azure Key Vault 리소스에서 진단을 사용하도록 설정하려고 한다고 가정합니다. 위에서 설명한 것처럼 적절한 권한이 있는 관리 테넌트의 사용자는 이 시나리오를 활성화하기 위해 [Azure Resource Manager 템플릿](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json)을 배포합니다.

위임된 구독과 함께 사용할 정책 할당을 만드는 작업은 현재 Azure Portal이 아닌 API를 통해 수행해야 합니다. 이렇게 하려면 **apiVersion** 을 **2019-04-01-preview** 로 설정해야 합니다. 이는 새 **delegatedManagedIdentityResourceId** 속성을 포함합니다. 이 속성을 사용하면 고객 테넌트(Azure Lighthouse에 등록된 구독 또는 리소스 그룹)에 있는 관리 ID를 포함할 수 있습니다.

다음 예제에서는 **delegatedManagedIdentityResourceId** 를 사용하는 역할 할당을 보여 줍니다.

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> [유사한 샘플](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag)을 사용하여 위임된 구독에 태그(수정 효과 사용)를 추가하거나 제거하는 정책을 배포하는 방법을 보여 줄 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Policy](../../governance/policy/index.yml)에 대해 알아봅니다.
- [Azure 리소스에 대한 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)에 대해 알아봅니다.
