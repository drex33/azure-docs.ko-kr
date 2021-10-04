---
title: 백업 및 복원 - 스냅샷, 지역 중복
description: Azure Synapse Analytics 전용 SQL 풀에서 백업 및 복원 작업을 수행하는 방법에 대해 알아봅니다. 백업을 사용하여 데이터 웨어하우스를 주 지역의 복원 지점으로 복원합니다. 지역 중복 백업을 사용하여 다른 지역에 복원합니다.
services: synapse-analytics
author: joannapea
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: d17f370739acf5280850beb1eb14ad8cdc0268a6
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129424939"
---
# <a name="backup-and-restore-in-azure-synapse-dedicated-sql-pool"></a>Azure Synapse 전용 SQL 풀에서 백업 및 복원

Azure Synapse 전용 SQL 풀에서 백업 및 복원을 사용하는 방법을 알아봅니다. 전용 SQL 풀 복원 지점을 사용하여 주 지역의 데이터 웨어하우스를 이전 상태로 복구하거나 복사합니다. 데이터 웨어하우스 지역 중복 백업을 사용하여 다른 지역에 복원합니다.

## <a name="what-is-a-data-warehouse-snapshot"></a>데이터 웨어하우스 스냅샷이란?

*데이터 웨어하우스 스냅샷* 은 데이터 웨어하우스를 이전 상태로 복구하거나 복사하는 데 활용할 수 있는 복원 지점을 만듭니다.  전용 SQL 풀은 분산 시스템이므로 데이터 웨어하우스 스냅샷은 Azure Storage에 있는 여러 파일로 구성됩니다. 스냅샷은 데이터 웨어하우스에 저장된 데이터의 증분 변경 내용을 캡처합니다.

*데이터 웨어하우스 복원* 은 기존 데이터 웨어하우스 또는 삭제된 데이터 웨어하우스의 복원 지점에서 만들어지는 새 데이터 웨어하우스입니다. 데이터 웨어하우스 복원은 데이터가 실수로 손상되거나 삭제된 후 데이터를 다시 만들기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. 데이터 웨어하우스 스냅샷은 테스트 또는 개발 용도로 데이터 웨어하우스의 복사본을 만드는 강력한 메커니즘이기도 합니다. 전용 SQL 풀 복원 속도는 원본 및 대상 데이터 웨어하우스의 데이터베이스 크기 및 위치에 따라 달라질 수 있습니다.

## <a name="automatic-restore-points"></a>자동 복원 지점

스냅샷은 복원 지점을 만드는 기본 제공 기능입니다. 이 기능은 사용하도록 설정할 필요가 없습니다. 그러나 복원 지점을 만들려면 전용 SQL 풀이 활성 상태여야 합니다. 전용 SQL 풀이 자주 일시 중지되는 경우에는 자동 복원 지점이 만들어지지 않을 수 있으므로 일시 중지하기 전에 사용자 정의 복원 지점을 만들어야 합니다. 서비스에서 자동 복원 지점을 사용하여 복구를 위한 SLA를 유지 관리하므로 사용자는 현재 이러한 복원 지점을 삭제할 수 없습니다.

데이터 웨어하우스의 스냅샷은 하루 종일 생성되어 7일 동안 사용할 수 있는 복원 지점을 만듭니다. 이 보존 기간은 변경할 수 없습니다. 전용 SQL 풀은 8시간 RPO(복구 지점 목표)를 지원합니다. 지난 7일 동안 수행된 스냅샷 중 하나에서 주 지역의 데이터 웨어하우스를 복원할 수 있습니다.

마지막 스냅샷이 시작한 시간을 보려면 온라인 전용 SQL 풀에서 이 쿼리를 실행합니다.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>사용자 정의 복원 지점

