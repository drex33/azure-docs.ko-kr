---
title: Blob 데이터에 대 한 핫, 쿨 및 보관 액세스 계층
titleSuffix: Azure Storage
description: Azure storage는 사용 방법에 따라 가장 비용 효율적인 방식으로 blob 데이터를 저장할 수 있도록 다양 한 액세스 계층을 제공 합니다. Blob Storage에 대 한 핫, 쿨 및 보관 액세스 계층에 대해 알아봅니다.
author: tamram
ms.author: tamram
ms.date: 11/01/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: fryu
ms.openlocfilehash: 304ca8f96d2b8973bd4f7f394bc0d2fbff541a61
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131421991"
---
# <a name="hot-cool-and-archive-access-tiers-for-blob-data"></a>Blob 데이터에 대 한 핫, 쿨 및 보관 액세스 계층

클라우드에 저장된 데이터는 기하급수적으로 증가합니다. 확장 저장소 요구에 대 한 비용을 관리 하려면 액세스 빈도 및 보존 기간에 따라 데이터를 구성 하는 것이 도움이 될 수 있습니다. Azure storage는 사용 방법에 따라 가장 비용 효율적인 방식으로 blob 데이터를 저장할 수 있도록 다양 한 액세스 계층을 제공 합니다. Azure Storage 액세스 계층은 다음과 같습니다.

- **핫 계층** -자주 액세스 하거나 수정 되는 데이터를 저장 하는 데 최적화 된 온라인 계층입니다. 핫 계층의 저장소 비용은 가장 낮지만 액세스 비용은 가장 낮습니다.
- **쿨 계층** -자주 액세스 하거나 수정 되지 않는 데이터를 저장 하는 데 최적화 된 온라인 계층입니다. 쿨 계층의 데이터는 최소 30 일 동안 저장 해야 합니다. 쿨 계층은 핫 계층에 비해 저장소 비용이 낮고 액세스 비용이 높습니다.
- **보관 계층** -거의 액세스 하지 않는 데이터를 저장 하기 위해 최적화 된 오프 라인 계층으로, 시간 순서에 따라 유연한 대기 시간 요구 사항이 있습니다. 보관 계층의 데이터는 최소 180 일 동안 저장 해야 합니다.

Azure 저장소 용량 제한은 액세스 계층에 따라 설정 되지 않고 계정 수준에서 설정 됩니다. 한 계층에서 용량 사용량을 최대화 하거나 둘 이상의 계층에 용량을 분산 하도록 선택할 수 있습니다.

## <a name="online-access-tiers"></a>온라인 액세스 계층

데이터가 온라인 액세스 계층 (핫 또는 쿨)에 저장 되 면 사용자가 즉시 액세스할 수 있습니다. 핫 계층은 활성 상태인 데이터에 가장 적합 한 선택 이지만 쿨 계층은 자주 액세스 하지 않는 데이터에 적합 하지만 읽고 쓸 수 있어야 합니다.

핫 계층에 대 한 사용 시나리오 예는 다음과 같습니다.

- 활발하게 사용 중이거나 자주 읽고 쓸 것으로 예상되는 데이터
- 처리를 위해 준비 된 데이터 및 쿨 액세스 계층으로의 최종 마이그레이션

쿨 액세스 계층에 대 한 사용 시나리오는 다음과 같습니다.

- 단기 데이터 백업 및 재해 복구
- 자주 사용 되지 않지만 즉시 액세스할 수 있어야 하는 오래 된 데이터 집합입니다.
- 처리를 위해 추가 데이터를 수집 하는 동안 비용 효율적인 방식으로 저장 해야 하는 대량 데이터 집합

쿨 계층의 데이터는 약간 낮은 가용성이 있지만 핫 계층과 동일한 높은 내구성, 검색 대기 시간 및 처리량 특성을 제공 합니다. 쿨 계층의 데이터에 대해 약간 낮은 가용성 및 높은 액세스 비용은 핫 계층에 비해 전체 저장소 비용을 절감 하는 데 사용할 수 있습니다. 자세한 내용은 [스토리지에 대한 SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_5/)를 참조하세요.

