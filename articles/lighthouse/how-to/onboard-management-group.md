---
title: 관리 그룹의 모든 구독 온보딩
description: Azure Policy를 배포하여 관리 그룹 내의 모든 구독을 Azure Lighthouse 관리 테넌트에 위임할 수 있습니다.
ms.date: 08/13/2021
ms.topic: how-to
ms.openlocfilehash: 07f2bb850120b1f27f090aec9d0d3f97d9643d05
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122530054"
---
# <a name="onboard-all-subscriptions-in-a-management-group"></a>관리 그룹의 모든 구독 온보딩

[Azure Lighthouse](../overview.md)는 구독 및/또는 리소스 그룹의 위임을 허용하지만 [관리 그룹](../../governance/management-groups/overview.md)은 위임할 수 없습니다. 그러나 [Azure 정책](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups)을 배포하여 관리 그룹 내의 모든 구독을 Azure Lighthouse 관리 테넌트에 위임할 수 있습니다.

이 정책은 [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) 효과를 사용하여 관리 그룹 내의 각 구독이 지정된 관리 테넌트에게 위임되었는지 확인합니다. 구독이 아직 위임되지 않은 경우 정책은 매개 변수에 제공한 값을 기반으로 Azure Lighthouse 할당을 만듭니다. 그러면 마치 각각이 수동으로 온보딩된 것처럼 관리 그룹의 모든 구독에 액세스할 수 있습니다.

이 정책을 사용할 때 다음 사항에 유의합니다.

- 관리 그룹 내의 각 구독에는 동일한 권한 부여 집합이 있습니다. 액세스 권한이 부여된 사용자 및 역할을 변경하려면 구독을 수동으로 온보딩해야 합니다.
- 관리 그룹의 모든 구독이 온보딩되지만 Azure Lighthouse를 통해 관리 그룹 리소스에 대한 작업을 수행할 수 없습니다. 개별적으로 온보딩된 경우와 마찬가지로 작업할 구독을 선택해야 합니다.

아래에 지정되지 않는 한 이러한 모든 단계는 적절한 권한이 있는 고객 테넌트의 사용자가 수행해야 합니다.

> [!TIP]
> 이 항목에서는 서비스 공급자 및 고객만 언급하지만 [여러 테넌트를 관리하는 기업](../concepts/enterprise.md)에서도 동일한 프로세스를 사용할 수 있습니다.

## <a name="register-the-resource-provider-across-subscriptions"></a>구독 전반에 걸쳐 리소스 공급자 등록

일반적으로 **Microsoft.ManagedServices** 리소스 공급자는 온보딩 프로세스의 일부로 구독에 등록됩니다. 정책을 사용하여 관리 그룹에서 구독을 온보딩하는 경우 리소스 공급자를 미리 등록해야 합니다. 이는 고객 테넌트의 기여자 또는 소유자 사용자(또는 리소스 공급자에 대해  `/register/action` 작업을 수행할 권한이 있는 모든 사용자)가 수행할 수 있습니다. 자세한 내용은 [Azure 리소스 공급자 및 유형](../../azure-resource-manager/management/resource-providers-and-types.md)을 참조하세요.

[Azure Logic Apps를 사용하여 구독 간에 리소스 공급자를 자동으로 등록](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/register-managed-services-rp-customer)할 수 있습니다. 이 논리 앱은 관리 그룹 내의 각 구독에 리소스 공급자를 등록할 수 있는 제한된 권한으로 고객의 테넌트에 배포할 수 있습니다.

또한 [서비스 공급자의 테넌트에 배포할 수 있는 Azure Logic Apps](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/register-managed-services-rp-partner)도 제공합니다. 이 논리 앱은 논리 앱에 [테넌트 전체 관리자 동의를 부여](../../active-directory/manage-apps/grant-admin-consent.md)하여 여러 테넌트의 구독 간에 리소스 공급자를 할당할 수 있습니다. 테넌트 전체 관리자 동의를 부여하려면 조직을 대신하여 동의할 수 있는 사용자로 로그인해야 합니다. 이 옵션을 사용하여 여러 테넌트에 공급자를 등록하더라도 정책은 여전히 각 관리 그룹에 대해 개별적으로 배포되어야 합니다.

