---
title: 비디오를 사용하여 추론 메타데이터 녹화 및 스트리밍 - Azure Video Analyzer
description: 이 자습서에서는 Azure Video Analyzer를 사용하여 비디오 및 추론 메타데이터를 클라우드에 녹화하고 시각적 유추 메타데이터를 사용하여 녹화를 재생하는 방법을 알아봅니다.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fa2f6c4ec9a64dacc719f62f6a87c6d9d6e112d2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319334"
---
# <a name="tutorial-record-and-stream-inference-metadata-with-video"></a>자습서: 비디오를 사용하여 추론 메타데이터 녹화 및 스트리밍

[!INCLUDE [header](includes/edge-env.md)]

이 자습서에서는 Azure Video Analyzer를 사용하여 라이브 비디오 및 추론 메타데이터를 클라우드에 녹화하고 시각적 유추 메타데이터를 사용하여 녹화를 재생하는 방법을 알아봅니다. 이 사용 사례에서는 사용자 지정 모델을 사용하여 개체 **(yoloV3)** 를 감지하고 Video Analyzer 프로세서 **(개체 추적기)** 를 사용하여 개체를 추적하는 동안 비디오를 연속 녹화합니다. 비디오가 연속 녹화되므로 감지 및 추적되는 개체의 추론 메타데이터도 녹화됩니다. 

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>추천 참고 자료  

시작하기 전에 다음 문서를 참조하세요.

* [Azure Video Analyzer on IoT Edge 개요](../overview.md)
* [Azure Video Analyzer on IoT Edge 용어](../terminology.md)
* [Video Analyzer 파이프라인 개념](../pipeline.md) 
* [연속 비디오 녹화](../continuous-video-recording.md)
* [빠른 시작: 자체 모델로 라이브 비디오 분석l - HTTP](analyze-live-video-use-your-model-http.md)
* [빠른 시작: 라이브 비디오의 개체 추적](track-objects-live-video.md)

## <a name="prerequisites"></a>필수 구성 요소

이 자습서의 필수 구성 요소는 [!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]입니다.

