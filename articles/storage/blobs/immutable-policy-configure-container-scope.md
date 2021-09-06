---
title: 컨테이너에 대한 불변성 정책 구성
titleSuffix: Azure Storage
description: 범위가 컨테이너로 지정된 불변성 정책을 구성하는 방법을 알아봅니다. 불변성 정책은 데이터를 지울 수 없고 수정할 수 없는 상태로 저장하여 Blob Storage에 대한 WORM(Write Once, Read Many)을 지원합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/16/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1cdc40b8aebe2d80553a23deec3990d4349ebd79
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255612"
---
# <a name="configure-immutability-policies-for-containers"></a>컨테이너에 대한 불변성 정책 구성

변경이 불가능한 스토리지를 Azure Blob Storage에 사용하면 사용자가 중요 비즈니스용 데이터를 WORM(Write Once, Read Many) 상태로 저장할 수 있습니다. WORM 상태인 동안에는 사용자가 지정한 간격 동안 데이터를 수정하거나 삭제할 수 없습니다. Blob 데이터에 대한 불변성 정책을 구성하면 데이터를 덮어쓰거나 삭제하지 못하게 방지할 수 있습니다. 불변성 정책에는 시간 기반 보존 정책과 법적 보존이 포함됩니다. Blob Storage 불변성 정책에 대한 자세한 내용은 [비즈니스에 중요한 BLOB 데이터를 변경이 불가능한 스토리지에 저장](immutable-storage-overview.md)을 참조하세요.

불변성 정책의 적용 범위를 개별 Blob 버전(미리 보기) 또는 컨테이너로 지정할 수 있습니다. 이 문서에서는 컨테이너 수준 불변성 정책을 구성하는 방법을 설명합니다. 버전 수준 불변성 정책을 구성하는 방법은 [Blob 버전 수준 불변성 정책 구성(미리 보기)](immutable-policy-configure-version-scope.md)을 참조하세요.

## <a name="configure-a-retention-policy-on-a-container"></a>컨테이너의 보존 정책 구성

컨테이너의 시간 기반 보존 정책을 구성하려면 Azure Portal, PowerShell 또는 Azure CLI를 사용합니다. 컨테이너 수준 보존 정책은 1일~146000일 사이에서 구성할 수 있습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 컨테이너의 시간 기반 보존 정책을 구성하려면 다음 단계를 수행합니다.

1. 원하는 컨테이너로 이동합니다.
1. 오른쪽에서 **자세히** 단추를 선택한 다음, **액세스 정책** 을 선택합니다.
1. **변경이 불가능한 Blob Storage** 에서 **정책 추가** 를 선택합니다.
1. **정책 종류** 필드에서 **시간 기반 보존** 을 선택하고 보존 기간(일)을 지정합니다.
1. 컨테이너 범위를 사용하는 정책을 만들려면 **버전 수준 불변성 사용** 확인란을 선택하지 마세요.
1. 원한다면 **보호된 추가 허용** 을 선택하여 불변성 정책을 통해 보호되는 추가 Blob에 쓰기를 사용하도록 설정합니다. 자세한 내용은 [보호된 추가 Blob 쓰기 허용](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes) 섹션을 참조하세요.

    :::image type="content" source="media/immutable-policy-configure-container-scope/configure-retention-policy-container-scope.png" alt-text="범위가 컨테이너로 지정된 불변성 정책을 구성하는 방법을 보여주는 스크린샷":::

불변성 정책을 구성한 후에는 범위가 컨테이너로 지정된 것을 확인할 수 있습니다.

:::image type="content" source="media/immutable-policy-configure-container-scope/view-retention-policy-container-scope.png" alt-text="범위가 컨테이너로 지정된 기존 불변성 정책을 보여주는 스크린샷":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 컨테이너의 시간 기반 보존 정책을 구성하려면 [Set-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy) 명령을 호출하고 보존 간격(일)을 입력합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurepowershell
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -ContainerName <container> `
    -ImmutabilityPeriod 10
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 컨테이너의 시간 기반 보존 정책을 구성하려면 [az storage container immutability-policy create](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_create) 명령을 호출하고 보존 간격(일)을 입력합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage container immutability-policy \
    --resource-group <resource-group>
    --account-name <storage-account> \
    --container-name <container> \
    --period 10
```

---

