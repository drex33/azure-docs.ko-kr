---
title: Bicep을 사용하여 테넌트로 리소스 배포
description: 관리 그룹 범위에서 리소스를 배포하는 Bicep 파일을 만드는 방법을 설명합니다. 리소스 그룹을 만드는 방법을 보여주는 스크린샷
ms.topic: conceptual
ms.date: 11/22/2021
ms.openlocfilehash: ee0e3fcc323d002a52d6f5e30d0def77fc5ecda5
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133218637"
---
# <a name="subscription-deployments-with-bicep-files"></a>Bicep 파일을 사용한 구독 배포

이 문서에서는 리소스 그룹에 배포할 때 Bicep을 사용하여 범위를 설정하는 방법을 설명합니다.

리소스 관리를 간소화하기 위해 Azure 구독 수준에서 리소스를 배포할 수 있습니다. 예를 들어 구독에 [정책](../../governance/policy/overview.md)과 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 추가할 수 있으며 이렇게 하면 구독 전체에 적용됩니다. 구독 내에서 리소스 그룹을 만들고 구독의 리소스 그룹에 리소스를 배포할 수도 있습니다.

> [!NOTE]
> 구독 수준 배포에서 800개의 다른 리소스 그룹에 배포할 수 있습니다.

### <a name="microsoft-learn"></a>Microsoft Learn

배포 범위 및 실습 지침에 대해 자세히 알아보려면 **Microsoft Learn** 에서 [Bicep를 사용 하 여 구독, 관리 그룹 및 테 넌 트에 리소스 배포](/learn/modules/deploy-resources-scopes-bicep/) 를 참조 하세요.

## <a name="supported-resources"></a>지원되는 리소스

모든 리소스 종류를 구독 수준에 배포할 수 있는 것은 아닙니다. 이 섹션에서는 지원되는 리소스 종류를 나열합니다.

Azure Blueprints의 경우 다음을 사용합니다.

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [버전(Blueprints)](/azure/templates/microsoft.blueprint/blueprints/versions)

Azure 정책의 경우 다음을 사용합니다.

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

액세스 제어를 사용 하려면 다음을 사용 합니다.

* [accessReviewScheduleDefinitions](/azure/templates/microsoft.authorization/accessreviewscheduledefinitions)
* [accessReviewScheduleSettings](/azure/templates/microsoft.authorization/accessreviewschedulesettings)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleAssignmentScheduleRequests](/azure/templates/microsoft.authorization/roleassignmentschedulerequests)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)
* [roleEligibilityScheduleRequests](/azure/templates/microsoft.authorization/roleeligibilityschedulerequests)
* [roleManagementPolicyAssignments](/azure/templates/microsoft.authorization/rolemanagementpolicyassignments)

리소스 그룹에 배포하는 중첩된 템플릿의 경우 다음을 사용합니다.

* [배포](/azure/templates/microsoft.resources/deployments)

새 리소스 그룹을 만들려면 다음을 사용합니다.

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

구독을 관리하려면 다음을 사용합니다.

* [budgets](/azure/templates/microsoft.consumption/budgets)
* [구성-Advisor ](/azure/templates/microsoft.advisor/configurations)
* [lineOfCredit](/azure/templates/microsoft.billing/billingaccounts/lineofcredit)
* [중지](/azure/templates/microsoft.authorization/locks)
* [프로필 변경 분석 ](/azure/templates/microsoft.changeanalysis/profile)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [태그](/azure/templates/microsoft.resources/tags)

모니터링을 위해 다음을 사용 합니다.

* [diagnosticSettings](/azure/templates/microsoft.insights/diagnosticsettings)
* [logprofiles](/azure/templates/microsoft.insights/logprofiles)

보안을 위해 다음을 사용 합니다.

