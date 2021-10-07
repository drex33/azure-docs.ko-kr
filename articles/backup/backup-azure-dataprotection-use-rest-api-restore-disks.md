---
title: Azure Data Protection REST API 사용하여 Azure 디스크 복원
description: 이 문서에서는 Azure 데이터 보호 REST API 사용하여 Azure 디스크를 복원하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 10/06/2021
ms.assetid: 30f4e7ff-2a55-4a85-be44-55feedc24607
ms.openlocfilehash: d8898b407fdcbea154d9282ff7964a3d0fa0264c
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129622416"
---
# <a name="restore-azure-disks-using-azure-data-protection-rest-api"></a>Azure Data Protection REST API 사용하여 Azure 디스크 복원

이 문서에서는 Azure Backup 사용하여 [디스크를 복원하는 방법을 설명합니다.](disk-backup-overview.md)

>[!Note]
>- 현재 백업이 수행된 기존 원본 디스크를 교체하여 복원하는 OLR(Original-Location Recovery) 옵션은 지원되지 않습니다.
>- 복구 지점에서 복원하여 원본 디스크와 동일한 리소스 그룹 또는 다른 리소스 그룹에 새 디스크를 만들 수 있습니다. 이를 ALR(대체 위치 복구)이라고 합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- 새 디스크를 만드는 복원

- 복원 작업 상태 추적

## <a name="prerequisites"></a>필수 구성 요소

