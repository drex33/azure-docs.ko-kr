---
title: Apache Spark 및 이중 쓰기 프록시를 사용하여 Azure Managed Instance for Apache Cassandra로 실시간 마이그레이션
description: Apache Spark 및 이중 쓰기 프록시를 사용하여 Azure Managed Instance for Apache Cassandra로 마이그레이션하는 방법을 알아봅니다.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 061fd0f1244221aabb6fa37a84924be653fafbaa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131032459"
---
# <a name="live-migration-to-azure-managed-instance-for-apache-cassandra-by-using-a-dual-write-proxy"></a>이중 쓰기 프록시를 사용하여 Azure Managed Instance for Apache Cassandra로 실시간 마이그레이션

가능하면 [하이브리드 클러스터](configure-hybrid-cluster.md)를 구성하여 기존 클러스터에서 Azure Managed Instance for Apache Cassandra로 데이터를 마이그레이션할 때 Apache Cassandra 네이티브 기능을 사용하는 것이 좋습니다. 이 기능은 Apache Cassandra의 가십 프로토콜을 사용하여 원본 데이터 센터에서 새로운 관리형 인스턴스 데이터 센터로 원활하게 데이터를 복제합니다. 그러나 원본 데이터베이스 버전이 호환되지 않거나 하이브리드 클러스터를 설정할 수 없는 경우가 있을 수 있습니다. 

