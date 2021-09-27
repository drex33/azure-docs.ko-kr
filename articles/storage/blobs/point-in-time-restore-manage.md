---
title: 블록 BLOB 데이터에 지정 시간 복원 수행
titleSuffix: Azure Storage
description: 지정 시간 복원을 사용하여 지정된 시점에 블록 Blob 세트를 이전 상태로 복원하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/29/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c4dc8b3e079f224dbefde3bc12b5d79a4db32faa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589609"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>블록 BLOB 데이터에 지정 시간 복원 수행

지정 시간 복원을 사용하여 하나 이상의 블록 Blob 세트를 이전 상태로 복원할 수 있습니다. 이 문서에서는 스토리지 계정에 지정 시간 복원을 사용하도록 설정하는 방법과 복원 작업을 수행하는 방법을 설명합니다.

지정 시간 복원에 대한 자세한 내용은 [블록 Blob에 대한 지정 시간 복원](point-in-time-restore-overview.md)을 참조하세요.

> [!CAUTION]
> 지정 시간 복원은 블록 Blob에 대한 작업만 복원하도록 지원합니다. 컨테이너에 대한 작업은 복원할 수 없습니다. [컨테이너 삭제](/rest/api/storageservices/delete-container) 작업을 호출하여 스토리지 계정에서 컨테이너를 삭제하면 해당 컨테이너는 복원 작업을 통해 복원할 수 없습니다. 전체 컨테이너를 삭제하는 대신 개별 Blob을 삭제해야 나중에 복원할 수 있습니다. 또한 실수로 삭제되지 않도록 보호하기 위해 컨테이너 및 Blob에 대해 일시 삭제를 사용하도록 설정하는 것이 좋습니다. 자세한 내용은 [컨테이너의 일시 삭제](soft-delete-container-overview.md) 및 [Blob의 일시 삭제](soft-delete-blob-overview.md)를 참조하세요.

## <a name="enable-and-configure-point-in-time-restore"></a>지정 시간 복원 활성화 및 구성

지정 시간 복원을 사용하도록 설정하고 구성하기 전에 스토리지 계정에 대한 필수 구성 요소(일시 삭제, 변경 피드 및 Blob 버전 관리)를 사용하도록 설정합니다. 이러한 각 기능을 사용하도록 설정하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Blob에 일시 삭제를 사용하도록 설정](./soft-delete-blob-enable.md)
- [변경 피드를 사용하거나 사용하지 않도록 설정](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Blob 버전 관리 설정 및 관리](versioning-enable.md)

> [!IMPORTANT]
> 일시 삭제, 변경 피드 및 Blob 버전 관리를 사용하도록 설정하면 추가 요금이 발생할 수 있습니다. 자세한 내용은 [Blob의 일시 삭제](soft-delete-blob-overview.md), [Azure Blob Storage의 변경 피드 지원](storage-blob-change-feed.md) 및 [Blob 버전 관리](versioning-overview.md)를 참조하세요.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal을 사용하여 지정 시간 복원을 구성하려면 다음 단계를 수행합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **설정** 아래에서 **데이터 보호** 를 선택합니다.
1. **지정 시간 복원 켜기** 를 선택합니다. 이 옵션을 선택하면 Blob에 대한 일시 삭제, 버전 관리 및 변경 피드도 사용하도록 설정됩니다.
1. 지정 시간 복원에 대한 최대 복원 지점(일)을 설정합니다. 이 숫자는 Blob 일시 삭제에 대해 지정된 보존 기간보다 적어도 1일 더 작아야 합니다.
1. 변경 내용을 저장합니다.

다음 이미지에서는 7일 전 복원 지점과 Blob 일시 삭제에 대한 14일의 보존 기간을 사용하는 지정 시간 복원이 구성된 스토리지 계정을 보여 줍니다.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="Azure Portal에서 지정 시간 복원을 구성하는 방법을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 지정 시간 복원을 구성하려면 먼저 [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) 모듈 버전 2.6.0 이상을 설치합니다. 그런 다음, [Enable-AzStorageBlobRestorePolicy](/powershell/module/az.storage/enable-azstorageblobrestorepolicy) 명령을 호출하여 스토리지 계정에 대한 지정 시간 복원을 사용하도록 설정합니다.

