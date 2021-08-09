---
title: SQL Managed Instance 가상 클러스터에서 가상 네트워크 DNS 서버 설정 동기화
description: SQL Managed Instance 가상 클러스터에서 가상 네트워크 DNS 서버 설정을 동기화하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
author: srdan-bozovic-msft
ms.author: srbozovi
ms.topic: how-to
ms.date: 01/17/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 276b4033dc2a5c157245448453b72a8074b79702
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110707149"
---
# <a name="synchronize-virtual-network-dns-servers-setting-on-sql-managed-instance-virtual-cluster"></a>SQL Managed Instance 가상 클러스터에서 가상 네트워크 DNS 서버 설정 동기화
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 SQL Managed Instance 가상 클러스터에서 가상 네트워크 DNS 서버 설정을 동기화하는 시기와 방법을 설명합니다.

## <a name="when-to-synchronize-the-dns-setting"></a>DNS 설정을 동기화하는 시기

프라이빗 호스트 이름이 SQL Managed Instance에서 확인되어야 하는 몇 가지 시나리오(예: db 메일, 클라우드 또는 하이브리드 환경의 다른 SQL Server 인스턴스에 연결된 서버)가 있습니다. 이 경우에 Azure 내에서 사용자 지정 DNS를 구성해야 합니다. 자세한 내용은 [Azure SQL Managed Instance에 대한 사용자 지정 DNS 구성](custom-dns-configure.md)을 참조하세요.

Managed Instance를 호스트하는 [가상 클러스터](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)가 만들어진 후 이 변경 사항이 구현되면 가상 클러스터의 DNS 서버 설정을 가상 네트워크 구성과 동기화해야 합니다.

> [!IMPORTANT]
> DNS 서버 동기화 설정은 가상 클러스터에서 호스트되는 모든 관리되는 인스턴스에 영향을 줍니다.

## <a name="how-to-synchronize-the-dns-setting"></a>DNS 설정을 동기화하는 방법

### <a name="azure-rbac-permissions-required"></a>Azure RBAC 사용 권한 필요

DNS 서버 구성을 동기화하는 사용자에게는 다음 Azure 역할 중 하나가 있어야 합니다.

- 구독 참가자 역할 또는
- 다음 권한이 있는 사용자 지정 역할:
  - `Microsoft.Sql/virtualClusters/updateManagedInstanceDnsServers/action`

### <a name="use-azure-powershell"></a>Azure PowerShell 사용

DNS 서버 설정이 업데이트된 가상 네트워크를 가져옵니다.

```PowerShell
$ResourceGroup = 'enter resource group of virtual network'
$VirtualNetworkName = 'enter virtual network name'
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroup $ResourceGroup -Name $VirtualNetworkName
```
PowerShell 명령 [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction)을 사용하여 서브넷의 모든 가상 클러스터에 대한 DNS 서버 구성을 동기화합니다.

```PowerShell
Get-AzSqlVirtualCluster `
    | where SubnetId -match $virtualNetwork.Id `
    | select Id `
    | Invoke-AzResourceAction -Action updateManagedInstanceDnsServers -Force
```
### <a name="use-the-azure-cli"></a>Azure CLI 사용

DNS 서버 설정이 업데이트된 가상 네트워크를 가져옵니다.

```Azure CLI
resourceGroup="auto-failover-group"
virtualNetworkName="vnet-fog-eastus"
virtualNetwork=$(az network vnet show -g $resourceGroup -n $virtualNetworkName --query "id" -otsv)
```

Azure CLI 명령 [az resource invoke-action](/cli/azure/resource#az_resource_invoke_action)을 사용하여 서브넷의 모든 가상 클러스터에 대한 DNS 서버 구성을 동기화합니다.

```Azure CLI
az sql virtual-cluster list --query "[? contains(subnetId,'$virtualNetwork')].id" -o tsv \
    | az resource invoke-action --action updateManagedInstanceDnsServers --ids @-
```
## <a name="next-steps"></a>다음 단계

- 사용자 지정 DNS 구성에 대한 자세한 내용은 [Azure SQL Managed Instance에 대한 사용자 지정 DNS 구성](custom-dns-configure.md)을 참조하세요.
- 개요는 [Azure SQL Managed Instance란?](sql-managed-instance-paas-overview.md)을 참조하세요.
