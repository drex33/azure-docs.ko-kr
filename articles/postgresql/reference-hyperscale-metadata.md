---
title: 시스템 테이블 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 분산 쿼리 실행을 위한 메타데이터
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 8213ca39f85a50d7e5354948467f3fbd66cb4797
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094731"
---
# <a name="system-tables-and-views"></a>시스템 테이블 및 보기

하이퍼스케일(Citus)은 서버 그룹의 분산 데이터에 대한 정보를 포함하는 특수 테이블을 만들고 유지 관리합니다. 코디네이터 노드는 작업자 노드에서 쿼리를 실행하는 방법을 계획할 때 이러한 테이블을 참조합니다.

## <a name="coordinator-metadata"></a>코디네이터 메타데이터

하이퍼스케일(Citus)은 배포 열을 기반으로 각 분산 테이블을 여러 개의 논리 분할로 나눕니다. 그런 다음 코디네이터는 이러한 분할의 상태 및 위치에 대한 통계와 정보를 추적하는 메타데이터 테이블을 유지 관리합니다.

이 섹션에서는 이러한 각 메타데이터 테이블 및 스키마에 대해 설명합니다.
코디네이터 노드에 로그인한 후 SQL을 사용하여 이러한 테이블을 보고 쿼리할 수 있습니다.

> [!NOTE]
>
> 이전 버전의 Citus Engine을 실행하는 하이퍼스케일(Citus) 서버 그룹은 아래 나열된 모든 테이블을 제공하지 않을 수도 있습니다.

### <a name="partition-table"></a>파티션 테이블

pg\_dist\_partition 테이블은 배포되는 데이터베이스의 테이블에 대한 메타데이터를 저장합니다. 또한 배포된 각 테이블에 대해 배포 메서드에 대한 정보와 배포 열에 대한 자세한 정보를 저장합니다.

| 이름         | Type     | Description                                                                                                                                                                                                                                           |
|--------------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid | regclass | 이 행이 해당하는 분산 테이블입니다. 이 값은 pg_class 시스템 카탈로그 테이블의 relfilenode 열을 참조합니다.                                                                                                                   |
| partmethod   | char     | 분할/배포에 사용되는 메서드입니다. 다른 배포 메서드에 해당하는 이 열의 값은 추가: ‘a’, 해시: ‘h’, 참조 테이블: ‘n’입니다.                                                                          |
| partkey      | text     | 열 번호, 형식 및 기타 관련 정보 등의 배포 열에 대한 자세한 정보입니다.                                                                                                                                      |
| colocationid | 정수  | 이 테이블이 속한 공동 배치 그룹입니다. 동일한 그룹의 테이블을 사용하여 다른 최적화 간에 공동 배치 조인과 분산 롤업을 수행할 수 있습니다. 이 값은 pg_dist_colocation 테이블의 colocationid 열을 참조합니다.                      |
| repmodel     | char     | 데이터 복제에 사용되는 메서드입니다. 다른 복제 메서드에 해당하는 이 열의 값은 Citus 문 기반 복제: 'c', postgresql 스트리밍 복제: ‘s’, 2단계 커밋(참조 테이블의 경우): ‘t’입니다. |

```
SELECT * from pg_dist_partition;
 logicalrelid  | partmethod |                                                        partkey                                                         | colocationid | repmodel 
---------------+------------+------------------------------------------------------------------------------------------------------------------------+--------------+----------
 github_events | h          | {VAR :varno 1 :varattno 4 :vartype 20 :vartypmod -1 :varcollid 0 :varlevelsup 0 :varnoold 1 :varoattno 4 :location -1} |            2 | c
 (1 row)
```

### <a name="shard-table"></a>분할 테이블

pg\_dist\_shard 테이블은 테이블의 개별 분할에 대한 메타데이터를 저장합니다. pg_dist_shard에는 속산 분산 테이블 분할에 대한 정보 및 분할의 배포 열에 대한 통계량이 있습니다.
추가 분산 테이블의 경우 이러한 통계는 배포 열의 min/max 값에 해당합니다. 해시 분산 테이블의 경우 해당 분할에 할당된 해시 토큰 범위입니다. 이러한 통계는 SELECT 쿼리 중 관련 없는 분할을 정리하는 데 사용됩니다.

