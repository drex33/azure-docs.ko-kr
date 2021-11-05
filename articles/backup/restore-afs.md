---
title: Azure 파일 공유 복원
description: Azure Portal을 사용하여 Azure Backup을 통해 만든 복원 지점에서 전체 파일 공유 또는 특정 파일을 복원하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 11/03/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 464c9927f901a373310ad6d1c0812a5a5de1eaaa
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846843"
---
# <a name="restore-azure-file-shares"></a>Azure 파일 공유 복원

이 문서에서는 Azure Portal을 사용하여 [Azure Backup](./backup-overview.md)을 통해 만든 복원 지점에서 전체 파일 공유 또는 특정 파일을 복원하는 방법을 설명합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

* 전체 Azure 파일 공유를 복원합니다.
* 개별 파일 또는 폴더를 복원합니다.
* 복원 작업 상태를 추적합니다.

## <a name="steps-to-perform-a-restore-operation"></a>복원 작업을 수행하는 단계

복원 작업을 수행하려면 다음 단계를 사용합니다.

### <a name="select-the-file-share-to-restore"></a>복원할 파일 공유 선택

1. [Azure Portal](https://portal.azure.com/)에서 **Backup center** 로 이동 하 고 **복원** 을 클릭 합니다.

   :::image type="content" source="./media/restore-afs/backup-center-restore-inline.png" alt-text="Azure 파일 공유 복원 프로세스를 시작 하는 것을 보여 주는 스크린샷" lightbox="./media/restore-afs/backup-center-restore-expanded.png":::

1. 데이터 원본 유형으로 **Azure Files (Azure Storage)** 를 선택 하 고 복원 하려는 파일 공유를 선택한 다음 **계속** 을 클릭 합니다.

   :::image type="content" source="./media/restore-afs/azure-file-share-select-instance.png" alt-text="백업 항목을 선택 하는 것을 보여 주는 스크린샷":::

### <a name="full-share-recovery"></a>전체 공유 복구

이 복원 옵션을 사용하여 원래 위치나 대체 위치에서 전체 파일 공유를 복원할 수 있습니다.

1. 이전 단계에서 **계속** 을 선택 하면 **복원** 창이 열립니다. 복원 작업을 수행하는 데 사용할 복원 지점을 선택하려면 **복원 지점** 텍스트 상자 아래에 있는 **선택** 링크 텍스트를 선택합니다.

    ![선택을 선택하여 복원 지점 선택](./media/restore-afs/select-restore-point.png)

1. **복원 지점 선택** 컨텍스트 창이 오른쪽에 열리고 선택한 파일 공유에 사용할 수 있는 복원 지점이 나열됩니다. 복원 작업을 수행하는 데 사용할 복원 지점을 선택한 다음 **확인** 을 선택합니다.

   :::image type="content" source="./media/restore-afs/azure-file-share-select-restore-point-inline.png" alt-text="복원 지점을 선택 하는 것을 보여 주는 스크린샷" lightbox="./media/restore-afs/azure-file-share-select-restore-point-expanded.png":::

    >[!NOTE]
    >기본적으로 **복원 지점 선택** 창에는 지난 30일 동안의 복원 지점이 나열됩니다. 특정 기간 동안 생성된 복원 지점을 확인하려면 적절한 **시작 시간** 및 **종료 시간** 을 선택하여 범위를 지정하고 **새로 고침** 단추를 선택합니다.

1. 다음 단계는 **복원 위치** 를 선택하는 것입니다. **복구 대상** 섹션에서 데이터 복원 여부 또는 방법을 지정합니다. 토글 단추를 사용하여 다음 두 옵션 중 하나를 선택합니다.

    * **원래 위치**: 원래 원본과 동일한 위치에 전체 파일 공유를 복원합니다.
    * **대체 위치**: 대체 위치에 전체 파일 공유를 복원하고 원래 파일 공유를 그대로 유지합니다.

#### <a name="restore-to-the-original-location-full-share-recovery"></a>원래 위치로 복원(전체 공유 복구)

1. **복구 대상** 으로 **원래 위치** 를 선택한 다음, **충돌 시** 드롭다운 목록에서 적절한 옵션을 선택하여 충돌이 있을 경우 건너뛸지 아니면 덮어쓸지를 선택합니다.

1. **복원** 을 선택하여 복원 작업을 시작합니다.

   :::image type="content" source="./media/restore-afs/azure-file-share-original-location-recovery.png" alt-text="시작 화면에 복원을 선택 하는 것을 보여 주는 스크린샷":::

#### <a name="restore-to-an-alternate-location-full-share-recovery"></a>대체 위치로 복원(전체 공유 복구)

1. **복구 대상** 으로 **대체 위치** 를 선택합니다.
1. **스토리지 계정** 드롭다운 목록에서 백업된 콘텐츠를 복원할 대상 스토리지 계정을 선택합니다.
1. **파일 공유 선택** 드롭다운 목록에는 2단계에서 선택한 스토리지 계정에 있는 파일 공유가 표시됩니다. 백업된 콘텐츠를 복원할 파일 공유를 선택합니다.
1. **폴더 이름** 상자에서 복원된 콘텐츠로 대상 파일 공유에서 만들려는 폴더 이름을 지정합니다.
1. 충돌이 있을 경우 건너뛸지 아니면 덮어쓸지를 선택합니다.
1. 모든 상자에 적절한 값을 입력한 후 **복원** 을 선택하여 복원 작업을 시작합니다.

   :::image type="content" source="./media/restore-afs/azure-file-share-alternate-location-recovery.png" alt-text="대체 위치를 선택 하도록 보여 주는 스크린샷":::

### <a name="item-level-recovery"></a>항목 수준 복구

이 복원 옵션을 사용하여 원래 위치나 대체 위치에서 개별 파일 또는 폴더를 복원할 수 있습니다.

1. **백업 센터** 로 이동 하 여 메뉴에서 **백업 인스턴스** 를 선택 하 고 **Azure Storage (Azure Files)** 로 선택한 datasource 유형을 사용 합니다.
1. 항목 수준 복구를 수행 하려는 파일 공유를 선택 합니다.

   백업 항목 메뉴에 **파일 복구** 옵션이 표시 됩니다.

    ![파일 복구 선택](./media/restore-afs/file-recovery.png)

1. **파일 복구** 를 선택하면 **복원** 창이 열립니다. 복원 작업을 수행하는 데 사용할 복원 지점을 선택하려면 **복원 지점** 텍스트 상자 아래에 있는 **선택** 링크 텍스트를 선택합니다.

    ![선택 링크를 선택하여 복원 지점 선택](./media/restore-afs/select-restore-point.png)

1. **복원 지점 선택** 컨텍스트 창이 오른쪽에 열리고 선택한 파일 공유에 사용할 수 있는 복원 지점이 나열됩니다. 복원 작업을 수행하는 데 사용할 복원 지점을 선택한 다음 **확인** 을 선택합니다.

    ![복원 지점 선택](./media/restore-afs/restore-point.png)

1. 다음 단계는 **복원 위치** 를 선택하는 것입니다. **복구 대상** 섹션에서 데이터 복원 여부 또는 방법을 지정합니다. 토글 단추를 사용하여 다음 두 옵션 중 하나를 선택합니다.

    * **원래 위치**: 선택한 파일 또는 폴더를 원래 원본과 동일한 파일 공유에 복원합니다.
    * **대체 위치**: 선택한 파일 또는 폴더를 대체 위치로 복원하고 원래 파일 공유 콘텐츠를 그대로 유지합니다.

#### <a name="restore-to-the-original-location-item-level-recovery"></a>원래 위치로 복원(항목 수준 복구)

1. **복구 대상** 으로 **원래 위치** 를 선택한 다음, **충돌 시** 드롭다운 목록에서 적절한 옵션을 선택하여 충돌이 있을 경우 건너뛸지 아니면 덮어쓸지를 선택합니다.

    ![항목 수준 복구의 원래 위치](./media/restore-afs/original-location-item-level.png)

1. 복원할 파일 또는 폴더를 선택하려면 **파일 추가** 단추를 선택합니다. 컨텍스트 창이 오른쪽에 열리고 복원하도록 선택한 파일 공유 복구 지점의 콘텐츠가 표시됩니다.

    ![파일 추가 선택](./media/restore-afs/add-file.png)

1. 복원할 파일 또는 폴더에 해당하는 확인란을 선택한 다음 **선택** 을 선택합니다.

    ![파일 또는 폴더 선택](./media/restore-afs/select-file-folder.png)

1. 2~4 단계를 반복하여 복원할 파일 또는 폴더를 여러 개 선택합니다.
1. 복원할 항목을 모두 선택했으면 **복원** 을 선택하여 복원 작업을 시작합니다.

    ![복원을 선택하여 시작](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location-item-level-recovery"></a>대체 위치로 복원(항목 수준 복구)

1. **복구 대상** 으로 **대체 위치** 를 선택합니다.
1. **스토리지 계정** 드롭다운 목록에서 백업된 콘텐츠를 복원할 대상 스토리지 계정을 선택합니다.
1. **파일 공유 선택** 드롭다운 목록에는 2단계에서 선택한 스토리지 계정에 있는 파일 공유가 표시됩니다. 백업된 콘텐츠를 복원할 파일 공유를 선택합니다.
1. **폴더 이름** 상자에서 복원된 콘텐츠로 대상 파일 공유에서 만들려는 폴더 이름을 지정합니다.
1. 충돌이 있을 경우 건너뛸지 아니면 덮어쓸지를 선택합니다.
1. 복원할 파일 또는 폴더를 선택하려면 **파일 추가** 단추를 선택합니다. 컨텍스트 창이 오른쪽에 열리고 복원하도록 선택한 파일 공유 복구 지점의 콘텐츠가 표시됩니다.

    ![대체 위치로 복원할 항목 선택](./media/restore-afs/restore-to-alternate-location.png)

1. 복원할 파일 또는 폴더에 해당하는 확인란을 선택한 다음 **선택** 을 선택합니다.

    ![복구 대상 선택](./media/restore-afs/recovery-destination.png)

1. 6~8 단계를 반복하여 복원할 파일 또는 폴더를 여러 개 선택합니다.
1. 복원할 항목을 모두 선택했으면 **복원** 을 선택하여 복원 작업을 시작합니다.

    ![모든 파일을 선택한 후 확인 선택](./media/restore-afs/after-selecting-all-items.png)

## <a name="track-a-restore-operation"></a>복원 작업 추적

복원 작업이 트리거되면 백업 서비스에서 추적 작업을 만듭니다. Azure Backup에서 작업에 대한 알림을 포털에 표시합니다. 작업(job)의 작업(operation)을 보려면 알림 하이퍼링크를 선택합니다.

![알림 하이퍼링크 선택](./media/restore-afs/notifications-link.png)

Recovery Services 자격 증명 모음에서 복원 진행률을 모니터링할 수도 있습니다.

1. **백업 센터** 로 이동 하 고 메뉴에서 **백업 작업** 을 클릭 합니다.
1. 필요한 데이터 원본 유형 및 작업 상태에 대 한 작업을 필터링 합니다.

   :::image type="content" source="./media/restore-afs/backup-center-jobs-inline.png" alt-text="백업 작업을 선택 하는 것을 보여 주는 스크린샷" lightbox="./media/restore-afs/backup-center-jobs-expanded.png":::

1. 파일 공유에 해당하는 워크로드 이름을 선택하여 **전송된 데이터**, **복원된 파일 수** 등 복원 작업에 대한 자세한 정보를 확인합니다.

    ![복원된 세부 정보 확인](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>다음 단계

* [Azure 파일 공유 백업을 관리](manage-afs-backup.md)하는 방법을 알아봅니다.
