---
title: 확장 - Azure Database for PostgreSQL - 유연한 서버
description: Azure Database for PostgreSQL - 유연한 서버에서 사용할 수 있는 Postgres 확장에 대해 알아봅니다.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/30/2021
ms.openlocfilehash: ceac1b5fcf1e788a01034e48b20b7dcb88d2016e
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271292"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - 유연한 서버의 PostgreSQL 확장



PostgreSQL은 확장을 사용하여 데이터베이스의 기능을 확장하는 방법을 제공합니다. 확장은 명령을 사용하여 데이터베이스에서 로드하거나 제거할 수 있는 단일 패키지에서 여러 관련 SQL 개체를 함께 번들로 묶습니다. 데이터베이스에 로드한 확장은 기본 제공 기능처럼 작동합니다.

## <a name="how-to-use-postgresql-extensions"></a>PostgreSQL 확장을 사용하는 방법
PostgreSQL 확장을 사용하려면 먼저 데이터베이스에 설치해야 합니다. 특정 확장을 설치하려면 [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) 명령을 실행합니다. 이 명령은 패키지 개체를 데이터베이스에 로드합니다.

Azure Database for PostgreSQL은 아래에 나열된 대로 주요 확장의 일부만 지원합니다. 이 정보를 `SHOW azure.extensions;`를 실행하여 얻을 수도 있습니다. 이 문서에 나열되지 않은 확장은 Azure Database for PostgreSQL - 유연한 서버에서 지원되지 않습니다. Azure Database for PostgreSQL에서 사용자 고유의 확장을 만들거나 로드할 수는 없습니다.

## <a name="postgres-13-extensions"></a>Postgres 13 확장

다음 확장은 Postgres 버전 13이 있는 Azure Database for PostgreSQL - 유연한 서버에서 사용할 수 있습니다. 