## <a name="create-your-parameters-file"></a>매개 변수 파일 만들기

정책을 할당하려면 샘플 저장소에서 [deployLighthouseIfNotExistManagementGroup.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-delegate-management-groups/deployLighthouseIfNotExistManagementGroup.json) 파일을 배포하고 특정 테넌트 및 할당 세부 정보로 편집하는 [deployLighthouseIfNotExistsManagementGroup.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-delegate-management-groups/deployLighthouseIfNotExistsManagementGroup.parameters.json) 매개 변수 파일을 배포합니다. 이 두 파일에는 [개별 구독 온보딩](onboard-customer.md)에 사용되는 것과 동일한 세부 정보가 포함되어 있습니다.

아래 예는 구독을 Relecloud 관리되는 서비스 테넌트에 위임하는 매개 변수 파일을 보여 줍니다. 이 때 두 principalID에 대한 액세스 권한이 부여되는데, 하나는 Tier 1 지원을 위한 것이며, 다른 하나는 [고객 테넌트의 관리 ID에 delegateRoleDefinitionIds를 할당](deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)할 수 있는 자동화 계정입니다.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": { 
        "managedByName": { 
            "value": "Relecloud Managed Services" 
        }, 
        "managedByDescription": { 
            "value": "Relecloud provides managed services to its customers" 
        }, 
        "managedByTenantId": { 
            "value": "00000000-0000-0000-0000-000000000000" 
        }, 
        "managedByAuthorizations": { 
            "value": [ 
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000", 
                    "principalIdDisplayName": "Tier 1 Support", 
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c" 
                }, 
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000", 
                    "principalIdDisplayName": "Automation Account - Full access", 
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9", 
                    "delegatedRoleDefinitionIds": [ 
                        "b24988ac-6180-42a0-ab88-20f7382dd24c", 
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293", 
                        "91c1777a-f3dc-4fae-b103-61d183457e46" 
                    ] 
                }                 
            ] 
        } 
    } 
} 
```

## <a name="assign-the-policy-to-a-management-group"></a>관리 그룹에 정책 할당  

할당을 만들도록 정책을 편집한 후에는 관리 그룹 수준에서 할당할 수 있습니다. 정책을 할당하고 준수 상태 결과를 확인하는 방법에 대한 자세한 내용은  [빠른 시작: 정책 할당 만들기](../../governance/policy/assign-policy-portal.md)를 참조하세요.

아래 PowerShell 스크립트는 만든 템플릿 및 매개 변수 파일을 사용하여 지정된 관리 그룹 아래에 정책 정의를 추가하는 방법을 보여 줍니다. 기존 구독에 대한 할당 및 수정 작업을 만들어야 합니다.

```azurepowershell-interactive
New-AzManagementGroupDeployment -Name <DeploymentName> -Location <location> -ManagementGroupId <ManagementGroupName> -TemplateFile <path to file> -TemplateParameterFile <path to parameter file> -verbose
```

## <a name="confirm-successful-onboarding"></a>온보딩 성공 확인

다양한 방법으로 구독이 성공적으로 온보딩되었는지 확인할 수 있습니다. 자세한 내용은 [성공적인 온보딩 확인](onboard-customer.md#confirm-successful-onboarding)을 참조하세요.

관리 그룹에 대해 논리 앱 및 정책을 활성 상태로 유지하면 관리 그룹에 추가된 모든 새 구독도 온보딩됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Lighthouse에 고객 온보딩](onboard-customer.md)에 대해 자세히 알아봅니다.
- [Azure Policy](../../governance/policy/index.yml)에 대해 알아봅니다.
- [Azure Logic Apps](../../logic-apps/logic-apps-overview.md)에 대해 알아봅니다.
