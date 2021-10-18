---
title: Azure Communication Services - 음성 및 영상 통화 이벤트
description: 이 문서에서는 음성 및 영상 통화 이벤트에 대한 Event Grid 이벤트 원본으로 Azure Communication Services 사용하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 10/15/2021
ms.author: vikramdh
ms.openlocfilehash: 9259a5ce7151197f1155bfb91f23f3c98afca9b4
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134255"
---
# <a name="azure-communication-services---voice-and-video-calling-events"></a>Azure Communication Services - 음성 및 영상 통화 이벤트

이 문서에서는 통신 서비스 음성 및 영상 통화 이벤트에 대한 속성과 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요.

## <a name="events-types"></a>이벤트 유형

Azure Communication Services 다음과 같은 음성 및 영상 통화 이벤트 유형을 내선합니다.

| 이벤트 유형                                                  | 설명                                                                                    |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Microsoft.Communication.RecordingFileStatusUpdated | 기록 파일을 사용할 수 있을 때 게시됨 |

## <a name="event-responses"></a>이벤트 응답

이벤트가 트리거될 때 Event Grid 서비스는 해당 이벤트에 대한 데이터를 구독 엔드포인트로 보냅니다.

이 섹션에는 각 이벤트에 대한 데이터가 어떻게 표시되는지 예가 포함되어 있습니다.

> [!IMPORTANT]
> 통화 녹음/녹화 기능이 아직 공개 미리 보기 상태입니다.

### <a name="microsoftcommunicationrecordingfilestatusupdated"></a>Microsoft.Communication.RecordingFileStatusUpdated

```json
[
 {
  "id": "7283825e-f8f1-4c61-a9ea-752c56890500",
  "topic": "/subscriptions/{subscription-id}/resourcegroups/}{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/recording/call/{call-id}/recordingId/{recording-id}",
  "data": {
    "recordingStorageInfo": {
      "recordingChunks": [
        {
          "documentId": "0-eus-d12-801b3f3fc462fe8a01e6810cbff729b8",
          "index": 0,
          "endReason": "SessionEnded",
          "contentLocation": "https://storage.asm.skype.com/v1/objects/0-eus-d12-801b3f3fc462fe8a01e6810cbff729b8/content/video",
          "metadataLocation": "https://storage.asm.skype.com/v1/objects/0-eus-d12-801b3f3fc462fe8a01e6810cbff729b8/content/acsmetadata"
        }
      ]
    },
    "recordingStartTime": "2021-07-27T15:20:23.6089755Z",
    "recordingDurationMs": 6620,
    "sessionEndReason": "CallEnded"
  },
  "eventType": "Microsoft.Communication.RecordingFileStatusUpdated",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-07-27T15:20:34.2199328Z"
 }
]
```
