---
title: 격리된 액세스를 허용하도록 명명된 복제본 보안 구성
description: 사용자가 명명된 복제본에 액세스할 수 있지만 다른 복제본에는 액세스할 수 없도록 명명된 복제본을 구성 및 관리하기 위한 보안 고려 사항을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: how-to
author: yorek
ms.author: damauri
ms.reviewer: ''
ms.date: 7/27/2021
ms.openlocfilehash: 88410573dbefdfdedb4628babfffc601b19bed36
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567084"
---
# <a name="configure-isolated-access-to-a-hyperscale-named-replica"></a>명명된 하이퍼스케일 복제본에 대한 격리된 액세스 구성
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

이 문서에서는 기본 복제본 또는 다른 명명된 복제본에 대한 액세스 권한을 부여하지 않고 Azure SQL 하이퍼스케일 [명명된 복제본](service-tier-hyperscale-replicas.md)에 대한 액세스 권한을 부여하는 절차를 설명합니다. 이 시나리오는 명명된 복제본의 리소스 및 보안 격리를 허용하며 명명된 복제본이 자체 컴퓨팅 노드를 사용하여 실행되므로 Azure SQL 하이퍼스케일 데이터베이스에 대한 격리된 읽기 전용 액세스가 필요할 때마다 유용합니다. 이 컨텍스트에서 격리는 CPU와 메모리가 기본 복제본과 명명된 복제본 간에 공유되지 않고 명명된 복제본에서 실행되는 쿼리가 기본 또는 다른 복제본의 컴퓨팅 리소스를 사용하지 않음을 의미합니다. 또한 명명된 복제본에 액세스하는 보안 주체는 기본 복제본을 비롯한 다른 복제본에 액세스할 수 없습니다.

## <a name="create-a-login-in-the-master-database-on-the-primary-server"></a>주 서버의 master 데이터베이스에 로그인 만들기

*기본* 데이터베이스를 호스팅하는 논리 서버의 `master` 데이터베이스에서 다음을 실행하여 새 로그인을 만듭니다. 자신만의 강력하고 고유한 암호를 사용합니다.

```sql
create login [third-party-login] with password = 'Just4STRONG_PAZzW0rd!';
```

`sys.sql_logins` 시스템 보기에서 만들어진 로그인에 대한 SID 16진수 값을 검색합니다.

```sql
select sid from sys.sql_logins where name = 'third-party-login';
```

로그인을 사용하지 않도록 설정합니다. 이렇게 하면 이 로그인이 주 복제본을 호스팅하는 서버의 데이터베이스에 액세스할 수 없습니다.

```sql
alter login [third-party-login] disable;
```

## <a name="create-a-user-in-the-primary-read-write-database"></a>기본 읽기-쓰기 데이터베이스에 사용자 만들기

