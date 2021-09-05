---
title: 입력 데이터 복제 구성 - Azure Database for MySQL Flexible Server
description: 이 문서에서는 Azure Database for MySQL Flexible Server에 대해 입력 데이터 복제를 설정하는 방법을 설명합니다.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: how-to
ms.date: 06/08/2021
ms.openlocfilehash: ee0bafdfe7d7caae2d4ba65e9967d9c46e6b3e3c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536118"
---
# <a name="how-to-configure-azure-database-for-mysql-flexible-server-data-in-replication"></a>Azure Database for MySQL Flexible Server 입력 데이터 복제를 구성하는 방법

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

이 문서에서는 원본 및 복제본 서버를 구성하여 Azure Database for MySQL Flexible Server에서 [입력 데이터 복제](concepts-data-in-replication.md)를 설정하는 방법을 설명합니다. 이 문서에서는 이전에 MySQL 서버 및 데이터베이스를 사용한 경험이 있다고 가정합니다.

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 _슬레이브_ 라는 용어에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.

Azure Database for MySQL Flexible 서비스에서 복제본을 만들기 위해 [입력 데이터 복제](concepts-data-in-replication.md)가 온-프레미스, VM(가상 머신) 또는 클라우드 데이터베이스 서비스에서 원본 MySQL 서버의 데이터를 동기화합니다. 입력 데이터 복제는 위치 기반의 이진 로그(binlog) 파일을 기반으로 합니다. binlog 복제에 대한 자세히 알려면 [MySQL binlog 복제 개요](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)를 참조합니다.

이 문서의 단계를 수행하기 전에 입력 데이터 복제에 대한 [제한 사항 및 요구 사항](concepts-data-in-replication.md#limitations-and-considerations)을 검토합니다.

## <a name="create-an-azure-database-for-mysql-flexible-server-instance-to-use-as-a-replica"></a>복제본으로 사용할 Azure Database for MySQL Flexible Server 인스턴스 만들기

1. Azure Database for MySQL Flexible Server의 새 인스턴스 만들기(예: "replica.mysql.database.azure.com")를 만듭니다. 서버를 만드는 방법은 [Azure Portal을 사용하여 Azure Database for MySQL Flexible Server 서버 만들기](quickstart-create-server-portal.md)를 참조하세요. 이 서버는 입력 데이터 복제에 대한 "복제본" 서버입니다.

2. 동일한 사용자 계정 및 해당 권한을 만듭니다.

   사용자 계정은 원본 서버에서 복제본 서버로 복제되지 않습니다. 사용자에게 복제본 서버에 대한 액세스 권한을 제공하려는 경우 새로 만든 이 Azure Database for MySQL 유연한 서버에서 모든 계정과 해당 권한을 수동으로 만들어야 합니다.

## <a name="configure-the-source-mysql-server"></a>원본 MySQL 서버 구성

다음 단계에서는 입력 데이터 복제를 위해 온-프레미스, 가상 머신 또는 다른 클라우드 공급자가 호스트하는 데이터베이스 서비스에서 호스트되는 MySQL 서버를 준비하고 구성합니다. 이 서버는 입력 데이터 복제에 대한 "원본" 서버입니다.

1. 계속하기 전에 [원본 서버 요구 사항](concepts-data-in-replication.md#requirements)을 검토합니다.

2. 네트워킹 요구 사항 
    * 원본 서버가 3306 포트에서 인바운드 및 아웃바운드 트래픽을 모두 허용하고 **공용 IP 주소** 가 있는지, DNS에 공개적으로 액세스할 수 있는지, 아니면 FQDN(정규화된 도메인 이름)이 있는지 확인합니다.

    * 개인 액세스를 사용 중인 경우 원본 서버와 복제본 서버가 호스트되는 Vnet 간에 연결되어 있어야 합니다. 
    * [ExpressRoute](../../expressroute/expressroute-introduction.md) 또는 [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)을 사용하여 온-프레미스 원본 서버에 대한 사이트 간 연결이 제공되는지 확인하세요. 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Virtual Network 설명서](../../virtual-network/index.yml)를 참조하세요. 특히 단계별 세부 정보를 제공하는 빠른 시작 문서를 참조하세요.
    * 복제본 서버에서 개인 액세스가 사용되고 원본이 Azure VM인 경우 VNet 간 연결이 설정되어 있는지 확인합니다. VNet-Vnet 피어링이 지원됩니다. 다른 연결 방법을 사용하여 VNet 간 연결과 같이 여러 지역에서 VNet 간에 통신할 수도 있습니다. 자세한 내용은 [VNet 간 VPN 게이트웨이](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)를 참조하세요.
    * 가상 네트워크 네트워크 보안 그룹 규칙이 아웃바운드 포트 3306(MySQL이 Azure VM에서 실행 중인 경우에도 인바운드)을 차단하지 않는지 확인합니다. 가상 네트워크 NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](../../virtual-network/virtual-network-vnet-plan-design-arm.md) 문서를 참조하세요.
    * 복제 서버 IP 주소를 허용하도록 원본 서버의 방화벽 규칙을 구성합니다.

  
