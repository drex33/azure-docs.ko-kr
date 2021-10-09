---
title: Azure 파일 공유 백업 관리
description: 이 문서에서는 Azure Backup에 의해 백업된 Azure 파일 공유를 관리하고 모니터링하는 일반적인 작업에 대해 설명합니다.
ms.topic: conceptual
ms.date: 10/08/2021
ms.openlocfilehash: e955ed1cf01c055ea72218076799d7da31d096b7
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714317"
---
# <a name="manage-azure-file-share-backups"></a>Azure 파일 공유 백업 관리

이 문서에서는 [Azure Backup](./backup-overview.md)에 의해 백업된 Azure 파일 공유를 관리하고 모니터링하는 일반적인 작업에 대해 설명합니다. Recovery Services 자격 증명 모음에서 관리 작업을 수행하는 방법을 알아봅니다.

## <a name="monitor-jobs"></a>작업 모니터링

백업 또는 복원 작업을 트리거하면 백업 서비스에서 추적 작업을 만듭니다. **Backup 작업** 페이지에서 모든 작업의 진행률을 모니터링할 수 있습니다.

**Backup 작업** 페이지를 열려면:

1. 파일 공유의 백업을 구성하는 데 사용한 Recovery Services 자격 증명 모음을 엽니다. **개요** 창에서 **모니터링** 섹션 아래의 **백업 작업** 을 선택합니다.

   ![모니터링 섹션의 백업 작업](./media/manage-afs-backup/backup-jobs.png)

1. **확인** 을 선택하면 **백업 작업** 창에 모든 작업의 상태가 나열됩니다. 모니터링할 파일 공유에 해당하는 워크로드 이름을 선택합니다.

   ![워크로드 이름](./media/manage-afs-backup/workload-name.png)

## <a name="monitor-using-azure-backup-reports"></a>Azure Backup 보고서를 사용 하 여 모니터링

