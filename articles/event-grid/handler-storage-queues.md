---
title: Azure Event Grid 이벤트에 대한 이벤트 처리기로서의 스토리지 큐
description: Azure Event Grid 이벤트에 대한 이벤트 처리기로 Azure 스토리지 큐를 사용하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 09/28/2021
ms.openlocfilehash: 12314aaad4204892523ab88166bf86a80c35c939
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217791"
---
# <a name="storage-queue-as-an-event-handler-for-azure-event-grid-events"></a>Azure Event Grid 이벤트에 대한 이벤트 처리기로서의 스토리지 큐
이벤트 처리기는 이벤트가 전송된 위치입니다. 처리기는 이벤트를 처리하기 위한 추가 작업을 수행합니다. 이벤트를 처리하도록 여러 Azure 서비스가 자동으로 구성되며, **Azure Queue Storage** 가 그 중 하나입니다. 

**Queue Storage** 를 사용하여 끌어와야 할 이벤트를 수신합니다. 응답 시간이 너무 오래 걸리는 장기 실행 프로세스가 있는 경우 Queue Storage를 사용할 수 있습니다. 이벤트를 Queue Storage를 보내면 앱이 자체 일정에 따라 프로세스를 끌어와서 처리할 수 있습니다.

## <a name="tutorials"></a>자습서
Queue Storage를 이벤트 처리기로 사용하는 예제는 다음 학습서를 참조하세요. 

|제목  |Description  |
|---------|---------|
| [빠른 시작: Azure CLI 및 Event Grid를 사용하여 Azure Queue Storage로 사용자 지정 이벤트 라우팅](custom-event-to-queue-storage.md) | Queue Storage에 사용자 지정 이벤트를 보내는 방법을 설명합니다. |

## <a name="rest-examples-for-put"></a>REST 예제(PUT의 경우)

### <a name="storage-queue-as-the-event-handler"></a>이벤트 처리기로서의 스토리지 큐

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "StorageQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>",
                "queueName": "<QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="storage-queue-as-the-event-handler---delivery-with-managed-identity"></a>이벤트 처리기로서의 스토리지 큐 - 관리 ID로 전달

```json
{
    "properties": 
    {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "StorageQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>",
                    "queueName": "<QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="storage-queue-as-the-event-handler-with-a-deadletter-destination"></a>배달 못한 대상이 있는 이벤트 처리기로서의 저장소 큐

```json
{
    "name": "",
    "properties": 
    {
        "destination": 
        {
            "endpointType": "StorageQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DESTINATION STORAGE>",
                "queueName": "queue1"
            }
        },
        "eventDeliverySchema": "EventGridSchema",
        "deadLetterDestination": 
        {
            "endpointType": "StorageBlob",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DEADLETTER STORAGE>",
                "blobContainerName": "test"
            }
        }
    }
}
```

### <a name="storage-queue-as-the-event-handler-with-a-deadletter-destination---managed-identity"></a>배달 못한 대상이 있는 이벤트 처리기로서의 저장소 큐 - 관리 ID

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "StorageQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DESTINATION STORAGE>",
                "queueName": "queue1"
            }
        },
        "eventDeliverySchema": "EventGridSchema",
        "deadLetterWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "deadLetterDestination": 
            {
                "endpointType": "StorageBlob",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<DEADLETTER STORAGE>",
                    "blobContainerName": "test"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>다음 단계
지원되는 이벤트 처리기 목록은 [이벤트 처리기](event-handlers.md) 문서를 참조하세요. 
