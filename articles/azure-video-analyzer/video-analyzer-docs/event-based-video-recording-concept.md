---
title: Azure Video Analyzer 이벤트 기반 비디오 녹화
description: 'EVR (이벤트 기반 비디오 기록)은 이벤트에 의해 트리거될 때 비디오를 기록 하는 프로세스를 나타냅니다. 문제의 이벤트는 비디오 신호 자체의 처리(예: 동작이 감지된 경우) 또는 독립 원본(예: 도어 센서가 도어가 열렸다는 신호를 보냄)으로 인해 발생할 수 있습니다. 이 문서에서는 EVR과 관련된 몇 가지 사용 사례에 대해 설명합니다.'
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fcb1c7d1e62e8391596b6a88eef9c98a6c1cde0c
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133060123"
---
# <a name="event-based-video-recording"></a>이벤트 기반 비디오 녹화  

EVR(이벤트 기반 비디오 녹화)은 이벤트에 의해 트리거되는 비디오 녹화 프로세스를 말합니다. 문제의 이벤트는 비디오 신호 자체의 처리(예: 동작이 감지된 경우) 또는 독립 원본(예: 도어 센서가 도어가 열렸다는 신호를 보냄)으로 인해 발생할 수 있습니다. 이 문서에서는 EVR과 관련된 몇 가지 사용 사례에 대해 설명합니다.

기록에 대 한 타임 스탬프는 UTC로 저장 됩니다. 녹화 된 비디오는 비디오 분석기의 스트리밍 기능을 사용 하 여 재생할 수 있습니다. 자세한 내용은 [비디오 녹화 재생](playback-recordings-how-to.md)을 참조하세요.

## <a name="suggested-pre-reading"></a>추천 참고 자료  

* [파이프라인 개념](pipeline.md)
* [비디오 녹화 개념](video-recording.md) 

## <a name="overview"></a>개요 

다음 두 가지 방법으로 Video Analyzer를 사용하여 EVR을 수행할 수 있습니다.
* 지정된 RTSP 지원 IP 카메라의 입력을 클라우드의 지정된 비디오 리소스로 녹화합니다. 그러면 각각의 새 이벤트가 해당 비디오 리소스에서 사용 가능한 녹화에 추가됩니다.
* MP4 파일을 IoT Edge 디바이스의 로컬 스토리지로 구분하는 레코드 - 각 이벤트는 새 MP4 파일을 생성합니다.

이벤트 기반 비디오 녹화와 관련된 몇 가지 사용 사례가 이 문서에 설명되어 있습니다.

### <a name="video-recording-triggered-by-motion-detection"></a>동작 감지로 트리거된 비디오 녹화  

이 사용 사례에서는 비디오에서 동작이 감지된 경우에만 비디오 클립을 기록하고 해당 비디오 클립이 생성될 때 경고를 표시할 수 있습니다. 이는 중요한 인프라의 보호와 관련된 상업적 보안 시나리오와 관련이 있을 수 있습니다. EVR은 스토리지 비용을 절감하는 데 도움이 됩니다.

아래 다이어그램은 이 사용 사례를 처리하는 파이프라인의 그래픽 표현을 보여 줍니다. 이러한 파이프라인의 파이프라인 토폴로지의 JSON 표현은 [여기](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-motion-video-sink/topology.json)에서 확인할 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-concept/motion-detection.svg" alt-text="동작이 감지되는 경우 라이브 비디오에 대한 이벤트 기반 녹화":::

