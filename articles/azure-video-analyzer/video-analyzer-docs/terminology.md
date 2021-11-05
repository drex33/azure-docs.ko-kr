---
title: Azure Video Analyzer 용어
description: 이 문서에서는 Azure Video Analyzer 용어에 대한 개요를 제공합니다.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9010ae81606bcd84d37debc4c702a0bc53e328dd
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846960"
---
# <a name="azure-video-analyzer-terminology"></a>Azure Video Analyzer 용어

이 문서에서는 [Azure Video Analyzer](overview.md)와 관련된 용어에 대한 개요를 제공합니다.

## <a name="pipeline-topology"></a>파이프라인 토폴로지

[파이프라인 토폴로지](pipeline.md)에서 미디어를 캡처할 위치, 처리하는 방법 및 결과를 전달해야 하는 위치를 정의할 수 있습니다. 이를 통해 Live Video Analytics 애플리케이션을 구축할 수 있는 소스, 프로세서 및 싱크 노드로 구성된 파이프라인을 정의할 수 있습니다. 

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326) 는 Real-Time 스트리밍 프로토콜을 참조 합니다. 실시간 속성을 사용하여 데이터 전송을 제어하는 애플리케이션 수준 프로토콜입니다. RTSP는 오디오 및 비디오와 같은 실시간 데이터를 요청 시 제어된 방식으로 전송할 수 있도록 지원하는 확장 가능한 프레임워크를 제공합니다. Video Analyzer는 RTSP를 지원하는 IP 카메라에서 비디오 캡처, 분석 및 녹화를 [지원](pipeline.md#rtsp-source)합니다.

## <a name="vms"></a>VMS

[VMS](https://en.wikipedia.org/wiki/Video_management_system)는 비디오 관리 시스템을 가리킵니다. 이러한 시스템은 CCTV 카메라를 구성 및 제어 하 고 비디오를 캡처 및 기록 하는 데 사용 됩니다. 이 시스템은 녹화된 비디오를 재생할 클라이언트 애플리케이션도 제공합니다.

## <a name="recording"></a>기록 중

보안 카메라용 비디오 관리 시스템 컨텍스트에서 비디오 녹화는 카메라에서 비디오를 캡처하여 모바일과 브라우저 앱을 통해 나중에 볼 수 있도록 저장하는 프로세스를 가리킵니다. 비디오 녹화는 [연속 비디오 녹화](continuous-video-recording.md) 및 [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md)로 분류할 수 있습니다. 이 내용은 [비디오 기록](video-recording.md) 개념 페이지에 자세히 설명되어 있습니다.

## <a name="streaming"></a>스트리밍

Video Analyzer를 사용하여 [HLS(HTTP 라이브 스트리밍)](https://developer.apple.com/streaming/) 및 [MPEG-DASH](https://dashif.org/about/) 같은 업계 표준의 HTTP 기반 미디어 스트리밍 프로토콜을 사용하여 클라이언트로 비디오 녹화를 스트리밍할 수 있습니다. [Azure Video Analyzer 플레이어 위젯](https://github.com/Azure/video-analyzer/blob/main/widgets/readme.md)(웹 구성 요소)을 사용하여 비디오 리소스를 재생할 수 있습니다. 또한 HLS는 [JW Player](https://www.jwplayer.com/), [hls.js](https://github.com/video-dev/hls.js/), [VideoJS](https://videojs.com/), [Google Shaka Player](https://github.com/google/shaka-player) 같은 웹 플레이어에서 지원됩니다. 기본적으로 모바일 앱에서는 Android의 [Exoplayer](https://github.com/google/ExoPlayer) 및 iOS의 [AV Foundation](https://developer.apple.com/av-foundation/)을 사용하여 렌더링을 할 수 있습니다. MPEG-DASH도 마찬가지로 [이 페이지의 클라이언트 목록](https://dashif.org/tools/clients/)에서 지원됩니다.

## <a name="exporting"></a>내보내기

보안 카메라에 대 한 VM의 컨텍스트에서 비디오 내보내기는 선택한 비디오를 기록 하 고 별도 파일 (일반적으로 [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) 파일)을 만들어 외부에서 공유할 수 있는 프로세스를 나타냅니다. 예를 들어 비디오 기록에는 직원의 프롬프트 작업에서 안전 인시던트를 방지 하는 푸티지가 포함 될 수 있습니다. 이후 학습 세션에서 사용 하기 위해 기록의 해당 부분을 MP4 파일로 내보낼 수 있습니다.

## <a name="video"></a>동영상

비디오는 녹화, 재생, 스트리밍 및 내보내기와 같은 VM 기능을 가능 하 게 하는 비디오 분석기 계정의 리소스입니다. 비디오는 RTSP 카메라에서 녹음할 수 있으며 녹화 된 기존 비디오의 일부를 내보내 만들 수 있습니다. 녹화 된 비디오는 비디오 분석기 플레이어 위젯 또는 다른 호환 가능한 플레이어를 사용 하 여 스트리밍 및 볼 수 있습니다. 내보낸 비디오를 MP4 파일로 다운로드할 수 있습니다.

비디오 분석기를 사용 하 여 RTSP 카메라에서 기록 하는 경우 해당 비디오 리소스를 해당 카메라에 연결 해야 합니다. 카메라에서 해당 비디오 리소스로 비디오를 지속적으로 기록 하거나 이벤트에 따라 산발적을 기록할 수 있습니다.-Video Analyzer는 기존 비디오 리소스에 데이터 추가를 지원 합니다. 그러나 이렇게 하려면 RTSP 카메라의 속성 (해상도, 프레임 율 등)이 변경 되지 않은 상태로 유지 되어야 합니다. 카메라 설정을 변경 해야 하는 경우 새 비디오 리소스로의 녹화로 전환 해야 합니다.

Video Analyzer 계정을 만들 때 Azure Storage 계정을 연결해야 합니다. 녹화 된 비디오와 내보낸 비디오 모두 저장소 계정의 컨테이너에 blob으로 저장 됩니다. 이러한 비디오 리소스와 관련된 모든 콘텐츠는 해당 컨테이너에 Blob으로 저장되고 Video Analyzer는 메타데이터(예: 이름, 설명, 생성 시간)를 보유합니다.

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/docs/guides/)는 언어에 제약 받지 않는 고성능 RPC(원격 프로시저 호출) 프레임워크입니다. [프로토콜 버퍼 3](https://developers.google.com/protocol-buffers/docs/proto3)을 통해 세션 기반 구조적 스키마를 통신을 위한 기본 메시지 교환 형식으로 사용합니다.

## <a name="low-latency-streaming"></a>짧은 대기 시간 스트리밍

대기 시간이 짧은 비디오 스트리밍은 VM 시스템의 유용한 기능입니다. 비디오 분석기는 약 2 초의 지연 시간으로 라이브 비디오를 스트리밍할 수 있습니다. 대기 시간은 카메라 앞에서 이벤트가 발생 하는 순간과 재생 장치에서 해당 이벤트가 표시 되는 시점 사이의 지연 시간을 의미 합니다.

## <a name="next-steps"></a>다음 단계

- [파이프라인](pipeline.md)에 대해 알아봅니다.
