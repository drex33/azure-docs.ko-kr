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
ms.openlocfilehash: a1b91c76f8bacd75e7cb91febf084676d3b15101
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132954394"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일이란?

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


## <a name="what-is-azure-arc-vs-azure-arc-enabled-data-services-vs-azure-arc-enabled-postgresql-hyperscale"></a>Azure arc 및 azure arc 사용 데이터 서비스와 Azure Arc 지원 PostgreSQL Hyperscale 이란?

Azure **arc** 는 azure 핵심 요소, Azure Stack 및 Azure IoT 중 하나입니다. Azure Arc는 고객이 사용자 환경을 단순화 하 여 하이브리드 배포의 복잡성을 관리 하는 데 도움이 됩니다. Azure Stack Microsoft 또는 해당 파트너는 하드웨어 및 소프트웨어 (어플라이언스)를 제공 합니다. Microsoft는 Azure Arc를 사용 하 여 소프트웨어를 제공 합니다. 고객 또는 파트너는 지원 인프라를 제공 하 고 솔루션을 운영 합니다. Azure Arc는 Azure Stack에서 지원 됩니다. Azure Arc를 사용 하면 azure 데이터 센터 외부에 있는 인프라에서 Azure 서비스를 실행할 수 있으며, 원하는 경우 다른 Azure 관리 서비스와 통합할 수 있습니다.

**Azure arc 사용 데이터 서비스** 는 azure arc의 일부입니다. 고객이 데이터를 관리할 수 있도록 하는 제품 및 서비스 모음입니다. 이를 통해 고객은 다음을 수행할 수 있습니다.

- 모든 물리적 인프라에서 Azure 데이터 서비스 실행
- 모든 곳에서 동일한 클라우드 기술을 사용 하 여 작업 최적화
- 응용 프로그램 또는 데이터베이스가 호스트 되는 위치에 관계 없이 동일한 기본 기술을 사용 하 여 응용 프로그램 개발 최적화 (Azure PaaS 또는 Azure Arc에서)
- 사용자 고유의 데이터 센터에서 클라우드 기술을 사용 하 고 규정 요구 사항을 충족 합니다 (데이터 상주 & 고객 제어). 즉, 클라우드로 이동할 수 없는 경우 클라우드는 귀하에 게 제공 됩니다.

Azure Arc 사용 데이터 서비스에서 제공 하는 몇 가지 값은 다음과 같습니다.
- 항상 최신
- 탄력적 확장
- 셀프서비스 프로비저닝
- 통합 관리
- 클라우드 청구
- 연결 된 (Azure에 대 한) 및 경우에 따라 연결 된 (Azure) 시나리오에 대 한 지원. (직접 및 간접 연결 모드)

**Azure arc 사용 PostgreSQL Hyperscale** 은 azure arc 사용 데이터 서비스의 일부로 제공 되는 데이터베이스 엔진 중 하나입니다. 


## <a name="compare-postgres-solutions-provided-by-microsoft-in-azure"></a>Azure에서 Microsoft가 제공 하는 Postgres 솔루션 비교

Microsoft는 다음과 같은 두 가지 방법으로 Azure에서 Postgres 데이터베이스 서비스를 제공 합니다.
- Azure PaaS (Platform As A Service)의 관리 되는 서비스
- 고객 또는 파트너/공급 업체가 운영 하는 Azure Arc를 사용 하는 반 관리 서비스로

### <a name="in-azure-paas"></a>Azure PaaS
**[Azure PaaS](https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer)에서** Microsoft는 관리 서비스로 postgres를 위한 몇 가지 배포 옵션을 제공 합니다.

