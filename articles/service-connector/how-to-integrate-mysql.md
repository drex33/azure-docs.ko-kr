---
title: 서비스 커넥터와 Azure Database for MySQL 통합
description: 서비스 커넥터를 사용 하 여 응용 프로그램에 Azure Database for MySQL 통합
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ce2839d614298d33f5bb1697c328b258463b6269
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053054"
---
# <a name="integrate-azure-database-for-mysql-with-service-connector"></a>서비스 커넥터와 Azure Database for MySQL 통합

이 페이지에는 서비스 커넥터를 사용 하는 Azure Database for MySQL의 지원 되는 인증 유형 및 클라이언트 유형이 나와 있습니다. 서비스 커넥터를 사용 하지 않고 다른 프로그래밍 언어의 Azure Database for MySQL에 계속 연결할 수 있습니다. 이 페이지에는 서비스 연결을 만들 때 제공 되는 기본 환경 변수 이름 및 값 (또는 스프링 부팅 구성)도 표시 됩니다. [서비스 커넥터 환경 변수 명명 규칙](concept-service-connector-internals.md)에 대해 자세히 알아볼 수 있습니다.

## <a name="supported-compute-service"></a>지원 되는 계산 서비스

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>지원 되는 인증 유형 및 클라이언트 유형

| 클라이언트 유형 | 시스템이 할당한 관리 ID | 사용자 할당 관리 Id | 비밀/ConnectionString | 서비스 주체 |
| --- | --- | --- | --- | --- |
| .Net (MySqlConnector) | | | ![예 아이콘](./media/green-check.png) | |
| Java(JDBC) | | | ![예 아이콘](./media/green-check.png) | |
| Java-스프링 부팅 (JDBC) | | | ![예 아이콘](./media/green-check.png) | |
| Node.js (mysql) | | | ![예 아이콘](./media/green-check.png) | |
| Python (mysql-커넥터-python) | | | ![예 아이콘](./media/green-check.png) | |
| Python-Django | | | ![예 아이콘](./media/green-check.png) | |
| Go (mysql 용 sql 드라이버) | | | ![예 아이콘](./media/green-check.png) | |
| PHP (mysqli) | | | ![예 아이콘](./media/green-check.png) | |
| Ruby (mysql2) | | | ![예 아이콘](./media/green-check.png) | |

## <a name="default-environment-variable-names-or-application-properties"></a>기본 환경 변수 이름 또는 응용 프로그램 속성

### <a name="net-mysqlconnector"></a>.NET(MySqlConnector) 

**비밀/ConnectionString**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_MYSQL_CONNECTIONSTRING | ADO.NET MySQL 연결 문자열 | `Server={MySQLName}.mysql.database.azure.com;Database={MySQLDbName};Port=3306;SSL Mode=Required;User Id={MySQLUsername};Password={TestDbPassword}` |

### <a name="java-jdbc"></a>Java(JDBC)

**비밀/ConnectionString**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_MYSQL_CONNECTIONSTRING | JDBC MySQL 연결 문자열 | `jdbc:mysql://{MySQLName}.mysql.database.azure.com:3306/{MySQLDbName}?sslmode=required&user={MySQLUsername}&password={Uri.EscapeDataString(TestDbPassword)}` |

### <a name="java---spring-boot-jdbc"></a>Java-스프링 부팅 (JDBC)

**비밀/ConnectionString**

| 애플리케이션 속성 | 설명 | 예제 값 |
| --- | --- | --- |
| datatsource | 스프링 부팅 JDBC 데이터베이스 URL | `jdbc:mysql://{MySQLName}.mysql.database.azure.com:3306/{MySQLDbName}?sslmode=required` |
| datatsource. 사용자 이름 | 데이터베이스 사용자 이름 | `{MySQLUsername}@{MySQLName}` |
| datatsource | 데이터베이스 암호 | `****` |

### <a name="nodejs-mysql"></a>Node.js (mysql) 

**비밀/ConnectionString**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
|---------|---------|---------|
| AZURE_MYSQL_HOST | 데이터베이스 호스트 URL  | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_USER | 데이터베이스 사용자 이름 | `MySQLDbName` |
| AZURE_MYSQL_PASSWORD | 데이터베이스 암호 | `****` |
| AZURE_MYSQL_DATABASE | 데이터베이스 이름  | `{MySQLUsername}@{MySQLName}` |
| AZURE_MYSQL_PORT | 포트 번호 | `3306` |
| AZURE_MYSQL_SSL | SSL 옵션 | `true` |

### <a name="python-mysql-connector-python"></a>Python (mysql-커넥터-python)

**비밀/ConnectionString**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_MYSQL_HOST | 데이터베이스 호스트 URL  | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_NAME | 데이터베이스 이름 | `{MySQLDbName}` |
| AZURE_MYSQL_PASSWORD | 데이터베이스 암호  | `****` |
| AZURE_MYSQL_USER | 데이터베이스 사용자 이름  | `{MySQLUsername}@{MySQLName}` |

### <a name="python-django"></a>Python-Django

**비밀/ConnectionString**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_MYSQL_HOST | 데이터베이스 호스트 URL  | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_USER | 데이터베이스 사용자 이름 | `{MySQLUsername}@{MySQLName}` |
| AZURE_MYSQL_PASSWORD | 데이터베이스 암호 | `****` |
| AZURE_MYSQL_NAME | 데이터베이스 이름 | `MySQLDbName` |


### <a name="go-go-sql-driver-for-mysql"></a>Go (mysql 용 sql 드라이버)

**비밀/ConnectionString**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_MYSQL_CONNECTIONSTRING | Go-sql 드라이버 연결 문자열 | `{MySQLUsername}@{MySQLName}:{Password}@tcp({ServerHost}:{Port})/{Database}?tls=true` |


### <a name="php-mysqli"></a>PHP (mysqli)

**비밀/ConnectionString**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
|---------|---------|---------|
| AZURE_MYSQL_HOST | 데이터베이스 호스트 URL | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_USERNAME | 데이터베이스 사용자 이름 | `{MySQLUsername}@{MySQLName}` |
| AZURE_MYSQL_PASSWORD | 데이터베이스 암호 | `****` |
| AZURE_MYSQL_DBNAME | 데이터베이스 이름 | `{MySQLDbName}` |
| AZURE_MYSQL_PORT | 포트 번호  | `3306` |
| AZURE_MYSQL_FLAG | SSL 또는 기타 플래그 | `MYSQLI_CLIENT_SSL` |

### <a name="ruby-mysql2"></a>Ruby (mysql2)

**비밀/ConnectionString**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
|---------|---------|---------|
| AZURE_MYSQL_HOST | 데이터베이스 호스트 URL  | `{MySQLName}.mysql.database.azure.com` |
| AZURE_MYSQL_USERNAME | 데이터베이스 사용자 이름 | `{MySQLUsername}@{MySQLName}` |
| AZURE_MYSQL_PASSWORD | 데이터베이스 암호 | `****` |
| AZURE_MYSQL_DATABASE | 데이터베이스 이름 | `{MySQLDbName}` |
| AZURE_MYSQL_SSLMODE | SSL 옵션 | `required` |

## <a name="next-steps"></a>다음 단계

서비스 커넥터에 대해 자세히 알아보려면 아래 나열된 자습서를 따르세요.

> [!div class="nextstepaction"]
> [서비스 커넥터 개념에 대해 알아보기](./concept-service-connector-internals.md)
