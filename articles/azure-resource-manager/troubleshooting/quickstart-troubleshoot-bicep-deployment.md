---
title: Bicep 파일 배포 문제 해결
description: Bicep 파일 배포를 모니터링하고 문제를 해결하는 방법을 알아봅니다. 활동 로그 및 배포 기록을 표시합니다.
ms.date: 10/26/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 44c98be9a0553d3e255b2272a3a8797bae61d3da
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101410"
---
# <a name="quickstart-troubleshoot-bicep-file-deployments"></a>빠른 시작: Bicep 파일 배포 문제 해결

이 빠른 시작에서는 Bicep 파일 배포 오류를 해결하는 방법을 설명합니다. 오류가 있는 파일을 만들고 오류를 수정하는 방법을 알아봅니다.

배포와 관련된 세 가지 유형의 오류가 있습니다.

- **검증 오류** 는 배포가 시작되기 전에 발생하며 파일의 구문 오류로 인해 발생합니다. 편집자는 이러한 오류를 포착할 수 있습니다.
- **실행 전 오류** 는 배포를 시작한 후 리소스가 배포되기 전에 발생합니다. 이러한 오류는 배포를 시작하지 않고 발견됩니다. 예를 들어 매개 변수 값이 잘못된 경우 실행 전 유효성 검사에서 오류가 발견됩니다.
- **배포 오류** 는 배포 프로세스 중에 발생하며 배포 진행 상황을 평가해야만 찾을 수 있습니다.

