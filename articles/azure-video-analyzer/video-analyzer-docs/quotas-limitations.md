---
title: Azure Video Analyzer 할당량 및 제한 사항 - Azure
description: 이 문서에서는 Azure Video Analyzer 할당량 및 제한 사항을 설명합니다.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: a94ebd36728519b7ae73d9cc48c82097dcfdbb21
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602079"
---
# <a name="video-analyzer-quotas-and-limitations"></a>Video Analyzer 할당량 및 제한 사항

이 문서에서는 Azure Video Analyzer 할당량 및 제한 사항을 설명합니다.

## <a name="quotas-and-limitations---edge-module"></a>할당량 및 제한 - 에지 모듈

이 섹션에서는 Video Analyzer 에지 모듈의 할당량 및 제한 사항을 열거합니다.

### <a name="maximum-period-of-disconnected-use"></a>연결되지 않은 최대 사용 기간

Edge 모듈에서는 인터넷 연결이 일시적으로 끊어질 수 있습니다. 모듈 연결이 36시간 넘게 끊어진 상태를 유지할 경우 실행 중이던 모든 라이브 파이프라인이 비활성화됩니다. 모든 추가 직접 메서드 호출은 차단됩니다.

Edge 모듈을 작동 상태로 복원하려면 모듈이 Azure Video Analyzer 계정과 성공적으로 통신할 수 있도록 인터넷 연결을 다시 설정해야 합니다.

### <a name="maximum-number-of-live-pipelines"></a>최대 활성 파이프라인 수

모듈당 최대 1000개의 라이브 파이프라인(`livePipelineSet`를 통해 만들어짐)이 지원됩니다.

### <a name="maximum-number-of-pipeline-topologies"></a>최대 파이프라인 토폴로지 수

모듈당 최대 50개의 파이프라인 토폴로지(`pipelineTopologySet`를 통해 만들어짐)가 지원됩니다.

### <a name="limitations-on-pipeline-topologies"></a>파이프라인 토폴로지에 대한 제한 사항

다음은 여러 노드가 파이프라인 토폴로지에서 함께 연결될 수 있는 방법에 대한 제한 사항입니다.

* RTSP 원본
   * 파이프라인 토폴로지마다 하나의 RTSP 원본만 허용됩니다.
* 동작 감지 프로세서
   * RTSP 원본에서 직접적으로 다운스트림이어야 합니다.
   * HTTP 또는 gRPC 확장 프로세서에서 다운스트림으로 사용할 수 없습니다.
* 신호 게이트 프로세서
   * RTSP 원본에서 직접적으로 다운스트림이어야 합니다.
   * HTTP 또는 gRPC 확장 프로세서에서 업스트림으로 사용할 수 없습니다.
* 개체 추적기 프로세서
   * HTTP 또는 gRPC 확장 프로세서에서 즉시 다운스트리밍해야 합니다. 확장 프로세서는 라이브 비디오의 모든 프레임에 AI 모델을 적용하지 않아야 합니다.
* 라인 교차 프로세서
   * 개체 추적기 프로세서에서 즉시 다운스트리밍해야 합니다.
* 비디오 싱크 
   * RTSP 원본 또는 신호 게이트 프로세서에서 직접적으로 다운스트림이어야 합니다.
* 파일 싱크
   * 신호 게이트 프로세서에서 직접적으로 다운스트림이어야 합니다.
   * HTTP나 gRPC 확장 프로세서 또는 동작 감지 프로세서에서 직접적으로 다운스트림일 수 없습니다.
* IoT Hub 싱크
   * IoT Hub 원본에서 직접적으로 다운스트림일 수 없습니다.

### <a name="supported-cameras"></a>지원되는 카메라
RTSP 프로토콜을 지원하는 IP 카메라만 사용할 수 있습니다. [ONVIF 규격 제품](https://www.onvif.org/conformant-products) 페이지에서 RTSP를 지원하는 IP 카메라를 찾을 수 있습니다. G, S 또는 T 프로필을 준수하는 디바이스를 찾습니다.

H.264 비디오와 AAC 오디오를 사용하도록 카메라를 구성해야 합니다. 다른 코덱은 현재 지원되지 않습니다.

Video Analyzer는 [인터리브된 RTP 스트림]( https://datatracker.ietf.org/doc/html/rfc2326#section-10.12)을 사용하는 RTSP만 지원합니다. 이 모드에서 RTP 트래픽은 RTSP TCP 연결을 통해 터널링됩니다. UDP를 통한 RTP 트래픽은 지원되지 않습니다.

### <a name="support-for-video-ai"></a>비디오 AI 지원
HTTP 또는 gRPC 확장 프로세서는 외부 AI 모듈을 사용하여 이미지/비디오 프레임 데이터의 전송만 지원합니다. 따라서 오디오 데이터에 대한 추론 실행은 지원되지 않습니다. 결과적으로 `inputs` 중 하나로 RTSP 원본 노드가 있는 파이프라인 토폴로지의 프로세서 노드도 `outputSelectors` 속성을 사용하여 비디오만 프로세서로 전달되도록 합니다. 이 [토폴로지](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-grpcExtension-video-sink/topology.json)를 예로 참조하세요.

## <a name="quotas-and-limitations---service"></a>할당량 및 제한 사항 - 서비스

이 섹션에서는 Video Analyzer 계정의 할당량 및 제한 사항을 열거합니다.

### <a name="limitations-on-service-operations-at-preview"></a>미리 보기의 서비스 작업 제한 사항

미리 보기 릴리스에서는 Video Analyzer 서비스가 다음을 지원하지 않습니다.

* 중단 없이 한 구독에서 다른 구독으로 계정을 마이그레이션하는 기능
* 계정에서 둘 이상의 스토리지 계정을 사용하는 기능
* Video Analyzer 계정과 다른 지역에서 스토리지 계정을 사용할 수 있습니다.

### <a name="limits-on-video-resources"></a>비디오 리소스에 대한 제한 사항
미리 보기 릴리스에서는 각 Video Analyzer 계정에 최대 10,000개의 비디오 리소스가 있을 수 있습니다. 더 높은 한도에 대한 비즈니스 요구가 있는 경우 Azure Portal에서 지원 티켓을 엽니다.

### <a name="limits-on-access-policies"></a>액세스 정책에 대한 제한 사항
미리 보기 릴리스에서는 각 Video Analyzer 계정에는 최대 20개의 액세스 정책이 있을 수 있습니다.

### <a name="limits-on-registered-edge-modules"></a>등록된 에지 모듈에 대한 제한 사항
미리 보기 릴리스에서는 각 Video Analyzer 계정에 최대 1,000개의 에지 모듈을 등록할 수 있습니다. 더 높은 한도에 대한 비즈니스 요구가 있는 경우 Azure Portal에서 지원 티켓을 엽니다.

### <a name="limits-on-client-api-calls"></a>클라이언트 API 호출에 대한 제한 사항
미리 보기 릴리스의 클라이언트 API에는 다음과 같은 제한이 적용됩니다.

* 10초 이내에 최대 50개의 요청
* 시간당 최대 600개 요청

## <a name="limitations---video-analyzer-player-widgets"></a>제한 사항 - Video Analyzer 플레이어 위젯

미리 보기 릴리스에서는 iOS 디바이스에서 재생이 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

[개요](overview.md)
