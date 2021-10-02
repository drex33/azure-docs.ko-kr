---
title: Media Services에서 권장되는 라이브 스트리밍 인코더
description: Media Services에서 권장하는 라이브 스트리밍 온-프레미스 인코더에 대한 자세한 정보
services: media-services
keywords: 인코딩, 인코더, 미디어
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: media-services
ms.openlocfilehash: 6b5bec7006fa7bea7c887286d9cdc1a7b51a3d5c
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389858"
---
# <a name="verified-on-premises-live-streaming-encoders"></a>검증된 온-프레미스 라이브 스트리밍 인코더

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services에서 [라이브 이벤트](/rest/api/media/liveevents)(채널)는 라이브 스트리밍 콘텐츠를 처리하기 위한 파이프라인을 나타냅니다. 라이브 이벤트는 다음 두 가지 방법 중 하나로 라이브 입력 스트림을 받습니다.

* 온-프레미스 라이브 인코더가 다중 비트 전송률 RTMP 또는 부드러운 스트리밍(조각난 MP4) 스트림을 Media Services에서 라이브 인코딩을 수행하도록 설정되지 않은 라이브 이벤트로 보냅니다. 수집된 스트림은 어떠한 추가적인 처리 없이 라이브 이벤트를 통과합니다. 이 방법을 **통과** 라고 합니다. 클라이언트에 대한 적응 비트 전송률 스트리밍을 허용하기 위해 라이브 인코더는 단일 비트 전송률 스트림 대신 다중 비트 전송률 스트림을 통과 라이브 이벤트로 보내는 것이 좋습니다. 

    통과 라이브 이벤트에 다중 비트 전송률 스트림을 사용하는 경우 재생 쪽에서 예기치 않은 동작을 방지하려면 비디오 GOP 크기와 다른 비트 전송률의 비디오 조각을 동기화해야 합니다.

  > [!TIP]
  > 통과 방법은 라이브 스트리밍을 수행하는 가장 경제적인 방법입니다.
  > 서비스는 수집 요구 사항에 따라 기본 및 표준 통과 모드를 지원 합니다. 기본적인 통과는 가장 경제적 이지만 기능에서 제한 됩니다. 
 
* 온-프레미스 라이브 인코더는 단일 비트 전송률 스트림을 RTMP 또는 부드러운 스트리밍(조각화된 MP4) 형식의 하나로 Media Services를 통해 라이브 인코딩을 수행할 수 있는 라이브 이벤트로 전송합니다. 그러면 라이브 이벤트에서 들어오는 단일 비트 전송률 스트림을 다중 비트 전송률(적응) 비디오 스트림으로 라이브 인코딩합니다.

이 문서에서는 확인된 온-프레미스 라이브 스트리밍 인코더에 대해 설명합니다. 확인은 공급업체 자체 확인 또는 고객 검증을 통해 수행됩니다. Microsoft Azure Media Services는 각 인코더의 전체 테스트 또는 엄격한 테스트를 수행하지 않으며 업데이트 시 지속적으로 다시 확인하지 않습니다. 온-프레미스 라이브 인코더를 확인하는 방법에 관한 지침은 [온-프레미스 인코더 확인](encode-on-premises-encoder-partner.md)을 참조하세요.

Media Services를 사용하는 라이브 인코딩에 대한 자세한 내용은 [Media Services v3을 사용하는 라이브 스트리밍](stream-live-streaming-concept.md)을 참조하세요.

## <a name="encoder-requirements"></a>인코더 요구 사항

인코더는 HTTPS 또는 RTMPS 프로토콜을 사용하는 경우 TLS 1.2를 지원해야 합니다.

## <a name="live-encoders-that-output-rtmp"></a>RTMP를 출력하는 라이브 인코더

Media Services는 RTMP이 출력으로 포함된 다음 라이브 인코더 중 하나를 사용하도록 권장합니다. 지원되는 URL 체계는 `rtmp://` 또는 `rtmps://`입니다.

RTMP를 통해 스트리밍할 때 방화벽 및/또는 프록시 설정을 검사하여 아웃바운드 TCP 포트 1935 및 1936이 열려 있는지 확인합니다.<br/><br/>
RTMPS를 통해 스트리밍할 때 방화벽 및/또는 프록시 설정을 검사하여 아웃바운드 TCP 포트 2935 및 2936이 열려 있는지 확인합니다.

> [!NOTE]
> 인코더는 RTMPS 프로토콜을 사용하는 경우 TLS 1.2를 지원해야 합니다.

