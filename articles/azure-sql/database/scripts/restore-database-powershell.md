---
title: 'PowerShell: SQL Database에서 데이터베이스의 자동 백업 복원'
description: Azure PowerShell 예제 스크립트를 사용하여 SQL Database의 데이터베이스를 자동 백업에서 이전 시점으로 복원합니다.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: devx-track-azurepowershell
ms.devlang: PowerShell
ms.topic: sample
author: SQLSourabh
ms.author: sourabha
ms.reviewer: mathoma
ms.date: 03/27/2019
ms.openlocfilehash: 0d6b8352aa0606101fc171f639c5887aa0879c6d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751294"
---
# <a name="use-powershell-to-restore-a-database-to-an-earlier-point-in-time"></a>PowerShell을 사용하여 데이터베이스를 이전 시점으로 복원

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

이 PowerShell 스크립트 예제는 SQL Database의 데이터베이스를 특정 시점으로 복원합니다.  

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 Az PowerShell 1.4.0 이상이 필요합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Create SQL Database")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 데이터베이스 및 탄력적 풀을 호스트하는 서버를 만듭니다. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | 서버에 데이터베이스를 만듭니다. |
| [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) | 독립 실행형 또는 풀링된 데이터베이스의 지역 중복 백업을 가져옵니다. |
| [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) | 데이터베이스를 복원합니다. |
| [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) | 데이터베이스를 제거합니다. |
| [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | 복원할 수 있는 삭제된 데이터베이스를 가져옵니다. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

추가 SQL Database PowerShell 스크립트 샘플은 [Azure SQL Database PowerShell 스크립트](../powershell-script-content-guide.md)에 있습니다.
