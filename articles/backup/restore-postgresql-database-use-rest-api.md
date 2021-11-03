---
title: Azure 데이터 보호 REST API 통해 Azure PostgreSQL 데이터베이스 복원
description: Azure Data Protection REST API 사용하여 Azure PostGreSQL 데이터베이스를 복원하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 10/23/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: eef655cb482bbfe61bcd645f6038616b4321229c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052791"
---
# <a name="restore-azure-postgresql-databases-using-azure-data-protection-rest-api"></a>Azure 데이터 보호 REST API 사용하여 Azure PostgreSQL 데이터베이스 복원

이 문서에서는 Azure Backup 백업된 [Azure PostgreSQL](/azure/postgresql/overview#azure-database-for-postgresql---single-server) 서버로 Azure PostgreSQL 데이터베이스를 복원하는 방법을 설명합니다.

PaaS 데이터베이스인 경우 백업이 수행된 기존 데이터베이스를 교체하여 복원하는 OLR(Original-Location Recovery) 옵션은 지원되지 않습니다. 복구 지점에서 복원하여 동일한 Azure PostgreSQL 서버 또는 다른 PostgreSQL 서버에 새 데이터베이스를 만들 수 있습니다. 이를 원본 데이터베이스와 복원된(새) 데이터베이스를 유지하는 데 도움이 되는 ALR(Alternate-Location Recovery)이라고 합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- 복원하여 새 PostgreSQL 데이터베이스 만들기

- 복원 작업 상태 추적

## <a name="prerequisites"></a>필수 구성 요소

- [Backup 자격 증명 모음 만들기](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [PostgreSQL 데이터베이스 백업 정책 만들기](backup-azure-data-protection-use-rest-api-create-update-postgresql-policy.md)

- [PostgreSQL 데이터베이스 백업 구성](backup-azure-data-protection-use-rest-api-backup-postgresql.md)

예제에서 _testBkpVaultRG_ 리소스 그룹 아래에 있는 기존 백업 자격 증명 모음 _TestBkpVault_ 를 참조합니다.

## <a name="restore-a-backed-up-postgresql-database"></a>백업된 PostgreSQL 데이터베이스 복원

### <a name="set-up-permissions"></a>권한 설정

Backup 자격 증명 모음은 관리 ID를 사용하여 다른 Azure 리소스에 액세스합니다. 백업에서 복원하려면 백업 자격 증명 모음의 관리 ID에 데이터베이스를 복원해야 하는 Azure PostgreSQL 서버에 대한 권한 집합이 필요합니다.

대상 PostgreSQL 서버에서 자격 증명 모음의 시스템 할당 관리 ID에 대한 관련 권한을 할당하려면 [Azure PostgreSQL 데이터베이스를 백업하는 데 필요한 권한 집합을 참조하세요.](/azure/backup/backup-azure-database-postgresql-overview#set-of-permissions-needed-for-azure-postgresql-database-restore)

복구 지점을 스토리지 계정에 파일로 복원하려면 [여기에](/azure/backup/restore-azure-database-postgresql#restore-permissions-on-the-target-storage-account)설명된 대로 백업 자격 증명 모음의 시스템 할당 관리 ID가 대상 스토리지 계정에 액세스해야 합니다.

### <a name="fetching-the-relevant-recovery-point"></a>관련 복구 지점 가져오기

백업 인스턴스에 사용 가능한 모든 복구 지점을 나열하려면 [복구 지점 목록](/rest/api/dataprotection/recovery-points/list) API를 사용합니다.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/recoveryPoints?api-version=2021-07-01
```

예를 들어 이 API는 다음으로 변환됩니다.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149/recoveryPoints?api-version=2021-07-01
```

#### <a name="responses-for-list-of-recovery-points"></a>복구 지점 목록에 대한 응답

*GET* 요청을 제출하면 응답이 200(정상)으로 반환되고 모든 관련 세부 정보가 포함된 모든 불연속 복구 지점 목록이 반환됩니다.

|이름  |Type  |설명  |
|---------|---------|---------|
|200 정상     |    [AzureBackupRecoveryPointResourceList](/rest/api/dataprotection/recovery-points/list#azurebackuprecoverypointresourcelist)     |   정상      |
|기타 상태 코드     |    [CloudError](/rest/api/dataprotection/recovery-points/list#clouderror)     |     오류 응답은 작업 실패의 이유를 설명합니다.    |

##### <a name="example-response-for-list-of-recovery-points"></a>복구 지점 목록에 대한 응답 예제

```http
HTTP/1.1 200 OK
Content-Length: 53396
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 11999
x-ms-correlation-request-id: 41f7ef85-f31e-4db7-87ef-115e3ca65b93
x-ms-routing-request-id: SOUTHINDIA:20211022T200018Z:ba3bc1ce-c081-4895-a292-beeeb6eb22cc
Cache-Control: no-cache
Date: Fri, 22 Oct 2021 20:00:18 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "properties": {
        "objectType": "AzureBackupDiscreteRecoveryPoint",
        "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b",
        "recoveryPointTime": "2021-10-21T16:31:16.8316716Z",
        "recoveryPointType": "Full",
        "friendlyName": "794ead7c7661410da03997d210d469e7",
        "recoveryPointDataStoresDetails": [
          {
            "id": "9ea7eaf4-eeb8-4c8f-90a7-7f04b60bf075",
            "type": "VaultStore",
            "creationTime": "2021-10-21T16:31:16.8316716Z",
            "expiryTime": "2022-10-21T16:31:16.8316716Z",
            "metaData": null,
            "visible": true,
            "state": "COMMITTED",
            "rehydrationExpiryTime": null,
            "rehydrationStatus": null
          }
        ],
        "retentionTagName": "Default",
        "retentionTagVersion": "637212748405148394",
        "policyName": "osspol3",
        "policyVersion": null
      },
.
.
.
.
```

보관 계층에서 복구 지점을 가져오려면 _recoveryPointDataStoreDetails의_ _형식_ 변수를 _ArchiveStore_ 로 수정합니다.

위 목록에서 관련 복구 지점을 선택하고 복원 요청을 준비합니다. 복원할 위의 목록에서 _794ead7c7661410da03997d210d469e7이라는_ 복구 지점을 선택합니다.

### <a name="prepare-the-restore-request"></a>복원 요청 준비

PostgreSQL 데이터베이스에 대한 다양한 복원 옵션이 있습니다. 복구 지점을 다른 데이터베이스로 복원하거나 파일로 복원할 수 있습니다. 복구 지점은 보관 계층에도 있을 수 있습니다.

#### <a name="restore-as-database"></a>데이터베이스로 복원

[위에](#set-up-permissions)자세히 설명된 대로 권한이 할당된 대상 PostgreSQL 서버와 필요한 PostgreSQL 데이터베이스 이름을 사용하여 만들 새 PostgreSQL 데이터베이스의 ARM ID(Azure Resource Manager ID)를 생성합니다. 예를 들어 다른 구독을 사용하는 리소스 그룹 **targetrg의** 대상 PostgreSQL 서버 **targetossserver에서** PostgreSQL 데이터베이스의 이름을 **emprestored21로** 지정할 수 있습니다.

```http
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21"
```

다음 요청 본문에는 복구 지점 ID 및 복원 대상 세부 정보가 포함됩니다.

```json
{
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "VaultStore",
    "restoreTargetInfo": {
      "objectType": "restoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "dataSourceInfo": {
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
        "resourceName": "emprestored21",
        "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceLocation": "westus",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
      },
      "dataSourceSetInfo": {
        "objectType": "DatasourceSet",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver",
        "resourceName": "targetossserver",
        "resourceType": "Microsoft.DBforPostgreSQL/servers",
        "resourceLocation": "westus",
        "resourceUri": "",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
      },
      "datasourceAuthCredentials": {
        "objectType": "SecretStoreBasedAuthCredentials",
        "secretStoreResource": {
          "secretStoreType": "AzureKeyVault",
          "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
          "value": null
        }
      },
      "restoreLocation": "westus"
    },
    "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b"
  }
}
```

보관 기반 복구 지점의 경우 다음을 수행해야 합니다.

1. 보관 데이터 저장소에서 자격 증명 모음 저장소로 리하이딩합니다.
1. 원본 데이터 저장소를 수정합니다.
1. 다른 매개 변수를 추가하여 리하일레이션 우선 순위를 지정합니다.
1. 리하위드 복구 지점을 자격 증명 모음 데이터 저장소에 보존할 기간을 지정합니다.

```json
{
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "ArchiveStore",
    "restoreTargetInfo": {
      "objectType": "restoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "dataSourceInfo": {
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
        "resourceName": "emprestored21",
        "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceLocation": "westus",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
      },
      "dataSourceSetInfo": {
        "objectType": "DatasourceSet",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver",
        "resourceName": "targetossserver",
        "resourceType": "Microsoft.DBforPostgreSQL/servers",
        "resourceLocation": "westus",
        "resourceUri": "",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
      },
      "datasourceAuthCredentials": {
        "objectType": "SecretStoreBasedAuthCredentials",
        "secretStoreResource": {
          "secretStoreType": "AzureKeyVault",
          "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
          "value": null
        }
      },
      "restoreLocation": "westus"
    },
    "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b",
    "rehydration_priority": "Standard",
    "rehydration_retention_duration": "P15D",
  }
}
```

#### <a name="restore-as-files"></a>파일로 복원

[위에서](#set-up-permissions)설명한 대로 권한이 할당된 스토리지 계정 내에서 컨테이너의 URI를 가져옵니다. 예를 들어 다른 구독을 가진 스토리지 계정 **testossstorageaccount에** 있는 **testcontainerrestore라는** 컨테이너가 있습니다.

```http
"https://testossstorageaccount.blob.core.windows.net/testcontainerrestore"
```

```json
{
  "objectType": "ValidateRestoreRequestObject",
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "VaultStore",
    "restoreTargetInfo": {
      "targetDetails": {
        "url": "https://testossstorageaccount.blob.core.windows.net/testcontainerrestore",
        "filePrefix": "empdb11_postgresql-westus_1628853549768",
        "restoreTargetLocationType": "AzureBlobs"
      },
      "restoreLocation": "westus",
      "recoveryOption": "FailIfExists",
      "objectType": "RestoreFilesTargetInfo"
    },
    "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b"
  }
}
```

보관 기반 복구 지점의 경우 원본 데이터 저장소를 수정하고, 아래 설명된 대로 리하위드 복구 지점의 리하이드레이션 우선 순위 및 보존 기간(일)을 추가합니다.

```json
{
  "objectType": "ValidateRestoreRequestObject",
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "ArchiveStore",
    "restoreTargetInfo": {
      "targetDetails": {
        "url": "https://testossstorageaccount.blob.core.windows.net/testcontainerrestore",
        "filePrefix": "empdb11_postgresql-westus_1628853549768",
        "restoreTargetLocationType": "AzureBlobs"
      },
      "restoreLocation": "westus",
      "recoveryOption": "FailIfExists",
      "objectType": "RestoreFilesTargetInfo"
    },
    "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b",
    "rehydration_priority": "Standard",
    "rehydration_retention_duration": "P15D",
  }
}
```

#### <a name="validate-restore-requests"></a>복원 요청 유효성 검사

요청 본문이 준비되면 [API 복원에 대한 유효성 검사를](/rest/api/dataprotection/backup-instances/validate-for-restore)사용하여 유효성을 검사합니다. 백업 API에 대한 유효성 검사와 마찬가지로 *POST* 작업입니다.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/validateRestore?api-version=2021-07-01
```

예를 들어 이 API는 다음으로 변환됩니다.

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149/ValidateRestore?api-version=2021-07-01"
```

이 POST API의 요청 본문에 대해 [자세히 알아보세요.](/rest/api/dataprotection/backup-instances/validate-for-restore#request-body)

##### <a name="request-body-to-validate-restore-request"></a>복원 요청의 유효성을 검사하기 위한 요청 본문

위의 섹션에서 동일한 의 [섹션을](#create-a-request-body-for-restore-operations)생성했습니다. 이제 개체 형식을 추가하고 이를 사용하여 유효성 검사 작업을 트리거합니다.

```json
{
    "objectType": "ValidateRestoreRequestObject",
    "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b",
    "restoreRequestObject": {
      "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
      "sourceDataStoreType": "VaultStore",
      "restoreTargetInfo": {
        "objectType": "restoreTargetInfo",
        "recoveryOption": "FailIfExists",
        "dataSourceInfo": {
          "objectType": "Datasource",
          "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
          "resourceName": "emprestored21",
          "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
          "resourceLocation": "westus",
          "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
          "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
        },
        "dataSourceSetInfo": {
          "objectType": "DatasourceSet",
          "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver",
          "resourceName": "targetossserver",
          "resourceType": "Microsoft.DBforPostgreSQL/servers",
          "resourceLocation": "westus",
          "resourceUri": "",
          "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
        },
        "datasourceAuthCredentials": {
          "objectType": "SecretStoreBasedAuthCredentials",
          "secretStoreResource": {
            "secretStoreType": "AzureKeyVault",
            "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
            "value": null
          }
        },
        "restoreLocation": "westus"
      }
  }
}
```

##### <a name="response-to-validate-restore-requests"></a>복원 요청 유효성 검사에 대한 응답

_유효성 검사 복원 요청은_ [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 따라서 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

다른 작업이 생성될 때 202(수락됨), 해당 작업이 완료될 때 200(정상)인 두 개의 응답이 반환됩니다.

|이름  |Type  |설명  |
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
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-07-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: bae60c92-669d-45a4-aed9-8392cca7cc8d
x-ms-routing-request-id: CENTRALUSEUAP:20210708T205935Z:f51db7a4-9826-4084-aa3b-ae640dc78af6
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 20:59:35 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-07-01
X-Powered-By: ASP.NET
```

간단한 GET 요청으로 _Azure-AsyncOperation_ 헤더를 *추적합니다.* 요청이 성공하면 상태 응답과 함께 200(정상)을 반환합니다.

```http
GET https://management.azure.com/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/providers/Microsoft.DataProtection/locations/westus/operationStatus/YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzY4NDNmZWZkLWU4ZTMtNDM4MC04ZTJhLWUzMTNjMmNhNjI1NA==?api-version=2021-07-01
```

```http
{
  "id": "/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/providers/Microsoft.DataProtection/locations/westus/operationStatus/YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzY4NDNmZWZkLWU4ZTMtNDM4MC04ZTJhLWUzMTNjMmNhNjI1NA==",
  "name": "YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzY4NDNmZWZkLWU4ZTMtNDM4MC04ZTJhLWUzMTNjMmNhNjI1NA==",
  "status": "Inprogress",
  "startTime": "2021-10-22T20:22:41.0305623Z",
  "endTime": "0001-01-01T00:00:00Z"
}
```

응답은 복원 요청을 제출하기 전에 해결해야 하는 오류를 나타냅니다. 다음 예에서는 대상 데이터베이스의 버전이 더 낮으므로 복원할 수 없는 경우를 나타냅니다.

```http
---------- Response (1892 ms) ------------

HTTP/1.1 200 OK
Content-Length: 1236
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 11999
x-ms-correlation-request-id: 784764f8-941d-4f05-8d8c-c02d2c05f799
x-ms-routing-request-id: SOUTHINDIA:20211022T202725Z:e109a061-a09e-4f13-acd0-9b9833f851ac
Cache-Control: no-cache
Date: Fri, 22 Oct 2021 20:27:25 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/providers/Microsoft.DataProtection/locations/westus/operationStatus/YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzY4NDNmZWZkLWU4ZTMtNDM4MC04ZTJhLWUzMTNjMmNhNjI1NA==",
  "name": "YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzY4NDNmZWZkLWU4ZTMtNDM4MC04ZTJhLWUzMTNjMmNhNjI1NA==",
  "status": "Failed",
  "error": {
    "additionalInfo": [
      {
        "type": "UserFacingError",
        "info": {
          "message": "Restoring backups of a higher PostgreSQL version to a lower version is not supported.",
          "recommendedAction": [
            "Restore to the same or a higher PostgreSQL version from which the backup was taken."
          ],
          "code": "UserErrorRestoreToLowerVersion",
          "target": "",
          "innerError": {
            "code": "InnerErrorCodeUnavailable",
            "additionalInfo": {
              "DetailedNonLocalisedMessage": "Restoring backup from version:10 of PostgreSQL to 9.5 of PostgreSQL not supported, as the restore server version is lower."
            }
          },
          "isRetryable": false,
          "isUserError": false,
          "properties": {
            "ActivityId": "2a23524f-0217-4bc1-bbe8-1546d2e6204d-Ibz"
          }
        }
      }
    ],
    "code": "UserErrorRestoreToLowerVersion",
    "message": "Restoring backups of a higher PostgreSQL version to a lower version is not supported."
  },
  "startTime": "2021-10-22T20:22:41.0305623Z",
  "endTime": "2021-10-22T20:23:11Z"
}
```

오류를 수정하고 요청의 유효성을 다시 검사하면 200(정상)은 성공 응답을 반환합니다.

```http
HTTP/1.1 200 OK
Content-Length: 443
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 11999
x-ms-correlation-request-id: 61d62dd8-8e1a-473c-bcc6-c6a7a19fb035
x-ms-routing-request-id: SOUTHINDIA:20211022T203846Z:89af04a6-4e91-4b64-8998-a369dc763408
Cache-Control: no-cache
Date: Fri, 22 Oct 2021 20:38:46 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/providers/Microsoft.DataProtection/locations/westus/operationStatus/YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzU0NDI4YzdhLTJjNWEtNDNiOC05ZjBjLTM2NmQ3ZWVjZDUxOQ==",
  "name": "YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzU0NDI4YzdhLTJjNWEtNDNiOC05ZjBjLTM2NmQ3ZWVjZDUxOQ==",
  "status": "Succeeded",
  "startTime": "2021-10-22T20:28:24.3820169Z",
  "endTime": "2021-10-22T20:28:49Z"
}
```

#### <a name="trigger-restore-requests"></a>복원 요청 트리거

트리거 복원 작업은 ***POST*** API입니다. 트리거 복원 작업에 대해 [자세히 알아봅니다.](/rest/api/dataprotection/backup-instances/trigger-restore)

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/restore?api-version=2021-07-01
```

예를 들어 API는 다음으로 변환됩니다.

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149/restore?api-version=2021-07-01"
```

##### <a name="create-a-request-body-for-restore-operations"></a>복원 작업에 대한 요청 본문 만들기

요청의 유효성이 검사되면 동일한 요청 본문을 사용하여 사소한 변경으로 _복원 요청을_ 트리거합니다.

###### <a name="example-request-body-for-restore"></a>복원에 대한 예제 요청 본문

_유효성 검사 복원 요청_ 본문에서 유일한 변경 내용은 시작 시 _restoreRequest_ 개체를 제거하는 것입니다.

```json
{
  "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
  "sourceDataStoreType": "VaultStore",
  "restoreTargetInfo": {
    "objectType": "restoreTargetInfo",
    "recoveryOption": "FailIfExists",
    "dataSourceInfo": {
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
      "resourceName": "emprestored21",
      "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
      "resourceLocation": "westus",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
      "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
    },
    "dataSourceSetInfo": {
      "objectType": "DatasourceSet",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver",
      "resourceName": "targetossserver",
      "resourceType": "Microsoft.DBforPostgreSQL/servers",
      "resourceLocation": "westus",
      "resourceUri": "",
      "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
    },
    "datasourceAuthCredentials": {
      "objectType": "SecretStoreBasedAuthCredentials",
      "secretStoreResource": {
        "secretStoreType": "AzureKeyVault",
        "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
        "value": null
      }
    },
    "restoreLocation": "westus"
  },
  "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b"
}
```

#### <a name="response-to-trigger-restore-requests"></a>복원 요청 트리거에 대한 응답

_트리거 복원 요청은_ [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 따라서 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

다른 작업이 생성될 때 202(수락됨), 해당 작업이 완료될 때 200(정상)인 두 개의 응답이 반환됩니다.

|이름  |Type  |설명  |
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
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-07-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1197
x-ms-correlation-request-id: 8661209c-5b6a-44fe-b676-4e2b9c296593
x-ms-routing-request-id: CENTRALUSEUAP:20210708T204652Z:69e3fa4b-c5d9-4601-9410-598006ada187
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 20:46:52 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-07-01
X-Powered-By: ASP.NET
```

간단한 GET 요청으로 _Azure-AsyncOperation_ 헤더를 *추적합니다.* 요청이 성공하면 복원 요청 완료를 위해 추가로 추적해야 하는 작업 ID가 있는 200(정상)을 반환합니다.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-07-01

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

복원 요청 트리거는 복원 작업을 _트리거합니다._ 결과 작업 ID를 추적하려면 [GET Jobs API](/rest/api/dataprotection/jobs/get)를 사용합니다.

간단한 *GET* 명령을 사용하여 위의 [트리거 복원 응답에](#example-response-to-trigger-restore-request) 있는 _JobId를_ 추적합니다.

```http
 GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852?api-version=2021-07-01
```

위에서 언급한 작업 상태는 복원 작업이 완료되었음을 나타냅니다.

## <a name="next-steps"></a>다음 단계

- [Azure PostgreSQL Backup 개요](backup-azure-database-postgresql-overview.md)