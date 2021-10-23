---
title: AutoML 이미지에 대 한 ONNX를 사용 하는 로컬 유추
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 자동화 ML와 함께 onnx를 사용 하 여 분류, 개체 검색 및 구분에 대 한 컴퓨터 비전 모델에 대 한 예측을 만들 수 있습니다.
author: vadthyavath
ms.author: rvadthyavath
ms.service: machine-learning
ms.subservice: automl
ms.topic: how-to
ms.date: 10/18/2021
ms.openlocfilehash: 0a81248dc11e47b35c8649fdff19ab2fa570fa41
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273782"
---
# <a name="make-predictions-with-onnx-on-computer-vision-onnx-models-from-automl"></a>AutoML의 컴퓨터 비전 ONNX 모델에서 ONNX를 사용 하 여 예측 수행 

이 문서에서는 onnx를 사용 하 여 Azure Machine Learning의 자동화 된 ML에서 생성 된 컴퓨터 비전 onnx 모델에 대 한 예측을 만드는 방법에 대해 알아봅니다. 

ONNX를 사용 하 여 예측을 수행 하려면 다음을 수행 해야 합니다. 
 
1. 자동 ML 교육 실행에서 onnx 모델 파일 다운로드
1. ONNX 모델의 입/출력 이해
1. 입력 이미지에 필요한 형식이 되도록 데이터를 전처리 합니다.
1. Python 용 ONNX runtime을 사용 하 여 유추 수행
1. 개체 검색 및 조각화 작업에 대 한 예측 시각화

