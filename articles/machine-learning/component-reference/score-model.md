---
title: '모델 점수 매기기: 구성 요소 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 모델 점수 매기기 구성 요소를 사용 하 여 학습 된 분류 또는 회귀 모델을 사용 하 여 예측을 생성 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 83e466e7218b327bbd871c6693466a10744c9b79
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570002"
---
# <a name="score-model"></a>모델 채점

이 문서에서는 Azure Machine Learning 디자이너의 구성 요소에 대해 설명 합니다.

학습 된 분류 또는 회귀 모델을 사용 하 여 예측을 생성 하려면이 구성 요소를 사용 합니다.

## <a name="how-to-use"></a>사용 방법

1. **모델 점수 매기기** 구성 요소를 파이프라인에 추가 합니다.

2. 학습된 모델 및 새 입력 데이터를 포함하는 데이터 집합을 연결합니다. 

    데이터는 사용 중인 학습된 모델의 형식과 호환되는 형식이어야 합니다. 입력 데이터 집합의 스키마 또한 일반적으로 모델을 학습하는 데 사용되는 데이터의 스키마와 일치해야 합니다.

3. 파이프라인을 제출합니다.

## <a name="results"></a>결과

[모델 점수 매기기](./score-model.md)를 사용하여 점수 집합을 생성한 후에는 다음을 수행합니다.

+ 모델의 정확도(성능)를 평가하는 데 사용되는 메트릭 집합을 생성 하려면 점수가 매겨진 데이터 집합을 [모델 계산](./evaluate-model.md)에 연결할 수 있습니다. 
+ 구성 요소를 마우스 오른쪽 단추로 클릭 하 고 **시각화** 를 선택 하 여 결과 예제를 표시 합니다.
<!-- + To Save the results to a dataset. -->

점수 또는 예측 값은 모델 및 입력 데이터에 따라 다양한 형식일 수 있습니다.

- 분류 모델의 경우 [점수 매기기 모델](./score-model.md) 은 클래스의 예측 값 뿐만 아니라 예측 값의 가능성을 출력합니다.
- 회귀 모델의 경우 [모델 점수 매기기](./score-model.md)는 예측된 숫자 값만 생성합니다.


## <a name="publish-scores-as-a-web-service"></a>점수를 웹 서비스로 게시

채점의 일반적인 용도는 출력을 예측 웹 서비스의 일부로 반환하는 것입니다. 자세한 정보는 Azure Machine Learning 디자이너에서 파이프라인을 기반으로 실시간 엔드포인트를 배포하는 방법에 대한 [이 자습서](../tutorial-designer-automobile-price-deploy.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning 하는 데 [사용할 수 있는 구성 요소 집합](component-reference.md) 을 참조 하세요.