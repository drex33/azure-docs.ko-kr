---
title: Azure Data Protection REST API를 사용하여 스토리지 계정에서 Blob 복원
description: 이 문서에서는 REST API를 사용하여 스토리지 계정의 Blob을 복원하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 07/09/2021
ms.assetid: 9b8d21e6-3e23-4345-bb2b-e21040996afd
ms.openlocfilehash: 0ba444126026dc77d6e9b963edb6bed3d108c97b
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598747"
---
# <a name="restore-azure-blobs-to-point-in-time-using-azure-data-protection-rest-api"></a>Azure Data Protection REST API를 사용하여 Azure Blob을 특정 시점으로 복원

이 문서에서는 Azure Backup을 사용하여 [Blob](blob-backup-overview.md)을 모든 시점으로 복원하는 방법을 설명합니다.

> [!IMPORTANT]
> Azure Backup을 사용하여 Azure Blob을 복원하기 전에 [중요 사항](blob-restore.md#before-you-start)을 참조하세요.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- Azure Blob을 시점으로 복원

- 복원 작업 상태 추적

## <a name="prerequisites"></a>필수 구성 요소

- [Backup 자격 증명 모음 만들기](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [BLOB 백업 정책 만들기](backup-azure-dataprotection-use-rest-api-create-update-blob-policy.md)

- [BLOB 백업 구성](backup-azure-dataprotection-use-rest-api-backup-blobs.md)

리소스 그룹 _testBkpVaultRG_ 아래에 있는 기존 백업 자격 증명 모음 _TestBkpVault_ 를 참조하세요. 이 예에서 스토리지 계정의 Blob의 이름은 "msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d"입니다.

## <a name="restoring-azure-blobs-within-a-storage-account"></a>스토리지 계정 내에서 Azure Blob 복원

### <a name="fetching-the-valid-time-range-for-restore"></a>복원에 유효한 시간 범위 가져오기

Blob에 대한 작업 백업이 연속적이기 때문에 복원할 고유한 지점이 없습니다. 대신 Blob을 모든 시점으로 복원할 수 있는 유효한 시간 범위를 가져와야 합니다. 이 예제에서는 지난 30일 이내에 복원할 유효한 시간 범위를 확인해 보겠습니다.

복원 가능한 시간 범위는 [복원 가능한 시간 범위 찾기](/rest/api/dataprotection/restorable-time-ranges/find) API를 사용하여 나열할 수 있습니다. 스토리지 계정의 Blob에 대한 연속 백업 범위를 계산하는 작업을 트리거하는 *POST* API입니다.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/findRestorableTimeRanges?api-version=2021-01-01
```

예를 들어 이렇게 하면 다음으로 변환됩니다.

```http
POST https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d/findRestorableTimeRanges?api-version=2021-01-01
```

#### <a name="create-the-request-body-to-fetch-valid-time-ranges-for-restore"></a>복원을 위한 유효한 시간 범위를 가져오는 요청 본문 만들기

유효한 시간 범위를 계산하는 작업을 트리거하기 위해 다음은 요청 본문의 구성 요소입니다.

|**이름**  |**형식**  |**설명**  |
|---------|---------|---------|
|sourceDatastoreType     |   [RestoreSourceDataStoreType](/rest/api/dataprotection/restorable-time-ranges/find#restoresourcedatastoretype)      |    복원할 데이터가 포함된 데이터 저장소     |
|startTime     |    String     |  목록 복원 범위 요청의 시작 시간입니다. ISO 8601 형식.       |
|endTime     |    String     |    목록 복원 범위 요청의 종료 시간입니다. ISO 8601 형식.     |

##### <a name="example-request-body-to-fetch-valid-time-range"></a>유효한 시간 범위를 가져오기 위한 요청 본문의 예

다음 요청 본문은 복원할 수 있는 연속 데이터의 시간 범위를 가져오는 데 필요한 속성을 정의합니다. Blob 백업은 스토리지 계정에 있으므로 데이터 저장소는 '작동 중'입니다. 검색 프로세스를 좁히고 사용 가능한 시간 범위를 반환하도록 하는 시작 및 종료 시간을 제공할 수 있습니다.

```json
{
  "sourceDataStoreType": "OperationalStore",
  "startTime": "",
  "endTime": ""
}
```

#### <a name="responses-for-fetch-valid-time-ranges"></a>유효한 시간 범위 가져오기에 대한 응답

*POST* 요청을 제출하면 이에 대한 응답은 요청의 지정된 시작 및 종료 시간 내에서 복원에 사용할 수 있는 시작 및 종료 시간 범위와 함께 200(정상)입니다.

|**이름**  |**형식**  |**설명**  |
|---------|---------|---------|
|200(OK)     |   [AzureBackupFindRestorableTimeRangesResponseResource](/rest/api/dataprotection/restorable-time-ranges/find#azurebackupfindrestorabletimerangesresponseresource)      |    정상     |
|기타 상태 코드     |    [CloudError](/rest/api/dataprotection/restorable-time-ranges/find#clouderror)     |  작업이 실패한 이유를 설명하는 오류 응답입니다.       |

##### <a name="example-response-for-fetch-valid-time-ranges"></a>유효한 시간 범위 가져오기에 대한 응답 예

```http
HTTP/1.1 200 OK
Content-Length: 379
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: a2b7c2d9-01f5-499a-b521-55da4862c79a
x-ms-routing-request-id: CENTRALUSEUAP:20210708T184646Z:4996a2bf-2df8-48a7-9b53-a552466a27f7
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 18:46:45 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "id": "msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d",
  "type": "Microsoft.DataProtection/backupVaults/backupInstances/findRestorableTimeRanges",
  "properties": {
    "restorableTimeRanges": [
      {
        "startTime": "2021-07-06T18:46:45.947728Z",
        "endTime": "2021-07-08T18:46:45.9932408Z",
        "objectType": "RestorableTimeRange"
      }
    ],
    "objectType": "AzureBackupFindRestorableTimeRangesResponse"
  }
}
```

### <a name="preparing-the-restore-request"></a>복원 요청 준비

동일한 스토리지 계정에 대한 특정 시점이 고정되면 복원할 수 있는 여러 옵션이 있습니다.

#### <a name="restoring-all-the-blobs-to-a-point-in-time"></a>모든 Blob을 시점으로 복원

이 옵션을 사용하면 선택한 시점으로 다시 롤백하여 스토리지 계정의 모든 블록 Blob이 복원됩니다. 많은 양의 데이터를 포함하거나 높은 변동이 발생하는 스토리지 계정은 복원하는 데 시간이 더 오래 걸릴 수 있습니다.

##### <a name="constructing-the-request-body-for-point-in-time-restore-of-all-blobs"></a>모든 Blob의 특정 시점 복원을 위한 요청 본문 구성

이 시나리오에서 기억해야 할 핵심 사항은 다음과 같습니다.

- 동일한 스토리지 계정에서 복원이 진행 중입니다. 이는 복원 대상 개체가 데이터 원본과 동일함을 의미합니다. 이는 아래의 복원 대상 정보 섹션에 반영됩니다.
- 이는 연속 백업이므로 복원 시간은 특정 복구 지점이 아니라 특정 시점입니다.
- 모든 Blob이 복원됩니다.
- 백업이 있는 원본 데이터 저장소는 동일한 스토리지 계정입니다. 따라서 원본 데이터 저장소는 '운영' 데이터 저장소입니다.

```json
{
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryTimeBasedRestoreRequest",
    "restoreTargetInfo": {
      "objectType": "RestoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "restoreLocation": "westus",
      "datasourceInfo": {
        "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
        "resourceLocation": "westus",
        "resourceName": "msblobbackup",
        "resourceType": "Microsoft.Storage/storageAccounts",
        "resourceUri": ""
      }
    },
    "sourceDataStoreType": "OperationalStore",
    "recoveryPointTime": "2021-07-08T00:00:00.0000000Z"
  }
}
```

#### <a name="restoring-few-containers-to-a-point-in-time"></a>일부 컨테이너를 특정 시점으로 복원

이 옵션을 사용하면 접두사 일치를 사용하여 Blob의 하위 집합을 복원하거나 복원할 최대 10개의 컨테이너를 선택할 수 있습니다. 지정된 시점에 해당 Blob을 이전 상태로 되돌리기 위해 단일 컨테이너 내에서 또는 여러 컨테이너에서 사전순으로 최대 10개의 Blob 범위를 지정할 수 있습니다. 접두사를 사용하는 경우 염두에 두어야 할 몇 가지 사항이 있습니다.

- 슬래시(/)를 사용하여 Blob 접두사에서 컨테이너 이름을 구분할 수 있습니다.
- 지정된 범위의 시작은 포함되지만 지정된 범위는 제외됩니다.

접두사를 사용하여 Blob 범위를 복원하는 방법에 대해 [자세히 알아보세요](blob-restore.md#use-prefix-match-for-restoring-blobs).

##### <a name="constructing-the-request-body-for-point-in-time-restore-of-selected-containers-or-few-blobs"></a>선택한 컨테이너 또는 몇 개 Blob의 특정 시점 복원을 위한 요청 본문 구성

이 시나리오에서 기억해야 할 핵심 사항은 다음과 같습니다.

- 동일한 스토리지 계정에서 복원이 진행 중입니다. 이는 복원 대상 개체가 데이터 원본과 동일함을 의미합니다. 이는 아래의 복원 대상 정보 섹션에 반영됩니다.
- 이는 연속 백업이므로 복원 시간은 특정 복구 지점이 아니라 특정 시점입니다.
- 스토리지 계정 내의 몇 가지 항목이 복원됩니다. 접두사 패턴이 있는 컨테이너 또는 Blob일 수 있습니다.
- 백업이 있는 원본 데이터 저장소는 동일한 스토리지 계정입니다. 따라서 원본 데이터 저장소는 '운영' 데이터 저장소입니다.

```json
{
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryTimeBasedRestoreRequest",
    "restoreTargetInfo": {
      "objectType": "ItemLevelRestoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "restoreLocation": "westus",
      "restoreCriteria": [
        {
          "objectType": "RangeBasedItemLevelRestoreCriteria",
          "minMatchingValue": "Container1",
          "maxMatchingValue": "Container10-0"
        }
      ],
      "datasourceInfo": {
        "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
        "resourceLocation": "westus",
        "resourceName": "msblobbackup",
        "resourceType": "Microsoft.Storage/storageAccounts",
        "resourceUri": ""
      }
    },
    "sourceDataStoreType": "OperationalStore",
    "recoveryPointTime": "2021-07-08T00:00:00.0000000Z"
  }
}
```

#### <a name="validating-restore-requests"></a>복원 요청 검증

요청 본문이 준비되면 [복원 API 유효성 검사](/rest/api/dataprotection/backup-instances/validate-for-restore)를 사용하여 유효성을 검사할 수 있습니다. 백업 API의 유효성 검사와 마찬가지로 이것은 *POST* 작업입니다.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/validateRestore?api-version=2021-01-01
```