범용 v2 계정에서 쿨 계층의 blob은 30 일이 경과할 때까지 다른 계층으로 삭제 되거나 이동 되는 경우 초기 삭제 페널티의 영향을 받습니다. 요금이 비례하여 배분됩니다. 예를 들어 blob이 쿨 계층으로 이동 하 고 21 일 후에 삭제 되는 경우 쿨 계층에 해당 blob을 저장 하는 9 (30-21) 일에 해당 하는 초기 삭제 요금이 청구 됩니다.

핫 및 쿨 계층은 모든 중복성 구성을 지원 합니다. Azure Storage의 데이터 중복성 옵션에 대 한 자세한 내용은 [Azure Storage 중복성](../common/storage-redundancy.md)을 참조 하십시오.  

## <a name="archive-access-tier"></a>보관 액세스 계층

보관 계층은 거의 액세스 하지 않는 데이터를 저장 하기 위한 오프 라인 계층입니다. 보관 액세스 계층의 저장소 비용은 가장 낮지만 핫 및 쿨 계층에 비해 데이터 검색 비용과 대기 시간이 더 높습니다. 보관 액세스 계층의 예제 사용 시나리오는 다음과 같습니다.

- 장기 백업, 보조 백업 및 보관 데이터 세트
- 사용 가능한 최종 형태로 처리된 후에도 보존할 필요가 있는 원래(원시) 데이터
- 장기간 저장할 필요가 있거나 거의 액세스하지 않는 규정 준수 및 보관 데이터

데이터는 최소 180 일 동안 보관 계층에 유지 되거나 초기 삭제 요금이 적용 되어야 합니다. 예를 들어 blob을 보관 계층으로 이동한 다음 45 일 후에 삭제 하거나 핫 계층으로 이동 하는 경우 보관 계층에 해당 blob을 저장 하는 135 (180-45) 일에 해당 하는 초기 삭제 요금이 청구 됩니다.

Blob은 보관 계층에 있지만 읽거나 수정할 수는 없습니다. 보관 계층에서 blob을 읽거나 다운로드 하려면 먼저 핫 또는 쿨 인 blob을 온라인 계층으로 리하이드레이션 해야 합니다. 보관 계층의 데이터는 리하이드레이션 작업에 대해 지정한 우선 순위에 따라 리하이드레이션 최대 15 시간이 걸릴 수 있습니다. Blob 리하이드레이션에 대 한 자세한 내용은 [보관 계층의 blob 리하이드레이션 개요](archive-rehydrate-overview.md)를 참조 하세요.

보관 된 blob의 메타 데이터는 읽기 액세스에 계속 사용할 수 있으므로 blob 및 해당 속성, 메타 데이터 및 인덱스 태그를 나열할 수 있습니다. 보관 계층의 blob에 대 한 메타 데이터는 읽기 전용 이지만 blob 인덱스 태그는 읽거나 쓸 수 있습니다. 스냅숏은 보관 된 blob에 대해 지원 되지 않습니다.

보관 계층의 blob에 대해 지원 되는 작업은 다음과 같습니다.

- [Blob 복사](/rest/api/storageservices/copy-blob)
- [Blob 삭제](/rest/api/storageservices/delete-blob)
- [Find Blobs by Tags](/rest/api/storageservices/find-blobs-by-tags)
- [Blob 메타데이터 가져오기](/rest/api/storageservices/get-blob-metadata)
- [Blob 속성 가져오기](/rest/api/storageservices/get-blob-properties)
- [Get Blob Tags](/rest/api/storageservices/get-blob-tags)
- [Blob 나열](/rest/api/storageservices/list-blobs)
- [Set Blob Tags](/rest/api/storageservices/set-blob-tags)
- [Blob 계층 설정](/rest/api/storageservices/set-blob-tier)

