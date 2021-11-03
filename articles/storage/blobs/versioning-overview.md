---
title: Blob 버전 관리
titleSuffix: Azure Storage
description: Blob Storage 버전 관리는 개체의 이전 버전을 자동으로 유지 관리하고 타임스탬프를 사용하여 식별합니다. 데이터가 잘못 수정되거나 삭제된 경우 이전 버전의 Blob을 복원하여 데이터를 복구할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/10/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6807423e59c5877b67a8faaf3aebe4fd60e671fd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424934"
---
# <a name="blob-versioning"></a>Blob 버전 관리

Blob 스토리지 버전 관리를 사용하도록 설정하여 이전 버전의 개체를 자동으로 유지 관리할 수 있습니다. Blob 버전 관리를 사용하는 경우 데이터를 잘못 수정하거나 삭제한 경우 이전 버전의 Blob을 복원하여 데이터를 복구할 수 있습니다.

## <a name="recommended-data-protection-configuration"></a>권장되는 데이터 보호 구성

Blob 버전 관리는 Blob 데이터에 대한 포괄적인 데이터 보호 전략의 일환입니다. Blob 데이터에 대한 최적의 보호를 위해 다음과 같은 모든 데이터 보호 기능을 사용하는 것이 좋습니다.

- Blob 버전 관리는 Blob의 이전 버전을 자동으로 유지합니다. Blob 버전 관리를 사용하는 경우 데이터를 잘못 수정하거나 삭제한 경우 이전 버전의 Blob을 복원하여 데이터를 복구할 수 있습니다. Blob 버전 관리를 사용하도록 설정하는 방법을 알아보려면 [Blob 버전 관리 사용 및 관리](versioning-enable.md)를 참조하세요.
- 컨테이너 일시 삭제는 삭제된 컨테이너를 복원합니다. 컨테이너 일시 삭제를 사용하도록 설정하는 방법을 알아보려면 [컨테이너에 대한 일시 삭제 사용 및 관리](soft-delete-container-enable.md)를 참조하세요.
- Blob 일시 삭제 - 삭제된 Blob, 스냅샷 또는 버전을 복원합니다. Blob 일시 삭제를 사용하도록 설정하는 방법을 알아보려면 [Blob에 대한 일시 삭제 사용 및 관리](soft-delete-blob-enable.md)를 참조하세요.

Microsoft의 데이터 보호 권장 사항에 대해 자세히 알아보려면 [데이터 보호 개요](data-protection-overview.md)를 참조하세요.

## <a name="how-blob-versioning-works"></a>Blob 버전 관리의 작동 방식

버전은 지정된 시점에 Blob의 상태를 캡처합니다. 각 버전은 버전 ID로 식별됩니다. 스토리지 계정에 대해 Blob 버전 관리를 사용하도록 설정하면 Blob이 처음 생성될 때 그리고 이후에 Blob이 수정될 때마다 Azure Storage에서 고유 ID로 새 버전이 자동으로 만들어집니다.

버전 ID는 현재 버전 또는 이전 버전을 식별할 수 있습니다. Blob은 현재 버전을 한 번에 하나만 가질 수 있습니다.

새 Blob을 만들면 단일 버전이 존재하고 이 버전이 현재 버전입니다. 기존 Blob을 수정하면 현재 버전은 이전 버전이 됩니다. 업데이트된 상태를 캡처하기 위해 새 버전이 만들어지고 이 새 버전이 현재 버전이 됩니다. Blob을 삭제하면 Blob의 현재 버전은 이전 버전이 되고 현재 버전은 더 이상 존재하지 않습니다. Blob의 모든 이전 버전은 지속됩니다.

다음 다이어그램은 쓰기 작업에서 버전이 생성되는 과정과 이전 버전이 현재 버전으로 승격되는 방식을 보여줍니다.

:::image type="content" source="media/versioning-overview/blob-versioning-diagram.png" alt-text="Blob 버전 관리의 작동 방식을 보여 주는 다이어그램":::

Blob 버전은 변경할 수 없습니다. 기존 Blob 버전의 콘텐츠나 메타데이터는 수정할 수 없습니다.

Blob의 버전 수가 많으면 Blob 목록 작업 대기 시간이 길어질 수 있습니다. Microsoft는 Blob 하나 당 1000개 미만의 버전을 유지 관리하기를 권합니다. 수명 주기 관리를 사용하여 오래된 버전을 자동으로 삭제할 수 있습니다. 수명 주기 관리에 대한 자세한 내용은 [Azure Blob Storage 액세스 계층을 자동화하여 비용 최적화](./lifecycle-management-overview.md)를 참조하세요.

