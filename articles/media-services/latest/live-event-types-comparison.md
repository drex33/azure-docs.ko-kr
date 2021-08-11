---
title: Azure Media Services LiveEvent 유형
description: Azure Media Services에서 라이브 이벤트를 *통과* 또는 *라이브 인코딩* 으로 설정할 수 있습니다. 이 문서에서는 라이브 이벤트 유형을 비교하는 상세 테이블을 보여 줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 3126cbf4f9d87e9baa7267e309210b751a47069d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897801"
---
# <a name="live-event-types-comparison"></a>라이브 이벤트 유형 비교

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services에서 [라이브 이벤트](/rest/api/media/liveevents)는 *통과*(온-프레미스 라이브 인코더가 다중 비트 전송률 스트림을 전송) 또는 *라이브 인코딩*(온-프레미스 라이브 인코더가 단일 비트 전송률 스트림을 전송)으로 설정할 수 있습니다. 

이 문서에서는 라이브 이벤트 유형의 기능을 비교합니다.

## <a name="types-comparison"></a>형식 비교 

다음 표에서는 라이브 이벤트 유형의 기능을 비교합니다. 형식은 생성 중에 [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype)을 사용하여 설정됩니다.

* **LiveEventEncodingType.None** - 온-프레미스 라이브 인코더가 다중 비트 전송률 스트림을 보냅니다. 수집된 스트림은 추가적인 처리 없이 라이브 이벤트를 통과합니다. 통과 라이브 이벤트라고도 합니다.
* **LiveEventEncodingType.Standard** - 온-프레미스 라이브 인코더가 단일 비트 전송률 스트림을 라이브 이벤트로 보내고 Media Services는 여러 비트 전송률 스트림을 만듭니다. 기여 피드가 720p 이상 해상도인 경우 **Default720p** 사전 설정은 6개의 해상도/비트 전송률 쌍 집합을 인코딩합니다(이 문서 뒷부분에 세부 정보가 제공됨).
* **LiveEventEncodingType.Premium1080p** - 온-프레미스 라이브 인코더가 단일 비트 전송률 스트림을 라이브 이벤트로 보내고 Media Services는 여러 비트 전송률 스트림을 만듭니다. Default1080p 사전 설정은 해상도/비트 전송률 쌍의 출력 세트를 지정합니다(이 문서 뒷부분에 세부 정보가 제공됨). 

| 기능 | 통과 라이브 이벤트 | Standard 또는 Premium1080p 라이브 이벤트 |
| --- | --- | --- |
| 단일 비트 전송률 입력은 클라우드에서 다중 비트 전송률로 인코딩됩니다. |예 |예 |
| 기여 피드에 대한 최대 비디오 해상도 |4K(60프레임/초에서 4096x2160) |1080p(30프레임/초에서 1920x1088)|
| 기여 피드에 권장되는 최대 계층|최대 12개|하나의 오디오|
| 출력의 최대 계층| 입력과 같음|최대 6개(아래 시스템 사전 설정 참조)|
| 기여 피드의 최대 집계 대역폭|60Mbps|해당 없음|
| 기여에서 단일 계층의 최대 전송률 |20Mbps|20Mbps|
| 여러 언어 오디오 트랙 지원|예|예|
| 지원되는 입력 비디오 코덱 |H.264/AVC 및 H.265/HEVC|H.264/AVC|
| 지원되는 출력 비디오 코덱|입력과 같음|H.264/AVC|
| 지원되는 비디오 비트 수준, 입력 및 출력|HDR 10/HLG를 포함하여 최대 10비트|8비트|
| 지원되는 입력 오디오 코덱|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| 지원되는 출력 오디오 코덱|입력과 같음|AAC-LC|
| 출력 비디오의 최대 비디오 해상도|입력과 같음|Standard - 720p, Premium1080p - 1080p|
| 입력 비디오의 최대 프레임 전송률|초당 60프레임|Standard 또는 Premium1080p - 초당 30프레임|
| 입력 프로토콜|RTMP, fragmented-MP4(부드러운 스트리밍)|RTMP, fragmented-MP4(부드러운 스트리밍)|
| 가격|[가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/) 를 참조하고 "라이브 비디오" 탭 클릭|[가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/) 를 참조하고 "라이브 비디오" 탭 클릭|
| 최대 실행 시간| 연중무휴 라이브 선형 | 연중무휴 라이브 선형(미리 보기)|
| 포함된 CEA 608/708 캡션 데이터 전달 가능|예|예|
| 라이브 기록을 켜는 기능|예|예|
| 슬레이트 삽입 지원|예|예|
| API를 통한 광고 신호 지원| 예|예|
| SCTE-35 인밴드 메시지를 통한 광고 신호 지원|예|예|
| 기여 피드의 일시 정지에서 복구하는 기능|예|부분|
| 균일하지 않은 입력 GOP에 대한 지원|예|아니요 - 입력에 고정된 GOP 지속 기간이 있어야 함|
| 변수 프레임 속도 입력에 대한 지원|예|아니요 - 입력은 고정된 프레임 속도여야 함. 예를 들어 움직임이 많은 장면 중에는 사소한 차이가 허용됩니다. 그러나 기여 피드는 프레임 속도를 15프레임/초 등으로 늦출 수 없습니다.|
| 입력 피드가 손실될 경우 라이브 이벤트 자동 차단|예|12시간 동안 LiveOutput 실행이 없는 경우|

