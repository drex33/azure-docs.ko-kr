---
title: ARM 템플릿 JSON을 Bicep으로 디컴파일
description: Azure Resource Manager 템플릿을 Bicep 파일로 디컴파일하는 명령을 설명합니다.
ms.topic: conceptual
ms.date: 11/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 807a2090d50822c75d57c4dd9120cdacf618eff2
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132934993"
---
# <a name="decompiling-arm-template-json-to-bicep"></a>ARM 템플릿 JSON을 Bicep으로 디컴파일링

이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 Bicep 파일로 디컴파일하는 방법을 설명합니다. 변환 명령을 실행하려면 [Bicep CLI 설치](./install.md)가 되어 있어야 합니다.

ARM 템플릿을 디컴파일하면 Bicep 개발을 시작할 수 있습니다. ARM 템플릿 라이브러리가 있고 향후 개발을 위해 Bicep을 사용하려는 경우 Bicep으로 디컴파일할 수 있습니다. 그러나 Bicep 파일은 Bicep에 대한 모범 사례를 구현하기 위해 수정이 필요할 수 있습니다.

이 문서에서는 Azure CLI에서 `decompile` 명령을 실행하는 방법을 보여줍니다. Azure CLI를 사용하지 않는 경우 명령을 시작할 때 `az` 없이 명령을 실행합니다. 예를 들어 `az bicep decompile`는 ``bicep decompile``가 됩니다.

## <a name="decompile-from-json-to-bicep"></a>JSON에서 Bicep으로 디컴파일

ARM 템플릿 JSON을 Bicep으로 디컴파일하려면 다음을 사용합니다.

```azurecli
az bicep decompile --file main.json
```

이 명령은 ARM 템플릿과 동일한 디렉터리에 _main.bicep_ 이라는 파일을 만듭니다.

> [!CAUTION]
> 디컴파일이 파일을 변환하려고 하지만 ARM 템플릿 JSON에서 Bicep으로의 매핑이 보장되지 않습니다. 생성된 Bicep 파일에서 경고 및 오류를 수정해야 할 수 있습니다. 또는 정확한 변환을 수행할 수 없는 경우 디컴파일이 실패할 수 있습니다. 문제 또는 부정확한 변환을 보고하려면 [문제를 만듭니다](https://github.com/Azure/bicep/issues).

디컴파일 및 [빌드](bicep-cli.md#build) 명령은 기능적으로 동일한 템플릿을 생성합니다. 그러나 구현은 정확하게 일치하지 않을 수 있습니다. 템플릿을 JSON에서 Bicep으로 변환한 다음 다시 JSON으로 변환하면 원래 템플릿과 구문이 다른 템플릿이 생성될 수 있습니다. 배포되었을 때, 변환된 템플릿은 동일한 결과를 생성합니다.

## <a name="fix-conversion-issues"></a>변환 문제 수정

다음 ARM 템플릿이 있다고 가정합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

디컴파일하면 다음과 같은 결과가 나타납니다.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
@description('Storage Account type')
param storageAccountType string = 'Standard_LRS'

@description('Location for all resources.')
param location string = resourceGroup().location

var storageAccountName_var = 'store${uniqueString(resourceGroup().id)}'

resource storageAccountName 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName_var
  location: location
  sku: {
    name: storageAccountType
  }
  kind: 'StorageV2'
  properties: {}
}

output storageAccountName string = storageAccountName_var
```

디컴파일된 파일은 작동하지만 일부 이름은 변경해야 할 수도 있습니다. 변수 `var storageAccountName_var`에는 비정상적인 명명 규칙이 있습니다. 다음과 같이 변경해 보겠습니다.

```bicep
var uniqueStorageName = 'store${uniqueString(resourceGroup().id)}'
```

리소스에는 변경하려는 기호 이름이 있습니다. 기호 이름에 `storageAccountName` 대신 `exampleStorage`를 사용합니다.

```bicep
resource exampleStorage 'Microsoft.Storage/storageAccounts@2019-06-01' = {
```

스토리지 계정 이름에 대한 변수 이름을 변경한 후에는 사용되는 위치를 변경해야 합니다.

```bicep
resource exampleStorage 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: uniqueStorageName
```

그리고 출력에서 다음을 사용합니다.

```bicep
output storageAccountName string = uniqueStorageName
```

전체 파일은 다음과 같습니다.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
@description('Storage Account type')
param storageAccountType string = 'Standard_LRS'

@description('Location for all resources.')
param location string = resourceGroup().location

var uniqueStorageName = 'store${uniqueString(resourceGroup().id)}'

resource exampleStorage 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageAccountType
  }
  kind: 'StorageV2'
  properties: {}
}

output storageAccountName string = uniqueStorageName
```

## <a name="export-template-and-convert"></a>템플릿 내보내기 및 변환

리소스 그룹의 템플릿을 내보낸 다음, 템플릿을 `decompile` 명령에 직접 전달할 수 있습니다. 다음 예시에서는 내보낸 템플릿을 디컴파일하는 방법을 보여 줍니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
az bicep decompile --file main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[포털](#tab/azure-portal)

포털을 통해 [템플릿 내보내기](../templates/export-template-portal.md).

다운로드한 파일에서 `bicep decompile <filename>`를 엽니다.

---

## <a name="next-steps"></a>다음 단계

모든 Bicep CLI 명령에 대해 알아보려면 [Bicep CLI 명령](bicep-cli.md)을 참조하세요.
