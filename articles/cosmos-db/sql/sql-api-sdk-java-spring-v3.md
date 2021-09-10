---
title: SQL API 릴리스 정보 및 리소스에 대한 Spring Data Azure Cosmos DB v3
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL Async Java SDK의 각 버전 간 변경 내용을 포함하여 SQL API용 Spring Data Azure Cosmos DB v3에 대해 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 2cd051b9c20df2d002feaa3908f577be9530fef9
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117151"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Core(SQL) API용 Spring Data Azure Cosmos DB v3: 릴리스 정보 및 리소스
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 변경 피드 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 3 OLTP 커넥터](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP 커넥터](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST (영문)](/rest/api/cosmos-db/)
> * [REST 리소스 공급자](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-query-getting-started.md)
> * [대량 실행기 - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [대량 실행기 - Java](sql-api-sdk-bulk-executor-java.md)

Core(SQL) 용 Spring Data Azure Cosmos DB 버전 3을 통해 개발자는 Spring 애플리케이션에서 Azure Cosmos DB를 사용할 수 있습니다. Spring Data Azure Cosmos DB는 데이터베이스 및 컬렉션을 조작하고, 문서 작업을 수행하고, 쿼리를 실행하기 위한 Spring Data 인터페이스를 제공합니다. 동기화 및 비동기(사후 대응식) API는 모두 동일한 Maven 아티팩트에서 지원됩니다. 

> [!IMPORTANT]
> Spring Data Azure Cosmos DB는 Spring Data 프레임 워크에 대한 종속성이 있습니다.
> 
> 3\.0.0에서 3.4.0의 azure-스프링 데이터 cosmos 버전은 스프링 데이터 버전 2.2 및 2.3을 지원합니다.
> 
> 3\.5.0 이상의 azure-스프링 데이터 cosmos 버전은 스프링 데이터 버전 2.4.3 이상을 지원합니다.
>

[Spring Framework](https://spring.io/projects/spring-framework)는 Java 애플리케이션 개발을 간소화하는 프로그래밍 및 구성 모델입니다. Spring은 종속성 주입을 사용하여 애플리케이션의 "연결"을 간소화합니다. 애플리케이션을 보다 간단하게 빌드하고 테스트할 수 있기 때문에 많은 개발자들이 Spring을 좋아합니다. [Spring Boot](https://spring.io/projects/spring-boot)는 웹 애플리케이션 및 마이크로 서비스 개발을 목적으로 이러한 배관의 처리를 확장합니다. [Spring Data](https://spring.io/projects/spring-data)는 Spring 또는 Spring Boot 애플리케이션의 컨텍스트에서 Azure Cosmos DB와 같은 데이터 저장소에 액세스하기 위한 프로그래밍 모델 및 프레임워크입니다. 

[Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) 애플리케이션에서 Spring Data Azure Cosmos DB를 사용할 수 있습니다.

> [!IMPORTANT]  
> 이러한 릴리스 정보는 Spring Data Azure Cosmos DB 버전 3에 대한 것입니다. [여기에서 버전 2의 릴리스 정보](sql-api-sdk-java-spring-v2.md)를 확인할 수 있습니다. 
>
> Spring Data Azure Cosmos DB는 SQL API만 지원합니다.
>
> 다른 Azure Cosmos DB API의 Spring Data에 대한 정보는 다음 문서를 참조하세요.
> * [Azure Cosmos DB의 Spring Data for Apache Cassandra](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Azure Cosmos DB의 Spring Data MongoDB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
>

## <a name="get-started-fast"></a>빠른 시작

  [Spring Boot Starter 가이드](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)에 따라 Spring Data Azure Cosmos DB을 시작하고 실행합니다. Spring Boot Starter 접근 방식은 Spring Data Azure Cosmos DB 커넥터를 사용하여 시작하는 데 권장되는 방법입니다.

  또는 아래와 같이 Spring Data Azure Cosmos DB 종속성을 아래 표시된 `pom.xml` 파일에 추가할 수 있습니다.

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest-version</version>
  </dependency>
  ```

## <a name="helpful-content"></a>유용한 콘텐츠

| 콘텐츠 | 링크 |
|---|---|
|**SDK 다운로드**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-cosmos) |
|**API 설명서** | [Java API 참조 설명서](/java/api/overview/azure/spring-data-cosmos-readme?view=azure-java-stable&preserve-view=true) |
|**SDK에 참여** | [GitHub의 Java용 Azure SDK 중앙 리포지토리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) | 
|**시작** | [빠른 시작: Azure Cosmos DB SQL API 데이터를 관리하는 Spring Data Azure Cosmos DB 앱을 빌드](./create-sql-api-spring-data.md) <br> [빠른 시작 코드가 포함된 GitHub 리포지토리](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) | 
|**기본 코드 샘플** | [Azure Cosmos DB: SQL API에 대한 Spring Data Azure Cosmos DB v3 예제](sql-api-spring-data-sdk-samples.md) <br> [샘플 코드가 포함된 GitHub 리포지토리](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples)|
| **성능 팁**| [Java SDK v4에 대한 성능 팁(Spring Data에 적용 가능)](performance-tips-java-sdk-v4-sql.md)| 
| **문제 해결** | [Java SDK v4에 대한 문제 해결(Spring Data에 적용 가능)](troubleshoot-java-sdk-v4-sql.md) | 
| **Azure Cosmos DB 워크샵 및 랩** |[Cosmos DB 워크샵 홈페이지](https://aka.ms/cosmosworkshop)

> [!IMPORTANT]
> * 3\.10.0 릴리스 - 버전 3.10.0 이상을 사용하는 것이 좋습니다.
> * 3\.5.0 릴리스는 Spring Boot 2.4.3 이상을 지원합니다.

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="additional-notes"></a>추가적인 참고 사항

* Spring Data Azure Cosmos DB는 Java JDK 8 및 Java JDK 11을 지원합니다.
* Spring Data 2.3은 현재 지원되지 않습니다. 현재 Spring Data 2.4는 지원되지 않습니다.

## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>다음 단계

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)에 대해 자세히 알아보세요.

[Spring Framework](https://spring.io/projects/spring-framework)에 대해 자세히 알아봅니다.

[Spring Boot](https://spring.io/projects/spring-boot)에 대해 자세히 알아봅니다.

[Spring Data](https://spring.io/projects/spring-data)에 대해 자세히 알아봅니다.