Blob 버전 관리는 표준 범용 v2, 프리미엄 블록 Blob 및 레거시 Blob Storage 계정에 사용할 수 있습니다. Azure Data Lake Storage Gen2에서 사용하도록 설정된 계층 구조 네임스페이스를 사용하는 스토리지 계정은 현재 지원되지 않습니다.

2019-10-10 버전 및 그 이상의 Azure Storage REST API는 Blob 버전 관리를 지원합니다.

> [!IMPORTANT]
> Blob 버전 관리로 스토리지 계정이나 컨테이너를 실수로 삭제하는 경우에는 복구할 수 없습니다. 스토리지 계정이 실수로 삭제되지 않도록 하려면 스토리지 계정 리소스 잠금을 설정하세요. 스토리지 계정 잠금에 대한 자세한 내용은 [스토리지 계정에 Azure Resource Manager 잠금 적용](../common/lock-account-resource.md)을 참조하세요.

### <a name="version-id"></a>버전 ID

각 Blob 버전은 고유한 버전 ID로 식별됩니다. 버전 ID 값은 Blob이 업데이트된 시간의 타임스탬프입니다. 버전 ID는 버전이 생성될 때 할당됩니다.

버전 ID를 제공하여 특정 버전의 Blob을 읽거나 삭제할 수 있습니다. 버전 ID를 생략하면 작업이 현재 버전에 대해 작동합니다.

Blob을 만들거나 수정하기 위해 쓰기 작업을 호출하는 경우 Azure Storage가 응답해서 *x-ms-version-id* 헤더를 반환합니다. 이 헤더에는 쓰기 작업으로 만들어진 Blob의 현재 버전에 대한 버전 ID가 포함됩니다.

버전 ID는 버전의 수명이 지속되는 동안 동일하게 유지됩니다.

### <a name="versioning-on-write-operations"></a>쓰기 작업의 버전 관리

Blob 버전 관리를 켜면 Blob에 쓰기 작업을 수행할 때마다 새 버전을 만듭니다. 쓰기 작업에는 [Blob 배치](/rest/api/storageservices/put-blob), [블록 목록 배치](/rest/api/storageservices/put-block-list), [Blob 복사](/rest/api/storageservices/copy-blob)및 [Blob 메타데이터 설정](/rest/api/storageservices/set-blob-metadata)이 포함됩니다.

쓰기 작업에서 새 Blob을 만드는 경우 결과물 Blob이 Blob의 현재 버전이 됩니다. 쓰기 작업이 기존 Blob을 수정하면 현재 버전은 이전 버전이 되고 업데이트된 Blob을 캡처하기 위해 새로운 현재 버전이 만들어집니다.

다음 다이어그램에서는 쓰기 작업이 Blob 버전에 미치는 영향을 보여 줍니다. 쉽게 설명하면, 이 문서의 다이어그램은 버전 ID를 간단한 정수 값으로 표시합니다. 실제 버전 ID는 타임스탬프입니다. 현재 버전은 파란색으로 표시되고 이전 버전은 회색으로 표시됩니다.

:::image type="content" source="media/versioning-overview/write-operations-blob-versions.png" alt-text="쓰기 작업이 버전이 지정된 Blob에 미치는 영향을 보여 주는 다이어그램":::

> [!NOTE]
> 스토리지 계정에서 버전 관리를 사용하도록 설정하기 전에 만든 Blob은 버전 ID가 없습니다. Blob이 수정되면 수정된 Blob이 현재 버전이 되고 업데이트 전의 Blob의 상태를 저장한 버전이 만들어집니다. 버전에는 생성 시간인 버전 ID가 할당됩니다.

스토리지 계정에 대해 Blob 버전 관리를 사용하도록 설정하면 블록 Blob에 대한 모든 쓰기 작업은 새 버전 만들기를 트리거합니다. 단, [블록 배치](/rest/api/storageservices/put-block) 작업을 제외됩니다.

페이지 Blob 및 추가 Blob의 경우 쓰기 작업의 하위 집합만 버전 만들기를 트리거합니다. 다음과 같은 작업이 여기에 포함됩니다.

- [Blob 배치](/rest/api/storageservices/put-blob)
- [블록 목록 배치](/rest/api/storageservices/put-block-list)
- [Blob 메타데이터 설정](/rest/api/storageservices/set-blob-metadata)
- [Blob 복사](/rest/api/storageservices/copy-blob)

