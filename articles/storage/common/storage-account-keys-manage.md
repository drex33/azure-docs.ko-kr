---
title: 계정 선택키 관리
titleSuffix: Azure Storage
description: 스토리지 계정 액세스 키를 보고, 관리하고, 회전하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2021
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 600c651601e4281b717c1c8fa7808f3663be4af6
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113093946"
---
# <a name="manage-storage-account-access-keys"></a>스토리지 계정 액세스 키 관리

스토리지 계정을 만들 때 Azure는 두 개의 512비트 스토리지 계정 액세스 키를 생성합니다. 이러한 키를 사용하면 공유 키 권한 부여를 통해 스토리지 계정의 데이터에 액세스 권한을 부여할 수 있습니다.

Microsoft는 Azure Key Vault를 사용하여 액세스 키를 관리하고, 키를 정기적으로 회전하고 다시 생성할 것을 권장합니다. Azure Key Vault를 사용하면 애플리케이션을 중단하지 않고 쉽게 키를 회전할 수 있습니다. 키는 수동 회전도 가능합니다.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>계정 액세스 키 보기

Azure Portal, PowerShell 또는 Azure CLI를 사용하여 계정 액세스 키를 보고 복사할 수 있습니다. 또한 Azure Portal은 복사할 수 있는 스토리지 계정에 연결 문자열을 제공합니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 스토리지 계정 액세스 키 또는 연결 문자열을 보고 복사하는 방법은 다음과 같습니다:

