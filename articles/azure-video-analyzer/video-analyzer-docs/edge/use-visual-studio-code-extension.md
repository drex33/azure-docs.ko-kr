---
title: Azure Video Analyzer 용 Visual Studio Code 확장 사용
description: 이 문서에서는 Azure Video Analyzer 용 Visual Studio Code 확장을 시작 하는 단계를 안내 합니다.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 09/28/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5321cca2e7bf7a522ec40691f755dbb6b927c5ac
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103072"
---
# <a name="use-the-visual-studio-code-extension-for-azure-video-analyzer"></a>Azure Video Analyzer 용 Visual Studio Code 확장 사용

[!INCLUDE [header](includes/edge-env.md)]

이 문서에서는 Azure Video Analyzer 용 비디오 스튜디오 코드 확장을 시작 하는 단계를 안내 합니다. IoT Hub를 통해 Visual Studio Code 확장을 Video Analyzer Edge 모듈에 연결 하 고 [샘플 파이프라인 토폴로지](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/cvr-video-sink)를 배포 합니다. 그런 다음 비디오 리소스에 비디오를 지속적으로 기록 하는 라이브 파이프라인을 통해 시뮬레이션 된 라이브 비디오 스트림을 실행 합니다. 다음 다이어그램은 파이프라인을 나타냅니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-continuous-video-recording/continuous-video-recording-overview.svg" alt-text="연속 비디오 녹화":::
 
 ## <a name="prerequisites"></a>사전 요구 사항
 
* [Azure Video Analyzer 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.azure-video-analyzer) 을 사용 하 여 [Visual Studio Code](https://code.visualstudio.com/)
* [빠른 시작: Azure Video Analyzer 시작](./get-started-detect-motion-emit-events.md) 또는 [연속 비디오 녹화 및 재생](./use-continuous-video-recording.md) 자습서

 > [!NOTE]
 > 이 문서의 이미지는 [연속 비디오 녹화 및 재생](./use-continuous-video-recording.md) 자습서를 기반으로 합니다.    

## <a name="set-up-your-development-environment"></a>개발 환경 설정

### <a name="obtain-your-iot-hub-connection-string"></a>IoT Hub 연결 문자열 가져오기

Video Analyzer Edge 모듈을 호출 하려면 Visual Studio Code 확장을 IoT Hub에 연결 하는 데 먼저 연결 문자열을 사용 해야 합니다.

1. Azure Portal에서 IoT Hub 계정으로 이동합니다.
1. 왼쪽 창에서 **공유 액세스 정책** 을 찾아 선택합니다.
1. **iothubowner** 라는 정책을 선택합니다.
1. **기본 연결 문자열** 값을 복사합니다. `HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX`와 같이 표시됩니다.

### <a name="connect-the-visual-studio-code-extension-to-the-iot-hub"></a>IoT Hub에 대 한 Visual Studio Code 확장을 커넥트 합니다.

IoT Hub 연결 문자열을 사용 하 여 Visual Studio Code 확장을 Video Analyzer 모듈에 연결 합니다. 

1.  Visual Studio Code의 왼쪽 끝에 있는 활동 표시줄에서 **Azure Video Analyzer** 아이콘을 선택 합니다.
1.  비디오 분석기 확장 창에서 **연결 문자열 입력** 단추를 클릭 합니다.
1.  위쪽에 IoT Hub 연결 문자열을 붙여 넣습니다.
1.  AVA가 배포 되는 장치를 선택 합니다. 기본값은로 지정 됩니다 `avasample-iot-edge-device` .
1.  Video Analyzer 모듈을 선택 합니다. 기본값은로 지정 됩니다 `avaedge` .

![연결 문자열을 입력 하는 방법을 보여 주는 Gif](./media/use-visual-studio-code-extension/enter-connection-string.gif)

이제 비디오 분석기 확장 창에 모든 모듈이 포함 된 연결 된 장치가 표시 됩니다. 모듈 아래에는 파이프라인 토폴로지가 나열 됩니다. 기본적으로 배포된 파이프라인 토폴로지가 없습니다.

## <a name="create-a-pipeline-topology"></a>파이프라인 토폴로지 만들기 

[파이프라인 토폴로지](../pipeline.md) 를 사용 하면 연결 된 노드 집합을 통해 사용자 지정 요구에 대해 라이브 비디오 또는 녹화 된 비디오를 처리 하 고 분석 하는 방법을 설명할 수 있습니다. 

1.  **모듈** 왼쪽에서 **파이프라인 토폴로지** 를 마우스 오른쪽 단추로 클릭 하 고 **파이프라인 토폴로지 만들기** 를 선택 합니다.
1.  맨 위를 따라 **샘플 토폴로지 시도** 의 **연속 비디오 녹음** 에서 **Azure video Analyzer 비디오에 기록** 을 선택 합니다. 메시지가 표시 되 면 **계속** 을 클릭 합니다.
1.  오른쪽 위에서 **저장** 을 클릭 합니다.

![토폴로지를 추가 하는 방법을 보여 주는 Gif](./media/use-visual-studio-code-extension/add-topology.gif)

이제 왼쪽에 **CVRToVideoSink** 레이블이 붙은 **파이프라인 토폴로지** 목록에 항목이 있습니다. 일부 매개 변수가 변수로 정의 되는 파이프라인 토폴로지입니다.

## <a name="create-a-live-pipeline"></a>라이브 파이프라인 만들기

라이브 파이프라인은 파이프라인 토폴로지의 인스턴스입니다. 파이프라인 토폴로지의 변수는 라이브 파이프라인을 만들 때 채워집니다.

1.  **파이프라인 토폴로지** 왼쪽에서 **CVRToVideoSink** 을 마우스 오른쪽 단추로 클릭 하 고 **라이브 파이프라인 만들기** 를 선택 합니다.
1.  **인스턴스 이름** 에을 입력 `livePipeline1` 합니다.
1. **매개 변수** 섹션에서 **rtspurl** 매개 변수 아래에을 입력 `rtsp://rtspsim:554/media/camera-300s.mkv` 합니다.
1.  오른쪽 위에서 **저장 및 활성화** 를 클릭 합니다.

![라이브 파이프라인을 만들고 활성화 하는 방법을 보여 주는 Gif](./media/use-visual-studio-code-extension/create-and-activate.gif)

이제 라이브 파이프라인이 활성화 되었으므로 [연속 비디오 녹화 및 재생](./use-continuous-video-recording.md#prepare-to-monitor-the-modules) 자습서에 표시 된 것 처럼 IoT Hub 확장에서 **기본 제공 이벤트 끝점 모니터링 시작** 단추를 클릭 하 여 작업 이벤트를 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

Visual Studio Code 확장을 통해 샘플 파이프라인 토폴로지 중 하나를 로드 하 고 각 노드의 속성을 볼 수도 있습니다. [Azure Video Analyzer 참조의 Visual Studio Code 확장](../visual-studio-code-extension.md) 을 따라 변수를 매개 변수화 하 고 모듈을 연결 하는 방법을 알아봅니다.
