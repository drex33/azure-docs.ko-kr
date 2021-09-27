---
title: Backup 센터를 사용하여 작업 수행
description: 이 문서에서는 백업 센터를 사용하여 작업을 수행하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: e8b357c19caeb20c2a50d9a05f9d8d583bd8f640
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083623"
---
# <a name="perform-actions-using-backup-center"></a>Backup 센터를 사용하여 작업 수행

백업 센터를 사용하면 개별 자격 증명 모음으로 이동할 필요 없이 중앙 인터페이스에서 주요 백업 관련 작업을 수행할 수 있습니다. 백업 센터에서 수행할 수 있는 몇 가지 작업은 다음과 같습니다.

* 데이터 원본에 대한 백업 구성
* 백업 인스턴스 복원
* 새 자격 증명 모음 만들기
* 새 백업 정책 만들기
* 백업 인스턴스에 대한 주문형 백업 트리거
* 백업 인스턴스에 대해 백업 중지

## <a name="supported-scenarios"></a>지원되는 시나리오

* 백업 센터는 현재 Azure VM 백업, Azure VM 백업의 SQL, Azure VM 백업의 SAP HANA, Azure Files 백업 및 Azure Database for PostgreSQL 서버 백업에 대해 지원됩니다.
* 지원되는 시나리오와 지원되지 않는 시나리오 상세 목록은 [지원 매트릭스](backup-center-support-matrix.md)를 참조하세요.

## <a name="configure-backup"></a>백업 구성

Azure VM, Azure VM의 SQL, Azure VM의 SAP HANA 또는 Azure Files를 백업하는 경우 Recovery Services 자격 증명 모음을 사용해야 합니다. Azure Databases for PostgreSQL 서버를 백업하는 경우 백업 자격 증명 모음을 사용해야 합니다. 

백업하려는 데이터 원본 유형에 따라 아래 설명된 대로 적절한 지침을 따르세요.

### <a name="configure-backup-to-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 백업 구성

1. 백업 센터로 이동하여 **개요** 탭 상단에서 **+ 백업** 을 선택합니다.

    ![백업 센터 개요](./media/backup-center-actions/backup-center-overview-configure-backup.png)

2. 백업할 데이터 원본 유형을 선택합니다.

    ![VM 백업을 구성할 데이터 원본 선택](./media/backup-center-actions/backup-select-datasource-vm.png)

3. Recovery Services 자격 증명 모음을 선택하고 **계속** 을 선택합니다. 그러면 Recovery Services 자격 증명 모음에서 연결할 수 있는 것과 동일한 백업 구성 환경으로 이동합니다. [Recovery Services 자격 증명 모음을 사용하여 Azure Virtual Machines에 대한 백업을 구성하는 방법에 대해 자세히 알아봅니다](tutorial-backup-vm-at-scale.md).

### <a name="configure-backup-to-a-backup-vault"></a>백업 자격 증명 모음으로 백업 구성

1. 백업 센터로 이동하여 **개요** 탭 상단에서 **+ 백업** 을 선택합니다.
2. 백업할 데이터 원본 유형을 선택합니다(이 경우 Azure Database for PostgreSQL 서버).

    ![Azure Database for PostgreSQL 서버 백업을 구성할 데이터 원본 선택](./media/backup-center-actions/backup-select-datasource-type-postgresql.png)

3. **계속** 을 선택합니다. 그러면 백업 자격 증명 모음에서 연결할 수 있는 것과 동일한 백업 구성 환경으로 이동합니다. [백업 자격 증명 모음으로 Azure Database for PostgreSQL 서버에 대한 백업을 구성하는 방법에 대해 자세히 알아봅니다](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases).

## <a name="restore-a-backup-instance"></a>백업 인스턴스 복원

복원하려는 데이터 원본의 유형에 따라 아래 설명된 대로 적절한 지침을 따르세요.