* [advancedThreatProtectionSettings](/azure/templates/microsoft.security/advancedthreatprotectionsettings)
* [alertsSuppressionRules](/azure/templates/microsoft.security/alertssuppressionrules)
* [assessmentMetadata](/azure/templates/microsoft.security/assessmentmetadata)
* [인사](/azure/templates/microsoft.security/assessments)
* [autoProvisioningSettings](/azure/templates/microsoft.security/autoprovisioningsettings)
* [커넥터용](/azure/templates/microsoft.security/connectors)
* [deviceSecurityGroups](/azure/templates/microsoft.security/devicesecuritygroups)
* [ingestionSettings](/azure/templates/microsoft.security/ingestionsettings)
* [pricings](/azure/templates/microsoft.security/pricings)
* [securityContacts](/azure/templates/microsoft.security/securitycontacts)
* [설정](/azure/templates/microsoft.security/settings)
* [workspaceSettings](/azure/templates/microsoft.security/workspacesettings)

지원되는 다른 형식은 다음과 같습니다.

* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

## <a name="set-scope"></a>집합 범위

범위를 구독으로 설정합니다.

```bicep
targetScope = 'subscription'
```

## <a name="deployment-commands"></a>배포 명령

구독에 배포하려면 구독 수준 배포 명령을 사용합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI의 경우 [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create)를 사용합니다. 다음 예제에서는 리소스 그룹을 만드는 템플릿을 배포합니다.

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-file main.bicep \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell 배포 명령의 경우 [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) 또는 별칭인 `New-AzSubscriptionDeployment`를 사용합니다. 다음 예제에서는 리소스 그룹을 만드는 템플릿을 배포합니다.

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateFile main.bicep `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

---

ARM 템플릿 배포를 위한 배포 명령 및 옵션에 대한 자세한 내용은 다음을 참조하세요.

* [ARM 템플릿 및 Azure CLI를 사용하여 리소스 배포](deploy-cli.md)
* [ARM 템플릿 및 Azure PowerShell을 사용하여 리소스 배포](deploy-powershell.md)
* [Cloud Shell에서 ARM 템플릿 배포](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>배포 위치 및 이름

구독 수준 배포의 경우 배포를 위한 위치를 제공해야 합니다. 배포의 위치는 배포하는 리소스의 위치와는 별개입니다. 배포 위치는 배포 데이터를 저장할 위치를 지정합니다. [관리 그룹](deploy-to-management-group.md) 및 [테넌트](deploy-to-tenant.md) 배포에도 위치가 필요합니다. [리소스 그룹](deploy-to-resource-group.md) 배포의 경우 배포 데이터를 저장하는 데 리소스 그룹의 위치가 사용됩니다.

배포 이름을 제공하거나 기본 배포 이름을 사용할 수 있습니다. 기본 이름은 템플릿 파일의 이름입니다. 예를 들어 _main.json_ 이라는 템플릿을 배포하면 **main** 이라는 기본 배포 이름이 생성됩니다.

각 배포 이름의 경우 위치는 변경할 수 없습니다. 다른 위치의 이름이 동일한 기존 배포가 있는 경우 하나의 위치에서 배포를 만들 수 없습니다. 예를 들어 **centralus** 에서 이름이 **deployment1** 인 구독 배포를 만들 경우 나중에 이름은 **deployment1** 이지만 위치는 **westus** 인 다른 배포를 만들 수 없습니다. 오류 코드 `InvalidDeploymentLocation`을 수신하게 되면 해당 이름의 이전 배포와 다른 이름이나 동일한 위치를 사용합니다.

## <a name="deployment-scopes"></a>배포 범위

구독에 배포할 경우 다음에 리소스를 배포할 수 있습니다.

* 작업의 대상 구독
* 테넌트의 모든 구독
* 구독 또는 다른 구독 내의 리소스 그룹
* 구독에 대한 테넌트

[확장 리소스](scope-extension-resources.md)의 범위는 배포 대상과 다른 대상으로 지정할 수 있습니다.

템플릿을 배포하는 사용자는 지정된 범위에 대한 액세스 권한이 있어야 합니다.

### <a name="scope-to-subscription"></a>구독으로 범위 지정

대상 구독에 리소스를 배포하려면 `resource` 키워드를 사용하여 해당 리소스를 추가합니다.

```bicep
targetScope = 'subscription'

