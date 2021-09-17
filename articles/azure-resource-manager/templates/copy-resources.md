---
title: 리소스의 여러 인스턴스 배포
description: ARM 템플릿(Azure Resource Manager 템플릿)의 복사 작업 및 배열을 사용하여 리소스 형식을 여러 번 배포합니다.
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: fc1b8389280880372e8209f5c699b39363e286bf
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123449786"
---
# <a name="resource-iteration-in-arm-templates"></a>ARM 템플릿의 리소스 반복

이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)에서 리소스의 인스턴스를 두 개 이상 만드는 방법을 보여줍니다. 템플릿의 리소스 섹션에 복사 루프를 추가하여 배포할 리소스의 수를 동적으로 설정할 수 있습니다. 템플릿 구문을 반복하지 않아도 됩니다.

[속성](copy-properties.md), [변수](copy-variables.md) 및 [출력](copy-outputs.md)과 함께 복사 루프를 사용할 수도 있습니다.

리소스 배포 여부를 지정해야 하는 경우, [조건 요소](conditional-resource-deployment.md)를 참조하세요.

## <a name="syntax"></a>구문

`copy` 요소를 템플릿의 리소스 섹션에 추가하여 리소스의 여러 인스턴스를 배포합니다. `copy` 요소의 일반적인 형식은 다음과 같습니다.

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

`name` 속성은 루프를 식별하는 모든 값입니다. `count` 속성은 리소스 형식에 대해 원하는 반복 횟수를 지정합니다.

