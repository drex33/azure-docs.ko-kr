---
title: Azure API Management 일시 삭제(미리 보기) | Microsoft Docs
description: 일시 삭제를 통해 삭제한 API Management 인스턴스를 복구할 수 있습니다.
ms.service: api-management
ms.topic: conceptual
author: dlepow
ms.author: danlep
ms.date: 11/27/2020
ms.openlocfilehash: ca142ec96bf4ec45cc7c2f612a38ee0d10bf9615
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000578"
---
# <a name="api-management-soft-delete-preview"></a>API Management 일시 삭제(미리 보기)

API Management 일시 삭제(미리 보기)를 이용해 최근에 삭제한 APIM(API Management) 인스턴스를 복구 및 복원할 수 있습니다.

> [!IMPORTANT]
> `2020-06-01-preview` 이후의 API 버전을 사용해 삭제한 API Management만 일시 삭제되어 본 문서에서 설명한 단계를 통해 복구할 수 있습니다. 이전 API 버전을 통해 삭제한 APIM은 영구 삭제됩니다. Azure PowerShell 및 Azure CLI는 현재 `2020-06-01-preview` 버전을 사용하지 않아 마찬가지로 영구 삭제됩니다.

## <a name="supporting-interfaces"></a>인터페이스 지원

일시 삭제 기능은 [REST API](/rest/api/apimanagement/2021-01-01-preview/api-management-service/restore)를 통해 사용할 수 있습니다.

> [!TIP]
> Azure REST API 호출을 위한 팁과 도구는 [Azure REST API 참조](/rest/api/azure/)를 참조하세요.

| 작업(Operation) | Description | API Management 네임스페이스 | 최소 API 버전 |
|--|--|--|--|
| [만들기 또는 업데이트](/rest/api/apimanagement/2021-01-01-preview/api-management-service/create-or-update) | API Management 서비스 만들거나 업데이트 하기  | API Management 서비스 | 모두 |
| `restore`속성 설정을 **true** 로 하여 [만들기 또는 업데이트](/rest/api/apimanagement/2021-01-01-preview/api-management-service/create-or-update) | 이전에 일시 삭제된 API Management 서비스를 삭제 취소합니다. `restore`가 지정되어 `true`로 설정되어 있으면 다른 모든 속성은 무시됩니다.  | API Management 서비스 |  2020-06-01-미리 보기 |
| [삭제](/rest/api/apimanagement/2021-01-01-preview/api-management-service/delete) | 기존 API Management 서비스를 삭제합니다. | API Management 서비스 | 2020-06-01-미리 보기|
| [이름별로 가져오기](/rest/api/apimanagement/2021-01-01-preview/deleted-services/get-by-name) | 이름으로 일시 삭제한 Api Management 서비스를 가져옵니다. | 삭제한 서비스 | 2020-06-01-미리 보기 |
| [구독별 목록](/rest/api/apimanagement/2021-01-01-preview/deleted-services/list-by-subscription) | 일시 삭제된 서비스 가운데 주어진 구독에 대해 삭제 취소가 가능한 모든 서비스를 나열합니다. | 삭제한 서비스 | 2020-06-01-미리 보기
| [제거](/rest/api/apimanagement/2021-01-01-preview/deleted-services/purge) | API Management 서비스를 제거합니다(삭제 취소 옵션 없이 삭제합니다). | 삭제한 서비스 | 2020-06-01-미리 보기

## <a name="soft-delete-behavior"></a>일시 삭제 동작

모든 API 버전을 통해 API Management 인스턴스를 이용할 수 있지만 APIM 인스턴스를 일시 삭제하고 복구 옵션을 확보하려면 `2020-06-01-preview` 이후 버전을 사용해야 합니다.

API Management 인스턴스 삭제 시, 해당 서비스는 삭제 상태로 존재하며 어떤 APIM  작업에도 액세스할 수 없게 됩니다. 이러한 상태에서 해당 APIM 인스턴스는 나열, 복구, 제거(영구 삭제)만 가능합니다.

이와 동시에, Azure는 APIM 인스턴스에 해당되는 기본 데이터의 삭제를 미리 지정된 (48시간의) 보존 간격 이후에 실행하기 위해 예약합니다. 자격 증명 인스턴스에 해당하는 DNS 레코드도 보존 간격 기간 동안 유지됩니다. 일시 삭제된 API Management 인스턴스의 이름은 보존 기간이 지날 때까지 다시 사용할 수 없습니다.

