---
title: 비디오 의류에서 사람들이 감지됨
description: 이 항목에서는 비디오 의류 기능에서 감지된 사람의 개요를 제공합니다.
ms.topic: conceptual
ms.date: 11/15/2021
ms.author: juliako
ms.openlocfilehash: 4bc0c0fd21a68d80bf8c8803f1a64e6e0ea097c7
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495082"
---
# <a name="peoples-detected-clothing-preview"></a>사람들이 검색한 의류(미리 보기)

Media용 Video Analyzer는 비디오에서 의류를 신고 있는 사람과 관련된 의류를 감지하고 감지된 의류 유형 및 모양 타임스탬프(시작, 끝)와 같은 정보를 제공합니다. API는 검색 신뢰도 수준을 반환합니다.
 
이 기능이 유용할 수 있는 두 가지 예는 다음과 같습니다.
 
* 콘텐츠 작성자를 위한 비디오 광고, 뉴스 또는 스포츠 게임과 같은 원시 데이터를 만들 때 효율성을 개선합니다(예: 비디오 아카이브에서 빨간색 티셔츠를 입고 있는 사람 찾기).
* 이벤트 후 분석 - 사고 또는 범죄 사후 이벤트(예: 폭발, 은행 강도, 인시던트)를 더 잘 분석하기 위해 사람의 움직임을 감지하고 추적합니다.
 
고급 **옵션 고급** 비디오 또는 고급 비디오 + 오디오 사전  ->  **설정(비디오** **+ 오디오** 인덱싱 아래)을 선택하여 파일을 인덱싱할 때 새로 추가된 의류 감지 기능을 사용할 수 있습니다. 표준 인덱싱에는 이 새로운 고급 모델이 포함되지 않습니다.
 
:::image type="content" source="./media/detected-clothing/index-video.png" alt-text="이 스크린샷은 인덱싱 비디오 옵션을 나타냅니다.":::  

[비디오 분석기 for Media(이전](https://www.videoindexer.ai/) **Video Indexer)** 웹 사이트에서 비디오 Insights 보도록 선택하면 관찰된 사람 추적 인사이트에서 **People's detected** clothing을 볼 수 있습니다. 사람의 썸네일을 선택할 때 검색된 의류를 사용할 수 있게 되었습니다.

:::image type="content" source="./media/detected-clothing/observed-people.png" alt-text="관찰된 사람 스크린샷":::  
 
미디어용 비디오 분석기 웹 사이트의 비디오 타임라인에서 사람의 감지된 의류를 보려면 보기 Insights **보기로** 이동하여  ->   **모두** 옵션 또는 사용자 지정 **보기** 를  ->   선택하고 **관찰된 사람 을** 선택합니다. 

:::image type="content" source="./media/detected-clothing/observed-person.png" alt-text="관찰된 사람 스크린샷":::  
 
특정 의류를 검색하여 관찰된 모든 사람을 반환하려면 **Insights** 검색 표시줄을 사용하거나 Media용 Video Analyzer 웹 사이트의 비디오 **타임라인에서** 사용할 수 있습니다.

다음 JSON 응답은 관찰된 사람들이 관련된 의류를 감지한 것을 추적할 때 Media용 Video Analyzer가 반환하는 내용을 보여 줍니다.

```json
"observedPeople": [
    {
        "id": 1,
        "thumbnailId": "68bab0f2-f084-4c2b-859b-a951ed03c209",
        "clothing": [
            {
                "id": 1,
                "type": "sleeve",
                "properties": {
                    "length": "short"
                }
            },
            {
                "id": 2,
                "type": "pants",
                "properties": {
                    "length": "long"
                }
            }
        ],
        "instances": [
            {
                "adjustedStart": "0:00:05.5055",
                "adjustedEnd": "0:00:09.9766333",
                "start": "0:00:05.5055",
                "end": "0:00:09.9766333"
            }
        ]
    },
    {
        "id": 2,
        "thumbnailId": "449bf52d-06bf-43ab-9f6b-e438cde4f217",
        "clothing": [
            {
                "id": 1,
                "type": "sleeve",
                "properties": {
                    "length": "short"
                }
            },
            {
                "id": 2,
                "type": "pants",
                "properties": {
                    "length": "long"
                }
            }
        ],
        "instances": [
            {
                "adjustedStart": "0:00:07.2072",
                "adjustedEnd": "0:00:10.5105",
                "start": "0:00:07.2072",
                "end": "0:00:10.5105"
            }
        ]
    },
]
```

## <a name="limitations-and-assumptions"></a>제한 및 가정

가음성(누락된 검색)의 영향을 방지하거나 완화하려면 검색된 의류의 제한에 유의해야 합니다.
 
* 감지기 결과를 최적화하려면 정적 카메라의 비디오 장면을 사용합니다(이동식 카메라 또는 혼합 된 장면도 결과를 제공).
* 사람이 작게 표시되는지 검색되지 않습니다(최소 사람 키는 200픽셀).
* 최대 프레임 크기는 HD입니다.
* 사람들이 서 있거나 지고 있지 않으면 감지되지 않습니다.
* 저화질 비디오(예: 어두운 조명 조건)는 감지 결과에 영향을 미칠 수 있습니다.
* 최소 30 FPS의 권장 프레임 속도입니다.
* 권장 비디오 입력은 단일 프레임에 최대 10명의 사람을 포함해야 합니다. 이 기능은 단일 프레임에서 더 많은 사람을 감지할 수 있지만 한 프레임에서 최대 10명의 사람을 검색한 감지 결과의 신뢰도가 가장 높습니다.
* 비슷한 옷을 입은 사람(예: 유니폼을 입은 사람, 스포츠 경기의 선수)은 동일한 ID 번호를 가진 동일한 사람으로 감지될 수 있습니다.
* 폐색 – 폐색(장면/자체 또는 다른 사람에 의한 폐색)이 있는 오류가 있을 수 있습니다.
* 포즈: 서로 다른 포즈(앞/뒤)로 인해 트랙이 분할될 수 있습니다.

## <a name="next-steps"></a>다음 단계 

[비디오에서 관찰된 사람 추적](observed-people-tracing.md)
