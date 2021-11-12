---
title: 아웃바운드 방화벽 규칙(미리 보기)
description: 아웃바운드 방화벽 규칙 기능의 개요입니다.
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
ms.reviewer: vanto
ms.date: 11/10/2021
ms.openlocfilehash: d69536d8ed72dbd3793dd2481897d4534dbc2fdd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132352707"
---
# <a name="outbound-firewall-rules-for-azure-sql-database-and-azure-synapse-analytics-preview"></a>Azure SQL Database 및 Azure Synapse Analytics 대한 아웃바운드 방화벽 규칙(미리 보기)
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa-formerly-sqldw.md)] 

아웃바운드 방화벽 규칙은 Azure SQL 논리 서버에서 고객 정의 Azure Storage 계정 목록 및 Azure SQL 논리 서버로 네트워크 트래픽을 제한합니다. 이 목록에 없는 스토리지 계정 또는 SQL 데이터베이스에 액세스하려는 시도는 거부됩니다. 이 기능을 지원하는 Azure SQL Database 기능은 다음과 같습니다.

- [감사](auditing-overview.md)
- [취약점 평가](sql-vulnerability-assessment.md)
- [I/E 서비스](database-import-export-azure-services-off.md)
- OPENROWSET
- 대량 삽입
- [탄력적 쿼리](elastic-query-overview.md)

> [!IMPORTANT]
> 이 문서는 Azure SQL Database와 Azure Synapse Analytics의 [전용 SQL 풀(이전의 SQL DW)](../../synapse-analytics\sql-data-warehouse\sql-data-warehouse-overview-what-is.md) 모두에 적용됩니다. 이 설정은 서버와 연결된 모든 SQL Database 및 전용 SQL 풀(이전의 SQL DW) 데이터베이스에 적용됩니다. 편의상 '데이터베이스'라는 용어는 Azure SQL Database 및 Azure Synapse Analytics의 데이터베이스를 모두 나타냅니다. 마찬가지로 '서버'에 대한 모든 참조는 Azure SQL Database 및 Azure Synapse Analytics의 전용 SQL 풀(이전의 SQL DW)을 호스트하는 [논리 SQL 서버](logical-servers.md)를 나타냅니다. 이 문서는 Azure Synapse Analytics 작업 영역의 Azure SQL Managed Instance 또는 전용 SQL 풀에는 *적용되지* 않습니다.

## <a name="set-outbound-firewall-rules-in-the-azure-portal"></a>Azure Portal 아웃바운드 방화벽 규칙 설정

1. Azure SQL Database 대한 **방화벽 및 가상 네트워크** 블레이드에서 **아웃바운드 네트워킹** 섹션으로 이동한 다음 **아웃바운드 네트워킹 제한 구성을** 선택합니다.

   ![아웃바운드 네트워킹 섹션의 스크린샷][1]  

   그러면 오른쪽에 다음 블레이드가 열립니다.

   ![아무것도 선택하지 않은 아웃바운드 네트워킹 블레이드의 스크린샷][2]  

1. **아웃바운드 네트워킹 제한** 확인란을 선택한 다음 도메인 추가 단추를 사용하여 Storage 계정(또는 데이터베이스 SQL)에 대한 FQDN을 **추가합니다.**

   ![FQDN을 추가하는 방법을 보여주는 아웃바운드 네트워킹 블레이드의 스크린샷][3]  

1. 완료되면 아래와 비슷한 화면이 표시됩니다. **확인을** 선택하여 이러한 설정을 적용합니다.

   ![FQDN이 추가된 후 아웃바운드 네트워킹 블레이드의 스크린샷][4]  

## <a name="set-outbound-firewall-rules-using-powershell"></a>PowerShell을 사용하여 아웃바운드 방화벽 규칙 설정

> [!IMPORTANT]
> Azure SQL Database에서는 여전히 PowerShell Azure Resource Manager 모듈은 지원하지만 향후 모든 개발은 Az.Sql 모듈을 위한 것입니다. 이러한 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다. 다음 스크립트에는 [Azure PowerShell 모듈](/powershell/azure/install-az-ps)이 필요합니다.

다음 PowerShell 스크립트는 **RestrictOutboundNetworkAccess** 속성을 사용하여 아웃바운드 네트워킹 설정을 변경하는 방법을 보여줍니다.

```powershell
# Get current settings for Outbound Networking
(Get-AzSqlServer -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName>).RestrictOutboundNetworkAccess

# Update setting for Outbound Networking
$SecureString = ConvertTo-SecureString "<ServerAdminPassword>" -AsPlainText -Force

Set-AzSqlServer -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName> -SqlAdministratorPassword $SecureString  -RestrictOutboundNetworkAccess "Enabled"
```

이러한 PowerShell cmdlet을 사용하여 아웃바운드 방화벽 규칙 구성

```powershell
# List all Outbound Firewall Rules
Get-AzSqlServerOutboundFirewallRule -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName>

# Add an Outbound Firewall Rule
New-AzSqlServerOutboundFirewallRule -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName> -AllowedFQDN testOBFR1

# List a specific Outbound Firewall Rule
Get-AzSqlServerOutboundFirewallRule -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName> -AllowedFQDN <StorageAccountFQDN>

#Delete an Outbound Firewall Rule
Remove-AzSqlServerOutboundFirewallRule -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName> -AllowedFQDN <StorageAccountFQDN>
```

## <a name="set-outbound-firewall-rules-using-the-azure-cli"></a>Azure CLI 사용하여 아웃바운드 방화벽 규칙 설정

> [!IMPORTANT]
> 이 섹션의 모든 스크립트에는 [Azure CLI](/cli/azure/install-azure-cli)가 필요합니다.

### <a name="azure-cli-in-a-bash-shell"></a>Bash 셸의 Azure CLI

다음 CLI 스크립트는 bash 셸에서 아웃바운드 네트워킹 **설정(RestrictOutboundNetworkAccess** 속성 사용)을 변경하는 방법을 보여줍니다.

```azurecli-interactive
# Get current setting for Outbound Networking 
az sql server show -n sql-server-name -g sql-server-group --query "RestrictOutboundNetworkAccess"

# Update setting for Outbound Networking
az sql server update -n sql-server-name -g sql-server-group --set RestrictOutboundNetworkAccess="Enabled"
```

이러한 CLI 명령을 사용하여 아웃바운드 방화벽 규칙 구성

```azurecli-interactive
# List a server's outbound firewall rules.
az sql server outbound-firewall-rule list -g sql-server-group -s sql-server-name

# Create a new outbound firewall rule
az sql server outbound-firewall-rule create -g sql-server-group -s sql-server-name --outbound-rule-fqdn allowedFQDN

# Show the details for an outbound firewall rule.
az sql server outbound-firewall-rule show -g sql-server-group -s sql-server-name --outbound-rule-fqdn allowedFQDN

# Delete the outbound firewall rule.
az sql server outbound-firewall-rule delete -g sql-server-group -s sql-server-name --outbound-rule-fqdn allowedFQDN
```

## <a name="next-steps"></a>다음 단계

- Azure SQL Database 보안 개요는 [데이터베이스 보안 설정](security-overview.md)을 참조하세요.
- Azure SQL Database 연결에 대한 개요는 [Azure SQL 연결 아키텍처](connectivity-architecture.md)를 참조하세요.

<!--Image references-->
[1]: media/outbound-firewall-rules/Step1.jpg
[2]: media/outbound-firewall-rules/Step2.jpg
[3]: media/outbound-firewall-rules/Step3.jpg
[4]: media/outbound-firewall-rules/Step4.jpg
