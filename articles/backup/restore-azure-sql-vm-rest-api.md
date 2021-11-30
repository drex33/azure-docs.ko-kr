---
title: REST API를 사용 하 여 Azure vm의 SQL server 데이터베이스 복원
description: REST API를 사용 하 여에서 만든 복원 지점에서 Azure VM의 SQL 서버 데이터베이스를 복원 하는 방법에 대해 알아봅니다 Azure Backup
ms.topic: conceptual
ms.date: 11/30/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 0bc8c822090e6498a5adfc5dc9edc4869e172118
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133289574"
---
# <a name="restore-sql-server-databases-in-azure-vms-with-rest-api"></a>REST API를 사용 하 여 Azure vm의 SQL Server 데이터베이스 복원

이 문서에서는 REST API를 사용 하 여 [Azure Backup](./backup-overview.md) 만든 복원 지점에서 Azure VM의 SQL 서버 데이터베이스를 복원 하는 방법을 설명 합니다.

이 문서를 끝까지 읽으면 REST API를 사용하여 다음 작업을 수행하는 방법을 배우게 될 것입니다.

- 백업 된 SQL 데이터베이스의 복원 지점이 표시 됩니다.
- 전체 SQL 데이터베이스를 복원 합니다.

## <a name="prerequisites"></a>필수 조건

복원에 SQL 데이터베이스를 백업 했다고 가정 합니다. 없는 경우 create [REST API를 사용 하 여 Azure vm의 Backup SQL Server 데이터베이스](backup-azure-sql-vm-rest-api.md) 를 참조 하세요.

이 문서에서는 다음 리소스를 사용합니다.

- **Recovery Services 자격 증명 모음**: *SQLServer2012*
- **리소스 그룹**: *SQLServerSelfHost*
- **SQL server**: *sqlserver-0*
- **SQL 데이터베이스**: *msdb*

## <a name="primary-region-restore"></a>기본 영역 복원

