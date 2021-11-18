---
title: Azure 데이터 보호 REST API 사용하여 Azure PostgreSQL 데이터베이스 백업
description: 이 문서에서는 REST API 사용하여 Azure PostgreSQL 데이터베이스의 백업 작업을 구성, 시작 및 관리하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 10/22/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.assetid: 55fa0a81-018f-4843-bef8-609a44c97dcd
ms.openlocfilehash: cc5decd55c9724b486e657dcac70f38eb8c875a5
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719963"
---
# <a name="back-up-azure-postgresql-databases-using-azure-data-protection-via-rest-api"></a>REST API 통해 Azure 데이터 보호를 사용하여 Azure PostgreSQL 데이터베이스 백업

이 문서에서는 REST API 통해 Azure PostgreSQL 데이터베이스에 대한 백업을 관리하는 방법을 설명합니다.

Azure PostgreSQL 데이터베이스 백업 지원 시나리오, 제한 사항 및 인증 메커니즘에 대한 자세한 내용은 [개요](backup-azure-database-postgresql-overview.md) 문서를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- [Backup 자격 증명 모음 만들기](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [PostgreSQL 백업 정책 만들기](backup-azure-data-protection-use-rest-api-create-update-postgresql-policy.md)

## <a name="configure-backup"></a>백업 구성

자격 증명 모음 및 정책이 만들어지면 Azure PostgreSQL 데이터베이스를 보호하기 위해 고려해야 하는 세 가지 중요한 지점이 있습니다.

### <a name="key-entities-involved"></a>관련된 주요 엔터티

#### <a name="postgresql-database-to-be-protected"></a>보호할 PostgreSQL 데이터베이스

보호할 PostgreSQL 데이터베이스의 ARM ID(Azure Resource Manager ID)를 가져옵니다. 데이터베이스의 식별자로 사용됩니다. 다른 구독의 리소스 그룹 **ossdemoRG에** 있는 PostgreSQL 서버 **testpostgresql** 아래에 **empdb11이라는** 데이터베이스의 예를 사용합니다. 다음 예제에서는 bash를 사용합니다.

```http
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11"
```

#### <a name="azure-key-vault"></a>Azure Key Vault

Azure Backup 서비스는 PostgreSQL 데이터베이스에 연결하기 위한 사용자 이름과 암호를 저장하지 않습니다. 대신 백업 관리자가 *키를 키 자격 증명* 모음에 시드해야 합니다. 그러면 Azure Backup 서비스에서 키 자격 증명 모음에 액세스하고, 키를 읽고, 데이터베이스에 액세스합니다. 관련 키의 비밀 식별자를 확인합니다. 다음 예제에서는 bash를 사용합니다.

```http
"https://testkeyvaulteus.vault.azure.net/secrets/ossdbkey"
```

#### <a name="backup-vault"></a>Backup 자격 증명 모음

백업 자격 증명 모음은 PostgreSQL 서버에 연결한 다음 키 자격 증명 모음에 있는 키를 통해 데이터베이스에 액세스해야 합니다. 따라서 PostgreSQL 서버 및 키 자격 증명 모음에 액세스해야 합니다. Backup 자격 증명 모음의 MSI에 대한 액세스 권한이 부여됩니다.

PostgreSQL 서버 및 데이터베이스에 대한 키가 저장되는 Azure Key Vault에서 자격 증명 모음의 MSI를 백업하기 위해 부여해야 하는 [적절한 권한을 읽어보십시오.](./backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-backup)

### <a name="prepare-the-request-to-configure-backup"></a>백업을 구성하기 위한 요청 준비

관련 권한이 자격 증명 모음 및 PostgreSQL 데이터베이스로 설정되고 자격 증명 모음 및 정책이 구성되면 백업 구성 요청을 준비할 수 있습니다. 다음은 Azure PostgreSQL 데이터베이스에 대한 백업을 구성하는 요청 본문입니다. Azure PostgreSQL 데이터베이스의 ARM ID(Azure Resource Manager ID) 및 해당 세부 정보는 _datasourceinfo_ 섹션에 설명되어 있으며 정책 정보는 _policyinfo_ 섹션에 있습니다.

```json
{
  "backupInstance": {
    "dataSourceInfo": {
          "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
          "resourceUri": "",
          "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
          "resourceName": "empdb11",
          "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
          "resourceLocation": "westUS",
          "objectType": "Datasource"
      },
      "dataSourceSetInfo": {
          "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
          "resourceUri": "",
          "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
          "resourceName": "testpostgresql",
          "resourceType": "Microsoft.DBforPostgreSQL/servers",
          "resourceLocation": "westUS",
          "objectType": "DatasourceSet"
      },
      "policyInfo": {
          "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
          "policyVersion": ""
      },
    "objectType": "BackupInstance"
  }
}
```

### <a name="validate-the-request-to-configure-backup"></a>백업을 구성하기 위한 요청의 유효성 검사

백업 구성 요청이 성공하는지 확인하려면 백업 [API에 대한 유효성 검사를](/rest/api/dataprotection/backup-instances/validate-for-backup)사용합니다. 응답을 사용하여 필요한 필수 구성을 수행한 다음 백업 요청에 대한 구성을 제출할 수 있습니다.

백업 요청의 유효성 검사는 _POST_ 작업이며 URI에는 `{subscriptionId}` , , 매개 변수가 `{vaultName}` 포함됩니다. `{vaultresourceGroupName}`

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.DataProtection/backupVaults/{backupVaultName}/validateForBackup?api-version=2021-01-01
```

예를 들어 이 API는 다음으로 변환됩니다.

```http
POST https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/validateForBackup?api-version=2021-01-01
```

앞에서 준비한 [요청 본문은](#prepare-the-request-to-configure-backup) 보호할 Azure PostgreSQL 데이터베이스의 세부 정보를 제공하는 데 사용됩니다.

#### <a name="example-request-body"></a>요청 본문 예제

```json
{
  "backupInstance": {
    "dataSourceInfo": {
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
        "resourceUri": "",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceName": "empdb11",
        "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceLocation": "westUS",
        "objectType": "Datasource"
    },
    "dataSourceSetInfo": {
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
        "resourceUri": "",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceName": "testpostgresql",
        "resourceType": "Microsoft.DBforPostgreSQL/servers",
        "resourceLocation": "westUS",
        "objectType": "DatasourceSet"
    },
    "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
        "policyVersion": ""
    },
    "objectType": "BackupInstance"
  }
}
```

#### <a name="responses-for-backup-request-validation"></a>백업 요청 유효성 검사에 대한 응답

백업 요청 유효성 검사는 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 따라서 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

다른 작업이 생성될 때 202(수락됨), 해당 작업이 완료될 때 200(정상)인 두 개의 응답이 반환됩니다.

|이름  |유형  |Description  |
|---------|---------|---------|
|202 수락됨     |         |  작업이 비동기적으로 완료됩니다.      |
|200 정상     |   [OperationJobExtendedInfo](/rest/api/dataprotection/backup-instances/validate-for-backup#operationjobextendedinfo)      |     수락됨    |
| 기타 상태 코드 |    [CloudError](/rest/api/dataprotection/backup-instances/validate-for-backup#clouderror)    |    작업이 실패한 이유를 설명하는 오류 응답    |

##### <a name="example-responses-for-validate-backup-request"></a>백업 요청 유효성 검사의 예제 응답

###### <a name="error-response"></a>오류 응답

지정된 디스크가 이미 보호된 경우 응답을 HTTP 400(잘못된 요청)으로 반환하고 지정된 디스크가 백업 자격 증명 모음으로 보호되고 세부 정보가 함께 표시됩니다.

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
x-ms-routing-request-id: WESTUS:20210830T142949Z:0be72802-02ad-485d-b91f-4aadd92c059c
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
            "Delete the backup instance testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149 from the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault to re-protect the datasource in any other vault."
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

###### <a name="track-response"></a>응답 추적

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
x-ms-routing-request-id: WESTUS:20210707T124850Z:105f2105-6db1-44bf-8a34-45972a8ba861
Cache-Control: no-cache
Date: Wed, 07 Jul 2021 12:48:50 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

간단한 *GET* 명령으로 _Azure-AsyncOperation_ 헤더를 사용하여 결과 작업을 추적합니다.

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

완료되면 200(OK)을 반환하고 응답 본문에는 사용 권한과 같이 충족해야 하는 추가 요구 사항이 나열됩니다.

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
              "DetailedNonLocalisedMessage": "Validate for Protection failed. Exception Message: The client 'a8b24f84-f43c-45b3-aa54-e3f6d54d31a6' with object id 'a8b24f84-f43c-45b3-aa54-e3f6d54d31a6' does not have authorization to perform action 'Microsoft.Authorization/roleAssignments/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/providers/Microsoft.Authorization' or the scope is invalid. If access was recently granted, please refresh your credentials."
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

모든 권한을 부여한 다음 유효성 검사 요청을 다시 제출하면 결과 작업을 추적합니다. 모든 조건이 충족되면 성공 응답을 200(정상)으로 반환합니다.

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

요청의 유효성이 검사되면 [백업 인스턴스 만들기 API](/rest/api/dataprotection/backup-instances/create-or-update)에 동일한 항목을 제출할 수 있습니다. Backup 인스턴스는 Backup 자격 증명 모음 내에서 Azure Backup 데이터 보호 서비스로 보호되는 항목을 나타냅니다. 여기서 Azure PostgreSQL 데이터베이스는 백업 인스턴스이며, 위에서 유효성을 검사한 동일한 요청 본문을 사소한 추가와 함께 사용할 수 있습니다.

백업 인스턴스에 고유한 이름을 사용합니다. 따라서 리소스 이름과 고유 식별자의 조합을 사용하는 것이 좋습니다. 예를 들어 다음 작업에서는 _testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149를_ 사용하여 백업 인스턴스 이름으로 표시합니다.

백업 인스턴스를 만들거나 업데이트하려면 다음 ***PUT*** 작업을 사용합니다.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/{BkpvaultName}/backupInstances/{UniqueBackupInstanceName}?api-version=2021-01-01
```