다음 예제는 일시 삭제를 사용하도록 설정하고, 일시 삭제 보존 기간을 설정하고, 변경 피드를 사용하도록 설정한 후 지정 시간 복원을 사용하도록 설정합니다. 예제를 실행하는 경우 꺾쇠 괄호의 값을 고유한 값으로 바꿔야 합니다.

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable blob soft delete with a retention of 14 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 14

# Enable change feed and versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable point-in-time restore with a retention period of 7 days.
# The retention period for point-in-time restore must be at least
# one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 7

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 지정 시간 복원을 구성하려면 먼저 Azure CLI 버전 2.2.0 이상을 설치합니다. 그런 다음, [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) 명령을 호출하여 스토리지 계정에 대한 지정 시간 복원 및 기타 필수 데이터 보호 설정을 사용하도록 설정합니다.

다음 예에서는 일시 삭제를 사용하도록 설정하고 일시 삭제 보존 기간을 14일로 설정하고, 변경 피드 및 버전 관리를 사용하도록 설정하고, 복원 기간이 7일인 지정 시간 복원을 사용하도록 설정합니다. 예제를 실행하는 경우 꺾쇠 괄호의 값을 고유한 값으로 바꿔야 합니다.

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-delete-retention true \
    --delete-retention-days 14 \
    --enable-versioning true \
    --enable-change-feed true \
    --enable-restore-policy true \
    --restore-days 7
