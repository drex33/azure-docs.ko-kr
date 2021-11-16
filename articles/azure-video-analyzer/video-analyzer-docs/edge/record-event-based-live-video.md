---
title: 클라우드에 이벤트 기반 비디오 녹화 및 클라우드에서 재생 자습서 - Azure
description: 이 자습서에서는 Azure Video Analyzer를 사용하여 이벤트 기반 비디오를 클라우드에 녹화하고 클라우드에서 이를 재생하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1edec79cd7baa20819e0599b039fbdfe20f35d92
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484810"
---
# <a name="tutorial-event-based-video-recording-and-playback"></a>자습서: 이벤트 기반 비디오 녹화 및 재생

[!INCLUDE [header](includes/edge-env.md)]

이 자습서에서는 Azure Video Analyzer를 사용하여 라이브 비디오 원본의 일부를 클라우드의 Video Analyzer에 선택적으로 녹화하는 방법에 대해 알아봅니다. 이 사용 사례는 이 자습서에서 [EVR(이벤트 기반 비디오 녹화)](../event-based-video-recording-concept.md)이라고 합니다. 라이브 비디오의 일부를 녹화하려면 개체 감지 AI 모델을 사용하여 비디오의 개체를 찾고, 특정 형식의 개체가 감지되는 경우에만 비디오 클립을 녹화합니다. Video Analyzer를 사용하여 녹화된 비디오 클립을 재생하는 방법도 알아봅니다. 이 기능은 관심 있는 비디오 클립을 보관해야 하는 다양한 시나리오에 유용합니다. 

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>추천 참고 자료  

시작하기 전에 다음 문서를 참조하세요.