// resource group created in target subscription
resource exampleResource 'Microsoft.Resources/resourceGroups@2020-10-01' = {
  ...
}
```

구독에 배포하는 예제는 [리소스 그룹 만들기](#create-resource-groups) 및 [정책 정의 할당](#assign-policy-definition)을 참조하세요.

작업의 구독과 다른 구독에 리소스를 배포하려면 [모듈](modules.md)을 추가합니다. [subscription 함수](bicep-functions-scope.md#subscription)를 사용하여 `scope` 속성을 설정합니다. 배포하려는 구독의 ID에 `subscriptionId` 속성을 제공합니다.

```bicep
targetScope = 'subscription'

param otherSubscriptionID string

// module deployed at subscription level but in a different subscription
module exampleModule 'module.bicep' = {
  name: 'deployToDifferentSub'
  scope: subscription(otherSubscriptionID)
}
```

### <a name="scope-to-resource-group"></a>리소스 그룹에 대한 범위

구독 내의 리소스 그룹에 리소스를 배포하려면 모듈을 추가하고 해당 `scope` 속성을 설정합니다. 리소스 그룹이 이미 있는 경우 [resourceGroup 함수](bicep-functions-scope.md#resourcegroup)를 사용하여 범위 값을 설정합니다. 리소스 그룹 이름을 입력하세요.

```bicep
targetScope = 'subscription'

param resourceGroupName string

module exampleModule 'module.bicep' = {
  name: 'exampleModule'
  scope: resourceGroup(resourceGroupName)
}
```

리소스 그룹이 동일한 Bicep 파일에 만들어지는 경우 리소스 그룹의 기호 이름을 사용하여 범위 값을 설정합니다. 범위를 기호 이름으로 설정하는 예는 [리소스 그룹 및 리소스 만들기](#create-resource-group-and-resources)를 참조하세요.

### <a name="scope-to-tenant"></a>테넌트로 범위 지정

테넌트에서 리소스를 만들려면 모듈을 추가합니다. [tenant 함수](bicep-functions-scope.md#tenant)를 사용하여 `scope` 속성을 설정합니다.

템플릿을 배포하는 사용자에게는 [테넌트에서 배포하는 데 필요한 액세스 권한이 있어야 합니다](deploy-to-tenant.md#required-access).

다음 예제에는 테넌트에 배포되는 모듈이 포함되어 있습니다.

```bicep
targetScope = 'subscription'

// module deployed at tenant level
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

모듈을 사용하는 대신 일부 리소스 종류에 대해 범위를 `tenant()`로 설정할 수 있습니다. 다음 예제에서는 테넌트에 관리 그룹을 배포합니다.

```bicep
targetScope = 'subscription'

param mgName string = 'mg-${uniqueString(newGuid())}'

// management group created at tenant
resource managementGroup 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {}
}

output output string = mgName
```

