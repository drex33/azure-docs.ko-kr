---
title: Kafka Connect for Azure Cosmos DB - 원본 커넥터
description: Azure Cosmos DB 원본 커넥터는 Azure Cosmos DB 변경 피드에서 데이터를 읽고 이 데이터를 Kafka 토픽에 게시하는 기능을 제공합니다. Kafka Connect for Azure Cosmos DB는 Azure Cosmos DB의 데이터를 읽고 쓰는 커넥터입니다.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: kuthapar
ms.openlocfilehash: 99879c5fa14cc91e8a39b99ff1a428f31bfde744
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116947"
---
# <a name="kafka-connect-for-azure-cosmos-db---source-connector"></a>Kafka Connect for Azure Cosmos DB - 원본 커넥터
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Kafka Connect for Azure Cosmos DB는 Azure Cosmos DB의 데이터를 읽고 쓰는 커넥터입니다. Azure Cosmos DB 원본 커넥터는 Azure Cosmos DB 변경 피드에서 데이터를 읽고 이 데이터를 Kafka 토픽에 게시하는 기능을 제공합니다.

## <a name="prerequisites"></a>필수 조건

* 작업할 완전한 환경을 제공하므로 [Confluent Platform 설정](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md)으로 시작합니다. Confluent Platform을 사용하지 않으려면 Zookeeper, Apache Kafka, Kafka Connect를 직접 설치하고 구성해야 합니다. 또한 Azure Cosmos DB 커넥터를 수동으로 설치하고 구성해야 합니다.
* Azure Cosmos DB 계정 만들기, 컨테이너 [설정 가이드](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md)
* WSL2를 사용하여 GitHub Codespaces, Mac, Ubuntu에서 테스트되는 Bash 셸. 이 셸은 Cloud Shell 또는 WSL1에서 작동하지 않습니다.
* [Java 11 이상](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) 다운로드
* [Maven](https://maven.apache.org/download.cgi) 다운로드

## <a name="install-the-source-connector"></a>원본 커넥터 설치

권장되는 [Confluent Platform 설정](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Confluent_Platform_Setup.md)을 사용하면 Azure Cosmos DB 원본 커넥터가 설치에 포함되며 이 단계를 건너뛸 수 있습니다.

그렇지 않으면 최신 [릴리스](https://github.com/microsoft/kafka-connect-cosmosdb/releases)의 JAR 파일을 사용하고 커넥터를 수동으로 설치할 수 있습니다. 자세한 내용은 이 [지침](https://docs.confluent.io/current/connect/managing/install.html#install-connector-manually)을 참조하세요. 소스 코드에서 새 JAR 파일을 패키지할 수도 있습니다.

```bash
# clone the kafka-connect-cosmosdb repo if you haven't done so already
git clone https://github.com/microsoft/kafka-connect-cosmosdb.git
cd kafka-connect-cosmosdb

# package the source code into a JAR file
mvn clean package

# include the following JAR file in Confluent Platform installation
ls target/*dependencies.jar
```

## <a name="create-a-kafka-topic"></a>Kafka 토픽 만들기

Confluent Control Center를 사용하여 Kafka 토픽을 만듭니다. 이 시나리오에서는 "apparels"라는 Kafka 토픽을 만들고 비스키마 포함 JSON 데이터를 토픽에 씁니다. 제어 센터 내에서 토픽을 만들려면 [Kafka 토픽 문서 만들기](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-2-create-ak-topics)를 참조하세요.

## <a name="create-the-source-connector"></a>원본 커넥터 만들기

### <a name="create-the-source-connector-in-kafka-connect"></a>Kafka Connect에서 원본 커넥터 만들기

Kafka Connect에서 Azure Cosmos DB 원본 커넥터를 만들려면 다음 JSON 구성을 사용합니다. 필수 조건의 Azure Cosmos DB 설정 가이드에서 저장했어야 하는 `connect.cosmos.connection.endpoint`, `connect.cosmos.master.key` 속성의 자리 표시자 값을 바꿔야 합니다.

```json
{
  "name": "cosmosdb-source-connector",
  "config": {
    "connector.class": "com.azure.cosmos.kafka.connect.source.CosmosDBSourceConnector",
    "tasks.max": "1",
    "key.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "connect.cosmos.task.poll.interval": "100",
    "connect.cosmos.connection.endpoint": "https://<cosmosinstance-name>.documents.azure.com:443/",
    "connect.cosmos.master.key": "<cosmosdbprimarykey>",
    "connect.cosmos.databasename": "kafkaconnect",
    "connect.cosmos.containers.topicmap": "apparels#kafka",
    "connect.cosmos.offset.useLatest": false,
    "value.converter.schemas.enable": "false",
    "key.converter.schemas.enable": "false"
  }
}
```

위의 각 구성 속성에 대한 자세한 내용은 [원본 속성](#source-configuration-properties) 섹션을 참조하세요. 모든 값을 채운 후 JSON 파일을 로컬 위치에 저장합니다. REST API를 사용하여 커넥터를 만드는 데 이 파일을 사용할 수 있습니다.

#### <a name="create-connector-using-control-center"></a>제어 센터를 사용하여 커넥터 만들기

Confluent Control Center 포털에서 커넥터를 쉽게 만들 수 있습니다. [Confluent 설정 가이드](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html#step-3-install-a-ak-connector-and-generate-sample-data)에 따라 제어 센터에서 커넥터를 만듭니다. 설정할 때 `DatagenConnector` 옵션을 사용하는 대신 `CosmosDBSourceConnector` 타일을 사용하세요. 원본 커넥터를 구성할 때 JSON 파일에 입력한 값을 입력합니다.

또는 커넥터 페이지에서 **커넥터 구성 파일 업로드** 옵션을 사용하여 이전 섹션에서 빌드한 JSON 파일을 업로드할 수 있습니다.

:::image type="content" source="./media/kafka-connector-source/upload-connector-config.png" alt-text="커넥터 구성을 업로드합니다.":::

#### <a name="create-connector-using-rest-api"></a>REST API를 사용하여 커넥터 만들기

Connect REST API를 사용하여 원본 커넥터 만들기

```bash
# Curl to Kafka connect service
curl -H "Content-Type: application/json" -X POST -d @<path-to-JSON-config-file> http://localhost:8083/connectors
```

## <a name="insert-document-into-azure-cosmos-db"></a>Azure Cosmos DB에 문서 삽입

1. [Azure Portal](https://portal.azure.com/learn.docs.microsoft.com)에 로그인하고 Azure Cosmos DB 계정으로 이동합니다.
1. **데이터 탐색** 탭을 열고 **데이터베이스** 를 선택합니다.
1. 이전에 만든 "kafkaconnect" 데이터베이스와 "kafka" 컨테이너를 엽니다.
1. 새 JSON 문서를 만들려면 SQL API 창에서 "kafka" 컨테이너를 확장하고 **항목** 을 선택한 다음 툴바에서 **새 항목** 을 선택합니다.
1. 이제 다음 구조를 사용하여 컨테이너에 문서를 추가합니다. 다음 샘플 JSON 블록을 항목 탭에 붙여넣어 현재 콘텐츠를 덮어씁니다.

   ``` json
 
   {
     "id": "2",
     "productId": "33218897",
     "category": "Women's Outerwear",
     "manufacturer": "Contoso",
     "description": "Black wool pea-coat",
     "price": "49.99",
     "shipping": {
       "weight": 2,
       "dimensions": {
         "width": 8,
         "height": 11,
         "depth": 3
       }
     }
   }
 
   ```

1. **저장** 을 선택합니다.
1. 왼쪽 메뉴에서 항목을 보고 문서가 저장되었는지 확인합니다.

### <a name="confirm-data-written-to-kafka-topic"></a>Kafka 토픽에 기록된 데이터 확인

1. `<http://localhost:9000>`에서 Kafka 토픽 UI를 엽니다.
1. 만든 Kafka "apparels" 토픽을 선택합니다.
1. 이전에 Azure Cosmos DB에 삽입한 문서가 Kafka 항목에 나타나는지 확인합니다.

### <a name="cleanup"></a>정리

Confluent Control Center에서 커넥터를 삭제하려면 만든 원본 커넥터로 이동하여 **삭제** 아이콘을 선택합니다.

:::image type="content" source="./media/kafka-connector-source/delete-source-connector.png" alt-text="Confluent 센터에서 커넥터 삭제":::

또는 커넥터 REST API를 사용합니다.

```bash
# Curl to Kafka connect service
curl -X DELETE http://localhost:8083/connectors/cosmosdb-source-connector
```

Azure CLI를 사용하여 생성된 Azure Cosmos DB 서비스와 해당 리소스 그룹을 삭제하려면 이 [단계](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/CosmosDB_Setup.md#cleanup)를 참조하세요.

## <a name="source-configuration-properties"></a>원본 구성 속성

다음 설정은 Kafka 원본 커넥터를 구성하는 데 사용됩니다. 이러한 구성 값은 사용되는 Azure Cosmos DB 컨테이너, Kafka 항목이 작성된 데이터, 데이터를 직렬화할 형식을 결정합니다. 기본값이 있는 예는 이 [구성 파일](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/src/docker/resources/source.example.json)을 참조하세요.

| Name | 유형 | 설명 | 필수/선택 |
| :--- | :--- | :--- | :--- |
| connector.class | 문자열 | Azure Cosmos DB 원본의 클래스 이름입니다. `com.azure.cosmos.kafka.connect.source.CosmosDBSourceConnector`로 설정되어야 합니다. | 필수 |
| connect.cosmos.databasename | 문자열 | 읽을 데이터베이스의 이름입니다. | 필수 |
| connect.cosmos.master.key | 문자열 | Azure Cosmos DB 기본 키입니다. | 필수 |
| connect.cosmos.connection.endpoint | URI | 계정 엔드포인트입니다. | 필수 |
| connect.cosmos.containers.topicmap | 문자열 | 컨테이너 매핑에 대한 쉼표로 구분된 토픽입니다. 예: topic1#coll1, topic2#coll2 | 필수 |
| connect.cosmos.messagekey.enabled | Boolean | 이 값은 Kafka 메시지 키를 설정해야 하는지 여부를 나타냅니다. 기본값은 `true` | 필수 |
| connect.cosmos.messagekey.field | 문자열 | 문서의 필드 값을 메시지 키로 사용합니다. 기본값은 `id`입니다. | 필수 |
| connect.cosmos.offset.useLatest | Boolean |  가장 최근의 원본 오프셋을 사용하려면 `true`로 설정합니다. 가장 먼저 기록된 오프셋을 사용하려면 `false`로 설정합니다. 기본값은 `false`여야 합니다. | 필수 |
| connect.cosmos.task.poll.interval | Int | 변경 사항에 대해 변경 피드 컨테이너를 폴링하는 간격입니다. | 필수 |
| key.converter | 문자열 | Kafka 토픽에 기록된 키 데이터의 serialization 형식입니다. | 필수 |
| value.converter | 문자열 | Kafka 토픽에 기록된 값 데이터의 serialization 형식입니다. | 필수 |
| key.converter.schemas.enable | 문자열 | 키 데이터에 포함된 스키마가 있는 경우 `true`로 설정합니다. | 선택 사항 |
| value.converter.schemas.enable | 문자열 | 키 데이터에 포함된 스키마가 있는 경우 `true`로 설정합니다. | 선택 사항 |
| tasks.max | Int | 커넥터 원본 작업의 최대 수입니다. 기본값은 `1`여야 합니다. | 선택 사항 |

## <a name="supported-data-types"></a>지원되는 데이터 형식

Azure Cosmos DB 원본 커넥터는 JSON 문서를 스키마로 변환하고 다음 JSON 데이터 형식을 지원합니다.

| JSON 데이터 형식 | 스키마 유형 |
| :--- | :--- |
| Array | Array |
| Boolean | Boolean | 
| Number | Float32<br>Float64<br>Int8<br>Int16<br>Int32<br>Int64|
| Null | 문자열 |
| 개체(JSON)| 구조체|
| String | String |

## <a name="next-steps"></a>다음 단계

* Azure Cosmos DB용 Kafka 연결 [싱크 커넥터](kafka-connector-sink.md)