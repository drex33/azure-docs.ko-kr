---
title: 유용한 진단 쿼리 - 하이퍼스케일(Citus)-Azure Database for PostgreSQL
description: 분산 데이터 등에 대해 알아보기 위한 쿼리
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: efd251e48beb4e2f2b3db16f694da14888e876dd
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822538"
---
# <a name="useful-diagnostic-queries"></a>유용한 진단 쿼리

## <a name="finding-which-node-contains-data-for-a-specific-tenant"></a>특정 테넌트에 대한 데이터를 포함하는 노드 찾기

다중 테넌트 사용 사례에서 특정 테넌트에 대한 행을 포함하는 작업자 노드를 확인할 수 있습니다.  하이퍼스케일(Citus)은 분산 테이블의 행을 분할하여 그룹화하고 각 분할된 데이터베이스를 서버 그룹의 작업자 노드에 배치합니다. 

애플리케이션의 테넌트가 저장소이고 상점 ID=4의 데이터를 보유하는 작업자 노드를 찾으려고 한다고 가정하겠습니다.  즉, 배포 열의 값이 4인 행을 포함하는 분할된 데이터베이스의 위치를 찾으려고 합니다.

``` postgresql
SELECT shardid, shardstate, shardlength, nodename, nodeport, placementid
  FROM pg_dist_placement AS placement,
       pg_dist_node AS node
 WHERE placement.groupid = node.groupid
   AND node.noderole = 'primary'
   AND shardid = (
     SELECT get_shard_id_for_distribution_column('stores', 4)
   );
```

출력에는 작업자 데이터베이스의 호스트와 포트가 포함됩니다.

```
┌─────────┬────────────┬─────────────┬───────────┬──────────┬─────────────┐
│ shardid │ shardstate │ shardlength │ nodename  │ nodeport │ placementid │
├─────────┼────────────┼─────────────┼───────────┼──────────┼─────────────┤
│  102009 │          1 │           0 │ 10.0.0.16 │     5432 │           2 │
└─────────┴────────────┴─────────────┴───────────┴──────────┴─────────────┘
```

## <a name="finding-the-distribution-column-for-a-table"></a>테이블의 배포 열 찾기

하이퍼스케일(Citus)의 각 분산 테이블에는 "배포 열"이 있습니다. (자세한 내용은 [분산 데이터 모델링](concepts-hyperscale-choose-distribution-column.md)을 참조하세요.) 이것이 어떤 열인지 알아야 할 수 있습니다. 예를 들어 테이블을 조인하거나 필터링할 때 "배포 열에 필터 추가"와 같은 힌트가 포함된 오류 메시지가 표시될 수 있습니다.

코디네이터 노드의 `pg_dist_*` 테이블에는 분산 데이터베이스에 대한 다양한 메타데이터가 포함됩니다. 특히 `pg_dist_partition`은 각 테이블의 배포 열에 대한 정보를 저장합니다. 편리한 유틸리티 함수를 사용하여 메타데이터의 하위 수준 세부 정보에서 배포 열 이름을 조회할 수 있습니다. 예제 및 해당 출력은 다음과 같습니다.

``` postgresql
-- create example table

CREATE TABLE products (
  store_id bigint,
  product_id bigint,
  name text,
  price money,

  CONSTRAINT products_pkey PRIMARY KEY (store_id, product_id)
);

-- pick store_id as distribution column

SELECT create_distributed_table('products', 'store_id');

-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

예제 출력:

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ store_id      │
└───────────────┘
```

## <a name="detecting-locks"></a>잠금 검색

이 쿼리는 모든 작업자 노드에서 실행되며 잠금, 열린 시간 및 잘못된 쿼리를 식별합니다.

``` postgresql
SELECT run_command_on_workers($cmd$
  SELECT array_agg(
    blocked_statement || ' $ ' || cur_stmt_blocking_proc
    || ' $ ' || cnt::text || ' $ ' || age
  )
  FROM (
    SELECT blocked_activity.query    AS blocked_statement,
           blocking_activity.query   AS cur_stmt_blocking_proc,
           count(*)                  AS cnt,
           age(now(), min(blocked_activity.query_start)) AS "age"
    FROM pg_catalog.pg_locks         blocked_locks
    JOIN pg_catalog.pg_stat_activity blocked_activity
      ON blocked_activity.pid = blocked_locks.pid
    JOIN pg_catalog.pg_locks         blocking_locks
      ON blocking_locks.locktype = blocked_locks.locktype
     AND blocking_locks.DATABASE IS NOT DISTINCT FROM blocked_locks.DATABASE
     AND blocking_locks.relation IS NOT DISTINCT FROM blocked_locks.relation
     AND blocking_locks.page IS NOT DISTINCT FROM blocked_locks.page
     AND blocking_locks.tuple IS NOT DISTINCT FROM blocked_locks.tuple
     AND blocking_locks.virtualxid IS NOT DISTINCT FROM blocked_locks.virtualxid
     AND blocking_locks.transactionid IS NOT DISTINCT FROM blocked_locks.transactionid
     AND blocking_locks.classid IS NOT DISTINCT FROM blocked_locks.classid
     AND blocking_locks.objid IS NOT DISTINCT FROM blocked_locks.objid
     AND blocking_locks.objsubid IS NOT DISTINCT FROM blocked_locks.objsubid
     AND blocking_locks.pid != blocked_locks.pid
    JOIN pg_catalog.pg_stat_activity blocking_activity ON blocking_activity.pid = blocking_locks.pid
    WHERE NOT blocked_locks.GRANTED
     AND blocking_locks.GRANTED
    GROUP BY blocked_activity.query,
             blocking_activity.query
    ORDER BY 4
  ) a
$cmd$);
```