| 이름          | Type     | Description                                                                                                                                                                                  |
|---------------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid  | regclass | 이 행이 해당하는 분산 테이블입니다. 이 값은 pg_class 시스템 카탈로그 테이블의 relfilenode 열을 참조합니다.                                                          |
| shardid       | bigint   | 이 분할에 할당된 고유 식별자입니다.                                                                                                                                           |
| shardstorage  | char     | 분할에 사용되는 스토리지 유형입니다. 다음 표에서는 다양한 스토리지 유형을 설명합니다.                                                                                               |
| shardminvalue | text     | 추가 분산 테이블의 경우 이 분할에 있는 배포 열의 최소값(포함)입니다. 해시 분산 테이블의 경우 해당 분할에 할당된 최소 해시 토큰 값(포함)입니다. |
| shardmaxvalue | text     | 추가 분산 테이블의 경우 이 분할에 있는 배포 열의 최대값(포함)입니다. 해시 분산 테이블의 경우 해당 분할에 할당된 최대 해시 토큰 값(포함)입니다. |

```
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

#### <a name="shard-storage-types"></a>분할 스토리지 유형

pg\_dist\_shard의 shardstorage 열은 분할에 사용된 스토리지 유형을 나타냅니다. 다양한 분할 스토리지 유형에 대한 간략한 개요 및 설명은 아래와 같습니다.

| 스토리지 유형 | Shardstorage 값 | Description                                                                        |
|--------------|--------------------|------------------------------------------------------------------------------------|
| TABLE        | 't'                | 해당 분할이 정규 분산 테이블에 속하는 데이터를 저장함을 나타냅니다.         |
| COLUMNAR     | 'c'                | 분할이 주상 데이터를 저장함을 나타냅니다. (분산 cstore_fdw 테이블에서 사용됨) |
| FOREIGN      | 'f'                | 분할이 외래 데이터를 저장함을 나타냅니다. (분산 file_fdw 테이블에서 사용됨)    |

### <a name="shard-placement-table"></a>분할 배치 테이블

pg\_dist\_placement 테이블은 작업자 노드에서 분할 복제본의 위치를 추적합니다. 특정 노드에 할당된 분할의 각 복제본을 분할 배치라고 합니다. 이 테이블은 각 분할 배치의 상태 및 위치에 대한 정보를 저장합니다.

| 이름        | Type   | Description                                                                                                                               |
|-------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------|
| shardid     | bigint | 이 배치와 연결된 분할 식별자입니다. 이 값은 pg_dist_shard 카탈로그 테이블의 shardid 열을 참조합니다.             |
| shardstate  | int    | 이 배치의 상태를 설명합니다. 다양한 분할 상태에 대해서는 아래 섹션에서 설명합니다.                                         |
| shardlength | bigint | 추가 분산 테이블의 경우 작업자 노드의 분할 배치 크기(바이트)입니다. 해시 분산 테이블의 경우 0입니다.            |
| placementid | bigint | 개별 배치마다 자동으로 생성되는 고유 식별자입니다.                                                                           |
| groupid     | int    | 스트리밍 복제 모델이 사용될 때 하나의 주 서버와 0개 이상의 보조 서버 그룹을 나타냅니다. |

```
SELECT * from pg_dist_placement;
  shardid | shardstate | shardlength | placementid | groupid
 ---------+------------+-------------+-------------+---------
   102008 |          1 |           0 |           1 |       1
   102008 |          1 |           0 |           2 |       2
   102009 |          1 |           0 |           3 |       2
   102009 |          1 |           0 |           4 |       3
   102010 |          1 |           0 |           5 |       3
   102010 |          1 |           0 |           6 |       4
   102011 |          1 |           0 |           7 |       4
