---
title: Blob에 대한 일시 삭제
titleSuffix: Azure Storage
description: Blob에 대한 일시 삭제는 애플리케이션 또는 다른 스토리지 계정 사용자에 의해 잘못 수정되거나 삭제될 때 데이터를 보다 쉽게 복구할 수 있도록 데이터를 보호합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/23/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 1c645e50a49a268d762b1195e91484783a73367c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128562553"
---
# <a name="soft-delete-for-blobs"></a>Blob에 대한 일시 삭제

Blob 일시 삭제는 지정된 기간 동안 시스템에 삭제된 데이터를 유지하여 실수로 인한 삭제 또는 덮어쓰기로부터 개별 Blob, 스냅샷 또는 버전을 보호합니다. 보존 기간 중 일시 삭제된 개체를 삭제된 시점의 상태로 복원할 수 있습니다. 보존 기간이 만료되면 개체가 영구적으로 삭제됩니다.

> [!IMPORTANT]
> 계층 구조 네임스페이스 기능을 사용하도록 설정한 계정의 일시 삭제는 현재 미리 보기 상태로 지원되며 모든 Azure 지역에서 전역적으로 사용할 수 있습니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>
>
> 미리 보기에 등록하려면 [이 양식](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pUOVRVOUpDRUtHVUtDUUtMVTZUR0tUMjZWNy4u)을 참조하세요.

## <a name="recommended-data-protection-configuration"></a>권장되는 데이터 보호 구성

Blob 일시 삭제는 Blob 데이터에 대한 포괄적인 데이터 보호 전략의 일환입니다. Blob 데이터에 대한 최적의 보호를 위해 다음과 같은 모든 데이터 보호 기능을 사용하는 것이 좋습니다.

- 컨테이너 일시 삭제는 삭제된 컨테이너를 복원합니다. 컨테이너 일시 삭제를 사용하도록 설정하는 방법을 알아보려면 [컨테이너에 대한 일시 삭제 사용 및 관리](soft-delete-container-enable.md)를 참조하세요.
- Blob 버전 관리는 Blob의 이전 버전을 자동으로 유지합니다. Blob 버전 관리를 사용하는 경우 데이터를 잘못 수정하거나 삭제한 경우 이전 버전의 Blob을 복원하여 데이터를 복구할 수 있습니다. Blob 버전 관리를 사용하도록 설정하는 방법을 알아보려면 [Blob 버전 관리 사용 및 관리](versioning-enable.md)를 참조하세요.
- Blob 일시 삭제 - 삭제된 Blob, 스냅샷 또는 버전을 복원합니다. Blob 일시 삭제를 사용하도록 설정하는 방법을 알아보려면 [Blob에 대한 일시 삭제 사용 및 관리](soft-delete-blob-enable.md)를 참조하세요.

Microsoft의 데이터 보호 권장 사항에 대해 자세히 알아보려면 [데이터 보호 개요](data-protection-overview.md)를 참조하세요.

## <a name="how-blob-soft-delete-works"></a>Blob 일시 삭제 작동 방식

스토리지 계정에 대해 Blob 일시 삭제를 사용하도록 설정할 때 삭제된 개체의 보존 기간(1~365일)을 지정합니다. 보존 기간은 데이터를 삭제하거나 덮어쓴 후 데이터를 사용할 수 있는 기간을 나타냅니다. 객체를 삭제하거나 덮어쓰는 즉시 보존 기간 시계가 시작됩니다.

보존 기간이 활성 상태이지만 [Blob 삭제 취소](/rest/api/storageservices/undelete-blob) 작업을 호출하여 삭제된 Blob을 해당 스냅샷과 함께 복원하거나 삭제된 버전을 복원할 수 있습니다. 다음 다이어그램에서는 Blob 일시 삭제를 사용하는 경우 삭제된 개체를 복원하는 방법을 보여줍니다.

:::image type="content" source="media/soft-delete-blob-overview/blob-soft-delete-diagram.png" alt-text="일시 삭제된 Blob을 복원하는 방법을 보여주는 다이어그램":::