해당 기능을 사용하면 스냅샷을 수동으로 트리거하여 대규모 수정 전후의 데이터 웨어하우스 복원 지점을 만들 수 있습니다. 해당 기능은 복원 지점을 논리적으로 일관되게 유지할 수 있도록 하여 작업 중단 또는 사용자 오류가 발생하는 경우 빠른 복구 시간을 위해 추가적인 데이터 보호 기능을 제공합니다. 사용자 정의 복원 지점은 7일 동안 사용할 수 있으며 자동으로 삭제됩니다. 사용자 정의 복원 지점의 보존 기간은 변경할 수 없습니다. 어떤 시점에서든 **42개의 사용자 정의 복원 지점** 만 지원되므로 다른 복원 지점을 만들기 전에 [삭제](/powershell/module/azurerm.sql/remove-azurermsqldatabaserestorepoint)해야 합니다. 사용자 정의 복원 지점은 [PowerShell](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsont#examples) 또는 Azure Portal을 통해 스냅샷을 트리거하여 만들 수 있습니다.

> [!NOTE]
> 7일보다 더 긴 복원 시점이 필요한 경우 [여기서](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points) 이 기능에 대해 투표해 주세요. 또한 사용자 정의 복원 지점을 만들어 새로 만든 복원 지점에서 새 데이터 웨어하우스로 복원할 수 있습니다. 복원되면 전용 SQL 풀이 온라인 상태가 되며, 이를 무기한 일시 중지하여 컴퓨팅 비용을 절감할 수 있습니다. 일시 중지된 데이터베이스에는 Azure Synapse 스토리지 요금으로 스토리지 비용이 부과됩니다. 복원된 데이터 웨어하우스의 활성 복사본이 필요한 경우 몇 분 만에 다시 시작할 수 있습니다.

### <a name="restore-point-retention"></a>복원 지점 보존

다음은 복원 지점 보존 기간에 대한 세부 정보입니다.

1. 전용 SQL 풀은 7일 보존 기간에 도달하는 경우 **및** 최소 42개 총 복원 지점(사용자 정의 및 자동 포함)이 있는 경우 복원 지점을 삭제합니다.
2. 전용 SQL 풀이 일시 중지된 경우 스냅샷이 생성되지 않습니다.
3. SQL 풀이 일시 중지된 경우를 포함하여 복원 지점을 사용하는 시간에서 절대 일수로 복원 지점의 보존 기간을 측정합니다.
4. 전용 SQL 풀은 해당 복원 지점이 7일의 보존 기간에 도달하지 않으면 언제든지 최대 42개 사용자 정의 복원 지점 또는 42개 자동 복원 지점을 저장할 수 있도록 보장합니다.
5. 스냅샷을 생성하는 경우 전용 SQL 풀은 7일 넘게 일시 중지된 다음, 다시 시작됩니다. 총 복원 지점이 42개(사용자 정의 및 자동을 포함)가 될 때까지 복원 지점을 유지합니다.

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>SQL 풀을 드롭할 때의 스냅샷 보존

전용 SQL 풀을 드롭하면 최종 스냅샷이 생성되고 7일 동안 저장됩니다. 삭제 시 생성된 최종 복원 지점으로 전용 SQL 풀을 복원할 수 있습니다. 전용 SQL 풀이 일시 중지된 상태에서 드롭하면 스냅샷이 생성되지 않습니다. 해당 시나리오에서는 전용 SQL 풀을 드롭하기 전에 사용자 정의 복원 지점을 만들어야 합니다.

> [!IMPORTANT]
> 전용 SQL 풀을 호스팅하는 서버/작업 영역을 삭제하면 해당 서버/작업 영역에 속한 모든 데이터베이스도 삭제되고 복구할 수 없습니다. 삭제된 서버는 복원할 수 없습니다.

## <a name="geo-backups-and-disaster-recovery"></a>지역 백업 및 재해 복구

지역 백업은 [쌍을 이루는 데이터 센터](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)에 하루에 한 번 생성됩니다. 지역 복원의 RPO는 24시간입니다. 전용 SQL 풀이 지원되는 다른 지역에 있는 서버로 지역 백업을 복원할 수 있습니다. 지역 백업을 사용하면 주 지역의 복원 지점에 액세스할 수 없는 경우에 데이터 웨어하우스를 복원할 수 있습니다.

전용 SQL 풀에 대한 지역 백업이 필요하지 않은 경우 사용하지 않도록 설정하고 재해 복구 스토리지 비용을 절감할 수 있습니다. 지역 백업을 사용하지 않도록 설정하려면 [방법 가이드: 전용 SQL 풀(이전의 SQL DW)에 대해 지역 백업 비활성화](disable-geo-backup.md)를 참조하세요. 지역 백업을 사용하지 않도록 설정하면 기본 Azure 데이터 센터를 사용할 수 없는 경우 전용 SQL 풀을 쌍을 이루는 Azure 지역으로 복구할 수 없습니다. 

> [!NOTE]
> 지역 백업에 더 짧은 RPO가 필요한 경우 [여기에서](https://feedback.azure.com/forums/307516-sql-data-warehouse) 이 기능에 대해 투표해 주세요. 또한 사용자 정의 복원 지점을 만들어 새로 만든 복원 지점에서 다른 지역의 새 데이터 웨어하우스로 복원할 수 있습니다. 복원한 후에는 데이터 웨어하우스가 온라인 상태 이며 계산 비용을 절약 하기 위해 무기한 일시 중지할 수 있습니다. 일시 중지된 데이터베이스에는 Azure Premium Storage 요금으로 스토리지 비용이 부과됩니다. 더 짧은 복구 지점에 대 한 또 다른 일반적인 패턴은 데이터 웨어하우스의 기본 및 보조 인스턴스로 데이터를 병렬로 수집 하는 것입니다. 이 시나리오에서 데이터는 원본 (또는 원본)에서 수집 데이터 웨어하우스의 두 개의 개별 인스턴스 (기본 및 보조)로 유지 됩니다. 계산 비용을 절약 하기 위해 웨어하우스의 보조 인스턴스를 일시 중지할 수 있습니다. 데이터 웨어하우스의 활성 복사본이 필요한 경우 몇 분만 소요 되는 다시 시작할 수 있습니다.

## <a name="data-residency"></a>데이터 상주 

페어링된 데이터 센터가 사용자의 국가 외부에 있는 경우 LRS(로컬 중복 스토리지)에 데이터베이스를 프로비전하여 해당 지역 내에 데이터가 유지되도록 할 수 있습니다. 데이터베이스가 이미 RA-GRS(지리적 중복 스토리지 읽기 전용, 현재 기본값)에 프로비전된 경우 지역 백업에서 옵트아웃(opt out)할 수 있지만 데이터베이스는 지역 쌍으로 복제되는 스토리지에 계속 남아 있습니다. 고객 데이터가 해당 지역 내에 유지되도록 하기 위해 전용 SQL 풀을 로컬 중복 스토리지로 프로비전하거나 복원할 수 있습니다. 로컬 중복 스토리지를 프로비전하거나 복원하는 방법에 대한 자세한 내용은 [Azure Synapse Analytics에서 전용 SQL 풀(이전의 SQL DW)에 대한 단일 지역 상주를 구성하기 위한 방법 가이드](single-region-residency.md)를 참조하세요.

쌍을 이루는 데이터 센터가 다른 국가에 있는지 확인하려면 [Azure 쌍을 이루는 지역](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)을 참조하세요.

## <a name="backup-and-restore-costs"></a>백업 및 복원 비용

Azure 청구서에는 Storage 및 재해 복구 스토리지에 대한 항목이 있습니다. 스토리지 요금은 스냅샷에서 캡처된 증분 변경과 함께 주 지역에 데이터를 저장하는 데 드는 총비용입니다. 스냅샷 요금 청구 방법에 대한 자세한 내용은 [스냅샷 요금 청구 방법 이해](/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 참조하세요. 지역 중복 요금은 지역 백업을 저장하는 데 드는 비용을 포함합니다.  

주 데이터 웨어하우스 및 7일 간의 스냅샷 변경에 대한 총 비용은 가장 가까운 TB로 반올림하여 계산됩니다. 예를 들어 데이터 웨어하우스가 1.5TB이고 스냅샷에서 100GB를 캡처하면 2TB 데이터에 대한 비용이 Azure Premium Storage 요율로 청구됩니다.

지역 중복 스토리지를 사용하는 경우 별도의 스토리지 비용이 청구됩니다. 지역 중복 스토리지는 표준 RA-GRS(Read-Access Geo Redundant Storage) 요금이 청구됩니다.

Azure Synapse 가격 책정에 대한 자세한 내용은 [Azure Synapse 가격 책정](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)을 참조하세요. 지역 간에 복원하는 경우 데이터 송신 요금이 청구되지 않습니다.

## <a name="restoring-from-restore-points"></a>복원 지점에서 복원

각 스냅샷에서 스냅샷이 시작된 시간을 나타내는 복원 지점을 만듭니다. 데이터 웨어하우스를 복원하려면 복원 지점을 선택하고 복원 명령을 실행합니다.  

복원된 데이터 웨어하우스와 현재 데이터 웨어하우스 중 하나를 유지하거나 삭제할 수 있습니다. 현재 데이터 웨어하우스를 복원된 데이터 웨어하우스로 바꾸려면 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)를 MODIFY NAME 옵션과 함께 사용하여 이름을 변경할 수 있습니다.

데이터 웨어하우스를 복원하려면 [전용 SQL 풀 복원](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal)을 참조하세요.

삭제되거나 일시 중지된 데이터 웨어하우스를 복원하기 위해 [지원 티켓을 만들](sql-data-warehouse-get-started-create-support-ticket.md) 수 있습니다.

## <a name="cross-subscription-restore"></a>구독 간 복원

구독 간에 직접 복원해야 하는 경우 [여기](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore)에서 해당 기능에 투표하세요. 다른 서버로 복원하고 구독 간에 서버를 [‘이동’](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)하여 구독 간 복원을 수행합니다.

## <a name="geo-redundant-restore"></a>지역 중복 복원

선택한 성능 수준에서 전용 SQL 풀을 지원하는 모든 지역으로 [전용 SQL 풀을 복원](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell)할 수 있습니다.

> [!NOTE]
> 지역 중복 복원을 수행하려면 이 기능에서 옵트아웃(opt out)하지 않아야 합니다.

## <a name="next-steps"></a>다음 단계

복원 지점에 대한 자세한 내용은 [사용자 정의 복원 지점](sql-data-warehouse-restore-points.md)을 참조하세요.