```

#### <a name="shard-placement-states"></a>분할 배치 상태

하이퍼스케일(Citus)은 배치별로 분할 상태를 관리합니다. 배치로 인해 시스템이 불일치 상태로 전환되면 Citus는 자동으로 시스템을 사용할 수 없음으로 표시합니다. 배치 상태는 shardstate 열 내의 pg_dist_shard_placement 테이블에 기록됩니다. 다양한 분할 배치 상태의 간략한 개요는 다음과 같습니다.

| 상태 이름 | Shardstate 값 | Description                                                                                                                                                                                                                                                                                                                                                                                                                         |
|------------|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| FINALIZED  | 1                | 새 분할이 만들어지는 상태입니다. 이 상태의 분할 배치는 최신으로 간주되어 쿼리 계획 및 실행에 사용됩니다.                                                                                                                                                                                                                                                                                 |
| INACTIVE   | 3                | 이 상태의 분할 배치는 동일한 분할의 다른 복제본과 동기화되지 않으므로 비활성 상태로 간주됩니다. 상태는 이 배치에 대해 추가, 수정(삽입, 업데이트, 삭제)하거나 DDL 작업이 실패하는 경우 발생할 수 있습니다. 쿼리 계획자는 계획 및 실행 중에 이 상태의 배치를 무시합니다. 사용자는 이러한 분할의 데이터를 최종 복제본과 백그라운드 작업으로 동기화할 수 있습니다. |
| TO_DELETE  | 4                | Citus가 master_apply_delete_command 호출에 응답하여 분할 배치를 삭제하려고 시도했다가 실패하면 해당 배치가 이 상태로 이동됩니다. 그런 다음 사용자는 이러한 분할을 후속 백그라운드 작업으로 삭제할 수 있습니다.                                                                                                                                                                                                              |

### <a name="worker-node-table"></a>작업자 노드 테이블

pg\_dist\_node 테이블에는 클러스터의 작업자 노드에 대한 정보가 포함되어 있습니다.

| 이름             | Type    | Description                                                                                                                                                                                |
|------------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nodeid           | int     | 개별 노드에 대해 자동으로 생성된 식별자입니다.                                                                                                                                          |
| groupid          | int     | 스트리밍 복제 모델이 사용될 때 하나의 주 서버와 0개 이상의 보조 서버 그룹을 나타내는 데 사용되는 식별자입니다. 기본적으로 nodeid와 동일합니다.         |
| nodename         | text    | PostgreSQL 작업자 노드의 호스트 이름 또는 IP 주소입니다.                                                                                                                                     |
| nodeport         | int     | PostgreSQL 작업자 노드가 수신 대기하는 포트 번호입니다.                                                                                                                              |
| noderack         | text    | (선택 사항)작업자 노드에 대한 랙 배치 정보입니다.                                                                                                                                 |
| hasmetadata      | boolean | 내부용으로 예약된 속성입니다.                                                                                                                                                                 |
| isactive         | boolean | 노드가 활성 상태이고 분할 배치를 수락하는지 여부입니다.                                                                                                                                     |
| noderole         | text    | 노드가 주 노드인지 아니면 보조 노드인지 여부입니다.                                                                                                                                                 |
| nodecluster      | text    | 이 노드를 포함하는 클러스터의 이름입니다.                                                                                                                                               |
| shouldhaveshards | boolean | false인 경우, 이미 있는 분할이 공동 배치되지 않는 한 재분산할 때 분할이 노드 밖으로 이동(드레이닝)되거나, 새 분산 테이블의 분할이 노드에 배치되지 않습니다. |

```
SELECT * from pg_dist_node;
 nodeid | groupid | nodename  | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | shouldhaveshards
--------+---------+-----------+----------+----------+-------------+----------+----------+-------------+------------------
      1 |       1 | localhost |    12345 | default  | f           | t        | primary  | default     | t
      2 |       2 | localhost |    12346 | default  | f           | t        | primary  | default     | t
      3 |       3 | localhost |    12347 | default  | f           | t        | primary  | default     | t
(3 rows)
```

### <a name="distributed-object-table"></a>분산 개체 테이블

citus.pg\_dist\_object 테이블에는 코디네이터 노드에 생성되어 작업자 노드에 전파된 유형 및 함수와 같은 개체 목록이 포함됩니다. 관리자가 새 작업자 노드를 클러스터에 추가하면 하이퍼스케일(Citus)은 (개체 종속성을 충족하기에 올바른 순서로) 분산 개체의 복사본을 새 노드에 자동으로 만듭니다.

| 이름                        | Type    | Description                                          |
|-----------------------------|---------|------------------------------------------------------|
| classid                     | oid     | 분산 개체의 클래스입니다.                      |
| objid                       | oid     | 분산 개체의 개체 ID입니다.                  |
| objsubid                    | 정수 | 분산 개체의 개체 하위 ID(예: attnum)입니다. |
| type                        | text    | pg 업그레이드 중에 사용되는 안정적인 주소의 일부입니다.   |
| object_names                | text[]  | pg 업그레이드 중에 사용되는 안정적인 주소의 일부입니다.   |
| object_args                 | text[]  | pg 업그레이드 중에 사용되는 안정적인 주소의 일부입니다.   |
| distribution_argument_index | 정수 | 분산 함수/프로시저에만 유효합니다.      |
| colocationid                | 정수 | 분산 함수/프로시저에만 유효합니다.      |

\"안정적인 주소\"는 특정 서버와 독립적으로 개체를 고유하게 식별합니다. 하이퍼스케일(Citus)은 [pg\_identify\_object\_as\_address()](https://www.postgresql.org/docs/current/functions-info.html#FUNCTIONS-INFO-OBJECT-TABLE) 함수로 만든 안정된 주소를 사용하여 PostgreSQL을 업그레이드하는 동안 개체를 추적합니다.

다음은 `create_distributed_function()` 함수가 `citus.pg_dist_object` 테이블에 항목을 추가하는 방법의 예입니다.

```psql
CREATE TYPE stoplight AS enum ('green', 'yellow', 'red');

