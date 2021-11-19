---
title: Blob 스냅샷
titleSuffix: Azure Storage
description: Blob 스냅샷이 어떻게 작동하고 어떻게 청구되는지 이해합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/02/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 7ae07b48d63059e22f63fb3b201a1a5aab7d960e
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132810238"
---
# <a name="blob-snapshots"></a>Blob 스냅샷

스냅샷은 특정 시점에 생성된 Blob의 읽기 전용 버전입니다.

> [!NOTE]
> Blob 버전 관리는 Blob의 이전 버전을 유지하는 탁월한 방법을 제공합니다. 자세한 내용은 [Blob 버전 관리](versioning-overview.md)를 참조하세요.

## <a name="about-blob-snapshots"></a>Blob 스냅샷 정보

> [!IMPORTANT]
> 계층 구조 네임스페이스 기능이 사용하도록 설정된 계정의 스냅숏은 현재 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>
>
> 미리 보기에 등록하려면 [이 양식](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR9iuLyDgXDNIkMaAAVSMpJxUOTRZODFORzBQSkhIMEQ0UlhZUFZWNFdKRS4u)을 참조하세요.

Blob URI에 스냅샷이 만들어진 시점의 시간을 나타내는 Blob URI에 추가된 **DateTime** 값이 있다는 점을 제외하고 Blob의 스냅샷은 해당 Blob와 동일합니다. 예를 들어 페이지 Blob URI가 `http://storagesample.core.blob.windows.net/mydrives/myvhd`이면 스냅샷 URI는 `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`과 유사합니다.

> [!NOTE]
> 모든 스냅샷은 기본 Blob의 URI를 공유합니다. 기본 Blob와 스냅샷의 유일한 차이는 추가된 **DateTime** 값입니다.

Blob 하나에 여러 스냅샷이 있을 수 있습니다. 스냅샷은 독립적으로 또는 기본 Blob에 대한 [Blob 삭제](/rest/api/storageservices/delete-blob) 작업의 일부로 명시적으로 삭제될 때까지 유지됩니다. 기본 Blob와 연결된 스냅샷을 열거하여 현재 스냅샷을 추적할 수 있습니다.

Blob의 스냅샷을 만들면 blob의 시스템 속성이 같은 값으로 스냅샷에 복사됩니다. 만들 때 스냅샷에 대한 별도의 메타데이터를 지정하지 않으면 기본 Blob의 메타데이터가 스냅샷에 복사됩니다. 스냅샷을 만든 후에 읽기, 복사 또는 삭제할 수 있지만 수정할 수 없습니다.

기본 blob와 연결된 모든 임대는 스냅샷에 영향을 주지 않습니다. 스냅샷에 대해 임대를 가져올 수 없습니다.

VM 디스크에 대한 현재 정보 및 상태를 저장하는 데 VHD 파일을 사용합니다. VM 내에서 디스크를 분리하거나 VM을 종료하고, 해당 VHD 파일의 스냅샷을 만들 수 있습니다. 해당 스냅샷 파일을 나중에 사용하여 해당 시점에 VHD 파일을 검색하여 VM을 다시 만들 수 있습니다.

## <a name="understand-how-snapshots-accrue-charges"></a>스냅샷 요금 청구 방법 이해

### <a name="important-billing-considerations"></a>청구 관련 중요 고려 사항

아래 목록에는 스냅샷을 만들 때 고려할 주요 사항이 나와 있습니다.

- 스토리지 계정에서는 고유 블록이나 페이지가 Blob에 있는지 혹은 스냅샷에 있는지에 관계없이 요금이 발생합니다. 스냅샷의 기준이 되는 Blob을 업데이트할 때까지는 해당 Blob와 연결된 스냅샷에 대해 계정에 추가 요금이 부과되지 않습니다. 기본 Blob를 업데이트한 후에 해당 스냅샷과 달라집니다. 이 경우에 Blob 또는 스냅샷 각각에서 고유한 블록이나 페이지에 대한 요금이 청구됩니다.
- 블록 Blob 내의 블록을 바꾸고 나면 해당 블록은 이후부터 고유 블록으로 요금이 청구됩니다. 블록의 ID와 데이터가 스냅샷에서와 같더라도 마찬가지입니다. 블록을 다시 커밋하면 스냅샷의 해당 항목과 달라지므로 해당 데이터에 대해 요금이 부과됩니다. 같은 데이터로 업데이트하는 페이지 Blob 내 페이지의 경우에도 마찬가지입니다.
- Blob의 전체 콘텐츠를 덮어쓰는 메서드를 호출하여 블록 Blob을 업데이트하면 Blob의 모든 블록이 바뀝니다. 해당 Blob에 스냅샷이 연결되어 있으면 스냅샷과 기본 Blob의 모든 블록이 달라지므로 두 Blob의 모든 블록에 대해 요금이 부과됩니다. 이는 기본 Blob와 스냅샷의 데이터가 동일하게 유지되더라도 마찬가지입니다.
- Azure Blob service에서는 두 블록이 같은 데이터를 포함하는지를 확인할 수 없습니다. 업로드/커밋되는 각 블록은 데이터와 블록 ID가 같더라도 고유한 블록으로 처리됩니다. 고유한 블록에 대해서는 비용이 부과되므로 스냅샷이 포함된 Blob을 업데이트하면 고유한 블록이 추가로 생성되어 추가 비용이 발생함을 고려해야 합니다.

