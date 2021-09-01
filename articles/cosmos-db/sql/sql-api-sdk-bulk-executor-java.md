---
title: Azure Cosmos DB는 대량 실행기 Java API, SDK 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB Bulk Executor Java SDK의 각 버전 간 변경 내용을 포함하여 Bulk Executor Java API 및 SDK에 대한 모든 것을 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 9798c7cd870bc05901e47cc1bcf410bd6d5a42a6
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117077"
---
# <a name="java-bulk-executor-library-download-information"></a>Java Bulk Executor 라이브러리: 정보 다운로드
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

| | 링크/메모 |
|---|---|
|**설명**|클라이언트 애플리케이션은 Bulk Executor 라이브러리를 통해 Azure Cosmos DB 계정에서 대량 작업을 수행할 수 있습니다. Bulk Executor 라이브러리는 BulkImport 및 BulkUpdate 네임스페이스를 제공합니다. BulkImport 모듈은 컬렉션이 최대 범위 내에 사용되도록 프로비전된 처리량과 같이 최적화된 방법으로 문서를 대량 수집할 수 있습니다. BulkUpdate 모듈은 Azure Cosmos 컨테이너의 기존 데이터를 패치로 대량 업데이트할 수 있습니다.|
|**SDK 다운로드**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**GitHub의 대량 실행기 라이브러리**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API 설명서**| [Java API 참조 설명서](/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**시작**|[Bulk Executor 라이브러리 Java SDK 시작](bulk-executor-java.md)|
|**지원되는 최소 런타임**|[JDK(Java Development Kit) 7 이상](/java/azure/jdk/)|

## <a name="release-notes"></a>릴리스 정보
### <a name="2123"></a><a name="2.12.3"></a>2.12.3

* `GoneException`이 `IllegalStateException`에 래핑될 때 재시도 정책 수정 - 이 변경은 410에서 게이트웨이 캐시를 새로 고쳐서 Spark 커넥터(Spark 2.4용)가 사용자 지정 재시도 정책을 사용하여 파티션 분할 중에 쿼리가 성공할 수 있도록 하는 데 필요합니다.

### <a name="2122"></a><a name="2.12.2"></a>2.12.2

* 일시적인 오류로 인해 문서를 항상 가져오지 않는 문제를 해결합니다.

### <a name="2121"></a><a name="2.12.1"></a>2.12.1

* 최신 Cosmos Core SDK 버전을 사용하도록 업그레이드합니다.

### <a name="2120"></a><a name="2.12.0"></a>2.12.0

* 대량 작업을 위해 Spark 커넥터를 통해 제공되는 RU 예산 처리를 개선합니다. baseBatchSize가 있는 Spark 커넥터에서 초기 일회성 대량 가져오기가 수행되고 위의 대량 가져오기에 대한 RU 사용량이 수집됩니다.
  위의 RU 사용량을 기반으로 miniBatchSizeAdjustmentFactor를 계산하고, 이를 기반으로 미니 일괄 처리 크기를 조정합니다. 경과 시간 및 각 일괄 처리 가져오기에 사용된 RU를 기반으로 절전 기간이 계산되어 초당 RU 사용량을 제한하고 다음 일괄 처리 가져오기 전에 스레드를 일시 중지하는 데 사용됩니다.

### <a name="2110"></a><a name="2.11.0"></a>2.11.0

* 중첩 파티션 키를 사용할 때 대량 업데이트를 차단하는 버그 수정

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* DocumentAnalyzer.java가 json에서 중첩 파티션 키 값을 정확하게 추출하는 데 필요한 픽스

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* BulkDelete 작업에 특정 오류를 다시 시도할 수 있는 기능을 추가하고, 다시 시도할 수 있는 사용자에게 오류 목록을 반환합니다.

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Cosmos SDK 버전 2.4.7에 대한 업데이트입니다.

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* 'id' 및 파티션 키 값이 업데이트된 항목 목록에 추가된 후 패치가 적용된 문서 속성이 배치되도록 'id' 및 파티션 키 값에 대한 'mergeAll'을 수행하는 데 필요한 픽스입니다.

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* 동시성의 시작 수준을 1로 업데이트하고 minibatch의 디버그 로그를 추가했습니다.