---
title: BACPAC 파일을 가져와서 Azure SQL Database의 데이터베이스 만들기
description: BACPAC 파일을 사용하여 Azure SQL Database 또는 Azure SQL Managed Instance의 새 데이터베이스를 만듭니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: sqldbrb=1, devx-track-azurepowershell, mode-other
ms.devlang: ''
ms.topic: quickstart
author: SQLSourabh
ms.author: sourabha
ms.reviewer: cawrites, mathoma
ms.date: 10/29/2020
ms.openlocfilehash: 0b5c96269d6c0a241a3bcf5920e7b35c9b1b3f1d
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133070567"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>빠른 시작: Azure SQL Database 또는 Azure SQL Managed Instance의 데이터베이스로 BACPAC 파일을 가져옵니다.
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) 파일을 사용하여 Azure SQL Database 또는 SQL Managed Instance의 SQL Server 데이터베이스를 가져올 수 있습니다. Azure Blob Storage(표준 스토리지만 해당)에 저장된 BACPAC 파일에서 또는 온-프레미스 위치의 로컬 스토리지에서 데이터를 가져올 수 있습니다. 더 빠른 리소스를 더 많이 제공하여 가져오기 속도를 높이려면 가져오기 프로세스 중에 데이터베이스를 더 높은 서비스 계층 및 컴퓨팅 크기로 확장합니다. 그런 다음, 가져오기가 성공하면 크기를 축소할 수 있습니다.

> [!NOTE]
> 가져온 데이터베이스의 호환성 수준은 원본 데이터베이스의 호환성 수준에 기반합니다.

> [!IMPORTANT]
> 데이터베이스를 가져온 후에는 데이터베이스를 현재 호환성 수준에서(AdventureWorks2008R2 데이터베이스에 대해 수준 100) 또는 더 높은 수준에서 작동하도록 선택할 수 있습니다. 특정 호환성 수준에서 데이터베이스를 운영하기 위한 옵션 및 그 영향에 대한 자세한 내용은 [ALTER DATABASE 호환성 수준](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)을 참조하세요. 또한 호환성 수준과 관련된 추가 데이터베이스 수준 설정에 대한 자세한 내용은 [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)을 참조하세요.

## <a name="using-azure-portal"></a>Azure Portal 사용

이 비디오를 시청하여 Azure Portal에서 BACPAC 파일을 통해 가져오는 방법을 확인하거나 아래에서 계속 읽어 보세요.

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

