---
title: Azure Video Analyzer 용어
description: 이 문서에서는 Azure Video Analyzer 용어에 대한 개요를 제공합니다.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 1b9de2608d3b6ccb3922636f8cd473e79eff5e00
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070245"
---
# <a name="azure-video-analyzer-terminology"></a>Azure Video Analyzer 용어

이 문서에서는 [Azure Video Analyzer](overview.md)와 관련된 용어에 대한 개요를 제공합니다.

## <a name="pipeline-topology"></a>파이프라인 토폴로지

[파이프라인 토폴로지](pipeline.md)에서 미디어를 캡처할 위치, 처리하는 방법 및 결과를 전달해야 하는 위치를 정의할 수 있습니다. 이를 통해 Live Video Analytics 애플리케이션을 구축할 수 있는 소스, 프로세서 및 싱크 노드로 구성된 파이프라인을 정의할 수 있습니다. 

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326)는 Real-Time Streaming Protocol을 가리킵니다. 실시간 속성을 사용하여 데이터 전송을 제어하는 애플리케이션 수준 프로토콜입니다. RTSP는 오디오 및 비디오와 같은 실시간 데이터를 요청 시 제어된 방식으로 전송할 수 있도록 지원하는 확장 가능한 프레임워크를 제공합니다. Video Analyzer는 RTSP를 지원하는 IP 카메라에서 비디오 캡처, 분석 및 녹화를 [지원](pipeline.md#rtsp-source)합니다.


## <a name="recording"></a>기록 중

보안 카메라용 비디오 관리 시스템 컨텍스트에서 비디오 녹화는 카메라에서 비디오를 캡처하여 모바일과 브라우저 앱을 통해 나중에 볼 수 있도록 저장하는 프로세스를 가리킵니다. 비디오 녹화는 [연속 비디오 녹화](continuous-video-recording.md) 및 [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md)로 분류할 수 있습니다. 이 내용은 [비디오 기록](video-recording.md) 개념 페이지에 자세히 설명되어 있습니다.

## <a name="video"></a>동영상

Video Analyzer 계정을 만들 때 Azure Storage 계정을 연결해야 합니다. Video Analyzer를 사용하여 카메라에서 라이브 비디오를 녹화하고 해당 데이터를 디스크 또는 클라우드 스토리지에 유지할 수 있습니다. 후자의 경우 데이터는 스토리지 계정의 컨테이너에 Blob으로 저장됩니다. 비디오는 이러한 Blob 컨테이너에 매핑되는 Video Analyzer 계정의 리소스입니다. 이러한 비디오 리소스와 관련된 모든 콘텐츠는 해당 컨테이너에 Blob으로 저장되고 Video Analyzer는 메타데이터(예: 이름, 설명, 생성 시간)를 보유합니다.

Video Analyzer를 사용하여 비디오 리소스를 만들고 기존 비디오에 데이터를 추가할 수 있습니다. 이를 통해 연속적인 이벤트 기반 비디오 녹화 및 재생 시나리오가 가능합니다(에지 디바이스에서 비디오 캡처, Video Analyzer에 녹화 및 Video Analyzer 스트리밍 기능을 통한 재생 포함).

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/docs/guides/)는 언어에 제약 받지 않는 고성능 RPC(원격 프로시저 호출) 프레임워크입니다. [프로토콜 버퍼 3](https://developers.google.com/protocol-buffers/docs/proto3)을 통해 세션 기반 구조적 스키마를 통신을 위한 기본 메시지 교환 형식으로 사용합니다.

## <a name="streaming"></a>스트리밍

Video Analyzer를 사용하여 [HLS(HTTP 라이브 스트리밍)](https://developer.apple.com/streaming/) 및 [MPEG-DASH](https://dashif.org/about/) 같은 업계 표준의 HTTP 기반 미디어 스트리밍 프로토콜을 사용하여 클라이언트로 비디오 녹화를 스트리밍할 수 있습니다. [Azure Video Analyzer 플레이어 위젯](https://github.com/Azure/video-analyzer/blob/main/widgets/readme.md)(웹 구성 요소)을 사용하여 비디오 리소스를 재생할 수 있습니다. 또한 HLS는 [JW Player](https://www.jwplayer.com/), [hls.js](https://github.com/video-dev/hls.js/), [VideoJS](https://videojs.com/), [Google Shaka Player](https://github.com/google/shaka-player) 같은 웹 플레이어에서 지원됩니다. 기본적으로 모바일 앱에서는 Android의 [Exoplayer](https://github.com/google/ExoPlayer) 및 iOS의 [AV Foundation](https://developer.apple.com/av-foundation/)을 사용하여 렌더링을 할 수 있습니다. MPEG-DASH도 마찬가지로 [이 페이지의 클라이언트 목록](https://dashif.org/members/)에서 지원됩니다.

## <a name="vms"></a>VMS

[VMS](https://en.wikipedia.org/wiki/Video_management_system)는 비디오 관리 시스템을 가리킵니다. 이 시스템은 CCTV 카메라를 구성하고 제어하며, 해당 카메라에서 비디오를 캡처하고 녹화하는 데 사용됩니다. 이 시스템은 녹화된 비디오를 재생할 클라이언트 애플리케이션도 제공합니다.

## <a name="next-steps"></a>다음 단계

- [파이프라인](pipeline.md)에 대해 알아봅니다.
