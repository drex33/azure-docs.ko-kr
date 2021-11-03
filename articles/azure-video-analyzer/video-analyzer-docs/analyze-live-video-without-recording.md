---
title: 기록 없이 라이브 비디오 분석 - Azure
description: 파이프라인 토폴로지를 사용하여 에지 또는 클라우드에 기록하지 않고도 라이브 비디오 스트림에서 분석을 추출할 수 있습니다. 이 문서에서는 이러한 개념에 대해 설명합니다.
ms.topic: conceptual
ms.date: 06/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5bf4490a058fb00e12c9c43faa5af0a6f261fede
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131043948"
---
# <a name="analyzing-live-videos-without-recording"></a>녹화하지 않고 라이브 비디오 분석


## <a name="suggested-pre-reading"></a>추천 참고 자료 

* [파이프라인 개념](pipeline.md)
* [파이프라인 확장 개념](pipeline-extension.md)
* [이벤트 기반 비디오 녹화 개념](event-based-video-recording-concept.md)

## <a name="overview"></a>개요  

파이프라인 토폴로지를 사용하여 비디오의 일부를 파일 또는 자산에 기록하지 않고도 라이브 비디오를 분석할 수 있습니다. 아래에 표시된 파이프라인 토폴로지는 이 문서에서 다루는 [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md)에 대한 그래프와 유사하지만 비디오 싱크 노드 또는 파일 싱크 노드는 포함하지 않습니다.

> [!NOTE]
> 라이브 비디오 분석은 현재 에지 모듈에만 사용할 수 있으며 클라우드에는 사용할 수 없습니다.

### <a name="motion-detection"></a>동작 감지

아래 표시된 파이프라인 토폴로지는 [RTSP 원본](pipeline.md#rtsp-source) 노드, [동작 감지 프로세서](pipeline.md#motion-detection-processor) 노드 및 [IoT Hub 메시지 싱크](pipeline.md#iot-hub-message-sink) 노드로 구성됩니다. [JSON 표현](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/motion-detection/topology.json)에서 사용된 설정을 볼 수 있습니다. 이 토폴로지에서는 들어오는 라이브 비디오 스트림에서 동작을 감지하고 IoT Hub 메시지 싱크 노드를 통해 동작 이벤트를 다른 앱 및 서비스로 릴레이할 수 있습니다. 외부 앱 또는 서비스는 경고를 트리거하거나 적절한 사람에게 알림을 보낼 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video-without-recording/motion-detection.svg" alt-text="라이브 비디오에서 동작 감지":::

### <a name="analyzing-video-using-a-custom-vision-model"></a>사용자 지정 비전 모델을 사용하여 비디오 분석 

아래에 표시된 파이프라인 토폴로지에서는 별도의 모듈에 패키지된 사용자 지정 비전 모델을 사용하여 라이브 비디오 스트림을 분석할 수 있습니다. [JSON 표현](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtension/topology.json)에 사용된 설정을 볼 수 있습니다. 유추 서비스로 실행되는 IoT Edge 모듈로 모델을 래핑하는 데 사용할 수 있는 다른 [예](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions)가 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video-without-recording/motion-detected-frames.svg" alt-text="사용자 지정 비전 모듈을 사용하여 라이브 비디오 분석":::

이 파이프라인 토폴로지에서는 RTSP 원본의 비디오 입력이 [HTTP 확장 프로세서](pipeline.md#http-extension-processor) 노드로 전송됩니다. 그러면 노드는 REST를 통해 외부 유추 서비스로 이미지 프레임(JPEG, BMP 또는 PNG 형식)을 보냅니다. 외부 유추 서비스의 결과는 HTTP 확장 노드에서 검색된 후 IoT Hub 메시지 싱크 노드를 통해 IoT Edge 허브로 릴레이됩니다. 이 유형의 파이프라인 토폴로지를 사용하여 교차로를 통과하는 차량의 시계열 분포를 이해하고, 소매점의 소비자 트래픽 패턴을 이해하는 등의 다양한 시나리오에 대한 솔루션을 만들 수 있습니다.

>[!TIP]
> 다운스트림으로 보내기 전에 `samplingOptions` 필드를 사용하여 HTTP 확장 프로세서 노드 내에서 프레임 속도를 관리할 수 있습니다.

이 예제의 향상된 기능은 HTTP 확장 프로세서 노드에 앞서 동작 탐지기 프로세서를 사용하는 것입니다. 이를 통해 비디오에 동작 활동이 있는 경우에만 유추 서비스가 사용되므로 유추 서비스에 대한 부하를 줄일 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video-without-recording/custom-model.svg" alt-text="움직이는 프레임에서 사용자 지정 비전 모듈을 사용하여 라이브 비디오 분석":::

## <a name="next-steps"></a>다음 단계

[빠른 시작: 자체 모델로 라이브 비디오 분석l - HTTP](analyze-live-video-use-your-model-http.md)
