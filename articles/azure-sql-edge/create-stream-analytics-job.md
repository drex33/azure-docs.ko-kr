---
title: Azure SQL Edge에서 T-SQL 스트리밍 작업 만들기
description: Azure SQL Edge에서 Stream Analytics 작업을 만드는 방법에 대해 알아봅니다.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 8f5059352793adf7cd3e8635d270ba85a3014758
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434217"
---
# <a name="create-a-data-streaming-job-in-azure-sql-edge"></a>Azure SQL Edge에서 데이터 스트리밍 작업 만들기 

이 문서에서는 Azure SQL Edge에서 T-SQL 스트리밍 작업을 만드는 방법을 설명합니다. 외부 스트리밍 입력 및 출력 개체를 만든 다음 스트리밍 작업을 만드는 과정의 일부로 스트리밍 작업 쿼리를 정의합니다.

## <a name="configure-the-external-stream-input-and-output-objects"></a>외부 스트림 입력 및 출력 개체 구성

T-SQL 스트리밍은 SQL Server의 외부 데이터 원본 기능을 사용하여 스트리밍 작업의 외부 스트림 입력 및 출력과 연결된 데이터 원본을 정의합니다. 외부 스트림 입력 또는 출력 개체를 만들려면 다음 T-SQL 명령이 필요합니다.

