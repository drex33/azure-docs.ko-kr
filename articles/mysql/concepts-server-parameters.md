---
title: 서버 매개 변수 - Azure Database for MySQL
description: 이 토픽에서는 Azure Database for MySQL에서 서버 매개 변수를 구성하기 위한 지침을 제공합니다.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/26/2021
ms.openlocfilehash: 8220afc8020e5a6a4ba77c46a98ee3c220c3f37e
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114675319"
---
# <a name="server-parameters-in-azure-database-for-mysql"></a>Azure Database for MySQL의 서버 매개 변수

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

이 문서에서는 Azure Database for MySQL에서 서버 매개 변수를 구성하는 데 있어서의 고려 사항 및 지침을 제공합니다.

## <a name="what-are-server-parameters"></a>서버 매개 변수는 무엇인가요? 

MySQL 엔진은 엔진 동작을 구성 및 조정하는 데 사용할 수 있는 여러 서버 변수/매개 변수를 제공합니다. 일부 매개 변수는 런타임 중에 동적으로 설정할 수 있으며, "정적"이기 때문에 서버를 다시 시작해야 적용되는 매개 변수도 있습니다.

Azure Database for MySQL은 [Azure Portal](./howto-server-parameters.md), [Azure CLI](./howto-configure-server-parameters-using-cli.md) 및 [PowerShell](./howto-configure-server-parameters-using-powershell.md)을 사용하여 워크로드의 요구 사항에 맞게 다양한 MySQL 서버 매개 변수의 값을 변경하는 기능을 제공합니다.

## <a name="configurable-server-parameters"></a>구성 가능한 서버 매개 변수

지원되는 서버 매개 변수 목록은 계속 확장됩니다. Azure Portal의 서버 매개 변수 탭을 사용하여 전체 목록을 보고 서버 매개 변수 값을 구성할 수 있습니다.

다음 섹션을 참조하여 일반적으로 업데이트되는 여러 서버 매개 변수의 제한에 대해 자세히 알아보세요. 이러한 제한은 서버의 가격 책정 계층 및 vCore에 의해 결정됩니다.

### <a name="thread-pools"></a>스레드 풀

MySQL은 일반적으로 모든 클라이언트 연결에 대해 스레드를 할당합니다. 동시 사용자 수가 늘어나면 그에 따라 성능이 저하됩니다. 많은 활성 스레드는 늘어난 컨텍스트 전환, 스레드 경합, CPU 캐시의 잘못된 로컬 특성으로 인해 성능에 크게 영향을 줄 수 있습니다.

서버 쪽 기능이고 연결 풀과 구분되는 스레드 풀은 서버에서 실행되는 활성 스레드 수를 제한하고 스레드 변동을 최소화하기 위해 사용될 수 있는 작업자 스레드의 동적 풀을 도입하여 성능을 극대화합니다. 이렇게 하면 연결이 폭주해도 서버에 리소스 부족이 발생하거나 메모리 부족 오류로 인한 충돌이 발생하지 않도록 보장하는 데 도움이 됩니다. 스레드 풀은 OLTP 워크로드와 같이 짧은 쿼리와 CPU 집약적 워크로드에 가장 효과적입니다.

스레드 풀에 대한 자세한 내용은 [Azure Database for MySQL의 스레드 풀 소개](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/introducing-thread-pools-in-azure-database-for-mysql-service/ba-p/1504173)를 참조하세요.

> [!NOTE]
> 스레드 풀 기능은 MySQL 5.6 버전에 대해 지원되지 않습니다. 

### <a name="configuring-the-thread-pool"></a>스레드 풀 구성
스레드 풀을 사용하도록 설정하려면 `thread_handling` 서버 매개 변수를 "pool-of-threads"로 업데이트합니다. 기본적으로 이 매개 변수는 `one-thread-per-connection`으로 설정됩니다. 즉, MySQL이 새로 연결될 때마다 새로운 스레드를 만듭니다. 이것은 정적 매개 변수이며 적용하려면 서버를 다시 시작해야 합니다.

또한 다음 서버 매개 변수를 설정하여 풀에서 스레드의 최소 및 최대 개수를 구성할 수 있습니다. 
- `thread_pool_max_threads`: 이 값은 풀에서 스레드 수가 이 값을 초과하지 않도록 보장합니다.
- `thread_pool_min_threads`: 이 값은 연결이 닫힌 후에도 예약되는 스레드 수를 설정합니다.

