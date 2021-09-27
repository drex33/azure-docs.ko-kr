---
title: 복사 작업의 내결함성
titleSuffix: Azure Data Factory & Azure Synapse
description: 호환 되지 않는 데이터를 건너뛰어 Azure Data Factory 및 Synapse Analytics 파이프라인에서 복사 작업에 내결함성을 추가 하는 방법에 대해 알아봅니다.
author: dearandyxu
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: yexu
ms.openlocfilehash: c6b5a08cc4f0cc90f8ae827f4f8c2c7469e92b44
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124767568"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory-and-synapse-analytics-pipelines"></a>Azure Data Factory 및 Synapse Analytics 파이프라인의 복사 작업에 대 한 내결함성
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [현재 버전](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

원본 저장소에서 대상 저장소로 데이터를 복사 하는 경우 복사 작업은 데이터 이동 중에 중단이 발생 하지 않도록 특정 수준의 내결함성을 제공 합니다. 예를 들어 대상 데이터베이스에서 기본 키가 생성되었지만 원본 데이터베이스에 기본 키가 정의되어 있지 않은 경우 원본 저장소에서 대상 저장소로 수백만 개의 행을 복사합니다. 원본에서 대상으로 중복 행을 복사하는 경우 대상 데이터베이스에서 PK 위반 오류가 발생합니다. 현재 복사 작업은 이러한 오류를 처리하는 두 가지 방법을 제공합니다. 
- 오류가 발생하면 복사 작업을 중단할 수 있습니다. 
- 내결함성을 사용하여 호환되지 않는 데이터를 건너뛰고 나머지 항목을 계속 복사할 수 있습니다. 예를 들어 이 경우에는 중복된 행을 건너뜁니다. 또한 복사 작업 내에서 세션 로그를 사용하도록 설정하여 건너뛴 데이터를 로깅할 수 있습니다. 자세한 내용은 [복사 작업의 세션 로그](copy-activity-log.md)를 참조하세요.

## <a name="copying-binary-files"></a>이진 파일 복사 

이 서비스는 이진 파일을 복사할 때 다음과 같은 내결함성 시나리오를 지원 합니다. 다음 시나리오에서 복사 작업을 중단하거나 나머지 항목을 계속 복사하도록 선택할 수 있습니다.

1. 서비스에서 복사할 파일을 다른 응용 프로그램에서 동시에 삭제 하 고 있습니다.
2. 이러한 파일 또는 폴더의 Acl에는 구성 된 연결 정보 보다 높은 권한 수준이 필요 하기 때문에 일부 특정 폴더 또는 파일은 서비스 액세스를 허용 하지 않습니다.
3. 데이터 일관성 확인 설정을 사용 하는 경우 하나 이상의 파일이 원본 및 대상 저장소 간에 일치 하는지 확인 되지 않습니다.

### <a name="configuration"></a>구성 
스토리지 저장소 간에 이진 파일을 복사하는 경우 다음과 같이 내결함성을 사용하도록 설정할 수 있습니다. 

```json
"typeProperties": { 
    "source": { 
        "type": "BinarySource", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreReadSettings", 
            "recursive": true 
            } 
    }, 
    "sink": { 
        "type": "BinarySink", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreWriteSettings" 
        } 
    }, 
    "skipErrorFile": { 
        "fileMissing": true, 
        "fileForbidden": true, 
        "dataInconsistency": true,
        "invalidFileName": true        
    }, 
    "validateDataConsistency": true, 
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {            
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    }
} 
```
속성 | Description | 허용되는 값 | 필수
-------- | ----------- | -------------- | -------- 
skipErrorFile | 데이터 이동 중에 건너뛸 오류 유형을 지정하는 속성 그룹입니다. | | 예
fileMissing | SkipErrorFile 속성 모음 내에서 키-값 쌍 중 하나를 선택 하 여 서비스에서 복사 작업을 수행 하는 동안 다른 응용 프로그램에서 삭제 되는 파일을 건너뛸지 여부를 결정 합니다. <br/> -True: 다른 애플리케이션에서 삭제할 파일을 건너뛰어 나머지 항목을 복사하려고 합니다. <br/> -False: 데이터 이동 중에 원본 저장소에서 파일이 삭제되면 복사 작업을 중단하려고 합니다. <br/>이 속성은 기본적으로 true로 설정됩니다. | True(기본값) <br/>False | 예
fileForbidden | SkipErrorFile 속성 모음 내에서 키-값 쌍 중 하나를 사용 하 여 특정 파일을 건너뛸지 여부를 결정 합니다. 이러한 파일 또는 폴더의 Acl에는 구성 된 연결 보다 높은 권한 수준이 필요 합니다. <br/> -True: 파일을 건너뛰고 나머지 항목을 복사하려고 합니다. <br/> -False: 폴더 또는 파일에 대한 권한 문제가 발생하면 복사 작업을 중단하려고 합니다. | True <br/>False(기본값) | 예
dataInconsistency | skipErrorFile 속성 모음 내에서 키-값 쌍 중 하나를 선택하여 원본 및 대상 저장소 간에 일치하지 않는 데이터를 건너뛸지 여부를 결정합니다. <br/> -True: 일치하지 않는 데이터를 건너뛰고 나머지 항목을 복사하려고 합니다. <br/> -False: 일관되지 않은 데이터가 발견되면 복사 활동을 중단하려고 합니다. <br/>이 속성은 validateDataConsistency를 True로 설정한 경우에만 유효합니다. | True <br/>False(기본값) | 예
invalidFileName | skipErrorFile 속성 모음 내에서 키-값 쌍 중 하나로, 대상 저장소에 대한 파일 이름이 잘못된 경우 특정 파일을 건너뛸지 여부를 결정합니다. <br/> \- True: 파일 이름이 잘못된 파일을 건너뛰고 나머지 항목을 복사하려고 합니다. <br/> - False: 파일 이름이 잘못된 경우 복사 작업을 중단하려고 합니다. <br/>이 속성은 스토리지 저장소에서 ADLS Gen2로 이진 파일을 복사하거나 AWS S3에서 스토리지 저장소로만 이진 파일을 복사하는 경우에 작동합니다. | True <br/>False(기본값) | 예
logSettings  | 건너뛴 개체 이름을 로깅하려는 경우 지정할 수 있는 속성 그룹입니다. | &nbsp; | 예
linkedServiceName | 세션 로그 파일을 저장할 [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) 또는 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties)의 연결된 서비스입니다. | `AzureBlobStorage` 또는 `AzureBlobFS` 형식의 연결된 서비스 이름은 로그 파일을 저장하는 데 사용되는 인스턴스를 참조합니다. | 예
경로 | 로그 파일의 경로입니다. | 로그 파일을 저장하는 데 사용할 경로를 지정합니다. 경로를 지정하지 않으면 서비스가 대신 컨테이너를 만듭니다. | 예

