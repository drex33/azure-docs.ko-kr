---
title: Azure Event Grid 이벤트에 대한 이벤트 처리기로서의 웹후크
description: Azure Event Grid 이벤트에 대한 이벤트 처리기로 웹후크를 사용하는 방법을 설명합니다. Azure Automation Runbook 및 논리 앱은 웹후크를 통해 이벤트 처리기로 지원됩니다.
ms.topic: conceptual
ms.date: 09/15/2021
ms.openlocfilehash: 4f790c6a4ced30d0a0e34cb594581aae09ed1004
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128550477"
---
# <a name="webhooks-automation-runbooks-logic-apps-as-event-handlers-for-azure-event-grid-events"></a>Azure Event Grid 이벤트에 대한 이벤트 처리기로서의 웹후크, Automation Runbook, Logic Apps
이벤트 처리기는 이벤트가 전송된 위치입니다. 처리기는 이벤트를 처리하기 위한 추가 작업을 수행합니다. 여러 Azure 서비스는 이벤트를 처리하도록 자동으로 구성됩니다. 이벤트를 처리하기 위해 WebHook를 사용할 수도 있습니다. WebHook는 이벤트를 처리하기 위해 Azure에서 호스트될 필요가 없습니다. Event Grid는 HTTPS 웹후크 엔드포인트만을 지원합니다.

> [!NOTE]
> - Azure Automation Runbook 및 논리 앱은 웹후크를 통해 이벤트 처리기로 지원됩니다. 
> - **웹 후크** 를 **엔드포인트 형식** 으로 사용하여 Azure 함수를 이벤트 처리기로 구성하는 경우에도 **Azure 함수** 를 엔드포인트 형식으로 사용합니다. 자세한 내용은 [이벤트 처리기로 Azure 함수 사용](handler-functions.md)을 참조하세요.

## <a name="webhooks"></a>Webhook
웹후크를 이벤트 처리기로 사용하는 방법에 대한 개요 및 예제는 다음 문서를 참조하세요. 

|제목  |Description  |
|---------|---------|
| 빠른 시작: [Azure CLI](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md) 및 [포털](custom-event-quickstart-portal.md)을 사용하여 사용자 지정 이벤트 만들기 및 라우팅 | 사용자 지정 이벤트를 WebHook로 전송하는 방법을 보여줍니다. |
| 빠른 시작: [Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) 및 [포털](blob-event-quickstart-portal.md)을 사용하여 Blob Storage 이벤트를 사용자 지정 웹 엔드포인트로 라우팅 | Blob Storage 이벤트를 WebHook로 전송하는 방법을 보여줍니다. |
| [빠른 시작: 컨테이너 레지스트리 이벤트 전송](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure CLI를 사용하여 Container Registry 이벤트를 전송하는 방법을 보여줍니다. |
| [개요: HTTP 엔드포인트에 대한 이벤트 수신](receive-events.md) | 이벤트 구독으로부터 이벤트를 수신하기 위해 HTTP 엔드포인트의 유효성을 검사하고, 이벤트를 수신하고 역직렬화하는 방법을 설명합니다. |


## <a name="azure-automation"></a>Azure Automation
Azure Automation Runbooks를 사용하여 이벤트를 처리할 수 있습니다. 자동화된 Runbooks를 사용하여 이벤트를 처리하는 작업은 웹후크를 통해 지원됩니다. Runbook용 웹후크를 만든 다음, 웹후크 처리기를 사용합니다. 예제는 다음 자습서를 참조하세요. 

|제목  |Description  |
|---------|---------|
|[자습서: Event Grid 및 Microsoft Teams를 사용한 Azure Automation](ensure-tags-exists-on-new-virtual-machines.md) |이벤트를 전송 하는 가상 머신을 만듭니다. 이벤트는 가상 머신에 태그를 지정하는 Automation Runbook을 트리거하고, Microsoft 팀 채널에 전송되는 메시지를 트리거합니다. |


## <a name="logic-apps"></a>Logic Apps
**Logic Apps** 를 사용하여 Event Grid 이벤트를 처리하는 비즈니스 프로세스를 구현합니다. 이 시나리오에서는 웹후크를 명시적으로 만들지 않습니다. 웹후크는 Event Grid에서 이벤트를 처리하도록 논리 앱을 구성할 때 자동으로 생성됩니다. 예제는 다음 자습서를 참조하세요. 

|제목  |Description  |
|---------|---------|
| [자습서: Azure Event Grid 및 Logic Apps를 사용하여 가상 머신 변경 모니터링](monitor-virtual-machine-changes-event-grid-logic-app.md) | 논리 앱은 가상 머신의 변경 내용을 모니터링하고 이러한 변경에 대한 이메일을 보냅니다. |
| [자습서: Logic Apps를 사용하여 Azure IoT Hub 이벤트에 관한 이메일 알림 보내기](publish-iot-hub-events-to-logic-apps.md) | 논리 앱은 사용자의 IoT Hub에 디바이스가 추가될 때마다 알림 이메일을 보냅니다. |
| [자습서: Azure Functions 및 Azure Logic Apps를 사용하여 Azure Event Grid를 통해 받은 Azure Service Bus 이벤트에 응답](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid는 Service Bus 토픽의 메시지를 함수 앱 및 논리 앱에 전송합니다. |

## <a name="rest-example-for-put"></a>REST 예제(PUT의 경우)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "WebHook",
            "properties": 
            {
                "endpointUrl": "<WEB HOOK URL>",
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>다음 단계
지원되는 이벤트 처리기 목록은 [이벤트 처리기](event-handlers.md) 문서를 참조하세요. 
