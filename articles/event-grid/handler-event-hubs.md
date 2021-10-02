---
title: Azure Event Grid 이벤트에 대한 이벤트 처리기로서의 이벤트 허브
description: Azure Event Grid 이벤트에 대한 이벤트 처리기로 이벤트 허브를 사용하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 590364d759fe6f628cfaa306d162d00f575c0622
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354416"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Azure Event Grid 이벤트에 대한 이벤트 처리기로서의 이벤트 허브
이벤트 처리기는 이벤트가 전송된 위치입니다. 처리기는 이벤트를 처리하기 위한 작업을 수행합니다. 이벤트를 처리하도록 여러 Azure 서비스가 자동으로 구성되며, **Azure Event Hubs** 가 그 중 하나입니다. 

솔루션이 이벤트를 처리할 수 있는 속도보다 빠르게 Event Grid에서 이벤트를 가져오는 경우 **Event Hubs** 를 사용하세요. 이벤트가 이벤트 허브에 있으면 애플리케이션은 자체 일정에 따라 이벤트 허브에서 이벤트를 처리할 수 있습니다. 들어오는 이벤트를 처리하도록 이벤트 처리 규모를 확장할 수 있습니다.

## <a name="tutorials"></a>자습서
다음 예제를 참조하세요. 

|제목  |Description  |
|---------|---------|
| [빠른 시작: Azure CLI를 사용하여 Azure Event Hubs로 사용자 지정 이벤트 라우팅](custom-event-to-eventhub.md) | 애플리케이션에서 처리하도록 사용자 지정 이벤트를 이벤트 허브로 보냅니다. |
| [Resource Manager 템플릿: Event Grid 사용자 지정 항목 만들기 및 이벤트 허브로 이벤트 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid-event-hubs-handler)| 사용자 지정 토픽에 대한 구독을 만드는 Resource Manager 템플릿입니다. Azure Event Hubs에 이벤트를 전송합니다. |

[!INCLUDE [event-grid-message-headers](./includes/event-grid-message-headers.md)]


## <a name="rest-examples-for-put"></a>REST 예제(PUT의 경우)


### <a name="event-hub"></a>이벤트 허브

```json
{
  "properties": 
  {
    "destination": 
    {
      "endpointType": "EventHub",
      "properties": 
      {
        "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
      }
    },
    "eventDeliverySchema": "EventGridSchema"
  }
}
```

### <a name="event-hub---delivery-with-managed-identity"></a>이벤트 허브 - 관리 ID로 전달

```json
{
  "properties": {
    "deliveryWithResourceIdentity": 
    {
      "identity": 
      {
        "type": "SystemAssigned"
      },
      "destination": 
      {
        "endpointType": "EventHub",
        "properties": 
        {
          "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
        }
      }
    },
    "eventDeliverySchema": "EventGridSchema"
  }
}
```

## <a name="delivery-properties"></a>배달 속성
이벤트 구독을 사용하면 배달된 이벤트에 포함 되는 HTTP 헤더를 설정할 수 있습니다. 이 기능을 사용하여 대상에 필요한 사용자 지정 헤더를 설정할 수 있습니다. Azure Event Hubs에 전달 되는 이벤트에 사용자 지정 헤더를 설정할 수 있습니다.

이벤트 허브 내 특정 파티션에 이벤트를 게시해야 하는 경우 이벤트 구독에서 `ParitionKey` 속성을 정의하여 대상 이벤트 허브 파티션을 식별하는 파티션 키를 지정합니다.

| 헤더 이름 | 헤더 형식 |
| :-- | :-- |
|`PartitionKey` | 정적 |

자세한 내용은 [사용자 지정 배달 속성](delivery-properties.md)을 참조하세요. 

## <a name="next-steps"></a>다음 단계
지원되는 이벤트 처리기 목록은 [이벤트 처리기](event-handlers.md) 문서를 참조하세요. 
