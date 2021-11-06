---
title: Azure Database for MySQL 단일 서버의 새로운 기능
description: MySQL Community Edition을 기반으로 하는 Microsoft 클라우드의 관계형 데이터베이스 서비스인 Azure Database for MySQL - 단일 서버에 대한 최신 업데이트에 관해 알아봅니다.
author: hjtoland3
ms.service: mysql
ms.author: jtoland
ms.custom: mvc
ms.topic: conceptual
ms.date: 06/17/2021
ms.openlocfilehash: ef710559cb8d51b21e859bb08c50c5a2ed07b74c
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894206"
---
# <a name="whats-new-in-azure-database-for-mysql---single-server"></a>Azure Database for MySQL - 단일 서버의 새로운 기능은 무엇인가요?

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL은 Microsoft 클라우드의 관계형 데이터베이스 서비스입니다. 이 서비스는 [MySQL Community](https://www.mysql.com/products/community/) Edition(GPLv2 라이선스에서 사용 가능) 데이터베이스 엔진을 기반으로 하며 버전 5.6(사용 중지), 5.7 및 8.0을 지원합니다. [Azure Database for MySQL - 단일 서버](./overview.md#azure-database-for-mysql---single-server)는 데이터베이스 사용자 지정에 대한 최소한의 요구 사항으로 완전 관리형 데이터베이스 서비스를 제공하는 배포 모드입니다. 단일 서버 플랫폼은 최소한의 사용자 구성과 제어를 통해 패치, 백업, 고가용성 및 보안 등 대부분의 데이터베이스 관리 기능을 처리하도록 설계되었습니다.

이 문서에서는 2021년 1월부터 Azure Database for MySQL - 단일 서버의 새로운 릴리스 및 기능을 요약합니다. 목록은 가장 최근의 업데이트가 먼저 있는 역방향 시간순으로 표시됩니다.

## <a name="october-2021"></a>2021년 10월

- **알려진 문제**

MySQL 8.0.27 클라이언트는 Azure Database for MySQL - 단일 서버와 호환되지 않습니다. mysql.exe 또는 워크벤치를 통해 만든 MySQL 8.0.27 클라이언트의 모든 연결이 실패합니다. 해결 방법으로 이전 버전의 클라이언트(MySQL 8.0.27 이전)를 사용하거나 [대신 Azure Database for MySQL - 유연한 서버의](https://docs.microsoft.com/azure/mysql/flexible-server/overview) 인스턴스를 만드는 것이 좋습니다.

## <a name="june-2021"></a>2021년 6월
  
이 Azure Database for MySQL - 단일 서버 릴리스에는 다음과 같은 업데이트가 포함되어 있습니다.

- **MySQL 8.0용 Portal/CLI에서 서버 매개 변수 `activate_all_roles_on_login`을 변경하는 기능 사용됨**

  이제 사용자는 Azure Portal 및 CLI를 사용하여 activate_all_roles_on_login 매개 변수의 값을 변경할 수 있습니다. 이 매개 변수는 사용자가 서버에 로그인할 때 부여된 모든 역할의 자동 활성화를 사용하도록 설정할지를 구성하는 데 도움이 됩니다. 자세한 내용은 [서버 시스템 변수](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html)를 참조하세요.

- **MySQL Community 버그 #29596969 및 #94668 해결됨**

  이 릴리스에서는 필드가 MySQL 8.0용 PRIMARY KEY로 표시된 경우 CREATE TABLE 쿼리에서 기본 식이 무시되는 문제가 해결됩니다. (MySQL Community 버그 #29596969, 버그 #94668). 자세한 내용은 [MySQL 버그: #94668: 필드가 PK로 설정된 경우 CREATE TABLE 쿼리 중 식 기본값이 NULL로 설정됨](https://bugs.mysql.com/bug.php?id=94668)을 참조하세요.

- **“SHOW TABLE” 쿼리에서 중복 테이블 이름 문제가 해결됨**

  테이블 작업을 수행하는 동안 테이블 캐시를 세부적으로 제어할 수 있도록 새 기능을 도입했습니다. 새 기능의 코드 결함으로 인해 디렉터리 캐시의 항목이 잘못 구성되거나 추가될 수 있으며, 같은 이름의 두 테이블을 반환하는 것과 같은 예기치 않은 동작이 발생할 수 있습니다. 디렉터리 캐시는 “SHOW TABLE” 관련 쿼리에 대해서만 작동합니다. DML 또는 DDL 쿼리에는 영향을 미치지 않습니다. 이 문제는 이번 릴리스에서 완전히 해결되었습니다.

- **디스크로의 임시 테이블 스필을 줄이기 위해 서버 매개 변수 `max_heap_table_size`의 기본값을 높임**

  이 릴리스에서는 매개 변수 `max_heap_table_size`에 대해 허용되는 최댓값이 범용 64 vCore 및 메모리 최적화 32 vCore에 대해 8589934592로 변경되었습니다.

- **포털에서 `sql_require_primary_key` 매개 변수의 값 설정과 관련된 문제가 해결됨**

  이제 사용자는 Azure Portal에서 직접 `sql_require_primary_key` 매개 변수의 값을 수정할 수 있습니다.

- **계획된 유지 관리 알림의 일반 공급**

  이 릴리스는 Azure Database for MySQL - 단일 서버에서 계획된 유지 관리 알림의 일반 공급을 제공합니다. 자세한 내용은 [계획된 유지 관리 알림](concepts-planned-maintenance-notification.md) 문서를 참조하세요.

- **기본적으로 매개 변수 `redirect_enabled`가 사용됨**

  이번 릴리스에서는 매개 변수 `redirect_enabled`가 기본적으로 사용됩니다. 리디렉션은 애플리케이션이 백 엔드 서버 노드에 직접 연결할 수 있도록 하여 클라이언트 애플리케이션과 MySQL 서버 간의 네트워크 대기 시간을 줄이는 것을 목표로 합니다. PHP 애플리케이션의 리디렉션 지원은 Microsoft에서 개발한 [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 확장을 통해 제공됩니다. 자세한 내용은 [리디렉션을 사용하여 Azure Database for MySQL에 연결](howto-redirection.md) 문서를 참조하세요.

>[!Note]
> * 리디렉션은 프라이빗 링크 설정에서 작동하지 않습니다. Azure Database for MySQL에 대한 프라이빗 링크를 사용하는 경우 연결 문제가 발생할 수도 있습니다. 이 문제를 해결하려면 redirect_enabled 매개 변수를 “OFF”로 설정하고 클라이언트 애플리케이션을 다시 시작해야 합니다.</br>
> * mysqlnd_azure 리디렉션 드라이버를 사용하여 Azure Database for MySQL에 연결하는 PHP 애플리케이션이 있는 경우(기본적으로 리디렉션 사용) 삽입 트랜잭션에 영향을 주는 데이터 인코딩 문제가 발생할 수도 있습니다.</br>
> 이 문제를 해결하려면:
>    - Azure Portal에서 redirect_enabled 매개 변수를 “OFF”로 설정하여 리디렉션을 사용하지 않도록 설정하고, PHP 애플리케이션을 다시 시작하여 변경 후 드라이버 캐시를 지웁니다.
>     - 연결된 후 세션 수준에서 설정을 기반으로 문자 집합 관련 매개 변수를 명시적으로 설정합니다(예: “set names utf8mb4”).

## <a name="february-2021"></a>2021년 2월

이 Azure Database for MySQL - 단일 서버 릴리스에는 다음과 같은 업데이트가 포함되어 있습니다.

- 버전 5.7 및 8.0 대용량 스토리지 서버의 데이터 입력에 대한 GTID(전역 트랜잭션 식별자)를 지원하는 새 저장 프로시저가 추가되었습니다.
- MySQL 버전을 5.6.50 및 5.7.32로 지원하도록 업데이트되었습니다.

## <a name="january-2021"></a>2021년 1월

이 Azure Database for MySQL - 단일 서버 릴리스에는 다음과 같은 업데이트가 포함되어 있습니다.

- 첫 번째 관리자 권한을 자동으로 수정하도록 "암호 재설정"을 사용하도록 설정했습니다.
- `auto_increment_increment/auto_increment_offset` 서버 매개 변수 및 `session_track_gtids`가 노출되었습니다.
- innodb 버퍼 풀 덤프/복원 제어에 대한 새 저장 프로시저가 추가되었습니다.
- 큰 스토리지 서버에 대한 innodb 준비 관련 서버 매개 변수가 노출되었습니다.

## <a name="contacts"></a>연락처

Azure Database for MySQL 작업에 대한 질문이나 제안이 있는 경우 Azure Database for MySQL 팀([@AskAzure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com))에 문의하세요. 이 이메일 주소는 기술 지원 별칭이 아닙니다.

또한 문의의 다음 사항을 적절히 고려해 주세요.

- Azure 고객 지원팀에 문의하려면 [Azure Portal에서 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.
- 계정 관련 문제를 해결하려면 Azure Portal에서 [지원 요청](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출합니다.
- 피드백을 제공하거나 새 기능을 요청하려면 [UserVoice](https://feedback.azure.com/d365community/forum/47b1e71d-ee24-ec11-b6e6-000d3a4f0da0)를 통해 항목을 만드세요.

## <a name="next-steps"></a>다음 단계

- [Azure Database for MySQL 가격 책정](https://azure.microsoft.com/pricing/details/mysql/server/)에 대해 자세히 알아봅니다.
- Azure Database for MySQL - 단일 서버에 대한 [공용 설명서](./single-server/index.yml)를 찾아보세요.
- [일반적인 오류 문제 해결](./howto-troubleshoot-common-errors.md)에 대한 세부 정보를 검토합니다.
