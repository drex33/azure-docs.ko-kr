---
title: Azure Backup Server를 사용하여 SQL Server 백업
description: 이 문서에서는 MABS(Microsoft Azure Backup Server)를 사용하여 SQL Server 데이터베이스를 백업하는 구성을 알아봅니다.
ms.topic: conceptual
ms.date: 07/28/2021
ms.openlocfilehash: f97fc81b051b8c7d76ee1769b82ad4a2d489357b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528122"
---
# <a name="back-up-sql-server-to-azure-by-using-azure-backup-server"></a>Azure Backup Server를 사용하여 Azure에 SQL Server 백업

MABS(Microsoft Azure Backup Server)는 SQL Server 데이터베이스에 대한 백업 및 복구를 제공합니다. SQL Server 데이터베이스 백업뿐만 아니라 SQL Server 컴퓨터의 시스템 백업 또는 완전 백업을 실행할 수 있습니다. MABS가 보호할 수 있는 것은 다음과 같습니다.

- 독립 실행형 SQL Server 인스턴스
- SQL Server FCI(장애 조치(failover) 클러스터 인스턴스)

>[!Note]
>MABS v3 UR2는 CSV(클러스터 공유 볼륨)를 사용하여 SQL Server FCI(장애 조치(failover) 클러스터 인스턴스)를 지원합니다.
>
>Azure에서 스토리지 공간 다이렉트를 사용하는 SQL Server FCI 및 Azure 공유 디스크를 사용하는 SQL Server FCI의 보호는 이 기능에서 지원됩니다. Azure VM에 배포된 SQL FCI 인스턴스를 보호하기 위해 DPM 서버를 Azure VM에 배포해야 합니다.
>
>다음의 기본 설정을 가진 SQL Server AlwaysOn 가용성 그룹:
>- 보조 사용
>- 보조만
>- 주
>- 임의의 복제본

SQL Server 데이터베이스를 백업하고 Azure에서 복구하려면 다음을 수행합니다.

1. Azure에서 SQL Server 데이터베이스를 보호하기 위한 백업 정책을 만듭니다.
1. Azure에서 주문형 백업 복사본을 만듭니다.
1. Azure에서 데이터베이스를 복구합니다.

## <a name="prerequisites-and-limitations"></a>필수 구성 요소 및 제한 사항

