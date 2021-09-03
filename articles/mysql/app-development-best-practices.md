---
title: 앱 개발 모범 사례 - Azure Database for MySQL
description: Azure Database for MySQL을 사용하여 앱을 빌드하기 위한 모범 사례에 대해 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 1ec3d86ea66e436732cd8d1044c0658238ba781f
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113086265"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-mysql"></a>Azure Database for MySQL을 사용하여 애플리케이션을 빌드하기 위한 모범 사례

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

다음은 Azure Database for MySQL을 사용하여 클라우드 지원 애플리케이션을 빌드하는 데 유용한 몇 가지 모범 사례입니다. 이러한 모범 사례를 참고하면 앱 개발 시간을 줄일 수 있습니다. 

## <a name="configuration-of-application-and-database-resources"></a>애플리케이션 및 데이터베이스 리소스 구성

### <a name="keep-the-application-and-database-in-the-same-region"></a>애플리케이션과 데이터베이스를 동일한 지역에 유지

Azure에서 애플리케이션을 배포할 때 모든 종속성이 동일한 지역에 있는지 확인합니다. 지역 또는 가용성 영역에 걸쳐 인스턴스를 분산하는 경우 네트워크 대기 시간이 발생하므로 애플리케이션의 전반적인 성능에 영향을 줄 수 있습니다.

### <a name="keep-your-mysql-server-secure"></a>MySQL 서버의 보안 유지

MySQL 서버를 [보안](./concepts-security.md)이 유지되고 공용으로 액세스할 수 없도록 구성합니다. 다음 옵션 중 하나를 사용하여 서버의 보안을 유지합니다.

- [방화벽 규칙](./concepts-firewall-rules.md)
- [가상 네트워크](./concepts-data-access-and-security-vnet.md)
- [Azure Private Link](./concepts-data-access-security-private-link.md)

보안을 위해 항상 SSL을 통해 MySQL 서버에 연결하고 MySQL 서버 및 애플리케이션에서 TLS 1.2를 사용하도록 구성해야 합니다. [SSL/TLS를 구성하는 방법](./concepts-ssl-connection-security.md)을 참조하세요.

### <a name="use-advanced-networking-with-aks"></a>AKS에서 고급 네트워킹 사용

VM에서 가속 네트워킹을 사용하도록 설정하면 VM의 대기 시간, 지터 및 CPU 사용률이 감소됩니다. 자세한 내용은 [Azure Kubernetes Service 및 Azure Database for MySQL 모범 사례](concepts-aks.md)를 참조하세요.

### <a name="tune-your-server-parameters"></a>서버 매개 변수 조정

읽기 작업이 많은 워크로드의 경우 `tmp_table_size` 및 `max_heap_table_size`는 서버 매개 변수를 조정하는 데 유용하며 성능을 향상시킬 수 있습니다. 이러한 변수에 필요한 값을 계산하려면 총 연결당 메모리 값 및 기본 메모리를 확인합니다. 서버의 총 메모리에 대해 기본 메모리 계정과 결합된 `tmp_table_size`를 제외한 연결당 메모리 매개 변수의 합입니다.

`tmp_table_size` 및 `max_heap_table_size`에 가능한 최대 크기를 계산하려면 다음 수식을 사용합니다.

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

> [!NOTE]
> 총 메모리는 프로비전된 vCore에서 서버에 포함된 총 메모리 양을 나타냅니다.  예를 들어 범용 2개 vCore의 Azure Database for MySQL 서버에서 총 메모리는 5GB * 2가 됩니다. 각 계층의 메모리에 대한 자세한 정보는 [가격 책정 계층](./concepts-pricing-tiers.md) 설명서를 참조하세요.
>
> 기본 메모리는 MySQL가 서버 시작 시 초기화하고 할당하는 `query_cache_size` 및 `innodb_buffer_pool_size`와 같은 메모리 변수를 나타냅니다. `sort_buffer_size` 및 `join_buffer_size`와 같은 연결별 메모리는 쿼리에 필요할 때만 할당되는 메모리입니다.

### <a name="create-non-admin-users"></a>비관리 사용자 만들기

각 데이터베이스에 대해 [비관리 사용자를 만듭니다](./howto-create-users.md). 일반적으로 사용자 이름은 데이터베이스 이름으로 식별됩니다.

### <a name="reset-your-password"></a>암호 재설정

