---
title: 단일 지역 상주
description: Azure Synapse Analytics에서 전용 SQL 풀(이전의 SQL DW)에 대한 단일 지역 상주를 구성하기 위한 방법 가이드
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/15/2021
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: fb42e82f91e2f386eab21083c42a2d494c396977
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133434808"
---
# <a name="configure-single-region-residency-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 전용 SQL 풀(이전의 SQL DW)에 대한 단일 지역 상주 구성

이 문서에서는 데이터가 지리적 경계 내에 유지되도록 로컬 중복 스토리지에 데이터베이스를 프로비전하는 방법을 알아봅니다. 이 문서는 Azure 지역 쌍이 국가 외부의 지역에 있는 전용 SQL 풀에만 적용됩니다. 이 문서의 단계를 수행하면 대부분의 데이터 및 모든 백업이 [쌍을 이루는 지역](../../availability-zones/cross-region-replication-azure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)에 복제되지 않습니다. 논리 서버 이름, 논리 데이터베이스 이름, 리소스 그룹 이름과 같은 일부 메타데이터는 기본적으로 쌍을 이루는 지역에 여전히 복제되며 변경할 수 없습니다. 

단일 지역 상주를 얻으려면 SQL 풀을 프로비전할 때 지역 중복 옵션에 대해 '아니요'를 선택하여 전용 SQL 풀(이전의 SQL DW)을 로컬 중복 스토리지에 프로비전합니다. SQL 풀을 이미 프로비전했고 지역 중복 스토리지에 있는 경우에는 복원 프로세스 중에 SQL 풀을 복원하고 지역 중복 스토리지에 대해 '아니요'를 선택할 수 있습니다. 두 시나리오에 대한 지침은 아래에 자세히 설명되어 있습니다. 

## <a name="provision-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-azure-portal"></a>Azure Portal을 통해 로컬 중복 스토리지에 전용 SQL 풀(이전의 SQL DW)을 프로비전합니다.

로컬 중복 스토리지에 전용 SQL 풀(이전의 SQL DW)을 프로비전하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/) 계정에 로그인합니다.
1. **전용 SQL 풀(이전의 SQL DW)** 을 검색합니다.

   ![새 DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

1. 추가를 클릭하고 **기본 사항** 탭*에서 요청하는 정보를 작성합니다.

1. 지역 쌍이 다른 지리적 위치에 있는 지역(예: 동남 아시아)에 서버가 있는 경우 지역 중복을 옵트 아웃할 수 있는 옵션이 있습니다. 

   ![데이터 상주](./media/sql-data-warehouse-single-region-residency/data-residency-1.png)

1. **다음: 네트워킹** 을 선택하여 퍼블릭 또는 프라이빗 엔드포인트를 구성합니다. 

1. **다음: 추가 설정** 을 선택하여 백업에서 복원하거나 빈 데이터베이스를 샘플링하거나 만듭니다.  

1. **다음: 태그** 를 선택하여 SQL 풀을 분류할 태그를 구성합니다. 

1. 완료되면 **다음: 리뷰 + 만들기** 를 클릭합니다. 데이터 보존을 보장하기 위해 올바른 스토리지 중복성이 선택되었는지 확인합니다. 

    ![단일 지역 풀 만들기](./media/sql-data-warehouse-single-region-residency/data-residency-2.png)

1. **만들기** 를 선택합니다.  

## <a name="provision-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-powershell"></a>PowerShell을 통해 로컬 중복 스토리지에 전용 SQL 풀(이전의 SQL DW) 프로비전
PowerShell을 통해 새 전용 SQL 풀을 만들려면 [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell cmdlet을 사용합니다.

1. 시작하기 전에 [Azure PowerShell을 설치](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)해야 합니다.
2. PowerShell을 엽니다.
3. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
4. 복원할 데이터베이스가 포함된 구독을 선택합니다.
5. 로컬 중복 스토리지에 새 데이터베이스를 만듭니다.
1. 데이터베이스가 로컬 중복 스토리지에 생성되었는지 확인합니다.  

```powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -Edition 'DataWarehouse' -ServerName $ServerName -DatabaseName $DatabaseName -BackupStorageRedundancy 'Local'
Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -Edition 'DataWarehouse' -ServerName $ServerName -DatabaseName $DatabaseName 
```

## <a name="restore-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-azure-portal"></a>Azure Portal을 통해 로컬 중복 스토리지에 대한 전용 SQL 풀(이전의 SQL DW) 복원
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 복원하려는 전용 SQL 풀로 이동합니다.

1. [개요] 블레이드의 위쪽에서 **복원** 을 선택합니다.

1. 복원할 복원 지점을 선택합니다. 

1. **지역 중복** 에서 '아니요'를 선택합니다. 

   ![포털을 통해 복원](./media/sql-data-warehouse-single-region-residency/data-residency-3.png)

## <a name="restore-a-dedicated-sql-pool-formerly-sql-dw-on-locally-redundant-storage-through-powershell"></a>PowerShell을 통해 로컬 중복 스토리지에 전용 SQL 풀(이전의 SQL DW) 복원
PowerShell을 통해 새 전용 SQL 풀을 만들려면 [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell cmdlet을 사용합니다.

1. 시작하기 전에 [Azure PowerShell을 설치](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)해야 합니다.
2. PowerShell을 엽니다.
3. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
4. 복원할 데이터베이스가 포함된 구독을 선택합니다.
5. 전용 SQL 풀(이전의 SQL DW)의 복원 지점을 나열합니다.
1. RestorePointCreationDate를 사용하여 원하는 복원 지점을 선택합니다.
1. BackupStorageRedundnacy를 '로컬'로 지정하는 Restore-AzSqlDatabase PowerShell cmdlet을 사용하여 전용 SQL 풀(이전의 SQL DW)을 원하는 복원 지점으로 복원합니다. 

```powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID -BackupStorageRedundancy 'Local'

# Verify the status of restored database
$RestoredDatabase.status
```


## <a name="next-steps"></a>다음 단계

- [기존 전용 SQL 풀(이전의 SQL DW) 복원](sql-data-warehouse-restore-active-paused-dw.md)
- [삭제된 전용 SQL 풀(이전의 SQL DW) 복원](sql-data-warehouse-restore-deleted-dw.md)
