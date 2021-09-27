---
title: 입력 데이터 복제를 사용 하 여 Azure Database for MySQL Amazon RDS for MySQL 마이그레이션
description: 이 문서에서는 입력 데이터 복제를 사용 하 여 Amazon RDS for MySQL을 Azure Database for MySQL로 마이그레이션하는 방법을 설명 합니다.
author: SudheeshGH
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 09/24/2021
ms.openlocfilehash: d05806b9d69543fdd4f5584661a6b8b74ac1f01e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647160"
---
# <a name="migrate-amazon-rds-for-mysql-to-azure-database-for-mysql-using-data-in-replication"></a>데이터 내 복제를 사용 하 여 Azure Database for MySQL로 Amazon RDS for MySQL 마이그레이션

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 ‘슬레이브’에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.

Mysql 덤프 및 복원, MySQL 워크 벤치 내보내기/가져오기 또는 Azure Database Migration Service (DMS)와 같은 다양 한 방법을 사용 하 여 MySQL 데이터베이스를 Azure Database for MySQL로 마이그레이션할 수 있습니다. 그러나 MySQL dump, restore 또는 mydumper/myloader와 같은 오픈 소스 도구의 조합을 사용 하 여 데이터에 복제를 사용 하면 가동 중지 시간을 최소화 하면서 워크 로드를 마이그레이션할 수 있습니다.

입력 데이터 복제는 이진 로그 파일 위치 메서드에 따라 데이터 변경 내용을 원본 서버에서 대상 서버로 복제하는 기술입니다. 이 시나리오에서 원본(데이터베이스 변경이 시작된 곳)으로 작동하는 MySQL 인스턴스는 업데이트 및 변경 내용을 "이벤트"로 이진 로그에 기록합니다. 이진 로그의 정보는 기록되는 데이터베이스 변경 내용에 따라 다른 로깅 형식으로 저장됩니다. 복제본은 원본에서 이진 로그를 읽고 복제본의 로컬 데이터베이스에서 이진 로그의 이벤트를 실행하도록 구성됩니다.

원본 MySQL 서버의 데이터를 대상 MySQL 서버와 동기화 하기 위해 [데이터를 복제](/azure/mysql/flexible-server/concepts-data-in-replication) 하도록 설정 하는 경우 기본 (또는 원본 데이터베이스)에서 복제본 (또는 대상 데이터베이스)으로 응용 프로그램을 선택적으로 사용할 수 있습니다.

이 자습서에서는 MySQL 용 Amazon RDS를 실행 하는 원본 서버와 Azure Database for MySQL를 실행 하는 대상 서버 간에 데이터를 복제 하도록 설정 하는 방법에 대해 알아봅니다.

## <a name="performance-considerations"></a>성능 고려 사항

이 자습서를 시작 하기 전에 작업을 수행 하는 데 사용할 클라이언트 컴퓨터의 위치 및 용량에 대 한 성능 영향을 고려해 야 합니다.

### <a name="client-location"></a>클라이언트 위치

데이터베이스 서버와 동일한 위치에 실행 되는 클라이언트 컴퓨터에서 덤프/복원 작업을 수행 합니다.

- Azure Database for MySQL 서버의 경우 클라이언트 컴퓨터는 대상 데이터베이스 서버와 동일한 VNet 및 동일한 가용성 영역에 있어야 합니다.
- 원본 Amazon RDS 데이터베이스 인스턴스의 경우 클라이언트 인스턴스는 동일한 Amazon VPC (가상 사설망) 및 가용성 영역 (원본 데이터베이스 서버)에 있어야 합니다.
위의 경우 파일 전송 프로토콜 (예: FTP 또는 SFTP)을 사용 하 여 클라이언트 컴퓨터 간에 덤프 파일을 이동 하거나 Azure Blob Storage로 업로드할 수 있습니다. 총 마이그레이션 시간을 줄이기 위해 파일을 전송 하기 전에 압축할 수 있습니다.

### <a name="client-capacity"></a>클라이언트 용량

클라이언트 컴퓨터의 위치에 관계 없이 요청 된 작업을 수행 하는 데 적절 한 계산, i/o 및 네트워크 용량이 필요 합니다. 일반적인 권장 사항은 다음과 같습니다.

