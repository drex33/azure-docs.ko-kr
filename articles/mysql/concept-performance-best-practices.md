---
title: 성능 모범 사례 - Azure Database for MySQL
description: 이 문서에서는 Azure Database for MySQL의 성능을 모니터링하고 조정하기 위한 몇 가지 권장 사항을 설명합니다.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/28/2021
ms.openlocfilehash: dc47e06643f3bb4ebf986382e149e13fd9bbc968
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536790"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>Azure Database for MySQL - 단일 서버의 최적의 성능을 위한 모범 사례

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL - 단일 서버를 사용하면서 최상의 성능을 얻는 방법에 대해 알아봅니다. 플랫폼에 새로운 기능을 추가하는 경우 이 섹션에서 권장 사항을 계속 갱신합니다.

## <a name="physical-proximity"></a>물리적 근접성

 응용 프로그램과 데이터베이스를 동일한 지역에 배포해야 합니다. 성능 벤치마킹 실행을 시작하기 전에 빠른 검사로 간단한 SELECT 1 쿼리를 사용하여 클라이언트와 데이터베이스 간의 네트워크 대기 시간을 확인합니다. 

## <a name="accelerated-networking"></a>가속 네트워킹

Azure 가상 머신, Azure Kubernetes, 또는 App Services를 사용하는 경우 응용 프로그램 서버에 가속화된 네트워킹을 사용합니다. 가속화된 네트워킹을 사용하면 VM에 대한 SR-IOV(단일 루트 I/O 가상화)를 구현할 수 있어 네트워킹 성능이 크게 향상됩니다. 이 고성능 경로는 데이터 경로에서 호스트를 우회함으로써 대기 시간, 지터 및 CPU 사용률을 줄이므로 지원되는 VM 유형에서 가장 까다로운 네트워크 워크로드에 사용합니다.

## <a name="connection-efficiency"></a>연결 효율

새 연결 설정 작업은 항상 비용과 시간이 많이 걸립니다. 응용 프로그램은 데이터베이스 연결을 요청할 때 새 유휴 데이터베이스 연결을 만드는 대신 기존 유휴 데이터베이스 연결의 할당을 우선적으로 적용합니다.  다음은 좋은 연결 방법에 대한 몇 가지 옵션입니다.

