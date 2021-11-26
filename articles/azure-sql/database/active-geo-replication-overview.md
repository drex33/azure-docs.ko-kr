---
title: 활성 지리적 복제
description: 활성 지역 복제를 사용하여 동일하거나 다른 지역에 있는 Azure SQL Database 개별 데이터베이스의 읽기 가능한 보조 데이터베이스를 만듭니다.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: mathoma
ms.date: 10/25/2021
ms.openlocfilehash: 968f470545aa166366b57c2da80457cedb019f61
ms.sourcegitcommit: 8178cd2d9a47a67bb324483bd0879a57591706a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133110159"
---
# <a name="active-geo-replication"></a>활성 지리적 복제
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

활성 지역 복제는 주 데이터베이스에 대해 지속적으로 동기화된 읽기 가능한 보조 데이터베이스를 만들 수 있는 기능입니다. 읽을 수 있는 보조 데이터베이스는 주 데이터베이스와 동일한 Azure 지역 또는 더 일반적으로 다른 지역에 있을 수 있습니다. 이러한 종류의 읽기 가능한 보조 데이터베이스를 지역 보조 또는 지역 복제본이라고도 합니다.

활성 지역 복제는 지역 재해 또는 대규모 중단 시 개별 데이터베이스의 빠른 재해 복구를 수행할 수 있는 비즈니스 연속성 솔루션으로 설계되었습니다. 지역 복제가 설정되면 다른 Azure 지역의 지역 보조 지역에 대한 지역 장애 조치(failover)를 시작할 수 있습니다. 지역 장애 조치(failover)는 애플리케이션에서 프로그래밍 방식으로 시작하거나 사용자가 수동으로 시작합니다.

