---
title: PgBouncer - Azure Database for PostgreSQL - 유연한 서버
description: 이 문서에서는 기본 제공 PgBouncer 확장의 개요를 제공합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/30/2021
ms.openlocfilehash: 6ecd7202e2610cd6d85253861b58fb72c513e065
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271273"
---
# <a name="pgbouncer-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - 유연한 서버의 PgBouncer



Azure Database for PostgreSQL - 유연한 서버는 기본 제공 연결 풀링 솔루션으로 [PgBouncer](https://github.com/pgbouncer/pgbouncer)를 제공합니다. 이 서비스는 데이터베이스별 서버 기준으로 사용하도록 설정할 수 있는 선택적 서비스이며 퍼블릭 및 프라이빗 액세스 모두에서 지원됩니다. PgBouncer는 Postgres 데이터베이스 서버와 동일한 가상 머신에서 실행됩니다. Postgres는 프로세스 기반 모델을 연결에 사용하므로 많은 유휴 연결을 유지 관리하는 데 비용이 많이 듭니다. 따라서 서버가 몇천 개 이상의 연결을 실행하면 Postgres 자체에서 리소스 제약 조건이 적용됩니다. PgBouncer의 주요 이점은 데이터베이스 서버에서 유휴 연결 및 수명이 짧은 연결을 개선하는 것입니다.

PgBouncer는 비동기 I/O를 활용하는 좀 더 간단한 모델을 사용하며, 필요한 경우, 즉 열린 트랜잭션 내부 또는 쿼리가 활성 상태일 때만 실제 Postgres 연결을 사용합니다. 이 모델은 오버헤드가 낮은 수천 개의 연결을 더 쉽게 지원할 수 있으며 오버헤드가 낮은 최대 10,000개의 연결로 크기 조정할 수 있습니다.

사용하도록 설정하면 PgBouncer는 데이터베이스 서버의 포트 6432에서 실행됩니다. 동일한 호스트 이름을 사용하도록 애플리케이션의 데이터베이스 연결 구성을 변경할 수 있지만 포트를 6432로 변경하여 PgBouncer 사용을 시작하고 향상된 유휴 연결 크기 조정의 이점을 누릴 수 있습니다.

> [!Note]
> PgBouncer는 공용 액세스 및 프라이빗 액세스 네트워킹 모두에서 범용 및 메모리 최적화 컴퓨팅 계층에서 지원됩니다. 

## <a name="enabling-and-configuring-pgbouncer"></a>PgBouncer 사용 및 구성

PgBouncer를 사용하도록 설정하려면 Azure Portal에서 “서버 매개 변수” 블레이드로 이동하여 “PgBouncer”를 검색하고 PgBouncer를 사용하도록 설정하기 위해 pgbouncer.enabled 설정을 “true”로 변경하면 됩니다. 서버를 다시 시작할 필요가 없습니다. 그러나 다른 PgBouncer 매개 변수를 설정하려면 제한 사항 섹션을 참조하세요.

다음 매개 변수를 설정하여 PgBouncer를 구성할 수 있습니다.

| 매개 변수 이름             | 설명 | 기본값 | 
|----------------------|--------|-------------|
| pgbouncer.default_pool_size | 이 매개 변수 값을 사용자/데이터베이스 쌍당 연결 수로 설정합니다.      | 50       | 
| pgBouncer.max_client_conn | 이 매개 변수 값을 지원하려는 PgBouncer에 대한 최대 클라이언트 연결 수로 설정합니다.     | 5,000     | 
| pgBouncer.pool_mode | 이 매개 변수 값을 트랜잭션 풀링을 위한 TRANSACTION으로 설정합니다(대부분의 워크로드에 권장되는 설정).      | TRANSACTION     |
| pgBouncer.min_pool_size | 이 값보다 낮은 경우 풀에 서버 연결을 추가합니다.    |   0(사용 안 함)   |
| pgbouncer.ignore_startup_parameters | PgBouncer에서 무시할 수 있는 쉼표로 구분된 매개 변수 목록입니다. 예을 들어 PgBouncer가 `extra_float_digits` 매개 변수를 무시하도록 할 수 있습니다.|   |
| pgbouncer.query_wait_timeout | 쿼리가 실행을 기다리는 데 허용되는 최대 시간(초)입니다. 이 시간 동안 쿼리가 서버에 할당되지 않으면 클라이언트의 연결이 끊어집니다. | 120s |
| pgBouncer.stats_users | 선택 사항입니다. 이 매개 변수 값을 기존 사용자의 이름으로 설정하여 “PgBouncer”라는 특수 PgBouncer 통계 데이터베이스에 로그인할 수 있습니다.    |      |

PgBouncer 구성에 대한 자세한 내용은 [pgbouncer.ini](https://www.pgbouncer.org/config.html)를 참조하세요.

> [!Note] 
> PgBouncer 업그레이드는 Azure에서 관리 합니다.

## <a name="switching-your-application-to-use-pgbouncer"></a>PgBouncer를 사용하도록 애플리케이션 전환

PgBouncer 사용을 시작하려면 다음 단계를 수행합니다.
1. 데이터베이스 서버에 연결하되 일반 포트 5432 대신 포트 **6432** 를 사용하고 이 연결이 작동하는지 확인합니다.
```azurecli-interactive
psql "host=myPgServer.postgres.database.azure.com port=6432 dbname=postgres user=myUser password=myPassword sslmode=require"
```
2. PgBouncer에 대해 QA 환경에서 애플리케이션을 테스트하여 호환성 문제가 없는지 확인합니다. PgBouncer 프로젝트는 호환성 매트릭스를 제공하며 대부분의 사용자는 **트랜잭션 풀링** 을 사용하는 것이 좋습니다. https://www.PgBouncer.org/features.html#sql-feature-map-for-pooling-modes
3. 프로덕션 애플리케이션을 변경하여 **5432** 대신 포트 **6432** 에 연결하고 호환성 문제를 나타낼 수 있는 애플리케이션 쪽 오류를 모니터링합니다.

> [!Note] 
> PgBouncer를 사용하도록 설정한 경우에도 동일한 호스트 이름을 사용하여 포트 5432를 통해 데이터베이스 서버에 직접 연결할 수 있습니다.

## <a name="pgbouncer-in-zone-redundant-high-availability"></a>영역 중복 고가용성의 PgBouncer

영역 중복 고가용성 구성 서버에서 주 서버는 PgBouncer를 실행합니다. 6432 포트를 통해 주 서버의 PgBouncer에 연결할 수 있습니다. 장애 조치(failover) 후 새로운 주 서버인 새로 승격된 대기 서버에서 PgBouncer가 다시 시작됩니다. 따라서 애플리케이션 연결 문자열은 동일한 사후 장애 조치(failover)로 유지됩니다. 

## <a name="using-pgbouncer-with-other-connection-pools"></a>다른 연결 풀에서 PgBouncer 사용

경우에 따라 애플리케이션 쪽 연결 풀이 이미 있거나 AKS 사이드카와 같은 애플리케이션 쪽에 PgBouncer가 설정되어 있을 수 있습니다. 이러한 경우 유휴 연결 크기 조정 이점을 제공하기 때문에 기본 제공 PgBouncer를 활용하는 것이 유용할 수 있습니다.

데이터베이스 서버에서 PgBouncer와 함께 애플리케이션 쪽 풀을 활용하는 것이 유용할 수 있습니다. 여기서 애플리케이션 쪽 풀은 초기 연결 대기 시간을 줄이는 이점을 제공하며(연결을 초기화하는 초기 왕복이 훨씬 빠르기 때문에) 데이터베이스 쪽 PgBouncer는 유휴 연결 크기 조정 이점을 제공합니다.

## <a name="limitations"></a>제한 사항
 
* PgBouncer는 현재 버스트 가능한 서버 컴퓨팅 계층에서 지원되지 않습니다. 
* 컴퓨팅 계층을 범용 또는 메모리 최적화에서 버스트 가능 계층으로 변경하면 PgBouncer 기능이 손실됩니다.
* 크기 조정 작업, HA 장애 조치(failover) 또는 다시 시작 중에 서버를 다시 시작할 때마다 서버 가상 머신과 함께 PgBouncer도 다시 시작됩니다. 따라서 기존 연결을 다시 설정해야 합니다.
* 알려진 문제로 인해 포털에 모든 PgBouncer 매개 변수가 표시되지는 않습니다. PgBouncer를 사용하도록 설정하고 매개 변수를 저장한 후에는 매개 변수 화면(예: 개요 클릭)을 종료한 다음 매개 변수 페이지로 돌아가야 합니다. 
* [Scram 인증은](how-to-connect-scram.md) PgBouncer에서 지원 되지 않습니다.
  
## <a name="next-steps"></a>다음 단계

- [네트워킹 개념](./concepts-networking.md)에 대해 알아보기
- 유연한 서버 [개요](./overview.md)