주 지역에서 일반적인 복원 작업을 트리거하려면 다음 섹션을 참조 하세요. 지역 간 복원의 경우 [지역 간](#cross-region-restore) 복원 섹션을 참조 하세요.

### <a name="fetch-containername-and-protecteditemname"></a>ContainerName 및 ProtectedItemName 페치

대부분의 복원 관련 API 호출의 경우 `{containerName}` 및 URI 매개 변수에 대 한 값을 전달 해야 `{protectedItemName}` 합니다. [GET backupprotectableitems](/rest/api/backup/protecteditems/get) 작업의 응답 본문에서 ID 특성을 사용하여 이러한 매개 변수의 값을 검색합니다. 이 예에서는 보호 하려는 데이터베이스의 ID는 다음과 같습니다.

```
/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0/protectedItems/sqldatabase;mssqlserver;msdb
```

값은 다음과 같이 변환 됩니다.

- `{containername}`: *VMAppContainer; 계산 SQLServerPMDemo; sqlserver-0*
- `{protectedItemName}`: *backup-sqldatabase; mssqlserver; msdb*

### <a name="fetch-recovery-points-for-backed-up-sql-database"></a>백업 SQL 데이터베이스에 대 한 복구 지점의 페치

백업 된 데이터베이스를 복원 하려면 다음을 수행 합니다.

1. 복구 지점을 선택 하 여 복원 작업을 수행 합니다.
1. [복구 지점 목록](/rest/api/site-recovery/recoverypoints/listbyreplicationprotecteditems) REST API 호출을 사용 하 여 백업 된 항목의 사용 가능한 복구 지점을 나열 합니다. 모든 관련 값이 포함 된 *GET* 작업입니다.

    ```http
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2016-12-01
    ```

   다음과 같이 URI 값을 설정합니다.

   - `{fabricName}`: *Azure*
   - `{vaultName}`: *SQLServer2012*
   - `{containerName}`: *VMAppContainer; 계산 SQLServerPMDemo; sqlserver-0*
   - `{protectedItemName}`: *backup-sqldatabase; mssqlserver; msdb*
   - `{resourceGroupName}`: *SQLServerSelfHost*

   GET URI에는 필요한 모든 매개 변수가 있습니다. 추가 요청 본문이 필요없습니다.

    ```http
    GET "https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.RecoveryServices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0/protectedItems/sqldatabase;mssqlserver;msdb/recoveryPoints?api-version=2016-12-01"
    ```

#### <a name="example-response-for-fetch-recovery-points"></a>복구 지점 페치에 대한 응답의 예

*GET* URI를 제출 하면 200 응답이 반환 됩니다.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: fab6cc6f-db1e-4ac1-acac-fc82ebdb1fdb
x-ms-client-request-id: 6fb93717-2876-47df-b01f-d53af5f08785; 6fb93717-2876-47df-b01f-d53af5f08785
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14999
x-ms-correlation-request-id: fab6cc6f-db1e-4ac1-acac-fc82ebdb1fdb
x-ms-routing-request-id: SOUTHINDIA:20180604T061127Z:fab6cc6f-db1e-4ac1-acac-fc82ebdb1fdb
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 06:11:26 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0/protectedItems/SQLDataBase;mssqlserver;msdb/recoveryPoints/55515936059579",
      "name": "55515936059579",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "AzureWorkloadSQLRecoveryPoint",
        "recoveryPointTimeInUTC": "2018-06-01T22:15:12Z",
        "type": "Full"
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0/protectedItems/SQLDataBase;mssqlserver;msdb/recoveryPoints/62043109781074",
      "name": "62043109781074",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "AzureWorkloadSQLRecoveryPoint",
        "recoveryPointTimeInUTC": "2018-05-31T22:15:08Z",
        "type": "Full"
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0/protectedItems/SQLDataBase;mssqlserver;msdb/recoveryPoints/69710749096214",
      "name": "69710749096214",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "AzureWorkloadSQLRecoveryPoint",
        "recoveryPointTimeInUTC": "2018-05-30T22:15:09Z",
        "type": "Full"
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0/protectedItems/SQLDataBase;mssqlserver;msdb/recoveryPoints/55459165802209",
      "name": "55459165802209",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "AzureWorkloadSQLRecoveryPoint",
        "recoveryPointTimeInUTC": "2018-05-29T22:15:15Z",
        "type": "Full"
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0/protectedItems/SQLDataBase;mssqlserver;msdb/recoveryPoints/56798287946753",
      "name": "56798287946753",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "AzureWorkloadSQLRecoveryPoint",
        "recoveryPointTimeInUTC": "2018-05-28T13:18:15Z",
        "type": "Full"
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0/protectedItems/SQLDataBase;mssqlserver;msdb/recoveryPoints/DefaultRangeRecoveryPoint",
      "name": "DefaultRangeRecoveryPoint",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "AzureWorkloadSQLPointInTimeRecoveryPoint",
        "timeRanges": [
          {
            "startTime": "2018-05-28T11:03:34Z",
            "endTime": "2018-06-02T00:02:31Z"
          }
        ],
        "type": "Log"
      }
    }
  ]
}
```

복구 지점은 `{name}` 위의 응답에서 필드를 사용 하 여 식별 됩니다.

### <a name="database-recovery-using-rest-api"></a>REST API를 사용 하 여 데이터베이스 복구

복원을 트리거하는 것은 *POST* 요청입니다. 이 작업을 수행 하려면 [트리거 복원](/rest/api/backup/restores/trigger) REST API를 사용 합니다.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

값 {containerName}과 {protectedItemName}은 [여기](#fetch-containername-and-protecteditemname)에 설정된 것과 같고, recoveryPointID는 위에서 언급한 복구 지점의 {name} 필드입니다.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SQLServerSelfHost/providers/Microsoft.RecoveryServices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0/protectedItems/sqldatabase;mssqlserver;msdb/recoveryPoints/56798287946753/restore?api-version=2019-05-13'
```

#### <a name="create-request-body"></a>요청 본문 만들기

##### <a name="request-body-example-to-restore-a-database-to-the-same-data-directory"></a>동일한 데이터 디렉터리에 데이터베이스를 복원 하는 요청 본문 예제

다음 요청 본문에서는 동일한 데이터 디렉터리에 대 한 SQL 데이터베이스 복원을 트리거하는 데 필요한 속성을 정의 합니다.

