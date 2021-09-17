---
title: REST API를 사용하여 Azure 파일 공유 백업
description: REST API를 사용하여 Recovery Services 자격 증명 모음에서 Azure 파일 공유를 백업하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 152613031eb6c3ba7a0c1078d3d53bcf170ce646
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123424171"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>REST API를 통해 Azure Backup을 사용하여 Azure 파일 공유 백업

이 문서에서는 REST API를 통해 Azure Backup을 사용하여 Azure 파일 공유를 백업하는 방법을 설명합니다.

이 문서에서는 파일 공유의 백업을 구성하기 위해 Recovery Services 자격 증명 모음 및 정책을 이미 만들었다고 가정합니다. 이미 만들어 놓지 않은 경우라면 새 자격 증명 모음 및 정책 만들기를 위한 [자격 증명 모음 만들기](./backup-azure-arm-userestapi-createorupdatevault.md) 및 [정책 만들기](./backup-azure-arm-userestapi-createorupdatepolicy.md) REST API 자습서를 참조하세요.

이 문서에서는 다음 리소스를 사용합니다.

- **RecoveryServicesVault**: *azurefilesvault*

- **정책:** *schedule1*

- **리소스 그룹**: *azurefiles*

- **스토리지 계정**: *testvault2*

- **파일 공유**: *testshare*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>REST API를 사용하여 보호되지 않는 Azure 파일 공유에 대한 백업 구성

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>보호되지 않는 Azure 파일 공유를 사용하여 스토리지 계정 검색

자격 증명 모음은 Recovery Services 자격 증명 모음에 백업할 수 있는 파일 공유를 사용하여 구독의 모든 Azure storage 계정을 검색해야 합니다. [새로 고침 작업](/rest/api/backup/protection-containers/refresh)을 사용하여 이 자격 증명 모음을 트리거합니다. 이 작업은 비동기 *POST* 작업으로서 자격 증명 모음이 현재 구독의 모든 보호되지 않는 Azure 파일 공유의 최신 목록을 가져오고 해당 공유 파일을 ‘캐시’해야 합니다. 파일 공유가 '캐시'되면 Recovery Services에서 파일 공유에 액세스하여 보호할 수 있습니다.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

POST URI에는 `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}`, `{fabricName}` 매개 변수가 있습니다. 이 예제에서 이 매개 변수들에 대한 값은 다음과 같습니다.

- `{fabricName}`은 *Azure* 입니다.

- `{vaultName}`은 *azurefilesvault* 입니다.

- `{vaultresourceGroupName}`은 *azurefiles* 입니다.

- $filter=backupManagementType eq 'AzureStorage'

모든 필수 매개 변수가 URI에서 지정되므로 별도 요청 본문이 필요 없습니다.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses-to-the-refresh-operation"></a>새로 고침 작업에 대한 응답

'새로 고침' 작업은 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

이 작업은 다른 작업을 만드는 경우 202(수락됨), 해당 작업이 완료되는 경우 200(정상)의 두 응답을 반환합니다.

##### <a name="example-responses-to-the-refresh-operation"></a>새로 고침 작업에 대한 응답의 예

*POST* 요청을 제출하면 202(수락됨) 응답이 반환됩니다.

```http
HTTP/1.1 202 Accepted
'Pragma': 'no-cache'
'Expires': '-1'
'Location': ‘https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/ResourceGroups
/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/
cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01’
'Retry-After': '60'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-client-request-id': ‘3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61’
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11996'
'x-ms-correlation-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-routing-request-id': CENTRALUSEUAP:20200203T091326Z:6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'Date': 'Mon, 03 Feb 2020 09:13:25 GMT'
```

간단한 *GET* 명령으로 “위치” 헤더를 사용하여 결과 작업 추적

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

모든 Azure Storage 계정이 검색되면 GET 명령은 204(콘텐츠 없음) 응답을 반환합니다. 이제 자격 증명 모음에서 구독 내에서 백업할 수 있는 파일 공유를 사용하여 스토리지 계정을 검색할 수 있습니다.

```http
HTTP/1.1 200 NoContent
Cache-Control  : no-cache
Pragma   : no-cache
X-Content-Type-Options  : nosniff
x-ms-request-id    : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security  : max-age=31536000; includeSubDomains
X-Powered-By    : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11933
x-ms-correlation-request-id   : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105304Z:d9bdb266-8349-4dbd-9688-de52f07648b2
Date   : Mon, 27 Jan 2020 10:53:04 GMT
```

