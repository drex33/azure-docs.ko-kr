---
title: Blob 데이터에 대한 핫, 쿨 및 보관 액세스 계층
titleSuffix: Azure Storage
description: Azure Storage는 Blob 데이터를 사용하는 방법에 따라 가장 비용 효율적인 방식으로 저장할 수 있도록 다양한 액세스 계층을 제공합니다. Blob Storage 핫, 쿨 및 보관 액세스 계층에 대해 알아봅니다.
author: tamram
ms.author: tamram
ms.date: 09/29/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: fryu
ms.openlocfilehash: ee0a9714f305a63ae3f25628f1bd9e9b14ce6e01
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129294371"
---
# <a name="hot-cool-and-archive-access-tiers-for-blob-data"></a>Blob 데이터에 대한 핫, 쿨 및 보관 액세스 계층

클라우드에 저장된 데이터는 기하급수적으로 증가합니다. 확장 스토리지 요구 사항에 대한 비용을 관리하려면 액세스 빈도 및 보존 기간에 따라 데이터를 구성하는 것이 유용할 수 있습니다. Azure Storage는 Blob 데이터를 사용하는 방법에 따라 가장 비용 효율적인 방식으로 저장할 수 있도록 다양한 액세스 계층을 제공합니다. Azure Storage 액세스 계층은 다음과 같습니다.

- **핫 계층** - 자주 액세스하거나 수정하는 데이터를 저장하기 위해 최적화된 온라인 계층입니다. 핫 계층은 스토리지 비용이 가장 높지만 액세스 비용은 가장 낮습니다.
- **쿨 계층** - 자주 액세스하거나 수정하지 않는 데이터를 저장하기 위해 최적화된 온라인 계층입니다. 쿨 계층의 데이터는 최소 30일 동안 저장해야 합니다. 쿨 계층은 핫 계층에 비해 스토리지 비용이 낮고 액세스 비용이 높습니다.
- **보관 계층** - 거의 액세스하지 않고 유연한 대기 시간 요구 사항이 있는 데이터를 시간 순서대로 저장하는 데 최적화된 오프라인 계층입니다. 보관 계층의 데이터는 최소 180일 동안 저장해야 합니다.

Azure Storage 용량 제한은 액세스 계층에 따라 설정되지 않고 계정 수준에서 설정됩니다. 한 계층에서 용량 사용량을 최대화하거나 둘 이상의 계층에 용량을 분산하도록 선택할 수 있습니다.

## <a name="online-access-tiers"></a>온라인 액세스 계층

데이터가 온라인 액세스 계층(핫 또는 쿨)에 저장되면 사용자는 즉시 액세스할 수 있습니다. 핫 계층은 활성 상태인 데이터에 가장 적합한 선택이지만 쿨 계층은 액세스 빈도가 낮지만 읽기 및 쓰기에는 여전히 사용할 수 있어야 하는 데이터에 적합합니다.

핫 계층에 대한 예제 사용 시나리오는 다음과 같습니다.

- 활발하게 사용 중이거나 자주 읽고 쓸 것으로 예상되는 데이터
- 처리 및 쿨 액세스 계층에 대한 최종 마이그레이션을 위해 스테이징된 데이터.

쿨 액세스 계층에 대한 사용 시나리오는 다음과 같습니다.

- 단기 데이터 백업 및 재해 복구.
- 자주 사용되지 않지만 즉시 액세스할 수 있어야 하는 이전 데이터 집합입니다.
- 처리를 위해 추가 데이터를 수집하는 동안 비용 효율적인 방식으로 저장해야 하는 큰 데이터 집합입니다.

쿨 계층의 데이터는 가용성이 약간 낮지만 핫 계층과 동일한 높은 내구성, 검색 대기 시간 및 처리량 특성을 제공합니다. 쿨 계층의 데이터의 경우 핫 계층에 비해 가용성이 약간 낮고 액세스 비용이 높을수록 전반적인 스토리지 비용이 낮아질 수 있습니다. 자세한 내용은 [스토리지에 대한 SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_5/)를 참조하세요.

범용 v2 계정의 쿨 계층에 있는 Blob은 삭제되거나 30일이 경과하기 전에 다른 계층으로 이동하면 초기 삭제 패널티가 적용됩니다. 요금이 비례하여 배분됩니다. 예를 들어 Blob을 쿨 계층으로 이동한 다음 21일 후에 삭제하는 경우 해당 Blob을 쿨 계층에 저장하는 데 9일(30 -21)에 해당하는 초기 삭제 요금이 청구됩니다.

