---
title: 복사 활동의 데이터 일관성 확인
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory에서 복사 활동의 데이터 일관성 확인을 사용하도록 설정하는 방법을 알아봅니다.
author: dearandyxu
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: yexu
ms.openlocfilehash: 3e2bcf4e267a93c9504dfc22e1308f0a4dba890d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642534"
---
#  <a name="data-consistency-verification-in-copy-activity"></a>복사 활동의 데이터 일관성 확인

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

원본 저장소에서 대상 저장소로 데이터를 이동하는 경우 Azure Data Factory 복사 활동은 추가적인 데이터 일관성 확인을 수행하여 데이터가 원본 저장소에서 대상 저장소로 복사될 뿐 아니라 원본 저장소와 대상 저장소 간에 일관된 것으로 확인되는지 확인하는 옵션을 제공합니다. 데이터 이동 중에 일관되지 않은 파일이 발견되면 복사 작업을 중단하거나, 일관되지 않은 파일을 건너뛰도록 내결함성 설정을 사용하도록 설정하여 나머지를 계속 복사합니다. 복사 작업에서 세션 로그 설정을 사용하도록 설정하여 건너뛴 파일 이름을 가져올 수 있습니다. 자세한 내용은 [복사 작업의 세션 로그](copy-activity-log.md)를 참조하세요.

## <a name="supported-data-stores-and-scenarios"></a>지원되는 데이터 저장소 및 시나리오

-   FTP, sFTP, HTTP를 제외한 모든 연결에서 데이터 일관성 확인을 지원합니다. 
-   준비 복사 시나리오에서는 데이터 일관성 확인을 지원하지 않습니다.
-   이진 파일을 복사하는 경우, 데이터 일관성 확인은 복사 작업에 'PreserveHierarchy' 동작이 설정된 경우에만 사용할 수 있습니다.
-   데이터 일관성 확인을 사용하도록 설정된 단일 복사 작업의 이진 파일 여러 개를 복사하는 경우, 일관성 없는 파일을 건너뛰도록 하는 내결함성 설정을 사용하여 해당 복사 작업을 취소할 지 나머지를 계속해서 복사할 지를 선택할 수 있는 옵션을 사용할 수 있습니다. 
-   데이터 일관성 확인을 사용하도록 설정된 단일 복사 작업의 테이블을 복사하는 경우, 원본에서 읽은 행 개수가 대상으로 복사된 행 개수에 호환되지 않는 건너뛴 행 개수를 더한 값과 다르면 복사 작업이 실패합니다.


## <a name="configuration"></a>구성
다음 예제에서는 복사 활동에서 데이터 일관성 확인을 사용하기 위한 JSON 정의를 제공합니다. 

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
    "validateDataConsistency": true, 
    "skipErrorFile": { 
        "dataInconsistency": true 
    }, 
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
validateDataConsistency | 이 속성에 true를 설정하면 이진 파일을 복사하는 경우의 복사 작업은 원본 저장소에서 대상 저장소로 복사된 각 이진 파일의 파일 크기, lastModifiedDate 및 MD5 체크섬을 확인하여 원본 저장소와 대상 저장소 간 데이터 일관성을 확인합니다. 테이블 형식 데이터를 복사하는 경우, 원본에서 읽은 전체 행 개수가 대상으로 복사된 행 개수에 호환되지 않는 건너뛴 행 개수를 더한 값과 같은지 확인하기 위해 복사 작업은 작업 완료 후에 전체 행 개수를 확인합니다. 이 옵션을 사용하도록 설정하면 복사 성능에 영향을 줍니다.  | True<br/>False(기본값) | 예
dataInconsistency | skipErrorFile 속성 모음 내에서 키-값 쌍 중 하나를 선택하여 일관되지 않은 파일을 건너뛸지 여부를 결정합니다. <br/> \- True: 일관되지 않은 파일을 건너뛰고 나머지를 복사하려고 합니다.<br/> - False: 일관되지 않은 파일이 발견되면 복사 작업을 중단하려고 합니다.<br/>이 속성은 이진 파일을 복사할 때 validateDataConsistency를 True로 설정한 경우에만 유효합니다.  | True<br/>False(기본값) | 예
logSettings | 세션 로그에서 건너뛴 파일을 기록할 수 있도록 지정할 수 있는 속성 그룹입니다. | | 예
linkedServiceName | 세션 로그 파일을 저장할 [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) 또는 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties)의 연결된 서비스입니다. | 로그 파일을 저장하는 데 사용되는 인스턴스를 참조하는 `AzureBlobStorage` 또는 `AzureBlobFS` 형식의 연결된 서비스 이름입니다. | 예
경로 | 로그 파일의 경로입니다. | 로그 파일을 저장할 경로를 지정합니다. 경로를 지정하지 않으면 서비스가 대신 컨테이너를 만듭니다. | 예

