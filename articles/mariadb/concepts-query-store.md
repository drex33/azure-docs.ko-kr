---
title: 쿼리 저장소 - Azure Database for MariaDB
description: 시간 경과에 따른 성능을 추적하는 데 도움이 되는 Azure Database for MariaDB의 쿼리 저장소 기능에 대해 알아봅니다.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: cc43fd3ea141852373d80814f1c60c5a2c88c358
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132956370"
---
# <a name="monitor-azure-database-for-mariadb-performance-with-query-store"></a>쿼리 저장소를 사용하여 Azure Database for MariaDB 성능 모니터링

**적용 대상:** Azure Database for MariaDB 10.2

Azure Database for MariaDB의 쿼리 저장소 기능은 시간 경과에 따라 쿼리 성능을 추적하는 방법을 제공합니다. 쿼리 저장소는 가장 오래 실행되고 리소스를 가장 많이 사용하는 쿼리를 신속하게 찾도록 지원하여 성능 문제 해결을 단순화합니다. 쿼리 저장소는 쿼리 및 런타임 통계의 기록을 자동으로 캡처하고 검토를 위해 보존합니다. 데이터베이스 사용량 패턴을 볼 수 있도록 데이터를 기간별로 구분합니다. 모든 사용자, 데이터베이스 및 쿼리에 대한 데이터는 Azure Database for MariaDB 인스턴스의 **mysql** 스키마 데이터베이스에 저장됩니다.

## <a name="common-scenarios-for-using-query-store"></a>쿼리 저장소 사용에 대한 일반적인 시나리오

쿼리 저장소는 다음을 비롯한 여러 시나리오에 사용할 수 있습니다.

- 재발된 쿼리를 검색하는 경우
- 지정된 기간에 쿼리가 실행된 횟수를 확인하는 경우
- 전체 기간에 대한 쿼리의 평균 실행 시간을 비교하여 큰 델타를 확인하는 경우

## <a name="enabling-query-store"></a>쿼리 저장소 사용

쿼리 저장소는 옵트인 기능이므로 서버에서 기본적으로 활성화되지 않습니다. 쿼리 저장소는 지정된 서버의 모든 데이터베이스에 대해 전역으로 사용하거나 사용하지 않도록 설정되며 데이터베이스별로 켜거나 끌 수 없습니다.

### <a name="enable-query-store-using-the-azure-portal"></a>Azure Portal을 통해 쿼리 저장소 사용

1. Azure Portal에 로그인하고 Azure Database for MariaDB 서버를 선택합니다.
2. 메뉴의 **설정** 섹션에서 **서버 매개 변수** 를 선택합니다.
3. query_store_capture_mode 매개 변수를 검색합니다.
4. 값을 모두로 설정하고 **저장** 합니다.

쿼리 저장소에서 대기 통계를 사용하도록 설정하려면 다음과 같이 합니다.

1. query_store_wait_sampling_capture_mode 매개 변수를 검색합니다.
2. 값을 모두로 설정하고 **저장** 합니다.

데이터의 첫 번째 배치가 mysql 데이터베이스에서 지속되는 데 최대 20분이 걸립니다.

## <a name="information-in-query-store"></a>쿼리 저장소의 정보

쿼리 저장소에는 다음과 같은 두 개의 저장소가 있습니다.

- 쿼리 실행 통계 정보를 지속하기 위한 런타임 통계 저장소
- 대기 통계 정보를 지속하기 위한 대기 통계 저장소

공간 사용량을 최소화하기 위해 런타임 통계 스토리지의 런타임 실행 통계가 고정된 구성 가능 기간을 통해 집계됩니다. 이러한 저장소의 정보는 쿼리 저장소 보기를 쿼리하여 표시됩니다.

다음 쿼리는 쿼리 저장소의 쿼리에 대한 정보를 반환합니다.

```sql
SELECT * FROM mysql.query_store;
```

또는 대기 통계에 대한 이 쿼리는 다음을 수행합니다.

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>대기 쿼리 찾기