- [Backup 자격 증명 모음 만들기](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [디스크 백업 정책 만들기](backup-azure-dataprotection-use-rest-api-create-update-disk-policy.md)

- [디스크 백업 구성](backup-azure-dataprotection-use-rest-api-backup-disks.md)

이 예제에서는 리소스 그룹 _testBkpVaultRG_ 아래에 있는 기존 백업 자격 증명 모음 _TestBkpVault를_ 참조합니다. 여기서 Azure 디스크 이름은 _msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed_ 입니다.

## <a name="restore-to-create-a-new-azure-disk"></a>복원하여 새 Azure 디스크 만들기

### <a name="set-up-permissions"></a>권한 설정

백업 자격 증명 모음은 관리 ID를 사용하여 다른 Azure 리소스에 액세스합니다. 백업에서 복원하려면 백업 자격 증명 모음의 관리 ID에 디스크를 복원해야 하는 리소스 그룹에 대한 권한 집합이 필요합니다.

백업 자격 증명 모음에는 시스템 할당 관리 ID가 사용됩니다. 이 ID는 리소스당 하나로 제한되며 이 리소스의 수명 주기에 연결됩니다. 관리 ID에 권한을 부여하려면 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용합니다. 관리 ID는 Azure 리소스에서만 사용할 수 있는 특정 서비스 주체입니다. [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아보세요.

디스크를 복원/만들 대상 리소스 그룹에 자격 증명 모음의 시스템 할당 관리 ID에 대한 관련 권한을 할당합니다. [자세히 알아보기](restore-managed-disks.md#restore-to-create-a-new-disk).

### <a name="fetch-the-list-of-recovery-points"></a>복구 지점 목록 가져오기

백업 인스턴스에 사용 가능한 모든 복구 지점을 나열하려면 [복구 지점 목록](/rest/api/dataprotection/recovery-points/list) API를 사용합니다.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/recoveryPoints?api-version=2021-01-01
```

예를 들어 이 API는 다음으로 변환됩니다.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/recoveryPoints?api-version=2021-01-01
```

#### <a name="responses-for-list-of-recovery-points"></a>복구 지점 목록에 대한 응답

*GET* 요청을 제출하면 응답이 200(정상)으로 반환되고 모든 관련 세부 정보가 포함된 모든 불연속 복구 지점 목록이 반환됩니다.

|Name  |Type  |Description  |
|---------|---------|---------|
|200 정상     |    [AzureBackupRecoveryPointResourceList](/rest/api/dataprotection/recovery-points/list#azurebackuprecoverypointresourcelist)     |   정상      |
|기타 상태 코드     |    [CloudError](/rest/api/dataprotection/recovery-points/list#clouderror)     |     오류 응답은 작업 실패의 이유를 설명합니다.    |

##### <a name="example-response-for-list-of-recovery-points"></a>복구 지점 목록에 대한 응답 예제

```http
HTTP/1.1 200 OK
Content-Length: 7550
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 11999
x-ms-correlation-request-id: f01e2448-bdc5-4971-aee4-2edd1945c719
x-ms-routing-request-id: CENTRALUSEUAP:20210830T173435Z:0063423e-8b5e-493e-bb2e-74b7c7947c6c
Cache-Control: no-cache
Date: Mon, 30 Aug 2021 17:34:34 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/recoveryPoints/a3d02fc3ab8a4c3a8cc26688c26d3356",
      "name": "a3d02fc3ab8a4c3a8cc26688c26d3356",
      "type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints",
      "properties": {
        "objectType": "AzureBackupDiscreteRecoveryPoint",
        "recoveryPointId": "a3d02fc3ab8a4c3a8cc26688c26d3356",
        "recoveryPointTime": "2021-08-30T10:02:11.6354913Z",
        "recoveryPointType": "Incremental",
        "friendlyName": "119ac243-a789-4a41-be24-0461bceb3888",
        "recoveryPointDataStoresDetails": [
          {
            "id": "13e7c1fe-005d-4b80-8532-c58d937132bb",
            "type": "OperationalStore",
            "creationTime": "2021-08-30T10:02:11.6354913Z",
            "expiryTime": "2021-09-06T10:02:11.6354913Z",
            "metaData": null,
            "visible": true,
            "state": "COMMITTED",
            "rehydrationExpiryTime": null,
            "rehydrationStatus": null
          }
        ],
        "retentionTagName": "Default",
        "retentionTagVersion": "637607428336647408",
        "policyName": "DiskBackupPolicy-03",
        "policyVersion": null
      }
    },
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/recoveryPoints/8f76ebc4c54847c09455d5785a150ce2",
      "name": "8f76ebc4c54847c09455d5785a150ce2",
      "type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints",
      "properties": {
        "objectType": "AzureBackupDiscreteRecoveryPoint",
        "recoveryPointId": "8f76ebc4c54847c09455d5785a150ce2",
        "recoveryPointTime": "2021-08-29T10:01:50.7749008Z",
        "recoveryPointType": "Incremental",
        "friendlyName": "1ac5aa6b-3583-464f-9604-7490c04c2b22",
        "recoveryPointDataStoresDetails": [
          {
            "id": "13e7c1fe-005d-4b80-8532-c58d937132bb",
            "type": "OperationalStore",
            "creationTime": "2021-08-29T10:01:50.7749008Z",
            "expiryTime": "2021-09-05T10:01:50.7749008Z",
            "metaData": null,
            "visible": true,
            "state": "COMMITTED",
            "rehydrationExpiryTime": null,
            "rehydrationStatus": null
          }
        ],
        "retentionTagName": "Default",
        "retentionTagVersion": "637607428336647408",
        "policyName": "DiskBackupPolicy-03",
        "policyVersion": null
      }
    },
    .
    .
    .
    .
}
```

위 목록에서 관련 복구 지점을 선택하고 복원 요청을 준비합니다. 복원할 위의 목록에서 _a3d02fc3ab8a4c3a8cc26688c26d3356이라는_ 복구 지점을 선택합니다.

### <a name="prepare-the-restore-request"></a>복원 요청 준비

대상 리소스 [그룹(위에서](#set-up-permissions)자세히 설명한 대로 권한이 할당됨)과 필요한 디스크 이름을 사용하여 만들 새 디스크의 ARM(Azure Resource Manager) ID를 생성합니다.

예를 들어 백업된 디스크와 동일한 지역에 있지만 다른 구독에 있는 리소스 그룹 _targetrg_ 아래에 _APITestDisk2라는_ 디스크를 사용합니다.

#### <a name="construct-the-request-body-for-restore-request"></a>복원 요청에 대한 요청 본문 생성

다음 요청 본문에는 복구 지점 ID 및 복원 대상 세부 정보가 포함됩니다.

```json
{
  "recoveryPointId": "a3d02fc3ab8a4c3a8cc26688c26d3356",
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "OperationalStore",
    "restoreTargetInfo": {
      "objectType": "restoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "dataSourceInfo": {
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
        "resourceName": "APITestDisk2",
        "resourceType": "Microsoft.Compute/disks",
        "resourceLocation": "westUS",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
        "datasourceType": "Microsoft.Compute/disks"
      },
      "restoreLocation": "westUS"
    }
  }
}
```

#### <a name="validate-restore-requests"></a>복원 요청 유효성 검사

요청 본문이 준비되면 복원 [API](/rest/api/dataprotection/backup-instances/validate-for-restore)에 대한 유효성 검사를 사용하여 유효성을 검사합니다. 백업 API에 대한 유효성 검사와 마찬가지로 *POST* 작업입니다.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/validateRestore?api-version=2021-01-01
```

예를 들어 이 API는 다음으로 변환됩니다.

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/validateRestore?api-version=2021-01-01"
```

이 POST API의 요청 본문에 대해 [자세히 알아보세요.](/rest/api/dataprotection/backup-instances/validate-for-restore#request-body) 

##### <a name="request-body-to-validate-restore-request"></a>복원 요청의 유효성을 검사하기 위한 요청 본문

위의 섹션에서 동일한 의 [섹션을](#construct-the-request-body-for-restore-request)생성했습니다. 이제 개체 형식을 추가하고 이를 사용하여 유효성 검사 작업을 트리거합니다.

```json

{
  "objectType": "ValidateRestoreRequestObject",
  "recoveryPointId": "a3d02fc3ab8a4c3a8cc26688c26d3356",
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "OperationalStore",
    "restoreTargetInfo": {
      "objectType": "restoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "dataSourceInfo": {
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
        "resourceName": "APITestDisk2",
        "resourceType": "Microsoft.Compute/disks",
        "resourceLocation": "westUS",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
        "datasourceType": "Microsoft.Compute/disks"
      },
      "restoreLocation": "westUS"
    }
  }
}
```

##### <a name="response-to-validate-restore-requests"></a>복원 요청 유효성 검사에 대한 응답

_유효성 검사 복원 요청은_ [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 따라서 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

다른 작업이 생성될 때 202(수락됨), 해당 작업이 완료될 때 200(정상)인 두 개의 응답이 반환됩니다.

|이름  |Type  |Description  |
|---------|---------|---------|
|200 정상     |         |  유효성 검사 요청 상태       |
|202 수락됨     |         |     수락됨    |

###### <a name="example-response-to-restore-validate-request"></a>유효성 검사 요청을 복원하기 위한 응답 예

*POST* 작업이 제출되면 _Azure-asyncOperation_ 헤더를 사용하여 초기 응답을 202(수락됨)로 반환합니다.

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

간단한 GET 요청으로 _Azure-AsyncOperation_ 헤더를 *추적합니다.* 요청이 성공하면 성공 상태 응답과 함께 200(정상)을 반환합니다.

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

#### <a name="trigger-restore-requests"></a>복원 요청 트리거

트리거 복원 작업은 ***POST*** API입니다. 트리거 복원 작업에 대해 [자세히 알아봅니다.](/rest/api/dataprotection/backup-instances/trigger-restore)

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/restore?api-version=2021-01-01
```

예를 들어 API는 다음으로 변환됩니다.

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/restore?api-version=2021-01-01"
```

##### <a name="create-a-request-body-for-restore-operations"></a>복원 작업에 대한 요청 본문 만들기

요청의 유효성이 검사되면 동일한 요청 본문을 사용하여 사소한 변경으로 _복원 요청을_ 트리거합니다.

###### <a name="example-request-body-for-restore"></a>복원에 대한 요청 본문 예제

유효성 검사 복원 요청 본문에서 유일한 변경 내용은 시작 시 _restoreRequest_ 개체를 제거하고 개체 유형을 변경하는 것입니다.

```json
{
  "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
  "recoveryPointId": "a3d02fc3ab8a4c3a8cc26688c26d3356",
  "sourceDataStoreType": "OperationalStore",
  "restoreTargetInfo": {
    "datasourceInfo": {
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
      "datasourceType": "Microsoft.Compute/disks",
      "resourceName": "APITestDisk2",
      "resourceType": "Microsoft.Compute/disks",
      "resourceLocation": "westUS",
      "objectType": "Datasource"
    },
    "restoreLocation": "westUS",
    "recoveryOption": "FailIfExists",
    "objectType": "RestoreTargetInfo"
  }
}
```

#### <a name="response-to-trigger-restore-requests"></a>복원 요청 트리거에 대한 응답

_트리거 복원 요청은_ [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 따라서 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

다른 작업이 생성될 때 202(수락됨), 해당 작업이 완료될 때 200(정상)인 두 개의 응답이 반환됩니다.

|이름  |Type  |Description  |
|---------|---------|---------|
|200 정상     |         |  복원 요청 상태       |
|202 수락됨     |         |     수락됨    |

##### <a name="example-response-to-trigger-restore-request"></a>복원 요청을 트리거하는 응답의 예

*POST* 작업이 제출되면 _Azure-asyncOperation_ 헤더를 사용하여 초기 응답을 202(수락됨)로 반환합니다.

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

간단한 GET 요청으로 _Azure-AsyncOperation_ 헤더를 *추적합니다.* 요청이 성공하면 복원 요청 완료를 위해 추가로 추적해야 하는 작업 ID가 있는 200(정상)을 반환합니다.

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

#### <a name="track-jobs"></a>작업 추적

_트리거 복원 요청이_ 복원 작업을 트리거했습니다. 결과 작업 ID를 추적하려면 [GET Jobs API](/rest/api/dataprotection/jobs/get)를 사용합니다.

간단한 GET 명령을 사용하여 위의 [트리거 복원 응답에](#example-response-to-trigger-restore-request) 있는 _JobId를_ 추적합니다.

```http
 GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852?api-version=2021-01-01

{
  "properties": {
    "activityID": "2881cc22-f527-4af4-9b34-46c6c7b72076-Ibz",
    "subscriptionId": "62b829ee-7936-40c9-a1c9-47a93f9f3965",
    "backupInstanceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed",
    "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/DiskBackup-Policy",
    "dataSourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup",
    "vaultName": "testBkpVault",
    "backupInstanceFriendlyName": "msdiskbackup",
    "policyName": "DiskBackup-Policy",
    "sourceResourceGroup": "RG-DiskBackup",
    "dataSourceSetName": null,
    "dataSourceName": "msdiskbackup",
    "sourceDataStoreName": null,
    "destinationDataStoreName": null,
    "progressEnabled": false,
    "etag": "W/\"datetime'2021-08-26T07%3A18%3A16.157629Z'\"",
    "sourceSubscriptionID": "62b829ee-7936-40c9-a1c9-47a93f9f3965",
    "dataSourceLocation": "westUS",
    "startTime": "2021-08-26T07:12:09.940517Z",
    "endTime": "2021-08-26T07:18:15.6815066Z",
    "dataSourceType": "Microsoft.Compute/disks",
    "operationCategory": "Restore",
    "operation": "Restore",
    "status": "Completed",
    "restoreType": null,
    "isUserTriggered": true,
    "rehydrationPriority": null,
    "supportedActions": [
      ""
    ],
    "duration": "PT6M5.7409896S",
    "progressUrl": null,
    "errorDetails": null,
    "extendedInfo": {
      "backupInstanceState": null,
      "dataTransferedInBytes": null,
      "targetRecoverPoint": null,
      "sourceRecoverPoint": {
        "recoveryPointID": "3a512290ec6b43d6b9a644869f4a3b38",
        "recoveryPointTime": "2021-08-25T09:03:11.6889015Z"
      },
      "recoveryDestination": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
      "subTasks": [
        {
          "taskId": 1,
          "taskName": "Trigger Restore",
          "taskStatus": "Completed",
          "taskProgress": null,
          "additionalDetails": null
        }
      ],
      "additionalDetails": null
    }
  },
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/3bc62c80-913f-47fa-b829-b7df476682be",
  "name": "3bc62c80-913f-47fa-b829-b7df476682be",
  "type": "Microsoft.DataProtection/BackupVaults/backupJobs"
}
```

위의 작업 상태는 복원 작업이 완료되고 디스크가 지정된 구독 및 대상 리소스 그룹으로 복구되었음을 나타냅니다.

## <a name="next-steps"></a>다음 단계

[Azure 디스크 백업 개요](disk-backup-overview.md)

Azure Backup REST Api에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [Azure Data Protection 공급자 REST API](/rest/api/dataprotection/)
- [Azure REST API 시작하기](/rest/api/azure/)