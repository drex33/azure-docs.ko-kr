---
title: 비디오 연속 녹화 및 재생 자습서 - Azure Video Analyzer
description: 이 자습서에서는 Azure Video Analyzer를 사용하여 비디오를 클라우드에 연속 녹화하고 재생하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6a86dac6b242139d406dd6513a70846c57fa1c05
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131559355"
---
# <a name="tutorial-continuous-video-recording-and-playback"></a>자습서: 연속 비디오 녹화 및 재생

[!INCLUDE [header](includes/edge-env.md)]

이 자습서에서는 Azure Video Analyzer를 사용하여 비디오를 클라우드에 [연속 녹화(CVR)](../continuous-video-recording.md)하고 재생하는 방법을 알아봅니다. 이 기능은 며칠, 몇 주, 몇 달 또는 몇 년 동안 카메라의 영상 보관을 유지해야 하거나 녹화 중인 동영상의 보존 기간을 지정할 수 있는 안전 및 규정 준수와 같은 시나리오에 유용합니다. 보존 정책은 동영상을 저장할 기간(예: 지난 7일)을 정의합니다. 자세한 내용은 [보존 정책 관리](../manage-retention-policy.md) 문서를 참조하세요.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 관련 리소스 설치
> * CVR을 수행하는 코드 검사
> * 샘플 코드 실행
> * 결과 검사 및 비디오 보기

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>추천 참고 자료  

시작하기 전에 다음 문서를 참조하세요.

* [Video Analyzer 개요](../overview.md)
* [Video Analyzer 용어](../terminology.md)
* [Video Analyzer 파이프라인 개념](../pipeline.md) 
* [연속 비디오 녹화 시나리오](../continuous-video-recording.md)

## <a name="prerequisites"></a>필수 구성 요소

이 자습서의 필수 구성 요소는 [!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]입니다.