모든 오류 유형에서 배포 문제를 해결하는 데 사용하는 오류 코드를 반환합니다. 유효성 검사 및 실행 전 오류는 배포 기록에 표시되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 다음 항목이 필요합니다.

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
- 최신 [Bicep 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)이 포함된 [Visual Studio Code](https://code.visualstudio.com).
- 최신 버전의 [Azure PowerShell](/powershell/azure/install-az-ps) 또는 [Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-bicep-file-with-errors"></a>오류가 있는 Bicep 파일 만들기

다음 Bicep 파일을 복사하여 로컬에 저장합니다. 이 파일을 사용하여 유효성 검사 오류, 실행 전 오류 및 배포 오류를 해결합니다. 이 빠른 시작에서는 파일 이름을 **troubleshoot.bicep** 으로 지정했다고 가정하지만 아무 이름이나 지정할 수 있습니다.

```bicep
@description('SKU for the storage account')
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
parameter storageAccountType string = 'Standard_LRS'

@description('Prefix for storage name.')
param prefixName string

var storageAccountName = '${prefixName}${uniqueString(resourceGroup().id)}'

resource storageAccount 'Microsoft.Storage/storageAccounts@2021-06-01' = {
  name: storageAccountName
  location: resourceGroup().location
  sku: {
    name: storageAccountType
  }
  kind: 'StorageV2'
  properties: {}
}

resource existingVNet 'Microsoft.Network/virtualNetworks@2021-03-01' existing = {
  name: 'doesnotexist'
}

output storageAccountName string = storageAccountName
output vnetResult object = existingVNet
```

## <a name="fix-validation-error"></a>유효성 검사 오류 해결

Visual Studio Code에서 파일을 엽니다. Visual Studio Code가 구문 오류를 식별한다는 것을 알 수 있습니다. 첫 번째 매개 변수 선언은 오류를 나타내기 위해 빨간색 물결선으로 표시됩니다.

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/show-visual-studio-code-error.png" alt-text="구문 오류를 보여 주는 Visual Studio Code의 스크린샷.":::

오류로 표시된 줄은 다음과 같습니다.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
parameter storageAccountType string = 'Standard_LRS'
```

`parameter` 위로 마우스를 가져가면 오류 메시지가 표시됩니다.

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/declaration-not-recognized.png" alt-text="Visual Studio Code의 오류 메시지 스크린샷.":::

메시지 상태: "이 선언 유형을 인식할 수 없습니다. 매개 변수, 변수, 리소스 또는 출력 선언을 지정합니다." 이 파일을 배포하려고 하면 배포 명령에서 동일한 오류 메시지가 표시됩니다.

[매개 변수 선언](../bicep/parameters.md)에 대한 문서를 보면 키워드가 실제로 `param`임을 알 수 있습니다. 해당 구문을 변경하면 유효성 검사 오류가 사라집니다. `@allowed` 데코레이터도 오류로 표시되었지만 해당 오류는 매개 변수 선언을 변경하여 해결되기도 합니다. 데코레이터 다음에 매개 변수 선언이 필요하기 때문에 데코레이터가 오류로 표시되었습니다. 선언이 올바르지 않을 때 이 조건은 true가 아닙니다.

고정 줄은 다음과 같습니다.

```bicep
param storageAccountType string = 'Standard_LRS'
```

## <a name="fix-preflight-error"></a>실행 전 오류 수정

유효성 검사 오류를 수정했으므로 이제 파일을 배포할 차례입니다. 그러나 비행 전 오류를 보려면 잘못된 매개 변수 값을 제공해야 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create --name troubleshootRG --location westus
az deployment group create \
  --resource-group troubleshootRG \
  --template-file troubleshoot.bicep \
  --parameters prefixName=longNamewith!!Charactersthatarenotallowed
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name troubleshootRG -Location westus
New-AzResourceGroupDeployment `
  -ResourceGroupName troubleshootRG `
  -TemplateFile troubleshoot.bicep `
  -prefixName longNamewith!!Charactersthatarenotallowed
```

---

Azure Resource Manager는 스토리지 계정의 이름에 허용되지 않는 문자가 포함되어 있는지 확인합니다. 배포를 시도하지 않습니다. 

실행 전 유효성 검사가 실패했음을 나타내는 오류 메시지가 표시됩니다. 또한 스토리지 계정 이름의 길이는 3~24자여야 하고 숫자와 소문자만 사용해야 한다는 메시지가 표시됩니다. 제공한 접두사가 해당 요구 사항을 충족하지 않습니다. 이 오류 코드에 대한 자세한 내용은 [스토리지 계정 이름 오류 해결](error-storage-account-name.md)을 참조하세요.

실행 전 오류가 발견되었으므로 기록에 배포가 없습니다.

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/no-deployment.png" alt-text="기록에 배포가 없는 포털의 스크린샷.":::

그러나 실패한 배포는 활동 로그에 있습니다.

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/preflight-activity-log.png" alt-text="오류가 있는 활동 로그의 스크린샷.":::

로그 항목의 세부 정보를 열어 오류 메시지를 볼 수 있습니다.

## <a name="fix-deployment-error"></a>배포 오류 수정

파일을 다시 배포하고 이름 접두사 매개 변수에 허용되는 값을 제공합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create --name troubleshootRG --location westus
az deployment group create --resource-group troubleshootRG --template-file troubleshoot.bicep --parameters prefixName=stg
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name troubleshootRG -Location westus
New-AzResourceGroupDeployment -ResourceGroupName troubleshootRG -TemplateFile troubleshoot.bicep -prefixName stg
```

---

배포가 시작되지만 가상 네트워크를 찾을 수 없다는 메시지와 함께 실패합니다. 일반적으로 리소스에 대한 참조를 변경하여 이 오류를 수정합니다. 이 빠른 시작에서는 참조를 삭제합니다. 이 오류 코드에 대한 자세한 내용은 [리소스를 찾을 수 없음 오류 해결](error-not-found.md)을 참조하세요.

포털에서 배포가 기록에 나타납니다.

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/view-deployment-history.png" alt-text="포털의 배포 기록 스크린샷.":::

배포 기록에서 항목을 열어 오류에 대한 세부 정보를 얻을 수 있습니다. 활동 로그에도 오류가 있습니다.

Bicep 파일은 리소스 그룹에 존재하지 않는 가상 네트워크를 참조하려고 시도합니다. 기존 가상 네트워크에 대한 참조를 삭제하여 오류를 수정합니다.

```bicep
@description('SKU for the storage account')
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
parameter storageAccountType string = 'Standard_LRS'

@description('Prefix for storage name.')
param prefixName string

var storageAccountName = '${prefixName}${uniqueString(resourceGroup().id)}'

resource storageAccount 'Microsoft.Storage/storageAccounts@2021-06-01' = {
  name: storageAccountName
  location: resourceGroup().location
  sku: {
    name: storageAccountType
  }
  kind: 'StorageV2'
  properties: {}
}

output storageAccountName string = storageAccountName
```

오류 없이 해당 Bicep 파일을 배포할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않으면 리소스 그룹을 삭제합니다. Cloud Shell 또는 포털에서 리소스 그룹을 삭제할 수 있습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name troubleshootRG
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

꺾쇠 괄호를 포함하여 `<rgname>`을 리소스 그룹 이름으로 바꿉니다.

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

이 빠른 시작에서는 Bicep 파일 배포 오류를 해결하는 방법을 배웠습니다.

> [!div class="nextstepaction"]
> [일반적인 Azure 배포 오류 문제 해결](common-deployment-errors.md)
