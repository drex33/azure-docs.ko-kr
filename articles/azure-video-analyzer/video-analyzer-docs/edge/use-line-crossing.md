---
title: Azure Video Analyzer를 사용하여 라이브 비디오에서 개체가 가상의 선과 교차할 때 감지
description: 이 빠른 시작에서는 Azure Video Analyzer를 사용하여 (시뮬레이션된) IP 카메라의 라이브 비디오 피드에서 개체가 선과 교차할 때 감지하는 방법을 보여줍니다.
ms.topic: tutorial
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: faa8053ed2d6dbc2b1df0917fcb08dd308fb1910
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131552604"
---
# <a name="tutorial-detect-when-objects-cross-a-virtual-line-in-a-live-video"></a>자습서: 라이브 비디오에서 개체가 가상의 선과 교차할 때 감지

[!INCLUDE [header](includes/edge-env.md)]

이 자습서에서는 Azure Video Analyzer를 사용하여 (시뮬레이션된) IP 카메라의 라이브 비디오 피드에서 개체가 가상의 선과 교차할 때 감지하는 방법을 보여줍니다. 컴퓨터 비전 모델을 적용하여 라이브 비디오 피드의 프레임에 있는 개체를 감지하는 방법을 알아봅니다. 그런 다음, 개체 추적기 노드를 사용하여 다른 프레임에서 해당 개체를 추적하고 그 결과를 선 교차 노드로 보낼 수 있습니다.

선 교차 노드를 사용하면 개체가 가상의 선과 교차할 때 감지할 수 있습니다. 이벤트에는 방향(시계 방향, 시계 반대 방향) 및 방향별 총 카운터 수가 포함됩니다.  

이 자습서에서는 Azure VM을 IoT Edge 디바이스로 사용하고, 시뮬레이션된 라이브 비디오 스트림을 사용합니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>Azure 리소스 설정

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>개요

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/track-objects-live-video/line-crossing-tracker-topology.png" alt-text="라이브 비디오에서 개체가 가상의 선과 교차할 때 감지":::