## <a name="system-presets"></a>시스템 사전 설정

라이브 인코더의 출력에 포함되는 해상도 및 비트 전송률은 [presetName](/rest/api/media/liveevents/create#liveeventencoding)에 따라 결정됩니다. **표준** 라이브 인코더(LiveEventEncodingType.Standard)를 사용하는 경우 *Default720p* 사전 설정은 아래 설명된 것처럼 6개의 해상도/비트 전송률 쌍 세트를 지정합니다. 그렇지 않고 **Premium1080p** 라이브 인코더(LiveEventEncodingType.Premium1080p)를 사용하는 경우 *Default1080p* 사전 설정은 해상도/비트 전송률 쌍 출력 세트를 지정합니다.

> [!NOTE]
> 표준 라이브 인코딩에 대해 설정된 경우 Default1080p 사전 설정을 라이브 이벤트에 적용할 수 없으며 이 경우 오류가 발생합니다. 또한 Premium1080p 라이브 인코더에 Default720p 사전 설정을 적용하려고 해도 오류가 발생합니다.

### <a name="output-video-streams-for-default720p"></a>Default720p에 대한 출력 비디오 스트림

기여 피드가 720p 이상 해상도인 경우 **Default720p** 사전 설정은 다음 6개 레이어에 대한 피드를 인코딩합니다. 아래 표에서는 비트 전송률을 kbps로 나타내고, MaxFPS는 최대 허용 프레임 속도(프레임/초)를 나타내며 프로필은 사용된 H.264 프로필을 나타냅니다.

| Bitrate | 너비 | 높이 | MaxFPS | 프로필 |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |높음 |
| 2200 |960 |540 |30 |높음 |
| 1350 |704 |396 |30 |높음 |
| 850 |512 |288 |30 |높음 |
| 550 |384 |216 |30 |높음 |
| 200 |340 |192 |30 |높음 |

> [!NOTE]
> 라이브 인코딩 사전 설정을 사용자 지정해야 하는 경우 Azure Portal을 통해 지원 티켓을 엽니다. 원하는 비디오 해상도 및 비트 전송률 표를 지정해야 합니다. 오디오 인코딩 비트 전송률의 사용자 지정은 지원되지 않습니다. 720p에서 레이어가 하나만 있고, 6레이어 이하인지 확인합니다. 또한 미리 설정을 요청하도록 지정합니다.

### <a name="output-video-streams-for-default1080p"></a>Default1080p에 대한 출력 비디오 스트림

기여 피드가 1080p 이상 해상도인 경우 **Default1080p** 사전 설정은 다음 6개 레이어에 대한 피드를 인코딩합니다.

| Bitrate | 너비 | 높이 | MaxFPS | 프로필 |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |높음 |
| 3000 |1280 |720 |30 |높음 |
| 1600 |960 |540 |30 |높음 |
| 800 |640 |360 |30 |높음 |
| 400 |480 |270 |30 |높음 |
| 200 |320 |180 |30 |높음 |

> [!NOTE]
> 라이브 인코딩 사전 설정을 사용자 지정해야 하는 경우 Azure Portal을 통해 지원 티켓을 엽니다. 원하는 해상도 및 비트 전송률 표를 지정해야 합니다. 1080p에서 레이어가 하나만 있고, 6레이어 이하인지 확인합니다. 또한 Premium1080p 라이브 인코더에 대한 사전 설정을 요청하도록 지정합니다. 비트 전송률 및 해상도의 특정 값은 시간이 지남에 따라 조정될 수 있습니다.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Default720p 및 Default1080p에 대한 출력 오디오 스트림

*Default720p* 및 *Default1080p* 사전 설정 모두에서 오디오는 스테레오 AAC-LC(128kbps)로 인코딩됩니다. 샘플링 속도는 기여 피드의 오디오 트랙 샘플링 속도를 따릅니다.

## <a name="implicit-properties-of-the-live-encoder"></a>라이브 인코더의 암시적 속성

이전 섹션에서는 레이어 수, 해상도, 비트 전송률 등의 사전 설정을 통해 명시적으로 제어할 수 있는 라이브 인코더의 속성에 대해 설명합니다. 이 섹션에서는 암시적 속성을 명확하게 설명합니다.

### <a name="group-of-pictures-gop-duration"></a>GOP(사진 그룹) 기간

라이브 인코더는 기여 피드의 [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) 구조를 따릅니다. 즉, 출력 레이어가 동일한 GOP 기간을 갖게 됩니다. 따라서 GOP 기간이 고정된(일반적으로 2초) 기여 피드를 생성하도록 온-프레미스 인코더를 구성하는 것이 좋습니다. 이렇게 하면 서비스의 나가는 HLS 및 MPEG DASH 스트림에도 고정된 GOP 기간이 지정됩니다. 약간의 GOP 기간 변형은 대부분의 디바이스에서 허용될 수 있습니다.

### <a name="frame-rate"></a>프레임 속도

또한 라이브 인코더는 기여 피드의 개별 비디오 프레임 지속 시간을 따릅니다. 즉, 출력 계층에 동일한 기간의 프레임이 포함됩니다. 따라서 프레임 속도가 고정된(초당 30프레임 이내) 기여 피드를 생성하도록 온-프레미스 인코더를 구성하는 것이 좋습니다. 이렇게 하면 서비스의 나가는 HLS 및 MPEG DASH 스트림에도 고정된 프레임 속도가 지정됩니다. 약간의 프레임 속도 변형은 대부분의 디바이스에서 허용될 수 있지만 라이브 인코더가 올바르게 재생되는 출력을 생성한다는 보장이 없습니다. 온-프레미스 라이브 인코더는 어떤 방식으로도 프레임을 삭제하지 않아야 하며(예: 배터리 부족 상태에서) 프레임 속도가 달라지지 않아야 합니다.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>기여 피드 및 출력 레이어의 해상도

라이브 인코더는 기여 피드를 상향 주파수로 변환하지 않도록 구성됩니다. 따라서 출력 레이어의 최대 해상도는 기여 피드의 출력 크기를 초과하지 않습니다.

예를 들어, 720p의 기여 피드를 Default1080p 라이브 인코딩에 대해 구성된 라이브 이벤트에 보낼 경우 출력에는 5개 레이어(3Mbps의 720p부터 시작해서 200kbps의 1080p까지)만 있습니다. 또는 360p의 기여 피드를 표준 라이브 인코딩용으로 구성된 라이브 이벤트로 보내면 출력에 3개의 레이어(288p, 216p 및 192p의 해상도)가 포함됩니다. 중복 제거의 경우 160x90픽셀의 기여 피드를 표준 라이브 인코더로 보내면 출력에 기여 피드와 동일한 비트 전송률, 160x90 해상도의 레이어 1개가 포함됩니다.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>기여 피드 및 출력 레이어의 비트 전송률

라이브 인코더는 기여 피드의 비트 전송률에 관계 없이 사전 설정의 비트 전송률 설정을 적용하도록 구성됩니다. 결과적으로 출력 레이어의 비트 전송률이 기여 피드의 속도를 초과할 수 있습니다. 예를 들어, 1Mbps의 해상도 720p에서 기여 피드를 전송하면 출력 레이어는 위의 [표](live-event-types-comparison.md#output-video-streams-for-default720p)에 나오는 것과 동일하게 유지됩니다.

## <a name="next-steps"></a>다음 단계

[라이브 스트리밍 개요](live-streaming-overview.md)
