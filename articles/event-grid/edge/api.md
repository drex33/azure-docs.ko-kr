---
title: REST API - Azure Event Grid IoT Edge | Microsoft Docs
description: Event Grid on IoT Edge의 REST API.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: d9e634597d82b6cac52237f0c800cdfc33bc883a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635416"
---
# <a name="rest-api"></a>REST API
이 문서에서는 IoT Edge의 Azure Event Grid에 있는 REST API에 대해 설명합니다.

## <a name="common-api-behavior"></a>일반적인 API 동작

### <a name="base-url"></a>기준 URL
IoT Edge의 Event Grid에는 HTTP(포트 5888) 및 HTTPS(포트 4438)를 통해 노출된 다음 API가 있습니다.

* HTTP의 기준 URL: http://eventgridmodule:5888
* HTTPS의 기준 URL: https://eventgridmodule:4438

### <a name="request-query-string"></a>요청 쿼리 문자열
모든 API 요청에는 다음 쿼리 문자열 매개 변수가 있어야 합니다.

`?api-version=2019-01-01-preview`

### <a name="request-content-type"></a>요청 콘텐츠 형식
모든 API 요청에는 **Content-Type** 이 있어야 합니다.

**EventGridSchema** 또는 **CustomSchema** 의 경우 Content-Type의 값은 다음 값 중 하나일 수 있습니다.

`Content-Type: application/json`

`Content-Type: application/json; charset=utf-8`

구조적 모드의 **CloudEventSchemaV1_0** 의 경우 Content-Type의 값은 다음 값 중 하나일 수 있습니다.

`Content-Type: application/cloudevents+json`
    
`Content-Type: application/cloudevents+json; charset=utf-8`
    
`Content-Type: application/cloudevents-batch+json`
    
`Content-Type: application/cloudevents-batch+json; charset=utf-8`

이진 모드의 **CloudEventSchemaV1_0** 의 경우 [설명서](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md)에서 세부 정보를 참조하십시오.

### <a name="error-response"></a>오류 응답
모든 API는 다음 페이로드가 있는 오류를 반환합니다.

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>토픽 관리

### <a name="put-topic-create--update"></a>토픽 배치(만들기/업데이트)

**요청**: ``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**페이로드**:

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**요청**: HTTP 200

**페이로드**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>토픽 가져오기

**요청**: ``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**요청**: HTTP 200

**페이로드**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>모든 토픽 가져오기

**요청**: ``` GET /topics?api-version=2019-01-01-preview ```

**요청**: HTTP 200

**페이로드**:
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>토픽 삭제

**요청**: ``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**응답**: HTTP 200, 빈 페이로드

## <a name="manage-event-subscriptions"></a>이벤트 구독 관리
이 섹션의 샘플은 `EndpointType=Webhook;`을 사용합니다. `EndpointType=EdgeHub / EndpointType=EventGrid`의 json 샘플은 다음 섹션에 있습니다. 

### <a name="put-event-subscription-create--update"></a>이벤트 구독 배치(만들기/업데이트)

**요청**: ``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**페이로드**:
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "persistencePolicy": "true",
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**요청**: HTTP 200

**페이로드**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>이벤트 구독 가져오기

**요청**: ``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**요청**: HTTP 200

**페이로드**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>이벤트 구독 가져오기

**요청**: ``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**요청**: HTTP 200

**페이로드**:
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>이벤트 구독 삭제

**요청**: ``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**응답**: HTTP 200, 페이로드 없음


## <a name="publish-events-api"></a>이벤트 API 게시

### <a name="send-batch-of-events-in-event-grid-schema"></a>이벤트 일괄 처리 보내기(Event Grid 스키마)

**요청**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**응답**: HTTP 200, 빈 페이로드


**페이로드 필드 설명**
- ```Id```는 필수입니다. 호출자가 입력한 어떤 문자열 값이든 될 수 있습니다. Event Grid는 이 필드에서 중복을 감지하거나 의미 체계를 적용하지 않습니다.
- ```Topic```은 선택 사항이지만 지정할 경우 요청 URL의 topic_name이 일치해야 합니다.
- ```Subject```는 필수이며 임의의 문자열 값일 수 있습니다.
- ```EventType```은 필수이며 임의의 문자열 값일 수 있습니다.
- ```EventTime```은 필수이며 유효성을 검사하지 않지만 적절한 DateTime이어야 합니다.
- ```DataVersion```는 필수입니다.
- ```MetadataVersion```은 선택 사항이며 지정할 경우 ```"1"``` 값이 포함된 문자열이어야 합니다.
- ```Data```는 선택 사항이며 임의의 JSON 토큰(숫자, 문자열, 부울, 배열, 개체)일 수 있습니다.

### <a name="send-batch-of-events-in-custom-schema"></a>이벤트 배치 보내기(사용자 지정 스키마)

**요청**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**응답**: HTTP 200, 빈 페이로드


**페이로드 제한 사항**
- 이벤트 배열이어야 합니다.
- 각 배열 항목은 JSON 개체여야 합니다.
- 기타 제약 조건은 없습니다(페이로드 크기 제외).

## <a name="examples"></a>예

### <a name="set-up-topic-with-eventgrid-schema"></a>EventGrid 스키마로 토픽 설정
**eventgridschema** 에 게시할 이벤트에 필요한 토픽을 설정합니다.

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>사용자 지정 스키마로 토픽 설정
`customschema`에 게시할 이벤트에 필요한 토픽을 설정합니다.

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>클라우드 이벤트 스키마로 토픽 설정
`cloudeventschema`에 게시할 이벤트에 필요한 토픽을 설정합니다.

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>WebHook을 대상으로, eventgridschema에 게시될 이벤트 설정
이 대상 형식을 사용하여 (HTTP 엔드포인트를 호스팅하는) 다른 모듈이나 네트워크/인터넷의 모든 HTTP 주소 지정 가능 엔드포인트로 이벤트를 보냅니다.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

