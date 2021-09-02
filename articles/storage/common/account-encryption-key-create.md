---
title: 테이블 및 큐에 대한 고객 관리형 키를 지원하는 계정 만들기
titleSuffix: Azure Storage
description: 테이블 및 큐에 대한 고객 관리형 키 구성을 지원하는 스토리지 계정을 만드는 방법을 알아봅니다. Azure CLI 또는 Azure Resource Manager 템플릿을 사용하여 Azure Storage 암호화용 계정 암호화 키에 의존하는 스토리지 계정을 만듭니다. 그런 다음 계정용 고객 관리형 키를 구성할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/09/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 9ab311c8e348c6e0cbb703a73562770a2f4f963f
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251753"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>테이블 및 큐에 대한 고객 관리형 키를 지원하는 계정 만들기

Azure Storage는 미사용 스토리지 계정의 모든 데이터를 암호화합니다. 기본값으로 큐 스토리지 및 테이블 스토리지는 서비스로 범위가 지정되고 Microsoft에서 관리하는 키를 사용합니다. 또한 고객 관리형 키를 사용하여 큐 또는 테이블 데이터를 암호화할 수도 있습니다. 큐 및 테이블에서 고객 관리형 키를 사용하려면 먼저 서비스 대신 계정으로 범위가 지정된 암호화 키를 사용하는 스토리지 계정을 만들어야 합니다. 큐 및 테이블 데이터에 계정 암호화 키를 사용하는 계정을 만든 후에는 해당 스토리지 계정에 대한 고객 관리형 키를 구성할 수 있습니다.

이 문서에서는 계정으로 범위가 지정된 키에 의존하는 스토리지 계정을 만드는 방법을 설명합니다. Microsoft는 계정을 처음 만들 때 계정 키를 사용하여 계정의 데이터를 암호화하고 Microsoft가 해당 키를 직접 관리합니다. 그런 다음 계정에 대한 고객 관리형 키를 구성하여 사용자 고유의 키를 제공하고, 키 버전을 업데이트하며, 키를 회전하고, 액세스 제어를 철회하는 기능을 포함한 여러 혜택을 활용할 수 있습니다.

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>계정 암호화 키를 사용하는 계정 만들기

스토리지 계정을 만들 때는 큐 및 테이블에 계정 암호화 키를 사용할 수 있도록 새 스토리지 계정을 구성해야 합니다. 계정을 만든 후에는 암호화 키의 범위를 변경할 수 없습니다.

스토리지 계정은 GPv2(범용 v2) 형식이어야 합니다. Azure portal, PowerShell, Azure CLI 또는 Azure Resource Manager 템플릿을 사용하여 스토리지 계정을 만들고 해당 계정이 계정 암호화 키를 사용하도록 구성할 수 있습니다.

스토리지 계정을 만드는 방법에 대해 자세히 알아보려면 [스토리지 계정 만들기](storage-account-create.md)를 참조하세요.

> [!NOTE]
> 스토리지 계정을 만들 때는 큐 및 테이블 스토리지만 계정 암호화 키를 사용하여 데이터를 암호화하도록 선택적으로 구성할 수 있습니다. Blob Storage 및 Azure Files는 항상 계정 암호화 키를 사용하여 데이터를 암호화합니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal로 계정 암호화 키에 의존하는 스토리지 계정을 만들려면 다음 단계를 수행합니다.

1. 왼쪽 포털 메뉴에서 **스토리지 계정** 을 선택하여 스토리지 계정 목록을 표시합니다.
1. **스토리지 계정** 페이지에서 **새로 만들기** 를 선택합니다.
1. **기본 사항** 탭의 필드를 채웁니다.
1. 고급 탭에서 **테이블 및 큐** 섹션을 찾고 **고객 관리 키 지원 사용** 을 선택합니다.

    :::image type="content" source="media/account-encryption-key-create/enable-cmk-tables-queues.png" alt-text="새 계정을 만들 때 큐 및 테이블에 대해 고객 관리형 키를 사용하도록 설정하는 방법을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 계정 암호화 키에 의존하는 스토리지 계정을 만들려면 Azure PowerShell 모듈 버전 3.4.0 이상을 설치했는지 확인해야 합니다. 자세한 내용은 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

다음으로, 다음과 같이 적절한 매개 변수로 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 명령을 호출하여 범용 v2 스토리지 계정을 만듭니다.

- `-EncryptionKeyTypeForQueue` 옵션을 포함하고 해당 값을 `Account`로 설정하여 계정 암호화 키로 큐 스토리지의 데이터를 암호화합니다.
- `-EncryptionKeyTypeForTable` 옵션을 포함하고 해당 값을 `Account`로 설정하여 계정 암호화 키로 테이블 스토리지의 데이터를 암호화합니다.

다음 예제에서는 RA-GRS(읽기 액세스 지역 중복 스토리지)를 위해 구성되며, 계정 암호화 키를 사용하여 큐 및 테이블 스토리지의 데이터를 암호화하는 범용 v2 스토리지 계정을 만드는 방법을 보여 줍니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 계정 암호화 키에 의존하는 스토리지 계정을 만들려면 Azure CLI 모듈 버전 2.0.80 이상을 설치했는지 확인해야 합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

