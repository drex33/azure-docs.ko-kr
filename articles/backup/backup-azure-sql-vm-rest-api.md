---
title: REST API 통해 Azure Backup 사용하여 Azure VM에서 SQL 서버 데이터베이스 백업
description: REST API 사용하여 Recovery Services 자격 증명 모음의 Azure VM에서 SQL 서버 데이터베이스를 백업하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 11/30/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 65f16a1b5614d68f03842f9647474ccdf050f2f9
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133289570"
---
# <a name="back-up-sql-server-databases-in-azure-vms-using-azure-backup-via-rest-api"></a>REST API 통해 Azure Backup 사용하여 Azure VM에서 SQL 서버 데이터베이스 백업

이 문서에서는 REST API 통해 Azure Backup 사용하여 Azure VM에서 SQL 서버 데이터베이스를 백업하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

- Recovery Services 자격 증명 모음
- SQL 데이터베이스에 대한 백업을 구성하는 정책입니다.

새 자격 증명 모음 및 정책을 만드는 방법에 대한 자세한 내용은 자격 증명 모음 [만들기](backup-azure-arm-userestapi-createorupdatevault.md) 및 정책 REST API 자습서 [만들기를](backup-azure-arm-userestapi-createorupdatepolicy.md) 참조하세요.

다음 리소스를 사용합니다.

- **Recovery Services 자격 증명 모음:** *SQLServer2012*
- **정책:** *HourlyLogBackup*
- **리소스 그룹:** *SQLServerSelfHost*

## <a name="configure-backup-for-unprotected-sql-server-databases-in-azure-vm"></a>Azure VM에서 보호되지 않는 SQL 서버 데이터베이스에 대한 백업 구성

### <a name="discover-unprotected-sql-server-databases"></a>보호되지 않는 SQL Server 데이터베이스 검색

자격 증명 모음은 Recovery Services 자격 증명 모음에 백업할 수 있는 SQL 데이터베이스를 사용하여 구독의 모든 Azure VM을 검색해야 합니다. 세부 정보를 가져오려면 [새로 고침 작업을](/rest/api/backup/protectioncontainers/refresh)트리거합니다. 자격 증명 모음이 현재 구독에서 보호되지 않은 모든 SQL 데이터베이스의 최신 목록을 수신하고 캐시하도록 하는 비동기 *POST* *작업입니다.* 데이터베이스가 *캐시되면* 복구 서비스에서 데이터베이스에 액세스하여 보호할 수 있습니다.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{vaultResourceGroupName}/providers/microsoft.recoveryservices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

*POST URI에는* `{subscriptionId}` , , 및 매개 `{vaultName}` `{vaultresourceGroupName}` `{fabricName}` 변수가 있습니다. 다음 예제에서 다른 매개 변수의 값은 다음과 같습니다.

- `{fabricName}`: *Azure*
- `{vaultName}`: *SQLServer2012*
- `{vaultresourceGroupName}`: *SQLServerSelfHost*
- `$filter`: *backupManagementType eq 'AzureWorkload'*

필요한 모든 매개 변수가 URI3에 제공되기 때문에 별도의 요청 본문이 필요하지 않습니다.

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureWorkload'
```

#### <a name="responses-to-the-refresh-operation"></a>새로 고침 작업에 대한 응답

*새로 고침* 작업은 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

다른 작업을 만들 때 202(수락됨)와 해당 작업이 완료되면 200(정상)의 두 응답을 반환합니다.

##### <a name="example-responses-to-the-refresh-operation"></a>새로 고침 작업에 대한 응답의 예

*POST* 요청을 제출하면 202(수락됨) 응답이 반환됩니다.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
X-Content-Type-Options: nosniff
x-ms-request-id: a85ee4a2-56d7-4477-b29c-d140a8bb90fe
x-ms-client-request-id: 4653a4ed-ffbe-4492-ae7d-3e1ab03722af; 4653a4ed-ffbe-4492-ae7d-3e1ab03722af
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: a85ee4a2-56d7-4477-b29c-d140a8bb90fe
x-ms-routing-request-id: SOUTHINDIA:20180528T075517Z:a85ee4a2-56d7-4477-b29c-d140a8bb90fe
Cache-Control: no-cache
Date: Mon, 28 May 2018 07:55:16 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/operationResults/a60bfc5e-e237-4ead-be5c-b845e9566ea8?api-version=2016-12-01
X-Powered-By: ASP.NET
```