> [!NOTE]
> Azure SQL 하이퍼스케일의 활성 지역 복제는 [현재 퍼블릭 미리 보기](https://aka.ms/hsgeodr)로 제공됩니다. 다음과 같은 제한 사항이 있습니다. 동일하거나 다른 지역에 있는 하나의 지역 보조, 강제, 계획된 장애 조치(failover)는 지원되지 않습니다. 지역 보조에서의 데이터베이스 복원은 지원되지 않습니다. 지역 보조를 데이터베이스 복사본에 대한 원본 데이터베이스로 사용하거나 또 다른 지역 보조의 주 데이터베이스로 사용하는 것은 지원되지 않습니다.
> 
> 지역 보조 데이터베이스를 주 데이터베이스(쓰기 가능한 데이터베이스)로 설정해야 하는 경우 아래 단계를 수행합니다.
> 1. PowerShell의 cmdlet [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) 또는 Azure CLI의 [az sql db replica delete-link](/cli/azure/sql/db/replica#az_sql_db_replica_delete_link)를 사용하여 지역에서 복제 링크를 끊습니다. 그러면 보조 데이터베이스가 읽기-쓰기 독립 실행형 데이터베이스로 설정됩니다. 주 데이터베이스에서 커밋되었지만 보조 데이터베이스로 복제되지 않은 데이터는 모두 손실됩니다. 이전의 주 데이터베이스가 사용 가능할 때 또는 경우에 따라 이전의 주 데이터베이스를 사용 가능한 최신 시점으로 복원함으로써 이러한 변경 내용을 복구할 수 있습니다.
> 2. 이전의 주 데이터베이스가 사용 가능한 경우 이를 삭제한 다음, 새로운 주 데이터베이스에 대한 지역 복제를 설정합니다(새 보조 데이터베이스가 시드됩니다). 
> 3. 애플리케이션에서 연결 문자열을 이에 따라 업데이트합니다.

> [!NOTE]
> Azure SQL Managed Instance는 활성 지역 복제를 지원하지 않습니다. SQL Managed Instance 인스턴스의 지리적 장애 조치(failover)의 경우 [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md)을 사용합니다.

> [!NOTE]
> 활성 지역 복제를 사용하여 Azure 독일에서 SQL 데이터베이스를 마이그레이션하려면 [활성 지역 복제를 사용하여 SQL 데이터베이스 마이그레이션](../../germany/germany-migration-databases.md#migrate-sql-database-using-active-geo-replication)을 참조하세요.

애플리케이션에 지역 복제 외에도 안정적인 연결 엔드포인트 및 자동 지역 장애 조치(failover) 지원이 필요한 경우 자동 장애 [조치(failover) 그룹을](auto-failover-group-overview.md)사용합니다.

다음 다이어그램은 활성 지역 복제를 사용하는 지역 중복 클라우드 애플리케이션의 일반적인 구성을 보여 줍니다.

![활성 지역 복제](./media/active-geo-replication-overview/geo-replication.png)

어떤 이유로든 주 데이터베이스가 실패하는 경우 보조 데이터베이스에 대한 지역 장애 조치(failover)를 시작할 수 있습니다. 보조가 주 역할로 승격되면 다른 모든 보조는 새 주 역할에 자동으로 연결됩니다.

다음을 사용하여 지역 복제를 관리하고 지역 장애 조치(failover)를 시작할 수 있습니다.

- [Azure Portal](active-geo-replication-configure-portal.md)
- [PowerShell: 단일 데이터베이스](scripts/setup-geodr-and-failover-database-powershell.md)
- [PowerShell: 탄력적 풀](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- [Transact-SQL: 단일 데이터베이스 또는 탄력적 풀](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: 단일 데이터베이스](/rest/api/sql/replicationlinks)

활성 지역 복제는 [Always On 가용성 그룹](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) 기술을 활용하여 주 복제본에서 생성된 트랜잭션 로그를 모든 지역 복제본에 비동기적으로 복제합니다. 특정 시점에서 보조 데이터베이스는 주 데이터베이스 뒤에 약간 뒤에 있을 수 있지만 보조 데이터베이스의 데이터는 트랜잭션 일관성이 보장됩니다. 즉, 커밋되지 않은 트랜잭션에서 변경한 내용은 표시되지 않습니다. 

> [!NOTE]
> 활성 지역 복제는 주 복제본에서 보조 복제본으로 데이터베이스 트랜잭션 로그를 스트리밍하여 변경 내용을 복제합니다. 구독자에서 DML(INSERT, UPDATE, DELETE) 명령을 실행하여 변경 내용을 복제하는 [트랜잭션 복제와는](/sql/relational-databases/replication/transactional/transactional-replication)관련이 없습니다.

지역 복제에서 제공하는 지역 중복성을 통해 애플리케이션은 자연 재해, 치명적인 사용자 오류 또는 악의적인 행위로 인해 전체 Azure 지역 또는 지역의 일부가 영구적으로 손실되는 것을 신속하게 복구할 수 있습니다. 지역에서 복제 RPO는 비즈니스 [연속성 개요에서](business-continuity-high-availability-disaster-recover-hadr-overview.md)찾을 수 있습니다.

다음 그림에서는 미국 중북부 지역의 주 지역과 미국 중남부 지역의 지역 보조 지역으로 구성된 활성 지역 복제의 예를 보여 주었습니다.

![지역에서 복제 관계](./media/active-geo-replication-overview/geo-replication-relationship.png)

재해 복구 외에도 다음과 같은 시나리오에서 활성 지역 복제를 사용할 수 있습니다.

- **데이터베이스 마이그레이션:** 활성 지역 복제를 사용하여 가동 중지 시간을 최소화하면서 한 서버에서 다른 서버로 데이터베이스를 마이그레이션할 수 있습니다.
- **애플리케이션 업그레이드**: 애플리케이션을 업그레이드하는 동안 추가 보조 데이터베이스를 장애 복구 복사본으로 만들 수 있습니다.

전체 비즈니스 연속성을 달성하기 위해 데이터베이스 지역 중복성을 추가하는 것은 솔루션의 일부일 뿐입니다. 치명적인 오류 후 애플리케이션(서비스) 엔드투엔드 복구에는 서비스 및 모든 종속성 서비스를 구성하는 모든 구성 요소의 복구가 필요합니다. 이러한 구성 요소의 예에는 클라이언트 소프트웨어(예: 사용자 지정 JavaScript를 사용한 브라우저), 웹 프런트 엔드, 스토리지 및 DNS가 포함됩니다. 모든 구성 요소는 동일한 오류에 탄력적이며 애플리케이션의 복구 시간 목표(RTO) 내에서 사용할 수 있는 것이 중요합니다. 따라서 모든 종속성 서비스를 확인하고 제고하는 보장 사항 및 기능을 이해해야 합니다. 그런 다음 의존하는 서비스 장애 조치 중 서비스 기능을 확인하도록 적절한 단계를 수행해야 합니다. 재해 복구를 위한 솔루션 설계에 대한 자세한 내용은 [활성 지역 복제를 사용하여 재해 복구를 위한 클라우드 솔루션 설계](designing-cloud-solutions-for-disaster-recovery.md)를 참조하세요.

## <a name="active-geo-replication-terminology-and-capabilities"></a>활성 지역 복제 용어 및 기능

- **자동 비동기 복제**

  기존 데이터베이스에 대한 지역 보조 데이터베이스만 만들 수 있습니다. 지역 보조 데이터베이스는 주 데이터베이스가 있는 서버가 아닌 모든 논리 서버에 만들 수 있습니다. 생성되면 지역 보조 복제본이 주 데이터베이스의 데이터로 채워집니다. 이 프로세스를 시드라고 합니다. 지역 보조 복제본을 만들고 시드한 후에는 주 데이터베이스에 대한 업데이트가 자동으로 지역 보조 복제본에 비동기적으로 복제됩니다. 비동기 복제는 트랜잭션이 복제되기 전에 주 데이터베이스에서 커밋된다는 것을 의미합니다.

- **읽을 수 있는 지역 보조 복제본**

  애플리케이션은 지역 보조 복제본에 액세스하여 주 데이터베이스에 액세스하는 데 사용되는 것과 동일하거나 다른 보안 주체를 사용하여 읽기 전용 쿼리를 실행할 수 있습니다. 자세한 내용은 [읽기 전용 복제본을 사용하여 읽기 전용 쿼리 워크로드 오프로드를 참조하세요.](read-scale-out.md)

   > [!IMPORTANT]
   > 지역 복제를 사용하여 주 복제본과 동일한 지역에 보조 복제본을 만들 수 있습니다. 이러한 보조를 사용하여 동일한 지역의 읽기 확장 시나리오를 충족할 수 있습니다. 그러나 동일한 지역의 보조 복제본은 치명적인 오류 또는 대규모 중단에 대한 추가 복원력을 제공하지 않으므로 재해 복구를 위해 적합한 장애 조치(failover) 대상이 아닙니다. 또한 가용성 영역 격리를 보장하지 않습니다. 중요 비즈니스용 또는 Premium 서비스 계층 [영역 중복 구성](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) 또는 범용 서비스 계층 영역 중복 [구성을](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) 사용하여 가용성 영역 격리를 달성합니다.
   >

- **계획된 지역 장애 조치(failover)**

  계획된 지역 장애 조치(failover)는 전체 데이터 동기화를 완료한 후 주 및 지역 보조 데이터베이스의 역할을 전환합니다. 계획된 장애 조치(failover)로 인해 데이터가 손실되지 않습니다. 계획된 지역 장애 조치 기간은 지역 보조 데이터베이스에 동기화해야 하는 주 데이터베이스의 트랜잭션 로그 크기에 따라 달라집니다. 계획된 지역 장애 조치(failover)는 다음과 같은 시나리오를 위해 설계되었습니다.

  - 데이터 손실이 허용되지 않는 경우 프로덕션에서 DR 훈련을 수행합니다. 
  - 데이터베이스를 다른 지역으로 재배치합니다. 
  - 중단이 완화된 후 데이터베이스를 주 지역으로 반환합니다(장애 복구(failback)라고 함).

- **계획되지 않은 지역 장애 조치(failover)**

  계획되지 않거나 강제적인 지역 장애 조치(failover)는 주 데이터베이스와의 동기화 없이 지역 보조 데이터베이스를 주 역할로 즉시 전환합니다. 주 에서 커밋되었지만 보조에 아직 복제되지 않은 트랜잭션은 손실됩니다. 이 작업은 주 데이터베이스에 액세스할 수 없는 가동 중단 시 복구 방법으로 설계되었지만 데이터베이스 가용성을 신속하게 복원해야 합니다. 원래 주 주 데이터가 다시 온라인 상태가 되면 자동으로 다시 연결되고, 현재 주 데이터를 사용하여 다시 연결되고, 새로운 지역 보조가 됩니다.

  > [!IMPORTANT]
  > 계획된 지역 장애 조치(failover) 또는 계획되지 않은 지역 장애 조치(failover) 후 새 주 데이터베이스가 다른 논리 서버에 있기 때문에 새 주 데이터베이스에 대한 연결 엔드포인트가 변경됩니다.

- **읽을 수 있는 여러 지역 보조**

  주 지역 보조에 대해 최대 4개의 지역 보조를 만들 수 있습니다. 보조가 하나만 있고 실패하면 새 보조가 만들어질 때까지 애플리케이션이 더 높은 위험에 노출됩니다. 보조가 여러 개 있는 경우 보조 항목 중 하나가 실패하더라도 애플리케이션은 보호된 상태로 유지됩니다. 추가 보조를 사용하여 읽기 전용 워크로드를 확장할 수도 있습니다.

  > [!TIP]
  > 활성 지역 복제를 사용하여 전역적으로 분산된 애플리케이션을 빌드하고 4개 이상의 지역에 있는 데이터에 대한 읽기 전용 액세스를 제공해야 하는 경우 보조 보조 복제본(연결이라고 하는 프로세스)을 만들어 추가 지역 복제본을 만들 수 있습니다. 연결된 지역 복제본의 복제 지연 시간이 주 복제본에 직접 연결된 지역 복제본보다 높을 수 있습니다. 연결된 지역 복제 토폴로지 설정은 프로그래밍 방식으로만 지원되며 Azure Portal 지원되지 않습니다.

- **탄력적 풀에서 데이터베이스 지역 복제**

  각 지역 보조 데이터베이스는 단일 데이터베이스 또는 탄력적 풀의 데이터베이스일 수 있습니다. 각 지역 보조 데이터베이스에 대한 탄력적 풀 선택은 별개이며 토폴로지(주 또는 보조)의 다른 복제본 구성에 따라 달라지지 않습니다. 각 탄력적 풀은 단일 논리 서버 내에 포함됩니다. 논리 서버의 데이터베이스 이름은 고유해야 하므로 동일한 주 데이터베이스의 여러 지역 보조 데이터베이스는 탄력적 풀을 공유할 수 없습니다.

- **사용자 제어 지역 장애 조치(failover) 및 장애 복구(failback)**

  초기 시드를 완료한 지역 보조는 애플리케이션 또는 사용자가 언제든지 주 역할(장애 조치)으로 명시적으로 전환할 수 있습니다. 주 지역에 액세스할 수 없는 가동 중단 중에는 계획되지 않은 지역 장애 조치(failover)만 사용할 수 있습니다. 이는 지역 보조를 새 주 지역으로 즉시 승격합니다. 가동 중단이 완화되면 시스템은 자동으로 복구된 주체를 지역 보조로 만들고 새 주 지역과 함께 최신 상태로 만듭니다. 지역 복제의 비동기 특성으로 인해 이러한 트랜잭션이 지역 보조로 복제되기 전에 주 트랜잭션이 실패하는 경우 계획되지 않은 지역 장애 조치(failover) 중에 최근 트랜잭션이 손실될 수 있습니다. 여러 지역 보조가 있는 주 항목이 장애 조치를 하면 시스템은 자동으로 복제 관계를 다시 구성하고 나머지 지역 보조를 새로 승격된 주에 연결합니다. 이때 사용자 개입이 필요 없습니다. 지역 장애 조치(failover)를 발생시킨 중단이 완화된 후 주 지역을 원래 지역으로 반환하는 것이 바람직할 수 있습니다. 이렇게 하려면 계획된 지역 장애 조치(failover)를 호출합니다.

## <a name="prepare-for-geo-failover"></a><a name="preparing-secondary-database-for-failover"></a> 지역 장애 조치(failover) 준비

지역 장애 조치(failover) 후 애플리케이션이 새 주 데이터베이스에 즉시 액세스할 수 있도록 보조 서버에 대한 인증 및 네트워크 액세스가 올바르게 구성되었는지 확인합니다. 자세한 내용은 [재해 복구 후의 SQL Database 보안](active-geo-replication-security-configure.md)을 참조하세요. 또한 보조 데이터베이스의 백업 보존 정책이 주 데이터베이스의 백업 보존 정책과 일치하는지 확인합니다. 이 설정은 데이터베이스의 일부가 아니며 주 데이터베이스에서 복제되지 않습니다. 기본적으로 지역 보조는 기본 PITR 보존 기간인 7일로 구성됩니다. 자세한 내용은 [SQL Database 자동화된 백업](automated-backups-overview.md)을 참조하세요.

> [!IMPORTANT]
> 데이터베이스가 장애 조치(failover) 그룹의 멤버인 경우 지역 복제 장애 조치(failover) 명령을 사용하여 장애 조치(failover)를 시작할 수 없습니다. 그룹에 대해 장애 조치(failover) 명령을 사용합니다. 개별 데이터베이스를 장애 조치(failover)해야 하는 경우 먼저 해당 데이터베이스를 장애 조치(failover) 그룹에서 제거해야 합니다. 자세한 내용은  [자동 장애 조치(failover) 그룹을](auto-failover-group-overview.md) 참조하세요.

## <a name="configure-geo-secondary"></a><a name="configuring-secondary-database"></a> 지역 보조 구성

기본 및 지역 보조는 모두 동일한 서비스 계층을 가져야 합니다. 또한 지역 보조 데이터베이스는 주 데이터베이스와 동일한 백업 스토리지 중복성 및 컴퓨팅 크기(DTU 또는 vCore)로 구성하는 것이 좋습니다. 주 데이터베이스에 쓰기 작업이 많은 경우 컴퓨팅 크기가 낮은 지역 보조 데이터베이스를 따라가지 못할 수 있습니다. 이렇게 하면 지역 보조에서 복제 지연이 발생하며 결국 지역 보조를 이용할 수 없게 될 수 있습니다. 이러한 위험을 완화하기 위해 활성 지역 복제는 보조가 따라갈 수 있도록 필요한 경우 주 트랜잭션 로그 속도를 줄입니다(제한).

불균형 지역 보조 구성의 또 다른 결과는 장애 조치(failover) 후 새 주 데이터베이스의 컴퓨팅 용량 부족으로 인해 애플리케이션 성능이 저하할 수 있다는 것입니다. 이 경우 충분한 리소스를 포함하도록 데이터베이스를 확장해야 하며, 이는 상당한 시간이 걸릴 수 있으며, 확장 프로세스가 끝날 때 [고가용성](high-availability-sla.md) 장애 조치(failover)가 필요하므로 애플리케이션 워크로드가 중단될 수 있습니다.

컴퓨팅 크기가 낮은 지역 보조 데이터베이스를 만들려는 경우 시간별 주 데이터베이스의 로그 IO 속도를 모니터링해야 합니다. 이렇게 하면 복제 부하를 유지하는 데 필요한 지역 보조 데이터베이스의 최소 컴퓨팅 크기를 예상할 수 있습니다. 예를 들어 주 데이터베이스가 P6(1000 DTU)이고 로그 IO가 50%로 유지되는 경우 지역 보조 데이터베이스는 P4(500 DTU) 이상이어야 합니다. 기록 로그 IO 데이터를 검색하려면 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 뷰를 사용합니다. 단기 급증을 더 잘 반영하는 세분성이 더 높은 최근 로그 IO 데이터를 검색하려면 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 보기를 사용합니다.

> [!TIP]
> 지역 보조 데이터베이스에서 낮은 컴퓨팅 크기로 인한 주 데이터베이스의 트랜잭션 로그 IO 제한은 sys.dm_exec_requests 및 sys.dm_os_wait_stats [데이터베이스](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) 뷰에 표시되는 [HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 대기 유형을 사용하여 보고됩니다.
>
> 주 데이터베이스의 트랜잭션 로그 IO는 지역 보조 데이터베이스의 낮은 컴퓨팅 크기와 관련이 없는 이유로 제한될 수 있습니다. 이러한 종류의 제한은 지역 보조 데이터베이스의 컴퓨팅 크기가 주 데이터베이스와 동일하거나 더 높은 경우에도 발생할 수 있습니다. 다양한 종류의 로그 IO 제한에 대한 대기 유형을 비롯한 자세한 내용은 [트랜잭션 로그 속도 거버넌스를 참조하세요.](resource-limits-logical-server.md#transaction-log-rate-governance)

기본적으로 지역 보조 데이터베이스의 백업 스토리지 중복성은 주 데이터베이스와 동일합니다. 다른 백업 스토리지 중복성을 사용하여 지역 보조를 구성할 수 있습니다. 백업은 항상 주 데이터베이스에서 수행됩니다. 보조가 다른 백업 스토리지 중복으로 구성된 경우 지역 장애 조치(failover) 후 지역 보조가 주로 승격되면 새 백업이 저장되고 새 주(이전 보조)에서 선택한 스토리지 유형(RA-GRS, ZRS, LRS)에 따라 요금이 청구됩니다. 

## <a name="cross-subscription-geo-replication"></a>구독 간 지역 복제

동일한 Azure Active Directory 테넌트에서 주 구독과 다른 구독에 지역 보조를 만들려면 이 섹션의 단계를 따릅니다.

1. 아래 T-SQL 명령을 실행하는 클라이언트 컴퓨터의 IP 주소를 주 서버와 보조 **서버의** 서버 방화벽에 추가합니다. 동일한 클라이언트 컴퓨터에서 주 서버에 연결된 동안 다음 쿼리를 실행하여 IP 주소를 확인할 수 있습니다.
  
   ```sql
   select client_net_address from sys.dm_exec_connections where session_id = @@SPID;
   ``` 

   자세한 내용은 [방화벽 구성을 참조하세요.](firewall-configure.md)

2. **주** 서버의 master 데이터베이스에서 활성 지역 복제 설정 전용 SQL 인증 로그인을 만듭니다. 필요에 따라 로그인 이름 및 암호를 조정합니다.

   ```sql
   create login geodrsetup with password = 'ComplexPassword01';
   ```

3. 동일한 데이터베이스에서 로그인에 대한 사용자를 만들고 역할에 추가합니다. `dbmanager`

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup;
   ```

4. 새 로그인의 SID 값을 기록해 둡시다. 다음 쿼리를 사용하여 SID 값을 얻습니다.

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup';
   ```

5. 주 데이터베이스(master  데이터베이스가 아닌)에 커넥트 동일한 로그인에 대한 사용자를 만듭니다.

   ```sql
   create user geodrsetup for login geodrsetup;
   ```

6. 동일한 데이터베이스에서 `db_owner` 사용자를 역할에 추가합니다.

   ```sql
   alter role db_owner add member geodrsetup;
   ```

7. **보조** 서버의 master 데이터베이스에서 동일한 이름, 암호 및 SID를 사용하여 주 서버와 동일한 로그인을 만듭니다. 아래 샘플 명령의 16진수 SID 값을 4단계에서 얻은 값으로 대체합니다.

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E;
   ```

8. 동일한 데이터베이스에서 로그인에 대한 사용자를 만들고 `dbmanager` 역할에 추가합니다.

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup;
   ```

9. 새 로그인을 사용하여 **주** 서버의 master 데이터베이스에 커넥트 `geodrsetup` 보조 서버에서 지역 보조 만들기를 시작합니다. 필요에 따라 데이터베이스 이름 및 보조 서버 이름을 조정합니다. 명령이 실행되면 주 데이터베이스의 [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) 뷰와 **주** **서버의** master 데이터베이스에 있는 [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) 보기를 쿼리하여 지역 보조 생성을 모니터링할 수 있습니다. 지역 보조 데이터베이스를 만드는 데 필요한 시간은 주 데이터베이스 크기에 따라 달라집니다.

   ```sql
   alter database [dbrep] add secondary on server [servername];
   ```

10. 지역 보조를 성공적으로 만든 후에는 이 절차에서 만든 사용자, 로그인 및 방화벽 규칙을 제거할 수 있습니다.

> [!NOTE]
> 설치 및 지역 장애 조치(failover)를 포함한 구독 간 지역 복제 작업은 T-SQL 명령을 통해서만 지원됩니다.
> 
> 기본 및/또는 보조 서버에 [프라이빗 엔드포인트가](private-endpoint-overview.md) 구성되어 있고 [공용 네트워크 액세스가 거부된](connectivity-settings.md#deny-public-network-access)경우 T-SQL 사용하여 지역 보조를 추가하는 것은 지원되지 않습니다. 프라이빗 엔드포인트가 구성되었지만 공용 네트워크 액세스가 허용되는 경우 공용 IP 주소에서 주 서버에 연결할 때 지역 보조 데이터베이스를 추가할 수 있습니다. 지역 보조가 추가되면 공용 액세스를 거부할 수 있습니다.
> 
> 기본 또는 보조 논리 서버에서 Azure SQL 대한 [인증만](https://techcommunity.microsoft.com/t5/azure-sql/azure-active-directory-only-authentication-for-azure-sql/ba-p/2417673) 활성화된 경우 다른 Azure 테넌트에서 논리 서버에 지역 보조를 만드는 Azure Active Directory 지원되지 않습니다.

## <a name="keep-credentials-and-firewall-rules-in-sync"></a><a name="keeping-credentials-and-firewall-rules-in-sync"></a> 자격 증명 및 방화벽 규칙을 동기화된 상태로 유지

데이터베이스에 연결하기 위해 공용 네트워크 액세스를 사용하는 경우 지역 복제 [데이터베이스에 데이터베이스 수준 IP 방화벽 규칙을](firewall-configure.md) 사용하는 것이 좋습니다. 이러한 규칙은 데이터베이스와 함께 복제되어 모든 지역 보조 데이터베이스가 주 데이터베이스와 동일한 IP 방화벽 규칙을 갖도록 합니다. 이 방법을 사용하면 고객이 주 데이터베이스와 보조 데이터베이스를 호스트하는 서버에서 방화벽 규칙을 수동으로 구성하고 유지 관리하지 않아도 될 수 있습니다. 마찬가지로 포함된 [데이터베이스 사용자를](logins-create-manage.md) 데이터 액세스에 사용하면 주 데이터베이스와 보조 데이터베이스의 인증 자격 증명이 항상 동일합니다. 이러한 방식으로 지역 장애 조치(failover) 후 인증 자격 증명 불일치로 인해 중단이 없습니다. 포함된 사용자 대신 로그인 및 사용자를 사용하는 경우 보조 데이터베이스에 대해 동일한 로그인이 있는지 확인하기 위해 추가 단계를 수행해야 합니다. 구성 세부 정보는 [로그인 및 사용자를 구성하는 방법을 참조하세요.](active-geo-replication-security-configure.md)

## <a name="scale-primary-database"></a><a name="upgrading-or-downgrading-primary-database"></a> 주 데이터베이스 크기 조정

지역 보조 데이터베이스의 연결을 끊지 않고도 주 데이터베이스를 동일한 서비스 계층 내의 다른 컴퓨팅 크기로 확장하거나 축소할 수 있습니다. 스케일 업할 때 먼저 지역 보조를 강화한 다음 주 지역 을 확장하는 것이 좋습니다. 축소할 때 순서를 반대로 바급합니다. 먼저 기본을 축소한 다음, 보조를 축소합니다.

> [!NOTE]
> 장애 조치(failover) 그룹 구성의 일부로 지역 보조를 만든 경우 규모를 축소하지 않는 것이 좋습니다. 이는 데이터 계층에 지역 장애 조치(failover) 후 일반 워크로드를 처리할 수 있는 충분한 용량이 있는지 확인하기 위한 것입니다.

> [!IMPORTANT]
> 보조 데이터베이스를 상위 계층으로 먼저 확장하지 않으면 장애 조치(failover) 그룹의 주 데이터베이스를 더 높은 서비스 계층(버전)으로 확장할 수 없습니다. 예를 들어 주 범용 중요 비즈니스용 확장하려면 먼저 지역 보조를 중요 비즈니스용 확장해야 합니다. 이 규칙을 위반하는 방식으로 주 또는 지역 보조를 확장하려고 하면 다음 오류가 표시됩니다.
>
> `The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="prevent-loss-of-critical-data"></a><a name="preventing-the-loss-of-critical-data"></a> 중요한 데이터의 손실 방지

광역 네트워크의 대기 시간이 높기 때문에 지역에서 복제는 비동기 복제 메커니즘을 사용합니다. 비동기 복제는 주 복제가 실패할 경우 데이터 손실을 피할 수 없게 만듭니다. 데이터 손실로부터 중요한 트랜잭션을 보호하기 위해 애플리케이션 개발자는 트랜잭션을 커밋한 직후 [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) 저장 프로시저를 호출할 수 있습니다. 호출은 `sp_wait_for_database_copy_sync` 마지막으로 커밋된 트랜잭션이 보조 데이터베이스의 트랜잭션 로그에서 전송 및 강화될 때까지 호출 스레드를 차단합니다. 그러나 보조에서 전송된 트랜잭션이 재생(다시 실행)될 때까지 기다리지 않습니다. `sp_wait_for_database_copy_sync` 는 특정 지역 복제 링크로 범위가 지정됩니다. 주 데이터베이스에 대한 연결 권한이 있는 모든 사용자는 이 프로시저를 호출할 수 있습니다.

> [!NOTE]
> `sp_wait_for_database_copy_sync` 는 특정 트랜잭션에 대한 지역 장애 조치(failover) 후 데이터 손실을 방지하지만 읽기 액세스에 대한 전체 동기화를 보장하지는 않습니다. 프로시저 호출로 인한 지연은 중요할 수 있으며 호출 시 주 에서 `sp_wait_for_database_copy_sync` 아직 전송되지 않은 트랜잭션 로그의 크기에 따라 달라집니다.

## <a name="monitor-geo-replication-lag"></a><a name="monitoring-geo-replication-lag"></a> 지역 복제 지연 모니터링

RPO 측면에서 지연 시간을 모니터링하려면 주 데이터베이스에서 [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)의 *replication_lag_sec* 열을 사용합니다. 주 지역에서 커밋되고 보조의 트랜잭션 로그로 강화된 트랜잭션 간의 지연 시간(초)을 표시합니다. 예를 들어 지연 시간이 1초이면 현재 가동 중단의 영향을 받고 지역 장애 조치(failover)가 시작되면 마지막 1초 동안 커밋된 트랜잭션이 손실됩니다.

지역 보조 데이터베이스에서 강화된 주 데이터베이스의 변경 내용과 관련하여 지연을 측정하려면 지역 보조 데이터베이스의 *last_commit* 시간을 주 데이터베이스의 동일한 값과 비교합니다.

> [!TIP]
> 주 *replication_lag_sec* NULL인 경우 주 주에서는 현재 지역 보조가 얼마나 뒤처져 있는지 알지 못한다는 의미입니다. 이는 일반적으로 프로세스가 다시 시작된 후에 발생하며 일시적 상태여야 합니다. *replication_lag_sec* 오랜 시간 동안 NULL을 반환하는 경우 경고를 보내는 것이 좋습니다. 지역 보조가 연결 오류로 인해 주 지역과 통신할 수 없음을 나타낼 수 있습니다.
> 
> 지역 보조에서 *last_commit* 시간과 주 지역 간의 차이가 커질 수 있는 조건도 있습니다. 예를 들어 오랜 시간 동안 변경하지 않고 주 에서 커밋이 이루어지면 차이가 큰 값으로 이동한 후 빠르게 0으로 돌아갑니다. 이러한 두 값 간의 차이가 오랫동안 큰 경우 경고를 보내는 것이 좋습니다.

## <a name="programmatically-manage-active-geo-replication"></a><a name="programmatically-managing-active-geo-replication"></a> 프로그래밍 방식으로 활성 지역 복제 관리

앞서 설명한 것처럼 활성 지역 복제는 T-SQL, Azure PowerShell 및 REST API 사용하여 프로그래밍 방식으로도 관리될 수 있습니다. 다음 표는 사용 가능한 명령의 집합을 보여 줍니다. 활성 지역 복제는 관리를 위해 [Azure SQL Database REST API](/rest/api/sql/) 및 [Azure PowerShell cmdlet](/powershell/azure/)을 비롯한 Azure Resource Manager API 세트를 포함합니다. 이러한 Api는 azure RBAC (역할 기반 액세스 제어)를 지원 합니다. 액세스 역할을 구현하는 방법에 관한 자세한 내용은 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 참조하세요.

### <a name="t-sql-manage-geo-failover-of-single-and-pooled-databases"></a><a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: 단일 및 풀링된 데이터베이스의 지역 장애 조치 (failover) 관리

> [!IMPORTANT]
> 이러한 T SQL 명령은 활성 지역 복제에만 적용 되며 장애 조치 (failover) 그룹에는 적용 되지 않습니다. 따라서 장애 조치 (failover) 그룹만 지 원하는 SQL Managed Instance에는 적용 되지 않습니다.

| 명령 | 설명 |
| --- | --- |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |**ADD SECONDARY ON SERVER** 인수를 사용 하 여 기존 데이터베이스에 대 한 보조 데이터베이스를 만들고 데이터 복제를 시작 합니다. |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |장애 조치 (failover)를 시작 하기 위해 보조 데이터베이스를 주 데이터베이스로 전환 하려면 **장애 조치** 또는 **FORCE_FAILOVER_ALLOW_DATA_LOSS** 사용 |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |**서버에서 보조 데이터베이스 제거** 를 사용 하 여 SQL Database와 지정 된 보조 데이터베이스 간의 데이터 복제를 종료 합니다. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |서버의 각 데이터베이스에 대한 모든 기존 복제 링크에 대한 정보를 반환합니다. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |지정된 데이터베이스의 복제 링크에 대한 마지막 복제 시간, 마지막 복제 지연 및 기타 정보를 가져옵니다. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |복제 링크에 대 한 변경 내용을 포함 하 여 모든 데이터베이스 작업의 상태를 표시 합니다. |
| [sys.sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |모든 커밋된 트랜잭션이 지역 보조 데이터베이스의 트랜잭션 로그에 확정 될 때까지 응용 프로그램을 대기 시킵니다. |
|  | |

### <a name="powershell-manage-geo-failover-of-single-and-pooled-databases"></a><a name="powershell-manage-failover-of-single-and-pooled-databases"></a> PowerShell: 단일 및 풀링된 데이터베이스의 지역 장애 조치 (failover) 관리

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 여전히 Azure SQL Database에서 지원되지만 향후의 모든 개발은 Az.Sql 모듈을 위한 것입니다. 이러한 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다.

| Cmdlet | Description |
| --- | --- |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |하나 이상의 데이터베이스를 가져옵니다. |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary) |기존 데이터베이스에 대한 보조 데이터베이스를 만들고 데이터 복제를 시작합니다. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary) |장애 조치를 시작하기 위해 보조 데이터베이스로 전환합니다. |
| [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) |SQL Database와 지정된 보조 데이터베이스 간의 데이터 복제를 종료합니다. |
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) |데이터베이스에 대 한 지역에서 복제 링크를 가져옵니다. |
|  | |

> [!TIP]
> 샘플 스크립트는 [활성 지역 복제를 사용하여 단일 데이터베이스 구성 및 장애 조치(failover)](scripts/setup-geodr-and-failover-database-powershell.md) 및 [활성 지역 복제를 사용하여 풀링된 데이터베이스 구성 및 장애 조치(failover)](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)를 참조하세요.

### <a name="rest-api-manage-geo-failover-of-single-and-pooled-databases"></a><a name="rest-api-manage-failover-of-single-and-pooled-databases"></a> REST API: 단일 및 풀링된 데이터베이스의 지역 장애 조치 (failover) 관리

| API | 설명 |
| --- | --- |
| [데이터베이스 생성 또는 업데이트(createMode=Restore)](/rest/api/sql/databases/createorupdate) |주 보조 데이터베이스 또는 보조 데이터베이스를 만들거나, 업데이트하거나, 복원합니다. |
| [데이터베이스 만들기 또는 업데이트 상태 가져오기](/rest/api/sql/databases/createorupdate) |만들기 작업 동안 상태를 반환합니다. |
| [보조 데이터베이스를 주 데이터베이스로 설정(계획된 장애 조치(failover))](/rest/api/sql/replicationlinks/failover) |현재 주 데이터베이스에서 장애 조치(failover)를 통해 주 데이터베이스로 사용할 보조 데이터베이스를 설정합니다. **이 옵션은 SQL Managed Instance에서 지원되지 않습니다.**|
| [보조 데이터베이스를 주 데이터베이스로 설정(계획되지 않은 장애 조치(failover))](/rest/api/sql/replicationlinks/failoverallowdataloss) |현재 주 데이터베이스에서 장애 조치(failover)를 통해 주 데이터베이스로 사용할 보조 데이터베이스를 설정합니다. 이 작업으로 인해 데이터가 손실될 수 있습니다. **이 옵션은 SQL Managed Instance에서 지원되지 않습니다.**|
| [복제 링크 가져오기](/rest/api/sql/replicationlinks/get) |지역 복제 파트너 관계의 지정된 데이터베이스에 대한 특정 복제 링크를 가져옵니다. sys.geo_replication_links 카탈로그 뷰에 표시되는 정보를 검색합니다. **이 옵션은 SQL Managed Instance에서 지원되지 않습니다.**|
| [복제 링크 - 데이터베이스 기준 목록](/rest/api/sql/replicationlinks/listbydatabase) | 지역 복제 파트너 관계의 지정된 데이터베이스에 대한 모든 복제 링크를 가져옵니다. sys.geo_replication_links 카탈로그 뷰에 표시되는 정보를 검색합니다. |
| [복제 링크 삭제](/rest/api/sql/replicationlinks/delete) | 데이터베이스 복제 링크를 삭제합니다. 장애 조치(failover) 중에 수행할 수 없습니다. |
|  | |

## <a name="next-steps"></a>다음 단계

- 샘플 스크립트에 대해서는 다음을 참조하세요.
  - [활성 지역 복제를 사용 하 여 단일 데이터베이스 구성 및 장애 조치 (failover)](scripts/setup-geodr-and-failover-database-powershell.md)
  - [활성 지역 복제를 사용 하 여 풀링된 데이터베이스를 구성 하 고 장애 조치 (failover)](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)합니다.
- SQL Database는 자동 장애 조치(failover) 그룹도 지원합니다. 자세한 내용은 [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md) 사용을 참조하세요.
- 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)를 참조하세요.
- Azure SQL Database 자동화 백업에 대한 자세한 내용은 [SQL Database 자동화 백업](automated-backups-overview.md)을 참조하세요.
- 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](recovery-using-backups.md)을 참조하세요.
- 새로운 주 서버 및 데이터베이스의 인증 요구 사항에 대해 알아보려면 [재해 복구 후의 SQL Database 보안](active-geo-replication-security-configure.md)을 참조하세요.
