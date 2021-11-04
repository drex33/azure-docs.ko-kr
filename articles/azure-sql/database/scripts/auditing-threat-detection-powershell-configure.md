---
title: 감사 및 Advanced Threat Protection의 PowerShell 예제 - Azure SQL Database
description: Azure SQL Database에서 감사 및 Advanced Threat Protection을 구성하는 Azure PowerShell 예제 스크립트
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: security, sqldbrb=1, devx-track-azurepowershell
ms.devlang: PowerShell
ms.topic: sample
author: DavidTrigano
ms.author: datrigan
ms.reviewer: mathoma, vanto
ms.date: 04/28/2020
ms.openlocfilehash: cee0c010101c132cb8c4f105ccfd46a5cb8afcf6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "131465310"
---
# <a name="use-powershell-to-configure-sql-database-auditing-and-advanced-threat-protection"></a>PowerShell을 사용하여 SQL Database 감사 및 Advanced Threat Protection 구성
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

이 PowerShell 스크립트 예제는 Azure SQL Database 감사 및 Advanced Threat Protection을 구성합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 Az PowerShell 1.4.0 이상이 필요합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/database-auditing-and-threat-detection/database-auditing-and-threat-detection.ps1?highlight=15-16 "Configure auditing and threat detection")]

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
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 서버를 만듭니다. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | 데이터베이스 또는 탄력적 풀을 만듭니다. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | 스토리지 계정을 만듭니다. |
| [Set-AzSqlDatabaseAuditing](/powershell/module/az.sql/set-azsqldatabaseaudit) | 데이터베이스에 대한 감사 정책을 설정합니다. |
| Set-AzSqlDatabaseThreatDetectionPolicy | 데이터베이스에 대한 Advanced Threat Protection 정책을 설정합니다. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

추가 SQL Database PowerShell 스크립트 샘플은 [Azure SQL Database PowerShell 스크립트](../powershell-script-content-guide.md)에 있습니다.
