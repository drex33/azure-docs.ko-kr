---
title: 오디오 효과 감지
description: '오디오 효과 감지는 Azure Video Analyzer for Media AI 기능 중 하나입니다. 다양한 음향 이벤트를 감지하고 이를 다양한 음향 범주(예: 총성, 비명, 군중 반응 등)로 분류할 수 있습니다.'
ms.topic: conceptual
ms.date: 05/12/2021
ms.author: juliako
ms.openlocfilehash: 689abf9c61c0b1a462cfafe02c461906626e516b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128632541"
---
#  <a name="audio-effects-detection-preview"></a>오디오 효과 감지(미리 보기)

**오디오 효과 감지** 는 Azure Video Analyzer for Media AI 기능 중 하나입니다. 다양한 음향 이벤트를 감지하고 이를 다양한 음향 범주(예: 총성, 비명, 군중 반응 등)로 분류할 수 있습니다.
 
오디오 이벤트 감지는 여러 도메인에서 사용할 수 있습니다. 다음과 같은 두 가지 예가 있습니다.

* 오디오 효과 감지는 **공공 안전 및 사법** 영역에서 진행됩니다. 오디오 효과 감지는 총성, 폭발 및 투명 효과를 감지하고 분류할 수 있습니다. 따라서 스마트 도시 시스템 또는 카메라와 마이크를 포함하는 기타 퍼블릭 환경에서 구현할 수 있습니다. 위반 인시던트를 빠르고 정확하게 검색할 수 있습니다. 
* **미디어 및 엔터테인먼트** 영역에서 대량의 비디오 보관 파일 세트가 있는 회사에서는 음성이 아닌 효과로 비디오 내용을 개선하여 청력에 문제가 있는 사람들에게 더 많은 컨텍스트를 제공함으로써 접근성 시나리오를 쉽게 개선할 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/audio-effects-detection/audio-effects.jpg" alt-text="오디오 효과 이미지":::
<br/>*Video Analyzer for Media 오디오 효과 감지 출력 예제*

## <a name="supported-audio-categories"></a>지원되는 오디오 범주  

**오디오 효과 감지** 는 8가지 범주를 검색하고 분류할 수 있습니다. 다음 표에서는 다른 VI 사전 설정으로 분할되고 **표준** 및 **고급** 으로 구분된 다양한 범주를 찾을 수 있습니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/media-services/)을 참조하세요.

|인덱싱 유형 |표준 인덱싱| 고급 인덱싱|
|---|---|---|
|**사전 설정 이름** |**"오디오만"** <br/>**"비디오 + 오디오"** |**"오디오 이동"**<br/> **"고급 비디오 + 오디오"**|
|**인사이트 창에 표시**|| V|
|군중 반응 |V| V|
| 침묵| V| V|
| 총성 ||V |
| 깨진 유리 ||V|
| 경보 울림|| V |
| 사이렌 소리|| V |
| 웃음|| V |
| 개짓는 소리|| V|

## <a name="result-formats"></a>결과 형식

오디오 효과는 특정 기간 및 신뢰도 점수와 함께 범주 ID, 유형, 이름 및 범주별 인스턴스 세트를 포함하는 인사이트 JSON에서 검색됩니다.

`name` 매개 변수는 JSON이 인덱싱되는 언어로 표시되지만 형식은 항상 동일하게 유지됩니다.

```json
audioEffects: [{
        id: 0,
        type: "Gunshot",
        name: "Gunshot",
        instances: [{
                confidence: 0.649,
                adjustedStart: "0:00:13.9",
                adjustedEnd: "0:00:14.7",
                start: "0:00:13.9",
                end: "0:00:14.7"
            }, {
                confidence: 0.7706,
                adjustedStart: "0:01:54.3",
                adjustedEnd: "0:01:55",
                start: "0:01:54.3",
                end: "0:01:55"
            }
        ]
    }, {
        id: 1,
        type: "CrowdReactions",
        name: "Crowd Reactions",
        instances: [{
                confidence: 0.6816,
                adjustedStart: "0:00:47.9",
                adjustedEnd: "0:00:52.5",
                start: "0:00:47.9",
                end: "0:00:52.5"
            },
            {
                confidence: 0.7314,
                adjustedStart: "0:04:57.67",
                adjustedEnd: "0:05:01.57",
                start: "0:04:57.67",
                end: "0:05:01.57"
            }
        ]
    }
],
```

## <a name="how-to-index-audio-effects"></a>오디오 효과를 인덱싱하는 방법

사용자는 오디오 효과 검색을 포함 하도록 인덱스 프로세스를 설정 하기 위해 아래에서 볼 수 있는 것 처럼 "비디오 + 오디오 인덱싱" 메뉴의 고급 사전 설정 중 하나를 선택 해야 합니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/audio-effects-detection/index-audio-effect.png" alt-text="오디오 효과 인덱싱 이미지":::

## <a name="closed-caption"></a>선택 자막

선택 자막 파일에서 오디오 효과를 검색하는 경우 다음 구조를 대괄호로 묶어 검색합니다.

|Type| 예제|
|---|---|
|SRT |00:00:00,000  00:00:03,671<br/>[총성]|
|VTT |00:00:00.000  00:00:03.671<br/>[총성]|
|TTML|신뢰도: 0.9047 <br/> `<p begin="00:00:00.000" end="00:00:03.671">[Gunshot]</p>`|
|TXT |[총성]|
|CSV |0.9047,00:00:00.000,00:00:03.671, [총성]|

자막 파일의 오디오 효과는 다음 논리를 통해 검색됩니다.

* `Silence` 이벤트 유형은 자막에 추가되지 않습니다.
* 이벤트를 표시하는 최대 기간은 5초입니다.
* 이벤트를 표시하는 최소 타이머 기간은 700밀리초입니다.

## <a name="adding-audio-effects-in-closed-caption-files"></a>자막 파일에 오디오 효과 추가

`includeAudioEffects` 매개 변수에서 true를 선택하여 [비디오 자막 가져오기 API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Captions)를 통해 또는 **다운로드** -> **선택 캡션** -> **오디오 효과 포함** 을 선택하여 video.ai 포털 환경을 통해 Azure Video Analyzer에서 지원하는 선택 자막 파일에 오디오 효과를 추가할 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/audio-effects-detection/close-caption.jpg" alt-text="CC의 오디오 효과":::

> [!NOTE]
> 자막 파일에서 [대본 업데이트](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Transcript)를 사용하거나 자막 파일에서 [사용자 지정 언어 모델을 업데이트](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model)하는 경우 해당 파일에 포함된 오디오 효과는 무시됩니다.

## <a name="limitations-and-assumptions"></a>제한 사항 및 가정

* 모델은 비음성 세그먼트에서만 작동합니다.
* 모델은 현재 한 번에 하나의 범주에 대해서만 작동합니다. 예를 들어 백그라운드의 울음 소리와 말소리나 총성 및 폭발음은 현재 지원되지 않습니다.
* 이 모델은 현재 배경에 큰 소리의 음악이 들리는 경우를 지원하지 않습니다.
* 최소 세그먼트 길이 – 2초

## <a name="next-steps"></a>다음 단계

[개요](video-indexer-overview.md) 검토