언제든지 일시 삭제 보존 기간을 변경할 수 있습니다. 업데이트된 보존 기간은 보존 기간이 변경된 후 삭제된 데이터에만 적용됩니다. 보존 기간이 변경되기 전에 삭제된 데이터에는 삭제 당시 유효했던 보존 기간이 적용됩니다.

일시 삭제된 개체를 삭제하려고 해도 만료 시간에는 영향이 없습니다.

Blob 일시 삭제를 사용하지 않도록 설정하면 일시 삭제 보존 기간이 경과할 때까지 스토리지 계정에서 일시 삭제된 개체에 계속 액세스하고 복구할 수 있습니다.

Blob 버전 관리는 범용 v2, 블록 Blob, Blob 스토리지 계정에서 사용할 수 있습니다. 계층 구조 네임스페이스가 있는 Storage 계정은 현재 지원되지 않습니다.

버전 2017-07-29 이상의 Azure Storage REST API는 Blob 일시 삭제를 지원합니다.

> [!IMPORTANT]
> Blob 일시 삭제는 개별 Blob, 스냅샷, 디렉터리(계층 구조 네임스페이스 내) 또는 버전을 복원하는 데만 사용할 수 있습니다. 컨테이너와 해당 내용을 복원하려면 스토리지 계정에 대해 컨테이너 일시 삭제도 사용하도록 설정해야 합니다. Blob 데이터의 완전한 보호를 위해 Blob 일시 삭제와 함께 컨테이너 일시 삭제와 Blob 버전 관리를 사용하는 것이 좋습니다. 자세한 내용은 [데이터 보호 개요](data-protection-overview.md)를 참조하세요.
>
> Blob 일시 삭제는 스토리지 계정 삭제를 방지하지 않습니다. 스토리지 계정이 삭제되지 않도록 보호하려면 스토리지 계정 리소스에 대한 잠금을 구성합니다. 스토리지 계정 잠금에 대한 자세한 내용은 [스토리지 계정에 Azure Resource Manager 잠금 적용](../common/lock-account-resource.md)을 참조하세요.

### <a name="how-deletions-are-handled-when-soft-delete-is-enabled"></a>일시 삭제 사용 시 삭제가 처리되는 방법

Blob 일시 삭제를 사용하는 경우 Blob을 삭제하면 해당 Blob이 일시 삭제된 것으로 표시됩니다. 스냅샷이 만들어지지 않습니다. 보존 기간이 만료되면 일시 삭제된 Blob이 영구적으로 삭제됩니다.

Blob에 스냅샷이 있는 경우 스냅샷도 삭제해야 Blob을 삭제할 수 있습니다. Blob과 해당 스냅샷을 삭제하면 Blob과 스냅샷이 모두 일시 삭제된 것으로 표시됩니다. 새 스냅샷은 만들어지지 않습니다.

또한 기본 Blob을 삭제하지 않고 활성 스냅샷을 하나 이상 삭제할 수 있습니다. 이 경우 스냅샷은 일시 삭제됩니다.

계층 구조 네임스페이스 기능이 활성화된 계정에서 디렉터리를 삭제하면 디렉터리와 해당 내용이 모두 일시 삭제된 것으로 표시됩니다.

일시 삭제된 개체는 명시적으로 표시되거나 나열되지 않은 경우 보이지 않습니다. 일시 삭제된 개체를 나열하는 방법에 대한 자세한 내용은 [일시 삭제된 Blob 관리 및 복원](soft-delete-blob-manage.md)을 참조하세요.

### <a name="how-overwrites-are-handled-when-soft-delete-is-enabled"></a>일시 삭제 사용 시 덮어쓰기가 처리되는 방법

> [!IMPORTANT]
> 이 섹션은 계층 구조 네임스페이스가 있는 계정에는 적용되지 않습니다.

[Blob 배치](/rest/api/storageservices/put-blob), [블록 목록 배치](/rest/api/storageservices/put-block-list) 또는 [Blob 복사](/rest/api/storageservices/copy-blob)와 같은 작업을 호출하면 Blob의 데이터를 덮어씁니다. Blob 일시 삭제를 사용하는 경우 Blob을 덮어쓰면 쓰기 작업 전에 Blob 상태의 일시 삭제된 스냅샷이 자동으로 만들어집니다. 보존 기간이 만료되면 일시 삭제된 스냅샷이 영구적으로 삭제됩니다.

