---
title: Personalizer 작동 방식 - Personalizer
description: Personalizer _루프_ 는 기계 학습을 사용하여 콘텐츠의 상위 작업을 예측하는 모델을 구축합니다. 모델은 순위 및 보상 호출을 통해 보낸 데이터에서만 교육됩니다.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 9c9d9f2b06a34feca2974bf97fb5d494c6105471
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829931"
---
# <a name="how-personalizer-works"></a>Personalizer의 작동 원리

Personalizer 리소스 _학습 루프_ 는 기계 학습을 사용하여 콘텐츠의 상위 작업을 예측하는 모델을 구축합니다. 모델은 **순위** 및 **보상** 호출을 통해 보낸 데이터에서만 교육됩니다. 모든 루프는 서로 완전히 독립적입니다.

## <a name="rank-and-reward-apis-impact-the-model"></a>순위 및 보상 API가 모델에 영향을 미침

_기능이 포함된 작업_ 및 _컨텍스트 기능_ 을 순위 API로 보냅니다. **Rank** API는 다음 중 하나를 사용하도록 결정합니다.

* _익스플로잇_: 과거 데이터에 따라 최상의 작업을 결정하는 현재 모델입니다.
* _탐색_: 상위 작업 대신 다른 작업을 선택합니다. Azure Portal에서 Personalizer 리소스에 대해 [이 비율을 구성](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt)합니다.

보상 점수를 결정하여 보상 API로 보냅니다. **Reward** API는 다음과 같습니다.

* 각 순위 호출의 기능 및 보상 점수를 기록하여 모델을 학습시키기 위한 데이터를 수집합니다.
* 이 데이터를 사용하여 _학습 정책_ 에 지정된 구성에 따라 모델을 업데이트합니다.

## <a name="your-system-calling-personalizer"></a>시스템에서 Personalizer 호출

다음 이미지에서는 Rank 호출 및 Reward 호출의 아키텍처 흐름을 보여 줍니다.

![대체 텍스트](./media/how-personalizer-works/personalization-how-it-works.png "개인 설정 작동 방식")

1. _기능이 포함된 작업_ 및 _컨텍스트 기능_ 을 순위 API로 보냅니다.

    * Personalizer에서 현재 모델을 활용할지, 아니면 모델에 대한 새 선택 항목을 검색할지를 결정합니다.
    * 순위 결과가 EventHub로 전송됩니다.
1. 상위 순위가 _보상 작업 ID_ 로 시스템에 반환됩니다.
    시스템에서 해당 콘텐츠를 표시하고 고유의 비즈니스 규칙에 따라 보상 점수를 결정합니다.
1. 시스템은 보상 점수를 학습 루프로 반환합니다.
    * Personalizer에서 보상을 받으면 해당 보상을 EventHub에 보냅니다.
    * 순위와 보상이 상호 관련됩니다.
    * AI 모델이 상관 관계 결과에 따라 업데이트됩니다.
    * 유추 엔진이 새 모델로 업데이트됩니다.

## <a name="personalizer-retrains-your-model"></a>Personalizer에서 모델 다시 학습

Personalizer는 Azure Portal의 Personalizer 리소스에 대한 **모델 빈도 업데이트** 설정을 기반으로 모델을 다시 학습시킵니다.

Personalizer는 Azure Portal의 Personalizer 리소스에 대한 **데이터 보존** 기간(일) 설정을 기반으로 현재 보존된 모든 데이터를 사용합니다.

## <a name="research-behind-personalizer"></a>Personalizer에 대한 연구

Personalizer는 Microsoft Research의 백서, 연구 활동 및 진행 중인 탐구 영역을 포함하여 [보충 학습](concepts-reinforcement-learning.md) 분야의 최첨단 과학 및 연구를 기반으로 합니다.

## <a name="next-steps"></a>다음 단계

Personalizer의 [상위 시나리오](where-can-you-use-personalizer.md)에 대해 알아보기