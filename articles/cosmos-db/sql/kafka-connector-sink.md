---
title: Kafka Connect for Azure Cosmos DB - 싱크 커넥터
description: Azure Cosmos DB 싱크 커넥터를 사용하여 Apache Kafka 토픽의 데이터를 Azure Cosmos DB 데이터베이스로 내보낼 수 있습니다. 커넥터는 Kafka의 데이터를 폴링하여 토픽 구독을 기반으로 데이터베이스의 컨테이너에 씁니다.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: kuthapar
ms.openlocfilehash: aa4b870a76d9d346116a48633bf3baddc7c44ef4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056737"
---
# <a name="kafka-connect-for-azure-cosmos-db---sink-connector"></a>Kafka Connect for Azure Cosmos DB - 싱크 커넥터
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Kafka Connect for Azure Cosmos DB는 Azure Cosmos DB의 데이터를 읽고 쓰는 커넥터입니다. Azure Cosmos DB 싱크 커넥터를 사용하여 Apache Kafka 토픽의 데이터를 Azure Cosmos DB 데이터베이스로 내보낼 수 있습니다. 커넥터는 Kafka의 데이터를 폴링하여 토픽 구독을 기반으로 데이터베이스의 컨테이너에 씁니다.

## <a name="prerequisites"></a>필수 조건

