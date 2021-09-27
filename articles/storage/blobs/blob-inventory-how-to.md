---
title: Azure Storage Blob 인벤토리 보고서 사용
description: 스토리지 계정 내 컨테이너, Blob, 스냅샷 및 Blob 버전의 개요를 확인합니다.
services: storage
author: normesta
ms.service: storage
ms.date: 08/16/2021
ms.topic: how-to
ms.author: normesta
ms.reviewer: klaasl
ms.subservice: blobs
ms.openlocfilehash: 67bd943028ba321aa4fa3a5acca30e80cfc36a32
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615570"
---
# <a name="enable-azure-storage-blob-inventory-reports"></a>Azure Storage Blob 인벤토리 보고서 사용

Azure Storage Blob 인벤토리 기능은 스토리지 계정 내의 컨테이너, Blob, 스냅샷 및 Blob 버전의 개요를 제공합니다. 인벤토리 보고서를 사용하여 전체 데이터 크기, 사용 기간, 암호화 상태, 불변성 정책, 법적 보류 등의 다양한 Blob 및 컨테이너 특성을 이해할 수 있습니다. 이 보고서는 비즈니스 및 규정 준수 요구 사항에 대한 데이터 개요를 제공합니다.

Blob 인벤토리 보고서에 대해 자세히 알아보려면 [Azure Storage Blob 인벤토리](blob-inventory.md)를 참조하세요.

