---
title: Azure Arc 지원 PostgreSQL 하이퍼스케일이란?
description: Azure Arc 지원 PostgreSQL 하이퍼스케일이란?
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: fa2a244a0a8e2181f5e3d396980c8239ab868584
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131553002"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일이란?

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


## <a name="what-is-azure-arc-vs-azure-arc-enabled-data-services-vs-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 및 Azure Arc 지원 데이터 서비스와 Azure Arc 지원 PostgreSQL 하이퍼스케일이란?

**Azure Arc** Azure 하이브리드 제품군의 핵심 요소 중 하나인 Azure Arc, Azure Stack 및 Azure IoT. Azure Arc 고객이 고객 환경을 간소화하여 하이브리드 배포의 복잡성을 관리하는 데 도움이 됩니다. Azure Stack 통해 Microsoft 또는 해당 파트너는 하드웨어 및 소프트웨어(어플라이언스)를 제공합니다. Azure Arc Microsoft는 소프트웨어만 제공합니다. 고객 또는 파트너는 지원 인프라를 제공하고 솔루션을 운영합니다. Azure Arc Azure Stack 지원됩니다. Azure Arc 사용하면 Azure 데이터 센터 외부에 있는 인프라에서 Azure 서비스를 실행할 수 있으며 원하는 경우 다른 Azure 관리 서비스와 통합할 수 있습니다.

**Azure Arc 지원 데이터 서비스는** Azure Arc 일부입니다. 고객이 데이터를 관리할 수 있는 제품 및 서비스 제품군입니다. 이를 통해 고객은 다음을 수행할 수 있습니다.

- 물리적 인프라에서 Azure 데이터 서비스 실행
- 어디서나 동일한 클라우드 기술을 사용하여 운영 최적화
- 애플리케이션 또는 데이터베이스가 호스트되는 위치(Azure PaaS 또는 Azure Arc)와 관계없이 동일한 기본 기술을 사용하여 애플리케이션 개발을 최적화합니다.
- 자체 데이터 센터에서 클라우드 기술을 사용하지만 규정 요구 사항(데이터 상주 & 고객 제어)을 충족합니다. 즉, "클라우드에 올 수 없는 경우 클라우드가 제공됩니다."

Azure Arc 지원 데이터 서비스에서 제공하는 일부 값은 다음과 같습니다.
- 항상 최신
- 탄력적 확장
- 셀프서비스 프로비저닝
- 통합 관리
- 클라우드 청구
- 연결(Azure에) 및 경우에 따라 연결된(Azure에) 시나리오에 대한 지원 (직접 및 간접 연결 모드)

**Azure Arc 지원 PostgreSQL 하이퍼스케일은** Azure Arc 지원 데이터 서비스의 일부로 사용할 수 있는 데이터베이스 엔진 중 하나입니다. 


## <a name="compare-postgres-solutions-provided-by-microsoft-in-azure"></a>Azure에서 Microsoft가 제공하는 Postgres 솔루션 비교

Microsoft는 다음 두 가지 방법으로 Azure에서 Postgres 데이터베이스 서비스를 제공합니다.
- Azure PaaS에서 관리되는 서비스로(Platform as A Service)
- 고객 또는 파트너/공급업체가 운영하는 Azure Arc 있는 반관리형 서비스

### <a name="in-azure-paas"></a>Azure PaaS에서
**[Azure PaaS에서](https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer)** Microsoft는 Postgres에 대한 몇 가지 배포 옵션을 관리형 서비스로 제공합니다.

