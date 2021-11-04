---
title: 기계 학습 알고리즘을 선택하는 방법
titleSuffix: Azure Machine Learning
description: 클러스터링, 분류 또는 회귀 실험에서 감독 및 자율 학습에 대한 Azure Machine Learning 알고리즘을 선택하는 방법입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 10/21/2021
ms.openlocfilehash: cae79937f1faa36488820d6266484fb43330e821
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560799"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Azure Machine Learning을 위한 알고리즘을 선택하는 방법

일반적인 질문은 "어떤 기계 학습 알고리즘을 사용해야 하나요?"입니다. 선택하는 알고리즘은 주로 데이터 과학 시나리오의 두 가지 서로 다른 측면에 따라 결정됩니다.

 - **데이터로 어떤 작업을 수행할 예정인가요?** 구체적으로, 과거 데이터에서 학습하여 답변하고 싶은 비즈니스 질문은 무엇인가요?

 - **데이터 과학 시나리오의 요구 사항은 무엇인가요?** 구체적으로, 정확도, 학습 시간, 선형성, 매개 변수 수 및 솔루션이 지원하는 기능 수는 어떻게 되나요?

 ![알고리즘 선택 시 고려 사항: 무엇을 알고 싶나요? 시나리오 요구 사항은 무엇인가요?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>비즈니스 시나리오 및 Machine Learning 알고리즘 치트 시트

[Azure Machine Learning 알고리즘 치트 시트](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri)는 첫 번째 고려 사항인 **데이터로 어떤 작업을 수행할 예정인가요?** 에 도움이 됩니다. Machine Learning 알고리즘 치트 시트에서 수행하려는 작업을 찾은 다음 예측 분석 솔루션을 위한 [Azure Machine Learning 디자이너](./concept-designer.md?WT.mc_id=docs-article-lazzeri) 알고리즘을 찾습니다. 

Machine Learning 디자이너는 [다중 클래스 의사 결정 포리스트](./algorithm-module-reference/multiclass-decision-forest.md?WT.mc_id=docs-article-lazzeri), [추천 시스템](./algorithm-module-reference/evaluate-recommender.md?WT.mc_id=docs-article-lazzeri), [신경망 회귀](./algorithm-module-reference/neural-network-regression.md?WT.mc_id=docs-article-lazzeri), [다중 신경망](./algorithm-module-reference/multiclass-neural-network.md?WT.mc_id=docs-article-lazzeri) 및 [K-평균 클러스터링](./algorithm-module-reference/k-means-clustering.md?WT.mc_id=docs-article-lazzeri)과 같은 포괄적인 알고리즘 포트폴리오를 제공합니다. 각 알고리즘은 다양한 유형의 기계 학습 문제를 해결하기 위해 고안되었습니다. 각 [알고리즘의](./component-reference/component-reference.md?WT.mc_id=docs-article-lazzeri) 작동 방식 및 알고리즘을 최적화하기 위해 매개 변수를 조정하는 방법에 대한 설명서와 함께 전체 목록은 Machine Learning 디자이너 알고리즘 및 구성 요소 참조를 참조하세요.

> [!NOTE]
> 기계 학습 알고리즘 치트 시트를 다운로드하려면 [Azure Machine Learning 알고리즘 치트 시트](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri)로 이동하세요.
> 
> 

Azure Machine Learning 알고리즘 치트 시트의 지침과 함께 솔루션에 대한 기계 학습 알고리즘을 선택할 때는 다른 요구 사항을 염두에 두어야 합니다. 다음은 정확도, 학습 시간, 선형성 매개 변수 수 및 기능 수와 같은 고려해야 할 추가 요소입니다.

## <a name="comparison-of-machine-learning-algorithms"></a>기계 학습 알고리즘 비교

일부 학습 알고리즘에서는 데이터 구조 또는 원하는 결과에 대해 특수한 가정을 합니다. 사용자 요구에 적합한 것을 찾을 수 있는 경우 보다 유용한 결과, 보다 정확한 예측 또는 단축된 교육 시간을 제공할 수 있습니다.

다음 표에는 분류, 회귀 및 클러스터링 제품군에서 알고리즘의 가장 중요한 특성이 요약되어 있습니다.

| **알고리즘** | **정확도(Accuracy)** | **학습 시간** | **선형성** | **매개 변수** | **참고** |
| --- |:---:|:---:|:---:|:---:| --- |
| **분류 제품군** | | | | | |
| [2클래스 로지스틱 회귀](./algorithm-module-reference/two-class-logistic-regression.md?WT.mc_id=docs-article-lazzeri) |좋음  |빠름 |예 |4 | |
| [2클래스 의사 결정 포리스트](./algorithm-module-reference/two-class-decision-forest.md?WT.mc_id=docs-article-lazzeri) |우수 |보통 |예 |5 |더 느린 채점 시간을 표시합니다. 누적 트리 예측의 스레드 잠금으로 인한 더 느린 채점 시간 때문에 일대다 다중 클래스를 사용하지 않는 것이 좋습니다. |
| [2클래스 향상된 의사 결정 트리](./algorithm-module-reference/two-class-boosted-decision-tree.md?WT.mc_id=docs-article-lazzeri) |우수 |보통 |예 |6 |큰 메모리 공간 |
| [2클래스 인공신경망](./algorithm-module-reference/two-class-neural-network.md?WT.mc_id=docs-article-lazzeri) |좋음 |보통 |예 |8 | |
| [2클래스 평균 퍼셉트론](./algorithm-module-reference/two-class-averaged-perceptron.md?WT.mc_id=docs-article-lazzeri) |좋음 |보통 |예 |4 | |
| [2클래스 Support Vector Machine](./algorithm-module-reference/two-class-support-vector-machine.md?WT.mc_id=docs-article-lazzeri) |좋음 |빠름 |예 |5 |큰 기능 집합의 적합 |
| [다중 클래스 로지스틱 회귀](./algorithm-module-reference/multiclass-logistic-regression.md?WT.mc_id=docs-article-lazzeri) |좋음 |빠름 |예 |4 | |
| [다중 클래스 의사 결정 포리스트](./algorithm-module-reference/multiclass-decision-forest.md?WT.mc_id=docs-article-lazzeri) |우수 |보통 |예 |5 |더 느린 채점 시간 표시 |
| [다중 클래스 향상된 의사 결정 트리](./algorithm-module-reference/multiclass-boosted-decision-tree.md?WT.mc_id=docs-article-lazzeri) |우수 |보통 |예 |6 | 적용 범위가 좁아 약간의 위험이 있지만 정확도를 향상시키는 경향이 있습니다. |
| [다중 클래스 신경망](./algorithm-module-reference/multiclass-neural-network.md?WT.mc_id=docs-article-lazzeri) |좋음 |보통 |예 |8 | |
| [일대다 다중 클래스](./algorithm-module-reference/one-vs-all-multiclass.md?WT.mc_id=docs-article-lazzeri) | - | - | - | - |선택된 2클래스 메서드의 속성을 참조하세요. |
| **회귀 제품군** | | | | | |
| [선형 회귀](./algorithm-module-reference/linear-regression.md?WT.mc_id=docs-article-lazzeri) |좋음 |빠름 |예 |4 | |
| [의사 결정 포리스트 회귀](./algorithm-module-reference/decision-forest-regression.md?WT.mc_id=docs-article-lazzeri)|우수 |보통 |예 |5 | |
| [향상된 의사 결정 트리 회귀](./algorithm-module-reference/boosted-decision-tree-regression.md?WT.mc_id=docs-article-lazzeri) |우수 |보통 |예 |6 |큰 메모리 공간 |
| [인공신경망 회귀](./algorithm-module-reference/neural-network-regression.md?WT.mc_id=docs-article-lazzeri) |좋음 |보통 |예 |8 | |
| **클러스터링 제품군** | | | | | |
| [K-평균 클러스터링](./algorithm-module-reference/k-means-clustering.md?WT.mc_id=docs-article-lazzeri) |우수 |보통 |Yes |8 |클러스터링 알고리즘 |

## <a name="requirements-for-a-data-science-scenario"></a>데이터 과학 시나리오에 대한 요구 사항

데이터를 사용하여 수행할 작업을 확인한 후에는 솔루션에 대한 추가 요구 사항을 확인해야 합니다. 

다음 요구 사항을 절충하여 선택합니다.

- 정확도
- 학습 시간
- 선형성
- 매개 변수 수
- 기능 수

## <a name="accuracy"></a>정확도

기계 학습의 정확도는 전체 사례에 대한 실제 결과의 비율로 모델의 효율성을 측정합니다. Machine Learning 디자이너에서 모델 [평가 구성 요소는](./algorithm-module-reference/evaluate-model.md?WT.mc_id=docs-article-lazzeri) 업계 표준 평가 메트릭 집합을 계산합니다. 이 구성 요소를 사용하여 학습된 모델의 정확도를 측정할 수 있습니다.

항상 가장 정확한 응답을 얻어야 하는 것은 아닙니다. 용도에 따라 근사치가 적절한 경우도 있습니다. 이 경우 더 근접한 방법을 사용하여 처리 시간을 크게 줄일 수 있습니다. 또한 대략적인 방법은 자연스럽게 과잉 맞춤을 방지하는 경향이 있습니다.

모델 평가 구성 요소를 사용하는 방법에는 다음 세 가지가 있습니다.

- 모델을 평가하기 위해 학습 데이터에 대한 점수 생성
- 모델에 점수를 생성하지만 이러한 점수를 예약된 테스트 집합의 점수와 비교합니다.
- 동일한 데이터 세트를 사용하여 서로 다른 두 개의 관련 모델에 대한 점수를 비교합니다.

기계 학습 모델의 정확도를 평가하는 데 사용할 수 있는 메트릭 및 접근 방식의 전체 목록은 모델 구성 요소 평가 를 [참조하세요.](./algorithm-module-reference/evaluate-model.md?WT.mc_id=docs-article-lazzeri)

## <a name="training-time"></a>학습 시간

감독 학습에서 학습은 기록 데이터를 사용하여 오류를 최소화하는 기계 학습 모델을 작성하는 것을 의미합니다. 모델을 학습하는 데 필요한 시간 또는 분은 알고리즘에 따라 크게 다릅니다. 학습 시간은 대체로 정확도와 밀접하게 연결되어 일반적으로 서로를 수반합니다. 

또한 일부 알고리즘은 다른 항목보다 데이터 요소 수에 보다 민감합니다. 특히 데이터 세트가 큰 경우 시간 제한이 있기 때문에 특정 알고리즘을 선택할 수 있습니다.

Machine Learning 디자이너에서 기계 학습 모델을 만들고 사용하는 과정은 일반적으로 다음 세 단계로 진행됩니다.

1.  특정 알고리즘 유형을 선택한 다음 매개 변수 또는 하이퍼 매개 변수를 정의하여 모델을 구성합니다. 

2.  레이블이 지정되고 알고리즘과 호환되는 데이터가 있는 데이터 세트를 제공합니다. 데이터와 모델을 둘 다 [모델 학습 구성 요소](./algorithm-module-reference/train-model.md?WT.mc_id=docs-article-lazzeri)에 커넥트.

3.  학습이 완료되면 [채점 구성 요소](./algorithm-module-reference/score-model.md?WT.mc_id=docs-article-lazzeri) 중 하나와 함께 학습된 모델을 사용하여 새 데이터에 대한 예측을 만듭니다.

## <a name="linearity"></a>선형성

통계 및 기계 학습의 선형성은 데이터 세트의 변수와 상수 간에 선형 관계가 있음을 의미합니다. 예를 들어 선형 분류 알고리즘은 클래스를 직선(또는 더 높은 차원의 아날로그)으로 구분할 수 있다고 가정합니다.

많은 기계 학습 알고리즘에서 선형성을 활용합니다. Azure Machine Learning 디자이너에는 다음이 포함됩니다. 

- [다중 클래스 로지스틱 회귀](./algorithm-module-reference/multiclass-logistic-regression.md?WT.mc_id=docs-article-lazzeri)
- [2클래스 로지스틱 회귀](./algorithm-module-reference/two-class-logistic-regression.md?WT.mc_id=docs-article-lazzeri)
- [지원 벡터 머신](./algorithm-module-reference/two-class-support-vector-machine.md?WT.mc_id=docs-article-lazzeri)  

선형 회귀 알고리즘은 데이터가 직선을 따르는 경향이 있다고 가정합니다. 이러한 가정은 일부 문제에 대해서는 나쁘지 않지만 다른 경우에는 정확도가 떨어집니다. 이러한 단점에도 불구하고 선형 알고리즘은 첫 번째 전략으로 널리 사용됩니다. 알고리즘 방식으로 간단하고 학습 시간이 빠른 경향이 있습니다.

![비선형 클래스 경계](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***비선형 클래스 경계** _: _선형 분류 알고리즘에 의존하며 결과의 정확도가 떨어집니다.*

![비선형 추세 반영 데이터](./media/how-to-select-algorithms/nonlinear-trend.png)

***비선형 추세 반영 데이터** _: _선형 회귀 방법을 사용하면 필요한 것보다 훨씬 많은 오류가 생성됩니다.*

## <a name="number-of-parameters"></a>매개 변수 수

매개 변수는 데이터 과학자가 알고리즘을 설정할 때 전환하기 위한 노브입니다. 허용 오차, 반복 횟수 또는 알고리즘이 동작하는 방식의 변형 간 옵션 등 알고리즘의 동작에 영향을 주는 숫자입니다. 알고리즘의 학습 시간과 정확도는 때때로 올바른 설정을 얻는 데 민감할 수 있습니다. 일반적으로 많은 수의 매개 변수를 포함하는 알고리즘에서 좋은 조합을 찾기 위해서는 많은 수의 평가판 및 오류가 자주 발생합니다.

또는 Machine Learning 디자이너에 [모델 하이퍼 부모 튜닝 구성 요소가](./algorithm-module-reference/tune-model-hyperparameters.md?WT.mc_id=docs-article-lazzeri) 있습니다. 이 구성 요소의 목표는 기계 학습 모델에 대한 최적의 하이퍼parameters를 결정하는 것입니다. 구성 요소는 다양한 설정 조합을 사용하여 여러 모델을 빌드하고 테스트합니다. 모든 모델에 대한 메트릭을 비교하여 설정 조합을 얻습니다. 

이 방법은 매개 변수 공간을 스팬하는 데는 좋지만 모델을 학습하는 데 필요한 시간이 매개 변수 수에 따라 기하급수적으로 증가한다는 단점이 있습니다. 장점은 일반적으로 매개 변수를 많이 포함할수록 알고리즘의 유연성이 향상된다는 것입니다. 매개 변수 설정의 올바른 조합을 찾을 수 있다면 매우 좋은 정확도를 얻을 수 있는 경우가 많습니다.

## <a name="number-of-features"></a>기능 수

기계 학습에서 기능은 분석하려는 현상의 정량화 가능한 변수입니다. 특정 데이터 형식의 경우 데이터 요소에 비해 기능 수가 매우 클 수 있습니다. 보통 genetics 또는 텍스트 데이터가 그렇습니다. 

기능 수가 많으면 일부 학습 알고리즘이 교착 상태에 빠질 수 있으며 이로 인해 학습 시간이 상당히 길어집니다. [지원 벡터 머신](./algorithm-module-reference/two-class-support-vector-machine.md?WT.mc_id=docs-article-lazzeri)은 기능 수가 많은 시나리오에 특히 적합합니다. 이러한 이유로 대부분의 애플리케이션에서는 텍스트 및 이미지 분류에 대한 정보 검색에서 사용되었습니다. 지원 벡터 컴퓨터는 분류 및 회귀 작업 모두에 사용할 수 있습니다.

지정된 출력의 경우 기능 선택은 입력에 통계 테스트를 적용하는 프로세스를 의미합니다. 목표는 출력 예측 능력이 더 뛰어난 열을 확인하는 것입니다. Machine Learning 디자이너의 [필터 기반 기능 선택 구성 요소는](./algorithm-module-reference/filter-based-feature-selection.md?WT.mc_id=docs-article-lazzeri) 선택할 수 있는 여러 기능 선택 알고리즘을 제공합니다. 구성 요소에는량 상관 관계 및 chi-squared 값과 같은 상관 관계 메서드가 포함됩니다.

[순열 기능 중요도 구성 요소를](./algorithm-module-reference/permutation-feature-importance.md?WT.mc_id=docs-article-lazzeri) 사용하여 데이터 세트에 대한 기능 중요도 점수 집합을 컴퓨팅할 수도 있습니다. 그런 다음 이러한 점수를 모델에서 사용하기에 가장 적합한 기능을 결정하는 데 활용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

 - [Azure Machine Learning 디자이너에 대해 자세히 알아보기](./concept-designer.md?WT.mc_id=docs-article-lazzeri)
 - Azure Machine Learning 디자이너에서 사용할 수 있는 모든 기계 학습 알고리즘에 대한 설명은 [Machine Learning 디자이너 알고리즘 및 구성 요소 참조를 참조하세요.](./component-reference/component-reference.md?WT.mc_id=docs-article-lazzeri)
 - 딥 러닝, 기계 학습, AI 간의 관계를 알아보려면 [딥 러닝과 기계 학습](./concept-deep-learning-vs-machine-learning.md?WT.mc_id=docs-article-lazzeri)을 참조하세요.