---
title: Azure Synapse Analytics, Azure SQL Database 및 SQL Server 커넥터 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 azure Synapse analytics에서 azure Synapse analytics, Azure SQL Database 및 SQL Server 커넥터를 사용 하 여 문제를 해결 하는 방법에 대해 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/13/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 84208764621bd03959db7d695bf0616dcccc4491
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130064783"
---
# <a name="troubleshoot-the-azure-synapse-analytics-azure-sql-database-and-sql-server-connectors-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory 및 azure Synapse에서 azure Synapse Analytics, Azure SQL Database 및 SQL Server 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 azure Synapse에서 azure Synapse Analytics, Azure SQL Database 및 SQL Server 커넥터와 관련 된 일반적인 문제를 해결 하기 위한 제안 사항을 제공 합니다.

## <a name="error-code-sqlfailedtoconnect"></a>오류 코드: SqlFailedToConnect

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
    
## <a name="error-code-sqloperationfailed"></a>오류 코드: SqlOperationFailed

- **메시지**: `A database operation failed. Please search error to get more details.`

- **원인 및 권장 사항**: 오류의 원인이 여러 가지일 수 있습니다. 아래 목록에서 가능한 원인 분석과 권장 사항을 확인하세요.

    | 원인 분석                                               | 권장                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 오류 메시지에 “SqlException”이라는 문자열이 포함되어 있으면 SQL Database는 특정 작업이 실패했음을 나타내는 오류를 throw합니다. | SQL 오류가 명확하지 않은 경우 데이터베이스를 최신 호환성 수준 ‘150’으로 변경해 보세요. 최신 버전의 SQL 오류를 throw할 수 있습니다. 자세한 내용은 [설명서](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat)를 참조하세요. <br/> SQL 문제를 해결하는 방법에 대한 자세한 내용을 보려면 [데이터베이스 엔진 오류](/sql/relational-databases/errors-events/database-engine-events-and-errors)에서 SQL 오류 코드를 검색하세요. 추가 도움이 필요한 경우 Azure SQL 지원으로 문의하세요. |
    | 오류 메시지에 “PdwManagedToNativeInteropException”이라는 문자열이 포함된 경우 이 오류는 일반적으로 원본 및 싱크 열 크기의 불일치로 인해 발생합니다. | 원본 및 싱크 열의 크기를 확인합니다. 추가 도움이 필요한 경우 Azure SQL 지원으로 문의하세요. |
    | 오류 메시지에 “InvalidOperationException”이라는 문자열이 포함된 경우 일반적으로 이 오류는 잘못된 입력 데이터로 인해 발생합니다. | 문제가 발생한 행을 확인하려면 복사 작업에서 내결함성 기능을 사용하도록 설정하세요. 이렇게 하면 추가 조사를 위해 문제가 있는 행을 스토리지로 리디렉션할 수 있습니다. 자세한 내용은 [복사 작업 내결함성](./copy-activity-fault-tolerance.md)을 참조하세요. |


## <a name="error-code-sqlunauthorizedaccess"></a>오류 코드: SqlUnauthorizedAccess

- **메시지**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **원인**: 자격 증명이 올바르지 않거나 로그인 계정이 SQL 데이터베이스에 액세스할 수 없습니다.

- **권장 사항**: 로그인 계정에 SQL 데이터베이스에 액세스할 수 있는 권한이 있는지 확인합니다.


## <a name="error-code-sqlopenconnectiontimeout"></a>오류 코드: SqlOpenConnectionTimeout

- **메시지**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **원인**: 이 문제는 일시적인 SQL 데이터베이스 오류일 수 있습니다.

- **권장 사항**: 작업을 다시 시도하여 연결된 서비스 연결 문자열을 더 큰 연결 시간 제한 값으로 업데이트합니다.


## <a name="error-code-sqlautocreatetabletypemapfailed"></a>오류 코드: SqlAutoCreateTableTypeMapFailed

- **메시지**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **원인**: 자동 생성 테이블이 원본 요구 사항을 충족하지 않습니다.