> [!NOTE]
> 대기 통계는 최고 워크로드 동안 사용하도록 설정하거나 중요한 워크로드에 대해 무기한으로 켜두면 안 됩니다. <br>높은 CPU 사용률 또는 더 낮은 vCores로 구성된 서버에서 실행되는 워크로드의 경우 대기 통계를 사용하도록 설정할 때는 주의해야 합니다. 무기한으로 켜두면 안 됩니다. 

대기 이벤트 유형은 유사성을 기준으로 서로 다른 대기 이벤트를 버킷으로 결합합니다. 쿼리 저장소는 대기 이벤트 유형, 특정 대기 이벤트 이름 및 해당하는 쿼리를 제공합니다. 이 대기 정보를 쿼리 런타임 통계와 상호 연관시킬 수 있다는 것은 쿼리 성능 특성에 영향을 미치는 내용을 더 잘 이해할 수 있음을 의미합니다.

다음은 쿼리 저장소의 대기 통계를 사용하여 워크로드에 대한 더 많은 통찰력을 얻을 수 있는 방법의 몇 가지 예입니다.

| **관찰** | **동작** |
|---|---|
|최고 잠금 대기 | 영향을 받는 쿼리에 대한 쿼리 텍스트를 확인하고 대상 엔터티를 식별합니다. 쿼리 저장소에서 자주 실행되거나 오래 실행되는 동일한 엔터티를 수정하는 다른 쿼리를 확인합니다. 이러한 쿼리를 식별한 후 애플리케이션 논리를 변경하여 동시성을 개선하거나 덜 제한적인 격리 수준을 사용하는 것이 좋습니다. |
|높은 버퍼 IO 대기 | 쿼리 저장소에서 물리적 읽기 횟수가 많은 쿼리를 찾습니다. 해당 쿼리가 IO 대기가 많은 쿼리와 일치하는 경우 검사 대신 검색을 수행하기 위해 기본 엔터티에 인덱스를 도입하는 것이 좋습니다. 이렇게 하면 쿼리의 IO 오버헤드가 최소화됩니다. 포털에서 서버에 대한 **성능 권장 사항** 을 확인하여 쿼리를 최적화하는 이 서버에 대한 인덱스 권장 사항이 있는지 확인합니다. |
|높은 메모리 대기 | 쿼리 저장소에서 메모리 사용량이 많은 상위 쿼리를 찾습니다. 이러한 쿼리는 영향을 받는 쿼리의 추가 진행을 지연시킬 수 있습니다. 포털에서 서버에 대한 **성능 권장 사항** 을 확인하여 이러한 쿼리를 최적화하는 인덱스 권장 사항이 있는지 확인합니다.|

## <a name="configuration-options"></a>구성 옵션

쿼리 저장소를 사용하도록 설정하면 데이터가 15분 집계 창에 저장되고 각 창에는 최대 500개의 고유 쿼리가 포함됩니다.

다음 옵션은 쿼리 저장소 매개 변수를 구성하는 데 사용할 수 있습니다.

| **매개 변수** | **설명** | **기본값** | **Range** |
|---|---|---|---|
| query_store_capture_mode | 값에 따라 쿼리 저장소 기능을 설정/해제합니다. 참고: performance_schema가 꺼진 경우 query_store_capture_mode를 켜면 performance_schema와 이 기능에 필요한 성능 스키마 계측의 하위 집합이 켜집니다. | ALL | NONE, ALL |
| query_store_capture_interval | 쿼리 저장소 캡처 간격(분)입니다. 쿼리 메트릭이 집계되는 간격을 지정할 수 있습니다. | 15 | 5 - 60 |
| query_store_capture_utility_queries | 시스템에서 실행되는 모든 유틸리티 쿼리의 캡처를 켜거나 끕니다. | 아니요 | 예, 아니요 |
| query_store_retention_period_in_days | 쿼리 저장소에 데이터를 유지할 기간(일)입니다. | 7 | 1 - 30 |

다음 옵션은 특히 대기 통계에 적용됩니다.