다음 작업은 새 버전 만들기를 트리거하지 않습니다. 해당 작업의 변경 내용을 캡처하려면 수동 스냅샷을 만듭니다.

- [페이지 배치](/rest/api/storageservices/put-page)(페이지 Blob)
- [블록 추가](/rest/api/storageservices/append-block)(추가 Blob)

한 Blob의 모든 버전은 동일한 유형의 Blob이어야 합니다. Blob에 이전 버전이 있는 경우, Blob 및 해당 버전을 모두 삭제하지 않으면 한 유형의 Blob을 다른 유형으로 덮어쓸 수 없습니다.

### <a name="versioning-on-delete-operations"></a>삭제 작업의 버전 관리

버전 ID를 지정하지 않고 [Blob 삭제](/rest/api/storageservices/delete-blob) 작업을 호출하면 현재 버전은 이전 버전이 되고 현재 버전은 더 이상 존재하지 않습니다. Blob의 모든 기존 이전 버전은 유지됩니다.

다음 다이어그램은 삭제 작업이 버전이 지정된 Blob에 어떤 영향을 주는지 보여 줍니다.

:::image type="content" source="media/versioning-overview/delete-versioned-base-blob.png" alt-text="버전이 지정된 Blob의 삭제를 보여 주는 다이어그램":::

Blob의 특정 버전을 삭제하려면 삭제 작업에서 해당 버전의 ID를 제공합니다. 스토리지 계정에 대해 Blob 일시 삭제를 사용하도록 설정한 경우 일시 삭제 보존 기간이 경과할 때까지 버전이 시스템에서 유지됩니다.

Blob에 새 데이터를 쓰면 Blob의 현재 버전이 새로 만들어집니다. 다음 다이어그램에서 볼 수 있듯이, 모든 기존 버전은 영향을 받지 않습니다.

:::image type="content" source="media/versioning-overview/recreate-deleted-base-blob.png" alt-text="삭제 후 버전이 지정된 Blob의 다시 만들기를 보여 주는 다이어그램":::

### <a name="access-tiers"></a>액세스 계층

[Blob 계층 설정](/rest/api/storageservices/set-blob-tier) 작업을 호출하여 현재 버전을 비롯한 모든 블록 Blob 버전을 다른 Blob 액세스 계층으로 이동할 수 있습니다. 이전 버전의 Blob을 쿨 또는 보관 계층으로 이동시켜 더 낮은 용량의 요금을 활용할 수 있습니다. 자세한 내용은 [Blob 데이터에 대한 핫, 쿨 및 보관 액세스 계층을 참조하세요.](access-tiers-overview.md)

블록 Blob을 적절한 계층으로 이동하는 프로세스를 자동화하려면 Blob 수명 주기 관리를 사용합니다. 수명 주기 관리에 대한 자세한 내용은 [Azure Blob 스토리지 수명 주기 관리](./lifecycle-management-overview.md)를 참조하세요.

## <a name="enable-or-disable-blob-versioning"></a>Blob 버전 관리 사용 또는 사용하지 않음

Blob 버전 관리를 사용하거나 사용하지 않도록 설정하는 방법을 알아보려면 [Blob 버전 관리 사용 및 관리](versioning-enable.md)를 참조하세요.

Blob 버전 관리를 사용하지 않도록 설정해도 기존 Blob, 버전 또는 스냅샷은 삭제되지 않습니다. Blob 버전 관리를 해제하면 기존 버전은 스토리지 계정에서 계속 액세스할 수 있습니다. 이후에는 새 버전이 생성되지 않습니다.

스토리지 계정에서 버전 관리를 사용하지 않도록 설정한 후 Blob을 만들거나 수정한 경우 Blob을 덮어쓰면 새 버전이 만들어집니다. 업데이트된 Blob은 더 이상 현재 버전이 아니고 버전 ID가 없습니다. Blob에 대한 이후의 모든 업데이트는 이전 상태를 저장하지 않고 해당 데이터를 덮어씁니다.

버전 관리를 사용하지 않도록 설정한 후에도 버전 ID를 사용하여 버전을 읽거나 삭제할 수 있습니다. 버전 관리를 사용하지 않도록 설정한 후 Blob 버전을 나열할 수도 있습니다.

다음 다이어그램은 버전 관리를 사용하지 않도록 설정한 후 Blob을 수정하는 것이 버전이 지정되지 않은 Blob을 만드는 방법을 보여 줍니다. Blob과 연결된 모든 기존 버전은 유지됩니다.

