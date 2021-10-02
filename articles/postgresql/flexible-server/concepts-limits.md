---
title: 제한 - Azure Database for PostgreSQL - 유연한 서버
description: 이 문서에서는 연결 수 및 스토리지 엔진 옵션과 같은 Azure Database for PostgreSQL - 유연한 서버의 제한을 설명합니다.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/17/2021
ms.openlocfilehash: 1966ce24919e2d98658afe2cec09d37e3b567c60
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129387490"
---
# <a name="limits-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - 유연한 서버의 제한



다음 섹션에서는 데이터베이스 서비스의 용량 및 기능 제한에 대해 설명합니다. 리소스(컴퓨팅, 메모리, 스토리지) 계층에 대해 알아보려면 [컴퓨팅 및 스토리지](concepts-compute-storage.md) 문서를 참조하세요.

## <a name="maximum-connections"></a>최대 연결 수

가격 책정 계층 및 vCore당 최대 연결 수는 아래와 같습니다. Azure 시스템에서 Azure Database for PostgreSQL - 유연한 서버를 모니터링하려면 3개의 연결이 필요합니다.

| SKU 이름             | vCore 수 | 메모리 크기 | 최대 연결 수 | 최대 사용자 연결 |
|----------------------|--------|-------------|-----------------|----------------------|
| **버스트 가능**        |        |             |                 |                      |
| B1ms                 | 1      | 2GiB       | 50              | 47                   |
| B2s                  | 2      | 4GiB       | 100             | 97                   |
| **범용**  |        |             |                 |                      |
| D2s_v3               | 2      | 8GiB       | 859             | 856                  |
| D4s_v3               | 4      | 16GiB      | 1719            | 1716                 |
| D8s_v3               | 8      | 32GiB      | 3438            | 3435                 |
| D16s_v3              | 16     | 64GiB      | 5,000            | 4997                 |
| D32s_v3              | 32     | 128GiB     | 5,000            | 4997                 |
| D48s_v3              | 48     | 192GiB     | 5,000            | 4997                 |
| D64s_v3              | 64     | 256GiB     | 5,000            | 4997                 |
| **메모리 최적화** |        |             |                 |                      |
| E2s_v3               | 2      | 16GiB      | 1719            | 1716                 |
| E4s_v3               | 4      | 32GiB      | 3438            | 3433                 |
| E8s_v3               | 8      | 64GiB      | 5,000            | 4997                 |
| E16s_v3              | 16     | 128GiB     | 5,000            | 4997                 |
| E32s_v3              | 32     | 256GiB     | 5,000            | 4997                 |
| E48s_v3              | 48     | 384GiB     | 5,000            | 4997                 |
| E64s_v3              | 64     | 432GiB     | 5,000            | 4997                 |

연결 한도를 초과하면 다음과 같은 오류가 발생할 수 있습니다.
> 오류: 죄송합니다. 이미 클라이언트가 너무 많습니다.

> [!IMPORTANT]
> 최상의 환경을 위해 PgBouncer와 같은 연결 풀러를 사용하여 연결을 효율적으로 관리하는 것이 좋습니다.

유휴 상태일지라도 PostgreSQL 연결은 약 10MB의 메모리를 차지할 수 있습니다. 또한 새 연결을 만드는 데는 시간이 걸립니다. 대부분의 애플리케이션은 많은 단기 연결을 요청합니다. 이는 이러한 상황을 복잡하게 만듭니다. 결과적으로 실제 워크로드에 사용할 수 있는 리소스가 줄어들어 성능이 저하됩니다. 연결 풀링은 유휴 연결을 줄이고 기존 연결을 다시 사용하는 데 사용할 수 있습니다. 자세한 내용은 [블로그 게시물](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)을 참조하세요.

## <a name="functional-limitations"></a>기능 제한 사항

### <a name="scale-operations"></a>크기 조정 작업

- 서버 스토리지의 크기를 조정하려면 서버를 다시 시작해야 합니다.
- 서버 스토리지는 2배만 증가하여 확장할 수 있습니다. 자세한 내용은 [컴퓨팅 및 스토리지](concepts-compute-storage.md)를 참조하세요.
- 서버 스토리지 크기를 줄이는 것은 현재 지원되지 않습니다.

### <a name="server-version-upgrades"></a>서버 버전 업그레이드

- 주 데이터베이스 엔진 버전 간에 자동화된 마이그레이션은 현재 지원되지 않습니다. 다음의 주 버전으로 업그레이드하려는 경우 새 엔진 버전을 사용하여 만든 서버에 주 버전을 [덤프 및 복원](../howto-migrate-using-dump-and-restore.md)합니다.

### <a name="storage"></a>스토리지

- 구성된 후에는 스토리지 크기를 줄일 수 없습니다. 원하는 스토리지 크기로 새 서버를 만들고, 수동 [덤프 및 복원](../howto-migrate-using-dump-and-restore.md)을 수행하고, 새 서버로 데이터베이스를 마이그레이션해야 합니다.
- 현재 스토리지 자동 증가 기능을 사용할 수 없습니다. 사용량을 모니터링하고 스토리지를 더 큰 크기로 늘리세요. 
- 스토리지 사용량이 95%에 도달하거나 사용 가능한 용량이 5GiB 미만인 경우 디스크가 가득 찬 상황과 관련된 오류를 방지하기 위해 서버가 자동으로 **읽기 전용 모드** 로 전환됩니다. 
- 특정 임계값을 초과할 때 `storage used` 또는 `storage percent`에 대한 경고 규칙을 설정하여 스토리지 크기 증가와 같은 조치를 사전에 취할 수 있도록 하는 것이 좋습니다. 예를 들어, 스토리지 백분율이 80% 사용량을 초과하는 경우 경고를 설정할 수 있습니다.
  
