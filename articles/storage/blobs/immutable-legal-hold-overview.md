---
title: 변경이 불가능한 Blob 데이터에 대한 법적 보존
titleSuffix: Azure Storage
description: 법적 보존은 명시적으로 지울 때까지 WORM(Write-Once, Read-Many) 형식으로 BLOB 데이터를 저장합니다. 데이터를 WORM 상태로 유지해야 하는 기간을 알 수 없는 경우 법적 보존을 사용합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/22/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 1434f689f8f629fd04ec0c0b059a1298b32d5ac9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537002"
---
# <a name="legal-holds-for-immutable-blob-data"></a>변경이 불가능한 Blob 데이터에 대한 법적 보존

법적 보존은 법적 조사를 위해 또는 일반 보호 정책에 적용할 수 있는 임시 불변성 정책입니다. 법적 보존은 명시적으로 지울 때까지 WORM(Write-Once, Read-Many) 형식으로 BLOB 데이터를 저장합니다. 법적 보존을 적용하면 BLOB를 만들고 읽을 수 있지만, 수정하거나 삭제할 수는 없습니다. 데이터를 WORM 상태로 유지해야 하는 기간을 알 수 없는 경우 법적 보존을 사용합니다.

Blob Storage 불변성 정책에 대한 자세한 내용은 [비즈니스에 중요한 BLOB 데이터를 변경이 불가능한 스토리지에 저장](immutable-storage-overview.md)을 참조하세요.

## <a name="legal-hold-scope"></a>법적 보존 범위

법적 보존 정책은 다음 범위 중 하나에서 구성할 수 있습니다.

- 버전 수준 정책(미리 보기): 중요한 데이터의 세부적 관리를 위해 개별 BLOB 버전 수준에 대해 법적 보존을 구성할 수 있습니다.
- 컨테이너 수준 정책: 컨테이너 수준에서 구성된 법적 보존은 해당 컨테이너의 모든 BLOB에 적용됩니다. 개별 BLOB은 자체 불변성 정책으로 구성할 수 없습니다.

### <a name="version-level-policy-scope-preview"></a>버전 수준 정책 범위(미리 보기)

BLOB 버전에서 법적 보존을 구성하려면 먼저 부모 컨테이너에서 버전 수준 불변성을 사용하도록 설정해야 합니다. 버전 수준 불변성을 사용하도록 설정한 후에는 사용하지 않도록 설정할 수 없습니다. 자세한 내용은 [컨테이너에서 버전 수준 불변성 지원 사용](immutable-policy-configure-version-scope.md#enable-support-for-version-level-immutability-on-a-container)을 참조하세요.

컨테이너에 대해 버전 수준 불변성을 사용하도록 설정한 후에는 더 이상 컨테이너 수준에서 법적 보존을 설정할 수 없습니다. 법적 보존은 개별 BLOB 버전에 적용되어야 합니다. BLOB의 최신 버전 또는 이전 버전에 대해 법적 보존을 구성할 수 있습니다.

버전 수준 법적 보존 정책에서는 스토리지 계정에 대해 BLOB 버전 관리를 사용하도록 요구합니다. Blob 버전 관리를 사용하도록 설정하는 방법을 알아보려면 [Blob 버전 관리 사용 및 관리](versioning-enable.md)를 참조하세요. 버전 관리를 사용하도록 설정하면 청구에 영향을 줄 수 있습니다. 자세한 내용은 [Blob 버전 관리](versioning-overview.md#pricing-and-billing)의 **가격 책정 및 청구** 섹션을 참조하세요.

버전 수준 법적 보존을 사용하도록 설정하는 방법에 대한 자세한 내용은 [법적 보존 구성 또는 선택 취소](immutable-policy-configure-version-scope.md#configure-or-clear-a-legal-hold)를 참조하세요.

### <a name="container-level-scope"></a>컨테이너 수준 범위

컨테이너에 대해 법적 보존을 구성하는 경우 해당 컨테이너의 모든 개체에 적용됩니다. 법적 보존을 선택 취소할 경우, 컨테이너에 적용되는 시간 기반 보존 정책이 있지 않은 한 클라이언트가 컨테이너에서 개체를 다시 작성하여 삭제할 수 있습니다.

컨테이너에 법적 보존이 적용되면 모든 기존 BLOB는 30초 이내에 변경할 수 없는 WORM 상태로 전환됩니다. 해당 정책으로 보호되는 컨테이너에 업로드되는 모든 새 Blob도 변경이 불가능한 상태로 이동합니다. 모든 Blob이 변경이 불가능한 상태가 되면 변경이 불가능한 컨테이너에서 덮어쓰기 또는 삭제 작업이 허용되지 않습니다. 계층 구조 네임스페이스가 있는 계정의 경우 Blob의 이름을 바꾸거나 Blob을 다른 디렉터리로 이동할 수 없습니다.

컨테이너 수준 범위에서 법적 보존을 구성하는 방법을 알아보려면 [법적 보존 구성 또는 선택 취소](immutable-policy-configure-container-scope.md#configure-or-clear-a-legal-hold)를 참조하세요.

#### <a name="legal-hold-tags"></a>법적 보존 태그

컨테이너 수준 법적 보존은 식별자 문자열 역할을 하는 하나 이상의 사용자 정의 영숫자 태그와 연결되어야 합니다. 예를 들어, 태그에는 사례 ID 또는 이벤트 이름이 포함될 수 있습니다.

#### <a name="audit-logging"></a>감사 로깅

법적 보존이 적용된 각각의 컨테이너는 정책 감사 로그를 제공합니다.  로그에는 사용자 ID, 명령 유형, 타임스탬프 및 법적 보존 태그가 포함됩니다. 감사 로그는 SEC 17a-4(f) 규정 지침에 따라 정책의 수명 동안 유지됩니다.

[Azure 활동 로그](../../azure-monitor/essentials/platform-logs-overview.md)는 모든 관리 서비스 활동에 대한 더 포괄적인 로그를 제공합니다. [Azure 리소스 로그](../../azure-monitor/essentials/platform-logs-overview.md)는 데이터 작업에 대한 정보를 유지합니다. 이러한 로그는 규정 또는 다른 목적으로 필요할 수 있으므로 사용자가 로그를 지속적으로 저장할 책임이 있습니다.

#### <a name="limits"></a>제한

컨테이너 수준 법적 보존에는 다음 제한이 적용됩니다.

- 스토리지 계정의 경우 법적 보존이 설정된 컨테이너의 최대 수는 10,000개입니다.
- 컨테이너의 경우 법적 보존 태그의 최대 수는 10개입니다.
- 법적 보존 태그의 최소 길이는 세 개의 영숫자입니다. 최대 길이는 23자의 영숫자입니다.
- 컨테이너의 경우 정책 기간 동안 최대 10개의 법적 보존 정책 감사 로그가 보존됩니다.

## <a name="next-steps"></a>다음 단계

- [데이터 보호 개요](data-protection-overview.md)
- [비즈니스에 중요한 BLOB 데이터를 변경이 불가능한 스토리지에 저장](immutable-storage-overview.md)
- [변경이 불가능한 BLOB 데이터에 대한 시간 기반 보존 정책](immutable-time-based-retention-policy-overview.md)
- [BLOB 버전에 대한 불변성 정책 구성(미리 보기)](immutable-policy-configure-version-scope.md)
- [컨테이너에 대한 불변성 정책 구성](immutable-policy-configure-container-scope.md)
