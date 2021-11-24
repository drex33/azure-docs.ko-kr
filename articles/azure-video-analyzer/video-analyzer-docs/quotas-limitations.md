---
title: Azure Video Analyzer 할당량 및 제한 사항
description: 이 문서에서는 Azure Video Analyzer 할당량 및 제한 사항을 설명합니다.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5ea4a1df70b33964b0ee7676850a52a6276bbfc0
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133060017"
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

Video Analyzer는 [인터리브된 RTP 스트림](https://datatracker.ietf.org/doc/html/rfc2326#section-10.12)을 사용하는 RTSP만 지원합니다. 이 모드에서 RTP 트래픽은 RTSP TCP 연결을 통해 터널링됩니다. UDP를 통한 RTP 트래픽은 지원되지 않습니다. 

### <a name="support-for-video-ai"></a>비디오 AI 지원
HTTP 또는 gRPC 확장 프로세서는 외부 AI 모듈을 사용하여 이미지/비디오 프레임 데이터의 전송만 지원합니다. 따라서 오디오 데이터에 대한 추론 실행은 지원되지 않습니다. 결과적으로 `inputs` 중 하나로 RTSP 원본 노드가 있는 파이프라인 토폴로지의 프로세서 노드도 `outputSelectors` 속성을 사용하여 비디오만 프로세서로 전달되도록 합니다. 이 [토폴로지](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-grpcExtension-video-sink/topology.json)를 예로 참조하세요.

## <a name="quotas-and-limitations---live-and-batch-pipeline"></a>할당량 및 제한 사항 - 라이브 및 일괄 처리 파이프라인

이 섹션에서는 Video Analyzer 클라우드 파이프라인의 할당량 및 제한에 대해 열거합니다. 

### <a name="maximum-number-of-pipeline-topologies"></a>최대 파이프라인 토폴로지 수 

Video Analyzer 계정당 최대 5개 파이프라인 토폴로지만 지원됩니다. 

### <a name="limits-on-concurrent-activation-of-live-pipelines"></a>라이브 파이프라인의 동시 활성화 제한 

5분 동안 라이브 파이프라인을 10개까지 활성화할 수 있으며, 이러한 파이프라인은 항상 10개까지 상태일 수 `Activating` 있습니다.

이러한 제한은 파이프라인 작업에 적용되지 않습니다.  

### <a name="maximum-live-pipelines-per-topology"></a>토폴로지당 최대 라이브 파이프라인  

토폴로지당 라이브 파이프라인은 50개까지 지원됩니다.  

### <a name="concurrent-low-latency-streaming-sessions"></a>동시 짧은 대기 시간 스트리밍 세션  

각 활성 라이브 파이프라인에 대해 [대기 시간이 짧은 스트림을](playback-recordings-how-to.md#low-latency-streaming)보는 클라이언트 애플리케이션이 하나만 있을 수 있습니다. 다른 클라이언트가 연결을 시도하면 요청이 거부됩니다.  

### <a name="limitations-on-designing-pipeline-topologies"></a>파이프라인 토폴로지 디자인에 대한 제한 사항 

다음은 파이프라인 토폴로지에서 함께 연결할 수 있는 여러 노드 및 해당 제한 사항입니다. 

* RTSP 원본
   * 파이프라인 토폴로지마다 하나의 RTSP 원본만 허용됩니다.
* 비디오 원본
   * 파이프라인 토폴로지당 하나의 비디오 원본만 허용됩니다.
   * [일괄 처리 종류의](pipeline.md#batch-pipeline)파이프라인 토폴로지에서만 사용할 수 있습니다. 형식의 비디오 리소스만 수락할 수 `archive` 있습니다. 
* 인코더 프로세서 
   * 파이프라인 토폴로지당 하나의 인코더 프로세서만 허용됩니다.
   * [일괄 처리 종류의](pipeline.md#batch-pipeline)파이프라인 토폴로지에서만 사용할 수 있으며, 이러한 토폴로지에서 비디오 비디오 싱크 노드의 즉시 업스트림이어야 합니다.
   * H.264 코덱을 통해 비디오 인코딩 및 AAC 코덱을 통해 오디오만 지원합니다.
   * 사용자가 녹화된 비디오를 다운스트림 처리를 위해 원하는 형식으로 변환할 때 인코딩 속성을 지정할 수 있습니다. 두 가지 유형: (a) 시스템 사전 설정 (b) 사용자 지정 사전 설정입니다. 각 사전 설정에 허용되는 구성은 아래 표에 나열되어 있습니다. 
     
     | 구성       | 시스템 기본 설정        | 사용자 지정 사전 설정 |
     | -----------         | -----------          |----------- |
     | 비디오 인코더 비트 전송률 kbps      | 원본과 동일      | 200~16,000kbps |
     | 프레임 속도       | 원본과 동일      | 0~300 |
     | 높이    | 원본과 동일        | 1~4320 |
     | 너비    | 원본과 동일       | 1~8192 |
     | 모드   | Pad        | Pad, PreserveAspectRatio, Stretch |     
     | 오디오 인코더 비트 전송률 kbps  | 원본과 동일        | 허용되는 값: 96, 112, 128, 160, 192, 224, 256 |
     
* 비디오 싱크 
   *  파이프라인 토폴로지당 하나의 비디오 싱크만 허용됩니다.
   *  RTSP 원본 또는 인코더 프로세서 노드에서 즉시 다운스트림이어야 합니다. 
   *  일괄 처리 종류의 파이프라인 토폴로지에서 사용되는 경우 출력으로 MP4 파일을 생성합니다.

### <a name="support-for-batch-video-export"></a>일괄 처리 비디오 내보내기 지원 

* 세그먼트 선택 
   * 내보낼 보관된 비디오 부분의 시작 및 끝 타임스탬프인 시간 순서는 UTC 시간으로 지정해야 합니다. 
   * 시간 시퀀스의 최대 범위(종료 타임스탬프 - 시작 타임스탬프)는 24시간 미만이거나 같아야 합니다. 

### <a name="supported-cameras"></a>지원되는 카메라

RTSP 프로토콜을 지원하는 IP 카메라만 사용할 수 있습니다. [ONVIF 규격 제품](https://www.onvif.org/conformant-products) 페이지에서 RTSP를 지원하는 IP 카메라를 찾을 수 있습니다. G, S 또는 T 프로필을 준수하는 디바이스를 찾습니다. 

H.264 비디오와 AAC 오디오를 사용하도록 카메라를 구성해야 합니다. 다른 코덱은 현재 지원되지 않습니다. 비디오 인코딩 비트 전송 속도는 500~3000Kbps 사이여야 합니다.true ingestion bitrate가 이 임계값을 초과하면, 지수 백오프로 다시 연결되고 연결이 끊어집니다.

Video Analyzer는 [인터리브된 RTP 스트림](https://datatracker.ietf.org/doc/html/rfc2326#section-10.12)을 사용하는 RTSP만 지원합니다. 이 모드에서 RTP 트래픽은 RTSP TCP 연결을 통해 터널링됩니다. UDP를 통한 RTP 트래픽은 지원되지 않습니다. 

### <a name="support-for-video-ai"></a>비디오 AI 지원 

라이브 또는 녹화된 비디오 분석은 현재 지원되지 않습니다.

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
