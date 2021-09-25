---
title: 입력 데이터 복제 구성 - Azure Database for MariaDB
description: 이 문서에서는 Azure Database for MariaDB에서 입력 데이터 복제를 설정하는 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 01/18/2021
ms.openlocfilehash: d7590ac05693f7731ee444e0e6ecb243eef80d3e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128652171"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Azure Database for MariaDB에서 입력 데이터 복제 구성

이 문서에서는 원본 및 복제본 서버를 구성하여 Azure Database for MariaDB에서 [입력 데이터 복제](concepts-data-in-replication.md)를 설정하는 방법에 대해 설명합니다. 이 문서에서는 이전에 MariaDB 서버 및 데이터베이스를 사용한 경험이 있다고 가정합니다.

Azure Database for MariaDB 서비스에서 복제본을 만들기 위해 [입력 데이터 복제](concepts-data-in-replication.md)가 온-프레미스, VM(가상 머신) 또는 클라우드 데이터베이스 서비스에서 원본 MariaDB 서버의 데이터를 동기화합니다. 입력 데이터 복제는 MariaDB에 네이티브인 이진 로그(binlog) 파일 위치 기반 복제를 기반으로 합니다. binlog 복제에 대해 자세히 알아보려면 [binlog 복제 개요](https://mariadb.com/kb/en/library/replication-overview/)를 참조하세요.

이 문서의 단계를 수행하기 전에 입력 데이터 복제에 대한 [제한 사항 및 요구 사항](concepts-data-in-replication.md#limitations-and-considerations)을 검토합니다.

> [!NOTE]
> 원본 서버 버전이 10.2 이상인 경우 [전역 트랜잭션 ID](https://mariadb.com/kb/en/library/gtid/)를 사용하여 입력 데이터 복제를 설정하는 것이 좋습니다.

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 *slave* 에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.

## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>복제본으로 사용할 MariaDB 서버 만들기

1. 새 Azure Database for MariaDB 서버를 만듭니다(예: replica.mariadb.database.azure.com). 이 서버는 입력 데이터 복제의 복제본 서버입니다.

    서버를 만드는 방법은 [Azure Portal을 사용하여 Azure Database for MariaDB 서버 만들기](quickstart-create-mariadb-server-database-using-azure-portal.md)를 참조하세요.

   > [!IMPORTANT]
   > Azure Database for MariaDB 서버를 범용 또는 메모리 최적화 가격 책정 계층에 생성해야 합니다.

2. 동일한 사용자 계정 및 해당 권한을 만듭니다.

    사용자 계정은 원본 서버에서 복제본 서버로 복제되지 않습니다. 복제본 서버에 대한 액세스 권한을 사용자에게 제공하려면, 새로 만든 이 Azure Database for MariaDB 서버에서 모든 계정과 해당 권한을 수동으로 만들어야 합니다.

3. 원본 서버의 IP 주소를 복제본의 방화벽 규칙에 추가합니다. 

   [Azure Portal](howto-manage-firewall-portal.md) 또는 [Azure CLI](howto-manage-firewall-cli.md)를 사용하여 방화벽 규칙을 업데이트합니다.

## <a name="configure-the-source-server"></a>원본 서버 구성

다음 단계에서는 입력 데이터 복제를 위해 온-프레미스, VM 또는 클라우드 데이터베이스 서비스에서 호스트되는 MariaDB 서버를 준비하고 구성합니다. MariaDB 서버는 입력 데이터 복제 원본입니다.

1. 계속하기 전에 [주 서버 요구 사항](concepts-data-in-replication.md#requirements)을 검토합니다. 

2. 원본 서버가 3306 포트에서 인바운드 및 아웃바운드 트래픽을 모두 허용하고 **공용 IP 주소** 가 있는지, DNS에 공개적으로 액세스할 수 있는지, 아니면 FQDN(정규화된 도메인 이름)이 있는지 확인합니다. 

   다른 머신에서 호스트되는 MySQL 명령줄과 같은 도구 또는 Azure Portal에서 사용할 수 있는 [Azure Cloud Shell](../cloud-shell/overview.md)에서 연결을 시도하여 원본 서버에 대한 연결을 테스트합니다.

   조직에 엄격한 보안 정책이 있고 원본 서버의 모든 IP 주소가 Azure에서 원본 서버로 통신할 수 있도록 허용하지 않는 경우 아래 명령을 사용하여 Azure Database for MariaDB 서버의 IP 주소를 확인할 수 있습니다.

   1. MySQL 명령줄과 같은 도구를 사용하여 Azure Database for MariaDB에 로그인합니다.
   2. 아래 쿼리를 실행합니다.

      ```bash
      mysql> SELECT @@global.redirect_server_host;
      ```

      다음은 몇 가지 샘플 출력입니다.

      ```bash
      +-----------------------------------------------------------+
      | @@global.redirect_server_host                             |
      +-----------------------------------------------------------+
      | e299ae56f000.tr1830.westus1-a.worker.database.windows.net |
       +-----------------------------------------------------------+
      ```

   3. MySQL 명령줄을 종료합니다.
   4. Ping 유틸리티에서 아래 단계를 실행하여 IP 주소를 가져옵니다.

      ```bash
      ping <output of step 2b>
      ```

      예를 들면 다음과 같습니다.

      ```bash
      C:\Users\testuser> ping e299ae56f000.tr1830.westus1-a.worker.database.windows.net
      Pinging tr1830.westus1-a.worker.database.windows.net (**11.11.111.111**) 56(84) bytes of data.
      ```

   5. 이전 단계의 출력된 IP 주소를 3306 포트에 포함하도록 원본 서버의 방화벽 규칙을 구성합니다.

   > [!NOTE]
   > 이 IP 주소는 유지 관리/배포 작업으로 인해 변경될 수 있습니다. 이 연결 방법은 3306 포트에서 모든 IP 주소를 허용할 여유가 없는 고객만을 위한 것입니다.

3. 이진 로깅을 설정합니다.

    주 복제본에서 이진 로깅이 사용 설정되어 있는지 확인하려면 다음 명령을 입력합니다.

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) 변수가 `ON` 값을 반환하면 서버에서 이진 로깅이 사용하도록 설정됩니다.

   `log_bin`에서 `OFF` 값을 반환하는 경우 `log_bin=ON`이 이진 로깅을 설정하도록 **my.cnf** 파일을 편집합니다. 서버를 다시 시작하여 변경 내용을 적용합니다.

4. 원본 서버 설정을 구성합니다.

    입력 데이터 복제를 사용하려면 원본 서버와 복제본 서버 간에 `lower_case_table_names` 매개 변수가 일치해야 합니다. Azure Database for MariaDB에서 `lower_case_table_names` 매개 변수는 기본적으로 `1`입니다.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

5. 새 복제 역할을 만들고, 권한을 설정합니다.

   원본 서버에서 복제 권한으로 구성된 사용자 계정을 만듭니다. SQL 명령 또는 MySQL Workbench를 사용하여 계정을 만들 수 있습니다. SSL로 복제할 계획인 경우 사용자 계정을 만들 때 이를 지정해야 합니다.

   원본 서버에서 사용자 계정을 추가하는 방법에 대한 자세한 내용은 [MariaDB 설명서](https://mariadb.com/kb/en/library/create-user/)를 참조하세요.

   다음 명령을 사용하여 새로운 복제 역할은 원본 자체를 호스트하는 머신뿐만 아니라 모든 머신에서도 원본에 액세스할 수 있습니다. 이 액세스의 경우 사용자를 만들기 위해 명령에 **syncuser \@‘%’** 를 지정합니다.

   MariaDB 설명서에 관한 자세한 내용은 [계정 이름 지정](https://mariadb.com/kb/en/library/create-user/#account-names)을 참조하세요.

   **SQL 명령**

   - SSL을 사용한 복제

       모든 사용자 연결에 SSL이 필요하도록 설정하려면 다음 명령을 입력하여 사용자를 만듭니다.

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - SSL 없이 복제

       모든 연결에 SSL이 필요하지 않은 경우 다음 명령을 입력하여 사용자를 만듭니다.

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   MySQL Workbench에서 복제 역할을 만들려면 **관리** 창에서 **사용자 및 권한** 을 선택합니다. 그러고 나서 **계정 추가** 를 선택합니다.

   ![사용자 및 권한](./media/howto-data-in-replication/users_privileges.png)

   **로그인 이름** 필드에 사용자 이름을 입력합니다.

   ![사용자 동기화](./media/howto-data-in-replication/syncuser.png)

   **관리 역할** 패널을 선택한 다음, **전체 권한** 목록에서 **복제 슬레이브** 를 선택합니다. **적용** 을 선택하여 복제 역할을 만듭니다.

   ![복제 슬레이브](./media/howto-data-in-replication/replicationslave.png)

6. 원본 서버를 읽기 전용 모드로 설정합니다.

   데이터베이스를 덤프하려면 서버가 읽기 전용 모드여야 합니다. 읽기 전용 모드에 있는 동안 원본 서버에서 쓰기 트랜잭션을 처리할 수 없습니다. 비즈니스 영향을 방지하기 위해, 사용량이 적은 시간에 읽기 전용 창을 예약합니다.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

7. 현재 이진 로그 파일 이름 및 오프셋을 가져옵니다.

   [`show master status`](https://mariadb.com/kb/en/library/show-master-status/) 명령을 실행하여 현재 이진 로그 파일 이름 및 오프셋을 확인합니다.

   ```sql
   show master status;
   ```

   결과는 다음 테이블과 유사합니다.

   ![마스터 상태 결과](./media/howto-data-in-replication/masterstatus.png)

   이후 단계에서 이진 파일 이름이 사용되므로 적어 두세요.

8. GTID 위치를 가져옵니다(선택 사항, GTID를 통한 복제에 필요).

   [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) 함수를 실행하여 해당 binlog 파일 이름 및 오프셋에 대한 GTID 위치를 가져옵니다.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```

## <a name="dump-and-restore-the-source-server"></a>원본 서버 덤프 및 복원

1. 원본 서버에서 모든 데이터베이스를 덤프합니다.

   mysquldump를 사용하여 원본 서버에서 모든 데이터베이스를 덤프합니다. MySQL 라이브러리 및 테스트 라이브러리는 덤프할 필요가 없습니다.

    자세한 내용은 [덤프 및 복원](howto-migrate-dump-restore.md)을 참조하세요.

2. 원본 서버를 읽기/쓰기 모드로 설정합니다.

   데이터베이스가 덤프되면 원본 MariaDB 서버를 읽기/쓰기 모드로 다시 변경합니다.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. 덤프 파일을 새 서버로 복원합니다.

   덤프 파일을 Azure Database for MariaDB 서비스에서 생성된 서버로 복원합니다. 덤프 파일을 MariaDB 서버로 복원하는 방법은 [덤프 및 복원](howto-migrate-dump-restore.md)을 참조하세요.

   덤프 파일이 큰 경우, 복제본 서버와 동일한 지역 내의 Azure의 VM에 업로드합니다. VM에서 Azure Database for MariaDB 서버로 복원합니다.

## <a name="link-the-source-and-replica-servers-to-start-data-in-replication"></a>원본 서버와 복제본 서버를 연결하여 입력 데이터 복제 시작

1. 원본 서버를 설정합니다.

   모든 데이터 내부 복제 기능은 저장 프로시저에 의해 수행됩니다. [데이터 내부 복제 저장 프로시저](reference-stored-procedures.md)에서 모든 프로시저를 확인할 수 있습니다. 저장 프로시저는 MySQL 셸 또는 MySQL Workbench에서 실행할 수 있습니다.

   두 서버를 연결하고 복제를 시작하려면 Azure DB for MariaDB 서비스에서 대상 복제본 서버에 로그인합니다. 그런 다음, Azure DB For MariaDB 서버에서 `mysql.az_replication_change_master` 또는 `mysql.az_replication_change_master_with_gtid` 저장 프로시저를 사용하여 외부 인스턴스를 원본 서버로 설정합니다.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   또는

   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: 원본 서버의 호스트 이름
   - master_user: 원본 서버의 사용자 이름
   - master_password: 원본 서버의 암호
   - master_log_file: 실행 중인 `show master status`의 이진 로그 파일 이름
   - master_log_pos: 실행 중인 `show master status`의 이진 로그 위치
   - master_gtid_pos: `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`를 실행하는 GTID 위치
   - master_ssl_ca: CA 인증서의 컨텍스트. SSL을 사용하지 않는 경우 빈 문자열을 전달합니다.*


    *master_ssl_ca 매개 변수를 변수로 전달하는 것이 좋습니다. 자세한 내용은 다음 예를 참조하세요.

   **예제**

   - SSL을 사용한 복제

       다음 명령을 실행하여 `@cert` 변수를 만듭니다.

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE\'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       SSL을 사용하는 복제는 companya.com 도메인에서 호스트되는 원본 서버와 Azure Database for MariaDB에서 호스트되는 복제본 서버 간에 설정됩니다. 이 저장 프로시저는 복제본에서 실행됩니다.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```

   - SSL 없이 복제

       SSL을 사용하지 않는 복제는 companya.com 도메인에서 호스트되는 원본 서버와 Azure Database for MariaDB에서 호스트되는 복제본 서버 간에 설정됩니다. 이 저장 프로시저는 복제본에서 실행됩니다.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. 복제를 시작합니다.

   `mysql.az_replication_start` 저장 프로시저를 호출하여 복제를 시작합니다.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. 복제 상태를 확인합니다.

   복제본 서버에서 [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) 명령을 호출하여 복제 상태를 확인합니다.

   ```sql
   show slave status;
   ```

   `Slave_IO_Running` 및 `Slave_SQL_Running`의 상태가 `yes`이고 `Seconds_Behind_Master`의 값이 `0`이면 복제가 제대로 작동하는 것입니다. `Seconds_Behind_Master`는 복제본이 얼마나 지연되었는지를 나타냅니다. 값이 `0`이 아니면 복제본 서버에서 업데이트를 처리하고 있는 것입니다.

4. 해당 서버 변수를 업데이트하여 입력 데이터 복제를 더 안전하게 만듭니다(GTID가 없는 복제에만 필요).

    MariaDB의 네이티브 복제 제한으로 인해 GTID 시나리오 없이 복제 시 [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) 및 [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) 변수를 설정해야 합니다.

    복제본 서버의 `sync_master_info` 및 `sync_relay_log_info` 변수를 확인하여 입력 데이터 복제가 안정적인지 확인하고 변수를 `1`로 설정합니다.

## <a name="other-stored-procedures"></a>기타 저장 프로시저

### <a name="stop-replication"></a>복제 중지

원본 서버와 복제본 서버 간의 복제를 중지하려면 다음 저장 프로시저를 사용합니다.

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>복제 관계 제거

원본 서버와 복제본 서버 간의 관계를 제거하려면 다음 저장 프로시저를 사용합니다.

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>복제 오류를 건너뜁니다.

복제 오류를 건너뛰고 복제를 허용하려면 다음 저장 프로시저를 사용합니다.

```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>다음 단계

Azure Database for MariaDB를 위한 [입력 데이터 복제](concepts-data-in-replication.md)에 대해 자세히 알아보세요.
