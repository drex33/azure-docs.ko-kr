---
title: 덤프 및 복원을 사용하여 마이그레이션 - Azure Database for MariaDB
description: 이 문서에서는 mysqldump, MySQL Workbench, phpMyAdmin 등의 도구를 사용하여 Azure Database for MariaDB에서 데이터베이스를 백업하고 복원하는 두 가지 일반적인 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 2/27/2020
ms.openlocfilehash: 35b1e84bdf74afd9577c7c98f023179dd769cd66
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628223"
---
# <a name="migrate-your-mariadb-database-to-an-azure-database-for-mariadb-by-using-dump-and-restore"></a>덤프 및 복원을 사용하여 MariaDB 데이터베이스를 Azure Database for MariaDB로 마이그레이션

이 문서에서는 Azure Database for MariaDB에서 데이터베이스를 백업하고 복원하는 두 가지 일반적인 방법을 설명합니다.
- 명령줄 도구를 사용(mysqldump 사용)하여 덤프 및 복원 
- phpMyAdmin을 사용하여 덤프 및 복원

## <a name="prerequisites"></a>사전 요구 사항
데이터베이스 마이그레이션을 시작하기 전에 다음을 수행합니다.
- [Azure Database for MariaDB 서버 만들기 - Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) 명령줄 유틸리티를 설치합니다.
- 덤프 및 복원 명령을 실행하기 위해 [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) 또는 다른 타사 MySQL 도구를 다운로드하여 설치합니다.

## <a name="use-common-tools"></a>일반 도구 사용
MySQL Workbench 또는 mysqldump와 같은 일반 유틸리티 및 도구를 사용하여 Azure Database for MariaDB에 원격으로 연결하고 데이터를 복원합니다. 인터넷에 연결된 클라이언트 머신에서 이러한 도구를 사용하여 Azure Database for MariaDB에 연결합니다. 최상의 보안 방법으로 SSL 암호화 연결을 사용합니다. 자세한 내용은 [Azure Database for MariaDB에서 SSL 연결 구성](concepts-ssl-connection-security.md)을 참조하세요. 데이터를 Azure Database for MariaDB로 마이그레이션할 때 덤프 파일을 특별한 클라우드 위치로 이동할 필요가 없습니다. 

## <a name="common-uses-for-dump-and-restore"></a>덤프 및 복원을 위한 일반적인 사용
몇 가지 일반적인 시나리오에서 mysqldump, mysqlpump 등의 MySQL 유틸리티를 사용하여 데이터베이스를 Azure Database for MariaDB 서버로 덤프하고 로드할 수 있습니다. 

- 전체 데이터베이스를 마이그레이션할 때 데이터베이스 덤프를 사용합니다. 많은 양의 데이터를 이동하거나 실시간 사이트 또는 애플리케이션에 대한 서비스 중단을 최소화하려는 경우 이 권장 사항이 유지됩니다. 
-  Azure Database for MariaDB로 데이터를 로드할 때 데이터베이스의 모든 테이블이 InnoDB 스토리지 엔진을 사용하는지 확인합니다. Azure Database for MariaDB는 InnoDB 스토리지 엔진만 지원하고 다른 스토리지 엔진은 지원하지 않습니다. 테이블이 다른 스토리지 엔진으로 구성된 경우 Azure Database for MariaDB로 마이그레이션하기 전에 해당 스토리지 엔진을 InnoDB 엔진 형식으로 변환합니다.
   
   예를 들어, MyISAM 테이블을 사용하는 WordPress 앱 또는 웹앱이 있는 경우 먼저 해당 테이블을 InnoDB 형식으로 마이그레이션하여 테이블을 변환한 후 Azure Database for MariaDB에 복원합니다. `ENGINE=InnoDB` 절을 사용하여 테이블을 만드는 데 사용할 엔진을 설정한 다음, 복원하기 전에 데이터를 호환되는 테이블로 전송합니다. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- 데이터베이스를 덤프할 때 호환성 문제를 방지하려면 원본 및 대상 시스템에서 동일한 버전의 MariaDB를 사용하고 있는지 확인합니다. 예를 들어, 기존 MariaDB 서버가 버전 10.2이면 버전 10.2를 실행하도록 구성된 Azure Database for MariaDB로 마이그레이션해야 합니다. `mysql_upgrade` 명령은 Azure Database for MariaDB 서버에서 작동하지 않으며 지원되지 않습니다. MariaDB 버전 간에 업그레이드해야 하는 경우 먼저 사용자 환경에서 초기 버전의 데이터베이스를 이후 버전의 MariaDB로 덤프하거나 내보냅니다. 그런 다음, Azure Database For MariaDB로 마이그레이션하기 전에 `mysql_upgrade`를 실행할 수 있습니다.

