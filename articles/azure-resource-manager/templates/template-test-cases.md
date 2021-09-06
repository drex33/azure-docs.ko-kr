---
title: 테스트 도구 키트에 대한 템플릿 테스트 사례
description: Azure Resource Manager 템플릿 테스트 도구 키트에서 실행하는 템플릿 테스트에 대해 설명합니다.
ms.topic: conceptual
ms.date: 07/30/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 961a62ae45b423e2e4c2abf8a4e7d771c2775591
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536564"
---
# <a name="test-cases-for-arm-templates"></a>ARM 템플릿 테스트 사례

이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)에 대한 [템플릿 테스트 도구 키트](test-toolkit.md)를 통해 실행되는 테스트에 대해 설명합니다. 테스트를 **통과** 하거나 **실패** 하는 예제를 제공하고, 각 테스트의 이름을 포함하고 있습니다. 테스트를 실행하는 방법 또는 특정 테스트를 실행하는 방법에 대한 자세한 내용은 [테스트 매개 변수](test-toolkit.md#test-parameters)를 참조하세요.

## <a name="use-correct-schema"></a>올바른 스키마 사용

테스트 이름: **Deploymenttemplate 스키마가 올바름**

템플릿에서 유효한 스키마 값을 지정해야 합니다.

스키마가 유효하지 않으므로 다음 예제는 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-01-01/deploymentTemplate.json#",
}
```

`2015-01-01` 스키마 버전이 더 이상 사용되지 않고 유지 관리되지 않으므로 다음 예제는 **경고** 를 표시합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
}
```

다음 예제는 유효한 스키마를 사용하여 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
}
```

템플릿의 `schema` 속성은 다음 스키마 중 하나로 설정해야 합니다.

- `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="declared-parameters-must-be-used"></a>선언된 매개 변수를 사용해야 함

테스트 이름: **매개 변수를 참조 해야함**

이 테스트는 템플릿에 사용되지 않는 매개 변수 또는 유효한 식에 사용되지 않는 매개 변수를 찾습니다.

템플릿의 혼동을 줄이려면 정의되었지만 사용되지 않는 매개 변수를 모두 삭제합니다. 사용되지 않는 매개 변수를 제거하면 불필요한 값을 제공할 필요가 없으므로 템플릿 배포가 간소화됩니다.

매개 변수를 참조하는 식에 선행 대괄호(`[`)가 없으므로 다음 예제는 **실패** 합니다.

```json
"resources": [
  {
    "location": " parameters('location')]"
  }
]
```

식이 유효하므로 다음 예제는 **통과** 합니다.

```json
"resources": [
  {
    "location": "[parameters('location')]"
  }
]
```

## <a name="secure-parameters-cant-have-hard-coded-default"></a>보안 매개 변수에는 하드 코딩된 기본값을 사용할 수 없음

테스트 이름: **보안 문자열 매개 변수에는 기본값을 사용할 수 없음**

