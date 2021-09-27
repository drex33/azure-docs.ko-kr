---
title: 커넥터 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics의 커넥터 문제를 해결하는 방법을 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 09/09/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 0808b52c777389cfdf641094fd152ee9f11b482f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782725"
---
# <a name="troubleshoot-azure-data-factory-and-azure-synapse-analytics-connectors"></a>Azure Data Factory 및 Azure Synapse Analytics 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse 커넥터의 문제를 해결하는 일반적인 방법을 설명합니다.

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code-azurebloboperationfailed"></a>오류 코드: AzureBlobOperationFailed

- **메시지**: “Blob 작업에 실패했습니다. ContainerName: %containerName;, 경로: %path;.”

- **원인**: Blob Storage 작업에서 문제가 발생했습니다.

- **권장 사항**: 오류 세부 정보를 확인하려면 [Blob Storage 오류 코드](/rest/api/storageservices/blob-service-error-codes)를 참조하세요. 추가 도움이 필요한 경우 Blob Storage 팀으로 문의하세요.


### <a name="invalid-property-during-copy-activity"></a>복사 작업 중에 잘못된 속성이 발견됨

- **메시지**: `Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **원인**: 데이터 세트에 정의된 유형이 복사 작업에 정의된 원본 또는 싱크 유형과 일치하지 않습니다.

- **해결 방법**: 데이터 세트 또는 파이프라인 JSON 정의를 편집하여 유형이 일치하도록 만든 다음 배포를 다시 실행합니다.

### <a name="error-code-fipsmodeisnotsupport"></a>오류 코드: FIPSModeIsNotSupport

- **메시지**: `Fail to read data form Azure Blob Storage for Azure Blob connector needs MD5 algorithm which can't co-work with FIPS mode. Please change diawp.exe.config in self-hosted integration runtime install directory to disable FIPS policy following https://docs.microsoft.com/en-us/dotnet/framework/configure-apps/file-schema/runtime/enforcefipspolicy-element.`

- **원인**: 자체 호스팅 통합 런타임이 설치된 VM에서 FIPS 정책을 사용하도록 설정되어 있습니다.

- **권장 사항**: 자체 호스팅 통합 런타임이 설치된 VM에서 FIPS 모드를 사용하지 않도록 설정합니다. Windows에서는 FIPS 모드가 권장되지 않습니다.

### <a name="error-code-azureblobinvalidblocksize"></a>오류 코드: AzureBlobInvalidBlockSize

- **메시지**: `Block size should between %minSize; MB and 100 MB.`

- **원인**: 블록 크기가 blob 제한을 초과합니다.

### <a name="error-code-azurestorageoperationfailedconcurrentwrite"></a>오류 코드: AzureStorageOperationFailedConcurrentWrite

- **메시지**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`

- **원인**: 동시 복사 작업이 여러 개 실행되고 있거나 애플리케이션이 동일한 파일에 쓰고 있습니다.

### <a name="error-code-azureappendblobconcurrentwriteconflict"></a>오류 코드: AzureAppendBlobConcurrentWriteConflict

- **메시지**: `Detected concurrent write to the same append blob file, it's possible because you have multiple concurrent copy activities runs or applications writing to the same file '%name;'. Please check your ADF configuration and retry.`

- **원인**: 동시 쓰기 요청이 여러 개 발생하고 있으므로 파일 내용에 충돌이 발생합니다.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>오류 메시지: 요청 크기가 너무 큽니다.

- **증상**: 기본 쓰기 일괄 처리 크기를 사용하여 Azure Cosmos DB로 데이터를 복사할 때 다음과 같은 오류가 표시됩니다. `Request size is too large.`

- **원인**: Azure Cosmos DB에서는 단일 요청의 크기가 2MB로 제한됩니다. 수식은 ‘요청 크기 = 단일 문서 크기 \* 쓰기 일괄 처리 크기’입니다. 문서 크기가 큰 경우 기본 동작으로 인해 요청 크기가 너무 커집니다. 쓰기 일괄 처리 크기를 조정할 수 있습니다.

- **해결 방법**: 복사 작업 싱크에서 ‘쓰기 일괄 처리 크기’ 값을 줄입니다(기본값은 10000입니다).

### <a name="error-message-unique-index-constraint-violation"></a>오류 메시지: 고유한 인덱스 제약 조건 위반

- **증상**: Azure Cosmos DB로 데이터를 복사할 때 다음과 같은 오류가 표시됩니다.

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **원인**: 다음 두 가지 가능한 원인이 있습니다.

    - 원인 1: 쓰기 동작으로 **Insert** 를 사용한 경우, 이 오류는 원본 데이터에 ID가 동일한 행 또는 개체가 있음을 의미합니다.
    - 원인 2: 쓰기 동작으로 **Upsert** 를 사용했고 컨테이너에 대해 다른 고유 키를 설정한 경우, 이 오류는 원본 데이터에 ID는 서로 다르지만 정의된 고유 키에 대해 값이 같은 행 또는 개체가 있음을 의미합니다.

- **해결 방법**: 

    - 원인 1의 경우 쓰기 동작으로 **Upsert** 를 설정합니다.
    - 원인 2의 경우 각 문서의 정의된 고유 키 값이 서로 다른지 확인합니다.

### <a name="error-message-request-rate-is-large"></a>오류 메시지: 요청 빈도가 높습니다.

