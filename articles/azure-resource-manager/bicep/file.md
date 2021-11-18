---
title: Bicep 파일 구조 및 구문
description: 선언 구문을 사용하여 Bicep 파일의 구조 및 속성을 설명합니다.
ms.topic: conceptual
ms.date: 11/17/2021
ms.openlocfilehash: 35d4264168f0e6b53311600d46d8913873850f20
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132755757"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>Bicep 파일의 구조 및 구문 이해

이 문서에서는 Bicep 파일의 구조와 구문을 설명합니다. 여기서는 파일의 다른 섹션 및 해당 섹션에서 사용할 수 있는 속성을 보여 줍니다.

Bicep 파일을 만드는 과정을 안내하는 단계별 자습서는 [빠른 시작: Visual Studio Code를 사용하여 Bicep 파일 만들기](./quickstart-create-bicep-use-visual-studio-code.md)를 참조하세요.

## <a name="bicep-format"></a>Bicep 형식

Bicep은 선언적 언어이므로 요소가 임의의 순서로 표시될 수 있습니다. 명령적 언어와 달리 요소의 순서는 배포가 처리되는 방식에 영향을 주지 않습니다.

Bicep 파일은 다음과 같은 요소로 구성되어 있습니다.

```bicep
targetScope = '<scope>'

@<decorator>(<argument>)
param <parameter-name> <parameter-data-type> = <default-value>

var <variable-name> = <variable-value>

resource <resource-symbolic-name> '<resource-type>@<api-version>' = {
  <resource-properties>
}

module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

output <output-name> <output-data-type> = <output-value>
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

예를 들어 SKU 매개 변수를 추가하여 리소스에 대해 서로 다른 크기를 지정할 수 있습니다. 테스트 또는 프로덕션에 배포하는지에 따라 다른 값을 전달할 수 있습니다.

```bicep
param storageSKU string = 'Standard_LRS'
```

매개 변수는 Bicep 파일에서 사용할 수 있습니다.

```bicep
sku: {
  name: storageSKU
}
```

자세한 내용은 [Bicep의 매개 변수](./parameters.md)를 참조하세요.

## <a name="parameter-decorators"></a>매개 변수 데코레이터

각 매개 변수에 대해 하나 이상의 데코레이터를 추가할 수 있습니다. 이러한 데코레이터는 매개 변수를 설명하고 전달되는 값에 대한 제약 조건을 정의합니다. 다음 예제에서는 하나의 데코레이터를 보여 주지만 사용할 수 있는 다른 많은 데코레이터가 있습니다.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

사용 가능한 모든 데코레이터에 대한 설명을 비롯한 자세한 내용은 [데코레이터 를 참조하세요.](parameters.md#decorators)

## <a name="variables"></a>변수

변수에 복잡한 식을 캡슐화하여 Bicep 파일을 더 읽기 쉽게 만들 수 있습니다. 예를 들어 여러 값을 결합하여 구성된 리소스 이름에 대한 변수를 추가할 수 있습니다.

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

복잡한 식이 필요할 때마다 이 변수를 적용합니다.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
```

자세한 내용은 [의 변수](./variables.md)를 참조하세요.

## <a name="resources"></a>리소스

`resource` 키워드를 사용하여 배포할 리소스를 정의합니다. 리소스 선언에는 해당 리소스에 대한 기호화된 이름이 포함됩니다. Bicep 파일의 다른 부분에서 이 기호 이름을 사용하여 리소스에서 값을 얻습니다.

리소스 선언에는 리소스 종류 및 API 버전이 포함됩니다. 리소스 선언의 본문 내에 리소스 유형에 특정한 속성을 포함합니다.

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

자세한 내용은 [Bicep의 리소스 선언](resource-declaration.md)을 참조하세요.

일부 리소스에는 부모/자식 관계가 있습니다. 부모 리소스 내부 또는 외부에서 자식 리소스를 정의할 수 있습니다.

다음 예제에서는 부모 리소스 내에서 자식 리소스를 정의하는 방법을 보여줍니다. 여기에는 스토리지 계정 내에 정의된 자식 리소스(파일 서비스)가 있는 스토리지 계정이 포함됩니다. 파일 서비스에는 그 안에 정의된 자식 리소스(공유)도 있습니다.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/insidedeclaration.bicep" highlight="9,12":::

다음 예제에서는 부모 리소스 외부에서 자식 리소스를 정의하는 방법을 보여줍니다. 부모 속성을 사용하여 부모/자식 관계를 식별합니다. 동일한 세 개의 리소스가 정의됩니다.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/outsidedeclaration.bicep" highlight="10,12,15,17":::

