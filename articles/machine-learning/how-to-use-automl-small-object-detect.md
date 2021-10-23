---
title: AutoML을 사용 하 여 이미지의 작은 개체 검색
description: Azure Machine Learning 자동화 ML를 설정 하 여 작은 개체 검색 모델을 학습 합니다.
author: PhaniShekhar
ms.author: phmantri
ms.service: machine-learning
ms.subservice: automl
ms.topic: how-to
ms.date: 10/13/2021
ms.openlocfilehash: 7800399be259dddf2782c47696ce014442d8bf55
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234456"
---
# <a name="train-a-small-object-detection-model-with-automl-preview"></a>AutoML (미리 보기)을 사용 하 여 작은 개체 검색 모델 학습

> [!IMPORTANT]
> 이 기능은 현재 공개 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 Azure Machine Learning에서 [자동화 된 ML](concept-automated-ml.md) 를 사용 하 여 고해상도 이미지에서 작은 개체를 검색 하도록 개체 검색 모델을 학습 하는 방법을 배웁니다.

일반적으로 개체 검색에 대 한 컴퓨터 비전 모델은 비교적 규모가 긴 개체가 있는 데이터 집합에서 효과적으로 작동 합니다. 그러나 메모리 및 계산 제약 조건으로 인해 이러한 모델은 고해상도 이미지에서 작은 개체를 검색할 때 실행 되는 경향이 있습니다. 고해상도 이미지는 일반적으로 크기 때문에 모델에 입력 하기 전에 크기를 조정 하 여 초기 이미지 크기에 상대적인 작은 개체를 검색 하는 기능을 제한 합니다.

