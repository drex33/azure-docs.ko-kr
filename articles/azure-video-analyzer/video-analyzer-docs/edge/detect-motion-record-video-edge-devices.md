---
title: 에지 디바이스에서 동작 감지 및 비디오 녹화 - Azure
description: Azure Video Analyzer를 사용하여 시뮬레이션된 IP 카메라에서 라이브 비디오 피드를 분석합니다. 동작이 있는지 감지하는 방법, 동작이 있다면 에지 디바이스의 로컬 파일 시스템에 MP4 비디오 클립을 녹화하는 방법을 보여줍니다. 이 빠른 시작에서는 Azure VM을 IoT Edge 디바이스로 사용하고, 시뮬레이션된 라이브 비디오 스트림도 사용합니다.
ms.topic: quickstart
ms.date: 06/01/2021
zone_pivot_groups: video-analyzer-programming-languages
ms.custom: ignite-fall-2021
ms.openlocfilehash: 305bd0f21e9143df5b28998689992a03351b5a8e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030759"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>빠른 시작: 에지 디바이스에서 동작을 감지하고 비디오 녹화

[!INCLUDE [header](includes/edge-env.md)]

이 빠른 시작에서는 Azure Video Analyzer를 사용하여 시뮬레이션된 IP 카메라에서 라이브 비디오 피드를 분석하는 방법을 보여줍니다. 동작이 있는지 감지하는 방법, 동작이 있다면 에지 디바이스의 로컬 파일 시스템에 MP4 비디오 클립을 녹화하는 방법을 보여줍니다. 이 빠른 시작에서는 Azure VM을 IoT Edge 디바이스로 사용하고, 시뮬레이션된 라이브 비디오 스트림도 사용합니다.

## <a name="prerequisites"></a>필수 구성 요소

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>샘플 비디오 검토

이 빠른 시작에 사용할 Azure 리소스를 설정할 때, 짧은 주차장 비디오가 IoT Edge 디바이스로 사용되는 Azure의 Linux VM에 복사됩니다. 이 비디오 파일은 이 자습서의 라이브 스트림을 시뮬레이션하는 데 사용됩니다.

[VLC 미디어 플레이어](https://www.videolan.org/vlc/) 같은 애플리케이션을 열고, Ctrl+N 키를 누르고, [이 링크](https://lvamedia.blob.core.windows.net/public/lots_015.mkv)를 주차장 비디오에 붙여넣어 재생을 시작합니다. 5초 마크 정도에 흰색 차가 주차장을 통과합니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

Video Analyzer를 사용하여 자동차의 동작을 감지하고 5초 마크 정도에 시작되는 비디오 클립을 녹화하려면 다음 단계를 완료합니다.

## <a name="examine-and-edit-the-sample-files"></a>샘플 파일 검사 및 편집

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/csharp/sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/python/sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>배포 매니페스트 생성 및 배포

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/csharp/generate-deploy-deployment-manifest.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/python/generate-deploy-deployment-manifest.md)]
::: zone-end

## <a name="run-the-sample-program"></a>샘플 프로그램 실행

1. Visual Studio Code에서 확장 탭을 열고(또는 Ctrl+Shift+X를 누르고) Azure IoT Hub를 검색합니다.
1. 마우스 오른쪽 단추를 클릭하고 확장 설정을 선택합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/extension-settings.png" alt-text= "Extension settings":::

1. "자세한 정보 메시지 표시"를 검색하고 활성화합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/verbose-message.png" alt-text= "Show Verbose Message":::

1.  ::: zone pivot="programming-language-csharp"
    [!INCLUDE [header](includes/common-includes/csharp-run-program.md)]
    ::: zone-end

    ::: zone pivot="programming-language-python"
    [!INCLUDE [header](includes/common-includes/python-run-program.md)]
    ::: zone-end