### <a name="minimize-costs-with-snapshot-management"></a>스냅샷 관리 비용을 최소화

추가 비용을 방지하기 위해서는 스냅샷을 신중하게 관리하는 것이 좋습니다. 스냅샷 스토리지으로 인해 발생하는 비용을 최소화하려면 다음 모범 사례를 따를 수 있습니다.

- 애플리케이션 디자인상 스냅샷을 유지해야 하는 경우가 아니면, Blob을 업데이트할 때마다 같은 데이터로 업데이트하더라도 해당 Blob에 연결된 스냅샷을 삭제한 후에 다시 만듭니다. Blob의 스냅샷을 삭제한 후에 다시 만들면 Blob와 스냅샷이 달라지지 않습니다.
- Blob에 대한 스냅샷을 유지하는 경우 Blob을 업데이트할 때 전체 Blob을 덮어쓰는 메서드를 호출하지 마세요. 대신, 비용을 낮게 유지하기 위해 가능한 적은 수의 블록을 업데이트합니다.

### <a name="snapshot-billing-scenarios"></a>스냅샷 청구 시나리오

다음 시나리오에서는 블록 Blob와 해당 스냅샷에 대해 비용이 청구되는 방법을 보여 줍니다.

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

Blob의 읽기 전용 복사본인 스냅샷을 만들면 계정에 데이터 스토리지 요금이 추가로 부과될 수 있습니다. 애플리케이션을 디자인할 때는 비용을 최소화할 수 있도록 이러한 요금 발생 방식을 파악하는 것이 중요합니다.

Blob 스냅샷은 Blob 버전처럼 활성 데이터와 동일한 요금이 청구됩니다. 스냅샷에 청구되는 요금은 기본 Blob이나 해당 스냅샷(또는 버전)에 대해 계층을 명시적으로 설정했는지 여부에 따라 달라집니다. Blob 계층에 대한 자세한 내용은 [Blob 데이터에 대한 핫, 쿨 및 보관 액세스 계층을 참조하세요.](access-tiers-overview.md)

