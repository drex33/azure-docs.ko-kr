---
title: Bicep에서 리소스의 여러 인스턴스 배포
description: Bicep 파일의 루프 및 배열을 사용하여 여러 리소스 인스턴스를 배포합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: 3185d6bac1e20e1d29c4f55b0a4e954b5ae35499
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634886"
---
# <a name="resource-iteration-in-bicep"></a>Bicep의 리소스 반복

이 문서에서는 Bicep 파일에서 둘 이상의 리소스 인스턴스를 만드는 방법을 보여 줍니다. 파일의 `resource` 섹션에 루프를 추가하고 배포할 리소스 수를 동적으로 설정할 수 있습니다. 또한 Bicep 파일에서 구문이 반복하지 않도록 합니다.

[속성](loop-properties.md), [변수](loop-variables.md) 및 [출력](loop-outputs.md)과 함께 루프를 사용할 수도 있습니다.

리소스 배포 여부를 지정해야 하는 경우, [조건 요소](conditional-resource-deployment.md)를 참조하세요.

## <a name="syntax"></a>Syntax

루프는 다음과 같은 방법으로 여러 리소스를 선언하는 데 사용할 수 있습니다.

- 배열 반복.

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
    <resource-properties>
  }]
  ```

- 배열의 요소 반복.

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for (<item>, <index>) in <collection>: {
    <resource-properties>
  }]
  ```

- 루프 인덱스 사용.

  ```bicep
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <index> in range(<start>, <stop>): {
    <resource-properties>
  }]
  ```

## <a name="loop-limits"></a>루프 한계

Bicep 파일의 루프 반복은 음수일 수 없으며 800회 반복을 초과할 수 없습니다. Bicep 파일을 배포하려면 최신 버전의 [Bicep 도구](install.md)를 설치합니다.

## <a name="resource-iteration"></a>리소스 반복

다음 예제에서는 `storageCount` 매개 변수에 지정된 스토리지 계정 수를 만듭니다.

```bicep
param rgLocation string = resourceGroup().location
param storageCount int = 2

resource storageAcct 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, storageCount): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

인덱스 `i`는 스토리지 계정 리소스 이름을 만드는 데 사용됩니다.

다음 예제에서는 `storageNames` 매개 변수에 제공된 각 이름에 대해 하나의 스토리지 계정을 만듭니다.

```bicep
param rgLocation string = resourceGroup().location
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