예를 들어 이렇게 하면 다음으로 변환됩니다.

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d/validateRestore?api-version=2021-01-01"
```

이 POST API에 대한 요청 본문은 [여기](/rest/api/dataprotection/backup-instances/validate-for-restore#request-body)에 자세히 설명되어 있습니다. [모든 Blob 복원](#constructing-the-request-body-for-point-in-time-restore-of-all-blobs) 및 [일부 항목 복원](#constructing-the-request-body-for-point-in-time-restore-of-selected-containers-or-few-blobs) 시나리오에 대해 위 섹션과 동일하게 구성했습니다. 유효성 검사 작업을 트리거하는 데 동일한 절차를 사용합니다.

##### <a name="response-to-validate-restore-requests"></a>복원 요청 유효성 검사에 대한 응답

유효성 검사 복원 요청은 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

이 작업은 다른 작업을 만드는 경우 202(수락됨) 및 해당 작업이 완료되는 경우 200(정상)의 두 응답을 반환합니다.

|속성  |유형  |설명  |
|---------|---------|---------|
|200 정상     |         |  유효성 검사 요청 상태       |
|202 수락됨     |         |     수락됨    |

###### <a name="example-response-to-restore-validate-request"></a>유효성 검사 요청을 복원하기 위한 응답 예

*POST* 작업이 제출되면 초기 응답은 Azure-asyncOperation 헤더와 함께 202 수락됨이 됩니다.

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 10
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: bae60c92-669d-45a4-aed9-8392cca7cc8d
x-ms-routing-request-id: CENTRALUSEUAP:20210708T205935Z:f51db7a4-9826-4084-aa3b-ae640dc78af6
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 20:59:35 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

간단한 *GET* 요청으로 Azure-AsyncOperation 헤더를 추적합니다. 요청이 성공하면 성공 상태 응답과 함께 200 성공을 반환합니다.

```http
 GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==",
  "status": "Succeeded",
  "startTime": "2021-07-08T20:59:35.0060264Z",
  "endTime": "2021-07-08T20:59:57Z"
}
```

#### <a name="triggering-restore-requests"></a>복원 요청 트리거

트리거하는 복원 작업은 ***POST*** API입니다. 트리거 복원 작업에 대한 모든 세부 정보는 [여기](/rest/api/dataprotection/backup-instances/trigger-restore)에 설명되어 있습니다.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/restore?api-version=2021-01-01
```

