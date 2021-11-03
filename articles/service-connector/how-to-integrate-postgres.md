---
title: 서비스 커넥터와 Azure Database for PostgreSQL 통합
description: Service Connector를 통해 애플리케이션에 Azure Database for PostgreSQL 통합
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6c8cfb866da9a57c54f443558212c5d88269fe0a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052806"
---
# <a name="integrate-azure-database-for-postgresql-with-service-connector"></a>서비스 커넥터와 Azure Database for PostgreSQL 통합

이 페이지에는 서비스 커넥터를 사용하여 지원되는 인증 유형 및 클라이언트 유형의 Azure Database for PostgreSQL 표시됩니다. Service Connector를 사용하지 않고도 다른 프로그래밍 언어로 Azure Database for PostgreSQL 연결할 수 있습니다. 이 페이지에는 서비스 연결을 만들 때 얻을 수 있는 기본 환경 변수 이름 및 값(또는 Spring Boot 구성)도 표시됩니다. [Service Connector 환경 변수 명명 규칙](concept-service-connector-internals.md)에 대해 자세히 알아볼 수 있습니다.

## <a name="supported-compute-service"></a>지원되는 컴퓨팅 서비스

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>지원되는 인증 유형 및 클라이언트 유형

| 클라이언트 유형 | 시스템이 할당한 관리 ID | 사용자 할당 관리 ID | Secret/ConnectionString | 서비스 주체 |
| --- | --- | --- | --- | --- |
| .Net(ADO.NET) | | | ![예 아이콘](./media/green-check.png) | |
| Java(JDBC) | | | ![예 아이콘](./media/green-check.png) | |
| Java - Spring Boot(JDBC) | | | ![예 아이콘](./media/green-check.png) | |
| Node.js(pg) | | | ![예 아이콘](./media/green-check.png) | |
| Python(psycopg2) | | | ![예 아이콘](./media/green-check.png) | |
| Python-Django | | | ![예 아이콘](./media/green-check.png) | |
| Go(pg) | | | ![예 아이콘](./media/green-check.png) | |
| PHP(네이티브) | | | ![예 아이콘](./media/green-check.png) | |
| Ruby(ruby-pg) | | | ![예 아이콘](./media/green-check.png) | |

## <a name="default-environment-variable-names-or-application-properties"></a>기본 환경 변수 이름 또는 애플리케이션 속성

### <a name="net-adonet"></a>.NET(ADO.NET) 

**Secret/ConnectionString**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | ADO.NET PostgreSQL 연결 문자열 | `Server={your-postgres-server-name}.postgres.database.azure.com;Database={database-name};Port=5432;Ssl Mode=Require;User Id={username}@{servername};Password=****;` |

### <a name="java-jdbc"></a>Java(JDBC)

**Secret/ConnectionString**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | JDBC PostgreSQL 연결 문자열 | `jdbc:postgresql://{your-postgres-server-name}.postgres.database.azure.com:5432/{database-name}?sslmode=require&user={username}%40{servername}l&password=****` |

### <a name="java---spring-boot-jdbc"></a>Java - Spring Boot(JDBC)

**Secret/ConnectionString**

| 애플리케이션 속성 | 설명 | 예제 값 |
| --- | --- | --- |
| spring.datatsource.url | 데이터베이스 URL | `jdbc:postgresql://{your-postgres-server-name}.postgres.database.azure.com:5432/{database-name}?sslmode=require` |
| spring.datatsource.username | 데이터베이스 사용자 이름 | `{username}@{servername}` |
| spring.datatsource.password | 데이터베이스 암호 | `****` |

### <a name="nodejs-pg"></a>Node.js(pg) 

**Secret/ConnectionString**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
|---------|---------|---------|
| AZURE_POSTGRESQL_HOST | 데이터베이스 호스트 URL | `{your-postgres-server-name}.postgres.database.azure.com` |
| AZURE_POSTGRESQL_USER | 데이터베이스 사용자 이름 | `{username}@{servername}` |
| AZURE_POSTGRESQL_PASSWORD | 데이터베이스 암호 | `****` |
| AZURE_POSTGRESQL_DATABASE | 데이터베이스 이름 | `{database-name}` |
| AZURE_POSTGRESQL_PORT | 포트 번호  | `5432` |
| AZURE_POSTGRESQL_SSL | SSL 옵션  | `true` |

### <a name="python-psycopg2"></a>Python(psycopg2)

**Secret/ConnectionString**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | psycopg2 연결 문자열 | `dbname={database-name} host={your-postgres-server-name}.postgres.database.azure.com port=5432 sslmode=require user={username}@{servername} password=****` |

### <a name="python-django"></a>Python-Django

**Secret/ConnectionString**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_POSTGRESQL_HOST | 데이터베이스 호스트 URL | `{your-postgres-server-name}.postgres.database.azure.com` |
| AZURE_POSTGRESQL_USER | 데이터베이스 사용자 이름 | `{username}@{servername}` |
| AZURE_POSTGRESQL_PASSWORD | 데이터베이스 암호 | `****` |
| AZURE_POSTGRESQL_NAME | 데이터베이스 이름 | `{database-name}` |


### <a name="go-pg"></a>Go(pg)

**Secret/ConnectionString**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | Go pg 연결 문자열 | `host={your-postgres-server-name}.postgres.database.azure.com dbname={database-name} sslmode=require user={username}@{servername} password=****` |


### <a name="php-native"></a>PHP (네이티브)

**비밀/ConnectionString**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | PHP 기본 postgres 연결 문자열 | `host={your-postgres-server-name}.postgres.database.azure.com port=5432 dbname={database-name} sslmode=requrie user={username}@{servername} password=****` |

### <a name="ruby-ruby-pg"></a>Ruby (ruby)

**비밀/ConnectionString**

| 기본 환경 변수 이름 | 설명 | 예제 값 |
| --- | --- | --- |
| AZURE_POSTGRESQL_CONNECTIONSTRING | Ruby pg 연결 문자열 | `host={your-postgres-server-name}.postgres.database.azure.com port=5432 dbname={database-name} sslmode=require user={username}@{servername} password=****` |

## <a name="next-steps"></a>다음 단계

서비스 커넥터에 대해 자세히 알아보려면 아래 나열된 자습서를 따르세요.

> [!div class="nextstepaction"]
> [서비스 커넥터 개념에 대해 알아보기](./concept-service-connector-internals.md)
