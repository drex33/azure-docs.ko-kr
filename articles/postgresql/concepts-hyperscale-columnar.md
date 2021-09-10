---
title: 열 형식 테이블 스토리지 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 열 형식 스토리지를 사용하여 데이터 압축
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 557fe7d2242b67f8fa53541aef25cfc44090e48b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536592"
---
# <a name="columnar-table-storage"></a>열 형식 테이블 스토리지

Azure Database for PostgreSQL - 하이퍼스케일(Citus)은 분석 및 데이터 웨어하우징 워크로드에 대해 추가 전용 열 형식 테이블 스토리지를 지원합니다. 행이 아닌 열이 디스크에 연속적으로 저장되면 데이터를 더 압축할 수 있고 쿼리는 열의 하위 집합을 더 빨리 요청할 수 있습니다.

## <a name="usage"></a>사용량

열 형식 스토리지를 사용하려면 테이블을 만들 때 `USING columnar`를 지정합니다.

```postgresql
CREATE TABLE contestant (
    handle TEXT,
    birthdate DATE,
    rating INT,
    percentile FLOAT,
    country CHAR(3),
    achievements TEXT[]
) USING columnar;
```

하이퍼스케일(Citus)은 삽입하는 동안 행을 "스프라이프"의 열 형식 스토리지로 변환합니다. 각 스트라이프는 트랜잭션 하나에 해당하는 데이터 또는 행 150000개 중에서 더 작은 값을 보관합니다.  (열 형식 테이블의 스트라이프 크기 및 기타 매개 변수는 [alter_columnar_table_set](reference-hyperscale-functions.md#alter_columnar_table_set) 함수로 변경할 수 있습니다.)

예를 들어 다음 문은 5개의 행을 모두 동일한 스트라이프에 넣고 있는데 모든 값이 단일 트랜잭션에 삽입되기 때문입니다.

```postgresql
-- insert these values into a single columnar stripe

INSERT INTO contestant VALUES
  ('a','1990-01-10',2090,97.1,'XA','{a}'),
  ('b','1990-11-01',2203,98.1,'XA','{a,b}'),
  ('c','1988-11-01',2907,99.4,'XB','{w,y}'),
  ('d','1985-05-05',2314,98.3,'XB','{}'),
  ('e','1995-05-05',2236,98.2,'XC','{a}');
```

하이퍼스케일(Citus)은 스트라이프별로 열 형식의 데이터를 별도로 압축하기 때문에 가능하면 큰 스트라이프를 만드는 것이 좋습니다. `VACUUM VERBOSE`를 사용하여 압축률, 스트라이프 수, 스트라이프당 행 평균과 같은 열 형식 테이블에 대한 팩트를 볼 수 있습니다.

```postgresql
VACUUM VERBOSE contestant;
```
```
INFO:  statistics for "contestant":
storage id: 10000000000
total file size: 24576, total data size: 248
compression rate: 1.31x
total row count: 5, stripe count: 1, average rows per stripe: 5
chunk count: 6, containing data for dropped columns: 0, zstd compressed: 6
```

출력은 하이퍼스케일(Citus)에서 zstd 압축 알고리즘을 사용하여 1.31x 데이터 압축을 얻은 것을 보여 줍니다. 압축률은 a) 메모리에 스테이징된 삽입된 데이터의 크기와 b) 해당 데이터가 최종 스트라이프에서 압축된 크기를 비교합니다.

측정 방법으로 인해 한 테이블에 대한 행과 열 형식 스토리지 간의 크기 차이와 압축률이 일치할 수도 있고 일치하지 않을 수도 있습니다. 이러한 차이를 제대로 찾는 유일한 방법은 동일한 데이터를 포함하는 한 행으로 된 열 형식 테이블을 생성하여 비교하는 것입니다.

## <a name="measuring-compression"></a>압축 측정

압축 절감 효과를 벤치마킹하기 위해 더 많은 데이터로 새 예제를 만들어 보겠습니다.