* 데이터베이스에 원격 파일 공유의 파일이 포함되어 있으면 오류 ID 104를 나타내며 보호가 실패합니다. MABS는 원격 파일 공유에 대한 SQL Server 데이터 보호를 지원하지 않습니다.
* MABS는 원격 SMB 공유에 저장되는 데이터베이스를 보호할 수 없습니다.
* [가용성 그룹 복제본이 읽기 전용으로 구성](/sql/database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server)되었는지 확인합니다.
* SQL Server의 Sysadmin 그룹에 **NTAuthority\System** 시스템 계정을 명시적으로 추가해야 합니다.
* 부분적으로 포함된 데이터베이스를 대체 위치에 복구하는 경우 대상 SQL 인스턴스에서 [포함된 데이터베이스](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#enable) 기능을 사용하도록 설정했는지 확인해야 합니다.
* 파일 스트림 데이터베이스를 대체 위치에 복구하는 경우 대상 SQL 인스턴스에서 [파일 스트림 데이터베이스](/sql/relational-databases/blob/enable-and-configure-filestream) 기능을 사용하도록 설정했는지 확인해야 합니다.
* SQL Server AlwaysOn 보호:
  * 보호 그룹을 만드는 과정에서 조회를 실행하면 MABS가 가용성 그룹을 검색합니다.
  * MABS는 장애 조치(failover)를 검색하고 데이터베이스를 계속 보호합니다.
  * MABS는 SQL Server 인스턴스에 대해 다중 사이트 클러스터 구성을 지원합니다.
* AlwaysOn 기능을 사용하는 데이터베이스를 보호하는 경우 MABS에는 다음과 같은 제한 사항이 있습니다.
  * MABS는 다음과 같은 백업 기본 설정에 따라 SQL Server에서 설정된 가용성 그룹의 백업 정책을 적용합니다.
    * 보조 사용 - 주 복제본이 유일한 온라인 복제본인 경우를 제외하고는 보조 복제본에 대한 백업이 수행됩니다. 보조 복제본을 여러 개 사용할 수 있는 경우 백업 우선 순위가 가장 높은 노드가 백업을 위해 선택됩니다. 주 복제본만 사용할 수 있는 경우에는 주 복제본에서 백업이 수행됩니다.
    * 보조만 - 주 복제본에 대해서는 백업이 수행되지 않습니다. 주 복제본이 유일한 온라인 복제본인 경우 백업이 수행되지 않습니다.
    * 주 - 항상 주 복제본 백업이 수행됩니다.
    * 임의의 복제본 - 가용성 그룹의 모든 가용성 복제본에 대해 백업을 수행할 수 있습니다. 백업할 노드는 각 노드의 백업 우선 순위에 따라 결정됩니다.
  * 다음 사항에 유의하세요.
    * 읽기 가능한 모든 복제본(주 복제본, 동기 보조 복제본, 비동기 보조 복제본)에서 백업을 수행할 수 있습니다.
    * 백업에서 복제본이 제외된 경우(예: **복제본 제외** 가 사용하도록 설정되거나 복제본이 읽을 수 없음으로 표시된 경우) 해당 복제본은 어떤 옵션에서도 백업을 위해 선택되지 않습니다.
    * 복제본을 여러 개 사용할 수 있으며 읽을 수 있는 경우 백업 우선 순위가 가장 높은 노드가 백업을 위해 선택됩니다.
    * 선택한 노드에서 백업에 실패할 경우 백업 작업이 실패합니다.
    * 원래 위치로 복구할 수 없습니다.
* SQL Server 2014 이상 백업 문제:
  * SQL Server 2014에는 [Microsoft Azure Blob Storage에서 온-프레미스 SQL Server에 대한 데이터베이스](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)를 만드는 기능이 새로 추가되었습니다. MABS를 사용하여 이 구성을 보호할 수 없습니다.
  * SQL AlwaysOn 옵션의 “보조 사용” 백업 기본 설정에는 몇 가지 알려진 이슈가 있습니다. MABS는 항상 보조 복제본에서 백업을 수행합니다. 보조 복제본을 찾을 수 없는 경우 백업에 실패합니다.

## <a name="before-you-start"></a>시작하기 전에

시작하기 전에 [Azure Backup Server를 설치 및 준비](backup-azure-microsoft-azure-backup.md)해야 합니다.

## <a name="create-a-backup-policy"></a>백업 정책 만들기

Azure에서 SQL Server 데이터베이스를 보호하려면 먼저 백업 정책을 만듭니다.

1. Azure Backup Server에서 **보호** 작업 영역을 선택합니다.
1. **새로 만들기** 를 선택하여 보호 그룹을 만듭니다.

    ![Azure Backup Server에서 보호 그룹 만들기](./media/backup-azure-backup-sql/protection-group.png)
1. 시작 페이지에서 보호 그룹을 만드는 방법에 대한 지침을 검토합니다. 그런 후 **다음** 을 선택합니다.
1. 보호 그룹 유형에서 **서버** 를 선택합니다.

    ![서버 보호 그룹 유형 선택](./media/backup-azure-backup-sql/pg-servers.png)
1. 백업하려는 데이터베이스가 있는 SQL Server 인스턴스를 펼칩니다. 해당 서버에서 백업할 수 있는 다양한 데이터 원본이 표시됩니다. **모든 SQL 공유** 를 펼친 다음 백업하려는 데이터베이스를 선택합니다. 이 예제에서는 ReportServer$MSDPM2012 및 ReportServer$MSDPM2012TempDB를 선택합니다. **다음** 을 선택합니다.

    ![SQL Server 데이터베이스 선택](./media/backup-azure-backup-sql/pg-databases.png)
1. 보호 그룹의 이름을 지정한 다음 **온라인 보호** 를 선택합니다.

    ![데이터 보호 방법 선택 - 단기 디스크 보호 또는 온라인 Azure 보호](./media/backup-azure-backup-sql/pg-name.png)
1. **단기 목표 지정** 페이지에서 필요한 입력을 포함하여 디스크에 백업 지점을 만듭니다.

    이 예제에서는 **보존 범위** 를 ‘5일’로 설정합니다. 백업 **동기화 빈도** 는 ‘15분’마다 한 번으로 설정됩니다. **빠른 전체 백업** 은 ‘오후 8시’로 설정됩니다.

    ![백업 보호의 단기 목표 설정](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > 이 예제에서 백업 지점은 매일 오후 8시에 생성됩니다. 전날 오후 8시 백업 지점 이후 수정된 데이터가 전송됩니다. 이 프로세스를 **빠른 전체 Backup** 이라고 합니다. 트랜잭션 로그를 15분마다 동기화하지만 오후 9시에 데이터베이스를 복구해야 하는 경우 마지막 빠른 전체 백업 지점에서 로그를 재생하여 지점을 만듭니다(예제에서는 오후 8시).
   >
   >

1. **다음** 을 선택합니다. MABS는 사용할 수 있는 전체 스토리지 공간을 보여 줍니다. 또한 잠재적인 디스크 공간 사용률을 보여 줍니다.

    ![MABS에서 디스크 할당 설정](./media/backup-azure-backup-sql/pg-storage.png)

    기본적으로 MABS는 데이터 원본(SQL Server 데이터베이스)당 하나의 볼륨을 만듭니다. 볼륨은 초기 백업 복사본에 사용됩니다. 이 구성에서 LDM(논리 디스크 관리자)은 MABS 보호를 데이터 원본(SQL Server 데이터베이스) 300개로 제한합니다. 이 제한을 해결하려면 **DPM 스토리지 풀에 데이터 공동 배치** 를 선택합니다. 이 옵션을 사용하는 경우 MABS는 여러 데이터 원본에 하나의 볼륨을 사용합니다. 이렇게 설정하면 MABS에서 최대 2,000개의 SQL Server 데이터베이스를 보호할 수 있습니다.

    **볼륨 자동 증가** 를 선택하는 경우 MABS는 프로덕션 데이터가 증가함에 따라 증가한 백업 볼륨을 처리할 수 있습니다. **볼륨 자동 증가** 를 선택하지 않은 경우 MABS는 백업 스토리지를 보호 그룹의 데이터 원본으로 제한합니다.
1. 관리자인 경우 이 초기 백업을 **네트워크를 통해 자동으로** 전송하도록 선택하고 전송 시간을 선택할 수 있습니다. 또는 **수동으로** 백업 전송을 선택합니다. 그런 후 **다음** 을 선택합니다.

    ![MABS에서 복제본 생성 방법 선택](./media/backup-azure-backup-sql/pg-manual.png)

    초기 백업 복사본은 전체 데이터 원본(SQL Server 데이터베이스)을 전송해야 합니다. 백업 데이터는 프로덕션 서버(SQL Server 컴퓨터)에서 MABS로 이동합니다. 백업이 큰 경우 네트워크를 통해 데이터를 전송하면 대역폭이 정체될 수 있습니다. 이런 이유로 관리자는 이동식 미디어를 사용하여 초기 백업을 **수동으로** 전송하도록 선택할 수 있습니다. 또는 지정된 시간에 **네트워크를 통해 자동으로** 데이터를 전송할 수 있습니다.

    초기 백업이 완료되면 백업은 초기 백업 복사본에 대해 증분 방식으로 계속됩니다. 증분 백업은 크기가 작으며 네트워크를 통해 간편하게 전송될 수 있습니다.
1. 일관성 확인을 실행할 시간을 선택합니다. 그런 후 **다음** 을 선택합니다.

    ![일관성 확인을 실행할 시간 선택](./media/backup-azure-backup-sql/pg-consistent.png)

    MABS는 백업 지점의 무결성에 대해 일관성 확인을 실행할 수 있습니다. 프로덕션 서버(예제에서는 SQL Server 컴퓨터)의 백업 파일과 MABS에서 해당 파일에 대해 백업된 데이터의 체크섬을 계산합니다. 검사를 통해 충돌을 발견하면 MABS의 백업된 파일이 손상된 것으로 간주됩니다. MABS는 체크섬 불일치에 해당하는 블록을 전송하여 백업된 데이터를 수정합니다. 일관성 확인은 성능 집약적인 작업이므로 관리자는 일관성 확인을 예약하거나 자동으로 실행하도록 선택할 수 있습니다.
1. Azure에서 보호할 데이터 원본을 선택합니다. 그런 후 **다음** 을 선택합니다.

    ![Azure에서 보호할 데이터 원본 선택](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. 관리자인 경우 조직의 정책에 맞는 백업 일정 및 보존 정책을 선택할 수 있습니다.

    ![일정 및 보존 정책 선택](./media/backup-azure-backup-sql/pg-schedule.png)

    예제에서는 매일 오후 12시와 오후 8시에 백업이 수행됩니다.

    > [!TIP]
    > 빠른 복구를 위해 디스크에 몇 가지 단기 복구 지점이 유지됩니다. 이러한 복구 지점은 운영 복구에 사용됩니다. Azure는 높은 SLA 및 보장된 가용성을 제공하는 적절한 오프사이트 위치의 역할을 합니다.
    >
    > DPM(Data Protection Manager)을 사용하여 로컬 디스크 백업이 완료된 이후 시간으로 Azure Backup을 예약합니다. 이 연습을 수행하면 최신 디스크 백업이 Azure에 복사됩니다.
    >

1. 보존 정책 일정을 선택합니다. 보존 정책의 작동 방식에 대한 자세한 내용은 [Azure Backup을 사용하여 테이프 인프라 대체](backup-azure-backup-cloud-as-tape.md)를 참조하세요.

    ![MABS에서 보존 정책 선택](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    이 예제에서는 다음이 적용됩니다.

    * 매일 오후 12시와 오후 8시에 백업이 수행됩니다. 백업은 180일 동안 유지됩니다.
    * 토요일 오후 12시의 백업은 104주 동안 유지됩니다.
    * 매월 마지막 토요일 오후 12시의 백업은 60개월 동안 유지됩니다.
    * 3월 마지막 토요일 오후 12시의 백업은 10년 동안 유지됩니다.

    보존 정책을 선택한 후 **다음** 을 선택합니다.
1. 초기 백업 복사본을 Azure에 전송하는 방법을 선택합니다.

    * **네트워크를 통해 자동으로** 옵션은 백업 일정에 따라 데이터를 Azure로 전송합니다.
    * **오프라인 백업** 에 대한 자세한 내용은 [오프라인 백업 개요](offline-backup-overview.md)를 참조하세요.

    전송 메커니즘을 선택한 후 **다음** 을 선택합니다.
1. **요약** 페이지에서 정책 세부 사항을 검토합니다. 그런 다음 **그룹 만들기** 를 선택합니다. **닫기** 를 선택하고 **모니터링** 작업 영역에서 작업 진행률을 볼 수 있습니다.

    ![보호 그룹 만들기 진행률](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>SQL Server 데이터베이스의 주문형 백업 복사본 만들기

첫 번째 백업이 발생하면 복구 지점이 생성됩니다. 예약 실행을 대기하는 대신 복구 지점 만들기를 수동으로 트리거할 수 있습니다.

1. 보호 그룹에서 데이터베이스 상태가 **정상** 인지 확인합니다.

    ![데이터베이스 상태를 표시하는 보호 그룹](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. 데이터베이스를 마우스 오른쪽 단추로 클릭한 후 **복구 지점 만들기** 를 선택합니다.

    ![온라인 복구 지점 만들기 선택](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. 드롭다운 메뉴에서 **온라인 보호** 를 선택합니다. 그런 다음 **확인** 을 선택하여 Azure에서 복구 지점 만들기를 시작합니다.

    ![Azure에서 복구 지점 만들기 시작](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. **모니터링** 작업 영역에서 작업 진행률을 볼 수 있습니다.

    ![모니터링 콘솔에서 작업 진행률 보기](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Azure에서 SQL Server 데이터베이스 복구

Azure에서 SQL Server 데이터베이스와 같은 보호된 엔터티를 복구하려면 다음을 수행합니다.

1. DPM 서버 관리 콘솔을 엽니다. **복구** 작업 영역으로 이동하여 DPM에서 백업하는 서버를 확인합니다. 데이터베이스(예제에서는 ReportServer$MSDPM2012)를 선택합니다. **온라인** 으로 끝나는 **복구 시간** 을 선택합니다.

    ![복구 지점 선택](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. 데이터베이스 이름을 마우스 오른쪽 단추로 클릭하고 **복구** 를 선택합니다.

    ![Azure에서 데이터베이스 복구](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. DPM에서는 복구 지점에 대한 세부 정보를 보여줍니다. **다음** 을 선택합니다. 데이터베이스를 덮어쓰려면 복구 형식 **SQL Server의 원본 인스턴스에 복구** 를 선택합니다. 그런 후 **다음** 을 선택합니다.

    ![원래 위치로 데이터베이스 복구](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    예제에서는 DPM을 사용하여 데이터베이스를 다른 SQL Server 인스턴스 또는 독립 실행형 네트워크 폴더에 복구할 수 있습니다.
1. **복구 옵션 지정** 페이지에서 복구 옵션을 선택할 수 있습니다. 예를 들어 **네트워크 대역폭 사용량 제한** 을 선택하여 복구에 사용되는 대역폭을 제한할 수 있습니다. 그런 후 **다음** 을 선택합니다.
1. **요약** 페이지에 현재 복구 구성이 표시됩니다. **복구** 를 선택합니다.

    복구 상태는 복구되는 데이터베이스를 표시합니다. **닫기** 를 선택하여 마법사를 닫고 **모니터링** 작업 영역에서 진행률을 볼 수 있습니다.

    ![복구 프로세스 시작](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    복구가 완료되면 복원된 데이터베이스는 애플리케이션과 일치합니다.

### <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure Backup FAQ](backup-azure-backup-faq.yml)를 참조하세요.