- [CREATE EXTERNAL FILE FORMAT(Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql)

- [CREATE EXTERNAL DATA SOURCE(Transact-SQL)](/sql/t-sql/statements/create-external-data-source-transact-sql)

- [CREATE EXTERNAL STREAM(Transact-SQL)](#example-create-an-external-stream-object-to-azure-sql-database)

또한 Azure SQL Edge, SQL Server 또는 Azure SQL Database가 출력 스트리밍으로 사용되는 경우 [CREATE DATABASE SCOPED CREDENTIAL(Transact-SQL)](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)이 필요합니다. 이 T-SQL 명령은 데이터베이스에 액세스하기 위한 자격 증명을 정의합니다.

### <a name="supported-input-and-output-stream-data-sources"></a>지원되는 입력 및 출력 스트림 데이터 원본

현재 Azure SQL Edge는 스트림 입력 및 출력으로 다음 데이터 원본만 지원합니다.

| 데이터 원본 유형 | 입력 | 출력 | Description |
|------------------|-------|--------|------------------|
| Azure IoT Edge hub | Y | Y | 스트리밍 데이터를 읽고 Azure IoT Edge Hub에 쓰기 위한 데이터 원본입니다. 자세한 내용은 [IoT Edge Hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub)를 참조하세요.|
| SQL Database | N | Y | SQL Database에 스트리밍 데이터를 쓰는 데이터 원본 연결입니다. 데이터베이스는 Azure SQL Edge의 로컬 데이터베이스이거나, SQL Server 또는 Azure SQL Database의 원격 데이터베이스일 수 있습니다.|
| Kafka | Y | N | Kafka 토픽에서 스트리밍 데이터를 읽는 데이터 원본입니다. 이 어댑터는 현재 Azure SQL Edge의 Intel 또는 AMD 버전에서만 사용할 수 있습니다. Azure SQL Edge의 ARM64 버전에는 사용할 수 없습니다.|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>예제: Azure IoT Edge Hub에 대한 외부 스트림 입력/출력 개체 만들기

다음 예제에서는 Azure IoT Edge 허브에 대한 외부 스트리밍 개체를 만듭니다. Azure IoT Edge Hub에 대한 외부 스트림 입력/출력 데이터 원본을 만들려면 읽는/쓰는 데이터의 레이아웃을 외부 파일 형식부터 만들어야 합니다.

1. JSON 형식의 외부 파일 형식을 만듭니다.

    ```sql
    Create External file format InputFileFormat
    WITH 
    (  
       format_type = JSON,
    )
    go
    ```

2. Azure IoT Edge 허브에 대한 외부 데이터 원본을 만듭니다. 다음 T-SQL 스크립트는 Azure SQL Edge와 동일한 Docker 호스트에서 실행되는 IoT Edge 허브에 대한 데이터 원본 연결을 만듭니다.

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput 
    WITH 
    (
        LOCATION = 'edgehub://'
    )
    go
    ```

3. Azure IoT Edge 허브에 대한 외부 스트리밍 개체를 만듭니다. 다음 T-SQL 스크립트는 IoT Edge 허브에 대한 스트리밍 개체를 만듭니다. IoT Edge 허브 스트림 개체의 경우 LOCATION 매개 변수는 읽거나 쓰는 IoT Edge 허브 토픽/채널의 이름입니다.

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors 
    WITH 
    (
        DATA_SOURCE = EdgeHubInput,
        FILE_FORMAT = InputFileFormat,
        LOCATION = N'TemperatureSensors',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    go
    ```

### <a name="example-create-an-external-stream-object-to-azure-sql-database"></a>예제: 외부 스트림 개체 Azure SQL Database 만들기

다음 예제에서는 Azure SQL Edge의 로컬 데이터베이스에 대한 외부 스트리밍 개체를 만듭니다. 

1. 데이터베이스에 마스터 키를 만듭니다. 이 키는 자격 증명 비밀을 암호화하는 데 필요합니다.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. SQL Server 원본에 액세스하기 위한 데이터베이스 범위 자격 증명을 만듭니다. 다음 예제에서는 IDENTITY = 'username' 및 SECRET = 'password'를 사용하여 외부 데이터 원본에 대한 자격 증명을 만듭니다.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. CREATE EXTERNAL DATA SOURCE를 사용하여 외부 데이터 원본을 만듭니다. 다음 예제를 참조하세요.

    * *LocalSQLOutput* 이라는 외부 데이터 원본을 만듭니다.
    * 외부 데이터 원본(`LOCATION = '<vendor>://<server>[:<port>]'`)을 확인합니다. 예제에서는 Azure SQL Edge의 로컬 인스턴스를 가리킵니다.
    * 이전에 만든 자격 증명을 사용합니다.

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput 
    WITH 
    (
        LOCATION = 'sqlserver://tcp:.,1433',
        CREDENTIAL = SQLCredential
    )
    go
    ```

4. 외부 스트림 개체를 만듭니다. 다음 예제에서는 dbo *MySQLDatabase* 데이터베이스의 *dbo.TemperatureMeasurements* 테이블을 가리키는 외부 스트림 개체를 만듭니다.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements 
    WITH 
    (
        DATA_SOURCE = LocalSQLOutput,
        LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    ```

### <a name="example-create-an-external-stream-object-for-kafka"></a>예제: 외부 스트림 개체 Kafka 만들기

다음 예제에서는 Azure SQL Edge의 로컬 데이터베이스에 대한 외부 스트리밍 개체를 만듭니다. 이 예제에서는 Kafka 서버가 익명 액세스를 위해 구성되어 있다고 가정합니다. 

1. CREATE EXTERNAL DATA SOURCE를 사용하여 외부 데이터 원본을 만듭니다. 다음 예제를 참조하세요.

    ```sql
    Create EXTERNAL DATA SOURCE [KafkaInput] 
    With
    (
        LOCATION = N'kafka://<kafka_bootstrap_server_name_ip>:<port_number>'
    )
    GO
    ```
2. Kafka 입력에 대한 외부 파일 형식을 만듭니다. 다음 예제에서는 GZipped 압축을 사용하여 JSON 파일 형식을 만들었습니다. 

   ```sql
   CREATE EXTERNAL FILE FORMAT JsonGzipped  
    WITH 
    (  
        FORMAT_TYPE = JSON , 
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' 
    )
   ```

3. 외부 스트림 개체를 만듭니다. 다음 예제에서는 Kafka 항목 `*TemperatureMeasurement*`를 가리키는 외부 스트리밍 개체를 만듭니다.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurement 
    WITH 
    (  
        DATA_SOURCE = KafkaInput, 
        FILE_FORMAT = JsonGzipped,
        LOCATION = 'TemperatureMeasurement',
        INPUT_OPTIONS = 'PARTITIONS: 10' 
    ); 
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>스트리밍 작업 및 스트리밍 쿼리 만들기

`sys.sp_create_streaming_job` 시스템 저장 프로시저를 사용하여 스트리밍 쿼리를 정의하고 스트리밍 작업을 만듭니다. `sp_create_streaming_job` 저장 프로시저는 다음 매개 변수를 사용합니다.

- `job_name`: 스트리밍 작업의 이름입니다. 스트리밍 작업 이름은 인스턴스 전체에서 고유합니다.
- `statement`: [Stream Analytics 쿼리 언어](/stream-analytics-query/stream-analytics-query-language-reference) 기반의 스트리밍 쿼리 명령문입니다.

다음 예제에서는 하나의 스트리밍 쿼리로 간단한 스트리밍 작업을 만듭니다. 이 쿼리는 IoT Edge Hub에서 입력을 읽고 데이터베이스의 `dbo.TemperatureMeasurements`에 씁니다.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

다음 예제에서는 여러 쿼리를 통해 더 복잡한 스트리밍 작업을 만듭니다. 이러한 쿼리에는 내장된 `AnomalyDetection_ChangePoint` 함수를 사용하여 온도 데이터의 이상을 식별하는 쿼리가 포함됩니다.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>스트리밍 작업 시작, 중지, 삭제 및 모니터링

Azure SQL Edge에서 스트리밍 작업을 시작하려면 `sys.sp_start_streaming_job` 저장 프로시저를 실행합니다. 저장 프로시저에서 입력과 동일한 스트리밍 작업을 시작해야 합니다.

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

스트리밍 작업을 중지하려면 `sys.sp_stop_streaming_job` 저장 프로시저를 실행합니다. 저장 프로시저는 중지할 스트리밍 작업의 이름을 입력으로 요구합니다.

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

스트리밍 작업을 삭제하려면 `sys.sp_drop_streaming_job` 저장 프로시저를 실행합니다. 저장 프로시저는 삭제할 스트리밍 작업의 이름을 입력으로 요구합니다.

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

스트리밍 작업의 현재 상태를 가져오려면 `sys.sp_get_streaming_job` 저장 프로시저를 실행합니다. 저장 프로시저는 삭제할 스트리밍 작업의 이름을 입력으로 요구합니다. 스트리밍 작업의 이름 및 현재 상태를 출력합니다.

```sql
exec sys.sp_get_streaming_job @name=N'StreamingJob1'
        WITH RESULT SETS
(
       (
       name nvarchar(256),
       status nvarchar(256),
       error nvarchar(256)
       )
)
```

스트리밍 작업의 상태는 다음 중 하나입니다.

| 상태 | Description |
|--------| ------------|
| 생성일 | 스트리밍 작업이 만들어졌지만 아직 시작되지 않았습니다. |
| 시작 중 | 스트리밍 작업이 시작 단계에 있습니다. |
| 유휴 상태 | 스트리밍 작업이 실행 중이지만 처리할 입력이 없습니다. |
| 처리 중 | 스트리밍 작업이 실행 중이며 입력을 처리하고 있습니다. 이 상태는 스트리밍 작업이 정상임을 나타냅니다. |
| 성능 저하됨 | 스트리밍 작업이 실행 중이지만 입력 처리 중에 치명적이지 않은 오류가 발생했습니다. 입력 작업은 계속 실행되지만 오류가 발생하는 입력은 삭제됩니다. |
| 중지됨 | 스트리밍 작업이 중지되었습니다. |
| 실패 | 스트림 작업이 실패했습니다. 일반적으로 처리하는 동안 치명적인 오류가 발생했음을 나타냅니다. |

## <a name="next-steps"></a>다음 단계

- [Azure SQL Edge에서 스트리밍 작업과 관련된 메타데이터 보기](streaming-catalog-views.md) 
- [외부 스트림 만들기](create-external-stream-transact-sql.md)