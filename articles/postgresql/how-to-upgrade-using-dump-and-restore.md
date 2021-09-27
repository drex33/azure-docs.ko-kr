---
title: 덤프 및 복원을 사용하여 업그레이드 - Azure Database for PostgreSQL
description: 데이터베이스의 덤프 및 복원을 사용하여 Azure Database for PostgreSQL 상위 버전으로 마이그레이션하기 위한 오프라인 업그레이드 방법에 대해 설명합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/21/2021
ms.openlocfilehash: b2216754cbdb6081a82f71392aee6e5f8ce2d3ba
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128648414"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>덤프 및 복원을 사용하여 PostgreSQL 데이터베이스 업그레이드

>[!NOTE]
> 이 설명서에 나온 개념은 Azure Database for PostgreSQL 단일 서버 및 Azure Database for PostgreSQL 유연한 서버(미리 보기) 모두에 적용됩니다. 

다음 방법을 사용하여 데이터베이스를 더 높은 주 버전 서버로 마이그레이션하여 Azure Database for PostgreSQL에 배포된 PostgreSQL 서버를 업그레이드할 수 있습니다.
* **오프라인** 방법: PostgreSQL [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 및 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)를 사용하며 데이터 마이그레이션에 대한 가동 중지 시간이 발생합니다. 이 문서에서는 이 업그레이드/마이그레이션 방법에 대해 다룹니다.
* **온라인** 방법: [DMS(Database Migration Service)](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md)를 사용합니다. 이 방법을 사용하면 가동 중지 시간을 줄일 수 있으며 대상 데이터베이스를 원본과 동기화 상태로 유지하고, 이를 잘라낼 시기를 선택할 수 있습니다. 그러나 DMS를 사용하기 위해 몇 가지 필수 구성 요소 및 제한 사항을 해결해야 합니다. 자세한 내용은 [DMS 설명서](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md)를 참조하세요. 

 다음 표에는 데이터베이스 크기 및 시나리오에 따른 몇 가지 권장 사항이 나와 있습니다.

