---
title: 클라우드 데이터베이스의 분산 트랜잭션(미리 보기)
description: Azure SQL Database 및 Azure SQL Managed Instance의 탄력적 데이터베이스 트랜잭션 개요입니다.
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1, ignite-fall-2021
ms.topic: conceptual
author: scoriani
ms.author: scoriani
ms.reviewer: mathoma
ms.date: 11/02/2021
ms.openlocfilehash: 3c44d9838f9f3983dfc067a091b2507544a7db38
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005996"
---
# <a name="distributed-transactions-across-cloud-databases-preview"></a>클라우드 데이터베이스의 분산 트랜잭션(미리 보기)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!IMPORTANT]
> Azure SQL Managed Instance의 분산 트랜잭션이 이제 일반 공급 됩니다. Azure SQL Database에 대 한 Elastic Database 트랜잭션은 미리 보기 상태입니다.

Azure SQL Database (미리 보기) 및 Azure SQL Managed Instance에 대 한 탄력적 데이터베이스 트랜잭션을 사용 하면 여러 데이터베이스에 걸쳐 트랜잭션을 실행할 수 있습니다. 탄력적 데이터베이스 트랜잭션은 ADO.NET을 사용하여 .NET 애플리케이션에서 사용할 수 있고 [System.Transaction 클래스](/dotnet/api/system.transactions)를 사용하여 친숙한 프로그래밍 환경과 통합될 수 있습니다. 라이브러리를 가져오려면 [.NET Framework 4.6.1(웹 설치 관리자)](https://www.microsoft.com/download/details.aspx?id=49981)을 참조하세요.
또한 관리 되는 인스턴스 분산 트랜잭션은 [SQL transact-sql](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql)에서 사용할 수 있습니다.

온-프레미스에서 이러한 시나리오를 사용하려면 일반적으로 MSDTC(Microsoft Distributed Transaction Coordinator)를 실행해야 합니다. MSDTC는 Azure에서 Platform as a Service 응용 프로그램에 사용할 수 없으므로 이제 분산 트랜잭션을 조정 하는 기능이 SQL Database 또는 SQL Managed Instance에 직접 통합 되었습니다. 애플리케이션은 분산 트랜잭션을 시작하도록 모든 데이터베이스에 연결할 수 있으며, 다음 그림에 표시된 것처럼 데이터베이스 또는 서버 중 하나는 분산 트랜잭션을 투명하게 조정합니다.

이 문서에서 “분산 트랜잭션” 및 “탄력적 데이터베이스 트랜잭션”은 동의어로 간주되며 서로 바꾸어 사용할 수 있습니다.

  ![Elastic Database 트랜잭션을 사용한 Azure SQL Database와 분산 트랜잭션 ][1]

## <a name="common-scenarios"></a>일반적인 시나리오

탄력적 데이터베이스 트랜잭션은 여러 개의 다른 데이터베이스에 저장된 데이터에 대한 애플리케이션의 원자성 변경을 가능하게 합니다. SQL Database와 SQL Managed Instance 모두 c # 및 .net의 클라이언트 쪽 개발 환경을 지원 합니다. [SQL transact-sql](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) 을 사용 하 여 서버 쪽 환경 (저장 프로시저 또는 서버 쪽 스크립트에 작성 된 코드)은 SQL Managed Instance에만 사용할 수 있습니다.

> [!IMPORTANT]
> Azure SQL Database와 Azure SQL Managed Instance 간에 탄력적 데이터베이스 트랜잭션을 실행 하는 것은 지원 되지 않습니다. 탄력적 데이터베이스 트랜잭션은 SQL Database의 데이터베이스 집합 또는 관리 되는 인스턴스의 집합 데이터베이스에 걸쳐 있을 수 있습니다. 

탄력적 데이터베이스 트랜잭션은 다음 시나리오를 대상으로 합니다.

* Azure의 다중 데이터베이스 응용 프로그램:이 시나리오에서는 여러 종류의 데이터가 서로 다른 데이터베이스에 상주 하도록 SQL Database 또는 SQL Managed Instance에서 여러 데이터베이스에 걸쳐 데이터가 수직 분할 됩니다. 일부 작업은 둘 이상의 데이터베이스에 보관되는 데이터에 대한 변경을 필요로 합니다. 애플리케이션은 여러 데이터베이스에 걸쳐 변경을 조정하고 원자성을 보장하기 위해 탄력적 데이터베이스 트랜잭션을 사용합니다.
* Azure의 분할 된 데이터베이스 응용 프로그램:이 시나리오에서 데이터 계층은 [Elastic Database 클라이언트 라이브러리](elastic-database-client-library.md) 또는 자체 분할를 사용 하 여 SQL Database 또는 SQL Managed Instance에서 여러 데이터베이스에 걸쳐 데이터를 수평으로 분할 합니다. 한 가지 주요한 사용 사례는 여러 테넌트에 변경이 미칠 때 분할된 다중 테넌트 애플리케이션에 원자성 변경을 수행해야 하는 경우입니다. 서로 다른 데이터베이스에 상주하는 하나의 테넌트에서 다른 테넌트로 전송하는 인스턴스를 생각해 볼 수 있습니다. 두 번째 사례는 대규모 테넌트에 필요한 용량을 공급하기 위한 세분화된 분할이며, 일반적으로 원자성 작업이 동일한 테넌트의 여러 데이터베이스로 확장되어야 하는 필요성을 수반합니다. 세 번째 사례는 여러 데이터베이스에 걸쳐 복제되는 참조 데이터에 대한 원자성 업데이트입니다. 이러한 줄을 따라 원자 단위로 트랜잭션 된 작업은 이제 여러 데이터베이스에서 조정 될 수 있습니다.
  탄력적 데이터베이스 트랜잭션은 여러 데이터베이스에 걸쳐 트랜잭션 원자성을 보장하기 위해 2단계 커밋을 사용합니다. 이것은 단일 트랜잭션 내에 한 번에 100개 미만의 데이터베이스가 관여되는 트랜잭션에 적합합니다. 이러한 제한은 적용되지 않지만 이러한 제한을 초과할 때 탄력적 데이터베이스 트랜잭션의 성능 및 성공률을 예상해야 합니다.

## <a name="installation-and-migration"></a>설치 및 마이그레이션

탄력적 데이터베이스 트랜잭션의 기능은 .NET 라이브러리 System.Data.dll 및 System.Transactions.dll 업데이트를 통해 제공됩니다. DLL은 원자성을 보장하기 위해 필요한 경우 2단계 커밋이 사용되도록 합니다. 탄력적 데이터베이스 트랜잭션을 사용하여 애플리케이션 개발을 시작하려면 [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) 이상 버전을 설치합니다. 이전 버전의 .NET framework를 실행하면, 트랜잭션이 분산 트랜잭션으로 승격하지 못하고 예외가 발생합니다.

설치 후에는 SQL Database에 대 한 연결을 사용 하 여 시스템 트랜잭션에서 분산 트랜잭션 api를 사용 하 고 Managed Instance를 SQL 수 있습니다. 기존 MSDTC 애플리케이션에서 이러한 API를 사용하고 있는 경우에는 4.6.1 Framework를 설치한 후에 .NET 4.6에 대한 기존 애플리케이션을 다시 빌드합니다. 프로젝트가 .net 4.6를 대상으로 하는 경우 새 프레임 워크 버전에서 업데이트 된 dll을 자동으로 사용 하 고 SQL Database 또는 SQL Managed Instance에 대 한 연결과 함께 분산 트랜잭션 API 호출을 자동으로 사용 합니다.

탄력적 데이터베이스 트랜잭션은 MSDTC를 설치할 필요가 없습니다. 대신 탄력적 데이터베이스 트랜잭션은 서비스 내에서 서비스에 의해 직접적으로 관리됩니다. 이는 SQL Database 또는 SQL Managed Instance 분산 트랜잭션을 사용할 필요가 없기 때문에 클라우드 시나리오를 크게 간소화 합니다. 섹션 4는 탄력적 데이터베이스 트랜잭션 배포 방법 및 필요한 .NET framework를 Azure에 대한 클라우드 애플리케이션과 함께 보다 자세히 설명합니다.

## <a name="net-installation-for-azure-cloud-services"></a>Azure Cloud Services용 .NET 설치

Azure에서는 .NET 애플리케이션을 호스트하는 여러 제품을 제공합니다. [Azure App Service, Cloud Services 및 Virtual Machines 비교](/azure/architecture/guide/technology-choices/compute-decision-tree)에서 다양한 제품을 비교할 수 있습니다. 제품의 게스트 OS가 탄력적인 트랜잭션에 필요한 .NET 4.6.1보다 작은 경우 게스트 OS를 4.6.1로 업그레이드해야 합니다.

Azure App Service 경우 게스트 OS로의 업그레이드는 현재 지원되지 않습니다. Azure Virtual Machines의 경우 VM에 로그인하여 최신 .NET Framework용 설치 관리자를 실행하기만 하면 됩니다. Azure Cloud Services의 경우 최신 .NET 버전의 설치를 배포의 시작 작업에 포함해야 합니다. 개념 및 단계는 [클라우드 서비스 역할에 .NET 설치](../../cloud-services/cloud-services-dotnet-install-dotnet.md)에 문서화되어 있습니다.  

.NET 4.6.1용 설치 관리자는 Azure 클라우드 서비스에서 프로세스를 부트스트랩하는 과정에서 .NET 4.6용 설치 관리자보다 더 많은 임시 스토리지 공간을 필요로 할 수 있습니다. 성공적인 설치를 위해 다음 예에서처럼 LocalResources 섹션의 ServiceDefinition.csdef 파일과 시작 작업의 환경 설정에서 Azure 클라우드 서비스의 임시 스토리지 공간을 늘려야 합니다.

```xml
<LocalResources>
...
    <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
</LocalResources>
<Startup>
    <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
    ...
            <Variable name="TEMP">
                <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
            </Variable>
            <Variable name="TMP">
                <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
            </Variable>
        </Environment>
    </Task>
</Startup>
```

## <a name="net-development-experience"></a>.NET 개발 환경

### <a name="multi-database-applications"></a>다중 데이터베이스 애플리케이션

다음 샘플 코드는 .NET System.Transactions와 친숙한 프로그래밍 환경을 사용합니다. TransactionScope 클래스는 .NET에서 앰비언트 트랜잭션을 설정합니다. ("앰비언트 트랜잭션"은 현재 스레드에 상주 하는 트랜잭션입니다.) TransactionScope 내에서 열려 있는 모든 연결은 트랜잭션에 참여 합니다. 다른 데이터베이스가 참여하면, 트랜잭션은 분산 트랜잭션으로 자동 승격됩니다. 트랜잭션의 결과는 커밋을 나타내기 위해 완료해야 하는 범위를 설정하여 제어됩니다.

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection(connStrDb1))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }
    using (var conn2 = new SqlConnection(connStrDb2))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into T2 values(2)");
        cmd2.ExecuteNonQuery();
    }
    scope.Complete();
}
```

### <a name="sharded-database-applications"></a>분할된 데이터베이스 애플리케이션

또한 SQL Database 및 Managed Instance SQL에 대 한 탄력적 데이터베이스 트랜잭션은 탄력적 데이터베이스 클라이언트 라이브러리의 openconnectionforkey 메서드를 사용 하 여 확장 된 데이터 계층에 대 한 연결을 여는 분산 트랜잭션 조정을 지원 합니다. 여러 개의 다른 분할 키 값에 걸쳐 변경 사항에 대해 트랜잭션 일관성을 보장해야 하는 경우를 고려해 보겠습니다. 다른 분할 키 값을 호스팅하는 분할에 대한 연결은 OpenConnectionForKey를 사용하여 중개됩니다. 일반적인 상황에서 트랜잭션 보장이 분산 트랜잭션을 요구하도록 하기 위해서 다른 분할에 연결될 수 있습니다.
다음 코드 샘플에서 이 접근 방법을 보여 줍니다. shardmap이라는 매개 변수가 탄력적 데이터베이스 클라이언트 라이브러리의 분할된 데이터베이스 맵을 나타내기 위해 사용된다고 가정합니다.

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
    {
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }
    using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
    {
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into T1 values(2)");
        cmd2.ExecuteNonQuery();
    }
    scope.Complete();
}
```