> [!NOTE]
> 보관 계층은 ZRS, GZRS 또는 RA-GZRS 계정에 대해 지원 되지 않습니다. 계정이 LRS로 설정 된 동안에는 LRS에서 GRS로의 마이그레이션이 보관 계층으로 이동 하지 않은 경우에만 지원 됩니다. 계정이 LRS 된 시간부터 30 일 이내에 업데이트를 수행 하는 경우 계정을 GRS로 다시 이동할 수 있으며, 계정이 LRS로 설정 된 동안에는 blob가 보관 계층으로 이동 하지 않습니다.

## <a name="default-account-access-tier-setting"></a>기본 계정 액세스 계층 설정

Storage 계정에는 새 blob을 만든 온라인 계층을 나타내는 기본 액세스 계층 설정이 있습니다. 기본 액세스 계층 설정은 핫 또는 쿨로 설정할 수 있습니다. 사용자는 blob을 업로드 하거나 계층을 변경할 때 개별 blob에 대 한 기본 설정을 재정의할 수 있습니다.

새 범용 v2 저장소 계정에 대 한 기본 액세스 계층은 기본적으로 핫 계층으로 설정 됩니다. 저장소 계정을 만들 때 또는 만든 후에 기본 액세스 계층 설정을 변경할 수 있습니다. 저장소 계정에서이 설정을 변경 하지 않거나 blob을 업로드할 때 계층을 명시적으로 설정 하지 않으면 기본적으로 새 blob이 핫 계층으로 업로드 됩니다.

명시적으로 할당 된 계층이 없는 blob는 기본 계정 액세스 계층 설정에서 계층을 유추 합니다. Blob의 액세스 계층이 기본 계정 액세스 계층 설정에서 유추 되는 경우 Azure Portal는 액세스 계층을 **핫 (유추)** 또는 **쿨 (유추)** 로 표시 합니다.

저장소 계정에 대 한 기본 액세스 계층 설정을 변경 하는 것은 액세스 계층을 명시적으로 설정 하지 않은 계정의 모든 blob에 적용 됩니다. 범용 v2 계정에서 기본 액세스 계층 설정을 핫에서 쿨로 전환 하는 경우 액세스 계층이 유추 되는 모든 blob에 대 한 쓰기 작업 (1만 당)에 대해 요금이 청구 됩니다. 범용 v2 계정에서 쿨에서 핫으로 전환 하는 경우 읽기 작업 (1만 당) 및 데이터 검색 (GB 당) 모두에 대 한 요금이 청구 됩니다.

레거시 Blob Storage 계정을 만드는 경우 만들 때 기본 액세스 계층 설정을 핫 또는 쿨로 지정 해야 합니다. 레거시 Blob Storage 계정에서 기본 계정 액세스 계층 설정을 핫에서 쿨로 변경 하는 데에는 요금이 부과 되지 않습니다. Blob Storage 계정에서 쿨에서 핫으로 전환 하는 경우 읽기 작업 (1만 당) 및 데이터 검색 (GB 당) 모두에 대 한 요금이 청구 됩니다. 가능 하면 Blob Storage 계정이 아닌 범용 v2 저장소 계정을 사용 하는 것이 좋습니다.

> [!NOTE]
> 보관 계층은 저장소 계정에 대 한 기본 액세스 계층으로 지원 되지 않습니다.

## <a name="setting-or-changing-a-blobs-tier"></a>Blob 계층 설정 또는 변경

Blob 계층을 만들 때 명시적으로 설정 하려면 blob을 업로드할 때 계층을 지정 합니다.

Blob을 만든 후에는 다음 방법 중 하나로 계층을 변경할 수 있습니다.

