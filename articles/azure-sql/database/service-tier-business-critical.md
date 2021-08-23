---
title: 중요 비즈니스 서비스 계층
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database 및 Azure SQL Managed Instance에 대한 중요 비즈니스용 서비스 계층에 대해 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: mathoma, urmilano
ms.date: 12/04/2018
ms.openlocfilehash: 5c4ea72c94e71e0c57c0d88e5f8324111ae151c2
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112119700"
---
# <a name="business-critical-tier---azure-sql-database-and-azure-sql-managed-instance"></a>중요 비즈니스용 계층 - Azure SQL Database 및 Azure SQL Managed Instance 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 중요 비즈니스용 계층은 DTU 구매 모델에서 프리미엄이라고 합니다. vCore 기반 구매 모델과 DTU 기반 구매 모델의 비교는 [Azure SQL Database 구매 모델 및 리소스](purchasing-models.md)를 참조하세요.

Azure SQL Database와 Azure SQL Managed Instance는 모두 인프라 오류가 발생하는 경우에도 99.99%의 가용성을 보장하기 위해 클라우드 환경에 맞게 조정되는 SQL Server 데이터베이스 엔진 아키텍처를 기반으로 합니다. 다음과 같은 세 가지 아키텍처 모델이 사용됩니다.
- 범용/표준 
- 중요 비즈니스용/프리미엄
- 하이퍼스케일

프리미엄/중요 비즈니스용 서비스 계층 모델은 데이터베이스 엔진 프로세스의 클러스터를 기반으로 합니다. 이 아키텍처 모델은 항상 사용 가능한 데이터베이스 엔진 노드의 쿼럼이 있다는 사실에 의존하며 유지 보수 작업 중에도 워크로드에 최소한의 성능 영향을 줍니다. 하이퍼스케일 서비스 계층은 현재 Azure SQL Database(SQL Managed Instance 아님)에서만 사용할 수 있으며, Azure 아키텍처를 활용하여 범용 및 중요 비즈니스용 서비스 계층에 사용할 수 있는 제한을 넘어 Azure SQL Database의 데이터베이스에 대한 스토리지 및 컴퓨팅 리소스를 확장하는 확장성이 뛰어난 스토리지 및 컴퓨팅 성능 계층입니다.

Azure는 최종 사용자의 가동 중단 시간을 최소화하면서 기본 운영 체제, 드라이버 및 SQL Server 데이터베이스 엔진을 업그레이드하고 패치를 적용합니다. 

프리미엄 가용성은 프리미엄 및 중요 비즈니스용 서비스 계층에서 사용할 수 있으며 지속적인 유지 관리 작업으로 인해 성능에 미치는 영향을 감당할 수 없는 집약적인 워크로드에 맞게 설계되었습니다.

컴퓨팅 및 스토리지는 프리미엄 모델의 단일 노드에 통합됩니다. 이 아키텍처 모델의 고가용성은 SQL Server [Always On 가용성 그룹](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)과 유사한 기술을 사용하여 4개의 노드 클러스터에 배포된 컴퓨팅(SQL Server 데이터베이스 엔진 프로세스) 및 스토리지(로컬로 장착된 SSD)의 복제를 통해 이루어집니다.

![데이터베이스 엔진 노드의 클러스터](./media/service-tier-business-critical/business-critical-service-tier.png)

SQL Server 데이터베이스 엔진 프로세스와 기본 mdf/ldf 파일이 SSD 스토리지가 로컬에 연결되어 있는 동일한 노드에 모두 배치되기 때문에 워크로드에 대한 대기 시간이 짧습니다. SQL Server [Always On 가용성 그룹](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)과 유사한 기술을 사용하여 고가용성이 구현됩니다. 모든 데이터베이스는 고객 워크로드에 액세스할 수 있는 주 데이터베이스 하나와 데이터 복사본을 포함하는 보조 프로세스 3개가 있는 데이터베이스 노드의 클러스터입니다. 주 노드는 어떤 이유로든 주 노드의 작동이 실패하는 경우 보조 복제본의 데이터를 사용할 수 있도록 하기 위해 변경 내용을 보조 노드로 지속적으로 푸시합니다 장애 조치(failover)는 SQL Server 데이터베이스 엔진을 통해 처리됩니다. 보조 복제본 하나가 주 노드가 되고 클러스터에 충분한 노드를 보장하기 위해 새로운 보조 복제본이 만들어집니다. 워크로드는 새로운 주 노드에 자동으로 리디렉션됩니다.

또한 중요 비즈니스용 클러스터에는 기본 워크로드의 성능에 영향을 주면 안 되는 읽기 전용 쿼리(예: 보고서)를 실행하는 데 사용될 수 있는 기본 읽기 전용 노드를 무료로 제공하는 [읽기 확장](read-scale-out.md) 기능이 기본으로 제공됩니다.

## <a name="when-to-choose-this-service-tier"></a>이 서비스 계층을 선택하는 시기