resource storageAcct 'Microsoft.Storage/storageAccounts@2021-02-01' = [for name in storageNames: {
  name: '${name}${uniqueString(resourceGroup().id)}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

배포된 리소스에서 값을 반환하려는 경우에는 [출력 섹션](loop-outputs.md)에서 루프를 사용할 수 있습니다.

## <a name="resource-iteration-with-condition"></a>조건이 있는 리소스 반복

다음 예제에서는 필터링된 리소스 루프와 결합된 중첩된 루프를 보여 줍니다. 필터는 부울 값으로 계산되는 식이어야 합니다.

```bicep
resource parentResources 'Microsoft.Example/examples@2020-06-06' = [for parent in parents: if(parent.enabled) {
  name: parent.name
  properties: {
    children: [for child in parent.children: {
      name: child.name
      setting: child.settingValue
    }]
  }
}]
```

필터는 모듈 루프에서도 지원됩니다.

## <a name="deploy-in-batches"></a>일괄 배포

기본적으로 Resource Manager는 병렬로 리소스를 만듭니다. 루프를 사용하여 한 리소스 종류의 여러 인스턴스를 만드는 경우 해당 인스턴스는 모두 동시에 배포됩니다. 생성되는 순서는 정해져 있지 않습니다. Bicep 파일에서 총 리소스 제한이 800개라는 점을 제외하고 병렬로 배포되는 리소스의 수에는 제한이 없습니다.

한 리소스 종류의 모든 인스턴스를 동시에 업데이트하려고 하지는 않을 수 있습니다. 예를 들어 프로덕션 환경을 업데이트할 때 특정 수를 한 번에 업데이트하도록 업데이트를 늦추려고 할 수 있습니다. 일괄 처리할 인스턴스의 하위 집합을 지정하고 동시에 배포할 수 있습니다. 다른 인스턴스는 해당 일괄 처리가 완료되기를 기다립니다.

리소스 인스턴스를 직렬로 배포하려면 [batchSize 데코레이터](./file.md#resource-and-module-decorators)를 추가합니다. 해당 값을 한 번에 배포할 인스턴스 수로 설정합니다. 루프에 이전 인스턴스에 대한 종속성이 생성됩니다. 따라서 이전 일괄 처리가 완료될 때까지 하나의 일괄 처리를 시작하지 않습니다.

```bicep
param rgLocation string = resourceGroup().location

@batchSize(2)
resource storageAcct 'Microsoft.Storage/storageAccounts@2021-02-01' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: rgLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
}]
```

## <a name="iteration-for-a-child-resource"></a>자식 리소스에 대한 반복

중첩된 자식 리소스에 대해 루프를 사용할 수 없습니다. 자식 리소스의 인스턴스를 두 개 이상 만들려면 자식 리소스를 최상위 리소스로 변경합니다.

예를 들어, 일반적으로 파일 서비스 및 파일 공유를 스토리지 계정에 대한 중첩 리소스로 정의한다고 가정합니다.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
  resource service 'fileServices' = {
    name: 'default'
    resource share 'shares' = {
      name: 'exampleshare'
    }
  }
}
```

파일 공유를 두 개 이상 만들려면 스토리지 계정 외부로 이동합니다. `parent` 속성을 통해 부모 리소스와의 관계를 정의합니다.

다음 예제에서는 스토리지 계정, 파일 서비스 및 두 개 이상의 파일 공유를 만드는 방법을 보여 줍니다.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
}

resource service 'Microsoft.Storage/storageAccounts/fileServices@2021-02-01' = {
  name: 'default'
  parent: stg
}

resource share 'Microsoft.Storage/storageAccounts/fileServices/shares@2021-02-01' = [for i in range(0, 3): {
  name: 'exampleshare${i}'
  parent: service
}]
```

## <a name="example-templates"></a>예제 템플릿

다음 예제에서는 여러 리소스 또는 속성 인스턴스를 만들기 위한 일반적인 시나리오를 보여 줍니다.

|템플릿  |설명  |
|---------|---------|
|[스토리지 반복](https://github.com/Azure/azure-docs-bicep-samples/blob/main/bicep/multiple-instance/loopstorage.bicep) |이름의 인덱스 번호를 사용하여 여러 스토리지 계정을 배포합니다. |
|[스토리지 직렬 반복](https://github.com/Azure/azure-docs-bicep-samples/blob/main/bicep/multiple-instance/loopserialstorage.bicep) |여러 스토리지 계정을 한 번에 하나씩 배포합니다. 이름에는 인덱스 번호가 포함됩니다. |
|[배열을 사용하여 스토리지 반복](https://github.com/Azure/azure-docs-bicep-samples/blob/main/bicep/multiple-instance/loopstoragewitharray.bicep) |여러 스토리지 계정을 배포합니다. 이름에는 배열의 값이 포함됩니다. |

## <a name="next-steps"></a>다음 단계

- 루프의 다른 용도는 다음을 참조하세요.
  - [Bicep 파일의 속성 반복](loop-properties.md)
  - [Bicep 파일의 변수 반복](loop-variables.md)
  - [Bicep 파일의 출력 반복](loop-outputs.md)
- Bicep 파일의 섹션에 대해 알아보려면 [Bicep 파일의 구조 및 구문 이해](file.md)를 참조하세요.
- 여러 리소스를 배포하는 방법에 대한 자세한 내용은 [Bicep 모듈 사용](modules.md)을 참조하세요.
- 루프에서 생성된 리소스에 대한 종속성을 설정하려면 [리소스 종속성 설정](./resource-declaration.md#set-resource-dependencies)을 참조하세요.
- PowerShell을 사용하여 배포하는 방법을 알아보려면 [Bicep 및 Azure PowerShell을 사용하여 리소스 배포](deploy-powershell.md)를 참조하세요.
- Azure CLI를 사용하여 배포하는 방법을 알아보려면 [Bicep 및 Azure CLI를 사용하여 리소스 배포](deploy-cli.md)를 참조하세요.