### <a name="networking"></a>네트워킹

- 현재 VNET 안팎으로 이동하는 것은 지원되지 않습니다.
- VNET 내에서 배포와 공용 액세스를 결합하는 것은 현재 지원되지 않습니다.
- 방화벽 규칙은 VNET에서 지원되지 않으나 대신 네트워크 보안 그룹을 사용할 수 있습니다.
- 공용 액세스 데이터베이스 서버는 예를 들어 `postgres_fdw`를 통해 공용 인터넷에 연결할 수 있으며 이 액세스는 제한할 수 없습니다. VNET 기반 서버는 네트워크 보안 그룹을 사용하여 아웃바운드 액세스를 제한할 수 있습니다.

### <a name="high-availability-ha"></a>HA(고가용성)

- 영역 중복 HA는 현재 버스트 가능 서버에 대해 지원되지 않습니다.
- 서버가 HA 대기로 장애 조치(failover)할 때 데이터베이스 서버 IP 주소가 변경됩니다. 서버 IP 주소 대신 DNS 레코드를 사용해야 합니다.
- HA로 구성된 유연한 서버로 논리적 복제를 구성한 경우 대기 서버로 장애 조치(failover) 시 논리적 복제 슬롯이 대기 서버로 복사되지 않습니다. 
- 제한 사항을 포함한 영역 중복 HA에 대한 자세한 내용은 [개념 - HA 설명서](concepts-high-availability.md) 페이지를 참조하세요.

### <a name="availability-zones"></a>가용성 영역

- 서버를 다른 가용성 영역으로 수동으로 이동하는 것은 현재 지원되지 않습니다.
- HA 대기 서버의 가용성 영역은 수동으로 구성할 수 없습니다.

### <a name="postgres-engine-extensions-and-pgbouncer"></a>Postgres 엔진, 확장 및 PgBouncer

- Postgres 10 이하 버전은 지원되지 않습니다. 이전 Postgres 버전이 필요한 경우 [단일 서버](../overview-single-server.md) 옵션을 사용하는 것이 좋습니다.
- 확장 지원은 현재 Postgres `contrib` 확장으로 제한됩니다.
- 기본 제공 PgBouncer 연결 풀러는 현재 버스트 가능 서버에서 사용할 수 없습니다.
- SCRAM 인증은 기본 제공 PgBouncer를 사용 하는 연결에서 지원 되지 않습니다.

### <a name="stopstart-operation"></a>작업 중지/시작

- 7일 이상 서버를 중지할 수 없습니다.

### <a name="scheduled-maintenance"></a>예약된 유지 관리

- 이미 계획된 업그레이드 전 5일 이내에는 유지 관리 기간을 변경해도 해당 업그레이드에는 영향을 미치지 않습니다. 변경 사항은 다음 예약된 유지 관리에만 적용됩니다.

### <a name="backing-up-a-server"></a>서버 백업

- 백업은 시스템에서 관리되므로 현재 이러한 백업을 수동으로 실행할 수 있는 방법이 없습니다. 대신 `pg_dump`를 사용하는 것이 좋습니다.
- 백업은 항상 스냅샷 기반 전체 백업(차등 백업 아님)이므로 백업 스토리지 활용도를 높일 수 있습니다. 트랜잭션 로그(미리 쓰기 로그 - WAL)는 전체/차등 백업과 별개이며 지속적으로 보관됩니다.

### <a name="restoring-a-server"></a>서버 복원

- 지정 시간 복구 기능을 사용하면 기반이 되는 서버와 동일한 컴퓨팅 및 스토리지 구성으로 새 서버가 생성됩니다.
- VNET 기반 데이터베이스 서버는 백업에서 복원할 때 동일한 VNET으로 복원됩니다.
- 복원 동안 만든 새 서버에는 원래 서버에 존재했던 방화벽 규칙이 없습니다. 방화벽 규칙은 새 서버에 대해 별도로 만들어야 합니다.
- 삭제된 서버 복원은 지원되지 않습니다.
- 교차 지역 복원은 지원되지 않습니다.

### <a name="other-features"></a>기타 기능

* Azure AD 인증은 아직 지원되지 않습니다. Azure AD 인증이 필요한 경우 [단일 서버](../overview-single-server.md) 옵션을 사용하는 것이 좋습니다.
* 읽기 복제본은 아직 지원되지 않습니다. 읽기 복제본이 필요한 경우 [단일 서버](../overview-single-server.md) 옵션을 사용하는 것이 좋습니다.
* 리소스를 다른 구독으로 이동하는 것은 지원되지 않습니다. 


## <a name="next-steps"></a>다음 단계

- [컴퓨팅 및 스토리지 옵션에 사용할 수 있는 항목](concepts-compute-storage.md) 이해
- [지원되는 PostgreSQL 데이터베이스 버전](concepts-supported-versions.md) 알아보기
- [Azure Portal을 사용하여 Azure Database for PostgreSQL에서 서버를 백업 및 복원하는 방법](how-to-restore-server-portal.md) 검토