예를 들어 이렇게 하면 다음으로 변환됩니다.

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d/restore?api-version=2021-01-01"
```

##### <a name="creating-a-request-body-for-restore-operations"></a>복원 작업을 위한 요청 본문 만들기

요청이 검증되면 동일한 요청 본문을 사용하여 사소한 변경으로 복원 요청을 트리거할 수 있습니다.

###### <a name="example-request-body-for-all-blobs-restore"></a>모든 Blob 복원에 대한 요청 본문 예

유효성 검사 복원 요청 본문의 유일한 변경 사항은 시작 시 "restoreRequest" 개체를 제거하는 것입니다.

```json
{
  "objectType": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "restoreTargetInfo": {
    "objectType": "RestoreTargetInfo",
    "recoveryOption": "FailIfExists",
    "restoreLocation": "westus",
    "datasourceInfo": {
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceLocation": "westus",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceUri": ""
    }
  },
  "sourceDataStoreType": "OperationalStore",
  "recoveryPointTime": "2021-07-08T00:00:00Z"
}
```

###### <a name="example-request-body-for-items-or-few-blobs-restore"></a>항목 또는 몇 개의 Blob 복원에 대한 요청 본문 예

유효성 검사 복원 요청 본문의 유일한 변경 사항은 시작 시 "restoreRequest" 개체를 제거하는 것입니다.

```json
{
  "objectType": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "restoreTargetInfo": {
    "objectType": "ItemLevelRestoreTargetInfo",
    "recoveryOption": "FailIfExists",
    "restoreLocation": "westus",
    "restoreCriteria": [
      {
        "objectType": "RangeBasedItemLevelRestoreCriteria",
        "minMatchingValue": "Container1",
        "maxMatchingValue": "Container2"
      }
    ],
    "datasourceInfo": {
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceLocation": "westus",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceUri": ""
    }
  },
  "sourceDataStoreType": "OperationalStore",
  "recoveryPointTime": "2021-07-08T00:00:00.0000000Z"
}
```

#### <a name="response-to-trigger-restore-requests"></a>복원 요청 트리거에 대한 응답

트리거 복원 요청은 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

이 작업은 다른 작업을 만드는 경우 202(수락됨) 및 해당 작업이 완료되는 경우 200(정상)의 두 응답을 반환합니다.

|속성  |유형  |설명  |
|---------|---------|---------|
|200 정상     |         |  복원 요청 상태       |
|202 수락됨     |         |     수락됨    |

##### <a name="example-response-to-trigger-restore-request"></a>복원 요청을 트리거하는 응답의 예

*POST* 작업이 제출되면 초기 응답은 Azure-asyncOperation 헤더와 함께 202 수락됨이 됩니다.

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 30
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1197
x-ms-correlation-request-id: 8661209c-5b6a-44fe-b676-4e2b9c296593
x-ms-routing-request-id: CENTRALUSEUAP:20210708T204652Z:69e3fa4b-c5d9-4601-9410-598006ada187
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 20:46:52 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

간단한 *GET* 요청으로 Azure-AsyncOperation 헤더를 추적합니다. 요청이 성공하면 복원 요청 완료를 위해 추가로 추적해야 하는 작업 ID와 함께 200 성공을 반환합니다.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==",
  "status": "Succeeded",
  "startTime": "2021-07-08T20:46:52.4110868Z",
  "endTime": "2021-07-08T20:46:56Z",
  "properties": {
    "jobId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852",
    "objectType": "OperationJobExtendedInfo"
  }
}
```

