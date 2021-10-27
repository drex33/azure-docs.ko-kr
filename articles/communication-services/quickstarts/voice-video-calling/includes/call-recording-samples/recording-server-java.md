---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: ravithanneeru
manager: joseys
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: joseys
ms.openlocfilehash: b71ae256329d9fe8d28f0b5154f0b732de61009f
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130050660"
---
## <a name="sample-code"></a>샘플 코드
[GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/ServerRecording)에서 이 빠른 시작에 대한 최종 코드를 찾습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정을 만듭니다. 자세한 내용은 [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)를 참조하세요.
- [Node.js(12.18.4 이상)](https://nodejs.org/en/download/)
- [JDK(Java Development Kit)](/azure/developer/java/fundamentals/java-jdk-install), 버전 11 이상.
- [Apache Maven](https://maven.apache.org/download.cgi).
- [Spring boot framework v- 2.5.0](https://spring.io/projects/spring-boot)
- Azure Communication Services 리소스를 만듭니다. 자세한 내용은 [Azure Communication Services 리소스 만들기](../../../create-communication-resource.md)를 참조하세요. 이 빠른 시작에 대한 **연결 문자열** 리소스를 기록해야 합니다.
- Azure Storage 계정 및 컨테이너에 대한 자세한 내용은 [스토리지 계정 만들기](../../../../../storage/common/storage-account-create.md?tabs=azure-portal)를 참조하세요. 이 빠른 시작을 위해 **연결 문자열** 및 **컨테이너 이름** 을 기록해야 합니다.
- [Azure Event Grid](../../../../../event-grid/overview.md) 웹후크.

## <a name="object-model"></a>개체 모델

다음 클래스는 Java 녹음/녹화 서버 앱의 주요 기능 중 일부를 처리합니다.

| 속성                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallingServerClientBuilder | 이 클래스는 CallingServerClient의 인스턴스를 만드는 데 사용됩니다.|
| CallingServerClient | 이 클래스는 통화 기능에 필요합니다. CallingServerClientBuilder를 통해 인스턴스를 얻고 이를 사용하여 통화 시작/끊기, 오디오 재생/취소 및 참가자 추가/제거를 수행할 수 있습니다. |

## <a name="getting-servercallid-as-a-requirement-for-call-recording-server-apis"></a>통화 녹음/녹화 서버 API에 대한 요구 사항으로 serverCallId 가져오기

> [!NOTE]
> 이 API는 개발자를 위한 미리 보기로 제공되며 수신한 피드백을 기반으로 변경될 수 있습니다. 프로덕션 환경에서 이 API를 사용하지 마세요. 이 API를 사용하려면 ACS 통화 웹 SDK의 ‘베타’ 릴리스를 사용하세요. 녹음/녹화 흐름이 있는 클라이언트 샘플은 [GitHub](https://github.com/Azure-Samples/communication-services-web-calling-hero/tree/public-preview)에서 사용할 수 있습니다.


통화 녹음/녹화는 핵심 통화 API의 확장 기능입니다. 먼저 레코딩 기능 API 개체를 가져와야 합니다.

```JavaScript
const callRecordingApi = call.api(Features.Recording);
```

녹음/녹화 변경 내용 구독:

```JavaScript
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);
```

녹음/녹화 세션을 시작/중지/일시 중지/계속하는 데 사용할 수 있는 서버 통화 ID 가져오기:

통화가 연결되면 `getServerCallId` 메서드를 사용하여 서버 통화 ID를 가져옵니다.

```JavaScript
callAgent.on('callsUpdated', (e: { added: Call[]; removed: Call[] }): void => {
    e.added.forEach((addedCall) => {
        addedCall.on('stateChanged', (): void => {
            if (addedCall.state === 'Connected') {
                addedCall.info.getServerCallId().then(result => {
                    dispatch(setServerCallId(result));
                }).catch(err => {
                    console.log(err);
                });
            }
        });
    });
});
```

## <a name="create-a-calling-server-client"></a>통화 서버 클라이언트 만들기

통화 서버 클라이언트를 만들려면 Communication Services 연결 문자열을 사용하여 통화 서버 클라이언트 개체에 전달합니다.

```java
NettyAsyncHttpClientBuilder httpClientBuilder = new NettyAsyncHttpClientBuilder();
CallingServerClientBuilder builder = new CallingServerClientBuilder().httpClient(httpClientBuilder.build())
 .connectionString(connectionString);
 callingServerClient = builder.buildClient();
```

## <a name="start-recording-session-using-startrecordingwithresponse-server-api"></a>'startRecordingWithResponse' 서버 API를 사용하여 녹음/녹화 세션 시작

통화 시작 시 수신한 서버 통화 ID를 사용합니다.

```java
URI recordingStateCallbackUri = new URI("<CallbackUri>");

Response<StartCallRecordingResult> response = this.callingServerClient.initializeServerCall("<serverCallId>")
.startRecordingWithResponse(String.valueOf(recordingStateCallbackUri), null, null);
```
`startRecordingWithResponse` API 응답에는 녹음/녹화 세션의 녹음/녹화 ID가 포함됩니다.

## <a name="start-recording-session-with-startrecordingoptions-using-startrecordingwithresponse-server-api"></a>'startRecordingWithResponse' 서버 API를 사용하여 StartRecordingOptions로 녹음/녹화 세션 시작

통화 시작 시 수신한 서버 통화 ID를 사용합니다.

- RecordingContent는 녹음/녹화 콘텐츠 형식을 전달하는 데 사용됩니다. 예: AUDIO/AUDIO_VIDEO.
- RecordingChannel은 녹음 채널 형식을 전달하는 데 사용됩니다. 예: 혼합/혼합되지 않습니다.
- RecordingFormat은 녹음/녹화 형식을 전달하는 데 사용됩니다. 예: MP4/MP3/WAV.

```java
URI recordingStateCallbackUri = new URI("<CallbackUri>");
StartRecordingOptions recordingOptions = new StartRecordingOptions();
recordingOptions.setRecordingContent(RecordingContent.AUDIO_VIDEO);
recordingOptions.setRecordingChannel(RecordingChannel.MIXED);
recordingOptions.setRecordingFormat(RecordingFormat.MP4);
Response<StartCallRecordingResult> response = this.callingServerClient.initializeServerCall("<serverCallId>")
.startRecordingWithResponse(String.valueOf(recordingStateCallbackUri), recordingOptions, null);
```

## <a name="stop-recording-session-using-stoprecordingwithresponse-server-api"></a>'stopRecordingWithResponse' 서버 API를 사용하여 녹음/녹화 세션 중지

`startRecordingWithResponse`의 응답으로 받은 녹음/녹화 ID를 사용합니다.

```java
Response<Void> response = this.callingServerClient.initializeServerCall(serverCallId)
.stopRecordingWithResponse(recordingId, null);
```

## <a name="pause-recording-session-using-pauserecordingwithresponse-server-api"></a>'pauseRecordingWithResponse' 서버 API를 사용하여 녹음/녹화 세션 일시 중지

`startRecordingWithResponse`의 응답으로 받은 녹음/녹화 ID를 사용합니다.

```java
Response<Void> response = this.callingServerClient.initializeServerCall(serverCallId)
.pauseRecordingWithResponse(recordingId, null);
```

## <a name="resume-recording-session-using-resumerecordingwithresponse-server-api"></a>'resumeRecordingWithResponse' 서버 API를 사용하여 녹음/녹화 세션 계속

`startRecordingWithResponse`의 응답으로 받은 녹음/녹화 ID를 사용합니다.

```java
Response<Void> response = this.callingServerClient.initializeServerCall(serverCallId)
.resumeRecordingWithResponse(serverCallId, null);
```

## <a name="download-recording-file-using-downloadtowithresponse-server-api"></a>'downloadToWithResponse' 서버 API를 사용하여 녹음/녹화 파일 다운로드

[Azure Event Grid](../../../../../event-grid/overview.md) 웹후크를 사용하거나 녹음/녹화된 미디어를 다운로드할 준비가 되면 서비스에 알리기 위해 기타 트리거된 작업을 사용해야 합니다.

다음은 이벤트 스키마의 예입니다.

```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from AMS storage
                    "contentLocation": string, //ACS URL where the content is located
                    "metadataLocation": string, // ACS URL where the metadata for this chunk is located
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft”, etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}
```
녹음/녹화된 미디어를 다운로드하려면 `CallingServerClient` 클래스의 `downloadToWithResponse` 메서드를 사용합니다. 다음은 `downloadToWithResponse` 메서드에 지원되는 매개 변수입니다.

- `contentLocation`: 콘텐츠가 있는 ACS URL입니다.
- `destinationPath`: 파일 위치입니다.
- `parallelDownloadOptionss`: 병렬 다운로드가 작동하는 방식을 수정하기 위한 선택적 ParallelDownloadOptions 개체입니다.
- `overwrite`: True인 경우 파일이 있으면 덮어씁니다.
- `context`: 요청 컨텍스트를 나타내는 컨텍스트입니다.

```Java
Boolean overwrite = true;
ParallelDownloadOptions parallelDownloadOptions = null;
Context context = null;

String filePath = String.format(".\\%s.%s", documentId, fileType);
Path destinationPath = Paths.get(filePath);

Response<Void> downloadResponse = callingServerClient.downloadToWithResponse(contentLocation, destinationPath, parallelDownloadOptions, overwrite, context);
```
녹음/녹화 파일의 콘텐츠 위치 및 문서 ID는 각 `recordingChunk`에 대해 `contentLocation` 및 `documentId` 필드에서 각각 가져올 수 있습니다.