일시 삭제된 개체가 명시적으로 표시되거나 나열되지 않은 경우 일시 삭제된 스냅샷은 보이지 않습니다. 일시 삭제된 개체를 나열하는 방법에 대한 자세한 내용은 [일시 삭제된 Blob 관리 및 복원](soft-delete-blob-manage.md)을 참조하세요.

복사 작업을 보호하려면 대상 스토리지 계정에 대해 Blob 일시 삭제를 사용하도록 설정해야 합니다.

Blob 일시 삭제는 Blob 메타데이터 또는 속성을 쓰는 작업을 방지하지 않습니다. Blob의 메타데이터 또는 속성을 업데이트할 때 일시 삭제된 스냅샷은 만들어지지 않습니다.

Blob 일시 삭제는 보관 계층의 Blob에 대한 덮어쓰기 방지를 제공하지 않습니다. 임의 계층의 새 Blob이 보관 계층의 Blob을 덮어쓰면 덮어쓴 Blob이 영구적으로 삭제됩니다.

Premium Storage 계정의 경우 일시 삭제된 스냅샷은 스냅샷 100개의 Blob당 제한에 포함되지 않습니다.

### <a name="restoring-soft-deleted-objects"></a>일시 삭제된 개체 복원

보존 기간 내에 [Blob 삭제 취소](/rest/api/storageservices/undelete-blob) 작업을 호출하여 일시 삭제된 Blob 또는 디렉터리(계층 구조 네임스페이스 내)를 복원할 수 있습니다. **Blob 삭제 취소** 작업은 Blob 및 이와 연결된 일시 삭제된 스냅샷을 복원합니다. 보존 기간 중 삭제된 스냅샷은 모두 복원됩니다.

계층 구조 네임스페이스가 있는 계정에서 **Blob 삭제 취소** 작업을 사용하여 일시 삭제된 디렉터리와 모든 내용을 복원할 수도 있습니다. 일시 삭제된 Blob이 포함된 디렉터리 이름을 바꾸면 일시 삭제된 Blob의 연결이 디렉터리에서 끊어집니다. 이러한 Blob을 복원하려면 디렉터리 이름을 원래 이름으로 되돌리거나 원래 디렉터리 이름을 사용하는 별도의 디렉터리를 만들어야 합니다. 그렇지 않으면 일시 삭제된 Blob을 복원하려고 할 때 오류가 발생합니다.

일시 삭제되지 않은 Blob에 대해 **Blob 삭제 취소** 를 호출하면 Blob와 연결된 일시 삭제된 스냅샷이 복원됩니다. Blob이 스냅샷을 포함하지 않고 일시 삭제되지 않은 경우에는 **Blob 삭제 취소** 를 호출해도 효과가 없습니다.

일시 삭제된 스냅샷을 기본 Blob으로 승격하려면 먼저 기본 Blob에 대해 **Blob 삭제 취소** 를 호출하여 Blob과 해당 스냅샷을 복원합니다. 그런 다음, 원하는 스냅샷을 기본 Blob에 복사합니다. 새 Blob에 스냅샷을 복사할 수도 있습니다.

일시 삭제된 Blob 또는 스냅샷의 데이터는 개체가 복원될 때까지 읽을 수 없습니다.

일시 삭제된 개체를 복원하는 방법에 대한 자세한 내용은 [일시 삭제된 Blob 관리 및 복원](soft-delete-blob-manage.md)을 참조하세요.

## <a name="blob-soft-delete-and-versioning"></a>Blob 일시 삭제 및 버전 관리

> [!IMPORTANT]
> 버전 관리는 계층 구조 네임스페이스가 있는 계정에서 지원되지 않습니다.

