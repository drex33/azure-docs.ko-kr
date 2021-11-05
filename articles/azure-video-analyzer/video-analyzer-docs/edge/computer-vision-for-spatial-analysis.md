---
title: 공간 분석을 위해 Computer Vision을 사용하여 라이브 비디오 분석 - Azure
description: 이 자습서에서는 Azure Cognitive Services의 Computer Vision 공간 분석 AI 기능과 함께 Azure Video Analyzer를 사용하여 (시뮬레이션된) IP 카메라의 라이브 비디오 피드를 분석하는 방법을 보여줍니다.
author: Juliako
ms.author: juliako
ms.service: azure-video-analyzer
ms.topic: tutorial
ms.date: 06/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f492272e2365d242859357e86056fd3d5095e88b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101028"
---
# <a name="tutorial-live-video-with-computer-vision-for-spatial-analysis-preview"></a>자습서: Computer Vision과 함께 라이브 비디오를 사용하여 공간 분석(미리 보기)

[!INCLUDE [header](includes/edge-env.md)]

이 자습서에서는 [Azure Cognitive Services의 공간 분석 AI 서비스를 위한 Computer Vision](../../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md)과 함께 Azure Video Analyzer를 사용하여 (시뮬레이션된) IP 카메라의 라이브 비디오 피드를 분석하는 방법을 보여줍니다. 이 추론 서버를 통해 스트리밍 비디오를 분석하여 물리적 공간에서 사람과 움직임 간의 공간 관계를 이해하는 방법을 확인할 수 있습니다. 비디오 피드의 프레임은 이 추론 서버에 전송되고, 결과는 IoT Edge 허브로 전송되고, 일부 조건이 충족되면 비디오 클립이 녹화되고 Video Analyzer 계정에 비디오로 저장됩니다.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
>
> - 리소스 설정
> - 코드 검사
> - 샘플 코드 실행
> - 이벤트 모니터링

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>추천 참고 자료

시작하기 전에 다음 문서를 참조하세요.

- [Video Analyzer 개요](../overview.md)
- [Video Analyzer 용어](../terminology.md)
- [파이프라인 개념](../pipeline.md)
- [이벤트 기반 비디오 녹화](record-event-based-live-video.md)
- 공간 분석을 위한 [Azure Cognitive Service Computer Vision 컨테이너](../../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md).

## <a name="prerequisites"></a>필수 구성 요소

다음은 공간 분석 모듈을 Azure Video Analyzer 모듈에 연결하기 위한 필수 조건입니다.

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

   > [!Note]
   > 개발 컴퓨터가 연결된 네트워크에서 5671 포트를 통해 고급 메시지 큐 프로토콜을 허용하는지 확인합니다. 이렇게 설정하면 Azure IoT Tools에서 Azure IoT Hub와 통신할 수 있습니다.

## <a name="set-up-azure-resources"></a>Azure 리소스 설정

