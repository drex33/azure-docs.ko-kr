---
title: 서버 매개 변수 - Azure Database for MariaDB
description: 이 항목에서는 Azure Database for MariaDB 서버 매개 변수를 구성하기 위한 지침을 제공합니다.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: 7797ee9d20b33a25c1b51289036651c7ad9f22a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664149"
---
# <a name="server-parameters-in-azure-database-for-mariadb"></a>Azure Database for MariaDB의 서버 매개 변수

이 문서에서는 Azure Database for MariaDB에서 서버 매개 변수를 구성하는 데 있어서의 고려 사항 및 지침을 제공합니다.

## <a name="what-are-server-parameters"></a>서버 매개 변수는 무엇인가요? 

MariaDB 엔진은 엔진 동작을 구성하고 조정하는 데 사용할 수 있는 다양한 서버 변수/매개 변수를 제공합니다. 일부 매개 변수는 런타임 중에 동적으로 설정할 수 있으며, "정적"이기 때문에 서버를 다시 시작해야 적용되는 매개 변수도 있습니다.

Azure Database for MariaDB는 [Azure Portal](./howto-server-parameters.md), [Azure CLI](./howto-configure-server-parameters-cli.md) 및 [PowerShell](./howto-configure-server-parameters-using-powershell.md)을 사용하여 워크로드의 요구 사항에 맞게 다양한 MariaDB 서버 매개 변수의 값을 변경하는 기능을 제공합니다.

## <a name="configurable-server-parameters"></a>구성 가능한 서버 매개 변수

지원되는 서버 매개 변수 목록은 계속 확장됩니다. Azure Portal의 서버 매개 변수 탭을 사용하여 전체 목록을 보고 서버 매개 변수 값을 구성할 수 있습니다.

다음 섹션을 참조하여 일반적으로 업데이트되는 여러 서버 매개 변수의 제한에 대해 자세히 알아보세요. 이러한 제한은 서버의 가격 책정 계층 및 vCore에 의해 결정됩니다.

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

Azure Database for MariaDB에서 이진 로그는 항상 사용하도록 설정됩니다(즉, `log_bin` 은 ON으로 설정됨). 트리거를 사용하려는 경우 *슈퍼 권한이 없고 이진 로깅을 사용하도록 설정되어 있습니다(덜 안전한 `log_bin_trust_function_creators` 변수를 사용할 수 있음)* 와 비슷한 오류가 표시됩니다.