1. _operations.json_ 코드가 `pipelineTopologyList` 및 `livePipelineList` 직접 메서드를 호출합니다. 이전 빠른 시작을 완료한 후에 리소스를 정리했다면 이 프로세스가 빈 목록을 반환한 다음, 일시 중지됩니다. Enter 키를 누릅니다.

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
   Executing operation WaitForInput

   Press Enter to continue
   ```

   터미널 창에 직접 메서드 호출의 다음 세트가 표시됩니다.

   - pipelineTopologyUrl을 사용하는 `pipelineTopologySet`에 대한 호출
   - 다음 본문을 사용하는 `livePipelineSet`에 대한 호출

     ```
     {
       "@apiVersion": "1.1",
       "name": "Sample-Pipeline-1",
       "properties": {
         "topologyName": "EVRToFilesOnMotionDetection",
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

   - 라이브 파이프라인 및 비디오 흐름을 시작하는 `livePipelineActivate`에 대한 호출입니다.
   - 라이브 파이프라인이 실행 중 상태임을 나타내는 `livePipelineList`에 대한 두 번째 호출입니다.

1. TERMINAL 창의 출력은 Enter를 눌러 계속 진행할 때 일시 중지됩니다. 아직 Enter 키를 선택하지 마세요. 위로 스크롤하면 호출한 직접 메서드에 대한 JSON 응답 페이로드가 보입니다.
1. Visual Studio Code에서 출력 창으로 전환합니다. Video Analytics Edge 모듈에서 IoT 허브에 보내는 메시지가 표시됩니다. 이 빠른 시작의 다음 섹션에서는 이러한 메시지를 설명합니다.
1. 파이프라인 토폴로지는 계속 실행되고 결과를 출력합니다. RTSP 시뮬레이터가 원본 비디오를 계속 반복합니다. 파이프라인 토폴로지를 중지하려면 TERMINAL 창으로 돌아가서 Enter를 선택합니다.

일련의 다음 호출은 리소스를 정리합니다.

- `livePipelineDeactivate`를 호출하면 라이브 파이프라인이 비활성화됩니다.
- `livePipelineDelete`를 호출하면 라이브 파이프라인이 삭제됩니다.
- `pipelineTopologyDelete` 호출은 토폴로지를 삭제합니다.
- 마지막 `pipelineTopologyList` 호출은 목록이 비어 있음을 보여줍니다.

## <a name="interpret-results"></a>결과 해석

파이프라인 토폴로지를 실행하면 동작 감지기 프로세서 노드의 결과가 IoT Hub 싱크 노드를 통해 IoT 허브에 전달됩니다. Visual Studio Code의 OUTPUT 창에 표시되는 메시지에는 body 섹션과 applicationProperties 섹션이 포함되어 있습니다. 자세한 내용은 [IoT Hub 메시지 만들기 및 읽기](../../../iot-hub/iot-hub-devguide-messages-construct.md)를 참조하세요.

다음 메시지에서 Video Analyzer 에지 모듈은 애플리케이션 속성 및 본문의 콘텐츠를 정의합니다.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 이벤트

파이프라인 토폴로지가 인스턴스화되면 RTSP 원본 노드가 rtspsim-live555 컨테이너에서 실행되는 RTSP 서버에 연결을 시도합니다. 연결에 성공하면 다음 이벤트가 출력됩니다.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [ava-sample-device/avaadge]:
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

앞의 출력에서

- 이 메시지는 진단 이벤트 MediaSessionEstablished입니다. RTSP 원본 노드(주체)에서 RTSP 시뮬레이터와의 연결을 설정하고 라이브 피드(시뮬레이션된)를 받기 시작했다는 것을 나타냅니다.
- applicationProperties에서 주체는 메시지가 생성된 파이프라인 토폴로지의 노드를 참조합니다. 여기서는 메시지가 RTSP 원본 노드에서 시작됩니다.
- applicationProperties에서 eventType은 이 이벤트가 진단 이벤트임을 나타냅니다.
- eventTime 값은 이벤트가 발생한 시간입니다.
- body 섹션에는 진단 이벤트에 대한 데이터가 포함됩니다. 이 예제의 데이터는 SDP(세션 설명 프로토콜) 세부 정보로 구성됩니다.

### <a name="recordingstarted-event"></a>RecordingStarted 이벤트

동작이 감지되면 신호 게이트 프로세서 노드가 활성화되고 파이프라인 토폴로지의 파일 싱크 노드가 MP4 파일 쓰기를 시작합니다. 파일 싱크 노드가 작업 이벤트를 보냅니다. 유형은 동작 감지 프로세서의 결과임을 나타내기 위해 motion으로 설정됩니다. eventTime 값은 동작이 발생한 UTC 시간입니다. 이 프로세스에 대한 자세한 내용은 이 빠른 시작의 [개요](detect-motion-record-video-edge-devices.md#overview) 섹션을 참조하세요.

다음은 이 메시지의 예제입니다.

```
[IoTHubMonitor] [05:37:27 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation&quot;: &quot;/var/media/sampleFilesFromEVR-filesinkOutput-20210511T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/fileSink",
    "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
    "eventTime": "2021-05-11T05:37:27.713Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

위의 메시지에서

- applicationProperties에서 주체는 메시지가 생성된 파이프라인의 노드를 참조합니다. 여기서는 메시지가 파일 싱크 노드에서 시작됩니다.
- applicationProperties에서 eventType은 이 이벤트가 작동 중임을 나타냅니다.
- eventTime 값은 이벤트가 발생한 시간입니다. 이 시간은 MediaSessionEstablished가 지나고 비디오 흐름이 시작된 지 5-6초 후입니다. 이 시간은 [자동차가 주차장에 진입하기 시작한](#review-the-sample-video) 5-6초 마크에 해당합니다.
- body 섹션에는 운영 이벤트에 대한 데이터가 포함됩니다. 이 경우 테이터는 outputType 및 outputLocation으로 구성됩니다.
- outputType 변수는 이 정보가 파일 경로에 대한 것임을 나타냅니다.
- outputLocation 값은 에지 모듈에서 MP4 파일의 위치입니다.

### <a name="recordingstopped-and-recordingavailable-events"></a>RecordingStopped 및 RecordingAvailable 이벤트

[파이프라인 토폴로지](../pipeline.md)에서 신호 게이트 프로세서 노드의 속성을 살펴보면 활성화 시간이 5초로 설정된 것을 볼 수 있습니다. 따라서 **RecordingStarted** 이벤트가 수신된 후 약 5초가 지나면 다음이 표시됩니다.

- 녹화가 중지되었음을 나타내는 **RecordingStopped** 이벤트입니다.
- 이제 MP4 파일을 볼 수 있음을 나타내는 **RecordingAvailable** 이벤트입니다.

일반적으로 두 이벤트는 서로 몇 초 간격을 두고 내보내집니다.

## <a name="play-the-mp4-clip"></a>MP4 클립 재생

MP4 파일은 VIDEO_OUTPUT_FOLDER_ON_DEVICE 키를 사용하여 .env 파일에서 구성한 에지 디바이스의 디렉터리에 기록됩니다. 기본값을 사용한 경우 결과는 /var/media/ 폴더에 있어야 합니다.

MP4 클립을 재생하는 방법은 다음과 같습니다.

1. 리소스 그룹으로 이동하여 VM을 찾은 다음, VM에 연결합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/detect-motion-record-video-edge-devices/sample-iot-edge-device.png" alt-text= "Edge device":::

1. Azure 리소스 설정 과정에서 생성된 자격 증명을 사용하여 로그인합니다.
1. 명령 프롬프트에서 관련 디렉터리로 이동합니다. 기본 위치는 /var/media입니다. 이 디렉터리에 MP4 파일이 있습니다.

1. [SCP(Secure Copy)](../../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md)를 사용하여 로컬 머신에 파일을 복사합니다.
1. [VLC 미디어 플레이어](https://www.videolan.org/vlc/) 또는 기타 MP4 플레이어를 사용하여 파일을 재생합니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 빠른 시작을 시도하려면 여기서 만든 리소스를 그대로 유지합니다. 그렇지 않으면 Azure Portal에서 리소스 그룹으로 이동하고, 이 빠른 시작을 실행한 리소스 그룹을 선택한 다음, 모든 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

- [사용자 고유의 모델로 Live Video Analytics 실행](analyze-live-video-use-your-model-http.md) 빠른 시작에 따라 라이브 비디오 피드에 AI를 적용합니다.
- 고급 사용자에 대한 추가 문제를 검토합니다.

  - RTSP 시뮬레이터를 사용하는 대신 RTSP를 지원하는 [IP 카메라](https://en.wikipedia.org/wiki/IP_camera)를 사용합니다. [ONVIF 규격 제품](https://www.onvif.org/conformant-products/) 페이지에서 RTSP를 지원하는 IP 카메라를 찾을 수 있습니다. G, S 또는 T 프로필을 준수하는 디바이스를 찾습니다.
  - Azure에서 Linux VM을 사용하는 대신 AMD64 또는 x64 Linux 디바이스를 사용합니다. 이 디바이스는 IP 카메라와 동일한 네트워크에 있어야 합니다. [Linux에 Azure IoT Edge 런타임 설치](../../../iot-edge/how-to-install-iot-edge.md?view=iotedge-2020-11&preserve-view=true)의 지침을 따릅니다. 그런 다음, [가상 Linux 디바이스에 첫 번째 IoT Edge 모듈 배포](../../../iot-edge/quickstart-linux.md?view=iotedge-2020-11&preserve-view=true)의 지침에 따라 디바이스를 Azure IoT Hub에 등록합니다.