```json
{
   "properties":{
        "objectType":"AzureWorkloadSQLRestoreRequest",
        "shouldUseAlternateTargetLocation":false,
        "isNonRecoverable":false,
        "targetInfo":{"ContainerName": "compute;SQLServerPMDemo;sqlserver-0", "DatabaseName" : "SQLINSTANCE/msdb"},
        "alternateDirectoryPaths":null,
        "isFallbackOnDefaultDirectoryEnabled":true,
        "recoveryType":"AlternateLocation",
        "sourceResourceId":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SQLServerPMDemo/providers/Azure/virtualmachines/compute;SQLServerPMDemo;sqlserver-0"       
    }
}
```

##### <a name="request-body-example-to-restore-a-database-to-an-alternate-data-directory"></a>데이터베이스를 대체 데이터 디렉터리로 복원 하는 요청 본문 예제

다음 요청 본문에서는 동일한 데이터 디렉터리에 대 한 SQL 데이터베이스 복원을 트리거하는 데 필요한 속성을 정의 합니다.

```json
{
    "properties":{ 
        "objectType":"AzureWorkloadSQLRestoreRequest",
        "shouldUseAlternateTargetLocation":true,
        "isNonRecoverable":false,
        "targetInfo":
        {
            "overwriteOption":"Overwrite","containerName":"compute;oneboxrg;oneboxvm","databaseName":"SQLINSTANCE/msdb"},
            "alternateDirectoryPaths":[{"mappingType":"Log","sourcePath":"C:\\SQLfiles\\Default.ldf","targetPath":"C:\\SQLFiles\\Temp.ldf"}],
            "recoveryType":"AlternateLocation",
            "sourceResourceId":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SQLServerPMDemo/providers/Azure/virtualmachines/compute;SQLServerPMDemo;sqlserver-0"
        }
    }
}
```

#### <a name="response"></a>응답

복원 작업의 트리거는 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 이 작업을 수행하면 별도로 추적해야 하는 다른 작업이 생성됩니다.

다른 작업을 만들 때 202 (수락 됨) 및 해당 작업이 완료 되 면 200 (OK)의 두 응답을 반환 합니다.

##### <a name="response-example"></a>응답 예제

```http
Status Code:
OK

Headers:
Pragma                        : no-cache
Cache-Control                 : no-cache
Server                        : Microsoft-IIS/10.0,Microsoft-IIS/10.0
X-Content-Type-Options        : nosniff
x-ms-request-id               : f17973f5-c788-482f-8aad-6bb50e647a2e
x-ms-client-request-id        : b0356a0e-c68d-4ac2-a53f-4f546685146d,b0356a0e-c68d-4ac2-a53f-4f546685146d
X-Powered-By                  : ASP.NET
Strict-Transport-Security     : max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-resource-requests: 149
x-ms-correlation-request-id   : f17973f5-c788-482f-8aad-6bb50e647a2e
x-ms-routing-request-id       : SOUTHINDIA:20210801T104711Z:f17973f5-c788-482f-8aad-6bb50e647a2e
Date                          : Sun, 01 Aug 2021 10:47:11 GMT

{
    "id":"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SQLServerSelfHost/providers/Microsoft.RecoveryServices/vaults/SQLServer2012/backupJobs/0bda1a53-73fa-427e-9a1c-72a2016adee3",
    "name": "0bda1a53-73fa-427e-9a1c-72a2016adee3",
    "type": "Microsoft.RecoveryServices/vaults/backupJobs",
    "properties": {
    "jobType": "AzureWorkloadJob",
    "actionsInfo": [
      1
    ],
    "workloadType": "SQLDataBase",
    "duration": "PT1.6543659S",
    "extendedInfo": {
      "tasksList": [
        {
          "taskId": "Transfer data from vault",
          "status": "InProgress"
        }
      ],
      "propertyBag": {
        "Job Type": "Recovery to the original database"
      }
    },
    "isUserTriggered": true,
    "entityFriendlyName": "msdb [sqlserver-0]",
    "backupManagementType": "AzureWorkload",
    "operation": "Restore",
    "status": "InProgress",
    "startTime": "2021-08-01T10:47:09.5865449Z",
    "activityId": "b0356a0e-c68d-4ac2-a53f-4f546685146d"
  }
}
```