Azure Backup [Azure Monitor 로그](../azure-monitor/logs/log-analytics-tutorial.md) 및 [Azure 통합 문서](../azure-monitor/visualize/workbooks-overview.md)를 사용 하는 보고 솔루션을 제공 합니다. 이러한 리소스는 백업에 대 한 풍부한 통찰력을 얻는 데 도움이 됩니다. 이러한 보고서를 활용 하 여 Azure Files 백업 항목, 항목 수준의 작업 및 활성 정책의 세부 정보를 파악할 수 있습니다. 백업 보고서에서 사용할 수 있는 전자 메일 보고서 기능을 사용 하면 자동화 된 작업을 만들어 전자 메일을 통해 정기적으로 보고서를 받을 수 있습니다. Azure Backup 보고서를 구성 하 고 보는 방법에 [대해 알아봅니다](/azure/backup/configure-reports#get-started) .

## <a name="create-a-new-policy"></a>새 정책 만들기

Recovery Services 자격 증명 모음의 **백업 정책** 섹션에서 Azure 파일 공유를 백업하는 새 정책을 만들 수 있습니다. 파일 공유에 대한 백업을 구성할 때 만든 모든 정책은 **정책 유형** 이 **Azure 파일 공유** 로 표시됩니다.

새 백업 정책을 만들려면 다음 단계를 수행 합니다.

1. Recovery Services 자격 증명 모음의 **백업 정책** 창에서 **+ 추가** 를 선택 합니다.

   :::image type="content" source="./media/manage-afs-backup/new-backup-policy.png" alt-text="새 백업 정책 만들기를 시작 하는 옵션을 보여 주는 스크린샷":::

1. **추가** 창에서 **Azure 파일 공유** 를 **정책 유형** 으로 선택합니다.

   :::image type="content" source="./media/manage-afs-backup/define-policy-type.png" alt-text="Azure 파일 공유를 정책 형식으로 선택 하는 것을 보여 주는 스크린샷":::

1. **Azure 파일 공유** 에 대 한 **백업 정책** 창이 열리면 정책 이름을 지정 합니다.

1. **백업 일정** 에서 백업에 대 한 적절 한 빈도 ( **매일** 또는 **매시간**)를 선택 합니다.

   :::image type="content" source="./media/manage-afs-backup/backup-frequency-types.png" alt-text="백업 빈도 유형을 보여 주는 스크린샷":::

   - **매일**: 하루에 한 번 백업을 트리거합니다. 일별 빈도에서 적절 한 값을 선택 합니다.

     - **시간**: 백업 작업을 트리거해야 하는 타임 스탬프입니다.
     - **표준 시간대**: 백업 작업에 해당 하는 표준 시간대입니다.

   - **매시간**: 하루에 여러 백업을 트리거합니다. 매시간 빈도에 대해 적절 한 값을 선택 합니다.
   
     - **일정**: 연속 백업 간의 시간 간격 (시간)입니다.
     - **시작 시간**: 해당 요일의 첫 번째 백업 작업을 트리거해야 하는 시간입니다.
     - **기간**: 선택한 일정에 따라 백업 작업을 트리거해야 하는 시간 범위 (시간)를 나타냅니다.
     - **표준 시간대**: 백업 작업에 해당 하는 표준 시간대입니다.
     
     예를 들어 RPO (복구 지점 목표) 요구 사항은 4 시간이 고 작업 시간은 오전 9 시에서 오후 9 시 사이입니다. 이러한 요구 사항을 충족 하기 위해 백업 일정에 대 한 구성은 다음과 같습니다.
    
     - 일정: 4 시간 마다
     - 시작 시간: 오전 9 시 
     - 기간: 12 시간 
     
     :::image type="content" source="./media/manage-afs-backup/hourly-backup-frequency-values-scenario.png" alt-text="시간별 백업 빈도 값의 예를 보여 주는 스크린샷":::

     선택 사항에 따라 백업 작업 세부 정보 (백업 작업이 트리거될 때의 타임 스탬프)가 backup 정책 블레이드에 표시 됩니다.

1. **보존 범위** 에서 매일, 매주, 매월 또는 매년으로 태그가 지정 된 백업에 대 한 적절 한 보존 값을 지정 합니다.

1. 정책의 모든 특성을 정의한 후 **만들기** 를 클릭 합니다.
  
### <a name="view-policy"></a>정책 보기

기존 백업 정책을 보려면 다음을 수행합니다.

1. 파일 공유의 백업을 구성하는 데 사용한 Recovery Services 자격 증명 모음을 엽니다. Recovery Services 자격 증명 모음 메뉴에서 **관리** 섹션 아래의 **백업 정책** 을 선택합니다. 자격 증명 모음에 구성된 모든 백업 정책이 표시됩니다.

   :::image type="content" source="./media/manage-afs-backup/all-backup-policies.png" alt-text="모든 백업 정책을 보여 주는 스크린샷":::

1. **Azure 파일 공유** 와 관련된 정책을 보려면 오른쪽 위의 드롭다운 목록에서 **Azure 파일 공유** 를 선택합니다.

   :::image type="content" source="./media/manage-afs-backup/azure-file-share.png" alt-text="Azure 파일 공유를 선택 하는 프로세스를 보여 주는 스크린샷":::

## <a name="modify-policy"></a>정책 수정

백업 정책을 수정하여 백업 빈도 또는 보존 기간을 변경할 수 있습니다.

정책을 수정하려면 다음을 수행합니다.

1. 파일 공유의 백업을 구성하는 데 사용한 Recovery Services 자격 증명 모음을 엽니다. Recovery Services 자격 증명 모음 메뉴에서 **관리** 섹션 아래의 **백업 정책** 을 선택합니다. 자격 증명 모음에 구성된 모든 백업 정책이 표시됩니다.

   ![자격 증명 모음의 모든 백업 정책](./media/manage-afs-backup/all-backup-policies-modify.png)

1. Azure 파일 공유와 관련된 정책을 보려면 오른쪽 위의 드롭다운 목록에서 **Azure 파일 공유** 를 선택합니다. 수정할 백업 정책을 선택합니다.

   ![수정할 Azure 파일 공유](./media/manage-afs-backup/azure-file-share-modify.png)

1. **일정** 창이 열립니다. 필요에 따라 **백업 일정** 및 **보존 기간** 을 편집하고 **저장** 을 선택합니다. 창에 “업데이트 진행 중” 메시지가 표시됩니다. 정책 변경 내용이 성공적으로 업데이트되면 “백업 정책을 업데이트했습니다.” 메시지가 표시됩니다.

   ![수정된 정책 저장](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>파일 공유에 대한 보호 중지

Azure 파일 공유를 중지하는 두 가지 방법이 있습니다.

* 이후의 모든 백업 작업을 중지하고 ‘모든 복구 지점을 삭제’합니다.
* 이후의 모든 백업 작업을 중지하지만 ‘복구 지점은 그대로 유지’합니다.

스토리지에 복구 지점을 유지할 경우 Azure Backup에서 만드는 기본 스냅샷이 보관되므로 비용이 발생할 수 있습니다. 복구 지점을 유지할 경우 나중에 파일 공유를 복원할 수 있는 이점이 있습니다. 복구 지점을 유지하는 비용에 대한 자세한 내용은 [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/backup/)를 참조하세요. 모든 복구 지점을 삭제하기로 결정하면 파일 공유를 복원할 수 없습니다.

Azure 파일 공유 보호를 중지하려면:

1. 파일 공유 복구 지점이 포함된 Recovery Services 자격 증명 모음을 엽니다. **보호된 항목** 섹션에서 **백업 항목** 을 선택합니다. 백업 항목 유형 목록이 표시됩니다.

   ![Backup 항목](./media/manage-afs-backup/backup-items.png)

1. **Backup 관리 유형** 목록에서 **Azure Storage(Azure 파일)** 를 선택합니다. **백업 항목(Azure Storage(Azure Files))** 목록이 표시됩니다.

   ![Azure Storage(Azure Files) 선택](./media/manage-afs-backup/azure-storage-azure-files.png)

1. **백업 항목(Azure Storage(Azure Files))** 목록에서 보호를 중지하려는 백업 항목을 선택합니다.

1. **백업 중지** 옵션을 선택합니다.

   ![백업 중지 선택](./media/manage-afs-backup/stop-backup.png)

1. **백업 중지** 창에서 **백업 데이터 보존** 또는 **백업 데이터 삭제** 를 선택합니다. 그런 다음 **백업 중지** 를 선택합니다.

    ![백업 데이터 보존 또는 백업 데이터 삭제 선택](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>파일 공유에 대한 보호 다시 시작

파일 공유에 대한 보호가 중지되었을 때 **백업 데이터 보존** 옵션을 선택한 경우 보호를 다시 시작할 수 있습니다. **백업 데이터 삭제** 옵션을 선택한 경우 파일 공유에 대한 보호를 다시 시작할 수 없습니다.

Azure 파일 공유에 대한 보호를 다시 시작하려면 다음을 수행합니다.

1. 파일 공유 복구 지점이 포함된 Recovery Services 자격 증명 모음을 엽니다. **보호된 항목** 섹션에서 **백업 항목** 을 선택합니다. 백업 항목 유형 목록이 표시됩니다.

   ![다시 시작할 백업 항목](./media/manage-afs-backup/backup-items-resume.png)

1. **Backup 관리 유형** 목록에서 **Azure Storage(Azure 파일)** 를 선택합니다. **백업 항목(Azure Storage(Azure Files))** 목록이 표시됩니다.

   ![Azure Storage(Azure Files) 목록](./media/manage-afs-backup/azure-storage-azure-files.png)

1. **백업 항목(Azure Storage(Azure Files))** 목록에서 보호를 다시 시작할 백업 항목을 선택합니다.

1. **백업 다시 시작** 옵션을 선택합니다.

   ![백업 다시 시작 선택](./media/manage-afs-backup/resume-backup.png)

1. **백업 정책** 창이 열립니다. 원하는 정책을 선택하여 백업을 다시 시작합니다.

1. 백업 정책을 선택한 후 **저장** 을 선택합니다. 포털에 “업데이트 진행 중” 메시지가 표시됩니다. 백업이 성공적으로 다시 시작되면 “보호된 Azure 파일 공유에 대한 백업 정책을 업데이트했습니다.” 메시지가 표시됩니다.

   ![백업 정책을 업데이트함](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>백업 데이터 삭제

**백업 중지** 작업 중 또는 보호를 중지 한 후 언제 든 지 파일 공유의 백업을 삭제할 수 있습니다. 복구 지점을 삭제하기 전에 며칠 또는 몇 주 기다리는 것이 도움이 될 수도 있습니다. 백업 데이터를 삭제하는 경우 삭제할 특정 복구 지점을 선택할 수 없습니다. 백업 데이터를 삭제하도록 결정하면 해당 파일 공유에 연결된 모든 복구 지점을 삭제합니다.

다음 절차에서는 파일 공유에 대한 보호가 중지된 것으로 가정합니다.

Azure 파일 공유에 대한 백업 데이터를 삭제하려면 다음을 수행합니다.

1. 백업 작업이 중지된 경우 **백업 항목** 대시보드에서 **백업 다시 시작** 및 **백업 데이터 삭제** 옵션을 사용할 수 있습니다. **백업 데이터 삭제** 옵션을 선택합니다.

   ![백업 데이터 삭제](./media/manage-afs-backup/delete-backup-data.png)

1. **백업 데이터 삭제** 창이 열립니다. 삭제를 확인하려면 파일 공유의 이름을 입력합니다. 필요에 따라 **이유** 또는 **주석** 상자에 추가 정보를 제공합니다. 백업 데이터 삭제가 결정되었으면 **삭제** 를 선택합니다.

   ![데이터 삭제 확인](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>스토리지 계정 등록 취소

다른 Recovery Services 자격 증명 모음을 사용하여 특정 스토리지 계정에서 파일 공유를 보호하려면 먼저 해당 스토리지 계정에서 [모든 파일 공유에 대한 보호를 중지](#stop-protection-on-a-file-share)합니다. 그런 다음, 보호에 사용되는 현재 Recovery Services 자격 증명 모음에서 계정을 등록 취소합니다.

다음 절차에서는 등록을 취소하려는 스토리지 계정의 모든 파일 공유에 대한 보호가 중지된 것으로 가정합니다.

스토리지 계정을 등록 취소하려면 다음을 수행합니다.

1. 스토리지 계정이 등록된 Recovery Services 자격 증명 모음을 엽니다.
1. **개요** 창에서 **관리** 섹션 아래의 **백업 인프라** 옵션을 선택합니다.

   ![백업 인프라 선택](./media/manage-afs-backup/backup-infrastructure.png)

1. **백업 인프라** 창이 열립니다. **Azure Storage 계정** 섹션에서 **스토리지 계정** 을 선택합니다.

   ![스토리지 계정 선택](./media/manage-afs-backup/storage-accounts.png)

1. **스토리지 계정** 을 선택하면 자격 증명 모음에 등록된 스토리지 계정 목록이 표시됩니다.
1. 등록 취소하려는 스토리지 계정을 마우스 오른쪽 단추로 클릭하고 **등록 취소** 를 선택합니다.

   ![등록 취소 선택](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure 파일 공유 백업 문제 해결](./troubleshoot-azure-files.md)을 참조하세요.