| **매개 변수** | **설명** | **기본값** | **Range** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | 대기 통계를 켜고 끌 수 있습니다. | 없음 | NONE, ALL |
| query_store_wait_sampling_frequency | 대기 샘플링 빈도(초)를 변경합니다. 5~300초 | 30 | 5-300 |

> [!NOTE]
> 현재 **query_store_capture_mode** 는 이 구성을 대체합니다. 즉, **query_store_capture_mode** 및 **query_store_wait_sampling_capture_mode** 를 모두 사용하도록 설정하여 대기 통계가 작동하도록 해야 합니다. **query_store_capture_mode** 가 꺼져 있는 경우, 대기 통계가 performance_schema enabled 및 쿼리 저장소에 의해 캡처된 query_text를 활용하므로 대기 통계도 켜집니다.

[Azure Portal](howto-server-parameters.md) 을 사용하여 매개 변수에 대한 다른 값을 가져오거나 설정합니다.

## <a name="views-and-functions"></a>보기 및 함수

다음 보기 및 함수를 사용하여 쿼리 저장소를 보고 관리합니다. [일부 권한 공용 역할](howto-create-users.md#create-more-admin-users)의 사용자는 이러한 보기를 사용하여 쿼리 저장소의 데이터를 볼 수 있습니다. 이러한 보기는 **mysql** 데이터베이스에서만 사용할 수 있습니다.

쿼리는 리터럴 및 상수를 제거한 후 구조를 확인하여 정규화됩니다. 리터럴 값을 제외하고 두 개의 쿼리가 동일한 경우 동일한 해시가 있습니다.

### <a name="mysqlquery_store"></a>mysql.query_store

이 보기는 쿼리 저장소의 모든 데이터를 반환합니다. 각 고유 데이터베이스 ID, 사용자 ID 및 쿼리 ID에 대한 하나의 행이 있습니다.

| **이름** | **데이터 형식** | **IS_NULLABLE** | **설명** |
|---|---|---|---|
| `schema_name`| varchar(64) | 아니요 | 스키마의 이름입니다. |
| `query_id`| bigint(20) | 아니요| 특정 쿼리에 대해 생성되는 고유 ID로 같은 쿼리가 다른 스키마에서 실행되는 경우 새 ID가 생성됩니다. |
| `timestamp_id` | timestamp| 아니요| 쿼리가 실행되는 타임스탬프입니다. query_store_interval 구성을 기반으로 합니다.|
| `query_digest_text`| longtext| 아니요| 모든 리터럴을 제거한 후 정규화된 쿼리 텍스트입니다.|
| `query_sample_text` | longtext| 아니요| 리터럴을 사용한 실제 쿼리의 첫 번째 모양입니다.|
| `query_digest_truncated` | bit| YES| 쿼리 텍스트가 잘렸는지의 여부입니다. 쿼리가 1KB 보다 길면 값은 예입니다.|
| `execution_count` | bigint(20)| 아니요| 이 타임스탬프 ID/구성된 시간 간격 동안 쿼리를 실행했던 횟수입니다.|
| `warning_count` | bigint(20)| 아니요| 시간 간격 동안 이 쿼리가 생성된 경고의 수입니다.|
| `error_count` | bigint(20)| 아니요| 시간 간격 동안 이 쿼리가 생성된 오류의 수입니다.|
| `sum_timer_wait` | double| YES| 시간 간격 동안 이 쿼리의 전체 실행 시간입니다.|
| `avg_timer_wait` | double| YES| 시간 간격 동안 이 쿼리에 대한 평균 실행 시간입니다.|
| `min_timer_wait` | double| YES| 이 쿼리에 대한 최소 실행 시간입니다.|
| `max_timer_wait` | double| YES| 최대 실행 시간입니다.|
| `sum_lock_time` | bigint(20)| 아니요| 이 기간 동안 이 쿼리 실행에 대한 모든 잠금에 소요된 총시간입니다.|
| `sum_rows_affected` | bigint(20)| 아니요| 행 수가 영향을 받음|
| `sum_rows_sent` | bigint(20)| 아니요| 클라이언트로 전송된 행 수입니다.|
| `sum_rows_examined` | bigint(20)| 아니요| 검사된 행 수|
| `sum_select_full_join` | bigint(20)| 아니요| 전체 조인 수입니다.|
| `sum_select_scan` | bigint(20)| 아니요| 선택된 검색 수입니다. |
| `sum_sort_rows` | bigint(20)| 아니요| 정렬된 행 수입니다.|
| `sum_no_index_used` | bigint(20)| 아니요| 쿼리에서 어떤 인덱스도 사용하지 않은 횟수입니다|
| `sum_no_good_index_used` | bigint(20)| 아니요| 쿼리 실행 엔진이 양호한 인덱스를 사용하지 않은 횟수입니다|
| `sum_created_tmp_tables` | bigint(20)| 아니요| 만든 임시 테이블의 총 수입니다.|
| `sum_created_tmp_disk_tables` | bigint(20)| 아니요| 디스크에 생성된 임시 테이블의 총 수입니다(I/O 생성).|
| `first_seen` | timestamp| 아니요| 집계 기간 중 쿼리의 첫 번째 발생입니다(UTC).|
| `last_seen` | timestamp| 아니요| 이 집계 기간 중 쿼리의 마지막 발생입니다(UTC).|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

이 보기는 쿼리 저장소의 대기 이벤트 데이터를 반환합니다. 각 고유 데이터베이스 ID, 사용자 ID, 쿼리 ID 및 이벤트에 대한 하나의 행이 있습니다.

| **이름**| **데이터 형식** | **IS_NULLABLE** | **설명** |
|---|---|---|---|
| `interval_start` | timestamp | 아니요| 간격의 시작입니다(15분 증분).|
| `interval_end` | timestamp | 아니요| 간격의 끝입니다(15분 증분).|
| `query_id` | bigint(20) | 아니요| 쿼리 저장소에서 정규화된 쿼리에 대해 생성된 고유 ID입니다.|
| `query_digest_id` | varchar(32) | 아니요| 쿼리 저장소에서 모든 리터럴을 제거한 후의 정규화된 쿼리 텍스트입니다. |
| `query_digest_text` | longtext | 아니요| 쿼리 저장소에서 리터럴을 사용한 실제 쿼리의 첫 번째 모양입니다. |
| `event_type` | varchar(32) | 아니요| 대기 이벤트의 범주입니다. |
| `event_name` | varchar(128) | 아니요| 대기 이벤트의 이름입니다. |
| `count_star` | bigint(20) | 아니요| 쿼리의 간격 중 샘플링된 대기 이벤트 수입니다. |
| `sum_timer_wait_ms` | double | 아니요| 시간 간격 중에 이 쿼리의 총 대기 시간(밀리초)입니다. |

### <a name="functions"></a>Functions

| **이름**| **설명** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | 지정된 타임스탬프 이전의 모든 쿼리 저장소 데이터를 제거합니다. |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | 지정된 타임스탬프 이전의 모든 대기 이벤트 데이터를 제거합니다. |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | 만료 날짜가 지정된 타임스탬프 이전인 권장 사항을 제거합니다. |

## <a name="limitations-and-known-issues"></a>제한 사항 및 알려진 문제

- MariaDB 서버에 `default_transaction_read_only` 매개 변수가 있으면 쿼리 저장소는 데이터를 캡처할 수 없습니다.
- 긴 유니코드 쿼리(\>= 6000바이트)가 발견되면 쿼리 저장소 기능이 중단될 수 있습니다.
- 대기 통계의 보존 기간은 24시간입니다.
- 대기 통계는 샘플을 사용하여 이벤트의 비율을 캡처합니다. `query_store_wait_sampling_frequency` 매개 변수를 사용하여 빈도를 수정할 수 있습니다.
- 쿼리 저장소는 버전 10.3에서 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Query Performance Insights](concepts-query-performance-insight.md)에 대한 자세한 정보