:::image type="content" source="media/versioning-overview/modify-base-blob-versioning-disabled.png" alt-text="버전 관리를 사용하지 않도록 설정한 후 수정된 기본 Blob을 보여 주는 다이어그램":::

## <a name="blob-versioning-and-soft-delete"></a>Blob 버전 관리와 일시 삭제

최적의 데이터 보호를 위해 스토리지 계정에 버전 관리와 Blob 일시 삭제를 모두 사용하도록 설정하는 것이 좋습니다. Blob 일시 삭제에 대한 자세한 내용은 [Azure Storage Blob 일시 삭제](./soft-delete-blob-overview.md)를 참조하세요.

### <a name="overwriting-a-blob"></a>Blob 덮어쓰기

스토리지 계정에 Blob 버전 관리와 Blob 일시 삭제를 모두 사용하는 경우 Blob을 덮어쓰면 자동으로 새 버전이 만들어집니다. 새 버전은 일시 삭제되지 않으며 일시 삭제 보존 기간이 만료되어도 제거되지 않습니다. 일시 삭제된 스냅샷이 만들어지지 않습니다.

### <a name="deleting-a-blob-or-version"></a>Blob 또는 버전 삭제

스토리지 계정에 대해 버전 관리 및 일시 삭제를 모두 사용하도록 설정된 경우 Blob을 삭제하면 Blob의 현재 버전은 이전 버전이 됩니다. 새 버전이 만들어지지 않으며 일시 삭제된 스냅샷이 만들어지지 않습니다. 삭제된 Blob에에는 일시 삭제 보존 기간이 적용되지 않습니다.

일시 삭제는 Blob 버전 삭제를 위한 추가 보호 기능을 제공합니다. 이전 버전의 Blob을 삭제하면 해당 버전이 일시 삭제됩니다. 일시 삭제된 버전은 일시 삭제 보존 기간이 경과할 때까지 보존되며 이 기간이 지나면 영구적으로 삭제됩니다.

Blob의 이전 버전을 삭제 하려면 **Blob 삭제** 작업을 호출하고 버전 ID를 지정합니다.

다음 다이어그램은 Blob 또는 Blob 버전을 삭제할 때 발생하는 상황을 보여 줍니다.

:::image type="content" source="media/versioning-overview/soft-delete-historical-version.png" alt-text="일시 삭제를 사용하도록 설정한 버전의 삭제를 보여 주는 다이어그램":::

### <a name="restoring-a-soft-deleted-version"></a>일시 삭제된 버전 복원

[Blob 삭제 취소](/rest/api/storageservices/undelete-blob) 작업을 사용하여 일시 삭제 보존 기간 동안 일시 삭제된 버전을 복원할 수 있습니다. **Blob 삭제 취소** 작업은 Blob의 일시 삭제된 모든 버전을 항상 복원합니다. 일시 삭제된 단일 버전만 복원하는 것은 불가능합니다.

**Blob 삭제 취소** 작업을 사용하여 일시 삭제된 버전을 복원해도 어떤 버전도 현재 버전으로 승격되지 않습니다. 현재 버전을 복원하려면 먼저 일시 삭제된 모든 버전을 복원한 다음, [Blob 복사](/rest/api/storageservices/copy-blob) 작업을 사용하여 이전 버전을 새 현재 버전으로 복사합니다.

다음 다이어그램에서는 **Blob 삭제 취소** 작업을 사용하여 일시 삭제된 Blob 버전을 복원하는 방법과 **Blob 복사** 작업을 사용하여 Blob의 현재 버전을 복원하는 방법을 보여 줍니다.

:::image type="content" source="media/versioning-overview/undelete-version.png" alt-text="일시 삭제된 버전을 복원하는 방법을 보여 주는 다이어그램":::

일시 삭제 보존 기간이 경과한 후에는 일시 삭제된 Blob 버전이 영구적으로 삭제됩니다.

## <a name="blob-versioning-and-blob-snapshots"></a>Blob 버전 관리와 Blob 스냅샷

Blob 스냅샷은 특정 시점에 생성된 Blob의 읽기 전용 복사본입니다. Blob 스냅샷과 Blob 버전은 유사하지만, 스냅샷은 사용자 또는 애플리케이션이 수동으로 만들지만 Blob 버전은 스토리지 계정에 사용하도록 설정된 경우 쓰기 또는 삭제 작업을 실행했을 때 자동으로 생성됩니다.