다음으로, 다음과 같이 적절한 매개 변수로 [az 스토리지 계정 만들기](/cli/azure/storage/account#az_storage_account_create) 명령을 호출하여 범용 v2 스토리지 계정을 만듭니다.

- `--encryption-key-type-for-queue` 옵션을 포함하고 해당 값을 `Account`로 설정하여 계정 암호화 키로 큐 스토리지의 데이터를 암호화합니다.
- `--encryption-key-type-for-table` 옵션을 포함하고 해당 값을 `Account`로 설정하여 계정 암호화 키로 테이블 스토리지의 데이터를 암호화합니다.

다음 예제에서는 RA-GRS(읽기 액세스 지역 중복 스토리지)를 위해 구성되며, 계정 암호화 키를 사용하여 큐 및 테이블 스토리지의 데이터를 암호화하는 범용 v2 스토리지 계정을 만드는 방법을 보여 줍니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[템플릿](#tab/template)

다음 JSON 예제에서는 RA-GRS(읽기 액세스 지역 중복 스토리지)를 위해 구성되며, 계정 암호화 키를 사용하여 큐 및 테이블 스토리지의 데이터를 암호화하는 범용 v2 스토리지 계정을 만듭니다. 꺾쇠괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

계정 암호화 키를 사용하는 계정을 만든 후 Azure Key Vault 또는 Key Vault 관리형 HSM(하드웨어 보안 모듈)에 저장된 고객 관리형 키를 구성할 수 있습니다. 키 자격 증명 모음의 고객 관리형 키를 저장하는 방법은 [Azure Key Vault에 저장된 고객 관리형 키로 암호화 구성](customer-managed-keys-configure-key-vault.md)을 참조하세요. 관리되는 HSM의 고객 관리형 키를 저장하는 방법은 [Azure Key Vault 관리되는 HSM에 저장된 고객 관리형 키로 암호화 구성](customer-managed-keys-configure-key-vault-hsm.md)을 참조하세요.

## <a name="verify-the-account-encryption-key"></a>계정 암호화 키 확인

계정을 만든 후 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 스토리지 계정이 계정으로 범위가 지정된 암호화 키를 사용하고 있는지 확인할 수 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

스토리지 계정의 서비스에서 Azure Portal 계정으로 범위가 지정된 암호화 키를 사용하고 있는지 확인하려면 다음 단계를 수행합니다.

1. Azure Portal의 새 스토리지 계정으로 이동합니다.
1. **보안 + 네트워킹** 섹션에서 **암호화** 를 선택합니다.
1. 계정 암호화 키를 사용하여 스토리지 계정을 만든 경우 **암호화** 탭에서 네 가지 Azure Storage 서비스(Blob, 파일, 테이블 및 큐)에 대해 고객 관리형 키를 사용하도록 설정할 수 있다는 것을 알 수 있습니다.

    :::image type="content" source="media/account-encryption-key-create/verify-cmk-tables-queues.png" alt-text="스토리지 계정이 계정 암호화 키에 의존하는지 확인하는 방법을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 스토리지 계정의 서비스가 계정 암호화 키를 사용하고 있는지 확인하려면 [AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) 명령을 호출합니다. 이 명령은 스토리지 계정 속성 및 해당 값의 집합을 반환합니다. `Encryption` 속성 내에서 각 서비스에 대한 `KeyType` 필드를 찾아 `Account`으로 설정되어 있는지 확인합니다.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 스토리지 계정의 서비스가 계정 암호화 키를 사용하고 있는지 확인하려면 [az storage account show](/cli/azure/storage/account#az_storage_account_show) 명령을 호출합니다. 이 명령은 스토리지 계정 속성 및 해당 값의 집합을 반환합니다. 암호화 속성 내에서 각 서비스에 대한 `keyType` 필드를 찾아 `Account`로 설정되어 있는지 확인합니다.

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource-group>
```

# <a name="template"></a>[템플릿](#tab/template)

해당 없음

---

스토리지 계정이 계정으로 범위가 지정된 암호화 키를 사용하고 있는지 확인한 후에는 해당 계정에 대해 고객 관리형 키를 사용하도록 설정할 수 있습니다. 그러면 네 가지 Azure Storage 서비스 &mdash;Blob, 파일, 테이블 및 큐&mdash;가 암호화를 위해 고객 관리형 키를 사용합니다.

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

계정으로 범위가 지정된 암호화 키를 사용하도록 만들어진 스토리지 계정은 테이블 스토리지 용량 및 트랜잭션 관련 비용이 기본 서비스 범위 키를 사용하는 계정과 다른 비율로 청구됩니다. 자세한 내용은 [Azure Table Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/tables/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure Storage 암호화](storage-service-encryption.md)
- [Azure Storage 암호화용 고객 관리형 키](customer-managed-keys-overview.md)
- [Azure Key Vault에 저장된 고객 관리형 키를 사용하여 암호화 구성](customer-managed-keys-configure-key-vault.md)
- [Azure Key Vault 관리형 HSM에 저장된 고객 관리형 키를 사용하여 암호화 구성](customer-managed-keys-configure-key-vault-hsm.md).