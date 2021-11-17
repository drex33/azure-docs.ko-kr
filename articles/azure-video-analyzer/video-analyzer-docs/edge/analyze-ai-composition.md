---
title: AI 컴퍼지션을 사용하여 여러 AI 모델로 라이브 비디오 스트림 분석
description: 이 문서에서는 Azure Video Analyzer의 AI 컴퍼지션 기능을 사용하여 여러 AI 모델로 라이브 비디오 스트림을 분석하는 방법에 대한 지침을 제공합니다.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: d4d54fba6f3b59c0e32286333694fa460cdb07b3
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487542"
---
# <a name="analyze-live-video-streams-with-multiple-ai-models-using-ai-composition"></a>AI 컴퍼지션을 사용하여 여러 AI 모델로 라이브 비디오 스트림 분석

[!INCLUDE [header](includes/edge-env.md)]

특정 고객 시나리오에서는 여러 AI 모델로 비디오를 분석해야 합니다. 해당 모델은 [서로를 보강](../ai-composition-overview.md#sequential-ai-composition)하거나, [동일한 비디오 스트림](../ai-composition-overview.md#combined-ai-composition)에서 [독립적으로 병렬 작업](../ai-composition-overview.md#parallel-ai-composition)하거나, 보강되고 독립적인 병렬 모델이 조합되어 동일한 비디오 스트림에서 작업하여 실행 가능한 인사이트를 제공할 수 있습니다.

Azure Video Analyzer는 [AI 컴퍼지션](../ai-composition-overview.md)이라는 기능을 통해 이러한 시나리오를 지원합니다. 이 가이드에서는 동일한 비디오 스트림에 여러 모델을 보강된 방식으로 적용하는 방법을 보여 줍니다. Tiny(Light) YOLO와 일반 YOLO 모델을 병렬로 사용하여 관심 있는 개체를 검색합니다. Tiny YOLO 모델은 계산이 더 간단하지만 YOLO 모델보다 정확도가 낮으며 먼저 호출됩니다. 검색된 개체가 특정 신뢰도 임계값을 통과하면 순차적으로 스테이징되는 일반 YOLO 모델이 호출되지 않으므로 기본 리소스를 효율적으로 활용할 수 있습니다.

이 가이드의 단계를 완료하면 AI 작성 가능성이 있는 파이프라인을 통해 시뮬레이션된 라이브 비디오 스트림을 실행하고 특정 시나리오로 확장할 수 있습니다. 다음 다이어그램은 해당 파이프라인을 그래픽으로 보여줍니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-ai-composition/motion-with-object-detection-using-ai-composition.svg" alt-text="AI 컴퍼지션 개요":::
 
## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 수 있습니다.

    > [!NOTE]
    > 서비스 주체를 만들 수 있는 권한이 있는 Azure 구독이 필요합니다(소유자 역할이 이를 제공함). 적절한 권한이 없는 경우 계정 관리자에게 문의하여 적절한 권한을 부여하세요.
* 개발 컴퓨터의 [Visual Studio Code](https://code.visualstudio.com/). [Azure IoT Tools 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)이 있는지 확인합니다.
* 개발 머신이 연결된 네트워크가 아웃바운드 트래픽에 대해 5671 포트를 통해 AMQP(고급 메시지 큐 프로토콜)를 허용하는지 확인합니다. 이렇게 설정하면 Azure IoT Tools에서 Azure IoT Hub와 통신할 수 있습니다.
* [사용자 고유의 gRPC 모델을 사용하여 라이브 비디오 분석](analyze-live-video-use-your-model-grpc.md) 빠른 시작을 완료합니다. 빠른 시작은 방법 가이드에 대한 엄격한 요구 사항이므로 건너뛰지 마세요.

> [!TIP]
> Azure IoT Tools 확장을 설치하는 동안 Docker를 설치하라는 메시지가 표시될 수 있습니다. 이 메시지는 무시해도 됩니다.
>
> 만든 Azure 리소스와 관련된 문제가 발생하는 경우 [문제 해결 가이드](troubleshoot.md#common-error-resolutions) 를 참조하여 일반적으로 발생하는 문제를 해결하세요.

## <a name="review-the-video-sample"></a>비디오 샘플 검토

필수 조건 섹션에 지정된 빠른 시작을 완료했으므로 에지 디바이스가 이미 만들어져 있습니다. 이 에지 디바이스에는 특정 비디오 파일이 포함된 입력 폴더 /home/localedgeuser/samples/input이 있습니다. IoT Edge 디바이스에 로그인하고 /home/localedgeuser/samples/input/ 디렉터리로 변경한 후 다음 명령을 실행하여 이 방법 가이드에 사용할 입력 파일을 가져옵니다.

wget https://avamedia.blob.core.windows.net/public/co-final.mkv

또한 원하는 경우 [VLC 미디어 플레이어](https://www.videolan.org/vlc/)가 있는 머신에서 Ctrl+N을 선택한 다음, [샘플 비디오(.mkv)](https://avamedia.blob.core.windows.net/public/co-final.mkv) 링크를 붙여넣어 재생을 시작합니다. 고속도로를 달리는 자동차 장면이 표시됩니다.

## <a name="create-and-deploy-the-pipeline"></a>파이프라인 만들기 및 배포

필수 조건에서 완료한 빠른 시작의 단계와 마찬가지로 여기에 제공된 단계를 수행할 수 있지만 약간 조정해야 합니다.

1. 방금 완료한 빠른 시작의 [파이프라인 만들기 및 배포](analyze-live-video-use-your-model-grpc.md#create-and-deploy-the-pipeline) 섹션에 있는 지침을 따릅니다. 단계를 진행하면서 다음과 같이 조정해야 합니다. 이 단계는 직접 메서드 호출의 올바른 본문이 사용되었는지 확인하는 데 도움이 됩니다.
    
    *operations.json* 파일을 편집합니다.
    
    * 파이프라인 토폴로지 링크를 변경합니다. `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/ai-composition/topology.json"`
    * `livePipelineSet`에서  
       1. `"topologyName" : "AIComposition"`을 확인합니다. 
       2. `rtspUrl` 매개 변수 값을 `"rtsp://rtspsim:554/media/co-final.mkv"`로 변경합니다.
        
    * `pipelineTopologyDelete`에서 이름을 편집합니다. `"name" : "AIComposition"`
2. [IoT Edge 배포 매니페스트 생성 및 배포](analyze-live-video-use-your-model-grpc.md#generate-and-deploy-the-iot-edge-deployment-manifest) 섹션의 지침을 따르지만 src/edge/deployment.composite.template.json 배포 매니페스트를 대신 사용합니다.
3. [샘플 프로그램 실행](analyze-live-video-use-your-model-grpc.md#run-the-sample-program) 섹션의 지침을 따릅니다.
4. 결과 세부 정보는 [결과 해석](analyze-live-video-use-your-model-grpc.md#interpret-results) 섹션을 참조하세요. 허브의 분석 이벤트와 진단 이벤트 외에도 사용한 토폴로지는 신호 게이트의 AI 신호 기반 활성화를 통해 트리거되는 관련 비디오 클립을 클라우드에 만듭니다. 이 클립과 함께, 수행할 다운스트림 워크플로에 대한 허브의 [작업 이벤트](record-event-based-live-video.md#operational-events)도 제공됩니다. Azure Portal에 로그인하여 비디오 클립을 [검사하고 재생](record-event-based-live-video.md#playing-back-the-recording)할 수 있습니다.

## <a name="clean-up"></a>정리

이 애플리케이션을 계속 사용하지 않으려면 이 빠른 시작에서 만든 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

[진단 메시지](monitor-log-edge.md)에 대해 자세히 알아봅니다.
