---
title: Azure Video Analyzer 시작 - Azure
description: 이 빠른 시작에서는 Azure Video Analyzer를 시작하는 단계를 안내합니다. Azure VM을 IoT Edge 디바이스와 시뮬레이션된 라이브 비디오 스트림으로 사용합니다.
ms.service: azure-video-analyzer
ms.topic: quickstart
ms.date: 06/01/2021
ms.openlocfilehash: 001b1bd2e1f2f3350ad9e9cef28bb3ec682332ef
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748680"
---
# <a name="quickstart-get-started-with-azure-video-analyzer"></a>빠른 시작: Azure Video Analyzer 시작

이 빠른 시작에서는 Azure Video Analyzer를 시작하는 단계를 안내합니다. Azure VM을 IoT Edge 디바이스와 시뮬레이션된 라이브 비디오 스트림으로 사용합니다.

설치 단계가 완료되면 해당 스트림에서 동작을 감지하고 보고하는 파이프라인을 통해 시뮬레이션된 라이브 비디오 스트림을 실행할 수 있습니다. 다음 다이어그램은 해당 파이프라인을 그래픽으로 보여줍니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/motion-detection.svg" alt-text="동작 감지":::

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 수 있습니다.

    [!INCLUDE [azure-subscription-permissions](./includes/common-includes/azure-subscription-permissions.md)]
* 다음 확장이 포함된 [Visual Studio Code](https://code.visualstudio.com/)

    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

[!INCLUDE [install-docker-prompt](./includes/common-includes/install-docker-prompt.md)]

## <a name="set-up-azure-resources"></a>Azure 리소스 설정

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

배포 프로세스는 약 **20분** 이 걸립니다. 배포 프로세스가 완료되면 Azure 구독에 다음을 비롯한 특정 Azure 리소스가 배포됩니다.
1. **Video Analyzer 계정** - 이 [클라우드 서비스](overview.md)는 Video Analyzer 에지 모듈을 등록하고 녹화된 비디오 및 비디오 분석을 재생하는 데 사용됩니다.
1. **스토리지 계정** - 녹화된 비디오 및 비디오 분석을 저장하는 데 사용됩니다.
1. **관리 ID** - 위의 스토리지 계정에 대한 액세스를 관리하는 데 사용되는 사용자 할당 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)입니다.
1. **가상 머신** - 시뮬레이션된 에지 디바이스로 작동할 가상 머신입니다.
1. **IoT Hub** - IoT 애플리케이션, IoT Edge 및 관리하는 디바이스 간의 양방향 통신을 위한 중앙 메시지 허브 역할을 합니다.

<!-- TODO: provide a link to the readme.md in github.com/azure-video-analyzer/setup/readme.md where we can list out all resources like virtual network etc. -->

## <a name="set-up-your-development-environment"></a>개발 환경 설정

### <a name="obtain-your-iot-hub-connection-string"></a>IoT Hub 연결 문자열 가져오기

1. Azure Portal에서, 위의 설정 단계에서 만든 IoT Hub로 이동합니다.
1. 왼쪽 탐색 영역에서 **공유 액세스 정책** 옵션을 찾아 클릭합니다.
1. **iothubowner** 라는 정책을 클릭합니다.
1. **기본 연결 문자열** 을 복사합니다. `HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX` 형식입니다.

### <a name="connect-to-the-iot-hub"></a>IoT Hub에 연결

1. Visual Studio Code를 열고 **보기** > **탐색기** 를 선택합니다. 또는 Ctrl+Shift+E를 선택합니다.
1. **탐색기** 탭의 왼쪽 아래 모서리에서 **Azure IoT Hub** 를 선택합니다.
1. **기타 옵션** 아이콘을 선택하여 상황에 맞는 메뉴를 표시합니다. 그런 다음, **IoT Hub 연결 문자열 설정** 을 선택합니다.
1. 입력 상자가 표시되면 IoT Hub 연결 문자열을 입력합니다.
1. 약 30초 후에 왼쪽 아래 섹션에서 Azure IoT Hub가 새로 고침됩니다. 에지 디바이스 `avasample-iot-edge-device`에 다음 모듈이 배포되었을 것입니다.
    * Video Analyzer 에지 모듈(모듈 이름 **avaedge**)
    * RTSP 시뮬레이터(모듈 이름 **rtspsim**)


> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/modules-node.png" alt-text="모듈 노드 확장":::

> [!TIP]
> 에지 디바이스(예: ARM64 디바이스)에 [Video Analyzer를 수동으로 배포](deploy-iot-edge-device.md)한 경우 해당 디바이스의 Azure IoT Hub 아래에 모듈이 표시됩니다. 해당 모듈을 선택하고 아래의 나머지 단계를 수행할 수 있습니다.

### <a name="prepare-to-monitor-the-modules"></a>모듈 모니터링 준비 

이 빠른 시작 실행을 사용하면 이벤트가 IoT Hub로 전송됩니다. 이러한 이벤트를 보려면 다음 단계를 수행합니다.

1. Visual Studio Code에서 **확장** 탭을 열고(또는 Ctrl+Shift+X를 누르고) **Azure IoT Hub** 를 검색합니다.
1. 마우스 오른쪽 단추를 클릭하고 **확장 설정** 을 선택합니다.
 
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/get-started-detect-motion-emit-events/extension-settings.png" alt-text="확장 설정 선택":::
1. "자세한 정보 메시지 표시"를 검색하고 활성화합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/get-started-detect-motion-emit-events/verbose-message.png" alt-text="자세한 정보 메시지 표시":::
1. Visual Studio Code에서 탐색기 창을 열고, 왼쪽 아래 모서리에서 **Azure IoT Hub** 를 찾습니다.
1. **디바이스** 노드를 펼칩니다.
1. `avasample-iot-edge-device`를 마우스 오른쪽 단추로 클릭하고, **기본 제공 이벤트 엔드포인트 모니터링 시작** 을 선택합니다.

    [!INCLUDE [provide-builtin-endpoint](./includes/common-includes/provide-builtin-endpoint.md)]

## <a name="use-direct-method-calls"></a>직접 메서드 호출 사용

이제 Video Analyzer 에지 모듈이 노출하는 직접 메서드를 호출하여 라이브 비디오 스트림을 분석할 수 있습니다. 모듈이 제공하는 직접 메서드를 모두 검사하려면 [Video Analyzer 직접 메서드](direct-methods.md)를 참조하세요. 직접 메서드에 대한 스키마는 [여기](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/data-plane/VideoAnalyzer.Edge/preview/1.0.0/AzureVideoAnalyzerSdkDefinitions.json)에서 찾을 수 있습니다.

### <a name="enumerate-pipeline-topologies"></a>파이프라인 토폴로지 열거

이 단계를 수행하면 모듈의 모든 [파이프라인 토폴로지](pipeline.md)가 열거됩니다.

1. "avaedge" 모듈을 마우스 오른쪽 단추로 클릭하고 바로 가기 메뉴에서 **모듈 직접 메서드 호출** 을 선택합니다.
1. Visual Studio Code 창의 상단 가운데에 편집 상자 팝업 창이 표시됩니다. 편집 상자에 `pipelineTopologyList`를 입력하고 Enter 키를 누릅니다.
1. 그런 다음, 편집 상자에 아래 JSON 페이로드를 복사하여 붙여넣고 Enter 키를 누릅니다.
   
```json
{
    "@apiVersion" : "1.0"
}
```

몇 초 이내에 출력 창에 다음 응답이 표시됩니다.
    
```
[DirectMethod] Invoking Direct Method [pipelineTopologyList] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": {
    "value": []
  }
}
```

생성된 파이프라인 토폴로지가 없으므로 위의 응답은 올바른 것입니다.

### <a name="set-a-pipeline-topology"></a>파이프라인 토폴로지 설정

위의 단계에 따라 `pipelineTopologySet`를 호출하고 JSON을 페이로드로 사용하여 다음 파이프라인 토폴로지를 설정할 수 있습니다. "MotionDetection"이라는 파이프라인 토폴로지를 만들겠습니다.


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

