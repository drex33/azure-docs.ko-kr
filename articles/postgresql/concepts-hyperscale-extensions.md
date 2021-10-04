---
title: 확장 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL - 하이퍼스케일(Citus)에서 확장을 사용하여 데이터베이스의 기능을 확장하는 방법을 설명합니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 098f955894762908d802862fe91b5cfa9e2595ec
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401886"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL - 하이퍼스케일(Citus)의 PostgreSQL 확장

PostgreSQL은 확장을 사용하여 데이터베이스 기능을 확장할 수 있도록 지원합니다. 확장을 통해 관련된 여러 SQL 개체를 단일 패키지에 번들로 묶을 수 있으며 단일 명령을 사용해서 데이터베이스에서 로드하거나 제거할 수 있습니다. 데이터베이스에 로드된 확장은 기본 제공 기능처럼 작동할 수 있습니다. PostgreSQL 확장에 대한 자세한 내용은 [관련 개체를 확장으로 패키지](https://www.postgresql.org/docs/current/static/extend-extensions.html)를 참조하세요.

## <a name="use-postgresql-extensions"></a>PostgreSQL 확장 사용

PostgreSQL 확장을 사용하려면 먼저 데이터베이스에 설치해야 합니다. 특정 확장을 설치하려면 psql 도구에서 [CREATE EXTENSION](https://www.postgresql.org/docs/current/static/sql-createextension.html) 명령을 실행하여 패키지 개체를 데이터베이스에 로드합니다.

> [!NOTE]
> `CREATE EXTENSION`이 권한 거부 오류와 함께 실패하면 대신 `create_extension()` 함수를 시도합니다. 예를 들면 다음과 같습니다.
>
> ```sql
> SELECT create_extension('postgis');
> ```

Azure Database for PostgreSQL - 하이퍼스케일(Citus)은 현재 여기에 나열된 대로 키 확장의 하위 집합을 지원합니다. 나열된 항목 이외의 확장은 지원되지 않습니다. Azure Database for PostgreSQL로 사용자 고유의 확장을 만들 수 없습니다.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>PostgreSQL용 Azure 데이터베이스에서 지원하는 확장

다음 표에는 PostgreSQL용 Azure 데이터베이스에서 현재 지원하는 표준 PostgreSQL 확장이 나와 있습니다. 이 정보를 `SELECT * FROM pg_available_extensions;`를 실행하여 얻을 수도 있습니다.

서버 그룹에 설치된 각 확장의 버전은 PostgreSQL의 버전(11, 12 또는 13)에 따라 다를 수 있습니다. 표에 데이터베이스 버전별로 확장 버전이 나열되어 있습니다.

### <a name="citus-extension"></a>Citus 확장

> [!div class="mx-tableFixed"]
> | **내선 번호** | **설명** | **PG 11** | **PG 12** | **PG 13** | **PG 14** |
> |---|---|---|---|---|
> | [citus](https://github.com/citusdata/citus) | Citus 분산 데이터베이스입니다. | 9.5-2 | 10.0-3 | 10.1-1 | 10.2-2 |

### <a name="data-types-extensions"></a>데이터 형식 확장

> [!div class="mx-tableFixed"]
> | **내선 번호** | **설명** | **PG 11** | **PG 12** | **PG 13** | **PG 14** |
> |---|---|---|---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | 대/소문자 구분 문자 문자열 형식을 제공합니다. | 1.5 | 1.6 | 1.6 | 1.6 |
> | [cube](https://www.postgresql.org/docs/current/static/cube.html) | 다차원 큐브의 데이터 형식을 제공합니다. | 1.4 | 1.4 | 1.4 | 1.5 |
> | [hll](https://github.com/citusdata/postgresql-hll) | HyperLogLog 데이터 구조를 제공합니다. | 2.15 | 2.15 | 2.16 | 2.16 |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | 키/값 쌍 집합을 저장하기 위한 데이터 형식을 제공합니다. | 1.5 | 1.6 | 1.7 | 1.8 |
> | [isn](https://www.postgresql.org/docs/current/static/isn.html) | 국제 제품 번호 매기기 표준에 대한 데이터 형식을 제공합니다. | 1.2 | 1.2 | 1.2 | 1.2 |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | LOB(Large Object) 유지 관리입니다. | 1.1 | 1.1 | 1.1 | 1.1 |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | 계층적 트리 구조에 대한 데이터 형식을 제공합니다. | 1.1 | 1.1 | 1.2 | 1.2 |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | 직선 세그먼트 또는 부동 소수점 간격을 나타내는 데이터 형식입니다. | 1.3 | 1.3 | 1.3 | 1.4 |
> | [tdigest](https://github.com/tvondra/tdigest) | 분위수 및 절사평균과 같은 순위 기반 통계의 온라인 누적을 위한 데이터 형식입니다. | 1.0 | 1.0 | 1.2.0 | 1.2.0 |
> | [topn](https://github.com/citusdata/postgresql-topn/) | top-n JSONB에 대한 형식입니다. | 2.3.1 | 2.3.1 | 2.4.0 | 2.4.0 |

### <a name="full-text-search-extensions"></a>전체 텍스트 검색 확장

> [!div class="mx-tableFixed"]
> | **내선 번호** | **설명** | **PG 11** | **PG 12** | **PG 13** | **PG 14** |
> |---|---|---|---|---|
> | [dict\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | 정수에 대한 텍스트 검색 사전 템플릿을 제공합니다. | 1.0 | 1.0 | 1.0 | 1.0 |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | 확장된 동의어 처리를 위한 텍스트 검색 사전 템플릿입니다. | 1.0 | 1.0 | 1.0 | 1.0 |
> | [unaccent](https://www.postgresql.org/docs/current/static/unaccent.html) | Lexemes에서 악센트(분음 기호)를 제거하는 텍스트 검색 사전입니다. | 1.1 | 1.1 | 1.1 | 1.1 |

### <a name="functions-extensions"></a>함수 확장

> [!div class="mx-tableFixed"]
> | **내선 번호** | **설명** | **PG 11** | **PG 12** | **PG 13** | **PG 14** |
> |---|---|---|---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | 자동 증가 필드를 위한 함수입니다. | 1.0 | 1.0 | 1.0 | 1.0 |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | 지구 표면의 대원 거리를 계산하는 방법을 제공합니다. | 1.1 | 1.1 | 1.1 | 1.1 |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | 문자열 간 유사성 및 거리를 확인하기 위한 몇 가지 함수를 제공합니다. | 1.1 | 1.1 | 1.1 | 1.1 |
> | [insert\_username](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | 테이블을 변경한 사람을 추적하는 함수입니다. | 1.0 | 1.0 | 1.0 | 1.0 |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | 정수 집계 및 열거자입니다(사용되지 않음). | 1.1 | 1.1 | 1.1 | 1.1 |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | null 없는 정수 배열을 조작하기 위한 함수 및 연산자를 제공합니다. | 1.2 | 1.2 | 1.3 | 1.5 |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | 마지막 수정 시간을 추적하는 함수입니다. | 1.0 | 1.0 | 1.0 | 1.0 |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | 시간 또는 ID로 분할된 테이블을 관리합니다. | 4.5.1 | 4.5.1 | 4.5.1 | 4.5.1 |
> | [pg\_trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | trigram 일치를 기준으로 영숫자 텍스트의 유사성을 확인하기 위한 함수 및 연산자를 제공합니다. | 1.4 | 1.4 | 1.5 | 1.6 |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | 암호화 함수를 제공합니다. | 1.3 | 1.3 | 1.3 | 1.3 |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | 참조 무결성을 구현하는 함수입니다(사용되지 않음). | 1.0 | 1.0 | 1.0 | 1.0 |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | 크로스탭을 비롯하여 전체 테이블을 조작하는 함수를 제공합니다. | 1.0 | 1.0 | 1.0 | 1.0 |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | 트리거된 변경 알림입니다. | 1.0 | 1.0 | 1.0 | 1.0 |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | 시간 이동 구현을 위한 함수입니다. | 1.0 | | | |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | UUID(범용 고유 식별자)를 생성합니다. | 1.1 | 1.1 | 1.1 | 1.1 |

### <a name="index-types-extensions"></a>인덱스 형식 확장

> [!div class="mx-tableFixed"]
> | **내선 번호** | **설명** | **PG 11** | **PG 12** | **PG 13** | **PG 14** |
> |---|---|---|---|---|
> | [bloom](https://www.postgresql.org/docs/current/bloom.html) | 블룸 액세스 메서드 - 서명 파일 기반 인덱스입니다. | 1.0 | 1.0 | 1.0 | 1.0 |
> | [btree\_gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | 특정 데이터 형식에 대해 B-트리 유사 동작을 구현하는 샘플 GIN 연산자 클래스를 제공합니다. | 1.3 | 1.3 | 1.3 | 1.3 |
> | [btree\_gist](https://www.postgresql.org/docs/current/static/btree-gist.html) | B-트리를 구현하는 GiST 인덱스 연산자 클래스를 제공합니다. | 1.5 | 1.5 | 1.5 | 1.6 |

### <a name="language-extensions"></a>언어 확장

> [!div class="mx-tableFixed"]
> | **내선 번호** | **설명** | **PG 11** | **PG 12** | **PG 13** | **PG 14** |
> |---|---|---|---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | PL/pgSQL 로드 가능 절차 언어. | 1.0 | 1.0 | 1.0 | 1.0 |

### <a name="miscellaneous-extensions"></a>기타 확장

> [!div class="mx-tableFixed"]
> | **내선 번호** | **설명** | **PG 11** | **PG 12** | **PG 13** | **PG 14** |
> |---|---|---|---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | PostgreSQL에 대한 관리 기능입니다. | 2.0 | 2.0 | 2.1 | 2.1 |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | 관계 무결성을 확인하는 함수입니다. | 1.1 | 1.2 | 1.2 | 1.3 |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | 데이터베이스 세션 내에서 다른 PostgreSQL 데이터베이스로의 연결을 지원하는 모듈입니다. 이 확장에 대한 정보는 "dblink 및 postgres_fdw" 섹션을 참조하세요. | 1.2 | 1.2 | 1.2 | 1.2 |
> | [file\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | 플랫 파일 액세스를 위한 외래 데이터 래퍼입니다. | 1.0 | 1.0 | 1.0 | 1.0 |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | 낮은 수준에서 데이터베이스 페이지의 콘텐츠를 검사합니다. | 1.7 | 1.7 | 1.8 | 1.9 |
> | [pg\_buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | 공유 버퍼 캐시에서 일어나는 작업을 실시간으로 검사하기 위한 수단을 제공합니다. | 1.3 | 1.3 | 1.3 | 1.3 |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | PostgreSQL용 작업 스케줄러입니다. | 1.3 | 1.3 | 1.3 | 1.4 |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | FSM(사용 가능한 공간 맵)을 검사합니다. | 1.2 | 1.2 | 1.2 | 1.2 |
> | [pg\_prewarm](https://www.postgresql.org/docs/current/static/pgprewarm.html) | 관계 데이터를 버퍼 캐시에 로드하는 방법을 제공합니다. | 1.2 | 1.2 | 1.2 | 1.2 |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | 서버에서 실행되는 모든 SQL 문의 실행 통계를 추적하는 수단을 제공합니다. 이 확장에 대한 정보는 "pg_stat_statements" 섹션을 참조하세요. | 1.6 | 1.7 | 1.8 | 1.9 |
> | [pg\_visibility](https://www.postgresql.org/docs/current/pgvisibility.html) | VM(표시 유형 맵) 및 페이지 수준 표시 유형 정보를 검사합니다. | 1.2 | 1.2 | 1.2 | 1.2 |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | 행 수준 잠금 정보를 표시하는 방법을 제공합니다. | 1.2 | 1.2 | 1.2 | 1.2 |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | 튜플 수준 통계를 표시하는 방법을 제공합니다. | 1.5 | 1.5 | 1.5 | 1.5 |
> | [postgres\_fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | 외부 PostgreSQL 서버에 저장된 데이터에 액세스하는 데 사용되는 외부 데이터 래퍼입니다. 이 확장에 대한 정보는 "dblink 및 postgres_fdw" 섹션을 참조하세요.| 1.0 | 1.0 | 1.0 | 1.1 |
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | TLS/SSL 인증서에 대한 정보입니다. | 1.2 | 1.2 | 1.2 | 1.2 |
> | [tsm\_system\_rows](https://www.postgresql.org/docs/current/tsm-system-rows.html) | 행 수를 제한값으로 사용하는 TABLESAMPLE 메서드입니다. | 1.0 | 1.0 | 1.0 | 1.0 |
> | [tsm\_system\_time](https://www.postgresql.org/docs/current/tsm-system-time.html) | 밀리초 단위의 시간을 제한값으로 사용하는 TABLESAMPLE 메서드입니다. | 1.0 | 1.0 | 1.0 | 1.0 |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath 쿼리 및 XSLT입니다. | 1.1 | 1.1 | 1.1 | 1.1 |


### <a name="postgis-extensions"></a>PostGIS 확장

> [!div class="mx-tableFixed"]
> | **내선 번호** | **설명** | **PG 11** | **PG 12** | **PG 13** | **PG 14** |
> |---|---|---|---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | PostgreSQL에 대한 공간 및 지리적 개체입니다. | 2.5.5 | 3.0.3 | 3.0.3 | 3.1.4 |
> | address\_standardizer, address\_standardizer\_data\_us | 주소를 구성 요소로 구문 분석하는 데 사용됩니다. 지오코딩 주소 정규화 단계를 지원하는 데 사용됩니다. | 2.5.5 | 3.0.3 | 3.0.3 | 3.1.4 |
> | postgis\_sfcgal | PostGIS SFCGAL 함수입니다. | 2.5.5 | 3.0.3 | 3.0.3 | 3.1.4 |
> | postgis\_tiger\_geocoder | PostGIS 타이거 지오코더(tiger geocoder) 및 리버스 지오코더(reverse geocoder) | 2.5.5 | 3.0.3 | 3.0.3 | 3.1.4 |
> | postgis\_topology | PostGIS는 토폴로지 공간 유형 및 함수입니다. | 2.5.5 | 3.0.3 | 3.0.3 | 3.1.4 |


## <a name="pg_stat_statements"></a>pg_stat_statements
[pg\_stat\_statements 확장](https://www.postgresql.org/docs/current/pgstatstatements.html)은 모든 Azure Database for PostgreSQL 서버에 미리 로드되어 SQL 문의 실행 통계를 추적하는 방법을 제공합니다.

`pg_stat_statements.track` 설정은 확장에서 계산되는 문을 제어합니다. 기본적으로 `top`으로 설정되는데 이는 클라이언트에서 직접 실행한 모든 문이 추적되는 것을 의미합니다. 다른 두 가지 추적 수준은 `none`과 `all`입니다. 이 설정은 [Azure Portal](./howto-configure-server-parameters-using-portal.md)이나 [Azure CLI](./howto-configure-server-parameters-using-cli.md)를 통해 서버 매개 변수로 구성할 수 있습니다.

pg_stat_statements를 통해 제공되는 쿼리 실행 정보와 각 SQL 문을 기록할 때 서버 성능에 미치는 영향 사이에는 상충 관계가 있습니다. pg_stat_statements 확장을 적극적으로 사용하지 않는 경우에는 `pg_stat_statements.track`을 `none`으로 설정하는 것이 좋습니다. 일부 타사 모니터링 서비스가 pg_stat_statements에 기반하여 쿼리 성능 인사이트를 제공할 수 있으므로 여기에 해당하는 경우인지 여부를 확인합니다.

## <a name="dblink-and-postgres_fdw"></a>dblink 및 postgres_fdw

dblink 및 postgres\_fdw를 사용하여 한 PostgreSQL 서버에서 다른 PostgreSQL 서버로 또는 동일한 서버의 다른 데이터베이스로 연결할 수 있습니다.  받는 서버는 해당 방화벽을 통한 보내는 서버의 연결을 허용해야 합니다.  이러한 확장을 사용하여 Azure Database for PostgreSQL 서버 또는 하이퍼스케일(Citus) 서버 그룹 간에 연결하려면 **Azure 서비스 및 리소스에서 이 서버 그룹(또는 서버)에 액세스할 수 있도록 허용** 을 ON으로 설정합니다.  또한 확장을 사용하여 동일한 서버로 루프백하려는 경우 이 설정을 켜야 합니다.
**Azure 서비스 및 리소스에서 이 서버 그룹에 액세스할 수 있도록 허용** 설정은 **네트워킹** 아래의 하이퍼스케일(Citus) 서버 그룹에 대한 Azure Portal 페이지에서 찾을 수 있습니다.  현재는 다른 Azure Database for PostgreSQL 서버 및 하이퍼스케일(Citus) 서버 그룹에 대한 연결을 제외하고 Azure Database for PostgreSQL 단일 서버 및 하이퍼스케일(Citus)에서의 아웃바운드 연결은 지원되지 않습니다.