[onnx (open 신경망 Exchange)](https://onnx.ai/about.html) 는 인기 있는 AI 프레임 워크에서 모델 가져오기 및 내보내기 (상호 운용성)을 가능 하 게 하는 기계 학습 및 심층 학습 모델에 대 한 오픈 표준입니다. 자세한 내용은 [Onnx github 프로젝트](https://github.com/onnx/onnx)를 살펴보세요.

[Onnx Runtime (ORT)](https://onnxruntime.ai/index.html) 은 크로스 플랫폼 유추를 지 원하는 오픈 소스 프로젝트입니다. ONNX Runtime은 입력 이미지에 대 한 유추를 수행 하는 데 사용할 수 있는 다양 한 프로그래밍 언어 (Python, c + +, c #, c, JAVA, JS 등)에서 Api를 제공 합니다. 모델을 ONNX 형식으로 내보낸 후에는 프로젝트에 필요한 모든 프로그래밍 언어에서 이러한 Api를 사용할 수 있습니다. 이 가이드에서는 [Onnx Runtime](https://onnxruntime.ai/docs/get-started/with-python.html) 용 Python api를 사용 하 여 인기 있는 비전 작업을 위한 이미지에 대 한 예측을 수행 하는 방법에 대해 알아봅니다. 그러나 이러한 onnx에서 내보낸 모델을 여러 언어로 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* 지원 되는 이미지 작업 (분류, 개체 검색 또는 구분 작업)에 대 한 자동화 된 ML 학습 된 컴퓨터 비전 모델입니다. [자동 ML 컴퓨터 비전 작업 지원](how-to-auto-train-image-models.md) 에 대 한 자세한 정보

* [Onnxruntime](https://onnxruntime.ai/docs/get-started/with-python.html) 패키지를 설치 합니다.  이 문서의 메서드는 버전 1.3.0-1.8.0를 사용 하 여 테스트 되었습니다.

## <a name="download-onnx-model-files"></a>ONNX 모델 파일 다운로드

Azure Machine Learning studio UI 또는 Azure Machine Learning Python SDK를 사용 하 여 자동화 된 ML 실행에서 onnx 모델 파일을 다운로드할 수 있습니다. 실험 이름 및 부모 실행 ID를 사용 하 여 SDK를 통해 다운로드 하는 것이 좋습니다. 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio

Azure Machine Learning studio에서 실험으로 이동 하 여 (학습 노트북에서 생성 된 실험에 대 한 하이퍼링크를 사용 하거나 자산 아래 실험에서 실험의 이름을 선택 하 여) 최상의 자식 실행을 선택 합니다. 다음 그림에 표시 된 것 처럼 최상의 자식 실행 내에서로 이동 하 `outputs+logs`  ->  `train_artifacts` 여 `labels.json` (학습 데이터 집합의 모든 클래스나 레이블이 포함 된) 및 `model.onnx` (onnx 형식의 모델) 파일을 수동으로 다운로드 하 여 디렉터리에 저장 합니다 .이 문서에서는 다운로드 한 파일을 디렉터리에 저장 한다고 가정해 보겠습니다 `./automl_models` . 

![ONNX 모델 파일 다운로드](./media/how-to-inference-onnx-automl-image-models/onnx-files-manual-download.png)

### <a name="azure-machine-learning-python-sdk"></a>Azure Machine Learning Python SDK

SDK를 사용 하면 실험 이름 및 부모 실행 ID를 사용 하 여 가장 적합 한 자식 실행 (기본 메트릭 기준)을 선택할 수 있습니다. 그런 다음 및 파일을 다운로드할 수 있습니다 `labels.json` `model.onnx` .
다음 코드는 관련 된 기본 메트릭에 따라 가장 적합 한 자식 실행을 반환 합니다.

```python
# Select the best child run
from azureml.train.automl.run import AutoMLRun

run_id = "" # specify the run id
automl_image_run = AutoMLRun(experiment=experiment, run_id=run_id)
best_child_run = automl_image_run.get_best_child()
```

다운로드 `labels.json` 파일은 학습 데이터 집합의 모든 클래스/레이블로 구성 됩니다.

```python
import json

labels_file = "automl_models/labels.json"
best_child_run.download_file(name="train_artifacts/labels.json", output_file_path=labels_file)

```
파일 다운로드 `model.onnx`
```python
onnx_model_path = "automl_models/model.onnx"
best_child_run.download_file(name="train_artifacts/model.onnx", output_file_path=onnx_model_path)
```

모델 다운로드 단계 후에는 ONNX runtime python 패키지를 사용 하 여 파일을 사용 하 여 추론를 수행 `model.onnx` 합니다. 데모를 위해 각 비전 작업을 사용 하 여 [이미지 데이터 집합을 준비 하는 방법 문서](how-to-prepare-datasets-for-automl-images.md) 에서 사용한 것과 동일한 데이터 집합을 활용 합니다. 각 데이터 집합을 사용 하 여 모든 비전 작업을 위한 모델을 학습 하 여 ONNX 모델 유추를 설명 했습니다.
 
## <a name="load-the-labels-and-onnx-model-files"></a>레이블 및 ONNX 모델 파일 로드

다음 코드 조각에서 로드 `labels.json` 하는 경우 (예를 들어, ONNX 모델이 레이블 ID를 2로 예측 하는 경우 파일의 세 번째 인덱스에 지정 된 레이블 이름에 해당 `labels.json` )
```python
import onnxruntime

labels_file = "automl_models/labels.json"
with open(labels_file) as f:
    classes = json.load(f)
print(classes)
try:
    session = onnxruntime.InferenceSession(onnx_model_path)
    print("ONNX model loaded...")
except Exception as e: 
    print("Error loading ONNX file: ",str(e))
```

## <a name="get-expected-input-and-output-details-for-an-onnx-model"></a>ONNX 모델에 필요한 입력 및 출력 정보 가져오기

모델을 사용할 경우 모델 또는 작업별 출력을 알 수 있도록 입력 수, 출력 수, 이미지를 전처리 하는 데 필요한 입력 모양/형식, 출력 모양 등의 몇 가지 모델 및 태스크 관련 세부 정보를 알고 있어야 합니다.

```python
sess_input = session.get_inputs()
sess_output = session.get_outputs()
print(f"No. of inputs : {len(sess_input)}, No. of outputs : {len(sess_output)}")

for idx, input_ in enumerate(range(len(sess_input))):
    input_name = sess_input[input_].name
    input_shape = sess_input[input_].shape
    input_type = sess_input[input_].type
    print(f"{idx} Input name : { input_name }, Input shape : {input_shape}, \
    Input type  : {input_type}")  

for idx, output in enumerate(range(len(sess_output))):
    output_name = sess_output[output].name
    output_shape = sess_output[output].shape
    output_type = sess_output[output].type
    print(f" {idx} Output name : {output_name}, Output shape : {output_shape}, \
    Output type  : {output_type}") 
``` 

### <a name="expected-input-and-output-formats-for-onnx-model"></a>ONNX 모델에 대 한 입력 및 출력 형식이 필요 합니다.

모든 ONNX 모델에는 미리 정의 된 입력 및 출력 형식 집합이 있습니다.

# <a name="multi-class-image-classification"></a>[다중 클래스 이미지 분류 ](#tab/multi-class)

이 예제에서는 [fridgeObjects](https://cvbp-secondary.z19.web.core.windows.net/datasets/image_classification/fridgeObjects.zip) 데이터 집합에서 학습 된 모델을 134 이미지와 4 개의 클래스/레이블로 적용 하 여 onnx 모델 유추를 설명 합니다. 이미지 분류 작업을 학습 하는 방법에 대 한 자세한 내용은 [다중 클래스 이미지 분류 노트북](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-classification-multiclass)을 참조 하세요.

### <a name="input-format"></a>입력 형식
    
입력은 전처리 된 이미지입니다.

| 입력 이름  | 입력 셰이프  | 입력 형식 | 설명 |
| -------- |----------:|-----|--------|
| input1 | `(batch_size,num_channels, height, width)` | ndarray (float) | Input은 `(1, 3, 224, 224)` 일괄 처리 크기 1에 대 한 모양과 `crop_size` 학습 예제에서 사용 되는 값에 해당 하는 높이 및 너비 224의 전처리 된 이미지입니다. |
    

### <a name="output-format"></a>출력 형식

출력은 모든 레이블/클래스에 대 한 logits의 배열입니다.
         
| 출력 이름   | 출력 모양  | 출력 형식 | 설명 |
| -------- |----------|-----|------|
| output1 | `(batch_size, num_classes)` | ndarray (float) | 모델은 logits (소프트 최대값 없음)을 반환 합니다. 예를 들어 일괄 처리 크기 1 및 4 클래스의 경우 다음을 반환 합니다. `(1,4)` |

# <a name="multi-label-image-classification"></a>[다중 레이블 이미지 분류](#tab/multi-label)

이 예제에서는 ONNX 모델 유추를 설명 하는 128 이미지 및 4 개 클래스/레이블로 [다중 레이블 냉장고 objects 데이터 집합](https://cvbp-secondary.z19.web.core.windows.net/datasets/image_classification/multilabelFridgeObjects.zip) 에서 학습 된 모델을 사용 합니다. 이미지 분류 다중 레이블 모델 학습의 경우 [다중 레이블 이미지 분류 노트북](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-classification-multilabel)을 참조 하세요.

### <a name="input-format"></a>입력 형식

입력은 미리 처리 된 이미지입니다.

| 입력 이름       | 입력 셰이프  | 입력 형식 | 설명 |
| -------- |----------|-----|--------|
| input1 | (batch_size, num_channels, 높이, 너비) | ndarray (float) | Input은 일괄 처리 크기 1에 대해 (1, 3, 224, 224) 셰이프를 포함 하는 전처리 된 이미지 이며 학습 예제의 crop_size에 사용 되는 값에 해당 하는 height 및 width 224입니다. |
        
### <a name="output-format"></a>출력 형식:

출력은 모든 레이블/클래스에 대 한 logits의 배열입니다.
    
      
| 출력 이름       | 출력 모양  | 출력 형식 | 설명 |
| -------- |----------|-----|------
| output1 | (batch_size, num_classes) | ndarray (float) | 모델은 logits (시그모이드 없음)을 반환 합니다. 예를 들어 일괄 처리 크기 1 및 4 클래스의 경우 다음을 반환 합니다. `(1,4)` |


# <a name="object-detection-cnn"></a>[개체 검색 CNN](#tab/object-detect-cnn)

이 개체 검색 예제에서는 128 이미지의 [냉장고 개체 검색 데이터 집합](https://cvbp-secondary.z19.web.core.windows.net/datasets/object_detection/odFridgeObjects.zip) 에 대해 학습 된 모델을 사용 하 고, 클래스 또는 레이블 4 개를 사용 하 여 onnx 모델 유추를 설명 합니다. 이 예제에서는 유추 단계를 보여 주기 위해 더 빠른 R-CNN 모델을 학습 합니다. 학습 개체 검색 모델은 [개체 검색 노트북](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-object-detection)을 참조 하세요.

### <a name="input-format"></a>입력 형식

입력은 미리 처리 된 이미지입니다.

| 입력 이름       | 입력 셰이프  | 입력 형식 | Description |
| -------- |----------|-----|--------|
| 입력 | (batch_size, num_channels, 높이, 너비) | ndarray(float) | 입력은 일괄 처리 크기 1의 경우 셰이프(1, 3, 600, 800) 및 높이 600 및 너비 800을 사용하여 전처리된 이미지입니다.|
        
    
### <a name="output-format"></a>출력 형식

출력은 상자, 레이블 및 점수의 튜플입니다.
  
| 출력 이름       | 출력 셰이프  | 출력 형식 | 설명 |
| -------- |----------|-----|------|
| 박스 | `(n_boxes, 4)`- 각 상자가 있는 위치 `(x_min, y_min, x_max, y_max)` | ndarray(float) | 모델은 왼쪽 위와 오른쪽 아래 좌표가 있는 n개의 상자를 반환합니다. |
| 레이블 | `(n_boxes)`| ndarray(float) | 각 상자에 있는 개체의 레이블 또는 클래스 ID |  
| 점수 | `(n_boxes)` | ndarray(float) | 각 상자에 있는 개체의 신뢰도 점수 |    


# <a name="object-detection-with-yolo"></a>[YOLO를 통해 개체 검색](#tab/object-detect-yolo)

이 개체 검색 예제에서는 128개의 이미지와 4개의 클래스 또는 레이블로 된 [개체 감지 데이터 세트에](https://cvbp-secondary.z19.web.core.windows.net/datasets/object_detection/odFridgeObjects.zip) 대해 학습된 모델을 사용하여 ONNX 모델 유추를 설명합니다. 이 예제에서는 유추 단계를 보여 주도록 YOLO 모델을 훈련합니다. 학습 개체 검색 모델에 대한 자세한 내용은 개체 감지 Notebook 을 [참조하세요.](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-object-detection) 

### <a name="input-format"></a>입력 형식

입력은 일괄 처리 크기 1의 경우 셰이프(1, 3, 640, 640) 및 학습 예제에 사용된 값에 해당하는 높이 및 너비 640을 사용하는 전처리된 이미지입니다.        

| 입력 이름       | 입력 셰이프  | 입력 형식 | Description |
| -------- |----------|-----|--------|
| 입력 | (batch_size, num_channels, 높이, 너비) | ndarray(float) | 입력은 일괄 처리 크기 1의 경우 셰이프(1, 3, 600, 800) 및 높이 600 및 너비 800을 사용하여 전처리된 이미지입니다.|
        
### <a name="output-format"></a>출력 형식
출력은 상자, 레이블 및 점수 목록입니다. YOLO의 경우 상자, 레이블 및 점수를 추출하는 첫 번째 출력이 필요합니다.
    
| 출력 이름       | 출력 셰이프  | 출력 형식 | 설명 |
| -------- |----------|-----|------|
| 출력 | `(n_boxes, 6)`- 각 상자가 있는 위치 `(x_min, y_min, x_max, y_max, confidence_score, class_id)` | ndarray(float) | 모델은 개체 신뢰도 점수, 클래스 또는 레이블 ID와 함께 왼쪽 위 및 오른쪽 아래 좌표가 있는 n개의 상자를 반환합니다. |

# <a name="instance-segmentation"></a>[인스턴스 구분](#tab/instance-segmentation)

이 인스턴스 구분 예제에서는 이미지 128개와 클래스/레이블 4개와 함께 128개 개체 [데이터 세트 데이터 세트에](https://cvbp-secondary.z19.web.core.windows.net/datasets/object_detection/odFridgeObjectsMask.zip) 대해 학습된 Mask R-CNN 모델을 사용하여 ONNX 모델 유추를 설명합니다. 인스턴스 구분 모델 학습에 대한 자세한 내용은 인스턴스 구분 Notebook 을 [참조하세요.](https://github.com/Azure/azureml-examples/tree/81c7d33ed82f62f419472bc11f7e1bad448ff15b/python-sdk/tutorials/automl-with-azureml/image-instance-segmentation)

### <a name="input-format"></a>입력 형식

입력은 미리 처리된 이미지입니다. 마스크 R-CNN에 대한 ONNX 모델은 다양한 모양의 이미지에서 작동하도록 내보내졌지만, 성능을 향상하기 위해 학습 이미지 크기와 일치하도록 고정된 크기로 크기를 조정하는 것이 좋습니다.
    
| 입력 이름       | 입력 셰이프  | 입력 형식 | Description |
| -------- |----------|-----|--------|
| 입력 | (batch_size, num_channels, 높이, 너비) | ndarray(float) | 입력은 일괄 처리 크기 1의 모양(1, 3, input_image_height, input_image_width)과 입력 이미지와 비슷한 높이와 너비를 가진 전처리된 이미지입니다. |
        
    
### <a name="output-format"></a>출력 형식

출력은 상자(인스턴스), 레이블 및 점수의 튜플입니다.
    
| 출력 이름       | 출력 셰이프  | 출력 형식 | 설명 |
| -------- |----------|-----|------|
| 박스 | `(n_boxes, 4)`- 각 상자가 있는 위치 `(x_min, y_min, x_max, y_max)` | ndarray(float) | 모델은 왼쪽 위와 오른쪽 아래 좌표가 있는 n개의 상자를 반환합니다. |
| 레이블 | `(n_boxes)`| ndarray(float) | 각 상자에 있는 개체의 레이블 또는 클래스 ID |  
| 점수 | `(n_boxes)` | ndarray(float) | 각 상자에 있는 개체의 신뢰도 점수 |    
| 마스크 | `(n_boxes, 1, height, width)` | ndarray(float) | 입력 이미지의 셰이프 높이와 너비를 사용하여 검색된 개체의 마스크(다각형) |    

---

## <a name="preprocessing"></a>전처리

# <a name="multi-class-image-classification"></a>[다중 클래스 이미지 분류](#tab/multi-class)

ONNX 모델 유추에 필요한 다음 전처리 단계를 수행합니다.

1. 이미지를 RGB로 변환
2. 학습 `valid_resize_size` 중에 유효성 검사 `valid_resize_size` 데이터 세트의 변환에 사용되는 값에 해당하는 ( , )로 이미지 크기를 조정합니다. 의 `valid_resize_size` 기본값은 256입니다.
3. 가운데에서 이미지를 ( `height_onnx_crop_size` , `width_onnx_crop_size` )로 자입니다. 이 값은 `valid_crop_size` 에 해당하며 기본값은 224입니다.
4. HxWxC -> CxHxW
5. float 형식으로 변환
6. ImageNet의 `mean` = [0.485, 0.456, 0.406] 및 `std` = [0.229, 0.224, 0.225]로 정규화

[하이퍼 매개 변수](how-to-auto-train-image-models.md#configure-model-algorithms-and-hyperparameters) 및 학습 중에 다른 값을 선택한 경우 해당 값을 사용해야 `valid_resize_size` `valid_crop_size` 합니다.

ONNX 모델에 필요한 입력 셰이프를 가져오세요.

```python
batch, channel, height_onnx_crop_size, width_onnx_crop_size = session.get_inputs()[0].shape
batch, channel, height_onnx_crop_size, width_onnx_crop_size
```

### <a name="without-pytorch"></a>pytorch 없이

```python
def preprocess(image, resize_size, crop_size_onnx):
    """perform pre-processing on raw input image
  
    :param image: raw input image
    :type image: PIL image
    :param resize_size: value to resize the image
    :type image: Int
    :param crop_size_onnx: expected crop size of an input image in onnx model
    :type crop_size_onnx: Int
    :return: pre-processed image in numpy format
    :rtype: ndarray of shape 1xCxHxW
    """

    image = image.convert('RGB')
    # resize
    image = image.resize((resize_size, resize_size))

    # center crop
    left = (resize_size - crop_size_onnx)/2
    top = (resize_size - crop_size_onnx)/2
    right = (resize_size + crop_size_onnx)/2
    bottom = (resize_size + crop_size_onnx)/2
    image = image.crop((left, top, right, bottom))
    np_image = np.array(image)

    # HWC -> CHW
    np_image = np_image.transpose(2, 0, 1)# CxHxW

    # normalize the image
    mean_vec = np.array([0.485, 0.456, 0.406])
    std_vec = np.array([0.229, 0.224, 0.225])
    norm_img_data = np.zeros(np_image.shape).astype('float32')
    for i in range(np_image.shape[0]):
        norm_img_data[i,:,:] = (np_image[i,:,:]/255 - mean_vec[i]) / std_vec[i]
    np_image = np.expand_dims(norm_img_data, axis=0)# 1xCxHxW
    return np_image

from PIL import Image

# you can modify resize_size based on your trained model
resize_size = 256 

# height and width will be same for classification
crop_size_onnx = height_onnx_crop_size 
test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_size, crop_size_onnx)

```

### <a name="with-pytorch"></a>pytorch를 통해

```python
import torch
from torchvision import transforms
# tested with torch version: '1.7.1', torchvision version: '0.8.2')

def _make_3d_tensor(x) -> torch.Tensor:
    """This function is for images that have less channels.

    :param x: input tensor
    :type x: torch.Tensor
    :return: return a tensor with the correct number of channels
    :rtype: torch.Tensor
    """
    return x if x.shape[0] == 3 else x.expand((3, x.shape[1], x.shape[2]))

def preprocess(image, resize_size, crop_size_onnx):
    transform = transforms.Compose([
        transforms.Resize(resize_size),
        transforms.CenterCrop(crop_size_onnx),
        transforms.ToTensor(),
        transforms.Lambda(_make_3d_tensor),
        transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225])])
    
    img_data = transform(image)
    img_data = img_data.numpy()
    img_data = np.expand_dims(img_data, axis=0)
    return img_data

# you can modify resize_size based on your trained model
resize_size = 256

#height and width will be same for classification
test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_size, crop_size_onnx)
```

# <a name="multi-label-image-classification"></a>[다중 레이블 이미지 분류](#tab/multi-label)

ONNX 모델 유추에 필요한 다음 전처리 단계를 수행합니다.

1. 이미지를 RGB로 변환
2. 학습 중에 유효성 검사 데이터 세트의 변환에 사용되는 값에 해당하는 ( , )로 이미지 크기를 `valid_resize_size` 조정합니다(valid_resize_size `valid_resize_size` 기본값은 256).
3. 이미지를 가운데에 자르기(height_onnx_crop_size, width_onnx_crop_size). 이 값은 `valid_crop_size` 에 해당하며 기본값은 224입니다.
4. HxWxC -> CxHxW
5. float 형식으로 변환
6. ImageNet의 `mean` = [0.485, 0.456, 0.406] 및 `std` = [0.229, 0.224, 0.225]로 정규화

[하이퍼 매개 변수](how-to-auto-train-image-models.md#configure-model-algorithms-and-hyperparameters) 및 학습 중에 다른 값을 선택한 경우 `valid_resize_size` `valid_crop_size` 해당 값을 사용 해야 합니다.

ONNX 모델에 필요한 입력 셰이프를 가져옵니다.

```python
batch, channel, height_onnx_crop_size, width_onnx_crop_size = session.get_inputs()[0].shape
batch, channel, height_onnx_crop_size, width_onnx_crop_size
```

### <a name="without-pytorch"></a>PyTorch 없음

```python
def preprocess(image, resize_size, crop_size_onnx):
    """perform pre-processing on raw input image
  
    :param image: raw input image
    :type image: PIL image
    :param resize_size: value to resize the image
    :type image: Int
    :param crop_size_onnx: expected crop size of an input image in onnx model
    :type crop_size_onnx: Int
    :return: pre-processed image in numpy format
    :rtype: ndarray of shape 1xCxHxW
    """

    image = image.convert('RGB')
    # resize
    image = image.resize((resize_size, resize_size))

    # center crop
    left = (resize_size - crop_size_onnx)/2
    top = (resize_size - crop_size_onnx)/2
    right = (resize_size + crop_size_onnx)/2
    bottom = (resize_size + crop_size_onnx)/2
    image = image.crop((left, top, right, bottom))
    np_image = np.array(image)

    # HWC -> CHW
    np_image = np_image.transpose(2, 0, 1)# CxHxW

    # normalize the image
    mean_vec = np.array([0.485, 0.456, 0.406])
    std_vec = np.array([0.229, 0.224, 0.225])
    norm_img_data = np.zeros(np_image.shape).astype('float32')
    for i in range(np_image.shape[0]):
        norm_img_data[i,:,:] = (np_image[i,:,:]/255 - mean_vec[i]) / std_vec[i]
    np_image = np.expand_dims(norm_img_data, axis=0)# 1xCxHxW
    return np_image

from PIL import Image

# you can modify resize_size based on your trained model
resize_size = 256 

# height and width will be same for classification
crop_size_onnx = height_onnx_crop_size 
test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_size, crop_size_onnx)

```

### <a name="with-pytorch"></a>PyTorch 사용

```python
import torch
from torchvision import transforms
# tested with torch version: '1.7.1', torchvision version: '0.8.2')

def _make_3d_tensor(x) -> torch.Tensor:
    """This function is for images that have less channels.

    :param x: input tensor
    :type x: torch.Tensor
    :return: return a tensor with the correct number of channels
    :rtype: torch.Tensor
    """
    return x if x.shape[0] == 3 else x.expand((3, x.shape[1], x.shape[2]))

def preprocess(image, resize_size, crop_size_onnx):
    transform = transforms.Compose([
        transforms.Resize(resize_size),
        transforms.CenterCrop(crop_size_onnx),
        transforms.ToTensor(),
        transforms.Lambda(_make_3d_tensor),
        transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225])])
    
    img_data = transform(image)
    img_data = img_data.numpy()
    img_data = np.expand_dims(img_data, axis=0)
    return img_data

# you can modify resize_size based on your trained model
resize_size = 256

#height and width will be same for classification
crop_size_onnx = height_onnx_crop_size 
test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_size, crop_size_onnx)
```


# <a name="object-detection-faster-r-cnn"></a>[개체 검색 빨라진 R-CNN](#tab/object-detect-cnn)

Fast R CNN 알고리즘을 사용 하 여 개체를 검색 하려면 이미지 자르기를 제외 하 고 이미지 분류와 동일한 전처리 단계를 따르세요. 높이 및 너비를 사용 하 여 이미지 크기 `600` `800` 를 조정 하 고, 다음 코드를 사용 하 여 예상 입력 높이 및 너비를 가져올 수 있습니다.

```python
batch, channel, height_onnx, width_onnx = session.get_inputs()[0].shape
batch, channel, height_onnx, width_onnx
```

그런 다음 전처리 단계를 수행 합니다.

```python
def preprocess(image, height_onnx, width_onnx):
    """perform pre-processing on raw input image
    
    :param image: raw input image
    :type image: PIL image
    :param height_onnx: expected height of an input image in onnx model
    :type height_onnx: Int
    :param width_onnx: expected width of an input image in onnx model
    :type width_onnx: Int
    :return: pre-processed image in numpy format
    :rtype: ndarray of shape 1xCxHxW
    """

    image = image.convert('RGB')
    image = image.resize((width_onnx, height_onnx))
    np_image = np.array(image)
    # HWC -> CHW
    np_image = np_image.transpose(2, 0, 1)# CxHxW

    # Normalize the image
    mean_vec = np.array([0.485, 0.456, 0.406])
    std_vec = np.array([0.229, 0.224, 0.225])
    norm_img_data = np.zeros(np_image.shape).astype('float32')
    for i in range(np_image.shape[0]):
        norm_img_data[i,:,:] = (np_image[i,:,:]/255 - mean_vec[i]) / std_vec[i]
        
        
    np_image = np.expand_dims(norm_img_data, axis=0)# 1xCxHxW
    return np_image

from PIL import Image

test_image_path = "automl_models/test_image.jpg"
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, height_onnx, width_onnx)
```

# <a name="object-detection-with-yolo"></a>[YOLO를 사용 하 여 개체 검색](#tab/object-detect-yolo)

번째 YOLO 알고리즘을 사용 하 여 개체를 검색 하려면 이미지 자르기를 제외 하 고 이미지 분류와 동일한 전처리 단계를 따르세요. 높이 및 너비를 사용 하 여 이미지 크기 `600` `800` 를 조정 하 고, 다음 코드를 사용 하 여 예상 입력 높이 및 너비를 가져올 수 있습니다.

```python
batch, channel, height_onnx, width_onnx = session.get_inputs()[0].shape
batch, channel, height_onnx, width_onnx
```

YOLO에 필요한 전처리는 yolo_onnx_preprocessing_utils를 참조 [하세요. py](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml/image-object-detection)

```python
from yolo_onnx_preprocessing_utils import preprocess

test_image_path = "automl_models_od_yolo/test_image.jpg"
img_data, pad = preprocess(test_image_path)
```

# <a name="instance-segmentation"></a>[인스턴스 조각화](#tab/instance-segmentation)

ONNX 모델 유추에 필요한 다음과 같은 전처리 단계를 수행 합니다.

1. 이미지를 RGB로 변환
2. 이미지 크기 조정 
3. HxWxC-> CxHxW
4. Float 형식으로 변환
5. ImageNet의 `mean` = [0.485, 0.456, 0.406] 및 `std` = [0.229, 0.224, 0.225]로 정규화

의 `resize_height` 경우 `resize_width` `min_size` `max_size` Mask-rcnn에 대 한 및 [하이퍼 매개 변수](how-to-auto-train-image-models.md#configure-model-algorithms-and-hyperparameters) 를 통해 바인딩된 학습 중에 사용한 값을 사용할 수도 있습니다.

```python
def preprocess(image, resize_height, resize_width):
    """perform pre-processing on raw input image
        
    :param image: raw input image
    :type image: PIL image
    :param resize_height: resize height of an input image
    :type resize_height: Int
    :param resize_width: resize width of an input image
    :type resize_width: Int
    :return: pre-processed image in numpy format
    :rtype: ndarray of shape 1xCxHxW
    """

    image = image.convert('RGB')
    image = image.resize((resize_width,resize_height))
    np_image = np.array(image)

    # HWC -> CHW
    np_image = np_image.transpose(2, 0, 1)# CxHxW

    # normalize the image
    mean_vec = np.array([0.485, 0.456, 0.406])
    std_vec = np.array([0.229, 0.224, 0.225])
    norm_img_data = np.zeros(np_image.shape).astype('float32')
    for i in range(np_image.shape[0]):
        norm_img_data[i,:,:] = (np_image[i,:,:]/255 - mean_vec[i]) / std_vec[i]
    np_image = np.expand_dims(norm_img_data, axis=0)# 1xCxHxW
    return np_image

from PIL import Image
# use height and width based on trained model
resize_height, resize_width = 600, 800 
test_image_path = 'automl_models/test_image.jpg'
img = Image.open(test_image_path)
print("Input image dimensions: ", img.size)
display(img)
img_data = preprocess(img, resize_height, resize_width)

```

---

## <a name="inference-with-onnx-runtime"></a>ONNX Runtime을 사용한 유추

ONNX runtime을 사용 하는 추론는 각 컴퓨터 비전 작업 마다 다릅니다.

# <a name="multi-class-image-classification"></a>[다중 클래스 이미지 분류](#tab/multi-class)

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX runtime
    
    :param onnx_session: onnx inference session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: scores with shapes
            (1, No. of classes in training dataset) 
    :rtype: ndarray
    """
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    print(f"No. of inputs : {len(sess_input)}, No. of outputs : {len(sess_output)}")    
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    scores = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return scores[0]

scores = get_predictions_from_ONNX(session, img_data)
```

# <a name="multi-label-image-classification"></a>[다중 레이블 이미지 분류](#tab/multi-label)

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX runtime
    
    :param onnx_session: onnx inference session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: scores with shapes
            (1, No. of classes in training dataset) 
    :rtype: ndarray
    """
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    print(f"No. of inputs : {len(sess_input)}, No. of outputs : {len(sess_output)}")    
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    scores = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return scores[0]

scores = get_predictions_from_ONNX(session, img_data)
```

# <a name="object-detection-faster-r-cnn"></a>[개체 검색 빨라진 R-CNN](#tab/object-detect-cnn)

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX runtime
    
    :param onnx_session: onnx model session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: boxes, labels , scores 
            (No. of boxes, 4) (No. of boxes,) (No. of boxes,)
    :rtype: tuple
    """
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    boxes, labels, scores = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return boxes, labels, scores

boxes, labels, scores = get_predictions_from_ONNX(session, img_data)
```

# <a name="object-detection-with-yolo"></a>[YOLO를 사용 하 여 개체 검색](#tab/object-detect-yolo)

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX runtime
    
    :param onnx_session: onnx model session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: boxes, labels , scores 
    :rtype: list
    """
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    pred = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return pred[0]

result = get_predictions_from_ONNX(session, img_data)

```

# <a name="instance-segmentation"></a>[인스턴스 조각화](#tab/instance-segmentation)

인스턴스 조각화 모델은 상자, 레이블, 점수 및 마스크를 예측 합니다. ONNX는 해당 경계 상자와 클래스 신뢰도 점수와 함께 각 인스턴스당 예측 마스크를 출력 합니다. 필요한 경우 이진 마스크에서 다각형으로 변환 해야 할 수 있습니다.

```python
def get_predictions_from_ONNX(onnx_session,img_data):
    """perform predictions with ONNX runtime
    
    :param onnx_session: onnx model session
    :type onnx_session: class InferenceSession
    :param img_data: pre-processed numpy image
    :type img_data: ndarray with shape 1xCxHxW
    :return: boxes, labels , scores , masks with shapes
            (No. of instances, 4) (No. of instances,) (No. of instances,)
            (No. of instances, 1, HEIGHT, WIDTH))  
    :rtype: tuple
    """
    
    sess_input = onnx_session.get_inputs()
    sess_output = onnx_session.get_outputs()
    # predict with ONNX Runtime
    output_names = [ output.name for output in sess_output]
    boxes, labels, scores, masks = onnx_session.run(output_names=output_names,\
                                               input_feed={sess_input[0].name: img_data})
    return boxes, labels, scores, masks


boxes, labels, scores, masks = get_predictions_from_ONNX(session, img_data)
```

---

## <a name="postprocessing"></a>후 처리

# <a name="multi-class-image-classification"></a>[다중 클래스 이미지 분류](#tab/multi-class)

`softmax()`예측 값을 적용 하 여 각 클래스의 확률 (분류 신뢰도 점수)을 가져옵니다. 예측은 확률이 가장 높은 클래스가 됩니다. 

### <a name="without-pytorch"></a>PyTorch 없음

```python

def softmax(x):
    x = x.reshape(-1)
    e_x = np.exp(x - np.max(x))
    return e_x / e_x.sum(axis=0)
conf_scores = softmax(scores).tolist()
class_idx = np.argmax(conf_scores)
print("predicted class:",(class_idx, classes[class_idx]))

```

### <a name="with-pytorch"></a>PyTorch 사용

```python

conf_scores = torch.nn.functional.softmax(torch.from_numpy(scores), dim=1).tolist()[0]
class_idx = np.argmax(conf_scores)
print("predicted class:", (class_idx, classes[class_idx]))
```

# <a name="multi-label-image-classification"></a>[다중 레이블 이미지 분류](#tab/multi-label)

이 단계는 다중 클래스 분류와 다릅니다. 여러 레이블 이미지 분류에 대 한 신뢰도 점수를 얻으려면 logits (ONNX output)에 시그모이드를 적용 해야 합니다.

### <a name="without-pytorch"></a>PyTorch 없음

```python

import math

def sigmoid(x):
    return 1 / (1 + math.exp(-x))

# we apply a threshold of 0.5 on confidence scores
cutoff_threshold = .5
for class_idx, prob in enumerate([sigmoid(logit) for logit in scores[0]]):
    if prob > cutoff_threshold:
        print("predicted class:", (class_idx, classes[class_idx]))
```

### <a name="with-pytorch"></a>PyTorch 사용

```python

# we apply a threshold of 0.5 on confidence scores
cutoff_threshold = .5
conf_scores = torch.sigmoid(torch.from_numpy(scores[0])).tolist()
for class_idx, prob in enumerate(conf_scores):
    if prob > cutoff_threshold:
        print("predicted class:", (class_idx, classes[class_idx]))
```

다중 클래스 및 다중 레이블 분류의 경우 자동화 된 ML에서 지원 되는 모든 알고리즘에 대해 위에서 설명한 것과 동일한 단계를 따를 수 있습니다.


# <a name="object-detection-fast-r-cnn"></a>[개체 검색 Fast R CNN](#tab/object-detect-cnn)

```python
def _get_box_dims(image_shape, box):
    box_keys = ['topX', 'topY', 'bottomX', 'bottomY']
    height, width = image_shape[0], image_shape[1]

    box_dims = dict(zip(box_keys, [coordinate.item() for coordinate in box]))

    box_dims['topX'] = box_dims['topX'] * 1.0 / width
    box_dims['bottomX'] = box_dims['bottomX'] * 1.0 / width
    box_dims['topY'] = box_dims['topY'] * 1.0 / height
    box_dims['bottomY'] = box_dims['bottomY'] * 1.0 / height

    return box_dims

def _get_prediction(boxes, labels, scores, image_shape, classes):
    bounding_boxes = []
    for box, label_index, score in zip(boxes, labels, scores):
        box_dims = _get_box_dims(image_shape, box)

        box_record = {'box': box_dims,
                      'label': classes[label_index],
                      'score': score.item()}

        bounding_boxes.append(box_record)

    return bounding_boxes

bounding_boxes = _get_prediction(boxes, labels, scores, (height_onnx,width_onnx), classes)
print(json.dumps(bounding_boxes, indent=1))

# Filter the results with threshold.
# Please replace the threshold for your test scenario.
score_threshold = 0.8
filtered_bounding_boxes = []
for box in bounding_boxes:
    if box['score'] >= score_threshold:
        filtered_bounding_boxes.append(box)
```

### <a name="visualize-boxes"></a>상자 시각화

```python

import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image
%matplotlib inline

IMAGE_SIZE = (18,12)
plt.figure(figsize=IMAGE_SIZE)
img_np = mpimg.imread(test_image_path)
img = Image.fromarray(img_np.astype('uint8'),'RGB')
x, y = img.size
print(img.size)

fig,ax = plt.subplots(1)
# Display the image
ax.imshow(img_np)

# Draw box and label for each detection 
for detect in filtered_bounding_boxes:
    label = detect['label']
    box = detect['box']
    ymin, xmin, ymax, xmax =  box['topY'],box['topX'], box['bottomY'],box['bottomX']
    topleft_x, topleft_y = x * xmin, y * ymin
    width, height = x * (xmax - xmin), y * (ymax - ymin)
    print('{}: {}, {}, {}, {}'.format(detect['label'], topleft_x, topleft_y, width, height))
    rect = patches.Rectangle((topleft_x, topleft_y), width, height, 
                             linewidth=1, edgecolor='green',facecolor='none')

    ax.add_patch(rect)
    color = 'green'
    plt.text(topleft_x, topleft_y, label, color=color)

plt.show()
```

# <a name="object-detection-with-yolo"></a>[YOLO를 사용 하 여 개체 검색](#tab/object-detect-yolo)

다음 코드는 상자, 레이블 및 점수를 만듭니다. 이러한 경계 상자 세부 정보를 사용 하 여 더 빠른 R-CNN 모델에 대해 수행한 것과 동일한 후 처리 단계를 수행 합니다. 

```python
from yolo_onnx_preprocessing_utils import non_max_suppression, _convert_to_rcnn_output
import torch

result = non_max_suppression(
    torch.from_numpy(result),
    conf_thres=.1,
    iou_thres=.5)
label, image_shape = _convert_to_rcnn_output(result[0], height_onnx, width_onnx, pad)
boxes = np.array(label["boxes"])
labels = np.array(label["labels"])
labels = [label[0] for label in labels]
scores = np.array(label["scores"])
scores = [score[0] for score in scores]
boxes, labels, scores
```

### <a name="visualize-boxes"></a>상자 시각화

```python

import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image
%matplotlib inline

IMAGE_SIZE = (18,12)
plt.figure(figsize=IMAGE_SIZE)
img_np = mpimg.imread(test_image_path)
img = Image.fromarray(img_np.astype('uint8'),'RGB')
x, y = img.size
print(img.size)

fig,ax = plt.subplots(1)
# Display the image
ax.imshow(img_np)

# Draw box and label for each detection 
for detect in filtered_bounding_boxes:
    label = detect['label']
    box = detect['box']
    ymin, xmin, ymax, xmax =  box['topY'],box['topX'], box['bottomY'],box['bottomX']
    topleft_x, topleft_y = x * xmin, y * ymin
    width, height = x * (xmax - xmin), y * (ymax - ymin)
    print('{}: {}, {}, {}, {}'.format(detect['label'], topleft_x, topleft_y, width, height))
    rect = patches.Rectangle((topleft_x, topleft_y), width, height, 
                             linewidth=1, edgecolor='green',facecolor='none')

    ax.add_patch(rect)
    color = 'green'
    plt.text(topleft_x, topleft_y, label, color=color)

plt.show()
```


# <a name="instance-segmentation"></a>[인스턴스 조각화](#tab/instance-segmentation)

#### <a name="visualize-the-masks-and-bounding-boxes"></a>마스크 및 경계 상자 시각화

```python
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.patches as patches
import cv2
%matplotlib inline

def display_detections(image, boxes, labels, scores, masks, resize_height, 
                       resize_width, classes, score_threshold=0.3):
    """visualize boxes and masks
    
    :param image: raw image
    :type image: PIL image
    :param boxes: box with shape (No. of instances, 4) 
    :type boxes: ndarray 
    :param labels: classes with shape (No. of instances,) 
    :type labels: ndarray
    :param scores: scores with shape (No. of instances,)
    :type scores: ndarray
    :param masks: masks with shape (No. of instances, 1, HEIGHT, WIDTH) 
    :type masks:  ndarray
    :param resize_height: resize height of an input image
    :type resize_height: Int
    :param resize_width: resize width of an input image
    :type resize_width: Int
    :param classes: classes with shape (No. of classes) 
    :type classes:  list
    :param score_threshold: threshold on scores in the range of 0-1
    :type score_threshold: float
    :return: None     
   
    """

    _, ax = plt.subplots(1, figsize=(12,9))

    image = np.array(image)
    original_height = image.shape[0]
    original_width = image.shape[1]

    for mask, box, label, score in zip(masks, boxes, labels, scores):        
        if score <= score_threshold:
            continue
        mask = mask[0, :, :, None]        
        # resize boxes to original raw input size
        box = [box[0]*original_width/resize_width, 
               box[1]*original_height/resize_height, 
               box[2]*original_width/resize_width, 
               box[3]*original_height/resize_height]
        
        mask = cv2.resize(mask, (image.shape[1],image.shape[0]), 0, 0, interpolation = cv2.INTER_NEAREST)
        # mask is a matrix with values in the range of [0,1]
        # higher values indicate presence of object and vice versa
        # select threshold or cut-off value to get objects present               
        mask = mask > 0.5 
        image_masked = image.copy()
        image_masked[mask] = (0, 255, 255)
        alpha = .5 # alpha blending with range 0 to 1
        cv2.addWeighted(image_masked, alpha, image, 1 - alpha,0, image)        
        rect = patches.Rectangle((box[0], box[1]), box[2] - box[0], box[3] - box[1],\
                                 linewidth=1, edgecolor='b', facecolor='none')
        ax.annotate(classes[label] + ':' + str(np.round(score, 2)), (box[0], box[1]),\
                    color='w', fontsize=12)
        ax.add_patch(rect)
        

    ax.imshow(image)
    plt.show()

display_detections(img, boxes.copy(), labels, scores, masks.copy(), 
                   resize_height, resize_width, classes, score_threshold=.5)
```

---


## <a name="next-steps"></a>다음 단계
* [자동화 된 ML에서 컴퓨터 비전 작업에 대해 자세히 알아보세요](how-to-auto-train-image-models.md).
* [자동화된 ML 실험 문제 해결](how-to-troubleshoot-auto-ml.md) 

