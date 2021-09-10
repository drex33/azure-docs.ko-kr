---
title: CREATE EXTERNAL STREAM (Transact-SQL) - Azure SQL Edge
description: Azure SQL Edge의 CREATE EXTERNAL STREAM 문에 대해 알아보기
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 70c33d12e905e585f5bb6c852fc87e867db8af52
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567382"
---
# <a name="create-external-stream-transact-sql"></a>CREATE EXTERNAL STREAM(Transact-SQL)

EXTERNAL STREAM 개체는 입력과 출력 스트림이라는 두 가지 용도를 모두 갖고 있습니다. Azure Event Hub, Azure IoT Hub(또는 Edge Hub) 또는 Kafka와 같은 이벤트 수집 서비스에서 스트리밍 데이터를 쿼리하는 입력으로 사용할 수도 있고, 스트리밍 쿼리의 결과를 저장하는 위치와 방법을 지정하는 출력으로 사용할 수도 있습니다.

EXTERNAL STREAM을 지정하고 이벤트 허브 또는 Blob 스토리지 같은 서비스의 출력과 입력으로 만들 수도 있습니다. 이를 통해 스트리밍 쿼리가 결과를 외부 스트림에 출력으로 유지하고, 또 다른 스트리밍 쿼리가 입력과 동일한 외부 스트림에서 읽는 시나리오를 연결하는 것이 가능합니다.

현재 Azure SQL Edge는 스트림 입력 및 출력으로 다음 데이터 원본만 지원합니다.

| 데이터 원본 유형 | 입력 | 출력 | Description |
|------------------|-------|--------|------------------|
| Azure IoT Edge hub | Y | Y | 스트리밍 데이터를 읽고 Azure IoT Edge Hub에 쓰기 위한 데이터 원본입니다. 자세한 내용은 [IoT Edge Hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub)를 참조하세요.|
| SQL Database | N | Y | SQL Database에 스트리밍 데이터를 쓰는 데이터 원본 연결입니다. 데이터베이스는 Azure SQL Edge의 로컬 데이터베이스이거나, SQL Server 또는 Azure SQL Database의 원격 데이터베이스일 수 있습니다.|
| Kafka | Y | N | Kafka 토픽에서 스트리밍 데이터를 읽는 데이터 원본입니다. Azure SQL Edge의 ARM64 버전에는 kafka 지원 기능을 사용할 수 없습니다.|



## <a name="syntax"></a>구문

```syntaxsql
CREATE EXTERNAL STREAM {external_stream_name}
( <column_definition> [, <column_definition> ] * ) -- Used for Inputs - optional
WITH  ( <with_options> )

<column_definition> ::=
  column_name <column_data_type>

<data_type> ::=
[ type_schema_name . ] type_name
    [ ( precision [ , scale ] | max ) ]

<with_options> ::=
  DATA_SOURCE = data_source_name,
  LOCATION = location_name,
  [FILE_FORMAT = external_file_format_name], --Used for Inputs - optional
  [<optional_input_options>],
  [<optional_output_options>],
  TAGS = <tag_column_value>

<optional_input_options> ::=
  INPUT_OPTIONS = '[<Input_options_data>]'

<Input_option_data> ::=
      <input_option_values> [ , <input_option_values> ]

<input_option_values> ::=
  PARTITIONS: [number_of_partitions]
  | CONSUMER_GROUP: [ consumer_group_name ]
  | TIME_POLICY: [ time_policy ]
  | LATE_EVENT_TOLERANCE: [ late_event_tolerance_value ]
  | OUT_OF_ORDER_EVENT_TOLERANCE: [ out_of_order_tolerance_value ]

<optional_output_options> ::=
  OUTPUT_OPTIONS = '[<output_option_data>]'

<output_option_data> ::=
      <output_option_values> [ , <output_option_values> ]

<output_option_values> ::=
   REJECT_POLICY: [ reject_policy ]
   | MINIMUM_ROWS: [ row_value ]
   | MAXIMUM_TIME: [ time_value_minutes]
   | PARTITION_KEY_COLUMN: [ partition_key_column_name ]
   | PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ]
   | SYSTEM_PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ]
   | PARTITION_KEY: [ partition_key_name ]
   | ROW_KEY: [ row_key_name ]
   | BATCH_SIZE: [ batch_size_value ]
   | MAXIMUM_BATCH_COUNT: [ batch_value ]
   | STAGING_AREA: [ blob_data_source ]

<tag_column_value> ::= -- Reserved for Future Usage
);
```

