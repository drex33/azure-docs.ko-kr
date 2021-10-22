---
title: 'PowerShell: 자동 장애 조치(failover) 그룹에 데이터베이스 추가'
description: Azure PowerShell 예제 스크립트를 사용하여 Azure SQL Database Database의 데이터베이스를 만들어서 자동 장애 조치(failover) 그룹에 추가하고 장애 조치(failover)를 테스트합니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: PowerShell
ms.topic: sample
author: emlisa
ms.author: emlisa
ms.reviewer: mathoma
ms.date: 07/16/2019
ms.openlocfilehash: 651c19be68f4a468b116b193bfbe03f439ad1369
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130163593"
---
# <a name="use-powershell-to-add-a-database-to-a-failover-group"></a>PowerShell을 사용하여 장애 조치(failover) 그룹에 데이터베이스 추가

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

이 PowerShell 스크립트 예제에서는 Azure SQL Database의 단일 데이터베이스를 만들고, 장애 조치(failover) 그룹을 만들고, 데이터베이스를 이 그룹에 추가하고, 장애 조치(failover)를 테스트합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 Az PowerShell 1.4.0 이상이 필요합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="sample-scripts"></a>샘플 스크립트

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add a database to a failover group")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 서버를 만듭니다. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 서버에 대한 서버 수준 방화벽 규칙을 만듭니다. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | 새 데이터베이스를 만듭니다. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | 새 장애 조치(failover) 그룹을 만듭니다. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | 하나 이상의 데이터베이스를 가져옵니다. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | 장애 조치(failover) 그룹에 하나 이상의 데이터베이스를 추가합니다. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 장애 조치(failover) 그룹을 가져오거나 나열합니다. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| 장애 조치(failover) 그룹의 장애 조치(failover)를 실행합니다. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹을 제거합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

추가 SQL Database PowerShell 스크립트 샘플은 [Azure SQL Database PowerShell 스크립트](../powershell-script-content-guide.md)에 있습니다.
