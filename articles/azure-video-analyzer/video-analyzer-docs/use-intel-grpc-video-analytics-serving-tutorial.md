---
title: Intel OpenVINO™ DL Streamer - Edge AI Extension을 사용하여 gRPC를 통해 Azure Video Analyzer에서 라이브 비디오 분석
description: 이 자습서에는 Intel의 Intel OpenVINO™ DL Streamer – Edge AI Extension을 사용하여 (시뮬레이션된) IP 카메라의 라이브 비디오 피드를 분석하는 방법을 보여줍니다.
ms.topic: tutorial
ms.service: azure-video-analyzer
ms.date: 06/01/2021
ms.openlocfilehash: 664ef2be2c9349f01c95339be2842d85eaa22d0f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130244794"
---
# <a name="tutorial-analyze-live-video-with-intel-openvino-dl-streamer--edge-ai-extension"></a>자습서: Intel OpenVINO™ DL Streamer – Edge AI Extension을 사용하여 라이브 비디오 분석 

이 자습서에는 Intel의 Intel OpenVINO™ DL Streamer – Edge AI Extension을 사용하여 (시뮬레이션된) IP 카메라의 라이브 비디오 피드를 분석하는 방법을 보여줍니다. 이 유추 서버가 개체 감지(사람, 차량 또는 자전거) 및 개체 분류(차량 특성)를 위한 여러 가지 모델과 개체 추적(사람, 차량 및 자전거)을 위한 한 가지 모델에 대한 액세스를 제공하는 방법을 알아봅니다. gRPC 모듈과 통합하면 비디오 프레임을 AI 유추 서버로 보낼 수 있습니다. 그 결과는 IoT Edge Hub로 전송됩니다. Azure Video Analyzer와 동일한 컴퓨팅 노드에서 이 유추 서비스를 실행하면 공유 메모리를 통해 비디오 데이터를 보낼 수 있다는 장점이 있습니다. 이렇게 하면 라이브 비디오 피드의 프레임 속도로 유추를 실행할 수 있습니다(예: 30프레임/초). 

이 자습서에서는 Azure VM을 시뮬레이션된 IoT Edge 디바이스로 사용하고, 시뮬레이션된 라이브 비디오 스트림을 사용합니다. C#으로 작성된 샘플 코드 및 [동작 감지 및 이벤트 내보내기](detect-motion-emit-events-quickstart.md) 빠른 시작을 기반으로 합니다.

[!INCLUDE [use-x86-64](./includes/common-includes/use-x86-64.md)]

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>Azure 리소스 설정

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="review-the-sample-video"></a>샘플 비디오 검토

Azure 리소스가 설치되면 주차장의 짧은 비디오가 IoT Edge 디바이스로 사용하는 Azure의 Linux VM에 복사됩니다. 이 자습서에서는 비디오 파일을 사용하여 라이브 스트림을 시뮬레이션합니다.

