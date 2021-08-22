---
title: REST API를 사용하여 Azure VM 백업
description: 이 문서에서는 REST API를 사용하여 Azure VM Backup의 백업 작업을 구성, 시작 및 관리하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 57187a9f7ecf3e1d00fa395d25d98fd03f5d2698
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461026"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>REST API를 통해 Azure Backup을 사용하여 Azure VM 백업

이 문서에서는 REST API를 통해 Azure Backup을 사용하여 Azure VM의 백업을 관리하는 방법을 설명합니다. 이전에 보호되지 않는 Azure VM에 대한 보호를 처음으로 구성하고, 보호된 Azure VM에 대한 주문형 백업을 트리거하고, 여기에 설명된 대로 REST API를 통해 백업된 VM의 백업 속성을 수정합니다.

새 자격 증명 모음 및 정책 만들기는 [자격 증명 모음 만들기](backup-azure-arm-userestapi-createorupdatevault.md) 및 [정책 만들기](backup-azure-arm-userestapi-createorupdatepolicy.md) REST API 자습서를 참조하세요.

기본 정책("DefaultPolicy")을 사용하여 리소스 그룹 "testVaultRG" 내에 있는 Recovery Services 자격 증명 모음 "testVault"에 대해 리소스 그룹 "testRG"의 VM "testVM"을 보호하려는 경우를 가정해봅시다.

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>REST API를 사용하여 보호되지 않는 Azure VM에 대한 백업 구성

### <a name="discover-unprotected-azure-vms"></a>보호되지 않는 Azure VM 검색

먼저 자격 증명 모음은 Azure VM을 식별할 수 있어야 합니다. [새로 고침 작업](/rest/api/backup/protection-containers/refresh)을 사용하여 이 자격 증명 모음을 트리거합니다. 자격 증명 모음이 현재 구독에서 보호되지 않는 모든 VM의 최신 목록을 가져와서 '캐시'하도록 하는 비동기식 *POST* 작업입니다. VM이 '캐시되면' Recovery Services는 해당 VM에 액세스하고 보호할 수 있습니다.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

POST URI에는 `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}`, `{fabricName}` 매개 변수가 있습니다. `{fabricName}`은 "Azure"입니다. 이 예에 따르면 `{vaultName}`은 "testVault"이고 `{vaultresourceGroupName}`은 "testVaultRG"입니다. 모든 필수 매개 변수가 URI에서 지정되므로 별도의 요청 본문이 필요 없습니다.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses-to-refresh-operation"></a>새로 고침 작업에 대한 응답

'새로 고침' 작업은 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

이 작업은 다른 작업을 만드는 경우 202(수락됨) 및 해당 작업이 완료되는 경우 200(정상)의 두 응답을 반환합니다.

|속성  |유형  |Description  |
|---------|---------|---------|
|204 콘텐츠 없음     |         |  반환된 콘텐츠가 없는 경우 정상      |
|202 수락됨     |         |     수락됨    |

##### <a name="example-responses-to-refresh-operation"></a>새로 고침 작업에 대한 응답 예

*POST* 요청을 제출하면 202(수락됨) 응답이 반환됩니다.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
X-Content-Type-Options: nosniff
x-ms-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-client-request-id: 4910609f-bb9b-4c23-8527-eb6fa2d3253f; 4910609f-bb9b-4c23-8527-eb6fa2d3253f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-routing-request-id: SOUTHINDIA:20180521T105701Z:43cf550d-e463-421c-8922-37e4766db27d
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:57:00 GMT
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
X-Powered-By: ASP.NET
```

간단한 *GET* 명령으로 “위치” 헤더를 사용하여 결과 작업 추적

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
```

모든 Azure VM을 검색하면 GET 명령이 204(콘텐츠 없음) 응답을 반환합니다. 이제 자격 증명 모음은 구독 내의 모든 VM을 검색할 수 있습니다.

```http
HTTP/1.1 204 NoContent
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-client-request-id: 25bb6345-f9fc-4406-be1a-dc6db0eefafe; 25bb6345-f9fc-4406-be1a-dc6db0eefafe
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14997
x-ms-correlation-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-routing-request-id: SOUTHINDIA:20180521T105825Z:cf6cd73b-9189-4942-a61d-878fcf76b1c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:58:25 GMT
X-Powered-By: ASP.NET
```

