---
title: Bicep을 사용하여 관리 그룹에 리소스 배포
description: 관리 그룹 범위에서 리소스를 배포하는 Bicep 파일을 만드는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 11/22/2021
ms.openlocfilehash: 55840f743c0291e877f22e357d9894c712cf85af
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132935924"
---
# <a name="management-group-deployments-with-bicep-files"></a>Bicep 파일을 사용하여 관리 그룹 배포

이 문서에서는 관리 그룹에 배포할 때 Bicep을 사용하여 범위를 설정하는 방법을 설명합니다.

조직이 성장하면 Bicep 파일을 배포하여 관리 그룹 수준에서 리소스를 만들 수 있습니다. 예를 들어 관리 그룹에 대한 [정책](../../governance/policy/overview.md)을 정의하고 할당하거나 [Azure RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 할당해야 할 수 있습니다. 관리 그룹 수준 템플릿을 사용하면 선언적으로 정책을 적용하고 관리 그룹 수준에서 역할을 할당할 수 있습니다.

### <a name="microsoft-learn"></a>Microsoft Learn

배포 범위 및 실습 지침에 대한 자세한 내용은 **Microsoft Learn** [Bicep을 사용하여 구독, 관리 그룹 및 테넌트](/learn/modules/deploy-resources-scopes-bicep/) 리소스 배포를 참조하세요.

## <a name="supported-resources"></a>지원되는 리소스

모든 리소스 종류를 관리 그룹 수준에 배포할 수 있는 것은 아닙니다. 이 섹션에서는 지원되는 리소스 종류를 나열합니다.

Azure Blueprints의 경우 다음을 사용합니다.

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [버전](/azure/templates/microsoft.blueprint/blueprints/versions)

Azure Policy의 경우 다음을 사용합니다.

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

액세스 제어의 경우 다음을 사용합니다.

* [privateLinkAssociations](/azure/templates/microsoft.authorization/privatelinkassociations)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleAssignmentScheduleRequests](/azure/templates/microsoft.authorization/roleassignmentschedulerequests)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)
* [roleEligibilityScheduleRequests](/azure/templates/microsoft.authorization/roleeligibilityschedulerequests)
* [roleManagementPolicyAssignments](/azure/templates/microsoft.authorization/rolemanagementpolicyassignments)

구독 또는 리소스 그룹에 배포하는 중첩된 템플릿의 경우 다음을 사용합니다.

* [deployments](/azure/templates/microsoft.resources/deployments)

리소스를 관리하려면 다음을 사용합니다.

* [diagnosticSettings](/azure/templates/microsoft.insights/diagnosticsettings)
* [태그](/azure/templates/microsoft.resources/tags)

