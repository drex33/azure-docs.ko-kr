---
title: Azure 데이터 보호 REST API를 사용 하 여 Azure 디스크를 백업 합니다.
description: 이 문서에서는 REST API를 사용 하 여 Azure 디스크의 백업 작업을 구성, 시작 및 관리 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 10/06/2021
ms.assetid: 6050a941-89d7-4b27-9976-69898cc34cde
ms.openlocfilehash: 6aeba0682839384b930dca0c2df34f109260335c
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129622425"
---
# <a name="back-up-azure-disks-using-azure-data-protection-via-rest-api"></a>REST API를 통해 Azure 데이터 보호를 사용 하 여 Azure 디스크 백업

이 문서에서는 REST API를 통해 Azure 디스크에 대 한 백업을 관리 하는 방법을 설명 합니다.

Azure Disk Backup 지역 가용성, 지원되는 시나리오 및 제한 사항에 대한 정보는 [지원 매트릭스](disk-backup-support-matrix.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- [Backup 자격 증명 모음 만들기](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [디스크 백업 정책 만들기](backup-azure-dataprotection-use-rest-api-create-update-disk-policy.md)

## <a name="configure-backup"></a>백업 구성

### <a name="key-entities-involved"></a>관련된 주요 엔터티

자격 증명 모음 및 정책을 만들면 Azure Disk를 보호하기 위해 고려해야 할 세 가지 중요한 사항이 있습니다.

#### <a name="disk-to-be-protected"></a>보호할 디스크

ARM ID와 보호할 디스크의 위치를 적어둡니다. 디스크의 식별자 역할을 합니다.

```http
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup"
```

#### <a name="snapshot-resource-group"></a>스냅샷 리소스 그룹

디스크 스냅샷은 구독 내의 리소스 그룹에 저장됩니다. 지침으로 Azure Backup 서비스에서 사용할 스냅샷 데이터 저장소로 전용 리소스 그룹을 만드는 것이 좋습니다. 전용 리소스 그룹을 사용하면 리소스 그룹에 대한 액세스 권한을 제한할 수 있기 때문에 안전하고 쉽게 백업 데이터를 관리할 수 있습니다. 디스크 스냅숏을 저장 하려는 리소스 그룹의 ARM ID를 적어둡니다.

```http
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/snapshot-rg"
```

#### <a name="backup-vault"></a>Backup 자격 증명 모음

백업 자격 증명 모음에는 백업을 사용 하도록 설정 하기 위해 디스크에 대 한 권한이 필요 합니다. 자격 증명 모음의 시스템이 할당한 관리 ID는 해당 권한을 할당하는 데 사용됩니다.

### <a name="assign-permissions"></a>권한 할당

RBAC를 통해 자격 증명 모음(자격 증명 모음 MSI로 표시) 및 관련 디스크 및/또는 디스크 RG에 몇 가지 권한을 할당해야 합니다. 이 작업은 Azure Portal 또는 CLI를 통해 수행할 수 있습니다. 관련 사용 권한을 할당 하려면 [managed disks의 백업을 구성 하기 위한 필수 구성 요소](/azure/backup/backup-managed-disks-ps#assign-permissions)를 참조 하세요.

### <a name="prepare-the-request-to-configure-backup"></a>백업을 구성하기 위한 요청 준비

관련 권한이 자격 증명 모음 및 디스크로 설정 되 고 자격 증명 모음 및 정책이 구성 되 면 백업을 구성 하는 요청을 준비할 수 있습니다. Azure 디스크에 대 한 백업을 구성 하는 요청 본문은 다음과 같습니다. Azure 디스크의 Azure Resource Manager ID (ARM ID) 및 세부 정보는 _datasourceinfo_ 섹션에서 설명 하 고 정책 정보는 스냅숏 리소스 그룹이 정책 매개 변수 중 하나로 제공 되는 _policyinfo_ 섹션에 있습니다.

```json
{
  "backupInstance": {
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Compute/",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup",
      "resourceLocation": "westUS",
      "resourceName": "msdiskbackup",
      "resourceType": "Microsoft.Compute/disks",
      "resourceUri": ""
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/DiskBackup-Policy",
      "policyParameters": {
        "dataStoreParametersList": [
          {
            "dataStoreType": "OperationalStore",
            "objectType": "AzureOperationalStoreParameters",
            "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/snapshot-rg"
          }
        ]
      }
    },
    "objectType": "BackupInstance"
  }
}
```

### <a name="validate-the-request-to-configure-backup"></a>백업을 구성하기 위한 요청의 유효성 검사

백업 구성에 대 한 요청이 성공적으로 수행 되는지 확인 하려면 [BACKUP API에 대 한 유효성 검사](/rest/api/dataprotection/backup-instances/validate-for-backup)를 사용 합니다. 응답을 사용 하 여 필수 구성 요소를 수행한 다음 백업 요청에 대 한 구성을 제출할 수 있습니다.

Backup 요청에 대 한 유효성 검사는 _POST_ 작업이 고 URI는 `{subscriptionId}` , `{vaultName}` , 매개 변수를 포함 `{vaultresourceGroupName}` 합니다.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.DataProtection/backupVaults/{backupVaultName}/validateForBackup?api-version=2021-01-01
```

예를 들어이 API는 다음과 같이 변환 됩니다.

```http
POST https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/validateForBackup?api-version=2021-01-01
```

앞에서 준비한 [요청 본문](#prepare-the-request-to-configure-backup) 은 보호할 Azure 디스크의 세부 정보를 제공 하는 데 사용 됩니다.

#### <a name="example-request-body"></a>요청 본문 예제

```json
{
  "backupInstance": {
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Compute/disks",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup",
      "resourceLocation": "westUS",
      "resourceName": "msdiskbackup",
      "resourceType": "Microsoft.Compute/disks",
      "resourceUri": ""
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/DiskBackup-Policy",
      "policyParameters": {
        "dataStoreParametersList": [
          {
            "dataStoreType": "OperationalStore",
            "objectType": "AzureOperationalStoreParameters",
            "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/snapshot-rg"
          }
        ]
      }
    },
    "objectType": "BackupInstance"
  }
}
```

#### <a name="responses-for-backup-request-validation"></a>백업 요청 유효성 검사에 대 한 응답

백업 요청 유효성 검사는 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 따라서이 작업은 별도로 추적 해야 하는 다른 작업을 만듭니다.

이 작업은 다른 작업을 만드는 경우 202(수락됨) 및 해당 작업이 완료되는 경우 200(정상)의 두 응답을 반환합니다.

|속성  |Type  |Description  |
|---------|---------|---------|
|202 수락됨     |         |  작업이 비동기적으로 완료됩니다.      |
|200 정상     |   [OperationJobExtendedInfo](/rest/api/dataprotection/backup-instances/validate-for-backup#operationjobextendedinfo)      |     수락됨    |
| 기타 상태 코드 |    [CloudError](/rest/api/dataprotection/backup-instances/validate-for-backup#clouderror)    |    작업이 실패한 이유를 설명하는 오류 응답    |

##### <a name="example-responses-for-validate-backup-request"></a>백업 요청 유효성 검사의 예제 응답

###### <a name="error-response"></a>오류 응답

지정 된 디스크가 이미 보호 되 고 있는 경우 응답을 HTTP 400 (잘못 된 요청)로 반환 하 고, 지정 된 디스크가 세부 정보와 함께 백업 자격 증명 모음에 대해 보호 되는 것으로 상태를 지정 합니다.

```http
HTTP/1.1 400 BadRequest
Content-Length: 1012
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 0c99ff0f-6c26-4ec7-899f-205435e89894
x-ms-routing-request-id: CENTRALUSEUAP:20210830T142949Z:0be72802-02ad-485d-b91f-4aadd92c059c
Cache-Control: no-cache
Date: Mon, 30 Aug 2021 14:29:49 GMT
X-Powered-By: ASP.NET

{
  "error": {
    "additionalInfo": [
      {
        "type": "UserFacingError",
        "info": {
          "message": "Datasource is already protected under the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault.",
          "recommendedAction": [
            "Delete the backup instance SharedDataDisk from the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault to re-protect the datasource in any other vault."
          ],
          "details": null,
          "code": "UserErrorDppDatasourceAlreadyProtected",
          "target": "",
          "innerError": null,
          "isRetryable": false,
          "isUserError": false,
          "properties": {
            "ActivityId": "0c99ff0f-6c26-4ec7-899f-205435e89894"
          }
        }
      }
    ],
    "code": "UserErrorDppDatasourceAlreadyProtected",
    "message": "Datasource is already protected under the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault.",
    "target": null,
    "details": null
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

간단한 *GET* 명령을 사용 하 여 _Azure-AsyncOperation_ 헤더를 사용 하 여 결과 작업을 추적 합니다.

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

완료 되 면 200 (OK)을 반환 하 고, 응답 본문에는 사용 권한과 같이 추가로 충족 되어야 하는 요구 사항이 나열 됩니다.

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
              "DetailedNonLocalisedMessage": "Validate for Protection failed. Exception Message: The client 'a8b24f84-f43c-45b3-aa54-e3f6d54d31a6' with object id 'a8b24f84-f43c-45b3-aa54-e3f6d54d31a6' does not have authorization to perform action 'Microsoft.Authorization/roleAssignments/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup/providers/Microsoft.Authorization' or the scope is invalid. If access was recently granted, please refresh your credentials."
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

모든 권한을 부여 하는 경우 유효성 검사 요청을 다시 제출 하 고, 결과 작업을 추적 하 고, 모든 조건이 충족 되는 경우 성공 응답을 200 (OK)로 반환 합니다.

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

요청의 유효성이 검사되면 [백업 인스턴스 만들기 API](/rest/api/dataprotection/backup-instances/create-or-update)에 동일한 항목을 제출할 수 있습니다. 백업 인스턴스는 백업 자격 증명 모음 내에서 Azure Backup의 데이터 보호 서비스로 보호 되는 항목을 나타냅니다. 여기에서 Azure 디스크는 백업 인스턴스이고, 위에서 유효성을 검사 한 것과 같은 요청 본문을 사용 하 여 약간의 추가를 수행할 수 있습니다.

백업 인스턴스에 대해 고유한 이름을 사용 합니다. 따라서 리소스 이름과 고유 식별자를 조합 하 여 사용 하는 것이 좋습니다. 예를 들어 다음 작업에서 _2dc6eb5b-d008-4d68-9e49-7132d99da0ed_ 를 사용 하 여 백업 인스턴스 이름으로 표시 합니다.

백업 인스턴스를 만들거나 업데이트하려면 다음 ***PUT*** 작업을 사용합니다.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/{BkpvaultName}/backupInstances/{UniqueBackupInstanceName}?api-version=2021-01-01
```

예를 들어이 API는 다음과 같이 변환 됩니다.

```http
 PUT https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed?api-version=2021-01-01
```

#### <a name="create-the-request-for-configure-backup"></a>백업 구성을 위한 요청 만들기

백업 인스턴스를 만들기 위해 요청 본문의 구성 요소는 다음과 같습니다.

|Name  |Type  |설명  |
|---------|---------|---------|
|properties     |  [BackupInstance](/rest/api/dataprotection/backup-instances/create-or-update#backupinstance)       |     BackupInstanceResource 속성    |

##### <a name="example-request-for-configure-backup"></a>백업 구성을 위한 예제 요청

[위에서](#configure-backup)언급 한 대로 고유한 이름을 사용 하 여 백업 요청의 유효성을 검사 하는 데 사용한 것과 동일한 요청 본문을 사용 합니다.

```json
{
  "name": "msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed",
  "type": "Microsoft.DataProtection/backupvaults/backupInstances",
  "properties": {
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Compute/disks",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup",
      "resourceLocation": "westUS",
      "resourceName": "msdiskbackup",
      "resourceType": "Microsoft.Compute/disks",
      "resourceUri": ""
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/DiskBackup-Policy",
      "policyParameters": {
        "dataStoreParametersList": [
          {
            "dataStoreType": "OperationalStore",
            "objectType": "AzureOperationalStoreParameters",
            "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/snapshot-rg"
          }
        ]
      }
    },
    "objectType": "BackupInstance"
  }
}
```

#### <a name="responses-to-configure-backup-request"></a>백업 요청 구성에 대한 응답

_백업 인스턴스 만들기 요청_ 은 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 따라서이 작업은 별도로 추적 해야 하는 다른 작업을 만듭니다.

이는 백업 인스턴스가 만들어지고 보호가 구성 될 때 201 (Created) 응답을 반환 하 고, 해당 구성이 완료 되 면 200 (확인)을 반환 합니다.

|Name  |Type  |설명  |
|---------|---------|---------|
|201 생성됨   |   [백업 인스턴스](/rest/api/dataprotection/backup-instances/create-or-update#backupinstanceresource)      |  백업 인스턴스가 생성되고 보호가 구성되고 있음      |
|200 정상     |    [백업 인스턴스](/rest/api/dataprotection/backup-instances/create-or-update#backupinstanceresource)     |     보호가 구성됨    |
| 기타 상태 코드 |    [CloudError](/rest/api/dataprotection/backup-instances/validate-for-backup#clouderror)    |    작업이 실패한 이유를 설명하는 오류 응답    |

##### <a name="example-responses-to-configure-backup-request"></a>백업 요청 구성에 대한 예제 응답

*PUT* 요청을 제출하여 백업 인스턴스를 만든 후 초기 응답은 Azure-asyncOperation 헤더를 포함하는 201(생성됨)입니다. 요청 본문에는 모든 백업 인스턴스 속성이 포함 되어 있습니다.

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
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed",
  "name": "msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed",
  "type": "Microsoft.DataProtection/backupVaults/backupInstances",
  "properties": {
    "friendlyName": "msdiskbackup",
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Compute/disks",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup",
      "resourceLocation": "westUS",
      "resourceName": "msdiskbackup",
      "resourceType": "Microsoft.Compute/disks",
      "resourceUri": ""
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/DiskBackup-Policy",
      "policyParameters": {
        "dataStoreParametersList": [
          {
            "dataStoreType": "OperationalStore",
            "objectType": "AzureOperationalStoreParameters",
            "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/snapshot-rg"
          }
        ]
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

그런 다음 간단한 *GET* 명령을 사용 하 여 _Azure-AsyncOperation_ 헤더를 사용 하 여 결과 작업을 추적 합니다.

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

### <a name="stop-protection-and-delete-data"></a>보호 중지 및 데이터 삭제

Azure 디스크에서 보호를 제거하고 백업 데이터도 삭제하려면 [삭제 작업을](/rest/api/dataprotection/backup-instances/delete)수행합니다.

보호 중지 및 데이터 삭제는 *DELETE* 작업입니다.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}?api-version=2021-01-01
```

예를 들어 이 API는 다음으로 변환됩니다.

```http
DELETE "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed?api-version=2021-01-01"
```

#### <a name="responses-for-delete-protection"></a>삭제 방지에 대한 응답

보호 *DELETE* 작업은 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 따라서 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

다른 작업이 생성될 때 202(수락됨), 해당 작업이 완료될 때 200(정상)인 두 개의 응답이 반환됩니다.

|이름  |Type  |Description  |
|---------|---------|---------|
|200 정상     |         |  삭제 요청의 상태       |
|202 수락됨     |         |     수락됨    |

##### <a name="example-responses-for-delete-protection"></a>삭제 방지에 대한 예제 응답

*DELETE* 요청을 제출하면 초기 응답은 Azure-asyncOperation 헤더가 있는 202(수락됨)가 됩니다.

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

간단한 GET 요청으로 _Azure-AsyncOperation_ 헤더를 *추적합니다.* 요청이 성공하면 성공 상태 응답과 함께 200 OK가 반환됩니다.

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

[Azure 디스크 백업에서 데이터 복원](backup-azure-arm-userestapi-restoreazurevms.md)

Azure Backup REST API에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Data Protection 공급자 REST API](/rest/api/dataprotection/)
- [Azure REST API 시작하기](/rest/api/azure/)