## <a name="cross-region-restore"></a>지역 간 복원

지역 간 복원을 사용 하도록 설정한 경우 복구 지점은 보조 쌍으로 연결 된 지역에도 복제 됩니다. 그런 다음 이러한 복구 지점과 해당 쌍을 이루는 지역에 있는 컴퓨터에 대 한 복원을 트리거할 수 있습니다. 정상적인 복원에서는 대상 컴퓨터를 보조 지역의 대상 자격 증명 모음에 등록 해야 합니다. 다음 단계는 종단 간 프로세스를 설명 합니다.

1. 보조 지역에 복제 되는 백업 항목을 가져옵니다. 

   아래 예제에는 다음이 있어야 합니다.

   - 컨테이너: *VMAppContainer; 계산 SQLServerPMDemo; sqlserver-0*
   - 이 문서의 앞부분에서 설명한 단계를 사용 하 여 복원 해야 하는 데이터베이스에 대 한 보호 된 항목: *backup-sqldatabase; mssqlserver; msdb*)

1. 보조 지역에 복제 되는 복구 지점의 (고유 및/또는 로그)을 가져옵니다.
1. 보조 쌍으로 연결 된 지역 내에서 자격 증명 모음에 등록 된 대상 서버를 선택 합니다.
1. 해당 서버에 대 한 복원을 트리거하고 *JobId* 를 사용 하 여 추적 합니다.

### <a name="fetch-distinct-recovery-points-from-the-secondary-region"></a>보조 지역에서 고유 복구 지점의 페치

[복구 지점의 목록 API](/rest/api/backup/recovery-points-crr/list) 를 사용 하 여 보조 지역의 데이터베이스에 대해 사용 가능한 복구 지점의 목록을 가져올 수 있습니다. 다음 예에서는 지정 된 시간 범위 내에서 전체 및 차등 복구 지점의 페치를 위해 선택적 필터를 적용 합니다.

```http
GET "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SQLServerSelfHost/providers/Microsoft.RecoveryServices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;compute;SQLServerPMDemo;sqlserver-0/protectedItems/SQLDataBase;mssqlserver;msdb/recoveryPoints/?$filter=startDate eq'2021-07-25 08:41:32 AM' and endDate eq '2021-08-01 08:41:45 AM' and restorePointQueryType eq 'FullAndDifferential' and extendedInfo eq 'True'&api-version=2018-12-20"
```

#### <a name="example-response-for-fetch-recovery-points"></a>복구 지점의 가져오기에 대 한 예제 응답

```http
Headers:
Pragma                        : no-cache
X-Content-Type-Options        : nosniff
x-ms-request-id               : 66b3fbb4-e38a-4a4b-98c7-56db66ab52e6
x-ms-client-request-id        : 35eb7834-8b5c-4a2c-adda-eee2ed02eb08,35eb7834-8b5c-4a2c-adda-eee2ed02eb08
Strict-Transport-Security     : max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-resource-requests: 149
x-ms-correlation-request-id   : 66b3fbb4-e38a-4a4b-98c7-56db66ab52e6
x-ms-routing-request-id       : SOUTHINDIA:20210801T102906Z:66b3fbb4-e38a-4a4b-98c7-56db66ab52e6
Cache-Control                 : no-cache
Date                          : Sun, 01 Aug 2021 10:29:06 GMT
Server                        : Microsoft-IIS/10.0
X-Powered-By                  : ASP.NET

Body:
{
  "value": [
    {
      "id":
"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SQLServerSelfHost/providers/Microsoft.RecoveryServices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;compute;SQLServerPMDemo;sqlserver-0/protectedItems/SQLDataBase;mssqlserver;msdb/RecoveryPoints/932604119111216382",
      "name": "932604119111216382",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "AzureWorkloadSQLRecoveryPoint",
        "recoveryPointTimeInUTC": "2021-07-31T16:33:48Z",
        "type": "Full"
      }
    },
    {
      "id":
"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SQLServerSelfHost/providers/Microsoft.RecoveryServices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;compute;SQLServerPMDemo;sqlserver-0/protectedItems/SQLDataBase;mssqlserver;msdb/RecoveryPoints/932599942005436803",
      "name": "932599942005436803",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "AzureWorkloadSQLRecoveryPoint",
        "recoveryPointTimeInUTC": "2021-07-30T16:33:49Z",
        "type": "Full"
      }
    },
.....
```

