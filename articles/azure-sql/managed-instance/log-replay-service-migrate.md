---
title: 로그 재생 서비스를 사용하여 SQL Managed Instance로 데이터베이스 마이그레이션
description: 로그 재생 서비스를 사용하여 SQL Server에서 SQL Managed Instance로 데이터베이스를 마이그레이션하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: migration
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-azurecli, devx-track-azurepowershell
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: mathoma
ms.date: 09/07/2021
ms.openlocfilehash: 85bf8c07da9d283011d17f1f96ad76e0fa411213
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123535314"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-by-using-log-replay-service-preview"></a>로그 재생 서비스(미리 보기)를 사용하여 SQL Server에서 SQL Managed Instance로 데이터베이스 마이그레이션
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 현재 공개 미리 보기로 제공되는 LRS(로그 재생 서비스)를 사용하여 SQL Server 2008-2019에서 Azure SQL Managed Instance로의 데이터베이스 마이그레이션을 수동으로 구성하는 방법을 설명합니다. LRS는 SQL Managed Instance에서 사용할 수 있는 클라우드 서비스로, SQL Server 로그 전달 기술을 기반으로 합니다. 

[Azure Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md) 및 LRS는 동일한 기본 마이그레이션 기술과 동일한 API를 사용합니다. LRS 릴리스를 통해 온-프레미스 SQL Server와 SQL Managed Instance 간에 복잡한 사용자 지정 마이그레이션과 하이브리드 아키텍처를 추가로 사용할 수 있습니다.

## <a name="when-to-use-log-replay-service"></a>로그 재생 서비스를 사용하는 경우

마이그레이션에 Azure Database Migration Service를 사용할 수 없는 경우 PowerShell, Azure CLI cmdlet 또는 API에서 직접 LRS를 사용하여 데이터베이스 마이그레이션을 수동으로 빌드하고 SQL Managed Instance에 오케스트레이션할 수 있습니다. 

다음과 같은 경우에 LRS 사용을 고려할 수 있습니다.
- 데이터베이스 마이그레이션 프로젝트에 대한 추가 제어가 필요합니다.
- 마이그레이션 중단에 대한 가동 중지 시간이 거의 허용되지 않습니다.
- Database Migration Service 실행 파일을 사용자 환경에 설치할 수 없습니다.
- Database Migration Service 실행 파일에 데이터베이스 백업에 대한 파일 액세스 권한이 없습니다.
- 사용할 수 있는 호스트 OS에 액세스할 수 없거나 관리자 권한이 없습니다.
- 사용자 환경에서 Azure로 네트워크 포트를 열 수 없습니다.
- 네트워크 대역폭 제한, 또는 환경 내 프록시 차단 문제가 있습니다.
- 백업은 `TO URL` 옵션을 통해 Azure Blob Storage에 직접 저장됩니다.
- 차등 백업을 사용해야 합니다.

> [!NOTE]
> Database Migration Service를 사용하여 SQL Server에서 SQL Managed Instance로 데이터베이스 마이그레이션을 자동화하는 것이 좋습니다. 이 서비스는 `NORECOVERY` 모드의 로그 전달과 동일하게 백엔드에서 동일한 LRS 클라우드 서비스를 사용합니다. Database Migration Service에서 시나리오를 완전히 지원하지 않는 경우 LRS를 수동으로 사용하여 마이그레이션을 오케스트레이션하는 것이 좋습니다.

## <a name="how-it-works"></a>작동 방식

LRS를 사용하여 클라우드로 데이터베이스를 마이그레이션하는 사용자 지정 솔루션을 빌드하려면 이 섹션의 뒷부분에 나오는 다이어그램과 표에 표시된 대로 몇 가지 오케스트레이션 단계가 필요합니다.

마이그레이션은 `CHECKSUM`이 사용 설정된 상태에서 SQL Server의 전체 데이터베이스 백업과 Azure Blob Storage로의 백업 파일 복사로 이루어집니다. LRS는 Blob Storage에서 SQL Managed Instance로 백업 파일을 복원하는 데 사용됩니다. Blob Storage은 SQL Server와 SQL Managed Instance 사이의 중간 스토리지입니다.

