---
title: Bicep을 사용하여 리소스 그룹에 리소스 배포
description: Bicep 파일에 리소스를 배포하는 방법을 설명합니다. 둘 이상의 리소스 그룹을 대상으로 지정하는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 57e6814a8d7acd5c35995e037c2fd0c1c6804819
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426943"
---
# <a name="resource-group-deployments-with-bicep-files"></a>Bicep 파일을 사용하여 리소스 그룹 배포

이 문서에서는 리소스 그룹에 배포할 때 Bicep을 사용하여 범위를 설정하는 방법을 설명합니다.

## <a name="supported-resources"></a>지원되는 리소스

대부분의 리소스는 리소스 그룹에 배포가 가능합니다. 사용 가능한 리소스 목록은 [ARM 템플릿 참조](/azure/templates)를 참조하십시오.

## <a name="set-scope"></a>집합 범위

기본적으로 Bicep 파일의 범위는 리소스 그룹입니다. 범위를 명시적으로 설정하려면 다음을 사용합니다.

```bicep
targetScope = 'resourceGroup'
```

그러나 대상 범위를 리소스 그룹으로 설정하는 것은 필요하지 않습니다. 기본적으로 해당 범위가 사용되기 때문입니다.

## <a name="deployment-commands"></a>배포 명령

리소스 그룹에 배포하려면 리소스 그룹 배포 명령을 사용합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI의 경우 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create)를 사용합니다. 다음 예제에서는 리소스 그룹을 만드는 템플릿을 배포합니다.

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-file main.bicep \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell 배포 명령의 경우 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)를 사용합니다. 다음 예제에서는 리소스 그룹을 만드는 템플릿을 배포합니다.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile main.bicep `
  -storageAccountType Standard_GRS `
```

---

ARM 템플릿 배포를 위한 배포 명령 및 옵션에 대한 자세한 내용은 다음을 참조하세요.

* [ARM 템플릿 및 Azure CLI를 사용하여 리소스 배포](deploy-cli.md)
* [ARM 템플릿 및 Azure PowerShell을 사용하여 리소스 배포](deploy-powershell.md)
* [Cloud Shell에서 ARM 템플릿 배포](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>배포 범위

리소스 그룹에 배포하는 경우 리소스를 배포할 수 있는 대상은 다음과 같습니다.

* 배포 작업의 대상 리소스 그룹
* 동일한 구독 또는 다른 구독에 있는 다른 리소스 그룹
* 테넌트의 모든 구독
* 리소스 그룹의 테넌트

[확장 리소스](scope-extension-resources.md)의 범위를 배포 대상과 다른 대상으로 지정할 수 있습니다.

템플릿을 배포하는 사용자는 지정된 범위에 대한 액세스 권한이 있어야 합니다.

이 섹션에서는 다양한 범위를 지정하는 방법을 보여 줍니다. 단일 템플릿에서 여러 범위를 결합할 수 있습니다.

### <a name="scope-to-target-resource-group"></a>대상 리소스 그룹으로 범위 지정

대상 리소스 그룹에 리소스를 배포하려면 해당 리소스를 Bicep 파일에 추가합니다.

```bicep
// resource deployed to target resource group
resource exampleResource 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

