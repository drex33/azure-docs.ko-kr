---
title: 데이터의 이중 암호화에 인프라 암호화 사용
titleSuffix: Azure Storage
description: 데이터가 안전하다는 높은 수준의 보증을 요구하는 고객은 Azure Storage 인프라 수준에서 256비트 AES 암호화를 사용하도록 설정할 수도 있습니다. 인프라 암호화를 사용하도록 설정하면 스토리지 계정 또는 암호화 범위의 데이터는 두 개의 서로 다른 암호화 알고리즘과 두 개의 서로 다른 키를 사용하여 두 번 암호화됩니다.
services: storage
author: tamram
ms.service: storage
ms.date: 06/01/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c4dca05b855666a04d1def09228c596eb931bbf5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128607010"
---
# <a name="enable-infrastructure-encryption-for-double-encryption-of-data"></a>데이터의 이중 암호화에 인프라 암호화 사용

Azure Storage는 256비트 AES 암호화를 사용하여 서비스 수준에서 스토리지 계정의 모든 데이터를 자동으로 암호화하는, 사용 가능한 가장 강력한 블록 암호화 중 하나이며, FIPS 140-2를 준수합니다. 데이터 안전에 대한 높은 수준의 보증을 요구하는 고객은 이중 암호화를 위해 Azure Storage 인프라 수준에서 256비트 AES 암호화를 사용하도록 설정할 수도 있습니다. Azure Storage 데이터의 이중 암호화는 암호화 알고리즘 또는 키 중 하나가 손상될 수 있는 시나리오로부터 보호합니다. 이 시나리오에서 추가 암호화 계층은 계속해서 데이터를 보호합니다.

전체 스토리지 계정 또는 계정 내의 암호화 범위에 대해 인프라 암호화를 사용하도록 설정할 수 있습니다. 스토리지 계정 또는 암호화 범위에 대해 인프라 암호화를 사용하도록 설정하면, 서로 다른 암호화 알고리즘 2개와 서로 다른 키 2개를 사용하여 데이터가 두 번(서비스 수준에서 한 번, 인프라 수준에서 한 번) 암호화됩니다.