예제 출력:

```
┌───────────────────────────────────────────────────────────────────────────────────┐
│                               run_command_on_workers                              │
├───────────────────────────────────────────────────────────────────────────────────┤
│ (10.0.0.16,5432,t,"")                                                             │
│ (10.0.0.20,5432,t,"{""update ads_102277 set name = 'new name' where id = 1; $ sel…│
│…ect * from ads_102277 where id = 1 for update; $ 1 $ 00:00:03.729519""}")         │
└───────────────────────────────────────────────────────────────────────────────────┘
```

## <a name="querying-the-size-of-your-shards"></a>분할된 데이터베이스의 크기 쿼리

이 쿼리는 `my_distributed_table`이라고 부르는 지정된 분산 테이블의 모든 분할된 데이터베이스의 크기를 제공합니다.

``` postgresql
SELECT *
FROM run_command_on_shards('my_distributed_table', $cmd$
  SELECT json_build_object(
    'shard_name', '%1$s',
    'size',       pg_size_pretty(pg_table_size('%1$s'))
  );
$cmd$);
```

예제 출력:

```
┌─────────┬─────────┬───────────────────────────────────────────────────────────────────────┐
│ shardid │ success │                                result                                 │
├─────────┼─────────┼───────────────────────────────────────────────────────────────────────┤
│  102008 │ t       │ {"shard_name" : "my_distributed_table_102008", "size" : "2416 kB"}    │
│  102009 │ t       │ {"shard_name" : "my_distributed_table_102009", "size" : "3960 kB"}    │
│  102010 │ t       │ {"shard_name" : "my_distributed_table_102010", "size" : "1624 kB"}    │
│  102011 │ t       │ {"shard_name" : "my_distributed_table_102011", "size" : "4792 kB"}    │
└─────────┴─────────┴───────────────────────────────────────────────────────────────────────┘
```

## <a name="querying-the-size-of-all-distributed-tables"></a>모든 분산 테이블의 크기 쿼리

이 쿼리는 각 분산 테이블의 크기와 해당 인덱스의 크기에 대한 목록을 가져옵니다.

``` postgresql
SELECT
  tablename,
  pg_size_pretty(
    citus_total_relation_size(tablename::text)
  ) AS total_size
FROM pg_tables pt
JOIN pg_dist_partition pp
  ON pt.tablename = pp.logicalrelid::text
WHERE schemaname = 'public';
```

예제 출력:

```
┌───────────────┬────────────┐
│   tablename   │ total_size │
├───────────────┼────────────┤
│ github_users  │ 39 MB      │
│ github_events │ 98 MB      │
└───────────────┴────────────┘
```

분산 테이블 크기를 쿼리하는 다른 하이퍼스케일(Citus) 함수가 있습니다. [테이블 크기 확인](howto-hyperscale-table-size.md)을 참조하세요.

## <a name="identifying-unused-indices"></a>사용하지 않는 인덱스 식별

다음 쿼리는 지정된 분산 테이블(`my_distributed_table`)에 대한 작업자 노드에서 사용하지 않는 인덱스를 식별합니다.

``` postgresql
SELECT *
FROM run_command_on_shards('my_distributed_table', $cmd$
  SELECT array_agg(a) as infos
  FROM (
    SELECT (
      schemaname || '.' || relname || '##' || indexrelname || '##'
                 || pg_size_pretty(pg_relation_size(i.indexrelid))::text
                 || '##' || idx_scan::text
    ) AS a
    FROM  pg_stat_user_indexes ui
    JOIN  pg_index i
    ON    ui.indexrelid = i.indexrelid
    WHERE NOT indisunique
    AND   idx_scan < 50
    AND   pg_relation_size(relid) > 5 * 8192
    AND   (schemaname || '.' || relname)::regclass = '%s'::regclass
    ORDER BY
      pg_relation_size(i.indexrelid) / NULLIF(idx_scan, 0) DESC nulls first,
      pg_relation_size(i.indexrelid) DESC
  ) sub
$cmd$);
```

예제 출력:

```
┌─────────┬─────────┬───────────────────────────────────────────────────────────────────────┐
│ shardid │ success │                            result                                     │
├─────────┼─────────┼───────────────────────────────────────────────────────────────────────┤
│  102008 │ t       │                                                                       │
│  102009 │ t       │ {"public.my_distributed_table_102009##some_index_102009##28 MB##0"}   │
│  102010 │ t       │                                                                       │
│  102011 │ t       │                                                                       │
└─────────┴─────────┴───────────────────────────────────────────────────────────────────────┘
```

