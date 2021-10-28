---
title: 클라우드 간 연속 비디오 녹화 및 재생 - Azure
description: 이 자습서에서는 Azure Live Video Analytics on IoT Edge를 사용하여 클라우드에 지속적으로 비디오를 녹화하고, Azure Media Services를 사용하여 비디오에서 원하는 부분을 스트리밍하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: f28af228008f2977f202d2d33b62b4fd7525b8b8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130241842"
---
# <a name="tutorial-use-live-video-analytics-for-continuous-video-recording-and-playback-tofrom-the-cloud"></a>자습서: Live Video Analytics를 사용하여 클라우드 간 연속 비디오 녹화 및 재생

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

이 자습서에서는 Azure Live Video Analytics on IoT Edge를 사용하여 클라우드에 [지속적으로 비디오를 녹화(CVR)](continuous-video-recording-concept.md)하고, Azure Media Services를 사용하여 비디오에서 원하는 부분을 스트리밍하는 방법을 알아봅니다. 이 기능은 카메라에 기록된 내용을 수일 또는 수주일 동안 유지해야 하는 안전, 규정 준수 등의 시나리오에 유용합니다. 

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 관련 리소스 설치
> * CVR을 수행하는 코드 검사
> * 샘플 코드 실행
> * 결과 검사 및 비디오 보기

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>추천 참고 자료  

시작하기 전에 다음 문서를 참조하세요.

