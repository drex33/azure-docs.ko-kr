---
title: 입력 데이터 복제 사용하여 Amazon RDS for MySQL을 Azure Database for MySQL 마이그레이션
description: 이 문서에서는 입력 데이터 복제 사용하여 Amazon RDS for MySQL을 Azure Database for MySQL 마이그레이션하는 방법을 설명합니다.
author: SudheeshGH
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 09/24/2021
ms.openlocfilehash: 01d3832d8c643a6870239534377cd282feef01f1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082477"
---
# <a name="migrate-amazon-rds-for-mysql-to-azure-database-for-mysql-using-data-in-replication"></a>입력 데이터 복제 사용하여 Amazon RDS for MySQL을 Azure Database for MySQL 마이그레이션

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 *슬레이브* 라는 용어에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.

MySQL 덤프 및 복원, MySQL Workbench 내보내기 및 가져오기와 같은 메서드를 사용하거나 Azure Database Migration Service 사용하여 MySQL 데이터베이스를 Azure Database for MySQL 마이그레이션할 수 있습니다. 입력 데이터 복제 mysqldump 또는 mydumper 및 myloader와 같은 오픈 소스 도구를 조합하여 가동 중지 시간을 최소화하면서 워크로드를 마이그레이션할 수 있습니다.

입력 데이터 복제 이진 로그 파일 위치 메서드에 따라 원본 서버에서 대상 서버로 데이터 변경 내용을 복제하는 기술입니다. 이 시나리오에서 원본으로 작동하는 MySQL 인스턴스(데이터베이스 변경이 시작되는 경우)는 업데이트 및 변경 내용을 이진 로그에 *이벤트로* 씁니다. 이진 로그의 정보는 기록되는 데이터베이스 변경 내용에 따라 다른 로깅 형식으로 저장됩니다. 복제본은 원본에서 이진 로그를 읽고 복제본의 로컬 데이터베이스에 있는 이진 로그에서 이벤트를 실행하도록 구성됩니다.

원본 MySQL 서버에서 대상 MySQL 서버로 데이터를 동기화하도록 [입력 데이터 복제](../mysql/flexible-server/concepts-data-in-replication.md) 설정한 경우 주(또는 원본 데이터베이스)에서 복제본(또는 대상 데이터베이스)으로 애플리케이션을 선택적으로 중단할 수 있습니다.

이 자습서에서는 MySQL용 RDS(Amazon Relational Database Service)를 실행하는 원본 서버와 Azure Database for MySQL 실행하는 대상 서버 간에 입력 데이터 복제 설정하는 방법을 알아봅니다.

## <a name="performance-considerations"></a>성능 고려 사항

이 자습서를 시작하기 전에 작업을 수행하는 데 사용할 클라이언트 컴퓨터의 위치 및 용량이 성능에 미치는 영향을 고려합니다.

### <a name="client-location"></a>클라이언트 위치

데이터베이스 서버와 동일한 위치에서 시작된 클라이언트 컴퓨터에서 덤프 또는 복원 작업을 수행합니다.

- Azure Database for MySQL 서버의 경우 클라이언트 컴퓨터는 대상 데이터베이스 서버와 동일한 가상 네트워크 및 동일한 가용성 영역에 있어야 합니다.
- 원본 Amazon RDS 데이터베이스 인스턴스의 경우 클라이언트 인스턴스는 원본 데이터베이스 서버와 동일한 Amazon Virtual Private Cloud 및 가용성 영역에 있어야 합니다.
위의 경우 FTP 또는 SFTP와 같은 파일 전송 프로토콜을 사용하여 클라이언트 컴퓨터 간에 덤프 파일을 이동하거나 Azure Blob Storage 업로드할 수 있습니다. 총 마이그레이션 시간을 줄이려면 파일을 전송하기 전에 압축합니다.

### <a name="client-capacity"></a>클라이언트 용량

클라이언트 컴퓨터의 위치와 관계없이 요청된 작업을 수행하려면 적절한 컴퓨팅, I/O 및 네트워크 용량이 필요합니다. 일반적인 권장 사항은 다음과 같습니다.