LRS는 전체 백업이 복원된 후 추가된 새로운 차등 또는 로그 백업에 대해 Blob Storage를 모니터링합니다. 그런 다음 LRS는 이러한 새 파일을 자동으로 복원합니다. 서비스를 사용하여 SQL Managed Instance에서 복원되는 백업 파일의 진행률을 모니터링하고, 필요한 경우 프로세스를 중지할 수 있습니다.

LRS에는 백업 파일에 대한 특정 명명 규칙이 필요하지 않습니다. Blob Storage에 배치된 모든 파일을 스캔하고 파일 헤더만 읽어 백업 체인을 생성합니다. 데이터베이스는 마이그레이션 프로세스 동안 "복원 중" 상태가 됩니다. 데이터베이스는 [NORECOVERY](/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) 모드로 복원되므로 마이그레이션 프로세스가 완료될 때까지 읽거나 쓰는 데 사용할 수 없습니다. 

여러 데이터베이스를 마이그레이션하는 경우 다음을 수행해야 합니다.
 
- 각 데이터베이스에 대한 백업을 Blob Storage에 있는 별도의 폴더에 저장합니다.
- 각 데이터베이스에 대해 개별적으로 LRS를 시작합니다.
- 다른 경로를 지정하여 Blob Storage 폴더를 구분합니다. 

자동 완성 또는 연속 모드로 LRS를 시작할 수 있습니다. 자동 완성 모드로 시작하는 경우 지정된 백업 파일이 마지막으로 복원되면 마이그레이션이 자동으로 완료됩니다. 연속 모드로 LRS를 시작하는 경우, 서비스에서는 추가된 모든 새 백업 파일을 연속적으로 복원하고 마이그레이션은 수동 중단으로만 완료됩니다. 

최종 로그 테일 백업을 수행하고 SQL Managed Instance에 복원된 것으로 표시된 후에 수동으로 중지하는 것이 좋습니다. 마지막 중단 단계를 수행하면 데이터베이스가 온라인 상태가 되고 SQL Managed Instance에서 읽기 및 쓰기가 가능하게 됩니다.

자동 완성을 통해 자동으로 또는 중단을 통해 수동으로 LRS가 중지된 후에는 SQL Managed Instance에서 온라인 상태가 된 데이터베이스에 대해 복원 프로세스를 계속할 수 없습니다. 자동 완성 또는 중단을 통해 마이그레이션이 완료된 후 추가 백업 파일을 복원하려면 데이터베이스를 삭제해야 합니다. 또한 LRS를 다시 시작하여 전체 백업 체인을 복원해야 합니다.

:::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="SQL Managed Instance에 대한 로그 재생 서비스 오케스트레이션 단계를 설명하는 다이어그램." border="false":::
    
