---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/29/2021
ms.author: glenga
ms.custom: fasttrack-edit
ms.openlocfilehash: c9fba484463680e53b3aa6f32f37fa8326332c92
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133289128"
---
이 표는 Azure Functions 런타임의 주요 버전에서 지원되는 바인딩을 보여 줍니다.


| 유형 | 1.x | 2.x 이상<sup>1</sup> | 트리거 | 입력 | 출력 |
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|
| [Azure Cosmos DB](../articles/azure-functions/functions-bindings-cosmosdb-v2.md)               |✔|✔|✔|✔|✔|
| [Azure SQL(미리 보기)](../articles/azure-functions/functions-bindings-azure-sql.md)               | |✔| |✔|✔|
| [Dapr](https://github.com/dapr/azure-functions-extension)<sup>3</sup>             | |✔|✔|✔|✔|
| [Event Grid](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| |✔|
| [Event Hubs](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|
| [HTTP 및 웹후크](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
| [IoT Hub](../articles/azure-functions/functions-bindings-event-iot.md)             |✔|✔|✔| |✔|
| [Kafka](https://github.com/azure/azure-functions-kafka-extension)<sup>2</sup>             | |✔|✔| |✔|
| [Mobile Apps](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔| | |✔|✔|
| [Notification Hubs](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔|✔| |✔|
| [RabbitMQ](../articles/azure-functions/functions-bindings-rabbitmq.md)<sup>2</sup>             | |✔|✔| |✔|
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [Service Bus](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|
| [SignalR](../articles/azure-functions/functions-bindings-signalr-service.md)             | |✔| |✔|✔|
| [Table Storage](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔| |✔|✔|
| [타이머](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|

<sup>1</sup> 버전 2.x 런타임부터는 HTTP 및 Timer를 제외한 모든 바인딩이 등록되어야 합니다. [바인딩 확장 등록](../articles/azure-functions/functions-bindings-register.md)을 참조하세요.

<sup>2</sup> 트리거는 소비 계획에서 지원되지 않습니다. [런타임 기반 트리거](../articles/azure-functions/functions-networking-options.md#premium-plan-with-virtual-network-triggers)가 필요합니다.

<sup>3</sup> Kubernetes, IoT Edge 및 기타 자체 호스팅 모드에서만 지원됩니다.
