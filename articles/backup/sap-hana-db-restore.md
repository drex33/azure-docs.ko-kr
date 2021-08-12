---
title: Azure VM에서 SAP HANA 데이터베이스 복원
description: 이 문서에서는 Azure Virtual Machines에서 실행되는 SAP HANA 데이터베이스를 복원하는 방법에 대해 알아봅니다. 지역 간 복원을 사용하여 데이터베이스를 보조 지역으로 복원할 수도 있습니다.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: d0b1af610ffa19f2a7708ee6f96de335a1886f78
ms.sourcegitcommit: 43be2ce9bf6d1186795609c99b6b8f6bb4676f47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108279985"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Azure VM에서 SAP HANA 데이터베이스 복원

이 문서에서는 Azure Backup 서비스에서 Recovery Services 자격 증명 모음에 백업한 Azure VM(Virtual Machine)에서 실행되는 SAP HANA 데이터베이스를 복원하는 방법을 설명합니다. 복원은 개발/테스트 시나리오를 위한 데이터 복사본을 생성하거나 이전 상태로 돌아가려는 경우 사용할 수 있습니다.

SAP HANA 데이터베이스를 백업하는 방법에 대한 자세한 내용은 [Azure VM에 SAP HANA 데이터베이스 백업](./backup-azure-sap-hana-database.md)을 참조하세요.

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>특정 시점 또는 복구 지점으로 복원

Azure Backup에서는 다음과 같이 Azure VM에서 실행되는 SAP HANA 데이터베이스를 복원할 수 있습니다.

* 로그 백업을 사용하여 특정 날짜 또는 시간(초)으로 복원합니다. Azure Backup은 선택한 시간을 기준으로 복원해야 하는 적절한 전체 차등 백업 및 로그 백업 체인을 자동으로 결정합니다.

* 특정 복구 지점으로 복원하려면 특정 전체 또는 차등 백업을 복원합니다.

## <a name="prerequisites"></a>사전 요구 사항

데이터베이스를 복원하기 전에 다음에 유의하세요.

* 동일한 지역에 있는 SAP HANA 인스턴스로만 데이터베이스를 복원할 수 있습니다.

* 대상 인스턴스를 원본과 동일한 자격 증명 모음에 등록해야 합니다.

* Azure Backup은 동일한 VM에 있는 두 개의 서로 다른 SAP HANA 인스턴스를 구분할 수 없습니다. 따라서 한 인스턴스의 데이터를 동일한 VM의 다른 인스턴스에 복원하는 것은 불가능합니다.

* 대상 SAP HANA 인스턴스를 복원할 준비가 되었는지 확인하려면 해당 **백업 준비** 상태를 확인합니다.

  1. 대상 SAP HANA 인스턴스가 등록된 자격 증명 모음을 엽니다.

  1. 자격 증명 모음 대시보드의 **시작** 아래에서 **백업** 을 선택합니다.

      ![자격 증명 모음 대시보드에서 백업](media/sap-hana-db-restore/getting-started-backup.png)

  1. **백업** 의 **백업할 항목** 아래에서 **Azure VM의 SAP HANA** 를 선택합니다.

      ![Azure VM의 SAP HANA 선택](media/sap-hana-db-restore/sap-hana-backup.png)

  1. **VM에서 DB 검색** 아래에서 **자세히 보기** 를 선택합니다.

      ![세부 정보 보기](media/sap-hana-db-restore/view-details.png)

  1. 대상 VM의 **백업 준비 상태** 를 검토합니다.

      ![보호되는 서버](media/sap-hana-db-restore/protected-servers.png)

* SAP HANA가 지원하는 복원 유형에 대해 자세히 알아보려면 SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148)을 참조하세요.

## <a name="restore-a-database"></a>데이터베이스 복원

복원하려면 다음 권한이 필요합니다.

* 복원하려는 자격 증명 모음의 **Backup 운영자** 권한
* 백업되는 원본 VM에 대한 **참가자(쓰기)** 액세스 권한
* 대상 VM에 대한 **기여자(쓰기)** 액세스 권한:
  * 동일한 VM으로 복원하는 경우 이 VM이 원본 VM입니다.
  * 대체 위치로 복원하는 경우 이 VM이 새 대상 VM입니다.

