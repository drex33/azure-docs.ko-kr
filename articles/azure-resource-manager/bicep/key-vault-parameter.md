---
title: Bicep이 있는 Key Vault 비밀
description: Bicep 배포 중에 키 자격 증명 모음의 비밀을 매개 변수로 전달하는 방법을 보여줍니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: e940a812b4e010e9499a9a85cfd400b679d43781
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2021
ms.locfileid: "112376309"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-bicep-deployment"></a>Azure Key Vault를 사용하여 Bicep 배포 중에 보안 매개 변수 값 전달

Bicep 파일 또는 매개 변수 파일에 암호 등의 보안 값을 직접 입력하는 대신 배포 중에 [Azure Key Vault](../../key-vault/general/overview.md)에서 값을 검색할 수 있습니다. [모듈](./modules.md)에 `secure:true` 한정자가 포함된 `string` 매개 변수가 필요한 경우 [getSecret 함수](bicep-functions-resource.md#getsecret)를 사용하여 Key Vault 비밀을 가져올 수 있습니다. 해당 Key Vault ID만 참조하므로 이 값은 절대 노출되지 않습니다.

> [!IMPORTANT]
> 이 문서에서는 중요한 값을 템플릿 매개 변수로 전달하는 방법을 중점적으로 알아봅니다. 비밀이 매개 변수로 전달되면 Key Vault는 배포할 리소스 그룹과 다른 구독에 있을 수 있습니다. 
>
> 가상 머신 속성을 Key Vault의 인증서 URL로 설정하는 방법은 이 문서에서 다루지 않습니다. 해당 시나리오의 빠른 시작 템플릿은 [가상 컴퓨터에 Azure Key Vault에서 인증서 설치](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/vm-winrm-keyvault-windows)를 참조하세요.

## <a name="deploy-key-vaults-and-secrets"></a>키 자격 증명 모음 및 비밀 배포

Bicep 배포 중에 키 자격 증명 모음에 액세스하려면 키 자격 증명 모음에서 `enabledForTemplateDeployment`를 `true`로 설정합니다.

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

키 자격 증명 모음의 소유자에게는 비밀을 만들 수 있는 액세스 권한이 자동으로 부여됩니다. 비밀을 사용하는 사용자가 키 자격 증명 모음의 소유자가 아닌 경우 다음을 사용하여 액세스 권한을 부여합니다.

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

키 자격 증명 모음을 만들고 비밀을 추가하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [CLI를 사용하여 비밀 설정 및 검색](../../key-vault/secrets/quick-create-cli.md)
- [PowerShell을 사용하여 비밀 설정 및 검색](../../key-vault/secrets/quick-create-powershell.md)
- [포털을 사용하여 비밀 설정 및 검색](../../key-vault/secrets/quick-create-portal.md)
- [.NET을 사용하여 비밀 설정 및 검색](../../key-vault/secrets/quick-create-net.md)
- [Node.js를 사용하여 비밀 설정 및 검색](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>비밀 액세스 권한 부여

Bicep 파일을 배포하는 사용자에게는 리소스 그룹 및 키 자격 증명 모음의 범위에 대한 `Microsoft.KeyVault/vaults/deploy/action` 권한이 있어야 합니다. [소유자](../../role-based-access-control/built-in-roles.md#owner) 및 [참여자](../../role-based-access-control/built-in-roles.md#contributor) 역할 모두 이 액세스 권한을 부여합니다. 키 자격 증명 모음을 만들면 소유자가 되며 권한을 갖게 됩니다.

다음 절차는 최소의 권한을 가진 역할을 만드는 방법과 해당 역할 사용자에게 할당하는 방법을 보여 줍니다.

1. 사용자 지정 역할 정의 JSON 파일 만들기

    ```json
    {
      "Name": "Key Vault Bicep deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a Bicep file with the access to the secrets in the Key Vault.",
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

[관리형 애플리케이션](../managed-applications/overview.md)용 Bicep 파일과 함께 키 자격 증명 모음을 사용하는 경우, **어플라이언스 리소스 공급자** 서비스 주체에 대한 액세스 권한을 부여해야 합니다. 자세한 내용은 [Access Key Vault secret when deploying Azure Managed Applications](../managed-applications/key-vault-access.md)(Azure Managed Applications를 배포할 때 Key Vault 비밀 액세스)를 참조하세요.

## <a name="use-getsecret-function"></a>getSecret 함수 사용

[getSecret 함수](./bicep-functions-resource.md#getsecret)를 사용하여 Key Vault 비밀을 가져오고 모듈의 `string` 매개 변수에 값을 전달할 수 있습니다. `getSecret` 함수는 `Microsoft.KeyVault/vaults` 리소스에 대해서만 호출할 수 있으며 `@secure()` 데코레이터가 있는 매개 변수에서만 사용할 수 있습니다.

다음 Bicep 파일은 Azure SQL 서버를 만듭니다. `adminPassword` 매개 변수에는 `@secure()` 데코레이터가 있습니다.

```bicep
param sqlServerName string
param adminLogin string

@secure()
param adminPassword string

resource sqlServer 'Microsoft.Sql/servers@2020-11-01-preview' = {
  name: sqlServerName
  location: resourceGroup().location
  properties: {
    administratorLogin: adminLogin
    administratorLoginPassword: adminPassword
    version: '12.0'
  }
}
```

주 Bicep 파일과 동일한 디렉터리에 있는 파일 이름이 *sql.bicep* 인 경우 앞의 Bicep 파일을 모듈로 사용해 보겠습니다.

다음 Bicep 파일은 sql.bicep을 모듈로 사용합니다.  Bicep 파일은 기존 키 자격 증명 모음을 참조하고 `getSecret` 함수를 호출하여 키 자격 증명 모음 비밀을 검색한 다음, 값을 매개 변수로 모듈에 전달합니다.

```bicep
param sqlServerName string
param adminLogin string

param subscriptionId string
param kvResourceGroup string
param kvName string

resource kv 'Microsoft.KeyVault/vaults@2019-09-01' existing = {
  name: kvName
  scope: resourceGroup(subscriptionId, kvResourceGroup )
}

module sql './sql.bicep' = {
  name: 'deploySQL'
  params: {
    sqlServerName: sqlServerName
    adminLogin: adminLogin
    adminPassword: kv.getSecret('vmAdminPassword')
  }
}
```

## <a name="reference-secrets-in-parameter-file"></a>매개 변수 파일의 참조 비밀

모듈을 사용하지 않으려면 매개 변수 파일에서 직접 Key Vault를 참조할 수 있습니다. 다음 이미지에서는 매개 변수 파일이 비밀을 참조하고 Bicep 파일에 해당 값을 전달하는 방법을 보여줍니다.

![Resource Manager 키 자격 증명 모음 통합 다이어그램](./media/key-vault-parameter/statickeyvault.png)

다음 Bicep 파일은 관리자 암호를 포함하는 SQL 서버를 배포합니다. 암호 매개 변수는 보안 문자열로 설정됩니다. 하지만 Bicep은 해당 값이 제공되는 위치를 지정하지는 않습니다.

```bicep
param adminLogin string

@secure()
param adminPassword string

param sqlServerName string

resource sqlServer 'Microsoft.Sql/servers@2020-11-01-preview' = {
  name: sqlServerName
  location: resourceGroup().location
  properties: {
    administratorLogin: adminLogin
    administratorLoginPassword: adminPassword
    version: '12.0'
  }
}
```

---

이제 앞의 Bicep 파일에 대한 매개 변수 파일을 만듭니다. 매개 변수 파일에서 Bicep 파일의 매개 변수 이름과 일치하는 매개 변수를 지정합니다. 매개 변수 값으로는 Key Vault의 비밀을 참조합니다. Key Vault의 리소스 식별자와 비밀의 이름을 전달하여 비밀을 참조합니다.

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
  --template-file <Bicep-file> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile <Bicep-file> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="next-steps"></a>다음 단계

- 키 자격 증명 모음에 대한 일반적인 정보는 [Azure Key Vault란?](../../key-vault/general/overview.md)을 참조하세요.
- 키 비밀을 참조하는 전체 예제는 GitHub의 [키 자격 증명 모음 예제](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)를 참조하세요.
- 키 자격 증명 모음의 보안 값 전달을 다루는 Microsoft Learn 모듈은 [고급 ARM 템플릿 기능을 사용하여 복잡한 클라우드 배포 관리](/learn/modules/manage-deployments-advanced-arm-template-features/)를 참조하세요.