- **ProxySQL**: [ProxySQL](https://proxysql.com/)을 사용합니다. 기본 제공 연결 풀링이 있으며 응용 프로그램 코드를 변경하여 요청 시 필요에 따라 작업 부하를 여러 읽기 복제본으로 [부하 분산](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) 합니다.

- **Heimdall Data Proxy**: 또는 공급업체 중립적 독점 프록시 솔루션인 Heimdall Data Proxy를 활용할 수도 있습니다. 복제 지연 검색을 사용하여 쿼리 캐싱 및 읽기/쓰기 분할을 지원합니다. [Heimdall 프록시를 사용하여 MySQL 성능을 가속화](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/accelerate-mysql-performance-with-the-heimdall-proxy/ba-p/1063349)하는 방법을 참조할 수도 있습니다.  

- **영구적 또는 장기간 연결**: 응용 프로그램에 짧은 트랜잭션이나 5-10밀리초 미만의 실행 시간을 갖는 쿼리가 포함된 경우, 단기간 연결을 영구적 연결로 바꿉니다. 단기간 연결을 영구 연결로 대체하려면 코드를 약간 변경해야 하지만 많은 일반적인 응용 프로그램 시나리오에서 성능 향상에 큰 영향을 줍니다. 트랜잭션이 완료되면 시간 제한을 설정하거나 연결을 닫아야 합니다.

- **복제본**: 복제본을 사용하는 경우 [ProxySQL](https://proxysql.com/)을 사용하여 주 서버와 읽기 가능한 보조 복제본 서버 간의 부하를 분산합니다. [ProxySQL을 설정하는 방법](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847)을 알아봅니다.

## <a name="data-import-configurations"></a>데이터 가져오기 구성

- 데이터 가져오기 작업을 시작하기 전에 인스턴스를 더 높은 SKU 크기로 임시로 확장한 다음 가져오기가 성공할 경우 규모를 축소할 수 있습니다.
- 온라인 또는 오프라인 마이그레이션에 대해 [Azure Database Migration Service(DMS)](https://datamigration.microsoft.com/)를 사용하여 가동 중지 시간을 최소화한 채 데이터를 가져올 수 있습니다. 

## <a name="azure-database-for-mysql-memory-recommendations"></a>Azure Database for MySQL 메모리 권장 사항

Azure Database for MySQL 성능 모범 사례는 충분한 RAM을 할당하여 작업 집합이 메모리에 거의 완전히 상주할 수 있도록 하는 것입니다. 

- [한도](./concepts-pricing-tiers.md) 에 도달하는 데 사용되는 메모리 백분율이 [MySQL 서버용 메트릭](./concepts-monitoring.md)을 사용하는지 확인합니다. 
- 이러한 숫자에 대한 경고를 설정하여 서버가 한도에 도달하면 문제를 해결하기 위해 프롬프트 작업을 수행할 수 있도록 합니다. 정의된 한도에 따라 데이터베이스 SKU를 더 높은 계산 크기나 더 나은 가격 책정 계층으로 확장하여 성능이 크게 향상되는지 확인합니다. 
- 크기 조정 작업 후 성능 숫자가 더 이상 크게 떨어지지 않을 때까지 확장합니다. DB 인스턴스의 메트릭을 모니터링하는 방법에 대한 자세한 내용은 [MySQL DB 메트릭](./concepts-monitoring.md#metrics)을 참조하세요.
 
## <a name="use-innodb-buffer-pool-warmup"></a>InnoDB 버퍼 풀 사용 준비

Azure Database for MySQL 서버를 다시 시작한 후에는 테이블이 쿼리될 때 스토리지에 있는 데이터 페이지를 로드하여 대기 시간이 늘어나고 쿼리를 처음 실행할 때 성능이 저하됩니다. 대기 시간이 중요한 작업에는 이런 상황이 부적합할 수 있습니다. 

InnoDB 버퍼 풀 준비를 활용하면 DML 또는 SELECT 작업이 해당 행에 액세스할 때까지 기다리는 대신 다시 시작 전 버퍼 풀에 있던 디스크 페이지를 다시 로드하여 준비 기간이 단축됩니다.

[InnoDB 버퍼 풀 서버 매개 변수](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html)를 구성하여 성능 매개 변수를 나타내는 Azure Database for MySQL 서버를 다시 시작한 후 준비 기간을 줄일 수 있습니다. InnoDB는 서버 종료 시 각 버퍼 풀에 대해 최근에 사용된 페이지의 백분율을 저장하고 서버 시작 시 이러한 페이지를 복원합니다.

또한 이러한 성능 향상을 위해 서버의 시작 시간이 길어진다는 것을 이해해야 합니다. 이 매개 변수를 사용하도록 설정한 경우, 서버에 프로비전된 IOPS에 따라 서버 시작 및 다시 시작 시간이 늘어날 수 있습니다. 

이 시간 동안 서버를 사용할 수 없으므로, 다시 시작 시간을 테스트하고 모니터하여 시작/다시 시작 성능이 적절한지 확인하는 것이 좋습니다. 프로비전 된 IOPS가 1000 미만인 경우 이 매개 변수를 사용하지 않는 것이 좋습니다. 즉, 프로비전된 스토리지가 335GB 미만인 경우에는 이 매개 변수를 사용하지 않는 것이 좋습니다.

서버를 종료할 때 버퍼 풀의 상태를 저장하려면 서버 매개 변수 `innodb_buffer_pool_dump_at_shutdown`을 `ON`으로 설정합니다. 이와 비슷하게 서버 시작 시 버퍼 풀 상태를 복원하도록 서버 매개 변수 `innodb_buffer_pool_load_at_startup`을 `ON`으로 설정합니다. 서버 매개 변수 `innodb_buffer_pool_dump_pct`의 값을 낮추고 미세 조정하여 시작/다시 시작 시간에 대한 영향을 제어할 수 있습니다. 이 매개 변수는 기본적으로 `25`로 설정됩니다.

> [!Note]
> InnoDB 버퍼 풀 준비 매개 변수는 최대 16TB 스토리지를 포함하는 범용 스토리지 서버에서만 지원됩니다. [Azure Database for MySQL 스토리지 옵션](./concepts-pricing-tiers.md#storage)에 대해 자세히 알아보기.

## <a name="next-steps"></a>다음 단계

- [Azure Database for MySQL을 사용한 서버 작업에 대한 모범 사례](concept-operation-excellence-best-practices.md) <br/>
- [Azure Database for MySQL 모니터링에 대한 모범 사례](concept-monitoring-best-practices.md)<br/>
- [Azure Database for MySQL 시작](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>
