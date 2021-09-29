---
title: Advisor로 Azure 앱의 성능 향상
description: Azure Advisor의 성능 권장 사항을 사용하여 중요 비즈니스용 애플리케이션의 속도 및 응답성을 향상시키는 데 도움이 됩니다.
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 7229f4a786fc0afc43793ffe730470427ca2b107
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234929"
---
# <a name="improve-the-performance-of-azure-applications-by-using-azure-advisor"></a>Azure Advisor를 사용하여 Azure 애플리케이션의 성능 향상

Azure Advisor 성능 권장 사항은 중요 비즈니스용 애플리케이션의 속도 및 응답성을 향상시키는 데 도움이 될 수 있습니다. Advisor 대시보드의 **성능** 탭에서 Advisor를 사용하여 성능 권장 사항을 볼 수 있습니다.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>정상 엔드포인트로 더 빠르게 장애 조치(failover)할 수 있게 Traffic Manager 프로필의 DNS TTL(Time To Live) 단축

Azure Traffic Manager 프로필에 대한 [TTL(time-to-live) 설정](../traffic-manager/traffic-manager-performance-considerations.md)을 사용하여 지정된 엔드포인트가 쿼리에 응답을 멈춘 경우 엔드포인트를 전환하는 속도를 지정할 수 있습니다. TTL 값이 줄면 클라이언트가 작동하는 엔드포인트로 더 빠르게 라우팅됩니다.

Azure Advisor는 더 긴 TTL이 구성된 Traffic Manager 프로필을 식별합니다. [빠른 장애 조치](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/)를 위해 프로필이 구성되었는지 여부에 따라 20초 또는 60초로 TTL을 구성할 것이 권장됩니다.

## <a name="improve-database-performance-by-using-sql-database-advisor-temporarily-disabled"></a>SQL Database Advisor를 사용하여 데이터베이스 성능 향상(일시적으로 사용 안 함)

Azure Advisor는 모든 Azure 리소스에 대한 권장 사항을 일관되고 통합된 보기로 표시합니다. SQL Database Advisor와 통합하여 데이터의 성능을 향상시키기 위한 권장 사항이 표시됩니다. SQL Database Advisor는 사용 기록을 분석하여 데이터베이스의 성능을 평가합니다. 그런 후 데이터베이스의 일반적인 워크로드를 실행하는 데 가장 적합한 권장 사항을 제공합니다.

> [!NOTE]
> 권장 사항을 얻기 전에 데이터베이스를 일주일 동안 사용해야 하며, 해당 주 내에는 일정한 작업을 수행해야 합니다. SQL Database Advisor는 일관성 있는 쿼리 패턴을 임의 활동 버스트보다 더욱 쉽게 최적화할 수 있습니다.

자세한 내용은 [SQL Database Advisor](../azure-sql/database/database-advisor-implement-performance-recommendations.md)를 참조하세요.

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>안정성 및 성능 향상을 위해 Storage 클라이언트 라이브러리를 최신 버전으로 업그레이드

최신 버전의 Storage 클라이언트 라이브러리 SDK에는 고객이 보고하고 QA 프로세스를 통해 사전에 식별된 문제에 대한 수정 사항이 포함되어 있습니다. 또한 최신 버전은 Azure Storage를 사용하여 전반적인 환경을 개선할 수 있는 새로운 기능과 더불어 안정성 및 성능 최적화를 제공합니다. Advisor는 부실 버전의 SDK를 사용하는 경우 최신 버전의 SDK로 업그레이드하는 데 필요한 권장 사항 및 단계를 제공합니다. 권장 사항은 C++ 및 .NET의 지원되는 언어에 대한 것입니다.

## <a name="improve-app-service-performance-and-reliability"></a>App Service 성능 및 안정성 향상