[Azure Portal은 Azure SQL Database에서 Azure Blob Storage에 저장된 BACPAC 파일을 통해 단일 데이터베이스를 만드는 기능 *만* 지원합니다.](https://portal.azure.com) 

BACPAC 파일을 사용하여 [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)로 데이터베이스를 마이그레이션하려면 SQL Server Management Studio 또는 SQLPackage를 사용합니다. 현재는 Azure Portal 또는 Azure PowerShell 사용이 지원되지 않습니다.

> [!NOTE]
> Azure Portal 또는 PowerShell을 통해 제출된 가져오기/내보내기 요청을 처리하는 머신은 BACPAC 파일뿐만 아니라 DacFX(데이터 계층 애플리케이션 프레임워크)에서 생성한 임시 파일도 저장해야 합니다. 필요한 디스크 공간은 동일한 크기의 데이터베이스 간에도 크게 다르며, 데이터베이스 크기의 최대 3배까지 디스크 공간이 필요할 수 있습니다. 가져오기/내보내기 요청을 실행하는 머신에는 450GB의 로컬 디스크 공간만 있습니다. 따라서 일부 요청은 `There is not enough space on the disk` 오류와 함께 실패할 수 있습니다. 이 경우 해결 방법은 충분한 로컬 디스크 공간이 있는 머신에서 sqlpackage.exe를 실행하는 것입니다. 이 문제를 방지하려면 SqlPackage를 사용하여 150GB보다 큰 데이터베이스를 가져오거나 내보내는 것이 좋습니다.

1. Azure Portal을 사용하여 BACPAC 파일에서 새 단일 데이터베이스로 가져오려면 적절한 서버 페이지를 연 다음, 도구 모음에서 **데이터베이스 가져오기** 를 선택합니다.  

   ![데이터베이스 가져오기1](./media/database-import/sql-server-import-database.png)

1. BACPAC 파일의 스토리지 계정 및 컨테이너를 선택한 다음, 가져올 BACPAC 파일을 선택합니다.

1. 새 데이터베이스의 크기(일반적으로 원본과 동일)를 지정하고 대상 SQL Server 자격 증명을 제공합니다. 새 Azure SQL Database 데이터베이스에 가능한 값 목록은 [데이터베이스 만들기](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)를 참조하세요.

   ![데이터베이스 가져오기2](./media/database-import/sql-server-import-database-settings.png)

1. **확인** 을 클릭합니다.

1. 가져오기 진행률을 모니터링하려면 데이터베이스의 서버 페이지를 열고, **설정** 아래에서 **가져오기/내보내기 기록** 을 선택합니다. 성공하면 가져오기가 **완료됨** 상태가 됩니다.

   ![데이터베이스 가져오기 상태](./media/database-import/sql-server-import-database-history.png)

1. 데이터베이스가 서버에서 라이브 상태인지 확인하려면 **SQL 데이터베이스** 를 선택하고 새 데이터베이스가 **온라인** 상태인지 확인합니다.

## <a name="using-sqlpackage"></a>SqlPackage 사용

[SqlPackage](/sql/tools/sqlpackage) 명령줄 유틸리티를 사용하여 SQL Server 데이터베이스를 가져오려면 [매개 변수 및 속성 가져오기](/sql/tools/sqlpackage#import-parameters-and-properties)를 참조하세요. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 및 [Visual Studio용 SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt)에는 SqlPackage가 포함되어 있습니다. Microsoft 다운로드 센터에서 최신 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)를 다운로드할 수도 있습니다. 

규모 및 성능과 관련하여 대부분의 프로덕션 환경에서는 Azure Portal보다 SqlPackage를 사용하는 것이 좋습니다. `BACPAC` 파일을 사용하는 마이그레이션에 대한 SQL Server 고객 자문 팀 블로그는 [BACPAC 파일을 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files)을 참조하세요.

DTU 기반 프로비저닝 모델은 각 계층에 대한 데이터베이스 최대 크기 값을 선택할 수 있도록 해줍니다. 데이터베이스를 가져올 때 [지원되는 값 중 하나를 사용합니다](/sql/t-sql/statements/create-database-transact-sql). 

다음 SqlPackage 명령은 로컬 스토리지의 **AdventureWorks2008R2** 데이터베이스를 **mynewserver20170403** 이라는 논리적 SQL 서버로 가져옵니다. **프리미엄** 서비스 계층과 **P6** 서비스 목표로 **myMigratedDatabase** 라는 새 데이터베이스를 만듭니다. 이러한 값을 사용자 환경에 적절하게 변경합니다.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> 회사 방화벽 뒤에서 Azure SQL Database에 연결하려면 방화벽에 1433 포트가 열려 있어야 합니다. SQL Managed Instance에 연결하려면 [지점 및 사이트 간 연결](../managed-instance/point-to-site-p2s-configure.md) 또는 Express 경로 연결이 필요합니다.

이 예제는 Active Directory 유니버설 인증으로 SqlPackage를 사용하여 데이터베이스를 가져오는 방법을 보여줍니다.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>PowerShell 사용

> [!NOTE]
> [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)는 현재 Azure PowerShell을 사용하여 BACPAC 파일에서 인스턴스 데이터베이스로 데이터베이스를 마이그레이션하는 기능을 지원하지 않습니다. SQL Managed Instance로 가져오려면 SQL Server Management Studio 또는 SQLPackage를 사용합니다.

> [!NOTE]
> 포털 또는 PowerShell을 통해 제출된 가져오기/내보내기 요청을 처리하는 머신은 bacpac 파일뿐만 아니라 DacFX(데이터 계층 애플리케이션 프레임워크)에서 생성한 임시 파일도 저장해야 합니다. 필요한 디스크 공간은 동일한 크기의 DB 간에도 크게 다르며, 데이터베이스 크기의 최대 3배를 차지할 수 있습니다. 가져오기/내보내기 요청을 실행하는 머신에는 450GB의 로컬 디스크 공간만 있습니다. 따라서 일부 요청은 "디스크 공간이 부족합니다." 오류와 함께 실패할 수 있습니다. 이 경우 해결 방법은 충분한 로컬 디스크 공간이 있는 머신에서 sqlpackage.exe를 실행하는 것입니다. 이 문제를 방지하려면 150GB보다 큰 데이터베이스를 가져오거나 내보낼 때 SqlPackage를 사용합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> PowerShell Azure RM(Resource Manager) 모듈은 여전히 지원되지만 향후 모든 개발은 Az.Sql 모듈을 위한 것입니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 계속 수신할 예정입니다.  Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다. 호환성에 대한 자세한 내용은 [새로운 Azure PowerShell Az 모듈 소개](/powershell/azure/new-azureps-module-az)를 참조하세요.

[New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) cmdlet을 사용하여 Azure에 데이터베이스 가져오기 요청을 제출합니다. 데이터베이스 크기에 따라 가져오기를 완료하는 데 다소 시간이 걸릴 수 있습니다. DTU 기반 프로비저닝 모델은 각 계층에 대한 데이터베이스 최대 크기 값을 선택할 수 있도록 해줍니다. 데이터베이스를 가져올 때 [지원되는 값 중 하나를 사용합니다](/sql/t-sql/statements/create-database-transact-sql). 

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

[Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) cmdlet을 사용하여 가져오기의 진행 상황을 확인할 수 있습니다. 요청 직후에 cmdlet을 실행하면 대개 `Status: InProgress`가 반환됩니다. `Status: Succeeded`가 표시되면 가져오기가 완료된 것입니다.

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az-sql-db-import](/cli/azure/sql/db#az_sql_db_import) 명령을 사용하여 Azure에 데이터베이스 가져오기 요청을 제출합니다. 데이터베이스 크기에 따라 가져오기를 완료하는 데 다소 시간이 걸릴 수 있습니다. DTU 기반 프로비저닝 모델은 각 계층에 대한 데이터베이스 최대 크기 값을 선택할 수 있도록 해줍니다. 데이터베이스를 가져올 때 [지원되는 값 중 하나를 사용합니다](/sql/t-sql/statements/create-database-transact-sql). 

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

> [!TIP]
> 다른 스크립트 예제는 [BACPAC 파일에서 데이터베이스 가져오기](scripts/import-from-bacpac-powershell.md)를 참조하세요.

## <a name="cancel-the-import-request"></a>가져오기 요청 취소

[데이터베이스 작업 - 취소 API](/rest/api/sql/databaseoperations/cancel) 또는 Powershell [Stop-AzSqlDatabaseActivity 명령](/powershell/module/az.sql/Stop-AzSqlDatabaseActivity)(여기에서는 powershell 명령의 예)을 사용합니다.

```cmd
Stop-AzSqlDatabaseActivity -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -OperationId $Operation.OperationId
```


## <a name="limitations"></a>제한 사항

- 탄력적 풀의 데이터베이스로 가져오기는 지원되지 않습니다. 단일 데이터베이스로 데이터를 가져온 다음, 해당 데이터베이스를 탄력적 풀로 이동할 수 있습니다.
- Azure 서비스에 대한 액세스 허용이 끄기로 설정되어 있으면 가져오기/내보내기 서비스가 작동하지 않습니다. 그러나 Azure VM에서 sqlpackage.exe를 수동으로 실행하거나 DACFx API를 사용하여 코드에서 직접 내보내기를 수행하면 이 문제를 해결할 수 있습니다.
- 가져오기는 새 데이터베이스를 만드는 동안 백업 스토리지 중복도를 지정하는 것을 지원하지 않으며 기본 지역 중복 백업 스토리지 중복도를 사용하여 새 데이터베이스를 만듭니다. 이 문제를 해결하려면 먼저 Azure Portal 또는 PowerShell을 사용하여 원하는 백업 스토리지 중복도로 빈 데이터베이스를 만든 다음, 이 빈 데이터베이스에 BACPAC를 가져옵니다. 

> [!NOTE]
> Azure SQL Database의 구성 가능한 백업 스토리지 중복도는 현재 동남 아시아 Azure 지역에서만 공개 미리 보기로 제공됩니다.

## <a name="import-using-wizards"></a>마법사를 사용하여 가져오기

다음 마법사를 사용할 수도 있습니다.

- [SQL Server Management Studio의 데이터 계층 애플리케이션 가져오기 마법사](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard)
- [SQL Server 가져오기 및 내보내기 마법사](/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard)

## <a name="next-steps"></a>다음 단계

- Azure SQL Database의 데이터베이스에 연결하고 쿼리하는 방법을 알아보려면 [빠른 시작: Azure SQL Database: SQL Server Management Studio를 사용하여 데이터에 연결 및 쿼리](connect-query-ssms.md)를 참조하세요.
- BACPAC 파일을 사용한 마이그레이션에 관한 SQL Server 고객 자문 팀 블로그는 [BACPAC 파일을 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407)을 참조하세요.
- 성능 권장 사항을 비롯한 전체 SQL Server 데이터베이스 마이그레이션 프로세스에 대한 설명은 [Azure SQL Database로 SQL Server 데이터베이스 마이그레이션](migrate-to-database-from-sql-server.md)을 참조하세요.
- 스토리지 키 및 공유 액세스 서명을 안전하게 관리하고 공유하는 방법을 알아보려면 [Azure Storage 보안 가이드](../../storage/blobs/security-recommendations.md)를 참조하세요.
