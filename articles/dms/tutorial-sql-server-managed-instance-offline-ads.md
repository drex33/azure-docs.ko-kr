---
title: '자습서: Azure Data Studio를 사용하여 오프라인에서 Azure SQL Managed Instance로 SQL Server 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service와 함께 Azure Data Studio를 사용하여 오프라인에서 Azure SQL Managed Instance로 SQL Server 마이그레이션(미리 보기)
services: dms
author: mokabiru
ms.author: mokabiru
manager: ''
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 10/05/2021
ms.openlocfilehash: a94587fc0ccaa2683ee00e2da01efad4cad77184
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546442"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-offline-using-azure-data-studio-with-dms-preview"></a>자습서: DMS와 함께 Azure Data Studio를 사용하여 오프라인에서 Azure SQL Managed Instance로 SQL Server 마이그레이션(미리 보기)

Azure Data Studio에서 Azure SQL 마이그레이션 확장을 사용하여 SQL Server 인스턴스에서 Azure SQL Managed Instance로 데이터베이스를 마이그레이션할 수 있습니다. 수동 작업이 필요할 수 있는 방법은 [SQL Server 인스턴스를 Azure SQL Managed Instance로 마이그레이션](../azure-sql/migration-guides/managed-instance/sql-server-to-managed-instance-guide.md) 문서를 참조하세요.

이 자습서에서는 Azure DMS(Database Migration Service)와 함께 Azure Data Studio를 사용하여 **AdventureWorks** 데이터베이스를 SQL Server의 온-프레미스 인스턴스에서 Azure SQL Managed Instance로 마이그레이션합니다. 이 자습서에서는 마이그레이션 프로세스 중에 허용되는 가동 중지 시간을 고려하는 오프라인 마이그레이션 모드를 집중적으로 설명합니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.
> [!div class="checklist"]
>
> * Azure Data Studio에서 Azure SQL로 마이그레이션 마법사를 시작합니다.
> * 원본 SQL Server 데이터베이스의 평가 실행
> * 원본 SQL Server, 백업 위치, 대상 Azure SQL Managed Instance의 세부 정보 지정
> * 새 Azure Database Migration Service를 만들고 자체 호스팅 통합 런타임을 설치하여 원본 서버와 백업에 액세스합니다.
> * 마이그레이션을 시작하고 완료될 때까지 진행률 모니터링

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

