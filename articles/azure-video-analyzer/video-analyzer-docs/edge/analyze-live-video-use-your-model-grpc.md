---
title: 자체 gRPC 모델로 라이브 비디오 분석
description: 이 빠른 시작에서는 Video Analyzer를 통해 자체 gRPC 모델을 사용하여 라이브 비디오를 분석하는 방법을 설명합니다.
ms.service: azure-video-analyzer
ms.topic: quickstart
ms.date: 06/01/2021
zone_pivot_groups: video-analyzer-programming-languages
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3f71efb14d70a63c8f7884ac010dd248aa72223b
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494618"
---
# <a name="quickstart-analyze-live-video-with-your-own-model---grpc"></a>빠른 시작: 자체 모델로 라이브 비디오 분석 - gRPC

[!INCLUDE [header](includes/edge-env.md)]

이 빠른 시작에서는 Azure Video Analyzer를 사용하여 시뮬레이션된 IP 카메라에서 라이브 비디오 피드를 분석하는 방법을 보여줍니다. 컴퓨터 비전 모델을 적용하여 개체를 감지하는 방법을 볼 수 있습니다. 라이브 비디오 피드의 프레임 하위 집합은 유추 서비스로 보냅니다. 결과는 IoT Edge Hub로 보냅니다.

이 빠른 시작에서는 Azure VM을 IoT Edge 디바이스로 사용하고, 시뮬레이션된 라이브 비디오 스트림을 사용합니다. C#으로 작성된 샘플 코드 및 [동작 감지 및 이벤트 내보내기](detect-motion-emit-events-quickstart.md) 빠른 시작을 기반으로 합니다.

## <a name="prerequisites"></a>필수 구성 요소

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-use-your-model-grpc/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-use-your-model-grpc/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>샘플 비디오 검토

Azure 리소스가 설치되면 고속도로 교통에 대한 짧은 비디오가 IoT Edge 디바이스로 사용하는 Azure의 Linux VM에 복사됩니다. 이 빠른 시작에서는 비디오 파일을 사용하여 라이브 스트림을 시뮬레이션합니다.

