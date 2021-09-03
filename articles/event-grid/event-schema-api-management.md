---
title: Event Grid 원본으로 API Management 사용
description: 이 문서에서는 Event Grid 이벤트 원본으로 Azure API Management를 사용하는 방법을 설명합니다. 방법 문서에 대한 스키마와 링크를 제공합니다.
ms.topic: conceptual
author: dlepow
ms.author: danlep
ms.date: 07/12/2021
ms.openlocfilehash: c14107561886a9e29c2d95c5d04847274afdf4e3
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113734112"
---
# <a name="azure-api-management-as-an-event-grid-source-preview"></a>Event Grid 원본으로 Azure API Management 사용(미리 보기)

이 문서에서는 [Azure API Management](../api-management/index.yml) 이벤트의 속성과 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](./event-schema.md)를 참조하세요. API Management를 이벤트 원본으로 사용할 수 있는 문서의 링크도 제공합니다.

## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

API Management는 다음 이벤트 유형을 내보냅니다.

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.APIManagement.UserCreated | 사용자가 만들어질 때 발생합니다. |
| Microsoft.APIManagement.UserUpdated | 사용자가 업데이트될 때 발생합니다. |
| Microsoft.APIManagement.UserDeleted | 사용자가 삭제될 때 발생합니다. |
| Microsoft.APIManagement.APICreated | API가 만들어질 때 발생합니다. |
| Microsoft.APIManagement.APIUpdated | API가 업데이트될 때 발생합니다. |
| Microsoft.APIManagement.APIDeleted | API가 삭제될 때 발생합니다. |
| Microsoft.APIManagement.ProductCreated | 제품이 만들어질 때 발생합니다. |
| Microsoft.APIManagement.ProductUpdated | 제품이 업데이트될 때 발생합니다. |
| Microsoft.APIManagement.ProductDeleted | 제품이 삭제될 때 발생합니다. |
| Microsoft.APIManagement.ReleaseCreated | API 릴리스가 만들어질 때 발생합니다. |
| Microsoft.APIManagement.ReleaseUpdated | API 릴리스가 업데이트될 때 발생합니다. |
| Microsoft.APIManagement.ReleaseDeleted | API 릴리스가 삭제될 때 발생합니다. |
| Microsoft.APIManagement.SubscriptionCreated | 구독이 만들어질 때 발생합니다. |
| Microsoft.APIManagement.SubscriptionUpdated | 구독이 업데이트될 때 발생합니다. |
| Microsoft.APIManagement.SubscriptionDeleted | 구독이 삭제될 때 발생합니다. |

## <a name="example-event"></a>예제 이벤트

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)
다음 예제에서는 제품 생성 이벤트의 스키마를 보여 줍니다. 다른 API Management 리소스 생성 이벤트의 스키마는 비슷합니다.

```json
[{
  "id": "92c502f2-a966-42a7-a428-d3b319844544",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/products/myproduct",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/products/myproduct"
  },
  "eventType": "Microsoft.ApiManagement.ProductCreated",
  "dataVersion": "1",
  "metadataVersion": "1",
  "eventTime": "2021-07-02T00:47:47.8536532Z"
}]
```

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

다음 예제에서는 제품 생성 이벤트의 스키마를 보여 줍니다. 다른 API Management 리소스 생성 이벤트의 스키마는 비슷합니다. 

```json
[{
  "id": "81dac958-49cf-487e-8805-d0baf0ee485a",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/products/myproduct",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/products/myproduct"
  },
  "Type": "Microsoft.ApiManagement.ProductCreated",
  "Time": "2021-07-02T00:38:44.3978295Z",
  "specversion":"1.0"
}]
```
---

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)
다음 예제에서는 사용자 삭제 이벤트의 스키마를 보여 줍니다. 다른 API Management 리소스 삭제 이벤트의 스키마는 비슷합니다.

```json
[{
  "id": "92c502f2-a966-42a7-a428-d3b319844544",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/users/apimuser-contoso-com",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/users/apimuser-contoso-com"
  },
  "eventType": "Microsoft.ApiManagement.UserDeleted",
  "dataVersion": "1",
  "metadataVersion": "1",
  "eventTime": "2021-07-02T00:47:47.8536532Z"
}]
```

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

