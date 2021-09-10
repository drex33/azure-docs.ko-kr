---
title: 연결되지 않은 Azure 디스크 식별 - Azure Portal
description: Azure Portal을 사용하여 연결되지 않은 Azure 관리 및 비관리(VHD/페이지 Blob) 디스크를 찾는 방법입니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 14ff8552b2e5244a9820f575a397ecc506f24447
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689457"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks---azure-portal"></a>연결되지 않은 Azure 관리 및 비관리 디스크 찾기 및 삭제 - Azure Portal

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

Azure에서 VM(가상 머신)을 삭제할 때 기본적으로 VM에 연결된 디스크는 삭제되지 않습니다. 이는 의도하지 않은 VM 삭제로 인한 데이터 손실을 방지하는 데 도움이 됩니다. VM을 삭제한 후에도 연결되지 않은 디스크에 대한 요금을 계속 지불합니다. 이 문서에서는 Azure Portal에서 연결되지 않은 디스크를 찾아서 삭제하여 불필요한 비용을 줄이는 방법을 보여줍니다. 삭제는 영구적이며 디스크를 삭제한 후에는 데이터를 복구할 수 없습니다.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>관리 디스크: 연결되지 않은 디스크 찾기 및 삭제

연결되지 않은 관리 디스크가 있고 해당 디스크의 데이터가 더 이상 필요하지 않은 경우 다음 프로세스에 따라 Azure Portal에서 이들을 찾을 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **디스크** 를 검색하고 선택합니다.

    **디스크** 블레이드에 모든 디스크의 목록이 표시됩니다. **소유자** 열에 " **-** "가 있는 디스크는 연결되지 않은 디스크입니다.

    [![관리 디스크 블레이드의 스크린샷(디스크의 소유자 열에 -가 있으면 연결되지 않은 디스크임).](media/disks-find-unattached-portal/managed-disk-unattached-owner.png)](media/disks-find-unattached-portal/managed-disk-owner-unattached.png#lightbox)

1. 삭제할 연결되지 않은 디스크를 선택하면 해당 디스크의 블레이드가 열립니다.
1. 디스크의 블레이드에서 디스크 상태가 연결되지 않음인지 확인하고 **삭제** 를 선택합니다.

    :::image type="content" source="media/disks-find-unattached-portal/delete-managed-disk-unattached.png" alt-text="개별 관리 디스크 블레이드의 스크린샷 연결되지 않은 디스크는 이 블레이드에 연결되지 않음 상태로 표시됩니다. 해당 데이터를 더 이상 유지하지 않아도 되는 경우 이 디스크를 삭제할 수 있습니다.":::

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>비관리 디스크: 연결되지 않은 디스크 찾기 및 삭제

비관리 디스크는 [Azure Storage 계정](../storage/common/storage-account-overview.md)에 [페이지 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs)으로 저장된 VHD 파일입니다.

VM에 연결되지 않은 비관리 디스크가 있으며 해당 디스크의 데이터가 더 이상 필요하지 않아 디스크를 삭제하려는 경우 다음 프로세스에 따라 Azure Portal에서 이 작업을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **디스크(클래식)** 를 검색하고 선택합니다.

    모든 비관리 디스크의 목록이 표시됩니다. **다음에 연결됨** 열에 " **-** "가 있는 디스크는 연결되지 않은 디스크입니다.

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-attached-to.png" alt-text="비관리 디스크 블레이드의 스크린샷 이 블레이드에서 다음에 연결됨 열에 -가 있는 디스크는 연결되지 않은 디스크입니다.":::

1. 삭제할 연결되지 않은 디스크를 선택하면 해당 디스크의 블레이드가 열립니다.

1. 디스크의 블레이드에서 **다음에 연결됨** 이 여전히 **-** 이기 때문에 연결되지 않았음을 확인할 수 있습니다.

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-select-blade.png" alt-text="개별 비관리 디스크 블레이드의 스크린샷 연결되지 않은 경우 다음에 연결됨 값이 -입니다. 이 디스크 데이터가 더 이상 필요하지 않은 경우 삭제할 수 있습니다.":::

1. **삭제** 를 선택합니다.

    :::image type="content" source="media/disks-find-unattached-portal/delete-unmanaged-disk-unattached.png" alt-text="삭제가 강조 표시된 개별 비관리 디스크 블레이드의 스크린샷":::

## <a name="next-steps"></a>다음 단계

연결되지 않은 스토리지 계정을 자동으로 찾아서 삭제하려면 [CLI](linux/find-unattached-disks.md) 또는 [PowerShell](windows/find-unattached-disks.md) 문서를 참조하세요.

자세한 내용은 [스토리지 계정 삭제](../storage/common/storage-account-create.md#delete-a-storage-account) 및 [PowerShell을 사용하여 분리된 디스크 확인](/archive/blogs/ukplatforms/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell)을 참조하세요.
