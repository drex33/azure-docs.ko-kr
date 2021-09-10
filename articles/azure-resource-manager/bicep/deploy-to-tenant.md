---
title: Bicep을 사용하여 테넌트로 리소스 배포
description: Bicep 파일의 테넌트 범위에서 리소스를 배포하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: 3f446670cd85daa5b541cc9b5858ab768ac8411d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453412"
---
# <a name="tenant-deployments-with-bicep-file"></a>Bicep 파일을 통해 테넌트 배포

조직이 성숙되면 Azure AD 테넌트 전체에서 [정책](../../governance/policy/overview.md) 또는 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 정의하거나 할당해야 할 수 있습니다. 테넌트 수준 템플릿을 사용하면 선언적으로 정책을 적용하고 전역 수준에서 역할을 할당할 수 있습니다.

## <a name="supported-resources"></a>지원되는 리소스

모든 리소스 종류를 테넌트 수준에 배포할 수 있는 것은 아닙니다. 이 섹션에서는 지원되는 리소스 종류를 나열합니다.

Azure RBAC(Azure 역할 기반 액세스 제어)의 경우 다음을 사용합니다.

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

관리 그룹, 구독 또는 리소스 그룹에 배포하는 중첩된 템플릿의 경우 다음을 사용합니다.

* [deployments](/azure/templates/microsoft.resources/deployments)

관리 그룹을 만들려면 다음을 사용합니다.

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

구독을 만들려면 다음을 사용합니다.

* [aliases](/azure/templates/microsoft.subscription/aliases)

비용을 관리하려면 다음을 사용합니다.

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [instructions](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

포털을 구성하려면 다음을 사용합니다.

* [tenantConfigurations](/azure/templates/microsoft.portal/tenantconfigurations)

기본 제공 정책 정의는 테넌트 수준 리소스이지만 테넌트에서 사용자 지정 정책 정의를 배포할 수는 없습니다. 리소스에 기본 제공 정책 정의를 할당하는 예제는 [tenantResourceId 예제](./bicep-functions-resource.md#tenantresourceid)를 참조하세요.

## <a name="set-scope"></a>집합 범위

범위를 테넌트로 설정하려면 다음을 사용합니다.

```bicep
targetScope = 'tenant'
```

## <a name="required-access"></a>필요한 액세스

템플릿을 배포하는 주체는 테넌트 범위에서 리소스를 만들 수 있는 권한이 있어야 합니다. 주체는 배포 작업(`Microsoft.Resources/deployments/*`)을 실행하고 템플릿에 정의된 리소스를 만들 수 있는 권한이 있어야 합니다. 예를 들어 관리 그룹을 만들려면 주체는 테넌트 범위에서 기여자 권한이 있어야 합니다. 역할 할당을 만들려면 주체에 소유자 권한이 있어야 합니다.

Azure Active Directory의 전역 관리자에게 역할을 할당할 수 있는 권한이 자동으로 생기지는 않습니다. 테넌트 범위에서 템플릿 배포를 사용하도록 설정하려면 전역 관리자가 다음 단계를 수행해야 합니다.

1. 계정 액세스 권한을 승격하여 전역 관리자가 역할을 할당할 수 있도록 합니다. 자세한 내용은 [모든 Azure 구독 및 관리 그룹을 관리할 수 있도록 액세스 권한 상승](../../role-based-access-control/elevate-access-global-admin.md)을 참조하세요.

1. 템플릿을 배포해야 하는 주체에 소유자 또는 기여자를 할당합니다.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

이제 주체는 템플릿을 배포하는 데 필요한 권한이 있습니다.

## <a name="deployment-commands"></a>배포 명령

테넌트 배포에 대한 명령은 리소스 그룹 배포에 대한 명령과 다릅니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI의 경우 [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create)를 사용합니다.

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-file main.bicep
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell의 경우 [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment)를 사용합니다.

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateFile main.bicep
```

---

ARM 템플릿 배포를 위한 배포 명령 및 옵션에 대한 자세한 내용은 다음을 참조하세요.

* [ARM 템플릿 및 Azure CLI를 사용하여 리소스 배포](deploy-cli.md)
* [ARM 템플릿 및 Azure PowerShell을 사용하여 리소스 배포](deploy-powershell.md)
* [Cloud Shell에서 ARM 템플릿 배포](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>배포 위치 및 이름

테넌트 수준 배포의 경우 배포할 위치를 제공해야 합니다. 배포 위치는 배포하는 리소스의 위치와는 별개입니다. 배포 위치는 배포 데이터를 저장할 위치를 지정합니다. [구독](deploy-to-subscription.md) 및 [관리 그룹](deploy-to-management-group.md) 배포에도 위치가 필요합니다. [리소스 그룹](deploy-to-resource-group.md) 배포의 경우 리소스 그룹의 위치는 배포 데이터를 저장하는 데 사용됩니다.

배포 이름을 제공하거나 기본 배포 이름을 사용할 수 있습니다. 기본 이름은 템플릿 파일의 이름입니다. 예를 들어 _main.bicep_ 이라는 파일을 배포하면 **main** 이라는 기본 배포 이름이 만들어집니다.

각 배포 이름의 경우 위치는 변경할 수 없습니다. 다른 위치의 이름이 동일한 기존 배포가 있는 경우 하나의 위치에서 배포를 만들 수 없습니다. 예를 들어 **centralus** 에서 이름이 **deployment1** 인 테넌트 배포를 만들 경우 나중에 이름은 **deployment1** 이지만 위치는 **westus** 인 다른 배포를 만들 수 없습니다. 오류 코드 `InvalidDeploymentLocation`을 수신하게 되면 해당 이름의 이전 배포와 다른 이름이나 동일한 위치를 사용합니다.

## <a name="deployment-scopes"></a>배포 범위

테넌트에 배포하는 경우 리소스를 다음에 배포할 수 있습니다.

* 테넌트
* 테넌트 내의 관리 그룹
* 구독
* 리소스 그룹

[확장 리소스](scope-extension-resources.md)의 범위를 배포 대상과 다른 대상으로 지정할 수 있습니다.

템플릿을 배포하는 사용자는 지정된 범위에 대한 액세스 권한이 있어야 합니다.

이 섹션에서는 다양한 범위를 지정하는 방법을 보여 줍니다. 단일 템플릿에서 여러 범위를 결합할 수 있습니다.

### <a name="scope-to-tenant"></a>테넌트로 범위 지정

Bicep 파일 내에 정의된 리소스는 테넌트에 적용됩니다.

```bicep
targetScope = 'tenant'

// create resource at tenant
resource mgName_resource 'Microsoft.Management/managementGroups@2020-02-01' = {
  ...
}
```

### <a name="scope-to-management-group"></a>관리 그룹에 대한 범위

테넌트 내에서 관리 그룹을 대상으로 지정하려면 [모듈](modules.md)을 추가합니다. [managementGroup 함수](bicep-functions-scope.md#managementgroup)를 사용하여 해당 `scope` 속성을 설정합니다. 관리 그룹 이름을 입력합니다.

```bicep
targetScope = 'tenant'

param managementGroupName string

// create resources at management group level
module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupName)
}
```

### <a name="scope-to-subscription"></a>구독으로 범위 지정

테넌트 내에서 구독을 대상으로 지정하려면 모듈을 추가합니다. [Subscription 함수](bicep-functions-scope.md#subscription)를 사용하여 해당 `scope` 속성을 설정합니다. 구독 ID를 입력합니다.

```bicep
targetScope = 'tenant'