스토리지 계정에 Blob 버전 관리와 Blob 일시 삭제를 모두 사용하는 경우 Blob을 덮어쓰면 자동으로 새 버전이 만들어집니다. 새 버전은 일시 삭제되지 않으며 일시 삭제 보존 기간이 만료되어도 제거되지 않습니다. 일시 삭제된 스냅샷이 만들어지지 않습니다. Blob을 삭제하면 현재 Blob 버전이 이전 버전이 되므로 현재 버전이 더 이상 존재하지 않게 됩니다. 새 버전이 만들어지지 않으며 일시 삭제된 스냅샷이 만들어지지 않습니다.

일시 삭제와 버전 관리를 함께 사용하면 Blob 버전이 삭제되지 않도록 보호됩니다. 일시 삭제를 사용하는 경우 버전을 삭제하면 일시 삭제된 버전이 만들어집니다. **Blob 삭제 취소** 작업을 사용하여 일시 삭제 보존 기간 동안 일시 삭제된 버전을 복원할 수 있습니다. **Blob 삭제 취소** 작업은 Blob의 일시 삭제된 모든 버전을 항상 복원합니다. 일시 삭제된 단일 버전만 복원하는 것은 불가능합니다.

일시 삭제 보존 기간이 경과한 후에는 일시 삭제된 Blob 버전이 영구적으로 삭제됩니다.

> [!NOTE]
> 버전 관리가 사용하도록 설정된 경우 삭제된 Blob에서 **Blob 삭제 취소** 작업을 호출하면 일시 삭제된 버전이나 스냅샷은 복원되지만 현재 버전은 복원되지 않습니다. 현재 버전을 복원하려면 이전 버전을 현재 버전에 복사하여 승격합니다.