간단한 *GET* 명령과 함께 *Location* 헤더를 사용하여 결과 작업을 추적합니다.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/operationResults/a60bfc5e-e237-4ead-be5c-b845e9566ea8?api-version=2016-12-01
```

모든 SQL 데이터베이스가 검색되면 *GET* 명령은 200(콘텐츠 없음) 응답을 반환합니다. 이제 자격 증명 모음은 구독 내에서 백업할 수 있는 SQL 데이터베이스가 있는 모든 VM을 검색할 수 있습니다.

```http
HTTP/1.1 204 NoContent
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 55ae46bb-0d61-4284-a408-bcfaa36af643
x-ms-client-request-id: b5ffa56f-a521-48a4-91b2-e3bc1e3f1110; b5ffa56f-a521-48a4-91b2-e3bc1e3f1110
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14968
x-ms-correlation-request-id: 55ae46bb-0d61-4284-a408-bcfaa36af643
x-ms-routing-request-id: SOUTHINDIA:20180528T075607Z:55ae46bb-0d61-4284-a408-bcfaa36af643
Cache-Control: no-cache
Date: Mon, 28 May 2018 07:56:06 GMT
X-Powered-By: ASP.NET
```

### <a name="list-vms-with-sql-databases-to-back-up-with-recovery-services-vault"></a>Recovery Services 자격 증명 모음으로 백업할 SQL 데이터베이스가 있는 VM 나열

*캐싱이* 완료된 것을 확인하려면 Recovery Services 자격 증명 모음으로 백업할 수 있는 SQL 데이터베이스를 사용하여 구독의 모든 VM을 나열합니다. 그런 다음, 응답에서 원하는 스토리지 계정을 찾습니다. 이 작업은 GET [ProtectableContainers](/rest/api/backup/protectablecontainers/list) 작업을 사용하여 수행합니다.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureWorkload'
```

>[!Note]
>*GET* URI에는 필요한 모든 매개 변수가 있습니다. 추가 요청 본문이 필요없습니다.

응답 본문의 예:

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectableContainers/VMAppContainer;Compute;SQLServerPMDemo;ad-primary-dc",
      "name": "VMAppContainer;Compute;SQLServerPMDemo;ad-primary-dc",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",
      "properties": {
        "friendlyName": "ad-primary-dc",
        "backupManagementType": "AzureWorkload",
        "protectableContainerType": "VMAppContainer",
        "healthStatus": "Healthy",
        "containerId": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerPMDemo/providers/Microsoft.Compute/virtualMachines/ad-primary-dc"
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectableContainers/VMAppContainer;Compute;SQLServerPMDemo;ad-secondry-dc",
      "name": "VMAppContainer;Compute;SQLServerPMDemo;ad-secondry-dc",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",
      "properties": {
        "friendlyName": "ad-secondry-dc",
        "backupManagementType": "AzureWorkload",
        "protectableContainerType": "VMAppContainer",
        "healthStatus": "Healthy",
        "containerId": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerPMDemo/providers/Microsoft.Compute/virtualMachines/ad-secondry-dc"
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectableContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0",
      "name": "VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",
      "properties": {
        "friendlyName": "sqlserver-0",
        "backupManagementType": "AzureWorkload",
        "protectableContainerType": "VMAppContainer",
        "healthStatus": "Healthy",
        "containerId": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerPMDemo/providers/Microsoft.Compute/virtualMachines/sqlserver-0"
      }
    }
  ]
}
```

응답 본문에서 친숙한 이름을 가진 VM을 찾을 수 있으므로 위에서 수행한 새로 고침 작업이 성공했습니다. Recovery Services 자격 증명 모음은 이제 동일한 구독에서 보호되지 않는 SQL 데이터베이스가 있는 VM을 성공적으로 검색할 수 있습니다.

### <a name="register-vms-with-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 VM 등록

Azure Backup 서비스가 VM 내의 SQL 데이터베이스와 상호 작용할 수 있도록 Recovery Services 자격 증명 모음에 VM을 등록해야 *합니다(이름* 필드의 값을 사용하여 Azure VM 컨테이너 식별). 보호 가능한 컨테이너 목록 결과의 속성 백에서 HTTP 요청 본문을 가져오려면 JSON 요청에 값을 제공해야 합니다.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.recoveryservices/vaults/{vaultName}/backupFabrics/Azure/protectionContainers/{containerName}?api-version=2016-12-01
```

