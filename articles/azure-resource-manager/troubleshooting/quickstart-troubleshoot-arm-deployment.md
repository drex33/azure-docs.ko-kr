---
title: ARM 템플릿 배포 문제 해결을 참조하세요.
description: ARM 템플릿(Azure Resource Manager 템플릿) 배포 오류 문제를 해결하는 방법에 대해 알아봅니다.
ms.date: 11/04/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 561fab9d7a94bb4eb97d59fcae835dd8cc72a778
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849526"
---
# <a name="quickstart-troubleshoot-arm-template-deployments"></a>빠른 시작: ARM 템플릿 배포 문제 해결

이 빠른 시작은 ARM 템플릿(Azure Resource Manager 템플릿) 배포 오류 문제를 해결하는 방법에 대해 설명합니다. 오류가 있는 템플릿을 설정하고 오류를 해결하는 방법을 알아봅니다.

배포와 관련된 세 가지 유형의 오류가 있습니다.

- **검증 오류** 는 배포가 시작되기 전에 발생하며 파일의 구문 오류로 인해 발생합니다. 편집기에서 이러한 오류를 식별할 수 있습니다.
- 배포 명령이 실행되지만 리소스가 배포되지 않은 경우 **실행 전 유효성 검사 오류** 가 발생합니다. 이러한 오류는 배포를 시작하지 않고 발견됩니다. 예를 들어 매개 변수 값이 잘못된 경우 실행 전 유효성 검사에서 오류가 발견됩니다.
- **배포 오류** 는 배포 프로세스 중에 발생하며 배포 진행 상황을 평가해야만 찾을 수 있습니다.