* [Live Video Analytics on IoT Edge 개요](overview.md)
* [Live Video Analytics on IoT Edge 용어](terminology.md)
* [미디어 그래프 개념](media-graph-concept.md) 
* [연속 비디오 녹화 시나리오](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>필수 구성 요소

이 자습서의 필수 구성 요소는 다음과 같습니다.

* 개발 머신의 [Visual Studio Code](https://code.visualstudio.com/)([Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 및 [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) 확장 포함)

    > [!TIP]
    > Docker를 설치하라는 메시지가 표시될 수 있습니다. 이 메시지는 무시하세요.
* 개발 머신에 설치된 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer)
* [Live Video Analytics 리소스 설치 스크립트](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) 완료

이러한 단계가 완료되면 다음과 같은 관련 Azure 리소스가 Azure 구독에 배포됩니다.

* Azure IoT Hub
* Azure Storage 계정
* Azure Media Services 계정
* [IoT Edge 런타임](../../iot-edge/how-to-provision-single-device-linux-symmetric.md)이 설치된 Azure의 Linux VM

> [!TIP]
> 만든 Azure 리소스와 관련된 문제가 발생하는 경우 **[문제 해결 가이드](troubleshoot-how-to.md#common-error-resolutions)** 를 참조하여 일반적으로 발생하는 문제를 해결하세요.

## <a name="concepts"></a>개념

[미디어 그래프 개념](media-graph-concept.md) 문서에 설명된 대로, 미디어 그래프를 통해

- 미디어를 캡처해야 하는 위치,
- 처리 방법,
- 결과를 전달할 위치를 정의할 수 있습니다. 
 
 CVR을 수행하려면 RTSP 가능 카메라에서 비디오를 캡처하고, [Azure Media Services 자산](terminology.md#asset)에 연속 녹화해야 합니다. 아래 다이어그램은 해당 미디어 그래프의 그래픽 표현을 보여줍니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording-tutorial/continuous-video-recording-overview.svg" alt-text="미디어 그래프":::

이 자습서에서는 [Live555 Media Server](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)를 사용하여 빌드된 에지 모듈을 하나 사용하여 RTSP 카메라를 시뮬레이션합니다. 미디어 그래프 내에서 [RTSP 원본](media-graph-concept.md#rtsp-source) 노드를 사용하여 라이브 피드를 가져온 다음, 해당 비디오를 자산에 기록할 [자산 싱크 노드](media-graph-concept.md#asset-sink)로 보냅니다. 이 자습서에서 사용되는 비디오는 [고속도로 교차로 샘플 비디오](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv)입니다.
<iframe src="https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

## <a name="set-up-your-development-environment"></a>개발 환경 설정

시작하기 전에 [필수 구성 요소](#prerequisites)의 세 번째 항목을 완료했는지 확인합니다. 리소스 설치 스크립트가 완료되면 중괄호를 선택하여 폴더 구조를 표시합니다. ~/clouddrive/lva-sample 디렉터리 아래에 만들어진 몇 개의 파일을 볼 수 있습니다.

![앱 설정](./media/quickstarts/clouddrive.png)

이 자습서에서 다루는 파일은 다음과 같습니다.

* **~/clouddrive/lva-sample/edge-deployment/.env**: Visual Studio Code에서 모듈을 에지 디바이스에 배포하는 데 사용하는 속성이 포함되어 있습니다.
* **~/clouddrive/lva-sample/appsettings.json**: Visual Studio Code에서 샘플 코드를 실행하는 데 사용합니다.

이러한 파일은 다음 단계를 수행하는 데 필요합니다.

1. GitHub 링크(https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp )의 리포지토리를 복제합니다.
1. Visual Studio Code를 시작하고, 리포지토리를 다운로드한 폴더를 엽니다.
1. Visual Studio Code에서 src/cloud-to-device-console-app 폴더로 이동하고, **appsettings.json** 이라는 파일을 만듭니다. 이 파일에는 프로그램을 실행하는 데 필요한 설정이 포함되어 있습니다.
1. ~/clouddrive/lva-sample/appsettings.json 파일의 콘텐츠를 복사합니다. 텍스트는 다음과 같습니다.
    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
    IoT Hub 연결 문자열을 사용하면 Visual Studio Code를 사용하여 Azure IoT Hub를 통해 명령을 에지 모듈에 보낼 수 있습니다.
    
1. 다음으로, src/edge 폴더로 이동하고, **.env** 라는 파일을 만듭니다.
1. ~/clouddrive/lva-sample/edge-deployment/.env 파일의 콘텐츠를 복사합니다. 텍스트는 다음과 같습니다.

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaedgeuser/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>샘플 파일 검사

Visual Studio Code에서 src/edge/deployment.template.json을 엽니다. 이 템플릿은 에지 디바이스(Azure Linux VM)에 배포할 에지 모듈을 정의합니다. **모듈** 섹션 아래에는 다음 이름을 사용하는 두 개 항목이 있습니다.

* **lvaEdge**: Live Video Analytics on IoT Edge 모듈입니다.
* **rtspsim**: RTSP 시뮬레이터입니다.

다음으로, src/cloud-to-device-console-app 폴더로 이동합니다. 이 폴더에는 앞에서 만든 appsettings.json 파일과 몇 가지 다른 파일이 있습니다.

* **c2d-console-app.csproj**: Visual Studio Code의 프로젝트 파일입니다.
* **operations.json**: 이 파일에는 우리가 실행할 여러 작업이 나열됩니다.
* **Program.cs**: 다음과 같은 작업을 수행하는 샘플 프로그램 코드입니다.
    * 앱 설정을 로드합니다.
    * Live Video Analytics on IoT Edge 모듈이 공개하는 직접 메서드를 호출합니다. 이 모듈을 통해 [직접 메서드](direct-methods.md)를 호출하여 라이브 비디오 스트림을 분석할 수 있습니다.
    * **터미널** 창에서 프로그램의 출력을 검토하고, **출력** 창에서 모듈이 생성한 이벤트를 살펴볼 수 있도록 일시 중지합니다.
    * 리소스를 정리하는 직접 메서드를 호출합니다.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge 배포 매니페스트 생성 및 배포 

배포 매니페스트는 에지 디바이스에 배포되는 모듈 및 해당 모듈의 구성 설정을 정의합니다. 다음 단계에 따라 템플릿 파일에서 매니페스트를 생성한 다음, 에지 디바이스에 배포합니다.

1. Visual Studio Code를 시작합니다.
1. 왼쪽 아래 모서리에서 **AZURE IOT HUB** 창 옆에 있는 **추가 작업** 아이콘을 선택하여 IoT Hub 연결 문자열을 설정합니다. src/cloud-to-device-console-app/appsettings.json 파일의 문자열을 복사합니다. 

    ![IoT Hub 연결 문자열 설정](./media/quickstarts/set-iotconnection-string.png)
    > [!NOTE]
    > IoT Hub에 대한 기본 제공 엔드포인트 정보를 제공하라는 메시지가 표시될 수 있습니다. 해당 정보를 가져오려면 Azure Portal에서 IoT Hub로 이동하여 왼쪽 탐색 창에서 **기본 제공 엔드포인트** 옵션을 찾습니다. 여기를 클릭하고 **Event Hub 호환 엔드포인트** 섹션에서 **Event Hub 호환 엔드포인트** 를 찾습니다. 상자의 텍스트를 복사하여 사용합니다. 엔드포인트는 다음과 같이 표시됩니다.  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```

1. src/edge/deployment.template.json 파일을 마우스 오른쪽 단추로 클릭하고, **IoT Edge 배포 매니페스트 생성** 을 선택합니다. Visual Studio Code는 .env 파일의 값을 사용하여 배포 템플릿 파일에서 찾은 값을 대체합니다. 이렇게 하면 src/edge/config 폴더에 **deployment.amd64.json** 이라는 매니페스트 파일이 생성됩니다.

   ![IoT Edge 배포 매니페스트 생성](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. src/edge/config/deployment.amd64.json 파일을 마우스 오른쪽 단추로 클릭하고, **단일 디바이스용 배포 만들기** 를 선택합니다.

   ![단일 디바이스용 배포 만들기](./media/quickstarts/create-deployment-single-device.png)
1. **IoT Hub 디바이스 선택** 메시지가 표시됩니다. 드롭다운 목록에서 lva-sample-device를 선택합니다.
1. 약 30초 후에 왼쪽 아래 섹션에서 Azure IoT Hub가 새로 고침됩니다. 에지 디바이스에 다음 모듈이 배포된 것을 볼 수 있습니다.
    * Live Video Analytics on IoT Edge(모듈 이름 **lvaEdge**)
    * RTSP 시뮬레이터(모듈 이름 **rtspsim**)
 
    ![IoT Hub](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>모듈 모니터링 준비 

Live Video Analytics on IoT Edge 모듈을 사용하여 라이브 비디오 스트림을 녹화할 때 모듈은 IoT Hub에 이벤트를 보냅니다. 이러한 이벤트를 보려면 다음 단계를 수행합니다.

1. Visual Studio Code에서 탐색기 창을 열고, 왼쪽 아래 모서리에서 **Azure IoT Hub** 를 찾습니다.
1. **디바이스** 노드를 펼칩니다.
1. 마우스 오른쪽 단추로 lva-sample-device 파일을 클릭하고, **기본 제공 이벤트 엔드포인트 모니터링 시작** 을 선택합니다.

    ![기본 제공 이벤트 엔드포인트 모니터링 시작](./media/quickstarts/start-monitoring-iothub-events.png)

    > [!NOTE]
    > IoT Hub에 대한 기본 제공 엔드포인트 정보를 제공하라는 메시지가 표시될 수 있습니다. 해당 정보를 가져오려면 Azure Portal에서 IoT Hub로 이동하여 왼쪽 탐색 창에서 **기본 제공 엔드포인트** 옵션을 찾습니다. 여기를 클릭하고 **Event Hub 호환 엔드포인트** 섹션에서 **Event Hub 호환 엔드포인트** 를 찾습니다. 상자의 텍스트를 복사하여 사용합니다. 엔드포인트는 다음과 같이 표시됩니다.  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```

## <a name="run-the-program"></a>프로그램 실행 

1. Visual Studio Code에서 **확장** 탭을 열고(또는 Ctrl+Shift+X를 누름) Azure IoT Hub를 검색합니다.
1. 마우스 오른쪽 단추를 클릭하고 **확장 설정** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="확장 설정":::
1. "자세한 정보 메시지 표시"를 검색하고 활성화합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="자세한 정보 메시지 표시":::
1. src/cloud-to-device-console-app/operations.json으로 이동합니다.
1. **GraphTopologySet** 노드 아래에서 다음을 편집합니다.

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/2.0/topology.json" `
1. 그런 다음, **GraphInstanceSet** 및 **GraphTopologyDelete** 노드에서 **topologyName** 값이 이전 그래프 토폴로지의 **name** 속성 값과 일치하는지 확인합니다.

    `"topologyName" : "CVRToAMSAsset"`  
1. 브라우저에서 [토폴로지](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/2.0/topology.json)를 열고 assetNamePattern을 확인합니다. 자산에 고유한 이름을 지정하기 위해 operations.json 파일에서 그래프 인스턴스 이름을 (기본값 "Sample-Graph-1"에서 다른 값으로) 변경해야 할 수도 있습니다.

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
1. F5 키를 선택하여 디버깅 세션을 시작합니다. **터미널** 창에 일부 메시지가 출력됩니다.
1. operations.json 파일이 GraphTopologyList 및 GraphInstanceList를 호출하여 시작됩니다. 이전 빠른 시작 또는 자습서를 마친 후 리소스를 정리했다면 이 작업은 다음과 같이 빈 목록을 반환하고, 사용자가 **Enter** 키를 선택할 수 있도록 일시 중지됩니다.

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
      "@apiVersion": "2.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```

1. **터미널** 창에서 **Enter** 키를 선택하면 다음과 같은 직접 메서드 세트가 호출됩니다.
   * 이전 topologyUrl을 사용하여 GraphTopologySet 호출
   * 다음 본문을 사용하여 GraphInstanceSet 호출
     
     ```
     {
       "@apiVersion": "2.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "CVRToAMSAsset",
         "description": "Sample graph description",
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
   * 그래프 인스턴스를 시작하고 비디오 흐름을 시작하는 GraphInstanceActivate 호출
   * 그래프 인스턴스가 실행 중 상태임을 보여주는 두 번째 GraphInstanceList 호출 
1. 이제 **터미널** 창의 출력이 **계속하려면 Enter 키를 누르세요** 라는 메시지에서 일시 중지됩니다. 지금은 **Enter** 키를 선택하지 마세요. 위로 스크롤하여 호출한 직접 메서드에 대한 JSON 응답 페이로드를 확인합니다.
1. 이제 Visual Studio Code에서 **출력** 창으로 전환하면 Live Video Analytics on IoT Edge 모듈에서 IoT Hub로 보내는 메시지가 표시됩니다.

   이러한 메시지에 대한 내용은 다음 섹션에서 다룹니다.
1. 그래프 인스턴스가 계속 실행되어 비디오를 녹화합니다. RTSP 시뮬레이터가 원본 비디오를 계속 반복합니다. 녹화를 중지하려면 **터미널** 창으로 돌아가서 **Enter** 를 선택합니다. 다음과 같은 일련의 호출을 수행하여 리소스를 정리합니다.

   * 그래프 인스턴스를 비활성화하는 GraphInstanceDeactivate 호출
   * 인스턴스를 삭제하는 GraphInstanceDelete 호출
   * 토폴로지를 삭제하는 GraphTopologyDelete 호출
   * 이제 목록이 비어 있음을 보여주는 마지막 GraphTopologyList 호출

## <a name="interpret-the-results"></a>결과 해석 

미디어 그래프를 실행하면 Live Video Analytics on IoT Edge 모듈에서 특정 진단 및 작업 이벤트를 IoT Edge 허브에 보냅니다. 이러한 이벤트는 Visual Studio Code의 **출력** 창에 표시되는 메시지입니다. 여기에는 body 섹션 및 applicationProperties 섹션이 포함되어 있습니다. 이러한 섹션이 나타내는 내용을 이해하려면 [IoT Hub 메시지 작성 및 읽기](../../iot-hub/iot-hub-devguide-messages-construct.md)를 참조하세요.

다음 메시지에서 애플리케이션 속성 및 body의 콘텐츠는 Live Video Analytics 모듈에서 정의됩니다.

## <a name="diagnostics-events"></a>진단 이벤트 

### <a name="mediasession-established-event"></a>MediaSession Established 이벤트

그래프 인스턴스가 활성화되면 RTSP 원본 노드는 rtspsim 모듈에서 실행되는 RTSP 서버에 연결을 시도합니다. 성공하면 이 이벤트가 출력됩니다.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T09:42:18.1280000Z"
  }
}
```

* 이 메시지는 진단 이벤트(MediaSessionEstablished)입니다. RTSP 원본 노드(주체)에서 RTSP 시뮬레이터와의 연결을 설정하고 라이브 피드(시뮬레이션된)를 받기 시작했음을 나타냅니다.
* applicationProperties의 subject 섹션은 메시지가 생성된 그래프 토폴로지의 노드를 참조합니다. 여기서는 메시지가 RTSP 원본 노드에서 시작됩니다.
* applicationProperties의 eventType 섹션은 진단 이벤트임을 나타냅니다.
* eventTime 섹션은 이벤트가 발생한 시간을 나타냅니다.
* body 섹션에는 진단 이벤트에 대한 데이터가 포함됩니다. 여기서는 [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 세부 정보입니다.

## <a name="operational-events"></a>작업 이벤트 

### <a name="recordingstarted-event"></a>RecordingStarted 이벤트

자산 싱크 노드는 비디오 녹화가 시작되면 **Microsoft.Media.Graph.Operational.RecordingStarted** 유형의 이 이벤트를 내보냅니다.

```
[IoTHubMonitor] [9:42:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-04-09T09:42:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

applicationProperties의 subject 섹션은 이 메시지를 생성한 그래프의 자산 싱크 노드를 참조합니다.

body 섹션에는 출력 위치에 대한 정보가 포함됩니다. 이 경우 비디오가 녹화되는 Azure Media Services 자산의 이름입니다. 이 값을 적어 두세요.

### <a name="recordingavailable-event"></a>RecordingAvailable 이벤트

이름에서 알 수 있듯이, 녹화가 시작되면 RecordingStarted 이벤트가 전송되지만, 비디오 데이터는 아직 자산에 업로드되지 않았을 수 있습니다. 자산 싱크 노드는 자산에 비디오 데이터를 업로드한 후 **Microsoft.Media.Graph.Operational.RecordingAvailable** 유형의 이 이벤트를 내보냅니다.

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-04-09T09:43:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

이 이벤트는 플레이어 또는 클라이언트에서 비디오 재생을 시작하는 데 충분한 데이터가 자산에 기록되었음을 나타냅니다.

applicationProperties의 subject 섹션은 이 메시지를 생성한 그래프의 AssetSink 노드를 참조합니다.

body 섹션에는 출력 위치에 대한 정보가 포함됩니다. 이 경우 비디오가 녹화되는 Azure Media Services 자산의 이름입니다.

### <a name="recordingstopped-event"></a>RecordingStopped 이벤트

Graph 인스턴스를 비활성화하면 자산 싱크 노드는 자산에 비디오를 녹화하는 것을 중지합니다. 그리고 **Microsoft.Media.Graph.Operational.RecordingStopped** 형식의 이 이벤트를 내보냅니다.

```
[IoTHubMonitor] [11:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-04-10T11:33:31.051Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

이 이벤트는 기록이 중지되었음을 나타냅니다.

applicationProperties의 subject 섹션은 이 메시지를 생성한 그래프의 AssetSink 노드를 참조합니다.

body 섹션에는 출력 위치에 대한 정보가 포함됩니다. 여기서는 비디오가 기록되는 Azure Media Services 자산의 이름입니다.

## <a name="media-services-asset"></a>Media Services 자산  

Azure Portal에 로그인하여 비디오를 살펴보면 미디어 그래프에서 만든 Media Services 자산을 검사할 수 있습니다.

1. 웹 브라우저를 열고 [Azure Portal](https://portal.azure.com/)로 이동합니다. 자격 증명을 입력하여 포털에 로그인합니다. 기본 보기는 서비스 대시보드입니다.
1. 구독에 있는 리소스 중에서 Media Services 계정을 찾아 계정 창을 엽니다.
1. **Media Services** 목록에서 **자산** 을 선택합니다.

    ![Media Services 자산](./media/continuous-video-recording-tutorial/assets.png)
1. sampleAsset-CVRToAMSAsset-Sample-Graph-1라는 이름으로 나열된 자산을 찾을 수 있습니다. 그래프 토폴로지 파일에서 선택한 명명 패턴입니다.
1. 자산을 선택합니다.
1. 자산 세부 정보 페이지의 **스트리밍 URL** 텍스트 상자 아래에서 **새로 만들기** 를 선택합니다.

    ![새 자산](./media/continuous-video-recording-tutorial/new-asset.png)

1. 열린 마법사에서 기본 옵션을 그대로 적용하고 **추가** 를 선택합니다. 자세한 내용은 [비디오 재생](video-playback-concept.md)을 참조하세요.

    > [!TIP]
    > [스트리밍 엔드포인트가 실행 중](../latest/stream-streaming-endpoint-concept.md)인지 확인합니다.
1. 플레이어에서 비디오를 로드해야 합니다. **재생** 을 선택하여 비디오를 봅니다.

> [!NOTE]
> 비디오 원본은 카메라 피드를 시뮬레이션하는 컨테이너이므로 비디오의 타임스탬프는 그래프 인스턴스를 활성화한 시간 및 비활성화한 시간과 관련이 있습니다. 여러 날의 기록을 찾아서 해당 보관 파일의 일부를 보려면 [여러 날 녹화본 재생](playback-multi-day-recordings-tutorial.md) 자습서를 참조하세요. 또한 이 자습서에서는 화면에 표시되는 비디오에 타임스탬프가 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 자습서를 진행하려면 여기서 만든 리소스를 그대로 둡니다. 다른 자습서를 진행할 생각이 없으면 Azure Portal로 이동하여 해당하는 리소스 그룹을 찾고, 이 자습서를 실행한 리소스 그룹을 선택하고, 리소스 그룹을 삭제합니다.

## <a name="next-steps"></a>다음 단계

* RTSP 시뮬레이터 대신 RTSP를 지원하는 [IP 카메라](https://en.wikipedia.org/wiki/IP_camera)를 사용합니다. [ONVIF 규격](https://www.onvif.org/conformant-products/) 제품 페이지에서 프로필 G, S 또는 T를 준수하는 디바이스를 찾아 RTSP를 지원하는 IP 카메라를 검색할 수 있습니다.
* Azure Linux VM 대신 AMD64 또는 X64 Linux 디바이스를 사용합니다. 이 디바이스는 IP 카메라와 동일한 네트워크에 있어야 합니다. [Linux에 Azure IoT Edge 런타임 설치](../../iot-edge/how-to-provision-single-device-linux-symmetric.md)의 지침을 따릅니다. 그런 다음, [가상 Linux 디바이스에 첫 번째 IoT Edge 모듈 배포](../../iot-edge/quickstart-linux.md) 빠른 시작의 지침에 따라 디바이스를 Azure IoT Hub에 등록합니다.
