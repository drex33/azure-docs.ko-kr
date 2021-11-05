---
title: 템플릿 사양에 대한 포털 양식 만들기
description: Azure Portal 양식에 표시되는 양식을 만드는 방법을 알아봅니다. 양식을 사용하여 템플릿 사양 배포
ms.topic: tutorial
ms.date: 11/02/2021
ms.openlocfilehash: 3175264b8d3a5b9fd1698c8ccd1522ddb1f54b63
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479695"
---
# <a name="tutorial-create-azure-portal-forms-for-a-template-spec"></a>자습서: 템플릿 사양에 대한 Azure Portal 양식 만들기

사용자가 [템플릿 사양](template-specs.md)을 배포하는 데 도움이 되도록 Azure Portal에 표시되는 양식을 만들 수 있습니다. 사용자는 이 양식을 사용하여 템플릿 사양에 매개 변수로 전달되는 값을 제공할 수 있습니다.

템플릿 사양을 만들 때 양식과 ARM 템플릿(Azure Resource Manager 템플릿)을 함께 패키징합니다. 포털을 통해 템플릿 사양을 배포하면 양식이 자동으로 시작됩니다.

:::image type="content" source="./media/template-specs-create-portal-forms/view-portal.png" alt-text="템플릿 사양에 대한 값을 제공하는 양식의 스크린샷":::

## <a name="prerequisites"></a>사전 요구 사항

활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Azure PowerShell의 경우 [버전 6.0.0 이상](/powershell/azure/install-az-ps)을 사용합니다. Azure CLI의 경우 [버전 2.24.0 이상](/cli/azure/install-azure-cli)을 사용합니다.

## <a name="create-template"></a>템플릿 만들기

양식에서 사용할 수 있는 여러 가지 포털 요소를 표시하려면 여러 매개 변수가 있는 ARM 템플릿을 사용합니다. 다음 템플릿은 키 자격 증명 모음을 만들고, 키 자격 증명 모음에 대한 사용자의 권한을 구성하고, 비밀을 추가합니다.

