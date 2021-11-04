---
title: '추천 평가: 구성 요소 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 Evaluate 추천 구성 요소를 사용 하 여 추천 모델 예측의 정확도를 평가 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: aa43a0e7fdf7214f52855ad2bc7601188c305474
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570058"
---
# <a name="evaluate-recommender"></a>추천 평가

이 문서에서는 Azure Machine Learning designer에서 Evaluate 추천 구성 요소를 사용 하는 방법을 설명 합니다. 목표는 권장 사항 모델이 만든 예측의 정확도를 측정하는 것입니다. 이 구성 요소를 사용 하 여 다양 한 종류의 권장 사항을 평가할 수 있습니다.  
  
-   사용자 및 항목에 대한 예측된 등급    
-   사용자에게 권장되는 항목  
  
권장 사항 모델을 사용하여 예측을 만들 때 지원되는 각 예측 형식에 대해 약간 다른 결과가 반환됩니다. Evaluate 추천 구성 요소는 점수가 매겨진 데이터 집합의 열 형식에서 예측 종류를 추론 합니다. 예를 들어 점수가 매겨진 데이터 세트에는 다음이 포함될 수 있습니다.

- 사용자-항목-등급 삼중 쌍
- 사용자 및 권장 항목

구성 요소는 생성 되는 예측의 유형에 따라 적절 한 성능 메트릭도 적용 합니다. 

  
## <a name="how-to-configure-evaluate-recommender"></a>추천 평가를 구성하는 방법

Evaluate 추천 구성 요소는 해당 "그라운드" 데이터와 함께 권장 사항 모델을 사용 하 여 예측 출력을 비교 합니다. 예를 들어 [점수 .Svd 추천](score-svd-recommender.md) 구성 요소는 추천 평가를 사용 하 여 분석할 수 있는 점수가 매겨진 데이터 집합을 생성 합니다.

### <a name="requirements"></a>요구 사항

추천 평가에는 다음 데이터 세트가 입력으로 필요합니다. 
  
#### <a name="test-dataset"></a>테스트 데이터 세트

테스트 데이터 세트는 사용자-항목-등급 삼중 쌍 형식의 “지상 실측” 데이터를 포함합니다.  

#### <a name="scored-dataset"></a>점수가 매겨진 데이터 집합입니다.

점수가 매겨진 데이터 세트에는 권장 사항 모델이 생성한 예측이 포함됩니다.  
  
두 번째 데이터 세트의 열은 점수 매기기 프로세스 중에 수행된 예측의 종류에 따라 달라집니다. 예를 들어 점수가 매겨진 데이터 세트에는 다음 중 하나가 포함될 수 있습니다.

- 사용자, 항목 및 사용자가 항목에 대해 제공할 가능성이 있는 등급
- 권장되는 사용자 및 항목 목록 

### <a name="metrics"></a>메트릭

모델에 대한 성능 메트릭은 입력 유형을 기반으로 생성됩니다. 자세한 내용은 아래 섹션을 참조하세요.

## <a name="evaluate-predicted-ratings"></a>예측 등급 평가  

예측 등급을 평가할 때 점수가 매겨진 데이터 세트(추천 평가에 대한 두 번째 입력)에는 이러한 요구 사항을 충족하는 사용자-항목-등급 삼중 쌍이 포함되어야 합니다.
  
-   데이터 세트의 첫 번째 열에는 사용자 식별자가 포함됩니다.    
-   두 번째 열에는 항목 식별자가 포함됩니다.  
-   세 번째 열은 해당 사용자-항목 등급이 포함됩니다.  
  
> [!IMPORTANT] 
> 성공적으로 평가하려면 열 이름이 각각 `User`, `Item`, `Rating`이어야 합니다.  
  
추천 평가는 “지상 실측” 데이터 세트의 등급을 점수가 매겨진 데이터 세트의 예측 등급과 비교합니다. 그런 다음 MAE(평균 절대 오차)와 RMSE(제곱 평균 오차)를 계산합니다.



## <a name="evaluate-item-recommendations"></a>항목 권장 사항 평가

항목 권장 사항을 평가할 때 각 사용자에 대해 권장되는 항목을 포함하는 점수가 매겨진 데이터 세트를 사용합니다.
  
-   데이터 세트의 첫 번째 열에는 사용자 식별자가 포함되어야 합니다.    
-   모든 후속 열에는 해당 항목이 사용자에게 얼마나 관련성에 있는지에 따라 정렬한 해당 권장 항목 식별자가 포함되어야 합니다. 

해당 데이터 세트를 연결하기 전에 가장 관련성이 높은 항목을 먼저 가져오도록 데이터 세트를 정렬하는 것이 좋습니다.  

> [!IMPORTANT] 
> 추천 평가가 작동하려면 열 이름이 `User`, `Item 1`, `Item 2`, `Item 3` 등이어야 합니다.  
  
추천 평가에서는 평균 NDCG(Normalized Discounted Cumulative Gain)를 계산한 다음 출력 데이터 세트에 반환합니다.  
  
권장 항목에 대한 실제 “지상 실측”을 알 수 없으므로 추천 평가에서는 테스트 데이터 세트의 사용자-항목 등급을 NDCG 계산에서 이점으로 사용합니다. 평가 하려면 추천 점수 매기기 구성 요소가 테스트 데이터 집합에서 "그라운드" 등급을 가진 항목에 대 한 권장 사항만 생성 해야 합니다.  
  

## <a name="next-steps"></a>다음 단계

Azure Machine Learning 하는 데 [사용할 수 있는 구성 요소 집합](component-reference.md) 을 참조 하세요. 
