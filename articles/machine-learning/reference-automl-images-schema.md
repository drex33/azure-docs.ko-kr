---
title: 컴퓨터 비전 작업의 JSONL 형식
titleSuffix: Azure Machine Learning
description: 컴퓨터 비전 작업에 대해 자동화 된 ML 실험에서 데이터를 사용 하기 위해 JSONL 파일의 형식을 지정 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.topic: reference
ms.reviewer: nibaccam
ms.author: rvadthyavath
author: vadthyavath
ms.date: 10/13/2021
ms.custom: ''
ms.openlocfilehash: 376ba24800b3d547a302d83eef960c58afd73054
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130008676"
---
# <a name="data-schemas-to-train-computer-vision-models-with-automated-machine-learning"></a>자동화 된 machine learning을 사용 하 여 컴퓨터 비전 모델을 학습 하는 데이터 스키마

학습 및 유추 중 컴퓨터 비전 작업에 대해 자동화 된 ML 실험에서 데이터를 사용 하기 위해 JSONL 파일의 형식을 지정 하는 방법에 대해 알아봅니다.


## <a name="data-schema-for-training"></a>학습을 위한 데이터 스키마 

Azure Machine Learning 이미지에 대 한 AutoML을 사용 하려면 입력 이미지 데이터가 [JSONL](https://jsonlines.org/) (JSON 줄) 형식으로 준비 되어야 합니다. 이 섹션에서는 이미지 분류 다중 클래스, 이미지 분류 다중 레이블, 개체 검색 및 인스턴스 조각화를 위한 입력 데이터 형식 또는 스키마에 대해 설명 합니다. 또한 최종 학습 또는 유효성 검사 JSON 줄 파일의 샘플을 제공 합니다.

### <a name="image-classification-binarymulti-class"></a>이미지 분류 (이진/다중 클래스)

**각 JSON 줄에서 입력 데이터 형식/스키마:**
```json
{
   "image_url":"AmlDatastore://data_directory/../Image_name.image_format",
   "image_details":{
      "format":"image_format",
      "width":"image_width",
      "height":"image_height"
   },
   "label":"class_name",
}
```

| 키       | Description  | 예제 |
| -------- |----------|-----|
| image_url | AML 데이터 저장소의 이미지 위치<br>`Required, String` | `"AmlDatastore://data_directory/Image_01.jpg"` |
| image_details | 이미지 세부 정보<br>`Optional, Dictionary` | `"image_details":{"format": "jpg", "width": "400px", "height": "258px"}` |
| format  | 이미지 유형 ( [Pillow](https://pillow.readthedocs.io/en/stable/releasenotes/8.0.1.html) library에서 사용 가능한 모든 이미지 형식이 지원 됨)<br>`Optional, String from {"jpg", "jpeg", "png", "jpe", "jfif","bmp", "tif", "tiff"}`  |  `"jpg" or "jpeg" or "png" or "jpe" or "jfif" or "bmp" or "tif" or "tiff"` |
| width | 이미지의 너비입니다.<br>`Optional, String or Positive Integer`  | `"400px" or 400`|
| 키 | 이미지의 높이입니다.<br>`Optional, String or Positive Integer` | `"200px" or 200` |
| label | 이미지의 클래스/레이블<br>`Required, String` | `"cat"` |


다중 클래스 이미지 분류에 대 한 JSONL 파일의 예:
```json
{"image_url": "AmlDatastore://image_data/Image_01.jpg", "image_details":{"format": "jpg", "width": "400px", "height": "258px"}, "label": "can"}
{"image_url": "AmlDatastore://image_data/Image_02.jpg", "image_details": {"format": "jpg", "width": "397px", "height": "296px"}, "label": "milk_bottle"}
.
.
.
{"image_url": "AmlDatastore://image_data/Image_n.jpg", "image_details": {"format": "jpg", "width": "1024px", "height": "768px"}, "label": "water_bottle"}
  ```

### <a name="image-classification-multi-label"></a>이미지 분류 다중 레이블

다음은 이미지 분류를 위한 각 JSON 줄의 입력 데이터 형식/스키마 예제입니다.


```json
{
   "image_url":"AmlDatastore://data_directory/../Image_name.image_format",
   "image_details":{
      "format":"image_format",
      "width":"image_width",
      "height":"image_height"
   },
   "label":[
      "class_name_1",
      "class_name_2",
      "class_name_3",
      "...",
      "class_name_n"
        
   ]
}
```

| 키       | Description  | 예제 |
| -------- |----------|-----|
| image_url | AML 데이터 저장소의 이미지 위치<br>`Required, String` | `"AmlDatastore://data_directory/Image_01.jpg"` |
| image_details | 이미지 세부 정보<br>`Optional, Dictionary` | `"image_details":{"format": "jpg", "width": "400px", "height": "258px"}` |
| format  | 이미지 유형 ( [Pillow](https://pillow.readthedocs.io/en/stable/releasenotes/8.0.1.html) library에서 사용할 수 있는 모든 이미지 형식이 지원 됨)<br>`Optional, String from {"jpg", "jpeg", "png", "jpe", "jfif", "bmp", "tif", "tiff"}`  |  `"jpg" or "jpeg" or "png" or "jpe" or "jfif" or "bmp" or "tif" or "tiff"` |
| width | 이미지의 너비입니다.<br>`Optional, String or Positive Integer`  | `"400px" or 400`|
| 키 | 이미지의 높이입니다.<br>`Optional, String or Positive Integer` | `"200px" or 200` |
| label | 이미지의 클래스/레이블 목록<br>`Required, List of Strings` | `["cat","dog"]` |


이미지 분류 다중 레이블에 대 한 JSONL 파일의 예:

```json
{"image_url": "AmlDatastore://image_data/Image_01.jpg", "image_details":{"format": "jpg", "width": "400px", "height": "258px"}, "label": ["can"]}
{"image_url": "AmlDatastore://image_data/Image_02.jpg", "image_details": {"format": "jpg", "width": "397px", "height": "296px"}, "label": ["can","milk_bottle"]}
.
.
.
{"image_url": "AmlDatastore://image_data/Image_n.jpg", "image_details": {"format": "jpg", "width": "1024px", "height": "768px"}, "label": ["carton","milk_bottle","water_bottle"]}
  ```

### <a name="object-detection"></a>개체 감지

다음은 개체 검색에 대 한 예제 JSONL 파일입니다.

```json
{
   "image_url":"AmlDatastore://data_directory/../Image_name.image_format",
   "image_details":{
      "format":"image_format",
      "width":"image_width",
      "height":"image_height"
   },
   "label":[
      {
         "label":"class_name_1",
         "topX":"xmin/width",
         "topY":"ymin/height",
         "bottomX":"xmax/width",
         "bottomY":"ymax/height",
         "isCrowd":"isCrowd"
      },
      {
         "label":"class_name_2",
         "topX":"xmin/width",
         "topY":"ymin/height",
         "bottomX":"xmax/width",
         "bottomY":"ymax/height",
         "isCrowd":"isCrowd"
      },
      "..."
   ]
}
```

여기서 각 부분의 의미는 다음과 같습니다. 
- xmin = x 경계 상자의 왼쪽 위 모퉁이의 좌표
- ymin = 경계 상자의 왼쪽 위 모퉁이에 대 한 y 좌표입니다.
- xmax = 경계 상자의 오른쪽 아래 모퉁이의 x 좌표입니다.
- ymax = 경계 상자의 오른쪽 아래 모퉁이의 y 좌표입니다.



| 키       | Description  | 예제 |
| -------- |----------|-----|
| image_url | AML 데이터 저장소의 이미지 위치<br>`Required, String` | `"AmlDatastore://data_directory/Image_01.jpg"` |
| image_details | 이미지 세부 정보<br>`Optional, Dictionary` | `"image_details":{"format": "jpg", "width": "400px", "height": "258px"}` |
| format  | 이미지 유형 ( [Pillow](https://pillow.readthedocs.io/en/stable/releasenotes/8.0.1.html) library에서 사용할 수 있는 모든 이미지 형식이 지원 됩니다. 그러나 YOLO의 경우 [opencv](https://pypi.org/project/opencv-python/4.3.0.36/) 에서 허용 하는 이미지 형식만 지원 됩니다.<br>`Optional, String from {"jpg", "jpeg", "png", "jpe", "jfif", "bmp", "tif", "tiff"}`  |  `"jpg" or "jpeg" or "png" or "jpe" or "jfif" or "bmp" or "tif" or "tiff"` |
| width | 이미지의 너비입니다.<br>`Optional, String or Positive Integer`  | `"499px" or 499`|
| 키 | 이미지의 높이입니다.<br>`Optional, String or Positive Integer` | `"665px" or 665` |
| 레이블 (외부 키) | 각 상자가 `label, topX, topY, bottomX, bottomY, isCrowd` 왼쪽 위 좌표와 오른쪽 아래 좌표에 대 한 사전 인 경계 상자 목록<br>`Required, List of dictionaries` | `[{"label": "cat", "topX": 0.260, "topY": 0.406, "bottomX": 0.735, "bottomY": 0.701, "isCrowd": 0}]` |
| 레이블 (내부 키)| 경계 상자에 있는 개체의 클래스/레이블<br>`Required, String` | `"cat"` |
| topX | 경계 상자와 이미지 너비의 왼쪽 위 모퉁이에 대 한 x 좌표의 비율<br>`Required, Float in the range [0,1]` | `0.260` |
| topY | 경계 상자와 이미지 높이의 왼쪽 위 모퉁이에 대 한 y 좌표의 비율입니다.<br>`Required, Float in the range [0,1]` | `0.406` |
| bottomX | 경계 상자와 이미지 너비의 오른쪽 아래 모퉁이의 x 좌표 비율<br>`Required, Float in the range [0,1]` | `0.735` |
| bottomY | 경계 상자와 이미지 높이의 오른쪽 아래 모퉁이에 대 한 y 좌표의 비율입니다.<br>`Required, Float in the range [0,1]` | `0.701` |
| isCrowd | 경계 상자가 개체의 주위에 있는지 여부를 나타냅니다. 이 특수 플래그를 설정 하면 메트릭을 계산할 때이 특정 경계 상자를 건너뜁니다.<br>`Optional, Bool` | `0` |


개체 검색에 대 한 JSONL 파일의 예:

```json
{"image_url": "AmlDatastore://image_data/Image_01.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "can", "topX": 0.260, "topY": 0.406, "bottomX": 0.735, "bottomY": 0.701, "isCrowd": 0}]}
{"image_url": "AmlDatastore://image_data/Image_02.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "carton", "topX": 0.172, "topY": 0.153, "bottomX": 0.432, "bottomY": 0.659, "isCrowd": 0}, {"label": "milk_bottle", "topX": 0.300, "topY": 0.566, "bottomX": 0.891, "bottomY": 0.735, "isCrowd": 0}]}
.
.
.
{"image_url": "AmlDatastore://image_data/Image_n.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "carton", "topX": 0.0180, "topY": 0.297, "bottomX": 0.380, "bottomY": 0.836, "isCrowd": 0}, {"label": "milk_bottle", "topX": 0.454, "topY": 0.348, "bottomX": 0.613, "bottomY": 0.683, "isCrowd": 0}, {"label": "water_bottle", "topX": 0.667, "topY": 0.279, "bottomX": 0.841, "bottomY": 0.615, "isCrowd": 0}]}
  ```

### <a name="instance-segmentation"></a>인스턴스 조각화

예를 들어, 자동화 된 ML는 다각형을 입력 및 출력으로 지원 하 고 마스크는 지원 하지 않습니다.

다음은 인스턴스 구분에 대 한 예제 JSONL 파일입니다.

```json
{
   "image_url":"AmlDatastore://data_directory/../Image_name.image_format",
   "image_details":{
      "format":"image_format",
      "width":"image_width",
      "height":"image_height"
   },
   "label":[
      {
         "label":"class_name",
         "isCrowd":"isCrowd",
         "polygon":[["x1", "y1", "x2", "y2", "x3", "y3", "...", "xn", "yn"]]
      }
   ]
}
```

| 키       | Description  | 예제 |
| -------- |----------|-----|
| image_url | AML 데이터 저장소의 이미지 위치<br>`Required, String` | `"AmlDatastore://data_directory/Image_01.jpg"` |
| image_details | 이미지 세부 정보<br>`Optional, Dictionary` | `"image_details":{"format": "jpg", "width": "400px", "height": "258px"}` |
| format  | 이미지 형식<br>`Optional, String from {"jpg", "jpeg", "png", "jpe", "jfif", "bmp", "tif", "tiff" }`  |  `"jpg" or "jpeg" or "png" or "jpe" or "jfif" or "bmp" or "tif" or "tiff"` |
| width | 이미지의 너비입니다.<br>`Optional, String or Positive Integer`  | `"499px" or 499`|
| 키 | 이미지의 높이입니다.<br>`Optional, String or Positive Integer` | `"665px" or 665` |
| 레이블 (외부 키) | 각 마스크가의 사전 인 마스크 목록 `label, isCrowd, polygon coordinates` <br>`Required, List of dictionaries` | ` [{"label": "can", "isCrowd": 0, "polygon": [[0.577, 0.689,`<br> ` 0.562, 0.681,`<br> `0.559, 0.686]]}]` |
| 레이블 (내부 키)| 마스크에 있는 개체의 클래스/레이블<br>`Required, String` | `"cat"` |
| isCrowd | 마스크가 개체의 주위에 있는지 여부를 나타냅니다.<br>`Optional, Bool` | `0` |
| polygon | 개체의 다각형 좌표<br>`Required,  List of list for multiple segments of the same instance. Float values in the range [0,1]` | ` [[0.577, 0.689, 0.567, 0.689, 0.559, 0.686]]` |


인스턴스 구분에 대 한 JSONL 파일의 예:

```python
{"image_url": "AmlDatastore://image_data/Image_01.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "can", "isCrowd": 0, "polygon": [[0.577, 0.689, 0.567, 0.689, 0.559, 0.686, 0.380, 0.593, 0.304, 0.555, 0.294, 0.545, 0.290, 0.534, 0.274, 0.512, 0.2705, 0.496, 0.270, 0.478, 0.284, 0.453, 0.308, 0.432, 0.326, 0.423, 0.356, 0.415, 0.418, 0.417, 0.635, 0.493, 0.683, 0.507, 0.701, 0.518, 0.709, 0.528, 0.713, 0.545, 0.719, 0.554, 0.719, 0.579, 0.713, 0.597, 0.697, 0.621, 0.695, 0.629, 0.631, 0.678, 0.619, 0.683, 0.595, 0.683, 0.577, 0.689]]}]}
{"image_url": "AmlDatastore://image_data/Image_02.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "carton", "isCrowd": 0, "polygon": [[0.240, 0.65, 0.234, 0.654, 0.230, 0.647, 0.210, 0.512, 0.202, 0.403, 0.182, 0.267, 0.184, 0.243, 0.180, 0.166, 0.186, 0.159, 0.198, 0.156, 0.396, 0.162, 0.408, 0.169, 0.406, 0.217, 0.414, 0.249, 0.422, 0.262, 0.422, 0.569, 0.342, 0.569, 0.334, 0.572, 0.320, 0.585, 0.308, 0.624, 0.306, 0.648, 0.240, 0.657]]}, {"label": "milk_bottle",  "isCrowd": 0, "polygon": [[0.675, 0.732, 0.635, 0.731, 0.621, 0.725, 0.573, 0.717, 0.516, 0.717, 0.505, 0.720, 0.462, 0.722, 0.438, 0.719, 0.396, 0.719, 0.358, 0.714, 0.334, 0.714, 0.322, 0.711, 0.312, 0.701, 0.306, 0.687, 0.304, 0.663, 0.308, 0.630, 0.320, 0.596, 0.32, 0.588, 0.326, 0.579]]}]}
.
.
.
{"image_url": "AmlDatastore://image_data/Image_n.jpg", "image_details": {"format": "jpg", "width": "499px", "height": "666px"}, "label": [{"label": "water_bottle", "isCrowd": 0, "polygon": [[0.334, 0.626, 0.304, 0.621, 0.254, 0.603, 0.164, 0.605, 0.158, 0.602, 0.146, 0.602, 0.142, 0.608, 0.094, 0.612, 0.084, 0.599, 0.080, 0.585, 0.080, 0.539, 0.082, 0.536, 0.092, 0.533, 0.126, 0.530, 0.132, 0.533, 0.144, 0.533, 0.162, 0.525, 0.172, 0.525, 0.186, 0.521, 0.196, 0.521 ]]}, {"label": "milk_bottle", "isCrowd": 0, "polygon": [[0.392, 0.773, 0.380, 0.732, 0.379, 0.767, 0.367, 0.755, 0.362, 0.735, 0.362, 0.714, 0.352, 0.644, 0.352, 0.611, 0.362, 0.597, 0.40, 0.593, 0.444,  0.494, 0.588, 0.515, 0.585, 0.621, 0.588, 0.671, 0.582, 0.713, 0.572, 0.753 ]]}]}
```

## <a name="data-format-for-inference"></a>유추를 위한 데이터 형식

이 섹션에서는 배포 된 모델을 사용 하는 경우 예측을 만드는 데 필요한 입력 데이터 형식을 설명 합니다. 앞에서 언급 한 이미지 형식은 내용 유형과 함께 수락 됩니다 `application/octet-stream` .

### <a name="input-format"></a>입력 형식

다음은 작업별 모델 끝점을 사용 하 여 작업에 대 한 예측을 생성 하는 데 필요한 입력 형식입니다. 모델을 [배포한](how-to-auto-train-image-models.md#register-and-deploy-model)후에는 다음 코드 조각을 사용 하 여 모든 작업에 대 한 예측을 가져올 수 있습니다.

```python
# input image for inference
sample_image = './test_image.jpg'
# load image data
data = open(sample_image, 'rb').read()
# set the content type
headers = {'Content-Type': 'application/octet-stream'}
# if authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'
# make the request and display the response
response = requests.post(scoring_uri, data, headers=headers)
```
### <a name="output-format"></a>출력 형식

모델 끝점에 대 한 예측은 작업 유형에 따라 서로 다른 구조를 따릅니다. 이 섹션에서는 다중 클래스, 다중 레이블 이미지 분류, 개체 검색 및 인스턴스 조각화 태스크에 대 한 출력 데이터 형식을 살펴봅니다.  

#### <a name="image-classification"></a>이미지 분류

이미지 분류의 끝점은 데이터 집합의 모든 레이블과 입력 이미지의 확률 점수를 다음 형식으로 반환 합니다.

```json
{
   "filename":"/tmp/tmppjr4et28",
   "probs":[
      2.098e-06,
      4.783e-08,
      0.999,
      8.637e-06
   ],
   "labels":[
      "can",
      "carton",
      "milk_bottle",
      "water_bottle"
   ]
}
```

#### <a name="image-classification-multi-label"></a>이미지 분류 다중 레이블

이미지 분류 다중 레이블의 경우 모델 끝점은 레이블 및 해당 확률을 반환 합니다.

```json
{
   "filename":"/tmp/tmpsdzxlmlm",
   "probs":[
      0.997,
      0.960,
      0.982,
      0.025
   ],
   "labels":[
      "can",
      "carton",
      "milk_bottle",
      "water_bottle"
   ]
}
```

#### <a name="object-detection"></a>개체 감지

개체 검색 모델은 상자 레이블 및 신뢰도 점수와 함께 배율이 지정 된 왼쪽 및 오른쪽 아래 좌표와 함께 여러 상자를 반환 합니다.

```json
{
   "filename":"/tmp/tmpdkg2wkdy",
   "boxes":[
      {
         "box":{
            "topX":0.224,
            "topY":0.285,
            "bottomX":0.399,
            "bottomY":0.620
         },
         "label":"milk_bottle",
         "score":0.937
      },
      {
         "box":{
            "topX":0.664,
            "topY":0.484,
            "bottomX":0.959,
            "bottomY":0.812
         },
         "label":"can",
         "score":0.891
      },
      {
         "box":{
            "topX":0.423,
            "topY":0.253,
            "bottomX":0.632,
            "bottomY":0.725
         },
         "label":"water_bottle",
         "score":0.876
      }
   ]
}
```
#### <a name="instance-segmentation"></a>인스턴스 조각화

인스턴스 구분에서 출력은 배율이 지정 된 왼쪽 위 좌표와 오른쪽 아래 좌표, 레이블, 신뢰도 점수 및 다각형이 있는 여러 상자로 구성 됩니다 (마스크 아님). 여기에서 polygon 값은 스키마 섹션에서 설명한 것과 동일한 형식입니다.

```json
{
   "filename":"/tmp/tmpi8604s0h",
   "boxes":[
      {
         "box":{
            "topX":0.679,
            "topY":0.491,
            "bottomX":0.926,
            "bottomY":0.810
         },
         "label":"can",
         "score":0.992,
         "polygon":[
            [
               0.82, 0.811, 0.771, 0.810, 0.758, 0.805, 0.741, 0.797, 0.735, 0.791, 0.718, 0.785, 0.715, 0.778, 0.706, 0.775, 0.696, 0.758, 0.695, 0.717, 0.698, 0.567, 0.705, 0.552, 0.706, 0.540, 0.725, 0.520, 0.735, 0.505, 0.745, 0.502, 0.755, 0.493
            ]
         ]
      },
      {
         "box":{
            "topX":0.220,
            "topY":0.298,
            "bottomX":0.397,
            "bottomY":0.601
         },
         "label":"milk_bottle",
         "score":0.989,
         "polygon":[
            [
               0.365, 0.602, 0.273, 0.602, 0.26, 0.595, 0.263, 0.588, 0.251, 0.546, 0.248, 0.501, 0.25, 0.485, 0.246, 0.478, 0.245, 0.463, 0.233, 0.442, 0.231, 0.43, 0.226, 0.423, 0.226, 0.408, 0.234, 0.385, 0.241, 0.371, 0.238, 0.345, 0.234, 0.335, 0.233, 0.325, 0.24, 0.305, 0.586, 0.38, 0.592, 0.375, 0.598, 0.365
            ]
         ]
      },
      {
         "box":{
            "topX":0.433,
            "topY":0.280,
            "bottomX":0.621,
            "bottomY":0.679
         },
         "label":"water_bottle",
         "score":0.988,
         "polygon":[
            [
               0.576, 0.680, 0.501, 0.680, 0.475, 0.675, 0.460, 0.625, 0.445, 0.630, 0.443, 0.572, 0.440, 0.560, 0.435, 0.515, 0.431, 0.501, 0.431, 0.433, 0.433, 0.426, 0.445, 0.417, 0.456, 0.407, 0.465, 0.381, 0.468, 0.327, 0.471, 0.318
            ]
         ]
      }
   ]
}
```

## <a name="next-steps"></a>다음 단계

[자동화 된 ML를 사용 하 여 컴퓨터 비전 모델 학습을 위해 데이터를 준비](how-to-prepare-datasets-for-automl-images.md)하는 방법을 알아봅니다.
