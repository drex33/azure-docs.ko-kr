---
title: Azure Cosmos DB Apache Spark 3 OLTP Connector for SQL API(미리 보기) 릴리스 정보 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL Java SDK의 각 버전 간 변경 내용을 포함하여 SQL API용 Azure Cosmos DB Apache Spark 3 OLTP 커넥터에 대해 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 06/21/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 2107f607d7af3dbcf1a0bed91be2a1e9b9b7edcc
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112466729"
---
# <a name="azure-cosmos-db-apache-spark-3-oltp-connector-for-core-sql-api-release-notes-and-resources"></a>Core(SQL) API용 Azure Cosmos DB Apache Spark 3 OLTP 커넥터: 릴리스 정보 및 리소스
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

**Azure Cosmos DB Spark 3 OLTP 커넥터** 는 SQL API를 사용하여 Azure Cosmos DB에 대한 Apache Spark v3 지원을 제공합니다.
[Azure Cosmos DB](introduction.md)는 개발자가 SQL, MongoDB, Cassandra, Graph 및 Table과 같은 표준 API를 사용하여 데이터를 사용할 수 있도록 하는 전역 분산 데이터베이스 서비스입니다.

## <a name="documentation"></a>설명서

- [시작](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/quick-start.md)
- [Catalog API](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/catalog-api.md)
- [구성 매개 변수 참조](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/configuration-reference.md)


## <a name="version-compatibility"></a>버전 호환성

| 커넥터     | Spark         | 최소 Java 버전 | 지원되는 Scala 버전 |
| ------------- | ------------- | -------------------- | -----------------------  |
| 4.0.0         | 3.1.1         |        8             | 2.12                     |

## <a name="download"></a>다운로드 

jar의 maven 좌표를 사용하여 Maven에서 Databricks Runtime 8에 Spark 커넥터를 자동으로 설치할 수 있습니다. `com.azure.cosmos.spark:azure-cosmos-spark_3-1_2-12:4.1.0`

SBT 프로젝트에서 Cosmos DB Spark 커넥터를 통합할 수도 있습니다.
```scala
libraryDependencies += "com.azure.cosmos.spark" % "azure-cosmos-spark_3-1_2-12" % "4.1.0"
```

[Maven Central Repo](https://search.maven.org/artifact/com.azure.cosmos.spark/azure-cosmos-spark_3-1_2-12/)에서 Cosmos DB Spark 커넥터를 사용할 수 있습니다.

### <a name="general"></a>일반

버그가 발생하면 [여기](https://github.com/Azure/azure-sdk-for-java/issues/new)에서 문제를 신고하세요.

새로운 기능이나 변경 사항을 제안하려면 버그와 동일한 방식으로 문제를 신고하세요.

[!INCLUDE[Changelog](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos-spark_3-1_2-12/CHANGELOG.md)]

## <a name="next-steps"></a>다음 단계

[Azure Cosmos DB Spark 3 OLTP 커넥터 사용에 대한 빠른 시작 가이드](create-sql-api-spark.md) 검토
