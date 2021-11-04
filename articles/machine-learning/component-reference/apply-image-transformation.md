---
title: 이미지 변환 적용
titleSuffix: Azure Machine Learning
description: 이미지 변환 적용 구성 요소를 사용하여 이미지 디렉터리에 이미지 변환을 적용하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 45929ecdcc4327a12e2b2867a0cb0c95ca78a3fd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570500"
---
# <a name="apply-image-transformation"></a>이미지 변환 적용 

이 문서에서는 Azure Machine Learning 디자이너에서 이미지 변환 적용 구성 요소를 사용하여 이전에 지정된 이미지 변환을 기반으로 입력 이미지 디렉터리를 수정하는 방법을 설명합니다.  

[Init Image Transformation](init-image-transformation.md) 구성 요소를 연결하여 변환을 지정한 다음, 이러한 변환을 이미지 변환 적용 구성 요소의 입력 이미지 디렉터리에 적용할 수 있습니다.

## <a name="how-to-use-apply-image-transformation"></a>[이미지 변환 적용]을 사용하는 방법  

1. **파이프라인에 이미지 변환 적용** 구성 요소를 추가합니다. 이 구성 요소는 *Computer Vision/이미지 데이터 변환* 범주에서 찾을 수 있습니다. 

2. **이미지 변환 시작** 의 출력을 **이미지 변환 적용** 의 왼쪽 입력에 연결합니다.

     > [!NOTE]
     > [Init Image Transformation](init-image-transformation.md) 구성 요소에서 생성된 이미지 변환만 이 구성 요소에 허용됩니다. 다른 종류의 변환의 경우 [변환 적용](apply-transformation.md)에 연결하세요. 그렇지 않으면 'InvalidTransformationDirectoryError'가 throw됩니다.


3. 변환하려는 이미지 디렉터리를 연결합니다.

4. **모드** 의 경우 입력 변환을 사용할 목적으로 '학습용 ' 또는 '유추용'을 지정합니다. 

   **학습용** 을 선택하는 경우 [이미지 변환 시작]에서 지정한 모든 변환이 적용됩니다.

   **유추용** 을 선택하는 경우 새 샘플을 임의로 만드는 것과 같은 변환이 적용되기 전에 제외됩니다. 이는 '임의의 수평 대칭 이동'과 같이 새 샘플을 임의로 만드는 변환 작업이 학습에서 데이터 확대에 사용되기 때문입니다. 정확한 예측 및 평가를 위해 유추 샘플을 수정해야 하기 때문에 유추에서 제거해야 합니다.

   > [!NOTE]
   > **유추용** 모드에서 제외되는 변환은 임의의 크기 조정 자르기, 임의 자르기, 임의 가로 대칭 이동, 임의 수직 대칭 이동, 임의 회전, 임의 관계, 임의 회색조, 임의 원근, 임의 지우기입니다.

5. 새 이미지 디렉터리에 이미지 변환을 적용하려면 파이프라인을 제출합니다.  

### <a name="component-parameters"></a>구성 요소 매개 변수

| Name | 범위 | Type | 기본값                   | Description                              |
| ---- | ----- | ---- | ------------------------- | ---------------------------------------- |
| Mode | 모두   | 모드 | (사용자 지정 필요) | 입력 변환을 사용하는 용도. '임의' 변환 작업은 유추에서 제외하고 학습에 유지해야 합니다. |

### <a name="expected-inputs"></a>예상 입력  

| Name                       | Type                    | 설명                       |
| -------------------------- | ----------------------- | --------------------------------- |
| 이미지 변환 입력 | TransformationDirectory | 이미지 변환 입력        |
| 이미지 디렉터리 입력      | ImageDirectory          | 변환할 이미지 디렉터리 |

### <a name="outputs"></a>출력  

| Name                   | Type           | Description            |
| ---------------------- | -------------- | ---------------------- |
| 이미지 디렉터리 출력 | ImageDirectory | 이미지 디렉터리 출력 |

## <a name="next-steps"></a>다음 단계

Azure Machine Learning [사용할 수 있는 구성 요소 집합을](component-reference.md) 참조하세요. 