> [!IMPORTANT]
> Microsoft는 Blob 버전 관리를 사용하도록 설정한 후에는 애플리케이션을 업데이트하여 블록 Blob의 스냅샷 만들기를 중지하기를 권장합니다. 스토리지 계정에 버전 관리를 사용하도록 설정하는 경우 모든 블록 Blob 업데이트 및 삭제가 버전에 의해 캡처되고 보존됩니다. Blob 버전 관리를 사용하도록 설정하는 경우 스냅샷을 만드는 것이 블록 Blob 데이터에 대한 추가 보호를 제공하지 않으며, 비용과 애플리케이션 복잡성을 높일 수 있습니다.

### <a name="snapshot-a-blob-when-versioning-is-enabled"></a>버전 관리를 사용하는 경우의 Blob 스냅샷

권장되지는 않지만 버전이 지정된 Blob의 스냅샷을 만들 수 있습니다. 버전 관리를 사용하지만 Blob의 스냅샷 만들기를 중지하도록 애플리케이션을 업데이트할 수 없는 경우, 애플리케이션에서 스냅샷과 버전을 모두 지원할 수 있습니다.

버전이 지정된 Blob의 스냅샷을 만들면 스냅샷이 만들어질 때 새 버전이 동시에 만들어집니다. 스냅샷이 만들어질 때 새로운 현재 버전도 만들어집니다.

다음 다이어그램에서는 버전이 지정된 Blob의 스냅샷을 만들 때 발생하는 상황을 보여 줍니다. 이 다이어그램에서 버전 ID가 2와 3인 Blob 버전과 스냅샷에는 동일한 데이터가 포함되어 있습니다.

:::image type="content" source="media/versioning-overview/snapshot-versioned-blob.png" alt-text="버전이 지정된 Blob의 스냅샷을 보여 주는 다이어그램":::

## <a name="authorize-operations-on-blob-versions"></a>Blob 버전에 대한 작업 권한 부여

다음 방법 중 하나를 사용하여 Blob 버전에 대한 액세스 권한을 부여할 수 있습니다.

- Azure RBAC(역할 기반 액세스 제어)를 사용하여 Azure AD(Azure Active Directory) 보안 주체에 사용 권한을 부여합니다. Microsoft에서는 강화된 보안과 사용 편의성을 위해 Azure AD의 사용을 권장합니다. Blob 작업에서 Azure AD를 사용하는 방법에 관한 자세한 내용은 [Azure Storage에서 데이터에 대한 액세스 권한 부여](../common/authorize-data-access.md)를 참조하세요.
- SAS(공유 액세스 서명)를 사용하여 Blob 버전에 대한 액세스를 위임할 수 있습니다. 특정 버전의 작업에 대한 SAS 토큰을 만들기 위해 Blob 버전을 나타내는 서명된 리소스 종류 `bv`의 버전 ID를 지정합니다. 공유 액세스 서명에 대한 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../common/storage-sas-overview.md)를 참조하세요.
- 계정 액세스 키를 사용하여 공유 키로 Blob 버전에 작업 권한을 부여합니다. 자세한 내용은 [공유 키를 사용하여 권한 부여](/rest/api/storageservices/authorize-with-shared-key)를 참조하세요.

Blob 버전 관리는 실수로 인한 삭제 또는 악의적인 삭제에서 데이터를 보호하도록 설계되었습니다. 보호를 강화하려면, Blob 버전의 삭제에 특별한 권한이 필요합니다. 다음 섹션에서는 Blob 버전을 삭제하는 데 필요한 권한에 대해 설명합니다.

### <a name="azure-rbac-action-to-delete-a-blob-version"></a>Blob 버전을 삭제하는 Azure RBAC 작업

다음 표에서는 Blob 또는 Blob 버전 삭제를 지원하는 Azure RBAC 작업을 보여 줍니다.

| Description | Blob 서비스 작업 | Azure RBAC 데이터 작업 필요 | Azure 기본 제공 역할 지원 |
|----------------------------------------------|------------------------|---------------------------------------------------------------------------------------|-------------------------------|
| 현재 버전 삭제 | Blob 삭제 | **Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete** | Storage Blob 데이터 기여자 |
| 이전 버전 삭제 | Blob 삭제 | **Microsoft.Storage/storageAccounts/BlobServices/containers/Blobs/deleteBlobVersion/action** | Storage Blob 데이터 소유자 |

### <a name="shared-access-signature-sas-parameters"></a>SAS(공유 액세스 서명) 매개 변수

