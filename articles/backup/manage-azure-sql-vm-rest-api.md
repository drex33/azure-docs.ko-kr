---
title: REST API 사용하여 Azure VM에서 SQL 서버 데이터베이스 관리
description: REST API 사용하여 Azure Backup 백업되는 Azure VM에서 SQL 서버 데이터베이스를 관리하고 모니터링하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 11/29/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 1f37405727ee22b0dacbe34ffbed4da2940aeefc
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133289599"
---
# <a name="manage-sql-server-databases-in-azure-vms-with-rest-api"></a>REST API 사용하여 Azure VM에서 SQL 서버 데이터베이스 관리

이 문서에서는 Azure Backup 백업되는 SQL 서버 데이터베이스를 관리하고 모니터링하는 방법을 [설명합니다.](backup-overview.md)

## <a name="monitor-jobs"></a>작업 모니터링

Azure Backup 서비스는 백그라운드에서 실행되는 작업을 트리거합니다. 여기에는 백업 트리거, 복원 작업 및 백업 비활성화와 같은 시나리오가 포함됩니다. 해당 ID를 사용하여 이러한 작업을 추적할 수 있습니다.

### <a name="fetch-job-information-from-operations"></a>작업(operation)에서 작업(job) 정보 가져오기

백업 트리거와 같은 작업은 응답으로 *jobID를* 반환합니다.

예를 들어 [백업 트리거 REST API](backup-azure-sql-vm-rest-api.md#trigger-an-on-demand-backup-for-the-database) 작업의 최종 응답은 다음과 같습니다.

```json
{
  "id": "cd2a3b13-d392-4e81-86ac-02ea91cc70b9",
  "name": "cd2a3b13-d392-4e81-86ac-02ea91cc70b9",
  "status": "Succeeded",
  "startTime": "2018-05-28T11:43:21.6516182Z",
  "endTime": "2018-05-28T11:43:21.6516182Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "c22eca5d-0c1c-48a0-a40d-69bef708d92a"
  }
}
```

백업 작업이 **jobId** 필드로 식별되고 GET 요청을 사용하여 [여기에 설명된](/rest/api/backup/jobdetails/) 대로 추적할 수 있습니다.

### <a name="track-the-job"></a>작업 추적

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2016-12-01
```

`{jobName}`는 위에서 언급한 *jobId입니다.* 응답은 작업 상태를 나타내는 **상태** 필드가 있는 200(정상)입니다. *Completed* 또는 *CompletedWithWarnings이면* **extendedInfo** 섹션에 자세한 작업 세부 정보가 표시됩니다.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/Microsoft.RecoveryServices/vaults/SQLServer2012/backupJobs/c22eca5d-0c1c-48a0-a40d-69bef708d92a?api-version=2016-12-01
```

#### <a name="response"></a>응답

이름  | Type  |  Description
--- | --- | ----
200 정상 |  JobResource  | 정상

#### <a name="response-example"></a>응답 예제

*GET* URI를 제출하면 200 응답이 반환됩니다.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: e057b496-8ceb-45b6-bd9e-367f7dd73d6d
x-ms-client-request-id: 1ffda117-b2c0-4a80-a9ba-43ba66eaec9b; 1ffda117-b2c0-4a80-a9ba-43ba66eaec9b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14999
x-ms-correlation-request-id: e057b496-8ceb-45b6-bd9e-367f7dd73d6d
x-ms-routing-request-id: SOUTHINDIA:20180528T115536Z:e057b496-8ceb-45b6-bd9e-367f7dd73d6d
Cache-Control: no-cache
Date: Mon, 28 May 2018 11:55:35 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupJobs/c22eca5d-0c1c-48a0-a40d-69bef708d92a",
  "name": "c22eca5d-0c1c-48a0-a40d-69bef708d92a",
  "type": "Microsoft.RecoveryServices/vaults/backupJobs",
  "properties": {
    "jobType": "AzureWorkloadJob",
    "workloadType": "SQLDataBase",
    "duration": "00:03:13.6439467",
    "actionsInfo": [
      1
    ],
    "errorDetails": [
      {
        "errorCode": 510008,
        "errorString": "Operation cancelled as a conflicting operation was already running on the same database.",
        "errorTitle": "OperationCancelledBecauseConflictingOperationRunningUserError",
        "recommendations": [
          "Please try again after sometime."
        ]
      }
    ],
    "extendedInfo": {
      "tasksList": [
        {
          "taskId": "Transfer data to vault",
          "status": "Failed"
        }
      ],
      "propertyBag": {
        "Data Transferred (in MB)": "0"
      }
    },
    "entityFriendlyName": "MSSQLSERVER/msDB [sqlserver-0.contoso.com]",
    "backupManagementType": "AzureWorkload",
    "operation": "Backup (Full)",
    "status": "Failed",
    "startTime": "2018-05-28T11:43:21.6516182Z",
    "endTime": "2018-05-28T11:46:35.2955649Z",
    "activityId": "6b033cf6-f875-4c03-8985-9add07ec2845"
  }
} 
}
```

## <a name="modify-policy"></a>정책 수정

데이터베이스를 보호하는 정책을 변경하려면 보호를 사용하도록 설정하는 것과 동일한 형식을 사용합니다. 그러나 요청 본문에 새 정책 ID를 제공하고 요청을 제출합니다. 예를 들어 *testVM의* 정책을 *HourlyLogPolicy에서* *ProdPolicy로* 변경하려면 요청 본문에 *ProdPolicy* ID를 제공합니다.

```json
{
  "properties": {
    "backupManagementType": "AzureWorkload",
    "workloadType": "SQLDataBase",
    "policyId": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupPolicies/ProdPolicy"
  },
  "location": "westcentralus"
}
```

응답은 보호를 사용하도록 설정하기 위해 언급된 것과 동일한 형식을 따릅니다.

## <a name="stop-protection-and-retain-existing-data"></a>보호 중지 및 기존 데이터 보존

보호된 데이터베이스에서 보호를 제거하고 이미 백업된 데이터를 유지하려면 백업을 사용하도록 설정하고 요청을 제출하는 데 사용한 요청 본문에서 정책을 제거합니다. 정책과의 연결을 제거하면 백업이 더 이상 트리거되지 않으며 새 복구 지점이 생성되지 않습니다.

```json
{
  "properties": {
    "protectedItemType": "AzureVmWorkloadSQLDatabase",
    "protectionState": "ProtectionStopped",
    "sourceResourceId":
"/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerPMDemo/providers/Microsoft.Compute/virtualMachines/sqlserver-0",
    "policyId": ""
  }
}
```

### <a name="sample-response"></a>샘플 응답

데이터베이스에 대한 보호를 중지하는 것은 비동기 작업입니다. 작업을 수행하면 추적해야 하는 다른 작업이 생성됩니다. 다른 작업을 만들 때 202(수락됨)와 해당 작업이 완료된 경우 200의 두 응답을 반환합니다.

작업이 수락된 경우의 응답 헤더:

```http
Status Code:
OK

