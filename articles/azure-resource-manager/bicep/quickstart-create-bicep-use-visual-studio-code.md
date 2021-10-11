---
title: Bicep 파일 만들기 - Visual Studio Code
description: Azure 리소스 배포를 위해 Visual Studio Code 및 Bicep 파일에 대한 Bicep 확장 사용
ms.date: 10/01/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 95e7a601da5c8a9976d2341c2964df0f4d9718d0
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354955"
---
# <a name="quickstart-create-bicep-files-with-visual-studio-code"></a>빠른 시작: Visual Studio Code를 사용하여 Bicep 파일 만들기

이 빠른 시작은 Visual Studio Code에서 [Bicep 파일](overview.md)을 만드는 단계를 안내합니다. 스토리지 계정 및 가상 네트워크를 만듭니다. 또한 Bicep 확장을 통해 형식 안전성, 구문 유효성 검사 및 자동 완성을 제공하여 개발을 간소화하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

Bicep 개발 환경을 설정하려면 [Bicep 도구 설치](install.md)를 참조하세요. 해당 단계를 완료한 후 [Visual Studio Code](https://code.visualstudio.com/) 및 [Bicep 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)을 사용할 수 있습니다. 최신 [Azure CLI](/cli/azure/) 또는 최신 [Azure PowerShell 모듈](/powershell/azure/new-azureps-module-az)을 사용할 수도 있습니다.

## <a name="add-resource-snippet"></a>리소스 코드 조각 추가

Visual Studio Code를 시작하고 *main.bicep* 라는 새 파일을 만듭니다.

Bicep 확장이 포함된 VS Code는 미리 정의된 코드 조각을 제공하여 개발을 간소화합니다. 이 빠른 시작에서는 가상 네트워크를 만드는 코드 조각을 추가합니다.

*main.bicep* 에 **vnet** 를 입력합니다. 목록에서 **res-vnet** 를 선택한 다음, Tab 키 또는 Enter 키를 선택합니다.

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/add-snippet.png" alt-text="가상 네트워크에 대한 코드 조각 추가":::

> [!TIP]
> VS Code에 이러한 intellisense 옵션이 표시되지 않는 경우 [필수 구성 요소](#prerequisites)에 지정된 대로 Bicep 확장을 설치했는지 확인합니다. 확장을 설치한 경우 Bicep 파일을 연 후 Bicep 언어 서비스를 시작할 시간을 제공합니다. 일반적으로 빠르게 시작되지만 시작할 때까지 intellisense 옵션이 없습니다. 오른쪽 아래 모서리에 있는 알림은 서비스가 시작 중임을 나타냅니다. 해당 알림이 사라지면 서비스가 실행 중인 것입니다.

이제 Bicep 파일에 다음 코드가 포함됩니다.

```bicep
resource virtualNetwork 'Microsoft.Network/virtualNetworks@2019-11-01' = {
  name: 'name'
  location: resourceGroup().location
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.0.0.0/16'
      ]
    }
    subnets: [
      {
        name: 'Subnet-1'
        properties: {
          addressPrefix: '10.0.0.0/24'
        }
      }
      {
        name: 'Subnet-2'
        properties: {
          addressPrefix: '10.0.1.0/24'
        }
      }
    ]
  }
}
```

이 코드 조각에는 가상 네트워크를 정의하는 데 필요한 모든 값이 포함되어 있습니다. 그러나 요구 사항에 맞게 코드를 수정할 수 있습니다. 예를 들어, `name`은 가상 네트워크의 좋은 이름이 아닙니다. `name` 속성을 `examplevnet`로 변경합니다.

```bicep
name: 'examplevnet'
```

이 Bicep 파일을 배포할 수 있지만 배포하기 전에 매개 변수 및 스토리지 계정을 추가합니다.

## <a name="add-parameter"></a>매개 변수 추가

이제 스토리지 계정 이름에 사용할 매개 변수를 추가합니다. 가상 네트워크 위에 몇 줄을 추가하고 다음을 입력합니다.

```bicep
param storageName
```

**storageName** 뒤에 공백을 추가하면 Intellisense는 매개 변수에 사용할 수 있는 데이터 형식을 제안합니다.

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/add-param.png" alt-text="매개 변수에 문자열 형식 추가":::

**문자열** 을 선택합니다.

다음 매개 변수를 사용합니다.

```bicep
param storageName string
```

이 매개 변수는 정상적으로 작동하지만 스토리지 계정에는 이름 길이의 제한이 있습니다. 이름에는 3~24자를 사용해야 합니다. 매개 변수에 데코레이터를 추가하여 관련 요구 사항을 지정할 수 있습니다.

매개 변수 위에 줄을 추가하고 **@** 을 입력합니다. 사용 가능한 데코레이터가 표시됩니다. **minLength** 및 **maxLength** 의 데코레이터가 둘 다 있습니다.

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/add-decorators.png" alt-text="매개 변수에 데코레이터 추가":::

데코레이터를 둘 다 추가하고 아래와 같이 문자 제한을 지정합니다.

```bicep
@minLength(3)
@maxLength(24)
param storageName string
```

매개 변수의 설명을 추가할 수도 있습니다. Bicep 파일을 배포하는 사람이 제공할 값을 이해하는 데 도움이 되는 정보를 포함합니다.

```bicep
@minLength(3)
@maxLength(24)
@description('Provide a name for the storage account. Use only lower case letters and numbers. The name must be unique across Azure.')
param storageName string
```

매개 변수를 사용할 준비가 되었습니다.

## <a name="add-resource"></a>리소스 추가

스토리지 계정을 정의하는 코드 조각을 사용하는 대신 Intellisense를 사용하여 값을 설정합니다. Intellisense를 사용하면 값을 수동으로 입력하는 것보다 훨씬 더 쉽게 이 단계를 수행할 수 있습니다.

리소스를 정의하려면 `resource` 키워드를 사용합니다.  가상 네트워크 아래에 **resource exampleStorage** 를 입력합니다.

```bicep
resource exampleStorage
```

**exampleStorage** 는 배포 중인 리소스의 기호 이름입니다. 이 이름을 사용하여 Bicep 파일의 다른 부분에 있는 리소스를 참조할 수 있습니다.

기호 이름 뒤에 공백을 추가하면 리소스 종류 목록이 표시됩니다. 사용 가능한 옵션에서 선택할 수 있을 때까지 **스토리지** 를 계속 입력합니다.

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/select-resource-type.png" alt-text="리소스 종류의 스토리지 계정 선택":::

**Microsoft.Storage/storageAccounts** 를 선택한 후 사용 가능한 API 버전이 제공됩니다. 최신 버전을 선택합니다.

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/select-api-version.png" alt-text="리소스 종류의 API 버전 선택":::

리소스 종류의 작은따옴표 뒤에 **=** 및 공백을 추가합니다. 리소스에 속성을 추가할 수 있는 옵션이 제공됩니다. **required-properties** 를 선택합니다.

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/select-required-properties.png" alt-text="필수 속성 추가":::

이 옵션은 배포에 필요한 리소스 종류의 모든 속성을 추가합니다. 이 옵션을 선택하면 스토리지 계정에 다음 속성이 포함됩니다.

```bicep
resource exampleStorage 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name:
  location:
  sku: {
    name:
  }
  kind:

}
```

거의 완료되었습니다. 해당 속성의 값을 제공하면 됩니다.

다시, Intellisense가 도움이 됩니다. 스토리지 계정의 이름을 포함하는 매개 변수인 `name`을 `storageName`으로 설정합니다. `location`의 경우 `eastus`로 설정합니다. SKU 이름 및 종류를 추가할 때 Intellisense는 유효한 옵션을 제공합니다.

완료되면 다음을 사용할 수 있습니다.

```bicep
@minLength(3)
@maxLength(24)
param storageName string

resource virtualNetwork 'Microsoft.Network/virtualNetworks@2019-11-01' = {
  name: 'examplevnet'
  location: resourceGroup().location
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.0.0.0/16'
      ]
    }
    subnets: [
      {
        name: 'Subnet-1'
        properties: {
          addressPrefix: '10.0.0.0/24'
        }
      }
      {
        name: 'Subnet-2'
        properties: {
          addressPrefix: '10.0.1.0/24'
        }
      }
    ]
  }
}

resource exampleStorage 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: storageName
  location: 'eastus'
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
}
```

Bicep 구문에 관한 자세한 내용은 [Bicep 구조](./file.md)를 참조하세요.

## <a name="visualize-resources"></a>리소스 시각화

파일의 리소스 표현을 볼 수 있습니다.

왼쪽 위 모퉁이에서 시각화 도우미 단추를 선택하여 Bicep 시각화 도우미를 엽니다.

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/bicep-visualizer.png" alt-text="Bicep 시각화 도우미":::

시각화 도우미는 리소스 종속성 정보와 함께 Bicep 파일에 정의된 리소스를 보여 줍니다. 이 빠른 시작에 정의된 두 리소스는 종속성 관계가 없으므로 두 리소스 간에 커넥터가 표시되지 않습니다.

## <a name="deploy-the-bicep-file"></a>Bicep 파일 배포

만든 파일을 배포하려면 PowerShell 또는 Azure CLI를 엽니다. 통합된 Visual Studio Code 터미널을 사용하려면 `ctrl` + ```` ` ```` 키 조합을 선택합니다. 현재 디렉터리를 Bicep 파일이 있는 위치로 변경합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name exampleRG --location eastus

az deployment group create --resource-group exampleRG --template-file main.bicep --parameters storageName={your-unique-name}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name exampleRG -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName exampleRG -TemplateFile ./main.bicep -storageName "{your-unique-name}"
```

---

> [!NOTE]
> 중괄호를 포함하여 **{your-unique-name}** 을 고유한 스토리지 계정 이름으로 바꿉니다.

배포가 완료되면 배포에 성공했음을 나타내는 메시지가 표시됩니다. 스토리지 계정이 이미 사용되었음을 나타내는 오류 메시지가 표시되면 입력한 스토리지 이름이 사용 중입니다. 고유할 가능성이 높은 이름을 제공합니다.

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요 없으면 Azure CLI 또는 Azure PowerShell 모듈을 사용하여 빠른 시작 리소스 그룹을 삭제합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name exampleRG
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name exampleRG
```

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Microsoft Learn의 Bicep](learn-bicep.md)
