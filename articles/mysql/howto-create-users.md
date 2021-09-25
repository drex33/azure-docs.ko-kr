---
title: Azure Database for MySQL에서 데이터베이스 및 사용자 생성하기
description: 이 문서에서는 Azure Database for MySQL 서버와 상호작용할 새로운 사용자 계정을 생성하는 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2021
ms.openlocfilehash: 479fe6c4f30c8272d73c9d1d085f541beeefe229
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128579662"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql"></a>Azure Database for MySQL에서 데이터베이스 및 사용자 생성하기

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

이 문서에서는 Azure Database for MySQL에서 사용자를 생성하는 방법을 설명합니다.

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 *slave* 에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.

Azure Database for MySQL 서버를 처음으로 생성하면 서버 관리 사용자 로그인 사용자 이름과 암호가 제공됩니다. 자세한 내용은 [빠른 시작](quickstart-create-mysql-server-database-using-azure-portal.md)을 참조하세요. Azure Portal에서 서버 관리 사용자 이름을 결정할 수 있습니다.

서버 관리 사용자에게는 다음 권한이 있습니다.

   SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER

Azure Database for MySQL 서버가 생성되면 첫 번째 서버 관리자 계정을 사용하여 추가 사용자를 생성하고 관리자 권한을 부여할 수 있습니다. 또한 서버 관리자 계정은 개별 데이터베이스 스키마에 액세스할 수 있는 적은 권한의 사용자를 생성하는 데 사용할 수 있습니다.

> [!NOTE]
> SUPER 권한 및 DBA 역할은 지원되지 않습니다. 제한 사항 문서의 [권한](concepts-limits.md#privileges--data-manipulation-support)을 검토하여 서비스에서 지원되지 않는 기능을 확인하세요.
>
> `validate_password` 및 `caching_sha2_password`와 같은 암호 플러그인은 서비스에서 지원되지 않습니다.

## <a name="to-create-a-database-with-a-non-admin-user-in-azure-database-for-mysql"></a>Azure Database for MySQL에서 관리 사용자가 아닌 사용자로 데이터베이스를 생성하는 방법

1. 연결 정보 및 관리 사용자 이름을 가져옵니다.
   데이터베이스 서버에 연결하려면 전체 서버 이름 및 관리자 로그인 자격 증명이 필요합니다. Azure Portal의 서버 **개요** 페이지 또는 **속성** 페이지에서 서버 이름과 로그인 정보를 쉽게 찾을 수 있습니다.

2. 관리자 계정 및 암호를 사용하여 데이터베이스 서버에 연결합니다. MySQL Workbench, mysql.exe, HeidiSQL 등 선호하는 클라이언트 도구를 사용하면 됩니다.

   연결 방법을 잘 모르겠으면 [단일 서버에 대한 데이터 연결 및 쿼리](./connect-workbench.md) 또는 [유연한 서버에 대한 데이터 연결 및 쿼리](./flexible-server/connect-workbench.md)를 참조하세요.

3. 다음 SQL 코드를 편집하고 실행합니다. 자리 표시자 값 `db_user`을 사용하려는 신규 사용자 이름으로 변경합니다. 자리 표시자 값 `testdb`을 데이터베이스 이름으로 변경합니다.

   이 SQL 코드는 testdb라는 새 데이터베이스를 생성합니다. 그런 다음 MySQL 서비스에 새로운 사용자를 생성하고 해당 사용자에 대한 새 데이터베이스 스키마(testdb.\*)에 모든 권한을 부여합니다.

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. 데이터베이스 내에서 권한 부여를 확인합니다.

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. 서버에 로그인하고, 지정된 데이터베이스를 지정하고, 새로운 사용자 이름 및 암호를 사용합니다. 이 예에서는 mysql 명령줄을 보여 줍니다. 이 명령을 사용하면 사용자의 암호를 입력하라는 메시지가 표시됩니다. 사용자 고유의 서버 이름, 데이터베이스 이름, 사용자 이름으로 변경합니다.

### <a name="single-server"></a>[단일 서버](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

### <a name="flexible-server"></a>[유연한 서버](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```

## <a name="to-create-more-admin-users-in-azure-database-for-mysql"></a>Azure Database for MySQL에서 관리 사용자를 생성하는 방법

1. 연결 정보 및 관리 사용자 이름을 가져옵니다.
   데이터베이스 서버에 연결하려면 전체 서버 이름 및 관리자 로그인 자격 증명이 필요합니다. Azure Portal의 서버 **개요** 페이지 또는 **속성** 페이지에서 서버 이름과 로그인 정보를 쉽게 찾을 수 있습니다.

2. 관리자 계정 및 암호를 사용하여 데이터베이스 서버에 연결합니다. MySQL Workbench, mysql.exe, HeidiSQL 등 선호하는 클라이언트 도구를 사용하면 됩니다.

   연결 방법을 잘 모르는 경우, [MySQL Workbench를 사용하여 연결하고 데이터 쿼리](./connect-workbench.md)를 참조하세요.

3. 다음 SQL 코드를 편집하고 실행합니다. 자리 표시자 값 `new_master_user`을 신규 사용자 이름으로 변경합니다. 이 구문은 모든 데이터베이스 스키마( *.* )에 나열된 권한을 사용자(이 예시에서는 `new_master_user`)에 부여합니다.

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. 권한 부여 확인:

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="azure_superuser"></a>azure_superuser

모든 Azure Database for MySQL 서버는 "Azure_superuser" 라는 사용자로 생성됩니다. 모니터링, 백업, 기타 정기 유지 관리를 수행하여 서버를 관리하기 위해 Microsoft에서 생성한 시스템 계정입니다. 온 콜 엔지니어가 인시던트 중에 인증서 인증을 통해 이 계정을 사용하여 서버에 액세스하고 JIT(just-in-time) 프로세스를 사용하여 액세스를 요청해야 합니다.

## <a name="next-steps"></a>다음 단계

새 사용자의 머신 IP 주소에 대한 방화벽을 열고 연결하도록 설정합니다.

* [단일 서버에서 방화벽 규칙 생성 및 관리](howto-manage-firewall-using-portal.md)
* [유연한 서버에서 방화벽 규칙 생성 및 관리](flexible-server/how-to-connect-tls-ssl.md)

사용자 계정 관리에 대한 자세한 내용은 [사용자 계정 관리](https://dev.mysql.com/doc/refman/5.7/en/access-control.html), [GRANT 구문](https://dev.mysql.com/doc/refman/5.7/en/grant.html), [권한](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html)에 대한 MySQL 제품 설명서를 참조하세요.
