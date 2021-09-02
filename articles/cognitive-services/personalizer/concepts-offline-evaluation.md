---
title: 오프라인 평가 방법 사용 - Personalizer
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 오프라인 평가를 사용하여 앱의 효과를 측정하고 학습 루프를 분석하는 방법에 대해 설명합니다.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 95217acd5c423c71eb75a43ee1e756d6b95b696d
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829778"
---
# <a name="offline-evaluation"></a>오프라인 평가

오프라인 평가는 코드를 변경하거나 사용자 환경에 영향을 주지 않고 Personalizer 서비스의 효율성을 테스트하고 평가할 수 있는 방법입니다. 오프라인 평가는 애플리케이션에서 Rank 및 Reward API로 보낸 과거 데이터를 사용하여 다른 순위에서 수행된 방식을 비교합니다.

오프라인 평가는 날짜 범위에서 수행됩니다. 범위의 끝은 현재 시간까지 늦을 수 있으며, 범위의 시작은 [데이터 보존](how-to-settings.md)에 지정된 일 수보다 클 수 없습니다.

오프라인 평가를 통해 다음과 같은 질문에 답할 수 있습니다.

* 성공적인 맞춤 설정을 위해 Personalizer 순위는 얼마나 효과적인가요?
    * Personalizer 온라인 기계 학습 정책으로 달성되는 평균 보상은 어떻게 되나요?
    * Personalizer는 애플리케이션에서 기본적으로 수행한 효율성과 어떻게 비교하나요?
    * 맞춤 설정에 대한 임의 선택의 비교 효율성은 어떻게 되나요?
    * 수동으로 지정된 여러 학습 정책의 비교 효율성은 어떻게 되나요?
* 성공적인 맞춤 설정에 어느 정도 기여하는 컨텍스트의 기능은 무엇인가요?
* 성공적인 맞춤 설정에 어느 정도 기여하는 작업의 기능은 무엇인가요?

또한 오프라인 평가를 사용하여 Personalizer에서 향후 결과를 향상시키는 데 사용할 수 있는 더 최적화된 학습 정책을 검색할 수 있습니다.

오프라인 평가는 검색에 사용할 이벤트의 비율에 대한 지침을 제공하지 않습니다.

## <a name="prerequisites-for-offline-evaluation"></a>오프라인 평가를 위한 필수 조건

대표적인 오프라인 평가를 위한 중요한 고려 사항은 다음과 같습니다.

* 충분한 데이터가 있습니다. 추천되는 이벤트의 최소 수는 50,000개입니다.
* 대표적인 사용자 동작 및 트래픽이 있는 기간의 데이터를 수집합니다.

## <a name="discovering-the-optimized-learning-policy"></a>최적화된 학습 정책 검색

Personalizer는 오프라인 평가 프로세스를 사용하여 최적의 학습 정책을 자동으로 검색할 수 있습니다.

오프라인 평가가 수행되면 현재 온라인 정책과 비교하여 새 정책을 사용한 Personalizer의 비교 효율성을 확인할 수 있습니다. 그런 다음 학습 정책을 다운로드하고 모델 및 정책 패널에 업로드하여 Personalizer에서 즉시 적용할 수 있습니다. 나중에 분석하거나 사용하기 위해 다운로드할 수도 있습니다.

평가에 포함된 현재 정책:

| 학습 설정 | 목적|
|--|--|
|**온라인 정책**| Personalizer에서 사용되는 현재 학습 정책입니다. |
|**기초**|애플리케이션의 기본값(Rank 호출에서 보낸 첫 번째 작업에 의해 결정)입니다.|
|**임의 정책**|제공된 작업 중에서 항상 임의의 작업을 선택하여 반환하는 가상 순위 동작입니다.|
|**사용자 지정 정책**|평가를 시작할 때 업로드되는 추가 학습 정책입니다.|
|**최적화된 정책**|최적화된 정책을 검색하는 옵션을 사용하여 평가를 시작한 경우 최적화된 정책 비교가 수행되며, 최적화된 정책을 다운로드하거나 온라인 학습 정책으로 만들어서 현재 정책을 대체할 수 있습니다.|

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>오프라인 평가 결과의 관련성 이해

오프라인 평가를 실행하는 경우 결과의 _신뢰 한계_ 를 반드시 분석해야 합니다. 신뢰 구간이 넓으면 애플리케이션에서 보상 추정이 정확하거나 유의미할 만큼 충분한 데이터를 받지 못했음을 의미합니다. 시스템에서 더 많은 데이터를 누적하고 오프라인 평가를 더 오랜 기간에 걸쳐 실행하면 신뢰 구간이 좁아집니다.

## <a name="how-offline-evaluations-are-done"></a>오프라인 평가 수행 방법

오프라인 평가는 **반사실적 평가** 라는 방법을 사용하여 수행됩니다.

Personalizer는 사용자의 동작(및 이에 따라 보상)을 소급하여 예측할 수 없고(사용자가 본 것과 다른 것을 표시했을 경우 Personalizer에서 발생한 상황을 인식할 수 없음) 측정된 보상을 통해서만 학습할 수 있다는 가정하에 빌드되었습니다.

평가에 사용되는 개념 프로세스는 다음과 같습니다.

```
[For a given _learning policy), such as the online learning policy, uploaded learning policies, or optimized candidate policies]:
{
    Initialize a virtual instance of Personalizer with that policy and a blank model;

    [For every chronological event in the logs]
    {
        - Perform a Rank call

        - Compare the reward of the results against the logged user behavior.
            - If they match, train the model on the observed reward in the logs.
            - If they don't match, then what the user would have done is unknown, so the event is discarded and not used for training or measurement.

    }

    Add up the rewards and statistics that were predicted, do some aggregation to aid visualizations, and save the results.
}
```

오프라인 평가는 관찰된 사용자 동작만 사용합니다. 이 프로세스는 특히 애플리케이션에서 많은 수의 작업을 사용하여 Rank 호출을 수행하는 경우 많은 양의 데이터를 삭제합니다.


## <a name="evaluation-of-features"></a>기능 평가

오프라인 평가는 더 높은 보상 가중치를 부여하고 있는 작업 또는 컨텍스트에 대한 특정 기능의 수에 대한 정보를 제공할 수 있습니다. 정보는 지정된 기간 및 데이터에 대한 평가를 사용하여 계산되며 시간에 따라 달라질 수 있습니다.

기능 평가를 살펴보고 다음과 같이 질문하는 것이 좋습니다.

* 더 효과적인 기능과 같은 맥락에서 애플리케이션 또는 시스템에서 추가로 제공할 수 있는 다른 기능은 무엇인가요?
* 낮은 효율성으로 인해 제거할 수 있는 기능은 무엇인가요? 효율성이 낮은 기능은 _노이즈_ 를 기계 학습에 추가합니다.
* 실수로 포함된 기능이 있나요? 예를 들어 사용자 식별 정보, 중복 ID 등이 있습니다.
* 규정 또는 사용 책임에 대한 고려 사항으로 인해 개인에 맞게 설정하는 데 사용할 수 없는 바람직하지 않은 기능이 있나요? 바람직하지 않은 기능을 프록시(즉, 밀접하게 미러링하거나 상관 관계 지정)할 수 있는 기능이 있나요?


## <a name="next-steps"></a>다음 단계

[Personalizer 구성](how-to-settings.md) 
 [오프라인 평가 실행](how-to-offline-evaluation.md) [Personalizer 작동 방식](how-personalizer-works.md) 이해
