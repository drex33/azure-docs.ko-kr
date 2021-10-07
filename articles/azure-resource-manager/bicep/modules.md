---
title: Bicep 모듈
description: 모듈을 정의하고 사용하는 방법과 모듈 범위를 사용하는 방법을 설명합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 10/05/2021
ms.openlocfilehash: bd5069db6a2ad9cb14f5f0b3bc28612afa519727
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619598"
---
# <a name="use-bicep-modules"></a>Bicep 모듈 사용

Bicep을 사용하면 복잡 한 솔루션을 모듈로 분할할 수 있습니다. Bicep 모듈은 다른 Bicep 파일에서 배포된 Bicep 파일일 뿐입니다. 모듈에서 리소스 선언의 복잡한 세부 정보를 캡슐화하여 모듈을 사용하는 파일의 가독성을 향상시킬 수 있습니다. 이러한 모듈을 다시 사용하고 다른 사용자와 공유할 수 있습니다. Bicep 모듈은 배포를 위해 중첩된 템플릿이 있는 단일 Azure Resource Manager [템플릿으로 변환됩니다.](../templates/linked-templates.md#nested-template)

이 문서에서는 모듈을 정의하고 사용하는 방법을 설명합니다.

자습서는 [Bicep 템플릿을 사용하여 Azure 리소스 배포](/learn/modules/deploy-azure-resources-by-using-bicep-templates/)를 참조하세요.

## <a name="define-modules"></a>모듈 정의

모든 Bicep 파일을 모듈로 사용할 수 있습니다. 모듈은 매개 변수 및 출력만 다른 Bicep 파일에 대한 계약으로 노출합니다. 매개 변수 및 출력은 선택 사항입니다.

다음 Bicep 파일을 직접 배포하여 스토리지 계정을 만들거나 모듈로 사용할 수 있습니다.  다음 섹션에서는 모듈을 사용하는 방법을 보여줍니다.

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_RAGRS'
  'Standard_ZRS'
  'Premium_LRS'
  'Premium_ZRS'
  'Standard_GZRS'
  'Standard_RAGZRS'
])
param storageSKU string = 'Standard_LRS'
param location string

var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

출력은 부모 Bicep 파일에 값을 전달하는 데 사용됩니다.

## <a name="consume-modules"></a>모듈 사용

_모듈_ 키워드를 사용하여 모듈을 사용합니다. 다음 Bicep 파일은 참조 중인 모듈 파일에 정의된 리소스를 배포합니다.

```bicep
@minLength(3)
@maxLength(11)
param namePrefix string
param location string = resourceGroup().location

module stgModule 'storageAccount.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

- **모듈**: 키워드.
- **심볼 이름**(stgModule): 모듈에 대한 식별자입니다.
- **모듈 파일:** 모듈 파일은 상대 경로를 사용하여 참조해야 합니다. 일관된 컴파일 플랫폼 간 사용을 위해 슬래시(/) 디렉터리 구분 기호를 사용하여 Bicep의 모든 경로를 지정해야 합니다. Windows 백슬래시(\\) 문자는 지원되지 않습니다. 경로에는 공백을 포함할 수 있습니다.
- 모듈을 사용하는 경우 **_name_** 속성(storagedeploy)이 필요합니다. Bicep에서 템플릿 IL을 생성하는 경우 이 필드는 모듈에 대해 생성되는 중첩된 배포 리소스의 이름으로 사용됩니다.

    ```json
    ...
    ...
    "resources": [
      {
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2020-10-01",
        "name": "storageDeploy",
        "properties": {
          ...
        }
      }
    ]
    ...
    ```
- **_params_** 속성에는 모듈 파일에 전달할 매개 변수가 포함됩니다. 이러한 매개 변수는 Bicep 파일에 정의된 매개 변수와 일치합니다.

리소스와 마찬가지로 모듈은 다른 모듈이나 리소스 배포에 의존하지 않는 한 병렬로 배포됩니다. 종속성에 대한 자세한 내용은 [리소스 종속성 설정](resource-declaration.md#set-resource-dependencies)을 참조하세요.

모듈에서 출력 값을 가져오려면 `stgModule.outputs.storageEndpoint`와 같은 구문을 사용하여 속성 값을 검색합니다. 여기서, `stgModule`은 모듈의 식별자입니다.

모듈을 조건부로 배포할 수 있습니다. [리소스를 조건부로 배포](conditional-resource-deployment.md)할 때와 동일한 **if** 구문을 사용합니다.

```bicep
param deployZone bool

module dnsZone 'dnszones.bicep' = if (deployZone) {
  name: 'myZoneModule'
}
```

루프를 사용하여 모듈을 여러 번 배포할 수 있습니다. 자세한 내용은 [Bicep의 모듈 반복을](loop-modules.md)참조하세요.

## <a name="configure-module-scopes"></a>모듈 범위 구성

모듈을 선언할 때 포함하는 Bicep 파일의 범위와 다른 모듈의 범위를 설정할 수 있습니다. 속성을 사용하여 `scope` 모듈의 범위를 설정합니다. Scope 속성을 제공하지 않으면 모듈이 부모의 대상 범위에 배포됩니다.

다음 Bicep 파일은 리소스 그룹을 만들고 리소스 그룹에 모듈을 배포하는 방법을 보여줍니다.

```bicep
// set the target scope for this file
targetScope = 'subscription'

@minLength(3)
@maxLength(11)
param namePrefix string

param location string = deployment().location

var resourceGroupName = '${namePrefix}rg'
resource myResourceGroup 'Microsoft.Resources/resourceGroups@2021-04-01' = {
  name: resourceGroupName
  location: location
  scope: subscription()
}

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: myResourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

다음 예제는 기존 리소스 그룹에 배포합니다.

```bicep
targetScope = 'subscription'

resource firstRG 'Microsoft.Resources/resourceGroups@2021-04-01' existing = {
  name: 'demogroup1'
}

resource secondRG 'Microsoft.Resources/resourceGroups@2021-04-01' existing = {
  name: 'demogroup2'
}

module storage1 'storageAccount.bicep' = {
  name: 'westusdeploy'
  scope: firstRG
  params: {
    storagePrefix: 'stg1'
    location: 'westus'
  }
}

module storage2 'storageAccount.bicep' = {
  name: 'eastusdeploy'
  scope: secondRG
  params: {
    storagePrefix: 'stg2'
    location: 'eastus'
  }
}
```

범위 속성은 유효한 범위 개체로 설정해야 합니다. Bicep 파일이 리소스 그룹, 구독 또는 관리 그룹을 배포하는 경우 모듈의 범위를 해당 리소스의 기호 이름으로 설정할 수 있습니다. 또는 범위 함수를 사용하여 유효한 범위를 얻을 수 있습니다.

해당 함수는 다음과 같습니다.

- [resourceGroup](bicep-functions-scope.md#resourcegroup)
- [subscription](bicep-functions-scope.md#subscription)
- [managementGroup](bicep-functions-scope.md#managementgroup)
- [테넌트](bicep-functions-scope.md#tenant)

다음 예제에서는 `managementGroup` 함수를 사용하여 범위를 설정합니다.

```bicep
param managementGroupName string

module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupName)
}
```

## <a name="next-steps"></a>다음 단계

- 중요한 값을 모듈에 전달하려면 [getSecret](bicep-functions-resource.md#getsecret) 함수를 사용합니다.
- 루프를 사용하여 모듈을 여러 번 배포할 수 있습니다. 자세한 내용은 [Bicep의 모듈 반복을](loop-modules.md)참조하세요.
