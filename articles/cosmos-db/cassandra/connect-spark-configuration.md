---
title: Spark에서 Azure Cosmos DB Cassandra API를 사용하여 작업
description: 이 문서는 Spark에서 Cosmos DB Cassandra API 통합에 대한 기본 페이지입니다.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/01/2019
ms.openlocfilehash: f0e695248ecddebb4e737465a639e6385f6d29f0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536728"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Spark에서 Azure Cosmos DB Cassandra API에 연결
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

이 문서는 Spark에서 Azure Cosmos DB Cassandra API 통합에 대한 문서 시리즈 중 하나입니다. 문서는 연결, DDL(데이터 정의 언어) 작업, 기본 DML(데이터 조작 언어) 작업 및 Spark에서 고급 Azure Cosmos DB Cassandra API 통합을 다룹니다. 

## <a name="prerequisites"></a>사전 요구 사항
* [Azure Cosmos DB Cassandra API 계정을 프로비전합니다](manage-data-dotnet.md#create-a-database-account).

* 선택한 Spark 환경 [[Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) | [Azure HDInsight Spark](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md) | 기타]를 프로비전합니다.

## <a name="dependencies-for-connectivity"></a>연결에 대한 종속성
* **Cassandra용 Spark 커넥터:** Spark 커넥터는 Azure Cosmos DB Cassandra API에 연결하는 데 사용됩니다.  Spark 및 Spark 환경의 Scala 버전과 호환되는 [Maven central]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector)에 위치한 커넥터의 버전을 식별하고 사용합니다. Spark 3.0 이상과 maven 좌표 `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0`에서 사용할 수 있는 Spark 커넥터를 지원하는 환경이 권장됩니다. Spark 2.x를 사용하는 경우 maven 좌표 `com.datastax.spark:spark-cassandra-connector_2.11:2.4.3`에서 Spark 커넥터를 사용하는 Spark 버전 2.4.5 환경을 권장합니다.