## <a name="set-up-azure-resources"></a>Azure 리소스 설정

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)
[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>개요
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/record-stream-inference-data-with-video/overview.svg" alt-text="파이프라인":::

이 다이어그램은 [파이프라인](../pipeline.md) 및 원하는 시나리오를 수행하는 추가 모듈을 그림으로 표현한 것입니다. 다음과 같은 세 가지 IoT Edge 모듈이 있습니다.
* Video Analyzer 모듈.
* HTTP 엔드포인트 내부에서 AI 모델을 실행하는 에지 모듈. 이 AI 모듈은 다양한 형식의 개체를 감지할 수 있는 [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3) 모델을 사용합니다.
* RTSP 카메라를 시뮬레이션하는 [RTSP 시뮬레이터 모듈](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)

다이어그램에서 볼 수 있듯이, 파이프라인에서 [RTSP 원본](../pipeline.md#rtsp-source) 노드를 사용하여 고속도로에서 시뮬레이션된 라이브 교통 비디오를 캡처하고 해당 비디오를 두 개의 경로로 보냅니다.

* 첫 번째 경로는 HTTP 확장 노드에 대한 것입니다. HTTP 확장 노드는 프록시 역할을 수행합니다. 매 10번째 비디오 프레임을 지정된 이미지 형식으로 변환합니다. 그런 다음 HTTP를 통해 이미지를 HTTP 엔드포인트 뒤에서 AI 모델을 실행하는 다른 에지 모듈로 릴레이합니다. 이 예에서 해당 에지 모듈은 다양한 형식의 개체를 감지할 수 있는 YOLOv3 모델을 사용하여 빌드됩니다. HTTP 확장 프로세서 노드는 감지 결과를 수집하고 이러한 결과와 모든 비디오 프레임(10번째 프레임뿐만 아니라)을 개체 추적기 노드로 보냅니다. 개체 추적기 노드는 광학 흐름 기술을 사용하여 AI 모델이 적용되지 않은 9개 프레임의 개체를 추적합니다. 추적기 노드는 결과를 비디오 싱크 노드 및 IoT Hub 싱크 노드에 게시합니다. [비디오 싱크](../pipeline.md#video-sink) 노드는 녹화된 비디오와 함께 재생될 개체 추적기 노드의 추론 메타데이터를 사용합니다. 그러면 [IoT Hub 메시지 싱크](../pipeline.md#iot-hub-message-sink) 노드에서는 이러한 이벤트를 [IoT Edge Hub](../../../iot-fundamentals/iot-glossary.md#iot-edge-hub)에 보냅니다.

* 두 번째 경로는 비디오를 연속 녹화하기 위해 RTSP 원본에서 비디오 싱크 노드로 직접 전달하는 것입니다. 이 자습서에서 사용되는 비디오는 [고속도로 교차로 샘플 비디오](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv)입니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

이 자습서에서는 다음을 수행합니다.

1. 개발 환경을 설정합니다.
1. 필요한 에지 모듈을 배포합니다.
1. 라이브 파이프라인을 만들고 배포합니다.
1. 결과를 해석합니다.
1. 리소스를 정리합니다.

## <a name="set-up-your-development-environment"></a>개발 환경 설정
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

## <a name="examine-the-sample-files"></a>샘플 파일 검사

Visual Studio Code에서 src/edge로 이동합니다. 만든 .env 파일 및 몇 가지 배포 템플릿 파일이 표시됩니다. 이 템플릿은 에지 디바이스(Azure Linux VM)에 배포할 에지 모듈을 정의합니다. .env 파일에는 Video Analyzer 자격 증명처럼 이러한 템플릿에 사용되는 변수의 값이 포함되어 있습니다.

src/edge/deployment.yolov3.template.json 파일을 엽니다. **모듈** 섹션 아래에는 위의 "개념" 섹션에서 나열한 항목에 해당하는 3개 항목이 있습니다.

* **avaedge**: Video Analyzer on IoT Edge 모듈
* **yolov3**: YOLO v3 모델을 사용하여 빌드된 AI 모듈
* **rtspsim**: RTSP 시뮬레이터


다음으로, src/cloud-to-device-console-app 폴더로 이동합니다. 이 폴더에는 앞에서 만든 appsettings.json 파일과 몇 가지 다른 파일이 있습니다.

* **c2d-console-app.csproj**: Visual Studio Code의 프로젝트 파일입니다.
* **operations.json**: 이 파일에는 우리가 실행할 여러 작업이 나열됩니다.
* **Program.cs**: 다음과 같은 작업을 수행하는 샘플 프로그램 코드입니다.
    * 앱 설정을 로드합니다.

    * Video Analyzer on IoT Edge 모듈이 공개하는 직접 메서드를 호출합니다. 이 모듈을 통해 [직접 메서드](direct-methods.md)를 호출하여 라이브 비디오 스트림을 분석할 수 있습니다.

    * **터미널** 창에서 프로그램의 출력을 검토하고, **출력** 창에서 모듈이 생성한 이벤트를 살펴볼 수 있도록 일시 중지합니다.
    * 리소스를 정리하는 직접 메서드를 호출합니다.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge 배포 매니페스트 생성 및 배포 

1. 마우스 오른쪽 단추로 _src/edge/deployment.yolov3.template.json_ 파일을 클릭한 다음, **IoT Edge 배포 매니페스트 생성** 을 선택합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/analyze-live-video-use-your-model-http/generate-deployment-manifest.png" alt-text="IoT Edge 배포 매니페스트 생성의 스크린샷":::

    * _deployment.yolov3.amd64.json_ 매니페스트 파일은 _src/edge/config_ 폴더에 만들어집니다.
1. 마우스 오른쪽 단추로 _src/edge/config/deployment.yolov3.amd64.json_ 을 클릭하고 **단일 디바이스용 배포 만들기** 를 선택합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/analyze-live-video-use-your-model-http/deployment-single-device.png" alt-text= "Screenshot of Create Deployment for Single Device":::

1. IoT Hub 디바이스를 선택하라는 메시지가 표시되면 **avasample-iot-edge-device** 를 선택합니다.
1. 약 30초 후 창의 왼쪽 아래 모서리에서 Azure IoT Hub를 새로 고칩니다. 이제 에지 디바이스에 다음과 같은 배포된 모듈이 표시됩니다.

   - **avaedge** 모듈 - Video Analyzer 모듈입니다.
   - **rtspsim** 모듈 - RTSP 서버를 시뮬레이션하고 라이브 비디오 피드의 원본 역할을 합니다.
   - **yolov3** 모듈 - 컴퓨터 비전을 이미지에 적용하고, 개체 형식의 여러 클래스를 반환하는 YoloV3 개체 감지 모델입니다.

     > [!div class="mx-imgBorder"]
     > :::image type="content" source="./media/vscode-common-screenshots/avaextension.png" alt-text= "Screenshot of YoloV3 object detection model":::
     
## <a name="create-and-deploy-the-live-pipeline"></a>라이브 파이프라인 만들기 및 배포

### <a name="edit-the-sample-files"></a>샘플 파일 업로드

1. Visual Studio Code에서 **확장** 탭을 열고(또는 Ctrl+Shift+X를 누름) Azure IoT Hub를 검색합니다.
1. 마우스 오른쪽 단추를 클릭하고 **확장 설정** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="확장 설정":::
1. "자세한 정보 메시지 표시"를 검색하고 활성화합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="자세한 정보 메시지 표시":::
1. src/cloud-to-device-console-app/operations.json으로 이동합니다.
1. **pipelineTopologySet** 노드 아래에서 다음을 편집합니다.
 
    `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-with-httpExtension-and-objectTracking/topology.json" `
    
1. 다음으로, **livePipelineSet** 및 **pipelineTopologyDelete** 노드에서 **topologyName** 값이 이전 파이프라인 토폴로지의 **name** 속성 값과 일치하는지 확인합니다.

    `"pipelineTopologyName" : "CVRHttpExtensionObjectTracking"`
1. 브라우저에서 [파이프라인 토폴로지](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-with-httpExtension-and-objectTracking/topology.json)를 열고 videoName을 살펴봅니다. `sample-cvr-with-inference-metadata`로 하드 코딩되었습니다. 자습서에서 이를 허용할 수 있습니다. 프로덕션 환경에서는 고유한 각 RTSP 카메라를 고유한 이름을 가진 비디오 리소스에 녹화하도록 주의해야 합니다.  

1. HTTP 확장 노드의 설정을 검사합니다.

  ```=
     "samplingOptions":{
         "skipSamplesWithoutAnnotation":"false",
         "maximumSamplesPerSecond":"2"
    }
  ```

여기서는 확장 노드가 추론 결과의 포함 여부에 관계 없이 모든 프레임을 다운스트림 개체 추적기 노드에 전달해야 하므로 `skipSamplesWithoutAnnotation`이 `false`로 설정되었습니다. 개체 추적기는 약 15프레임 넘게 개체를 추적할 수 있습니다. AI 모델에는 처리를 위한 최대 FPS가 있으며, 이는 `maximumSamplesPerSecond`에 설정해야 하는 가장 높은 값입니다.


## <a name="run-the-sample-program"></a>샘플 프로그램 실행

1. F5 키를 선택하여 디버깅 세션을 시작합니다. **터미널** 창에 일부 메시지가 출력됩니다.
1. pipelineTopologyList 및 livePipelineList를 호출하면 operations.json 파일이 시작됩니다. 이전 빠른 시작 또는 자습서를 마친 후 리소스를 정리했다면 이 작업은 다음과 같이 빈 목록을 반환하고, 사용자가 **Enter** 키를 선택할 수 있도록 일시 중지됩니다.
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
1. **터미널** 창에서 **Enter** 키를 선택하면 다음과 같은 직접 메서드 세트가 호출됩니다.
   * 이전 pipelinetopologyUrl을 사용하여 pipelineTopologySet 호출
   * 다음 본문을 사용하여 livePipelineSet 호출
     
        ```
        {
          "@apiVersion": "1.1",
          "name": "Sample-Pipeline-1",
          "properties": {
            "topologyName": "CVRHttpExtensionObjectTracking",
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
   * 라이브 파이프라인 및 비디오 흐름을 시작하는 livePipelineActivate에 대한 호출입니다.
   * 라이브 파이프라인이 실행 중 상태임을 나타내는 livePipelineList에 대한 두 번째 호출입니다.
1. 터미널 창의 출력이 [계속하려면 Enter를 누르세요]라는 메시지에서 일시 중지됩니다. 아직 Enter 키를 선택하지 마세요. 위로 스크롤하여 호출한 직접 메서드에 대한 JSON 응답 페이로드를 확인합니다.
1. Visual Studio Code에서 출력 창으로 전환합니다. Video Analyzer on IoT Edge 모듈에서 IoT 허브에 보내는 메시지가 표시됩니다.
1. 파이프라인은 계속 실행되고 결과를 출력합니다. RTSP 시뮬레이터가 원본 비디오를 계속 반복합니다. 라이브 파이프라인을 중지하려면 **TERMINAL** 창으로 돌아가서 Enter를 선택합니다.
1. 일련의 다음 호출은 리소스를 정리합니다.

    * livePipelineDeactivate를 호출하면 라이브 파이프라인이 비활성화됩니다.
    * livePipelineDelete를 호출하면 라이브 파이프라인이 삭제됩니다.
    * pipelineTopologyDelete를 호출하면 파이프라인 토폴로지가 삭제됩니다.
    * 마지막으로 pipelineTopologyList를 호출하면 목록이 비어 있음을 보여줍니다.       

## <a name="interpret-results"></a>결과 해석

라이브 파이프라인을 실행하면 HTTP 확장 프로세서 노드의 결과가 개체 추적기 노드, IoT Hub 싱크 노드를 지나 IoT 허브에 전송됩니다. **OUTPUT** 창에 표시되는 메시지에는 본문 섹션과 applicationProperties 섹션이 포함되어 있습니다. 자세한 내용은 [IoT Hub 메시지 만들기 및 읽기](../../../iot-hub/iot-hub-devguide-messages-construct.md)를 참조하세요.

다음 메시지에서 Video Analyzer 모듈은 애플리케이션 속성 및 본문의 콘텐츠를 정의합니다.

## <a name="diagnostics-events"></a>진단 이벤트
### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 이벤트

라이브 파이프라인이 활성화되면 RTSP 원본 노드는 rtspsim-live555 컨테이너에서 실행되는 RTSP 서버에 연결을 시도합니다. 연결에 성공하면 다음 이벤트가 출력됩니다. 이벤트 유형은 Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished입니다.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [avasample-iot-edge-device/avaedge]:
{  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
  }
}
```

이 메시지에서 다음 세부 정보를 확인합니다.

* 메시지는 진단 이벤트입니다. MediaSessionEstablished는 RTSP 원본 노드(주체)에서 RTSP 시뮬레이터에 연결되었고 시뮬레이션된 라이브 피드를 받기 시작했음을 나타냅니다.
* applicationProperties에서 주체는 메시지가 라이브 파이프라인의 RTSP 원본 노드에서 생성되었음을 나타냅니다.
* applicationProperties에서 eventType은 이 이벤트가 진단 이벤트임을 나타냅니다.
* eventTime은 이벤트가 발생한 시간을 나타냅니다.
* 본문에는 진단 이벤트에 대한 데이터가 포함됩니다. 이 경우 데이터는 [SDP(세션 설명 프로토콜)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 세부 정보로 구성됩니다.

## <a name="operational-events"></a>작업 이벤트

### <a name="object-tracking-events"></a>개체 추적 이벤트

HTTP 확장 프로세서 노드는 0번째, 15번째, 30번째, …번째 프레임을 yolov3 모듈에 보내고, 추론 결과를 받습니다. 그런 다음, 이러한 결과와 모든 비디오 프레임을 개체 추적기 노드로 보냅니다. 프레임 0에서 개체가 감지된 후 개체 추적기가 해당 개체에 고유한 sequenceId를 할당한다고 가정하겠습니다. 그리고 1, 2,..., 14프레임에서 개체 추적기가 해당 개체를 추적할 수 있는 경우 개체 추적기는 동일한 sequenceId를 사용하여 결과를 출력합니다. 결과의 다음 코드 조각에서 `sequenceId`가 반복되지만 개체가 이동하는 동안 경계 상자의 위치가 변경되었습니다.

프레임 M:

```json
  {
    "type": "entity",
    "subtype": "objectDetection",
    "inferenceId": "4d325fc4dc7a43b2a781bf7d6bdb3ff0",
    "sequenceId": "0999a1dde5b241c3a0b2db025f87ab32",
    "entity": {
      "tag": {
        "value": "car",
        "confidence": 0.95237225
      },
      "box": {
        "l": 0.0025893003,
        "t": 0.550063,
        "w": 0.1086607,
        "h": 0.12116724
      }
    }
  },
```

프레임 N:

```json
{
  "type": "entity",
  "subtype": "objectDetection",
  "inferenceId": "317aafdab7e940388be1e4c4cc58c366",
  "sequenceId": "0999a1dde5b241c3a0b2db025f87ab32",
  "entity": {
    "tag": {
      "value": "car",
      "confidence": 0.95237225
    },
    "box": {
      "l": 0.0027777778,
      "t": 0.54901963,
      "w": 0.108333334,
      "h": 0.12009804
    }
  }
},
```

### <a name="recordingstarted-event"></a>RecordingStarted 이벤트
비디오 싱크 노드는 미디어 녹화가 시작되면 **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStarted** 유형의 다음 이벤트를 내보냅니다.

```
[IoTHubMonitor] [9:42:38 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-with-inference-metadata"
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

이름에서 알 수 있듯이, 녹화가 시작되면 RecordingStarted 이벤트가 전송되지만, 미디어 데이터는 아직 자산에 업로드되지 않았을 수 있습니다. 비디오 싱크 노드는 미디어가 업로드되면 **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable** 유형의 다음 이벤트를 내보냅니다.

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [ava-sample-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-with-inference-metadata"
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
[IoTHubMonitor] [11:33:31 PM] Message received from [ava-sample-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-with-inference-metadata"
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

## <a name="streaming-the-recording-with-visual-inference-metadata"></a>시각적 추론 메타데이터를 사용하여 녹화 스트리밍

Azure Portal에 로그인하여 비디오를 살펴보면 라이브 파이프라인에서 만든 Video Analyzer 비디오 리소스를 검사할 수 있습니다.

1. 웹 브라우저를 열고 [Azure Portal](https://portal.azure.com/)로 이동합니다. 자격 증명을 입력하여 포털에 로그인합니다. 기본 보기는 서비스 대시보드입니다.
1. 구독에 있는 리소스 중에서 Video Analyzer 계정을 찾아 계정 창을 엽니다.
1. **비디오 분석기** 목록에서 **비디오** 를 선택합니다.
1. 이름이 `sample-cvr-with-inference-metadata`인 비디오가 나열됩니다. 이 이름은 파이프라인 토폴로지 파일에서 선택한 이름입니다.
1. 비디오를 선택합니다.
1. 비디오 세부 정보 페이지에서 **재생** 아이콘을 클릭합니다.
1. 추론 메타데이터를 비디오에서 경계 상자로 보려면 **경계 상자** 아이콘(빨간색 원)을 클릭합니다.
   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/record-stream-inference-data-with-video/video-playback.png" alt-text="비디오 재생 스크린샷":::

[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

* RTSP 시뮬레이터 대신 RTSP를 지원하는 [IP 카메라](https://en.wikipedia.org/wiki/IP_camera)를 사용합니다. [ONVIF 규격](https://www.onvif.org/conformant-products/) 제품 페이지에서 프로필 G, S 또는 T를 준수하는 디바이스를 찾아 RTSP를 지원하는 IP 카메라를 검색할 수 있습니다.
* Azure Linux VM 대신 AMD64 또는 X64 Linux 디바이스를 사용합니다. 이 디바이스는 IP 카메라와 동일한 네트워크에 있어야 합니다. [Linux에 Azure IoT Edge 런타임 설치](../../../iot-edge/how-to-install-iot-edge.md)의 지침을 따릅니다. 그런 다음, [가상 Linux 디바이스에 첫 번째 IoT Edge 모듈 배포](../../../iot-edge/quickstart-linux.md) 빠른 시작의 지침에 따라 디바이스를 Azure IoT Hub에 등록합니다.
 
