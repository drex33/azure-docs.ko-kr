---
title: Azure Database for PostgreSQL 백업
description: 장기 보존(미리 보기)을 사용한 Azure Database for PostgreSQL 백업에 대해 알아보기
ms.topic: conceptual
ms.date: 09/06/2021
ms.custom: references_regions , devx-track-azurecli
ms.openlocfilehash: 1896f836d9eeb2f4d32e4b0784424837a2f80d0c
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081836"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>장기 보존(미리 보기)을 사용한 Azure Database for PostgreSQL 백업

Azure Backup 및 Azure Database Services는 최대 10년 동안 백업을 유지하는 Azure Database for PostgreSQL 서버에 대한 엔터프라이즈급 백업 솔루션을 빌드하기 위해 함께 제공됩니다.

장기 보존 외에도 솔루션에는 아래에 나열된 다른 많은 기능도 있습니다.

- Azure Active Directory 및 MSI(관리되는 서비스 ID) 인증을 사용하여 데이터베이스에 대한 Azure RBAC(Azure 역할 기반 액세스 제어).
- 고객이 개별 데이터베이스 수준에서 예약된 및 주문형 백업을 제어합니다.
- 데이터베이스 수준은 Postgres 서버 또는 Blob Storage로 직접 복원됩니다.
- 장기 보존.
- 모든 작업 및 작업에 대한 중앙 모니터링.
- 백업은 별도의 보안 및 장애 도메인에 저장됩니다. 따라서 원본 서버가 손상되거나 심지어 종료되더라도 백업은 [Backup 자격 증명 모음](backup-vault-overview.md)에서 안전하게 유지됩니다.
- **pg_dump** 사용하면 데이터베이스 버전 간에 복원하거나 백업의 일부만 복원할 수 있도록 복원의 유연성이 향상됩니다.

이 솔루션은 독립적으로 또는 최대 35일의 보존 기간을 제공하는 Azure PostgreSQL에서 제공하는 네이티브 백업 솔루션 외에도 사용할 수 있습니다. 네이티브 솔루션은 최신 백업에서 복구하려는 경우와 같은 작업 복구에 적합합니다. Azure Backup 솔루션은 규정 준수 요구 사항과 보다 세분화되고 유연한 백업 및 복원에 도움이 됩니다.

## <a name="support-matrix"></a>지원 매트릭스

