---
title: 콘텐츠 인식 인코딩 사전 설정
description: 이 문서에서는 Microsoft Azure Media Services v3의 콘텐츠 인식 인코딩에 대해 설명합니다.
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/17/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5f333b4ca86e24c845a8a91c621a2b3f7c8c984e
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122531046"
---
# <a name="content-aware-encoding-preset"></a>콘텐츠 인식 인코딩 사전 설정

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[적응 비트 전송률 스트리밍](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)으로 배달하도록 콘텐츠를 준비하려면 비디오를 높은 비트 전송률에서 낮은 비트 전송률까지 여러 번 인코딩해야 합니다. 이렇게 하면 비디오의 비트 전송률이 감소하고 따라서 해상도가 감소하므로 정상적인 품질 저하를 보장할 수 있습니다. 이러한 다중 비트 전송률 인코딩은 이른바 인코딩 사다리(해상도 및 비트 전송률 테이블)를 사용합니다. Media Services [기본 제공 인코딩 사전 설정](/rest/api/media/transforms/createorupdate#encodernamedpreset)을 참조하세요.

처리하는 콘텐츠를 파악하고 개별 비디오의 복잡도에 맞게 인코딩 사다리를 사용자 지정/조정해야 합니다. 각 해상도에는 더 이상 품질 개선이 인식되지 않는 비트 전송률이 있습니다. 인코더는 이 최적의 비트 전송률 값에서 작동합니다. 다음 수준의 최적화는 콘텐츠를 기반으로 해상도를 선택하는 것입니다. 예를 들어 PowerPoint 프레젠테이션 비디오에서는 720p를 사용하는 것이 좋습니다. 더 나아가 인코더는 비디오 내에서 각 샷의 설정을 최적화하는 작업을 할 수 있습니다. 

Microsoft의 [적응 스트리밍](encode-autogen-bitrate-ladder.md) 사전 설정은 원본 비디오 품질 및 해상도의 가변성 문제를 부분적으로 해결합니다. 고객은 다양한 콘텐츠를 다양한 해상도로 보유합니다. 콘텐츠는 1080p, 720p, 심지어 SD 또는 그 이하의 해상도입니다. 또한 모든 원본 콘텐츠가 필름 또는 TV 스튜디오의 고품질 mezzanine인 것은 아닙니다. 적응 스트리밍 사전 설정은 비트 전송률 사다리가 입력된 mezzanine의 해상도 또는 평균 비트 전송률을 초과하지 않도록 하여 이러한 문제를 해결합니다. 그러나 이 사전 설정은 해상도 및 비트 전송률 이외의 원본 속성을 검사하지 않습니다.

## <a name="the-content-aware-encoding-preset"></a>콘텐츠 인식 인코딩 사전 설정

콘텐츠 인식 인코딩 사전 설정은 인코더가 지정된 해상도에서 최적의 비트 전송률 값을 검색할 수 있도록 하는 사용자 지정 논리를 통합하여 '적응 비트 전송률 스트리밍' 메커니즘을 확장하지만 광범위한 계산 분석은 필요하지 않습니다. 이 기본 설정은 GOP 정렬 MP4 파일을 생성합니다. 입력 콘텐츠에 따라 서비스가 입력 콘텐츠의 초기 경량 분석을 수행하고 그 결과를 사용하여 적응 스트리밍으로 전송을 위해 최적 레이어 수, 적합한 비트 전송률 및 해상도 설정을 결정합니다. 이 사전 설정은 출력 파일이 적응 스트리밍 사전 설정보다 낮은 비트 전송률로 제공되지만 시청자에게 여전히 좋은 환경을 제공하는 품질로 유지되어 복잡도가 중저수준인 비디오에 특히 효과적입니다. 출력에는 비디오 및 오디오가 인터리브된 MP4 파일이 포함됩니다.

다음 샘플 그래프는 [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) 및 [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion)와 같은 품질 메트릭을 사용하여 비교 결과를 보여 줍니다. 원본은 인코더에 스트레스를 가하기 위해 영화 및 TV 드라마에서 복잡성이 높은 샷을 짧은 클립으로 연결하여 만들어졌습니다. 정의에 따라 이 사전 설정은 콘텐츠마다 다른 결과를 생성합니다. 이는 일부 콘텐츠의 경우 비트 전송률을 크게 낮추거나 품질을 크게 개선할 수 없다는 의미이기도 합니다.

![PSNR을 사용하는 RD(전송률 왜곡) 곡선](media/encode-content-aware-concept/msrv1.png)

**그림 1: 높은 복잡성의 원본에 대한 PSNR 메트릭 사용 RD(전송률 왜곡) 곡선**

![VMAF를 사용하는 RD(전송률 왜곡) 곡선](media/encode-content-aware-concept/msrv2.png)

**그림 2: 높은 복잡성의 원본에 대한 VMAF 메트릭 사용 RD(전송률 왜곡) 곡선**

다음은 다른 범주의 원본 콘텐츠에 대한 결과입니다. 여기서 인코더는 입력 품질이 저하되었음을 확인할 수 있었습니다(낮은 비트 전송률로 인한 많은 압축 아티팩트). 콘텐츠 인식 사전 설정을 사용했을 때 인코더는 대부분의 클라이언트에서 멈춤 없이 스트림을 재생할 수 있도록 충분히 낮은 비트 전송률로 하나의 출력 레이어만 생성하기로 결정했습니다.

![PSNR 사용 RD 곡선](media/encode-content-aware-concept/msrv3.png)

**그림 3: 저품질 입력(1080p)에 대한 PSNR 사용 RD 곡선**

![VMAF 사용 RD 곡선](media/encode-content-aware-concept/msrv4.png)

**그림 4: 저품질 입력(1080p)에 대한 VMAF 사용 RD 곡선**

## <a name="8-bit-hevc-h265-support"></a>8비트 HEVC(H.265) 지원

Azure Media Services의 표준 인코더는 이제 8비트 HEVC(H.265) 인코딩 지원을 제공합니다. HEVC 콘텐츠는 ‘hev1’ 형식을 사용하여 동적 패키지 작성 도구를 통해 전달 및 패키지할 수 있습니다.

HEVC 샘플을 사용하는 새 .NET 사용자 지정 인코딩은 [media-services-v3-dotnet Git Hub 리포지토리](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC)에서 사용할 수 있습니다. 사용자 지정 인코딩 외에도 AMS는 [2021년 2월 릴리스 정보](https://docs.microsoft.com/azure/media-services/latest/release-notes#february-2021)에서 볼 수 있는 다른 새로운 기본 제공 HEVC 인코딩 사전 설정도 지원합니다.
  
## <a name="next-steps"></a>다음 단계

* [자습서: Media Services v3를 사용하여 비디오 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)
* [자습서: URL에 따라 원격 파일 인코딩 및 비디오 스트림 - REST](stream-files-tutorial-with-rest.md)
* [자습서: URL에 따라 원격 파일 인코딩 및 비디오 스트림 - CLI](stream-files-cli-quickstart.md)
* [자습서: URL에 따라 원격 파일 인코딩 및 비디오 스트림 - .NET](stream-files-dotnet-quickstart.md)
* [자습서: URL에 따라 원격 파일 인코딩 및 비디오 스트림 - Node.js](stream-files-nodejs-quickstart.md)