다음 예제에서는 사용자 삭제 이벤트의 스키마를 보여 줍니다. 다른 API Management 리소스 삭제 이벤트의 스키마는 비슷합니다. 

```json
[{
  "id": "81dac958-49cf-487e-8805-d0baf0ee485a",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/users/apimuser-contoso-com",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/users/apimuser-contoso-com"
  },
  "Type": "Microsoft.ApiManagement.UserDeleted",
  "Time": "2021-07-02T00:38:44.3978295Z",
  "specversion":"1.0"
}]
```
---

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)

다음 예제에서는 API 업데이트 이벤트의 스키마를 보여 줍니다. `data` 속성에는 `updatedProperies` 배열과 `resourceUri`가 둘 다 포함됩니다.  다른 API Management 리소스 업데이트 이벤트의 스키마는 비슷합니다. 
```json
[{
  "id": "95015754-aa51-4eb6-98d9-9ee322b82ad7",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/apis/myapi;Rev=1",
  "data": {
    "updatedProperties": [
      "path"
    ],
    "resourceUri": "/subscriptions/subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/apis/myapi;Rev=1"
  },
  "eventType": "Microsoft.ApiManagement.APIUpdated",
  "dataVersion": "1",
  "metadataVersion": "1",
  "eventTime": "2021-07-12T23:13:44.9048323Z"
}]
```

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

다음 예제에서는 API 업데이트 이벤트의 스키마를 보여 줍니다. `data` 속성에는 `updatedProperies` 배열과 `resourceUri`가 둘 다 포함됩니다.  다른 API Management 리소스 업데이트 이벤트의 스키마는 비슷합니다. 

```json
[{
  "id": "95015754-aa51-4eb6-98d9-9ee322b82ad7",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/apis/myapi;Rev=1",
  "data": {
    "updatedProperties": [
      "path"
    ],
    "resourceUri": "/subscriptions/subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/apis/myapi;Rev=1"
  },
  "Type": "Microsoft.ApiManagement.APIUpdated",
  "Time": "2021-07-12T23:13:44.9048323Z",
  "specversion":1.0
}]
```
---

## <a name="event-properties"></a>이벤트 속성

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 형식 | Description |
| -------- | ---- | ----------- |
| `topic` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 리소스 이름과 리소스 형식을 포함하여 준수 상태 변경이 적용되는 리소스의 정규화된 ID입니다. `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` 형식 사용 |
| `eventType` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `eventTime` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `data` | object | API Management 이벤트 데이터입니다. |
| `dataVersion` | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| `metadataVersion` | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 형식 | Description |
| -------- | ---- | ----------- |
| `source` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 리소스 이름과 리소스 형식을 포함하여 준수 상태 변경이 적용되는 리소스의 정규화된 ID입니다. `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` 형식 사용 |
| `type` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `time` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `data` | object | API Management 이벤트 데이터입니다. |
| `specversion` | 문자열 | CloudEvents 스키마 사양 버전입니다. |

---

데이터 개체의 속성은 다음과 같습니다.

| 속성 | 형식 | Description |
| -------- | ---- | ----------- |
| `resourceUri` | 문자열 | 이벤트를 트리거한 API Management 리소스의 URI입니다. |
| `updatedProperties` | string[] | 업데이트 이벤트를 트리거한 API Management 리소스에서 업데이트된 속성 목록입니다. |

## <a name="tutorials-and-how-tos"></a>자습서 및 방법

|제목  |설명  |
|---------|---------|
| [API Management에서 Event Grid로 이벤트 보내기](../api-management/how-to-event-grid.md)| Event Grid를 사용하여 API Management 이벤트를 구독하는 방법 |

## <a name="next-steps"></a>다음 단계

- Azure Event Grid에 대한 소개는 [Event Grid란?](./overview.md)을 참조하세요.
- Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](./subscription-creation-schema.md)를 참조하세요.


