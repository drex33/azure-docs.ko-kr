---
title: 클라우드 비즈니스 연속성 - 데이터베이스 복구
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure SQL Database 및 SQL Managed Instance에서 클라우드 업무 연속성과 데이터베이스 복구를 지원하고 중요 업무용 클라우드 응용 프로그램을 계속해서 실행할 수 있도록 하는 방법을 알아봅니다.
keywords: 무중단 업무 방식, 클라우드 무중단 업무 방식, 데이터베이스 재해 복구, 데이터베이스 복구
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: BustosMSFT
ms.author: robustos
ms.reviewer: mathoma
ms.date: 06/25/2019
ms.openlocfilehash: c4a7228662d1070f001b5cbe9d2199639362bccb
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114465645"
---
# <a name="overview-of-business-continuity-with-azure-sql-database--azure-sql-managed-instance"></a>Azure SQL Database와 Azure SQL Managed Instance를 사용한 비즈니스 연속성 개요
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

**비즈니스 연속성** 은 Azure SQL Database 및 SQL Managed Instance가 특히 컴퓨팅 인프라가 중단되는 상황에서도 비즈니스를 계속 운영할 수 있도록 하는 메커니즘, 정책 및 절차를 나타냅니다. 대부분의 경우 SQL Database와 SQL Managed Instance는 클라우드 환경에서 발생할 수 있는 중단 이벤트에 대처하고 응용 프로그램 및 비즈니스 프로세스를 계속 실행합니다. 그러나 SQL Database에서 자동으로 처리할 수 없는 몇 가지 중단 이벤트가 있습니다.

- 사용자가 실수로 테이블 행을 삭제하거나 업데이트했습니다.
- 악의적인 공격자가 데이터를 삭제하거나 데이터베이스를 삭제하는 데 성공했습니다.
- 지진으로 인해 정전되었거나 데이터 센터가 일시적으로 사용할 수 없게 되는 경우.

이 개요에서는 비즈니스 연속성 및 재해 복구를 위해 SQL Database 및 SQL Managed Instance 제공하는 기능을 설명합니다. 데이터 손실을 유발하거나 데이터베이스 및 애플리케이션을 사용할 수 없게 만들 수 있는 중단 이벤트에서의 복구와 관련된 옵션, 권장 사항 및 자습서에 대해 알아봅니다. 사용자 또는 애플리케이션 오류가 데이터 무결성에 영향을 주거나, Azure 지역에 가동 중단이 발생하거나, 애플리케이션에 유지 관리가 필요할 때 수행할 작업을 알아봅니다.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>비즈니스 연속성을 제공하는 데 사용할 수 있는 SQL Database 기능

데이터베이스 관점에서 다음 네 가지 주요 잠재적 중단 시나리오가 있습니다.

- 로컬 하드웨어 또는 소프트웨어 오류 - 디스크 드라이브 오류와 같이 데이터베이스 노드에 영향을 미칩니다.
- 데이터 손상 또는 삭제 - 일반적으로 애플리케이션 버그 또는 사람의 실수로 인해 발생합니다. 이러한 오류는 응용 프로그램에서 개별적으로 발생하며 일반적으로 데이터베이스 서비스에서 검색할 수 없습니다.
- 데이터 센터 중단 - 아마도 자연 재해로 인해 발생했을 수 있습니다. 이 시나리오에는 대체 데이터 센터로의 애플리케이션 장애 조치가 포함된 일부 수준의 지리적 중복성이 필요합니다.
- 업그레이드 또는 유지 관리 오류, 예정된 인프라 유지 관리 또는 업그레이드 중에 발생하는 돌발적인 문제는 이전 데이터베이스 상태로 신속한 롤백이 필요할 수 있습니다.

로컬 하드웨어 및 소프트웨어 오류를 완화하기 위해 SQL Database는 최대 99.995%의 가용성 SLA로 이러한 오류로부터 자동 복구를 보장하는 [고가용성 아키텍처](high-availability-sla.md)를 포함합니다.  

