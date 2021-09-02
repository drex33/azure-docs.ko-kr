---
title: SQL 함수 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 하이퍼스케일(Citus) SQL API의 함수
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 04/07/2021
ms.openlocfilehash: 65288730cafaa39507eeab4ed2e3d29267080262
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123310484"
---
# <a name="functions-in-the-hyperscale-citus-sql-api"></a>하이퍼스케일(Citus) SQL API의 함수

이 섹션에서는 하이퍼스케일(Citus)에서 제공하는 사용자 정의 함수에 대한 참조 정보를 제공합니다. 이러한 함수는 하이퍼스케일(Citus)에 분산된 기능을 제공하는 데 도움이 됩니다.

> [!NOTE]
>
> 이전 버전의 Citus Engine을 실행하는 하이퍼스케일(Citus) 서버 그룹은 아래 나열된 모든 함수를 제공하지 않을 수 있습니다.

## <a name="table-and-shard-ddl"></a>테이블 및 분할 DDL

### <a name="create_distributed_table"></a>create\_distributed\_table

create\_distributed\_table() 함수는 분산 테이블을 정의하고 해시 분산 테이블인 경우 분할을 만드는 데 사용됩니다. 이 함수는 테이블 이름, 배포 열 및 선택적 배포 방법을 가져와 적절한 메타데이터를 삽입하여 테이블을 분산됨으로 표시합니다. 배포 방법이 지정되지 않은 경우 함수는 기본적으로 '해시' 배포로 설정됩니다. 테이블이 해시 분산된 경우 함수는 분할 수 및 분할 복제 요소 구성 값을 기반으로 작업자 분할도 만듭니다. 테이블에 행이 포함된 경우 작업자 노드에 자동으로 분산됩니다.

이 함수는 master\_create\_distributed\_table() 다음에 master\_create\_worker\_shards() 사용을 대체합니다.

#### <a name="arguments"></a>인수

**table\_name:** 분산해야 하는 테이블의 이름입니다.

**distribution\_column:** 테이블이 배포되는 열입니다.

**distribution\_type:** (선택 사항) 테이블이 배포되는 방법입니다. 허용되는 값은 추가 또는 해시이며 기본값은 '해시'입니다.

**colocate\_with:** (선택 사항) 다른 테이블의 공동 배치 그룹에 현재 테이블을 포함합니다. 기본적으로 테이블은 동일한 유형의 열에 의해 분산되고 동일한 분할 수를 가지며 동일한 복제 계수를 갖는 경우 함께 배치됩니다. `colocate_with`에 사용할 수 있는 값은 `default`, 새 공동 배치 그룹을 시작하기 위한 `none` 또는 해당 테이블과 공동 배치할 다른 테이블의 이름입니다.  ([테이블 공동 배치](concepts-hyperscale-colocation.md)를 참조하세요.)

기본값 `colocate_with`는 암시적 공동 배치 작업을 수행합니다. [공동 배치](concepts-hyperscale-colocation.md)는 테이블이 관련되거나 조인될 때 매우 유용할 수 있습니다.  그러나 두 테이블이 관련이 없지만 배포 열에 동일한 데이터 유형을 사용하는 경우 실수로 두 테이블을 함께 배치하면 [분할 재조정](howto-hyperscale-scale-rebalance.md) 중에 성능이 저하될 수 있습니다.  테이블 분할은 \"cascade\"에서 불필요하게 함께 이동됩니다.

새 분산 테이블이 다른 테이블과 관련이 없는 경우 `colocate_with => 'none'`을 지정하는 것이 가장 좋습니다.

#### <a name="return-value"></a>반환 값

해당 없음

#### <a name="example"></a>예

이 예는 github\_events 테이블이 repo\_id 열의 해시로 분산되어야 함을 데이터베이스에 알립니다.

```postgresql
SELECT create_distributed_table('github_events', 'repo_id');

-- alternatively, to be more explicit:
SELECT create_distributed_table('github_events', 'repo_id',
                                colocate_with => 'github_repo');
```

### <a name="create_reference_table"></a>create\_reference\_table

create\_reference\_table() 함수는 작은 참조 또는 차원 테이블을 정의하는 데 사용됩니다. 이 함수는 테이블 이름을 사용하고 모든 작업자 노드에 복제된 분할이 하나뿐인 분산 테이블을 만듭니다.

#### <a name="arguments"></a>인수

**table\_name:** 분산해야 하는 작은 차원 또는 참조 테이블의 이름입니다.

#### <a name="return-value"></a>반환 값

해당 없음

#### <a name="example"></a>예

이 예에서는 국가 테이블을 참조 테이블로 정의해야 함을 데이터베이스에 알립니다.