```

---

## <a name="choose-a-restore-point"></a>복원 지점 선택

복원 지점은 데이터가 복원되는 날짜 및 시간입니다. Azure Storage는 항상 UTC 날짜/시간 값을 복원 지점으로 사용합니다. 그러나 Azure Portal을 사용하여 현지 시간으로 복원 지점을 지정하고 해당 날짜/시간 값을 UTC 날짜/시간 값으로 변환하여 복원 작업을 수행할 수 있습니다.

PowerShell 또는 Azure CLI를 사용하여 복원 작업을 수행하는 경우 복원 지점을 UTC 날짜/시간 값으로 지정해야 합니다. UTC 시간 값 대신 현지 시간 값을 사용하여 복원 지점을 지정하는 경우에도 복원 작업이 일부 경우에 여전히 예상대로 작동할 수 있습니다. 예를 들어, 현지 시간이 UTC에서 5시간을 뺀 시간과 같을 경우 현지 시간 값을 지정하면 제공한 값보다 5시간 이전이 복원 지점이 됩니다. 해당 5시간 동안 복원할 범위의 데이터를 변경하지 않은 경우에는 지정된 시간 값에 관계 없이 복원 작업은 동일한 결과를 생성합니다. 예기치 않은 결과를 방지하려면 복원 지점을 UTC 시간으로 지정하는 것이 좋습니다.

## <a name="perform-a-restore-operation"></a>복원 작업 수행

스토리지 계정의 모든 컨테이너를 복원하거나 하나 이상의 컨테이너에서 Blob 범위를 복원할 수 있습니다. Blob 범위는 사전순으로 정의됩니다. 복원 작업당 최대 10개의 사전순 범위가 지원됩니다. 범위의 시작은 포함되고 범위의 끝은 제외됩니다.

시작 범위와 끝 범위에 지정된 컨테이너 패턴은 최소 3자 이상을 포함해야 합니다. 컨테이너 이름을 Blob 이름과 구분하는 데 사용되는 슬래시(/)는 이 최솟값에 포함되지 않습니다.

와일드카드 문자는 사전순 범위에서 지원되지 않습니다. 모든 와일드카드 문자는 표준 문자로 처리됩니다.

복원 작업에 전달된 범위에서 명시적으로 지정하여 `$root` 및 `$web` 컨테이너에서 Blob을 복원할 수 있습니다. `$root` 및 `$web` 컨테이너는 명시적으로 지정된 경우에만 복원됩니다. 다른 시스템 컨테이너는 복원할 수 없습니다.

블록 Blob만 복원됩니다. 페이지 Blob 및 추가 Blob은 복원 작업에 포함되지 않습니다. 추가 Blob과 관련된 제한 사항에 대한 자세한 내용은 [블록 Blob에 대한 지정 시간 복원](point-in-time-restore-overview.md)을 참조하세요.

> [!IMPORTANT]
> 복원 작업 수행 시 Azure Storage는 작업 중 복원되는 범위의 BLOB에 대한 데이터 작업을 차단합니다. 읽기, 쓰기, 삭제 작업은 기본 위치에서 차단됩니다. 따라서 복원 작업이 진행되는 동안에는 Azure Portal의 컨테이너 나열 등의 작업이 정상적으로 수행되지 않을 수 있습니다.
>
> 스토리지 계정이 지역에서 복제되는 경우 보조 위치에서의 읽기 작업은 복원 작업 중에 진행될 수 있습니다.
>
> 일련의 데이터를 복원하는 데 걸리는 시간은 복원 작업을 선택하기 위한 최대 1시간 후 복원 기간 중에 수행된 쓰기 및 삭제 작업의 건수에 따라 달라집니다. 예를 들어 100만 개의 개체에 하루에 3천 개의 개체가 추가되고 1천 개의 개체가 삭제되는 계정의 경우 30일 전의 시점으로 복원하는 데 약 2~3시간이 소요됩니다. 변경 수가 적은 복원의 경우 복원하는 데 최대 1시간이 소요됩니다. 변동률이 이와 같은 계정에는 보존 기간과 복원 범위를 90일 전을 초과하지 않도록 설정하는 것이 좋습니다.

### <a name="restore-all-containers-in-the-account"></a>계정의 모든 컨테이너 복원

스토리지 계정의 모든 컨테이너를 복원하여 지정된 시점의 이전 상태로 되돌릴 수 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 스토리지 계정의 모든 컨테이너와 Blob을 복원하려면 다음 단계를 수행합니다.

1. 스토리지 계정에 대한 컨테이너의 목록으로 이동합니다.
1. 도구 모음에서 **컨테이너 복원**, **모두 복원** 을 차례로 선택합니다.
1. **모든 컨테이너 복원** 창에서 날짜 및 시간을 제공하여 복원 지점을 지정합니다.
1. 해당 확인란을 선택하여 계속 진행하도록 합니다.
1. **복원** 을 선택하여 복원 작업을 시작합니다.

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="모든 컨테이너를 지정된 복원 지점으로 복원하는 방법을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 스토리지 계정의 모든 컨테이너와 Blob을 복원하려면 **Restore-AzStorageBlobRange** 명령을 호출하고 복원 지점을 UTC 날짜/시간 값으로 제공합니다. 기본적으로 **Restore-AzStorageBlobRange** 명령은 비동기적으로 실행되며 복원 작업의 상태를 확인하는 데 사용할 수 있는 **PSBlobRestoreStatus** 형식의 개체를 반환합니다.

다음 예제에서는 스토리지 계정의 컨테이너를 현재 시간보다 12시간 이전 상태로 비동기적으로 복원하고 복원 작업의 속성 중 일부를 확인합니다.

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).ToUniversalTime().AddHours(-12)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the restore point in UTC time.
$restoreOperation.Parameters.TimeToRestore
```

복원 작업을 동기적으로 실행하려면 명령에 **-WaitForComplete** 매개 변수를 포함합니다. **-WaitForComplete** 매개 변수가 있으면 PowerShell에서는 작업에 대한 복원 ID를 포함하는 메시지를 표시한 다음, 복원 작업이 완료될 때까지 실행을 차단합니다. 복원 작업에 필요한 시간은 복원되는 데이터의 양에 따라 다르며, 대량 복원 작업은 완료하는 데 최대 1시간이 걸릴 수 있습니다.

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 스토리지 계정의 모든 컨테이너와 Blob을 복원하려면 [az storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) 명령을 호출하고 복원 지점을 UTC 날짜/시간 값으로 제공합니다.