`mode` 및 `batchSize` 속성을 사용하여 리소스를 병렬로 배포할지 또는 순차적으로 배포할지를 지정합니다. 이러한 속성은 [직렬 또는 병렬](#serial-or-parallel)에 설명되어 있습니다.

## <a name="copy-limits"></a>복사 제한

개수는 800개를 초과할 수 없습니다.

개수는 음수가 될 수 없습니다. 최신 버전의 Azure CLI, PowerShell 또는 REST API를 사용하여 템플릿을 배포하는 경우에는 0이 될 수 있습니다. 특히 다음을 사용해야 합니다.

- Azure PowerShell **2.6** 이상
- Azure CLI **2.0.74** 이상
- REST API 버전 **2019-05-10** 이상
- [연결된 배포](linked-templates.md)는 배포 리소스 유형에 API 버전 **2019-05-10** 이상을 사용해야 합니다.

이전 버전의 PowerShell, CLI 및 REST API는 개수에 0을 지원하지 않습니다.

복사 루프에서 [전체 모드 배포](deployment-modes.md)를 사용할 때는 주의해야 합니다. 전체 모드를 사용하여 리소스 그룹에 다시 배포하면 복사 루프를 확인한 후 템플릿에 지정되지 않은 모든 리소스가 삭제됩니다.

## <a name="resource-iteration"></a>리소스 반복

다음 예제에서는 `storageCount` 매개 변수에 지정된 스토리지 계정 수를 만듭니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ]
}
```

각 리소스의 이름에는 `copyIndex()` 함수가 포함되어 있으며 이 함수는 루프에서 현재 반복을 반환합니다. `copyIndex()`는 0부터 시작합니다. 따라서 예제는 다음과 같습니다.

```json
"name": "[concat('storage', copyIndex())]",
```

다음과 같은 이름을 만듭니다.

- storage0
- storage1
- storage2

인덱스 값을 오프셋하려면 `copyIndex()` 함수에 값을 전달하면 됩니다. 반복 횟수는 여전히 복사 요소에서 지정되지만 `copyIndex` 값은 지정된 값으로 오프셋됩니다. 따라서 예제는 다음과 같습니다.

```json
"name": "[concat('storage', copyIndex(1))]",
```

다음과 같은 이름을 만듭니다.

- storage1
- storage2
- storage3

복사 작업은 배열의 각 요소를 반복할 수 있으므로 배열을 사용할 때 유용합니다. 배열의 `length` 함수를 사용하여 반복 횟수를 지정하고, `copyIndex`를 사용하여 배열의 현재 인덱스를 검색합니다.

다음 예제에서는 매개 변수에 제공된 각 이름에 대해 하나의 스토리지 계정을 만듭니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
          "type": "array",
          "defaultValue": [
            "contoso",
            "fabrikam",
            "coho"
          ]
      }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

배포된 리소스에서 값을 반환하려는 경우에는 [출력 섹션에서 복사](copy-outputs.md)를 사용할 수 있습니다.

## <a name="serial-or-parallel"></a>직렬 또는 병렬

기본적으로 Resource Manager는 병렬로 리소스를 만듭니다. 템플릿의 총 리소스 제한인 800개를 제외하고 병렬로 배포되는 리소스의 수에는 제한이 없습니다. 생성되는 순서는 정해져 있지 않습니다.

그러나 그 결과로 리소스가 배포되도록 지정하려고 합니다. 예를 들어 프로덕션 환경을 업데이트할 때 특정 수를 한 번에 업데이트하도록 업데이트를 늦추려고 할 수 있습니다.

리소스의 여러 인스턴스를 직렬로 배포하려면 `mode`를 **직렬** 로 설정하고 `batchSize`를 한 번에 배포할 인스턴스 수로 설정합니다. Resource Manager는 직렬 모드에서 루프에 이전 인스턴스의 종속성을 만듭니다. 따라서 이전 일괄 처리가 완료될 때까지 하나의 일괄 처리를 시작하지 않습니다.

`batchSize` 값은 copy 요소의 `count` 값을 초과할 수 없습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

`mode` 속성은 기본값인 **병렬** 도 수용합니다.

## <a name="iteration-for-a-child-resource"></a>자식 리소스에 대한 반복

자식 리소스에 대해 복사 루프를 사용할 수 없습니다. 일반적으로 다른 리소스 내에 중첩된 것으로 정의된 여러 인스턴스를 만들려면 대신 해당 리소스를 최상위 리소스로 만들어야 합니다. 형식 및 이름 속성을 통해 부모 리소스와의 관계를 정의합니다.

예를 들어, 데이터 세트를 데이터 팩터리 내에 자식 리소스로 정의한다고 가정합니다.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

둘 이상의 데이터 세트를 만들려면 데이터 팩터리의 외부로 이동합니다. 데이터 세트는 데이터 팩터리와 같은 수준에 있어야 하지만 여전히 데이터 팩터리의 자식 리소스입니다. 형식 및 이름 속성을 통해 데이터 집합과 데이터 팩터리 간의 관계를 유지합니다. 템플릿의 해당 위치에서 형식을 더 이상 유추할 수 없으므로 `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}` 형식으로 정규화된 형식을 제공해야 합니다.

데이터 팩터리 인스턴스로 부모/자식 관계를 설정하려면 부모 리소스 이름을 포함하는 데이터 집합에 대해 이름을 제공합니다. 사용할 형식: `{parent-resource-name}/{child-resource-name}`.

다음 예에서는 구현을 보여줍니다.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/factories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

## <a name="example-templates"></a>예제 템플릿

다음 예제에서는 여러 리소스 또는 속성 인스턴스를 만들기 위한 일반적인 시나리오를 보여 줍니다.

|템플릿  |Description  |
|---------|---------|
|[스토리지 복사](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |이름의 인덱스 번호를 사용하여 여러 스토리지 계정을 배포합니다. |
|[스토리지 직렬 복사](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |여러 스토리지 계정을 한 번에 하나씩 배포합니다. 이름에는 인덱스 번호가 포함됩니다. |
|[배열을 사용하여 스토리지 복사](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |여러 스토리지 계정을 배포합니다. 이름에는 배열의 값이 포함됩니다. |
| [리소스 그룹 복사](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyRG.json) | 여러 리소스 그룹을 배포 합니다. |

## <a name="next-steps"></a>다음 단계

- 복사 루프에서 만들어진 리소스에 대한 종속성을 설정하려면 [ARM 템플릿에서 리소스 배포 순서 정의](./resource-dependency.md)를 참조하세요.
- 자습서를 살펴보려면 [자습서: ARM 템플릿을 사용하여 여러 리소스 인스턴스 만들기](template-tutorial-create-multiple-instances.md)를 참조하세요.
- 리소스 복사를 다루는 Microsoft Learn 모듈은 [고급 ARM 템플릿 기능을 사용하여 복잡한 클라우드 배포 관리](/learn/modules/manage-deployments-advanced-arm-template-features/)를 참조하세요.
- 복사 루프의 다른 용도는 다음을 참조하세요.
  - [ARM 템플릿의 속성 반복](copy-properties.md)
  - [ARM 템플릿의 변수 반복](copy-variables.md)
  - [ARM 템플릿의 출력 반복](copy-outputs.md)
- 중첩된 템플릿과 함께 복사를 사용하는 방법에 대한 자세한 내용은 [복사 사용](linked-templates.md#using-copy)을 참조하세요.