```postgresql
-- first a wide table using row storage
CREATE TABLE perf_row(
  c00 int8, c01 int8, c02 int8, c03 int8, c04 int8, c05 int8, c06 int8, c07 int8, c08 int8, c09 int8,
  c10 int8, c11 int8, c12 int8, c13 int8, c14 int8, c15 int8, c16 int8, c17 int8, c18 int8, c19 int8,
  c20 int8, c21 int8, c22 int8, c23 int8, c24 int8, c25 int8, c26 int8, c27 int8, c28 int8, c29 int8,
  c30 int8, c31 int8, c32 int8, c33 int8, c34 int8, c35 int8, c36 int8, c37 int8, c38 int8, c39 int8,
  c40 int8, c41 int8, c42 int8, c43 int8, c44 int8, c45 int8, c46 int8, c47 int8, c48 int8, c49 int8,
  c50 int8, c51 int8, c52 int8, c53 int8, c54 int8, c55 int8, c56 int8, c57 int8, c58 int8, c59 int8,
  c60 int8, c61 int8, c62 int8, c63 int8, c64 int8, c65 int8, c66 int8, c67 int8, c68 int8, c69 int8,
  c70 int8, c71 int8, c72 int8, c73 int8, c74 int8, c75 int8, c76 int8, c77 int8, c78 int8, c79 int8,
  c80 int8, c81 int8, c82 int8, c83 int8, c84 int8, c85 int8, c86 int8, c87 int8, c88 int8, c89 int8,
  c90 int8, c91 int8, c92 int8, c93 int8, c94 int8, c95 int8, c96 int8, c97 int8, c98 int8, c99 int8
);

-- next a table with identical columns using columnar storage
CREATE TABLE perf_columnar(LIKE perf_row) USING COLUMNAR;
```

두 테이블을 동일한 대규모 데이터 세트로 채웁니다.

```postgresql
INSERT INTO perf_row
  SELECT
    g % 00500, g % 01000, g % 01500, g % 02000, g % 02500, g % 03000, g % 03500, g % 04000, g % 04500, g % 05000,
    g % 05500, g % 06000, g % 06500, g % 07000, g % 07500, g % 08000, g % 08500, g % 09000, g % 09500, g % 10000,
    g % 10500, g % 11000, g % 11500, g % 12000, g % 12500, g % 13000, g % 13500, g % 14000, g % 14500, g % 15000,
    g % 15500, g % 16000, g % 16500, g % 17000, g % 17500, g % 18000, g % 18500, g % 19000, g % 19500, g % 20000,
    g % 20500, g % 21000, g % 21500, g % 22000, g % 22500, g % 23000, g % 23500, g % 24000, g % 24500, g % 25000,
    g % 25500, g % 26000, g % 26500, g % 27000, g % 27500, g % 28000, g % 28500, g % 29000, g % 29500, g % 30000,
    g % 30500, g % 31000, g % 31500, g % 32000, g % 32500, g % 33000, g % 33500, g % 34000, g % 34500, g % 35000,
    g % 35500, g % 36000, g % 36500, g % 37000, g % 37500, g % 38000, g % 38500, g % 39000, g % 39500, g % 40000,
    g % 40500, g % 41000, g % 41500, g % 42000, g % 42500, g % 43000, g % 43500, g % 44000, g % 44500, g % 45000,
    g % 45500, g % 46000, g % 46500, g % 47000, g % 47500, g % 48000, g % 48500, g % 49000, g % 49500, g % 50000
  FROM generate_series(1,50000000) g;

INSERT INTO perf_columnar
  SELECT
    g % 00500, g % 01000, g % 01500, g % 02000, g % 02500, g % 03000, g % 03500, g % 04000, g % 04500, g % 05000,
    g % 05500, g % 06000, g % 06500, g % 07000, g % 07500, g % 08000, g % 08500, g % 09000, g % 09500, g % 10000,
    g % 10500, g % 11000, g % 11500, g % 12000, g % 12500, g % 13000, g % 13500, g % 14000, g % 14500, g % 15000,
    g % 15500, g % 16000, g % 16500, g % 17000, g % 17500, g % 18000, g % 18500, g % 19000, g % 19500, g % 20000,
    g % 20500, g % 21000, g % 21500, g % 22000, g % 22500, g % 23000, g % 23500, g % 24000, g % 24500, g % 25000,
    g % 25500, g % 26000, g % 26500, g % 27000, g % 27500, g % 28000, g % 28500, g % 29000, g % 29500, g % 30000,
    g % 30500, g % 31000, g % 31500, g % 32000, g % 32500, g % 33000, g % 33500, g % 34000, g % 34500, g % 35000,
    g % 35500, g % 36000, g % 36500, g % 37000, g % 37500, g % 38000, g % 38500, g % 39000, g % 39500, g % 40000,
    g % 40500, g % 41000, g % 41500, g % 42000, g % 42500, g % 43000, g % 43500, g % 44000, g % 44500, g % 45000,
    g % 45500, g % 46000, g % 46500, g % 47000, g % 47500, g % 48000, g % 48500, g % 49000, g % 49500, g % 50000
  FROM generate_series(1,50000000) g;

VACUUM (FREEZE, ANALYZE) perf_row;
VACUUM (FREEZE, ANALYZE) perf_columnar;
```