예제 템플릿은 [대상 리소스 그룹에 배포](#deploy-to-target-resource-group)를 참조하십시오.

### <a name="scope-to-different-resource-group"></a>다른 리소스 그룹으로 범위 지정

대상 리소스 그룹이 아닌 리소스 그룹에 리소스를 배포하려면 [모듈](modules.md)을 추가합니다. [resourceGroup 함수](bicep-functions-scope.md#resourcegroup)를 사용하여 해당 모듈에 대한 `scope` 속성을 설정합니다.

리소스 그룹이 다른 구독에 있는 경우 구독 ID 및 리소스 그룹의 이름을 제공합니다. 리소스 그룹이 현재 배포와 동일한 구독에 있는 경우 리소스 그룹의 이름만 제공합니다. [resourceGroup 함수](bicep-functions-scope.md#resourcegroup)에서 구독을 지정하지 않으면 현재 구독이 사용됩니다.

다음 예제에서는 다른 구독에 있는 리소스 그룹을 대상으로 하는 모듈을 보여 줍니다.

```bicep
param otherResourceGroup string
param otherSubscriptionID string

// module deployed to different subscription and resource group
module exampleModule 'module.bicep' = {
  name: 'otherSubAndRG'
  scope: resourceGroup(otherSubscriptionID, otherResourceGroup)
}
```

다음 예제에서는 동일한 구독의 리소스 그룹을 대상으로 하는 모듈을 보여 줍니다.

```bicep
param otherResourceGroup string

// module deployed to resource group in the same subscription
module exampleModule 'module.bicep' = {
  name: 'otherRG'
  scope: resourceGroup(otherResourceGroup)
}
```

예제 템플릿은 [여러 리소스 그룹에 배포](#deploy-to-multiple-resource-groups)를 참조하십시오.

### <a name="scope-to-subscription"></a>구독으로 범위 지정

구독에 리소스 그룹을 배포하려면 모듈을 추가합니다. [subscription 함수](bicep-functions-scope.md#subscription)를 사용하여 해당 `scope` 속성을 설정합니다.

현재 구독에 배포하려면 매개 변수 없이 subscription 함수를 사용합니다.

```bicep

// module deployed at subscription level
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription()
}
```

다른 구독에 배포하려면 subscription 함수에서 해당 구독 ID를 매개 변수로 지정합니다.

```bicep
param otherSubscriptionID string

// module deployed at subscription level but in a different subscription
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription(otherSubscriptionID)
}
```

예제 템플릿은 [리소스 그룹 만들기](#create-resource-group)를 참조하십시오.

### <a name="scope-to-tenant"></a>테넌트로 범위 지정

테넌트에서 리소스를 만들려면 모듈을 추가합니다. [tenant 함수](bicep-functions-scope.md#tenant)를 사용하여 `scope` 속성을 설정합니다.

템플릿을 배포하는 사용자에게는 [테넌트에서 배포하는 데 필요한 액세스 권한이 있어야 합니다](deploy-to-tenant.md#required-access).

다음 예제에는 테넌트에 배포되는 모듈이 포함되어 있습니다.

```bicep
// module deployed at tenant level
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

모듈을 사용하는 대신 일부 리소스 종류에 대해 범위를 `tenant()`로 설정할 수 있습니다. 다음 예제에서는 테넌트에 관리 그룹을 배포합니다.

```bicep
param mgName string = 'mg-${uniqueString(newGuid())}'

// ManagementGroup deployed at tenant
resource managementGroup 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {}
}

output output string = mgName
```

자세한 내용은 [관리 그룹](deploy-to-management-group.md#management-group)을 참조하십시오.

## <a name="deploy-to-target-resource-group"></a>대상 리소스 그룹으로 배포

대상 리소스 그룹에 리소스를 배포하려면 템플릿의 `resources` 섹션에서 해당 리소스를 정의합니다. 다음 템플릿은 배포 작업에 지정된 리소스 그룹에 스토리지 계정을 만듭니다.

:::code language="bicep" source="~/azure-docs-bicep-samples/samples/create-storage-account/azuredeploy.bicep":::

## <a name="deploy-to-multiple-resource-groups"></a>여러 리소스 그룹으로 배포

단일 Bicep 파일에서 둘 이상의 리소스 그룹에 배포할 수 있습니다.

> [!NOTE]
> 단일 배포의 **800개 리소스 그룹** 에만 배포할 수 있습니다. 일반적으로 이 제한 사항으로 인해 부모 템플릿에 지정된 하나의 리소스 그룹 및 중첩되거나 연결된 배포에서 최대 799개의 리소스 그룹에 배포할 수 있습니다. 그러나 부모 템플릿에 중첩되거나 연결된 템플릿만이 포함되고 자체적으로 리소스를 배포하지 않는 경우 중첩되거나 연결된 배포에서 최대 800개의 리소스 그룹이 포함될 수 있습니다.

다음 예제에서는 두 개의 스토리지 계정을 만듭니다. 첫 번째 스토리지 계정은 배포 작업에 지정된 리소스 그룹에 배포됩니다. 두 번째 스토리지 계정은 `secondResourceGroup` 및 `secondSubscriptionID` 매개 변수에 지정된 리소스 그룹에 배포됩니다.

```bicep
@maxLength(11)
param storagePrefix string

param firstStorageLocation string = resourceGroup().location

param secondResourceGroup string
param secondSubscriptionID string = ''
param secondStorageLocation string

var firstStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
var secondStorageName = '${storagePrefix}${uniqueString(secondSubscriptionID, secondResourceGroup)}'

module firstStorageAcct 'storage.bicep' = {
  name: 'storageModule1'
  params: {
    storageLocation: firstStorageLocation
    storageName: firstStorageName
  }
}

module secondStorageAcct 'storage.bicep' = {
  name: 'storageModule2'
  scope: resourceGroup(secondSubscriptionID, secondResourceGroup)
  params: {
    storageLocation: secondStorageLocation
    storageName: secondStorageName
  }
}
```

두 모듈은 **storage.bicep** 이라는 동일한 Bicep 파일을 사용합니다.

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

## <a name="create-resource-group"></a>리소스 그룹 만들기

리소스 그룹 배포에서 구독의 수준으로 전환하고 리소스 그룹을 만들 수 있습니다. 다음 템플릿은 스토리지 계정을 대상 리소스 그룹에 배포하고 지정된 구독에 새 리소스 그룹을 만듭니다.

```bicep
@maxLength(11)
param storagePrefix string

param firstStorageLocation string = resourceGroup().location

param secondResourceGroup string
param secondSubscriptionID string = ''
param secondLocation string

var firstStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

// resource deployed to target resource group
module firstStorageAcct 'storage2.bicep' = {
  name: 'storageModule1'
  params: {
    storageLocation: firstStorageLocation
    storageName: firstStorageName
  }
}

// module deployed to subscription
module newRG 'resourceGroup.bicep' = {
  name: 'newResourceGroup'
  scope: subscription(secondSubscriptionID)
  params: {
    resourceGroupName: secondResourceGroup
    resourceGroupLocation: secondLocation
  }
}
```

이전 예제에서는 새 리소스 그룹을 만드는 모듈에 대해 다음 Bicep 파일을 사용합니다.

```bicep
targetScope='subscription'

param resourceGroupName string
param resourceGroupLocation string

resource newRG 'Microsoft.Resources/resourceGroups@2021-01-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
}
```

## <a name="next-steps"></a>다음 단계

다른 범위에 대해 알아보려면 다음을 참조합니다.

* [구독 배포](deploy-to-subscription.md)
* [관리 그룹 배포](deploy-to-management-group.md)
* [테넌트 배포](deploy-to-tenant.md)
