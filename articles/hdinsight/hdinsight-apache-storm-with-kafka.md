---
title: '자습서: Apache Storm 및 Apache Kafka - Azure HDInsight'
description: HDInsight에서 Apache Storm 및 Apache Kafka를 사용하여 스트리밍 파이프라인을 만드는 방법을 알아봅니다. 이 자습서에서는 KafkaBolt 및 KafkaSpout 구성 요소를 사용하여 Kafka에서 데이터를 스트리밍합니다.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: bf296c7de096fe5f8330951bd47628c5147d4ec7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339671"
---
# <a name="tutorial-use-apache-storm-with-apache-kafka-on-hdinsight"></a>자습서: HDInsight에서 Apache Storm 및 Apache Kafka 사용

이 자습서에서는 HDInsight의 [Apache Kafka](https://kafka.apache.org/)를 사용하여 [Apache Storm](https://storm.apache.org/) 토폴로지를 통해 데이터를 읽고 쓰는 방법을 보여줍니다. 또한 이 자습서에서는 데이터를 Storm 클러스터의 [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) 호환 스토리지에 유지하는 방법을 보여줍니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Storm 및 Kafka
> * 코드 이해
> * Kafka 및 Storm 클러스터 만들기
> * 토폴로지 작성
> * 토폴로지 구성
> * Kafka 토픽 만들기
> * 토폴로지 시작
> * 토폴로지 중지
> * 리소스 정리

## <a name="prerequisites"></a>필수 구성 요소

* Kafka 토픽 생성 방법 이해. 자세한 내용은 [HDInsight의 Kafka 빠른 시작](./kafka/apache-kafka-get-started.md) 문서를 참조하세요.

* Storm 솔루션(토폴로지) 빌드 및 배포 방법 이해. 특히, [Apache Storm Flux](https://storm.apache.org/releases/current/flux.html) 프레임워크를 사용하는 토폴로지에 대한 기본 지식이 필요합니다. 자세한 내용은 [Java에서 Apache Storm 토폴로지 만들기](./storm/apache-storm-develop-java-topology.md) 문서를 참조하세요.

* [Java JDK 1.8](https://www.oracle.com/technetwork/pt/java/javase/downloads/jdk8-downloads-2133151.html) 이상 \- HDInsight 3.5 이상에는 Java 8이 필요합니다.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* SSH 클라이언트(`ssh` 및 `scp` 명령 필요) - 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조합니다.

Java 및 JDK를 설치할 때 사용자의 개발 워크스테이션에 다음 환경 변수를 설정할 수 있습니다. 하지만 변수가 존재하며 시스템에 대한 올바른 값을 포함하는지 확인해야 합니다.

* `JAVA_HOME` - JDK가 설치된 디렉터리를 가리켜야 합니다.
* `PATH` - 다음 경로를 포함해야 합니다.
  
    * `JAVA_HOME`(또는 이와 동등한 경로)
    * `JAVA_HOME\bin`(또는 이와 동등한 경로)
    * Maven이 설치된 디렉터리

> [!IMPORTANT]  
> 이 문서의 단계를 수행하려면 HDInsight의 Storm과 HDInsight의 Kafka 클러스터를 모두 포함하는 Azure 리소스 그룹이 필요합니다. 이러한 클러스터는 모두 Azure Virtual Network에 있으며, 여기서는 Storm 클러스터와 Kafka 클러스터 간에 직접 통신할 수 있습니다.
> 
> 사용자의 편의를 위해, 이 문서는 필요한 모든 Azure 리소스를 만들 수 있는 템플릿에 연결되어 있습니다. 
>
> 가상 네트워크에서 HDInsight 사용에 대한 자세한 내용은 [HDInsight용 가상 네트워크 계획](hdinsight-plan-virtual-network-deployment.md) 문서를 참조하세요.

## <a name="storm-and-kafka"></a>Storm 및 Kafka

Apache Storm은 Apache Kafka로 작업하기 위한 몇 가지 구성 요소를 제공합니다. 이 자습서에서는 다음 구성 요소가 사용됩니다.

* `org.apache.storm.kafka.KafkaSpout`: 이 구성 요소는 Kafka에서 데이터를 읽습니다. 이 구성 요소는 다음 구성 요소에 의존합니다.

    * `org.apache.storm.kafka.SpoutConfig`: Spout 구성 요소에 대한 구성을 제공합니다.

    * `org.apache.storm.spout.SchemeAsMultiScheme` 및 `org.apache.storm.kafka.StringScheme`: Kafka의 데이터가 Storm 튜플로 변환되는 방식입니다.

* `org.apache.storm.kafka.bolt.KafkaBolt`: 이 구성 요소는 Kafka 데이터를 씁니다. 이 구성 요소는 다음 구성 요소에 의존합니다.

    * `org.apache.storm.kafka.bolt.selector.DefaultTopicSelector`: 쓸 토픽을 설명합니다.

    * `org.apache.kafka.common.serialization.StringSerializer`: 데이터를 문자열 값으로 직렬화하도록 Bolt를 구성합니다.

    * `org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper`: Storm 토폴로지 내에서 사용되는 튜플 데이터 구조에서 Kafka에 저장되는 필드로 매핑합니다.

이러한 구성 요소는 `org.apache.storm : storm-kafka` 패키지에서 사용할 수 있습니다. Storm 버전과 일치하는 패키지 버전을 사용합니다. HDInsight 3.6의 경우 Storm 버전은 1.1.0입니다.
또한 추가 Kafka 구성 요소를 포함하는 `org.apache.kafka : kafka_2.10` 패키지도 필요합니다. Kafka 버전과 일치하는 패키지 버전을 사용합니다. HDInsight 3.6의 경우 Kafka 버전은 1.1.1입니다.

다음 XML은 [Apache Maven](https://maven.apache.org/) 프로젝트에 대한 `pom.xml`의 종속성 선언입니다.

```xml
<!-- Storm components for talking to Kafka -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-kafka</artifactId>
    <version>1.1.0</version>
</dependency>
<!-- needs to be the same Kafka version as used on your cluster -->
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka_2.10</artifactId>
    <version>1.1.1</version>
    <!-- Exclude components that are loaded from the Storm cluster at runtime -->
    <exclusions>
        <exclusion>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
        </exclusion>
        <exclusion>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
        </exclusion>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

## <a name="understanding-the-code"></a>코드 이해

이 문서에 사용된 코드는 [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka)에서 사용할 수 있습니다.

이 자습서와 함께 다음 2가지 토폴로지가 제공됩니다.

* Kafka-writer: 임의 문장을 생성한 후 Kafka에 저장합니다.

* Kafka-reader: Kafka에서 데이터를 읽고 Storm 클러스터의 HDFS 호환 파일 저장소에 저장합니다.

    > [!WARNING]  
    > Storm이 HDInsight에서 사용되는 HDFS 호환 스토리지를 사용할 수 있도록 하려면 스크립트 작업이 필요합니다. 이 스크립트는 여러 개의 jar 파일을 Storm에 대한 `extlib` 경로에 설치합니다. 이 자습서의 템플릿은 클러스터 생성 중에 자동으로 이 스크립트를 사용합니다.
    >
    > Storm 클러스터를 만드는 데 이 문서의 템플릿을 사용하지 않을 경우 클러스터에 스크립트 작업을 수동으로 적용해야 합니다.
    >
    > 스크립트 작업은 [https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh](https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh)에 있으며, Storm 클러스터의 supervisor 및 nimbus 노드에 적용됩니다. 스크립트 작업 사용에 대한 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md) 문서를 참조하세요.

토폴로지는 [Flux](https://storm.apache.org/releases/current/flux.html)를 사용하여 정의됩니다. Flux는 Storm 0.10.x에서 소개되었으며, 토폴로지 구성을 코드에서 분리할 수 있습니다. Flux 프레임워크를 사용하는 토폴로지의 경우 토폴로지는 YAML 파일에 정의됩니다. YAML 파일은 토폴로지의 일부로 포함할 수 있습니다. 또는 토폴로지를 전송할 때 독립 실행형 파일로 사용할 수도 있습니다. Flux는 런타임 시 변수 대체도 지원하며, 이 예제에서 사용됩니다.

이러한 토폴로지에 대해 런타임에 다음 매개 변수가 설정됩니다.

* `${kafka.topic}`: 토폴리지에서 읽고 쓰는 Kafka 토픽의 이름입니다.

* `${kafka.broker.hosts}`: Kafka broker가 실행되는 호스트입니다. broker 정보는 Kafka에 쓸 때 KafkaBolt에서 사용합니다.

* `${kafka.zookeeper.hosts}`: Kafka 클러스터에서 Zookeeper가 실행되는 호스트입니다.

* `${hdfs.url}`: HDFSBolt 구성 요소에 대한 파일 시스템 URL입니다. 데이터를 Azure Storage 계정에 쓸지 또는 Azure Data Lake Storage에 쓸지를 나타냅니다.

* `${hdfs.write.dir}`: 데이터가 기록된 디렉터리입니다.

Flux 토폴로지에 대한 자세한 내용은 [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html)을 참조하세요.

### <a name="kafka-writer"></a>Kafka-writer

Kafka-writer 토폴로지에서 Kafka Bolt 구성 요소는 두 개의 문자열 값을 매개 변수로 사용합니다. 이러한 매개 변수는 Bolt가 Kafka에 __key__ 및 __message__ 값으로 전송하는 튜플 필드를 나타냅니다. key는 Kafka에서 데이터를 분할하는 데 사용됩니다. message는 저장되는 데이터입니다.

이 예제에서 `com.microsoft.example.SentenceSpout` 구성 요소는 2개의 필드 `key` 및 `message`를 포함하는 튜플을 내보냅니다. Kafka Bolt는 이러한 필드를 추출하고 해당 데이터를 Kafka로 보냅니다.

이러한 필드는 이름 `key` 및 `message`를 사용하지 않아도 합니다. 이러한 이름은 매핑을 보다 쉽게 이해하도록 하기 위해 이 필드에서 사용되었습니다.

다음 YAML은 Kafka-writer 구성 요소에 대한 정의입니다.

```yaml
# kafka-writer
---

# topology definition
# name to be used when submitting
name: "kafka-writer"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Topic selector for KafkaBolt
  - id: "topicSelector"
    className: "org.apache.storm.kafka.bolt.selector.DefaultTopicSelector"
    constructorArgs:
      - "${kafka.topic}"

  # Mapper for KafkaBolt
  - id: "kafkaMapper"
    className: "org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper"
    constructorArgs:
      - "key"
      - "message"

  # Producer properties for KafkaBolt
  - id: "producerProperties"
    className: "java.util.Properties"
    configMethods:
      - name: "put"
        args:
          - "bootstrap.servers"
          - "${kafka.broker.hosts}"
      - name: "put"
        args:
          - "acks"
          - "1"
      - name: "put"
        args:
          - "key.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
      - name: "put"
        args:
          - "value.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
 

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "com.microsoft.example.SentenceSpout"
    parallelism: 8

# Bolt definitions
bolts:
  - id: "kafka-bolt"
    className: "org.apache.storm.kafka.bolt.KafkaBolt"
    parallelism: 8
    configMethods:
    - name: "withProducerProperties"
      args: [ref: "producerProperties"]
    - name: "withTopicSelector"
      args: [ref: "topicSelector"]
    - name: "withTupleToKafkaMapper"
      args: [ref: "kafkaMapper"]

# Stream definitions

streams:
  - name: "spout --> kafka" # Streams data from the sentence spout to the Kafka bolt
    from: "sentence-spout"
    to: "kafka-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="kafka-reader"></a>Kafka-reader

Kafka-reader 토폴로지에서 Spout 구성 요소는 Kafka의 데이터를 문자열 값으로 읽습니다. 그런 후에 해당 데이터는 로깅 구성 요소에 의해 Storm 로그에 기록되고, HDFS Bolt 구성 요소에 의해 Storm 클러스터에 대한 HDFS 호환 파일 시스템에 기록됩니다.

```yaml
# kafka-reader
---

# topology definition
# name to be used when submitting
name: "kafka-reader"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Convert data from Kafka into string tuples in storm
  - id: "stringScheme"
    className: "org.apache.storm.kafka.StringScheme"
  - id: "stringMultiScheme"
    className: "org.apache.storm.spout.SchemeAsMultiScheme"
    constructorArgs:
      - ref: "stringScheme"

  - id: "zkHosts"
    className: "org.apache.storm.kafka.ZkHosts"
    constructorArgs:
      - "${kafka.zookeeper.hosts}"

  # Spout configuration
  - id: "spoutConfig"
    className: "org.apache.storm.kafka.SpoutConfig"
    constructorArgs:
      # brokerHosts
      - ref: "zkHosts"
      # topic
      - "${kafka.topic}"
      # zkRoot
      - ""
      # id
      - "readerid"
    properties:
      - name: "scheme"
        ref: "stringMultiScheme"

    # How often to sync files to HDFS; every 1000 tuples.
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "KB"

  # File format; read the directory from filters at run time, and use a .txt extension when writing.
  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  # Internal file format; fields delimited by `|`.
  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "kafka-spout"
    className: "org.apache.storm.kafka.KafkaSpout"
    constructorArgs:
      - ref: "spoutConfig"
    # Set to the number of partitions for the topic
    parallelism: 8

# Bolt definitions
bolts:
  - id: "logger-bolt"
    className: "com.microsoft.example.LoggerBolt"
    parallelism: 1
  
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
    parallelism: 1

# Stream definitions

streams:
  # Stream data to log
  - name: "kafka --> log" # name isn't used (placeholder for logging, UI, etc.)
    from: "kafka-spout"
    to: "logger-bolt"
    grouping:
      type: SHUFFLE
  
  # stream data to file
  - name: "kafka --> hdfs"
    from: "kafka-spout"
    to: "hdfs-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="property-substitutions"></a>속성 대체

이 프로젝트에는 토폴로지가 사용되는 매개 변수를 전달하는 데 사용하는 `dev.properties`라는 파일이 포함되어 있습니다. 이 파일은 다음 속성을 정의합니다.

| dev.properties 파일 | Description |
| --- | --- |
| `kafka.zookeeper.hosts` | Kafka 클러스터에 대한 [Apache ZooKeeper](https://zookeeper.apache.org/) 호스트입니다. |
| `kafka.broker.hosts` | Kafka 브로커 호스트(작업자 노드를)입니다. |
| `kafka.topic` | 토폴로지에서 사용되는 Kafka 토픽입니다. |
| `hdfs.write.dir` | Kafka-reader 토폴로지가 쓰는 디렉터리입니다. |
| `hdfs.url` | Storm 클러스터에서 사용되는 파일 시스템입니다. Azure Storage 계정의 경우 `wasb://` 값을 사용합니다. Azure Data Lake Storage Gen2의 경우 `abfs://` 값을 사용합니다. Azure Data Lake Storage Gen1의 경우 `adl://` 값을 사용합니다. |

## <a name="create-the-clusters"></a>클러스터 만들기

HDInsight의 Apache Kafka는 공용 인터넷을 통한 액세스를 Kafka broker에 제공하지 않습니다. Kafka를 사용하는 경우 동일한 Azure 가상 네트워크에 있어야 합니다. 이 자습서에서는 Kafka 클러스터와 Storm 클러스터가 동일한 Azure 가상 네트워크에 있습니다. 

다음 다이어그램은 Storm과 Kafka 사이의 통신 흐름을 보여줍니다.

:::image type="content" source="./media/hdinsight-apache-storm-with-kafka/apache-storm-kafka-vnet.png" alt-text="Azure 가상 네트워크에 있는 Storm 및 Kafka 클러스터 다이어그램":::

> [!NOTE]  
> SSH 및 [Apache Ambari](https://ambari.apache.org/)와 같은 클러스터의 다른 서비스는 인터넷을 통해 액세스할 수 있습니다. HDInsight에서 사용할 수 있는 공용 포트에 대한 자세한 내용은 [HDInsight에서 사용하는 포트 및 URI](hdinsight-hadoop-port-settings-for-services.md)를 참조하세요.

Azure Virtual Network를 만든 후 그 안에 Kafka 및 Storm 클러스터를 만들려면 다음 단계를 사용합니다.

1. Azure에 로그인하고 Azure Portal에서 템플릿을 열려면 다음 단추를 사용합니다.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-storm-java-kafka%2Fmaster%2Fcreate-kafka-storm-clusters-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Azure Resource Manager 템플릿의 위치는 **https://github.com/Azure-Samples/hdinsight-storm-java-kafka/blob/master/create-kafka-storm-clusters-in-vnet.json** 입니다. 이 템플릿은 다음 리소스를 만듭니다.

    * Azure 리소스 그룹
    * Azure Virtual Network
    * Azure Storage 계정
    * HDInsight 버전 3.6의 Kafka(작업자 노드 3개)
    * HDInsight 버전 3.6의 Storm(작업자 노드 3개)

   > [!WARNING]  
   > HDInsight에서 Kafka의 사용 가능성을 보장하려면 클러스터에 작업자 노드가 3개 이상 포함되어야 합니다. 이 템플릿은 세 개의 작업자 노드를 포함하는 Kafka 클러스터를 만듭니다.

2. 다음 지침을 사용하여 **사용자 지정 배포** 섹션의 항목을 채웁니다.

   1. 다음 정보를 사용하여 **사용자 지정된 템플릿** 섹션의 항목을 채웁니다.

      | 설정 | 값 |
      | --- | --- |
      | Subscription | Azure 구독 |
      | Resource group | 리소스를 포함하는 리소스 그룹입니다. |
      | 위치 | 리소스가 만들어지는 Azure 지역입니다. |
      | Kafka 클러스터 이름 | Kafka 클러스터의 이름입니다. |
      | Storm 클러스터 이름 | Storm 클러스터의 이름입니다. |
      | 클러스터 로그인 사용자 이름 | 클러스터의 관리 사용자 이름입니다. |
      | 클러스터 로그인 암호 | 클러스터의 관리 사용자 암호입니다. |
      | SSH 사용자 이름 | 클러스터용으로 만들 SSH 사용자입니다. |
      | SSH 암호 | SSH 사용자에 대한 암호입니다. |
   
      :::image type="content" source="./media/hdinsight-apache-storm-with-kafka/storm-kafka-template.png" alt-text="템플릿 매개 변수 그림":::

3. **사용 약관** 을 읽은 다음 **위에 명시된 사용 약관에 동의함** 을 선택합니다.

4. 마지막으로 **대시보드에 고정** 을 선택한 다음 **구매** 를 선택합니다.

> [!NOTE]  
> 클러스터를 만드는 데 최대 20분이 걸릴 수 있습니다.

## <a name="build-the-topology"></a>토폴로지 작성

1. 개발 환경에서 [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka)로부터 프로젝트를 다운로드하고, 명령줄을 열어 다운로드한 위치로 디렉터리를 변경합니다.

2. **hdinsight-storm-java-kafka** 디렉터리에서 다음 명령을 사용하여 프로젝트를 컴파일하고 배포할 패키지를 만듭니다.

   ```bash
   mvn clean package
   ```

    패키지 프로세스는 `target` 디렉터리에 `KafkaTopology-1.0-SNAPSHOT.jar`라는 파일을 만듭니다.

3. 다음 명령을 사용하여 HDInsight 클러스터의 Storm에 패키지를 복사합니다. `sshuser`를 클러스터의 SSH 사용자 이름으로 바꿉니다. `stormclustername`을 __Storm__ 클러스터의 이름으로 바꿉니다.

   ```bash
   scp ./target/KafkaTopology-1.0-SNAPSHOT.jar sshuser@stormclustername-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
   ```

    메시지가 표시되면 클러스터를 만들 때 사용한 암호를 입력합니다.

## <a name="configure-the-topology"></a>토폴로지 구성

1. 다음 방법 중 하나를 사용하여 HDInsight 클러스터에서 **Kafka** 에 대한 Kafka broker 호스트를 검색합니다.

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    > [!IMPORTANT]  
    > 다음 Bash 예제에서는 `$CLUSTERNAME`에 __Kafka__ 클러스터 이름이 포함되어 있다고 가정합니다. 또한 [jq](https://stedolan.github.io/jq/) 버전 1.5 이상이 설치되어 있다고 가정합니다. 메시지가 표시되면 클러스터 로그인 계정에 대한 암호를 입력합니다.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    반환되는 값은 다음 텍스트와 유사합니다.

    ```output
    <brokername1>.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,<brokername2>.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
     ```

    > [!IMPORTANT]  
    > 클러스터에 대해 두 개 이상의 브로커 호스트가 있을 수 있습니다. 클라이언트에 대한 모든 호스트의 전체 목록을 제공할 필요는 없습니다. 하나 또는 두 개로도 충분합니다.

2. 다음 방법 중 하나를 사용하여 HDInsight 클러스터에서 __Kafka__ 에 대한 Zookeeper 호스트를 검색합니다.

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    > [!IMPORTANT]  
    > 다음 Bash 예제에서는 `$CLUSTERNAME`에 __Kafka__ 클러스터가 포함되어 있다고 가정합니다. [jq](https://stedolan.github.io/jq/)도 설치되어 있다고 가정합니다. 메시지가 표시되면 클러스터 로그인 계정에 대한 암호를 입력합니다.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    반환되는 값은 다음 텍스트와 유사합니다.

    ```output
    <zookeepername1>.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,<zookeepername2>.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
    ```

    > [!IMPORTANT]  
    > 두 개 이상의 Zookeeper 노드가 있습니다. 클라이언트에 대한 모든 호스트의 전체 목록을 제공할 필요는 없습니다. 하나 또는 두 개로도 충분합니다.

    나중에 사용하므로 이 값을 저장합니다.

3. 프로젝트의 루트에서 `dev.properties` 파일을 편집합니다. 이 파일에서 일치하는 줄에 __Kafka__ 클러스터에 대한 Broker 및 Zookeeper 호스트 정보를 추가합니다. 다음 예제는 이전 단계의 예제 값을 사용하여 구성됩니다.

    ```bash
    kafka.zookeeper.hosts: <zookeepername1>.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,<zookeepername2>.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
    kafka.broker.hosts: <brokername1>.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,<brokername2>.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
    kafka.topic: stormtopic
    ```

    > [!IMPORTANT]  
    > `hdfs.url` 항목은 Azure Storage 계정을 사용하는 클러스터에 대해 구성됩니다. Data Lake Storage를 사용하는 Storm 클러스터에서 이 토폴로지를 사용하려면 이 값을 `wasb`에서 `adl`로 변경합니다.

4. `dev.properties` 파일을 저장하고 다음 명령을 사용하여 **Storm** 클러스터로 업로드합니다.

     ```bash
    scp dev.properties USERNAME@BASENAME-ssh.azurehdinsight.net:dev.properties
    ```

    **USERNAME** 은 클러스터의 SSH 사용자 이름으로 바꿉니다. **BASENAME** 은 클러스터를 만들 때 사용한 기본 이름으로 바꿉니다.

## <a name="create-the-kafka-topic"></a>Kafka 토픽 만들기

Kafka에서는 데이터를 _토픽_ 에 저장합니다. Storm 토폴로지를 시작하기 전에 토픽을 만들어야 합니다. 토폴로지를 만들려면 다음 단계를 사용합니다.

1. 다음 명령을 사용하여 SSH를 통해 __Kafka__ 클러스터에 연결합니다. `sshuser`은 클러스터를 만들 때 사용한 SSH 사용자 이름으로 바꿉니다. `kafkaclustername`을 Kafka 클러스터의 이름으로 바꿉니다.

    ```bash
    ssh sshuser@kafkaclustername-ssh.azurehdinsight.net
    ```

    메시지가 표시되면 클러스터를 만들 때 사용한 암호를 입력합니다.
   
    자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. Kafka 토픽을 만들려면 다음 명령을 사용합니다. `$KAFKAZKHOSTS`를 토폴로지 구성 시 사용한 Zookeeper 호스트 정보로 바꿉니다.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    이 명령은 Kafka 클러스터에 대한 Zookeeper에 연결한 후 `stormtopic`이라는 새 토픽을 만듭니다. 이 토픽은 Storm 토폴로지에서 사용됩니다.

## <a name="start-the-writer"></a>기록기 시작

1. 다음을 사용하여 SSH를 통해 **Storm** 클러스터에 연결합니다. `sshuser`은 클러스터를 만들 때 사용한 SSH 사용자 이름으로 바꿉니다. `stormclustername`을 Storm 클러스터의 이름으로 바꿉니다.

    ```bash
    ssh sshuser@stormclustername-ssh.azurehdinsight.net
    ```

    메시지가 표시되면 클러스터를 만들 때 사용한 암호를 입력합니다.
   
    자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. Storm 클러스터에 대한 SSH 연결에서 다음 명령을 사용하여 기록기 토폴로지를 시작합니다.

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    이 명령에서 사용된 매개 변수는 다음과 같습니다.

    * `org.apache.storm.flux.Flux`: Flux를 사용하여 이 토폴로지를 구성하고 실행합니다.

    * `--remote`: Nimbus에 토폴로지를 제출합니다. 토폴로지가 클러스터의 작업자 노드에 분산됩니다.

    * `-R /writer.yaml`: `writer.yaml` 파일을 사용하여 토폴로지를 구성합니다. `-R`은 이 리소스가 jar 파일에 포함되어 있는지를 나타내며, jar의 루트에 있으므로 `/writer.yaml`이 그 경로입니다.

    * `--filter`: `dev.properties` 파일의 값을 사용하여 `writer.yaml` 토폴로지의 항목을 입력합니다. 예를 들어 파일에서 `kafka.topic` 항목의 값으로 토폴로지 정의의 `${kafka.topic}` 항목을 바꿉니다.

## <a name="start-the-reader"></a>판독기 시작

1. Storm 클러스터에 대한 SSH 세션에서 다음 명령을 사용하여 판독기 토폴로지를 시작합니다.

   ```bash
   storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
   ```

2. 1분 정도 기다린 후 다음 명령을 사용하여 판독기 토폴로지에서 사용되는 파일을 확인합니다.

    ```bash
    hdfs dfs -ls /stormdata
    ```

    다음 텍스트와 유사하게 출력됩니다.

    ```output
    Found 173 items
      -rw-r--r--   1 storm supergroup       5137 2018-04-09 19:00 /stormdata/hdfs-bolt-4-0-1523300453088.txt
      -rw-r--r--   1 storm supergroup       5128 2018-04-09 19:00 /stormdata/hdfs-bolt-4-1-1523300453624.txt
      -rw-r--r--   1 storm supergroup       5131 2018-04-09 19:00 /stormdata/hdfs-bolt-4-10-1523300455170.txt
      ...
    ```

3. 파일의 내용을 보려면 다음 명령을 사용합니다. `filename.txt`를 파일의 이름으로 바꿉니다.

    ```bash
    hdfs dfs -cat /stormdata/filename.txt
    ```

    다음 텍스트는 파일 내용의 예입니다.

    > 네 가지 점수와 7년 전
    >
    > 백설 공주와 일곱 난쟁이
    >
    > 두 가지 특성을 사용합니다.
    >
    > 백설 공주와 일곱 난쟁이
    >
    > 두 가지 특성을 사용합니다.
    >
    > 네 가지 점수와 7년 전
    >
    > 하루에 사과 하나면 의사가 필요 없습니다.

## <a name="stop-the-topologies"></a>토폴로지 중지

Storm 클러스터에 대한 SSH 세션에서 다음 명령을 사용하여 Storm 토폴로지를 중지합니다.

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 리소스를 정리하려면 리소스 그룹을 삭제합니다. 리소스 그룹을 삭제하면 연결된 HDInsight 클러스터 및 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

Azure Portal을 사용하여 리소스 그룹을 제거하려면:

1. Azure Portal에서 왼쪽의 메뉴를 확장하여 서비스 메뉴를 연 다음 __리소스 그룹__ 을 선택하여 리소스 그룹 목록을 표시합니다.
2. 삭제할 리소스 그룹을 찾은 다음 목록 오른쪽에 있는 __자세히__ 단추(...)를 마우스 오른쪽 단추로 클릭합니다.
3. __리소스 그룹 삭제__ 를 선택한 다음 확인합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 [Apache Storm](https://storm.apache.org/) 토폴로지를 사용하여 HDInsight의 [Apache Kafka](https://kafka.apache.org/)에서 읽고 쓰는 방법을 알아보았습니다. 또한 HDInsight에서 사용되는 [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) 호환 스토리지에 데이터를 저장하는 방법도 배웠습니다.

> [!div class="nextstepaction"]
> [Apache Kafka 생산자 및 소비자 API 사용](kafka/apache-kafka-producer-consumer-api.md)