## <a name="modify-an-unlocked-retention-policy"></a>잠금 해제된 보존 정책 수정

잠금 해제된 시간 기반 보존 정책을 수정하여 보존 간격을 줄이거나 늘리고, 컨테이너의 추가 Blob에 대한 쓰기를 추가로 허용할 수 있습니다. 잠금 해제된 정책을 삭제할 수도 있습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 잠금 해제된 시간 기반 보존 정책을 수정하려면 다음 단계를 수행합니다.

1. 원하는 컨테이너로 이동합니다.
1. **자세히** 단추를 선택하고 **액세스 정책** 을 선택합니다.
1. **변경이 불가능한 Blob 버전** 섹션에서 잠금이 해제된 기존 정책을 찾습니다. **자세히** 단추를 선택한 다음, 메뉴에서 **편집** 을 선택합니다.
1. 정책의 새 보존 간격을 입력합니다. **보호된 추가 허용** 을 선택하여 보호된 추가 Blob에 쓰기를 허용할 수도 있습니다.

    :::image type="content" source="media/immutable-policy-configure-container-scope/modify-retention-policy-container-scope.png" alt-text="잠금 해제된 시간 기반 보존 정책을 수정하는 방법을 보여주는 스크린샷":::

잠금 해제된 정책을 삭제하려면 **자세히** 단추를 선택하고 **삭제** 를 선택합니다.

> [!NOTE]
> 버전 수준 불변성 사용 확인란을 선택하여 버전 수준 불변성 정책(미리 보기)을 사용하도록 설정할 수 있습니다. 버전 수준 불변성 정책 사용에 대한 자세한 내용은 [Blob 버전 수준 불변성 정책 구성(미리 보기)](immutable-policy-configure-version-scope.md)을 참조하세요.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

잠금 해제된 정책을 수정하려면 먼저 [Get-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/get-azrmstoragecontainerimmutabilitypolicy) 명령을 호출하여 정책을 검색합니다. 다음으로, [AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy) 명령을 호출하여 정책을 업데이트합니다. 새 보존 간격(일) 및 `-ExtendPolicy` 매개 변수를 포함합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurepowershell
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container>

Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -ContainerName <container> `
    -ImmutabilityPeriod 21 `
    -AllowProtectedAppendWrite true `
    -Etag $policy.Etag `
    -ExtendPolicy
```

잠금 해제된 정책을 삭제하려면 [Remove-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/remove-azrmstoragecontainerimmutabilitypolicy) 명령을 호출합니다.

```azurepowershell
Remove-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container> 
    -Etag $policy.Etag
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 잠금 해제된 시간 기반 보존 정책을 수정하려면 [az storage container immutability-policy extend](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_extend) 명령을 호출하고 새 보존 간격(일)을 입력합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
$etag=$(az storage container immutability-policy show /
        --account-name <storage-account> /
        --container-name <container> /
        --query etag /
        --output tsv) 

az storage container immutability-policy \
    --resource-group <resource-group>
    --account-name <storage-account> \
    --container-name <container> \
    --period 21 \
    --if-match $etag \
    --allow-protected-append-writes true 
```

잠금 해제된 정책을 삭제하려면 [az storage container immutability-policy delete](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_delete) 명령을 호출합니다.

---

## <a name="lock-a-time-based-retention-policy"></a>시간 기준 보존 정책 잠금

시간 기반 보존 정책 테스트를 마친 후에는 정책을 잠글 수 있습니다. 잠긴 정책은 SEC 17a-4(f) 및 기타 규정을 따릅니다. 잠긴 정책의 보존 간격을 최대 5회까지 늘릴 수 있지만, 줄일 수는 없습니다.

잠긴 정책은 삭제할 수 없습니다. 하지만 보존 간격이 만료된 후에는 Blob을 삭제할 수 있습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 정책을 잠그려면 다음 단계를 수행합니다.

1. 잠긴 정책이 있는 컨테이너로 이동합니다.
1. **변경이 불가능한 Blob 버전** 섹션에서 잠금이 해제된 기존 정책을 찾습니다. **자세히** 단추를 선택한 다음, 메뉴에서 **정책 잠금** 을 선택합니다.
1. 정책을 잠그려 한다는 것을 확인합니다.

:::image type="content" source="media/immutable-policy-configure-container-scope/lock-retention-policy.png" alt-text="Azure Portal에서 시간 기반 보존 정책을 잠그는 방법을 보여주는 스크린샷":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 정책을 잠그려면 먼저 [Get-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/get-azrmstoragecontainerimmutabilitypolicy) 명령을 호출하여 정책의 ETag를 검색합니다. 그런 다음, [Lock-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/lock-azrmstoragecontainerimmutabilitypolicy) 명령을 호출하고 ETag 값을 전달하여 정책을 잠급니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurepowershell
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container>

Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container> `
    -Etag $policy.Etag
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 정책을 잠그려면 먼저 [az storage container immutability-policy show](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_show) 명령을 호출하여 정책의 ETag를 검색합니다. 그런 다음, [az storage container immutability-policy lock](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_lock) 명령을 호출하고 ETag 값을 전달하여 정책을 잠급니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
$etag=$(az storage container immutability-policy show /
        --account-name <storage-account> /
        --container-name <container> /
        --query etag /
        --output tsv) 

