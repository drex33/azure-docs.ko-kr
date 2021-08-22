---
title: 데이터베이스 마이그레이션 - Azure Database for PostgreSQL - 단일 서버
description: PostgreSQL 데이터베이스를 스크립트 파일로 추출하고 데이터를 해당 파일에서 대상 데이터베이스로 가져오는 방법을 설명합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 5b8f07ed32931533383475f936a3d1f08b7e3960
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122531051"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>내보내기 및 가져오기를 사용하여 PostgreSQL 데이터베이스 마이그레이션
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

[pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html)를 사용하여 PostgreSQL 데이터베이스를 스크립트 파일로 추출하고, [psql](https://www.postgresql.org/docs/current/static/app-psql.html)을 사용하여 데이터를 해당 파일에서 대상 데이터베이스로 가져올 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- 액세스를 허용하기 위한 방화벽 규칙을 사용하는 [PostgreSQL용 Azure Database 서버](quickstart-create-server-database-portal.md) 및 이에 속한 데이터베이스
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 명령줄 유틸리티 설치
- [psql](https://www.postgresql.org/docs/current/static/app-psql.html) 명령줄 유틸리티 설치

이러한 단계를 수행하여 PostgreSQL 데이터베이스를 내보내고 가져옵니다.

## <a name="create-a-script-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>로드할 데이터를 포함하는 pg_dump를 사용하여 스크립트 파일 만들기
온-프레미스 또는 VM의 기존 PostgreSQL 데이터베이스를 sql 스크립트 파일로 내보내려면 기존 환경에서 다음 명령을 실행합니다.

```bash
pg_dump --host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
예를 들어, 로컬 서버와 **testdb** 라는 데이터베이스가 있는 경우:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>대상 Azure Database for PostgreSQL에서 데이터 가져오기
psql 명령줄 및 --dbname 매개 변수(-d)를 사용하여 sqp 파일에서 Azure Database for PostgreSQL에 데이터를 가져오고 sql 파일에서 데이터를 로드할 수 있습니다.

```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user> --dbname=<target database name>
```
이 예제에서는 psql 유틸리티 및 이전 단계의 **testdb.sql** 이라는 스크립트 파일을 사용하여 대상 서버 **mydemoserver.postgres.database.azure.com** 의 **mypgsqldb** 데이터베이스로 데이터를 가져옵니다.

**단일 서버** 의 경우 다음 명령을 사용합니다. 
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

**유연한 서버** 의 경우 다음 명령을 사용합니다.  
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin --dbname=mypgsqldb
```



## <a name="next-steps"></a>다음 단계
- 덤프 및 복원을 사용하여 PostgreSQL 데이터베이스를 마이그레이션하려면 [덤프 및 복원을 사용하여 PostgreSQL 데이터베이스 마이그레이션](howto-migrate-using-dump-and-restore.md)을 참조하세요.
- Azure Database for PostgreSQL로 데이터베이스 마이그레이션에 대한 자세한 내용은 [데이터베이스 마이그레이션 가이드](/data-migration/)를 참조하세요.
