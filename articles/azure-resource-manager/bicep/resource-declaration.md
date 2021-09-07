---
title: Bicep에서 리소스 선언
description: Bicep에 배포할 리소스를 선언하는 방법을 설명합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: a540a30cd93d9f1dc54f77355f2f6560444131c1
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122567786"
---
# <a name="resource-declaration-in-bicep"></a>Bicep의 리소스 선언

Bicep 파일을 통해 리소스를 배포하려면 `resource` 키워드를 사용하여 리소스 선언을 추가합니다.

## <a name="set-resource-type-and-version"></a>리소스 유형 및 버전 설정

Bicep 파일에 리소스를 추가하는 경우 리소스 유형 및 API 버전을 설정하여 시작합니다. 이러한 값은 리소스에 사용할 수 있는 다른 속성을 결정합니다.

다음 예제에서는 스토리지 계정에 리소스 유형 및 API 버전을 설정하는 방법을 보여 줍니다. 예제에는 전체 리소스 선언이 표시되지 않습니다.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

리소스에 대한 기호화된 이름을 설정합니다. 앞의 예제에서 기호화된 이름은 `stg`입니다. 기호화된 이름에 모든 값을 사용할 수 있지만, Bicep 파일의 다른 리소스, 매개 변수 또는 변수와 같을 수는 없습니다. 기호화된 이름이 리소스 이름과 동일하지 않습니다. 심볼 이름을 사용하여 Bicep 파일의 다른 부분에 있는 리소스를 참조할 수 있습니다.

Bicep은 [ARM 템플릿(Azure Resource Manager 템플릿) JSON](../templates/syntax.md)에서 사용할 수 있는 `apiProfile`을 지원하지 않습니다.

## <a name="set-resource-name"></a>리소스 이름 설정

각 리소스는 하나의 이름을 갖습니다. 리소스 이름을 설정할 때, [리소스 이름에 대한 규칙 및 제한 사항](../management/resource-name-rules.md)에 주의하십시오.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  ...
}
```

일반적으로 배포 중에 다른 값을 전달할 수 있도록 이름을 매개 변수로 설정하게 됩니다.

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  ...
}
```

## <a name="set-location"></a>위치 설정

많은 리소스에는 위치가 필요합니다. 리소스에 intellisense 또는 [템플릿 참조](/azure/templates/)를 통해 위치가 필요한지 여부를 판단할 수 있습니다. 다음 예제에서는 스토리지 계정에 사용되는 위치 매개 변수를 추가합니다.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: 'eastus'
  ...
}
```

일반적으로 위치를 다른 위치에 배포할 수 있도록 매개 변수로 설정하게 됩니다.

```bicep
param location string = resourceGroup().location

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: location
  ...
}
```

다양한 리소스 형식이 다양한 위치에서 지원됩니다. Azure 서비스에 대해 지원되는 위치를 가져오려면 [지역별 사용 가능 제품](https://azure.microsoft.com/global-infrastructure/services/)을 참조하세요.  리소스 종류에 대해 지원되는 위치를 가져오려면 Azure PowerShell 또는 Azure CLI를 사용합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="set-tags"></a>태그 설정

배포 중 리소스에 태그를 적용할 수 있습니다. 태그를 통해 배포된 리소스를 논리적으로 구성할 수 있습니다. 태그를 지정할 수 있는 다양한 방법의 예는 [ARM 템플릿 태그](../management/tag-resources.md#arm-templates)를 참조하세요.

## <a name="set-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID 설정

일부 리소스는 [Azure 리소스에 대한 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 지원합니다. 이러한 리소스에는 리소스 선언의 루트 수준에 ID 개체가 있습니다. 

시스템 할당 또는 사용자 할당 ID를 사용할 수 있습니다.

다음 예제에서는 Azure Kubernetes Service 클러스터에 대해 시스템 할당 ID를 구성하는 방법을 보여 줍니다.

```bicep
resource aks 'Microsoft.ContainerService/managedClusters@2020-09-01' = {
  name: clusterName
  location: location
  tags: tags
  identity: {
    type: 'SystemAssigned'
  }
```

다음 예제에서는 가상 머신에 대한 사용자 할당 ID를 구성하는 방법을 보여 줍니다.

```bicep
param userAssignedIdentity string

resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  name: vmName
  location: location
  identity: {
    type: 'UserAssigned'
    userAssignedIdentities: {
      '${userAssignedIdentity}': {}
    }
  }
```

## <a name="set-resource-specific-properties"></a>리소스 관련 속성 설정

위의 속성은 대부분의 리소스 형식에 대해 일반적입니다. 이러한 값을 설정한 후에는 배포하고 있는 리소스 유형과 관련된 속성을 설정해야 합니다.

Intellisense 또는 [템플릿 참조](/azure/templates/)를 사용하여 사용 가능한 속성과 필요한 속성을 판단합니다. 다음 예제에서는 스토리지 계정에 나머지 속성을 설정합니다.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: 'eastus'
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

## <a name="set-resource-dependencies"></a>리소스 종속성 설정

리소스를 배포할 때 일부 리소스가 다른 리소스보다 먼저 존재하는지 확인해야 할 수 있습니다. 예를 들어 데이터베이스를 배포하기 전에 논리 SQL Server가 필요합니다. 하나의 리소스를 다른 리소스에 종속된 것으로 표시하여 이 관계를 설정합니다. 리소스 배포 순서는 [암시적 종속성](#implicit-dependency) 및 [명시적 종속성](#explicit-dependency)의 두 가지 방법으로 영향을 받을 수 있습니다.

Azure Resource Manager는 리소스 간의 종속성을 평가한 후 종속된 순서에 따라 리소스를 배포합니다. 리소스가 서로 종속되어 있지 않은 경우 Resource Manager는 이를 병렬로 배포합니다. 동일한 Bicep 파일에 배포되는 리소스에 대한 종속성만 정의하면 됩니다.

### <a name="implicit-dependency"></a>암시적 종속성

암시적 종속성은 한 리소스 선언이 동일한 배포의 다른 리소스를 참조할 때 만들어집니다. 예를 들어 다음 예제에서 *dnsZone* 은 두 번째 리소스 정의에서 참조됩니다.

```bicep
resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'myZone'
  location: 'global'
}