예를 들어 이 API는 다음으로 변환됩니다.

```http
 PUT https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149?api-version=2021-01-01
```

#### <a name="create-the-request-for-configure-backup"></a>백업 구성을 위한 요청 만들기

백업 인스턴스를 만들려면 요청 본문의 구성 요소는 다음과 같습니다.

|이름  |유형  |설명  |
|---------|---------|---------|
|properties     |  [BackupInstance](/rest/api/dataprotection/backup-instances/create-or-update#backupinstance)       |     BackupInstanceResource 속성    |

##### <a name="example-request-for-configure-backup"></a>백업 구성을 위한 예제 요청

[위에서](#configure-backup)설명한 대로 고유한 이름으로 백업 요청의 유효성을 검사하는 데 사용한 것과 동일한 요청 본문을 사용합니다.

```json
{
  "name": "testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
  "type": "Microsoft.DataProtection/backupvaults/backupInstances",
  "properties": {
    "dataSourceInfo": {
            "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
            "resourceUri": "",
            "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceName": "empdb11",
            "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceLocation": "westUS",
            "objectType": "Datasource"
        },
        "dataSourceSetInfo": {
            "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
            "resourceUri": "",
            "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceName": "testpostgresql",
            "resourceType": "Microsoft.DBforPostgreSQL/servers",
            "resourceLocation": "westUS",
            "objectType": "DatasourceSet"
        },
        "policyInfo": {
            "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
            "policyVersion": ""
        }
    },
    "objectType": "BackupInstance"
  }
}
```

#### <a name="responses-to-configure-backup-request"></a>백업 요청 구성에 대한 응답

_백업 인스턴스 만들기 요청은_ [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 따라서 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

백업 인스턴스가 만들어지고 보호가 구성될 때 201(생성됨)과 해당 구성이 완료되면 200(OK)의 두 응답을 반환합니다.

|이름  |유형  |설명  |
|---------|---------|---------|
|201 생성됨   |   [백업 인스턴스](/rest/api/dataprotection/backup-instances/create-or-update#backupinstanceresource)      |  백업 인스턴스가 생성되고 보호가 구성되고 있음      |
|200 정상     |    [백업 인스턴스](/rest/api/dataprotection/backup-instances/create-or-update#backupinstanceresource)     |     보호가 구성됨    |
| 기타 상태 코드 |    [CloudError](/rest/api/dataprotection/backup-instances/validate-for-backup#clouderror)    |    작업이 실패한 이유를 설명하는 오류 응답    |

##### <a name="example-responses-to-configure-backup-request"></a>백업 요청 구성에 대한 예제 응답

*PUT* 요청을 제출하여 백업 인스턴스를 만들면 초기 응답은 _Azure-asyncOperation_ 헤더가 있는 201(생성)입니다. 요청 본문에는 모든 백업 인스턴스 속성이 포함됩니다.

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
x-ms-routing-request-id: WESTUS:20210707T170219Z:9e897266-5d86-4d13-b298-6561c60cf043
Cache-Control: no-cache
Date: Wed, 07 Jul 2021 17:02:18 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
    "properties": {
        "friendlyName": "testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
        "dataSourceInfo": {
            "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
            "resourceUri": "",
            "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceName": "empdb11",
            "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceLocation": "westUS",
            "objectType": "Datasource"
        },
        "dataSourceSetInfo": {
            "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
            "resourceUri": "",
            "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceName": "testpostgresql",
            "resourceType": "Microsoft.DBforPostgreSQL/servers",
            "resourceLocation": "westUS",
            "objectType": "DatasourceSet"
        },
        "policyInfo": {
            "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
            "policyVersion": ""
        },
        "protectionStatus": {
            "status": "ProtectionConfigured"
        },
        "currentProtectionState": "ProtectionConfigured",
        "provisioningState": "Succeeded",
        "objectType": "BackupInstance"
    },
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
    "name": "testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
    "type": "Microsoft.DataProtection/backupVaults/backupInstances"
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

Azure PostgreSQL 데이터베이스에 대 한 보호를 제거 하 고 백업 데이터도 삭제 하려면 [삭제 작업](/rest/api/dataprotection/backup-instances/delete)을 수행 합니다.

보호 중지 및 데이터 삭제는 *DELETE* 작업입니다.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}?api-version=2021-01-01
```

예를 들어이 API는 다음과 같이 변환 됩니다.

```http
DELETE "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149?api-version=2021-01-01"
```

#### <a name="responses-for-delete-protection"></a>삭제 방지에 대한 응답

보호 *DELETE* 작업은 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 따라서이 작업은 별도로 추적 해야 하는 다른 작업을 만듭니다.

다른 작업이 생성될 때 202(수락됨), 해당 작업이 완료될 때 200(정상)인 두 개의 응답이 반환됩니다.

|이름  |유형  |설명  |
|---------|---------|---------|
|200 정상     |         |  삭제 요청의 상태       |
|202 수락됨     |         |     수락됨    |

##### <a name="example-responses-for-delete-protection"></a>삭제 방지에 대한 예제 응답

*삭제* 요청을 제출 하면 초기 응답은 _asyncOperation_ 헤더를 사용 하 여 202 (수락) 됩니다.

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
x-ms-routing-request-id: WESTUS:20210708T071330Z:5c3a9f3e-53aa-4d5d-bf9a-20de5601b090
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 07:13:29 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

간단한 *GET* 요청을 사용 하 여 _AsyncOperation_ 헤더를 추적 합니다. 요청이 성공적으로 완료 되 면 성공 상태 응답과 함께 200 (정상)이 반환 됩니다.

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

[Azure PostGreSQL 데이터베이스 백업에서 데이터 복원](restore-postgresql-database-use-rest-api.md)

Azure Backup REST Api에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [Azure Data Protection 공급자 REST API](/rest/api/dataprotection/)
- [Azure REST API 시작하기](/rest/api/azure/)