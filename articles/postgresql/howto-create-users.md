---
title: 사용자 만들기 - Azure Database for PostgreSQL - 단일 서버
description: 이 문서에서는 Azure Database for PostgreSQL - 단일 서버와 상호 작용할 새로운 사용자 계정을 만드는 방법을 설명합니다.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2019
ms.openlocfilehash: a91b334b5494d8db44c86352c95fd7b4d1ea2c14
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604078"
---
# <a name="create-users-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - 단일 서버에서 사용자 만들기

이 문서에서는 Azure Database for PostgreSQL 서버에서 사용자를 만드는 방법을 설명합니다.

Azure 구독 사용자 및 해당 권한을 만들고 관리하는 방법을 알아보려면 [Azure RBAC(Azure 역할 기반 액세스 제어) 문서](../role-based-access-control/built-in-roles.md)를 방문하거나 [역할을 사용자 지정하는 방법](../role-based-access-control/custom-roles.md)을 검토하세요.

## <a name="the-server-admin-account"></a>서버 관리자 계정

Azure Database for PostgreSQL을 처음으로 만들면 서버 관리 사용자 이름과 암호가 제공됩니다. 자세한 내용은 [빠른 시작](quickstart-create-server-database-portal.md)에서 단계별 접근 방식을 참조하세요. 서버 관리 사용자 이름은 사용자 지정 이름이므로 Azure Portal에서 선택된 서버 관리 사용자 이름을 찾을 수 있습니다.

Azure Database for PostgreSQL 서버는 3개의 기본 역할이 정의된 상태로 만들어집니다. 이러한 역할은 다음 명령을 실행하여 확인할 수 있습니다. `SELECT rolname FROM pg_roles;`

- azure_pg_admin
- azure_superuser
- 서버 관리 사용자

서버 관리 사용자는 azure_pg_admin 역할의 멤버입니다. 하지만 서버 관리자 계정은 azure_superuser 역할에 속하지 않습니다. 이 서비스는 관리되는 PaaS 서비스이므로 Microsoft만 슈퍼 사용자 역할에 속합니다.

PostgreSQL 엔진은 [PostgreSQL 제품 설명서](https://www.postgresql.org/docs/current/static/sql-createrole.html)의 설명대로 권한을 사용하여 데이터베이스 개체에 대한 액세스를 제어합니다. Azure Database for PostgreSQL에서 서버 관리 사용자에게는 LOGIN, NOSUPERUSER, INHERIT, CREATEDB, CREATEROLE, NOREPLICATION 권한이 부여됩니다.

서버 관리 사용자 계정을 사용하여 사용자를 더 만들고 이 사용자에게 azure_pg_admin 역할을 부여할 수 있습니다. 또한 서버 관리자 계정을 사용하여 개별 데이터베이스 및 스키마에 액세스할 수 있는 낮은 권한의 사용자 및 역할을 만들 수 있습니다.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL에서 추가 관리 사용자를 만드는 방법

1. 연결 정보 및 관리 사용자 이름을 가져옵니다.
   데이터베이스 서버에 연결하려면 전체 서버 이름 및 관리자 로그인 자격 증명이 필요합니다. Azure Portal의 서버 **개요** 페이지 또는 **속성** 페이지에서 서버 이름과 로그인 정보를 쉽게 찾을 수 있습니다.

2. 관리자 계정 및 암호를 사용하여 데이터베이스 서버에 연결합니다. 원하는 클라이언트 도구(예: pgAdmin 또는 psql)를 사용합니다.
   연결하는 방법을 잘 모르는 경우 [빠른 시작](./quickstart-create-server-database-portal.md)을 참조하세요.

3. 다음 SQL 코드를 편집하고 실행합니다. 자리 표시자 값 <new_user>의 새 사용자 이름을 바꾸고, 자리 표시자 암호를 자신만의 강력한 암호로 바꿉니다. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';

   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL에서 데이터베이스 사용자를 만드는 방법

1. 연결 정보 및 관리 사용자 이름을 가져옵니다.
   데이터베이스 서버에 연결하려면 전체 서버 이름 및 관리자 로그인 자격 증명이 필요합니다. Azure Portal의 서버 **개요** 페이지 또는 **속성** 페이지에서 서버 이름과 로그인 정보를 쉽게 찾을 수 있습니다.

2. 관리자 계정 및 암호를 사용하여 데이터베이스 서버에 연결합니다. 원하는 클라이언트 도구(예: pgAdmin 또는 psql)를 사용합니다.

3. 다음 SQL 코드를 편집하고 실행합니다. `<db_user>` 자리 표시자 값을 원하는 새 사용자 이름으로 바꾸고, `<newdb>` 자리 표시자 값을 자신의 데이터베이스 이름으로 바꿉니다. 자리 표시자 암호를 자신만의 강력한 암호로 바꿉니다.

   이 sql 코드 구문은 예제를 제공할 목적으로 testdb라는 새 데이터베이스를 만듭니다. 그런 다음, PostgreSQL 서비스에 새 사용자를 만들고 해당 사용자의 새 데이터베이스에 연결 권한을 부여합니다.

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. 관리자 계정을 사용하면 데이터베이스의 개체를 보호하기 위해 추가 권한을 부여해야 할 수 있습니다. 데이터베이스 역할 및 권한에 대한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/current/static/ddl-priv.html)를 참조하세요. 예를 들면 다음과 같습니다.

   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

   사용자가 "role" 테이블을 만드는 경우 테이블은 해당 사용자에 게 속합니다. 다른 사용자가 테이블에 액세스 해야 하는 경우 테이블 수준에서 다른 사용자에 게 권한을 부여 해야 합니다.

   예를 들면 다음과 같습니다. 

    ```sql
    GRANT SELECT ON ALL TABLES IN SCHEMA <schema_name> TO <db_user>;
    ```

5. 새로운 사용자 이름과 암호를 사용하여 지정된 데이터베이스를 지정하고 서버에 로그인합니다. 이 예에서는 psql 명령줄을 보여 줍니다. 이 명령을 사용하면 사용자 이름에 대한 암호를 묻는 메시지가 표시됩니다. 사용자 고유의 서버 이름, 데이터베이스 이름 및 사용자 이름으로 바꿉니다.

   ```shell
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>다음 단계

새 사용자 컴퓨터의 IP 주소에 대한 방화벽을 열어 연결을 허용합니다. [Azure CLI](howto-manage-firewall-using-cli.md) 또는 [Azure Portal을 사용하여 Azure Database for PostgreSQL 방화벽 규칙을 만들고 관리합니다](howto-manage-firewall-using-portal.md).

사용자 계정 관리에 대한 자세한 내용 PostgreSQL 제품 설명서에서 [데이터베이스 역할 및 권한](https://www.postgresql.org/docs/current/static/user-manag.html), [GRANT 구문](https://www.postgresql.org/docs/current/static/sql-grant.html) 및 [권한](https://www.postgresql.org/docs/current/static/ddl-priv.html)을 참조하세요.
