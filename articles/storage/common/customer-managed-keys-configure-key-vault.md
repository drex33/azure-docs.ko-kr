---
title: Azure Key Vault에 저장된 고객 관리형 키를 사용하여 암호화 구성
titleSuffix: Azure Storage
description: Azure Portal, PowerShell 또는 Azure CLI를 사용하여 Azure Key Vault에 저장된 고객 관리형 키로 Azure Storage 암호화를 구성하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/16/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 7377f0e296f571640aba35639972e66a60c6dd0f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114444652"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault"></a>Azure Key Vault에 저장된 고객 관리형 키를 사용하여 암호화 구성

Azure Storage는 미사용 스토리지 계정의 모든 데이터를 암호화합니다. 기본적으로 데이터는 Microsoft 관리형 키로 암호화됩니다. 암호화 키에 대한 추가 제어를 위해 사용자 고유의 키를 관리할 수 있습니다. 고객 관리형 키는 Azure Key Vault 또는 Key Vault 관리형 HSM(하드웨어 보안 모델)에 저장되어 있어야 합니다.

이 문서에서는 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 키 자격 증명 모음에 저장된 고객 관리형 키로 암호화를 구성하는 방법에 대해 알아봅니다. 관리형 HSM에 저장된 고객 관리형 키로 암호화를 구성하는 방법에 대한 자세한 내용은 [Azure Key Vault 관리형 HSM에 저장된 고객 관리형 키로 암호화 구성](customer-managed-keys-configure-key-vault-hsm.md)을 참조하세요.

> [!NOTE]
> Azure Key Vault 및 Azure Key Vault 관리형 HSM은 구성에 대해 동일한 API 및 관리 인터페이스를 지원합니다.

## <a name="configure-a-key-vault"></a>키 자격 증명 모음 구성

신규 또는 기존 키 자격 증명 모음을 사용하여 고객 관리형 키를 저장할 수 있습니다. 스토리지 계정 및 키 자격 증명 모음은 동일한 지역에 있어야 하지만 서로 다른 구독에 있을 수도 있습니다.

Azure Storage 암호화에서 고객 관리형 키를 사용하려면 키 자격 증명 모음에 대해 일시 삭제 및 제거 보호를 모두 사용하도록 설정해야 합니다. 일시 삭제는 새 키 자격 증명 모음을 만들 때 기본적으로 사용하도록 설정되며 해제할 수 없습니다. 키 자격 증명 모음을 만들 때 또는 만든 후에 제거 보호를 사용하도록 설정할 수 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal을 사용하여 키 자격 증명 모음을 만드는 방법은 [빠른 시작: Azure Portal을 사용하여 키 자격 증명 모음 만들기](../../key-vault/general/quick-create-portal.md)를 참조하세요. 키 자격 증명 모음을 만들 때 다음 그림에 표시된 것처럼 **보호 제거 사용** 을 선택합니다.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/configure-key-vault-portal.png" alt-text="키 자격 증명 모음을 만들 때 보호 제거를 사용하도록 설정하는 방법을 보여 주는 스크린샷":::

기존 키 자격 증명 모음에 보호 제거를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal에서 키 자격 증명 모음으로 이동합니다.
1. **설정** 아래에서 **속성** 을 선택합니다.
1. **보호 제거** 섹션에서 **보호 제거 사용** 을 선택합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 새 키 자격 증명 모음을 만들려면 버전 2.0.0 이상의 [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) PowerShell 모듈을 설치합니다. 그런 다음 [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)를 호출하여 새 키 자격 증명 모음을 만듭니다. Az.KeyVault 모듈의 2.0.0 이상 버전을 사용하는 경우 새 키 자격 증명 모음을 만들 때 일시 삭제가 기본적으로 사용하도록 설정됩니다.

다음 예제에서는 일시 삭제 및 제거 보호를 모두 사용하도록 설정하여 새 키 자격 증명 모음을 만듭니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

PowerShell을 사용하여 기존 키 자격 증명 모음에 보호 제거를 사용하도록 설정하는 방법을 알아보려면 [PowerShell에서 일시 삭제를 사용하는 방법](../../key-vault/general/key-vault-recovery.md)을 참조하세요.

다음으로 시스템 할당 관리 ID를 스토리지 계정에 할당합니다. 이 관리 ID를 사용하여 스토리지 계정에게 키 자격 증명 모음에 액세스할 수 있는 권한을 부여합니다. 시스템 할당 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.

PowerShell을 사용하여 관리 ID를 할당하려면 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)를 호출합니다.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

마지막으로, 스토리지 계정이 액세스 권한을 가지도록 키 자격 증명 모음에 대한 액세스 정책을 구성합니다. 이 단계에서는 이전에 스토리지 계정에 할당한 관리 ID를 사용합니다.

