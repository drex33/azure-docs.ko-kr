---
title: SQL 인사이트 사용
description: Azure Monitor에서 SQL 인사이트 사용
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: 9a5d14c3363f5d4b4d25e0592b184b6e706fef6b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566669"
---
# <a name="enable-sql-insights-preview"></a>SQL 인사이트 사용(미리 보기)
이 문서에서는 [SQL 인사이트](sql-insights-overview.md)를 사용하여 SQL 배포를 모니터링하는 방법을 설명합니다. SQL 배포에 연결하고 DMV(동적 관리 뷰)를 사용하여 모니터링 데이터를 수집하는 Azure 가상 머신에서 모니터링이 수행됩니다. 모니터링 프로필을 사용하여 수집되는 데이터 세트와 수집 빈도를 제어할 수 있습니다.

> [!NOTE]
> 리소스 관리자 템플릿을 사용하여 모니터링 프로필 및 가상 머신을 만들어 SQL 인사이트를 사용하도록 설정하려면 [SQL 인사이트에 대한 Resource Manager 템플릿 샘플](resource-manager-sql-insights.md)을 참조하세요.

SQL 인사이트를 사용하도록 설정하는 방법을 알아보려면 이 데이터 노출 에피소드를 참조할 수도 있습니다.
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/How-to-Set-up-Azure-Monitor-for-SQL-Insights/player?format=ny]

