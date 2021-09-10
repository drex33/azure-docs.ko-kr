---
title: Azure Cosmos DB용 Kafka Connect를 사용하여 데이터 읽고 쓰기
description: Azure Cosmos DB용 Kafka Connect는 Azure Cosmos DB의 데이터를 읽고 쓰는 커넥터입니다. Kafka Connect는 Apache Kafka와 기타 시스템 사이에 확장 가능하면서도 안정적인 방법으로 데이터를 스트리밍할 수 있는 도구입니다.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: kuthapar
ms.openlocfilehash: 02489a961edf3c9044209da64882d0ab60d20bb4
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117186"
---
# <a name="kafka-connect-for-azure-cosmos-db"></a>Azure Cosmos DB용 Kafka Connect
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

[Kafka Connect](http://kafka.apache.org/documentation.html#connect)는 Apache Kafka와 기타 시스템 사이에 확장 가능하면서도 안정적인 방법으로 데이터를 스트리밍할 수 있는 도구입니다. Kafka Connect를 사용하면 Kafka와 대량의 데이터 세트를 주고 받는 커넥터를 정의할 수 있습니다. Azure Cosmos DB용 Kafka Connect는 Azure Cosmos DB의 데이터를 읽고 쓰는 커넥터입니다.

## <a name="source--sink-connectors-semantics"></a>원본 및 싱크 커넥터 의미 체계

* **원본 커넥터** - 현재 이 커넥터는 여러 작업에서 한 번 이상, 단일 작업에서 단 한 번만 지원합니다.

* **싱크 커넥터** - 이 커넥터는 단 한 번만 의미 체계를 완벽하게 지원합니다.

## <a name="supported-data-formats"></a>지원되는 데이터 형식

다음 데이터 형식을 지원하도록 원본 및 싱크 커넥터를 구성할 수 있습니다.

| 서식 | 설명 |
| :----------- | :---------- |
| 일반 JSON  | 연결된 스키마가 없는 JSON 레코드 구조 |
| 스키마가 있는 JSON | 데이터가 예상된 형식과 일치하도록 명시적 스키마 정보가 있는 JSON 레코드 구조 |
| AVRO | Apache의 Hadoop 프로젝트 내에서 개발된 행 중심 원격 프로시저 호출 및 데이터 직렬화 프레임워크 JSON을 사용하여 데이터 형식, 프로토콜을 정의하고 데이터를 압축 이진 형식으로 직렬화합니다.

형식 및 직렬화를 포함한 키 및 값 설정은 Kafka에서 독립적으로 구성할 수 있습니다. 따라서 키와 값에 대해 각각 서로 다른 데이터 형식으로 작업할 수 있습니다. 다양한 데이터 형식을 수용하기 위해 `key.converter` 및 `value.converter`에 모두 적용되는 변환기 구성이 있습니다.

## <a name="converter-configuration-examples"></a>변환기 구성 예제

### <a name="plain-json"></a><a id="json-plain"></a>일반 JSON

데이터 연결을 위해 스키마 레지스트리 없이 JSON을 사용해야 하는 경우 Kafka에서 지원되는 `JsonConverter`를 사용합니다. 다음 예제에서는 구성에 추가되는 `JsonConverter` 키 및 값 속성을 보여줍니다.

  ```java
  key.converter=org.apache.kafka.connect.json.JsonConverter
  key.converter.schemas.enable=false
  value.converter=org.apache.kafka.connect.json.JsonConverter
  value.converter.schemas.enable=false
  ```

### <a name="json-with-schema"></a><a id="json-with-schema"></a>스키마가 있는 JSON

키 또는 값이 내부 스키마와 데이터를 모두 포함하는 복합 JSON 개체로 처리되도록 `key.converter.schemas.enable` 및 `value.converter.schemas.enable` 속성을 true로 설정합니다. 이러한 속성이 없으면 키 또는 값이 일반 JSON으로 처리됩니다. 

  ```java
  key.converter=org.apache.kafka.connect.json.JsonConverter
  key.converter.schemas.enable=true
  value.converter=org.apache.kafka.connect.json.JsonConverter
  value.converter.schemas.enable=true
  ```

Kafka에 대한 결과 메시지는 아래의 예시와 같으며, JSON에서 스키마 및 페이로드가 최상위 요소로 사용됩니다.

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
      ],
      "optional": false,
      "name": "ksql.users"
    },
    "payload": {
      "userid": 123,
      "name": "user's name"
    }
  }
  ```

> [!NOTE]
> Azure Cosmos DB에 기록된 메시지는 스키마와 페이로드로 구성됩니다. 메시지 크기 뿐만 아니라 페이로드와 스키마로 구성된 비율도 확인할 수 있습니다. 스키마는 Kafka에 쓰는 모든 메시지에서 반복됩니다. 이러한 시나리오에서는 스키마가 별도로 저장되고 메시지가 페이로드만 보관하는 JSON 스키마 또는 AVRO와 같은 직렬화 형식을 사용할 가능성이 높습니다.

### <a name="avro"></a><a id="avro"></a>AVRO

Kafka 커넥터는 AVRO 데이터 서식을 지원합니다. AVRO 형식을 사용하려면 Kafka Connect가 AVRO 데이터를 작업하는 방법을 알 수 있도록 `AvroConverter`를 구성합니다. Azure Cosmos DB Kafka Connect는 Apache 2.0 라이선스 하에서 Confluent에서 제공하는 [AvroConverter](https://www.confluent.io/hub/confluentinc/kafka-connect-avro-converter)를 사용하여 테스트되었습니다. 원하는 경우 다른 사용자 지정 변환기를 사용할 수도 있습니다.

Kafka는 키와 값을 독립적으로 처리합니다. 작업자 구성에서 `key.converter` 및 `value.converter` 속성을 필요한 대로 지정합니다. `AvroConverter`를 사용하는 경우 스키마 레지스트리의 URL을 제공하는 추가 변환기 속성을 추가합니다. 다음 예제에서는 구성에 추가되는 AvroConverter 키 및 값 속성을 보여줍니다.

  ```java
  key.converter=io.confluent.connect.avro.AvroConverter
  key.converter.schema.registry.url=http://schema-registry:8081
  value.converter=io.confluent.connect.avro.AvroConverter
  value.converter.schema.registry.url=http://schema-registry:8081
  ```

## <a name="choose-a-conversion-format"></a>변환 형식 선택

다음은 변환 형식을 선택할 때 고려할 사항입니다.

* **원본 커넥터** 를 구성하는 경우:

  * Kafka Connect가 Kafka에 쓰는 메시지에 일반 JSON을 포함하도록 하려면 [일반 JSON](#json-plain) 구성을 설정합니다.

  * Kafka Connect가 Kafka에 쓰는 메시지에 스키마를 포함하도록 하려면 [스키마가 있는 JSON](#json-with-schema) 구성을 설정합니다.

  * Kafka Connect가 Kafka에 쓰는 메시지에 AVRO 형식을 포함하도록 하려면 [AVRO](#avro) 구성을 설정합니다.

* Kafka 토픽의 JSON 데이터를 **싱크 커넥터** 에 사용하는 경우 JSON이 Kafka 토픽에 작성될 때 어떻게 직렬화되었는지 알아봅니다.

  * JSON 직렬 변환기를 사용하여 작성되었으면 JSON 변환기`(org.apache.kafka.connect.json.JsonConverter)`를 사용하도록 Kafka Connect를 설정합니다.

    * JSON 데이터가 일반 문자열로 작성되었으면 데이터에 중첩된 스키마 또는 페이로드가 포함되어 있는지 확인합니다. 포함되어 있다면 [스키마가 있는 JSON](#json-with-schema) 구성을 설정합니다.
    * JSON 데이터를 사용 중이지만 스키마 또는 페이로드 구문이 없는 경우라면, [일반 JSON](#json-plain) 구성에 따라 `schemas.enable=false`를 설정하여 스키마를 찾지 **말라고** Kafka Connect에 알려야 합니다.

  * AVRO 직렬 변환기를 사용하여 작성되었으면 [AVRO](#avro) 구성에 따라 AVRO 변환기`(io.confluent.connect.avro.AvroConverter)`를 사용하도록 Kafka Connect를 설정합니다.

## <a name="configuration"></a>Configuration

### <a name="common-configuration-properties"></a>일반 구성 속성

원본 커넥터와 싱크 커넥터는 다음과 같은 일반 구성 속성을 공유합니다.

| Name | 유형 | Description | 필수/선택 |
| :--- | :--- | :--- | :--- |
| connect.cosmos.connection.endpoint | uri | Cosmos 엔드포인트 URI 문자열 | 필수 |
| connect.cosmos.master.key | 문자열 | 싱크가 연결되는 Azure Cosmos DB 기본 키. | 필수 |
| connect.cosmos.databasename | 문자열 | 싱크가 쓰는 Azure Cosmos 데이터베이스의 이름. | 필수 |
| connect.cosmos.containers.topicmap | 문자열 | Kafka 토픽과 Azure Cosmos DB 컨테이너 간의 매핑. CSV를 `topic#container,topic2#container2`로 사용하여 형식이 지정됩니다. | 필수 |

