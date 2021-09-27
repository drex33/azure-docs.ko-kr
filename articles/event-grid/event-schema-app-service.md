---
title: Event Grid 원본으로서의 Azure App Service
description: 이 문서에서는 Event Grid 이벤트 원본으로 Azure App Service를 사용하는 방법을 설명합니다. 스키마와 자습서 및 방법 문서 링크를 제공합니다.
author: jasonfreeberg
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: jafreebe
ms.openlocfilehash: 92667242b52ee58383f68e5b032eb5580cad2826
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647445"
---
# <a name="azure-app-service-as-an-event-grid-source"></a>Event Grid 원본으로서의 Azure App Service

이 문서에서는 Azure App Service 이벤트의 속성과 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요. 또한 이벤트 원본으로 Azure App Service를 사용하기 위한 빠른 시작 및 자습서의 목록을 제공합니다.

## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

Azure App Service는 다음과 같은 이벤트 유형을 내보냅니다.

|    이벤트 유형                                             |    Description                                                     |
|-----------------------------------------------------------|--------------------------------------------------------------------|
|    Microsoft.Web/sites.BackupOperationStarted             |    백업이 시작하면 트리거됨                             |
|    Microsoft.Web/sites.BackupOperationCompleted           |    백업이 완료되면 트리거됨                           |
|    Microsoft.Web/sites.BackupOperationFailed              |    백업이 실패하면 트리거됨                              |
|    Microsoft.Web/sites.RestoreOperationStarted            |    백업으로부터 복원이 시작하면 트리거됨        |
|    Microsoft.Web/sites.RestoreOperationCompleted          |    백업으로부터 복원이 완료되면 트리거됨      |
|    Microsoft.Web/sites.RestoreOperationFailed             |    백업으로부터 복원이 실패하면 트리거됨         |
|    Microsoft.Web/sites.SlotSwapStarted                    |    슬롯 교환이 시작하면 트리거됨                          |
|    Microsoft.Web/sites.SlotSwapCompleted                  |    슬롯 교환이 완료되면 트리거됨                      |
|    Microsoft.Web/sites.SlotSwapFailed                     |    슬롯 교환이 실패하면 트리거됨                           |
|    Microsoft.Web/sites.SlotSwapWithPreviewStarted         |    미리 보기가 있는 슬롯 교환이 시작하면 트리거됨           |
|    Microsoft.Web/sites.SlotSwapWithPreviewCancelled       |    미리 보기가 있는 슬롯 교환이 취소되면 트리거됨    |
|    Microsoft.Web/sites.AppUpdated.Restarted               |    사이트가 다시 시작되면 트리거됨                      |
|    Microsoft.Web/sites.AppUpdated.Stopped                 |    사이트가 중지되면 트리거됨                          |
|    Microsoft.Web/sites.AppUpdated.ChangedAppSettings      |    사이트의 앱 설정이 변경되면 트리거됨             |
|    Microsoft.Web/serverfarms.AppServicePlanUpdated        |    App Service 계획이 업데이트되면 트리거됨                 |

## <a name="properties-common-to-all-events"></a>모든 이벤트에 공통된 속성

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)
이벤트가 트리거될 때 Event Grid 서비스는 해당 이벤트에 대한 데이터를 구독 엔드포인트로 보냅니다.
이 섹션에는 각 이벤트에 대한 데이터가 어떻게 표시되는지 예가 포함되어 있습니다. 각 이벤트에는 다음과 같은 최상위 데이터가 있습니다.

|     속성          |     유형     |     Description                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `topic`              |    문자열    |    이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다.                                      |
|    `subject`            |    문자열    |    게시자가 정의한 이벤트 주체에 대한 경로입니다.                                                                                              |
|    `eventType`          |    문자열    |    이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다.                                                                                  |
|    `eventTime`          |    문자열    |    공급자의 UTC 시간을 기준으로 이벤트가 생성된 시간입니다.                                                                         |
|    `id`                 |    문자열    |    이벤트에 대한 고유 식별자입니다.                                                                                                            |
|    `data`               |    object    |    Blob Storage 이벤트 데이터입니다.                                                                                                                    |
|    `dataVersion`        |    문자열    |    데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다.                                                          |
|    `metadataVersion`    |    문자열    |    이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다.    |

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

