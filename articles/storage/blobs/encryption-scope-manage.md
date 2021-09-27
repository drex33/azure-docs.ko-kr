---
title: 암호화 범위 만들기 및 관리
description: 암호화 범위를 만들어 컨테이너 또는 BLOB 수준에서 BLOB 데이터를 격리하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.date: 05/10/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 283fd9dc199f86992be9f3952cf3a82b916ae56c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570914"
---
# <a name="create-and-manage-encryption-scopes"></a>암호화 범위 만들기 및 관리

암호화 범위를 사용하면 개별 Blob 또는 컨테이너 수준에서 암호화를 관리할 수 있습니다. 암호화 범위를 사용하여 동일한 스토리지 계정에 있지만 다른 고객에 속한 데이터 간에 보안 경계를 만들 수 있습니다. 암호화 범위에 대한 자세한 내용은 [BLOB 스토리지의 암호화 범위](encryption-scope-overview.md)를 참조하십시오.

이 문서에서는 암호화 범위를 만드는 방법을 설명합니다. 또한 BLOB 또는 컨테이너를 만들 때 암호화 범위를 지정하는 방법을 기술합니다.

## <a name="create-an-encryption-scope"></a>암호화 범위 만들기

Azure Key Vault 또는 Azure Key Vault 관리형 HSM(Hardware Security Model)에 저장된 Microsoft 관리형 키 또는 고객 관리형 키로 보호되는 암호화 범위를 만들 수 있습니다. 고객 관리형 키를 사용하여 암호화 범위를 만들려면 먼저 키 자격 증명 모음 또는 관리되는 HSM을 만들고 범위에 사용하려는 키를 추가해야 합니다. 키 자격 증명 모음 또는 관리되는 HSM은 제거 보호를 사용하도록 설정해야 하며 스토리지 계정과 동일한 지역에 있어야 합니다.

암호화 범위는 만들 때 자동으로 사용하도록 설정됩니다. 암호화 범위를 만든 후에는 BLOB를 만들 때 암호화 범위를 지정할 수 있습니다. 컨테이너를 만들 때 기본 암호화 범위를 지정할 수도 있습니다. 이 범위는 컨테이너의 모든 BLOB에 자동으로 적용됩니다.

# <a name="portal"></a>[포털](#tab/portal)

Azure Portal에서 암호화 범위를 만드는 방법은 다음과 같습니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **암호화** 설정을 선택합니다.
1. **암호화 범위** 탭을 선택합니다.
1. **추가** 단추를 클릭하여 새 암호화 범위를 추가합니다.
1. **암호화 범위 만들기** 창에서 새 범위의 이름을 입력합니다.
1. **Microsoft 관리형 키** 또는 **고객 관리형 키** 중에서 원하는 유형의 암호화 키 지원을 선택합니다.
    - **Microsoft 관리형 키** 를 선택한 경우 **만들기** 를 클릭하여 암호화 범위를 만듭니다.
    - **고객 관리형 키** 를 선택한 경우 구독을 선택하고 Key Vault 또는 관리 HSM과 이 암호화 범위에 사용할 키를 지정합니다.
1. 스토리지 계정에 대해 인프라 암호화를 사용하도록 설정한 경우 새 암호화 범위에 대해 자동으로 사용하도록 설정됩니다. 그렇지 않으면 암호화 범위에 대해 인프라 암호화를 사용하도록 설정할지 여부를 선택할 수 있습니다.

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="Azure Portal에서 암호화 범위를 만드는 방법을 보여주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 암호화 범위를 만들려면 [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) PowerShell 모듈, 버전 3.4.0 이상을 설치합니다.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Microsoft 관리형 키로 보호되는 암호화 범위 만들기

Microsoft 관리형 키로 보호되는 새 암호화 범위를 만들려면 `-StorageEncryption` 매개 변수를 사용하여 [New-AzStorageEncryptionScope](/powershell/module/az.storage/new-azstorageencryptionscope) 명령을 호출합니다.