> [!div class="mx-tableFixed"]
> | **내선 번호**| **확장 버전** | **설명** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 3.1.1           | 주소를 구성 요소로 구문 분석하는 데 사용됩니다. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 3.1.1           | Address Standardizer US 데이터 세트 예제|
> |[amcheck](https://www.postgresql.org/docs/13/amcheck.html)                    | 1.2             | 관계 무결성을 확인하는 함수|
> |[bloom](https://www.postgresql.org/docs/13/bloom.html)                    | 1.0             | bloom 액세스 메서드 - 서명 파일 기반 인덱스|
> |[btree_gin](https://www.postgresql.org/docs/13/btree-gin.html)                    | 1.3             | GIN의 공통 데이터 형식 인덱싱 지원|
> |[btree_gist](https://www.postgresql.org/docs/13/btree-gist.html)                   | 1.5             | GiST의 공통 데이터 형식 인덱싱 지원|
> |[citext](https://www.postgresql.org/docs/13/citext.html)                       | 1.6             | 대/소문자를 구분하지 않는 문자열의 데이터 형식|
> |[cube](https://www.postgresql.org/docs/13/cube.html)                         | 1.4             | 다차원 큐브에 대한 데이터 형식|
> |[dblink](https://www.postgresql.org/docs/13/dblink.html)                       | 1.2             | 데이터베이스 내에서 다른 PostgreSQL 데이터베이스에 연결|
> |[dict_int](https://www.postgresql.org/docs/13/dict-int.html)                     | 1.0             | 정수에 대한 텍스트 검색 사전 템플릿|
> |[dict_xsyn](https://www.postgresql.org/docs/13/dict-xsyn.html)                     | 1.0             | 확장된 동의어 처리를 위한 텍스트 검색 사전 템플릿|
> |[earthdistance](https://www.postgresql.org/docs/13/earthdistance.html)                | 1.1             | 지구 표면의 대권거리 계산|
> |[fuzzystrmatch](https://www.postgresql.org/docs/13/fuzzystrmatch.html)                | 1.1             | 문자열 간의 유사성 및 거리 결정|
> |[hstore](https://www.postgresql.org/docs/13/hstore.html)                       | 1.7             | 키/값 쌍 집합을 저장하기 위한 데이터 형식|
> |[intagg](https://www.postgresql.org/docs/13/intagg.html)                     | 1.1             | 정수 집계 및 열거자 (사용되지 않음)|
> |[intarray](https://www.postgresql.org/docs/13/intarray.html)                     | 1.3             | 정수의 1차원 배열에 대한 함수, 연산자 및 인덱스 지원|
> |[isn](https://www.postgresql.org/docs/13/isn.html)                          | 1.2             | 국제 제품 번호 지정 표준에 대한 데이터 형식|
> |[lo](https://www.postgresql.org/docs/13/lo.html)                            | 1.1             | LOB(Large Object) 유지 관리 |
> |[ltree](https://www.postgresql.org/docs/13/ltree.html)                        | 1.2             | 계층적 트리 구조에 대한 데이터 형식|
> |[pageinspect](https://www.postgresql.org/docs/13/pageinspect.html)                        | 1.8             | 낮은 수준에서 데이터베이스 페이지의 내용 검사|
> |[pg_buffercache](https://www.postgresql.org/docs/13/pgbuffercache.html)               | 1.3             | 공유 버퍼 캐시를 검사합니다|
> |[pg_cron](https://github.com/citusdata/pg_cron)                        | 1.3             | PostgreSQL용 작업 스케줄러|
> |[pg_freespacemap](https://www.postgresql.org/docs/13/pgfreespacemap.html)               | 1.2             | FSM(사용 가능한 공간 맵) 검사|
> |[pg_partman](https://github.com/pgpartman/pg_partman)         | 4.5.0           | 시간 또는 ID로 분할된 테이블을 관리하기 위한 확장 |
> |[pg_prewarm](https://www.postgresql.org/docs/13/pgprewarm.html)                   | 1.2             | 관계 데이터 사전 준비|
> |[pg_stat_statements](https://www.postgresql.org/docs/13/pgstatstatements.html)           | 1.8             | 실행된 모든 SQL 문의 실행 통계 추적|
> |[pg_trgm](https://www.postgresql.org/docs/13/pgtrgm.html)                      | 1.5             | 트라이그램을 기반으로 하는 텍스트 유사성 측정 및 인덱스 검색|
> |[pg_visibility](https://www.postgresql.org/docs/13/pgvisibility.html)                      | 1.2             | VM(표시 유형 맵) 및 페이지 수준 표시 유형 정보 검사|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.5             | 감사 기능 제공|
> |[pgcrypto](https://www.postgresql.org/docs/13/pgcrypto.html)                     | 1.3             | 암호화 함수 제공| 
> |[pglogical](https://github.com/2ndQuadrant/pglogical)       | 2.3.2                | 논리적 스트리밍 복제 |
> |[pgrowlocks](https://www.postgresql.org/docs/13/pgrowlocks.html)                   | 1.2             | 행 수준 잠금 정보 표시|
> |[pgstattuple](https://www.postgresql.org/docs/13/pgstattuple.html)                  | 1.5             | 튜플 수준 통계 표시|
> |[plpgsql](https://www.postgresql.org/docs/13/plpgsql.html)                      | 1.0             | PL/pgSQL 절차적 언어|
> |[postgis](https://www.postgis.net/)                      | 3.1.1           | PostGIS 기하학, 지리 |
> |[postgis_raster](https://www.postgis.net/)               | 3.1.1           | PostGIS 래스터 형식 및 함수| 
> |[postgis_sfcgal](https://www.postgis.net/)               | 3.1.1           | PostGIS SFCGAL 함수|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 3.1.1           | PostGIS 타이거 지오코더(tiger geocoder) 및 리버스 지오코더(reverse geocoder)|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 3.1.1           | PostGIS 토폴로지 공간 형식 및 함수|
> |[postgres_fdw](https://www.postgresql.org/docs/13/postgres-fdw.html)                 | 1.0             | 원격 PostgreSQL 서버에 대한 외부 데이터 래퍼|
> |[sslinfo](https://www.postgresql.org/docs/13/sslinfo.html)                    | 1.2             | SSL 인증서 관련 정보|
> |[tsm_system_rows](https://www.postgresql.org/docs/13/tsm-system-rows.html)                    | 1.0             |  행 수를 제한으로 허용하는 TABLESAMPLE 메서드|
> |[tsm_system_time](https://www.postgresql.org/docs/13/tsm-system-time.html)                    | 1.0             |  시간(밀리초)을 제한으로 허용하는 TABLESAMPLE 메서드|
> |[unaccent](https://www.postgresql.org/docs/13/unaccent.html)                     | 1.1             | 악센트를 제거하는 텍스트 검색 사전|
> |[uuid-ossp](https://www.postgresql.org/docs/13/uuid-ossp.html)                    | 1.1             | UUID(범용 고유 식별자)를 생성합니다|

## <a name="postgres-12-extensions"></a>Postgres 12 확장

다음 확장은 Postgres 버전 12가 있는 Azure Database for PostgreSQL - 유연한 서버에서 사용할 수 있습니다. 

> [!div class="mx-tableFixed"]
> | **내선 번호**| **확장 버전** | **설명** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 3.0.0           | 주소를 구성 요소로 구문 분석하는 데 사용됩니다. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 3.0.0           | Address Standardizer US 데이터 세트 예제|
> |[amcheck](https://www.postgresql.org/docs/12/amcheck.html)                    | 1.2             | 관계 무결성을 확인하는 함수|
> |[bloom](https://www.postgresql.org/docs/12/bloom.html)                    | 1.0             | bloom 액세스 메서드 - 서명 파일 기반 인덱스|
> |[btree_gin](https://www.postgresql.org/docs/12/btree-gin.html)                    | 1.3             | GIN의 공통 데이터 형식 인덱싱 지원|
> |[btree_gist](https://www.postgresql.org/docs/12/btree-gist.html)                   | 1.5             | GiST의 공통 데이터 형식 인덱싱 지원|
> |[citext](https://www.postgresql.org/docs/12/citext.html)                       | 1.6             | 대/소문자를 구분하지 않는 문자열의 데이터 형식|
> |[cube](https://www.postgresql.org/docs/12/cube.html)                         | 1.4             | 다차원 큐브에 대한 데이터 형식|
> |[dblink](https://www.postgresql.org/docs/12/dblink.html)                       | 1.2             | 데이터베이스 내에서 다른 PostgreSQL 데이터베이스에 연결|
> |[dict_int](https://www.postgresql.org/docs/12/dict-int.html)                     | 1.0             | 정수에 대한 텍스트 검색 사전 템플릿|
> |[dict_xsyn](https://www.postgresql.org/docs/12/dict-xsyn.html)                     | 1.0             | 확장된 동의어 처리를 위한 텍스트 검색 사전 템플릿|
> |[earthdistance](https://www.postgresql.org/docs/12/earthdistance.html)                | 1.1             | 지구 표면의 대권거리 계산|
> |[fuzzystrmatch](https://www.postgresql.org/docs/12/fuzzystrmatch.html)                | 1.1             | 문자열 간의 유사성 및 거리 결정|
> |[hstore](https://www.postgresql.org/docs/12/hstore.html)                       | 1.6             | 키/값 쌍 집합을 저장하기 위한 데이터 형식|
> |[hypopg](https://github.com/HypoPG/hypopg)                                   |  1.2             | 가상 인덱스에 대 한 지원 추가 확장 |
> |[intagg](https://www.postgresql.org/docs/12/intagg.html)                     | 1.1             | 정수 집계 및 열거자 (사용되지 않음)|
> |[intarray](https://www.postgresql.org/docs/12/intarray.html)                     | 1.2             | 정수의 1차원 배열에 대한 함수, 연산자 및 인덱스 지원|
> |[isn](https://www.postgresql.org/docs/12/isn.html)                          | 1.2             | 국제 제품 번호 지정 표준에 대한 데이터 형식|
> |[lo](https://www.postgresql.org/docs/12/lo.html)                            | 1.1             | LOB(Large Object) 유지 관리 |
> |[ltree](https://www.postgresql.org/docs/12/ltree.html)                        | 1.1             | 계층적 트리 구조에 대한 데이터 형식|
> |[pageinspect](https://www.postgresql.org/docs/12/pageinspect.html)                        | 1.7             | 낮은 수준에서 데이터베이스 페이지의 내용 검사|
> |[pg_buffercache](https://www.postgresql.org/docs/12/pgbuffercache.html)               | 1.3             | 공유 버퍼 캐시를 검사합니다|
> |[pg_cron](https://github.com/citusdata/pg_cron)                        | 1.3             | PostgreSQL용 작업 스케줄러|
> |[pg_freespacemap](https://www.postgresql.org/docs/12/pgfreespacemap.html)               | 1.2             | FSM(사용 가능한 공간 맵) 검사|
> |[pg_partman](https://github.com/pgpartman/pg_partman)         | 4.5.0           | 시간 또는 ID로 분할된 테이블을 관리하기 위한 확장 |
> |[pg_prewarm](https://www.postgresql.org/docs/12/pgprewarm.html)                   | 1.2             | 관계 데이터 사전 준비|
> |[pg_stat_statements](https://www.postgresql.org/docs/12/pgstatstatements.html)           | 1.7             | 실행된 모든 SQL 문의 실행 통계 추적|
> |[pg_trgm](https://www.postgresql.org/docs/12/pgtrgm.html)                      | 1.4             | 트라이그램을 기반으로 하는 텍스트 유사성 측정 및 인덱스 검색|
> |[pg_visibility](https://www.postgresql.org/docs/12/pgvisibility.html)                      | 1.2             | VM(표시 유형 맵) 및 페이지 수준 표시 유형 정보 검사|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.4             | 감사 기능 제공|
> |[pgcrypto](https://www.postgresql.org/docs/12/pgcrypto.html)                     | 1.3             | 암호화 함수 제공|
>|[pglogical](https://github.com/2ndQuadrant/pglogical)       | 2.3.2                | 논리적 스트리밍 복제 |
> |[pgrowlocks](https://www.postgresql.org/docs/12/pgrowlocks.html)                   | 1.2             | 행 수준 잠금 정보 표시|
> |[pgstattuple](https://www.postgresql.org/docs/12/pgstattuple.html)                  | 1.5             | 튜플 수준 통계 표시|
> |[plpgsql](https://www.postgresql.org/docs/12/plpgsql.html)                      | 1.0             | PL/pgSQL 절차적 언어|
> |[postgis](https://www.postgis.net/)                      | 3.0.0           | PostGIS 기하학, 지리 |
> |[postgis_raster](https://www.postgis.net/)               | 3.0.0           | PostGIS 래스터 형식 및 함수| 
> |[postgis_sfcgal](https://www.postgis.net/)               | 3.0.0           | PostGIS SFCGAL 함수|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 3.0.0           | PostGIS 타이거 지오코더(tiger geocoder) 및 리버스 지오코더(reverse geocoder)|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 3.0.0           | PostGIS 토폴로지 공간 형식 및 함수|
> |[postgres_fdw](https://www.postgresql.org/docs/12/postgres-fdw.html)                 | 1.0             | 원격 PostgreSQL 서버에 대한 외부 데이터 래퍼|
> |[sslinfo](https://www.postgresql.org/docs/12/sslinfo.html)                    | 1.2             | SSL 인증서 관련 정보|
> |[tsm_system_rows](https://www.postgresql.org/docs/12/tsm-system-rows.html)                    | 1.0             |  행 수를 제한으로 허용하는 TABLESAMPLE 메서드|
> |[tsm_system_time](https://www.postgresql.org/docs/12/tsm-system-time.html)                    | 1.0             |  시간(밀리초)을 제한으로 허용하는 TABLESAMPLE 메서드|
> |[unaccent](https://www.postgresql.org/docs/12/unaccent.html)                     | 1.1             | 악센트를 제거하는 텍스트 검색 사전|
> |[uuid-ossp](https://www.postgresql.org/docs/12/uuid-ossp.html)                    | 1.1             | UUID(범용 고유 식별자) 생성|

## <a name="postgres-11-extensions"></a>Postgres 11 확장

다음 확장은 Postgres 버전 11이 있는 Azure Database for PostgreSQL - 유연한 서버에서 사용할 수 있습니다. 

> [!div class="mx-tableFixed"]
> | **내선 번호**| **확장 버전** | **설명** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 주소를 구성 요소로 구문 분석하는 데 사용됩니다. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Address Standardizer US 데이터 세트 예제|
> |[amcheck](https://www.postgresql.org/docs/11/amcheck.html)                    | 1.1             | 관계 무결성을 확인하는 함수|
> |[bloom](https://www.postgresql.org/docs/11/bloom.html)                    | 1.0             | bloom 액세스 메서드 - 서명 파일 기반 인덱스|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | GIN의 공통 데이터 형식 인덱싱 지원|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | GiST의 공통 데이터 형식 인덱싱 지원|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | 대/소문자를 구분하지 않는 문자열의 데이터 형식|
> |[cube](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | 다차원 큐브에 대한 데이터 형식|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | 데이터베이스 내에서 다른 PostgreSQL 데이터베이스에 연결|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | 정수에 대한 텍스트 검색 사전 템플릿|
> |[dict_xsyn](https://www.postgresql.org/docs/11/dict-xsyn.html)                     | 1.0             | 확장된 동의어 처리를 위한 텍스트 검색 사전 템플릿|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | 지구 표면의 대권거리 계산|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | 문자열 간의 유사성 및 거리 결정|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | 키/값 쌍 집합을 저장하기 위한 데이터 형식|
> |[hypopg](https://github.com/HypoPG/hypopg)                                   |  1.1.2            | 가상 인덱스 지원을 추가하는 확장 |
> |[intagg](https://www.postgresql.org/docs/11/intagg.html)                     | 1.1             | 정수 집계 및 열거자 (사용되지 않음)|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | 정수의 1차원 배열에 대한 함수, 연산자 및 인덱스 지원|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | 국제 제품 번호 지정 표준에 대한 데이터 형식|
> |[lo](https://www.postgresql.org/docs/11/lo.html)                            | 1.1             | LOB(Large Object) 유지 관리 |
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | 계층적 트리 구조에 대한 데이터 형식|
> |[pageinspect](https://www.postgresql.org/docs/11/pageinspect.html)                        | 1.7             | 낮은 수준에서 데이터베이스 페이지의 내용 검사|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | 공유 버퍼 캐시를 검사합니다|
> |[pg_cron](https://github.com/citusdata/pg_cron)                        | 1.3             | PostgreSQL용 작업 스케줄러|
> |[pg_freespacemap](https://www.postgresql.org/docs/11/pgfreespacemap.html)               | 1.2             | FSM(사용 가능한 공간 맵) 검사|
> |[pg_partman](https://github.com/pgpartman/pg_partman)         | 4.5.0           | 시간 또는 ID로 분할된 테이블을 관리하기 위한 확장 |
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | 관계 데이터 사전 준비|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | 실행된 모든 SQL 문의 실행 통계 추적|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | 트라이그램을 기반으로 하는 텍스트 유사성 측정 및 인덱스 검색|
> |[pg_visibility](https://www.postgresql.org/docs/11/pgvisibility.html)                      | 1.2             | VM(표시 유형 맵) 및 페이지 수준 표시 유형 정보 검사|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | 감사 기능 제공|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | 암호화 함수 제공|
>|[pglogical](https://github.com/2ndQuadrant/pglogical)       | 2.3.2                | 논리적 스트리밍 복제 |
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | 행 수준 잠금 정보 표시|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | 튜플 수준 통계 표시|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL 절차적 언어|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS 기하학, 지리 및 래스터 공간 유형 및 함수|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL 함수|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS 타이거 지오코더(tiger geocoder) 및 리버스 지오코더(reverse geocoder)|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS 토폴로지 공간 형식 및 함수|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | 원격 PostgreSQL 서버에 대한 외부 데이터 래퍼|
> |[sslinfo](https://www.postgresql.org/docs/11/sslinfo.html)                    | 1.2             | SSL 인증서 관련 정보|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | 크로스탭을 포함한 전체 테이블을 조작하는 함수|
> |[tsm_system_rows](https://www.postgresql.org/docs/11/tsm-system-rows.html)                    | 1.0             |  행 수를 제한으로 허용하는 TABLESAMPLE 메서드|
> |[tsm_system_time](https://www.postgresql.org/docs/11/tsm-system-time.html)                    | 1.0             |  시간(밀리초)을 제한으로 허용하는 TABLESAMPLE 메서드|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | 악센트를 제거하는 텍스트 검색 사전|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | UUID(범용 고유 식별자) 생성|


## <a name="dblink-and-postgres_fdw"></a>dblink 및 postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) 및 [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html)를 사용하면 한 PostgreSQL 서버에서 다른 PostgreSQL 서버로 또는 동일한 서버의 다른 데이터베이스로 연결할 수 있습니다. 유연한 서버는 PostgreSQL 서버에 대한 수신/발신 연결을 모두 지원합니다. 발신 서버는 수신 서버에 대한 아웃바운드 연결을 허용해야 합니다. 마찬가지로, 수신 서버는 발신 서버의 연결을 허용해야 합니다. 

이와 같은 두 가지 확장을 사용할 계획인 경우 [VNet 통합](concepts-networking.md)을 이용하여 서버를 배포하는 것이 좋습니다. 기본적으로 VNet 통합은 VNET의 서버 간 연결을 허용합니다. 또한 [VNet 네트워크 보안 그룹](../../virtual-network/manage-network-security-group.md)을 사용하여 액세스를 사용자 지정하도록 선택할 수 있습니다.

## <a name="pg_prewarm"></a>pg_prewarm

pg_prewarm 확장은 관계형 데이터를 캐시에 로드합니다. 캐시를 사전 준비하면 다시 시작한 후 쿼리를 처음 실행할 때 더 나은 응답 시간을 보입니다. 자동 사전 준비 기능은 현재 Azure Database for PostgreSQL - 유연한 서버에서 사용할 수 없습니다.

## <a name="pg_cron"></a>pg_cron

[pg_cron](https://github.com/citusdata/pg_cron/)은 데이터베이스 내부에서 확장으로 실행되는 간단한 PostgreSQL용 cron 기반 작업 스케줄러입니다. pg_cron 확장을 사용하여 PostgreSQL 데이터베이스 내에서 예약된 유지 관리 작업을 실행할 수 있습니다. 예를 들어, 테이블을 주기적으로 정리하거나 오래된 데이터 작업을 제거할 수 있습니다.

`pg_cron`은 여러 작업을 병렬로 실행할 수 있지만 한 번에 최대 하나의 작업 인스턴스를 실행합니다. 첫 번째 실행이 완료되기 전에 두 번째 실행이 시작되어야 하는 경우 첫 번째 실행이 완료되는 즉시 두 번째 실행이 대기되고 시작됩니다. 이렇게 하면 작업이 예약된 횟수만큼 정확하게 실행되고 동시에 실행되지 않습니다.

몇 가지 예는 다음과 같습니다.

토요일 오전 3시 30분(GMT)에 오래된 데이터를 삭제하려면
```
SELECT cron.schedule('30 3 * * 6', $$DELETE FROM events WHERE event_time < now() - interval '1 week'$$);
```
매일 오전 10:00(GMT)에 정리하려면
```
SELECT cron.schedule('0 10 * * *', 'VACUUM');
```

pg_cron에서 모든 작업의 예약을 취소하려면
```
SELECT cron.unschedule(jobid) FROM cron.job;
```
> [!NOTE]
> pg_cron 확장은 postgres 데이터베이스 내의 모든 Azure Database for PostgreSQL 유연한 서버에 미리 로드 되어 보안을 손상 시 키 지 않고 PostgreSQL DB 인스턴스 내의 다른 데이터베이스에서 실행 되도록 작업을 예약할 수 있는 기능을 제공 합니다.

## <a name="pg_stat_statements"></a>pg_stat_statements

[pg_stat_statements 확장](https://www.postgresql.org/docs/current/pgstatstatements.html)은 모든 Azure Database for PostgreSQL 유연한 서버에 미리 로드되어 SQL 문의 실행 통계를 추적하는 수단을 제공합니다.
확장을 통해 어떤 명령문을 계산할지 제어하는 `pg_stat_statements.track` 설정의 기본값은 `top`이며 이것은 클라이언트가 직접 실행하는 모든 명령문이 추적된다는 의미입니다. 다른 두 가지 추적 수준은 `none`과 `all`입니다. 이 설정은 서버 매개 변수로 구성할 수 있습니다.

pg_stat_statements를 통해 제공되는 쿼리 실행 정보와 각 SQL 문을 기록할 때 서버 성능에 미치는 영향 사이에는 상충 관계가 있습니다. pg_stat_statements 확장을 적극적으로 사용하지 않는 경우에는 `pg_stat_statements.track`을 `none`으로 설정하는 것이 좋습니다. 일부 타사 모니터링 서비스가 pg_stat_statements에 기반하여 쿼리 성능 정보를 제공할 수 있으므로 여기에 해당하는 경우인지 여부를 확인하십시오.


## <a name="next-steps"></a>다음 단계

사용하려는 확장이 표시되지 않으면 알려주세요. [피드백 포럼](https://feedback.azure.com/d365community/forum/c5e32b97-ee24-ec11-b6e6-000d3a4f0da0)에서 기존 요청에 투표하거나 새 피드백 요청을 작성하세요.
