---
title: 템플릿이 있는 Key Vault 비밀
description: 배포하는 동안 키 자격 증명 모음의 비밀을 매개 변수로 전달하는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 06/18/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: a5ee223c9cfa2dada3da4f6eb901550c9d4eec9d
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2021
ms.locfileid: "112380719"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Azure Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달

템플릿 또는 매개 변수 파일에 암호 등의 보안 값을 넣는 대신에 배포 중에 [Azure Key Vault](../../key-vault/general/overview.md)에서 값을 검색할 수 있습니다. 매개 변수 파일에서 Key Vault 및 비밀을 참조하여 이 값을 검색합니다. 해당 Key Vault ID만 참조하므로 이 값은 절대 노출되지 않습니다.

> [!IMPORTANT]
> 이 문서에서는 중요한 값을 템플릿 매개 변수로 전달하는 방법을 중점적으로 알아봅니다. 비밀이 매개 변수로 전달되면 키 자격 증명 모음은 배포할 리소스 그룹과 다른 구독에 있을 수 있습니다. 
>
> 가상 머신 속성을 키 자격 증명 모음의 인증서 URL로 설정하는 방법은 이 문서에서 다루지 않습니다. 해당 시나리오의 빠른 시작 템플릿은 [가상 컴퓨터에 Azure Key Vault에서 인증서 설치](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/vm-winrm-keyvault-windows)를 참조하세요.

## <a name="deploy-key-vaults-and-secrets"></a>키 자격 증명 모음 및 비밀 배포

템플릿 배포 중에 키 자격 증명 모음에 액세스하려면 키 자격 증명 모음에서 `enabledForTemplateDeployment`을 `true`로 설정합니다.

키 자격 증명 모음이 이미 있는 경우 템플릿 배포를 허용하는지 확인합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

새 키 자격 증명 모음을 만들고 비밀을 추가하려면 다음을 사용합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name ExampleGroup --location centralus
az keyvault create \
  --name ExampleVault \
  --resource-group ExampleGroup \
  --location centralus \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name ExampleVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name ExampleGroup -Location centralus
New-AzKeyVault `
  -VaultName ExampleVault `
  -resourceGroupName ExampleGroup `
  -Location centralus `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName ExampleVault -Name 'ExamplePassword' -SecretValue $secretvalue
```

---

키 자격 증명 모음의 소유자에게는 비밀을 만들 수 있는 액세스 권한이 자동으로 부여됩니다. 다른 사용자가 비밀을 만들어야 하는 경우 다음을 사용합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

사용자가 비밀을 검색하는 템플릿을 배포하는 경우 액세스 정책이 필요하지 않습니다. 사용자가 비밀을 직접 사용해야 하는 경우에만 액세스 정책에 사용자를 추가합니다. 배포 권한은 다음 섹션에 정의되어 있습니다.

키 자격 증명 모음을 만들고 비밀을 추가하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [CLI를 사용하여 비밀 설정 및 검색](../../key-vault/secrets/quick-create-cli.md)
- [PowerShell을 사용하여 비밀 설정 및 검색](../../key-vault/secrets/quick-create-powershell.md)
- [포털을 사용하여 비밀 설정 및 검색](../../key-vault/secrets/quick-create-portal.md)
- [.NET을 사용하여 비밀 설정 및 검색](../../key-vault/secrets/quick-create-net.md)
- [Node.js를 사용하여 비밀 설정 및 검색](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-deployment-access-to-the-secrets"></a>비밀에 대한 배포 액세스 권한 부여

템플릿을 배포하는 사용자에게는 `Microsoft.KeyVault/vaults/deploy/action` 리소스 그룹 및 키 자격 증명 모음의 범위에 대한 권한이 있어야 합니다. 이 액세스를 확인하면 Azure Resource Manager는 승인되지 않은 사용자가 키 자격 증명 모음에 대한 리소스 ID를 전달하여 비밀에 액세스할 수 없도록 방지합니다. 비밀에 대한 쓰기 권한을 부여하지 않고 사용자에게 배포 액세스 권한을 부여할 수 있습니다.

[소유자](../../role-based-access-control/built-in-roles.md#owner) 및 [참여자](../../role-based-access-control/built-in-roles.md#contributor) 역할 모두 이 액세스 권한을 부여합니다. 키 자격 증명 모음을 만들면 소유자가 되며 권한을 갖게 됩니다.

다른 사용자의 경우 `Microsoft.KeyVault/vaults/deploy/action` 권한을 부여합니다. 다음 절차는 최소의 권한을 가진 역할을 만드는 방법과 해당 역할을 사용자에게 할당하는 방법을 보여 줍니다.

1. 사용자 지정 역할 정의 JSON 파일 만들기

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```

    "00000000-0000-0000-0000-000000000000"를 구독 ID로 바꾸십시오.

2. JSON 파일을 사용하여 새 역할 만들기:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    샘플은 리소스 그룹 수준에서 사용자에게 사용자 지정 역할을 지정합니다.

