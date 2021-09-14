---
title: Apache Spark를 사용하여 Azure Managed Instance for Apache Cassandra로 마이그레이션
description: Apache Spark를 사용하여 Azure Managed Instance for Apache Cassandra로 마이그레이션하는 방법을 알아봅니다.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: tutorial
ms.date: 08/17/2021
ms.openlocfilehash: 5d78161f73dfa1927483d7c6f8bad3ce925b3631
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426871"
---
# <a name="migrate-to-azure-managed-instance-for-apache-cassandra-using-apache-spark"></a>Apache Spark를 사용하여 Azure Managed Instance for Apache Cassandra로 마이그레이션

> [!IMPORTANT]
> Apache Cassandra용 Azure Managed Instance는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

가능하면 [하이브리드 클러스터](configure-hybrid-cluster.md)를 구성하여 기존 클러스터에서 Azure Managed Instance for Apache Cassandra로 데이터를 마이그레이션할 때 Apache Cassandra 네이티브 복제를 사용하는 것이 좋습니다. 이 방법에서는 Apache Cassandra의 가십 프로토콜을 사용하여 원본 데이터 센터에서 새로운 관리형 인스턴스 데이터 센터로 데이터를 복제합니다. 그러나 원본 데이터베이스 버전이 호환되지 않거나 하이브리드 클러스터를 설정할 수 없는 경우가 있을 수 있습니다. 

이 자습서에서는 Cassandra Spark 커넥터 및 Azure Databricks for Apache Spark를 사용하여 오프라인 방식으로 Azure Managed Instance for Apache Cassandra로 데이터를 마이그레이션하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

* [Azure Portal](create-cluster-portal.md) 또는 [Azure CLI](create-cluster-cli.md)를 사용하여 Azure Managed Instance for Apache Cassandra 클러스터를 프로비저닝하고 [CQLSH를 사용하여 클러스터에 연결](./create-cluster-portal.md#connecting-to-your-cluster)할 수 있는지 확인합니다.

* [관리형 Cassandra VNet 내에서 Azure Databricks 계정을 프로비저닝](deploy-cluster-databricks.md)합니다. 또한 원본 Cassandra 클러스터에 대한 네트워크 액세스 권한이 있는지 확인합니다.

* 원본 Cassandra 데이터베이스에서 대상 Cassandra Managed Instance 데이터베이스로 키스페이스/테이블 구성표를 이미 마이그레이션했는지 확인합니다.


## <a name="provision-an-azure-databricks-cluster"></a>Azure Databricks 클러스터 프로비전

Spark 3.0을 지원하는 Databricks Runtime 버전 7.5를 선택하는 것이 좋습니다.

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-runtime.png" alt-text="Databricks Runtime 버전 찾기를 보여 주는 스크린샷":::

## <a name="add-dependencies"></a>종속성 추가

Apache Spark Cassandra 커넥터 라이브러리를 클러스터에 추가하여 네이티브 및 Azure Cosmos DB Cassandra 엔드포인트 모두에 연결합니다. 클러스터에서 **라이브러리** > **새로 설치** > **Maven** 을 선택한 다음 Maven 좌표에 `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0`을 추가합니다.

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-search-packages.png" alt-text="Databricks에서 Maven 패키지 검색을 보여 주는 스크린샷":::

**설치** 를 선택한 다음 설치가 완료되면 클러스터를 다시 시작합니다.

> [!NOTE]
> Cassandra Connector 라이브러리가 설치된 후 Databricks 클러스터를 다시 시작해야 합니다.

## <a name="create-scala-notebook-for-migration"></a>마이그레이션을 위한 Scala Notebook 만들기

Databricks에서 Scala Notebook을 만듭니다. 원본 및 대상 Cassandra 구성을 해당 자격 증명, 원본 및 대상 키 공간과 테이블로 바꿉니다. 다음 코드를 실행합니다.

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val sourceCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val targetCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.connection.remoteConnectionsPerExecutor" -> "10",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from source Cassandra
val DFfromSourceCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(sourceCassandra)
  .load
  
//Write to target Cassandra
DFfromSourceCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(targetCassandra)
  .mode(SaveMode.Append) // only required for Spark 3.x
  .save
```

> [!NOTE]
> 각 행의 `writetime`을 유지하거나 소급 적용해야 하는 경우 [실시간 마이그레이션](dual-write-proxy-migration.md) 문서를 참조하세요. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 Apache Cassandra용 Azure Managed Instance 리소스 관리](manage-resources-cli.md)