이벤트가 트리거될 때 Event Grid 서비스는 해당 이벤트에 대한 데이터를 구독 엔드포인트로 보냅니다.
이 섹션에는 각 이벤트에 대한 데이터가 어떻게 표시되는지 예가 포함되어 있습니다. 각 이벤트에는 다음과 같은 최상위 데이터가 있습니다.

|     속성          |     유형     |     Description                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `source`              |    문자열    |    이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다.                                      |
|    `subject`            |    문자열    |    게시자가 정의한 이벤트 주체에 대한 경로입니다.                                                                                              |
|    `type`          |    문자열    |    이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다.                                                                                  |
|    `time`          |    문자열    |    공급자의 UTC 시간을 기준으로 이벤트가 생성된 시간입니다.                                                                         |
|    `id`                 |    문자열    |    이벤트에 대한 고유 식별자입니다.                                                                                                            |
|    `data`               |    object    |    Blob Storage 이벤트 데이터입니다.                                                                                                                    |
| `specversion` | 문자열 | CloudEvents 스키마 사양 버전입니다. |

---

## <a name="example-events"></a>예제 이벤트

### <a name="backupoperationstarted-backupoperationcompleted-backupoperationfailed"></a>BackupOperationStarted, BackupOperationCompleted, BackupOperationFailed

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.BackupOperationStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```
# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.BackupOperationStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
    "specversion": "1.0"
}
```

---

데이터 개체에는 다음 속성이 포함되어 있습니다.

|    속성                |    유형      |    설명                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    앱에 대한 작업의 세부 정보입니다.                                                                                       |
|    `action`                  |    문자열    |    작업의 동작 유형입니다.                                                                                   |
|    `name`                    |    문자열    |    이 이벤트가 발생한 웹 사이트의 이름입니다.                                                                          |
|    `clientRequestId`         |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 앱 서비스에서 생성한 클라이언트 요청 ID         |
|    `correlationRequestId`    |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 앱 서비스에서 생성한 상관 관계 요청 ID입니다.    |
|    `requestId`               |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 앱 서비스에서 생성한 요청 ID입니다.                |
|    `address`                 |    문자열    |    이 작업의 HTTP 요청 URL입니다.                                                                                |
|    `verb`                    |    문자열    |    이 작업의 HTTP 동사입니다.                                                                                       |

### <a name="restoreoperationstarted-restoreoperationcompleted-restoreoperationfailed"></a>RestoreOperationStarted, RestoreOperationCompleted, RestoreOperationFailed

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.RestoreOperationStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "POST"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.RestoreOperationStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "name": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "POST"
    },
    "specversion": "1.0"
}
```

---

데이터 개체에는 다음 속성이 포함되어 있습니다.

|    속성                |    유형      |    설명                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    앱에 대한 작업의 세부 정보입니다.                                                                                       |
|    `action`                  |    문자열    |    작업의 동작 유형입니다.                                                                                   |
|    `name`                    |    문자열    |    이 이벤트가 발생한 웹 사이트의 이름입니다.                                                                          |
|    `clientRequestId`         |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 앱 서비스에서 생성한 클라이언트 요청 ID         |
|    `correlationRequestId`    |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 앱 서비스에서 생성한 상관 관계 요청 ID입니다.    |
|    `requestId`               |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 앱 서비스에서 생성한 요청 ID입니다.                |
|    `address`                 |    문자열    |    이 작업의 HTTP 요청 URL입니다.                                                                                |
|    `verb`                    |    문자열    |    이 작업의 HTTP 동사입니다.                                                                                       |

### <a name="slotswapstarted-slotswapcompleted-slotswapfailed"></a>SlotSwapStarted, SlotSwapCompleted, SlotSwapFailed

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.SlotSwapStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.SlotSwapStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "specversion": "1.0"
}
```

---

데이터 개체에는 다음 속성이 포함되어 있습니다.