자세한 내용은 [Bicep에서 자식 리소스에 대한 이름 및 형식 설정](child-resource-name-type.md)을 참조하세요.

## <a name="modules"></a>모듈

모듈을 사용하면 다른 Bicep 파일의 Bicep 파일에서 코드를 다시 사용할 수 있습니다. 모듈 선언에서 다시 사용할 파일에 연결합니다. Bicep 파일을 배포하면 모듈의 리소스도 배포됩니다.

```bicep
module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}
```

기호화된 이름을 사용하면 파일의 다른 위치에서 모듈을 참조할 수 있습니다. 예를 들어 기호화된 이름 및 출력 값의 이름을 사용하여 모듈에서 출력 값을 가져올 수 있습니다.

자세한 내용은 [Bicep 모듈](./modules.md)을 참조하세요.

## <a name="resource-and-module-decorators"></a>리소스 및 모듈 데코레이터

리소스 또는 모듈 정의에 데코레이터를 추가할 수 있습니다. 지원되는 데코레이터는 `batchSize(int)`뿐입니다. `for` 식을 사용하는 리소스 또는 모듈 정의에만 적용할 수 있습니다.

기본적으로 리소스는 병렬로 배포됩니다. `batchSize` 데코레이터를 추가하는 경우 인스턴스를 순차적으로 배포합니다.

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

자세한 내용은 [일괄 처리로 배포](loops.md#deploy-in-batches)를 참조하세요.

## <a name="outputs"></a>출력

출력을 사용하여 배포에서 값을 반환합니다. 일반적으로 다른 작업에 해당 값을 다시 사용해야 하는 경우 배포된 리소스에서 값을 반환합니다.

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

자세한 내용은 [Bicep의 출력](./outputs.md)을 참조하세요.

## <a name="loops"></a>루프

Bicep 파일에 반복 루프를 추가하여 의 여러 복사본을 정의할 수 있습니다.

* resource
* 모듈(module)
* 변수
* 속성(property)
* output

식을 사용하여 `for` 루프를 정의합니다.

```bicep
param moduleCount int = 2

module stgModule './example.bicep' = [for i in range(0, moduleCount): {
  name: '${i}deployModule'
  params: {
  }
}]
```

배열, 개체 또는 정수 인덱스로 반복할 수 있습니다.

자세한 내용은 [Bicep의 반복 루프를 참조하세요.](loops.md)

## <a name="conditional-deployment"></a>조건부 배포

조건부로 배포된 Bicep 파일에 리소스 또는 모듈을 추가할 수 있습니다. 배포하는 동안 조건이 평가되고 결과에 따라 리소스 또는 모듈이 배포되는지 여부가 결정됩니다. 식을 사용하여 `if` 조건부 배포를 정의합니다.

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

자세한 내용은 [Bicep의 조건부 배포를 참조하세요.](conditional-resource-deployment.md)

## <a name="whitespace"></a>공백

Bicep 파일을 작성할 때 공백과 탭은 무시됩니다.

Bicep은 새라인 구분입니다. 예를 들면 다음과 같습니다.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  ...
}
```

다음과 같이 작성할 수 없습니다.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' =
    if (newOrExisting == 'new') {
      ...
    }
```

여러 줄로 개체 및 [배열을 정의합니다.](./data-types.md#arrays) [](./data-types.md#objects)

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

## <a name="known-limitations"></a>알려진 제한 사항

- 단일 apiProfile을 각 리소스 유형에 대해 설정된 apiVersion에 매핑하는 데 사용되는 apiProfile 개념을 지원하지 않습니다.
- 사용자 정의 함수 지원 없음
- 일부 Bicep 기능에는 중간 언어 (Azure Resource Manager JSON 템플릿)에 해당 하는 변경이 필요 합니다. 모든 필수 업데이트가 글로벌 Azure에 배포 된 경우 이러한 기능을 사용할 수 있는 것으로 발표 합니다. Azure Stack와 같은 다른 환경을 사용 하는 경우에는 기능이 사용 가능 하 게 될 때 지연이 발생할 수 있습니다. Bicep 기능은 중간 언어가 해당 환경 에서도 업데이트 된 경우에만 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Bicep에 대한 소개는 [Bicep이란?](./overview.md)을 참조하세요. Bicep 데이터 형식은 [데이터 형식](./data-types.md)을 참조하세요.
