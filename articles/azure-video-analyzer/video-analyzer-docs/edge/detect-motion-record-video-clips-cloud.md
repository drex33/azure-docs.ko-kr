---
title: Azure Video Analyzer를 사용하여 동작을 감지하고 비디오 녹화
description: 이 빠른 시작에서는 Azure Video Analyzer 에지 모듈을 사용하여 라이브 비디오 스트림에서 동작을 감지하고 Video Analyzer 계정에 비디오를 녹화하는 방법을 보여줍니다.
ms.topic: quickstart
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 48c41b812a4cdbd416d0dd146aba938cce419864
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484193"
---
# <a name="quickstart-detect-motion-record-video-to-video-analyzer"></a>빠른 시작: 동작을 감지하고 Video Analyzer에 비디오 녹화

[!INCLUDE [header](includes/edge-env.md)]

이 문서에서는 Azure Video Analyzer 에지 모듈을 [이벤트 기반 기록](../event-based-video-recording-concept.md)에 사용하는 단계를 안내합니다. Azure의 Linux VM이 IoT Edge 디바이스이자 시뮬레이션된 라이브 비디오 스트림으로 사용됩니다. 이 비디오 스트림을 분석하여 움직이는 물체가 있는지 확인합니다. 동작이 감지되면 이벤트가 Azure IoT Hub로 전송되고, 비디오 스트림의 관련 부분이 Video Analyzer 계정에 [비디오 리소스](../terminology.md#video)로 기록됩니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

    [!INCLUDE [azure-subscription-permissions](./includes/common-includes/azure-subscription-permissions.md)]
* 다음 확장이 포함된 [Visual Studio Code](https://code.visualstudio.com/)
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="set-up-azure-resources"></a>Azure 리소스 설정

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

배포 프로세스는 약 **20분** 이 걸립니다. 배포 프로세스가 완료되면 Azure 구독에 다음을 비롯한 특정 Azure 리소스가 배포됩니다.
1. **Video Analyzer 계정** - 이 [클라우드 서비스](../overview.md)는 Video Analyzer 에지 모듈을 등록하고 녹화된 비디오 및 비디오 분석을 재생하는 데 사용됩니다.
1. **스토리지 계정** - 녹화된 비디오 및 비디오 분석을 저장하는 데 사용됩니다.
1. **관리 ID** - 사용자가 할당한 [관리 ID]../../../active-directory/managed-identities-azure-resources/overview.md)로, 위의 스토리지 계정에 대한 액세스를 관리하는 데 사용됩니다.
1. **가상 머신** - 시뮬레이션된 에지 디바이스로 작동할 가상 머신입니다.
1. **IoT Hub** - IoT 애플리케이션, IoT Edge 및 관리하는 디바이스 간의 양방향 통신을 위한 중앙 메시지 허브 역할을 합니다.

자세한 내용은 [여기](https://github.com/Azure/video-analyzer/tree/main/setup)서 확인할 수 있습니다.

## <a name="review-the-sample-video"></a>샘플 비디오 검토

위의 배포에서 만든 가상 머신에는 여러 MKV 파일이 있습니다.  이러한 파일 중 하나는 `lots_015.mkv`입니다. 다음 단계에서는 이 비디오 파일을 사용하여 이 자습서의 라이브 스트림을 시뮬레이션하겠습니다.

[VLC Player](https://www.videolan.org/vlc/)와 같은 애플리케이션을 사용하여 시작하고, `Ctrl+N`을 누르고, [주차장 비디오 샘플](https://avamedia.blob.core.windows.net/public/lots_015.mkv) 링크를 붙여넣어 재생을 시작할 수 있습니다. 5초 마크 정도에 흰색 차가 주차장을 통과합니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

아래 단계를 완료하면 Video Analyzer 에지 모듈을 사용하여 자동차의 동작을 감지하고 약 5초 마크에서 시작되는 비디오 클립을 녹화할 수 있습니다. 

아래 다이어그램은 전체 흐름의 시각적 표현입니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/detect-motion-record-video-clips-cloud/topology.svg" alt-text="동작 이벤트를 기반으로 비디오 리소스에 이벤트 기반 비디오 녹화":::

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

## <a name="use-direct-method-calls-to-analyze-live-video"></a>직접 메서드 호출을 사용하여 라이브 비디오 분석

이제 Video Analyzer 에지 모듈이 노출하는 직접 메서드를 호출하여 라이브 비디오 스트림을 분석할 수 있습니다. 모듈이 제공하는 직접 메서드를 모두 검사하려면 [Video Analyzer 직접 메서드](direct-methods.md)를 참조하세요.

### <a name="enumerate-pipeline-topologies"></a>파이프라인 토폴로지 열거

이 단계를 수행하면 모듈의 모든 [파이프라인 토폴로지](../pipeline.md)가 열거됩니다.

1. "avaedge" 모듈을 마우스 오른쪽 단추로 클릭하고 바로 가기 메뉴에서 **모듈 직접 메서드 호출** 을 선택합니다.
1. Visual Studio Code 창의 상단 가운데에 편집 상자 팝업 창이 표시됩니다. 편집 상자에 "pipelineTopologyList"를 입력하고 Enter 키를 누릅니다.
1. 그런 다음, 편집 상자에 아래 JSON 페이로드를 복사하여 붙여넣고 Enter 키를 누릅니다.
   
```json
{
    "@apiVersion" : "1.1"
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

위의 단계에 따라 `pipelineTopologySet`를 호출하고 JSON을 페이로드로 사용하여 다음 파이프라인 토폴로지를 설정할 수 있습니다. "EVRtoVideoSinkOnMotionDetection"이라는 파이프라인 토폴로지를 만들겠습니다.

> [!NOTE]
> 아래 페이로드에서 `videoName` 속성은 Video Analyzer 계정에 생성되는 비디오 리소스의 이름이 되는 "sample-motion-video-camera001"로 설정됩니다. 이 리소스 이름은 녹화하는 각 라이브 비디오 원본에 대해 고유해야 합니다. 이름이 고유할 수 있도록 아래의 `videoName` 속성을 필요한 대로 편집해야 합니다.

```
{
  "@apiVersion": "1.1",
  "name": "EVRtoVideoSinkOnMotionDetection",
  "properties": {
    "description": "Event-based video recording to Video Sink based on motion events",
    "parameters": [
      {
        "name": "rtspUserName",
        "type": "String",
        "description": "rtsp source user name.",
        "default&quot;: &quot;dummyUserName"
      },
      {
        "name": "rtspPassword",
        "type": "String",
        "description": "rtsp source password.",
        "default&quot;: &quot;dummyPassword"
      },
      {
        "name": "rtspUrl",
        "type": "String",
        "description&quot;: &quot;rtsp Url"
      },
      {
        "name": "motionSensitivity",
        "type": "String",
        "description": "motion detection sensitivity",
        "default&quot;: &quot;medium"
      },
      {
        "name": "hubSinkOutputName",
        "type": "String",
        "description": "hub sink output name",
        "default&quot;: &quot;inferenceOutput"
      }
    ],
    "sources": [
      {
        "@type": "#Microsoft.VideoAnalyzer.RtspSource",
        "name": "rtspSource",
        "endpoint": {
          "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
          "url": "${rtspUrl}",
          "credentials": {
            "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
            "username": "${rtspUserName}",
            "password&quot;: &quot;${rtspPassword}"
          }
        }
      }
    ],
    "processors": [
      {
        "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
        "name": "motionDetection",
        "sensitivity": "${motionSensitivity}",
        "inputs": [
          {
            "nodeName": "rtspSource",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value&quot;: &quot;video"
              }
            ]
          }
        ]
      },
      {
        "@type": "#Microsoft.VideoAnalyzer.SignalGateProcessor",
        "name": "signalGateProcessor",
        "inputs": [
          {
            "nodeName&quot;: &quot;motionDetection"
          },
          {
            "nodeName": "rtspSource",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value&quot;: &quot;video"
              }
            ]
          }
        ],
        "activationEvaluationWindow": "PT1S",
        "activationSignalOffset": "PT0S",
        "minimumActivationTime": "PT30S",
        "maximumActivationTime&quot;: &quot;PT30S"
      }
    ],
    "sinks": [
      {
        "@type": "#Microsoft.VideoAnalyzer.VideoSink",
        "name": "videoSink",
        "videoName": "sample-motion-video-camera001",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value&quot;: &quot;video"
              }
            ]
          }
        ],
        "videoCreationProperties": {
          "title": "sample-motion-video-camera001",
          "description": "Motion-detection based recording of clips to a video resource",
          "segmentLength&quot;: &quot;PT30S"
        },
        "localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
        "localMediaCacheMaximumSizeMiB&quot;: &quot;2048"
      },
      {
        "@type": "#Microsoft.VideoAnalyzer.IoTHubMessageSink",
        "name": "hubSink",
        "hubOutputName": "${hubSinkOutputName}",
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

위의 JSON 페이로드는 5개의 매개 변수(그 중 4개는 기본값이 있음)를 정의하는 그래프 토폴로지를 생성합니다. 토폴로지에는 원본 노드([RTSP 원본](../pipeline.md#rtsp-source)) 1개, 프로세서 노드 2개([동작 감지 프로세서](../pipeline.md#motion-detection-processor) 및 [신호 게이트 프로세서](../pipeline.md#signal-gate-processor)), 싱크 노드 2개(IoT Hub 싱크 및 [비디오 싱크](../pipeline.md#video-sink))가 있습니다. 토폴로지의 시각적 표현은 위에 나와 있습니다.

몇 초 이내에 **출력** 창에 다음 응답이 표시됩니다.

```
[DirectMethod] Invoking Direct Method [pipelineTopologySet] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-05-03T15:17:53.483Z",
      "lastModifiedAt&quot;: &quot;2021-05-03T15:17:53.483Z"
    },
    "name": "EVRtoVideoSinkOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Video Sink based on motion events",
      "parameters": [
        {
          "name": "hubSinkOutputName",
          "type": "string",
          "description": "hub sink output name",
          "default&quot;: &quot;inferenceOutput"
        },
        {
          "name": "motionSensitivity",
          "type": "string",
          "description": "motion detection sensitivity",
          "default&quot;: &quot;medium"
        },
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtsp source password.",
          "default&quot;: &quot;dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description&quot;: &quot;rtsp Url"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtsp source user name.",
          "default&quot;: &quot;dummyUserName"
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
              "password&quot;: &quot;${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        },
        {
          "@type": "#Microsoft.VideoAnalyzer.SignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.VideoSink",
          "localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "videoName": "sample-motion-video-camera001",
          "videoCreationProperties": {
            "title": "sample-motion-video-camera001",
            "description": "Motion-detection based recording of clips to a video resource",
            "segmentLength&quot;: &quot;PT30S"
          },
          "name": "videoSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        },
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
          "name": "hubSink",
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



새 파이프라인 토폴로지가 생성되었음을 나타내는 상태 코드 201이 반환됩니다. 다음 단계로 아래의 직접 메서드를 사용해 보세요.

* `pipelineTopologySet`를 다시 호출하고 상태 코드 200이 반환되는지 확인합니다. 상태 코드 200은 기존 파이프라인 토폴로지가 성공적으로 업데이트되었음을 나타냅니다.
* `pipelineTopologySet`를 다시 호출하되, 설명 문자열을 변경합니다. 응답의 상태 코드가 200이고 설명이 새 값으로 업데이트되었는지 확인합니다.
* 이전 단계에서 설명한 대로 `pipelineTopologyList`를 호출하고, 반환된 페이로드에 "EVRtoVideoSinkOnMotionDetection" 토폴로지가 나열되는지 확인합니다.

### <a name="read-the-pipeline-topology"></a>파이프라인 토폴로지 읽기

이제 다음 페이로드를 사용하여 `pipelineTopologyGet`을 호출합니다.
```

{
    "@apiVersion" : "1.1",
    "name&quot; : &quot;EVRtoVideoSinkOnMotionDetection"
}
```

몇 초 이내에 출력 창에 다음 응답이 표시됩니다.

```
[DirectMethod] Invoking Direct Method [pipelineTopologyGet] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-05-03T15:17:53.483Z",
      "lastModifiedAt&quot;: &quot;2021-05-03T15:17:53.483Z"
    },
    "name": "EVRtoVideoSinkOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Video Sink based on motion events",
      "parameters": [
        {
          "name": "hubSinkOutputName",
          "type": "string",
          "description": "hub sink output name",
          "default&quot;: &quot;inferenceOutput"
        },
        {
          "name": "motionSensitivity",
          "type": "string",
          "description": "motion detection sensitivity",
          "default&quot;: &quot;medium"
        },
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtsp source password.",
          "default&quot;: &quot;dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description&quot;: &quot;rtsp Url"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtsp source user name.",
          "default&quot;: &quot;dummyUserName"
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
              "password&quot;: &quot;${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        },
        {
          "@type": "#Microsoft.VideoAnalyzer.SignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.VideoAnalyzer.VideoSink",
          "localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "videoName": "sample-motion-video-camera001",
          "videoCreationProperties": {
            "title": "sample-motion-video-camera001",
            "description": "Motion-detection based recording of clips to a video resource",
            "segmentLength&quot;: &quot;PT30S"
          },
          "name": "videoSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        }
      ]
    }
  }
}
```

응답 페이로드에서 다음 속성을 확인합니다.

* 상태 코드가 성공을 나타내는 200입니다.
* 페이로드에는 `createdAt` 타임스탬프 및 `lastModifiedAt` 타임스탬프가 포함됩니다.

### <a name="create-a-live-pipeline-using-the-topology"></a>토폴로지를 사용하여 라이브 파이프라인 만들기

다음으로, 위의 파이프라인 토폴로지를 참조하는 라이브 파이프라인을 만듭니다. 다음 페이로드를 사용하여 `livePipelineSet` 직접 메서드를 호출합니다.

```
{
    "@apiVersion" : "1.1",
    "name" : "Sample-Pipeline-1",
    "properties" : {
        "topologyName" : "EVRtoVideoSinkOnMotionDetection",
        "description" : "Sample pipeline description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/lots_015.mkv" }
        ]
    }
}
```

다음 사항에 유의하세요.

* 위의 페이로드는 라이브 파이프라인에서 사용할 토폴로지("EVRtoVideoSinkOnMotionDetection")를 지정합니다.
* 이 페이로드에는 토폴로지 페이로드에 기본값이 없는 `rtspUrl`의 매개 변수 값이 포함되어 있습니다.

몇 초 이내에 출력 창에 다음 응답이 표시됩니다.

```
[DirectMethod] Invoking Direct Method [livePipelineSet] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-05-03T15:20:29.023Z",
      "lastModifiedAt&quot;: &quot;2021-05-03T15:20:29.023Z"
    },
    "name": "Sample-Pipeline-1",
    "properties": {
      "state": "Inactive",
      "description": "Sample pipeline description",
      "topologyName": "EVRtoVideoSinkOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value&quot;: &quot;rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

응답 페이로드에서 다음 속성을 확인합니다.

* 상태 코드가 201입니다. 이 코드는 새 라이브 파이프라인이 생성되었음을 의미합니다.
* 상태가 "비활성"입니다. 이 상태는 라이브 파이프라인이 생성되었지만 활성화되지 않았음을 의미합니다. 자세한 내용은 [파이프라인 상태](../pipeline.md#pipeline-states)를 참조하세요.

다음 단계로 아래의 직접 메서드를 사용해 보세요.

* 동일한 페이로드를 사용하여 `livePipelineSet`를 다시 호출하고 반환된 상태 코드가 200인지 확인합니다.
* 다른 설명을 사용하여 `livePipelineSet`를 다시 호출하고 응답 페이로드에 라이브 파이프라인이 성공적으로 업데이트되었음을 나타내는 업데이트된 설명이 있는지 확인합니다.

    > [!NOTE]
    > [파이프라인 토폴로지](../pipeline.md#pipeline-topologies)에서 설명했듯이, 여러 라이브 파이프라인을 만들고 동일한 파이프라인 토폴로지를 사용하여 여러 카메라의 라이브 비디오 스트림을 분석할 수 있습니다. 그러나 이 토폴로지는 `videoName` 값을 하드 코딩합니다. 라이브 비디오 원본 하나만 Video Analyzer 비디오 리소스에 녹화해야 하므로 이 토폴로지로 추가 라이브 파이프라인을 만들면 안 됩니다.

### <a name="activate-the-live-pipeline"></a>라이브 파이프라인 활성화

다음으로, 파이프라인을 통해 (시뮬레이션된) 라이브 비디오 흐름을 시작하는 라이브 파이프라인을 활성화할 수 있습니다. 다음 페이로드를 사용하여 `livePipelineActivate` 직접 메서드를 호출합니다.

```
{
    "@apiVersion" : "1.1",
    "name" : "Sample-Pipeline-1"
}
```

몇 초 이내에 출력 창에 다음 응답이 표시됩니다.

```
[DirectMethod] Invoking Direct Method [livePipelineActivate] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": null
}
```

응답 페이로드의 상태 코드 200은 라이브 파이프라인이 성공적으로 활성화되었음을 나타냅니다.

### <a name="check-the-state-of-the-live-pipeline"></a>라이브 파이프라인의 상태 확인

이제 다음 페이로드를 사용하여 `livePipelineGet` 직접 메서드를 호출합니다.

```
{
    "@apiVersion" : "1.1",
    "name&quot; : &quot;Sample-Pipeline-1"
}
```

몇 초 이내에 출력 창에 다음 응답이 표시됩니다.

```
[DirectMethod] Invoking Direct Method [livePipelineGet] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-05-03T14:21:21.750Z",
      "lastModifiedAt&quot;: &quot;2021-05-03T14:21:21.750Z"
    },
    "name": "Sample-Pipeline-1",
    "properties": {
      "state": "Active",
      "description": "Sample pipeline description",
      "topologyName": "EVRtoVideoSinkOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value&quot;: &quot;rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

응답 페이로드에서 다음 속성을 확인합니다.

* 상태 코드가 성공을 나타내는 200입니다.
* 상태가 "활성"입니다. 이는 라이브 파이프라인이 현재 "활성" 상태임을 나타냅니다.

## <a name="observe-results"></a>결과 확인

위에서 만들고 활성화한 라이브 파이프라인은 동작 감지 프로세서 노드를 사용하여 들어오는 라이브 비디오 스트림에서 동작을 감지하고 IoT Hub 싱크로 이벤트를 보냅니다. 그런 다음, 이러한 이벤트가 IoT Hub에 메시지로 릴레이되면 확인할 수 있습니다. 출력 창에 다음 메시지가 표시됩니다.

```
[IoTHubMonitor] [1:22:53 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620066173760872 1 IN IP4 172.18.0.4\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=Z2QAKKzZQHgCJoQAAAMABAAAAwDwPGDGWA==,aOvhEsiw\r\na=control:track1\r\n"
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-05-03T18:22:53.761Z",
    "dataVersion": "1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066173816,
    "iothub-message-source": "Telemetry",
    "messageId": "c2de6a40-1e0a-45ef-9449-599fc5680d05",
    "contentType": "application/json",
    "contentEncoding": "utf-8"
  }
}
[IoTHubMonitor] [1:22:59 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "timestamp": 145805956115743,
    "inferences": [
      {
        "type": "motion",
        "motion": {
          "box": {
            "l": 0.48954,
            "t": 0.140741,
            "w": 0.075,
            "h": 0.058824
          }
        }
      }
    ]
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/processors/motionDetection",
    "eventType": "Microsoft.VideoAnalyzer.Analytics.Inference",
    "eventTime": "2021-05-03T18:22:59.063Z",
    "dataVersion&quot;: &quot;1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066179359,
    "iothub-message-source": "Telemetry",
    "messageId": "9ccfab80-2993-42c7-9452-92e21df96413",
    "contentType": "application/json",
    "contentEncoding&quot;: &quot;utf-8"
  }
}

```

위의 메시지에서 다음 속성을 확인합니다.

* 각 메시지에 `body` 섹션 및 `properties` 섹션이 포함되어 있습니다. 이러한 섹션이 나타내는 내용을 이해하려면 [IoT Hub 메시지 작성 및 읽기](../../../iot-hub/iot-hub-devguide-messages-construct.md) 문서를 참조하세요.
* 첫 번째 메시지는 RTSP 원본 노드(주체)가 RTSP 시뮬레이터와 연결을 설정할 수 있었으며 (시뮬레이션된) 라이브 피드를 받기 시작했음을 나타내는 **MediaSessionEstablished** 입니다.
* `subject`는 메시지가 생성된 라이브 파이프라인의 노드를 참조합니다. 이 경우 메시지는 RTSP 원본 노드에서 온 것입니다.
* `eventType`은 이 이벤트가 진단 이벤트임을 나타냅니다.
* `eventTime`은 이벤트가 발생한 시간을 나타냅니다.
* `body`에는 이벤트에 대한 데이터, 즉, [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 메시지가 포함됩니다.
* 두 번째 메시지는 **Inference** 이벤트입니다. **MediaSessionEstablished** 메시지가 표시되고 약 5초 후에 전송된 것을 확인할 수 있습니다. 이 5초라는 시간은 비디오가 시작되는 시점과 자동차가 주차장을 통과하는 시점 사이의 지연 시간입니다.
* `subject`는 이 메시지를 생성한 파이프라인의 동작 감지 프로세서 노드를 참조합니다.
* 이 이벤트는 분석 이벤트이므로 `body`에 `timestamp` 및 `inferences` 데이터가 포함됩니다.
* `inferences` 섹션은 `type`이 "motion"이며 "motion" 이벤트에 대한 추가 데이터가 있음을 나타냅니다.

본문의 `messageId` 섹션은 "9ccfab80-2993-42c7-9452-92e21df96413"입니다. 다음 작업 이벤트에 표시됩니다.

```
[IoTHubMonitor] [1:23:31 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "video",
    "outputLocation&quot;: &quot;sample-motion-video-camera001"
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
    "eventTime": "2021-05-03T18:23:31.319Z",
    "dataVersion&quot;: &quot;1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066211373,
    "iothub-message-source": "Telemetry",
    "messageId": "c7cbb363-7cc7-4169-936f-55de5fae111c",
    "correlationId": "9ccfab80-2993-42c7-9452-92e21df96413",
    "contentType": "application/json",
    "contentEncoding&quot;: &quot;utf-8"
  }
}
```
이 **RecordingStarted** 메시지는 비디오 녹화가 시작되었음을 나타냅니다. "9ccfab80-2993-42c7-9452-92e21df96413"의 `correlationId` 값은 **Inference** 메시지의 `messageId`와 일치하므로 녹화를 트리거한 이벤트를 추적할 수 있습니다. 다음 작업 이벤트는 다음과 같습니다.

```
[IoTHubMonitor] [1:24:00 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "video",
    "outputLocation&quot;: &quot;sample-motion"
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingAvailable",
    "eventTime": "2021-05-03T18:24:00.686Z",
    "dataVersion&quot;: &quot;1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066240741,
    "iothub-message-source": "Telemetry",
    "messageId": "5b26aa88-e037-4834-af34-a6a4df3c42c2",
    "correlationId": "9ccfab80-2993-42c7-9452-92e21df96413",
    "contentType": "application/json",
    "contentEncoding&quot;: &quot;utf-8"
  }
}
[IoTHubMonitor] [1:24:00 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
```

이 **RecordingAvailable** 이벤트는 미디어 데이터가 비디오 리소스에 기록되었음을 나타냅니다. `correlationId`는 똑같이 "9ccfab80-2993-42c7-9452-92e21df96413"입니다. 이 메시지 체인의 마지막 작업 이벤트(`correlationId`가 같음)는 다음과 같습니다.

```
[IoTHubMonitor] [1:24:00 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "video",
    "outputLocation&quot;: &quot;sample-motion"
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingStopped",
    "eventTime": "2021-05-03T18:24:00.710Z",
    "dataVersion&quot;: &quot;1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066240766,
    "iothub-message-source": "Telemetry",
    "messageId": "f3dbd5d5-3176-4d5b-80d8-c67de85bc619",
    "correlationId": "9ccfab80-2993-42c7-9452-92e21df96413",
    "contentType": "application/json",
    "contentEncoding&quot;: &quot;utf-8"
  }
}
```
이 **RecordingStopped** 이벤트는 신호 게이트가 닫혔으며 들어오는 라이브 비디오의 관련 부분이 기록되었음을 나타냅니다. `correlationId`는 똑같이 "9ccfab80-2993-42c7-9452-92e21df96413"입니다.

이 토폴로지에서 신호 게이트 프로세서 노드의 활성화 시간이 30초로 구성되었습니다. 즉, 파이프라인 토폴로지가 약 30초의 분량의 비디오를 녹화합니다.  비디오가 녹화되는 동안 동작 감지 프로세서 노드는 **Inference** 이벤트를 계속 내보냅니다. 이 이벤트는 **RecordingAvailable** 이벤트와 **RecordingStopped** 이벤트 사이의 출력 창에 표시됩니다.

라이브 파이프라인이 계속 실행되도록 두면 RTSP 시뮬레이터가 비디오 파일의 끝에 도달하여 중지/연결 해제됩니다. 그러면 RTSP 원본 노드가 시뮬레이터에 다시 연결하고, 프로세스가 반복됩니다.
    
## <a name="invoke-additional-direct-method-calls-to-clean-up"></a>추가 직접 메서드 호출을 호출하여 정리

다음으로, 직접 메서드를 호출하여 라이브 파이프라인을 비활성화하고 삭제할 수 있습니다(순서대로).

### <a name="deactivate-the-live-pipeline"></a>라이브 파이프라인 비활성화

다음 페이로드를 사용하여 `livePipelineDeactivate` 직접 메서드를 호출합니다.

```
{
    "@apiVersion" : "1.1",
    "name" : "Sample-Pipeline-1"
}
```

몇 초 이내에 출력 창에 다음 응답이 표시됩니다.

```
[DirectMethod] Invoking Direct Method [livePipelineDeactivate] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": null
}
```

상태 코드 200은 라이브 파이프라인이 성공적으로 비활성화되었음을 나타냅니다.


### <a name="delete-the-live-pipeline"></a>라이브 파이프라인 삭제

다음 페이로드를 사용하여 `livePipelineDelete` 직접 메서드를 호출합니다.

```
{
    "@apiVersion" : "1.1",
    "name&quot; : &quot;Sample-Pipeline-1"
}
```

몇 초 이내에 출력 창에 다음 응답이 표시됩니다.

```
[DirectMethod] Invoking Direct Method [livePipelineDelete] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": null
}
```

응답의 상태 코드 200은 라이브 파이프라인이 성공적으로 삭제되었음을 나타냅니다.


### <a name="delete-the-pipeline-topology"></a>파이프라인 토폴로지 삭제

다음 페이로드를 사용하여 `pipelineTopologyDelete` 직접 메서드를 호출합니다.

```
{
    "@apiVersion" : "1.1",
    "name&quot; : &quot;EVRtoVideoSinkOnMotionDetection"
}
```

몇 초 이내에 출력 창에 다음 응답이 표시됩니다.

```
[DirectMethod] Invoking Direct Method [pipelineTopologyDelete] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": null
}
```

상태 코드 200은 파이프라인 토폴로지가 성공적으로 삭제되었음을 나타냅니다.

## <a name="playing-back-the-recording"></a>녹화본 재생

Azure Portal에 로그인하여 비디오를 살펴보면 라이브 파이프라인에서 만든 Video Analyzer 비디오 리소스를 검사할 수 있습니다.
1. 웹 브라우저를 열고 [Azure Portal](https://portal.azure.com/)로 이동합니다. 자격 증명을 입력하여 포털에 로그인합니다. 기본 보기는 서비스 대시보드입니다.
1. 구독에 있는 리소스 중에서 Video Analyzer 계정을 찾아 계정 창을 엽니다.
1. **비디오 분석기** 목록에서 **비디오** 를 선택합니다.
1. 이름이 `sample-motion-video-camera001`인 비디오가 나열됩니다. 이 이름은 파이프라인 토폴로지 파일에서 선택한 이름입니다.
1. 비디오를 선택합니다.
1. 비디오 세부 정보 페이지가 열리고 재생이 자동으로 시작됩니다.

    <!--TODO: add image -- ![Video playback]() TODO: new screenshot is needed here -->


[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]    

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

* [녹화된 비디오 재생](../playback-recordings-how-to.md) 방법 알아보기
* [빠른 시작: 라이브 비디오 분석](analyze-live-video-use-your-model-http.md) 시도
