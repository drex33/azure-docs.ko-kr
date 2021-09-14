---
title: Blob 버전에 대한 불변성 정책 구성(미리 보기)
titleSuffix: Azure Storage
description: 범위가 Blob 버전으로 지정된 불변성 정책을 구성하는 방법을 알아봅니다(미리 보기). 불변성 정책은 데이터를 지울 수 없고 수정할 수 없는 상태로 저장하여 Blob Storage에 대한 WORM(한 번 쓰기, 여러 번 읽기) 지원을 제공합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/31/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 04e05f67787b285dd1286e0c6b7a6b251262ed0f
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272244"
---
# <a name="configure-immutability-policies-for-blob-versions-preview"></a>Blob 버전에 대한 불변성 정책 구성(미리 보기)

Azure Blob Storage용 변경이 불가능한 스토리지를 사용하면 사용자가 중요 비즈니스용 데이터를 WORM(Write Once, Read Many) 상태로 저장할 수 있습니다. WORM 상태에 있는 동안에는 사용자가 데이터를 지정한 간격 동안 수정하거나 삭제할 수 없습니다. Blob 데이터에 대한 불변성 정책을 구성하면 데이터를 덮어쓰거나 삭제하지 못하도록 방지할 수 있습니다. 불변성 정책에는 시간 기반 보존 정책과 법적 보존이 포함됩니다. Blob Storage 불변성 정책에 대한 자세한 내용은 [비즈니스에 중요한 Blob 데이터를 변경이 불가능한 스토리지에 저장](immutable-storage-overview.md)을 참조하세요.

불변성 정책의 범위는 개별 Blob 버전(미리 보기) 또는 컨테이너로 지정할 수 있습니다. 이 문서에서는 버전 수준 불변성 정책을 구성하는 방법에 대해 설명합니다. 컨테이너 수준 불변성 정책을 구성하는 방법에 자세한 내용은 [컨테이너에 대한 불변성 정책 구성](immutable-policy-configure-container-scope.md)을 참조하세요.

버전 수준 불변성 정책 구성은 2단계 프로세스입니다.