Headers:
Pragma                        : no-cache
X-Content-Type-Options        : nosniff
x-ms-request-id               : 388c9359-e237-4644-8f6c-38ae5eb0dfcb
x-ms-client-request-id        : 5ef896e6-d812-431a-ad58-9d9ee1bb8bb3,5ef896e6-d812-431a-ad58-9d9ee1bb8bb3
Strict-Transport-Security     : max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-resource-requests: 107
x-ms-correlation-request-id   : 388c9359-e237-4644-8f6c-38ae5eb0dfcb
x-ms-routing-request-id       : SOUTHINDIA:20211126T054036Z:388c9359-e237-4644-8f6c-38ae5eb0dfcb
Cache-Control                 : no-cache
Date                          : Fri, 26 Nov 2021 05:40:36 GMT
Server                        : Microsoft-IIS/10.0
X-Powered-By                  : ASP.NET

Body:
{
  "id": "9c3521c9-0bc9-4092-96e3-065262eaee11",
  "name": "9c3521c9-0bc9-4092-96e3-065262eaee11",
  "status": "Succeeded",
  "startTime": "2021-11-26T05:36:36.5262731Z",
  "endTime": "2021-11-26T05:36:36.5262731Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "68178d86-d564-460b-9643-829046aac1b1"
  }
}
```

그런 다음 GET *명령으로* 위치 헤더 또는 Azure-AsyncOperation 헤더를 사용하여 결과 작업을 추적합니다.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupoperations/9c3521c9-0bc9-4092-96e3-065262eaee11?api-version=2016-12-01
```

### <a name="response-body"></a>응답 본문

```json
{
  "id": "9c3521c9-0bc9-4092-96e3-065262eaee11",
  "name": "9c3521c9-0bc9-4092-96e3-065262eaee11",
  "status": "Succeeded",
  "startTime": "2021-11-26T05:36:36.5262731Z",
  "endTime": "2021-11-26T05:36:36.5262731Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "68178d86-d564-460b-9643-829046aac1b1"
  }
}
```

## <a name="stop-protection-and-delete-backup-data"></a>보호 중지 및 백업 데이터 삭제

보호된 파일 공유에서 보호를 제거하고 백업 데이터도 삭제하려면 삭제 작업을 수행합니다.

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

*containerName* 및 *protectedItemName* 매개 변수는 [이 문서의](backup-azure-sql-vm-rest-api.md)백업 구성 단계에서 설정한 대로 설정됩니다.

### <a name="responses"></a>응답

보호 삭제는 비동기 작업입니다. 작업을 수행하면 별도로 추적해야 하는 다른 작업이 생성됩니다. 다른 작업을 만들 때 202(수락됨)와 해당 작업이 완료되면 204(NoContent)의 두 응답을 반환합니다.

## <a name="next-steps"></a>다음 단계

- [SQL 서버 데이터베이스에 대한 백업을 구성하는 동안 문제를 해결하는](backup-sql-server-azure-troubleshoot.md)방법을 알아봅니다.