URI에 대한 변수를 다음과 같이 설정합니다.

- `{resourceGroupName}` - *SQLServerSelfHost*
- `{fabricName}` - *Azure*
- `{vaultName}` - *SQLServer2012*
- `{containerName}` - *GET ProtectableContainers* 작업의 응답 본문에 있는 이름 특성입니다. 이 예제에서 특성 이름은 *VMAppContainer입니다. 컴퓨팅; SQLServerPMDemo;sqlserver-0*.

>[!NOTE]
>항상 응답의 name 특성을 이 요청에 입력합니다. container-name 형식을 하드 코딩하거나 생성하지 마십시오. container-name 형식을 만들거나 하드 코딩하면 나중에 형식이 바뀔 때 API 호출이 실패합니다.

<br>

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0?api-version=2016-12-01
```

요청 본문 만들기는 다음과 같습니다.

```json
{
  "properties": {
    "backupManagementType": "AzureWorkload",
    "friendlyName": "sqlserver-0",
    "containerType": "VMAppContainer",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectableContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0",
    "workloadType": "SQLDataBase"
  }
}
```

요청 본문의 전체 정의 목록 및 기타 세부 정보는 [ProtectionContainers-Register를 참조하세요.](/rest/api/backup/protectioncontainers/register#azurestoragecontainer)

이는 비동기 작업이며 작업이 수락되면 202(수락됨) 및 작업이 완료되면 200(정상)의 두 응답을 반환합니다.  작업 상태를 추적하려면 location 헤더를 사용하여 작업의 최신 상태를 가져옵니다.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0/operationResults/2a72d206-b4d8-4c59-89ef-ef3283132237?api-version=2016-12-01
```

작업 완료 시 요청 본문의 예:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0",
  "name": "VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers",
  "properties": {
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerPMDemo/providers/Microsoft.Compute/virtualMachines/sqlserver-0",
    "lastUpdatedTime": "2018-05-28T08:33:14.7304852Z",
    "extendedInfo": {
      "hostServerName": "sqlserver-0.shopkart.com",
      "inquiryInfo": {
        "status": "Success",
        "errorDetail": {
          "code": "Success",
          "message": "",
          "recommendations": [
            ""
          ]
        },
        "inquiryDetails": [
          {
            "type": "SQL",
            "itemCount": 5,
            "inquiryValidation": {
              "status": "Success",
              "errorDetail": {
                "code": "Success",
                "message": "",
                "recommendations": [
                  ""
                ]
              }
            }
          }
        ]
      }
    },
    "friendlyName": "sqlserver-0",
    "backupManagementType": "AzureWorkload",
    "registrationStatus": "Registered",
    "healthStatus": "Healthy",
    "containerType": "VMAppContainer",
    "protectableObjectType": "VMAppContainer"
  }
}
```

응답 본문의 *registrationstatus* 매개 변수 값에서 등록이 성공했는지 확인할 수 있습니다. 이 경우 *SQLServer2012* 에 등록된 상태로 표시됩니다. 따라서 등록 작업이 성공했습니다.

### <a name="inquire-all-unprotected-sql-databases-under-a-vm"></a>VM에서 보호되지 않는 모든 SQL 데이터베이스 조회

스토리지 계정의 보호 가능한 항목에 대해 문의하려면 [보호 컨테이너 조회](/rest/api/backup/protectioncontainers/inquire) 작업을 사용합니다. 이는 비동기 작업이며 location 헤더를 사용하여 결과를 추적해야 합니다.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01$filter={$filter}
```

