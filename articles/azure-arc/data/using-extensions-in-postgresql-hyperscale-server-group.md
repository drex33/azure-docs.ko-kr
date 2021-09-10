---
title: PostgreSQL 확장 사용
description: PostgreSQL 확장 사용
titleSuffix: Azure Arc-enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 831b3e220afe826b5190588b8855b72a8d648916
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529055"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에서 PostgreSQL 확장 사용

PostgreSQL는 확장과 함께 사용할 때 가장 효율적입니다. 실제로 자체적인 하이퍼 크기 조정 기능의 주요 요소는 기본적으로 설치되는 Microsoft 제공 `citus` 확장이며,이 확장을 통해 Postgres가 여러 노드에 걸쳐 데이터를 투명하게 분할할 수 있습니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="supported-extensions"></a>지원되는 확장
표준 [`contrib`](https://www.postgresql.org/docs/12/contrib.html) 확장 및 다음 확장은 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 컨테이너에 이미 배포되어 있습니다.
- [`citus`](https://github.com/citusdata/citus), v: 10.0. [Citus 데이터](https://www.citusdata.com/)의 Citus 확장은 PostgreSQL 엔진에 하이퍼 크기 조정 기능을 제공하므로 기본적으로 로드됩니다. Azure Arc PostgreSQL 하이퍼스케일 서버 그룹에서 Citus 확장을 삭제하는 것은 지원되지 않습니다.
- [`pg_cron`](https://github.com/citusdata/pg_cron), v: 1.3
- [`pgaudit`](https://www.pgaudit.org/), v: 1.4
- plpgsql, v: 1.0
- [`postgis`](https://postgis.net), v: 3.0.2
- [`plv8`](https://plv8.github.io/), v: 2.3.14
- [`pg_partman`](https://github.com/pgpartman/pg_partman), v: 4.4.1/
- [`tdigest`](https://github.com/tvondra/tdigest), v: 1.0.1

이 목록에 대한 업데이트는 시간이 지남에 따라 진화함에 따라 게시됩니다.

> [!IMPORTANT]
> 이 미리 보기에서는 서버 그룹에 위에 나열된 것 이외의 확장을 제공할 수 있지만, 시스템에는 보관되지 않습니다. 즉, 시스템을 다시 시작한 후에는 사용할 수 없으며, 다시 가져와야 합니다.

이 가이드에서는 다음 두 가지 확장을 사용하는 시나리오를 살펴보겠습니다.
- [`PostGIS`](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)

## <a name="which-extensions-need-to-be-added-to-the-shared_preload_libraries-and-created"></a>Shared_preload_libraries에 추가하고 만들어야 하는 확장은 무엇인가요?

|확장   |Shared_preload_libraries에 추가되어야 합니다.  |생성되어야 함 |
|-------------|--------------------------------------------------|---------------------- |
|`pg_cron`      |아니요       |예        |
|`pg_audit`     |예       |예        |
|`plpgsql`      |예       |예        |
|`postgis`      |아니요       |예        |
|`plv8`      |아니요       |예        |

## <a name="add-extensions-to-the-shared_preload_libraries"></a>`shared_preload_libraries`에 확장 추가
`shared_preload_libraries`에 대한 자세한 내용은 [여기](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SHARED-PRELOAD-LIBRARIES)에서 PostgreSQL 설명서를 참조하세요.
- `contrib`의 부분인 확장에는 이 단계가 필요하지 않습니다.
- shared_preload_libraries에 의해 미리 로드하지 않아도 되는 확장에는 이 단계가 필요하지 않습니다. 이러한 확장의 경우 다음 단락 [확장 만들기](#create-extensions)로 이동할 수 있습니다.

### <a name="add-an-extension-at-the-creation-time-of-a-server-group"></a>서버 그룹을 만들 때 확장 추가
```azurecli
az postgres arc-server create -n <name of your postgresql server group> --extensions <extension names>
```
### <a name="add-an-extension-to-an-instance-that-already-exists"></a>이미 존재하는 인스턴스에 확장 추가
```azurecli
az postgres arc-server server edit -n <name of your postgresql server group> --extensions <extension names>
```




## <a name="show-the-list-of-extensions-added-to-shared_preload_libraries"></a>Shared_preload_libraries에 추가된 확장 목록 표시
다음 명령 중 하나를 실행합니다.

### <a name="with-cli-command"></a>CLI 명령을 사용하여
```azurecli
az postgres arc-server show -n <server group name>
```
출력에서 스크롤하고 서버 그룹의 사양에 engine\extensions 섹션이 있는지 확인합니다. 예를 들면 다음과 같습니다.
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
### <a name="with-kubectl"></a>kubectl을 사용하여
```console
kubectl describe postgresql-12s/postgres02
```
출력에서 스크롤하고 서버 그룹의 사양에 engine\extensions 섹션이 있는지 확인합니다. 예를 들면 다음과 같습니다.
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


## <a name="create-extensions"></a>확장 만들기
선택한 클라이언트 도구를 사용해 서버 그룹에 연결하고 표준 PostgreSQL 쿼리를 실행합니다.
```console
CREATE EXTENSION <extension name>;
```

## <a name="show-the-list-of-extensions-created"></a>생성된 확장 목록 표시
선택한 클라이언트 도구를 사용해 서버 그룹에 연결하고 표준 PostgreSQL 쿼리를 실행합니다.
```console
select * from pg_extension;
```

## <a name="drop-an-extension"></a>확장을 끊습니다.
선택한 클라이언트 도구를 사용해 서버 그룹에 연결하고 표준 PostgreSQL 쿼리를 실행합니다.
```console
drop extension <extension name>;
```

## <a name="the-postgis-extension"></a>`PostGIS`확장.
`shared_preload_libraries`에 `PostGIS` 확장을 추가할 필요가 없습니다.
MIT의 도시 연구 & 계획 부서에서 [샘플 데이터](http://duspviz.mit.edu/tutorials/intro-postgis/)를 가져오십시오. 필요에 따라 압축 풀기를 설치하려면 `apt-get install unzip`을 실행합니다.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

데이터베이스에 연결하고 `PostGIS` 확장을 만듭니다.

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> 패키지의 확장 중 하나를 사용하려면 `postgis` (예:, `postgis_raster`, `postgis_topology`, `postgis_sfcgal`, `fuzzystrmatch`...) 먼저 postgis 확장을 만든 다음 다른 확장을 만들어야 합니다. 예: `CREATE EXTENSION postgis`; `CREATE EXTENSION postgis_raster`;

그리고 스키마를 만듭니다.

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

이제 coffee_shops 테이블을 분산하여 스케일 아웃 기능과 `PostGIS`이 결합할 수 있습니다.

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

몇 가지 데이터를 로드해 보겠습니다.

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

그런 다음 `PostGIS` `geometry` 데이터 형식에서 올바르게 인코딩된 위도 및 경도로 `geom` 필드를 채웁니다.

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

이제 MIT (77 매사추세츠 Ave at 42.359055,-71.093500)에 가장 가까운 커피숍을 나열할 수 있습니다.

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


## <a name="the-pg_cron-extension"></a>`pg_cron` 확장.

이제 shared_preload_libraries에 추가하여 PostgreSQL 서버 그룹에 `pg_cron`을 사용하도록 설정합니다.

```azurecli
az postgres arc-server update -n pg2 -ns arc --extensions pg_cron
```

서버 그룹은 확장 설치 완료를 다시 시작합니다. 2-3분 정도 걸릴 수 있습니다.

이제 다시 연결하여 `pg_cron` 확장을 만들 수 있습니다.

```sql
CREATE EXTENSION pg_cron;
```

테스트를 위해 앞의 `coffee_shops` 테이블에서 임의의 이름을 사용하는 테이블 `the_best_coffee_shop`을 만들고 테이블 내용을 삽입합니다.

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

`cron.schedule`뿐만 아니라 몇 가지 SQL 문을 사용하여 임의의 테이블 이름을 가져오고 (분산 쿼리 결과를 저장하는 임시 테이블을 사용하는 알림), `the_best_coffee_shop`에 저장할 수 있습니다.

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

이제 1분에 한 번은 다른 이름을 가져옵니다.

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

구문에 대한 자세한 내용은 [pg_cron README](https://github.com/citusdata/pg_cron) 정보를 참조하세요.


## <a name="next-steps"></a>다음 단계
- [`plv8`](https://plv8.github.io/)에 대한 설명서 읽기
- [`PostGIS`](https://postgis.net/)에 대한 설명서 읽기
- [`pg_cron`](https://github.com/citusdata/pg_cron)에 대한 설명서 읽기