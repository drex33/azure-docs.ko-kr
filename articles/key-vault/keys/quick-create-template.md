---
title: Azure 빠른 시작 - Azure Resource Manager 템플릿을 사용하여 Azure Key Vault 및 키 만들기 | Microsoft Docs
description: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Key Vault를 만들고 자격 증명 모음에 키를 추가하는 방법을 보여주는 빠른 시작입니다.
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: mvc,subject-armqs, devx-track-azurepowershell
ms.date: 10/14/2020
ms.author: sebansal
ms.openlocfilehash: 8e8a0e931a2c33883bf18ad92a367110d9be093e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128572966"
---
# <a name="quickstart-create-an-azure-key-vault-and-a-key-by-using-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Azure 키 자격 증명 모음 및 키 만들기 

[Azure Key Vault](../general/overview.md)는 키, 암호, 인증서 및 기타 비밀 등, 비밀에 안전한 자격 증명을 제공하는 클라우드 서비스입니다. 이 빠른 시작에서는 키 자격 증명 모음 및 키를 만들기 위해 ARM 플랫폼(Azure Resource Manager 템플릿)을 배포하는 과정을 다루고 있습니다.


## <a name="prerequisites"></a>필수 구성 요소

이 문서를 완료하려면 다음이 필요합니다.

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- 사용자에게 할당된 Azure 기본 제공 역할, 권장 역할 **기여자** 가 있어야 합니다. [여기서 자세히 알아보세요.](../../role-based-access-control/role-assignments-portal.md)
- 권한을 구성하려면 템플릿에 Azure AD 사용자 개체 ID가 필요합니다. 다음 절차는 개체 ID(GUID)를 가져옵니다.

    1. **사용해 보세요** 를 선택하여 다음 Azure PowerShell 또는 Azure CLI 명령을 수행한 다음, 스크립트를 셸 창에 붙여넣습니다. 스크립트를 붙여넣으려면 셸을 마우스 오른쪽 단추로 클릭하고 **붙여넣기** 를 선택합니다.

        # <a name="cli"></a>[CLI](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    1. 개체 ID를 기록해 둡니다. 이 빠른 시작의 다음 섹션에서 필요합니다.

## <a name="review-the-template"></a>템플릿 검토

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the key vault to be created."
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
        "description": "The SKU of the vault to be created."
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "The name of the key to be created."
      }
    },
    "keyType": {
      "type": "string",
      "metadata": {
        "description": "The JsonWebKeyType of the key to be created."
      }
    },
    "keyOps": {
      "type": "array",
      "defaultValue": [],
      "metadata": {
        "description": "The permitted JSON web key operations of the key to be created."
      }
    },
    "keySize": {
      "type": "int",
      "defaultValue": 2048,
      "metadata": {
        "description": "The size in bits of the key to be created."
      }
    },
    "curveName": {
      "type": "string",
      "defaultValue": "",
      "metadata": {
        "description": "The JsonWebKeyCurveName of the key to be created."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enableRbacAuthorization": false,
        "enableSoftDelete": false,
        "enabledForDeployment": false,
        "enabledForDiskEncryption": false,
        "enabledForTemplateDeployment": false,
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
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
      "type": "Microsoft.KeyVault/vaults/keys",
      "apiVersion": "2019-09-01",
      "name": "[concat(parameters('vaultName'), '/', parameters('keyName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('vaultName'))]"
      ],
      "properties": {
        "kty": "[parameters('keyType')]",
        "keyOps": "[parameters('keyOps')]",
        "keySize": "[parameters('keySize')]",
        "curveName": "[parameters('curveName')]"
      }
    }
  ],
  "outputs": {
    "proxyKey": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.KeyVault/vaults/keys', parameters('vaultName'), parameters('keyName')))]"
    }
  }
}
```

템플릿에 정의된 두 개의 리소스는 다음과 같습니다.

- [Microsoft.KeyVault/vaults](/azure/templates/microsoft.keyvault/vaults)
- Microsoft.KeyVault/vaults/keys

추가 Azure Key Vault 템플릿 샘플은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular)에서 찾을 수 있습니다.

## <a name="parameters-and-definitions"></a>매개 변수 및 정의

|매개 변수  |정의  |
|---------|---------|
|**keyOps**  | 키를 사용하여 수행할 수 있는 작업을 지정합니다. 이 매개 변수를 지정하지 않으면 모든 작업을 수행할 수 있습니다. 이 매개 변수에 허용되는 값은 [JWK(JSON Web Key) 사양](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)에 정의된 쉼표로 구분된 키 작업 목록입니다. <br> `["sign", "verify", "encrypt", "decrypt", " wrapKey", "unwrapKey"]` |
|**CurveName**  |  EC 키 형식의 타원 곡선 이름입니다. [JsonWebKeyCurveName](/rest/api/keyvault/createkey/createkey#jsonwebkeycurvename)을 참조하세요. |
|**Kty**  |  만들 키의 유형입니다. 유효한 값은 [JsonWebKeyType](/rest/api/keyvault/createkey/createkey#jsonwebkeytype)을 참조하세요. |
|**태그** | 키/값 쌍 형식의 애플리케이션 특정 메타데이터입니다.  |
|**nbf**  |  키를 사용할 수 없는 시간을 DateTime 개체로 지정합니다. 형식은 Unix 타임스탬프(UTC에서 1970년 1월 1일 Unix Epoch 이후의 초)입니다.  |
|**exp**  |  만료 시간을 DateTime 개체로 지정합니다. 형식은 Unix 타임스탬프(UTC에서 1970년 1월 1일 Unix Epoch 이후의 초)입니다. |

## <a name="deploy-the-template"></a>템플릿 배포
[Azure Portal](../../azure-resource-manager/templates/deploy-portal.md), Azure PowerShell, Azure CLI 또는 REST API를 사용할 수 있습니다. 배포 방법에 대해 알아보려면 [템플릿 배포](../../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

Azure Portal을 사용하여 키 자격 증명 모음 및 키를 확인하거나 다음 Azure CLI 또는 Azure PowerShell 스크립트를 사용하여 생성된 키를 나열할 수 있습니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault key list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultKey -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>리소스 정리

다른 Key Vault 빠른 시작과 자습서는 이 빠른 시작을 기반으로 빌드됩니다. 이후의 빠른 시작 및 자습서를 계속 진행하려는 경우 이러한 리소스를 유지하는 것이 좋습니다.
더 이상 필요 없으면 리소스 그룹을 삭제하고 Key Vault 및 관련 리소스를 삭제합니다. Azure CLI 또는 Azure PowerShell을 사용하여 리소스 그룹을 삭제하려면 다음을 수행합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 ARM 템플릿을 사용하여 키 자격 증명 모음 및 키를 만들고 배포의 유효성을 검사했습니다. Key Vault 및 Azure Resource Manager에 대한 자세한 내용은 아래 문서를 참조하세요.

- [Azure Key Vault 개요](../general/overview.md) 참조
- [Azure Resource Manager](../../azure-resource-manager/management/overview.md)에 대해 자세히 알아보기
- [Azure Key Vault 보안 개요](../general/security-features.md)를 검토하세요.