템플릿의 보안 매개 변수에 하드 코드된 기본값을 제공하지 마세요. 보안 매개 변수는 빈 문자열을 기본값으로 사용하거나 식에서 [newGuid](template-functions-string.md#newguid) 함수를 사용합니다.

암호와 같은 중요한 값을 포함하는 매개 변수에는 `secureString` 또는 `secureObject` 형식을 사용할 수 있습니다. 매개 변수가 보안 유형을 사용하는 경우 매개 변수 값이 기록되지 않거나 배포 기록에 저장되지 않습니다. 이 작업을 수행하면 악의적인 사용자가 중요한 값을 검색하지 못합니다.

기본값을 제공하면 템플릿 또는 배포 기록에 액세스할 수 있는 사용자가 해당 값을 검색할 수 있습니다.

다음 예제는 **실패** 합니다.

```json
"parameters": {
  "adminPassword": {
    "defaultValue": "HardcodedPassword",
    "type": "secureString"
  }
}
```

다음 예제는 **통과** 합니다.

```json
"parameters": {
  "adminPassword": {
    "type": "secureString"
  }
}
```

`newGuid` 함수가 사용되므로 다음 예제는 **통과** 합니다.

```json
"parameters": {
  "secureParameter": {
    "type": "secureString",
    "defaultValue": "[newGuid()]"
  }
}
```

## <a name="environment-urls-cant-be-hard-coded"></a>환경 URL은 하드 코딩할 수 없음

테스트 이름: **DeploymentTemplate에 하드 코드된 URI가 포함되지 않아야 함**

템플릿에서 환경 URL을 하드 코딩하지 마세요. 대신 [environment](template-functions-deployment.md#environment) 함수를 사용하여 배포 중에 이러한 URL을 동적으로 가져옵니다. 차단된 URL 호스트 목록은 [테스트 사례](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1)를 참조하세요.

URL이 하드 코딩되어 있으므로 다음 예제는 **실패** 합니다.

```json
"variables":{
  "AzureURL":"https://management.azure.com"
}
```

[concat](template-functions-string.md#concat) 또는 [uri](template-functions-string.md#uri)와 함께 사용하는 경우에도 테스트에 **실패** 합니다.

```json
"variables":{
  "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
  "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

다음 예제는 **통과** 합니다.

```json
"variables": {
  "AzureSchemaURL": "[environment().gallery]"
}
```

## <a name="location-uses-parameter"></a>위치에서 매개 변수 사용

테스트 이름: **위치는 하드 코드되어서는 안 됨**

리소스의 위치를 설정하려면 형식이 `string`으로 설정된 `location`이라는 매개 변수가 템플릿에 있어야 합니다. 기본 템플릿(_azuredeploy.json_ 또는 _mainTemplate.json_)에서 이 매개 변수는 기본적으로 리소스 그룹 위치로 설정할 수 있습니다. 연결되거나 중첩된 템플릿에서 위치 매개 변수에는 기본 위치를 사용할 수 없습니다.

템플릿 사용자는 리소스를 만들 수 있는 지역에 대한 액세스가 제한될 수 있습니다. 하드 코딩된 리소스 위치는 사용자가 리소스를 만들지 못하도록 차단할 수 있습니다. 리소스 그룹이 사용자가 액세스할 수 없는 지역에 만들어진 경우 `"[resourceGroup().location]"` 식은 사용자를 차단할 수 있습니다. 차단된 사용자는 템플릿을 사용할 수 없습니다.

기본적으로 리소스 그룹 위치로 설정된 `location` 매개 변수를 제공하면 사용자가 편리할 때 기본값을 사용할 수 있지만, 다른 위치를 지정할 수도 있습니다.

리소스의 `location`이 `resourceGroup().location`으로 설정되어 있으므로 다음 예제는 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ]
}
```

`location` 매개 변수를 사용하지만 이 매개 변수가 기본적으로 하드 코딩된 위치로 설정되어 있으므로 다음 예제는 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "westus"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[parameters('location')]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

템플릿이 기본 템플릿으로 사용되면 다음 예제는 **통과** 합니다. 기본적으로 리소스 그룹 위치로 설정되지만 사용자가 다른 값을 제공할 수 있도록 하는 매개 변수를 만듭니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for the resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[parameters('location')]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

> [!NOTE]
> 앞의 예제가 연결된 템플릿으로 사용되면 테스트가 **실패** 합니다. 연결된 템플릿으로 사용하는 경우 기본값을 제거합니다.

## <a name="resources-should-have-location"></a>리소스에 위치가 있어야 함

테스트 이름: **리소스에 위치가 있어야 함**

리소스의 위치는 [템플릿 식](template-expressions.md) 또는 `global`으로 설정해야 합니다. 템플릿 식은 일반적으로 [위치에서 매개 변수 사용](#location-uses-parameter)에 설명된 `location` 매개 변수를 사용합니다.

`location`이 식 또는 `global`이 아니므로 다음 예제는 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "westus",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

리소스 `location`이 `global`로 설정되어 있으므로 다음 예제는 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "global",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}

```

`location` 매개 변수에서 식을 사용하므로 다음 예제도 **통과** 합니다. 리소스 `location`에서 식의 값을 사용합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for the resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[parameters('location')]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>VM 크기에서 매개 변수 사용

테스트 이름: **VM 크기는 매개 변수여야 함**

`hardwareProfile` 개체의 `vmSize`를 하드 코딩하지 마세요. `hardwareProfile`이 생략되거나 하드 코딩된 값이 포함되면 테스트가 실패합니다. 템플릿의 사용자가 배포된 가상 머신의 크기를 수정할 수 있도록 매개 변수를 제공합니다. 자세한 내용은 [Microsoft.Compute virtualMachines](/azure/templates/microsoft.compute/virtualmachines)를 참조하세요.

`hardwareProfile` 개체의 `vmSize`가 하드 코딩된 값이므로 다음 예제는 **실패** 합니다.

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-12-01",
    "name": "demoVM",
    "location": "[parameters('location')]",
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_D2_v3"
      }
    }
  }
]
```

매개 변수에서 `vmSize` 값을 지정하면 다음 예제는 **통과** 합니다.

```json
"parameters": {
  "vmSizeParameter": {
    "type": "string",
    "defaultValue": "Standard_D2_v3",
    "metadata": {
      "description": "Size for the virtual machine."
    }
  }
}
```

그런 다음, `hardwareProfile`에서 `vmSize`에 대한 식을 사용하여 매개 변수의 값을 참조합니다.

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-12-01",
    "name": "demoVM",
    "location": "[parameters('location')]",
    "properties": {
      "hardwareProfile": {
        "vmSize": "[parameters('vmSizeParameter')]"
      }
    }
  }
]
```

## <a name="min-and-max-values-are-numbers"></a>최소 및 최대 값은 숫자

테스트 이름: **최소 및 최대 값은 숫자**

`minValue` 및 `maxValue`를 사용하여 매개변수를 정의하는 경우 숫자로 지정하세요. `minValue` 및 `maxValue`를 쌍으로 사용해야 합니다. 그렇지 않으면 테스트가 실패합니다.

`minValue` 및 `maxValue`가 문자열이므로 다음 예제는 **실패** 합니다.

```json
"exampleParameter": {
  "type": "int",
  "minValue": "0",
  "maxValue": "10"
}
```

`minValue`만 사용되므로 다음 예제는 **실패** 합니다.

```json
"exampleParameter": {
  "type": "int",
  "minValue": 0
}
```

`minValue` 및 `maxValue`가 숫자이므로 다음 예제는 **통과** 합니다.

```json
"exampleParameter": {
  "type": "int",
  "minValue": 0,
  "maxValue": 10
}
```

## <a name="artifacts-parameter-defined-correctly"></a>올바르게 정의된 아티팩트 매개 변수

테스트 이름: **아티팩트 매개 변수**

`_artifactsLocation` 및 `_artifactsLocationSasToken`에 대한 매개 변수를 포함하는 경우 올바른 기본값 및 형식을 사용하세요. 이 테스트를 통과하려면 다음 조건을 충족해야 합니다.

- 하나의 매개 변수를 제공하는 경우 다른 매개 변수를 제공해야 합니다.
- `_artifactsLocation`은 `string`이어야 합니다.
- `_artifactsLocation`에는 기본 템플릿의 기본값이 있어야 합니다.
- `_artifactsLocation`에는 중첩된 템플릿의 기본값이 있을 수 없습니다.
- `_artifactsLocation`에는 해당 기본값으로 `"[deployment().properties.templateLink.uri]"` 또는 원시 리포지토리 URL이 있어야 합니다.
- `_artifactsLocationSasToken`은 `secureString`이어야 합니다.
- `_artifactsLocationSasToken`에는 해당 기본값으로 빈 문자열만 있을 수 있습니다.
- `_artifactsLocationSasToken`에는 중첩된 템플릿의 기본값이 있을 수 없습니다.

## <a name="declared-variables-must-be-used"></a>선언된 변수를 사용해야 함

테스트 이름: **변수를 참조해야 함**

이 테스트는 템플릿에 사용되지 않거나 유효한 식에 사용되지 않는 변수를 찾습니다. 템플릿에서 혼동을 줄이려면 정의되었지만 사용되지 않는 모든 변수를 삭제합니다.

`copy` 요소를 사용하여 값을 반복하는 변수를 참조해야 합니다. 자세한 내용은 [ARM 템플릿의 변수 반복](copy-variables.md)을 참조하세요.

`copy` 요소를 사용하는 변수가 참조되지 않으므로 다음 예제는 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "copy": [
      {
        "name": "stringArray",
        "count": "[parameters('itemCount')]",
        "input": "[concat('item', copyIndex('stringArray', 1))]"
      }
    ]
  },
  "resources": [],
  "outputs": {}
}
```

변수를 참조하는 식에 선행 대괄호(`[`)가 없으므로 다음 예제는 **실패** 합니다.

```json
"outputs": {
  "outputVariable": {
    "type": "string",
    "value": " variables('varExample')]"
  }
}
```

변수가 `outputs`에서 참조되므로 다음 예제는 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "copy": [
      {
        "name": "stringArray",
        "count": "[parameters('itemCount')]",
        "input": "[concat('item', copyIndex('stringArray', 1))]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "arrayResult": {
      "type": "array",
      "value": "[variables('stringArray')]"
    }
  }
}
```

식이 유효하므로 다음 예제는 **통과** 합니다.

```json
"outputs": {
  "outputVariable": {
    "type": "string",
    "value": "[variables('varExample')]"
  }
}
```

## <a name="dynamic-variable-should-not-use-concat"></a>동적 변수는 concat을 사용해서는 안 됨

테스트 이름: **동적 변수 참조는 concat을 사용해서는 안 됨**

다른 변수 또는 매개 변수의 값을 기반으로 변수를 동적으로 구성해야 하는 경우도 있습니다. 값을 설정할 때 [concat](template-functions-string.md#concat) 함수를 사용하지 마세요. 대신, 사용 가능한 옵션이 포함된 개체를 사용하고 배포 중에 개체에서 속성 중 하나를 동적으로 가져옵니다.

다음 예제는 **통과** 합니다. `currentImage` 변수는 배포 중에 동적으로 설정됩니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "osType": {
      "type": "string",
      "allowedValues": [
        "Windows",
        "Linux"
      ]
    }
  },
  "variables": {
    "imageOS": {
      "Windows": {
        "image": "Windows Image"
      },
      "Linux": {
        "image": "Linux Image"
      }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
    "result": {
      "type": "string",
      "value": "[variables('currentImage')]"
    }
  }
}
```