### <a name="selecting-the-relevant-azure-vm"></a>관련 Azure VM 선택

 구독에서 [모든 보호 가능한 항목 나열하기](/rest/api/backup/backup-protectable-items/list)에서 "캐싱"을 수행했는지 확인하고 응답에서 원하는 VM을 찾을 수 있습니다. [이 작업의 응답](#example-responses-to-get-operation)은 또한 Recovery Services가 VM을 식별하는 방법에 대한 정보를 제공합니다.  패턴에 친숙해지면 이 단계를 건너뛰고 직접 [보호 활성화](#enabling-protection-for-the-azure-vm)를 진행할 수 있습니다.

이 작업은 *GET* 작업입니다.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

*GET* URI에는 필요한 모든 매개 변수가 있습니다. 추가 요청 본문이 필요없습니다.

#### <a name="responses-to-get-operation"></a>가져오기 작업에 대한 응답

|속성  |유형  |설명  |
|---------|---------|---------|
|200 정상     | [WorkloadProtectableItemResourceList](/rest/api/backup/backup-protectable-items/list#workloadprotectableitemresourcelist)        |       정상 |

#### <a name="example-responses-to-get-operation"></a>가져오기 작업에 대한 응답 예

*GET* 요청이 제출되면 200(정상) 응답이 반환됩니다.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-client-request-id: 40c8601a-c217-4c68-87da-01db8dac93dd; 40c8601a-c217-4c68-87da-01db8dac93dd
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14979
x-ms-correlation-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-routing-request-id: SOUTHINDIA:20180521T071408Z:7c2cf56a-e6be-4345-96df-c27ed849fe36
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:14:08 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectableItems/vm;iaasvmcontainerv2;testRG;testVM",
      "name": "iaasvmcontainerv2;testRG;testVM",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
      "properties": {
        "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "virtualMachineVersion": "Compute",
        "resourceGroup": "testRG",
        "backupManagementType": "AzureIaasVM",
        "protectableItemType": "Microsoft.Compute/virtualMachines",
        "friendlyName": "testVM",
        "protectionState": "NotProtected"
      }
    },……………..

```

> [!TIP]
> *GET* 응답에서 값의 개수는 '페이지'당 200개로 제한됩니다. 'nextLink' 필드를 사용하여 다음 집합의 응답에 대한 URL을 가져옵니다.

이 응답에는 보호되지 않는 모든 Azure VM 목록이 포함되며 각 `{value}`에는 Azure Recovery Service에서 백업을 구성하는 데 필요한 모든 정보가 포함됩니다. Backup을 구성하려면 `{properties}` 섹션에서 `{name}` 필드 및 `{virtualMachineId}` 필드를 참고합니다. 아래에 설명된 대로 이러한 필드 값에서 두 개의 변수를 생성합니다.

- containerName = "iaasvmcontainer;"+`{name}`
- protectedItemName = "vm;"+ `{name}`
- `{virtualMachineId}`는 나중에 [요청 본문](#example-request-body)에 사용됨

예제에서는 위의 값이 다음으로 변환됩니다.

- containerName = "iaasvmcontainer;iaasvmcontainerv2;testRG;testVM"
- protectedItemName = "vm;iaasvmcontainerv2;testRG;testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Azure VM 보호 사용

관련 VM이 "캐시"되고 "확인"된 경우 정책을 선택하여 보호합니다. 자격 증명 모음의 기존 정책에 대한 자세한 내용은 [정책 API 목록](/rest/api/backup/backup-policies/list)을 참조하세요. 그런 다음, 정책 이름을 참조하여 [관련 정책](/rest/api/backup/protection-policies/get)을 선택합니다. 정책을 만들려면 [정책 자습서 만들기](backup-azure-arm-userestapi-createorupdatepolicy.md)를 참조하세요. 다음 예에서 "DefaultPolicy"를 선택합니다.

보호 사용은 '보호된 항목'를 만드는 비동기 *PUT* 작업입니다.

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

`{containerName}` 및 `{protectedItemName}`은 위에서 생성됩니다. `{fabricName}`은 "Azure"입니다. 예를 들어 이렇게 하면 다음으로 변환됩니다.

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>요청 본문 만들기

보호된 항목을 만들려면 요청 본문의 구성 요소는 다음과 같습니다.

|속성  |유형  |설명  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |ProtectedItem 리소스 속성         |

요청 본문 및 기타 세부 정보에 대한 전체 정의 목록은 [보호된 항목 REST API 문서 만들기](/rest/api/backup/protected-items/create-or-update#request-body)를 참조하세요.

##### <a name="example-request-body"></a>요청 본문 예제

다음 요청 본문은 보호된 항목을 만드는 데 필요한 속성을 정의합니다.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

`{sourceResourceId}`는 [보호 가능한 항목 목록의 응답](#example-responses-to-get-operation)의 위에서 설명한 `{virtualMachineId}`입니다.

#### <a name="responses-to-create-protected-item-operation"></a>보호 항목 생성 작업에 대한 응답

보호된 항목 만들기는 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

이 작업은 다른 작업을 만드는 경우 202(수락됨) 및 해당 작업이 완료되는 경우 200(정상)의 두 응답을 반환합니다.

|속성  |유형  |설명  |
|---------|---------|---------|
|200 정상     |    [ProtectedItemResource](/rest/api/backup/protected-item-operation-results/get#protecteditemresource)     |  정상       |
|202 수락됨     |         |     수락됨    |

##### <a name="example-responses-to-create-protected-item-operation"></a>보호 항목 생성 작업에 대한 응답 예

보호된 항목 만들기 또는 업데이트를 위한 *PUT* 요청을 제출하면 초기 응답은 위치 헤더 또는 Azure-async-header를 사용한 202(수락됨)입니다.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

그런 다음, 간단한 *GET* 명령으로 위치 헤더 또는 Azure-AsyncOperation 헤더를 사용하여 결과 작업을 추적합니다.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

작업이 완료되면 응답 본문에서 보호된 항목 콘텐츠를 사용하여 200(정상)을 반환합니다.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM",
  "name": "VM;testRG;testVM",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems",
  "properties": {
    "friendlyName": "testVM",
    "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "protectionStatus": "Healthy",
    "protectionState": "IRPending",
    "healthStatus": "Passed",
    "lastBackupStatus": "",
    "lastBackupTime": "2001-01-01T00:00:00Z",
    "protectedItemDataId": "17592691116891",
    "extendedInfo": {
      "recoveryPointCount": 0,
      "policyInconsistent": false
    },
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "backupManagementType": "AzureIaasVM",
    "workloadType": "VM",
    "containerName": "iaasvmcontainerv2;testRG;testVM",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "policyName": "DefaultPolicy"
  }
}
```

이렇게 하면 VM에 대한 보호가 사용하도록 설정되고 정책 일정에 따라 첫 번째 백업이 트리거됩니다.

### <a name="excluding-disks-in-azure-vm-backup"></a>Azure VM 백업에서 디스크 제외

Azure Backup은 또한 Azure VM에서 디스크의 하위 집합을 선택적으로 백업하는 방법을 제공합니다. 자세한 내용은 [여기](selective-disk-backup-restore.md)를 참조하세요. 보호를 사용하도록 설정하는 동안 일부 디스크를 선택적으로 백업하려면 다음 코드 조각이 [보호 사용 설정 중 요청 본문](#example-request-body)이어야 합니다.

```json
{
"properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "extendedProperties":  {
      "diskExclusionProperties":{
          "diskLunList":[0,1],
          "isInclusionList":true
        }
    }
}
}
```

위의 요청 본문에서 백업할 디스크 목록은 확장 속성 섹션에 제공됩니다.

|속성  |값  |
|---------|---------|
|diskLunList     | 디스크 LUN 목록은 *데이터 디스크의 LUN* 목록입니다. **OS 디스크는 항상 백업되며 언급할 필요가 없습니다**.        |
|IsInclusionList     | 백업 중에 LUN을 포함하려면 **true** 여야 합니다. **false** 이면 앞서 언급한 LUN이 제외됩니다.         |

따라서 요구 사항이 OS 디스크만 백업하는 것이라면 _모든_ 데이터 디스크를 제외해야 합니다. 더 쉬운 방법은 데이터 디스크가 포함되지 않아야 한다고 말하는 것입니다. 따라서 디스크 LUN 목록은 비어 있고 **IsInclusionList** 는 **true** 가 됩니다. 마찬가지로 하위 집합을 선택하는 더 쉬운 방법은 무엇인지 생각해 보세요. 항상 몇 개의 디스크를 제외하거나 몇 개의 디스크를 항상 포함해야 합니다. 그에 따라 LUN 목록과 부울 변수 값을 선택합니다.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>보호된 Azure VM에 대한 주문형 백업 트리거

Azure VM이 백업용으로 구성되면 정책 일정에 따라 백업이 수행됩니다. 첫 번째 예약 백업을 대기하거나 언제든 주문형 백업을 트리거할 수 있습니다. 주문형 백업의 보존은 백업 정책의 보존과는 별개이며 특정 날짜/시간을 지정할 수 있습니다. 지정하지 않으면 주문형 백업을 트리거한 날로부터 30일까지로 간주됩니다.

주문형 백업의 트리거는 *POST* 작업입니다.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

`{containerName}` 및 `{protectedItemName}`은 [위에서](#responses-to-get-operation) 생성됩니다. `{fabricName}`은 "Azure"입니다. 예를 들어 이렇게 하면 다음으로 변환됩니다.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body-for-on-demand-backup"></a>주문형 백업을 위한 요청 본문 생성

주문형 백업을 트리거하려면 요청 본문의 구성 요소는 다음과 같습니다.

|이름  |유형  |설명  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |BackupRequestResource 속성         |

요청 본문 및 기타 세부 정보에 대한 전체 정의 목록은 [보호된 항목 REST API 문서의 백업 트리거](/rest/api/backup/backups/trigger#request-body)를 참조하세요.

#### <a name="example-request-body-for-on-demand-backup"></a>주문형 백업에 대한 요청 본문의 예

다음 요청 본문은 보호된 항목의 백업을 트리거하는 데 필요한 속성을 정의합니다. 보존을 지정하지 않으면 백업 작업을 트리거한 시간에서 30일 동안 유지됩니다.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses-for-on-demand-backup"></a>주문형 백업에 대한 응답

주문형 백업의 트리거는 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

이 작업은 다른 작업을 만드는 경우 202(수락됨) 및 해당 작업이 완료되는 경우 200(정상)의 두 응답을 반환합니다.

|속성  |유형  |Description  |
|---------|---------|---------|
|202 수락됨     |         |     수락됨    |

#### <a name="example-responses-for-on-demand-backup"></a>주문형 백업에 대한 응답 예

주문형 백업에 대한 *POST* 요청을 제출하면 초기 응답은 위치 헤더 또는 Azure-async-header를 사용한 202(수락됨)입니다.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

그런 다음, 간단한 *GET* 명령으로 위치 헤더 또는 Azure-AsyncOperation 헤더를 사용하여 결과 작업을 추적합니다.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

작업이 완료되면 응답 본문에서 결과 백업 작업의 ID를 사용하여 200(정상)을 반환합니다.

```json
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-client-request-id: 5a63209d-3708-4e69-a75f-9499f4c8977c; 5a63209d-3708-4e69-a75f-9499f4c8977c
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14995
x-ms-correlation-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-routing-request-id: SOUTHINDIA:20180521T083723Z:a8b13524-2c95-445f-b107-920806f385c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:37:22 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "00000000-0000-0000-0000-000000000000",
  "status": "Succeeded",
  "startTime": "2018-05-21T08:35:40.9488967Z",
  "endTime": "2018-05-21T08:35:40.9488967Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "7ddead57-bcb9-4269-ac31-6a1b57588700"
  }
}
```

백업 작업은 장기 실행 작업이므로 [REST API를 사용한 모니터링 작업 문서](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)에 설명된 대로 추적해야 합니다.

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>보호된 Azure VM에 대한 백업 구성 수정

### <a name="changing-the-policy-of-protection"></a>보호 정책 변경

VM을 보호하는 정책을 변경하려면 [보호 사용](#enabling-protection-for-the-azure-vm)과 동일한 형식을 사용할 수 있습니다. [요청 본문](#example-request-body)의 새 정책 ID를 제공하고 요청을 제출합니다. 예: testVM의 정책을 'DefaultPolicy'에서 'ProdPolicy'로 변경하려면 요청 본문에 'ProdPolicy' ID를 제공합니다.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

응답은 [보호 사용의 경우](#responses-to-create-protected-item-operation)에 설명된 것과 동일한 형식을 따름

#### <a name="excluding-disks-during-azure-vm-protection"></a>Azure VM 보호 중 디스크 제외

Azure VM이 이미 백업된 경우 보호 정책을 변경하여 백업하거나 제외할 디스크 목록을 지정할 수 있습니다. [보호 사용 설정 중 디스크 제외](#excluding-disks-in-azure-vm-backup)와 동일한 형식으로 요청을 준비하세요.

> [!IMPORTANT]
> 위의 요청 본문은 항상 제외하거나 포함할 데이터 디스크의 최종 복사본입니다. 이는 이전 구성에 *추가* 되지 않습니다. 예: 보호를 "데이터 디스크 1 제외"로 먼저 업데이트한 다음 "데이터 디스크 2 제외"로 반복하면 후속 백업에서 *데이터 디스크 2만 제외* 되고 데이터 디스크 1이 포함됩니다. 이것은 항상 후속 백업에 포함/제외되는 최종 목록입니다.

제외되거나 포함된 디스크의 현재 목록을 얻으려면 [여기](/rest/api/backup/protected-items/get)에 언급된 보호 항목 정보를 가져옵니다. 응답은 데이터 디스크 LUN 목록을 제공하고 포함 또는 제외 여부를 나타냅니다.

### <a name="stop-protection-but-retain-existing-data"></a>보호를 중지하지만 기존 데이터는 보존

보호된 VM에서 보호를 제거하지만 이미 백업된 데이터를 보존하려면 요청 본문에서 정책을 제거하고 요청을 제출합니다. 정책과의 연결을 제거하면 백업은 더 이상 트리거되지 않으며 새 복원 지점도 생성되지 않습니다.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

응답은 [주문형 백업 트리거하는 경우](#example-responses-for-on-demand-backup)에 설명된 것과 동일한 형식을 따릅니다. [REST API 문서를 사용한 모니터링 작업](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)에 설명된 대로 결과 작업을 추적해야 합니다.

### <a name="stop-protection-and-delete-data"></a>보호 중지 및 데이터 삭제

보호된 VM에 대한 보호를 제거하고 백업 데이터도 삭제하려면 [여기](/rest/api/backup/protected-items/delete)에 설명된 대로 삭제 작업을 수행합니다.

보호 중지 및 데이터 삭제는 *DELETE* 작업입니다.

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

`{containerName}` 및 `{protectedItemName}`은 [위에서](#responses-to-get-operation) 생성됩니다. `{fabricName}`은 "Azure"입니다. 예를 들어 이렇게 하면 다음으로 변환됩니다.

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="responses-for-delete-protection"></a>삭제 방지에 대한 응답

보호 *DELETE* 작업은 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

이 작업은 다른 작업을 만드는 경우 202(수락됨) 및 해당 작업이 완료되는 경우 204(NoContent)의 두 응답을 반환합니다.

|속성  |유형  |Description  |
|---------|---------|---------|
|204 NoContent     |         |  NoContent       |
|202 수락됨     |         |     수락됨    |

> [!IMPORTANT]
> 실수로 인한 삭제 시나리오를 방지하기 위해 Recovery Services 자격 증명 모음에 [사용할 수 있는 일시 삭제 기능](use-restapi-update-vault-properties.md#soft-delete-state)이 있습니다. 자격 증명 모음의 일시 삭제 상태가 사용으로 설정되어 있으면 삭제 작업으로 데이터가 즉시 삭제되지 않습니다. 14일 동안 보관된 후 영구적으로 제거됩니다. 이 14일 동안의 스토리지 요금은 청구되지 않습니다. 삭제 작업을 취소하려면 [삭제 취소 섹션](#undo-the-deletion)을 참조하세요.

### <a name="undo-the-deletion"></a>삭제 취소

실수로 삭제한 항목을 취소하는 것은 백업 항목을 만드는 것과 유사합니다. 삭제를 실행 취소한 후 항목은 유지되지만 이후 백업은 트리거되지 않습니다.

삭제 취소는 [정책을 변경하거나](#changing-the-policy-of-protection) [보호를 사용하도록 설정하는](#enabling-protection-for-the-azure-vm)것과 매우 유사한 *PUT* 작업입니다. [요청 본문](#example-request-body)에서 변수 *isRehydrate* 를 사용하여 삭제를 취소하고 요청을 제출하려고 하기만 하면 됩니다. 예: testVM에 대한 삭제를 취소하려면 다음 요청 본문을 사용해야 합니다.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "protectionState": "ProtectionStopped",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "isRehydrate": true
  }
}
```

응답은 [주문형 백업 트리거하는 경우](#example-responses-for-on-demand-backup)에 설명된 것과 동일한 형식을 따릅니다. [REST API 문서를 사용한 모니터링 작업](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)에 설명된 대로 결과 작업을 추적해야 합니다.

## <a name="next-steps"></a>다음 단계

[Azure Virtual Machine 백업에서 데이터 복원](backup-azure-arm-userestapi-restoreazurevms.md).

Azure Backup REST API에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Recovery Services 공급자 REST API](/rest/api/recoveryservices/)
- [Azure REST API 시작하기](/rest/api/azure/)
