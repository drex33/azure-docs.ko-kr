---
title: 점수 이미지 모델 구성 요소 사용
titleSuffix: Azure Machine Learning
description: Azure Machine Learning의 이미지 점수 매기기 구성 요소를 사용 하 여 학습 된 이미지 모델을 사용 하 여 예측을 생성 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 57cd0fb2b4c64010955f189b9cf28ced54883637
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570108"
---
# <a name="score-image-model"></a>이미지 모델 채점

이 문서에서는 Azure Machine Learning 디자이너의 구성 요소에 대해 설명 합니다.

이 구성 요소를 사용 하 여 입력 이미지 데이터에 대해 학습 된 이미지 모델을 사용 하 여 예측을 생성 합니다.

## <a name="how-to-configure-score-image-model"></a>이미지 모델 점수 매기기를 구성하는 방법

1. 파이프라인에 **점수 매기기 이미지 모델** 구성 요소를 추가 합니다.

2. 학습된 이미지 모델과 입력 이미지 데이터가 포함된 데이터 세트를 연결합니다. 

    데이터는 ImageDirectory 유형이어야 합니다. 이미지 디렉터리를 가져오는 방법에 대 한 자세한 내용은 [이미지 디렉터리로 변환](convert-to-image-directory.md) 구성 요소를 참조 하세요. 입력 데이터 집합의 스키마 또한 일반적으로 모델을 학습하는 데 사용되는 데이터의 스키마와 일치해야 합니다.

3. 파이프라인을 제출합니다.

## <a name="results"></a>결과

[점수 이미지 모델](score-image-model.md)을 사용 하 여 점수 집합을 생성 한 후 모델의 정확도 (성능)를 평가 하는 데 사용 되는 메트릭 집합을 생성 하려면이 구성 요소와 점수가 매겨진 데이터 집합을 연결 하 여 [모델을 평가할](evaluate-model.md)수 있습니다. 

### <a name="publish-scores-as-a-web-service"></a>점수를 웹 서비스로 게시

채점의 일반적인 용도는 출력을 예측 웹 서비스의 일부로 반환하는 것입니다. 자세한 정보는 Azure Machine Learning 디자이너에서 파이프라인을 기반으로 실시간 엔드포인트를 배포하는 방법에 대한 [이 자습서](../tutorial-designer-automobile-price-deploy.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning 하는 데 [사용할 수 있는 구성 요소 집합](component-reference.md) 을 참조 하세요.