## <a name="use-recent-api-version"></a>최근 API 버전 사용

테스트 이름: **apiVersions는 최신 버전이어야 함**

각 리소스의 API 버전은 문자열로 하드 코딩된 최신 버전을 사용해야 합니다. 이 테스트는 도구 키트 캐시에 있는 리소스 공급자 버전과 비교하여 템플릿의 API 버전을 평가합니다. 테스트가 실행된 날짜로부터 2년 미만의 API 버전은 최신 버전으로 간주됩니다. 최신 버전을 사용할 수 있는 경우 미리 보기 버전을 사용하지 마세요.

API 버전을 찾을 수 없다는 경고는 해당 버전이 도구 키트의 캐시에 포함되어 있지 않음을 나타냅니다. 권장되는 최신 버전의 API를 사용하면 경고를 생성할 수 있습니다.

[도구 키트 캐시](https://github.com/Azure/arm-ttk/tree/master/arm-ttk/cache)에 대해 자세히 알아보세요.

API 버전이 2년을 초과한 이전 버전이므로 다음 예제는 **실패** 합니다.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]"
  }
]
```

최신 버전을 사용할 수 있을 때 미리 보기 버전이 사용되므로 다음 예제는 **실패** 합니다.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2020-08-01-preview",
    "name": "storageaccount1",
    "location": "[parameters('location')]"
  }
]
```

