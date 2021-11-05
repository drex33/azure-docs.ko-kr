---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: juliako
ms.custom: ignite-fall-2021
ms.openlocfilehash: 57d367535226203a67a2ffbf3abe4e6246f09a88
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861470"
---
* 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 수 있습니다.

    [!INCLUDE [azure-subscription-permissions](../../common-includes/azure-subscription-permissions.md)]
    * 다음 확장이 포함된 [Visual Studio Code](https://code.visualstudio.com/)
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

        [!INCLUDE [install-docker-prompt](../../common-includes/install-docker-prompt.md)]
    * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
* [Python 3](https://www.python.org/downloads/)(3.6.9 이상), [Pip 3](https://pip.pypa.io/en/stable/installing/) 및 필요에 따라 [venv](https://docs.python.org/3/library/venv.html).        
   
## <a name="set-up-azure-resources"></a>Azure 리소스 설정

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)  
[!INCLUDE [resources](../../../includes/common-includes/azure-resources.md)]

## <a name="overview"></a>개요

> [!div class="mx-imgBorder"]
> :::image type="content" source="./../../../media/detect-motion-record-video-edge-devices/overview.png" alt-text="연결된 유추 이벤트를 IoT Edge Hub에 게시":::

위의 다이어그램에서는 이 빠른 시작의 신호 흐름을 보여줍니다. [에지 모듈](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)은 RTSP(Real-Time Streaming Protocol) 서버를 호스트하는 IP 카메라를 시뮬레이션합니다. [RTSP 원본](./../../../../pipeline.md#rtsp-source) 노드는 이 서버에서 비디오 피드를 가져와서 비디오 프레임을 [동작 감지 프로세서](./../../../../pipeline.md#motion-detection-processor) 노드로 보냅니다. RTSP 원본은 이벤트에 의해 트리거될 때까지 닫힌 상태로 유지되는 [신호 게이트 프로세서](./../../../../pipeline.md#signal-gate-processor) 노드로 동일한 비디오 프레임을 보냅니다.

동작 감지 프로세서는 비디오의 동작을 감지하면 신호 게이트 프로세서 노드로 이벤트를 보내서 트리거합니다. 게이트가 구성된 시간 동안 열리고 [파일 싱크](./../../../../pipeline.md#file-sink) 노드로 비디오 프레임을 보냅니다. 이 싱크 노드는 에지 디바이스의 로컬 파일 시스템에 MP4 파일로 비디오를 녹화합니다. 이 파일은 구성된 위치에 저장됩니다.

이 빠른 시작에서는 다음을 수행합니다.

1. 파이프라인을 만들고 배포합니다.
1. 결과를 해석합니다.
1. 리소스를 정리합니다.
## <a name="set-up-your-development-environment"></a>개발 환경 설정
[!INCLUDE [setup development environment](./../../../includes/set-up-dev-environment/python/python-set-up-dev-env.md)]
