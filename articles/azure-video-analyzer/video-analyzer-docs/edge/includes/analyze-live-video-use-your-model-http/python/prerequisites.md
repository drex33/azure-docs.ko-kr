---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 09/10/2021
ms.author: juliako
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0b948421911bfa93c290d295f64bdc469896a234
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101046"
---
* 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 수 있습니다.

    > [!NOTE]
    > 기여자 이상의 역할이 있는 Azure 구독이 필요합니다. 적절한 권한이 없는 경우 계정 관리자에게 문의하여 적절한 권한을 부여하세요.
    * 다음 확장이 포함된 [Visual Studio Code](https://code.visualstudio.com/)
        * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

        [!INCLUDE [install-docker-prompt](../../common-includes/install-docker-prompt.md)]
        * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
    * [Python 3](https://www.python.org/downloads/)(3.6.9 이상), [Pip 3](https://pip.pypa.io/en/stable/installing/) 및 필요에 따라 [venv](https://docs.python.org/3/library/venv.html).
* [동작 감지 및 이벤트 내보내기](../../../detect-motion-emit-events-quickstart.md) 빠른 시작 읽기
## <a name="set-up-azure-resources"></a>Azure 리소스 설정

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)  
[!INCLUDE [resources](../../../includes/common-includes/azure-resources.md)]

## <a name="overview"></a>개요
이 빠른 시작에서는 Video Analyzer를 사용하여 차량 및 사람과 같은 개체를 감지합니다. 연결된 유추 이벤트를 IoT Edge Hub에 게시합니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./../../../media/analyze-live-video-use-your-model-http/overview.png" alt-text="연결된 유추 이벤트를 IoT Edge Hub에 게시":::

위의 다이어그램에서는 이 빠른 시작에서 신호가 어떻게 흐르는지 보여줍니다. [에지 모듈](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)은 RTSP(Real-Time Streaming Protocol) 서버를 호스팅하는 IP 카메라를 시뮬레이션합니다. [RTSP 원본 노드](./../../../../pipeline.md#rtsp-source)는 이 서버에서 비디오 피드를 가져와서 비디오 프레임을 [HTTP 확장 프로세서 노드](./../../../../pipeline.md#http-extension-processor)로 보냅니다.

HTTP 확장 노드는 프록시 역할을 수행합니다. samplingOptions 필드에서 설정한 수신 비디오 프레임을 샘플링하고 비디오 프레임을 지정된 이미지 형식으로 변환합니다. 그런 다음, REST를 통해 이미지를 HTTP 엔드포인트 뒤에서 AI 모델을 실행하는 다른 에지 모듈로 릴레이합니다. 이 예에서 해당 에지 모듈은 다양한 형식의 개체를 감지할 수 있는 YOLOv3 모델을 사용하여 빌드됩니다. HTTP 확장 프로세서 노드는 검색 결과를 수집하고, 이벤트를 [IoT Hub 메시지 싱크 노드](./../../../../pipeline.md#iot-hub-message-sink)에 게시합니다. 그런 다음, 노드에서 이러한 이벤트를 [IoT Edge Hub](../../../../../../iot-fundamentals/iot-glossary.md?view=iotedge-2018-06&preserve-view=true#iot-edge-hub)에 보냅니다.

이 빠른 시작에서는 다음을 수행합니다.

* livePipeline을 만들고 배포합니다.
* 결과를 해석합니다.
* 리소스를 정리합니다.
## <a name="set-up-your-development-environment"></a>개발 환경 설정
[!INCLUDE [setup development environment](./../../../includes/set-up-dev-environment/python/python-set-up-dev-env.md)]