다이어그램에서 RTSP 원본 노드는 카메라에서 라이브 비디오 피드를 캡처하여 [동작 감지 프로세서](pipeline.md#motion-detection-processor) 노드에 전달합니다. 라이브 비디오에서 동작을 감지할 때 동작 감지 프로세서 노드는 [신호 게이트 프로세서](pipeline.md#signal-gate-processor) 노드뿐만 아니라 IoT Hub 메시지 싱크 노드로도 이동하는 이벤트를 생성합니다. IoT Hub 메시지 싱크 노드는 이벤트를 IoT Edge Hub로 보내고 여기에서 다른 대상으로 라우팅하여 경고를 트리거할 수 있습니다. 

또한 동작 감지기 노드의 이벤트는 신호 게이트 프로세서 노드를 트리거하고, 열어 라이브 비디오 피드가 RTSP 원본 노드 흐름에서 [비디오 싱크 노드](pipeline.md#video-sink)로 이동할 수 있도록 합니다. 이후에 이러한 동작 감지 이벤트가 없으면 구성된 시간 후에 게이트가 닫힙니다. 이는 비디오 리소스에 추가되는 녹화된 비디오의 기간을 결정합니다.

### <a name="video-recording-based-on-events-from-other-sources"></a>다른 원본의 이벤트를 기반으로 하는 비디오 녹화  

이 사용 사례에서는 다른 IoT 센서의 신호를 사용하여 비디오 녹화를 트리거할 수 있습니다. 아래 다이어그램은 이 사용 사례를 처리하는 파이프라인의 그래픽 표현을 보여 줍니다. 이러한 파이프라인의 파이프라인 토폴로지의 JSON 표현은 [여기](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-file-sink/topology.json)에서 확인할 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-concept/other-sources.svg" alt-text="외부 소스에서 신호를 받는 경우 라이브 비디오에 대한 이벤트 기반 녹화":::

다이어그램에서 외부 센서는 IoT Edge Hub로 이벤트를 보냅니다. 그런 다음 이벤트는 [IoT Hub 메시지 원본](pipeline.md#iot-hub-message-source) 노드를 통해 신호 게이트 프로세서 노드로 라우팅됩니다. 신호 게이트 프로세서 노드의 동작은 이전 사용 사례와 동일합니다. 이벤트에 의해 트리거될 때 라이브 비디오 피드를 열고 RTSP 원본 노드에서 파일 싱크 노드로 이동하도록 합니다. 새 MP4 파일은 게이트가 열릴 때마다 IoT Edge 디바이스의 로컬 스토리지에 녹화됩니다.

### <a name="video-recording-based-on-an-external-inferencing-module"></a>외부 추론 모듈을 기반으로 하는 비디오 녹화 

이 사용 사례에서는 외부 논리 시스템의 신호를 기반으로 비디오를 녹화할 수 있습니다. 이러한 사용 사례의 예제로는 고속도로 트래픽의 비디오 피드에서 트럭이 감지된 경우에만 클라우드에 비디오를 녹화하는 경우가 있을 수 있습니다. 아래 다이어그램은 이 사용 사례를 처리하는 파이프라인의 그래픽 표현을 보여 줍니다. 이러한 파이프라인의 파이프라인 토폴로지의 JSON 표현은 [여기](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json)에서 확인할 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-concept/external-inferencing-module.svg" alt-text="외부 추론 모듈에서 신호를 받는 경우 라이브 비디오에 대한 이벤트 기반 녹화":::

다이어그램에서 RTSP 원본 노드는 카메라에서 라이브 비디오 피드를 캡처하여 두 개의 분기로 전달합니다. 하나는 [신호 게이트 프로세서](pipeline.md#signal-gate-processor) 노드를 포함하고 다른 하나는 [HTTP 확장](pipeline.md#http-extension-processor) 노드를 사용하여 외부 논리 모듈로 데이터를 보냅니다. HTTP 확장 노드를 사용하면 파이프라인이 REST를 통해 외부 유추 서비스로 이미지 프레임(JPEG, BMP 또는 PNG 형식)을 보낼 수 있습니다. 이 신호 경로는 일반적으로 낮은 프레임 속도(5fps 미만)만 지원할 수 있습니다. HTTP 확장 프로세서 노드를 사용하여 외부 추론 모듈로 이동하는 비디오의 프레임 속도로 낮출 수 있습니다.

외부 유추 서비스의 결과는 HTTP 확장 노드에서 검색되며, IoT Hub 메시지 싱크 노드를 통해 IoT Edge Hub로 릴레이되어 여기에서 외부 논리 모듈에 의해 추가로 처리될 수 있습니다. 예를 들어 유추 서비스에서 차량 감지를 수행할 수 있는 경우 논리 모듈이 트럭과 같은 특정 자동차를 찾을 수 있습니다. 논리 모듈이 관심 개체를 감지하면 IoT Edge Hub를 통해 파이프라인의 IoT Hub 메시지 원본 노드로 이벤트를 전송하여 신호 게이트 프로세서 노드를 트리거할 수 있습니다. 신호 게이트의 출력이 비디오 싱크 노드로 이동하는 것으로 표시됩니다. 트럭이 검색될 때마다 비디오가 클라우드에 녹화됩니다(비디오 리소스에 추가됨).

이 예제의 향상된 기능은 HTTP 확장 프로세서 노드에 앞서 동작 탐지기 프로세서를 사용하는 것입니다. 이렇게 하면 야간과 같이 고속도로에 장시간 차량이 없을 때 유추 서비스의 부하를 줄일 수 있습니다.

## <a name="resiliency"></a>복원력
복원 력 기록 (EVR에도 적용 됨) [에 대 한 참고](continuous-video-recording.md#resilient-recording) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [자습서: 이벤트 기반 비디오 녹화](record-event-based-live-video.md)
* [녹화된 콘텐츠 재생](playback-recordings-how-to.md)