Azure Advisor는 App Services 환경을 개선하고 관련 플랫폼 기능을 검색하기 위한 권장 사항을 통합합니다. App Service 권장 사항의 예는 다음과 같습니다.
* 완화 옵션을 사용하여 메모리나 CPU 리소스가 앱 런타임에서 소모되는 인스턴스 검색
* 웹앱 및 데이터베이스와 같은 리소스를 함께 배치할 때 성능을 향상시키고 비용을 줄일 수 있는 인스턴스 검색

자세한 내용은 [Azure App Service에 대한 모범 사례](../app-service/app-service-best-practices.md)를 참조하세요.

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>관리 디스크를 사용하여 디스크 I/O 제한 방지

Advisor는 확장성 대상에 도달하는 스토리지 계정에 속한 가상 머신을 식별합니다. 이 조건으로 해당 VM이 I/O 제한에 걸리기 쉽습니다. Advisor는 성능 저하를 방지하기 위해 관리 디스크를 사용하도록 추천합니다.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Premium Storage를 사용하여 가상 머신 디스크의 성능 및 안정성 향상

Advisor는 스토리지 계정에 많은 양의 트랜잭션이 있는 표준 디스크를 갖춘 가상 머신을 식별합니다. 프리미엄 디스크로 업그레이드할 것을 권장합니다. 

Azure Premium Storage는 I/O 사용량이 많은 작업을 실행하는 가상 머신에서 대기 시간이 짧은 고성능 디스크 지원을 제공합니다. Premium Storage 계정을 사용하는 가상 머신 디스크는 SSD에 데이터를 저장합니다. 애플리케이션에 대한 최상의 성능을 위해 높은 IOPS가 필요한 모든 가상 머신 디스크를 Premium Storage 마이그레이션하는 것이 좋습니다.

## <a name="remove-data-skew-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Azure Synapse Analytics 테이블에서 데이터 기울이기를 제거하여 쿼리 성능 향상

데이터 기울이기는 워크로드를 실행하는 경우 불필요한 데이터 이동이나 리소스 병목 현상을 일으킬 수 있습니다. Advisor가 15% 이상의 배포 데이터 기울이기를 검색합니다. 데이터를 재배포하고 테이블 배포 키 선택 영역을 다시 방문하는 것이 좋습니다. 기울이기 식별 및 제거에 대한 자세한 정보는 [기울이기 문제 해결](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)을 참조합니다.

## <a name="create-or-update-outdated-table-statistics-in-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Azure Synapse Analytics 테이블에서 오래된 테이블 통계를 만들거나 업데이트하여 쿼리 성능을 향상시킵니다.

Advisor는 최신 [테이블 통계](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-statistics.md)가 없는 테이블을 식별하고 통계를 만들거나 업데이트할 것을 권장합니다. Azure Synapse Analytics의 쿼리 최적화 프로그램은 최신 통계를 사용하여 쿼리 결과의 카디널리티 또는 행 수를 계산합니다. 해당 예상치를 통해 쿼리 최적화 프로그램은 가장 빠른 성능을 제공하는 쿼리 계획을 만들 수 있습니다.

## <a name="improve-mysql-connection-management"></a>MySQL 연결 관리 개선

Advisor 분석은 MySQL 서버에 연결하는 애플리케이션이 연결을 효율적으로 관리하지 않을 수 있음을 나타낼 수 있습니다. 이로 인해 불필요한 리소스 소비 및 전반적으로 더 긴 애플리케이션 대기 시간이 발생할 수 있습니다. 연결 관리를 향상시키려면 수명이 짧은 연결 수를 줄이고 불필요한 유휴 연결을 제거하는 것이 좋습니다. ProxySQL과 같은 서버 쪽 연결 풀러를 구성하여 개선을 할 수 있습니다.


## <a name="scale-up-to-optimize-cache-utilization-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Azure Synapse Analytics 테이블에서 캐시 사용률을 최적화하도록 스케일 업하여 쿼리 성능 향상