모든 오류 유형에서 배포 문제를 해결하는 데 사용하는 오류 코드를 반환합니다. 유효성 검사 및 실행 전 오류는 활동 로그에 표시되지만 배포 기록에는 표시되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 다음 항목이 필요합니다.

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
- 최신 [Azure Resource Manager 도구 확장](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)이 있는 [Visual Studio Code](https://code.visualstudio.com/).
- 최신 버전의 [Azure PowerShell](/powershell/azure/install-az-ps) 또는 [Azure CLI](/cli/azure/install-azure-cli)를 설치합니다.

## <a name="create-a-template-with-errors"></a>오류가 있는 템플릿 만들기

다음 템플릿을 복사하여 로컬에 저장합니다. 이 파일을 사용하여 유효성 검사 오류, 실행 전 오류 및 배포 오류를 해결합니다. 이 빠른 시작에서는 파일 이름을 _troubleshoot.json_ 으로 지정했다고 가정하지만 아무 이름이나 지정할 수 있습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameterss": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "prefixName": {
      "type": "string"
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('prefixName'), uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-04-01",
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
    },
    "vnetResult": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'doesnotexist'), '2021-03-01', 'Full')]"
    }
  }
}
```

## <a name="fix-validation-error"></a>유효성 검사 오류 해결

Visual Studio Code에서 파일을 엽니다. 아래의 물결선은 `parameterss:` 오류를 나타냅니다. 오류 위로 마우스를 가져가면 유효성 검사 오류가 표시됩니다.

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/validation-error.png" alt-text="Visual Studio Code 템플릿 유효성 검사 오류의 스크린샷.":::

`variables` 및 `resources`에 _정의되지 않은 매개 변수 참조_ 에 대한 오류가 있음을 알 수 있습니다.

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/validation-undefined-parameter.png" alt-text="정의되지 않은 매개 변수 참조 오류를 보여 주는 Visual Studio Code 스크린샷.":::

모든 오류는 요소 이름의 철자가 잘못되어 발생합니다.

```json
"parameterss": {
```

오류 메시지 상태 _템플릿 유효성 검사 실패: '템플릿' 형식의 개체에서 멤버 '매개 변수'를 찾을 수 없습니다. 경로 '매개 변수', 줄 4, 위치 16_.

[매개 변수](../templates/syntax.md#parameters)에 대한 ARM 템플릿 구문은 `parameters`가 올바른 요소 이름임을 보여줍니다.

유효성 검사 오류 및 _정의되지 않은 매개 변수 참조_ 오류를 해결하려면 맞춤법을 수정하고 파일을 저장합니다.

```json
"parameters": {
```

## <a name="fix-preflight-error"></a>실행 전 오류 수정

실행 전 유효성 검사 오류를 만들려면 `prefixName` 매개 변수에 잘못된 값을 사용합니다.

이 빠른 시작에서는 리소스 그룹 이름에 _troubleshootRG_ 를 사용하지만 어떠한 이름도 사용할 수 있습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create --name troubleshootRG --location westus
az deployment group create \
  --resource-group troubleshootRG \
  --template-file troubleshoot.json \
  --parameters prefixName=long!!StoragePrefix
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name troubleshootRG -Location westus
New-AzResourceGroupDeployment `
  -ResourceGroupName troubleshootRG `
  -TemplateFile troubleshoot.json `
  -prefixName long!!StoragePrefix
```

---

템플릿이 실행 전 유효성 검사에 실패하고 배포가 실행되지 않습니다. `prefixName`는 11문자를 초과하며 특수 문자와 대문자를 포함합니다.

스토리지 이름은 3 ~ 24문자여야 하며, 소문자와 숫자만 포함할 수 있습니다. 접두사 값이 잘못된 스토리지 이름을 만들었습니다. 자세한 내용은 [스토리지 계정 이름 오류 해결](error-storage-account-name.md)을 참조하세요. 실행 전 오류를 해결하려면 11문자 이하 및 소문자 또는 숫자만 포함하는 접두사를 사용합니다.

배포가 실행되지 않았으므로 배포 기록이 없습니다.

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/preflight-no-deploy.png" alt-text="실행 전 오류에 대한 배포를 표시하지 않는 리소스 그룹 개요의 스크린샷.":::

활동 로그에 실행 전 오류가 표시됩니다. 로그를 선택하여 오류의 세부 정보를 확인합니다.

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/preflight-activity-log.png" alt-text="실행 전 오류가 있는 리소스 그룹 활동 로그의 스크린샷.":::

## <a name="fix-deployment-error"></a>배포 오류 수정

`storage`와 같은 유효한 접두사 값으로 배포를 실행합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create --name troubleshootRG --location westus
az deployment group create \
  --resource-group troubleshootRG \
  --template-file troubleshoot.json \
  --parameters prefixName=storage
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name troubleshootRG -Location westus
New-AzResourceGroupDeployment `
  -ResourceGroupName troubleshootRG `
  -TemplateFile troubleshoot.json `
  -prefixName storage
```

---

배포가 시작되고 배포 기록에 표시됩니다. `outputs`가 리소스 그룹에 존재하지 않는 가상 네트워크를 참조하므로 배포가 실패합니다. 그러나 스토리지 계정에 대한 오류가 없으므로 리소스가 배포되었습니다. 실패한 배포가 배포 기록에 표시됩니다.

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/deployment-failed.png" alt-text="실패한 배포를 보여 주는 리소스 그룹 개요의 스크린샷.":::

배포 오류를 해결하려면 유효한 리소스를 사용하도록 참조 함수를 변경합니다. 이 오류 코드에 대한 자세한 내용은 [리소스를 찾을 수 없음 오류 해결](error-not-found.md)을 참조하세요. 이 빠른 시작에서는 `vnetResult` 앞에 오는 쉼표와 모든 `vnetResult`를 삭제합니다. 파일을 저장하고 배포를 다시 실행합니다.

```json
"vnetResult": {
  "type": "object",
  "value": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'doesnotexist'), '2021-03-01', 'Full')]"
}
```

유효성 검사, 실행 전 및 배포 오류가 해결된 후 다음 템플릿은 스토리지 계정을 배포합니다. 배포 및 활동 로그에 성공적인 배포가 표시됩니다.

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
      ]
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "prefixName": {
      "type": "string"
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('prefixName'), uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-04-01",
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

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않으면 리소스 그룹을 삭제합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name troubleshootRG
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name troubleshootRG
```

---

포털에서 리소스 그룹을 삭제하려면 다음 단계를 따르세요.

1. Azure Portal의 검색 상자에 **리소스 그룹** 을 입력합니다.
1. **이름으로 필터링** 필드에 리소스 그룹 이름을 입력합니다.
1. 해당 리소스 그룹 이름을 선택합니다.
1. **리소스 그룹 삭제** 를 선택합니다.
1. 삭제를 확인하려면 리소스 그룹 이름을 입력하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 ARM 템플릿 배포 오류 문제를 해결하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [일반적인 Azure 배포 오류 문제 해결](common-deployment-errors.md)