> [!NOTE]
> 다음은 이진 파일을 복사할 때 복사 작업에서 내결함성을 사용하는 데 있어 필수 구성 요소입니다.
> 원본 저장소에서 삭제되는 특정 파일을 건너뛰는 경우:
> - 원본 데이터 세트 및 싱크 데이터 세트는 이진 형식이어야 하며, 압축 형식은 지정할 수 없습니다. 
> - 지원되는 데이터 저장소 형식은 Azure Blob storage, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure Files, File System, FTP, SFTP, Amazon S3, Google Cloud Storage 및 HDFS입니다.
> - 원본 데이터 세트(폴더, 와일드카드 또는 파일 목록이 될 수 있음)에서 여러 파일을 지정하는 경우에만 복사 작업에서 특정 오류 파일을 건너뛸 수 있습니다. 원본 데이터 세트에서 대상으로 복사할 단일 파일이 지정된 경우 오류가 발생하면 복사 작업이 실패합니다.
>
> 원본 저장소에서 액세스가 금지될 때 특정 파일을 건너뛰는 경우:
> - 원본 데이터 세트 및 싱크 데이터 세트는 이진 형식이어야 하며, 압축 형식은 지정할 수 없습니다. 
> - 지원되는 데이터 저장소 형식은 Azure Blob storage, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure Files, SFTP, Amazon S3 및 HDFS입니다.
> - 원본 데이터 세트(폴더, 와일드카드 또는 파일 목록이 될 수 있음)에서 여러 파일을 지정하는 경우에만 복사 작업에서 특정 오류 파일을 건너뛸 수 있습니다. 원본 데이터 세트에서 대상으로 복사할 단일 파일이 지정된 경우 오류가 발생하면 복사 작업이 실패합니다.
>
> 원본 및 대상 저장소 간에 일치하지 않는 것으로 확인되는 특정 파일을 건너뛰는 경우:
> - [여기](./copy-activity-data-consistency.md) 데이터 일관성 문서에서 자세한 내용을 볼 수 있습니다.