핫 및 쿨 계층은 모든 중복 구성을 지원합니다. Azure Storage 데이터 중복 옵션에 대한 자세한 내용은 [중복성 Azure Storage 참조하세요.](../common/storage-redundancy.md)  

## <a name="archive-access-tier"></a>보관 액세스 계층

보관 계층은 거의 액세스하지 않는 데이터를 저장하기 위한 오프라인 계층입니다. 보관 액세스 계층은 스토리지 비용이 가장 낮지만 핫 및 쿨 계층에 비해 데이터 검색 비용과 대기 시간이 더 높습니다. 보관 액세스 계층에 대한 사용 예시 시나리오는 다음과 같습니다.

- 장기 백업, 보조 백업 및 보관 데이터 세트
- 사용 가능한 최종 형태로 처리된 후에도 보존할 필요가 있는 원래(원시) 데이터
- 장기간 저장할 필요가 있거나 거의 액세스하지 않는 규정 준수 및 보관 데이터

데이터는 최소 180일 동안 보관 계층에 남아 있어야 합니다. 그렇지 않으면 초기 삭제 요금이 부과됩니다. 예를 들어 Blob이 보관으로 이동된 다음, 45일이 지나서 삭제되거나 핫 계층으로 이동되는 경우 보관 계층에 해당 Blob을 저장한 135일(180-45)에 해당하는 초기 삭제 요금이 청구됩니다.

Blob이 보관 계층에 있는 동안에는 읽거나 수정할 수 없습니다. 보관 계층에서 Blob을 읽거나 다운로드하려면 먼저 핫 또는 쿨 온라인 계층으로 리하이딩해야 합니다. 보관 계층의 데이터는 리하이딩하는 데 최대 15시간이 걸릴 수 있습니다. Blob 리하일레이션에 대한 자세한 내용은 [보관 계층의 Blob 리하일레이션 개요를 참조하세요.](archive-rehydrate-overview.md)

보관된 Blob의 메타데이터는 읽기 액세스에 사용할 수 있으므로 Blob 및 해당 속성, 메타데이터 및 인덱스 태그를 나열할 수 있습니다. 보관 계층의 Blob에 대한 메타데이터는 읽기 전용이지만 Blob 인덱스 태그는 읽거나 쓸 수 있습니다. 스냅샷은 보관된 Blob에 대해 지원되지 않습니다.

보관 계층의 Blob에 대해 지원되는 작업은 다음과 같습니다.

- [Blob 속성 가져오기](/rest/api/storageservices/get-blob-properties)
- [Blob 메타데이터 가져오기](/rest/api/storageservices/get-blob-metadata)
- [Set Blob Tags](/rest/api/storageservices/set-blob-tags)
- [Get Blob Tags](/rest/api/storageservices/get-blob-tags)
- [Find Blobs by Tags](/rest/api/storageservices/find-blobs-by-tags)
- [Blob 나열](/rest/api/storageservices/list-blobs)
- [Blob 계층 설정](/rest/api/storageservices/set-blob-tier)
- [Blob 복사](/rest/api/storageservices/copy-blob)
- [URL에서 Blob 복사](/rest/api/storageservices/copy-blob-from-url)
- [Blob 삭제](/rest/api/storageservices/delete-blob)

> [!NOTE]
> 보관 계층은 ZRS, GZRS 또는 RA-GZRS 계정에 대해 지원되지 않습니다. LRS에서 GRS로의 마이그레이션은 계정이 LRS로 설정되었을 때 Blob이 보관 계층으로 이동하지 않은 경우에만 지원됩니다. 계정이 LRS가 된 시점으로부터 30일 이내에 업데이트가 수행되고 계정이 LRS로 설정된 동안 Blob이 보관 계층으로 이동되지 않은 경우 계정을 GRS로 다시 이동할 수 있습니다.

## <a name="default-account-access-tier-setting"></a>기본 계정 액세스 계층 설정

Storage 계정에는 새 Blob이 만들어지는 온라인 계층을 나타내는 기본 액세스 계층 설정이 있습니다. 기본 액세스 계층 설정은 핫 또는 쿨로 설정할 수 있습니다. 사용자는 Blob을 업로드하거나 해당 계층을 변경할 때 개별 Blob에 대한 기본 설정을 재정의할 수 있습니다.

