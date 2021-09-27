---
title: Azure Storage Explorer Blob 버전 관리 가이드 | Microsoft Docs
description: Azure Storage Explorer에 대한 Blob 버전 관리 지침
services: storage
author: JasonYeMSFT
ms.service: storage
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: chuye
ms.openlocfilehash: dfff19eee4c852e336d0d943a9509d286d510347
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626687"
---
# <a name="azure-storage-explorer-blob-versioning-guide"></a>Azure Storage Explorer Blob 버전 관리 가이드

Microsoft Azure Storage Explorer를 사용하면 Blob 버전에 간편하게 액세스하고 버전을 관리할 수 있습니다. 이 가이드는 Storage Explorer에서 Blob 버전 관리가 작동하는 방식을 이해하는 데 도움이 됩니다. 계속하기 전에 [Blob 버전 관리](../blobs/versioning-overview.md)에 대한 자세한 내용을 확인하는 것이 좋습니다.

## <a name="terminology"></a>용어

여기서는 이 문서에서 설명하는 사용법을 이해하는 데 도움이 되는 몇 가지 정의를 제공합니다.

- 일시 삭제: 대체 자동 데이터 보호 기능입니다. [여기서](../blobs/soft-delete-blob-overview.md) 일시 삭제에 대해 자세히 알아볼 수 있습니다.
- 활성 Blob: Blob 또는 Blob 버전이 활성 상태에서 생성되었습니다. 활성 상태의 Blob 또는 Blob 버전에서만 작업할 수 있습니다.
- 일시 삭제된 Blob: 일시 삭제된 것으로 표시된 Blob 또는 Blob 버전입니다. 일시 삭제된 Blob은 보존 기간 동안에만 유지됩니다.
- Blob 버전: Blob 버전 관리를 사용하여 만든 Blob입니다. 각 Blob 버전은 버전 ID와 연결됩니다.
- 현재 버전: 현재 버전으로 표시된 Blob 버전입니다.
- 이전 버전: 현재 버전으로 표시되지 않은 Blob 버전입니다.
- 버전이 없는 Blob: Blob 버전 관리를 사용하지 않도록 설정한 상태에서 만든 Blob입니다. 버전이 없는 Blob에는 버전 ID가 없습니다.

## <a name="view-blob-versions"></a>Blob 버전 보기

Storage Explorer는 Blob을 볼 수 있는 네 가지 보기를 지원합니다.

| 보기 | 활성 버전이 없는 Blob | 일시 삭제된, 버전이 없는 Blob | Blob 버전 |
| ---- | :----------: | :-----------: | :------------------: |
| 활성 Blob | 예 | 예 | 현재 버전만 |
| 활성 Blob 및 일시 삭제된 Blob | 예 | 예 | 현재 버전만 |
| 활성 Blob 및 현재 버전이 없는 Blob | 예 | 예 | 현재 버전 또는 최신 활성 버전 |
| 모든 Blob 및 현재 버전이 없는 Blob | 예 | 예 | 현재 버전 또는 최신 버전 |

### <a name="active-blobs"></a>활성 Blob

이 보기에서 Storage Explorer에는 다음 항목이 표시됩니다.

- 활성 버전이 없는 Blob
- 현재 버전

### <a name="active-blobs-and-soft-deleted-blobs"></a>활성 Blob 및 일시 삭제된 Blob

이 보기에서 Storage Explorer에는 다음 항목이 표시됩니다.

- 활성 버전이 없는 Blob
- 일시 삭제된, 버전이 없는 Blob
- 현재 버전

### <a name="active-blobs-and-blobs-without-current-version"></a>활성 Blob 및 현재 버전이 없는 Blob

이 보기에서 Storage Explorer에는 다음 항목이 표시됩니다.

- 활성 버전이 없는 Blob
- 현재 버전
- 최신 활성 이전 버전

현재 버전이 없지만 활성 이전 버전은 있는 Blob의 경우에는 Storage Explorer에 최신 활성 이전 버전이 해당 Blob의 표현으로 표시됩니다.

### <a name="all-blobs-and-blobs-without-current-version"></a>모든 Blob 및 현재 버전이 없는 Blob

