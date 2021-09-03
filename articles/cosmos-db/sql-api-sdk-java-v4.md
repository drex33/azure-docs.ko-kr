---
title: Azure Cosmos DB Java SDK v4 for SQL API 릴리스 정보 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL Async Java SDK의 각 버전 간 변경 내용을 포함하여 SQL API용 Azure Cosmos DB Java SDK v4 및 SDK에 대한 모든 것을 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: d23bf4f6e8667a3e8477292e88bed8f47ec6fc5f
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122531302"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 for Core(SQL) API: 릴리스 정보 및 리소스
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
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
> * [SQL](./sql-query-getting-started.md)
> * [대량 실행기 - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [대량 실행기 - Java](sql-api-sdk-bulk-executor-java.md)

Azure Cosmos DB Java SDK v4 for Core(SQL)는 비동기 API와 동기 API를 하나의 Maven 아티팩트로 결합합니다. V4 SDK는 Project Reactor 및 [Netty 라이브러리](https://netty.io/)를 기준으로 향상된 성능, 새 API 기능 및 비동기 지원을 제공합니다. Azure Cosmos DB Java SDK v4와 [Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) 및 [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md)를 사용하여 성능을 향상시킬 수 있습니다.

> [!IMPORTANT]  
> 이러한 릴리스 정보는 Azure Cosmos DB Java SDK v4에만 해당됩니다. 현재 v4보다 이전 버전을 사용하는 경우 v4로 업그레이드하는 데 도움이 필요하면 [Azure Cosmos DB Java SDK v4로 마이그레이션](migrate-java-v4-sdk.md) 가이드를 참조하세요.
>
> 빠른 시작을 위한 세 가지 단계는 다음과 같습니다.
> 1. SDK를 사용할 수 있도록 [지원되는 최소 Java 런타임 JDK 8](/java/azure/jdk/)을 설치합니다.
> 2. Maven 아티팩트에 대한 액세스 권한을 가져오고 기본 Azure Cosmos DB 요청을 안내하는 [Azure Cosmos DB Java SDK v4에 대한 빠른 시작 가이드](./create-sql-api-java.md)를 사용합니다.
> 3. 애플리케이션에 대한 SDK를 최적화하는 Azure Cosmos DB Java SDK v4 [성능 팁](performance-tips-java-sdk-v4-sql.md) 및 [문제 해결](troubleshoot-java-sdk-v4-sql.md) 가이드를 읽어 보세요.
>
> [Azure Cosmos DB 워크샵 및 랩](https://aka.ms/cosmosworkshop)은 Azure Cosmos DB Java SDK v4 사용 방법을 배우는 데 유용한 또 다른 리소스입니다.
>

## <a name="helpful-content"></a>유용한 콘텐츠

| 콘텐츠 | 링크 |
|---|---|
|**SDK 다운로드**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API 설명서** | [Java API 참조 설명서](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-cosmos/latest/index.html) |
|**SDK에 참여** | [GitHub의 Java용 Azure SDK 중앙 리포지토리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**시작** | [빠른 시작: Azure Cosmos DB SQL API 데이터를 관리하는 Java 앱 빌드](./create-sql-api-java.md) <br> [빠른 시작 코드가 포함된 GitHub 리포지토리](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**기본 코드 샘플** | [Azure Cosmos DB: SQL API에 대한 Java 예제](sql-api-java-sdk-samples.md) <br> [샘플 코드가 포함된 GitHub 리포지토리](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**변경 피드를 사용하는 콘솔 앱**| [변경 피드 - Java SDK v4 샘플](create-sql-api-java-changefeed.md) <br> [샘플 코드가 포함된 GitHub 리포지토리](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**웹앱 샘플**| [Java SDK v4로 웹앱 빌드](sql-api-java-application.md) <br> [샘플 코드가 포함된 GitHub 리포지토리](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **성능 팁**| [Java SDK v4용 성능 팁](performance-tips-java-sdk-v4-sql.md)| 
| **문제 해결** | [Java SDK v4 문제 해결](troubleshoot-java-sdk-v4-sql.md) |
| **이전 SDK에서 v4로 마이그레이션** | [Java V4 SDK로 마이그레이션](migrate-java-v4-sdk.md) |
| **지원되는 최소 런타임**|[JDK 8](/java/azure/jdk/) | 
| **Azure Cosmos DB 워크샵 및 랩** |[Cosmos DB 워크샵 홈페이지](https://aka.ms/cosmosworkshop)

> [!IMPORTANT]
> * 4\.18.0 릴리스 - 버전 4.18.0 이상을 사용하는 것이 좋습니다.
> * 4\.13.0 릴리스는 `reactor-core` 및 `reactor-netty` 주 버전을 `2020.0.4 (Europium)` 릴리스 트레인으로 업데이트합니다.

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos/CHANGELOG.md)]

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](includes/cosmos-db-sdk-faq.md)] 

## <a name="next-steps"></a>다음 단계
Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요.