| **데이터베이스/시나리오** | **덤프/복원(오프라인)** | **DMS(온라인)** |
| ------ | :------: | :-----: |
| 데이터베이스가 작고 업그레이드하는 데 가동 중지 시간을 감당할 수 있습니다.  | X | |
| 소규모 데이터베이스(< 10GB)  | X | X | 
| 중소 DB(10GB – 100GB) | X | X |
| 대용량 데이터베이스(> 100GB) |  | X |
| 데이터베이스 크기와 관계 없이 업그레이드하는 데 가동 중지 시간을 감당할 수 있습니다. | X |  |
| 다시 부팅을 포함하여 DMS [필수 구성 요소](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md#prerequisites)를 처리할 수 있나요? |  | X |
| 업그레이드 프로세스 중에 DDL 및 기록되지 않은 테이블을 피할 수 있나요? | |  X |

이 가이드에는 원본 서버에서 더 높은 버전의 PostgreSQL를 실행하는 대상 서버로 마이그레이션하는 방법을 보여 주는 몇 가지 오프라인 마이그레이션 방법과 예제가 나와 있습니다.

> [!NOTE]
> PostgreSQL 덤프 및 복원은 여러 가지 방법으로 수행할 수 있습니다. 이 가이드에 제공된 방법 중 하나를 사용하여 마이그레이션하도록 선택하거나 필요에 따라 다른 방법을 선택할 수 있습니다. 추가 매개 변수를 사용하는 자세한 덤프 및 복원 구문은 [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 및 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) 문서를 참조하세요. 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-database-for-postgresql"></a>Azure Database for PostgreSQL에서 덤프 및 복원을 사용하기 위한 필수 구성 요소
 
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.

- 업그레이드하려는 더 낮은 버전의 엔진을 실행하는 **원본** PostgreSQL 데이터베이스 서버.
- 원하는 주 버전 [Azure Database for PostgreSQL 서버 단일 서버](quickstart-create-server-database-portal.md) 또는 [Azure Database for PostgreSQL 유연한 서버](./flexible-server/quickstart-create-server-portal.md)를 포함하는 **대상** PostgreSQL 데이터베이스 서버. 
- 덤프 및 복원 명령을 실행할 PostgreSQL 클라이언트 시스템. 더 높은 버전의 데이터베이스를 사용하는 것이 좋습니다. 예를 들어 PostgreSQL 버전 9.6에서 11로 업그레이드하는 경우 PostgreSQL 버전 11 클라이언트를 사용하세요. 
  - PostgreSQL이 설치된 Linux 또는 Windows 클라이언트일 수 있으며, [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 및 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) 명령줄 유틸리티가 설치되어 있습니다. 
  - 또는 [Azure Cloud Shell](https://shell.azure.com)을 사용하거나 [Azure Portal](https://portal.azure.com)의 오른쪽 위에 있는 메뉴 모음에서 Azure Cloud Shell을 클릭하여 사용할 수 있습니다. 덤프 및 복원 명령을 실행하기 전에 `az login` 계정에 로그인해야 합니다.
- PostgreSQL 클라이언트는 원본 및 대상 서버와 동일한 지역에서 실행되는 것이 좋습니다. 


## <a name="additional-details-and-considerations"></a>추가 세부 정보 및 고려 사항
- 포털에서 “연결 문자열”을 클릭하여 원본 및 대상 데이터베이스에 대한 연결 문자열을 찾을 수 있습니다. 
- 서버에서 둘 이상의 데이터베이스를 실행 중일 수 있습니다. 원본 서버에 연결하고 `\l`를 실행하여 데이터베이스 목록을 찾을 수 있습니다.
- 대상 데이터베이스 서버에서 해당 데이터베이스를 만들거나 데이터베이스를 만드는 `pg_dump` 명령에 `-C` 옵션을 추가합니다.
- `azure_maintenance` 또는 템플릿 데이터베이스를 업그레이드 해서는 안 됩니다. 템플릿 데이터베이스를 변경한 경우 변경 내용을 마이그레이션하거나 대상 데이터베이스를 변경하도록 선택할 수 있습니다.
- 이 마이그레이션 모드에 적합한 데이터베이스를 확인하려면 위의 표를 참조하세요.
- Azure Cloud Shell을 사용하려면 20분 후 세션 시간이 초과되는 것을 참고하세요. 데이터베이스 크기가 10GB 미만인 경우 세션 시간이 초과되지 않고 업그레이드를 완료할 수 있습니다. 그렇지 않은 경우 10~15분에 한 번 아무 키나 누르는 등 다른 방법으로 세션을 열어 두어야 할 수 있습니다. 


## <a name="example-database-used-in-this-guide"></a>이 가이드에 사용되는 예제 데이터베이스

이 가이드에서는 예제를 보여 주기 위해 다음 원본 및 대상 서버와 데이터베이스 이름을 사용합니다.

 | **설명** | **값** |
 | ------- | ------- |
 | 원본 서버(v9.5) | pg-95.postgres.database.azure.com |
 | 원본 데이터베이스 | bench5gb |
 | 원본 데이터베이스 크기 | 5GB |
 | 원본 사용자 이름 | pg@pg-95 |
 | 대상 서버(v11) | pg-11.postgres.database.azure.com |
 | 대상 데이터베이스 | bench5gb |
 | 대상 사용자 이름 | pg@pg-11 |

>[!NOTE]
> 유연한 서버는 PostgreSQL 버전 11 이상을 지원합니다. 또한 유연한 서버 사용자 이름에 @dbservername이 필요하지 않습니다.

## <a name="upgrade-your-databases-using-offline-migration-methods"></a>오프라인 마이그레이션 방법을 사용하여 데이터베이스 업그레이드
업그레이드에 대해 이 섹션에서 설명하는 방법 중 하나를 사용하도록 선택할 수 있습니다. 작업을 수행하는 동안 다음 팁을 사용할 수 있습니다.

- 원본 및 대상 데이터베이스에 대해 동일한 암호를 사용하는 경우 `PGPASSWORD=yourPassword` 환경 변수를 설정할 수 있습니다.  그러면 psql, pg_dump 및 pg_restore와 같은 명령을 실행할 때마다 암호를 제공할 필요가 없습니다.  마찬가지로, `PGUSER`, `PGSSLMODE` 등의 추가 변수를 설정할 수 있습니다. [PostgreSQL 환경 변수](https://www.postgresql.org/docs/11/libpq-envars.html)를 참조하세요.
  
- PostgreSQL 서버에 TLS/SSL 연결이 필요한 경우(기본적으로 Azure Database for PostgreSQL 서버에서), pg_restore 도구가 TLS와 연결되도록 환경 변수 `PGSSLMODE=require`를 설정합니다. TLS를 사용하지 않으면 `FATAL:  SSL connection is required. Please specify SSL options and retry.`라는 오류가 발생할 수 있습니다.

- Windows 명령줄에서 pg_restore 명령을 실행하기 전에 명령 `SET PGSSLMODE=require`를 실행합니다. Linux 또는 Bash에서 pg_restore 명령을 실행하기 전에 명령 `export PGSSLMODE=require`를 실행합니다.

>[!Important]
> 이 문서에서 제공하는 단계 및 방법은 pg_dump/pg_restore 명령의 몇 가지 예제를 제공하기 위한 것이며, 업그레이드를 수행할 수 있는 모든 방법을 나타내는 것이 아닙니다. 프로덕션에서 사용하기 전에 테스트 환경에서 명령을 테스트하고 유효성을 검사하는 것이 좋습니다.

### <a name="migrate-the-roles"></a>역할 마이그레이션

역할(사용자)은 전역 개체이며, 데이터베이스를 복원하기 전에 별도로 새 클러스터로 마이그레이션해야 합니다. `pg_dumpall` 이진 파일을 -r (--roles-only) 옵션과 함께 사용하여 역할을 덤프할 수 있습니다.
원본 서버에서 모든 역할을 덤프하려면

```azurecli-interactive
pg_dumpall -r --host=mySourceServer --port=5432 --username=myUser --database=mySourceDB > roles.sql
```

`roles.sql` `NOSUPERUSER` `NOBYPASSRLS` 대상 서버에서 psql을 사용 하 여 콘텐츠를 복원 하기 전에을 편집 하 고 및의 참조를 제거 합니다.

```azurecli-interactive
psql -f roles.sql --host=myTargetServer --port=5432 --username=myUser
```

덤프 스크립트는 오류 없이 완전히 실행될 수 없습니다. 특히 스크립트는 원본 클러스터에 있는 모든 역할에 대해 CREATE ROLE을 실행하기 때문에 azure_pg_admin 또는 azure_superuser 같은 부트스트랩 슈퍼 사용자에 대해 "역할이 이미 있습니다."라는 오류 메시지가 표시됩니다. 이것은 치명적인 오류가 아니므로 무시해도 됩니다. `--clean` 옵션을 사용하면 존재하지 않는 개체에 대한 무해한 오류 메시지가 추가로 생성될 수 있지만 `--if-exists`를 추가하면 이를 최소화할 수 있습니다.


### <a name="method-1-using-pg_dump-and-psql"></a>방법 1: pg_dump 및 psql 사용

이 방법에는 두 단계가 포함됩니다. 먼저, `pg_dump`를 사용하여 원본 서버에서 SQL 파일을 덤프합니다. 다음으로, `psql`을 사용하여 파일을 대상 서버로 가져옵니다. 자세한 내용은 [내보내기 및 가져오기를 사용하여 마이그레이션](howto-migrate-using-export-and-import.md) 설명서를 참조하세요.

### <a name="method-2-using-pg_dump-and-pg_restore"></a>방법 2: pg_dump 및 pg_restore 사용

이 업그레이드 방법에서는 먼저 `pg_dump`를 사용하여 원본 서버에서 덤프를 만듭니다. 그런 다음 `pg_restore`를 사용하여 해당 덤프 파일을 대상 서버로 복원합니다. 자세한 내용은 [덤프 및 복원을 사용한 마이그레이션](howto-migrate-using-dump-and-restore.md)을 참조하세요. 

### <a name="method-3-using-streaming-the-dump-data-to-the-target-database"></a>방법 3: 덤프 데이터를 대상 데이터베이스로 스트리밍 사용

PostgreSQL 클라이언트가 없거나 Azure Cloud Shell을 사용하려는 경우 이 방법을 사용할 수 있습니다. 데이터베이스 덤프는 대상 데이터베이스 서버로 직접 스트리밍되며 덤프를 클라이언트에 저장하지 않습니다. 따라서 클라이언트는 제한된 스토리지와 함께 사용할 수 있으며 Azure Cloud Shell에서도 실행할 수 있습니다. 

1. `\l` 명령을 사용하여 대상 서버에 데이터베이스가 있는지 확인합니다. 데이터베이스가 없는 경우 데이터베이스를 만듭니다.
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```SQL
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. 파이프를 사용하여 덤프를 실행하고 하나의 명령줄로 복원합니다. 
    ```azurecli-interactive
    pg_dump -Fc --host=mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore  --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    예를 들면 다음과 같습니다.

    ```azurecli-interactive
    pg_dump -Fc --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. 업그레이드(마이그레이션) 프로세스가 완료되면 대상 서버를 사용하여 애플리케이션을 테스트할 수 있습니다. 
4. 서버 내의 모든 데이터베이스에 대해 이 프로세스를 반복합니다.

 예를 들어 다음 표에서는 덤프 스트리밍 방법을 사용하여 마이그레이션하는 데 걸린 시간을 보여 줍니다. 샘플 데이터는 [pgbench](https://www.postgresql.org/docs/10/pgbench.html)를 사용하여 채워집니다. 데이터베이스의 개체 수가 pgbench에서 생성된 테이블 및 인덱스와 다른 개체 수를 가질 수 있으므로 데이터베이스를 업그레이드하는 데 걸리는 실제 시간을 이해하기 위해 데이터베이스의 덤프와 복원을 테스트하는 것이 좋습니다. 

| **데이터베이스 크기** | **대략적인 소요 시간** | 
| ----- | ------ |
| 1GB  | 1-2분 |
| 5GB | 8-10분 |
| 10 GB | 15-20분 |
| 50GB | 1-1.5시간 |
| 100GB | 2.5-3시간|
   
### <a name="method-4-using-parallel-dump-and-restore"></a>방법 4: 병렬 덤프 및 복원 사용 

데이터베이스에 더 많은 테이블이 있고 해당 데이터베이스의 덤프 및 복원 프로세스를 병렬화하려는 경우 이 방법을 고려할 수 있습니다. 또한 백업 덤프를 수용할 수 있도록 클라이언트 시스템에 충분한 스토리지가 필요합니다. 이러한 병렬 덤프 및 복원 프로세스는 전체 마이그레이션을 완료하는 데 걸리는 시간을 줄입니다. 예를 들어 방법 1 및 2를 사용하여 마이그레이션하는 데 1-1.5시간이 걸린 50GB의 데이터베이스는 이 방법으로 30분 이내에 완료되었습니다.

1. 원본 서버의 각 데이터베이스에 대해 대상 서버에 해당 데이터베이스를 만듭니다.

    ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    ```

    ```SQL
    postgres> create database myDB;
   ```

   예를 들면 다음과 같습니다.
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"
    psql (12.3 (Ubuntu 12.3-1.pgdg18.04+1), server 13.3)
    SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres> create database bench5gb;
    postgres> \q
    ```

2. 작업 수 = 4(데이터베이스의 테이블 수)를 사용하여 디렉터리 형식으로 pg_dump 명령을 실행합니다. 더 큰 컴퓨팅 계층과 더 많은 테이블이 있는 경우 더 큰 수로 늘릴 수 있습니다. 이 pg_dump는 각 작업에 대해 압축된 파일을 저장할 디렉터리를 만듭니다.

    ```azurecli-interactive
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    예를 들면 다음과 같습니다.
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. 그런 다음, 대상 서버에서 백업을 복원합니다.
    ```azurecli-interactive
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    예를 들면 다음과 같습니다.
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> 이 문서에서 설명하는 프로세스를 사용하여 미리 보기 상태인 Azure Database for PostgreSQL 유연한 서버를 업그레이드할 수도 있습니다. 주요 차이점은 유연한 서버 대상에 대한 연결 문자열에 `@dbName`이 없다는 점입니다.  예를 들어 사용자 이름이 `pg`인 경우 연결 문자열의 단일 서버 사용자 이름은 `pg@pg-95`이고, 유연한 서버에서는 `pg`를 사용하기만 하면 됩니다.

## <a name="post-upgrademigrate"></a>업그레이드 후/마이그레이션
주 버전 업그레이드가 완료되면 `ANALYZE` 각 데이터베이스에서 명령을 실행하여 테이블을 새로 고치는 것이 `pg_statistic` 좋습니다. 그렇지 않으면 성능 문제가 발생할 수 있습니다.

```SQL
postgres=> analyze;
ANALYZE
```

## <a name="next-steps"></a>다음 단계

- 대상 데이터베이스 함수에 만족했으면 이전 데이터베이스 서버를 삭제할 수 있습니다. 
- Azure Database for PostgreSQL 단일 서버의 경우에만 해당됩니다. 원본 서버와 동일한 데이터베이스 엔드포인트를 사용하려는 경우 이전 원본 데이터베이스 서버를 삭제한 후에는 이전 데이터베이스 서버 이름을 사용하여 읽기 복제본을 만들 수 있습니다. 안정적인 복제 상태가 설정되면 복제본을 중지할 수 있습니다. 그러면 복제본 서버가 독립 서버로 승격됩니다. 자세한 내용은 [복제](./concepts-read-replicas.md)를 참조하세요.

>[!Important] 
> 프로덕션에 직접 사용하기 전에 새로운 PostgreSQL 업그레이드 버전을 테스트하는 것이 좋습니다. 여기에는 이전 원본 버전 원본과 최신 버전 대상 간의 서버 매개 변수 비교가 포함됩니다. 동일한지 확인하고 새 버전에 추가된 새 매개 변수를 확인합니다. 버전 간의 차이점은 [여기](https://www.postgresql.org/docs/release/)에서 확인할 수 있습니다.