[관리형 애플리케이션](../managed-applications/overview.md) 템플릿과 함께 키 자격 증명 모음을 사용하는 경우, **어플라이언스 리소스 공급자** 서비스 주체에 액세스를 허용해야 합니다. 자세한 내용은 [Access Key Vault secret when deploying Azure Managed Applications](../managed-applications/key-vault-access.md)(Azure Managed Applications를 배포할 때 Key Vault 비밀 액세스)를 참조하세요.

## <a name="reference-secrets-with-static-id"></a>정적 ID로 비밀 참조

이 방식을 사용할 때는 템플릿이 아닌 매개 변수 파일에서 Key Vault를 참조합니다. 다음 이미지에서는 매개 변수 파일이 암호를 참조하고 템플릿에 해당 값을 전달하는 방법을 보여줍니다.

![Resource Manager Key Vault 통합 정적 ID 다이어그램](./media/key-vault-parameter/statickeyvault.png)

[자습서: Resource Manager 템플릿 배포 시 Azure Key Vault 통합](./template-tutorial-use-key-vault.md)에서는 이 방법을 사용합니다.

예를 들어 다음 템플릿은 관리자 암호를 포함하는 SQL 서버를 배포합니다. 암호 매개 변수는 보안 문자열로 설정됩니다. 하지만 이 템플릿은 해당 값이 제공되는 위치를 지정하지는 않습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('sqlServerName')]",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

이제 위 템플릿용으로 매개 변수 파일을 만들어야 합니다. 매개 변수 파일에서 템플릿의 매개 변수 이름과 일치하는 매개 변수를 지정합니다. 매개 변수 값으로는 Key Vault의 비밀을 참조합니다. Key Vault의 리소스 식별자와 비밀의 이름을 전달하여 비밀을 참조합니다.

다음 매개 변수 파일에는 키 자격 증명 모음 비밀이 이미 있어야 하고, 리소스 ID에 정적 값을 제공합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "value": "exampleadmin"
    },
    "adminPassword": {
      "reference": {
        "keyVault": {
          "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
        },
        "secretName": "ExamplePassword"
      }
    },
    "sqlServerName": {
      "value": "<your-server-name>"
    }
  }
}
```

현재 버전이 아닌 비밀의 버전을 사용해야 할 경우 `secretVersion` 속성을 포함합니다.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

템플릿을 배포하고 매개 변수 파일을 전달합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az deployment group create \
  --resource-group SqlGroup \
  --template-uri <template-file-URI> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <template-file-URI> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="reference-secrets-with-dynamic-id"></a>동적 ID로 비밀 참조

이전 섹션에서는 매개 변수에서 Key Vault 비밀의 정적 리소스 ID를 전달하는 방법을 살펴보았습니다. 일부 시나리오에서는 현재 배포를 기반으로 달라지는 키 자격 증명 모음 비밀을 참조해야 합니다. 매개 변수 파일에 참조 매개 변수를 만드는 대신 템플릿에 매개 변수 값을 전달할 수도 있습니다. 해결 방법은 링크된 템플릿을 사용하여 키 자격 증명 모음 비밀의 리소스 ID를 동적으로 생성하는 것입니다.

매개 변수 파일에 템플릿 식이 허용되지 않기 때문에 매개 변수 파일에 리소스 ID를 동적으로 생성할 수 없습니다.

부모 템플릿에서 중첩된 템플릿을 추가하고 동적으로 생성된 리소스 ID를 포함하는 매개 변수에 전달합니다. 다음 이미지에서는 연결된 템플릿의 매개 변수가 암호를 참조하는 방법을 보여줍니다.

![동적 ID](./media/key-vault-parameter/dynamickeyvault.png)

다음 템플릿은 동적으로 키 자격 증명 모음 ID를 만들고 매개 변수로 전달합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "The location where the resources will be deployed."
        }
      },
      "vaultName": {
        "type": "string",
        "metadata": {
          "description": "The name of the keyvault that contains the secret."
        }
      },
      "secretName": {
        "type": "string",
        "metadata": {
          "description": "The name of the secret."
        }
      },
      "vaultResourceGroupName": {
        "type": "string",
        "metadata": {
          "description": "The name of the resource group that contains the keyvault."
        }
      },
      "vaultSubscription": {
        "type": "string",
        "defaultValue": "[subscription().subscriptionId]",
        "metadata": {
          "description": "The name of the subscription that contains the keyvault."
        }
      }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

## <a name="next-steps"></a>다음 단계

- 키 자격 증명 모음에 대한 일반적인 정보는 [Azure Key Vault란?](../../key-vault/general/overview.md)을 참조하세요.
- 키 비밀을 참조하는 전체 예제는 GitHub의 [키 자격 증명 모음 예제](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)를 참조하세요.
- 키 자격 증명 모음의 보안 값 전달을 다루는 Microsoft Learn 모듈은 [고급 ARM 템플릿 기능을 사용하여 복잡한 클라우드 배포 관리](/learn/modules/manage-deployments-advanced-arm-template-features/)를 참조하세요.
