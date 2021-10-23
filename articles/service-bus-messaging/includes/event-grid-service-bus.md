---
title: 포함 파일
description: 포함 파일
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/20/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 491d8cb7cc11d0a904ad70fac8d54c9c6e16670d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130287434"
---
## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

Service Bus는 다음과 같은 이벤트 유형을 내보냅니다.

| 이벤트 유형 | Description |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | 큐 또는 구독에 활성 메시지가 있고 수신 대기 중인 수신기가 없는 경우 발생합니다. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListeners | 배달 못한 편지 큐에 활성 메시지가 있고 수신 대기 중인 수신기가 없는 경우 발생합니다. |
| Microsoft.ServiceBus.ActiveMessagesAvailablePeriodicNotifications | 특정 큐 또는 구독에 활성 수신기가 있더라도 큐 또는 구독에 활성 메시지가 있는 경우 30초마다 발생합니다. 활성 메시지 수가 0에서 큐 또는 구독에 대한 양수 값으로 전환될 때도 발생합니다. |
| Microsoft.ServiceBus.DeadletterMessagesAvailablePeriodicNotifications | 특정 큐 또는 구독의 비활성 문자 엔터티에 활성 수신기가 있더라도 큐 또는 구독의 데드 문자 엔터티에 메시지가 있는 경우 30초마다 발생합니다. 또한 데드 문자 메시지 수가 큐 또는 구독의 데드 문자 엔터티에 대해 0에서 양수 값으로 전환될 때 발생합니다. | 

## <a name="example-event"></a>예제 이벤트

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)

### <a name="active-messages-available-with-no-listeners"></a>수신기 없이 사용 가능한 활성 메시지
큐 또는 구독에 활성 메시지가 있고 수신 대기 중인 수신기가 없는 경우 이 이벤트가 생성됩니다.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="deadletter-messages-available-with-no-listeners"></a>수신기 없이 사용 가능한 배달 못한 편지 메시지

배달 못한 편지 큐 이벤트에 대한 스키마 역시 비슷합니다. 배달 못한 편지 큐마다 메시지는 있고 활성 수신기는 없는 이벤트가 하나 이상 발생합니다.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="active-messages-available-periodic-notifications"></a>활성 메시지 사용 가능한 정기 알림
특정 큐 또는 구독에 활성 메시지가 있는 경우 해당 특정 큐 또는 구독에 활성 메시지가 있는 경우에도 이 이벤트가 정기적으로 발생합니다.

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="deadletter-messages-available-periodic-notifications"></a>배달 못한 편지 메시지 사용 가능한 정기 알림
특정 큐 또는 구독에 활성 메시지가 있는 경우 해당 특정 큐 또는 구독의 배달 못한 편지 엔터티에 배달 못한 편지 메시지가 있는 경우에도 이 이벤트가 정기적으로 발생합니다.

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

### <a name="active-messages-available-with-no-listeners"></a>수신기 없이 사용 가능한 활성 메시지
큐 또는 구독에 활성 메시지가 있고 수신 대기 중인 수신기가 없는 경우 이 이벤트가 생성됩니다.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "type": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

#### <a name="deadletter-messages-available-with-no-listeners"></a>수신기 없이 사용 가능한 배달 못한 편지 메시지

배달 못한 편지 큐 이벤트에 대한 스키마 역시 비슷합니다. 배달 못한 편지 큐마다 메시지는 있고 활성 수신기는 없는 이벤트가 하나 이상 발생합니다.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "type": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListeners",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

#### <a name="active-messages-available-periodic-notifications"></a>활성 메시지 사용 가능한 정기 알림
특정 큐 또는 구독에 활성 메시지가 있는 경우 해당 특정 큐 또는 구독에 활성 메시지가 있는 경우에도 이 이벤트가 정기적으로 발생합니다.

```json
[{
  "source": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "type": "Microsoft.ServiceBus.ActiveMessagesAvailablePeriodicNotifications",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

#### <a name="deadletter-messages-available-periodic-notifications"></a>배달 못한 편지 메시지 사용 가능한 정기 알림
특정 큐 또는 구독에 활성 메시지가 있는 경우 해당 특정 큐 또는 구독의 배달 못한 편지 엔터티에 배달 못한 편지 메시지가 있는 경우에도 이 이벤트가 정기적으로 발생합니다.

```json
[{
  "source": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "type": "Microsoft.ServiceBus.DeadletterMessagesAvailablePeriodicNotifications",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

---



### <a name="event-properties"></a>이벤트 속성

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)
이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 형식 | Description |
| -------- | ---- | ----------- |
| `topic` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 게시자가 정의한 이벤트 주체의 경로입니다. |
| `eventType` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `eventTime` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `data` | object | Blob Storage 이벤트 데이터입니다. |
| `dataVersion` | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| `metadataVersion` | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 형식 | Description |
| -------- | ---- | ----------- |
| `source` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 게시자가 정의한 이벤트 주체의 경로입니다. |
| `type` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `time` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `data` | object | Blob Storage 이벤트 데이터입니다. |
| `specversion` | 문자열 | CloudEvents 스키마 사양 버전입니다. |

---

데이터 개체의 속성은 다음과 같습니다.

| 속성 | 형식 | Description |
| -------- | ---- | ----------- |
| `namespaceName` | 문자열 | 리소스가 있는 Service Bus 네임스페이스입니다. |
| `requestUri` | 문자열 | 이벤트를 내보내는 특정 큐 또는 구독의 URI입니다. |
| `entityType` | 문자열 | 이벤트를 내보내는 Service Bus 엔터티의 유형(큐 또는 구독)입니다. |
| `queueName` | 문자열 | 큐를 구독하는 경우 활성 메시지가 있는 큐입니다. 토픽/구독을 사용하는 경우 값이 null입니다. |
| `topicName` | 문자열 | 활성 메시지가 있는 Service Bus 구독이 속하는 토픽입니다. 큐를 사용하는 경우 null 값입니다. |
| `subscriptionName` | 문자열 | 활성 메시지가 있는 Service Bus 구독입니다. 큐를 사용하는 경우 null 값입니다. |