스토리지 계정에 대해 인프라 암호화를 사용하도록 설정한 경우 새 암호화 범위에 대해 자동으로 사용하도록 설정됩니다. 그렇지 않으면 암호화 범위에 대해 인프라 암호화를 사용하도록 설정할지 여부를 선택할 수 있습니다. 인프라 암호화가 사용하도록 설정된 새 범위를 만들려면 `-RequireInfrastructureEncryption` 매개 변수를 포함합니다.

예제의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$scopeName1 = "customer1scope"

New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -StorageEncryption
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>고객 관리형 키로 보호되는 암호화 범위 만들기

키 자격 증명 모음 또는 관리되는 HSM에 저장된 고객 관리형 키로 보호되는 새 암호화 범위를 만들려면 먼저 스토리지 계정에 대한 고객 관리형 키를 구성합니다. 관리되는 ID를 스토리지 계정에 할당한 뒤 관리되는 ID를 사용하여 키 자격 증명 모음 또는 관리되는 HSM에 대한 액세스 정책을 구성함으로써 스토리지 계정이 액세스 권한을 가질 수 있도록 해야 합니다.

암호화 범위와 함께 사용할 고객 관리형 키를 구성하려면 키 자격 증명 모음 또는 관리되는 HSM에서 제거 보호를 사용하도록 설정해야 합니다. 키 자격 증명 모음 또는 관리되는 HSM은 스토리지 계정과 같은 지역에 있어야 합니다.

예제의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$keyVaultName = "<key-vault>"
$keyUri = "<key-uri>"
$scopeName2 = "customer2scope"

# Assign a system managed identity to the storage account.
$storageAccount = Set-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -AssignIdentity

# Configure the access policy for the key vault.
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

그런 다음 `-KeyvaultEncryption` 매개 변수를 사용하여 [New-AzStorageEncryptionScope](/powershell/module/az.storage/new-azstorageencryptionscope) 명령을 호출하고 키 URI를 지정합니다. 키 URI에 키 버전을 포함하는 것은 선택 사항입니다. 키 버전을 생략하면 암호화 범위는 가장 최신 키 버전을 자동으로 사용합니다. 키 버전을 포함하는 경우 다른 버전을 사용하려면 수동으로 키 버전을 업데이트해야 합니다.

스토리지 계정에 대해 인프라 암호화를 사용하도록 설정한 경우 새 암호화 범위에 대해 자동으로 사용하도록 설정됩니다. 그렇지 않으면 암호화 범위에 대해 인프라 암호화를 사용하도록 설정할지 여부를 선택할 수 있습니다. 인프라 암호화가 사용하도록 설정된 새 범위를 만들려면 `-RequireInfrastructureEncryption` 매개 변수를 포함합니다.

예제의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI를 사용하여 암호화 범위를 만들려면 먼저 Azure CLI 버전 2.20.0 이상을 설치합니다.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Microsoft 관리형 키로 보호되는 암호화 범위 만들기

Microsoft 관리형 키로 보호되는 새 암호화 범위를 만들려면 [az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_create) 명령을 호출하여 `--key-source` 매개 변수를 `Microsoft.Storage`로 지정합니다.

스토리지 계정에 대해 인프라 암호화를 사용하도록 설정한 경우 새 암호화 범위에 대해 자동으로 사용하도록 설정됩니다. 그렇지 않으면 암호화 범위에 대해 인프라 암호화를 사용하도록 설정할지 여부를 선택할 수 있습니다. 인프라 암호화가 사용하도록 설정된 새 범위를 만들려면 `--require-infrastructure-encryption` 매개 변수를 포함하고 해당 값을 `true`로 설정합니다.

자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>고객 관리형 키로 보호되는 암호화 범위 만들기

