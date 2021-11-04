---
title: '클러스터에 데이터 할당: 구성 요소 참조'
titleSuffix: Azure Machine Learning
description: 클러스터링 모델의 점수를 매기는 Azure Machine Learning의 클러스터에 데이터 할당 구성 요소를 사용 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: fa639154b1f22364fa0923a4162b4d4aaf19c9d3
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570043"
---
# <a name="component-assign-data-to-clusters"></a>구성 요소: 클러스터에 데이터 할당

이 문서에서는 Azure Machine Learning designer에서 *클러스터에 데이터 할당* 구성 요소를 사용 하는 방법을 설명 합니다. 구성 요소는 *K를 의미* 하는 클러스터링 알고리즘을 사용 하 여 학습 된 클러스터링 모델을 통해 예측을 생성 합니다.

클러스터에 데이터 할당 구성 요소는 각 새 데이터 요소에 대해 가능한 할당을 포함 하는 데이터 집합을 반환 합니다. 

## <a name="how-to-use-assign-data-to-clusters"></a>클러스터에 데이터 할당을 사용하는 방법
  
1. Azure Machine Learning 디자이너에서 이전에 학습된 클러스터링 모델을 찾습니다. 다음 방법 중 하나를 사용하여 클러스터링 모델을 만들고 학습할 수 있습니다.  
  
    - K를 구성 하 [여 클러스터링 알고리즘](k-means-clustering.md) 을 구성 하 고, 데이터 집합 및 학습 클러스터링 모델 구성 요소를 사용 하 여 모델을 학습 합니다 (이 문서).  
  
    - 작업 영역의 **저장된 모델** 그룹에서 기존의 학습된 클러스터링 모델을 추가할 수도 있습니다.

2. **클러스터에 데이터 할당** 의 왼쪽 입력 포트에 학습된 모델을 연결합니다.  

3. 새 데이터 세트를 입력으로 연결합니다. 

   이 데이터 세트에서 레이블은 선택 사항입니다. 일반적으로 클러스터링은 자율 학습 방법입니다. 범주를 미리 알고 있어야 하는 것은 아닙니다. 그러나 입력 열은 클러스터링 모델을 학습하는 데 사용된 열과 동일해야 합니다. 그렇지 않으면 오류가 발생합니다.

    > [!TIP]
    > 클러스터 예측에서 디자이너에 기록되는 열 수를 줄이려면 [데이터 세트에서 열 선택](select-columns-in-dataset.md)을 사용하고 열 하위 집합을 선택합니다. 
    
4. 결과(클러스터 할당)를 표시하는 열을 포함하여 전체 입력 데이터 세트를 결과에 포함하려면 **추가하려면 선택/결과만 출력하려면 선택 취소** 확인란을 선택된 상태로 둡니다.
  
    확인란의 선택을 취소하면 결과만 반환됩니다. 이 옵션은 웹 서비스의 일부로 예측을 만드는 경우에 유용할 수 있습니다.
  
5.  파이프라인을 제출합니다.  
  
### <a name="results"></a>결과

+  데이터 집합의 값을 보려면 해당 구성 요소를 마우스 오른쪽 단추로 클릭 한 다음 **시각화** 를 선택 합니다. 또는 구성 요소를 선택 하 고 오른쪽 패널의 **출력** 탭으로 전환한 다음 **포트 출력** 의 히스토그램 아이콘을 클릭 하 여 결과를 시각화 합니다.