이 문제를 해결 하기 위해 자동화 된 ML는 공개 미리 보기 컴퓨터 비전 기능의 일부로 바둑판식 배열을 지원 합니다. 자동화 된 ML의 바둑판식 배열 기능은 [작은 개체 검색에 대 한 바둑판식 배열](https://openaccess.thecvf.com/content_CVPRW_2019/papers/UAVision/Unel_The_Power_of_Tiling_for_Small_Object_Detection_CVPRW_2019_paper.pdf)기능의 개념을 기반으로 합니다.

바둑판식으로 배열 하면 각 이미지가 타일의 그리드로 나뉩니다. 인접 한 타일은 너비 및 높이 차원에서 서로 겹칩니다. 다음 이미지와 같이 타일은 원본에서 잘립니다. 

![타일 생성](./media/how-to-use-automl-small-object-detect/tiles-generation.png)

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 작업 영역을 만들려면 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

* 이 문서에서는 [컴퓨터 비전 작업에 대해 자동화 된 machine learning 실험](how-to-auto-train-image-models.md)을 구성 하는 방법을 잘 알고 있다고 가정 합니다. 

## <a name="supported-models"></a>지원되는 모델

바둑판식 배열을 사용 하는 작은 개체 검색은 현재 다음 모델에 대해 지원 됩니다.

* fasterrcnn_resnet18_fpn
* fasterrcnn_resnet50_fpn
* fasterrcnn_resnet34_fpn
* fasterrcnn_resnet101_fpn
* fasterrcnn_resnet152_fpn
* retinanet_resnet50_fpn

## <a name="enable-tiling-during-training"></a>학습 중 바둑판식 배열 사용

바둑판식 배열을 사용 하기 위해 `tile_grid_size` (3, 2)와 같은 값으로 매개 변수를 설정할 수 있습니다. 여기서 3은 너비 차원의 타일 수이 고 2는 height 차원에 따른 타일 수입니다. 이 매개 변수를 (3, 2)로 설정 하면 각 이미지가 3 x 2 타일의 그리드로 분할 됩니다. 각 타일은 인접 한 타일과 겹치므로 타일 테두리에 속하는 모든 개체는 타일 중 하나에 완전히 포함 됩니다. 이러한 겹침은 매개 변수를 통해 제어할 수 있으며 `tile_overlap_ratio` 기본값은 25%입니다.

바둑판식 배열을 사용 하는 경우 전체 이미지와 여기에서 생성 된 타일이 모델을 통해 전달 됩니다. 이러한 이미지와 타일은 `min_size` `max_size` 모델에 공급 하기 전에 및 매개 변수에 따라 크기가 조정 됩니다. 이 추가 데이터를 처리 하 여 계산 시간이 비례적으로 늘어납니다. 

예를 들어 `tile_grid_size` 매개 변수가 (3, 2) 이면 바둑판식 배열 없이 계산 시간이 약 7 배가 됩니다.

하이퍼 `tile_grid_size` 매개 변수 공간에서 값을 문자열로 지정할 수 있습니다.

```python
parameter_space = {
    'model_name': choice('fasterrcnn_resnet50_fpn'),
    'tile_grid_size': choice('(3, 2)'),
    ...
}
```

매개 변수 값은 이미지 크기 `tile_grid_size` 및 이미지 내 개체 크기에 따라 달라 집니다. 예를 들어 많은 수의 타일이 이미지에 더 작은 개체가 있는 경우 유용 합니다.

데이터 집합에 대해이 매개 변수에 대 한 최적의 값을 선택 하려면 하이퍼 매개 변수 검색을 사용할 수 있습니다. 이렇게 하려면 하이퍼 매개 변수 공간에서이 매개 변수에 대해 선택할 수 있는 값을 지정할 수 있습니다.

```python
parameter_space = {
    'model_name': choice('fasterrcnn_resnet50_fpn'),
    'tile_grid_size': choice('(2, 1)', '(3, 2)', '(5, 3)'),
    ...
}
```
## <a name="tiling-during-inference"></a>유추 중 바둑판식 배열

바둑판식 배열을 사용 하 여 학습 된 모델을 배포 하는 경우에는 유추 과정에서 바둑판식 배열도 발생 합니다. 자동화 된 ML는 `tile_grid_size` 학습의 값을 사용 하 여 유추 하는 동안 타일을 생성 합니다. 전체 이미지 및 해당 타일은 모델을 통해 전달 되 고, 다음 이미지와 같이 모델의 개체 제안이 병합 되어 최종 예측을 출력 합니다.

![개체 제안 병합](./media/how-to-use-automl-small-object-detect/tiles-merge.png)

> [!NOTE] 
> 여러 타일에서 동일한 개체가 검색 될 수 있습니다. 중복 검색은 이러한 중복을 제거 하기 위해 수행 됩니다.
>
> 중복 검색은 타일 및 이미지의 제안에 대해 NMS를 실행 하 여 수행 됩니다. 여러 제안이 겹치면 점수가 가장 높은 항목이 선택 되 고 다른 항목이 중복으로 삭제 됩니다. 두 제안 간의 교집합 (iou)이 매개 변수 보다 클 경우 두 개의 제안이 겹치는 것으로 간주 됩니다 `tile_predictions_nms_thresh` .

또한 학습에 사용 하도록 설정 하지 않고 유추 중에만 바둑판식 배열을 사용 하도록 설정할 수 있습니다. 이렇게 하려면 `tile_grid_size` 유추가 아니라 유추 중에만 매개 변수를 설정 합니다. 

이렇게 하면에서 일부 데이터 집합의 성능을 향상 시킬 수 있으며, 학습 시간에 바둑판식 배열에 추가 비용이 발생 하지 않습니다. 

## <a name="tiling-hyperparameters"></a>하이퍼 매개 변수 바둑판식 배열 

다음은 바둑판식 배열 기능을 제어 하는 데 사용할 수 있는 매개 변수입니다.

| 매개 변수 이름    | 설명   | 기본값 |
| --------------- |-------------| -------|
| `tile_grid_size` |  각 이미지를 바둑판식으로 배열 하는 데 사용할 그리드 크기입니다. 학습, 유효성 검사 및 유추 중에 사용할 수 있습니다.<br><br>문자열로 전달 되는 두 정수의 튜플 (예: `'(3, 2)'`<br><br> *참고:이 매개 변수를 설정 하면 모든 타일과 이미지가 모델에 의해 처리 되므로 계산 시간이 비례적으로 늘어납니다.*| 기본값 없음 |
| `tile_overlap_ratio` | 각 차원에서 인접 한 타일 사이의 겹치기 비율을 제어 합니다. 타일 경계에 속하는 개체가 너무 커서 타일 중 하나에 완전히 들어가지 않는 경우이 매개 변수의 값을 늘려 개체가 하나 이상의 타일에 완전히 맞춰지도록 합니다.<br> <br>  은 [0, 1)의 float 여야 합니다.| 0.25 |
| `tile_predictions_nms_thresh` | 타일 및 이미지의 예측을 병합 하는 동안 최대값이 아닌 비 표시 (nms)를 수행 하는 데 사용 하는 공용 구조체 임계값의 교집합입니다. 유효성 검사 및 유추 중에 사용 가능 합니다. 최종 예측에서 개체 별로 여러 상자가 검색 되 면이 매개 변수를 변경 합니다.  <br><br> [0, 1]의 float 여야 합니다. | 0.25 |


## <a name="example-notebooks"></a>노트북 예제

개체 검색 모델을 설정 하 고 학습 하는 방법에 대 한 자세한 코드 예제는 [개체 검색 샘플 노트북](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml/image-object-detection/auto-ml-image-object-detection.ipynb) 을 참조 하세요.

>[!NOTE]
> 이 문서의 모든 이미지는 [MIT 라이선스 계약](https://choosealicense.com/licenses/mit/)의 허용 된 사용 섹션에 따라 사용할 수 있습니다.  
> Copyright © 2020 Roboflow, i n c.

## <a name="next-steps"></a>다음 단계

* [모델 배포 방법 및 위치](how-to-deploy-and-where.md)에 대해 자세히 알아봅니다.
* 각 실행에 대해 제공되는 성능 차트 및 메트릭의 정의와 예제는 [자동화된 Machine Learning 실험 결과 평가](how-to-understand-automated-ml.md)를 참조하세요. 