* **Cassandra API용 Azure Cosmos DB 도우미 라이브러리:** Spark 커넥터 외에도 Spark 2.x 버전을 사용하는 경우에는 [요율 제한](./scale-account-throughput.md#handling-rate-limiting-429-errors)을 처리하기 위해 Azure Cosmos DB의 maven 좌표 `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.2.0`과 함께 [azure-cosmos-cassandra-spark-helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.2.0/jar)라는 다른 라이브러리가 필요합니다. 이 라이브러리는 사용자 지정 연결 팩터리 및 다시 시도 정책 클래스를 포함합니다.

  Azure Cosmos DB의 재시도 정책은 HTTP 상태 코드 429("요청 빈도가 높음") 예외를 처리하도록 구성됩니다. Azure Cosmos DB Cassandra API는 Cassandra 네이티브 프로토콜에서 이러한 예외를 오버로드된 오류로 변환하고, back-off를 사용하여 다시 시도할 수 있습니다. Azure Cosmos DB는 프로비전된 처리량 모델을 사용하므로 수신/송신 요금이 증가하는 경우 요청 속도 제한 예외가 발생합니다. 재시도 정책은 일시적으로 컨테이너에 할당된 처리량을 초과하는 데이터 급증에 대해 spark 작업을 보호합니다. Spark 3.x 커넥터를 사용하는 경우 이 라이브러리를 구현하지 않아도 됩니다. 

  > [!NOTE] 
  > 재시도 정책은 순간적인 급증에 대해서만 spark 작업을 보호할 수 있습니다. 워크로드를 실행하는 데 필요한 충분한 RU를 구성하지 않은 경우 다시 시도 정책이 적용되지 않고 재시도 정책 클래스에서 예외를 다시 throw합니다.

* **Azure Cosmos DB 계정 연결 세부 정보:** Azure Cassandra API 계정 이름, 계정 엔드포인트 및 키입니다.

## <a name="optimizing-spark-connector-throughput-configuration"></a>Spark 커넥터 처리량 구성 최적화 

다음 섹션에는 Cassandra용 Spark 커넥터를 사용하여 처리량을 제어하기 위한 모든 관련 매개 변수가 나와 있습니다. Spark 작업에 대한 처리량을 최대화하기 위해 매개 변수를 최적화하려면 너무 많은 제한 및 백오프를 방지하기 위해(처리량도 낮아질 수 있음) `spark.cassandra.output.concurrent.writes`, `spark.cassandra.concurrent.reads` 및 `spark.cassandra.input.reads_per_sec` 구성을 올바르게 구성해야 합니다.

이러한 구성의 최적 값은 다음 4가지 요소에 따라 다릅니다.

-   데이터를 수집하는 테이블에 대해 구성된 처리량(요청 단위)
- Spark 클러스터의 작업자 수
-   Spark 작업에 대해 구성된 실행기 수(Spark 버전에 따라 `spark.cassandra.connection.connections_per_executor_max` 또는 `spark.cassandra.connection.remoteConnectionsPerExecutor`를 사용하여 제어할 수 있음)
-   동일한 데이터 센터에 배치된 경우 cosmos DB에 대한 각 요청의 평균 대기 시간. 이 값을 쓰기의 경우 10밀리초, 읽기의 경우 3밀리초로 가정합니다.

예를 들어 5개의 작업자가 있고 `spark.cassandra.output.concurrent.writes` 값이 1이고, `spark.cassandra.connection.remoteConnectionsPerExecutor` 값이 1이면 테이블에 동시에 쓰는 5개의 작업자가 있는 것입니다(각각 스레드 1개). 단일 쓰기를 수행하는 데 10밀리초가 걸리면 스레드당, 초당 100개의 요청(1000밀리초를 10으로 나눈 값)을 보낼 수 있습니다. 5개 작업자를 사용하는 경우 초당 500개 쓰기가 됩니다. 쓰기당 5개 RU(요청 단위)인 평균 비용에서 대상 테이블에는 최소 2500개(초당 5개 RU x 500개 쓰기)의 요청 단위가 프로비저닝되어야 합니다.

실행기 수를 늘리면 지정된 작업의 스레드 수가 증가하여 처리량이 증가할 수 있습니다. 그러나 이러한 상황이 미치는 정확한 영향은 작업에 따라 달라질 수 있으며, 작업자 수로 처리량을 제어하는 것이 좀 더 확실합니다. 프로파일링하여 지정된 요청에 대한 정확한 비용을 확인함으로써 RU(요청 단위) 요금도 알 수 있습니다. 이렇게 하면 테이블 또는 키스페이스에 대한 처리량을 프로비저닝할 때 좀 더 정확하게 작업할 수 있습니다. 요청 수준별로 요청 단위 요금을 가져오는 방법을 이해하려면 [여기](./find-request-unit-charge-cassandra.md)의 문서를 살펴보세요. 

### <a name="scaling-throughput-in-the-database"></a>데이터베이스의 처리량 확장

Cassandra Spark 커넥터는 Azure Cosmos DB의 처리량을 매우 효율적으로 포화시킵니다. 결과적으로 다시 시도가 유효하더라도 속도 제한 관련 오류를 방지하기 위해 테이블 또는 키스페이스 수준에서 프로비저닝된 충분한 처리량(RU)이 있는지 확인해야 합니다. 주어진 테이블 또는 키스페이스에서 최소 400RU 설정으로는 충분하지 않습니다. 최소 처리량 구성 설정에서도 Spark 커넥터는 약 **6000개 요청 단위** 이상에 해당하는 속도로 쓸 수 있습니다.

Spark를 사용한 데이터 이동에 필요한 RU 설정이 정상 상태 워크로드에 필요한 것보다 높은 경우 Azure Cosmos DB에서 처리량을 체계적으로 확장 및 축소하여 주어진 기간 동안 워크로드의 요구 사항을 충족할 수 있습니다. 프로그래밍 방식 및 동적으로 확장하기 위한 다양한 옵션을 이해하려면 [Cassandra API의 탄력적 크기 조정](scale-account-throughput.md)에 대한 문서를 읽어보세요. 

> [!NOTE]
> 위의 지침에서는 데이터가 균일하게 분포되어 있다고 가정합니다. 데이터가 상당히 많이 기울어진 경우(즉, 동일한 파티션 키 값에 대한 읽기/쓰기 수가 비정상적으로 많음) 테이블에 많은 수의 [요청 단위](../request-units.md)가 프로비저닝된 경우에도 여전히 병목 현상이 발생할 수 있습니다. 요청 단위는 실제 파티션 간에 균등하게 분할되며 과도한 데이터 기울이기로 인해 단일 파티션에 대한 요청 병목 현상이 발생할 수 있습니다.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Spark 커넥터 처리량 구성 매개 변수

다음 표에서는 커넥터에서 제공된 Azure Cosmos DB Cassandra API 관련 처리량 구성 매개 변수를 나열합니다. 모든 구성 매개 변수의 자세한 목록은 Spark Cassandra 커넥터 GitHub 리포지토리의 [구성 참조](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) 페이지를 참조하세요.

| **속성 이름** | **기본값** | **설명** |
|---------|---------|---------|
| spark.cassandra.output.batch.size.rows |  1 |단일 일괄 처리당 행 수입니다. 이 매개 변수를 1로 설정합니다. 이 매개 변수는 과도한 워크로드에 대한 높은 처리량을 달성하는 데 사용됩니다. |
| spark.cassandra.connection.connections_per_executor_max(Spark 2.x) spark.cassandra.connection.remoteConnectionsPerExecutor(Spark 3.x)  | 없음 | 실행기별 노드당 연결의 최대 수입니다. 10*n은 n개 노드 Cassandra 클러스터의 노드당 10개의 연결과 같습니다. 따라서 5개 노드 Cassandra 클러스터에 대한 실행기별 노드당 5개의 연결이 필요한 경우 이 구성을 25로 설정해야 합니다. 이 값을 병렬 처리 수준 또는 spark 작업이 구성된 실행기 수에 따라 수정합니다.   |
| spark.cassandra.output.concurrent.writes  |  100 | 실행기당 발생할 수 있는 병렬 쓰기 수를 정의합니다. "batch.size.rows"를 1로 설정했으므로 이 값을 적절하게 크기 조정해야 합니다. 이 값을 병렬 처리 수준 또는 워크로드에 대해 달성하려는 처리량에 따라 수정합니다. |
| spark.cassandra.concurrent.reads |  512 | 실행기당 발생할 수 있는 병렬 읽기 수를 정의합니다. 이 값을 병렬 처리 수준 또는 워크로드에 대해 달성하려는 처리량에 따라 수정합니다.  |
| spark.cassandra.output.throughput_mb_per_sec  | 없음 | 실행기당 총 쓰기 처리량을 정의합니다. 이 매개 변수는 spark 작업 처리량의 상한 및 Cosmos 컨테이너의 프로비저닝된 처리량을 기준으로 사용될 수 있습니다.   |
| spark.cassandra.input.reads_per_sec| 없음   | 실행기당 총 읽기 처리량을 정의합니다. 이 매개 변수는 spark 작업 처리량의 상한 및 Cosmos 컨테이너의 프로비저닝된 처리량을 기준으로 사용될 수 있습니다.  |
| spark.cassandra.output.batch.grouping.buffer.size |  1000  | Cassandra API에 보내기 전에 메모리에 저장할 수 있는 단일 spark 작업당 일괄 처리 수를 정의합니다. |
| spark.cassandra.connection.keep_alive_ms | 60000 | 사용되지 않는 연결을 사용할 수 있는 기간을 정의합니다. | 

spark 작업에 대해 예상하는 워크로드 및 Cosmos DB 계정에 대해 프로비전한 처리량에 따라 처리량 및 이러한 매개 변수의 병렬 처리 수준을 조정합니다.


## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Spark에서 Azure Cosmos DB Cassandra API에 연결

### <a name="cqlsh"></a>cqlsh
다음 명령은 cqlsh에서 Azure CosmosDB Cassandra API에 연결하는 방법을 자세히 설명합니다.  Spark에서 샘플을 통해 실행할 때 유효성 검사에 유용합니다.<br>
**Linux/Unix/Mac에서:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1. Azure Databricks
아래 문서에서는 Azure Databricks 클러스터 프로비전, Azure Cosmos DB Cassandra API에 연결을 위한 클러스터 구성 및 DDL 작업, DML 작업 등을 다루는 몇 가지 샘플 노트북을 설명합니다.<BR>
[Azure Databricks에서 Azure Cosmos DB Cassandra API를 사용하여 작업](spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2. Azure HDInsight-Spark
아래 문서에서는 HDinsight-Spark 서비스, 프로비전, Azure Cosmos DB Cassandra API에 연결을 위한 클러스터 구성 및 DDL 작업, DML 작업 등을 다루는 몇 가지 샘플 노트북을 설명합니다.<BR>
[Azure HDInsight-Spark에서 Azure Cosmos DB Cassandra API를 사용하여 작업](spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3. 일반적인 Spark 환경
위의 섹션은 Azure Spark 기반 PaaS 서비스에 관련되었던 반면 이 섹션에서는 일반적인 Spark 환경을 다룹니다.  커넥터 종속성, 가져오기 및 Spark 세션 구성은 아래에서 자세히 설명됩니다. "다음 단계" 섹션에서는 DDL 작업, DML 작업 등에 대한 코드 샘플을 다룹니다.  

#### <a name="connector-dependencies"></a>커넥터 종속성:

1. maven 좌표를 추가하여 [Spark용 Cassandra 커넥터](connect-spark-configuration.md#dependencies-for-connectivity) 가져오기
2. Cassandra API의 [Azure Cosmos DB 도우미 라이브러리](connect-spark-configuration.md#dependencies-for-connectivity)에 대한 maven 좌표 추가

#### <a name="imports"></a>가져오기:

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Spark 세션 구성:

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
//spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10") // Spark 2.x
spark.conf.set("spark.cassandra.connection.remoteConnectionsPerExecutor", "10") // Spark 3.x
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>다음 단계

다음 문서에서는 Azure Cosmos DB Cassandra API와 Spark 통합을 보여줍니다. 
 
* [DDL 작업](spark-ddl-operations.md)
* [만들기/삽입 작업](spark-create-operations.md)
* [읽기 작업](spark-read-operation.md)
* [Upsert 작업](spark-upsert-operations.md)
* [삭제 작업](spark-delete-operation.md)
* [집계 작업](spark-aggregation-operations.md)
* [테이블 복사 작업](spark-table-copy-operations.md)