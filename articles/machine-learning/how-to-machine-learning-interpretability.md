---
title: 모델 해석성(미리 보기)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Python SDK를 사용하여 학습 및 추론하는 동안 기계 학습 모델이 예측하는 방법을 이해하고 설명하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.custom: responsible-ml, mktng-kw-nov2021
ms.date: 11/04/2021
ms.openlocfilehash: baf146725c01736fde36420779e495ce3906f891
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852601"
---
# <a name="model-interpretability-in-azure-machine-learning-preview"></a>Azure Machine Learning의 모델 해석력(미리 보기)

이 문서에서는 Azure Machine Learning 모델 해석에 사용할 수 있는 방법을 설명합니다. 

## <a name="why-does-model-interpretability-matter"></a>모델 해석성이 중요한 이유는 무엇인가요?

모델 해석력은 회사 정책, 산업 표준 및 정부 규정 준수를 보장하는 데 있어 데이터 과학자, 감사자, 비즈니스 의사 결정자에게 매우 중요합니다.

+ 데이터 과학자는 임원 및 관련자가 결과의 가치와 정확도를 이해할 수 있도록 모델에 대해 설명할 수 있어야 합니다. 해석력은 모델을 디버그하고 모델 개선 방법에 대한 합리적인 결정을 내릴 때도 꼭 필요합니다. 

+ 법적 감사자에게는 규정 준수와 관련하여 모델의 유효성을 검사하고 모델의 결정이 인간에게 어떤 영향을 주는지 모니터링할 수 있는 도구가 필요합니다. 

+ 비즈니스 의사 결정권자는 최종 사용자에게 투명성을 제공할 수 있는 능력이 있어야 안심할 수 있으며 신뢰를 얻고 유지하는 데 도움이 됩니다.

모델 개발의 두 가지 주요 단계에서는 기계 학습 모델을 설명할 수 있는 능력을 보유하는 것이 중요합니다.

+ 학습 단계 중에 모델 디자이너와 평가자는 모델의 해석력 출력을 사용하여 가설을 확인하고 관련자와 신뢰를 쌓을 수 있습니다. 또한 모델에 대한 인사이트를 사용하여 디버깅, 모델 동작이 목표와 일치하는지 확인하기 위한 유효성 검사를 진행할 수 있으며, 모델 불공정 또는 중요하지 않은 기능이 있는지도 확인할 수 있습니다.

+ 추론 단계 동안 배포된 모델에 대한 투명성을 제공하면 경영진은 “배포 시” 모델이 어떻게 작동하고 모델의 결정이 실제 세계의 사람들에게 어떤 영향을 주는지 이해하는 데 도움이 됩니다. 

## <a name="interpretability-with-azure-machine-learning"></a>Azure Machine Learning에서의 모델 해석력

모델 해석력 클래스는 다음 SDK 패키지를 통해 제공됩니다([Azure Machine Learning용 SDK 패키지 설치](/python/api/overview/azure/ml/install) 방법 알아보기).

* `azureml.interpret`에는 Microsoft에서 지원하는 기능이 포함되어 있습니다.

일반적인 용도로는 `pip install azureml-interpret`을 사용합니다.

## <a name="how-to-interpret-your-model"></a>모델을 해석하는 방법

SDK에서 클래스 및 메서드를 사용하여 다음을 수행할 수 있습니다.
+ 전체 모델 및/또는 개별 데이터 요소에 대한 기능 중요도 값을 생성하여 모델 예측을 설명합니다. 
+ 학습 및 유추 중에 실제 데이터 세트에서 모델 해석력을 대규모로 달성합니다.
+ 대화형 시각화 대시보드를 사용하여 학습 시간에 데이터 및 설명의 패턴을 발견합니다.

기계 학습에서 **기능** 은 대상 데이터 요소를 예측하는 데 사용되는 데이터 필드입니다. 예를 들어 신용 위험을 예측하려면 나이, 계정 규모, 계정 사용 기간에 대한 데이터 필드를 사용할 수 있습니다. 이 경우에는 나이, 계정 규모, 계정 사용 기간이 **기능** 이 됩니다. 기능 중요도는 각 데이터 필드가 모델의 예측에 어떻게 영향을 주는지 알려줍니다. 예를 들어 계정 크기 및 계정 나이는 예측 값에 크게 영향을 미치지 않지만, 나이는 예측에 많이 사용될 수 있습니다. 데이터 과학자는 이 프로세스를 통해 예측되는 결과를 설명할 수 있으므로 관련자는 모델에서 가장 중요한 기능이 무엇인지 알 수 있습니다.