`endpointUrl` 특성의 제약 조건:
- null이 아니어야 합니다.
- 절대 URL이어야 합니다.
- EventGridModule 설정에서 outbound__webhook__httpsOnly가 true로 설정된 경우 HTTPS 전용이어야 합니다.
- outbound__webhook__httpsOnly가 false로 설정된 경우 HTTP나 HTTPS여야 합니다.

`eventDeliverySchema` 속성의 제약 조건:
- 구독 토픽의 입력 스키마와 일치해야 합니다.
- null일 수 있습니다. 기본값은 토픽의 입력 스키마로 설정됩니다.

### <a name="set-up-iot-edge-as-destination"></a>IoT Edge를 대상으로 설정

이 대상을 사용하여 이벤트를 IoT Edge 허브로 보내고 에지 허브의 라우팅/필터링/전달 하위 시스템에 연결합니다.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>Event Grid Cloud를 대상으로 설정

이 대상을 사용하여 이벤트를 클라우드의 Event Grid(Azure)로 보냅니다. 에지에 이벤트 구독을 만들기 전에 이벤트를 보낼 클라우드에서 사용자 토픽을 설정해야 합니다.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

EndpointUrl:
- null이 아니어야 합니다.
- 절대 URL이어야 합니다.
- 경로 `/api/events`는 요청 URL 경로에 정의되어 있어야 합니다.
- 쿼리 문자열에 `api-version=2018-01-01`이 있어야 합니다.
- EventGridModule 설정에서 outbound__eventgrid__httpsOnly가 true로 설정된 경우(기본값은 true) HTTPS 전용이어야 합니다.
- outbound__eventgrid__httpsOnly가 false로 설정되면 HTTP나 HTTPS일 수 있습니다.
- outbound__eventgrid__allowInvalidHostnames가 false로 설정된 경우(기본값은 false) 다음 엔드포인트 중 하나를 대상으로 해야 합니다.
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- null이 아니어야 합니다.

TopicName:
- Subscription.EventDeliverySchema가 EventGridSchema로 설정된 경우 이 필드의 값은 클라우드의 Event Grid로 전달되기 전에 모든 이벤트의 토픽 필드에 배치됩니다.
- Subscription.EventDeliverySchema가 CustomEventSchema로 설정된 경우 이 속성은 무시되고 사용자 지정 이벤트 페이로드는 수신된 그대로 전달됩니다.

## <a name="set-up-event-hubs-as-a-destination"></a>Event Hubs를 대상으로 설정

Event Hubs에 게시하려면 `endpointType`을 `eventHub`로 설정하고 다음을 입력합니다.

* connectionString: 공유 액세스 정책을 통해 생성된 특정 대상 Event Hubs 연결 문자열입니다.

    >[!NOTE]
    > 연결 문자열은 엔터티와 관련되어 있어야 합니다. 네임스페이스 연결 문자열은 사용할 수 없습니다. Azure Portal에서 게시하려는 특정 Event Hubs로 이동하고 **공유 액세스 정책** 을 클릭하여 새 엔터티 관련 연결 문자열을 생성하여 엔터티 관련 연결 문자열을 생성할 수 있습니다.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-queues-as-a-destination"></a>Service Bus 큐를 대상으로 설정

Service Bus 큐를 게시하려면 `endpointType`을 `serviceBusQueue`로 설정하고 다음을 입력합니다.

* connectionString: 공유 액세스 정책을 통해 생성된 특정 대상 Service Bus 큐 연결 문자열입니다.

    >[!NOTE]
    > 연결 문자열은 엔터티와 관련되어 있어야 합니다. 네임스페이스 연결 문자열은 사용할 수 없습니다. Azure Portal에서 게시하려는 특정 Service Bus 큐로 이동하고 **공유 액세스 정책** 을 클릭하여 새 엔터티 관련 연결 문자열을 생성하여 엔터티 관련 연결 문자열을 생성합니다.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-topics-as-a-destination"></a>Service Bus 토픽을 대상으로 설정

Service Bus 토픽을 게시하려면 `endpointType`을 `serviceBusTopic`으로 설정하고 다음을 입력합니다.

* connectionString: 공유 액세스 정책을 통해 생성된 특정 대상 Service Bus 토픽 연결 문자열입니다.

    >[!NOTE]
    > 연결 문자열은 엔터티와 관련되어 있어야 합니다. 네임스페이스 연결 문자열은 사용할 수 없습니다. Azure Portal에서 게시하려는 특정 Service Bus 토픽으로 이동하고 **공유 액세스 정책** 을 클릭하여 새 엔터티 관련 연결 문자열을 생성하여 엔터티 관련 연결 문자열을 생성합니다.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-storage-queues-as-a-destination"></a>스토리지 큐를 대상으로 설정

스토리지 큐를 게시하려면 `endpointType`을 `storageQueue`로 설정하고 다음을 입력합니다.

* queueName: 게시하는 스토리지 큐의 이름입니다.
* connectionString: 스토리지 큐가 있는 스토리지 계정의 연결 문자열입니다.

  >[!NOTE]
  > Event Hubs, Service Bus 큐 및 Service Bus 항목과 달리 Storage 큐에 사용되는 연결 문자열은 엔터티별로 다릅니다. 대신 Storage 계정에 대한 연결 문자열이어야 합니다.

  ```json
  {
    "properties": {
      "destination": {
        "endpointType": "storageQueue",
        "properties": {
          "queueName": "<your-storage-queue-name>",
          "connectionString": "<your-storage-account-connection-string>"
        }
      }
    }
  }
  ```