이 JSON 페이로드는 세 개의 매개 변수를 정의하는 토폴로지를 만듭니다. 그 중 두 개는 기본값이 있습니다. 토폴로지에는 원본 노드 하나([RTSP 원본](pipeline.md#rtsp-source)), 프로세서 노드 하나([동작 감지 프로세서](pipeline.md#motion-detection-processor)), 싱크 노드 하나([IoT Hub 메시지 싱크](pipeline.md#iot-hub-message-sink))가 있습니다. 토폴로지의 시각적 표현은 위에 나와 있습니다.

몇 초 안에 **출력** 창에 다음 응답이 표시됩니다.

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

1. `pipelineTopologySet`을 다시 호출합니다. 반환된 상태 코드는 200입니다. 이 코드는 기존 토폴로지가 성공적으로 업데이트되었음을 나타냅니다.
1. `pipelineTopologySet`을 다시 호출하지만 설명 문자열을 변경합니다. 반환된 상태 코드는 200이며, 설명이 새 값으로 업데이트됩니다.
1. 이전 섹션에서 설명한 대로 `pipelineTopologyList`를 호출합니다. 이제 반환된 페이로드에서 "MotionDetection" 토폴로지를 볼 수 있습니다.

### <a name="read-the-pipeline-topology"></a>파이프라인 토폴로지 읽기

다음 페이로드를 사용하여 `pipelineTopologyGet`을 호출합니다.

```json
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

몇 초 안에 **출력** 창에 다음 응답이 표시됩니다.

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

* 상태 코드가 200이며, 성공을 나타냅니다.
* 페이로드에는 `createdAt` 타임스탬프 및 `lastModifiedAt` 타임스탬프가 포함됩니다.

### <a name="create-a-live-pipeline-using-the-topology"></a>토폴로지를 사용하여 라이브 파이프라인 만들기

다음으로, 위의 파이프라인 토폴로지를 참조하는 라이브 파이프라인을 만듭니다. 다음 페이로드를 사용하여 `livePipelineSet` 직접 메서드를 호출합니다.

```json
{
    "@apiVersion" : "1.0",
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

* 위의 페이로드는 라이브 파이프라인에서 사용할 토폴로지("MotionDetection")를 지정합니다.
* 이 페이로드에는 토폴로지 페이로드에 기본값이 없는 `rtspUrl`의 매개 변수 값이 포함되어 있습니다. 이 값은 아래 샘플 비디오에 대한 링크입니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LTY4]


몇 초 안에 **출력** 창에 다음 응답이 표시됩니다.

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

* 상태 코드가 201입니다. 이 코드는 새 라이브 파이프라인이 생성되었음을 의미합니다.
* 상태가 "비활성"입니다. 이 상태는 라이브 파이프라인이 생성되었지만 활성화되지 않았음을 의미합니다. 자세한 내용은 [파이프라인 상태](pipeline.md#pipeline-states)를 참조하세요.

다음 단계로 아래의 직접 메서드를 사용해 보세요.

* 동일한 페이로드를 사용하여 `livePipelineSet`를 다시 호출하고 반환된 상태 코드가 200인지 확인합니다.
* 다른 설명을 사용하여 `livePipelineSet`를 다시 호출하고 응답 페이로드에 라이브 파이프라인이 성공적으로 업데이트되었음을 나타내는 업데이트된 설명이 있는지 확인합니다.
* `livePipelineSet`를 호출하되, 이름을 "mdpipeline2"로 변경하고 `rtspUrl`을 "rtsp://rtspsim:554/media/lots_015.mkv"로 변경합니다. 응답 페이로드에서 새로 만든(즉, 상태 코드가 201인) 라이브 파이프라인을 확인합니다.
    > [!NOTE]
    > [파이프라인 토폴로지](pipeline.md#pipeline-topologies)에서 설명했듯이, 여러 라이브 파이프라인을 만들고 동일한 파이프라인 토폴로지를 사용하여 여러 카메라의 라이브 비디오 스트림을 분석할 수 있습니다. 추가 라이브 파이프라인을 만드는 경우 정리 단계에서 삭제해야 합니다.

### <a name="activate-the-live-pipeline"></a>라이브 파이프라인 활성화

다음으로, 파이프라인을 통해 (시뮬레이션된) 라이브 비디오 흐름을 시작하는 라이브 파이프라인을 활성화할 수 있습니다. 다음 페이로드를 사용하여 `livePipelineActivate` 직접 메서드를 호출합니다.

```json
{
    "@apiVersion" : "1.0",
    "name" : "mdpipeline1"
}
```

몇 초 안에 출력 창에 다음 응답이 표시됩니다.

```json
{
  "status": 200,
  "payload": null
}
```

상태 코드 200은 라이브 파이프라인이 성공적으로 활성화되었음을 나타냅니다.

### <a name="check-the-state-of-the-live-pipeline"></a>라이브 파이프라인의 상태 확인

이제 다음 페이로드를 사용하여 `livePipelineGet` 직접 메서드를 호출합니다.

```json
{
    "@apiVersion" : "1.0",
    "name" : "mdpipeline1"
}
```

몇 초 안에 출력 창에 다음 응답이 표시됩니다.

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

* 상태 코드가 200이며, 성공을 나타냅니다.
* 상태가 "활성"입니다. 이는 라이브 파이프라인이 현재 "활성" 상태임을 나타냅니다.

## <a name="observe-results"></a>결과 확인

위에서 만들고 활성화한 라이브 파이프라인은 동작 감지 프로세서 노드를 사용하여 들어오는 라이브 비디오 스트림에서 동작을 감지하고 IoT Hub 싱크로 이벤트를 보냅니다. 그런 다음, 이러한 이벤트가 IoT Hub에 메시지로 릴레이되면 확인할 수 있습니다. 출력 창에 다음과 같은 "본문"이 포함된 메시지가 표시됩니다.


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

세부 정보를 잘 보세요.

*   유추 섹션은 형식이 동작임을 나타냅니다. 동작 이벤트에 대한 추가 데이터를 제공하며, 동작이 감지된 비디오 프레임의 영역(지정된 타임스탬프)에 대한 경계 상자를 제공합니다.

    
## <a name="invoke-additional-direct-method-calls-to-clean-up"></a>추가 직접 메서드 호출을 호출하여 정리

다음으로, 직접 메서드를 호출하여 라이브 파이프라인을 비활성화하고 삭제할 수 있습니다(순서대로).

### <a name="deactivate-the-live-pipeline"></a>라이브 파이프라인 비활성화

다음 페이로드를 사용하여 `livePipelineDeactivate` 직접 메서드를 호출합니다.

```json
{
    "@apiVersion" : "1.0",
    "name" : "mdpipeline1"
}
```

몇 초 안에 **출력** 창에 다음 응답이 표시됩니다.

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
    "@apiVersion" : "1.0",
    "name" : "mdpipeline1"
}
```

몇 초 안에 **출력** 창에 다음 응답이 표시됩니다.

```json
{
  "status": 200,
  "payload": null
}
```
상태 코드 200은 라이브 파이프라인이 성공적으로 삭제되었음을 나타냅니다.

"mdpipeline2"라는 파이프라인도 만든 경우 이 추가 파이프라인을 삭제하지 않고는 파이프라인 토폴로지를 삭제할 수 없습니다. 다음 페이로드를 사용하여 `livePipelineDelete` 직접 메서드를 다시 호출합니다.

```
{
    "@apiVersion" : "1.0",
    "name" : "mdpipeline2"
}
```

몇 초 안에 출력 창에 다음 응답이 표시됩니다.

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
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

몇 초 안에 **출력** 창에 다음 응답이 표시됩니다.

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

* [빠른 시작: 동작이 감지되면 클라우드에 비디오 녹화](detect-motion-record-video-clips-cloud.md) 시도
* [빠른 시작: 라이브 비디오 분석](analyze-live-video-use-your-model-http.md) 시도
* [진단 메시지](monitor-log-edge.md)에 대해 자세히 알아보기 
