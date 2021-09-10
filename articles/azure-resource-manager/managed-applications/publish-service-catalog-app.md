---
title: 서비스 카탈로그 관리형 앱 게시
description: 조직의 구성원을 위한 Azure 관리형 애플리케이션을 만드는 방법이 나와 있습니다.
author: tfitzmac
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli, devx-track-azurepowershell, subject-rbac-steps
ms.date: 08/16/2021
ms.author: tomfitz
ms.openlocfilehash: 7722017e2cbe9c0f195ce24ee5452674ad0fd344
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253750"
---
# <a name="quickstart-create-and-publish-a-managed-application-definition"></a>빠른 시작: 관리되는 애플리케이션 정의 만들기 및 게시

이 빠른 시작에서는 [Azure Managed Applications](overview.md)를 사용하는 방법을 소개합니다. 조직의 구성원을 위한 관리형 애플리케이션을 만들고 게시할 수 있습니다.

관리형 애플리케이션을 서비스 카탈로그에 게시하려면 다음을 수행해야 합니다.

* 관리되는 애플리케이션과 함께 배포할 리소스를 정의하는 템플릿을 만듭니다.
* 관리 되는 애플리케이션을 배포할 때 포털에 대한 사용자 인터페이스 요소를 정의합니다.
* 필요한 템플릿 파일이 포함된 _.zip_ 패키지를 만듭니다.
* 사용자의 구독에 속한 리소스 그룹에 액세스해야 하는 사용자, 그룹 또는 애플리케이션을 결정합니다.
* _.zip_ 패키지를 나타내고 ID에 대한 액세스를 요청하는 관리형 애플리케이션 정의를 만듭니다.

## <a name="create-the-arm-template"></a>ARM 템플릿 만들기

모든 관리되는 애플리케이션 정의에는 _mainTemplate.json_ 이라는 파일이 포함됩니다. 여기에서 표시할 Azure 리소스를 정의합니다. 템플릿은 일반 ARM 템플릿과 차이가 없습니다.

이름이 _mainTemplate.json_ 인 파일을 만듭니다. 이름은 대/소문자를 구분합니다.

파일에 다음 JSON을 추가합니다. 스토리지 계정을 만들기 위한 매개 변수를 정의하고 스토리지 계정의 속성을 지정합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountNamePrefix": {
      "type": "string"
    },
    "storageAccountType": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
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
    "storageEndpoint": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
    }
  }
}
```

_mainTemplate.json_ 파일을 저장합니다.

## <a name="define-your-create-experience"></a>만들기 환경 정의

게시자는 관리형 애플리케이션을 만들기 위한 포털 환경을 정의합니다. _createUiDefinition.json_ 파일은 포털 인터페이스를 생성합니다. 사용자는 드롭다운, 텍스트 상자 및 암호 상자를 포함한 [컨트롤 요소](create-uidefinition-elements.md)를 사용하여 각 매개 변수에 대한 입력을 제공하는 방법을 정의합니다.

이름이 _createUiDefinition.json_ 인 파일을 만듭니다(이 이름은 대/소문자를 구분함).

다음 시작 JSON을 파일에 추가하고 저장합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "storageConfig",
        "label": "Storage settings",
        "subLabel": {
          "preValidation": "Configure the infrastructure settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Storage settings",
        "elements": [
          {
            "name": "storageAccounts",
            "type": "Microsoft.Storage.MultiStorageAccountCombo",
            "label": {
              "prefix": "Storage account name prefix",
              "type": "Storage account type"
            },
            "defaultValue": {
              "type": "Standard_LRS"
            },
            "constraints": {
              "allowedTypes": [
                "Premium_LRS",
                "Standard_LRS",
                "Standard_GRS"
              ]
            }
          }
        ]
      }
    ],
    "outputs": {
      "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
      "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
      "location": "[location()]"
    }
  }
}
```

자세히 알아보려면 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.

## <a name="package-the-files"></a>파일을 패키지로 만들기

이름이 _app.zip_ 인 _.zip_ 파일에 두 개의 파일을 추가합니다. 두 파일은 _.zip 파일_ 의 루트 수준에 있어야 합니다. 폴더에 배치하는 경우 관리되는 애플리케이션 정의를 만들 때 필요한 파일이 없음을 나타내는 오류가 나타납니다.

패키지를 사용할 수 있는 액세스 가능한 위치에 패키지를 업로드합니다. 스토리지 계정의 고유한 이름을 제공해야 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name storageGroup -Location eastus