스레드 풀에서 짧은 쿼리의 성능 문제를 해결하기 위해 Azure Database for MySQL에서는 일괄 처리 실행을 사용하도록 허용합니다. 일괄 처리 실행에서는 쿼리 실행 후 즉시 스레드 풀로 돌아가는 대신 다음 쿼리에 이 연결이 사용되도록 기다리기 위해 잠시 동안 스레드가 활성 상태로 유지됩니다. 그런 후 스레드가 쿼리를 빠르게 실행하고, 완료되었으면, 이 프로세스의 전체 시간 소비가 임계값을 초과하기 전까지 다음 항목을 기다립니다. 일괄 처리 실행 동작은 다음과 같은 서버 매개 변수를 사용하여 결정됩니다.  

-  `thread_pool_batch_wait_timeout`: 이 값은 스레드가 다른 쿼리의 처리를 기다리는 시간을 지정합니다.
- `thread_pool_batch_max_time`: 이 값은 스레드가 쿼리를 실행하고 다음 쿼리를 기다리는 주기를 반복할 최대 횟수를 결정합니다.

> [!IMPORTANT]
> 프로덕션에서는 이를 설정하기 전에 스레드 풀을 테스트하세요. 

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

Azure Database for MySQL에서 이진 로그는 항상 사용하도록 설정됩니다(예를 들어 `log_bin`이 ON으로 설정됨). 트리거를 사용하려는 경우 *슈퍼 권한이 없고 이진 로깅을 사용하도록 설정되어 있습니다(덜 안전한 `log_bin_trust_function_creators` 변수를 사용할 수 있음).* 와 비슷한 오류가 표시됩니다. 

이진 로깅 형식은 항상 **행** 이고 서버에 대한 모든 연결은 **항상** 행 기반 이진 로깅을 사용합니다. 행 기반 이진 로깅을 사용하는 경우 보안 문제가 존재하지 않고 이진 로깅이 중단되지 않아 안전하게 [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) 를 **TRUE** 상태로 설정할 수 있습니다.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

이 매개 변수에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size)를 참조하세요.

#### <a name="servers-on-general-purpose-storage-v1-supporting-up-to-4-tb"></a>[범용 스토리지 v1의 서버(최대 4TB 지원)](concepts-pricing-tiers.md#general-purpose-storage-v1-supports-up-to-4-tb)

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