복구 지점은 `{name}` 위의 응답에서 필드를 사용 하 여 식별 됩니다.

### <a name="get-access-token"></a>액세스 토큰 가져오기

지역 간 복원을 수행 하려면 Azure Backup 서비스 간에 적절 한 통신을 가능 하 게 하는 액세스 토큰이 필요 합니다. 액세스 토큰을 가져오려면 다음 단계를 수행합니다.

1. [AAD 속성 API](/rest/api/backup/aad-properties/get) 를 사용 하 여 보조 지역에 대 한 Azure Active Directory (AAD) 속성을 가져옵니다 (아래 예제에서는 *westus* ).

    ```http
    GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.RecoveryServices/locations/westus/backupAadProperties?api-version=2018-12-20
    ```

   반환 되는 응답은 다음과 같은 형식입니다.

    ```json
    {
      "properties": {
        "tenantId": "00000000-0000-0000-0000-000000000000",
        "audience": "https://RecoveryServices/IaasCoord/aadmgmt/wus",
        "servicePrincipalObjectId": "00000000-0000-0000-0000-000000000000"
      }
    }
    ```

1. 액세스 토큰 [가져오기 API](/rest/api/backup/recovery-points-get-access-token-for-crr/get-access-token) 를 사용 하 여 Azure Backup 서비스 간의 통신을 가능 하 게 하는 액세스 토큰을 가져옵니다.

    ```http
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/accessToken?api-version=2018-12-20
    ```

   요청 본문의 경우 이전 단계의 AAD 속성 API에서 반환된 응답의 내용을 붙여넣습니다.

   반환 되는 응답 형식은 다음과 같습니다.

    ```json
    {
      "protectableObjectUniqueName": "MSSQLSERVER/model",
        "protectableObjectFriendlyName": "msdb",
        "protectableObjectWorkloadType": "SQL",
        "protectableObjectProtectionState": "Protected",
        "protectableObjectContainerHostOsName": "sqlserver-0",
        "protectableObjectParentLogicalContainerName": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SQLServerPMDemo/providers/Microsoft.Compute/virtualMachines/sqlserver-0",
        "containerId": "0000000",
        "policyName": "HourlyLogBackup",
        "policyId": "00000000-0000-0000-0000-000000000000",
        "objectType": "WorkloadCrrAccessToken",
        "accessTokenString": "<access-token-string>",
        "subscriptionId": "00000000-0000-0000-0000-000000000000",
        "resourceGroupName": "SQLServerSelfHost",
        "resourceName": "SQLServer2012",
        "resourceId": "0000000000000000000",
        "protectionContainerId": 0000000,
        "recoveryPointId": "932603497994988273",
        "recoveryPointTime": "7/31/2021 4:33:17 PM",
        "containerName": "Compute;SQLServerPMDemo;sqlserver-0",
        "containerType": "VMAppContainer",
        "backupManagementType": "AzureWorkload",
        "datasourceType": "SQLDataBase",
        "datasourceName": "msdb",
        "datasourceId": "932350676859704517",
        "datasourceContainerName": "Compute;SQLServerPMDemo;sqlserver-0",
        "coordinatorServiceStampId": "00000000-0000-0000-0000-000000000000",
        "coordinatorServiceStampUri": "https://pod01-wbcm1.eus.backup.windowsazure.com",
        "protectionServiceStampId": "00000000-0000-0000-0000-000000000000",
        "protectionServiceStampUri": "https://pod01-prot1j-int.eus.backup.windowsazure.com",
        "rpOriginalSAOption": false,
        "rpIsManagedVirtualMachine": false,
        "bMSActiveRegion": "EastUS"
    }
    ```

### <a name="restore-disks-to-the-secondary-region"></a>디스크를 보조 지역으로 복원