## <a name="create-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기
SQL 인사이트는 하나 이상의 [Log Analytics 작업 영역](../logs/data-platform-logs.md#log-analytics-and-workspaces)에 데이터를 저장합니다.  SQL 인사이트를 사용하도록 설정하려면 [작업 영역을 만들거나](../logs/quick-create-workspace.md) 기존 작업 영역을 선택해야 합니다. 여러 모니터링 프로필에 단일 작업 영역을 사용할 수 있지만 작업 영역과 프로필은 동일한 Azure 지역에 있어야 합니다. SQL 인사이트의 기능을 활성화하고 액세스하려면 작업 영역에 [Log Analytics 참가자 역할](../logs/manage-access.md)이 있어야 합니다. 

## <a name="create-monitoring-user"></a>모니터링 사용자 만들기 
모니터링할 SQL 배포에 사용자가 필요합니다. 여러 유형의 SQL 배포에 대해서는 아래 절차를 따르세요.

아래 지침에서는 모니터링할 수 있는 SQL 유형별 프로세스를 다룹니다.  한 번에 여러 SQL 리소스에 대한 스크립트를 사용하여 이 작업을 수행하려면 다음 [추가 정보 파일](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/SQL%20Insights%20Onboarding%20Scripts/Permissions_LoginUser_Account_Creation-README.txt) 및 [예제 스크립트](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/SQL%20Insights%20Onboarding%20Scripts/Permissions_LoginUser_Account_Creation.ps1)를 참조하세요.


### <a name="azure-sql-database"></a>Azure SQL Database

> [!NOTE]
> SQL 인사이트는 다음과 같은 Azure SQL Database 시나리오를 지원하지 않습니다.
> - **탄력적 풀**: 탄력적 풀에 대한 메트릭을 수집할 수 없습니다. 탄력적 풀 내의 데이터베이스에 대해서는 메트릭을 수집할 수 없습니다.
> - **낮은 서비스 계층**: 기본, S0, S1, S2 [서비스 계층](../../azure-sql/database/resource-limits-dtu-single-databases.md)의 데이터베이스에 대한 메트릭을 수집할 수 없습니다.
> 
> SQL 인사이트는 다음과 같은 Azure SQL Database 시나리오를 제한적으로 지원합니다.
> - **서버리스 계층**: [서버리스 컴퓨팅 계층](../../azure-sql/database/serverless-tier-overview.md)을 사용하여 데이터베이스에 대한 메트릭을 수집할 수 있습니다. 그러나 메트릭을 수집하는 프로세스는 자동 일시 중지 지연 타이머를 다시 설정하여 데이터베이스가 자동 일시 중지 상태를 시작하지 않도록 합니다.

Azure Portal에서 [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) 또는 [쿼리 편집기(미리 보기)](../../azure-sql/database/connect-query-portal.md)를 사용하여 Azure SQL Database를 엽니다.

다음 스크립트를 실행하여 필요한 권한을 보유한 사용자를 만듭니다. *user* 를 사용자 이름으로 바꾸고 *mystrongpassword* 를 암호로 바꿉니다.

```sql
CREATE USER [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW DATABASE STATE TO [user]; 
GO 
```

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-script.png" alt-text="telegraf 사용자 스크립트를 만듭니다." lightbox="media/sql-insights-enable/telegraf-user-database-script.png":::

사용자가 만들어졌는지 확인합니다.

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-verify.png" alt-text="telegraf 사용자 스크립트를 확인합니다." lightbox="media/sql-insights-enable/telegraf-user-database-verify.png":::

```sql
select name as username,
       create_date,
       modify_date,
       type_desc as type,
       authentication_type_desc as authentication_type
from sys.database_principals
where type not in ('A', 'G', 'R', 'X')
       and sid is not null
order by username
```

### <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance
Azure SQL Managed Instance에 로그인하고, [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) 또는 유사한 도구를 사용하여 다음 스크립트를 실행하고 필요한 권한을 보유한 모니터링 사용자를 만듭니다. *user* 를 사용자 이름으로 바꾸고 *mystrongpassword* 를 암호로 바꿉니다.

 
```sql
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO 
```

### <a name="sql-server"></a>SQL Server
SQL Server를 실행하는 Azure 가상 머신에 로그인하고, [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) 또는 유사한 도구를 사용하여 다음 스크립트를 실행하고 필요한 권한을 보유한 모니터링 사용자를 만듭니다. *user* 를 사용자 이름으로 바꾸고 *mystrongpassword* 를 암호로 바꿉니다.

 
```sql
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO
```

사용자가 만들어졌는지 확인합니다.

```sql
select name as username,
       create_date,
       modify_date,
       type_desc as type
from sys.server_principals
where type not in ('A', 'G', 'R', 'X')
       and sid is not null
order by username
```

## <a name="create-azure-virtual-machine"></a>Azure Virtual Machine 만들기 
데이터를 수집하여 SQL을 모니터링하는 데 사용할 Azure 가상 머신을 하나 이상 만들어야 합니다.  

> [!NOTE]
>  [모니터링 프로필](#create-sql-monitoring-profile)은 모니터링하려는 다양한 유형의 SQL에서 수집할 데이터를 지정합니다. 각 모니터링 가상 머신에는 하나의 모니터링 프로필만 연결할 수 있습니다. 여러 모니터링 프로필이 필요한 경우 각각에 대한 가상 머신을 만들어야 합니다.

### <a name="azure-virtual-machine-requirements"></a>Azure 가상 머신 요구 사항
Azure 가상 머신에는 다음과 같은 요구 사항이 있습니다.

- 운영 체제: Ubuntu 18.04 
- 권장되는 Azure 가상 머신 크기: Standard_B2s(CPU 2개, 4GiB 메모리) 
- 지원되는 지역: [Azure Monitor 에이전트에서 지원하는 모든 지역](../agents/azure-monitor-agent-overview.md#supported-regions)

> [!NOTE]
> Standard_B2s(CPU 2개, 4GiB 메모리) 가상 머신 크기는 최대 100개의 연결 문자열을 지원합니다. 단일 가상 머신에 100개가 넘는 연결을 할당해서는 안 됩니다.

SQL 리소스의 네트워크 설정에 따라 가상 머신을 SQL 리소스와 동일한 가상 네트워크에 배치하여 네트워크 연결을 통해 모니터링 데이터를 수집할 수 있도록 설정해야 할 수도 있습니다.  

## <a name="configure-network-settings"></a>네트워크 설정 구성
SQL의 각 유형은 모니터링 가상 머신이 SQL에 안전하게 액세스하는 방법을 제공합니다.  다음 섹션에서는 SQL의 유형에 따른 옵션을 다룹니다.

### <a name="azure-sql-database"></a>Azure SQL Database

SQL 인사이트는 가상 네트워크뿐 아니라 퍼블릭 엔드포인트를 통해 Azure SQL Database에 액세스하도록 지원합니다.

퍼블릭 엔드포인트를 통해 액세스하려면 **방화벽 설정** 페이지와 [IP 방화벽 설정](../../azure-sql/database/network-access-controls-overview.md#ip-firewall-rules) 섹션에 규칙을 추가합니다.  가상 네트워크에서 액세스를 지정하려면 [가상 네트워크 방화벽 규칙](../../azure-sql/database/network-access-controls-overview.md#virtual-network-firewall-rules)과 [Azure Monitor 에이전트에 필요한 서비스 태그](../agents/azure-monitor-agent-overview.md#networking)를 설정합니다.  [이 문서](../../azure-sql/database/network-access-controls-overview.md#ip-vs-virtual-network-firewall-rules)에서는 두 가지 유형의 방화벽 규칙 간의 차이점을 설명합니다.

:::image type="content" source="media/sql-insights-enable/set-server-firewall.png" alt-text="서버 방화벽 설정" lightbox="media/sql-insights-enable/set-server-firewall.png":::

:::image type="content" source="media/sql-insights-enable/firewall-settings.png" alt-text="방화벽 설정" lightbox="media/sql-insights-enable/firewall-settings.png":::


### <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

모니터링 가상 머신이 SQL MI 리소스와 동일한 VNet에 있는 경우 [동일한 VNet 내에서 연결](../../azure-sql/managed-instance/connect-application-instance.md#connect-inside-the-same-vnet)을 참조하세요. 모니터링 가상 머신이 SQL MI 리소스와 다른 VNet에 있는 경우 [다른 VNet 내에서 연결](../../azure-sql/managed-instance/connect-application-instance.md#connect-inside-a-different-vnet)을 참조하세요.


### <a name="sql-server"></a>SQL Server 
모니터링 가상 머신이 SQL 가상 머신 리소스와 동일한 VNet에 있는 경우 [가상 네트워크 내에서 SQL Server에 연결](../../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md#connect-to-sql-server-within-a-virtual-network)을 참조하세요. 모니터링 가상 머신이 SQL 가상 머신 리소스와 다른 VNet에 있는 경우 [인터넷을 통해 SQL Server에 연결](../../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md#connect-to-sql-server-over-the-internet)을 참조하세요.

## <a name="store-monitoring-password-in-key-vault"></a>Key Vault에 모니터링 암호 저장
SQL 사용자 연결 암호는 모니터링 프로필 연결 문자열에 직접 입력하는 대신 Key Vault에 저장해야 합니다.

SQL 모니터링을 위한 프로필을 설정하는 경우 사용하려는 Key Vault 리소스에 대해 다음 권한 중 하나가 필요합니다.

- Microsoft.Authorization/roleAssignments/write 
- 사용자 액세스 관리자 또는 소유자 등의 Microsoft.Authorization/roleAssignments/delete 권한 

지정한 Key Vault를 사용하는 SQL Monitoring 프로필을 만드는 과정에서 새 액세스 정책이 자동으로 만들어집니다. Key Vault 네트워킹 설정에 ‘모든 네트워크에서 액세스 허용’을 사용합니다.


## <a name="create-sql-monitoring-profile"></a>SQL Monitoring 프로필 만들기
Azure Portal의 **Azure Monitor** 메뉴에 있는 **인사이트** 섹션에서 **SQL(미리 보기)** 을 선택하여 SQL 인사이트를 엽니다. **새 프로필 만들기** 를 클릭합니다. 

:::image type="content" source="media/sql-insights-enable/create-new-profile.png" alt-text="새 프로필을 만듭니다." lightbox="media/sql-insights-enable/create-new-profile.png":::

이 프로필은 SQL 시스템에서 수집하려는 정보를 저장합니다.  다음에 대한 특정 설정이 포함됩니다. 

- Azure SQL Database 
- Azure SQL Managed Instance
- 가상 머신에서 SQL Server 실행  

예를 들면 데이터 수집 빈도, 수집할 데이터 및 데이터를 보낼 작업 영역에 대해 다른 설정을 사용하여 *SQL Production* 이라는 이름의 프로필과 *SQL Staging* 이라는 이름의 다른 프로필을 만들 수 있습니다. 

프로필은 선택한 구독 및 리소스 그룹에 [데이터 수집 규칙](../agents/data-collection-rule-overview.md) 리소스로 저장됩니다. 각 프로필에는 다음이 필요합니다.

- 이름. 만든 후에는 편집할 수 없습니다.
- 위치입니다. Azure 지역입니다.
- 모니터링 데이터를 저장할 Log Analytics 작업 영역입니다.
- 수집할 SQL 모니터링 데이터의 빈도 및 형식에 대한 컬렉션 설정입니다.

> [!NOTE]
> 프로필의 위치는 모니터링 데이터를 전송하려는 Log Analytics 작업 영역과 동일한 위치에 있어야 합니다.


:::image type="content" source="media/sql-insights-enable/profile-details.png" alt-text="프로필 세부 정보입니다." lightbox="media/sql-insights-enable/profile-details.png":::

모니터링 프로필에 대한 세부 정보를 입력한 후 **모니터링 프로필 만들기** 를 클릭합니다. 프로필이 배포되기까지 최대 1분이 소요됩니다.  **모니터링 프로필** 콤보 상자에 새 프로필이 나열되어 표시되지 않으면 새로 고침 단추를 클릭합니다. 배포가 완료되면 프로필이 표시됩니다.  새 프로필을 선택한 후 **프로필 관리** 탭을 선택하여 프로필에 연결할 모니터링 머신을 추가합니다.

### <a name="add-monitoring-machine"></a>모니터링 머신 추가
**모니터링 머신 추가** 를 선택하여 컨텍스트 패널을 열고, SQL 인스턴스를 모니터링하고 연결 문자열을 제공하도록 설정할 가상 머신을 선택합니다.

구독과 모니터링 가상 머신의 이름을 선택합니다. Key Vault를 사용하여 모니터링 사용자의 암호를 저장하는 경우, 해당 비밀이 포함된 Key Vault 리소스를 선택하고 연결 문자열에 사용할 URL과 비밀 이름을 입력합니다. 다른 SQL 배포의 연결 문자열을 식별하는 방법에 대한 자세한 내용은 다음 섹션을 참조하세요.


:::image type="content" source="media/sql-insights-enable/add-monitoring-machine.png" alt-text="모니터링 머신을 추가합니다." lightbox="media/sql-insights-enable/add-monitoring-machine.png":::


### <a name="add-connection-strings"></a>연결 문자열 추가 
연결 문자열은 동적 관리 뷰를 실행하기 위해 SQL에 로그인할 때 SQL 인사이트에서 사용해야 하는 사용자 이름을 지정합니다. Key Vault를 사용하여 모니터링 사용자의 암호를 저장하는 경우 사용할 비밀의 URL과 이름을 제공합니다. 

연결 문자열은 SQL 리소스의 각 유형에 따라 달라집니다.

#### <a name="azure-sql-database"></a>Azure SQL Database
다음 형식으로 연결 문자열을 입력합니다.

```
sqlAzureConnections": [ 
   "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=$username;Password=$password;" 
}
```

데이터베이스에 대한 **연결 문자열** 메뉴 항목에서 세부 정보를 가져옵니다.

:::image type="content" source="media/sql-insights-enable/connection-string-sql-database.png" alt-text="SQL 데이터베이스 연결 문자열" lightbox="media/sql-insights-enable/connection-string-sql-database.png":::

읽기 가능한 보조를 모니터링하려면 연결 문자열에 키값 `ApplicationIntent=ReadOnly`를 포함합니다. SQL Insights는 단일 보조 모니터링을 지원합니다. 수집된 데이터는 기본 또는 보조를 반영하도록 태그가 지정됩니다. 

#### <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance
다음 형식으로 연결 문자열을 입력합니다.

```
"sqlManagedInstanceConnections": [ 
      "Server= mysqlserver.database.windows.net;Port=1433;User Id=$username;Password=$password;", 
    ] 
```
관리되는 인스턴스의 **연결 문자열** 메뉴 항목에서 세부 정보를 가져옵니다.


:::image type="content" source="media/sql-insights-enable/connection-string-sql-managed-instance.png" alt-text="SQL Managed Instance 연결 문자열" lightbox="media/sql-insights-enable/connection-string-sql-managed-instance.png":::

읽기 가능한 보조를 모니터링하려면 연결 문자열에 키값 `ApplicationIntent=ReadOnly`를 포함합니다. SQL Insights는 단일 보조 모니터링을 지원하며 수집된 데이터는 기본 또는 보조를 반영하도록 태그가 지정됩니다. 

#### <a name="sql-server"></a>SQL Server 
다음 형식으로 연결 문자열을 입력합니다.

```
"sqlVmConnections": [ 
   "Server=MyServerIPAddress;Port=1433;User Id=$username;Password=$password;" 
] 
```

모니터링 가상 머신이 동일한 VNET에 있는 경우 서버의 개인 IP 주소를 사용합니다.  그렇지 않으면 공용 IP 주소를 사용합니다. Azure SQL 가상 머신을 사용하는 경우 리소스의 **보안** 페이지에서 이 작업에 사용할 포트를 확인할 수 있습니다.

:::image type="content" source="media/sql-insights-enable/sql-vm-security.png" alt-text="SQL 가상 머신 보안" lightbox="media/sql-insights-enable/sql-vm-security.png":::

## <a name="monitoring-profile-created"></a>모니터링 프로필 생성됨 

SQL 리소스에서 데이터를 수집하도록 가상 머신을 구성하려면 **모니터링 가상 머신 추가** 를 선택합니다. **개요** 탭으로 돌아가지 마세요. 몇 분 후에 상태 열 표시가 “수집 중”으로 변경되며, 모니터링하도록 선택한 SQL 리소스의 데이터가 표시됩니다.

데이터가 표시되지 않으면 [SQL 인사이트 문제 해결](sql-insights-troubleshoot.md)을 참조하여 문제를 식별합니다. 

:::image type="content" source="media/sql-insights-enable/profile-created.png" alt-text="프로필 생성됨" lightbox="media/sql-insights-enable/profile-created.png":::

> [!NOTE]
> 모니터링 프로필 또는 모니터링 VM의 연결 문자열을 업데이트해야 하는 경우 SQL 인사이트 **프로필 관리** 탭을 통해 이 작업을 수행할 수 있습니다. 업데이트가 저장되면 약 5분 이내에 변경 내용이 적용됩니다.

## <a name="next-steps"></a>다음 단계

- SQL 인사이트를 사용하도록 설정한 후에 제대로 작동하지 않는 경우 [SQL 인사이트 문제 해결](sql-insights-troubleshoot.md)을 참조하세요.