로그인이 생성되면 데이터베이스의 기본 읽기-쓰기 복제본(예: WideWorldImporters)에 연결하고([Azure SQL에서 데이터베이스 복원](https://github.com/yorek/azure-sql-db-samples/tree/master/samples/01-restore-database)에서 복원을 위한 샘플 스크립트를 찾을 수 있음), 해당 로그인에 대한 데이터베이스 사용자를 만듭니다.

```sql
create user [third-party-user] from login [third-party-login];
```

선택 단계로 데이터베이스 사용자가 만들어지면 어떤 식으로든 다시 사용하도록 설정되는 로그인에 대한 우려가 있는 경우 이전 단계에서 만들어진 서버 로그인을 삭제할 수 있습니다. 기본 데이터베이스를 호스팅하는 논리 서버의 master 데이터베이스에 연결하고 다음을 실행합니다.

```sql
drop login [third-party-login];
```

## <a name="create-a-named-replica-on-a-different-logical-server"></a>다른 논리 서버에 명명된 복제본 만들기

명명된 복제본에 대한 액세스를 격리하는 데 사용할 새 Azure SQL 논리 서버를 만듭니다. [Azure SQL Database에서 서버 및 단일 데이터베이스 만들기 및 관리](single-database-manage.md)에 있는 지침을 따릅니다. 명명된 복제본을 만들려면 이 서버가 주 복제본을 호스팅하는 서버와 동일한 Azure 지역에 있어야 합니다.

예를 들어 AZ CLI 사용:

```azurecli
az sql server create -g MyResourceGroup -n MyNamedReplicaServer -l MyLocation --admin-user MyAdminUser --admin-password MyStrongADM1NPassw0rd!
```

그런 다음 이 서버의 기본 데이터베이스에 대해 명명된 복제본을 만듭니다. 예를 들어 AZ CLI를 사용하는 경우:

```azurecli
az sql db replica create -g MyResourceGroup -n WideWorldImporters -s MyPrimaryServer --secondary-type Named --partner-database WideWorldImporters_NR --partner-server MyNamedReplicaServer
```

## <a name="create-a-login-in-the-master-database-on-the-named-replica-server"></a>명명된 복제본 서버의 master 데이터베이스에 로그인 만들기

이전 단계에서 만들어진 명명된 복제본을 호스팅하는 논리 서버의 `master` 데이터베이스에 연결합니다. 주 복제본에서 검색된 SID를 사용하여 로그인을 추가합니다.

```sql
create login [third-party-login] with password = 'Just4STRONG_PAZzW0rd!', sid = 0x0...1234;
```

이 시점에서 `third-party-login`을 사용하는 사용자와 애플리케이션은 명명된 복제본에 연결할 수 있지만 기본 복제본에는 연결할 수 없습니다.

## <a name="grant-object-level-permissions-within-the-database"></a>데이터베이스 내에서 개체 수준 권한 부여

설명된 대로 로그인 인증을 설정하고 나면 일반 `GRANT`, `DENY` 및 `REVOKE` 문을 사용하여 데이터베이스 내에서 권한 부여 또는 개체 수준 권한을 관리할 수 있습니다. 이 문에서 데이터베이스에 만든 사용자의 이름 또는 이 사용자를 구성원으로 포함하는 데이터베이스 역할을 참조하세요. 주 복제본에서 이러한 명령을 실행해야 합니다. 변경 사항은 모든 보조 복제본에 전파되지만 서버 수준 로그인이 만들어진 명명된 복제본에만 적용됩니다.

기본적으로 새로 만들어진 사용자에게는 최소한의 권한이 부여된다는 점에 유의하세요(예: 사용자 테이블에 액세스할 수 없음). `third-party-user`이 테이블의 데이터를 읽도록 허용하려면 `SELECT` 권한을 명시적으로 부여해야 합니다.

```sql
grant select on [Application].[Cities] to [third-party-user];
```

모든 테이블에 대해 개별적으로 권한을 부여하는 대신 사용자를 `db_datareaders` [데이터베이스 역할](/sql/relational-databases/security/authentication-access/database-level-roles)에 추가하여 모든 테이블에 대한 읽기 액세스를 허용하거나 [스키마](/sql/relational-databases/security/authentication-access/create-a-database-schema)를 사용하여 스키마의 모든 기존 테이블 및 새 테이블에 대한 [액세스를 허용](/sql/t-sql/statements/grant-schema-permissions-transact-sql)합니다.

## <a name="test-access"></a>액세스 테스트

클라이언트 도구를 사용하여 이 구성을 테스트하고 기본 복제본 및 명명된 복제본에 연결을 시도할 수 있습니다. 예를 들어, `sqlcmd`를 사용하면 `third-party-login` 사용자를 사용하여 기본 복제본에 연결을 시도할 수 있습니다.

```
sqlcmd -S MyPrimaryServer.database.windows.net -U third-party-login -P Just4STRONG_PAZzW0rd! -d WideWorldImporters
```

사용자가 서버에 연결할 수 없으므로 오류가 발생합니다.

```
Sqlcmd: Error: Microsoft ODBC Driver 13 for SQL Server : Login failed for user 'third-party-login'. Reason: The account is disabled.
```

명명된 복제본에 대한 연결 시도가 성공합니다.

```
sqlcmd -S MyNamedReplicaServer.database.windows.net -U third-party-login -P Just4STRONG_PAZzW0rd! -d WideWorldImporters_NR
```

오류가 반환되지 않으며 부여된 개체 수준 권한이 허용하는 대로 명명된 복제본에서 쿼리를 실행할 수 있습니다.

추가 정보는 다음 항목을 참조하세요.

* Azure SQL 논리 서버, [Azure SQL Database의 서버란?](logical-servers.md) 참조
* 데이터베이스 액세스 및 로그인 관리는 [SQL Database 보안: 데이터베이스 액세스 및 로그인 보안 관리](logins-create-manage.md)를 참조하세요.
* 데이터베이스 엔진 권한, [권한](/sql/relational-databases/security/permissions-database-engine) 참조 
* 개체 권한 부여, [개체 권한 부여](/sql/t-sql/statements/grant-object-permissions-transact-sql) 참조