1. [Azure Portal](https://portal.azure.com)의 스토리지 계정으로 이동합니다.

2. **설정** 에서 **액세스 키** 를 선택합니다. 계정 액세스 키는 물론 각 키의 전체 연결 문자열이 나타납니다.

3. **key1** 아래에서 **키** 값을 찾고, **복사** 단추를 클릭하여 계정 키를 복사합니다.

4. 또는 전체 연결 문자열을 복사할 수 있습니다. **key1** 아래에서 **연결 문자열** 값을 찾고, **복사** 단추를 클릭하여 연결 문자열을 복사합니다.

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="Azure Portal에서 액세스 키를 확인하는 방법을 보여 주는 스크린 샷":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 계정 액세스 키를 검색하려면 [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey) 명령을 호출합니다.

다음 예에서는 첫 번째 키를 검색합니다. 두 번째 키를 검색하려면 `Value[0]` 대신 `Value[1]`을 사용합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 계정 액세스 키를 나열하려면 다음 예제와 같이 [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) 명령을 호출합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다. 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

두 키 중 하나를 사용하여 Azure Storage에 액세스할 수 있지만 일반적으로 첫 번째 키를 사용하고 키를 회전할 때를 대비해 두 번째 키의 사용을 예약하는 것이 좋습니다.

계정의 액세스 키를 보거나 읽으려면 사용자가 서비스 관리자이거나 **Microsoft. Storage/storageAccounts/listkeys/action** 을 포함하는 Azure 역할을 할당받아야 합니다. 이 작업을 포함하는 일부 Azure 기본 제공 역할은 **소유자**, **Contributor** 및 **스토리지 계정 키 운영자 서비스 역할** 입니다. 서비스 관리자 역할에 대한 자세한 내용은 [클래식 구독 관리자 역할, Azure 역할 및 Azure AD 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md)을 참조하세요. Azure Storage의 기본 제공 역할에 대한 자세한 내용은 [Azure RBAC에 대한 Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md#storage)의 **스토리지** 섹션을 참조하세요.

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Azure Key Vault를 사용하여 액세스 키 관리

Microsoft는 Azure Key Vault를 사용하여 액세스 키를 관리하고 회전할 것을 권장합니다. 사용자의 애플리케이션이 Key Vault의 키에 안전하게 액세스할 수 있으므로 애플리케이션 코드를 사용하여 키를 저장하지 않아도 됩니다. 키 관리를 위한 Key Vault 사용 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Key Vault 및 PowerShell을 사용하여 스토리지 계정 키 관리](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Azure Key Vault 및 Azure CLI를 사용하여 스토리지 계정 키 관리](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>액세스 키의 수동 회전

Microsoft는 스토리지 계정을 안전하게 유지하기 위해 액세스 키를 정기적으로 회전할 것을 권장합니다. 가급적 Azure Key Vault를 사용하여 액세스 키를 관리하십시오. Key Vault를 사용하지 않는 경우 수동으로 키를 회전해야 합니다.

키를 회전할 수 있도록 두 개의 액세스 키가 할당됩니다. 두 개의 키가 있으면 애플리케이션이 프로세스 전체에서 Azure Storage에 대한 액세스 권한을 유지할 수 있습니다.

> [!WARNING]
> 액세스 키를 다시 생성하면 스토리지 계정 키에 종속된 모든 애플리케이션과 Azure 서비스에 영향을 미칠 수 있습니다. 계정 키를 사용하여 스토리지 계정에 액세스하는 모든 클라이언트는 미디어 서비스, 클라우드, 데스크톱 및 모바일 애플리케이션, Azure Storage용 그래픽 사용자 인터페이스 애플리케이션(예: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/))을 포함하여 새로운 키를 사용하도록 업데이트되어야 합니다.

액세스 키를 수동으로 순환하려는 경우, 키 만료 정책을 설정한 다음, Azure Monitor 쿼리를 사용하여 액세스 키를 순환할 시기를 결정하는 것이 좋습니다.

### <a name="create-a-key-expiration-policy"></a>키 만료 정책 생성

#### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 키 만료 정책을 설정하는 기능은 아직 사용할 수 없습니다. PowerShell 또는 Azure CLI 중 하나를 사용할 수 있습니다.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

키 만료 정책을 생성하려면 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 명령을 사용하고 `-KeyExpirationPeriodInDay` 매개 변수를 순환하기 전에 액세스 키를 활성화할 수 있는 일 수로 설정합니다. 

```powershell
$account = Set-AzStorageAccount -ResourceGroupName <resource-group> -Name `
    <storage-account-name>  -KeyExpirationPeriodInDay <period-in-days> 
```

> [!TIP]
> [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 명령의 `-KeyExpirationPeriodInDay` 매개 변수를 설정하여 스토리지 계정을 만들 때 키 만료 정책을 설정할 수도 있습니다.

정책이 적용되었는지 확인하려면, 이전 명령에서 `$account` 변수로 반환된 [PSStorageAccount](/dotnet/api/microsoft.azure.commands.management.storage.models.psstorageaccount)의 `KeyPolicy` 속성을 사용합니다. 
  
```powershell
$account.KeyPolicy
``` 

키 만료 기간이 콘솔 출력에 나타납니다.

> [!div class="mx-imgBorder"]
> ![액세스 키 만료 기간](./media/storage-account-keys-manage/key-policy-powershell.png)

만료 기간보다 오랫동안 활성 상태인 경우, 기존 키를 순환할 수 있습니다. 키를 만든 시기를 확인하려면, `KeyCreationTime` 속성을 사용합니다. 
  
```powershell
$account.KeyCreationTime
``` 

두 액세스 키에 대한 액세스 키 생성 시간이 콘솔 출력에 표시됩니다.

> [!div class="mx-imgBorder"]
> ![액세스 키 생성 시간](./media/storage-account-keys-manage/key-creation-time-powershell.png)


#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

기존 스토리지 계정에서 키 만료 정책을 생성하려면, [az storage account update](/cli/azure/storage/account#az_storage_account_update) 명령을 사용하고 `--key-exp-days` 매개 변수를 순환하기 전에 액세스 키를 활성화할 수 있는 일 수로 설정합니다. 

```azurecli-interactive
az storage account update \
  -n <storage-account-name> \
  -g <resource-group> --key-exp-days <period-in-days>
```

> [!TIP]
> [az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령의 `-KeyExpirationPeriodInDay` 매개 변수를 설정하여 스토리지 계정을 만들 때 키 만료 정책을 설정할 수도 있습니다.

정책이 적용되었는지 확인하려면, [az storage account show](/cli/azure/storage/account#az_storage_account_show) 명령을 호출하고 `-query` 매개 변수에 `{KeyPolicy:keyPolicy}` 문자열을 사용합니다.
  
```azurecli-interactive
az storage account show \
  -n <storage-account-name> \
  -g <resource-group-name> \
  --query "{KeyPolicy:keyPolicy}"
```

키 만료 기간이 콘솔 출력에 나타납니다.

```json
{
  "KeyPolicy": {
    "keyExpirationPeriodInDays": 5
  }
}
```


만료 기간보다 오랫동안 활성 상태인 경우, 기존 키를 순환할 수 있습니다. 키가 만들어진 시기를 확인하려면, [az storage account show](/cli/azure/storage/account#az_storage_account_show) 명령을 사용한 다음 query 매개 변수에 `keyCreationTime` 문자열을 사용합니다.
  
```azurecli-interactive
az storage account show \
  -n <storage-account-name> \
  -g <resource-group-name> \
  --query "keyCreationTime"
```

---

### <a name="query-for-policy-violations"></a>정책 위반에 대한 쿼리

[Azure Log Analytics 작업 영역](../blobs/monitor-blob-storage.md#send-logs-to-azure-log-analytics)으로 로그를 보내는 진단 설정을 생성하는 경우, Azure Monitor 로그 쿼리를 사용하여 키가 만료되었는지 여부를 확인할 수 있습니다. 

키가 만료되었는지 확인하려면 **로그 검색** 표시줄에 다음 쿼리를 입력합니다.

```Kusto
StorageBlobLogs | where KeyExpiryStatus startsWith "Policy Violated". 
```

쿼리 만료가 가까운지 확인하는 데 도움이 되는 쿼리를 생성할 수도 있습니다. 다음 쿼리는 이 정보를 제공합니다.

```Kusto
resources  
| where type =~ 'microsoft.storage/storageAccounts' 
| extend days = datetime_diff('day', now(), todatetime(parse_json(properties).keyCreationTime)) 
| extend KeyExpiryStatus = iff(days > 180, "Policy Violated", "") 
| project name, days, KeyExpiryStatus  
```

### <a name="rotate-access-keys"></a>액세스 키를 순환

#### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 스토리지 계정 액세스 키를 회전하는 방법은 다음과 같습니다.

1. 스토리지 계정의 보조 액세스 키를 참조하도록 애플리케이션 코드의 연결 문자열을 업데이트합니다.

2. [Azure Portal](https://portal.azure.com)의 스토리지 계정으로 이동합니다.

3. **설정** 에서 **액세스 키** 를 선택합니다.

4. 스토리지 계정에 대한 기본 키를 다시 생성하려면 기본 액세스 키 옆에 있는 **다시 생성** 단추를 선택합니다.

5. 새 기본 액세스 키를 참조하도록 코드의 연결 문자열을 업데이트합니다.

6. 같은 방식으로 보조 액세스 키를 다시 생성합니다.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 스토리지 계정 액세스 키를 회전하는 방법은 다음과 같습니다.

1. 스토리지 계정의 보조 액세스 키를 참조하도록 애플리케이션 코드의 연결 문자열을 업데이트합니다.

2. 다음 예제와 같이 [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) 명령을 호출하여 기본 액세스 키를 다시 생성합니다.

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

3. 새 기본 액세스 키를 참조하도록 코드의 연결 문자열을 업데이트합니다.

4. 같은 방식으로 보조 액세스 키를 다시 생성합니다. 보조 키를 다시 생성하려면 `key1` 대신 `key2`를 키 이름으로 사용합니다.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 스토리지 계정 액세스 키를 회전하는 방법은 다음과 같습니다.

1. 스토리지 계정의 보조 액세스 키를 참조하도록 애플리케이션 코드의 연결 문자열을 업데이트합니다.

2. 다음 예제와 같이 [az storage account keys renew](/cli/azure/storage/account/keys#az_storage_account_keys_renew) 명령을 호출하여 기본 액세스 키를 다시 생성합니다.

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary
    ```

1. 새 기본 액세스 키를 참조하도록 코드의 연결 문자열을 업데이트합니다.

2. 같은 방식으로 보조 액세스 키를 다시 생성합니다. 보조 키를 다시 생성하려면 `key1` 대신 `key2`를 키 이름으로 사용합니다.

---

> [!NOTE]
> 모든 애플리케이션에서 키 중 하나만 동시에 사용하는 것이 좋습니다. 어떤 경우에는 키 1을 사용하고, 다른 경우에는 키 2를 사용하면 어떤 애플리케이션이 액세스 권한을 상실해야만 키를 순환할 수 있게 됩니다.

계정의 액세스 키를 회전하려면 사용자가 서비스 관리자이거나 **Microsoft.Storage/storageAccounts/regeneratekey/action** 을 포함하는 Azure 역할을 할당받아야 합니다. 이 작업을 포함하는 일부 Azure 기본 제공 역할은 **소유자**, **Contributor** 및 **스토리지 계정 키 운영자 서비스 역할** 입니다. 서비스 관리자 역할에 대한 자세한 내용은 [클래식 구독 관리자 역할, Azure 역할 및 Azure AD 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md)을 참조하세요. Azure Storage의 Azure 기본 제공 역할에 대한 자세한 내용은 [Azure RBAC에 대한 Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md#storage)의 **스토리지** 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Storage 계정 개요](storage-account-overview.md)
- [스토리지 계정을 만드는](storage-account-create.md)