#### <a name="servers-on-general-purpose-storage-v1-supporting-up-to-16-tb"></a>[범용 스토리지 v1의 서버(최대 16TB 지원)](concepts-pricing-tiers.md#general-purpose-storage-v2-supports-up-to-16-tb-storage)

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
> `innodb_file_per_table`은 [범용 스토리지 v2](concepts-pricing-tiers.md#general-purpose-storage-v2-supports-up-to-16-tb-storage)의 범용 및 메모리 최적화 가격 책정 계층에서만 업데이트될 수 있습니다.

MySQL은 테이블을 만드는 동안 제공된 구성에 따라 InnoDB 테이블을 다른 테이블스페이스에 저장합니다. [시스템 테이블스페이스](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html)는 InnoDB 데이터 사전의 스토리지 영역입니다. [file-per-table 테이블스페이스](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html)에는 단일 InnoDB 테이블에 대한 데이터 및 인덱스를 포함하며 파일 시스템에 자체 데이터 파일로 저장됩니다. 이 동작은 `innodb_file_per_table` 서버 매개 변수에 의해 제어됩니다. `innodb_file_per_table`을 `OFF`로 설정하면 InnoDB가 시스템 테이블스페이스에 테이블을 만듭니다. 그렇지 않으면 InnoDB는 file-per-table 테이블스페이스에 테이블을 만듭니다.

Azure Database for MySQL는 [범용 스토리지 v2](concepts-pricing-tiers.md#general-purpose-storage-v2-supports-up-to-16-tb-storage)의 단일 데이터 파일에서 최대 **4TB** 를 지원합니다. 데이터베이스 크기가 4TB보다 큰 경우 [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) 테이블스페이스에 테이블을 만들어야 합니다. 단일 테이블 크기가 4TB보다 큰 경우에는 파티션 테이블을 사용해야 합니다.

### <a name="join_buffer_size"></a>join_buffer_size

이 매개 변수에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size)를 참조하세요.

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
> 최상의 환경을 위해 ProxySQL과 같은 연결 풀을 사용하여 연결을 효율적으로 관리하는 것이 좋습니다.

MySQL에 대한 새 클라이언트 연결을 만들려면 시간이 필요하며, 일단 설정되면 이러한 연결은 유휴 상태일 때에도 데이터베이스 리소스를 차지합니다. 대부분의 애플리케이션은 많은 단기 연결을 요청합니다. 이는 이러한 상황을 복잡하게 만듭니다. 결과적으로 실제 워크로드에 사용할 수 있는 리소스가 줄어들어 성능이 저하됩니다. 유휴 연결을 줄이고 기존 연결을 다시 사용하는 연결 풀러는 이러한 문제를 방지하는 데 도움이 됩니다. ProxySQL 설정에 대해 알아보려면 [블로그 게시물](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)을 방문하세요.

>[!Note]
>ProxySQL은 오픈 소스 커뮤니티 도구입니다. 최상의 노력을 기준으로 Microsoft에서 지원됩니다. 신뢰할 수 있는 지침을 사용하여 프로덕션 지원을 받으려면 평가 후 [ProxySQL 제품 지원](https://proxysql.com/services/support/)에 문의할 수 있습니다.

### <a name="max_heap_table_size"></a>max_heap_table_size

이 매개 변수에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size)를 참조하세요.

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

쿼리 캐시는 기본적으로 해제되어 있습니다. 쿼리 캐시를 사용하도록 설정하려면 `query_cache_type` 매개 변수를 구성합니다. 

이 매개 변수에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size)를 참조하세요.

> [!NOTE]
> 쿼리 캐시는 MySQL 5.7.20부터 더 이상 사용되지 않으며 MySQL 8.0에서 제거되었습니다.

|**가격 책정 계층**|**vCore**|**기본값(바이트)**|**최솟값(바이트)**|**최댓값**|
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

### <a name="lower_case_table_names"></a>lower_case_table_names

lower_case_table_name은 기본적으로 1로 설정되며 MySQL 5.6 및 MySQL 5.7에서 이 매개변수를 업데이트할 수 있습니다.

이 매개 변수에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_lower_case_table_names)를 참조하세요.

> [!NOTE]
> MySQL 8.0에서 lower_case_table_name은 기본적으로 1로 설정되며, 사용자가 이를 변경할 수 없습니다.

### <a name="innodb_strict_mode"></a>innodb_strict_mode

“행 크기가 너무 큼(> 8126)”과 유사한 오류가 표시되는 경우 **innodb_strict_mode** 매개 변수를 해제할 수 있습니다. 서버 매개 변수 **innodb_strict_mode** 는 서버 수준에서 전역 수정이 허용되지 않는데, 행 데이터 크기가 8k보다 큰 경우 데이터가 오류 없이 잘려 잠재적인 데이터 손실로 이어질 수 있기 때문입니다. 페이지 크기 제한에 맞춰 스키마를 수정하는 것이 좋습니다. 

이 매개 변수는 `init_connect`를 사용하여 세션 수준에서 설정할 수 있습니다. 세션 수준에서 **innodb_strict_mode** 를 설정하려면 [설정 매개 변수가 목록에 없음](./howto-server-parameters.md#setting-parameters-not-listed)을 참조하세요.

> [!NOTE]
> 읽기 복제본 서버가 있는 경우 소스 서버에서 세션 수준으로 **innodb_strict_mode** 를 OFF로 설정하면 복제가 중단됩니다. 읽기 복제본이 있으면 매개 변수를 OFF로 설정하는 것이 좋습니다.

### <a name="sort_buffer_size"></a>sort_buffer_size

이 매개 변수에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size)를 참조하세요.

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

이 매개 변수에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size)를 참조하세요.

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