이 파일을 복사하여 로컬에 저장합니다. 이 자습서에서는 이름이 **keyvault.json** 인 것으로 가정하지만, 다른 이름을 지정할 수도 있습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Specifies the Azure location where the key vault should be created."
      }
    },
    "enabledForDeployment": {
      "type": "bool",
      "defaultValue": false,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies whether Azure Virtual Machines are permitted to retrieve certificates stored as secrets from the key vault."
      }
    },
    "enabledForDiskEncryption": {
      "type": "bool",
      "defaultValue": false,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies whether Azure Disk Encryption is permitted to retrieve secrets from the vault and unwrap keys."
      }
    },
    "enabledForTemplateDeployment": {
      "type": "bool",
      "defaultValue": false,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies whether Azure Resource Manager is permitted to retrieve secrets from the key vault."
      }
    },
    "tenantId": {
      "type": "string",
      "defaultValue": "[subscription().tenantId]",
      "metadata": {
        "description": "Specifies the Azure Active Directory tenant ID that should be used for authenticating requests to the key vault. Get it by using Get-AzSubscription cmdlet."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the secret that you want to create."
      }
    },
    "secretValue": {
      "type": "securestring",
      "metadata": {
        "description": "Specifies the value of the secret that you want to create."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "enabledForDeployment": "[parameters('enabledForDeployment')]",
        "enabledForDiskEncryption": "[parameters('enabledForDiskEncryption')]",
        "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
        "tenantId": "[parameters('tenantId')]",
        "accessPolicies": [
          {
            "objectId": "[parameters('objectId')]",
            "tenantId": "[parameters('tenantId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]"
            }
          }
        ],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/secrets",
      "apiVersion": "2019-09-01",
      "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "properties": {
        "value": "[parameters('secretValue')]"
      }
    }
  ]
}
```

## <a name="create-default-form"></a>기본 양식 만들기

Azure Portal은 양식을 만들고 미리 볼 수 있는 샌드박스를 제공합니다. 이 샌드박스는 기존 ARM 템플릿으로 양식을 생성할 수 있습니다. 이 기본 양식을 사용하여 템플릿 사양에 대한 양식 만들기를 시작할 것입니다.

1. [양식 보기 샌드박스](https://aka.ms/form/sandbox)를 엽니다.

1. **패키지 형식** 을 **CustomTemplate** 으로 설정합니다.

   :::image type="content" source="./media/template-specs-create-portal-forms/package-type.png" alt-text="패키지 유형을 사용자 지정 템플릿으로 설정하는 스크린샷":::

1. 기존 템플릿을 여는 아이콘을 선택합니다.

   :::image type="content" source="./media/template-specs-create-portal-forms/open-template.png" alt-text="파일을 여는 아이콘의 스크린샷":::

1. 로컬에 저장한 키 자격 증명 모음 템플릿으로 이동합니다. 키 자격 증명 모음을 선택하고 **열기** 를 선택합니다.
1. 현재 변경 내용을 덮어쓸지 묻는 메시지가 표시되면 **예** 를 선택합니다.
1. 자동 생성된 양식이 코드 창에 표시됩니다. 이 양식을 수정하지 않고 작동 모습을 보려면 **미리 보기** 를 선택합니다.

   :::image type="content" source="./media/template-specs-create-portal-forms/preview-form.png" alt-text="미리 보기를 선택하는 스크린샷":::

1. 샌드박스에 양식이 표시됩니다. 양식에는 구독, 리소스 그룹 및 지역을 선택하는 필드가 있습니다. 템플릿의 모든 매개 변수에 대한 필드도 있습니다.

   대부분의 필드는 텍스트 상자이지만, 일부는 매개 변수 형식과 관련된 필드입니다. 매개 변수로 허용되는 값이 템플릿에 포함된 경우 자동 생성된 양식에서 드롭다운 요소를 사용합니다. 드롭다운 요소는 허용되는 값으로 미리 채워져 있습니다.

   > [!WARNING]
   > **만들기** 를 선택하면 실제 배포가 시작되므로 선택하지 마세요. 이 자습서의 뒷부분에서 템플릿 사양을 배포할 기회가 있습니다.

## <a name="customize-form"></a>양식 사용자 지정

기본 양식은 양식을 이해하기에 좋은 출발점이지만 일반적으로 기본 양식을 사용자 지정해서 사용하게 됩니다. 샌드박스 또는 Visual Studio Code에서 양식을 편집할 수 있습니다. 미리 보기 옵션은 샌드박스에서만 사용할 수 있습니다.

1. 올바른 스키마를 설정해 보겠습니다. 스키마 텍스트를 다음으로 바꿉니다.

   ::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform.json" range="1-2" highlight="2" :::

1. 양식의 용도를 설명하는 **제목** 을 지정합니다.

   ::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform.json" range="1-6" highlight="6" :::

1. 기본 양식에는 템플릿에 대한 모든 필드가 **기본** 이라는 하나의 단계로 결합되어 있습니다. 사용자가 제공하는 값을 이해하는 데 도움이 되도록, 양식을 여러 단계로 나눕니다. 각 단계에는 배포할 솔루션의 논리적 부분과 관련된 필드가 포함됩니다.

   **기본** 이라는 레이블이 지정된 단계를 찾습니다. 이 단계를 유지하되, 그 아래에 단계를 추가합니다. 새 단계에서는 키 자격 증명 모음을 구성하고, 사용자 권한을 설정하고, 비밀을 지정하는 데 초점을 둡니다. 기본 단계 뒤에 쉼표를 추가해야 합니다.

   ::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/steps.json" highlight="15-32" :::

   > [!IMPORTANT]
   > 양식의 속성은 대/소문자를 구분합니다. 예제에 표시된 대/소문자를 사용해야 합니다.

1. **미리 보기** 를 선택합니다. 단계가 표시되지만, 대부분의 단계에는 요소가 없습니다.

   :::image type="content" source="./media/template-specs-create-portal-forms/view-steps.png" alt-text="양식 단계의 스크린샷":::

1. 이제 요소를 적절한 단계로 이동합니다. **비밀 이름** 및 **비밀 값** 이라는 레이블이 지정된 요소부터 시작합니다. **기본** 단계에서 이러한 요소를 제거하고 **비밀** 단계에 추가합니다.

   ```json
   {
     "name": "secret",
     "label": "Secret",
     "elements": [
       {
         "name": "secretName",
         "type": "Microsoft.Common.TextBox",
         "label": "Secret Name",
         "defaultValue": "",
         "toolTip": "Specifies the name of the secret that you want to create.",
         "constraints": {
           "required": true,
           "regex": "",
           "validationMessage": ""
         },
         "visible": true
       },
       {
         "name": "secretValue",
         "type": "Microsoft.Common.PasswordBox",
         "label": {
           "password": "Secret Value",
           "confirmPassword": "Confirm password"
         },
         "toolTip": "Specifies the value of the secret that you want to create.",
         "constraints": {
           "required": true,
           "regex": "",
           "validationMessage": ""
         },
         "options": {
           "hideConfirmation": true
         },
         "visible": true
       }
     ]
   }
   ```

1. 요소를 이동할 때 `outputs` 섹션을 수정해야 합니다. 현재 출력 섹션은 요소가 여전히 기본 단계에 있는 것처럼 요소를 참조합니다. `secret` 단계의 요소를 참조하도록 구문을 수정합니다.

   ```json
   "outputs": {
     "parameters": {
       ...
       "secretName": "[steps('secret').secretName]",
       "secretValue": "[steps('secret').secretValue]"
     }
   ```

1. 요소를 적절한 단계로 계속 이동합니다. 하나씩 처리하지 말고, 업데이트된 양식을 살펴보세요.

   ::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform.json" :::

이 파일을 **keyvaultform.json** 이라는 이름으로 로컬에 저장합니다.

## <a name="create-template-spec"></a>템플릿 사양 만들기

템플릿 사양을 만들 때 두 파일을 모두 제공해야 합니다.

PowerShell의 경우 [New-AzTemplateSpec](/powershell/module/az.resources/new-aztemplatespec)을 사용하고 `-UIFormDefinitionFile` 매개 변수에 양식을 입력합니다.

```azurepowershell
New-AzTemplateSpec `
  -name keyvaultspec `
  -version 1 `
  -ResourceGroupName templateSpecRG `
  -location westus2 `
  -templatefile keyvault.json `
  -UIFormDefinitionFile keyvaultform.json
```

Azure CLI의 경우 [az ts create](/cli/azure/ts#az_ts_create) 명령을 사용하고 `--ui-form-definition` 매개 변수에 양식을 입력합니다.

```azurecli
az ts create \
  --name keyvaultspec \
  --version 1 \
  --resource-group templatespecRG \
  --location westus2 \
  --template-file keyvault.json \
  --ui-form-definition keyvaultform.json
```

## <a name="deploy-through-portal"></a>포털을 통해 배포

양식을 테스트하려면 포털로 이동하고 템플릿 사양으로 이동한 다음, **배포** 를 선택합니다.

:::image type="content" source="./media/template-specs-create-portal-forms/deploy-template-spec.png" alt-text="배포 옵션이 있는 템플릿 사양 개요의 스크린샷":::

만든 양식이 표시됩니다. 단계를 진행하고 필드 값을 입력합니다.

**기본** 단계에는 **지역** 에 대한 필드가 표시됩니다. 이 필드는 리소스 그룹의 위치에 사용됩니다. **키 자격 증명 모음** 단계에는 **위치** 에 대한 필드가 표시됩니다. 이 필드는 키 자격 증명 모음의 위치에 사용됩니다.

**권한** 단계에서는 개체 ID의 고유한 사용자 ID를 입력할 수 있습니다. 키 및 비밀 권한에는 기본값(`["list"]`)을 사용합니다. 다음 섹션에서 이 옵션을 개선하겠습니다.

값을 모두 입력한 후에는 **만들기** 를 선택하여 템플릿 사양을 배포합니다.

## <a name="improve-the-form"></a>양식 개선

이전 섹션에서는 단계를 추가하고 요소를 이동했지만, 기본 동작은 변경하지 않았습니다. 이 섹션에서는 템플릿 사양의 사용자를 위한 환경을 개선하는 변경 작업을 수행합니다.

이전에는 두 개의 권한 필드가 텍스트 상자였습니다. 이제 드롭다운을 사용합니다. 형식을 `Microsoft.Common.DropDown`으로 설정합니다.

다음과 같이 `keysPermissions`를 업데이트합니다.

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="169-171" highlight="3" :::

그리고 다음과 같이 `secretsPermissions`를 업데이트합니다.

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="253-255" highlight="3" :::

이러한 필드는 배열을 템플릿에 전달해야 합니다. 일반 드롭다운은 값을 하나만 선택할 수 있으므로 작동하지 않습니다. 값을 2개 이상 선택하여 배열로 전달하려면 `multiselect` 필드를 추가하고 `true`로 설정합니다.

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="169-173" highlight="5" :::

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="253-257" highlight="5" :::

마지막으로 드롭다운 및 기본값으로 허용되는 값을 지정해야 합니다.

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="169-304" highlight="6-8,12-81,90-92,96-133" :::

새 버전의 템플릿 사양을 만듭니다.

PowerShell:

```azurepowershell
New-AzTemplateSpec `
  -name keyvaultspec `
  -version 2 `
  -ResourceGroupName templateSpecRG `
  -location westus2 `
  -templatefile keyvault.json `
  -UIFormDefinitionFile keyvaultform.json
```

Azure CLI:

```azurecli
az ts create \
  --name keyvaultspec \
  --version 2 \
  --resource-group templatespecRG \
  --location westus2 \
  --template-file keyvault.json \
  --ui-form-definition keyvaultform.json
```

향상된 포털 양식을 사용하여 템플릿 사양을 다시 배포합니다.

:::image type="content" source="./media/template-specs-create-portal-forms/view-portal.png" alt-text="템플릿 사양에 대한 값을 제공하는 양식의 스크린샷":::

이제 여러 값을 허용하는 권한 필드가 드롭다운으로 제공됩니다.

## <a name="next-steps"></a>다음 단계

템플릿 사양을 연결된 템플릿으로 배포하는 방법은 [자습서: 템플릿 사양을 연결된 템플릿으로 배포](template-specs-deploy-linked-template.md)를 참조하십시오.
