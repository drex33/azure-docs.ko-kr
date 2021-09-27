---
title: Azure Storage Explorer 일시 삭제 가이드 | Microsoft Docs
description: Azure Storage Explorer 일시 삭제
services: storage
author: JasonYeMSFT
ms.service: storage
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: chuye
ms.openlocfilehash: 0d097e769bfea8ff9c65921e8f0d851372187a02
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128564907"
---
# <a name="azure-storage-explorer-soft-delete-guide"></a>Azure Storage Explorer 일시 삭제 가이드

일시 삭제는 실수로 중요한 데이터를 삭제하는 영향을 완화하는 데 도움이 됩니다. 이 가이드는 Storage Explorer 이 기능을 활용하는 방법을 이해하는 데 도움이 됩니다. 계속하기 전에 [Blob 일시 삭제](../blobs/soft-delete-blob-overview.md)에 대해 자세히 읽어보는 것이 좋습니다.

## <a name="configuring-delete-retention-policy"></a>삭제 보존 정책 구성

Storage Explorer 각 스토리지 계정에 대해 삭제 보존 정책을 구성할 수 있습니다. 스토리지 계정 아래의 "Blob 컨테이너" 노드에 대한 상황에 맞는 메뉴를 열고 **일시 삭제 정책 구성...** 을 선택합니다.

새 정책을 설정하는 데 최대 30초가 걸릴 수 있습니다. 새 정책이 적용될 때까지 기다리지 않고 데이터를 삭제하면 예기치 않은 동작이 발생할 수 있습니다. Storage Explorer 활동 로그에서 성공적으로 구성된 정책을 보고하기 전에 30초 동안 기다립니다.

## <a name="soft-delete-with-hierarchical-namespace-enabled"></a>계층 구조 네임스페이스를 사용하도록 설정된 일시 삭제

일시 삭제 기능은 HNS(계층 구조 네임스페이스)를 사용하거나 사용하지 않는 Blob 컨테이너 간에 기본적인 차이점이 있습니다.

HNS 사용 Blob 컨테이너에는 실제 디렉터리 가 있습니다. 이러한 디렉터리도 일시 삭제할 수 있습니다. 실제 디렉터리를 일시 삭제하면 해당 디렉터리 아래에 있는 모든 활성 Blob 또는 디렉터리에 액세스할 수 없게 됩니다. 디렉터리를 삭제하지 않으면 이러한 Blob 및 디렉터리도 복구되고 디렉터리 만료 시 삭제됩니다. 이미 일시 삭제된 Blob 또는 그 아래의 디렉터리도 그대로 유지됩니다.

비 HNS 사용 Blob 컨테이너는 동일한 이름의 일시 삭제된 Blob 및 활성 Blob이 공존하도록 허용하지 않습니다. 일시 삭제된 Blob과 동일한 이름의 Blob을 업로드하면 일시 삭제된 Blob이 새 Blob의 스냅샷이 됩니다. HNS 사용 Blob 컨테이너에서 동일한 작업을 수행하면 일시 삭제된 Blob이 새 Blob과 공존하게 됩니다. 또한 HNS 사용 Blob 컨테이너를 사용하면 동일한 이름의 여러 일시 삭제된 Blob이 공존할 수 있습니다.

HNS 사용 Blob 컨테이너의 각 일시 삭제된 Blob 또는 디렉터리에는 `DeletionID` 속성이 있습니다. 이 속성은 동일한 이름의 Blob 또는 디렉터리를 구분합니다. HNS를 사용하지 않는 Blob 컨테이너의 일시 삭제된 Blob에는 `DeletionID` 속성이 없습니다.

비 HNS 사용 Blob 컨테이너는 "Blob 버전"도 지원합니다. Blob 버전이 사용하도록 설정된 경우 삭제와 같은 특정 작업의 동작이 변경됩니다. 자세한 내용은 [blob 버전 Azure Storage Explorer 가이드를 참조하세요.](./storage-explorer-blob-versioning.md)

## <a name="view-soft-deleted-blobs"></a>일시 삭제된 Blob 보기

Blob 탐색기에서 일시 삭제된 Blob은 특정 보기 컨텍스트 아래에 표시됩니다.

HNS를 사용하지 않는 Blob 컨테이너의 경우 "활성 Blob 및 일시 삭제된 Blob" 또는 "현재 버전이 없는 모든 Blob 및 Blob" 보기 컨텍스트를 선택하여 일시 삭제된 Blob을 봅니다.

HNS를 사용하는 Blob 컨테이너의 경우 "활성 및 일시 삭제된 Blob" 또는 "삭제된 전용" 보기 컨텍스트를 선택하여 일시 삭제된 Blob 및 디렉터리를 봅니다.

## <a name="delete-blobs"></a>Blob 삭제

Blob 또는 디렉터리를 삭제할 때 Storage Explorer 스토리지 계정의 현재 삭제 보존 정책을 확인합니다. 그러면 삭제 작업을 계속 진행하면 어떻게 되는지 확인하는 대화 상자가 표시됩니다. 일시 삭제를 사용하지 않도록 설정한 경우 일시 삭제 사용 단추를 선택하여 확인 대화 상자에서 일시 **삭제를 사용하도록** 설정할 수 있습니다.

> [!WARNING]
> 방금 저장한 경우 Storage Explorer 새 삭제 보존 정책이 표시되지 않을 수 있습니다. 데이터를 삭제하기 전에 새 정책이 적용될 때까지 30초 이상 기다리는 것이 좋습니다.

## <a name="undelete-blobs"></a>Blob의 언게팅

Storage Explorer 일시 삭제된 Blob을 재귀적으로 일괄 처리로 삭제할 수 있습니다.

Blob 삭제를 제거하려면 삭제를 제거하려는 일시 삭제된 Blob을 선택하고 도구 모음 또는 상황에 맞는 메뉴에서 삭제 → **삭제 제거 선택 안 을** 사용합니다.

디렉터리에서 Blob을 재귀적으로 지울 수도 있습니다. Active Directory가 선택 항목에 포함된 경우 Storage Explorer 일시 삭제된 Blob 또는 디렉터리를 모두 삭제하지 않습니다.

HNS 사용 Blob 컨테이너에서 동일한 이름의 활성 Blob이 이미 있는 경우 Blob 삭제 취소가 실패합니다.

> [!NOTE]
> 일시 삭제된 스냅샷은 기본 Blob을 삭제 취소해야만 삭제할 수 있습니다. 개별 스냅샷을 지체할 수 있는 방법은 없습니다.

## <a name="undelete-blobs-by-date-range"></a>날짜 범위별 Blob의 언셀리화

Storage Explorer 삭제 시간에 따라 Blob 삭제를 해제할 수도 있습니다.

날짜 범위별로 Blob 삭제를 제거하려면 삭제를 제거하려는 일시 삭제된 Blob을 선택하고 도구 모음 또는 상황에 맞는 메뉴에서 삭제되지 **않은 → 날짜별 삭제 제거...를** 사용합니다.

**날짜별 Undelete...** 삭제 시간이 지정한 범위를 벗어난 Blob 또는 디렉터리를 필터링한다는 점을 제외하고 **선택한** 삭제 해제와 정확히 동일하게 작동합니다.

## <a name="see-also"></a>참고 항목

- [Azure Storage Explorer Blob 버전 관리 가이드](./storage-explorer-blob-versioning.md)
- [Blob에 대한 일시 삭제](../blobs/soft-delete-blob-overview.md)