:::row:::
    :::column:::
        Postgres 단일 서버에 대 한 azure Database 및 Postgres 유연한 서버용 Azure Database 이러한 서비스는 Microsoft에서 관리 하는 단일 노드/단일 인스턴스 Postgres 폼 팩터입니다. Azure Database for Postgres 유연한 서버는이 서비스의 가장 최근 진화입니다.
    :::column-end:::
    :::column:::
        :::image type="content" source="media/postgres-hyperscale/azure-database-for-postgresql-bigger.png" alt-text="Azure Database for PostgreSQL":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        Azure Database for PostgreSQL Hyperscale (Citus). 이 서비스는 Microsoft에서 관리 하는 다중 노드/다중 인스턴스 Postgres 폼 요소입니다. 단일 노드 Postgres를 분산 된 데이터베이스 시스템으로 변환 하는 Postgres의 Citus 확장에 의해 구동 됩니다. 규모를 확장 하는 동안 데이터와 잠재적으로 작업을 허용 하는 쿼리를 분산 하 여 확장성과 성능을 향상 시킬 수 있습니다. 응용 프로그램은 서버 그룹이 라고도 하는 단일 Postgres 인스턴스를 볼 수 있습니다. 그러나 내부적으로이 서버 그룹은 함께 작동 하는 여러 Postgres 인스턴스를 구성 된 합니다. 규모를 확장 하면 워크 로드의 성능 및 확장성을 잠재적으로 향상 시키는 서버 그룹 내의 Postgres 인스턴스 수가 증가 합니다. 사용자의 요구 사항 및 워크 로드의 특성, 서버 그룹에 추가 하는 Postgres 인스턴스 수에 따라 결정 합니다. 
        자세한 정보는 다음 비디오를 시청 하세요.
        - [데모-Postgres Hyperscale (Citus)을 사용 하는 고성능 HTAP:](https://youtu.be/W_3e07nGFxY )
        - [데모 – Azure에서 Python & Postgres를 사용 하 여 HTAP 응용 프로그램 빌드:](https://youtu.be/YDT8_riLLs0 )
    :::column-end:::
    :::column:::
        :::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Azure Database for PostgreSQL 하이퍼스케일(Citus)":::
    :::column-end:::
:::row-end:::



### <a name="with-azure-arc"></a>Azure Arc 사용

:::row:::
    :::column:::
        **Azure arc를 사용 하 여** Microsoft **는 단일** postgres 제품/서비스를 제공 합니다. **Azure arc 사용 PostgreSQL hyperscale**. Azure Arc를 사용 하면 다음을 수행할 수 있는 **하나의 postgres 제품** 을 제공 하 여 azure PaaS와 비교 하 여 postgres에 대 한 제품 정의 및 고객 환경을 간소화 했습니다.
        - Postgres 단일/유연한 서버용 Azure Database와 같은 단일 노드/단일 인스턴스 Postgres 배포
        - Citus () Azure Database for PostgreSQL와 같은 다중 노드/다중 인스턴스 Postgres 배포
        - 고객 들이 Postgres 배포를 단일 노드에서 Postgres의 다중 노드로, 원하는 경우 그 반대로 할 수 있으므로 유연성이 매우 높습니다. 데이터 마이그레이션 없이 간단한 환경을 사용 하 여이 작업을 수행할 수 있습니다.
    :::column-end:::
    :::column:::
        :::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="Azure Arc 지원 PostgreSQL 하이퍼스케일":::
    :::column-end:::
:::row-end:::

Azure PaaS의 해당 형제와 마찬가지로, 다중 노드/인스턴스 형태에서 Postgres는 단일 노드 Postgres를 분산 된 데이터베이스 시스템으로 변환 하는 Citus 확장을 기반으로 합니다. 규모를 확장 하는 동안 작업을 잠재적으로 잠재적으로 확장 및 성능 수준에 도달할 수 있는 데이터와 쿼리를 배포 합니다. 응용 프로그램은 서버 그룹이 라고도 하는 단일 Postgres 인스턴스를 볼 수 있습니다. 그러나 내부적으로이 서버 그룹은 함께 작동 하는 여러 Postgres 인스턴스를 구성 된 합니다. 규모를 확장 하면 서버 그룹 내의 Postgres 인스턴스 수가 증가 하 여 워크 로드의 성능 및 확장성을 향상 시킬 수 있습니다. 사용자의 요구 사항 및 워크 로드의 특성, 서버 그룹에 추가 하는 Postgres 인스턴스 수에 따라 결정 합니다. 원할 경우 서버 그룹의 Postgres 인스턴스 수를 1로 줄일 수 있습니다.  Azure Arc를 사용 하면 Microsoft에서 제공 하는 모든 Postgres 배포가 하이퍼 확장 준비 됩니다.


Azure Arc 사용 데이터 서비스에서 제공 하는 직접 연결 모드를 사용 하 여 Azure Portal에서 Azure Arc 사용 PostgreSQL Hyperscale을 배포할 수 있습니다. 간접 연결 모드를 사용 하는 경우이를 호스트 하는 인프라에서 Azure Arc 사용 PostgreSQL Hyperscale을 배포 합니다.

**Azure Arc 사용 PostgreSQL Hyperscale을 사용 하면 다음을 수행할 수 있습니다.**
- 간단히 Postgres 관리
    - 명령 하나를 사용 하 여 Postgres 인스턴스 프로 비전/프로 비전 해제
    - 모든 규모에서: 확장/축소/축소/축소 
- 모니터링, 장애 조치 (failover), 백업, 패치/업그레이드, 액세스 제어 &
- 뛰어난 규모의 성능 & 성능으로 Postgres 앱 빌드
    - 여러 Postgres instances에서 계산을 수평으로 확장
    - 데이터 및 쿼리 배포
    - Citus 확장을 실행 합니다.
    - 표준 PostgreSQL를 분산 데이터베이스 시스템으로 변환
- 모든 인프라에 Postgres 배포
    - 온-프레미스, 다중 클라우드 (AWS, GCP, Azure), edge
- Azure와 통합 (선택 사항)
- 사용한 만큼 지불 (사용량 청구 기준)
- Microsoft의 Postgres에서 지원 받기

**추가 고려 사항:**
- Azure Arc-enabled PostgreSQL Hyperscale은 새 데이터베이스 엔진이 아니거나 기존 데이터베이스 엔진의 특정 버전이 아닙니다. Azure PaaS에서 실행 되는 것과 동일한 데이터베이스 엔진입니다. Microsoft 클라우드로 이동할 수 없는 경우 Azure Arc를 사용 합니다. Microsoft 클라우드는 귀하에 게 제공 됩니다. Azure Arc의 혁신은 Microsoft에서이 데이터베이스 엔진을 제공 하는 방법 및 Microsoft가이 데이터베이스 엔진을 중심으로 제공 하는 환경에 있습니다. 

- Azure Arc 사용 PostgreSQL Hyperscale은 데이터 복제 솔루션이 아닙니다. 비즈니스 데이터는 Arc 배포에 그대로 유지 됩니다. Azure 클라우드에 복제 되지 않습니다. 데이터 복제/복제본 읽기와 같은 데이터베이스 엔진의 기능을 설정 하도록 선택 하지 않은 경우 이 경우 데이터는 Azure Arc 때문이 아니라 데이터 복제 기능을 설정 하도록 선택 했기 때문에 Postgres 배포 외부에서 복제 될 수 있습니다.

- Azure Arc 사용 PostgreSQL Hyperscale에 대해 실행 하기 위해 워크 로드에 특정 드라이버 또는 공급자를 사용할 필요가 없습니다. 모든 "Postgres 응용 프로그램"은 Azure Arc 사용 PostgreSQL Hyperscale에 대해 실행할 수 있어야 합니다.

- 규모 확장 및 규모 확장 작업은 자동으로 수행 되지 않습니다. 사용자가 제어 합니다. 사용자는 이러한 작업을 스크립팅할 수 있으며 이러한 스크립트의 실행을 자동화할 수 있습니다. 일부 워크 로드는 규모 확장의 이점을 누릴 수 없습니다. "다음 단계" 섹션에서 제안 하는 것 처럼이 항목에 대 한 자세한 내용을 참조 하세요.

**이러한 기능에 대해 자세히 알아보려면이 데이터 노출 에피소드를 참조할 수도 있습니다.**
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/What-is-Azure-Arc-Enabled-PostgreSQL-Hyperscale--Data-Exposed/player?format=ny]


## <a name="roles-and-responsibilities-azure-managed-services-platform-as-a-service-paas-_vs_-azure-arc-enabled-data-services"></a>역할 및 책임: Azure 관리 서비스 (PaaS (Platform as a service))와 azure Arc 사용 데이터 서비스 _비교_
:::image type="content" source="media/postgres-hyperscale/rr-azure-paas-vs-azure-arc.png" alt-text="Azure PaaS 및 Azure Arc의 역할 및 책임":::

## <a name="next-steps"></a>다음 단계
- **사용해보기.** AKS(Azure Kubernetes Service), AWS EKS(Elastic Kubernetes Service), Google GKE(Cloud Kubernetes Engine) 또는 Azure VM에서 [Azure Arc 빠른 시작](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services)을 사용하여 빠르게 시작하세요. 

- **배포 하 고 직접 만듭니다.** 고유한 Kubernetes 클러스터에 만들려면 다음 단계를 따르세요. 
   1. [클라이언트 도구 설치](install-client-tools.md)
   2. [Azure Arc 지원 데이터 서비스 배포 계획](plan-azure-arc-data-services.md)
   3. [Azure Arc에 Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 만들기](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Azure Arc](https://aka.ms/azurearc)
   - Azure Arc 사용 가능 [데이터 서비스](https://azure.microsoft.com/services/azure-arc/hybrid-data-services) [](overview.md)
   - [연결 모드 및 요구 사항](connectivity.md)



- **Azure Database for PostgreSQL hyperscale의 개념 및 방법 가이드를 읽고 여러 PostgreSQL hyperscale 노드에 데이터를 분산 하 고 성능을 향상** 시킬 수 있습니다.
    * [노드 및 테이블](../../postgresql/concepts-hyperscale-nodes.md)
    * [애플리케이션 유형 확인](../../postgresql/concepts-hyperscale-app-type.md)
    * [배포 열 선택](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [테이블 공동 배치](../../postgresql/concepts-hyperscale-colocation.md)
    * [테이블 분산 및 수정](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [다중 테넌트 데이터베이스 설계](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [실시간 분석 대시보드 설계](../../postgresql/tutorial-design-database-hyperscale-realtime.md)* 

