---
title: Azure SQL Database 및 Azure Synapse Analytics의 연결 설정
description: 이 문서에서는 Azure SQL Database와 Azure Synapse Analytics의 프록시 및 리디렉션 설정과 TLS(전송 계층 보안) 버전 선택에 관해 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: connect
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: mathoma, vanto
ms.date: 08/03/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: de6af2c8581c157634ee1f8676e3dbc84ccd1803
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128590681"
---
# <a name="azure-sql-connectivity-settings"></a>Azure SQL 연결 설정
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa-formerly-sqldw.md)]

이 문서에서는 Azure Synapse Analytics에서 Azure SQL Database 및 [전용 SQL 풀(이전에는 SQL DW)](../../synapse-analytics\sql-data-warehouse\sql-data-warehouse-overview-what-is.md)용 서버에 대한 연결을 제어하는 ​​설정을 소개합니다. 이 설정은 서버와 연결된 모든 SQL Database 및 전용 SQL 풀(이전의 SQL DW) 데이터베이스에 적용됩니다.

> [!IMPORTANT]
> 이 문서는 Azure SQL Managed Instance에 적용되지 않습니다. 이 문서는 Azure Synapse Analytics 작업 영역의 전용 SQL 풀에도 적용되지 않습니다. 작업 영역에서 Azure Synapse Analytics 대한 [IP 방화벽](../../synapse-analytics/security/synapse-workspace-ip-firewall.md) 규칙을 구성하는 방법에 대한 지침은 Azure Synapse Analytics IP 방화벽 규칙을 참조하세요.

연결 설정은 다음 스크린샷에 표시된 것처럼 **방화벽 및 가상 네트워크** 화면에서 액세스할 수 있습니다.

:::image type="content" source="media/single-database-create-quickstart/manage-connectivity-settings.png" alt-text="SQL Server용 Azure Portal의 방화벽 및 가상 네트워크 설정 스크린샷":::

> [!NOTE]
> 이 설정은 적용되는 즉시 효력을 냅니다. 고객이 각 설정의 요구 사항을 충족하지 않으면 연결이 끊길 수 있습니다.

## <a name="deny-public-network-access"></a>퍼블릭 네트워크 액세스 거부

**공용 네트워크 액세스 거부** 가 **예** 로 설정되면 프라이빗 엔드포인트를 통한 연결만 허용됩니다. 이 설정이 **아니요**(기본값)인 경우, [네트워크 액세스 개요](network-access-controls-overview.md)에 설명된 대로 고객은 퍼블릭 엔드포인트(IP 기반 방화벽 규칙 또는 가상 네트워크 기반 방화벽 규칙 사용) 또는 프라이빗 엔드포인트(Azure Private Link 사용)를 사용하여 연결할 수 있습니다.

 ![공용 네트워크 액세스 거부가 예로 설정된 경우와 공용 네트워크 액세스 거부가 아니요로 설정된 경우의 연결을 보여 주는 다이어그램입니다.][2]

논리 서버에서 기존 프라이빗 엔드포인트 없이 **공용 네트워크 액세스 거부** 를 **예** 로 설정하려고 하면 다음과 비슷한 오류 메시지를 표시하며 실패합니다.  

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server.
Please set up private endpoints and retry the operation.
```

> [!NOTE]
> 프라이빗 엔드포인트로 이미 구성된 논리 서버에서 가상 네트워크 방화벽 규칙을 정의하려면 **공용 네트워크 액세스 거부** 를 **아니요** 로 설정합니다.

**공용 네트워크 액세스 거부** 가 **예** 로 설정되면 프라이빗 엔드포인트를 통한 연결만 허용됩니다. 퍼블릭 엔드포인트를 통한 모든 연결은 거부되며 다음과 비슷한 오류 메시지가 표시됩니다.  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

**공용 네트워크 액세스 거부** 를 **예** 로 설정하면 방화벽 규칙을 추가하거나 업데이트하려는 모든 시도가 거부되며 다음과 비슷한 오류 메시지가 표시됩니다.

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>PowerShell을 통해 공용 네트워크 액세스 변경

> [!IMPORTANT]
> Azure SQL Database에서는 여전히 PowerShell Azure Resource Manager 모듈은 지원하지만 향후 모든 개발은 Az.Sql 모듈을 위한 것입니다. 이러한 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다. 다음 스크립트에는 [Azure PowerShell 모듈](/powershell/azure/install-az-ps)이 필요합니다.

다음 PowerShell 스크립트는 서버 수준에서 **공용 네트워크 액세스** 속성을 `Get`(가져오기) 및 `Set`(설정)하는 방법을 보여 줍니다.

```powershell
# Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Disabled"
```

## <a name="change-public-network-access-via-cli"></a>CLI를 통해 공용 네트워크 액세스 변경

> [!IMPORTANT]
> 이 섹션의 모든 스크립트에는 [Azure CLI](/cli/azure/install-azure-cli)가 필요합니다.

### <a name="azure-cli-in-a-bash-shell"></a>Bash 셸의 Azure CLI

다음 CLI 스크립트는 Bash 셸에서 **공용 네트워크 액세스** 설정을 변경하는 방법을 보여 줍니다.

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"
```

## <a name="minimal-tls-version"></a>최소 TLS 버전 