|    속성                |    유형      |    설명                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    앱에 대한 작업의 세부 정보입니다.                                                                                       |
|    `action`                 |    문자열    |    작업의 동작 유형입니다.                                                                                   |
|    `name`                    |    문자열    |    이 이벤트가 발생한 웹 사이트의 이름입니다.                                                                          |
|    `clientRequestId`         |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 앱 서비스에서 생성한 클라이언트 요청 ID         |
|    `correlationRequestId`    |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 앱 서비스에서 생성한 상관 관계 요청 ID입니다.    |
|   `requestId`               |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 앱 서비스에서 생성한 요청 ID입니다.                |
|    `address`                 |    문자열    |    이 작업의 HTTP 요청 URL입니다.                                                                                |
|    `verb`                    |    문자열    |    이 작업의 HTTP 동사입니다.                                                                                       |
|    `sourceSlot`              |    문자열    |    교환의 원본 슬롯입니다.                                                                                       |

### <a name="slotswapwithpreviewstarted-slotswapwithpreviewcancelled"></a>SlotSwapWithPreviewStarted, SlotSwapWithPreviewCancelled

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.SlotSwapWithPreviewStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.SlotSwapWithPreviewStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "name": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "specversion": "1.0"
}
```

---

데이터 개체에는 다음 속성이 포함되어 있습니다.

|    속성                |    유형      |    설명                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    앱에 대한 작업의 세부 정보입니다.                                                                                       |
|    `action`                 |    문자열    |    작업의 동작 유형입니다.                                                                                   |
|    `name`                    |    문자열    |    이 이벤트가 발생한 웹 사이트의 이름입니다.                                                                          |
|    `clientRequestId`         |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 앱 서비스에서 생성한 클라이언트 요청 ID         |
|    `correlationRequestId`    |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 앱 서비스에서 생성한 상관 관계 요청 ID입니다.    |
|    `requestId`               |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 앱 서비스에서 생성한 요청 ID입니다.                |
|    `address`                 |    문자열    |    이 작업의 HTTP 요청 URL입니다.                                                                                |
|    `verb`                    |    문자열    |    이 작업의 HTTP 동사입니다.                                                                                       |

### <a name="appupdatedrestarted-appupdatedstopped-appupdatedchangedappsettings"></a>AppUpdated.Restarted, AppUpdated.Stopped, AppUpdated.ChangedAppSettings

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)

```json
{
    "id": "b74ea56b-2a3f-4de5-a5d7-38e60c81cf23",
    "topic": "/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.AppUpdated",
    "eventTime": "2020-01-28T18:22:30.2760952Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Stopped"
        },
        "name": "<site-name>",
        "clientRequestId": "64a5e0aa-7cee-4ff1-9093-b9197b820014",
        "correlationRequestId": "25bb36a5-8f6c-4f04-b615-e9a0ee045756",
        "requestId": "f2e8eb3f-b190-42de-b99e-6acefe587374",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop",
        "verb": "POST"
    },
    "dataVersion": "1'",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

```json
{
    "id": "b74ea56b-2a3f-4de5-a5d7-38e60c81cf23",
    "source": "/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.AppUpdated",
    "time": "2020-01-28T18:22:30.2760952Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Stopped"
        },
        "name": "<site-name>",
        "clientRequestId": "64a5e0aa-7cee-4ff1-9093-b9197b820014",
        "correlationRequestId": "25bb36a5-8f6c-4f04-b615-e9a0ee045756",
        "requestId": "f2e8eb3f-b190-42de-b99e-6acefe587374",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop",
        "verb": "POST"
    },
    "specversion": "1.0"
}
```

---

데이터 개체의 속성은 다음과 같습니다.

|    속성                |    유형      |    설명                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    앱에 대한 작업의 세부 정보입니다.                                                                                       |
|    `action`                  |    문자열    |    작업의 동작 유형입니다.                                                                                   |
|    `name`                    |    문자열    |    이 이벤트가 발생한 웹 사이트의 이름입니다.                                                                          |
|    `clientRequestId`         |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 앱 서비스에서 생성한 클라이언트 요청 ID         |
|    `correlationRequestId`    |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 앱 서비스에서 생성한 상관 관계 요청 ID입니다.    |
|    `requestId`               |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 앱 서비스에서 생성한 요청 ID입니다.                |
|    `address`                 |    문자열    |    이 작업의 HTTP 요청 URL입니다.                                                                                |
|    `verb`                    |    문자열    |    이 작업의 HTTP 동사입니다.                                                                                       |

