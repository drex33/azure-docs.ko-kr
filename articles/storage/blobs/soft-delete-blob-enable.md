---
title: Blob에 일시 삭제를 사용하도록 설정
titleSuffix: Azure Storage
description: Blob에 대해 일시 삭제를 사용하도록 설정하여 실수로 인한 삭제 또는 덮어쓰기로부터 Blob 데이터를 보호합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 790afdb013d2721bc90238cfe0caf7aa4534c632
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289499"
---
# <a name="enable-soft-delete-for-blobs"></a>Blob에 일시 삭제를 사용하도록 설정

Blob 일시 삭제는 지정된 기간 동안 시스템에서 삭제된 데이터를 유지하여 개별 Blob 및 해당 버전, 스냅샷 및 메타데이터를 실수로 삭제하거나 덮어쓰지 않도록 보호합니다. 보존 기간 중에는 삭제 시 Blob을 해당 상태로 복원할 수 있습니다. 보존 기간이 만료되면 Blob이 영구적으로 삭제됩니다. Blob 일시 삭제에 대한 자세한 내용은 [Blob 일시 삭제](soft-delete-blob-overview.md)를 참조하세요.

Blob 일시 삭제는 Blob 데이터에 대한 포괄적인 데이터 보호 전략의 일환입니다. Microsoft의 데이터 보호 권장 사항에 대해 자세히 알아보려면 [데이터 보호 개요](data-protection-overview.md)를 참조하세요.

> [!NOTE]
> Blob 일시 삭제는 계층 구조 네임스페이스 기능을 사용하도록 설정된 계정의 Blob 및 디렉터리를 보호할 수도 있습니다. 계층 구조 네임스페이스 기능을 사용하도록 설정한 계정에 대한 Blob 일시 삭제는 현재 공개 미리 보기로 제공되며 모든 Azure 지역에서 전역적으로 사용할 수 있습니다. 

Blob 일시 삭제는 새 스토리지 계정에 대해 기본적으로 비활성화되어 있습니다. Azure Portal, PowerShell 또는 Azure CLI를 사용하여 언제든지 스토리지 계정에 대해 일시 삭제를 사용하거나 사용하지 않도록 설정할 수 있습니다.

## <a name="enable-blob-soft-delete"></a>Blob 일시 삭제 사용

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 스토리지 계정에 대해 Blob 일시 삭제를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 스토리지 계정으로 이동합니다.
1. **Blob service** 아래에서 **데이터 보호** 옵션을 찾습니다.
1. **복구** 섹션에서 **Blob 일시 삭제 켜기** 를 선택합니다.
1. 1~365일 사이의 보존 기간을 지정합니다. Microsoft는 최소 7일의 보존 기간을 권장합니다.
1. 변경 내용을 저장합니다.

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="Azure Portal에서 일시 삭제를 활성화하는 방법을 보여주는 스크린샷":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 Blob 일시 삭제를 사용하도록 설정하려면 [Enable-AzStorageBlobDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) 명령을 호출하여 보존 기간(일)을 지정합니다.

다음 예제에서는 Blob 일시 삭제를 사용하도록 설정하고 보존 기간을 7일로 설정합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

Blob 일시 삭제에 대한 현재 설정을 확인하려면 [Get-AzStorageBlobServiceProperty](/powershell/module/az.storage/get-azstorageblobserviceproperty) 명령을 호출합니다.

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-CLI)

Azure CLI를 사용하여 Blob 일시 삭제를 사용하도록 설정하려면 [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) 명령을 호출하여 보존 기간(일)을 지정합니다.

다음 예제에서는 Blob 일시 삭제를 사용하도록 설정하고 보존 기간을 7일로 설정합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

Blob 일시 삭제에 대한 현재 설정을 확인하려면 [az storage account blob-service-properties show](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_show) 명령을 호출합니다.

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="enable-blob-soft-delete-hierarchical-namespace"></a>Blob 일시 삭제 사용(계층 구조 네임스페이스)

Blob 일시 삭제는 계층 구조 네임스페이스 기능이 활성화된 계정의 Blob 및 디렉터리를 보호할 수도 있습니다. 

> [!IMPORTANT]
> 계층 구조 네임스페이스 기능을 사용하도록 설정한 계정의 일시 삭제는 현재 미리 보기로 제공되며 모든 Azure 지역에서 전역적으로 사용할 수 있습니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>
>
> 미리 보기에 등록하려면 [이 양식](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pUOVRVOUpDRUtHVUtDUUtMVTZUR0tUMjZWNy4u)을 참조하세요.

