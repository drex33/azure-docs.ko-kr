---
title: 템플릿 사양 만들기 및 배포
description: ARM 템플릿에서 템플릿 사양을 만드는 방법에 대해 알아봅니다. 그런 다음 구독에서 리소스 그룹에 템플릿 사양을 배포합니다.
author: tfitzmac
ms.date: 05/04/2021
ms.topic: quickstart
ms.author: tomfitz
ms.custom: mode-other
ms.openlocfilehash: 7004cf1eaf3c7ba2aa370ace0f2265367a51e60e
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133068869"
---
# <a name="quickstart-create-and-deploy-template-spec"></a>빠른 시작: 템플릿 사양 만들기 및 배포

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 [템플릿 사양](template-specs.md)으로 패키징하는 방법을 보여줍니다. 그런 다음, 해당 템플릿 사양을 배포합니다. 템플릿 사양에는 스토리지 계정을 배포하는 ARM 템플릿이 포함되어 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Azure PowerShell에서 템플릿 사양을 사용하려면 [버전 5.0.0 이상](/powershell/azure/install-az-ps)을 설치해야 합니다. Azure CLI에서 사용하려면 [버전 2.14.2 이상](/cli/azure/install-azure-cli)을 사용합니다.

## <a name="create-template"></a>템플릿 만들기

로컬 템플릿에서 템플릿 사양을 만듭니다. 다음 템플릿을 복사하고 **azuredeploy.json** 이라는 파일에 로컬로 저장합니다. 이 빠른 시작에서는 **c:\Templates\azuredeploy.json** 경로에 저장했지만 모든 경로를 사용할 수 있다고 가정합니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json":::

## <a name="create-template-spec"></a>템플릿 사양 만들기

템플릿 사양은 `Microsoft.Resources/templateSpecs`라는 리소스 형식입니다. 템플릿 사양을 만들려면 PowerShell, Azure CLI, 포털 또는 ARM 템플릿을 사용합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 템플릿 사양을 포함할 새 리소스 그룹을 만듭니다.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. 해당 리소스 그룹에서 템플릿 사양을 만듭니다. 새 템플릿 사양에 **storageSpec** 라는 이름을 지정합니다.

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. 템플릿 사양을 포함할 새 리소스 그룹을 만듭니다.

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. 해당 리소스 그룹에서 템플릿 사양을 만듭니다. 새 템플릿 사양에 **storageSpec** 라는 이름을 지정합니다.

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **템플릿 사양** 을 검색합니다. 사용 가능한 옵션에서 **템플릿 사양** 을 선택합니다.

    :::image type="content" source="./media/quickstart-create-template-specs/search-template-spec.png" alt-text="템플릿 사양 검색":::

1. **템플릿 가져오기** 를 선택합니다.

    :::image type="content" source="./media/quickstart-create-template-specs/import-template.png" alt-text="템플릿 가져오기":::

1. 폴더 아이콘을 선택합니다.

    :::image type="content" source="./media/quickstart-create-template-specs/open-folder.png" alt-text="폴더 열기":::

1. 저장한 로컬 템플릿으로 이동하여 선택합니다. **열기** 를 선택합니다.
1. **가져오기** 를 선택합니다.

    :::image type="content" source="./media/quickstart-create-template-specs/select-import.png" alt-text="가져오기 옵션 선택":::

1. 다음 값을 제공합니다.

    - **이름**: 템플릿 사양의 이름을 입력합니다.  예: **storageSpec**
    - **구독**: 템플릿 사양을 만드는 데 사용되는 Azure 구독을 선택합니다.
    - **리소스 그룹**: **새로 만들기** 를 선택한 다음, 새 리소스 그룹 이름을 지정합니다.  예: **templateSpecRG**.
    - **위치**: 리소스 그룹에 대한 위치를 선택합니다. 예: **미국 서부 2**.
    - **버전**: 탬플릿 사양의 버전을 입력합니다. **1.0** 을 사용합니다.

1. **검토 + 만들기** 를 선택합니다.
1. **만들기** 를 선택합니다.

# <a name="arm-template"></a>[ARM 템플릿](#tab/azure-resource-manager)

> [!NOTE]
> ARM 템플릿을 사용하는 대신 PowerShell 또는 CLI를 사용하여 템플릿 사양을 만드는 것이 좋습니다. 이러한 도구는 연결된 템플릿을 기본 템플릿에 연결된 아티팩트로 자동 변환합니다. ARM 템플릿을 사용하여 템플릿 사양을 만드는 경우 연결된 템플릿을 아티팩트로 수동으로 추가해야 합니다. 이 작업은 복잡할 수 있습니다.