## <a name="supported-model-interpretability-techniques"></a>지원되는 모델 해석 기술

 `azureml-interpret`은 해석 가능한 모델을 학습하고 블랙 박스 AI 시스템을 설명하는 데 도움을 주는 오픈 소스 Python 패키지인 [Interpret-Community](https://github.com/interpretml/interpret-community/)에서 개발한 해석력 기술을 사용합니다. [Interpret-Community](https://github.com/interpretml/interpret-community/)는 해당 SDK를 지원하는 설명자에 대한 호스트 역할을 하며 현재 다음 해석력 기술을 지원하고 있습니다.

|해석력 기술|Description|Type|
|--|--|--------------------|
|SHAP 트리 설명자| [SHAP](https://github.com/slundberg/shap)의 트리 설명자는 **트리 및 트리 결합체** 에 특정되는 다항식 시간 고속 SHAP 값 예측 알고리즘에 중점을 둡니다.|모델별|
|SHAP 심층 설명자| SHAP의 설명을 기반으로 하는 심층 설명자는 “[SHAP NIPS 문서](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)에 설명된 DeepLIFT와의 연결을 기반으로 하는 딥 러닝 모델의 SHAP 값에 대한 고속 근사값 알고리즘입니다. TensorFlow 백엔드를 사용하는 **TensorFlow** 모델 및 **keras** 모델이 지원됩니다(PyTorch도 예비 단계로 지원됩니다).”|모델별|
|SHAP 선형 설명자| SHAP의 선형 설명자는 **선형 모델** 에 대해 SHAP 값을 컴퓨팅하고, 필요에 따라 기능 간 상관관계를 고려합니다.|모델별|
|SHAP 커널 설명자| SHAP의 커널 설명자는 특별히 가중치가 적용된 로컬 선형 회귀를 사용하여 **모든 모델** 에 대한 SHAP 값을 예측합니다.|모델 독립적|
|모방 설명자(전역 서로게이트)| 모방 설명자는 [전역 서로게이트 모델](https://christophm.github.io/interpretable-ml-book/global.html)을 학습하여 블랙 박스 모델을 모방하도록 하는 아이디어를 기반으로 합니다. 전역 서로게이트 모델은 **모든 블랙 박스 모델** 의 예측에 대해 최대한 정확한 근사치를 산출하도록 학습된, 본질적으로 해석이 가능한 모델입니다. 데이터 과학자는 해당 서로게이트 모델을 해석하여 블랙 박스 모델에 대한 결론을 내릴 수 있습니다. LightGBM(LGBMExplainableModel), 선형 회귀(LinearExplainableModel), 추측 경사 하강 설명 모델(SGDExplainableModel), 의사 결정 트리(DecisionTreeExplainableModel)의 4가지 해석 가능 모델 중 하나를 서로게이트 모델로 사용할 수 있습니다.|모델 독립적|
|순열 기능 중요도 설명자(PFI)| 순열 기능 중요도는 Breiman의 [랜덤 포리스트 논문](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf)(섹션 10 참조)에서 착안한 분류 및 회귀 모델을 설명하는 데 사용되는 기법입니다. 높은 수준에서 순열 기능 중요도는 전체 데이터 세트에 대해 한 번에 하나의 기능에 대한 데이터 순서를 무작위로 섞고 관심 성능 메트릭이 얼마나 변경되는지를 계산하는 방식으로 작동합니다. 변화가 클수록 해당 기능이 중요한 것입니다. PFI는 **모든 기본 모델** 의 전체적인 동작을 설명할 수 있지만 개별 예측은 설명하지 않습니다. |모델 독립적|

위에서 설명한 해석력 기술 외에도 `TabularExplainer`라는 다른 SHAP 기반 설명자를 지원합니다. 모델에 따라 `TabularExplainer`는 지원되는 다음 SHAP 설명자 중 하나를 사용합니다.

* 모든 트리 기반 모델용 TreeExplainer
* DNN 모델용 DeepExplainer
* 선형 모델용 LinearExplainer
* 다른 모든 모델용 KernelExplainer

`TabularExplainer`는 또한 직접 SHAP 설명자 전반에서 기능 및 성능을 크게 개선했습니다.

* **초기화 데이터 세트 요약**. 빠르게 설명하는 것이 가장 중요한 경우에는 초기화 데이터 세트를 요약하고 몇 가지 대표적인 샘플의 소규모 세트를 생성합니다. 이렇게 하면 전체적인, 그리고 개별적인 기능 중요도 값의 생성 속도를 높일 수 있습니다.
* **평가 데이터 집합 샘플링**. 사용자가 대규모의 평가 샘플을 전달하지만 실제로는 모든 샘플을 평가할 필요가 없는 경우, 샘플링 매개 변수를 true로 설정하여 전체 모델 설명 계산의 속도를 높일 수 있습니다.

다음 다이어그램은 지원되는 설명자의 현재 구조체를 보여 줍니다.

[![Machine Learning 해석력 아키텍처](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>지원되는 기계 학습 모델

`azureml.interpret`SDK 패키지는 다음 데이터 세트 형식으로 학습된 모델을 지원합니다.
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

설명 함수는 모델과 파이프라인 모두를 입력으로 수락합니다. 모델을 제공하는 경우 해당 모델은 예측 함수 `predict` 또는 `predict_proba`를 구현하거나 Scikit 규칙을 준수해야 합니다. 모델이 이 기능을 지원하지 않는 경우 Scikit에서 `predict` 또는 `predict_proba`와 동일한 결과를 생성하는 함수로 모델을 래핑하고 선택한 설명자와 함께 해당 래퍼 함수를 사용할 수 있습니다. 파이프라인이 제공되는 경우 설명 함수는 실행 중인 파이프라인 스크립트가 예측을 반환한다고 가정합니다. 이 래핑 기법을 사용하면 `azureml.interpret`는 PyTorch, TensorFlow, Keras 딥 러닝 프레임워크 및 클래식 기계 학습 모델을 통해 학습된 모델을 지원할 수 있습니다.

## <a name="local-and-remote-compute-target"></a>로컬 및 원격 컴퓨팅 대상

`azureml.interpret` 패키지는 로컬 및 원격 컴퓨팅 대상 모두에서 작동하도록 설계되었습니다. 로컬로 실행하는 경우 SDK 함수는 Azure 서비스에 연결되지 않습니다. 

Azure Machine Learning 컴퓨팅에 대한 설명을 원격으로 실행하고 설명 정보를 Azure Machine Learning 실행 기록 서비스에 기록할 수 있습니다. 해당 정보가 기록되면 설명의 보고서 및 시각화를 Azure Machine Learning 스튜디오에서 사용자 분석용으로 쉽게 사용할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- 로컬 및 Azure Machine Learning 원격 컴퓨터 리소스 모두에서 진행하는 모델 학습에 대해 해석력을 사용 설정하려면 [방법 가이드](how-to-machine-learning-interpretability-aml.md)를 참조하세요. 
- [자동화된 기계 학습 모델에 대한 해석성](how-to-machine-learning-interpretability-automl.md)을 사용하도록 설정하는 방법을 알아봅니다.
- 추가 시나리오는 [샘플 Notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)를 참조하세요. 
- 텍스트 해석력 시나리오에 관심이 있는 경우 [Interpret-Community](https://github.com/interpretml/interpret-community/)와 관련된 오픈 소스 리포지토리인 [Interpret-text](https://github.com/interpretml/interpret-text)를 참조하여 NLP에 대한 해석력 기술에 대한 정보를 확인하세요. `azureml.interpret` 패키지는 현재 해당 기술을 지원하지 않지만 [텍스트 분류에 대한 예제 Notebook](https://github.com/interpretml/interpret-text/blob/master/notebooks/text_classification/text_classification_classical_text_explainer.ipynb)을 사용하여 시작할 수 있습니다.
