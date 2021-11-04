---
title: 일대일 다중 클래스
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 한 vs-one 다중 클래스 구성 요소를 사용 하 여 앙상블 이진 분류 모델에서 다중 클래스 분류 모델을 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 176b605de08e705571aea1b0e391192d2c18532b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570252"
---
# <a name="one-vs-one-multiclass"></a>일대일 다중 클래스

이 문서에서는 Azure Machine Learning designer에서 한 vs-one 다중 클래스 구성 요소를 사용 하는 방법을 설명 합니다. 목표는 *일대일* 접근 방식을 사용하여 여러 클래스를 예측할 수 있는 분류 모델을 만드는 것입니다.

이 구성 요소는 연속 또는 범주 예측 변수에 따라 결과가 달라 지는 경우 세 개 이상의 가능한 결과를 예측 하는 모델을 만드는 데 유용 합니다. 이 메서드를 사용하면 여러 출력 클래스가 필요한 문제에 대해서도 이진 분류 방법을 사용할 수 있습니다.

### <a name="more-about-one-versus-one-models"></a>일대일 모델에 대한 자세한 정보

일부 분류 알고리즘은 디자인에 따라 두 개 이상의 클래스를 사용할 수 있도록 허용합니다. 기타 값은 두 값(이진 또는 2 클래스 모델) 중 하나로 가능한 결과를 제한합니다. 하지만 다양한 전략을 통해 다중 클래스 분류 작업에 대한 이진 분류 알고리즘을 적용할 수도 있습니다. 

이 구성 요소는 클래스 쌍 마다 이진 모델이 생성 되는 일대일 메서드를 구현 합니다. 예측 시에는 가장 많은 표를 받은 클래스가 선택됩니다. `n_classes * (n_classes - 1) / 2` 분류자를 맞춰야 하므로 이 메서드는 O(n_classes^2) 복잡성 때문에 일반적으로 일대다에 비해 느립니다. 그러나 이 메서드는 `n_samples`로 잘 확장되지 않는 커널 알고리즘과 같은 알고리즘에 유리할 수 있습니다. 각 개별 학습 문제에는 데이터의 작은 하위 집합만 포함되는 반면, 일대다에서는 전체 데이터 세트가 `n_classes`회 사용되기 때문입니다.

기본적으로 구성 요소는 개별 모델의 앙상블을 만든 다음 결과를 병합 하 여 모든 클래스를 예측 하는 단일 모델을 만듭니다. 모든 이진 분류자는 일대일 모델에 대한 기준으로 사용할 수 있습니다.  

예를 들어 [2 클래스 지원 벡터 컴퓨터](two-class-support-vector-machine.md) 모델을 구성 하 고이를 일대일 다중 클래스 구성 요소에 대 한 입력으로 제공 한다고 가정해 보겠습니다. 구성 요소는 출력 클래스의 모든 멤버에 대해 2 클래스 지원 벡터 컴퓨터 모델을 만듭니다. 그런 다음, 일대일 메서드를 적용하여 모든 클래스에 대한 결과를 결합합니다.  

구성 요소는 OneVsOneClassifier의 추가 정보를 사용 하 고 [여기](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsOneClassifier.html)에서 자세한 내용을 확인할 수 있습니다.

## <a name="how-to-configure-the-one-vs-one-multiclass-classifier"></a>일대일 다중 클래스 분류자 구성 방법  

이 구성 요소는 여러 클래스를 분석 하는 이진 분류 모델의 앙상블을 만듭니다. 이 구성 요소를 사용 하려면 먼저 *이진 분류* 모델을 구성 하 고 학습 해야 합니다. 

단일 vs 다중 클래스 구성 요소에 이진 모델을 연결 합니다. 그런 다음 레이블이 있는 학습 데이터 세트와 함께 [모델 학습](train-model.md)을 사용하여 모델 앙상블을 학습합니다.

모델을 결합하면 일대일 다중 클래스는 여러 이진 분류 모델을 만들고 각 클래스에 대한 알고리즘을 최적화한 다음 모델을 병합합니다. 이 구성 요소는 학습 데이터 집합에 여러 클래스 값이 있을 수 있는 경우에도 이러한 작업을 수행 합니다.

1. 단일 vs 다중 클래스 구성 요소를 디자이너의 파이프라인에 추가 합니다. 이 구성 요소는 **분류** 범주의 **Machine Learning-Initialize** 에서 찾을 수 있습니다.

   일대일 다중 클래스 분류자는 자체적으로 구성 가능한 매개 변수가 없습니다. 모든 사용자 지정은 입력으로 제공되는 이진 분류 모델에서 수행해야 합니다.

2. 파이프라인에 이진 분류 모델을 추가하고 해당 모델을 구성합니다. 예를 들어 [2 클래스 지원 벡터 컴퓨터](two-class-support-vector-machine.md) 또는 [2 클래스 고급 의사 결정 트리](two-class-boosted-decision-tree.md)를 사용할 수 있습니다.

3. 파이프라인에 [모델 학습](train-model.md) 구성 요소를 추가 합니다. 일대일 다중 클래스 출력인, 학습되지 않은 분류자를 연결합니다.

4. [학습 모델](train-model.md)의 기타 입력에서 여러 클래스 값이 있는 레이블이 있는 학습 데이터 세트를 연결합니다.

5. 파이프라인을 제출합니다.

## <a name="results"></a>결과

학습을 완료한 후에는 모델을 사용하여 다중 클래스 예측을 만들 수 있습니다.

또는 레이블이 지정한 유효성 검사 데이터 세트에 대한 [교차 유효성 검사](cross-validate-model.md)를 위해 학습되지 않은 분류자를 교차 유효성 검사 모델에 전달할 수 있습니다.


## <a name="next-steps"></a>다음 단계

Azure Machine Learning 하는 데 [사용할 수 있는 구성 요소 집합](component-reference.md) 을 참조 하세요. 