키 자격 증명 모음 또는 관리되는 HSM에 있는 고객 관리형 키로 보호되는 새 암호화 범위를 만들려면 먼저 스토리지 계정에 대한 고객 관리형 키를 구성합니다. 관리되는 ID를 스토리지 계정에 할당한 뒤 관리되는 ID를 사용하여 키 자격 증명 모음에 대한 액세스 정책을 구성함으로써 스토리지 계정이 액세스 권한을 가질 수 있도록 합니다. 자세한 내용은 [Azure Storage 암호화용 고객 관리형 키](../common/customer-managed-keys-overview.md)를 참조하세요.

암호화 범위와 함께 사용할 고객 관리형 키를 구성하려면 키 자격 증명 모음 또는 관리되는 HSM에서 제거 보호를 사용하도록 설정해야 합니다. 키 자격 증명 모음 또는 관리되는 HSM은 스토리지 계정과 같은 지역에 있어야 합니다.

예제의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity

storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group> \
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

그런 다음 `--key-uri` 매개 변수와 함께 [az storage account encryption-scope](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_create) 명령을 호출하고 키 URI를 지정합니다. 키 URI에 키 버전을 포함하는 것은 선택 사항입니다. 키 버전을 생략하면 암호화 범위는 가장 최신 키 버전을 자동으로 사용합니다. 키 버전을 포함하는 경우 다른 버전을 사용하려면 수동으로 키 버전을 업데이트해야 합니다.

스토리지 계정에 대해 인프라 암호화를 사용하도록 설정한 경우 새 암호화 범위에 대해 자동으로 사용하도록 설정됩니다. 그렇지 않으면 암호화 범위에 대해 인프라 암호화를 사용하도록 설정할지 여부를 선택할 수 있습니다. 인프라 암호화가 사용하도록 설정된 새 범위를 만들려면 `--require-infrastructure-encryption` 매개 변수를 포함하고 해당 값을 `true`로 설정합니다.

예제의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

키 자격 증명 모음 또는 관리되는 HSM에서 고객 관리형 키를 사용하여 Azure Storage 암호화를 구성하는 방법에 대해 알아보려면 다음 문서를 참조하십시오.

- [Azure Key Vault에 저장된 고객 관리형 키를 사용하여 암호화 구성](../common/customer-managed-keys-configure-key-vault.md)
- [Azure Key Vault 관리형 HSM에 저장된 고객 관리형 키를 사용하여 암호화를 구성합니다](../common/customer-managed-keys-configure-key-vault-hsm.md).

인프라 암호화에 대한 자세한 내용은 [데이터 이중 암호화를 위한 인프라 암호화 사용](../common/infrastructure-encryption-enable.md)을 참조하세요.

## <a name="list-encryption-scopes-for-storage-account"></a>스토리지 계정의 암호화 범위 나열

# <a name="portal"></a>[포털](#tab/portal)

Azure Portal에서 스토리지 계정의 암호화 범위를 보려면 해당 스토리지 계정의 **암호화 범위** 설정으로 이동합니다. 이 창에서 암호화 범위를 사용 또는 사용하지 않도록 설정하거나 암호화 범위에 대한 키를 변경할 수 있습니다.

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="Azure Portal의 암호화 범위 목록을 보여주는 스크린샷":::

키 URI 및 버전과 키 버전의 자동 업데이트 여부 등 고객 관리형 키에 대한 세부 정보를 보려면 **키** 열의 링크를 누르십시오.

:::image type="content" source="media/encryption-scope-manage/customer-managed-key-details-portal.png" alt-text="암호화 범위와 함께 사용되는 키에 대한 세부 정보를 보여주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 스토리지 계정에 사용할 수 있는 암호화 범위를 나열하려면 **Get-AzStorageEncryptionScope** 명령을 호출합니다. 예제의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

