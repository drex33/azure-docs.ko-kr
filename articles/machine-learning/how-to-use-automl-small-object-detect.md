---
title: AutoML을 사용하여 이미지에서 작은 개체 검색
description: Azure Machine Learning 자동화된 ML 설정하여 작은 개체 검색 모델을 학습합니다.
author: PhaniShekhar
ms.author: phmantri
ms.service: machine-learning
ms.subservice: automl
ms.topic: how-to
ms.date: 10/13/2021
ms.openlocfilehash: 2fb5dfc77a15e1f9fcd5b90a058e2cc03f81b910
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130008785"
---
# <a name="train-a-small-object-detection-model-with-automl-preview"></a>AutoML을 사용하여 작은 개체 검색 모델 학습(미리 보기)

> [!IMPORTANT]
> 이 기능은 현재 공개 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 Azure Machine Learning [자동화된 ML](concept-automated-ml.md) 사용하여 고해상도 이미지에서 작은 개체를 검색하도록 개체 검색 모델을 학습하는 방법을 알아봅니다.

일반적으로 개체 감지를 위한 컴퓨터 비전 모델은 비교적 큰 개체가 있는 데이터 세트에 적합합니다. 그러나 메모리 및 계산 제약 조건으로 인해 이러한 모델은 고해상도 이미지에서 작은 개체를 검색해야 할 때 성능이 저하되는 경향이 있습니다. 고해상도 이미지는 일반적으로 크기가 크기 때문에 모델에 입력하기 전에 크기가 조정되므로 초기 이미지 크기를 기준으로 더 작은 개체를 검색하는 기능이 제한됩니다.