### <a name="monitoring"></a>모니터링 

#### <a name="output-from-copy-activity"></a>복사 작업의 출력
각 복사 작업 실행의 출력을 통해 읽고 쓰고 건너뛰는 파일 수를 가져올 수 있습니다. 

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```

#### <a name="session-log-from-copy-activity"></a>복사 작업의 세션 로그

건너뛴 파일 이름을 로깅하도록 구성할 경우 이 경로(`https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`)에서 로그 파일을 확인할 수 있습니다. 

로그 파일은 csv 파일이어야 합니다. 로그 파일의 스키마는 다음과 같습니다.

열 | Description 
-------- | -----------  
타임스탬프 | 파일을 건너뛴 타임 스탬프입니다.
Level | 이 항목의 로그 수준입니다. 파일 건너뛰기를 표시하는 항목의 경우 ‘경고’ 수준입니다.
OperationName | 각 파일에 대 한 복사 작업 동작입니다. 건너뛸 파일을 지정하는 경우 ‘FileSkip’입니다.
OperationItem | 건너뛸 파일 이름입니다.
메시지 | 파일을 건너뛰는 이유를 설명하는 추가 정보입니다.

로그 파일의 예는 다음과 같습니다. 
```
Timestamp,Level,OperationName,OperationItem,Message 
2020-03-24 05:35:41.0209942,Warning,FileSkip,"bigfile.csv","File is skipped after read 322961408 bytes: ErrorCode=UserErrorSourceBlobNotExist,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=The required Blob is missing. ContainerName: https://transferserviceonebox.blob.core.windows.net/skipfaultyfile, path: bigfile.csv.,Source=Microsoft.DataTransfer.ClientLibrary,'." 
2020-03-24 05:38:41.2595989,Warning,FileSkip,"3_nopermission.txt","File is skipped after read 0 bytes: ErrorCode=AdlsGen2OperationFailed,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=ADLS Gen2 operation failed for: Operation returned an invalid status code 'Forbidden'. Account: 'adlsgen2perfsource'. FileSystem: 'skipfaultyfilesforbidden'. Path: '3_nopermission.txt'. ErrorCode: 'AuthorizationPermissionMismatch'. Message: 'This request is not authorized to perform this operation using this permission.'. RequestId: '35089f5d-101f-008c-489e-01cce4000000'..,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Operation returned an invalid status code 'Forbidden',Source=,''Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message='Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message=Operation returned an invalid status code 'Forbidden',Source=Microsoft.DataTransfer.ClientLibrary,',Source=Microsoft.DataTransfer.ClientLibrary,'." 
```
위의 로그에서 다른 응용 프로그램이 서비스를 복사할 때이 파일을 삭제 했기 때문에 bigfile.csv를 건너뜀을 확인할 수 있습니다. 사용 권한 문제로 인해 서비스에서 해당 서비스에 액세스할 수 없으므로 및 3_nopermission.txt을 건너뛰었습니다.


## <a name="copying-tabular-data"></a>테이블 형식 데이터 복사 

### <a name="supported-scenarios"></a>지원되는 시나리오
복사 작업은 호환되지 않는 테이블 형식 데이터의 검색, 건너뛰기 및 로깅에 대한 다음 세 가지 시나리오를 지원합니다.

- **원본 데이터 형식 및 싱크 원시 형식 간의 비호환성** 

    다음은 그 예입니다.  3개의 INT 형식 열을 포함하는 스키마 정의를 사용하여 Blob Storage에 있는 CSV 파일의 데이터를 SQL Database로 복사합니다. 123,456,789와 같은 숫자 데이터를 포함하는 CSV 파일 행은 싱크 저장소에 성공적으로 복사됩니다. 그러나 123,456, abc와 같은 숫자가 아닌 값을 포함하는 행을 호환되지 않는 것으로 감지하고 건너뜁니다.

- **원본과 싱크 간의 열 수 불일치**

    다음은 그 예입니다.  6개의 열이 포함된 스키마 정의를 사용하여 Blob Storage에 있는 CSV 파일의 데이터를 SQL Database로 복사합니다. 6개의 열이 포함된 CSV 파일 행은 싱크 저장소에 성공적으로 복사됩니다. 6개를 초과하는 많은 열을 포함하는 CSV 파일 행은 호환되지 않는 것으로 감지하고 건너뜁니다.

- **SQL Server/Azure SQL 데이터베이스/Azure Cosmos DB에 쓸 때 기본 키 위반**.

    다음은 그 예입니다.  SQL Server에서 SQL Database로 데이터를 복사합니다. 기본 키가 싱크 SQL Database에 정의되어 있지만 이러한 기본 키가 원본 SQL Server에 정의되어 있지 않습니다. 원본에 있는 중복된 행을 싱크로 복사할 수 없습니다. 복사 작업은 원본 데이터의 첫 번째 행만 싱크에 복사합니다. 중복된 기본 키 값을 포함하는 후속 원본 행을 호환되지 않는 것으로 감지하고 건너뜁니다.

>[!NOTE]
>- PolyBase를 사용하여 Azure Synapse Analytics로 데이터를 로드하려면 복사 작업에서 "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)"를 통해 거부 정책을 지정하여 PolyBase의 네이티브 내결함성 설정을 구성하세요. PolyBase와 호환되지 않는 행을 계속해서 아래와 같이 정상적으로 Blob 또는 ADLS로 리디렉션할 수 있습니다.
>- [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)를 호출하도록 복사 작업이 구성된 경우 이 기능이 적용되지 않습니다.
>- [SQL 싱크의 저장 프로시저](./connector-azure-sql-database.md#invoke-a-stored-procedure-from-a-sql-sink)를 호출하도록 복사 작업이 구성된 경우 이 기능이 적용되지 않습니다.

### <a name="configuration"></a>구성
다음 예제에서는 복사 작업에서 호환되지 않는 행을 건너뛰도록 구성하기 위한 JSON 정의를 제공합니다.

```json
"typeProperties": { 
    "source": { 
        "type": "AzureSqlSource" 
    }, 
    "sink": { 
        "type": "AzureSqlSink" 
    }, 
    "enableSkipIncompatibleRow": true, 
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {            
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    } 
}, 
```

속성 | Description | 허용되는 값 | 필수
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | 복사 중에 호환되지 않는 행을 건너뛸지 지정합니다. | True<br/>False(기본값) | 예
logSettings | 호환되지 않는 행을 기록하려는 경우 지정할 수 있는 속성 그룹입니다. | &nbsp; | 예
linkedServiceName | 건너뛰는 행을 포함하는 로그를 저장하는 [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) 또는 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties)의 연결된 서비스입니다. | `AzureBlobStorage` 또는 `AzureBlobFS` 형식의 연결된 서비스 이름은 로그 파일을 저장하는 데 사용되는 인스턴스를 참조합니다. | 예
경로 | 건너뛴 행을 포함하는 로그 파일의 경로입니다. | 호환되지 않는 데이터를 기록하는 데 사용하려는 경로를 지정합니다. 경로를 지정하지 않으면 서비스가 대신 컨테이너를 만듭니다. | 예

### <a name="monitor-skipped-rows"></a>건너뛴 행 모니터링
복사 작업 실행이 완료되면 복사 작업 출력에서 건너뛴 행의 수를 볼 수 있습니다.

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```

