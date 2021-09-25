---
title: '자습서: 최소한의 가동 중지 시간으로 Azure Database for MySQL – 단일 서버를 Azure Database for MySQL – 유연한 서버로 마이그레이션'
description: 이 문서에서는 최소한의 가동 중지 시간으로 Azure Database for MySQL – 단일 서버를 Azure Database for MySQL – 유연한 서버로 마이그레이션하는 방법을 설명합니다.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: how-to
ms.date: 06/18/2021
ms.openlocfilehash: 0197d3c4a76158663d721604cdd8e033b3d78153
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589647"
---
# <a name="tutorial-migrate-azure-database-for-mysql--single-server-to-azure-database-for-mysql--flexible-server-with-minimal-downtime"></a>자습서: 최소한의 가동 중지 시간으로 Azure Database for MySQL – 단일 서버를 Azure Database for MySQL – 유연한 서버로 마이그레이션

mydumper/myloader 및 입력 데이터 복제와 같은 오픈 소스 도구의 조합을 사용하여 애플리케이션 가동 중지 시간을 최소화하면서 Azure Database for MySQL – 단일 서버를 Azure Database for MySQL – 유연한 서버로 마이그레이션할 수 있습니다.

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 ‘슬레이브’에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.

입력 데이터 복제는 이진 로그 파일 위치 메서드에 따라 데이터 변경 내용을 원본 서버에서 대상 서버로 복제하는 기술입니다. 이 시나리오에서 원본(데이터베이스 변경이 시작된 곳)으로 작동하는 MySQL 인스턴스는 업데이트 및 변경 내용을 "이벤트"로 이진 로그에 기록합니다. 이진 로그의 정보는 기록되는 데이터베이스 변경 내용에 따라 다른 로깅 형식으로 저장됩니다. 복제본은 원본에서 이진 로그를 읽고 복제본의 로컬 데이터베이스에서 이진 로그의 이벤트를 실행하도록 구성됩니다.

데이터를 Azure Database for MySQL의 한 인스턴스에서 다른 인스턴스로 동기화하도록 입력 데이터 복제를 설정하는 경우 애플리케이션을 주(또는 원본 데이터베이스)에서 복제본(또는 대상 데이터베이스)으로 선택적으로 중단할 수 있습니다.

