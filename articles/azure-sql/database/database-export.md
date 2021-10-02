---
title: Azure SQL 데이터베이스를 BACPAC 파일로 내보내기(Azure Portal)
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure Portal을 사용하여 데이터베이스를 BACPAC 파일로 내보냅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
author: cawrites
ms.custom: sqldbrb=2
ms.author: chadam
ms.reviewer: ''
ms.date: 01/11/2021
ms.topic: how-to
ms.openlocfilehash: a4882c763baf00220ac610ef9c0c8c6f5f56740d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361068"
---
# <a name="export-to-a-bacpac-file---azure-sql-database-and-azure-sql-managed-instance"></a>BACPAC 파일로 내보내기 - Azure SQL Database 및 Azure SQL Managed Instance

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

다른 플랫폼에 보관하거나 이동하기 위해 데이터베이스를 내보내야 할 경우 데이터베이스 스키마 및 데이터를 [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#Anchor_4) 파일로 내보낼 수 있습니다. BACPAC 파일은 메타데이터 및 데이터베이스의 데이터를 포함하는 BACPAC의 확장명을 가진 ZIP 파일입니다. BACPAC 파일은 Azure Blob Storage 또는 온-프레미스 스토리지의 로컬 스토리지에 저장할 수 있으며 나중에 Azure SQL Database, Azure SQL Managed Instance 또는 SQL Server 인스턴스로 다시 가져올 수 있습니다.

## <a name="considerations"></a>고려 사항

- 내보내기에서 트랜잭션이 일관되도록 하려면 내보내기 중에 쓰기 작업을 수행하지 않거나 데이터베이스의 [트랜잭션 일치](database-copy.md) 복사본에서 내보내도록 해야 합니다.
- Blob Storage로 내보내는 경우 BACPAC 파일의 최대 크기는 200GB입니다. 더 큰 BACPAC 파일을 보관하려면 로컬 스토리지로 내보냅니다.
- 이 문서에서 설명하는 방법을 사용하여 Azure Premium Storage에서 BACPAC 파일을 내보낼 수는 없습니다.
- 방화벽 뒤의 스토리지는 현재 지원되지 않습니다.
- StorageURI의 스토리지 파일 이름 또는 입력 값은 길이가 128자 미만이어야 하며 '. '로 끝날 수 없으며 공백 문자 또는 '<,>,*,%,&,:,\,/,?'.와 같은 특수 문자를 포함할 수 없습니다. 
- 내보내기 작업이 20시간을 초과하면 취소될 수 있습니다. 내보내는 중에 성능을 향상시키기 위해 다음을 수행할 수 있습니다.

  - 컴퓨팅 크기를 일시적으로 늘립니다.
  - 내보내기 중에 모든 읽기 및 쓰기 작업 중단
  - 모든 대형 테이블에 null이 아닌 값의 [클러스터형 인덱스](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described) 를 사용합니다. 클러스터형 인덱스가 없는 경우 6~12시간 이상 소요되면 내보내기에 실패할 수 있습니다. 전체 테이블 내보내기를 시도하려면 내보내기 서비스에서 테이블 스캔을 완료해야 하기 때문입니다. 테이블이 내보내기에 최적화되었는지 확인하는 좋은 방법은 **DBCC SHOW_STATISTICS** 를 실행하고 *RANGE_HI_KEY* 가 null이 아닌지와 해당 값이 잘 배포되어 있는지 검토하는 것입니다. 자세한 내용은 [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql)를 참조하세요.

> [!NOTE]
> BACPAC는 백업에 사용되는 목적이 아니며 작업을 복원합니다. Azure에서는 모든 사용자 데이터베이스의 백업이 자동으로 생성됩니다. 자세한 내용은 [비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md) 및 [SQL Database 백업](automated-backups-overview.md)을 참조하세요.

## <a name="the-azure-portal"></a>Azure Portal

Azure Portal를 사용하여 [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) 에서 데이터베이스의 BACPAC를 내보내는 기능은 현재 지원되지 않습니다. 대신 SQL Server Management Studio 또는 SQLPackage를 사용해야 합니다.

> [!NOTE]
> Azure Portal 또는 PowerShell을 통해 제출된 가져오기/내보내기 요청을 처리하는 머신은 BACPAC 파일뿐만 아니라 DacFX(데이터 계층 애플리케이션 프레임워크)에서 생성한 임시 파일도 저장해야 합니다. 필요한 디스크 공간은 동일한 크기의 데이터베이스 간에도 크게 다르며, 데이터베이스 크기의 최대 3배까지 디스크 공간이 필요할 수 있습니다. 가져오기/내보내기 요청을 실행하는 머신에는 450GB의 로컬 디스크 공간만 있습니다. 따라서 일부 요청은 `There is not enough space on the disk` 오류와 함께 실패할 수 있습니다. 이 경우 해결 방법은 충분한 로컬 디스크 공간이 있는 머신에서 sqlpackage.exe를 실행하는 것입니다. 이 문제를 방지하려면 [SqlPackage](#sqlpackage-utility)를 사용하여 150GB보다 큰 데이터베이스를 가져오거나 내보내는 것이 좋습니다.

1. [Azure Portal](https://portal.azure.com)을 사용하여 데이터베이스를 내보내려면 데이터베이스에 대한 페이지를 열고 도구 모음에서 **내보내기** 를 클릭합니다.

   ![내보내기 단추를 강조 표시하는 스크린샷.](./media/database-export/database-export1.png)

2. BACPAC 파일 이름을 지정하고 내보내기에 대해 기존 Azure Storage 계정 및 컨테이너를 선택한 다음, 원본 데이터베이스에 액세스할 수 있는 적절한 자격 증명을 제공합니다. Azure 관리자인 경우에는 Azure 관리자가 Azure SQL Database 또는 Azure SQL Managed Instance에서 관리자 권한을 보유하고 있지 않으므로 SQL **서버 관리자 로그인** 이 필요합니다.

    ![데이터베이스 내보내기](./media/database-export/database-export2.png)

3. **확인** 을 클릭합니다.

4. 내보내기 작업의 진행률을 모니터링하려면 내보낼 데이터베이스가 포함된 서버에 대한 페이지를 엽니다. **설정** 에서 **가져오기/내보내기 기록** 을 클릭합니다.

   ![내보내기 기록](./media/database-export/export-history.png)

## <a name="sqlpackage-utility"></a>SQLPackage 유틸리티

[SqlPackage](/sql/tools/sqlpackage) 명령줄 유틸리티를 사용하여 SQL의 데이터 바이러스를 내보내려면 [매개 변수 및 속성 내보내기](/sql/tools/sqlpackage#export-parameters-and-properties)를 참조하세요. SQLPackage 유틸리티는 최신 버전의 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 및 [Visual Studio용 SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt)가 함께 제공되며, Microsoft 다운로드 센터에서 직접 최신 버전의 [SqlPackage](/sql/tools/sqlpackage/sqlpackage-download)를 다운로드할 수 있습니다.

대부분의 프로덕션 환경에서 규모 및 성능에 SQLPackage 유틸리티를 사용하는 것이 좋습니다. BACPAC 파일을 사용한 마이그레이션에 관한 SQL Server 고객 자문 팀 블로그는 [BACPAC 파일을 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files)을 참조하세요.

이 예제는 Active Directory 유니버설 인증으로 SqlPackage.exe를 사용하여 데이터베이스를 내보내는 방법을 보여줍니다.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="sql-server-management-studio-ssms"></a>SSMS(SQL Server Management Studio)

최신 버전의 SQL Server Management Studio에서는 Azure SQL Database 또는 SQL Managed Instance 데이터베이스의 데이터베이스를 BACPAC 파일로 내보내는 마법사를 제공합니다. [데이터 계층 애플리케이션 내보내기](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)를 참조하세요.

## <a name="powershell"></a>PowerShell

> [!NOTE]
> [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)는 현재 Azure PowerShell을 사용하여 데이터베이스를 BACPAC 파일로 내보내는 기능을 지원하지 않습니다. 관리되는 인스턴스를 BACPAC 파일로 내보내려면 SQL Server Management Studio 또는 SQLPackage를 사용합니다.

[New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) cmdlet을 사용하여 Azure SQL Database 서비스에 데이터베이스 내보내기 요청을 제출합니다. 데이터베이스 크기에 따라 내보내기 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

내보내기 요청의 상태를 확인하려면 [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) cmdlet을 사용합니다. 이 요청 직후에 이 명령을 실행하면 **Status: InProgress** 가 반환됩니다. **Status : Succeeded** 가 표시되면 내보내기가 완료된 것입니다.

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```
## <a name="cancel-the-export-request"></a>내보내기 요청 취소

[데이터베이스 작업 - 취소 API](/rest/api/sql/databaseoperations/cancel) 또는 Powershell [Stop-AzSqlDatabaseActivity 명령](/powershell/module/az.sql/Stop-AzSqlDatabaseActivity)(여기에서는 powershell 명령의 예)을 사용합니다.

```cmd
Stop-AzSqlDatabaseActivity -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -OperationId $Operation.OperationId
```

## <a name="next-steps"></a>다음 단계

- 보관을 위해 데이터베이스를 내보내는 방법의 대안으로 사용되는 단일 데이터베이스 및 풀링된 데이터베이스의 장기 백업 보존에 대한 자세한 내용은 [장기 백업 보존](long-term-retention-overview.md)을 참조하세요. SQL 에이전트 작업을 사용하여 장기 백업 보존의 대안으로 [복사 전용 데이터베이스 백업](/sql/relational-databases/backup-restore/copy-only-backups-sql-server)을 예약할 수 있습니다.
- BACPAC 파일을 사용한 마이그레이션에 관한 SQL Server 고객 자문 팀 블로그는 [BACPAC 파일을 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files)을 참조하세요.
- SQL Server Database에 BACPAC를 가져오는 방법에 대해 자세히 알아보려면 [SQL Server Database로 BACPAC 가져오기](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)를 참조하세요.
- SQL Server 데이터베이스에서 BACPAC를 내보내는 방법을 알아보려면 [데이터 계층 애플리케이션 내보내기](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)를 참조하세요.
- 데이터 마이그레이션 서비스를 사용하여 데이터베이스 마이그레이션에 대한 자세한 내용은 [DMS를 사용하여 오프라인에서 SQL Server를 Azure SQL Database로 마이그레이션](../../dms/tutorial-sql-server-to-azure-sql.md)을 참조하세요.
- 마이그레이션에 대한 사전 준비로 SQL Server에서 Azure SQL Database로 내보내는 경우 [Azure SQL Database에 SQL Server 데이터베이스 마이그레이션](migrate-to-database-from-sql-server.md)을 참조하세요.
- 스토리지 키 및 공유 액세스 서명을 안전하게 관리하고 공유하는 방법을 알아보려면 [Azure Storage 보안 가이드](../../storage/blobs/security-recommendations.md)를 참조하세요.