- 직접 또는 [수명 주기 관리](#blob-lifecycle-management) 정책을 통해 [Blob 계층 설정](/rest/api/storageservices/set-blob-tier) 작업을 호출 합니다. [Set Blob 계층](/rest/api/storageservices/set-blob-tier) 을 호출 하는 것은 일반적으로 blob 계층을 더울 계층에서 냉각기로 변경할 때 가장 적합 한 옵션입니다.
- Blob [복사](/rest/api/storageservices/copy-blob) 작업을 호출 하 여 한 계층에서 다른 계층으로 blob을 복사 합니다. Blob을 보관 계층에서 온라인 계층으로 리하이드레이션 blob을 쿨에서 핫으로 이동 하는 대부분의 시나리오에는 [Blob 복사](/rest/api/storageservices/copy-blob) 를 호출 하는 것이 좋습니다. Blob을 복사 하 여 원본 blob에 필요한 저장소 간격이 아직 경과 되지 않은 경우 초기 삭제 페널티를 방지할 수 있습니다. 그러나 blob을 복사 하면 원본 blob 및 대상 blob의 두 blob에 대 한 용량 요금이 발생 합니다.

핫에서 핫으로 변경 하므로 blob의 계층을 핫에서 쿨 또는 보관으로 변경 하는 것이 즉각적입니다. 보관 계층에서 핫 또는 쿨 계층으로 blob을 리하이드레이션 하는 데 최대 15 시간이 걸릴 수 있습니다.

쿨 및 Archive 계층 간에 blob을 이동할 때는 다음 사항을 염두에 두어야 합니다.

- Blob 계층이 저장소 계정의 기본 액세스 계층을 기반으로 쿨로 유추 되 고 blob가 보관 계층으로 이동 되 면 초기 삭제 요금이 발생 하지 않습니다.
- Blob을 명시적으로 쿨 계층으로 이동한 다음 보관 계층으로 이동 하면 초기 삭제 요금이 적용 됩니다.

다음 표에는 다양 한 계층 간에 blob을 이동 하기 위해 수행할 수 있는 방법이 요약 되어 있습니다.

| 원본/대상 | 핫 액세스 계층 | 쿨 액세스 계층 | 보관 액세스 계층 |
|--|--|--|--|
| **핫 액세스 계층** | N/A | Blob 계층 설정 또는 Blob **복사를** 사용하여 **Blob** 계층을 핫에서 쿨로 변경합니다. [자세한 정보...](manage-access-tier.md)<br /><br />수명 주기 관리 정책을 사용하여 Blob을 쿨 계층으로 이동합니다. [자세한 정보...](lifecycle-management-overview.md) | Blob 계층 설정 또는 Blob **복사를** 사용하여 **Blob** 계층을 핫에서 보관으로 변경합니다. [자세한 정보...](archive-blob.md) <br /><br />수명 주기 관리 정책을 통해 Blob을 보관합니다. [자세한 정보...](lifecycle-management-overview.md) |
| **쿨 액세스 계층** | Blob 계층 설정 또는 Blob **복사를** 사용하여 **Blob** 계층을 쿨에서 핫으로 변경합니다. [자세한 정보...](manage-access-tier.md) <br /><br />수명 주기 관리 정책을 사용하여 Blob을 핫 계층으로 이동합니다. [자세한 정보...](lifecycle-management-overview.md) | N/A | Blob 계층 설정 또는 Blob **복사를** 사용하여 Blob 계층을 쿨에서 **보관으로** 변경합니다. [자세한 정보...](archive-blob.md) <br /><br />수명 주기 관리 정책을 통해 Blob을 보관합니다. [자세한 정보...](lifecycle-management-overview.md) |
| **보관 액세스 계층** | Blob 계층 설정 또는 **Blob** **복사를** 사용하여 핫 계층으로 리하이딩합니다. [자세한 정보...](archive-rehydrate-to-online-tier.md) | Blob 계층 설정 또는 **Blob** **복사를** 사용하여 쿨 계층으로 리하이딩합니다. [자세한 정보...](archive-rehydrate-to-online-tier.md) | N/A |

## <a name="blob-lifecycle-management"></a>Blob 수명 주기 관리

Blob Storage 수명 주기 관리는 지정된 조건이 충족될 때 데이터를 원하는 액세스 계층으로 전환하는 데 사용할 수 있는 규칙 기반 정책을 제공합니다. 수명 주기 관리를 사용하여 수명 종료 시 데이터를 만료할 수도 있습니다. 자세히 알아보려면 [Azure Blob Storage 액세스 계층을 자동화하여 비용 최적화](./lifecycle-management-overview.md)를 참조하세요.

> [!NOTE]
> 프리미엄 블록 Blob Storage 계정에 저장된 데이터는 [Blob 계층 설정](/rest/api/storageservices/set-blob-tier) 또는 Azure Blob Storage 수명 주기 관리를 사용하여 핫, 쿨 또는 보관으로 계층화할 수 없습니다. 데이터를 이동하려면 URL에서 블록 배치 API 또는 이 [API를](/rest/api/storageservices/put-block-from-url) 지원하는 AzCopy 버전을 사용하여 블록 Blob Storage 계정에서 다른 계정의 핫 계층으로 Blob을 동기적으로 복사해야 합니다. **URL에서 블록 배치** API는 서버에서 데이터를 동기적으로 복사합니다. 이는 모든 데이터를 원래 서버 위치에서 대상 위치로 이동하면 호출이 완료된다는 의미입니다.

## <a name="summary-of-access-tier-options"></a>액세스 계층 옵션 요약

다음 표에는 핫, 쿨 및 보관 액세스 계층의 기능이 요약되어 있습니다.

|  | **핫 액세스 계층** | **쿨 액세스 계층** | **보관 액세스 계층** |
|--|--|--|--|
| **가용성** | 99.9% | 99% | 오프라인 |
| **가용성** <br> **(RA-GRS 읽기)** | 99.99% | 99.9% | 오프라인 |
| **사용 요금** | 스토리지 비용이 높지만 액세스 및 트랜잭션 비용은 낮아지지만 | 스토리지 비용이 낮아지지만 액세스 및 트랜잭션 비용이 높아질 수 있습니다. | 스토리지 비용이 가장 낮지만 액세스 및 트랜잭션 비용이 가장 높습니다. |
| **최소 권장 데이터 보존 기간** | 해당 없음 | 30일<sup>1</sup> | 180일 |
| **대기 시간** <br> **(첫 번째 바이트까지의 시간)** | 밀리초 | 밀리초 | 시간<sup>2</sup> |
| **지원되는 중복성 구성** | 모두 | 모두 | LRS, GRS 및 RA-GRS<sup>3만</sup> |

<sup>1</sup> 범용 v2 계정의 쿨 계층에 있는 개체의 최소 보존 기간은 30일입니다. Blob Storage 계정의 경우 쿨 계층에 대한 최소 보존 기간이 없습니다.

<sup>2</sup> 보관 계층에서 Blob을 리하우전할 때 표준 또는 높은 리하일레이션 우선 순위 옵션을 선택할 수 있습니다. 각기 다른 검색 대기 시간 및 비용을 제공합니다. 자세한 내용은 [보관 계층 에서 Blob 리하일레이션 개요를 참조하세요.](archive-rehydrate-overview.md)

<sup>3</sup> Azure Storage 중복 구성에 대한 자세한 내용은 [중복성 Azure Storage 참조하세요.](../common/storage-redundancy.md)

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

모든 스토리지 계정은 Blob 계층을 기반으로 하는 블록 Blob Storage에 가격 책정 모델을 사용합니다. 다음 섹션에서 설명하는 청구 고려 사항에 유의하세요.

블록 Blob의 가격 책정에 대한 자세한 내용은 [블록 Blob 가격 책정을 참조하세요.](https://azure.microsoft.com/pricing/details/storage/blobs/)

### <a name="storage-capacity-costs"></a>용량 비용 Storage

저장된 데이터의 양 외에도 데이터 저장 비용은 액세스 계층에 따라 달라집니다. 계층이 냉각되면 기가바이트당 용량 비용이 감소합니다.

### <a name="data-access-costs"></a>데이터 액세스 비용

계층이 차가워질수록 데이터 액세스 요금이 증가합니다. 쿨 및 보관 액세스 계층의 데이터에는 읽기에 대한 기가바이트당 데이터 액세스 요금이 청구됩니다.

### <a name="transaction-costs"></a>트랜잭션 비용

트랜잭션당 요금은 모든 계층에 적용되며 계층이 낮아지면 증가합니다.

### <a name="geo-replication-data-transfer-costs"></a>지역 복제 데이터 전송 비용

이 요금은 GRS 및 RA-GRS를 포함하여 지역에서 복제가 구성된 계정에만 해당합니다. 지역 복제 데이터 전송에는 기가바이트당 요금이 발생합니다.

### <a name="outbound-data-transfer-costs"></a>아웃바운드 데이터 전송 비용

아웃바운드 데이터 전송(Azure 지역 외부로 전송되는 데이터)은 기가바이트 단위로 대역폭 사용량에 대한 요금이 청구됩니다. 아웃바운드 데이터 전송 요금에 대한 자세한 내용은 [대역폭 가격 책정 정보](https://azure.microsoft.com/pricing/details/bandwidth/) 페이지를 참조하세요.

### <a name="changing-the-default-account-access-tier"></a>기본 계정 액세스 계층 변경

계정 액세스 계층을 변경하면 계층이 명시적으로 설정되지 않은 모든 Blob에 대한 계층 변경 요금이 발생합니다. 자세한 내용은 [Blob의 액세스 계층 변경 섹션을 참조하세요.](#changing-a-blobs-access-tier)

### <a name="changing-a-blobs-access-tier"></a>Blob의 액세스 계층 변경

Blob의 계층을 변경할 때 다음과 같은 청구 영향이 있습니다.

- 계층 간에 Blob을 업로드하거나 이동하는 경우 업로드 또는 계층 변경 시 즉시 해당 요금으로 비용이 청구됩니다.
- Blob이 쿨 계층(핫에서 쿨로, 핫에서 보관으로 또는 쿨에서 보관으로)으로 이동되면 작업은 대상 계층에 대한 쓰기 작업으로 청구됩니다. 여기서 대상 계층의 쓰기 작업(10,000개당) 및 데이터 쓰기(GB당) 요금이 적용됩니다.
- Blob이 계층 계층으로 이동되면(보관에서 쿨로, 핫으로 보관 또는 쿨에서 핫으로) 작업은 원본 계층에서 읽기로 청구되며, 원본 계층의 읽기 작업(10,000개당) 및 데이터 검색(GB당) 요금이 적용됩니다. 쿨 또는 보관 계층에서 이동한 모든 Blob에 대한 초기 삭제 요금도 적용될 수 있습니다.
- 보관 계층에서 Blob을 리하딩하는 동안 해당 Blob의 데이터는 데이터가 복원되고 Blob 계층이 핫 또는 쿨로 변경될 때까지 보관된 데이터로 요금이 청구됩니다.

다음 표에는 이러한 계층의 변경 내용에 대한 대금 청구 방식이 요약되어 있습니다.

| | **쓰기 요금(작업 + 액세스)** | **읽기 요금(작업 + 액세스)** |
| ---- | ----- | ----- |
| **Blob 계층 작업 설정** | 핫에서 쿨로<br> 핫에서 보관<br> 보관할 쿨 | 보관에서 쿨로<br> 보관에서 핫으로<br> 쿨에서 핫으로

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