### <a name="get-list-of-storage-accounts-with-file-shares-that-can-be-backed-up-with-recovery-services-vault"></a>Recovery Services 자격 증명 모음으로 백업할 수 있는 파일 공유를 사용하여 스토리지 계정 목록을 가져옵니다.

"캐싱"이 완료되었는지 확인하려면 Recovery Services 자격 증명 모음으로 백업할 수 있는 파일 공유를 사용하여 구독의 모든 스토리지 계정을 나열합니다. 그런 다음, 응답에서 원하는 스토리지 계정을 찾습니다. 이 작업은 [GET ProtectableContainers](/rest/api/backup/protectable-containers/list) 작업을 사용하여 수행합니다.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

*GET* URI에는 필요한 모든 매개 변수가 있습니다. 추가 요청 본문이 필요없습니다.

응답 본문의 예:

```json
{

  "value": [

    {

      "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers
 /Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/

protectableContainers/StorageContainer;Storage;AzureFiles;testvault2",

      "name": "StorageContainer;Storage;AzureFiles;testvault2",

      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",

      "properties": {

        "friendlyName": "testvault2",

        "backupManagementType": "AzureStorage",

        "protectableContainerType": "StorageContainer",

        "healthStatus": "Healthy",

        "containerId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/
 AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2"

      }

    }

  ]

}
```

친숙한 이름으로 응답 본문에서 *testvault2* 스토리지 계정을 찾을 수 있으므로 위에서 수행한 새로 고침 작업은 성공했습니다. 이제 Recovery Services 자격 증명 모음이 동일한 구독에서 보호되지 않는 파일 공유를 사용하여 스토리지 계정을 성공적으로 검색할 수 있습니다.

### <a name="register-storage-account-with-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 스토리지 계정 등록

이 단계는 스토리지 계정을 자격 증명 모음에 등록해 놓지 않은 경우에만 필요합니다. [ProtectionContainers-Register 작업](/rest/api/backup/protection-containers/register)을 통해 자격 증명 모음을 등록할 수 있습니다.

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

URI에 대한 변수를 다음과 같이 설정합니다.

- {resourceGroupName} - *azurefiles*
- {fabricName} - *Azure*
- {vaultName} - *azurefilesvault*
- {containerName}- GET ProtectableContainers 작업의 응답 본문에 있는 name 특성입니다.
   이 예제에서는 *StorageContainer;Storage;AzureFiles;testvault2* 입니다.

>[!NOTE]
> 항상 응답의 name 특성을 이 요청에 입력합니다. container-name 형식을 하드 코딩하거나 생성하지 마십시오. container-name 형식을 만들거나 하드 코딩하면 나중에 형식이 바뀔 때 API 호출이 실패합니다.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

요청 본문 만들기는 다음과 같습니다.

```json
{

 "properties": {


  "containerType": "StorageContainer",


  "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",


  "resourceGroup": "AzureFiles",


  "friendlyName": "testvault2",


  "backupManagementType": "AzureStorage"

 }
}
```