다음 예제에서는 스토리지 계정의 모든 컨테이너를 지정된 날짜 및 시간보다 12시간 이전 상태로 비동기적으로 복원합니다. 복원 작업의 상태를 확인하려면 [az storage account show](/cli/azure/storage/account#az_storage_account_show)를 호출합니다.

```azurecli
az storage blob restore \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --no-wait
```

복원 작업의 속성을 확인하려면 [az storage account show](/cli/azure/storage/account#az_storage_account_show)를 호출하고 **blobRestoreStatus** 속성을 확장합니다. 다음 예제에서는 **status** 속성을 확인하는 방법을 보여 줍니다.

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource_group> \
    --expand blobRestoreStatus \
    --query blobRestoreStatus.status \
    --output tsv
```

**az storage blob restore** 명령을 동기적으로 실행하고 복원 작업이 완료될 때까지 실행을 차단하려면 `--no-wait` 매개 변수를 생략합니다.

---

### <a name="restore-ranges-of-block-blobs"></a>블록 Blob 범위 복원

지정된 시점에 해당 Blob을 이전 상태로 되돌리기 위해 단일 컨테이너 내에서 또는 여러 컨테이너에서 사전순으로 하나 이상의 Blob 범위를 복원할 수 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal을 사용하여 하나 이상의 컨테이너에 있는 Blob 범위를 복원하려면 다음 단계를 수행합니다.

1. 스토리지 계정에 대한 컨테이너의 목록으로 이동합니다.
1. 복원할 컨테이너를 선택합니다.
1. 도구 모음에서 **컨테이너 복원**, **선택한 복원** 을 차례로 선택합니다.
1. **선택한 컨테이너 복원** 창에서 날짜 및 시간을 제공하여 복원 지점을 지정합니다.
1. 복원할 범위를 지정합니다. 슬래시(/)를 사용하여 Blob 접두사에서 컨테이너 이름을 구분합니다.
1. 기본적으로 **선택한 컨테이너 복원** 창은 컨테이너의 모든 Blob을 포함하는 범위를 지정합니다. 전체 컨테이너를 복원하지 않으려면 이 범위를 삭제합니다. 기본 범위는 다음 그림에 나와 있습니다.

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="사용자 지정 범위를 지정하기 전에 삭제할 기본 Blob 범위를 보여 주는 스크린샷":::

1. 해당 확인란을 선택하여 계속 진행하도록 합니다.
1. **복원** 을 선택하여 복원 작업을 시작합니다.

다음 이미지는 범위 세트에 대한 복원 작업을 보여 줍니다.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="하나 이상의 컨테이너에서 Blob 범위를 복원하는 방법을 보여 주는 스크린샷":::

이미지에 표시된 복원 작업에서는 다음 작업을 수행합니다.

- ‘container1’의 전체 콘텐츠를 복원합니다.
- ‘container2’에서 사전순 범위 ‘blob1’~’blob5’의 Blob을 복원합니다. 이 범위는 ‘blob1’, ‘blob11’, ‘blob100’, ‘blob2’ 등의 이름으로 Blob을 복원합니다. 범위의 끝은 제외되므로 이름이 ‘blob4’로 시작하는 Blob은 복원하지만 이름이 ‘blob5’로 시작하는 Blob은 복원하지 않습니다.
- ‘container3’ 및 ‘container4’의 모든 Blob을 복원합니다. 범위의 끝은 제외되므로 이 범위는 ‘container5’는 복원하지 않습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

단일 Blob 범위를 복원하려면 **Restore-AzStorageBlobRange** 명령을 호출하고 `-BlobRestoreRange` 매개 변수에 대한 컨테이너 및 Blob 이름 범위를 사전순으로 지정합니다. 예를 들어 ‘container1’이라는 단일 컨테이너에 Blob을 복원하려면 ‘container1’으로 시작하고 ‘container2’로 끝나는 범위를 지정하면 됩니다. 시작 및 끝 범위에 명명된 컨테이너가 존재할 필요는 없습니다. 범위의 끝은 제외되기 때문에 스토리지 계정에 ‘container2’라는 컨테이너가 포함되어 있어도 ‘container1’이라는 컨테이너만 복원됩니다.

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

복원할 컨테이너에서 Blob의 하위 집합을 지정하려면 슬래시(/)를 사용하여 컨테이너 이름을 Blob 접두사 패턴과 구분합니다. 예를 들어 다음 범위는 단일 컨테이너에서 이름이 *d*~*f* 문자로 시작하는 Blob을 선택합니다.

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

그런 다음, **Restore-AzStorageBlobRange** 명령에 범위를 제공합니다. `-TimeToRestore` 매개 변수에 UTC **DateTime** 값을 제공하여 복원 지점을 지정합니다. 다음 예제는 지정된 범위의 Blob을 현재 시점에서 3일 전의 상태로 복원합니다.

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

기본적으로 **Restore-AzStorageBlobRange** 명령은 비동기적으로 실행됩니다. 복원 작업을 비동기적으로 시작하면 PowerShell에서 작업에 대한 속성 테이블을 즉시 표시합니다.

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

여러 블록 Blob 범위를 복원하려면 `-BlobRestoreRange` 매개 변수에 범위 배열을 지정합니다. 다음 예제에서는 *container1* 및 *container4* 의 전체 콘텐츠를 24시간 전 상태로 복원하는 두 범위를 지정하고 결과를 변수에 저장합니다.

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-24) `
    -BlobRestoreRange @($range1, $range2)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the blob ranges specified for the operation.
$restoreOperation.Parameters.BlobRanges
```

복원 작업을 동기적으로 실행하고 완료될 때까지 실행을 차단하려면 명령에 **-WaitForComplete** 매개 변수를 포함합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Blob 범위를 복원하려면 [az storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) 명령을 호출하고 `--blob-range` 매개 변수에 대한 컨테이너 및 Blob 이름 범위를 사전순으로 지정합니다. 여러 범위를 지정하려면 각 고유 범위에 대해 `--blob-range` 매개 변수를 제공합니다.

예를 들어 ‘container1’이라는 단일 컨테이너에 Blob을 복원하려면 ‘container1’으로 시작하고 ‘container2’로 끝나는 범위를 지정하면 됩니다. 시작 및 끝 범위에 명명된 컨테이너가 존재할 필요는 없습니다. 범위의 끝은 제외되기 때문에 스토리지 계정에 ‘container2’라는 컨테이너가 포함되어 있어도 ‘container1’이라는 컨테이너만 복원됩니다.

복원할 컨테이너에서 Blob의 하위 집합을 지정하려면 슬래시(/)를 사용하여 컨테이너 이름을 Blob 접두사 패턴과 구분합니다. 아래에 표시된 예제에서는 이름이 문자 `d`~`f`로 시작하는 컨테이너의 Blob 범위를 비동기적으로 복원합니다.

```azurecli
az storage blob restore \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --blob-range container1 container2
    --blob-range container3/d container3/g
    --no-wait
```

**az storage blob restore** 명령을 동기적으로 실행하고 복원 작업이 완료될 때까지 실행을 차단하려면 `--no-wait` 매개 변수를 생략합니다.

---

## <a name="next-steps"></a>다음 단계

- [블록 Blob에 대한 지정 시간 복원(미리 보기)](point-in-time-restore-overview.md)
- [일시 삭제](./soft-delete-blob-overview.md)
- [변경 피드](storage-blob-change-feed.md)
- [Blob 버전 관리](versioning-overview.md)
