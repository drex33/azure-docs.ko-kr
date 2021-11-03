---
title: 일반적인 오류 문제 해결 - Azure Database for MySQL
description: Azure Database for MySQL 서비스를 처음 사용하는 사용자에게 발생하는 일반적인 마이그레이션 오류 문제를 해결하는 방법을 알아봅니다.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 5/21/2021
ms.openlocfilehash: e66940b74110e5870acfffb255e7de4942ad1b71
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453405"
---
# <a name="commonly-encountered-errors-during-or-post-migration-to-azure-database-for-mysql"></a>Azure Database for MySQL로 마이그레이션하는 동안 또는 마이그레이션 후에 일반적으로 발생하는 오류

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Azure Database for MySQL은 MySQL 커뮤니티 버전을 기반으로 하는 완전 관리형 서비스입니다. 관리되는 서비스 환경의 MySQL 환경은 사용자 환경에서 MySQL을 실행하는 것과 다를 수 있습니다. 이 문서에서는 처음으로 Azure Database for MySQL로 마이그레이션하거나 개발하는 동안 발생할 수 있는 몇 가지 일반적인 오류를 확인할 수 있습니다.

## <a name="common-connection-errors"></a>일반적인 연결 오류

### <a name="error-1184-08s01-aborted-connection-22-to-db-db-name-user-user-host-hostip-init_connect-command-failed"></a>오류 1184(08S01): db에 대한 연결 22 중단: 'db-name' 사용자: 'user' 호스트: 'hostIP'(init_connect 명령 실패)

위의 오류는 로그인 성공 후 세션 설정 시 명령을 실행하기 전에 발생합니다. 위의 메시지는 `init_connect` 서버 매개 변수에 잘못된 값을 설정하여 세션 초기화에 실패했음을 나타냅니다.

세션 수준에서 지원되지 않는 `require_secure_transport`와 같은 일부 서버 매개 변수가 있으므로 `init_connect`를 사용하여 이러한 매개 변수 값을 변경하려고 하면 MySQL 서버에 연결하는 동안 아래와 같은 1184 오류가 발생할 수 있습니다.

mysql> 데이터베이스 표시; 오류 2006(HY000): MySQL 서버가 연결되지 않았습니다. 다시 연결하는 중... 연결 ID:    64897 현재 데이터베이스: *** 없음 *** 오류 1184(08S01): db에 대한 연결 22 중단: 'db-name' 사용자: 'user' 호스트: 'hostIP'(init_connect 명령 실패)

**해결 방법**: Azure Portal의 서버 매개 변수 탭에서 `init_connect` 값을 다시 설정하고 init_connect 매개 변수를 사용하여 지원되는 서버 매개 변수만 설정합니다.

## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>SUPER 권한 및 DBA 역할 부족으로 인한 오류

SUPER 권한 및 DBA 역할은 서비스에서 지원되지 않습니다. 따라서 아래 나열된 몇 가지 일반적인 오류가 발생할 수 있습니다.

### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>오류 1419: SUPER 권한이 없고 이진 로깅을 사용하도록 설정되어 있습니다(덜 안전한 log_bin_trust_function_creators 변수를 *사용할 수 있음*).

함수를 만들거나 아래와 같이 트리거하거나 스키마를 가져오는 동안 위의 오류가 발생할 수 있습니다. CREATE FUNCTION 또는 CREATE TRIGGER와 같은 DDL 문은 이진 로그에 기록되므로 보조 복제본에서 실행할 수 있습니다. 복제본 SQL 스레드에는 권한을 상승시키는 데 활용할 수 있는 모든 권한이 있습니다. 이진 로깅을 사용하도록 설정된 서버에 대해 이러한 위험을 방지하기 위해 MySQL 엔진을 사용하려면 저장된 함수 작성자에게 일반적인 CREATE ROUTINE 권한 외에도 SUPER 권한이 있어야 합니다.

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**해결 방법**: 이 오류를 해결하려면 포털의 [서버 매개 변수](howto-server-parameters.md) 블레이드에서 `log_bin_trust_function_creators`를 1로 설정하고 DDL 문을 실행하거나 스키마를 가져와서 원하는 개체를 만듭니다. `log_bin_trust_function_creators`를 1로 계속 유지하여 서버에서 향후 오류를 방지할 수 있습니다. `log_bin_trust_function_creators`를 [MySQL 커뮤니티 설명서](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)에 강조 표시된 보안 위험으로 설정하는 것이 좋습니다. 이진 로그는 위협에 노출되지 않으므로 Azure Database for MySQL에서 최소화됩니다.

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>줄 101의 오류 1227(42000): 액세스가 거부되었습니다. 이 작업에 대한 SUPER 권한(하나 이상)이 필요합니다. 종료 코드 1로 인해 작업이 실패했습니다.

위의 오류는 덤프 파일을 가져오거나 [정의자](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html)가 포함된 프로시저를 만드는 동안 발생할 수 있습니다.

**해결 방법**:  이 오류를 해결하기 위해 관리 사용자는 다음 예제와 같이 GRANT 명령을 실행하여 프로시저를 만들거나 실행할 수 있는 권한을 부여할 수 있습니다.

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```

또는 definer를 가져오기 프로세스를 실행하는 관리 사용자의 이름으로 대체할 수 있습니다. 아래를 참조하세요.

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```

