---
title: 보충 학습 - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer는 작업 및 현재 컨텍스트에 대한 정보를 사용하여 보다 나은 순위를 추천합니다. 이러한 작업 및 컨텍스트에 대한 정보는 기능으로 참조되는 특성 또는 속성입니다.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.openlocfilehash: 6ad6414060bc744a85aaecfc8b9ceeac8dd57d30
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830759"
---
# <a name="what-is-reinforcement-learning"></a>보충 학습이란?

보충 학습은 사용 피드백을 받아서 동작을 학습하는 기계 학습 방법입니다.
 
보충 학습은 다음과 같은 방식으로 작동합니다.

* 의사 결정 또는 선택 같은 동작을 시행할 수 있는 기회 또는 자유도를 제공합니다.
* 환경 및 선택에 대한 컨텍스트 정보를 제공합니다.
* 동작이 특정 목표를 얼마나 잘 달성하는지 피드백을 제공합니다.

보충 학습에는 여러 하위 유형과 스타일이 있지만, Personalizer가 작동하는 개념 원리는 다음과 같습니다.

* 애플리케이션은 대안 목록의 콘텐츠 하나를 표시할 수 있는 기회를 제공합니다.
* 애플리케이션은 각 대안에 대한 정보와 사용자 컨텍스트를 제공합니다.
* 애플리케이션은 _보상 점수_ 를 컴퓨팅합니다.

다른 보충 학습 방법과는 달리, Personalizer는 시뮬레이션을 수행할 필요가 없습니다. Personalizer의 학습 알고리즘은 고유한 기회를 만들려면 시간과 비용이 들고 최적이 아닌 성능을 얻게 되면 후회를 할 수 밖에 없다는 점(가능한 보상의 손실)을 이해함으로써 외부 세계에 대응하고(제어하는 것이 아니라) 각 데이터 포인트에서 학습하도록 설계되었습니다.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Personalizer는 어떤 유형의 보충 학습 알고리즘을 사용하나요?

현재 버전의 Personalizer는 특정 컨텍스트에서 불연속 작업 간에 결정 또는 선택하도록 프레임이 설정된 보충 학습 방법인 **Contextual Bandits** 를 사용합니다.

특정 컨텍스트에서 가능한 최상의 결정을 내리도록 학습된 모델인 _의사 결정 메모리_ 는 선형 모델 세트를 사용합니다. 이 방식은 반복적으로 비즈니스 성과를 보여주었으며 부분적으로 검증된 방법입니다. 다중 패스 학습 없이 실제 세계로부터 매우 빠르게 배울 수 있으므로 부분적이고, 감독 학습 모델 및 심층 신경망을 보완할 수 있으므로 부분적입니다.

탐색/공격 트래픽 할당은 설정된 탐색 비율에 따라 임의로 만들어지고, 탐색에 대한 기본 알고리즘은 엡실론 그리디(Epsilon-Greedy)입니다.

### <a name="history-of-contextual-bandits"></a>Contextual Bandits의 역사

John Langford는 다루기 쉬운 보충 학습 하위 집합을 설명하기 위해 Contextual Bandits라는 신조어를 만들었으며(Langford 및 Zhang 공저 [2007]), 다음과 같은 패러다임을 익히는 방법에 대한 이해를 돕기 위해 6편의 논문을 발표했습니다.

* Beygelzimer 외 공저 [2011]
* Dudík 외 공저 [2011a,b]
* Agarwal 외 공저 [2014, 2012]
* Beygelzimer 및 Langford 공저 [2009]
* Li 외 공저 [2010]

John은 이전에도 Joint Prediction(ICML 2015), Contextual Bandit Theory(NIPS 2013), Active Learning(ICML 2009), Sample Complexity Bounds(ICML 2003) 등의 토픽에 대한 여러 자습서를 발표했습니다.

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Personalizer는 어떤 기계 학습 프레임워크를 사용하나요?

현재 Personalizer는 기계 학습의 기초로 [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki)를 사용합니다. 이 프레임워크는 모든 이벤트를 사용하여 개인 설정 순위를 지정하고 모델을 학습할 때 최대 처리량과 가장 낮은 대기 시간을 허용합니다.

## <a name="references"></a>참조

* [기술적인 문제가 적도록 상황에 맞는 결정 내리기](https://arxiv.org/abs/1606.03966)
* [공정 분류에 대한 축소 접근 방식](https://arxiv.org/abs/1803.02453)
* [움직이는 세계에서 효율적인 Contextual Bandits](https://arxiv.org/abs/1708.01799)
* [잔여 손실 예측: 보충: 증분 피드백 없이 학습](https://openreview.net/pdf?id=HJNMYceCW)
* [보충 학습을 사용하여 지침 및 시각적 관찰 결과를 작업에 매핑](https://arxiv.org/abs/1704.08795)
* [선생님보다 검색을 더 잘하는 방법 배우기](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>다음 단계

[오프라인 평가](concepts-offline-evaluation.md) 