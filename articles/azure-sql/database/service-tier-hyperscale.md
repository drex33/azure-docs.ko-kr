---
title: 하이퍼스케일 서비스 계층이란?
description: 이 문서에서는 Azure SQL Database의 vCore 기반 구매 모델에 있는 하이퍼스케일 서비스 계층에 대해 설명하고, 범용 및 중요 비즈니스용 서비스 계층의 차이점도 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 9/9/2021
ms.openlocfilehash: 12f445c1b532ff3989010ca2a4b747ca0c241aef
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546496"
---
# <a name="hyperscale-service-tier"></a>하이퍼스케일 서비스 계층
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database는 인프라 오류의 경우에도 99.99%의 가용성을 보장하기 위해 클라우드 환경에 대해 조정되는 SQL Server 데이터베이스 엔진 아키텍처를 기반으로 합니다. Azure SQL Database에 사용되는 세 가지 아키텍처 모델이 있습니다.

- 범용/표준
- 하이퍼스케일
- 중요 비즈니스용/프리미엄

Azure SQL Database의 하이퍼스케일 서비스 계층은 vCore 기반 구매 모델의 최신 서비스 계층입니다. 이 서비스 계층은 Azure 아키텍처를 활용하여 범용 및 중요 비즈니스용 서비스 계층에 사용할 수 있는 제한을 초과하여 Azure SQL Database용 스토리지 및 컴퓨팅 리소스를 확장하는 확장성이 뛰어난 스토리지 및 컴퓨팅 성능 계층입니다.

> [!NOTE]
>
> - vCore 기반 구매 모델의 범용 및 중요 비즈니스용 서비스 계층에 대한 자세한 내용은 [범용](service-tier-general-purpose.md) 및 [중요 비즈니스용](service-tier-business-critical.md) 서비스 계층을 참조하세요. vCore 기반 구매 모델과 DTU 기반 구매 모델의 비교는 [Azure SQL Database 구매 모델 및 리소스](purchasing-models.md)를 참조하세요.
> - 하이퍼스케일 서비스 계층은 현재 Azure SQL Database에만 사용할 수 있으며, Azure SQL Managed Instance에는 사용할 수 없습니다.


