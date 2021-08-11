---
title: XEvent 이벤트 파일 코드
description: Azure SQL Database에서 확장 이벤트의 이벤트 파일 대상을 보여주는 2단계 코드 샘플에 대해 PowerShell 및 Transact-SQL을 제공합니다. Azure Storage는 이 시나리오의 필수 부분입니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: PowerShell
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 06/06/2020
ms.openlocfilehash: 869f2c8ca5f0cbfb422c1aaa65907a2ad941e86a
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112678145"
---
# <a name="event-file-target-code-for-extended-events-in-azure-sql-database"></a>Azure SQL Database의 확장 이벤트에 대한 이벤트 파일 대상 코드
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

확장 이벤트에 대한 정보를 캡처하고 보고하는 확실한 방법을 위한 전체 코드 샘플이 필요할 수 있습니다.

Microsoft SQL Server의 [이벤트 파일 대상](/previous-versions/sql/sql-server-2016/ff878115(v=sql.130)) 을 사용하여 이벤트 출력을 로컬 하드 드라이브 파일에 저장합니다. 하지만 이러한 파일은 Azure SQL Database에서 사용할 수 없습니다. 대신 Azure Storage 서비스를 사용하여 이벤트 파일 대상을 지원합니다.

이 항목에서는 2단계 코드 샘플을 제공합니다.

- 클라우드에서 Azure Storage 컨테이너를 만드는 PowerShell.
- Transact-SQL:
  - Azure Storage 컨테이너를 이벤트 파일 대상에 할당합니다.
  - 이벤트 세션 등을 만들고 시작합니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 여전히 Azure SQL Database에서 지원되지만 향후의 모든 개발은 Az.Sql 모듈을 위한 것입니다. 이러한 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다.

- Azure 계정 및 구독 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
- 테이블을 만들 수 있는 데이터베이스.
  
  - 또는 몇 분 이내에 [**AdventureWorksLT** 데모 데이터베이스를 만들](single-database-create-quickstart.md) 수 있습니다.