이진 로깅 형식은 항상 **행** 이고 서버에 대한 모든 연결은 **항상** 행 기반 이진 로깅을 사용합니다. 행 기반 이진 로깅을 사용하는 경우 보안 문제가 존재하지 않고 이진 로깅이 중단되지 않아 안전하게 [`log_bin_trust_function_creators`](https://mariadb.com/docs/reference/mdb/system-variables/log_bin_trust_function_creators/) 를 **TRUE** 상태로 설정할 수 있습니다.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

이 매개 변수에 대해 자세히 알아보려면 [MariaDB 설명서](https://mariadb.com/kb/en/innodb-system-variables/#innodb_buffer_pool_size)를 검토하세요.

#### <a name="servers-supporting-up-to-4-tb-storage"></a>최대 4TB 스토리지를 지원하는 서버

|**가격 책정 계층**|**vCore**|**기본값(바이트)**|**최솟값(바이트)**|**최댓값(바이트)**|
|---|---|---|---|---|
|Basic|1|872415232|134217728|872415232|
|Basic|2|2684354560|134217728|2684354560|
|범용|2|3758096384|134217728|3758096384|
|범용|4|8053063680|134217728|8053063680|
|범용|8|16106127360|134217728|16106127360|
|범용|16|32749125632|134217728|32749125632|
|범용|32|66035122176|134217728|66035122176|
|범용|64|132070244352|134217728|132070244352|
|메모리 최적화|2|7516192768|134217728|7516192768|
|메모리 최적화|4|16106127360|134217728|16106127360|
|메모리 최적화|8|32212254720|134217728|32212254720|
|메모리 최적화|16|65498251264|134217728|65498251264|
|메모리 최적화|32|132070244352|134217728|132070244352|

#### <a name="servers-support-up-to-16-tb-storage"></a>최대 16TB 스토리지 지원 서버

|**가격 책정 계층**|**vCore**|**기본값(바이트)**|**최솟값(바이트)**|**최댓값(바이트)**|
|---|---|---|---|---|
|Basic|1|872415232|134217728|872415232|
|Basic|2|2684354560|134217728|2684354560|
|범용|2|7516192768|134217728|7516192768|
|범용|4|16106127360|134217728|16106127360|
|범용|8|32212254720|134217728|32212254720|
|범용|16|65498251264|134217728|65498251264|
|범용|32|132070244352|134217728|132070244352|
|범용|64|264140488704|134217728|264140488704|
|메모리 최적화|2|15032385536|134217728|15032385536|
|메모리 최적화|4|32212254720|134217728|32212254720|
|메모리 최적화|8|64424509440|134217728|64424509440|
|메모리 최적화|16|130996502528|134217728|130996502528|
|메모리 최적화|32|264140488704|134217728|264140488704|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

> [!NOTE]
> `innodb_file_per_table` 은 범용 및 메모리 최적화 가격 책정 계층에서만 업데이트될 수 있습니다.

MariaDB는 테이블 생성 중에 제공한 구성에 따라 InnoDB 테이블을 다른 테이블스페이스에 저장합니다. [시스템 테이블스페이스](https://mariadb.com/kb/en/innodb-system-tablespaces/)는 InnoDB 데이터 사전의 스토리지 영역입니다. [file-per-table 테이블스페이스](https://mariadb.com/kb/en/innodb-file-per-table-tablespaces/)에는 단일 InnoDB 테이블에 대한 데이터 및 인덱스를 포함하며 파일 시스템에 자체 데이터 파일로 저장됩니다. 이 동작은 `innodb_file_per_table` 서버 매개 변수에 의해 제어됩니다. `innodb_file_per_table`을 `OFF`로 설정하면 InnoDB가 시스템 테이블스페이스에 테이블을 만듭니다. 그렇지 않으면 InnoDB는 file-per-table 테이블스페이스에 테이블을 만듭니다.

Azure Database for MariaDB는 단일 데이터 파일에서 가장 큰 **1TB** 를 지원합니다. 데이터베이스 크기가 1TB보다 큰 경우 [innodb_file_per_table](https://mariadb.com/kb/en/innodb-system-variables/#innodb_file_per_table) 테이블스페이스에 테이블을 만들어야 합니다. 단일 테이블 크기가 1TB보다 큰 경우에는 파티션 테이블을 사용해야 합니다.

### <a name="join_buffer_size"></a>join_buffer_size

이 매개 변수에 대해 자세히 알아보려면 [MariaDB 설명서](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size)를 검토하세요.

|**가격 책정 계층**|**vCore**|**기본값(바이트)**|**최솟값(바이트)**|**최댓값(바이트)**|
|---|---|---|---|---|
|Basic|1|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|Basic|2|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|범용|2|262144|128|268435455|
|범용|4|262144|128|536870912|
|범용|8|262144|128|1073741824|
|범용|16|262144|128|2147483648|
|범용|32|262144|128|4294967295|
|범용|64|262144|128|4294967295|
|메모리 최적화|2|262144|128|536870912|
|메모리 최적화|4|262144|128|1073741824|
|메모리 최적화|8|262144|128|2147483648|
|메모리 최적화|16|262144|128|4294967295|
|메모리 최적화|32|262144|128|4294967295|

### <a name="max_connections"></a>max_connections

|**가격 책정 계층**|**vCore**|**기본값**|**최솟값**|**최댓값**|
|---|---|---|---|---|
|Basic|1|50|10|50|
|Basic|2|100|10|100|
|범용|2|300|10|600|
|범용|4|625|10|1250|
|범용|8|1250|10|2500|
|범용|16|2500|10|5,000|
|범용|32|5,000|10|10000|
|범용|64|10000|10|20000|
|메모리 최적화|2|625|10|1250|
|메모리 최적화|4|1250|10|2500|
|메모리 최적화|8|2500|10|5,000|
|메모리 최적화|16|5,000|10|10000|
|메모리 최적화|32|10000|10|20000|

연결 한도를 초과하면 다음과 같은 오류가 발생할 수 있습니다.
> 오류 1040(08004): 연결이 너무 많음

> [!IMPORTANT]
> 최상의 환경을 위해 ProxySQL과 같은 연결 풀러를 사용하여 연결을 효율적으로 관리하는 것이 좋습니다.

MariaDB에 대한 새 클라이언트 연결을 만들려면 시간이 오래 걸리고 일단 설정되면 이러한 연결은 유휴 상태일 때에도 데이터베이스 리소스를 차지합니다. 대부분의 애플리케이션은 많은 단기 연결을 요청합니다. 이는 이러한 상황을 복잡하게 만듭니다. 결과적으로 실제 워크로드에 사용할 수 있는 리소스가 줄어들어 성능이 저하됩니다. 유휴 연결을 줄이고 기존 연결을 다시 사용하는 연결 풀러는 이러한 문제를 방지하는 데 도움이 됩니다. ProxySQL 설정에 대해 알아보려면 [블로그 게시물](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)을 방문하세요.

>[!Note]
>ProxySQL은 오픈 소스 커뮤니티 도구입니다. 최상의 노력을 기준으로 Microsoft에서 지원됩니다. 신뢰할 수 있는 지침을 사용하여 프로덕션 지원을 받으려면 평가 후 [ProxySQL 제품 지원](https://proxysql.com/services/support/)에 문의할 수 있습니다.

### <a name="max_heap_table_size"></a>max_heap_table_size

이 매개 변수에 대해 자세히 알아보려면 [MariaDB 설명서](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size)를 검토하세요.

|**가격 책정 계층**|**vCore**|**기본값(바이트)**|**최솟값(바이트)**|**최댓값(바이트)**|
|---|---|---|---|---|
|Basic|1|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|Basic|2|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|범용|2|16777216|16384|268435455|
|범용|4|16777216|16384|536870912|
|범용|8|16777216|16384|1073741824|
|범용|16|16777216|16384|2147483648|
|범용|32|16777216|16384|4294967295|
|범용|64|16777216|16384|4294967295|
|메모리 최적화|2|16777216|16384|536870912|
|메모리 최적화|4|16777216|16384|1073741824|
|메모리 최적화|8|16777216|16384|2147483648|
|메모리 최적화|16|16777216|16384|4294967295|
|메모리 최적화|32|16777216|16384|4294967295|

### <a name="query_cache_size"></a>query_cache_size

쿼리 캐시는 기본적으로 `have_query_cache` 매개 변수를 사용하여 MariaDB에서 사용하도록 설정됩니다. 

이 매개 변수에 대해 자세히 알아보려면 [MariaDB 설명서](https://mariadb.com/kb/en/server-system-variables/#query_cache_size)를 검토하세요.

|**가격 책정 계층**|**vCore**|**기본값(바이트)**|**최솟값(바이트)**|**최댓값(바이트)**|
|---|---|---|---|---|
|Basic|1|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|Basic|2|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|범용|2|0|0|16777216|
|범용|4|0|0|33554432|
|범용|8|0|0|67108864|
|범용|16|0|0|134217728|
|범용|32|0|0|134217728|
|범용|64|0|0|134217728|
|메모리 최적화|2|0|0|33554432|
|메모리 최적화|4|0|0|67108864|
|메모리 최적화|8|0|0|134217728|
|메모리 최적화|16|0|0|134217728|
|메모리 최적화|32|0|0|134217728|

### <a name="sort_buffer_size"></a>sort_buffer_size

이 매개 변수에 대해 자세히 알아보려면 [MariaDB 설명서](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size)를 검토하세요.

|**가격 책정 계층**|**vCore**|**기본값(바이트)**|**최솟값(바이트)**|**최댓값(바이트)**|
|---|---|---|---|---|
|Basic|1|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|Basic|2|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|범용|2|524288|32768|4194304|
|범용|4|524288|32768|8388608|
|범용|8|524288|32768|16777216|
|범용|16|524288|32768|33554432|
|범용|32|524288|32768|33554432|
|범용|64|524288|32768|33554432|
|메모리 최적화|2|524288|32768|8388608|
|메모리 최적화|4|524288|32768|16777216|
|메모리 최적화|8|524288|32768|33554432|
|메모리 최적화|16|524288|32768|33554432|
|메모리 최적화|32|524288|32768|33554432|

### <a name="tmp_table_size"></a>tmp_table_size

이 매개 변수에 대해 자세히 알아보려면 [MariaDB 설명서](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size)를 검토하세요.

|**가격 책정 계층**|**vCore**|**기본값(바이트)**|**최솟값(바이트)**|**최댓값(바이트)**|
|---|---|---|---|---|
|Basic|1|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|Basic|2|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|범용|2|16777216|1024|67108864|
|범용|4|16777216|1024|134217728|
|범용|8|16777216|1024|268435456|
|범용|16|16777216|1024|536870912|
|범용|32|16777216|1024|1073741824|
|범용|64|16777216|1024|1073741824|
|메모리 최적화|2|16777216|1024|134217728|
|메모리 최적화|4|16777216|1024|268435456|
|메모리 최적화|8|16777216|1024|536870912|
|메모리 최적화|16|16777216|1024|1073741824|
|메모리 최적화|32|16777216|1024|1073741824|

### <a name="time_zone"></a>time_zone

초기 배포 시 Azure for MariaDB 서버는 표준 시간대 정보에 대한 시스템 테이블을 포함하지만 이 테이블은 채워지지 않습니다. MySQL 명령줄 또는 MySQL Workbench와 같은 도구에서 `mysql.az_load_timezone` 저장 프로시저를 호출하여 표준 시간대 테이블을 채울 수 있습니다. 저장 프로시저를 호출하고 글로벌 또는 세션 수준 표준 시간대를 설정하는 방법은 [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) 또는 [Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) 문서를 참조하세요.

## <a name="non-configurable-server-parameters"></a>구성 불가능한 서버 매개 변수

아래 서버 매개 변수는 서비스에서 구성할 수 없습니다.

|**매개 변수**|**고정 값**|
| :------------------------ | :-------- |
|기본 계층의 innodb_file_per_table|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256MB|
|innodb_log_files_in_group|2|

여기에 나와 있지 않은 다른 서버 매개 변수는 [MariaDB](https://mariadb.com/kb/en/server-system-variables/)의 기본값으로 설정됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 서버 매개 변수를 구성하는](./howto-server-parameters.md) 방법을 알아봅니다
- [Azure CLI를 사용하여 서버 매개 변수를 구성하는](./howto-configure-server-parameters-cli.md) 방법을 알아봅니다
- [PowerShell을 사용하여 서버 매개 변수를 구성하는](./howto-configure-server-parameters-using-powershell.md) 방법을 알아봅니다