* [Azure Video Analyzer 개요](../overview.md)
* [Azure Video Analyzer 용어](../terminology.md)
* [Video Analyzer 파이프라인 개념](../pipeline.md) 
* [이벤트 기반 비디오 녹화](../event-based-video-recording-concept.md)
* [자습서: IoT Edge 모듈 개발](../../../iot-edge/tutorial-develop-for-linux.md)
* [deployment.*.template.json을 편집하는 방법](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* [IoT Edge 배포 매니페스트에서 경로를 선언하는 방법](../../../iot-edge/module-composition.md#declare-routes)에 대한 섹션

## <a name="prerequisites"></a>필수 구성 요소

이 자습서의 필수 구성 요소는 다음과 같습니다.
* 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

    [!INCLUDE [azure-subscription-permissions](./includes/common-includes/azure-subscription-permissions.md)]
* 머신에 [Docker를 설치](https://docs.docker.com/desktop/#download-and-install)합니다.
* 다음 확장이 포함된 [Visual Studio Code](https://code.visualstudio.com/)
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

## <a name="set-up-azure-resources"></a>Azure 리소스 설정

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)
[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>개요

이벤트 기반 비디오 녹화는 이벤트에서 트리거되는 비디오 녹화 프로세스를 나타냅니다. 이 이벤트는 다음에서 생성될 수 있습니다.
- 비디오 신호 자체의 처리(예: 비디오에서 움직이는 개체를 감지하는 경우)
- 독립적인 원본(예: 문 열기) 

또는 특정 이벤트가 발생한 것을 추론 서비스에서 감지하는 경우에만 녹화를 트리거할 수도 있습니다. 이 자습서에서는 고속도로에서 이동하는 차량의 비디오를 사용하고, 트럭이 감지될 때마다 비디오 클립을 녹화합니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/overview.svg" alt-text="파이프라인":::

이 다이어그램은 [파이프라인](../pipeline.md) 및 원하는 시나리오를 수행하는 추가 모듈을 그림으로 표현한 것입니다. 다음 4개의 IoT Edge 모듈이 포함되어 있습니다.

* Video Analyzer on IoT Edge 모듈
* HTTP 엔드포인트 내부에서 AI 모델을 실행하는 에지 모듈. 이 AI 모듈은 다양한 형식의 개체를 감지할 수 있는 [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3) 모델을 사용합니다.
* 개체를 계산하고 필터링하는 사용자 지정 모듈(다이어그램에서는 개체 카운터라고 함). 이 자습서에서는 개체 카운터를 빌드하고 배포합니다.
* RTSP 카메라를 시뮬레이션하는 [RTSP 시뮬레이터 모듈](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)
    
다이어그램에서 볼 수 있듯이, 파이프라인에서 [RTSP 원본](../pipeline.md#rtsp-source) 노드를 사용하여 고속도로에서 시뮬레이션된 라이브 교통 비디오를 캡처하고 해당 비디오를 두 개의 경로로 보냅니다.

* 첫 번째 경로는 HTTP 확장 노드에 대한 것입니다. 노드는 `samplingOptions` 필드를 사용하여 사용자가 설정한 값으로 비디오 프레임을 샘플링한 다음, 프레임을 개체 감지기인 AI 모듈 YOLOv3에 이미지로 릴레이합니다. 노드는 모델에서 감지된 개체(교통 차량)인 결과를 받습니다. 그런 다음, HTTP 확장 노드에서 IoT Hub 메시지 싱크 노드를 통해 결과를 IoT Edge 허브에 게시합니다.

* objectCounter 모듈이 IoT Edge 허브에서 메시지를 받도록 설정되며, 여기에는 개체 감지 결과(교통 차량)가 포함됩니다. 모듈은 이러한 메시지를 확인하고 설정을 통해 구성된 특정 형식의 개체를 찾습니다. 이러한 개체가 있으면 이 모듈에서 메시지를 IoT Edge 허브에 보냅니다. 이러한 "개체 발견" 메시지는 파이프라인의 IoT Hub 원본 노드로 라우팅됩니다. 이러한 메시지를 받으면 파이프라인의 IoT Hub 원본 노드가 [신호 게이트 프로세서](../pipeline.md#signal-gate-processor) 노드를 트리거합니다. 그러면 신호 게이트 프로세서 노드가 구성된 시간 동안 열립니다. 비디오는 이 시간 동안 게이트를 통과하여 비디오 싱크 노드로 이동합니다. 그러면 라이브 스트림의 이 부분이 [비디오 싱크](../pipeline.md#video-sink) 노드를 통해 Video Analyzer 계정의 [비디오](../terminology.md#video)에 녹화됩니다. 이 자습서에서 사용되는 비디오는 [고속도로 교차로 샘플 비디오](https://avamedia.blob.core.windows.net/public/camera-300s.mkv)입니다.

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

src/edge/deployment.objectCounter.template.json 파일을 엽니다. **모듈** 섹션 아래에는 위의 "개념" 섹션에서 나열한 항목에 해당하는 4개 항목이 있습니다.

* **avaedge**: Video Analyzer 모듈
* **yolov3**: YOLO v3 모델을 사용하여 빌드된 AI 모듈
* **rtspsim**: RTSP 시뮬레이터
* **objectCounter**: yolov3의 결과에서 특정 개체를 찾는 모듈

objectCounter 모듈의 경우 "image" 값에 사용되는 (${MODULES.objectCounter}) 문자열을 확인합니다. 이는 IoT Edge 모듈을 개발하는 방법에 대한 [자습서](../../../iot-edge/tutorial-develop-for-linux.md)를 기반으로 합니다. Visual Studio Code에서 objectCounter 모듈의 코드가 src/edge/modules/objectCounter 아래에 있음을 자동으로 인식합니다. 

IoT Edge 배포 매니페스트에서 경로를 선언하는 방법은 [이 섹션](../../../iot-edge/module-composition.md#declare-routes)을 참조하세요. 그런 다음, 템플릿 JSON 파일의 경로를 검사합니다. 다음 사항을 확인합니다.

> [!NOTE]
> "truck"이라는 태그가 지정되고 신뢰 수준이 50% 이상인 개체를 찾도록 설정된 objectCounter 모듈의 desired 속성을 확인하세요.

다음으로, src/cloud-to-device-console-app 폴더로 이동합니다. 이 폴더에는 앞에서 만든 appsettings.json 파일과 몇 가지 다른 파일이 있습니다.

* **c2d-console-app.csproj**: Visual Studio Code의 프로젝트 파일입니다.
* **operations.json**: 이 파일에는 우리가 실행할 여러 작업이 나열됩니다.
* **Program.cs**: 다음과 같은 작업을 수행하는 샘플 프로그램 코드입니다.
    * 앱 설정을 로드합니다.

    * Video Analyzer 모듈이 공개하는 직접 메서드를 호출합니다. 이 모듈을 통해 [직접 메서드](direct-methods.md)를 호출하여 라이브 비디오 스트림을 분석할 수 있습니다.

    * **터미널** 창에서 프로그램의 출력을 검토하고, **출력** 창에서 모듈이 생성한 이벤트를 살펴볼 수 있도록 일시 중지합니다.
    * 리소스를 정리하는 직접 메서드를 호출합니다.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge 배포 매니페스트 생성 및 배포 

배포 매니페스트는 에지 디바이스에 배포되는 모듈 및 해당 모듈의 구성 설정을 정의합니다. 다음 단계에 따라 템플릿 파일에서 매니페스트를 생성한 다음, 에지 디바이스에 배포합니다.

Visual Studio Code를 사용하여 [이러한 지침](../../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution)에 따라 Docker에 로그인합니다. 그런 다음, **IoT Edge 솔루션 빌드 및 푸시** 를 선택합니다. 이 단계에서는 src/edge/deployment.objectCounter.template.json을 사용합니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/build-push.png" alt-text="IoT Edge 솔루션 빌드 및 푸시":::

이 작업은 개체 수를 계산하는 objectCounter 모듈을 빌드하고 이미지를 Azure Container Registry로 푸시합니다.

* .env 파일에 CONTAINER_REGISTRY_USERNAME_myacr 및 CONTAINER_REGISTRY_PASSWORD_myacr 환경 변수가 정의되었는지 확인합니다.

이 단계에서는 IoT Edge 배포 매니페스트를 src/edge/config/deployment.objectCounter.amd64.json에 만듭니다. 마우스 오른쪽 단추로 해당 파일을 클릭하고 **단일 디바이스용 배포 만들기** 를 선택합니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/create-deployment-single-device.png" alt-text="단일 디바이스용 배포 만들기":::

Video Analyzer와 관련된 첫 번째 자습서인 경우 Visual Studio Code에서 IoT Hub 연결 문자열을 입력하라는 메시지를 표시합니다. appsettings.json 파일에서 복사할 수 있습니다.

[!INCLUDE [provide-builtin-endpoint](./includes/common-includes/provide-builtin-endpoint.md)]

다음으로, Visual Studio Code에서 IoT Hub 디바이스를 선택하라는 메시지를 표시합니다. avasample-iot-edge-device라는 IoT Edge 디바이스를 선택합니다.

이 단계에서는 IoT Edge 디바이스에 에지 모듈을 배포하는 작업이 시작되었습니다.
약 30초 후에 Visual Studio Code의 왼쪽 아래 섹션에서 Azure IoT Hub를 새로 고칩니다. $edgeAgent, $edgeHub, avaedge, rtspsim, yolov3, objectCounter의 6개 모듈이 배포된 것을 알 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/modules.png" alt-text="배포된 4개 모듈":::

## <a name="run-the-program"></a>프로그램 실행

1. Visual Studio Code에서 **확장** 탭을 열고(또는 Ctrl+Shift+X를 누름) Azure IoT Hub를 검색합니다.
1. 마우스 오른쪽 단추를 클릭하고 **확장 설정** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="확장 설정":::
1. "자세한 정보 메시지 표시"를 검색하고 활성화합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="자세한 정보 메시지 표시":::
1. src/cloud-to-device-console-app/operations.json으로 이동합니다.
1. **pipelineTopologySet** 노드 아래에서 다음을 편집합니다.


    `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json" `
    
1. 다음으로, **livePipelineSet** 및 **pipelineTopologyDelete** 노드에서 **topologyName** 값이 이전 파이프라인 토폴로지의 **name** 속성 값과 일치하는지 확인합니다.

    `"pipelineTopologyName" : "EVRtoVideoSinkOnObjDetect"`
1. 브라우저에서 [파이프라인 토폴로지](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json)를 열고 videoName을 살펴봅니다. `sample-evr-video`로 하드 코딩되었습니다. 자습서에서 이를 허용할 수 있습니다. 프로덕션 환경에서는 고유한 각 RTSP 카메라를 고유한 이름을 가진 비디오 리소스에 녹화하도록 주의해야 합니다.
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
            "topologyName": "EVRtoVideoSinkOnObjDetect",
            "description": "Sample topology description",
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
    
   * 라이브 파이프라인 및 비디오 흐름을 시작하는 livePipelineActivate 호출
   * 라이브 파이프라인이 실행 중 상태임을 보여주는 livePipelineList의 두 번째 호출 
     
1. 이제 **터미널** 창의 출력이 **계속하려면 Enter 키를 누르세요** 라는 메시지에서 일시 중지됩니다. 지금은 **Enter** 키를 선택하지 마세요. 위로 스크롤하여 호출한 직접 메서드에 대한 JSON 응답 페이로드를 확인합니다.
1. 이제 Visual Studio Code에서 **출력** 창으로 전환하면 Video Analyzer 모듈에서 IoT Hub로 보내는 메시지가 표시됩니다.

   이러한 메시지에 대한 내용은 다음 섹션에서 다룹니다.
1. 라이브 파이프라인은 계속 실행되고 비디오를 녹화합니다. RTSP 시뮬레이터가 원본 비디오를 계속 반복합니다. 녹화를 중지하려면 **터미널** 창으로 돌아가서 **Enter** 를 선택합니다. 다음과 같은 일련의 호출을 수행하여 리소스를 정리합니다.

   * livePipelineDeactivate를 호출하면 라이브 파이프라인이 비활성화됩니다.
   * livePipelineDelete를 호출하면 라이브 파이프라인이 삭제됩니다.
   * pipelineTopologyDelete를 호출하면 토폴로지가 삭제됩니다.
   * 마지막으로 pipelineTopologyList를 호출하면 목록이 비어 있음을 보여줍니다.

## <a name="interpret-the-results"></a>결과 해석 

라이브 파이프라인을 실행하면 Video Analytics 모듈이 특정 진단 및 작업 이벤트를 IoT Edge 허브에 보냅니다. 이러한 이벤트는 Visual Studio Code의 **출력** 창에 표시되는 메시지입니다. 여기에는 body 섹션 및 applicationProperties 섹션이 포함되어 있습니다. 이러한 섹션이 나타내는 내용을 이해하려면 [IoT Hub 메시지 작성 및 읽기](../../../iot-hub/iot-hub-devguide-messages-construct.md)를 참조하세요.

다음 메시지에서 애플리케이션 속성 및 본문의 콘텐츠는 Video Analytics 모듈에서 정의됩니다.

## <a name="diagnostics-events"></a>진단 이벤트

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 이벤트 

라이브 파이프라인이 활성화되면 RTSP 원본 노드는 RTSP 시뮬레이터 컨테이너에서 실행되는 RTSP 서버에 연결을 시도합니다. 성공하면 이 이벤트가 출력됩니다.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [avasample-iot-edge-device/avaadge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzers.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-04-09T09:42:18.1280000Z"
  }
}
```


* 이 메시지는 진단 이벤트(MediaSessionEstablished)입니다. RTSP 원본 노드(주체)에서 RTSP 시뮬레이터와의 연결을 설정하고 라이브 피드(시뮬레이션된)를 받기 시작했음을 나타냅니다.
* applicationProperties의 subject 섹션은 메시지가 생성된 파이프라인 토폴로지의 노드를 참조합니다. 여기서는 메시지가 RTSP 원본 노드에서 시작됩니다.
* applicationProperties의 eventType 섹션은 진단 이벤트임을 나타냅니다.
* eventTime 섹션은 이벤트가 발생한 시간을 나타냅니다.
* body 섹션에는 진단 이벤트에 대한 데이터가 포함됩니다. 여기서는 [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 세부 정보입니다.

## <a name="operational-events"></a>작업 이벤트

파이프라인이 한동안 실행되면 objectCounter 모듈에서 이벤트를 받게 됩니다. 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [avasample-iot-edge-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime&quot;: &quot;2020-05-17T17:53:44.062Z"
  }
}
```

applicationProperties 섹션에는 이벤트 시간이 포함됩니다. 이는 objectCounter 모듈에서 yolov3 모듈의 결과에 관심 있는 개체(트럭)가 포함된 것을 관찰한 시간입니다.

비디오에서 다른 트럭이 감지됨에 따라 이러한 이벤트가 더 많이 표시될 수 있습니다.

### <a name="recordingstarted-event"></a>RecordingStarted 이벤트

개체 카운터에서 이벤트를 보낸 직후에 **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStarted** 유형의 이벤트가 표시됩니다.

```
[IoTHubMonitor] [5:53:46 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-evr-video"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStarted",
    "eventTime": "2021-04-09T09:42:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

applicationProperties의 subject 섹션은 이 메시지를 생성한 라이브 파이프라인의 비디오 싱크 노드를 참조합니다. body 섹션에는 출력 위치에 대한 정보가 포함됩니다. 여기서는 비디오가 녹화되는 Video Analyzer 비디오 리소스의 이름입니다.

### <a name="recordingavailable-event"></a>RecordingAvailable 이벤트

이름에서 알 수 있듯이, 녹화가 시작되면 RecordingStarted 이벤트가 전송되지만, 미디어 데이터는 아직 자산에 업로드되지 않았을 수 있습니다. 비디오 싱크 노드는 미디어가 업로드되면 **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable** 유형의 다음 이벤트를 내보냅니다.

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-evr-video"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable",
    "eventTime": "2021-04-09T09:43:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

이 이벤트는 플레이어 또는 클라이언트에서 비디오 재생을 시작하기에 충분한 데이터가 비디오 리소스에 기록되었음을 나타냅니다. applicationProperties의 subject 섹션은 이 메시지를 생성한 라이브 파이프라인의 비디오 싱크 노드를 참조합니다. body 섹션에는 출력 위치에 대한 정보가 포함됩니다. 여기서는 비디오가 녹화되는 Video Analyzer 리소스의 이름입니다.

### <a name="recordingstopped-event"></a>RecordingStopped 이벤트

라이브 파이프라인을 비활성화하면 비디오 싱크 노드가 미디어 녹화를 중지합니다. 그리고 **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStopped** 유형의 다음 이벤트를 내보냅니다.

```
[IoTHubMonitor] [11:33:31 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-evr-video"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStopped",
    "eventTime": "2021-04-10T11:33:31.051Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

이 이벤트는 기록이 중지되었음을 나타냅니다. applicationProperties의 subject 섹션은 이 메시지를 생성한 라이브 파이프라인의 비디오 싱크 노드를 참조합니다. body 섹션에는 출력 위치에 대한 정보가 포함됩니다. 여기서는 비디오가 녹화되는 Video Analyzer 리소스의 이름입니다.

## <a name="playing-back-the-recording"></a>녹화본 재생

Azure Portal에 로그인하여 비디오를 살펴보면 라이브 파이프라인에서 만든 Video Analyzer 비디오 리소스를 검사할 수 있습니다.
1. 웹 브라우저를 열고 [Azure Portal](https://portal.azure.com/)로 이동합니다. 자격 증명을 입력하여 포털에 로그인합니다. 기본 보기는 서비스 대시보드입니다.
1. 구독에 있는 리소스 중에서 Video Analyzer 계정을 찾아 계정 창을 엽니다.
1. **비디오 분석기** 목록에서 **비디오** 를 선택합니다.
1. 이름이 `sample-evr-video`인 비디오가 나열됩니다. 이 이름은 파이프라인 토폴로지 파일에서 선택한 이름입니다.
1. 비디오를 선택합니다.
1. 비디오 세부 정보 페이지가 열리고 재생이 자동으로 시작됩니다.

    <!--TODO: add image -- ![Video playback]() TODO: new screenshot is needed here -->

[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [clean-up-resources](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

* RTSP 시뮬레이터 대신 RTSP를 지원하는 [IP 카메라](https://en.wikipedia.org/wiki/IP_camera)를 사용합니다. [ONVIF 규격](https://www.onvif.org/conformant-products/) 제품 페이지에서 프로필 G, S 또는 T를 준수하는 디바이스를 찾아 RTSP를 지원하는 IP 카메라를 검색할 수 있습니다.
* Azure Linux VM 대신 AMD64 또는 X64 Linux 디바이스를 사용합니다. 이 디바이스는 IP 카메라와 동일한 네트워크에 있어야 합니다. [Linux에 Azure IoT Edge 런타임 설치](../../../iot-edge/how-to-install-iot-edge.md)의 지침을 따릅니다. 그런 다음, [가상 Linux 디바이스에 첫 번째 IoT Edge 모듈 배포](../../../iot-edge/quickstart-linux.md) 빠른 시작의 지침에 따라 디바이스를 Azure IoT Hub에 등록합니다.