관리 그룹은 테넌트 수준 리소스입니다. 그러나 새 관리 그룹의 범위를 테넌트로 설정하여 관리 그룹 배포에서 관리 그룹을 만들 수 있습니다. [관리 그룹](#management-group)을 참조하세요.

## <a name="set-scope"></a>집합 범위

범위를 관리 그룹으로 설정하려면 다음을 사용합니다.

```bicep
targetScope = 'managementGroup'
```

## <a name="deployment-commands"></a>배포 명령

관리 그룹에 배포하려면 관리 그룹 배포 명령을 사용합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI의 경우 [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create)를 사용합니다.

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell의 경우 [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment)를 사용합니다.

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

---

ARM 템플릿 배포를 위한 배포 명령 및 옵션에 대한 자세한 내용은 다음을 참조하세요.

* [ARM 템플릿 및 Azure CLI를 사용하여 리소스 배포](deploy-cli.md)
* [ARM 템플릿 및 Azure PowerShell을 사용하여 리소스 배포](deploy-powershell.md)
* [Cloud Shell에서 ARM 템플릿 배포](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>배포 위치 및 이름

관리 그룹 수준 배포의 경우 배포할 위치를 제공해야 합니다. 배포 위치는 배포하는 리소스의 위치와는 별개입니다. 배포 위치는 배포 데이터를 저장할 위치를 지정합니다. [구독](deploy-to-subscription.md) 및 [테넌트](deploy-to-tenant.md) 배포에도 위치가 필요합니다. [리소스 그룹](deploy-to-resource-group.md) 배포의 경우 리소스 그룹의 위치는 배포 데이터를 저장하는 데 사용됩니다.

배포 이름을 제공하거나 기본 배포 이름을 사용할 수 있습니다. 기본 이름은 템플릿 파일의 이름입니다. 예를 들어 _main.bicep_ 이라는 템플릿을 배포하면 **main** 이라는 기본 배포 이름이 만들어집니다.

각 배포 이름의 경우 위치는 변경할 수 없습니다. 다른 위치의 이름이 동일한 기존 배포가 있는 경우 하나의 위치에서 배포를 만들 수 없습니다. 예를 들어 **centralus** 에서 이름이 **deployment1** 인 관리 그룹 배포를 만드는 경우 나중에 이름은 **deployment1** 이지만 위치는 **westus** 인 다른 배포를 만들 수 없습니다. 오류 코드 `InvalidDeploymentLocation`을 수신하게 되면 해당 이름의 이전 배포와 다른 이름이나 동일한 위치를 사용합니다.

## <a name="deployment-scopes"></a>배포 범위

관리 그룹에 배포하는 경우 다음에 리소스를 배포할 수 있습니다.

* 작업의 대상 관리 그룹
* 테넌트의 다른 관리 그룹
* 관리 그룹의 구독
* 관리 그룹의 리소스 그룹
* 리소스 그룹의 테넌트

[확장 리소스](scope-extension-resources.md)의 범위를 배포 대상과 다른 대상으로 지정할 수 있습니다.

템플릿을 배포하는 사용자는 지정된 범위에 대한 액세스 권한이 있어야 합니다.

### <a name="scope-to-management-group"></a>관리 그룹에 대한 범위

대상 관리 그룹에 리소스를 배포하려면 `resource` 키워드를 사용하여 해당 리소스를 추가합니다.

```bicep
targetScope = 'managementGroup'

// policy definition created in the management group
resource policyDefinition 'Microsoft.Authorization/policyDefinitions@2019-09-01' = {
  ...
}
```

다른 관리 그룹을 대상으로 하려면 [모듈](modules.md)을 추가합니다. [managementGroup 함수](bicep-functions-scope.md#managementgroup)를 사용하여 `scope` 속성을 설정합니다. 관리 그룹 이름을 제공합니다.

```bicep
targetScope = 'managementGroup'

param otherManagementGroupName string

// module deployed at management group level but in a different management group
module exampleModule 'module.bicep' = {
  name: 'deployToDifferentMG'
  scope: managementGroup(otherManagementGroupName)
}
```

### <a name="scope-to-subscription"></a>구독으로 범위 지정

관리 그룹 내에서 구독을 대상으로 지정할 수도 있습니다. 템플릿을 배포하는 사용자는 지정된 범위에 대한 액세스 권한이 있어야 합니다.

관리 그룹 내의 구독을 대상으로 하려면 모듈을 추가합니다. [subscription 함수](bicep-functions-scope.md#subscription)를 사용하여 `scope` 속성을 설정합니다. 구독 ID를 입력합니다.

```bicep
targetScope = 'managementGroup'

param subscriptionID string

// module deployed to subscription in the management group
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription(subscriptionID)
}
```

### <a name="scope-to-resource-group"></a>리소스 그룹에 대한 범위

관리 그룹 내에서 리소스 그룹을 대상으로 지정할 수도 있습니다. 템플릿을 배포하는 사용자는 지정된 범위에 대한 액세스 권한이 있어야 합니다.

관리 그룹 내에서 리소스 그룹을 대상으로 하려면 모듈을 추가합니다. [resourceGroup 함수](bicep-functions-scope.md#resourcegroup)를 사용하여 `scope` 속성을 설정합니다.  구독 ID 및 리소스 그룹 이름을 제공합니다.

```bicep
targetScope = 'managementGroup'

param subscriptionID string
param resourceGroupName string

// module deployed to resource group in the management group
module exampleModule 'module.bicep' = {
  name: 'deployToRG'
  scope: resourceGroup(subscriptionID, resourceGroupName)
}
```

### <a name="scope-to-tenant"></a>테넌트로 범위 지정

테넌트에서 리소스를 만들려면 모듈을 추가합니다. [tenant 함수](bicep-functions-scope.md#tenant)를 사용하여 `scope` 속성을 설정합니다. 템플릿을 배포하는 사용자에게는 [테넌트에서 배포하는 데 필요한 액세스 권한이 있어야 합니다](deploy-to-tenant.md#required-access).

```bicep
targetScope = 'managementGroup'

// module deployed at tenant level
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

또는 관리 그룹과 같은 일부 리소스 종류에 대해 범위를 `/`로 설정할 수 있습니다. 새 관리 그룹을 만드는 방법에 대해서는 다음 섹션에서 설명합니다.

## <a name="management-group"></a>관리 그룹

관리 그룹 배포에서 관리 그룹을 만들려면 범위를 테넌트로 설정해야 합니다.

다음 예에서는 루트 관리 그룹에 새 관리 그룹을 만듭니다.

```bicep
targetScope = 'managementGroup'

param mgName string = 'mg-${uniqueString(newGuid())}'

resource newMG 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {}
}

output newManagementGroup string = mgName
```

다음 예제에서는 배포를 대상으로 하는 관리 그룹에 새 관리 그룹을 만듭니다. [관리 그룹 함수](bicep-functions-scope.md#managementgroup)를 사용합니다.

```bicep
targetScope = 'managementGroup'

param mgName string = 'mg-${uniqueString(newGuid())}'

resource newMG 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {
    details: {
      parent: {
        id: managementGroup().id
      }
    }
  }
}

output newManagementGroup string = mgName
```

## <a name="subscriptions"></a>Subscriptions

ARM 템플릿을 사용하여 관리 그룹에서 새 Azure 구독을 만들려면 다음을 참조하세요.

* [프로그래밍 방식으로 Azure 기업계약 구독 만들기](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)
* [프로그래밍 방식으로 Microsoft 고객 계약에 대한 Azure 구독 만들기](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-customer-agreement.md)
* [프로그래밍 방식으로 Microsoft 파트너 계약에 대한 Azure 구독 만들기](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-partner-agreement.md)

기존 Azure 구독을 새 관리 그룹으로 이동하는 템플릿을 배포하려면 [ARM 템플릿에서 구독 이동](../../governance/management-groups/manage.md#move-subscriptions-in-arm-template)을 참조하세요.

## <a name="azure-policy"></a>Azure Policy

관리 그룹에 배포되는 사용자 지정 정책 정의는 관리 그룹의 확장입니다. 사용자 지정 정책 정의의 ID를 가져오려면 [Extensionresourceid()](./bicep-functions-resource.md#extensionresourceid) 함수를 사용합니다. 기본 제공 정책 정의는 테넌트 수준 리소스입니다. 기본 제공 정책 정의의 ID를 가져오려면 [Tenantresourceid()](./bicep-functions-resource.md#tenantresourceid) 함수를 사용합니다.

다음 예에서는 관리 그룹 수준에서 정책을 [정의](../../governance/policy/concepts/definition-structure.md)하고 할당하는 방법을 보여 줍니다.

```bicep
targetScope = 'managementGroup'

@description('An array of the allowed locations, all other locations will be denied by the created policy.')
param allowedLocations array = [
  'australiaeast'
  'australiasoutheast'
  'australiacentral'
]

resource policyDefinition 'Microsoft.Authorization/policyDefinitions@2020-09-01' = {
  name: 'locationRestriction'
  properties: {
    policyType: 'Custom'
    mode: 'All'
    parameters: {}
    policyRule: {
      if: {
        not: {
          field: 'location'
          in: allowedLocations
        }
      }
      then: {
        effect: 'deny'
      }
    }
  }
}

resource policyAssignment 'Microsoft.Authorization/policyAssignments@2020-09-01' = {
  name: 'locationAssignment'
  properties: {
    policyDefinitionId: policyDefinition.id
  }
}
```

## <a name="next-steps"></a>다음 단계

다른 범위에 대해 알아보려면 다음을 참조합니다.

* [리소스 그룹 배포](deploy-to-resource-group.md)
* [구독 배포](deploy-to-subscription.md)
* [테넌트 배포](deploy-to-tenant.md)