스토리지 계정에 하나 이상의 규칙이 있는 정책을 추가하여 Blob 인벤토리 보고서를 사용합니다. [Azure Portal](https://portal.azure.com/)을 사용하여 정책을 추가, 편집 또는 제거합니다.

## <a name="enable-inventory-reports"></a>인벤토리 보고서 사용

### <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)에 로그인하여 시작합니다.

2. 스토리지 계정을 찾아 계정 개요를 표시합니다.

3. **데이터 관리** 에서 **Blob 인벤토리** 를 선택합니다.

4. **첫 번째 인벤토리 규칙 추가** 를 선택합니다.

   **규칙 추가** 페이지가 나타납니다.

5. **규칙 추가** 페이지에서 새 규칙의 이름을 지정합니다.

6. 컨테이너를 선택합니다.

7. **인벤토리에 대한 개체 유형** 에서 Blob 또는 컨테이너에 대해 보고서를 만들지 여부를 선택합니다.

   **Blob** 을 선택한 경우 **Blob 하위 유형** 에서 보고서에 포함할 Blob의 유형을 선택하고, 인벤토리 보고서에 Blob 버전 및/또는 스냅샷을 포함할지 여부를 선택합니다.

   > [!NOTE]
   > 해당하는 옵션을 사용하는 새 규칙을 저장하려면 계정에서 버전 및 스냅샷을 사용하도록 설정해야 합니다.

8. 보고서에 포함할 필드 및 보고서의 형식을 선택합니다.

9. 보고서를 생성할 빈도를 선택합니다.

9. 필요에 따라 인벤토리 보고서의 필터 Blob에 접두사 일치를 추가합니다.

10. **저장** 을 선택합니다.

    :::image type="content" source="./media/blob-inventory-how-to/portal-blob-inventory.png" alt-text="Azure Portal을 사용하여 Blob 인벤토리 규칙을 추가하는 방법을 보여 주는 스크린샷":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell"></a>

Azure PowerShell 모듈을 사용하여 정적 웹 사이트 호스팅을 사용하도록 설정할 수 있습니다.

1. Windows PowerShell 명령 창을 엽니다.

2. 최신 Azure PowerShell 모듈이 있는지 확인합니다. [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요.

3. `Connect-AzAccount` 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

   ```powershell
   Connect-AzAccount
   ```

4. ID가 둘 이상의 구독과 연결된 경우 정적 웹 사이트를 호스트하는 스토리지 계정의 구독으로 활성 구독을 설정합니다.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

5. 사용하려는 스토리지 계정을 정의하는 스토리지 계정 컨텍스트를 가져옵니다.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   - `<resource-group-name>` 자리 표시자 값을 리소스 그룹의 이름으로 바꿉니다.

   - `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

6. [New-AzStorageBlobInventoryPolicyRule](/powershell/module/az.storage/new-azstorageblobinventorypolicyrule) 명령을 사용하여 인벤토리 규칙을 만듭니다. 각 규칙에는 보고서 필드가 나열됩니다. 보고서 필드의 전체 목록은 [Azure Storage Blob 인벤토리](blob-inventory.md)를 참조하세요.

   ```powershell
    $containerName = "my-container"

    $rule1 = New-AzStorageBlobInventoryPolicyRule -Name Test1 -Destination $containerName -Disabled -Format Csv -Schedule Daily -PrefixMatch con1,con2 `
                -ContainerSchemaField Name,Metadata,PublicAccess,Last-modified,LeaseStatus,LeaseState,LeaseDuration,HasImmutabilityPolicy,HasLegalHold

    $rule2 = New-AzStorageBlobInventoryPolicyRule -Name test2 -Destination $containerName -Format Parquet -Schedule Weekly  -BlobType blockBlob,appendBlob -PrefixMatch aaa,bbb `
                -BlobSchemaField name,Last-Modified,Metadata,LastAccessTime

    $rule3 = New-AzStorageBlobInventoryPolicyRule -Name Test3 -Destination $containerName -Format Parquet -Schedule Weekly -IncludeBlobVersion -IncludeSnapshot -BlobType blockBlob,appendBlob -PrefixMatch aaa,bbb `
                -BlobSchemaField name,Creation-Time,Last-Modified,Content-Length,Content-MD5,BlobType,AccessTier,AccessTierChangeTime,Expiry-Time,hdi_isfolder,Owner,Group,Permissions,Acl,Metadata,LastAccessTime

    $rule4 = New-AzStorageBlobInventoryPolicyRule -Name test4 -Destination $containerName -Format Csv -Schedule Weekly -BlobType blockBlob -BlobSchemaField Name,BlobType,Content-Length,Creation-Time

   ```

7. [Set-AzStorageBlobInventoryPolicy](/powershell/module/az.storage/set-azstorageblobinventorypolicy)를 사용하여 Blob 인벤토리 정책을 만듭니다. `-Rule` 매개 변수를 사용하여 이 명령으로 규칙을 전달합니다.

   ```powershell
   $policy = Set-AzStorageBlobInventoryPolicy -StorageAccount $storageAccount -Rule $rule1,$rule2,$rule3,$rule4  
   ```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli"></a>

CLI([Azure 명령줄 인터페이스)](/cli/azure/)를 사용하여 정적 웹 사이트 호스팅을 사용하도록 설정할 수 있습니다.

1. 먼저 [Azure Cloud Shell](../../cloud-shell/overview.md)을 열거나 Azure CLI를 로컬로 [설치](/cli/azure/install-azure-cli)한 경우 Windows PowerShell과 같은 명령 콘솔 애플리케이션을 엽니다.

2. ID가 둘 이상의 구독과 연결된 경우 정적 웹 사이트를 호스트하는 스토리지 계정의 구독으로 활성 구독을 설정합니다.

   ```azurecli
      az account set --subscription <subscription-id>
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

3. JSON 문서에서 정책의 규칙을 정의합니다. 다음은 `policy.json`이라는 예제 JSON 파일의 콘텐츠를 보여 줍니다.

    ```json
    {
    "enabled": true,
    "type": "Inventory",
    "rules": [
      {
        "enabled": true,
        "name": "inventoryPolicyRule2",
        "destination": "mycontainer",
        "definition": {
          "filters": {
            "blobTypes": [
              "blockBlob"
            ],
            "prefixMatch": [
              "inventoryprefix1",
              "inventoryprefix2"
            ],
            "includeSnapshots": true,
            "includeBlobVersions": true
          },
          "format": "Csv",
          "schedule": "Daily",
          "objectType": "Blob",
          "schemaFields": [
            "Name",
            "Creation-Time",
            "Last-Modified",
            "Content-Length",
            "Content-MD5",
            "BlobType",
            "AccessTier",
            "AccessTierChangeTime",
            "Snapshot",
            "VersionId",
            "IsCurrentVersion",
            "Metadata"
          ]
        }
      }
     ]
   }

   ```

4. [az storage account blob-inventory-policy](/cli/azure/storage/account/blob-inventory-policy#az_storage_account_blob_inventory_policy_create) 만들기 명령을 사용하여 Blob 인벤토리 정책을 만듭니다. `--policy` 매개 변수를 사용하여 JSON 문서의 이름을 제공합니다.

   ```azurecli
   az storage account blob-inventory-policy create -g myresourcegroup --account-name mystorageaccount --policy @policy.json
   ```

---

## <a name="next-steps"></a>다음 단계

- [컨테이너당 Blob의 개수 및 총 크기 계산](calculate-blob-count-size.md)
- [Azure Blob Storage 수명 주기 관리](./lifecycle-management-overview.md)
