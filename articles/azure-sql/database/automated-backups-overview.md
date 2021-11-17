---
title: 자동 지역 중복 백업
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database 및 Azure SQL Managed Instance는 몇 분마다 로컬 데이터베이스 백업을 자동으로 만들고 Azure 읽기 액세스 지역 중복 스토리지를 사용하여 지리적 중복을 제공합니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: references_regions, devx-track-azurepowershell
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: mathoma, wiassaf, danil
ms.date: 08/28/2021
ms.openlocfilehash: 884ae5d4677f33a4326ead8daae5d74b20d8596c
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555170"
---
# <a name="automated-backups---azure-sql-database--azure-sql-managed-instance"></a>자동화된 백업 - Azure SQL Database 및 Azure SQL Managed Instance

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

> [!div class="nextstepaction"]
> [Azure SQL 개선하기 위한 설문 조사](https://aka.ms/AzureSQLSurveyNov2021)

## <a name="what-is-a-database-backup"></a>데이터베이스 백업이란?

데이터가 손상되거나 삭제되지 않도록 보호하는 데이터베이스 백업은 비즈니스 연속성 및 재해 복구 전략의 필수 요소입니다. 이러한 백업을 사용하면 구성된 보존 기간 내의 특정 시점으로 데이터베이스를 복원할 수 있습니다. 데이터 보호 규칙에서 백업을 오랫동안(최대 10년) 사용할 수 있도록 요구하는 경우 단일 데이터베이스 및 풀링된 데이터베이스에 대해 [장기 보존](long-term-retention-overview.md)을 구성할 수 있습니다.

### <a name="backup-frequency"></a>Backup 주기

SQL Database와 SQL Managed Instance는 SQL Server 기술을 사용하여 일주일마다 [전체 백업](/sql/relational-databases/backup-restore/full-database-backups-sql-server), 12~24시간마다 [차등 백업](/sql/relational-databases/backup-restore/differential-backups-sql-server), 5~10분마다 [트랜잭션 로그 백업](/sql/relational-databases/backup-restore/transaction-log-backups-sql-server)을 만듭니다. 트랜잭션 로그 백업의 빈도는 컴퓨팅 크기와 데이터베이스 작업의 양을 기준으로 합니다.

사용자가 데이터베이스를 복원할 때 서비스에서는 전체, 차등, 트랜잭션 로그 백업 중 무엇을 복원해야 하는지 판단합니다.

### <a name="backup-storage-redundancy"></a>백업 스토리지 중복성

기본적으로 SQL Database 및 SQL Managed Instance는 [쌍을 이루는 지역](../../best-practices-availability-paired-regions.md)에 복제되는 지역 중복 [스토리지 Blob](../../storage/common/storage-redundancy.md)에 데이터를 저장합니다. 지역 중복을 사용하면 주 지역의 백업 스토리지에 영향을 주는 중단으로부터 보호하고 재해 발생 시 서버를 다른 지역에 복원할 수 있습니다. 

백업 스토리지 중복성을 구성하는 옵션은 로컬 중복, 영역 중복 또는 지역 중복 스토리지 Blob 중에 무엇을 사용할 것인지 선택할 수 있는 유연성을 제공합니다. 관리형 인스턴스 또는 SQL 데이터베이스가 배포된 지역 내에 데이터가 유지되도록 하려면 기본 지역 중복 백업 스토리지 중복성을 변경하고 백업에 대해 로컬 중복 또는 영역 중복 스토리지 Blob을 구성하면 됩니다. 스토리지 중복성 메커니즘은 계획된 이벤트 그리고 일시적인 하드웨어 오류, 네트워크 또는 정전, 대규모 자연 재해 등의 계획되지 않은 이벤트로부터 데이터를 보호하기 위해 여러 복사본을 저장합니다. 구성된 백업 스토리지 중복성은 PITR(특정 시점 복원)에 사용되는 단기 백업 보존 설정과 LTR(장기 백업)에 사용되는 장기 보존 백업 모두에 적용됩니다. 

SQL Database의 경우 데이터베이스를 만들 때 백업 스토리지 중복성을 구성하거나 기존 데이터베이스를 업데이트할 수 있습니다. 기존 데이터베이스의 변경 내용은 이후 백업에만 적용됩니다. 기존 데이터베이스의 백업 스토리지 중복성을 업데이트한 후 변경 내용이 적용될 때까지 최대 48시간이 걸릴 수 있습니다. 로컬 또는 영역 중복 스토리지를 사용하도록 데이터베이스를 업데이트하는 즉시 지역 복원이 사용되지 않습니다. 


> [!IMPORTANT]
> 하이퍼스케일 및 SQL Managed Instance에 대한 백업 스토리지 중복성은 데이터베이스를 만드는 중에만 설정할 수 있습니다. 리소스가 프로비저닝되면 이 설정을 수정할 수 없습니다. [데이터베이스 복사](database-copy.md) 프로세스를 사용하여 기존 하이퍼스케일 데이터베이스의 백업 스토리지 중복 설정을 업데이트할 수 있습니다. 

> [!IMPORTANT]
> 영역 중복 스토리지는 현재 [특정 지역](../../storage/common/storage-redundancy.md#zone-redundant-storage)에서만 사용할 수 있습니다. 

> [!NOTE]
> Azure SQL Database에 대해 구성 가능한 백업 스토리지 중복성은 현재 동남 아시아 Azure 지역에서만 일반 공급되고, 모든 Azure 지역에서는 공개 미리 보기로 이용할 수 있습니다. 

### <a name="backup-usage"></a>백업 사용

백업을 사용하여 다음을 수행할 수 있습니다.

- **기존 데이터베이스의 특정 시점 복원** - Azure Portal, Azure PowerShell, Azure CLI 또는 REST API를 사용하여 [기존 데이터베이스를 보존 기간 내의 특정 시점으로 복원](recovery-using-backups.md#point-in-time-restore)합니다. SQL Database의 경우 이 작업은 원본 데이터베이스와 동일한 서버에 새 데이터베이스를 만들지만, 원본 데이터베이스를 덮어쓰지 않도록 다른 이름을 사용합니다. 복원이 완료된 후 원본 데이터베이스를 삭제해도 됩니다. 또는 원래 데이터베이스의 [이름을 바꾼](/sql/relational-databases/databases/rename-a-database) 다음, 복원된 데이터베이스의 이름을 원래 데이터베이스 이름으로 바꿀 수 있습니다. 마찬가지로 SQL Managed Instance의 경우 이 작업은 동일한 구독 및 동일한 지역에 있는 같은 관리형 인스턴스 또는 다른 관리형 인스턴스에 데이터베이스 복사본을 만듭니다.
- **삭제된 데이터베이스의 특정 시점 복원** - [삭제된 데이터베이스를 삭제 시점으로 복원](recovery-using-backups.md#deleted-database-restore)하거나 보존 기간 내의 특정 시점으로 복원합니다. 삭제된 데이터베이스는 원래 데이터베이스가 생성되었던 동일한 서버 또는 관리형 인스턴스에만 복원할 수 있습니다. 데이터베이스를 삭제할 때, 서비스에서는 데이터 손실을 방지하기 위해 삭제 전에 최종 트랜잭션 로그 백업을 수행합니다.
- **지역 복원** - [데이터베이스를 다른 지리적 지역으로 복원합니다](recovery-using-backups.md#geo-restore). 주 지역의 서버 및 데이터베이스에 액세스할 수 없는 경우 지역 복원을 통해 지리적 재해로부터 데이터베이스를 복구할 수 있습니다. 지역 복원은 Azure 지역의 기존 서버 또는 관리형 인스턴스에 새 데이터베이스를 만듭니다.
   > [!IMPORTANT]
   > 지역 복원은 지역 중복 백업 스토리지가 구성된 SQL 데이터베이스 또는 관리형 인스턴스에만 사용할 수 있습니다.
- **장기 백업에서 복원** - 데이터베이스에 LTR(장기 보존 정책)을 구성한 경우 단일 데이터베이스 또는 풀링된 데이터베이스의 [특정 장기 백업에서 데이터베이스를 복원](long-term-retention-overview.md)합니다. LTR을 사용하면 Azure Portal, Azure CLI 또는 Azure PowerShell 사용하여 [이전 버전의 데이터베이스를 복원하여](long-term-backup-retention-configure.md) 규정 준수 요청을 충족하거나 이전 버전의 애플리케이션을 실행할 수 있습니다. 자세한 내용은 [장기 보존](long-term-retention-overview.md)을 참조하세요.

> [!NOTE]
> Azure Storage에서 *복제* 라는 용어는 한 위치에서 다른 위치로 Blob을 복사하는 것을 말합니다. SQL에서 *데이터베이스 복제* 는 여러 보조 데이터베이스를 주 데이터베이스와 동기화된 상태로 유지하는 데 사용되는 다양한 기술을 말합니다.

### <a name="restore-capabilities-and-features-of-azure-sql-database-and-azure-sql-managed-instance"></a><a id="restore-capabilities"></a>Azure SQL Database 및 Azure SQL Managed Instance의 기능 복원

이 표에는 [PITR(특정 시점 복원)](recovery-using-backups.md#point-in-time-restore), [지역 복원](recovery-using-backups.md#geo-restore) 및 [장기 보존 백업](long-term-retention-overview.md)의 기능과 특징이 요약되어 있습니다.

| **백업 속성** | PITR(특정 시점 복원) | 지역 복원 | 장기 백업 복원 |           
|----|--|--|--|
| **SQL 백업 유형** | 전체, 차등, 로그 | PITR 백업의 복제된 복사본 | 전체 백업만 | 
| **RPO(복구 지점 목표)** . |  컴퓨팅 크기 및 데이터베이스 작업량에 따라 5~10분. | 지역 복제에 따라 최대 1시간.\*  |  일주일(또는 사용자 정책).|
| **RTO(복구 시간 목표)** . | 복원에는 일반적으로 12시간 미만이 소요되지만 크기 및 활동에 따라 더 오래 걸릴 수 있습니다. [복구](recovery-using-backups.md#recovery-time)를 참조하세요. | 복원에는 일반적으로 12시간 미만이 소요되지만 크기 및 활동에 따라 더 오래 걸릴 수 있습니다. [복구](recovery-using-backups.md#recovery-time)를 참조하세요. | 복원에는 일반적으로 12시간 미만이 소요되지만 크기 및 활동에 따라 더 오래 걸릴 수 있습니다. [복구](recovery-using-backups.md#recovery-time)를 참조하세요. | 
| **보존** | 기본 7일, 최대 35일 |  기본적으로 사용, 소스와 동일.\*\* | 기본적으로 사용 안 함, 최대 10년 보존 |     
| **Azure Storage**  | 기본적으로 지역 중복 선택적으로 영역 또는 로컬 중복 스토리지를 구성할 수 있습니다. | PITR 백업 스토리지 중복이 지역 중복으로 설정된 경우 사용할 수 있습니다. PITR 백업 저장소가 영역 또는 로컬 중복 스토리지인 경우 사용할 수 없습니다. | 기본적으로 지역 중복 영역 또는 로컬 중복 스토리지를 구성할 수 있습니다. | 
| **동일한 지역에 새 데이터베이스를 만드는 데 사용** | 지원 여부 | 지원됨 | 지원 여부 |
| **다른 지역에 새 데이터베이스를 만드는 데 사용** | 지원되지 않음 | 모든 Azure 지역에서 지원됨 | 모든 Azure 지역에서 지원됨 |
| **다른 구독에서 새 데이터베이스를 만드는 데 사용** |  지원되지 않음  |  지원되지 않음\*\*\* | 지원되지 않음\*\*\*  | 
| **Azure Portal을 통한 복원**|예|예|예|
| **PowerShell을 통한 복원** |예|예|예| 
| **Azure CLI를 통한 복원** |예|예|예| 
| | | | |

\* 대규모 데이터베이스가 필요하고 비즈니스 연속성을 보장해야 하는 업무상 중요한 애플리케이션의 경우 [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md)을 사용합니다. 

\*\* 모든 PITR 백업은 기본적으로 지역 중복 스토리지에 저장됩니다. 따라서 지역 복원은 기본적으로 사용하도록 설정되어 있습니다. 

\*\*\* 해결 방법은 새 서버로 복원하고 리소스 이동을 사용하여 서버를 다른 구독으로 이동하는 것입니다.

### <a name="restoring-a-database-from-backups"></a>백업에서 데이터베이스 복원 

복원을 수행하려면 [백업에서 데이터베이스 복원](recovery-using-backups.md)을 참조하세요. 다음 예제를 사용하여 백업 구성 및 복원 작업을 시도해 볼 수 있습니다.

| 작업 | Azure portal | Azure CLI | Azure PowerShell |
|---|---|---|---|
| **백업 보존 변경** | [SQL 데이터베이스](#change-the-short-term-retention-policy-using-the-azure-portal) <br/> [SQL Managed Instance](#change-the-short-term-retention-policy-using-the-azure-portal) | [SQL 데이터베이스](#change-the-short-term-retention-policy-using-azure-cli) <br/> [SQL Managed Instance](#change-the-short-term-retention-policy-using-azure-cli) | [SQL 데이터베이스](#change-the-short-term-retention-policy-using-powershell) <br/>[SQL Managed Instance](#change-the-short-term-retention-policy-using-powershell) |
| **장기 백업 보존 변경** | [SQL 데이터베이스](long-term-backup-retention-configure.md#create-long-term-retention-policies)<br/> [SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md) | [SQL 데이터베이스](long-term-backup-retention-configure.md) <br/> [SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md) | [SQL 데이터베이스](long-term-backup-retention-configure.md)<br/>[SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md)  |
| **지정 시간에서 데이터베이스 복원** | [SQL 데이터베이스](recovery-using-backups.md#point-in-time-restore)<br>[SQL Managed Instance](../managed-instance/point-in-time-restore.md) | [SQL 데이터베이스](/cli/azure/sql/db#az_sql_db_restore) <br/> [SQL Managed Instance](/cli/azure/sql/midb#az_sql_midb_restore) | [SQL 데이터베이스](/powershell/module/az.sql/restore-azsqldatabase) <br/> [SQL Managed Instance](/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| **삭제된 데이터베이스 복원** | [SQL 데이터베이스](recovery-using-backups.md)<br>[SQL Managed Instance](../managed-instance/point-in-time-restore.md#restore-a-deleted-database) | [SQL 데이터베이스](long-term-backup-retention-configure.md#restore-from-ltr-backups) <br/> [SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md#restore-from-ltr-backups) | [SQL 데이터베이스](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [SQL Managed Instance](/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| **Azure Blob Storage에서 데이터베이스 복원** |  |  | <br/>[SQL Managed Instance](../managed-instance/restore-sample-database-quickstart.md) |

## <a name="backup-scheduling"></a>백업 일정 예약

새 데이터베이스가 만들어지거나 복원되는 즉시 첫 번째 전체 백업이 예약됩니다. 이 백업은 일반적으로 30분 이내에 완료되지만 데이터베이스의 크기가 큰 경우에는 더 오래 걸릴 수 있습니다. 예를 들어 복원된 데이터베이스 또는 데이터베이스 복사본은 일반적으로 새 데이터베이스보다 크기 때문에 초기 백업이 더 오래 걸릴 수 있습니다. 첫 번째 전체 백업 후, 모든 향후 백업이 자동으로 예약 및 관리됩니다. 모든 데이터베이스 백업의 정확한 타이밍은 전반적인 시스템 워크로드의 균형을 조정하는 SQL Database 또는 SQL Managed Instance 서비스에 의해 결정됩니다. 백업 작업의 일정을 변경하거나 사용하지 않도록 설정할 수 없습니다.

> [!IMPORTANT]
> 새 데이터베이스, 복원된 데이터베이스 또는 복사된 데이터베이스의 경우 초기 전체 백업 후에 초기 트랜잭션 로그 백업이 생성된 시점부터 특정 시점 복원 기능을 사용할 수 있게 됩니다.

## <a name="backup-storage-consumption"></a>백업 스토리지 사용량

SQL Server 백업 및 복원 기술을 사용하여 데이터베이스를 특정 시점으로 복원하려면 하나의 전체 백업, 하나의 차등 백업(선택 사항), 하나 이상의 트랜잭션 로그 백업으로 구성된 중단 없는 백업 체인이 필요합니다. SQL Database 및 SQL Managed Instance 백업 일정에는 매주 1회의 전체 백업이 포함됩니다. 따라서 전체 보존 기간 내에 PITR을 제공하려면 구성된 보존 기간보다 최대 일주일이 더 긴 전체, 차등 및 트랜잭션 로그 백업을 시스템에서 추가로 저장해야 합니다. 

즉, 보존 기간의 특정 시점에는 보존 기간의 가장 오래된 시간보다 오래된 전체 백업이 있어야 하고, 다음 전체 백업 전까지는 해당 전체 백업의 중단 없는 차등 백업 및 트랜잭션 로그 백업 체인이 있어야 합니다.

> [!NOTE]
> PITR을 제공하기 위해 추가 백업은 구성된 보존 기간보다 최대 일주일 더 오래 저장됩니다. 백업 스토리지는 모든 백업과 동일한 요금이 청구됩니다. 

PITR 기능을 제공하는 데 더 이상 필요 없는 백업은 자동으로 삭제됩니다. 차등 백업 및 로그 백업은 이전 전체 백업을 복원할 수 있어야 하므로 세 가지 백업 유형이 주간 세트에서 함께 제거됩니다.

[TDE 암호화](transparent-data-encryption-tde-overview.md) 데이터베이스가 포함된 모든 데이터베이스의 경우 백업 스토리지 압축 및 비용을 줄이기 위해 백업이 압축됩니다. 평균 백업 압축 비율은 3-4회입니다. 하지만 데이터의 특성과 데이터베이스에서 데이터 압축 사용 여부에 따라 훨씬 낮거나 훨씬 높을 수 있습니다.

SQL Database 및 SQL Managed Instance는 사용한 총 백업 스토리지를 누적 값으로 계산합니다. 매시간 이 값은 Azure 청구 파이프라인에 보고되며, 매시간 사용량을 집계하여 각 월말에 사용량을 계산하는 데 사용됩니다. 데이터베이스가 삭제된 후 백업이 만료되어 삭제되면 사용량이 감소합니다. 모든 백업이 삭제되고 PITR이 더 이상 불가능한 경우 청구가 중지됩니다.
   
> [!IMPORTANT]
> 데이터베이스가 삭제되어도 PITR을 제공하기 위해 데이터베이스 백업이 유지됩니다. 데이터베이스를 삭제하고 다시 만들면 스토리지 및 컴퓨팅 비용이 절감될 수 있지만, 데이터베이스가 삭제될 때마다 시스템에서는 삭제된 각 데이터베이스의 백업을 유지하므로 백업 스토리지 비용이 상승할 수 있습니다. 

### <a name="monitor-consumption"></a>사용량 모니터링

vCore 데이터베이스의 경우 각 백업 유형(전체, 차등 및 로그)에서 사용하는 스토리지가 데이터베이스 모니터링 창에 별도의 메트릭으로 보고됩니다. 다음 다이어그램에서는 단일 데이터베이스에 대한 백업 스토리지 사용량을 모니터링하는 방법을 보여 줍니다. 이 기능은 현재 관리형 인스턴스에 사용할 수 없습니다.

![Azure Portal에서 데이터베이스 백업 사용량 모니터링](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>백업 스토리지 사용량 미세 조정

데이터베이스의 최대 데이터 크기까지는 백업 스토리지 사용 요금이 부과되지 않습니다. 백업 스토리지 초과 사용량은 개별 데이터베이스의 워크로드 및 최대 크기에 따라 달라집니다. 백업 스토리지 사용량을 줄이기 위해 다음과 같은 몇 가지 튜닝 기법을 고려합니다.

- [백업 보존 기간](#change-the-short-term-retention-policy-using-the-azure-portal)을 사용자 요구에 따라 가능한 최소 기간으로 줄입니다.
- 인덱스 다시 빌드와 같은 대량 쓰기 작업은 수행하지 않는 것이 좋습니다.
- 대량 데이터 로드 작업의 경우에는 [클러스터형 columnstore 인덱스](/sql/relational-databases/indexes/columnstore-indexes-overview) 및 관련 [모범 사례](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance)를 사용하고/사용하거나 비클러스터형 인덱스의 수를 줄이는 것이 좋습니다.
- 범용 서비스 계층에서 프로비전된 데이터 스토리지는 백업 스토리지의 가격보다 저렴합니다. 초과 백업 스토리지 비용이 지속적으로 많이 발생하는 경우 데이터 스토리지를 늘려서 백업 스토리지를 절약할 수 있습니다.
- 임시 결과 및/또는 임시 데이터를 저장하기 위해 애플리케이션 논리에서 영구 테이블 대신 TempDB를 사용합니다.
- 되도록이면 로컬 중복 백업 스토리지를 사용합니다(예: 개발/테스트 환경).

## <a name="backup-retention"></a>백업 보존

Azure SQL Database 및 Azure SQL Managed Instance 단기 및 장기 백업 보존을 모두 제공합니다. 단기 보존 백업은 데이터베이스에 대한 보존 기간과 PITR(Point-In-Time-Restore)을 허용하는 반면 장기 보존은 다양한 규정 준수 요구 사항에 대한 백업을 제공합니다.  

### <a name="short-term-retention"></a>단기 보존

모든 새 데이터베이스, 복원된 데이터베이스 및 복사된 데이터베이스의 경우 Azure SQL Database 및 Azure SQL Managed Instance는 기본적으로 최근 7일 내에 PITR을 허용하기 위해 충분한 백업을 유지합니다. 데이터베이스 또는 관리되는 인스턴스에 대해 정의된 보존 기간 내의 모든 지정 시간으로 데이터베이스를 복원할 수 있도록 정기적으로 전체, 차등 및 로그 백업이 수행됩니다. 또한 Azure SQL 데이터베이스의 경우 차등 백업을 12시간 또는 24시간 빈도로 구성할 수 있습니다. 

> [!NOTE]
> 24시간 차등 백업 빈도로 데이터베이스를 복원하는 데 필요한 시간이 늘어날 수 있습니다. 

하이퍼스케일 및 기본 계층 데이터베이스를 제외하고, 각 활성 데이터베이스의 [백업 보존 기간을 1-35일 사이에서 변경](#change-the-short-term-retention-policy)할 수 있습니다. [백업 스토리지 사용량](#backup-storage-consumption)에 설명된 것처럼 PITR을 사용하기 위해 저장된 백업이 보존 기간보다 오래되었을 수 있습니다. Azure SQL Managed Instance만 사용하는 경우 데이터베이스가 0-35일 범위에서 삭제되면 PITR 백업 보존율을 설정할 수 있습니다. 

데이터베이스를 삭제하면 시스템에서는 해당 보존 기간을 사용하여 온라인 데이터베이스와 동일한 방식으로 백업을 유지합니다. 삭제된 데이터베이스의 백업 보존 기간은 변경할 수 없습니다.

> [!IMPORTANT]
> 서버 또는 관리형 인스턴스를 삭제하면 해당 서버 또는 관리형 인스턴스의 모든 데이터베이스도 삭제되며 복구할 수 없습니다. 삭제된 서버 또는 관리형 인스턴스는 복원할 수 없습니다. 그러나 데이터베이스 또는 관리형 인스턴스에 대해 LTR(장기 보존)을 구성한 경우에는 장기 보존 백업이 삭제되지 않으며, 동일한 구독의 다른 서버 또는 관리형 인스턴스의 데이터베이스를 장기 보존 백업이 수행된 시점으로 복원하는 데 사용할 수 있습니다.

최근 1-35일 이내에 PITR 용도로 수행된 백업 보존을 단기 백업 보존이라고도 합니다. 백업을 최대 단기 보존 기간인 35일보다 오래 유지해야 하는 경우 [장기 보존](long-term-retention-overview.md)을 사용하면 됩니다.

### <a name="long-term-retention"></a>장기 보존

SQL Database 및 SQL Managed Instance 모두 Azure Blob 스토리지에서 최대 10년 동안 전체 백업 LTR(장기 보존)을 구성할 수 있습니다. LTR 정책을 구성한 후에는 매주 전체 백업이 다른 스토리지 컨테이너에 자동으로 복사됩니다. 다양한 규정 준수 요구 사항을 충족하기 위해 주간, 월간 및/또는 연간 전체 백업에 대해 다른 보존 기간을 선택할 수 있습니다. 스토리지 사용량은 선택한 LTR 백업 빈도 및 보존 기간에 따라 달라집니다. [LTR 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=sql-database)를 사용하여 LTR 스토리지 비용을 추정할 수 있습니다.

> [!IMPORTANT]
> 기존 Azure SQL Database의 백업 스토리지 중복성 업데이트는 데이터베이스에 대해 수행되는 이후 백업에만 적용됩니다. 데이터베이스의 모든 기존 LTR 백업은 기존 스토리지 Blob에 계속 상주하고, 새 백업은 요청된 스토리지 Blob 유형에 저장됩니다. 

LTR에 대한 자세한 내용은 [장기 백업 보존](long-term-retention-overview.md)을 참조하세요.

## <a name="backup-storage-costs"></a>백업 스토리지 비용

백업 스토리지 가격은 구매 모델(DTU 또는 vCore), 선택한 백업 스토리지 중복성 옵션 및 지역에 따라 달라집니다. 백업 스토리지는 월간 사용량(GB/월) 기준으로 청구됩니다. 가격 책정 정보는 [Azure SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/) 및 [Azure SQL Managed Instance 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/single/) 페이지를 참조하세요. 

구매 모델에 대한 자세한 내용은 [vCore 및 DTU 구매 모델 중에서 선택](purchasing-models.md)을 참조하세요.

> [!NOTE]
> Azure 청구서에는 전체 백업 스토리지 사용량이 아닌 백업 스토리지 초과 사용량만 표시됩니다. 예를 들어 데이터 스토리지 4TB를 프로비전할 경우 무료 백업 스토리지 공간은 4TB입니다. 이 상황에서 총 5.8TB의 백업 스토리지 공간을 사용하면 Azure 청구서에는 백업 스토리지 초과 사용량만 표시되므로 1.8TB만 표시됩니다.

### <a name="dtu-model"></a>DTU 모델

DTU 모델에서는 데이터베이스 및 탄력적 풀의 백업 스토리지에 대한 추가 요금이 부과되지 않습니다. 백업 스토리지 가격은 데이터베이스 또는 풀 가격에 포함되어 있습니다.

### <a name="vcore-model"></a>vCore 모델

SQL Database의 단일 데이터베이스에는 데이터베이스의 최대 데이터 스토리지 크기의 100%에 해당하는 백업 스토리지 용량이 추가 요금 없이 제공됩니다. 탄력적 풀 및 관리형 인스턴스의 경우 각각 풀의 최대 데이터 스토리지 또는 최대 인스턴스 스토리지 크기의 100%에 해당하는 백업 스토리지 용량이 추가 요금 없이 제공됩니다. 

단일 데이터베이스의 경우 이 수식은 청구 가능한 총 백업 스토리지 사용량을 계산하는 데 사용됩니다.

`Total billable backup storage size = (size of full backups + size of differential backups + size of log backups) – maximum data storage`

풀링된 데이터베이스의 경우 청구 가능한 총 백업 스토리지 크기는 풀 수준에서 집계되며 다음과 같이 계산됩니다.

`Total billable backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - maximum pool data storage`

관리형 인스턴스의 경우 청구 가능한 총 백업 스토리지 크기는 인스턴스 수준에서 집계되며 다음과 같이 계산됩니다.

`Total billable backup storage size = (total size of full backups + total size of differential backups + total size of log backups) – maximum instance data storage`

청구 가능한 총 백업 스토리지(있는 경우)는 사용하는 백업 스토리지 중복성의 요율에 따라 GB/월 단위로 요금이 청구됩니다. 이 백업 스토리지 사용량은 개별 데이터베이스, 탄력적 풀 및 관리형 인스턴스의 워크로드 및 크기에 따라 달라집니다. 백업의 크기는 변경된 데이터 양에 비례하므로 자주 수정되는 데이터베이스는 차등 및 로그 백업의 크기가 큽니다. 따라서 이러한 데이터베이스는 백업 비용이 더 많이 듭니다.

SQL Database 및 SQL Managed Instance는 청구 가능한 총 백업 스토리지를 모든 백업 파일의 누적 값으로 계산합니다. 매시간 이 값은 Azure 청구 파이프라인에 보고되고, Azure 청구 파이프라인에서는 이 시간당 사용량을 집계하여 월말에 백업 스토리지 사용량을 산정합니다. 데이터베이스가 삭제되면 오래된 백업이 만료되어 삭제되므로 백업 스토리지 사용량이 점차 감소합니다. 차등 백업 및 로그 백업은 이전 전체 백업을 복원할 수 있어야 하므로 세 가지 백업 유형이 주간 세트에서 함께 제거됩니다. 모든 백업이 삭제되면 청구가 중지됩니다. 

간단한 예로, 데이터베이스의 누적 백업 스토리지가 744GB이고 데이터베이스가 완전히 유휴 상태이기 때문에 이 양이 한 달 내내 그대로 유지된다고 가정하겠습니다. 이 누적 스토리지 사용량을 시간당 사용량으로 변환하려면 744(매월 31일 X 매일 24시간)로 나눕니다. SQL Database는 데이터베이스가 시간당 1GB의 일정한 속도로 PITR 백업을 사용했다고 Azure 청구 파이프라인에 보고합니다. Azure 청구는 이 사용량을 집계하고 전체 월에 대해 744GB의 사용량을 표시합니다. 비용은 해당 지역의 양/GB/월 요금에 따라 결정됩니다.

이제 좀 더 복잡한 예를 들겠습니다. 동일한 유휴 데이터베이스의 보존 기간이 월 중간에 7일에서 14일로 증가했다고 가정해보겠습니다. 이와 같은 증가로 인해 총 백업 스토리지가 2배로 늘어나서 1,488GB가 됩니다. SQL Database는 1~372시간(해당 월의 앞쪽 절반)의 사용량이 1GB라고 보고합니다. 373~744(해당 월의 뒤쪽 절반)의 사용량은 2GB라고 보고합니다. 이 사용량은 최종 청구 1,116GB/월로 집계됩니다.

실제 백업 청구 시나리오는 더 복잡합니다. 데이터베이스의 변동률은 워크로드에 따라 다르고 시간이 지나면서 달라지므로, 각 차등 및 로그 백업의 크기가 달라질 뿐 아니라 백업 스토리지 사용량도 그에 따라 변합니다. 뿐만 아니라 각 차등 백업에는 마지막 전체 백업 이후 데이터베이스에서 수행된 모든 변경 내용이 포함되어 있으므로, 모든 차등 백업의 총 크기는 일주일 동안 점진적으로 증가하다가 오래된 전체, 차등 및 로그 백업의 보존 기간이 만료되면 급격히 감소합니다. 예를 들어 전체 백업이 완료된 직후 인덱스 다시 작성처럼 쓰기 집약적인 작업이 실행되면 인덱스 다시 작성을 통해 수행된 수정 내용은 다시 작성하는 동안 수행되는 트랜잭션 로그 백업, 다음 차등 백업, 다음 전체 백업이 발생할 때까지 수행되는 모든 차등 백업에 포함됩니다. 대규모 데이터베이스의 두 번째 시나리오에서는 차등 백업이 지나치게 커지는 경우 서비스에서 최적화를 통해 차등 백업 대신 전체 백업을 만듭니다. 이렇게 하면 다음 전체 백업이 수행될 때까지 모든 차등 백업의 크기가 줄어듭니다.

[사용량 모니터링](#monitor-consumption)에 설명된 것처럼 시간에 따른 각 백업 유형(전체, 차등, 트랜잭션 로그)의 총 백업 스토리지 사용량을 모니터링할 수 있습니다.

### <a name="backup-storage-redundancy"></a>백업 스토리지 중복성

백업 스토리지 중복성은 다음과 같은 방식으로 백업 비용에 영향을 줍니다.
- 로컬 중복 가격 = x
- 영역 중복 가격 = 1.25x
- 지역 중복 가격 = 2x

백업 스토리지 가격 책정에 대한 자세한 내용은 [Azure SQL Database 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/single/) 및 [Azure SQL Managed Instance 가격 책정 페이지](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)를 참조하세요.

> [!IMPORTANT]
> SQL 관리형 인스턴스에 대해 구성 가능한 백업 스토리지 중복성은 모든 Azure 지역에서 사용할 수 있고, SQL Database에 대해 구성 가능한 백업 스토리지 중복성은 현재 동남 아시아 Azure 지역에서만 사용할 수 있습니다. Managed Instance의 경우 관리형 인스턴스를 만드는 프로세스 중에만 지정할 수 있습니다. 리소스가 프로비전된 후에는 백업 스토리지 중복성 옵션을 변경할 수 없습니다.

### <a name="monitor-costs"></a>비용 모니터링

백업 스토리지 비용을 이해하려면 Azure Portal의 **비용 관리 + 청구** 로 이동하여 **비용 관리** 를 선택한 다음, **비용 분석** 을 선택합니다. 원하는 구독을 **범위** 로 선택하고 관심 있는 기간 및 서비스를 필터링합니다.

**서비스 이름** 에 대한 필터를 추가한 다음, 드롭다운 목록에서 **SQL 데이터베이스** 를 선택합니다. **미터 하위 범주** 필터를 사용하여 서비스에 대한 청구 카운터를 선택합니다. 단일 데이터베이스 또는 탄력적 풀의 경우 **단일/탄력적 풀 PITR 백업 스토리지** 를 선택합니다. 관리형 인스턴스의 경우 **mi PITR 백업 스토리지** 를 선택합니다. **스토리지** 및 **계산** 하위 범주에도 관심이 갈지 모르지만, 이 둘은 백업 스토리지 비용과는 관련이 없습니다.

![백업 스토리지 비용 분석](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

  >[!NOTE]
  > 미터는 현재 사용 중인 카운터에 대해서만 표시됩니다. 카운터를 사용할 수 없는 경우 범주를 현재 사용하고 있지 않을 가능성이 높습니다. 예를 들어 관리형 인스턴스가 배포되지 않은 고객에게는 관리형 인스턴스 카운터가 표시되지 않습니다. 마찬가지로 스토리지를 사용하지 않는 리소스에 대한 스토리지 카운터는 표시되지 않습니다. 

자세한 내용은 [Azure SQL Database 비용 관리](cost-management.md)를 참조하세요.

## <a name="encrypted-backups"></a>암호화된 백업

데이터베이스가 TDE를 사용하여 암호화된 경우 LTR 백업을 포함한 백업이 미사용 시 자동으로 암호화됩니다. Azure SQL의 모든 새 데이터베이스는 기본적으로 TDE를 사용하도록 구성됩니다. TDE에 대한 자세한 내용은 [SQL Database 및 SQL Managed Instance를 사용한 투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)를 참조하세요.

## <a name="backup-integrity"></a>백업 무결성

주기적으로 Azure SQL 엔지니어링 팀은 자동화된 데이터베이스 백업의 복원을 자동으로 테스트합니다. (이 테스트는 현재 SQL Managed Instance에서 사용할 수 없습니다. 워크로드에 따라 예약된 SQL Managed Instance의 데이터베이스에서 DBCC CHECKDB를 예약해야 합니다.)

지정 시간 복원 시 데이터베이스도 DBCC CHECKDB 무결성 검사를 받습니다.

무결성 검사 중에 문제가 발견되면 해당 경고를 엔지니어링 팀에 알려줍니다. 자세한 내용은 [SQL Database의 데이터 무결성](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)을 참조하세요.

모든 데이터베이스 백업은 추가 백업 무결성을 제공하는 CHECKSUM 옵션을 통해 수행됩니다.

## <a name="compliance"></a>규정 준수

DTU 기반 서비스 계층에서 vCore 기반 서비스 계층으로 데이터베이스를 마이그레이션하는 경우 애플리케이션의 데이터 복구 정책이 손상되지 않도록 PITR 보존이 유지됩니다. 기본 보존 기간이 규정 준수 요구 사항을 충족하지 못하는 경우 PITR 보존 기간을 변경할 수 있습니다. 자세한 내용은 [PITR 백업 보존 기간 변경](#change-the-short-term-retention-policy)을 참조하세요.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-short-term-retention-policy"></a>단기 보존 정책 변경

Azure Portal, PowerShell 또는 REST API를 사용 하 여 기본 PITR 백업 보존 기간 및 차등 백업 빈도를 변경할 수 있습니다. 다음 예에서는 PITR 보존을 28 일로 변경 하는 방법과 차등 백업을 24 시간 간격으로 변경 하는 방법을 보여 줍니다.

> [!WARNING]
> 현재 보존 기간을 줄이면 새 보존 기간보다 오래된 시점으로 복원할 수 없게 됩니다. 새 보존 기간 내에 PITR을 제공하는 데 더 이상 필요 없는 백업은 삭제됩니다. 현재 보존 기간을 늘려도 새 보존 기간의 더 오래된 시점으로 복원하는 기능이 즉시 제공되지는 않습니다. 시간이 지나 시스템이 백업을 장기간 보존하기 시작하면 이 기능을 사용할 수 있습니다.

> [!NOTE]
> 이러한 API는 PITR 보존 기간에만 영향을 줍니다. 데이터베이스의 LTR을 구성한 경우에는 LTR이 영향을 받지 않습니다. LTR 보존 기간을 변경하는 방법에 대한 내용은 [장기 보존](long-term-retention-overview.md)을 참조하세요.

### <a name="change-the-short-term-retention-policy-using-the-azure-portal"></a>Azure Portal를 사용 하 여 단기 보존 정책 변경

Azure Portal를 사용 하 여 활성 데이터베이스에 대해 PITR 백업 보존 기간 또는 차등 백업 빈도를 변경 하려면 보존 기간을 변경 하려는 데이터베이스가 있는 서버 또는 관리 되는 인스턴스로 이동 합니다. 왼쪽 창에서 **백업** 을 선택하고 **보존 정책** 탭을 선택한 다음, PITR 백업 보존을 변경할 데이터베이스를 선택합니다. 그런 다음, 작업 모음에서 **보존 구성** 을 선택합니다.

#### <a name="sql-database"></a>[SQL 데이터베이스](#tab/single-database)

![PITR 보존 변경, 서버 수준](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

![PITR 보존 변경, 관리되는 인스턴스](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-short-term-retention-policy-using-azure-cli"></a>Azure CLI를 사용 하 여 단기 보존 정책 변경

Azure CLI에 대한 환경을 준비합니다.

[!INCLUDE[azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

#### <a name="sql-database"></a>[SQL 데이터베이스](#tab/single-database)

다음 예제를 사용 하 여 활성 Azure SQL 데이터베이스에 대 한 PITR 백업 보존 및 차등 백업 빈도를 변경 합니다.

```azurecli
# Set new PITR differential backup frequency on an active individual database
# Valid backup retention must be between 1 and 35 days
# Valid differential backup frequency must be ether 12 or 24
az sql db str-policy set \
    --resource-group myresourcegroup \
    --server myserver \
    --name mydb \
    --retention-days 28 \
    --diffbackup-hours 24
```

#### <a name="sql-database"></a>[SQL 데이터베이스](#tab/managed-instance)

다음 예를 사용 하 여 SQL Managed Instance에서 **단일 활성** 데이터베이스의 백업 보존 PITR을 변경할 수 있습니다.

```azurecli
# Set new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
az sql midb short-term-retention-policy set \
    --resource-group myresourcegroup \
    --managed-instance myinstance \
    --name mymanageddb \
    --retention-days 1 \
```

다음 예를 사용 하 여 SQL Managed Instance의 **모든 활성** 데이터베이스에 대해 PITR 백업 보존 기간을 변경할 수 있습니다.

```azurecli
# Set new PITR backup retention period for ALL active databases
# Valid backup retention must be between 1 and 35 days
az sql midb short-term-retention-policy set \
    --resource-group myresourcegroup \
    --managed-instance myinstance \
    --retention-days 1 \
```

---

### <a name="change-the-short-term-retention-policy-using-powershell"></a>PowerShell을 사용 하 여 단기 보존 정책 변경

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell AzureRM 모듈은 여전히 SQL Database와 SQL Managed Instance에서 지원되지만 모든 향후 개발은 Az.Sql 모듈을 위한 것입니다. 자세한 내용은 [AzureRM.SQL](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈의 명령에 대한 인수는 AzureRm 모듈의 인수와 실질적으로 동일합니다.

#### <a name="sql-database"></a>[SQL 데이터베이스](#tab/single-database)

활성 Azure SQL 데이터베이스에 대 한 PITR 백업 보존 및 차등 백업 빈도를 변경 하려면 다음 PowerShell 예제를 사용 합니다.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

```powershell
# SET new PITR differential backup frequency on an active individual database
# Valid differential backup frequency must be ether 12 or 24. 
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28 -DiffBackupIntervalInHours 24
```

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

SQL Managed Instance의 **단일 활성** 데이터베이스에 대한 PITR 백업 보존을 변경하려면 다음 PowerShell 예를 사용합니다.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase -RetentionDays 1
```

SQL Managed Instance의 **모든 활성** 데이터베이스의 PITR 백업 보존을 변경하려면 다음 PowerShell 예를 사용합니다.

```powershell
# SET new PITR backup retention period for ALL active databases
# Valid backup retention must be between 1 and 35 days
Get-AzSqlInstanceDatabase -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 1
```

SQL Managed Instance의 **삭제된 단일** 데이터베이스의 PITR 백업 보존을 변경하려면 다음 PowerShell 예를 사용합니다.
 
```powershell
# SET new PITR backup retention on an individual deleted database
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database.
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

SQL Managed Instance의 **삭제된 모든** 데이터베이스의 PITR 백업 보존을 변경하려면 다음 PowerShell 예를 사용합니다.

```powershell
# SET new PITR backup retention for ALL deleted databases
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

보존 기간이 0일이면 백업이 즉시 삭제되고 삭제된 데이터베이스에 대해 더 이상 유지되지 않습니다.
삭제된 데이터베이스의 PITR 백업 보존을 줄인 후에는 다시 늘릴 수 없습니다.

---

### <a name="change-the-short-term-retention-policy-using-the-rest-api"></a>REST API를 사용 하 여 단기 보존 정책 변경

아래 요청은 보존 기간을 28 일로 업데이트 하 고 차등 백업 빈도를 24 시간으로 설정 합니다.


#### <a name="sql-database"></a>[SQL 데이터베이스](#tab/single-database)

#### <a name="sample-request"></a>샘플 요청

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2021-02-01-preview
```

#### <a name="request-body"></a>요청 본문

```json
{ 
    "properties":{
        "retentionDays":28
        "diffBackupIntervalInHours":24
  }
}
```

#### <a name="sample-response"></a>샘플 응답: 

```json
{ 
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
    "diffBackupIntervalInHours":24
  }
}
```


자세한 내용은 [백업 보존 REST API](/rest/api/sql/backupshorttermretentionpolicies)를 참조하세요.

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

#### <a name="sample-request"></a>샘플 요청

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>요청 본문

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>샘플 응답

상태 코드: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

자세한 내용은 [백업 보존 REST API](/rest/api/sql/backupshorttermretentionpolicies)를 참조하세요.

---

## <a name="configure-backup-storage-redundancy"></a>백업 스토리지 중복성 구성

> [!NOTE]
> SQL Managed Instance 백업에 대해 구성 가능한 스토리지 중복성은 관리형 인스턴스를 만드는 프로세스 중에만 지정할 수 있습니다. 리소스가 프로비전된 후에는 백업 스토리지 중복성 옵션을 변경할 수 없습니다. SQL Database의 경우 이 기능의 공개 미리 보기가 현재 모든 Azure 지역에 제공되고, 동남 아시아 Azure 지역에는 이 기능이 일반 공급됩니다. 

관리형 인스턴스의 백업 스토리지 중복성은 인스턴스를 만드는 동안에만 설정할 수 있습니다. SQL Database의 경우 데이터베이스를 만들 때 중복성을 설정하거나 기존 데이터베이스의 중복성을 업데이트할 수 있습니다. 기본값은 지역 중복 스토리지입니다. 로컬 중복, 영역 중복 및 지역 중복 백업 스토리지 간의 가격 차이를 보려면 [관리형 인스턴스 가격 책정 페이지](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)를 방문하세요.

### <a name="configure-backup-storage-redundancy-by-using-the-azure-portal"></a>Azure Portal을 사용하여 백업 스토리지 중복성 구성

#### <a name="sql-database"></a>[SQL 데이터베이스](#tab/single-database)

Azure Portal의 **SQL Database 만들기** 창에서 백업 스토리지 중복성을 구성할 수 있습니다. 옵션은 백업 스토리지 중복성 섹션에서 사용할 수 있습니다. 
![SQL Database 만들기 창 열기](./media/automated-backups-overview/sql-database-backup-storage-redundancy.png)

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

Azure Portal에서 백업 스토리지 중복성을 변경하는 옵션은 SQL Managed Instance를 만들 때 **기본** 탭의 **Managed Instance 구성** 옵션에서 액세스할 수 있는 **컴퓨팅 + 스토리지** 창에 있습니다.
![컴퓨팅 + 스토리지 구성 창 열기](./media/automated-backups-overview/open-configuration-blade-managedinstance.png)

**컴퓨팅 + 스토리지** 창에서 백업 스토리지 중복성을 선택하는 옵션을 찾습니다.
![백업 스토리지 중복성 구성](./media/automated-backups-overview/select-backup-storage-redundancy-managedinstance.png)

---

### <a name="configure-backup-storage-redundancy-by-using-the-azure-cli"></a>Azure CLI를 사용 하 여 백업 저장소 중복성 구성

#### <a name="sql-database"></a>[SQL 데이터베이스](#tab/single-database)

새 데이터베이스를 만들 때 백업 저장소 중복성을 구성 하려면 매개 변수를 지정 하면 `backup-storage-redundancy` 됩니다. 가능한 값은 Geo, Zone 및 Local입니다. 기본적으로 모든 SQL 데이터베이스는 백업에 지역 중복 스토리지를 사용합니다. 로컬 또는 영역 중복 백업 저장소를 사용 하 여 데이터베이스를 만들거나 업데이트 하는 경우 지역 복원을 사용할 수 없습니다.

```azurecli
az sql db create \
    --resource-group myresourcegroup \
    --server myserver \
    --name mydb \
    --tier GeneralPurpose \
    --backup-storage-redundancy Local
```

매개 변수를 사용 하 여 기존 데이터베이스를 업데이트할 수도 있습니다 `backup-storage-redundancy` .

```azurecli
az sql db update \
    --resource-group myresourcegroup \
    --server myserver \
    --name mydb \
    --backup-storage-redundancy Local
```
자세한 내용은 [az sql db create](/cli/azure/sql/db#az_sql_db_create) 및 [az sql db update](/cli/azure/sql/db#az_sql_db_update)를 참조 하십시오.

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

Azure CLI를 사용 하는 경우 SQL Managed Instance에 대해 백업 저장소 중복성을 구성할 수 없습니다. 자세한 내용은 [Azure Portal](#configure-backup-storage-redundancy-by-using-the-azure-portal) 또는 [PowerShell](#configure-backup-storage-redundancy-by-using-powershell) 옵션을 참조 하세요.

---

### <a name="configure-backup-storage-redundancy-by-using-powershell"></a>PowerShell을 사용하여 백업 스토리지 중복성 구성

#### <a name="sql-database"></a>[SQL 데이터베이스](#tab/single-database)

새 데이터베이스를 만들 때 백업 스토리지 중복성을 구성하려면 -BackupStoageRedundancy 매개 변수를 지정하면 됩니다. 가능한 값은 Geo, Zone 및 Local입니다. 기본적으로 모든 SQL 데이터베이스는 백업에 지역 중복 스토리지를 사용합니다. 로컬 또는 영역 중복 백업 스토리지를 사용하여 데이터베이스를 만들면 지역 복원을 사용할 수 없습니다. 

```powershell
# Create a new database with geo-redundant backup storage.  
New-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -ServerName "Server01" -DatabaseName "Database03" -Edition "GeneralPurpose" -Vcore 2 -ComputeGeneration "Gen5" -BackupStorageRedundancy Geo
```

자세한 내용은 [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)를 참조하세요.

기존 데이터베이스의 백업 스토리지 중복성을 업데이트하려면 -BackupStorageRedundancy 매개 변수를 사용하면 됩니다. 가능한 값은 Geo, Zone 및 Local입니다.
변경 내용이 데이터베이스에 적용될 때까지 최대 48시간이 걸릴 수 있습니다. 지역 중복 백업 스토리지에서 로컬 또는 영역 중복 스토리지로 전환하면 지역 복원을 사용할 수 없습니다. 

```powershell
# Change the backup storage redundancy for Database01 to zone-redundant. 
Set-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -DatabaseName "Database01" -ServerName "Server01" -BackupStorageRedundancy Zone
```

자세한 내용은 [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)를 참조하세요.

> [!NOTE]
> 데이터베이스 복원, 데이터베이스 복사 또는 보조 작업 만들기에 -BackupStorageRedundancy 매개 변수를 사용하려면 Azure PowerShell 버전 Az.Sql 2.11.0을 사용합니다. 


#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

관리형 인스턴스를 만드는 동안 백업 스토리지 중복성을 구성하려면 -BackupStorageRedundancy 매개 변수를 지정하면 됩니다. 가능한 값은 Geo, Zone 및 Local입니다.

```powershell
New-AzSqlInstance -Name managedInstance2 -ResourceGroupName ResourceGroup01 -Location westcentralus -AdministratorCredential (Get-Credential) -SubnetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/resourcegroup01/providers/Microsoft.Network/virtualNetworks/vnet_name/subnets/subnet_name" -LicenseType LicenseIncluded -StorageSizeInGB 1024 -VCore 16 -Edition "GeneralPurpose" -ComputeGeneration Gen4 -BackupStorageRedundancy Geo
```

자세한 내용은 [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)를 참조하세요.

---

## <a name="use-azure-policy-to-enforce-backup-storage-redundancy"></a>Azure Policy를 사용하여 백업 스토리지 중복성 적용

모든 데이터를 단일 Azure 지역에 유지해야 하는 데이터 보존 요구 사항이 있는 경우 Azure Policy를 사용하여 SQL Database 또는 Managed Instance에 영역 중복 또는 로컬 중복 백업을 적용하면 됩니다. Azure Policy는 Azure 리소스에 규칙을 적용하는 정책을 만들고 할당하고 관리하는 데 사용할 수 있는 서비스입니다. Azure Policy는 해당 리소스가 회사 표준 및 서비스 수준 계약을 준수하도록 유지하는 데 도움이 됩니다. 자세한 내용은 [Azure Policy 개요](../../governance/policy/overview.md)를 참조하세요. 

### <a name="built-in-backup-storage-redundancy-policies"></a>기본 제공 백업 스토리지 중복성 정책 

지역 중복 백업 스토리지를 사용하는 새 데이터베이스 또는 인스턴스 만들기를 차단하기 위해 구독 또는 리소스 그룹 수준에서 할당할 수 있는 다음과 같은 새 기본 제공 정책이 추가됩니다. 

[SQL Database는 GRS 백업 중복을 사용하지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb219b9cf-f672-4f96-9ab0-f5a3ac5e1c13)


[SQL Managed Instance는 GRS 백업 중복을 사용하지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9934fd7-29f2-4e6d-ab3d-607ea38e9079)

SQL Database 및 Managed Instance에 대한 기본 제공 정책 정의의 전체 목록은 [여기](./policy-reference.md)서 찾을 수 있습니다.

조직 수준에서 데이터 보존 요구 사항을 적용하려면 구독에 이러한 정책을 할당하면 됩니다. 구독 수준에서 이러한 정책을 할당하면 지정된 구독의 사용자는 Azure Portal 또는 Azure PowerShell을 통해 지역 중복 백업 스토리지를 사용하는 데이터베이스 또는 관리형 인스턴스를 만들 수 없습니다. 

> [!IMPORTANT]
> T-SQL을 통해 데이터베이스를 만들 때는 Azure 정책이 적용되지 않습니다. T-SQL을 사용하여 데이터베이스를 만들 때 데이터 보존을 적용하려면 [CREATE DATABASE 문에서 BACKUP_STORAGE_REDUNDANCY 매개 변수의 입력으로 'LOCAL' 또는 'ZONE'을 사용](/sql/t-sql/statements/create-database-transact-sql#create-database-using-zone-redundancy-for-backups)합니다.

[Azure Portal](../../governance/policy/assign-policy-portal.md) 또는 [Azure PowerShell](../../governance/policy/assign-policy-powershell.md)을 사용하여 정책을 할당하는 방법을 알아보세요.

## <a name="next-steps"></a>다음 단계

- 데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. 다른 SQL Database 비즈니스 연속성 솔루션에 대해 알아보려면 [비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)를 참조하세요.
- Azure Portal을 사용하여 Azure Blob Storage에서 자동화된 백업의 장기 보존에 따라 구성, 관리 및 복원하는 방법에 대한 내용은 [Azure Portal을 사용하여 장기 백업 보존 관리](long-term-backup-retention-configure.md)를 참조하세요.
- PowerShell을 사용하여 Azure Blob Storage에서 자동화된 백업의 장기 보존에 따라 구성, 관리 및 복원하는 방법에 대한 내용은 [PowerShell을 사용하여 장기 백업 보존 관리](long-term-backup-retention-configure.md)를 참조하세요. 
- [Azure Portal을 사용하여 지정 시간으로 데이터베이스를 복원](recovery-using-backups.md)하는 방법에 대해 자세히 알아봅니다.
- [PowerShell을 사용하여 지정 시간으로 데이터베이스를 복원](scripts/restore-database-powershell.md)하는 방법에 대해 자세히 알아봅니다.
- Azure SQL Managed Instance의 백업 스토리지 사용량에 대한 모든 정보는 설명된 [Managed Instance의 백업 스토리지 사용량](https://aka.ms/mi-backup-explained)을 참조하세요.
- Azure SQL Managed Instance의 백업 스토리지 보존 및 비용을 미세 조정하는 방법을 알아보려면 [Managed Instance의 백업 스토리지 비용 미세 조정](https://aka.ms/mi-backup-tuning)을 참조하세요.