## <a name="performance-considerations"></a>성능 고려 사항
대규모 데이터베이스를 덤프할 때 성능을 최적화하려면 다음 사항을 고려해야 합니다.
-   mysqldump에서 `exclude-triggers` 옵션을 사용합니다. 데이터 복원 중 트리거 명령 실행을 방지하기 위해 덤프 파일에서 트리거를 제외합니다. 
-   `single-transaction` 옵션을 사용하여 트랜잭션 격리 모드를 REPEATABLE READ로 설정하고 데이터를 덤프하기 전에 START TRANSACTION SQL 문을 서버로 보냅니다. 단일 트랜잭션 내에서 많은 테이블을 덤프하면 복원 중 스토리지가 추가로 소비됩니다. `single-transaction` 옵션과 `lock-tables` 옵션은 함께 사용할 수 없습니다. LOCK TABLES는 보류 중인 트랜잭션을 암시적으로 커밋시키기 때문입니다. 대형 테이블을 덤프하려면 `single-transaction` 옵션을 `quick` 옵션과 결합합니다. 
-   여러 VALUE 목록을 포함하는 `extended-insert` 여러 행 구문을 사용합니다. 이 접근 방식을 사용하면 덤프 파일이 작아지고 파일을 다시 로드할 때 삽입 속도가 빨라집니다.
-  데이터가 기본 키 순서로 스크립팅되도록 데이터베이스를 덤프할 때 mysqldump에서 `order-by-primary` 옵션을 사용합니다.
-   데이터를 덤프할 때 mysqldump에서 `disable-keys` 옵션을 사용하여 로드 전에 외래 키 제약 조건을 사용하지 않도록 설정합니다. 외래 키 검사를 사용하지 않도록 설정하면 성능이 향상될 수 있습니다. 제약 조건을 활성화하고 참조 무결성을 확인하도록 로드 후 데이터를 확인합니다.
-   적절한 경우 분할된 테이블을 사용합니다.
-   병렬로 데이터를 로드합니다. 리소스 한도에 도달할 수 있는 너무 많은 병렬 처리를 방지하고 Azure Portal에서 사용할 수 있는 메트릭을 사용하여 리소스를 모니터링합니다. 
-   테이블 데이터가 로드된 후 인덱스 생성이 발생하도록 데이터베이스를 덤프할 때 mysqlpump에서 `defer-table-indexes` 옵션을 사용합니다.
-   백업 파일을 Azure Blob 저장소에 복사하고 여기에서 복원을 수행합니다. 이 접근 방식은 인터넷을 통해 복원하는 것보다 훨씬 더 빠릅니다.

## <a name="create-a-backup-file"></a>Backup 파일 만들기

로컬 온-프레미스 서버 또는 가상 머신에 기존 MariaDB 데이터베이스를 백업하려면 mysqldump을 사용하여 다음 명령을 실행합니다. 

```bash
$ mysqldump --opt -u <uname> -p<pass> <dbname> > <backupfile.sql>
```

제공할 매개 변수:
- *\<uname>* : 데이터베이스 사용자 이름 
- *\<pass>* : 데이터베이스의 암호(-p와 암호 사이에 공백 없음) 
- *\<dbname>* : 데이터베이스의 이름 
- *\<backupfile.sql>* : 데이터베이스 백업의 파일 이름 
- *\<--opt>* : mysqldump 옵션 

예를 들어, 사용자 이름이 *testuser* 이고 암호가 없는 MariaDB 서버에서 *testdb* 라는 데이터베이스를 파일 testdb_backup.sql에 백업하려면 다음 명령을 사용합니다. 명령은 데이터베이스를 다시 만드는 데 필요한 모든 SQL 문을 포함하는 `testdb_backup.sql`이라는 파일로 `testdb` 데이터베이스를 백업합니다. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
데이터베이스에서 백업할 특정 테이블을 선택하려면 테이블 이름을 공백으로 구분해서 나열합니다. 예를 들어, *testdb* 에서 table1 및 table2 테이블만 백업하려면 다음 예제를 따릅니다. 