싱크 커넥터별 구성은 [싱크 커넥터 설명서](kafka-connector-sink.md)를 참조하세요.

원본 커넥터별 구성은 [원본 커넥터 설명서](kafka-connector-source.md)를 참조하세요.

## <a name="common-configuration-errors"></a>일반적인 구성 오류

Kafka Connect에서 변환기를 잘못 구성하면 오류가 발생할 수 있습니다. 사용자는 Kafka에 이미 저장된 메시지를 역직렬화하려고 할 것이므로 이러한 오류는 Kafka Connector 싱크에서 발생합니다. 직렬화는 원본에서 설정되므로 일반적으로 변환기 문제는 원본에서 발생하지 않습니다.

자세한 정보는 [일반 구성 오류](https://www.confluent.io/blog/kafka-connect-deep-dive-converters-serialization-explained/#common-errors) 문서를 참조하세요.

## <a name="project-setup"></a>프로젝트 설정

초기 설정 지침은 [개발자 연습 및 프로젝트 설정](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Developer_Walkthrough.md)을 참조하세요.

## <a name="performance-testing"></a>성능 테스트

싱크 커넥터와 원본 커넥터의 성능 테스트 실행에 대한 자세한 정보는 [성능 테스트 문서](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/Performance_Testing.md)를 참조하세요.

커넥터의 성능 테스트 환경을 배포하는 정확한 단계는 [성능 환경 설정](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/src/perf/README.md)을 참조하세요.

## <a name="resources"></a>리소스

* [Kafka 연결](http://kafka.apache.org/documentation.html#connect)
* [Kafka Connect 심층 분석 – 변환기 및 직렬화 설명](https://www.confluent.io/blog/kafka-connect-deep-dive-converters-serialization-explained/)

## <a name="next-steps"></a>다음 단계

* Kafka Connect for Azure Cosmos DB [원본 커넥터](kafka-connector-source.md)
* Kafka Connect for Azure Cosmos DB [싱크 커넥터](kafka-connector-sink.md)