|지원  |세부 정보  |
|---------|---------|
|지원되는 배포   |  [Azure Database for PostgreSQL - 단일 서버](../postgresql/overview.md#azure-database-for-postgresql---single-server)     |
|지원되는 Azure 지역 |  미국 동부, 미국 동부 2, 미국 중부, 미국 중남부, 미국 서부, 미국 서부 2, 미국 중서부, 브라질 남부, 캐나다 중부, 북유럽, 서유럽, 영국 남부, 영국 서부, 독일 중서부, 스위스 북부, 스위스 서부, 동아시아, 동남 아시아, 일본 동부, 일본 서부, 한국 중부, 한국 남부, 인도 중부, 오스트레일리아 동부, 오스트레일리아 중부, 오스트레일리아 중부 2, 아랍에미리트 북부  |
|지원되는 Azure PostgreSQL 버전    |   9.5, 9.6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>기능 고려 사항 및 제한 사항

- 모든 작업은 Azure Portal만 지원됩니다.
- 최대 데이터베이스 크기에 권장되는 제한은 400GB입니다.
- 지역 간 백업을 지원하지 않습니다. 즉, Azure PostgreSQL 서버를 다른 지역의 자격 증명 모음에 백업할 수 없습니다. 마찬가지로 자격 증명 모음과 동일한 지역 내에 있는 서버로만 백업을 복원할 수 있습니다.
- 복원 시 데이터만 복구됩니다. "역할"이 복원되지 않습니다.
- 미리 보기에서는 테스트 환경에서만 솔루션을 실행하는 것이 좋습니다.

## <a name="prerequisite-permissions-for-configure-backup-and-restore"></a>백업 및 복원을 구성하기 위한 필수 조건 권한

Azure Backup은 엄격한 보안 지침을 따릅니다. 네이티브 Azure 서비스인 경우에도 리소스에 대한 사용 권한은 가정하지 않으며 사용자가 명시적으로 지정해야 합니다.  마찬가지로 데이터베이스에 연결하기 위한 자격 증명은 저장되지 않습니다. 이는 데이터를 보호하는 데 중요합니다. 대신 Azure Active Directory 인증을 사용합니다.

[이 문서를 다운로드](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx)하여 자동화된 스크립트 및 관련 지침을 확인하세요. 백업 및 복원을 위해 Azure PostgreSQL 서버에 적절한 사용 권한 집합을 부여합니다.

## <a name="backup-process"></a>백업 프로세스

1. 이 솔루션은 **pg_dump** 를 사용하여 Azure PostgreSQL 데이터베이스의 백업을 수행합니다.

2. 백업하려는 Azure PostgreSQL 데이터베이스를 지정하면 서비스는 서버 및 데이터베이스에서 백업 작업을 수행할 수 있는 올바른 권한 집합 및 액세스 권한이 있는지 확인합니다.

3. Azure Backup는 백업 확장이 설치된 작업자 역할을 회전합니다. 이 확장은 Postgres 서버와 통신합니다.

4. 이 확장은 코디네이터 및 SQL 플러그 인으로 구성됩니다. 코디네이터는 백업 구성, 백업 및 복원 같은 다양한 워크플로를 트리거하는 역할을 담당하고, 플러그 인은 실제 데이터 흐름을 담당합니다.
  
5. 선택한 데이터베이스에서 구성 보호를 트리거하면 백업 서비스가 백업 일정과 기타 정책 세부 정보를 사용하여 코디네이터를 설정합니다.

6. 예약된 시간에 코디네이터가 플러그 인과 통신하고 **pg_dump** 를 사용하여 Postgres 서버의 백업 데이터를 스트리밍하기 시작합니다.

7. 플러그 인은 Backup 자격 증명 모음에 직접 데이터를 보내기 때문에 준비 위치가 필요 없습니다. 데이터는 Microsoft 관리 키를 사용하여 암호화되며 Azure Backup 서비스를 통해 스토리지 계정에 저장됩니다.

8. 데이터 전송이 완료되면 코디네이터가 백업 서비스를 사용하여 커밋을 확인합니다.

    ![백업 프로세스](./media/backup-azure-database-postgresql-overview/backup-process.png)

## <a name="configure-backup-on-azure-postgresql-databases"></a>Azure PostgreSQL 데이터베이스에서 백업 구성

여러 Azure PostgreSQL 서버에서 여러 데이터베이스에 백업을 구성할 수 있습니다. 서비스에서 Postgres 서버를 백업하는 데 필요한 [필수 구성 요소 권한](#prerequisite-permissions-for-configure-backup-and-restore)이 이미 구성되어 있는지 확인합니다.

다음 지침은 Azure Backup를 사용하여 Azure PostgreSQL 데이터베이스에서 백업을 구성하는 단계별 가이드입니다.

1. 프로세스를 시작하는 방법에는 다음 두 가지가 있습니다.

    1. [Backup 센터](backup-center-overview.md) -> **개요** -> **백업** 으로 이동합니다.

        ![Backup 센터로 이동](./media/backup-azure-database-postgresql/backup-center.png)

        **시작: 백업 구성** 에서 **Datasource 유형** 을 **Azure Database for PostgreSQL** 를 선택합니다.

        ![시작: 백업 구성에서 데이터 원본 유형을 선택합니다](./media/backup-azure-database-postgresql/initiate-configure-backup.png)

    1. 또는 [백업 자격 증명 모음](backup-vault-overview.md) -> **백업** 으로 바로 이동할 수 있습니다.

        ![백업 자격 증명 모음으로 이동](./media/backup-azure-database-postgresql/backup-vaults.png)

        ![Backup 자격 증명 모음에서 백업 선택](./media/backup-azure-database-postgresql/backup-backup-vault.png)

1. **백업 구성** 에서, Postgres 데이터베이스를 백업할 **Backup 자격 증명 모음** 을 선택합니다. 이 정보는 이미 자격 증명 모음 컨텍스트에 있는 경우 미리 채워져 있습니다.

    ![백업 구성에서 백업 자격 증명 모음 선택](./media/backup-azure-database-postgresql/configure-backup.png)

1. **백업 정책** 선택 및 만들기.

    ![백업 정책 선택](./media/backup-azure-database-postgresql/backup-policy.png)

1. 백업할 리소스 또는 Postgres 데이터베이스를 선택합니다.

    ![백업할 리소스 선택](./media/backup-azure-database-postgresql/select-resources.png)

1. 자격 증명 모음과 동일한 지역에 있는 경우 구독의 모든 Azure PostgreSQL 서버 목록에서 선택할 수 있습니다. 화살표를 확장하여 서버 내의 데이터베이스 목록을 확인합니다.

    ![서버 선택](./media/backup-azure-database-postgresql/choose-servers.png)

1. 서비스는 선택한 데이터베이스에서 이러한 검사를 실행하여 자격 증명 모음에 선택한 Postgres 서버 및 데이터베이스를 백업할 수 있는 권한이 있는지 확인합니다.
    1. 계속하려면 모든 데이터베이스에 대한 **백업 준비 상태를** **성공으로** 읽어야 합니다.
    1. 오류가 있는 경우 오류를 **수정하고** 유효성을 **재검사하거나** 선택 항목에서 데이터베이스를 제거합니다.

    ![수정할 유효성 검사 오류](./media/backup-azure-database-postgresql/validation-errors.png)

1. **검토 및 구성** 에서 모든 세부 정보를 확인하고 백업 **구성** 을 선택하여 작업을 제출합니다.

    ![검토 및 구성에서 세부 정보 확인](./media/backup-azure-database-postgresql/review-and-configure.png)

1. 트리거되면 **백업 구성** 작업이 백업 인스턴스를 만듭니다. 백업 센터 또는 자격 증명 모음 보기의 [백업 인스턴스](backup-center-monitor-operate.md#backup-instances) 창에서 작업의 상태를 추적할 수 있습니다.

    ![Backup 인스턴스](./media/backup-azure-database-postgresql/backup-instances.png)

1. 백업은 정책에 정의된 백업 일정에 따라 트리거됩니다. [Backup 작업](backup-center-monitor-operate.md#backup-jobs)에서 작업을 추적할 수 있습니다. 현재 지난 7일 동안의 작업을 볼 수 있습니다.

    ![백업 작업](./media/backup-azure-database-postgresql/backup-jobs.png)

## <a name="create-backup-policy"></a>백업 정책 만들기

1. **백업 센터** -> **백업 정책** -> **추가** 로 이동합니다. 또는 **Backup 자격 증명 모음** -> **백업 정책** -> **추가** 로 이동할 수 있습니다.

    ![백업 정책 추가](./media/backup-azure-database-postgresql/add-backup-policy.png)

1. 새 정책에 대한 **이름** 을 입력합니다.

    ![정책 이름 입력](./media/backup-azure-database-postgresql/enter-policy-name.png)

1. 백업 일정을 정의합니다. 현재 **주간** 백업을 지원합니다. 하나 이상의 요일에 백업을 예약할 수 있습니다.

    ![백업 일정 정의](./media/backup-azure-database-postgresql/define-backup-schedule.png)

1. **보존** 설정을 정의합니다. 하나 이상의 보존 규칙을 추가할 수 있습니다. 각 보존 규칙은 특정 백업에 대한 입력과 해당 백업에 대한 데이터 저장소 및 보존 기간을 가정합니다.

1. 두 개의 데이터 백업 저장소(또는 계층) 즉 **백업 데이터 저장소**(표준 계층) 또는 **보관 데이터 저장소**(미리 보기) 중 하나에 백업을 저장하도록 선택할 수 있습니다.

   **만료 시** 를 선택하여 백업 데이터 저장소의 만료 시 보관 데이터 저장소로 백업을 이동할 수 있습니다.

1. **기본 보존 규칙** 은 다른 보존 규칙이 없을 때 적용되며 기본값은 3개월입니다.

    - 보존 기간 범위는 **백업 데이터 저장소** 에서 7일에서 10년 사이입니다.
    - 보존 기간 범위는 **보관 데이터 저장소** 에서 7개월에서 10년 사이입니다.

    ![보존 기간 편집](./media/backup-azure-database-postgresql/edit-retention.png)

>[!NOTE]
>보존 규칙은 미리 결정된 우선 순위 순서로 평가됩니다. 우선 순위는 **연도별** 규칙의 가장 높으며, 그 다음에는 **월별** 및 **주간** 규칙이 적용됩니다. 다른 규칙이 한정되지 않은 경우 기본 보존 설정이 적용됩니다. 예를 들어 동일한 복구 지점은 매주 수행된 첫 번째 성공한 백업뿐만 아니라 매월 수행된 첫 번째 성공한 백업일 수 있습니다. 그러나 월별 규칙 우선 순위가 주간 규칙의 우선 순위 보다 높기 때문에 매월 수행된 첫 번째 성공한 백업에 해당하는 보존은 적용됩니다.

## <a name="restore"></a>복원

서비스에 대상 서버에 대한 적절한 사용 권한 집합이 있는 경우 동일한 구독 내의 Azure PostgreSQL 서버로 데이터베이스를 복원할 수 있습니다. 서비스에서 Postgres 서버를 백업하는 데 필요한 [필수 구성 요소 권한](#prerequisite-permissions-for-configure-backup-and-restore)이 이미 구성되어 있는지 확인합니다.

이 단계별 가이드에 따라 복원을 트리거합니다.

1. 복원 프로세스를 시작하는 방법에는 두 가지가 있습니다.

   1. [Backup 센터](backup-center-overview.md) -> **개요** -> **복원** 으로 이동합니다.

      ![백업 센터에서 복원을 선택](./media/backup-azure-database-postgresql/backup-center-restore.png)

      **시작: 복원** 에서 **데이터 원본 유형** 을 **Azure Database for PostgreSQL** 으로 선택합니다. **백업 인스턴스** 를 선택합니다.

      ![시작: 복원에서 데이터 원본 유형 선택](./media/backup-azure-database-postgresql/initiate-restore.png)

   1. 또는 **Backup 자격 증명 모음** -> **Backup 인스턴스** 로 바로 이동할 수 있습니다. 복원하려는 데이터베이스에 해당하는 **Backup 인스턴스를** 선택합니다.

      ![복원을 위한 Backup 인스턴스](./media/backup-azure-database-postgresql/backup-instances-restore.png)

      ![Backup 인스턴스 목록](./media/backup-azure-database-postgresql/list-backup-instances.png)

      ![복원 선택](./media/backup-azure-database-postgresql/select-restore.png)

1. 선택한 백업 인스턴스에 사용할 수 있는 모든 전체 백업 목록에서 **복구 지점을 선택합니다**. 기본적으로 최신 복구 지점을 선택합니다.

    ![복구 지점 선택](./media/backup-azure-database-postgresql/select-recovery-point.png)

    ![복구 지점 목록](./media/backup-azure-database-postgresql/list-recovery-points.png)

1. **복원 매개 변수** 입력. 이 시점에서 **데이터베이스로 복원** 및 **파일로 복원** 의 두 가지 복원 중에서 선택할 수 있습니다.

1. **데이터베이스로 복원**: 백업 데이터를 복원하여 대상 PostgreSQL 서버에 새 데이터베이스를 만듭니다.

    - 대상 서버는 원본 서버와 같을 수 있습니다. 그러나 원래 데이터베이스를 덮어쓰는 것은 지원되지 않습니다.
    - 모든 구독의 서버에서 선택할 수 있지만 자격 증명 모음과 동일한 지역에서 선택할 수 있습니다.
    - **검토 + 복원** 을 선택합니다. 이렇게 하면 서비스에 대상 서버에 대한 적절한 복원 권한이 있는지 확인하기 위한 유효성 검사가 트리거됩니다.

    ![데이터베이스로 복원](./media/backup-azure-database-postgresql/restore-as-database.png)

1. **파일로 복원**: 백업 파일을 대상 스토리지 계정(BLOB)에 덤프합니다.

    - 모든 구독의 스토리지 계정에서 선택할 수 있지만 자격 증명 모음과 동일한 지역에서 선택할 수 있습니다.
    - 선택한 스토리지 계정에 대해 필터링된 컨테이너 목록에서 대상 컨테이너를 선택합니다.
    - **검토 + 복원** 을 선택합니다. 이렇게 하면 서비스에 대상 서버에 대한 적절한 복원 권한이 있는지 확인하기 위한 유효성 검사가 트리거됩니다.

    ![파일로 복원](./media/backup-azure-database-postgresql/restore-as-files.png)

1. 복구 지점이 보관 계층에 있는 경우 복원하기 전에 복구 지점을 리하이드레이션해야 합니다.
   
   ![리하이드레이션 설정](./media/backup-azure-database-postgresql/rehydration-settings.png)
   
   리하이드레이션에 필요한 다음과 같은 추가 매개 변수를 제공합니다.
   - **리하이드레이션 우선 순위:** 기본값은 **표준** 입니다.
   - **리하이드레이션 기간:** 최대 리하이드레이션 기간은 30일이고 최소 리하이드레이션 기간은 10일입니다. 기본값은 **15** 입니다.
   
   복구 지점은 지정된 리하이드레이션 기간 동안 **백업 데이터 저장소** 에 저장됩니다.


1. 정보를 검토하고 **복원** 을 선택합니다. 이렇게 하면 **백업 작업** 에서 추적할 수 있는 해당 복원 작업이 트리거됩니다.

>[!NOTE]
>Azure Database for PostgreSQL에 대한 보관 지원은 제한된 공개 미리 보기로 제공됩니다.



## <a name="manage-the-backed-up-azure-postgresql-databases"></a>백업된 Azure PostgreSQL 데이터베이스 관리

**백업 인스턴스** 에서 수행할 수 있는 관리 작업은 다음과 같습니다.

### <a name="on-demand-backup"></a>주문형 백업

정책에 지정된 일정에 없는 백업을 트리거하려면 **백업 인스턴스** -> **지금 백업** 으로 이동합니다.
연결된 백업 정책에 정의된 보존 규칙 목록에서 선택합니다.

![지금 백업 트리거](./media/backup-azure-database-postgresql/backup-now.png)

![보존 규칙 목록에서 선택](./media/backup-azure-database-postgresql/retention-rules.png)

### <a name="change-policy"></a>정책 변경

백업 인스턴스를 통해 연결된 정책을 변경할 수 있습니다.

1. 백업 **인스턴스** -> **변경 정책** 을 선택합니다.

    ![정책 변경](./media/backup-azure-database-postgresql/change-policy.png)

1. 데이터베이스에 적용할 새 정책을 선택합니다.

    ![정책 재할당](./media/backup-azure-database-postgresql/reassign-policy.png)

## <a name="troubleshooting"></a>문제 해결

이 섹션에서는 Azure Backup를 사용하여 Azure PostgreSQL 데이터베이스를 백업하기 위한 문제 해결 정보를 제공합니다.

### <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

백업하거나 복원할 PG 서버에서 백업 자격 증명 모음 MSI에 **읽기** 액세스 권한을 부여합니다.

PostgreSQL 데이터베이스에 대한 보안 연결을 설정하기 위해 Azure Backup은 [MSI(관리 서비스 ID)](../active-directory/managed-identities-azure-resources/overview.md) 인증 모델을 사용합니다. 즉, 백업 자격 증명 모음은 사용자가 명시적으로 권한을 부여한 리소스에만 액세스할 수 있습니다.

시스템 MSI는 생성 시 자격 증명 모음에 자동으로 할당됩니다. 데이터베이스를 백업하려는 PostgreSQL 서버에 대한 액세스 권한을 이 자격 증명 모음 MSI에 부여해야 합니다.

단계:

1. Postgres server에서 **Access Control(IAM)** 창으로 이동합니다.

    ![액세스 제어 창](./media/backup-azure-database-postgresql/access-control-pane.png)

1. **역할 할당 추가** 를 선택합니다.

    ![역할 할당 추가](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. 열리는 오른쪽 컨텍스트 창에서 다음을 입력합니다.<br>

   - **역할:** 드롭다운 목록에서 **읽기 권한자** 역할을 선택합니다.<br>
   - **액세스 할당:** 드롭다운 목록에서 **사용자, 그룹 또는 서비스 주체** 옵션을 선택합니다.<br>
   - **선택:** 이 서버 및 해당 데이터베이스를 백업하려는 백업 자격 증명 모음 이름을 입력합니다.<br>

    ![직무 선택](./media/backup-azure-database-postgresql/select-role-and-enter-backup-vault-name.png)

### <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Azure Active Directory를 사용하여 인증할 수 있는 데이터베이스 백업 사용자를 만듭니다.

PostgreSQL 서버에 대한 Azure Active Directory 관리자가 없거나 Azure Active Directory를 사용하여 인증할 수 있는 백업 사용자가 없을 때 이 오류가 발생할 수 있습니다.

단계:

OSS 서버에 Active Directory 관리자를 추가합니다.

이 단계는 암호 대신 Azure Active Directory를 사용하여 인증할 수 있는 사용자를 통해 데이터베이스에 연결하는 데 필요합니다. Azure Database for PostgreSQL의 Azure AD 관리 사용자에게 **azure_ad_admin** 역할이 있습니다. **Azure_ad_admin** 역할만 Azure AD를 사용하여 인증할 수 있는 새 데이터베이스 사용자를 만들 수 있습니다.

1. 서버 보기의 왼쪽 탐색 창에 있는 Active Directory 관리자 탭으로 이동하고 자신(또는 다른 사용자)을 Active Directory 관리자로 추가합니다.

    ![Active Directory 관리자 설정](./media/backup-azure-database-postgresql/set-admin.png)

1. AD 관리 사용자 설정을 **저장** 해야 합니다.

    ![Active Directory 관리 사용자 설정 저장](./media/backup-azure-database-postgresql/save-admin-setting.png)

권한 부여 단계를 완료하기 위해 수행해야 하는 단계 목록은 [이 문서](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx)를 참조하세요.

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

서버 보기에서 **Azure 서비스에 대한 액세스 허용** 플래그를 사용하여 네트워크 시야를 설정합니다. 서버 보기의 **연결 보안** 창에서 **Azure 서비스에 대한 액세스 허용** 플래그를 **예** 로 설정합니다.

>[!Note]
>이 플래그를 사용하도록 설정하기 전에 **공용 네트워크 액세스 거부** 플래그를 **아니요로** 설정해야 합니다.

![Azure 서비스에 대한 액세스 허용](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

### <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

#### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>파일로 복원할 때 스토리지 계정 컨테이너에 복원할 수 있는 권한

1. 백업 자격 증명 모음 MSI에 Azure Portal 사용하여 스토리지 계정 컨테이너에 액세스할 수 있는 권한을 부여합니다.
    1. **스토리지 계정** -> **액세스 컨트롤** -> **역할 할당 추가** 로 이동합니다.
    1. **스토리지 Blob 데이터 기여자** 역할을 Backup 자격 증명 모음 MSI에 할당합니다.

    !["스토리지 Blob 데이터 기여자" 역할 할당](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. 또는 Azure CLI [az role assignment create](/cli/azure/role/assignment) 명령을 사용하여 복원하려는 특정 컨테이너에 대해 세부적인 권한을 부여합니다.

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. 담당자 매개 변수를 자격 증명 모음 MSI의 **애플리케이션 ID** 및 범위 매개 변수로 바꿔 특정 컨테이너를 참조합니다.
    1. 자격 증명 모음 MSI의 **애플리케이션 ID를** 얻으려면 **애플리케이션 유형** 아래에서 **모든 애플리케이션** 을 선택합니다.

        ![모든 애플리케이션을 선택합니다](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. 자격 증명 모음 이름을 검색하고 애플리케이션 ID를 복사합니다.

        ![자격 증명 모음 이름 검색](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>다음 단계

[Backup 자격 증명 모음 개요](backup-vault-overview.md)