[VLC 미디어 플레이어](https://www.videolan.org/vlc/)와 같은 애플리케이션을 엽니다. Ctrl+N을 선택한 다음, [비디오](https://lvamedia.blob.core.windows.net/public/lots_015.mkv)에 대한 링크를 붙여넣어 재생을 시작합니다. 주차장에 있는 차량 영상이 보입니다. 대부분은 주차되어 있고 한 대는 움직이고 있습니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

이 자습서에서는 Intel의 Intel OpenVINO™ DL Streamer – Edge AI Extension과 함께 Video Analyzer를 사용하여 차량 등의 물체를 감지하거나, 차량을 분류하거나, 차량, 사람, 자전거 등을 추적합니다. 결과에 따른 유추 이벤트를 IoT Edge Hub에 게시합니다.

## <a name="overview"></a>개요

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/grpc-vas-extension-with-vino.png" alt-text="Intel DL Streamer Edge AI 모듈을 사용하는 Azure Video Analyzer 파이프라인의 개요":::

이 다이어그램에서는 이 자습서의 신호 흐름을 보여 줍니다. [에지 모듈](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)은 RTSP(Real-Time Streaming Protocol) 서버를 호스팅하는 IP 카메라를 시뮬레이션합니다. [RTSP 원본](pipeline.md#rtsp-source) 노드는 이 서버에서 비디오 피드를 가져와서 비디오 프레임을 [gRPC 확장 프로세서](pipeline.md#grpc-extension-processor) 노드로 보냅니다. 

gRPC 확장 프로세서 노드는 디코딩된 비디오 프레임을 입력으로 가져오고, 이러한 프레임을 gRPC 서버에서 노출하는 [gRPC](terminology.md#grpc) 엔드포인트로 릴레이합니다. 이 노드는 [공유 메모리](https://en.wikipedia.org/wiki/Shared_memory)를 사용하여 데이터를 전송하거나 콘텐츠를 gRPC 메시지의 본문에 직접 포함할 수 있도록 지원합니다. 또한 이 노드에는 비디오 프레임을 gRPC 엔드포인트로 릴레이하기 전에 스케일링하고 인코딩하기 위한 기본 제공 이미지 포맷터가 포함되어 있습니다. 스케일러는 이미지 가로 세로 비율을 유지하거나 패딩하거나 늘일 수 있는 옵션을 제공합니다. 이미지 인코더는 jpeg, png 또는 bmp 형식을 지원합니다. [여기](pipeline.md#grpc-extension-processor)서 프로세서에 대해 자세히 알아보세요.

## <a name="about-intel-openvino-dl-streamer--edge-ai-extension-module"></a>Intel OpenVINO™ DL Streamer – Edge AI Extension 모듈 정보


OpenVINO™ DL Streamer - Edge AI Extension 모듈은 OpenVINO™ DL Streamer를 사용하여 만든 비디오 분석 파이프라인을 제공하는 Intel VA Serving(Video Analytics Serving) 기반의 마이크로서비스입니다. 개발자는 디코딩된 비디오 프레임을 AI 확장 모듈로 보낼 수 있습니다. 그러면 이 모듈에서는 개체를 검색, 분류 또는 추적하고 그 결과를 반환합니다. AI 확장 모듈은 Microsoft에서 제공하는 Azure Video Analyzer 같은 비디오 분석 플랫폼과 호환되는 gRPC API를 노출합니다.

복잡한 고성능 라이브 비디오 분석 솔루션을 구축하려면 Azure Video Analyzer 모듈이 에지에서 스케일링을 활용할 수 있는 강력한 추론 엔진과 쌍을 이뤄야 합니다. 이 자습서에서 추론 요청은 Azure Video Analyzer와 함께 작동하도록 설계된 Edge 모듈인 Intel OpenVINO™ DL Streamer – Edge AI Extension으로 전송됩니다.

이 유추 서버의 초기 릴리스에서는 다음 [모델](https://github.com/intel/video-analytics-serving/tree/master/samples/ava_ai_extension#edge-ai-extension-module-options)에 액세스할 수 있습니다.

- object_detection for person_vehicle_bike_detection ![차량 개체 감지](./media/use-intel-openvino-tutorial/object-detection.png)

- object_classification for vehicle_attributes_recognition ![차량 개체 분류](./media/use-intel-openvino-tutorial/object-classification.png)

- object_tracking for person_vehicle_bike_tracking ![사람 차량 개체 추적](./media/use-intel-openvino-tutorial/object-tracking.png)

미리 로드된 개체 감지, 개체 분류 및 개체 추적 파이프라인을 사용하여 빠르게 시작합니다. 뿐만 아니라 미리 로드된 [person-vehicle-bike-detection-crossroad-0078](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/person-vehicle-bike-detection-crossroad-0078/README.md) 및 [vehicle-attributes-recognition-barrier-0039 모델](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/vehicle-attributes-recognition-barrier-0039/README.md)이 함께 제공됩니다.

> [!NOTE]
> Intel에서 제공하는 Edge 모듈인 OpenVINO™ DL Streamer – Edge AI Extension과 그 안에 포함된 소프트웨어를 다운로드하고 사용하는 것은 [사용권 계약](https://www.intel.com/content/www/us/en/legal/terms-of-use.html)의 사용 약관에 동의하는 것입니다.
> Intel은 인권을 존중하며 인권 남용 공모를 방지하기 위해 최선을 다하고 있습니다. [Intel의 글로벌 인권 원칙](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html)을 참조하세요. Intel의 제품 및 소프트웨어는 국제적으로 인정되는 인권 침해를 유발하거나 인권 침해에 기여하지 않는 애플리케이션에서만 사용해야 합니다.

본인의 사용 사례에 맞게 배포 템플릿의 파이프라인 환경 변수를 간단하게 변경하여 파이프라인을 유연하게 사용할 수 있습니다. 따라서 파이프라인 모델을 신속하게 변경할 수 있으며, Azure Video Analyzer와 결합하면 미디어 파이프라인과 추론 모델을 불과 몇 초 만에 변경할 수 있습니다.

이 자습서에서는 다음을 수행합니다.

1. 개발 환경을 설정합니다.
1. 필요한 에지 모듈을 배포합니다.
1. 라이브 파이프라인을 만들고 배포합니다.
1. 결과를 해석합니다.
1. 리소스를 정리합니다.

## <a name="set-up-your-development-environment"></a>개발 환경 설정
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

## <a name="deploy-the-required-modules"></a>필요한 모듈 배포

1. Visual Studio Code에서 *src/edge/deployment.openvino.grpc.template.json* 파일을 마우스 오른쪽 단추로 클릭한 다음, **IoT Edge 배포 매니페스트 생성** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/analyze-live-video-use-your-model-http/generate-deployment-manifest.png" alt-text="IoT Edge 배포 매니페스트 생성":::
1. *deployment.openvino.grpc.amd64.json* 매니페스트 파일은 *src/edge/config* 폴더에 생성됩니다.
1. 마우스 오른쪽 단추로 *src/edge/config/deployment.openvino.grpc.amd64.json* 파일을 클릭하고 **단일 디바이스용 배포 만들기** 를 클릭합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/analyze-live-video-use-your-model-http/deployment-single-device.png" alt-text= "Create Deployment for a Single Device":::
1. IoT Hub 디바이스를 선택하라는 메시지가 표시되면 **avasample-iot-edge-device** 를 선택합니다.
1. 약 30초 후 창의 왼쪽 아래 모서리에서 Azure IoT Hub를 새로 고칩니다. 이제 에지 디바이스에 다음과 같은 배포된 모듈이 표시됩니다.

    * **avaedge** 라는 Video Analyzer 에지 모듈.
    * **rtspsim** 모듈 - RTSP 서버를 시뮬레이션하고 라이브 비디오 피드의 원본 역할을 합니다. 
    * **avaextension** 모듈 - 개체 감지, 분류 및 모델 추적 기능이 있는 Intel OpenVINO DL Streamer 모듈로, 컴퓨터 비전을 이미지에 적용하고 여러 개체 유형의 클래스를 반환합니다.

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/vscode-common-screenshots/avaextension.png" alt-text= "OpenVINO object detection model":::

> [!NOTE]
> 디바이스에서 사용할 수 있는 경우 Intel OpenVINO DL Streamer - Edge AI Extension 모듈에서 CPU, VPU 및 GPU를 지원할 수 있는 *deployment.openvino.grpc.xpu.template.json* 템플릿도 포함되어 있습니다. 이 템플릿은 Intel의 Docker 허브 이미지를 가리킵니다.

### <a name="prepare-to-monitor-events"></a>이벤트 모니터링 준비

Azure Video Analyzer 디바이스를 마우스 오른쪽 단추로 클릭하고, **기본 제공 이벤트 엔드포인트 모니터링 시작** 을 선택합니다. 이 단계는 Visual Studio Code의 **출력** 창에서 IoT Hub 이벤트를 모니터링하는 데 필요합니다.

![모니터링 시작](./media/quickstarts/start-monitoring-iot-hub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles-persons-or-bike"></a>차량, 사람 또는 자전거를 감지하는 샘플 프로그램 실행
브라우저에서 이 자습서의 [파이프라인 토폴로지](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json)를 열면 `grpcExtensionAddress`의 값이 `tcp://avaExtension:5001`로 설정된 것을 볼 수 있습니다. *httpExtensionOpenVINO* 자습서와 달리 url을 gRPC 서버로 변경할 필요가 없습니다. 대신 작업 파일에서 `extensionConfiguration`을 사용하여 특정 파이프라인을 실행하도록 모듈에 지시합니다. 값을 제공하지 않으면 "person_vehicle_bike_detection"은 기본적으로 "object_detection"으로 설정됩니다. 지원되는 다른 파이프라인을 실험해 볼 수 있습니다.

1. *operations.json* 파일을 편집합니다.
    * 라이브 파이프라인 토폴로지에 대한 링크를 변경합니다.

        `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json"`

    * `pipelineTopologySet` 아래에서 이전 링크의 값과 일치하도록 라이브 파이프라인 토폴로지의 이름을 편집합니다.

      `"topologyName" : "InferencingWithOpenVINOgRPC"`

    * `pipelineTopologyDelete` 아래에서 이름을 편집합니다.

      `"name": "InferencingWithOpenVINOgRPC"`
    
    * `rtspUrl` 아래에서 url을 `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`로 편집합니다.

rtsp 매개변수 뒤에 다음 extensionConfiguration을 추가합니다.

```
{
    "name": "extensionConfiguration",
    "value": "{\"pipeline\":{\"name\":\"object_detection\",\"version\":\"person_vehicle_bike_detection\"}}"
}
```


`operations.json`은 다음과 같습니다.
```
{
  "apiVersion": "1.0",
  "operations": [
      {
          "opName": "pipelineTopologyList",
          "opParams": {}
      },
      {
          "opName": "WaitForInput",
          "opParams": {
              "message": "Press Enter to continue."
          }
      },
      {
          "opName": "livePipelineList",
          "opParams": {}
      },
      {
          "opName": "WaitForInput",
          "opParams": {
              "message": "Press Enter to continue. This is start the activating steps."
          }
      },
      {
          "opName": "pipelineTopologySet",
          "opParams": {
              "pipelineTopologyUrl": "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json"
          }
      },
      {
          "opName": "livePipelineSet",
          "opParams": {
              "name": "Sample-Pipeline-1",
              "properties": {
                  "topologyName": "InferencingWithOpenVINOgRPC",
                  "description": "Sample pipeline description",
                  "parameters": [
                      {
                          "name": "rtspUrl",
                          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
                      },
                      {
                          "name": "rtspUserName",
                          "value": "testuser"
                      },
                      {
                          "name": "rtspPassword",
                          "value": "testpassword"
                      },
                      {
                        "name": "extensionConfiguration",
                        "value": "{\"pipeline\":{\"name\":\"object_detection\",\"version\":\"person_vehicle_bike_detection\"}}"
                      }
                  ]
              }
          }
      },
      {
          "opName": "livePipelineActivate",
          "opParams": {
              "name": "Sample-Pipeline-1"
          }
      },
      {
          "opName": "WaitForInput",
          "opParams": {
              "message": "Press Enter to continue. This is start the deactivating steps."
          }
      },
      {
          "opName": "livePipelineDeactivate",
          "opParams": {
              "name": "Sample-Pipeline-1"
          }
      },
      {
          "opName": "livePipelineDelete",
          "opParams": {
              "name": "Sample-Pipeline-1"
          }
      },
      {
          "opName": "livePipelineList",
          "opParams": {}
      },
      {
          "opName": "pipelineTopologyDelete",
          "opParams": {
              "name": "InferencingWithOpenVINOgRPC"
          }
      },
      {
          "opName": "pipelineTopologyList",
          "opParams": {}
      }
  ]
}
```

`extensionConfiguration`을 사용하면 데이터 문자열을 Intel DL Streamer - Edge AI 모듈에 전달할 수 있습니다. 올바른 데이터가 있으면 각 파이프라인에 특정 모델을 사용하도록 Edge AI 모듈에 지시할 수 있습니다. 예를 들어 위에서 사용된 "object_detection" 모델을 분류, 추적 등의 지원되는 다른 모델로 바꿀 수 있습니다. 이렇게 하려면 아래 예제를 참조하세요.

분류:
```
{
    "name": "extensionConfiguration",
    "value": "{\"pipeline\":{\"name\":\"object_classification\",\"version\":\"vehicle_attributes_recognition\"}}"
}
```

추적:
```
{
    "name": "extensionConfiguration",
    "value": "{\"pipeline\":{\"name\":\"object_tracking\",\"version\":\"person_vehicle_bike_tracking\"}}"
}
```

1. Visual Studio Code에서 **확장** 탭을 열고(또는 Ctrl+Shift+X를 누름) Azure IoT Hub를 검색합니다.
1. 마우스 오른쪽 단추를 클릭하고 **확장 설정** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="확장 설정":::
1. "자세한 정보 메시지 표시"를 검색하고 활성화합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="자세한 정보 메시지 표시":::
1. 디버깅 세션을 시작하려면 F5 키를 선택합니다. **터미널** 창에 메시지가 출력되어 표시됩니다.
1. *operations.json* 코드가 `pipelineTopologyList` 및 `livePipelineList` 직접 메서드를 호출하여 시작됩니다. 이전 빠른 시작/자습서를 마치고 리소스를 정리한 경우 이 프로세스는 빈 목록을 반환하고 일시 중지됩니다. 계속하려면 Enter 키를 선택합니다.

    **터미널** 창에 직접 메서드 호출의 다음 세트가 표시됩니다.

     * 이전 `topologyUrl`을 사용하는 `pipelineTopologySet`에 대한 호출
     * 다음 본문을 사용하는 `livePipelineSet`에 대한 호출

         ```
         {
           "@apiVersion": "1.0",
           "name": "Sample-Pipeline-1",
           "properties": {
             "topologyName": "InferencingWithGrpcExtension",
             "description": "Sample pipeline description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/lots_015.mkv"
               },
               {
                 "name": "rtspUserName",
                 "value": "testuser"
               },
               {
                 "name": "rtspPassword",
                 "value": "testpassword"
               },
               {
                 "name": "grpcExtensionAddress",
                 "value": "tcp://avaextension:5001"
               },
               {
                 "name": "extensionConfiguration",
                 "value": "{\"pipeline\":{\"name\":\"object_detection\",\"version\":\"person_vehicle_bike_detection\"}}"
               }
             ]
           }
         }
         ```

     * 파이프라인 및 비디오 흐름을 시작하는 `livePipelineActivate`에 대한 호출
1. **터미널** 창의 출력이 `Press Enter to continue` 프롬프트에서 일시 중지됩니다. 아직 Enter 키를 선택하지 마세요. 위로 스크롤하여 호출한 직접 메서드에 대한 JSON 응답 페이로드를 확인합니다.
1. Visual Studio Code에서 **출력** 창으로 전환합니다. Video Analyzer 모듈에서 IoT 허브에 보내는 메시지가 표시됩니다. 이 자습서의 다음 섹션에서는 이러한 메시지를 설명합니다.
1. 라이브 파이프라인은 계속 실행되고 결과를 출력합니다. RTSP 시뮬레이터가 원본 비디오를 계속 반복합니다. 라이브 파이프라인을 중지하려면 **TERMINAL** 창으로 돌아가서 Enter를 선택합니다. 

    일련의 다음 호출은 리소스를 정리합니다.
      * `livePipelineDeactivate`를 호출하면 파이프라인이 비활성화됩니다.
      * `livePipelineDelete`를 호출하면 파이프라인이 삭제됩니다.
      * `pipelineTopologyDelete`에 대한 호출은 토폴로지를 삭제합니다.
      * `pipelineTopologyList`에 대한 최종 호출은 목록이 비어 있음을 보여 줍니다.

## <a name="interpret-results"></a>결과 해석

라이브 파이프라인을 실행하면 gRPC 확장 프로세서 노드의 결과가 IoT Hub 메시지 싱크 노드를 통해 IoT 허브에 전송됩니다. **OUTPUT** 창에 표시되는 메시지에는 `body` 섹션이 포함되어 있습니다. 자세한 내용은 [IoT Hub 메시지 만들기 및 읽기](../../iot-hub/iot-hub-devguide-messages-construct.md)를 참조하세요.

다음 메시지에서 Video Analyzer 모듈은 본문의 콘텐츠를 정의합니다. 

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 이벤트

라이브 파이프라인이 활성화되면 RTSP 원본 노드는 rtspsim-live555 컨테이너에서 실행되는 RTSP 서버에 연결을 시도합니다. 연결에 성공하면 다음 이벤트가 출력됩니다.

```
[IoTHubMonitor] [10:51:34 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620204694595500 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
}
```

앞의 출력에서, 

* 메시지는 진단 이벤트 `MediaSessionEstablished`입니다. RTSP 원본 노드(주체)에서 RTSP 시뮬레이터에 연결되었고 라이브 피드(시뮬레이션된)를 받기 시작했음을 나타냅니다.
* `sdp` 섹션에는 진단 이벤트에 대한 데이터가 포함됩니다. 이 경우 데이터는 [SDP(세션 설명 프로토콜)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 세부 정보로 구성됩니다.

### <a name="inference-event"></a>유추 이벤트

gRPC 확장 프로세서 노드는 Intel OpenVINO™ DL Streamer – Edge AI Extension에서 유추 결과를 받습니다. 그런 다음, IoT Hub 메시지 싱크 노드를 통해 결과를 유추 이벤트로 내보냅니다. 

이러한 이벤트에서 유형은 자동차 또는 트럭과 같은 엔터티임을 나타내기 위해 `entity`로 설정됩니다. 

다음 예제에서는 차량 한 대가 감지되었으며, 신뢰도 값은 0.9를 초과합니다.

```
[IoTHubMonitor] [3:10:23 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "timestamp": 145819820073974,
  "inferences": [
    {
      "type": "entity",
      "subtype": "vehicleDetection",
      "entity": {
        "tag": {
          "value": "vehicle",
          "confidence": 0.9147264
        },
        "box": {
          "l": 0.6853116,
          "t": 0.5035262,
          "w": 0.04322505,
          "h": 0.03426218
        }
      }
    }
```

메시지에서 다음 세부 정보를 확인합니다.

* `body` 섹션에는 분석 이벤트에 대한 데이터가 포함됩니다. 이 경우 이벤트는 유추 이벤트이므로 본문에 `inferences` 데이터가 포함됩니다.
* `inferences` 섹션은 `type`이 `entity`임을 나타냅니다. 이 섹션에는 엔터티에 대한 추가 데이터가 포함됩니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 빠른 시작이나 자습서를 사용하려면 여기서 만든 리소스를 그대로 유지하세요. 그렇지 않으면 Azure Portal, 리소스 그룹으로 차례로 이동하고, 이 자습서를 실행한 리소스 그룹을 선택한 다음, 모든 리소스를 삭제하세요.

## <a name="next-steps"></a>다음 단계

고급 사용자에 대한 추가 문제를 검토합니다.

* RTSP 시뮬레이터를 사용하는 대신 RTSP를 지원하는 [IP 카메라](https://en.wikipedia.org/wiki/IP_camera)를 사용합니다. [ONVIF 규격](https://www.onvif.org/conformant-products/) 제품 페이지에서 RTSP를 지원하는 IP 카메라를 검색할 수 있습니다. G, S 또는 T 프로필을 준수하는 디바이스를 찾습니다.
* Azure Linux VM 대신 Intel x64 Linux 디바이스를 사용합니다. 이 디바이스는 IP 카메라와 동일한 네트워크에 있어야 합니다. [Linux에 Azure IoT Edge 런타임 설치](../../iot-edge/how-to-provision-single-device-linux-symmetric.md)의 지침을 따릅니다. 그런 다음, [가상 Linux 디바이스에 첫 번째 IoT Edge 모듈 배포](../../iot-edge/quickstart-linux.md)의 지침에 따라 디바이스를 Azure IoT Hub에 등록합니다.
