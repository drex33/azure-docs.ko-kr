---
title: Azure Storage Explorer 일시 삭제 가이드 | Microsoft Docs
description: Azure Storage Explorer에서 일시 삭제
services: storage
author: JasonYeMSFT
ms.service: storage
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: chuye
ms.openlocfilehash: 8a54d02d434ba9a0552668e24022108d3ad561b9
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452750"
---
# <a name="azure-storage-explorer-soft-delete-guide"></a>Azure Storage Explorer 일시 삭제 가이드

일시 삭제는 중요 한 데이터를 실수로 삭제 하는 경우의 영향을 줄이는 데 도움이 됩니다. 이 가이드는 Storage Explorer에서이 기능을 활용할 수 있는 방법을 이해 하는 데 도움이 됩니다. 계속 하기 전에 [blob 일시 삭제](../blobs/soft-delete-blob-overview.md)에 대 한 자세한 내용을 확인 하는 것이 좋습니다.

## <a name="configuring-delete-retention-policy"></a>삭제 보존 정책 구성

Storage Explorer에서 각 저장소 계정에 대 한 삭제 보존 정책을 구성할 수 있습니다. 저장소 계정에서 모든 "Blob 컨테이너" 노드에 대 한 상황에 맞는 메뉴를 열고 **일시 삭제 정책 구성**...을 선택 합니다. 

새 정책 설정을 적용 하는 데 최대 30 초까지 걸릴 수 있습니다. 새 정책이 적용 될 때까지 기다리지 않고 데이터를 삭제 하면 예기치 않은 동작이 발생할 수 있습니다. 활동 로그에서 성공적으로 구성 된 정책을 보고 하기 전까지 30 초 동안 대기 Storage Explorer.

## <a name="soft-delete-with-hierarchical-namespace-enabled"></a>계층적 네임 스페이스를 사용 하는 일시 삭제

일시 삭제 기능은 계층적 네임 스페이스 (HNS)를 사용 하거나 사용 하지 않는 blob 컨테이너 간에 근본적인 차이가 있습니다.

HNS를 사용 하는 blob 컨테이너에는 실제 디렉터리가 있습니다. 이러한 디렉터리는 일시 삭제 될 수도 있습니다. 실제 디렉터리가 일시 삭제 되 면 그 아래의 모든 활성 blob 또는 디렉터리에 액세스할 수 없게 됩니다. 이러한 blob 및 디렉터리는 디렉터리가 삭제 되 고 디렉터리가 만료 될 때 삭제 될 때 복구 됩니다. 이미 일시 삭제 된 blob 또는 blob의 디렉터리는 그대로 유지 됩니다.

HNS를 사용 하지 않는 blob 컨테이너는 이름이 같은 일시 삭제 된 blob 및 활성 blob을 허용 하지 않습니다. 일시 삭제 된 blob과 이름이 같은 blob을 업로드 하면 일시 삭제 된 blob이 새 blob의 스냅숏이 됩니다. HNS를 사용 하는 blob 컨테이너에서 동일한 작업을 수행 하면 일시 삭제 된 blob이 새 blob와 함께 사용 됩니다. 또한 HNS를 사용 하는 blob 컨테이너는 동일한 이름을 가진 여러 개의 일시 삭제 된 blob을 함께 사용할 수 있습니다.

HNS를 사용 하는 blob 컨테이너에서 일시 삭제 된 각 blob 또는 디렉터리에는 `DeletionID` 속성이 있습니다. 이 속성은 동일한 이름의 blob 또는 디렉터리를 구분 합니다. 비 HNS 사용 blob 컨테이너의 일시 삭제 된 blob에는 속성이 없습니다 `DeletionID` .

또한 HNS를 사용 하지 않는 blob 컨테이너는 "blob 버전 관리"를 지원 합니다. Blob 버전 관리를 사용 하도록 설정 하면 delete와 같은 특정 작업의 동작이 변경 됩니다. 자세한 내용은 [Azure Storage Explorer blob 버전 관리 가이드](./storage-explorer-blob-versioning.md)를 참조 하세요.