## <a name="transact-sql-development-experience"></a>Transact-SQL 개발 환경

Transact-SQL을 사용하는 서버 측 분산 트랜잭션은 Azure SQL Managed Instance에서만 사용할 수 있습니다. 분산 트랜잭션은 동일한 [서버 신뢰 그룹](../managed-instance/server-trust-group-overview.md)에 속한 Managed Instance 간에만 실행할 수 있습니다. 이 시나리오에서 Managed Instance는 [연결된 서버](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine#TsqlProcedure)를 사용하여 서로를 참조해야 합니다.

다음 샘플 Transact-SQL 코드는 [BEGIN DISTRIBUTED TRANSACTION](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql)을 사용하여 분산 트랜잭션을 시작합니다.

```sql
    -- Configure the Linked Server
    -- Add one Azure SQL Managed Instance as Linked Server
    EXEC sp_addlinkedserver
        @server='RemoteServer', -- Linked server name
        @srvproduct='',
        @provider='sqlncli', -- SQL Server Native Client
        @datasrc='managed-instance-server.46e7afd5bc81.database.windows.net' -- SQL Managed Instance endpoint

    -- Add credentials and options to this Linked Server
    EXEC sp_addlinkedsrvlogin
        @rmtsrvname = 'RemoteServer', -- Linked server name
        @useself = 'false',
        @rmtuser = '<login_name>',         -- login
        @rmtpassword = '<secure_password>' -- password

    USE AdventureWorks2012;
    GO
    SET XACT_ABORT ON;
    GO
    BEGIN DISTRIBUTED TRANSACTION;
    -- Delete candidate from local instance.
    DELETE AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    -- Delete candidate from remote instance.
    DELETE RemoteServer.AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    COMMIT TRANSACTION;
    GO
```

## <a name="combining-net-and-transact-sql-development-experience"></a>.NET 및 Transact-SQL 개발 환경 결합

System.Transaction 클래스를 사용하는 .NET 애플리케이션은 TransactionScope 클래스를 Transact-SQL 문 BEGIN DISTRIBUTED TRANSACTION과 결합할 수 있습니다. TransactionScope 내에서 BEGIN DISTRIBUTED TRANSACTION을 실행 하는 내부 트랜잭션은 명시적으로 분산 트랜잭션으로 승격 됩니다. 또한 TransactionScope 내에서 두 번째 SqlConnecton을 열면 암시적으로 분산 트랜잭션으로 승격됩니다. 분산 트랜잭션이 시작되면 .NET 또는 Transact-SQL에서 들어오는 모든 후속 트랜잭션 요청이 상위 분산 트랜잭션에 포함됩니다. 따라서 BEGIN 문으로 시작된 모든 중첩된 트랜잭션 범위는 동일한 트랜잭션으로 종료되고 COMMIT/ROLLBACK 문은 전체 결과에 다음과 같은 영향을 미칩니다.
 * COMMIT 문은 BEGIN 문으로 시작된 트랜잭션 범위에 영향을 주지 않습니다. 즉, TransactionScope 개체에서 Complete() 메서드가 호출되기 전에는 결과가 커밋되지 않습니다. TransactionScope 개체가 완료되기 전에 삭제되면 범위 내에서 수행된 모든 변경 내용이 롤백됩니다.
 * ROLLBACK 문으로 인해 전체 TransactionScope가 롤백됩니다. TransactionScope 내에 새 트랜잭션을 등록하려는 시도뿐 아니라 TransactionScope 개체에서 Complete()를 호출하려고 시도도 나중에 실패합니다.

트랜잭션이 Transact-SQL을 통해 분산 트랜잭션으로 명시적으로 승격되는 예는 다음과 같습니다.

```csharp
using (TransactionScope s = new TransactionScope())
{
    using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
    {
        conn.Open();
    
        // Transaction is here promoted to distributed by BEGIN statement
        //
        Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
        // ...
    }
 
    using (SqlConnection conn2 = new SqlConnection(DB1_ConnectionString)
    {
        conn2.Open();
        // ...
    }
    
    s.Complete();
}
```

다음 예시에서는 TransactionScope 내에서 두 번째 SqlConnecton이 시작된 후 암시적으로 분산 트랜잭션으로 승격되는 트랜잭션을 보여 줍니다.

```csharp
using (TransactionScope s = new TransactionScope())
{
    using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
    {
        conn.Open();
        // ...
    }
    
    using (SqlConnection conn = new SqlConnection(DB1_ConnectionString)
    {
        // Because this is second SqlConnection within TransactionScope transaction is here implicitly promoted distributed.
        //
        conn.Open(); 
        Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
        Helper.ExecuteNonQueryOnOpenConnection(conn, lsQuery);
        // ...
    }
    
    s.Complete();
}
```

## <a name="transactions-for-sql-database"></a>SQL Database에 대 한 트랜잭션

> [!IMPORTANT]
> Azure SQL Database에 대 한 분산 트랜잭션은 미리 보기 상태입니다. 

탄력적 데이터베이스 트랜잭션은 Azure SQL Database의 여러 서버에 걸쳐 지원됩니다. 트랜잭션이 서버 경계를 교차하는 경우 참여하는 서버는 먼저 상호 통신 관계가 설정되어야 합니다. 통신 관계가 설정된 후에는 두 서버 중 하나의 모든 데이터베이스가 다른 서버 데이터베이스와의 탄력적인 트랜잭션에 참여할 수 있습니다. 서버 3개 이상에 걸쳐 트랜잭션이 진행될 때는 모든 서버 쌍에 대해 통신 관계가 설정되어 있어야 합니다.

다음 PowerShell cmdlet을 사용하여 탄력적인 데이터베이스 트랜잭션에 대한 서버 간 통신 관계를 관리할 수 있습니다.

* **New-AzSqlServerCommunicationLink**: 이 cmdlet을 사용하여 Azure SQL Database의 두 서버 간에 새 통신 관계를 만듭니다. 해당 관계는 대칭적이기 때문에 두 서버 모두 상대 서버와의 트랜잭션을 시작할 수 있습니다.
* **Get-AzSqlServerCommunicationLink**: 기존 통신 관계와 해당 속성을 검색하려면 이 cmdlet을 사용합니다.
* **Remove-AzSqlServerCommunicationLink**: 기존 통신 관계와 해당 속성을 제거하려면 이 cmdlet을 사용합니다.

## <a name="transactions-for-sql-managed-instance"></a>SQL Managed Instance에 대 한 트랜잭션

> [!IMPORTANT]
> Azure SQL Managed Instance의 분산 트랜잭션이 이제 일반 공급 됩니다.

분산 트랜잭션은 여러 인스턴스 내의 데이터베이스에서 지원 됩니다. 트랜잭션이 관리되는 인스턴스 경계를 넘으면 참여하는 인스턴스가 상호 보안 및 통신 관계에 있어야 합니다. 이 작업은 Azure Portal 또는 Azure PowerShell 또는 Azure CLI 사용하여 수행할 수 있는 [서버 신뢰 그룹](../managed-instance/server-trust-group-overview.md)를 만들어 수행합니다. 인스턴스가 동일한 가상 네트워크에 없는 경우 [가상 네트워크 피어링을](../../virtual-network/virtual-network-peering-overview.md) 구성해야 하며, 네트워크 보안 그룹 인바운드 및 아웃바운드 규칙은 참여하는 모든 가상 네트워크에서 포트 5024 및 11000-12000을 허용해야 합니다.

  ![Azure Portal의 서버 신뢰 그룹][3]

다음 다이어그램에서는 .NET 또는 Transact-SQL 분산 트랜잭션을 실행할 수 있는 관리되는 인스턴스가 있는 서버 신뢰 그룹을 보여 제공합니다. 

  ![탄력적 트랜잭션을 사용한 Azure SQL Managed Instance와 분산 트랜잭션][2]

## <a name="monitoring-transaction-status"></a>트랜잭션 상태 모니터링

DMV(동적 관리 뷰)를 사용하여 진행 중인 탄력적 데이터베이스 트랜잭션의 상태와 진행률을 모니터링합니다. 트랜잭션과 관련된 모든 DMV는 SQL Database 및 SQL Managed Instance 분산 트랜잭션과 관련이 있습니다. 해당 DMV 목록은 다음에서 찾을 수 있습니다. [트랜잭션 관련 동적 관리 뷰 및 함수(Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql)

다음 DMV는 특히 유용합니다.

* **sys.dm\_tran\_active\_transactions**: 현재 활성 트랜잭션과 그 상태를 나열합니다. UOW(작업 단위) 열은 동일한 분산 트랜잭션에 속하는 다른 자식 트랜잭션을 식별할 수 있습니다. 동일한 분산 트랜잭션 내의 모든 트랜잭션은 동일한 UOW 값을 갖습니다. 자세한 내용은 [DMV 설명서](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql)를 참조하세요.
* **sys.dm\_tran\_database\_transactions**: 로그에 트랜잭션 배치 같은 트랜잭션에 대한 추가적인 정보를 제공합니다. 자세한 내용은 [DMV 설명서](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql)를 참조하세요.
* **sys.dm\_tran\_locks**: 진행 중인 트랜잭션에 의해 현재 유지되는 잠금에 대한 정보를 제공합니다. 자세한 내용은 [DMV 설명서](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql)를 참조하세요.

## <a name="limitations"></a>제한 사항

현재 SQL Database 탄력적 데이터베이스 트랜잭션에 적용되는 제한은 다음과 *같습니다.*

* SQL Database의 데이터베이스 간 트랜잭션만 지원됩니다. 다른 [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) 리소스 공급자 및 SQL Database 이외의 데이터베이스는 탄력적 데이터베이스 트랜잭션에 참여할 수 없습니다. 즉, 탄력적 데이터베이스 트랜잭션은 온-프레미스 SQL Server와 Azure SQL Database로 확장될 수 없습니다. 온-프레미스 분산 트랜잭션을 위해서는 MSDTC를 계속 사용하세요.
* .NET 애플리케이션의 클라이언트 조정 트랜잭션만 지원됩니다. BEGIN DISTRIBUTED TRANSACTION 같은 T-SQL에 대한 서버 쪽 지원이 계획되어 있지만 아직 제공되지 않습니다.
* WCF 서비스에서 트랜잭션은 지원되지 않습니다. 예를 들어 트랜잭션을 실행하는 WCF 서비스 메서드가 있습니다. 트랜잭션 범위 내로 호출을 묶으면 [System.ServiceModel.ProtocolException](/dotnet/api/system.servicemodel.protocolexception)으로 실패합니다.

현재 SQL Managed Instance 분산 *트랜잭션에* 적용되는 제한은 다음과 같습니다.

* 관리되는 인스턴스의 데이터베이스 간 트랜잭션만 지원됩니다. 다른 [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) 리소스 공급자 및 Azure SQL Managed Instance 외부의 데이터베이스는 분산 트랜잭션에 참여할 수 없습니다. 즉, 분산 트랜잭션은 온-프레미스 SQL Server 및 Azure SQL Managed Instance 확장할 수 없습니다. 온-프레미스 분산 트랜잭션을 위해서는 MSDTC를 계속 사용하세요.
* WCF 서비스에서 트랜잭션은 지원되지 않습니다. 예를 들어 트랜잭션을 실행하는 WCF 서비스 메서드가 있습니다. 트랜잭션 범위 내로 호출을 묶으면 [System.ServiceModel.ProtocolException](/dotnet/api/system.servicemodel.protocolexception)으로 실패합니다.
* Azure SQL Managed Instance가 분산 트랜잭션에 참여하려면 [서버 신뢰 그룹](../managed-instance/server-trust-group-overview.md)에 속해야 합니다.
* [서버 신뢰 그룹](../managed-instance/server-trust-group-overview.md)의 제한 사항은 분산 트랜잭션에 영향을 미칩니다.
* 분산 트랜잭션에 참여하는 Managed Instance는 프라이빗 엔드포인트(배포된 가상 네트워크의 개인 IP 주소 사용)를 통해 연결되어야 하며 프라이빗 FQDN을 사용하여 상호 참조되어야 합니다. 클라이언트 애플리케이션은 프라이빗 엔드포인트에서 분산 트랜잭션을 사용할 수 있습니다. 또한 Transact-SQL이 프라이빗 엔드포인트를 참조하는 연결된 서버를 활용하는 경우, 클라이언트 애플리케이션은 퍼블릭 엔드포인트에서도 분산 트랜잭션을 사용할 수 있습니다. 이 제한 사항은 다음 다이어그램에 설명되어 있습니다.

![프라이빗 엔드포인트 연결 제한][4]

## <a name="next-steps"></a>다음 단계

* 질문이 있는 경우 [SQL Database에 대한 Microsoft Q&A 질문 페이지](/answers/topics/azure-sql-database.html)에서 문의하세요.
* 기능 요청의 경우 SQL Database [피드백 포럼](https://feedback.azure.com/forums/217321-sql-database/) 또는 [SQL Managed Instance 포럼](https://feedback.azure.com/forums/915676-sql-managed-instance)에 추가합니다.



<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
[2]: ./media/elastic-transactions-overview/sql-mi-distributed-transactions.png
[3]: ./media/elastic-transactions-overview/server-trust-groups-azure-portal.png
[4]: ./media/elastic-transactions-overview/managed-instance-distributed-transactions-private-endpoint-limitations.png