[VLC 미디어 플레이어](https://www.videolan.org/vlc/)와 같은 애플리케이션을 엽니다. Ctrl+N을 선택한 다음, [고속도로 교차로 샘플 비디오](https://avamedia.blob.core.windows.net/public/camera-300s.mkv) 링크를 붙여넣어 재생을 시작합니다. 고속도로 교통에서 이동하는 많은 차량의 영상이 표시됩니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LTY4]

이 빠른 시작에서는 Video Analyzer를 사용하여 차량 및 사람과 같은 개체를 감지합니다. 연결된 유추 이벤트를 IoT Edge Hub에 게시합니다.

## <a name="create-and-deploy-the-pipeline"></a>파이프라인 만들기 및 배포

### <a name="examine-and-edit-the-sample-files"></a>샘플 파일 검사 및 편집

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-use-your-model-grpc/csharp/sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-use-your-model-grpc/python/sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge 배포 매니페스트 생성 및 배포

1. _src/edge/_ deployment.grpcyolov3icpu.template.json 파일을 마우스 오른쪽 단추로 클릭한 다음, **IoT Edge 배포 매니페스트 생성** 을 선택합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/analyze-live-video-use-your-model-grpc/generate-deployment-manifest.png" alt-text="IoT Edge 배포 매니페스트 생성":::

1. _deployment.grpcyolov3icpu.amd64.json_ 매니페스트 파일은 src/edge/config 폴더에 만들어집니다.

1. src/edge/config/ **deployment.grpcyolov3icpu.amd64.json** 을 마우스 오른쪽 단추로 클릭하고 **단일 디바이스용 배포 만들기** 를 선택합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/analyze-live-video-use-your-model-grpc/deployment-single-device.png" alt-text= "Create Deployment for Single Device":::

1. IoT Hub 디바이스를 선택하라는 메시지가 표시되면 avasample-iot-edge-device를 선택합니다.
1. 약 30초 후 창의 왼쪽 아래 모서리에서 Azure IoT Hub를 새로 고칩니다. 이제 에지 디바이스에 다음과 같은 배포된 모듈이 표시됩니다.

   - **avaedge** 라는 Video Analyzer 모듈.
   - **rtspsim** 모듈 - RTSP 서버를 시뮬레이션하고 라이브 비디오 피드의 원본 역할을 합니다.
   - **avaextension** 모듈 - gRPC를 통신 방법으로 사용하고, 컴퓨터 비전을 이미지에 적용하고, 개체 형식의 여러 클래스를 반환하는 YOLOv3 개체 감지 모델입니다.

     > [!div class="mx-imgBorder"]
     > :::image type="content" source="./media/vscode-common-screenshots/avaextension.png" alt-text= "YoloV3 object detection model":::

## <a name="run-the-sample-program"></a>샘플 프로그램 실행

1. ::: zone pivot="programming-language-csharp"
    [!INCLUDE [header](includes/common-includes/csharp-run-program.md)]
    ::: zone-end

    ::: zone pivot="programming-language-python"
    [!INCLUDE [header](includes/common-includes/python-run-program.md)]
    ::: zone-end
1. pipelineTopologyList 및 livePipelineList 직접 메서드를 호출하면 **operations.json** 코드가 시작됩니다. 이전 빠른 시작이 완료된 후에 리소스를 정리한 경우 이 프로세스에서 빈 목록을 반환한 다음, 일시 중지합니다. 계속하려면 Enter 키를 선택합니다.

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

1. 터미널 창에 직접 메서드 호출의 다음 세트가 표시됩니다.

   - 이전 pipelineTopologyUrl을 사용하는 pipelineTopologySet에 대한 호출
   - 다음 본문을 사용하는 livePipelineSet에 대한 호출:

   ```
   {
     "@apiVersion": "1.1",
     "name": "Sample-Pipeline-1",
     "properties": {
       "topologyName": "InferencingWithGrpcExtension",
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
         },
         {
           "name": "grpcExtensionAddress",
           "value": "tcp://avaextension:44000"
         }
       ]
     }
   }
   ```

   - 라이브 파이프라인 및 비디오 흐름을 시작하는 livePipelineActivate에 대한 호출입니다.
   - 라이브 파이프라인이 실행 중 상태임을 나타내는 livePipelineList에 대한 두 번째 호출입니다.

1. 터미널 창의 출력이 [계속하려면 Enter를 누르세요]라는 메시지에서 일시 중지됩니다. 아직 Enter 키를 선택하지 마세요. 위로 스크롤하여 호출한 직접 메서드에 대한 JSON 응답 페이로드를 확인합니다.
1. Visual Studio Code에서 출력 창으로 전환합니다. Video Analyzer 모듈에서 IoT 허브에 보내는 메시지가 표시됩니다. 이 빠른 시작의 다음 섹션에서는 이러한 메시지를 설명합니다.
1. 파이프라인은 계속 실행되고 결과를 출력합니다. RTSP 시뮬레이터가 원본 비디오를 계속 반복합니다. 파이프라인을 중지하려면 TERMINAL 창으로 돌아가서 Enter를 선택합니다.

   일련의 다음 호출은 리소스를 정리합니다.

   - `livePipelineDeactivate`를 호출하면 라이브 파이프라인이 비활성화됩니다. 
   - `livePipelineDelete`를 호출하면 라이브 파이프라인이 삭제됩니다.
   - `pipelineTopologyDelete`에 대한 호출은 토폴로지를 삭제합니다.
   - `pipelineTopologyList`에 대한 최종 호출은 목록이 비어 있음을 보여 줍니다.

## <a name="interpret-results"></a>결과 해석

파이프라인 토폴로지를 실행하면 gRPC 확장 프로세서 노드의 결과가 IoT Hub 메시지 싱크 노드를 통해 IoT 허브에 전달됩니다. OUTPUT 창에 표시되는 메시지에는 본문 섹션과 applicationProperties 섹션이 포함되어 있습니다. 자세한 내용은 [IoT Hub 메시지 만들기 및 읽기](../../../iot-hub/iot-hub-devguide-messages-construct.md)를 참조하세요.

다음 메시지에서 Video Analyzer 모듈은 애플리케이션 속성 및 본문의 콘텐츠를 정의합니다.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 이벤트

파이프라인이 인스턴스화되면 RTSP 원본 노드가 rtspsim-live555 컨테이너에서 실행되는 RTSP 서버에 연결을 시도합니다. 연결에 성공하면 다음 이벤트가 출력됩니다. 이벤트 유형은 Microsoft.VideoAnalyzer..Diagnostics.MediaSessionEstablished입니다.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzers.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-04-09T09:42:18.1280000Z"
  }
}
```

이 메시지에서 다음 세부 정보를 확인합니다.

- 메시지는 진단 이벤트입니다. MediaSessionEstablished는 RTSP 원본 노드(주체)에서 RTSP 시뮬레이터에 연결되었고 시뮬레이션된 라이브 피드를 받기 시작했음을 나타냅니다.
- applicationProperties에서 주체는 메시지가 파이프라인의 RTSP 원본 노드에서 생성되었음을 나타냅니다.
- applicationProperties에서 eventType은 이 이벤트가 진단 이벤트임을 나타냅니다.
- eventTime은 이벤트가 발생한 시간을 나타냅니다.
- 본문에는 진단 이벤트에 대한 데이터가 포함됩니다. 이 경우 데이터는 SDP(세션 설명 프로토콜) 세부 정보로 구성됩니다.

### <a name="inference-event"></a>유추 이벤트

gRPC 확장 프로세서 노드는 avaextension 모듈에서 유추 결과를 받습니다. 그런 다음, IoT Hub 메시지 싱크 노드를 통해 결과를 유추 이벤트로 내보냅니다. 이러한 이벤트에서 유형은 자동차 또는 트럭과 같은 엔터티임을 나타내기 위해 엔터티로 설정됩니다. eventTime 값은 개체가 감지된 UTC 시간입니다. 다음 예제에서는 신뢰 수준이 서로 다른 세 대의 자동차가 동일한 비디오 프레임에서 감지되었습니다.

```json
[IoTHubMonitor] [1:48:04 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "timestamp": 145589011404622,
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "car",
          "confidence": 0.97052866
        },
        "box": {
          "l": 0.40896654,
          "t": 0.60390747,
          "w": 0.045092657,
          "h": 0.029998193
        }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "car",
          "confidence": 0.9547283
        },
        "box": {
          "l": 0.20050547,
          "t": 0.6094412,
          "w": 0.043425046,
          "h": 0.037724357
        }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "car",
          "confidence": 0.94567955
        },
        "box": {
          "l": 0.55363107,
          "t": 0.5320657,
          "w": 0.037418623,
          "h": 0.027014252
        }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "car",
          "confidence": 0.8916893
        },
        "box": {
          "l": 0.6642384,
          "t": 0.581689,
          "w": 0.034349587,
          "h": 0.027812533
        }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "car",
          "confidence": 0.8547814
        },
        "box": {
          "l": 0.584758,
          "t": 0.60079926,
          "w": 0.07082855,
          "h": 0.034121
        }
      }
    }
  ]
}
```

메시지에서 다음 세부 정보를 확인합니다.

- 본문 섹션에는 분석 이벤트에 대한 데이터가 포함됩니다. 이 경우 이벤트는 유추 이벤트이므로 본문에 추론 데이터가 포함됩니다.
- 유추 섹션은 형식이 엔터티임을 나타냅니다. 이 섹션에는 엔터티에 대한 추가 데이터가 포함됩니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cleanup](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

- gRPC 프로토콜을 사용하여 다른 파이프라인 토폴로지를 실행해 보세요.

- 고급 사용자에 대한 추가 문제를 검토합니다.

  - RTSP 시뮬레이터를 사용하는 대신 RTSP를 지원하는 [IP 카메라](https://en.wikipedia.org/wiki/IP_camera)를 사용합니다. [ONVIF 규격](https://www.onvif.org/conformant-products/) 제품 페이지에서 RTSP를 지원하는 IP 카메라를 검색할 수 있습니다. G, S 또는 T 프로필을 준수하는 디바이스를 찾습니다.
  - Azure Linux VM 대신 AMD64 또는 x64 Linux 디바이스를 사용합니다. 이 디바이스는 IP 카메라와 동일한 네트워크에 있어야 합니다. [Linux에 Azure IoT Edge 런타임 설치](../../../iot-edge/how-to-install-iot-edge.md?view=iotedge-2018-06&preserve-view=true)의 지침을 따릅니다. 그런 다음, [가상 Linux 디바이스에 첫 번째 IoT Edge 모듈 배포](../../../iot-edge/quickstart-linux.md?view=iotedge-2018-06&preserve-view=true)의 지침에 따라 디바이스를 Azure IoT Hub에 등록합니다.