CREATE OR REPLACE FUNCTION intersection()
RETURNS stoplight AS $$
DECLARE
        color stoplight;
BEGIN
        SELECT *
          FROM unnest(enum_range(NULL::stoplight)) INTO color
         ORDER BY random() LIMIT 1;
        RETURN color;
END;
$$ LANGUAGE plpgsql VOLATILE;

SELECT create_distributed_function('intersection()');

-- will have two rows, one for the TYPE and one for the FUNCTION
TABLE citus.pg_dist_object;
```

```
-[ RECORD 1 ]---------------+------
classid                     | 1247
objid                       | 16780
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
-[ RECORD 2 ]---------------+------
classid                     | 1255
objid                       | 16788
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
```

### <a name="colocation-group-table"></a>공동 배치 그룹 테이블

pg\_dist\_colocation 테이블에는 함께 배치해야 하는 테이블 또는 [공동 배치된](concepts-hyperscale-colocation.md) 테이블에 대한 정보가 포함되어 있습니다.
두 테이블이 동일한 공동 배치 그룹에 있는 경우 하이퍼스케일(Citus)은 동일한 파티션 값이 있는 분할이 동일한 작업자 노드에 배치되도록 합니다.
공동 배치는 조인 최적화, 특정 분산 롤업 및 외래 키 지원을 가능하도록 합니다. 분할 공동 배치는 분할 수, 복제 요소 및 파티션 열 유형이 모두 두 테이블 간에 일치할 때 유추됩니다. 그러나 원하는 경우 분산 테이블을 만들 때 사용자 지정 공동 배치 그룹을 지정할 수 있습니다.

| 이름                   | Type | Description                                                                   |
|------------------------|------|-------------------------------------------------------------------------------|
| colocationid           | int  | 이 행에 해당하는 공동 배치 그룹의 고유 식별자입니다.          |
| shardcount             | int  | 이 공동 배치 그룹의 모든 테이블에 대한 분할 수입니다.                          |
| replicationfactor      | int  | 이 공동 배치 그룹의 모든 테이블에 대한 복제 요소입니다.                  |
| distributioncolumntype | oid  | 이 공동 배치 그룹의 모든 테이블에 대한 배포 열의 유형입니다. |

```
SELECT * from pg_dist_colocation;
  colocationid | shardcount | replicationfactor | distributioncolumntype 
 --------------+------------+-------------------+------------------------
             2 |         32 |                 2 |                     20
  (1 row)
