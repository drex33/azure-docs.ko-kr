---
title: Azure VM에서 SQL Server 데이터베이스 복원
description: 이 문서에서는 Azure Backup으로 백업되는 Azure VM에서 실행되는 SQL Server 데이터베이스 및 복원하는 방법을 설명합니다. 지역 간 복원을 사용하여 데이터베이스를 보조 지역으로 복원할 수도 있습니다.
ms.topic: conceptual
ms.date: 11/02/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 0e687cbae798a480eaf417de04b653b88d2e2c94
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131431115"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Azure VM에서 SQL Server 데이터베이스 복원

이 문서에서는 [Azure Backup](backup-overview.md) 서비스를 사용하여 Azure Backup Recovery Services 자격 증명 모음으로 백업된 SQL Server 데이터베이스(Azure 가상 머신에서 실행)를 복원하는 방법을 설명합니다.

이 문서에서는 SQL Server 데이터베이스를 복원하는 방법을 설명합니다. 자세한 내용은 [Azure VM에 SQL Server 데이터베이스 백업](backup-azure-sql-database.md)을 참조하세요.

## <a name="restore-to-a-time-or-a-recovery-point"></a>시간이나 복구 지점으로 복원

Azure Backup은 다음과 같은 방법으로 Azure VM에서 실행되는 SQL Server 데이터베이스를 복원할 수 있습니다.

- 트랜잭션 로그 백업을 사용하여 특정 날짜 또는 시간(초)으로 복원합니다. Azure Backup은 선택한 시간을 기준으로 복원해야 하는 해당 전체 차등 백업 및 로그 백업 체인을 자동으로 결정합니다.
- 특정 복구 지점으로 복원하려면 특정 전체 또는 차등 백업을 복원합니다.

## <a name="restore-prerequisites"></a>복원 필수 조건

데이터베이스를 복원하기 전에 다음에 유의하십시오.