이 다이어그램에서는 이 자습서의 신호 흐름을 보여 줍니다. [에지 모듈](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)은 RTSP(Real-Time Streaming Protocol) 서버를 호스팅하는 IP 카메라를 시뮬레이션합니다. [RTSP 원본](../pipeline.md#rtsp-source) 노드는 이 서버에서 비디오 피드를 가져와서 비디오 프레임을 [HTTP 확장 프로세서](../pipeline.md#http-extension-processor) 노드로 보냅니다.

HTTP 확장 노드는 프록시 역할을 수행합니다. 매 10번째 비디오 프레임을 지정된 이미지 형식으로 변환합니다. 그런 다음, HTTP를 통해 이미지를 HTTP 엔드포인트 내부에서 AI 모델을 실행하는 다른 에지 모듈에 릴레이합니다. 이 예에서 해당 에지 모듈은 다양한 형식의 개체를 감지할 수 있는 [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3) 모델을 사용하여 빌드됩니다. HTTP 확장 프로세서 노드는 감지 결과를 수집하고 이러한 결과와 모든 비디오 프레임(10번째 프레임뿐만 아니라)을 개체 추적기 노드로 보냅니다. 개체 추적기 노드는 광학 흐름 기술을 사용하여 AI 모델이 적용되지 않은 9개 프레임의 개체를 추적합니다. 추적기 노드는 결과를 IoT Hub 메시지 싱크 노드에 게시합니다. 그러면 이 [IoT Hub 메시지 싱크](../pipeline.md#iot-hub-message-sink) 노드에서는 이러한 이벤트를 [IoT Edge Hub](../../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub)에 보냅니다.

선 교차 노드는 업스트림 개체 추적기 노드로부터 결과를 받습니다. 개체 추적기 노드의 출력에는 감지된 개체의 좌표가 포함됩니다. 선 교차 노드는 이러한 좌표를 선 좌표와 비교 평가합니다. 개체가 선과 교차할 때 선 교차 노드는 이벤트를 내보냅니다. 이벤트는 IoT Edge 허브 메시지 싱크로 전송됩니다. 

이 자습서에서는 다음을 수행합니다.

1. 개발 환경을 설정합니다.
1. 필요한 에지 모듈을 배포합니다.
1. 라이브 파이프라인을 만들고 배포합니다.
1. 결과를 해석합니다.
1. 좌표를 계산하는 방법을 이해합니다.
1. 리소스를 정리합니다.

## <a name="set-up-your-development-environment"></a>개발 환경 설정
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

## <a name="deploy-the-required-modules"></a>필요한 모듈 배포

1. Visual Studio Code에서 *src/edge/deployment.yolov3.template.json* 파일을 마우스 오른쪽 단추로 클릭한 다음, **IoT Edge 배포 매니페스트 생성** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/analyze-live-video-use-your-model-http/generate-deployment-manifest.png" alt-text="IoT Edge 배포 매니페스트 생성":::
1. *deployment.yolov3.amd64.json* 매니페스트 파일은 *src/edge/config* 폴더에 만들어집니다.
1. 마우스 오른쪽 단추로 *src/edge/config/deployment.yolov3.amd64.json* 을 클릭하고 **단일 디바이스용 배포 만들기** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/analyze-live-video-use-your-model-http/deployment-single-device.png" alt-text= "Create Deployment for Single Device":::
1. IoT Hub 디바이스를 선택하라는 메시지가 표시되면 **avasample-iot-edge-device** 를 선택합니다.
1. 약 30초 후 창의 왼쪽 아래 모서리에서 Azure IoT Hub를 새로 고칩니다. 이제 에지 디바이스에 다음과 같은 배포된 모듈이 표시됩니다.

    * **avaedge** 라는 Video Analyzer 에지 모듈.
    * **rtspsim** 모듈 - RTSP 서버를 시뮬레이션하고 라이브 비디오 피드의 원본 역할을 합니다. 
    * **avaextension** 모듈 - 컴퓨터 비전을 이미지에 적용하고, 개체 형식의 여러 클래스를 반환하는 YOLOv3 개체 감지 모델입니다.

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/vscode-common-screenshots/avaextension.png" alt-text= "YoloV3 object detection model":::


## <a name="create-and-deploy-the-live-pipeline"></a>라이브 파이프라인 만들기 및 배포

### <a name="review-sample-video"></a>샘플 비디오 검토

Azure 리소스가 설치되면 고속도로 교통에 대한 짧은 비디오가 IoT Edge 디바이스로 사용하는 Azure의 Linux VM에 복사됩니다. 이 자습서에서는 비디오 파일을 사용하여 라이브 스트림을 시뮬레이션합니다.

[VLC 미디어 플레이어](https://www.videolan.org/vlc/)와 같은 애플리케이션을 엽니다. Ctrl+N을 선택한 다음, [고속도로 교차로 샘플 비디오](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) 링크를 붙여넣어 재생을 시작합니다. 고속도로 교통에서 이동하는 많은 차량의 영상이 표시됩니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LTY4]

### <a name="examine-and-edit-the-sample-files"></a>샘플 파일 검사 및 편집

Visual Studio Code에서 src/cloud-to-device-console-app 폴더로 이동합니다. 이 폴더에는 앞에서 만든 appsettings.json 파일과 몇 가지 다른 파일이 있습니다.

* **c2d-console-app.csproj**: Visual Studio Code의 프로젝트 파일입니다.
* **operations.json**: 이 파일에는 우리가 실행할 여러 작업이 나열됩니다.
* **Program.cs**: 다음과 같은 작업을 수행하는 샘플 프로그램 코드입니다.
    * 앱 설정을 로드합니다.
    * Video Analyzer 에지 모듈이 공개하는 직접 메서드를 호출합니다. 이 모듈을 통해 [직접 메서드](direct-methods.md)를 호출하여 라이브 비디오 스트림을 분석할 수 있습니다.
    * **터미널** 창에서 프로그램의 출력을 검토하고, **출력** 창에서 모듈이 생성한 이벤트를 살펴볼 수 있도록 일시 중지합니다.
    * 리소스를 정리하는 직접 메서드를 호출합니다.

1. operations.json 파일을 편집합니다.
    
    * 파이프라인 토폴로지의 링크를 변경합니다.
    * `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/line-crossing/topology.json"`
    * `livePipelineSet` 아래에서 이전 링크의 값과 일치하도록 토폴로지의 이름을 편집합니다.
    * `"topologyName" : "LineCrossingWithHttpExtension"`
    * `pipelineTopologyDelete` 아래에서 이름을 편집합니다.
    * `"name" : "LineCrossingWithHttpExtension"`
    
브라우저에서 파이프라인 토폴로지의 URL을 열고 HTTP 확장 노드에 대한 설정을 검사합니다.

```
   "samplingOptions":{
       "skipSamplesWithoutAnnotation":"false",
       "maximumSamplesPerSecond":"2"
   }
```

여기서는 확장 노드가 추론 결과의 포함 여부에 관계 없이 모든 프레임을 다운스트림 개체 추적기 노드에 전달해야 하므로 `skipSamplesWithoutAnnotation`이 `false`로 설정되었습니다. 개체 추적기는 약 15프레임 넘게 개체를 추적할 수 있습니다. AI 모델에는 처리를 위한 최대 FPS가 있으며, 이는 `maximumSamplesPerSecond`에 설정해야 하는 가장 높은 값입니다.

선 교차 노드 매개 변수 자리 표시자 `linecrossingName` 및 `lineCoordinates`도 살펴봅니다. 두 매개 변수의 기본값이 제공되었지만 operations.json을 사용하여 기본값을 덮어쓰겠습니다. operations.json 파일의 다른 매개 변수(예: rtsp url)를 토폴로지에 전달하는 방법을 살펴봅니다.  

 
## <a name="run-the-sample-program"></a>샘플 프로그램 실행

1. 디버깅 세션을 시작하려면 F5 키를 선택합니다. **터미널** 창에 메시지가 출력되어 표시됩니다.
1. operations.json 코드가 `pipelineTopologyList` 및 `livePipelineList` 직접 메서드를 호출하여 시작됩니다. 이전 빠른 시작/자습서를 마치고 리소스를 정리한 경우 이 프로세스는 빈 목록을 반환하고 일시 중지됩니다. 계속하려면 Enter 키를 선택합니다.
    
    ```
    -------------------------------Executing operation pipelineTopologyList-----------------------  
    Request: pipelineTopologyList  --------------------------------------------------
    {
    "@apiVersion": "1.1"
    }
    ---------------  
    Response: pipelineTopologyList - Status: 200  ---------------
    {
    "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    
    Press Enter to continue
    ```
    
    **터미널** 창에 직접 메서드 호출의 다음 세트가 표시됩니다.
    
    * `pipelineTopologyUrl`의 내용을 사용하는 `pipelineTopologySet`에 대한 호출
    * 다음 본문을 사용하는 `livePipelineSet`에 대한 호출
        
    ```json
    {
    "@apiVersion": "1.1",
    "name": "Sample-Pipeline-1",
    "properties": {
      "topologyName": "LineCrossingWithHttpExtension",
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
    * 라이브 파이프라인 및 비디오 흐름을 시작하는 `livePipelineActivate`에 대한 호출입니다.
    * 라이브 파이프라인이 실행 중 상태임을 나타내는 `livePipelineList`에 대한 두 번째 호출입니다.
1. 터미널 창의 출력이 [계속하려면 Enter를 누르세요]라는 메시지에서 일시 중지됩니다. 아직 Enter 키를 선택하지 마세요. 위로 스크롤하여 호출한 직접 메서드에 대한 JSON 응답 페이로드를 확인합니다.
1. Visual Studio Code에서 출력 창으로 전환합니다. Video Analyzer 모듈에서 IoT 허브에 보내는 메시지가 표시됩니다. 이 자습서의 다음 섹션에서는 이러한 메시지를 설명합니다.
1. 라이브 파이프라인은 계속 실행되고 결과를 출력합니다. RTSP 시뮬레이터가 원본 비디오를 계속 반복합니다. 라이브 파이프라인을 중지하려면 **TERMINAL** 창으로 돌아가서 Enter를 선택합니다.
1. 일련의 다음 호출은 리소스를 정리합니다.

    * `livePipelineDeactivate`를 호출하면 라이브 파이프라인이 비활성화됩니다.
    * `livePipelineDelete`를 호출하면 라이브 파이프라인이 삭제됩니다.
    * `pipelineTopologyDelete`를 호출하면 파이프라인 토폴로지가 삭제됩니다.
    * `pipelineTopologyList`에 대한 최종 호출은 목록이 비어 있음을 보여 줍니다.
    
## <a name="interpret-results"></a>결과 해석

라이브 파이프라인을 실행하면 HTTP 확장 프로세서 노드의 결과가 IoT Hub 메시지 싱크 노드를 통해 IoT 허브에 전송됩니다. **출력** 창에 표시되는 메시지에는 `body` 섹션 및 `applicationProperties` 섹션이 포함되어 있습니다. 자세한 내용은 [IoT Hub 메시지 만들기 및 읽기](../../../iot-hub/iot-hub-devguide-messages-construct.md)를 참조하세요.

다음 메시지에서 Video Analyzer 모듈은 애플리케이션 속성 및 본문의 콘텐츠를 정의합니다.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 이벤트

라이브 파이프라인이 활성화되면 RTSP 원본 노드는 rtspsim-live555 컨테이너에서 실행되는 RTSP 서버에 연결을 시도합니다. 연결에 성공하면 다음 이벤트가 출력됩니다. 이벤트 유형은 **MediaSessionEstablished** 입니다.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [avasample-iot-edge-device/avaedge]:
{  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/cafetaria-01.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-05-06T17:33:09.554Z",
    "dataVersion": "1.0"
  },
  }
}
```

이 메시지에서 다음 세부 정보를 확인합니다.

* 메시지는 진단 이벤트입니다. **MediaSessionEstablished** 는 RTSP 원본 노드(주체)에서 RTSP 시뮬레이터에 연결되었고 시뮬레이션된 라이브 피드를 받기 시작했음을 나타냅니다.
* `applicationProperties`에서 "subject"는 메시지가 라이브 파이프라인의 RTSP 원본 노드에서 생성되었음을 나타냅니다.
* `applicationProperties`에서 "eventType"은 이 이벤트가 진단 이벤트임을 나타냅니다.
* "eventTime"은 이벤트가 발생한 시간을 나타냅니다.
* 본문에는 진단 이벤트에 대한 데이터가 포함됩니다. 이 경우 데이터는 [SDP(세션 설명 프로토콜)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 세부 정보로 구성됩니다.

## <a name="line-crossing-events"></a>선 교차 이벤트

HTTP 확장 프로세서 노드는 0번째, 15번째, 30번째, …번째 프레임을 avaextension 모듈에 보내고, 추론 결과를 받습니다. 그런 다음, 이러한 결과와 모든 비디오 프레임을 개체 추적기 노드로 보냅니다. 개체 추적기는 모든 프레임(0, 1, 2,...)에 대한 추론 결과를 생성합니다. 그러면 선 교차 노드가 추론 결과를 토폴로지에 지정된 선 좌표와 대조하여 검사합니다. 개체가 이러한 좌표와 교차하는 경우 이벤트가 트리거됩니다. 이벤트는 다음과 같습니다.
```
{
  "body": {
    "timestamp": 145865319410261,
    "inferences": [
      {
        "type": "event",
        "subtype": "lineCrossing",
        "inferenceId": "8f4f7b25d6654536908bcfe34374a15e",
        "relatedInferences": [
          "c9ea5decdd6a487089ded249c748cf5b"
        ],
        "event": {
          "name": "LineCrossing1",
          "properties": {
            "counterclockwiseTotal": "35",
            "direction": "counterclockwise",
            "total": "38",
            "clockwiseTotal": "3"
          }
        }
      }
    ]
  },
```
이 메시지에서 다음 세부 정보를 확인합니다.
* 유형이 `event`이고 하위 유형이 `lineCrossing`입니다.
* 교차된 선의 토폴로지에 지정된 `name`이 이벤트에 포함되어 있습니다.
* 모든 방향으로 교차하는 선의 `total` 수.
* `clockwiseTotal`교차 수.
* `counterclockwiseTotal` 교차 수.
* `direction`에 이 이벤트의 방향이 포함되어 있습니다.

> [!NOTE] 
> 이 자습서의 원클릭 배포를 사용하여 Azure 리소스를 배포한 경우 표준 DS1 가상 머신이 만들어집니다. 그러나 YOLO와 같은 리소스 집약적 AI 모델에서 정확한 결과를 얻으려면 VM 크기를 늘려야 할 수 있습니다. [VM 크기를 조정](../../../virtual-machines/windows/resize-vm.md)하여 요구 사항에 따라 vcpus 및 메모리 수를 늘립니다. 그런 다음 라이브 파이프라인을 다시 활성화하여 추론을 확인합니다.

## <a name="customize-for-your-own-environment"></a>환경에 맞게 사용자 지정

이 자습서는 선의 올바른 선 좌표를 계산하는 데 사용되었던 샘플 비디오에 적용됩니다. 토폴로지 파일을 검사하면 `lineCoordinates` 매개 변수에 `[[0.5,0.1], [0.5,0.9]]` 값이 포함된 것을 볼 수 있습니다.

이 값은 무엇을 의미할까요? 2D 이미지에 선을 그리려면 A와 B라는 두 개의 점이 필요하며, 두 점을 잇는 가상의 선이 있습니다. 각 점은 전체 이미지 해상도와 관련된 위치를 결정하기 위해 고유의 x 및 y 좌표를 갖습니다. 이 예제에서 점 A는 `[0.5,0.1]`이고 점 B는 `[0.5,0.9]`입니다. 다음은 이 선을 시각적으로 표현한 것입니다.
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/track-objects-live-video/line-crossing-visual-example.png" alt-text="그림에서 선 교차를 보여주는 시각적 예제":::

이 이미지에서 점 A와 점 B 사이에 선이 있습니다. 선을 넘어 이동하는 모든 개체는 이 자습서의 앞부분에서 설명한 것처럼 방향과 같은 속성을 사용하여 이벤트를 만듭니다. 또한 왼쪽 아래 모서리를 보면 x축과 y축을 확인할 수 있습니다. 이것은 선 교차 노드에 필요한 값으로 좌표를 정규화하는 방법을 설명하기 위한 그림입니다. 

다음은 계산 예제입니다. 비디오 해상도가 1920 x 1080이라고 가정하겠습니다. 1920 및 1080은 각각 x축과 y축의 픽셀 수입니다.
사용하려는 비디오 프레임에서 이미지를 만듭니다. 이제 이미지 편집기 프로그램(즉, MSPaint)에서 해당 이미지를 엽니다. 점 A를 지정하려는 위치로 커서를 이동합니다. 왼쪽 아래 모서리에 해당 커서 위치의 x 및 y 좌표가 표시됩니다.
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/track-objects-live-video/line-crossing-mspaint-coordinates.png" alt-text="MSPaint를 사용한 선 교차의 시각적 예제":::

이러한 값을 적어 두고, 점 B에도 같은 과정을 반복하여 동일한 값을 적어 둡니다. 이제 점 A의 x 및 y 값과 점 B의 x 및 y 값이 생겼습니다(예: 점 A: x=1024, y=96 점 B: x=1024, y=960). 우리에게 필요한 것은 0과 1 사이의 숫자이므로 이러한 값은 선 교차 노드에 적합한 값이 아닌 것 같습니다. 값을 계산하려면 다음 수식을 적용합니다.

`x coordinate / image resolution along x axis` - 이 예제에서는 `1024/1920 = 0.5`입니다. y에도 동일한 작업을 수행하면 `96/1080=0.1`입니다. 이것이 점 A의 정규화된 좌표입니다. 점 B에도 같은 과정을 반복합니다. 결국에는 이 자습서의 앞부분에서 설명한 것처럼 0과 1 사이의 값 배열 `[[0.5,0.1], [0.5,0.9]]`를 얻게 됩니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

* 라이브 파이프라인을 통해 다른 비디오를 시도해 보세요.