>[!NOTE]
>- 이진 파일을 Azure Blob이나 Azure Data Lake Storage Gen2에서 복사하거나 해당 위치로 복사하는 경우, ADF는 [Azure Blob API](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions?view=azure-dotnet-legacy&preserve-view=true)와 [Azure Data Lake Storage Gen2 API](/rest/api/storageservices/datalakestoragegen2/path/update#request-headers)를 사용하여 차단 수준 MD5 체크섬 확인을 실행합니다. 파일의 ContentMD5가 Azure Blob 또는 Azure Data Lake Storage Gen2에 데이터 원본으로 존재하는 경우, ADF는 해당 파일도 마찬가지로 읽은 후에 파일 수준 MD5 체크섬 확인을 실행합니다. Azure Blob 또는 Azure Data Lake Storage Gen2를 데이터 대상으로 하여 파일을 복사한 후, ADF는 ContentMD5를 Azure Blob 또는 Azure Data Lake Storage Gen2에 기록하여 데이터 일관성 확인을 위해 다운스트림 애플리케이션에서 이것을 사용할 수 있도록 합니다.
>- 스토리지 저장소 간의 이진 파일 복사 시 ADF는 파일 크기 확인을 실행합니다.

## <a name="monitoring"></a>모니터링

### <a name="output-from-copy-activity"></a>복사 활동의 출력
복사 활동이 완전히 실행된 후 각 복사 활동 실행의 출력에서 데이터 일관성 확인 결과를 확인할 수 있습니다.

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
“dataConsistencyVerification 속성”에서 데이터 일관성 확인의 세부 정보를 확인할 수 있습니다.

**VerificationResult** 값: 
-   **Verified**:  복사된 데이터가 원본 저장소와 대상 저장소 간에 일관된 것으로 확인되었습니다. 
-   **NotVerified**: 복사 활동에서 validateDataConsistency를 사용하도록 설정하지 않았으므로 복사된 데이터가 일관된 것으로 확인되지 않았습니다. 
-   **Unsupported**: 이 특정 복사 쌍의 경우 데이터 일관성 확인이 지원되지 않으므로 복사된 데이터가 일관된 것으로 확인되지 않았습니다. 

**InconsistentData** 값: 
-   **Found**: ADF 복사 활동이 일관되지 않은 데이터를 찾았습니다. 
-   **건너뜀**: ADF 복사 활동이 일관되지 않은 데이터를 찾고 건너뛰었습니다. 
-   **없음**: ADF 복사 활동이 일관되지 않은 데이터를 찾지 않았습니다. 데이터가 원본 저장소와 대상 저장소 간에 일관된 것으로 확인되었거나 복사 활동에서 validateDataConsistency를 사용하지 않도록 설정했기 때문일 수 있습니다. 

### <a name="session-log-from-copy-activity"></a>복사 활동의 세션 로그

일관되지 않은 파일을 기록하도록 구성할 경우 `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` 경로에서 로그 파일을 찾을 수 있습니다.  로그 파일은 csv 파일입니다. 

로그 파일의 스키마는 다음과 같습니다.

열 | Description 
-------- | -----------  
타임스탬프 | ADF가 일관되지 않은 파일을 건너뛴 타임스탬프입니다.
Level | 이 항목의 로그 수준입니다. 파일 건너뛰기를 표시하는 항목의 경우 ‘경고’ 수준입니다.
OperationName | 각 파일에 대한 ADF 복사 활동 동작입니다. 건너뛸 파일을 지정하는 경우 ‘FileSkip’입니다.
OperationItem | 건너뛸 파일 이름입니다.
메시지 | 파일을 건너뛰는 이유를 설명하는 추가 정보입니다.

로그 파일의 예는 다음과 같습니다. 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
위 로그 파일에서 원본 저장소와 대상 저장소 간에 일관된 것으로 확인되지 않았기 때문에 sample1.csv를 건너뛰었음을 알 수 있습니다. sample1.csv가 일관되지 않은 이유가 ADF 복사 활동이 복사하는 동안 동시에 다른 애플리케이션이 해당 파일을 변경하고 있었기 때문임을 나타내는 자세한 정보를 확인할 수 있습니다. 



## <a name="next-steps"></a>다음 단계
다른 복사 작업 문서를 참조하세요.

- [복사 작업 개요](copy-activity-overview.md)
- [복사 작업 내결함성](copy-activity-fault-tolerance.md)