Blob 버전에 대한 서명된 리소스는 `bv`입니다. 자세한 내용은 [서비스 SAS 만들기](/rest/api/storageservices/create-service-sas) 또는 [사용자 위임 SAS 만들기](/rest/api/storageservices/create-user-delegation-sas)를 참조하세요.

다음 표에서는 SAS에서 Blob 버전을 삭제하는 데 필요한 권한을 보여 줍니다.

| **사용 권한** | **URI 기호** | **허용되는 작업** |
|----------------|----------------|------------------------|
| DELETE         | x              | Blob 버전 삭제 |

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

Blob 버전 관리를 사용하도록 설정하면 계정에 추가 데이터 스토리지 요금이 부과될 수 있습니다. 애플리케이션을 디자인할 때는 비용을 최소화할 수 있도록 이러한 요금 발생 방식을 파악하는 것이 중요합니다.

Blob 버전은 Blob 스냅샷처럼 활성 데이터와 동일한 요금이 청구됩니다. 버전에 청구되는 금액은 기본 Blob의 계층을 명시적으로 설정했는지 아니면 해당 버전 혹은 스냅샷에 대해 명시적으로 설정했는지에 따라 달라집니다. Blob 계층에 대한 자세한 내용은 [Blob 데이터에 대한 핫, 쿨 및 보관 액세스 계층을 참조하세요.](access-tiers-overview.md)