이 문서에서는 SQL Server에서 SQL Managed Instance로 오프라인 마이그레이션을 설명합니다. 온라인 마이그레이션에 관해서는 [Azure Data Studio를 사용하여 온라인에서 Azure SQL Managed Instance로 SQL Server 마이그레이션](tutorial-sql-server-managed-instance-online-ads.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* [Azure Data Studio 다운로드 및 설치](/sql/azure-data-studio/download-azure-data-studio)
* Azure Data Studio Marketplace에서 [Azure SQL 마이그레이션 확장 설치](/sql/azure-data-studio/extensions/azure-sql-migration-extension)
* 아래 나열된 기본 제공 역할 중 하나에 할당된 Azure 계정이 있습니다.
    - 대상 Azure SQL Managed Instance(및 SMB 네트워크 공유에서 데이터베이스 백업 파일을 업로드하기 위한 스토리지 계정)의 기여자
    - 대상 Azure SQL Managed Instance 또는 Azure Storage 계정을 포함하는 Azure 리소스 그룹에 대한 소유자 또는 기여자 역할
    - Azure 구독에 대한 소유자 또는 기여자 역할
* [Azure Portal에서 SQL Managed Instance 만들기](../azure-sql/managed-instance/instance-create-quickstart.md) 문서의 세부 정보에 따라 SQL Managed Instance를 만듭니다.
* 원본 SQL Server는 연결하는 데 사용되는 로그인이 *sysadmin* 서버 역할의 구성원이거나 `CONTROL SERVER` 권한이 있는지 확인합니다. 
* Azure Database Migration Service가 데이터베이스 마이그레이션에 사용할 수 있는 전체 데이터베이스 백업 파일과 후속 트랜잭션 로그 백업 파일이 포함된 SMB 네트워크 공유, Azure Storage 계정 파일 공유 또는 Azure Storage 계정 Blob 컨테이너를 제공합니다.
    > [!IMPORTANT]
    > - 데이터베이스 백업 파일이 SMB 네트워크 공유에 제공된 경우 DMS 서비스가 데이터베이스 백업 파일을 업로드하고 데이터베이스 마이그레이션에 사용할 수 있도록 [Azure Storage 계정을 만듭니다](../storage/common/storage-account-create.md).  Azure Database Migration Service 인스턴스와 동일한 지역에 Azure Storage 계정을 만들어야 합니다.
    > - Azure Database Migration Service는 백업을 새로 시작하는 것이 아니라 사용자가 재해 복구 계획의 일부로 보유한 기존 백업을 마이그레이션에 사용합니다.
    > - [`WITH CHECKSUM` 옵션을 사용하여 백업](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?preserve-view=true&view=sql-server-2017)해야 합니다. 
    > - 각 백업은 별도의 백업 파일 또는 여러 백업 파일에 쓸 수 있습니다. 그러나 단일 백업 미디어에 여러 백업(예: 전체 및 t-log)을 추가하는 것은 지원되지 않습니다. 
    > - 압축된 백업을 제공하여 대량 백업 마이그레이션과 관련된 잠재적인 문제 발생 가능성을 줄일 수 있습니다.
* 원본 SQL Server 인스턴스를 실행하는 서비스 계정에 데이터베이스 백업 파일이 포함된 SMB 네트워크 공유에 대한 읽기 및 쓰기 권한이 있는지 확인합니다.

* TDE(투명한 데이터 암호화)로 보호된 데이터베이스를 마이그레이션하는 경우 데이터베이스 복원 전에 원본 SQL Server 인스턴스의 인증서를 대상 Azure SQL Managed Instance로 마이그레이션해야 합니다. 자세한 내용은 [TDE 보호 데이터베이스의 인증서를 Azure SQL Managed Instance로 마이그레이션](../azure-sql/managed-instance/tde-certificate-migrate.md)을 참조하세요.
    > [!TIP]
    > 데이터베이스에 [Always Encrypted](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio)로 보호되는 중요한 데이터가 포함되어 있는 경우 DMS와 함께 Azure Data Studio를 사용하는 마이그레이션 프로세스는 Always Encrypted 키를 대상 Azure SQL Managed Instance로 자동 마이그레이션합니다.

* **데이터베이스 백업이 네트워크 파일 공유에 있는 경우** 데이터베이스 백업에 액세스하고 마이그레이션하기 위해 [자체 호스팅 통합 런타임](../data-factory/create-self-hosted-integration-runtime.md)을 설치할 머신을 제공합니다. 마이그레이션 마법사는 자체 호스팅 통합 런타임을 다운로드하고 설치하기 위한 다운로드 링크와 인증 키를 제공합니다. 마이그레이션을 준비하기 위해 자체 호스팅 통합 런타임을 설치할 머신에 다음 아웃바운드 방화벽 규칙과 도메인 이름이 사용하도록 설정되어 있는지 확인합니다.

    | 도메인 이름                                          | 아웃바운드 포트 | Description                |
    | ----------------------------------------------------- | -------------- | ---------------------------|
    | 퍼블릭 클라우드: `{datafactory}.{region}.datafactory.azure.net`<br> 또는 `*.frontend.clouddatahub.net` <br> Azure Government: `{datafactory}.{region}.datafactory.azure.us` <br> 중국: `{datafactory}.{region}.datafactory.azure.cn` | 443            | 자체 호스팅 통합 런타임에서 데이터 마이그레이션 서비스에 연결하는 데 필요합니다. <br>퍼블릭 클라우드에서 새로 만든 Data Factory의 경우 {datafactory}.{region}.datafactory.azure.net 형식의 자체 호스팅 통합 런타임 키에서 FQDN을 찾습니다. 이전 Data Factory의 경우 FQDN이 자체 호스팅 통합 키에 표시되지 않으면 *.frontend.clouddatahub.net을 대신 사용합니다. |
    | `download.microsoft.com`    | 443            | 업데이트를 다운로드하기 위해 자체 호스팅 통합 런타임에서 필요합니다. 자동 업데이트를 사용하지 않도록 설정한 경우 이 도메인 구성을 건너뛸 수 있습니다. |
    | `*.core.windows.net`          | 443            | 자체 호스팅 통합 런타임에서 네트워크 공유의 데이터베이스 백업을 업로드하기 위해 Azure Storage 계정에 연결하는 데 사용합니다. |

    > [!TIP]
    > 데이터베이스 백업 파일이 이미 Azure Storage 계정에 제공된 경우 마이그레이션 프로세스 중에 자체 호스팅 통합 런타임이 필요하지 않습니다.

* 자체 호스팅 통합 런타임을 사용하는 경우 런타임이 설치된 시스템이 백업 파일이 있는 원본 SQL Server 인스턴스 및 네트워크 파일 공유에 연결할 수 있는지 확인합니다. 네트워크 파일 공유에 대한 액세스를 허용하려면 아웃바운드 포트 445를 사용하도록 설정해야 합니다. [자체 호스팅 통합 런타임 사용을 위한 권장 사항](migration-using-azure-data-studio.md#recommendations-for-using-self-hosted-integration-runtime-for-database-migrations)도 참조하세요.
* Azure Database Migration Service를 처음 사용하는 경우 Microsoft.DataMigration 리소스 공급자가 구독에 등록되어 있는지 확인하세요. 다음 단계에 따라 [리소스 공급자를 등록](quickstart-create-data-migration-service-portal.md#register-the-resource-provider)할 수 있습니다.

## <a name="launch-the-migrate-to-azure-sql-wizard-in-azure-data-studio"></a>Azure Data Studio에서 Azure SQL로 마이그레이션 마법사 시작

1. Azure Data Studio를 열고 서버 아이콘을 선택하여 온-프레미스 SQL Server(또는 Azure Virtual Machine의 SQL Server)에 연결합니다.
1. 서버 연결에서 마우스 오른쪽 단추를 클릭하고 **관리** 를 선택합니다.
1. 서버의 홈페이지에서 **Azure SQL 마이그레이션** 확장을 선택합니다.
1. Azure SQL 마이그레이션 대시보드에서 **Azure SQL로 마이그레이션** 을 선택하여 마이그레이션 마법사를 시작합니다.
    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-offline-ads/launch-migrate-to-azure-sql-wizard.png" alt-text="Azure SQL로 마이그레이션 마법사 시작":::
1. 마이그레이션 마법사의 첫 번째 단계에서는 이미 Azure Data Studio에 로그인한 경우 해당 Azure 계정을 연결하거나 새 Azure 계정을 연결합니다.

## <a name="run-database-assessment-and-select-target"></a>데이터베이스 평가 실행 및 대상 선택

1. 평가를 실행할 데이터베이스를 선택하고 **다음** 을 선택합니다.
1. 대상으로 Azure SQL Managed Instance를 선택합니다.
    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-offline-ads/assessment-complete-target-selection.png" alt-text="평가 확인":::
1. **보기/선택** 단추를 선택하여 데이터베이스 평가 결과의 세부 정보를 확인하고, 마이그레이션할 데이터베이스를 선택하고, **확인** 을 선택합니다.
    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-offline-ads/assessment-issues-details.png" alt-text="데이터베이스 평가 세부 정보":::
1. 해당 드롭다운 목록에서 구독, 위치, 리소스 그룹을 선택하여 **대상 Azure SQL Managed Instance** 를 지정하고 **다음** 을 선택합니다.

## <a name="configure-migration-settings"></a>마이그레이션 설정 구성

1. 마이그레이션 모드로 **온라인 마이그레이션** 을 선택합니다.
    > [!NOTE]
    > 오프라인 마이그레이션 모드에서는 대상 Azure SQL Managed Instance에 데이터베이스 백업이 복원되는 동안에는 원본 SQL Server 데이터베이스를 읽기 및 쓰기 작업에 사용할 수 없습니다. 마이그레이션이 완료될 때까지 애플리케이션 가동 중지 시간을 고려해야 합니다.
1. 데이터베이스 백업의 위치를 선택합니다. 데이터베이스 백업은 온-프레미스 네트워크 공유 또는 Azure Storage Blob 컨테이너에 있을 수 있습니다.

    > [!NOTE]
    > 데이터베이스 백업이 온-프레미스 네트워크 공유에서 제공되는 경우 DMS에서는 마법사의 다음 단계에서 자체 호스팅 통합 런타임을 설정하도록 요구합니다. 자체 호스팅 통합 런타임은 원본 데이터베이스 백업에 액세스하고, 백업 세트의 유효성을 확인하고, Azure Storage 계정에 업로드하는 데 필요합니다.<br/> 데이터베이스 백업이 이미 Azure Storage Blob 컨테이너에 있는 경우 자체 호스팅 통합 런타임을 설정할 필요가 없습니다.

1. 백업 위치를 선택한 후 원본 SQL Server와 원본 백업 위치의 세부 정보를 제공합니다.

    |필드    |Description  |
    |---------|-------------|
    |**원격 자격 증명 - 사용자 이름**    |원본 SQL Server 인스턴스에 연결하고 백업 파일의 유효성을 검사하기 위한 자격 증명(Windows/SQL 인증)입니다.         |
    |**원본 자격 증명 - 암호**    |원본 SQL Server 인스턴스에 연결하고 백업 파일의 유효성을 검사하기 위한 자격 증명(Windows/SQL 인증)입니다.         |
    |**백업이 포함된 네트워크 공유 위치**     |전체 및 트랜잭션 로그 백업 파일이 포함된 네트워크 공유 위치입니다. 유효한 백업 세트에 속하지 않는 네트워크 공유의 잘못된 파일이나 백업 파일은 마이그레이션 프로세스 중에 자동으로 무시됩니다.        |
    |**네트워크 공유 위치에 대한 읽기 권한이 있는 Windows 사용자 계정**     |백업 파일을 검색할 수 있도록 네트워크 공유에 대한 읽기 권한이 있는 Windows 자격 증명(사용자 이름)입니다.       |
    |**암호**     |백업 파일을 검색할 수 있도록 네트워크 공유에 대한 읽기 권한이 있는 Windows 자격 증명(암호)입니다.         |
    |**대상 데이터베이스 이름** |마이그레이션 프로세스 중에 대상의 데이터베이스 이름을 변경하려는 경우 대상 데이터베이스 이름을 수정할 수 있습니다.            |

1. 해당 드롭다운 목록에서 **구독**, **위치**, **리소스 그룹** 을 선택하여 **Azure Storage 계정** 을 지정합니다. 이 Azure Storage 계정은 DMS에서 네트워크 공유의 데이터베이스 백업을 업로드하는 데 사용됩니다. DMS는 업로드 프로세스 중에 지정된 스토리지 계정에 Blob 컨테이너를 자동으로 만들기 때문에 컨테이너를 만들 필요가 없습니다.
> [!IMPORTANT]
> 루프백 확인 기능을 사용하도록 설정되어 있고 원본 SQL Server와 파일 공유가 동일한 컴퓨터에 있으면 원본은 FQDN을 사용하여 파일 공유에 액세스할 수 없습니다. 이 문제를 해결하려면 [여기](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd)의 지침을 사용하여 루프백 확인 기능을 사용하지 않도록 설정합니다.

## <a name="create-azure-database-migration-service"></a>Azure Database Migration Service 생성

1. 새 Azure Database Migration Service를 만들거나 이전에 만든 기존 서비스를 다시 사용합니다.
    > [!NOTE]
    > 이전에 Azure Portal을 사용하여 DMS를 만든 경우 Azure Data Studio의 마이그레이션 마법사에서 다시 사용할 수 없습니다. 이전에 Azure Data Studio를 사용하여 만든 DMS만 다시 사용할 수 있습니다.
1. 기존 DMS가 있거나 새 DMS를 만들어야 하는 경우 **리소스 그룹** 을 선택합니다. **Azure Database Migration Service** 드롭다운에는 선택한 리소스 그룹의 기존 DMS가 나열됩니다.
1. 기존 DMS를 다시 사용하려면 드롭다운 목록에서 선택하며 자체 호스팅 통합 런타임의 상태가 페이지 아래쪽에 표시됩니다.
1. 새 DMS를 만들려면 **새로 만들기** 를 선택합니다. **Azure Database Migration Service 만들기** 화면에서 DMS의 이름을 입력하고 **만들기** 를 선택합니다.
1. DMS를 성공적으로 만든 후에는 **통합 런타임** 설정을 위한 세부 정보가 제공됩니다.
1. **통합 런타임 다운로드 및 설치** 를 선택하여 웹 브라우저에서 다운로드 링크를 엽니다. 다운로드를 완료합니다. 원본 SQL Server에 연결하기 위한 필수 조건을 충족하는 머신과 원본 백업을 포함하는 위치에 통합 런타임을 설치합니다.
1. 설치가 완료된 후 **Microsoft Integration Runtime 구성 관리자** 가 자동으로 시작되어 등록 프로세스를 시작합니다.
1. Azure Data Studio의 마법사 화면에서 제공되는 인증 키 중 하나를 복사하여 붙여넣습니다. 인증 키가 유효하면 **등록** 을 계속할 수 있음을 나타내는 녹색 확인 아이콘이 Integration Runtime 구성 관리자에 표시됩니다.
1. 자체 호스팅 통합 런타임 등록을 성공적으로 완료한 후 **Microsoft Integration Runtime 구성 관리자** 를 닫고 Azure Data Studio의 마이그레이션 마법사로 다시 전환합니다.
1. Azure Data Studio의 **Azure Database Migration Service 만들기** 화면에서 **연결 테스트** 를 선택하여 새로 만든 DMS가 새로 등록된 자체 호스팅 통합 런타임에 연결되어 있는지 유효성을 검사합니다.
    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-offline-ads/test-connection-integration-runtime-complete.png" alt-text="통합 런타임 연결 테스트":::
1. 요약을 검토하고 **완료** 를 선택하여 데이터베이스 마이그레이션을 시작합니다.

## <a name="monitor-your-migration"></a>마이그레이션 모니터링

1. **데이터베이스 마이그레이션 상태** 에서 진행 중인 마이그레이션, 완료된 마이그레이션, 실패한 마이그레이션(있는 경우)을 추적할 수 있습니다.

    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-offline-ads/monitor-migration-dashboard.png" alt-text="마이그레이션 대시보드 모니터링":::
1. **진행 중인 데이터베이스 마이그레이션** 을 선택하여 진행 중인 마이그레이션을 확인하고 데이터베이스 이름을 선택하여 자세한 내용을 확인합니다.
1. 마이그레이션 세부 정보 페이지에 백업 파일과 해당 상태가 표시됩니다.

    | 상태 | Description |
    |--------|-------------|
    | 도착 | 백업 파일이 원본 백업 위치에 도착하고 유효성이 검사됨 |
    | 업로드 | 통합 런타임이 현재 Azure Storage에 백업 파일을 업로드하고 있음|
    | 업로드됨 | 백업 파일이 Azure Storage에 업로드됨 |
    | 복원 중 | Azure Database Migration Service가 현재 Azure SQL Managed Instance에 백업 파일을 복원하고 있음|
    | 복원 | 백업 파일이 Azure SQL Managed Instance에서 성공적으로 복원됨 |
    | 취소됨 | 마이그레이션 프로세스가 취소됨 |
    | 무시됨 | 백업 파일이 유효한 데이터베이스 백업 체인에 속하지 않으므로 무시됨 |

    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-offline-ads/offline-to-mi-migration-details-inprogress-backups-restored.png" alt-text="오프라인 백업 복원 세부 정보":::

모든 데이터베이스 백업이 Azure SQL Managed Instance에 복원된 후에는 Azure SQL Managed Instance의 마이그레이션된 데이터베이스를 사용할 수 있도록 준비하기 위해 Azure DMS가 자동 마이그레이션 중단을 시작하고, 마이그레이션 상태가 ‘진행 중’에서 ‘성공’으로 변경됩니다. 

> [!IMPORTANT]
> 마이그레이션 이후에는 중요 비즈니스용 서비스 계층이 있는 SQL Managed Instance의 가용성이 범용보다 훨씬 더 오래 걸릴 수 있습니다. AlwaysOn 고가용성 그룹에 대해 세 개의 보조 복제본을 시드해야 하기 때문입니다. 이 작업 기간은 데이터 크기에 따라 다릅니다. 자세한 내용은 [관리 작업 기간](../azure-sql/managed-instance/management-operations-overview.md#duration)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* T-SQL RESTORE 명령을 사용하여 SQL Managed Instance로 데이터베이스를 마이그레이션하는 방법을 보여주는 자습서는 [복원 명령을 사용하여 백업을 SQL Managed Instance에 복원](../azure-sql/managed-instance/restore-sample-database-quickstart.md)을 참조하세요.
* SQL Managed Instance에 대한 자세한 내용은 [SQL Managed Instance란?](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)을 참조하세요.
* SQL Managed Instance에 앱을 연결하는 방법에 대한 자세한 내용은 [애플리케이션 연결](../azure-sql/managed-instance/connect-application-instance.md)을 참조하세요.