위의 URI에 대한 변수를 다음과 같이 설정합니다.

- `{resourceGroupName}`: *SQLServerSelfHost*
- `{vaultName}`: *SQLServer2012*
- `{fabricName}`: *Azure*
- `{containerName}`: GET ProtectableContainers 작업의 응답 본문에서 name 특성을 참조하세요. 이 예제에서 특성 이름은 *VMAppContainer입니다. 컴퓨팅; SQLServerPMDemo;sqlserver-0*.

```http
POST https://management.azure.com/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0/inquire?api-version=2016-12-01$filter=workloadType EQ 'SQLDatabase'
```

요청이 성공하면 상태 코드 *OK* 를 반환합니다.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
X-Content-Type-Options: nosniff
x-ms-request-id: 50295ae9-3d5b-48d1-8a6d-a0acb6d06b98
x-ms-client-request-id: 4174f98a-80b9-4747-9500-6f702ed83930; 4174f98a-80b9-4747-9500-6f702ed83930
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1197
x-ms-correlation-request-id: 50295ae9-3d5b-48d1-8a6d-a0acb6d06b98
x-ms-routing-request-id: SOUTHINDIA:20180528T084628Z:50295ae9-3d5b-48d1-8a6d-a0acb6d06b98
Cache-Control: no-cache
Date: Mon, 28 May 2018 08:46:28 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0/operationResults/f0751ec2-445a-4d0e-a6a5-a19957459655?api-version=2016-12-01
X-Powered-By: ASP.NET
```

### <a name="select-the-databases-you-want-to-back-up"></a>백업하려는 데이터베이스 선택

구독 아래의 모든 보호 가능한 항목을 나열하고 백업할 데이터베이스를 찾으려면 [GET backupprotectableItems](/rest/api/backup/backupprotectableitems/list) 작업을 사용합니다.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

다음과 같이 URI를 생성합니다.

- `{resourceGroupName}`: *SQLServerSelfHost*
- `{vaultName}`: *SQLServer2012*
- `{$filter}`: *backupManagementType eq 'AzureWorkload'*

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureWorkload'
```

샘플 응답:

```json
Status Code:200

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;SQLServerSelfHost;SQLServersql2012/protectableItems/sqldatabase;mssqlserver;msdb",
      "name": "sqldatabase;mssqlserver;msdb",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
      "properties": {
        "parentName": "MSSQLSERVER",
        "serverName": "SQLServersql2012",
        "isAutoProtectable": false,
        "subinquireditemcount": 0,
        "subprotectableitemcount": 0,
        "backupManagementType": "AzureWorkload",
        "workloadType": "SQL",
        "protectableItemType": "SQLDataBase",
        "friendlyName": "msdb",
        "protectionState": "NotProtected"
      }
    }
]
}
```

응답에는 보호되지 않는 모든 데이터베이스 목록이 포함되며 Azure Recovery Service에서 백업을 구성하는 데 필요한 모든 정보가 포함됩니다. 나중에 사용할 데이터베이스 이름을 저장합니다.

### <a name="enable-backup-for-the-database"></a>데이터베이스에 백업 사용

관련 데이터베이스를 *식별한* 후 이름을 지정합니다.

1. 보호할 정책을 선택합니다.
1. [자격 증명 모음에 기존 정책을 나열하고 정책 API 목록을 참조하세요.](/rest/api/backup/backuppolicies/list)
1. 정책 이름을 참조하여 [관련](/rest/api/backup/protectionpolicies/get) 정책을 선택합니다.
1. [정책 자습서](./backup-azure-arm-userestapi-createorupdatepolicy.md)를 만듭니다.

