---
title: Azure SQL Database 및 Azure Synapse Analytics에서 서버란?
titleSuffix: ''
description: Azure SQL Database 및 Azure Synapse Analytics에서 사용되는 논리 SQL 서버 및 이러한 서버를 관리하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: LitKnd
ms.author: kendralittle
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 7ecd1dcb21960dcb5c0897c9ca5c26dd6280df11
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133362857"
---
# <a name="what-is-a-logical-sql-server-in-azure-sql-database-and-azure-synapse"></a>Azure SQL Database 및 Azure Synapse에서 논리 SQL 서버란?
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Azure SQL Database 및 Azure Synapse Analytics에서 서버는 데이터베이스 컬렉션에 대한 중앙 관리 지점 역할을 하는 논리적 구문입니다. 서버 수준에서 [로그인](logins-create-manage.md), [방화벽 규칙](firewall-configure.md), [감사 규칙](../../azure-sql/database/auditing-overview.md), [위협 탐지 정책](threat-detection-configure.md) 및 [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md)을 관리할 수 있습니다. 서버는 리소스 그룹과 다른 지역에 위치할 수 있습니다. Azure Synapse Analytics에서 Azure SQL Database 또는 데이터 웨어하우스 데이터베이스에 데이터베이스를 만들려면 서버가 있어야 합니다. 단일 서버에서 관리하는 모든 데이터베이스는 서버와 동일한 지역 내에서 생성됩니다.

이 서버는 온-프레미스 환경에서 친숙할 수 있는 SQL Server 인스턴스와 구별됩니다. 특히 데이터베이스 또는 데이터 웨어하우스 데이터베이스를 관리하는 서버와 관련하여 데이터베이스 또는 데이터 웨어하우스 데이터베이스의 위치에 대한 보증은 없습니다. 또한 Azure SQL Database 및 Azure Synapse는 인스턴스 수준 액세스 또는 기능을 노출하지 않습니다. 반대로 관리형 인스턴스의 인스턴스 데이터베이스는 모두 온-프레미스 또는 가상 머신 환경에서 SQL Server를 친숙하게 사용하는 것과 동일한 방식으로 함께 물리적으로 배치됩니다.

서버를 만들 때 해당 서버의 마스터 데이터베이스 및 해당 서버에서 생성된 모든 데이터베이스에 대한 관리 권한이 있는 서버 로그인 계정 및 암호를 제공합니다. 이 초기 계정이 SQL 로그인 계정입니다. Azure SQL Database 및 Azure Synapse Analytics는 인증을 위해 SQL 인증 및 Azure Active Directory 인증을 지원합니다. 로그인 및 인증에 대한 내용은 [Azure SQL Database에서 데이터베이스 및 로그인 관리](logins-create-manage.md)를 참조하세요. Windows 인증은 지원되지 않습니다.

SQL Database 및 Azure Synapse의 서버:

- Azure 구독 내에서 만들었지만 다른 구독에 포함된 리소스를 사용하여 이동시킬 수 있습니다
- 데이터베이스, 탄력적 풀 및 데이터 웨어하우스의 상위 리소스입니다.
- 데이터베이스, 탄력적 풀 및 데이터 웨어하우스 데이터베이스의 네임스페이스를 제공합니다.
- 강력한 수명 의미 체계를 가진 논리 컨테이너로서 서버를 삭제하고 해당 데이터베이스, 탄력적 풀 및 SQL 풀을 삭제합니다.
- [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md)에 포함됩니다. 서버 내의 데이터베이스, 탄력적 풀 및 데이터 웨어하우스 데이터베이스는 서버에서 액세스 권한을 상속합니다.
- Azure 리소스를 관리하기 위한 데이터베이스, 탄력적 풀 및 데이터 웨어하우스 데이터베이스의 ID 상위 요소입니다(데이터베이스 및 풀은 URL 구성표 참조).
- 지역에 리소스 배치
- 데이터베이스 액세스에 대한 연결 엔드포인트를 제공합니다(`<serverName>`.database.windows.net).
- 마스터 데이터베이스에 연결하여 DMV를 통해 포함된 리소스 관련 메타데이터에 대한 액세스를 제공합니다.
- 데이터베이스에 적용되는 관리 정책, 즉 로그인, 방화벽, 감사, 위협 요소 탐지 등에 대한 범위를 제공합니다.
- 상위 구독 내의 할당량으로 제한됩니다(기본적으로 구독 당 6대의 서버임. [여기에서 구독 제한 참조](../../azure-resource-manager/management/azure-subscription-service-limits.md)).
- 포함한 리소스에 대한 데이터베이스 할당량 및 DTU 또는 vCore 할당량의 범위를 제공합니다(예: 45000DTU).
- 포함된 리소스에서 사용하도록 설정된 기능에 대한 버전 관리 범위입니다.
- 서버 수준 주체 로그인은 서버에 있는 모든 데이터베이스를 관리할 수 있습니다.
- 서버에서 하나 이상의 데이터베이스에 대한 액세스를 부여하는 온-프레미스 환경의 SQL Server 인스턴스에서 해당 항목과 비슷한 로그인을 포함하고, 제한된 관리 권한이 부여될 수 있습니다. 자세한 내용은 [로그인](logins-create-manage.md)을 참조하세요.
- 서버에서 생성되는 모든 데이터베이스에 대한 기본 데이터 정렬은 `SQL_LATIN1_GENERAL_CP1_CI_AS`입니다. 여기서 `LATIN1_GENERAL`은 영어(미국)이고, `CP1`은 코드 페이지 1252이며, `CI`는 대/소문자 비구분이고, `AS`는 악센트 구분입니다.

