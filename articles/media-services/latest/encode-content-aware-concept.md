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
ms.date: 09/16/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: a34e2d16edb4a8ec9ba40a4426fcbbd9b2127b16
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128590407"
---
# <a name="content-aware-encoding"></a>콘텐츠 인식 인코딩

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview-of-the-content-aware-encoding-preset"></a>콘텐츠 인식 인코딩 사전 설정 개요

[적응 비트 전송률 스트리밍을](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)사용하여 배달할 콘텐츠를 준비하려면 비디오를 여러 비트 전송률(높음~낮음) 및 다중 해상도로 인코딩해야 합니다. 이 기술을 사용하면 Apple iOS, Android, Windows 및 Mac의 최신 비디오 플레이어가 버퍼링 없이 콘텐츠를 원활하게 스트리밍하는 스트리밍 프로토콜을 사용할 수 있습니다. 이러한 다양한 디스플레이 크기(해상도) 및 품질(비트 전송률)을 변환하면 플레이어가 현재 네트워크 조건에서 지원할 수 있는 최상의 비디오 버전을 선택할 수 있습니다. 네트워크는 LTE, 4G, 5G, 공용 Wi-Fi 또는 홈 네트워크와 크게 다를 수 있습니다.

콘텐츠를 여러 변환으로 인코딩하려면 인코더에 생성할 내용을 알려주는 해상도 및 비트 전송률 테이블인 "인코딩 타이머"를 생성해야 합니다. 이러한 사다리의 예제는 Media Services 기본 제공 [인코딩 사전 설정을 참조하세요.](/rest/api/media/transforms/createorupdate#encodernamedpreset)

이상적인 조건에서는 인코딩하는 콘텐츠의 형식을 알고 있어야 합니다. 이 정보를 사용하여 원본 비디오의 복잡성 및 동작에 맞게 인코딩 사다리를 조정할 수 있습니다. 즉, 사다리의 각 디스플레이 크기(해상도)에서 품질 향상이 인지적이지 않은 비트 전송률이 있어야 합니다. 인코더는 이 최적 비트 전송률 값으로 작동합니다.

다음 수준의 최적화는 콘텐츠에 따라 해상도를 선택하는 것입니다. 예를 들어 작은 텍스트가 있는 PowerPoint 프레젠테이션의 비디오는 높이가 720픽셀 줄 아래로 인코딩될 때 흐리게 표시됩니다. 또한 샷 및 편집 방법에 따라 전체적으로 동작과 복잡성을 변경하는 비디오가 있을 수도 있습니다.  이렇게 하면 각 장면 또는 샷 경계에서 인코딩 설정을 조정하고 조정할 수 있습니다. 스마트 인코더를 사용하여 비디오 내의 각 장면에 대한 인코딩 설정을 최적화할 수 있습니다.

Azure Media Services 원본 비디오의 비트 전송률 및 해상도의 가변성 문제를 부분적으로 해결하는 [적응 스트리밍](encode-autogen-bitrate-ladder.md) 사전 설정을 제공합니다. 그러나 이 사전 설정은 원본 콘텐츠를 분석하여 얼마나 복잡한지 또는 얼마나 많은 동작이 포함되어 있는지를 파악하지 않습니다. 

콘텐츠 인식 인코딩 사전 설정은 인코더가 광범위한 계산 분석 없이도 지정된 해상도에 대해 최적의 비트 전송률 값을 검색할 수 있는 논리를 추가하여 보다 정적인 "적응 비트 전송률 스트리밍" 인코딩 사전 설정을 개선합니다. 이 사전 설정은 원본 파일을 기반으로 GOP 정렬 MP4의 고유한 "사다리"를 출력합니다. 원본 비디오가 제공된 경우 사전 설정은 입력 콘텐츠에 대한 초기 빠른 분석을 수행하고 결과를 사용하여 최고 품질의 적응 비트 전송률 스트리밍 환경을 제공하는 데 필요한 최적의 레이어 수, 비트 전송률 및 해상도를 결정합니다. 이 사전 설정은 출력 파일이 더 정적인 적응 스트리밍 사전 설정보다 낮은 비트 전송률이지만 대상에게 여전히 좋은 환경을 제공하는 품질인 중소 규모의 복잡성 비디오에 효과적입니다. 출력 폴더에는 스트리밍을 위해 비디오 및 오디오가 준비된 여러 MP4 파일이 포함됩니다.

## <a name="configure-output-settings"></a>출력 설정 구성

또한 개발자는 적응 비트 전송률 스트리밍 구간을 인코딩하기 위한 최적의 설정을 결정할 때 콘텐츠 인식 인코딩 사전 설정에서 사용하는 출력 범위를 제어할 수도 있습니다.

**개발자는 PresetConfigurations** 클래스를 사용하여 일련의 제약 조건 및 옵션을 콘텐츠 인식 인코딩 미리 설정에 전달하여 인코더에서 생성된 결과 파일을 제어할 수 있습니다. 속성은 인코딩 작업의 환경 또는 비용을 제어하기 위해 모든 인코딩을 특정 최대 해상도로 제한하려는 상황에 특히 유용합니다.  또한 대상에서 모바일 네트워크 또는 대역폭 제약 조건이 있는 글로벌 지역에서 지원할 수 있는 최대 비트 전송률 및 최소 비트 전송률도 제어할 수 있습니다.

## <a name="supported-codecs"></a>지원되는 코덱

콘텐츠 인식 인코딩 사전 설정은 다음 코덱과 함께 사용할 수 있습니다.
-  H.264
-  HEVC(H.265)

## <a name="how-to-use"></a>사용 방법

코드에서 사전 설정을 사용하는 방법에 대한 자세한 내용은 [콘텐츠 인식 인코딩 방법](./encode-content-aware-How-to.md) 및 전체 샘플에 대한 링크를 참조하세요.

## <a name="technical-details-on-content-aware-preset"></a>콘텐츠 인식 사전 설정에 대한 기술 세부 정보

이제 콘텐츠 인식 인코딩 사전 설정의 작동 방식을 좀 더 자세히 살펴보겠습니다.  다음 샘플 그래프는 [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) 및 [VMAF와](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion)같은 품질 메트릭을 사용하여 비교를 보여줍니다. 원본은 인코더에 스트레스를 가하기 위해 영화 및 TV 드라마에서 복잡성이 높은 샷을 짧은 클립으로 연결하여 만들어졌습니다. 정의에 따라 이 사전 설정은 콘텐츠마다 다른 결과를 생성합니다. 이는 일부 콘텐츠의 경우 비트 전송률을 크게 낮추거나 품질을 크게 개선할 수 없다는 의미이기도 합니다.

![PSNR을 사용하는 RD(전송률 왜곡) 곡선](media/encode-content-aware-concept/msrv1.png)

**그림 1: 높은 복잡성의 원본에 대한 PSNR 메트릭 사용 RD(전송률 왜곡) 곡선**

![VMAF를 사용하는 RD(전송률 왜곡) 곡선](media/encode-content-aware-concept/msrv2.png)

**그림 2: 높은 복잡성의 원본에 대한 VMAF 메트릭 사용 RD(전송률 왜곡) 곡선**

다음은 다른 범주의 원본 콘텐츠에 대한 결과입니다. 여기서 인코더는 입력 품질이 저하되었음을 확인할 수 있었습니다(낮은 비트 전송률로 인한 많은 압축 아티팩트). 콘텐츠 인식 사전 설정을 사용하여 인코더가 출력 계층을 하나만 생성하기로 결정했습니다. 이 계층은 대부분의 클라이언트가 정지하지 않고 스트림을 재생할 수 있을 만큼 충분히 낮은 비트 전송률입니다.

![PSNR 사용 RD 곡선](media/encode-content-aware-concept/msrv3.png)

**그림 3: 저품질 입력(1080p)에 대한 PSNR 사용 RD 곡선**

![VMAF 사용 RD 곡선](media/encode-content-aware-concept/msrv4.png)

**그림 4: 저품질 입력(1080p)에 대한 VMAF 사용 RD 곡선**

  
## <a name="next-steps"></a>다음 단계
* [콘텐츠 인식 인코딩 사전 설정을 사용하는 방법](encode-content-aware-how-to.md)
* [자습서: Media Services v3를 사용하여 비디오 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)