1. ARM 템플릿을 사용하여 템플릿 사양을 만들면 템플릿이 리소스 정의에 포함됩니다. 로컬 템플릿에 수행해야 하는 몇 가지 변경 내용이 있습니다. 다음 템플릿을 복사하고 **azuredeploy.json** 으로 로컬로 저장합니다.

    > [!NOTE]
    > 포함된 템플릿에서 모든 [템플릿 식](template-expressions.md)을 두 번째 왼쪽 대괄호로 이스케이프해야 합니다. `"[` 대신 `"[[`을(를) 사용합니다. JSON 배열은 여전히 하나의 왼쪽 대괄호를 사용합니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2021-05-01",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2021-05-01",
              "name": "1.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "mainTemplate": {
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
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2021-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Azure CLI 또는 PowerShell을 사용하여 새 리소스 그룹을 만듭니다.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Azure CLI 또는 PowerShell을 사용하여 템플릿을 배포합니다.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

---

## <a name="deploy-template-spec"></a>템플릿 사양 배포

템플릿 사양을 배포하려면 템플릿을 배포하는 데 사용하는 것과 동일한 배포 명령을 사용합니다. 배포할 템플릿 사양의 리소스 ID를 전달합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 새 스토리지 계정을 포함하는 리소스 그룹을 만듭니다.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

1. 템플릿 사양의 리소스 ID를 가져옵니다.

    ```azurepowershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Versions.Id
    ```

1. 템플릿 사양을 배포합니다.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG
    ```

1. ARM 템플릿과 정확히 동일한 매개 변수를 제공합니다. 스토리지 계정 유형에 대한 매개 변수를 사용하여 템플릿 사양을 다시 배포합니다.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG `
      -storageAccountType Standard_GRS
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. 새 스토리지 계정을 포함하는 리소스 그룹을 만듭니다.

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. 템플릿 사양의 리소스 ID를 가져옵니다.

    ```azurecli
    id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
    ```

    > [!NOTE]
    > 템플릿 사양 ID를 가져와서 Windows PowerShell의 변수에 할당하는 것과 관련된 알려진 문제가 있습니다.

1. 템플릿 사양을 배포합니다.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id
    ```

1. ARM 템플릿과 정확히 동일한 매개 변수를 제공합니다. 스토리지 계정 유형에 대한 매개 변수를 사용하여 템플릿 사양을 다시 배포합니다.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters storageAccountType='Standard_GRS'
    ```

# <a name="portal"></a>[포털](#tab/azure-portal)

1. 사용자가 만든 템플릿 사양을 선택합니다.

    :::image type="content" source="./media/quickstart-create-template-specs/select-template-spec.png" alt-text="템플릿 사양 선택":::

1. **배포** 를 선택합니다.

    :::image type="content" source="./media/quickstart-create-template-specs/deploy-template-spec.png" alt-text="템플릿 사양 배포":::

1. 다음 값을 제공합니다.

    - **구독**: 리소스를 만들 Azure 구독을 선택합니다.
    - **리소스 그룹**: **새로 만들기** 를 선택한 다음, **storageRG** 를 입력합니다.
    - **스토리지 계정 유형**: **Standard_GRS** 를 선택합니다.

1. **검토 + 만들기** 를 선택합니다.
1. **만들기** 를 선택합니다.

# <a name="arm-template"></a>[ARM 템플릿](#tab/azure-resource-manager)

1. 다음 템플릿을 복사하고 **storage.json** 이라는 파일에 로컬로 저장합니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2021-04-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Azure CLI 또는 PowerShell을 사용하여 스토리지 계정에 대한 새 리소스 그룹을 만듭니다.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Azure CLI 또는 PowerShell을 사용하여 템플릿을 배포합니다.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="grant-access"></a>액세스 권한 부여

조직의 다른 사용자가 템플릿 사양을 배포하도록 하려면 해당 사용자에게 읽기 액세스 권한을 부여해야 합니다. 공유하려는 템플릿 사양이 포함된 리소스 그룹에 대한 Azure AD 그룹에 읽기 권한자 역할을 할당할 수 있습니다. 자세한 내용은 [자습서: Azure PowerShell을 사용하여 Azure 리소스에 대한 그룹 액세스 권한 부여](../../role-based-access-control/tutorial-role-assignments-group-powershell.md)를 참조하세요.

## <a name="update-template"></a>템플릿 업데이트

템플릿 사양에서 템플릿에 적용할 변경 내용을 식별했다고 가정해 보겠습니다. 다음 템플릿은 스토리지 계정 이름에 대한 접두사를 추가하는 것을 제외하면 이전 템플릿과 비슷합니다. 다음 템플릿을 복사하고 azuredeploy.json 파일을 업데이트합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/templatespecs/azuredeploy2.json":::

