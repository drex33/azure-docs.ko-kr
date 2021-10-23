---
title: 단일 데이터베이스 리소스 크기 조정
description: 이 문서에서는 Azure SQL Database에서 단일 데이터베이스에 사용할 수 있는 컴퓨팅 및 스토리지 리소스의 크기를 조정하는 방법을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, references_regions, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: mathoma
ms.date: 04/09/2021
ms.openlocfilehash: 3b52a5c86310ed6e87048274835c0dfbec1b9e9c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130216184"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Azure SQL Database에서 단일 데이터베이스 리소스 크기 조정

이 문서에서는 프로비전 컴퓨팅 계층에 있는 Azure SQL Database에서 사용할 수 있는 컴퓨팅 및 스토리지 리소스의 크기를 조정하는 방법을 설명합니다. 대신 [서버리스 컴퓨팅 계층](serverless-tier-overview.md)은 컴퓨팅 자동 크기 조정을 제공하고 사용된 컴퓨팅에 대해 초당 요금을 청구합니다.

처음으로 vCore 또는 DTU의 수를 선택한 이후 실제 환경에 따라 다음을 사용하여 데이터베이스를 동적으로 확장 또는 축소할 수 있습니다.

* [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Azure Portal](single-database-manage.md#the-azure-portal)
* [PowerShell](/powershell/module/az.sql/set-azsqldatabase)
* [Azure CLI](/cli/azure/sql/db#az_sql_db_update)
* [REST API](/rest/api/sql/databases/update)

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](file-space-manage.md)를 참조하세요.

## <a name="impact"></a>영향

서비스 계층 또는 컴퓨팅 계층 변경은 주로 다음 단계를 수행하는 서비스와 관련이 있습니다.

1. 데이터베이스에 대한 새 컴퓨팅 인스턴스를 만듭니다. 

    요청된 서비스 계층 및 컴퓨팅 크기로 새 계산 인스턴스를 만듭니다. 일부 서비스 계층 및 컴퓨팅 크기 조합 변경의 경우 새 컴퓨팅 인스턴스에 데이터베이스의 복제본을 만들어야 하며, 여기에는 데이터 복사가 수반되고 전체 대기 시간에 큰 영향을 미칠 수 있습니다. 그럼에도 이 단계 동안 데이터베이스는 온라인 상태로 유지되며, 원래 컴퓨팅 인스턴스의 데이터베이스에 대한 연결은 계속 전달됩니다.

2. 새 컴퓨팅 인스턴스로 연결 라우팅을 전환합니다.

    원래 컴퓨팅 인스턴스의 데이터베이스에 대한 기존 연결이 삭제됩니다. 새 컴퓨팅 인스턴스의 데이터베이스에 대한 모든 새 연결이 설정됩니다. 일부 서비스 계층 및 컴퓨팅 크기 조합 변경의 경우 데이터베이스 파일은 전환 도중 분리된 후 다시 연결됩니다.  그럼에도 데이터베이스를 일반적으로 30초 미만 동안, 또 몇 초 동안 사용할 수 없을 때 전환으로 인해 잠깐의 서비스 중단이 발생할 수 있습니다. 연결이 끊어질 때 실행되는 장기 실행 트랜잭션이 있는 경우 중단된 트랜잭션을 복구하기 위해 이 단계가 오래 걸릴 수 있습니다. [가속 데이터베이스 복구](../accelerated-database-recovery.md)를 통해 장기 실행 트랜잭션의 중단으로 인한 영향을 줄일 수 있습니다.

> [!IMPORTANT]
> 워크플로의 단계 중에는 데이터가 손실되지 않습니다. 서비스 계층이 변경되는 동안 Azure SQL Database를 사용하는 애플리케이션 및 구성 요소에서 몇 가지 [다시 시도 논리](troubleshoot-common-connectivity-issues.md)를 구현했는지 확인합니다.

## <a name="latency"></a>대기 시간

서비스 계층 변경, 단일 데이터베이스 또는 탄력적 풀의 컴퓨팅 크기 조정, 탄력적 풀에서 데이터베이스의 이동, 탄력적 풀 사이의 데이터베이스 이동에 따른 예상 대기 시간은 다음과 같이 매개 변수화됩니다.

|서비스 계층|기본 단일 데이터베이스,</br>표준(S0-S1)|기본 탄력적 풀,</br>표준(S2-S12), </br>범용 단일 데이터베이스 또는 탄력적 풀|프리미엄 또는 중요 비즈니스용 단일 데이터베이스 또는 탄력적 풀|하이퍼스케일
|:---|:---|:---|:---|:---|
|**기본 단일 데이터베이스,</br> 표준(S0-S1)**|&bull; &nbsp;사용된 공간에 독립적인 일정한 대기 시간</br>&bull; &nbsp;일반적으로 5분 미만|&bull; &nbsp;데이터 복사로 인한 사용된 데이터베이스 공간의 비례 대기 시간</br>&bull; &nbsp;일반적으로 사용되는 공간(GB)당 1분 미만|&bull; &nbsp;데이터 복사로 인한 사용된 데이터베이스 공간의 비례 대기 시간</br>&bull; &nbsp;일반적으로 사용되는 공간(GB)당 1분 미만|&bull; &nbsp;데이터 복사로 인한 사용된 데이터베이스 공간의 비례 대기 시간</br>&bull; &nbsp;일반적으로 사용되는 공간(GB)당 1분 미만|
|**기본 탄력적 풀, </br>표준(S2-S12), </br>범용 단일 데이터베이스 또는 탄력적 풀**|&bull; &nbsp;데이터 복사로 인한 사용된 데이터베이스 공간의 비례 대기 시간</br>&bull; &nbsp;일반적으로 사용되는 공간(GB)당 1분 미만|&bull; &nbsp;단일 데이터베이스의 경우, 사용된 공간에 독립적인 일정한 대기 시간</br>&bull; &nbsp;일반적으로 단일 데이터베이스의 경우 5분 미만</br>&bull; &nbsp;탄력적 풀의 경우 데이터베이스 수에 비례|&bull; &nbsp;데이터 복사로 인한 사용된 데이터베이스 공간의 비례 대기 시간</br>&bull; &nbsp;일반적으로 사용되는 공간(GB)당 1분 미만|&bull; &nbsp;데이터 복사로 인한 사용된 데이터베이스 공간의 비례 대기 시간</br>&bull; &nbsp;일반적으로 사용되는 공간(GB)당 1분 미만|
|**프리미엄 또는 중요 비즈니스용 단일 데이터베이스 또는 탄력적 풀**|&bull; &nbsp;데이터 복사로 인한 사용된 데이터베이스 공간의 비례 대기 시간</br>&bull; &nbsp;일반적으로 사용되는 공간(GB)당 1분 미만|&bull; &nbsp;데이터 복사로 인한 사용된 데이터베이스 공간의 비례 대기 시간</br>&bull; &nbsp;일반적으로 사용되는 공간(GB)당 1분 미만|&bull; &nbsp;데이터 복사로 인한 사용된 데이터베이스 공간의 비례 대기 시간</br>&bull; &nbsp;일반적으로 사용되는 공간(GB)당 1분 미만|&bull; &nbsp;데이터 복사로 인한 사용된 데이터베이스 공간의 비례 대기 시간</br>&bull; &nbsp;일반적으로 사용되는 공간(GB)당 1분 미만|
|**하이퍼스케일**|해당 없음|해당 없음|해당 없음|&bull; &nbsp;사용된 공간에 독립적인 일정한 대기 시간</br>&bull; &nbsp;일반적으로 2분 미만|

> [!NOTE]
> 추가로 표준(S2-S12) 및 범용 데이터베이스의 경우, 탄력적 풀에서 또는 탄력적 풀 사이에 데이터베이스를 이동하는 데 따른 대기 시간은 프리미엄 파일 공유([PFS](../../storage/files/storage-files-introduction.md)) 스토리지 사용 시 데이터베이스 크기에 비례합니다.
>
> 데이터베이스가 PFS 스토리지를 사용하는지 여부를 확인하려면 데이터베이스의 컨텍스트에서 다음 쿼리를 실행합니다. AccountType 열의 값이 `PremiumFileStorage` 또는 `PremiumFileStorage-ZRS`인 경우 데이터베이스에서 PFS 스토리지를 사용 중인 것입니다.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!NOTE]
> 중요 비즈니스용에서 범용 계층으로 크기를 조정할 때 기본적으로 영역 중복 속성은 동일하게 유지됩니다. 범용 계층에 대한 영역 중복 전환에 따른 대기 시간뿐만 아니라 영역 중복성 사용되는 경우 이 다운그레이드에 따른 대기 시간은 데이터베이스 크기에 비례합니다.

