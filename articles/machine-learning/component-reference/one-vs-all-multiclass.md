---
title: OVA(One-vs-All)  다중 클래스
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디자이너에서 일대다 다중 클래스 구성 요소를 사용하여 이진 분류 모델의 앙상블을 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 7d935c2b3b5342b8a3407e607a15fcafd7371ebb
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570129"
---
# <a name="one-vs-all-multiclass"></a>OVA(One-vs-All)  다중 클래스

이 문서에서는 Azure Machine Learning 디자이너에서 일대다 다중 클래스 구성 요소를 사용하는 방법을 설명합니다. 목표는 *일대다* 접근 방법을 사용하여 여러 클래스를 예측할 수 있는 분류 모델을 만드는 것입니다.

이 구성 요소는 결과가 연속 또는 범주 예측 변수에 따라 달라지는 경우 3개 이상의 가능한 결과를 예측하는 모델을 만드는 데 유용합니다. 이 메서드를 사용하면 여러 출력 클래스가 필요한 문제에 대해서도 이진 분류 방법을 사용할 수 있습니다.

### <a name="more-about-one-versus-all-models"></a>일대다 모델에 대한 자세한 정보

일부 분류 알고리즘은 디자인에 따라 2개 이상의 클래스를 사용할 수 있도록 허용합니다. 기타 값은 두 값(이진 또는 2 클래스 모델) 중 하나로 가능한 결과를 제한합니다. 하지만 다양한 전략을 통해 다중 클래스 분류 작업에 대한 이진 분류 알고리즘을 적용할 수도 있습니다. 

이 구성 요소는 여러 출력 클래스 각각에 대해 이진 모델이 만들어지는 일 대 모든 메서드를 구현합니다. 구성 요소는 이진 분류 문제인 것처럼 해당 보수(모델의 다른 모든 클래스)에 대해 개별 클래스에 대해 이러한 각 이진 모델을 평가합니다. 계산 효율성 외에도(오직 `n_classes` 분류자만 필요함) 이 방법의 이점 중 하나는 해석력입니다. 각 클래스는 하나의 분류자로만 표시되므로 해당하는 분류자를 검사하여 클래스에 대한 정보를 얻을 수 있습니다. 이는 가장 일반적으로 사용되는 다중 클래스 분류 전략이며 합당한 기본 선택입니다. 그런 다음, 구성 요소는 이러한 이진 분류자를 실행하고 신뢰도 점수가 가장 높은 예측을 선택하여 예측을 수행합니다. 

기본적으로 구성 요소는 개별 모델의 앙상블을 만든 다음 결과를 병합하여 모든 클래스를 예측하는 단일 모델을 만듭니다. 모든 이진 분류자는 하나 이상의 모델에 대한 기준으로 사용할 수 있습니다.  

예를 들어 [2클래스 Support Vector Machine](two-class-support-vector-machine.md) 모델을 구성하고 일대다 다중 클래스 구성 요소에 대한 입력으로 제공합니다. 구성 요소는 출력 클래스의 모든 멤버에 대해 2클래스 지원 벡터 컴퓨터 모델을 만듭니다. 그런 다음, 하나 이상의 메서드를 적용하여 모든 클래스에 대한 결과를 결합합니다.  

구성 요소는 sklearn의 OneVsRestClassifier를 [사용하며, 여기에서](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsRestClassifier.html)자세한 내용을 확인할 수 있습니다.

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>일대다 다중 클래스 분류자 구성 방법  

이 구성 요소는 여러 클래스를 분석하는 이진 분류 모델의 앙상블을 만듭니다. 이 구성 요소를 사용하려면 먼저 *이진 분류* 모델을 구성하고 학습시켜야 합니다. 

이진 모델을 일대다 다중 클래스 구성 요소에 연결합니다. 그런 다음 레이블이 있는 학습 데이터 세트와 함께 [모델 학습을](train-model.md) 사용하여 모델 앙상블을 학습합니다.

모델을 결합하면 일대다 다중 클래스는 여러 이진 분류 모델을 만들고 각 클래스에 대한 알고리즘을 최적화한 다음 모델을 병합합니다. 구성 요소는 학습 데이터 세트에 여러 클래스 값이 있을 수 있더라도 이러한 작업을 수행합니다.

1. 디자이너에서 파이프라인에 일대다 다중 클래스 구성 요소를 추가합니다. 이 구성 요소는 **분류** 범주의 **Machine Learning - 초기화** 에서 찾을 수 있습니다.

   일대다 다중 클래스 분류자는 자체적으로 구성 가능한 매개 변수가 없습니다. 모든 사용자 지정은 입력으로 제공되는 이진 분류 모델에서 수행해야 합니다.

2. 파이프라인에 이진 분류 모델을 추가하고 해당 모델을 구성합니다. 예를 들어 [2 클래스 지원 벡터 컴퓨터](two-class-support-vector-machine.md) 또는 [2 클래스 고급 의사 결정 트리](two-class-boosted-decision-tree.md)를 사용할 수 있습니다.

3. 파이프라인에 [모델 학습](train-model.md) 구성 요소를 추가합니다. 일대다 다중 클래스 출력인, 학습되지 않은 분류자 연결

4. [학습 모델](train-model.md)의 기타 입력에서 여러 클래스 값이 있는 레이블이 있는 학습 데이터 세트를 연결합니다.

5. 파이프라인을 제출합니다.

## <a name="results"></a>결과

학습을 완료한 후에는 모델을 사용하여 다중 클래스 예측을 만들 수 있습니다.

또는 레이블이 지정한 유효성 검사 데이터 세트에 대한 [교차 유효성 검사](cross-validate-model.md)를 위해 학습되지 않은 분류자를 교차 유효성 검사 모델에 전달할 수 있습니다.


## <a name="next-steps"></a>다음 단계

Azure Machine Learning [사용할 수 있는 구성 요소 집합을](component-reference.md) 참조하세요. 