| 작업 | 세부 정보 |
| :----------------------------- | :------------------------- |
| **1. SQL Server에서 Blob Storage로 데이터베이스 백업 복사**. | [AzCopy](../../storage/common/storage-use-azcopy-v10.md) 또는 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)를 사용하여 SQL Server에서 Blob Storage 컨테이너로 전체, 차등 및 로그 백업을 복사합니다. <br /><br />임의의 파일 이름을 사용합니다. LRS에는 특정 파일 명명 규칙이 필요하지 않습니다.<br /><br />여러 데이터베이스를 마이그레이션하는 경우 각 데이터베이스에 대해 별도의 폴더가 필요합니다. |
| **2. 클라우드에서 LRS 시작**. | 다음 cmdlet 중에서 선택하여 서비스를 다시 시작할 수 있습니다. PowerShell([start-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay)) 또는 Azure CLI([az_sql_midb_log_replay_start cmdlets](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)). <br /><br /> Blob Storage의 백업 폴더를 가리키는 각 데이터베이스에 대해 개별적으로 LRS를 시작합니다. <br /><br /> 서비스를 시작한 후에는 Blob Storage 컨테이너에서 백업을 가져오고 SQL Managed Instance에서 복원을 시작합니다.<br /><br /> 연속 모드에서 LRS를 시작한 경우 처음 업로드된 모든 백업이 복원된 후 서비스는 폴더에 업로드된 새 파일이 있는지 확인합니다. 서비스는 중지될 때까지 LSN(로그 시퀀스 번호) 체인을 기반으로 로그를 지속적으로 적용합니다. |
| **2.1. 작업 진행률 모니터링**. | 다음 cmdlet 중에서 선택하여 복원 작업 진행률을 모니터링할 수 있습니다. PowerShell([get-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay)) 또는 Azure CLI([az_sql_midb_log_replay_show cmdlets](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)). |
| **2.2. 필요한 경우 작업 중지**. | 마이그레이션 프로세스를 중지해야 하는 경우 다음 cmdlet 중에서 선택하여 중지합니다. PowerShell([stop-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)) 또는 Azure CLI([az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop)). <br /><br /> 작업을 중지하면 SQL Managed Instance에서 복원하는 데이터베이스가 삭제됩니다. 작업을 중지한 후에는 데이터베이스에 대해 LRS를 다시 시작할 수 없습니다. 마이그레이션 프로세스를 처음부터 다시 시작해야 합니다. |
| **3. 준비가 되면 클라우드 중단**. | 애플리케이션 및 워크로드를 중지합니다. 마지막 로그 테일 백업을 가져오고 Azure Blob Storage에 업로드합니다.<br /><br /> 다음 cmdlet 중에서 선택하여 LRS `complete` 작업을 시작하고 중단을 완료합니다. PowerShell([complete-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay)) 또는 Azure CLI [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete). 이 작업을 통해 LRS가 중지되고 데이터베이스는 온라인 상태가 되어 SQL Managed Instance에서 읽기 및 쓰기에 사용됩니다.<br /><br /> SQL Server에서 SQL Managed Instance로 애플리케이션 연결 문자열을 다시 지정합니다. 애플리케이션의 수동 연결 문자열 변경을 통해, 또는 자동으로(예: 애플리케이션이 속성에서 연결 문자열, 또는 데이터베이스를 읽을 수 있는 경우) 이 단계를 오케스트레이션해야 합니다. |

## <a name="requirements-for-getting-started"></a>시작 요구 사항

### <a name="sql-server-side"></a>SQL Server 측
- SQL Server 2008-2019
- 데이터베이스의 전체 백업(하나 또는 여러 파일)
- 차등 백업(하나 또는 여러 파일)
- 로그 백업(트랜잭션 로그 파일에 대해 분할하지 않음)
- 백업에 `CHECKSUM` 사용(필수)