키 자격 증명 모음에 대한 액세스 정책을 설정하려면 [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)를 호출합니다.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 새 키 자격 증명 모음을 만들려면 [az keyvault create](/cli/azure/keyvault#az_keyvault_create)를 호출합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-purge-protection
```

Azure CLI를 사용하여 기존 키 자격 증명 모음에 보호 제거를 사용하도록 설정하는 방법은 [CLI에서 일시 삭제를 사용하는 방법](../../key-vault/general/key-vault-recovery.md)을 참조하세요.

다음으로 시스템 할당 관리 ID를 스토리지 계정에 할당합니다. 이 관리 ID를 사용하여 스토리지 계정에게 키 자격 증명 모음에 액세스할 수 있는 권한을 부여합니다. 시스템 할당 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.

Azure CLI를 사용하여 관리 ID를 할당하려면 [az storage account update](/cli/azure/storage/account#az_storage_account_update)를 호출합니다.

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

마지막으로, 스토리지 계정이 액세스 권한을 가지도록 키 자격 증명 모음에 대한 액세스 정책을 구성합니다. 이 단계에서는 이전에 스토리지 계정에 할당한 관리 ID를 사용합니다.

키 자격 증명 모음에 대한 액세스 정책을 설정하려면 [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy)를 호출합니다.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

---

## <a name="add-a-key"></a>키 추가

다음으로 키 자격 증명 모음에 키를 추가합니다.

Azure Storage 암호화는 2048, 3072, 4096 크기의 RSA 및 RSA HSM 키를 지원합니다. 키에 대한 자세한 내용은 [키 정보](../../key-vault/keys/about-keys.md)를 참조하세요.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal을 사용하여 키를 추가하는 방법은 [빠른 시작: Azure Portal을 사용하여 Azure Key Vault에서 키 설정 및 검색](../../key-vault/keys/quick-create-portal.md)을 참조하세요.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 키를 추가하려면 [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)를 호출합니다. 대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 바꾸고 위의 예제에 정의된 변수를 사용해야 합니다.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName `
    -Name <key> `
    -Destination 'Software'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 키를 추가하려면 [az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create)를 호출합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

---

## <a name="configure-encryption-with-customer-managed-keys"></a>고객 관리형 키로 암호화 구성

다음으로 Azure Key Vault에서 고객 관리형 키를 사용하도록 Azure Storage 계정을 구성하고 스토리지 계정과 연결할 키를 지정합니다.

고객 관리형 키를 사용하여 암호화를 구성하는 경우 연결된 키 자격 증명 모음에서 새 버전을 사용할 수 있을 때마다 Azure Storage 암호화에 사용되는 키 버전을 자동으로 업데이트하도록 선택할 수 있습니다. 또는, 키 버전이 수동으로 업데이트될 때까지 암호화에 사용할 키 버전을 명시적으로 지정할 수 있습니다.

> [!NOTE]
> 키를 회전하려면 Azure Key Vault에서 새 버전의 키를 만듭니다. Azure Storage는 Azure Key Vault 키 회전을 처리하지 않으므로 키를 수동으로 회전하거나 일정에 따라 회전하는 함수를 만들어야 합니다.

### <a name="configure-encryption-for-automatic-updating-of-key-versions"></a>암호화에서 키 버전 자동 업데이트 구성

Azure Storage는 암호화에 사용되는 고객 관리형 키를 최신 키 버전을 사용하도록 자동으로 업데이트할 수 있습니다. Azure Key Vault에서 고객 관리형 키를 회전하는 경우 Azure Storage는 자동으로 암호화에 최신 버전의 키를 사용하기 시작합니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 키 버전 자동 업데이트를 사용하도록 고객 관리형 키를 구성하려면 다음 단계를 수행합니다.

1. 스토리지 계정으로 이동합니다.
1. 스토리지 계정에 대한 **설정** 블레이드에서 **암호화** 를 클릭합니다. 기본적으로 키 관리는 다음 이미지와 같이 **Microsoft 관리 키** 로 설정됩니다.

    ![암호화 옵션을 보여 주는 포털 스크린샷](./media/customer-managed-keys-configure-key-vault/portal-configure-encryption-keys.png)

1. **고객 관리형 키** 옵션을 선택합니다.
1. **Key Vault에서 선택** 옵션을 선택합니다.
1. **키 자격 증명 모음 및 키 선택** 을 선택합니다.
1. 사용하려는 키가 포함된 키 자격 증명 모음을 선택합니다.
1. 키 자격 증명 모음에서 키를 선택합니다.

   ![키 자격 증명 모음 및 키를 선택하는 방법을 보여 주는 스크린샷](./media/customer-managed-keys-configure-key-vault/portal-select-key-from-key-vault.png)

1. 변경 내용을 저장합니다.

키를 지정하면 Azure Portal은 키 버전 자동 업데이트가 사용하도록 설정되어 있음을 나타내고 현재 암호화에 사용 중인 키 버전을 표시합니다.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/portal-auto-rotation-enabled.png" alt-text="키 버전 자동 업데이트가 사용하도록 설정되었음을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 고객 관리형 키에서 키 버전 자동 업데이트를 구성하려면 [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) 모듈 버전 2.0.0 이상을 설치합니다.

고객 관리형 키의 키 버전을 자동으로 업데이트하려면 스토리지 계정의 고객 관리형 키를 사용하도록 암호화를 구성할 때 키 버전을 생략합니다. 다음 예제와 같이 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)를 호출하여 스토리지 계정의 암호화 설정을 업데이트하고 **-KeyvaultEncryption** 옵션을 포함하여 스토리지 계정에 고객 관리형 키를 사용하도록 설정합니다.

대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 바꾸고 위의 예제에 정의된 변수를 사용해야 합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 고객 관리형 키에서 키 버전 자동 업데이트를 구성하려면 [Azure CLI 버전 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) 이상을 설치합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

고객 관리형 키의 키 버전을 자동으로 업데이트하려면 스토리지 계정의 고객 관리형 키를 사용하도록 암호화를 구성할 때 키 버전을 생략합니다. 다음 예제와 같이 [az storage account update](/cli/azure/storage/account#az_storage_account_update)를 호출하여 스토리지 계정의 암호화 설정을 업데이트합니다. `--encryption-key-source` 매개 변수를 포함하고 이를 `Microsoft.Keyvault`로 설정하여 계정에서 고객 관리형 키를 사용하도록 설정합니다.

대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

---

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>암호화에서 키 버전 수동 업데이트 구성

키 버전을 수동으로 업데이트하려면 암호화에서 고객 관리형 키를 구성할 때 버전을 명시적으로 지정합니다. 이 경우 키 자격 증명 모음에서 새 버전이 만들어지면 Azure Storage에서 키 버전을 자동으로 업데이트하지 않습니다. 새 키 버전을 사용하려면 Azure Storage 암호화에 사용되는 버전을 수동으로 업데이트해야 합니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 키 버전 수동 업데이트를 사용하도록 고객 관리형 키를 구성하려면 버전을 포함하여 키 URI를 지정합니다. 키를 URI로 지정하려면 다음 단계를 수행합니다.

1. Azure Portal에서 키 URI를 찾으려면 키 자격 증명 모음으로 이동하여 **키** 설정을 선택합니다. 원하는 키를 선택한 다음, 키를 클릭하여 버전을 확인합니다. 키 버전을 선택하여 해당 버전의 설정을 봅니다.
1. URI를 제공하는 **키 식별자** 필드의 값을 복사합니다.

    ![키 자격 증명 모음 키 URI를 보여 주는 스크린샷](media/customer-managed-keys-configure-key-vault/portal-copy-key-identifier.png)

1. 스토리지 계정에 대한 **암호화 키** 설정에서 **키 URI 입력** 옵션을 선택합니다.
1. 복사한 URI를 **키 URI** 필드에 붙여넣습니다. 키 버전 자동 업데이트를 사용하도록 설정하려면 URI에서 키 버전을 생략합니다.

   ![키 URI를 입력하는 방법을 보여 주는 스크린샷](./media/customer-managed-keys-configure-key-vault/portal-specify-key-uri.png)

1. 키 자격 증명 모음을 포함하는 구독을 지정합니다.
1. 변경 내용을 저장합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

키 버전 수동 업데이트를 사용하도록 고객 관리형 키를 구성하려면 스토리지 계정의 암호화를 구성할 때 키 버전을 명시적으로 제공합니다. 다음 예제와 같이 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)를 호출하여 스토리지 계정의 암호화 설정을 업데이트하고 **-KeyvaultEncryption** 옵션을 포함하여 스토리지 계정에 고객 관리형 키를 사용하도록 설정합니다.

대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 바꾸고 위의 예제에 정의된 변수를 사용해야 합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

키 버전을 수동으로 업데이트할 때 새 버전을 사용하도록 스토리지 계정의 암호화 설정을 업데이트해야 합니다. 먼저 [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey)를 호출하여 최신 버전의 키를 가져옵니다. 그런 다음 이전 예제와 같이 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)를 호출하여 새 버전의 키를 사용하도록 스토리지 계정의 암호화 설정을 업데이트합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

키 버전 수동 업데이트를 사용하도록 고객 관리형 키를 구성하려면 스토리지 계정의 암호화를 구성할 때 키 버전을 명시적으로 제공합니다. 다음 예제와 같이 [az storage account update](/cli/azure/storage/account#az_storage_account_update)를 호출하여 스토리지 계정의 암호화 설정을 업데이트합니다. `--encryption-key-source` 매개 변수를 포함하고 이를 `Microsoft.Keyvault`로 설정하여 계정에서 고객 관리형 키를 사용하도록 설정합니다.

대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

키 버전을 수동으로 업데이트할 때 새 버전을 사용하도록 스토리지 계정의 암호화 설정을 업데이트해야 합니다. 먼저 [az keyvault show](/cli/azure/keyvault#az_keyvault_show)를 호출하여 키 자격 증명 모음 URI를 쿼리하고 [az keyvault key list-versions](/cli/azure/keyvault/key#az_keyvault_key_list-versions)를 호출하여 키 버전을 쿼리합니다. 그런 다음 이전 예제와 같이 [az storage account update](/cli/azure/storage/account#az_storage_account_update)를 호출하여 새 버전의 키를 사용하도록 스토리지 계정의 암호화 설정을 업데이트합니다.

---

## <a name="change-the-key"></a>키 변경

Azure Storage 암호화에 사용하는 키는 언제든지 변경할 수 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal을 사용하여 키를 변경하려면 다음 단계를 수행합니다.

1. 스토리지 계정으로 이동하여 **암호화** 설정을 표시합니다.
1. 키 자격 증명 모음을 선택하고 새 키를 선택합니다.
1. 변경 내용을 저장합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 키를 변경하려면 [고객 관리형 키로 암호화 구성](#configure-encryption-with-customer-managed-keys)에 표시된 대로 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)를 호출하고 새 키 이름 및 버전을 제공합니다. 새 키가 다른 키 자격 증명 모음에 있으면 키 자격 증명 모음 URI도 업데이트해야 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 키를 변경하려면 [고객 관리형 키로 암호화 구성](#configure-encryption-with-customer-managed-keys)에 표시된 대로 [az storage account update](/cli/azure/storage/account#az_storage_account_update)를 호출하고 새 키 이름 및 버전을 제공합니다. 새 키가 다른 키 자격 증명 모음에 있으면 키 자격 증명 모음 URI도 업데이트해야 합니다.

---

## <a name="revoke-customer-managed-keys"></a>고객 관리형 키 철회

고객 관리형 키를 철회하면 스토리지 계정과 키 자격 증명 모음 간의 연결이 제거됩니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal을 사용하여 고객 관리형 키를 철회하려면 [고객 관리형 키 사용 안 함](#disable-customer-managed-keys)에 설명된 대로 키를 사용하지 않도록 설정합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

키 자격 증명 모음 액세스 정책을 제거하여 고객 관리형 키를 철회할 수 있습니다. PowerShell을 사용하여 고객 관리형 키를 철회하려면 다음 예제와 같이 [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) 명령을 호출합니다. 대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 바꾸고 위의 예제에 정의된 변수를 사용해야 합니다.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

키 자격 증명 모음 액세스 정책을 제거하여 고객 관리형 키를 철회할 수 있습니다. Azure CLI를 사용하여 고객 관리형 키를 철회하려면 다음 예제와 같이 [az keyvault delete-policy](/cli/azure/keyvault#az_keyvault_delete_policy) 명령을 호출합니다. 대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 바꾸고 위의 예제에 정의된 변수를 사용해야 합니다.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

---

## <a name="disable-customer-managed-keys"></a>고객 관리형 키 사용 안 함

고객 관리형 키를 사용하지 않도록 설정하면 스토리지 계정이 Microsoft 관리 키로 다시 암호화됩니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 고객 관리형 키를 사용하지 않도록 설정하려면 다음 단계를 수행합니다.

1. 스토리지 계정으로 이동하여 **암호화** 설정을 표시합니다.
1. **사용자 고유 키 사용** 설정 옆에 있는 확인란의 선택을 취소합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 고객 관리형 키를 사용하지 않도록 설정하려면 다음 예제와 같이 `-StorageEncryption` 옵션을 사용하여 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 명령을 호출합니다. 대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 바꾸고 위의 예제에 정의된 변수를 사용해야 합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 고객 관리형 키를 사용하지 않도록 설정하려면 다음 예제와 같이 [az storage account update](/cli/azure/storage/account#az_storage_account_update) 명령을 호출하고 `--encryption-key-source parameter`를 `Microsoft.Storage`로 설정합니다. 대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 바꾸고 위의 예제에 정의된 변수를 사용해야 합니다.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

---

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure Storage 암호화](storage-service-encryption.md)
- [Azure Storage 암호화용 고객 관리형 키](customer-managed-keys-overview.md)
- [Azure Key Vault 관리형 HSM에 저장된 고객 관리형 키를 사용하여 암호화 구성](customer-managed-keys-configure-key-vault-hsm.md).
