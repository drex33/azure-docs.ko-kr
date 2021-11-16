---
title: Azure Cosmos DB Apache Spark 3 OLTP Connector for SQL API(미리 보기) 릴리스 정보 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL Java SDK의 각 버전 간 변경 내용을 포함하여 SQL API용 Azure Cosmos DB Apache Spark 3 OLTP 커넥터에 대해 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/12/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: abb10485baebc0b7379ec650fd9eeb95ebadf326
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491282"
---
# <a name="azure-cosmos-db-apache-spark-3-oltp-connector-for-core-sql-api-release-notes-and-resources"></a>Core(SQL) API용 Azure Cosmos DB Apache Spark 3 OLTP 커넥터: 릴리스 정보 및 리소스
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

**Azure Cosmos DB OLTP Spark 커넥터는** SQL API를 사용하여 Azure Cosmos DB에 대한 Apache Spark 지원을 제공합니다. Azure Cosmos DB는 개발자가 SQL, MongoDB, Cassandra, Graph 및 Table과 같은 표준 API를 사용하여 데이터를 사용할 수 있도록 하는 전역 분산 데이터베이스 서비스입니다.

환경을 개선하는 방법에 대한 피드백 또는 아이디어가 있는 경우 [SDK GitHub 리포지토리에서 문제를 만듭니다.](https://github.com/Azure/azure-sdk-for-java/issues/new)

## <a name="documentation-links"></a>문서 링크

* [시작](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/quick-start.md)
* [Catalog API](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/catalog-api.md)
* [구성 매개 변수 참조](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/configuration-reference.md)

## <a name="version-compatibility"></a>버전 호환성

| 커넥터     | 최소 Spark 버전 | 최소 Java 버전 | 지원되는 Scala 버전 | 지원되는 Databricks 런타임 |
| ------------- | --------------------- | -------------------- | -----------------------  | ----------------------------- |
| 4.4.0         | 3.1.1                 | 8                    | 2.12                     | 8. \* , 9.\*                    |
| 4.3.1         | 3.1.1                 | 8                    | 2.12                     | 8. \* , 9.\*                    |
| 4.3.0         | 3.1.1                 | 8                    | 2.12                     | 8. \* , 9.\*                    |
| 4.2.0         | 3.1.1                 | 8                    | 2.12                     | 8.\*                          |
| 4.1.0         | 3.1.1                 | 8                    | 2.12                     | 8.\*                          |
| 4.0.0         | 3.1.1                 | 8                    | 2.12                     | 8.\*                          |
| 4.0.0-beta.3  | 3.1.1                 | 8                    | 2.12                     | 8.\*                          |
| 4.0.0-beta.2  | 3.1.1                 | 8                    | 2.12                     | 8.\*                          |
| 4.0.0-beta.1  | 3.1.1                 | 8                    | 2.12                     | 8.\*                          |

## <a name="download"></a>다운로드

jar의 maven 좌표를 사용하여 Maven에서 Databricks Runtime 8에 Spark 커넥터를 자동으로 설치할 수 있습니다. `com.azure.cosmos.spark:azure-cosmos-spark_3-1_2-12:4.4.0`

SBT 프로젝트에서 Cosmos DB Spark 커넥터를 통합할 수도 있습니다.

```scala
libraryDependencies += "com.azure.cosmos.spark" % "azure-cosmos-spark_3-1_2-12" % "4.4.0"
```

Azure Cosmos DB Spark 커넥터는 [Maven Central Repo](https://search.maven.org/search?q=g:com.azure.cosmos.spark)에서 사용할 수 있습니다.

버그가 발생하거나 기능 변경을 제안하려면 [문제를 제출합니다.](https://github.com/Azure/azure-sdk-for-java/issues/new) 

## <a name="next-steps"></a>다음 단계

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)에 대해 자세히 알아보세요.

[Apache Spark](https://spark.apache.org/)에 대해 자세히 알아봅니다.