스토리지 계정별로 리소스 그룹의 모든 암호화 범위를 나열하려면 파이프라인 구문을 사용합니다.

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI를 사용하여 스토리지 계정에 사용할 수 있는 암호화 범위를 나열하려면 [az storage account encryption-scope list](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_list) 명령을 호출합니다. 예제의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="create-a-container-with-a-default-encryption-scope"></a>기본 암호화 범위를 사용하여 컨테이너 만들기

컨테이너를 만들 때 기본 암호화 범위를 지정할 수 있습니다. 해당 컨테이너의 BLOB는 기본적으로 해당 범위를 사용하게 됩니다.

컨테이너가 모든 BLOB에서 기본 범위를 사용하도록 구성된 경우를 제외하고는 개별 BLOB는 자체 암호화 범위를 사용하여 만들 수 있습니다. 자세한 내용은 [컨테이너 및 BLOB의 암호화 범위](encryption-scope-overview.md#encryption-scopes-for-containers-and-blobs)를 참조하십시오.

# <a name="portal"></a>[포털](#tab/portal)

Azure Portal에서 기본 암호화 범위를 사용하여 컨테이너를 만들려면 먼저 [암호화 범위 만들기](#create-an-encryption-scope)에 설명된 대로 암호화 범위를 만듭니다. 그런 다음, 다음 단계에 따라 컨테이너를 만듭니다.

1. 스토리지 계정의 컨테이너 목록으로 이동하고 **추가** 단추를 선택하여 새 컨테이너를 만듭니다.
1. **새 컨테이너** 창에서 **고급** 설정을 확장합니다.
1. **암호화 범위** 드롭다운에서 컨테이너의 기본 암호화 범위를 선택합니다.
1. 컨테이너의 모든 BLOB가 기본 암호화 범위를 사용하도록 요구하려면 확인란을 선택하여 **컨테이너의 모든 BLOB에 대해 이 암호화 범위를 사용** 합니다. 이 확인란을 선택하면 컨테이너의 개별 BLOB가 기본 암호화 범위를 재정의할 수 없습니다.

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="기본 암호화 범위를 사용하는 컨테이너를 보여주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 기본 암호화 범위의 컨테이너를 만들려면 [Get-AzStorageEncryptionScope](/powershell/module/az.storage/new-azstoragecontainer) 명령을 호출하여 `-DefaultEncryptionScope` 매개 변수에 대한 범위를 지정합니다. 컨테이너의 모든 BLOB가 컨테이너의 기본 범위를 사용하도록 강제하려면 `-PreventEncryptionScopeOverride` 매개 변수를 `true`로 설정합니다.

```powershell
$containerName1 = "container1"
$ctx = New-AzStorageContext -StorageAccountName $accountName -UseConnectedAccount

# Create a container with a default encryption scope that cannot be overridden.
New-AzStorageContainer -Name $containerName1 `
    -Context $ctx `
    -DefaultEncryptionScope $scopeName1 `
    -PreventEncryptionScopeOverride $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI를 사용하여 기본 암호화 범위로 컨테이너를 만들려면 [az storage container create](/cli/azure/storage/container#az_storage_container_create) 명령을 호출하고 `--default-encryption-scope` 매개변수에 대한 범위를 지정합니다. 컨테이너의 모든 BLOB가 컨테이너의 기본 범위를 사용하도록 강제하려면 `--prevent-encryption-scope-override` 매개 변수를 `true`로 설정합니다.

다음 예제에서는 Azure AD 계정을 사용하여 컨테이너를 만드는 작업에 권한을 부여합니다. 계정 액세스 키를 사용할 수도 있습니다. 자세한 내용은 [Azure CLI를 사용하여 Blob 또는 큐 데이터에 대한 액세스 권한 부여](./authorize-data-operations-cli.md)를 참조하세요.

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <container> \
    --default-encryption-scope <scope> \
    --prevent-encryption-scope-override true \
    --auth-mode login
```

---

클라이언트가 기본 암호화 범위의 컨테이너에 BLOB를 업로드할 때 범위를 지정하려고 하고 컨테이너는 BLOB가 기본 범위를 재정의하지 않도록 구성된 경우 해당 작업은 컨테이너 암호화 정책에 의해 해당 요청이 금지됨을 나타내는 메시지와 함께 실패합니다.

## <a name="upload-a-blob-with-an-encryption-scope"></a>암호화 범위를 사용하여 BLOB 업로드

BLOB를 업로드하는 경우 해당 BLOB의 암호화 범위를 지정하거나 컨테이너의 기본 암호화 범위(지정된 경우)를 사용할 수 있습니다.

# <a name="portal"></a>[포털](#tab/portal)

Azure Portal을 통해 암호화 범위를 사용하여 BLOB를 업로드하려면 먼저 [암호화 범위 만들기](#create-an-encryption-scope)에 설명된 대로 암호화 범위를 만듭니다. 그런 다음, 다음 단계에 따라 BLOB를 만듭니다.

1. BLOB를 업로드하고자 하는 컨테이너로 이동합니다.
1. **업로드** 단추를 선택하고 업로드할 BLOB를 찾습니다.
1. **BLOB 업로드** 창에서 **고급** 설정을 확장합니다.
1. **암호화 범위** 드롭다운 섹션을 찾습니다. 기본적으로 컨테이너의 기본 암호화 범위(지정된 경우)를 사용하여 BLOB가 만들어집니다. 컨테이너가 BLOB의 기본 암호화 범위 사용을 요구하는 경우 이 섹션을 사용할 수 없습니다.
1. 업로드하는 BLOB에 대해 다른 범위를 지정하려면 **기존 범위 선택** 을 선택한 다음 드롭다운에서 원하는 범위를 선택합니다.

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="암호화 범위를 사용하여 BLOB를 업로드하는 방법을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 통해 암호화 범위를 사용하여 BLOB를 업로드하려면 [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) 명령을 호출하고 BLOB의 암호화 범위를 제공합니다.

```powershell
$containerName2 = "container2"
$localSrcFile = "C:\temp\helloworld.txt"
$ctx = New-AzStorageContext -StorageAccountName $accountName -UseConnectedAccount

# Create a new container with no default scope defined.
New-AzStorageContainer -Name $containerName2 -Context $ctx

# Upload a block upload with an encryption scope specified.
Set-AzStorageBlobContent -Context $ctx `
    -Container $containerName2 `
    -File $localSrcFile `
    -Blob "helloworld.txt" `
    -BlobType Block `
    -EncryptionScope $scopeName2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI를 통해 암호화 범위를 사용하여 BLOB를 업로드하려면 [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) 명령을 호출하고 BLOB의 암호화 범위를 제공합니다.

Azure Cloud Shell를 사용하는 경우 [BLOB 업로드](storage-quickstart-blobs-cli.md#upload-a-blob)에 설명된 단계에 따라 루트 디렉터리에 파일을 만듭니다. 그런 다음, 다음 샘플을 사용하여 이 파일을 BLOB에 업로드할 수 있습니다.

```azurecli-interactive
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --file <file> \
    --name <file> \
    --encryption-scope <scope>
```

---

## <a name="change-the-encryption-key-for-a-scope"></a>범위에 따른 암호화 키 변경

암호화 범위를 보호하는 키를 Microsoft 관리형 키에서 고객 관리형 키로 변경하려면 먼저 스토리지 계정에 대하여 Azure Key Vault 또는 Key Vault HSM를 사용하는 고객 관리형 키를 사용하도록 설정했는지 확인합니다. 자세한 내용은 [Azure Key Vault에 저장된 고객 관리형 키로 암호화 구성](../common/customer-managed-keys-configure-key-vault.md) 또는 [Azure Key Vault에 저장된 고객 관리형 키로 암호화 구성](../common/customer-managed-keys-configure-key-vault.md)을 참조하십시오.

# <a name="portal"></a>[포털](#tab/portal)

Azure Portal에서 범위를 보호하는 키를 변경하는 방법은 다음과 같습니다.

1. **암호화 범위** 탭으로 이동하여 스토리지 계정에 대한 암호화 범위 목록을 봅니다.
1. 수정하려는 범위 옆에 있는 **자세히** 단추를 선택합니다.
1. **암호화 범위 편집** 창에서 암호화 형식을 Microsoft 관리형 키에서 고객 관리형 키로 변경하거나 그 반대로 변경할 수 있습니다.
1. 새 고객 관리형 키를 선택하려면 **새 키 사용** 을 선택하고 키 자격 증명 모음, 키, 키 버전을 지정합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

암호화 범위를 보호하는 키를 PowerShell을 사용하여 고객 관리형 키에서 Microsoft 관리형 키로 변경하려면 **Update-AzStorageEncryptionScope** 명령을 호출하고 `-StorageEncryption` 매개 변수를 전달합니다.

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

그런 다음 **Update-AzStorageEncryptionScope** 명령을 호출하고 `-KeyUri` 매개 변수와 `-KeyvaultEncryption` 매개 변수를 전달합니다.

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

암호화 범위를 보호하는 키를 Azure CLI를 사용하여 고객 관리형 키에서 Microsoft 관리형 키로 변경하려면 [az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_update) 명령을 호출하고 `Microsoft.Storage` 값을 사용하여 `--key-source` 매개 변수를 전달합니다.

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

다음으로 **az storage account encryption-scope update** 명령을 호출하고, `--key-uri` 매개 변수를 전달하고, 값이 `Microsoft.KeyVault`인 `--key-source` 매개 변수를 전달합니다.

```powershell
az storage account encryption-scope update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="disable-an-encryption-scope"></a>암호화 범위를 사용하지 않도록 설정

암호화 범위를 사용하지 않도록 설정하면 더 이상 요금이 청구되지 않습니다. 불필요한 요금을 방지할 수 있도록 필요하지 않은 모든 암호화 범위를 사용하지 않도록 설정합니다. 자세한 내용은 [미사용 데이터에 대한 Azure Storage 암호화](../common/storage-service-encryption.md)를 참조하세요.

# <a name="portal"></a>[포털](#tab/portal)

Azure Portal에서 암호화 범위를 사용하지 않도록 설정하려면 스토리지 계정의 **암호화 범위** 설정으로 이동하여 원하는 암호화 범위를 선택하고 **사용 안 함** 을 선택합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 암호화 범위를 사용하지 않도록 설정하려면 다음 예제와 같이 Update-AzStorageEncryptionScope 명령을 호출하고 값이 `disabled`인 `-State` 매개 변수를 포함시킵니다. 암호화 범위를 다시 사용하도록 설정하려면 `-State` 매개 변수를 `enabled`로 설정하여 동일한 명령을 호출합니다. 예제의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI를 사용하여 암호화 범위를 사용하지 않도록 설정하려면 다음 예제와 같이 [az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_update) 명령을 호출하고 값이 `Disabled`인 `--state` 매개 변수를 포함시킵니다. 암호화 범위를 다시 사용하도록 설정하려면 `--state` 매개 변수를 `Enabled`로 설정하여 동일한 명령을 호출합니다. 예제의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <scope> \
    --state Disabled
```

> [!IMPORTANT]
> 암호화 범위를 삭제할 수 없습니다. 예기치 않은 비용 발생을 방지하려면 현재 필요하지 않은 모든 암호화 범위를 사용하지 않도록 설정해야 합니다.

---

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure Storage 암호화](../common/storage-service-encryption.md)
- [BLOB 스토리지의 암호화 범위](encryption-scope-overview.md)
- [Azure Storage 암호화용 고객 관리형 키](../common/customer-managed-keys-overview.md)
- [데이터 이중 암호화를 위한 인프라 암호화 사용](../common/infrastructure-encryption-enable.md)