이 데이터의 경우 칼럼 형식 테이블에서 8배 초과의 압축 비율을 확인할 수 있습니다.

```postgresql
SELECT pg_total_relation_size('perf_row')::numeric/
       pg_total_relation_size('perf_columnar') AS compression_ratio;
 compression_ratio
--------------------
 8.0196135873627944
(1 row)
```

## <a name="example"></a>예제

열 형식 스토리지는 테이블 분할이 잘 작동합니다. 예를 보려면 Citus 엔진 커뮤니티 설명서의 [열 형식 스토리지로 보관](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage)을 참조하세요.

## <a name="gotchas"></a>알려진 내용

* 열 형식 스토리지는 스트라이프별로 압축합니다. 스트라이프는 트랜잭션별로 만들어지므로 트랜잭션별로 하나의 행을 삽입하면 단일 행이 자체의 스트라이프에 배치됩니다. 단일 행 스트라이프의 압축 및 성능이 행 테이블보다 더 나빠질 수 있습니다. 항상 열 형식 테이블에 대량으로 삽입합니다.
* 많은 양의 작은 스트라이프를 뒤섞어서 열 형식으로 만드는 경우 중단이 발생합니다.
  유일한 해결 방법은 열 형식 테이블을 새로 만들고 한 트랜잭션으로 원본에서 데이터를 복사하는 것입니다.
  ```postgresql
  BEGIN;
  CREATE TABLE foo_compacted (LIKE foo) USING columnar;
  INSERT INTO foo_compacted SELECT * FROM foo;
  DROP TABLE foo;
  ALTER TABLE foo_compacted RENAME TO foo;
  COMMIT;
  ```
* 기본적으로 압축할 수 없는 데이터는 특정 열을 선택할 때 열 형식 스토리지가 여전히 유용하더라도 문제가 될 수 있습니다. 나머지 열을 메모리에 로드할 필요가 없습니다.
* 행 파티션과 열 파티션이 혼합되어 있는 분할된 테이블에서는 업데이트 대상을 신중하게 지정해야 합니다. 행 파티션만 적중하도록 필터링합니다.
   * 작업이 특정 행 파티션(예: `UPDATE p2 SET i = i + 1`)을 대상으로 하는 경우 성공합니다. 지정된 열 파티션(예: `UPDATE p1 SET i = i + 1`)을 대상으로 하는 경우 실패합니다.
   * 작업이 분할된 테이블을 대상으로 하고 모든 열 형식 파티션을 제외하는 WHERE 절이 있는 경우(예: `UPDATE parent SET i = i + 1 WHERE timestamp = '2020-03-15'`) 성공합니다.
   * 작업이 분할된 테이블을 대상으로 하지만 파티션 키 열에 대해 필터링하지 않는 경우 실패합니다. 열 형식만 있는 파티션에서 행과 일치하는 WHERE 절이 있는 경우라도 충분하지 않습니다. 파티션 키도 필터링해야 합니다.

## <a name="limitations"></a>제한 사항

이 기능에는 여전히 중요한 제한 사항이 있습니다. [하이퍼스케일(Citus) 제한 및 제한 사항](concepts-hyperscale-limits.md#columnar-storage)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* Citus [시계열 자습서](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage)(외부 링크)의 컬럼 형식 스토리지 예를 참조하세요.
