---
title: Azure Event Grid-Azure Event Grid 토픽 및 Event Grid 도메인에 대 한 진단 로그
description: 이 문서에서는 Azure Event Grid 토픽 또는 도메인의 진단 로그에 대 한 개념 정보를 제공 합니다.
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: b7656969a9c607060ffd71ff9d7e66ee51cc5f83
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132868175"
---
# <a name="diagnostic-logs-for-event-grid-topics-and-event-grid-domains"></a>Event Grid 토픽 및 Event Grid 도메인에 대 한 진단 로그

진단 설정을 통해 Event Grid 사용자는 스토리지 계정, 이벤트 허브 또는 Log Analytics 작업 영역 중 하나에서 **게시 및 전달 오류** 로그를 캡처하고 볼 수 있습니다. 이 문서에서는 로그 및 예제 로그 항목에 대한 스키마를 제공합니다.

## <a name="schema-for-publishdelivery-failure-logs"></a>게시/전달 오류 로그의 스키마

| 속성 이름 | 데이터 형식 | Description |
| ------------- | --------- | ----------- |
| Time | DateTime | 로그 항목이 생성된 시간 <p>**예제 값:**  01-29-2020 09:52:02.700</p> |
| EventSubscriptionName | String | 이벤트 구독의 이름 <p>**예제 값:** ‘EVENTSUB1’</p> <p>이 속성은 전달 오류 로그에 대해서만 존재합니다.</p>  |
| Category | String | 로그 카테고리 이름. <p>**예제 값:** ‘DeliveryFailures’ 또는 ‘PublishFailures’ | 
| OperationName | String | 오류의 원인인 작업의 이름입니다.<p>**예제 값:** 전달 오류의 경우 ‘Deliver’. |
| 메시지 | String | 오류의 원인과 기타 추가 정보를 설명하는 사용자에 대한 로그 메시지입니다. |
| ResourceId | String | 토픽/도메인 리소스에 대한 리소스 ID<p>**예제 값:** `/SUBSCRIPTIONS/SAMPLE-SUBSCRIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/TOPIC1` |

## <a name="example---schema-for-publishdelivery-failure-logs"></a>예제-게시/배달 실패 로그에 대 한 스키마

```json
{
    "time": "2019-11-01T00:17:13.4389048Z",
    "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
    "eventSubscriptionName": "SAMPLEDESTINATION",
    "category": "DeliveryFailures",
    "operationName": "Deliver",
    "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
}
```

의 가능한 값은 `Outcome` `NotFound` ,, `Aborted` `TimedOut` , `GenericError` 및 `Busy` 입니다. Event Grid는 `message`의 이벤트 처리기에서 받은 모든 정보를 로그합니다. 예를 들어 `GenericError`의 경우 HTTP 상태 코드, 오류 코드 및 오류 메시지를 로그합니다.

## <a name="schema-for-data-plane-requests"></a>데이터 평면 요청에 대 한 스키마

| 속성 이름 | 데이터 형식 | Description |
| ------------- | --------- | ----------- |
| NetworkAccess | String | 허용된 값은 <ul><li>`PublicAccess` -공용 IP를 통해 연결 하는 경우</li><li>`PrivateAccess` -개인 링크를 통해 연결 하는 경우</li></ul> |
| ClientIpAddress | String | 들어오는 요청의 원본 IP |
| TlsVersion | String | 클라이언트 연결에서 사용 하는 TLS (전송 계층 보안) 버전입니다. 가능한 값: **1.0**, **1.1** 및 **1.2** |
| 인증/유형 | String | 메시지를 게시할 때 인증에 사용 되는 비밀의 유형입니다. <ul><li>`Key` – 요청에서 SAS 키를 사용 합니다.</li><li>`SASToken` – 요청은 SAS 키에서 생성 된 SAS 토큰을 사용 합니다.</li><li>`AADAccessToken`– Azure Active Directory 발급 된 JWT (JSON Web Token) 토큰</li><li>`Unknown` – 위의 인증 유형 없음 옵션 요청은이 인증 유형을 갖습니다. </li></ul> |
| 인증/ObjectId | String | 인증 유형이로 설정 된 경우 사용 되는 서비스 주체의 ObjectId입니다. `AADAccessToken` |
| OperationResult | String | 게시의 결과입니다. 가능한 값은 다음과 같습니다. <ul><li>Success</li><li>권한 없음</li><li>사용할 수 없음</li><li>RequestEntityTooLarge</li><li>BadRequest</li><li>InternalServerError</li></ul> |
| TotalOperations | String | 이러한 추적은 각 게시 요청에 대해 내보내지 않습니다. 위의 값의 고유한 각 조합에 대 한 집계는 1 분 마다 내보내집니다. |

## <a name="example---schema-for-data-plane-requests"></a>예제-데이터 평면 요청에 대 한 스키마

```json
{
    "time": "2021-10-26T21:44:16.8117322Z",
    "resourceId": "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/RESOURCEGROUPS/BMT-TEST/PROVIDERS/MICROSOFT.EVENTGRID/DOMAINS/BMTAUDITLOGDOMAIN",
    "operationName": "Microsoft.EventGrid/events/send",
    "category": "DataPlaneRequests",
    "level": "Information",
    "region": "CENTRALUSEUAP",
    "properties": {
        "aggregatedRequests": [
            {
                "networkAccess": "PublicAccess",
                "clientIpAddress": "xx.xx.xx.xxx",
                "tlsVersion": "1.2",
                "authentication": {
                            "type": "AADAccessToken",
                            "objectId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"
                },
                "operationResult": "Success",
                "totalOperations": 1
            }
        ]
    }
}
```

`DataPlaneRequests`진단 설정을 선택 하면 Event Grid 리소스는 공용 및 개인 액세스 작업을 포함 하 여 데이터 평면 작업에 대 한 감사 추적을 게시 하기 시작 합니다. 이 추적은 필요한 경우 하나 이상의 요청을 기록할 수 있습니다.

## <a name="next-steps"></a>다음 단계

토픽 또는 도메인에 대해 진단 로그를 사용하도록 설정하는 방법을 알아보려면 [진단 로그 사용](enable-diagnostic-logs-topic.md)을 참조하세요.
