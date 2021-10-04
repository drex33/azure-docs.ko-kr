---
title: 기계 학습 공정성(미리 보기)
titleSuffix: Azure Machine Learning
description: 기계 학습 공정성과 Fairlearn Python 패키지가 불공정성을 평가하고 완화하는 데 어떻게 도움이 되는지 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 04/02/2021
ms.custom: responsible-ml
ms.openlocfilehash: 34c8a18d842ac9e362bd81a522522ea9e845b06e
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129426890"
---
# <a name="machine-learning-fairness-preview"></a>기계 학습 공정성(미리 보기)

기계 학습 공정성에 대해 알아보고 [Fairlearn](https://fairlearn.github.io/) 오픈 소스 Python 패키지가 기계 학습 모델의 불공정 문제를 평가하고 완화하는 데 어떻게 도움이 되는지 알아봅니다. 

## <a name="what-is-machine-learning-fairness"></a>기계 학습 공정성이란 무엇인가요?

AI와 기계 학습 시스템이 불공정한 동작을 보일 수 있습니다. 불공정한 동작을 정의하는 한 가지 방법은 사람들에게 미치는 피해 또는 영향입니다. AI 시스템은 여러 가지 유형의 피해를 입힐 수 있습니다. 자세한 내용은 [Kate Crawford의 NeurIPS 2017 키 노트](https://www.youtube.com/watch?v=fMym_BKWQzk)를 참조하세요.

다음 두 가지는 AI로 인한 대표적인 피해 유형입니다.

- 할당 피해: AI 시스템이 특정 그룹에 대한 기회, 리소스 또는 정보를 확장하거나 알려주지 않습니다. 예를 들어 채용, 입학 및 대출 심사에서 모델이 다른 그룹보다 특정 그룹에서 유력 후보를 선택하는 경향을 보일 수 있습니다.

- 서비스 품질 피해: AI 시스템이 특정 그룹에 대해서는 다른 그룹에 하는 것처럼 작동하지 않습니다. 예를 들어 음성 인식 시스템이 여성에게는 남성에게 하듯이 작동하지 않을 수 있습니다.

AI 시스템의 불공정한 동작을 줄이려면 이러한 피해를 평가하고 완화해야 합니다.

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>Fairlearn을 사용한 공정성 평가 및 완화

Fairlearn은 기계 학습 시스템 개발자가 시스템의 공정성을 평가하고 불공정성을 완화할 수 있는 오픈 소스 Python 패키지입니다.

>[!NOTE]
> 공정성은 사회 기술적 과제입니다. 공평, 적법 절차 등 공정성의 여러 측면은 정량적 공정성 메트릭으로 캡처할 수 없습니다. 또한 여러 정성적 공정성 메트릭을 모두 동시에 만족할 수는 없습니다. Fairlearn 오픈 소스 패키지의 목표는 인간이 다양한 영향 및 완화 전략을 평가할 수 있도록 하는 것입니다. 궁극적으로 인공 지능 및 기계 학습 모델을 구축하여 시나리오에 적절한 절충안을 만드는 것은 인간 사용자의 몫입니다.

Fairlearn 오픈 소스 패키지에는 두 가지 구성 요소가 있습니다.

- 평가 대시보드: 모델의 예측이 다른 그룹에 어떤 영향을 주는지 평가하기 위한 Jupyter Notebook 위젯입니다. 또한 공정성 및 성능 메트릭을 사용하여 여러 모델을 비교할 수 있습니다.
- 완화 알고리즘: 이진 분류 및 회귀의 불공정을 완화하는 알고리즘 세트입니다.

두 구성 요소가 함께 작동하여 데이터 과학자 및 비즈니스 리더가 공정성과 성능 사이에서 적절히 타협하고, 요구 사항에 가장 적합한 완화 전략을 선택할 수 있도록 도와줍니다.

## <a name="assess-fairness-in-machine-learning-models"></a>기계 학습 모델의 공정성 평가

Fairlearn 오픈 소스 패키지에서 공정성은 **그룹 공정성** 이라고 하는 방법을 사용하여 개념화되며, 이 방법에서는 피해를 입을 위험에 노출되는 개인 그룹은 누구인가요?라는 질문을 던집니다. 아집단이라고도 하는 관련 그룹은 **중요한 기능** 또는 중요한 특성을 통해 정의됩니다. 민감 정보는 `sensitive_features`라는 벡터 또는 행렬로 Fairlearn 오픈 소스 패키지의 예측 도구에 전달됩니다. 이 용어는 시스템 디자이너가 그룹 공정성을 평가할 때 이러한 기능을 중요하게 생각해야 한다는 의미입니다. 

이러한 기능과 관련하여 비공개 데이터로 인한 프라이버시 침해 여부에 주의해야 합니다. 하지만 "중요한"이라는 단어는 이러한 기능을 예측에 사용하면 안 된다는 의미가 아닙니다.

>[!NOTE]
> 공정성 평가는 순전히 기술적인 활동이 아닙니다.  Fairlearn 오픈 소스 패키지는 모델의 공정성을 평가하는 데 도움이 될 수 있지만 평가를 수행하지는 않습니다.  Fairlearn 오픈 소스 패키지는 공정성을 평가하기 위한 정량적 메트릭을 식별하는 데 도움이 되지만 개발자는 자체 모델의 공정성을 평가하기 위해 정성적 분석도 수행해야 합니다.  위에서 언급한 민감 정보는 이러한 종류의 정성적 분석의 한 예입니다.     

평가 단계에서 공정성은 차이 메트릭을 통해 정량화됩니다. **차이 메트릭** 은 여러 그룹에서 모델의 동작을 평가하고 비율 또는 차이로 비교할 수 있습니다. Fairlearn 오픈 소스 패키지는 두 가지 클래스의 불일치 메트릭을 지원합니다.


- 모델 성능의 차이: 이 메트릭 세트는 여러 하위 그룹에서 선택한 성능 메트릭 값의 차이를 계산합니다. 일부 사례:

  - 정확도 차이
  - 오류율 차이
  - 정확도 차이
  - 재현율 차이
  - MAE 차이
  - 기타

- 선택 비율의 차이: 이 메트릭은 여러 하위 그룹 간의 선택 비율 차이를 포함합니다. 대출 승인율의 차이를 예로 들 수 있습니다. 선택 비율은 각 클래스에서 1로 분류된 데이터 요소의 비율(이진 분류) 또는 예측 값의 분포(회귀)를 의미합니다.

## <a name="mitigate-unfairness-in-machine-learning-models"></a>기계 학습 모델의 불공정 완화

### <a name="parity-constraints"></a>패리티 제약 조건

Fairlearn 오픈 소스 패키지에는 다양한 불공정 완화 알고리즘이 포함되어 있습니다. 이러한 알고리즘은 예측 요인의 동작에 대한 **패리티 제약 조건** 또는 조건이라는 제약 조건 세트를 지원합니다. 패리티 제약 조건을 사용하려면 중요한 기능이 정의하는 그룹(예: 여러 인종)에서 예측 요인 동작의 일부 측면을 비교할 수 있어야 합니다. Fairlearn 오픈 소스 패키지의 완화 알고리즘은 이러한 패리티 제약 조건을 사용하여 관찰된 공정성 문제를 완화합니다.

>[!NOTE]
> 모델에서 불공정을 완화한다는 것은 불공정을 줄이는 것을 의미하지만, 이러한 기술적 완화가 이러한 불공정을 완전히 제거할 수는 없습니다.  Fairlearn 오픈 소스 패키지의 불공정 완화 알고리즘은 기계 학습 모델에서 불공정을 줄이는 데 도움이 되는 제안 완화 전략을 제공할 수 있지만 불공정을 완전히 제거하는 솔루션은 아닙니다.  각 특정 개발자의 기계 학습 모델에 대해 고려해야 하는 다른 패리티 제약 조건 또는 기준이 있을 수 있습니다. Azure Machine Learning을 사용하는 개발자는 완화 조치가 기계 학습 모델의 의도된 사용 및 배포에서 불공정성을 충분히 제거하는지 스스로 결정해야 합니다.  

Fairlearn 오픈 소스 패키지는 다음 유형의 패리티 제약 조건을 지원합니다. 

|패리티 제약 조건  | 목적  |기계 학습 작업  |
|---------|---------|---------|
|인구 통계 패리티     |  할당 피해 완화 | 이진 분류, 회귀 |
|균등 배당률  | 할당 및 서비스 품질 피해 진단 | 이진 분류        |
|평등한 기회 | 할당 및 서비스 품질 피해 진단 | 이진 분류        |
|제한된 그룹 손실     |  서비스 품질 피해 완화 | 회귀 |

### <a name="mitigation-algorithms"></a>완화 알고리즘

Fairlearn 오픈 소스 패키지는 후처리 및 불공정 감소 완화 알고리즘을 제공합니다.

- 축소: 이 알고리즘은 표준 블랙박스 기계 학습 예측 도구(예: LightGBM 모델)를 통해 일련의 재가중치 학습 데이터 세트를 사용하여 재학습된 모델을 생성합니다. 예를 들어 특정 성별의 신청자에게 가중치를 더하거나 빼서 모델을 다시 학습시켜 성별 그룹 간의 차이를 줄일 수 있습니다. 그러면 사용자는 정확도(또는 다른 성능 메트릭)와 차이 간에 적절하게 절충하는 모델을 선택할 수 있으며, 일반적으로 절충은 비즈니스 규칙과 비용 계산을 기반으로 해야 합니다.  
- 후처리: 이 알고리즘은 기존 분류자와 중요한 기능을 입력으로 사용합니다. 그런 다음, 분류자의 예측을 변환하여 지정된 공정성 제약 조건을 적용합니다. 임계값 최적화의 가장 큰 장점은 모델을 다시 학습시킬 필요가 없기 때문에 간단하고 유연하다는 점입니다. 

| 알고리즘 | Description | 기계 학습 작업 | 중요한 기능 | 지원되는 패리티 제약 조건 | 알고리즘 유형 |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | [공정 분류에 대한 축소 접근 방식](https://arxiv.org/abs/1803.02453)에 설명된 공정성 분류에 대한 블랙박스 접근 방식 | 이진 분류 | 범주 | [인구 통계 패리티](#parity-constraints), [균등 배당률](#parity-constraints) | 축소 |
| `GridSearch` | [공정 분류에 대한 축소 접근 방식](https://arxiv.org/abs/1803.02453)에 설명된 블랙박스 접근 방식| 이진 분류 | 이진 | [인구 통계 패리티](#parity-constraints), [균등 배당률](#parity-constraints) | 축소 |
| `GridSearch` | [공정 회귀: 정량적 정의 및 축소 기반 알고리즘](https://arxiv.org/abs/1905.12843)에 설명된 제한된 그룹 손실에 대한 알고리즘을 사용하여 공정 회귀의 그리드 검색 변형을 구현하는 블랙박스 접근 방식 | 회귀 | 이진 | [제한된 그룹 손실](#parity-constraints) | 축소 |
| `ThresholdOptimizer` | [감독 학습의 기회 균등](https://arxiv.org/abs/1610.02413) 논문을 기반으로 하는 후처리 알고리즘입니다. 이 기술은 기존 분류자와 중요한 기능을 입력으로 사용하고, 분류자의 예측을 모노톤으로 변환하여 지정된 패리티 제약 조건을 적용합니다. | 이진 분류 | 범주 | [인구 통계 패리티](#parity-constraints), [균등 배당률](#parity-constraints) | 후처리 |

## <a name="next-steps"></a>다음 단계

- Fairlearn의 [GitHub](https://github.com/fairlearn/fairlearn/), [사용자 가이드](https://fairlearn.github.io/main/user_guide/index.html), [예](https://fairlearn.github.io/main/auto_examples/index.html) 및 [샘플 Notebooks](https://github.com/fairlearn/fairlearn/tree/master/notebooks)를 확인하여 다양한 구성 요소를 사용하는 방법을 알아봅니다.
- Azure Machine Learning에서 기계 학습 모델의 공정성 평가를 사용하도록 설정하는 [방법](how-to-machine-learning-fairness-aml.md)을 알아봅니다.
- Azure Machine Learning의 추가 공정성 평가 시나리오는 [샘플 Notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)를 참조하세요. 