이 문제를 해결하기 위해 자동화된 ML 공개 미리 보기 컴퓨터 비전 기능의 일부로 바둑판식 배열을 지원합니다. 자동화된 ML 바둑판식 배열 기능은 작은 개체 [감지를 위한 바둑판식 배열 기능의](https://openaccess.thecvf.com/content_CVPRW_2019/papers/UAVision/Unel_The_Power_of_Tiling_for_Small_Object_Detection_CVPRW_2019_paper.pdf)개념을 기반으로 합니다.

바둑판식 배열을 할 때 각 이미지는 타일 그리드로 나뉩니다. 인접한 타일은 너비 및 높이 차원에서 서로 겹칩니다. 타일은 다음 이미지와 같이 원본에서 잘립니다. 

![타일 생성](./media/how-to-use-automl-small-object-detect/tiles-generation.png)

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 작업 영역을 만들려면 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

* 이 문서에서는 컴퓨터 비전 작업에 대해 자동화된 기계 학습 실험을 구성하는 방법에 대해 잘 알고 있다고 [가정합니다.](how-to-auto-train-image-models.md) 

[!INCLUDE [automl-sdk-version](../../includes/machine-learning-automl-sdk-version.md)]

## <a name="supported-models"></a>지원되는 모델

바둑판식 배열을 사용하는 작은 개체 검색은 현재 다음 모델에 대해 지원됩니다.

* fasterrcnn_resnet18_fpn
* fasterrcnn_resnet50_fpn
* fasterrcnn_resnet34_fpn
* fasterrcnn_resnet101_fpn
* fasterrcnn_resnet152_fpn
* retinanet_resnet50_fpn

## <a name="enable-tiling-during-training"></a>학습 중에 바둑판식 배열 사용

바둑판식 배열을 사용하려면 `tile_grid_size` 매개 변수를 (3, 2)와 같은 값으로 설정할 수 있습니다. 여기서 3은 너비 차원의 타일 수이고 2는 높이 차원의 타일 수입니다. 이 매개 변수를 (3, 2)로 설정하면 각 이미지가 3 x 2 타일의 그리드로 분할됩니다. 각 타일은 인접한 타일과 겹치므로 타일 테두리에 있는 모든 개체가 타일 중 하나에 완전히 포함됩니다. 이 중첩은 매개 변수를 통해 제어할 수 `tile_overlap_ratio` 있으며, 기본값은 25%입니다.

바둑판식 배열을 사용하도록 설정하면 이미지에서 생성된 전체 이미지와 타일이 모델을 통해 전달됩니다. 이러한 이미지와 타일은 `min_size` 모델에 공급하기 전에 및 매개 변수에 따라 `max_size` 크기가 조정됩니다. 이 추가 데이터를 처리하므로 계산 시간이 비례적으로 증가합니다. 

예를 들어 `tile_grid_size` 매개 변수가 (3, 2)이면 계산 시간은 바둑판식 배열이 없는 것과 비교했을 때 약 7배입니다.

하이퍼매개수 공간에서 값을 문자열로 지정할 수 `tile_grid_size` 있습니다.

```python
parameter_space = {
    'model_name': choice('fasterrcnn_resnet50_fpn'),
    'tile_grid_size': choice('(3, 2)'),
    ...
}
```

매개 변수의 값은 `tile_grid_size` 이미지 내의 이미지 크기 및 개체 크기에 따라 달라집니다. 예를 들어 이미지에 더 작은 개체가 있는 경우 더 많은 수의 타일이 유용할 수 있습니다.

데이터 세트에 대해 이 매개 변수의 최적 값을 선택하려면 하이퍼 매개 변수 검색을 사용할 수 있습니다. 이렇게 하려면 하이퍼 매개 변수 공간에서 이 매개 변수에 대한 값 선택을 지정할 수 있습니다.

```python
parameter_space = {
    'model_name': choice('fasterrcnn_resnet50_fpn'),
    'tile_grid_size': choice('(2, 1)', '(3, 2)', '(5, 3)'),
    ...
}
```
## <a name="tiling-during-inference"></a>유추 중 바둑판식 배열

바둑판식 배열로 학습된 모델을 배포하면 유추 중에도 바둑판식 배열이 발생합니다. 자동화된 ML 학습의 값을 사용하여 `tile_grid_size` 유추 중에 타일을 생성합니다. 전체 이미지와 해당 타일이 모델을 통해 전달되고, 해당 타일의 개체 제안이 다음 이미지와 같이 최종 예측을 출력하도록 병합됩니다.

![개체 제안 병합](./media/how-to-use-automl-small-object-detect/tiles-merge.png)

> [!NOTE] 
> 여러 타일에서 동일한 개체가 검색될 수 있습니다. 중복 검색은 이러한 중복 항목을 제거하기 위해 수행됩니다.
>
> 타일 및 이미지의 제안에 대해 NMS를 실행하여 중복 검색을 수행합니다. 여러 제안이 겹치면 점수가 가장 높은 제안이 선택되고 다른 제안은 중복으로 삭제됩니다. 두 제안 간의 교집합(iou)이 매개 변수보다 크면 두 제안이 겹치는 것으로 `tile_predictions_nms_thresh` 간주됩니다.

또한 학습에서 사용하지 않고 유추 중에만 바둑판식 배열을 사용하도록 설정하는 옵션이 있습니다. 이렇게 하려면 `tile_grid_size` 학습용이 아니라 유추 중에만 매개 변수를 설정합니다. 

이렇게 하면 일부 데이터 세트의 성능이 향상될 수 있으며, 학습 시 바둑판식 배열과 함께 제공되는 추가 비용이 발생하지 않습니다. 

## <a name="tiling-hyperparameters"></a>하이퍼 부모 바둑판식 배열 

다음은 바둑판식 배열 기능을 제어하는 데 사용할 수 있는 매개 변수입니다.

| 매개 변수 이름    | 설명   | 기본값 |
| --------------- |-------------| -------|
| `tile_grid_size` |  각 이미지를 바둑판식으로 배열하는 데 사용할 그리드 크기입니다. 학습, 유효성 검사 및 유추 중에 사용할 수 있습니다.<br><br>문자열로 전달된 두 정수의 튜플(예: `'(3, 2)'`<br><br> *참고: 이 매개 변수를 설정하면 모든 타일과 이미지가 모델에서 처리되므로 계산 시간이 비례적으로 증가합니다.*| 기본값 없음 |
| `tile_overlap_ratio` | 각 차원의 인접 타일 간 겹침 비율을 제어합니다. 타일 경계에 속하는 개체가 너무 커서 타일 중 하나에 완전히 맞지 않는 경우 개체가 타일 중 하나 이상에 완전히 맞도록 이 매개 변수의 값을 늘립니다.<br> <br>  [0, 1)의 float여야 합니다.| 0.25 |
| `tile_predictions_nms_thresh` | 타일 및 이미지에서 예측을 병합하는 동안 nms(최대값이 아닌 비표시 제거)를 수행하는 데 사용할 공용선 임계값을 초과하는 교집합입니다. 유효성 검사 및 유추 중에 사용할 수 있습니다. 최종 예측에서 개체당 여러 상자가 검색된 경우 이 매개 변수를 변경합니다.  <br><br> [0, 1]에서 float여야 합니다. | 0.25 |


## <a name="example-notebooks"></a>노트북 예제

개체 검색 모델을 설정하고 학습하는 자세한 코드 예제는 개체 검색 [샘플 Notebook을](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml/image-object-detection/auto-ml-image-object-detection.ipynb) 참조하세요.

>[!NOTE]
> 이 문서의 모든 이미지는 [MIT 라이선싱 계약의](https://choosealicense.com/licenses/mit/)허용된 사용 섹션에 따라 사용할 수 있습니다.  
> Copyright © 2020 Roboflow, Inc.

## <a name="next-steps"></a>다음 단계

* [모델 배포 방법 및 위치](how-to-deploy-and-where.md)에 대해 자세히 알아봅니다.
* 각 실행에 대해 제공되는 성능 차트 및 메트릭의 정의와 예제는 [자동화된 Machine Learning 실험 결과 평가](how-to-understand-automated-ml.md)를 참조하세요. 