- SQL Server Management Studio(ssms.exe)(이상적으로 최신 월별 업데이트 버전).
  다음 위치에서 최신 ssms.exe를 다운로드할 수 있습니다.
  
  - [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)항목
  - [직접 다운로드 링크](https://go.microsoft.com/fwlink/?linkid=616025)

- [Azure PowerShell 모듈](https://go.microsoft.com/?linkid=9811175) 이 설치되어 있어야 합니다.

  - 이 모듈은 **New-AzStorageAccount** 같은 명령을 제공합니다.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>1단계: Azure Storage 컨테이너용 PowerShell 코드

이 PowerShell은 2단계 코드 샘플의 1단계입니다.

이 스크립트는 이전 실행(있는 경우) 다음에 정리하는 명령으로 시작하며, 재실행이 가능합니다.

1. PowerShell 스크립트를 Notepad.exe와 같은 간단한 텍스트 편집기로 붙여 넣은 다음 확장명을 **.ps1** 으로 지정하여 스크립트를 파일로 저장합니다.
2. 관리자 권한으로 PowerShell ISE를 시작 합니다.
3. 프롬프트에서<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>을 입력한 다음 Enter를 누릅니다.
4. PowerShell ISE에서 **.ps1** 파일을 엽니다. 스크립트를 실행합니다.
5. 가장 먼저 Azure에 로그인할 수 있는 새 창이 열립니다.

   - 세션을 중단하지 않고 스크립트를 다시 실행하는 경우 **Add-AzureAccount** 명령에 주석을 입력하는 편리한 옵션을 사용할 수 있습니다.

![스크립트를 실행하려면 Azure 모듈이 설치된 PowerShell ISE가 준비되어 있어야 합니다.](./media/xevent-code-event-file/event-file-powershell-ise-b30.png)

### <a name="powershell-code"></a>PowerShell 코드

이 PowerShell 스크립트는 사용자가 Az 모듈을 이미 설치했다고 가정합니다. 자세한 내용은 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요.

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Connect-AzAccount (or  Connect-AzAccount), just one time.';
#Connect-AzAccount;   # Same as  Connect-AzAccount.

#-------------- 2 ------------------------

'
TODO: Edit the values assigned to these variables, especially the first few!
';

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME';
$resourceGroupName   = 'YOUR_RESOURCE-GROUP-NAME';

$policySasExpiryTime = '2018-08-28T23:44:56Z';
$policySasStartTime  = '2017-10-01';

$storageAccountLocation = 'YOUR_STORAGE_ACCOUNT_LOCATION';
$storageAccountName     = 'YOUR_STORAGE_ACCOUNT_NAME';
$containerName          = 'YOUR_CONTAINER_NAME';
$policySasToken         = ' ? ';

$policySasPermission = 'rwl';  # Leave this value alone, as 'rwl'.

#--------------- 3 -----------------------

# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script.

'Choose an existing subscription for the current PowerShell environment.';

Select-AzSubscription -Subscription $subscriptionName;

#-------------- 4 ------------------------

'
Clean up the old Azure Storage Account after any previous run,
before continuing this new run.';

if ($storageAccountName) {
    Remove-AzStorageAccount `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName;
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString();

'
Create a storage account.
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...';

New-AzStorageAccount `
    -Name              $storageAccountName `
    -Location          $storageAccountLocation `
    -ResourceGroupName $resourceGroupName `
    -SkuName           'Standard_LRS';

[System.DateTime]::Now.ToString();
[System.Media.SystemSounds]::Beep.Play();

'
Get the access key for your storage account.
';

$accessKey_ForStorageAccount = `
    (Get-AzStorageAccountKey `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName
        ).Value[0];

"`$accessKey_ForStorageAccount = $accessKey_ForStorageAccount";

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
';

#--------------- 6 -----------------------

# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
';

$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $accessKey_ForStorageAccount;

'Create a container within the storage account.
';

$containerObjectInStorageAccount = New-AzStorageContainer `
    -Name    $containerName `
    -Context $context;

'Create a security policy to be applied to the SAS token.
';

New-AzStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime;

'
Generate a SAS token for the container.
';
try {
    $sasTokenWithPolicy = New-AzStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
catch {
    $Error[0].Exception.ToString();
}

#-------------- 7 ------------------------

'Display the values that YOU must edit into the Transact-SQL script next!:
';

"storageAccountName: $storageAccountName";
"containerName:      $containerName";
"sasTokenWithPolicy: $sasTokenWithPolicy";

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
';

'
(Later, return here to delete your Azure Storage account. See the preceding  Remove-AzStorageAccount -Name $storageAccountName)';

'
Now shift to the Transact-SQL portion of the two-part code sample!';

# EOFile
```

스크립트가 종료될 때 PowerShell 스크립트가 인쇄하는 몇 가지 명명된 값을 기록해 둡니다. 다음 2단계에서 Transact-SQL 스크립트로 해당 값을 편집해야 합니다.

<!--
TODO:   Consider whether the preceding PowerShell code example deserves to be updated to the latest package (AzureRM.SQL?).
2020/June/06   Adding the !NOTE below about "ADLS Gen2 storage accounts".
Related to   https://github.com/MicrosoftDocs/azure-docs/issues/56520
-->

> [!NOTE]
> 위의 PowerShell 코드 예제에서 SQL 확장 이벤트는 ADLS Gen2 스토리지 계정과 호환되지 않습니다.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>2단계: Azure Storage 컨테이너를 사용하는 Transact-SQL 코드

- 이 코드 샘플의 1단계에서 Azure Storage 컨테이너를 만드는 PowerShell 스크립트를 실행했습니다.
- 다음으로, 2단계에서 다음 Transact-SQL 스크립트는 컨테이너를 사용해야 합니다.

이 스크립트는 이전 실행(있는 경우) 다음에 정리하는 명령으로 시작하며, 재실행이 가능합니다.

PowerShell 스크립트가 종료될 때 몇 가지 명명된 값을 인쇄했습니다. 이러한 값을 사용하려면 Transact-SQL 스크립트를 편집해야 합니다. Transact-SQL 스크립트에서 **TODO** 를 찾아 편집점을 찾습니다.

1. SQL Server Management Studio(ssms.exe)를 엽니다.
2. Azure SQL Database의 데이터베이스에 연결합니다.
3. 클릭하여 새 쿼리 창을 엽니다.
4. 쿼리 창에 다음 Transact-SQL 스크립트를 붙여 넣습니다.
5. 스크립트의 모든 **TODO** 를 찾고 적절히 편집합니다.
6. 저장한 다음 스크립트를 실행합니다.

> [!WARNING]
> 앞의 PowerShell 스크립트에서 생성된 SAS 키 값은 '?'(물음표)로 시작될 수 있습니다. 다음 T-SQL 스크립트에서 SAS 키를 사용하는 경우 *앞의 '?'를 제거해야 합니다*. 그렇지 않으면 보안에 의해 작업이 차단될 수 있습니다.

### <a name="transact-sql-code"></a>Transact-SQL 코드

```sql
---- TODO: First, run the earlier PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.

SET NOCOUNT ON;
GO

----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.

IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO

CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO

INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).

IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO

IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO

CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO

------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO

CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO

------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO

-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                -- TODO: The name of the .xel file needs to be an exact match to the files in the storage account Container (You can use Storage Account explorer from the portal to find out the exact file names or you can retrieve the name using the following DMV-query: select target_data from sys.dm_xe_database_session_targets. The 3rd xml-node, "File name", contains the name of the file currently written to.)
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO

-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzStorageAccount to delete your Azure Storage account!';
GO
```

실행 시 대상이 연결되지 않으면 이벤트 세션을 중지했다 다시 시작해야 합니다.

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```

## <a name="output"></a>출력

Transact-SQL 스크립트가 완료되면 **event_data_XML** 열 헤더 아래 셀을 클릭합니다. 하나의 **\<event>** 요소가 표시되고 하나의 UPDATE 문이 표시됩니다.

다음은 테스트 중 생성된 하나의 **\<event>** 요소입니다.

```xml
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```

앞에 나오는 Transact-SQL 스크립트는 다음 시스템 함수를 사용해서 event_file을 읽었습니다.

- [sys.fn_xe_file_target_read_file(Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)

확장된 이벤트에서 데이터를 보기 위한 고급 옵션에 대한 설명은 다음에서 사용할 수 있습니다.

- [확장된 이벤트의 대상 데이터에 대한 고급 보기](/sql/relational-databases/extended-events/advanced-viewing-of-target-data-from-extended-events-in-sql-server)

## <a name="converting-the-code-sample-to-run-on-sql-server"></a>SQL Server 실행을 위해 코드 샘플 변환

Microsoft SQL Server에서 위의 Transact-SQL 샘플을 실행하는 경우를 가정하겠습니다.

- 간단히 Azure Storage 컨테이너를 *C:\myeventdata.xel* 과 같은 간단한 파일로 바꾼다고 가정합니다. 이 파일은 SQL Server를 호스팅하는 컴퓨터의 로컬 하드 드라이브에 기록됩니다.
- **CREATE MASTER KEY** 및 **CREATE CREDENTIAL** 에는 Transact-SQL 종류의 문이 필요하지 않습니다.
- **CREATE EVENT SESSION** 문의 **ADD TARGET** 절에서 **filename=** 에 지정된 Http 값을 *C:\myfile.xel* 와 같은 전체 경로 문자열로 바꾸겠습니다.
  
  - Azure Storage 계정은 사용하지 않습니다.

## <a name="more-information"></a>자세한 정보

Azure Storage 서비스에서 계정 및 컨테이너에 대한 자세한 내용은 다음을 참조하세요.

- [.NET에서 Blob Storage를 사용하는 방법](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [컨테이너, BLOB, 메타데이터 이름 명명 및 참조](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)
- [루트 컨테이너 사용](/rest/api/storageservices/Working-with-the-Root-Container)
- [1단원: Azure 컨테이너에 저장된 액세스 정책 및 공유 액세스 서명 만들기](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage)
  - [2단원: 공유 액세스 서명을 사용하여 SQL Server 자격 증명 만들기](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#2---create-a-sql-server-credential-using-a-shared-access-signature)
- [Microsoft SQL Server의 확장 이벤트](/sql/relational-databases/extended-events/extended-events).