- 덤프 또는 복원에 실시간 데이터 처리(예: 압축 또는 압축 풀기)가 포함된 경우 덤프 또는 복원 스레드당 하나 이상의 CPU 코어가 있는 인스턴스 클래스를 선택합니다.
- 클라이언트 인스턴스에 사용할 수 있는 네트워크 대역폭이 충분한지 확인합니다. 가속 네트워킹 기능을 지원하는 인스턴스 유형을 사용합니다. 자세한 내용은 Azure Virtual Machine 네트워킹 가이드의 "가속 [네트워킹" 섹션을 참조하세요.](../virtual-network/create-vm-accelerated-networking-cli.md)
- 클라이언트 컴퓨터의 스토리지 계층이 예상 읽기/쓰기 용량을 제공하는지 확인합니다. Premium SSD 스토리지에서 Azure 가상 머신을 사용하는 것이 좋습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

- 클라이언트 컴퓨터에 [mysqlclient를](https://dev.mysql.com/downloads/) 설치하여 덤프를 만들고 대상 Azure Database for MySQL 서버에서 복원 작업을 수행합니다.
- 더 큰 데이터베이스의 경우 데이터베이스의 병렬 덤프 및 복원을 위해 [mydumper 및 myloader를](https://centminmod.com/mydumper.html) 설치합니다.

    > [!NOTE]
    > Mydumper는 Linux 배포판에서만 실행할 수 있습니다. 자세한 내용은 [mydumper를 설치하는 방법을 참조하세요.](https://github.com/maxbube/mydumper#how-to-install-mydumpermyloader)

- 버전 5.7 또는 8.0을 실행하는 Azure Database for MySQL 서버의 인스턴스를 만듭니다.

    > [!IMPORTANT]
    > 대상이 영역 중복 HA(고가용성)가 있는 유연한 서버를 Azure Database for MySQL 경우 이 구성에 대해 입력 데이터 복제 지원되지 않습니다. 해결 방법을 위해 서버를 만드는 동안 영역 중복 HA를 설정합니다.
    >
    > 1. 영역 중복 HA를 사용하도록 설정된 서버를 만듭니다.
    > 1. HA를 사용하지 않도록 설정합니다.
    > 1. 문서에 따라 입력 데이터 복제 설정합니다.
    > 1. 중단 후 입력 데이터 복제 구성을 제거합니다.
    > 1. HA를 사용하도록 설정합니다.

설명된 대로 여러 매개 변수 및 기능이 올바르게 구성되고 설정되었는지 확인합니다.

- 호환성을 위해 원본 및 대상 데이터베이스 서버가 동일한 MySQL 버전에 있어야 합니다.
- 각 테이블에 기본 키가 있습니다. 테이블에 기본 키가 없으면 복제 프로세스가 느려질 수 있습니다.
- 원본 및 대상 데이터베이스의 문자 집합이 동일한지 확인합니다.
- 매개 `wait_timeout` 변수를 적절한 시간으로 설정합니다. 시간은 가져오거나 마이그레이션하려는 데이터 또는 워크로드의 양에 따라 달라집니다.
- 모든 테이블에서 InnoDB를 사용하는지 확인합니다. Azure Database for MySQL 서버는 InnoDB 스토리지 엔진만 지원합니다.
- 보조 인덱스가 많은 테이블 또는 큰 테이블의 경우 복원하는 동안 성능 오버헤드의 영향이 표시됩니다. 문에 보조 키 정의가 포함되지 않도록 덤프 파일을 `CREATE TABLE` 수정합니다. 데이터를 가져온 후 복원 프로세스 중에 성능 저하를 방지하기 위해 보조 인덱스를 다시 만듭니다.

마지막으로 입력 데이터 복제 준비하려면 다음을 수행합니다.

- 대상 Azure Database for MySQL 서버가 포트 3306을 통해 원본 Amazon RDS for MySQL 서버에 연결할 수 있는지 확인합니다.
- 원본 Amazon RDS for MySQL 서버가 포트 3306에서 인바운드 및 아웃바운드 트래픽을 모두 허용하는지 확인합니다.
- [Azure ExpressRoute](../expressroute/expressroute-introduction.md) 또는 [Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)VPN Gateway 를 사용하여 원본 서버에 사이트 대 [사이트 연결을](../vpn-gateway/tutorial-site-to-site-portal.md) 제공해야 합니다. 가상 네트워크를 만드는 자세한 내용은 [Azure Virtual Network 설명서를 참조하세요.](../virtual-network/index.yml) 또한 단계별 세부 정보가 있는 빠른 시작 문서를 참조하세요.
- 대상 Azure Database for MySQL 서버 IP 주소를 허용하도록 원본 데이터베이스 서버의 네트워크 보안 그룹을 구성합니다.

> [!IMPORTANT]
> 원본 Amazon RDS for MySQL 인스턴스가 GTID_mode ON으로 설정된 경우 Azure Database for MySQL 유연한 서버의 대상 인스턴스도 GTID_mode ON으로 설정해야 합니다.

## <a name="configure-the-target-instance-of-azure-database-for-mysql"></a>Azure Database for MySQL 대상 인스턴스 구성

입력 데이터 복제 대상인 Azure Database for MySQL 대상 인스턴스를 구성하려면 다음을 수행합니다.

1. 매개 `max_allowed_packet` 변수 값을 1GB인 **1073741824** 최대값으로 설정합니다. 이 값은 긴 행과 관련된 오버플로 문제를 방지합니다.
1. 마이그레이션 `slow_query_log` 중에 , `general_log` , 및 매개 `audit_log_enabled` `query_store_capture_mode` 변수를 **OFF로** 설정하면 쿼리 로깅과 관련된 오버헤드를 제거할 수 있습니다.
1. 대상 Azure Database for MySQL 서버의 컴퓨팅 크기를 최대 64개의 vCore로 확장합니다. 이 크기는 원본 서버에서 데이터베이스 덤프를 복원할 때 더 많은 컴퓨팅 리소스를 제공합니다.

    마이그레이션이 완료된 후 애플리케이션 요구 사항을 충족하도록 항상 컴퓨팅을 축소할 수 있습니다.

1. 마이그레이션하는 동안 더 많은 IOPS를 얻거나 마이그레이션에 대한 최대 IOPS를 늘리려면 스토리지 크기를 확장합니다.

   > [!NOTE]
   > 사용 가능한 최대 IOPS는 컴퓨팅 크기에 따라 결정됩니다. 자세한 내용은 Azure Database for MySQL 컴퓨팅 및 스토리지 옵션 - 유연한 서버 의 IOPS [섹션을 참조하세요.](../mysql/flexible-server/concepts-compute-storage.md#iops)

## <a name="configure-the-source-amazon-rds-for-mysql-server"></a>원본 Amazon RDS for MySQL 서버 구성

입력 데이터 복제 *원본인* Amazon RDS에서 호스트되는 MySQL 서버를 준비하고 구성하려면 다음을 수행합니다.

1. 원본 Amazon RDS for MySQL 서버에서 이진 로깅이 사용하도록 설정되어 있는지 확인합니다. 자동화된 백업이 사용하도록 설정되어 있는지 확인하거나 원본 Amazon RDS for MySQL 서버에 대한 읽기 복제본이 있는지 확인합니다.

1. 변경 내용이 Azure Database for MySQL 대상 인스턴스에 적용될 때까지 원본 서버의 이진 로그 파일이 유지되는지 확인합니다.

    입력 데이터 복제 Azure Database for MySQL 복제 프로세스를 관리하지 않습니다.

1. 원본 Amazon RDS 서버에서 이진 로그 보존을 확인하여 이진 로그가 보존되는 시간을 확인하려면 `mysql.rds_show_configuration` 저장 프로시저를 호출합니다.

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
1. 이진 로그 보존 기간을 구성하려면 `rds_set_configuration` 저장 프로시저를 실행하여 이진 로그가 원하는 시간 동안 원본 서버에 유지되도록 합니다. 예:

    ```
    Mysql> Call mysql.rds_set_configuration(‘binlog retention hours', 96);
    ```

    덤프를 만든 다음 복원하는 경우 이전 명령을 사용하면 델타 변경 내용을 빠르게 파악할 수 있습니다.

   > [!NOTE]
   > 정의된 보존 기간에 따라 원본 서버에 이진 로그를 저장할 충분한 디스크 공간이 있는지 확인합니다.

원본 Amazon RDS for MySQL 서버에서 데이터 덤프를 캡처하는 방법에는 두 가지가 있습니다. 한 가지 방법은 원본 서버에서 직접 데이터 덤프를 캡처하는 것입니다. 다른 방법은 MySQL용 Amazon RDS 읽기 복제본에서 덤프를 캡처하는 것입니다.

- 원본 서버에서 직접 데이터 덤프를 캡처하려면 다음을 수행합니다.

    1. 트랜잭션적으로 일관된 데이터 덤프를 얻으려면 몇 분 동안 애플리케이션에서 쓰기를 중지해야 합니다.

       `read_only`데이터 덤프를 캡처할 때 쓰기가 처리되지 않도록 매개 변수를 일시적으로 **값 1로** 설정할 수도 있습니다.

    1. 원본 서버에서 쓰기를 중지한 후 명령을 실행하여 이진 로그 파일 이름 및 오프셋을 `Mysql> Show master status;` 수집합니다.
    1. 이러한 값을 저장하여 Azure Database for MySQL 서버에서 복제를 시작합니다.
    1. 데이터 덤프를 만들려면 `mysqldump` 다음 명령을 실행하여 를 실행합니다.

        ```
        $ mysqldump -h hostname -u username -p –single-transaction –databases dbnames –order-by-primary> dumpname.sql
        ```

- 원본 서버에서 쓰기를 중지하는 것이 옵션이 아니거나 원본 서버에서 데이터 덤프 성능이 허용되지 않는 경우 복제본 서버에서 덤프를 캡처합니다.

    1. 원본 서버와 동일한 구성으로 Amazon MySQL 읽기 복제본을 만듭니다. 그런 다음, 덤프를 만듭니다.
    1. Amazon RDS for MySQL 읽기 복제본이 원본 Amazon RDS for MySQL 서버를 따라가도록 합니다.
    1. 읽기 복제본에서 복제본 지연 시간이 **0에** 도달하면 저장 프로시저를 호출하여 복제를 `mysql.rds_stop_replication` 중지합니다.

        ```
        Mysql> call mysql.rds_stop_replication;
        ```

    1. 복제가 중지 된 상태에서 복제본에 연결 합니다. 그런 다음 `SHOW SLAVE STATUS` 명령을 실행 하 여 **Relay_Master_Log_File** 필드에서 현재 이진 로그 파일 이름을 검색 하 고 **Exec_Master_Log_Pos** 필드에서 로그 파일의 위치를 검색 합니다.
    1. Azure Database for MySQL 서버에서 복제를 시작 하려면이 값을 저장 합니다.
    1. Amazon RDS for MySQL 읽기 복제본에서 데이터 덤프를 만들려면 `mysqldump` 다음 명령을 실행 하 여를 실행 합니다.

        ```
        $ mysqldump -h hostname -u username -p –single-transaction –databases dbnames –order-by-primary> dumpname.sql
        ```

    > [!NOTE]
    > 또한 mydumper를 사용 하 여 Amazon RDS for MySQL 데이터베이스에서 데이터의 병렬화 된 덤프를 캡처할 수 있습니다. 자세한 내용은 [mydumper/myloader를 사용 하 여 Azure Database for MySQL에 대 한 대량 데이터베이스 마이그레이션](../mysql/concepts-migrate-mydumper-myloader.md)을 참조 하세요.

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>원본 서버와 복제본 서버를 연결하여 입력 데이터 복제 시작

1. Mysql 기본 복원을 사용 하 여 데이터베이스를 복원 하려면 다음 명령을 실행 합니다.

    ```
    $ mysql -h <target_server> -u <targetuser> -p < dumpname.sql
    ```

   > [!NOTE]
   > 대신 myloader를 사용 하는 경우 [mydumper/myloader를 사용 하 여 Azure Database for MySQL로 큼 데이터베이스 마이그레이션](../mysql/concepts-migrate-mydumper-myloader.md)을 참조 하세요.

1. Amazon RDS for MySQL 서버에 로그인 하 고 복제 사용자를 설정 합니다. 그런 다음이 사용자에 게 필요한 권한을 부여 합니다.

    - SSL을 사용 하는 경우 다음 명령을 실행 합니다.

        ```
        Mysql> CREATE USER 'syncuser'@'%' IDENTIFIED BY 'userpassword';
        Mysql> GRANT REPLICATION SLAVE, REPLICATION CLIENT on *.* to 'syncuser'@'%' REQUIRE SSL; 
        Mysql> SHOW GRANTS FOR syncuser@'%';
        ```

    - SSL을 사용 하지 않는 경우 다음 명령을 실행 합니다.

        ```
        Mysql> CREATE USER 'syncuser'@'%' IDENTIFIED BY 'userpassword';
        Mysql> GRANT REPLICATION SLAVE, REPLICATION CLIENT on *.* to 'syncuser'@'%'; 
        Mysql> SHOW GRANTS FOR syncuser@'%';
        ```

    모든 데이터 내부 복제 기능은 저장 프로시저에 의해 수행됩니다. 모든 절차에 대 한 자세한 내용은 [입력 데이터 복제 저장 프로시저](../mysql/reference-stored-procedures.md#data-in-replication-stored-procedures)를 참조 하세요. MySQL shell 또는 MySQL 워크 벤치에서 이러한 저장 프로시저를 실행할 수 있습니다.

1. Amazon RDS for MySQL 원본 서버와 Azure Database for MySQL 대상 서버를 연결 하려면 대상 Azure Database for MySQL 서버에 로그인 합니다. 다음 명령을 실행 하 여 Amazon RDS for MySQL 서버를 원본 서버로 설정 합니다.

    ```
    CALL mysql.az_replication_change_master('source_server','replication_user_name','replication_user_password',3306,'<master_bin_log_file>',master_bin_log_position,'<master_ssl_ca>');
    ```

1. 원본 Amazon RDS for MySQL 서버와 대상 Azure Database for MySQL 서버 간에 복제를 시작 하려면 다음 명령을 실행 합니다.

    ```
    Mysql> CALL mysql.az_replication_start;
    ```

1. 복제 상태를 확인 하려면 복제 서버에서 다음 명령을 실행 합니다.

    ```
    Mysql> show slave status\G
    ```

    `Slave_IO_Running`및 `Slave_SQL_Running` 매개 변수의 상태가 **Yes** 이면 복제가 시작 되 고 실행 중 상태입니다.

1. 매개 변수의 값을 확인 `Seconds_Behind_Master` 하 여 대상 서버가 지연 된 시간을 확인 합니다.

    값이 **0** 이면 대상이 원본 서버에서 모든 업데이트를 처리 한 것입니다. 값이 **0** 이 아닌 경우 대상 서버는 계속 업데이트를 처리 하 고 있습니다.

## <a name="ensure-a-successful-cutover"></a>성공적으로 중단되는지 확인

성공적으로 완료 되었는지 확인 하려면:

1. 대상 Azure Database for MySQL 서버에서 적절 한 로그인 및 데이터베이스 수준 사용 권한을 구성 합니다.
1. 원본 Amazon RDS for MySQL 서버에 대 한 쓰기를 중지 합니다.
1. 대상 Azure Database for MySQL 서버가 원본 서버를 사용 하 여 catch 되었으며 `Seconds_Behind_Master` 값이 **0** 인지 확인 `show slave status` 합니다.
1. `mysql.az_replication_stop`모든 변경 내용이 대상 Azure Database for MySQL 서버에 복제 되었으므로 저장 프로시저를 호출 하 여 복제를 중지 합니다.
1. `mysql.az_replication_remove_master`입력 데이터 복제 구성을 제거 하려면를 호출 합니다.
1. 클라이언트와 클라이언트 응용 프로그램을 대상 Azure Database for MySQL 서버로 리디렉션합니다.

이 시점에서 마이그레이션이 완료 됩니다. 응용 프로그램은 Azure Database for MySQL를 실행 하는 서버에 연결 됩니다.

## <a name="next-steps"></a>다음 단계

- Azure Database for MySQL로 데이터베이스 마이그레이션에 대한 자세한 내용은 [데이터베이스 마이그레이션 가이드](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)을 참조합니다.
- 비디오를 보면 [Azure 관리 서비스로 MySQL/PostgreSQL 앱을 쉽게 마이그레이션할 수](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201)있습니다. MySQL 앱을 Azure Database for MySQL로 마이그레이션하는 방법을 보여 주는 데모를 포함 합니다.