[지역 간 복원 트리거 API](/rest/api/backup/cross-region-restore/trigger)를 사용하여 항목을 보조 지역으로 복원합니다.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.RecoveryServices/locations/{azureRegion}/backupCrossRegionRestore?api-version=2018-12-20
```

요청 본문에는 다음 두 부분이 있어야 합니다.

1. *crossRegionRestoreAccessDetails*: 이전 단계에서 수행 된 액세스 토큰 가져오기 API 요청에서 응답의 *속성* 블록을 붙여넣어 요청 본문의이 세그먼트를 채웁니다.

2. *restoreRequest*: 요청 본문의 *restoreRequest* 세그먼트를 채우려면 데이터베이스를 복원 해야 하는 컨테이너 (보조 지역의 자격 증명 모음에 등록 됨)의 세부 정보를 복원 된 데이터베이스를 저장할 이름과 함께 전달 해야 합니다. 보조 지역에 대 한 전체 백업 복원을 수행 하려면 *AlternateLocation* 을 복구 유형으로 지정 합니다.

VM의 디스크를 보조 지역으로 복원 하는 샘플 요청 본문은 다음과 같습니다.

```json
  {
  "crossRegionRestoreAccessDetails": {
          "protectableObjectUniqueName": "MSSQLSERVER/model",
        "protectableObjectFriendlyName": "msdb",
        "protectableObjectWorkloadType": "SQL",
        "protectableObjectProtectionState": "Protected",
        "protectableObjectContainerHostOsName": "sqlserver-0",
        "protectableObjectParentLogicalContainerName": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SQLServerPMDemo/providers/Microsoft.Compute/virtualMachines/sqlserver-0",
        "containerId": "0000000",
        "policyName": "HourlyLogBackup",
        "policyId": "00000000-0000-0000-0000-000000000000",
        "objectType": "WorkloadCrrAccessToken",
        "accessTokenString": "<access-token-string>",
        "subscriptionId": "00000000-0000-0000-0000-000000000000",
        "resourceGroupName": "SQLServerSelfHost",
        "resourceName": "SQLServer2012",
        "resourceId": "0000000000000000000",
        "protectionContainerId": 0000000,
        "recoveryPointId": "932603497994988273",
        "recoveryPointTime": "7/31/2021 4:33:17 PM",
        "containerName": "Compute;SQLServerPMDemo;sqlserver-0",
        "containerType": "VMAppContainer",
        "backupManagementType": "AzureWorkload",
        "datasourceType": "SQLDataBase",
        "datasourceName": "msdb",
        "datasourceId": "932350676859704517",
        "datasourceContainerName": "Compute;SQLServerPMDemo;sqlserver-0",
        "coordinatorServiceStampId": "00000000-0000-0000-0000-000000000000",
        "coordinatorServiceStampUri": "https://pod01-wbcm1.eus.backup.windowsazure.com",
        "protectionServiceStampId": "00000000-0000-0000-0000-000000000000",
        "protectionServiceStampUri": "https://pod01-prot1j-int.eus.backup.windowsazure.com",
        "rpOriginalSAOption": false,
        "rpIsManagedVirtualMachine": false,
        "bMSActiveRegion": "EastUS"
    },
    "restoreRequest": {
        "objectType": "AzureWorkloadSQLRestoreRequest",
        "shouldUseAlternateTargetLocation": true,
        "isNonRecoverable": false,
        "alternateDirectoryPaths": [],
        "recoveryType": "AlternateLocation",
        "sourceResourceId":"/subscriptions/600000000-0000-0000-0000-000000000000/resourceGroups/SQLServerPMDemo/providers/Microsoft.Compute/virtualMachines/sqlserver-0",
        "targetInfo": {
            "overwriteOption": "FailOnConflict",
          "containerId":"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/RestoreRG/providers/Microsoft.RecoveryServices/vaults/wusRestoreVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;restorerg;wusrestorevm",
          "databaseName": "MSSQLSERVER/msdb_restored_8_1_2021_1758"
        }
      }
  }
```

## <a name="next-steps"></a>다음 단계

Azure Backup REST API에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Recovery Services 공급자 REST API](/rest/api/recoveryservices/)
- [Azure REST API 시작하기](/rest/api/azure/)