중요 비즈니스용 서비스 계층은 기본 SSD 스토리지에서 짧은 대기 시간 응답(평균1 ~ 2밀리초) 및 기본 인프라가 실패하는 경우 빠른 복구가 필요하거나, 보고서와 분석은 물론, 읽기 전용 쿼리를 무료로 읽을 수 있는 주 데이터베이스의 보조 복제본으로 오프로드할 필요성이 있는 애플리케이션용으로 설계되었습니다.

범용 계층 대신 중요 비즈니스용 서비스 계층을 선택해야 하는 주요 이유는 다음과 같습니다.
-    **낮은 I/O 대기 시간 요구 사항** – 스토리지 레이어에서 빠른 응답(평균 1~2밀리초)이 필요한 워크로드는 중요 비즈니스용 계층을 사용해야 합니다. 
-    **과 데이터베이스 간의 빈번한 통신** 애플리케이션 레이어 캐싱 또는 [요청 일괄 처리](../performance-improve-use-batching.md)를 사용할 수 없으며 신속하게 처리해야 할 많은 SQL 쿼리를 전송해야 하는 애플리케이션은 중요 비즈니스용 계층에 적합합니다.
-    **많은 수의 업데이트** – 삽입, 업데이트 및 삭제 작업은 `CHECKPOINT` 작업을 수행해 데이터 파일에 저장해야 하는 메모리(더티 페이지)의 데이터 페이지를 수정합니다. 잠재적인 데이터베이스 엔진 프로세스 충돌 또는 많은 수의 더티 페이지가 있는 데이터베이스의 장애 조치(failover)로 인해 범용 계층에서 복구 시간이 늘어날 수 있습니다. 많은 메모리 내 변경을 유발하는 워크로드가 있는 경우에는 중요 비즈니스용 계층을 사용합니다. 
-    **데이터를 수정하는 장기 실행 트랜잭션** 긴 시간 동안 열린 트랜잭션은 로그 파일 잘림을 방지하여 로그 크기와 [VLF(가상 로그 파일)](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch) 수를 늘릴 수 있습니다. VLF 수가 많으면 장애 조치(failover) 후 데이터베이스의 복구 속도가 느려질 수 있습니다.
-    무료 보조 읽기 전용 복제본으로 리디렉션될 수 있는 **보고 및 분석 쿼리 워크로드** 입니다.
- **복원력이 향상되고 오류를 신속하게 복구** 합니다. 시스템 오류가 발생하면 주 인스턴스의 데이터베이스가 사용하지 않도록 설정되고 보조 복제본 중 하나는 쿼리를 처리할 준비가 된 새로운 읽기/쓰기 주 데이터베이스가 됩니다. 데이터베이스 엔진은 로그 파일에서 트랜잭션을 분석하고 다시 실행하거나 메모리 버퍼의 모든 데이터를 로드할 필요가 없습니다.
- **고급 데이터 손상 방지** 중요 비즈니스용 계층은 비즈니스 연속성을 위해 백그라운드에서 데이터베이스 복제본을 활용하므로 서비스는 SQL Server 데이터베이스 [미러링 및 가용성 그룹](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)에 사용되는 것과 동일한 기술인 자동 페이지 복구도 활용합니다. 복제본이 데이터 무결성 문제로 인해 페이지를 읽을 수 없는 경우 다른 복제본에서 페이지의 새 복사본을 검색하여 데이터 손실 또는 고객의 가동 중지 시간 없이 읽을 수 없는 페이지를 대체합니다. 이 기능은 데이터베이스에 지역 보조 복제본이 있는 경우 범용 계층에서 적용할 수 있습니다.
- **고가용성** - 다중 AZ 구성의 중요 비즈니스용 계층은 범용 계층의 99.99%에 비해 99.995%의 가용성을 보장합니다.
- **빠른 지역 복구** - 지역 복제로 구성된 중요 비즈니스용 계층은 배포된 전체 시간에 대해 5초의 RPO(복구 지점 목표) 및 30초의 RTO(복구 시간 목표)를 보장합니다.

## <a name="next-steps"></a>다음 단계

- [SQL Managed Instance](../managed-instance/resource-limits.md#service-tier-characteristics)의 중요 비즈니스용 계층, [vCore 모델](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) 또는 [DTU 모델](resource-limits-dtu-single-databases.md#premium-service-tier)의 단일 데이터베이스 또는 [vCore 모델](resource-limits-vcore-elastic-pools.md#business-critical---provisioned-compute---gen4) 및 [DTU 모델](resource-limits-dtu-elastic-pools.md#premium-elastic-pool-limits)에 대한 탄력적 풀의 리소스 특성(코어 수, I/O, 메모리)을 찾습니다.
- [범용](service-tier-general-purpose.md) 및 [하이퍼스케일](service-tier-hyperscale.md) 계층에 대해 알아봅니다.
- [Service Fabric](../../service-fabric/service-fabric-overview.md)에 대해 알아봅니다.
- 고가용성 및 재해 복구에 대한 자세한 옵션은 [비즈니스 연속성](business-continuity-high-availability-disaster-recover-hadr-overview.md)을 참조하세요.