:::row:::
    :::column:::
        Azure Database for Postgres 단일 서버 및 Azure Database for Postgres 유연한 서버. 이러한 서비스는 Microsoft에서 관리하는 단일 노드/단일 인스턴스 Postgres 폼 팩터입니다. Azure Database for Postgres 유연한 서버는 이 서비스의 최신 진화입니다.
    :::column-end:::
    :::column:::
        :::image type="content" source="media/postgres-hyperscale/azure-database-for-postgresql-bigger.png" alt-text="Azure Database for PostgreSQL":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        Azure Database for PostgreSQL 하이퍼스케일(Citus). 이 서비스는 Microsoft에서 관리하는 다중 노드/다중 인스턴스 Postgres 폼 팩터입니다. 단일 노드 Postgres를 분산 데이터베이스 시스템으로 변환하는 Postgres에 대한 Citus 확장을 통해 구동됩니다. 규모를 확장하면 워크로드가 비교할 수 없는 수준의 규모 및 성능에 도달할 수 있는 데이터와 쿼리가 배포됩니다. 애플리케이션에는 서버 그룹이라고도 하는 단일 Postgres 인스턴스가 표시됩니다. 그러나 이 서버 그룹은 함께 작동하는 여러 Postgres 인스턴스로 구성됩니다. 확장하면 서버 그룹 내의 Postgres 인스턴스 수가 증가하여 워크로드의 성능과 확장성이 향상될 수 있습니다. 사용자의 요구 사항 및 워크로드의 특성에 따라 서버 그룹에 추가하는 Postgres 인스턴스 수를 결정합니다. 
        자세한 인사이트를 보려면 다음 비디오를 시청하세요.
        - [데모 - Postgres 하이퍼스케일(Citus) 고성능 HTAP:](https://youtu.be/W_3e07nGFxY )
        - [데모 – Azure에서 Python & Postgres를 통해 HTAP 애플리케이션 빌드:](https://youtu.be/YDT8_riLLs0 )
    :::column-end:::
    :::column:::
        :::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Azure Database for PostgreSQL 하이퍼스케일(Citus)":::
    :::column-end:::
:::row-end:::



### <a name="with-azure-arc"></a>With Azure Arc

:::row:::
    :::column:::
        **Azure Arc Microsoft는** **단일** Postgres 제품/서비스인 **Azure Arc 지원 PostgreSQL 하이퍼스케일 을** 제공합니다. Azure Arc 통해 다음을 수행할 수 있는 하나의 Postgres 제품을 제공하여 Azure PaaS에 비해 **Postgres에** 대한 제품 정의 및 고객 환경을 간소화했습니다.
        - Azure Database for Postgres 단일/유연한 서버와 같은 단일 노드/단일 인스턴스 Postgres 배포
        - Azure Database for PostgreSQL 하이퍼스케일(Citus) 같은 다중 노드/다중 인스턴스 Postgres 배포
        - 고객은 Postgres 배포를 한 노드에서 Postgres의 다중 노드로, 원하는 경우 그 반대로 변형할 수 있도록 함으로써 뛰어난 유연성을 제공합니다. 데이터 마이그레이션 없이 간단한 환경으로 이 작업을 수행할 수 있습니다.
    :::column-end:::
    :::column:::
        :::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="Azure Arc 지원 PostgreSQL 하이퍼스케일":::
    :::column-end:::
:::row-end:::

Azure PaaS의 형제와 마찬가지로 다중 노드/인스턴스 형식에서 Postgres는 단일 노드 Postgres를 분산 데이터베이스 시스템으로 변환하는 Citus 확장을 통해 구동됩니다. 규모를 확장하면 워크로드가 예상치 못한 규모 및 성능 수준에 도달할 수 있는 데이터와 쿼리가 배포됩니다. 애플리케이션에는 서버 그룹이라고도 하는 단일 Postgres 인스턴스가 표시됩니다. 그러나 이 서버 그룹은 함께 작동하는 여러 Postgres 인스턴스로 구성됩니다. 확장하면 서버 그룹 내의 Postgres 인스턴스 수가 증가하여 워크로드의 성능과 확장성이 향상될 수 있습니다. 사용자의 요구 사항 및 워크로드의 특성에 따라 서버 그룹에 추가하는 Postgres 인스턴스 수를 결정합니다. 원하는 경우 서버 그룹의 Postgres 인스턴스 수를 1로 줄일 수 있습니다.  Azure Arc 통해 Microsoft에서 제공하는 모든 Postgres 배포는 하이퍼스케일 준비가 완료됩니다.


Azure Arc 지원 데이터 서비스에서 제공하는 직접 연결 모드를 사용하면 Azure Portal Azure Arc 지원 PostgreSQL 하이퍼스케일을 배포할 수 있습니다. 간접 연결 모드를 사용하는 경우 호스트하는 인프라에서 Azure Arc 지원 PostgreSQL 하이퍼스케일을 배포합니다.

**Azure Arc 사용하도록 설정된 PostgreSQL 하이퍼스케일을 사용하면 다음을 수행할 수 있습니다.**
- 간단히 Postgres 관리
    - 하나의 명령으로 Postgres 인스턴스 프로비저닝/프로비저닝 해제
    - 모든 규모에서: 강화/축소/규모 확장/축소/축소 
- 모니터링, 장애 조치(failover), 백업, 패치/업그레이드, 액세스 제어 & 간소화
- 예상치 못한 규모 & 성능으로 Postgres 앱 빌드
    - 여러 Postgres 인스턴스에서 수평으로 컴퓨팅 확장
    - 데이터 및 쿼리 배포
    - Citus 확장 실행
    - 표준 PostgreSQL을 분산 데이터베이스 시스템으로 변환
- 인프라에 Postgres 배포
    - 온-프레미스, 다중 클라우드(AWS, GCP, Azure), 에지
- Azure와 통합(선택 사항)
- 사용한 요금 지불(사용량 청구당)
- Postgres에서 Microsoft의 지원 받기

**추가 고려 사항:**
- Azure Arc 사용하도록 설정된 PostgreSQL 하이퍼스케일은 새 데이터베이스 엔진이 아니거나 기존 데이터베이스 엔진의 특정 버전이 아닙니다. Azure PaaS에서 실행되는 것과 동일한 데이터베이스 엔진입니다. microsoft 클라우드에 올 수 없는 경우 Azure Arc 주의하세요. Microsoft 클라우드가 곧 출시될 예정입니다. Azure Arc 혁신은 Microsoft가 이 데이터베이스 엔진을 제공하는 방법과 Microsoft가 이 데이터베이스 엔진에 대해 제공하는 경험에 있습니다. 

- Azure Arc 지원 PostgreSQL 하이퍼스케일은 데이터 복제 솔루션도 아닙니다. 비즈니스 데이터는 Arc 배포에 유지됩니다. Azure 클라우드에 복제되지 않습니다. 데이터 복제/읽기 복제본과 같은 데이터베이스 엔진의 기능을 설정하도록 선택한 경우가 아니면 이 경우 데이터는 Azure Arc 아니라 데이터 복제 기능을 설정하도록 선택했기 때문에 Postgres 배포 외부에서 복제될 수 있습니다.

- Azure Arc 사용 가능한 PostgreSQL 하이퍼스케일에 대해 실행하기 위해 워크로드에 특정 드라이버 또는 공급자를 사용할 필요가 없습니다. 모든 "Postgres 애플리케이션"은 Azure Arc 지원 PostgreSQL 하이퍼스케일에 대해 실행할 수 있어야 합니다.

- 규모 확장 및 규모 축소 작업은 자동으로 수행되지 않습니다. 사용자가 제어합니다. 사용자는 이러한 작업을 스크립팅하고 해당 스크립트의 실행을 자동화할 수 있습니다. 모든 워크로드가 스케일 아웃을 활용할 수 있는 것은 아닙니다. "다음 단계" 섹션에서 제시한 대로 이 항목에 대한 자세한 내용을 읽어보십시오.

**이러한 기능에 대해 자세히 알아보려면 이 데이터 노출 에피소드를 참조할 수도 있습니다.**
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/What-is-Azure-Arc-Enabled-PostgreSQL-Hyperscale--Data-Exposed/player?format=ny]


## <a name="roles-and-responsibilities-azure-managed-services-platform-as-a-service-paas-_vs_-azure-arc-enabled-data-services"></a>역할 및 책임: Azure 관리형 서비스(PaaS(Platform as a Service)) _및_ Azure Arc 지원 데이터 서비스
:::image type="content" source="media/postgres-hyperscale/rr-azure-paas-vs-azure-arc.png" alt-text="Azure PaaS와 Azure Arc 역할 및 책임":::

## <a name="next-steps"></a>다음 단계
- **사용해보기.** AKS(Azure Kubernetes Service), AWS EKS(Elastic Kubernetes Service), Google GKE(Cloud Kubernetes Engine) 또는 Azure VM에서 [Azure Arc 빠른 시작](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services)을 사용하여 빠르게 시작하세요. 

- **배포하고 직접 만듭니다.** 고유한 Kubernetes 클러스터에 만들려면 다음 단계를 따르세요. 
   1. [클라이언트 도구 설치](install-client-tools.md)
   2. [Azure Arc 데이터 컨트롤러 만들기](create-data-controller.md)
   3. [Azure Arc에 Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 만들기](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Azure Arc](https://aka.ms/azurearc)
   - 여기 [및](https://azure.microsoft.com/services/azure-arc/hybrid-data-services) [여기에서](overview.md) Azure Arc 지원 데이터 서비스
   - [연결 모드 및 요구 사항](connectivity.md)



- **여러 PostgreSQL 하이퍼스케일 노드에 데이터를 분산하고 잠재적으로 더 나은 성능을 활용하려면 하이퍼스케일을 Azure Database for PostgreSQL 개념 및 방법 가이드를 읽어보세요.**
    * [노드 및 테이블](../../postgresql/concepts-hyperscale-nodes.md)
    * [애플리케이션 유형 확인](../../postgresql/concepts-hyperscale-app-type.md)
    * [배포 열 선택](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [테이블 공동 배치](../../postgresql/concepts-hyperscale-colocation.md)
    * [테이블 분산 및 수정](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [다중 테넌트 데이터베이스 설계](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [실시간 분석 대시보드 설계](../../postgresql/tutorial-design-database-hyperscale-realtime.md)* 

