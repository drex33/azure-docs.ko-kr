---
title: SQL API용 Azure Cosmos DB Apache Spark 2 OLTP 커넥터 릴리스 정보 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL Async Java SDK의 각 버전 간 변경 내용을 포함하여 SQL API용 Azure Cosmos DB Apache Spark 2 OLTP 커넥터에 대해 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 318faba31bec6b08c81d5d9f1d8741ddb65b5292
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116994"
---
# <a name="azure-cosmos-db-apache-spark-2-oltp-connector-for-core-sql-api-release-notes-and-resources"></a>Core(SQL) API용 Azure Cosmos DB Apache Spark 2 OLTP 커넥터: 릴리스 정보 및 리소스
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

Core(SQL)용 Azure Cosmos DB Apache Spark 2 OLTP 커넥터를 사용하여 빅 데이터 분석을 가속화할 수 있습니다. Spark 커넥터를 사용하면 Azure Cosmos DB에 저장된 데이터에서 [Spark](https://spark.apache.org/) 작업을 실행할 수 있습니다. 일괄 처리 및 스트림 처리가 지원됩니다.

Azure에서 관리형 Spark 클러스터를 제공하는 [Azure Databricks](https://azure.microsoft.com/services/databricks) 또는 [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)와 함께 커넥터를 사용할 수 있습니다. 다음 표에서는 지원되는 버전을 보여 줍니다.

| 구성 요소 | 버전 |
|---------|-------|
| Apache Spark | 2.4.*x*, 2.3.*x*, 2.2.*x* 및 2.1.*x* |
| Scala | 2.11 |
| Azure Databricks(런타임 버전) | 3\.4 이상 |

> [!WARNING]
> 이 커넥터는 Azure Cosmos DB의 Core(SQL) API를 지원합니다.
> MongoDB용 Cosmos DB API의 경우 [MongoDB Connector for Spark](https://docs.mongodb.com/spark-connector/master/)(Spark용 MongoDB 커넥터)를 사용합니다.
> Cosmos DB Cassandra API의 경우 [Cassandra Spark connector](https://github.com/datastax/spark-cassandra-connector)(Cassandra Spark 커넥터)를 사용합니다.
>

## <a name="resources"></a>리소스

| 리소스 | 링크 |
|---|---|
| **SDK 다운로드** | [최신 .jar 다운로드](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG), [Maven](https://search.maven.org/search?q=a:azure-cosmosdb-spark_2.4.0_2.11) |
|**API 설명서** | [Spark 커넥터 참조]() |
|**SDK에 기여** | [GitHub의 Apache Spark용 Azure Cosmos DB 커넥터](https://github.com/Azure/azure-cosmosdb-spark) | 
|**시작** | [Apache Spark-Azure Cosmos DB 커넥터를 사용하여 빅 데이터 분석 가속화](./create-sql-api-spark.md) <br> [Apache Kafka 및 Azure Cosmos DB에서 Apache Spark 정형 스트림 사용](../../hdinsight/apache-kafka-spark-structured-streaming-cosmosdb.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>릴리스 기록

### <a name="330"></a>3.3.0
#### <a name="new-features"></a>새로운 기능
- 변경 피드를 처리해야 하는 시작 시간을 지정하는 데 사용할 수 있는 새 구성 옵션인 `changefeedstartfromdatetime`을 추가합니다. 자세한 내용은 [Config options](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)(구성 옵션)를 참조하세요.

### <a name="320"></a>3.2.0
#### <a name="key-bug-fixes"></a>주요 버그 수정
- 큰 결과 집합(예: 수백만 개의 행)에 대해 실행기에서 과도한 메모리를 사용하게 하여 결국 `java.lang.OutOfMemoryError: GC overhead limit exceeded` 오류를 발생시키는 회귀를 수정합니다.

### <a name="311"></a>3.1.1
#### <a name="key-bug-fixes"></a>주요 버그 수정
* `ID`에 `ChangeFeedMaxPagesPerBatch` 구성이 적용된 파이프 문자(|)가 포함되는 스트리밍 검사점 에지 사례를 수정합니다.

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>새로운 기능
* 중첩된 파티션 키가 사용되는 경우 대량 업데이트에 대한 지원을 추가합니다.
* Azure Cosmos DB에 쓰는 동안 Decimal 및 Float 데이터 형식에 대한 지원을 추가합니다.
* Long(Unix epoch)을 값으로 사용하는 경우 타임스탬프 형식에 대한 지원을 추가합니다.

### <a name="308"></a>3.0.8
#### <a name="key-bug-fixes"></a>주요 버그 수정
* `WriteThroughputBudget` 구성을 사용할 때 발생하는 형식 캐스팅 예외를 수정합니다.

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>새로운 기능
* 대량 실패에 대한 오류 정보를 예외 및 로그에 추가합니다.

### <a name="306"></a>3.0.6
#### <a name="key-bug-fixes"></a>주요 버그 수정
* 스트리밍 검사점 문제를 수정합니다.

### <a name="305"></a>3.0.5
#### <a name="key-bug-fixes"></a>주요 버그 수정
* 노이즈를 줄이기 위해 의도치 않게 ERROR 수준으로 유지되는 메시지의 로그 수준을 수정합니다.

### <a name="304"></a>3.0.4
#### <a name="key-bug-fixes"></a>주요 버그 수정
* 파티션을 분할하는 동안 구조적 스트리밍의 버그를 수정합니다. 버그로 인해 몇 가지 변경 피드 레코드가 누락되거나 검사점 쓰기에 대해 Null 예외가 발생할 수 있습니다.

### <a name="303"></a>3.0.3
#### <a name="key-bug-fixes"></a>주요 버그 수정
* readStream에 대해 제공된 사용자 지정 스키마를 무시하는 버그를 수정합니다.

### <a name="302"></a>3.0.2
#### <a name="key-bug-fixes"></a>주요 버그 수정
* 빌드 시간을 50%까지 늘리는 회귀(음영 처리되지 않은 JAR에 모든 음영 처리된 종속성 포함)을 수정합니다.

### <a name="301"></a>3.0.1
#### <a name="key-bug-fixes"></a>주요 버그 수정
* RequestTimeoutException으로 인해 TCP를 통한 직접 전송이 실패하게 하는 종속성 문제를 수정합니다.

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>새로운 기능
* 연결 관리 및 연결 풀링을 개선하여 메타데이터 호출 수를 줄입니다.

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>다음 단계

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)에 대해 자세히 알아보세요.

[Apache Spark](https://spark.apache.org/)에 대해 자세히 알아봅니다.