resource otherResource 'Microsoft.Example/examples@2020-06-01' = {
  name: 'exampleResource'
  properties: {
    // get read-only DNS zone property
    nameServers: dnsZone.properties.nameServers
  }
}
```

중첩된 리소스에는 포함하는 리소스에 대한 암시적 종속성도 있습니다.

```bicep
resource myParent 'My.Rp/parentType@2020-01-01' = {
  name: 'myParent'
  location: 'West US'

  // depends on 'myParent' implicitly
  resource myChild 'childType' = {
    name: 'myChild'
  }
}
```

암시적 종속성이 있는 경우에는 **명시적 종속성을 추가하지 마세요**.

중첩된 리소스에 대한 자세한 내용은 [Bicep에서 자식 리소스의 이름과 종류 설정](./child-resource-name-type.md)을 참조하세요.

### <a name="explicit-dependency"></a>명시적 종속성

명시적 종속성은 `dependsOn` 속성을 사용하여 선언됩니다. 이 속성은 리소스 식별자 배열을 허용하므로 둘 이상의 종속성을 지정할 수 있습니다. 

다음 예제에서는 `dnsZone`이라는 DNS 영역에 종속되는 `otherZone`이라는 DNS 영역을 보여 줍니다.

```bicep
resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'demoeZone1'
  location: 'global'
}

resource otherZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'demoZone2'
  location: 'global'
  dependsOn: [
    dnsZone
  ]
}
```

리소스 간의 관계를 매핑하기 위해 `dependsOn`을 사용하는 경향이 있을 수 있지만 왜 그렇게 하는지 이해하는 것이 중요합니다. 예를 들어, 리소스가 상호 연결되는 방식을 문서화하기 위해 `dependsOn`은 올바른 접근 방식이 아닙니다. 배포 후 `dependsOn` 요소에 정의된 리소스를 쿼리할 수 없습니다. 불필요한 종속성을 설정하면 Resource Manager가 해당 리소스를 병렬로 배포할 수 없기 때문에 배포 시간이 느려집니다.

명시적 종속성이 필요한 경우도 있지만 이러한 경우는 드문 편입니다. 대부분의 경우 심볼 이름을 사용하여 리소스 간의 종속성을 암시할 수 있습니다. 명시적 종속성을 설정하는 경우 해당 종속성을 제거하는 방법이 있는지를 고려해야 합니다.

### <a name="visualize-dependencies"></a>앱 종속성 시각화

Visual Studio Code는 종속성을 시각화하기 위한 도구를 제공합니다. Visual Studio Code에서 Bicep 파일을 열고 왼쪽 위 모서리에서 시각화 도우미 단추를 선택합니다.  다음 스크린샷에서는 가상 머신의 종속성을 보여 줍니다.

:::image type="content" source="./media/resource-declaration/bicep-resource-visualizer.png" alt-text="Visual Studio Code Bicep 리소스 시각화 도우미 스크린샷":::

## <a name="reference-existing-resources"></a>기존 리소스 참조

현재 Bicep 파일 외부에 있는 리소스를 참조하려면 리소스 선언에서 `existing` 키워드를 사용합니다.

`existing` 키워드를 사용하는 경우 리소스의 `name`을 제공합니다. 다음 예제에서는 현재 배포와 동일한 리소스 그룹에서 기존 스토리지 계정을 가져옵니다.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: 'examplestorage'
}

output blobEndpoint string = stg.properties.primaryEndpoints.blob
```

필요에 따라 `scope` 속성을 설정하여 다른 범위에 있는 리소스에 액세스할 수 있습니다. 다음 예제에서는 다른 리소스 그룹에서 기존 스토리지 계정을 참조합니다.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: 'examplestorage'
  scope: resourceGroup(exampleRG)
}

output blobEndpoint string = stg.properties.primaryEndpoints.blob
```

범위를 설정하는 방법에 대한 자세한 내용은 [Bicep에 대한 범위 함수](bicep-functions-scope.md)를 참조하세요.

위의 예제에서는 스토리지 계정을 배포하지 않습니다. 대신 심볼 이름을 사용하여 기존 리소스의 속성에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 리소스를 조건부로 배포하려면 [Bicep의 조건부 배포](./conditional-resource-deployment.md)를 참조하세요.