호환되지 않는 행을 로깅하도록 구성할 경우 이 경로(`https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`)에서 로그 파일을 확인할 수 있습니다. 

로그 파일은 csv 파일입니다. 로그 파일의 스키마는 다음과 같습니다.

열 | Description 
-------- | -----------  
타임스탬프 | 호환 되지 않는 행을 건너뛴 타임 스탬프입니다.
Level | 이 항목의 로그 수준입니다. 이 항목이 건너뛴 행을 표시하는 경우 '경고' 수준입니다.
OperationName | 각 행에 대 한 복사 작업 동작 동작입니다. 호환되지 않는 특정 행을 건너뛰었음을 지정하는 경우 'TabularRowSkip'입니다.
OperationItem | 원본 데이터 저장소에서 건너뛴 행입니다.
메시지 | 이 특정 행이 호환되지 않는 이유를 보여 주는 추가 정보입니다.


로그 파일 내용의 예는 다음과 같습니다.

```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:32.2586581, Warning, TabularRowSkip, """data1"", ""data2"", ""data3""," "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'." 
2020-02-26 06:22:33.2586351, Warning, TabularRowSkip, """data4"", ""data5"", ""data6"",", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)." 
```

위의 샘플 로그 파일을 보면 원본 저장소에서 대상 저장소로 형식을 변환할 때 발생하는 문제로 인해 "data1, data2, data3"이라는 행을 건너뛴 것을 알 수 있습니다. 원본 저장소에서 대상 저장소로의 PK 위반 문제로 인해 "data4, data5, data6"라는 또 다른 행도 건너뛰었습니다. 


