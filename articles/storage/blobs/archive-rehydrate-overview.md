---
title: 보관 계층의 Blob 리하이드레이션 개요
description: Blob이 보관 액세스 계층에 있는 동안에는 오프라인으로 간주되며 읽거나 수정할 수 없습니다. 보관된 Blob의 데이터를 읽거나 수정하려면 먼저 Blob을 온라인 계층(핫 또는 쿨 계층)으로 리하이드레이션해야 합니다.
services: storage
author: tamram
ms.author: tamram
ms.date: 08/24/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: fryu
ms.openlocfilehash: 33f29c44e32f1deed0c21e3695e61457a31c7f65
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829679"
---
# <a name="overview-of-blob-rehydration-from-the-archive-tier"></a>보관 계층의 Blob 리하이드레이션 개요

Blob이 보관 액세스 계층에 있는 동안에는 오프라인으로 간주되며 읽거나 수정할 수 없습니다. 보관된 Blob의 데이터를 읽거나 수정하려면 먼저 Blob을 온라인 계층(핫 또는 쿨 계층)으로 리하이드레이션해야 합니다. 보관 계층에 저장된 Blob을 리하이드레이션하는 옵션에는 다음 두 가지가 있습니다.

- [보관된 Blob을 온라인 계층에 복사](#copy-an-archived-blob-to-an-online-tier): 보관된 Blob을 [Blob 복사](/rest/api/storageservices/copy-blob) 또는 [URL에서 Blob 복사](/rest/api/storageservices/copy-blob-from-url) 작업으로 핫 또는 쿨 계층의 새 Blob에 복사하여 리하이드레이션할 수 있습니다. 대부분의 시나리오에서는 이 옵션이 권장됩니다.

- [Blob의 액세스 계층을 온라인 계층으로 변경](#change-a-blobs-access-tier-to-an-online-tier) - [Blob 계층 설정](/rest/api/storageservices/set-blob-tier) 작업을 통해 계층을 변경하여 보관된 Blob을 핫 또는 쿨 계층으로 리하이드레이션할 수 있습니다.

보관 계층에서 Blob을 리하이드레이션하는 데는 몇 시간이 걸릴 수 있습니다. 최적의 성능을 위해 더 큰 Blob을 리하이드레이션하는 것이 좋습니다. 여러 개의 소규모 Blob을 동시에 리하이드레이션하면 시간이 추가적으로 더 소요될 수 있습니다.

보관 계층에서 온라인 계층으로 Blob을 리하이드레이션할 때 이벤트를 발생시키고 해당 이벤트를 이벤트 처리기로 보내도록 [Azure Event Grid](../../event-grid/overview.md)를 구성할 수 있습니다. 자세한 내용은 [Blob 리하이드레이션에서 이벤트 처리](#handle-an-event-on-blob-rehydration)를 참조하세요.

Azure Storage의 액세스 계층에 대한 자세한 내용은 [Azure Blob Storage의 액세스 계층 - 핫, 쿨 및 보관 스토리지 계층](storage-blob-storage-tiers.md)을 참조하세요.

## <a name="rehydration-priority"></a>리하이드레이션 우선 순위

Blob을 리하이드레이션하는 경우 [Blob 계층 설정](/rest/api/storageservices/set-blob-tier) 또는 [Blob 복사](/rest/api/storageservices/copy-blob)/[URL에서 Blob 복사](/rest/api/storageservices/copy-blob-from-url) 작업에서 선택적 *x-ms-rehydrate-priority* 헤더를 통해 리하이드레이션 작업의 우선 순위를 설정할 수 있습니다. 리하이드레이션 우선 순위 옵션은 다음과 같습니다.

- **표준 우선 순위**: 리하이드레이션 요청은 수신된 순서대로 처리되고 최대 15시간이 걸릴 수 있습니다.
- **높은 우선 순위**: 리하이드레이션 요청은 표준 우선 순위 요청보다 우선되며 크기가 10GB 미만인 개체의 경우 1시간 이내에 완료될 수 있습니다.

리하이드레이션 작업이 진행되는 동안 리하이드레이션 우선 순위를 확인하려면 [Blob 속성 가져오기](/rest/api/storageservices/get-blob-properties)를 호출하여 `x-ms-rehydrate-priority` 헤더의 값을 반환합니다. 리하이드레이션 우션 순위 속성은 ‘표준’ 또는 ‘높음’ 중 하나를 반환합니다.

표준 우선 순위는 기본 리하이드레이션 옵션입니다. 우선 순위가 높은 리하이드레이션이 더 빠르지만 표준 우선 순위 리하이드레이션보다 비용이 많이 듭니다. 우선 순위가 높은 리하이드레이션은 Blob 크기와 현재 수요에 따라 1시간 이상 걸릴 수 있습니다. 응급 데이터 복원 상황에서 사용할 수 있게 우선 순위가 높은 리하이드레이션을 예약하는 것이 좋습니다.

표준 우선 순위 및 높은 우선 순위 리하이드레이션 요청 간 가격 차이에 대한 자세한 내용은 [Azure Blob Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/)을 참조하세요.

## <a name="copy-an-archived-blob-to-an-online-tier"></a>보관된 Blob을 온라인 계층으로 복사

Blob을 보관 계층에서 온라인 계층으로 이동하는 첫 번째 옵션은 보관된 Blob을 핫 또는 쿨 계층에 있는 새 대상 Blob에 복사하는 것입니다. [Blob 복사](/rest/api/storageservices/copy-blob) 또는 [URL에서 Blob 복사](/rest/api/storageservices/copy-blob-from-url) 작업을 사용하여 Blob을 복사할 수 있습니다. 보관된 Blob을 온라인 계층의 새 Blob으로 복사할 경우 원본 Blob은 보관 계층에서 수정되지 않은 상태로 유지됩니다.

보관된 Blob을 다른 이름의 새 Blob이나 다른 컨테이너로 복사해야 합니다. 동일한 Blob으로 복사하여 원본 Blob을 덮어쓸 수 없습니다.

다음과 같은 이유로 Blob을 보관 계층에서 온라인 계층으로 이동해야 하는 대부분의 시나리오에서 복사 작업을 수행하는 것이 좋습니다.

- 복사 작업을 수행하면 필요한 180일 기간이 경과하기 전에 Blob의 계층을 보관 계층에서 변경하는 경우 평가되는 초기 삭제 요금을 피할 수 있습니다. 자세한 내용은 [보관 액세스 계층](storage-blob-storage-tiers.md#archive-access-tier)을 참조하세요.
- 스토리지 계정에 적용되는 수명 주기 관리 정책이 있는 경우 [Blob 계층 설정](/rest/api/storageservices/set-blob-tier)을 사용하여 Blob을 리하이드레이션하면 마지막 수정 시간이 정책에 대해 설정된 임계값을 초과하므로 수명 주기 정책이 리하이드레이션 후에 Blob을 보관 계층으로 다시 이동하는 시나리오가 발생할 수 있습니다. 복사 작업을 수행하면 원본 Blob이 보관 계층에 유지되고, 다른 이름 및 마지막 수정 시간을 사용하여 새 Blob이 만들어지므로 디하이드레이션된 Blob이 수명 주기 정책에 따라 다시 보관 계층으로 이동될 위험이 없습니다.

보관 계층에서 Blob을 복사하는 작업은 선택된 리하이드레이션 우선 순위에 따라 완료하는 데 몇 시간이 걸릴 수 있습니다. 백그라운드에서 Blob 복사 작업은 보관된 원본 Blob을 읽어서 선택한 대상 계층에 새 온라인 Blob을 만듭니다. 리하이드레이션 작업이 완료되기 전에 부모 컨테이너의 Blob을 나열할 때 새 Blob이 표시될 수 있지만, 해당 계층이 보관 계층으로 설정됩니다. 보관 계층의 원본 Blob에서 읽기 작업이 완료되고 Blob의 콘텐츠가 온라인 계층의 새 대상 Blob에 기록될 때까지 데이터를 사용할 수 없습니다. 새 Blob은 독립 복사본이므로 수정하거나 삭제해도 보관 계층의 원본 Blob에는 영향을 주지 않습니다.

Blob을 온라인 계층으로 복사하여 리하이드레이션하는 방법에 대해 알아보려면 [복사 작업을 사용하여 Blob 리하이드레이션](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-with-a-copy-operation)을 참조하세요.

> [!IMPORTANT]
> 리하이드레이션이 완료될 때까지 원본 Blob을 삭제하지 마세요. 원본 Blob이 삭제된 경우 대상 Blob은 복사를 완료하지 못할 수 있습니다. 복사 작업이 완료되면 발생하는 이벤트를 처리하여 원본 Blob을 삭제해도 안전한 시기를 알 수 있습니다. 자세한 내용은 [Blob 리하이드레이션에서 이벤트 처리](#handle-an-event-on-blob-rehydration)를 참조하세요.

온라인 대상 계층에 보관된 Blob을 복사하는 것은 동일한 스토리지 계정 내에서만 지원됩니다. 보관된 Blob을 보관 계층에도 있는 대상 Blob에 복사할 수는 없습니다.

다음 표에서는 원본 및 대상 Blob의 계층에 따른 Blob 복사 작업의 동작을 보여 줍니다.

|  | **핫 계층 원본** | **쿨 계층 원본** | **보관 계층 원본** |
|--|--|--|--|
| **핫 계층 대상** | 지원됨 | 지원 여부 | 동일한 계정 내에서 지원됩니다. Blob 리하이드레이션 필요합니다. |
| **쿨 계층 대상** | 지원됨 | 지원 여부 | 동일한 계정 내에서 지원됩니다. Blob 리하이드레이션 필요합니다. |
| **보관 계층 대상** | 지원됨 | 지원됨 | 지원되지 않음 |

## <a name="change-a-blobs-access-tier-to-an-online-tier"></a>Blob의 액세스 계층을 온라인 계층으로 변경

보관 계층에서 온라인 계층으로 Blob을 리하이드레이션하는 두 번째 옵션은 [Blob 계층 설정](/rest/api/storageservices/set-blob-tier)을 호출하여 Blob의 계층을 변경하는 것입니다. 이 작업을 사용하면 보관된 Blob의 계층을 핫 또는 쿨 계층 중 하나로 변경할 수 있습니다.

[Blob 계층 설정](/rest/api/storageservices/set-blob-tier) 요청이 시작되면 취소할 수 없습니다. 리하이드레이션 작업을 수행하는 동안 Blob의 액세스 계층 설정은 리하이드레이션 프로세스가 완료될 때까지 보관된 상태로 계속 표시됩니다. 리하이드레이션 작업이 완료되면 Blob의 액세스 계층 속성이 새 계층을 반영하도록 업데이트됩니다.

계층을 온라인 계층으로 변경하여 Blob을 리하이드레이션하는 방법을 알아보려면 [계층을 변경하여 Blob 리하이드레이션](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-by-changing-its-tier)을 참조하세요.

> [!CAUTION]
> Blob의 계층 변경은 마지막 수정 시간에 영향을 주지 않습니다. 스토리지 계정에 적용되는 [수명 주기 관리](storage-lifecycle-management-concepts.md) 정책이 있는 경우 **Blob 계층 설정** 을 사용하여 Blob을 리하이드레이션하면 마지막 수정 시간이 정책에 대해 설정된 임계값을 초과하므로 수명 주기 정책이 리하이드레이션 후에 Blob을 보관 계층으로 다시 이동하는 시나리오가 발생할 수 있습니다.
>
> 이 시나리오를 방지하려면 [보관된 Blob을 온라인 계층에 복사](#copy-an-archived-blob-to-an-online-tier) 섹션에 설명된 대로 복사하여 보관된 Blob을 리하이드레이션합니다. 복사 작업을 수행하면 업데이트된 마지막 수정 시간으로 Blob의 새 인스턴스가 만들어지므로 수명 주기 관리 정책이 트리거되지 않습니다.

## <a name="check-the-status-of-a-blob-rehydration-operation"></a>Blob 리하이드레이션 작업의 상태 확인

Blob 리하이드레이션 작업을 수행하는 동안 [Blob 속성 가져오기](/rest/api/storageservices/get-blob-properties) 작업을 호출하여 상태를 확인할 수 있습니다. 리하이드레이션 작업의 상태를 확인하는 방법을 알아보려면 [리하이드레이션 작업의 상태 확인](archive-rehydrate-to-online-tier.md#check-the-status-of-a-rehydration-operation)을 참조하세요.

## <a name="handle-an-event-on-blob-rehydration"></a>Blob 리하이드레이션에 대한 이벤트 처리

보관된 Blob의 리하이드레이션은 최대 15시간이 걸릴 수 있으며, **Blob 속성 가져오기** 를 반복적으로 폴링하여 리하이드레이션이 완료되었는지 여부를 확인하는 것은 비효율적입니다. [Azure Event Grid](../../event-grid/overview.md)를 사용하여 리하이드레이션이 완료될 때 발생하는 이벤트를 캡처할 경우 더 나은 성능 및 비용 최적화가 구현됩니다.

Azure Event Grid는 Blob을 리하이드레이션하는 데 사용된 작업에 따라 Blob 리하이드레이션에서 다음 두 이벤트 중 하나를 발생시킵니다.

- **Microsoft.Storage.BlobCreated** 이벤트는 Blob이 생성될 때 발생합니다. Blob 리하이드레이션의 컨텍스트에서 이 이벤트는 [Blob 복사](/rest/api/storageservices/copy-blob) 또는 [URL에서 Blob 복사](/rest/api/storageservices/copy-blob-from-url) 작업으로 인해 핫 또는 쿨 계층에 새 대상 Blob이 만들어지고 Blob의 데이터가 보관 계층에서 완전히 리하이드레이션될 때 발생합니다.
- **Microsoft.Storage.BlobTierChanged** 이벤트는 Blob 계층이 변경될 때 발생합니다. Blob 리하이드레이션 컨텍스트에서 이 이벤트는 [Blob 계층 설정](/rest/api/storageservices/set-blob-tier) 작업으로 인해 보관된 Blob의 계층이 핫 또는 쿨 계층으로 변경될 때 발생합니다.

리하이드레이션에서 이벤트를 캡처하고 Azure 함수 이벤트 처리기로 보내는 방법을 알아보려면 [Blob 리하이드레이션 이벤트에 대한 응답으로 Azure 함수 실행](archive-rehydrate-handle-event.md)을 참조하세요.

Blob Storage에서 이벤트를 처리하는 방법에 대한 자세한 내용은 [Azure Blob Storage 이벤트에 대응](storage-blob-event-overview.md) 및 [Event Grid 원본으로서의 Azure Blob Storage](../../event-grid/event-schema-blob-storage.md)를 참조하세요.

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

[Blob 계층 설정](/rest/api/storageservices/set-blob-tier)을 사용한 리하이드레이션 작업은 데이터 읽기 트랜잭션과 데이터 검색 크기에 따라 요금이 청구됩니다. 높은 우선 순위 리하이드레이션를 사용하는 경우 표준 우선 순위보다 작업 및 데이터 검색 비용이 더 높습니다. 높은 우선 순위 리하이드레이션은 청구서에 별도의 항목으로 표시됩니다. 몇 기가바이트의 보관 Blob을 반환하는 높은 우선 순위 요청이 5시간 이상 걸리는 경우 높은 우선 순위 검색 요금이 부과되지는 않습니다. 그렇지만 표준 검색 요금이 계속 적용됩니다.

[Blob 복사](/rest/api/storageservices/copy-blob) 또는 [URL에서 Blob 복사](/rest/api/storageservices/copy-blob-from-url)를 사용하여 보관된 Blob을 온라인 계층으로 복사하면 데이터 읽기 트랜잭션과 데이터 검색 크기에 따라 요금이 청구됩니다. 온라인 계층에서 대상 Blob을 만드는 경우 데이터 쓰기 트랜잭션에 대해 요금이 청구됩니다. 원본 Blob이 보관 계층에서 수정되지 않은 상태로 유지되기 때문에 온라인 Blob에 복사하는 경우 조기 삭제 요금은 적용되지 않습니다. 선택한 경우 높은 우선 순위 검색 요금은 적용됩니다.

보관 계층의 Blob은 최소 180일 동안 저장해야 합니다. 180일이 경과하기 전에 보관된 Blob의 계층을 삭제하거나 변경하면 초기 삭제 요금이 발생합니다. 자세한 내용은 [보관 액세스 계층](storage-blob-storage-tiers.md#archive-access-tier)을 참조하세요.

블록 Blob 및 데이터 리하이드레이션에 대한 가격 책정에 대한 자세한 내용은 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/)을 참조하세요. 아웃바운드 데이터 전송 요금에 대한 자세한 내용은 [데이터 전송 가격 책정 정보](https://azure.microsoft.com/pricing/details/data-transfers/)를 참조하세요.

## <a name="see-also"></a>참고 항목

- [Azure Blob Storage: 핫, 쿨 및 보관 스토리지 계층](storage-blob-storage-tiers.md)
- [보관된 Blob을 온라인 계층으로 리하이드레이션](archive-rehydrate-to-online-tier.md)
- [Blob 리하이드레이션 이벤트에 대한 응답으로 Azure 함수 실행](archive-rehydrate-handle-event.md)
- [Blob Storage 이벤트에 응답](storage-blob-event-overview.md)