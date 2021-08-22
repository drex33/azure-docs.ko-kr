---
title: REST API를 사용하여 백업 작업 관리
description: 이 문서에서는 REST API를 사용하여 Azure Backup의 백업 및 복원 작업을 추적하고 관리하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b234533e-ac51-4482-9452-d97444f98b38
ms.openlocfilehash: a4f1a0c3701e2b2d46a555f82433642e5a013b24
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438087"
---
# <a name="track-backup-and-restore-jobs-using-rest-api"></a>REST API를 사용하여 백업 및 복원 작업 추적

Azure Backup 서비스는 백업을 트리거하거나, 복원 작업을 수행하거나, 백업을 사용하지 않도록 설정하는 등의 다양한 시나리오에서 백그라운드로 실행되는 작업을 트리거합니다. 이러한 작업은 해당 ID를 사용하여 추적할 수 있습니다.

## <a name="fetch-job-information-from-operations"></a>작업(operation)에서 작업(job) 정보 가져오기

백업 트리거와 같은 작업은 항상 jobID를 반환합니다. 예를 들어 [백업 트리거 REST API 작업](backup-azure-arm-userestapi-backupazurevms.md#example-responses-for-on-demand-backup)의 최종 응답은 다음과 같습니다:

```http
{
  "id": "cd153561-20d3-467a-b911-cc1de47d4763",
  "name": "cd153561-20d3-467a-b911-cc1de47d4763",
  "status": "Succeeded",
  "startTime": "2018-09-12T02:16:56.7399752Z",
  "endTime": "2018-09-12T02:16:56.7399752Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "41f3e94b-ae6b-4a20-b422-65abfcaf03e5"
  }
}
```

Azure VM 백업 작업은 “jobId” 필드로 식별되며 [여기](/rest/api/backup/job-details)에 언급된 대로 간단한 *GET* 요청을 사용하여 추적할 수 있습니다.

## <a name="tracking-the-job"></a>작업 추적

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

`{jobName}`은 위에 언급된 “jobId”입니다. 응답은 항상 작업의 현재 상태를 나타내는 “상태” 필드가 포함된 200 OK입니다. "Completed" 또는 "CompletedWithWarnings"이면 'extendedInfo' 섹션에 해당 작업에 대한 자세한 내용이 표시됩니다.

### <a name="response"></a>응답

|이름  |유형  |설명  |
|---------|---------|---------|
|200 정상     | [JobResource](/rest/api/backup/job-details/get#jobresource)        | 정상        |

#### <a name="example-response"></a>예제 응답

*GET* URI를 제출하면 200(정상) 응답이 반환됩니다.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-client-request-id: ba4dff71-1655-4c1d-a71f-c9869371b18b; ba4dff71-1655-4c1d-a71f-c9869371b18b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14989
x-ms-correlation-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-routing-request-id: SOUTHINDIA:20180521T102317Z:e9702101-9da2-4681-bdf3-a54e17329a56
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:23:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-RecoveryServices-ResourceGroup-centralindia/providers/microsoft.recoveryservices/vaults/abdemovault/backupJobs/7ddead57-bcb9-4269-ac31-6a1b57588700",
  "name": "7ddead57-bcb9-4269-ac31-6a1b57588700",
  "type": "Microsoft.RecoveryServices/vaults/backupJobs",
  "properties": {
    "jobType": "AzureIaaSVMJob",
    "duration": "00:20:23.0896697",
    "actionsInfo": [
      1
    ],
    "virtualMachineVersion": "Compute",
    "extendedInfo": {
      "tasksList": [
        {
          "taskId": "Take Snapshot",
          "duration": "00:00:00",
          "status": "Completed"
        },
        {
          "taskId": "Transfer data to vault",
          "duration": "00:00:00",
          "status": "Completed"
        }
      ],
      "propertyBag": {
        "VM Name": "uttestvmub1",
        "Backup Size": "2332 MB"
      }
    },
    "entityFriendlyName": "uttestvmub1",
    "backupManagementType": "AzureIaasVM",
    "operation": "Backup",
    "status": "Completed",
    "startTime": "2018-05-21T08:35:40.9488967Z",
    "endTime": "2018-05-21T08:56:04.0385664Z",
    "activityId": "7df8e874-1d66-4f81-8e91-da2fe054811d"
  }
}
}

```
