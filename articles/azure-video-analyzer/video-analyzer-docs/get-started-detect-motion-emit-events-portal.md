---
title: Azure Portal을 사용하여 Azure Video Analyzer 시작 - Azure
description: 이 빠른 시작에서는 Azure Portal을 사용하여 Azure Video Analyzer를 시작하는 단계를 안내합니다.
ms.topic: quickstart
ms.date: 06/01/2021
ms.openlocfilehash: df63755aa66399a2f457673ba17391d5b42f31a8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734234"
---
# <a name="quickstart-get-started-with-azure-video-analyzer-in-the-azure-portal"></a>빠른 시작: Azure Portal에서 Azure Video Analyzer 시작

이 빠른 시작에서는 Azure Video Analyzer를 시작하는 단계를 안내합니다. Azure Portal을 사용하여 Azure Video Analyzer 계정과 포함 리소스를 만듭니다. 그런 다음, Video Analyzer 에지 모듈과 RTSP(실시간 스트리밍 프로토콜) 카메라 시뮬레이터 모듈을 Azure IoT Edge 디바이스에 배포합니다.

설정 단계가 완료되면 해당 스트림에서 동작을 감지하고 보고하는 파이프라인을 통해 시뮬레이션된 라이브 비디오 스트림을 실행할 수 있습니다. 다음 다이어그램은 해당 파이프라인을 그래픽으로 보여줍니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/motion-detection.svg" alt-text="동작을 감지하고 보고하는 파이프라인 다이어그램":::

## <a name="prerequisites"></a>필수 구성 요소

- 활성화된 Azure 구독. 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.

  [!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](./includes/note-account-storage-same-subscription.md)]

- 관리자 권한이 있는 IoT Edge 디바이스:
  - [IoT Edge 디바이스에 배포](deploy-iot-edge-device.md)
  - [Windows에서 Linux용 IoT Edge에 배포](deploy-iot-edge-linux-on-windows.md)
- [Visual Studio Code](https://code.visualstudio.com/)([Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 확장 포함)

[!INCLUDE [install-docker-prompt](./includes/common-includes/install-docker-prompt.md)]

## <a name="prepare-your-iot-edge-device"></a>IoT Edge 디바이스 준비

권한 없는 로컬 사용자 계정으로 IoT Edge 디바이스에서 실행되도록 Azure Video Analyzer 모듈을 구성해야 합니다. 이 모듈은 애플리케이션 구성 데이터를 저장하기 위해 특정 로컬 폴더가 필요합니다. RTSP 카메라 시뮬레이터 모듈에는 라이브 비디오 피드를 합성할 수 있는 비디오 파일이 필요합니다.

IoT Edge 디바이스에서 다음 명령을 실행합니다.

`bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"`

해당 명령의 디바이스 준비 스크립트는 입력 및 구성 폴더를 만들고, 비디오 입력 파일을 다운로드하고, 올바른 권한으로 사용자 계정을 만드는 작업을 자동화합니다. 명령이 성공적으로 완료되면 에지 디바이스에 생성된 다음 폴더가 표시됩니다.

- _/home/localedgeuser/samples_
- _/home/localedgeuser/samples/input_
- _/var/lib/videoanalyzer_
- _/var/media_

_/home/localedgeuser/samples/input\* 폴더의 비디오(_.mkv) 파일은 라이브 비디오를 시뮬레이트하는 데 사용됩니다.

## <a name="create-azure-resources"></a>Azure 리소스 만들기

다음 단계는 필요한 Azure 리소스(Video Analyzer 계정, 스토리지 계정, 사용자가 할당한 관리 ID)를 만드는 것입니다. 그런 다음, 선택적 컨테이너 레지스트리를 만들고 Video Analyzer 계정에 Video Analyzer 에지 모듈을 등록할 수 있습니다.

Azure Video Analyzer 계정을 만들 때 Azure Storage 계정을 연결해야 합니다. Video Analyzer를 사용하여 카메라에서 라이브 비디오를 녹화하는 경우 해당 데이터는 스토리지 계정의 컨테이너에 BLOB으로 저장됩니다. 다음과 같이 관리 ID를 사용하여 Video Analyzer 계정에 스토리지 계정에 대한 적절한 액세스 권한을 부여해야 합니다.

[!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](./includes/note-account-storage-same-subscription.md)]