### <a name="innodb-buffer-pool-warmup"></a>InnoDB 버퍼 풀 준비
Azure Database for MySQL 서버를 다시 시작한 후 테이블을 쿼리하면 디스크에 있는 데이터 페이지가 로드됩니다. 이로 인해 대기 시간이 늘어나고 첫 번째 쿼리 실행 성능이 느려집니다. 대기 시간에 민감한 워크로드에는 이것이 허용되지 않을 수 있습니다. InnoDB 버퍼 풀 준비를 활용하면 DML 또는 SELECT 작업이 해당 행에 액세스할 때까지 기다리는 대신 다시 시적 전 버퍼 풀에 있던 디스크 페이지를 다시 로드하여 준비 기간이 단축됩니다.

[InnoDB 버퍼 풀 서버 매개 변수](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html)를 구성하여 성능 매개 변수를 나타내는 Azure Database for MySQL 서버를 다시 시작한 후 준비 기간을 줄일 수 있습니다. InnoDB는 서버 종료 시 각 버퍼 풀에 대해 최근에 사용된 페이지의 백분율을 저장하고 서버 시작 시 이러한 페이지를 복원합니다.

또한 이러한 성능 향상을 위해 서버의 시작 시간이 길어진다는 것을 이해해야 합니다. 이 매개변수를 사용하도록 설정한 경우, 서버에 프로비전된 IOPS에 따라 서버 시작 및 다시 시작 시간이 늘어날 수 있습니다. 이 기간 동안 서버를 사용할 수 없으므로, 다시 시작 시간을 테스트하고 모니터하여 시작/다시 시작 성능이 적절한지 확인하는 것이 좋습니다. 프로비전된 IOPS가 1000개 IOPS 미만인 경우(또는 프로비전된 스토리지가 335GB 미만인 경우)에는 이 매개 변수를 사용하지 않는 것이 좋습니다.

서버 종료 시 버퍼 풀의 상태를 저장하려면 서버 매개 변수 `innodb_buffer_pool_dump_at_shutdown`을 `ON`으로 설정합니다. 이와 비슷하게 서버 시작 시 버퍼 풀 상태를 복원하도록 서버 매개 변수 `innodb_buffer_pool_load_at_startup`을 `ON`으로 설정합니다. 서버 매개 변수 `innodb_buffer_pool_dump_pct`의 값을 낮추고 미세 조정하여 시작/다시 시작에 대한 영향을 제어할 수 있습니다. 기본적으로 이 매개 변수는 `25`로 설정됩니다.

> [!Note]
> InnoDB 버퍼 풀 준비 매개 변수는 최대 16TB 스토리지를 포함하는 범용 스토리지 서버에서만 지원되지 않습니다. [Azure Database for MySQL 스토리지 옵션](./concepts-pricing-tiers.md#storage)에 대해 자세히 알아보세요.

### <a name="time_zone"></a>time_zone

초기 배포 시 Azure for MySQL 서버는 표준 시간대 정보에 대한 시스템 테이블을 포함하지만 이러한 테이블은 채워지지 않습니다. MySQL 명령줄 또는 MySQL Workbench와 같은 도구에서 `mysql.az_load_timezone` 저장 프로시저를 호출하여 표준 시간대 테이블을 채울 수 있습니다. 저장 프로시저를 호출하고 글로벌 또는 세션 수준 표준 시간대를 설정하는 방법은 [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) 또는 [Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) 문서를 참조하세요.

## <a name="non-configurable-server-parameters"></a>구성 불가능한 서버 매개 변수

아래 서버 매개 변수는 서비스에서 구성할 수 없습니다.

|**매개 변수**|**고정 값**|
| :------------------------ | :-------- |
|기본 계층의 innodb_file_per_table|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256MB|
|innodb_log_files_in_group|2|

여기에 나열되지 않은 다른 변수는 MySQL 기본 제공 값으로 설정됩니다. 기본값은 [8.0](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html), [5.7](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html) 및 [5.6](https://dev.mysql.com/doc/refman/5.6/en/server-system-variables.html) 버전의 MySQL 문서를 참조하세요. 

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 서버 매개 변수를 구성하는](./howto-server-parameters.md) 방법을 알아봅니다
- [Azure CLI를 사용하여 서버 매개 변수를 구성하는](./howto-configure-server-parameters-using-cli.md) 방법을 알아봅니다
- [PowerShell을 사용하여 서버 매개 변수를 구성하는](./howto-configure-server-parameters-using-powershell.md) 방법을 알아봅니다