<a id="enable-blob-soft-delete-hierarchical-namespace"></a>

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 스토리지 계정에 대해 Blob 일시 삭제를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 스토리지 계정으로 이동합니다.
1. **데이터 관리** 에서 **데이터 보호** 옵션을 찾습니다.
1. **복구** 섹션에서 **Blob 일시 삭제 사용** 을 선택합니다.
1. 1~365일 사이의 보존 기간을 지정합니다. Microsoft는 최소 7일의 보존 기간을 권장합니다.
1. 변경 내용을 저장합니다.

> [!div class="mx-imgBorder"]
> ![계층 구조 네임스페이스가 있는 계정의 Azure Portal에서 일시 삭제를 사용하도록 설정하는 방법을 보여주는 스크린샷](./media/soft-delete-blob-enable/blob-soft-delete-configuration-portal-hierarchical-namespace.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 최신 **PowershellGet** 모듈을 설치합니다. 그런 다음 PowerShell 콘솔을 닫았다가 다시 엽니다.

    ```powershell
    install-Module PowerShellGet –Repository PSGallery –Force 
    ```

2.  **Az.Storage** 미리 보기 모듈을 설치합니다.

    ```powershell
    Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.7.1-preview -AllowClobber -AllowPrerelease -Force
    ```
    PowerShell 모듈 설치 방법에 대한 자세한 내용은 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

3. 스토리지 계정 키, 연결 문자열 또는 Azure AD(Azure Active Directory)를 사용하여 스토리지 계정 권한을 부여합니다. [계정에 연결](data-lake-storage-directory-file-acl-powershell.md#connect-to-the-account)을 참조하세요.

   다음 예제에서는 스토리지 계정 키를 사용하여 권한을 부여합니다.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
   ```

4. PowerShell을 사용하여 Blob 일시 삭제를 사용하도록 설정하려면 [Enable-AzStorageDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstoragedeleteretentionpolicy) 명령을 사용하여 보존 기간(일)을 지정합니다.

   다음 예제에서는 계정에 대해 일시 삭제를 사용하도록 설정하고 보존 기간을 4일로 설정합니다. 

   ```powershell
   Enable-AzStorageDeleteRetentionPolicy -RetentionDays 4  -Context $ctx
   ```
5. Blob 일시 삭제에 대한 현재 설정을 확인하려면 `Get-AzStorageServiceProperty` 명령을 사용합니다.

   ```powershell
    Get-AzStorageServiceProperty -ServiceType Blob -Context $ctx
   ```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-CLI)

1. [Azure Cloud Shell](../../cloud-shell/overview.md)을 열거나 Azure CLI를 로컬로 [설치](/cli/azure/install-azure-cli)한 경우 Windows PowerShell과 같은 명령 콘솔 애플리케이션을 엽니다.

2. `storage-preview` 확장을 설치합니다.

   ```azurecli
   az extension add -n storage-preview
   ```
3. 스토리지 계정에 연결합니다. [계정에 연결](data-lake-storage-directory-file-acl-cli.md#connect-to-the-account)을 참조하세요.

   > [!NOTE]
   > 이 문서에 제공된 예제는 Azure AD(Active Directory) 권한 부여를 보여 줍니다. 권한 부여 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Blob 또는 큐 데이터에 대한 액세스 권한 부여I](./authorize-data-operations-cli.md)를 참조하세요.
 
4. Azure CLI를 사용하여 일시 삭제를 사용하도록 설정하려면 `az storage fs service-properties update` 명령을 호출하여 보존 기간(일)을 지정합니다.

   다음 예제에서는 Blob 및 디렉터리 일시 삭제를 사용하도록 설정하고 보존 기간을 5일로 설정합니다. 

   ```azurecli
   az storage fs service-properties update --delete-retention --delete-retention-period 5 --auth-mode login
   ```

5. Blob 일시 삭제에 대한 현재 설정을 확인하려면 `az storage fs service-properties update` 명령을 호출합니다.

   ```azurecli
   az storage fs service-properties update --delete-retention false --connection-string $con
   ```

---

## <a name="next-steps"></a>다음 단계

- [Blob에 대한 일시 삭제](soft-delete-blob-overview.md)
- [일시 삭제된 Blob 관리 및 복원](soft-delete-blob-manage.md)