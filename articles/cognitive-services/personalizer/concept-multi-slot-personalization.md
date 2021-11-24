---
title: 다중 슬롯 개인 설정
description: Personalizer Rank 및 Reward API를 사용하여 단일 슬롯 및 다중 슬롯 개인 설정을 사용하는 위치와 시기에 대해 알아봅니다.
services: cognitive-services
author: jeffmend
ms.author: jeffme
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 05/24/2021
ms.custom: mode-other
ms.openlocfilehash: 3db9477172bfc3aa4b2bd8fa6676838be9911918
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133064247"
---
# <a name="multi-slot-personalization-preview"></a>다중 슬롯 개인 설정(미리 보기)

다중 슬롯 개인 설정(미리 보기)을 사용하면 사용자에게 둘 이상의 작업(예: 제품 또는 콘텐츠)이 표시되는 웹 레이아웃, 캐러셀 및 목록에서 콘텐츠를 대상으로 지정할 수 있습니다. Personalizer 다중 슬롯 API를 사용하면 Personalizer의 AI 모델이 사용자 인터페이스의 배치를 고려하고 학습하면서, 특정 동작을 구동하는 사용자 컨텍스트 및 제품을 학습할 수 있습니다. 예를 들어 Personalizer는 특정 제품 또는 콘텐츠가 페이지의 주요 강조 표시보다 사이드바 또는 바닥글로 더 많은 클릭을 발생시킨다는 것을 알 수 있습니다.

이 문서에서는 다중 슬롯 개인 설정이 결과를 향상시키는 이유, 사용하도록 설정하는 방법 및 사용 시기에 대해 알아봅니다. 이 문서에서는 사용자가 `Rank` 및 `Reward`와 같은 Personalizer API에 익숙하고 애플리케이션에서 사용하는 방법을 개념적으로 이해하고 있다고 가정합니다. Personalizer 및 작동 방식에 익숙하지 않은 경우 계속하기 전에 다음을 검토하세요.

* [Personalizer란?](what-is-personalizer.md) 
* [Personalizer의 작동 원리](how-personalizer-works.md) 

> [!IMPORTANT] 
>  다중 슬롯 개인 설정은 공개 미리 보기로 제공됩니다. 기능, 접근 방식 및 프로세스는 사용자 피드백에 따라 변경됩니다. 다중 슬롯 미리 보기를 사용하도록 설정하면 루프의 다른 Personalizer 기능이 영구적으로 사용하지 않도록 설정됩니다. Personalizer 루프에 대해 사용하도록 설정되면 다중 슬롯 개인 설정을 해제할 수 없습니다. 이 문서를 읽고 다중 슬롯 개인 설정을 위한 Personalizer 루프를 구성하기 전에 영향을 고려합니다.

<!-- 
We encourage you to provide feedback on multi-slot personalization APIs via ...
-->

## <a name="when-to-use-multi-slot-personalization"></a>다중 슬롯 개인 설정 사용 시기

사용자에게 제품 및/또는 콘텐츠를 표시할 때마다 고객에게 두 개 이상의 항목을 표시할 수 있습니다. 예를 들면 다음과 같습니다.

* **홈페이지용 웹 사이트 레이아웃**: 많은 타일 및 페이지 영역이 다양한 모양과 크기의 상자, 배너 및 사이드바에서 콘텐츠를 강조 표시하기 위해 사용됩니다. 다중 슬롯 개인 설정은 이 레이아웃의 특성이 고객의 선택 및 작업에 미치는 영향에 대해 학습합니다.
* **캐러셀**: 동적으로 변경되는 콘텐츠의 캐러셀에는 몇 가지 항목을 순환해야 합니다. 다중 슬롯 개인 설정은 시퀀스와 표시 기간이 클릭 및 참여에 미치는 영향을 학습할 수 있습니다.
* **관련 제품/콘텐츠 및 포함된 참조**: 배너, 사이드바, 비네트 및 바닥글 상자에 추가 콘텐츠 및 제품에 대한 참조를 포함하거나 섞어서 사용자를 연결하는 것이 일반적입니다. 다중 슬롯 개인 설정은 더 많은 사용을 유도할 가능성이 가장 높은 위치에 참조를 할당하는 데 도움이 될 수 있습니다.
* **검색 결과 또는 목록**: 결과를 목록 또는 타일로 제공하는 애플리케이션 검색 기능을 사용하는 경우 다중 슬롯 개인 설정을 사용하여 기존 순위 매기기보다 더 많은 메타데이터를 고려하여 위에서 강조 표시할 항목을 선택할 수 있습니다.
* **동적 채널 및 재생 목록**: 다중 슬롯 개인 설정은 비디오 또는 곡 목록이 동적 채널에서 다음에 재생되도록 짧은 시퀀스를 결정하는 데 도움이 될 수 있습니다.

