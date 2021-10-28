---
title: gRPC를 통해 Intel OpenVINO™ DL Streamer – Edge AI Extension과 함께 Live Video Analytics를 사용하여 라이브 비디오 분석
description: 이 자습서에는 Intel의 Intel OpenVINO™ DL Streamer – Edge AI Extension과 함께 Live Video Analytics를 사용하여 시뮬레이션된 IP 카메라에서 라이브 비디오 피드를 분석하는 방법을 보여줍니다.
ms.topic: tutorial
ms.date: 02/04/2021
ms.service: media-services
ms.author: faneerde
author: fvneerden
ms.openlocfilehash: 0eddbed98a4110e1d5e2f832690c7b87850111b0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254304"
---
# <a name="tutorial-analyze-live-video-by-using-live-video-analytics-with-intel-openvino-dl-streamer--edge-ai-extension"></a>자습서: Intel OpenVINO™ DL Streamer – Edge AI Extension과 함께 Live Video Analytics를 사용하여 라이브 비디오 분석 

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

이 자습서에는 Intel의 Intel OpenVINO™ DL Streamer – Edge AI Extension을 사용하여 (시뮬레이션된) IP 카메라의 라이브 비디오 피드를 분석하는 방법을 보여줍니다. 이 유추 서버가 개체 감지(사람, 차량 또는 자전거) 및 개체 분류(차량 특성)를 위한 여러 가지 모델과 개체 추적(사람, 차량 및 자전거)을 위한 한 가지 모델에 대한 액세스를 제공하는 방법을 알아봅니다. gRPC 모듈과 통합하면 비디오 프레임을 AI 유추 서버로 보낼 수 있습니다. 그 결과는 IoT Edge Hub로 전송됩니다. Live Video Analytics와 동일한 컴퓨팅 노드에서 이 유추 서비스를 실행하면 공유 메모리를 통해 비디오 데이터를 보낼 수 있다는 장점이 있습니다. 이렇게 하면 라이브 비디오 피드의 프레임 속도로 유추를 실행할 수 있습니다(예: 초당 30프레임). 

이 자습서에서는 Azure VM을 IoT Edge 디바이스로 사용하고, 시뮬레이션된 라이브 비디오 스트림을 사용합니다. C#으로 작성된 샘플 코드 및 [동작 감지 및 이벤트 내보내기](detect-motion-emit-events-quickstart.md) 빠른 시작을 기반으로 합니다.

> [!NOTE]
> 이 자습서를 사용하려면 에지 디바이스로 x86-64 컴퓨터를 사용해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 수 있습니다.
  > [!NOTE]
  > 서비스 주체를 만들 수 있는 권한이 있는 Azure 구독이 필요합니다(**소유자 역할** 이 이를 제공함). 적절한 권한이 없는 경우 계정 관리자에게 문의하여 적절한 권한을 부여하세요. 
