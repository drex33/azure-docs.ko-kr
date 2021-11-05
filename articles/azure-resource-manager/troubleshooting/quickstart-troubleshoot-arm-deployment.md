---
title: ARM 템플릿 배포 문제 해결을 참조하세요.
description: ARM 템플릿(Azure Resource Manager 템플릿) 배포를 모니터링하고 문제를 해결하는 방법에 대해 알아봅니다. 활동 로그 및 배포 기록을 표시합니다.
ms.date: 11/01/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b2dae6d4f90410b8ce1f8fc455aa95ede0ab0d3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053617"
---
# <a name="quickstart-troubleshoot-arm-template-deployments"></a>빠른 시작: ARM 템플릿 배포 문제 해결

이 빠른 시작은 ARM 템플릿(Azure Resource Manager 템플릿) 배포 오류 문제를 해결하는 방법에 대해 설명합니다. 두 오류를 사용하여 템플릿을 설정하고 활동 로그 및 배포 기록을 사용하여 오류를 해결하는 방법을 알아봅니다.

템플릿 배포와 관련된 오류는 다음과 같은 두 가지 유형이 있습니다.

- **검증 오류** 는 배포가 시작되기 전에 발생하며 파일의 구문 오류로 인해 발생합니다.
- **배포 오류** 는 배포 프로세스 중에 발생하며 API 버전과 같은 잘못된 값으로 인해 발생할 수 있습니다.