## <a name="set-up-azure-resources"></a>Azure 리소스 설정

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)
[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="concepts"></a>개념

[이](../pipeline.md) 문서에 설명된 것처럼 비디오 분석기 파이프라인을 사용하여 다음을 정의할 수 있습니다.

- 미디어를 캡처해야 하는 위치,
- 처리 방법,
- 결과를 전달할 위치를 정의할 수 있습니다. 
 
 CVR을 수행하려면 RTSP 가능 카메라에서 비디오를 캡처하고, [비디오 리소스](../terminology.md#video)에 연속 녹화해야 합니다. 다음 다이어그램은 이 파이프라인을 그림으로 표현한 것입니다. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="../media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="CVR에 대한 Video Analyzer 파이프라인":::
<!-- ./media/continuous-video-recording-tutorial/continuous-video-recording-overview.svg -->

이 자습서에서는 [Live555 Media Server](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)를 사용하여 빌드된 에지 모듈을 사용하여 RTSP 카메라를 시뮬레이션합니다. 파이프라인 내에서 [RTSP 원본](../pipeline.md#rtsp-source) 노드를 사용하여 라이브 피드를 가져온 다음, 해당 비디오를 Video Analyzer 계정에 기록할 [비디오 싱크 노드](../pipeline.md#video-sink)로 보냅니다. 이 자습서에서 사용되는 비디오는 [고속도로 교차로 샘플 비디오](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv)입니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

이 자습서에서는 다음을 수행합니다.

1. 개발 환경을 설정합니다.
1. 필요한 에지 모듈을 배포합니다.
1. 라이브 파이프라인을 만들고 배포합니다.
1. 결과를 해석합니다.
1. 리소스를 정리합니다.

## <a name="set-up-your-development-environment"></a>개발 환경 설정


### <a name="get-the-sample-code"></a>샘플 코드 가져오기

1. [AVA C# 샘플 리포지토리](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp)를 복제합니다.
1. Visual Studio Code를 시작하고 리포지토리가 다운로드된 폴더를 엽니다.
1. Visual Studio Code에서 src/cloud-to-device-console-app 폴더로 이동하고, **appsettings.json** 이라는 파일을 만듭니다. 이 파일에는 프로그램을 실행하는 데 필요한 설정이 포함되어 있습니다.
1. 위의 설정 단계에서 만든 스토리지 계정의 파일 공유를 찾아 "deployment-output" 파일 공유 아래에서 **appsettings.json** 파일을 찾습니다. 파일을 클릭한 다음, "다운로드" 단추를 누릅니다. 콘텐츠는 다음과 같은 새 브라우저 탭에서 열립니다.

   ```
   {
       "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",
       "deviceId" : "avasample-iot-edge-device",
       "moduleId" : "avaedge"
   }
   ```

   IoT Hub 연결 문자열을 사용하면 Visual Studio Code를 사용하여 Azure IoT Hub를 통해 명령을 에지 모듈에 보낼 수 있습니다. 위의 JSON을 **src/cloud-to-device-console-app/appsettings.json** 파일에 복사합니다.

### <a name="connect-to-the-iot-hub"></a>IoT Hub에 연결

1. Visual Studio Code에서 왼쪽 아래 모서리의 **AZURE IOT HUB** 창 옆에 있는 **추가 작업** 아이콘을 선택하여 IoT Hub 연결 문자열을 설정합니다. src/cloud-to-device-console-app/appsettings.json 파일의 문자열을 복사합니다.

    <!-- commenting out the image for now ![Set IoT Hub connection string]()./media/quickstarts/set-iotconnection-string.png-->
    [!INCLUDE [provide-builtin-endpoint](./includes/common-includes/provide-builtin-endpoint.md)]
1. 약 30초 후에 왼쪽 아래 섹션에서 Azure IoT Hub가 새로 고침됩니다. 에지 디바이스 `avasample-iot-edge-device`에 다음 모듈이 배포되었을 것입니다.
    - Edge Hub(모듈 이름 **edgeHub**)
    - Edge 에이전트(모듈 이름 **edgeAgent**)
    - Video Analyzer(모듈 이름 **avaedge**)
    - RTSP 시뮬레이터(모듈 이름 **rtspsim**)

### <a name="prepare-to-monitor-the-modules"></a>모듈 모니터링 준비

이 빠른 시작 또는 자습서 실행을 사용하면 이벤트가 IoT Hub로 전송됩니다. 이러한 이벤트를 보려면 다음 단계를 수행합니다.

1. Visual Studio Code에서 탐색기 창을 열고, 왼쪽 아래 모서리에서 **Azure IoT Hub** 를 찾습니다.
1. **디바이스** 노드를 펼칩니다.
1. `avasample-iot-edge-device`를 마우스 오른쪽 단추로 클릭하고, **기본 제공 이벤트 엔드포인트 모니터링 시작** 을 선택합니다.

    [!INCLUDE [provide-builtin-endpoint](./includes/common-includes/provide-builtin-endpoint.md)]

## <a name="examine-the-sample-files"></a>샘플 파일 검사

Visual Studio Code에서 src/cloud-to-device-console-app 폴더로 이동합니다. 이 폴더에는 앞에서 만든 appsettings.json 파일과 몇 가지 다른 파일이 있습니다.

* **c2d-console-app.csproj**: Visual Studio Code의 프로젝트 파일입니다.
* **operations.json**: 이 파일에는 우리가 실행할 여러 작업이 나열됩니다.
* **Program.cs**: 다음과 같은 작업을 수행하는 샘플 프로그램 코드입니다.
    * 앱 설정을 로드합니다.
    * Video Analyzer 에지 모듈이 공개하는 직접 메서드를 호출합니다. 이 모듈을 통해 [직접 메서드](direct-methods.md)를 호출하여 라이브 비디오 스트림을 분석할 수 있습니다.
    * **터미널** 창에서 프로그램의 출력을 검토하고, **출력** 창에서 모듈이 생성한 이벤트를 살펴볼 수 있도록 일시 중지합니다.
    * 리소스를 정리하는 직접 메서드를 호출합니다.

## <a name="run-the-program"></a>프로그램 실행 

1. Visual Studio Code에서 src/cloud-to-device-console-app/operations.json으로 이동합니다.
1. **pipelineTopologySet** 노드 아래에서 다음을 편집합니다.

    `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-video-sink/topology.json" `
1. 다음으로, **livePipelineSet** 및 **pipelineTopologyDelete** 노드에서 **topologyName** 값이 이전 파이프라인 토폴로지의 **name** 속성 값과 일치하는지 확인합니다.

    `"topologyName" : "CVRToVideoSink"`  
1. 브라우저에서 [파이프라인 토폴로지](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-video-sink/topology.json)를 열고 videoName을 살펴봅니다. `sample-cvr-video`로 하드 코딩되었습니다. 자습서에서 이를 허용할 수 있습니다. 프로덕션 환경에서는 고유한 각 RTSP 카메라를 고유한 이름을 가진 비디오 리소스에 녹화하도록 주의해야 합니다.
1. F5 키를 선택하여 디버깅 세션을 시작합니다. **터미널** 창에 일부 메시지가 출력됩니다.
1. operations.json 파일은 `pipelineTopologyList` 및 `livePipelineList`에 대한 호출로 시작됩니다. 이전 빠른 시작이나 자습서를 완료한 후에 리소스를 삭제했으면 이 작업에서 다음과 같은 빈 목록을 반환합니다.

    ```
    --------------------------------------------------------------------------
    Executing operation pipelineTopologyList
    -----------------------  Request: pipelineTopologyList  --------------------------------------------------
    {
      "@apiVersion": "1.1"
    }
    ---------------  Response: pipelineTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------

    ```

1. 직접 메서드 호출의 다음 설정을 지정합니다.
   * 이전 `topologyUrl`을 사용하여 `pipelineTopologySet` 호출
   * 다음 본문을 사용하여 `livePipelineSet` 호출
     
     ```
     {
       "@apiVersion": "1.1",
       "name": "Sample-Pipeline-1",
       "properties": {
         "topologyName": "CVRToVideoSink",
         "description": "Sample pipeline description",
         "parameters": [
           {
             "name": "rtspUrl",
             "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
           },
           {
             "name": "rtspUserName",
             "value": "testuser"
           },
           {
             "name": "rtspPassword",
             "value": "testpassword"
           }
         ]
       }
     }
     ```
   * `livePipelineActivate`를 호출하여 라이브 파이프라인을 시작하고 비디오 흐름을 시작한 다음, **터미널** 창에서 **Enter** 키를 선택하기 위해 일시 중지합니다.
1. 이제 **터미널** 창의 출력이 **계속하려면 Enter 키를 누르세요** 라는 메시지에서 일시 중지됩니다. 지금은 **Enter** 키를 선택하지 마세요. 위로 스크롤하여 호출한 직접 메서드에 대한 JSON 응답 페이로드를 확인합니다.
1. 이제 Visual Studio Code에서 **출력** 창으로 전환하면 Video Analyzer 에지 모듈에서 IoT Hub로 보내는 메시지가 표시됩니다. 이러한 메시지에 대한 내용은 다음 섹션에서 다룹니다.
1. 라이브 파이프라인은 계속 실행되고 비디오를 녹화합니다. RTSP 시뮬레이터가 원본 비디오를 계속 반복합니다. 녹화를 중지하려면 **터미널** 창으로 돌아가서 **Enter** 를 선택합니다. 다음과 같은 일련의 호출을 수행하여 리소스를 정리합니다.

   * `livePipelineDeactivate`를 호출하면 라이브 파이프라인이 비활성화됩니다.
   * `livePipelineDelete`를 호출하면 라이브 파이프라인이 삭제됩니다.
   * 라이브 파이프라인이 실행 중 상태임을 나타내는 `livePipelineList`에 대한 두 번째 호출.
   * `pipelineTopologyDelete`를 호출하면 토폴로지가 삭제됩니다.
   * 마지막으로 `pipelineTopologyList`를 호출하면 목록이 비어 있음을 보여줍니다.

## <a name="interpret-the-results"></a>결과 해석

라이브 파이프라인을 실행하면 Video Analytics 에지 모듈이 특정 진단 및 작업 이벤트를 IoT Edge 허브에 보냅니다. 이러한 이벤트는 Visual Studio Code의 **출력** 창에 표시되는 메시지입니다. 메시지에는 `body` 섹션 및 `applicationProperties` 섹션이 포함되어 있습니다. 이러한 섹션이 나타내는 내용을 이해하려면 [IoT Hub 메시지 작성 및 읽기](../../../iot-hub/iot-hub-devguide-messages-construct.md)를 참조하세요.

다음 메시지에서 애플리케이션 속성 및 본문의 콘텐츠는 Video Analytics 에지 모듈에서 정의됩니다.


## <a name="diagnostics-events"></a>진단 이벤트 

### <a name="mediasession-established-event"></a>MediaSession Established 이벤트

파이프라인이 활성화되면 RTSP 원본 노드는 rtspsim 모듈에서 실행되는 RTSP 서버에 연결을 시도합니다. 성공하면 이 이벤트가 출력됩니다.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzers.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-04-09T09:42:18.1280000Z"
  }
}
```

* 이 메시지는 진단 이벤트(**Microsoft.VideoAnalyzers.Diagnostics.MediaSessionEstablished**)입니다. RTSP 원본 노드(주체)에서 RTSP 시뮬레이터와의 연결을 설정하고 라이브 피드(시뮬레이션된)를 받기 시작했음을 나타냅니다.
* applicationProperties의 subject 섹션은 메시지가 생성된 파이프라인 토폴로지의 노드를 참조합니다. 여기서는 메시지가 RTSP 원본 노드에서 시작됩니다.
* applicationProperties의 eventType 섹션은 진단 이벤트임을 나타냅니다.
* eventTime 섹션은 이벤트가 발생한 시간을 나타냅니다.
* body 섹션에는 진단 이벤트에 대한 데이터가 포함됩니다. 여기서는 [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 세부 정보입니다.

## <a name="operational-events"></a>작업 이벤트 

### <a name="recordingstarted-event"></a>RecordingStarted 이벤트

비디오 싱크 노드는 미디어 녹화가 시작되면 **Microsoft.VideoAnalyzers.Pipeline.OperationalRecordingStarted** 유형의 다음 이벤트를 내보냅니다.

```
[IoTHubMonitor] [9:42:38 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-video"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStarted",
    "eventTime": "2021-04-09T09:42:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

applicationProperties의 subject 섹션은 이 메시지를 생성한 라이브 파이프라인의 비디오 싱크 노드를 참조합니다.

body 섹션에는 출력 위치에 대한 정보가 포함됩니다. 여기서는 비디오가 녹화되는 Video Analyzer 리소스의 이름입니다.

### <a name="recordingavailable-event"></a>RecordingAvailable 이벤트

이름에서 알 수 있듯이, 녹화가 시작되면 **RecordingStarted** 이벤트가 전송되지만, 미디어 데이터는 아직 자산에 업로드되지 않았을 수 있습니다. 비디오 싱크 노드는 미디어가 업로드되면 **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable** 유형의 다음 이벤트를 내보냅니다.

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [ava-sample-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-video"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable",
    "eventTime": "2021-04-09T09:43:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

이 이벤트는 플레이어 또는 클라이언트에서 비디오 재생을 시작하기에 충분한 데이터가 비디오 리소스에 기록되었음을 나타냅니다.

applicationProperties의 subject 섹션은 이 메시지를 생성한 라이브 파이프라인의 비디오 싱크 노드를 참조합니다.

body 섹션에는 출력 위치에 대한 정보가 포함됩니다. 여기서는 비디오가 녹화되는 Video Analyzer 리소스의 이름입니다.

### <a name="recordingstopped-event"></a>RecordingStopped 이벤트

라이브 파이프라인을 비활성화하면 비디오 싱크 노드가 미디어 녹화를 중지합니다. 그리고 **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStopped** 유형의 다음 이벤트를 내보냅니다.

```
[IoTHubMonitor] [11:33:31 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-video"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStopped",
    "eventTime": "2021-04-10T11:33:31.051Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

이 이벤트는 기록이 중지되었음을 나타냅니다.

applicationProperties의 subject 섹션은 이 메시지를 생성한 라이브 파이프라인의 비디오 싱크 노드를 참조합니다.

body 섹션에는 출력 위치에 대한 정보가 포함됩니다. 여기서는 비디오가 녹화되는 Video Analyzer 리소스의 이름입니다.

## <a name="playing-back-the-recording"></a>녹화본 재생

Azure Portal에 로그인하여 비디오를 살펴보면 라이브 파이프라인에서 만든 Video Analyzer 비디오 리소스를 검사할 수 있습니다.

1. 웹 브라우저를 열고 [Azure Portal](https://portal.azure.com/)로 이동합니다. 자격 증명을 입력하여 포털에 로그인합니다. 기본 보기는 서비스 대시보드입니다.
1. 구독에 있는 리소스 중에서 Video Analyzer 계정을 찾아 계정 창을 엽니다.
1. **Video Analyzer** 목록에서 **비디오** 를 선택합니다.
1. 이름이 `sample-cvr-video`인 비디오가 나열됩니다. 이 이름은 파이프라인 토폴로지 파일에서 선택한 이름입니다.
1. 비디오를 선택합니다.
1. 비디오 세부 정보 페이지가 열리고 재생이 자동으로 시작됩니다.

[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

* RTSP 시뮬레이터 대신 RTSP를 지원하는 [IP 카메라](https://en.wikipedia.org/wiki/IP_camera)를 사용합니다. [ONVIF 규격](https://www.onvif.org/conformant-products/) 제품 페이지에서 프로필 G, S 또는 T를 준수하는 디바이스를 찾아 RTSP를 지원하는 IP 카메라를 검색할 수 있습니다.
* Azure Linux VM 대신 AMD64 또는 X64 Linux 디바이스를 사용합니다. 이 디바이스는 IP 카메라와 동일한 네트워크에 있어야 합니다. [Linux에 Azure IoT Edge 런타임 설치](../../../iot-edge/how-to-install-iot-edge.md)의 지침을 따릅니다. 그런 다음, [가상 Linux 디바이스에 첫 번째 IoT Edge 모듈 배포](../../../iot-edge/quickstart-linux.md) 빠른 시작의 지침에 따라 디바이스를 Azure IoT Hub에 등록합니다.