Azure Advisor는 Azure Synapse Analytics 테이블에 높은 수준의 캐시가 사용되는 비율과 낮은 적중률이 있는지 검색합니다. 이 상태는 Azure Synapse Analytics 인스턴스의 성능에 영향을 줄 수 있는 높은 캐시 제거를 나타냅니다. Advisor는 Azure Synapse Analytics 인스턴스를 스케일 업하여 워크로드에 충분한 캐시 용량을 할당할 것을 권장합니다.

## <a name="convert-azure-synapse-analytics-tables-to-replicated-tables-to-increase-query-performance"></a>Azure Synapse Analytics 테이블을 복제된 테이블로 변환하여 쿼리 성능 향상

Advisor는 복제된 테이블이 아닌 변환을 통해 이점을 누릴 수 있는 테이블을 식별합니다. 해당 테이블을 변환하는 것을 제안합니다. 권장 사항은 다음을 기준으로 합니다.
- 복제된 테이블의 크기입니다. 
- 열의 수입니다. 
- 테이블 배포 유형입니다. 
- Azure Synapse Analytics 테이블의 파티션 수입니다. 

컨텍스트에 대한 권장 사항에 추가적인 추론이 제공될 수 있습니다. 이 권장 사항을 결정하는 방법에 대한 자세한 정보는 [Azure Synapse Analytics 권장 사항](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-concept-recommendations.md#replicate-tables)을 참조하세요. 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-the-latest-azure-features"></a>스토리지 계정을 Azure Resource Manager로 마이그레이션하여 최신 Azure 기능 이용하기

스토리지 계정 배포 모델을 Azure Resource Manager로 마이그레이션하여 다음과 같은 이점을 활용할 수 있습니다.
- 템플릿 배포입니다.
- 추가 보안 옵션입니다.
- 최신 Azure Storage 기능을 사용할 수 있도록 GPv2 계정으로 업그레이드할 수 있습니다. 

Advisor는 클래식 배포 모델을 사용 중인 독립형 스토리지 계정을 식별하고 Resource Manager 배포 모델로 마이그레이션할 것을 권장합니다.

> [!NOTE]
> Azure Monitor의 클래식 경고는 2019년 8월에 사용이 중지되었습니다. 새 플랫폼에서 경고 기능을 유지하려면 Resource Manager를 사용하도록 클래식 스토리지 계정을 업그레이드하는 것이 좋습니다. 자세한 내용은 [클래식 경고 사용 중지](../azure-monitor/alerts/monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)를 참조하세요.

## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>최대 구독 한도에 도달하지 않도록 스토리지 계정을 설계합니다.

Azure 지역 하나는 구독당 최대 250개의 스토리지 계정을 지원합니다. 이 한도에 도달하면 해당 영역/구독 조합에서 스토리지 계정을 만들 수 없습니다. Advisor는 구독을 확인하고 최대 한도에 도달하는 데 가까운 구독/영역에 대한 스토리지 계정 수를 줄일 수 있는 권장 사항을 제공합니다.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-p2s-use"></a>높은 P2S 사용을 해결하기 위해 VPN 게이트웨이 SKU 크기를 늘리는 것이 좋습니다.

각 Azure VPN Gateway SKU는 지정된 수의 동시 P2S 연결만 지원할 수 있습니다. 연결 수가 게이트웨이 제한에 가깝기 때문에 추가 연결 시도가 실패할 수 있습니다. 게이트웨이의 크기를 늘리면 더 많은 동시 P2S 사용자를 지원할 수 있습니다. Advisor는 게이트웨이의 크기를 늘리기 위한 권장 사항 및 지침을 제공합니다.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-cpu"></a>높은 CPU 사용을 해결하기 위해 VPN 게이트웨이 SKU 크기를 늘리는 것이 좋습니다.

트래픽 부하가 높으면 VPN gateway는 높은 CPU로 인해 패킷을 삭제할 수 있습니다. VPN Gateway SKU를 업그레이드하는 것이 좋습니다. VPN Gateway의 크기를 늘리면 높은 CPU 사용으로 인해 연결이 끊어지지 않습니다. Advisor는 이 문제를 사전에 해결하기 위한 권장 사항을 제공합니다. 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>로드 처리량, 데이터 압축 및 쿼리 성능을 최대화하기 위해 로드할 때 일괄 처리 크기를 늘립니다.

Advisor는 데이터베이스에 로드할 때 일괄 처리 크기를 늘려 로드 성능 및 처리량을 늘릴 수 있는지 검색합니다. COPY 문을 사용하는 것이 좋습니다. COPY 문을 사용할 수 없는 경우 SQLBulkCopy API 또는 BCP와 같은 로딩 유틸리티를 사용할 때 일괄 처리 크기를 늘려야 합니다. 일반적으로 10만에서 100만 행 사이의 일괄 처리 크기를 사용하는 것이 좋습니다. 일괄 처리 크기를 늘리면 로드 처리량, 데이터 압축, 쿼리 성능이 향상됩니다.

## <a name="co-locate-the-storage-account-in-the-same-region-to-minimize-latency-when-loading"></a>로드할 때 대기 시간을 최소화하기 위해 스토리지 계정을 동일한 지역 내에 공동 배치

Advisor는 전용 SQL 풀과 다른 지역에서 로드하고 있는지 검색합니다. 데이터를 로드할 때 대기 시간을 최소화하려면 전용 SQL 풀과 동일한 지역에 있는 스토리지 계정에서 로드하는 것이 좋습니다. 이렇게 변경하면 대기 시간을 최소화하고 로드 성능을 향상시킬 수 있습니다.

## <a name="use-a-supported-kubernetes-version"></a>지원되는 Kubernetes 버전 사용

Advisor가 지원되지 않는 버전의 Kubernetes를 검색합니다.

## <a name="optimize-the-performance-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers"></a>Azure Database for MySQL, Azure Database for PostgreSQL 및 Azure Database for MariaDB 서버의 성능 최적화

### <a name="fix-the-cpu-pressure-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-with-cpu-bottlenecks"></a>CPU 병목 상태를 사용하여 Azure Database for MySQL, Azure Database for PostgreSQL 및 Azure Database for MariaDB 서버의 CPU 압력을 수정합니다.
장기간 CPU 사용률이 높으면 워크로드의 쿼리 성능이 저하될 수 있습니다. CPU 크기를 높이면 데이터베이스 쿼리의 런타임 최적화 및 전반적인 성능 향상에 도움이 됩니다. Advisor는 CPU 제한 워크로드를 실행할 가능성이 높은 CPU 사용률이 높은 서버를 식별하고 컴퓨팅을 스케일링하는 것을 권장합니다.

### <a name="reduce-memory-constraints-on-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Azure Database for MySQL, Azure Database for PostgreSQL 및 Azure Database for MariaDB 서버에서 메모리 제약 조건을 줄이거나 메모리 최적화 SKU로 이동합니다.
낮은 캐시 적중률로 인해 쿼리 성능이 느려지고 IOPS가 증가할 수 있습니다. 이 문제는 잘못된 쿼리 계획 또는 메모리 집약적 워크로드로 인해 발생할 수 있습니다. 쿼리 계획을 수정하거나 Azure Database for PostgreSQL, Azure Database for MySQL 또는 Azure Database for MariaDB 서버의 [메모리를 늘리면](../postgresql/concepts-pricing-tiers.md) 데이터베이스 워크로드의 실행을 최적화하는 데 도움이 됩니다. Azure Advisor는 이 높은 버퍼 풀 변동의 영향을 받는 서버를 식별합니다. 다음 작업 중 하나를 수행하는 것이 좋습니다. 
- 쿼리 계획 수정
- 더 많은 메모리가 있는 SKU로 이동 
- 더 많은 IOPS를 얻기 위해 스토리지 크기를 늘립니다.

### <a name="use-an-azure-database-for-mysql-or-azure-database-for-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>읽기 집약적 워크로드에 대한 읽기를 스케일 아웃하려면 Azure Database for MySQL 또는 Azure Database for PostgreSQL 읽기 복제본을 사용합니다.
Advisor는 지난 7일간 서버에서 쓰기에 대한 읽기 비율 같은 워크로드 기반 추론을 사용하여 읽기 집약적인 워크로드를 식별합니다. 읽기/쓰기 비율이 높은 Azure Database for PostgreSQL 또는 Azure Database for MySQL 리소스는 CPU 또는 메모리 경합이 발생할 수 있으며 쿼리 성능이 느려질 수 있습니다. [복제본](../postgresql/howto-read-replicas-portal.md)을 추가하면 복제본 서버에 대한 읽기를 스케일 아웃하고 주 서버에서 CPU 또는 메모리 제약 조건을 방지할 수 있습니다. Advisor는 읽기 집약적인 워크로드를 사용하는 서버를 식별하고 [읽기 복제본](../postgresql/concepts-read-replicas.md)을 추가하여 읽기 워크로드의 일부를 오프로드하는 것을 권장합니다.


### <a name="scale-your-azure-database-for-mysql-azure-database-for-postgresql-or-azure-database-for-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>연결 제약 조건을 방지하기 위해 Azure Database for MySQL, Azure Database for PostgreSQL 또는 Azure Database for MariaDB 서버를 상위 SKU로 스케일링
데이터베이스 서버에 대한 새 연결은 메모리를 차지합니다. 서버에 대한 연결이 메모리의 [상한](../postgresql/concepts-limits.md)으로 인해 실패하는 경우 데이터베이스 서버의 성능이 저하됩니다. Azure Advisor는 여러 연결 오류가 발생하며 실행되는 서버를 식별합니다. 다음 작업 중 하나를 수행하여 서버에 더 많은 메모리를 제공하도록 서버 연결 제한을 업그레이드하는 것이 좋습니다.
- 컴퓨팅 스케일 업 
- 코어당 컴퓨팅을 많이 사용하는 메모리 최적화 SKU를 사용합니다.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>캐시와 애플리케이션 성능을 개선하기 위해 다른 크기나 SKU로 캐시 스케일링

캐시 인스턴스는 높은 메모리 압력, 높은 서버 부하 또는 높은 네트워크 대역폭에서 실행되지 않는 경우에 가장 잘 작동합니다. 해당 조건으로 인해 응답하지 않거나 데이터 손실이 발생하거나 사용할 수 없게 될 수 있습니다. Advisor는 해당 조건에서 캐시 인스턴스를 식별합니다. 다음 작업 중 하나를 수행하는 것이 좋습니다.
- 모범 사례를 적용하여 메모리 압력, 서버 부하 또는 네트워크 대역폭을 줄입니다.
- 용량이 더 많은 다른 크기나 SKU로 스케일링합니다.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>트래픽이 있는 지역을 Azure Cosmos DB 계정에 추가

Advisor는 현재 구성되지 않은 지역에서 트래픽을 포함하는 Azure Cosmos DB 계정을 검색합니다. 해당 지역을 추가하는 것이 좋습니다. 이렇게 하면 해당 지역에서 들어오는 요청에 대한 대기 시간이 향상되고 지역 중단이 발생한 경우 가용성이 보장됩니다. [Azure Cosmos DB를 사용한 글로벌 데이터 배포에 대한 자세한 정보를 알아보세요.](../cosmos-db/distribute-data-globally.md)

## <a name="configure-your-azure-cosmos-db-indexing-policy-by-using-custom-included-or-excluded-paths"></a>사용자 지정 포함 또는 제외 경로를 사용하여 Azure Cosmos DB 인덱싱 정책 구성

Advisor는 기본 인덱싱 정책을 사용하는 Azure Cosmos DB 컨테이너를 식별하지만 사용자 지정 인덱싱 정책의 이점을 누릴 수 있습니다. 해당 결정은 워크로드 패턴을 기반으로 합니다. 기본 인덱싱 정책은 모든 속성을 인덱싱합니다. 인덱싱에 사용되는 명시적으로 포함 또는 제외된 경로를 쿼리 필터에 사용하는 사용자 지정 인덱싱 정책은 RU 및 스토리지 사용량을 줄일 수 있습니다. [인덱스 정책 수정에 대한 자세한 정보를 알아보세요.](../cosmos-db/index-policy.md)

## <a name="set-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Azure Cosmos DB 쿼리 페이지 크기(MaxItemCount)를 -1로 설정 

Azure Advisor는 쿼리 페이지 크기 100을 사용하는 Azure Cosmos DB 컨테이너를 식별합니다. 빠른 검사의 경우에는 페이지 크기를 -1로 사용하는 것이 좋습니다. [MaxItemCount에 대한 자세한 정보를 알아봅니다.](../cosmos-db/sql-api-query-metrics.md)

## <a name="consider-using-accelerated-writes-feature-in-your-hbase-cluster-to-improve-cluster-performance"></a>클러스터 성능을 향상시키려면 HBase 클러스터에서 가속화된 쓰기 기능을 사용하는 것이 좋습니다.
Azure Advisor는 지난 7일간의 시스템 로그를 분석하고 클러스터에 다음 시나리오가 발생했는지 확인합니다.
1. 긴 WAL 동기화 대기 시간 
2. 쓰기 요청 수가 많은 경우 (최소 3 1 시간 이상의 windows 1000 avg_write_requests/second/node)

이러한 상태는 클러스터의 쓰기 대기 시간이 길다는 것을 나타냅니다. 이는 클러스터에서 많은 워크로드를 수행했기 때문일 수 있습니다. 클러스터의 성능을 향상시키기 위해 Azure HDInsight HBase에서 제공하는 가속화된 쓰기 기능을 활용하는 것을 고려할 수 있습니다. HDInsight Apache HBase 클러스터의 가속 쓰기 기능은 클라우드 스토리지를 사용하는 대신 모든 RegionServer(작업자 노드)에 프리미엄 SSD 관리 디스크를 연결합니다. 따라서 애플리케이션의 쓰기 대기 시간이 단축되고 복원력이 향상됩니다. 이 기능에 대한 자세한 내용을 보려면 [자세한 정보를 알아보세요](../hdinsight/hbase/apache-hbase-accelerated-writes.md#how-to-enable-accelerated-writes-for-hbase-in-hdinsight)

## <a name="review-azure-data-explorer-table-cache-period-policy-for-better-performance-preview"></a>성능 향상을 위해 Azure Data Explorer 테이블 캐시 기간(정책) 검토(미리 보기)
이 권장 사항은 구성된 캐시 기간(정책)을 초과하여 가장 많은 수의 쿼리를 포함하는 Azure Data Explorer 테이블을 표시합니다(캐시 외부 데이터에 액세스하는 쿼리 비율별로 상위 10개의 테이블이 표시됨). 클러스터의 성능을 향상시키기 위한 권장 조치: 이 테이블에 대한 쿼리를 필요한 최소 시간 범위(정의된 정책 내)로 제한합니다. 또는 전체 시간 범위의 데이터가 필요한 경우 캐시 기간을 권장 값으로 늘립니다.

## <a name="improve-performance-by-optimizing-mysql-temporary-table-sizing"></a>MySQL 임시 테이블 크기 조정을 최적화하여 성능 향상
Advisor 분석에 따르면 임시 테이블 매개 변수 설정이 낮기 때문에 MySQL 서버에 불필요한 I/O 오버헤드가 발생할 수 있습니다. 이로 인해 불필요한 디스크 기반 트랜잭션이 발생하고 성능이 저하될 수 있습니다. 디스크 기반 트랜잭션 수를 줄이려면 'tmp_table_size' 및 'max_heap_table_size' 매개 변수 값을 늘리는 것이 좋습니다. [자세히 알아보기](https://aka.ms/azure_mysql_tmp_table)

## <a name="distribute-data-in-server-group-to-distribute-workload-among-nodes"></a>서버 그룹에 데이터를 배포하여 노드 간에 워크로드 분산
Advisor는 데이터가 배포되지 않았지만 코디네이터에 유지되는 서버 그룹을 식별합니다. 이를 기반으로 하여 Advisor는 전체 하이퍼스케일(Citus) 혜택을 통해 서버 그룹의 작업자 노드에 데이터를 배포할 것을 권장합니다. 이는 서버 그룹에서 각 노드의 리소스를 활용하여 쿼리 성능을 향상시킵니다. [자세히 알아보기](https://go.microsoft.com/fwlink/?linkid=2135201) 

## <a name="improve-user-experience-and-connectivity-by-deploying-vms-closer-to-windows-virtual-desktop-deployment-location"></a>Windows Virtual Desktop 배포 위치에 더 가깝게 VM을 배포하여 사용자 환경 및 연결을 개선
VM이 WVD(Windows Virtual Desktop)를 사용하여 사용자가 연결하는 위치와 다른 지역에 있는 것으로 확인되었습니다. 이로 인해 연결 응답 시간이 길어질 수 있으며 WVD에서 전반적인 사용자 환경에 영향을 줄 수 있습니다. 호스트 풀에 대해 VM을 만들 때 사용자에게 가까운 지역을 사용하려고 해야 합니다. 가까운 근접성을 사용하면 WVD 서비스에 대해 계속 만족할 수 있으며 전반적인 환경의 품질이 향상됩니다. [여기에서 연결 대기 시간에 대한 자세한 정보를 알아보세요](../virtual-desktop/connection-latency.md).

## <a name="upgrade-to-the-latest-version-of-the-immersive-reader-sdk"></a>최신 버전의 몰입형 리더 SDK로 업그레이드
이 구독에서 오래된 버전의 몰입형 리더 SDK를 사용하는 리소스를 확인했습니다. 최신 버전의 몰입형 리더 SDK를 사용하면 통합 환경을 사용자 지정하고 향상시킬 수 있는 확장된 기능 집합, 업데이트된 보안, 성능이 제공됩니다.
[몰입형 판독기 SDK](../applied-ai-services/immersive-reader/index.yml)에 대한 자세한 정보를 알아보세요.

## <a name="improve-vm-performance-by-changing-the-maximum-session-limit"></a>최대 세션 제한을 변경하여 VM 성능 개선

Advisor는 처음에 부하 분산 알고리즘으로 설정된 호스트 풀이 있음을 감지하고 해당 호스트 풀의 최대 세션 제한은 999999보다 크거나 같습니다. 깊이를 우선한 부하 분산은 최대 세션 제한을 사용하여 단일 세션 호스트에서 동시 세션을 가질 수 있는 최대 사용자 수를 결정합니다. 최대 세션 제한이 너무 높으면 모든 사용자 세션이 동일한 세션 호스트로 지정되어 성능 및 안정성 문제가 발생할 수 있습니다. 따라서 호스트 풀에서 깊이를 우선하여 부하 분산을 설정하는 경우 VM의 배포 및 용량 구성에 따라 적절한 최대 세션 제한도 설정해야 합니다. 

Windows Virtual Desktop의 부하 분산에 대한 자세한 정보를 알아보려면 [Windows Virtual Desktop 부하 분산 방법 구성](../virtual-desktop/troubleshoot-set-up-overview.md)을 참조하세요.

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Advisor에서 성능 권장 사항에 액세스하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

2.  Advisor 대시보드에서 **성능** 탭을 선택합니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.

* [Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 점수](azure-advisor-score.md)
* [Advisor 비용 권장 사항](advisor-cost-recommendations.md)
* [Advisor 안정성 권장 사항](advisor-high-availability-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [Advisor 운영 우수성 권장 사항](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