Blob 또는 버전의 계층을 변경하지 않은 경우 해당 Blob과 그 버전, 그리고 만약 있다면 스냅샷까지 포함된 고유한 데이터 블록에 요금이 청구됩니다. 자세한 내용은 [Blob 계층이 명시적으로 설정되지 않은 경우의 요금 청구](#billing-when-the-blob-tier-has-not-been-explicitly-set)를 참조하세요.

Blob 또는 버전의 계층을 변경한 경우 Blob 및 버전이 최종적으로 동일한 계층에 있는지 여부에 관계 없이 전체 개체에 대해 요금이 청구됩니다. 자세한 내용은 [Blob 계층이 명시적으로 설정된 경우의 청구](#billing-when-the-blob-tier-has-been-explicitly-set)를 참조하세요.

> [!NOTE]
> 자주 덮어쓰는 데이터의 버전 관리를 사용하도록 설정하면 스토리지 용량 요금이 늘어나고 작업을 나열하는 동안 대기 시간이 늘어날 수 있습니다. 해당 문제를 완화하려면 버전 관리를 사용하지 않는 별도의 스토리지 계정에 자주 덮어쓴 데이터를 저장하세요.

Blob 스냅샷의 청구 정보에 대한 자세한 내용은 [Blob 스냅샷](snapshots-overview.md)을 참조하세요.

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Blob 계층이 명시적으로 설정되지 않은 경우의 요금 청구

기본 Blob 또는 해당 버전에 대한 Blob 계층을 명시적으로 설정하지 않은 경우에는 해당 Blob과 그 버전, 그리고 만약 있다면 스냅샷까지 포함된 고유한 블록이나 페이지에 요금이 청구됩니다. Blob 및 해당 버전에서 공유되는 데이터는 한 번만 청구됩니다. Blob이 업데이트되면 기본 Blob의 데이터는 해당 버전에 저장된 데이터와 달라지므로 고유한 데이터는 블록 또는 페이지별로 요금이 청구됩니다.

블록 Blob 내의 블록을 바꾸고 나면 해당 블록은 이후부터 고유 블록으로 요금이 청구됩니다. 블록의 ID와 데이터가 이전 버전의 ID 및 데이터와 동일해도 마찬가지입니다. 블록을 다시 커밋하면 이전 버전의 해당 항목과는 달라지므로 해당 데이터에 대해 요금이 부과됩니다. 같은 데이터로 업데이트하는 페이지 Blob 내 페이지의 경우에도 마찬가지입니다.

Blob 스토리지에는 두 블록에 동일한 데이터가 포함되어 있는지 여부를 확인할 방법이 없습니다. 업로드/커밋되는 각 블록은 데이터와 블록 ID가 같더라도 고유한 블록으로 처리됩니다. 고유한 블록에 대해 요금이 부과되기 때문에 버전 관리를 사용하는 경우 Blob을 업데이트하면 고유한 블록이 추가로 발생하고 추가 요금이 발생한다는 점을 유의해야 합니다.

Blob 버전 관리를 사용하는 경우 블록 Blob에 대한 업데이트 작업을 호출하여 가능한 최소한의 블록을 업데이트합니다. 블록에 대한 세분화된 제어를 허용하는 쓰기 작업은 [블록 배치](/rest/api/storageservices/put-block)와 [블록 목록 배치](/rest/api/storageservices/put-block-list)입니다. 반면 [Blob 배치](/rest/api/storageservices/put-blob) 작업은 Blob의 전체 콘텐츠를 대체하므로 추가 요금이 발생할 수 있습니다.

다음 시나리오에서는 Blob 계층이 명시적으로 설정되지 않은 경우 블록 Blob 및 해당 버전에 대한 요금을 계산하는 방법을 보여 줍니다.

#### <a name="scenario-1"></a>시나리오 1

시나리오 1에서는 Blob에 이전 버전이 있습니다. Blob은 버전이 만들어진 후 업데이트되지 않았기 때문에 고유한 블록 1, 2, 3에만 요금이 청구됩니다.

![기본 Blob과 이전 버전의 고유한 블록에 청구된 요금을 보여 주는 다이어그램 1](./media/versioning-overview/versions-billing-scenario-1.png)

#### <a name="scenario-2"></a>시나리오 2

시나리오 2에서는 Blob의 한 블록(다이어그램의 블록 3)이 업데이트되었습니다. 업데이트된 블록에 동일한 데이터와 동일한 ID가 포함된 경우에도 이전 버전의 블록 3과는 다릅니다. 따라서 4개 블록에 대한 요금이 계정에 청구됩니다.

![기본 Blob 및 이전 버전의 고유한 블록에 대한 청구를 보여 주는 다이어그램 2](./media/versioning-overview/versions-billing-scenario-2.png)

#### <a name="scenario-3"></a>시나리오 3

시나리오 3에서는 기본 Blob은 업데이트되었지만 버전은 업데이트되지 않았습니다. 기본 Blob에서 블록 3이 블록 4로 바뀌었지만 이전 버전은 계속 블록 3을 반영합니다. 따라서 4개 블록에 대한 요금이 계정에 청구됩니다.

![기본 Blob 및 이전 버전의 고유한 블록에 대한 청구를 보여 주는 다이어그램 3](./media/versioning-overview/versions-billing-scenario-3.png)

#### <a name="scenario-4"></a>시나리오 4

시나리오 4에서는 기본 Blob이 완전히 업데이트되었으며 원래 블록을 하나도 포함하지 않습니다. 따라서 기본 Blob에서 4개, 이전 버전에서 4개, 총 8개의 고유한 블록 &mdash; 요금이 계정에 청구됩니다. 이 시나리오는 [Blob 배치](/rest/api/storageservices/put-blob) 작업을 사용하여 Blob에 쓰기를 하는 경우에 발생할 수 있습니다. 이 경우 기본 Blob의 전체 콘텐츠가 대체되기 때문입니다.

![기본 Blob 및 이전 버전의 고유한 블록에 대한 청구를 보여 주는 다이어그램 4](./media/versioning-overview/versions-billing-scenario-4.png)

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Blob 계층이 명시적으로 설정된 경우 요금 청구

Blob 또는 버전(또는 스냅샷)에 대한 Blob 계층을 명시적으로 설정한 경우, 원본 계층의 개체와 블록을 공유하는지 여부에 관계없이 새 계층에서 개체의 전체 콘텐츠 길이에 대한 요금이 청구됩니다. 또한 원본 계층에서 가장 오래된 버전의 전체 콘텐츠 길이에 대한 요금이 청구됩니다. 원본 계층에 남아 있는 다른 모든 이전 버전이나 스냅샷은 [Blob 계층이 명시적으로 설정되지 않은 경우의 요금 청구](#billing-when-the-blob-tier-has-not-been-explicitly-set)에 설명된 대로 공유될 수 있는 고유한 블록에 대해 요금이 청구됩니다.

#### <a name="moving-a-blob-to-a-new-tier"></a>새 계층으로 Blob 이동

다음 표에서는 새 계층으로 이동할 때 Blob 또는 버전에 대한 요금 청구 동작을 설명합니다.

| Blob 계층이 명시적으로 설정된 경우에는 | 다음에 대한 요금이 청구됩니다. |
|-|-|
| 이전 버전의 기본 Blob | 새 계층의 기본 Blob 및 원본 계층의 가장 오래된 버전과 다른 버전의 모든 고유한 블록 <sup>1</sup> |
| 이전 버전 및 스냅샷이 있는 기본 Blob | 새 계층의 기본 Blob, 원본 계층의 가장 오래된 버전 및 원본 계층의 가장 오래된 스냅샷과 다른 버전 또는 스냅샷의 고유한 블록<sup>1</sup> |
| 이전 버전 | 새 계층의 버전과 원본 계층의 기본 Blob에는 다른 버전의 모든 고유한 블록이 추가됩니다. <sup>1</sup> |

<sup>1</sup> 원본 계층에서 이동되지 않은 다른 이전 버전 또는 스냅샷이 있는 경우 해당 버전 또는 스냅샷은 [Blob 계층이 명시적으로 설정되지 않은 경우의 청구](#billing-when-the-blob-tier-has-not-been-explicitly-set)에 설명된 대로 그 안에 포함된 고유한 블록 수에 따라 요금이 청구됩니다.

다음 다이어그램에서는 버전이 지정된 Blob을 다른 계층으로 이동할 때 개체의 요금을 청구하는 방법을 보여 줍니다.

:::image type="content" source="media/versioning-overview/versioning-billing-tiers.png" alt-text="버전이 지정된 Blob이 명시적으로 계층화된 경우 개체의 요금을 청구하는 방법을 보여 주는 다이어그램":::

Blob, 버전 또는 스냅샷에 대한 계층을 명시적으로 설정하는 작업은 취소할 수 없습니다. 새 계층으로 Blob을 이동한 다음 원래 계층으로 다시 이동하는 경우 원본 계층의 다른 개체와 블록을 공유하는 경우에도 개체의 전체 콘텐츠 길이에 대한 요금이 청구됩니다.

Blob, 버전 또는 스냅샷의 계층을 명시적으로 설정하는 작업은 다음과 같습니다.

- [Blob 계층 설정](/rest/api/storageservices/set-blob-tier)
- 계층을 지정하여 [Blob 배치](/rest/api/storageservices/put-blob)
- 지정된 계층을 사용하여 [블록 목록 배치](/rest/api/storageservices/put-block-list)
- 계층을 지정하여 [Blob 복사](/rest/api/storageservices/copy-blob)

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>일시 삭제를 사용하는 경우 Blob 삭제

Blob 일시 삭제를 사용하는 경우 계층을 명시적으로 설정한 기본 Blob을 삭제하거나 덮어쓰면 모든 이전 버전의 일시 삭제된 Blob은 전체 콘텐츠 길이로 청구됩니다. Blob 버전 관리와 일시 삭제를 함께 사용하는 방법에 대한 자세한 내용은 [Blob 버전 관리 및 일시 삭제](#blob-versioning-and-soft-delete)를 참조하세요.

다음 표에서는 버전 관리의 사용 여부에 따라 일시 삭제된 Blob에 대한 청구를 설명합니다. 버전 관리를 사용하지 않도록 설정한 경우 Blob이 일시 삭제되면 버전이 만들어집니다. 버전 관리를 사용하지 않도록 설정한 경우 Blob을 일시 삭제하면 일시 삭제 스냅샷이 만들어집니다.

| 계층을 명시적으로 설정하고 기본 Blob을 덮어 쓸 경우. | 다음에 대한 요금이 청구됩니다. |
|-|-|
| Blob 일시 삭제 및 버전 관리를 모두 사용하는 경우 | 계층에 관계 없이 모든 기존 버전의 전체 콘텐츠 길이 |
| Blob 일시 삭제를 사용하도록 설정했지만 버전 관리를 사용하지 않는 경우 | 계층에 관계 없이 모든 기존 일시 삭제 스냅샷이 전체 콘텐츠 길이에 포함됩니다. |

## <a name="feature-support"></a>기능 지원

이 표에서는 사용자 계정에서 이 기능이 지원되는 방법과 특정 기능을 활성화할 때 지원에 미치는 영향을 보여 줍니다.

| Storage 계정 유형                | Blob Storage(기본 지원)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| 표준 범용 v2 | ![예](../media/icons/yes-icon.png) |![아니요](../media/icons/no-icon.png)              | ![아니요](../media/icons/no-icon.png) |
| Premium 블록 Blob          | ![예](../media/icons/yes-icon.png) |![아니요](../media/icons/no-icon.png)              | ![아니요](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2와 NFS(네트워크 파일 시스템) 3.0 프로토콜 모두에는 계층 구조 네임스페이스를 사용하는 스토리지 계정이 필요합니다.

## <a name="see-also"></a>참조

- [Blob 버전 관리 설정 및 관리](versioning-enable.md)
- [Blob의 스냅샷 만들기](/rest/api/storageservices/creating-a-snapshot-of-a-blob)
- [Azure Storage Blob의 일시 삭제 기능](./soft-delete-blob-overview.md)