- Adobe Flash Media Live Encoder 3.2
- [Antix Digital](http://www.antixdigital.com/) StreamZ 라이브(이전 Imagine Communication SelenioFlex Live)
- [Blackmagic ATEM Mini 및 ATEM Mini PRO](https://www.blackmagicdesign.com/products/atemmini)
- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- Elemental Live(버전 2.14.15 이상)
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 및 Hero 8
- Haivision KB
- Haivision Makito X HEVC
- [Restream.io](https://restream.io/)
- OBS Studio
- [Streamlabs OBS](https://streamlabs.com/)
- [Switcher Studio(iOS)](https://www.switcherstudio.com/)
- Telestream Wirecast(TLS 1.2 요구 사항으로 인해 버전 13.0.2 이상)
- Telestream Wirecast S(RTMP만 지원됨. TLS 1.2 이상이 없으므로 RTMPS가 지원되지 않음)
- Teradek Slice 756
- VMIX
- xStream

> [!WARNING]
> 위 인코더 목록은 권장 사항 목록입니다. 인코더는 Microsoft에서 지속적으로 테스트 또는 유효성 검사하지 않으며, 호환성을 손상시킬 수 있는 인코더 공급업체 또는 오픈 소스 프로젝트에서 업데이트 또는 호환성이 손상되는 변경이 도입될 수 있습니다. 

## <a name="live-encoders-that-output-fragmented-mp4-smooth-streaming-ingest"></a>조각화된 MP4(부드러운 스트리밍 수집)를 출력하는 라이브 인코더

Media Services는 다중 비트 전송률 부드러운 스트리밍(조각난 MP4)을 출력으로 제공하는 다음 라이브 인코더 중 하나를 사용하도록 권장합니다. 지원되는 URL 체계는 `http://` 또는 `https://`입니다.

> [!NOTE]
> 인코더는 HTTPS 프로토콜을 사용하는 경우 TLS 1.2를 지원해야 합니다.

- Ateme TITAN Live
- [Antix Digital](http://www.antixdigital.com/) StreamZ 라이브(이전 Imagine Communication SelenioFlex Live)
- Cisco Digital Media Encoder 2200
- Elemental Live(TLS 1.2 요구 사항으로 인해 버전 2.14.15 이상)
- Envivio 4Caster C4 Gen III 
- [Ffmpeg](https://www.ffmpeg.org)
- Media Excel Hero Live 및 Hero 4K(UHD/HEVC)

> [!TIP]
>  라이브 이벤트를 여러 언어로 스트림하는 경우(예: 영어 오디오 트랙 하나 및 스페인어 오디오 트랙 하나) 라이브 피드를 통과 라이브 이벤트로 전송하도록 구성된 Media Excel 라이브 인코더를 사용하여 스트림할 수 있습니다.

> [!WARNING]
> 위 인코더 목록은 권장 사항 목록입니다. 인코더는 Microsoft에서 지속적으로 테스트 또는 유효성 검사하지 않으며, 언제든지 호환성을 손상시키는 인코더 공급업체 또는 오픈 소스 프로젝트에서 지원 또는 버그가 도입될 수 있습니다. 

## <a name="configuring-on-premises-live-encoder-settings"></a>온-프레미스 라이브 인코더 설정 구성

라이브 이벤트 유형에 유효한 설정에 대한 자세한 내용은 [라이브 이벤트 유형 비교](live-event-types-comparison-reference.md)를 참조하세요.

### <a name="playback-requirements"></a>재생 요구 사항

콘텐츠를 재생하려면 오디오 및 비디오 스트림이 둘 다 있어야 합니다. 비디오 전용 스트림의 재생은 지원되지 않습니다.

### <a name="configuration-tips"></a>구성 팁

- 가능하면 하드웨어에 내장된 인터넷 연결을 사용합니다.
- 대역폭 요구 사항을 결정할 때는 스트리밍 비트 전송률의 2배를 지정합니다. 필수는 아니지만, 이 간단한 규칙은 네트워크 정체의 영향을 완화하는 데 도움이 됩니다.
- 소프트웨어 기반 인코더를 사용하는 경우 불필요한 프로그램을 모두 닫습니다.
- 푸시를 시작한 후에 인코더 구성을 변경하면 이벤트에 부정적인 영향을 줍니다. 구성 변경으로 인해 이벤트가 불안정해질 수 있습니다. 
- Azure Media Services와의 지속적인 호환성을 위해 항상 최신 버전의 인코더 소프트웨어를 테스트하고 유효성을 검사하세요. Microsoft는 이 목록에 있는 인코더의 유효성을 다시 검사하지 않으며 대부분의 유효성 검사는 소프트웨어 공급업체에서 직접 “자체 인증”으로 수행됩니다.
- 이벤트를 설정할 수 있는 충분한 시간을 확보합니다. 대규모 이벤트의 경우 이벤트보다 1시간 전에 설정을 시작하는 것이 좋습니다.
- H.264 비디오 및 AAC-LC 오디오 코덱 출력을 사용합니다.
- 브로드캐스트하고 있는 라이브 이벤트 형식의 지원되는 해상도 및 프레임 속도를 계속 적용합니다(예: 60fps는 현재 거부됨).
- 비디오 품질 간에 키 프레임 또는 GOP temporal 정렬이 있는지 확인합니다.
- 각 비디오 품질에 대한 고유한 스트림 이름이 있는지 확인합니다.
- 최적의 적응 비트 전송률 성능을 위해 권장되는 엄격한 CBR 인코딩을 사용합니다.

> [!IMPORTANT]
> 클라우드에 조각을 업로드하는 데 CPU 및 IO 작업이 사용되므로 머신의 물리적 상태(CPU/메모리 등)를 감시합니다. 인코더에서 설정을 변경하는 경우 변경 내용을 적용하려면 반드시 채널/라이브 이벤트를 다시 설정해야 합니다.

## <a name="see-also"></a>참조

[Media Services v3을 통한 라이브 스트리밍](stream-live-streaming-concept.md)

## <a name="next-steps"></a>다음 단계

[인코더를 확인하는 방법](encode-on-premises-encoder-partner.md)
