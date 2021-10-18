---
title: Azure Communication Services-상태 이벤트
description: 이 문서에서는 사용자 상태 이벤트에 대 한 Event Grid 이벤트 원본으로 Azure 통신 서비스를 사용 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 10/15/2021
ms.author: vikramdh
ms.openlocfilehash: 75e23d5de55ab205cc35bf6e7496413f4dd4be9a
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134433"
---
# <a name="azure-communication-services---presence-events"></a>Azure Communication Services-상태 이벤트

이 문서에서는 통신 서비스 상태 이벤트에 대 한 속성 및 스키마를 제공 합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요.

## <a name="events-types"></a>이벤트 유형

Azure 통신 서비스는 다음과 같은 사용자의 현재 상태 이벤트 유형을 내보냅니다.

| 이벤트 유형                                                  | 설명                                                                                    |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Microsoft Communication. UserDisconnected | 통신 서비스 사용자가 통신 서비스와의 연결이 끊어진 상태로 지정 된 후에 게시 됨 |

## <a name="event-responses"></a>이벤트 응답

이벤트가 트리거될 때 Event Grid 서비스는 해당 이벤트에 대한 데이터를 구독 엔드포인트로 보냅니다.

이 섹션에는 각 이벤트에 대한 데이터가 어떻게 표시되는지 예가 포함되어 있습니다.

> [!IMPORTANT]
> Microsoft Communication to Disconnected 이벤트는 공개 미리 보기 상태입니다. 이 미리 보기 시간 동안에는 사용자의 연결 끊김 상태와 연결 된 로그를 전역적으로 복제할 수 있습니다. Event Grid를 통해이 이벤트를 구독 하 여 연결 되지 않은 상태를 가져올 수 있습니다.

> [!NOTE]
> Microsoft. Communication to Disconnected 이벤트는 채팅 컨텍스트에서만 적용 됩니다.
 
### <a name="microsoftcommunicationuserdisconnected"></a>Microsoft Communication. UserDisconnected

```json
[
 {
  "id": "8f60490d-0719-4d9d-a1a6-835362fb752e",
  "topic": "/subscriptions/{subscription-id}/resourcegroups/}{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "user/{rawId}",
  "data": {
    "userCommunicationIdentifier": {
      "rawId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_0000000b-d198-0d50-84f5-084822008d40",
      "communicationUser": {
        "id": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_0000000b-d198-0d50-84f5-084822008d40"
      }
    }
  },
  "eventType": "Microsoft.Communication.UserDisconnected",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-08-10T20:25:38Z"
 }
]
```