1. **컴퓨팅 디바이스 선택**  

    공간 분석 컨테이너를 실행하려면 [NVIDIA Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/)가 있는 컴퓨팅 디바이스가 필요합니다. GPU 가속과 함께 **[Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/)** 를 사용하는 것이 좋습니다. 그러나 컨테이너는 호스트 컴퓨터에 [Ubuntu 데스크톱 18.04 LTS](http://releases.ubuntu.com/18.04/)가 설치된 다른 **데스크톱 컴퓨터** 또는 **Azure VM** 에서 실행됩니다.

   #### <a name="azure-stack-edge-device"></a>[Azure Stack Edge 디바이스](#tab/azure-stack-edge)

   Azure Stack Edge는 HaaS(Hardware-as-a-Service) 솔루션이며 네트워크 데이터 전송 기능이 있는 AI 지원 에지 컴퓨팅 디바이스입니다. 자세한 준비 및 설정 지침은 [Azure Stack Edge 설명서](../../../databox-online/azure-stack-edge-deploy-prep.md)를 참조하세요.

   #### <a name="desktop-machine"></a>[데스크톱 컴퓨터](#tab/desktop-machine)

   #### <a name="minimum-hardware-requirements"></a>최소 하드웨어 요구 사항

   - 4GB 시스템 RAM
   - 4GB GPU RAM
   - 8코어 CPU
   - NVIDIA Tesla T4 GPU 1개
   - 20GB의 HDD 공간

   #### <a name="recommended-hardware"></a>권장 하드웨어

   - 32GB 시스템 RAM
   - 16GB GPU RAM
   - 8코어 CPU
   - NVIDIA Tesla T4 GPU 2개
   - 50GB의 SSD 공간

   #### <a name="azure-vm-with-gpu"></a>[GPU가 있는 Azure VM](#tab/virtual-machine)

   K80 GPU가 하나 있는 [NC 시리즈 VM](../../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 활용할 수 있습니다.
        
1. **에지 디바이스 설정**

    #### <a name="azure-stack-edge-device"></a>[Azure Stack Edge 디바이스](#tab/azure-stack-edge)
    [Azure Stack Edge 포털에서 컴퓨팅 구성](../../../cognitive-services/computer-vision/spatial-analysis-container.md#configure-compute-on-the-azure-stack-edge-portal)
    #### <a name="desktop-machine"></a>[데스크톱 컴퓨터](#tab/desktop-machine)
    [호스트 컴퓨터가 Azure Stack Edge 디바이스가 아닌 경우 다음 지침을 따르세요.](../../../cognitive-services/computer-vision/spatial-analysis-container.md#install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer)
    #### <a name="azure-vm-with-gpu"></a>[GPU가 있는 Azure VM](#tab/virtual-machine)
    1. [VM 만들기](../../../cognitive-services/computer-vision/spatial-analysis-container.md?tabs=virtual-machine#create-the-vm) 및 VM에 필요한 Docker 설치

        > [!Important]
        > 해당 문서에 언급된 **IoT 배포 매니페스트 단계를 건너뛰세요**. 자체 **[배포 매니페스트](#configure-deployment-template)** 파일을 사용하여 필요한 컨테이너를 배포합니다.

    1. VM에 연결하고 터미널에서 다음 명령을 입력합니다.
    ```bash
    bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"
    ```
    Azure Video Analyzer 모듈은 권한이 없는 로컬 사용자 계정을 사용하여 에지 디바이스에서 실행됩니다. 또한 애플리케이션 구성 데이터를 저장하기 위해 특정 로컬 폴더가 필요합니다. 마지막으로, 이 방법 가이드에서는 분석을 위해 실시간으로 비디오 피드를 AVA 모듈에 릴레이하는 [RTSP 시뮬레이터](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)를 사용합니다. 이 시뮬레이터는 입력 디렉터리에서 입력으로 사용합니다. 
    
    위에서 사용된 준비 디바이스 스크립트는 이러한 작업을 자동화하므로 하나의 명령을 실행하고, 관련 입력과 구성 폴더, 비디오 입력 파일 및 권한을 가진 사용자 계정을 모두 원활하게 만들 수 있습니다. 명령이 성공적으로 완료되면 에지 디바이스에 생성된 다음 폴더가 표시됩니다. 
    
      * `/home/localedgeuser/samples`
      * `/home/localedgeuser/samples/input`
      * `/var/lib/videoanalyzer`
      * `/var/media`
    
     분석할 입력 파일 역할을 하는 /home/localedgeuser/samples/input 폴더에 있는 비디오 파일(*.mkv)을 확인합니다.  

1. 다음으로, 다른 Azure 리소스를 배포합니다.

   [![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

   > [!NOTE]
   > 위의 단추는 NVIDIA GPU가 없는 기본 Virtual Machine을 만들고 사용합니다. ARM(Azure Resource Manager) 템플릿에서 기존 디바이스를 사용하라는 메시지가 표시되면 "기존 에지 디바이스 사용" 옵션을 사용하고 위 단계의 IoT Hub 및 디바이스 정보를 사용합니다.
   > :::image type="content" source="./media/spatial-analysis/use-existing-device.png" alt-text="기존 디바이스 사용":::

   [!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>개요

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis/overview.png" alt-text="공간 분석 개요":::

이 다이어그램에서는 이 자습서의 신호 흐름을 보여 줍니다. [에지 모듈](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)은 RTSP(Real-Time Streaming Protocol) 서버를 호스트하는 IP 카메라를 시뮬레이션합니다. [RTSP 원본](../pipeline.md#rtsp-source) 노드는 이 서버에서 비디오 피드를 가져와서 비디오 프레임을 `CognitiveServicesVisionProcessor` 노드로 보냅니다.

`CognitiveServicesVisionProcessor` 노드는 프록시 역할을 수행합니다. 비디오 프레임을 지정된 이미지 형식으로 변환합니다. 그런 다음 **공유 메모리** 를 통해 이미지를 gRPC 엔드포인트 뒤에서 AI 작업을 실행하는 다른 에지 모듈에 릴레이합니다. 이 예에서 에지 모듈은 공간 분석 모듈입니다. `CognitiveServicesVisionProcessor` 노드는 다음과 같은 두 가지 작업을 수행합니다.

- 결과를 수집하고 [IoT Hub 싱크](../pipeline.md#iot-hub-message-sink) 노드에 이벤트를 게시합니다. 그런 다음, 노드에서 이러한 이벤트를 [IoT Edge Hub](../../../iot-fundamentals/iot-glossary.md#iot-edge-hub)에 보냅니다.
- 또한 [신호 게이트 프로세서](../pipeline.md#signal-gate-processor)를 사용하여 RTSP 원본에서 30초 비디오 싱크를 캡처하여 비디오 파일로 저장합니다.

## <a name="create-the-computer-vision-resource"></a>Computer Vision 리소스 만들기

[Azure Portal](../../../iot-edge/how-to-deploy-modules-portal.md)에서 또는 Azure CLI를 통해 **표준 S1 계층** 에 대해 [Computer Vision](https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) 유형의 Azure 리소스를 만들어야 합니다. 

## <a name="set-up-your-development-environment"></a>개발 환경 설정

[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

## <a name="configure-deployment-template"></a>배포 템플릿 구성
#### <a name="azure-stack-edge-device"></a>[Azure Stack Edge 디바이스](#tab/azure-stack-edge)
**/src/edge/deployment.spatialAnalysis.ase.template.json** 에서 배포 파일을 찾습니다. 템플릿에는 `avaedge` 모듈, `rtspsim` 모듈 및 `spatialanalysis` 모듈이 있습니다. 

배포 템플릿 파일에서는

1. 배포 매니페스트는 포트 50051를 사용하여 `avaedge` 및 `spatialanalysis` 모듈 간에 통신합니다. 다른 애플리케이션에서 포트를 사용 중인 경우 `spatialanalysis` 모듈의 포트 바인딩을 열린 포트로 설정합니다.

   ```
   "PortBindings": {
       "50051/tcp": [
           {
               "HostPort": "50051"
           }
       ]
   }
   ```

1. `avaedge`의 `IpcMode`와 `spatialanalysis` 모듈 createOptions는 동일해야 하며 **host** 로 설정되어야 합니다.
1. RTSP 시뮬레이터가 작동하려면 Azure Stack Edge 디바이스를 사용할 때 볼륨 범위를 설정해야 합니다.

   1. [SMB 공유에 연결](../../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share)하고 [샘플 소매점 비디오 파일](https://lvamedia.blob.core.windows.net/public/retailshop-15fps.mkv)을 로컬 공유에 복사합니다.

      > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWMIPP]

   1. rtspsim 모듈이 다음과 같이 구성되었는지 확인합니다.
      ```
      "createOptions": {
                          "HostConfig": {
                            "Mounts": [
                              {
                                "Target": "/live/mediaServer/media",
                                "Source": "<your Local Docker Volume Mount name>",
                                "Type": "volume"
                              }
                            ],
                            "PortBindings": {
                              "554/tcp": [
                                {
                                  "HostPort": "554"
                                }
                              ]
                            }
                          }
                        }
      ```
#### <a name="desktop-machine"></a>[데스크톱 컴퓨터](#tab/desktop-machine)
**/src/edge/deployment.spatialAnalysis.generic.template.json** 에서 배포 파일을 찾습니다. 템플릿에는 `avaedge` 모듈, `rtspsim` 모듈 및 `spatialanalysis` 모듈이 있습니다. 

#### <a name="azure-vm-with-gpu"></a>[GPU가 있는 Azure VM](#tab/virtual-machine)
**/src/edge/deployment.spatialAnalysis.generic.template.json** 에서 배포 파일을 찾습니다. 템플릿에는 `avaedge` 모듈, `rtspsim` 모듈 및 `spatialanalysis` 모듈이 있습니다.

---

다음 표에서는 IoT Edge 모듈에 사용되는 다양한 환경 변수를 보여줍니다. `spatialanalysis`의 `env` 속성을 사용하여 위에서 언급한 배포 매니페스트에서 설정할 수도 있습니다.

| 설정 이름 | 값 | 설명|
|---------|---------|---------|
| DISPLAY | :1 | 이 값은 호스트 컴퓨터의 `echo $DISPLAY` 출력과 동일해야 합니다. Azure Stack Edge 디바이스에는 디스플레이가 없습니다. 이 설정은 적용할 수 없습니다.|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | **수정 금지**|
| ARCHON_LOG_LEVEL | Info; Verbose | 로깅 수준, 두 값 중 하나 선택|
| QT_X11_NO_MITSHM | 1 | **수정 금지**|
| OMP_WAIT_POLICY | PASSIVE | **수정 금지**|
| EULA | accept | 컨테이너를 실행하려면 이 값을 *accept* 로 설정해야 합니다. |
| ARCHON_TELEMETRY_IOTHUB | true | 원격 분석 이벤트를 IoT Hub로 보내려면 이 값을 true로 설정합니다. |
| 청구 | 엔드포인트 URI| 이 값은 Azure Portal의 Computer Vision 리소스에서 수집합니다. 리소스의 **키 및 엔드포인트** 블레이드에서 찾을 수 있습니다.|
| APIKEY | API 키| 이 값은 Azure Portal의 Computer Vision 리소스에서 수집합니다. 리소스의 **키 및 엔드포인트** 블레이드에서 찾을 수 있습니다. |
| LAUNCHER_TYPE | avaBackend | **수정 금지** |
| ARCHON_GRAPH_READY_TIMEOUT | 600 | GPU가 T4 또는 NVIDIA 2080 Ti가 **아닌** 경우 이 환경 변수를 추가합니다.|

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다. 

### <a name="gathering-keys-and-endpoint-uri"></a>키 및 엔드포인트 URI 수집

API 키는 공간 분석 컨테이너를 시작하는 데 사용되며 Computer Vision 리소스의 Azure Portal `Keys and Endpoint` 페이지에서 사용할 수 있습니다. 해당 페이지로 이동하여 `spatialAnalysis` 컨테이너에 필요한 키와 엔드포인트 URI를 찾습니다.  

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis/keys-endpoint.png" alt-text="엔드포인트 URI":::

## <a name="generate-and-deploy-the-deployment-manifest"></a>배포 매니페스트 생성 및 배포

배포 매니페스트는 에지 디바이스에 배포되는 모듈을 정의합니다. 또한 해당 모듈에 대한 구성 설정을 정의합니다.

다음 단계에 따라 템플릿 파일에서 매니페스트를 생성한 다음, 에지 디바이스에 배포합니다.

1. Visual Studio Code를 엽니다.
1. `AZURE IOT HUB` 창 옆에 있는 [추가 작업] 아이콘을 선택하여 IoT Hub 연결 문자열을 설정합니다. `src/cloud-to-device-console-app/appsettings.json` 파일에서 문자열을 복사할 수 있습니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/set-connection-string.png" alt-text="공간 분석: 연결 문자열":::

1. 폴더 탐색기에서 배포 템플릿 파일을 마우스 오른쪽 단추로 클릭하고 IoT Edge 배포 매니페스트 생성을 선택합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/generate-deployment-manifest.png" alt-text="공간 분석: 배포 amd64 json":::

   이렇게 하면 **src/edge/config** 폴더에 매니페스트 파일이 만들어집니다.

1. 생성된 매니페스트 파일을 마우스 오른쪽 단추로 클릭하고 **단일 디바이스용 배포 만들기** 를 선택한 다음, 에지 디바이스의 이름을 선택합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/deployment-single-device.png" alt-text="공간 분석: 단일 디바이스에 배포":::

1. 페이지 맨 위에 IoT Hub 디바이스를 선택하라는 메시지가 표시됩니다. 드롭다운 메뉴에서 에지 디바이스 이름을 선택합니다.
1. 약 30초 후 창의 왼쪽 아래 모서리에서 **AZURE IOT HUB** 창을 새로 고칩니다. 이제 에지 디바이스에 다음과 같은 배포된 모듈이 표시됩니다.

   - Azure Video Analyzer(모듈 이름 **avaedge**)
   - RTSP(Real-Time Streaming Protocol) 시뮬레이터(모듈 이름 **rtspsim**)
   - 공간 분석(모듈 이름 **spatialanalysis**)

배포가 성공하면 다음과 같은 메시지가 출력 창에 표시됩니다.

```
[Edge] Start deployment to device [<edge device name>]
[Edge] Deployment succeeded.
```

그런 다음, 디바이스/모듈에서 `avaedge`, `spatialanalysis` 및 `rtspsim` 모듈을 찾을 수 있으며 모듈의 상태는 "**실행 중**"입니다.

## <a name="prepare-to-monitor-events"></a>이벤트 모니터링 준비

이러한 이벤트를 보려면 다음 단계를 수행합니다.

1. Visual Studio Code에서 **확장** 탭을 열고(또는 Ctrl+Shift+X를 누름) Azure IoT Hub를 검색합니다.
1. 마우스 오른쪽 단추를 클릭하고 **확장 설정** 을 선택합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/extension-settings.png" alt-text="확장 설정":::

1. "자세한 정보 메시지 표시"를 검색하고 활성화합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/verbose-message.png" alt-text="자세한 정보 메시지 표시":::

1. 탐색기 창을 열고 왼쪽 아래 모서리에서 **AZURE IOT HUB** 를 찾아 마우스 오른쪽 단추로 클릭한 다음, [기본 제공 이벤트 엔드포인트 모니터링 시작]을 선택합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/start-monitoring.png" alt-text="공간 분석: 모니터링 시작":::

## <a name="run-the-program"></a>프로그램 실행

`src/cloud-to-device-console-app/operations.json` 파일에는 직접 메서드를 호출하는 program.cs가 있습니다. `operations.json` 파일을 편집하고 파이프라인 토폴로지 URL, 토폴로지 이름 및 RTSP URL을 업데이트해야 합니다.

operations.json:

- 다음과 같이 파이프라인 토폴로지를 설정합니다.

  ```json
  {
      "opName": "pipelineTopologySet",
      "opParams": {
          "pipelineTopologyUrl": "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-zone-crossing-operation-topology.json"
      }
  },
  ```
* `livePipelineSet` 아래에서 이전 링크의 값과 일치하도록 토폴로지의 이름을 편집합니다.
    * `"topologyName" : "PersonZoneCrossingTopology"`
* `pipelineTopologyDelete` 아래에서 이름을 편집합니다.
    * `"name" : "PersonZoneCrossingTopology"`

> [!Important]
> 위에서 사용된 토폴로지에는 VideoSink 리소스 `videoSink`에 대해 하드 코딩된 이름이 있습니다. 다른 비디오 원본을 선택하려면 이 값을 변경해야 합니다.

- 다음과 같이 라이브 파이프라인을 만들고 여기에서 파이프라인 토폴로지의 매개 변수를 설정합니다.

  ```json
  {
      "opName": "livePipelineSet",
      "opParams": {
          "name": "Sample-Pipeline-1",
          "properties": {
              "topologyName": "PersonZoneCrossingTopology",
              "description": "Sample pipeline description",
              "parameters": [
                  {
                      "name": "rtspUrl",
                      "value": "rtsp://rtspsim:554/media/retailshop-15fps.mkv"
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
  },
  ```

F5를 선택하여 디버그 세션을 실행하고 **TERMINAL** 지침을 따릅니다. 그러면 pipelineTopology가 설정되고, 라이브 파이프라인이 설정되고, 라이브 파이프라인이 활성화되고, 마지막으로 리소스가 삭제됩니다.

> [!Note]
> 프로그램은 라이브 파이프라인 활성화 단계에서 일시 중지됩니다. 터미널 탭을 열고 **Enter** 키를 눌러 리소스 비활성화 및 삭제 단계를 계속 진행합니다.

## <a name="interpret-results"></a>결과 해석

`spatialanalysis`는 큰 컨테이너이며 시작하는 데 최대 30초가 걸릴 수 있습니다. spatialanalysis 컨테이너가 실행되고 나면 추론 이벤트를 보내기 시작합니다. 다음과 같은 이벤트가 표시됩니다.

```JSON
[IoTHubMonitor] [3:37:28 PM] Message received from [ase03-edge/avaedge]:
{
  "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620671848135494 1 IN IP4 172.27.86.122\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/cafeteria.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.066\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/retailshop-15fps.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=Z2QAKKzZQHgCHoQAAAMABAAAAwDwPGDGWA==,aOvssiw=\r\na=control:track1\r\n"
}
[IoTHubMonitor] [3:37:30 PM] Message received from [ase03-edge/avaedge]:
{
  "type": "video",
  "location": "/videos/<your video name>",
  "startTime&quot;: &quot;2021-05-10T18:37:27.931Z"
}
[IoTHubMonitor] [3:37:40 PM] Message received from [ase03-edge/avaedge]:
{
  "state&quot;: &quot;initializing"
}
[IoTHubMonitor] [3:37:50 PM] Message received from [ase03-edge/avaedge]:
{
  "state&quot;: &quot;initializing"
}
[IoTHubMonitor] [3:38:18 PM] Message received from [ase03-edge/avaedge]:
{
  "type": "video",
  "location": "/videos/<your video name>",
  "startTime&quot;: &quot;2021-05-10T18:37:27.931Z"
}

```
> [!NOTE]
> **"초기화 중"** 메시지가 표시됩니다. 이 메시지는 spatialAnalysis 모듈이 시작되는 동안 표시되며 실행 중 상태로 전환될 때까지 최대 60초가 걸릴 수 있습니다. 잠시 기다리면 추론 이벤트 흐름이 표시됩니다.

파이프라인 토폴로지가 인스턴스화되면 "MediaSessionEstablished" 이벤트(여기서는 [샘플 MediaSessionEstablished 이벤트](detect-motion-emit-events-quickstart.md#mediasessionestablished-event))가 표시됩니다.

또한 공간 분석 모듈은 AI 인사이트 이벤트를 Azure Video Analyzer에 전송한 후 IoT Hub에 전송하며, **OUTPUT** 에도 표시됩니다. 이러한 AI 인사이트는 비디오 싱크 노드를 통해 비디오와 함께 기록됩니다. 아래 설명된 대로 Video Analyzer를 사용하여 이를 볼 수 있습니다.

## <a name="supported-spatial-analysis-operations"></a>지원되는 공간 분석 작업

다음은 `spatialAnalysis` 모듈이 제공하고 Azure Video Analyzer에서 지원되는 작업입니다.

- **personZoneCrossing**
- **personCrossingLine**
- **personDistance**
- **personCount**
- **customOperation**


다양한 작업과 해당 작업에서 지원되는 속성에 대해 자세히 알아보려면 **지원되는** **[공간 분석 작업](../spatial-analysis-operations.md)** 참조 문서를 읽어보세요.

## <a name="playing-back-the-recording"></a>녹화본 재생

Azure Portal에 로그인하여 비디오를 살펴보면 라이브 파이프라인에서 만든 Video Analyzer 비디오 리소스를 검사할 수 있습니다.

1. 웹 브라우저를 열고 [Azure Portal](https://portal.azure.com/)로 이동합니다. 자격 증명을 입력하여 포털에 로그인합니다. 기본 보기는 서비스 대시보드입니다.
1. 구독에 있는 리소스 중에서 Video Analyzer 계정을 찾아 계정 창을 엽니다.
1. **비디오 분석기** 목록에서 **비디오** 를 선택합니다.
1. 이름이 `personzonecrossing`인 비디오가 나열됩니다. 이 이름은 파이프라인 토폴로지 파일에서 선택한 이름입니다.
1. 비디오를 선택합니다.
1. 비디오 세부 정보 페이지에서 **재생** 아이콘을 클릭합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/sa-video-playback.png" alt-text="비디오 재생 스크린샷":::
   
1. 비디오의 추론 메타데이터를 보려면 **메타데이터 렌더링** 아이콘을 클릭합니다.
   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/record-stream-inference-data-with-video/bounding-box.png" alt-text="메타데이터 렌더링 아이콘":::

    비디오에서 오버레이로 볼 수 있는 3가지 옵션이 있습니다.  
      - **경계 상자**: 고유 ID를 가진 각 사람 주위에 경계 상자를 표시합니다.
      - **특성** - 가능한 경우 속도(ft/s) 및 방향(화살표 사용)과 같은 사람 특성을 표시합니다.
      - **개체 경로** - 가능한 경우 각 사람의 움직임에 대한 짧은 흔적을 표시합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/sa-video-playback-bounding-boxes.png" alt-text="경계 상자가 있는 비디오 재생 스크린샷":::

[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="next-steps"></a>다음 단계

`spatialAnalysis` 모듈이 제공하는 다양한 작업을 시도하고, 다음 pipelineTopologies를 참조하세요.

- [personCount](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-count-operation-topology.json)
- [personDistance](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-distance-operation-topology.json)
- [personCrossingLine](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-line-crossing-operation-topology.json)
- [customOperation](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/custom-operation-topology.json)