- 동일한 Azure 지역의 SQL Server 인스턴스에 데이터베이스를 복원할 수 있습니다.
- 대상 서버를 원본과 동일한 자격 증명 모음에 등록해야 합니다.
- 서버에서 여러 개의 인스턴스를 실행하고 있는 경우 모든 인스턴스가 실행 중이어야 합니다. 그렇지 않으면 데이터베이스를 복원할 대상 서버 목록에 서버가 나타나지 않습니다. 자세한 내용은 [문제 해결 단계](backup-sql-server-azure-troubleshoot.md#faulty-instance-in-a-vm-with-multiple-sql-server-instances)를 참조하십시오.
- TDE 암호화된 데이터베이스를 다른 SQL Server로 복원하려면, 먼저 [대상 서버로 인증서를 복원](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server)해야 합니다.
- [CDC](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server)가 사용하도록 설정된 데이터베이스는 [파일로 복원](#restore-as-files) 옵션을 사용하여 복원해야 합니다.
- “마스터” 데이터베이스를 복원하기 전에 시작 옵션 **-m AzureWorkloadBackup** 을 사용하여 단일 사용자 모드로 SQL Server 인스턴스를 시작합니다.
  - **-m** 값은 클라이언트의 이름입니다.
  - 지정된 클라이언트 이름으로만 연결을 열 수 있습니다.
- 모든 시스템 데이터베이스(모델, 마스터, msdb)에 대해 복원을 트리거하기 전에 SQL Server 에이전트 서비스를 중지합니다.
- 이러한 데이터베이스에 대한 연결을 도용하려고 하는 애플리케이션을 닫습니다.

## <a name="restore-a-database"></a>데이터베이스 복원

복원하려면 다음 권한이 필요합니다.

- 복원하려는 자격 증명 모음의 **Backup 운영자** 권한
- 백업되는 원본 VM에 대한 **참가자(쓰기)** 액세스 권한
- 대상 VM에 대한 **참가자(쓰기)** 액세스 권한
  - 동일한 VM으로 복원하는 경우 이 VM이 원본 VM입니다.
  - 대체 위치로 복원하는 경우 이 VM이 새 대상 VM입니다.

다음과 같이 복원합니다.

1. Azure Portal에서 **Backup center** 로 이동 하 고 **복원** 을 클릭 합니다.

   :::image type="content" source="./media/backup-azure-sql-database/backup-center-restore-inline.png" alt-text="복원 프로세스 시작을 보여 주는 스크린샷" lightbox="./media/backup-azure-sql-database/backup-center-restore-expanded.png":::

1. **Azure VM에서** 데이터 원본 유형으로 SQL를 선택 하 고, 복원할 데이터베이스를 선택 하 고 **계속** 을 클릭 합니다.

   :::image type="content" source="./media/backup-azure-sql-database/sql-restore.png" alt-text="Datasource 유형을 선택 하는 것을 보여 주는 스크린샷":::

1. **복원 구성** 에서 데이터를 복원할 위치(또는 방법)를 지정합니다.
   
   - **대체 위치**: 대체 위치에 데이터베이스를 복원하고 원래 원본 데이터베이스를 유지합니다.
   - **DB 덮어쓰기**: 원래 원본과 동일한 SQL Server 인스턴스에 데이터를 복원합니다. 이 옵션은 원래 데이터베이스를 덮어씁니다.

        > [!IMPORTANT]
        > 선택한 데이터베이스가 Always On 가용성 그룹에 속하면 SQL Server에서 데이터베이스를 덮어쓸 수 없습니다. **대체 위치** 만 사용할 수 있습니다.
        >
   - **파일로 복원**: 데이터베이스로 복원하는 대신 SQL Server Management Studio를 사용하여 파일이 있는 컴퓨터에서 나중에 데이터베이스로 복구할 수 있는 백업 파일을 복원합니다.

### <a name="restore-to-an-alternate-location"></a>대체 위치에 복원

1. **복원 구성** 메뉴의 **복원할 위치** 아래에서 **대체 위치** 를 선택합니다.
1. 데이터베이스를 복원하려는 SQL Server 이름 및 인스턴스를 선택합니다.
1. **복원된 DB 이름** 상자에 대상 데이터베이스의 이름을 입력합니다.
1. 해당되는 경우 **선택한 SQL 인스턴스에 이름이 같은 DB가 있으면 덮어쓰기** 를 선택합니다.
1. **복원 지점 선택** 을 선택한 다음 [특정 시점으로 복원](#restore-to-a-specific-point-in-time)할지 또는 [특정 복구 지점](#restore-to-a-specific-restore-point)으로 복원할지를 선택합니다.

   :::image type="content" source="./media/backup-azure-sql-database/sql-alternate-location-recovery.png" alt-text="복원 지점을 선택 하는 것을 보여 주는 스크린샷":::

   :::image type="content" source="./media/backup-azure-sql-database/restore-points-sql-inline.png" alt-text="지정 시간에 대 한 복원을 보여 주는 스크린샷" lightbox="./media/backup-azure-sql-database/restore-points-sql-expanded.png":::

1. **고급 구성** 메뉴에서:

    - 복원 후 데이터베이스를 작동하지 않는 상태로 유지하려면 **NORECOVERY를 사용하여 복원** 을 사용하도록 설정합니다.
    - 대상 서버에서 복원 위치를 변경하려면 새 대상 경로를 입력합니다.

        ![대상 경로 입력](./media/backup-azure-sql-database/target-paths.png)

1. **확인** 을 선택하여 복원을 트리거합니다. **알림** 영역에서 복원 진행률을 추적하거나 자격 증명 모음의 **백업 작업** 보기에서 추적합니다.

    > [!NOTE]
    > 특정 시점 복원은 전체 및 대량 로그된 복구 모델을 사용하는 데이터베이스의 로그 백업에만 사용할 수 있습니다.

### <a name="restore-and-overwrite"></a>복원 및 덮어쓰기

1. **복원 구성** 메뉴의 **복원할 위치** 아래에서 **DB 덮어쓰기** > **확인** 을 선택합니다.

    ![DB 덮어쓰기 선택](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. **복원 지점 선택** 에서 **로그(특정 시점 복원)** 을 선택하여 [특정 시점으로 복원](#restore-to-a-specific-point-in-time)합니다. 또는 **전체 및 차등** 을 선택하여 [특정 복구 지점](#restore-to-a-specific-restore-point)으로 복원합니다.

    > [!NOTE]
    > 특정 시점 복원은 전체 및 대량 로그된 복구 모델을 사용하는 데이터베이스의 로그 백업에만 사용할 수 있습니다.

### <a name="restore-as-files"></a>파일로 복원

백업 데이터를 데이터베이스 대신 .bak 파일로 복원하려면 **파일로 복원** 을 선택하십시오. 파일이 지정된 경로에 덤프되면 이러한 파일을 데이터베이스로 복원하려는 컴퓨터로 가져올 수 있습니다. 이러한 파일을 임의의 컴퓨터로 이동할 수 있으므로 이제 구독 및 지역 간에 데이터를 복원할 수 있습니다.

1. **복원 위치 및 방법** 에서 **파일로 복원** 을 선택합니다.
1. 백업 파일을 복원할 SQL Server 이름을 선택 합니다.
1. **서버의 대상 경로** 에서 2단계에서 선택한 서버에 폴더 경로를 입력합니다. 서비스에서 필요한 모든 백업 파일을 덤프하는 위치입니다. 일반적으로 네트워크 공유 경로 또는 탑재된 Azure 파일 공유 경로가 대상 경로로 지정되면 이를 통해 동일한 네트워크의 다른 컴퓨터 또는 동일한 Azure 파일 공유가 탑재된 다른 컴퓨터에서 이러한 파일에 더 쉽게 액세스할 수 있습니다.<BR>

   >[!Note]
   >등록된 대상 VM에 탑재된 Azure 파일 공유에서 데이터베이스 백업 파일을 복원하려면 NT AUTHORITY\SYSTEM에 파일 공유에 대한 액세스 권한이 있는지 확인합니다. 아래 지정된 단계를 수행하여 VM에 탑재된 AFS에 대한 읽기/쓰기 권한을 부여할 수 있습니다.
   >
   >- `PsExec -s cmd`를 실행하여 NT AUTHORITY\SYSTEM 셸에 입력
   >   - `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>` 실행
   >   - `dir \\<storageacct>.file.core.windows.net\<filesharename>` 액세스 확인
   >- `\\<storageacct>.file.core.windows.net\<filesharename>`을 경로로 하여 백업 자격 증명 모음에서 파일로 복원을 시작<BR>
   >[Sysinternals](/sysinternals/downloads/psexec) 페이지에서 PsExec를 다운로드할 수 있습니다.

1. **확인** 을 선택합니다.

    ![파일로 복원 선택](./media/backup-azure-sql-database/restore-as-files.png)

1. **복원 지점 선택** 을 선택한 다음 [특정 시점으로 복원](#restore-to-a-specific-point-in-time)할지 또는 [특정 복구 지점](#restore-to-a-specific-restore-point)으로 복원할지를 선택합니다.

1. 선택한 복구 지점과 연결된 모든 백업 파일은 대상 경로로 덤프됩니다. SQL Server Management Studio를 사용하여 모든 컴퓨터의 데이터베이스로 파일을 복원할 수 있습니다.

    ![대상 경로의 복원된 백업 파일](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>특정 시점으로 복원

복원 유형으로 **로그(시점)** 을 선택한 경우 다음을 수행합니다.

1. **복원 날짜/시간** 에서 달력을 엽니다. 달력에서 복구 지점이 있는 날짜는 굵은 형식으로 표시되 고 현재 날짜는 강조 표시됩니다.
1. 복구 지점이 있는 날짜를 선택합니다. 복구 지점이 없는 날짜는 선택할 수 없습니다.

    ![달력 열기](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. 날짜를 선택한 후에는 타임라인 그래프에 사용 가능한 복구 지점이 연속적인 범위로 표시됩니다.
1. 타임라인 그래프에서 복구 시간을 지정하거나 시간을 선택합니다. 그런 다음, **확인** 을 선택합니다.

### <a name="restore-to-a-specific-restore-point"></a>특정 복원 지점으로 복원

복원 유형으로 **전체 및 차등** 을 선택한 경우 다음을 수행합니다.

1. 목록에서 복구 지점을 선택하고 **확인** 을 선택하여 복원 지점 절차를 완료합니다.

    ![전체 복구 지점 선택](./media/backup-azure-sql-database/choose-full-recovery-point.png)

    >[!NOTE]
    > 기본값으로 최근 30일 동안의 복구 지점이 표시됩니다. **필터** 를 선택하고 사용자 지정 범위를 선택하여 30일보다 오래된 복구 지점을 표시할 수 있습니다.

### <a name="restore-databases-with-large-number-of-files"></a>파일 개수가 많은 데이터베이스 복원

데이터베이스에 있는 파일의 전체 문자열 크기가 [특정 한도](backup-sql-server-azure-troubleshoot.md#size-limit-for-files)보다 큰 경우 Azure Backup는 데이터베이스 파일 목록을 다른 PIT 구성 요소에 저장하므로 복원 작업 중에 대상 복원 경로를 설정할 수 없습니다. 대신 파일은 SQL 기본 경로로 복원됩니다.

  ![큰 파일이 있는 데이터베이스 복원](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="cross-region-restore"></a>지역 간 복원

복원 옵션 중 하나인 CRR(지역 간 복원)을 사용하면 Azure 쌍으로 연결된 지역인 보조 지역에서 Azure VM에 호스팅된 SQL 데이터베이스를 복원할 수 있습니다.

기능에 온보딩하려면 [시작하기 전 섹션](./backup-create-rs-vault.md#set-cross-region-restore)을 참조하세요.

CRR을 사용하도록 설정되어 있는지 확인하려면 [지역 간 복원 구성](backup-create-rs-vault.md#configure-cross-region-restore)의 지침을 따르십시오.

### <a name="view-backup-items-in-secondary-region"></a>보조 지역에서 백업 항목 보기

CRR을 사용하도록 설정된 경우 보조 지역에서 백업 항목을 볼 수 있습니다.

1. 포털에서 **Recovery Services 자격 증명 모음**  >  **백업 항목** 으로 이동합니다.
1. **보조 지역** 을 선택하여 보조 지역의 항목을 봅니다.

>[!NOTE]
>CRR 기능을 지원하는 백업 관리 유형만 목록에 표시됩니다. 현재 보조 지역에 대한 보조 지역 데이터 복원만 지원됩니다.

![보조 지역의 백업 항목](./media/backup-azure-sql-database/backup-items-secondary-region.png)

![보조 지역의 데이터베이스](./media/backup-azure-sql-database/databases-secondary-region.png)

### <a name="restore-in-secondary-region"></a>보조 지역의 복원

보조 지역 복원 사용자 환경은 주 지역 복원 사용자 환경과 유사합니다. 복원 구성 창에서 세부 정보를 구성하여 복원을 구성하는 경우 보조 지역 매개 변수만 제공하라는 메시지가 표시됩니다. 보조 지역에 자격 증명 모음이 있어야 하고, 보조 지역의 자격 증명 모음에 SQL 서버를 등록해야 합니다.

![복원 위치 및 방법](./media/backup-azure-sql-database/restore-secondary-region.png)

![진행 중인 복원 알림 트리거](./media/backup-azure-arm-restore-vms/restorenotifications.png)

>[!NOTE]
>- 복원이 트리거되고 데이터 전송 단계에서는 복원 작업을 취소할 수 없습니다.
>- 지역 간 복원 작업을 수행하는 데 필요한 역할/액세스 수준은 구독의 _백업 운영자_ 역할과 원본 및 대상 가상 머신의 _기여자(쓰기)_ 권한입니다. 백업 작업을 보기 위해 구독에서 필요한 최소 권한은 '백업 읽기 권한자'입니다.

### <a name="monitoring-secondary-region-restore-jobs"></a>보조 지역 복원 작업 모니터링

1. Azure Portal **백업 센터 백업**  >  **작업으로** 이동합니다.
1. 보조 지역의 작업을 보려면 **CrossRegionRestore에** 대한 필터 작업입니다.

   :::image type="content" source="./media/backup-azure-sql-database/backup-center-jobs-inline.png" alt-text="필터링된 Backup 작업을 보여주는 스크린샷." lightbox="./media/backup-azure-sql-database/backup-center-jobs-expanded.png":::

## <a name="next-steps"></a>다음 단계

Azure Backup에서 백업된 SQL Server 데이터베이스를 [관리 및 모니터링](manage-monitor-sql-database-backup.md)합니다.