## <a name="arguments"></a>인수

- [DATA_SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [FILE_FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **위치**: 데이터 원본에서 실제 데이터 또는 위치의 이름을 지정합니다.
   - Edge Hub 또는 Kafka 스트림 개체의 경우, 위치는 데이터를 읽거나 쓸 Edge Hub 또는 Kafka 토픽의 이름을 지정합니다.
   - SQL 스트림 개체(SQL Server, Azure SQL Database 또는 Azure SQL Edge)의 경우, 위치는 테이블의 이름을 지정합니다. 스트림이 대상 테이블과 동일한 데이터베이스 및 스키마에 생성된 경우에는 테이블 이름 접미사만 지정됩니다. 그렇지 않으면 테이블 이름을 정규화해야 합니다(<database_name.schema_name.table_name).
   - Azure Blob Storage 스트림 개체 위치는 Blob 컨테이너 내에서 사용할 경로 패턴을 참조합니다. 이 기능에 대한 자세한 내용은 (/articles/stream-analytics/stream-analytics-define-outputs.md#blob-storage-and-azure-data-lake-gen2)를 참조하세요.

- **INPUT_OPTIONS**: 옵션을 스트리밍 쿼리의 입력인 Kafka, IoT Edge Hub와 같은 서비스의 키-값 쌍으로 지정합니다.
    - 파티션: 토픽에 대해 정의된 파티션 수입니다. 사용할 수 있는 최대 파티션 수는 32개로 제한됩니다.
      - Kafka 입력 스트림에 적용됩니다.
    - CONSUMER_GROUP: Event 및 IoT Hubs는 한 소비자 그룹 내의 reader 수를 5로 제한합니다. 이 필드를 비워 두면 '$Default' 소비자 그룹이 사용됩니다.
      - 사용하도록 예약되었습니다. Azure SQL Edge에는 적용되지 않습니다.
    - TIME_POLICY: 지연 이벤트 또는 잘못된 이벤트가 허용 오차 값을 전달할 때 이벤트를 삭제할 것인지 아니면 이벤트 시간을 조정할 것인지 여부를 설명합니다.
      - 사용하도록 예약되었습니다. Azure SQL Edge에는 적용되지 않습니다.
    - LATE_EVENT_TOLERANCE: 허용되는 최대 시간 지연입니다. 지연은 이벤트의 타임스탬프와 시스템 시계의 차이를 나타냅니다.
      - 사용하도록 예약되었습니다. Azure SQL Edge에는 적용되지 않습니다.
    - OUT_OF_ORDER_EVENT_TOLERANCE: 이벤트가 입력에서 스트리밍 쿼리로 이동한 후 순서 없이 도착할 수 있습니다. 이러한 이벤트를 그대로 허용할 수도 있고, 일정 시간 동안 일시 중지하고 순서를 변경할 수도 있습니다.
      - 사용하도록 예약되었습니다. Azure SQL Edge에는 적용되지 않습니다.

- **OUTPUT_OPTIONS**: 옵션을 스트리밍 쿼리의 출력인 지원되는 서비스의 키-값 쌍으로 지정합니다.
  - REJECT_POLICY:  DROP | RETRY 데이터 변환 오류가 발생할 때 데이터 오류 처리 정책을 지정합니다.
    - 적용 대상: 지원되는 모든 출력
  - MINIMUM_ROWS: 출력에 작성된 일괄 처리마다 필요한 최소 행 수입니다. Parquet의 경우 일괄 처리마다 새 파일을 만듭니다.
    - 적용 대상: 지원되는 모든 출력
  - MAXIMUM_TIME: 일괄 처리당 최대 대기 시간(분 단위)입니다. 이 시간이 지나면 최소 행 수 요구 사항이 충족되지 않아도 일괄 처리가 출력에 기록됩니다.
    - 적용 대상: 지원되는 모든 출력
  - PARTITION_KEY_COLUMN: 파티션 키에 사용되는 열입니다.
    - 사용하도록 예약되었습니다. Azure SQL Edge에는 적용되지 않습니다.
  - PROPERTY_COLUMNS: 쉼표로 구분된 출력 열 이름 목록이며, 메시지에 사용자 지정 속성으로 첨부되는 방식으로 제공됩니다.
    - 사용하도록 예약되었습니다. Azure SQL Edge에는 적용되지 않습니다.
  - SYSTEM_PROPERTY_COLUMNS: Service Bus 메시지에서 채워질 시스템 속성 이름 및 출력 열의 JSON 형식 이름/값 쌍 컬렉션입니다. 예: { "MessageId":   "column1", "PartitionKey": "column2"}
    - 사용하도록 예약되었습니다. Azure SQL Edge에는 적용되지 않습니다.
  - PARTITION_KEY: 파티션 키를 포함하는 출력 열의 이름입니다. 파티션 키는 특정 테이블 내에서 분할의 고유 식별자 역할을 하며 엔터티 기본 키의 첫 번째 부분을 형성합니다. 크기가 최대 1KB인 문자열 값입니다.
    - 사용하도록 예약되었습니다. Azure SQL Edge에는 적용되지 않습니다.
  - ROW_KEY: 행 키를 포함하는 출력 열의 이름입니다. 행 키는 주어진 파티션 내 엔터티의 고유 식별자입니다. 엔터티 기본 키의 두 번째 부분을 형성합니다. 행 키는 크기가 최대 1KB인 문자열 값입니다.
    - 사용하도록 예약되었습니다. Azure SQL Edge에는 적용되지 않습니다.
  - BATCH_SIZE: 최대 레코드 수가 100개인 테이블 스토리지의 트랜잭션 수를 나타냅니다. Azure Functions의 경우 호출마다 함수로 전송되는 일괄 처리 크기(바이트)를 나타내며 기본값은 256kB입니다.
    - 사용하도록 예약되었습니다. Azure SQL Edge에는 적용되지 않습니다.
  - MAXIMUM_BATCH_COUNT: Azure 함수 호출마다 함수에 전송되는 최대 이벤트 수이며 기본값은 100입니다. SQL Database의 경우 모든 대량 삽입 트랜잭션과 함께 전송되는 최대 레코드 수이며 기본값은 10,000입니다.
    - 모든 SQL 기반 출력에 적용됩니다.
  - STAGING_AREA: EXTERNAL DATA SOURCE 개체에서 Blob Storage로 적용. Azure Synapse Analytics에 높은 처리량의 데이터를 수집하기 위한 준비 영역입니다.
    - 사용하도록 예약되었습니다. Azure SQL Edge에는 적용되지 않습니다.


## <a name="examples"></a>예

### <a name="example-1---edgehub"></a>예제 1 - EdgeHub

유형: 입력 또는 출력<br>

구문

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub
WITH
(
  LOCATION = 'edgehub://'
);

CREATE EXTERNAL FILE FORMAT myFileFormat
WITH (
   FORMAT_TYPE = JSON
);

CREATE EXTERNAL STREAM Stream_A
WITH
(
   DATA_SOURCE = MyEdgeHub,
   FILE_FORMAT = myFileFormat,
   LOCATION = '<mytopicname>',
   OUTPUT_OPTIONS =
     'REJECT_TYPE: Drop'
);
```

### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>예제 2 - Azure SQL Database, Azure SQL Edge, SQL Server

유형: 출력<br>

구문

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName
WITH IDENTITY = '<user>',
SECRET = '<password>';

-- Azure SQL Database
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl
WITH
(
  LOCATION = '<my_server_name>.database.windows.net',
  CREDENTIAL = SQLCredName
);

--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl
WITH
(
  LOCATION = ' <sqlserver://<ipaddress>,<port>',
  CREDENTIAL = SQLCredName
);

CREATE EXTERNAL STREAM Stream_A
WITH
(
    DATA_SOURCE = MyTargetSQLTable,
    LOCATION = '<DatabaseName>.<SchemaName>.<TableName>' ,
   --Note: If table is contained in the database, <TableName> should be sufficient
    OUTPUT_OPTIONS =
      'REJECT_TYPE: Drop'
);
```

### <a name="example-3---kafka"></a>예제 3 - Kafka

유형: 입력<br>

구문
```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets
WITH
(
  --The location maps to KafkaBootstrapServer
  LOCATION = 'kafka://<kafkaserver>:<ipaddress>',
  CREDENTIAL = kafkaCredName
);

CREATE EXTERNAL FILE FORMAT myFileFormat
WITH (
    FORMAT_TYPE = JSON,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
);

CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR)
WITH
(
    DATA_SOURCE = MyKafka_tweets,
    LOCATION = '<KafkaTopicName>',
    FILE_FORMAT = myFileFormat,
    INPUT_OPTIONS =
      'PARTITIONS: 5'
);
```

## <a name="see-also"></a>참고 항목

- [DROP EXTERNAL STREAM(Transact-SQL)](drop-external-stream-transact-sql.md)
