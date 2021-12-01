---
title: 사용자 정의 복원 지점
description: 전용 SQL 풀(이전의 SQL DW)의 복원 지점을 만드는 방법.
services: synapse-analytics
author: WilliamDAssafMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/03/2019
ms.author: wiassaf
ms.reviewer: igorstan
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.openlocfilehash: dc3c6a4892404cc93febbb6c913700e46621e176
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133368094"
---
# <a name="user-defined-restore-points-for-a-dedicated-sql-pool-formerly-sql-dw"></a>전용 SQL 풀(이전의 SQL DW)의 사용자 정의 복원 지점

이 문서에서는 PowerShell 및 Azure Portal을 사용하여 Azure Synapse Analytics에서 전용 SQL 풀(이전의 SQL DW)의 새 사용자 정의 복원 지점을 만드는 방법을 알아봅니다.

## <a name="create-user-defined-restore-points-through-powershell"></a>PowerShell을 통해 사용자 정의 복원 지점 만들기

사용자 정의 복원 지점을 만들려면 [New-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell cmdlet을 사용합니다.

1. 시작하기 전에 [Azure PowerShell을 설치](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)해야 합니다.
2. PowerShell을 엽니다.
3. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
4. 복원할 데이터베이스가 포함된 구독을 선택합니다.
5. 즉각적인 데이터 웨어하우스 복사본에 대한 복원 지점을 만듭니다.

```powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label
```

6. 모든 기존 복원 지점의 목록을 참조하세요.

```powershell
# List all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Azure Portal을 통해 사용자 정의 복원 지점 만들기

Azure Portal을 통해 사용자 정의 복원 지점을 만들 수도 있습니다.

1. [Azure Portal](https://portal.azure.com/) 계정에 로그인합니다.

2. 복원 지점을 만들려는 전용 SQL 풀(이전의 SQL DW)로 이동합니다.

3. 왼쪽 창에서 **개요** 를 선택하고 **+ 새 복원 지점** 을 선택합니다. 새 복원 지점 단추를 사용할 수 없는 경우 전용 SQL 풀(이전의 SQL DW)이 일시 중지되지 않았는지 확인합니다.

    ![새 복원 지점](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. 사용자 정의 복원 지점의 이름을 지정하고 **적용** 을 클릭합니다. 사용자 정의 복원 지점의 기본 보존 기간은 7일입니다.

    ![복원 지점에 대한 이름](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>다음 단계

- [기존 전용 SQL 풀(이전의 SQL DW) 복원](sql-data-warehouse-restore-active-paused-dw.md)
- [삭제된 전용 SQL 풀(이전의 SQL DW) 복원](sql-data-warehouse-restore-deleted-dw.md)
- [지역 백업 전용 SQL 풀(이전의 SQL DW) 복원](sql-data-warehouse-restore-from-geo-backup.md)
