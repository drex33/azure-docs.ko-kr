---
title: SQL API 릴리스 정보 및 리소스에 대한 Spring Data Azure Cosmos DB v2
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL Async Java SDK의 각 버전 간 변경 내용을 포함하여 SQL API용 Spring Data Azure Cosmos DB v2에 대해 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: b8f0cd354ec8389ad30f16a22f6ab7ef31a6e258
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116899"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>Core(SQL) API용 Spring Data Azure Cosmos DB v2: 릴리스 정보 및 리소스
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

 Core(SQL) API용 Spring Data Azure Cosmos DB 버전 2를 통해 개발자는 Spring 애플리케이션에서 Azure Cosmos DB를 사용할 수 있습니다. Spring Data Azure Cosmos DB는 데이터베이스 및 컬렉션을 조작하고, 문서 작업을 수행하고, 쿼리를 실행하기 위한 Spring 데이터 인터페이스를 제공합니다. 동기화 및 비동기(사후 대응식) API는 모두 동일한 Maven 아티팩트에서 지원됩니다. 

[Spring Framework](https://spring.io/projects/spring-framework)는 Java 애플리케이션 개발을 간소화하는 프로그래밍 및 구성 모델입니다. Spring은 종속성 주입을 사용하여 애플리케이션의 "연결"을 간소화합니다. 애플리케이션을 보다 간단하게 빌드하고 테스트할 수 있기 때문에 많은 개발자들이 Spring을 좋아합니다. [Spring Boot](https://spring.io/projects/spring-boot)는 웹 애플리케이션 및 마이크로 서비스 개발을 목적으로 이러한 연결의 처리를 확장합니다. [Spring Data](https://spring.io/projects/spring-data)는 Spring 또는 Spring Boot 애플리케이션의 컨텍스트에서 Azure Cosmos DB와 같은 데이터 저장소에 액세스하기 위한 프로그래밍 모델입니다. 

[Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) 애플리케이션에서 Spring Data Azure Cosmos DB를 사용할 수 있습니다.

> [!IMPORTANT]  
> 이러한 릴리스 정보는 Spring Data Azure Cosmos DB 버전 2에 대한 것입니다. [여기에서 버전 3의 릴리스 정보](sql-api-sdk-java-spring-v3.md)를 확인할 수 있습니다. 
>
> Spring Data Azure Cosmos DB는 SQL API만 지원합니다.
>
> 다른 Azure Cosmos DB API의 Spring Data에 대한 정보는 다음 문서를 참조하세요.
> * [Azure Cosmos DB의 Spring Data for Apache Cassandra](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Azure Cosmos DB의 Spring Data MongoDB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
>
> 신속하게 진행하고 싶으세요?
> 1. SDK를 사용할 수 있도록 [지원되는 최소 Java 런타임 JDK 8](/java/azure/jdk/)을 설치합니다.
> 2. [스타터](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)를 사용하여 Spring Data Azure Cosmos DB 앱을 만듭니다. 간단합니다.
> 3. 기본 Azure Cosmos DB 요청을 안내하는 [Spring Data Azure Cosmos DB 개발자 가이드](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb)를 참조하세요.
>
> [Spring Initializr](https://start.spring.io/)를 사용하여 Spring Boot Starter 앱을 빠르게 회전할 수 있습니다.
>

## <a name="resources"></a>리소스

| 리소스 | 링크 |
|---|---|
| **SDK 다운로드** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API 설명서** | [Spring Data Azure Cosmos DB 참조 설명서]() |
|**SDK에 기여** | [GitHub의 Spring Data Azure Cosmos DB 리포지토리](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Spring Boot Starter**| [Java용 Azure Cosmos DB Spring Boot Starter 클라이언트 라이브러리](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Azure Cosmos DB의 Spring TODO 앱 샘플**| [App Service Linux에서 엔드투엔드 Java 환경(2부)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**개발자 가이드** | [Spring Data Azure Cosmos DB 개발자 가이드](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**스타터 사용** | [Azure Cosmos DB SQL API에서 Spring Boot Starter를 사용하는 방법](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [Azure Cosmos DB Spring Boot Starter에 대한 GitHub 리포지토리](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Azure App Service 샘플** | [Azure App Service on Linux를 통해 Spring 및 Cosmos DB를 사용하는 방법](/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [TODO 앱 샘플](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>릴리스 기록

### <a name="230-may-21-2020"></a>2.3.0(2020년 5월 21일)
#### <a name="new-features"></a>새로운 기능
* Spring Boot 버전을 2.3.0으로 업데이트합니다.


### <a name="225-may-19-2020"></a>2.2.5(2020년 5월 19일)
#### <a name="new-features"></a>새로운 기능
* Azure Cosmos DB 버전을 3.7.3으로 업데이트합니다.
#### <a name="key-bug-fixes"></a>주요 버그 수정
* Azure Cosmos DB SDK 3.7.3의 메모리 누수 수정과 Netty 버전 업그레이드를 포함합니다.

### <a name="224-april-6-2020"></a>2.2.4(2020년 4월 6일)
#### <a name="key-bug-fixes"></a>주요 버그 수정
* `CosmosDbConfig`에서 고려할 `allowTelemetry` 플래그를 수정합니다.
* 컨테이너의 `TTL` 속성을 수정합니다.

### <a name="223-february-25-2020"></a>2.2.3(2020년 2월 25일)
#### <a name="new-features"></a>새로운 기능
* 파티션 키 API를 기준으로 새 `findAll`를 추가합니다.
* Azure Cosmos DB 버전을 3.7.0으로 업데이트합니다.
#### <a name="key-bug-fixes"></a>주요 버그 수정
* `collectionName` -> `containerName`을 수정합니다.
* `entityClass` 및 `domainClass` -> `domainType`을 수정합니다.
* "입력 엔터티 대신 리포지토리에 의해 저장된 엔터티 컬렉션 반환"을 수정합니다.

### <a name="2110-february-25-2020"></a>2.1.10(2020년 2월 25일)
#### <a name="key-bug-fixes"></a>주요 버그 수정
* 백포트는 "입력 엔터티 대신 리포지토리에 의해 저장된 엔터티 컬렉션 반환"을 수정합니다.

### <a name="222-january-15-2020"></a>2.2.2(2020년 1월 15일)
#### <a name="new-features"></a>새로운 기능
* Azure Cosmos DB 버전을 3.6.0으로 업데이트합니다.
#### <a name="key-bug-fixes"></a>주요 버그 수정

### <a name="221-december-31-2019"></a>2.2.1(2019년 12월 31일)
#### <a name="new-features"></a>새로운 기능
* Azure Cosmos DB SDK 버전을 3.5.0으로 업데이트합니다.
* 자동 컬렉션 만들기를 사용하거나 사용하지 않도록 주석 필드를 추가합니다.
* 예외 처리를 개선합니다. `CosmosDBAccessException`을 통해 `CosmosClientException`을 노출합니다.
* `ResponseDiagnostics`를 통해 `requestCharge` 및 `activityId`를 노출합니다.
#### <a name="key-bug-fixes"></a>주요 버그 수정
* SDK 3.5.0 업데이트는 "Cosmos DB HTTP 응답 헤더가 8192바이트보다 큰 경우 예외", "ConsistencyPolicy.defaultConsistencyLevel()은 제한된 부실 및 일관적인 접두사에서 실패합니다."를 수정합니다.
* `findById` 메서드의 동작을 수정합니다. 이전에는 엔터티를 찾지 못한 경우 이 메서드는 예외를 throw하는 대신 빈 결과를 반환했습니다.
* `CosmosPageRequest`가 사용된 경우 다음 페이지에 정렬이 적용되지 않은 버그를 수정합니다.

### <a name="219-december-26-2019"></a>2.1.9(2019년 12월 26일)
#### <a name="new-features"></a>새로운 기능
* 자동 컬렉션 만들기를 사용하거나 사용하지 않도록 주석 필드를 추가합니다.
#### <a name="key-bug-fixes"></a>주요 버그 수정
*  `findById` 메서드의 동작을 수정합니다. 이전에는 엔터티를 찾지 못한 경우 이 메서드는 예외를 throw하는 대신 빈 결과를 반환했습니다.

### <a name="220-october-21-2019"></a>2.2.0(2019년 10월 21일)
#### <a name="new-features"></a>새로운 기능
* 반응형 Cosmos 리포지토리를 완전하게 지원합니다.
* Azure Cosmos DB 요청 진단 문자열 및 쿼리 메트릭을 지원합니다.
* Azure Cosmos DB SDK 버전을 3.3.1로 업데이트합니다.
* Spring Framework 버전을 5.2.0.RELEASE로 업그레이드합니다.
* Spring Data Commons 버전을 2.2.0.RELEASE로 업그레이드합니다.
* `findByIdAndPartitionKey` 및 `deleteByIdAndPartitionKey` API를 추가합니다.
* azure-documentdb에서 종속성을 제거합니다.
* DocumentDB를 Azure Cosmos DB로 다시 브랜딩합니다.
#### <a name="key-bug-fixes"></a>주요 버그 수정
* "pageSize가 리포지토리의 총 항목보다 작은 경우 정렬 시 예외가 throw됩니다"를 수정합니다.

### <a name="218-october-18-2019"></a>2.1.8(2019년 10월 18일)
#### <a name="new-features"></a>새로운 기능
* DocumentDB API는 사용되지 않습니다.
* `findByIdAndPartitionKey` 및 `deleteByIdAndPartitionKey` API를 추가합니다.
* `_etag`에 따라 낙관적 잠금을 추가합니다.
* 문서 컬렉션 이름에 대해 SpEL 식을 사용하도록 설정합니다.
* 향상된 기능 `ObjectMapper`를 추가합니다.

### <a name="217-october-18-2019"></a>2.1.7(2019년 10월 18일)
#### <a name="new-features"></a>새로운 기능
* Azure Cosmos DB SDK 버전 3 종속성을 추가합니다.
* 반응형 Cosmos 리포지토리를 추가합니다.
* Azure Cosmos DB SDK 버전 3을 사용하도록 `DocumentDbTemplate`의 구현을 업데이트합니다.
* 반응형 Cosmos 리포지토리 지원에 대한 다른 구성 변경 사항을 추가합니다.

### <a name="212-march-19-2019"></a>2.1.2(2019년 3월 19일)
#### <a name="key-bug-fixes"></a>주요 버그 수정
* 다음에 대한 `applicationInsights` 종속성을 제거합니다.
    * 종속성이 오염될 잠재적 위험
    * Java 11 비호환
    * CPU 및/또는 메모리에 대한 잠재적 성능 저하 방지

### <a name="207-march-20-2019"></a>2.0.7(2019년 3월 20일)
#### <a name="key-bug-fixes"></a>주요 버그 수정
* 백포트는 다음에 대한 `applicationInsights` 종속성을 제거합니다.
    * 종속성이 오염될 잠재적 위험
    * Java 11 비호환
    * CPU 및/또는 메모리에 대한 잠재적 성능 저하 방지

### <a name="211-march-7-2019"></a>2.1.1(2019년 3월 7일)
#### <a name="new-features"></a>새로운 기능
* 주 버전을 2.1.1로 업데이트합니다.

### <a name="206-march-7-2019"></a>2.0.6(2019년 3월 7일)
#### <a name="new-features"></a>새로운 기능
* 원격 분석에서 모든 예외를 무시합니다.

### <a name="210-december-17-2018"></a>2.1.0(2018년 12월 17일)
#### <a name="new-features"></a>새로운 기능
* 버전을 2.1.0으로 업데이트하여 문제를 해결합니다.

### <a name="205-september-13-2018"></a>2.0.5(2018년 9월 13일)
#### <a name="new-features"></a>새로운 기능
* `exists` 및 `startsWith` 키워드를 추가합니다.
* 추가 정보를 업데이트합니다.
#### <a name="key-bug-fixes"></a>주요 버그 수정
* "엔터티에 대해 직접 자체 href를 호출할 수 없습니다"를 수정합니다.
* "컬렉션이 생성되지 않으면 findAll이 실패합니다"를 수정합니다.

### <a name="204-prerelease-august-23-2018"></a>2.0.4(시험판)(2018년 8월 23일)
#### <a name="new-features"></a>새로운 기능
* documentdb에서 cosmosdb로 패키지의 이름을 바꿉니다.
* 쿼리 메서드 키워드의 새 기능을 추가합니다. 이제 SQL API의 16개 키워드가 지원됩니다.
* 페이징 및 정렬을 사용하여 쿼리의 새 기능을 추가합니다.
* spring-data-cosmosdb의 구성을 간소화합니다.
* `deleteCollection` 및 `deleteAll` API를 추가합니다.

#### <a name="key-bug-fixes"></a>주요 버그 수정
* 버그 수정 및 결함을 완화합니다.

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>다음 단계
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)에 대해 자세히 알아보세요.

[Spring Framework](https://spring.io/projects/spring-framework)에 대해 자세히 알아봅니다.

[Spring Boot](https://spring.io/projects/spring-boot)에 대해 자세히 알아봅니다.

[Spring Data](https://spring.io/projects/spring-data)에 대해 자세히 알아봅니다.