기본적으로 새 스토리지 계정에 대한 기본 액세스 계층은 핫 계층으로 설정됩니다. 스토리지 계정을 만들 때 또는 만든 후에 기본 액세스 계층 설정을 변경할 수 있습니다. 이 설정을 변경하지 않으면 Blob이 기본적으로 핫 계층에 업로드됩니다.

명시적으로 할당된 계층이 없는 Blob은 기본 계정 액세스 계층 설정에서 해당 계층을 유추합니다. Blob의 액세스 계층이 기본 계정 액세스 계층 설정에서 유추되는 경우 Azure Portal 액세스 계층을 **핫(유추)** 또는 **쿨(유추)로** 표시합니다.

기본 계정 액세스 계층 설정을 변경하면 액세스 계층이 명시적으로 설정되지 않은 계정의 모든 Blob에 적용됩니다. 범용 v2 계정에서 기본 계정 액세스 계층 설정을 핫에서 쿨로 전환하면 액세스 계층이 유추되는 모든 Blob에 대해 쓰기 작업(10,000개당)에 대한 요금이 청구됩니다. Blob Storage 계정에서는 이 변경이 무료입니다. 범용 v2 또는 Blob Storage 계정에서 쿨에서 핫으로 전환하면 읽기 작업(10,000개당) 및 데이터 검색(GB당)에 대한 요금이 청구됩니다.

> [!NOTE]
> 보관 계층은 스토리지 계정의 기본 액세스 계층으로 지원되지 않습니다.

## <a name="setting-or-changing-a-blobs-tier"></a>Blob 계층 설정 또는 변경

Blob을 만들 때 명시적으로 Blob의 계층을 설정하려면 Blob을 업로드할 때 계층을 지정합니다.

Blob을 만든 후 다음 방법 중 하나를 통해 계층을 변경할 수 있습니다.

