---
title: Bicep 파일 구조 및 구문
description: 선언 구문을 사용하여 Bicep 파일의 구조 및 속성을 설명합니다.
ms.topic: conceptual
ms.date: 10/07/2021
ms.openlocfilehash: 8cdeb75aa676b9121d69ba8bf612067157328ee0
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067158"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>Bicep 파일의 구조 및 구문 이해

이 문서에서는 Bicep 파일의 구조에 대해 설명합니다. 여기서는 파일의 다른 섹션 및 해당 섹션에서 사용할 수 있는 속성을 보여 줍니다.

이 문서는 Bicep 파일에 대해 어느 정도 알고 있는 사용자를 위한 것입니다. 여기에서는 Bicep 파일의 구조에 대한 자세한 정보를 제공합니다. Bicep 파일을 만드는 과정을 안내하는 단계별 자습서는 [빠른 시작: Visual Studio Code를 사용하여 Bicep 파일 만들기](./quickstart-create-bicep-use-visual-studio-code.md)를 참조하세요.

## <a name="bicep-format"></a>Bicep 형식

Bicep 파일은 다음과 같은 요소로 구성되어 있습니다. Bicep은 선언적 언어이므로 요소가 임의의 순서로 표시될 수 있습니다.  명령적 언어와 달리 요소의 순서는 배포가 처리되는 방식에 영향을 주지 않습니다.

```bicep
targetScope = '<scope>'

@<decorator>(<argument>)
param <parameter-name> <parameter-data-type> = <default-value>

var <variable-name> = <variable-value>

resource <resource-symbolic-name> '<resource-type>@<api-version>' = {
  <resource-properties>
}

// conditional deployment
resource <resource-symbolic-name> '<resource-type>@<api-version>' = if (<condition-to-deploy>) {
  <resource-properties>
}

// iterative deployment
@<decorator>(<argument>)
resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
  <resource-properties>
}]

module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// conditional deployment
module <module-symbolic-name> '<path-to-file>' = if (<condition-to-deploy>) {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// iterative deployment
module <module-symbolic-name> '<path-to-file>' = [for <item> in <collection>: {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}]

// deploy to different scope
module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  scope: <scope-object>
  params: {
    <parameter-names-and-values>
  }
}

output <output-name> <output-data-type> = <output-value>

// iterative output
output <output-name> array = [for <item> in <collection>: {
  <output-properties>
}]
```

다음 예제는 해당 요소의 구현을 보여 줍니다.

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location

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

module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

## <a name="target-scope"></a>대상 범위

기본적으로 대상 범위는 `resourceGroup`으로 설정됩니다. 리소스 그룹 수준에서 배포하는 경우 Bicep 파일에서 대상 범위를 설정하지 않아도 됩니다.

허용되는 값은 다음과 같습니다.

* **resourceGroup** - [리소스 그룹 배포](deploy-to-resource-group.md)에 사용되는 기본값입니다.
* **subscription** - [구독 배포](deploy-to-subscription.md)에 사용됩니다.
* **managementGroup** - [관리 그룹 배포](deploy-to-management-group.md)에 사용됩니다.
* **tenant** - [테넌트 배포](deploy-to-tenant.md)에 사용됩니다.