Blob 또는 스냅샷의 계층을 변경하지 않은 경우 해당 Blob과 그 스냅샷, 그리고 만약 있다면 버전까지 포함된 고유 데이터 블록에 요금이 청구됩니다. 자세한 내용은 [Blob 계층이 명시적으로 설정되지 않은 경우의 요금 청구](#billing-when-the-blob-tier-has-not-been-explicitly-set)를 참조하세요.

Blob 또는 스냅샷의 계층을 변경한 경우 Blob과 스냅샷이 최종적으로 동일한 계층에 있는지 여부와 관계없이 전체 개체에 대해 요금이 청구됩니다. 자세한 내용은 [Blob 계층이 명시적으로 설정된 경우의 청구](#billing-when-the-blob-tier-has-been-explicitly-set)를 참조하세요.

Blob 버전의 청구 정보에 대한 자세한 내용은 [Blob 버전 관리](versioning-overview.md)를 참조하세요.

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Blob 계층이 명시적으로 설정되지 않은 경우의 요금 청구

기본 Blob 또는 해당 스냅샷에 대한 Blob 계층을 명시적으로 설정하지 않은 경우에는 해당 Blob과 그 스냅샷, 그리고 만약 있다면 버전까지 포함된 고유 블록이나 페이지에 요금이 청구됩니다. Blob 및 해당 스냅샷에서 공유되는 데이터는 한 번만 청구됩니다. Blob이 업데이트되면 기본 Blob의 데이터는 해당 스냅샷에 저장된 데이터와 달라지므로 고유 데이터는 블록 또는 페이지당 요금이 청구됩니다.

블록 Blob 내의 블록을 바꾸고 나면 해당 블록은 이후부터 고유 블록으로 요금이 청구됩니다. 블록의 ID와 데이터가 스냅샷에서와 같더라도 마찬가지입니다. 블록을 다시 커밋하면 스냅샷의 해당 항목과 달라지므로 해당 데이터에 대한 요금이 부과됩니다. 같은 데이터로 업데이트하는 페이지 Blob 내 페이지의 경우에도 마찬가지입니다.

Blob 스토리지에는 두 블록에 동일한 데이터가 포함되어 있는지 여부를 확인할 방법이 없습니다. 업로드/커밋되는 각 블록은 데이터와 블록 ID가 같더라도 고유한 블록으로 처리됩니다. 고유 블록에 대해 요금이 부과되기 때문에 Blob에 스냅샷 또는 버전이 있는 경우 Blob을 업데이트하면 고유 블록이 추가로 발생하여 추가 요금이 발생하므로 유의해야 합니다.

Blob에 스냅샷이 있는 경우 블록 Blob에 대한 업데이트 작업을 호출하여 가능한 최소한의 블록을 업데이트합니다. 블록에 대한 세분화된 제어를 허용하는 쓰기 작업은 [블록 배치](/rest/api/storageservices/put-block)와 [블록 목록 배치](/rest/api/storageservices/put-block-list)입니다. 반면 [Blob 배치](/rest/api/storageservices/put-blob) 작업은 Blob의 전체 콘텐츠를 대체하므로 추가 요금이 발생할 수 있습니다.

다음 시나리오에서는 Blob 계층이 명시적으로 설정되지 않은 경우 블록 Blob과 해당 스냅샷에 대해 어떻게 요금이 부과되는지 보여 줍니다.

#### <a name="scenario-1"></a>시나리오 1

시나리오 1에서는 스냅샷을 생성한 후 기본 Blob을 업데이트하지 않았으므로 고유 블록 1, 2, 3에 대해서만 비용이 청구됩니다.

![기본 Blob과 스냅샷의 고유 블록에 대한 요금 청구를 보여 주는 다이어그램 1](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>시나리오 2

시나리오 2에서는 기본 Blob은 업데이트되었지만 스냅샷은 업데이트되지 않았습니다. 블록 3이 업데이트되고 동일한 데이터와 동일한 ID를 가지고 있지만 스냅샷의 블록 3과 같지는 않습니다. 따라서 4개 블록에 대한 요금이 계정에 청구됩니다.

![기본 Blob과 스냅샷의 고유 블록에 대한 요금 청구를 보여 주는 다이어그램 2](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>시나리오 3

시나리오 3에서는 기본 Blob은 업데이트되었지만 스냅샷은 업데이트되지 않았습니다. 기본 Blob에서 블록 3이 블록 4로 바뀌었지만 스냅샷은 계속 블록 3을 반영합니다. 따라서 4개 블록에 대한 요금이 계정에 청구됩니다.

![기본 Blob과 스냅샷의 고유 블록에 대한 요금 청구를 보여 주는 다이어그램 3](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>시나리오 4

시나리오 4에서는 기본 Blob이 완전히 업데이트되었으며 원래 블록을 하나도 포함하지 않습니다. 따라서 8개 고유 블록 모두에 대한 요금이 계정에 청구됩니다.

![기본 Blob과 스냅샷의 고유 블록에 대한 요금 청구를 보여 주는 다이어그램 4](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

> [!TIP]
> 전체 Blob을 덮어쓰는 메서드를 호출하지 않고 비용을 낮게 유지하기 위해 개별 블록을 업데이트합니다.

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Blob 계층이 명시적으로 설정된 경우 요금 청구

Blob이나 스냅샷(또는 버전)에 대한 Blob 계층을 명시적으로 설정한 경우, 원본 계층의 개체와 블록을 공유하는지 여부와 관계없이 새 계층에서 개체의 전체 콘텐츠 길이에 대한 요금이 청구됩니다. 또한 원본 계층에서 가장 오래된 버전의 전체 콘텐츠 길이에 대한 요금도 청구됩니다. 원본 계층에 남아 있는 모든 버전이나 스냅샷은 [Blob 계층이 명시적으로 설정되지 않은 경우의 요금 청구](#billing-when-the-blob-tier-has-not-been-explicitly-set)에 설명된 대로 공유될 수 있는 고유 블록에 대한 요금이 청구됩니다.

#### <a name="moving-a-blob-to-a-new-tier"></a>새 계층으로 Blob 이동

다음 표에서는 Blob 또는 스냅샷이 새 계층으로 이동할 때의 요금 청구 방식을 설명합니다.

| Blob 계층이 명시적으로 설정 된 경우 | 다음에 대한 요금이 청구됩니다. |
|-|-|
| 스냅샷이 있는 기본 Blob | 새 계층의 기본 Blob, 원본 계층의 가장 오래된 스냅샷, 다른 스냅샷의 모든 고유 블록. <sup>1</sup> |
| 이전 버전과 스냅샷이 있는 기본 Blob | 새 계층의 기본 Blob, 원본 계층의 가장 오래된 버전, 원본 계층의 가장 오래된 스냅샷, 다른 버전 또는 스냅샷의 고유 블록<sup>1</sup>. |
| 스냅샷 | 새 계층의 스냅샷과 원본 계층의 기본 Blob에는 다른 스냅샷의 모든 고유 블록이 추가됩니다. <sup>1</sup> |

<sup>1</sup>원본 계층에서 이동되지 않은 다른 이전 버전 또는 스냅샷이 있는 경우 해당 버전 또는 스냅샷은 [Blob 계층이 명시적으로 설정되지 않은 경우의 청구](#billing-when-the-blob-tier-has-not-been-explicitly-set)에 설명된 대로 그 안에 포함된 고유 블록 수에 따라 요금이 청구됩니다.

다음 다이어그램은 스냅샷이 있는 Blob을 다른 계층으로 이동할 때 개체의 요금을 청구하는 방법을 보여 줍니다.

:::image type="content" source="media/snapshots-overview/snapshot-billing-tiers.png" alt-text="스냅샷이 있는 Blob이 명시적으로 계층화된 경우 개체의 요금을 청구하는 방법을 보여 주는 다이어그램":::

Blob, 버전 또는 스냅샷에 대한 계층을 명시적으로 설정하는 작업은 취소할 수 없습니다. 새 계층으로 Blob을 이동한 다음 원래 계층으로 다시 이동하는 경우 원본 계층의 다른 개체와 블록을 공유하는 경우에도 개체의 전체 콘텐츠 길이에 대한 요금이 청구됩니다.

Blob, 버전 또는 스냅샷의 계층을 명시적으로 설정하는 작업은 다음과 같습니다.

- [Blob 계층 설정](/rest/api/storageservices/set-blob-tier)
- 계층을 지정하여 [Blob 배치](/rest/api/storageservices/put-blob)
- 지정된 계층을 사용하여 [블록 목록 배치](/rest/api/storageservices/put-block-list)
- 계층을 지정하여 [Blob 복사](/rest/api/storageservices/copy-blob)

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>일시 삭제를 사용한 경우 Blob 삭제

Blob 일시 삭제를 사용하는 경우 계층을 명시적으로 설정한 기본 Blob을 삭제하거나 덮어쓰면 모든 이전 버전 또는 일시 삭제된 Blob의 스냅샷이 전체 콘텐츠 길이로 청구됩니다. Blob 버전 관리와 일시 삭제를 함께 사용하는 방법에 대한 자세한 내용은 [Blob 버전 관리 및 일시 삭제](versioning-overview.md#blob-versioning-and-soft-delete)를 참조하세요.

다음 표에서는 버전 관리의 사용 여부에 따라 일시 삭제된 Blob에 대한 청구를 설명합니다. 버전 관리를 사용한 경우 Blob이 일시 삭제되면 새로운 버전이 만들어집니다. 버전 관리를 사용하지 않는 경우 Blob을 일시 삭제하면 일시 삭제 스냅샷이 만들어집니다.

| 계층을 사용 하 여 기본 blob를 덮어쓸 때를 명시적으로 설정 하는 경우 ... | 다음에 대한 요금이 청구됩니다. |
|-|-|
| Blob 일시 삭제 및 버전 관리를 모두 사용하는 경우 | 계층에 관계 없이 모든 기존 버전의 전체 콘텐츠 길이 |
| Blob 일시 삭제를 사용하도록 설정했지만 버전 관리를 사용하지 않는 경우 | 계층에 관계 없이 모든 기존 일시 삭제 스냅샷이 전체 콘텐츠 길이에 포함됩니다. |

## <a name="feature-support"></a>기능 지원

이 표에서는 사용자 계정에서 이 기능이 지원되는 방법과 특정 기능을 활성화할 때 지원에 미치는 영향을 보여 줍니다.

| Storage 계정 유형 | Blob Storage(기본 지원) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> | SFTP <sup>1</sup> |
|--|--|--|--|--|
| 표준 범용 v2 | ![예](../media/icons/yes-icon.png) |![예](../media/icons/yes-icon.png)  <sup>2</sup>              | ![예](../media/icons/no-icon.png) | ![예](../media/icons/no-icon.png) |
| Premium 블록 Blob          | ![예](../media/icons/yes-icon.png) |![예](../media/icons/yes-icon.png)  <sup>2</sup>              | ![예](../media/icons/no-icon.png) | ![예](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2, NFS (네트워크 파일 시스템) 3.0 프로토콜 및 SFTP (Secure file Transfer protocol) 지원에는 모두 계층적 네임 스페이스를 사용 하는 저장소 계정이 필요 합니다.

<sup>2</sup>    기능은 미리 보기 수준에서 지원 됩니다.

## <a name="next-steps"></a>다음 단계

- [Blob 버전 관리](versioning-overview.md)
- [.NET에서 Blob 스냅샷 만들기 및 관리](snapshots-manage-dotnet.md)
- [증분 스냅샷을 사용하여 Azure 관리되지 않는 VM 디스크 백업](../../virtual-machines/windows/incremental-snapshots.md)
