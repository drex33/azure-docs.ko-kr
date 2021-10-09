---
title: Azure Portal의 Azure 파일 공유 백업
description: Azure Portal를 사용하여 Recovery Services 자격 증명 모음에 Azure 파일 공유를 백업하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 10/08/2021
ms.openlocfilehash: 8e50cee7e177375fae41ef6fb3a27f871427342c
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714398"
---
# <a name="back-up-azure-file-shares"></a>Azure 파일 공유 백업

이 문서에서는 Azure Portal에서 [Azure 파일 공유](../storage/files/storage-files-introduction.md)를 백업하는 방법을 설명합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

* Recovery Services 자격 증명 모음을 만듭니다.
* Recovery Services 자격 증명 모음에서 백업 구성
* 파일 공유 창에서 백업 구성
* 주문형 백업 작업을 실행하여 복원 지점 만들기

## <a name="prerequisites"></a>필수 구성 요소

* Azure 파일 공유 스냅샷 기반 백업 솔루션에 대해 [알아봅니다](azure-file-share-backup-overview.md).
* [지원되는 스토리지 계정 유형](azure-file-share-support-matrix.md) 중 하나에 파일 공유가 있는지 확인합니다.
* 파일 공유를 호스트하는 스토리지 계정과 동일한 지역 및 구독에서 [Recovery Services 자격 증명 모음](#create-a-recovery-services-vault)을 식별하거나 만듭니다.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configure-backup-from-the-recovery-services-vault"></a>Recovery Services 자격 증명 모음에서 백업 구성

다음 단계에서는 Recovery Services 자격 증명 모음 창에서 여러 파일 공유에 대한 백업을 구성하는 방법을 설명합니다.

1. [Azure Portal](https://portal.azure.com/)에서 파일 공유에 대한 백업 구성에 사용할 Recovery Services 자격 증명 모음을 엽니다.

1. **Recovery Services 자격 증명 모음** 창의 위쪽 메뉴에서 **+백업** 을 선택합니다.

   ![Recovery Services 자격 증명 모음](./media/backup-afs/recovery-services-vault.png)

    1. **백업 목표** 창의 드롭다운 목록에서 **Azure** 옵션을 선택하여 **워크로드가 실행되는 위치** 를 **Azure** 로 설정합니다.

          ![워크로드로 Azure 선택](./media/backup-afs/backup-goal.png)

    2. **백업할 항목** 의 드롭다운 목록에서 **Azure 파일 공유** 를 선택합니다.

          ![Azure 파일 공유 선택](./media/backup-afs/select-azure-file-share.png)

    3. **백업** 을 선택하여 자격 증명 모음에 Azure 파일 공유 확장을 등록합니다.

          ![백업을 선택하여 Azure 파일 공유를 자격 증명 모음에 연결](./media/backup-afs/register-extension.png)

1. **백업** 을 선택하면 **백업** 창이 열립니다. 보호할 파일 공유를 호스트하는 스토리지 계정을 선택하려면 **스토리지 계정** 텍스트 상자 아래에 있는 **선택** 링크 텍스트를 선택합니다.

   ![선택 링크 선택](./media/backup-afs/choose-select-link.png)

1. **스토리지 계정 선택 창** 이 오른쪽에 열리고 검색된 지원되는 스토리지 계정 집합이 나열됩니다. 이 자격 증명 모음과 연결된 계정이거나 자격 증명 모음과 동일한 지역에 있지만 아직 Recovery Services 자격 증명 모음에 연결되지 않은 계정입니다.

1. 검색된 스토리지 계정 목록에서 계정을 선택한 다음 **확인** 을 선택합니다.

   ![검색된 스토리지 계정에서 선택](./media/backup-afs/select-discovered-storage-account.png)
   
   >[!NOTE]
   > 스토리지 계정이 자격 증명 모음과 다른 지역에 있는 경우 검색된 스토리지 계정 목록에 표시되지 않습니다.

1. 다음 단계는 백업할 파일 공유를 선택하는 것입니다. **백업할 파일 공유** 섹션에서 **추가** 단추를 선택합니다.

   ![백업할 파일 공유 선택](./media/backup-afs/select-file-shares-to-back-up.png)

1. **파일 공유 선택** 컨텍스트 창이 오른쪽에 열립니다. Azure가 스토리지 계정에서 백업 가능한 파일 공유를 검색합니다. 최근에 추가한 파일 공유가 목록에 표시되지 않는 경우 파일 공유가 표시될 때까지 잠시 기다립니다.

1. **파일 공유 선택** 목록에서 백업할 파일 공유를 하나 이상 선택합니다. **확인** 을 선택합니다.

   ![파일 공유 선택](./media/backup-afs/select-file-shares.png)

1. 파일 공유에 대한 백업 정책을 선택하려면 다음 세 가지 옵션을 사용할 수 있습니다.

   * 기본 정책 선택<br>
   이 옵션을 통해 30일 동안 보존되는 매일 백업을 사용하도록 설정할 수 있습니다. 자격 증명 모음에 기존 백업 정책이 없으면 기본 정책 설정을 사용하여 백업 창이 열립니다. 기본 설정을 선택하려는 경우 **백업 사용** 을 직접 선택할 수 있습니다.

   * 새 정책 만들기 <br>

      1. 파일 공유에 대한 새 백업 정책을 만들려면 **백업 정책** 섹션의 드롭다운 목록 아래에 있는 링크 텍스트를 선택합니다.<br>

         ![새 정책 만들기](./media/backup-afs/create-new-policy.png)

      1. [새 정책 만들기](manage-afs-backup.md#create-a-new-policy) 섹션의 3-7단계를 따릅니다.

      1. 정책의 모든 특성을 정의한 후 **확인을** 클릭합니다.

         ![정책 이름 및 보존 값 지정](./media/backup-afs/policy-name.png)

   * 기존 백업 정책 중 하나 선택 <br>

      보호를 구성하기 위해 기존 백업 정책 중 하나를 선택하려면 **백업 정책** 드롭다운 목록에서 원하는 정책을 선택합니다.<br>

      ![기존 정책 선택](./media/backup-afs/choose-existing-policy.png)

1. **백업 사용** 을 선택하여 파일 공유 보호를 시작합니다.

   ![백업 사용 선택](./media/backup-afs/enable-backup.png)

백업 정책을 설정하면 예약된 시간에 파일 공유 스냅샷이 생성됩니다. 또한 복구 지점이 선택한 기간 동안 보존됩니다.


## <a name="configure-backup-from-the-file-share-pane"></a>파일 공유 창에서 백업 구성

다음 단계에서는 파일 공유 창에서 개별 파일 공유에 대한 백업을 구성하는 방법을 설명합니다.

1. [Azure Portal](https://portal.azure.com/)에서 백업할 파일 공유를 호스트하는 스토리지 계정을 엽니다.

1. 스토리지 계정에서 **파일 공유** 라는 레이블이 지정된 타일을 선택합니다. 스토리지 계정의 목차를 통해 **파일 공유** 로 이동할 수도 있습니다.

   ![스토리지 계정](./media/backup-afs/storage-account.png)

1. 파일 공유 목록에는 스토리지 계정에 있는 모든 파일 공유가 표시됩니다. 백업할 파일 공유를 선택합니다.

   ![파일 공유 목록](./media/backup-afs/file-shares-list.png)

1. 파일 공유 창의 **작업** 섹션에서 **백업** 을 선택합니다. **백업 구성** 창이 오른쪽에 로드됩니다.

   ![백업 구성 창](./media/backup-afs/configure-backup.png)

1. Recovery Services 자격 증명 모음을 선택하려면 다음 중 하나를 수행합니다.

    * 자격 증명 모음이 이미 있는 경우 **기존 Recovery Services 자격 증명 모음 선택** 라디오 단추를 선택한 다음 **자격 증명 모음 이름** 드롭다운 메뉴에서 기존 자격 증명 모음 중 하나를 선택합니다.

       ![기존 자격 증명 모음 선택](./media/backup-afs/select-existing-vault.png)

    * 자격 증명 모음이 없는 경우 **새 Recovery Services 자격 증명 모음 만들기** 라디오 단추를 선택합니다. 자격 증명 모음의 이름을 지정합니다. 파일 공유와 동일한 지역에 생성됩니다. 기본적으로 자격 증명 모음은 파일 공유와 동일한 리소스 그룹에 생성됩니다. 다른 리소스 그룹을 선택하려는 경우 **리소스 종류** 드롭다운 아래에 있는 **새로 만들기** 링크를 선택하고 리소스 그룹의 이름을 지정합니다. **확인** 을 선택하여 계속합니다.

       ![새 자격 증명 모음 만들기](./media/backup-afs/create-new-vault.png)

      >[!IMPORTANT]
      >스토리지 계정이 자격 증명 모음에 등록되어 있거나 보호할 파일 공유를 호스트하는 스토리지 계정 내에 몇 개의 보호된 공유만 있는 경우 Recovery Services 자격 증명 모음 이름이 미리 채워지며 편집할 수 없습니다. [여기에서 자세히 알아보세요](backup-azure-files-faq.yml#why-can-t-i-change-the-vault-to-configure-backup-for-the-file-share-).

1. **백업 정책** 을 선택하려면 다음 중 하나를 수행합니다.

    * 기본 정책을 그대로 둡니다. 30일 보존으로 매일 백업이 예약됩니다.

    * **백업 정책** 드롭다운 메뉴에서 기존 백업 정책(있는 경우)을 선택합니다.

       ![백업 정책 선택](./media/backup-afs/choose-backup-policy.png)

    * 요구 사항에 따라 매일/매주/매월/매년 보존으로 새 정책을 만듭니다.  

         1. **새 정책 만들기** 링크 텍스트를 선택합니다.

         2. [새 정책 만들기](manage-afs-backup.md#create-a-new-policy) 섹션의 3-7단계를 따릅니다.

         3. 정책의 모든 특성을 정의한 후 **확인을** 클릭합니다.

            ![새 백업 정책 만들기](./media/backup-afs/create-new-backup-policy.png)

1. **백업 사용** 을 선택하여 파일 공유 보호를 시작합니다.

   ![백업 사용 선택](./media/backup-afs/select-enable-backup.png)

1. 포털 알림을 통해 또는 파일 공유를 보호하는 데 사용하는 자격 증명 모음의 백업 작업을 모니터링하여 구성 진행 상황을 추적할 수 있습니다.

   ![포털 알림](./media/backup-afs/portal-notifications.png)

1. 백업 구성 작업이 완료되면 파일 공유 창의 **작업** 섹션에서 **백업** 을 선택합니다. **자격 증명 모음 기본 정보** 가 나열된 컨텍스트 창이 오른쪽에 로드됩니다. 여기에서 주문형 백업 및 복원 작업을 트리거할 수 있습니다.

   ![자격 증명 모음 기본 정보](./media/backup-afs/vault-essentials.png)

## <a name="run-an-on-demand-backup-job"></a>주문형 백업 작업 실행

백업 정책에서 예약된 시간이 아닐 때 백업 스냅샷 또는 복구 지점을 생성하려는 경우도 있습니다. 주문형 백업을 생성하는 일반적인 이유는 백업 정책을 구성한 직후입니다. 백업 정책의 일정에 따라 스냅샷이 생성될 때까지 몇 시간 또는 며칠이 걸릴 수 있습니다. 백업 정책이 적용될 때까지 데이터를 보호하려면 주문형 백업을 시작합니다. 파일 공유에 대해 계획된 변경 작업을 수행하기 전에 주문형 백업을 만들어야 하는 경우도 많습니다.

### <a name="from-the-recovery-services-vault"></a>Recovery Services 자격 증명 모음에서

1. 파일 공유를 백업하는 데 사용한 Recovery Services 자격 증명 모음을 엽니다. **개요** 창의 **보호된 항목** 섹션에서 **백업 항목** 을 선택합니다.

   ![백업 항목 선택](./media/backup-afs/backup-items.png)

1. **백업 항목** 을 선택하면 모든 **백업 관리 유형** 이 나열된 새 창이 **개요** 창 옆에 나타납니다.

   ![백업 관리 유형 목록](./media/backup-afs/backup-management-types.png)

1. **백업 관리 유형** 목록에서 **Azure Storage(Azure Files)** 를 선택합니다. 이 자격 증명 모음을 사용하여 백업된 모든 파일 공유 및 해당 스토리지 계정 목록이 표시됩니다.

   ![Azure Storage(Azure Files) 백업 항목](./media/backup-afs/azure-files-backup-items.png)

1. Azure 파일 공유 목록에서 원하는 파일 공유를 선택합니다. **백업 항목** 세부 정보가 표시됩니다. **백업 항목** 메뉴에서 **지금 백업** 을 선택합니다. 이 백업 작업은 주문형이기 때문에 복구 지점과 연결된 보존 정책이 없습니다.

   ![지금 백업 선택](./media/backup-afs/backup-now.png)

1. **지금 백업** 창이 열립니다. 복구 지점을 보존할 마지막 날을 지정합니다. 주문형 백업은 최대 10년 동안 보존할 수 있습니다.

   ![보존 날짜 선택](./media/backup-afs/retention-date.png)

1. **확인** 을 선택하여 실행되는 주문형 백업 작업을 확인합니다.

1. 포털 알림을 모니터링하여 백업 작업 실행 완료를 추적합니다. 자격 증명 모음 대시보드에서 작업 진행률을 모니터링할 수 있습니다. **백업 작업** > **진행 중** 을 선택합니다.

### <a name="from-the-file-share-pane"></a>파일 공유 창에서

1. 주문형 백업을 수행할 파일 공유의 **개요** 창을 엽니다.

1. **작업** 섹션에서 **백업** 을 선택합니다. **자격 증명 모음 기본 정보** 가 나열된 컨텍스트 창이 오른쪽에 로드됩니다. **지금 백업** 을 선택하여 주문형 백업을 수행합니다.

   ![지금 백업 선택](./media/backup-afs/select-backup-now.png)

1. **지금 백업** 창이 열립니다. 복구 지점의 보존 기간을 지정합니다. 주문형 백업은 최대 10년 동안 보존할 수 있습니다.

   ![백업 보존 날짜](./media/backup-afs/retain-backup-date.png)

1. **확인** 을 선택하여 확인합니다.

>[!NOTE]
>스토리지 계정의 파일 공유에 대해 보호를 구성하면 Azure Backup에서 해당 계정을 잠급니다. 이렇게 하면 백업된 파일 공유와 함께 스토리지 계정이 실수로 삭제되는 것을 방지할 수 있습니다.

## <a name="best-practices"></a>모범 사례

* Azure Backup에서 만든 스냅샷을 삭제하지 않습니다. 스냅샷을 삭제하면 복구 지점이 손실되거나 복원이 실패할 수 있습니다.

* Azure Backup에서 적용한 스토리지 계정 잠금을 제거하지 않습니다. 잠금을 삭제하면 스토리지 계정이 실수로 삭제될 수 있으며, 계정이 삭제될 경우 스냅샷 또는 백업이 손실됩니다.

## <a name="next-steps"></a>다음 단계

방법 배우기:

* [Azure 파일 공유 복원](restore-afs.md)
* [Azure 파일 공유 백업 관리](manage-afs-backup.md)