- **증상**: Azure Cosmos DB로 데이터를 복사할 때 다음과 같은 오류가 표시됩니다.

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **원인**: 사용된 RU(요청 단위)의 개수가 Azure Cosmos DB에 구성된 사용 가능한 RU의 개수보다 큽니다. Azure Cosmos DB가 RU를 계산하는 방법을 알아보려면 [Azure Cosmos DB의 요청 단위](../cosmos-db/request-units.md#request-unit-considerations)를 참조하세요.

- **해결 방법**: 다음 두 가지 방법 중 하나를 시도해 봅니다.

    - Azure Cosmos DB에서 ‘컨테이너 RU’의 개수를 늘립니다. 이 솔루션을 사용하면 복사 작업 성능이 개선되지만 Azure Cosmos DB에서 더 많은 비용이 발생합니다. 
    - *writeBatchSize* 를 더 작은 값으로 줄이고(예: 1000) *parallelCopies* 를 더 작은 값으로 줄입니다(예: 1). 이 솔루션을 사용하면 복사 실행 성능이 줄어들지만 Azure Cosmos DB에서 더 많은 비용이 발생하지 않습니다.

### <a name="columns-missing-in-column-mapping"></a>열 매핑에서 열이 누락됨

- **증상**: Azure Cosmos DB에 대해 열 매핑을 위한 스키마를 가져올 때 일부 열이 누락됩니다. 

- **원인**: Azure Data Factory 및 Synapse 파이프라인은 처음 10개의 Azure Cosmos DB 문서에서 스키마를 유추합니다. 일부 문서 열 또는 속성이 값을 포함하지 않을 경우 스키마를 감지하지 못하여 스키마가 표시되지 않습니다.

- **해결 방법**: 다음 코드에 나와 있는 것처럼 쿼리를 수정하여 결과 세트에 열 값이 빈 값으로 표시되도록 할 수 있습니다. (처음 10개의 문서에서 *impossible* 열이 누락되었다고 가정합니다.) 또는 매핑을 위한 열을 수동으로 추가할 수 있습니다.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>오류 메시지: 판독기에 대한 GuidRepresentation이 CSharpLegacy입니다.

- **증상**: Azure Cosmos DB MongoAPI 또는 MongoDB에서 UUID(Universally Unique Identifier) 필드가 있는 데이터를 복사할 때 다음과 같은 오류가 표시됩니다.

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **원인**: BSON(이진 JSON)에서 UUID를 표현하는 방법에는 UuidStardard와 UuidLegacy, 이렇게 두 가지가 있습니다. 기본적으로 UuidLegacy는 데이터를 읽는 데 사용됩니다. MongoDB의 UUID 데이터가 UuidStandard인 경우 오류가 발생합니다.

- **해결 방법**: MongoDB 연결 문자열에서 *uuidRepresentation=standard* 옵션을 추가합니다. 자세한 내용은 [MongoDB 연결 문자열](connector-mongodb.md#linked-service-properties)을 참조하세요.
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB(SQL API)

### <a name="error-code-cosmosdbsqlapioperationfailed"></a>오류 코드: CosmosDbSqlApiOperationFailed

- **메시지**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **원인**: CosmosDbSqlApi 작업에서 문제가 발생했습니다.

- **권장 사항**: 오류 세부 정보를 확인하려면 [Azure Cosmos DB 도움말 문서](../cosmos-db/troubleshoot-dot-net-sdk.md)를 참조하세요. 추가 도움이 필요한 경우 Azure Cosmos DB 팀으로 문의하세요.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>오류 메시지: 기본 연결이 닫혔습니다. SSL/TLS 보안 채널에 대한 신뢰 관계를 설정할 수 없습니다.

- **증상**: 다음 오류가 발생하여 복사 작업이 실패합니다. 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **원인**: TLS 핸드셰이크 중에 인증서 유효성 검사가 실패했습니다.

- **해결 방법**: 임시 방편으로 스테이징된 복사본을 사용하여 Azure Data Lake Storage Gen1에 대해 TLS(전송 계층 보안) 유효성 검사를 건너뜁니다. 이 문제를 재현하여 네트워크 모니터(netmon) 추적을 수집한 다음 네트워크 팀의 지원을 받아 로컬 네트워크 구성을 확인해야 합니다.

    :::image type="content" source="./media/connector-troubleshoot-guide/adls-troubleshoot.png" alt-text="문제 해결을 위한 Azure Data Lake Storage Gen1 연결 다이어그램.":::


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>오류 메시지: 원격 서버에서 다음과 같은 오류를 반환했습니다. (403) 사용할 수 없음

- **증상**: 다음 오류가 발생하여 복사 작업이 실패합니다. 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **원인**: 한 가지 가능한 원인은 사용자가 사용하는 서비스 주체 또는 관리 ID에 특정 폴더 또는 파일에 대한 액세스 권한이 없는 것입니다.

- **해결 방법**: 복사하려는 모든 폴더와 하위 폴더에 적절한 권한을 부여합니다. 자세한 내용은 [Azure Data Lake Storage Gen1 간에 데이터 복사](connector-azure-data-lake-store.md#linked-service-properties)를 참조하세요.

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>오류 메시지: 서비스 주체를 사용하여 토큰에 액세스하지 못했습니다. ADAL 오류: service_unavailable

- **증상**: 다음 오류가 발생하여 복사 작업이 실패합니다.

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **원인**: Azure Active Directory에서 소유하고 있는 STS(서비스 토큰 서버)를 사용할 수 없는 경우(즉, 다른 작업 때문에 요청을 처리할 수 없는 경우) HTTP 오류 503을 반환합니다. 

- **해결 방법**: 몇 분 후에 복사 작업을 다시 실행합니다.


## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>오류 코드: ADLSGen2OperationFailed

- **메시지**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **원인 및 권장 사항**: 오류의 원인이 여러 가지일 수 있습니다. 아래 목록에서 가능한 원인 분석과 권장 사항을 확인하세요.

  | 원인 분석                                               | 권장                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Azure Data Lake Storage Gen2가 작업이 실패했다는 오류를 throw하는 경우.| Azure Data Lake Storage Gen2에서 throw한 상세한 오류 메시지를 확인합니다. 일시적인 오류인 경우 작업을 다시 시도합니다. 추가 도움이 필요한 경우 Azure Storage 지원으로 문의하세요. 이때 오류 메시지에 표시된 요청 ID를 알려 주세요. |
  | 오류 메시지에 “Forbidden”이라는 문자열이 포함된 경우, 사용 중인 서비스 주체 또는 관리 ID에 Azure Data Lake Storage Gen2에 대한 액세스 권한이 없을 수 있습니다. | 오류를 해결하려면 [Azure Data Lake Storage Gen2에서 데이터 복사 및 변환](./connector-azure-data-lake-storage.md#service-principal-authentication)을 참조하세요. |
  | 오류 메시지에 “InternalServerError”라는 문자열이 포함된 경우, 이 오류는 Azure Data Lake Storage Gen2에서 반환한 것입니다. | 일시적인 오류로 인해 오류가 발생한 것일 수 있습니다. 그렇다면 작업을 다시 시도합니다. 문제가 지속되면 Azure Storage 지원에 문의하고 오류 메시지에 표시된 요청 ID를 알려 주세요. |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>Azure Data Lake Storage Gen2 계정에 대한 요청에서 시간 제한 오류가 발생함

- **Message**: 
  * 오류 코드 = `UserErrorFailedBlobFSOperation`
  * 오류 메시지 = `BlobFS operation failed for: A task was canceled.`

- **원인**: 이 문제의 원인은 Azure Data Lake Storage Gen2 싱크 시간 제한 오류입니다. 이 오류는 주로 자체 호스팅 IR(통합 런타임) 머신에서 발생합니다.

- **권장 사항**: 

    - 가능한 경우 자체 호스팅 IR 머신과 대상 Azure Data Lake Storage Gen2 계정을 동일한 지역에 배치합니다. 이렇게 하면 임의의 시간 제한 오류를 방지하고 더 나은 성능을 얻을 수 있습니다.

    - ExpressRoute와 같은 특수한 네트워크 설정이 있는지 살펴보고, 네트워크의 대역폭이 충분한지 확인합니다. 전체적인 대역폭이 낮다면 자체 호스팅 IR 동시 작업 설정을 줄이는 것이 좋습니다. 이렇게 하면 여러 동시 작업 간의 네트워크 리소스 경합을 방지할 수 있습니다.

    - 파일 크기가 보통이거나 작은 경우에는 비이진 복사본에 더 작은 블록 크기를 사용하여 시간 제한 오류를 방지할 수 있습니다. 자세한 내용은 [Blob Storage Put Block](/rest/api/storageservices/put-block)을 참조하세요.

       사용자 지정 블록 크기를 지정하려면 다음과 같이 JSON 파일 편집기에서 속성을 편지합니다.
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

        
## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL

### <a name="error-code-azurepostgresqlnpgsqldatatypenotsupported"></a>오류 코드: AzurePostgreSqlNpgsqlDataTypeNotSupported

- **메시지**: `The data type of the chosen Partition Column, '%partitionColumn;', is '%dataType;' and this data type is not supported for partitioning.`

- **권장 사항**: int, bigint, smallint, serial, bigserial, smallserial, 표준 시간대가 있거나 없는 타임스탬프, 표준 시간대 또는 날짜 데이터 형식이 없는 시간을 사용하여 파티션 열을 선택합니다.

### <a name="error-code-azurepostgresqlnpgsqlpartitioncolumnnamenotprovided"></a>오류 코드: AzurePostgreSqlNpgsqlPartitionColumnNameNotProvided

- **메시지**: `Partition column name must be specified.`

- **원인**: 파티션 열 이름이 제공되지 않았으며 자동으로 결정될 수 없습니다.
                  
## <a name="azure-files-storage"></a>Azure Files 스토리지

### <a name="error-code-azurefileoperationfailed"></a>오류 코드: AzureFileOperationFailed

- **메시지**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **원인**: Azure Files 스토리지 작업에서 문제가 발생했습니다.

- **권장 사항**: 오류 세부 정보를 확인하려면 [Azure Files 도움말](/rest/api/storageservices/file-service-error-codes)을 참조하세요. 추가 도움이 필요한 경우 Azure Files 팀으로 문의하세요.


## <a name="azure-synapse-analytics-azure-sql-database-and-sql-server"></a>Azure Synapse Analytics, Azure SQL Database 및 SQL Server

### <a name="error-code-sqlfailedtoconnect"></a>오류 코드: SqlFailedToConnect

- **메시지**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **원인 및 권장 사항**: 오류의 원인이 여러 가지일 수 있습니다. 아래 목록에서 가능한 원인 분석과 권장 사항을 확인하세요.

    | 원인 분석                                               | 권장                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Azure SQL의 경우 오류 메시지에 “SqlErrorNumber=47073”이라는 문자열이 포함되어 있으면 연결 설정에서 공용 네트워크 액세스가 거부되었음을 의미합니다. | Azure SQL 방화벽에서 **공용 네트워크 액세스 거부** 옵션을 *아니요* 로 설정합니다. 자세한 내용은 [Azure SQL 연결 설정](../azure-sql/database/connectivity-settings.md#deny-public-network-access)을 참조하세요. |
    | Azure SQL의 경우 오류 메시지에 “SqlErrorNumber=[errorcode]”와 같은 SQL 오류 코드가 포함된 경우 Azure SQL 문제 해결 가이드를 참조하세요. | 권장 사항을 보려면 [Azure SQL Database 및 Azure SQL Managed Instance 연결 문제 및 기타 오류 문제 해결](../azure-sql/database/troubleshoot-common-errors-issues.md)을 참조하세요. |
    | 방화벽 허용 목록에 포트 1433이 있는지 확인합니다. | 자세한 내용은 [SQL Server에서 사용하는 포트](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-)를 참조하세요. |
    | 오류 메시지에 “SqlException”이라는 문자열이 포함되어 있으면 SQL Database는 특정 작업이 실패했음을 나타내는 오류를 발생시킵니다. | 자세한 내용을 보려면 [데이터베이스 엔진 오류](/sql/relational-databases/errors-events/database-engine-events-and-errors)에서 SQL 오류 코드를 검색하세요. 추가 도움이 필요한 경우 Azure SQL 지원으로 문의하세요. |
    | 일시적인 문제(예: 불안정한 네트워크 연결)인 경우 작업 정책에 retry를 추가해 봅니다. | 자세한 내용은 [파이프라인 및 활동](./concepts-pipelines-activities.md#activity-policy)을 참조하세요. |
    | Azure SQL Database에 연결하려고 할 때 오류 메시지가 “IP 주소가 ‘...’인 클라이언트가 서버에 액세스할 수 없습니다”라는 문자열을 포함하는 경우 이 오류는 일반적으로 Azure SQL Database 방화벽 문제 때문에 발생합니다. | Azure SQL Server 방화벽 구성에서 **Azure 서비스 및 리소스가 이 서버에 액세스할 수 있도록 허용** 옵션을 사용하도록 설정합니다. 자세한 내용은 [Azure SQL Database 및 Azure Synapse IP 방화벽 규칙](../azure-sql/database/firewall-configure.md)을 참조하세요. |
    
### <a name="error-code-sqloperationfailed"></a>오류 코드: SqlOperationFailed

- **메시지**: `A database operation failed. Please search error to get more details.`

- **원인 및 권장 사항**: 오류의 원인이 여러 가지일 수 있습니다. 아래 목록에서 가능한 원인 분석과 권장 사항을 확인하세요.

    | 원인 분석                                               | 권장                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 오류 메시지에 “SqlException”이라는 문자열이 포함되어 있으면 SQL Database는 특정 작업이 실패했음을 나타내는 오류를 throw합니다. | SQL 오류가 명확하지 않은 경우 데이터베이스를 최신 호환성 수준 ‘150’으로 변경해 보세요. 최신 버전의 SQL 오류를 throw할 수 있습니다. 자세한 내용은 [설명서](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat)를 참조하세요. <br/> SQL 문제를 해결하는 방법에 대한 자세한 내용을 보려면 [데이터베이스 엔진 오류](/sql/relational-databases/errors-events/database-engine-events-and-errors)에서 SQL 오류 코드를 검색하세요. 추가 도움이 필요한 경우 Azure SQL 지원으로 문의하세요. |
    | 오류 메시지에 “PdwManagedToNativeInteropException”이라는 문자열이 포함된 경우 이 오류는 일반적으로 원본 및 싱크 열 크기의 불일치로 인해 발생합니다. | 원본 및 싱크 열의 크기를 확인합니다. 추가 도움이 필요한 경우 Azure SQL 지원으로 문의하세요. |
    | 오류 메시지에 “InvalidOperationException”이라는 문자열이 포함된 경우 일반적으로 이 오류는 잘못된 입력 데이터로 인해 발생합니다. | 문제가 발생한 행을 확인하려면 복사 작업에서 내결함성 기능을 사용하도록 설정하세요. 이렇게 하면 추가 조사를 위해 문제가 있는 행을 스토리지로 리디렉션할 수 있습니다. 자세한 내용은 [복사 작업 내결함성](./copy-activity-fault-tolerance.md)을 참조하세요. |


### <a name="error-code-sqlunauthorizedaccess"></a>오류 코드: SqlUnauthorizedAccess

- **메시지**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **원인**: 자격 증명이 올바르지 않거나 로그인 계정이 SQL 데이터베이스에 액세스할 수 없습니다.

- **권장 사항**: 로그인 계정에 SQL 데이터베이스에 액세스할 수 있는 권한이 있는지 확인합니다.


### <a name="error-code-sqlopenconnectiontimeout"></a>오류 코드: SqlOpenConnectionTimeout

- **메시지**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **원인**: 이 문제는 일시적인 SQL 데이터베이스 오류일 수 있습니다.

- **권장 사항**: 작업을 다시 시도하여 연결된 서비스 연결 문자열을 더 큰 연결 시간 제한 값으로 업데이트합니다.


### <a name="error-code-sqlautocreatetabletypemapfailed"></a>오류 코드: SqlAutoCreateTableTypeMapFailed

- **메시지**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **원인**: 자동 생성 테이블이 원본 요구 사항을 충족하지 않습니다.

- **권장 사항**: ‘매핑’에서 열 형식을 업데이트하거나 대상 서버에서 수동으로 싱크 테이블을 만듭니다.


### <a name="error-code-sqldatatypenotsupported"></a>오류 코드: SqlDataTypeNotSupported

- **메시지**: `A database operation failed. Check the SQL errors.`

- **원인**: 문제가 SQL 원본에서 발생했고 오류가 SqlDateTime 오버플로와 관련된 경우 데이터 값이 논리 형식 범위(1/1/1753 오전 12:00:00~12/31/9999 오후 11:59:59)를 초과하는 것입니다.

- **권장 사항**: 원본 SQL 쿼리에서 형식을 문자열로 캐스트하거나 복사 작업 열 매핑에서 열 형식을 *String* 으로 변경합니다.

- **원인**: 문제가 SQL 싱크에서 발생했고 오류가 SqlDateTime 오버플로와 관련된 경우 데이터 값이 싱크 테이블의 허용되는 범위를 초과하는 것입니다.

- **권장 사항**: 싱크 테이블에서 해당 열 형식을 *datetime2* 형식으로 업데이트합니다.


### <a name="error-code-sqlinvaliddbstoredprocedure"></a>오류 코드: SqlInvalidDbStoredProcedure

- **메시지**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **원인**: 지정된 저장 프로시저가 잘못되었습니다. 저장 프로시저가 데이터를 반환하지 않는 것이 원인일 수 있습니다.

- **권장 사항**: SQL 도구를 사용하여 저장 프로시저의 유효성을 검사합니다. 저장 프로시저가 데이터를 반환할 수 있는지 확인합니다.


### <a name="error-code-sqlinvaliddbquerystring"></a>오류 코드: SqlInvalidDbQueryString

- **메시지**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **원인**: 지정된 SQL 쿼리가 잘못되었습니다. 쿼리가 데이터를 반환하지 않는 것이 원인일 수 있습니다.

- **권장 사항**: SQL 도구를 사용하여 SQL 쿼리의 유효성을 검사합니다. 쿼리가 데이터를 반환할 수 있는지 확인합니다.


### <a name="error-code-sqlinvalidcolumnname"></a>오류 코드: SqlInvalidColumnName

- **메시지**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **원인**: 열을 찾을 수 없습니다. 구성이 잘못된 것일 수 있습니다.

- **권장 사항**: 쿼리의 열, 데이터 세트의 *structure* 및 작업의 *mappings* 를 확인합니다.


### <a name="error-code-sqlbatchwritetimeout"></a>오류 코드: SqlBatchWriteTimeout

- **메시지**: `Timeouts in SQL write operation.`

- **원인**: 일시적인 SQL 데이터베이스 오류로 인한 문제일 수 있습니다.

- **해결 방법**: 작업을 다시 시도합니다. 문제가 지속되면 Azure SQL 지원으로 문의하세요.


### <a name="error-code-sqlbatchwritetransactionfailed"></a>오류 코드: SqlBatchWriteTransactionFailed

- **메시지**: `SQL transaction commits failed.`

- **원인**: 예외 세부 정보에 지속적으로 트랜잭션 시간이 초과되었다고 표시되는 경우 통합 런타임과 데이터베이스 간의 네트워크 대기 시간이 기본 임계값인 30초보다 높은 것입니다.

- **권장 사항**: SQL에 연결된 서비스 연결 문자열을 120보다 크거나 값은 ‘연결 시간 제한’ 값으로 업데이트하고 작업을 다시 실행합니다.

- **원인**: 예외 세부 정보에 간헐적으로 SQL 연결이 끊어졌다고 표시되는 경우 일시적인 네트워크 오류 또는 SQL 데이터베이스 쪽 문제일 수 있습니다.

- **권장 사항**: 작업을 다시 시도하고 SQL 데이터베이스 쪽 메트릭을 검토합니다.


### <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>오류 코드: SqlBulkCopyInvalidColumnLength

- **메시지**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **원인**: SQL 대량 복사가 bcp(대량 복사 프로그램) 유틸리티 클라이언트로부터 잘못된 열 길이를 받아서 대량 복사가 실패했습니다.

- **권장 사항**: 문제가 발생한 행을 확인하려면 복사 작업에서 내결함성 기능을 사용하도록 설정합니다. 이렇게 하면 추가 조사를 위해 문제가 있는 행을 스토리지로 리디렉션할 수 있습니다. 자세한 내용은 [복사 작업 내결함성](./copy-activity-fault-tolerance.md)을 참조하세요.


### <a name="error-code-sqlconnectionisclosed"></a>오류 코드: SqlConnectionIsClosed

- **메시지**: `The connection is closed by SQL Database.`

- **원인**: SQL 연결은 높은 동시 실행이 진행되어 서버가 연결을 종료하면 SQL 데이터베이스에 의해 종료됩니다.

- **권장 사항**: 연결을 다시 시도합니다. 문제가 지속되면 Azure SQL 지원으로 문의하세요.

### <a name="error-code-sqlserverinvalidlinkedservicecredentialmissing"></a>오류 코드: SqlServerInvalidLinkedServiceCredentialMissing

- **메시지**: `The SQL Server linked service is invalid with its credential being missing.`

- **원인**: 연결된 서비스가 올바르게 구성되지 않았습니다.

- **권장 사항**: SQL 서버 연결된 서비스의 유효성을 검사하고 수정합니다. 

### <a name="error-code-sqlparallelfailedtodetectpartitioncolumn"></a>오류 코드: SqlParallelFailedToDetectPartitionColumn

- **메시지**: `Failed to detect the partition column with command '%command;', %message;.`

- **원인**: 테이블에 기본 키 또는 고유 키가 없습니다.

- **권장 사항**: 테이블에서 기본 키 또는 고유 인덱스가 생성되었는지 확인합니다. 

### <a name="error-code-sqlparallelfailedtodetectphysicalpartitions"></a>오류 코드: SqlParallelFailedToDetectPhysicalPartitions

- **메시지**: `Failed to detect the physical partitions with command '%command;', %message;.`

- **원인**: 테이블에 대한 실제 파티션이 생성되지 않았습니다. 데이터베이스를 확인합니다.

- **권장 사항**: 이 문제를 해결하려면 [분할된 테이블 및 인덱스 만들기](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes?view=sql-server-ver15&preserve-view=true)를 참조하세요.

### <a name="error-code-sqlparallelfailedtogetpartitionrangesynapse"></a>오류 코드: SqlParallelFailedToGetPartitionRangeSynapse

- **메시지**: `Failed to get the partitions for azure synapse with command '%command;', %message;.`

- **원인**: 테이블에 대한 실제 파티션이 생성되지 않았습니다. 데이터베이스를 확인합니다.

- **권장 사항**: 이 문제를 해결하려면 [전용 SQL 풀에서 테이블 분할](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-partition.md)을 참조하세요.

### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>오류 메시지: 문자열을 uniqueIDentifier로 변환하지 못했습니다.

- **증상**: 스테이징된 복사본 및 PolyBase를 사용하여 테이블 형식 데이터 원본(예: SQL Server)에서 Azure Synapse Analytics로 데이터를 복사할 때 다음 오류가 발생합니다.

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **원인**: Azure Synapse Analytics PolyBase는 빈 문자열을 GUID로 변환할 수 없습니다.

- **해결 방법**: 복사 작업 싱크의 PolyBase 설정에서 **형식 기본값 사용** 옵션을 *false* 로 설정합니다.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>오류 메시지: 필요한 데이터 형식: DECIMAL(x,x), 잘못된 값

- **증상**: 스테이징된 복사본 및 PolyBase를 사용하여 테이블 형식 데이터 원본(예: SQL Server)에서 Azure Synapse Analytics로 데이터를 복사할 때 다음 오류가 발생합니다.

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **원인**: Azure Synapse Analytics PolyBase는 십진수 열에 빈 문자열(Null 값)을 삽입할 수 없습니다.

- **해결 방법**: 복사 작업 싱크의 PolyBase 설정에서 **형식 기본값 사용** 옵션을 false로 설정합니다.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>오류 메시지: Java 예외 메시지: HdfsBridge::CreateRecordReader

- **증상**: PolyBase를 사용하여 Azure Synapse Analytics에 데이터를 복사할 때 다음 오류가 발생합니다.

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **원인**: 스키마(총 열 너비)가 너무 큰 것이(1MB 초과) 원인일 수 있습니다. 모든 열의 크기를 추가하여 대상 Azure Synapse Analytics 테이블의 스키마를 확인합니다.

    - Int = 4바이트
    - Bigint = 8바이트
    - Varchar(n), char(n), binary(n), varbinary(n) = n바이트
    - Nvarchar(n), nchar(n) = n*2바이트
    - Date = 6바이트
    - Datetime/(2), smalldatetime = 16바이트
    - Datetimeoffset = 20바이트
    - Decimal = 19바이트
    - Float = 8바이트
    - Money = 8바이트
    - Smallmoney = 4바이트
    - Real = 4바이트
    - Smallint = 2바이트
    - Time = 12바이트
    - Tinyint = 1바이트

- **해결 방법**: 
    - 열 너비를 1MB 미만으로 줄입니다.
    - 또는 PolyBase를 사용하지 않도록 설정하여 대량 삽입 방법을 사용합니다.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>오류 메시지: HTTP 조건부 헤더를 사용하여 지정한 조건이 충족되지 않습니다.

- **증상**: SQL 쿼리를 사용하여 Azure Synapse Analytics에서 데이터를 풀할 때 다음 오류가 발생합니다.

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **원인**: Azure Storage의 외부 테이블을 쿼리할 때 Azure Synapse Analytics에서 문제가 발생했습니다.

- **해결 방법**: SSMS(SQL Server Management Studio)에서 동일한 쿼리를 실행하여 동일한 결과가 나오는지 확인합니다. 이렇게 하려면 Azure Synapse Analytics에 대해 지원 티켓을 생성하고 Azure Synapse Analytics 서버 및 데이터베이스 이름을 알려 주세요.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>성능 계층이 낮아서 복사가 실패한 경우

- **증상**: Azure SQL Database로 데이터를 복사할 때 다음과 같은 오류가 표시됩니다. `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **원인**: Azure SQL Database s1이 I/O(입출력) 제한에 도달했습니다.

- **해결 방법**: Azure SQL Database 성능 계층을 업그레이드하여 문제를 해결합니다. 


### <a name="sql-table-cant-be-found"></a>SQL 테이블을 찾을 수 없음 

- **증상**: 하이브리드에서 온-프레미스 SQL Server 테이블로 데이터를 복사할 때 다음과 같은 오류가 표시됩니다. `Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **원인**: 사용 중인 SQL 계정에 .NET SqlBulkCopy.WriteToServer에서 발생한 요청을 실행하는 데 필요한 권한이 없습니다.

- **해결 방법**: 보다 높은 권한을 갖는 SQL 계정을 사용합니다.


### <a name="error-message-string-or-binary-data-is-truncated"></a>오류 메시지: 문자열 또는 이진 데이터가 잘림

- **증상**: 온-프레미스 Azure SQL Server 테이블로 데이터를 복사할 때 오류가 발생합니다. 

- **원인**: Cx SQL 테이블 스키마 정의에 예상보다 길이가 작은 열이 하나 이상 있습니다.

- **해결 방법**: 이 문제를 해결하려면 다음 방법을 시도합니다.

    1. 어느 행에 문제가 있는지 알아보려면 SQL 싱크 [내결함성](./copy-activity-fault-tolerance.md), 그중에서도 특히 “redirectIncompatibleRowSettings”를 적용합니다.

        > [!NOTE]
        > 내결함성을 사용하면 실행 시간이 길어져서 비용이 높아질 수 있습니다.

    2. SQL 테이블 스키마 열 길이를 기준으로 리디렉션된 데이터를 재차 확인하여 어느 열을 업데이트해야 하는지 알아봅니다.

    3. 결과에 따라 테이블 스키마를 업데이트합니다.

### <a name="error-code-faileddboperation"></a>오류 코드: FailedDbOperation

- **메시지**: `User does not have permission to perform this action.`

- **권장 사항**: PolyBase를 사용하여 데이터를 로드하는 동안 Azure Synapse Analytics 커넥터에 구성된 사용자에게 대상 데이터베이스에 대한 'CONTROL' 권한이 있어야 합니다. 자세한 내용은 이 [문서](./connector-azure-sql-data-warehouse.md#required-database-permission)를 참조하세요.


## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="error-code-azuretableduplicatecolumnsfromsource"></a>오류 코드: AzureTableDuplicateColumnsFromSource

- **메시지**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **원인**: 중복된 원본 열이 발생하는 이유는 다음 중 하나일 수 있습니다.
   * 데이터베이스를 원본으로 사용하고 있고 테이블 조인을 적용했습니다.
   * 구조화되지 않은 CSV 파일로 인해 헤더 행에서 열 이름이 중복되었습니다.

- **권장 사항**: 열 이름을 재차 확인하고 결과에 따라 수정합니다.


## <a name="db2"></a>DB2

### <a name="error-code-db2driverrunfailed"></a>오류 코드: DB2DriverRunFailed

- **메시지**: `Error thrown from driver. Sql code: '%code;'`

- **원인**: 오류 메시지에 “SQLSTATE=51002 SQLCODE=-805”이라는 문자열이 포함된 경우 [DB2에서 데이터 복사](./connector-db2.md#linked-service-properties)의 “팁”을 따릅니다.

- **권장 사항**: `packageCollection` 속성에서 “NULLID”를 설정해 봅니다.


## <a name="delimited-text-format"></a>구분된 텍스트 형식

### <a name="error-code-delimitedtextcolumnnamenotallownull"></a>오류 코드: DelimitedTextColumnNameNotAllowNull

- **메시지**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **원인**: 작업에서 ‘firstRowAsHeader’가 설정된 경우 첫 번째 행이 열 이름으로 사용됩니다. 이 오류는 첫 번째 행이 빈 값을 포함하고 있음을 의미합니다(예: ‘ColumnA, ColumnB’).

- **권장 사항**:  첫 번째 행을 확인하여 비어 있다면 값을 수정합니다.


### <a name="error-code-delimitedtextmorecolumnsthandefined"></a>오류 코드: DelimitedTextMoreColumnsThanDefined

- **메시지**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **원인 및 권장 사항**: 오류의 원인이 여러 가지일 수 있습니다. 아래 목록에서 가능한 원인 분석과 권장 사항을 확인하세요.

  | 원인 분석                                               | 권장                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | 문제가 있는 행의 열 개수가 첫 번째 행의 열 개수보다 큽니다. 데이터 문제 또는 잘못된 열 구분 기호나 따옴표 문자 설정으로 인한 문제일 수 있습니다. | 오류 메시지에서 행 개수를 확인하고 행의 열을 확인한 후 데이터를 수정합니다. |
  | 예상 열 개수가 오류 메시지에 “1”로 표시된다면 잘못된 압축 또는 형식 설정을 지정했기 때문에 파일을 잘못 구문 분석한 것일 수 있습니다. | 형식 설정이 원본 파일과 일치하는지 확인합니다. |
  | 원본이 폴더인 경우 지정된 폴더에 있는 파일의 스키마가 다를 수 있습니다. | 지정된 폴더의 파일이 동일한 스키마를 갖는지 확인합니다. |


## <a name="dynamics-365-dataverse-common-data-service-and-dynamics-crm"></a>Dynamics 365, Dataverse(Common Data Service) 및 Dynamics CRM

### <a name="error-code-dynamicscreateserviceclienterror"></a>오류 코드: DynamicsCreateServiceClientError

- **메시지**: `This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **원인**: Dynamics 서버 쪽에서 발생한 일시적인 문제입니다.

- **권장 사항**:  파이프라인을 다시 실행합니다. 또다시 실패할 경우 병렬 처리를 줄여 봅니다. 문제가 지속되면 Dynamics 지원으로 문의하세요.


### <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>스키마를 가져오거나 데이터를 미리 볼 때 열이 누락됨

- **증상**: 스키마를 가져오거나 데이터를 미리 볼 때 열이 누락됩니다. 오류 메시지: `The valid structure information (column name and type) are required for Dynamics source.`

- **원인**: Data Factory 및 Synapse 파이프라인은 처음 10개의 레코드에 값이 없는 열을 표시할 수 없습니다. 따라서 이 문제는 의도적으로 발생하는 문제입니다. 추가한 열이 올바른 형식인지 확인하세요. 

- **권장 사항**: 매핑 탭에서 열을 수동으로 추가합니다.


### <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>오류 코드: DynamicsMissingTargetForMultiTargetLookupField

- **메시지**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **원인**: 원본 또는 열 매핑에 대상 열이 없습니다.

- **권장 사항**:  
  1. 원본에 대상 열이 포함되어 있는지 확인합니다. 
  2. 열 매핑에 대상 열을 추가합니다. 싱크 열이 *{fieldName}@EntityReference* 형식인지 확인합니다.


### <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>오류 코드: DynamicsInvalidTargetForMultiTargetLookupField

- **메시지**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **원인**: 다중 대상 조회 필드의 대상 엔터티로 잘못된 엔터티 이름이 제공되었습니다.

- **권장 사항**: 다중 대상 조회 필드에 유효한 엔터티 이름을 지정합니다.


### <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>오류 코드: DynamicsInvalidTypeForMultiTargetLookupField

- **메시지**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **원인**: 대상 열의 값이 문자열이 아닙니다.

- **권장 사항**: 다중 대상 조회 대상 열에 유효한 문자열을 지정합니다.


### <a name="error-code-dynamicsfailedtorequetserver"></a>오류 코드: DynamicsFailedToRequetServer

- **메시지**: `The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **원인**: Dynamics 서버가 불안정하거나 액세스할 수 없습니다. 또는 네트워크에 문제가 있습니다.

- **권장 사항**: 자세한 내용을 알아보려면 네트워크 연결을 확인하거나 Dynamics 서버 로그를 확인합니다. 추가 도움이 필요한 경우 Dynamics 지원으로 문의하세요.


### <a name="error-code-dynamicsfailedtoconnect"></a>오류 코드: DynamicsFailedToConnect 
 
 - **메시지**: `Failed to connect to Dynamics: %message;` 
 
 - **원인 및 권장 사항**: 오류의 원인이 여러 가지일 수 있습니다. 아래 목록에서 가능한 원인 분석과 권장 사항을 확인하세요.

    | 원인 분석                                               | 권장                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 사용 사례가 다음 세 가지 조건을 **모두** 충족하는 경우 `ERROR REQUESTING ORGS FROM THE DISCOVERY SERVERFCB 'EnableRegionalDisco' is disabled.` 또는 `Unable to Login to Dynamics CRM, message:ERROR REQUESTING Token FROM THE Authentication context - USER intervention required but not permitted by prompt behavior AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access '00000007-0000-0000-c000-000000000000'`이 표시됩니다. <br/> 1. Dynamics 365, Common Data Service 또는 Dynamics CRM에 연결하고 있습니다.<br/>  2. Office365 인증을 사용하고 있습니다.<br/>  3. 테넌트 및 사용자가 [조건부 액세스](../active-directory/conditional-access/overview.md) 및/또는 다단계 인증을 위해 Azure Active Directory에 구성되어 있습니다(Dataverse 문서는 이 [링크](/powerapps/developer/data-platform/authenticate-office365-deprecation) 참조).<br/>  이러한 상황에서는 연결이 2021년 6월 8일 이전에 성공했습니다. 2021년 6월 9일부터 지역 검색 서비스의 사용 중단으로 인해 연결이 실패하기 시작합니다(이 [링크](/power-platform/important-changes-coming#regional-discovery-service-is-deprecated)참조).| 테넌트 및 사용자가 [조건부 액세스](../active-directory/conditional-access/overview.md) 및/또는 Multi-Factor Authentication이 필요한 Azure Active Directory에 구성된 경우, 2021년 6월 8일 이후에 인증하려면 'Azure AD 서비스 주체'를 사용해야 합니다. 자세한 단계는 이 [링를](./connector-dynamics-crm-office-365.md#prerequisites)를 참조하세요.|
    |오류 메시지에 `Office 365 auth with OAuth failed`라고 표시된다면 서버에 OAuth와 호환되지 않는 구성이 있는 것임을 의미합니다.| 1. Dynamics 지원 팀에 연락하여 상세한 오류 메시지를 제공하고 도움을 받습니다. <br/> 2. 서비스 주체 인증을 사용하세요. [예: Azure AD 서비스 주체 및 인증서 인증을 사용하는 Dynamics Online](./connector-dynamics-crm-office-365.md#example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication)을 참조할 수도 있습니다. 
    |오류 메시지에 `Unable to retrieve authentication parameters from the serviceUri`라고 표시된다면 Dynamics 서비스 URL을 잘못 입력했거나 프록시/방화벽이 트래픽을 인터셉트하도록 설정된 것임을 의미합니다. |1. 연결된 서비스에 올바른 서비스 URI를 입력했는지 확인합니다.<br/> 2. 자체 호스팅 IR을 사용하는 경우 방화벽/프록시가 Dynamics 서버로 전송되는 요청을 인터셉트하지 않는지 확인합니다. |
    |오류 메시지에 `An unsecured or incorrectly secured fault was received from the other party`라고 표시된다면 서버 쪽에서 예기치 않은 응답을 수신했음을 의미합니다.  | 1. Office 365 인증을 사용하는 경우 사용자 이름과 암호가 올바른지 확인합니다. <br/> 2. 올바른 서비스 URI를 입력했는지 확인합니다. <br/> 3. 지역 CRM URL(‘crm’ 뒤에 숫자가 있는 URL)을 사용하는 경우 올바른 지역 식별자를 사용했는지 확인합니다.<br/> 4. 도움이 필요하면 Dynamics 지원 팀으로 문의하세요. |
    |오류 메시지에 `No Organizations Found`라고 표시된다면 조직 이름이 잘못되었거나 서비스 URL에 잘못된 CRM 지역 식별자를 사용했음을 의미합니다.|1. 올바른 서비스 URI를 입력했는지 확인합니다.<br/>2. 지역 CRM URL(‘crm’ 뒤에 숫자가 있는 URL)을 사용하는 경우 올바른 지역 식별자를 사용했는지 확인합니다. <br/> 3. 도움이 필요하면 Dynamics 지원 팀으로 문의하세요. |
    | `401 Unauthorized` 및 AAD 관련 오류 메시지가 표시된다면 서비스 주체에 문제가 있음을 의미합니다. |오류 메시지의 지침에 따라 서비스 주체 문제를 해결합니다. |
   |그 밖의 오류의 경우, 보통 서버 쪽에 문제가 있는 것입니다. |[XrmToolBox](https://www.xrmtoolbox.com/)를 사용하여 연결을 설정합니다. 오류가 지속되면 Dynamics 지원 팀으로 문의하여 도움을 받으세요. |

### <a name="error-code-dynamicsoperationfailed"></a>오류 코드: DynamicsOperationFailed 
 
- **메시지**: `Dynamics operation failed with error code: %code;, error message: %message;.` 

- **원인**: 서버 쪽에서 작업이 실패했습니다. 

- **권장 사항**: 오류 메시지 `Dynamics operation failed with error code: {code}`에서 Dynamics 작업의 오류 코드를 확인하고 [웹 서비스 오류 코드](/powerapps/developer/data-platform/org-service/web-service-error-codes) 문서에서 자세한 정보를 참조합니다. 필요한 경우 Dynamics 지원 팀으로 문의하세요. 
 
 
### <a name="error-code-dynamicsinvalidfetchxml"></a>오류 코드: DynamicsInvalidFetchXml 
  
- **메시지**: `The Fetch Xml query specified is invalid.` 

- **원인**: 페치 XML에 오류가 있습니다.  

- **권장 사항**: 페치 XML에서 오류를 수정합니다. 
 
 
### <a name="error-code-dynamicsmissingkeycolumns"></a>오류 코드: DynamicsMissingKeyColumns 
 
- **메시지**: `Input DataSet must contain keycolumn(s) in Upsert/Update scenario. Missing key column(s): %column;`
 
- **원인**: 원본 데이터에 싱크 엔터티의 키 열이 없습니다. 

- **권장 사항**: 원본 데이터에 키 열이 있는지 확인하거나 싱크 엔터티에서 원본 열을 키 열에 매핑합니다. 
 
 
### <a name="error-code-dynamicsprimarykeymustbeguid"></a>오류 코드: DynamicsPrimaryKeyMustBeGuid 
 
- **메시지**: `The primary key attribute '%attribute;' must be of type guid.` 
 
- **원인**: 기본 키 열의 형식이 ‘Guid’가 아닙니다. 
 
- **권장 사항**: 원본 데이터의 기본 키 열이 ‘Guid’ 형식인지 확인합니다. 
 

### <a name="error-code-dynamicsalternatekeynotfound"></a>오류 코드: DynamicsAlternateKeyNotFound 
 
- **메시지**: `Cannot retrieve key information of alternate key '%key;' for entity '%entity;'.` 
 
- **원인**: 제공된 대체 키가 없습니다. 키 이름이 잘못되었거나 권한이 없는 것일 수 있습니다. 
 
- **권장 사항**: <br/> 
    1. 키 이름에서 오타를 수정합니다.<br/> 
    1. 엔터티에 대한 충분한 권한이 있는지 확인합니다. 
 
 
### <a name="error-code-dynamicsinvalidschemadefinition"></a>오류 코드: DynamicsInvalidSchemaDefinition 
 
- **메시지**: `The valid structure information (column name and type) are required for Dynamics source.` 
 
- **원인**: 열 매핑의 싱크 열에서 ‘type’ 속성이 누락되었습니다. 
 
- **권장 사항**: 포털에서 JSON 편집기를 사용하여 열 매핑에서 해당 열에 ‘type’ 속성을 추가할 수 있습니다. 


## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>오류 코드: FtpFailedToConnectToFtpServer

- **메시지**: `Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **원인**: FTP 서버에 대해 연결된 서비스 유형이 잘못 사용되었을 수 있습니다(예: SFTP(보안 FTP) 연결된 서비스를 사용하여 FTP 서버를 연결한 경우).

- **권장 사항**: 대상 서버의 포트를 확인합니다. FTP는 포트 21을 사용합니다.

### <a name="error-code-ftpfailedtoreadftpdata"></a>오류 코드: FtpFailedToReadFtpData

- **메시지**: `Failed to read data from ftp: The remote server returned an error: 227 Entering Passive Mode (*,*,*,*,*,*).`

- **원인**: 데이터 팩터리 또는 Synapse 파이프라인에서 지원하는 수동 모드에서 데이터 전송에 대해 1024에서 65535 사이의 포트 범위가 열려 있지 않습니다.

- **권장 사항**: 대상 서버의 방화벽 설정을 확인합니다. 포트 1024-65535 또는 FTP 서버에 지정된 포트 범위를 SHIR/Azure IR IP 주소로 엽니다.


## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>오류 코드: HttpFileFailedToRead

- **메시지**: `Failed to read data from http server. Check the error from http server：%message;`

- **원인**: 이 오류는 데이터 팩터리 또는 Synapse 파이프라인이 HTTP 서버와 통신하는데 HTTP 요청 작업이 실패한 경우 발생합니다.

- **권장 사항**: 오류 메시지에서 HTTP 상태 코드를 확인하고 원격 서버 문제를 해결합니다.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>오류 코드: ArgumentOutOfRangeException

- **메시지**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **원인**: Azure Data Factory 및 Synapse 파이프라인에서 DateTime 값은 0001-01-01 00:00:00~9999-12-31 23:59:59 범위에서 지원됩니다. 그러나 Oracle은 이보다 큰 범위의 DateTime 값을 지원하므로(예: 기원전 세기, min/sec>59 등), 이로 인해 오류가 발생하게 됩니다.

- **권장 사항**: 

    Oracle의 값이 지원되는 날짜 범위 안에 있는지 확인하려면 `select dump(<column name>)`을 실행합니다. 

    결과에서 바이트 시퀀스를 확인하려면 [How are dates stored in Oracle?](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle)(Oracle에서 날짜가 저장되는 방식)을 참조하세요.


## <a name="orc-format"></a>ORC 형식

### <a name="error-code-orcjavainvocationexception"></a>오류 코드: OrcJavaInvocationException

- **메시지**: `An error occurred when invoking Java, message: %javaException;.`
- **원인 및 권장 사항**: 오류의 원인이 여러 가지일 수 있습니다. 아래 목록에서 가능한 원인 분석과 권장 사항을 확인하세요.

    | 원인 분석                                               | 권장                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 오류 메시지에 “java.lang.OutOfMemory”, “Java 힙 공간” 및 “doubleCapacity”라는 문자열이 포함된 경우 일반적으로 이전 버전의 통합 런타임에 메모리 관리 문제가 있는 것입니다. | 자체 호스팅 통합 런타임을 사용 중인 경우 최신 버전으로 업그레이드하는 것이 좋습니다. |
    | 오류 메시지에 “java.lang.OutOfMemory”라는 문자열이 포함된 경우 통합 런타임에 파일을 처리할 수 있는 충분한 리소스가 없는 것입니다. | Integration Runtime의 동시 실행을 제한합니다. 자체 호스팅 IR의 경우 메모리가 8GB보다 크거나 같은 강력한 머신으로 스케일 업합니다. |
    |오류 메시지에 “NullPointerReference”라는 문자열이 포함된 경우 일시적인 오류가 원인일 수 있습니다. | 작업을 다시 시도하세요. 문제가 계속되면 지원에 문의하세요. |
    | 오류 메시지에 “BufferOverflowException”이라는 문자열이 포함된 경우 일시적인 오류가 원인일 수 있습니다. | 작업을 다시 시도하세요. 문제가 계속되면 지원에 문의하세요. |
    | 오류 메시지에 “java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable can’t be cast to org.apache.hadoop.io.Text”라는 문자열이 포함된 경우 Java 런타임의 형식 전환 문제가 원인일 수 있습니다. 일반적으로 이는 Java 런타임에서 원본 데이터를 처리할 수 없음을 의미합니다. | 이 문제는 데이터 문제입니다. ORC 형식 데이터에서 char 또는 varchar 대신 문자열을 사용해 보세요. |

### <a name="error-code-orcdatetimeexceedlimit"></a>오류 코드: OrcDateTimeExceedLimit

- **메시지**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **원인**: datetime 값이 ‘0001-01-01 00:00:00’인 경우 [율리우스력과 그레고리력](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates)의 차이가 원인일 수 있습니다.

- **권장 사항**: 틱 값을 확인하고 datetime 값 ‘0001-01-01 00:00:00’을 사용하지 않습니다.


## <a name="parquet-format"></a>Parquet 형식

### <a name="error-code-parquetjavainvocationexception"></a>오류 코드: ParquetJavaInvocationException

- **메시지**: `An error occurred when invoking java, message: %javaException;.`

- **원인 및 권장 사항**: 오류의 원인이 여러 가지일 수 있습니다. 아래 목록에서 가능한 원인 분석과 권장 사항을 확인하세요.

    | 원인 분석                                               | 권장                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 오류 메시지에 “java.lang.OutOfMemory”, “Java 힙 공간” 및 “doubleCapacity”라는 문자열이 포함된 경우 일반적으로 이전 버전의 통합 런타임에 메모리 관리 문제가 있는 것입니다. | 자체 호스팅 IR을 사용하고 있으며 버전이 3.20.7159.1 이전 버전인 경우 최신 버전으로 업그레이드하는 것이 좋습니다. |
    | 오류 메시지에 “java.lang.OutOfMemory”라는 문자열이 포함된 경우 통합 런타임에 파일을 처리할 수 있는 충분한 리소스가 없는 것입니다. | Integration Runtime의 동시 실행을 제한합니다. 자체 호스팅 IR의 경우 메모리가 8GB보다 크거나 같은 강력한 머신으로 스케일 업합니다. |
    | 오류 메시지에 “NullPointerReference”라는 문자열이 포함된 경우 일시적인 오류가 원인일 수 있습니다. | 작업을 다시 시도하세요. 문제가 계속되면 지원에 문의하세요. |

### <a name="error-code-parquetinvalidfile"></a>오류 코드: ParquetInvalidFile

- **메시지**: `File is not a valid Parquet file.`

- **원인**: 이 문제는 Parquet 파일 문제입니다.

- **권장 사항**: 입력이 유효한 Parquet 파일인지 확인합니다.


### <a name="error-code-parquetnotsupportedtype"></a>오류 코드: ParquetNotSupportedType

- **메시지**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **원인**: Azure Data Factory 및 Synapse 파이프라인에서는 Parquet 형식이 지원되지 않습니다.

- **권장 사항**: [복사 작업을 통해 지원되는 파일 형식 및 압축 코덱](./supported-file-formats-and-compression-codecs.md)으로 이동하여 원본 데이터를 재차 확인합니다.


### <a name="error-code-parquetmisseddecimalprecisionscale"></a>오류 코드: ParquetMissedDecimalPrecisionScale

- **메시지**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **원인**: 숫자 전체 자릿수와 소수 자릿수가 구문 분석되었으나 해당 정보가 제공되지 않았습니다.

- **권장 사항**: 원본이 올바른 전체 자릿수 및 소수 자릿수 정보를 반환하지 않습니다. 문제 열에서 정보를 확인하세요.


### <a name="error-code-parquetinvaliddecimalprecisionscale"></a>오류 코드: ParquetInvalidDecimalPrecisionScale

- **메시지**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **원인**: 스키마가 유효하지 않습니다.

- **권장 사항**: 문제 열에서 전체 자릿수와 소수 자릿수를 확인합니다.


### <a name="error-code-parquetcolumnnotfound"></a>오류 코드: ParquetColumnNotFound

- **메시지**: `Column %column; does not exist in Parquet file.`

- **원인**: 원본 스키마가 싱크 스키마와 일치하지 않습니다.

- **권장 사항**: 작업에서 매핑을 확인합니다. 원본 열이 올바른 싱크 열과 매핑될 수 있는지 확인합니다.


### <a name="error-code-parquetinvaliddataformat"></a>오류 코드: ParquetInvalidDataFormat

- **메시지**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **원인**: 데이터를 mappings.source에 지정된 형식으로 변환할 수 없습니다.

- **권장 사항**: 원본 데이터를 재차 확인하거나 복사 작업 열 매핑에서 이 열에 올바른 데이터 형식을 지정합니다. 자세한 내용은 [복사 활동에서 지원되는 파일 형식 및 압축 코덱](./supported-file-formats-and-compression-codecs.md)을 참조하세요.


### <a name="error-code-parquetdatacountnotmatchcolumncount"></a>오류 코드: ParquetDataCountNotMatchColumnCount

- **메시지**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **원인**: 원본 열 개수와 싱크 열 개수가 일치하지 않습니다.

- **권장 사항**: ‘mapping’에서 원본 열 개수가 싱크 열 개수와 일치하는지 재차 확인합니다.


### <a name="error-code-parquetdatatypenotmatchcolumntype"></a>오류 코드: ParquetDataTypeNotMatchColumnType

- **메시지**: `The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **원인**: 원본의 데이터를 싱크에 정의된 형식으로 변환할 수 없습니다.

- **권장 사항**: mapping.sink에 올바른 형식을 지정합니다.


### <a name="error-code-parquetbridgeinvaliddata"></a>오류 코드: ParquetBridgeInvalidData

- **메시지**: `%message;`

- **원인**: 데이터 값이 제한을 초과했습니다.

- **해결 방법**: 작업을 다시 시도합니다. 문제가 지속되면 지원으로 문의하세요.


### <a name="error-code-parquetunsupportedinterpretation"></a>오류 코드: ParquetUnsupportedInterpretation

- **메시지**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **원인**: 이 시나리오는 지원되지 않습니다.

- **권장 사항**:  'ParquetInterpretFor'는 'sparkSql'이 아니어야 합니다.


### <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>오류 코드: ParquetUnsupportFileLevelCompressionOption

- **메시지**: `File level compression is not supported for Parquet.`

- **원인**: 이 시나리오는 지원되지 않습니다.

- **권장 사항**: 페이로드에서 ‘CompressionType’을 제거합니다.


### <a name="error-code-usererrorjniexception"></a>오류 코드: UserErrorJniException

- **메시지**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **원인**: 일부 비정상(전역) 인수가 설정되어 있어 JVM(Java Virtual Machine)을 만들 수 없습니다.

- **권장 사항**: 자체 호스팅 IR의 ‘각 노드’를 호스트하는 머신에 로그인합니다. 시스템 변수가 `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G`와 같이 올바르게 설정되었는지 확인합니다. 모든 IR 노드를 다시 시작한 다음 파이프라인을 다시 실행합니다.


### <a name="arithmetic-overflow"></a>산술 연산 오버플로

- **증상**: Parquet 파일을 복사할 때 `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`이라는 오류 메시지가 표시됩니다.

- **원인**: Oracle에서 Parquet로 파일을 복사하는 경우 현재 38개 이하의 소수 자릿수와 20자 이하의 정수 부분 길이만 지원됩니다. 

- **해결 방법**: 임시 방편으로 이 문제가 있는 모든 열을 VARCHAR2로 변환할 수 있습니다.


### <a name="no-enum-constant"></a>열거형 상수 없음

- **증상**: 데이터를 Parquet 형식으로 복사할 때 `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` 또는 `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` 오류 메시지가 표시됩니다.

- **원인**: 

    이 문제는 열 이름에 공백 또는 지원되지 않는 특수 문자(예: ;{}()\n\t=)가 있는 경우 발생할 수 있습니다. Parquet에서는 이러한 형식을 지원하지 않습니다. 

    예를 들어, *contoso(test)* 와 같은 열 이름은 [코드](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java)에 있는 괄호 안의 형식을 `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");`과 같이 구문 분석합니다. 이러한 “test” 형식이 없어서 오류가 throw된 것입니다.

    지원되는 형식을 확인하려면 GitHub [apache/parquet-mr site](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)를 참조하세요.

- **해결 방법**: 

    다음을 재차 확인합니다.
    - 싱크 열 이름에 공백이 있습니다.
    - 공백이 있는 첫 번째 행이 열 이름으로 사용되었습니다.
    - OriginalType 형식이 지원됩니다. `,;{}()\n\t=`와 같은 특수 문자는 사용하지 않습니다. 

### <a name="error-code-parquetdatetimeexceedlimit"></a>오류 코드: ParquetDateTimeExceedLimit

- **메시지**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **원인**: datetime 값이 ‘0001-01-01 00:00:00’인 경우 율리우스력과 그레고리력의 차이가 원인일 수 있습니다. 자세한 내용은 [율리우스력 날짜와 그레고리력 날짜 간의 차이](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates)를 참조하세요.

- **해결 방법**: 틱 값을 확인하고 datetime 값 ‘0001-01-01 00:00:00’을 사용하지 않습니다.

### <a name="error-code-parquetinvalidcolumnname"></a>오류 코드: ParquetInvalidColumnName

- **메시지**: `The column name is invalid. Column name cannot contain these character:[,;{}()\n\t=]`

- **원인**: 열 이름에 잘못된 문자가 있습니다.

- **해결 방법**: 열 매핑을 추가하거나 수정하여 싱크 열 이름을 유효하게 만듭니다.

## <a name="rest"></a>REST

### <a name="error-code-restsinkcallfailed"></a>오류 코드: RestSinkCallFailed

- **메시지**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **원인**: 이 오류는 데이터 팩터리 또는 Synapse 파이프라인이 HTTP 프로토콜을 통해 REST 엔드포인트와 통신하는데 요청 작업이 실패한 경우 발생합니다.

- **권장 사항**: 오류 메시지에서 HTTP 상태 코드 또는 메시지를 확인하고 원격 서버 문제를 해결합니다.

### <a name="error-code-restsourcecallfailed"></a>오류 코드: RestSourceCallFailed

- **메시지**: `The HttpStatusCode %statusCode; indicates failure.&#xA;Request URL: %requestUri;&#xA;Response payload:%payload;`

- **원인**: 이 오류는 Azure Data Factory가 HTTP 프로토콜을 통해 REST 엔드포인트와 통신하는데 요청 작업이 실패한 경우 발생합니다.

- **권장 사항**: 오류 메시지에서 HTTP 상태 코드 또는 요청 URL이나 응답 페이로드를 확인하고 원격 서버 문제를 해결합니다.

### <a name="error-code-restsinkunsupportedcompressiontype"></a>오류 코드: RestSinkUNSupportedCompressionType

- **메시지**: `User Configured CompressionType is Not Supported By Azure Data Factory：%message;`

- **권장 사항**: REST 싱크에 대해 지원되는 압축 유형을 확인합니다.

### <a name="unexpected-network-response-from-the-rest-connector"></a>REST 커넥터로부터 예기치 않은 네트워크 응답 수신됨

- **증상**: 엔드포인트가 가끔씩 REST 커넥터로부터 예기치 않은 응답(400, 401, 403, 500)을 수신합니다.

- **원인**: REST 원본 커넥터는 연결된 서비스/데이터 세트/복사 원본의 URL 및 HTTP 메서드/헤더/본문을 매개 변수로 사용하여 HTTP 요청을 구성합니다. 이 문제는 이러한 매개 변수 중 하나 이상이 잘못 지정되어 발생한 것일 가능성이 큽니다.

- **해결 방법**: 
    - 명령 프롬프트 창에서 ‘curl’을 사용하여 매개 변수가 원인인지 확인합니다(**Accept** 헤더와 **User-Agent** 헤더는 항상 포함되어야 합니다).
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      명령이 전과 같이 예기치 않은 응답을 반환한다면 예상 응답을 반환할 때까지 앞에 나온 매개 변수를 ‘curl’을 사용하여 수정해 봅니다. 

      ‘curl--help’를 사용하면 명령의 고급 사용법을 확인할 수 있습니다.

    - REST 커넥터가 예기치 않은 응답을 반환하는 경우에만 추가 문제 해결을 위해 Microsoft 지원으로 문의하세요.
    
    - ‘curl’은 SSL 인증서 유효성 검사 문제를 재현하는 데 적합하지 않을 수 있습니다. 일부 시나리오에서는 SSL 인증서 유효성 검사 문제없이 ‘curl’ 명령이 성공적으로 실행됩니다. 하지만 동일한 URL이 브라우저에서 실행될 경우, 클라이언트가 서버와 신뢰를 설정할 수 있도록 하기 위한 SSL 인증서가 반환되지 않습니다.

      앞에 나온 원인에 대해서는 **Postman** 및 **Fiddler** 와 같은 도구가 권장됩니다.


## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>오류 코드: SftpOperationFail

- **메시지**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **원인**: SFTP 작업에 문제가 있습니다.

- **권장 사항**: SFTP에서 오류 세부 정보를 확인합니다.


### <a name="error-code-sftprenameoperationfail"></a>오류 코드: SftpRenameOperationFail

- **메시지**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **원인**: SFTP 서버에서 temp 파일의 이름 바꾸기를 지원하지 않습니다.

- **권장 사항**: 복사 싱크에서 “useTempFileRename”을 false로 설정하여 temp 파일로의 업로드를 비활성화합니다.


### <a name="error-code-sftpinvalidsftpcredential"></a>오류 코드: SftpInvalidSftpCredential

- **메시지**: `Invalid SFTP credential provided for '%type;' authentication type.`

- **원인**: 프라이빗 키 콘텐츠를 Azure Key Vault 또는 SDK에서 가져왔으나 올바르게 인코딩되지 않았습니다.

- **권장 사항**:  

    프라이빗 키 콘텐츠가 사용자의 키 자격 증명 모음에서 가져온 것인 경우, SFTP 연결된 서비스에 직접 업로드한다면 원본 키 파일이 작동할 수 있습니다.

    자세한 내용은 [데이터 팩터리 또는 Synapse 파이프라인을 사용하여 SFTP 서버 간에 데이터 복사](./connector-sftp.md#use-ssh-public-key-authentication)를 참조하세요. 프라이빗 키 콘텐츠는 base64로 인코딩된 SSH 프라이빗 키 콘텐츠입니다.

    원본 프라이빗 키 파일 ‘전체’를 base64 인코딩으로 변환하고 인코딩된 문자열을 사용자의 키 자격 증명 모음에 저장하세요. 원본 프라이빗 키 파일은 파일에서 **업로드** 를 선택하면 SFTP 연결된 서비스에서 작동할 수 있는 키 파일입니다.

    다음은 문자열을 생성하는 데 사용할 수 있는 몇 가지 샘플입니다.

    - C# 코드 사용:

        ```
        byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Python 코드 사용:

        ```
        import base64
        rfd = open(r'{Private Key Path}', 'rb')
        keyContent = rfd.read()
        rfd.close()
        print base64.b64encode(Key Content)
        ```

    - 타사 base64 변환 도구를 사용합니다. Microsoft는 [Encode to Base64 format](https://www.base64encode.org/) 도구를 추천합니다.

- **원인**: 잘못된 키 콘텐츠 형식이 선택되었습니다.

- **권장 사항**:  

    PKCS#8 형식 SSH 프라이빗 키(“-----BEGIN ENCRYPTED PRIVATE KEY-----”로 시작)는 현재 SFTP 서버에 액세스하는 용도로 지원되지 않습니다. 

    키를 “-----BEGIN RSA PRIVATE KEY-----”로 시작하는 기존 SSH 키 형식으로 변환하려면 다음 명령을 실행합니다.

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **원인**: 자격 증명 또는 프라이빗 키 콘텐츠가 잘못되었습니다.

- **권장 사항**: 키 파일 또는 암호가 올바른지 확인하려면 WinSCP와 같은 도구를 사용하여 재차 확인합니다.

### <a name="sftp-copy-activity-failed"></a>SFTP 복사 작업 실패

- **증상**: 
  * 오류 코드: UserErrorInvalidColumnMappingColumnNotFound 
  * 오류 메시지: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **원인**: 원본에 “AccMngr” 열이 없습니다.

- **해결 방법**: “AccMngr” 열이 있는지 확인하려면 대상 데이터 세트 열을 매핑하여 데이터 세트 구성을 재차 확인합니다.


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>오류 코드: SftpFailedToConnectToSftpServer

- **메시지**: `Failed to connect to SFTP server '%server;'.`

- **원인**: 오류 메시지에 “Socket read operation has timed out after 30,000 milliseconds”라는 문자열이 포함된 경우 SFTP 서버에 대해 올바르지 않은 연결된 서비스 유형이 사용된 것이 한 가지 가능한 원인일 수 있습니다. 예를 들어, SFTP 서버에 연결하는 데 FTP 연결된 서비스를 사용하고 있을 수 있습니다.

- **권장 사항**: 대상 서버의 포트를 확인합니다. 기본적으로 SFTP는 포트 22를 사용합니다.

- **원인**: 오류 메시지에 “Server response does not contain SSH protocol identification”이라는 문자열이 포함된 경우 SFTP 서버가 연결을 제한하고 있는 것이 한 가지 가능한 원인일 수 있습니다. SFTP 서버에서 병렬로 다운로드하기 위해 여러 연결이 생성되며, 때로는 이로 인해 SFTP 서버 제한이 발생할 수 있습니다. 보통은 서버마다 제한이 발생한 경우 표시되는 오류가 서로 다릅니다.

- **권장 사항**:  

    SFTP 데이터 세트의 최대 동시 연결 수를 1로 설정하고 복사 작업을 다시 실행합니다. 작업이 성공하면 제한이 문제였음을 확인할 수 있습니다.

    낮은 처리량을 올리려면 SFTP 관리자에게 동시 연결 개수 제한을 늘려 달라고 요청하거나 다음 중 하나를 수행합니다.

    * 자체 호스팅 IR을 사용하는 경우 허용 목록에 자체 호스팅 IR 컴퓨터의 IP를 추가합니다.
    * Azure IR을 사용하는 경우 [Azure Integration Runtime IP 주소](./azure-integration-runtime-ip-addresses.md)를 추가합니다. SFTP 서버 허용 목록에 IP 범위를 추가하지 않으려면 자체 호스팅 IR을 대신 사용합니다.


#### <a name="error-code-sftppermissiondenied"></a>오류 코드: SftpPermissionDenied

- **메시지**: `Permission denied to access '%path;'`

- **원인**: 작업을 수행할 때 지정된 사용자에게 폴더 또는 파일에 대한 읽기 또는 쓰기 권한이 없습니다.

- **권장 사항**: 사용자에게 SFTP 서버의 폴더 또는 파일을 읽거나 쓸 수 있는 권한을 부여합니다.
 
### <a name="error-code-sftpauthenticationfailure"></a>오류 코드: SftpAuthenticationFailure

- **메시지**: `Meet authentication failure when connect to Sftp server '%server;' using '%type;' authentication type. Please make sure you are using the correct authentication type and the credential is valid. For more details, see our troubleshooting docs.`

- **원인**: 지정된 자격 증명(암호 또는 프라이빗 키)이 잘못되었습니다.

- **권장 사항**: 자격 증명을 확인합니다.

- **원인**: 지정된 인증 유형이 허용되지 않거나 SFTP 서버에서 인증을 완료하는 데 충분하지 않습니다.

- **권장 사항**: 올바른 인증 유형을 사용하려면 다음 옵션을 적용합니다.
    - 서버에 암호가 필요한 경우 "기본"을 사용합니다.
    - 서버에 프라이빗 키가 필요한 경우 "SSH 퍼블릭 키 인증"을 사용합니다.
    - 서버에 "암호"와 "프라이빗 키"가 모두 필요한 경우 "다단계 인증"을 사용합니다.

- **원인**: SFTP 서버는 인증을 위해 "키보드 대화형"이 필요하지만 "암호"를 제공했습니다.

- **권장 사항**: 

    "키보드 대화형"은 "암호"와 다른 특수 인증 방법입니다. 즉, 서버에 로그인할 때 암호를 수동으로 입력해야 하며 이전에 저장된 암호를 사용할 수 없습니다. 그러나 ADF(Azure Data Factory)는 예약된 데이터 전송 서비스이며 런타임에 암호를 제공할 수 있는 팝업 입력 상자가 없습니다. <br/> 
    
    절충안으로 실제 수동 입력 대신 백그라운드에서 입력을 시뮬레이션하는 옵션이 제공됩니다. 이 방식은 "키보드 대화형"을 "암호"로 변경할 때와 같은 결과를 나타냅니다. 이 보안 문제를 수락할 수 있는 경우 아래 단계에 따라 사용하도록 설정합니다.<br/> 
    1. ADF 포털에서 SFTP 연결된 서비스를 마우스로 가리키고 코드 단추를 선택하여 해당 페이로드를 엽니다.
    1. typeProperties 섹션에서 `"allowKeyboardInteractiveAuth": true`를 추가합니다.
 
## <a name="sharepoint-online-list"></a>SharePoint Online 목록

### <a name="error-code-sharepointonlineauthfailed"></a>오류 코드: SharePointOnlineAuthFailed

- **메시지**: `The access token generated failed, status code: %code;, error message: %message;.`

- **원인**: 서비스 주체 ID와 키가 올바르게 설정되지 않았을 수 있습니다.

- **권장 사항**: 등록된 애플리케이션(서비스 주체 ID)과 키가 올바르게 설정되었는지 확인합니다.


## <a name="xml-format"></a>XML 형식

### <a name="error-code-xmlsinknotsupported"></a>오류 코드: XmlSinkNotSupported

- **메시지**: `Write data in XML format is not supported yet, choose a different format!`

- **원인**: 복사 작업에서 XML 데이터 세트가 싱크 데이터 세트로 사용되었습니다.

- **권장 사항**: 싱크 데이터 세트가 아닌 다른 형식으로 데이터 세트를 사용합니다.


### <a name="error-code-xmlattributecolumnnameconflict"></a>오류 코드: XmlAttributeColumnNameConflict

- **메시지**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **원인**: 특성 접두사가 사용되었으며 이로 인해 충돌이 발생했습니다.

- **권장 사항**: “attributePrefix” 속성에 다른 값을 설정합니다.


### <a name="error-code-xmlvaluecolumnnameconflict"></a>오류 코드: XmlValueColumnNameConflict

- **메시지**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **원인**: 자식 요소 이름 중 하나가 요소 값의 열 이름으로 사용되었습니다.

- **권장 사항**: “valueColumn” 속성에 다른 값을 설정합니다.


### <a name="error-code-xmlinvalid"></a>오류 코드: XmlInvalid

- **메시지**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **원인**: 입력 XML 파일의 형식이 잘못되었습니다.

- **권장 사항**: XML 파일의 형식을 올바르게 수정합니다.


## <a name="general-copy-activity-error"></a>일반적인 복사 작업 오류

### <a name="error-code-jrenotfound"></a>오류 코드: JreNotFound

- **메시지**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **원인**: 자체 호스팅 IR이 Java 런타임을 찾을 수 없습니다. 특정 원본을 읽으려면 Java 런타임이 필요합니다.

- **권장 사항**: 통합 런타임 환경을 확인합니다. [자체 호스팅 통합 런타임 사용](./format-parquet.md#using-self-hosted-integration-runtime)을 참조하세요.


### <a name="error-code-wildcardpathsinknotsupported"></a>오류 코드: WildcardPathSinkNotSupported

- **메시지**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **원인**: 싱크 데이터 세트가 와일드카드 값을 지원하지 않습니다.

- **권장 사항**: 싱크 데이터 세트를 확인하고 와일드카드 값을 사용하지 않고 경로를 다시 작성합니다.


### <a name="fips-issue"></a>FIPS 문제

- **증상**: FIPS 지원 자체 호스팅 IR 머신에서 다음과 같은 오류 메시지와 함께 복사 작업이 실패했습니다. `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **원인**: 이 오류는 Azure Blob, SFTP 등과 같은 커넥터를 사용하여 데이터를 복사하는 경우에 발생할 수 있습니다. FIPS(Federal Information Processing Standards)는 사용이 허용되는 특정 암호화 알고리즘 세트를 정의합니다. 머신에서 FIPS 모드가 사용하도록 설정된 경우, 일부 시나리오에서 복사 작업이 사용하는 일부 암호화 클래스가 차단됩니다.

- **해결 방법**: [Microsoft에서 더 이상 “FIPS 모드”를 권장하지 않는 이유](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)를 알아보고 자체 호스팅 IR 머신에서 FIPS를 사용하지 않을 수 있는지 검토합니다.

    또는 FIPS를 우회하여 활동 실행이 성공적으로 진행되도록 하려는 경우에는 다음을 수행합니다.

    1. 자체 호스팅 IR이 설치된 폴더를 엽니다. 경로는 보통 *C:\Program Files\Microsoft Integration Runtime \<IR version>\Shared* 입니다.

    2. *diawp.exe.config* 파일을 열고 `<runtime>` 섹션의 끝에 다음과 같이 `<enforceFIPSPolicy enabled="false"/>`를 추가합니다.

        :::image type="content" source="./media/connector-troubleshoot-guide/disable-fips-policy.png" alt-text="FIPS가 사용하지 않도록 설정된 diawp.exe.config 파일의 섹션을 보여 주는 스크린샷.":::

    3. 파일을 저장하고 자체 호스팅 IR 머신을 다시 시작합니다.

### <a name="error-code-jniexception"></a>오류 코드: JniException

- **메시지**: `An error occurred when invoking Java Native Interface.`

- **원인**: 오류 메시지에 "JVM을 만들 수 없습니다. JNI 반환 코드 [-6][JNI 호출 실패: 잘못된 인수]"가 포함된 경우 가능한 원인은 일부 잘못된(전역) 인수가 설정되어 있으므로 JVM을 만들 수 없다는 것입니다.

- **권장 사항**: 자체 호스팅 통합 런타임의 ‘각 노드’를 호스트하는 머신에 로그인합니다. 시스템 변수가 `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8G`와 같이 올바르게 설정되었는지 확인합니다. 모든 통합 런타임 노드를 다시 시작한 다음, 파이프라인을 다시 실행합니다.

### <a name="error-code-getoauth2accesstokenerrorresponse"></a>오류 코드: GetOAuth2AccessTokenErrorResponse

- **메시지**: `Failed to get access token from your token endpoint. Error returned from your authorization server: %errorResponse;.`

- **원인**: 클라이언트 ID 또는 클라이언트 암호가 잘못되었으며 권한 부여 서버에서 인증이 실패했습니다.

- **권장 사항**: 권한 부여 서버의 OAuth2 클라이언트 자격 증명 흐름 설정을 모두 수정합니다.

### <a name="error-code-failedtogetoauth2accesstoken"></a>오류 코드: FailedToGetOAuth2AccessToken

- **메시지**: `Failed to get access token from your token endpoint. Error message: %errorMessage;.`

- **원인**: OAuth2 클라이언트 자격 증명 흐름 설정이 잘못되었습니다.

- **권장 사항**: 권한 부여 서버의 OAuth2 클라이언트 자격 증명 흐름 설정을 모두 수정합니다.

### <a name="error-code-oauth2accesstokentypenotsupported"></a>오류 코드: OAuth2AccessTokenTypeNotSupported

- **메시지**: `The toke type '%tokenType;' from your authorization server is not supported, supported types: '%tokenTypes;'.`

- **원인**: 권한 부여 서버가 지원되지 않습니다.

- **권장 사항**: 지원되는 토큰 형식의 토큰을 반환할 수 있는 권한 부여 서버를 사용합니다.

### <a name="error-code-oauth2clientidcolonnotallowed"></a>오류 코드: OAuth2ClientIdColonNotAllowed

- **메시지**: `The character colon(:) is not allowed in clientId for OAuth2ClientCredential authentication.`

- **원인**: 클라이언트 ID에 잘못된 문자 콜론(`:`)이 포함되어 있습니다.

- **권장 사항**: 유효한 클라이언트 ID를 사용합니다.

### <a name="error-code-managedidentitycredentialobjectnotsupported"></a>오류 코드: ManagedIdentityCredentialObjectNotSupported

- **메시지**: `Managed identity credential is not supported in this version ('%version;') of Self Hosted Integration Runtime.`

- **권장 사항**: 지원되는 버전을 확인하고 통합 런타임을 더 높은 버전으로 업그레이드합니다.

### <a name="error-code-querymissingformatsettingsindataset"></a>오류 코드: QueryMissingFormatSettingsInDataset

- **메시지**: `The format settings are missing in dataset %dataSetName;.`

- **원인**: 데이터 세트 형식이 이진이며 지원되지 않습니다.

- **권장 사항**: 대신 DelimitedText, Json, Avro, Orc 또는 Parquet 데이터 세트를 사용합니다.

- **원인**: 파일 스토리지의 경우 데이터 세트에 형식 설정이 없습니다.

- **권장 사항**: 데이터 세트에서 "이진 복사"를 선택 취소하고 올바른 형식 설정으로 지정합니다.

### <a name="error-code-queryunsupportedcommandbehavior"></a>오류 코드: QueryUnsupportedCommandBehavior

- **메시지**: `The command behavior "%behavior;" is not supported.`

- **권장 사항**: 명령 동작을 미리 보기 또는 GetSchema API 요청 URL에 대한 매개 변수로 추가하지 마세요.

### <a name="error-code-dataconsistencyfailedtogetsourcefilemetadata"></a>오류 코드: DataConsistencyFailedToGetSourceFileMetadata

- **메시지**: `Failed to retrieve source file ('%name;') metadata to validate data consistency.`

- **원인**: 싱크 데이터 저장소에서 일시적인 문제가 발생했거나 싱크 데이터 저장소에서 메타데이터를 검색할 수 없습니다.

### <a name="error-code-dataconsistencyfailedtogetsinkfilemetadata"></a>오류 코드: DataConsistencyFailedToGetSinkFileMetadata

- **메시지**: `Failed to retrieve sink file ('%name;') metadata to validate data consistency.`

- **원인**: 싱크 데이터 저장소에서 일시적인 문제가 발생했거나 싱크 데이터 저장소에서 메타데이터를 검색할 수 없습니다.

### <a name="error-code-dataconsistencyvalidationnotsupportedfornondirectbinarycopy"></a>오류 코드: DataConsistencyValidationNotSupportedForNonDirectBinaryCopy

- **메시지**: `Data consistency validation is not supported in current copy activity settings.`

- **원인**: 데이터 일관성 유효성 검사는 직접 이진 복사 시나리오에서만 지원됩니다.

- **권장 사항**: 복사 작업 페이로드에서 'validateDataConsistency' 속성을 제거합니다.

### <a name="error-code-dataconsistencyvalidationnotsupportedforlowversionselfhostedintegrationruntime"></a>오류 코드: DataConsistencyValidationNotSupportedForLowVersionSelfHostedIntegrationRuntime

- **메시지**: `'validateDataConsistency' is not supported in this version ('%version;') of Self Hosted Integration Runtime.`

- **권장 사항**: 지원되는 통합 런타임 버전을 확인하고 더 높은 버전으로 업그레이드하거나 복사 활동에서 'validateDataConsistency' 속성을 제거합니다.

### <a name="error-code-skipmissingfilenotsupportedfornondirectbinarycopy"></a>오류 코드: SkipMissingFileNotSupportedForNonDirectBinaryCopy

- **메시지**: `Skip missing file is not supported in current copy activity settings, it's only supported with direct binary copy with folder.`

- **권장 사항**: 복사 활동 페이로드에서 skipErrorFile 설정의 'fileMissing'을 제거합니다.

### <a name="error-code-skipinconsistencydatanotsupportedfornondirectbinarycopy"></a>오류 코드: SkipInconsistencyDataNotSupportedForNonDirectBinaryCopy

- **메시지**: `Skip inconsistency is not supported in current copy activity settings, it's only supported with direct binary copy when validateDataConsistency is true.`

- **권장 사항**: 복사 활동 페이로드에서 skipErrorFile 설정의 'dataInconsistency'를 제거합니다.

### <a name="error-code-skipforbiddenfilenotsupportedfornondirectbinarycopy"></a>오류 코드: SkipForbiddenFileNotSupportedForNonDirectBinaryCopy

- **메시지**: `Skip forbidden file is not supported in current copy activity settings, it's only supported with direct binary copy with folder.`

- **권장 사항**: 복사 활동 페이로드에서 skipErrorFile 설정의 'fileforbidden'을 제거합니다.

### <a name="error-code-skipforbiddenfilenotsupportedforthisconnector"></a>오류 코드: SkipForbiddenFileNotSupportedForThisConnector

- **메시지**: `Skip forbidden file is not supported for this connector: ('%connectorName;').`

- **권장 사항**: 복사 활동 페이로드에서 skipErrorFile 설정의 'fileforbidden'을 제거합니다.

### <a name="error-code-skipinvalidfilenamenotsupportedfornondirectbinarycopy"></a>오류 코드: SkipInvalidFileNameNotSupportedForNonDirectBinaryCopy

- **메시지**: `Skip invalid file name is not supported in current copy activity settings, it's only supported with direct binary copy with folder.`

- **권장 사항**: 복사 활동 페이로드에서 skipErrorFile 설정의 'invalidFileName'을 제거합니다.

### <a name="error-code-skipinvalidfilenamenotsupportedforsource"></a>오류 코드: SkipInvalidFileNameNotSupportedForSource

- **메시지**: `Skip invalid file name is not supported for '%connectorName;' source.`

- **권장 사항**: 복사 활동 페이로드에서 skipErrorFile 설정의 'invalidFileName'을 제거합니다.

### <a name="error-code-skipinvalidfilenamenotsupportedforsink"></a>오류 코드: SkipInvalidFileNameNotSupportedForSink

- **메시지**: `Skip invalid file name is not supported for '%connectorName;' sink.`

- **권장 사항**: 복사 활동 페이로드에서 skipErrorFile 설정의 'invalidFileName'을 제거합니다.

### <a name="error-code-skipallerrorfilenotsupportedfornonbinarycopy"></a>오류 코드: SkipAllErrorFileNotSupportedForNonBinaryCopy

- **메시지**: `Skip all error file is not supported in current copy activity settings, it's only supported with binary copy with folder.`

- **권장 사항**: 복사 활동 페이로드에서 skipErrorFile 설정의 'allErrorFile'을 제거합니다.

### <a name="error-code-deletefilesaftercompletionnotsupportedfornondirectbinarycopy"></a>오류 코드: DeleteFilesAfterCompletionNotSupportedForNonDirectBinaryCopy

- **메시지**: `'deleteFilesAfterCompletion' is not support in current copy activity settings, it's only supported with direct binary copy.`

- **권장 사항**: 'deleteFilesAfterCompletion' 설정을 제거하거나 직접 이진 복사를 사용합니다.

### <a name="error-code-deletefilesaftercompletionnotsupportedforthisconnector"></a>오류 코드: DeleteFilesAfterCompletionNotSupportedForThisConnector

- **메시지**: `'deleteFilesAfterCompletion' is not supported for this connector: ('%connectorName;').`

- **권장 사항**: 복사 활동 페이로드에서 'deleteFilesAfterCompletion' 설정을 제거합니다.

### <a name="error-code-failedtodownloadcustomplugins"></a>오류 코드: FailedToDownloadCustomPlugins

- **메시지**: `Failed to download custom plugins.`

- **원인**: 잘못된 다운로드 링크 또는 일시적인 연결 문제입니다.

- **권장 사항**: 일시적인 문제라는 메시지가 표시되면 다시 시도합니다. 문제가 지속되면 지원 팀에 문의하세요.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
*  [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