* 다음 확장이 포함된 [Visual Studio Code](https://code.visualstudio.com/)
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [동작 감지 및 이벤트 내보내기](detect-motion-emit-events-quickstart.md) 빠른 시작을 완료하지 않은 경우 [Azure 리소스](detect-motion-emit-events-quickstart.md#set-up-azure-resources)를 설정하는 단계를 완료해야 합니다.

> [!TIP]
> Azure IoT Tools를 설치하는 경우 Docker를 설치하라는 메시지가 표시될 수 있습니다. 이 메시지는 무시할 수 있습니다.

## <a name="review-the-sample-video"></a>샘플 비디오 검토

Azure 리소스가 설치되면 주차장의 짧은 비디오가 IoT Edge 디바이스로 사용하는 Azure의 Linux VM에 복사됩니다. 이 빠른 시작에서는 비디오 파일을 사용하여 라이브 스트림을 시뮬레이션합니다.

[VLC 미디어 플레이어](https://www.videolan.org/vlc/)와 같은 애플리케이션을 엽니다. Ctrl+N을 선택한 다음, [비디오](https://lvamedia.blob.core.windows.net/public/lots_015.mkv)에 대한 링크를 붙여넣어 재생을 시작합니다. 주차장에 있는 차량 영상이 보입니다. 대부분은 주차되어 있고 한 대는 움직이고 있습니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

이 빠른 시작에서는 Intel의 Intel OpenVINO™ DL Streamer – Edge AI Extension과 함께 IoT Edge의 Live Video Analytics를 사용하여 차량 등의 물체를 감지하거나, 차량을 분류하거나, 차량, 사람, 자전거 등을 추적합니다. 결과에 따른 유추 이벤트를 IoT Edge Hub에 게시합니다.

## <a name="overview"></a>개요

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/grpc-vas-extension-with-vino.svg" alt-text="LVA MediaGraph 개요":::

이 다이어그램에서는 이 빠른 시작의 신호 흐름을 보여 줍니다. [Edge 모듈](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)은 RTSP(Real-Time Streaming Protocol) 서버를 호스팅하는 IP 카메라를 시뮬레이션합니다. [RTSP 원본](media-graph-concept.md#rtsp-source) 노드는 이 서버에서 비디오 피드를 가져와서 비디오 프레임을 [gRPC 확장 프로세서](media-graph-concept.md#grpc-extension-processor) 노드로 보냅니다. 

gRPC 확장 프로세서 노드는 디코딩된 비디오 프레임을 입력으로 가져오고, 이러한 프레임을 gRPC 서버에서 노출하는 [gRPC](terminology.md#grpc) 엔드포인트로 릴레이합니다. 이 노드는 [공유 메모리](https://en.wikipedia.org/wiki/Shared_memory)를 사용하여 데이터를 전송하거나 콘텐츠를 gRPC 메시지의 본문에 직접 포함할 수 있도록 지원합니다. 또한 이 노드에는 비디오 프레임을 gRPC 엔드포인트로 릴레이하기 전에 스케일링하고 인코딩하기 위한 기본 제공 이미지 포맷터가 포함되어 있습니다. 스케일러는 이미지 가로 세로 비율을 유지하거나 패딩하거나 늘일 수 있는 옵션을 제공합니다. 이미지 인코더는 jpeg, png 또는 bmp 형식을 지원합니다. [여기](media-graph-extension-concept.md#grpc-extension-processor)서 프로세서에 대해 자세히 알아보세요.

이 자습서에서는 다음을 수행합니다.

1. 미디어 그래프를 배포합니다.
1. 결과를 해석합니다.
1. 리소스를 정리합니다.

## <a name="about-intel-openvino-dl-streamer--edge-ai-extension-module"></a>Intel OpenVINO™ DL Streamer – Edge AI Extension 모듈 정보


OpenVINO™ DL Streamer - Edge AI Extension 모듈은 OpenVINO™ DL Streamer를 사용하여 만든 비디오 분석 파이프라인을 제공하는 Intel VA Serving(Video Analytics Serving) 기반의 마이크로서비스입니다. 개발자는 디코딩된 비디오 프레임을 AI 확장 모듈로 보낼 수 있습니다. 그러면 이 모듈에서는 개체를 검색, 분류 또는 추적하고 그 결과를 반환합니다. AI 확장 모듈은 Microsoft에서 제공하는 IoT Edge의 Live Video Analytics 같은 비디오 분석 플랫폼과 호환되는 gRPC API를 노출합니다. 

복잡한 고성능 라이브 비디오 분석 솔루션을 구축하려면 IoT Edge 모듈의 Live Video Analytics가 에지에서 규모를 활용할 수 있는 강력한 유추 엔진과 쌍을 이뤄야 합니다. 이 자습서에서 유추 요청은 IoT Edge의 Live Video Analytics와 함께 작동하도록 설계된 Edge 모듈인 [Intel OpenVINO™ DL Streamer – Edge AI Extension]()으로 전송됩니다.

이 유추 서버의 초기 릴리스에서는 다음 [모델](https://github.com/intel/video-analytics-serving/tree/master/samples/ava_ai_extension#edge-ai-extension-module-options)에 액세스할 수 있습니다.

- object_detection for person_vehicle_bike_detection ![차량 개체 감지](./media/use-intel-openvino-tutorial/object-detection.png)

- object_classification for vehicle_attributes_recognition ![차량 개체 분류](./media/use-intel-openvino-tutorial/object-classification.png)

- object_tracking for person_vehicle_bike_tracking ![사람 차량 개체 추적](./media/use-intel-openvino-tutorial/object-tracking.png)

미리 로드된 개체 감지, 개체 분류 및 개체 추적 파이프라인을 사용하여 빠르게 시작합니다. 뿐만 아니라 미리 로드된 [person-vehicle-bike-detection-crossroad-0078](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/person-vehicle-bike-detection-crossroad-0078/README.md) 및 [vehicle-attributes-recognition-barrier-0039 모델](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/vehicle-attributes-recognition-barrier-0039/README.md)이 함께 제공됩니다.

> [!NOTE]
> Intel에서 제공하는 Edge 모듈인 OpenVINO™ DL Streamer – Edge AI Extension과 그 안에 포함된 소프트웨어를 다운로드하고 사용하는 것은 [사용권 계약](https://www.intel.com/content/www/us/en/legal/terms-of-use.html)의 사용 약관에 동의하는 것입니다.
> Intel은 인권을 존중하며 인권 남용 공모를 방지하기 위해 최선을 다하고 있습니다. [Intel의 글로벌 인권 원칙](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html)을 참조하세요. Intel의 제품 및 소프트웨어는 국제적으로 인정되는 인권 침해를 유발하거나 인권 침해에 기여하지 않는 애플리케이션에서만 사용해야 합니다.

본인의 사용 사례에 맞게 배포 템플릿의 파이프라인 환경 변수를 간단하게 변경하여 파이프라인을 유연하게 사용할 수 있습니다. 따라서 파이프라인 모델을 신속하게 변경할 수 있으며, Live Video Analytics와 결합하면 미디어 파이프라인과 유추 모델을 불과 몇 초 만에 변경할 수 있습니다.  

## <a name="create-and-deploy-the-media-graph"></a>미디어 그래프를 만들고 배포

### <a name="examine-and-edit-the-sample-files"></a>샘플 파일 검사 및 편집

필수 구성 요소의 일부로 샘플 코드를 폴더에 다운로드했습니다. 다음 단계에 따라 샘플 파일을 검사하고 편집합니다.

1. Visual Studio Code에서 *src/edge* 로 이동합니다. *env* 파일 및 몇 가지 배포 템플릿 파일이 표시됩니다.

    배포 템플릿은 에지 디바이스에 대한 배포 매니페스트를 나타냅니다. 일부 자리 표시자 값을 포함합니다. *.env* 파일에는 이러한 변수에 대한 값이 포함되어 있습니다.

1. *src/cloud-to-device-console-app* 폴더로 이동합니다. 여기에는 *appsettings.json* 파일 및 몇 가지 다른 파일이 표시됩니다.

    * ***c2d-console-app.csproj*** - Visual Studio Code에 대한 프로젝트 파일
    * ***operations.json*** - 프로그램을 실행하려는 작업의 목록입니다.
    * ***Program.cs*** - 샘플 프로그램 코드입니다. 이 코드에서는 다음을 수행합니다.

        * 앱 설정을 로드합니다.
        * Live Video Analytics on IoT Edge 모듈에서 공개하는 직접 메서드를 호출합니다. 모듈을 사용하여 해당 [직접 메서드](direct-methods.md)를 호출하여 라이브 비디오 스트림을 분석할 수 있습니다.
        * **터미널** 창에서 프로그램의 출력을 검사하고, **출력** 창에서 모듈에서 생성된 이벤트를 검사할 수 있도록 일시 중지합니다.
        * 리소스를 정리하는 직접 메서드를 호출합니다.


1. *operations.json* 파일을 편집합니다.
    * 그래프 토폴로지의 링크를 변경합니다.

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json"`

    * `GraphInstanceSet` 아래에서 이전 링크의 값과 일치하도록 그래프 토폴로지의 이름을 편집합니다.

      `"topologyName" : "InferencingWithOpenVINOgRPC"`

    * `GraphTopologyDelete` 아래에서 이름을 편집합니다.

      `"name": "InferencingWithOpenVINOgRPC"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge 배포 매니페스트 생성 및 배포

1. *src/edge/deployment.openvino.grpc.cpu.template.json* 파일을 마우스 오른쪽 단추로 클릭한 다음, **IoT Edge 배포 매니페스트 생성** 을 클릭합니다.

    ![IoT Edge 배포 매니페스트 생성](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    *deployment.openvino.grpc.cpu.amd64.json* 매니페스트 파일은 *src/edge/config* 폴더에 생성됩니다.

> [!NOTE]
> Intel OpenVINO DL Streamer - Edge AI Extension 모듈에서 GPU를 지원하게 만들어 주는 *deployment.openvino.grpc.gpu.template.json* 템플릿도 포함되어 있습니다. 이 템플릿은 Intel의 Docker 허브 이미지를 가리킵니다.

위에서 언급한 템플릿은 Intel Docker 허브 이미지를 가리킵니다. 사용자 고유의 Azure Container Registry에 복사본을 호스트하려면 아래의 1단계와 2단계를 수행하면 됩니다.
1. docker CLI 도구가 설치된 디바이스(즉, 에지 디바이스)에 SSH를 수행하고 다음 단계에 따라 컨테이너를 끌어옵니다/태그를 지정합니다/푸시합니다.
    * Docker 허브에서 Intel의 이미지를 끌어옵니다.

        `sudo docker pull intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`
    
    * 사용자 고유의 Azure Container Registry 이름을 Intel 이미지의 태그로 지정합니다. {YOUR ACR NAME}을 .env 파일에서 찾을 수 있는 ACR 이름으로 바꿉니다.

        `sudo docker image tag intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
    * 태그가 지정된 이미지를 Azure Container Registry로 푸시합니다.

        `sudo docker push {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
2. 이제 Azure Container Registry에 호스트되는 새 이미지를 참조하도록 템플릿을 편집해야 합니다.
    * *deployment.openvino.grpc.cpu.template.json* 을 마우스 오른쪽 단추로 클릭하고, *lavExtension* 모듈 부분으로 이동하여 아래 항목을

        `intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`

        다음으로 바꿉니다.

        `{YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    * *deployment.openvino.grpc.gpu.template.json* 에 대해 2단계를 반복합니다.


3. [동작 감지 및 이벤트 내보내기](detect-motion-emit-events-quickstart.md) 빠른 시작을 완료한 경우 이 단계를 건너뜁니다. 

    그렇지 않으면 왼쪽 아래 모서리의 **Azure IoT Hub** 창 근처에서 **기타 작업** 아이콘을 선택한 다음, **IoT Hub 연결 문자열 설정** 을 선택합니다. *appsettings.json* 파일에서 문자열을 복사할 수 있습니다. 또는 Visual Studio Code 내에서 적절한 IoT 허브를 구성했는지 확인하려면 [IoT 허브 선택 명령](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)을 사용합니다.
    
    ![IoT Hub 연결 문자열 설정](./media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> IoT Hub에 대한 기본 제공 엔드포인트 정보를 제공하라는 메시지가 표시될 수 있습니다. 해당 정보를 가져오려면 Azure Portal에서 IoT Hub로 이동하여 왼쪽 탐색 창에서 **기본 제공 엔드포인트** 옵션을 찾습니다. 여기를 클릭하고 **Event Hub 호환 엔드포인트** 섹션에서 **Event Hub 호환 엔드포인트** 를 찾습니다. 상자의 텍스트를 복사하여 사용합니다. 엔드포인트는 다음과 같이 표시됩니다.  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. *src/edge/config/deployment.openvino.grpc.cpu.template.json* 파일을 마우스 오른쪽 단추로 클릭하고 **단일 디바이스용 배포 만들기** 를 선택합니다. 

    ![단일 디바이스용 배포 만들기](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. IoT Hub 디바이스를 선택하라는 메시지가 표시되면 **lva-sample-device** 를 선택합니다.
1. 약 30초 후 창의 왼쪽 아래 모서리에서 Azure IoT Hub를 새로 고칩니다. 이제 에지 디바이스에 다음과 같은 배포된 모듈이 표시됩니다.

    * **lvaEdge** 라는 이름의 Live Video Analytics 모듈
    * **rtspsim** 모듈 - RTSP 서버를 시뮬레이션하고 라이브 비디오 피드의 원본으로 작동합니다.
    * **lvaExtension** 모듈 - Intel OpenVINO™ DL Streamer – Edge AI Extension 

### <a name="prepare-to-monitor-events"></a>이벤트 모니터링 준비

마우스 오른쪽 단추로 Live Video Analytics 디바이스를 클릭하고, **기본 제공 이벤트 엔드포인트 모니터링 시작** 을 선택합니다. 이 단계는 Visual Studio Code의 **출력** 창에서 IoT Hub 이벤트를 모니터링하는 데 필요합니다. 

![모니터링 시작](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles-persons-or-bike"></a>차량, 사람 또는 자전거를 감지하는 샘플 프로그램 실행
브라우저에서 이 자습서의 [그래프 토폴로지](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json)를 열면 `grpcExtensionAddress`의 값이 `tcp://lvaExtension:5001`로 설정된 것을 볼 수 있습니다. *httpExtensionOpenVINO* 샘플과 달리 url을 gRPC 서버로 변경할 필요가 없습니다. 대신 앞에서 설명한 대로 환경 변수를 통해 특정 파이프라인을 실행하도록 모듈에 지시합니다. 기본 템플릿에서 이 변수를 "object_detection" for "person_vehicle_bike_detection"으로 설정했습니다. 지원되는 다른 파이프라인을 실험해 볼 수 있습니다. 

1. Visual Studio Code에서 **확장** 탭을 열고(또는 Ctrl+Shift+X를 누름) Azure IoT Hub를 검색합니다.
1. 마우스 오른쪽 단추를 클릭하고 **확장 설정** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="확장 설정":::
1. "자세한 정보 메시지 표시"를 검색하고 활성화합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="자세한 정보 메시지 표시":::
1. 디버깅 세션을 시작하려면 F5 키를 선택합니다. **터미널** 창에 메시지가 출력되어 표시됩니다.
1. *operations.json* 코드가 `GraphTopologyList` 및 `GraphInstanceList` 직접 메서드를 호출하여 시작됩니다. 이전 빠른 시작이 완료된 후에 리소스를 정리한 경우 이 프로세스에서 빈 목록을 반환한 다음, 일시 중지합니다. 계속하려면 Enter 키를 선택합니다.

    **터미널** 창에 직접 메서드 호출의 다음 세트가 표시됩니다.

     * 이전 `topologyUrl`을 사용하는 `GraphTopologySet`에 대한 호출
     * 다음 본문을 사용하는 `GraphInstanceSet`에 대한 호출

         ```
         {
           "@apiVersion": "2.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithOpenVINOgRPC",
             "description": "Sample graph description",
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
               }
             ]
           }
         }
         ```

     * 그래프 인스턴스 및 비디오 흐름을 시작하는 `GraphInstanceActivate`에 대한 호출
     * 그래프 인스턴스가 실행 중 상태임을 보여 주는 `GraphInstanceList`에 대한 두 번째 호출
1. **터미널** 창의 출력이 `Press Enter to continue` 프롬프트에서 일시 중지됩니다. 아직 Enter 키를 선택하지 마세요. 위로 스크롤하여 호출한 직접 메서드에 대한 JSON 응답 페이로드를 확인합니다.
1. Visual Studio Code에서 **출력** 창으로 전환합니다. Live Video Analytics on IoT Edge 모듈에서 IoT 허브에 보내는 메시지가 표시됩니다. 이 빠른 시작의 다음 섹션에서는 이러한 메시지를 설명합니다.
1. 미디어 그래프가 계속 실행되어 결과를 출력합니다. RTSP 시뮬레이터가 원본 비디오를 계속 반복합니다. 미디어 그래프를 중지하려면 **터미널** 창으로 돌아가서 Enter 키를 선택합니다. 

    일련의 다음 호출은 리소스를 정리합니다.
      * `GraphInstanceDeactivate`에 대한 호출은 그래프 인스턴스를 비활성화합니다.
      * `GraphInstanceDelete`에 대한 호출은 인스턴스를 삭제합니다.
      * `GraphTopologyDelete`에 대한 호출은 토폴로지를 삭제합니다.
      * `GraphTopologyList`에 대한 최종 호출은 목록이 비어 있음을 보여 줍니다.

## <a name="interpret-results"></a>결과 해석

미디어 그래프를 실행하면 HTTP 확장 프로세서 노드의 결과가 IoT Hub 싱크 노드를 통해 IoT 허브로 전달됩니다. **출력** 창에 표시되는 메시지에는 `body` 섹션 및 `applicationProperties` 섹션이 포함되어 있습니다. 자세한 내용은 [IoT Hub 메시지 만들기 및 읽기](../../iot-hub/iot-hub-devguide-messages-construct.md)를 참조하세요.

다음 메시지에서 Live Video Analytics 모듈은 애플리케이션 속성 및 본문의 콘텐츠를 정의합니다. 

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 이벤트

미디어 그래프가 인스턴스화되면 RTSP 원본 노드가 rtspsim-live555 컨테이너에서 실행되는 RTSP 서버에 연결을 시도합니다. 연결에 성공하면 다음 이벤트가 출력됩니다. 이벤트 유형은 **Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished** 입니다.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1612432131600584 1 IN IP4 172.18.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/homes_00425.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-214.166\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/homes_00425.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=64001F;sprop-parameter-sets=Z2QAH6zZQFAFuwFsgAAAAwCAAAAeB4wYyw==,aOvhEsiw\r\na=control:track1\r\n"
}
```

이 메시지에서 다음 세부 정보를 확인합니다.

* 메시지는 진단 이벤트입니다. `MediaSessionEstablished`는 RTSP 원본 노드(주체)에서 RTSP 시뮬레이터에 연결하고 라이브 피드(시뮬레이션된)를 받기 시작했음을 나타냅니다.
* `applicationProperties`에서 `subject`는 메시지가 미디어 그래프의 RTSP 원본 노드에서 생성되었음을 나타냅니다.
* `applicationProperties`에서 `eventType`은 이 이벤트가 진단 이벤트임을 나타냅니다.
* `eventTime`은 이벤트가 발생한 시간을 나타냅니다.
* `body`에는 진단 이벤트에 대한 데이터가 포함됩니다. 이 경우 데이터는 [SDP(세션 설명 프로토콜)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 세부 정보로 구성됩니다.

### <a name="inference-event"></a>유추 이벤트

gRPC 확장 프로세서 노드는 Intel OpenVINO™ DL Streamer – Edge AI Extension에서 유추 결과를 받습니다. 그런 다음, IoT Hub 싱크 노드를 통해 결과를 유추 이벤트로 내보냅니다. 

이러한 이벤트에서 유형은 자동차 또는 트럭과 같은 엔터티임을 나타내기 위해 `entity`로 설정됩니다. `eventTime` 값은 개체가 감지된 UTC 시간입니다. 

다음 예제에서는 이 모델이 차량, 차량 종류(밴) 및 차량 색상(흰색)을 식별했고 신뢰도 수준은 0.9보다 높으며, 개체 추적 모델을 사용할 때 엔터티에 ID를 할당한 것을 볼 수 있습니다.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "timestamp": 145118912223221,
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "vehicle",
          "confidence": 0.9605301
        },
        "attributes": [
          {
            "name": "color",
            "value": "white",
            "confidence": 0.9605301
          },
          {
            "name": "type",
            "value": "car",
            "confidence": 0.9605301
          }
        ],
        "box": {
          "l": 0.3958135,
          "t": 0.078730375,
          "w": 0.48403296,
          "h": 0.94352424
        },
        "id&quot;: &quot;1"
      }
    }
}
```

메시지에서 다음 세부 정보를 확인합니다.

* `applicationProperties`에서 `subject`는 메시지가 생성된 그래프 토폴로지의 노드를 참조합니다. 
* `applicationProperties`에서 `eventType`은 이 이벤트가 분석 이벤트임을 나타냅니다.
* `eventTime` 값은 이벤트가 발생한 시간을 나타냅니다.
* `body` 섹션에는 분석 이벤트에 대한 데이터가 포함됩니다. 이 경우 이벤트는 유추 이벤트이므로 본문에 `inferences` 데이터가 포함됩니다.
* `inferences` 섹션은 `type`이 `entity`임을 나타냅니다. 이 섹션에는 엔터티에 대한 추가 데이터가 포함됩니다.

## <a name="run-the-sample-program-to-detect-persons-or-vehicles-or-bikes"></a>샘플 프로그램을 실행하여 사람, 차량 또는 자전거 감지
다른 모델을 사용하려면 배포 템플릿을 변경해야 합니다. 지원되는 모델 간에 전환하려면 lvaExtenstion 모듈에 들어 있는 환경 변수를 변경하면 됩니다. 모델에 지원되는 값과 조합에 대한 내용은 [GitHub의 이 문서](https://github.com/intel/video-analytics-serving/tree/master/samples/ava_ai_extension#edge-ai-extension-module-options)를 참조하세요.

```
"Env":[
"PIPELINE_NAME=object_classification",
"PIPELINE_VERSION=vehicle_attributes_recognition"
],
```
> [!TIP]
> 템플릿을 복사하고 가능한 파이프라인마다 새 이름으로 저장합니다. 이렇게 하면 이러한 템플릿 중 하나를 기반으로 새 배포를 만들어서 모델 간에 전환할 수 있습니다.

변수를 변경한 후에는 템플릿을 다시 디바이스에 배포할 수 있습니다. 이제 위의 단계를 반복하여 샘플 프로그램을 새 토폴로지로 다시 실행할 수 있습니다. 유추 결과의 스키마는 비슷하지만 선택한 파이프라인 모델에 따라 표시되는 정보가 더 많거나 더 적을 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 빠른 시작이나 자습서를 사용하려면 여기서 만든 리소스를 그대로 유지하세요. 그렇지 않으면 Azure Portal, 리소스 그룹으로 차례로 이동하고, 이 자습서를 실행한 리소스 그룹을 선택한 다음, 모든 리소스를 삭제하세요.

## <a name="next-steps"></a>다음 단계

고급 사용자에 대한 추가 문제를 검토합니다.

* RTSP 시뮬레이터를 사용하는 대신 RTSP를 지원하는 [IP 카메라](https://en.wikipedia.org/wiki/IP_camera)를 사용합니다. [ONVIF 규격](https://www.onvif.org/conformant-products/) 제품 페이지에서 RTSP를 지원하는 IP 카메라를 검색할 수 있습니다. G, S 또는 T 프로필을 준수하는 디바이스를 찾습니다.
* Azure Linux VM 대신 Intel x64 Linux 디바이스를 사용합니다. 이 디바이스는 IP 카메라와 동일한 네트워크에 있어야 합니다. [Linux에 Azure IoT Edge 런타임 설치](../../iot-edge/how-to-provision-single-device-linux-symmetric.md)의 지침을 따릅니다. 그런 다음, [가상 Linux 디바이스에 첫 번째 IoT Edge 모듈 배포](../../iot-edge/quickstart-linux.md)의 지침에 따라 디바이스를 Azure IoT Hub에 등록합니다.
