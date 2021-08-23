---
title: 비디오에서 관찰된 사람 추적
titleSuffix: Azure Video Analyzer for Media
description: 이 항목에서는 비디오 개념에서 관찰된 사람 추적에 대한 개요를 제공합니다.
services: azure-video-analyzer
author: Juliako
manager: femila
ms.service: azure-video-analyzer
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 04/30/2021
ms.author: juliako
ms.openlocfilehash: 469073650bbcf00ba2634ac5be68c10bf7ac49ee
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112115394"
---
# <a name="trace-observed-people-in-a-video"></a>비디오에서 관찰된 사람 추적

Video Indexer는 비디오에서 관찰된 사람을 감지하고 비디오 프레임에서 사람의 위치 및 사람이 나타나는 시간의 정확한 타임스탬프(시작, 종료)와 같은 정보를 제공합니다. API는 감지 신뢰도를 포함하여 감지된 각 사람 인스턴스에 대한 경계 상자 좌표(픽셀 단위)를 반환합니다.  
 
이 기능이 유용할 수 있는 몇 가지 시나리오는 다음과 같습니다.

* 이벤트 후 분석 - 사고 또는 범죄 사후 이벤트(예: 폭발, 은행 강도, 인시던트)를 더 잘 분석하기 위해 사람의 움직임을 감지하고 추적합니다.  
* 콘텐츠 작성자를 위한 비디오 광고, 뉴스 또는 스포츠 게임과 같은 원시 데이터를 만들 때 효율성을 개선합니다(예: 비디오 아카이브에서 빨간색 티셔츠를 입고 있는 사람 찾기).
* 감지된 동일 인물의 ID를 사용하여 비디오에서 특정인의 모습에 대한 법정 증거와 같은 긴 비디오의 요약본을 만듭니다.
* 고객이 쇼핑가에서 통로를 이동하는 방법 또는 결제 대기줄에서 소비하는 시간과 같은 시간별 추세를 알아보고 분석합니다.

예를 들어 비디오에 사람이 포함된 경우 감지 작업은 비디오 프레임의 좌표와 함께 사람의 모습을 나열합니다. 이 기능을 사용하여 비디오에서 사람의 경로를 확인할 수 있습니다. 또한 이 기능을 통해 비디오에 동일한 사람의 여러 인스턴스가 있는지 확인할 수 있습니다.

새로 추가된 **관찰된 사람 추적** 기능은 **고급 옵션** -> **고급 비디오** 또는 **고급 비디오 + 오디오** 사전 설정(**비디오 + 오디오 인덱싱**)을 선택하여 파일을 인덱싱할 때 사용할 수 있습니다. 표준 인덱싱에는 이 새로운 고급 모델이 포함되지 않습니다. 


:::image type="content" source="./media/observed-people-tracing/youtube-trailer.png" alt-text="관찰된 사람 추적 스크린샷":::  
 
[Video Indexer](https://www.videoindexer.ai/account/login) 웹 사이트에서 비디오의 **인사이트** 를 보도록 선택하면 감지된 모든 사람의 썸네일과 함께 관찰된 사람 추적이 페이지에 표시됩니다. 특정인의 썸네일을 선택하고 비디오 플레이어에서 해당 인물이 표시되는 위치를 확인할 수 있습니다. 

다음 JSON 응답은 관찰된 사람을 추적할 때 Video Indexer에서 반환하는 내용을 보여 줍니다. 

```json
    {
    ...
    "videos": [
        {
            ...
            "insights": {
                ...
                "observedPeople": [{
                    "id": 1,
                    "thumbnailId": "560f2cfb-90d0-4d6d-93cb-72bd1388e19d",
                    "instances": [
                        {
                            "adjustedStart": "0:00:01.5682333",
                            "adjustedEnd": "0:00:02.7027",
                            "start": "0:00:01.5682333",
                            "end": "0:00:02.7027"
                        }
                    ]
                },
                {
                    "id": 2,
                    "thumbnailId": "9c97ae13-558c-446b-9989-21ac27439da0",
                    "instances": [
                        {
                            "adjustedStart": "0:00:16.7167",
                            "adjustedEnd": "0:00:18.018",
                            "start": "0:00:16.7167",
                            "end": "0:00:18.018"
                        }
                    ]
                },]
            }
            ...
            }
    ]
}
```

## <a name="limitations-and-assumptions"></a>제한 및 가정 

거짓 부정(누락된 감지) 및 제한된 세부 정보의 영향을 방지하거나 완화할 수 있도록 관찰된 사람 추적의 제한 사항에 유의해야 합니다.

* 감지기 결과를 최적화하려면 정적 카메라의 비디오 장면을 사용합니다(이동식 카메라 또는 혼합 된 장면도 결과를 제공). 
* 일반적으로는 작게 표시되는 사람은 감지되지 않습니다(최소 사람 높이는 200픽셀).
* 최대 프레임 크기는 HD입니다.
* 서 있거나 걷고 있는 사람은 일반적으로 감지되지 않습니다. 
* 저품질 비디오(예: 어두운 조명 조건)는 감지 결과에 영향을 줄 수 있습니다. 
* 권장 프레임 속도는 최소 30FPS입니다. 
* 권장 비디오 입력은 단일 프레임에 최대 10명의 사람을 포함해야 합니다. 이 기능은 단일 프레임에서 더 많은 사람을 감지할 수 있지만 한 프레임에서 최대 10명의 사람을 검색한 감지 결과의 신뢰도가 가장 높습니다. 
* 비슷한 옷을 입은 사람(예: 유니폼을 입은 사람, 스포츠 경기의 선수)은 동일한 ID 번호를 가진 동일한 사람으로 감지될 수 있습니다. 
* 폐색 – 폐색(장면/자체 또는 다른 사람에 의한 폐색)이 있는 오류가 있을 수 있습니다.
* 포즈: 서로 다른 포즈(앞/뒤)로 인해 트랙이 분할될 수 있습니다.       

## <a name="next-steps"></a>다음 단계

[개요](video-indexer-overview.md) 검토