보호를 사용하도록 설정하는 것은 *보호된 항목* 를 만드는 비동기 *PUT* 작업입니다.

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/{containerName};sqlserver-0/protectedItems/{protectedItemName}?api-version=2016-12-01
```

*GET backupprotectableitems* 작업의 응답 본문에서 ID 특성을 사용하여 **containerName** 및 **protectedItemName** 변수를 설정합니다.

이 예제에서 보호하려는 파일 공유의 ID는 다음과 같습니다.

```
/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0/protectedItems/sqldatabase;mssqlserver;msdb
```

- `{containerName}`: *VMAppContainer; 컴퓨팅; SQLServerPMDemo;sqlserver-0*
- `{protectedItemName}`: *sqldatabase;mssqlserver;msdb*

요청 본문을 만듭니다.

다음 요청 본문은 보호된 항목을 만드는 데 필요한 속성을 정의합니다.

```json
{
  "properties": {
    "backupManagementType": "AzureWorkload",
    "workloadType": "SQLDataBase",
    "policyId": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupPolicies/HourlyLogBackup"
  },
  "location": "westcentralus"
}
```
보호된 항목 만들기 또는 업데이트를 위한 *PUT* 요청을 제출하면 초기 응답은 위치 헤더를 포함하는 202(수락됨)입니다.

샘플 응답

보호된 항목을 만드는 작업은 비동기 작업으로서 추적이 필요한 다른 작업을 만듭니다. 다른 작업을 만들 때 202(수락됨)와 해당 작업이 완료되면 200(정상)의 두 응답을 반환합니다.

보호된 항목 만들기 또는 업데이트를 위한 *PUT* 요청을 제출하면 초기 응답은 위치 헤더를 포함하는 202(수락됨)입니다.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0/protectedItems/sqldatabase;mssqlserver;msdb/operationsStatus/b686a165-387f-461d-8579-c55338566338?api-version=2016-12-01
X-Content-Type-Options: nosniff
x-ms-request-id: ab6a8c6c-ab90-433a-8dc2-5194901d428d
x-ms-client-request-id: 7d03bcef-562a-4ddc-8086-a3f4981be915; 7d03bcef-562a-4ddc-8086-a3f4981be915
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: ab6a8c6c-ab90-433a-8dc2-5194901d428d
x-ms-routing-request-id: SOUTHINDIA:20180528T102112Z:ab6a8c6c-ab90-433a-8dc2-5194901d428d
Cache-Control: no-cache
Date: Mon, 28 May 2018 10:21:12 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0/protectedItems/sqldatabase;mssqlserver;msdb/operationResults/b686a165-387f-461d-8579-c55338566338?api-version=2016-12-01
X-Powered-By: ASP.NET
```

그런 다음, *GET* 명령으로 위치 헤더 또는 Azure-AsyncOperation 헤더를 사용하여 결과 작업을 추적합니다.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0/protectedItems/sqldatabase;mssqlserver;msdb/operationResults/b686a165-387f-461d-8579-c55338566338?api-version=2016-12-01
```

작업이 완료되면 응답 본문에 보호된 항목 콘텐츠가 있는 200(정상)을 반환합니다.

샘플 응답 본문:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0/protectedItems/SQLDataBase;mssqlserver;msdb",
  "name": "SQLDataBase;mssqlserver;msdb",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems",
  "properties": {
    "friendlyName": "msdb",
    "serverName": "sqlserver-0.shopkart.com",
    "parentName": "MSSQLSERVER",
    "parentType": "AzureVmWorkloadSQLInstance",
    "protectionStatus": "Healthy",
    "protectionState": "IRPending",
    "lastBackupStatus": "IRPending",
    "lastBackupErrorDetail": {
      "code": "Success",
      "message": ""
    },
    "protectedItemDataSourceId": "17592741727863",
    "protectedItemHealthStatus": "IRPending",
    "extendedInfo": {
      "recoveryPointCount": 0,
      "policyState": "Consistent"
    },
    "protectedItemType": "AzureVmWorkloadSQLDatabase",
    "backupManagementType": "AzureWorkload",
    "workloadType": "SQLDataBase",
    "containerName": "VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerPMDemo/providers/VMAppContainer/sqlserver-0",
    "policyId": "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupPolicies/HourlyLogBackup"
  }
}
```