- **권장 사항**: ‘매핑’에서 열 형식을 업데이트하거나 대상 서버에서 수동으로 싱크 테이블을 만듭니다.


## <a name="error-code-sqldatatypenotsupported"></a>오류 코드: SqlDataTypeNotSupported

- **메시지**: `A database operation failed. Check the SQL errors.`

- **원인**: 문제가 SQL 원본에서 발생했고 오류가 SqlDateTime 오버플로와 관련된 경우 데이터 값이 논리 형식 범위(1/1/1753 오전 12:00:00~12/31/9999 오후 11:59:59)를 초과하는 것입니다.

- **권장 사항**: 원본 SQL 쿼리에서 형식을 문자열로 캐스트하거나 복사 작업 열 매핑에서 열 형식을 *String* 으로 변경합니다.

- **원인**: 문제가 SQL 싱크에서 발생했고 오류가 SqlDateTime 오버플로와 관련된 경우 데이터 값이 싱크 테이블의 허용되는 범위를 초과하는 것입니다.

- **권장 사항**: 싱크 테이블에서 해당 열 형식을 *datetime2* 형식으로 업데이트합니다.


## <a name="error-code-sqlinvaliddbstoredprocedure"></a>오류 코드: SqlInvalidDbStoredProcedure

- **메시지**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **원인 및 권장 사항**: 오류의 원인이 여러 가지일 수 있습니다. 아래 목록에서 가능한 원인 분석과 권장 사항을 확인하세요.

  | 원인 분석                                               | 권장                                             |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | 지정 된 저장 프로시저가 잘못 되었습니다. | SQL 도구를 사용 하 여 저장 프로시저의 유효성을 검사 합니다. 저장 프로시저가 데이터를 반환할 수 있는지 확인합니다.  |
  | 조회 작업에서는 저장 프로시저에서 일부 값을 반환 해야 하지만 저장 프로시저 코드에서 값을 반환 하지 않습니다. | 저장 프로시저에서 데이터를 반환 하지 않을 것으로 예상 되는 경우 저장 프로시저 작업을 사용 합니다. |

## <a name="error-code-sqlinvaliddbquerystring"></a>오류 코드: SqlInvalidDbQueryString

- **메시지**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **원인**: 지정된 SQL 쿼리가 잘못되었습니다. 쿼리가 데이터를 반환하지 않는 것이 원인일 수 있습니다.

- **권장 사항**: SQL 도구를 사용하여 SQL 쿼리의 유효성을 검사합니다. 쿼리가 데이터를 반환할 수 있는지 확인합니다.


## <a name="error-code-sqlinvalidcolumnname"></a>오류 코드: SqlInvalidColumnName

- **메시지**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **원인**: 열을 찾을 수 없습니다. 구성이 잘못된 것일 수 있습니다.

- **권장 사항**: 쿼리의 열, 데이터 세트의 *structure* 및 작업의 *mappings* 를 확인합니다.


## <a name="error-code-sqlbatchwritetimeout"></a>오류 코드: SqlBatchWriteTimeout

- **메시지**: `Timeouts in SQL write operation.`

- **원인**: 일시적인 SQL 데이터베이스 오류로 인한 문제일 수 있습니다.

- **해결 방법**: 작업을 다시 시도합니다. 문제가 지속되면 Azure SQL 지원으로 문의하세요.


## <a name="error-code-sqlbatchwritetransactionfailed"></a>오류 코드: SqlBatchWriteTransactionFailed

- **메시지**: `SQL transaction commits failed.`

- **원인**: 예외 세부 정보에 지속적으로 트랜잭션 시간이 초과되었다고 표시되는 경우 통합 런타임과 데이터베이스 간의 네트워크 대기 시간이 기본 임계값인 30초보다 높은 것입니다.

- **권장 사항**: SQL에 연결된 서비스 연결 문자열을 120보다 크거나 값은 ‘연결 시간 제한’ 값으로 업데이트하고 작업을 다시 실행합니다.

- **원인**: 예외 세부 정보에 간헐적으로 SQL 연결이 끊어졌다고 표시되는 경우 일시적인 네트워크 오류 또는 SQL 데이터베이스 쪽 문제일 수 있습니다.