데이터 손실로부터 비즈니스를 보호하기 위해 SQL Database 및 SQL Managed Instance는 매주 전체 데이터베이스 백업, 12시간마다 차등 데이터베이스 백업 및 5-10분마다 트랜잭션 로그 백업을 자동으로 작성합니다. 백업은 모든 서비스 계층에 최소 7일 동안 RA-GRS 스토리지에 저장됩니다. 기본 지원을 제외한 모든 서비스 계층은 특정 시점 복원을 위해 구성 가능한 백업 보존 기간을 최대 35일까지 지원합니다.

SQL Database 및 SQL Managed Instance는 다양한 돌발 시나리오를 완화하는 데 유용한 몇 가지 비즈니스 연속성 기능도 제공합니다.

- [temporal 테이블](../temporal-tables.md)을 사용하면 특정 시점의 행 버전을 복원할 수 있습니다.
- [기본 제공 자동 백업](automated-backups-overview.md) 및 [특정 시점 복원](recovery-using-backups.md#point-in-time-restore) 을 사용하면 전체 데이터베이스를 지난 35일 이내의 특정 시점으로 복원할 수 있습니다.
- [삭제된 데이터베이스를 삭제된 시점으로 복원](recovery-using-backups.md#deleted-database-restore) 하는 기능은 **논리 서버가 삭제되지 않았** 다면 사용 가능합니다.
- [장기 백업 보존](long-term-retention-overview.md)을 사용하면 백업을 최대 10년 동안 유지할 수 있습니다. 이 기능은 SQL Managed Instance에서 제한된 공개 미리 보기로 제공됩니다.
- [활성 지역 복제를](active-geo-replication-overview.md) 사용하면 데이터 센터 가동 중단 또는 응용 프로그램 업그레이드 시 읽기 가능한 복제본을 만들고 모든 복제본에 수동으로 장애 조치(failover)를 취할 수 있습니다.
- [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md#terminology-and-capabilities) 을 사용하면 데이터 센터 가동 중단 시 응용 프로그램에서 자동으로 복구할 수 있습니다.

## <a name="recover-a-database-within-the-same-azure-region"></a>동일한 Azure 지역 내에서 데이터베이스 복구

자동 데이터베이스 백업을 사용하여 과거의 시점으로 데이터베이스를 복원할 수 있습니다. 이러한 방식으로 인력 오류로 인한 데이터 손상으로부터 복구할 수 있습니다. 특정 시점 복원을 사용하면 손상된 이벤트 이전의 데이터 상태인 새 데이터베이스를 동일한 서버에 만들 수 있습니다. 대부분의 데이터베이스에서 복원 작업은 12시간 미만이 걸립니다. 매우 크거나 매우 활성화된 상태인 데이터베이스를 복구하는 데는 시간이 더 오래 걸릴 수 있습니다. 복구 시간에 대한 자세한 내용은 [데이터베이스 복구 시간](recovery-using-backups.md#recovery-time)을 참조하세요.

지원되는 최대 PITR(특정 시점 복원) 보존 기간이 응용 프로그램에 충분하지 않을 경우 데이터베이스에 대한 LTR(장기 보존) 정책을 구성하여 확장할 수 있습니다. 자세한 내용은 [장기 백업 보존](long-term-retention-overview.md)을 참조하세요.

## <a name="compare-geo-replication-with-failover-groups"></a>장애 조치(failover) 그룹과 지역 복제 비교

[자동 장애 조치(failover) 그룹은](auto-failover-group-overview.md#terminology-and-capabilities) [지역 복제의](active-geo-replication-overview.md) 배포 및 사용을 간소화하고 다음 표에 설명된 대로 추가 기능을 더합니다.

|                                              | 지역에서 복제 | 장애 조치(failover) 그룹  |
|:---------------------------------------------| :-------------- | :----------------|
| **자동 장애 조치(Failover)**                          |     예          |      예         |
| **여러 데이터베이스를 동시에 장애 조치(failover)**  |     예          |      예         |
| **사용자가 장애 조치(failover) 후에 연결 문자열을 업데이트해야 함**      |     예         |      예          |
| **SQL Managed Instance 지원**                   |     예          |      예         |
| **주 복제본과 동일한 지역에 있을 수 있음**             |     예         |      예          |
| **여러 복제본**                            |     예         |      예          |
| **읽기 확장 지원**                          |     예         |      예         |


## <a name="recover-a-database-to-the-existing-server"></a>기존 서버에 데이터베이스 복구

드물긴 하지만 Azure 데이터 센터가 가동 중단될 수도 있습니다. 가동 중단이 발생하면 몇 분 내지 몇 시간 동안 지속될 수 있는 업무 중단이 발생합니다.

- 한 가지 방법은 데이터 센터 가동 중단이 끝나 데이터베이스가 다시 온라인 상태가 될 때까지 기다리는 것입니다. 이러한 방법은 데이터베이스의 오프라인 유지가 가능한 애플리케이션에 적합합니다. 예를 들어 지속적으로 작업할 필요가 없는 개발 프로젝트 또는 무료 평가판이 있습니다. 데이터 센터가 가동 중단되면 중단이 얼마나 지속될지 알 수 없으므로 이 옵션은 잠시 데이터베이스가 필요 없어도 되는 경우에만 적합합니다.
- 또 다른 옵션은 [지역 중복 데이터베이스 백업](recovery-using-backups.md#geo-restore)(지역 복원)을 사용하여 모든 Azure 지역의 모든 서버에서 데이터베이스를 복원하는 것입니다. 지리적 복원은 지역 중복 백업을 해당 원본으로 사용하고 가동 중단으로 인해 데이터베이스 또는 데이터 센터에 액세스할 수 없는 경우에도 데이터베이스를 복구하는 데 사용될 수 있습니다.
- 마지막으로, 데이터베이스 하나 이상에 [활성 지역 복제](active-geo-replication-overview.md) 를 사용한 지역 복제 또는 [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md) 을 구성한 경우 가동 중단 시 빠르게 복구할 수 있습니다. 이러한 기술의 선택에 따라 수동 또는 자동 장애 조치(failover)를 사용할 수 있습니다. 장애 조치(failover) 자체는 몇 초밖에 걸리지 않지만 서비스에서 장애 조치(failover)를 활성화하려면 최소 1시간이 걸립니다. 가동 중단 규모가 장애 조치(failover)를 수행하기에 적합한지를 확인해야 하기 때문입니다. 또한 비동기 복제의 특성상 장애 조치(failover)로 인해 데이터가 약간 손실될 수도 있습니다.

비즈니스 연속성 계획을 개발할 때는 중단 이벤트가 발생한 후 애플리케이션이 완전히 복구되기까지 허용되는 최대 시간을 이해해야 합니다. 응용 프로그램을 완전히 복구하는 데 필요한 시간을 RTO(복구 시간 목표)라고 합니다. 또한 우발적인 중단 이벤트가 발생한 후 복구될 때 응용 프로그램에서 손실을 허용할 수 있는 최근 데이터 업데이트의 최대 기간(시간 간격)도 감안해야 합니다. 잠재적인 데이터 손실을 RPO(복구 지점 목표)라고 합니다.

복구 방법마다 다른 수준의 RPO 및 RTO를 제공합니다. 특정 복구 방법을 선택하거나 방법을 조합하여 전체 응용 프로그램을 복구할 수 있습니다. 다음 표에서는 각 복구 옵션의 RPO 및 RTO를 비교합니다. 자동 장애 조치(failover) 그룹은 지역에서 복제의 배포 및 사용을 간소화하고 다음 표에 설명된 대로 추가 기능을 더합니다.

| 복구 방법 | RTO | RPO |
| --- | --- | --- |
| 지리적으로 복제된 백업에서 지역 복원 | 12 h | 1 h |
| 자동 장애 조치 그룹 | 1 h | 5초 |
| 수동 데이터베이스 장애 조치(failover) | 30초 | 5초 |

> [!NOTE]
> *수동 데이터베이스 장애 조치(failover)* 는 [계획되지 않은 모드](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities)를 사용하여 단일 데이터베이스를 지역에서 복제된 보조 복제본으로 장애 조치(failover)하는 경우를 가리킵니다.
자동 장애 조치(failover) RTO 및 RPO 세부 정보는 이 문서 앞부분의 표를 참조하세요.

애플리케이션이 다음 조건에 맞는 경우 자동 장애 조치(failover) 그룹을 사용합니다.

- 중요 업무용입니다.
- 12시간 이상의 가동 중지 시간을 허용하지 않는 SLA(서비스 수준 약정)가 있습니다.
- 가동 중지는 재무적 책임을 유발할 수 있습니다.
- 데이터 변경률이 높고 1시간에 해당하는 데이터 손실은 허용할 수 없는 수준입니다.
- 활성 지역 복제를 사용할 때 발생하는 추가적인 비용이 잠재적인 재무적 책임과 연계된 비즈니스 손실보다 낮습니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

응용 프로그램 요구 사항에 따라 데이터베이스 백업 및 활성 지역 복제를 함께 사용하도록 선택할 수 있습니다. 독립 실행형 데이터베이스 및 비즈니스 연속성 기능을 사용하는 탄력적 풀의 설계 고려 사항에 대한 논의는 [클라우드 재해 복구를 위한 응용 프로그램 디자인](designing-cloud-solutions-for-disaster-recovery.md) 및 [탄력적 풀 재해 복구 전략](disaster-recovery-strategies-for-applications-with-elastic-pool.md)을 참조하세요.

다음 섹션에서는 데이터베이스 백업 또는 활성 지역 복제를 사용하는 복구 단계를 대략적으로 설명합니다. 계획의 요구 사항, 복구 후 단계, 가동 중단을 시뮬레이션하여 재해 복구 훈련을 수행하는 방법을 포함한 자세한 단계는 [중단 상태에서 SQL Database의 데이터베이스 복구](disaster-recovery-guidance.md)를 참조하세요.

### <a name="prepare-for-an-outage"></a>가동 중단에 대비

어떤 비즈니스 연속성 기능을 사용하든 관계없이 다음 작업을 수행해야 합니다.

- 서버 수준 IP 방화벽 규칙, 로그인 및 master 데이터베이스 수준 사용 권한을 포함한 대상 서버를 식별하고 준비합니다.
- 클라이언트 및 클라이언트 애플리케이션을 새 서버로 리디렉션하는 방법을 결정합니다.
- 감사 설정 및 경고와 같은 다른 종속성을 문서화합니다.

적절한 준비 없이 장애 조치나 데이터베이스 복구 이후에 애플리케이션을 온라인 상태로 전환하면 추가 시간이 소요되고, 바쁜 업무 중 문제 해결이 필요할 수도 있어 비효율적입니다.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>지역에서 복제된 보조 데이터베이스로 장애 조치

활성 지역 복제 및 자동 장애 조치(failover) 그룹을 복구 메커니즘으로 사용할 경우, 자동 장애 조치(failover) 정책을 구성하거나 [계획되지 않은 수동 장애 조치(failover)](active-geo-replication-configure-portal.md#initiate-a-failover)를 사용할 수 있습니다. 일단 장애 조치가 시작되면 보조 데이터베이스는 새로운 주 데이터베이스로 승격되며, 새 트랜잭션을 기록하고 쿼리에 응답할 준비를 갖춥니다. 이때 최소한의 데이터 손실이 있을 수 있으나 아직 복제되지 않습니다. 장애 조치(failover) 프로세스 디자인에 대한 자세한 내용은 [클라우드 재해 복구를 위해 애플리케이션 디자인](designing-cloud-solutions-for-disaster-recovery.md)을 참조하세요.

> [!NOTE]
> 데이터 센터가 다시 온라인 상태가 되면 이전 주 데이터베이스는 새 주 데이터베이스에 자동으로 다시 연결되고 보조 데이터베이스가 됩니다. 주 데이터베이스를 다시 원래 지역으로 재배치해야 할 경우 계획된 장애 조치를 수동으로 시작할 수 있습니다(장애 복구).

### <a name="perform-a-geo-restore"></a>지역 복원 수행

지역 중복 스토리지(기본적으로 사용)와 함께 자동화된 백업을 사용하는 경우 [지역 복원](disaster-recovery-guidance.md#recover-using-geo-restore)을 사용하여 데이터베이스를 복구할 수 있습니다. 일반적으로 복구는 12시간 이내에 수행됩니다. 이때 마지막 로그 백업의 실행 및 복제를 기준으로 최대 1시간의 데이터 손실이 있을 수 있습니다. 복구가 완료될 때까지 데이터베이스는 어떤 트랜잭션도 기록할 수 없으며 쿼리에 응답할 수 없습니다. 지역 복원은 데이터베이스를 마지막으로 사용할 수 있는 시점으로만 복원합니다.

> [!NOTE]
> 응용 프로그램이 복구된 데이터베이스로 전환하기 전에 데이터 센터가 다시 온라인 상태가 되면 복구를 취소할 수 있습니다.

### <a name="perform-post-failover--recovery-tasks"></a>사후 장애 조치(failover)/복구 작업 수행

복구 메커니즘에서 복구한 후에는 사용자 및 애플리케이션이 다시 실행되기 전에 다음과 같은 추가 작업을 수행해야 합니다.

- 클라이언트 및 클라이언트 응용 프로그램을 새 서버 및 복원된 데이터베이스로 리디렉션합니다.
- 사용자가 연결할 수 있는 또는 [데이터베이스 수준 방화벽](firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)을 사용하여 적절한 규칙을 설정할 수 있는 적절한 서버 수준 IP 방화벽 규칙이 적용되고 있는지 확인합니다.
- 적절한 로그인 및 master 데이터베이스 수준 사용 권한이 설정되었는지 확인합니다(또는 [포함된 사용자](/sql/relational-databases/security/contained-database-users-making-your-database-portable)를 사용합니다).
- 필요에 따라 감사를 구성합니다.
- 필요에 따라 경고를 구성합니다.

> [!NOTE]
> 장애 조치(failover) 그룹을 사용 중이며 읽기-쓰기 수신기를 사용하여 데이터베이스에 연결하는 경우에는 장애 조치(failover) 후의 리디렉션이 응용 프로그램에 투명하게 자동으로 수행됩니다.

## <a name="upgrade-an-application-with-minimal-downtime"></a>최소 가동 중단으로 애플리케이션 업그레이드

애플리케이션 업그레이드와 같은 계획된 유지 관리로 인해 애플리케이션을 오프라인 상태로 전환해야 하는 경우도 있습니다. [애플리케이션 업그레이드 관리](manage-application-rolling-upgrade.md)에서는 활성 지역 복제를 사용하여 클라우드 애플리케이션의 롤링 업그레이드를 사용하도록 설정함으로써 업그레이드 기간에 가동 중지 시간을 최소화하고 오류가 발생한 경우 복구 경로를 제공하는 방법을 설명합니다.

## <a name="next-steps"></a>다음 단계

독립 실행형 데이터베이스 및 탄력적 풀에 대한 응용 프로그램 디자인 고려 사항에 대한 논의는 [클라우드 재해 복구를 위한 응용 프로그램 디자인](designing-cloud-solutions-for-disaster-recovery.md) 및 [탄력적 풀 재해 복구 전략](disaster-recovery-strategies-for-applications-with-elastic-pool.md)을 참조하세요.