APIM 인스턴스가 48시간 이내에 복구되지 않는 경우, 영구 삭제되어 복구가 불가능합니다. 일시 삭제 보존 기간을 포기하며 APIM를 [제거](#purge-a-soft-deleted-apim-instance)(영구 삭제)할 수도 있습니다.

## <a name="list-deleted-apim-instances"></a>삭제한 APIM 인스턴스 나열

삭제한 서비스[이름별로 가져오기](/rest/api/apimanagement/2021-01-01-preview/deleted-services/get-by-name) 또는 [구독별 나열](/rest/api/apimanagement/2021-01-01-preview/deleted-services/list-by-subscription) 작업을 통해 일시 삭제한 APIM 인스턴스를 복원(삭제 취소)할 수 있는지 확인할 수 있습니다.

### <a name="get-a-soft-deleted-instance-by-name"></a>이름별로 일시 삭제한 인스턴스 가져오기

API Management[이름별로 가져오기](/rest/api/apimanagement/2021-01-01-preview/deleted-services/get-by-name) 작업을 사용해 `{subscriptionId}`, `{location}`, `{serviceName}`을 Azure 구독, 리소스 위치, API Management 인스턴스 이름과 대체합니다.

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

삭제 취소가 가능한 경우, Azure는 `deletionDate` 및 `scheduledPurgeDate`를 보여 주는 APIM 인스턴스 기록을 다음의 예시와 같이 반환합니다.

```json
{
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ApiManagement/locations/southcentralus/deletedservices/apimtest",
    "name": "apimtest",
    "type": "Microsoft.ApiManagement/deletedservices",
    "location": "South Central US",
    "properties": {
        "serviceId": "/subscriptions/########-####-####-####-############/resourceGroups/apimtestgroup/providers/Microsoft.ApiManagement/service/apimtest",
        "scheduledPurgeDate": "2020-11-26T19:40:26.3596893Z",
        "deletionDate": "2020-11-24T19:40:50.1013572Z"
    }
}
```

### <a name="list-all-soft-deleted-instances-for-a-given-subscription"></a>주어진 구독에 대해 일시 삭제된 모든 인스턴스 나열

API Management[구독별 나열](/rest/api/apimanagement/2021-01-01-preview/deleted-services/list-by-subscription) 작업을 사용해, `{subscriptionId}`를 구독 ID와 대체합니다.

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/deletedservices?api-version=2020-06-01-preview
```

이렇게 하면 주어진 구독에 대해 각자 `deletionDate` 및 `scheduledPurgeDate`를 표시하는 일시 삭제된 서비스 중 삭제 취소가 가능한 모든 것들의 목록을 반환합니다.

## <a name="recover-a-deleted-apim-instance"></a>삭제한 APIM 인스턴스 복구

API Management[만들기 또는 업데이트 하기](/rest/api/apimanagement/2021-01-01-preview/api-management-service/create-or-update) 작업을 사용해 `{subscriptionId}`, `{resourceGroup}`, `{apimServiceName}`을 Azure 구독, 리소스 그룹 이름, API Management 이름으로 대체합니다.

```rest
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ApiManagement/service/{apimServiceName}?api-version=2021-01-01-preview
```

. . . 또한 요청 본문에서 `restore` 속성을 `true`로 설정합니다. (해당 플래그가 지정되어 *true* 로 설정되면, 기타 속성은 모두 무시됩니다.) 예를 들면,

```json
{
  "properties": {
    "publisherEmail": "help@contoso.com",
    "publisherName": "Contoso",
    "restore": true
  },
  "sku": {
    "name": "Developer",
    "capacity": 1
  },
  "location": "South Central US"
}
```

## <a name="purge-a-soft-deleted-apim-instance"></a>일시 삭제한 APIM 인스턴스 제거

API Management[제거](/rest/api/apimanagement/2021-01-01-preview/deleted-services/purge) 작업을 사용하여 `{subscriptionId}`, `{location}`, `{serviceName}`을 Azure 구독, 리소스 위치, API Management 이름과 대체합니다.

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

이렇게 하면 API Management 인스턴스가 Azure에서 영구적으로 삭제됩니다.

## <a name="next-steps"></a>다음 단계

장기 API Management 백업 및 복구 옵션에 대해 알아봅니다.

- [Azure API Management에서 서비스 백업 및 복원을 사용하여 재해 복구를 구현하는 방법](api-management-howto-disaster-recovery-backup-restore.md)