다중 슬롯 개인 설정을 사용하면 사용자 인터페이스에서 작업을 선택해야 하는 "슬롯"을 선언할 수 있습니다. 이를 통해 Personalizer가 제품 배치를 향상시킬 수 있도록 슬롯에 대한 다음과 같은 자세한 정보를 제공할 수도 있습니다. 큰 상자 또는 작은 상자인가요? 캡션 또는 기능만 표시되나요? 바닥글 또는 사이드바에 있나요?


## <a name="how-to-use-multi-slot-personalization"></a>다중 슬롯 개인 설정을 사용하는 방법 

1. [다중 슬롯 개인 설정 사용](#enable-multi-slot-personalization)
1. [순위 요청에 대한 JSON 개체 만들기](#create-json-object-for-a-rank-request)
1. [슬롯 및 기준선 작업을 정의하는 Rank API 호출](#use-the-response-of-the-rank-api)
1. [Reward API 호출](#call-the-reward-api)


### <a name="enable-multi-slot-personalization"></a>다중 슬롯 개인 설정 사용 

다중 슬롯 개인 설정이 유용한지 여부를 이해하고 결정하려면 아래 [단일 슬롯과 다중 슬롯 개인 설정 간의 차이점](#differences-between-single-slot-and-multi-slot-personalization)을 참조하세요. *다중 슬롯 개인 설정은 미리 보기 기능임*: 다중 슬롯 개인 설정 API를 테스트하려는 경우에는 새 Personalizer 루프를 만드는 것이 좋습니다. 이는 되돌릴 수 없으며 프로덕션에서 실행되는 Personalizer 루프에 영향을 줍니다.

루프를 다중 슬롯 개인 설정으로 변환하기로 결정한 후에는 이 Personalizer 루프에 대해 다음 단계를 한 번 수행해야 합니다.

[!INCLUDE [Upgrade Personalizer instance to Multi-Slot](./includes/upgrade-personalizer-multi-slot.md)]

### <a name="create-json-object-for-a-rank-request"></a>순위 요청에 대한 JSON 개체 만들기
다중 슬롯 개인 설정을 사용하려면 단일 슬롯 개인 설정 API와 약간 다른 API가 필요합니다.

각 순위 호출 요청의 슬롯 개체에서 작업을 할당할 수 있는 슬롯을 선언합니다.

* **슬롯 배열**: 슬롯 배열을 선언해야 합니다. 슬롯이 *정렬됨*: 배열에서 각 슬롯의 위치가 중요합니다. 각 슬롯에 적용하려는 보상/클릭/변환 수에 따라 슬롯 정의가 가장 많이 적용되는 것부터 순서를 지정하는 것이 좋습니다. 예를 들어 큰 홈페이지 "hero" 상자를 웹 사이트에 작은 바닥글이 아닌 슬롯 1로 배치합니다.  다른 모든 것이 동일하면 Personalizer는 시퀀스 앞부분에서 보상을 받을 가능성이 더 큰 작업을 할당합니다.
* **슬롯 ID**: 각 슬롯에 slotId를 제공해야 합니다. 이 순위 호출에서 다른 모든 슬롯에 대해 고유한 문자열입니다.
* **슬롯 기능**: 해당 슬롯에 대해 설명하고 다른 슬롯과 구분하는 추가 메타데이터를 제공해야 합니다. 이를 *기능* 이라고 합니다. 슬롯 기능을 결정할 때 컨텍스트 및 작업의 기능에 권장되는 동일한 지침을 따라야 합니다(참조: [컨텍스트 및 작업에 대한 기능]()).  일반적인 슬롯 기능은 사용자 인터페이스 요소의 크기, 위치 또는 시각적 특성을 식별하는 데 도움이 됩니다. 예를 들어 `position: "top-left"`, `size: "big"`, `animated: "no"`, `sidebar: "true"` 또는 `sequence: "1"`입니다.
* **기준선 작업**: 각 슬롯에 대한 기준선 작업 ID를 지정해야 합니다. 즉, Personalizer가 존재하지 않는 경우 해당 슬롯에 표시될 작업의 ID입니다. 이는 Personalizer를 [실습생 모드]()로 학습시키고 [오프라인 평가]()를 수행하는 경우 의미 있는 숫자를 갖기 위해 필요합니다.
* **충분한 작업 사용**: Personalizer가 각 슬롯에 하나 이상의 작업을 할당할 수 있도록 슬롯보다 더 많은 작업을 사용하여 순위를 호출해야 합니다. Personalizer는 슬롯 간에 작업 권장 사항을 반복하지 않습니다. 순위 응답은 각 작업을 하나의 슬롯에만 할당합니다. 

시간이 지남에 따라 슬롯을 추가 또는 제거하거나, 해당 기능을 추가 및 변경하거나, 배열의 순서를 다시 지정할 수 있습니다. Personalizer는 새 정보에 따라 학습을 채택하고 유지합니다.

다음은 몇 가지 기능 예를 포함하는 `slots` 개체 예입니다. 거의 모든 `slots` 개체는 UI가 천천히 변경되므로 안정적이며 대부분 자주 변경되지 않습니다. 그러나 각 순위 호출에 적절한 baselineAction ID를 할당해야 합니다. 


```json
"slots": [ 
    { 
      "id": "BigHighlight", 
      "features": [ 
            { 
              "size": "Large", 
              "position": "Left-Middle" 
            }
        ],
        "baselineAction": "BlackBoot_4656" 
    }, 

    { 
      "id": "Sidebar1", 
      "features": [ 
            { 
              "size": "Small", 
              "position": "Right-Top" 
            } 
        ],
        "baselineAction": "TrekkingShoe_1122"  
    }  
  ]

```


### <a name="use-the-response-of-the-rank-api"></a>Rank API의 응답 사용

위의 요청에서 다중 슬롯 순위 응답은 다음과 같습니다. 

```json
{ 
  "slots": [ 
        { 
          "id": "BigHighlight", 
          "rewardActionId": "WhiteSneaker_8181" 
        }, 
        { 
          "id": "SideBar1", 
          "rewardActionId": "BlackBoot_4656" 
        } 
    ], 
  "eventId": "123456D0-BFEE-4598-8196-C57383D38E10" 
} 
```

각 슬롯에 대해 rewardActionId를 사용하여 사용자 인터페이스를 적절하게 렌더링합니다.

### <a name="call-the-reward-api"></a>Reward API 호출

Personalizer는 획득한 보상을 최대화할 작업을 선택하는 방법을 학습합니다. 애플리케이션은 사용자 행동을 관찰하고 관찰된 반응에 따라 Personalizer에 대한 "보상 점수"를 계산합니다. 예를 들어 사용자가 `"slotId": "SideBar1",`에서 작업을 클릭한 경우 Personalizer에 "1"을 전송하여 작업 선택에 대한 긍정적인 강화를 제공합니다.

Reward API는 URL의 보상에 대한 eventId를 지정합니다.

```http
https://{endpoint}/personalizer/v1.0/events/{eventId}/reward
``` 

예를 들어 위의 이벤트에 대해 ID가 123456D0-BFEE-4598-8196-C57383D38E10/reward인 보상은 `https://{endpoint}/personalizer/v1.0/events/123456D0-BFEE-4598-8196-C57383D38E10/reward/reward`로 전송됩니다.

```json
{ 
  "reward": [ 
    { 
      "slotId": "BigHighlight", 
      "value": 0.2 
    }, 
    { 
      "slotId": "SideBar1", 
     "value": 1.0 
    }, 
  ] 
} 
```

Reward API를 한 번만 호출하는 경우 모든 보상 점수를 제공할 필요는 없습니다. 적절한 eventId 및 slotId를 사용하여 Reward API를 여러 번 호출할 수 있습니다. 이벤트에서 슬롯에 대한 보상 점수가 수신되지 않으면 Personalizer는 루프에 대해 구성된 기본 보상(일반적으로 0)을 암시적으로 할당합니다.


## <a name="differences-between-single-slot-and-multi-slot-personalization"></a>단일 슬롯과 다중 슬롯 개인 설정 간의 차이점

단일 슬롯 및 다중 슬롯의 개인 설정에서 Rank 및 Reward API를 사용하는 방법에는 차이가 있습니다.

|  설명  | 단일 슬롯 개인 설정   | 다중 슬롯 개인 설정    |  
|---------------|-------------------------------|-------------------------------|
| Rank API 호출 요청 요소 | 컨텍스트 개체 및 작업 목록을 보냅니다.  | 컨텍스트, 작업 목록 및 순서가 지정된 슬롯 목록을 보냅니다.     | 
| 기준선을 지정하는 순위 요청 | Personalizer는 작업 목록의 첫 번째 작업을 기준선 작업(Personalizer가 없는 경우 애플리케이션이 선택한 항목)으로 수행합니다.|각 슬롯에 사용된 기준선 ActionID를 지정해야 합니다.|
| Rank API 호출 응답 | 애플리케이션이 rewardActionId 필드에 표시된 작업을 강조 표시합니다.     | 응답에는 요청에 지정된 각 슬롯에 대해 다른 rewardActionId가 포함됩니다. 애플리케이션은 각 슬롯에 있는 해당 rewardActionId 작업을 표시합니다. | 
| Reward API 호출 | 사용자가 이 특정 eventId에 대해 rewardActionId와 상호 작용하는 방법에 따라 계산된 보상 점수를 통해 Reward API를 호출합니다. 예를 들어 사용자가 클릭한 경우 1의 보상을 보냅니다.     | rewardActionId가 있는 작업이 원하는 사용자 동작을 얼마나 잘 유도했는지에 따라 각 슬롯에 대해 보상을 지정합니다. 동일한 eventId가 있는 하나 이상의 Reward API 호출에서 보낼 수 있습니다.   | 


### <a name="impact-of-enabling-multi-slot-for-a-personalizer-loop"></a>Personalizer 루프에 다중 슬롯을 사용하도록 설정하면 미치는 영향

추가적으로 다중 슬롯을 사용하도록 설정하는 경우 다음을 고려하세요.

|  설명  | 단일 슬롯 개인 설정   | 다중 슬롯 개인 설정    | 
|---------------|-------------------------------|-------------------------------|
| 비활성 이벤트 및 활성화    | 활성 API를 호출하는 경우 Personalizer는 보상 대기 시간이 초과되는 경우 보상 점수를 예상하거나 구성된 기본 보상을 할당하여 이벤트를 활성화합니다. | Personalizer는 eventId에 지정된 모든 슬롯에 대한 보상을 활성화하고 예상합니다. |     
| 실습생 모드 | Personalizer Rank API는 항상 기준선 작업을 반환하고 기준선 작업을 모방하여 내부 모델을 학습합니다.| Personalizer Rank API는 baselineAction 필드에 지정된 각 슬롯에 대한 기준선 작업을 반환합니다. Personalizer는 최초 모방에 대한 내부 모델을 학습합니다. |
| 학습 속도 | 강조 표시된 작업 중 하나만 학습 | 모든 슬롯과의 상호 작용에서 학습할 수 있습니다. 이는 일반적으로 보상을 얻을 수 있는 더 많은 사용자 동작을 의미하므로 Personalizer가 더 빠르게 학습할 수 있도록 합니다. |  
| 오프라인 평가 |선택한 작업에 따라 기준선 및 최적화된 학습 설정에 대해 Personalizer의 성능을 비교합니다.| (미리 보기 제한 사항) 배열에서 첫 번째 슬롯의 성능만 평가합니다. 보다 정확한 평가를 위해 대부분의 보상을 가진 슬롯이 배열의 첫 번째 슬롯인지 확인하는 것이 좋습니다. |
| 자동 최적화(미리 보기) | Personalizer 루프가 백그라운드에서 주기적으로 오프라인 평가를 수행하고, 관리자 개입 없이 학습 설정을 최적화할 수 있습니다. | (미리 보기 제한 사항) 다중 슬롯 API를 사용하는 Personalizer 루프에 대해 자동 최적화가 사용하지 않도록 설정됩니다. |

## <a name="next-steps"></a>다음 단계
* [다중 슬롯 개인 설정을 사용하는 방법](how-to-multi-slot.md)
* [샘플: 다중 슬롯 개인 설정 루프를 실행하는 Jupyter Notebook](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer/azure-notebook)