## <a name="update-template-spec-version"></a>템플릿 사양 버전 업데이트

수정된 템플릿에 대한 새 템플릿 사양을 생성하는 대신 기존 템플릿 사양에 `2.0`이라는 새 버전을 추가합니다. 사용자는 배포할 버전을 선택할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 템플릿 사양에 대한 새 버전을 만듭니다.

   ```powershell
   New-AzTemplateSpec `
     -Name storageSpec `
     -Version "2.0" `
     -ResourceGroupName templateSpecRG `
     -Location westus2 `
     -TemplateFile "c:\Templates\azuredeploy.json"
   ```

1. 새 버전을 배포하려면 `2.0` 버전에 대한 리소스 ID를 가져옵니다.

   ```powershell
   $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "2.0").Versions.Id
   ```

1. 해당 모델을 배포합니다. 스토리지 계정 이름의 접두사를 제공합니다.

   ```powershell
   New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName storageRG `
     -namePrefix "demoaccount"
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. 템플릿 사양에 대한 새 버전을 만듭니다.

   ```azurecli
   az ts create \
     --name storageSpec \
     --version "2.0" \
     --resource-group templateSpecRG \
     --location "westus2" \
     --template-file "c:\Templates\azuredeploy.json"
   ```

1. 새 버전을 배포하려면 `2.0` 버전에 대한 리소스 ID를 가져옵니다.

   ```azurecli
   id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "2.0" --query "id")
   ```

1. 해당 모델을 배포합니다. 스토리지 계정 이름의 접두사를 제공합니다.

   ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters namePrefix='demoaccount'
    ```

# <a name="portal"></a>[포털](#tab/azure-portal)

1. 템플릿 사양에서 **새 버전 만들기** 를 선택합니다.

   :::image type="content" source="./media/quickstart-create-template-specs/select-versions.png" alt-text="새 버전 만들기":::

1. 새 버전 `2.0`의 이름을 지정하고 필요에 따라 메모를 추가합니다. **템플릿 편집** 을 선택합니다.

   :::image type="content" source="./media/quickstart-create-template-specs/add-version-name.png" alt-text="새 버전 이름 지정":::

1. 템플릿의 내용을 업데이트된 템플릿으로 바꿉니다. **검토 + 저장** 을 선택합니다.
1. **변경 내용 저장** 을 선택합니다.

1. 새 버전을 배포하려면 **버전** 을 선택합니다.

   :::image type="content" source="./media/quickstart-create-template-specs/see-versions.png" alt-text="버전 나열":::

1. 배포할 버전에 대해 점 세 개와 **배포** 를 선택합니다.

   :::image type="content" source="./media/quickstart-create-template-specs/deploy-version.png" alt-text="배포할 버전 선택":::

1. 이전 버전을 배포할 때와 같이 필드를 입력합니다.
1. **검토 + 만들기** 를 선택합니다.
1. **만들기** 를 선택합니다.

# <a name="arm-template"></a>[ARM 템플릿](#tab/azure-resource-manager)

1. 다시 말하자면 템플릿 사양에 맞게 작동하도록 로컬 템플릿을 변경해야 합니다. 다음 템플릿을 복사하고 azuredeploy.json으로 로컬로 저장합니다.

   ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2021-05-01",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2021-05-01",
              "name": "2.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "mainTemplate": {
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
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    },
                    "namePrefix": {
                      "type": "string",
                      "maxLength": 11,
                      "defaultValue": "store",
                      "metadata": {
                        "description": "Prefix for storage account name"
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat(parameters('namePrefix'), uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2021-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. 템플릿 사양에 새 버전을 추가하려면 Azure CLI 또는 PowerShell을 사용하여 템플릿을 배포합니다.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

1. 다음 템플릿을 복사하고 **storage.json** 이라는 파일에 로컬로 저장합니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2021-04-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '2.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Azure CLI 또는 PowerShell을 사용하여 템플릿을 배포합니다.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 배포한 리소스를 정리하려면 만든 리소스 그룹을 모두 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 리소스 그룹을 선택합니다.

1. 이름으로 필터링 필드에 리소스 그룹 이름(templateSpecRG 및 storageRG)을 입력합니다.

1. 해당 리소스 그룹 이름을 선택합니다.

1. 위쪽 메뉴에서 리소스 그룹 삭제를 선택합니다.

## <a name="next-steps"></a>다음 단계

연결된 템플릿을 포함하는 템플릿 사양을 만드는 방법에 대한 자세한 내용은 [연결된 템플릿의 템플릿 사양 만들기](template-specs-create-linked.md)를 참조하세요.