```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

한 번에 둘 이상의 데이터베이스를 백업하려면 --database 스위치를 사용하고 데이터베이스 이름을 공백으로 구분해서 나열합니다. 

```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>대상 서버에서 데이터베이스 만들기
데이터를 마이그레이션하려는 대상 Azure Database for MariaDB 서버에서 빈 데이터베이스를 만듭니다. MySQL Workbench와 같은 도구를 사용하여 데이터베이스를 만듭니다. 이 데이터베이스는 덤프된 데이터를 포함하는 데이터베이스와 이름이 같을 수 있고 다른 이름의 데이터베이스를 만들 수도 있습니다.

연결하려면 Azure Database for MariaDB의 **개요** 창에서 연결 정보를 찾습니다.

![Azure Portal에 있는 Azure Database for MariaDB 서버의 개요 창 스크린샷](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

MySQL Workbench에서 연결 정보를 추가합니다.

![MySQL Workbench의 MySQL 연결 창 스크린샷](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>MariaDB 데이터베이스 복원
대상 데이터베이스를 만든 후에는 mysql 명령 또는 MySQL Workbench를 사용하여 덤프 파일에서 새로 만든 데이터베이스로 데이터를 복원할 수 있습니다.

```bash
mysql -h <hostname> -u <uname> -p<pass> <db_to_restore> < <backupfile.sql>
```

이 예제에서는 대상 Azure Database for MariaDB 서버에서 새로 만든 데이터베이스로 데이터를 복원합니다.

```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-your-mariadb-database-by-using-phpmyadmin"></a>phpMyAdmin을 사용하여 MariaDB 데이터베이스 내보내기

내보내려면 환경에 로컬로 이미 설치했을 수 있는 일반 도구 phpMyAdmin을 사용할 수 있습니다. MariaDB 데이터베이스를 내보내려면 다음을 수행합니다.
1. phpMyAdmin을 엽니다.
1. 왼쪽 창에서 데이터베이스를 선택한 다음, **내보내기** 링크를 선택합니다. 데이터베이스의 덤프를 보는 새 페이지가 나타납니다.
1. **내보내기** 영역에서 **모두 선택** 링크를 선택하여 데이터베이스의 테이블을 선택합니다. 
1. **SQL 옵션** 영역에서 적절한 옵션을 선택합니다. 
1. **파일로 저장** 옵션 및 해당 압축 옵션을 선택한 다음, **이동** 을 선택합니다. 프롬프트에서 파일을 로컬로 저장합니다.

## <a name="import-your-database-by-using-phpmyadmin"></a>phpMyAdmin을 사용하여 데이터베이스 가져오기

가져오기 프로세스는 내보내기 프로세스와 유사합니다. 다음을 수행합니다.
1. phpMyAdmin을 엽니다. 
1. phpMyAdmin 설정 페이지에서 **추가** 를 선택하여 Azure Database for MariaDB 서버를 추가합니다. 
1. 연결 세부 정보 및 로그인 정보를 입력합니다.
1. 적절하게 명명된 데이터베이스를 만든 다음, 왼쪽 창에서 선택합니다. 기존 데이터베이스를 다시 쓰려면 데이터베이스 이름을 선택하고, 테이블 이름 옆에 있는 확인란을 모두 선택하고, **삭제** 를 선택하여 기존 테이블을 삭제합니다. 
1. **SQL** 링크를 선택하여 SQL 명령을 입력하거나 SQL 파일을 업로드할 수 있는 페이지를 표시합니다. 
1. **찾아보기** 단추를 선택하여 데이터베이스 파일을 찾습니다. 
1. **이동** 단추를 선택하여 백업을 내보내고, SQL 명령을 실행하고, 데이터베이스를 다시 만듭니다.

## <a name="next-steps"></a>다음 단계
- [Azure Database for MariaDB에 애플리케이션을 연결](./howto-connection-string.md)합니다.