```

### <a name="rebalancer-strategy-table"></a>재분산 장치 전략 테이블

이 테이블은 [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards)가 분할을 이동할 위치를 결정하는 데 사용할 수 있는 전략을 정의합니다.

| 이름                           | Type    | Description                                                                                                                                       |
|--------------------------------|---------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| default_strategy               | boolean | rebalance_table_shards가 기본적으로 이 전략을 선택해야 하는지 여부입니다. citus_set_default_rebalance_strategy를 사용하여 이 열을 업데이트합니다.             |
| shard_cost_function            | regproc | shardid를 bigint로 가져가고 비용의 개념을 실제 형식으로 반환해야 하는 비용 함수의 식별자입니다.                                |
| node_capacity_function         | regproc | nodeid를 int로 가져가고 노드 용량의 개념을 실제 형식으로 반환해야 하는 용량 함수의 식별자입니다.                          |
| shard_allowed_on_node_function | regproc | shardid bigint 및 nodeidarg int가 지정된 함수의 식별자입니다. Citus가 노드에 분할을 저장할 수 있는지 여부에 대한 부울 값을 반환합니다. |
| default_threshold              | float4  | 노드가 너무 꽉 찼거나 너무 비어 있는 것으로 간주되는 임계값이며 rebalance_table_shads가 언제 분할 이동을 시도해야 하는지 결정합니다.                    |
| minimum_threshold              | float4  | rebalance_table_shards()의 임계값 인수가 너무 낮게 설정되지 않도록 방지하는 보호 기능입니다.                                                  |

하이퍼스케일(Citus) 설치는 테이블에 다음 전략과 함께 제공됩니다.

```postgresql
SELECT * FROM pg_dist_rebalance_strategy;
```

```
-[ RECORD 1 ]-------------------+-----------------------------------
Name                            | by_shard_count
default_strategy                | true
shard_cost_function             | citus_shard_cost_1
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0
minimum_threshold               | 0
-[ RECORD 2 ]-------------------+-----------------------------------
Name                            | by_disk_size
default_strategy                | false
shard_cost_function             | citus_shard_cost_by_disk_size
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0.1
minimum_threshold               | 0.01
```

기본 전략인 `by_shard_count`는 모든 분할에 동일한 비용을 할당합니다. 이를 통해 전체 노드 간에 분할 수가 균등화됩니다. 미리 정의된 다른 전략인 `by_disk_size`는 디스크 크기와 일치하는 각 분할에 바이트 수만큼 비용을 할당하고 여기에 공동 배치된 분할의 비용을 할당합니다. 디스크 크기는 `pg_total_relation_size`를 사용하여 계산되므로 인덱스를 포함합니다. 이 전략은 모든 노드에서 동일한 디스크 공간을 확보하려고 시도합니다. 임계값 0.1에 유의합니다. 디스크 공간의 사소한 차이로 인한 불필요한 분할 이동을 방지합니다.

#### <a name="creating-custom-rebalancer-strategies"></a>사용자 지정 재분산 장치 전략 만들기

다음은 새 분할 재분산 장치 전략 내에서 사용할 수 있고 [citus_add_rebalance_strategy](reference-hyperscale-functions.md#citus_add_rebalance_strategy) 함수를 사용하여 [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table)에 등록되는 함수의 예입니다.

-   호스트 이름 패턴별 노드 용량 예외 설정:

    ```postgresql
    CREATE FUNCTION v2_node_double_capacity(nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename LIKE '%.v2.worker.citusdata.com' THEN 2 ELSE 1 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    ```

-   [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table)에서 측정한 분할로 이동하는 쿼리 수에 따라 재분산:

    ```postgresql
    -- example of shard_cost_function

    CREATE FUNCTION cost_of_shard_by_number_of_queries(shardid bigint)
        RETURNS real AS $$
        SELECT coalesce(sum(calls)::real, 0.001) as shard_total_queries
        FROM citus_stat_statements
        WHERE partition_key is not null
            AND get_shard_id_for_distribution_column('tab', partition_key) = shardid;
    $$ LANGUAGE sql;
    ```

-   노드의 특정 분할(10000) 격리(주소 \'10.0.0.1\'):

    ```postgresql
    -- example of shard_allowed_on_node_function

    CREATE FUNCTION isolate_shard_10000_on_10_0_0_1(shardid bigint, nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN shardid = 10000 ELSE shardid != 10000 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;

    -- The next two definitions are recommended in combination with the above function.
    -- This way the average utilization of nodes is not impacted by the isolated shard.
    CREATE FUNCTION no_capacity_for_10_0_0_1(nodeidarg int)
        RETURNS real AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN 0 ELSE 1 END)::real
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    CREATE FUNCTION no_cost_for_10000(shardid bigint)
        RETURNS real AS $$
        SELECT
            (CASE WHEN shardid = 10000 THEN 0 ELSE 1 END)::real
        $$ LANGUAGE sql;
    ```

### <a name="query-statistics-table"></a>쿼리 통계 테이블

하이퍼스케일(Citus)은 쿼리가 실행되는 방법에 대한 통계와 사용자에 대한 `citus_stat_statements`를 제공합니다. 이는 쿼리 속도에 대한 통계를 추적하는 PostgreSQL의 [pg\_stat\_statements](https://www.postgresql.org/docs/current/static/pgstatstatements.html) 보기와 유사합니다(조인될 수 있음).

이 보기는 다중 테넌트 애플리케이션에서 원래 테넌트에 대한 쿼리를 추적할 수 있으므로 테넌트 격리를 수행할 시기를 결정하는 데 유용합니다.

| 이름          | Type   | Description                                                                      |
|---------------|--------|----------------------------------------------------------------------------------|
| queryid       | bigint | 식별자(pg_stat_statements 조인에 적합)                                   |
| userId        | oid    | 쿼리를 실행한 사용자                                                           |
| dbid          | oid    | 코디네이터의 데이터베이스 인스턴스                                                 |
| Query         | text   | 익명화된 쿼리 문자열                                                          |
| executor      | text   | Citus 실행기 사용: 적응, 실시간, 작업-추적기, 라우터 또는 삽입-선택 |
| partition_key | text   | 라우터 실행 쿼리의 배포 열 값, 그렇지 않으면 NULL               |
| calls         | bigint | 쿼리가 실행된 횟수                                                |

```sql
-- create and populate distributed table
create table foo ( id int );
select create_distributed_table('foo', 'id');
insert into foo select generate_series(1,100);

-- enable stats
-- pg_stat_statements must be in shared_preload libraries
create extension pg_stat_statements;

select count(*) from foo;
select * from foo where id = 42;

select * from citus_stat_statements;
```

결과:

```
-[ RECORD 1 ]-+----------------------------------------------
queryid       | -909556869173432820
userid        | 10
dbid          | 13340
query         | insert into foo select generate_series($1,$2)
executor      | insert-select
partition_key |
calls         | 1
-[ RECORD 2 ]-+----------------------------------------------
queryid       | 3919808845681956665
userid        | 10
dbid          | 13340
query         | select count(*) from foo;
executor      | adaptive
partition_key |
calls         | 1
-[ RECORD 3 ]-+----------------------------------------------
queryid       | 5351346905785208738
userid        | 10
dbid          | 13340
query         | select * from foo where id = $1
executor      | adaptive
partition_key | 42
calls         | 1
```

주의 사항:

-   통계 데이터가 복제되지 않고 데이터베이스 크래시 또는 장애 조치(failover)가 계속됩니다.
-   `pg_stat_statements.max` GUC(기본값 5000)로 설정된 제한된 수의 쿼리를 추적합니다.
-   테이블을 잘라내려면 `citus_stat_statements_reset()` 함수를 사용합니다.

### <a name="distributed-query-activity"></a>분산 쿼리 작업

하이퍼스케일(Citus)은 분산 쿼리 결과를 빌드하기 위해 내부적으로 사용되는 분할 관련 쿼리를 포함하여 클러스터 전체의 쿼리 및 잠금을 지켜보는 특수 보기를 제공합니다.

-   **citus\_dist\_stat\_activity**: 모든 노드에서 실행 중인 분산 쿼리를 표시합니다. `pg_stat_activity`의 초집합 후자가 어디에 있든 사용할 수 있습니다.
-   **citus\_worker\_stat\_activity**: 개별 분할에 대한 조각 쿼리를 포함하여 작업자에 대한 쿼리를 표시합니다.
-   **citus\_lock\_waits**: 클러스터 전체에서 차단된 쿼리입니다.

처음 두 보기에는 [pg\_stat\_activity](https://www.postgresql.org/docs/current/static/monitoring-stats.html#PG-STAT-ACTIVITY-VIEW)의 모든 열과 쿼리를 시작한 작업자의 호스트 호스트/포트 및 클러스터의 코디네이터 노드에 대한 호스트/포트가 포함됩니다.

예를 들어 분산 테이블의 행을 계산한다고 간주합니다.

```postgresql
-- run from worker on localhost:9701

SELECT count(*) FROM users_table;
```

`citus_dist_stat_activity`에 표시되는 쿼리는 다음과 같습니다.

```postgresql
SELECT * FROM citus_dist_stat_activity;

-[ RECORD 1 ]----------+----------------------------------
query_hostname         | localhost
query_hostport         | 9701
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23723
usesysid               | 10
usename                | citus
application\_name      | psql
client\_addr           | 
client\_hostname       | 
client\_port           | -1
backend\_start         | 2018-10-05 13:27:14.419905+03
xact\_start            | 2018-10-05 13:27:16.362887+03
query\_start           | 2018-10-05 13:27:20.682452+03
state\_change          | 2018-10-05 13:27:20.896546+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | SELECT count(*) FROM users_table;
backend\_type          | client backend
```

이 쿼리에는 모든 분할의 정보가 필요합니다. 일부 정보는 `localhost:9700`에 저장된 분할 `users_table_102038`에 있습니다. `citus_worker_stat_activity` 보기를 살펴보면 분할에 액세스하는 쿼리를 확인할 수 있습니다.

```postgresql
SELECT * FROM citus_worker_stat_activity;

-[ RECORD 1 ]----------+-----------------------------------------------------------------------------------------
query_hostname         | localhost
query_hostport         | 9700
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23781
usesysid               | 10
usename                | citus
application\_name      | citus
client\_addr           | ::1
client\_hostname       | 
client\_port           | 51773
backend\_start         | 2018-10-05 13:27:20.75839+03
xact\_start            | 2018-10-05 13:27:20.84112+03
query\_start           | 2018-10-05 13:27:20.867446+03
state\_change          | 2018-10-05 13:27:20.869889+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | COPY (SELECT count(*) AS count FROM users_table_102038 users_table WHERE true) TO STDOUT
backend\_type          | client backend
```

`query` 필드는 계산할 분할에서 복사되는 데이터가 표시됩니다.

> [!NOTE]
> 라우터 쿼리(예: 다중 테넌트 애플리케이션의 단일 테넌트인 `SELECT
> * FROM table WHERE tenant_id = X`')가 트랜잭션 블록 없이 실행되면 citus\_worker\_stat\_activity에서 master\_query\_host\_name 및 master\_query\_host\_port 열은 NULL이 됩니다.

을 사용 하 여 빌드할 수 있는 유용한 쿼리의 예는 `citus_worker_stat_activity` 다음과 같습니다.

```postgresql
-- active queries' wait events on a certain node

SELECT query, wait_event_type, wait_event
  FROM citus_worker_stat_activity
 WHERE query_hostname = 'xxxx' and state='active';

-- active queries' top wait events

SELECT wait_event, wait_event_type, count(*)
  FROM citus_worker_stat_activity
 WHERE state='active'
 GROUP BY wait_event, wait_event_type
 ORDER BY count(*) desc;

-- total internal connections generated per node by Citus

SELECT query_hostname, count(*)
  FROM citus_worker_stat_activity
 GROUP BY query_hostname;

-- total internal active connections generated per node by Citus

SELECT query_hostname, count(*)
  FROM citus_worker_stat_activity
 WHERE state='active'
 GROUP BY query_hostname;
```

다음 뷰는 `citus_lock_waits` 입니다. 작동 방식을 확인 하기 위해 수동으로 잠금 상황을 생성할 수 있습니다. 먼저 코디네이터에서 테스트 테이블을 설정 합니다.

```postgresql
CREATE TABLE numbers AS
  SELECT i, 0 AS j FROM generate_series(1,10) AS i;
SELECT create_distributed_table('numbers', 'i');
```

그런 다음 코디네이터에서 두 세션을 사용하여 다음 문 시퀀스를 실행할 수 있습니다.

```postgresql
-- session 1                           -- session 2
-------------------------------------  -------------------------------------
BEGIN;
UPDATE numbers SET j = 2 WHERE i = 1;
                                       BEGIN;
                                       UPDATE numbers SET j = 3 WHERE i = 1;
                                       -- (this blocks)
```

`citus_lock_waits` 보기는 해당 상황을 보여 줍니다.

```postgresql
SELECT * FROM citus_lock_waits;

-[ RECORD 1 ]-------------------------+----------------------------------------
waiting_pid                           | 88624
blocking_pid                          | 88615
blocked_statement                     | UPDATE numbers SET j = 3 WHERE i = 1;
current_statement_in_blocking_process | UPDATE numbers SET j = 2 WHERE i = 1;
waiting_node_id                       | 0
blocking_node_id                      | 0
waiting_node_name                     | coordinator_host
blocking_node_name                    | coordinator_host
waiting_node_port                     | 5432
blocking_node_port                    | 5432
```

이 예제에서 쿼리는 코디네이터에서 시작되었지만 보기에는 작업자에서 시작되는 쿼리 간 잠금도 나열할 수 있습니다(예: 하이퍼스케일(Citus) MX로 실행).

## <a name="next-steps"></a>다음 단계

* [하이퍼스케일(Citus) 함수](reference-hyperscale-functions.md)에서 시스템 테이블을 변경하는 방법 알아보기
* [노드 및 테이블](concepts-hyperscale-nodes.md)의 개념 검토