이렇게 하면 데이터베이스에 대한 보호가 사용하도록 설정되고 정책 일정에 따라 첫 번째 백업이 트리거됩니다.

## <a name="trigger-an-on-demand-backup-for-the-database"></a>데이터베이스에 대한 주문형 백업 트리거

백업을 위해 데이터베이스를 구성한 후에는 정책 일정에 따라 백업이 실행됩니다. 첫 번째 예약 백업을 대기하거나 언제든 주문형 백업을 트리거할 수 있습니다.

주문형 백업의 트리거는 *POST* 작업입니다.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

{containerName} 및 {protectedItemName}이 백업을 사용하도록 설정하는 동안 위와 같이 생성됩니다. 예를 들어 이렇게 하면 다음으로 변환됩니다.

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/Microsoft.RecoveryServices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0/protectedItems/sqldatabase;mssqlserver;msdb/backup?api-version=2016-12-01
```

### <a name="create-request-body"></a>요청 본문 만들기

다음 요청 본문을 사용하여 주문형 전체 백업을 만듭니다.

```json
{
  "properties": {
    "objectType": "AzureWorkloadBackupRequest",
    "backupType": "Full"
  }
}
```

### <a name="responses-to-the-on-demand-backup-operation"></a>주문형 백업 작업에 대한 응답

주문형 백업의 트리거는 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

다른 작업을 만들 때 202(수락됨)와 해당 작업이 완료되면 200(정상)이라는 두 개의 응답을 반환합니다.

### <a name="example-responses-to-the-on-demand-backup-operation"></a>주문형 백업 작업에 대한 응답의 예

주문형 백업에 대한 *POST* 요청을 제출하면 초기 응답은 위치 헤더 또는 Azure-async-header를 사용한 202(수락됨)입니다.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0/protectedItems/sqldatabase;mssqlserver;msdb/operationsStatus/cd2a3b13-d392-4e81-86ac-02ea91cc70b9?api-version=2016-12-01
X-Content-Type-Options: nosniff
x-ms-request-id: a691e2a9-8203-462d-a4da-d1badde22f83
x-ms-client-request-id: 6b033cf6-f875-4c03-8985-9add07ec2845; 6b033cf6-f875-4c03-8985-9add07ec2845
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: a691e2a9-8203-462d-a4da-d1badde22f83
x-ms-routing-request-id: SOUTHINDIA:20180528T114321Z:a691e2a9-8203-462d-a4da-d1badde22f83
Cache-Control: no-cache
Date: Mon, 28 May 2018 11:43:21 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0/protectedItems/sqldatabase;mssqlserver;msdb/operationResults/cd2a3b13-d392-4e81-86ac-02ea91cc70b9?api-version=2016-12-01
X-Powered-By: ASP.NET
```

그런 다음, *GET* 명령으로 위치 헤더 또는 Azure-AsyncOperation 헤더를 사용하여 결과 작업을 추적합니다.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/SQLServerSelfHost/providers/microsoft.recoveryservices/vaults/SQLServer2012/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SQLServerPMDemo;sqlserver-0/protectedItems/sqldatabase;mssqlserver;msdb/operationsStatus/cd2a3b13-d392-4e81-86ac-02ea91cc70b9?api-version=2016-12-01
```

작업이 완료되면 응답 본문에 결과 백업 작업의 ID와 함께 200(정상)을 반환합니다.

#### <a name="sample-response-body"></a>샘플 응답 본문

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

백업 작업은 장기 실행 작업이기 때문에 [REST API 문서를 사용하여 모니터 작업에](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)설명된 대로 추적해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Rest API를 사용하여 SQL 데이터베이스를 복원하는](restore-azure-sql-vm-rest-api.md)방법을 알아봅니다.