1. 복원할 SAP HANA 데이터베이스가 등록된 자격 증명 모음을 엽니다.

1. 자격 증명 모음 대시보드의 **보호된 항목** 아래에서 **백업 항목** 을 선택합니다.

    ![Backup 항목](media/sap-hana-db-restore/backup-items.png)

1. **백업 항목** 의 **백업 관리 유형** 에서 **Azure VM의 SAP HANA** 를 선택합니다.

    ![백업 관리 유형](media/sap-hana-db-restore/backup-management-type.png)

1. 복원할 데이터베이스 선택

    ![복원할 데이터베이스](media/sap-hana-db-restore/database-to-restore.png)

1. 데이터베이스 메뉴를 검토합니다. 다음과 같은 데이터베이스 백업에 대한 정보를 제공합니다.

    * 가장 오래된 복원 지점 및 최신 복원 지점

    * 데이터베이스에 대한 지난 24시간 및 72시간 동안의 로그 백업 상태

    ![데이터베이스 메뉴](media/sap-hana-db-restore/database-menu.png)

1. **DB 복원** 을 선택합니다.

1. **복원 구성** 에서 데이터를 복원할 위치(또는 방법)를 지정합니다.

    * **대체 위치**: 대체 위치에 데이터베이스를 복원하고 원래 원본 데이터베이스를 유지합니다.

    * **DB 덮어쓰기**: 원래 원본과 동일한 SAP HANA 인스턴스에 데이터를 복원합니다. 이 옵션은 원래 데이터베이스를 덮어씁니다.

      ![복원 구성](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>대체 위치에 복원

1. **복원 구성** 메뉴의 **복원할 위치** 아래에서 **대체 위치** 를 선택합니다.

    ![대체 위치에 복원](media/sap-hana-db-restore/restore-alternate-location.png)

1. 데이터베이스를 복원할 SAP HANA 호스트 이름 및 인스턴스를 선택합니다.
1. **백업 준비 상태** 를 확인하여 대상 SAP HANA 인스턴스가 복원할 준비가 되었는지 확인합니다. 자세한 내용은 [사전 요구 사항 섹션](#prerequisites)을 참조하세요.
1. **복원된 DB 이름** 상자에 대상 데이터베이스의 이름을 입력합니다.

    > [!NOTE]
    > SDC(Single Database Container) 복원은 다음 [검사](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore)를 수행해야 합니다.

1. 해당되는 경우 **선택한 HANA 인스턴스에 이름이 같은 DB가 있으면 덮어쓰기** 를 선택합니다.
1. **확인** 을 선택합니다.

    ![복원 구성 - 최종 화면](media/sap-hana-db-restore/restore-configuration-last.png)

1. **복원 지점 선택** 에서 **로그(특정 시점 복원)** 을 선택하여 [특정 시점으로 복원](#restore-to-a-specific-point-in-time)합니다. 또는 **전체 및 차등** 을 선택하여 [특정 복구 지점으로 복원](#restore-to-a-specific-recovery-point)합니다.

### <a name="restore-and-overwrite"></a>복원 및 덮어쓰기

1. **복원 구성** 메뉴의 **복원할 위치** 아래에서 **DB 덮어쓰기** > **확인** 을 선택합니다.

    ![DB 덮어쓰기](media/sap-hana-db-restore/overwrite-db.png)

1. **복원 지점 선택** 에서 **로그(특정 시점 복원)** 을 선택하여 [특정 시점으로 복원](#restore-to-a-specific-point-in-time)합니다. 또는 **전체 및 차등** 을 선택하여 [특정 복구 지점으로 복원](#restore-to-a-specific-recovery-point)합니다.

### <a name="restore-as-files"></a>파일로 복원

백업 데이터를 데이터베이스가 아닌 파일로 복원하려면 **파일로 복원** 을 선택합니다. 파일이 지정된 경로에 덤프되면 이러한 파일을 데이터베이스로 복원하려는 SAP HANA 컴퓨터로 가져올 수 있습니다. 이러한 파일을 임의의 컴퓨터로 이동할 수 있으므로 이제 구독 및 지역 간에 데이터를 복원할 수 있습니다.

1. **복원 구성** 메뉴의 **복원 위치 및 방법** 아래에서 **파일로 복원** 을 선택합니다.
1. 백업 파일을 복원할 **호스트**/HANA 서버 이름을 선택합니다.
1. **서버의 대상 경로** 에서 2단계에서 선택한 서버에 폴더 경로를 입력합니다. 서비스에서 필요한 모든 백업 파일을 덤프하는 위치입니다.

    덤프되는 파일은 다음과 같습니다.

    * 데이터베이스 백업 파일
    * 카탈로그 파일
    * JSON 메타데이터 파일(관련된 각 백업 파일)

    일반적으로 네트워크 공유 경로 또는 탑재된 Azure 파일 공유 경로가 대상 경로로 지정되면 이를 통해 동일한 네트워크의 다른 컴퓨터 또는 동일한 Azure 파일 공유가 탑재된 다른 컴퓨터에서 이러한 파일에 더 쉽게 액세스할 수 있습니다.

    >[!NOTE]
    >등록된 대상 VM에 탑재된 Azure 파일 공유에서 데이터베이스 백업 파일을 복원하려면 루트 계정에 Azure 파일 공유에 대한 읽기/쓰기 권한이 있는지 확인합니다.

    ![대상 경로 선택](media/sap-hana-db-restore/restore-as-files.png)

1. 모든 백업 파일 및 폴더를 복원할 **복원 지점** 을 선택합니다.

    ![복원 지점 선택](media/sap-hana-db-restore/select-restore-point.png)

1. 선택한 복원 지점과 연결된 모든 백업 파일은 대상 경로로 덤프됩니다.
1. 선택한 복원 지점 유형(**특정 시점** 또는 **전체 및 차등**)에 따라 대상 경로에 만들어진 하나 이상의 폴더를 볼 수 있습니다. `Data_<date and time of restore>`라는 폴더 중 하나에는 전체 백업이 포함되고, `Log`라는 다른 폴더에는 로그 백업 및 기타 백업(예: 차등 및 증분)이 포함됩니다.
1. 복원된 이러한 파일을 데이터베이스로 복원하려는 SAP HANA 서버로 이동합니다.
1. 그런 다음, 다음 단계를 수행합니다.
    1. 다음 명령을 사용하여 백업 파일이 저장되는 폴더/디렉터리에 대한 사용 권한을 설정합니다.

        ```bash
        chown -R <SID>adm:sapsys <directory>
        ```

    1. 다음 명령 집합을 `<SID>adm`으로 실행합니다.

        ```bash
        su - <sid>adm
        ```

    1. 복원에 사용할 카탈로그 파일을 생성합니다. 전체 백업에 대한 JSON 메타데이터 파일에서 **BackupId** 를 추출합니다. 이는 나중에 복원 작업에서 사용됩니다. 전체 및 로그 백업이 서로 다른 폴더에 있는지 확인하고, 이러한 폴더에서 카탈로그 파일과 JSON 메타데이터 파일을 삭제합니다.

        ```bash
        hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
        ```

        위의 명령에서 다음을 수행합니다.

        * `<DataFileDir>` - 전체 백업이 포함된 폴더
        * `<LogFilesDir>` - 로그 백업, 차등 및 증분 백업(있는 경우)이 포함된 폴더
        * `<PathToPlaceCatalogFile>` - 생성된 카탈로그 파일을 배치해야 하는 폴더

    1. HANA Studio를 통해 새로 생성된 카탈로그 파일을 사용하여 복원하거나 새로 생성된 이 카탈로그를 사용하여 HDBSQL 복원 쿼리를 실행합니다. HDBSQL 쿼리는 아래에 나와 있습니다.

    * 특정 시점으로 복원하려면 다음을 수행합니다.

        복원된 새 데이터베이스를 만드는 경우 HDBSQL 명령을 실행하여 새 `<DatabaseName>` 데이터베이스를 만든 다음, 복원에 사용할 데이터베이스를 중지합니다. 그러나 기존 데이터베이스만 복원하는 경우 HDBSQL 명령을 실행하여 데이터베이스를 중지합니다.

        그런 다음, 다음 명령을 실행하여 데이터베이스를 복원합니다.

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` - 복원하려는 새 데이터베이스 또는 기존 데이터베이스의 이름
        * `<Timestamp>` - 특정 시점 복원에 대한 정확한 타임스탬프
        * `<DatabaseName@HostName>` - 백업이 복원에 사용되는 데이터베이스의 이름 및 이 데이터베이스가 있는 **호스트**/SAP HANA 서버 이름. `USING SOURCE <DatabaseName@HostName>` 옵션은 데이터 백업(복원에 사용됨)이 대상 SAP HANA 컴퓨터와 다른 SID 또는 이름을 사용하는 데이터베이스임을 지정합니다. 따라서 백업이 수행된 동일한 HANA 서버에서 수행되는 복원에 대해서는 이를 지정할 필요가 없습니다.
        * `<PathToGeneratedCatalogInStep3>` - **C단계** 에서 생성된 카탈로그 파일의 경로
        * `<DataFileDir>` - 전체 백업이 포함된 폴더
        * `<LogFilesDir>` - 로그 백업, 차등 및 증분 백업(있는 경우)이 포함된 폴더
        * `<BackupIdFromJsonFile>` - **C단계** 에서 추출된 **BackupId**

    * 특정 전체 또는 차등 백업으로 복원하려면 다음을 수행합니다.

        복원된 새 데이터베이스를 만드는 경우 HDBSQL 명령을 실행하여 새 `<DatabaseName>` 데이터베이스를 만든 다음, 복원에 사용할 데이터베이스를 중지합니다. 그러나 기존 데이터베이스만 복원하는 경우 HDBSQL 명령을 실행하여 데이터베이스를 중지합니다.

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` - 복원하려는 새 데이터베이스 또는 기존 데이터베이스의 이름
        * `<Timestamp>` - 특정 시점 복원에 대한 정확한 타임스탬프
        * `<DatabaseName@HostName>` - 백업이 복원에 사용되는 데이터베이스의 이름 및 이 데이터베이스가 있는 **호스트**/SAP HANA 서버 이름. `USING SOURCE <DatabaseName@HostName>` 옵션은 데이터 백업(복원에 사용됨)이 대상 SAP HANA 컴퓨터와 다른 SID 또는 이름을 사용하는 데이터베이스임을 지정합니다. 따라서 백업이 수행된 동일한 HANA 서버에서 수행되는 복원에 대해서는 이를 지정할 필요가 없습니다.
        * `<PathToGeneratedCatalogInStep3>` - **C단계** 에서 생성된 카탈로그 파일의 경로
        * `<DataFileDir>` - 전체 백업이 포함된 폴더
        * `<LogFilesDir>` - 로그 백업, 차등 및 증분 백업(있는 경우)이 포함된 폴더
        * `<BackupIdFromJsonFile>` - **C단계** 에서 추출된 **BackupId**

### <a name="restore-to-a-specific-point-in-time"></a>특정 시점으로 복원

복원 유형으로 **로그(시점)** 을 선택한 경우 다음을 수행합니다.

1. 로그 그래프에서 복구 지점을 선택하고 **확인** 을 선택하여 복원 지점을 선택합니다.

    ![복원 지점](media/sap-hana-db-restore/restore-point.png)

1. **복원** 메뉴에서 **복원** 을 선택하여 복원 작업을 시작합니다.

    ![복원 선택](media/sap-hana-db-restore/restore-restore.png)

1. **알림** 영역에서 복원 진행률을 추적하거나 데이터베이스 메뉴에서 **복원 작업** 을 선택하여 추적합니다.

    ![복원 트리거 성공](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>특정 복구 지점으로 복원

복원 유형으로 **전체 및 차등** 을 선택한 경우 다음을 수행합니다.

1. 목록에서 복구 지점을 선택하고 **확인** 을 선택하여 복원 지점을 선택합니다.

    ![특정 복구 지점 복원](media/sap-hana-db-restore/specific-recovery-point.png)

1. **복원** 메뉴에서 **복원** 을 선택하여 복원 작업을 시작합니다.

    ![복원 작업 시작](media/sap-hana-db-restore/restore-specific.png)

1. **알림** 영역에서 복원 진행률을 추적하거나 데이터베이스 메뉴에서 **복원 작업** 을 선택하여 추적합니다.

    ![복원 진행률](media/sap-hana-db-restore/restore-progress.png)

    > [!NOTE]
    > MDC(Multiple Database Container) 복원에서는 시스템 DB가 대상 인스턴스로 복원된 후, 등록 전 스크립트를 다시 실행해야 합니다. 그래야만 후속 테넌트 DB 복원 작업이 성공합니다. 자세한 내용은 [문제 해결 – MDC 복원](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore)을 참조하세요.

## <a name="cross-region-restore"></a>지역 간 복원

복원 옵션 중 하나인 CRR(지역 간 복원)을 사용하면 Azure 쌍으로 연결된 지역인 보조 지역에서 Azure VM에 호스트된 SAP HANA 데이터베이스를 복원할 수 있습니다.

미리 보기 중에 기능에 등록하려면 [시작하기 전에 섹션](./backup-create-rs-vault.md#set-cross-region-restore)을 참조하십시오.

CRR을 사용하도록 설정되어 있는지 확인하려면 [지역 간 복원 구성](backup-create-rs-vault.md#configure-cross-region-restore)의 지침을 따르십시오.

### <a name="view-backup-items-in-secondary-region"></a>보조 지역에서 백업 항목 보기

CRR을 사용하도록 설정된 경우 보조 지역에서 백업 항목을 볼 수 있습니다.

1. 포털에서 **Recovery Services 자격 증명 모음**  >  **백업 항목** 으로 이동합니다.
1. **보조 지역** 을 선택하여 보조 지역의 항목을 봅니다.

>[!NOTE]
>CRR 기능을 지원하는 백업 관리 유형만 목록에 표시됩니다. 현재 보조 지역에 대한 보조 지역 데이터 복원만 지원됩니다.

![보조 지역의 백업 항목](./media/sap-hana-db-restore/backup-items-secondary-region.png)

![보조 지역의 데이터베이스](./media/sap-hana-db-restore/databases-secondary-region.png)

### <a name="restore-in-secondary-region"></a>보조 지역의 복원

보조 지역 복원 사용자 환경은 주 지역 복원 사용자 환경과 유사합니다. 복원 구성 창에서 세부 정보를 구성하여 복원을 구성하는 경우 보조 지역 매개 변수만 제공하라는 메시지가 표시됩니다.

![복원 위치 및 방법](./media/sap-hana-db-restore/restore-secondary-region.png)

>[!NOTE]
>보조 지역의 가상 네트워크는 고유하게 할당해야 하며 해당 리소스 그룹의 다른 VM에는 사용할 수 없습니다.

![진행 중인 복원 알림 트리거](./media/backup-azure-arm-restore-vms/restorenotifications.png)

>[!NOTE]
>
>* 복원이 트리거되고 데이터 전송 단계에서는 복원 작업을 취소할 수 없습니다.
>* 보조 지역에서 복원하는 데 필요한 Azure 역할은 주 지역의 경우와 동일합니다.

### <a name="monitoring-secondary-region-restore-jobs"></a>보조 지역 복원 작업 모니터링

1. 포털에서 **Recovery Services 자격 증명 모음**  >  **백업 작업** 으로 이동합니다.
1. **보조 지역** 을 선택하여 보조 지역의 항목을 봅니다.

    ![필터링된 백업 작업](./media/sap-hana-db-restore/backup-jobs-secondary-region.png)

## <a name="next-steps"></a>다음 단계

* Azure Backup을 사용하여 백업된 SAP HANA 데이터베이스를 관리하는 [방법 알아보기](sap-hana-db-manage.md)
