---
title: Azure Data Protection REST API를 사용하여 스토리지 계정에서 Blob 백업
description: 이 문서에서는 REST API를 사용하여 Blob의 백업 작업을 구성, 시작, 관리하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 07/09/2021
ms.assetid: 7c244b94-d736-40a8-b94d-c72077080bbe
ms.openlocfilehash: 709579f814dde3e1972888b0edea18069334b6dd
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598758"
---
# <a name="back-up-blobs-in-a-storage-account-using-azure-data-protection-via-rest-api"></a>Azure Data Protection REST API를 통해 스토리지 계정에서 Blob 백업

이 문서에서는 REST API를 통해 스토리지 계정에서 Blob의 백업을 관리하는 방법을 설명합니다. Blob 백업은 스토리지 계정 수준에서 구성됩니다. 따라서 스토리지 계정의 모든 Blob은 운영 백업으로 보호됩니다.

Azure Blob 지역 가용성, 지원되는 시나리오 및 제한 사항에 관한 자세한 내용은 [지원 매트릭스](blob-backup-support-matrix.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- [Backup 자격 증명 모음 만들기](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [Blob 백업 정책 만들기](backup-azure-dataprotection-use-rest-api-create-update-blob-policy.md)

## <a name="configure-backup"></a>백업 구성

자격 증명 모음과 정책이 생성된 후 사용자가 스토리지 계정 내 모든 Azure Blob을 보호하기 위해 고려해야 할 두 가지 중요한 사항이 있습니다.

### <a name="key-entities-involved"></a>관련된 주요 엔터티

#### <a name="storage-account-which-contains-the-blobs-to-be-protected"></a>보호할 Blob이 포함된 스토리지 계정

보호할 Blob이 포함된 스토리지 계정의 Azure Resource Manager ID를 페치합니다. 이 ID는 스토리지 계정의 식별자로 사용됩니다. 다른 구독과 미국 서부의 리소스 그룹 _RG-BlobBackup_ 아래에서 _msblobbackup_ 이라는 스토리지 계정의 예제를 사용합니다.

```http
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup"
```

#### <a name="backup-vault"></a>Backup 자격 증명 모음

스토리지 계정 내에 있는 Blob에서 백업을 사용하려면 백업 자격 증명 모음에는 스토리지 계정에 대한 권한이 있어야 합니다. 자격 증명 모음의 시스템이 할당한 관리 ID는 해당 권한을 할당하는 데 사용됩니다. “TestBkpVaultRG” 리소스 그룹 아래 “미국 서부” 지역에서 “testBkpVault”라는 백업 자격 증명 모음의 예제를 사용합니다.

### <a name="assign-permissions"></a>권한 할당

RBAC를 통해 자격 증명 모음(자격 증명 모음 MSI로 표시) 및 관련 스토리지 계정에 몇 가지 권한을 할당해야 합니다. 이 작업은 포털, PowerShell 또는 REST API를 통해 수행할 수 있습니다. 모든 [관련 권한](blob-backup-configure-manage.md#grant-permissions-to-the-backup-vault-on-storage-accounts)에 관해 자세히 알아봅니다.

### <a name="prepare-the-request-to-configure-backup"></a>백업을 구성하기 위한 요청 준비

관련 권한이 자격 증명 모음과 스토리지 계정으로 설정되고 자격 증명 모음과 정책이 구성되면 백업을 구성하기 위한 요청을 준비할 수 있습니다. 다음은 스토리지 계정 내의 모든 Blob에 대해 백업을 구성하기 위한 요청 본문입니다. 스토리지 계정의 ARM ID(Azure Resource Manager ID)와 해당 세부 정보는 ‘datasourceinfo’ 섹션에 언급되고 정책 정보는 ‘policyinfo’ 섹션에 있습니다.

```json
{
  "backupInstance": {
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceLocation": "westUS",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceUri": ""
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/BlobBackup-Policy"
    },
    "objectType": "BackupInstance"
  }
}
```

### <a name="validate-the-request-to-configure-backup"></a>백업을 구성하기 위한 요청의 유효성 검사

백업을 구성하기 위한 요청이 실패하거나 [백업 API의 유효성 검사](/rest/api/dataprotection/backup-instances/validate-for-backup)를 사용하지 않는지 유효성을 검사할 수 있습니다. 고객은 응답을 사용하여 필요한 모든 필수 조건을 수행한 다음, 백업 요청에 대한 구성을 제출할 수 있습니다.

백업 요청의 유효성 검사는 POST 작업이며 URI에는 `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}` 매개 변수가 있습니다.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.DataProtection/backupVaults/{backupVaultName}/validateForBackup?api-version=2021-01-01
```

예를 들어, 이렇게 하면 다음으로 변환됩니다.

```http
POST https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/validateForBackup?api-version=2021-01-01
```

앞에서 준비한 [요청 본문](#prepare-the-request-to-configure-backup)은 보호할 스토리지 계정의 세부 정보를 제공하는 데 사용됩니다.

#### <a name="example-request-body"></a>요청 본문 예제

```json
{
  "backupInstance": {
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceLocation": "westUS",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceUri": ""
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/BlobBackup-Policy"
    },
    "objectType": "BackupInstance"
  }
}
```

#### <a name="responses-for-validate-backup-request"></a>백업 요청 유효성 검사의 응답

백업 요청의 유효성 검사는 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

이 작업은 다른 작업을 만드는 경우 202(수락됨) 및 해당 작업이 완료되는 경우 200(정상)의 두 응답을 반환합니다.

|속성  |유형  |Description  |
|---------|---------|---------|
|202 수락됨     |         |  작업이 비동기적으로 완료됩니다.      |
|200 정상     |   [OperationJobExtendedInfo](/rest/api/dataprotection/backup-instances/validate-for-backup#operationjobextendedinfo)      |     수락됨    |
| 기타 상태 코드 |    [CloudError](/rest/api/dataprotection/backup-instances/validate-for-backup#clouderror)    |    작업이 실패한 이유를 설명하는 오류 응답    |

##### <a name="example-responses-for-validate-backup-request"></a>백업 요청 유효성 검사의 예제 응답

###### <a name="error-response"></a>오류 응답

지정된 스토리지 계정이 이미 보호된 경우 응답은 HTTP 400(잘못된 요청)이며 지정된 스토리지 계정이 세부 정보와 함께 백업 자격 증명 모음에 대해 보호된다는 것을 분명히 명시합니다.

```http
HTTP/1.1 400 BadRequest
Content-Length: 999
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: f36eb67a-8932-42a8-8aba-c5ee2443aa2e
x-ms-routing-request-id: WESTUS:20210707T124745Z:bcd23af5-fa17-4cd0-9929-a55f141e33ce
Cache-Control: no-cache
Date: Wed, 07 Jul 2021 12:47:45 GMT
X-Powered-By: ASP.NET

{
  "error": {
    "additionalInfo": [
      {
        "type": "UserFacingError",
        "info": {
          "message": "Datasource is already protected under the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault.",
          "recommendedAction": [
            "Delete the backup instance msblobbackuptemp from the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault to re-protect the datasource in any other vault."
          ],
          "details": null,
          "code": "UserErrorDppDatasourceAlreadyProtected",
          "target": "",
          "innerError": null,
          "isRetryable": false,
          "isUserError": false,
          "properties": {
            "ActivityId": "f36eb67a-8932-42a8-8aba-c5ee2443aa2e"
          }
        }
      }
    ],
    "code": "UserErrorDppDatasourceAlreadyProtected",
    "message": "Datasource is already protected under the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault.",
    "target": null
  }
}
```

###### <a name="tracking-response"></a>응답 추적

데이터 원본이 보호되지 않는 경우 API는 추가 유효성 검사를 위해 진행하고 추적 작업을 만듭니다.

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 10
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1197
x-ms-correlation-request-id: 3e7cacb3-65cd-4b3c-8145-71fe90d57327
x-ms-routing-request-id: CENTRALUSEUAP:20210707T124850Z:105f2105-6db1-44bf-8a34-45972a8ba861
Cache-Control: no-cache
Date: Wed, 07 Jul 2021 12:48:50 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

간단한 *GET* 명령과 함께 “Azure-AsyncOperation” 헤더를 사용하여 결과 작업을 추적합니다.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "status": "Inprogress",
  "startTime": "2021-07-07T12:48:50.3432229Z",
  "endTime": "0001-01-01T00:00:00"
}
```

완료되면 200(OK)이 반환되며 응답 본문은 권한과 같이 충족해야 하는 추가 요구 사항을 나열합니다.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "status": "Failed",
  "error": {
    "additionalInfo": [
      {
        "type": "UserFacingError",
        "info": {
          "message": "Appropriate permissions to perform the operation is missing.",
          "recommendedAction": [
            "Grant appropriate permissions to perform this operation as mentioned at https://aka.ms/UserErrorMissingRequiredPermissions and retry the operation."
          ],
          "code": "UserErrorMissingRequiredPermissions",
          "target": "",
          "innerError": {
            "code": "UserErrorMissingRequiredPermissions",
            "additionalInfo": {
              "DetailedNonLocalisedMessage": "Validate for Protection failed. Exception Message: The client 'a8b24f84-f43c-45b3-aa54-e3f6d54d31a6' with object id 'a8b24f84-f43c-45b3-aa54-e3f6d54d31a6' does not have authorization to perform action 'Microsoft.Authorization/roleAssignments/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup/providers/Microsoft.Authorization' or the scope is invalid. If access was recently granted, please refresh your credentials."
            }
          },
          "isRetryable": false,
          "isUserError": false,
          "properties": {
            "ActivityId": "3e7cacb3-65cd-4b3c-8145-71fe90d57327"
          }
        }
      }
    ],
    "code": "UserErrorMissingRequiredPermissions",
    "message": "Appropriate permissions to perform the operation is missing."
  },
  "startTime": "2021-07-07T12:48:50.3432229Z",
  "endTime": "2021-07-07T12:49:22Z"
}
```

모든 권한이 부여되면 요청 유효성 검사를 다시 제출하고, 결과 작업을 추적합니다. 모든 조건이 충족되면 성공으로 200(OK)이 반환됩니다.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzlhMjk2YWM2LWRjNDMtNGRjZS1iZTU2LTRkZDNiMDhjZDlkOA==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzlhMjk2YWM2LWRjNDMtNGRjZS1iZTU2LTRkZDNiMDhjZDlkOA==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzlhMjk2YWM2LWRjNDMtNGRjZS1iZTU2LTRkZDNiMDhjZDlkOA==",
  "status": "Succeeded",
  "startTime": "2021-07-07T13:03:54.8627251Z",
  "endTime": "2021-07-07T13:04:06Z"
}
```

### <a name="configure-backup-request"></a>백업 요청 구성

요청의 유효성이 검사되면 [백업 인스턴스 만들기 API](/rest/api/dataprotection/backup-instances/create-or-update)에 동일한 항목을 제출할 수 있습니다. 백업 인스턴스는 백업 자격 증명 모음 내에서 Azure Backup의 데이터 보호 서비스로 보호되는 항목을 나타냅니다. 이 경우 스토리지 계정은 백업 인스턴스이며 사소한 추가 항목과 함께 위에서 유효성을 검사한 동일한 요청 본문을 사용할 수 있습니다.

백업 인스턴스의 고유한 이름을 결정해야 하므로 리소스 이름과 고유 식별자의 조합을 사용하는 것이 좋습니다. 여기서는 “msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d”의 예제를 사용하고 백업 인스턴스 이름으로 표시합니다.

백업 인스턴스를 만들거나 업데이트하려면 다음 ***PUT*** 작업을 사용합니다.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/{BkpvaultName}/backupInstances/{UniqueBackupInstanceName}?api-version=2021-01-01
```

예를 들어, 이렇게 하면 다음으로 변환됩니다.

```http
 PUT https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d?api-version=2021-01-01
```

#### <a name="create-the-request-for-configure-backup"></a>백업 구성을 위한 요청 만들기

백업 인스턴스를 만들기 위한 요청 본문의 구성 요소는 다음과 같습니다.

|Name  |유형  |설명  |
|---------|---------|---------|
|properties     |  [BackupInstance](/rest/api/dataprotection/backup-instances/create-or-update#backupinstance)       |     BackupInstanceResource 속성    |

##### <a name="example-request-for-configure-backup"></a>백업 구성을 위한 예제 요청

[위에서](#configure-backup) 설명한 대로 고유한 이름으로 백업 요청의 유효성을 검사하는 데 사용한 것과 동일한 요청 본문을 사용합니다.

```json
{
  "name": "msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d",
  "type": "Microsoft.DataProtection/backupvaults/backupInstances",
  "properties": {
    "objectType": "BackupInstance",
    "datasourceinfo": {
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceLocation": "westus",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceUri": ""
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/BlobBackup-Policy"
    }
  }
}
```

#### <a name="responses-to-configure-backup-request"></a>백업 요청 구성에 대한 응답

백업 인스턴스 요청 만들기는 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

백업 인스턴스가 만들어지고 보호가 구성되고 있는 경우 201(생성됨) 및 해당 구성이 완료된 경우 200(OK)의 두 가지 응답이 반환됩니다.

|Name  |유형  |설명  |
|---------|---------|---------|
|201 생성됨   |   [백업 인스턴스](/rest/api/dataprotection/backup-instances/create-or-update#backupinstanceresource)      |  백업 인스턴스가 생성되고 보호가 구성되고 있음      |
|200 정상     |    [백업 인스턴스](/rest/api/dataprotection/backup-instances/create-or-update#backupinstanceresource)     |     보호가 구성됨    |
| 기타 상태 코드 |    [CloudError](/rest/api/dataprotection/backup-instances/validate-for-backup#clouderror)    |    작업이 실패한 이유를 설명하는 오류 응답    |

##### <a name="example-responses-to-configure-backup-request"></a>백업 요청 구성에 대한 예제 응답

*PUT* 요청을 제출하여 백업 인스턴스를 만든 후 초기 응답은 Azure-asyncOperation 헤더를 포함하는 201(생성됨)입니다. 요청 본문에는 모든 백업 인스턴스 속성이 포함됩니다.

```http
HTTP/1.1 201 Created
Content-Length: 1149
Content-Type: application/json
Expires: -1
Pragma: no-cache
Retry-After: 15
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 5d9ccf1b-7ac1-456d-8ae3-36c93c0d2427
x-ms-routing-request-id: CENTRALUSEUAP:20210707T170219Z:9e897266-5d86-4d13-b298-6561c60cf043
Cache-Control: no-cache
Date: Wed, 07 Jul 2021 17:02:18 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d",
  "name": "msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d",
  "type": "Microsoft.DataProtection/backupVaults/backupInstances",
  "properties": {
    "friendlyName": "msblobbackup",
    "dataSourceInfo": {
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceUri": "",
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceLocation": "westus",
      "objectType": "Datasource"
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/BlobBackup-Policy"
    },
    "protectionStatus": {
      "status": "ConfiguringProtection"
    },
    "currentProtectionState": "ConfiguringProtection",
    "provisioningState": "Provisioning",
    "objectType": "BackupInstance"
  }
}
```

그런 다음, 간단한 *GET* 명령과 함께 Azure-AsyncOperation 헤더를 사용하여 결과 작업을 추적합니다.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==?api-version=2021-01-01
```

