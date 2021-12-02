---
title: Azure의 Oracle 데이터베이스에 대한 참조 아키텍처 | Microsoft Docs
description: Microsoft Azure Virtual Machines에서 Oracle Database Enterprise Edition 데이터베이스를 실행하는 방법에 대한 참조 아키텍처입니다.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 12/13/2019
ms.author: kegorman
ms.openlocfilehash: 22a96fe857eb81c7059e062a64e01035977d7c58
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133438804"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Azure의 Oracle Database Enterprise Edition에 대한 참조 아키텍처

**적용 대상:** :heavy_check_mark: Linux VM 

이 가이드에서는 Azure에 고가용성 Oracle 데이터베이스를 배포하는 방법을 자세히 설명합니다. 또한 이 가이드에서는 재해 복구 시 고려 사항을 자세히 살펴봅니다. 이러한 아키텍처는 고객 배포를 기반으로 작성되었습니다. 이 가이드는 Oracle Database Enterprise Edition에만 적용됩니다.

Oracle 데이터베이스의 성능을 최대화하는 방법에 관심이 있는 경우 [Oracle DB 설계](oracle-design.md)를 참조하세요.

## <a name="assumptions"></a>가정

- [가용성 영역](../../../availability-zones/az-overview.md)과 같은 Azure의 여러 개념을 이해하고 있습니다.
- Oracle Database Enterprise Edition 12c 이상을 실행하고 있습니다.
- 이 문서의 솔루션을 사용할 때 적용되는 라이선스 영향을 알고 있으며 인정합니다.

## <a name="high-availability-for-oracle-databases"></a>Oracle 데이터베이스의 고가용성

클라우드에서 고가용성을 달성하는 것은 모든 조직의 계획 및 설계에서 중요한 부분입니다. Microsoft Azure는 [가용성 영역](../../../availability-zones/az-overview.md) 및 가용성 집합(가용성 영역을 사용할 수 없는 지역에서 사용)을 제공합니다. 클라우드용으로 설계하려면 [가상 머신의 가용성 관리](../../availability.md)에 대해 자세히 알아보세요.

