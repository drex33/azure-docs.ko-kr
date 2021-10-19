---
title: Azure Database for MySQL의 복제 지연 문제 해결
description: Azure Database for MySQL의 복제 지연 문제 해결 방법을 알아보세요.
keywords: mysql, 문제 해결, 복제 대기시간 (초)
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 01/13/2021
ms.openlocfilehash: fac788db67bd96f60a2655f10d5f0a74d02b2c01
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165493"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>Azure Database for MySQL의 복제 지연 문제 해결

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

[읽기 복제본](concepts-read-replicas.md) 기능을 통해 Azure Database for MySQL 서버에서 읽기 전용 복제본 서버로 데이터를 복제할 수 있습니다. 애플리케이션에서 복제본 서버로 읽기 및 보고 쿼리를 라우팅하여 워크로드를 확장할 수 있습니다. 이 설정은 원본 서버의 압력을 줄입니다. 또한 확장 시 애플리케이션의 전반적인 성능 및 지연 문제가 개선됩니다.

복제본은 MySQL 엔진의 네이티브 이진 로그(binlog) 파일의 위치 기반 복제 기술을 사용하여 비동기식으로 업데이트됩니다. 자세한 내용은 [MySQL binlog 파일 위치 기반 복제 구성 개요](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)를 참조하세요.

보조 읽기 복제본의 복제 대기 시간은 몇 가지 요소에 따라 달라집니다. 이러한 요소에 포함되는 사항들은 다음과 같으며 이에 국한되지 않습니다.

- 네트워크 대기 시간
- 원본 서버의 트랜잭션 볼륨
- 원본 서버와 보조 읽기 복제본 서버의 컴퓨팅 계층
- 원본 서버와 보조 서버에서 실행되는 쿼리

이 문서에서는 Azure Database for MySQL의 복제 지연 문제 해결 방법을 알아보세요. 또한 복제본 서버에서 복제 지연 문제가 증가하는 몇 가지 일반적인 원인을 이해할 수 있습니다.

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 *slave* 에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.
>

## <a name="replication-concepts"></a>복제 개념

이진 로그를 실행하면 원본 서버가 커밋된 트랜잭션을 이진 로그에 기록합니다. 이진 로그는 복제에 사용됩니다. 기본적으로 최대 16TB의 스토리지를 지원하는 새로 프로비전된 모든 서버에 대해 설정되어 있습니다. 복제본 서버의 경우 두 개의 스레드가 각 복제 서버에서 실행됩니다. 한 스레드는 *IO 스레드* 이고 다른 스레드는 *SQL 스레드* 입니다.

- IO 스레드는 원본 서버에 연결되고 업데이트된 이진 로그를 요청합니다. 해당 스레드는 이진 로그 업데이트를 수신합니다. 이러한 업데이트는 복제 서버, *릴레이 로그* 라는 로컬 로그에 저장됩니다.
- SQL 스레드는 릴레이 로그를 읽은 다음 복제본 서버에 데이터 변경사항을 적용합니다.

## <a name="monitoring-replication-latency"></a>복제 대기 시간 모니터링

Azure Database for MySQL은 Azure Monitor에 [복제 지연 시간 (초)](concepts-monitoring.md) 메트릭을 제공합니다. 이 메트릭은 읽기 복제 서버에서만 사용 가능합니다. MySQL에서 사용 가능한 seconds_behind_master 메트릭으로 계산됩니다. 

