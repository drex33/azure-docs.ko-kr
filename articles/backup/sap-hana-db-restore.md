---
title: Azure VM에서 SAP HANA 데이터베이스 복원
description: 이 문서에서는 Azure Virtual Machines에서 실행되는 SAP HANA 데이터베이스를 복원하는 방법에 대해 알아봅니다. 지역 간 복원을 사용하여 데이터베이스를 보조 지역으로 복원할 수도 있습니다.
ms.topic: conceptual
ms.date: 11/02/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 427dc637a0aa44ab6a0627b7a844ad6c7d3bb46e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131431558"
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

  1. Azure Portal 백업 센터로 이동하여 **+백업을** 클릭합니다.

     :::image type="content" source="./media/sap-hana-db-restore/backup-center-configure-inline.png" alt-text="대상 SAP HANA 인스턴스를 복원할 준비가 되었는지 확인하는 프로세스를 시작하는 방법을 보여주는 스크린샷" lightbox="./media/sap-hana-db-restore/backup-center-configure-expanded.png":::

  1. 데이터 원본 유형으로 **Azure VM에서 SAP HANA** 선택하고 SAP HANA 인스턴스가 등록된 자격 증명 모음을 선택한 다음 **계속을** 클릭합니다.

     :::image type="content" source="./media/sap-hana-db-restore/hana-select-vault.png" alt-text="Azure VM에서 SAP HANA 선택하는 스크린샷":::

  1. **VM에서 DB 검색** 아래에서 **자세히 보기** 를 선택합니다.

     :::image type="content" source="./media/sap-hana-db-restore/hana-discover-databases.png" alt-text="데이터베이스 세부 정보를 보는 스크린샷.":::

  1. 대상 VM의 **백업 준비 상태** 를 검토합니다.

     :::image type="content" source="./media/sap-hana-db-restore/hana-select-virtual-machines-inline.png" alt-text="보호된 서버를 보여주는 스크린샷." lightbox="./media/sap-hana-db-restore/hana-select-virtual-machines-expanded.png":::

* SAP HANA가 지원하는 복원 유형에 대해 자세히 알아보려면 SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148)을 참조하세요.

## <a name="restore-a-database"></a>데이터베이스 복원

복원하려면 다음 권한이 필요합니다.

* 복원하려는 자격 증명 모음의 **Backup 운영자** 권한
* 백업되는 원본 VM에 대한 **참가자(쓰기)** 액세스 권한
* 대상 VM에 대한 **기여자(쓰기)** 액세스 권한:
  * 동일한 VM으로 복원하는 경우 이 VM이 원본 VM입니다.
  * 대체 위치로 복원하는 경우 이 VM이 새 대상 VM입니다.

1. Azure Portal **백업 센터로** 이동하여 **복원을** 클릭합니다.

   :::image type="content" source="./media/sap-hana-db-restore/backup-center-restore-inline.png" alt-text="SAP HANA 데이터베이스 복원을 시작하는 방법을 보여주는 스크린샷" lightbox="./media/sap-hana-db-restore/backup-center-restore-expanded.png":::

1. 데이터 원본 유형으로 **Azure VM에서 SAP HANA** 선택하고 복원하려는 데이터베이스를 선택한 다음 **계속을** 클릭합니다.

   :::image type="content" source="./media/sap-hana-db-restore/hana-restore-select-database.png" alt-text="백업 항목을 복원하는 방법을 보여주는 스크린샷":::

1. **복원 구성** 에서 데이터를 복원할 위치(또는 방법)를 지정합니다.

   * **대체 위치**: 대체 위치에 데이터베이스를 복원하고 원래 원본 데이터베이스를 유지합니다.

   * **DB 덮어쓰기**: 원래 원본과 동일한 SAP HANA 인스턴스에 데이터를 복원합니다. 이 옵션은 원래 데이터베이스를 덮어씁니다.

   :::image type="content" source="./media/sap-hana-db-restore/hana-restore-configuration.png" alt-text="구성 복원을 보여주는 스크린샷.":::

### <a name="restore-to-alternate-location"></a>대체 위치에 복원

1. **복원 구성** 메뉴의 **복원할 위치** 아래에서 **대체 위치** 를 선택합니다.

   :::image type="content" source="./media/sap-hana-db-restore/hana-alternate-location-recovery.png" alt-text="대체 위치로 복원하는 방법을 보여주는 스크린샷.":::

