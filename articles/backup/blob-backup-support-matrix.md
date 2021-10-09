---
title: Azure Blob 백업의 지원 매트릭스
description: Azure Blob을 백업할 경우의 지원 설정 및 제한 사항에 대한 요약입니다.
ms.topic: conceptual
ms.date: 10/07/2021
ms.custom: references_regions
ms.openlocfilehash: d0b524ba2865aa72f6fce88e326fffde435f71c3
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714157"
---
# <a name="support-matrix-for-azure-blobs-backup"></a>Azure Blob 백업의 지원 매트릭스

이 문서에서는 지역 가용성, 지원되는 시나리오 및 BLOB의 운영 백업에 대한 제한 사항을 요약합니다.

## <a name="supported-regions"></a>지원되는 지역

Blob에 대 한 운영 백업은 프랑스 남부, 남아프리카 공화국 서 부를 제외한 모든 공용 클라우드 지역에서 사용할 수 있습니다. 소 버린 클라우드 지역 (모든 Azure Government 지역 및 중국 지역 (중국 동부 제외) 에서도 사용할 수 있습니다.

## <a name="limitations"></a>제한 사항

Blob의 운영 백업은 Blob 특정 시점 복원, Blob 버전 관리, Blob에 대한 일시 삭제, Blob에 대한 변경 피드 및 로컬 백업 솔루션을 제공하는 잠금 삭제를 사용합니다. 따라서 이러한 기능에 적용되는 제한 사항은 운영 백업에도 적용됩니다.

**지원되는 시나리오:** 운영 백업은 표준 범용 v2 스토리지 계정에서만 블록 Blob을 지원합니다. 계층적 네임 스페이스를 사용 하는 Storage 계정 (ADLS Gen2 계정)은 지원 되지 않습니다.   <br><br>   또한 스토리지 계정에 있는 모든 페이지 Blob, 추가 Blob 및 프리미엄 Blob은 복원되지 않으며 블록 Blob만 복원됩니다.

**기타 제한 사항:**

- 보존 기간에 컨테이너를 삭제한 경우 해당 컨테이너는 특정 시점 복원 작업으로 복원되지 않습니다. 삭제된 컨테이너에서 Blob을 포함하는 Blob 범위를 복원하려고 하면 특정 시점 복원 작업이 실패합니다. 컨테이너를 삭제하지 못하도록 보호하는 방법에 대한 자세한 내용은 [컨테이너의 일시 삭제(미리 보기)](../storage/blobs/soft-delete-container-overview.md)를 참조하세요.
- Blob이 현재 순간과 복원 지점 사이에서 핫 및 쿨 계층 사이를 이동하면 Blob이 이전 계층으로 복원됩니다. 보관 계층에서는 블록 Blob의 복원이 지원되지 않습니다. 예를 들어, 핫 계층의 Blob을 2일 전에 보관 계층으로 이동하고 복원 작업을 3일 전 지점으로 복원하면 Blob이 핫 계층으로 복원되지 않습니다. 보관된 Blob을 복원하려면 먼저 보관 계층 외부로 이동합니다. 자세한 내용은 [보관 계층에서 BLOB 데이터 리하이드레이션](../storage/blobs/archive-rehydrate-overview.md)을 참조하세요.
- [Put Block](/rest/api/storageservices/put-block) 또는 [Put Block from URL](/rest/api/storageservices/put-block-from-url)을 통해 업로드되었지만 [Put Block List](/rest/api/storageservices/put-block-list)를 통해 커밋되지 않은 블록은 Blob에 속하지 않으므로 복원 작업의 일부로 복원되지 않습니다.
- 활성 임대가 있는 BLOB은 복원할 수 없습니다. 활성 임대가 있는 BLOB이 복원할 BLOB 범위에 포함된 경우 복원 작업이 자동으로 실패합니다. 복원 작업을 시작하기 전에 활성 임대를 중단합니다.
- 스냅샷은 복원 작업의 일부로 생성되거나 삭제되지 않습니다. 기본 BLOB만 이전 상태로 복원됩니다.
- 복원 중인 항목 사이에 [변경할 수 없는 Blob](../storage/blobs/immutable-storage-overview.md#about-immutable-storage-for-blobs)이 있는 경우 이러한 변경할 수 없는 Blob은 선택한 복구 지점에 따라 해당 상태로 복원되지 않습니다. 그러나 불변성을 사용하지 않는 다른 Blob은 예상대로 선택한 복구 지점으로 복원됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Blob에 대한 운영 백업 개요](blob-backup-overview.md)