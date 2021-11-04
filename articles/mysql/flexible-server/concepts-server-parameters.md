---
title: 서버 매개 변수 - Azure Database for MySQL - 유연한 서버
description: 이 토픽에서는 Azure Database for MySQL - 유연한 서버에서 서버 매개 변수를 구성하는 지침을 제공합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 59bb5a6a2a544eb72d1438c38ad3040c2ac43476
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468369"
---
# <a name="server-parameters-in-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL - 유연한 서버의 서버 매개 변수

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

이 문서에서는 Azure Database for MySQL - 유연한 서버에서 서버 매개 변수를 구성하는 데 있어서의 고려 사항 및 지침을 제공합니다.

## <a name="what-are-server-variables"></a>서버 변수란?

MySQL 엔진은 엔진 동작을 구성 및 조정하는 데 사용할 수 있는 여러 [서버 변수/매개 변수](https://dev.mysql.com/doc/refman/5.7/en/server-option-variable-reference.html)를 제공합니다. 일부 매개 변수는 런타임 중에 동적으로 설정할 수 있으며, "정적"이기 때문에 서버를 다시 시작해야 적용되는 매개 변수도 있습니다.

Azure Database for MySQL 유연한 서버는 [Azure Portal](./how-to-configure-server-parameters-portal.md) 및 [Azure CLI](./how-to-configure-server-parameters-cli.md)를 사용하여 워크로드의 요구 사항에 맞게 다양한 MySQL 서버 매개 변수의 값을 변경하는 기능을 제공합니다.

## <a name="configurable-server-parameters"></a>구성 가능한 서버 매개 변수

서버 매개 변수를 사용하여 Azure Database for MySQL 유연한 서버 구성을 관리할 수 있습니다. 서버 매개 변수는 서버를 만들 때 기본 값/권장 값으로 구성됩니다. Azure Portal의 서버 매개 변수 블레이드에는 수정 가능/불가능한 서버 매개 변수가 모두 표시됩니다. 수정 불가능한 서버 매개 변수는 회색으로 표시됩니다.

지원되는 서버 매개 변수 목록은 계속 확장됩니다. Azure Portal의 서버 매개 변수 탭을 사용하여 전체 목록을 보고 서버 매개 변수 값을 구성할 수 있습니다.

다음 섹션을 참조하여 일반적으로 업데이트되는 여러 서버 매개 변수의 제한에 대해 자세히 알아보세요. 제한은 서버의 컴퓨팅 계층 및 크기(vCore)에 따라 결정됩니다.

> [!NOTE]
> 수정할 수 없지만 환경에 대해 수정 가능한 것으로 여기고자 하는 서버 매개 변수를 수정하려는 경우 [UserVoice](https://feedback.azure.com/d365community/forum/47b1e71d-ee24-ec11-b6e6-000d3a4f0da0) 항목을 열거나 우선 순위 지정에 도움이 될 수 있는 피드백이 이미 있는 경우 투표합니다.

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

Azure Database for MySQL 유연한 서버에서 이진 로그는 항상 사용 설정됩니다(`log_bin`이 ON으로 설정됨). log_bin_trust_function_creators는 유연한 서버에서 기본적으로 ON으로 설정됩니다.

이진 로깅 형식은 항상 **행** 이고 서버에 대한 모든 연결은 **항상** 행 기반 이진 로깅을 사용합니다. 행 기반 이진 로깅을 사용하는 경우 보안 문제가 존재하지 않고 이진 로깅이 중단되지 않아 안전하게 [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)를 **ON** 상태로 유지할 수 있습니다.

[`log_bin_trust_function_creators`]가 OFF로 설정된 경우 트리거를 만들려고 하면 *슈퍼 권한이 없고 이진 로깅이 사용 설정되었습니다(덜 안전한 `log_bin_trust_function_creators` 변수를 사용하는 것이 좋습니다)* 와 유사한 오류가 발생할 수 있습니다.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

이 매개 변수에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size)를 참조하세요.

|**가격 책정 계층**|**vCore**|**메모리 크기(GiB)**|**기본값(바이트)**|**최솟값(바이트)**|**최댓값(바이트)**|
|---|---|---|---|---|---|
|버스트 가능(B1s)|1|1|134217728|33554432|134217728|
|버스트 가능(B1ms)|1|2|536870912|134217728|536870912|
|버스트 가능|2|4|2147483648|134217728|2147483648|
|범용|2|8|6442450944|134217728|6442450944|
|범용|4|16|12884901888|134217728|12884901888|
|범용|8|32|25769803776|134217728|25769803776|
|범용|16|64|51539607552|134217728|51539607552|
|범용|32|128|103079215104|134217728|103079215104|
|범용|48|192|154618822656|134217728|154618822656|
|범용|64|256|206158430208|134217728|206158430208|
|메모리 최적화|2|16|12884901888|134217728|12884901888|
|메모리 최적화|4|32|25769803776|134217728|25769803776|
|메모리 최적화|8|64|51539607552|134217728|51539607552|
|메모리 최적화|16|128|103079215104|134217728|103079215104|
|메모리 최적화|32|256|206158430208|134217728|206158430208|
|메모리 최적화|48|384|309237645312|134217728|309237645312|
|메모리 최적화|64|504|405874409472|134217728|405874409472|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

MySQL은 테이블을 만드는 동안 제공된 구성에 따라 InnoDB 테이블을 다른 테이블스페이스에 저장합니다. [시스템 테이블스페이스](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html)는 InnoDB 데이터 사전의 스토리지 영역입니다. [file-per-table 테이블스페이스](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html)에는 단일 InnoDB 테이블에 대한 데이터 및 인덱스를 포함하며 파일 시스템에 자체 데이터 파일로 저장됩니다. 이 동작은 `innodb_file_per_table` 서버 매개 변수에 의해 제어됩니다. `innodb_file_per_table`을 `OFF`로 설정하면 InnoDB가 시스템 테이블스페이스에 테이블을 만듭니다. 그렇지 않으면 InnoDB는 file-per-table 테이블스페이스에 테이블을 만듭니다.

Azure Database for MySQL 유연한 서버는 단일 데이터 파일에서 최대 **4TB** 를 지원합니다. 데이터베이스 크기가 4TB보다 큰 경우 [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) 테이블스페이스에 테이블을 만들어야 합니다. 단일 테이블 크기가 4TB보다 큰 경우에는 파티션 테이블을 사용해야 합니다.

### <a name="innodb_log_file_size"></a>innodb_log_file_size

[innodb_log_file_size](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_log_file_size) 는 [로그 그룹](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_log_group)에 있는 각 [로그 파일](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_log_file) 의 크기 (바이트)입니다. 로그 파일 [(innodb_log_file_size](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_log_file_size)innodb_log_files_in_group)의 결합 된 크기는  *  [](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_log_files_in_group)512gb 보다 약간 작은 최대값을 초과할 수 없습니다. 로그 파일 크기는 성능에 더 유용 하지만, 충돌 후 복구 시간이 높을 경우 단점이 있습니다. 충돌 복구 시 거의 발생 하지 않을 경우 복구 시간을 균형 있게 유지 하 고 피크 작업 중에 처리량을 최대화 해야 합니다. 또한 다시 시작 시간이 길어질 수 있습니다. MySQL, 512MB, 1GB 또는 2GB의 Azure database for MySQL 유연한 서버 값 중 하나에 innodb_log_size를 구성할 수 있습니다. 매개 변수가 정적 이므로 다시 시작 해야 합니다.

> [!NOTE]
> 매개 변수 innodb_log_file_size를 기본값에서 변경한 경우 다시 시작 지연이 발생 하지 않도록 하려면 "innodb_buffer_pool_pages_dirty '와 같은 전역 상태 표시" 값이 30 초 동안 0으로 유지 되는지 확인 합니다.



### <a name="max_connections"></a>max_connections

max_connection의 값은 서버의 메모리 크기에 따라 결정됩니다.

|**가격 책정 계층**|**vCore**|**메모리 크기(GiB)**|**기본값**|**최솟값**|**최댓값**|
|---|---|---|---|---|---|
|버스트 가능(B1s)|1|1|85|10|171|
|버스트 가능(B1ms)|1|2|171|10|341|
|버스트 가능|2|4|341|10|683|
|범용|2|8|683|10|1365|
|범용|4|16|1365|10|2731|
|범용|8|32|2731|10|5461|
|범용|16|64|5461|10|10923|
|범용|32|128|10923|10|21845|
|범용|48|192|16384|10|32768|
|범용|64|256|21845|10|43691|
|메모리 최적화|2|16|1365|10|2731|
|메모리 최적화|4|32|2731|10|5461|
|메모리 최적화|8|64|5461|10|10923|
|메모리 최적화|16|128|10923|10|21845|
|메모리 최적화|32|256|21845|10|43691|
|메모리 최적화|48|384|32768|10|65536|
|메모리 최적화|64|504|43008|10|86016|

연결 한도를 초과하면 다음과 같은 오류가 발생할 수 있습니다.
> 오류 1040(08004): 연결이 너무 많음

> [!IMPORTANT]
>최상의 환경을 위해 ProxySQL과 같은 연결 풀을 사용하여 연결을 효율적으로 관리하는 것이 좋습니다.

MySQL에 대한 새 클라이언트 연결을 만들려면 시간이 필요하며, 일단 설정되면 이러한 연결은 유휴 상태일 때에도 데이터베이스 리소스를 차지합니다. 대부분의 애플리케이션은 많은 단기 연결을 요청합니다. 이는 이러한 상황을 복잡하게 만듭니다. 결과적으로 실제 워크로드에 사용할 수 있는 리소스가 줄어들어 성능이 저하됩니다. 유휴 연결을 줄이고 기존 연결을 다시 사용하는 연결 풀러는 이러한 문제를 방지하는 데 도움이 됩니다. ProxySQL 설정에 대해 알아보려면 [블로그 게시물](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)을 방문하세요.

>[!Note]
>ProxySQL은 오픈 소스 커뮤니티 도구입니다. 최상의 노력을 기준으로 Microsoft에서 지원됩니다. 신뢰할 수 있는 지침을 사용하여 프로덕션 지원을 받으려면 평가 후 [ProxySQL 제품 지원](https://proxysql.com/services/support/)에 문의할 수 있습니다.

### <a name="innodb_strict_mode"></a>innodb_strict_mode

"행 크기가 너무 큼(> 8126)"과 유사한 오류가 표시되는 경우 **innodb_strict_mode** 매개 변수를 해제할 수 있습니다. 서버 매개 변수 **innodb_strict_mode** 는 서버 수준에서 전역 수정이 허용되지 않는데, 행 데이터 크기가 8k보다 큰 경우 데이터가 오류 없이 잘려 잠재적인 데이터 손실로 이어질 수 있기 때문입니다. 페이지 크기 제한에 맞춰 스키마를 수정하는 것이 좋습니다.

이 매개 변수는 `init_connect`를 사용하여 세션 수준에서 설정할 수 있습니다. 세션 수준에서 **innodb_strict_mode** 를 설정하려면 [설정 매개 변수가 목록에 없음](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters)을 참조하세요.

> [!NOTE]
> 읽기 복제본 서버가 있는 경우 소스 서버에서 세션 수준으로 **innodb_strict_mode** 를 OFF로 설정하면 복제가 중단됩니다. 복제본을 읽었다면 매개 변수를 OFF로 설정하는 것이 좋습니다.

### <a name="time_zone"></a>time_zone

초기 배포 시 Azure for MySQL 유연한 서버는 표준 시간대 정보에 대한 시스템 테이블을 포함하지만 이러한 테이블은 채워지지 않습니다. MySQL 명령줄 또는 MySQL Workbench와 같은 도구에서 `mysql.az_load_timezone` 저장 프로시저를 호출하여 표준 시간대 테이블을 채울 수 있습니다. 저장 프로시저를 호출하고 글로벌 또는 세션 수준 표준 시간대를 설정하는 방법은 [Azure Portal](./how-to-configure-server-parameters-portal.md#working-with-the-time-zone-parameter) 또는 [Azure CLI](./how-to-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) 문서를 참조하세요.

### <a name="binlog_expire_logs_seconds"></a>binlog_expire_logs_seconds 

Azure Database for MySQL에서 이 매개 변수는 서비스가 이진 로그 파일을 제거하기 전에 대기하는 시간(초)을 지정합니다.

이진 로그에는 테이블 생성 작업 또는 테이블 데이터 변경과 같은 데이터베이스 변경 사항을 설명하는 이벤트가 포함됩니다. 또한 잠재적으로 변경했을 수 있는 명령문에 대한 이벤트도 포함합니다. 이진 로그는 주로 복제 및 데이터 복구 작업의 두 가지 목적으로 사용됩니다.  일반적으로 이진 로그는 핸들이 서비스, 백업 또는 복제 세트에서 해제되는 즉시 제거됩니다. 복제본이 여러 개인 경우 가장 느린 복제본이 변경 사항을 읽을 때까지 기다렸다가 삭제됩니다. 더 오랜 기간 동안 이진 로그를 유지하려면 binlog_expire_logs_seconds 매개 변수를 구성할 수 있습니다. binlog_expire_logs_seconds가 기본값인 0으로 설정되면 이진 로그에 대한 핸들이 해제되는 즉시 제거됩니다. binlog_expire_logs_seconds > 0이면 제거하기 전에 구성된 초까지 기다립니다. Azure Database for MySQL의 경우 이진 파일의 백업 및 읽기 전용 복제본 제거와 같은 관리 기능은 내부적으로 처리됩니다. Azure Database for MySQL 서비스에서 데이터 출력을 복제할 때 복제본이 기본에서 변경 사항을 읽기 전에 이진 로그를 제거하지 않도록 이 매개 변수를 기본에서 설정해야 합니다. binlog_expire_logs_seconds를 더 높은 값으로 설정하면 이진 로그가 충분히 빨리 제거되지 않고 스토리지 청구가 증가할 수 있습니다. 

## <a name="non-modifiable-server-parameters"></a>수정 불가능한 서버 매개 변수

Azure Portal의 서버 매개 변수 블레이드에는 수정 가능/불가능한 서버 매개 변수가 모두 표시됩니다. 수정 불가능한 서버 매개 변수는 회색으로 표시됩니다. 세션 수준에서 수정 불가능한 서버 매개 변수를 구성하려는 경우 [Azure Portal](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters) 또는 [Azure CLI](./how-to-configure-server-parameters-cli.md#setting-non-modifiable-server-parameters) 문서에서 `init_connect`를 사용한 연결 수준에서의 매개 변수 설정을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Portal에서 서버 매개 변수](./how-to-configure-server-parameters-portal.md)를 구성하는 방법
- [Azure CLI에서 서버 매개 변수](./how-to-configure-server-parameters-cli.md)를 구성하는 방법
