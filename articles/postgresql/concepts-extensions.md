---
title: 확장 - Azure Database for PostgreSQL - 단일 서버
description: Azure Database for PostgreSQL - 단일 서버에서 사용 가능한 Postgres 확장에 대해 알아봅니다
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 1dfdab5c90c8a3acd574ec042f2cc8ad033cc592
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433501"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - 단일 서버의 PostgreSQL 확장
PostgreSQL은 확장을 사용하여 데이터베이스의 기능을 확장하는 방법을 제공합니다. 확장은 단일 명령을 사용하여 데이터베이스에서 로드하거나 제거할 수 있는 단일 패키지에서 여러 관련 SQL 개체를 함께 번들로 묶습니다. 데이터베이스에 로드한 확장은 기본 제공 기능처럼 작동합니다.

## <a name="how-to-use-postgresql-extensions"></a>PostgreSQL 확장을 사용하는 방법
PostgreSQL 확장을 사용하려면 먼저 데이터베이스에 설치해야 합니다. 특정 확장을 설치하려면 psql 도구에서 [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) 명령을 실행하여 패키지 개체를 데이터베이스에 로드합니다.

Azure Database for PostgreSQL은 다음에 나열된 대로 주요 확장의 하위 집합을 지원합니다. 이 정보를 `SELECT * FROM pg_available_extensions;`를 실행하여 얻을 수도 있습니다. 나열된 확장 이외의 확장은 지원되지 않습니다. Azure Database for PostgreSQL에서 사용자 고유의 확장을 만들 수는 없습니다.

## <a name="postgres-11-extensions"></a>Postgres 11 확장

다음 확장은 Postgres 버전 11이 있는 Azure Database for PostgreSQL 서버에서 사용할 수 있습니다. 

