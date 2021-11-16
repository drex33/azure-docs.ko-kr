---
title: OpenVINO™ Model Server – Intel의 AI 확장을 사용하여 라이브 비디오 분석
description: 이 자습서에서는 Intel에서 제공하는 사전 학습된 모델이 있는 AI 서버를 사용하여 시뮬레이션된 IP 카메라에서 라이브 비디오 피드를 분석합니다.
ms.service: azure-video-analyzer
ms.topic: tutorial
ms.date: 11/04/2021
titleSuffix: Azure
ms.custom: ignite-fall-2021
ms.openlocfilehash: f2e6e14849bfa00862b2fd87a0e42d88b315ab14
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490708"
---
# <a name="tutorial-analyze-live-video-using-openvino-model-server--ai-extension-from-intel"></a>자습서: OpenVINO™ Model Server – Intel의 AI 확장을 사용하여 라이브 비디오 분석 

[!INCLUDE [header](includes/edge-env.md)]

이 자습서에서는 [OpenVINO™ Model Server – Intel의 AI 확장](https://aka.ms/ava-intel-ovms)을 사용하여 시뮬레이션된 IP 카메라에서 라이브 비디오 피드를 분석하는 방법을 보여줍니다. 유추 서버가 물체(사람, 차량 또는 자전거) 감지용 모델과 차량 분류용 모델에 액세스하는 방법을 확인할 수 있습니다. 라이브 비디오 피드의 프레임 하위 집합이 유추 서버로 전송되고, IoT Edge Hub로 결과가 전송됩니다.

이 자습서에서는 Azure VM을 IoT Edge 디바이스로 사용하고, 시뮬레이션된 라이브 비디오 스트림을 사용합니다. C#으로 작성된 샘플 코드를 기반으로 합니다.

[!INCLUDE [use-x86-64](./includes/common-includes/use-x86-64.md)]

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>Azure 리소스 설정

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="about-openvino-model-server--ai-extension-from-intel"></a>OpenVINO™ Model Server – Intel의 AI 확장 정보

Intel® Distribution of [OpenVINO™ 툴킷](https://software.intel.com/content/www/us/en/develop/tools/openvino-toolkit.html)(개방형 시각적 유추 및 신경망 최적화)은 개발자와 데이터 과학자가 컴퓨터 비전 워크로드의 속도를 높이고 딥 러닝 유추 및 배포를 간소화하고 에지에서 클라우드에 이르는 Intel® 플랫폼 전반에서 다른 유형의 데이터를 간편하게 실행하도록 설정하는 데 유용합니다. 여기에는 모델 최적화 프로그램 및 유추 엔진이 있는 Intel® Deep Learning Deployment Toolkit 및 40가지 이상의 최적화된 사전 학습 모델이 있는 [Open Model Zoo](https://github.com/openvinotoolkit/open_model_zoo) 리포지토리가 포함됩니다.

복잡한 고성능 라이브 비디오 분석 솔루션을 구축하려면 Video Analyzer 모듈이 에지에서 규모를 활용할 수 있는 강력한 유추 엔진과 쌍을 이뤄야 합니다. 이 자습서에서는 유추 요청이 Video Analyzer와 작동하도록 설계된 모듈인 [OpenVINO™ Model Server – Intel의 AI 확장](https://aka.ms/ava-intel-ovms)으로 전송됩니다. 유추 서버 모듈에는 OVMS(OpenVINO™ Model Server)가 포함되어 있습니다. OVMS는 컴퓨터 비전 워크로드에 고도로 최적화되고 Intel® 아키텍처용으로 개발된 OpenVINO™ 도구 키트로 구동되는 유추 서버입니다. 유추 서버와 Video Analyzer 모듈 간에 비디오 프레임 및 추론 결과를 간편하게 교환할 수 있도록 OVMS에 확장이 추가되었기 때문에, OpenVINO™ 도구 키트 지원 모델을 실행할 수 있습니다([코드](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_wrapper)를 수정하여 유추 서버 모듈을 사용자 지정할 수 있음). Intel® 하드웨어가 제공하는 광범위한 가속 메커니즘에서 추가로 선택할 수도 있습니다. 여기에는 CPU(Atom, Core, Xeon), FPGA, VPU가 포함됩니다.

이 유추 서버의 초기 릴리스에서는 다음 [모델](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_models)에 액세스할 수 있습니다.

- 차량 감지(유추 URL: http://{module-name}:4000/vehicleDetection)
- 사람/차량/자전거 감지(유추 URL: http://{module-name}:4000/personVehicleBikeDetection)
- 차량 분류(유추 URL: http://{module-name}:4000/vehicleClassification)
- 얼굴 감지(유추 URL: http://{module-name}:4000/faceDetection)

> [!NOTE]
> Edge 모듈: OpenVINO™ Model Server – Intel의 AI 확장 및 포함된 소프트웨어를 다운로드하고 사용하면 [사용권 계약](https://www.intel.com/content/www/us/en/legal/terms-of-use.html)의 사용 약관에 동의하는 것입니다.
> Intel은 인권을 존중하며 인권 남용 공모를 방지하기 위해 최선을 다하고 있습니다. [Intel의 글로벌 인권 원칙](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html)을 참조하세요. Intel의 제품 및 소프트웨어는 국제적으로 인정되는 인권 침해를 유발하거나 인권 침해에 기여하지 않는 애플리케이션에서만 사용해야 합니다.

## <a name="overview"></a>개요

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/http-extension-with-vino.png" alt-text="개요":::

이 다이어그램에서는 이 빠른 시작의 신호 흐름을 보여 줍니다. [에지 모듈](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)은 RTSP(Real-Time Streaming Protocol) 서버를 호스팅하는 IP 카메라를 시뮬레이션합니다. [RTSP 원본](../pipeline.md#rtsp-source) 노드는 이 서버에서 비디오 피드를 가져와서 비디오 프레임을 [HTTP 확장 프로세서](../pipeline-extension.md#http-extension-processor) 노드로 보냅니다. 

HTTP 확장 프로세서 노드는 프록시 역할을 수행합니다. 들어오는 비디오 프레임의 하위 집합을 선택하고 해당 프레임을 이미지로 변환합니다. 그런 다음, REST를 통해 이미지를 HTTP 엔드포인트 내부에서 AI 모델을 실행하는 다른 에지 모듈에 릴레이합니다. 이 예에서 에지 모듈은 OpenVINO™ Model Server – Intel의 AI 확장입니다. HTTP 확장 프로세서 노드는 검색 결과를 수집하고, 이벤트를 [IoT Hub 메시지 싱크](../pipeline.md#iot-hub-message-sink) 노드에 게시합니다. 그런 다음, 후자의 노드에서 이러한 이벤트를 [IoT Edge Hub](../../../iot-fundamentals/iot-glossary.md#iot-edge-hub)에 보냅니다.

이 자습서에서는 다음을 수행합니다.

1. 개발 환경을 설정합니다.
1. 필요한 에지 모듈을 배포합니다.
1. 라이브 파이프라인을 만들고 배포합니다.
1. 결과를 해석합니다.
1. 리소스를 정리합니다.

## <a name="set-up-your-development-environment"></a>개발 환경 설정
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

### <a name="review-the-sample-video"></a>샘플 비디오 검토

Azure 리소스가 설치되면 [주차장의 짧은 비디오](https://avamedia.blob.core.windows.net/public/lots_015.mkv)가 IoT Edge 디바이스로 사용하는 Azure의 Linux VM에 복사됩니다. 이 빠른 시작에서는 비디오 파일을 사용하여 라이브 스트림을 시뮬레이션합니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

## <a name="deploy-the-required-modules"></a>필요한 모듈 배포

필수 구성 요소의 일부로 샘플 코드를 폴더에 다운로드했습니다. 다음 단계를 따라 필요한 모듈을 배포합니다.

1. Visual Studio Code에서 *src/edge/deployment.openvino.template.json* 파일을 마우스 오른쪽 단추로 클릭한 다음, **IoT Edge 배포 매니페스트 생성** 을 선택합니다.   
1. *deployment.openvino.amd64.json* 매니페스트 파일은 *src/edge/config* 폴더에 생성됩니다.
1. *src/edge/config/deployment.openvino.amd64.json* 파일을 마우스 오른쪽 단추로 클릭한 다음, **단일 디바이스용 배포 만들기** 를 클릭합니다.
1. IoT Hub 디바이스를 선택하라는 메시지가 표시되면 **avasample-iot-edge-device** 를 선택합니다.
1. 약 30초 후 창의 왼쪽 아래 모서리에서 Azure IoT Hub를 새로 고칩니다. 이제 에지 디바이스에 다음과 같은 배포된 모듈이 표시됩니다.
    * **avaedge** 라는 Video Analyzer 에지 모듈.
    * **rtspsim** 모듈 - RTSP 서버를 시뮬레이션하고 라이브 비디오 피드의 원본 역할을 합니다. 
    * **openvino** 모듈 - OpenVINO™ Model Server – Intel의 AI 확장 모듈입니다.
 
## <a name="create-and-activate-the-live-pipeline"></a>라이브 파이프라인 만들기 및 활성화

### <a name="edit-the-sample-code"></a>샘플 코드 편집
1. Visual Studio code에서 *src/cloud-to-device-console-app* 으로 이동하여 *operations.json* 파일을 편집합니다.
    * 라이브 파이프라인 토폴로지에 대한 링크를 변경합니다.

        `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/httpExtensionOpenVINO/topology.json"`

    * `livePipelineSet` 아래에서 이전 링크의 값과 일치하도록 라이브 파이프라인 토폴로지의 이름을 편집합니다.

      `"topologyName" : "InferencingWithOpenVINO"`

    * `pipelineTopologyDelete` 아래에서 이름을 편집합니다.

      `"name": "InferencingWithOpenVINO"`

### <a name="run-the-sample-program-to-detect-vehicles"></a>샘플 프로그램을 실행하여 차량 감지
이 자습서의 파이프라인 토폴로지(`pipelineTopologyUrl`)를 브라우저에서 열면 `inferencingUrl` 값이 `http://openvino:4000/vehicleDetection`으로 설정된 것이 보입니다. 이는 유추 서버가 라이브 비디오에서 차량을 감지하려고 시도함을 의미합니다.

1. 디버깅 세션을 시작하려면 F5 키를 선택합니다. **터미널** 창에 메시지가 출력되어 표시됩니다.
1. *operations.json* 코드가 `pipelineTopologyList` 및 `livePipelineList` 직접 메서드를 호출하여 시작됩니다. 이전 빠른 시작이 완료된 후에 리소스를 정리한 경우 이 프로세스에서 빈 목록을 반환합니다. **터미널** 창에 직접 메서드 호출의 다음 세트가 표시됩니다.

     * 이전 `pipelineTopologyUrl`을 사용하는 `pipelineTopologySet`에 대한 호출
     * 다음 본문을 사용하는 `livePipelineSet`에 대한 호출

         ```json
            {
              "@apiVersion": "1.1",
              "name": "Sample-Pipeline-1",
              "properties": {
                "topologyName": "InferencingWithOpenVINO",
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

     * 파이프라인 및 비디오 흐름을 시작하는 `livePipelineActivate`에 대한 호출
1. **터미널** 창의 출력이 `Press Enter to continue` 프롬프트에서 일시 중지됩니다. 아직 Enter 키를 선택하지 마세요. 위로 스크롤하여 호출한 직접 메서드에 대한 JSON 응답 페이로드를 확인합니다.
1. Visual Studio Code에서 **출력** 창으로 전환합니다. Video Analyzer 모듈에서 IoT 허브에 보내는 메시지가 표시됩니다. 이 빠른 시작의 다음 섹션에서는 이러한 메시지를 설명합니다.
1. 라이브 파이프라인은 계속 실행되고 결과를 출력합니다. RTSP 시뮬레이터가 원본 비디오를 계속 반복합니다. 라이브 파이프라인을 중지하려면 **TERMINAL** 창으로 돌아가서 Enter를 선택합니다. 

    일련의 다음 호출은 리소스를 정리합니다.
      * `livePipelineDeactivate`를 호출하면 파이프라인이 비활성화됩니다.
      * `livePipelineDelete`를 호출하면 파이프라인이 삭제됩니다.
      * `pipelineTopologyDelete`에 대한 호출은 토폴로지를 삭제합니다.
      * `pipelineTopologyList`에 대한 최종 호출은 목록이 비어 있음을 보여 줍니다.

## <a name="interpret-results"></a>결과 해석

라이브 파이프라인을 실행하면 HTTP 확장 프로세서 노드의 결과가 IoT Hub 메시지 싱크 노드를 통해 IoT 허브에 전송됩니다. **OUTPUT** 창에 표시되는 메시지에는 `body` 섹션이 포함되어 있습니다. 자세한 내용은 [IoT Hub 메시지 만들기 및 읽기](../../../iot-hub/iot-hub-devguide-messages-construct.md)를 참조하세요.

다음 메시지에서 Video Analyzer 모듈은 애플리케이션 속성 및 본문의 콘텐츠를 정의합니다. 

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 이벤트

라이브 파이프라인이 활성화되면 RTSP 원본 노드는 rtspsim-live555 컨테이너에서 실행되는 RTSP 서버에 연결을 시도합니다. 연결이 성공하면 **OUTPUT** 창에 다음과 같은 이벤트가 표시됩니다.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [ava-sample-device/avaadge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
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

앞의 출력에서, 

* 이 메시지는 진단 이벤트 **MediaSessionEstablished** 입니다. RTSP 원본 노드(주체)에서 RTSP 시뮬레이터에 연결되었고 라이브 피드(시뮬레이션된)를 받기 시작했음을 나타냅니다.
* `sdp` 섹션에는 진단 이벤트에 대한 데이터가 포함됩니다. 이 경우 데이터는 [SDP(세션 설명 프로토콜)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 세부 정보로 구성됩니다.

### <a name="inference-event"></a>유추 이벤트

HTTP 확장 프로세서 노드는 OpenVINO™ Model Server – AI 확장 모듈에서 유추 결과를 받습니다. 그런 다음, IoT Hub 메시지 싱크 노드를 통해 결과를 유추 이벤트로 내보냅니다. 

이러한 이벤트에서 유형은 자동차 또는 트럭과 같은 엔터티임을 나타내기 위해 `entity`로 설정됩니다. 이러한 이벤트의 `body`에 대한 다음 예제에서는 신뢰도 값이 0.9를 초과하는 차량이 검색되었습니다.

```json
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

## <a name="run-the-sample-program-to-detect-persons-vehicles-or-bikes"></a>샘플 프로그램을 실행하여 사람, 차량 또는 자전거 감지
다른 모델을 사용하려면 파이프라인 토폴로지와 `operations.json` 파일을 수정해야 합니다.

파이프라인 토폴로지(`pipelineTopologyUrl`에서 사용되는 URL)를 로컬 파일(예: `C:\TEMP\topology.json`)에 복사합니다. 해당 복사본을 열고 `inferencingUrl`의 값을 `http://openvino:4000/personVehicleBikeDetection`으로 편집합니다.

그런 다음, Visual Studio Code에서 *src/cloud-to-device-console-app* 폴더로 이동하여 `operations.json` 파일을 엽니다. `pipelineTopologyUrl`이 있는 줄을 다음과 같이 편집합니다.

```
      "pipelineTopologyFile" : "C:\\TEMP\\topology.json" 
```
이제 위 단계를 반복하여 새 토폴로지로 샘플 프로그램을 다시 실행할 수 있습니다. 유추 결과는 차량 감지 모델의 결과와(스키마에서) 유사하며 `subtype`만 `personVehicleBikeDetection`으로 설정됩니다.

## <a name="run-the-sample-program-to-classify-vehicles"></a>샘플 프로그램을 실행하여 차량 분류
Visual Studio Code에서 이전 단계의 `topology.json` 로컬 복사본을 열고 `inferencingUrl` 값을 `http://openvino:4000/vehicleClassification`으로 편집합니다. 사람, 차량 또는 자전거를 감지하는 이전 예제를 실행한 경우에는 `operations.json` 파일을 다시 수정할 필요가 없습니다.

이제 위 단계를 반복하여 새 토폴로지로 샘플 프로그램을 다시 실행할 수 있습니다. 샘플 분류 결과는 다음과 같습니다.

```json
{
  "timestamp": 145819896480527,
  "inferences": [
    {
      "type": "classification",
      "subtype": "color",
      "classification": {
        "tag": {
          "value": "gray",
          "confidence": 0.683415
        }
      }
    },
    {
      "type": "classification",
      "subtype": "type",
      "classification": {
        "tag": {
          "value": "truck",
          "confidence": 0.9978394
        }
      }
    }
  ]
}

```

## <a name="run-the-sample-program-to-detect-faces"></a>샘플 프로그램을 실행하여 얼굴 감지
Visual Studio Code에서 이전 단계의 `topology.json` 로컬 복사본을 열고 `inferencingUrl` 값을 `http://openvino:4000/faceDetection`으로 편집합니다. 사람, 차량 또는 자전거를 감지하는 이전 예제를 실행한 경우에는 `operations.json` 파일을 다시 수정할 필요가 없습니다.

이제 위 단계를 반복하여 새 토폴로지로 샘플 프로그램을 다시 실행할 수 있습니다. 샘플 감지 결과는 다음과 같습니다. (참고: 위에 사용된 주차장 비디오에는 감지 가능한 얼굴이 포함되어 있지 않습니다. - 이 모델을 시도해 보려면 다른 비디오에서 해야 합니다.)

```json
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "faceDetection",
        "entity": {
          "tag": {
            "value": "face",
            "confidence": 0.9997053742408752
          },
          "box": {
            "l": 0.2559490501880646,
            "t": 0.03403960168361664,
            "w": 0.17685115337371826,
            "h": 0.45835764706134796
          }
        }
      }
    ]
  }
```
## <a name="clean-up-resources"></a>리소스 정리

다른 빠른 시작이나 자습서를 사용하려면 여기서 만든 리소스를 그대로 유지하세요. 그렇지 않으면 Azure Portal, 리소스 그룹으로 차례로 이동하고, 이 자습서를 실행한 리소스 그룹을 선택한 다음, 모든 리소스를 삭제하세요.

## <a name="next-steps"></a>다음 단계

고급 사용자에 대한 추가 문제를 검토합니다.

* RTSP 시뮬레이터를 사용하는 대신 RTSP를 지원하는 [IP 카메라](https://en.wikipedia.org/wiki/IP_camera)를 사용합니다. [ONVIF 규격](https://www.onvif.org/conformant-products/) 제품 페이지에서 RTSP를 지원하는 IP 카메라를 검색할 수 있습니다. G, S 또는 T 프로필을 준수하는 디바이스를 찾습니다.
* Azure Linux VM 대신 로컬 x64 Linux 디바이스를 사용합니다. 이 디바이스는 IP 카메라와 동일한 네트워크에 있어야 합니다. [Linux에 Azure IoT Edge 런타임 설치](../../../iot-edge/how-to-install-iot-edge.md)의 지침을 따릅니다. 그런 다음, [가상 Linux 디바이스에 첫 번째 IoT Edge 모듈 배포](../../../iot-edge/quickstart-linux.md)의 지침에 따라 디바이스를 Azure IoT Hub에 등록합니다.
