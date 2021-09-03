---
title: IP 방화벽 규칙
description: Azure SQL Database 또는 Azure Synapse Analytics 방화벽에서 데이터베이스에 대한 서버 수준 IP 방화벽 규칙을 구성합니다. SQL Database에 대해 액세스를 관리하고 데이터베이스 수준 IP 방화벽 규칙을 구성합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.custom: sqldbrb=1, devx-track-azurecli, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: mathoma
ms.date: 07/14/2021
ms.openlocfilehash: c1bb51ff65e7239fc758553288c84a4a52f90740
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528457"
---
# <a name="azure-sql-database-and-azure-synapse-ip-firewall-rules"></a>Azure SQL Database 및 Azure Synapse IP 방화벽 규칙
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

예를 들어, Azure SQL Database 또는 Azure Synapse Analytics에 이름이 *mysqlserver* 인 새 서버를 만들 경우 서버 수준 방화벽은 서버에 대한 퍼블릭 엔드포인트(*mysqlserver.database.windows.net* 에서 액세스할 수 있음)에 대한 모든 액세스를 차단합니다. 간단히 하기 위해 *SQL Database* 는 SQL Database와 Azure Synapse Analytics를 참조할 때 사용됩니다.

> [!IMPORTANT]
> 이 문서는 *Azure SQL Managed Instance* 에 적용되지 *않습니다*. 네트워크 구성에 대한 내용은 [애플리케이션을 Azure SQL Database Managed Instance에 연결](../managed-instance/connect-application-instance.md)을 참조하세요.
>
> Azure Synapse는 서버 수준 IP 방화벽 규칙만 지원합니다. 데이터베이스 수준 방화벽 규칙은 지원하지 않습니다.

## <a name="how-the-firewall-works"></a>방화벽 작동 방식

다음 다이어그램과 같이 인터넷 및 Azure의 연결 시도가 서버 또는 데이터베이스에 도달하려면 먼저 방화벽을 통과해야 합니다.

   ![방화벽 구성 다이어그램][1]

### <a name="server-level-ip-firewall-rules"></a>서버 수준 IP 방화벽 규칙

이러한 규칙을 통해 클라이언트는 전체 서버, 즉 서버에서 관리하는 모든 데이터베이스에 액세스할 수 있습니다. 이 규칙은 *master* 데이터베이스에 저장됩니다. 서버에 대해 최대 128개의 서버 수준 IP 방화벽 규칙을 사용할 수 있습니다. **Azure 서비스 및 리소스에서 이 서버에 액세스하도록 허용** 설정을 사용하도록 설정하면 서버에 대한 단일 방화벽 규칙으로 계산됩니다.
  
Azure Portal, PowerShell 또는 Transact-SQL 문을 사용하여 서버 수준 IP 방화벽 규칙을 구성할 수 있습니다.

- Portal 또는 PowerShell을 사용하려면 구독 소유자 또는 구독 기여자여야 합니다.
- Transact-SQL을 사용하려면 서버 수준 보안 주체 로그인 또는 Azure Active Directory 관리자 권한으로 *master* 데이터베이스에 연결해야 합니다. (먼저 Azure 수준 사용 권한이 있는 사용자가 서버 수준 IP 방화벽 규칙을 만들어야 합니다.)

> [!NOTE]
> 기본적으로 Azure Portal에서 새 논리 SQL Server를 만드는 동안 **Azure 서비스 및 리소스가 이 서버에 액세스할 수 있도록 허용** 설정이 **아니요** 로 지정됩니다.

### <a name="database-level-ip-firewall-rules"></a>데이터베이스 수준 IP 방화벽 규칙

데이터베이스 수준 IP 방화벽 규칙을 사용하여 클라이언트는 특정(보안) 데이터베이스에 액세스할 수 있습니다. 각 데이터베이스(*master* 데이터베이스 포함)에 대해 이 규칙을 만들며 규칙은 개별 데이터베이스에 저장됩니다.
  