복제 대기 시간이 증가하는 원인을 파악하려면 [MySQL Workbench](connect-workbench.md) 또는 [Azure Cloud Shell](https://shell.azure.com)을 사용하여 복제 서버에 연결합니다. 다음 명령을 실행합니다.

>[!NOTE]
> 코드에서 예제 값을 복제 서버 이름 및 관리자 사용자 이름으로 변경합니다. 관리자 사용자 이름의 경우 Azure Database for MySQL이 필요합니다.

```azurecli-interactive
mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
```

Cloud Shell 터미널의 환경은 다음과 같습니다.

```
Requesting a Cloud Shell.Succeeded.
Connecting terminal...

Welcome to Azure Cloud Shell

Type "az" to use Azure CLI
Type "help" to learn about Cloud Shell

user@Azure:~$mysql -h myreplicademoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 64796
Server version: 5.6.42.0 Source distribution

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
```

동일한 Cloud Shell 터미널에서 다음 명령을 실행합니다.

```
mysql> SHOW SLAVE STATUS;
```

일반적인 출력은 다음과 같습니다.
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="복제 대기 시간 모니터링":::

출력에는 많은 정보가 포함됩니다. 일반적으로 다음 표에서 설명하는 행에만 초점을 맞춰야 합니다.

|메트릭|설명|
|---|---|
|슬레이블_IO_상태| IO의 스레드의 현재 상태를 표시합니다. 일반적으로 원본 (마스터) 서버를 동기화하는 경우 상태는 "마스터에서 이벤트를 보낼 때까지 대기”입니다. "마스터에 연결"과 같은 상태는 복제본과 원본 서버의 연결이 끊어졌다는 것을 나타냅니다. 원본 서버가 실행 중인지 확인하거나 방화벽에서 연결을 차단 하고 있는지 확인합니다.|
|Master_Log_File| 원본 서버에서 기록하고 있는 이진 로그 파일을 나타냅니다.|
|Read_Master_Log_Pos| 이진 로그 파일에 작성하는 원본 서버의 위치를 나타냅니다.|
|Relay_MASTER_LOG_FILE| 복제본 서버가 원본 서버에서 읽고 있는 이진 로그 파일을 나타냅니다.|
|Slave_IO_Running| IO 스레드 실행 여부를 나타냅니다. 값은 `Yes`이어야 합니다. 값이 `NO`이면 복제본이 손상된 것일 수 있습니다.|
|Slave_SQL_Running| SQL 스레드 실행 여부를 나타냅니다. 값은 `Yes`이어야 합니다. 값이 `NO`이면 복제본이 손상된 것일 수 있습니다.|
|Read_Master_Log_Pos| 복제본이 적용되는 Relay_Master_Log_File의 위치를 나타냅니다. 대기 시간이 있는 경우 해당 위치 시퀀스는 Read_Master_Log_Pos 보다 작아야 합니다.|
|Relay_Log_Space|기존 릴레이 로그 파일의 전체 크기를 합한 크기를 나타냅니다. 와 같이 쿼리 하 여 상한 크기를 확인할 수 있습니다 `SHOW GLOBAL VARIABLES` `relay_log_space_limit` .|
|Seconds_Behind_Master| 복제 대기 시간(초)을 표시합니다.|
|Last_IO_Errno|IO 스레드 오류 코드가 있는 경우 이를 표시합니다. 이러한 코드에 대한 자세한 내용은 [MySQL 서버 오류 메시지 참조](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html)를 참조하세요.|
|Last_IO_Errno| IO 스레드 오류 메시지가 있는 경우 이를 표시합니다.|
|Last_SQL_Errno|SQL 스레드 오류 코드가 있는 경우 이를 표시합니다. 이러한 코드에 대한 자세한 내용은 [MySQL 서버 오류 메시지 참조](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html)를 참조하세요.|
|Last_SQL_Error|SQL 스레드 오류 메시지가 있는 경우 이를 표시합니다.|
|Slave_SQL_Running| 현재 SQL 스레드 상태를 나타냅니다. 이 상태에서 `System lock`은 정상입니다. 또한 `Waiting for dependent transaction to commit`의 상태가 표시되는 것이 정상입니다. 이 상태는 복제본이 원본 서버가 커밋된 트랜잭션을 업데이트할 때까지 대기하고 있다는 것을 나타냅니다.|

Slave_IO_Running이 `Yes`이고 Slave_SQL_Running이 `Yes`인 경우 복제가 정상적으로 실행되고 있습니다. 

다음으로 Last_IO_Errno, Last_IO_Error, Last_SQL_Errno 및 Last_SQL_Error를 확인합니다.  이러한 필드에는 SQL 스레드를 중단한 가장 최근 오류에 대한 오류 번호 및 오류 메시지가 표시됩니다. 오류 번호 `0`과 빈 메시지는 오류 없음을 의미합니다. [MySQL 서버 오류 메시지 참조](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html)에서 오류 코드를 확인하여 0이 아닌 오류 값을 조사합니다.

## <a name="common-scenarios-for-high-replication-latency"></a>높은 복제 대기 시간의 일반적인 시나리오

다음 섹션에서는 높은 복제 대기 시간이 일반적인 시나리오를 다룹니다.

### <a name="network-latency-or-high-cpu-consumption-on-the-source-server"></a>원본 서버의 네트워크 대기 시간 또는 높은 CPU 사용량

다음 값이 표시되는 경우 네트워크 대기 시간이 길거나 원본 서버에서 CPU 사용량이 많은 경우에 복제 대기 시간이 발생할 수 있습니다.

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

이 경우 IO 스레드가 실행 중이며 원본 서버에서 대기 중입니다. 원본 서버가 이미 이진 로그 파일 번호 20에 기록되었습니다. 복제본은 최대 10개의 파일만 수신했습니다. 이 시나리오에서 높은 복제 대기 시간의 주요 원인은 원본 서버의 네트워크 속도 또는 높은 CPU 사용률입니다.  

Azure에서 일반적으로 지역 내의 네트워크 대기 시간은 밀리초 단위로 측정될 수 있습니다. 지역들의 대기 시간은 밀리초에서 초 사이입니다.

대부분의 경우 IO 스레드와 원본 서버 간의 연결 지연은 원본 서버의 높은 CPU 사용률로 인해 발생합니다. IO 스레드는 느리게 처리됩니다. Azure Monitor를 통해 원본 서버의 CPU 사용률과 동시 연결 수를 확인하여 문제를 검색할 수 있습니다.

원본 서버에서 CPU 사용률이 높지 않은 경우 네트워크 대기 시간아 문제일 수 있습니다. 네트워크 대기 시간이 갑자기 비정상적으로 긴 경우 [Azure 상태 페이지](https://status.azure.com/status)에서 알려진 문제나 작동 중단을 확인하세요. 

### <a name="heavy-bursts-of-transactions-on-the-source-server"></a>원본 서버의 높은 트랜잭션 버스트

다음 값이 표시되는 경우 원본 서버에서 높은 트랜잭션 버스트로 인해 복제 대기 시간이 발생할 수 있습니다. 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

출력은 복제본이 원본 서버 뒤에 있는 이진 로그를 검색할 수 있음을 보여줍니다. 그러나 복제본 IO 스레드는 릴레이 로그 공간이 이미 가득 찼음을 나타냅니다.

네트워크 속도는 지연을 유발하지 않습니다. 복제본이 캐시업을 시도합니다. 그러나 업데이트된 이진 로그 크기가 릴레이 로그 공간의 상한 값을 초과합니다.

이 문제를 해결하려면 원본 서버에서 [저속 쿼리 로그](concepts-server-logs.md)를 사용하도록 설정합니다. 저속 쿼리 로그를 사용하여 원본 서버에서 장기 실행 트랜잭션을 확인할 수 있습니다. 그 다음 확인된 쿼리를 조정하여 서버의 대기 시간을 줄입니다. 

일반적으로 이러한 종류의 복제 지연은 원본 서버의 데이터 로드로 인해 발생합니다. 원본 서버에 매주 또는 매월 데이터가 로드되는 경우 복제 지연은 불가피합니다 원본 서버에서 데이터 로드가 완료된 후 복제본 서버는 데이터를 캐치업합니다.

### <a name="slowness-on-the-replica-server"></a>복제본 서버의 속도 저하

다음 값을 발견하는 경우 복제본 서버에 문제가 있을 수 있습니다.

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: The binary log file sequence equals to Relay_Master_Log_File, e.g. mysql-bin.000191
Read_Master_Log_Pos: The position of master server written to the above file is larger than Relay_Log_Pos, e.g. 103978138
Relay_Master_Log_File: mysql-bin.000191
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Exec_Master_Log_Pos: The position of slave reads from master binary log file is smaller than Read_Master_Log_Pos, e.g. 13468882
Seconds_Behind_Master: There is latency and the value here is greater than 0
```

이 시나리오에서 출력은 IO 스레드와 SQL 스레드 모두 제대로 실행되고 있음을 보여줍니다. 복제본은 원본 서버에서 작성하는 것과 동일한 이진 로그 파일을 읽습니다. 그러나 복제본 서버의 일부 지연은 원본 서버와 동일한 트랜잭션을 반영합니다.

다음 섹션에서는 이러한 종류의 지연에 대한 일반적인 원인을 설명합니다.

#### <a name="no-primary-key-or-unique-key-on-a-table"></a>테이블에 기본 키 또는 고유 키가 없습니다.

Azure Database for MySQL은 행 기반 복제를 사용합니다. 원본 서버는 이진 로그에 이벤트를 작성하여 개별 테이블 행의 변경 사항을 기록합니다. 그런 다음 SQL 스레드는 이러한 변경 사항을 복제본 서버의 해당 테이블 행에 복제합니다. 테이블에 기본 키 또는 고유 키가 없는 경우 SQL 스레드는 대상 테이블의 모든 행을 스캔하여 변경 사항을 적용합니다. 이 검사를 수행하면 복제 지연이 발생할 수 있습니다.

MySQL에서 기본 키는 NULL 값을 포함할 수 없으므로 쿼리 성능의 빠른 향상을 보장하는 관련된 인덱스입니다. InnoDB 스토리지 엔진을 사용하는 경우 테이블 데이터는 기본 키에 따라 초고속으로 조회 및 정렬이 가능하도록 물리적으로 구성됩니다.

복제본 서버를 생성하기 전에 원본 서버의 테이블에 기본 키를 추가하는 것이 좋습니다. 원본 서버에서 기본 키를 추가하고 복제 지연 개선에 도움이 되는 읽기 복제본을 재생성합니다.

다음 쿼리를 사용하여 원본 서버에 기본 키가 없는 테이블을 확인합니다.

```sql
select tab.table_schema as database_name, tab.table_name 
from information_schema.tables tab left join 
information_schema.table_constraints tco 
on tab.table_schema = tco.table_schema 
and tab.table_name = tco.table_name 
and tco.constraint_type = 'PRIMARY KEY' 
where tco.constraint_type is null 
and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') 
and tab.table_type = 'BASE TABLE' 
order by tab.table_schema, tab.table_name;

```

#### <a name="long-running-queries-on-the-replica-server"></a>복제본 서버의 장기 실행 쿼리

복제본 서버의 워크 로드로 인해 IO 스레드가 SQL 스레드보다 지연될 수 있습니다. 복제본 서버에서 장기 실행 쿼리는 높은 복제 대기 시간의 일반적인 원인 중 하나입니다. 이 문제를 해결 하려면 복제 서버에서 [저속 쿼리 로그](concepts-server-logs.md)를 사용하도록 설정합니다.

저속 쿼리를 사용하면 복제본이 원본 서버를 캐치업할 수 없도록 리소스 사용량이 증가하거나 서버 속도가 저하될 수 있습니다. 이 시나리오에서는 저속 쿼리를 조정합니다. 빠른 쿼리는 SQL 스레드를 차단을 방지하고 복제 지연을 크게 향상시킵니다.

#### <a name="ddl-queries-on-the-source-server"></a>원본 서버의 DDL 쿼리

원본 서버에서 [`ALTER TABLE`](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html)과 같은 DDL (데이터 정의 언어) 명령은 시간이 오래 소요될 수 있습니다. DDL 명령이 실행되는 동안 원본 서버에서 수천 개의 다른 쿼리가 동시에 실행 될 수 있습니다. 

DDL이 복제되면 데이터베이스 일관성을 유지하기 위해 MySQL 엔진은 단일 복제 스레드에서 DDL을 실행합니다. 이 작업을 수행하는 동안 복제된 다른 모든 쿼리가 차단되며, 복제본 서버에서 DDL 작업이 완료될 때까지 기다려야 합니다. 온라인 DDL 작업을 수행하는 경우에도 이러한 지연이 발생합니다. DDL 작업을 수행하면 복제 지연이 증가합니다.

원본 서버에서 [저속 쿼리 로그](concepts-server-logs.md)를 사용하도록 설정한 경우 원본 서버에서 실행된 DDL 명령을 확인하여 이 지연 문제를 검색할 수 있습니다. 인덱스 삭제, 이름 변경 및 생성을 통해 ALTER TABLE 내부의 INPLACE 알고리즘을 사용할 수 있습니다. 테이블 데이터를 복사하고 테이블을 다시 빌드해야 할 수 있습니다.

일반적으로 동시 DML이 INPLACE 알고리즘에 지원됩니다. 그러나 작업을 준비하고 실행할 때 테이블에 대한 전용 메타 데이터를 잠시 잠글 수 있습니다. 따라서 CREATE INDEX 문의 경우 절 알고리즘과 잠금을 사용하여 테이블 복사 방법과 읽기 및 쓰기의 동시성 수준에 영향을 줄 수 있습니다. 전체 텍스트 인덱스 또는 공간 인덱스를 추가하여 DML 작업을 계속 방지할 수 있습니다.

다음 예에서 알고리즘 및 잠금 절을 사용하여 인덱스를 생성됩니다.

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

그러나 잠금이 필요한 DDL 문의 경우 복제 지연을 방지할 수 없습니다. 잠재적인 영향을 줄이기 위해 이러한 유형의 DDL 작업은 사용량이 적은 시간(예: 야간)에 수행됩니다.

#### <a name="downgraded-replica-server"></a>복제본 서버 다운그레이드

Azure Database for MySQL에서 읽기 복제본은 원본 서버와 동일한 서버 구성을 사용합니다. 복제본이 생성된 후 복제본 서버 구성을 변경할 수 있습니다.

복제본 서버가 다운그레이드되면 워크로드의 리소스 사용이 증가하며,이로 인해 복제 지연이 발생할 수 있습니다. 이 문제를 검색하려면 Azure Monitor를 사용하여 복제본 서버의 CPU 및 메모리 사용량을 확인하세요.

이 시나리오에서는 복제본 서버의 구성을 원본 서버 값 보다 크거나 같은 값으로 유지하는 것이 좋습니다. 이 구성을 사용하면 복제본을 원본 서버와 함께 유지할 수 있습니다.

#### <a name="improving-replication-latency-by-tuning-the-source-server-parameters"></a>원본 서버 매개 변수 조정을 통한 복제 지연 향상

Azure Database for MySQL에서 복제는 기본적으로 복제본의 병렬 스레드와 함께 실행되도록 최적화됩니다 원본 서버에서 동시성아 높은 작업 수행으로 인해 복제본 서버가 중단되는 경우 원본 서버에서 binlog_group_commit_sync_delay 매개 변수를 구성해 복제 지연을 향상시킬 수 있습니다.

binlog_group_commit_sync_delay 매개 변수는 이진 로그 파일을 동기화하기 전에 이진 로그 커밋이 대기하는 시간(마이크로초)을 제어합니다. 이 매개 변수의 이점은 커밋된 모든 트랜잭션을 즉시 적용하는 대신 원본 서버에서 이진 로그 업데이트를 대량으로 전송한다는 것입니다. 이러한 지연은 복제본의 IO가 감소와 성능 향상을 야기합니다.

binlog_group_commit_sync_delay 매개 변수를 1000으로 설정하는 것이 유용할 수 있습니다. 그런 다음 복제 지연을 모니터링합니다. 이 매개 변수를 신중하게 설정하고 동시성이 높은 워크로드에만 사용하세요.

> [!IMPORTANT]
> 복제본 서버에서 binlog_group_commit_sync_delay 매개 변수는 0으로 설정하는 것이 권장됩니다. 원본 서버와 달리 복제본 서버는 동시성이 높지 않으며 복제본 서버에서 binlog_group_commit_sync_delay 값을 늘리면 복제 대기 시간이 증가할 수 있으므로 이러한 방법이 권장됩니다.

여러 싱글톤 트랜잭션을 포함하며 동시성이 낮은 워크로드의 경우 binlog_group_commit_sync_delay 설정을 통해 대기 시간이 증가할 수 있습니다. 일부 트랜잭션만 커밋된 경우에도 IO 스레드가 대량의 이진 로그 업데이트를 기다리기 때문에 대기 시간이 늘어날 수 있습니다.

## <a name="next-steps"></a>다음 단계

[MySQL binlog 복제 개요](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)를 확인하세요.