최소 [TLS(전송 계층 보안)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) 버전 설정을 사용하면 고객이 SQL 데이터베이스에서 사용하는 TLS 버전을 선택할 수 있습니다.

현재 TLS 1.0, 1.1 및 1.2를 지원합니다. 최소 TLS 버전을 설정하면 최신 TLS 버전이 지원됩니다. 예를 들어 TLS 버전 1.1을 선택하면 TLS 1.1 및 1.2에 대한 연결만 허용되며 TLS 1.0을 사용한 연결은 거부됩니다. 테스트를 통해 애플리케이션이 지원되는지 확인한 후 최소 TLS 버전을 1.2로 설정하는 것이 좋습니다. 이 버전에는 이전 버전의 취약성에 대한 수정 프로그램이 포함되어 있으며 Azure SQL Database에서 지원되는 가장 높은 버전의 TLS입니다.

> [!IMPORTANT]
> 최소 TLS 버전의 기본값은 모든 버전을 허용하는 것입니다. TLS 버전을 적용한 후에는 기본값으로 되돌릴 수 없습니다.

이전 버전의 TLS가 사용되는 애플리케이션을 사용하는 고객의 경우 애플리케이션의 요구 사항에 따라 최소 TLS 버전을 설정하는 것이 좋습니다. 암호화되지 않은 연결을 사용하여 연결하는 애플리케이션을 사용하는 고객의 경우 최소 TLS 버전을 설정하지 않는 것이 좋습니다.

자세한 내용은 [SQL Database 연결에 대한 TLS 고려 사항](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)을 참조하세요.

최소 TLS 버전을 설정한 후에는 최소 TLS 버전의 서버보다 낮은 TLS 버전을 사용하는 고객의 로그인 시도가 실패하며 다음 오류가 표시됩니다.

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-the-minimal-tls-version-in-azure-portal"></a>Azure Portal에서 최소 TLS 버전 설정

**Azure Portal** 에서 [SQL Server](https://portal.azure.com) 리소스로 이동합니다. **보안** 설정에서 **방화벽 및 가상 네트워크** 를 선택합니다. 서버와 연결된 모든 SQL Database에 대해 원하는 **최소 TLS 버전** 을 선택하고 **저장** 을 선택합니다.

## <a name="set-the-minimal-tls-version-via-powershell"></a>PowerShell을 통해 최소 TLS 버전 설정

> [!IMPORTANT]
> Azure SQL Database에서는 여전히 PowerShell Azure Resource Manager 모듈은 지원하지만 향후 모든 개발은 Az.Sql 모듈을 위한 것입니다. 이러한 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다. 다음 스크립트에는 [Azure PowerShell 모듈](/powershell/azure/install-az-ps)이 필요합니다.

다음 PowerShell 스크립트는 논리 서버 수준에서 **최소 TLS 버전** 속성을 `Get`(가져오기) 및 `Set`(설정)하는 방법을 보여 줍니다.

```powershell
# Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-the-minimal-tls-version-via-the-azure-cli"></a>Azure CLI를 통해 최소 TLS 버전 설정

> [!IMPORTANT]
> 이 섹션의 모든 스크립트에는 [Azure CLI](/cli/azure/install-azure-cli)가 필요합니다.

### <a name="azure-cli-in-a-bash-shell"></a>Bash 셸의 Azure CLI

다음 CLI 스크립트는 bash 셸에서 **최소 TLS 버전** 설정을 변경하는 방법을 보여 줍니다.

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```

## <a name="change-the-connection-policy"></a>연결 정책 변경

[연결 정책](connectivity-architecture.md#connection-policy)은 고객이 Azure SQL Database에 연결하는 방법을 결정합니다.

## <a name="change-the-connection-policy-via-powershell"></a>PowerShell을 통해 연결 정책 변경

> [!IMPORTANT]
> Azure SQL Database에서는 여전히 PowerShell Azure Resource Manager 모듈은 지원하지만 향후 모든 개발은 Az.Sql 모듈을 위한 것입니다. 이러한 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다. 다음 스크립트에는 [Azure PowerShell 모듈](/powershell/azure/install-az-ps)이 필요합니다.

다음 PowerShell 스크립트는 PowerShell을 사용하여 연결 정책을 변경하는 방법을 보여 줍니다.

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id -ApiVersion 2014-04-01 -Verbose).Properties.ConnectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-the-connection-policy-via-the-azure-cli"></a>Azure CLI를 통해 연결 정책 변경

> [!IMPORTANT]
> 이 섹션의 모든 스크립트에는 [Azure CLI](/cli/azure/install-azure-cli)가 필요합니다.

### <a name="azure-cli-in-a-bash-shell"></a>Bash 셸의 Azure CLI

다음 CLI 스크립트는 bash 셸에서 연결 정책을 변경하는 방법을 보여 줍니다.

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Windows 명령 프롬프트의 Azure CLI

다음 CLI 스크립트는 Windows 명령 프롬프트(Azure CLI가 설치됨)에서 연결 정책을 변경하는 방법을 보여 줍니다.

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>다음 단계

- Azure SQL Database에서 연결이 작동하는 방식에 관한 개요는 [연결 아키텍처](connectivity-architecture.md)를 참조하세요.
- 서버의 연결 정책을 변경하는 방법에 관한 정보는 [conn-policy](/cli/azure/sql/server/conn-policy)를 참조하세요.

<!--Image references-->
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