- master 및 user 데이터베이스에 대한 데이터베이스 수준 IP 방화벽 규칙은 Transact-SQL 문을 사용하는 경우에 한해 첫 번째 서버 수준 방화벽을 구성한 후에만 구성할 수 있습니다.
- 서버 수준 IP 방화벽 규칙의 범위 밖에 있는 데이터베이스 수준 IP 방화벽 규칙의 IP 주소 범위를 지정한 경우, 데이터베이스 수준 범위 내에 IP 주소를 가진 클라이언트만이 데이터베이스에 액세스할 수 있습니다.
- 데이터베이스에 대해 최대 128개의 데이터베이스 수준 IP 방화벽 규칙을 가질 수 있습니다. 데이터베이스 수준 IP 방화벽 규칙을 구성하는 방법에 대한 자세한 내용은 이 문서 뒷부분에 나오는 예제 및 [sp_set_database_firewall_rule(Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)을 참조하세요.

### <a name="recommendations-for-how-to-set-firewall-rules"></a>방화벽 규칙을 설정하는 방법에 대한 권장 사항

가능하면 데이터베이스 수준 IP 방화벽 규칙을 사용하는 것이 좋습니다. 이 방법을 사용하면 보안을 강화하고 데이터베이스의 이식성을 높일 수 있습니다. 관리자를 위한 서버 수준 IP 방화벽 규칙을 사용합니다. 동일한 액세스 요구 사항을 가진 데이터베이스가 많이 있고 각 데이터베이스를 개별적으로 구성하지 않으려는 경우에도 이 방법을 사용합니다.

> [!NOTE]
> 비즈니스 연속성의 맥락에서 휴대용 데이터베이스에 대한 자세한 내용은 [재해 복구를 위한 인증 요구 사항](active-geo-replication-security-configure.md)을 참조하세요.

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>서버 수준 및 데이터베이스 수준 IP 방화벽 규칙 비교

*한 데이터베이스의 사용자가 다른 데이터베이스에서 완전히 분리되어야 하나요?*

그렇다면 데이터베이스 수준 IP 방화벽 규칙을 사용하여 액세스 권한을 부여하세요. 이 방법을 사용하면 서버 수준 IP 방화벽 규칙을 사용할 수 없게 되며, 방화벽을 통과해서 모든 데이터베이스에 액세스하도록 허용됩니다. 그러면 방어 수준이 약해집니다.

*해당 IP 주소의 사용자가 모든 데이터베이스에 액세스할 수 있어야 하나요?*

그렇다면 서버 수준 IP 방화벽 규칙을 사용하여 방화벽 규칙을 구성해야 하는 횟수를 줄이세요.

*IP 방화벽 규칙을 구성하는 개인이나 팀이 Azure Portal, PowerShell 또는 REST API를 통해서만 액세스하나요?*

그렇다면 서버 수준 IP 방화벽 규칙을 사용해야 합니다. 데이터베이스 수준 IP 방화벽 규칙은 Transact-SQL을 통해서만 구성할 수 있습니다.  

*IP 방화벽 규칙을 구성하는 개인이나 팀이 데이터베이스 수준에서 높은 수준의 권한을 가질 수 없도록 금지되나요?*

그렇다면 서버 수준 IP 방화벽 규칙을 사용합니다. Transact-SQL을 통해 데이터베이스 수준 IP 방화벽 규칙을 구성하려면 데이터베이스 수준에서 적어도 *CONTROL DATABASE* 권한이 있어야 합니다.  

*IP 방화벽 규칙을 구성하거나 감사하는 개인이나 팀이 많은(100개 정도) 데이터베이스에 대한 IP 방화벽 규칙을 중앙에서 관리하고 있나요?*

이 시나리오에서 모범 사례는 사용자의 요구 사항 및 환경에 따라 결정됩니다. 서버 수준 IP 방화벽 규칙이 구성하기 더 쉬울 수 있지만 스크립팅은 데이터베이스 수준에서 규칙을 구성할 수 있습니다. 또한 서버 수준 IP 방화벽 규칙을 사용하더라도 데이터베이스에 대해  권한이 있는 사용자가 데이터베이스 수준 IP 방화벽 규칙을 만들었는지 확인하기 위해서는 데이터베이스 수준 IP 방화벽 규칙을 감사해야 할 수 있습니다.

*서버 수준 및 데이터베이스 수준 IP 방화벽 규칙을 함께 사용할 수 있나요?*

예. 관리자와 같은 일부 사용자는 서버 수준 IP 방화벽 규칙이 필요할 수 있습니다. 데이터베이스 애플리케이션 사용자와 같은 경우는 데이터베이스 수준 IP 방화벽 규칙이 필요할 수 있습니다.

### <a name="connections-from-the-internet"></a>인터넷에서 연결

컴퓨터가 인터넷의 서버에 연결하려고 할 때, 방화벽은 먼저 연결을 요청하는 데이터베이스를 위해 데이터베이스 수준 IP 방화벽 규칙에 반하는 요청의 본래 IP 주소를 확인합니다.

- 주소가 데이텀베이스 수준 IP 방화벽 규칙에 지정된 범위 안에 있을 경우, 규칙을 포함한 데이터베이스 연결이 허용됩니다.
- 주소가 데이터베이스 수준 IP 방화벽 규칙의 범위를 벗어날 경우 방화벽은 서버 수준 IP 방화벽 규칙을 확인합니다. 주소가 서버 수준 IP 방화벽 규칙의 범위 안에 있을 경우, 연결이 허용됩니다. 서버 수준 IP 방화벽 규칙은 서버가 관리하는 모든 데이터베이스에 적용됩니다.  
- 주소가 데이터베이스 수준 또는 서버 수준 IP 방화벽 규칙의 범위 안에 없을 경우 연결 요청이 실패합니다.

> [!NOTE]
> 로컬 컴퓨터에서 Azure SQL Database로 액세스하려면, 네트워크의 방화벽과 로컬 컴퓨터가 TCP 포트 1433으로 나가는 통신을 허용하는지 확인합니다.

### <a name="connections-from-inside-azure"></a>Azure 내부에서 연결

Azure 내에 호스트된 애플리케이션을 SQL Server에 연결할 수 있게 하려면 Azure 연결을 사용하도록 설정해야 합니다. Azure 연결을 사용하도록 설정하려면 시작 및 끝 IP 주소를 0.0.0.0으로 설정한 방화벽 규칙이 있어야 합니다.

Azure의 애플리케이션이 서버에 연결하려고 하면 방화벽은 이 방화벽 규칙이 있는지 확인하여 Azure 연결이 허용되는지 확인합니다. 이 기능은 **방화벽 및 가상 네트워크** 설정에서 **Azure 서비스 및 리소스가 이 서버에 액세스하도록 허용** 을 **켜기** 로 전환하여 Azure Portal 블레이드에서 직접 켤 수 있습니다. 설정을 켜기로 전환하면 이름이 **AllowAllWindowsAzureIps** 인 IP 0.0.0.0~0.0.0.0에 대한 인바운드 방화벽 규칙이 생성됩니다. master 데이터베이스 [sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database) 뷰에서 규칙을 볼 수 있습니다. Portal을 사용하지 않는 경우 PowerShell 또는 Azure CLI를 사용하여 시작 및 끝 IP 주소가 0.0.0.0인 방화벽 규칙을 만듭니다. 

> [!IMPORTANT]
> 이 옵션은 다른 고객 구독에서의 연결을 포함하여 Azure에서의 모든 연결을 허용하도록 방화벽을 구성합니다. 이 옵션을 선택할 때 로그인 및 사용자 권한이 부여된 사용자만으로 액세스를 제한하는지 확인합니다.

## <a name="permissions"></a>사용 권한

Azure SQL Server에 대한 IP 방화벽 규칙을 만들고 관리하려면 다음 중 하나에서 수행할 수 있습니다.

- [SQL Server 기여자](../../role-based-access-control/built-in-roles.md#sql-server-contributor) 역할에서
- [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할에서
- Azure SQL Server를 포함하는 리소스의 소유자

## <a name="create-and-manage-ip-firewall-rules"></a>IP 방화벽 규칙 만들기 및 관리

첫 번째 서버 수준 방화벽 설정은 [Azure Portal](https://portal.azure.com/)을 사용하거나 [Azure PowerShell](/powershell/module/az.sql), [Azure CLI](/cli/azure/sql/server/firewall-rule) 또는 [REST API](/rest/api/sql/firewallrules/createorupdate)를 사용하여 프로그래밍 방식으로 만듭니다. 이러한 방법이나 Transact-SQL을 사용하여 추가 서버 수준 IP 방화벽 규칙을 만들고 관리합니다.

> [!IMPORTANT]
> 데이터베이스 수준 IP 방화벽 규칙은 Transact-SQL을 사용해야만 만들고 관리할 수 있습니다.

성능 향상을 위해 서버 수준 IP 방화벽 규칙이 데이터베이스 수준에서 일시적으로 캐시됩니다. 캐시를 새로 고치려면 [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql)를 참조하세요.

> [!TIP]
> [데이터베이스 감사](../../azure-sql/database/auditing-overview.md)를 사용하여 서버 수준 및 데이터베이스 수준의 방화벽 변경 내용을 감사할 수 있습니다.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Azure Portal을 사용하여 서버 수준 IP 방화벽 규칙 관리

Azure Portal에서 서버 수준 IP 방화벽 규칙을 설정하려면 데이터베이스 또는 서버의 개요 페이지로 이동합니다.

> [!TIP]
> 자습서는 [Azure Portal을 사용하여 데이터베이스 만들기](single-database-create-quickstart.md)를 참조하세요.

#### <a name="from-the-database-overview-page"></a>데이터베이스 개요 페이지에서

1. 데이터베이스 개요 페이지에서 서버 수준 IP 방화벽 규칙을 설정하려면 아래 이미지와 같이 도구 모음에서 **서버 방화벽 설정** 을 선택합니다.

    ![서버 IP 방화벽 규칙](./media/firewall-configure/sql-database-server-set-firewall-rule.png)

    서버에 대한 **방화벽 설정** 페이지가 열립니다.

2. 도구 모음에서 **클라이언트 IP 추가** 를 선택하여 사용 중인 컴퓨터의 IP 주소를 추가하고 **저장** 을 선택합니다. 현재 IP 주소에 대한 서버 수준 IP 방화벽 규칙이 생성됩니다.

    ![서버 수준 IP 방화벽 규칙 설정](./media/firewall-configure/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>서버 개요 페이지에서

서버에 대한 개요 페이지가 열립니다. 이 페이지에 정규화된 서버 이름(예: *mynewserver20170403.database.windows.net*)이 표시되며 추가 구성을 위한 옵션도 제공됩니다.

1. 이 페이지에서 서버 수준 규칙을 설정하려면 왼쪽의 **설정** 메뉴에서 **방화벽** 을 선택합니다.

2. 도구 모음에서 **클라이언트 IP 추가** 를 선택하여 사용 중인 컴퓨터의 IP 주소를 추가하고 **저장** 을 선택합니다. 현재 IP 주소에 대한 서버 수준 IP 방화벽 규칙이 생성됩니다.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Transact-SQL을 사용하여 IP 방화벽 규칙 관리

| 카탈로그 뷰 또는 저장 프로시저 | Level | 설명 |
| --- | --- | --- |
| [sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database) |서버 |현재 서버 수준 IP 방화벽 규칙을 표시합니다. |
| [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database) |서버 |서버 수준 IP 방화벽 규칙을 생성 및 업데이트합니다. |
| [sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database) |서버 |서버 수준 IP 방화벽 규칙을 제거합니다. |
| [sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database) |데이터베이스 |현재 데이터베이스 수준 IP 방화벽 규칙을 표시합니다. |
| [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) |데이터베이스 |데이터베이스 수준 IP 방화벽 규칙을 생성 및 업데이트합니다. |
| [sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database) |데이터베이스 |데이터베이스 수준 IP 방화벽 규칙을 제거합니다. |

다음 예제에서는 기존 규칙을 검토하고 ‘Contoso’ 서버에서 일정 범위의 IP 주소를 사용하도록 설정하고 IP 방화벽 규칙을 삭제합니다.

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

다음으로 서버 수준 IP 방화벽 규칙을 추가합니다.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

서버 수준 IP 방화벽 규칙을 삭제하려면 ‘sp_delete_firewall_rule’ 저장 프로시저를 실행합니다. 다음 예제에서는 *ContosoFirewallRule* 규칙을 삭제합니다.

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>PowerShell을 사용하여 서버 수준 IP 방화벽 규칙 관리

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 여전히 Azure SQL Database에서 지원되지만 모든 개발은 Az.Sql 모듈을 위한 것입니다. 이러한 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다.

| Cmdlet | Level | 설명 |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |서버 |현재 서버 수준 방화벽 규칙 반환 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |서버 |새 서버 수준 방화벽 규칙 만들기 |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |서버 |기존 서버 수준 방화벽 규칙 속성 업데이트 |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |서버 |서버 수준 방화벽 규칙 제거 |

다음 예제에서는 PowerShell을 사용하여 서버 수준 IP 방화벽 규칙을 설정합니다.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```

> [!TIP]
> $servername으로 정규화된 DNS 이름이 아닌 서버 이름을 지정합니다. 예를 들어, **mysqldbserver.database.windows.net** 대신 **mysqldbserver** 를 지정합니다.
>
> 빠른 시작의 컨텍스트에서 PowerShell 예제를 보려면 [DB 만들기 - PowerShell](powershell-script-content-guide.md) 및 [PowerShell을 사용하여 단일 데이터베이스 만들기 및 서버 수준 IP 방화벽 규칙 구성](scripts/create-and-configure-database-powershell.md)을 참조하세요.

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>CLI를 사용하여 서버 수준 IP 방화벽 규칙 관리

| Cmdlet | Level | 설명 |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|서버|서버 IP 방화벽 규칙을 만듭니다.|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|서버|서버의 IP 방화벽 규칙을 나열합니다.|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|서버|IP 방화벽 규칙의 세부 정보를 표시합니다.|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|서버|IP 방화벽 규칙을 업데이트합니다.|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|서버|IP 방화벽 규칙을 삭제합니다.|

다음 예제에서는 CLI를 사용하여 서버 수준 IP 방화벽 규칙을 설정합니다.

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```

> [!TIP]
> $servername으로 정규화된 DNS 이름이 아닌 서버 이름을 지정합니다. 예를 들어, **mysqldbserver.database.windows.net** 대신 **mysqldbserver** 를 지정합니다.
>
> 빠른 시작의 컨텍스트에서 CLI 예제를 보려면 [DB 만들기 - Azure CLI](az-cli-script-samples-content-guide.md) 및 [Azure CLI를 사용하여 단일 데이터베이스 만들기 및 서버 수준 IP 방화벽 규칙 구성](scripts/create-and-configure-database-cli.md)을 참조하세요.

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>REST API를 사용하여 서버 수준 IP 방화벽 규칙 관리

| API | Level | 설명 |
| --- | --- | --- |
| [방화벽 규칙 나열](/rest/api/sql/firewallrules/listbyserver) |서버 |현재 서버 수준 IP 방화벽 규칙을 표시합니다. |
| [방화벽 규칙 만들기 또는 업데이트](/rest/api/sql/firewallrules/createorupdate) |서버 |서버 수준 IP 방화벽 규칙을 생성 및 업데이트합니다. |
| [방화벽 규칙 삭제](/rest/api/sql/firewallrules/delete) |서버 |서버 수준 IP 방화벽 규칙을 제거합니다. |
| [방화벽 규칙 가져오기](/rest/api/sql/firewallrules/get) | 서버 | 서버 수준 IP 방화벽 규칙을 가져옵니다. |

## <a name="troubleshoot-the-database-firewall"></a>데이터베이스 방화벽 문제 해결

Azure SQL Database에 대한 액세스가 예상과 다르게 동작하는 경우 다음 사항을 고려하세요.

- **로컬 방화벽 구성:**

  컴퓨터가 Azure SQL Database에 액세스하려면 먼저 컴퓨터에서 TCP 포트 1433에 대한 방화벽 예외를 만들어야 하는 경우가 있습니다. Azure 클라우드 경계 내에서 연결하려면 포트를 추가로 열어야 할 수도 있습니다. 자세한 내용은 [ADO.NET 4.5 및 Azure SQL Database에 대한 1433 이외 포트](adonet-v12-develop-direct-route-ports.md)의 "SQL Database: 외부 및 내부" 섹션을 참조하세요.

- **Network Address Translation:**

  NAT(Network Address Translation)로 인해 Azure SQL Database로 연결할 때 컴퓨터에서 사용하는 IP 주소는 컴퓨터 IP 구성 설정에서 나타나는 IP 주소와 다를 수도 있습니다. 컴퓨터에서 Azure에 연결하는 데 사용하는 IP 주소를 보려면
    1. 포털에 로그인합니다.
    1. 데이터베이스를 호스팅하는 서버에서 **구성** 탭으로 이동합니다.
    1. **현재 클라이언트 IP 주소** 는 **허용된 IP 주소** 섹션에 표시됩니다. **이 컴퓨터가 서버에 액세스할 수 있도록 허용된 IP 주소** 또는 **추가** 를 선택합니다.

- **허용 목록의 변경 내용이 아직 적용되지 않았습니다.**

  Azure SQL Database 방화벽 구성에 변경 내용이 적용되려면 최대 5분 정도 걸릴 수 있습니다.

- **로그인 권한이 없거나 사용한 암호가 잘못되었습니다.**

  로그인에 서버에 대한 사용 권한이 없거나 사용한 암호가 잘못된 경우 서버 연결이 거부됩니다. 방화벽 설정을 만들면 클라이언트가 서버 연결을 시도할 수 있는 *기회* 가 제공됩니다. 클라이언트는 꼭 필요한 보안 자격 증명을 제공해야 합니다. 로그인 준비에 대한 자세한 내용은 [데이터베이스 액세스 제어 및 권한 부여](logins-create-manage.md)를 참조하세요.

- **동적 IP 주소:**

  동적 IP 주소를 사용하는 인터넷 연결이 있고 방화벽을 통과하는 데 문제가 있는 경우 다음 해결 방법 중 하나를 시도합니다.
  
  - 인터넷 서비스 공급자에게 서버에 액세스하는 클라이언트 컴퓨터에 할당된 IP 주소 범위를 요청합니다. 해당 IP 주소 범위를 IP 방화벽 규칙으로 추가합니다.
  - 클라이언트 컴퓨터 대신 고정 IP 주소를 얻습니다. IP 주소를 IP 방화벽 규칙으로 추가합니다.

## <a name="next-steps"></a>다음 단계

- 회사 네트워크 환경이 Azure 데이터 센터에서 사용하는 컴퓨팅 IP 주소 범위(SQL 범위 포함)의 인바운드 통신을 허용하는지 확인합니다. 이러한 IP 주소를 허용 목록에 추가해야 할 수도 있습니다. [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 참조하세요.  
- [Azure SQL Database에서 단일 데이터베이스를 만드는 방법](single-database-create-quickstart.md)에 대한 빠른 시작을 참조하세요.
- 오픈 소스 또는 타사 애플리케이션에서 Azure SQL Database의 데이터베이스에 연결하는 방법에 대한 도움말은 [Azure SQL Database에 대한 클라이언트 빠른 시작 코드 샘플](connect-query-content-reference-guide.md#libraries)을 참조하세요.
- 열어야 할 수 있는 추가 포트에 대한 자세한 내용은 [ADO.NET 4.5와 SQL Database에 대한 1433 이외 포트](adonet-v12-develop-direct-route-ports.md)의 "SQL Database: 외부 및 내부" 섹션을 참조하세요.
- Azure SQL Database 보안 개요는 [데이터베이스 보안 설정](security-overview.md)을 참조하세요.

<!--Image references-->
[1]: ./media/firewall-configure/sqldb-firewall-1.png