3. 이진 로깅을 설정합니다.

   다음 명령을 실행하여 원본에서 이진 로깅이 사용하도록 설정되어 있는지 확인합니다.

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) 변수가 "ON" 값으로 반환되면 서버에서 이진 로깅이 사용하도록 설정됩니다.

   `log_bin`이 "OFF" 값으로 반환되고 원본 서버가 온-프레미스 또는 구성 파일(my.cnf)에 액세스할 수 있는 가상 머신에서 실행되는 경우 다음 단계를 수행할 수 있습니다.
   1. 원본 서버에서 MySQL 구성 파일(my.cnf)을 찾습니다. 예를 들어: /etc/my.cnf입니다.
   2. 구성 파일을 열어 편집하고, 파일에서 **mysqld** 섹션을 찾습니다.
   3. mysqld 섹션에서 다음 줄을 추가합니다.

       ```bash
       log-bin=mysql-bin.log
       ```

   4. 변경 사항을 적용하려면 원본 서버에서 MySQL 서비스를 다시 시작합니다.
   5. 서버가 다시 시작되면 이전과 동일한 쿼리를 실행하여 이진 로깅이 사용하도록 설정되었는지 확인합니다.

      ```sql
      SHOW VARIABLES LIKE 'log_bin';
      ```

4. 원본 서버 설정을 구성합니다.

   입력 데이터 복제를 사용하려면 원본 서버와 복제본 서버 간에 `lower_case_table_names` 매개 변수가 일치해야 합니다. Azure Database for MySQL유연한 서버에서 이 매개 변수는 기본적으로 1입니다.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

