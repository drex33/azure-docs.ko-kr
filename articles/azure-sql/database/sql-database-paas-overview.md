---
title: Azure SQL Database 서비스란?
description: 'SQL Database에 대한 소개: 클라우드 환경에서 Microsoft의 관계형 데이터베이스 관리 시스템(RDBMS)의 기능과 기술 세부 정보를 살펴봅니다.'
keywords: sql 소개, sql 소개, sql 데이터베이스란
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: overview
author: LitKnd
ms.author: kendralittle
ms.reviewer: ''
ms.date: 09/21/2020
ms.openlocfilehash: b5b7c9c08e0d9a70bd98237cd66bacfc07a190bb
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133364411"
---
# <a name="what-is-azure-sql-database"></a>Azure SQL Database란?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database는 사용자 개입 없이 업그레이드, 패치, 백업, 모니터링 같은 대부분의 데이터베이스 관리 기능을 처리하는 완전 관리형 PaaS(Platform as a Service) 데이터베이스 엔진입니다. Azure SQL Database는 항상 최신 안정화 버전의 SQL Server 데이터베이스 엔진 및 패치된 OS에서 99.99% 가용성으로 실행됩니다. Azure SQL Database에 기본 제공되는 PaaS 기능 덕분에 비즈니스에 중요한 도메인 특정 데이터베이스 관리 및 최적화 활동에 집중할 수 있습니다.

Azure SQL Database를 사용하면 Azure의 애플리케이션 및 솔루션을 위한 고가용성 고성능 데이터 스토리지 레이어를 만들 수 있습니다. Azure SQL Database는 다양한 최신 클라우드 애플리케이션에 적합한 선택이 될 수 있는데, 그 이유는 관계형 데이터와 [비관계형 구조](../multi-model-features.md)(예: 그래프, JSON, 공간 및 XML)를 모두 처리할 수 있기 때문입니다.

Azure SQL Database는 안정적인 최신 버전의 [Microsoft SQL Server 데이터베이스 엔진](/sql/sql-server/sql-server-technical-documentation?toc=%2fazure%2fsql-database%2ftoc.json)을 기반으로 합니다. [고성능 메모리 내 기술](../in-memory-oltp-overview.md) 및 [지능형 쿼리 처리](/sql/relational-databases/performance/intelligent-query-processing?toc=%2fazure%2fsql-database%2ftoc.json)와 같은 고급 쿼리 처리 기능을 사용할 수 있습니다. 실제로 SQL Server의 최신 기능은 먼저 SQL Database에 릴리스된 후 SQL Server 자체에 릴리스됩니다. 수백만에 달하는 데이터베이스에 대해 테스트한 최신 SQL Server 기능을 패치나 업그레이드를 위한 오버헤드 없이 활용할 수 있습니다. 

SQL Database는 서로 다른 두 구매 모델 내에서 성능을 쉽게 정의하고 확장할 수 있습니다([vCore 기반 구매 모델](service-tiers-vcore.md) 및 [DTU 기반 구매 모델](service-tiers-dtu.md)). SQL Database는 기본 제공되는 고가용성, 백업 및 다른 일반적인 유지 관리 작업이 포함된 완전 관리형 서비스입니다. Microsoft는 SQL 및 운영 체제 코드의 모든 패치 및 업데이트를 처리합니다. 기본 인프라를 관리할 필요가 없습니다.

> [!div class="nextstepaction"]
> [Azure SQL 개선을 위한 설문 조사](https://aka.ms/AzureSQLSurveyNov2021)

Azure SQL Database를 처음 사용하는 경우에는 심층 [Azure SQL 비디오 시리즈](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)에서 *Azure SQL Database 개요* 비디오를 참조하세요.
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Database-Overview-7-of-61/player]



## <a name="deployment-models"></a>배포 모델

Azure SQL Database는 데이터베이스에 다음과 같은 옵션을 제공합니다.

- [단일 데이터베이스](single-database-overview.md)는 완전 관리형의 격리된 데이터베이스를 나타냅니다. 안정적인 단일 데이터 원본이 필요한 최신 클라우드 애플리케이션 및 마이크로 서비스가 있는 경우, 이 옵션을 사용할 수 있습니다. 단일 데이터베이스는 [SQL Server 데이터베이스 엔진](/sql/sql-server/sql-server-technical-documentation?toc=%2fazure%2fsql-database%2ftoc.json)에 [포함된 데이터베이스](/sql/relational-databases/databases/contained-databases?toc=%2fazure%2fsql-database%2ftoc.json)와 유사합니다.
- [탄력적 풀](elastic-pool-overview.md)은 CPU, 메모리 등의 공유 리소스 집합이 포함된 [단일 데이터베이스](single-database-overview.md)의 컬렉션입니다. 단일 데이터베이스를 탄력적 풀로 이동하거나 탄력적 풀에서 제거할 수 있습니다.