이 자습서에서는 [이중 쓰기 프록시](https://github.com/Azure-Samples/cassandra-proxy) 및 Apache Spark를 사용하여 실시간으로 데이터를 Azure Managed Instance for Apache Cassandra로 마이그레이션하는 방법을 설명합니다. 이 방법의 이점은 다음과 같습니다.

- **최소한의 애플리케이션 변경**. 프록시가 최소한의 구성 변경으로 또는 구성 변경 없이 애플리케이션 코드의 연결을 허용할 수 있습니다. 모든 요청을 원본 데이터베이스에 라우팅하고 비동기 방식으로 쓰기를 보조 대상으로 라우팅합니다. 
- **클라이언트 유선 프로토콜 종속성**. 이 방법은 백 엔드 리소스나 내부 프로토콜에 종속되지 않으므로 Apache Cassandra 유선 프로토콜을 구현하는 모든 원본 또는 대상 Cassandra 시스템에서 사용할 수 있습니다.

다음 이미지는 이 방법을 설명합니다.

:::image type="content" source="./media/migration/live-migration.gif" alt-text="데이터를 Azure Managed Instance for Apache Cassandra로 실시간 마이그레이션하는 방법을 보여 주는 애니메이션" border="false":::

## <a name="prerequisites"></a>필수 조건

* [Azure Portal](create-cluster-portal.md) 또는 [Azure CLI](create-cluster-cli.md)를 사용하여 Azure Managed Instance for Apache Cassandra 클러스터를 프로비저닝합니다. [CQLSH를 사용하여 클러스터에 연결](./create-cluster-portal.md#connecting-to-your-cluster)할 수 있는지 확인합니다.

* [관리형 Cassandra 가상 네트워크 내에서 Azure Databricks 계정을 프로비저닝](deploy-cluster-databricks.md)합니다. 계정에 원본 Cassandra 클러스터에 대한 네트워크 액세스 권한이 있는지 확인합니다. 기록 데이터를 로드하기 위해 이 계정에 Spark 클러스터를 만듭니다.

* 원본 Cassandra 데이터베이스에서 대상 Cassandra 관리형 인스턴스 데이터베이스로 키스페이스/테이블 구성표를 이미 마이그레이션했는지 확인합니다.

## <a name="provision-a-spark-cluster"></a>Spark 클러스터 프로비저닝

Spark 3.0을 지원하는 Azure Databricks 런타임 버전 7.5를 선택하는 것이 좋습니다.

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-runtime.png" alt-text="Azure Databricks Runtime 버전을 찾는 방법을 보여 주는 스크린샷":::

## <a name="add-spark-dependencies"></a>Spark 종속성 추가

네이티브 및 Azure Cosmos DB Cassandra 엔드포인트 모두에 연결하려면 Apache Spark Cassandra 커넥터 라이브러리를 클러스터에 추가해야 합니다. 클러스터에서 **라이브러리** > **새로 설치** > **Maven** 을 선택한 다음 Maven 좌표에 `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0`을 추가합니다.

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-search-packages.png" alt-text="Azure Databricks에서 Maven 패키지를 검색하는 방법을 보여 주는 스크린샷":::

**설치** 를 선택한 다음 설치가 완료되면 클러스터를 다시 시작합니다.

> [!NOTE]
> Cassandra 커넥터 라이브러리가 설치된 후 Azure Databricks 클러스터를 다시 시작해야 합니다.

## <a name="install-the-dual-write-proxy"></a>이중 쓰기 프록시 설치

이중 쓰기 중 성능을 최적화하려면 원본 Cassandra 클러스터의 모든 노드에 프록시를 설치하는 것이 좋습니다.

```bash
#assuming you do not have git already installed
sudo apt-get install git 

#assuming you do not have maven already installed
sudo apt install maven

#clone repo for dual-write proxy
git clone https://github.com/Azure-Samples/cassandra-proxy.git

#change directory
cd cassandra-proxy

#compile the proxy
mvn package
```

## <a name="start-the-dual-write-proxy"></a>이중 쓰기 프록시 시작

원본 Cassandra 클러스터의 모든 노드에 프록시를 설치하는 것이 좋습니다. 최소한 다음 명령을 실행하여 각 노드에서 프록시를 시작합니다. `<target-server>`를 대상 클러스터에 있는 노드 중 하나의 IP 또는 서버 주소로 바꿉니다. `<path to JKS file>`을 로컬 .jks 파일 경로로 바꾸고, `<keystore password>`를 해당 암호로 바꿉니다.  

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password>
```

이 방법으로 프록시를 시작하면 다음 조건이 충족된다고 가정합니다.

- 원본 및 대상 엔드포인트의 사용자 이름과 암호가 동일합니다.
- 원본 및 대상 엔드포인트가 SSL(Secure Sockets Layer)을 구현합니다.

원본 및 대상 엔드포인트가 이러한 조건을 충족할 수 없는 경우 계속해서 추가 구성 옵션을 살펴보세요.

### <a name="configure-ssl"></a>SSL 구성

SSL의 경우 기존 키 저장소(예: 원본 클러스터에서 사용하는 키 저장소)를 구현하거나 `keytool`을 사용하여 자체 서명된 인증서를 만들 수 있습니다.

```bash
keytool -genkey -keyalg RSA -alias selfsigned -keystore keystore.jks -storepass password -validity 360 -keysize 2048
```
SSL을 구현하지 않는 경우 원본 또는 대상 엔드포인트에 대해 SSL을 사용하지 않도록 설정할 수도 있습니다. `--disable-source-tls` 또는 `--disable-target-tls` 플래그를 사용합니다.

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --source-port 9042 --target-port 10350 --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password> --disable-source-tls true  --disable-target-tls true 
```

> [!NOTE]
> 프록시를 통해 데이터베이스에 대한 SSL 연결을 설정하는 경우 클라이언트 애플리케이션이 이중 쓰기 프록시에 사용된 것과 동일한 키 저장소와 암호를 사용하는지 확인합니다.

### <a name="configure-the-credentials-and-port"></a>자격 증명 및 포트 구성

기본적으로 원본 자격 증명은 클라이언트 앱에서 전달됩니다. 프록시는 원본 및 대상 클러스터에 연결하는 데 자격 증명을 사용합니다. 앞에서 설명한 것처럼 이 프로세스에서는 원본 및 대상 자격 증명이 동일하다고 가정합니다. 필요한 경우 프록시를 시작할 때 대상 Cassandra 엔드포인트의 사용자 이름과 암호를 다르게 지정할 수 있습니다.

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password>
```

기본 원본 및 대상 포트는 지정되지 않은 경우 9042가 됩니다. 대상 또는 원본 Cassandra 엔드포인트가 다른 포트에서 실행되는 경우 `--source-port` 또는 `--target-port`를 사용하여 다른 포트 번호를 지정할 수 있습니다. 

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar localhost <target-server> --source-port 9042 --target-port 10350 --proxy-jks-file <path to JKS file> --proxy-jks-password <keystore password> --target-username <username> --target-password <password>
```

### <a name="deploy-the-proxy-remotely"></a>원격으로 프록시 배포

클러스터 노드 자체에 프록시를 설치하지 않고 별도의 머신에 설치하려는 경우가 있을 수 있습니다. 이 경우 IP 주소를 `<source-server>`로 지정해야 합니다.

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar <source-server> <destination-server>
```

> [!NOTE]
> 네이티브 Apache Cassandra 클러스터의 모든 노드에서 프록시를 설치 및 실행하지 않으면 애플리케이션 성능에 영향을 주게 됩니다. 클라이언트 드라이버가 클러스터 내의 모든 노드에 대한 연결을 열 수 없습니다. 

### <a name="allow-zero-application-code-changes"></a>제로(0) 애플리케이션 코드 변경 허용

기본적으로 프록시는 포트 29042에서 수신 대기합니다. 이 포트를 가리키도록 애플리케이션 코드를 변경해야 합니다. 그러나 프록시가 수신 대기하는 포트를 변경할 수 있습니다. 다음을 수행하여 애플리케이션 수준 코드 변경 내용을 제거하면 됩니다.

- 원본 Cassandra 서버가 다른 포트에서 실행되도록 합니다.
- 프록시가 표준 Cassandra 포트 9042에서 실행되도록 합니다.

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --proxy-port 9042
```

> [!NOTE]
> 클러스터 노드에 프록시를 설치하면 노드를 다시 시작하지 않아도 됩니다. 그러나 애플리케이션 클라이언트가 많고 프록시를 표준 Cassandra 포트 9042에서 실행하여 애플리케이션 수준 코드가 변경되지 않도록 하려면 [Apache Cassandra 기본 포트](https://cassandra.apache.org/doc/latest/cassandra/faq/#what-ports-does-cassandra-use)를 변경해야 합니다. 그런 다음 클러스터에서 노드를 다시 시작하고 원본 포트를 원본 Cassandra 클러스터에 대해 정의한 새 포트로 구성해야 합니다. 
>
> 다음 예제에서는 포트 3074에서 실행되도록 원본 Cassandra 클러스터를 변경하고 포트 9042에서 클러스터를 시작합니다.
>
>```bash
>java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --proxy-port 9042 --source-port 3074
>``` 

### <a name="force-protocols"></a>프로토콜 강제 적용

프록시에는 원본 엔드포인트가 대상보다 고급인 경우에 필요하고, 그러지 않으면 지원되지 않는 프로토콜 강제 적용 기능이 있습니다. 해당하는 경우 `--protocol-version`과 `--cql-version`을 지정하여 프로토콜이 대상을 따르도록 강제 적용할 수 있습니다.

```bash
java -jar target/cassandra-proxy-1.0-SNAPSHOT-fat.jar source-server destination-server --protocol-version 4 --cql-version 3.11
```

이중 쓰기 프록시가 실행된 후 애플리케이션 클라이언트에서 포트를 변경하고 다시 시작해야 합니다. 또는 해당 방법을 선택한 경우 Cassandra 포트를 변경하고 클러스터를 다시 시작합니다. 그러면 프록시가 대상 엔드포인트로 쓰기 전달을 시작합니다. 프록시 도구에서 사용할 수 있는 [모니터링 및 메트릭](https://github.com/Azure-Samples/cassandra-proxy#monitoring)에 대해 알아볼 수 있습니다. 

## <a name="run-the-historical-data-load"></a>기록 데이터 로드 실행

데이터를 로드하려면 Azure Databricks 계정에 Scala Notebook을 만듭니다. 원본 및 대상 Cassandra 구성을 해당 자격 증명으로 바꾸고, 원본 및 대상 키스페이스와 테이블을 바꿉니다. 각 테이블에 대한 변수를 필요한 만큼 다음 샘플에 더 추가한 후 실행합니다. 애플리케이션에서 이중 쓰기 프록시로 요청을 보내기 시작하면 기록 데이터를 마이그레이션할 수 있습니다. 

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
    "spark.cassandra.connection.ssl.enabled" -> "true",
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
    "spark.cassandra.connection.remoteConnectionsPerExecutor" -> "1",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//set timestamp to ensure it is before read job starts
val timestamp: Long = System.currentTimeMillis / 1000

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
  .option("writetime", timestamp)
  .mode(SaveMode.Append)
  .save
```

> [!NOTE]
> 위의 Scala 샘플에서 `timestamp`는 원본 테이블의 모든 데이터를 읽기 전 현재 시간으로 설정되는 것을 확인할 수 있습니다. `writetime`은 이 소급 적용된 타임스탬프로 설정됩니다. 따라서 기록 데이터가 대상 엔드포인트에 로드될 때 기록되는 레코드는 기록 데이터를 읽는 동안 이중 쓰기 프록시의 이후 타임스탬프로 제공되는 업데이트를 덮어쓸 수 없습니다.
>
> 어떤 이유로든 ‘정확한’ 타임스탬프를 유지해야 하는 경우 [이 샘플](https://github.com/Azure-Samples/cassandra-migrator)처럼 타임스탬프를 유지하는 기록 데이터 마이그레이션 방법을 사용해야 합니다. 

## <a name="validate-the-source-and-target"></a>원본 및 대상의 유효성 검사

기록 데이터 로드가 완료되면 데이터베이스가 동기화되고 중단할 준비가 된 것입니다. 그러나 최종적으로 중단하기 전에 원본과 대상의 유효성을 검사하여 요청 결과가 일치하는지 확인하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 Apache Cassandra용 Azure Managed Instance 리소스 관리](manage-resources-cli.md)