param subscriptionID string

// create resources at subscription level
module  'module.bicep' = {
  name: 'deployToSub'
  scope: subscription(subscriptionID)
}
```

### <a name="scope-to-resource-group"></a>리소스 그룹에 대한 범위

테넌트 내에서 리소스 그룹을 대상으로 지정하려면 모듈을 추가합니다. [resourceGroup 함수](bicep-functions-scope.md#resourcegroup)를 사용하여 해당 `scope` 속성을 설정합니다. 구독 ID 및 리소스 그룹 이름을 입력합니다.

```bicep
targetScope = 'tenant'

param resourceGroupName string
param subscriptionID string

// create resources at resource group level
module  'module.bicep' = {
  name: 'deployToRG'
  scope: resourceGroup(subscriptionID, resourceGroupName)
}
```

## <a name="create-management-group"></a>관리 그룹 만들기

다음 템플릿은 관리 그룹을 만듭니다.

```bicep
targetScope = 'tenant'
param mgName string = 'mg-${uniqueString(newGuid())}'

resource mgName_resource 'Microsoft.Management/managementGroups@2020-02-01' = {
  name: mgName
  properties: {}
}
```

계정이 테넌트에 배포할 수 있는 권한이 없는 경우에도 다른 범위에 배포하여 관리 그룹을 만들 수 있습니다. 자세한 내용은 [관리 그룹](deploy-to-management-group.md#management-group)을 참조하세요.

## <a name="assign-role"></a>역할 할당

다음 템플릿은 테넌트 범위에서 역할을 할당합니다.

```bicep
targetScope = 'tenant'

@description('principalId if the user that will be given contributor access to the resourceGroup')
param principalId string

@description('roleDefinition for the assignment - default is owner')
param roleDefinitionId string = '8e3af657-a8ff-443c-a75c-2fe8c4bcb635'

var roleAssignmentName = guid(principalId, roleDefinitionId)

resource roleAssignment 'Microsoft.Authorization/roleAssignments@2020-03-01-preview' = {
  name: roleAssignmentName
  properties: {
    roleDefinitionId: tenantResourceId('Microsoft.Authorization/roleDefinitions', roleDefinitionId)
    principalId: principalId
  }
}
```

## <a name="next-steps"></a>다음 단계

다른 범위에 대해 알아보려면 다음을 참조합니다.

* [리소스 그룹 배포](deploy-to-resource-group.md)
* [구독 배포](deploy-to-subscription.md)
* [관리 그룹 배포](deploy-to-management-group.md)