Azure Portal을 사용하여 MySQL 서버에 대해 [암호를 초기화](./howto-create-manage-server-portal.md#update-admin-password)할 수 있습니다.

프로덕션 데이터베이스에 대해 서버 암호를 초기화하면 애플리케이션이 작동 중지될 수 있습니다. 애플리케이션 사용자에 대한 영향을 최소화하기 위해서는 사용량이 적은 시간에 프로덕션 워크로드에 대해 암호를 초기화하는 것이 좋습니다.

## <a name="performance-and-resiliency"></a>성능 및 복원력

다음은 애플리케이션의 성능 문제를 디버그하는 데 사용할 수 있는 몇 가지 도구와 사례입니다.

### <a name="enable-slow-query-logs-to-identify-performance-issues"></a>성능 문제 식별을 위해 느린 쿼리 로그 사용

서버에서 [느린 쿼리 로그](./concepts-server-logs.md) 및 [감사 로그](./concepts-audit-logs.md)를 사용하도록 설정할 수 있습니다. 느린 쿼리 로그를 분석하면 문제 해결을 위해 성능 병목 상태를 식별하는 데 도움이 됩니다.

감사 로그는 또한 Azure Monitor 로그, Azure Event Hubs 및 스토리지 계정의 Azure Diagnostics 로그를 통해서도 제공됩니다. [쿼리 성능 문제 해결 방법](./howto-troubleshoot-query-performance.md)을 참조하세요.

### <a name="use-connection-pooling"></a>연결 풀링 사용

데이터베이스 연결을 관리하면 전체적으로 애플리케이션의 성능에 상당한 영향을 줄 수 있습니다. 성능을 최적화하려면 연결이 설정되는 횟수 및 핵심 코드 경로에서 연결을 설정하는 데 걸리는 시간을 줄여야 합니다. 복원력 및 성능을 향상시키기 위해 [연결 풀링](./concepts-connectivity.md#access-databases-by-using-connection-pooling-recommended)을 사용하여 Azure Database for MySQL에 연결합니다.

[ProxySQL](https://proxysql.com/) 연결 풀러를 사용하여 연결을 효율적으로 관리할 수 있습니다. 연결 풀러를 사용하면 유휴 연결을 줄이고 기존 연결을 재사용하여, 문제를 방지하는 데 도움을 줄 수 있습니다. 자세한 내용은 [ProxySQL 설정 방법](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842)을 참조하세요.

### <a name="retry-logic-to-handle-transient-errors"></a>일시적인 오류를 처리하기 위한 다시 시도 논리

애플리케이션에서 데이터베이스에 대한 연결이 일시적으로 끊어지거나 손실되는 [일시적인 오류](./concepts-connectivity.md#handling-transient-errors)가 발생할 수 있습니다. 이러한 경우 서버는 5~10 초 내에 1~2회 다시 시도한 후에 가동되어 실행됩니다.

첫 번째로 다시 시도하기 전에 5초간 대기하는 것이 좋습니다. 그런 다음, 대기 시간을 최대 60초까지 점진적으로 늘려 다시 시도할 때마다 수행합니다. 애플리케이션에서 실패한 작업으로 간주되는 시점의 최대 다시 시도 횟수를 제한하여 추가 조사를 수행할 수 있습니다. 자세히 알아보려면 [연결 오류 문제를 해결하는 방법](./howto-troubleshoot-common-connection-issues.md)을 참조하세요.

### <a name="enable-read-replication-to-mitigate-failovers"></a>장애 조치(failover)를 완화하기 위해 읽기 복제본을 사용하도록 설정

장애 조치(failover) 시나리오에 [입력 데이터 복제](./howto-data-in-replication.md)를 사용할 수 있습니다. 읽기 복제본을 사용하는 경우 원본과 복제본 서버 사이에 자동화된 장애 조치(failover)가 수행되지 않습니다.

복제가 비동기적이기 때문에 원본과 복제본 사이에 지연이 발생합니다. 네트워크 지연은 원본 서버에서 실행되는 워크로드의 크기와 데이터 센터 간 발생하는 대기 시간 등 여러 가지 요인에 영향을 받을 수 있습니다. 대부분의 경우 복제본 지연 시간은 몇 초에서 몇 분 사이입니다.

## <a name="database-deployment"></a>데이터베이스 배포

### <a name="configure-an-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>CI/CD 배포 파이프라인에서 Azure Database for MySQL 작업을 구성합니다.

경우에 따라 데이터베이스에 변경 내용을 배포해야 합니다. 이러한 경우 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)를 통해 CI(연속 통합) 및 CD(지속적인 업데이트)를 사용하고 [MySQL 서버](/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment)용 작업을 사용하여 사용자 지정 스크립트를 실행하여 데이터베이스를 업데이트할 수 있습니다.

### <a name="use-an-effective-process-for-manual-database-deployment"></a>수동 데이터베이스 배포에 효과적인 프로세스 사용

수동 데이터베이스를 배포하는 중에 다음 단계를 수행하여 가동 중지 시간을 최소화하거나 배포에 실패할 위험을 줄입니다.

1. [mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) 또는 [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html)를 사용하여 새 데이터베이스에 프로덕션 데이터베이스 복사본을 만듭니다.
2. 새 스키마 변경 내용 또는 데이터베이스에 필요한 업데이트로 새 데이터베이스를 업데이트합니다.
3. 프로덕션 데이터베이스를 읽기 전용 상태로 전환합니다. 배포가 완료될 때까지 프로덕션 데이터베이스에서 쓰기 작업을 수행하지 않아야 합니다.
4. 1단계에서 새로 업데이트한 데이터베이스를 사용하여 애플리케이션을 테스트합니다.
5. 애플리케이션 변경 내용을 배포하고 애플리케이션이 현재 최근 업데이트된 새 데이터베이스를 사용 중인지 확인합니다.
6. 변경 사항을 롤백할 수 있도록 이전 프로덕션 데이터베이스를 보관합니다. 그런 다음, 필요한 경우 이전 프로덕션 데이터베이스를 삭제하거나 Azure Storage로 내보내도록 평가할 수 있습니다.

> [!NOTE]
> 전자 상거래 앱과 같이 읽기 전용 상태로 전환할 수 없는 애플리케이션의 경우 백업을 수행한 후 프로덕션 데이터베이스에 직접 변경 내용을 배포합니다. 이렇게 하면 일부 사용자가 실패한 요청을 경험할 수 있으므로 사용량이 적은 시간에 변경하여 앱에 트래픽이 적게 발생하도록 영향을 최소화해야 합니다.
>
> 애플리케이션 코드가 실패한 요청도 모두 처리하는지 확인합니다.

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>MySQL 네이티브 메트릭을 사용하여 워크로드가 메모리 내 임시 테이블 크기를 초과하는지 확인

읽기가 많은 워크로드의 경우 MySQL 서버에 실행되는 쿼리가 메모리 내 임시 테이블 크기를 초과할 수 있습니다. 읽기가 많은 워크로드는 서버가 임시 테이블 기록을 디스크로 전환하도록 만들어 애플리케이션 성능에 영향을 줄 수 있습니다. 임시 테이블 크기를 초과하여 서버가 디스크에 기록하는지 확인하려면 다음 메트릭을 확인하세요.

```sql
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```

`created_tmp_disk_tables` 메트릭은 생성된 테이블 수를 나타냅니다. `created_tmp_table` 메트릭은 워크로드에 따라 메모리에 생성되어야 하는 임시 테이블 수를 나타냅니다. 특정 쿼리를 실행할 때 임시 테이블이 사용되는지 확인하려면 쿼리에 대해 [EXPLAIN](https://dev.mysql.com/doc/refman/8.0/en/explain.html) 문을 실행합니다. 쿼리가 임시 테이블을 사용하여 실행되는 경우에는 `extra` 열의 세부 정보에 `Using temporary`가 나타납니다.

쿼리가 디스크로 넘치는 워크로드 백분율을 계산하려면 다음 수식에 메트릭 값을 사용합니다.

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

이상적으로 이 백분율은 25% 미만이어야 합니다. 백분율이 25% 이상이면 tmp_table_size 및 max_heap_table_size의 두 서버 매개 변수를 수정하는 것이 좋습니다.

## <a name="database-schema-and-queries"></a>데이터베이스 스키마 및 쿼리

다음은 데이터베이스 스키마와 쿼리를 빌드할 때 유의해야 할 몇 가지 팁입니다.

### <a name="use-the-right-datatype-for-your-table-columns"></a>테이블 열에 적합한 데이터 형식 사용

저장할 데이터 형식에 따라 올바른 데이터 형식을 사용하면 스토리지를 최적화하고, 잘못된 데이터 형식으로 인해 발생할 수 있는 오류를 줄일 수 있습니다.

### <a name="use-indexes"></a>인덱스 사용

느린 쿼리를 방지하려면 인덱스를 사용할 수 있습니다. 인덱스를 사용하면 특정 열이 포함된 행을 빠르게 찾을 수 있습니다. [MySQL에서 인덱스를 사용하는 방법](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html)을 참조하세요.

### <a name="use-explain-for-your-select-queries"></a>SELECT에 EXPLAIN 사용

`EXPLAIN` 문을 사용하여 쿼리 실행을 위해 MySQL이 수행 중인 작업에 대한 인사이트를 얻을 수 있습니다. 이것은 병목 상태 또는 쿼리 문제를 감지하는 데 도움이 됩니다. [EXPLAIN을 사용하여 쿼리 성능을 프로파일링하는 방법](./howto-troubleshoot-query-performance.md)을 참조하세요.