az storage container immutability-policy lock /
    --resource-group <resource-group> /
    --account-name <storage-account> /
    --container-name <container> /
    --if-match $etag
```

---

## <a name="configure-or-clear-a-legal-hold"></a>법적 보존 구성 또는 지우기

법적 보존은 법적 보존을 명시적으로 취소할 때까지 변경이 불가능한 데이터를 저장합니다. 법적 보존 정책에 대한 자세한 내용은 [변경이 불가능한 Blob 데이터에 대한 법적 보존](immutable-legal-hold-overview.md)을 참조하세요.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 컨테이너의 법적 보존을 구성하려면 다음 단계를 수행합니다.

1. 원하는 컨테이너로 이동합니다.
1. **자세히** 단추를 선택하고 **액세스 정책** 을 선택합니다.
1. **변경이 불가능한 Blob 버전** 에서 **정책 추가** 를 선택합니다.
1. 정책 종류로 **법적 보존** 을 선택하고 **확인** 을 선택하여 법적 보존을 적용합니다.

다음 이미지는 시간 기반 보존 정책과 법적 보존이 모두 구성된 컨테이너를 보여줍니다.

:::image type="content" source="media/immutable-policy-configure-container-scope/retention-policy-legal-hold-container-scope.png" alt-text="시간 기반 보존 정책과 법적 보존이 모두 구성된 컨테이너를 보여주는 스크린샷":::

법적 보존을 취소하려면 **액세스 정책** 대화 상자로 이동하여 **자세히** 단추를 선택한 다음, **삭제** 를 선택합니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 컨테이너의 법적 보존을 구성하려면 [Add-AzRmStorageContainerLegalHold](/powershell/module/az.storage/add-azrmstoragecontainerlegalhold) 명령을 호출합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurepowershell
Add-AzRmStorageContainerLegalHold -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> `
    -Tag <tag1>,<tag2>,...
```

법적 보존을 취소하려면 [Remove-AzRmStorageContainerLegalHold](/powershell/module/az.storage/remove-azrmstoragecontainerlegalhold) 명령을 호출합니다.

```azurepowershell
Remove-AzRmStorageContainerLegalHold -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> ` 
    -Tag <tag1>,<tag2>,...
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell을 사용하여 컨테이너의 법적 보존을 구성하려면 [az storage container legal-hold set](/cli/azure/storage/container/legal-hold#az_storage_container_legal_hold_set) 명령을 호출합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage container legal-hold set /
    --tags tag1 tag2 /
    --container-name <container> /
    --account-name <storage-account> /
    --resource-group <resource-group>
```

법적 보존을 취소하려면 [az storage container legal-hold clear](/cli/azure/storage/container/legal-hold#az_storage_container_legal_hold_clear) 명령을 호출합니다.

```azurecli
az storage container legal-hold clear /
    --tags tag1 tag2 /
    --container-name <container> /
    --account-name <storage-account> /
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>다음 단계

- [비즈니스에 중요한 BLOB 데이터를 변경이 불가능한 스토리지에 저장](immutable-storage-overview.md)
- [변경이 불가능한 Blob 데이터에 대한 시간 기반 보존 정책](immutable-time-based-retention-policy-overview.md)
- [변경이 불가능한 Blob 데이터에 대한 법적 보존](immutable-legal-hold-overview.md)
- [Blob 버전에 대한 불변성 정책 구성(미리 보기)](immutable-policy-configure-version-scope.md)