- 직접 또는 [수명 주기 관리](#blob-lifecycle-management) 정책을 통해 [Blob 계층 설정](/rest/api/storageservices/set-blob-tier) 작업을 호출합니다. [Blob 계층 설정을](/rest/api/storageservices/set-blob-tier) 호출하는 것은 일반적으로 Blob 계층을 더 인기 있는 계층에서 쿨 계층으로 변경할 때 가장 좋은 옵션입니다.
- [Blob 복사](/rest/api/storageservices/copy-blob) 작업을 호출하여 한 계층에서 다른 계층으로 Blob을 복사합니다. [Blob 복사](/rest/api/storageservices/copy-blob) 호출은 보관 계층에서 온라인 계층으로 Blob을 리하드하거나 Blob을 쿨에서 핫으로 이동하는 대부분의 시나리오에서 권장됩니다. Blob을 복사하여 원본 Blob에 필요한 스토리지 간격이 아직 경과되지 않은 경우 초기 삭제 패널티를 방지할 수 있습니다. 그러나 Blob을 복사하면 원본 Blob과 대상 Blob의 두 Blob에 대한 용량 요금이 발생합니다.

Blob의 계층을 핫에서 쿨 또는 보관으로 변경하는 것은 즉시 쿨에서 핫으로 변경됩니다. 보관 계층에서 핫 또는 쿨 계층으로 Blob을 리하우전하는 데 최대 15시간이 걸릴 수 있습니다.

쿨 계층과 보관 계층 간에 Blob을 이동할 때 다음 사항에 유의하세요.

- Blob이 스토리지 계정의 기본 액세스 계층을 기반으로 쿨로 유추되는 경우 Blob이 보관으로 이동되면 초기 삭제 요금이 발생하지 않습니다.
- Blob을 명시적으로 쿨 계층으로 이동한 다음, 보관으로 이동하면 초기 삭제 요금이 적용됩니다.

## <a name="blob-lifecycle-management"></a>Blob 수명 주기 관리

Blob Storage 수명 주기 관리는 지정된 조건이 충족될 때 데이터를 원하는 액세스 계층으로 전환하는 데 사용할 수 있는 규칙 기반 정책을 제공합니다. 수명 주기 관리를 사용하여 수명이 끝나면 데이터를 만료할 수도 있습니다. 자세히 알아보려면 [Azure Blob Storage 액세스 계층을 자동화하여 비용 최적화](./lifecycle-management-overview.md)를 참조하세요.

> [!NOTE]
> 프리미엄 블록 Blob Storage 계정에 저장된 데이터는 [Blob 계층 설정](/rest/api/storageservices/set-blob-tier) 또는 Azure Blob Storage 수명 주기 관리를 사용하여 핫, 쿨 또는 보관으로 계층화할 수 없습니다. 데이터를 이동하려면 URL에서 블록 배치 API 또는 이 [API를](/rest/api/storageservices/put-block-from-url) 지원하는 AzCopy 버전을 사용하여 블록 Blob Storage 계정에서 다른 계정의 핫 계층으로 Blob을 동기적으로 복사해야 합니다. **URL에서 블록 배치** API는 서버에서 데이터를 동기적으로 복사합니다. 이는 모든 데이터를 원래 서버 위치에서 대상 위치로 이동하면 호출이 완료된다는 의미입니다.

## <a name="summary-of-access-tier-options"></a>액세스 계층 옵션 요약

다음 표에는 핫, 쿨 및 보관 액세스 계층의 기능이 요약 되어 있습니다.

|  | **핫 액세스 계층** | **쿨 액세스 계층** | **보관 액세스 계층** |
|--|--|--|--|
| **가용성** | 99.9% | 99% | 오프라인 |
| **가용성** <br> **(RA-GRS 읽기)** | 99.99% | 99.9% | 오프라인 |
| **사용 요금** | 저장소 비용이 더 많이 듭니다. 액세스 및 트랜잭션 비용은 낮아집니다. | 저장소 비용이 낮지만 액세스 및 트랜잭션 비용이 더 많이 듭니다. | 가장 낮은 저장소 비용, 최고 액세스 및 트랜잭션 비용 |
| **권장 되는 최소 데이터 보존 기간** | 해당 없음 | 30일<sup>1</sup> | 180일 |
| **대기 시간** <br> **(첫 번째 바이트까지의 시간)** | 밀리초 | 밀리초 | 시간<sup>2</sup> |
| **지원되는 중복성 구성** | 모두 | 모두 | LRS, GRS 및 RA-GRS<sup>3</sup> 만 |

<sup>1</sup> 범용 v2 계정의 쿨 계층에 있는 개체의 최소 보존 기간은 30 일입니다. Blob Storage 계정의 경우 쿨 계층에 대 한 최소 보존 기간은 없습니다.

<sup>2</sup> 보관 계층에서 blob을 리하이드레이션 하는 경우 standard 또는 high 리하이드레이션 priority 옵션을 선택할 수 있습니다. 각는 서로 다른 검색 대기 시간 및 비용을 제공 합니다. 자세한 내용은 [보관 계층의 Blob 리하이드레이션 개요](archive-rehydrate-overview.md)를 참조하세요.

<sup>3</sup> Azure Storage의 중복성 구성에 대 한 자세한 내용은 [Azure Storage 중복성](../common/storage-redundancy.md)(영문)을 참조 하세요.

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

모든 저장소 계정은 blob 계층을 기반으로 하는 블록 blob 저장소에 대 한 가격 책정 모델을 사용 합니다. 다음 섹션에서 설명 하는 청구 고려 사항에 유의 하세요.

블록 blob의 가격 책정에 대 한 자세한 내용은 [블록 blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/)을 참조 하세요.

### <a name="storage-capacity-costs"></a>Storage 용량 비용

데이터를 저장 하는 비용은 저장 되는 데이터의 양과 더불어 액세스 계층에 따라 달라 집니다. 계층의 수가 감소 하면 기가바이트 당 용량 비용이 감소 합니다.

### <a name="data-access-costs"></a>데이터 액세스 비용

계층이 차가워질수록 데이터 액세스 요금이 증가합니다. 쿨 및 보관 액세스 계층에 있는 데이터의 경우 읽기에 대해 기가바이트당 데이터 액세스 요금이 부과됩니다.

### <a name="transaction-costs"></a>트랜잭션 비용

트랜잭션 별 요금은 모든 계층에 적용 되 고 계층이 냉각기로 늘어납니다.

### <a name="geo-replication-data-transfer-costs"></a>지역에서 복제 데이터 전송 비용

이 요금은 GRS 및 RA-GRS를 포함하여 지역에서 복제가 구성된 계정에만 해당합니다. 지역 복제 데이터 전송에는 기가바이트당 요금이 발생합니다.

### <a name="outbound-data-transfer-costs"></a>아웃바운드 데이터 전송 비용

아웃 바운드 데이터 전송 (Azure 지역 외부로 전송 되는 데이터)에는 기가바이트 단위로 대역폭 사용량에 대 한 요금이 청구 됩니다. 아웃바운드 데이터 전송 요금에 대한 자세한 내용은 [대역폭 가격 책정 정보](https://azure.microsoft.com/pricing/details/bandwidth/) 페이지를 참조하세요.

### <a name="changing-the-default-account-access-tier"></a>기본 계정 액세스 계층 변경

계정 액세스 계층을 변경 하면 계층을 명시적으로 설정 하지 않은 모든 blob에 대해 계층 변경 요금이 발생 합니다. 자세한 내용은 [blob의 액세스 계층 변경](#changing-a-blobs-access-tier)섹션을 참조 하세요.

### <a name="changing-a-blobs-access-tier"></a>Blob의 액세스 계층 변경

Blob의 계층을 변경할 때 다음 청구에 영향을 염두에 두십시오.

- 계층 간에 Blob을 업로드하거나 이동하는 경우 업로드 또는 계층 변경 시 즉시 해당 요금으로 비용이 청구됩니다.
- Blob을 냉각기 계층 (핫에서 쿨, 핫에서 보관 또는 쿨로 보관)으로 이동 하는 경우 대상 계층에 대 한 쓰기 작업 (1만) 및 데이터 쓰기 (GB 당) 요금이 적용 되는 대상 계층에 대 한 쓰기 작업으로 작업이 청구 됩니다.
- Blob이 핫 계층으로 이동 하면 (archive to 쿨, archive to hot 또는 쿨 to hot) 작업은 원본 계층에서 읽기로 청구 되며 원본 계층의 읽기 작업 (1만 당) 및 데이터 검색 (GB 당) 요금이 적용 됩니다. 쿨 또는 보관 계층에서 이동한 모든 Blob에 대한 초기 삭제 요금도 적용해야 합니다.
- Blob이 보관 계층에서 발생 하는 동안 데이터를 복원 하 고 blob 계층이 핫 또는 쿨로 변경 될 때까지 blob의 데이터는 보관 된 데이터로 청구 됩니다.

다음 표에는 이러한 계층의 변경 내용에 대한 대금 청구 방식이 요약되어 있습니다.

| | **쓰기 요금(작업 + 액세스)** | **읽기 요금(작업 + 액세스)** |
| ---- | ----- | ----- |
| **Blob 계층 작업 설정** | 핫-쿨<br> 핫에서 보관<br> 보관할 쿨 | 쿨에 보관<br> 핫에 보관<br> 쿨에서 핫으로

버전 관리를 사용하도록 설정하거나 Blob에 스냅샷이 있는 경우 Blob에 대한 액세스 계층을 변경하면 추가 요금이 발생할 수 있습니다. 버전 관리를 사용하도록 설정된 Blob에 대한 자세한 내용은 Blob 버전 관리 설명서의 [가격 책정 및 청구](versioning-overview.md#pricing-and-billing)를 참조하세요. 스냅샷이 포함된 Blob에 대한 자세한 내용은 Blob 스냅샷 설명서의 [가격 책정 및 청구](snapshots-overview.md#pricing-and-billing)를 참조하세요.

## <a name="feature-support"></a>기능 지원

이 표에서는 사용자 계정에서 이 기능이 지원되는 방법과 특정 기능을 활성화할 때 지원에 미치는 영향을 보여 줍니다.

| Storage 계정 유형 | Blob Storage(기본 지원) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> |
|--|--|--|--|
| 표준 범용 v2 | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |
| Premium 블록 Blob | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2와 NFS (네트워크 파일 시스템) 3.0 프로토콜 모두에는 계층적 네임 스페이스를 사용 하는 저장소 계정이 필요 합니다.

지역별 기능 지원에 대 한 자세한 내용은 [지역별 사용 가능한 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=storage)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Storage 계정에서 Blob 계층을 관리하는 방법](manage-access-tier.md)
- [Azure Storage 계정의 기본 계정 액세스 계층을 관리하는 방법](../common/manage-account-default-access-tier.md)
- [Azure Blob Storage 액세스 계층을 자동화하여 비용 최적화](./lifecycle-management-overview.md)