### <a name="if-youre-restoring-from-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음에서 복원하는 경우

1. 백업 센터로 이동하여 **개요** 탭 상단에서 **복원** 을 선택합니다.

    ![VM 복원을 위한 백업 센터 개요](./media/backup-center-actions/backup-center-overview-restore.png)

2. 복원하려는 데이터 원본 유형을 선택합니다.

    ![VM 복원을 위한 데이터 원본 선택](./media/backup-center-actions/restore-select-datasource-vm.png)

3. 백업 인스턴스를 선택하고 **계속** 을 선택합니다. 그러면 Recovery Services 자격 증명 모음에서 연결할 수 있는 것과 동일한 복원 설정 환경으로 이동합니다. [Recovery Services 자격 증명 모음을 사용하여 Azure Virtual Machines를 복원하는 방법에 대해 자세히 알아봅니다](backup-azure-arm-restore-vms.md#before-you-start).

### <a name="if-youre-restoring-from-a-backup-vault"></a>백업 자격 증명 모음에서 복원하는 경우

1. 백업 센터로 이동하여 **개요** 탭 상단에서 **복원** 을 선택합니다.
2. 복원할 데이터 원본 유형을 선택합니다(이 경우 Azure Database for PostgreSQL 서버).

    ![Azure Database for PostgreSQL 서버 복원을 위한 데이터 원본 선택](./media/backup-center-actions/restore-select-datasource-postgresql.png)

3. 백업 인스턴스를 선택하고 **계속** 을 선택합니다. 그러면 Recovery Services 자격 증명 모음에서 연결할 수 있는 것과 동일한 복원 설정 환경으로 이동합니다. [백업 자격 증명 모음으로 Azure Database for PostgreSQL 서버를 복원하는 방법에 대해 자세히 알아봅니다](restore-azure-database-postgresql.md).

## <a name="create-a-new-vault"></a>새 자격 증명 모음 만들기

백업 센터로 이동하고 **개요** 탭 상단에서 **+ 자격 증명 모음** 을 선택하여 새 자격 증명 모음을 만들 수 있습니다.

![자격 증명 모음 만들기](./media/backup-center-actions/backup-center-create-vault.png)

* [Recovery Services 자격 증명 모음 만들기에 대해 자세히 알아보기](backup-create-rs-vault.md)
* [백업 자격 증명 모음 만들기에 대해 자세히 알아보기](backup-vault-overview.md)

## <a name="create-a-new-backup-policy"></a>새 백업 정책 만들기

백업하려는 데이터 원본 유형에 따라 아래 설명된 해당 지침을 따르세요.

### <a name="if-youre-backing-up-to-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 백업하는 경우

1. 백업 센터로 이동하여 **개요** 탭 상단에서 **+ 정책** 을 선택합니다.

    ![백업 정책에 대한 백업 센터 개요](./media/backup-center-actions/backup-center-overview-policy.png)

2. 백업할 데이터 원본 유형을 선택합니다.

    ![VM 백업 정책에 대한 데이터 원본 선택](./media/backup-center-actions/policy-select-datasource-vm.png)

3. Recovery Services 자격 증명 모음을 선택하고 **계속** 을 선택합니다. 그러면 Recovery Services 자격 증명 모음에서 연결할 수 있는 것과 동일한 정책 생성 환경으로 이동합니다. [Recovery Services 자격 증명 모음을 사용하여 Azure Virtual Machine에 대한 새 백업 정책을 만드는 방법에 대해 자세히 알아봅니다](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

### <a name="if-youre-backing-up-to-a-backup-vault"></a>백업 자격 증명 모음에 백업하는 경우

1. 백업 센터로 이동하여 **개요** 탭 상단에서 **+ 정책** 을 선택합니다.
2. 백업할 데이터 원본 유형을 선택합니다(이 경우 Azure Database for PostgreSQL 서버).

    ![Azure Database for PostgreSQL 서버 백업에 대한 정책에 대한 데이터 원본 선택](./media/backup-center-actions/policy-select-datasource-postgresql.png)

3. **계속** 을 선택합니다. 그러면 백업 자격 증명 모음에서 연결할 수 있는 것과 동일한 정책 생성 환경으로 이동합니다. [백업 자격 증명 모음으로 새 백업 정책을 만드는 방법에 대해 자세히 알아봅니다](backup-azure-database-postgresql.md#create-backup-policy).

## <a name="execute-an-on-demand-backup-for-a-backup-instance"></a>백업 인스턴스에 대한 주문형 백업 실행

백업 센터를 사용하면 백업 자산 전체에서 백업 인스턴스를 검색하고 요청 시 백업 작업을 실행할 수 있습니다.

보호를 중지하려면 백업 센터로 이동하여 **백업 인스턴스** 메뉴 항목을 선택합니다. 이를 선택하면 액세스 권한이 있는 모든 백업 인스턴스의 세부 정보를 볼 수 있습니다. 백업하려는 백업 인스턴스를 검색할 수 있습니다. 그리드의 항목을 마우스 오른쪽 단추로 클릭하면 가능한 작업 목록이 열립니다. 주문형 백업을 실행하려면 **지금 백업** 옵션을 선택합니다.

![주문형 백업](./media/backup-center-actions/backup-center-on-demand-backup.png)

[Azure Virtual Machines의 주문형 백업 수행에 대해 자세히 알아보기](backup-azure-manage-vms.md#run-an-on-demand-backup)

[Azure Database for PostgreSQL 서버의 주문형 백업 수행에 대해 자세히 알아보기](backup-azure-database-postgresql.md)

## <a name="stop-backup-for-a-backup-instance"></a>백업 인스턴스에 대해 백업 중지

백업 중인 기본 리소스가 더 이상 존재하지 않는 경우와 같이 백업 인스턴스에 대한 백업을 중지하려는 시나리오가 있습니다.

보호를 중지하려면 백업 센터로 이동하여 **백업 인스턴스** 메뉴 항목을 선택합니다. 이 옵션을 선택하면 액세스 권한이 있는 모든 백업 인스턴스의 세부 정보를 볼 수 있습니다. 백업하려는 백업 인스턴스를 검색할 수 있습니다. 그리드의 항목을 마우스 오른쪽 단추로 클릭하면 가능한 작업 목록이 열립니다. 백업 인스턴스에 대한 백업을 중지하려면 **백업 중지** 옵션을 선택합니다.

![보호 중지](./media/backup-center-actions/backup-center-stop-protection.png)

- Azure Virtual Machines 백업을 중지하는 방법에 대해 [자세히 알아보세요.](backup-azure-manage-vms.md#stop-protecting-a-vm)
- 디스크 백업 중지에 대해 [자세히 알아보세요.](manage-azure-managed-disks.md#stop-protection-preview)
- Azure Database for PostgreSQL Server에 대한 백업을 중지하는 방법에 대해 [자세히 알아봅니다.](manage-azure-database-postgresql.md#stop-protection-preview)

## <a name="resume-backup"></a>백업 다시 시작

**보호 중지** 중에 데이터를 보존한 경우 워크로드에 대한 백업을 다시 시작할 수 있습니다.

- 디스크에 대한 보호를 다시 재개하는 방법에 대해 [자세히 알아보세요.](manage-azure-database-postgresql.md#resume-protection)
- Azure Database for PostgreSQL 서버에 대한 보호를 다시 재개하는 방법에 대해 [자세히 알아보세요.](manage-azure-managed-disks.md#resume-protection)

## <a name="next-steps"></a>다음 단계

* [백업 모니터링 및 운영](backup-center-monitor-operate.md)
* [백업 공간 제어](backup-center-govern-environment.md)
* [백업에 대한 인사이트 얻기](backup-center-obtain-insights.md)