```postgresql
SELECT create_reference_table('nation');
```

### <a name="upgrade_to_reference_table"></a>upgrade\_to\_reference\_table

upgrade\_to\_reference\_table() 함수는 분할 수가 1인 기존 분산 테이블을 가져와서 인식된 참조 테이블로 업그레이드합니다. 이 함수를 호출하면 테이블이 [create_reference_table](#create_reference_table)을 사용하여 만들어진 것처럼 됩니다.

#### <a name="arguments"></a>인수

**table\_name:** 참조 테이블로 배포할 분산 테이블 이름(분할 수 = 1)입니다.

#### <a name="return-value"></a>반환 값

해당 없음

#### <a name="example"></a>예

이 예에서는 국가 테이블을 참조 테이블로 정의해야 함을 데이터베이스에 알립니다.

```postgresql
SELECT upgrade_to_reference_table('nation');
```

### <a name="mark_tables_colocated"></a>mark\_tables\_colocated

mark\_tables\_colocated() 함수는 분산 테이블(원본)과 기타 목록(대상)을 사용하여 대상을 원본과 동일한 공동 배치 그룹에 넣습니다. 원본이 아직 그룹에 없는 경우 이 함수는 원본과 대상을 그룹에 할당합니다.

[create_distributed_table](#create_distributed_table)의 `colocate_with` 매개 변수를 통해 테이블 배포 시 테이블 공동 배치를 수행해야 하지만, 필요한 경우 `mark_tables_colocated`가 나중에 처리할 수 있습니다.

#### <a name="arguments"></a>인수

**source\_table\_name:** 대상이 일치하도록 할당될 공동 배치 그룹이 있는 분산 테이블의 이름입니다.

**target\_table\_names:** 분산된 대상 테이블의 이름 배열로, 비어 있지 않아야 합니다. 이러한 분산 테이블은 다음의 원본 테이블과 일치해야 합니다.

> -   배포 방법
> -   배포 열 유형
> -   복제 유형
> -   분할 수

위의 어느 것도 적용되지 않으면 하이퍼스케일(Citus)에서 오류가 발생합니다. 예를 들어, 배포 열 유형이 서로 다른 `apples` 및 `oranges` 테이블을 공동 배치하려고 하면 다음과 같은 결과가 발생합니다.

```
ERROR:  XX000: cannot colocate tables apples and oranges
DETAIL:  Distribution column types don't match for apples and oranges.
```

#### <a name="return-value"></a>반환 값

해당 없음

#### <a name="example"></a>예

이 예에서는 `products` 및 `line_items`를 `stores`와 동일한 공동 배치 그룹에 배치합니다. 이 예에서는 이러한 테이블이 모두 일치하는 유형(대부분 \"store id\")의 열에 분산되어 있다고 가정합니다.

```postgresql
SELECT mark_tables_colocated('stores', ARRAY['products', 'line_items']);
```

### <a name="create_distributed_function"></a>create\_distributed\_function

코디네이터 노드에서 작업자로 함수를 전파하고 분산 실행을 위해 표시합니다. 코디네이터에서 배포 함수가 호출되면 하이퍼스케일(Citus)은 \"배포 인수\"의 값을 사용하여 함수를 실행할 작업자 노드를 선택합니다. 작업자에 대해 함수를 실행하면 병렬 처리가 향상되고 대기 시간을 줄이기 위해 코드가 분할 내 데이터에 더 가깝게 가져올 수 있습니다.

Postgres 검색 경로는 분산 함수 실행 중에 코디네이터에서 작업자로 전파되지 않으므로 분산 함수 코드는 데이터베이스 개체의 이름을 완전히 한정해야 합니다. 또한 함수에서 발생하는 알림은 사용자에게 표시되지 않습니다.

#### <a name="arguments"></a>인수

**function\_name:** 배포되는 함수의 이름입니다. 여러 함수가 PostgreSQL에서 동일한 이름을 가질 수 있으므로 이름에는 함수의 매개 변수 형식이 괄호 안에 포함되어야 합니다. 예를 들어, `'foo(int)'`는 `'foo(int, text)'`와 다릅니다.

**distribution\_arg\_name:** (선택 사항) 배포할 인수 이름입니다. 편의를 위해(또는 함수 인수에 이름이 없는 경우) `'$1'`과 같은 위치 자리 표시자가 허용됩니다. 이 매개 변수를 지정하지 않으면 `function_name`이라는 함수는 작업자에 대해서만 만들어집니다. 나중에 작업자 노드가 추가되면 함수도 자동으로 만들어집니다.

**colocate\_with:** (선택 사항) 분산 함수가 분산 테이블(또는 보다 일반적으로 공동 배치 그룹)을 읽거나 쓸 때 `colocate_with` 매개 변수를 사용하여 해당 테이블의 이름을 지정해야 합니다. 그런 다음, 각 함수 호출은 관련 분할이 포함된 작업자 노드에서 실행됩니다.

#### <a name="return-value"></a>반환 값

해당 없음

#### <a name="example"></a>예

```postgresql
-- an example function which updates a hypothetical
-- event_responses table which itself is distributed by event_id
CREATE OR REPLACE FUNCTION
  register_for_event(p_event_id int, p_user_id int)
RETURNS void LANGUAGE plpgsql AS $fn$
BEGIN
  INSERT INTO event_responses VALUES ($1, $2, 'yes')
  ON CONFLICT (event_id, user_id)
  DO UPDATE SET response = EXCLUDED.response;
END;
$fn$;

-- distribute the function to workers, using the p_event_id argument
-- to determine which shard each invocation affects, and explicitly
-- colocating with event_responses which the function updates
SELECT create_distributed_function(
  'register_for_event(int, int)', 'p_event_id',
  colocate_with := 'event_responses'
);
```

### <a name="alter_columnar_table_set"></a>alter_columnar_table_set

arter_columnar_table_set() 함수는 [열 형식 테이블](concepts-hyperscale-columnar.md)의 설정을 변경합니다. 열 형식이 아닌 테이블에서 이 함수를 호출하면 오류가 발생합니다. 테이블 이름을 제외한 모든 인수는 선택 사항입니다.

모든 열 형식 테이블의 현재 옵션을 보려면 다음 테이블을 참조하세요.

```postgresql
SELECT * FROM columnar.options;
```

새로 만든 테이블의 열 형식 설정 기본값은 다음 GUC로 재정의할 수 있습니다.

* columnar.compression
* columnar.compression_level
* columnar.stripe_row_count
* columnar.chunk_row_count

#### <a name="arguments"></a>인수

**table_name:** 열 형식 테이블의 이름입니다.

**chunk_row_count:** (선택 사항) 새로 삽입된 데이터의 청크당 최대 행 수입니다. 기존 데이터 청크는 변경되지 않으며 이 최댓값보다 많은 행을 가질 수 있습니다. 기본값은 10000입니다.

**stripe_row_count:** (선택 사항) 새로 삽입된 데이터의 스트라이프당 최대 행 수입니다. 기존 데이터 스트라이프는 변경되지 않으며 이 최댓값보다 많은 행을 가질 수 있습니다. 기본값은 150000입니다.

**compression:** (선택 사항) `[none|pglz|zstd|lz4|lz4hc]` 새로 삽입된 데이터의 압축 유형입니다. 기존 데이터는 다시 압축되거나 압축 해제되지 않습니다. 기본 및 제안된 값은 zstd입니다(지원이 컴파일된 경우).

**compression_level:** (선택 사항) 유효한 설정은 1부터 19까지입니다. 압축 방법이 선택한 수준을 지원하지 않는 경우 가장 가까운 수준이 선택됩니다.

#### <a name="return-value"></a>반환 값

해당 없음

#### <a name="example"></a>예

```postgresql
SELECT alter_columnar_table_set(
  'my_columnar_table',
  compression => 'none',
  stripe_row_count => 10000);
```

## <a name="metadata--configuration-information"></a>메타데이터 / 구성 정보

### <a name="master_get_table_metadata"></a>master\_get\_table\_metadata

master\_get\_table\_metadata() 함수를 사용하여 분산 테이블의 배포 관련 메타데이터를 반환할 수 있습니다. 이 메타데이터에는 테이블의 관계 ID, 스토리지 유형, 배포 방법, 배포 열, 복제 수, 최대 분할 크기 및 분할 배치 정책이 포함됩니다. 내부적으로 이 함수는 하이퍼스케일(Citus) 메타데이터 테이블을 쿼리하여 필요한 정보를 가져오고 이를 튜플에 연결한 후 사용자에게 반환합니다.

#### <a name="arguments"></a>인수

**table\_name:** 메타데이터를 가져오려는 분산 테이블의 이름입니다.

#### <a name="return-value"></a>반환 값

튜플에는 다음 정보가 포함되어 있습니다.

**logical\_relid:** 분산 테이블의 Oid입니다. 이는 pg\_class 시스템 카탈로그 테이블의 relfilenode 열을 참조합니다.

**part\_storage\_type:** 테이블에 사용되는 스토리지 유형입니다. 't'(표준 테이블), 'f'(외래 테이블) 또는 'c'(열 형식 테이블)일 수 있습니다.

**part\_method:** 테이블에 사용되는 배포 방법입니다. 'a'(추가) 또는 'h'(해시)일 수 있습니다.

**part\_key:** 테이블의 배포 열입니다.

**part\_replica\_count:** 현재 분할 복제 수입니다.

**part\_max\_size:** 현재 최대 분할 크기 (바이트)입니다.

**part\_placement\_policy:** 테이블의 분할을 배치하는 데 사용되는 분할 배치 정책입니다. 1(로컬 노드 우선) 또는 2(라운드 로빈)일 수 있습니다.

#### <a name="example"></a>예

아래 예는 github\_events 테이블에 대한 테이블 메타데이터를 가져와서 표시합니다.

```postgresql
SELECT * from master_get_table_metadata('github_events');
 logical_relid | part_storage_type | part_method | part_key | part_replica_count | part_max_size | part_placement_policy 
---------------+-------------------+-------------+----------+--------------------+---------------+-----------------------
         24180 | t                 | h           | repo_id  |                  2 |    1073741824 |                     2
(1 row)
```

### <a name="get_shard_id_for_distribution_column"></a>get\_shard\_id\_for\_distribution\_column

하이퍼스케일(Citus)은 행의 배포 열 값과 테이블의 배포 방법을 기반으로 배포 테이블의 모든 행을 분할에 할당합니다. 대부분의 경우, 정확한 매핑은 데이터베이스 관리자가 무시할 수 있는 낮은 수준의 세부 정보입니다. 그러나 수동 데이터베이스 유지 관리 작업을 위해 또는 단순히 호기심을 충족시키기 위해 행의 분할을 결정하는 것이 유용할 수 있습니다. `get_shard_id_for_distribution_column` 함수는 해시 분산, 범위 분산 및 참조 테이블에 대해 이 정보를 제공합니다. 추가 배포에는 작동하지 않습니다.

#### <a name="arguments"></a>인수

**table\_name:** 분산 테이블입니다.

**distribution\_value:** 배포 열의 값입니다.

#### <a name="return-value"></a>반환 값

분할 ID 하이퍼스케일(Citus)은 주어진 테이블의 배포 열 값과 연결됩니다.

#### <a name="example"></a>예

```postgresql
SELECT get_shard_id_for_distribution_column('my_table', 4);

 get_shard_id_for_distribution_column
--------------------------------------
                               540007
(1 row)
```

### <a name="column_to_column_name"></a>column\_to\_column\_name

`pg_dist_partition`의 `partkey` 열을 텍스트 열 이름으로 변환합니다. 변환은 배포 테이블의 배포 열을 결정하는 데 유용합니다.

자세한 내용은 [배포 열 선택](concepts-hyperscale-choose-distribution-column.md)을 참조하세요.

#### <a name="arguments"></a>인수

**table\_name:** 분산 테이블입니다.

**column\_var\_text:** `pg_dist_partition` 테이블의 `partkey` 값입니다.

#### <a name="return-value"></a>반환 값

`table_name`의 배포 열 이름입니다.

#### <a name="example"></a>예

```postgresql
-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

출력:

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ company_id    │
└───────────────┘
```

### <a name="citus_relation_size"></a>citus\_relation\_size

지정된 분산 테이블의 모든 분할에서 사용하는 디스크 공간을 가져옵니다.
디스크 공간에는 \"메인 포크\"의 크기가 포함되지만, 분할에 대한 가시성 맵과 여유 공간 맵은 제외됩니다.

#### <a name="arguments"></a>인수

**logicalrelid:** 분산 테이블의 이름입니다.

#### <a name="return-value"></a>반환 값

bigint의 크기(바이트)입니다.

#### <a name="example"></a>예

```postgresql
SELECT pg_size_pretty(citus_relation_size('github_events'));
```

```
pg_size_pretty
--------------
23 MB
```

### <a name="citus_table_size"></a>citus\_table\_size

인덱스(TOAST, 여유 공간 맵 및 가시성 맵 포함)를 제외하고 지정된 분산 테이블의 모든 분할에서 사용되는 디스크 공간을 가져옵니다.

#### <a name="arguments"></a>인수

**logicalrelid:** 분산 테이블의 이름입니다.

#### <a name="return-value"></a>반환 값

bigint의 크기(바이트)입니다.

#### <a name="example"></a>예

```postgresql
SELECT pg_size_pretty(citus_table_size('github_events'));
```

```
pg_size_pretty
--------------
37 MB
```

### <a name="citus_total_relation_size"></a>citus\_total\_relation\_size

모든 인덱스 및 TOAST 데이터를 포함하여 지정된 분산 테이블의 모든 분할에서 사용되는 총 디스크 공간을 가져옵니다.

#### <a name="arguments"></a>인수

**logicalrelid:** 분산 테이블의 이름입니다.

#### <a name="return-value"></a>반환 값

bigint의 크기(바이트)입니다.

#### <a name="example"></a>예

```postgresql
SELECT pg_size_pretty(citus_total_relation_size('github_events'));
```

```
pg_size_pretty
--------------
73 MB
```

### <a name="citus_stat_statements_reset"></a>citus\_stat\_statements\_reset

[citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table)에서 모든 행을 제거합니다.
이 함수는 `pg_stat_statements_reset()`과 별개로 작동합니다. 모든 통계를 다시 설정하려면 두 함수 모두 호출합니다.

#### <a name="arguments"></a>인수

해당 없음

#### <a name="return-value"></a>반환 값

없음

## <a name="server-group-management-and-repair"></a>서버 그룹 관리 및 복구

### <a name="master_copy_shard_placement"></a>master\_copy\_shard\_placement

수정 명령 또는 DDL 작업 중에 분할 배치가 업데이트되지 않으면 비활성으로 표시됩니다. 그런 다음, master\_copy\_shard\_placement 함수를 호출하여 정상 배치의 데이터를 사용하여 비활성 분할 배치를 복구할 수 있습니다.

분할을 복구하기 위해 이 함수는 먼저 비정상 분할 배치를 삭제하고 코디네이터의 스키마를 사용하여 다시 만듭니다. 분할 배치가 만들어지면 함수는 정상 배치의 데이터를 복사하고 메타데이터를 업데이트하여 새 분할 배치를 정상으로 표시합니다. 이 함수를 사용하면 복구 중 동시 수정으로부터 분할을 보호할 수 있습니다.

#### <a name="arguments"></a>인수

**shard\_id:** 복구할 분할의 ID입니다.

**source\_node\_name:** 정상 분할 배치가 있는 노드의 DNS 이름입니다(\"원본\" 노드).

**source\_node\_port:** 데이터베이스 서버가 수신 대기 중인 원본 작업자 노드의 포트입니다.

**target\_node\_name:** 잘못된 분할 배치가 있는 노드의 DNS 이름입니다(\"대상\" 노드).

**source\_node\_port:** 데이터베이스 서버가 수신 중인 대상 작업자 노드의 포트입니다.

#### <a name="return-value"></a>반환 값

해당 없음

#### <a name="example"></a>예

아래 예는 포트 5432의 'bad\_host'에서 실행되는 데이터베이스 서버에 있는 분할 12345의 비활성 분할 배치를 복구합니다. 이를 복구하기 위해 포트의 'good\_host'에서 실행 중인 서버에 있는 정상 분할 배치의 데이터를 사용합니다.
5432.

```postgresql
SELECT master_copy_shard_placement(12345, 'good_host', 5432, 'bad_host', 5432);
```

### <a name="master_move_shard_placement"></a>master\_move\_shard\_placement

이 함수는 주어진 분할(및 공동 배치된 분할)을 한 노드에서 다른 노드로 이동합니다. 일반적으로 데이터베이스 관리자가 직접 호출하지 않고 분할 재조정 중에 간접적으로 사용됩니다.

데이터를 이동하는 방법에는 차단 또는 비 차단의 두 가지가 있습니다. 차단 방식은 이동 중에 분할에 대한 모든 수정이 일시 중지됨을 의미합니다.
분할 쓰기 차단을 방지하는 두 번째 방법은 Postgres 10 논리적 복제를 사용하는 것입니다.

이동 작업이 성공하면 소스 노드의 분할이 삭제됩니다. 어느 지점에서든 이동이 실패하면 이 함수는 오류를 throw하고 원본 및 대상 노드를 변경하지 않고 그대로 둡니다.

#### <a name="arguments"></a>인수

**shard\_id:** 이동할 분할의 ID입니다.

**source\_node\_name:** 정상 분할 배치가 있는 노드의 DNS 이름입니다(\"원본\" 노드).

**source\_node\_port:** 데이터베이스 서버가 수신 대기 중인 원본 작업자 노드의 포트입니다.

**target\_node\_name:** 잘못된 분할 배치가 있는 노드의 DNS 이름입니다(\"대상\" 노드).

**source\_node\_port:** 데이터베이스 서버가 수신 중인 대상 작업자 노드의 포트입니다.

**shard\_transfer\_mode:** (선택 사항) PostgreSQL 논리적 복제를 사용할지 또는 교차 작업자 COPY 명령을 사용할지 여부와 관계없이 복제 방법을 지정합니다. 가능한 값은 다음과 같습니다.

> -   `auto`: 논리적 복제가 가능한 경우 복제본 ID가 필요하고 그렇지 않으면 레거시 동작을 사용합니다(예: 분할 복구, PostgreSQL 9.6). 이것은 기본값입니다.
> -   `force_logical`: 테이블에 복제본 ID가 없는 경우에도 논리적 복제를 사용합니다. 테이블에 대한 동시 업데이트/삭제 문은 복제 중에 실패합니다.
> -   `block_writes`: 기본 키 또는 복제본 ID가 없는 테이블에는 COPY(쓰기 차단)를 사용합니다.

#### <a name="return-value"></a>반환 값

해당 없음

#### <a name="example"></a>예

```postgresql
SELECT master_move_shard_placement(12345, 'from_host', 5432, 'to_host', 5432);
```

### <a name="rebalance_table_shards"></a>rebalance\_table\_shards

rebalance\_table\_ shards() 함수는 주어진 테이블의 분할을 이동하여 작업자 간에 균등하게 분산되도록 합니다. 이 함수는 먼저 서버 그룹이 주어진 임계 값 내에서 균형을 이루도록 하기 위해 필요한 이동 목록을 계산합니다. 그런 다음, 분할 배치를 원본 노드에서 대상 노드로 하나씩 이동하고 해당 분할 메타데이터를 업데이트하여 이동을 반영합니다.

모든 분할은 분할이 \"균등하게 분산\"되는지 여부를 판단할 때 비용이 할당됩니다. 기본적으로 각 분할의 비용(값 1)이 동일하므로, 작업자 간에 비용을 균등하게 배포하는 것은 각 분할의 수를 균일화하는 것과 같습니다. 고정 비용 전략은 \"by\_shard\_count\"라고 하며 기본 재조정 전략입니다.

기본 전략은 다음과 같은 경우에 적합합니다.

*  분할의 크기가 거의 동일한 경우
*  분할이 거의 동일한 양의 트래픽을 수신하는 경우
*  작업자 노드의 크기/형식이 모두 동일한 경우
*  분할이 특정 작업자에게 고정되지 않은 경우

이러한 가정 중 하나라도 충족되지 않으면 기본 재조정으로 인해 계획이 잘못될 수 있습니다. 이 경우 `rebalance_strategy` 매개 변수를 사용하여 전략을 사용자 지정할 수 있습니다.

rebalance\_table\_shards를 실행하기 전에 [get_rebalance_table_shards_plan](#get_rebalance_table_shards_plan)을 호출하여 수행할 작업을 보고 확인하는 것이 좋습니다.

#### <a name="arguments"></a>인수

**table\_name:** (선택 사항) 분할을 재조정해야 하는 테이블의 이름입니다. Null인 경우 모든 기존 공동 배치 그룹의 균형을 재조정합니다.

**threshold:** (선택 사항) 0.0에서 1.0 사이의 부동 숫자로 평균 사용률과 노드 사용률의 최대 차이 비율을 나타냅니다. 예를 들어, 0.1을 지정하면 분할 리밸런서가 동일한 분할 수 ±10%를 유지하도록 모든 노드의 균형을 조정합니다.
특히, 분할 리밸런서는 모든 작업자 노드의 사용률을 (1-임계값) \* average\_utilization\... (1
+ 임계값) \* average\_utilization 범위로 수렴하려 할 것입니다.

**max\_shard\_moves:** (선택 사항) 이동할 최대 분할 수입니다.

**excluded\_shard\_list:** (선택 사항) 리밸런스 작업 중 이동하지 않아야 하는 분할의 식별자입니다.

**shard\_transfer\_mode:** (선택 사항) PostgreSQL 논리적 복제를 사용할지 또는 교차 작업자 COPY 명령을 사용할지 여부와 관계없이 복제 방법을 지정합니다. 가능한 값은 다음과 같습니다.

> -   `auto`: 논리적 복제가 가능한 경우 복제본 ID가 필요하고 그렇지 않으면 레거시 동작을 사용합니다(예: 분할 복구, PostgreSQL 9.6). 이것은 기본값입니다.
> -   `force_logical`: 테이블에 복제본 ID가 없는 경우에도 논리적 복제를 사용합니다. 테이블에 대한 동시 업데이트/삭제 문은 복제 중에 실패합니다.
> -   `block_writes`: 기본 키 또는 복제본 ID가 없는 테이블에는 COPY(쓰기 차단)를 사용합니다.

**drain\_only:** (선택 사항) true인 경우 [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table)에서 `shouldhaveshards`가 false로 설정된 작업자 노드의 분할을 다른 곳으로 이동합니다. 다른 분할은 이동하지 마세요.

**rebalance\_strategy:** (선택 사항) [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table)의 전략 이름입니다.
이 인수가 생략되면 함수는 테이블에 표시된 대로 기본 전략을 선택합니다.

#### <a name="return-value"></a>반환 값

해당 없음

#### <a name="example"></a>예

아래 예는 기본 임계값 내에서 github\_events 테이블의 분할 재조정을 시도합니다.

```postgresql
SELECT rebalance_table_shards('github_events');
```

이 예 사용에서는 ID 1과 2와 함께 분할을 이동하지 않고 github\_events 테이블의 재조정을 시도합니다.

```postgresql
SELECT rebalance_table_shards('github_events', excluded_shard_list:='{1,2}');
```

### <a name="get_rebalance_table_shards_plan"></a>get\_rebalance\_table\_shards\_plan

[rebalance_table_shards](#rebalance_table_shards)의 계획된 분할 이동을 수행하지 않고 출력합니다.
가능성은 낮지만 get\_rebalance\_table\_shards\_plan은 동일한 인수를 가진 rebalance\_table\_shards 호출이 수행하는 것과 약간 다른 계획을 출력할 수 있습니다. 이 둘은 동시에 실행되지 않기 때문에 서버 그룹(\-예: 디스크 공간\-)에 대한 팩트가 호출마다 다를 수 있습니다.

#### <a name="arguments"></a>인수

rebalance\_table\_shards와 동일한 인수: relation, threshold, max\_shard\_moves, excluded\_shard\_list 및 drain\_only. 인수의 의미는 해당 함수의 설명서를 참조하세요.

#### <a name="return-value"></a>반환 값

튜플은 다음 열을 포함합니다.

-   **table\_name**: 분할이 이동할 테이블
-   **shardid**: 의심스러운 분할
-   **shard\_size**: 크기(바이트)
-   **sourcename**: 원본 노드의 호스트 이름
-   **sourceport**: 원본 노드의 포트
-   **targetname**: 대상 노드의 호스트 이름
-   **targetport**: 대상 노드의 포트

### <a name="get_rebalance_progress"></a>get\_rebalance\_progress

분할 재조정이 시작되면 `get_rebalance_progress()` 함수는 관련된 모든 분할의 진행 상황을 나열합니다. `rebalance_table_shards()`에서 계획되고 실행된 이동을 모니터링합니다.

#### <a name="arguments"></a>인수

해당 없음

#### <a name="return-value"></a>반환 값

튜플은 다음 열을 포함합니다.

-   **sessionid**: 재조정 모니터의 Postgres PID
-   **table\_name**: 분할이 이동하는 테이블
-   **shardid**: 의심스러운 분할
-   **shard\_size**: 크기(바이트)
-   **sourcename**: 원본 노드의 호스트 이름
-   **sourceport**: 원본 노드의 포트
-   **targetname**: 대상 노드의 호스트 이름
-   **targetport**: 대상 노드의 포트
-   **progress**: 0 = 이동 대기 중, 1 = 이동 중, 2 = 완료

#### <a name="example"></a>예

```sql
SELECT * FROM get_rebalance_progress();
```

```
┌───────────┬────────────┬─────────┬────────────┬───────────────┬────────────┬───────────────┬────────────┬──────────┐
│ sessionid │ table_name │ shardid │ shard_size │  sourcename   │ sourceport │  targetname   │ targetport │ progress │
├───────────┼────────────┼─────────┼────────────┼───────────────┼────────────┼───────────────┼────────────┼──────────┤
│      7083 │ foo        │  102008 │    1204224 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102009 │    1802240 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102018 │     614400 │ n2.foobar.com │       5432 │ n4.foobar.com │       5432 │        1 │
│      7083 │ foo        │  102019 │       8192 │ n3.foobar.com │       5432 │ n4.foobar.com │       5432 │        2 │
└───────────┴────────────┴─────────┴────────────┴───────────────┴────────────┴───────────────┴────────────┴──────────┘
```

### <a name="citus_add_rebalance_strategy"></a>citus\_add\_rebalance\_strategy

[pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table)에 행을 추가합니다.

#### <a name="arguments"></a>인수

이러한 인수에 대한 자세한 내용은 `pg_dist_rebalance_strategy`의 해당 열 값을 참조하세요.

**name:** 새 전략의 식별자

**shard\_cost\_function:** 각 분할의 \"비용\"을 결정하는 데 사용되는 함수를 식별합니다.

**node\_capacity\_function:** 노드 용량을 측정하는 함수를 식별

**shard\_allowed\_on\_node\_function:** 어떤 노드에 어떤 분할을 배치할지 결정하는 함수를 식별

**default\_threshold:** 누적 분할 비용이 노드 간에 얼마나 정확하게 균형을 이루어야 하는지를 조정하는 부동 소수점 임계값

**minimum\_threshold:** (선택 사항) rebalance\_table\_shards()의 임계값 인수에 허용되는 최솟값을 보유하는 보호 열. 기본값은 0입니다.

#### <a name="return-value"></a>반환 값

해당 없음

### <a name="citus_set_default_rebalance_strategy"></a>citus\_set\_default\_rebalance\_strategy

[pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table) 테이블을 업데이트하여 분할을 재조정할 때 선택한 기본값이 되도록 인수로 명명된 전략을 변경합니다.

#### <a name="arguments"></a>인수

**name:** pg\_dist\_rebalance\_strategy의 전략 이름

#### <a name="return-value"></a>반환 값

해당 없음

#### <a name="example"></a>예

```postgresql
SELECT citus_set_default_rebalance_strategy('by_disk_size');
```

### <a name="citus_remote_connection_stats"></a>citus\_remote\_connection\_stats

citus\_remote\_connection\_stats() 함수는 각 원격 노드에 대한 활성 연결 수를 표시합니다.

#### <a name="arguments"></a>인수

해당 없음

#### <a name="example"></a>예

```postgresql
SELECT * from citus_remote_connection_stats();
```

```
    hostname    | port | database_name | connection_count_to_node
----------------+------+---------------+--------------------------
 citus_worker_1 | 5432 | postgres      |                        3
(1 row)
```

### <a name="replicate_table_shards"></a>replicate\_table\_shards

replicate\_table\_shards() 함수는 주어진 테이블에서 덜 복제된 분할을 복제합니다. 이 함수는 먼저 복제를 위해 가져올 수 있는 덜 복제된 분할 및 위치의 목록을 계산합니다. 그런 다음, 함수는 해당 분할을 복사하고 해당 분할 메타데이터를 업데이트하여 복사본을 반영합니다.

#### <a name="arguments"></a>인수

**table\_name:** 분할을 복제해야 하는 테이블의 이름입니다.

**shard\_replication\_factor:** (선택 사양) 각 분할에 대해 달성하려는 원하는 복제 요소입니다.

**max\_shard\_copies:** (선택 사항) 원하는 복제 요소에 도달하기 위해 복사할 최대 분할 수입니다.

**excluded\_shard\_list:** (선택 사항) 복제 작업 중 복사하면 안 되는 분할의 식별자입니다.

#### <a name="return-value"></a>반환 값

N/A

#### <a name="examples"></a>예

아래 예에서는 github\_events 테이블의 shard를 shard\_replication\_factor에 복제하려고 시도합니다.

```postgresql
SELECT replicate_table_shards('github_events');
```

이 예에서는 최대 10개의 분할 복사본을 사용하여 github\_events 테이블의 분할을 원하는 복제 요소로 가져오려고 시도합니다. 리밸런서는 원하는 복제 요소에 도달하기 위해 최대 10개의 분할을 복사합니다

```postgresql
SELECT replicate_table_shards('github_events', max_shard_copies:=10);
```

### <a name="isolate_tenant_to_new_shard"></a>isolate\_tenant\_to\_new\_shard

이 함수는 배포 열에 특정 단일 값이 있는 행을 보관할 새 분할을 만듭니다. 대규모 테넌트를 자체 분할에 단독으로 배치하고 궁극적으로 자체 물리적 노드에 배치할 수 있는 다중 테넌트 하이퍼스케일(Citus) 사용 사례에 특히 유용합니다.

#### <a name="arguments"></a>인수

**table\_name:** 새 분할을 가져올 테이블의 이름입니다.

**tenant\_id:** 새 분할에 할당될 배포 열의 값입니다.

**cascade\_option:** (선택 사항) \"CASCADE\"로 설정하면 현재 테이블의 [공동 배치 그룹](concepts-hyperscale-colocation.md)에 있는 모든 테이블에서 분할도 분리됩니다.

#### <a name="return-value"></a>반환 값

**shard\_id:** 이 함수는 새로 만든 분할에 할당된 고유 ID를 반환합니다.

#### <a name="examples"></a>예

테넌트 135의 라인 항목을 보관할 새 분할을 만듭니다.

```postgresql
SELECT isolate_tenant_to_new_shard('lineitem', 135);
```

```
┌─────────────────────────────┐
│ isolate_tenant_to_new_shard │
├─────────────────────────────┤
│                      102240 │
└─────────────────────────────┘
```

## <a name="next-steps"></a>다음 단계

* 이 문서의 많은 함수는 시스템 [메타 데이터 테이블](reference-hyperscale-metadata.md)을 수정합니다.
* [서버 매개 변수](reference-hyperscale-parameters.md)는 일부 함수의 동작을 사용자 지정합니다.