$storageAccount = New-AzStorageAccount `
  -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent `
  -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name storageGroup --location eastus

az storage account create \
    --name mystorageaccount \
    --resource-group storageGroup \
    --location eastus \
    --sku Standard_LRS \
    --kind StorageV2

az storage container create \
    --account-name mystorageaccount \
    --name appcontainer \
    --public-access blob

az storage blob upload \
    --account-name mystorageaccount \
    --container-name appcontainer \
    --name "app.zip" \
    --file "D:\myapplications\app.zip"

```

---

## <a name="create-the-managed-application-definition"></a>관리형 애플리케이션 정의 만들기

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Azure Active Directory 사용자 그룹 또는 애플리케이션 만들기

다음 단계는 고객의 리소스를 관리하기 위한 사용자 그룹, 사용자 또는 애플리케이션을 선택하는 것입니다. 이 ID에는 할당된 역할에 따라 관리되는 리소스 그룹에 대한 권한이 있습니다. 역할은 소유자 또는 기여자 같은 Azure 기본 제공 역할입니다. 새 Active Directory 사용자 그룹을 만들려면 [그룹을 만들고 Azure Active Directory에 구성원 추가](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요.

리소스 관리에 사용할 사용자 그룹의 개체 ID가 필요합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
groupid=$(az ad group show --group mygroup --query objectId --output tsv)
```

---

### <a name="get-the-role-definition-id"></a>역할 정의 ID 가져오기

다음으로 사용자, 사용자 그룹 또는 애플리케이션에 대한 액세스 권한을 부여하려는 Azure 기본 제공 역할에 대한 역할 정의 ID가 필요합니다. 일반적으로 소유자 또는 참가자 또는 읽기 권한자 역할을 사용합니다. 다음 명령은 소유자 역할에 대한 역할 정의 ID를 가져오는 방법을 보여 줍니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
ownerid=$(az role definition list --name Owner --query [].name --output tsv)
```

---

### <a name="create-the-managed-application-definition"></a>관리형 애플리케이션 정의 만들기

관리되는 애플리케이션 정의를 저장하기 위한 리소스 그룹이 아직 없는 경우 지금 새로 만듭니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

---

이제 관리형 애플리케이션 정의 리소스를 만듭니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
blob=$(az storage blob url --account-name mystorageaccount --container-name appcontainer --name app.zip --output tsv)

az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$groupid:$ownerid" \
  --package-file-uri "$blob"
```

---

명령이 완료되면 리소스 그룹에 관리되는 애플리케이션 정의가 있습니다.

앞의 예제에서 사용된 몇 가지 매개 변수는 다음과 같습니다.

* **리소스 그룹**: 관리형 애플리케이션 정의를 만든 리소스 그룹의 이름입니다.
* **잠금 수준**: 관리되는 리소스 그룹에 배치하는 잠금 유형입니다. 고객이 이 리소스 그룹에서 바람직하지 않은 작업을 수행할 수 없게 합니다. 현재 지원되는 유일한 잠금 수준은 ReadOnly입니다. ReadOnly를 지정하는 경우 고객은 관리되는 리소스 그룹에 있는 리소스만 읽을 수 있습니다. 관리되는 리소스 그룹에 대한 액세스 권한이 부여된 게시자 ID는 잠금에서 제외됩니다.
* **권한 부여**: 관리되는 리소스 그룹에 권한을 부여하는 데 사용되는 보안 주체 ID 및 역할 정의 ID를 설명합니다. `<principalId>:<roleDefinitionId>` 형식으로 지정됩니다. 둘 이상의 값이 필요한 경우 `<principalId1>:<roleDefinitionId1>,<principalId2>:<roleDefinitionId2>` 형식으로 지정합니다. 값은 쉼표로 구분됩니다.
* **패키지 파일 URI**: 필요한 파일이 포함되어 있는 _.zip_ 패키지의 위치입니다.

## <a name="bring-your-own-storage-for-the-managed-application-definition"></a>관리형 애플리케이션 정의에 대한 사용자 고유의 스토리지 가져오기

생성 시 사용자가 제공한 스토리지 계정 내에 관리형 애플리케이션 정의를 저장하도록 선택하여 해당 위치와 액세스를 사용자가 자신의 규정 요구에 맞도록 완벽하게 관리할 수 있습니다.

> [!NOTE]
> 사용자 고유의 스토리지 가져오기는 ARM 템플릿 또는 관리형 애플리케이션 정의의 REST API 배포에서만 지원됩니다.

