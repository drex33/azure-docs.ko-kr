---
title: Azure Video Analyzer를 사용하여 라이브 비디오의 개체 추적
description: 이 빠른 시작에서는 Azure Video Analyzer를 사용하여 (시뮬레이션된) IP 카메라의 라이브 비디오 피드에 있는 개체를 추적하는 방법을 보여줍니다. 컴퓨터 비전 모델을 적용하여 라이브 비디오 피드의 프레임에 있는 개체를 감지하는 방법을 알아봅니다. 그런 다음, 개체 추적기 노드를 사용하여 다른 프레임에서 해당 개체를 추적할 수 있습니다.
ms.topic: quickstart
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2b9286dc6dba80de841969af285ab57e587f560a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319266"
---
# <a name="quickstart-track-objects-in-a-live-video"></a>빠른 시작: 라이브 비디오의 개체 추적

[!INCLUDE [header](includes/edge-env.md)]

이 빠른 시작에서는 Azure Video Analyzer를 사용하여 (시뮬레이션된) IP 카메라의 라이브 비디오 피드에 있는 개체를 추적하는 방법을 보여줍니다. 컴퓨터 비전 모델을 적용하여 라이브 비디오 피드의 프레임에 있는 개체를 감지하는 방법을 알아봅니다. 그런 다음, 개체 추적기 노드를 사용하여 다른 프레임에서 해당 개체를 추적할 수 있습니다.

