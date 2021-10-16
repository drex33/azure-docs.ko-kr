---
title: Azure Live Video Analytics on IoT Edge 용어
description: 이 문서에서는 Azure Live Video Analytics on IoT Edge 용어에 대한 개요를 제공합니다.
ms.topic: conceptual
ms.date: 05/30/2020
ms.openlocfilehash: 69da3515987387b62b59d33657e9a0b0a28a1389
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130073908"
---
# <a name="live-video-analytics-terminology"></a>Live Video Analytics 용어

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

이 문서에서는 [Live Video Analytics on IoT Edge](overview.md)와 관련된 용어에 대한 개요를 제공합니다.

## <a name="azure-media-services"></a>Azure Media Services

Azure Media Services는 미디어 솔루션을 빌드할 수 있도록 지원하는 클라우드 미디어 플랫폼입니다. 자세한 내용은 [Azure Media Services](../latest/media-services-overview.md) 설명서에서 확인할 수 있습니다.

## <a name="asset"></a>자산

[자산](../latest/assets-concept.md)은 Azure Media Services의 엔터티입니다. 이 엔터티는 Media Services 계정에 연결된 Azure 스토리지 계정의 Blob 컨테이너에 매핑됩니다. 자산과 연결된 모든 파일은 해당 컨테이너에 Blob으로 저장되며 Media Services는 자산과 연결된 메타데이터(예: 이름, 설명, 만든 시간)를 보관합니다.

Live Video Analytics on IoT Edge는 자산을 만들 수도 있고 기존 자산에 데이터를 추가할 수도 있습니다. 이를 통해 연속적이며 이벤트를 기반으로 하는 비디오 녹화 및 재생 시나리오를 지원할 수 있습니다(에지 디바이스의 비디오 캡처, Azure Media Services에 녹화, 기존 Azure Media Services 스트리밍을 통해 재생 기능 사용).

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/docs/guides/)는 언어에 제약 받지 않는 고성능 RPC(원격 프로시저 호출) 프레임워크입니다. [프로토콜 버퍼 3](https://developers.google.com/protocol-buffers/docs/proto3)을 통해 세션 기반 구조적 스키마를 통신을 위한 기본 메시지 교환 형식으로 사용합니다.

## <a name="media-graph"></a>미디어 그래프

[미디어 그래프](media-graph-concept.md)에서는 미디어를 캡처할 위치, 처리하는 방법, 결과를 전송해야 하는 위치를 정의할 수 있습니다. 미디어 그래프를 통해 원본, 프로세서, 싱크 노드로 구성된 그래프를 정의할 수 있으므로 라이브 비디오 분석 애플리케이션을 빌드하는 기능을 제공합니다. 미디어 그래프는 미디어 그래프 개념 페이지에서 자세히 설명합니다.

## <a name="recording"></a>기록 중

보안 카메라용 비디오 관리 시스템 컨텍스트에서 비디오 녹화는 카메라에서 비디오를 캡처하여 모바일과 브라우저 앱을 통해 나중에 볼 수 있도록 한 개 또는 여러 개의 파일에 저장하는 프로세스를 가리킵니다. 비디오 녹화는 [연속 비디오 녹화](continuous-video-recording-concept.md) 및 [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md)로 분류할 수 있습니다. 이 내용은 [비디오 기록](video-recording-concept.md) 개념 페이지에 자세히 설명되어 있습니다.

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326)는 Real-Time Streaming Protocol을 가리킵니다. 실시간 속성을 사용하여 데이터 전송을 제어하는 애플리케이션 수준 프로토콜입니다. RTSP는 오디오 및 비디오와 같은 실시간 데이터를 요청 시 제어된 방식으로 전송할 수 있도록 지원하는 확장 가능한 프레임워크를 제공합니다. 

## <a name="streaming"></a>스트리밍

Netflix, YouTube 등의 서비스를 통해 모바일 디바이스에서 비디오를 시청한 적이 있다면 스트리밍 비디오를 경험해 본 것입니다. “재생”을 누르는 즉시 재생이 시작되며(충분한 대역폭이 있는 경우) 비디오의 타임라인을 따라 앞뒤로 검색할 수 있습니다. 스트리밍에 사용된 아이디어는 비디오에서 시청 중인 부분만 전송하는 것과 서버에서 재생 클라이언트로 데이터가 여전히 전송되고 있는 중에 시청자가 비디오 재생을 시작하도록 허용하는 것입니다. Azure Media Services 컨텍스트에서 [스트리밍](https://en.wikipedia.org/wiki/Streaming_media)은 [Azure Media Services](../azure-media-player/azure-media-player-overview.md)에서 스트리밍 클라이언트(예: Azure Media Player)로 미디어를 전송하는 프로세스를 가리킵니다. Azure Media Services를 사용하여 [HLS(HTTP 라이브 스트리밍)](https://developer.apple.com/streaming/) 및 [MPEG-DASH](https://dashif.org/about/) 같은 업계 표준의 HTTP 기반 미디어 스트리밍 프로토콜을 사용하여 클라이언트로 비디오를 스트리밍할 수 있습니다. HLS는 Azure Media Player 및 [JW Player](https://www.jwplayer.com/), [hls.js](https://github.com/video-dev/hls.js/), [VideoJS](https://videojs.com/), [Google Shaka Player](https://github.com/google/shaka-player) 같은 웹 플레이어에서 지원됩니다. 기본적으로 모바일 앱에서는 Android의 [Exoplayer](https://github.com/google/ExoPlayer) 및 iOS의 [AV Foundation](https://developer.apple.com/av-foundation/)을 사용하여 렌더링을 할 수 있습니다. MPEG-DASH도 마찬가지로 Azure Media Player에서 지원되며 [이 페이지에서 클라이언트 목록을 찾을 수](https://dashif.org/members/) 있습니다. 

[미디어 그래프](#media-graph)를 사용하여 Azure Media Services의 자산에 비디오를 녹화하면 Media Services 스트리밍 기능을 활용하여 HLS 및 DASH로 비디오 스트림을 전송할 수 있습니다. 자세한 내용은 [비디오 재생](video-playback-concept.md) 문서에서 확인할 수 있습니다.

## <a name="vms"></a>VMS

[VMS](https://en.wikipedia.org/wiki/Video_management_system)는 비디오 관리 시스템을 가리킵니다. 이 시스템은 CCTV 카메라를 구성하고 제어하며, 해당 카메라에서 비디오를 캡처하고 녹화하는 데 사용됩니다. 이 시스템은 녹화된 비디오를 재생할 클라이언트 애플리케이션도 제공합니다.

## <a name="next-steps"></a>다음 단계

[미디어 그래프](media-graph-concept.md)