### <a name="select-your-storage-account"></a>사용자의 스토리지 계정을 선택합니다.

서비스 카탈로그와 함께 사용하기 위해 관리형 애플리케이션 정의를 포함할 [스토리지 계정을 만들어야](../../storage/common/storage-account-create.md) 합니다.

스토리지 계정의 리소스 ID를 복사합니다. 나중에 정의를 배포할 때 사용됩니다.

### <a name="set-the-role-assignment-for-appliance-resource-provider-in-your-storage-account"></a>스토리지 계정에서 “어플라이언스 리소스 공급자”에 대한 역할 할당을 설정합니다.

관리되는 애플리케이션 정의를 스토리지 계정에 배포하려면 먼저 스토리지 계정 범위에서 **어플라이언스 리소스 공급자** 사용자에게 **기여자** 역할을 할당합니다. 이 할당을 통해 ID는 스토리지 계정의 컨테이너에 정의 파일을 쓸 수 있습니다.

세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

### <a name="deploy-the-managed-application-definition-with-an-arm-template"></a>ARM 템플릿을 사용하여 관리형 애플리케이션 정의 배포

다음 ARM 템플릿을 사용하여 패키지된 관리형 애플리케이션을 서비스 카탈로그에 새 관리형 애플리케이션 정의로 배포합니다. 정의 파일은 해당 스토리지 계정에 저장되고 유지 관리됩니다.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "applicationName": {
      "type": "string",
      "metadata": {
        "description": "Managed Application name"
      }
    },
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
    "definitionStorageResourceID": {
      "type": "string",
      "metadata": {
        "description": "Storage account resource ID for where you're storing your definition"
      }
    },
    "_artifactsLocation": {
      "type": "string",
      "metadata": {
        "description": "The base URI where artifacts required by this template are located."
      }
    }
  },
  "variables": {
    "lockLevel": "None",
    "description": "Sample Managed application definition",
    "displayName": "Sample Managed application definition",
    "managedApplicationDefinitionName": "[parameters('applicationName')]",
    "packageFileUri": "[parameters('_artifactsLocation')]",
    "defLocation": "[parameters('definitionStorageResourceID')]",
    "managedResourceGroupId": "[concat(subscription().id,'/resourceGroups/', concat(parameters('applicationName'),'_managed'))]",
    "applicationDefinitionResourceId": "[resourceId('Microsoft.Solutions/applicationDefinitions',variables('managedApplicationDefinitionName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Solutions/applicationDefinitions",
      "apiVersion": "2020-08-21-preview",
      "name": "[variables('managedApplicationDefinitionName')]",
      "location": "[parameters('location')]",
      "properties": {
        "lockLevel": "[variables('lockLevel')]",
        "description": "[variables('description')]",
        "displayName": "[variables('displayName')]",
        "packageFileUri": "[variables('packageFileUri')]",
        "storageAccountId": "[variables('defLocation')]"
      }
    }
  ],
  "outputs": {}
}
```

`applicationDefinitions` 속성에 `storageAccountId`라는 새 속성을 추가하고 정의를 해당 값으로 저장할 스토리지 계정 ID를 제공합니다.

애플리케이션 정의 파일이 `applicationDefinitions`라는 컨테이너에 제공된 스토리지 계정에 저장되어 있는지 확인할 수 있습니다.

> [!NOTE]
> 보안 강화를 위해 관리형 애플리케이션 정의를 만들어 [암호화를 사용하도록 설정된 Azure Storage 계정 Blob](../../storage/common/storage-service-encryption.md)에 저장할 수 있습니다. 정의 콘텐츠는 스토리지 계정의 암호화 옵션을 통해 암호화됩니다. 파일에 대한 권한이 있는 사용자만 서비스 카탈로그의 정의를 볼 수 있습니다.

## <a name="make-sure-users-can-see-your-definition"></a>사용자가 정의를 볼 수 있는지 확인합니다.

사용자가 관리되는 애플리케이션 정의에 액세스할 수 있지만 조직의 다른 사용자도 액세스할 수 있는 것이 좋습니다. 정의에서 최소한 읽기 역할을 부여합니다. 구독 또는 리소스 그룹에서 이 수준의 액세스를 상속받을 수 있습니다. 정의에 대한 액세스 권한이 있는 사용자를 확인하고 사용자 또는 그룹을 추가하려면 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

관리되는 애플리케이션 정의를 게시했습니다. 이제 해당 정의의 인스턴스를 배포하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [빠른 시작: 서비스 카탈로그 앱 배포](deploy-service-catalog-quickstart.md)