개체 추적기는 모든 프레임의 개체를 감지해야 하는 경우에 유용하지만, 에지 디바이스는 모든 프레임에 비전 모델을 적용하는 데 필요한 컴퓨팅 성능을 갖고 있지 않습니다. 라이브 비디오 피드가 초당 30프레임이고 컴퓨터 비전 모델을 15번째 프레임마다 실행할 수 있는 경우 개체 추적기는 이러한 프레임의 결과를 가져온 다음, [광학 흐름](https://en.wikipedia.org/wiki/Optical_flow) 기술을 사용하여 모델이 다음 프레임에서 다시 적용될 때까지 2번째, 3번째,..., 14번째 프레임에 대한 결과를 생성합니다.

이 빠른 시작에서는 Azure VM을 IoT Edge 디바이스로 사용하고, 시뮬레이션된 라이브 비디오 스트림을 사용합니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>Azure 리소스 설정

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>개요

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/track-objects-live-video/object-tracker-topology.svg" alt-text="라이브 비디오의 개체 추적":::

이 다이어그램에서는 이 빠른 시작의 신호 흐름을 보여 줍니다. [에지 모듈](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)은 RTSP(Real-Time Streaming Protocol) 서버를 호스팅하는 IP 카메라를 시뮬레이션합니다. [RTSP 원본](../pipeline.md#rtsp-source) 노드는 이 서버에서 비디오 피드를 가져와서 비디오 프레임을 [HTTP 확장 프로세서](../pipeline.md#http-extension-processor) 노드로 보냅니다.

HTTP 확장 노드는 프록시 역할을 수행합니다. 매 15번째 비디오 프레임을 지정된 이미지 형식으로 변환합니다. 그런 다음, HTTP를 통해 이미지를 HTTP 엔드포인트 내부에서 AI 모델을 실행하는 다른 에지 모듈에 릴레이합니다. 이 예제에서 해당 에지 모듈은 다양한 형식의 개체를 감지할 수 있는 [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3) 모델을 사용합니다. HTTP 확장 프로세서 노드는 감지 결과를 수신하고 이러한 결과와 모든 비디오 프레임(15번째 프레임뿐만 아니라)을 [개체 추적기](../pipeline.md#object-tracker-processor) 노드로 보냅니다. 개체 추적기 노드는 광학 흐름 기술을 사용하여 AI 모델이 적용되지 않은 14개 프레임의 개체를 추적합니다. 추적기 노드는 결과를 IoT Hub 메시지 싱크 노드에 게시합니다. 그러면 이 [IoT Hub 메시지 싱크](../pipeline.md#iot-hub-message-sink) 노드에서는 이러한 이벤트를 [IoT Edge Hub](../../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub)에 보냅니다.

> [!NOTE]
> [개체 추적기](../pipeline.md#object-tracker-processor) 노드를 사용할 때 정확도와 처리 능력 간의 득실에 대한 설명을 검토하세요.

이 빠른 시작에서는 다음을 수행합니다.

1. 개발 환경을 설정합니다.
1. 필요한 에지 모듈을 배포합니다.
1. 라이브 파이프라인을 만들고 배포합니다.
1. 결과를 해석합니다.
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
    * **yolov3** 모듈 - YOLOV3 모델을 사용하여 다양한 개체를 감지합니다.

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/track-objects-live-video/object-tracker-modules.png" alt-text= "List of deployed IoT Edge modules":::


## <a name="create-and-deploy-the-live-pipeline"></a>라이브 파이프라인 만들기 및 배포

### <a name="examine-and-edit-the-sample-files"></a>샘플 파일 검사 및 편집

Visual Studio Code에서 src/cloud-to-device-console-app 폴더로 이동합니다. 이 폴더에는 앞에서 만든 appsettings.json 파일과 몇 가지 다른 파일이 있습니다.

* **c2d-console-app.csproj**: Visual Studio Code의 프로젝트 파일입니다.
* **operations.json**: 이 파일에는 우리가 실행할 여러 작업이 나열됩니다.
* **Program.cs**: 다음과 같은 작업을 수행하는 샘플 프로그램 코드입니다.
    * 앱 설정을 로드합니다.
    * Video Analyzer 모듈이 공개하는 직접 메서드를 호출합니다. 이 모듈을 통해 [직접 메서드](direct-methods.md)를 호출하여 라이브 비디오 스트림을 분석할 수 있습니다.
    * **터미널** 창에서 프로그램의 출력을 검토하고, **출력** 창에서 모듈이 생성한 이벤트를 살펴볼 수 있도록 일시 중지합니다.
    * 리소스를 정리하는 직접 메서드를 호출합니다.

1. operations.json 파일을 편집합니다.
    
    * 파이프라인 토폴로지의 링크를 변경합니다.
    * "pipelineTopologyUrl": "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/object-tracking/topology.json"
    * livePipelineSet에서 이전 링크의 값과 일치하도록 파이프라인 토폴로지의 이름을 편집합니다.
    * "topologyName": "ObjectTrackingWithHttpExtension"
    * pipelineTopologyDelete에서 이름을 편집합니다.
    * "name": "ObjectTrackingWithHttpExtension"
    
브라우저에서 파이프라인 토폴로지의 URL을 열고 HTTP 확장 노드에 대한 설정을 검사합니다.

```
   "samplingOptions":{
       "skipSamplesWithoutAnnotation":"false",
       "maximumSamplesPerSecond":"2"
   }
```

여기서는 확장 노드가 추론 결과의 포함 여부에 관계 없이 모든 프레임을 다운스트림 개체 추적기 노드에 전달해야 하므로 `skipSamplesWithoutAnnotation`이 `false`로 설정되었습니다. 개체 추적기는 약 15프레임 넘게 개체를 추적할 수 있습니다. AI 모델에는 처리를 위한 최대 FPS가 있으며, 이는 `maximumSamplesPerSecond`에 설정해야 하는 가장 높은 값입니다.
    
## <a name="run-the-sample-program"></a>샘플 프로그램 실행

1. 디버깅 세션을 시작하려면 F5 키를 선택합니다. **터미널** 창에 메시지가 출력되어 표시됩니다.
1. operations.json 코드가 `pipelineTopologyList` 및 `livePipelineList` 직접 메서드를 호출하여 시작됩니다. 이전 빠른 시작이 완료된 후에 리소스를 정리한 경우 이 프로세스에서 빈 목록을 반환한 다음, 일시 중지합니다. 계속하려면 Enter 키를 선택합니다.
    
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
        "topologyName": "ObjectTrackingWithHttpExtension",
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
    * 라이브 파이프라인 및 비디오 흐름을 활성화하는 `livePipelineActivate`에 대한 호출
    * 라이브 파이프라인이 실행 중 상태임을 나타내는 `livePipelineList`에 대한 두 번째 호출입니다.
1. 터미널 창의 출력이 [계속하려면 Enter를 누르세요]라는 메시지에서 일시 중지됩니다. 아직 Enter 키를 누르지 마세요. 위로 스크롤하여 호출한 직접 메서드에 대한 JSON 응답 페이로드를 확인합니다.
1. Visual Studio Code에서 출력 창으로 전환합니다. Video Analytics Edge 모듈에서 IoT 허브에 보내는 메시지가 표시됩니다. 이 빠른 시작의 다음 섹션에서는 이러한 메시지를 설명합니다.
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

* 메시지는 진단 이벤트입니다. **MediaSessionEstablished** 는 RTSP 원본 노드(주체)에서 RTSP 시뮬레이터에 연결되었고 시뮬레이션된 라이브 피드를 받기 시작했음을 나타냅니다.
* applicationProperties에서 주체는 메시지가 라이브 파이프라인의 RTSP 원본 노드에서 생성되었음을 나타냅니다.
* applicationProperties에서 eventType은 이 이벤트가 진단 이벤트임을 나타냅니다.
* eventTime은 이벤트가 발생한 시간을 나타냅니다.
* 본문에는 진단 이벤트에 대한 데이터가 포함됩니다. 이 경우 데이터는 [SDP(세션 설명 프로토콜)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 세부 정보로 구성됩니다.

## <a name="object-tracking-events"></a>개체 추적 이벤트

HTTP 확장 프로세서 노드는 0번째, 15번째, 30번째, …번째 프레임을 yolov3 모듈에 보내고, 추론 결과를 받습니다. 그런 다음, 이러한 결과와 모든 비디오 프레임을 개체 추적기 노드로 보냅니다. 프레임 0에서 개체가 감지된 후 개체 추적기가 해당 개체에 고유한 `sequenceId`를 할당한다고 가정하겠습니다. 그리고 1, 2,..., 14프레임에서 개체 추적기가 해당 개체를 추적할 수 있는 경우 개체 추적기는 동일한 `sequenceId`를 사용하여 결과를 출력합니다. 결과의 다음 코드 조각에서 `sequenceId`가 반복되지만 개체가 이동하는 동안 경계 상자의 위치가 변경되었습니다.

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

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

* [라이브 비디오에서 개체가 가상의 선과 교차할 때 감지](use-line-crossing.md)해 보세요.