서비스 수준 암호화는 Azure Key Vault 또는 Key Vault 관리형 HSM(하드웨어 보안 모델)(미리 보기)을 사용하여 Microsoft에서 관리형 키 또는 고객 관리형 키를 사용할 수 있도록 지원합니다. 인프라 수준 암호화는 Microsoft 관리형 키를 기반으로 하며 항상 별도의 키를 사용합니다. Azure Storage 암호화를 사용한 키 관리에 대한 자세한 내용은 [암호화 키 관리 정보](storage-service-encryption.md#about-encryption-key-management)를 참조하세요.

데이터를 이중으로 암호화하려면 먼저 인프라 암호화를 위해 구성된 스토리지 계정 또는 암호화 범위를 만들어야 합니다. 이 문서에서는 인프라 암호화를 사용하도록 설정하는 방법을 설명합니다.

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>인프라 암호화를 사용하도록 설정하여 계정 만들기

스토리지 계정에 인프라 암호화를 사용하도록 설정하려면 계정을 만들 때 인프라 암호화를 사용하도록 스토리지 계정을 구성해야 합니다. 계정을 만든 후에는 인프라 암호화를 사용하거나 사용하지 않도록 설정할 수 없습니다. 스토리지 계정은 GPv2(범용 v2) 형식이어야 합니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

PowerShell을 사용하여 인프라 암호화가 사용하도록 설정된 스토리지 계정을 만들려면 다음 단계를 수행합니다.

1. Azure Portal에서 **스토리지 계정** 페이지로 이동합니다.
1. **추가** 단추를 선택하여 GPv2(범용 v2) 스토리지 계정을 새로 추가합니다.
1. **고급** 탭에서 **인프라** 암호화를 찾은 다음 **사용** 을 선택합니다.
1. **검토 + 만들기** 를 선택하여 스토리지 계정 만들기를 완료합니다.

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="계정을 만들 때 인프라 암호화를 사용하도록 설정하는 방법을 보여 주는 스크린샷":::

Azure Portal을 사용하여 스토리지 계정에 인프라 암호화가 사용하도록 설정되어 있는지 확인하려면 다음 단계를 수행합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **설정** 아래에서 **암호화** 를 선택합니다.

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="계정에 대해 인프라 암호화가 사용하도록 설정되었는지 확인하는 방법을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 인프라 암호화가 사용하도록 설정된 스토리지 계정을 만들려면 [Az.Storage PowerShell 모듈](https://www.powershellgallery.com/packages/Az.Storage) 버전 2.2.0 이상을 설치했는지 확인합니다. 자세한 내용은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요.

다음으로, [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 명령을 호출하여 GPv2(범용 v2) 스토리지 계정을 만듭니다. 인프라 암호화를 사용하도록 설정하는 `-RequireInfrastructureEncryption` 옵션을 포함합니다.

다음 예제에서는 RA-GRS(읽기 액세스 지역 중복 스토리지)에 대해 구성된 GPv2(범용 v2) 스토리지 계정을 만들고 데이터의 이중 암호화에 대해 인프라 암호화를 사용하도록 설정하는 방법을 보여 줍니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

스토리지 계정에 대해 인프라 암호화를 사용하도록 설정했는지 확인하려면 [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) 명령을 호출합니다. 이 명령은 스토리지 계정 속성 및 해당 값의 집합을 반환합니다. `Encryption` 속성 내에서 `RequireInfrastructureEncryption` 필드를 검색하고 이 필드가 `True`로 설정되어 있는지 확인합니다.

다음 예제에서는 `RequireInfrastructureEncryption` 속성의 값을 검색합니다. 꺾쇠괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 인프라 암호화가 사용하도록 설정된 스토리지 계정을 만들려면 Azure CLI 버전 2.8.0 이상을 설치했는지 확인해야 합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

다음으로 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령을 호출하여 GPv2(범용 v2) 스토리지 계정을 만들고 인프라 암호화를 사용하도록 설정하는 `--require-infrastructure-encryption option`을 포함합니다.

다음 예제에서는 RA-GRS(읽기 액세스 지역 중복 스토리지)에 대해 구성된 GPv2(범용 v2) 스토리지 계정을 만들고 데이터의 이중 암호화에 대해 인프라 암호화를 사용하도록 설정하는 방법을 보여 줍니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

스토리지 계정에 대해 인프라 암호화를 사용하도록 설정했는지 확인하려면 [az storage account show](/cli/azure/storage/account#az-storage-account-show) 명령을 호출합니다. 이 명령은 스토리지 계정 속성 및 해당 값의 집합을 반환합니다. `encryption` 속성 내에서 `requireInfrastructureEncryption` 필드를 찾고 이 필드가 `true`로 설정되어 있는지 확인합니다.

다음 예제에서는 `requireInfrastructureEncryption` 속성의 값을 검색합니다. 꺾쇠괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[템플릿](#tab/template)

다음 JSON 예제에서는 RA-GRS(읽기 액세스 지역 중복 스토리지)에 대해 구성된 GPv2(범용 v2) 스토리지 계정을 만들고 데이터의 이중 암호화에 대해 인프라 암호화를 사용하도록 설정합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

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
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

Azure Policy는 스토리지 계정에 대해 인프라 암호화를 사용하도록 요구하는 기본 제공 정책을 제공합니다. 자세한 내용은 [Azure Policy 기본 제공 정책 정의](../../governance/policy/samples/built-in-policies.md#storage)의 **스토리지** 섹션을 참조하세요.

## <a name="create-an-encryption-scope-with-infrastructure-encryption-enabled"></a>인프라 암호화가 사용될 암호화 범위 만들기

계정에 대해 인프라 암호화를 사용하도록 설정한 경우, 해당 계정에서 만든 암호화 범위가 인프라 암호화를 자동으로 사용합니다. 계정 수준에서 인프라 암호화를 사용하도록 설정하지 않은 경우, 범위를 만들 때 암호화 범위에 대해 사용하도록 설정할 수 있는 옵션이 있습니다. 범위를 만든 후에는 암호화 범위에 대한 인프라 암호화 설정을 변경할 수 없습니다. 자세한 내용은 [암호화 범위 만들기](../blobs/encryption-scope-manage.md#create-an-encryption-scope)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure Storage 암호화](storage-service-encryption.md)
- [Azure Storage 암호화용 고객 관리형 키](customer-managed-keys-overview.md)
- [BLOB 스토리지의 암호화 범위](../blobs/encryption-scope-overview.md)