자세한 내용은 [관리 그룹](deploy-to-management-group.md#management-group)을 참조하십시오.

## <a name="resource-groups"></a>리소스 그룹

### <a name="create-resource-groups"></a>리소스 그룹 만들기

리소스 그룹을 만들려면 리소스 그룹의 이름과 위치를 사용하여 [Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) 리소스를 정의합니다.

다음 예제는 빈 리소스 그룹을 만듭니다.

```bicep
targetScope='subscription'

param resourceGroupName string
param resourceGroupLocation string

resource newRG 'Microsoft.Resources/resourceGroups@2021-01-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
}
```

### <a name="create-resource-group-and-resources"></a>리소스 그룹 및 리소스 만들기

리소스 그룹을 만들고 리소스를 배포하려면 모듈을 추가합니다. 모듈에는 리소스 그룹에 배포할 리소스가 포함됩니다. 모듈의 범위를 사용자가 만든 리소스 그룹의 기호화 된 이름으로 설정 합니다. 최대 800개의 리소스 그룹에 배포할 수 있습니다.

다음 예제는 리소스 그룹을 만들고 스토리지 계정을 배포합니다. 모듈의 `scope` 속성은 생성 중인 리소스 그룹의 기호 이름인 `newRG`로 설정되어 있습니다.

```bicep
targetScope='subscription'

param resourceGroupName string
param resourceGroupLocation string
param storageName string
param storageLocation string

resource newRG 'Microsoft.Resources/resourceGroups@2021-01-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
}

module storageAcct 'storage.bicep' = {
  name: 'storageModule'
  scope: newRG
  params: {
    storageLocation: storageLocation
    storageName: storageName
  }
}
```

이 모듈은 다음 콘텐츠가 포함된 **storage.bicep** 이라는 Bicep 파일을 사용합니다.

```bicep
param storageLocation string
param storageName string

resource storageAcct 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageName
  location: storageLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}
```

## <a name="azure-policy"></a>Azure Policy

### <a name="assign-policy-definition"></a>정책 정의 할당

다음 예제는 기존 정책 정의를 구독에 할당합니다. 정책 정의에서 매개 변수를 사용하는 경우 개체로 제공합니다. 정책 정의에서 매개 변수를 사용하지 않으면 기본 빈 개체를 사용합니다.

```bicep
targetScope = 'subscription'

param policyDefinitionID string
param policyName string
param policyParameters object = {}

resource policyAssign 'Microsoft.Authorization/policyAssignments@2020-09-01' = {
  name: policyName
  properties: {
    policyDefinitionId: policyDefinitionID
    parameters: policyParameters
  }
}
```

### <a name="create-and-assign-policy-definitions"></a>정책 정의 만들기 및 할당

동일한 Bicep 파일에서 정책 정의를 [정의](../../governance/policy/concepts/definition-structure.md)할 수 있습니다.

```bicep
targetScope = 'subscription'

resource locationPolicy 'Microsoft.Authorization/policyDefinitions@2020-09-01' = {
  name: 'locationpolicy'
  properties: {
    policyType: 'Custom'
    parameters: {}
    policyRule: {
      if: {
        field: 'location'
        equals: 'northeurope'
      }
      then: {
        effect: 'deny'
      }
    }
  }
}

resource locationRestrict 'Microsoft.Authorization/policyAssignments@2020-09-01' = {
  name: 'allowedLocation'
  properties: {
    policyDefinitionId: locationPolicy.id
  }
}
```

## <a name="access-control"></a>Access Control

역할 할당에 대해 자세히 알아보려면 [Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당 추가](../../role-based-access-control/role-assignments-template.md)를 참조하세요.

다음 예제에서는 리소스 그룹을 만들고 해당 그룹에 잠금을 적용하며 보안 주체에 역할을 할당합니다.

```bicep
targetScope = 'subscription'

@description('Name of the resourceGroup to create')
param resourceGroupName string

@description('Location for the resourceGroup')
param resourceGroupLocation string

@description('principalId of the user that will be given contributor access to the resourceGroup')
param principalId string

@description('roleDefinition to apply to the resourceGroup - default is contributor')
param roleDefinitionId string = 'b24988ac-6180-42a0-ab88-20f7382dd24c'

@description('Unique name for the roleAssignment in the format of a guid')
param roleAssignmentName string = guid(principalId, roleDefinitionId, resourceGroupName)

var roleID = '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/${roleDefinitionId}'

resource newResourceGroup 'Microsoft.Resources/resourceGroups@2019-10-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
  properties: {}
}

module applyLock 'lock.bicep' = {
  name: 'applyLock'
  scope: newResourceGroup
}

module assignRole 'role.bicep' = {
  name: 'assignRBACRole'
  scope: newResourceGroup
  params: {
    principalId: principalId
    roleNameGuid: roleAssignmentName
    roleDefinitionId: roleID
  }
}
```

다음 예는 잠금을 적용하는 모듈을 보여줍니다.

```bicep
resource createRgLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'rgLock'
  properties: {
    level: 'CanNotDelete'
    notes: 'Resource group should not be deleted.'
  }
}
```

다음 예는 역할을 할당하는 모듈을 보여줍니다.

```bicep
@description('The principal to assign the role to')
param principalId string

@description('A GUID used to identify the role assignment')
param roleNameGuid string = newGuid()

param roleDefinitionId string

resource roleNameGuid_resource 'Microsoft.Authorization/roleAssignments@2020-04-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: roleDefinitionId
    principalId: principalId
  }
}
```

## <a name="next-steps"></a>다음 단계

다른 범위에 대해 알아보려면 다음을 참조합니다.

* [리소스 그룹 배포](deploy-to-resource-group.md)
* [관리 그룹 배포](deploy-to-management-group.md)
* [테넌트 배포](deploy-to-tenant.md)