#### <a name="error-1227-42000-at-line-295-access-denied-you-need-at-least-one-of-the-super-or-set_user_id-privileges-for-this-operation"></a>줄 295의 오류 1227(42000): 액세스가 거부되었습니다. 이 작업에 대한 SUPER 또는 SET_USER_ID 권한이 필요합니다.

덤프 파일을 가져오거나 스크립트를 실행하는 과정에서 DEFINER 문을 사용하여 CREATE VIEW를 실행하는 동안 위의 오류가 발생할 수 있습니다. Azure Database for MySQL은 모든 사용자에게 SUPER 권한 또는 SET_USER_ID 권한을 허용하지 않습니다.

**해결 방법**:

* 가능하면 정의자 사용자를 사용하여 CREATE VIEW를 실행합니다. 다른 권한이 있는 다른 definer가 있는 많은 뷰가 있을 수 있으므로 실행 가능하지 않을 수 있습니다.  또는
* 덤프 파일 또는 CREATE VIEW 스크립트를 편집하고 덤프 파일에서 DEFINER = 문을 제거하거나 
* 덤프 파일 또는 CREATE VIEW 스크립트를 편집하고, 스크립트 파일을 가져오거나 실행하는 관리자 권한이 있는 사용자로 정의자 값을 바꿉니다.

> [!Tip]
> sed 또는 perl을 사용하여 덤프 파일 또는 SQL 스크립트를 수정하여 DEFINER = 문을 바꿉니다.

#### <a name="error-1227-42000-at-line-18-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation"></a>줄 18의 오류 1227(42000): 액세스가 거부되었습니다. 이 작업에 대한 SUPER 권한(하나 이상)이 필요합니다.

GTID가 활성화된 MySQL 서버에서 대상 Azure Database for MySQL 서버로 덤프 파일을 가져오려고 시도하는 경우 위의 오류가 발생할 수 있습니다. Mysqldump는 GTID가 사용 중인 서버의 덤프 파일에 SET @@SESSION.sql_log_bin=0 문을 추가하여 덤프 파일이 다시 로드되는 동안 이진 로깅을 비활성화합니다.

**해결 방법**: 가져오기 작업을 수행하는 동안 이 오류를 해결하려면 mysqldump 파일에서 아래 줄을 제거하거나 주석으로 처리하고 가져오기를 다시 실행하여 성공했는지 확인합니다.

SET @MYSQLDUMP_TEMP_LOG_BIN = @@SESSION.SQL_LOG_BIN; SET @@SESSION.SQL_LOG_BIN= 0; SET @@GLOBAL.GTID_PURGED=''; SET @@SESSION.SQL_LOG_BIN = @MYSQLDUMP_TEMP_LOG_BIN;

## <a name="common-connection-errors-for-server-admin-sign-in"></a>서버 관리자 로그인에 대한 일반적인 연결 오류

Azure Database for MySQL 서버를 만들면 서버를 만드는 동안 최종 사용자가 서버 관리자 로그인을 제공합니다. 서버 관리자 로그인을 통해 새 데이터베이스를 만들고, 새 사용자를 추가하고, 권한을 부여할 수 있습니다. 서버 관리자 로그인이 삭제되거나, 권한이 취소되거나, 해당 암호가 변경되면 연결 중에 애플리케이션에서 연결 오류가 표시될 수 있습니다. 다음은 몇 가지 일반적인 오류입니다.

### <a name="error-1045-28000-access-denied-for-user-usernameip-address-using-password-yes"></a>오류 1045(28000): 사용자 'username'@'IP address'에 대한 액세스가 거부되었습니다(암호 사용: 예)

위의 오류는 다음과 같은 경우에 발생합니다.

* 사용자 이름이 존재하지 않습니다.
* 사용자 이름이 삭제되었습니다.
* 암호가 변경되거나 재설정됩니다.

**해결 방법**:

* "사용자 이름"이 서버에 유효한 사용자로 존재하는지 또는 실수로 삭제되었는지 확인합니다. Azure Database for MySQL 사용자에 로그인하여 다음 쿼리를 실행할 수 있습니다.

  ```sql
  select user from mysql.user;
  ```

* 위의 쿼리를 실행하기 위해 MySQL에 로그인할 수 없는 경우 [Azure Portal을 사용하여 관리자 암호를 재설정](howto-create-manage-server-portal.md)하는 것이 좋습니다. Azure Portal의 암호 재설정 옵션을 사용하면 사용자를 다시 만들고, 암호를 다시 설정하고, 관리자 권한을 복원할 수 있습니다. 이를 통해 서버 관리자를 사용하여 로그인하고 추가 작업을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

원하는 답을 찾지 못한 경우 다음 옵션을 고려해 보세요.

* [Microsoft Q&A 질문 페이지](/answers/topics/azure-database-mysql.html) 또는 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql)에 질문을 게시합니다.
* Azure Database for MySQL 팀 [@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)에 이메일을 보냅니다. 이 이메일 주소는 기술 지원 별칭이 아닙니다.
* Azure 지원에 문의하여 [Azure Portal에서 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요. 계정 관련 문제를 해결하려면 Azure Portal에서 [지원 요청](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출합니다.
* 피드백을 제공하거나 새 기능을 요청하려면 [UserVoice](https://feedback.azure.com/d365community/forum/47b1e71d-ee24-ec11-b6e6-000d3a4f0da0)를 통해 항목을 만드세요.
