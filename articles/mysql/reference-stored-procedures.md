---
title: 관리 저장 프로시저 - Azure Database for MySQL
description: Azure Database for MySQL에서 입력 데이터 복제를 구성하고 표준 시간대를 설정하고 쿼리를 종료하는 데 유용한 저장 프로시저에 대해 알아봅니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 589c0a4ab358bb1b25fa58c2083dd82b13315d64
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122642120"
---
# <a name="azure-database-for-mysql-management-stored-procedures"></a>Azure Database for MySQL 관리 저장 프로시저

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

저장 프로시저는 MySQL 서버를 관리하는 데 도움이 되는 Azure Database for MySQL 서버에서 사용할 수 있습니다. 여기에는 서버의 연결, 쿼리의 관리 및 입력 데이터 복제 설정이 포함됩니다.  

## <a name="data-in-replication-stored-procedures"></a>입력 데이터 복제 저장 프로시저

데이터 내부 복제를 사용하면 다른 클라우드 공급자가 호스트하는 데이터베이스 서비스 또는 가상 머신의 온-프레미스에서 실행하는 MySQL 서버에서 MySQL 서비스에 대한 Azure 데이터베이스로 데이터를 동기화할 수 있습니다.

다음 저장 프로시저는 원본과 복제본 간의 입력 데이터 복제를 설정하거나 제거하는 데 사용됩니다.

|**저장 프로시저 이름**|**입력 매개 변수**|**출력 매개 변수**|**사용 정보**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|해당 없음|SSL 모드를 사용하여 데이터를 전송하려면 CA 인증서의 컨텍스트를 master_ssl_ca 매개 변수로 전달합니다. </br><br>SSL을 사용하지 않고 데이터를 전송하려면 빈 문자열을 master_ssl_ca 매개 변수로 전달합니다.|
|*mysql.az_replication _start*|해당 없음|해당 없음|복제를 시작합니다.|
|*mysql.az_replication _stop*|해당 없음|해당 없음|복제를 중지합니다.|
|*mysql.az_replication _remove_master*|해당 없음|해당 없음|원본과 복제본 간의 복제 관계를 제거합니다.|
|*mysql.az_replication_skip_counter*|해당 없음|해당 없음|복제 오류 하나를 건너뜁니다.|

Azure Database for MySQL에서 원본과 복제본 간 입력 데이터 복제를 설정하려면 [입력 데이터 복제 구성 방법](howto-data-in-replication.md)을 참조합니다.

## <a name="other-stored-procedures"></a>기타 저장 프로시저

다음 저장 프로시저는 Azure Database for MySQL에서 서버를 관리하는 데 사용할 수 있습니다.

|**저장 프로시저 이름**|**입력 매개 변수**|**출력 매개 변수**|**사용 정보**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|해당 없음|[`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) 명령과 동일합니다. 연결이 실행 중인 명령문을 종료한 후 제공된 processlist_id와 관련된 연결을 종료합니다.|
|*mysql.az_kill_query*|processlist_id|해당 없음|[`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) 명령과 동일합니다. 연결이 현재 실행 중인 명령문을 종료합니다. 연결 자체를 활성 상태로 유지합니다.|
|*mysql.az_load_timezone*|해당 없음|해당 없음|표준 시간대 표를 로드하여 `time_zone` 매개 변수를 명명된 값으로 설정할 수 있습니다(예: “미국/태평양”).|

## <a name="next-steps"></a>다음 단계
- [입력 데이터 복제](howto-data-in-replication.md) 설정 방법 알아보기
- [표준 시간대 표](howto-server-parameters.md#working-with-the-time-zone-parameter) 사용 방법 알아보기