## <a name="manage-servers-databases-and-firewalls-using-the-azure-portal"></a>Azure Portal을 사용하여 서버, 데이터베이스 및 방화벽 관리

서버에 대한 리소스 그룹을 미리 만들거나 서버 자체를 만드는 동안 만들 수 있습니다. 새 SQL Server를 만들거나 새 데이터베이스 만들기의 일부분으로 새 SQL Server 양식을 가져오는 여러 방법이 있습니다.

### <a name="create-a-blank-server"></a>빈 서버 만들기

[Azure Portal](https://portal.azure.com)을 사용하여 (데이터베이스, 탄력적 풀 또는 데이터 웨어하우스 데이터베이스 없이) 서버를 만들려면 비어 있는 SQL 서버(논리 SQL 서버) 양식으로 이동합니다.

### <a name="create-a-blank-or-sample-database-in-azure-sql-database"></a>Azure SQL Database에서 빈 데이터베이스 또는 샘플 데이터베이스 만들기

[Azure Portal](https://portal.azure.com)을 사용하여 SQL Database에서 데이터베이스를 만들려면 비어 있는 SQL Database 양식으로 이동하고 요청된 정보를 제공합니다. 리소스 그룹 및 서버를 미리 만들거나 데이터베이스 자체를 만드는 동안 만들 수 있습니다. 비어 있는 데이터베이스를 만들거나 Adventure Works LT에 따라 샘플 데이터베이스를 만들 수 있습니다.

  ![create database-1](./media/logical-servers/create-database-1.png)

> [!IMPORTANT]
> 데이터베이스의 가격 책정 계층 선택에 대한 자세한 내용은 [DTU 기반 구매 모델](service-tiers-dtu.md) 및 [vCore 기반 구매 모델](service-tiers-vcore.md)을 참조하세요.

관리되는 인스턴스를 만들려면 [관리되는 인스턴스 만들기](../managed-instance/instance-create-quickstart.md)를 참조하세요.

### <a name="manage-an-existing-server"></a>기존 서버 관리

기존 서버를 관리하려면 특정 데이터베이스 페이지, **SQL 서버** 페이지 또는 **모든 리소스** 페이지와 같은 다양한 방법을 사용하여 서버로 이동합니다.

기존 데이터베이스를 관리하려면 **SQL Database** 페이지로 이동하고 관리하려는 데이터베이스를 클릭합니다. 다음 스크린샷에서는 데이터베이스의 **개요** 페이지에서 데이터베이스의 서버 수준 방화벽을 설정하기 시작하는 방법을 보여줍니다.

   ![서버 방화벽 규칙](./media/single-database-create-quickstart/server-firewall-rule.png)

> [!IMPORTANT]
> 데이터베이스의 성능 속성을 구성하려면 [DTU 기반 구매 모델](service-tiers-dtu.md) 및 [vCore 기반 구매 모델](service-tiers-vcore.md)을 참조하세요.
> [!TIP]
> Azure Portal 빠른 시작은 [Azure Portal의 SQL Database에서 데이터베이스 만들기](single-database-create-quickstart.md)를 참조하세요.

## <a name="manage-servers-databases-and-firewalls-using-powershell"></a>PowerShell을 사용하여 서버, 데이터베이스 및 방화벽 관리

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 여전히 지원되지만 향후 모든 개발은 Az.Sql 모듈에 대해 진행됩니다. 이러한 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다.

Azure PowerShell을 사용하여 서버, 데이터베이스 및 방화벽을 만들고 관리하려면 다음 PowerShell cmdlet을 사용합니다. PowerShell을 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 탄력적 풀 만들기 및 관리에 대해서는 [탄력적 풀](elastic-pool-overview.md)을 참조하세요.

| Cmdlet | Description |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|데이터베이스 만들기 |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|하나 이상의 데이터베이스 가져오기|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|데이터베이스의 속성 설정 또는 기존 데이터베이스를 탄력적 풀로 이동|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|데이터베이스 제거|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|리소스 그룹 만들기|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|서버 만들기|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|서버에 대한 정보 반환|
|[Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver)|서버의 속성 수정|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|서버 제거|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|서버 수준 방화벽 규칙 만들기 |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|서버의 방화벽 규칙 가져오기|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|서버에서 방화벽 규칙 수정|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|서버에서 방화벽 규칙 삭제|
| New-AzSqlServerVirtualNetworkRule | Virtual Network 서비스 엔드포인트인 서브넷을 기반으로 [*가상 네트워크 규칙*](vnet-service-endpoint-rule-overview.md)을 만듭니다. |

> [!TIP]
> PowerShell 빠른 시작은 [PowerShell을 사용하여 Azure SQL Database에서 데이터베이스 만들기](single-database-create-quickstart.md)를 참조하세요. PowerShell 예제 스크립트에 대해서는 [PowerShell을 사용하여 Azure SQL Database에서 데이터베이스 만들기 및 방화벽 규칙 구성](scripts/create-and-configure-database-powershell.md) 및 [PowerShell을 사용하여 Azure SQL Database에서 데이터베이스 모니터링 및 크기 조정](scripts/monitor-and-scale-database-powershell.md)을 참조하세요.
>

## <a name="manage-servers-databases-and-firewalls-using-the-azure-cli"></a>Azure CLI를 사용하여 서버, 데이터베이스 및 방화벽 관리

[Azure CLI](/cli/azure)를 사용하여 서버, 데이터베이스 및 방화벽을 만들고 관리하려면 다음 [Azure CLI SQL Database](/cli/azure/sql/db) 명령을 사용합니다. [Cloud Shell](../../cloud-shell/overview.md)을 사용하여 CLI 브라우저에서 실행하거나 macOS, Linux 또는 Windows에서 [설치](/cli/azure/install-azure-cli)합니다. 탄력적 풀 만들기 및 관리에 대해서는 [탄력적 풀](elastic-pool-overview.md)을 참조하세요.

| Cmdlet | Description |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az_sql_db_create) |데이터베이스 만들기|
|[az sql db list](/cli/azure/sql/db#az_sql_db_list)|서버에서 관리하는 모든 데이터베이스 또는 탄력적 풀의 모든 데이터베이스 나열|
|[az sql db list-editions](/cli/azure/sql/db#az_sql_db_list_editions)|사용 가능한 서비스 목표 및 스토리지 용량 제한 나열|
|[az sql db list-usages](/cli/azure/sql/db#az_sql_db_list_usages)|데이터베이스 사용 정보 반환|
|[az sql db show](/cli/azure/sql/db#az_sql_db_show)|데이터베이스 가져오기
|[az sql db update](/cli/azure/sql/db#az_sql_db_update)|데이터베이스 업데이트|
|[az sql db delete](/cli/azure/sql/db#az_sql_db_delete)|데이터베이스 제거|
|[az group create](/cli/azure/group#az_group_create)|리소스 그룹 만들기|
|[az sql server create](/cli/azure/sql/server#az_sql_server_create)|서버 만들기|
|[az sql server list](/cli/azure/sql/server#az_sql_server_list)|서버 나열|
|[az sql server list-usages](/cli/azure/sql/server#az_sql_server_list-usages)|서버 사용 반환|
|[az sql server show](/cli/azure/sql/server#az_sql_server_show)|서버 가져오기|
|[az sql server update](/cli/azure/sql/server#az_sql_server_update)|서버 업데이트|
|[az sql server delete](/cli/azure/sql/server#az_sql_server_delete)|서버를 삭제합니다.|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|서버 방화벽 규칙 만들기|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|서버의 방화벽 규칙 나열|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|방화벽 규칙의 세부 정보 표시|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|방화벽 규칙 업데이트|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|방화벽 규칙 삭제|

> [!TIP]
> Azure CLI 빠른 시작은 [Azure CLI를 사용하여 Azure SQL Database에서 데이터베이스 만들기](az-cli-script-samples-content-guide.md)를 참조하세요. Azure CLI 예제 스크립트는 [CLI를 사용하여 Azure SQL Database에서 데이터베이스 만들기 및 방화벽 규칙 구성](scripts/create-and-configure-database-cli.md) 및 [CLI를 사용하여 Azure SQL Database에서 데이터베이스 모니터링 및 크기 조정](scripts/monitor-and-scale-database-cli.md)을 참조하세요.
>

## <a name="manage-servers-databases-and-firewalls-using-transact-sql"></a>Transact-SQL을 사용하여 서버, 데이터베이스 및 방화벽 관리

Transact-SQL을 사용하여 서버, 데이터베이스 및 방화벽을 만들고 관리하려면 다음 T-SQL cmdlet을 사용합니다. Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) 또는 서버에 연결하여 Transact-SQL 명령을 전달할 수 있는 다른 프로그램을 사용하여 이러한 명령을 실행할 수 있습니다. 탄력적 풀 관리에 대해서는 [탄력적 풀](elastic-pool-overview.md)을 참조하세요.

> [!IMPORTANT]
> Transact-SQL을 사용하여 서버를 만들거나 삭제할 수 없습니다.

| 명령 | Description |
| --- | --- |
|[CREATE DATABASE(Azure SQL Database)](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true) | Azure SQL Database에서 새로운 데이터베이스를 만듭니다. 새 데이터베이스를 만들려면 master 데이터베이스에 연결해야 합니다.|
|[CREATE DATABASE(Azure Synapse)](/sql/t-sql/statements/create-database-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Azure Synapse에서 새 데이터 웨어하우스 데이터베이스를 만듭니다. 새 데이터베이스를 만들려면 master 데이터베이스에 연결해야 합니다.|
| [ALTER DATABASE(Azure SQL Database)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |데이터베이스 또는 탄력적 풀을 수정합니다. |
|[ALTER DATABASE(Azure Synapse Analytics)](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true&tabs=sqlpool)|Azure Synapse에서 데이터 웨어하우스 데이터베이스를 수정합니다.|
|[DROP DATABASE(Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|데이터베이스를 삭제합니다.|
|[sys.database_service_objectives(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|데이터베이스에 대해(있는 경우) 버전(서비스 계층), 서비스 목표(가격 책정 계층) 및 탄력적 풀 이름을 반환합니다. 서버의 마스터 데이터베이스에 로그인하면 모든 데이터베이스에 대한 정보를 반환합니다. Azure Synapse의 경우 마스터 데이터베이스에 연결해야 합니다.|
|[sys.dm_db_resource_stats(Azure SQL 데이터베이스)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Azure SQL Database의 데이터베이스에 대한 CPU, IO 및 메모리 소비량을 반환합니다. 데이터베이스에서 활동이 없더라도 15초 간격으로 한 행이 있습니다.|
|[sys.resource_stats(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Azure SQL Database의 데이터베이스에 대한 CPU 사용량 및 스토리지 데이터를 반환합니다. 데이터는 5분 간격 이내로 수집 및 집계됩니다.|
|[sys.database_connection_stats(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Azure SQL Database의 데이터베이스 연결 이벤트에 대한 통계를 포함하며 데이터베이스 연결 성공 및 실패에 대한 개요를 제공합니다. |
|[sys.event_log(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Azure SQL Database에 대한 성공적인 Azure SQL Database 데이터베이스 연결, 연결 실패 및 교착 상태를 반환합니다. 이 정보를 사용하여 데이터베이스 활동을 추적하거나 문제를 해결할 수 있습니다.|
|[sp_set_firewall_rule(Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|서버에 대한 서버 수준 방화벽 설정을 만들거나 업데이트합니다. 이 저장 프로시저는 master 데이터베이스에서 서버 수준 보안 주체 로그인에 대해서만 사용할 수 있습니다. Azure 수준 사용 권한 가진 사용자가 첫 번째 서버 수준 방화벽 규칙을 만든 후에만 Transact-SQL을 사용하여 서버 수준 방화벽 규칙을 다시 만들 수 있습니다.|
|[sys.firewall_rules(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|서버와 연결된 서버 수준 방화벽 설정에 대한 정보를 반환합니다.|
|[sp_delete_firewall_rule(Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|서버에서 서버 수준 방화벽 설정을 제거합니다. 이 저장 프로시저는 master 데이터베이스에서 서버 수준 보안 주체 로그인에 대해서만 사용할 수 있습니다.|
|[sp_set_database_firewall_rule(Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Azure SQL Database의 데이터베이스에 대한 데이터베이스 수준 방화벽 규칙을 만들거나 업데이트합니다. 마스터 데이터베이스와 SQL Database의 사용자 데이터베이스에 대해 데이터베이스 방화벽 규칙을 구성할 수 있습니다. 데이터베이스 방화벽 규칙은 포함된 데이터베이스 사용자를 사용하는 경우에 유용합니다. Azure Synapse에서는 데이터베이스 방화벽 규칙이 지원되지 않습니다.|
|[sys.database_firewall_rules(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Azure SQL Database의 데이터베이스에 대한 데이터베이스 수준 방화벽 설정에 대한 정보를 반환합니다. |
|[sp_delete_database_firewall_rule(Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Azure SQL Database에서 자신의 데이터베이스에 대한 데이터베이스 수준 방화벽 설정을 제거합니다. |

> [!TIP]
> Microsoft Windows에서 SQL Server Management Studio를 사용하는 빠른 시작은 [Azure SQL Database: SQL Server Management Studio를 사용하여 데이터에 연결 및 쿼리](connect-query-ssms.md)를 참조하세요. Windows, Linux 또는 macOS에서 Visual Studio Code를 사용하는 빠른 시작은 [Azure SQL Database: Visual Studio Code를 사용하여 연결 및 데이터 쿼리](connect-query-vscode.md)를 참조하세요.

## <a name="manage-servers-databases-and-firewalls-using-the-rest-api"></a>REST API를 사용하여 서버, 데이터베이스 및 방화벽 관리

서버, 데이터베이스 및 방화벽을 만들고 관리하려면 다음 REST API 요청을 사용합니다.

| 명령 | Description |
| --- | --- |
|[서버 - Create 또는 Update](/rest/api/sql/servers/createorupdate)|새 서버를 만들거나 업데이트합니다.|
|[Servers - Delete](/rest/api/sql/servers/delete)|서버를 삭제합니다.|
|[Servers - Get](/rest/api/sql/servers/get)|서버를 가져옵니다.|
|[Servers - List](/rest/api/sql/servers/list)|서버 목록을 반환합니다.|
|[서버 - List by resource group](/rest/api/sql/servers/listbyresourcegroup)|리소스 그룹의 서버 목록을 반환합니다.|
|[Servers - Update](/rest/api/sql/servers/update)|기존 서버를 업데이트합니다.|
|[데이터베이스 - Create 또는 Update](/rest/api/sql/databases/createorupdate)|새 데이터베이스를 만들거나 기존 데이터베이스를 업데이트합니다.|
|[Databases - Delete](/rest/api/sql/databases/delete)|데이터베이스를 삭제합니다.|
|[데이터베이스 - Get](/rest/api/sql/databases/get)|데이터베이스를 가져옵니다.|
|[데이터베이스 - List by elastic pool](/rest/api/sql/databases/listbyelasticpool)|탄력적 풀에서 데이터베이스의 목록을 반환합니다.|
|[데이터베이스 - List by server](/rest/api/sql/databases/listbyserver)|서버의 데이터베이스의 목록을 반환합니다.|
|[데이터베이스 - Update](/rest/api/sql/databases/update)|기존 데이터베이스를 업데이트합니다.|
|[방화벽 규칙 - Create 또는 Update](/rest/api/sql/firewallrules/createorupdate)|방화벽 규칙을 만들거나 업데이트합니다.|
|[방화벽 규칙 - Delete](/rest/api/sql/firewallrules/delete)|방화벽 규칙을 삭제합니다.|
|[방화벽 규칙 - Get](/rest/api/sql/firewallrules/get)|방화벽 규칙을 가져옵니다.|
|[방화벽 규칙 - List by server](/rest/api/sql/firewallrules/listbyserver)|방화벽 규칙 목록을 반환합니다.|

## <a name="next-steps"></a>다음 단계

- SQL Server 데이터베이스를 Azure SQL Database로 마이그레이션하는 방법에 대한 자세한 내용은 [Azure SQL Database로 마이그레이션](migrate-to-database-from-sql-server.md)을 참조하세요.
- 지원되는 기능에 대한 자세한 내용은 [기능](features-comparison.md)을 참조하세요.