요청 본문 및 기타 세부 정보에 대한 전체 정의 목록은 [ProtectionContainers-Register](/rest/api/backup/protection-containers/register#azurestoragecontainer)를 참조하세요.

이것은 비동기 작업으로서 두 개의 응답, 즉 작업이 허용될 때 “202 수락됨”을, 작업이 완료될 때 “200 정상”을 반환합니다.  작업 상태를 추적하려면 location 헤더를 사용하여 작업의 최신 상태를 가져옵니다.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

작업 완료 시 요청 본문의 예:

```json
{
    "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/
protectionContainers/StorageContainer;Storage;AzureFiles;testvault2",
    "name": "StorageContainer;Storage;AzureFiles;testvault2",
    "properties": {
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
        "protectedItemCount": 0,
        "friendlyName": "testvault2",
        "backupManagementType": "AzureStorage",
        "registrationStatus": "Registered",
        "healthStatus": "Healthy",
        "containerType": "StorageContainer",
        "protectableObjectType": "StorageContainer"
    }
}
```

응답 본문의 *registrationstatus* 매개 변수 값에서 등록이 성공했는지 확인할 수 있습니다. 지금 살펴보고 있는 경우에 있어서는 *testvault2* 에 등록된 상태를 표시하므로 등록 작업이 성공했습니다.

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>스토리지 계정에서 보호되지 않는 모든 파일 공유를 조회합니다.

[Protection Containers-Inquire](/rest/api/backup/protection-containers/inquire) 작업을 사용하여 스토리지 계정의 보호 가능한 항목을 조회할 수 있습니다. 이는 비동기 작업이며 location 헤더를 사용하여 결과를 추적해야 합니다.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

위의 URI에 대한 변수를 다음과 같이 설정합니다.

- {vaultName} - *azurefilesvault*
- {fabricName} - *Azure*
- {containerName}- GET ProtectableContainers 작업의 응답 본문에 있는 name 특성을 가리킵니다. 이 예제에서는 *StorageContainer;Storage;AzureFiles;testvault2* 입니다.

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

요청이 성공적으로 완료되면 상태 코드 "정상"을 반환합니다.

```http
Cache-Control : no-cache
Pragma   : no-cache
X-Content-Type-Options: nosniff
x-ms-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security: max-age=31536000; includeSubDomains
Server  : Microsoft-IIS/10.0
X-Powered-B : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11932
x-ms-correlation-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-routing-request-id   : CENTRALUSEUAP:20200127T105305Z:68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
Date  : Mon, 27 Jan 2020 10:53:05 GMT
```

### <a name="select-the-file-share-you-want-to-back-up"></a>백업할 파일 공유 선택

구독 아래에 있는 모든 보호 가능한 항목을 나열하고 [GET backupprotectableItems](/rest/api/backup/backup-protectable-items/list) 작업을 사용하여 백업할 원하는 파일 공유를 찾을 수 있습니다.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

다음과 같이 URI를 생성합니다.

- {vaultName} - *azurefilesvault*
- {$filter} - *backupManagementType eq 'AzureStorage'*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupProtectableItems?$filter=backupManagementType eq 'AzureStorage'&api-version=2016-12-01
```

샘플 응답:

```json
Status Code:200

{
    "value": [
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afaccount1/protectableItems/azurefileshare;azurefiles1",
            "name": "azurefileshare;azurefiles1",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1",
                "parentContainerFriendlyName": "afaccount1",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "azurefiles1",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;afsresource",
            "name": "azurefileshare;afsresource",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
                "parentContainerFriendlyName": "afsaccount",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "afsresource",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare",
            "name": "azurefileshare;testshare",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
                "parentContainerFriendlyName": "testvault2",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "testshare",
                "protectionState": "NotProtected"
            }
        }
    ]
}
```

이 응답에는 보호되지 않는 모든 파일 공유 목록이 포함되며 Azure Recovery Service에서 백업을 구성하는 데 필요한 모든 정보가 포함됩니다.

### <a name="enable-backup-for-the-file-share"></a>파일 공유에 대한 백업 사용

관련 파일 공유를 친숙한 이름으로 "식별"한 후 보호할 정책을 선택합니다. 자격 증명 모음의 기존 정책에 대한 자세한 내용은 [정책 API 나열](/rest/api/backup/backup-policies/list)을 참조하세요. 그런 다음, 정책 이름을 참조하여 [관련 정책](/rest/api/backup/protection-policies/get)을 선택합니다. 정책을 만들려면 [정책 자습서 만들기](./backup-azure-arm-userestapi-createorupdatepolicy.md)를 참조하세요.

보호 사용은 '보호된 항목'을 만드는 비동기 *PUT* 작업입니다.

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

GET backupprotectableitems 작업의 응답 본문에서 ID 특성을 사용하여 **containername** 및 **protecteditemname** 변수를 설정합니다.

이 예제에서 보호하려는 파일 공유의 ID는 다음과 같습니다.

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- {containername} - *storagecontainer;storage;azurefiles;testvault2*
- {protectedItemName} - *azurefileshare;testshare*

또는 보호 컨테이너의 **name** 특성과 보호 가능한 항목 응답을 참조할 수 있습니다.

>[!NOTE]
>항상 응답의 name 특성을 이 요청에 입력합니다. 컨테이너 이름 형식 또는 보호된 항목 이름 형식을 하드 코딩하거나 만들지 마십시오. 컨테이너 이름 형식 또는 보호된 항목 이름 형식을 하드 코딩하거나 만들 경우 나중에 형식이 바뀔 때 API 호출이 실패합니다.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

요청 본문을 만듭니다.

다음 요청 본문은 보호된 항목을 만드는 데 필요한 속성을 정의합니다.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule1"
  }
}
```

**sourceResourceId** 는 GET backupprotectableItems의 응답에 있는 **parentcontainerFabricID** 입니다.