이 보기에서 Storage Explorer에는 다음 항목이 표시됩니다.

- 활성 버전이 없는 Blob
- 일시 삭제된, 버전이 없는 Blob
- 현재 버전
- 최신 이전 버전

현재 버전이 없는 Blob의 경우에는 Storage Explorer에 최신 이전 버전이 해당 Blob의 표현으로 표시됩니다.

> [!NOTE]
> 서비스 제한으로 인해 Blob 버전을 나열할 때 가상 디렉터리의 계층 보기를 가져오려면 Storage Explorer에서 몇 가지 추가 처리가 필요합니다. 다음 보기에서는 Blob을 나열하는 데 시간이 더 오래 걸립니다.
>
> - 활성 Blob 및 현재 버전이 없는 Blob
> - 모든 Blob 및 현재 버전이 없는 Blob

## <a name="manage-blob-versions"></a>Blob 버전 관리

### <a name="view-versions-of-a-blob"></a>Blob의 버전 보기

Storage Explorer는 모든 버전의 Blob을 볼 수 있는 **버전 관리** 명령을 제공합니다. Blob의 버전을 보려면 버전을 보려는 Blob을 선택하고 도구 모음 또는 바로 가기 메뉴에서 **기록 관리 &rarr; 버전 관리** 를 선택합니다.

### <a name="download-blob-versions"></a>Blob 버전 다운로드

하나 이상의 Blob 버전을 다운로드하려면 다운로드하려는 Blob 버전을 선택하고 도구 모음 또는 바로 가기 메뉴에서 **다운로드** 를 선택합니다.

여러 버전의 Blob을 다운로드하는 경우 다운로드한 파일의 버전 ID가 파일 이름 시작 부분에 표시됩니다.

### <a name="delete-blob-versions"></a>Blob 버전 삭제

하나 이상의 Blob 버전을 삭제하려면 삭제하려는 Blob 버전을 선택하고 도구 모음 또는 바로 가기 메뉴에서 **삭제** 를 선택합니다.

Blob 버전에는 일시 삭제 정책이 적용됩니다. 일시 삭제가 사용하도록 설정된 경우 Blob 버전은 일시 삭제됩니다. 한 가지 특별한 경우는 현재 버전을 삭제하는 것입니다. 현재 버전을 삭제하면 해당 버전이 자동으로 활성 이전 버전이 됩니다.

### <a name="promote-blob-version"></a>Blob 버전 승격

이전 버전을 현재 버전으로 승격시켜 Blob의 내용을 복원할 수 있습니다. 승격할 Blob 버전을 선택하고 도구 모음 또는 바로 가기 메뉴에서 **버전 승격** 을 선택합니다.

버전이 없는 Blob은 승격된 Blob 버전이 덮어씁니다. 작업을 확인하기 전에 해당 데이터가 더 이상 필요하지 않은지 확인하거나 아니면 데이터를 직접 백업하세요. 현재 버전은 자동으로 이전 버전이 되므로 Storage Explorer에서 확인 메시지가 표시되지 않습니다.

### <a name="undelete-blob-version"></a>Blob 버전 삭제 취소

Blob 버전은 개별적으로 삭제 취소할 수 없습니다. 삭제는 한꺼번에 취소해야 합니다. Blob의 모든 Blob 버전을 삭제 취소하려면 Blob의 버전 중 하나를 선택하고 도구 모음 또는 바로 가기 메뉴에서 **선택한 항목 삭제 취소** 를 선택합니다.

### <a name="change-access-tier-of-blob-versions"></a>Blob 버전의 액세스 계층 변경

각 Blob 버전에는 고유한 액세스 계층이 있습니다. Blob 버전의 액세스 계층을 변경하려면 액세스 계층을 변경할 Blob 버전을 선택하고 바로 가기 메뉴에서 **액세스 계층 변경...** 을 선택합니다.

## <a name="see-also"></a>참고 항목

- [Blob 버전 관리](../blobs/versioning-overview.md)
- [Blob에 대한 일시 삭제](../blobs/soft-delete-blob-overview.md)
- [Azure Storage Explorer 일시 삭제 가이드](./storage-explorer-soft-delete.md)