이 자습서에서는 mydumper/myloader 및 입력 데이터 복제를 사용하여 샘플 데이터베이스([classicmodels](https://www.mysqltutorial.org/mysql-sample-database.aspx))를 Azure Database for MySQL - 단일 서버 인스턴스에서 Azure Database for MySQL - 유연한 서버 인스턴스로 마이그레이션한 다음, 데이터를 동기화합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

* 다양한 시나리오의 입력 데이터 복제에 대한 네트워크 설정을 구성합니다.
* 주 및 복제본 간에 입력 데이터 복제를 구성합니다.
* 복제를 테스트합니다.
* 마이그레이션을 완료하기 위해 중단합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 필요합니다.

* 버전 5.7 또는 8.0을 실행하는 Azure Database for MySQL 단일 서버의 인스턴스
    > [!Note]
    > Azure Database for MySQL 단일 서버 버전 5.6을 실행하는 경우 인스턴스를 5.7로 업그레이드한 다음, 입력 데이터 복제를 구성합니다. 자세한 내용은 [Azure Database for MySQL - 단일 서버의 주 버전 업그레이드](how-to-major-version-upgrade.md)를 참조하세요.
* Azure Database for MySQL 유연한 서버의 인스턴스. 자세한 내용은 [Azure Database for MySQL 유연한 서버에서 인스턴스 만들기](./flexible-server/quickstart-create-server-portal.md) 문서를 참조하세요.
    > [!Note]
    > 영역 중복 고가용성 서버에 대한 입력 데이터 복제 구성은 지원되지 않습니다. 대상 서버에 대해 영역 중복 HA를 사용하려면 다음 단계를 수행합니다.
    >
    > 1. 영역 중복 HA가 사용하도록 설정된 서버 만들기
    > 2. HA를 사용하지 않도록 설정
    > 3. 문서에 따라 입력 데이터 복제 설정
    > 4. 중단 후 입력 데이터 복제 제거
    > 5. HA를 사용하도록 설정
    >
    > ***[GTID_Mode](./concepts-read-replicas.md#global-transaction-identifier-gtid)** 에서 원본 서버 및 대상 서버에 대한 설정이 동일한지 확인합니다.*

* MySQL Workbench를 사용하여 데이터베이스를 연결하고 만듭니다. 자세한 내용은 [MySQL Workbench를 사용하여 데이터 연결 및 쿼리](./flexible-server/connect-workbench.md) 문서를 참조하세요.
* 원본 및 대상 데이터베이스를 호스트하는 동일한 지역(또는 프라이빗 액세스 권한이 있는 동일한 VNet)에서 Linux를 실행하는 Azure VM이 있는지 확인합니다.
* mysql 클라이언트 또는 MySQL Workbench(클라이언트 도구)를 Azure VM에 설치합니다. 주 서버와 복제본 서버 모두에 연결할 수 있는지 확인합니다. 이 문서의 목적을 위해 mysql 클라이언트가 설치됩니다.
* mydumper/myloader를 Azure VM에 설치합니다. 자세한 내용은 [mydumper/myloader](concepts-migrate-mydumper-myloader.md) 문서를 참조하세요.
* 원본 서버에서 [classicmodels](https://www.mysqltutorial.org/wp-content/uploads/2018/03/mysqlsampledatabase.zip) 데이터베이스에 대한 샘플 데이터베이스 스크립트를 다운로드하고 실행합니다.

## <a name="configure-networking-requirements"></a>네트워킹 요구 사항 구성

입력 데이터 복제를 구성하려면 대상에서 3306 포트를 통해 원본에 연결할 수 있는지 확인해야 합니다. 원본에 설정된 엔드포인트 유형에 따라 적절한 다음 단계를 수행합니다.

* 퍼블릭 엔드포인트가 원본에서 사용하도록 설정된 경우 방화벽 규칙에서 "Azure 서비스에 대한 액세스 허용"을 사용하도록 설정하여 대상에서 원본에 연결할 수 있는지 확인합니다. 자세한 내용은 [방화벽 규칙 - Azure Database for MySQL](./concepts-firewall-rules.md#connecting-from-azure)을 참조하세요. 
* 원본에서 프라이빗 엔드포인트 및 "[퍼블릭 액세스 거부](concepts-data-access-security-private-link.md#deny-public-access-for-azure-database-for-mysql)"가 사용하도록 설정된 경우 대상을 호스트하는 동일한 VNet에 프라이빗 링크를 설치합니다. 자세한 내용은 [Private Link - Azure Database for MySQL](concepts-data-access-security-private-link.md)을 참조하세요.

## <a name="configure-data-in-replication"></a>입력 데이터 복제 구성

입력 데이터 복제를 구성하려면 다음 단계를 수행합니다.

1. mysql 클라이언트 도구를 설치한 Azure VM에 로그인합니다.

2. mysql 클라이언트 도구를 사용하여 원본 및 대상에 연결합니다.

3. mysql 클라이언트 도구에서 다음 명령을 실행하여 원본에서 log_bin이 사용하도록 설정되어 있는지 확인합니다.

    ```sql
    SHOW VARIABLES LIKE 'log_bin';
    ```

    > [!Note]
    > 최대 16TB를 지원하는 대용량 스토리지가 있는 Azure Database for MySQL 단일 서버를 사용하는 경우 이는 기본적으로 사용하도록 설정됩니다.

    > [!Tip]
    > 최대 4TB를 지원하는 Azure Database for MySQL 단일 서버를 사용하는 경우 이는 기본적으로 사용하도록 설정되지 않습니다. 그러나 원본 서버에 대한 [읽기 복제본](howto-read-replicas-portal.md)을 승격한 다음, 읽기 복제본을 삭제하면 매개변수가 ON으로 설정됩니다.

4. 원본 서버에 대한 SSL 적용에 따라 적절한 명령을 실행하여 복제 권한이 있는 사용자를 원본 서버에 만듭니다.

    SSL을 사용하는 경우 다음 명령을 실행합니다.

    ```sql
    CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
    GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
    ```

    SSL을 사용하지 않는 경우 다음 명령을 실행합니다.

    ```sql
    CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
    GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
    ```

5. mydumper를 사용하여 데이터베이스를 백업하려면 mydumper\myloader를 설치한 Azure VM에서 다음 명령을 실행합니다.

    ```bash
    $ mydumper --host=<primary_server>.mysql.database.azure.com --user=<username>@<primary_server> --password=<Password> --outputdir=./backup --rows=100 -G -E -R -z --trx-consistency-only --compress --build-empty-files --threads=16 --compress-protocol --ssl  --regex '^(classicmodels\.)' -L mydumper-logs.txt
    ```

    > [!Tip]
    > **--trx-consistency-only** 옵션은 백업을 수행하는 동안 트랜잭션 일관성을 위해 필요합니다.
    >
    > * mysqldump의 --single-transaction에 해당하는 mydumper입니다.
    > * 모든 테이블이 InnoDB인 경우에 유용합니다.
    > * "주" 스레드는 "덤프" 스레드에서 트랜잭션을 시작할 때까지 전체 잠금을 유지하기만 하면 됩니다.
    > * 가장 짧은 전체 잠금 기간을 제공합니다.

    "주" 스레드는 "덤프" 스레드에서 트랜잭션을 시작할 때까지 전체 잠금을 유지하기만 하면 됩니다.

    이 명령의 변수는 다음과 같습니다.

    * **--host:** 주 서버의 이름
    * **--user:** 사용자의 이름(주 서버에서 Azure Database for MySQL - 단일 서버를 실행하므로 username@servername 형식). 서버 관리자 또는 SELECT 및 RELOAD 권한이 있는 사용자를 사용할 수 있습니다.
    * **--Password:** 위 사용자의 암호

    mydumper를 사용하는 방법에 대한 자세한 내용은 [mydumper/myloader](concepts-migrate-mydumper-myloader.md)를 참조하세요.

6. 다음 명령을 실행하여 메타데이터 파일을 읽어 이진 로그 파일 이름과 오프셋을 확인합니다.

    ```bash
    $ cat ./backup/metadata 
    ```

    이 명령에서 **./backup** 은 이전 단계의 명령에 사용된 출력 디렉터리를 나타냅니다.

    결과는 다음 이미지와 같이 표시됩니다.

    :::image type="content" source="./media/howto-migrate-single-flexible-minimum-downtime/metadata.png" alt-text="Azure Database Migration Service와 지속적인 동기화":::

    이후 단계에서 사용할 수 있도록 이진 파일 이름을 적어 두세요.

7. 다음 명령을 실행하여 myloader를 통해 데이터베이스를 복원합니다.

    ```bash
    $ myloader --host=<servername>.mysql.database.azure.com --user=<username> --password=<Password> --directory=./backup --queries-per-transaction=100 --threads=16 --compress-protocol --ssl --verbose=3 -e 2>myloader-logs.txt
    ```

    이 명령의 변수는 다음과 같습니다.

    * **--host:** 복제본 서버의 이름
    * **--user:** 사용자의 이름. 서버 관리자 또는 스키마와 데이터를 데이터베이스에 복원할 수 있는 읽기\쓰기 권한이 있는 사용자를 사용할 수 있습니다.
    * **--Password:** 위 사용자의 암호

8. 주 서버의 SSL 적용에 따라 mysql 클라이언트 도구를 사용하여 복제본 서버에 연결하고 다음 단계를 수행합니다.

    * SSL 적용이 사용하도록 설정된 경우 다음을 수행합니다.

        i. [여기](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)서 Azure Database for MySQL 서버를 사용하여 SSL을 통해 통신하는 데 필요한 인증서를 다운로드합니다.

        ii. 메모장에서 파일을 열고, 해당 콘텐츠를 "PLACE YOUR PUBLIC KEY CERTIFICATE'S CONTEXT HERE(여기에 공개 키 인증서의 컨텍스트 배치)" 섹션에 붙여넣습니다.

        ```sql
        SET @cert = '-----BEGIN CERTIFICATE-----
        PLACE YOUR PUBLIC KEY CERTIFICATE'S CONTEXT HERE 
        -----END CERTIFICATE-----'
        ```

        iii. 입력 데이터 복제를 구성하려면 다음 명령을 실행합니다.

        ```sql
        CALL mysql.az_replication_change_master('<Primary_server>.mysql.database.azure.com', '=<username>@<primary_server>', '<Password>, 3306, '<File_Name>', <Position>, @cert);
        ```

        > [!Note]
        > 6단계에서 얻은 정보에서 위치와 파일 이름을 확인합니다.

    * SSL 적용이 사용하도록 설정되지 않은 경우 다음 명령을 실행합니다.

        ```sql
        CALL mysql.az_replication_change_master('<Primary_server>.mysql.database.azure.com', '=<username>@<primary_server>', '<Password>, 3306, '<File_Name>', <Position>, ‘’);
        ```

9. 복제본 서버에서 복제를 시작하려면 아래 저장 프로시저를 호출합니다.

    ```sql
    call  mysql.az_replication_start;
    ```

10. 복제 상태를 확인하려면 복제본 서버에서 다음 명령을 실행합니다.

     ```sql
     show slave status \G; 
     ```

    > [!Note]
    > MySQL Workbench를 사용하는 경우 \G 한정자가 필요하지 않습니다.

    *Slave_IO_Running* 및 *Slave_SQL_Running* 의 상태가 Yes이고 *Seconds_Behind_Master* 값이 0이면 복제가 제대로 작동하는 것입니다. Seconds_Behind_Master는 복제본의 지연 시간을 나타냅니다. 값이 0이 아니면 복제본에서 업데이트를 처리하고 있는 것입니다.

## <a name="testing-the-replication-optional"></a>복제 테스트(선택 사항)

입력 데이터 복제가 제대로 작동하는지 확인하기 위해 주 테이블의 변경 내용이 복제본에 복제되었는지 확인할 수 있습니다.

1. 테스트에 사용할 테이블(예: Customers 테이블)을 식별한 다음, 주 서버와 복제본 서버 각각에서 다음 명령을 실행하여 테이블에 포함된 항목 수가 동일한지 확인합니다.

    ```
    select count(*) from customers;
    ```

2. 나중에 비교할 수 있도록 항목 수를 적어 둡니다.

    복제를 테스트하려면 일부 데이터를 주 서버의 고객 테이블에 추가한 다음, 새 데이터가 복제되었는지 확인합니다. 이 경우 두 개의 행을 주 서버의 테이블에 추가한 다음, 복제본 서버에 복제되었는지 확인합니다.

3. 주 서버의 Customers 테이블에서 다음 명령을 실행하여 행을 삽입합니다.

    ```sql
    insert  into `customers`(`customerNumber`,`customerName`,`contactLastName`,`contactFirstName`,`phone`,`addressLine1`,`addressLine2`,`city`,`state`,`postalCode`,`country`,`salesRepEmployeeNumber`,`creditLimit`) values 
    (<ID>,'name1','name2','name3 ','11.22.5555','54, Add',NULL,'Add1',NULL,'44000','country',1370,'21000.00');
    ```

4. 복제 상태를 확인하려면 *show slave status \G* 를 호출하여 복제가 예상대로 작동하는지 확인합니다.

5. 개수가 동일한지 확인하려면 복제본 서버에서 다음 명령을 실행합니다.

    ```sql
    select count(*) from customers;
    ```

## <a name="ensure-a-successful-cutover"></a>성공적으로 중단되는지 확인

성공적으로 중단되는지 확인하려면 다음 작업을 수행합니다.

1. 대상 서버에 연결하도록 적절한 서버 수준 방화벽 및 가상 네트워크 규칙을 구성합니다. 포털에서 [원본](howto-manage-firewall-using-portal.md) 및 [대상](./flexible-server/how-to-manage-firewall-portal.md#create-a-firewall-rule-after-server-is-created)에 대한 방화벽 규칙을 비교할 수 있습니다.
2. 대상 서버에서 적절한 로그인 및 데이터베이스 수준 권한을 구성합니다. 원본 서버 및 대상 서버에서 *SELECT * FROM mysql.user;* 를 실행하여 비교할 수 있습니다.
3. Azure Database for MySQL 단일 서버에 대한 모든 들어오는 연결이 중지되는지 확인합니다.
    > [!Tip]
    > Azure Database for MySQL 단일 서버를 읽기 전용으로 설정할 수 있습니다.
4. *show slave status \G* 를 실행하고 *Seconds_Behind_Master* 매개 변수의 값이 0인지 확인하여 복제본에서 주를 캐치업했는지 확인합니다.
5. 클라이언트 및 클라이언트 애플리케이션을 Azure Database for MySQL 유연한 서버의 대상 인스턴스로 리디렉션합니다.
6. 복제본 서버에서 복제를 중지하는 mysql.az_replication_stop 저장 프로시저를 실행하여 최종 중단을 수행합니다.
7. *mysql.az_replication_remove_master를 호출* 하여 입력 데이터 복제 구성을 제거합니다.

이 시점에서 애플리케이션은 새 Azure Database for MySQL 유연한 서버에 연결되고, 원본의 변경 내용이 더 이상 대상에 복제되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Database for MySQL 유연한 서버에 데이터 복제](flexible-server/concepts-data-in-replication.md) 및 [Azure Database for MySQL 유연한 서버 입력 데이터 복제 구성](./flexible-server/how-to-data-in-replication.md) 입력 데이터 복제에 대해 자세히 알아봅니다.
* [일반적인 Azure Database for MySQL 오류 문제 해결](howto-troubleshoot-common-errors.md)에 대해 자세히 알아봅니다.
* [Azure Database Migration Service를 사용하여 MySQL에서 Azure Database for MySQL로 오프라인으로 마이그레이션](../dms/tutorial-mysql-azure-mysql-offline-portal.md)하는 방법에 대해 자세히 알아봅니다.