샘플 응답

보호된 항목을 만드는 작업은 비동기 작업으로서 추적이 필요한 다른 작업을 만듭니다. 이 작업은 다른 작업을 만드는 경우 202(수락됨), 해당 작업이 완료되는 경우 200(정상)의 두 응답을 반환합니다.

보호된 항목 만들기 또는 업데이트를 위한 *PUT* 요청을 제출하면 초기 응답은 위치 헤더를 포함하는 202(수락됨)입니다.

```http
HTTP/1.1 202 Accepted
Cache-Control  : no-cache
Pragma  : no-cache
Location : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
Retry-Afte  : 60
Azure-AsyncOperation  : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
X-Content-Type-Options : nosniff
x-ms-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-client-request-id: 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security : max-age=31536000; includeSubDomains
X-Powered-By  : ASP.NET
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105412Z:b55527fa-f473-4f09-b169-9cc3a7a39065
Date : Mon, 27 Jan 2020 10:54:12 GMT
```

그런 다음, *GET* 명령으로 위치 헤더 또는 Azure-AsyncOperation 헤더를 사용하여 결과 작업을 추적합니다.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

작업이 완료되면 응답 본문에서 보호된 항목 콘텐츠를 사용하여 200(정상)을 반환합니다.

샘플 응답 본문:

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

이제 파일 공유에 대해 보호가 사용 설정되고 최초 백업이 정책 일정에 따라 트리거될 예정임을 확인할 수 있습니다.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>파일 공유에 대한 주문형 백업 트리거

Azure 파일 공유가 백업용으로 구성되면 백업은 정책 일정에 따라 실행됩니다. 첫 번째 예약 백업을 대기하거나 언제든 주문형 백업을 트리거할 수 있습니다.

주문형 백업의 트리거는 POST 작업입니다.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

{containerName} 및 {protectedItemName}이 백업을 사용하도록 설정하는 동안 위와 같이 생성됩니다. 예를 들어 이렇게 하면 다음으로 변환됩니다.

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>요청 본문 만들기

주문형 백업을 트리거하려면 요청 본문의 구성 요소는 다음과 같습니다.

| 속성       | Type                       | 설명                       |
| ---------- | -------------------------- | --------------------------------- |
| 속성 | AzurefilesharebackupReques | BackupRequestResource 속성 |

요청 본문 및 기타 세부 정보에 대한 전체 정의 목록은 [보호된 항목 REST API 문서의 백업 트리거](/rest/api/backup/backups/trigger#request-body)를 참조하세요.

요청 본문 예제

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses-to-the-on-demand-backup-operation"></a>주문형 백업 작업에 대한 응답

주문형 백업의 트리거는 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

이 작업은 다른 작업을 만드는 경우 202(수락됨), 해당 작업이 완료되는 경우 200(정상)의 두 응답을 반환합니다.

### <a name="example-responses-to-the-on-demand-backup-operation"></a>주문형 백업 작업에 대한 응답의 예

주문형 백업에 대한 *POST* 요청을 제출하면 초기 응답은 위치 헤더 또는 Azure-async-header를 사용한 202(수락됨)입니다.

```http
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'Retry-After': '60'
'Azure-AsyncOperation': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040339Z:2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'Date': 'Thu, 06 Feb 2020 04:03:38 GMT'
'Content-Length': '0'
```

그런 다음, *GET* 명령으로 위치 헤더 또는 Azure-AsyncOperation 헤더를 사용하여 결과 작업을 추적합니다.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2016-12-01
```

작업이 완료되면 응답 본문에서 결과 백업 작업의 ID를 사용하여 200(정상)을 반환합니다.

#### <a name="sample-response-body"></a>샘플 응답 본문

```json
{
    "id": "dc62d524-427a-4093-968d-e951c0a0726e",
    "name": "dc62d524-427a-4093-968d-e951c0a0726e",
    "status": "Succeeded",
    "startTime": "2020-02-06T11:06:02.1327954Z",
    "endTime": "2020-02-06T11:06:02.1327954Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "39282261-cb52-43f5-9dd0-ffaf66beeaef"
    }
}
```

백업 작업은 장기 실행 작업이므로 [REST API를 사용한 모니터링 작업 문서](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job)에 설명된 대로 추적해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Rest API를 사용하여 Azure 파일 공유를 복원](restore-azure-file-share-rest-api.md)하는 방법을 알아봅니다.