## <a name="monitoring-client-connection-count"></a>클라이언트 연결 수 모니터링

다음 쿼리는 코디네이터에서 열린 연결 수를 계산하고 유형별로 그룹화합니다.

``` sql
SELECT state, count(*)
FROM pg_stat_activity
GROUP BY state;
```

예제 출력:

```
┌────────┬───────┐
│ state  │ count │
├────────┼───────┤
│ active │     3 │
│ idle   │     3 │
│ ∅      │     6 │
└────────┴───────┘
```

## <a name="viewing-system-queries"></a>시스템 쿼리 보기

### <a name="active-queries"></a>활성 쿼리

`pg_stat_activity` 보기는 현재 실행 중인 쿼리를 보여 줍니다. 필터링하여 백 엔드의 프로세스 ID와 함께 적극적으로 실행 중인 항목을 찾을 수 있습니다.

```sql
SELECT pid, query, state
  FROM pg_stat_activity
 WHERE state != 'idle';
```

### <a name="why-are-queries-waiting"></a>쿼리가 대기하는 이유

또한 대기 중인 비유휴 쿼리에서 가장 일반적인 이유를 확인하기 위해 쿼리할 수 있습니다. 이러한 이유에 대한 설명은 [PostgreSQL 설명서](https://www.postgresql.org/docs/current/monitoring-stats.html#WAIT-EVENT-TABLE)를 참조하세요.

```sql
SELECT wait_event || ':' || wait_event_type AS type, count(*) AS number_of_occurences
  FROM pg_stat_activity
 WHERE state != 'idle'
GROUP BY wait_event, wait_event_type
ORDER BY number_of_occurences DESC;
```

별도의 쿼리에서 `pg_sleep`을 동시에 실행하는 경우의 예제 출력은 다음과 같습니다.

```
┌─────────────────┬──────────────────────┐
│      type       │ number_of_occurences │
├─────────────────┼──────────────────────┤
│ ∅               │                    1 │
│ PgSleep:Timeout │                    1 │
└─────────────────┴──────────────────────┘
```

## <a name="index-hit-rate"></a>인덱스 적중률

이 쿼리는 모든 노드에서 인덱스 적중률을 제공합니다. 인덱스 적중률은 쿼리할 때 인덱스가 사용되는 빈도를 결정하는 데 유용합니다.
값 95% 이상이 적합합니다.

``` postgresql
-- on coordinator
SELECT 100 * (sum(idx_blks_hit) - sum(idx_blks_read)) / sum(idx_blks_hit) AS index_hit_rate
  FROM pg_statio_user_indexes;

-- on workers
SELECT nodename, result as index_hit_rate
FROM run_command_on_workers($cmd$
  SELECT 100 * (sum(idx_blks_hit) - sum(idx_blks_read)) / sum(idx_blks_hit) AS index_hit_rate
    FROM pg_statio_user_indexes;
$cmd$);
```

예제 출력:

```
┌───────────┬────────────────┐
│ nodename  │ index_hit_rate │
├───────────┼────────────────┤
│ 10.0.0.16 │ 96.0           │
│ 10.0.0.20 │ 98.0           │
└───────────┴────────────────┘
```

## <a name="cache-hit-rate"></a>캐시 적중률

대부분의 애플리케이션은 일반적으로 전체 데이터의 작은 부분에 한 번에 액세스합니다. PostgreSQL은 자주 액세스하는 데이터를 메모리에 저장하여 디스크에서 느리게 읽지 않도록 합니다. [pg_statio_user_tables](https://www.postgresql.org/docs/current/monitoring-stats.html#MONITORING-PG-STATIO-ALL-TABLES-VIEW) 보기에서 해당 통계를 볼 수 있습니다.

중요한 측정값은 메모리 캐시와 워크로드의 디스크에서 제공되는 데이터의 비율입니다.

``` postgresql
-- on coordinator
SELECT
  sum(heap_blks_read) AS heap_read,
  sum(heap_blks_hit)  AS heap_hit,
  100 * sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read)) AS cache_hit_rate
FROM
  pg_statio_user_tables;

-- on workers
SELECT nodename, result as cache_hit_rate
FROM run_command_on_workers($cmd$
  SELECT
    100 * sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read)) AS cache_hit_rate
  FROM
    pg_statio_user_tables;
$cmd$);
```

예제 출력:

```
┌───────────┬──────────┬─────────────────────┐
│ heap_read │ heap_hit │   cache_hit_rate    │
├───────────┼──────────┼─────────────────────┤
│         1 │      132 │ 99.2481203007518796 │
└───────────┴──────────┴─────────────────────┘
```

비율이 99%보다 훨씬 낮은 경우 데이터베이스에 사용할 수 있는 캐시를 늘리는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* 진단에 유용한 다른 [시스템 테이블](reference-hyperscale-metadata.md)에 대해 알아보기