작업이 완료되면 응답 본문에서 성공 메시지와 함께 200(정상)이 반환됩니다.

```json
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==",
  "status": "Succeeded",
  "startTime": "2021-07-07T17:02:19.0611871Z",
  "endTime": "2021-07-07T17:02:20Z"
}
```

> [!IMPORTANT]
> 스토리지 계정이 Blob 백업용으로 구성되면 변경 피드 및 삭제 잠금과 몇 가지 기능이 영향을 받습니다. [자세히 알아보기](blob-backup-configure-manage.md#effects-on-backed-up-storage-accounts).

### <a name="stop-protection-and-delete-data"></a>보호 중지 및 데이터 삭제

스토리지 계정에 대한 보호를 제거하고 백업 데이터도 삭제하려면 [여기](/rest/api/dataprotection/backup-instances/delete)에 설명된 대로 삭제 작업을 수행합니다.

보호 중지 및 데이터 삭제는 *DELETE* 작업입니다.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}?api-version=2021-01-01
```

예를 들어 이렇게 하면 다음으로 변환됩니다.

```http
DELETE "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d?api-version=2021-01-01"
```

#### <a name="responses-for-delete-protection"></a>삭제 방지에 대한 응답

보호 *DELETE* 작업은 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

이 작업은 다른 작업을 만드는 경우 202(수락됨) 및 해당 작업이 완료되는 경우 200(정상)의 두 응답을 반환합니다.

|속성  |유형  |설명  |
|---------|---------|---------|
|200 정상     |         |  삭제 요청의 상태       |
|202 수락됨     |         |     수락됨    |

##### <a name="example-responses-for-delete-protection"></a>삭제 방지에 대한 예제 응답

*DELETE* 요청을 제출한 후 초기 응답은 Azure-asyncOperation 헤더와 함께 202 수락됨이 됩니다.

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 30
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-deletes: 14999
x-ms-correlation-request-id: fee7a361-b1b3-496d-b398-60fed030d5a7
x-ms-routing-request-id: CENTRALUSEUAP:20210708T071330Z:5c3a9f3e-53aa-4d5d-bf9a-20de5601b090
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 07:13:29 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

간단한 *GET* 요청으로 Azure-AsyncOperation 헤더를 추적합니다. 요청이 성공하면 성공 상태 응답과 함께 200 OK가 반환됩니다.

```http
GET "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==?api-version=2021-01-01"

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==",
  "status": "Succeeded",
  "startTime": "2021-07-08T07:13:30.23815Z",
  "endTime": "2021-07-08T07:13:46Z"
}
```

## <a name="next-steps"></a>다음 단계

[Azure Blob 백업에서 데이터를 복원합니다](backup-azure-arm-userestapi-restoreazurevms.md).

Azure Backup REST API에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Data Protection 공급자 REST API](/rest/api/dataprotection/)
- [Azure REST API 시작하기](/rest/api/azure/)