5. 새 복제 역할을 만들고, 권한을 설정합니다.

   원본 서버에서 복제 권한으로 구성된 사용자 계정을 만듭니다. 이 작업은 SQL 명령 또는 MySQL Workbench와 같은 도구를 통해 수행할 수 있습니다. 사용자를 만들 때 지정해야 하므로 SSL을 사용하여 복제할지 여부를 고려합니다. 원본 서버에서 [사용자 계정을 추가](https://dev.mysql.com/doc/refman/5.7/en/user-names.html)하는 방법을 이해하려면 MySQL 설명서를 참조하세요.

   다음 명령에서 새로 만든 복제 역할은 원본 자체를 호스팅하는 컴퓨터뿐만 아니라 모든 컴퓨터에서도 원본에 액세스할 수 있습니다. 이 작업은 create user 명령에 “syncuser@’%’”를 지정하여 수행합니다. [계정 이름 지정](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)에 대한 자세한 내용은 MySQL 설명서를 참조하세요.

   **SQL 명령**

   *SSL을 사용한 복제*

   모든 사용자 연결에 SSL이 필요하도록 설정하려면 다음 명령을 사용하여 사용자를 만듭니다.

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *SSL 없이 복제*

   모든 연결에 SSL이 필요하지 않은 경우 다음 명령을 사용하여 사용자를 만듭니다.

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   MySQL Workbench에서 복제 역할을 만들려면 **관리** 패널에서 **사용자 및 권한** 패널을 연 다음, **계정 추가** 를 선택합니다.

   :::image type="content" source="./media/how-to-data-in-replication/users-privileges.png" alt-text="사용자 및 권한":::

   **로그인 이름** 필드에 사용자 이름을 입력합니다.

   :::image type="content" source="./media/how-to-data-in-replication/sync-user.png" alt-text="사용자 동기화":::

   **관리 역할** 패널을 선택한 다음, **전체 권한** 목록에서 **복제 슬레이브** 를 선택합니다. 그런 다음, **적용** 을 선택하여 복제 역할을 만듭니다.

   :::image type="content" source="./media/how-to-data-in-replication/replication-slave.png" alt-text="복제 슬레이브":::

6. 원본 서버를 읽기 전용 모드로 설정합니다.

   데이터베이스를 덤프하기 전에 서버를 읽기 전용 모드로 설정해야 합니다. 읽기 전용 모드에 있는 동안 원본 서버에서 쓰기 트랜잭션을 처리할 수 없습니다. 비즈니스에 미치는 영향을 평가하고, 필요한 경우 사용량이 적은 시간에 읽기 전용 창을 예약합니다.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

7. 이진 로그 파일 이름 및 오프셋을 가져옵니다.

   [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) 명령을 실행하여 현재 이진 로그 파일 이름 및 오프셋을 확인합니다.

   ```sql
    show master status;
   ```
   결과는 다음과 비슷합니다. 이후 단계에서 사용할 수 있도록 이진 파일 이름을 적어 두세요.

   :::image type="content" source="./media/how-to-data-in-replication/master-status.png" alt-text="마스터 상태 결과":::

## <a name="dump-and-restore-the-source-server"></a>원본 서버 덤프 및 복원

1. Azure Database for MySQL Flexible Server에 복제할 데이터베이스와 테이블을 결정하고, 원본 서버에서 덤프를 수행합니다.

    mysqldump를 사용하여 주 서버에서 데이터베이스를 덤프할 수 있습니다. 자세한 내용은 [덤프 및 복원](../concepts-migrate-dump-restore.md)을 참조하세요. MySQL 라이브러리 및 테스트 라이브러리는 덤프할 필요가 없습니다.


3. 원본 서버를 읽기/쓰기 모드로 설정합니다.

   데이터베이스가 덤프되면 원본 MySQL 서버를 읽기/쓰기 모드로 다시 변경합니다.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

4. 덤프 파일을 새 서버로 복원합니다.

   덤프 파일을 Azure Database for MySQL Flexible Server 서비스에서 생성된 서버로 복원합니다. 덤프 파일을 MySQL 서버로 복원하는 방법은 [덤프 및 복원](../concepts-migrate-dump-restore.md)을 참조하세요. 덤프 파일이 큰 경우, 복제본 서버와 동일한 지역 내의 Azure 가상 머신에 업로드합니다. 가상 머신에서 Azure Database for MySQL Flexible Server 서버로 복원합니다.


## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>원본 서버와 복제본 서버를 연결하여 입력 데이터 복제 시작

1. 원본 서버를 설정합니다.

   모든 입력 데이터 복제 기능은 저장 프로시저에 의해 수행됩니다. [입력 데이터 복제 저장 프로시저](../reference-stored-procedures.md)에서 모든 프로시저를 확인할 수 있습니다. 저장 프로시저는 MySQL 셸 또는 MySQL Workbench에서 실행할 수 있습니다.

   두 서버를 연결하고 복제를 시작하려면 Azure DB for MySQL 서비스에서 대상 복제본 서버에 로그인하고 외부 인스턴스를 원본 서버로 설정합니다. 이 작업은 Azure DB for MySQL 서버의 `mysql.az_replication_change_master` 저장 프로시저를 사용하여 수행합니다.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', <master_port>, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

 
   - master_host: 원본 서버의 호스트 이름
   - master_user: 원본 서버의 사용자 이름
   - master_password: 원본 서버의 암호
   - master_port: 원본 서버에서 연결을 수신 대기하는 포트 번호 (3306은 MySQL에서 수신 대기하는 기본 포트임)
   - master_log_file: 실행 중인 `show master status`의 이진 로그 파일 이름
   - master_log_pos: 실행 중인 `show master status`의 이진 로그 위치
   - master_ssl_ca: CA 인증서의 컨텍스트. SSL을 사용하지 않는 경우 빈 문자열을 전달합니다.

     이 매개 변수를 변수로 전달하는 것이 좋습니다. 자세한 내용은 다음 예를 참조하세요.

   > [!NOTE]
   > 원본 서버가 Azure VM에서 호스팅되는 경우 원본 서버와 복제본 서버가 서로 통신할 수 있도록 "Azure 서비스에 대한 액세스 허용"을 "켜기"로 설정합니다. 이 설정은 **연결 보안** 옵션에서 변경할 수 있습니다. 자세한 내용은 [포털을 사용하여 방화벽 규칙 관리](how-to-manage-firewall-portal.md)를 참조하세요.

   **예제**

   *SSL을 사용한 복제*

   `@cert` 변수는 다음 MySQL 명령을 실행하여 만듭니다.

      ```sql
      SET @cert = '-----BEGIN CERTIFICATE-----
      PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
      -----END CERTIFICATE-----'
      ```

   SSL을 사용하는 복제는 "companya.com" 도메인에서 호스트되는 원본 서버와 Azure Database for MySQL Flexible Server에서 호스트되는 복제본 서버 간에 설정됩니다. 이 저장 프로시저는 복제본에서 실행됩니다.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
      ```

   *SSL 없이 복제*

   SSL을 사용하지 않는 복제는 "companya.com" 도메인에서 호스트되는 원본 서버와 Azure Database for MySQL Flexible Server에서 호스트되는 복제본 서버 간에 설정됩니다. 이 저장 프로시저는 복제본에서 실행됩니다.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
      ```
2. 복제를 시작합니다.

   `mysql.az_replication_start` 저장 프로시저를 호출하여 복제를 시작합니다.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. 복제 상태를 확인합니다.

   복제본 서버에서 [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) 명령을 호출하여 복제 상태를 확인합니다.

   ```sql
   show slave status;
   ```

   `Slave_IO_Running` 및 `Slave_SQL_Running`의 상태가 "yes"이고 `Seconds_Behind_Master`의 값이 "0"이면 복제가 제대로 작동하는 것입니다. `Seconds_Behind_Master`는 복제본이 얼마나 지연되었는지를 나타냅니다. 값이 "0"이 아니면 복제본 서버에서 업데이트를 처리하고 있는 것입니다.

## <a name="other-useful-stored-procedures-for-data-in-replication-operations"></a>입력 데이터 복제 작업에 대한 기타 유용한 저장 프로시저

### <a name="stop-replication"></a>복제 중지

원본 서버와 복제본 서버 간의 복제를 중지하려면 다음 저장 프로시저를 사용합니다.

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>복제 관계 제거

원본 서버와 복제본 서버 간의 관계를 제거하려면 다음 저장 프로시저를 사용합니다.

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>복제 오류 건너뛰기

복제 오류를 건너뛰고 복제를 계속 진행하려면 다음 저장 프로시저를 사용합니다.

```sql
CALL mysql.az_replication_skip_counter;
```

```sql
SHOW BINLOG EVENTS [IN 'log_name'] [FROM pos][LIMIT [offset,] row_count]
```

  :::image type="content" source="./media/how-to-data-in-replication/show-binary-log.png" alt-text="이진 로그 결과 표시":::

## <a name="next-steps"></a>다음 단계

- Azure Database for MySQL Flexible Server를 위한 [입력 데이터 복제](concepts-data-in-replication.md)에 대해 자세히 알아보세요.