### <a name="serverfarmsappserviceplanupdated"></a>Serverfarms.AppServicePlanUpdated

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)

```json
{
    "id": "56501672-9150-40e1-893a-18420c7fdbf7",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
    "subject": "/Microsoft.Web/serverfarms/<plan-name>",
    "eventType": "Microsoft.Web.AppServicePlanUpdated",
    "eventTime": "2020-01-28T18:22:23.5516004Z",
    "data": {
        "serverFarmEventTypeDetail": {
            "stampKind": "Public",
            "action": "Updated",
            "status": "Started"
        },
        "serverFarmId": "0",
        "sku": {
            "name": "P1v2",
            "tier": "PremiumV2",
            "size": "P1v2",
            "family": "Pv2",
            "capacity": 1
        },
        "clientRequestId": "8f880321-a991-45c7-b743-6ff63fe4c004",
        "correlationRequestId": "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        "requestId": "b973a8e6-6949-4783-b44c-ac778be831bb",
        "address": "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        "verb": "PUT"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

```json
{
    "id": "56501672-9150-40e1-893a-18420c7fdbf7",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
    "subject": "/Microsoft.Web/serverfarms/<plan-name>",
    "type": "Microsoft.Web.AppServicePlanUpdated",
    "time": "2020-01-28T18:22:23.5516004Z",
    "data": {
        "serverFarmEventTypeDetail": {
            "stampKind": "Public",
            "action": "Updated",
            "status": "Started"
        },
        "serverFarmId": "0",
        "sku": {
            "name": "P1v2",
            "tier": "PremiumV2",
            "size": "P1v2",
            "family": "Pv2",
            "capacity": 1
        },
        "clientRequestId": "8f880321-a991-45c7-b743-6ff63fe4c004",
        "correlationRequestId": "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        "requestId": "b973a8e6-6949-4783-b44c-ac778be831bb",
        "address": "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        "verb": "PUT"
    },
    "specversion": "1.0"
}
```

---

데이터 개체의 속성은 다음과 같습니다.

|    속성                         |    유형      |    설명                                                                                                       |
|-------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appServicePlanEventTypeDetail`    |    object    |    App Service 계획에 대한 작업의 세부 정보입니다.                                                                          |
|    `stampKind`                        |    문자열    |    App Service 계획이 존재하는 환경의 종류입니다.                                                                     |
|    `action`                           |    문자열    |    App Service 계획에 대한 작업의 유형입니다.                                                                            |
|    `status`                           |    문자열    |    App Service 계획에 대한 작업의 상태입니다.                                                                   |
|    `sku`                              |    object    |    App Service 계획의 SKU입니다.                                                                                       |
|    `name`                             |    문자열    |    App Service 계획의 이름입니다.                                                                                      |
|    `Tier`                             |    문자열    |    App Service 계획의 계층입니다.                                                                                      |
|    `Size`                             |    문자열    |    App Service 계획의 크기입니다.                                                                                      |
|    `Family`                           |    문자열    |    App Service 계획의 제품군입니다.                                                                                        |
|    `Capacity`                         |    문자열    |    App Service 계획의 용량입니다.                                                                                      |
|    `action`                           |    문자열    |    작업의 동작 유형입니다.                                                                                   |
|    `name`                             |    문자열    |    이 이벤트가 발생한 웹 사이트의 이름입니다.                                                                          |
|    `clientRequestId`                  |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 앱 서비스에서 생성한 클라이언트 요청 ID         |
|    `correlationRequestId`             |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 앱 서비스에서 생성한 상관 관계 요청 ID입니다.    |
|    `requestId`                        |    문자열    |    이 이벤트를 트리거한 사이트 API 작업에 대해 앱 서비스에서 생성한 요청 ID입니다.                |
|    `address`                         |    문자열    |    이 작업의 HTTP 요청 URL입니다.                                                                                |
|    `verb`                             |    문자열    |    이 작업의 HTTP 동사입니다.                                                                                       |

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.