미리 보기 버전이 아닌 최신 버전이므로 다음 예제는 **통과** 합니다.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2021-02-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]"
  }
]
```

## <a name="use-hard-coded-api-version"></a>하드 코딩된 API 버전 사용

테스트 이름: **공급자 apiVersions는 허용되지 않음**

리소스 종류에 대한 API 버전은 사용할 수 있는 속성을 결정합니다. 템플릿에 하드 코드된 API 버전을 제공합니다. 사용할 수 있는 속성을 알 수 없으므로 배포 중에 결정된 API 버전을 검색하지 마세요.

다음 예제는 **실패** 합니다.

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
    ...
  }
]
```

다음 예제는 **통과** 합니다.

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-12-01",
    ...
  }
]
```

## <a name="properties-cant-be-empty"></a>속성은 비워 둘 수 없음

테스트 이름: **템플릿에 공백을 포함할 수 없음**

속성을 빈 값으로 하드 코딩하지 마세요. 빈 값은 null 및 빈 문자열, 개체 또는 배열을 포함합니다. 속성이 빈 값으로 설정되면 템플릿에서 해당 속성을 제거합니다. 속성은 매개 변수를 통해서와 같이 배포 중에 빈 값으로 설정할 수 있습니다.

[중첩된 템플릿](linked-templates.md#nested-template)의 `template` 속성에는 빈 속성이 포함될 수 있습니다. 중첩된 템플릿에 대한 자세한 내용은 [Microsoft.Resources deployments](/azure/templates/microsoft.resources/deployments)를 참조하세요.

빈 속성이 있으므로 다음 예제는 **실패** 합니다.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2021-01-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    "sku": {},
    "kind": ""
  }
]
```