> [!IMPORTANT]
> SQL Database와 SQL Server 간의 기능 차이점과 다양한 Azure SQL Database 옵션 간의 차이점을 파악하려면 [SQL Database 기능](features-comparison.md)을 참조하세요.

SQL Database는 여러 리소스 종류, 서비스 계층 및 컴퓨팅 크기를 사용하여 예측 가능한 성능을 제공합니다. 이 데이터베이스는 가동 중지 시간 없는 동적 확장성, 기본 제공된 지능형 최적화, 글로벌 확장성 및 가용성, 고급 보안 옵션 등을 제공합니다. 이러한 기능을 사용하면 가상 머신과 인프라를 관리하는 데 집중하는 대신, 빠른 앱 개발과 시장 출시 시간 단축에 주력할 수 있습니다. SQL Database는 현재 전 세계 38개 데이터 센터에 있으므로 가까운 데이터 센터에서 데이터베이스를 실행할 수 있습니다.

## <a name="scalable-performance-and-pools"></a>확장 가능한 성능 및 풀

할당된 리소스의 양을 정의할 수 있습니다. 
- 단일 데이터베이스를 사용하는 경우, 각 데이터베이스는 다른 데이터베이스와 격리되며 이식 가능합니다. 각 데이터베이스에는 보장된 양의 컴퓨팅, 메모리 및 스토리지 리소스가 있습니다. 데이터베이스에 할당된 리소스의 양은 해당 데이터베이스에만 사용되며 Azure의 다른 데이터베이스와 공유되지 않습니다. [단일 데이터베이스 리소스의 크기를 조정](single-database-scale.md)(동적으로 확장 및 축소)할 수 있습니다. 단일 데이터베이스 옵션은 요구 사항에 따라 다양한 컴퓨팅, 메모리 및 스토리지 리소스를 제공합니다. 예를 들어 1~80개의 vCore 또는 32GB~4TB의 리소스를 얻을 수 있습니다. 단일 데이터베이스에 대한 [하이퍼스케일 서비스 계층](service-tier-hyperscale.md)을 사용하면 빠른 백업 및 복원 기능을 통해 100TB까지 크기를 조정할 수 있습니다.
- 탄력적 풀을 사용하면 풀에 있는 모든 데이터베이스에서 공유하는 리소스를 할당할 수 있습니다. 새 데이터베이스를 만들거나 기존 단일 데이터베이스를 리소스 풀로 이동하면 리소스 사용을 최대한 늘리면서 비용을 절감할 수 있습니다. 또한 이 옵션은 동적으로 [탄력적 풀 리소스의 규모를 조정](elastic-pool-scale.md)(확장 및 축소)할 수 있는 기능도 제공합니다.

범용 서비스 계층에서 매월 저렴한 비용으로 소규모의 단일 데이터베이스에 첫 번째 앱을 빌드할 수 있습니다. 그런 다음, 솔루션의 요구 사항에 맞게 언제든지 중요 비즈니스용 서비스 계층으로 서비스 계층을 수동으로 또는 프로그래밍 방식으로 변경할 수 있습니다. 앱이나 고객에게 가동 중지 시간 없이 성능을 조정할 수 있습니다. 동적 확장성을 사용하면 데이터베이스가 빠르게 변화하는 리소스 요구 사항에 투명하게 대응할 수 있습니다. 필요할 경우 필요한 리소스에 대해서만 요금을 지불합니다.

동적 확장성은 자동 크기 조정과 다릅니다. 자동 크기 조정은 서비스가 조건에 따라 자동으로 크기를 조정하는 경우인 반면 동적 확장성은 가동 중지 시간 없이 수동 크기 조정을 허용합니다. 단일 데이터베이스 옵션은 수동 동적 확장성을 지원하지만 자동 크기 조정은 지원하지 않습니다. 더 많은 자동 환경은 데이터베이스에서 개별 데이터베이스 요구 사항에 따라 풀에 리소스를 공유하도록 허용하는 탄력적 풀을 사용하는 것이 좋습니다. 또 다른 옵션은 단일 데이터베이스에 대한 확장성을 자동화할 수 있는 스크립트를 사용하는 것입니다. 예제는 [PowerShell을 사용하여 단일 데이터베이스 모니터링 및 크기 조정](scripts/monitor-and-scale-database-powershell.md)을 참조하세요.

### <a name="purchasing-models"></a>구매 모델