- **권장 사항**: 작업을 다시 시도하고 SQL 데이터베이스 쪽 메트릭을 검토합니다.


## <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>오류 코드: SqlBulkCopyInvalidColumnLength

- **메시지**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **원인**: SQL 대량 복사가 bcp(대량 복사 프로그램) 유틸리티 클라이언트로부터 잘못된 열 길이를 받아서 대량 복사가 실패했습니다.

- **권장 사항**: 문제가 발생한 행을 확인하려면 복사 작업에서 내결함성 기능을 사용하도록 설정합니다. 이렇게 하면 추가 조사를 위해 문제가 있는 행을 스토리지로 리디렉션할 수 있습니다. 자세한 내용은 [복사 작업 내결함성](./copy-activity-fault-tolerance.md)을 참조하세요.


## <a name="error-code-sqlconnectionisclosed"></a>오류 코드: SqlConnectionIsClosed

- **메시지**: `The connection is closed by SQL Database.`

- **원인**: SQL 연결은 높은 동시 실행이 진행되어 서버가 연결을 종료하면 SQL 데이터베이스에 의해 종료됩니다.

- **권장 사항**: 연결을 다시 시도합니다. 문제가 지속되면 Azure SQL 지원으로 문의하세요.

## <a name="error-code-sqlserverinvalidlinkedservicecredentialmissing"></a>오류 코드: SqlServerInvalidLinkedServiceCredentialMissing

- **메시지**: `The SQL Server linked service is invalid with its credential being missing.`

- **원인**: 연결된 서비스가 올바르게 구성되지 않았습니다.

- **권장 사항**: SQL 서버 연결된 서비스의 유효성을 검사하고 수정합니다. 

## <a name="error-code-sqlparallelfailedtodetectpartitioncolumn"></a>오류 코드: SqlParallelFailedToDetectPartitionColumn

- **메시지**: `Failed to detect the partition column with command '%command;', %message;.`

- **원인**: 테이블에 기본 키 또는 고유 키가 없습니다.

- **권장 사항**: 테이블에서 기본 키 또는 고유 인덱스가 생성되었는지 확인합니다. 

## <a name="error-code-sqlparallelfailedtodetectphysicalpartitions"></a>오류 코드: SqlParallelFailedToDetectPhysicalPartitions

- **메시지**: `Failed to detect the physical partitions with command '%command;', %message;.`

- **원인**: 테이블에 대한 실제 파티션이 생성되지 않았습니다. 데이터베이스를 확인합니다.

- **권장 사항**: 이 문제를 해결하려면 [분할된 테이블 및 인덱스 만들기](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes?view=sql-server-ver15&preserve-view=true)를 참조하세요.

## <a name="error-code-sqlparallelfailedtogetpartitionrangesynapse"></a>오류 코드: SqlParallelFailedToGetPartitionRangeSynapse

- **메시지**: `Failed to get the partitions for azure synapse with command '%command;', %message;.`

- **원인**: 테이블에 대한 실제 파티션이 생성되지 않았습니다. 데이터베이스를 확인합니다.

- **권장 사항**: 이 문제를 해결하려면 [전용 SQL 풀에서 테이블 분할](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-partition.md)을 참조하세요.

## <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>오류 메시지: 문자열을 uniqueIDentifier로 변환하지 못했습니다.

- **증상**: 스테이징된 복사본 및 PolyBase를 사용하여 테이블 형식 데이터 원본(예: SQL Server)에서 Azure Synapse Analytics로 데이터를 복사할 때 다음 오류가 발생합니다.

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **원인**: Azure Synapse Analytics PolyBase는 빈 문자열을 GUID로 변환할 수 없습니다.

- **해결 방법**: 복사 작업 싱크의 PolyBase 설정에서 **형식 기본값 사용** 옵션을 *false* 로 설정합니다.


## <a name="error-message-expected-data-type-decimalxx-offending-value"></a>오류 메시지: 필요한 데이터 형식: DECIMAL(x,x), 잘못된 값

