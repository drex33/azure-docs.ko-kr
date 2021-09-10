---
title: Azure Cosmos DB는 SQL Async Java API, SDK 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL 비동기 Java SDK의 각 버전 간 변경 내용을 포함하여 SQL 비동기 Java API 및 SDK에 대한 모든 것을 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: ecec077db8ca0b9b1a5285256eabb67791d461f2
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117152"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>SQL API용 Azure Cosmos DB Async Java SDK: 릴리스 정보 및 리소스
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
> * [REST](/rest/api
> * [REST 리소스 공급자](/azure/azure-resource-manager/management/azure-services-resource-providers)
> * [SQL](sql-query-getting-started.md)
> * [대량 실행기 - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [대량 실행기 - Java](sql-api-sdk-bulk-executor-java.md)

SQL API 비동기 Java SDK는 [Netty 라이브러리](https://netty.io/)를 지원하고 비동기 작업을 제공하기 때문에 SQL API Java SDK와는 다릅니다. 기존 [SQL API Java SDK](sql-api-sdk-java.md)는 비동기 작업을 지원하지 않습니다. 

> [!IMPORTANT]  
> Azure Cosmos DB의 최신 Java SDK가 아닙니다! 프로젝트에 [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md)를 사용하는 것이 좋습니다. 업그레이드하려면 [Azure Cosmos DB Java SDK v4로 마이그레이션하기](migrate-java-v4-sdk.md) 안내서 및 [Reactor vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) 안내서의 지침을 따르세요. 
>

> [!IMPORTANT]  
> 2024년 8월 31일에 Azure Cosmos DB Async Java SDK v2.x가 사용 중지됩니다. SDK 및 SDK를 사용하는 모든 애플리케이션은 **계속 작동합니다**. Azure Cosmos DB는 이 SDK에 대한 추가 유지 관리 및 지원 제공을 중단합니다.
> 위의 지침에 따라 Azure Cosmos DB Java SDK v4로 마이그레이션하는 것이 좋습니다.
>

| | 링크 |
|---|---|
| **SDK 다운로드** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API 설명서** |[Java API 참조 설명서](/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient) | 
|**SDK에 참여** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**시작** | [비동기 Java SDK 시작](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**코드 샘플** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **성능 팁**| [Github 추가 정보](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **지원되는 최소 런타임**|[JDK 8](/java/azure/jdk/) | 

[!INCLUDE [Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>참고 항목
Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요.