### <a name="create-a-video-analyzer-account-in-the-azure-portal"></a>Azure Portal에서 Video Analyzer 계정 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 맨 위의 검색 창에서 **Video Analyzer** 를 입력합니다.
1. **서비스** 에서 **Video Analyzer** 를 선택합니다.
1. **추가** 를 선택합니다.
1. **Video Analyzer 계정 만들기** 섹션에서 다음 필수 값을 입력합니다.

   - **구독**: Video Analyzer 계정을 만드는 데 사용 중인 구독을 선택합니다.
   - **리소스 그룹**: Video Analyzer 계정을 만들 리소스 그룹을 선택하거나 **새로 만들기** 를 선택하여 리소스 그룹을 만듭니다.
   - **Video Analyzer 계정 이름**: Video Analyzer 계정의 이름을 입력합니다. 이름은 공백 없이 모두 소문자거나 숫자여야 하고 길이는 3~24자여야 합니다.
   - **위치**: Video Analyzer 계정을 배포할 위치를 선택합니다(예: **미국 서부 2**).
   - **스토리지 계정**: 스토리지 계정을 만듭니다. [표준 범용 v2](../../storage/common/storage-account-overview.md#types-of-storage-accounts) 스토리지 계정을 선택하는 것이 좋습니다.
   - **사용자 ID**: 사용자가 할당한 관리 ID를 새로 만들고 이름을 지정합니다.

1. 양식 맨 아래에서 **검토 + 만들기** 를 선택합니다.

### <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

1. **리소스 만들기** > **컨테이너** > **컨테이너 레지스트리** 를 선택합니다.
1. **기본 사항** 탭에서 **리소스 그룹** 과 **레지스트리 이름** 의 값을 입력합니다. 이전 섹션과 동일한 리소스 그룹을 사용합니다. 레지스트리 이름은 Azure 내에서 고유해야 하며, 5~50자의 영숫자를 포함해야 합니다.
1. 나머지 설정에 대해 기본값을 그대로 적용합니다. 그런 다음, **검토 + 만들기** 를 선택합니다. 설정을 검토한 후 **만들기** 를 선택합니다.

## <a name="deploy-edge-modules"></a>에지 모듈 배포

### <a name="deploy-the-video-analyzer-edge-module"></a>Video Analyzer 에지 모듈 배포

1. Video Analyzer 계정으로 이동합니다.
1. **에지** 창에서 **에지 모듈** 을 선택합니다.
1. **에지 모듈 추가** 를 선택하고 새 에지 모듈의 이름으로 **avaedge** 를 입력한 다음, **추가** 를 선택합니다.
1. **프로비저닝 토큰 복사** 페이지가 화면 오른쪽에 표시됩니다. **IoT 모듈 배포를 위해 권장되는 속성** 에서 다음 코드 조각을 복사합니다. 이후 단계에서 필요합니다.
   ```JSON
     {
         "applicationDataDirectory": "/var/lib/videoanalyzer",
         "ProvisioningToken": "XXXXXXX",
         "diagnosticsEventsOutputName": "diagnostics",
         "operationalEventsOutputName": "operational",
         "logLevel": "information",
         "LogCategories": "Application,Events",
         "allowUnsecuredEndpoints": true,
         "telemetryOptOut": false
     }
   ```
1. Azure IoT Hub 계정으로 이동합니다.
1. **자동 디바이스 관리** 에서 **IoT Edge** 를 선택합니다.
1. IoT Edge 디바이스의 **디바이스 ID** 값을 선택합니다.
1. **모듈 설정** 을 선택합니다.
1. **추가** 를 선택한 다음, 드롭다운 메뉴에서 **IoT Edge 모듈** 을 선택합니다.
1. **IoT Edge 모듈 이름** 으로 **avaedge** 를 입력합니다.
1. 다음 줄을 복사하여 **이미지 URI** 필드에 붙여넣습니다. `mcr.microsoft.com/media/video-analyzer:1`
1. **환경 변수** 를 선택합니다.
1. **이름** 에 **LOCAL_USER_ID** 를 입력합니다. **값** 에 **1010** 을 입력합니다.
1. **이름** 아래 두 번째 행에 **LOCAL_GROUP_ID** 를 입력합니다. **값** 에 **1010** 을 입력합니다.
1. **컨테이너 만들기 옵션** 을 선택하고 다음 줄을 복사하여 붙여넣습니다.
   ```json
   {
     "HostConfig": {
       "LogConfig": {
         "Type": "",
         "Config": {
           "max-size": "10m",
           "max-file": "10"
         }
       },
       "Binds": [
         "/var/media/:/var/media/",
         "/var/lib/videoanalyzer/:/var/lib/videoanalyzer"
       ],
       "IpcMode": "host",
       "ShmSize": 1536870912
     }
   }
   ```
1. **모듈 쌍 설정** 을 선택하고 Video Analyzer 계정의 **프로비저닝 토큰 복사** 페이지에서 이전에 복사한 코드 조각을 붙여넣습니다.
   ```JSON
     {
         "applicationDataDirectory": "/var/lib/videoanalyzer",
         "ProvisioningToken": "XXXXXXX",
         "diagnosticsEventsOutputName": "diagnostics",
         "operationalEventsOutputName": "operational",
         "logLevel": "information",
         "LogCategories": "Application,Events",
         "allowUnsecuredEndpoints": true,
         "telemetryOptOut": false
     }
   ```
1. 화면 맨 아래에서 **추가** 를 선택합니다.
1. **경로** 를 선택합니다.
1. **이름** 에 **AVAToHub** 를 입력합니다. **값** 에 `FROM /messages/modules/avaedge/outputs/* INTO $upstream`을 입력합니다.
1. **검토 + 만들기** 를 선택한 다음, **만들기** 를 선택하여 **avaedge** 에지 모듈을 배포합니다.

### <a name="deploy-the-edge-module-for-the-rtsp-camera-simulator"></a>RTSP 카메라 시뮬레이터용 에지 모듈 배포

1. IoT Hub 계정으로 이동합니다.
1. **자동 디바이스 관리** 에서 **IoT Edge** 를 선택합니다.
1. IoT Edge 디바이스의 **디바이스 ID** 값을 선택합니다.
1. **모듈 설정** 을 선택합니다.
1. **추가** 를 선택한 다음, 드롭다운 메뉴에서 **IoT Edge 모듈** 을 선택합니다.
1. **IoT Edge 모듈 이름** 으로 **rtspsim** 을 입력합니다.
1. 다음 줄을 복사하여 **이미지 URI** 필드에 붙여넣습니다. `mcr.microsoft.com/ava-utilities/rtspsim-live555:1.2`
1. **컨테이너 만들기 옵션** 을 선택하고 다음 줄을 복사하여 붙여넣습니다.
   ```json
   {
     "HostConfig": {
       "Binds": ["/home/localedgeuser/samples/input:/live/mediaServer/media"]
     }
   }
   ```
1. 화면 맨 아래에서 **추가** 를 선택합니다.
1. **검토 + 만들기** 를 선택한 다음, **만들기** 를 선택하여 **rtspsim** 에지 모듈을 배포합니다.

### <a name="verify-your-deployment"></a>배포 확인

디바이스 세부 정보 페이지에서 **avaedge** 및 **rtspsim** 모듈이 **배포에 지정됨** 과 **디바이스에서 보고됨** 둘 다로 나열되는지 확인합니다.

디바이스에서 모듈이 시작된 후 IoT Hub에 다시 보고되는 데 몇 분 정도 걸릴 수 있습니다. 업데이트된 상태를 보려면 페이지를 새로 고칩니다. 상태 코드: **200 -- OK** 는 [IoT Edge 런타임](../../iot-edge/iot-edge-runtime.md)이 정상이며 제대로 작동하고 있음을 의미합니다.

![IoT Edge 런타임의 상태 값 스크린샷](./media/deploy-iot-edge-device/status.png)

## <a name="set-up-your-development-environment"></a>개발 환경 설정

### <a name="obtain-your-iot-hub-connection-string"></a>IoT Hub 연결 문자열 가져오기

1. Azure Portal에서 IoT Hub 계정으로 이동합니다.
1. 왼쪽 창에서 **공유 액세스 정책** 을 찾아 선택합니다.
1. **iothubowner** 라는 정책을 선택합니다.
1. **기본 연결 문자열** 값을 복사합니다. `HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX`와 같이 표시됩니다.

### <a name="connect-to-iot-hub"></a>IoT Hub에 연결

1. Visual Studio Code를 열고 **보기** > **탐색기** 를 선택합니다. 또는 Ctrl+Shift+E를 선택합니다.
1. **탐색기** 탭의 왼쪽 아래 모서리에서 **Azure IoT Hub** 를 선택합니다.
1. **기타 옵션** 아이콘을 선택하여 상황에 맞는 메뉴를 표시합니다. 그런 다음, **IoT Hub 연결 문자열 설정** 을 선택합니다.
1. 입력 상자가 표시되면 IoT Hub 연결 문자열을 입력합니다.
1. 약 30초 후에 왼쪽 아래 섹션에서 Azure IoT Hub가 새로 고침됩니다. 디바이스 ID에 다음 모듈이 배포되어 있어야 합니다.

   - Video Analyzer 에지 모듈(모듈 이름 **avaedge**)
   - RTSP 시뮬레이터(모듈 이름 **rtspsim**)

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/get-started-detect-motion-emit-events/modules-node.png" alt-text="펼쳐진 모듈 노드 스크린샷":::

> [!TIP]
> 에지 디바이스(예: ARM64 디바이스)에 [Video Analyzer를 수동으로 배포](deploy-iot-edge-device.md)한 경우 해당 디바이스의 Azure IoT Hub 아래에 모듈이 표시됩니다. 해당 모듈을 선택하고 다음 단계를 계속 진행할 수 있습니다.

### <a name="prepare-to-monitor-the-modules"></a>모듈 모니터링 준비

이 빠른 시작을 사용하면 이벤트가 IoT Hub로 전송됩니다. 이러한 이벤트를 보려면 다음 단계를 수행합니다.

1. Visual Studio Code에서 **확장** 탭을 열고(또는 **Ctrl+Shift+X** 를 선택하고) Azure IoT Hub를 검색합니다.
1. IoT Hub 확장을 마우스 오른쪽 단추로 클릭하고 **확장 설정** 을 선택합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/get-started-detect-motion-emit-events/extension-settings.png" alt-text="확장 설정 선택 스크린샷":::

1. **자세한 정보 메시지 표시** 를 검색하고 사용하도록 설정합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/get-started-detect-motion-emit-events/verbose-message.png" alt-text="사용하도록 설정된 자세한 정보 메시지 표시 스크린샷":::

1. Visual Studio Code에서 **탐색기** 창을 열고 왼쪽 아래에서 **Azure IoT Hub** 를 찾습니다.
1. **디바이스** 노드를 펼칩니다.
1. 디바이스 ID를 마우스 오른쪽 단추로 클릭하고 **기본 제공 이벤트 엔드포인트 모니터링 시작** 을 선택합니다.

   > [!NOTE]
   > IoT Hub에 대한 기본 제공 엔드포인트 정보를 제공하라는 메시지가 표시될 수 있습니다. 해당 정보를 가져오려면 Azure Portal에서 IoT Hub 계정으로 이동한 다음, 왼쪽 창에서 **기본 제공 엔드포인트** 를 찾습니다. 기본 제공 엔드포인트를 선택하고 **Event Hub 호환 엔드포인트** 섹션을 찾습니다. 상자의 텍스트를 복사하여 사용합니다. 엔드포인트는 다음과 같이 표시됩니다.
   >
   > ```
   > Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
   > ```

## <a name="use-direct-method-calls"></a>직접 메서드 호출 사용

이제 Video Analyzer 에지 모듈이 노출하는 직접 메서드를 호출하여 라이브 비디오 스트림을 분석할 수 있습니다. 모듈이 제공하는 직접 메서드를 모두 검사하려면 [Video Analyzer 직접 메서드](direct-methods.md)를 참조하세요. 직접 메서드에 대한 스키마는 [여기](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/data-plane/VideoAnalyzer.Edge/preview/1.0.0/AzureVideoAnalyzerSdkDefinitions.json)에서 찾을 수 있습니다.

### <a name="enumerate-pipeline-topologies"></a>파이프라인 토폴로지 열거

이 단계를 수행하면 모듈의 모든 [파이프라인 토폴로지](pipeline.md)가 열거됩니다.

1. **avaedge** 모듈을 마우스 오른쪽 단추로 클릭하고 바로 가기 메뉴에서 **모듈 직접 메서드 호출** 을 선택합니다.
1. 편집 상자에 **pipelineTopologyList** 를 입력하고 Enter 키를 선택합니다.
1. 다음 JSON 페이로드를 복사하여 편집 상자에 붙여넣고 Enter 키를 선택합니다.

   ```json
   {
     "@apiVersion": "1.0"
   }
   ```

몇 초 이내에 **출력** 창에 다음 응답이 표시됩니다.

```
[DirectMethod] Invoking Direct Method [pipelineTopologyList] to [deviceId/avaedge] ...
[DirectMethod] Response from [deviceId/avaedge]:
{
  "status": 200,
  "payload": {
    "value": []
  }
}
```

생성된 파이프라인 토폴로지가 없으므로 해당 응답은 예상된 것입니다.

### <a name="set-a-pipeline-topology"></a>파이프라인 토폴로지 설정

앞에서 설명한 단계에 따라 `pipelineTopologySet`를 호출하면 다음 JSON을 페이로드로 사용하여 파이프라인 토폴로지를 설정할 수 있습니다. _MotionDetection_ 이라는 파이프라인 토폴로지를 만들겠습니다.

```json
{
  "@apiVersion": "1.0",
  "name": "MotionDetection",
  "properties": {
    "description": "Analyzing live video to detect motion and emit events",
    "parameters": [
      {
        "name": "rtspUrl",
        "type": "string",
        "description&quot;: &quot;rtspUrl"
      },
      {
        "name": "rtspUserName",
        "type": "string",
        "description": "rtspUserName",
        "default&quot;: &quot;dummyUserName"
      },
      {
        "name": "rtspPassword",
        "type": "string",
        "description": "rtspPassword",
        "default&quot;: &quot;dummypw"
      }
    ],
    "sources": [
      {
        "@type": "#Microsoft.VideoAnalyzer.RtspSource",
        "name": "rtspSource",
        "transport": "tcp",
        "endpoint": {
          "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
          "credentials": {
            "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
            "username": "${rtspUserName}",
            "password&quot;: &quot;${rtspPassword}"
          },
          "url&quot;: &quot;${rtspUrl}"
        }
      }
    ],
    "processors": [
      {
        "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
        "sensitivity": "medium",
        "name": "motionDetection",
        "inputs": [
          {
            "nodeName": "rtspSource",
            "outputSelectors": []
          }
        ]
      }
    ],
    "sinks": [
      {
        "hubOutputName": "inferenceOutput",
        "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
        "name": "iotHubSink",
        "inputs": [
          {
            "nodeName&quot;: &quot;motionDetection"
          }
        ]
      }
    ]
  }
}
```

이 JSON 페이로드는 세 개의 매개 변수를 정의하는 토폴로지를 만듭니다. 그중 두 개는 기본값이 있습니다. 토폴로지에는 원본 노드 1개([RTSP 원본](pipeline.md#rtsp-source)), 프로세서 노드 1개([동작 감지 프로세서](pipeline.md#motion-detection-processor)), 싱크 노드 1개([IoT Hub 메시지 싱크](pipeline.md#iot-hub-message-sink))가 있습니다. 페이로드는 토폴로지의 시각적 표현을 보여 줍니다.

몇 초 이내에 **출력** 창에 다음 응답이 표시됩니다.

```json
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:16:46.491Z",
      "lastModifiedAt": "2021-03-21T18:16:46.491Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtspPassword",
          "default": "dummypw"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description": "rtspUrl"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtspUserName",
          "default": "dummyUserName"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.VideoAnalyzer.RtspSource",
          "name": "rtspSource",
          "transport": "tcp",
          "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "medium",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "iotHubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

반환된 상태는 201입니다. 이 상태는 새 토폴로지가 만들어졌음을 나타냅니다.

다음 단계를 수행합니다.

- `pipelineTopologySet`을 다시 호출합니다. 반환된 상태 코드는 200입니다. 이 코드는 기존 토폴로지가 성공적으로 업데이트되었음을 나타냅니다.
- `pipelineTopologySet`을 다시 호출하지만 설명 문자열을 변경합니다. 반환된 상태 코드는 200이며, 설명이 새 값으로 업데이트됩니다.
- 이전 섹션에서 설명한 대로 `pipelineTopologyList`를 호출합니다. 이제 반환된 페이로드에서 _MotionDetection_ 토폴로지를 볼 수 있습니다.

### <a name="read-the-pipeline-topology"></a>파이프라인 토폴로지 읽기

다음 페이로드를 사용하여 `pipelineTopologyGet`을 호출합니다.

```json
{
  "@apiVersion": "1.0",
  "name": "MotionDetection"
}
```

몇 초 이내에 **출력** 창에 다음 응답이 표시됩니다.

```json
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:16:46.491Z",
      "lastModifiedAt": "2021-03-21T18:16:46.491Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtspPassword",
          "default": "dummypw"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description": "rtspUrl"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtspUserName",
          "default": "dummyUserName"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.VideoAnalyzer.RtspSource",
          "name": "rtspSource",
          "transport": "tcp",
          "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "medium",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "iotHubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

응답 페이로드에서 다음 세부 정보를 확인합니다.

- 상태 코드가 200이며, 성공을 나타냅니다.
- 페이로드에는 `createdAt` 타임스탬프 및 `lastModifiedAt` 타임스탬프가 포함됩니다.

### <a name="create-a-live-pipeline-by-using-the-topology"></a>토폴로지를 사용하여 라이브 파이프라인 만들기

다음으로, 앞의 파이프라인 토폴로지를 참조하는 라이브 파이프라인을 만듭니다. 다음 페이로드를 사용하여 `livePipelineSet` 직접 메서드를 호출합니다.

```json
{
  "@apiVersion": "1.0",
  "name": "mdpipeline1",
  "properties": {
    "topologyName": "MotionDetection",
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

이 페이로드는 다음과 같습니다.

- 라이브 파이프라인에서 사용되는 토폴로지(_MotionDetection_)를 지정합니다.
- 토폴로지 페이로드에 기본값이 없는 `rtspUrl`의 매개 변수 값이 포함됩니다. 이 값은 다음 샘플 비디오에 대한 링크입니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LTY4]

몇 초 이내에 **출력** 창에 다음 응답이 표시됩니다.

```json
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:27:41.639Z",
      "lastModifiedAt": "2021-03-21T18:27:41.639Z"
    },
    "name": "mdpipeline1",
    "properties": {
      "state": "Inactive",
      "description": "Sample pipeline description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspPassword",
          "value": "testpassword"
        },
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        }
      ]
    }
  }
}
```

응답 페이로드에서 다음을 확인합니다.

- 상태 코드가 201이며, 새 라이브 파이프라인이 생성되었음을 나타냅니다.
- 상태가 `Inactive`이며, 라이브 파이프라인이 생성되었지만 활성화되지 않은 상태임을 나타냅니다. 자세한 내용은 [파이프라인 상태](pipeline.md#pipeline-states)를 참조하세요.

다음 단계로 아래의 직접 메서드를 사용해 보세요.

- 동일한 페이로드를 사용하여 `livePipelineSet`를 다시 호출합니다. 이제 반환된 상태 코드가 200입니다.
- `livePipelineSet`를 다시 호출하되 다른 설명을 사용합니다. 응답 페이로드에서 라이브 파이프라인이 성공적으로 업데이트되었음을 나타내는 업데이트된 설명을 확인합니다.
- `livePipelineSet`를 호출하되 이름을 `mdpipeline2`로 변경하고 `rtspUrl`을 `rtsp://rtspsim:554/media/lots_015.mkv`로 변경합니다. 응답 페이로드에서 새로 만든(즉, 상태 코드가 201인) 라이브 파이프라인을 확인합니다.

  > [!NOTE]
  > [파이프라인 토폴로지](pipeline.md#pipeline-topologies)에서 설명했듯이, 여러 라이브 파이프라인을 만들고 동일한 파이프라인 토폴로지를 사용하여 여러 카메라의 라이브 비디오 스트림을 분석할 수 있습니다. 추가 라이브 파이프라인을 만드는 경우 정리 단계에서 삭제해야 합니다.

### <a name="activate-the-live-pipeline"></a>라이브 파이프라인 활성화

파이프라인을 통해 (시뮬레이트된) 라이브 비디오 흐름을 시작하는 라이브 파이프라인을 활성화할 수 있습니다. 다음 페이로드를 사용하여 `livePipelineActivate` 직접 메서드를 호출합니다.

```json
{
  "@apiVersion": "1.0",
  "name": "mdpipeline1"
}
```

몇 초 이내에 **출력** 창에 다음 응답이 표시됩니다.

```json
{
  "status": 200,
  "payload": null
}
```

상태 코드 200은 라이브 파이프라인이 성공적으로 활성화되었음을 나타냅니다.

### <a name="check-the-state-of-the-live-pipeline"></a>라이브 파이프라인의 상태 확인

다음 페이로드를 사용하여 `livePipelineGet` 직접 메서드를 호출합니다.

```json
{
  "@apiVersion": "1.0",
  "name": "mdpipeline1"
}
```

몇 초 이내에 **출력** 창에 다음 응답이 표시됩니다.

```json
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:27:41.639Z",
      "lastModifiedAt": "2021-03-21T18:27:41.639Z"
    },
    "name": "mdpipeline1",
    "properties": {
      "state": "Active",
      "description": "Sample pipeline description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspPassword",
          "value": "testpassword"
        },
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        }
      ]
    }
  }
}
```

응답 페이로드에서 다음 세부 정보를 확인합니다.

- 상태 코드가 200이며, 성공을 나타냅니다.
- 상태가 `Active`이며, 라이브 파이프라인이 현재 활성 상태임을 나타냅니다.

## <a name="observe-results"></a>결과 확인

만들고 활성화한 라이브 파이프라인은 동작 감지 프로세서 노드를 사용하여 들어오는 라이브 비디오 스트림에서 동작을 감지하고 IoT Hub 싱크로 이벤트를 보냅니다. 이벤트가 IoT Hub에 메시지로 릴레이되면 이제 확인할 수 있습니다. **출력** 창의 메시지에 다음 “본문”이 포함됩니다.

```json
{
  "timestamp": 145471641211899,
  "inferences": [
    {
      "type": "motion",
      "motion": {
        "box": {
          "l": 0.514644,
          "t": 0.574627,
          "w": 0.3375,
          "h": 0.096296
        }
      }
    }
  ]
}
```

`inferences` 섹션은 유형이 동작임을 나타냅니다. 동작 이벤트에 대한 추가 데이터를 제공합니다. 또한 동작이 감지된 비디오 프레임 영역(지정된 타임스탬프)에 대한 경계 상자를 제공합니다.

## <a name="invoke-more-direct-method-calls-to-clean-up"></a>추가 직접 메서드를 호출하여 정리

다음으로, 직접 메서드를 호출하여 라이브 파이프라인을 비활성화하고 삭제할 수 있습니다(순서대로).

### <a name="deactivate-the-live-pipeline"></a>라이브 파이프라인 비활성화

다음 페이로드를 사용하여 `livePipelineDeactivate` 직접 메서드를 호출합니다.

```json
{
  "@apiVersion": "1.0",
  "name": "mdpipeline1"
}
```

몇 초 이내에 **출력** 창에 다음 응답이 표시됩니다.

```json
{
  "status": 200,
  "payload": null
}
```

상태 코드 200은 라이브 파이프라인이 성공적으로 비활성화되었음을 나타냅니다.

다음으로, 이 문서의 앞에서 설명한 대로 `livePipelineGet`을 호출합니다. 상태 값을 확인합니다.

### <a name="delete-the-live-pipeline"></a>라이브 파이프라인 삭제

다음 페이로드를 사용하여 `livePipelineDelete` 직접 메서드를 호출합니다.

```json
{
  "@apiVersion": "1.0",
  "name": "mdpipeline1"
}
```

몇 초 이내에 **출력** 창에 다음 응답이 표시됩니다.

```json
{
  "status": 200,
  "payload": null
}
```

상태 코드 200은 라이브 파이프라인이 성공적으로 삭제되었음을 나타냅니다.

_mdpipeline2_ 라는 파이프라인도 만든 경우 이 추가 파이프라인도 삭제해야 파이프라인 토폴로지를 삭제할 수 있습니다. 다음 페이로드를 사용하여 `livePipelineDelete` 직접 메서드를 다시 호출합니다.

```
{
    "@apiVersion" : "1.0",
    "name" : "mdpipeline2"
}
```

몇 초 이내에 **출력** 창에 다음 응답이 표시됩니다.

```json
{
  "status": 200,
  "payload": null
}
```

상태 코드 200은 라이브 파이프라인이 성공적으로 삭제되었음을 나타냅니다.

`pipelineTopologyList`와 동일한 페이로드를 사용하여 `livePipelineList`를 호출할 수 있습니다. 라이브 파이프라인이 열거되지 않는지 관찰합니다.

### <a name="delete-the-pipeline-topology"></a>파이프라인 토폴로지 삭제

모든 라이브 파이프라인을 삭제한 후에는 다음 페이로드를 사용하여 `pipelineTopologyDelete` 직접 메서드를 호출할 수 있습니다.

```json
{
  "@apiVersion": "1.0",
  "name": "MotionDetection"
}
```

몇 초 이내에 **출력** 창에 다음 응답이 표시됩니다.

```json
{
  "status": 200,
  "payload": null
}
```

상태 코드 200은 토폴로지가 성공적으로 삭제되었음을 나타냅니다.

`pipelineTopologyList`를 호출하여 모듈에 토폴로지가 포함되지 않은 것을 확인할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

- [빠른 시작: 동작이 감지되면 클라우드에 비디오 녹화](detect-motion-record-video-clips-cloud.md)를 시도합니다.
- [빠른 시작: 라이브 비디오 분석](analyze-live-video-use-your-model-http.md)을 시도합니다.
- [진단 메시지](monitor-log-edge.md)에 대해 자세히 알아봅니다.
