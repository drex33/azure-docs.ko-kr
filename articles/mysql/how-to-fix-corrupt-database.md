---
title: 데이터베이스 손상 해결 - Azure Database for MySQL
description: 이 문서에서는 Azure Database for MySQL에서 데이터베이스 손상 문제를 해결하는 방법을 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: d9b79ba83613a854610eae8fee6392d6e12ef935
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122642163"
---
# <a name="troubleshoot-database-corruption-in-azure-database-for-mysql"></a>Azure Database for MySQL의 데이터베이스 손상 문제 해결

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

데이터베이스가 손상되면 애플리케이션 가동 중지 시간이 발생할 수 있습니다. 데이터 손실을 방지하려면 제시간에 손상 문제를 해결하는 것도 중요합니다. 데이터베이스가 손상되면 서버 로그에 다음 오류가 표시됩니다. `InnoDB: Database page corruption on disk or a failed.`

이 문서에서는 데이터베이스 또는 테이블 손상 문제를 해결하는 방법을 알아봅니다. Azure Database for MySQL은 InnoDB 엔진을 사용합니다. 또한 자동화된 손상 검사 및 복구 작업을 제공합니다. InnoDB는 읽은 모든 페이지에서 체크섬을 실행하여 손상된 페이지를 확인합니다. 체크섬이 일치하지 않으면 MySQL 서버가 자동으로 중지됩니다.

데이터베이스 손상 문제를 빠르게 완화하려면 다음 옵션을 사용해 보세요.

## <a name="restart-your-mysql-server"></a>MySQL 서버 다시 시작

일반적으로 애플리케이션이 테이블 또는 데이터베이스에 액세스할 때 데이터베이스 또는 테이블이 손상된 것을 알 수 있습니다. InnoDB는 서버를 다시 시작할 때 대부분의 문제를 해결할 수 있는 충돌 복구 메커니즘을 제공합니다. 따라서 서버를 다시 시작하면 데이터베이스를 오류 상태로 만든 크래시로부터 서버를 복구하는 데 도움이 될 수 있습니다.

## <a name="use-the-dump-and-restore-method"></a>덤프 및 복원 방법 사용

‘덤프 및 복원’ 방법을 사용하여 손상 문제를 해결하는 것이 좋습니다. 이 방법에는 다음이 포함됩니다.
1. 손상된 테이블 액세스
1. mysqldump 유틸리티를 사용하여 테이블의 논리적 백업 생성. 백업에는 테이블 구조와 데이터가 보존됩니다.
1. 데이터베이스에 테이블 다시 로드

### <a name="back-up-your-database-or-tables"></a>데이터베이스 또는 테이블 백업

> [!Important]
> - 클라이언트 머신에서 서버에 액세스할 수 있도록 방화벽 규칙을 구성했는지 확인합니다. 자세한 내용은 [단일 서버에서 방화벽 규칙 구성](howto-manage-firewall-using-portal.md) 및 [유연한 서버에서 방화벽 규칙 구성](flexible-server/how-to-connect-tls-ssl.md)을 참조하세요.
> - SSL을 사용하도록 설정한 경우 mysqldump에 대해 SSL 옵션 `--ssl-cert`를 사용합니다.

mysqldump를 사용하여 명령줄에서 백업 파일을 만듭니다. 다음 명령을 실행합니다.

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

매개 변수 설명:
- `[ssl-cert=/path/to/pem]`: SSL 인증서 경로입니다. 클라이언트 머신에서 SSL 인증서를 다운로드하고 명령에서 이 매개 변수에 경로를 설정합니다. SSL을 사용하지 않도록 설정한 경우에는 이 매개 변수를 사용하면 안 됩니다.
- `[host]`: Azure Database for MySQL 서버입니다.
- `[uname]`: 서버 관리 사용자의 이름입니다.
- `[pass]`: 관리 사용자의 암호입니다.
- `[dbname]`: 데이터베이스의 이름입니다.
- `[backupfile.sql]`: 데이터베이스 백업의 파일 이름입니다.

> [!Important]
> - 단일 서버의 경우 `admin-user@servername` 형식을 사용하여 다음 명령의 `myserveradmin`을 바꿉니다.
> - 유연한 서버의 경우 `admin-user` 형식을 사용하여 다음 명령의 `myserveradmin`을 바꿉니다.

특정 테이블이 손상된 경우 데이터베이스에서 백업할 특정 테이블을 선택합니다.
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

하나 이상의 데이터베이스를 백업하려면 `--database` 스위치를 사용하고 데이터베이스 이름을 공백으로 구분하여 나열합니다.

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

### <a name="restore-your-database-or-tables"></a>데이터베이스 또는 테이블 복원

다음 단계는 데이터베이스 또는 테이블을 복원하는 방법을 보여 줍니다. 백업 파일을 만든 후 mysql 유틸리티를 사용하여 테이블 또는 데이터베이스를 복원할 수 있습니다. 다음 명령을 실행합니다.

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
다음은 mysqldump를 사용하여 만든 백업 파일에서 `testdb`를 복원하는 예제입니다. 

> [!Important]
> - 단일 서버의 경우 `admin-user@servername` 형식을 사용하여 다음 명령의 `myserveradmin`을 바꿉니다.
> - 유연한 서버의 경우 ```admin-user``` 형식을 사용하여 다음 명령의 `myserveradmin`을 바꿉니다. 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>다음 단계
위의 단계를 수행해도 문제가 해결되지 않으면 언제든지 전체 서버를 복원할 수 있습니다.
- [Azure Database for MySQL에서 서버 복원 - 단일 서버](howto-restore-server-portal.md)
- [Azure Database for MySQL에서 서버 복원 - 유연한 서버](flexible-server/how-to-restore-server-portal.md)



