---
title: Azure Communication Services 통화 녹화 개요
titleSuffix: An Azure Communication Services concept document
description: 통화 녹화 기능 및 API를 간략히 설명합니다.
author: GrantMeStrength
manager: anvalent
services: azure-communication-services
ms.author: jken
ms.date: 06/30/2021
ms.topic: conceptual
ms.custom: references_regions
ms.service: azure-communication-services
ms.openlocfilehash: fef4972271046f7435140fd2d9ba3d18c7c3b11c
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123254759"
---
# <a name="calling-recording-overview"></a>통화 녹화 개요

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

> [!NOTE]
> 통화 녹음/녹화는 미국, 영국, 유럽, 아시아 및 호주 지역에서 만들어진 통신 서비스 리소스에 사용할 수 있습니다.

통화 녹화는 녹화를 시작, 중지, 일시 중지, 다시 시작하는 API 세트를 제공합니다. 이러한 API는 서버 쪽 비즈니스 논리에서 또는 사용자 동작에 의해 트리거되는 이벤트를 통해 액세스할 수 있습니다. 녹화된 미디어 출력은 MP4 오디오+비디오 형식으로, Teams에서 미디어를 녹화하는 데 사용하는 형식과 동일합니다. 미디어 및 메타데이터와 관련된 알림은 Event Grid를 통해 내보내집니다. 녹화는 선택한 장기 스토리지 솔루션을 검색하고 이동하기 위해 기본 제공 임시 스토리지에 48시간 동안 저장됩니다. 

![통화 녹음/녹화 개념 다이어그램](../media/call-recording-concept.png)

## <a name="media-output-types"></a>미디어 출력 형식
통화 녹화는 현재 혼합 오디오+비디오 MP4 출력 형식을 지원합니다. 출력 미디어는 Microsoft Teams 녹화를 통해 생성된 회의 녹화와 일치합니다.

| 채널 형식 | 콘텐츠 형식 | 동영상 | 오디오 |
| :----------- | :------------- | :---- | :--------------------------- |
| audioVideo | mp4 | 기본 타일 정렬의 모든 참가자에 대한 1920x1080 8 FPS 비디오 | 모든 참가자에 대한 16kHz mp4a 혼합 오디오 |


## <a name="run-time-control-apis"></a>런타임 컨트롤 API
런타임 컨트롤 API를 사용하여, 그룹 통화를 만들어 대화를 녹화하는 애플리케이션과 같은 내부 비즈니스 논리 트리거를 통해 또는 서버 애플리케이션에 녹화를 시작하도록 지시하는 사용자가 트리거하는 작업에서 녹화를 관리할 수 있습니다. 통화 녹음/녹화 API는 `serverCallId`를 사용하여 녹음/녹화를 시작하는 [Out-of-Call API](./call-automation-apis.md#out-of-call-apis)입니다. 통화할 때 통화가 설정된 후에는 `Microsoft.Communication.CallLegStateChanged` 이벤트를 통해 `serverCallId`가 반환됩니다. `serverCallId`는 `data.serverCallId` 필드에서 찾을 수 있습니다. Calling Client SDK에서 `serverCallId`를 검색하는 방법에 대한 자세한 내용은 [통화 녹음/녹화 빠른 시작 샘플](../../quickstarts/voice-video-calling/call-recording-sample.md)을 참조하세요. 녹음/녹화가 시작할 때 `recordingOperationId`가 반환됩니다. 이 작업은 일시 중지 및 재개와 같은 후속 작업에 사용됩니다.   

| 작업                            | 작동 방식            | 의견                       |
| :-------------------- | :--------------------- | :----------------------------- |
| 기록 시작       | `serverCallId`         | `recordingOperationId`를 반환합니다. | 
| 녹음/녹화 상태 가져오기   | `recordingOperationId` | `recordingState`를 반환합니다.       | 
| 녹음/녹화 일시 중지       | `recordingOperationId` | 통화 녹화를 일시 중지하고 다시 시작하면 통화 또는 회의의 일부 녹화를 건너뛰고 단일 파일에 녹화를 다시 시작할 수 있습니다. | 
| 녹음/녹화 다시 시작      | `recordingOperationId` | 일시 중지된 녹음/녹화 작업을 다시 시작합니다. 콘텐츠는 일시 중지되기 전의 콘텐츠와 동일한 파일에 포함됩니다. | 
| 녹음/녹화 중지        | `recordingOperationId` | 녹음/녹화를 중지하고 파일 다운로드를 위한 최종 미디어 처리를 시작합니다. | 


## <a name="event-grid-notifications"></a>Event Grid 알림

> Azure Communication Services는 녹화를 위한 단기 미디어 스토리지를 제공합니다. **48시간 이내에 보존하려는 녹화된 콘텐츠를 내보냅니다.** 48시간 후에는 더 이상 녹화를 사용할 수 없습니다.

Event Grid 알림 `Microsoft.Communication.RecordingFileStatusUpdated`는 녹음/녹화를 검색할 준비가 되면 일반적으로 녹화 프로세스가 완료된 후(예: 모임이 종료되고 녹음/녹화가 중지됨) 몇 분 후에 게시됩니다. 녹음/녹화 이벤트 알림에는 녹음/녹화된 미디어와 녹음/녹화 메타데이터 파일을 모두 검색하는 데 사용되는 `contentLocation` 및 `metadataLocation`이 포함됩니다.

### <a name="notification-schema-reference"></a>알림 스키마 참조
```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from storage
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                    "metadataLocation": <string>, // url of the metadata for this chunk
                    "contentLocation": <string>   // url of the mp4, mp3, or wav for this chunk
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft", etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}
```
## <a name="regulatory-and-privacy-concerns"></a>규정 및 프라이버시 문제

대부분의 국가 및 주에는 사용자가 통신 녹화에 동의해야 하는 PSTN, 음성, 비디오 통화 녹화에 적용되는 법률 및 규정이 있습니다. 법률을 준수하면서 통화 녹화 기능을 사용하는 것은 사용자의 책임입니다. 각 참가자에게 적용되는 법률을 준수하면서 녹화되는 통신의 당사자로부터 동의를 받아야 합니다.

개인 데이터의 유지 관리에 대한 규정에 따라 사용자 데이터를 내보낼 수 있어야 합니다. 이러한 요구 사항을 지원하기 위해 녹화 메타데이터 파일에는 `participants` 배열의 각 통화 참가자에 대한 participantId가 포함됩니다. 내부 사용자 ID가 있는 `participants` 배열의 MRI를 상호 참조하여 통화에서 참가자를 식별할 수 있습니다. 녹화 메타데이터 파일의 예는 아래에 참조용으로 제공됩니다.

## <a name="next-steps"></a>다음 단계
자세한 내용은 [통화 녹음/녹화 빠른 시작 샘플](../../quickstarts/voice-video-calling/call-recording-sample.md)을 확인하세요.

[Call Automation API](./call-automation-apis.md)에 대해 자세히 알아보세요.
