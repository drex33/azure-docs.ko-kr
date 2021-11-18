---
title: 이벤트 스키마 — IoT Edge의 Azure Event Grid | Microsoft Docs
description: IoT Edge의 Event Grid에 있는 이벤트 스키마입니다.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: accf7e5a30b07cbfe6a95bc33eb00a5e3aa23998
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716394"
---
# <a name="event-schemas"></a>이벤트 스키마

Event Grid 모듈에서는 JSON 형식의 이벤트를 허용하고 전송합니다. 현재 Event Grid에서 지원하는 스키마는 세 가지가 있습니다.

* **EventGridSchema**
* **CustomSchema**
* **CloudEventSchema**

토픽을 만드는 동안 게시자가 준수해야 하는 스키마를 구성할 수 있습니다. 지정하지 않으면 기본적으로 **EventGridSchema** 로 설정됩니다. 필요한 스키마를 준수하지 않는 이벤트는 거부됩니다.

구독자는 이벤트를 전송할 스키마를 구성할 수도 있습니다. 지정하지 않으면 기본적으로 토픽의 스키마로 설정됩니다.
현재 구독자 전송 스키마는 해당 토픽의 입력 스키마와 일치해야 합니다. 

## <a name="eventgrid-schema"></a>EventGrid 스키마

EventGrid 스키마는 게시 엔터티가 준수해야 하는 필수 속성 세트로 구성됩니다. 각 게시자는 최상위 필드를 채워야 합니다.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

### <a name="eventgrid-schema-properties"></a>EventGrid 스키마 속성

모든 이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 형식 | 필수 | Description |
| -------- | ---- | ----------- |-----------
| 토픽 | 문자열 | No | 게시된 토픽과 일치해야 합니다. 지정하지 않으면 Event Grid가 게시된 토픽의 이름으로 채웁니다. |
| subject | 문자열 | 예 | 게시자가 정의한 이벤트 주체의 경로입니다. |
| eventType | 문자열 | 예 | 이 이벤트 원본의 이벤트 유형입니다(예: BlobCreated). |
| eventTime | 문자열 | 예 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| ID | 문자열 | No | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | 예 | 게시 엔터티와 관련된 이벤트 데이터를 캡처하는 데 사용됩니다. |
| dataVersion | 문자열 | 예 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | 문자열 | No | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

### <a name="example--eventgrid-schema-event"></a>예제 — EventGrid 스키마 이벤트

```json
[
  {
    "id": "1807",
    "eventType": "recordInserted",
    "subject": "myapp/vehicles/motorcycles",
    "eventTime": "2017-08-10T21:03:07+00:00",
    "data": {
      "make": "Ducati",
      "model": "Monster"
    },
    "dataVersion": "1.0"
  }
]
```

## <a name="customevent-schema"></a>CustomEvent 스키마

사용자 지정 스키마에는 EventGrid 스키마와 같이 적용되는 필수 속성이 없습니다. 게시 엔터티는 이벤트 스키마를 완전히 제어할 수 있습니다. 최대 유연성을 제공하며 이벤트 기반 시스템이 이미 갖춰져 있고 기존 이벤트를 다시 사용하거나 특정 스키마에 연결되지 않도록 하려는 시나리오를 지원합니다.

### <a name="custom-schema-properties"></a>사용자 지정 스키마 속성

필수 속성이 없습니다. 페이로드를 결정하는 것은 게시 엔터티입니다.

### <a name="example--custom-schema-event"></a>예제 — 사용자 지정 스키마 이벤트

```json
[
  {
    "eventdata": {
      "make": "Ducati",
      "model": "Monster"
    }
  }
]
```

## <a name="cloudevent-schema"></a>CloudEvent 스키마

Event Grid는 위의 스키마 외에도 기본적으로 [CloudEvents JSON 스키마](https://github.com/cloudevents/spec/blob/main/cloudevents/formats/json-format.md)의 이벤트를 지원합니다. CloudEvents는 이벤트 데이터를 설명하는 공개 사양입니다. 이벤트를 게시하고 사용하는 데 필요한 일반 이벤트 스키마를 제공하여 상호 운용성을 간소화합니다. [CNCF](https://www.cncf.io/)의 일부이며 현재 사용할 수 있는 버전은 1.0-rc1입니다.

### <a name="cloudevent-schema-properties"></a>CloudEvent 스키마 속성

필수 봉투 속성에 대한 [CloudEvents 사양](https://github.com/cloudevents/spec/blob/main/cloudevents/formats/json-format.md#3-envelope)을 참조하세요.

### <a name="example--cloud-event"></a>예제 — 클라우드 이벤트
```json
[{
       "id": "1807",
       "type": "recordInserted",
       "source": "myapp/vehicles/motorcycles",
       "time": "2017-08-10T21:03:07+00:00",
       "datacontenttype": "application/json",
       "data": {
            "make": "Ducati",
            "model": "Monster"
        },
        "dataVersion": "1.0",
        "specVersion": "1.0-rc1"
}]
```