1. 먼저 새 컨테이너 또는 기존 컨테이너에서 버전 수준 지원을 사용하도록 설정합니다. 자세한 내용은 [컨테이너에서 버전 수준 불변성 지원 사용](#enable-support-for-version-level-immutability-on-a-container)을 참조하세요.
1. 다음으로 해당 컨테이너에 있는 하나 이상의 Blob 버전에 적용되는 시간 기반 보존 정책 또는 법적 보존을 구성합니다.

> [!IMPORTANT]
> 버전 수준 시간 불변성 정책은 현재 **미리 보기** 에 있습니다. 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

버전 수준 시간 기반 보존 정책을 구성하려면 Blob 버전 관리를 스토리지 계정에 사용하도록 설정해야 합니다. Blob 버전 관리를 사용하도록 설정하는 방법을 알아보려면 [Blob 버전 관리 사용 및 관리](versioning-enable.md)를 참조하세요.

버전 수준 불변성 정책에 지원되는 스토리지 계정 구성에 대한 자세한 내용은 [지원되는 계정 구성](immutable-storage-overview.md#supported-account-configurations)을 참조하세요.

## <a name="enable-support-for-version-level-immutability-on-a-container"></a>컨테이너에서 버전 수준 불변성 지원 사용

시간 기반 보존 정책을 Blob 버전에 적용하려면 먼저 버전 수준 불변성 지원을 사용하도록 설정해야 합니다. 새 컨테이너와 기존 컨테이너 모두에서 버전 수준 불변성을 지원하도록 구성할 수 있습니다. 그러나 기존 컨테이너는 지원을 사용하도록 설정하기 위해 마이그레이션 프로세스를 거쳐야 합니다.

컨테이너에 대한 버전 수준 불변성 지원을 사용하도록 설정해도 해당 컨테이너의 데이터는 변경할 수 없습니다. 또한 컨테이너에 대한 기본 불변성 정책 또는 특정 Blob 버전에 대한 불변성 정책을 구성해야 합니다.

### <a name="enable-version-level-immutability-for-a-new-container"></a>새 컨테이너에 버전 수준 불변성 사용

버전 수준 불변성 정책을 사용하려면 먼저 컨테이너에서 버전 수준 WORM 지원을 명시적으로 사용하도록 설정해야 합니다. 컨테이너를 만들거나 버전 수준 불변성 정책을 기존 컨테이너에 추가할 때 버전 수준 WORM 지원을 사용하도록 설정할 수 있습니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 버전 수준 불변성을 지원하는 컨테이너를 만들려면 다음 단계를 수행합니다.

1. Azure Portal에서 스토리지 계정에 대한 **컨테이너** 페이지로 이동하여 **추가** 를 선택합니다.
1. **새 컨테이너** 대화 상자에서 컨테이너 이름을 제공한 다음, **고급** 섹션을 펼칩니다.
1. **버전 수준 불변성 지원 사용** 을 선택하여 버전 수준 불변성을 컨테이너에 사용하도록 설정합니다.

    :::image type="content" source="media/immutable-policy-configure-version-scope/create-container-version-level-immutability.png" alt-text="버전 수준 불변성이 사용하도록 설정된 컨테이너를 만드는 방법을 보여 주는 스크린샷":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 버전 수준 불변성을 지원하는 컨테이너를 만들려면 먼저 [Az.Storage 모듈](https://www.powershellgallery.com/packages/Az.Storage/3.10.1-preview) 버전 3.10.1-preview를 설치합니다.

그런 후에 다음 예제와 같이 `-EnableImmutableStorageWithVersioning` 매개 변수를 지정하여 **New-AzRmStorageContainer** 명령을 호출합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurepowershell
# Create a container with version-level immutability support.
$container = New-AzRmStorageContainer -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> `
    -EnableImmutableStorageWithVersioning

# Verify that version-level immutability support is enabled for the container
$container.ImmutableStorageWithVersioning
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 버전 수준 불변성을 지원하는 컨테이너를 만들려면 먼저 Azure CLI 버전 2.27 이상을 설치합니다. Azure CLI를 설치하는 방법에 대한 자세한 내용은 [Azure CLI를 설치하는 방법](/cli/azure/install-azure-cli)을 참조하세요.

그런 후에 `--enable-vlw` 매개 변수를 지정하여 [az storage container-rm create](/cli/azure/storage/container-rm#az_storage_container_rm_create) 명령을 호출합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
# Create a container with version-level immutability support.
az storage container-rm create \
    --name <container> \
    --storage-account <storage-account> \
    --resource-group <resource-group> \
    --enable-vlw

# Verify that version-level immutability support is enabled for the container
az storage container-rm show \
    --storage-account <storage-account> \
    --name <container> \
    --query '[immutableStorageWithVersioning.enabled]' \
    --output tsv
```

---

### <a name="migrate-an-existing-container-to-support-version-level-immutability"></a>버전 수준 불변성을 지원하도록 기존 컨테이너 마이그레이션

기존 컨테이너에 대한 버전 수준 불변성 정책을 구성하려면 변경이 불가능한 버전 수준 스토리지를 지원하도록 컨테이너를 마이그레이션해야 합니다. 컨테이너를 마이그레이션하는 데 시간이 다소 걸릴 수 있으며, 이를 되돌릴 수 없습니다.

버전 수준 불변성 정책을 지원하도록 기존 컨테이너를 마이그레이션하려면 컨테이너에 컨테이너 수준 시간 기반 보존 정책이 구성되어 있어야 합니다. 컨테이너에 기존 정책이 없으면 마이그레이션에 실패합니다. 컨테이너 수준 정책의 보존 간격은 컨테이너에 대한 기본 버전 수준 정책의 보존 간격으로 유지됩니다.

컨테이너에 기존 컨테이너 수준 법적 보존이 있는 경우 이 법적 보존이 제거될 때까지 마이그레이션할 수 없습니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 변경이 불가능한 버전 수준 스토리지를 지원하도록 컨테이너를 마이그레이션하려면 다음 단계를 수행합니다.

1. 원하는 컨테이너로 이동합니다.
1. 오른쪽에 있는 **자세히** 단추를 선택한 다음, **액세스 정책** 을 선택합니다.
1. **변경 불가능한 Blob 스토리지** 아래에서 **정책 추가** 를 선택합니다.
1. **정책 유형** 필드에서 *시간 기반 보존* 을 선택하고, 보존 간격을 지정합니다.
1. **버전 수준 불변성 사용** 을 선택합니다.
1. **확인** 을 선택하여 지정된 보존 간격으로 컨테이너 수준 정책을 만든 다음, 버전 수준 불변성 지원으로 마이그레이션을 시작합니다.

    :::image type="content" source="media/immutable-policy-configure-version-scope/migrate-existing-container.png" alt-text="버전 수준 불변성을 지원하도록 기존 컨테이너를 마이그레이션하는 방법을 보여 주는 스크린샷":::

마이그레이션 작업이 진행되는 동안 컨테이너에 대한 정책 범위는 ‘컨테이너’로 표시됩니다.

:::image type="content" source="media/immutable-policy-configure-version-scope/container-migration-in-process.png" alt-text="진행 중인 컨테이너 마이그레이션 스크린샷":::

마이그레이션이 완료되면 컨테이너에 대한 정책 범위가 ‘버전’으로 표시됩니다. 표시된 정책은 컨테이너에 대한 기본 정책으로, 이후 컨테이너에서 생성된 모든 Blob 버전에 자동으로 적용됩니다. 버전에 대한 사용자 지정 정책을 지정하여 해당 버전의 기본 정책을 재정의할 수 있습니다.

:::image type="content" source="media/immutable-policy-configure-version-scope/container-migration-complete.png" alt-text="완료된 컨테이너 마이그레이션 스크린샷":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 버전 수준 변경이 불가능한 스토리지를 지원하도록 컨테이너를 마이그레이션하려면 먼저 컨테이너에 대한 컨테이너 수준 시간 기반 보존 정책이 있는지 확인합니다. 새로 만들려면 [Set-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy)를 호출합니다.

```azurepowershell
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
   -StorageAccountName <storage-account> `
   -ContainerName <container> `
   -ImmutabilityPeriod <retention-interval-in-days>
```

그런 후에 **Invoke-AzRmStorageContainerImmutableStorageWithVersioningMigration** 명령을 호출하여 컨테이너를 마이그레이션합니다. 명령을 비동기적으로 실행하려면 `-AsJob` 매개 변수를 포함합니다. 마이그레이션을 완료하는 데 시간이 걸릴 수 있기 때문에 작업을 비동기적으로 실행하는 것이 좋습니다.

```azurepowershell
$migrationOperation = Invoke-AzRmStorageContainerImmutableStorageWithVersioningMigration `
    -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> `
    -AsJob
```

장기 실행 작업의 상태를 확인하려면 작업의 **JobStateInfo.State** 속성을 읽습니다.

```azurepowershell
$migrationOperation.JobStateInfo.State
```

컨테이너에 기존 시간 기반 보존 정책이 없는 상태에서 버전 수준 불변성으로 마이그레이션하려고 하면 작업이 실패합니다. 다음 예제에서는 **JobStateInfo.State** 속성의 값을 확인하고, 컨테이너 수준 정책이 없어서 작업이 실패한 경우 오류 메시지를 표시합니다.

```azurepowershell
if ($migrationOperation.JobStateInfo.State -eq "Failed") {
Write-Host $migrationOperation.Error
}
The container <container-name> must have an immutability policy set as a default policy 
before initiating container migration to support object level immutability with versioning.
```

마이그레이션이 완료된 후 작업의 **Output** 속성을 검사하여 버전 수준 불변성 지원을 사용할 수 있는지 확인합니다.

```azurepowershell
$migrationOperation.Output
```

PowerShell 작업에 대한 자세한 내용은 [PowerShell 작업에서 Azure PowerShell cmdlet 실행](/powershell/azure/using-psjobs)을 참조하세요.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 버전 수준 변경이 불가능한 스토리지를 지원하도록 컨테이너를 마이그레이션하려면 먼저 컨테이너에 대한 컨테이너 수준 시간 기반 보존 정책이 있는지 확인합니다. 새로 만들려면 [az storage container immutability-policy create](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_create)를 호출합니다.

```azurecli
az storage container immutability-policy create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --container-name <container> \
    --period <retention-interval-in-days>
```

그런 후에 [az storage container-rm migrate-vlw](/cli/azure/storage/container-rm#az_storage_container_rm_migrate_vlw) 명령을 호출하여 컨테이너를 마이그레이션합니다. 명령을 비동기적으로 실행하려면 `--no-wait` 매개 변수를 포함합니다. 마이그레이션을 완료하는 데 시간이 걸릴 수 있기 때문에 작업을 비동기적으로 실행하는 것이 좋습니다.

```azurecli
az storage container-rm migrate-vlw \
    --resource-group <resource-group> \
    --storage-account <storage-account> \
    --name <container> \
    --no-wait
```

장기 실행 작업의 상태를 확인하려면 **migrationState** 속성의 값을 읽습니다.

```azurecli
az storage container-rm show \
    --storage-account <storage-account> \
    --name <container> \
    --query '[immutableStorageWithVersioning.migrationState]' \
    --output tsv
```

---

## <a name="configure-a-time-based-retention-policy-on-a-container"></a>컨테이너에 대한 시간 기반 보존 정책 구성

버전 수준 불변성이 컨테이너에 사용하도록 설정되면 컨테이너에 대한 기본 버전 수준 시간 기반 보존 정책을 지정할 수 있습니다. 컨테이너에 대한 기본 정책이 지정되면 해당 정책은 기본적으로 컨테이너에 만든 모든 새 Blob 버전에 적용됩니다. 컨테이너의 개별 Blob 버전에 대한 기본 정책을 재정의할 수 있습니다.

기본 정책이 구성되기 전에 있던 Blob 버전에는 해당 기본 정책이 자동으로 적용되지 않습니다.

버전 수준 불변성을 지원하도록 기존 컨테이너를 마이그레이션한 경우 마이그레이션 전에 적용된 컨테이너 수준 정책이 컨테이너의 기본 버전 수준 정책으로 마이그레이션됩니다.

### <a name="configure-a-default-time-based-retention-policy-on-a-container"></a>컨테이너에 대한 기본 시간 기반 보존 정책 구성

컨테이너의 기본 버전 수준 불변성 정책을 구성하려면 Azure Portal, PowerShell, Azure CLI 또는 Azure Storage SDK 중 하나를 사용합니다. [컨테이너에서 버전 수준 불변성 지원 사용](#enable-support-for-version-level-immutability-on-a-container)에 설명된 대로 컨테이너에 대해 버전 수준 불변성 지원을 사용하도록 설정했는지 확인합니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 컨테이너의 기본 버전 수준 불변성 정책을 구성하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **컨테이너** 페이지로 이동하여 정책을 적용하려는 컨테이너를 찾습니다.
1. 컨테이너 이름의 오른쪽에 있는 **자세히** 단추를 선택하고, **액세스 정책** 을 선택합니다.
1. **액세스 정책** 대화 상자의 **변경이 불가능한 Blob 스토리지** 섹션 아래에서 **정책 추가** 를 선택합니다.
1. **시간 기반 보존 정책** 을 선택하고, 보존 간격을 지정합니다.
1. 원하는 경우 **추가적인 보호된 추가 허용** 을 선택하여 불변성 정책으로 보호되는 추가 Blob에 쓰기를 사용하도록 설정합니다. 자세한 내용은 [보호된 추가 Blob 쓰기 허용](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes) 섹션을 참조하세요.
1. **확인** 을 선택하여 기본 정책을 컨테이너에 적용합니다.

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-default-retention-policy-container.png" alt-text="컨테이너에 대한 기본 버전 수준 보존 정책을 구성하는 방법을 보여 주는 스크린샷":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 컨테이너의 기본 버전 수준 불변성 정책을 구성하려면 [Set-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy) 명령을 호출합니다.

```azurepowershell
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
   -StorageAccountName <storage-account> `
   -ContainerName <container> `
   -ImmutabilityPeriod <retention-interval-in-days> `
   -AllowProtectedAppendWrite $true
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 컨테이너의 기본 버전 수준 불변성 정책을 구성하려면 [az storage container immutability-policy create](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_create) 명령을 호출합니다.

```azurecli
az storage container immutability-policy create \
    --account-name <storage-account> \
    --container-name <container> \
    --period <retention-interval-in-days> \
    --allow-protected-append-writes true
```

---

### <a name="determine-the-scope-of-a-retention-policy-on-a-container"></a>컨테이너에 대한 보존 정책 범위 결정

Azure Portal에서 시간 기반 보존 정책의 범위를 결정하려면 다음 단계를 수행합니다.

1. 원하는 컨테이너로 이동합니다.
1. 오른쪽에 있는 **자세히** 단추를 선택한 다음, **액세스 정책** 을 선택합니다.
1. **변경이 불가능한 Blob 스토리지** 아래에서 **범위** 필드를 찾습니다. 컨테이너가 기본 버전 수준 보존 정책으로 구성된 경우 범위는 다음 이미지와 같이 *버전* 으로 설정됩니다.

    :::image type="content" source="media/immutable-policy-configure-version-scope/version-scoped-retention-policy.png" alt-text="컨테이너에 대해 구성된 기본 버전 수준 보존 정책을 보여 주는 스크린샷":::

1. 컨테이너가 컨테이너 수준 보존 정책으로 구성된 경우 범위는 다음 이미지와 같이 *컨테이너* 로 설정됩니다.

    :::image type="content" source="media/immutable-policy-configure-version-scope/container-scoped-retention-policy.png" alt-text="컨테이너에 대해 구성된 컨테이너 수준 보존 정책을 보여 주는 스크린샷":::

## <a name="configure-a-time-based-retention-policy-on-an-existing-version"></a>기존 버전에 대한 시간 기반 보존 정책 구성

시간 기반 보존 정책은 Blob 데이터를 지정된 간격 동안 WORM 상태로 유지합니다. 시간 기반 보존 정책에 대한 자세한 내용은 [변경이 불가능한 Blob 데이터에 대한 시간 기반 보존 정책](immutable-time-based-retention-policy-overview.md)을 참조하세요.

Blob 버전에 대한 시간 기반 보존 정책을 구성할 수 있는 세 가지 옵션이 있습니다.

- 옵션 1: 범위가 컨테이너로 지정되고 기본적으로 컨테이너의 모든 개체에 적용되는 기본 정책을 구성할 수 있습니다. 컨테이너의 개체는 개별 Blob 버전에 대한 정책을 구성하여 명시적으로 재정의하는 경우를 제외하고는 기본 정책을 상속합니다. 자세한 내용은 [컨테이너에 대한 기본 시간 기반 보존 정책 구성](#configure-a-default-time-based-retention-policy-on-a-container)을 참조하세요.
- 옵션 2: 현재 버전의 Blob에 대한 정책을 구성할 수 있습니다. 이 정책은 컨테이너에 대해 구성된 기본 정책이 있고 잠금 해제된 경우 해당 정책을 재정의할 수 있습니다. 정책이 구성된 후에 만든 모든 이전 버전은 기본적으로 현재 버전의 Blob에 대한 정책을 상속합니다. 자세한 내용은 [현재 버전의 Blob에 대한 보존 정책 구성](#configure-a-retention-policy-on-the-current-version-of-a-blob)을 참조하세요.
- 옵션 3: 이전 버전의 Blob에 대한 정책을 구성할 수 있습니다. 이 정책은 현재 버전에 대해 구성된 기본 정책이 있고 잠금 해제된 경우 해당 정책을 재정의할 수 있습니다. 자세한 내용은 [이전 버전의 Blob에 대한 보존 정책 구성](#configure-a-retention-policy-on-a-previous-version-of-a-blob)이전 버전의 Blob에서 보존 정책 구성을 참조하세요.

Blob 버전 관리에 대한 자세한 내용은 [Blob 버전 관리](versioning-overview.md)를 참조하세요.

### <a name="portal"></a>[포털](#tab/azure-portal)

컨테이너로 이동하면 Azure Portal에서 Blob 목록을 표시합니다. 표시된 각 Blob은 현재 버전의 Blob을 나타냅니다. Blob에 대해 **자세히** 단추를 선택한 다음, **이전 버전 보기** 를 선택하여 이전 버전 목록에 액세스할 수 있습니다.  

### <a name="configure-a-retention-policy-on-the-current-version-of-a-blob"></a>현재 버전의 Blob에 대한 보존 정책 구성

현재 버전의 Blob에 대한 시간 기반 보존 정책을 구성하려면 다음 단계를 수행합니다.

1. 대상 Blob이 포함된 컨테이너로 이동합니다.
1. Blob 이름의 오른쪽에 있는 **자세히** 단추를 선택하고, **액세스 정책** 을 선택합니다. 이전 버전에 대해 시간 기반 보존 정책이 이미 구성된 경우 해당 정책이 **액세스 정책** 대화 상자에 표시됩니다.
1. **액세스 정책** 대화 상자의 **변경이 불가능한 Blob 버전** 섹션 아래에서 **정책 추가** 를 선택합니다.
1. **시간 기반 보존 정책** 을 선택하고, 보존 간격을 지정합니다.
1. **확인** 을 선택하여 정책을 현재 버전의 Blob에 적용합니다.

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-version.png" alt-text="현재 버전의 Blob에 대한 보존 정책을 구성하는 방법을 보여 주는 스크린샷":::

Blob에 대한 속성을 보고 현재 버전에 대한 정책이 사용하도록 설정되어 있는지 여부를 확인할 수 있습니다. Blob을 선택한 다음, **개요** 탭으로 이동하여 **버전 수준 불변성 정책** 속성을 찾습니다. 정책이 사용하도록 설정되면 **보존 기간** 속성에서 정책에 대한 만료 날짜와 시간을 표시합니다. 정책은 현재 버전에 대해 구성되거나 기본 정책이 적용되는 경우 Blob의 부모 컨테이너에서 상속될 수 있습니다.

:::image type="content" source="media/immutable-policy-configure-version-scope/view-version-level-retention-policy-portal.png" alt-text="Azure Portal에서 Blob 버전에 대한 불변성 정책 속성을 보여 주는 스크린샷":::

### <a name="configure-a-retention-policy-on-a-previous-version-of-a-blob"></a>이전 버전의 Blob에 대한 보존 정책 구성

이전 버전의 Blob에 대한 시간 기반 보존 정책도 구성할 수 있습니다. 이전 버전은 수정할 수 없다는 점에서 항상 변경할 수 없습니다. 그러나 이전 버전을 삭제할 수는 있습니다. 시간 기반 보존 정책은 적용되는 동안 삭제하지 못하도록 보호합니다.

이전 버전의 Blob에 대한 시간 기반 보존 정책을 구성하려면 다음 단계를 수행합니다.

1. 대상 Blob이 포함된 컨테이너로 이동합니다.
1. Blob을 선택한 다음, **버전** 탭으로 이동합니다.
1. 대상 버전을 찾은 다음, **자세히** 단추, **액세스 정책** 을 차례로 선택합니다. 이전 버전에 대해 시간 기반 보존 정책이 이미 구성된 경우 해당 정책이 **액세스 정책** 대화 상자에 표시됩니다.
1. **액세스 정책** 대화 상자의 **변경이 불가능한 Blob 버전** 섹션 아래에서 **정책 추가** 를 선택합니다.
1. **시간 기반 보존 정책** 을 선택하고, 보존 간격을 지정합니다.
1. **확인** 을 선택하여 정책을 현재 버전의 Blob에 적용합니다.

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-previous-version.png" alt-text="Azure Portal에서 이전 Blob 버전에 대한 보존 정책을 구성하는 방법을 보여 주는 스크린샷":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 Blob 버전에 대한 시간 기반 보존 정책을 구성하려면 **Set-AzStorageBlobImmutabilityPolicy** 명령을 호출합니다.

```azurepowershell
# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName <resource-group> `
        -Name <storage-account>).Context

Set-AzStorageBlobImmutabilityPolicy -Container <container> `
    -Blob <blob-version> `
    -Context $ctx `
    -ExpiresOn "2021-09-01T12:00:00Z" `
    -PolicyMode Unlocked
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

해당 없음

---

## <a name="configure-a-time-based-retention-policy-when-uploading-a-blob"></a>Blob을 업로드할 때 시간 기반 보존 정책 구성

Azure Portal을 사용하여 버전 수준 불변성을 지원하는 컨테이너에 Blob을 업로드하는 경우 새 Blob에 대한 시간 기반 보존 정책을 구성할 수 있는 몇 가지 옵션이 있습니다.

- 옵션 1: 컨테이너에 대해 기본 보존 정책이 구성된 경우 컨테이너의 정책을 사용하여 Blob을 업로드할 수 있습니다. 컨테이너에 대한 보존 정책이 있는 경우 이 옵션이 기본적으로 선택됩니다.
- 옵션 2: 컨테이너에 대해 기본 보존 정책이 구성된 경우 새 Blob에 대한 사용자 지정 보존 정책을 정의하거나 정책을 사용하지 않고 Blob을 업로드하여 기본 정책을 재정의하도록 선택할 수 있습니다.
- 옵션 3: 컨테이너에 대해 기본 정책이 구성되지 않은 경우 사용자 지정 정책을 사용하거나 정책을 사용하지 않고 Blob을 업로드할 수 있습니다.

Blob을 업로드할 때 시간 기반 보존 정책을 구성하려면 다음 단계를 수행합니다.

1. 원하는 컨테이너로 이동하여 **업로드** 를 선택합니다.
1. Blob **업로드** 대화 상자에서 **고급** 섹션을 펼칩니다.
1. **보존 정책** 필드에서 새 Blob에 대한 시간 기반 보존 정책을 구성합니다. 컨테이너에 대해 구성된 기본 정책이 있는 경우 해당 정책이 기본적으로 선택됩니다. Blob에 대한 사용자 지정 정책도 지정할 수 있습니다.

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-blob-upload.png" alt-text="Azure Portal에서 Blob 업로드에 대한 보존 정책을 구성할 수 있는 옵션을 보여 주는 스크린샷":::

## <a name="modify-or-delete-an-unlocked-retention-policy"></a>잠금 해제된 보존 정책 수정 또는 삭제

보존 간격을 줄이거나 늘리도록 잠금 해제된 시간 기반 보존 정책을 수정할 수 있습니다. 잠금 해제된 정책을 삭제할 수도 있습니다. Blob 버전에 대한 잠금 해제된 시간 기반 보존 정책을 편집하거나 삭제해도 다른 버전에 적용되는 정책에는 영향을 주지 않습니다. 컨테이너에 적용되는 기본 시간 기반 보존 정책이 있는 경우 수정되거나 삭제된 정책이 있는 Blob 버전은 더 이상 컨테이너에서 상속되지 않습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 잠금 해제된 시간 기반 보존 정책을 수정하려면 다음 단계를 수행합니다.

1. 대상 컨테이너 또는 버전을 찾습니다. **자세히** 단추, **액세스 정책** 을 차례로 선택합니다.
1. 잠금 해제된 기존 불변성 정책을 찾습니다. **자세히** 단추를 선택한 다음, 메뉴에서 **편집** 을 선택합니다.

    :::image type="content" source="media/immutable-policy-configure-version-scope/edit-existing-version-policy.png" alt-text="Azure Portal에서 기존 버전 수준 시간 기반 보존 정책을 편집하는 방법을 보여 주는 스크린샷":::

1. 정책 만료에 대한 새 날짜와 시간을 제공합니다.

잠금 해제된 정책을 삭제하려면 **자세히** 메뉴에서 **삭제** 를 선택합니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 잠금 해제된 시간 기반 보존 정책을 수정하려면 정책 만료 날짜와 시간을 새로 지정하여 Blob 버전에서 **Set-AzStorageBlobImmutabilityPolicy** 명령을 호출합니다.

```azurepowershell
$containerName = "<container>"
$blobName = "<blob>"

# Get the previous blob version.
$blobVersion = Get-AzStorageBlob -Container $containerName `
    -Blob $blobName `
    -VersionId "2021-08-31T00:26:41.2273852Z" `
    -Context $ctx

# Extend the retention interval by five days.
$blobVersion = $blobVersion | 
    Set-AzStorageBlobImmutabilityPolicy -ExpiresOn (Get-Date).AddDays(5) `

# View the new policy parameters.
$blobVersion.BlobProperties.ImmutabilityPolicy
```

잠금 해제된 보존 정책을 삭제하려면 **Remove-AzStorageBlobImmutabilityPolicy** 명령을 호출합니다.

```azurepowershell
$blobVersion = $blobVersion | Remove-AzStorageBlobImmutabilityPolicy
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

해당 없음

---

## <a name="lock-a-time-based-retention-policy"></a>시간 기반 보존 정책 잠금

시간 기반 보존 정책 테스트가 완료되면 해당 정책을 잠글 수 있습니다. 잠긴 정책은 SEC 17a-4(f) 및 기타 규정을 준수합니다. 잠긴 정책의 보존 간격을 최대 5배까지 늘릴 수 있지만, 줄일 수는 없습니다.

정책이 잠기면 해당 정책을 삭제할 수 없습니다. 하지만 보존 간격이 만료된 후에는 Blob을 삭제할 수 있습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 정책을 잠그려면 다음 단계를 수행합니다.

1. 대상 컨테이너 또는 버전을 찾습니다. **자세히** 단추, **액세스 정책** 을 차례로 선택합니다.
1. **변경이 불가능한 Blob 버전** 섹션 아래에서 잠금 해제된 기존 정책을 찾습니다. **자세히** 단추를 선택한 다음, 메뉴에서 **잠금 정책** 을 선택합니다.
1. 정책을 잠글 것인지 확인합니다.

    :::image type="content" source="media/immutable-policy-configure-version-scope/lock-policy-portal.png" alt-text="Azure Portal에서 시간 기반 보존 정책을 잠그는 방법을 보여 주는 스크린샷":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 정책을 잠그려면 **Set-AzStorageBlobImmutabilityPolicy** 명령을 호출하고 **PolicyMode** 매개 변수를 *Locked* 로 설정합니다.

다음 예제에서는 잠금 해제된 정책에 대해 적용된 것과 동일한 보존 간격을 지정하여 정책을 잠그는 방법을 보여 줍니다. 정책을 잠글 때 만료를 변경할 수도 있습니다.

```azurepowershell
# Get the previous blob version.
$blobVersion = Get-AzStorageBlob -Container $containerName `
    -Blob $blobName `
    -VersionId "2021-08-31T00:26:41.2273852Z" `
    -Context $ctx

$blobVersion = $blobVersion | 
    Set-AzStorageBlobImmutabilityPolicy `
        -ExpiresOn $blobVersion.BlobProperties.ImmutabilityPolicy.ExpiresOn `
        -PolicyMode Locked
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

해당 없음

---

## <a name="configure-or-clear-a-legal-hold"></a>법적 보존 구성 또는 취소

법적 보존이 명시적으로 취소될 때까지 해당 법적 보존에서 변경이 불가능한 데이터를 저장합니다. 법적 보존 정책에 대한 자세한 내용은 [변경이 불가능한 Blob 데이터에 대한 법적 보존](immutable-legal-hold-overview.md)을 참조하세요.

#### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 Blob 버전에 대한 법적 보존을 구성하려면 다음 단계를 수행합니다.

1. Blob의 현재 버전 또는 이전 버전일 수 있는 대상 버전을 찾습니다. **자세히** 단추, **액세스 정책** 을 차례로 선택합니다.
1. **변경이 불가능한 Blob 버전** 섹션 아래에서 **정책 추가** 를 선택합니다.
1. 정책 유형으로 **법적 보존** 을 선택하고, **확인** 을 선택하여 적용합니다.

다음 이미지에서는 시간 기반 보존 정책과 법적 보존이 모두 구성된 현재 버전의 Blob을 보여 줍니다.

:::image type="content" source="media/immutable-policy-configure-version-scope/configure-legal-hold-blob-version.png" alt-text="Blob 버전에 대해 구성된 법적 보존을 보여 주는 스크린샷":::

법적 보존을 취소하려면 **액세스 정책** 대화 상자로 이동하여 **자세히** 단추, **삭제** 를 차례로 선택합니다.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 Blob 버전에 대한 법적 보존을 구성하거나 지우려면 **Set-AzStorageBlobLegalHold** 명령을 호출합니다.

```azurepowershell
# Set a legal hold
Set-AzStorageBlobLegalHold -Container <container> `
    -Blob <blob-version> `
    -Context $ctx `
    -EnableLegalHold

# Clear a legal hold
Set-AzStorageBlobLegalHold -Container <container> `
    -Blob <blob-version> `
    -Context $ctx `
    -DisableLegalHold
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

해당 없음

---

## <a name="next-steps"></a>다음 단계

- [비즈니스에 중요한 BLOB 데이터를 변경이 불가능한 스토리지에 저장](immutable-storage-overview.md)
- [변경이 불가능한 Blob 데이터에 대한 시간 기반 보존 정책](immutable-time-based-retention-policy-overview.md)
- [변경이 불가능한 Blob 데이터에 대한 법적 보존](immutable-legal-hold-overview.md)
