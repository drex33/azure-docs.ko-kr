---
title: Computer Vision용 AutoML 설정
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Python SDK(미리 보기)를 사용하여 Computer Vision 모델을 학습하도록 자동화된 ML Azure Machine Learning 설정합니다.
services: machine-learning
author: swatig007
ms.author: swatig
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: automl
ms.date: 10/06/2021
ms.openlocfilehash: 312eea28371de6726bca5d1a89e305024e552dad
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254368"
---
# <a name="set-up-automl-to-train-computer-vision-models-with-python-preview"></a>Python을 사용하여 컴퓨터 비전 모델을 학습하도록 AutoML 설정(미리 보기)

> [!IMPORTANT]
> 이 기능은 현재 공개 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 [Azure Machine Learning Python SDK에서](/python/api/overview/azure/ml/)자동화된 ML 사용하여 이미지 데이터에 대한 컴퓨터 비전 모델을 학습하는 방법을 알아봅니다.

자동화된 ML 이미지 분류, 개체 감지 및 인스턴스 구분과 같은 컴퓨터 비전 작업에 대한 모델 학습을 지원합니다. Computer Vision 작업에 대한 AutoML 모델 작성은 현재 Azure Machine Learning Python SDK를 통해 지원됩니다. 결과 실험 실행, 모델 및 출력은 Azure Machine Learning Studio UI에서 액세스할 수 있습니다. [이미지 데이터에 대한 컴퓨터 비전 작업에 대한 자동화된 ml에 대해 자세히 알아봅니다.](concept-automated-ml.md)

> [!NOTE]
> Computer Vision 작업에 대한 자동화된 ML Azure Machine Learning Python SDK를 통해서만 사용할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 작업 영역을 만들려면 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