* 작업할 완전한 환경을 제공하므로 [Confluent Platform 설정](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md)으로 시작합니다. Confluent Platform을 사용하지 않으려면 Zookeeper, Apache Kafka, Kafka Connect를 직접 설치하고 구성해야 합니다. 또한 Azure Cosmos DB 커넥터를 수동으로 설치하고 구성해야 합니다.
* Azure Cosmos DB 계정 만들기, 컨테이너 [설정 가이드](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md)
* WSL2를 사용하여 GitHub Codespaces, Mac, Ubuntu에서 테스트되는 Bash 셸. 이 셸은 Cloud Shell 또는 WSL1에서 작동하지 않습니다.
* [Java 11 이상](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) 다운로드
* [Maven](https://maven.apache.org/download.cgi) 다운로드

## <a name="install-sink-connector"></a>싱크 커넥터 설치

권장되는 [Confluent Platform 설정](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md)을 사용하면 Azure Cosmos DB 싱크 커넥터가 설치에 포함되며 이 단계를 건너뛸 수 있습니다.  

이외의 경우에는 최신 [릴리스](https://github.com/microsoft/kafka-connect-cosmosdb/releases)에서 JAR 파일을 다운로드하거나 이 리포지토리를 패키지하여 새 JAR 파일을 만들 수 있습니다. JAR 파일을 사용하여 커넥터를 수동으로 설치하려면 다음 [지침](https://docs.confluent.io/current/connect/managing/install.html#install-connector-manually)을 참조하세요. 소스 코드에서 새 JAR 파일을 패키지할 수도 있습니다.

```bash
# clone the kafka-connect-cosmosdb repo if you haven't done so already
git clone https://github.com/microsoft/kafka-connect-cosmosdb.git
cd kafka-connect-cosmosdb

# package the source code into a JAR file
mvn clean package

# include the following JAR file in Kafka Connect installation
ls target/*dependencies.jar
```

## <a name="create-a-kafka-topic-and-write-data"></a>Kafka 토픽 만들기 및 데이터 쓰기

Confluent Platform을 사용하는 경우 Kafka 토픽을 만드는 가장 쉬운 방법은 제공된 제어 센터 UX를 사용하는 것입니다. 이외의 경우에는 다음 구문을 사용하여 Kafka 토픽을 수동으로 만들 수 있습니다.

```bash
./kafka-topics.sh --create --zookeeper <ZOOKEEPER_URL:PORT> --replication-factor <NO_OF_REPLICATIONS> --partitions <NO_OF_PARTITIONS> --topic <TOPIC_NAME>
```

이 시나리오에서는 “hotels“라는 Kafka 토픽을 만들고 비스키마 포함된 JSON 데이터를 토픽에 씁니다. 제어 센터 내에 토픽을 만들려면 [Confluent 가이드](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-2-create-ak-topics)를 참조하세요.

다음으로, Kafka 콘솔 생산자를 시작하여 “hotels“ 토픽에 몇 가지 레코드를 씁니다.

```powershell
# Option 1: If using Codespaces, use the built-in CLI utility
kafka-console-producer --broker-list localhost:9092 --topic hotels

# Option 2: Using this repo's Confluent Platform setup, first exec into the broker container
docker exec -it broker /bin/bash
kafka-console-producer --broker-list localhost:9092 --topic hotels

# Option 3: Using your Confluent Platform setup and CLI install
<path-to-confluent>/bin/kafka-console-producer --broker-list <kafka broker hostname> --topic hotels
```

콘솔 생산자에서 다음을 입력합니다.

```json
{"id": "h1", "HotelName": "Marriott", "Description": "Marriott description"}
{"id": "h2", "HotelName": "HolidayInn", "Description": "HolidayInn description"}
{"id": "h3", "HotelName": "Motel8", "Description": "Motel8 description"}
```

입력된 세 개의 레코드는 “hotels“ Kafka 토픽에 JSON 형식으로 게시됩니다.

## <a name="create-the-sink-connector"></a>싱크 커넥터 만들기

Kafka Connect에서 Azure Cosmos DB 싱크 커넥터를 만듭니다. 다음 JSON 본문은 싱크 커넥터의 구성을 정의합니다. 필수 조건의 Azure Cosmos DB 설정 가이드에서 저장한 `connect.cosmos.connection.endpoint` 및 `connect.cosmos.master.key`의 값을 바꾸어야 합니다.

각각의 이 구성 속성에 관한 자세한 내용은 [싱크 속성](#sink-configuration-properties) 섹션을 참조하세요.

```json
{
  "name": "cosmosdb-sink-connector",
  "config": {
    "connector.class": "com.azure.cosmos.kafka.connect.sink.CosmosDBSinkConnector",
    "tasks.max": "1",
    "topics": [
      "hotels"
    ],
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter.schemas.enable": "false",
    "key.converter": "org.apache.kafka.connect.json.JsonConverter",
    "key.converter.schemas.enable": "false",
    "connect.cosmos.connection.endpoint": "https://<cosmosinstance-name>.documents.azure.com:443/",
    "connect.cosmos.master.key": "<cosmosdbprimarykey>",
    "connect.cosmos.databasename": "kafkaconnect",
    "connect.cosmos.containers.topicmap": "hotels#kafka"
  }
}
```

모든 값을 채운 후 JSON 파일을 로컬 위치에 저장합니다. REST API를 사용하여 커넥터를 만드는 데 이 파일을 사용할 수 있습니다.

### <a name="create-connector-using-control-center"></a>제어 센터를 사용하여 커넥터 만들기

커넥터를 만드는 간편한 옵션은 제어 센터 웹 페이지를 통해 진행하는 것입니다. 이 [설치 가이드](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-3-install-a-ak-connector-and-generate-sample-data)에 따라 제어 센터에서 커넥터를 만듭니다. `DatagenConnector` 옵션을 사용하는 대신 `CosmosDBSinkConnector` 타일을 사용합니다. 싱크 커넥터를 구성할 때 JSON 파일을 입력한 대로 값을 작성합니다.

또는 커넥터 페이지에서 **커넥터 구성 파일 업로드** 옵션을 사용하여 앞에서 만든 JSON 파일을 업로드할 수 있습니다.

:::image type="content" source="./media/kafka-connector-sink/upload-connector-config.png" alt-text="커넥터 구성을 업로드합니다.":::

### <a name="create-connector-using-rest-api"></a>REST API를 사용하여 커넥터 만들기

Connect REST API를 사용하여 싱크 커넥터를 만듭니다.

```bash
# Curl to Kafka connect service
curl -H "Content-Type: application/json" -X POST -d @<path-to-JSON-config-file> http://localhost:8083/connectors

```

## <a name="confirm-data-written-to-cosmos-db"></a>Cosmos DB에 기록된 데이터 확인

[Azure Portal](https://portal.azure.com/learn.docs.microsoft.com)에 로그인하고 Azure Cosmos DB 계정으로 이동합니다. “hotels“ 토픽의 세 가지 레코드가 계정에 만들어졌는지 확인합니다.

## <a name="cleanup"></a>정리

제어 센터에서 커넥터를 삭제하려면 직접 만든 싱크 커넥터로 이동하고 **삭제** 아이콘을 클릭합니다.

:::image type="content" source="./media/kafka-connector-sink/delete-connector.png" alt-text="커넥터 삭제":::

또는 Connect REST API를 사용하여 삭제합니다.

```bash
# Curl to Kafka connect service
curl -X DELETE http://localhost:8083/connectors/cosmosdb-sink-connector
```

Azure CLI를 사용하여 생성된 Azure Cosmos DB 서비스와 해당 리소스 그룹을 삭제하려면 이 [단계](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md#cleanup)를 참조하세요.

## <a name="sink-configuration-properties"></a><a id="sink-configuration-properties"></a>싱크 구성 속성

다음 설정은 Cosmos DB Kafka 싱크 커넥터를 구성하는 데 사용됩니다. 이 구성 값은 사용되는 Kafka 토픽 데이터 형식, 기록되는 Azure Cosmos DB 컨테이너 및 데이터를 직렬화하는 형식을 결정합니다. 기본값과 함께 예제 구성 파일에 관해서는 [이 구성]( https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/src/docker/resources/sink.example.json)을 참조하세요.

| Name | Type | Description | 필수/선택 |
| :--- | :--- | :--- | :--- |
| 토픽 | list | 시청할 Kafka 토픽 목록입니다. | 필수 |
| connector.class | 문자열 | Azure Cosmos DB 싱크의 클래스 이름입니다. `com.azure.cosmos.kafka.connect.sink.CosmosDBSinkConnector`로 설정되어야 합니다. | 필수 |
| connect.cosmos.connection.endpoint | uri | Azure Cosmos 엔드포인트 URI 문자열입니다. | 필수 |
| connect.cosmos.master.key | 문자열 | 싱크가 연결되는 Azure Cosmos DB 기본 키. | 필수 |
| connect.cosmos.databasename | 문자열 | 싱크가 쓰는 Azure Cosmos 데이터베이스의 이름. | 필수 |
| connect.cosmos.containers.topicmap | 문자열 | Kafka 토픽과 Azure Cosmos DB 컨테이너 간 매핑으로, CSV를 사용하여 형식이 지정됩니다(예: `topic#container,topic2#container2`). | 필수 |
| key.converter | 문자열 | Kafka 토픽에 기록된 키 데이터의 serialization 형식입니다. | 필수 |
| value.converter | 문자열 | Kafka 토픽에 기록된 값 데이터의 serialization 형식입니다. | 필수 |
| key.converter.schemas.enable | 문자열 | 키 데이터에 포함된 스키마가 있는 경우 “true”로 설정합니다. | 선택 사항 |
| value.converter.schemas.enable | 문자열 | 키 데이터에 포함된 스키마가 있는 경우 “true”로 설정합니다. | 선택 사항 |
| tasks.max | int | 커넥터 싱크 작업의 최대 수입니다. 기본값은 `1` | 선택 사항 |

데이터는 항상 Azure Cosmos DB에 스키마 없이 JSON으로 기록됩니다.

## <a name="supported-data-types"></a>지원되는 데이터 형식

Azure Cosmos DB 싱크 커넥터는 다음 스키마 유형을 지원하는 JSON 문서로 싱크 레코드를 변환합니다.

| 스키마 유형 | JSON 데이터 형식 |
| :--- | :--- |
| Array | Array |
| Boolean | Boolean |
| Float32 | Number |
| Float64 | Number |
| Int8 | Number |
| Int16 | Number |
| Int32 | 숫자 |
| Int64 | Number|
| 지도 | 개체(JSON)|
| String | 문자열<br> Null |
| 구조체 | 개체(JSON) |

싱크 커넥터는 다음 AVRO 논리 형식도 지원합니다.

| 스키마 유형 | JSON 데이터 형식 |
| :--- | :--- |
| 날짜 | Number |
| 시간 | Number |
| 타임스탬프 | Number |

> [!NOTE]
> 바이트 역직렬화는 현재 Azure Cosmos DB 싱크 커넥터에서 지원되지 않습니다.

## <a name="single-message-transformssmt"></a>SMT(단일 메시지 변환)

싱크 커넥터 설정과 함께 SMT(단일 메시지 변환)를 사용하여 Kafka Connect 플랫폼을 통해 이동하는 메시지를 수정하도록 지정할 수 있습니다. 자세한 내용은 [Confluent SMT 설명서](https://docs.confluent.io/platform/current/connect/transforms/overview.html)를 참조하세요.

### <a name="using-the-insertuuid-smt"></a>InsertUUID SMT 사용

InsertUUID SMT를 사용하여 항목 ID를 자동으로 추가할 수 있습니다. 사용자 지정 `InsertUUID` SMT를 사용하면 Azure Cosmos DB에 기록되기 전에 각 메시지에 대해 임의 UUID 값을 사용하여 `id` 필드를 삽입할 수 있습니다.

> [!WARNING]
> 이 SMT는 메시지가 `id` 필드가 없는 경우에만 사용합니다. 그렇지 않으면 `id` 값을 덮어쓰며 데이터베이스에 중복 항목이 생성될 수 있습니다. 메시지 ID로 UUID를 사용하는 것이 빠르고 쉬울 수 있지만 Azure Cosmos DB에서 사용할 [이상적인 파티션 키](https://stackoverflow.com/questions/49031461/would-using-a-substring-of-a-guid-in-cosmosdb-as-partitionkey-be-a-bad-idea)가 아닙니다.

### <a name="install-the-smt"></a>SMT 설치

`InsertUUID` SMT를 사용하려면 먼저 Confluent Platform 설정에서 이 변환을 설치해야 합니다. 이 리포지토리에서 Confluent Platform 설정을 사용하는 경우 변환이 설치에 이미 포함되어 있으며 이 단계를 건너뛸 수 있습니다.

또는 [InsertUUID 원본](https://github.com/confluentinc/kafka-connect-insert-uuid)을 패키지하여 새 JAR 파일을 만들 수 있습니다. JAR 파일을 사용하여 커넥터를 수동으로 설치하려면 다음 [지침](https://docs.confluent.io/current/connect/managing/install.html#install-connector-manually)을 참조하세요.

```bash
# clone the kafka-connect-insert-uuid repo
https://github.com/confluentinc/kafka-connect-insert-uuid.git
cd kafka-connect-insert-uuid

# package the source code into a JAR file
mvn clean package

# include the following JAR file in Confluent Platform installation
ls target/*.jar
```

### <a name="configure-the-smt"></a>SMT 구성

싱크 커넥터 구성 내에 다음 속성을 추가하여 `id`를 설정합니다.

```json
"transforms": "insertID",
"transforms.insertID.type": "com.github.cjmatta.kafka.connect.smt.InsertUuid$Value",
"transforms.insertID.uuid.field.name": "id"
```

이 SMT 사용에 관한 자세한 내용은 [InsertUUID 리포지토리](https://github.com/confluentinc/kafka-connect-insert-uuid)를 참조하세요.

### <a name="using-smts-to-configure-time-to-live-ttl"></a>SMT를 사용하여 TTL(Time to Live) 구성

`InsertField` 및 `Cast` SMT를 둘 다 사용하면 Azure Cosmos DB에 생성된 각 항목에서 TTL을 구성할 수 있습니다. 항목 수준에서 TTL을 사용하도록 설정하기 전에 컨테이너에서 TTL을 사용하도록 설정합니다. 자세한 내용은 TTL([Time to Live](how-to-time-to-live.md#enable-time-to-live-on-a-container-using-azure-portal)) 문서를 참조하세요.

싱크 커넥터 구성 내에 다음 속성을 추가하여 TTL(초)을 설정합니다. 다음 예제에서는 TTL이 100초로 설정됩니다. 메시지에 이미 `TTL` 필드가 포함된 경우 이 SMT가 `TTL` 값을 덮어씁니다.

```json
"transforms": "insertTTL,castTTLInt",
"transforms.insertTTL.type": "org.apache.kafka.connect.transforms.InsertField$Value",
"transforms.insertTTL.static.field": "ttl",
"transforms.insertTTL.static.value": "100",
"transforms.castTTLInt.type": "org.apache.kafka.connect.transforms.Cast$Value",
"transforms.castTTLInt.spec": "ttl:int32"
```

이 SMT에 관한 자세한 내용은 [InsertField](https://docs.confluent.io/platform/current/connect/transforms/insertfield.html) 및 [Cast](https://docs.confluent.io/platform/current/connect/transforms/cast.html) 설명서를 참조하세요.

## <a name="troubleshooting-common-issues"></a>일반적인 문제 해결

Kafka 싱크 커넥터를 사용할 때 발생할 수 있는 몇 가지 일반적인 문제에 대한 해결 방법은 다음과 같습니다.

### <a name="read-non-json-data-with-jsonconverter"></a>JsonConverter를 통해 비 JSON 데이터 읽기

Kafka의 원본 토픽에 비 JSON 데이터가 있고 `JsonConverter`를 사용하여 읽으려고 하면 다음 예외가 표시됩니다.

```console
org.apache.kafka.connect.errors.DataException: Converting byte[] to Kafka Connect data failed due to serialization error:
…
org.apache.kafka.common.errors.SerializationException: java.io.CharConversionException: Invalid UTF-32 character 0x1cfa7e2 (above 0x0010ffff) at char #1, byte #7)

```

이 오류의 원인은 원본 토픽의 데이터가 Avro 형식이나 CSV 문자열 등의 다른 형식으로 직렬화되기 때문일 수 있습니다.

**해결 방법**: 토픽 데이터가 AVRO 형식인 경우 다음과 같이 `AvroConverter`를 사용하도록 Kafka Connect 싱크 커넥터를 변경합니다.

```json
"value.converter": "io.confluent.connect.avro.AvroConverter",
"value.converter.schema.registry.url": "http://schema-registry:8081",
```

### <a name="read-non-avro-data-with-avroconverter"></a>AvroConverter를 통해 비 Avro 데이터 읽기

이 시나리오는 Avro 변환기를 사용하여 Avro 형식이 아닌 토픽에서 데이터를 읽으려고 할 때 적용됩니다. 여기에는 고유한 통신 형식인 Confluent Schema Registry의 Avro 직렬 변환기가 아닌 Avro 직렬 변환기에서 작성한 데이터가 포함됩니다.

```console
org.apache.kafka.connect.errors.DataException: my-topic-name
at io.confluent.connect.avro.AvroConverter.toConnectData(AvroConverter.java:97)
…
org.apache.kafka.common.errors.SerializationException: Error deserializing Avro message for id -1
org.apache.kafka.common.errors.SerializationException: Unknown magic byte!

```

**해결 방법**: 원본 토픽의 serialization 형식을 확인합니다. 그런 다음, Kafka Connect 싱크 커넥터를 전환하여 적합한 변환기를 사용하거나, 업스트림 형식을 Avro로 전환합니다.

### <a name="read-a-json-message-without-the-expected-schemapayload-structure"></a>예상된 스키마/페이로드 구조가 없는 JSON 메시지 읽기

Kafka Connect는 다음과 같이 페이로드와 스키마를 둘 다 포함하는 JSON 메시지의 특수 구조를 지원합니다.

```json
{
  "schema": {
    "type": "struct",
    "fields": [
      {
        "type": "int32",
        "optional": false,
        "field": "userid"
      },
      {
        "type": "string",
        "optional": false,
        "field": "name"
      }
    ]
  },
  "payload": {
    "userid": 123,
    "name": "Sam"
  }
}
```

이 구조의 데이터가 포함되지 않은 JSON 데이터를 읽으려고 하면 다음 오류가 발생합니다.

```none
org.apache.kafka.connect.errors.DataException: JsonConverter with schemas.enable requires "schema" and "payload" fields and may not contain additional fields. If you are trying to deserialize plain JSON data, set schemas.enable=false in your converter configuration.
```

정확히 말하면, `schemas.enable=true`에 유효한 유일한 JSON 구조에는 위에 표시된 것처럼 스키마 및 페이로드 필드가 최상위 요소로 포함됩니다. 오류 메시지에 설명된 대로 일반 JSON 데이터만 있는 경우 커넥터의 구성을 다음으로 변경해야 합니다.

```json
"value.converter": "org.apache.kafka.connect.json.JsonConverter",
"value.converter.schemas.enable": "false",
```

## <a name="limitations"></a>제한 사항

* Azure Cosmos DB에서 데이터베이스와 컨테이너의 자동 생성은 지원되지 않습니다. 데이터베이스와 컨테이너는 이미 존재해야 하며 올바르게 구성되어야 합니다.

## <a name="next-steps"></a>다음 단계

다음 문서를 사용하여 Azure Cosmo DB의 변경 피드에 관해 자세히 알아볼 수 있습니다.

* [변경 피드 소개](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
* [변경 피드에서 읽기](read-change-feed.md)