> [!div class="nextstepaction"]
> [Azure SQL 개선하기 위한 설문 조사](https://aka.ms/AzureSQLSurveyNov2021)

## <a name="what-are-the-hyperscale-capabilities"></a>하이퍼스케일 기능에는 무엇이 있나요?

Azure SQL Database의 하이퍼스케일 서비스 계층은 다음과 같은 추가 기능을 제공합니다.

- 최대 100TB의 데이터베이스 크기 지원
- 컴퓨팅 리소스에 대한 I/O에 영향을 주지 않으면서 크기에 관계없이 거의 즉각적인 데이터베이스 백업(Azure Blob 스토리지에 저장된 파일 스냅샷 기반)  
- 몇 시간 또는 며칠이 아닌 몇 분 내에(데이터베이스 작업의 규모가 아닌) 빠른 데이터베이스 복원(파일 스냅샷 기반)
- 데이터 볼륨에 관계없이 트랜잭션 로그 처리량이 증가하고 트랜잭션 커밋 시간이 빨라져 전반적인 성능이 향상됩니다.
- 빠른 스케일 아웃 - 읽기 워크로드를 오프로드하고 핫 대기로 사용하기 위해 하나 이상의 [읽기 전용 복제본](service-tier-hyperscale-replicas.md)을 프로비저닝할 수 있습니다.
- 빠른 스케일 업 - 필요할 때 과도한 워크로드를 수용하도록 컴퓨팅 리소스를 지속적으로 스케일 업한 다음, 필요하지 않을 때 컴퓨팅 리소스를 다시 스케일 다운할 수 있습니다.

하이퍼스케일 서비스 계층은 클라우드 데이터베이스에서 기존에 확인되던 많은 실제 제한을 없애줍니다. 대부분의 다른 데이터베이스가 단일 노드에서 사용할 수 있는 리소스로 제한되지만 하이퍼스케일 서비스 계층의 데이터베이스에는 이러한 제한이 없습니다. 스토리지 아키텍처가 유연하기 때문에 필요에 따라 스토리지가 증가합니다. 실제로 하이퍼스케일 데이터베이스는 정의된 최대 크기로 만들어지지 않습니다. 하이퍼스케일 데이터베이스는 필요에 따라 확장되며, 사용하는 용량에 대해서만 요금이 청구됩니다. 읽기 집약적 워크로드의 경우 하이퍼스케일 서비스 계층에서 읽기 워크로드를 오프로드하는 데 필요한 추가 복제본을 프로비저닝하여 신속한 스케일 아웃을 제공합니다.

또한 데이터베이스 백업을 만들거나 규모 확대 또는 축소에 필요한 시간이 더 이상 데이터베이스의 데이터 볼륨과 관련되지 않습니다. 하이퍼스케일 데이터베이스는 거의 동시에 백업할 수 있습니다. 몇 분 안에 수십 테라바이트의 데이터베이스 규모를 확대 또는 축소할 수도 있습니다. 이 기능은 초기 구성 선택에 따른 여러 가지 우려를 해소해줍니다.

하이퍼스케일 서비스 계층의 컴퓨팅 크기에 대한 자세한 내용은 [서비스 계층 특성](service-tiers-vcore.md#service-tiers)을 참조하세요.

## <a name="who-should-consider-the-hyperscale-service-tier"></a>하이퍼스케일 서비스 계층을 고려하면 좋은 대상

하이퍼스케일 서비스 계층은 독립적으로 확장 가능한 컴퓨팅 및 스토리지 리소스를 사용하여 뛰어난 유연성과 고성능을 제공하므로 대부분의 비즈니스 워크로드에 적합합니다. 스토리지를 최대 100TB까지 자동 스케일링할 수 있는 기능은 다음과 같은 고객에게 매우 적합합니다.

- 대규모 데이터베이스가 온-프레미스에 있고, 클라우드로 이동하여 애플리케이션을 현대화하려고 합니다.
- 이미 클라우드에 있고, 다른 서비스 계층의 최대 데이터베이스 크기 제한(1~4TB)으로 제한됩니다.
- 데이터베이스가 작지만 수직 및 수평 컴퓨팅 스케일링, 고성능, 즉시 백업 및 빠른 데이터베이스 복원이 필요합니다.

하이퍼스케일 서비스 계층은 순수 OLTP에서 순수 분석에 이르기까지 광범위한 SQL 서버 워크로드를 지원하지만, 주로 OLTP 및 HTAP(하이브리드 트랜잭션 및 분석 처리) 워크로드에 최적화되어 있습니다.

> [!IMPORTANT]
> 탄력적 풀은 하이퍼스케일 서비스 계층을 지원하지 않습니다.

## <a name="hyperscale-pricing-model"></a>하이퍼스케일 가격 책정 모델

하이퍼스케일 서비스 계층은 [vCore 모델](service-tiers-vcore.md)에만 사용할 수 있습니다. 새 아키텍처에 맞게 가격 책정 모델이 범용 또는 중요 비즈니스용 서비스 계층과 약간 다릅니다.

- **컴퓨팅**:

  하이퍼스케일 컴퓨팅 단위 가격은 복제본별로 정해집니다. [Azure Hybrid 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/) 가격은 이름이 지정된 고가용성 복제본에 자동으로 적용됩니다. 기본적으로 Hyperscale 데이터베이스당 기본 복제본과 하나의 보조 [고가용성 복제본](service-tier-hyperscale-replicas.md)을 만듭니다.  사용자는 필요한 [SLA](https://azure.microsoft.com/support/legal/sla/azure-sql-database/)에 따라 총 고가용성 복제본 수를 0-4로 조정할 수 있습니다. 

- **스토리지**:

  하이퍼스케일 데이터베이스를 구성하는 경우 최대 데이터 크기를 지정할 필요가 없습니다. 하이퍼스케일 계층에서는 실제 할당에 따라 데이터베이스의 스토리지에 대한 요금이 청구됩니다. 스토리지는 40TB에서 100TB 사이에서 10GB 단위로 자동으로 할당됩니다. 필요한 경우 여러 데이터 파일을 동시에 확장할 수 있습니다. 시작 크기가 10GB인 하이퍼스케일 데이터베이스가 만들어지며, 40GB의 크기에 도달할 때까지 10분마다 10GB씩 확장합니다.

하이퍼스케일 가격 책정에 대한 자세한 내용은 [Azure SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/single/)을 참조하세요.

## <a name="distributed-functions-architecture"></a>분산 함수 아키텍처

모든 데이터 관리 기능을 하나의 위치/프로세스에 중앙 집중식으로 배치하는 기존 데이터베이스 엔진과 달리(소위 말하는 프로덕션의 분산 데이터베이스에도 모놀리식 데이터 엔진의 여러 복사본이 있음), 하이퍼스케일 데이터베이스는 여러 데이터 엔진의 의미 체계가 구분되어 있는 쿼리 처리 엔진을 데이터에 대한 장기 스토리지 및 지속성을 제공하는 구성 요소와 구분합니다. 이러한 방식으로 스토리지 용량을 필요한 만큼 원활하게 스케일 아웃할 수 있습니다(초기 목표는 100TB). 이름이 지정된 고가용성 복제본은 동일한 스토리지 구성 요소를 공유하므로 새 복제본을 스핀업하는 데 데이터 복사본이 필요하지 않습니다.

다음 다이어그램은 하이퍼스케일 데이터베이스에 있는 여러 유형의 노드를 보여 줍니다.

![아키텍처](./media/service-tier-hyperscale/hyperscale-architecture.png)

하이퍼스케일 데이터베이스에는 다음과 같은 다른 유형의 구성 요소가 포함됩니다.

### <a name="compute"></a>Compute

컴퓨팅 노드는 관계형 엔진이 상주하는 위치입니다. 여기서 언어, 쿼리 및 트랜잭션 처리가 수행됩니다. 하이퍼스케일 데이터베이스와의 모든 사용자 상호 작용은 이러한 컴퓨팅 노드를 통해 발생합니다. 컴퓨팅 노드에는 데이터 페이지를 가져오는 데 필요한 네트워크 왕복 횟수를 최소화하기 위해 SSD 기반 캐시[이전 다이어그램에 나오는 RBPEX(Resilient Buffer Pool Extension)]가 있습니다. 모든 읽기/쓰기 워크로드 및 트랜잭션이 처리되는 기본 컴퓨팅 노드가 1개 있습니다. 장애 조치(Failover)를 위해 핫 대기 노드의 역할을 할 뿐만 아니라 읽기 워크로드를 오프로드하기 위한 읽기 전용 컴퓨팅 노드(이 기능이 필요한 경우)의 역할을 하는 하나 이상의 보조 컴퓨팅 노드도 있습니다.

하이퍼스케일 컴퓨팅 노드에서 실행되는 데이터베이스 엔진은 다른 Azure SQL Database 서비스 계층에서와 동일합니다. 사용자가 하이퍼스케일 컴퓨팅 노드에서 데이터베이스 엔진과 상호 작용할 때 지원되는 노출 영역 및 엔진 동작은 [알려진 제한 사항](#known-limitations)을 제외하고 다른 서비스 계층에서와 동일합니다.

### <a name="page-server"></a>페이지 서버

페이지 서버는 스케일 아웃된 스토리지 엔진을 나타내는 시스템입니다.  각 페이지 서버는 데이터베이스의 페이지 하위 집합을 담당합니다.  명목상 각 페이지 서버는 최대 128GB 또는 최대 1TB의 데이터를 제어합니다. 둘 이상의 페이지 서버(중복성과 가용성을 위해 유지되는 페이지 서버 복제본 외부)에서 데이터가 공유되지 않습니다. 페이지 서버의 업무는 요청 시 컴퓨팅 노드에 데이터베이스 페이지를 제공하고, 트랜잭션으로 인해 데이터가 업데이트될 때 페이지를 최신 상태로 유지하는 것입니다. 페이지 서버는 로그 서비스에서 트랜잭션 로그 레코드를 재생하여 최신 상태로 유지됩니다. 또한 페이지 서버는 성능을 향상시키기 위해 SSD 기반 캐시 처리를 유지 관리합니다. 데이터 페이지의 장기 스토리지는 추가 안정성을 위해 Azure Storage에 보관됩니다.

### <a name="log-service"></a>로그 서비스

로그 서비스는 주 컴퓨팅 복제본의 트랜잭션 로그 레코드를 수락하고, 지속성 캐시에 보관하며, 데이터를 업데이트할 수 있도록 해당 캐시를 업데이트할 수 있도록 나머지 컴퓨팅 복제본과 관련 페이지 서버에 로그 레코드를 전달합니다. 이러한 방식으로 주 컴퓨팅 복제본의 모든 데이터 변경 내용은 로그 서비스를 통해 모든 보조 컴퓨팅 복제본 및 페이지 서버로 전파됩니다. 마지막으로 트랜잭션 로그 레코드는 가상 무한 스토리지 리포지토리인 Azure Storage 장기 스토리지로 푸시됩니다. 이 메커니즘을 사용하면 로그를 자주 잘라낼 필요가 없습니다. 또한 로그 서비스에는 로그 레코드에 대한 액세스 속도를 높이기 위해 로컬 메모리 및 SSD 캐시가 있습니다.

### <a name="azure-storage"></a>Azure Storage

Azure Storage는 데이터베이스의 모든 데이터 파일을 포함합니다. 페이지 서버는 Azure Storage의 데이터 파일을 최신 상태로 유지합니다. 이 스토리지는 Azure 지역 간의 복제 외에도 백업용으로 사용됩니다. 백업은 데이터 파일의 스토리지 스냅샷을 사용하여 구현됩니다. 스냅샷을 사용한 복원 작업은 데이터 크기에 관계없이 빠릅니다. 데이터는 데이터베이스의 백업 보존 기간 내의 특정 시점으로 복원할 수 있습니다.

## <a name="backup-and-restore"></a>백업 및 복원

백업은 파일 스냅샷을 기반으로 하므로 거의 즉각적입니다. 스토리지 및 컴퓨팅 분리를 사용하면 백업/복원 작업을 스토리지 계층으로 푸시하여 주 컴퓨팅 복제본의 처리 부담을 줄일 수 있습니다. 따라서 데이터베이스 백업은 주 컴퓨팅 노드의 성능에 영향을 주지 않습니다. 마찬가지로 PITR(특정 시점 복구)은 파일 스냅샷으로 되돌리는 방식으로 수행되며, 데이터 작업의 크기가 아닙니다. 동일한 Azure 지역에서 하이퍼스케일 데이터베이스를 복원하는 것은 일정한 시간 작업이며, 수 테라바이트의 데이터베이스도 몇 시간 또는 며칠이 아닌 몇 분 안에 복원할 수 있습니다. 기존 백업을 복원하여 새 데이터베이스를 만드는 것도 이 기능을 활용합니다. 개발 또는 테스트를 위해 수 테라바이트의 데이터베이스에 대한 데이터베이스 복사본조차도 몇 분 안에 만들 수 있습니다.

하이퍼스케일 데이터베이스의 지역 복원은 [하이퍼스케일 데이터베이스를 다른 지역으로 복원](#restoring-a-hyperscale-database-to-a-different-region)을 참조하세요.

## <a name="scale-and-performance-advantages"></a>확장 및 성능상의 이점

추가 읽기 전용 컴퓨팅 노드를 신속하게 스핀업/스핀다운하는 기능을 사용하여 하이퍼스케일 아키텍처는 상당한 읽기 기능을 허용하며 더 많은 쓰기 요청을 제공하기 위해 기본 컴퓨팅 노드를 해제할 수도 있습니다. 또한 하이퍼스케일 아키텍처의 공유 스토리지 아키텍처로 인해 컴퓨팅 노드 규모를 빠르게 확대/축소할 수 있습니다.

## <a name="create-a-hyperscale-database"></a>하이퍼스케일 데이터베이스 만들기

하이퍼스케일 데이터베이스는 [Azure Portal](https://portal.azure.com), [T-SQL](/sql/t-sql/statements/create-database-transact-sql), [PowerShell](/powershell/module/azurerm.sql/new-azurermsqldatabase) 또는 [CLI](/cli/azure/sql/db#az_sql_db_create)를 사용하여 만들 수 있습니다. 하이퍼스케일 데이터베이스는 [vCore 기반 구매 모델](service-tiers-vcore.md)을 통해서만 사용할 수 있습니다.

다음 T-SQL 명령은 하이퍼스케일 데이터베이스를 생성합니다. `CREATE DATABASE` 문에 버전 및 서비스 목표를 둘 다 지정해야 합니다. 유효한 서비스 목표 목록은 [리소스 제한](./resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen4)을 참조하세요.

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

그러면 4개의 코어가 있는 5세대 하드웨어 하이퍼스케일 데이터베이스가 만들어집니다.

## <a name="upgrade-existing-database-to-hyperscale"></a>기존 데이터베이스를 하이퍼스케일로 업그레이드

기존 Azure SQL Database의 기존 데이터베이스는 [Azure Portal](https://portal.azure.com), [T-SQL](/sql/t-sql/statements/alter-database-transact-sql), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase) 또는 [CLI](/cli/azure/sql/db#az_sql_db_update)를 사용하여 하이퍼스케일로 이동할 수 있습니다. 이 시점에서 이는 단방향 마이그레이션입니다. 데이터를 내보내고 가져오는 것 외에는 데이터베이스를 하이퍼스케일에서 다른 서비스 계층으로 이동할 수 없습니다. POC(개념 증명)의 경우 프로덕션 데이터베이스의 복사본을 만들고, 이 복사본을 하이퍼스케일로 마이그레이션하는 것이 좋습니다. Azure SQL Database의 기존 데이터베이스를 하이퍼스케일 계층으로 마이그레이션하는 것은 데이터 작업의 크기입니다.

다음 T-SQL 명령은 하이퍼스케일 서비스 계층으로 데이터베이스를 이동합니다. `ALTER DATABASE` 문에 버전 및 서비스 목표를 둘 다 지정해야 합니다.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

> [!NOTE]
> [지역 복제](active-geo-replication-overview.md) 관계의 일부인 데이터베이스(주 또는 보조)를 하이퍼스케일로 이동하려면 복제를 중지해야 합니다. [장애 조치(failover) 그룹](auto-failover-group-overview.md)의 데이터베이스는 먼저 그룹에서 제거해야 합니다.
>
> 데이터베이스가 하이퍼스케일로 이동된 후에는 해당 데이터베이스에 대해 새 하이퍼스케일 지역 복제본을 만들 수 있습니다. 하이퍼스케일에 대한 지역 복제는 미리 보기 상태이며 일부 [제한 사항](active-geo-replication-overview.md)이 있습니다.

## <a name="database-high-availability-in-hyperscale"></a>하이퍼스케일의 데이터베이스 고가용성

다른 모든 서비스 계층에서와 마찬가지로 하이퍼스케일은 컴퓨팅 복제본 가용성에 관계없이 커밋된 트랜잭션에 대한 데이터 내구성을 보장합니다. 사용할 수 없게 된 주 복제본으로 인한 가동 중지의 정도는 장애 조치(failover) 유형(계획된 유형 및 계획되지 않은 유형) 및 하나 이상의 고가용성 복제본이 있는지 여부에 따라 달라집니다. 계획된 장애 조치(failover)(즉, 유지 관리 이벤트)에서 시스템은 장애 조치(failover)를 시작하기 전에 새 주 복제본을 만들거나 기존 고가용성 복제본을 장애 조치(failover) 대상으로 사용합니다. 계획되지 않은 장애 조치(failover)(즉, 주 복제본의 하드웨어 오류)에서 시스템은 고가용성 복제본이 있는 경우 이를 장애 조치(failover) 대상으로 사용하거나 사용 가능한 컴퓨팅 용량 풀에서 새 주 복제본을 만듭니다. 후자의 경우 새 주 복제본을 만드는 데 필요한 추가 단계로 인해 가동 중지 기간이 더 길어집니다.

하이퍼스케일 SLA는 [Azure SQL Database에 대한 SLA](https://azure.microsoft.com/support/legal/sla/azure-sql-database)를 참조하세요.

## <a name="disaster-recovery-for-hyperscale-databases"></a>하이퍼스케일 데이터베이스에 대한 재해 복구

### <a name="restoring-a-hyperscale-database-to-a-different-region"></a>하이퍼스케일 데이터베이스를 다른 지역으로 복원

재해 복구 작업이나 훈련, 재배치 또는 기타 이유의 일부로 Azure SQL Database의 하이퍼스케일 데이터베이스를 현재 호스팅된 지역이 아닌 다른 지역으로 복원해야 하는 경우 기본 방법은 데이터베이스의 지역 복원을 수행하는 것입니다. 여기에는 SQL Database의 다른 데이터베이스를 다른 지역으로 복원하는 데 사용하는 것과 동일한 단계가 포함됩니다.

1. 적절한 서버가 아직 없는 경우 [서버](logical-servers.md)를 대상 지역에 만듭니다.  이 서버는 원래(원본) 서버와 동일한 구독에서 소유해야 합니다.
2. 자동 백업에서 Azure SQL Database의 데이터베이스를 복원하는 방법에 대한 페이지의 [지역 복원](./recovery-using-backups.md#geo-restore) 항목에 있는 지침을 따릅니다.

> [!NOTE]
> 원본과 대상이 별도의 지역에 있으므로 비지역 복원에서와 같이 데이터베이스는 데이터베이스 크기에 관계없이 빠르게 완료되는 스냅샷 스토리지를 원본 데이터베이스와 공유할 수 없습니다. 하이퍼스케일 데이터베이스에 대한 지역 복원의 경우 대상이 지역 복제된 스토리지의 쌍으로 연결된 지역에 있는 경우에도 데이터 크기 작업입니다. 따라서 지역 복원은 복원되는 데이터베이스 크기에 비례하여 시간이 걸립니다. 대상이 쌍으로 연결된 지역에 있는 경우 데이터 전송은 지역 내에서 수행되며, 이는 지역 간 데이터 전송보다 훨씬 빠르지만 여전히 데이터 크기 작업입니다.

## <a name="available-regions"></a><a name=regions></a>사용 가능한 지역

Azure SQL Database hyperscale 계층은 대부분의 Azure 지역에서 사용할 수 있습니다. 하이퍼스케일이 기본적으로 사용하도록 설정되지 않은 지역에서 하이퍼스케일 데이터베이스를 만들려는 경우 Azure Portal을 통해 온보딩 요청을 보낼 수 있습니다. 지침은 [Azure SQL Database에 대한 할당량 증가 요청](quota-increase-request.md)을 참조하세요. 요청을 제출하는 경우 다음 지침을 사용합니다.

- [지역 액세스](quota-increase-request.md#region) SQL Database 할당량 유형을 사용합니다.
- 설명에서 이름이 지정된 고가용성 복제본을 포함하여 컴퓨팅 SKU/총 코어 수를 추가하고, 하이퍼스케일 용량을 요청하고 있음을 나타냅니다.
- 또한 시간 경과에 따른 모든 데이터베이스의 전체 크기(TB)에 대한 예측을 지정합니다.


## <a name="known-limitations"></a>알려진 제한 사항

이는 현재 GA 상태의 하이퍼스케일 서비스 계층에 대한 제한 사항입니다.  Microsift는 이러한 제한 사항을 최대한 많이 제거하기 위해 적극적으로 노력하고 있습니다.

| 문제 | Description |
| :---- | :--------- |
| 서버에 대한 [백업 관리] 창에 하이퍼스케일 데이터베이스가 표시되지 않습니다. 이러한 데이터베이스는 보기에서 필터링됩니다.  | 하이퍼스케일에는 백업을 관리하는 별도의 방법이 있으므로 장기 보존 및 특정 시점 백업 보존 설정이 적용되지 않습니다. 따라서 하이퍼스케일 데이터베이스가 [백업 관리] 창에 나타나지 않습니다.<br><br>다른 Azure SQL Database 서비스 계층에서 하이퍼스케일로 마이그레이션된 데이터베이스의 경우 마이그레이션 전 백업은 원본 데이터베이스의 [백업 보존](automated-backups-overview.md#backup-retention) 기간 동안 유지됩니다. 이러한 백업은 원본 데이터베이스를 마이그레이션 전의 특정 시점으로 [복원](recovery-using-backups.md#programmatic-recovery-using-automated-backups)하는 데 사용할 수 있습니다.|
| 지정 시간 복원 | 하이퍼스케일이 아닌 데이터베이스는 하이퍼스케일 데이터베이스로 복원할 수 없으며, 반대의 경우도 마찬가지입니다. 서비스 계층을 변경하여 하이퍼스케일로 마이그레이션된 하이퍼스케일이 아닌 데이터베이스의 경우 마이그레이션 전의 특정 시점으로 복원하고 데이터베이스의 백업 보존 기간 내에 [프로그래밍 방식](recovery-using-backups.md#programmatic-recovery-using-automated-backups)으로 지원합니다. 복원된 데이터베이스는 하이퍼스케일이 아닙니다. |
| Azure SQL Database 서비스 계층을 하이퍼스케일로 변경하는 경우 데이터베이스에 1TB보다 큰 데이터 파일이 있으면 작업이 실패합니다. | 경우에 따라 서비스 계층을 하이퍼스케일로 변경하려고 시도하기 전에 대용량 파일을 1TB 미만으로 [축소](file-space-manage.md#shrinking-data-files)하여 이 문제를 해결할 수 있습니다. 다음 쿼리를 사용하여 데이터베이스 파일의 현재 크기를 확인합니다. `SELECT file_id, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| SQL Managed Instance | Azure SQL Managed Instance는 현재 하이퍼스케일 데이터베이스에서 지원되지 않습니다. |
| 탄력적 풀 |  탄력적 풀은 현재 하이퍼스케일에서 지원되지 않습니다.|
| 하이퍼스케일로 마이그레이션은 현재 단방향 작업입니다. | 데이터베이스가 하이퍼스케일로 마이그레이션되면 하이퍼스케일이 아닌 서비스 계층으로 직접 마이그레이션할 수 없습니다. 현재 하이퍼스케일 데이터베이스에서 하이퍼스케일이 아닌 데이터베이스로 마이그레이션하는 유일한 방법은 bacpac 파일 또는 기타 데이터 이동 기술(대량 복사, Azure Data Factory, Azure Dataricks, SSIS 등)을 사용하여 내보내거나 가져오는 것입니다. bacpac 내보내기/가져오기는 Azure Portal, PowerShell([New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) 또는 [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) 사용), Azure CLI([az sql db export](/cli/azure/sql/db#az_sql_db_export) 및 [az sql db import](/cli/azure/sql/db#az_sql_db_import) 사용) 및[REST API](/rest/api/sql/)에서 지원되지 않습니다. 소형 하이퍼스케일 데이터베이스(최대 200GB)에 대한 bacpac 가져오기/내보내기는 SSMS 및 [SqlPackage](/sql/tools/sqlpackage) 버전 18.4 이상을 사용하여 지원됩니다. 대형 데이터베이스의 경우 bacpac 내보내기/가져오기에 시간이 오래 걸릴 수 있으며, 여러 가지 이유로 실패할 수 있습니다.|
| 메모리 내 OLTP 개체가 포함된 데이터베이스 마이그레이션 | 하이퍼스케일은 메모리 최적화 테이블 형식, 테이블 변수 및 고유하게 컴파일된 모듈을 포함하여 메모리 내 OLTP 개체의 하위 집합을 지원합니다. 그러나 마이그레이션되는 데이터베이스에 모든 종류의 메모리 내 OLTP 개체가 있는 경우 프리미엄 및 중요 비즈니스용 서비스 계층에서 하이퍼스케일로의 마이그레이션은 지원되지 않습니다. 이러한 데이터베이스를 하이퍼스케일로 마이그레이션하려면 모든 메모리 내 OLTP 개체 및 해당 종속성을 삭제해야 합니다. 데이터베이스가 마이그레이션되면 이러한 개체를 다시 만들 수 있습니다. 내구성 및 비내구성 메모리 최적화 테이블은 현재 하이퍼스케일에서 지원되지 않으며, 디스크 테이블로 변경해야 합니다.|
| 지역에서 복제  | 하이퍼스케일의 [지역 복제](active-geo-replication-overview.md)가 현재 공개 미리 보기로 제공됩니다. |
| 인텔리전트 데이터베이스 기능 | "계획 강제 적용" 옵션을 제외하고 다른 모든 자동 튜닝 옵션은 아직 하이퍼스케일에서 지원되지 않습니다. 이 옵션은 사용하도록 설정된 것처럼 보일 수 있지만 권장 사항 또는 작업이 수행되지 않습니다. |
| Query Performance Insight | Query Performance Insights는 현재 하이퍼스케일 데이터베이스를 지원하지 않습니다. |
| 데이터베이스 축소 | DBCC SHRINKDATABASE 또는 DBCC SHRINKFILE은 현재 하이퍼스케일 데이터베이스를 지원하지 않습니다. |
| 데이터베이스 무결성 검사 | DBCC CHECKDB는 현재 하이퍼스케일 데이터베이스를 지원하지 않습니다. 해결 방법으로 DBCC CHECKFILEGROUP 및 DBCC CHECKTABLE을 사용할 수 있습니다. Azure SQL Database의 데이터 무결성 관리에 대한 자세한 내용은 [Azure SQL Database의 데이터 무결성](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)을 참조하세요. |
| 탄력적 작업 | 하이퍼스케일 데이터베이스를 작업 데이터베이스로 사용하는 것은 지원되지 않습니다. 그러나 탄력적 작업은 다른 Azure SQL 데이터베이스와 동일한 방식으로 하이퍼스케일 데이터베이스를 대상으로 할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

- 하이퍼스케일에 대한 FAQ는 [하이퍼스케일에 대한 질문과 대답](service-tier-hyperscale-frequently-asked-questions-faq.yml)을 참조하세요.
- 서비스 계층에 대한 자세한 내용은 [서비스 계층](purchasing-models.md)을 참조하세요.
- 서버 및 구독 수준의 제한에 대한 자세한 내용은 [서버에 대한 리소스 제한 개요](resource-limits-logical-server.md)를 참조하세요.
- 단일 데이터베이스에 대한 구매 모델 제한은 [단일 데이터베이스에 대한 Azure SQL Database vCore 기반 구매 모델 제한](resource-limits-vcore-single-databases.md)을 참조하세요.
- 기능 및 비교 목록은 [SQL 일반 기능](features-comparison.md)을 참조하세요.
