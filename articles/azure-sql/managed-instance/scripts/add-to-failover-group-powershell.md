---
title: 'PowerShell: 자동 장애 조치(failover) 그룹에 관리형 인스턴스 추가'
titleSuffix: Azure SQL Managed Instance
description: 관리형 인스턴스를 만들어 자동 장애 조치(failover) 그룹에 추가하고 장애 조치(failover)를 테스트하는 Azure PowerShell 예제 스크립트입니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: PowerShell
ms.topic: sample
author: emlisa
ms.author: emlisa
ms.reviewer: mathoma
ms.date: 07/16/2019
ms.openlocfilehash: 9cb777100386f3ee29a60d7e2feef6a6a741d615
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130164156"
---
# <a name="use-powershell-to-add-a-managed-instance-to-a-failover-group"></a>PowerShell을 사용하여 관리형 인스턴스를 장애 조치(failover) 그룹에 추가 

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

이 PowerShell 스크립트 예제는 두 개의 관리형 인스턴스를 만들어서 장애 조치(failover) 그룹에 추가한 다음, 기본 관리형 인스턴스에서 보조 관리형 인스턴스로 장애 조치(failover)를 테스트합니다. 

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 Azure PowerShell 1.4.0 이상이 필요합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="sample-scripts"></a>샘플 스크립트

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거합니다. 리소스 그룹을 두 번 제거해야 합니다. 리소스 그룹을 처음으로 제거하면 관리형 인스턴스와 가상 클러스터가 제거되지만, `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'` 오류 메시지와 함께 실패하게 됩니다. Remove-AzResourceGroup 명령을 한 번 더 실행하여 리소스 그룹뿐 아니라 나머지 리소스를 모두 제거합니다.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Azure 리소스 그룹을 만듭니다.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 가상 네트워크를 만듭니다.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 가상 네트워크에 서브넷 구성을 추가합니다. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 리소스 그룹의 가상 네트워크를 가져옵니다. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 가상 네트워크에 서브넷을 가져옵니다. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 네트워크 보안 그룹을 만듭니다. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | 경로 테이블을 만듭니다. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 가상 네트워크에 대한 서브넷 구성을 업데이트합니다.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 가상 네트워크를 업데이트합니다.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | 네트워크 보안 그룹을 가져옵니다. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| 네트워크 보안 그룹에 네트워크 보안 규칙 구성을 추가합니다. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | 네트워크 보안 그룹을 업데이트합니다.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | 경로 테이블에 경로를 추가합니다. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | 경로 테이블을 업데이트합니다.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | 관리형 인스턴스를 만듭니다.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Azure SQL Managed Instance에 대한 정보를 반환합니다. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 공용 IP 주소를 만듭니다.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Virtual Network 게이트웨이에 대한 IP 구성을 만듭니다. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Virtual Network 게이트웨이를 만듭니다. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | 두 가상 네트워크 게이트웨이를 연결합니다.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| 새 Azure SQL Managed Instance 장애 조치(failover) 그룹을 만듭니다.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | SQL Managed Instance 장애 조치(failover) 그룹을 가져오거나 나열합니다.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | SQL Managed Instance 장애 조치(failover) 그룹의 장애 조치(failover)를 실행합니다. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹을 제거합니다. | 

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

SQL Managed Instance에 대한 추가 PowerShell 스크립트 샘플은 [Azure SQL Managed Instance PowerShell 스크립트](../../database/powershell-script-content-guide.md)에서 찾을 수 있습니다.