### <a name="azure-side"></a>Azure 측
- PowerShell Az.SQL 모듈 버전 2.16.0 이상([설치](https://www.powershellgallery.com/packages/Az.Sql/) 또는 [Azure Cloud Shell](/azure/cloud-shell/)을 통해 액세스)
- Azure CLI 버전 2.19.0 이상([설치](/cli/azure/install-azure-cli))
- Azure Blob Storage 컨테이너 프로비전
- Blob Storage 컨테이너에 대해 읽기 및 나열 권한이 생성된 SAS(공유 액세스 서명) 보안 토큰

### <a name="migration-of-multiple-databases"></a>여러 데이터베이스의 마이그레이션
서로 다른 데이터베이스에 대한 백업 파일을 Blob Storage에 있는 별도 폴더에 두어야 합니다.

Blob Storage에서 적절한 폴더를 가리켜 각 데이터베이스에 대해 개별적으로 LRS를 시작합니다. LRS는 단일 관리되는 인스턴스당 최대 100개의 동시 복원 프로세스를 지원할 수 있습니다.

### <a name="azure-rbac-permissions"></a>Azure RBAC 권한
제공된 클라이언트를 통해 LRS를 실행하려면 다음 Azure 역할 중 하나가 필요합니다.
- 구독 소유자 역할
- [Managed Instance 기여자](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) 역할
- 다음 권한이 있는 사용자 지정 역할: `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>모범 사례

다음과 같은 최선의 구현 방법을 권장합니다.
- [Data Migration Assistant](/sql/dma/dma-overview)를 실행하여 데이터베이스를 SQL Managed Instance에 마이그레이션할 준비가 되었는지 확인합니다. 
- 단일 파일을 사용하는 대신 전체 및 차등 백업을 여러 파일로 분할합니다.
- 백업 압축을 사용합니다.
- Cloud Shell은 항상 최신 cdmlet 릴리스에 맞게 업데이트되므로 Cloud Shell을 사용해 스크립트를 실행합니다.
- LRS을 시작한 후 36시간 이내에 마이그레이션을 완료하도록 계획합니다. 이는 시스템 관리 소프트웨어 패치를 설치할 수 없도록 하는 유예 기간입니다.

> [!IMPORTANT]
> - 마이그레이션 프로세스가 완료될 때까지 LRS를 통해 복원되는 데이터베이스를 사용할 수 없습니다. 
> - LRS는 마이그레이션하는 동안 데이터베이스에 대한 읽기 전용 액세스를 지원하지 않습니다.
> - 마이그레이션이 완료된 후 LRS에서 복원 프로세스의 재시작을 지원하지 않으므로 마이그레이션 프로세스가 종료됩니다.

## <a name="steps-to-execute"></a>실행 단계

### <a name="make-backups-of-sql-server"></a>SQL Server의 백업 만들기

다음 옵션 중 하나를 사용하여 SQL Server의 백업을 만들 수 있습니다.

- 사용자 환경에서 Blob Storage에 대한 직접 백업을 제한하는 경우 로컬 디스크 스토리지에 백업한 다음 Azure Blob Storage에 파일을 업로드합니다.
- 환경 및 보안 프로시저에서 허용하는 경우 T-SQL의 `TO URL` 옵션을 사용하여 Blob Storage로 직접 백업합니다. 

로그 백업을 허용하기 위해 마이그레이션할 데이터베이스를 전체 복구 모드로 설정합니다.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

로컬 스토리지에 데이터베이스의 전체, 차등 및 로그 백업을 수동으로 만들려면 다음 샘플 T-SQL 스크립트를 사용합니다. LRS에 대해 필수이므로 `CHECKSUM` 옵션을 사용하도록 설정해야 합니다.

```SQL
-- Example of how to make a full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example of how to make a differential database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-a-storage-account"></a>스토리지 계정 만들기

Azure Blob Storage는 SQL Server와 SQL Managed Instance 간의 백업 파일에 대한 중간 스토리지로 사용됩니다. 스토리지 계정 내에 새 스토리지 계정 및 BLOB 컨테이너를 만들려면 다음 단계를 수행합니다.

1. [스토리지 계정 만들기](../../storage/common/storage-account-create.md?tabs=azure-portal)
2. 스토리지 계정 내부에 [BLOB 컨테이너 만들기](../../storage/blobs/storage-quickstart-blobs-portal.md).

### <a name="copy-backups-from-sql-server-to-blob-storage"></a>SQL Server에서 Blob Storage로 백업 복사

LRS를 사용하여 데이터베이스를 관리 인스턴스로 마이그레이션하는 경우 다음 방법을 사용하여 Blob Storage로 백업을 업로드할 수 있습니다.
- SQL Server 기본 [BACKUP TO URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) 기능 사용
- [AzCopy](../../storage/common/storage-use-azcopy-v10.md) 또는 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer)를 사용하여 Blob 컨테이너로 백업 업로드
- Azure Portal의 Storage Explorer 사용

### <a name="make-backups-from-sql-server-directly-to-blob-storage"></a>SQL Server에서 Blob Storage로 직접 백업 만들기
회사 및 네트워크 정책에서 이를 허용하는 경우 SQL Server 기본 [BACKUP TO URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) 옵션을 사용하여 SQL Server에서 Blob Storage로 직접 백업을 수행할 수 있습니다. 이 옵션을 사용할 수 있는 경우 로컬 스토리지에 백업을 만들고 Blob Storage에 업로드할 필요가 없습니다.

첫 번째 단계로 이 작업을 수행하려면 Blob Storage에 대한 SAS 인증 토큰을 생성한 다음 SQL Server 토큰을 가져와야 합니다. 두 번째 단계는 T-SQL의 `TO URL` 옵션을 사용하여 백업을 수행하는 것입니다. `CHEKSUM` 옵션이 사용 설정된 상태에서 모든 백업이 이루어지는 확인합니다.

참조를 위해 다음 샘플 코드는 Blob Storage에 대한 백업을 수행합니다. 이 예제에는 SAS 토큰을 가져오는 방법에 대한 지침이 포함되어 있지 않습니다. SAS 토큰을 생성하고 SQL Server로 가져오는 방법을 포함하는 자세한 지침은 자습서 [SQL Server에서 Azure Blob Storage 사용](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage)에서 확인할 수 있습니다. 

```SQL
-- Example of how to make a full database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO
-- Example of how to make a differential database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to a URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-a-blob-storage-sas-authentication-token-for-lrs"></a>LRS에 대한 Blob Storage SAS 인증 토큰 생성

Azure Blob Storage는 SQL Server와 SQL Managed Instance 간의 백업 파일에 대한 중간 스토리지로 사용됩니다. LRS에 대한 목록 및 읽기 권한만 있는 SAS 인증 토큰을 생성해야 합니다. 토큰은 LRS가 Blob Storage에 액세스하고 백업 파일을 사용하여 SQL Managed Instance에서 복원할 수 있도록 합니다. 

다음 단계에 따라 토큰을 생성합니다.

1. Azure Portal에서 Storage Explorer를 엽니다.
2. **BLOB 컨테이너** 를 확장합니다.
3. BLOB 컨테이너를 마우스 오른쪽 단추로 클릭하고, **공유 액세스 서명 가져오기** 를 선택합니다.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="SAS 인증 토큰을 생성하기 위한 선택 항목을 보여주는 스크린샷.":::

4. 토큰 만료 기간을 선택합니다. 토큰이 마이그레이션 기간 동안 유효한지 확인합니다.
5. 토큰에 대한 표준 시간대(UTC 또는 현지 시간)를 선택합니다.
    
   > [!IMPORTANT]
   > 토큰 및 관리 인스턴스의 표준 시간대가 일치하지 않을 수 있습니다. 표준 시간대를 고려하여 SAS 토큰에 적절한 유효 기간이 있는지 확인합니다. 가능한 경우 계획된 마이그레이션 기간 이전 및 이후 시간으로 표준 시간대를 설정합니다.
6. **읽기** 및 **나열** 권한만을 선택합니다.

   > [!IMPORTANT]
   > 다른 권한은 선택하지 마세요. 선택하면 LRS가 시작되지 않습니다. 이 보안 요구 사항은 의도적으로 설계되었습니다.
7. **만들기** 를 선택합니다.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="SAS 토큰 만료, 표준 시간대, 권한 선택과 만들기 단추를 보여주는 스크린샷.":::

지정된 유효 기간으로 SAS 인증이 생성됩니다. 다음 스크린샷에 표시된 것처럼 토큰의 URI 버전이 필요합니다.

:::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="SAS 토큰의 URI 버전 예시를 보여주는 스크린샷.":::

### <a name="copy-parameters-from-the-sas-token"></a>SAS 토큰에서 매개 변수 복사

SAS 토큰을 사용하여 LRS를 시작하기 전에 해당 구조를 이해해야 합니다. 생성된 SAS 토큰의 URI는 이 예와 같이 물음표(`?`)로 구분된 두 부분으로 구성됩니다.

:::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="로그 재생 서비스용으로 생성된 SAS 토큰에 대한 URI 예." border="false":::

`https://`부터 물음표(`?`)까지인 첫 번째 부분은 LRS에 대한 입력으로 공급되는 `StorageContainerURI` 매개 변수에 사용됩니다. 데이터베이스 백업 파일이 저장된 폴더에 대한 LRS 정보를 제공합니다.

물음표(`?`)부터 문자열 끝까지인 두 번째 부분은 `StorageContainerSasToken` 매개 변수입니다. 지정된 시간 동안 유효한 실제 서명된 인증 토큰입니다. 예제에 표시된 것처럼 이 부분이 반드시 `sp=`로 시작하는 것은 아닙니다. 사례에 따라 다를 수 있습니다.

다음과 같이 매개 변수를 복사합니다.

1. `https://`부터 물음표(`?`)까지인 토큰의 첫 번째 부분을 복사합니다. PowerShell 또는 Azure CLI에서 `StorageContainerUri` 매개 변수로 사용하여 LRS를 시작합니다.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="토큰의 첫 번째 부분 복사를 보여주는 스크린샷.":::

2. 물음표(`?`)부터 문자열 끝까지인 토큰의 두 번째 부분을 복사합니다. PowerShell 또는 Azure CLI에서 `StorageContainerSasToken` 매개 변수로 사용하여 LRS를 시작합니다.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="토큰의 두 번째 부분 복사를 보여주는 스크린샷.":::
   
> [!NOTE]
> 토큰의 부분을 복사할 때 물음표를 포함하지 않습니다.

### <a name="log-in-to-azure-and-select-a-subscription"></a>Azure 로그인 후 구독 선택

다음 PowerShell cmdlet을 사용하여 Azure에 로그인합니다.

```powershell
Login-AzAccount
```

다음 PowerShell cmdlet을 사용하여 관리 인스턴스가 있는 적절한 구독을 선택합니다.

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>마이그레이션 시작

LRS를 시작하여 마이그레이션을 시작합니다. 자동 완성 또는 연속 모드로 서비스를 시작할 수 있습니다. 

자동 완성 모드를 사용하는 경우 지정된 백업 파일이 마지막으로 복원되면 마이그레이션이 자동으로 완료됩니다. 이 옵션을 사용하려면 start 명령으로 마지막 백업 파일의 파일 이름을 지정해야 합니다. 

연속 모드를 사용하는 경우 서비스는 계속해서 추가된 새 백업 파일을 복원합니다. 마이그레이션은 수동 중단으로만 완료됩니다. 

### <a name="start-lrs-in-autocomplete-mode"></a>자동 완성 모드로 LRS 시작

자동 완성 모드로 LRS를 시작하려면 다음 PowerShell 또는 Azure CLI 명령을 사용합니다. `-LastBackupName` 매개 변수를 사용하여 마지막 백업 파일 이름을 지정합니다. 지정된 마지막 백업 파일을 복원하면 서비스가 자동으로 중단을 시작합니다.

PowerShell을 사용하여 자동 완성 모드로 LRS를 시작하는 예는 다음과 같습니다.

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

Azure CLI를 사용하여 자동 완성 모드로 LRS를 시작하는 예는 다음과 같습니다.

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>연속 모드에서 LRS 시작

PowerShell을 사용하여 연속 모드로 LRS를 시작하는 예는 다음과 같습니다.

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Azure CLI를 사용하여 연속 모드로 LRS를 시작하는 예는 다음과 같습니다.

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

연속 모드에서 LRS를 시작하기 위한 PowerShell 및 CLI 클라이언트는 동기식입니다. 따라서 클라이언트는 작업 시작 성공 또는 실패를 보고하는 API 응답을 기다립니다. 

기다리는 동안 명령은 명령 프롬프트에 컨트롤을 반환하지 않습니다. 마이그레이션 환경을 스크립팅하는 경우 스크립트의 나머지 부분을 계속하기 위해 LRS start 명령이 즉시 다시 제공되도록 하려면 `-AsJob` 스위치를 사용하여 PowerShell을 백그라운드 작업으로 실행할 수 있습니다. 예를 들어:

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

백그라운드 작업을 시작하면 작업이 완료되는 데 시간이 오래 걸리는 경우에도 작업 개체가 즉시 반환됩니다. 작업이 실행되는 동안 중단 없이 세션에서 작업을 계속할 수 있습니다. 백그라운드 작업으로 PowerShell을 실행하는 방법에 대한 자세한 내용은 [Powershell Start-Job](/powershell/module/microsoft.powershell.core/start-job#description) 설명서를 참조하세요.

마찬가지로, Linux에서 Azure CLI 명령을 백그라운드 프로세스로 시작하려면 LRS 시작 명령 끝에 앰퍼샌드(`&`)를 사용합니다.

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> LRS를 시작한 후 시스템에서 관리하는 모든 소프트웨어 패치가 36시간 동안 중지됩니다. 이 기간 후에는 다음 자동화된 소프트웨어 패치가 자동으로 LRS를 중지합니다. 이런 경우 마이그레이션을 다시 시작하고 처음부터 다시 시작해야 합니다. 

## <a name="monitor-the-migration-progress"></a>마이그레이션 진행률 모니터링

PowerShell을 통해 마이그레이션의 진행률을 모니터링하려면 다음 명령을 사용합니다.

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Azure CLI를 통해 마이그레이션의 진행률을 모니터링하려면 다음 명령을 사용합니다.

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>마이그레이션 중지

마이그레이션을 중지해야 하는 경우 다음 cmdlet을 사용합니다. 마이그레이션을 중지하면 SQL Managed Instance에서 복원 중인 데이터베이스가 삭제되므로 마이그레이션을 다시 시작할 수 없습니다.

PowerShell을 통해 마이그레이션 프로세스를 중지하려면 다음 명령을 사용합니다.

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Azure CLI를 통해 마이그레이션 프로세스를 중지하려면 다음 명령을 사용합니다.

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>마이그레이션 완료(연속 모드)

연속 모드에서 LRS를 시작한 경우 모든 백업이 복원되었는지 확인한 다음 중단을 시작하면 마이그레이션이 완료됩니다. 중단 이후 데이터베이스가 마이그레이션되고 읽기 및 쓰기 액세스가 준비됩니다.

PowerShell을 통해 LRS 연속 모드의 마이그레이션 프로세스를 완료하려면 다음 명령을 사용합니다.

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Azure CLI를 통해 LRS 연속 모드의 마이그레이션 프로세스를 완료하려면 다음 명령을 사용합니다.

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>기능 제한 사항

LRS의 기능 제한 사항은 다음과 같습니다.
- 복원하는 데이터베이스는 마이그레이션 프로세스 중에 읽기 전용 액세스에 사용할 수 없습니다.
- LRS 시작 이후 시스템에서 관리하는 모든 소프트웨어 패치가 36시간 동안 차단됩니다. 이 기간이 만료되면 다음 소프트웨어 업데이트는 LRS를 중지합니다. 그런 다음 LRS를 처음부터 다시 시작해야 합니다.
- LRS를 사용하려면 `CHECKSUM` 옵션이 사용 설정된 상태에서 SQL Server의 데이터베이스가 백업되어야 합니다.
- LRS에서 사용할 SAS 토큰은 전체 Azure Blob Storage 컨테이너에 대해 생성되어야 하며, 읽기 및 나열 권한만 있어야 합니다.
- 서로 다른 데이터베이스에 대한 백업 파일은 Blob Storage에서 별도의 폴더에 있어야 합니다.
- 파일 이름 에% 및 $ 문자를 포함 하는 백업 파일은 LRS에서 사용할 수 없습니다. 이러한 파일 이름 이름을 바꾸는 것이 좋습니다.
- Blob Storage에서 백업 파일이 포함된 별도의 폴더를 가리키는 각 데이터베이스에 대해 LRS를 별도로 시작해야 합니다.
- LRS는 단일 관리되는 인스턴스당 최대 100개의 동시 복원 프로세스를 지원할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

LRS 시작 후에는 모니터링 cmdlet(`get-azsqlinstancedatabaselogreplay` 또는 `az_sql_midb_log_replay_show`)을 사용하여 작업의 상태를 확인합니다. LRS가 시작되지 않고 오류가 발생하는 경우 가장 일반적인 문제를 확인합니다.

- SQL Managed Instance의 기존 데이터베이스가 SQL Server에서 마이그레이션하려는 데이터베이스와 동일한 이름을 갖고 있나요? 데이터베이스 중 하나의 이름을 바꿔서 이 충돌을 해결합니다.
- SQL Server에서 데이터베이스 백업이 `CHECKSUM` 옵션을 통해 이루어졌나요?
- SAS 토큰에 대한 권한이 LRS에 대한 읽기 및 나열인가요?
- `sv=2020-02-10...`으로 시작하는 콘텐츠를 포함하여 LRS에 대한 SAS 토큰에서 물음표(`?`) 이후 부분을 복사했나요? 
- SAS 토큰 유효 기간이 마이그레이션을 시작하고 완료하는 기간에 해당하나요? SQL Managed Instance 및 SAS 토큰에 사용되는 서로 다른 표준 시간대로 인해 불일치가 발생할 수 있습니다. SAS 토큰을 다시 생성하고 토큰의 유효 기간을 현재 날짜 전과 후로 늘립니다.
- 데이터베이스 이름, 리소스 그룹 이름 및 관리 인스턴스 이름의 철자가 정확한가요?
- 자동 완성 모드로 LRS를 시작한 경우, 지정된 마지막 백업 파일의 이름이 올바른가요?

## <a name="next-steps"></a>다음 단계
- [SQL Server에서 SQL Managed Instance로의 마이그레이션](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)에 대해 자세히 알아봅니다.
- [SQL Server와 SQL Managed Instance의 차이점](transact-sql-tsql-differences-sql-server.md)에 대해 자세히 알아봅니다.
- [Azure로 마이그레이션된 워크로드의 비용 및 크기 조정 모범 사례](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)에 대해 자세히 알아봅니다.
