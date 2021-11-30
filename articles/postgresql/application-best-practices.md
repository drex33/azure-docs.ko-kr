---
title: 앱 개발 모범 사례 - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL을 사용하여 앱을 빌드하는 데 유용한 모범 사례에 대해 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/10/2020
ms.openlocfilehash: b7a23616521e486670c8e4f2c555d7c383c6e90a
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133217865"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-postgresql"></a>Azure Database for PostgreSQL을 사용하여 애플리케이션을 빌드하는 데 유용한 모범 사례

다음은 Azure Database for PostgreSQL을 사용하여 클라우드 지원 애플리케이션을 빌드하는 데 유용한 몇 가지 모범 사례입니다. 이러한 모범 사례를 참고하면 앱 개발 시간을 줄일 수 있습니다.

## <a name="configuration-of-application-and-database-resources"></a>애플리케이션 및 데이터베이스 리소스 구성

### <a name="keep-the-application-and-database-in-the-same-region"></a>애플리케이션과 데이터베이스를 동일한 지역에 유지
Azure에서 애플리케이션을 배포할 때 모든 종속성이 동일한 지역에 있는지 확인합니다. 지역 또는 가용성 영역에 걸쳐 인스턴스를 분산하는 경우 네트워크 대기 시간이 발생하므로 애플리케이션의 전반적인 성능에 영향을 줄 수 있습니다.

### <a name="keep-your-postgresql-server-secure"></a>PostgreSQL 서버의 보안 유지
PostgreSQL 서버를 [보안](./concepts-security.md)이 유지되고 공용으로 액세스할 수 없도록 구성합니다. 다음 옵션 중 하나를 사용하여 서버의 보안을 유지합니다.
- [방화벽 규칙](./concepts-firewall-rules.md)
- [가상 네트워크](./concepts-data-access-and-security-vnet.md)
- [Azure Private Link](./concepts-data-access-and-security-private-link.md)

보안을 위해 항상 SSL을 통해 PostgreSQL 서버에 연결하고 PostgreSQL 서버 및 애플리케이션에서 TLS 1.2를 사용하도록 구성해야 합니다. [SSL/TLS를 구성하는 방법](./concepts-ssl-connection-security.md)을 참조하세요.

### <a name="tune-your-server-parameters"></a>서버 매개 변수 조정
읽기 작업이 많은 워크로드의 경우 `tmp_table_size` 및 `max_heap_table_size`는 서버 매개 변수를 조정하는 데 유용하며 성능을 향상시킬 수 있습니다. 이러한 변수에 필요한 값을 계산하려면 총 연결당 메모리 값 및 기본 메모리를 확인합니다. 서버의 총 메모리에 대해 기본 메모리 계정과 결합된 `tmp_table_size`를 제외한 연결당 메모리 매개 변수의 합입니다.