#### <a name="tracking-jobs"></a>작업 추적

트리거 복원 요청은 복원 작업을 트리거했으며 결과 작업 ID는 [GET Jobs API](/rest/api/dataprotection/jobs/get)를 사용하여 추적해야 합니다.

간단한 GET 명령을 사용하여 위의 [트리거 복원 응답](#example-response-to-trigger-restore-request)에 지정된 JobId를 추적합니다.

```http
 GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852?api-version=2021-01-01

{
  "properties": {
    "activityID": "4195ca6c-e02d-11eb-b0b1-70bc105e2242",
    "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx",
    "backupInstanceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d",
    "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/BlobBackup-Policy",
    "dataSourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
    "vaultName": "BV-JPE-GRS",
    "backupInstanceFriendlyName": "msblobbackup",
    "policyName": "BlobBackup-Policy",
    "sourceResourceGroup": "RG-BlobBackup",
    "dataSourceName": "msblobbackup",
    "progressEnabled": false,
    "etag": "W/\"datetime'2021-07-08T20%3A48%3A36.6999667Z'\"",
    "sourceSubscriptionID": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx",
    "dataSourceLocation": "westus",
    "startTime": "2021-07-08T20:44:19.5467125Z",
    "endTime": "2021-07-08T20:48:35.8297312Z",
    "dataSourceType": "Microsoft.Storage/storageAccounts/blobServices",
    "operationCategory": "Restore",
    "operation": "Restore",
    "status": "Completed",
    "isUserTriggered": true,
    "supportedActions": [
      ""
    ],
    "duration": "PT4M16.2830187S",
    "extendedInfo": {
      "sourceRecoverPoint": {
        "recoveryPointTime": "2021-07-08T00:00:00Z"
      },
      "recoveryDestination": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "subTasks": [
        {
          "taskId": 1,
          "taskName": "Trigger Restore",
          "taskStatus": "Completed"
        }
      ]
    }
  },
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852",
  "name": "c4bd49a1-0645-4eec-b207-feb818962852",
  "type": "Microsoft.DataProtection/BackupVaults/backupJobs"
}
```

위의 작업 상태는 복원 작업이 완료되었고 모든 Blob이 지정된 시점으로 복구되었음을 나타냅니다.

## <a name="next-steps"></a>다음 단계

[Azure Blob 백업 개요](blob-backup-overview.md).

Azure Backup REST API에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Data Protection 공급자 REST API](/rest/api/dataprotection/)
- [Azure REST API 시작하기](/rest/api/azure/)