모듈에서 Bicep 파일의 나머지 부분에 대한 범위와 다른 범위를 지정할 수 있습니다. 자세한 내용은 [모듈 범위 구성](modules.md#set-module-scope)을 참조하세요.

## <a name="parameters"></a>매개 변수

배포마다 달라져야 하는 값에 대한 매개 변수를 사용합니다. 배포 중에 값이 제공되지 않은 경우 사용되는 매개 변수의 기본값을 정의할 수 있습니다.

예를 들어 SKU 매개 변수를 추가하여 리소스에 대해 다른 크기를 지정할 수 있습니다. Bicep 함수를 사용하여 리소스 그룹 위치를 가져오는 등의 기본값을 만들 수 있습니다.

```bicep
param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location
```

사용할 수 있는 데이터 형식은 [의 데이터 형식](data-types.md)을 참조하세요.

매개 변수는 변수, 모듈 또는 리소스와 동일한 이름을 가질 수 없습니다.

자세한 내용은 [Bicep의 매개 변수](./parameters.md)를 참조하세요.

## <a name="parameter-decorators"></a>매개 변수 데코레이터

각 매개 변수에 대해 하나 이상의 데코레이터를 추가할 수 있습니다. 해당 데코레이터는 매개 변수에 허용되는 값을 정의합니다. 다음 예제에서는 Bicep 파일을 통해 배포할 수 있는 SKU를 지정합니다.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

다음 표에서는 사용할 수 있는 데코레이터와 그 사용 방법에 대해 설명합니다.

| 데코레이터 | 적용 대상 | 인수 | 설명 |
| --------- | ---- | ----------- | ------- |
| 허용됨 | 모두 | array | 매개 변수에 허용되는 값입니다. 해당 데코레이터를 사용하여 사용자가 올바른 값을 제공하는지 확인합니다. |
| description | 모두 | 문자열 | 매개 변수를 사용하는 방법을 설명하는 텍스트입니다. 포털을 통해 사용자에게 설명이 표시됩니다. |
| maxLength | 배열, 문자열 | int | 문자열 및 배열 매개 변수의 최대 길이입니다. 해당 값이 포함되어 있습니다. |
| maxValue | int | int | 정수 매개 변수의 최댓값입니다. 해당 값이 포함되어 있습니다. |
| metadata | 모두 | 개체 | 매개 변수에 적용할 사용자 지정 속성입니다. 설명 데코레이터와 동일한 Description 속성을 포함할 수 있습니다. |
| minLength | 배열, 문자열 | int | 문자열 및 배열 매개 변수의 최소 길이입니다. 해당 값이 포함되어 있습니다. |
| minValue | int | int | 정수 매개 변수의 최솟값입니다. 해당 값이 포함되어 있습니다. |
| secure | 문자열, 개체 | 없음 | 매개 변수를 안전하다고 표시합니다. 보안 매개 변수의 값은 배포 기록에 저장되지 않으며 기록되지 않습니다. 자세한 내용은 [보안 문자열 및 개체](data-types.md#secure-strings-and-objects)를 참조하세요. |

데코레이터은 [sys 네임 스페이스](bicep-functions.md#namespaces-for-functions)에 있습니다. 동일한 이름을 가진 다른 항목의 데코레이터를 구분 해야 하는 경우 데코레이터 앞에를 붙입니다 `sys` . 예를 들어 Bicep 파일에 라는 매개 변수가 포함 된 경우 `description` **설명** 데코레이터를 사용 하는 경우 sys 네임 스페이스를 추가 해야 합니다.

```bicep
@sys.description('The name of the instance.')
param name string
@sys.description('The description of the instance to display.')
param description string
```

자세한 내용은 [데코레이터](parameters.md#decorators)를 참조 하세요.

## <a name="variables"></a>variables

Bicep 파일에서 반복되는 복잡한 식에 변수를 사용합니다. 예를 들어 여러 값을 결합하여 구성된 리소스 이름에 대한 변수를 추가할 수 있습니다.

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

변수에 대한 [데이터 형식](data-types.md)을 지정하지 않습니다. 대신 값에서 데이터 형식을 유추합니다.

변수는 매개 변수, 모듈 또는 리소스와 동일한 이름을 가질 수 없습니다.

자세한 내용은 [의 변수](./variables.md)를 참조하세요.

## <a name="resource"></a>리소스

`resource` 키워드를 사용하여 배포할 리소스를 정의합니다. 리소스 선언에는 해당 리소스에 대한 기호화된 이름이 포함됩니다. 리소스에서 값을 가져와야 하는 경우 Bicep 파일의 다른 부분에서 기호화된 이름을 사용합니다. 기호화 된 이름은 대/소문자를 구분 합니다. 문자, 숫자 및 _를 포함할 수 있습니다. 숫자로 시작할 수 없습니다.

리소스 선언에는 리소스 종류 및 API 버전도 포함됩니다.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
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
```

리소스 선언에서 리소스 종류에 대한 속성을 포함합니다. 이러한 속성은 각 리소스 종류에 대해 고유합니다.

자세한 내용은 [Bicep의 리소스 선언](resource-declaration.md)을 참조하세요.

[리소스를 조건부로 배포](conditional-resource-deployment.md)하려면 `if` 식을 추가합니다.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
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
```

리소스 유형의 [인스턴스를 두 개 이상 배포](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md)하려면 `for` 식을 추가합니다. 식은 배열의 구성원을 반복할 수 있습니다.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  name: storageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}]
```

리소스는 매개 변수, 변수 또는 모듈과 동일한 이름을 가질 수 없습니다.

## <a name="modules"></a>모듈

모듈을 사용하여 다시 사용하려는 코드가 포함된 다른 Bicep 파일에 연결합니다. 모듈에는 배포할 리소스가 하나 이상 포함되어 있습니다. 이러한 리소스는 Bicep 파일의 다른 리소스와 함께 배포됩니다.

```bicep
module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}
```

기호화된 이름을 사용하면 파일의 다른 위치에서 모듈을 참조할 수 있습니다. 예를 들어 기호화된 이름 및 출력 값의 이름을 사용하여 모듈에서 출력 값을 가져올 수 있습니다. 기호 이름에는 a-z, A-Z, 0-9 및 '_'가 포함될 수 있습니다. 이름은 숫자로 시작할 수 없습니다.

모듈은 매개 변수, 변수 또는 리소스와 동일한 이름을 가질 수 없습니다.

리소스와 마찬가지로 모듈을 조건부로 또는 반복적으로 배포할 수 있습니다. 모듈에 대한 구문은 리소스의 구문과 동일합니다.

자세한 내용은 [Bicep 모듈](./modules.md)을 참조하세요.

## <a name="resource-and-module-decorators"></a>리소스 및 모듈 데코레이터

리소스 또는 모듈 정의에 데코레이터를 추가할 수 있습니다. 지원되는 데코레이터는 `batchSize(int)`뿐입니다. `for` 식을 사용하는 리소스 또는 모듈 정의에만 적용할 수 있습니다.

기본적으로 리소스는 병렬로 배포됩니다. 완료된 순서를 알 수는 없습니다. `batchSize` 데코레이터를 추가하는 경우 인스턴스를 순차적으로 배포합니다. 정수 인수를 사용하여 병렬로 배포할 인스턴스 수를 지정합니다.

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

`batchSize`데코레이터는 [sys 네임스페이스](bicep-functions.md#namespaces-for-functions)에 있습니다. 이 데코레이터를 동일한 이름의 다른 항목과 구별해야 하는 경우 데코레이터의 앞면에 **sys를 두세요.**`@sys.batchSize(2)`

자세한 내용은 [일괄 처리로 배포](loop-resources.md#deploy-in-batches)를 참조하세요.

## <a name="outputs"></a>출력

출력을 사용하여 배포에서 값을 반환합니다. 일반적으로 다른 작업에 해당 값을 다시 사용해야 하는 경우 배포된 리소스에서 값을 반환합니다.

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

출력 값에 대한 [데이터 형식](data-types.md)을 지정합니다.

출력은 매개 변수, 변수, 모듈 또는 리소스와 동일한 이름을 가질 수 없습니다.

자세한 내용은 [Bicep의 출력](./outputs.md)을 참조하세요.

## <a name="whitespace"></a>공백

Bicep 파일을 작성할 때 공백과 탭은 무시됩니다. 그러나 새 줄에는 [개체](./data-types.md#objects) 및 [배열](./data-types.md#arrays) 선언 등의 의미 체계가 있습니다.

## <a name="comments"></a>주석

한 줄 주석에는 `//`를 사용하고 여러 줄 주석에는 `/* ... */`를 사용합니다.

다음 예제는 한 줄 주석을 보여 줍니다.

```bicep
// This is your primary NIC.
resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
   ...
}
```

다음 예제는 다중 줄 주석을 보여 줍니다.

```bicep
/*
  This Bicep file assumes the key vault already exists and
  is in same subscription and resource group as the deployment.
*/
param existingKeyVaultName string
```

## <a name="multi-line-strings"></a>다중 선 문자열

문자열을 여러 선으로 나눌 수 있습니다. 세 개의 작은따옴표 문자 `'''`을 사용하여 다중 선 문자열을 시작하고 종료합니다.

다중 선 문자열 내의 문자는 있는 그대로 처리됩니다. 이스케이프 문자는 필요하지 않습니다. 다중 선 문자열에는 `'''`을 포함할 수 없습니다. 현재 문자열 보간은 지원되지 않습니다.

`'''`을 연 직후에 문자열을 시작하거나 새로운 선을 포함할 수 있습니다. 두 경우 모두 결과 문자열에는 새 선이 포함되지 않습니다. Bicep 파일의 선 끝에 따라 새 줄은 `\r\n` 또는 `\n`로 해석됩니다.

다음 예제는 다중 선 문자열을 보여 줍니다.

```bicep
var stringVar = '''
this is multi-line
  string with formatting
  preserved.
'''
```

이전 예제는 다음 JSON과 동일합니다.

```json
"variables": {
  "stringVar": "this is multi-line\r\n  string with formatting\r\n  preserved.\r\n"
}
```

## <a name="next-steps"></a>다음 단계

Bicep에 대한 소개는 [Bicep이란?](./overview.md)을 참조하세요. Bicep 데이터 형식은 [데이터 형식](./data-types.md)을 참조하세요.
