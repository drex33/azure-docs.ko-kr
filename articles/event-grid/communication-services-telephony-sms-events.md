---
title: Azure Communication Services - 전화 통신 및 SMS 이벤트
description: 이 문서에서는 Azure Communication Services 전화 통신 및 SMS 이벤트에 대한 Event Grid 이벤트 원본으로 사용하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 10/15/2021
ms.author: vikramdh
ms.openlocfilehash: 2456df394a25df3b64f464a144224d9f9b365408
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134252"
---
# <a name="azure-communication-services---telephony-and-sms-events"></a>Azure Communication Services - 전화 통신 및 SMS 이벤트

이 문서에서는 통신 서비스 전화 통신 및 SMS 이벤트에 대한 속성과 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요.

## <a name="events-types"></a>이벤트 유형

Azure Communication Services 다음과 같은 전화 통신 및 SMS 이벤트 유형을 내보낸다.

| 이벤트 유형                                                  | 설명                                                                                    |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Microsoft.Communication.SMSReceived                         | Communication Service와 연결된 전화 번호로 SMS를 받을 때 게시됩니다. |
| Microsoft.Communication.SMSDeliveryReportReceived           | Communication Service에서 보낸 SMS에 대해 배달 보고서를 받을 때 게시됩니다.     |

## <a name="event-responses"></a>이벤트 응답

이벤트가 트리거될 때 Event Grid 서비스는 해당 이벤트에 대한 데이터를 구독 엔드포인트로 보냅니다.

이 섹션에는 각 이벤트에 대한 데이터가 어떻게 표시되는지 예가 포함되어 있습니다.

### <a name="microsoftcommunicationsmsdeliveryreportreceived-event"></a>Microsoft.Communication.SMSDeliveryReportReceived event

```json
[{
  "id": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
    "From": "15555555555",
    "To": "+15555555555",
    "DeliveryStatus": "Delivered",
    "DeliveryStatusDetails": "No error.",
    "ReceivedTimestamp": "2020-09-18T00:22:20.2855749Z",
    "DeliveryAttempts": [
      {
        "Timestamp": "2020-09-18T00:22:14.9315918Z",
        "SegmentsSucceeded": 1,
        "SegmentsFailed": 0
      }
    ]
  },
  "eventType": "Microsoft.Communication.SMSDeliveryReportReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:22:20Z"
}]
```
### <a name="microsoftcommunicationsmsreceived-event"></a>Microsoft.Communication.SMSReceived event

```json
[{
  "id": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
  "topic": "/subscriptions/50ad1522-5c2c-4d9a-a6c8-67c11ecb75b8/resourcegroups/acse2e/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
    "From": "15555555555",
    "To": "15555555555",
    "Message": "Great to connect with ACS events",
    "ReceivedTimestamp": "2020-09-18T00:27:45.32Z"
  },
  "eventType": "Microsoft.Communication.SMSReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:27:47Z"
}]
```