최적의 데이터 보호를 위해 스토리지 계정에 버전 관리와 Blob 일시 삭제를 모두 사용하도록 설정하는 것이 좋습니다. Blob 버전 관리와 일시 삭제를 함께 사용하는 방법에 대한 자세한 내용은 [Blob 버전 관리 및 일시 삭제](versioning-overview.md#blob-versioning-and-soft-delete)를 참조하세요.

## <a name="blob-soft-delete-protection-by-operation"></a>작업별로 Blob 일시 삭제 방지

다음 표에서는 Blob 버전 관리를 사용하거나 사용하지 않고 Blob 일시 삭제를 사용하는 경우의 삭제 및 쓰기 작업에 대한 예상 동작을 설명합니다.

### <a name="storage-account-no-hierarchical-namespace"></a>스토리지 계정(계층 구조 네임스페이스 없음)

| REST API 작업 | 일시 삭제 사용 | 일시 삭제 및 버전 관리 사용 |
|--|--|--|
| [스토리지 계정 삭제](/rest/api/storagerp/storageaccounts/delete) | 변경되지 않았습니다. 삭제된 계정의 컨테이너 및 Blob은 복구할 수 없습니다. | 변경되지 않았습니다. 삭제된 계정의 컨테이너 및 Blob은 복구할 수 없습니다. |
| [컨테이너 삭제](/rest/api/storageservices/delete-container) | 변경되지 않았습니다. 삭제된 컨테이너의 Blob은 복구할 수 없습니다. | 변경되지 않았습니다. 삭제된 컨테이너의 Blob은 복구할 수 없습니다. |
| [Blob 삭제](/rest/api/storageservices/delete-blob) | Blob을 삭제하는 데 사용되는 경우 해당 스냅숏은 일시 삭제됨으로 표시됩니다. <br /><br /> Blob 스냅샷을 삭제하는 데 사용되는 경우 해당 스냅샷은 일시 삭제된 것으로 표시됩니다. | Blob을 삭제하는 데 사용되는 경우 현재 버전이 이전 버전이 되고 현재 버전이 삭제됩니다. 새 버전이 만들어지지 않으며 일시 삭제된 스냅샷이 만들어지지 않습니다.<br /><br /> Blob 버전을 삭제하는 데 사용되는 경우 해당 버전은 일시 삭제된 것으로 표시됩니다. |
| [Blob 삭제 취소](/rest/api/storageservices/undelete-blob) | 보존 기간 내에 삭제된 Blob과 모든 스냅샷을 복원합니다. | 보존 기간 내에 삭제된 Blob과 모든 버전을 복원합니다. |
| [Blob 배치](/rest/api/storageservices/put-blob)<br />[블록 목록 배치](/rest/api/storageservices/put-block-list)<br />[Blob 복사](/rest/api/storageservices/copy-blob)<br />[URL에서 Blob 복사](/rest/api/storageservices/copy-blob) | 활성 Blob에서 호출되는 경우 작업 전에 Blob의 상태 스냅샷이 자동으로 생성됩니다. <br /><br /> 일시 삭제된 Blob에서 호출되는 경우 Blob의 이전 상태 스냅샷은 같은 형식의 Blob으로 바뀌는 경우에만 생성됩니다. Blob이 다른 형식인 경우 기존의 모든 일시 삭제된 데이터는 영구적으로 삭제됩니다. | 작업 전에 Blob의 상태를 캡처하는 새 버전이 자동으로 생성됩니다. |
| [블록 배치](/rest/api/storageservices/put-block) | 활성 Blob에 블록을 커밋하는 데 사용되는 경우 변경 사항이 없습니다.<br /><br />일시 삭제된 Blob에 블록을 커밋하는 데 사용되는 경우 새 Blob이 만들어지고 일시 삭제된 Blob의 상태를 캡처하도록 스냅샷이 자동으로 만들어집니다. | 변경되지 않았습니다. |
| [페이지 배치](/rest/api/storageservices/put-page)<br />[URL에서 페이지 배치](/rest/api/storageservices/put-page-from-url) | 변경되지 않았습니다. 이 작업을 사용하여 덮어쓰거나 삭제하는 페이지 Blob 데이터는 저장되지 않으며 복구할 수 없습니다. | 변경되지 않았습니다. 이 작업을 사용하여 덮어쓰거나 삭제하는 페이지 Blob 데이터는 저장되지 않으며 복구할 수 없습니다. |
| [추가 블록](/rest/api/storageservices/append-block)<br />[URL에서 블록 추가](/rest/api/storageservices/append-block-from-url) | 변경되지 않았습니다. | 변경되지 않았습니다. |
| [Blob 속성 설정](/rest/api/storageservices/set-blob-properties) | 변경되지 않았습니다. 덮어쓰여진 Blob 속성은 복구할 수 없습니다. | 변경되지 않았습니다. 덮어쓰여진 Blob 속성은 복구할 수 없습니다. |
| [Blob 메타데이터 설정](/rest/api/storageservices/set-blob-metadata) | 변경되지 않았습니다. 덮어쓰여진 Blob 메타데이터는 복구할 수 없습니다. | 작업 전에 Blob의 상태를 캡처하는 새 버전이 자동으로 생성됩니다. |
| [Blob 계층 설정](/rest/api/storageservices/set-blob-tier) | 기본 Blob는 새 계층으로 이동됩니다. 활성 또는 일시 삭제된 스냅샷은 원래 계층에 남아 있습니다. 일시 삭제된 스냅샷은 만들어지지 않습니다. | 기본 Blob는 새 계층으로 이동됩니다. 활성 또는 일시 삭제된 스냅샷은 원래 계층에 남아 있습니다. 새 버전이 만들어지지 않습니다. |

### <a name="storage-account-hierarchical-namespace"></a>스토리지 계정(계층 구조 네임스페이스)

|**REST API 작업**|**일시 삭제 사용**|
|---|---|
|[경로 - 삭제](/rest/api/storageservices/datalakestoragegen2/path/delete) |일시 삭제된 blob 또는 디렉터리가 만들어집니다. 일시 삭제된 개체는 보존 기간 후 삭제됩니다.|
|[Blob 삭제](/rest/api/storageservices/delete-blob)|일시 삭제된 개체가 만들어집니다. 일시 삭제된 개체는 보존 기간 후 삭제됩니다. 스냅숏이 있는 blob에 대해서는 일시 삭제가 지원되지 않습니다.|
|blob 또는 디렉터리의 이름을 바꾸는 [경로 -생성](/rest/api/storageservices/datalakestoragegen2/path/create) | 기존 대상 blob 또는 빈 디렉터리가 일시 삭제되고 원본에서 이 파일을 바꿉니다. 일시 삭제된 개체는 보존 기간 후 삭제됩니다.|

## <a name="feature-support"></a>기능 지원

이 표에서는 사용자 계정에서 이 기능이 지원되는 방법과 특정 기능을 활성화할 때 지원에 미치는 영향을 보여 줍니다.

| Storage 계정 유형                | Blob Storage(기본 지원)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| 표준 범용 v2 | ![예](../media/icons/yes-icon.png) |![예 ](../media/icons/yes-icon.png) <sup>2</sup><sup>3</sup>                | ![No](../media/icons/no-icon.png) |
| Premium 블록 Blob          | ![예](../media/icons/yes-icon.png) |![예 ](../media/icons/yes-icon.png) <sup>2</sup><sup>3</sup>                | ![No](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2와 NFS(네트워크 파일 시스템) 3.0 프로토콜 모두에는 계층 구조 네임스페이스를 사용하는 스토리지 계정이 필요합니다.

<sup>2</sup>    기능은 미리 보기 수준에서 지원됩니다.

<sup>3</sup> 자세한 내용은 [Azure Data Lake Storage Gen2의 알려진 문제를 참조하세요.](data-lake-storage-known-issues.md) 이러한 문제는 계층 구조 네임스페이스 기능을 사용하도록 설정된 모든 계정에 적용됩니다.

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

일시 삭제된 모든 데이터는 활성 데이터와 동일한 요율로 요금이 청구됩니다. 보존 기간이 경과한 후 영구적으로 삭제되는 데이터에 대한 비용은 청구되지 않습니다.

일시 삭제를 사용하도록 설정할 때 기능이 요금에 영향을 주는 방법을 더 잘 이해할 수 있도록 짧은 보존 기간을 사용하는 것이 좋습니다. 최소 권장 보존 기간은 7일입니다.

자주 덮어쓰는 데이터에 일시 삭제를 사용하도록 설정하면 Blob을 나열할 때 스토리지 용량 요금과 대기 시간이 증가할 수 있습니다. 일시 삭제를 사용하지 않도록 설정된 별도의 스토리지 계정에 자주 덮어쓰는 데이터를 저장하여 이러한 추가 비용과 대기 시간을 줄일 수 있습니다.

Blob을 덮어쓰거나 삭제하는 경우 스냅샷 또는 버전의 자동 생성과 관련된 트랜잭션에 대해서는 요금이 청구되지 않습니다. 쓰기 작업에 대한 트랜잭션 속도의 **Blob 삭제 취소** 작업 호출에 대한 요금이 청구됩니다.

Blob Storage의 가격 책정에 대한 자세한 내용은 [Blob Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/) 페이지를 참조하세요.

## <a name="blob-soft-delete-and-virtual-machine-disks"></a>Blob 일시 삭제 및 가상 머신 디스크

Blob 일시 삭제는 내부적으로 페이지 Blob인 프리미엄 및 표준 비관리 디스크 모두에 사용할 수 있습니다. 일시 삭제는 [Blob 삭제](/rest/api/storageservices/delete-blob), [Blob 배치](/rest/api/storageservices/put-blob), [블록 목록 배치](/rest/api/storageservices/put-block-list) 및 [Blob 복사](/rest/api/storageservices/copy-blob) 작업에서 삭제하거나 덮어쓴 데이터를 복구하는 데 도움이 될 수 있습니다.

[페이지 배치](/rest/api/storageservices/put-page) 호출 시 덮어쓰는 데이터는 복구할 수 없습니다. Azure 가상 머신은 [페이지 배치](/rest/api/storageservices/put-page) 호출을 사용하여 비관리 디스크에 쓰므로 일시 삭제를 사용하여 Azure VM에서 비관리 디스크에 쓰기를 실행 취소하는 것은 지원되는 시나리오가 아닙니다.

## <a name="next-steps"></a>다음 단계

- [Blob에 일시 삭제를 사용하도록 설정](./soft-delete-blob-enable.md)
- [일시 삭제된 Blob 관리 및 복원](soft-delete-blob-manage.md)
- [Blob 버전 관리](versioning-overview.md)