1. 데이터베이스를 복원할 SAP HANA 호스트 이름 및 인스턴스를 선택합니다.
1. **백업 준비 상태** 를 확인하여 대상 SAP HANA 인스턴스가 복원할 준비가 되었는지 확인합니다. 자세한 내용은 [사전 요구 사항 섹션](#prerequisites)을 참조하세요.
1. **복원된 DB 이름** 상자에 대상 데이터베이스의 이름을 입력합니다.

    > [!NOTE]
    > SDC(Single Database Container) 복원은 다음 [검사](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore)를 수행해야 합니다.

1. 해당되는 경우 **선택한 HANA 인스턴스에 이름이 같은 DB가 있으면 덮어쓰기** 를 선택합니다.

1. **복원 지점 선택** 에서 **로그(특정 시점 복원)** 을 선택하여 [특정 시점으로 복원](#restore-to-a-specific-point-in-time)합니다. 또는 **전체 및 차등** 을 선택하여 [특정 복구 지점으로 복원](#restore-to-a-specific-recovery-point)합니다.

### <a name="restore-and-overwrite"></a>복원 및 덮어쓰기

1. **복원 구성** 메뉴의 **복원할 위치** 아래에서 **DB 덮어쓰기** > **확인** 을 선택합니다.

   :::image type="content" source="./media/sap-hana-db-restore/hana-overwrite-database.png" alt-text="데이터베이스를 덮어쓰는 방법을 보여주는 스크린샷":::

1. **복원 지점 선택** 에서 **로그(특정 시점 복원)** 을 선택하여 [특정 시점으로 복원](#restore-to-a-specific-point-in-time)합니다. 또는 **전체 및 차등** 을 선택하여 [특정 복구 지점으로 복원](#restore-to-a-specific-recovery-point)합니다.

### <a name="restore-as-files"></a>파일로 복원

>[!Note]
>파일로 복원은 CIFS 공유에서 작동하지 않지만 NFS에서 작동합니다.

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

   :::image type="content" source="./media/sap-hana-db-restore/hana-restore-as-files.png" alt-text="대상 경로를 선택하는 방법을 보여주는 스크린샷.":::

1. 모든 백업 파일 및 폴더를 복원할 **복원 지점** 을 선택합니다.

   :::image type="content" source="./media/sap-hana-db-restore/hana-select-recovery-point-inline.png" alt-text="복원 지점을 선택하는 방법을 보여주는 스크린샷." lightbox="./media/sap-hana-db-restore/hana-select-recovery-point-expanded.png":::

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
        * `<DatabaseName@HostName>` - 백업이 복원에 사용되는 데이터베이스의 이름 및 이 데이터베이스가 있는 **호스트**/SAP HANA 서버 이름. `USING SOURCE <DatabaseName@HostName>` 옵션은 데이터 백업(복원에 사용됨)이 대상 SAP HANA 컴퓨터와 다른 SID 또는 이름을 사용하는 데이터베이스임을 지정합니다. 따라서 백업이 수행 되는 동일한 HANA 서버에서 수행 되는 복원에 대해 지정할 필요가 없습니다.
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

기능에 온보딩하려면 [시작하기 전 섹션](./backup-create-rs-vault.md#set-cross-region-restore)을 참조하세요.

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

보조 지역 복원 사용자 환경은 주 지역 복원 사용자 환경과 유사합니다. 복원 구성 창에서 세부 정보를 구성하여 복원을 구성하는 경우 보조 지역 매개 변수만 제공하라는 메시지가 표시됩니다. 보조 지역에 자격 증명 모음이 있어야 하고, 보조 지역의 자격 증명 모음에 SAP HANA 서버를 등록해야 합니다.

![복원 위치 및 방법](./media/sap-hana-db-restore/restore-secondary-region.png)

![진행 중인 복원 알림 트리거](./media/backup-azure-arm-restore-vms/restorenotifications.png)

>[!NOTE]
>* 복원이 트리거되고 데이터 전송 단계에서는 복원 작업을 취소할 수 없습니다.
>* 지역 간 복원 작업을 수행하는 데 필요한 역할/액세스 수준은 구독의 _백업 운영자_ 역할과 원본 및 대상 가상 머신의 _기여자(쓰기)_ 권한입니다. 백업 작업을 보기 위해 구독에서 필요한 최소 권한은 '백업 읽기 권한자'입니다.

### <a name="monitoring-secondary-region-restore-jobs"></a>보조 지역 복원 작업 모니터링

1. Azure Portal에서 **백업 센터**  >  **백업 작업** 으로 이동 합니다.
1. 보조 지역의 작업을 보려면 값 간 **복원** 에 대 한 **작업** 을 필터링 합니다.

   :::image type="content" source="./media/sap-hana-db-restore/hana-view-jobs-inline.png" alt-text="필터링 된 백업 작업을 보여 주는 스크린샷" lightbox="./media/sap-hana-db-restore/hana-view-jobs-expanded.png":::

## <a name="next-steps"></a>다음 단계

* Azure Backup을 사용하여 백업된 SAP HANA 데이터베이스를 관리하는 [방법 알아보기](sap-hana-db-manage.md)