속성에 값이 포함되어 있으므로 다음 예제는 **통과** 합니다.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2021-01-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    "sku": {
      "name": "Standard_LRS",
      "tier": "Standard"
    },
    "kind": "Storage"
  }
]
```

## <a name="use-resource-id-functions"></a>리소스 ID 함수 사용

테스트 이름: **ID는 ResourceID에서 파생되어야 함**

리소스 ID를 지정하는 경우 리소스 ID 함수 중 하나를 사용합니다. 허용되는 함수는 다음과 같습니다.

- [resourceId](template-functions-resource.md#resourceid)
- [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
- [tenantResourceId](template-functions-resource.md#tenantresourceid)
- [extensionResourceId](template-functions-resource.md#extensionresourceid)

concat 함수를 사용하여 리소스 ID를 만들지 마세요. 다음 예제는 **실패** 합니다.

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

다음 예제는 **통과** 합니다.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>ResourceId 함수에 올바른 매개 변수 포함

테스트 이름: **ResourceId에 포함할 수 없음**

리소스 ID를 생성할 때는 선택적 매개 변수에 불필요한 함수를 사용하지 마세요. 기본적으로 [resourceId](template-functions-resource.md#resourceid) 함수는 현재 구독 및 리소스 그룹을 사용합니다. 이러한 값은 제공하지 않아도 됩니다.

현재 구독 ID 및 리소스 그룹 이름을 제공할 필요가 없으므로 다음 예제는 **실패** 합니다.

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

다음 예제는 **통과** 합니다.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

이 테스트는 다음과 같이 적용됩니다.

- [resourceId](template-functions-resource.md#resourceid)
- [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
- [tenantResourceId](template-functions-resource.md#tenantresourceid)
- [extensionResourceId](template-functions-resource.md#extensionresourceid)
- [reference](template-functions-resource.md#reference)
- [list*](template-functions-resource.md#list)

`reference` 및 `list*`의 경우 리소스 ID 구성에 `concat`을 사용하는 경우 이 텍스트에 **실패** 합니다.

## <a name="dependson-best-practices"></a>dependsOn 모범 사례

테스트 이름: **DependsOn 모범 사례**

배포 종속성을 설정하는 경우 [if](template-functions-logical.md#if) 함수를 사용하여 조건을 테스트하지 마세요. 하나의 리소스가 [조건부로 배포](conditional-resource-deployment.md)된 리소스에 종속되는 경우 다른 리소스와 마찬가지로 종속성을 설정합니다. 조건부 리소스가 배포되지 않으면 Azure Resource Manager가 필요한 종속성에서 해당 리소스를 자동으로 제거합니다.

`dependsOn` 요소는 [concat](template-functions-array.md#concat) 함수로 시작할 수 없습니다.

`if` 함수가 포함되어 있으므로 다음 예제는 **실패** 합니다.

```json
"dependsOn": [
  "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

`concat`으로 시작하므로 다음 예제는 **실패** 합니다.

```json
"dependsOn": [
  "[concat(variables('storageAccountName'))]"
]
```

다음 예제는 **통과** 합니다.

```json
"dependsOn": [
  "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>중첩 또는 연결된 배포는 디버그를 사용할 수 없음

테스트 이름: **배포 리소스는 디버그할 수 없음**

`Microsoft.Resources/deployments` 리소스 종류를 사용하여 [중첩되거나 연결된 템플릿](linked-templates.md)을 정의하는 경우 [디버깅](/azure/templates/microsoft.resources/deployments#debugsetting-object)을 사용하도록 설정할 수 있습니다. 디버깅은 템플릿을 테스트해야 하지만 중요한 정보를 공개할 수 있는 경우에 사용됩니다. 템플릿을 프로덕션에서 사용하기 전에 디버깅을 해제합니다. `debugSetting` 개체를 제거하거나 `detailLevel` 속성을 `none`으로 변경할 수 있습니다.

다음 예제는 **실패** 합니다.

```json
"debugSetting": {
  "detailLevel": "requestContent"
}
```

다음 예제는 **통과** 합니다.

```json
"debugSetting": {
  "detailLevel": "none"
}
```

## <a name="admin-user-names-cant-be-literal-value"></a>관리 사용자 이름은 리터럴 값일 수 없음

테스트 이름: **adminUsername은 리터럴이 아니어야 함**

`adminUserName`을 설정하는 경우 리터럴 값을 사용하지 마세요. 사용자 이름에 대한 매개 변수를 만들고, 식을 사용하여 매개 변수의 값을 참조합니다.

다음 예제는 리터럴 값을 사용하여 **실패** 합니다.

```json
"osProfile":  {
  "adminUserName": "myAdmin"
}
```

다음 예제는 식을 사용하여 **통과** 합니다.

```json
"osProfile": {
  "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>최신 VM 이미지 사용

테스트 이름: **VM 이미지는 최신 버전을 사용해야 함**

이 테스트는 사용하지 않도록 설정되어 있지만 출력에 통과한 것으로 표시됩니다. 템플릿에서 다음 조건을 확인하는 것이 가장 좋습니다.

템플릿에 이미지가 있는 가상 컴퓨터가 포함된 경우 최신 버전의 이미지를 사용하고 있는지 확인합니다.

## <a name="use-stable-vm-images"></a>안정적인 VM 이미지 사용

테스트 이름: **가상 머신은 미리 보기를 사용하지 않아야 함**

가상 컴퓨터는 미리 보기 이미지를 사용하지 않아야 합니다. 테스트는 `storageProfile`을 확인하여 `imageReference`에서 _preview_ 가 포함된 문자열을 사용하지 않는지 확인합니다. 그리고 해당 _preview_ 는 `imageReference` 속성, `offer`, `sku` 또는 `version`에서 사용되지 않습니다.

`imageReference` 속성에 대한 자세한 내용은 [Microsoft.Compute virtualMachines](/azure/templates/microsoft.compute/virtualmachines#imagereference-object) 및 [Microsoft.Compute virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets#imagereference-object)를 참조하세요.

`imageReference`가 _preview_ 를 포함한 문자열이므로 다음 예제는 **실패** 합니다.

```json
"properties": {
  "storageProfile": {
    "imageReference": "latest-preview"
  }
}
```

_preview_ 가 `offer`, `sku` 또는 `version`에서 사용되면 다음 예제는 **실패** 합니다.

```json
"properties": {
  "storageProfile": {
    "imageReference": {
      "publisher": "Canonical",
      "offer": "UbuntuServer_preview",
      "sku": "16.04-LTS-preview",
      "version": "preview"
    }
  }
}
```

다음 예제는 **통과** 합니다.

```json
"storageProfile": {
  "imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
  }
}
```

## <a name="dont-use-managedidentity-extension"></a>ManagedIdentity 확장 사용 안 함

테스트 이름: **ManagedIdentityExtension를 사용하지 않아야 함**

`ManagedIdentity` 확장을 가상 머신에 적용하지 마세요. 확장은 2019에서 사용되지 않으므로 더 이상 사용할 수 없습니다.

## <a name="outputs-cant-include-secrets"></a>출력에는 비밀을 포함할 수 없음

테스트 이름: **출력은 비밀을 포함할 수 없음**

잠재적으로 비밀을 공개할 수 있는 값을 `outputs` 섹션에 포함하지 마세요. 예를 들어 `secureString` 또는 `secureObject` 형식의 보안 매개 변수 또는 `listKeys`와 같은 [list*](template-functions-resource.md#list) 함수입니다.

템플릿의 출력은 배포 기록에 저장되므로 악의적인 사용자가 해당 정보를 찾을 수 있습니다.

출력 값에 보안 매개 변수를 포함하고 있으므로 다음 예제는 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "secureParam": {
      "type": "secureString"
    }
  },
  "functions": [],
  "variables": {},
  "resources": [],
  "outputs": {
    "badResult": {
      "type": "string",
      "value": "[concat('this is the value ', parameters('secureParam'))]"
    }
  }
}
```

다음 예제에서는 출력에서 [list *](template-functions-resource.md#list) 함수를 사용하기 때문에 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
      "type": "string"
    }
  },
  "functions": [],
  "variables": {},
  "resources": [],
  "outputs": {
    "badResult": {
      "type": "object",
      "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-02-01')]"
    }
  }
}
```

## <a name="use-protectedsettings-for-commandtoexecute-secrets"></a>commandToExecute 비밀에 protectedSettings 사용

테스트 이름: **commandToExecute는 비밀에 protectedSettings를 사용해야 함**

형식이 `CustomScript`인 리소스의 경우 암호와 같은 비밀 데이터가 `commandToExecute`에 포함되면 암호화된 `protectedSettings`를 사용합니다. 예를 들어 비밀 데이터는 `secureString` 또는 `secureObject` 형식의 보안 매개 변수, `listKeys`와 같은 [list*](template-functions-resource.md#list) 함수 또는 사용자 지정 스크립트에 사용할 수 있습니다.

일반 텍스트를 사용하므로 `settings` 개체에서 비밀 데이터를 사용하지 마세요. 자세한 내용은 [Microsoft.Compute virtualMachines/extensions](/azure/templates/microsoft.compute/virtualmachines/extensions), [Windows](
/azure/virtual-machines/extensions/custom-script-windows) 또는 [Linux](../../virtual-machines/extensions/custom-script-linux.md)를 참조하세요.

`settings`에서 보안 매개 변수와 함께 `commandToExecute`를 사용하므로 다음 예제는 **실패** 합니다.

```json
"parameters": {
  "adminPassword": {
    "type": "secureString"
  }
}
...
"properties": {
  "type": "CustomScript",
  "settings": {
    "commandToExecute": "[parameters('adminPassword')]"
  }
}
```

`settings`에서 `listKeys` 함수와 함께 `commandToExecute`를 사용하므로 다음 예제는 **실패** 합니다.

```json
"properties": {
  "type": "CustomScript",
  "settings": {
    "commandToExecute": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-02-01')]"
  }
}
```

`protectedSettings`에서 보안 매개변수와 함께 `commandToExecute`를 사용하므로 다음 예제는 **통과** 합니다.

```json
"parameters": {
  "adminPassword": {
    "type": "secureString"
  }
}
...
"properties": {
  "type": "CustomScript",
  "protectedSettings": {
    "commandToExecute": "[parameters('adminPassword')]"
  }
}
```

`protectedSettings`에서 `listKeys` 함수와 함께 `commandToExecute`를 사용하므로 다음 예제는 **통과** 합니다.

```json
"properties": {
  "type": "CustomScript",
  "protectedSettings": {
    "commandToExecute": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-02-01')]"
  }
}
```

## <a name="use-recent-api-versions-in-reference-functions"></a>reference 함수에서 최신 API 버전 사용

테스트 이름: **apiVersions는 reference 함수에서 최신 버전이어야 함**

[reference](template-functions-resource.md#reference) 함수에 사용되는 API 버전은 미리 보기 버전이 아닌 최신 버전이어야 합니다. 이 테스트는 도구 키트 캐시에 있는 리소스 공급자 버전과 비교하여 템플릿의 API 버전을 평가합니다. 테스트가 실행된 날짜로부터 2년 미만의 API 버전은 최신 버전으로 간주됩니다.

API 버전을 찾을 수 없다는 경고는 해당 버전이 도구 키트의 캐시에 포함되어 있지 않음을 나타냅니다. 권장되는 최신 버전의 API를 사용하면 경고를 생성할 수 있습니다.

[도구 키트 캐시](https://github.com/Azure/arm-ttk/tree/master/arm-ttk/cache)에 대해 자세히 알아보세요.

API 버전이 2년을 초과한 이전 버전이므로 다음 예제는 **실패** 합니다.

```json
"outputs": {
  "stgAcct": {
    "type": "string",
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01')]"
  }
}
```

API 버전이 미리 보기 버전이므로 다음 예제는 **실패** 합니다.

```json
"outputs": {
  "stgAcct": {
    "type": "string",
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2020-08-01-preview')]"
  }
}
```

API 버전이 2년 미만의 이전 버전이고 미리 보기 버전이 아니므로 다음 예제는 **통과** 합니다.

```json
"outputs": {
  "stgAcct": {
    "type": "string",
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2021-02-01')]"
  }
}
```

## <a name="use-type-and-name-in-resourceid-functions"></a>resourceId 함수에서 형식 및 이름 사용

테스트 이름: **리소스가 모호하지 않아야 함**

이 테스트는 사용하지 않도록 설정되어 있지만 출력에 통과한 것으로 표시됩니다. 템플릿에서 다음 조건을 확인하는 것이 가장 좋습니다.

[resourceId](template-functions-resource.md#resourceid)에는 리소스 종류와 리소스 이름이 포함되어야 합니다. 이 테스트는 모든 템플릿의 `resourceId` 함수를 찾고, 리소스가 템플릿에서 올바른 구문으로 사용되는지 확인합니다. 그렇지 않으면 함수가 모호한 것으로 간주됩니다.

예를 들어 다음과 같은 경우 `resourceId` 함수는 모호한 것으로 간주됩니다.

- 템플릿에 리소스가 없고 리소스 그룹이 지정되지 않은 경우
- 리소스에 조건이 포함되어 있고 리소스 그룹이 지정되지 않은 경우
- 관련 리소스에 이름 세그먼트의 일부만 포함된 경우. 예를 들어 자식 리소스에 둘 이상의 이름 세그먼트가 포함되어 있습니다. 자세한 내용은 [resourceId 설명](template-functions-resource.md#remarks-3)을 참조하세요.

## <a name="use-inner-scope-for-nested-deployment-secure-parameters"></a>중첩된 배포 보안 매개 변수에 내부 범위 사용

테스트 이름: **중첩된 배포의 보안 매개 변수**

`inner` 범위가 있는 중첩된 템플릿의 `expressionEvaluationOptions` 개체를 사용하여 `secureString` 또는 `secureObject` 형식의 보안 매개 변수 또는 `listKeys`와 같은 [list*](template-functions-resource.md#list) 함수가 포함된 식을 평가합니다. `outer` 범위를 사용하는 경우 식은 부모 템플릿의 범위 내에서 일반 텍스트로 평가됩니다. 그러면 배포 기록에 액세스할 수 있는 모든 사용자에게 보안 값이 표시됩니다. `expressionEvaluationOptions` 의 기본값은 `outer`입니다.

중첩된 템플릿에 대한 자세한 내용은 [Microsoft.Resources deployments](/azure/templates/microsoft.resources/deployments) 및 [중첩된 템플릿의 식 계산 범위](linked-templates.md#expression-evaluation-scope-in-nested-templates)를 참조하세요.

`expressionEvaluationOptions`에서 `outer` 범위를 사용하여 보안 매개 변수 또는 `list*` 함수를 평가하므로 다음 예제는 **실패** 합니다.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2021-04-01",
    "name": "nestedTemplate",
    "properties": {
      "expressionEvaluationOptions": {
        "scope": "outer"
      }
    }
  }
]
```

`expressionEvaluationOptions`에서 `inner` 범위를 사용하여 보안 매개 변수 또는 `list*` 함수를 평가하므로 다음 예제는 **통과** 합니다.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2021-04-01",
    "name": "nestedTemplate",
    "properties": {
      "expressionEvaluationOptions": {
        "scope": "inner"
      }
    }
  }
]
```

## <a name="next-steps"></a>다음 단계

- 테스트 도구 키트를 실행하는 방법에 대한 자세한 내용은 [ARM 템플릿 테스트 도구 키트 사용](test-toolkit.md)을 참조하세요.
- 테스트 도구 키트를 사용하는 Microsoft Learn 모듈은 [변경 내용 미리 보기 및 ARM 템플릿 테스트 도구 키트와 가상 분석을 사용한 Azure 리소스 유효성 검사](/learn/modules/arm-template-test/)를 참조하세요.
- 매개 변수 파일을 테스트하려면 [매개 변수 파일에 대한 테스트 사례](parameters.md)를 참조하세요.
- createUiDefinition 테스트는 [createUiDefinition.json에 대한 테스트 사례](createUiDefinition-test-cases.md)를 참조하세요.
- 모든 파일을 테스트하는 방법을 알아보려면 [모든 파일에 대한 테스트 사례](all-files-test-cases.md)를 참조하세요.