- **증상**: 스테이징된 복사본 및 PolyBase를 사용하여 테이블 형식 데이터 원본(예: SQL Server)에서 Azure Synapse Analytics로 데이터를 복사할 때 다음 오류가 발생합니다.

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **원인**: Azure Synapse Analytics PolyBase는 십진수 열에 빈 문자열(Null 값)을 삽입할 수 없습니다.

- **해결 방법**: 복사 작업 싱크의 PolyBase 설정에서 **형식 기본값 사용** 옵션을 false로 설정합니다.


## <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>오류 메시지: Java 예외 메시지: HdfsBridge::CreateRecordReader

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


## <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>오류 메시지: HTTP 조건부 헤더를 사용하여 지정한 조건이 충족되지 않습니다.

- **증상**: SQL 쿼리를 사용하여 Azure Synapse Analytics에서 데이터를 풀할 때 다음 오류가 발생합니다.

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **원인**: Azure Storage의 외부 테이블을 쿼리할 때 Azure Synapse Analytics에서 문제가 발생했습니다.

- **해결 방법**: SSMS(SQL Server Management Studio)에서 동일한 쿼리를 실행하여 동일한 결과가 나오는지 확인합니다. 이렇게 하려면 Azure Synapse Analytics에 대해 지원 티켓을 생성하고 Azure Synapse Analytics 서버 및 데이터베이스 이름을 알려 주세요.


## <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>성능 계층이 낮아서 복사가 실패한 경우

- **증상**: Azure SQL Database로 데이터를 복사할 때 다음과 같은 오류가 표시됩니다. `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **원인**: Azure SQL Database s1이 I/O(입출력) 제한에 도달했습니다.

- **해결 방법**: Azure SQL Database 성능 계층을 업그레이드하여 문제를 해결합니다. 


## <a name="sql-table-cant-be-found"></a>SQL 테이블을 찾을 수 없음 

- **증상**: 하이브리드에서 온-프레미스 SQL Server 테이블로 데이터를 복사할 때 다음과 같은 오류가 표시됩니다. `Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **원인**: 사용 중인 SQL 계정에 .NET SqlBulkCopy.WriteToServer에서 발생한 요청을 실행하는 데 필요한 권한이 없습니다.

- **해결 방법**: 보다 높은 권한을 갖는 SQL 계정을 사용합니다.


## <a name="error-message-string-or-binary-data-is-truncated"></a>오류 메시지: 문자열 또는 이진 데이터가 잘림

- **증상**: 온-프레미스 Azure SQL Server 테이블로 데이터를 복사할 때 오류가 발생합니다. 

- **원인**: Cx SQL 테이블 스키마 정의에 예상보다 길이가 작은 열이 하나 이상 있습니다.

- **해결 방법**: 이 문제를 해결하려면 다음 방법을 시도합니다.

    1. 어느 행에 문제가 있는지 알아보려면 SQL 싱크 [내결함성](./copy-activity-fault-tolerance.md), 그중에서도 특히 “redirectIncompatibleRowSettings”를 적용합니다.

        > [!NOTE]
        > 내결함성을 사용하면 실행 시간이 길어져서 비용이 높아질 수 있습니다.

    2. SQL 테이블 스키마 열 길이를 기준으로 리디렉션된 데이터를 재차 확인하여 어느 열을 업데이트해야 하는지 알아봅니다.

    3. 결과에 따라 테이블 스키마를 업데이트합니다.

## <a name="error-code-faileddboperation"></a>오류 코드: FailedDbOperation

- **메시지**: `User does not have permission to perform this action.`

- **권장 사항**: PolyBase를 사용하여 데이터를 로드하는 동안 Azure Synapse Analytics 커넥터에 구성된 사용자에게 대상 데이터베이스에 대한 'CONTROL' 권한이 있어야 합니다. 자세한 내용은 이 [문서](./connector-azure-sql-data-warehouse.md#required-database-permission)를 참조하세요.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

- [커넥터 문제 해결 가이드](connector-troubleshoot-guide.md)
- [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
- [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
- [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