- 덤프 또는 복원에 압축 또는 압축 풀기와 같은 데이터의 실시간 처리가 포함 된 경우 덤프/복원 스레드 당 하나 이상의 CPU 코어가 있는 인스턴스 클래스를 선택 합니다.
- 클라이언트 인스턴스에 사용할 수 있는 네트워크 대역폭이 충분 한지 확인 합니다. 가속화 된 네트워킹 기능을 지 원하는 인스턴스 유형을 사용 하는 것이 좋습니다. 자세한 내용은 [Azure 가상 컴퓨터 네트워킹 가이드](/azure/virtual-network/create-vm-accelerated-networking-cli)에서 가속화 된 네트워킹 섹션을 참조 하세요.
- 클라이언트 컴퓨터의 저장소 계층에서 예상 되는 읽기/쓰기 용량을 제공 하는지 확인 하 고 프리미엄 SSD 저장소를 사용 하는 Azure 가상 컴퓨터를 사용 하는 것이 좋습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

- 클라이언트 컴퓨터에 [mysqlclient](https://dev.mysql.com/downloads/) 를 설치 하 여 덤프를 만들고 대상 Azure Database for MySQL 서버에서 복원 작업을 수행 합니다.
- 대규모 데이터베이스의 경우 데이터베이스를 병렬로 덤프 하 고 복원 하기 위해 [mydumper/myloader](https://centminmod.com/mydumper.html) 를 설치 합니다.

    > [!NOTE]
    > Mydumper는 Linux 배포판 에서만 실행할 수 있습니다. [Mydumper을 설치 하는 방법](https://github.com/maxbube/mydumper#how-to-install-mydumpermyloader)을 참조 하세요.

- 5.7 또는 8.0 버전을 실행 하는 Azure Database for MySQL 서버의 인스턴스를 만듭니다.

    > [!IMPORTANT]
    > 대상이 영역 중복 고가용성을 사용 하는 유연한 서버 Azure Database for MySQL 경우에는이 구성에 대 한 데이터 인 복제가 지원 되지 않습니다. 이 문제를 해결 하려면 서버를 만드는 동안 다음 단계를 사용 하 여 영역 중복 HA를 설정 합니다.
    >
    > 1. 영역 중복 HA를 사용 하도록 설정 된 서버를 만듭니다.
    > 2. HA를 사용 하지 않습니다.
    > 3. 문서에 따라 데이터에서 복제를 설정 합니다.
    > 4. 데이터에서 복제 구성을 제거 하는 방법에 대 한 사후 조치.
    > 5. HA를 사용 하도록 설정 합니다.

또한 아래에 설명 된 대로 몇 가지 매개 변수 및 기능이 올바르게 구성 되 고 설정 되었는지 확인 하는 것이 좋습니다.

- 호환성을 위해 원본 및 대상 데이터베이스 서버를 동일한 MySQL 버전에 포함 하는 것이 좋습니다.
- 각 테이블에 기본 키가 있어야 합니다. 테이블에 기본 키가 없으면 복제 프로세스가 느려질 수 있습니다.
- 원본 및 대상 데이터베이스의 문자 집합은 동일 해야 합니다.
- `wait_timeout`가져오거나 마이그레이션할 데이터 또는 워크 로드의 양에 따라 매개 변수를 적절 한 시간으로 설정 합니다.
- Azure Database for MySQL 서버가 InnoDB 저장소 엔진도 지원 하므로 모든 테이블이 InnoDB을 사용 하는지 확인 합니다.
- 보조 인덱스가 많은 테이블이 나 매우 큰 테이블의 경우 성능 오버 헤드의 효과는 복원 중에 특히 표시 되 고, 덤프 파일을 수정 하 여 `CREATE TABLE` 문이 보조 키 정의를 포함 하지 않도록 하 고, 데이터를 가져온 후에 복원 프로세스 중에 성능 저하를 방지 하기 위해 보조 인덱스를 다시 만듭니다.

마지막으로 데이터 복제를 준비 하려면 다음을 수행 합니다.

- 대상 Azure Database for MySQL 서버에서 포트 3306을 통해 Amazon RDS for MySQL 서버에 연결할 수 있는지 확인 합니다.
- 원본 Amazon RDS for MySQL 서버에서 포트 3306에 대 한 인바운드 및 아웃 바운드 트래픽을 모두 허용 하는지 확인 합니다.
- [Express](/azure/expressroute/expressroute-introduction) 경로 또는 [VPN Gateway](/azure/vpn-gateway/vpn-gateway-about-vpngateways)를 사용 하 여 원본 서버에 대 한 [사이트 간 연결](/azure/vpn-gateway/tutorial-site-to-site-portal) 을 제공 해야 합니다. 가상 네트워크를 만드는 방법에 대 한 자세한 내용은 [Virtual Network 설명서](/azure/virtual-network/)와 특히 단계별 정보를 포함 하는 빠른 시작 문서를 참조 하세요.
- 대상 Azure Database for MySQL의 서버 IP 주소를 허용 하도록 원본 데이터베이스 서버의 네트워크 보안 그룹을 구성 합니다.

> [!IMPORTANT]
> 원본 Amazon RDS for MySQL 인스턴스에 GTID_mode가 ON으로 설정 된 경우에는 Azure Database for MySQL 유연한 서버의 대상 인스턴스도 GTID_mode ON으로 설정 해야 합니다.

## <a name="configure-the-target-instance-of-azure-database-for-mysql"></a>Azure Database for MySQL의 대상 인스턴스 구성

Azure Database for MySQL 대상 인스턴스를 구성 하려면 (데이터 인 복제를 위한 대상) 다음 단계를 수행 합니다.

1. `max_allowed_packet`매개 변수 값을 최대 **1073741824** (예: 1gb)로 설정 하 여 긴 행과 관련 된 오버플로 문제를 방지 합니다.
2. `slow_query_log`마이그레이션 중에,, 및 매개 변수를 OFF로 설정 하 여 `general_log` `audit_log_enabled` `query_store_capture_mode` 쿼리 로깅과 관련 된 오버 헤드를 제거할 수 있습니다. 
3. 대상 Azure Database for MySQL 서버의 계산 크기를 최대 64 vCores로 확장 하 여 원본 서버에서 데이터베이스 덤프를 복원할 때 추가 계산 리소스를 제공 합니다.

    마이그레이션이 완료 된 후 항상 응용 프로그램 요구에 맞게 계산을 다시 확장할 수 있습니다.

4. 저장소 크기를 확장 하 여 마이그레이션하는 동안 더 많은 IOPS를 얻거나 마이그레이션에 대 한 최대 IOPS를 늘립니다.

   > [!NOTE]
   > 사용 가능한 최대 IOPS는 계산 크기로 결정 됩니다. 자세한 내용은 [Azure Database for MySQL-유연한 서버에서 계산 및 저장소 옵션](/azure/mysql/flexible-server/concepts-compute-storage#iops)의 IOPS 섹션을 참조 하세요.

## <a name="configure-the-source-amazon-rds-for-mysql-server"></a>원본 Amazon RDS for MySQL 서버 구성

Amazon RDS에서 호스트 되는 MySQL 서버를 준비 하 고 구성 하려면 (데이터 인 복제의 경우 "원본") 다음 단계를 수행 합니다.

1. 자동화 된 백업이 사용 되는지 확인 하거나 Amazon RDS for MySQL 서버에 대 한 읽기 복제본이 있는지 확인 하 여 MySQL 용 Amazon RDS 서버에서 이진 로깅이 사용 되는지 확인 합니다.

2. 원본 서버의 이진 로그 파일이 Azure Database for MySQL 대상 인스턴스에 변경 내용이 적용 될 때까지 유지 되는지 확인 합니다.

    데이터 복제를 사용 하는 경우 Azure Database for MySQL는 복제 프로세스를 관리 하지 않습니다.

3. Amazon RDS 서버 원본에서 이진 로그 보존을 확인 하 여 이진 로그가 보존 된 시간을 확인 하려면 ' mysql.rds_show_configuration ' 저장 프로시저를 호출 합니다.

    ```
    mysql> call mysql.rds_show_configuration;
    +------------------------+-------+-----------------------------------------------------------------------------------------------------------+
    | name                   | value | description                                                                                               |
    +------------------------+-------+-----------------------------------------------------------------------------------------------------------+
    | binlog retention hours | 24    | binlog retention hours specifies the duration in hours before binary logs are automatically deleted.      |
    | source delay           | 0     | source delay specifies replication delay in seconds between current instance and its master.              |
    | target delay           | 0     | target delay specifies replication delay in seconds between current instance and its future read-replica. |
    +------------------------+-------            +-----------------------------------------------------------------------------------------------------------+
    3 rows in set (0.00 sec)
    ```
4. 이진 로그 보존 기간을 구성 하려면 `rds_set_configuration` 저장 프로시저를 실행 하 여 원하는 시간 동안 이진 로그가 원본 서버에 유지 되도록 합니다. 예를 들면 다음과 같습니다.

    ```
    Mysql> Call mysql.rds_set_configuration(‘binlog retention hours’, 96);
    ```

    이 방법은 덤프를 만든 다음 복원 하 여 델타 변경 내용을 신속 하 게 파악 하는 경우에 유용 합니다.

   > [!NOTE]
   > 정의 된 보존 기간에 따라 원본 서버에 이진 로그를 저장 하는 데 충분 한 디스크 공간이 있는지 확인 합니다.

Amazon RDS for MySQL 서버 원본에서 데이터 덤프를 가져오는 방법에는 두 가지가 있습니다. 한 가지 방법은 원본 서버에서 직접 데이터 덤프를 수행 하는 것이 고, 다른 방법은 Amazon RDS for MySQL 읽기 복제본에서 덤프를 캡처하는 것입니다.

- 원본 서버에서 직접 데이터 덤프를 캡처하는 경우에는 다음 단계를 수행 합니다.

    1. 트랜잭션 측면에서 일관 된 데이터 덤프를 얻기 위해 몇 분 동안 응용 프로그램에서 쓰기를 중지 해야 합니다.

       `read_only`데이터 덤프를 캡처할 때 쓰기를 처리 하지 않도록 매개 변수를 일시적으로 **1** 값으로 설정할 수도 있습니다.

    2. 원본 서버에서 쓰기를 중지 한 후 명령을 실행 하 여 이진 로그 파일 이름 및 오프셋을 수집 합니다 `Mysql> Show master status;` .
    3. Azure Database for MySQL 서버에서 복제를 시작 하려면이 값을 저장 합니다.
    4. 데이터의 덤프를 만들려면 mysqldump을 실행 하 고 다음 명령을 실행 합니다.

        ```
        $ mysqldump -h hostname -u username -p –single-transaction –databases dbnames –order-by-primary> dumpname.sql
        ```

- 원본 서버에서 쓰기를 중지 하는 것이 옵션이 아니거나 데이터 덤프 성능이 원본 서버에서 허용 되지 않는 경우 다음 단계를 수행 하 여 복제본 서버에서 덤프를 캡처합니다.

    1. 원본 서버와 동일한 구성으로 Amazon MySQL 읽기 복제본을 만든 다음 덤프를 만듭니다.
    2. Amazon RDS for MySQL 읽기 복제본이 Amazon MySQL for MySQL 서버를 사용 하 여 처리 되도록 합니다.
    3. 복제본이 읽기 복제본에서 **0** 에 도달 하면 저장 프로시저를 호출 하 여 복제를 중지 합니다 `mysql.rds_stop_replication` .

        ```
        Mysql> call mysql.rds_stop_replication;
        ```

    4. 복제가 중지 된 상태에서 복제본에 연결한 다음 명령을 실행 하 여 `SHOW SLAVE STATUS` **Relay_Master_Log_File** 필드에서 현재 이진 로그 파일 이름을 검색 하 고 **Exec_Master_Log_Pos** 필드에서 로그 파일의 위치를 검색 합니다.
    5. Azure Database for MySQL 서버에서 복제를 시작 하려면이 값을 저장 합니다.
    6. Amazon RDS for MySQL 읽기 복제본에서 데이터 덤프를 만들려면 다음 명령을 실행 하 여 mysqldump를 실행 합니다.

        ```
        $ mysqldump -h hostname -u username -p –single-transaction –databases dbnames –order-by-primary> dumpname.sql
        ```

    > [!NOTE]
    > 또한 mydumper를 사용 하 여 Amazon RDS for MySQL 데이터베이스에서 데이터에 대 한 병렬화 된 덤프를 만들 수 있습니다. 자세한 내용은 [mydumper/myloader를 사용 하 여 Azure Database for MySQL로 large Database 마이그레이션](/azure/mysql/concepts-migrate-mydumper-myloader)을 참조 하세요.

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>원본 서버와 복제본 서버를 연결하여 입력 데이터 복제 시작

1. Mysql 기본 복원을 사용 하 여 데이터베이스를 복원 하려면 다음 명령을 실행 합니다.

    ```
    $ mysql -h <target_server> -u <targetuser> -p < dumpname.sql
    ```

   > [!NOTE]
   > 대신 myloader를 사용 하는 경우 [mydumper/myloader를 사용 하 여 Azure Database for MySQL로 큼 데이터베이스 마이그레이션](/azure/mysql/concepts-migrate-mydumper-myloader)을 참조 하세요.

2. Amazon RDS for MySQL 서버에 로그인 하 고, 복제 사용자를 설정한 후이 사용자에 게 필요한 권한을 부여 합니다.

    - SSL을 사용 하는 경우 다음 명령을 실행 합니다.

        ```
        Mysql> CREATE USER 'syncuser'@'%' IDENTIFIED BY 'userpassword';
        Mysql> GRANT REPLICATION SLAVE, REPLICATION CLIENT on *.* to 'syncuser'@'%' REQUIRE SSL; 
        Mysql> SHOW GRANTS FOR syncuser@'%';
        ```

    - SSL을 사용하지 않는 경우 다음 명령을 실행합니다.

        ```
        Mysql> CREATE USER 'syncuser'@'%' IDENTIFIED BY 'userpassword';
        Mysql> GRANT REPLICATION SLAVE, REPLICATION CLIENT on *.* to 'syncuser'@'%'; 
        Mysql> SHOW GRANTS FOR syncuser@'%';
        ```

    모든 입력 데이터 복제 기능은 저장 프로시저에 의해 수행됩니다. 모든 절차에 대 한 자세한 내용은 [데이터 복제 저장 프로시저](/azure/mysql/reference-stored-procedures#data-in-replication-stored-procedures)를 참조 하세요. MySQL shell 또는 MySQL 워크 벤치에서 이러한 저장 프로시저를 실행할 수 있습니다.

3. Amazon RDS for MySQL 원본 서버와 Azure Database for MySQL 대상 서버를 연결 하려면 대상 Azure Database for MySQL 서버에 로그인 하 고 다음 명령을 실행 하 여 Amazon RDS for MySQL 서버를 원본 서버로 설정 합니다.

    ```
    CALL mysql.az_replication_change_master('source_server','replication_user_name','replication_user_password',3306,'<master_bin_log_file>',master_bin_log_position,'<master_ssl_ca>');
    ```

4. 원본 Amazon RDS for MySQL 서버와 대상 Azure Database for MySQL 서버 간에 복제를 시작 하려면 다음 명령을 실행 합니다.

    ```
    Mysql> CALL mysql.az_replication_start;
    ```

5. 복제 상태를 확인 하려면 복제 서버에서 다음 명령을 실행 합니다.

    ```
    Mysql> show slave status\G
    ```

    `Slave_IO_Running`및 `Slave_SQL_Running` 매개 변수의 상태가 **Yes** 이면 복제가 시작 되 고 실행 중 상태입니다.

6. 매개 변수의 값을 확인 `Seconds_Behind_Master` 하 여 대상 서버가 지연 된 시간을 확인 합니다.

    값이 **0** 이면 대상이 원본 서버에서 모든 업데이트를 처리 한 것입니다. 값이 **0** 이 아닌 경우 대상 서버는 계속 업데이트를 처리 하 고 있습니다.

## <a name="ensure-a-successful-cutover"></a>성공적으로 중단되는지 확인

성공적으로 중단되는지 확인하려면 다음 작업을 수행합니다.

1. 대상 Azure Database for MySQL 서버에서 적절 한 로그인 및 데이터베이스 수준 사용 권한을 구성 합니다.
2. 원본 Amazon RDS for MySQL 서버에 대 한 쓰기를 중지 합니다.
3. 대상 Azure Database for MySQL 서버가 원본 서버를 사용 하 여 catch 되었으며 `Seconds_Behind_Master` 값이 **0** 인지 확인 `show slave status` 합니다.
4. `mysql.az_replication_stop`모든 변경 내용이 대상 Azure Database for MySQL 서버에 복제 되었으므로 저장 프로시저를 호출 하 여 복제를 중지 합니다.
5. `mysql.az_replication_remove_master`을 호출 하 여 데이터 인 복제 구성을 제거 합니다.
6. 클라이언트와 클라이언트 응용 프로그램을 대상 Azure Database for MySQL 서버로 리디렉션합니다.

이제 마이그레이션이 완료 되 고 응용 프로그램이 Azure Database for MySQL를 실행 하는 서버에 연결 됩니다.

## <a name="next-steps"></a>다음 단계

- Azure Database for MySQL로 데이터베이스 마이그레이션에 대한 자세한 내용은 [데이터베이스 마이그레이션 가이드](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)을 참조합니다.
- Azure Database for MySQL에 MySQL 앱을 마이그레이션하는 방법을 보여주는 데모가 포함된 [MySQL/PostgreSQL 앱을 Azure 관리 서비스에 간편하게 마이그레이션하기](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201) 비디오를 시청합니다.
