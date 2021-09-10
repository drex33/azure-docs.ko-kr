---
title: Azure Video Analyzer Visual Studio Code 확장
description: 이 빠른 시작에서는 Azure Video Analyzer Visual Studio Code를 시작하는 단계를 안내합니다.
ms.service: azure-video-analyzer
ms.topic: quickstart
ms.date: 06/01/2021
ms.openlocfilehash: e3d118f2651e7b680b85bbb41bb6ecc3d7106bba
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745683"
---
# <a name="quickstart-azure-video-analyzer-visual-studio-code-extension"></a>빠른 시작: Azure Video Analyzer Visual Studio Code 확장

이 빠른 시작은 Video Analyzer Visual Studio Code 확장을 설정하고 Video Analyzer 에지 모듈에 연결하여 샘플 파이프라인 토폴로지를 배포하는 방법을 보여주도록 설계되었습니다.  시작 빠른 시작과 동일한 파이프라인을 사용합니다.  

설치 단계가 완료되면 해당 스트림에서 동작을 감지하고 보고하는 라이브 파이프라인을 통해 시뮬레이션된 라이브 비디오 스트림을 실행할 수 있습니다. 다음 다이어그램은 해당 파이프라인을 그래픽으로 보여줍니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/motion-detection.svg" alt-text="동작 감지":::
 
 ## <a name="prerequisites"></a>필수 구성 요소
 
* 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 다음 확장이 포함된 [Visual Studio Code](https://code.visualstudio.com/)
    * [Video Analyzer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.azure-video-analyzer)

* [시작 - Azure Video Analyzer](./get-started-detect-motion-emit-events.md) 빠른 시작을 완료하지 않은 경우 [Azure 리소스를 설정](#set-up-azure-resources)해야 합니다.    

## <a name="set-up-azure-resources"></a>Azure 리소스 설정

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="connect-the-azure-video-analyzer-visual-studio-code-extension-to-your-iot-hub"></a>IoT Hub에 Azure Video Analyzer Visual Studio Code 확장 연결

에지 모듈에 확장을 연결하려면 먼저 연결 문자열을 검색해야 합니다. 다음 단계에 따라 검색합니다.

1.  [Azure Portal](https://portal.azure.com)로 이동하여 IoT Hub를 선택합니다.
1.  왼쪽의 `Settings`에서 `Shared access policies`를 선택합니다.
1.  정책 이름 `iothubowner`를 선택합니다.
1.  오른쪽 창에서 `Primary connection string`을 복사합니다.

이제 연결 문자열이 생겼으므로, 아래 단계에 따라 확장을 에지 모듈에 연결합니다.

1.  Visual Studio Code의 왼쪽에서 `Azure Video Analyzer` 아이콘을 선택합니다.
1.  `Enter Connection String` 단추를 클릭합니다.
1.  맨 위에 포털의 연결 문자열을 붙여넣습니다.
1.  디바이스를 선택합니다. 기본값은 `avasample-iot-edge-device`입니다.
1.  Video Analyzer 모듈을 선택합니다. 기본값은 `avaedge`입니다.

이제 왼쪽에 기본 모듈이 있는 연결된 디바이스가 표시됩니다.  기본적으로 배포된 파이프라인 토폴로지가 없습니다.

## <a name="deploy-a-topology-and-live-pipeline"></a>토폴로지 및 라이브 파이프라인 배포

파이프라인 토폴로지는 Video Analyzer에서 작업 수행 방법을 정의하는 데 사용하는 기본 구성 블록입니다.  파이프라인 토폴로지에 대한 자세한 내용은 [여기](./pipeline.md)서 확인할 수 있습니다.  이 섹션에서는 템플릿인 파이프라인 토폴로지를 배포한 다음, 토폴로지 또는 라이브 파이프라인의 인스턴스를 만듭니다. 라이브 파이프라인은 실제 비디오 스트림에 연결됩니다.

1.  왼쪽의 `Modules`에서 `Pipeline topologies`을 마우스 오른쪽 단추로 클릭하고 `Create pipeline topology`을 선택합니다.
1.  위쪽에 있는 `Try sample topologies`의 `Motion Detection`에서 `Publish motion events to IoT Hub`를 선택합니다.  메시지가 표시되면 `Proceed`를 클릭합니다.
1.  오른쪽 위에서 `Save`를 클릭합니다.

이제 왼쪽의 `Pipeline topologies` 목록에 `MotionDetection`이라는 항목이 표시됩니다.  이것이 파이프라인 토폴로지이며, 일부 매개 변수는 라이브 파이프라인을 만들 때 제공할 수 있는 변수로 정의됩니다.  다음으로 라이브 파이프라인을 만듭니다.

1.  왼쪽의 `Pipeline topologies`에서 `MotionDetection`을 마우스 오른쪽 단추로 클릭하고 `Create live pipeline`을 선택합니다.
1.  `Live pipeline name`에 `mdpipeline1`을 입력합니다.
1.  `Parameters` 섹션에서 다음 작업을 수행합니다.
    - “rtspPassword”에 “testpassword”를 입력합니다.
    - “rtspUrl”에 “rtsp://rtspsim:554/media/camera-300s.mkv”를 입력합니다.
    - “rtspUserName”에 “testuser”를 입력합니다.
1.  오른쪽 위에서 "저장 및 활성화"를 클릭합니다.

그러면 에지 디바이스에 시작 토폴로지가 배포되고 라이브 파이프라인이 가동됩니다.  빠른 시작 시작하기에서 Azure IoT Hub 확장을 설치한 경우 Azure IoT-Hub Visual Studio Code 확장에서 빌드 이벤트 엔드포인트를 모니터링하여 [관찰 결과](./get-started-detect-motion-emit-events.md#observe-results) 섹션에 표시된 것처럼 모니터링할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

* [Azure Video Analytics Visual Studio Code 확장](./visual-studio-code-extension.md)의 다양한 기능에 대해 자세히 알아보세요.
