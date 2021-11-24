---
title: 빠른 시작 - Azure Database for MySQL과 통합
description: Azure Database for MySQL 인스턴스를 프로비전하고 준비한 다음, 한 번의 명령으로 영구 데이터베이스로 사용하도록 Azure Spring Cloud에서 Pet Clinic을 구성하는 방법을 설명합니다.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/15/2021
ms.custom: devx-track-java, devx-track-azurecli, mode-other
ms.openlocfilehash: cc3f5ee61a65c608f3ef11de69339dfde5d54d85
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133077389"
---
# <a name="quickstart-integrate-azure-spring-cloud-with-azure-database-for-mysql"></a>빠른 시작: Azure Spring Cloud와 Azure Database for MySQL 통합

기본 구성 [빠른 시작: Azure Spring Cloud에 앱 빌드 및 배포](quickstart-deploy-apps.md)에서 배포된 Pet Clinic은 시작 시 데이터로 채워지는 메모리 내 데이터베이스(HSQLDB)를 사용합니다. 이 빠른 시작에서는 Azure Database for MySQL 인스턴스를 프로비전하고 준비한 다음, 한 번의 명령으로 영구 데이터베이스로 사용하도록 Azure Spring Cloud에서 Pet Clinic을 구성하는 방법을 설명합니다.

## <a name="variables-preparation"></a>변수 준비

다음 값을 사용합니다. 텍스트 파일 또는 환경 변수에 저장하여 오류를 방지합니다. 암호는 최소 8자 이상이어야 하며 영문 대문자 1개, 영문 소문자 1개, 숫자 1개, 영숫자가 아닌 문자(!, $, #, % 등)가 1개 이상 포함되어야 합니다.

```bash
export RESOURCE_GROUP=<resource-group-name> # customize this
export MYSQL_SERVER_NAME=<mysql-server-name> # customize this
export MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_NAME}.mysql.database.azure.com
export MYSQL_SERVER_ADMIN_NAME=<admin-name> # customize this
export MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_NAME}\@${MYSQL_SERVER_NAME}
export MYSQL_SERVER_ADMIN_PASSWORD=<password> # customize this
export MYSQL_DATABASE_NAME=petclinic
```

## <a name="prepare-an-azure-database-for-mysql-instance"></a>Azure Database for MySQL 인스턴스 준비

1. 이전 빠른 시작에서 다음 명령을 실행하지 않은 경우 CLI 기본값을 설정합니다.
    ```azcli
    az configure --defaults group=<resource group name> spring-cloud=<service name>
    ```
1. Azure Database for MySQL 서버를 만듭니다. 

    ```azcli
    az mysql server create --resource-group ${RESOURCE_GROUP} \
        --name ${MYSQL_SERVER_NAME} \
        --admin-user ${MYSQL_SERVER_ADMIN_NAME} \
        --admin-password ${MYSQL_SERVER_ADMIN_PASSWORD} \
        --sku-name GP_Gen5_2 \
        --ssl-enforcement Disabled \
        --version 5.7
    ```

1. Azure 리소스에서 액세스를 허용합니다.

    ```azcli
    az mysql server firewall-rule create --name allAzureIPs \
     --server ${MYSQL_SERVER_NAME} \
     --resource-group ${RESOURCE_GROUP} \
     --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
    ```

1. 테스트를 위해 개발 머신에서 액세스를 허용합니다.

    ```azcli
    az mysql server firewall-rule create --name devMachine \
     --server ${MYSQL_SERVER_NAME} \
     --resource-group ${RESOURCE_GROUP} \
     --start-ip-address <ip-address-of-your-dev-machine> \
     --end-ip-address <ip-address-of-your-dev-machine>
    ```

1. 연결 시간 제한을 늘립니다.

    ```azcli
    az mysql server configuration set --name wait_timeout \
     --resource-group ${RESOURCE_GROUP} \
     --server ${MYSQL_SERVER_NAME} --value 2147483
    ```

1. MySQL 서버에서 데이터베이스를 만들고 해당 설정을 지정합니다.

    ```sql
    // SUBSTITUTE values
    mysql -u ${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
     -h ${MYSQL_SERVER_FULL_NAME} -P 3306 -p

    Enter password:
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 64379
    Server version: 5.6.39.0 MySQL Community Server (GPL)

    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql> CREATE DATABASE petclinic;
    Query OK, 1 row affected (0.10 sec)

    mysql> CREATE USER 'root' IDENTIFIED BY 'petclinic';
    Query OK, 0 rows affected (0.11 sec)

    mysql> GRANT ALL PRIVILEGES ON petclinic.* TO 'root';
    Query OK, 0 rows affected (1.29 sec)

    mysql> CALL mysql.az_load_timezone();
    Query OK, 3179 rows affected, 1 warning (6.34 sec)

    mysql> SELECT name FROM mysql.time_zone_name;
    ...

    mysql> quit
    Bye
    ```

1. 표준 시간대를 설정합니다.

    ```azcli
    az mysql server configuration set --name time_zone \
     --resource-group ${RESOURCE_GROUP} \
     --server ${MYSQL_SERVER_NAME} --value "US/Pacific"
    ```

## <a name="update-apps-to-use-mysql-database"></a>MySQL 데이터베이스를 사용하도록 앱 업데이트

MySQL을 샘플 앱의 데이터베이스로 사용하도록 설정하려면 활성 프로필 MySQL 및 데이터베이스 자격 증명을 환경 변수로 사용하여 *고객 서비스* 앱을 업데이트하면 됩니다.

```azcli
az spring-cloud app update --name customers-service \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql" \
    --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
        MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
        MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
        MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
```

## <a name="update-extra-apps"></a>추가 앱 업데이트

```azcli
az spring-cloud app update --name api-gateway \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql"
az spring-cloud app update --name admin-server \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql"
az spring-cloud app update --name customers-service \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql" \
    --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
        MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
        MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
        MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
az spring-cloud app update --name vets-service \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql" \
    --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
        MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
        MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
        MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
az spring-cloud app update --name visits-service \
    --jvm-options="-Xms2048m -Xmx2048m -Dspring.profiles.active=mysql" \
    --env MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_FULL_NAME} \
        MYSQL_DATABASE_NAME=${MYSQL_DATABASE_NAME} \
        MYSQL_SERVER_ADMIN_LOGIN_NAME=${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
        MYSQL_SERVER_ADMIN_PASSWORD=${MYSQL_SERVER_ADMIN_PASSWORD}
```

## <a name="next-steps"></a>다음 단계

* [Azure Database for MySQL 인스턴스를 Azure Spring Cloud의 애플리케이션에 바인딩](how-to-bind-mysql.md)
* [관리 ID를 사용하여 Azure SQL Database를 Azure Spring Cloud의 앱에 연결](./connect-managed-identity-to-azure-sql.md)