* 설치된 Azure Machine Learning Python SDK.
    SDK를 설치하려면 다음 중 하나를 수행할 수 있습니다. 
    * SDK를 자동으로 설치하고 ML 워크플로에 대해 미리 구성된 컴퓨팅 인스턴스를 만듭니다. 자세한 내용은 [Azure Machine Learning 컴퓨팅 인스턴스 만들기 및 관리를 참조하세요.](how-to-create-manage-compute-instance.md)

    * [`automl` 패키지를 직접 설치](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment)합니다. 여기에는 SDK의 [기본 설치](/python/api/overview/azure/ml/install#default-install)가 포함됩니다.
    
    > [!NOTE]
    > Python 3.6 및 3.7만 Computer Vision 작업에 대한 자동화된 ML 지원과 호환됩니다. 

## <a name="select-your-task-type"></a>작업 유형 선택
이미지에 대한 자동화된 ML 다음 작업 유형을 지원합니다.


작업 유형 | AutoMLImage 구성 구문
---|---
 이미지 분류 | `ImageTask.IMAGE_CLASSIFICATION`
이미지 분류 다중 레이블 | `ImageTask.IMAGE_CLASSIFICATION_MULTILABEL`
이미지 개체 검색 | `ImageTask.IMAGE_OBJECT_DETECTION`
이미지 인스턴스 구분| `ImageTask.IMAGE_INSTANCE_SEGMENTATION`

이 작업 유형은 필수 매개 변수이며 의 매개 변수를 사용하여 `task` 전달됩니다. `AutoMLImageConfig` 예를 들면 다음과 같습니다.

```python
from azureml.train.automl import AutoMLImageConfig
from azureml.automl.core.shared.constants import ImageTask
automl_image_config = AutoMLImageConfig(task=ImageTask.IMAGE_OBJECT_DETECTION)
```

## <a name="training-and-validation-data"></a>학습 및 유효성 검사 데이터

컴퓨터 비전 모델을 생성하려면 레이블이 지정되어 있는 이미지 데이터를 Azure Machine Learning [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset)형식의 모델 학습에 대한 입력으로 가져와야 합니다. 데이터 레이블 지정 프로젝트에서 내보낸 을 `TabularDataset` 사용하거나 레이블이 [지정한](./how-to-create-image-labeling-projects.md#export-the-labels)학습 데이터를 사용하여 새 를 만들 `TabularDataset` 수 있습니다. 

학습 데이터가 다른 형식(예: 파스칼 VOC 또는 COCO)인 경우 샘플 Notebook에 포함된 도우미 스크립트를 적용하여 데이터를 JSONL로 변환할 수 있습니다. [자동화된 ML](how-to-prepare-datasets-for-automl-images.md)사용하여 Computer Vision 작업을 위한 데이터를 준비하는 방법에 대해 자세히 알아봅니다. 

> [!Warning]
> TabularDatasets 만들기는 SDK를 사용하여 이 기능에 대한 JSONL 형식의 데이터에서 데이터 세트를 만드는 것만 지원됩니다. UI를 통해 데이터 세트를 만드는 것은 현재 지원되지 않습니다.


### <a name="jsonl-schema-samples"></a>JSONL 스키마 샘플

TabularDataset의 구조는 현재 작업에 따라 달라집니다. Computer Vision 작업 유형의 경우 다음 필드로 구성됩니다.

필드| 설명
---|---
`image_url`| Filepath를 StreamInfo 개체로 포함합니다.
`image_details`|이미지 메타데이터 정보는 높이, 너비 및 형식으로 구성됩니다. 이 필드는 선택 사항이므로 존재하거나 존재하지 않을 수 있습니다.
`label`| 작업 유형을 기반으로 하는 이미지 레이블의 json 표현입니다.

다음은 이미지 분류를 위한 샘플 JSONL 파일입니다.

```python
{
      "image_url": "AmlDatastore://image_data/Image_01.png",
      "image_details":
      {
          "format": "png",
          "width": "2230px",
          "height": "4356px"
      },
      "label": "cat"
  }
  {
      "image_url": "AmlDatastore://image_data/Image_02.jpeg",
      "image_details":
      {
          "format": "jpeg",
          "width": "3456px",
          "height": "3467px"
      },
      "label": "dog"
  }
  ```

  다음 코드는 개체 검색을 위한 샘플 JSONL 파일입니다.

  ```python
  {
      "image_url": "AmlDatastore://image_data/Image_01.png",
      "image_details":
      {
          "format": "png",
          "width": "2230px",
          "height": "4356px"
      },
      "label":
      {
          "label": "cat",
          "topX": "1",
          "topY": "0",
          "bottomX": "0",
          "bottomY": "1",
          "isCrowd": "true",
      }
  }
  {
      "image_url": "AmlDatastore://image_data/Image_02.png",
      "image_details":
      {
          "format": "jpeg",
          "width": "1230px",
          "height": "2356px"
      },
      "label":
      {
          "label": "dog",
          "topX": "0",
          "topY": "1",
          "bottomX": "0",
          "bottomY": "1",
          "isCrowd": "false",
      }
  }
  ```


### <a name="consume-data"></a>데이터 사용

데이터가 JSONL 형식이 되면 다음 코드를 사용하여 TabularDataset를 만들 수 있습니다.

```python
from azureml.core import Dataset

training_dataset = Dataset.Tabular.from_json_lines_files(
        path=ds.path('odFridgeObjects/odFridgeObjects.jsonl'),
        set_column_types={'image_url': DataType.to_stream(ds.workspace)})
training_dataset = training_dataset.register(workspace=ws, name=training_dataset_name)
```

자동화된 ML Computer Vision 작업의 학습 또는 유효성 검사 데이터 크기에 제약 조건을 적용하지 않습니다. 최대 데이터 세트 크기는 데이터 세트 뒤의 스토리지 계층(예: Blob 저장소)에 의해서만 제한됩니다. 이미지 또는 레이블의 최소 수는 없습니다. 그러나 출력 모델이 충분히 학습되도록 레이블당 최소 10~15개의 샘플로 시작하는 것이 좋습니다. 레이블/클래스의 총 수가 많을수록 레이블당 더 많은 샘플이 필요합니다.



학습 데이터는 필수이며 매개 변수를 사용하여 `training_data` 전달됩니다. 필요에 따라 AutoMLImageConfig의 매개 변수를 사용하여 모델에 사용할 유효성 검사 데이터 세트로 다른 TabularDataset를 지정할 수 `validation_data` 있습니다. 유효성 검사 데이터 세트를 지정하지 않으면 다른 값을 가진 인수를 전달하지 않는 한 기본적으로 학습 데이터의 20%가 유효성 `split_ratio` 검사에 사용됩니다.

예를 들면 다음과 같습니다.

```python
from azureml.train.automl import AutoMLImageConfig
automl_image_config = AutoMLImageConfig(training_data=training_dataset)
```

## <a name="compute-to-run-experiment"></a>실험 실행 컴퓨팅

자동화된 ML 모델 학습을 수행하기 위한 [컴퓨팅 대상을](concept-azure-machine-learning-architecture.md#compute-targets) 제공합니다. 컴퓨터 비전 작업에 대한 자동화된 ML 모델에는 GPU S SKU가 필요하며 NC 및 ND 제품군을 지원해야 합니다. 더 빠른 학습을 위해 NCsv3 시리즈(v100 GPU 사용)를 권장합니다. 다중 GPU VM SKU가 있는 컴퓨팅 대상은 여러 GPU를 활용하여 학습 속도도 단축합니다. 또한 여러 노드가 있는 컴퓨팅 대상을 설정할 때 모델에 대한 하이퍼매개터를 튜닝할 때 병렬 처리로 더 빠른 모델 학습을 수행할 수 있습니다.

컴퓨팅 대상은 필수 매개 변수이며 의 매개 변수를 사용하여 `compute_target` `AutoMLImageConfig` 전달됩니다. 예를 들면 다음과 같습니다.

```python
from azureml.train.automl import AutoMLImageConfig
automl_image_config = AutoMLImageConfig(compute_target=compute_target)
```

## <a name="configure-model-algorithms-and-hyperparameters"></a>모델 알고리즘 및 하이퍼 부모 구성

Computer Vision 작업을 지원하여 모델 알고리즘 및 스윕 하이퍼 부모 변수를 제어할 수 있습니다. 이러한 모델 알고리즘 및 하이퍼 매개 변수는 스윕에 대한 매개 변수 공간으로 전달됩니다.

모델 알고리즘은 필수이며 매개 변수를 통해 `model_name` 전달됩니다. 단일 을 `model_name` 지정하거나 여러 항목 중에서 선택할 수 있습니다. 모델 알고리즘을 제어하는 것 외에도 모델 학습에 사용되는 하이퍼 부모 변수를 튜닝할 수 있습니다. 노출되는 하이퍼 부모 중 상당수는 모델에 구애받지 않지만 하이퍼 부모 구성 요소의 작업별 또는 모델별 인스턴스가 있습니다.

### <a name="supported-model-algorithms"></a>지원되는 모델 알고리즘

다음 표에서는 각 컴퓨터 비전 작업에 대해 지원되는 모델을 요약합니다. 

작업 |  모델 알고리즘 | 문자열 리터럴 구문<br> ***`default_model`\**** 으로 나을 때 \*
---|----------|----------
이미지 분류<br> (다중 클래스 및 다중 레이블)| **MobileNet:** 모바일 애플리케이션에 대한 경량 모델 <br> **ResNet:** 잔여 네트워크<br> **ResNeSt:** 주의 네트워크 분할<br> **SE-ResNeXt50:** 편차 네트워크<br> **ViT:** 비전 변환기 네트워크| `mobilenetv2`   <br>`resnet18` <br>`resnet34` <br> `resnet50`  <br> `resnet101` <br> `resnet152`    <br> `resnest50` <br> `resnest101`  <br> `seresnext`  <br> `vits16r224` (작음) <br> **_`vitb16r224`\_** *(기본) <br>`vitl16r224` (대형)|
개체 감지 | **YOLOv5:** 1단계 개체 검색 모델   <br>  **더 빠른 RCNN ResNet FPN:** 2단계 개체 검색 모델  <br> **RetinaNet ResNet FPN:** Focal Loss를 통해 클래스 불균형을 해결합니다. <br> <br>*참고: YOLOv5 모델 크기에 대해서는 [ `model_size` 하이퍼매개터를](#model-specific-hyperparameters) 참조하세요.*| ***`yolov5`\**** <br> `fasterrcnn_resnet18_fpn` <br> `fasterrcnn_resnet34_fpn` <br> `fasterrcnn_resnet50_fpn` <br> `fasterrcnn_resnet101_fpn` <br> `fasterrcnn_resnet152_fpn` <br> `retinanet_resnet50_fpn` 
인스턴스 구분 | **MaskRCNN ResNet FPN**| `maskrcnn_resnet18_fpn` <br> `maskrcnn_resnet34_fpn` <br> **_`maskrcnn_resnet50_fpn`\_***  <br> `maskrcnn_resnet101_fpn` <br> `maskrcnn_resnet152_fpn` <br>`maskrcnn_resnet50_fpn`


### <a name="model-agnostic-hyperparameters"></a>모델 독립적 하이퍼parameters

다음 표에서는 모델에 구애받지 않는 하이퍼 부모에 대해 설명합니다.

| 매개 변수 이름 | Description | 기본값|
| ------------ | ------------- | ------------ |
| `number_of_epochs` | 학습 Epoch 수입니다. <br>양의 정수여야 합니다. |  15 <br> `yolov5`(제외: 30) |
| `training_batch_size` | 학습 일괄 처리 크기입니다.<br> 양의 정수여야 합니다.  | 다중 클래스/다중 레이블: 78 <br>*(vit-variant 제외:* <br> `vits16r224`: 128 <br>`vitb16r224`: 48 <br>`vitl16r224`:10)<br><br>개체 검색: 2 <br>`yolov5`(제외: 16) <br><br> 인스턴스 구분: 2  <br> <br> *참고: 기본값은 12 GiB GPU 메모리 에서 사용할 수 있는 가장 큰 일괄 처리 크기입니다.*|
| `validation_batch_size` | 유효성 검사 일괄 처리 크기입니다.<br> 양의 정수여야 합니다. | 다중 클래스/다중 레이블: 78 <br>*(vit-variant 제외:* <br> `vits16r224`: 128 <br>`vitb16r224`: 48 <br>`vitl16r224`:10)<br><br>개체 검색: 1 <br>`yolov5`(제외: 16) <br><br> 인스턴스 구분: 1  <br> <br> *참고: 기본값은 12 GiB GPU 메모리 에서 사용할 수 있는 가장 큰 일괄 처리 크기입니다.*|
| `grad_accumulation_step` | 그라데이션 누적은 `grad_accumulation_step` 해당 단계의 그라데이션을 누적하는 동안 모델 가중치를 업데이트하지 않고 구성된 개수를 실행한 다음 누적 그라데이션을 사용하여 가중치 업데이트를 계산하는 것을 의미합니다. <br> 양의 정수여야 합니다. | 1 |
| `early_stopping` | 학습 중에 조기 중지 논리를 사용하도록 설정합니다. <br> 0 또는 1이어야 합니다.| 1 |
| `early_stopping_patience` | 을(를) 통해 최소 Epoch 수 또는 유효성 검사 평가<br>실행이 중지되기 전에 기본 메트릭 개선이 없습니다.<br> 양의 정수여야 합니다. | 5 |
| `early_stopping_delay` | 대기할 Epoch 또는 유효성 검사 평가의 최소 수<br>이전의 기본 메트릭 개선은 조기 중지를 위해 추적됩니다.<br> 양의 정수여야 합니다. | 5 |
| `learning_rate` | 초기 학습 속도. <br>[0, 1] 범위의 float여야 합니다. | 다중 클래스: 0.01 <br>*(vit-variant 제외:* <br> `vits16r224`: 0.0125<br>`vitb16r224`: 0.0125<br>`vitl16r224`: 0.001) <br><br> 다중 레이블: 0.035 <br>*(vit-variant 제외:*<br>`vits16r224`: 0.025<br>`vitb16r224`: 0.025 <br>`vitl16r224`: 0.002) <br><br> 개체 검색: 0.005 <br>`yolov5`(제외: 0.01) <br><br> 인스턴스 구분: 0.005  |
| `lr_scheduler` | 학습 속도 스케줄러의 유형입니다. <br> `warmup_cosine` 또는 `step`이어야 합니다. | `warmup_cosine` |
| `step_lr_gamma` | 학습 속도 스케줄러가 인 경우 감마 `step` 값입니다.<br> [0, 1] 범위의 float여야 합니다. | 0.5 |
| `step_lr_step_size` | 학습 속도 스케줄러가 인 경우 단계 크기의 `step` 값입니다.<br> 양의 정수여야 합니다. | 5 |
| `warmup_cosine_lr_cycles` | 학습 속도 스케줄러가 인 경우 코사인 주기의 `warmup_cosine` 값입니다. <br> [0, 1] 범위의 float여야 합니다. | 0.45 |
| `warmup_cosine_lr_warmup_epochs` | 학습 속도 스케줄러가 인 경우 준비 Epoch의 `warmup_cosine` 값입니다. <br> 양의 정수여야 합니다. | 2 |
| `optimizer` | 최적화 프로그램의 유형입니다. <br> , , 중 하나여야 `sgd` `adam` `adamw` 합니다.  | `sgd` |
| `momentum` | 최적화 프로그램의 가 인 경우의 추진력 `sgd` 값입니다. <br> [0, 1] 범위의 float여야 합니다. | 0.9 |
| `weight_decay` | 최적화 프로그램의 값이 , 또는 인 경우 가중치 감소 `sgd` `adam` `adamw` 값입니다. <br> [0, 1] 범위의 float여야 합니다. | 1e-4 |
|`nesterov`| 최적화 도구가 인 경우 를 사용하도록 `nesterov` `sgd` 설정합니다. <br> 0 또는 1이어야 합니다.| 1 |
|`beta1` | 최적화 `beta1` 프로그램의 값이 또는 인 경우 `adam` `adamw` <br> [0, 1] 범위의 float여야 합니다. | 0.9 |
|`beta2` | 최적화 `beta2` 프로그램의 값이 또는 인 경우 `adam` `adamw`<br> [0, 1] 범위의 float여야 합니다. | 0.999 |
|`amsgrad` | 최적화 도구가 또는 인 경우 를 사용하도록 `amsgrad` `adam` `adamw` 설정합니다.<br> 0 또는 1이어야 합니다. | 0 |
|`evaluation_frequency`| 메트릭 점수를 얻기 위해 유효성 검사 데이터 세트를 평가하는 빈도입니다. <br> 양의 정수여야 합니다. | 1 |
|`split_ratio`| 유효성 검사 데이터가 정의되지 않은 경우 학습 데이터를 임의 학습 및 유효성 검사 하위 집합으로 분할하기 위한 분할 비율을 지정합니다. <br> [0, 1] 범위의 float여야 합니다.| 0.2 |
|`checkpoint_frequency`| 모델 검사점을 저장하는 빈도입니다. <br> 양의 정수여야 합니다. | 유효성 검사에 대한 최상의 기본 메트릭이 있는 Epoch의 검사점입니다.|
|`layers_to_freeze`| 모델에 대해 고정할 계층 수입니다. 예를 들어 값으로 2를 `seresnext` 전달하면 아래 지원되는 모델 계층 정보를 참조하는 layer0 및 layer1이 고정됩니다. <br> 양의 정수여야 합니다. <br><br>`'resnet': [('conv1.', 'bn1.'), 'layer1.', 'layer2.', 'layer3.', 'layer4.'],`<br>`'mobilenetv2': ['features.0.', 'features.1.', 'features.2.', 'features.3.', 'features.4.', 'features.5.', 'features.6.', 'features.7.', 'features.8.', 'features.9.', 'features.10.', 'features.11.', 'features.12.', 'features.13.', 'features.14.', 'features.15.', 'features.16.', 'features.17.', 'features.18.'],`<br>`'seresnext': ['layer0.', 'layer1.', 'layer2.', 'layer3.', 'layer4.'],`<br>`'vit': ['patch_embed', 'blocks.0.', 'blocks.1.', 'blocks.2.', 'blocks.3.', 'blocks.4.', 'blocks.5.', 'blocks.6.','blocks.7.', 'blocks.8.', 'blocks.9.', 'blocks.10.', 'blocks.11.'],`<br>`'yolov5_backbone': ['model.0.', 'model.1.', 'model.2.', 'model.3.', 'model.4.','model.5.', 'model.6.', 'model.7.', 'model.8.', 'model.9.'],`<br>`'resnet_backbone': ['backbone.body.conv1.', 'backbone.body.layer1.', 'backbone.body.layer2.','backbone.body.layer3.', 'backbone.body.layer4.']` | 기본값 없음  |


### <a name="task-specific-hyperparameters"></a>작업별 하이퍼parameters

다음 표에는 이미지 분류(다중 클래스 및 다중 레이블) 작업에 대한 하이퍼 부모 분석이 요약되어 있습니다.


| 매개 변수 이름       | Description           | 기본값  |
| ------------- |-------------|-----|
| `weighted_loss` | 가중치가 없는 손실의 경우 0입니다.<br>sqrt를 통해 가중치가 있는 손실의 경우 1입니다. (class_weights) <br> class_weights 가중치가 있는 손실의 경우 2입니다. <br> 0 또는 1 또는 2여야 합니다. | 0 |
| `valid_resize_size` | 유효성 검사 데이터 세트를 자르기 전에 크기를 조정하는 이미지 크기입니다. <br> 양의 정수여야 합니다. <br> <br> *참고: <li> `seresnext` 은 임의의 크기를 고려하지 않습니다. <li>크기가 너무 크면 학습 실행이 CUDA OOM에 포함될 수 있습니다.*  | 256  |
| `valid_crop_size` | 유효성 검사 데이터 세트를 위해 신경망에 입력되는 이미지 자르기 크기입니다.  <br> 양의 정수여야 합니다. <br> <br> *참고: <li> `seresnext` 은 임의의 크기를 고려하지 않습니다. <li> *ViT 변형은* 및 가 동일해야 `valid_crop_size` `train_crop_size` 합니다. <li> 크기가 너무 크면 학습 실행이 CUDA OOM에 포함될 수 있습니다.* | 224 |
| `train_crop_size` | 학습 데이터 세트를 위해 신경망에 입력되는 이미지 자르기 크기입니다.  <br> 양의 정수여야 합니다. <br> <br> *참고: <li> `seresnext` 은 임의의 크기를 고려하지 않습니다. <li> *ViT 변형은* 및 가 동일해야 `valid_crop_size` `train_crop_size` 합니다. <li> 크기가 너무 크면 학습 실행이 CUDA OOM에 포함될 수 있습니다.* | 224 |


다음 하이퍼매개는 개체 검색 및 인스턴스 구분 작업에 대한 것입니다.

> [!Warning]
> 이러한 매개 변수는 알고리즘에서 지원되지 `yolov5` 않습니다.

| 매개 변수 이름       | Description           | 기본값  |
| ------------- |-------------|-----|
| `validation_metric_type` | 유효성 검사 메트릭에 사용할 메트릭 계산 방법입니다.  <br> , , 또는 여야 `none` `coco` `voc` `coco_voc` 합니다. | `voc` |
| `min_size` | 백본에 공급하기 전에 크기가 조정될 이미지의 최소 크기입니다. <br> 양의 정수여야 합니다. <br> <br> *참고: 크기가 너무 크면 학습 실행이 CUDA OOM에 포함될 수 있습니다.*| 600 |
| `max_size` | 백본에 공급하기 전에 크기가 조정될 이미지의 최대 크기입니다. <br> 양의 정수여야 합니다.<br> <br> *참고: 크기가 너무 크면 학습 실행이 CUDA OOM에 포함될 수 있습니다.* | 1333 |
| `box_score_thresh` | 유추 중에는 분류 점수가 보다 큰 제안만 `box_score_thresh` 반환합니다. <br> [0, 1] 범위의 float여야 합니다.| 0.3 |
| `box_nms_thresh` | 예측 헤드에 대한 NMS(Non-Maximum Suppression) 임계값입니다. 유추 중에 사용됩니다.  <br>[0, 1] 범위의 float여야 합니다. | 0.5 |
| `box_detections_per_img` | 모든 클래스에 대해 이미지당 최대 검색 수입니다. <br> 양의 정수여야 합니다.| 100 |
| `tile_grid_size` | 각 이미지를 바둑판식으로 배열하는 데 사용할 그리드 크기입니다. <br>*참고: [작은 개체 검색](how-to-use-automl-small-object-detect.md) 논리를 사용하려면 tile_grid_size None이 아니어야 합니다.*<br> 문자열로 전달된 두 정수의 튜플입니다. 예: --tile_grid_size "(3, 2)" | 기본값 없음 |
| `tile_overlap_ratio` | 각 차원의 인접 타일 간에 겹치는 비율입니다. <br> [0, 1) 범위의 float여야 합니다. | 0.25 |
| `tile_predictions_nms_thresh` | 타일 및 이미지에서 예측을 병합하는 동안 NMS를 수행하는 데 사용할 IOU 임계값입니다. 유효성 검사/유추에 사용됩니다. <br> [0, 1] 범위의 float여야 합니다. | 0.25 |

### <a name="model-specific-hyperparameters"></a>모델별 하이퍼 부모

이 표에는 알고리즘과 관련한 하이퍼 변수가 `yolov5` 요약됩니다.

| 매개 변수 이름       | Description           | 기본값  |
| ------------- |-------------|----|
| `validation_metric_type` | 유효성 검사 메트릭에 사용할 메트릭 계산 방법입니다.  <br> , , 또는 여야 `none` `coco` `voc` `coco_voc` 합니다. | `voc` |
| `img_size` | 학습 및 유효성 검사를 위한 이미지 크기입니다. <br> 양의 정수여야 합니다. <br> <br> *참고: 크기가 너무 크면 학습 실행이 CUDA OOM에 포함될 수 있습니다.* | 640 |
| `model_size` | 모델 크기입니다. <br> , , 또는 여야 `small` `medium` `large` `xlarge` 합니다. <br><br> *참고: 모델 크기가 너무 크면 학습 실행이 CUDA OOM에 포함될 수 있습니다.*  | `medium` |
| `multi_scale` | 이미지 크기를 +/- 50%까지 변경하여 다중 크기 이미지를 사용하도록 설정 <br> 0 또는 1이어야 합니다. <br> <br> *참고: GPU 메모리가 충분하지 않으면 학습 실행이 CUDA OOM에 포함될 수 있습니다.* | 0 |
| `box_score_thresh` | 유추 중에는 보다 큰 점수를 가진 제안만 `box_score_thresh` 반환합니다. 점수는 개체 점수와 분류 확률을 곱한 것입니다. <br> [0, 1] 범위의 float여야 합니다. | 0.1 |
| `box_iou_thresh` | 최대값이 아닌 비표시 제거 후 처리에서 유추하는 동안 사용되는 IoU 임계값입니다. <br> [0, 1] 범위의 float여야 합니다. | 0.5 |


### <a name="data-augmentation"></a>데이터 보강 

일반적으로 딥 러닝 모델 성능은 더 많은 데이터로 향상될 수 있습니다. 데이터 보강은 과잉 맞춤을 방지하고 보이지 않는 데이터에 대한 모델의 일반화 기능을 개선하는 데 도움이 되는 데이터 세트의 데이터 크기와 가변성을 강화하는 실용적인 기술입니다. 자동화된 ML 입력 이미지를 모델에 공급하기 전에 컴퓨터 비전 작업에 따라 다양한 데이터 보강 기술을 적용합니다. 현재는 데이터 보강을 제어하는 노출된 하이퍼 부모(hyperparameter)가 없습니다. 

|작업 | 영향을 받은 데이터 세트 | 데이터 보강 기술이 적용되었습니다. |
|-------|----------|---------|
|이미지 분류(다중 클래스 및 다중 레이블) | 학습 <br><br><br> 유효성 검사 & 테스트| 임의 크기 조정 및 자르기, 가로 대칭 이동, 색 지터 (밝기, 대비, 채도 및 색상), 채널 기반 ImageNet의 평균 및 표준 편차를 사용 하는 정규화 <br><br><br>크기 조정, 가운데 자르기, 정규화 |
|개체 검색, 인스턴스 구분| 학습 <br><br> 유효성 검사 & 테스트 |경계 상자 주위의 임의 자르기, 확장, 수평 대칭 이동, 정규화, 크기 조정 <br><br><br>정규화, 크기 조정
|Yolov5를 사용 하 여 개체 검색| 학습 <br><br> 유효성 검사 & 테스트  |모자이크, 임의 상관 관계 (회전, 변환, 배율, 기울이기), 수평 대칭 이동 <br><br><br> 레터 박스 크기 조정|

## <a name="configure-your-experiment-settings"></a>실험 설정 구성

최적의 모델 및 하이퍼 매개 변수를 검색 하기 위해 대규모 스윕을 수행 하기 전에 기본값을 사용해 서 첫 번째 기준선을 가져오는 것이 좋습니다. 다음으로 여러 모델 및 해당 매개 변수를 스윕 하기 전에 동일한 모델에 대 한 하이퍼 매개 변수를 여러 개 탐색할 수 있습니다. 이렇게 하면 여러 모델과 여러 하이퍼 매개 변수를 각각 사용 하는 경우 검색 공간이 상당히 늘어나고 최적의 구성을 찾기 위해 더 많은 반복이 필요 하기 때문에 보다 반복적인 접근 방식을 사용할 수 있습니다.

지정 된 알고리즘 (예: yolov5)에 대 한 기본 하이퍼 매개 변수 값을 사용 하려는 경우 AutoML 이미지 실행에 대 한 구성을 다음과 같이 지정할 수 있습니다.

```python
from azureml.train.automl import AutoMLImageConfig
from azureml.train.hyperdrive import GridParameterSampling, choice
from azureml.automl.core.shared.constants import ImageTask

automl_image_config_yolov5 = AutoMLImageConfig(task=ImageTask.IMAGE_OBJECT_DETECTION,
                                               compute_target=compute_target,
                                               training_data=training_dataset,
                                               validation_data=validation_dataset,
                                               hyperparameter_sampling=GridParameterSampling({'model_name': choice('yolov5')}),
                                               iterations=1)
```

기준 모델을 작성 한 후에는 모델 알고리즘과 하이퍼 매개 변수 공간을 비우기 위해 모델 성능을 최적화할 수 있습니다. 다음 샘플 구성을 사용 하 여 각 알고리즘에 대 한 하이퍼 매개 변수를 스윕 하 고 learning_rate, 최적화 프로그램, lr_scheduler 등의 값 범위에서 선택 하 여 최적의 기본 메트릭으로 모델을 생성할 수 있습니다. 하이퍼 매개 변수 값을 지정 하지 않으면 지정 된 알고리즘에 기본값이 사용 됩니다.

### <a name="primary-metric"></a>기본 메트릭

모델 최적화 및 하이퍼 매개 변수 튜닝에 사용 되는 기본 메트릭은 작업 유형에 따라 달라 집니다. 현재 다른 기본 메트릭 값 사용은 지원 되지 않습니다. 

* `accuracy` IMAGE_CLASSIFICATION
* `iou` IMAGE_CLASSIFICATION_MULTILABEL
* `mean_average_precision` IMAGE_OBJECT_DETECTION
* `mean_average_precision` IMAGE_INSTANCE_SEGMENTATION
    
### <a name="experiment-budget"></a>실험 예산

필요에 따라 `experiment_timeout_hours` 실험을 종료 하기 전 까지의 시간을 사용 하 여 AutoML 비전 실험의 최대 시간 예산을 지정할 수 있습니다. 지정 하지 않으면 기본 실험 제한 시간은 7 일 (최대 60 일)입니다.

## <a name="sweeping-hyperparameters-for-your-model"></a>모델에 대 한 하이퍼 매개 변수 스윕

컴퓨터 비전 모델을 학습할 때 모델 성능은 선택 된 하이퍼 매개 변수 값에 따라 크게 달라 집니다. 최상의 성능을 얻기 위해 하이퍼 매개 변수를 튜닝할 수 있는 경우가 많습니다.
자동화 된 ML에서 컴퓨터 비전 작업을 지원 하므로 하이퍼 매개 변수를 스윕 하 여 모델에 대 한 최적의 설정을 찾을 수 있습니다. 이 기능은 Azure Machine Learning에서 하이퍼 매개 변수 튜닝 기능을 적용 합니다. 하이퍼 [매개 변수를 조정 하는 방법을 알아봅니다](how-to-tune-hyperparameters.md).

### <a name="define-the-parameter-search-space"></a>매개 변수 검색 공간 정의

매개 변수 공간에서 스윕 할 모델 알고리즘과 하이퍼 매개 변수를 정의할 수 있습니다. 지원 되는 모델 알고리즘과 각 작업 유형에 대 한 하이퍼 매개 변수 목록은 [모델 알고리즘 및 하이퍼 매개 변수 구성](#configure-model-algorithms-and-hyperparameters) 을 참조 하세요. [불연속 및 연속 하이퍼 매개 변수에 대해 지원 되는 배포에 대 한 세부 정보](how-to-tune-hyperparameters.md#define-the-search-space)를 참조 하세요.


### <a name="sampling-methods-for-the-sweep"></a>스윕에 대 한 샘플링 방법

하이퍼 매개 변수를 사용할 때 정의 된 매개 변수 공간을 사용 하 여 스윕에 사용할 샘플링 방법을 지정 해야 합니다. 현재 매개 변수를 사용 하 여 지원 되는 샘플링 메서드는 다음과 `hyperparameter_sampling` 같습니다.

* [무작위 샘플링](how-to-tune-hyperparameters.md#random-sampling)
* [그리드 샘플링](how-to-tune-hyperparameters.md#grid-sampling) 
* [Bayesian 샘플링](how-to-tune-hyperparameters.md#bayesian-sampling) 
    
현재 무작위 샘플링만 조건부 하이퍼 매개 변수 변수 공간을 지원함을 유의 해야 합니다.


### <a name="early-termination-policies"></a>조기 종료 정책

초기 종료 정책으로 잘못 실행 되는 실행을 자동으로 끝낼 수 있습니다. 초기 종료는 계산 효율성을 개선 하 고, 다른 방식으로 유망한 구성에 소요 된 계산 리소스를 저장 합니다. 이미지에 대 한 자동화 된 ML는 매개 변수를 사용 하 여 다음과 같은 초기 종료 정책을 지원 합니다 `early_termination_policy` . 종료 정책을 지정 하지 않으면 모든 구성이 완료 될 때까지 실행 됩니다.

* [산적 정책](how-to-tune-hyperparameters.md#bandit-policy)
* [중앙값 중지 정책](how-to-tune-hyperparameters.md#median-stopping-policy)
* [잘림 선택 영역 정책](how-to-tune-hyperparameters.md#truncation-selection-policy)

하이퍼 [매개 변수 스윕에 대 한 조기 종료 정책을 구성 하는 방법](how-to-tune-hyperparameters.md#early-termination)에 대해 자세히 알아보세요.

### <a name="resources-for-the-sweep"></a>스윕에 대 한 리소스

`iterations`스윕에 대해 및를 지정 하 여 하이퍼 매개 변수 스윕에 소요 되는 리소스를 제어할 수 있습니다 `max_concurrent_iterations` .

매개 변수 | 세부 정보
-----|----
`iterations` |  스윕 할 최대 구성 수에 대 한 필수 매개 변수입니다. 1~1000 사이의 정수여야 합니다. 지정 된 모델 알고리즘에 대 한 기본 하이퍼 매개 변수 변수만 탐색 하는 경우이 매개 변수를 1로 설정 합니다.
`max_concurrent_iterations`| 동시에 실행할 수 있는 최대 실행 수입니다. 지정하지 않으면 모든 실행이 병렬로 시작됩니다. 지정한 경우 1~100 사이의 정수여야 합니다.  <br><br> **참고:** 동시 실행 수는 지정 된 계산 대상에서 사용할 수 있는 리소스에 제어 됩니다. 원하는 동시성에 사용할 수 있는 리소스가 컴퓨팅 대상에 있는지 확인합니다.


> [!NOTE]
> 전체 스윕 구성 샘플은이 [자습서](tutorial-auto-train-image-models.md#hyperparameter-sweeping-for-image-tasks)를 참조 하세요.

### <a name="arguments"></a>인수

매개 변수 공간 스윕 중에 변경 되지 않는 고정 된 설정이 나 매개 변수를 인수로 전달할 수 있습니다. 인수는 이름-값 쌍으로 전달 되며 이름 앞에는 이중 대시가와 야 합니다. 

```python
from azureml.train.automl import AutoMLImageConfig
arguments = ["--early_stopping", 1, "--evaluation_frequency", 2]
automl_image_config = AutoMLImageConfig(arguments=arguments)
```

## <a name="submit-the-run"></a>실행 제출

`AutoMLImageConfig` 개체가 준비되면 실험을 제출할 수 있습니다.

```python
ws = Workspace.from_config()
experiment = Experiment(ws, "Tutorial-automl-image-object-detection")
automl_image_run = experiment.submit(automl_image_config)
```
## <a name="outputs-and-evaluation-metrics"></a>출력 및 평가 메트릭

Automl 학습 실행은 자식 실행의 출력 및 로그 및 메트릭 탭에서 볼 수 있는 점수 매기기 파일 및 환경 파일과 같은 출력 모델 파일, 평가 메트릭, 로그 및 배포 아티팩트를 생성 합니다.

> [!TIP]
> [실행 결과 보기](how-to-understand-automated-ml.md#view-run-results) 섹션에서 실행 결과를 탐색 하는 방법을 확인 합니다.

각 실행에 대해 제공 되는 성능 차트 및 메트릭의 정의와 예제는 자동화 된 [machine learning 실험 결과 평가](how-to-understand-automated-ml.md#metrics-for-image-models-preview) 를 참조 하세요.

## <a name="register-and-deploy-model"></a>모델 등록 및 배포

실행이 완료 되 면 최적 실행에서 만들어진 모델을 등록할 수 있습니다 (최상의 기본 메트릭을 생성 한 구성).

```Python
best_child_run = automl_image_run.get_best_child()
model_name = best_child_run.properties['model_name']
model = best_child_run.register_model(model_name = model_name, model_path='outputs/model.pt')
```

사용 하려는 모델을 등록 한 후에는 [ACI (Azure Container Instances](how-to-deploy-azure-container-instance.md) ) 또는 [AKS (Azure Kubernetes service)](how-to-deploy-azure-kubernetes-service.md)에서 웹 서비스로 배포할 수 있습니다. ACI는 배포를 테스트 하는 데 가장 적합 한 옵션 이지만 AKS는 대규모 프로덕션 사용에 더 적합 합니다.

이 예제에서는 AKS의 웹 서비스로 모델을 배포 합니다. AKS에서 배포 하려면 먼저 AKS 계산 클러스터를 만들거나 기존 AKS 클러스터를 사용 합니다. 배포 클러스터에 대해 GPU 또는 CPU VM Sku를 사용할 수 있습니다. 

```python

from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "cluster-aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="STANDARD_NC6", 
                                                        location="eastus2")
    # Create the cluster
    aks_target = ComputeTarget.create(workspace=ws, 
                                      name=aks_name, 
                                      provisioning_configuration=prov_config)
    aks_target.wait_for_completion(show_output=True)
```

그런 다음 모델을 포함 하는 웹 서비스를 설정 하는 방법을 설명 하는 유추 구성을 정의할 수 있습니다. 유추 구성에서 실행 되는 학습의 점수 매기기 스크립트 및 환경을 사용할 수 있습니다.

```python
from azureml.core.model import InferenceConfig

best_child_run.download_file('outputs/scoring_file_v_1_0_0.py', output_file_path='score.py')
environment = best_child_run.get_environment()
inference_config = InferenceConfig(entry_script='score.py', environment=environment)
```

그런 다음 AKS 웹 서비스로 모델을 배포할 수 있습니다.

```python
# Deploy the model from the best run as an AKS web service
from azureml.core.webservice import AksWebservice
from azureml.core.webservice import Webservice
from azureml.core.model import Model
from azureml.core.environment import Environment

aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True,                                                    
                                                cpu_cores=1,
                                                memory_gb=50,
                                                enable_app_insights=True)

aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           name='automl-image-test',
                           overwrite=True)
aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

또는 [Azure Machine Learning studio UI](https://ml.azure.com/)에서 모델을 배포할 수 있습니다. 자동화 된 ML 실행의 **모델** 탭에서 배포 하려는 모델로 이동 하 고 **배포** 를 선택 합니다.  

![Studio UI의 automl 실행에서 모델 선택  ](./media/how-to-auto-train-image-models/select-model.png)

**모델 배포 창에서** 모델 배포에 사용할 모델 배포 끝점 이름 및 추론 클러스터를 구성할 수 있습니다.

![구성 배포](./media/how-to-auto-train-image-models/deploy-image-model.png)

### <a name="update-inference-configuration"></a>업데이트 유추 구성

이전 단계에서는 최상의 모델에서 로컬 파일로 점수 매기기 파일을 다운로드 하 `outputs/scoring_file_v_1_0_0.py` `score.py` 고이를 사용 하 여 개체를 생성 했습니다 `InferenceConfig` . 이 스크립트는를 다운로드 한 후,를 만들기 전에 필요한 경우 모델 관련 유추 설정을 변경 하도록 수정할 수 있습니다 `InferenceConfig` . 예를 들어 다음은 점수 매기기 파일의 모델을 초기화 하는 코드 섹션입니다.
    
```
...
def init():
    ...
    try:
        logger.info("Loading model from path: {}.".format(model_path))
        model_settings = {...}
        model = load_model(TASK_TYPE, model_path, **model_settings)
        logger.info("Loading successful.")
    except Exception as e:
        logging_utilities.log_traceback(e, logger)
        raise
...
```

각 태스크 (및 일부 모델)에는 사전에 매개 변수 집합이 있습니다 `model_settings` . 기본적으로 학습 및 유효성 검사 중에 사용 된 매개 변수에 대해 동일한 값을 사용 합니다. 유추에 모델을 사용할 때 필요한 동작에 따라 이러한 매개 변수를 변경할 수 있습니다. 아래에서 각 작업 유형 및 모델에 대한 매개 변수 목록을 찾을 수 있습니다.  

| 작업 | 매개 변수 이름 | 기본값  |
|--------- |------------- | --------- |
|이미지 분류(다중 클래스 및 다중 레이블) | `valid_resize_size`<br>`valid_crop_size` | 256<br>224 |
|개체 검색, 인스턴스 구분| `min_size`<br>`max_size`<br>`box_score_thresh`<br>`box_nms_thresh`<br>`box_detections_per_img` | 600<br>1333<br>0.3<br>0.5<br>100 |
|를 사용하여 개체 검색 `yolov5`| `img_size`<br>`model_size`<br>`box_score_thresh`<br>`box_iou_thresh` | 640<br>중간<br>0.1<br>0.5 |

이러한 매개 변수에 대한 자세한 설명은 작업별 하이퍼 매개 변수 에 대한 위의 [섹션을 참조하세요.](#task-specific-hyperparameters)
    
바둑판식 배열을 사용하고 바둑판식 배열 동작을 제어하려는 경우 , 및 매개 변수를 사용할 수 `tile_grid_size` `tile_overlap_ratio` `tile_predictions_nms_thresh` 있습니다. 이러한 매개 변수에 대한 자세한 내용은 [AutoML을 사용하여 작은 개체 검색 모델 학습을](how-to-use-automl-small-object-detect.md)확인하세요.

## <a name="example-notebooks"></a>노트북 예제
[자동화된 기계 학습 샘플에 대한 GitHub Notebook 리포지토리](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml)에서 자세한 코드 예제 및 사용 사례를 검토합니다. 컴퓨터 비전 모델 빌드와 관련한 샘플은 'image-' 접두사로 폴더를 확인하세요.


## <a name="next-steps"></a>다음 단계

* [자습서: AutoML 및 Python을 사용하여 개체 검색 모델 학습(미리 보기)](tutorial-auto-train-image-models.md)
* [자동화된 ML 실험 문제 해결](how-to-troubleshoot-auto-ml.md)