두 가지 오류 유형에서 배포 문제를 해결하는 데 사용하는 오류 코드를 반환합니다. 두 가지 오류 유형 모두 활동 로그에 나타납니다. 배포가 시작된 것은 아니므로 유효성 검사 오류는 배포 기록에 나타나지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 다음 항목이 필요합니다.

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
- 최신 [Azure Resource Manager 도구 확장](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)이 있는 [Visual Studio Code](https://code.visualstudio.com/).
- [Azure Cloud Shell](../templates/deploy-cloud-shell.md#deploy-local-template)을 사용하여 로컬 템플릿을 배포하는 방법을 검토합니다.

## <a name="create-a-template-with-errors"></a>오류가 있는 템플릿 만들기

다음 템플릿에는 유효성 검사 오류 및 배포 오류가 포함되어 있습니다. 스토리지 계정을 배포할 수 있도록 템플릿 오류를 해결하 고 수정합니다.

두 샘플 템플릿의 오류는 다음 줄에 있습니다.

```json
"apiVersion1": "2018-07-02",
```

1. Visual Studio Code를 열고 **파일** > **새 파일** 을 선택합니다.
1. 템플릿을 복사하려면 **복사** 를 선택하고 새 파일에 붙여넣습니다.
1. **파일** > **저장하기** 를 선택하여 파일을 로컬 컴퓨터에 _azuredeploy.json_ 의 이름으로 저장합니다.

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
      "apiVersion1": "2018-07-02",
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

## <a name="deploy-the-template"></a>템플릿 배포

템플릿을 Cloud Shell에 업로드하고 Azure CLI 또는 Azure PowerShell을 사용하여 배포합니다.

1. [Cloud Shell](https://shell.azure.com)에 로그인합니다.
1. 왼쪽 위 모서리에서 **Bash** 또는 **PowerShell** 을 선택합니다.

    :::image type="content" source="media/quickstart-troubleshoot-arm-deployment/cloud-shell-upload.png" alt-text="셸을 선택하고 파일을 업로드하는 Azure Cloud Shell의 스크린샷.":::

1. **파일 업로드/다운로드** 를 선택하고 _azuredeploy.json_ 파일을 업로드합니다.
1. 템플릿을 배포하려면 다음 명령을 복사하여 셸 창에 붙여넣습니다.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter a resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    New-AzResourceGroup -Name "$resourceGroupName" -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    Write-Host "Press [ENTER] to continue ..."
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    echo "Enter a resource group name:" &&
    read resourceGroupName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    az group create --name $resourceGroupName --location $location &&
    az deployment group create --resource-group $resourceGroupName --template-file $HOME/azuredeploy.json &&
    echo "Press [ENTER] to continue ..."
    ```

    ---

    > [!NOTE]
    > 배포를 실행할 때와 동일한 리소스 그룹 이름 및 위치를 사용합니다. 리소스 그룹을 업데이트하라는 메시지가 표시되면 **예** 를 선택합니다.

## <a name="troubleshoot-the-validation-error"></a>유효성 검사 오류 문제 해결

유효성 검사 오류는 오류 코드와 함께 셸에 오류 메시지를 표시합니다 `InvalidRequestContent`.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```Output
New-AzResourceGroupDeployment: 6:49:10 PM - Error: Code=InvalidRequestContent; Message=The request content
was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type
'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 34, position 24.'.

New-AzResourceGroupDeployment: The deployment validation failed
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```Output
{"error":{"code":"InvalidRequestContent","message":"The request content was invalid and could not be
deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'.
Path 'properties.template.resources[0].apiVersion1', line 32, position 20.'."}}
```

---

### <a name="fix-the-validation-error"></a>유효성 검사 오류 수정

`apiVersion1`가 올바르지 않기 때문에 오류가 발생했습니다. Visual Studio Code를 사용하여 `apiVersion1`를 `apiVersion`로 변경하고 템플릿을 저장합니다. [템플릿 배포](#deploy-the-template)의 단계에 따라 템플릿을 업로드 하 고 배포를 다시 실행합니다.

## <a name="troubleshoot-the-deployment-error"></a>배포 오류 문제 해결

배포 오류는 오류 코드와 함께 셸에 오류 메시지를 표시 합니다 `NoRegisteredProviderFound`. 리소스 그룹의 **배포** 및 **활동 로그** 에서 오류를 볼 수도 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```Output
New-AzResourceGroupDeployment: 6:51:48 PM - The deployment 'azuredeploy' failed with error(s).
Showing 1 out of 1 error(s).
Status Message: No registered resource provider found for location 'centralus' and API version '2018-07-02'
for type 'storageAccounts'. The supported api-versions are '2021-06-01, 2021-05-01, 2021-04-01, 2021-02-01,
2021-01-01, 2020-08-01-preview, 2019-06-01, 2019-04-01, 2018-11-01, 2018-07-01, 2018-03-01-preview,
2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'.
The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast,
japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast,
australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus,
uksouth, ukwest, koreacentral, koreasouth, francecentral, australiacentral, southafricanorth, uaenorth,
switzerlandnorth, germanywestcentral, norwayeast, westus3, jioindiawest'.
(Code:NoRegisteredProviderFound)
CorrelationId: 11111111-1111-1111-1111-111111111111
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```Output
{"status":"Failed","error":{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage
details.", "details":[{"code":"BadRequest","message":"{\r\n  \"error\": {\r\n
\"code\": \"NoRegisteredProviderFound\", \r\n    \"message\": \"No registered resource provider found for
location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions
are '2021-06-01, 2021-05-01, 2021-04-01, 2021-02-01, 2021-01-01, 2020-08-01-preview, 2019-06-01,
2019-04-01, 2018-11-01, 2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01,
2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'.
The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast,
japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast,
australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus,
uksouth, ukwest, koreacentral, koreasouth, francecentral, australiacentral, southafricanorth, uaenorth,
switzerlandnorth, germanywestcentral, norwayeast, westus3, jioindiawest'.\"\r\n  }\r\n}"}]}}
```

---

### <a name="deployments"></a>배포

다음 단계를 사용하여 Azure Portal에서 배포 오류를 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 검색 상자에 _리소스 그룹_ 을 입력하고 **리소스 그룹** 을 선택합니다.

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/search-box.png" alt-text="Azure Portal 검색 창의 스크린샷.":::

1. 배포의 리소스 그룹 이름을 선택합니다.
1. **개요** 로 이동하여 링크 **1 실패** 를 선택합니다.

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-error.png" alt-text="실패한 배포를 강조 표시하는 스크린샷.":::

1. **배포** 에서 **오류 세부 정보** 를 선택합니다.

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-error-details.png" alt-text="오류 세부 정보에 대한 배포 실패 링크의 스크린샷.":::

    오류 메시지는 배포 명령의 출력과 동일합니다.

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-error-summary.png" alt-text="배포 오류의 세부 정보에 대한 스크린샷.":::

### <a name="activity-log"></a>활동 로그

리소스 그룹의 활동 로그에서 오류를 찾을 수도 있습니다. 활동 로그가 최신 배포 정보를 표시하는 데 몇 분 정도 소요됩니다.

1. 리소스 그룹에서 **활동 로그** 를 선택합니다.
1. 필터를 사용하여 로그를 찾고 보려는 로그를 선택합니다.

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-activity-log.png" alt-text="실패한 배포를 강조 표시하는 리소스 그룹의 활동 로그 스크린샷.":::

1. 로그를 선택하면 세부 정보가 표시됩니다.

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-activity-log-details.png" alt-text="실패한 배포의 오류 메시지를 보여주는 활동 로그 세부 정보의 스크린샷.":::

### <a name="fix-the-deployment-error"></a>배포 오류 수정

잘못된 API 버전으로 인해 **등록된 리소스 공급자를 찾을 수 없는** 배포 오류가 발생했습니다. Visual Studio Code를 사용하여 API 버전을 `2021-04-01`와 같은 유효한 값으로 변경하고 템플릿을 저장 합니다. [템플릿 배포](#deploy-the-template)의 단계에 따라 템플릿을 업로드 하 고 배포를 다시 실행합니다.

유효성 검사 및 배포 오류를 해결한 후 스토리지 계정이 만들어집니다. 리소스 그룹의 **개요** 로 이동하여 리소스를 봅니다. **배포** 및 **활동 로그** 에 성공적인 배포가 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않으면 리소스 그룹을 삭제합니다. Cloud Shell 또는 포털에서 리소스 그룹을 삭제할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

꺾쇠 괄호를 포함하여 `<rgname>`을 리소스 그룹 이름으로 바꿉니다.

```azurepowershell
Remove-AzResourceGroup -Name <rgname>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

꺾쇠 괄호를 포함하여 `<rgname>`을 리소스 그룹 이름으로 바꿉니다.

```azurecli
az group delete --name <rgname>
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