### <a name="use-environment-variables-for-connection-information"></a>연결 정보에 환경 변수 사용
애플리케이션 코드에 데이터베이스 자격 증명을 저장하지 마세요. 프런트 엔드 응용 프로그램에 따라 환경 변수 설정 지침을 따릅니다. App service 사용에 대 한 자세한 내용은 [앱 설정을 구성 하는 방법](../app-service/configure-common.md#configure-app-settings) 및 Azure Kuberentes 서비스에 대 한 자세한 내용은 [Kubernetes 암호를 사용 하는 방법](https://kubernetes.io/docs/concepts/configuration/secret/)을 참조 하세요.

## <a name="performance-and-resiliency"></a>성능 및 복원력
다음은 애플리케이션의 성능 문제를 디버그하는 데 사용할 수 있는 몇 가지 도구와 사례입니다.

### <a name="use-connection-pooling"></a>연결 풀링 사용
연결 풀링을 사용하면 시작 시 고정된 연결 집합이 설정되고 유지 관리됩니다. 이를 통해 데이터베이스 서버에 설정된 동적 새 연결로 인해 발생하는 서버의 메모리 조각화를 줄일 수 있습니다. 애플리케이션 프레임워크 또는 데이터베이스 드라이버가 지원하는 경우 연결 풀링을 애플리케이션 측에서 구성할 수 있습니다. 지원되지 않는 경우 애플리케이션 외부에서 실행 중이고 데이터베이스 서버에 연결된 [PgBouncer](https://pgbouncer.github.io/) 또는 [Pgpool](https://pgpool.net/mediawiki/index.php/Main_Page)과 같은 프록시 연결 풀 서비스를 활용하는 것도 좋습니다. PgBouncer 및 Pgpool은 모두 Azure Database for PostgreSQL과 작동하는 커뮤니티 기반 도구입니다.

### <a name="retry-logic-to-handle-transient-errors"></a>일시적인 오류를 처리하기 위한 다시 시도 논리
애플리케이션에서 데이터베이스에 대한 연결이 일시적으로 끊어지거나 손실되는 일시적인 오류가 발생할 수 있습니다. 이러한 경우 서버는 5~10 초 내에 1~2회 다시 시도한 후에 가동되어 실행됩니다. 첫 번째로 다시 시도하기 전에 5초간 대기하는 것이 좋습니다. 그런 다음, 대기 시간을 최대 60초까지 점진적으로 늘려 다시 시도할 때마다 수행합니다. 애플리케이션에서 실패한 작업으로 간주되는 시점의 최대 다시 시도 횟수를 제한하여 추가 조사를 수행할 수 있습니다. 자세히 알아보려면 [연결 오류 문제를 해결하는 방법](./concepts-connectivity.md)을 참조하세요.

### <a name="enable-read-replication-to-mitigate-failovers"></a>장애 조치(failover)를 완화하기 위해 읽기 복제본을 사용하도록 설정
장애 조치(failover) 시나리오에 [입력 데이터 복제](./concepts-read-replicas.md)를 사용할 수 있습니다. 읽기 복제본을 사용하는 경우, 원본 서버와 복제본 서버 간에 자동 장애 조치(failover)가 수행되지 않습니다. 복제는 비동기이므로 원본과 복제본 사이에 지연되는 시간이 발생하게 됩니다. 네트워크 지연은 원본 서버에서 실행되는 워크로드의 크기와 데이터 센터 간 발생하는 대기 시간 등 여러 가지 요인에 영향을 받을 수 있습니다. 대부분의 경우 복제본 지연 시간은 몇 초에서 몇 분 사이입니다.


## <a name="database-deployment"></a>데이터베이스 배포

### <a name="configure-cicd-deployment-pipeline"></a>CI/CD 배포 파이프라인 구성
경우에 따라 데이터베이스에 변경 내용을 배포해야 합니다. 이러한 경우 PostgreSQL 서버에 대한 [GitHub 작업](https://github.com/Azure/postgresql/blob/master/README.md)을 통해 CI(연속 통합)로 사용자 지정 스크립트를 실행하여 데이터베이스를 업데이트할 수 있습니다.

### <a name="define-manual-database-deployment-process"></a>수동 데이터베이스 배포 프로세스 정의
수동 데이터베이스를 배포하는 중에 다음 단계를 수행하여 가동 중지 시간을 최소화하거나 배포에 실패할 위험을 줄입니다.

- pg_dump를 사용하여 새 데이터베이스에 프로덕션 데이터베이스 복사본을 만듭니다.
- 새 스키마 변경 내용 또는 데이터베이스에 필요한 업데이트로 새 데이터베이스를 업데이트합니다.
- 프로덕션 데이터베이스를 읽기 전용 상태로 전환합니다. 배포가 완료될 때까지 프로덕션 데이터베이스에서 쓰기 작업을 수행하지 않아야 합니다.
- 1단계에서 새로 업데이트한 데이터베이스를 사용하여 애플리케이션을 테스트합니다.
- 애플리케이션 변경 내용을 배포하고 애플리케이션이 현재 최근 업데이트된 새 데이터베이스를 사용 중인지 확인합니다.
- 변경 사항을 롤백할 수 있도록 이전 프로덕션 데이터베이스를 보관합니다. 그런 다음, 필요한 경우 이전 프로덕션 데이터베이스를 삭제하거나 Azure Storage로 내보내도록 평가할 수 있습니다.

>  [!NOTE]
> 전자 상거래 앱과 같이 읽기 전용 상태로 전환할 수 없는 애플리케이션의 경우 백업을 수행한 후 프로덕션 데이터베이스에 직접 변경 내용을 배포합니다. 이렇게 하면 일부 사용자가 실패한 요청을 경험할 수 있으므로 사용량이 적은 시간에 변경하여 앱에 트래픽이 적게 발생하도록 영향을 최소화해야 합니다. 애플리케이션 코드가 실패한 요청도 모두 처리하는지 확인합니다.

## <a name="database-schema-and-queries"></a>데이터베이스 스키마 및 쿼리
다음은 데이터베이스 스키마와 쿼리를 빌드할 때 유의해야 할 몇 가지 팁입니다.

### <a name="use-bigint-or-uuid-for-primary-keys"></a>기본 키에 BIGINT 또는 UUID 사용
사용자 지정 애플리케이션 또는 일부 프레임워크를 빌드할 때 기본 키에 `BIGINT` 대신 `INT`를 사용하는 것이 좋습니다. ```INT```를 사용하는 경우, 데이터베이스의 값이 ```INT```데이터 형식의 스토리지 용량을 초과할 수 있는 지점에서 위험을 실행합니다. 이러한 변경 작업을 기존 프로덕션 애플리케이션에 적용하려면 개발하는 데 더 많은 시간이 소요될 수 있습니다. 또 다른 옵션은 기본 키에 [UUID](https://www.postgresql.org/docs/current/datatype-uuid.html)를 사용하는 것입니다. 이 식별자는 자동 생성된 128비트 문자열을 사용합니다(예: ```a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11```). [PostgreSQL 데이터 형식](https://www.postgresql.org/docs/8.1/datatype.html)에 대해 자세히 알아봅니다.

### <a name="use-indexes"></a>인덱스 사용

Postgres에는 다양한 방식으로 사용할 수 있는 여러 [인덱스](https://www.postgresql.org/docs/9.1/indexes.html) 유형이 있습니다. 인덱스를 사용하면 서버에서 인덱스 없이 수행하는 것 보다 훨씬 더 빠르게 특정 행을 찾고 검색할 수 있습니다. 하지만 인덱스는 데이터베이스 서버에 오버헤드를 추가하므로 인덱스 수가 너무 많아지지 않도록 방지합니다.

### <a name="use-autovacuum"></a>자동 진공 사용
Azure Database for PostgreSQL 서버에서 자동 진공을 사용하여 서버를 최적화할 수 있습니다. PostgreSQL을 사용하면 데이터베이스 동시성이 향상되지만 모든 업데이트 결과가 삽입 및 삭제됩니다. 삭제되는 레코드에는 일시적으로 표시하여 나중에 제거합니다. 이러한 작업을 수행하기 위해 PostgreSQL은 진공 작업을 실행합니다. 때때로 진공하지 않으면 누적된 데드 튜플로 인해 다음과 같은 결과가 발생할 수 있습니다.

- 데이터 블로트(예: 큰 데이터베이스 및 테이블)
- 부적절한 인덱스 증가
- I/O 증가

[자동 진공을 사용하여 최적화하는 방법](howto-optimize-autovacuum.md)에 대해 자세히 알아보세요.

### <a name="use-pg_stats_statements"></a>pg_stats_statements 사용
Pg_stat_statements는 Azure Database for PostgreSQL에서 기본적으로 사용하도록 설정된 PostgreSQL 확장입니다. 이 확장은 서버에서 실행되는 모든 SQL 문의 실행 통계를 추적하는 수단을 제공합니다. [pg_statement를 사용하는 방법](howto-optimize-query-stats-collection.md)을 참조하세요.


### <a name="use-the-query-store"></a>쿼리 저장소 사용
Azure Database for PostgreSQL의 [쿼리 저장소](./concepts-query-store.md) 기능은 쿼리 성능을 추적하는 더 효과적인 방법을 제공합니다. pg_stats_statements를 사용하지 않고 기능을 사용하는 것이 좋습니다.

### <a name="optimize-bulk-inserts-and-use-transient-data"></a>대량 삽입 최적화 및 임시 데이터 사용
임시 데이터를 포함하거나 큰 데이터 세트를 대량으로 삽입하는 워크로드 작업이 있는 경우 기록되지 않는 테이블을 사용하는 방안을 고려해 보세요. 기본적으로 원자성 및 내구성을 제공합니다. 원자성, 일관성, 격리 및 내구성이 모여서 ACID 속성을 구성합니다. [대량 삽입을 최적화하는 방법](howto-optimize-bulk-inserts.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[Postgres 가이드](http://postgresguide.com/)
