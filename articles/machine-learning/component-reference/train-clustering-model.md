---
title: '클러스터링 모델 학습: 구성 요소 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 클러스터링 모델 학습 구성 요소를 사용하여 클러스터링 모델을 학습하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/17/2021
ms.openlocfilehash: 16d20e71dba5c83093ec572aad4910ec9a89ba06
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570166"
---
# <a name="train-clustering-model"></a>클러스터링 모델 학습

이 문서에서는 Azure Machine Learning 디자이너의 구성 요소에 대해 설명합니다.

이 구성 요소를 사용하여 클러스터링 모델을 학습합니다.

구성 요소는 [K-Means](k-means-clustering.md) 클러스터링 구성 요소를 사용하여 이미 구성한 학습되지 않은 클러스터링 모델을 사용하고 레이블이 지정되거나 레이블이 지정되지 않은 데이터 집합을 사용하여 모델을 학습합니다. 구성 요소는 예측에 사용할 수 있는 학습된 모델과 학습 데이터의 각 사례에 대한 클러스터 할당 집합을 모두 만듭니다.

> [!NOTE]
> 기계 학습 모델 학습을 위한 일반 구성 요소인 [모델 학습](train-model.md) 구성 요소를 사용하여 클러스터링 모델을 학습할 수 없습니다. [모델 학습](train-model.md)은 감독된 학습 알고리즘에서만 작동하기 때문입니다. K-Means 및 다른 클러스터링 알고리즘을 사용하는 경우 자율 학습을 허용합니다. 즉, 알고리즘이 레이블이 지정되지 않은 데이터를 통해 학습할 수 있습니다.  
  
## <a name="how-to-use-train-clustering-model"></a>클러스터링 모델 학습을 사용하는 방법  

1.  디자이너에서 파이프라인에 **클러스터링 모델** 학습 구성 요소를 추가합니다. Machine Learning 구성 요소 의 **학습** 범주에서 **구성 요소를** 찾을 수 있습니다.  
  
2. [K-Means 클러스터링](k-means-clustering.md) 구성 요소 또는 호환되는 클러스터링 모델을 만드는 다른 사용자 지정 구성 요소를 추가하고 클러스터링 모델의 매개 변수를 설정합니다.  
    
3.  **클러스터링 모델 학습** 의 오른쪽 입력에 학습 데이터 세트를 연결합니다.
  
5.  **열 집합** 에서 클러스터를 작성하는 데 사용할 데이터 세트의 열을 선택합니다. 올바른 기능을 제공하는 열을 선택해야 합니다. 예를 들어 ID 또는 고유한 값이 있는 다른 열을 사용하거나 모든 값이 같은 열을 사용하지 마십시오.

    레이블을 사용할 수 있는 경우 해당 레이블을 기능으로 사용하거나 그대로 둘 수 있습니다.  
  
6. 새 클러스터 레이블과 함께 학습 데이터를 출력하려면 **추가하려면 선택/결과만 출력하려면 선택 취소** 옵션을 선택합니다.

    이 옵션의 선택을 취소하면 클러스터 할당만 출력됩니다. 

7. 파이프라인을 제출하거나 **클러스터링 모델 학습** 구성 요소를 클릭하고 **선택한 실행 을 선택합니다.**  
  
### <a name="results"></a>결과

학습을 완료한 후:

+ 학습된 모델의 스냅샷을 저장하려면 모델 학습 **구성** 요소의 오른쪽 패널에서 **출력** 탭을 선택합니다. 데이터 **세트 등록** 아이콘을 선택하여 모델을 재사용 가능한 구성 요소로 저장합니다.

+ 모델에서 점수를 생성하려면 [클러스터에 데이터 할당](assign-data-to-clusters.md)을 사용합니다.

> [!NOTE]
> 디자이너에서 학습된 모델을 배포해야 하는 경우 **모델 점수 매기기** 대신 [클러스터에 데이터 할당이](assign-data-to-clusters.md) 유추 파이프라인의 [웹 서비스 출력 구성 요소](web-service-input-output.md) 입력에 연결되어 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning [사용할 수 있는 구성 요소 집합을](component-reference.md) 참조하세요. 