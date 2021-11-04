---
title: '변형 적용: 구성 요소 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 변형 구성 요소를 사용 하 여 이전에 계산 된 변환을 기반으로 입력 데이터 집합을 수정 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/26/2020
ms.openlocfilehash: 3091c4d85594f5bcb4236f04f8d58e21dfe125de
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570235"
---
# <a name="apply-transformation-component"></a>변형 구성 요소 적용

이 문서에서는 Azure Machine Learning 디자이너의 구성 요소에 대해 설명 합니다.

이 구성 요소를 사용 하 여 이전에 계산 된 변환을 기반으로 입력 데이터 집합을 수정 합니다. 이 구성 요소는 유추 파이프라인에서 변환을 업데이트 해야 하는 경우에 필요 합니다.

예를 들어 **데이터 정규화** 구성 요소를 사용 하 여 z 점수를 사용 하 여 학습 데이터를 정규화 한 경우 점수 매기기 단계 중에 학습에 대해 계산 된 z 점수 값을 사용 하는 것이 좋습니다. Azure Machine Learning에서는 정규화 방법을 변환으로 저장한 다음 **변환 적용** 을 사용하여 점수를 매기기 전에 입력 데이터에 z 점수를 적용할 수 있습니다.

## <a name="how-to-save-transformations"></a>변환 저장 방법

디자이너를 사용하면 데이터 변환을 다른 파이프라인에서 사용할 수 있도록 **데이터 집합** 으로 저장할 수 있습니다.

1. 성공적으로 실행 된 데이터 변환 구성 요소를 선택 합니다.

1. **출력 + 로그** 탭을 선택합니다.

1. 변환 출력을 찾고, **데이터 집합** 을 선택 하 여 구성 요소 팔레트의 **데이터 집합** 범주 아래에 구성 요소로 저장 합니다.

## <a name="how-to-use-apply-transformation"></a>변형 적용 사용 방법  
  
1. 파이프라인에 **적용 변환** 구성 요소를 추가 합니다. 구성 요소 팔레트의 **모델 점수 매기기 & 평가** 섹션에서이 구성 요소를 찾을 수 있습니다. 
  
1. 구성 요소 팔레트의 **데이터 집합** 에서 사용 하려는 저장 된 변환을 찾습니다.

1. 저장 된 변환의 출력을 **변환 적용** 구성 요소의 왼쪽 입력 포트에 커넥트 합니다.

    데이터 집합에는 변환이 처음 디자인된 데이터 집합과 정확히 동일한 스키마(열 수, 열 이름, 데이터 형식)가 있어야 합니다.  
  
1. 원하는 구성 요소의 데이터 집합 출력을 **적용 변환** 구성 요소의 오른쪽 입력 포트에 커넥트 합니다.
  
1. 새 데이터 집합에 변환을 적용하려면 파이프라인을 제출합니다.  

> [!IMPORTANT]
> 학습 파이프라인에서 업데이트된 변환도 유추 파이프라인에서 가능하도록 하려면, 학습 파이프라인에서 업데이트된 변환이 있을 때마다 아래 단계를 수행해야 합니다.
> 1. 학습 파이프라인에서 [열 변환 선택](select-columns-transform.md)의 출력을 데이터 집합으로 등록 합니다.
> ![구성 요소 출력의 데이터 집합 등록](media/module/select-columns-transform-register-dataset.png)
> 1. 유추 파이프라인에서 **TD** 구성 요소를 제거 하 고 이전 단계에서 등록 된 데이터 집합으로 바꿉니다.
> ![TD 구성 요소 바꾸기](media/module/replace-tranformation-directory.png)

## <a name="next-steps"></a>다음 단계

Azure Machine Learning 하는 데 [사용할 수 있는 구성 요소 집합](component-reference.md) 을 참조 하세요. 