> [!TIP]
> 진행 중인 작업을 모니터링하려면 [SQL REST API를 사용하여 작업 관리](/rest/api/sql/operations/list), [CLI를 사용하여 작업 관리](/cli/azure/sql/db/op), [T-SQL을 사용하여 작업 모니터링](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)과 두 가지 PowerShell 명령, 즉 [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) 및 [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity)를 참조하세요.

## <a name="cancelling-changes"></a>변경 취소

서비스 계층 변경 또는 컴퓨팅 크기 조정 작업을 취소할 수 있습니다.

### <a name="the-azure-portal"></a>Azure Portal

데이터베이스 개요 블레이드에서 **알림** 으로 이동하고 진행 중인 작업을 나타내는 타일을 클릭합니다.

![진행 중인 작업](./media/single-database-scale/ongoing-operations.png)

다음으로 **이 작업 취소** 단추를 클릭합니다.

![진행 중인 작업 취소](./media/single-database-scale/cancel-ongoing-operation.png)

### <a name="powershell"></a>PowerShell

PowerShell 명령 프롬프트에서 `$resourceGroupName`, `$serverName` 및 `$databaseName`을 설정하고 다음 명령을 실행합니다.

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>기타 고려 사항

- 상위 서비스 계층이나 컴퓨팅 크기로 업그레이드하는 경우 더 큰 크기(최대 크기)를 명시적으로 지정하지 않는 한 최대 데이터베이스 크기는 증가하지 않습니다.
- 데이터베이스를 다운그레이드하려면 데이터베이스 사용 공간이 대상 서비스 계층 및 컴퓨팅 크기의 최대 허용 크기보다 작아야 합니다.
- **프리미엄** 에서 **표준** 계층으로 다운그레이드하는 경우 (1) 데이터베이스의 최대 크기가 대상 컴퓨팅 크기에서 지원되고 (2) 최대 크기가 대상 컴퓨팅 크기의 포함된 스토리지 용량을 초과하는 경우 추가 스토리지 비용이 적용됩니다. 예를 들어 최대 크기가 500GB인 P1 데이터베이스 크기를 S3으로 줄이는 경우 S3이 최대 크기인 1TB를 지원하고 포함된 스토리지 용량은 250GB에 불과하므로 추가 스토리지 비용이 적용됩니다. 따라서 추가 스토리지 용량은 500GB – 250GB = 250GB입니다. 추가 스토리지 가격 책정에 대한 자세한 내용은 [Azure SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요. 실제 사용된 공간의 크기가 포함된 스토리지 용량보다 작은 경우 데이터베이스 최대 크기를 포함된 크기로 줄여 이러한 추가 비용을 방지할 수 있습니다.
- [지역 복제](active-geo-replication-configure-portal.md)를 사용하도록 설정된 데이터베이스를 업그레이드하는 경우, 주 데이터베이스를 업그레이드하기 전에 먼저 해당 보조 데이터베이스를 원하는 서비스 계층 및 컴퓨팅 크기로 업그레이드합니다(최상의 성능을 위한 일반 지침). 다른 버전으로 업그레이드하는 경우 보조 데이터베이스를 먼저 업그레이드해야 합니다.
- [지역 복제](active-geo-replication-configure-portal.md)를 사용하도록 설정된 데이터베이스를 업그레이드하는 경우, 주 데이터베이스를 업그레이드하기 전에 먼저 해당 보조 데이터베이스를 원하는 서비스 계층 및 컴퓨팅 크기로 업그레이드합니다(최상의 성능을 위한 일반 지침). 다른 버전으로 다운그레이드하는 경우 주 데이터베이스를 먼저 다운그레이드해야 합니다.
- 복원 서비스는 여러 서비스 계층에서 서로 다르게 제공됩니다. **기본** 계층으로 다운그레이드하는 경우 백업 보존 기간이 더 짧아집니다. [Azure SQL Database 백업](automated-backups-overview.md)을 참조하세요.
- 데이터베이스의 새로운 속성은 변경이 완료될 때까지 적용되지 않습니다.
- 데이터베이스의 크기를 조정하기 위해 데이터를 복사해야 하는 경우([대기 시간](#latency) 참조) 서비스 계층을 변경할 때 리소스 사용률이 높으면 크기 조정 작업을 수행하는 데 시간이 더 오래 걸릴 수 있습니다. [ADR(가속 데이터베이스 복구)](/sql/relational-databases/accelerated-database-recovery-concepts)를 사용하는 경우 장기 실행 트랜잭션의 롤백은 중대한 지연 원인이 아닙니다. 하지만 높은 동시 리소스 사용량으로 인해 (특히 컴퓨팅 크기가 작은 경우) 크기 조정을 위한 컴퓨팅, 스토리지 및 네트워크 대역폭 리소스를 줄일 수 있습니다.

## <a name="billing"></a>결제

사용량 또는 데이터베이스가 한 시간 미만 동안 활성 상태였는지 여부와 무관하게, 해당 시간에 적용된 최고 서비스 계층 + 컴퓨팅 크기를 사용하여 데이터베이스가 있었던 각 시간에 대해 요금이 청구됩니다. 예를 들어 단일 데이터베이스를 만들고 5분 후 삭제하더라도 청구서에는 데이터베이스 1시간 사용에 대한 요금이 반영됩니다.

## <a name="change-storage-size"></a>스토리지 크기 변경

### <a name="vcore-based-purchasing-model"></a>vCore 기반 구매 모델

- 스토리지는 1GB 증분 단위로 데이터 스토리지 최대 크기 제한까지 프로비전할 수 있습니다. 구성 가능한 최소 데이터 스토리지는 5GB입니다. 각 서비스 목표에 대한 데이터 스토리지 최대 크기 제한은 [vCore 구매 모델을 사용한 단일 데이터베이스에 대한 리소스 제한](resource-limits-vcore-single-databases.md) 및 [DTU 구매 모델을 사용한 단일 데이터베이스에 대한 리소스 제한](resource-limits-dtu-single-databases.md)의 리소스 제한 설명서 페이지를 참조하세요.
- 단일 데이터베이스에 대한 데이터 스토리지는 [Azure Portal](https://portal.azure.com), [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az_sql_db_update) 또는 [REST API](/rest/api/sql/databases/update)를 사용하여 해당 최대 크기를 늘리거나 줄여서 프로비전할 수 있습니다. 최대 크기 값이 바이트로 지정된 경우 1GB(1073741824바이트)의 배수여야 합니다.
- 데이터베이스의 데이터 파일에 저장할 수 있는 데이터의 양은 구성된 데이터 스토리지 최대 크기에 의해 제한됩니다. 이 스토리지 외에도 Azure SQL Database는 트랜잭션 로그에 사용할 30% 더 많은 스토리지를 자동으로 할당합니다.
- Azure SQL Database는 `tempdb` 데이터베이스에 대해 vCore당 32GB를 자동으로 할당합니다. `tempdb`는 모든 서비스 계층의 로컬 SSD 스토리지에 있습니다.
- 단일 데이터베이스 또는 탄력적 풀에 대한 스토리지의 가격은 데이터 스토리지 및 트랜잭션 로그 스토리지 용량 합계에 해당 서비스 계층의 스토리지 단가를 곱한 값입니다. `tempdb`의 비용은 가격에 포함됩니다. 스토리지 가격에 대한 자세한 내용은 [Azure SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](file-space-manage.md)를 참조하세요.

### <a name="dtu-based-purchasing-model"></a>DTU 기반 구매 모델

- 단일 데이터베이스에 대한 DTU 가격에는 특정 크기의 스토리지가 추가 비용 없이 포함됩니다. 포함된 용량 외 추가 스토리지는 최대 250GB씩 총 1TB이 최대 크기 제한까지 추가 비용을 내고 프로비전할 수 있고 1TB 이상일 경우 256GB씩 프로비전할 수 있습니다. 포함된 스토리지 크기 및 최대 크기 제한에 대한 자세한 내용은 [단일 데이터베이스: 스토리지 크기 및 컴퓨팅 크기](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)를 참조하세요.
- 단일 데이터베이스에 대한 추가 스토리지는 Azure Portal, [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az_sql_db_update), 또는 [REST API](/rest/api/sql/databases/update)를 통해 해당하는 최대 크기를 늘려서 프로비전할 수 있습니다.
- 단일 데이터베이스에 대한 추가 스토리지 가격은 추가 스토리지 용량에 해당 서비스 계층의 추가 스토리지 단가를 곱한 것입니다. 추가 스토리지 가격에 대한 자세한 내용은 [Azure SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](file-space-manage.md)를 참조하세요.

### <a name="geo-replicated-database"></a>지역에서 복제된 데이터베이스

복제된 보조 데이터베이스의 데이터베이스 크기를 변경하려면 주 데이터베이스의 크기를 변경합니다. 이후 변경 사항이 복제되고 보조 데이터베이스에도 구현됩니다.

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>최대 크기가 1TB를 초과하는 경우의 P11 및 P15 제약 조건

현재 일부 예외 지역 이외의 모든 지역에서 프리미엄 계층의 스토리지는 1TB를 초과하여 사용할 수 있습니다(예외 지역: 중국 동부, 중국 북부, 독일 중부, 독일 북동부). 이러한 지역에서 프리미엄 계층 스토리지 최대 크기는 1TB로 제한됩니다. 다음 고려 사항 및 제한 사항은 최대 크기가 1TB보다 큰 P11 및 P15 데이터베이스에 적용됩니다.

- P11 또는 P15 데이터베이스의 최대 크기가 1TB보다 큰 값으로 설정된 경우에는 P11 또는 P15 데이터베이스에만 복원하거나 복사할 수 있습니다.  이후 크기 조정 재작업 시 할당된 공간 크기가 새 컴퓨팅 크기의 최대 크기 제한을 초과하지 않는 경우 데이터베이스를 다른 계산 크기로 재조정할 수 있습니다.
- 활성 지역 복제 시나리오의 경우:
  - 지역에서 복제 관계 설정: 주 데이터베이스가 P11 또는 P15인 경우 보조 데이터베이스는 P11 또는 P15이어야 합니다. 낮은 컴퓨팅 계층은 1TB를 지원하지 않으므로 보조 데이터베이스로 거부됩니다.
  - 지역에서 복제 관계에서 주 데이터베이스 업그레이드: 주 데이터베이스에서 최대 크기를 1TB보다 크게 변경하면 보조 데이터베이스에서도 동일한 변경 사항이 트리거됩니다. 변경 내용을 적용하려면 주 데이터베이스에서 두 가지 업그레이드에 성공해야 합니다. 1TB보다 큰 옵션에 대한 지역 제한 사항이 적용됩니다. 1TB보다 큰 크기를 지원하지 않는 지역에 보조 데이터베이스가 있는 경우 주 데이터베이스는 업그레이드되지 않습니다.
- 1TB보다 큰 크기의 P11/P15 데이터베이스 로드에 대한 Import/Export 서비스 사용은 지원되지 않습니다. SqlPackage.exe를 사용하여 데이터를 [가져오기](database-import.md) 및 [내보내기](database-export.md)합니다.

## <a name="next-steps"></a>다음 단계

전반적인 리소스 제한은 [Azure SQL Database vCore 기반 리소스 제한 - 단일 데이터베이스](resource-limits-vcore-single-databases.md) 및 [Azure SQL Database DTU 기반 리소스 제한 - 단일 데이터베이스](resource-limits-dtu-single-databases.md)를 참조하세요.