## <a name="copying-tabular-data-legacy"></a>테이블 형식 데이터 복사(레거시):

다음은 테이블 형식 데이터만 복사하기 위해 내결함성을 설정하는 레거시 방식입니다. 새 파이프라인이나 활동을 만드는 경우에는 [여기](#copying-tabular-data)에서 시작하는 것이 좋습니다.

### <a name="configuration"></a>구성
다음 예제에서는 복사 작업에서 호환되지 않는 행을 건너뛰도록 구성하기 위한 JSON 정의를 제공합니다.

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

속성 | Description | 허용되는 값 | 필수
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | 복사 중에 호환되지 않는 행을 건너뛸지 지정합니다. | True<br/>False(기본값) | 예
redirectIncompatibleRowSettings | 호환되지 않는 행을 기록하려는 경우 지정할 수 있는 속성 그룹입니다. | &nbsp; | 예
linkedServiceName | 건너뛰는 행을 포함하는 로그를 저장하는 [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) 또는 [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties)의 연결된 서비스입니다. | `AzureStorage` 또는 `AzureDataLakeStore` 형식의 연결된 서비스 이름은 사용자가 로그 파일을 저장하는 데 사용하려는 인스턴스를 참조합니다. | 예
경로 | 건너뛴 행을 포함하는 로그 파일의 경로입니다. | 호환되지 않는 데이터를 기록하는 데 사용하려는 경로를 지정합니다. 경로를 지정하지 않으면 서비스가 대신 컨테이너를 만듭니다. | 예

### <a name="monitor-skipped-rows"></a>건너뛴 행 모니터링
복사 작업 실행이 완료되면 복사 작업 출력에서 건너뛴 행의 수를 볼 수 있습니다.

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
호환되지 않는 행을 기록하도록 구성할 경우 `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` 경로에 로그 파일이 생깁니다. 

로그 파일은 csv 파일만 될 수 있습니다. 건너뛴 원래 데이터는 필요한 경우 쉼표를 열 구분 기호로 사용하여 기록됩니다. 로그 파일에서 원래의 원본 데이터에 "ErrorCode" 및 "ErrorMessage" 등의 두 열을 더할 수 있습니다. 여기서 비호환의 근본 원인을 파악할 수 있습니다. ErrorCode 및 ErrorMessage는 큰따옴표를 사용하여 인용됩니다. 

로그 파일 내용의 예는 다음과 같습니다.

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>다음 단계
다른 복사 작업 문서를 참조하세요.

- [복사 작업 개요](copy-activity-overview.md)
- [복사 작업 성능](copy-activity-performance.md)