## <a name="view-soft-deleted-blobs"></a>일시 삭제 된 blob 보기

Blob 탐색기에서 일시 삭제 된 blob은 특정 뷰 컨텍스트에서 표시 됩니다.

HNS를 사용 하지 않는 blob 컨테이너의 경우 일시 삭제 된 blob을 보려면 "활성 blob 및 일시 삭제 된 blob" 또는 "현재 버전이 없는 모든 blob 및 blob" 보기 컨텍스트를 선택 합니다.

HNS를 사용 하는 blob 컨테이너의 경우 "활성 및 일시 삭제 된 blob" 또는 "삭제 된 항목만" 뷰 컨텍스트를 선택 하 여 일시 삭제 된 blob 및 디렉터리를 확인 합니다.

## <a name="delete-blobs"></a>Blob 삭제

blob 또는 디렉터리를 삭제 하는 경우 Storage Explorer는 저장소 계정의 현재 삭제 보존 정책을 확인 합니다. 그러면 삭제 작업을 계속 하는 경우 확인 대화 상자가 표시 됩니다. 일시 삭제를 사용 하지 않도록 설정한 경우 **일시 삭제 사용** 단추를 선택 하 여 확인 대화 상자에서 일시 삭제를 사용 하도록 설정할 수 있습니다.

> [!WARNING]
> 방금 저장 한 경우에는 새 삭제 보존 정책 Storage Explorer 표시 되지 않을 수 있습니다. 새 정책이 적용 될 때까지 30 초 이상 기다렸다가 데이터를 삭제 하는 것이 좋습니다.

## <a name="undelete-blobs"></a>Blob 삭제 취소

일시 삭제 된 blob을 일괄 처리에서 반복적으로 삭제 취소할 수 Storage Explorer.

Blob의 삭제를 취소 하려면 삭제 취소할 일시 삭제 된 blob을 선택 하 고 도구 모음 또는 상황에 맞는 메뉴에서 **선택 취소 → 삭제** 취소를 사용 합니다.

디렉터리에서 재귀적으로 blob의 삭제를 취소할 수도 있습니다. 선택 영역에 active directory가 포함 되어 있는 경우에는 Storage Explorer에서 일시 삭제 된 모든 blob 또는 디렉터리를 삭제 취소 합니다.

HNS를 사용 하도록 설정 된 blob 컨테이너에서 이름이 같은 활성 blob이 이미 있는 경우 blob a 삭제 취소가 실패 합니다.

> [!Note]
> 일시 삭제 된 스냅숏은 기본 blob을 삭제 취소 하 여 삭제 취소할 수 있습니다. 개별 스냅숏의 삭제를 취소할 수 있는 방법은 없습니다.

## <a name="undelete-blobs-by-date-range"></a>날짜 범위로 blob 삭제 취소

Storage Explorer를 사용 하 여 삭제 시간을 기준으로 blob의 삭제를 취소할 수도 있습니다.

날짜 범위로 blob의 삭제를 취소 하려면 삭제 취소할 일시 삭제 된 blob을 선택 하 고 도구 모음 또는 상황에 맞는 메뉴에서 삭제 **취소 → 날짜순으로** 삭제 취소를 사용 합니다.

**날짜별 삭제 취소** ... 삭제 시간이 지정 된 범위를 벗어난 blob 또는 디렉터리를 필터링 한다는 점을 제외 하 고는 **선택 취소** 와 정확히 동일 하 게 작동 합니다.

## <a name="see-also"></a>참고 항목

* [Azure Storage Explorer Blob 버전 관리 가이드](./storage-explorer-blob-versioning.md)
* [Blob에 대한 일시 삭제](../blobs/soft-delete-blob-overview.md)