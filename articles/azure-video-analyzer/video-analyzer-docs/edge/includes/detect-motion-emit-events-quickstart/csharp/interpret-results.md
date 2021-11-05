---
author: fvneerden
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: faneerde
ms.custom: ignite-fall-2021
ms.openlocfilehash: 17f32b1500f5218a1a6668a875c63bfd64de83b9
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861182"
---
라이브 파이프라인을 실행하면 동작 감지기 프로세서 노드의 결과가 IoT Hub 메시지 싱크 노드를 통해 IoT 허브에 전송됩니다. Visual Studio Code의 **OUTPUT** 창에 표시되는 메시지에는 **body** 섹션과 **applicationProperties** 섹션이 포함되어 있습니다. 자세한 내용은 [IoT Hub 메시지 만들기 및 읽기](../../../../../../iot-hub/iot-hub-devguide-messages-construct.md)를 참조하세요.

다음 메시지에서 Video Analyzer 에지 모듈은 애플리케이션 속성 및 본문의 콘텐츠를 정의합니다.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 이벤트

라이브 파이프라인이 활성화되면 RTSP 원본 노드는 rtspsim-live555 컨테이너에서 실행되는 RTSP 서버에 연결을 시도합니다. 연결에 성공하면 다음 이벤트가 출력됩니다.

```
[IoTHubMonitor] [10:51:34 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
  {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620204694595500 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "properties": {
    "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ava-sample-deployment/providers/Microsoft.Media/videoAnalyzers/avasample",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-05-06T00:58:58.602Z",
    "dataVersion": "1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-message-source": "Telemetry",
    "messageId": "459c3255-7c86-4ff5-a1e5-7ce3fcb07627",
    "contentType": "application/json",
    "contentEncoding": "utf-8"
  }
}
```

앞의 출력에서,

- 이 메시지는 진단 이벤트 **MediaSessionEstablished** 입니다. RTSP 원본 노드(주체)에서 RTSP 시뮬레이터에 연결되었고 라이브 피드(시뮬레이션된)를 받기 시작했음을 나타냅니다.
- sdp 섹션에는 진단 이벤트에 대한 데이터가 포함됩니다. 이 경우 데이터는 [SDP(세션 설명 프로토콜)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 세부 정보로 구성됩니다.

### <a name="motiondetection-event"></a>MotionDetection 이벤트

동작이 감지되면 Video Analyzer 모듈이 유추 이벤트를 보냅니다. \* **\*유형** 은 동작 감지 프로세서의 결과임을 나타내기 위해 **motion** 으로 설정됩니다.

이 메시지의 예제는 다음과 같습니다.

```json
{
  "body": {
    "timestamp": 145818422564951,
    "inferences": [
      {
        "type": "motion",
        "motion": {
          "box": {
            "l": 0.322176,
            "t": 0.574627,
            "w": 0.525,
            "h": 0.088889
          }
        }
      }
    ]
  },
  "properties": { … },
  "systemProperties": { … }
}
```

이 예제에서:

- **본문** 값은 분석 이벤트에 대한 데이터입니다. 이 경우 이벤트는 유추 이벤트이므로 본문에 **타임스팸프** 및 **유추** 데이터가 포함됩니다.
- **유추** 데이터는 **형식** 이 **동작** 임을 나타냅니다. 해당 **동작** 이벤트에 대한 추가 데이터가 있습니다.
- **box** 섹션은 움직이는 개체 주위의 경계 상자에 대한 좌표를 포함합니다. 값은 비디오의 가로 및 세로 픽셀로 정규화됩니다. 예를 들어 원래 픽셀 좌표를 가져오려면 가로 크기를 1920으로 곱하고 세로 크기를 1080으로 곱합니다.

  ```
  l - distance from left of image
  t - distance from top of image
  w - width of bounding box
  h - height of bounding box
  ```