SQL Database는 다음의 구매 모델을 제공합니다.
- [vCore 기반 구매 모델](service-tiers-vcore.md)을 통해 vCore 개수, 크기나 메모리 및 스토리지의 크기와 속도를 선택할 수 있습니다. vCore 기반 구매 모델을 사용하면 [SQL Server용 Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 사용하여 비용을 절약할 수도 있습니다. Azure 하이브리드 혜택에 대한 자세한 내용은 이 문서의 뒷부분에 있는 "질문과 대답" 섹션을 참조하세요.
- [DTU 기반 구매 모델](service-tiers-dtu.md)은 경량 내지 중량의 데이터베이스 워크로드를 지원하기 위해 다음과 같은 세 가지 서비스 계층에서 컴퓨팅, 메모리 및 I/O 리소스를 함께 제공합니다. 각 계층 내의 컴퓨팅 크기는 이러한 리소스의 다양한 조합을 제공하여 추가 스토리지 리소스를 추가할 수 있습니다.
- [서버리스 모델](serverless-tier-overview.md)은 워크로드 요구 사항에 따라 자동으로 컴퓨팅 크기를 조정하고 초당 사용된 컴퓨팅 양에 대한 요금을 청구합니다. 또한 서버리스 컴퓨팅 계층은 스토리지 비용만 청구될 때 비활성 기간 동안 데이터베이스를 자동으로 일시 중지하고 활동이 반환되면 데이터베이스를 자동으로 다시 시작합니다.

### <a name="service-tiers"></a>서비스 계층

Azure SQL Database는 다음과 같은 다양한 유형의 애플리케이션을 위해 설계된 3가지 서비스 계층을 제공합니다.
- 일반적인 워크로드를 위해 설계된 [범용/표준](service-tier-general-purpose.md) 서비스 계층입니다. 예산 중심의 균형 잡힌 컴퓨팅 및 스토리지 옵션을 제공합니다.
- 트랜잭션 속도가 빠르면서 지연 시간이 최소화된 I/O를 가진 OLTP 애플리케이션을 위해 설계된 [중요 비즈니스용/프리미엄](service-tier-business-critical.md) 서비스 계층입니다. 여러 개의 격리된 복제본을 사용하여 장애에 대한 최고의 복원력을 제공합니다.
- 매우 큰 OLTP 데이터베이스와 스토리지 크기를 자동 조정하고 컴퓨팅 크기를 유동적으로 조정하는 기능을 위해 설계된 [하이퍼스케일](service-tier-hyperscale.md) 서비스 계층입니다.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>리소스 사용률 극대화를 위한 탄력적 풀

특히 사용 패턴이 비교적 예측 가능한 경우 많은 비즈니스 및 애플리케이션에서 단일 데이터베이스를 만들고 필요에 따라 충분히 성능을 확장하거나 축소할 수 있습니다. 사용 패턴을 예측할 수 없는 경우, 비용과 비즈니스 모델을 관리하기가 어려워질 수 있습니다. [탄력적 풀](elastic-pool-overview.md)은 이 문제를 해결하도록 설계되었습니다. 개별 데이터베이스 대신, 풀에 성능 리소스를 할당합니다. 단일 데이터베이스 성능이 아닌, 풀의 전체 성능 리소스에 대한 비용을 지불합니다.

   ![기본, 표준 및 프리미엄 버전에서 탄력적 풀을 보여 주는 그래픽](./media/sql-database-paas-overview/sqldb_elastic_pools.png)

탄력적 풀을 사용하면 리소스에 대한 요구가 변동함에 따라 데이터베이스 성능을 높이거나 낮추는 데 집중할 필요가 없습니다. 풀링된 데이터베이스는 필요에 따라 탄력적 풀의 성능 리소스를 사용합니다. 풀링된 데이터베이스는 풀의 한도를 사용하지만 초과하지 않으므로 개별 데이터베이스 사용량을 예측할 수 없는 경우에도 비용을 계속 예측할 수 있습니다.

[풀에 데이터베이스를 추가 및 제거](elastic-pool-overview.md)하여 소수의 데이터베이스에서 수천 개의 데이터베이스까지 자신이 관리하는 예산 범위 내에서 앱의 규모를 조정할 수 있습니다. 풀의 데이터베이스에서 사용할 수 있는 최소 및 최대 리소스를 제어하여 풀의 어떤 데이터베이스도 풀 리소스 전체를 사용하지 못하도록 하고 풀링된 모든 데이터베이스에 최소한의 리소스를 보장할 수 있습니다. 탄력적 풀을 사용한 SaaS(Software as a Service) 애플리케이션의 디자인 패턴에 대해 자세히 알아보려면 [SQL Database를 사용한 다중 테넌트 SaaS 애플리케이션 디자인 패턴](saas-tenancy-app-design-patterns.md)을 참조하세요.

스크립트는 탄력적 풀 모니터링 및 크기 조정에 도움을 줄 수 있습니다. 예제는 [PowerShell을 사용하여 Azure SQL Database에서 탄력적 풀 모니터링 및 크기 조정](scripts/monitor-and-scale-pool-powershell.md)을 참조하세요.


### <a name="blend-single-databases-with-pooled-databases"></a>단일 데이터베이스와 풀링된 데이터베이스의 혼합

즉, 단일 데이터베이스와 탄력적 풀을 혼합하고, 단일 데이터베이스와 탄력적 풀의 서비스 계층을 상황에 맞게 변경할 수 있습니다. 다른 Azure 서비스를 SQL Database와 조합하여 사용하면 고유한 앱 설계 요구를 충족시키고 비용 및 리소스를 효율적으로 운용하며 새로운 비즈니스 기회를 만들 수 있습니다.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>광범위한 모니터링 및 경고 기능

Azure SQL Database는 워크로드 특성에 대해 더욱 심층적인 인사이트를 얻는 데 도움이 되는 고급 모니터링 및 문제 해결 기능을 제공합니다. 이러한 기능 및 도구들은 다음과 같습니다.
 - 최신 버전의 SQL Server 데이터베이스 엔진에서 제공하는 기본 제공 모니터링 기능. 이 기능을 사용하면 실시간 성능 정보를 확인할 수 있습니다. 
 - Azure에서 제공하는 PaaS 모니터링 기능. 이 기능을 사용하면 다수의 데이터베이스 인스턴스를 모니터링하고 문제를 해결할 수 있습니다.

기본적으로 제공되는 SQL Server 모니터링 기능인 [쿼리 저장소](/sql/relational-databases/performance/best-practice-with-the-query-store)는 쿼리 성능을 실시간으로 기록하며, 잠재적인 성능 문제와 상위 리소스 소비자를 식별할 수 있습니다. 자동 조정 및 권장 사항은 재발된 성능 및 누락되거나 중복된 인덱스를 사용하여 쿼리에 관한 조언을 제공합니다. SQL Database 자동 조정 기능을 사용하면 문제를 해결할 수 있는 스크립트를 수동으로 적용할 수 있으며 또는 SQL Database가 픽스를 적용할 수 있습니다. 또한 SQL 데이터베이스는 픽스가 몇 가지 이점을 제공하는 것을 테스트 및 확인하고 그 결과에 따라 변경 사항을 유지하거나 되돌릴 수도 있습니다. 쿼리 저장소 및 자동 조정 기능 외에도 표준 [DMV 및 XEvent](monitoring-with-dmvs.md)를 사용하여 워크로드 성능을 모니터링할 수 있습니다.

Azure는 성능 등급과 결합된 [기본 제공 성능 모니터링](performance-guidance.md) 및 [경고](alerts-insights-configure-portal.md) 도구를 제공하며, 이를 통해 수천 개의 데이터베이스 상태를 모니터링할 수 있습니다. 이러한 도구를 사용하면 현재 또는 예상되는 성능 요구 사항에 따라 확장 또는 축소함으로써 발생하는 영향을 신속하게 평가할 수 있습니다. 또한 SQL Database는 쉬운 모니터링을 위해 [메트릭 및 리소스 로그를 내보낼](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) 수 있습니다. 리소스 사용량, 작업자와 세션 및 연결을 이러한 Azure 리소스 중 하나에 저장하도록 SQL Database를 구성할 수 있습니다.

- **Azure Storage**: 저렴한 가격으로 방대한 양의 원격 분석을 보관할 수 있습니다.
- **Azure Event Hubs**: 사용자 지정 모니터링 솔루션 또는 핫 파이프라인과 SQL Database 원격 분석을 통합합니다.
- **Azure Monitor 로그**: 보고, 경고 및 완화 기능을 사용하는 기본 제공 모니터링 솔루션의 경우

![Azure 모니터링 아키텍처 다이어그램](./media/sql-database-paas-overview/architecture.png)

## <a name="availability-capabilities"></a>가용성 기능

Azure SQL Database를 사용하면 중단 중에 비즈니스를 계속 진행할 수 있습니다. 기존 SQL Server 환경에서는 일반적으로 두 대 이상의 컴퓨터가 로컬로 설정되어 있습니다. 이러한 컴퓨터에는 단일 컴퓨터 또는 구성 요소의 장애로부터 보호하기 위해 정확하고 동기적으로 유지 관리되는 데이터 복사본이 있습니다. 이 환경은 고가용성을 제공 하지만 데이터 센터를 제거하는 자연 재해를 방지하지는 않습니다.

재해 복구는 데이터 사본이 있는 다른 컴퓨터 또는 컴퓨터 집합이 멀리 떨어져 위치하도록 치명적인 이벤트가 국지적으로만 발생한다고 가정합니다. SQL Server에서 비동기 모드로 실행되는 Always On 가용성 그룹을 사용하면 이 기능을 얻을 수 있습니다. 사용자는 트랜잭션을 커밋하기 전에 복제가 발생할 때까지 기다리지 않으려는 경우가 종종 있기 때문에 계획되지 않은 장애 조치를 수행하면 데이터 손실이 발생할 가능성이 있습니다.

프리미엄 및 중요 비즈니스용 서비스 계층의 데이터베이스는 가용성 그룹의 동기화와 [유사한 것을 이미 수행](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability)하고 있습니다. 낮은 서비스 계층의 데이터베이스는 [상이하지만 동등한 메커니즘](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability)을 사용하여 스토리지를 통한 중복성을 제공합니다. 기본 제공 논리는 단일 시스템 오류를 방지하는 데 도움이 됩니다. 활성 지역 복제 기능을 사용하면 전체 지역이 제거되는 재해를 방지할 수 있습니다.

Azure 가용성 영역은 단일 지역 내에서 단일 데이터 센터 빌드가 중단되는 것을 방지하려고 합니다. 이는 빌드에 대한 전원 또는 네트워크의 손실을 방지하는 데 도움이 됩니다. SQL Database에서는 서로 다른 가용 영역(실제로는 다른 건물)에 서로 다른 복제본을 배치합니다.

실제로 Azure의 서비스 수준 약정[(SLA)](https://azure.microsoft.com/support/legal/sla/)은 Microsoft에서 관리되는 전 세계 데이터 센터 네트워크의 지원을 받아 앱을 연중 무휴(24/7)로 실행할 수 있습니다. Azure 플랫폼은 모든 데이터베이스를 완벽하게 관리하며 데이터 무손실 및 높은 데이터 가용성을 보장합니다. Azure는 패치, 백업, 복제, 오류 감지, 기본 하드웨어, 소프트웨어 또는 네트워크 오류, 배포 버그 픽스, 장애 조치(failover), 데이터베이스 업그레이드 및 기타 유지 관리 작업을 자동으로 처리합니다. 표준 가용성은 컴퓨팅 계층과 스토리지 계층을 분리하여 달성합니다. 프리미엄 가용성은 컴퓨팅 및 스토리지를 단일 고성능 노드에 통합한 후 Always On 가용성 그룹과 유사한 기술을 구현함으로써 달성됩니다. Azure SQL Database의 고가용성 기능에 대한 자세한 내용은 [SQL Database 가용성](high-availability-sla.md)을 참조하세요. 

또한 SQL Database는 기본 제공 [비즈니스 연속성 및 글로벌 확장성](business-continuity-high-availability-disaster-recover-hadr-overview.md) 기능을 제공합니다. 여기에는 다음이 포함됩니다.

- [자동 백업](automated-backups-overview.md):

  SQL Database는 데이터베이스의 전체, 차등, 트랜잭션 로그 백업을 자동으로 수행하므로 특정 시점으로 복원할 수 있습니다. 단일 데이터베이스 및 풀링된 데이터베이스의 경우 장기 백업 보존을 위해 전체 데이터베이스 백업을 Azure Storage에 저장하도록 SQL Database를 구성할 수 있습니다. 관리되는 인스턴스의 경우 장기 백업 보존을 위해 복사 전용 백업도 수행할 수 있습니다.

- [지정 시간 복원](recovery-using-backups.md):

  모든 SQL Database 배포 옵션은 데이터베이스에 대해 자동 백업 보존 기간 내의 특정 시점으로 복구를 지원합니다.
- [활성 지역 복제](active-geo-replication-overview.md):

  단일 데이터베이스 및 풀링된 데이터베이스 옵션을 사용하면 동일하거나 전역적으로 분산되어 있는 Azure 데이터 센터에 최대 4개의 읽기 가능한 보조 데이터베이스를 구성할 수 있습니다. 예를 들어 읽기 전용 동시 트랜잭션 양이 많은 카탈로그 데이터베이스와 SaaS 애플리케이션이 있는 경우 활성 지역 복제를 사용하여 세계적인 읽기 규모를 사용하도록 설정합니다. 이렇게 하면 읽기 워크로드로 인한 주 서버의 병목 상태가 제거됩니다. 관리되는 인스턴스의 경우 자동 장애 조치(failover) 그룹을 사용합니다.
- [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md):

  모든 SQL Database 배포 옵션을 사용하면 장애 조치 그룹을 사용하여 글로벌 규모로 고가용성 및 부하 분산을 실행할 수 있습니다. 여기에는 데이터베이스, 탄력적 풀 및 관리되는 인스턴스의 투명한 지역 복제 및 장애 조치가 포함됩니다. 장애 조치 그룹을 사용하면 관리 오버헤드를 최소화하면서 전역으로 분산된 SaaS 애플리케이션을 만들 수 있습니다. 이렇게 하면 모든 복잡한 모니터링, 라우팅 및 장애 조치 오케스트레이션이 SQL Database로 유지됩니다.
- [영역 중복 데이터베이스](high-availability-sla.md):

  SQL Database를 사용하면 여러 가용성 영역에서 프리미엄 또는 중요 비즈니스용 데이터베이스 또는 탄력적 풀을 프로비전할 수 있습니다. 이러한 데이터베이스와 탄력적 풀에는 고가용성을 위한 여러 중복 복제본이 있기 때문에 이러한 복제본을 여러 가용성 영역에 배치하면 더 높은 복원력을 제공합니다. 여기에는 데이터 손실 없이 데이터 센터 크기 조정 실패에서 자동으로 복구하는 기능이 포함됩니다.

## <a name="built-in-intelligence"></a>기본 제공 인텔리전스

SQL Database에서 기본 제공 인텔리전스를 통해 데이터베이스를 실행하고 관리하는 비용을 크게 줄이고 애플리케이션의 성능과 보안을 모두 극대화할 수 있습니다. SQL Database는 수백만 개의 고객 워크로드를 24시간 실행하여 많은 양의 원격 분석 데이터를 수집하고 처리하는 동시에 고객 개인 정보를 완벽하게 관리합니다. 다양한 알고리즘은 서비스가 애플리케이션에 적용될 수 있도록 지속적으로 원격 분석 데이터를 평가합니다.

### <a name="automatic-performance-monitoring-and-tuning"></a>자동 성능 모니터링 및 튜닝

SQL Database는 모니터링해야 하는 쿼리에 대한 자세한 정보를 제공합니다. SQL Database는 데이터베이스 패턴을 인식하고 워크로드에 맞게 데이터베이스 스키마를 적용할 수 있습니다. SQL Database는 튜닝 작업을 검토하고 적용할 수 있는 [성능 튜닝 권장 사항](database-advisor-implement-performance-recommendations.md)을 제공합니다.

그러나 지속적으로 데이터베이스를 모니터링하는 것은 특히 많은 데이터베이스를 처리할 때 힘들고 지루한 작업입니다. [Intelligent Insights](intelligent-insights-overview.md)는 SQL Database 성능을 자동으로 모니터링함으로써 이 작업을 수행합니다. 이는 성능 저하 문제를 알려주고 각 문제의 근본 원인을 식별하며 가능하다면 성능 개선 권장 사항을 제공합니다.

SQL Database 및 Azure에서 제공하는 모든 사용 가능한 도구 및 보고서를 사용하더라도 상당히 많은 데이터베이스를 효율적으로 관리하기 어려울 수 있습니다. 수동으로 데이터베이스를 모니터링하고 튜닝하는 대신 [자동 튜닝](automatic-tuning-overview.md)을 사용하여 SQL Database에 대한 모니터링 및 튜닝 작업을 위임하도록 고려할 수 있습니다. SQL Database는 권장 사항, 테스트를 자동으로 적용하고 해당 튜닝 작업을 확인하여 성능이 계속 향상되도록 합니다. 이러한 방식으로 SQL Database는 자동으로 안전하게 제어된 방법으로 워크로드에 적용됩니다. 자동 튜닝은 데이터베이스 성능을 신중하게 모니터링하고 모든 튜닝 작업 전후와 비교함을 의미합니다. 성능이 개선되지 않는 경우 튜닝 작업은 되돌려집니다.

SQL Database를 기반으로 [SaaS 다중 테넌트 앱](saas-tenancy-app-design-patterns.md)을 실행하는 파트너들 중 상당수는 자동 성능 튜닝을 사용하여 애플리케이션이 항상 안정적이고 예측 가능한 성능을 제공하도록 합니다. 이 기능은 갑자기 성능 인시던트가 발생하는 위험을 크게 줄여 줍니다. 또한 일부 해당 고객이 SQL Server를 사용하기 때문에 SQL Database에서 제공하는 동일한 인덱싱 권장 사항을 사용하여 SQL Server 고객을 도울 수 있습니다.

[SQL Database에서 사용할 수 있는](automatic-tuning-overview.md) 자동 튜닝은 다음의 두 가지 측면이 있습니다.

- **자동 인덱스 관리**: 데이터베이스에 추가되어야 하는 인덱스 및 제거되어야 하는 인덱스를 식별합니다.
- **자동 계획 수정**: 문제가 있는 계획을 식별하고 SQL 계획 성능 문제를 해결합니다.

### <a name="adaptive-query-processing"></a>적응 쿼리 처리

다중 문 테이블 반환 함수, 일괄 처리 모드 메모리 부여 피드백 및 일괄 처리 모드 적응형 조인에 대해 인터리브된 실행을 포함하여 [적응 쿼리 처리](/sql/relational-databases/performance/intelligent-query-processing)를 사용할 수 있습니다. 이러한 적응 쿼리 처리 기능은 각각 유사한 "알아보기 및 적용" 방법을 적용하고 지금까지 다루기 힘든 쿼리 최적화 문제와 관련된 성능 문제를 추가로 해결할 수 있도록 합니다.

## <a name="advanced-security-and-compliance"></a>고급 보안 및 규정 준수

SQL Database는 다양한 [기본 제공 보안 및 규정 준수 기능](../../active-directory/identity-protection/concept-identity-protection-security-overview.md)을 제공하여 애플리케이션이 다양한 보안 및 규정 준수 요구 사항을 충족할 수 있도록 합니다.

> [!IMPORTANT]
> Microsoft는 다양한 규정 준수 표준에 대해 Azure SQL Database(모든 배포 옵션)를 인증했습니다. 자세한 내용은 [Microsoft Azure 보안 센터](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)를 참조하세요. 여기서 최신 SQL Database 규정 준수 인증서 목록을 찾을 수 있습니다.

### <a name="advance-threat-protection"></a>고급 위협 방지

Microsoft Defender for SQL은 고급 SQL 보안 기능의 통합 패키지입니다. 여기에는 데이터베이스 취약성 관리 및 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동 검색 기능이 포함됩니다. 이 패키지를 통해 이러한 기능을 한 곳에서 사용하도록 설정하고 관리할 수 있습니다.

- [취약성 평가](sql-vulnerability-assessment.md):

  이 서비스는 잠재적인 데이터베이스 취약성을 검색, 추적 및 수정하는 데 도움이 됩니다. 보안 상태에 대한 가시성을 제공하며, 보안 문제를 해결하고 데이터베이스 보안을 강화하기 위해 실행할 수 있는 단계를 포함합니다.
- [위협 탐지](threat-detection-configure.md):

  이 기능은 비정상적이며 잠재적으로 유해한 데이터베이스 액세스 또는 악용 시도를 나타내는 비정상적인 활동을 검색합니다. 지속적으로 의심스러운 활동에 대한 데이터베이스를 모니터링하고, 잠재적인 취약점, SQL 삽입 공격 및 비정상 데이터베이스 액세스 패턴에 대한 보안 경고를 즉시 제공합니다. 위협 감지 경고는 의심스러운 활동에 대한 세부 정보를 제공하고 위협을 조사하고 완화하는 방법에 대한 조치를 권장합니다.

### <a name="auditing-for-compliance-and-security"></a>규정 준수 및 보안에 대한 감사

[감사](../../azure-sql/database/auditing-overview.md)는 데이터베이스 이벤트를 추적하고 Azure Storage 계정의 감사 로그에 이벤트를 씁니다. 감사는 규정 준수를 유지 관리하고, 데이터베이스 작업을 이해하고, 비즈니스 문제나 의심스러운 보안 위반을 나타낼 수 있는 불일치 및 이상 활동을 파악하는 데 도움이 될 수 있습니다.

### <a name="data-encryption"></a>데이터 암호화.

SQL Database는 암호화를 제공하여 데이터를 보호합니다. 이동 중인 데이터의 경우에는 [전송 계층 보안](https://support.microsoft.com/kb/3135244)사용합니다. 미사용 데이터의 경우에는 [투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)를 사용합니다. 사용 중인 데이터의 경우에는 [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)를 사용합니다.

### <a name="data-discovery-and-classification"></a>데이터 검색 및 분류

[데이터 검색 및 분류](data-discovery-and-classification-overview.md)는 데이터베이스의 중요한 데이터의 검색, 분류, 레이블 지정 및 보호를 위해 Azure SQL Database에 기본 제공되는 기능입니다. 데이터베이스 분류 상태에 대한 가시성을 제공하고, 데이터베이스 내 및 해당 경계 외부의 중요한 데이터에 대한 액세스를 추적합니다.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 통합 및 다단계 인증

SQL Database를 사용하면 [Azure Active Directory 통합](authentication-aad-overview.md)에서 데이터베이스 사용자 및 다른 Microsoft 서비스의 ID를 중앙 집중식으로 관리할 수 있습니다. 이 기능은 사용 권한 관리를 간소화하고 보안을 향상시킵니다. Azure Active Directory는 MFA([Multi-Factor Authentication](authentication-mfa-ssms-overview.md))를 제공하여 단일 로그인 프로세스를 지원하는 동시에 데이터 및 애플리케이션 보안을 향상시킵니다.

## <a name="easy-to-use-tools"></a>사용하기 쉬운 도구

SQL Database로 애플리케이션을 빌드하고 관리하는 작업의 편의성과 생산성을 높이세요. SQL Database를 사용하면 멋진 앱을 만드는 데만 집중할 수 있습니다. 이미 설치된 도구와 기술을 사용하여 SQL Database에서 관리하고 개발할 수 있습니다.

|도구|Description|
|:---|:---|
|[Azure Portal](https://portal.azure.com/)|모든 Azure 서비스를 관리하는 웹 기반 애플리케이션.|
|[Azure Data Studio](/sql/azure-data-studio/)|Windows, macOS 및 Linux에서 실행되는 플랫폼 간 데이터베이스 도구.|
|[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)|SQL Server에서 SQL Database에 이르는 모든 SQL 인프라를 관리하는 체험, 다운로드 가능한 클라이언트 애플리케이션.|
|[Visual Studio의 SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt)|SQL Server 관계형 데이터베이스, Azure SQL Database의 데이터베이스, Integration Services 패키지, Analysis Services 데이터 모델 및 Reporting Services 보고서를 개발하는 체험, 다운로드 가능한 클라이언트 애플리케이션.|
|[Visual Studio Code](https://code.visualstudio.com/docs)|Windows, macOS 및 Linux용 오픈 소스 코드 편집기이며 무료로 다운로드할 수 있습니다. Microsoft SQL Server, Azure SQL Database 및 Azure Synapse Analytics를 쿼리하는 [mssql 확장](https://aka.ms/mssql-marketplace)을 비롯한 여러 확장을 지원합니다.|

SQL Database는 macOS, Linux 및 Windows에서 Python, Java, Node.js, PHP, Ruby 및 .NET을 사용하여 애플리케이션을 빌드하도록 지원합니다. SQL Database는 동일한 [연결 라이브러리](connect-query-content-reference-guide.md#libraries)를 SQL Server로 지원합니다.

[!INCLUDE [sql-database-create-manage-portal](../includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>SQL Database 질문과 대답

### <a name="can-i-control-when-patching-downtime-occurs"></a>패치 가동 중지 시간이 발생하는 경우를 제어할 수 있나요?

아니요. 일반적으로 패치 적용에 따른 영향은 앱에서 [다시 시도 논리를 사용](develop-overview.md#resiliency)하면 두드러지지 않습니다. 자세한 내용은 [Azure SQL Database의 Azure 유지 관리 이벤트 계획](planned-maintenance.md)을 참조하세요.



## <a name="engage-with-the-sql-server-engineering-team"></a>SQL Server 엔지니어링 팀에 문의

- [DBA 스택 교환](https://dba.stackexchange.com/questions/tagged/sql-server): 데이터베이스 관리에 관해 질문합니다.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): 개발에 관한 질문을 합니다.
- [Microsoft Q&A 질문 페이지](/answers/topics/azure-synapse-analytics.html): 기술에 관한 질문을 합니다.
- [사용자 의견](https://aka.ms/sqlfeedback): 버그 및 요청 기능을 보고합니다.
- [Reddit](https://www.reddit.com/r/SQLServer/): SQL Server에 관하여 논의합니다.

## <a name="next-steps"></a>다음 단계

- 단일 데이터베이스 및 탄력적 풀에 관한 비용 비교 및 계산기는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요.
- 이러한 빠른 시작을 참조하여 다음 항목을 시작하세요.

  - [Azure Portal에서 데이터베이스 만들기](single-database-create-quickstart.md)  
  - [Azure CLI를 사용하여 데이터베이스 만들기](az-cli-script-samples-content-guide.md)
  - [PowerShell을 사용하여 데이터베이스 만들기](powershell-script-content-guide.md)

- 일련의 Azure CLI 및 PowerShell 샘플은 다음을 참조하세요.
  - [SQL Database에 대한 Azure CLI 샘플](az-cli-script-samples-content-guide.md)
  - [SQL Database에 대한 Azure PowerShell 샘플](powershell-script-content-guide.md)

- 별도로 공지된 새로운 기능에 대한 내용은 [SQL Database용 Azure 로드맵](https://azure.microsoft.com/roadmap/?category=databases)을 참조하세요.
- [Azure SQL Database 블로그](https://azure.microsoft.com/blog/topics/database)를 참조하세요. SQL Database 뉴스 및 기능에 대한 SQL Server 제품 팀 구성원 블로그를 확인할 수 있습니다.