> [!div class="mx-tableFixed"]
> | **내선 번호**| **확장 버전** | **설명** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 주소를 구성 요소로 구문 분석하는 데 사용됩니다. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Address Standardizer US 데이터 세트 예제|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | GIN의 공통 데이터 형식 인덱싱 지원|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | GiST의 공통 데이터 형식 인덱싱 지원|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | 대/소문자를 구분하지 않는 문자열의 데이터 형식|
> |[cube](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | 다차원 큐브에 대한 데이터 형식|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | 데이터베이스 내에서 다른 PostgreSQL 데이터베이스에 연결|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | 정수에 대한 텍스트 검색 사전 템플릿|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | 지구 표면의 대권거리 계산|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | 문자열 간의 유사성 및 거리 결정|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | 키/값 쌍 집합을 저장하기 위한 데이터 형식|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | PostgreSQL에 대한 가상 인덱스|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | 정수의 1차원 배열에 대한 함수, 연산자 및 인덱스 지원|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | 국제 제품 번호 지정 표준에 대한 데이터 형식|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | 계층적 트리와 유사한 구조체에 대한 데이터 형식|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 상업적 RDBMS에서 함수 및 패키지의 하위 집합을 에뮬레이트하는 함수 및 연산자|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | 감사 기능 제공|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | 암호화 함수|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | pgRouting 확장|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | 행 수준 잠금 정보 표시|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | 튜플 수준 통계 표시|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | 공유 버퍼 캐시를 검사합니다|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | 시간 또는 ID로 분할된 테이블을 관리하기 위한 확장|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | 관계 데이터 사전 준비|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | 실행된 모든 SQL 문의 실행 통계 추적|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | 트라이그램을 기반으로 하는 텍스트 유사성 측정 및 인덱스 검색|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL 절차적 언어|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript(v8) 신뢰할 수 있는 절차적 언어|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS 기하학, 지리 및 래스터 공간 유형 및 함수|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL 함수|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS 타이거 지오코더(tiger geocoder) 및 리버스 지오코더(reverse geocoder)|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS 토폴로지 공간 형식 및 함수|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | 원격 PostgreSQL 서버에 대한 외부 데이터 래퍼(wrapper)|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | 크로스탭을 포함한 전체 테이블을 조작하는 함수|
> |[timescaledb](https://docs.timescale.com/latest)                    |1.7.4             | 시계열 데이터에 대한 확장성 있는 삽입 및 복잡한 쿼리를 사용합니다|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | 악센트를 제거하는 텍스트 검색 사전|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | UUID(범용 고유 식별자)를 생성합니다|

## <a name="postgres-10-extensions"></a>Postgres 10 확장 

다음 확장은 Postgres 버전 10이 있는 Azure Database for PostgreSQL 서버에서 사용할 수 있습니다.

> [!div class="mx-tableFixed"]
> | **내선 번호**| **확장 버전** | **설명** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 주소를 구성 요소로 구문 분석하는 데 사용됩니다. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Address Standardizer US 데이터 세트 예제|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | GIN의 공통 데이터 형식 인덱싱 지원|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | GiST의 공통 데이터 형식 인덱싱 지원|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | 자동으로 암호화된 암호에 대한 데이터 형식|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | 대/소문자를 구분하지 않는 문자열의 데이터 형식|
> |[cube](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | 다차원 큐브에 대한 데이터 형식|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | 데이터베이스 내에서 다른 PostgreSQL 데이터베이스에 연결|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | 정수에 대한 텍스트 검색 사전 템플릿|
> |[earthdistance](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | 지구 표면의 대권거리 계산|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | 문자열 간의 유사성 및 거리 결정|
> |[hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | 키/값 쌍 집합을 저장하기 위한 데이터 형식|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | PostgreSQL에 대한 가상 인덱스|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | 정수의 1차원 배열에 대한 함수, 연산자 및 인덱스 지원|
> |[isn](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | 국제 제품 번호 지정 표준에 대한 데이터 형식|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | 계층적 트리와 유사한 구조체에 대한 데이터 형식|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 상업적 RDBMS에서 함수 및 패키지의 하위 집합을 에뮬레이트하는 함수 및 연산자|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | 감사 기능 제공|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | 암호화 함수|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | pgRouting 확장|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | 행 수준 잠금 정보 표시|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | 튜플 수준 통계 표시|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | 공유 버퍼 캐시를 검사합니다|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 시간 또는 ID로 분할된 테이블을 관리하기 위한 확장|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | 관계 데이터 사전 준비|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | 실행된 모든 SQL 문의 실행 통계 추적|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | 트라이그램을 기반으로 하는 텍스트 유사성 측정 및 인덱스 검색|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | PL/pgSQL 절차적 언어|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript(v8) 신뢰할 수 있는 절차적 언어|
> |[postgis](https://www.postgis.net/)                      | 2.4.3           | PostGIS 기하학, 지리 및 래스터 공간 유형 및 함수|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | PostGIS SFCGAL 함수|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | PostGIS 타이거 지오코더(tiger geocoder) 및 리버스 지오코더(reverse geocoder)|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | PostGIS 토폴로지 공간 형식 및 함수|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | 원격 PostgreSQL 서버에 대한 외부 데이터 래퍼(wrapper)|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | 크로스탭을 포함한 전체 테이블을 조작하는 함수|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.7.4             | 시계열 데이터에 대한 확장성 있는 삽입 및 복잡한 쿼리를 사용합니다|
> |[unaccent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | 악센트를 제거하는 텍스트 검색 사전|
> |[uuid-ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | UUID(범용 고유 식별자)를 생성합니다|

## <a name="postgres-96-extensions"></a>Postgres 9.6 확장 

다음 확장은 Postgres 버전 9.6이 있는 Azure Database for PostgreSQL 서버에서 사용할 수 있습니다.

> [!div class="mx-tableFixed"]
> | **내선 번호**| **확장 버전** | **설명** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | 주소를 구성 요소로 구문 분석하는 데 사용됩니다. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Address Standardizer US 데이터 세트 예제|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | GIN의 공통 데이터 형식 인덱싱 지원|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | GiST의 공통 데이터 형식 인덱싱 지원|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | 자동으로 암호화된 암호에 대한 데이터 형식|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | 대/소문자를 구분하지 않는 문자열의 데이터 형식|
> |[cube](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | 다차원 큐브에 대한 데이터 형식|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | 데이터베이스 내에서 다른 PostgreSQL 데이터베이스에 연결|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | 정수에 대한 텍스트 검색 사전 템플릿|
> |[earthdistance](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | 지구 표면의 대권거리 계산|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | 문자열 간의 유사성 및 거리 결정|
> |[hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | 키/값 쌍 집합을 저장하기 위한 데이터 형식|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | PostgreSQL에 대한 가상 인덱스|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | 정수의 1차원 배열에 대한 함수, 연산자 및 인덱스 지원|
> |[isn](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | 국제 제품 번호 지정 표준에 대한 데이터 형식|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | 계층적 트리와 유사한 구조체에 대한 데이터 형식|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 상업적 RDBMS에서 함수 및 패키지의 하위 집합을 에뮬레이트하는 함수 및 연산자|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | 감사 기능 제공|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | 암호화 함수|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | pgRouting 확장|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | 행 수준 잠금 정보 표시|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | 튜플 수준 통계 표시|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | 공유 버퍼 캐시를 검사합니다|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 시간 또는 ID로 분할된 테이블을 관리하기 위한 확장|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | 관계 데이터 사전 준비|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | 실행된 모든 SQL 문의 실행 통계 추적|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | 트라이그램을 기반으로 하는 텍스트 유사성 측정 및 인덱스 검색|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | PL/pgSQL 절차적 언어|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript(v8) 신뢰할 수 있는 절차적 언어|
> |[postgis](https://www.postgis.net/)                      | 2.3.2           | PostGIS 기하학, 지리 및 래스터 공간 유형 및 함수|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | PostGIS SFCGAL 함수|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | PostGIS 타이거 지오코더(tiger geocoder) 및 리버스 지오코더(reverse geocoder)|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | PostGIS 토폴로지 공간 형식 및 함수|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | 원격 PostgreSQL 서버에 대한 외부 데이터 래퍼(wrapper)|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | 크로스탭을 포함한 전체 테이블을 조작하는 함수|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.7.4             | 시계열 데이터에 대한 확장성 있는 삽입 및 복잡한 쿼리를 사용합니다|
> |[unaccent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | 악센트를 제거하는 텍스트 검색 사전|
> |[uuid-ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | UUID(범용 고유 식별자)를 생성합니다|

## <a name="postgres-95-extensions"></a>Postgres 9.5 확장

>[!NOTE]
> PostgreSQL 버전 9.5은 사용이 중지되었습니다.

다음 확장은 Postgres 버전 9.5가 있는 Azure Database for PostgreSQL 서버에서 사용할 수 있습니다.

> [!div class="mx-tableFixed"]
> | **내선 번호**| **확장 버전** | **설명** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | 주소를 구성 요소로 구문 분석하는 데 사용됩니다. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Address Standardizer US 데이터 세트 예제|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | GIN의 공통 데이터 형식 인덱싱 지원|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | GiST의 공통 데이터 형식 인덱싱 지원|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | 자동으로 암호화된 암호에 대한 데이터 형식|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | 대/소문자를 구분하지 않는 문자열의 데이터 형식|
> |[cube](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | 다차원 큐브에 대한 데이터 형식|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | 데이터베이스 내에서 다른 PostgreSQL 데이터베이스에 연결|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | 정수에 대한 텍스트 검색 사전 템플릿|
> |[earthdistance](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | 지구 표면의 대권거리 계산|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | 문자열 간의 유사성 및 거리 결정|
> |[hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | 키/값 쌍 집합을 저장하기 위한 데이터 형식|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | PostgreSQL에 대한 가상 인덱스|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | 정수의 1차원 배열에 대한 함수, 연산자 및 인덱스 지원|
> |[isn](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | 국제 제품 번호 지정 표준에 대한 데이터 형식|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | 계층적 트리와 유사한 구조체에 대한 데이터 형식|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 상업적 RDBMS에서 함수 및 패키지의 하위 집합을 에뮬레이트하는 함수 및 연산자|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | 감사 기능 제공|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | 암호화 함수|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | pgRouting 확장|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | 행 수준 잠금 정보 표시|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | 튜플 수준 통계 표시|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | 공유 버퍼 캐시를 검사합니다|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 시간 또는 ID로 분할된 테이블을 관리하기 위한 확장|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | 관계 데이터 사전 준비|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | 실행된 모든 SQL 문의 실행 통계 추적|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | 트라이그램을 기반으로 하는 텍스트 유사성 측정 및 인덱스 검색|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | PL/pgSQL 절차적 언어|
> |[postgis](https://www.postgis.net/)                      | 2.3.0           | PostGIS 기하학, 지리 및 래스터 공간 유형 및 함수|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | PostGIS SFCGAL 함수|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | PostGIS 타이거 지오코더(tiger geocoder) 및 리버스 지오코더(reverse geocoder)|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | PostGIS 토폴로지 공간 형식 및 함수|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | 원격 PostgreSQL 서버에 대한 외부 데이터 래퍼(wrapper)|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | 크로스탭을 포함한 전체 테이블을 조작하는 함수|
> |[unaccent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | 악센트를 제거하는 텍스트 검색 사전|
> |[uuid-ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | UUID(범용 고유 식별자)를 생성합니다|


## <a name="pg_stat_statements"></a>pg_stat_statements
[pg_stat_statements 확장](https://www.postgresql.org/docs/current/pgstatstatements.html)은 모든 Azure Database for PostgreSQL 서버에 미리 로드되어 SQL 문의 실행 통계를 추적하는 수단을 제공합니다.
확장을 통해 어떤 명령문을 계산할지 제어하는 `pg_stat_statements.track` 설정의 기본값은 `top`이며 이것은 클라이언트가 직접 실행하는 모든 명령문이 추적된다는 의미입니다. 다른 두 가지 추적 수준은 `none`과 `all`입니다. 이 설정은 [Azure Portal](./howto-configure-server-parameters-using-portal.md)이나 [Azure CLI](./howto-configure-server-parameters-using-cli.md)를 통해 서버 매개 변수로 구성할 수 있습니다.

pg_stat_statements를 통해 제공되는 쿼리 실행 정보와 각 SQL 문을 기록할 때 서버 성능에 미치는 영향 사이에는 상충 관계가 있습니다. pg_stat_statements 확장을 적극적으로 사용하지 않는 경우에는 `pg_stat_statements.track`을 `none`으로 설정하는 것이 좋습니다. 일부 타사 모니터링 서비스가 pg_stat_statements에 기반하여 쿼리 성능 정보를 제공할 수 있으므로 여기에 해당하는 경우인지 여부를 확인하십시오.

## <a name="dblink-and-postgres_fdw"></a>dblink 및 postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) 및 [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html)를 사용하여 한 PostgreSQL 서버에서 다른 PostgreSQL 서버로, 또는 동일한 서버의 다른 데이터베이스로 연결할 수 있습니다. 받는 서버는 해당 방화벽을 통한 보내는 서버의 연결을 허용해야 합니다. 이러한 확장을 사용하여 Azure Database for PostgreSQL 서버 간을 연결하는 경우 "Azure 서비스에 대한 액세스 허용"을 켜기로 설정하여 이 작업을 수행할 수 있습니다. 확장을 사용하여 동일한 서버로 루프백하는 데도 이 작업이 필요합니다. "Azure 서비스에 대한 액세스 허용" 설정은 Postgres 서버의 Azure Portal 페이지에서 연결 보안 아래에 있습니다. “Azure 서비스에 대한 액세스 허용”을 설정하면 모든 Azure IP가 허용 목록에 배치됩니다.

현재는 동일한 지역의 다른 Azure Database for PostgreSQL 서버에 대한 연결을 제외하고 Azure Database for PostgreSQL의 아웃바운드 연결이 지원되지 않습니다.

## <a name="uuid"></a>uuid
만약 `uuid_generate_v4()`을 [uuid-ossp 확장](https://www.postgresql.org/docs/current/uuid-ossp.html)에서 사용하려는 경우, 성능 이점을 위해 [pgcrypto 확장](https://www.postgresql.org/docs/current/pgcrypto.html)에서 `gen_random_uuid()`와 비교해보세요.

## <a name="pgaudit"></a>pgAudit
[pgAudit 확장](https://github.com/pgaudit/pgaudit/blob/master/README.md)은 세션 및 개체 감사 로깅을 제공합니다. Azure Database for PostgreSQL에서 이 확장을 사용하는 방법을 알아보려면 [감사 개념 문서](concepts-audit.md)를 참조하세요. 

## <a name="pg_prewarm"></a>pg_prewarm
pg_prewarm 확장은 관계형 데이터를 캐시에 로드합니다. 캐시를 사전 준비하면 다시 시작 후 쿼리를 처음 실행할 때 더 나은 응답 시간을 보입니다. Postgres 10 이하에서는 사전 준비를 [prewarm 함수](https://www.postgresql.org/docs/10/pgprewarm.html)를 사용하여 수동으로 수행합니다.

Postgres 11 이상에서는 사전 준비를 [자동으로](https://www.postgresql.org/docs/current/pgprewarm.html)수행하도록 구성할 수 있습니다. pg_prewarm을 `shared_preload_libraries` 매개 변수 목록에 포함하고 변경 사항을 적용하기 위해 서버를 다시 시작해야 합니다. 매개 변수는 [Azure Portal](howto-configure-server-parameters-using-portal.md), [CLI](howto-configure-server-parameters-using-cli.md), REST API 또는 ARM 템플릿에서 설정할 수 있습니다. 

## <a name="timescaledb"></a>TimescaleDB
TimescaleDB는 PostgreSQL용 확장으로 패키지된 시계열 데이터베이스입니다. TimescaleDB는 시계열 워크로드에 대한 시간 기반 분석 함수, 최적화 및 스케일링 Postgres를 제공합니다.

[Timescale, Inc.](https://www.timescale.com/)의 등록 상표인 [TimescaleDB](https://docs.timescale.com/latest)에 대한 자세한 정보를 알아봅니다. Azure Database for PostgreSQL은 TimescaleDB [Apache-2 버전](https://www.timescale.com/legal/licenses)을 제공합니다.

### <a name="installing-timescaledb"></a>TimescaleDB 설치
TimescaleDB를 설치하려면 이를 먼저 서버의 미리 로드된 공유 라이브러리에 포함시켜야 합니다. Postgres의 `shared_preload_libraries` 매개 변수를 변경하려면 **서버를 다시 시작** 해야 합니다. 매개 변수는 [Azure Portal](howto-configure-server-parameters-using-portal.md) 또는 [Azure CLI](howto-configure-server-parameters-using-cli.md)를 사용해서 바꿀 수 있습니다.

다음과 같이 [Azure Portal](https://portal.azure.com/)을 사용합니다.

1. Azure Database for PostgreSQL 서버를 선택합니다.

2. 사이드바에서 **서버 매개 변수** 를 선택합니다.

3. `shared_preload_libraries` 매개 변수를 검색합니다.

4. **TimescaleDB** 를 선택합니다.

5. **저장** 을 선택하여 변경 내용을 보존합니다. 변경 내용이 저장되면 알림을 받습니다. 

6. 알림이 오면 서버를 **다시 시작** 하여 변경 내용을 적용합니다. 서버를 다시 시작하는 방법을 알아보려면 [Azure Database for PostgreSQL 서버 다시 시작](howto-restart-server-portal.md)을 참조하세요.


이제 Postgres 데이터베이스에서 TimescaleDB를 사용할 수 있습니다. 데이터베이스에 연결하여 다음 명령을 실행합니다.
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> 오류가 표시되면 shared_preload_libraries를 저장한 후 [서버를 다시 시작](howto-restart-server-portal.md)했는지 확인합니다. 

이제 TimescaleDB 하이퍼테이블을 [처음부터 새로](https://docs.timescale.com/getting-started/creating-hypertables) 만들거나 [PostgreSQL에서 기존 시계열 데이터](https://docs.timescale.com/getting-started/migrating-data)를 마이그레이션할 수 있습니다.

### <a name="restoring-a-timescale-database"></a>Timescale 데이터베이스 복원
pg_dump 및 pg_restore를 사용하여 Timescale 데이터베이스를 복원하려면 대상 데이터베이스에서 `timescaledb_pre_restore()` 및 `timescaledb_post restore()`라는 두 개의 도우미 프로시저를 실행해야 합니다.

먼저 대상 데이터베이스를 준비합니다.

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

이제 원본 데이터베이스에서 pg_dump를 실행하고 그 다음 pg_restore를 실행합니다. 복원 후 복원된 데이터베이스에서 다음 명령을 반드시 실행하세요.

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>다음 단계
사용하려는 확장이 표시되지 않으면 알려주세요. [피드백 포럼](https://feedback.azure.com/d365community/forum/c5e32b97-ee24-ec11-b6e6-000d3a4f0da0)에서 기존 요청에 투표하거나 새 피드백 요청을 작성하세요.