Oracle은 클라우드 네이티브 도구 및 제품 외에도 Azure에서 설정할 수 있는 [Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7), [FSFO를 사용하는 Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [분할](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html), [GoldenGate](https://www.oracle.com/middleware/technologies/goldengate.html) 등의 고가용성 솔루션을 제공합니다. 이 가이드에서는 이러한 각 솔루션의 참조 아키텍처를 설명합니다.

마지막으로, 클라우드용 애플리케이션을 마이그레이션하거나 만들 때 [재시도 패턴](/azure/architecture/patterns/retry) 및 [회로 차단기 패턴](/azure/architecture/patterns/circuit-breaker)과 같은 클라우드 네이티브 패턴을 추가하도록 애플리케이션 코드를 수정해야 합니다. [클라우드 디자인 패턴 가이드](/azure/architecture/patterns/)에 정의된 추가 패턴은 애플리케이션의 복원력을 향상하는 데 도움이 됩니다.

### <a name="oracle-rac-in-the-cloud"></a>클라우드의 Oracle RAC

Oracle RAC(Real Application Cluster)는 여러 인스턴스가 단일 데이터베이스 스토리지(전체 공유 아키텍처 패턴)에 액세스하도록 하여 고객이 높은 처리량을 얻을 수 있게 도와주는 Oracle의 솔루션입니다. Oracle RAC는 온-프레미스 고가용성에도 사용할 수 있지만, Oracle RAC 단독으로는 랙 수준 또는 데이터 센터 수준 오류가 아닌 인스턴스 수준 오류로부터 보호하므로 클라우드 고가용성에는 사용할 수 없습니다. 이러한 이유로 Oracle에서는 단일 인스턴스든 아니면 RAC든 상관없이 고가용성을 위해 데이터베이스에 Oracle Data Guard를 사용할 것을 권장합니다. 일반적으로 고객은 중요 업무용 애플리케이션을 실행하기 위해 높은 SLA를 요구합니다. Oracle RAC는 현재 Azure의 Oracle에서 인증되었거나 지원되지 않습니다. 그러나 Azure는 인스턴스 수준 오류로부터 보호하는 데 도움이 되는 Azure 제품 가용성 영역 및 계획된 유지 관리 기간 등의 기능을 제공합니다. 이 외에도 고객은 랙 수준은 물론이고 데이터 센터 수준 오류와 지리 정치적 오류로부터 데이터베이스를 보호하여 높은 성능과 복원력을 제공하는 Oracle Data Guard, Oracle GoldenGate, Oracle 분할 등의 기술을 사용할 수 있습니다.

여러 [가용성 영역](../../../availability-zones/az-overview.md)에서 Oracle Data Guard 또는 GoldenGate와 함께 Oracle 데이터베이스를 실행하는 고객은 99.99%의 작동 시간 SLA를 얻을 수 있습니다. 가용성 영역이 아직 없는 Azure 지역의 고객은 [가용성 집합](../../availability-set-overview.md)을 사용하여 99.95%의 작동 시간 SLA를 달성할 수 있습니다.

>참고: 고객의 작동 시간 목표가 Microsoft에서 제공하는 작동 시간 SLA보다 훨씬 높을 수 있습니다.

## <a name="disaster-recovery-for-oracle-databases"></a>Oracle 데이터베이스의 재해 복구

중요 업무용 애플리케이션을 클라우드에 호스팅하는 경우 고가용성 및 재해 복구를 설계하는 것이 중요합니다.

Oracle Database Enterprise Edition의 경우 Oracle Data Guard는 재해 복구를 위한 유용한 기능입니다. [쌍을 이루는 Azure 지역](../../../availability-zones/cross-region-replication-azure.md)에 대기 데이터베이스 인스턴스를 설정하고 재해 복구를 위한 Data Guard 장애 조치(failover)를 설정할 수 있습니다. 데이터가 손실되지 않도록 하려면 Active Data Guard 외에도 Oracle Data Guard Far Sync 인스턴스를 배포하는 것이 좋습니다. 

애플리케이션에서 대기 시간을 허용하는 경우(철저한 테스트가 필요함) Oracle 주 데이터베이스와 다른 가용성 영역에 Data Guard Far Sync 인스턴스를 설정하는 것을 고려해 볼 수 있습니다. **최대 가용성** 모드를 사용하여 다시 실행 파일을 Far Sync 인스턴스에 동기 전송하도록 설정합니다. 그러면 이러한 파일은 대기 데이터베이스에 비동기적으로 전송됩니다. 

**최대 가용성** 모드(동기)의 다른 가용성 영역에 Far Sync 인스턴스를 설정할 때 애플리케이션에서 성능 손실을 허용하지 않는 경우에는 주 데이터베이스와 동일한 가용성 영역에 Far Sync 인스턴스를 설정하면 됩니다. 주 데이터베이스와 가까이 있는 Far Sync 인스턴스를 여러 개 설정하고 대기 데이터베이스와 가까이 있는 인스턴스를 하나 이상 설정하면 가용성을 더욱 높일 수 있습니다(역할이 전환되는 경우). 이 [Oracle Active Data Guard Far Sync 백서](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)에서 Oracle Data Guard Far Sync에 대해 자세히 알아보세요.

Oracle Standard Edition 데이터베이스를 사용하는 경우 고가용성 및 재해 복구를 설정할 수 있는 DBVisit Standby와 같은 ISV 솔루션이 있습니다.

## <a name="reference-architectures"></a>참조 아키텍처

### <a name="oracle-data-guard"></a>Oracle 데이터 가드

Oracle Data Guard는 엔터프라이즈 데이터의 고가용성, 데이터 보호 및 재해 복구를 보장합니다. Data Guard는 대기 데이터베이스를 트랜잭션 측면에서 일관적인 주 데이터베이스 복사본으로 유지 관리합니다. 주 데이터베이스와 보조 데이터베이스 간의 거리와 애플리케이션에서 허용하는 대기 시간에 따라 동기 또는 비동기 복제를 설정할 수 있습니다. 그러면 계획된 중단 또는 계획되지 않은 중단으로 인해 주 데이터베이스를 사용할 수 없게 될 때 Data Guard가 대기 데이터베이스를 주 역할로 전환하여 가동 중지 시간을 최소화할 수 있습니다. 

Oracle Data Guard를 사용하는 경우 읽기 전용으로 보조 데이터베이스를 열 수도 있습니다. 이 구성을 Active Data Guard라고 합니다. Oracle Database 12c에서는 Data Guard Far Sync 인스턴스라는 기능을 도입했습니다. 이 인스턴스를 사용하면 성능에 영향을 주지 않고 Oracle 데이터베이스의 데이터 무손실 구성을 설정할 수 있습니다.

> [!NOTE]
> Active Data Guard를 사용하려면 추가 라이선스가 필요합니다. 이 라이선스는 Far Sync 기능에도 필요합니다. 라이선스에 대한 자세한 내용은 Oracle 담당자에게 문의하세요.

#### <a name="oracle-data-guard-with-fsfo"></a>FSFO를 사용하는 Oracle Data Guard
FSFO(Fast-Start Failover)를 사용하는 Oracle Data Guard는 별도의 머신에 broker를 설정하여 추가 복원력을 제공할 수 있습니다. Data Guard broker와 보조 데이터베이스는 모두 관찰자를 실행하여 주 데이터베이스의 가동 중지 시간을 관찰합니다. 따라서 Data Guard 관찰자 설정에서도 중복성이 구현됩니다. 

Oracle Database 12.2 이상 버전에서는 Oracle Data Guard broker 하나에 여러 관찰자를 구성할 수도 있습니다. 이렇게 설정하면 한 관찰자와 보조 데이터베이스에서 가동 중지 시간이 발생할 때 추가 가용성을 제공합니다. Data Guard Broker는 가볍기 때문에 비교적 작은 가상 머신에 호스트할 수 있습니다. Data Guard Broker 및 장점에 대한 자세한 내용은 이 토픽의 [Oracle 설명서](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)를 참조하세요.

다음 다이어그램은 가용성 영역을 사용하여 Azure에서 Oracle Data Guard를 사용하기 위한 권장 아키텍처입니다. 이 아키텍처를 통해 99.99%의 VM 작동 시간 SLA를 얻을 수 있습니다.

![다음 다이어그램은 가용성 영역을 사용하여 Azure에서 Oracle Data Guard를 사용하기 위한 권장 아키텍처를 보여줍니다.](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

이전 다이어그램에서는 클라이언트 시스템이 Oracle 백 엔드에서 웹을 통해 사용자 지정 애플리케이션에 액세스합니다. 웹 프런트 엔드는 부하 분산 장치에 구성됩니다. 웹 프런트 엔드는 해당 애플리케이션 서버를 호출하여 작업을 처리합니다. 애플리케이션 서버는 주 Oracle 데이터베이스를 쿼리합니다. Oracle 데이터베이스는 라이선스 비용을 절감하고 성능을 최대화하기 위해 [제약이 있는 코어 vCPU](../../../virtual-machines/constrained-vcpu.md)가 포함된 하이퍼스레드 [메모리 최적화 가상 머신](../../sizes-memory.md)을 사용하여 구성되었습니다. 성능과 고가용성을 위해 여러 프리미엄 또는 울트라 디스크(Managed Disks)가 사용됩니다.

Oracle 데이터베이스는 고가용성을 위해 여러 가용성 영역에 배치됩니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 복원력을 보장하려면 활성화된 모든 지역에서 적어도 3개의 별도 영역이 필요합니다. Azure 지역 내에서 가용성 영역을 물리적으로 분리하면 데이터를 데이터 센터 오류로부터 보호할 수 있습니다. 또한 중단이 발생하면 데이터베이스를 시작하고 보조 데이터베이스로 장애 조치(failover)하도록 두 개의 가용성 영역에 두 개의 FSFO 관찰자가 설정되었습니다. 

이전 아키텍처의 영역과 다른 가용성 영역에(이 예제에서는 AZ 1) 추가 관찰자 및/또는 대기 데이터베이스를 설정할 수 있습니다. 마지막으로 OEM(Oracle Enterprise Manager)이 Oracle 데이터베이스의 작동 시간 및 성능을 모니터링합니다. 또한 OEM에서 다양한 성능 및 사용량 보고서를 생성할 수 있습니다.

가용성 영역이 지원되지 않는 지역에서는 가용성 집합을 사용하여 Oracle 데이터베이스를 고가용성 방식으로 배포할 수 있습니다. 가용성 집합을 사용하면 VM 작동 시간 99.95%를 달성할 수 있습니다. 다음 다이어그램은 이 사용법의 참조 아키텍처입니다.

![Data Guard Broker - FSFO에서 가용성 집합을 사용하는 Oracle 데이터베이스](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * OEM 인스턴스를 하나만 배포하므로 Oracle Enterprise Manager VM을 가용성 집합에 배치할 필요가 없습니다.
> * 현재는 가용성 집합 구성에서 울트라 디스크를 지원하지 않습니다.

#### <a name="oracle-data-guard-far-sync"></a>Oracle Data Guard Far Sync

Oracle Data Guard Far Sync는 Oracle 데이터베이스에 데이터 무손실 보호 기능을 제공합니다. 이 기능을 사용하면 데이터베이스 머신에서 오류가 발생할 때 데이터 손실을 방지할 수 있습니다. Oracle Data Guard Far Sync는 별도의 VM에 설치해야 합니다. Far Sync는 제어 파일, 암호 파일, spfile 및 대기 로그만 있는 가벼운 Oracle 인스턴스입니다. 데이터 파일이나 rego 로그 파일은 없습니다. 

데이터 무손실 보호를 위해 주 데이터베이스와 Far Sync 인스턴스 간에 동기 통신이 필요합니다. Far Sync 인스턴스는 동기 방식으로 주 데이터베이스에서 다시 실행을 받아서 모든 대기 데이터베이스에 즉시 비동기 방식으로 전달합니다. 뿐만 아니라 이 설정은 모든 대기 데이터베이스가 아닌 Far Sync 인스턴스에만 다시 실행을 보내면 되기 때문에 주 데이터베이스의 오버헤드를 줄입니다. Far Sync 인스턴스에서 오류가 발생하면 Data Guard는 거의 무손실에 가까운 데이터 보호를 유지하기 위해 자동으로 주 데이터베이스에서 보조 데이터베이스로 비동기 전송을 사용합니다. 복원력을 더 높이려는 고객은 각 데이터베이스 인스턴스(주 데이터베이스 및 보조 데이터베이스)마다 여러 개의 Far Sync 인스턴스를 배포하면 됩니다.

다음 다이어그램은 Oracle Data Guard Far Sync를 사용하는 고가용성 아키텍처입니다.

![Data Guard Far Sync 및 Broker - FSFO에서 가용성 집합을 사용하는 Oracle 데이터베이스](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

위의 아키텍처는 둘 사이의 대기 시간을 줄이기 위해 데이터베이스 인스턴스와 동일한 가용성 영역에 Far Sync 인스턴스를 배포했습니다. 애플리케이션의 대기 시간이 중요한 경우에는 데이터베이스와 Far Sync 인스턴스를 [근접 배치 그룹](../../../virtual-machines/linux/proximity-placement-groups.md)에 배포하는 것이 좋습니다.

다음 다이어그램은 고가용성 및 재해 복구를 달성하기 위해 Oracle Data Guard FSFO와 Far Sync를 활용하는 아키텍처입니다.

![재해 복구를 위해 Data Guard Far Sync 및 Broker - FSFO에서 가용성 집합을 사용하는 Oracle 데이터베이스](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

GoldenGate를 사용하면 엔터프라이즈 전체의 여러 이기종 플랫폼 간에 데이터를 트랜잭션 수준에서 교환 및 조작할 수 있습니다. GoldenGate는 기존 인프라의 트랜잭션 무결성 및 최소 오버헤드로 커밋된 트랜잭션을 이동합니다. GoldenGate의 모듈식 아키텍처는 다양한 토폴로지에서 선택한 데이터 레코드, 트랜잭션 변경 내용, DDL(데이터 정의 언어) 변경 내용을 유연하게 추출하고 복제할 수 있습니다.

Oracle GoldenGate를 사용하면 양방향 복제를 제공하여 고가용성 데이터베이스를 구성할 수 있습니다. 이렇게 하면 **다중 마스터** 또는 **활성-활성 구성** 을 설정할 수 있습니다. 다음 다이어그램은 Azure의 Oracle GoldenGate 활성-활성 설정에 권장되는 아키텍처입니다. 다음 아키텍처에서 Oracle 데이터베이스는 라이선스 비용을 절감하고 성능을 최대화하기 위해 [제약이 있는 코어 vCPU](../../../virtual-machines/constrained-vcpu.md)가 포함된 하이퍼스레드 [메모리 최적화 가상 머신](../../sizes-memory.md)을 사용하여 구성되었습니다. 성능과 고가용성을 위해 여러 프리미엄 또는 울트라 디스크(관리 디스크)가 사용됩니다.

![Data Guard Broker - FSFO에서 가용성 영역을 사용하는 Oracle 데이터베이스](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> 현재 가용성 영역을 사용할 수 없는 지역에서는 가용성 집합을 사용하여 비슷한 아키텍처를 설정할 수 있습니다.

Oracle GoldenGate에는 Oracle 데이터베이스 서버 간에 데이터를 비동기적으로 복제할 수 있는 추출, 펌프, Replicat 등의 프로세스가 있습니다. 이러한 프로세스를 통해 양방향 복제를 설정하면 가용성 영역 수준에서 가동 중지 시간이 발생하더라도 데이터베이스의 고가용성을 보장할 수 있습니다. 위의 다이어그램에서 추출 프로세스는 Oracle 데이터베이스와 동일한 서버에서 실행되는 반면, 데이터 펌프 및 Replicat 프로세스는 동일한 가용성 영역에 있는 별도의 서버에서 실행됩니다. Replicat 프로세스는 다른 가용성 영역에 있는 데이터베이스에서 데이터를 수신하여 해당 가용성 영역의 Oracle 데이터베이스에 데이터를 커밋하는 데 사용됩니다. 마찬가지로, 데이터 펌프 프로세스는 추출 프로세스에서 추출한 데이터를 다른 가용성 영역의 Replicat 프로세스로 보냅니다. 

위의 아키텍처 다이어그램은 별도의 서버에 구성된 데이터 펌프 및 Replicat 프로세스를 보여주지만, 서버의 용량과 사용량에 따라 동일한 서버에 모든 Oracle GoldenGate 프로세스를 설정할 수도 있습니다. 서버의 사용량 패턴을 파악하려면 항상 Azure에서 AWR 보고서와 메트릭을 확인하세요.

다른 가용성 영역 또는 다른 지역에 Oracle GoldenGate 양방향 복제를 설정하는 경우 서로 다른 구성 요소 간의 대기 시간이 애플리케이션에서 허용하는 수준이어야 합니다. 가용성 영역 및 지역 간의 대기 시간은 다양한 요인에 따라 달라질 수 있습니다. 서로 다른 가용성 영역 및/또는 지역에 있는 애플리케이션 계층과 데이터베이스 계층 간에 성능 테스트를 설정하여 애플리케이션 성능 요구 사항을 충족하는지 확인하는 것이 좋습니다.

애플리케이션 계층은 자체 서브넷에 설정할 수 있으며 데이터베이스 계층은 자체 서브넷에 분리할 수 있습니다. 가능하다면 [Azure Application Gateway](../../../application-gateway/overview.md)를 사용하여 애플리케이션 서버 간에 트래픽을 분산하는 것이 좋습니다. Azure Application Gateway는 견고한 웹 트래픽 부하 분산 장치입니다. 사용자 세션을 동일한 서버에 유지하여 데이터베이스의 충돌을 최소화하는 쿠키 기반 세션 선호도를 제공합니다. Application Gateway의 대안은 [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) 및 [Azure Traffic Manager](../../../traffic-manager/traffic-manager-overview.md)입니다.

### <a name="oracle-sharding"></a>Oracle 분할

분할은 Oracle 12.2에서 도입된 데이터 계층 패턴입니다. 분할을 통해 독립 데이터베이스의 데이터를 수평으로 분할하고 스케일링할 수 있습니다. 이 아키텍처는 각 데이터베이스가 전용 가상 머신에 호스트되는 비공유 아키텍처이며, 복원력과 가용성이 향상될 뿐 아니라 높은 읽기 및 쓰기 처리량을 제공합니다. 이 패턴은 단일 실패 지점이 없으며, 오류 격리를 제공하고 가동 중지 시간 없이 롤링 업그레이드가 가능합니다. 익스텐트 하나의 가동 중지 시간 또는 데이터 센터 수준 오류의 가동 중지 시간은 다른 데이터 센터에 있는 다른 익스텐트의 성능 또는 가용성에 영향을 주지 않습니다. 

분할은 처리량이 많아서 가동 중지 시간을 허용할 수 없는 OLTP 애플리케이션에 적합합니다. 분할 키가 동일한 모든 행은 항상 동일한 익스텐트에 있으므로 성능이 향상되어 높은 일관성을 제공합니다. 분할을 사용하는 애플리케이션에서는 분할 키(예: *customerId* 또는 *accountNum*)를 사용하여 주로 데이터에 액세스하는 데이터 모델 및 데이터 배포 전략(일관적인 해시, 범위, 목록 또는 복합)을 잘 정의해야 합니다. 또한 분할을 사용하여 특정 데이터 세트를 최종 고객과 더 가까이에 저장하면 성능 및 규정 준수 요구 사항을 충족하는 데 도움이 됩니다.

고가용성 및 재해 복구를 위해 익스텐트를 복제하는 것이 좋습니다. 이 설정은 Oracle Data Guard 또는 Oracle GoldenGate와 같은 Oracle 기술을 사용하여 수행할 수 있습니다. 복제 단위는 익스텐트, 익스텐트의 일부 또는 익스텐트 그룹입니다. 하나 이상의 익스텐트가 중단되거나 속도가 저하되어도 분할된 데이터베이스의 가용성에는 영향을 주지 않습니다. 고가용성을 원하는 경우 주 익스텐트가 배치된 가용성 영역에 대기 익스텐트를 배치하면 됩니다. 재해 복구를 원하는 경우 대기 익스텐트가 다른 지역에 있어도 됩니다. 익스텐트를 여러 지역에 배포하여 해당 지역의 트래픽을 처리할 수도 있습니다. [Oracle 분할 설명서](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html)에서 분할된 데이터베이스의 고가용성 및 복제를 구성하는 방법에 대해 자세히 알아보세요.

Oracle 분할은 주로 다음과 같은 구성 요소로 구성됩니다. 이러한 구성 요소에 대한 자세한 내용은 [Oracle 분할 설명서](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)에서 찾을 수 있습니다.

- **익스텐트 카탈로그** - 특수 용도의 Oracle 데이터베이스로, 모든 익스텐트 데이터베이스 구성 데이터를 저장하는 영구 저장소입니다. 익스텐트 추가 또는 제거, 데이터 매핑, 익스텐트 데이터베이스의 DDL과 같은 모든 구성 변경은 익스텐트 카탈로그에서 시작됩니다. 익스텐트 카탈로그에는 SDB에 있는 모든 중복 테이블의 마스터 복사본도 포함됩니다. 

  익스텐트 카탈로그는 구체화된 뷰를 사용하여 모든 익스텐트에 있는 중복 테이블의 변경 내용을 자동으로 복제합니다. 또한 익스텐트 카탈로그 데이터베이스는 분할 키를 지정하지 않는 다중 익스텐트 쿼리를 처리하는 데 사용되는 쿼리 코디네이터 역할을 합니다. 
  
  익스텐트 카탈로그 고가용성을 위해 Oracle Data Guard를 가용성 영역 또는 가용성 집합과 함께 사용하는 것이 가장 좋습니다. 익스텐트 카탈로그의 가용성은 분할된 데이터베이스의 가용성에 영향을 주지 않습니다. 익스텐트 카탈로그의 가동 중지 시간은 ata Guard 장애 조치(failover)가 완료되는 짧은 기간 동안 유지 관리 작업 및 다중 익스텐트 쿼리에만 영향을 줍니다. 온라인 트랜잭션은 SDB에서 계속 라우팅되고 실행되며 카탈로그 중단의 영향을 받지 않습니다.

- **익스텐트 디렉터** - 익스텐트가 상주하는 각 지역/가용성 영역에 배포해야 하는 가벼운 서비스입니다. 익스텐트 디렉터는 Oracle 분할의 컨텍스트에서 배포되는 글로벌 서비스 관리자입니다. 고가용성을 위해 익스텐트가 있는 각 가용성 영역에 하나 이상의 익스텐트 디렉터를 배포하는 것이 좋습니다. 

  데이터베이스에 처음 연결할 때 익스텐트 디렉터를 통해 라우팅 정보가 설정되고 후속 요청에서 익스텐트 디렉터를 우회하도록 캐시됩니다. 익스텐트와 세션이 설정되면 모든 SQL 쿼리와 DML이 지원되고 지정된 익스텐트 범위에서 실행됩니다. 이 라우팅은 속도가 빠르며 인트라 익스텐트 트랜잭션을 수행하는 모든 OLTP 워크로드에 사용됩니다. 최고 수준의 성능과 가용성이 필요한 모든 OLTP 워크로드에는 직접 라우팅을 사용하는 것이 좋습니다. 익스텐트를 사용할 수 없게 되거나 분할 토폴로지가 변경되면 라우팅 캐시가 자동으로 새로 고침됩니다. 
  
  고성능 데이터 종속 라우팅의 경우 Oracle에서는 분할된 데이터베이스의 데이터에 액세스할 때 연결 풀을 사용할 것을 권장합니다. Oracle 연결 풀, 언어별 라이브러리 및 드라이버는 Oracle 분할을 지원합니다. 자세한 내용은 [Oracle 분할 설명서](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9)를 참조하세요.

- **글로벌 서비스** - 글로벌 서비스는 일반 데이터베이스 서비스와 비슷합니다. 글로벌 서비스는 데이터베이스 서비스의 모든 속성 외에도 클라이언트와 익스텐트 간의 지역 선호도, 복제 지연 허용 오차 등의 분할된 데이터베이스 속성을 갖고 있습니다. 분할된 데이터베이스에서 데이터를 읽고 쓰는 글로벌 서비스는 하나만 만들어야 합니다. Active Data Guard를 사용하고 익스텐트의 읽기 전용 복제본을 설정하는 경우 읽기 전용 워크로드에 사용할 또 다른 글로벌 서비스를 만들 수 있습니다. 클라이언트는 이러한 글로벌 서비스를 사용하여 데이터베이스에 연결할 수 있습니다.

- **익스텐트 데이터베이스** - 익스텐트 데이터베이스는 Oracle 데이터베이스입니다. 각 데이터베이스는 FSFO(Fast-Start Failover)가 설정된 Broker 구성에서 Oracle Data Guard를 사용하여 복제됩니다. 각 익스텐트에서 Data Guard 장애 조치(failover) 및 복제를 설정할 필요가 없습니다. 공유 데이터베이스를 만들 때 자동으로 구성되고 배포됩니다. 특정 익스텐트에 오류가 발생하면 Oracle 공유가 자동으로 데이터베이스 연결을 주 데이터베이스에서 대기 데이터베이스로 장애 조치(failover)합니다.

두 가지 인터페이스, 즉, Oracle Enterprise Manager 클라우드 제어 GUI 및/또는 명령줄 유틸리티 `GDSCTL`를 사용하여 Oracle 분할된 데이터베이스를 배포하고 관리할 수 있습니다. 클라우드 제어를 사용하여 여러 익스텐트의 가용성 및 성능을 모니터링할 수도 있습니다. `GDSCTL DEPLOY` 명령은 자동으로 익스텐트 및 해당 수신기를 만듭니다. 또한 이 명령은 관리자가 지정한 익스텐트 수준 고가용성에 사용되는 복제 구성을 자동으로 배포합니다.

데이터베이스를 분할하는 여러 가지 방법이 있습니다.

* 시스템 관리형 분할 - 분할을 사용하여 자동으로 익스텐트 간에 분산
* 사용자 정의 분할 - 데이터의 익스텐트 매핑을 지정할 수 있습니다. 규정 또는 데이터 지역화 요구 사항이 있는 경우에 적합합니다.
* 복합 분할 - 여러 _shardspaces_ 에 사용되는 시스템 관리형 분할과 사용자 정의 분할의 조합입니다.
* 테이블 하위 파티션 - 일반적인 분할된 테이블과 유사합니다.

Oracle 설명서에서 다른 [분할 방법](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html)에 대해 자세히 알아보세요.

애플리케이션 및 개발자에게는 분할된 데이터베이스가 단일 데이터베이스처럼 보일 수 있지만, 분할되지 않는 데이터베이스에서 분할된 데이터베이스로 마이그레이션할 때에는 중복될 테이블과 분할될 테이블을 결정하기 위한 신중한 계획이 필요합니다. 

중복 테이블은 모든 익스텐트에 저장되고, 분할된 테이블은 여러 익스텐트에 분산됩니다. 작은 차원 테이블은 복제하고 팩트 테이블은 분산/분할하는 것이 좋습니다. 익스텐트 카탈로그를 중앙 코디네이터로 사용하거나 각 익스텐트에서 데이터 펌프를 실행하여 분할된 데이터베이스에 데이터를 로드할 수 있습니다. Oracle 설명서에서 [분할된 데이터베이스로 데이터 마이그레이션](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html)에 대해 자세히 알아보세요.

#### <a name="oracle-sharding-with-data-guard"></a>Data Guard를 사용하는 Oracle 분할

Oracle Data Guard는 시스템 관리형 분할, 사용자 정의 분할 및 복합 분할 방법을 이용하는 분할에 사용할 수 있습니다.

다음 다이어그램은 각 익스텐트의 고가용성을 위해 Oracle Data Guard를 사용하는 Oracle 분할의 참조 아키텍처입니다. 이 아키텍처 다이어그램은 _복합 분할 방법_ 을 보여줍니다. 이 아키텍처 다이어그램은 데이터 위치, 부하 분산, 고가용성, 재해 복구 등에 대한 요구 사항이 다른 애플리케이션과 차이가 날 수 있으며, 다른 분할 방법을 사용할 수 있습니다. Oracle 분할을 사용하면 이러한 요구 사항을 충족할 수 있으며 이러한 옵션을 제공하여 수평적으로, 효율적으로 스케일링할 수 있습니다. Oracle GoldenGate를 사용하여 유사한 아키텍처를 배포할 수도 있습니다.

![Data Guard Broker - FSFO에서 가용성 영역을 사용하는 Oracle 데이터베이스 분할](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

시스템 관리형 분할은 구성 및 관리가 가장 쉬운 반면, 사용자 정의 분할 또는 복합 분할은 데이터 및 애플리케이션이 지리적으로 분산되어 있거나 각 익스텐트의 복제본을 제어해야 하는 시나리오에 적합합니다. 

위의 아키텍처에서 복합 분할은 데이터를 지리적으로 분산하고 애플리케이션 계층을 수평적으로 스케일 아웃하는 데 사용됩니다. 복합 분할은 시스템 관리형 분할과 사용자 정의 분할의 조합이므로 두 방법의 이점을 모두 제공합니다. 위의 시나리오에서 가장 먼저 데이터가 지역별로 구분된 여러 shardspaces에 분할됩니다. 그런 다음, 데이터가 일관된 해시를 통해 shardspace의 여러 익스텐트에 추가로 분할됩니다. 각 shardspace에는 여러 shardgroup이 포함됩니다. 각 shardgroup에는 여러 익스텐트가 있으며 이 경우 shardgroup은 복제의 "단위"입니다. 각 shardgroup에는 shardspace의 모든 데이터가 포함됩니다. shardgroup A1 및 B1은 주 shardgroup이고, shardgroup A2 및 B2는 보조 shardgroup입니다. shardgroup이 아닌 개별 익스텐트를 복제 단위로 선택할 수 있습니다.

위의 아키텍처에서 GSM/익스텐트 디렉터는 고가용성을 위해 모든 가용성 영역에 배포됩니다. 데이터 센터/지역마다 하나 이상의 GSM/익스텐트 디렉터를 배포하는 것이 좋습니다. 또한 애플리케이션 서버 인스턴스는 shardgroup를 포함하고 있는 모든 가용성 영역에 배포됩니다. 이렇게 설정하면 애플리케이션이 애플리케이션 서버와 데이터베이스/shardgroup 간의 대기 시간을 짧게 유지할 수 있습니다. 데이터베이스에 오류가 발생하는 경우 데이터베이스 역할이 전환되면 대기 데이터베이스와 동일한 영역에 있는 애플리케이션 서버에서 요청을 처리할 수 있습니다. Azure Application Gateway와 익스텐트 디렉터는 요청 및 응답 대기 시간을 추적하면서 그에 맞게 요청을 라우팅합니다.

애플리케이션 관점에서 클라이언트 시스템은 클라이언트와 가장 가까운 지역으로 요청을 리디렉션하는 Azure Application Gateway(또는 Azure의 다른 부하 분산 기술)에 요청을 보냅니다. Azure Application Gateway는 고정 세션도 지원하므로 동일한 클라이언트에서 들어오는 모든 요청은 동일한 애플리케이션 서버로 라우팅됩니다. 애플리케이션 서버는 데이터 액세스 드라이버의 연결 풀링을 사용합니다. 이 기능은 JDBC, ODP.NET, OCI 등의 드라이버에서 사용할 수 있습니다. 이러한 드라이버는 요청의 일부로 지정된 분할 키를 인식할 수 있습니다. JDBC 클라이언트용 [Oracle UCP(Universal Connection Pool)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html)는 Apache Tomcat이나 IIS 같은 비 Oracle 애플리케이션 클라이언트를 사용하도록 설정하여 Oracle 분할 작업을 수행할 수 있습니다. 

초기 요청 중에 애플리케이션 서버는 해당 지역의 익스텐트 디렉터에 연결하여 요청을 라우팅해야 하는 익스텐트의 라우팅 정보를 가져옵니다. 전달된 분할 키에 따라 디렉터는 애플리케이션 서버를 해당하는 익스텐트에 라우팅합니다. 애플리케이션 서버는 맵을 작성하여 이 정보를 캐시하고, 후속 요청에서 익스텐트 디렉터를 우회하여 요청을 익스텐트에 직접 라우팅합니다.

#### <a name="oracle-sharding-with-goldengate"></a>GoldenGate를 사용하는 Oracle 분할

다음 다이어그램은 각 익스텐트의 지역 내 고가용성을 위해 Oracle GoldenGate를 사용하는 Oracle 분할의 참조 아키텍처입니다. 위의 아키텍처와는 반대로 이 아키텍처는 단일 Azure 지역(여러 가용성 영역) 내의 고가용성만 보여줍니다. 앞의 예제와 비슷하게 Oracle GoldenGate를 사용하여 다중 지역 고가용성 분할된 데이터베이스를 배포할 수 있습니다.

![GoldenGate에서 가용성 영역을 사용하는 Oracle Database 분할](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

위의 참조 아키텍처에서는 _시스템 관리형_ 분할 방법을 사용하여 데이터를 분할합니다. Oracle GoldenGate 복제는 청크 수준에서 수행되므로 한 익스텐트에 복제된 데이터의 절반을 다른 익스텐트에 복제할 수 있습니다. 나머지 절반은 다른 익스텐트에 복제할 수 있습니다. 

데이터가 복제되는 방식은 복제 인수에 따라 달라집니다. 복제 인수가 2인 경우 shardgroup의 3개 익스텐트에 각 데이터 청크의 복사본 2개가 만들어집니다. 마찬가지로 복제 인수가 3이고 shardgroup에 익스텐트가 3개 있는 경우에는 각 익스텐트의 모든 데이터가 shardgroup의 다른 모든 익스텐트에 복제됩니다. shardgroup에 있는 각 익스텐트의 복제 인수가 서로 다를 수 있습니다. 이 설정은 shardgroup 내부와 여러 shardgroup에서 효율적으로 고가용성 및 재해 복구 디자인을 정의하는 데 도움이 됩니다.

위의 아키텍처에서 shardgroup A와 shardgroup B는 동일한 데이터를 포함하고 있지만 서로 다른 가용성 영역에 있습니다. shardgroup A와 shardgroup B의 복제 인수가 똑같이 3인 경우 분할된 테이블의 각 행/청크가 두 shardgroup에서 6회 복제됩니다. shardgroup A의 복제 인수가 3이고 shardgroup B의 복제 인수가 2인 경우에는 각 행/청크가 두 shardgroup에서 5회 복제됩니다.

이 설정은 인스턴스 수준 또는 가용성 영역 수준 오류가 발생할 때 데이터 손실을 방지합니다. 애플리케이션 계층은 각 익스텐트에서 읽고 쓸 수 있습니다. 충돌을 최소화하기 위해 Oracle 분할에서는 해시 값의 각 범위에 대한 "마스터 청크"를 지정합니다. 이 기능은 특정 청크에 대한 쓰기 요청이 해당 청크로 전달되도록 보장합니다. 또한 Oracle GoldenGate는 발생하는 충돌을 처리할 수 있도록 자동 충돌 감지 및 해결 기능을 제공합니다. Oracle 분할을 사용하는 GoldenGate를 구현하는 방법에 대한 자세한 내용과 제한 사항은 [분할된 데이터베이스에서 Oracle GoldenGate 사용](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72)에 대한 Oracle 설명서를 참조하세요.

위의 아키텍처에서 GSM/익스텐트 디렉터는 고가용성을 위해 모든 가용성 영역에 배포됩니다. 데이터 센터 또는 지역마다 하나 이상의 GSM/익스텐트 디렉터를 배포하는 것이 좋습니다. 또한 애플리케이션 서버 인스턴스는 shardgroup를 포함하고 있는 모든 가용성 영역에 배포됩니다. 이렇게 설정하면 애플리케이션이 애플리케이션 서버와 데이터베이스/shardgroup 간의 대기 시간을 짧게 유지할 수 있습니다. 데이터베이스에 오류가 발생하는 경우 데이터베이스 역할이 전환되면 대기 데이터베이스와 동일한 영역에 있는 애플리케이션 서버에서 요청을 처리할 수 있습니다. Azure Application Gateway와 익스텐트 디렉터는 요청 및 응답 대기 시간을 추적하면서 그에 맞게 요청을 라우팅합니다.

애플리케이션 관점에서 클라이언트 시스템은 클라이언트와 가장 가까운 지역으로 요청을 리디렉션하는 Azure Application Gateway(또는 Azure의 다른 부하 분산 기술)에 요청을 보냅니다. Azure Application Gateway는 고정 세션도 지원하므로 동일한 클라이언트에서 들어오는 모든 요청은 동일한 애플리케이션 서버로 라우팅됩니다. 애플리케이션 서버는 데이터 액세스 드라이버의 연결 풀링을 사용합니다. 이 기능은 JDBC, ODP.NET, OCI 등의 드라이버에서 사용할 수 있습니다. 이러한 드라이버는 요청의 일부로 지정된 분할 키를 인식할 수 있습니다. JDBC 클라이언트용 [Oracle UCP(Universal Connection Pool)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html)는 Apache Tomcat이나 IIS 같은 비 Oracle 애플리케이션 클라이언트를 사용하도록 설정하여 Oracle 분할 작업을 수행할 수 있습니다. 

초기 요청 중에 애플리케이션 서버는 해당 지역의 익스텐트 디렉터에 연결하여 요청을 라우팅해야 하는 익스텐트의 라우팅 정보를 가져옵니다. 전달된 분할 키에 따라 디렉터는 애플리케이션 서버를 해당하는 익스텐트에 라우팅합니다. 애플리케이션 서버는 맵을 작성하여 이 정보를 캐시하고, 후속 요청에서 익스텐트 디렉터를 우회하여 요청을 익스텐트에 직접 라우팅합니다.

## <a name="patching-and-maintenance"></a>패치 및 유지 관리

Oracle 워크로드를 Azure에 배포하면 모든 호스트 OS 수준 패치를 Microsoft에서 처리합니다. 고객이 계획된 유지 관리에 대비할 수 있도록 계획된 OS 수준 유지 관리 일정이 고객에게 미리 전달됩니다. 서로 다른 두 가용성 영역의 두 서버가 동시에 패치되는 일은 절대 없습니다. VM 유지 관리 및 패치에 대한 자세한 내용은 [가상 머신의 가용성 관리](../../availability.md)를 참조하세요. 

[Azure Automation 업데이트 관리](../../../automation/update-management/overview.md)를 사용하여 가상 머신 운영 체제 패치를 자동화할 수 있습니다. [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) 또는 [Azure Automation 업데이트 관리](../../../automation/update-management/overview.md)를 사용하여 Oracle 데이터베이스의 패치 및 유지 관리를 자동화하고 예약하면 가동 중지 시간을 최소화할 수 있습니다. Oracle 데이터베이스의 컨텍스트에서 사용하는 방법을 알아보려면 [지속적인 업데이트 및 청록색 배포](/devops/deliver/what-is-continuous-delivery)를 참조하세요.

## <a name="architecture-and-design-considerations"></a>아키텍처 및 디자인 고려 사항

- 라이선스 비용을 절감하고 성능을 최대화하기 위해 [제약이 있는 코어 vCPU](../../../virtual-machines/constrained-vcpu.md)가 포함된 하이퍼스레드 [메모리 최적화 가상 머신](../../sizes-memory.md)을 Oracle Database VM에 사용하는 것이 좋습니다. 성능과 고가용성을 위해 여러 프리미엄 또는 울트라 디스크(관리 디스크)를 사용합니다.
- 관리 디스크를 사용하는 경우 재부팅 시 디스크/디바이스 이름이 변경될 수 있습니다. 다시 부팅해도 탑재가 유지되도록 이름 대신 디바이스 UUID를 사용하는 것이 좋습니다. 자세한 내용은 [Linux VM에서 소프트웨어 RAID 구성](/previous-versions/azure/virtual-machines/linux/configure-raid#add-the-new-file-system-to-etcfstab)을 참조하세요.
- 가용성 영역을 사용하여 지역 내 고가용성을 구현합니다.
- Oracle 데이터베이스에 울트라 디스크(사용 가능한 경우) 또는 프리미엄 디스크를 사용하는 것이 좋습니다.
- Oracle Data Guard를 사용하여 다른 Azure 지역에 대기 Oracle 데이터베이스를 설정하는 것이 좋습니다.
- 애플리케이션과 데이터베이스 계층 간의 대기 시간을 줄이기 위해 [근접 배치 그룹](../../co-location.md#proximity-placement-groups)을 사용하는 것이 좋습니다.
- 관리, 모니터링 및 로깅을 위해 [Oracle Enterprise Manager](https://docs.oracle.com/en/enterprise-manager/)를 설정합니다.
- 데이터베이스의 스토리지 관리를 간소화하기 위해 Oracle ASM(자동 스토리지 관리)을 사용하는 것이 좋습니다.
- [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines)을 사용하여 가동 중지 시간 없이 데이터베이스 패치 및 업데이트를 관리합니다.
- 애플리케이션 코드를 수정하여 애플리케이션의 복원력 향상에 도움이 될 수 있는 [재시도 패턴](/azure/architecture/patterns/retry), [회로 차단기 패턴](/azure/architecture/patterns/circuit-breaker), [클라우드 디자인 패턴 가이드](/azure/architecture/patterns/)에 정의된 기타 패턴과 같은 클라우드 네이티브 패턴을 추가합니다.

## <a name="next-steps"></a>다음 단계

시나리오에 적용되는 다음 Oracle 참조 문서를 검토합니다.

- [Oracle Data Guard 소개](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Oracle Data Guard Broker 개념](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [활성-활성 고가용성을 위한 Oracle GoldenGate 구성](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Oracle 분할의 개요](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [모든 거리에서 Oracle Active Data Guard Far Sync 데이터 무손실](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)
