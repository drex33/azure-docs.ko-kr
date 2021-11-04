---
title: 스튜디오를 사용하여 디자이너에서 학습된 모델 배포
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 스튜디오를 사용하여 코드를 한 줄도 작성하지 않고 가계 학습 모델을 배포합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: deploy, studio, designer
ms.openlocfilehash: 4722a32435ad3b1a8f7fdcf0cd46c077b52a5f08
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557607"
---
# <a name="use-the-studio-to-deploy-models-trained-in-the-designer"></a>스튜디오를 사용하여 디자이너에서 학습된 모델 배포

이 문서에서는 Azure Machine Learning 스튜디오에서 디자이너 모델을 실시간 엔드포인트로 배포하는 방법을 알아봅니다.

등록하거나 다운로드한 후에는 다른 모델과 마찬가지로 디자이너에서 학습한 모델을 사용할 수 있습니다. 내보낸 모델은 IoT(사물 인터넷) 및 로컬 배포와 같은 사용 사례에서 배포할 수 있습니다.

스튜디오의 배포는 다음 단계로 구성됩니다.

1. 학습된 모델을 등록합니다.
1. 모델에 대한 항목 스크립트 및 conda 종속성 파일을 다운로드합니다.
1. (선택 사항) 항목 스크립트를 구성합니다.
1. 컴퓨팅 대상에 모델을 배포합니다.

디자이너에서 직접 모델을 배포하여 모델 등록 및 파일 다운로드 단계를 건너뛸 수도 있습니다. 이 작업은 빠른 배포를 위해 유용할 수 있습니다. 자세한 내용은 [디자이너를 사용하여 모델 배포](tutorial-designer-automobile-price-deploy.md)를 참조하세요.

디자이너에서 학습한 모델은 SDK 또는 CLI(명령줄 인터페이스)를 통해 배포할 수도 있습니다. 자세한 내용은 [Azure Machine Learning을 사용하여 모델 배포](how-to-deploy-and-where.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)

* 다음 구성 요소 중 하나를 포함 하는 완료 된 학습 파이프라인:
    - [모델 구성 요소 학습](./algorithm-module-reference/train-model.md)
    - [변칙 검색 모델 구성 요소 학습](./algorithm-module-reference/train-anomaly-detection-model.md)
    - [클러스터링 모델 구성 요소 학습](./algorithm-module-reference/train-clustering-model.md)
    - [Pytorch 모델 구성 요소 학습](./algorithm-module-reference/train-pytorch-model.md)
    - [.SVD 추천 구성 요소 학습](./algorithm-module-reference/train-svd-recommender.md)
    - [Vowpal Wabbit 모델 구성 요소 학습](./algorithm-module-reference/train-vowpal-wabbit-model.md)
    - [넓은 & 심화 모델 구성 요소 학습](./algorithm-module-reference/train-wide-and-deep-recommender.md)

## <a name="register-the-model"></a>모델 등록

학습 파이프라인이 완료된 후에는 학습된 모델을 Azure Machine Learning 작업 영역에 등록하여 다른 프로젝트의 모델에 액세스할 수 있습니다.

1. [모델 학습 구성 요소](./algorithm-module-reference/train-model.md)를 선택 합니다.
1. 오른쪽 창에서 **출력 + 로그** 탭을 선택합니다.
1. **모델 등록** 아이콘을 선택합니다. ![기어 아이콘 스크린샷](./media/how-to-deploy-model-designer/register-model-icon.png)

    ![모델 학습 구성 요소 오른쪽 창의 스크린샷](./media/how-to-deploy-model-designer/train-model-right-pane.png)

1. 모델의 이름을 입력하고 **저장** 을 선택합니다.

모델을 등록한 후에는 스튜디오의 **모델** 자산 페이지에서 찾을 수 있습니다.
    
![모델 자산 페이지의 등록된 모델 스크린샷](./media/how-to-deploy-model-designer/models-asset-page.png)

## <a name="download-the-entry-script-file-and-conda-dependencies-file"></a>항목 스크립트 파일 및 conda 종속성 파일 다운로드

Azure Machine Learning 스튜디오에서 모델을 배포하려면 다음 파일이 필요합니다.

- **항목 스크립트 파일** - 학습된 모델을 로드하고, 요청에서 입력 데이터를 처리하고, 실시간 추론을 수행하고, 결과를 반환합니다. `score.py` **모델 학습** 구성 요소가 완료 되 면 디자이너에서 자동으로 항목 스크립트 파일을 생성 합니다.

- **Conda 종속성 파일** - webservice가 종속된 pip 및 Conda 패키지를 지정합니다. `conda_env.yaml` **모델 학습** 구성 요소가 완료 되 면 디자이너에서 자동으로 파일을 만듭니다.

**모델 학습** 구성 요소의 오른쪽 창에서 다음 두 파일을 다운로드할 수 있습니다.

1. **모델 학습** 구성 요소를 선택 합니다.
1. **출력 + 로그** 탭에서 `trained_model_outputs` 폴더를 선택합니다.
1. `conda_env.yaml`파일 및 `score.py` 파일을 다운로드합니다.

    ![오른쪽 창의 배포용 다운로드 파일 스크린샷](./media/how-to-deploy-model-designer/download-artifacts-in-right-pane.png)

또는 모델을 등록한 후 **모델** 자산 페이지에서 파일을 다운로드할 수 있습니다.

1. **모델** 자산 페이지로 이동합니다.
1. 배포하려는 모델을 선택합니다.
1. **아티팩트** 탭을 선택합니다.
1. `trained_model_outputs` 폴더를 선택합니다.
1. `conda_env.yaml`파일 및 `score.py` 파일을 다운로드합니다.  

    ![모델 세부 정보 페이지의 배포용 다운로드 파일 스크린샷](./media/how-to-deploy-model-designer/download-artifacts-in-models-page.png)

> [!NOTE]
> 이 `score.py` 파일은 **모델 점수 매기기** 구성 요소와 거의 동일한 기능을 제공 합니다. 그러나 [점수 .Svd 추천](./algorithm-module-reference/score-svd-recommender.md), [점수 Wide and Deep 추천](./algorithm-module-reference/score-wide-and-deep-recommender.md), [점수 Vowpal wabbit 모델](./algorithm-module-reference/score-vowpal-wabbit-model.md) 등의 일부 구성 요소에는 다른 점수 매기기 모드의 매개 변수가 있습니다. 항목 스크립트에서 이러한 매개 변수를 변경할 수도 있습니다.
>
>`score.py` 파일에서 매개 변수를 설정하는 방법에 대한 자세한 내용은 [항목 스크립트 구성](#configure-the-entry-script) 섹션을 참조하세요.

## <a name="deploy-the-model"></a>모델 배포

필요한 파일을 다운로드한 후에는 모델을 배포할 준비가 된 것입니다.

1. **모델** 자산 페이지에서 등록된 모델을 선택합니다.
1. **배포** 를 선택 하 고 **웹 서비스에 배포를** 선택 합니다.
    ![모델 자산 페이지의 배포 단추 스크린샷](./media/how-to-deploy-model-designer/open-deploy-wizard.png)
1. 구성 메뉴에서 다음 정보를 입력합니다.

    - 엔드포인트의 이름을 입력합니다.
    - [Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) 또는 [Azure Container Instance](how-to-deploy-azure-container-instance.md)에 모델을 배포하도록 선택합니다.
    - **항목 스크립트 파일** 에 대한 `score.py`를 업로드합니다.
    - **Conda 종속성 파일** 에 대한 `conda_env.yml`을 업로드합니다. 

    >[!TIP]
    > **고급** 설정에서 배포를 위한 CPU/메모리 용량 및 기타 매개 변수를 설정할 수 있습니다. 이러한 설정은 상당한 양의 메모리(약 4GB)를 사용하는 PyTorch 모델과 같은 특정 모델에 중요합니다.

1. **배포** 를 선택하여 모델을 실시간 엔드포인트로 배포합니다.

    ![모델 자산 페이지의 배포 모델 스크린샷](./media/how-to-deploy-model-designer/deploy-model.png)

## <a name="consume-the-real-time-endpoint"></a>실시간 엔드포인트 사용

배포가 성공하면 **엔드포인트** 자산 페이지에서 실시간 엔드포인트를 찾을 수 있습니다. 이 경우 클라이언트가 실시간 엔드포인트에 요청을 전송하는 데 사용할 수 있는 REST 엔드포인트를 찾을 수 있습니다. 

> [!NOTE]
> 또한 디자이너는 테스트를 위해 샘플 데이터 json 파일을 생성하고 **trained_model_outputs** 폴더에서 `_samples.json`을 다운로드할 수 있습니다.

다음 코드 샘플을 사용하여 실시간 엔드포인트를 사용합니다.

```python

import json
from pathlib import Path
from azureml.core.workspace import Workspace, Webservice
 
service_name = 'YOUR_SERVICE_NAME'
ws = Workspace.get(
    name='WORKSPACE_NAME',
    subscription_id='SUBSCRIPTION_ID',
    resource_group='RESOURCEGROUP_NAME'
)
service = Webservice(ws, service_name)
sample_file_path = '_samples.json'
 
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)
score_result = service.run(json.dumps(sample_data))
print(f'Inference result = {score_result}')
```

### <a name="consume-computer-vision-related-real-time-endpoints"></a>Computer Vision 관련 실시간 엔드포인트 사용

Computer Vision 관련 실시간 엔드포인트를 사용할 때 웹 서비스는 문자열만 입력으로 허용하므로 이미지를 바이트로 변환해야 합니다. 다음은 샘플 코드입니다.

```python
import base64
import json
from copy import deepcopy
from pathlib import Path
from azureml.studio.core.io.image_directory import (IMG_EXTS, image_from_file, image_to_bytes)
from azureml.studio.core.io.transformation_directory import ImageTransformationDirectory

# image path
image_path = Path('YOUR_IMAGE_FILE_PATH')

# provide the same parameter setting as in the training pipeline. Just an example here.
image_transform = [
    # format: (op, args). {} means using default parameter values of torchvision.transforms.
    # See https://pytorch.org/docs/stable/torchvision/transforms.html
    ('Resize', 256),
    ('CenterCrop', 224),
    # ('Pad', 0),
    # ('ColorJitter', {}),
    # ('Grayscale', {}),
    # ('RandomResizedCrop', 256),
    # ('RandomCrop', 224),
    # ('RandomHorizontalFlip', {}),
    # ('RandomVerticalFlip', {}),
    # ('RandomRotation', 0),
    # ('RandomAffine', 0),
    # ('RandomGrayscale', {}),
    # ('RandomPerspective', {}),
]
transform = ImageTransformationDirectory.create(transforms=image_transform).torch_transform

# download _samples.json file under Outputs+logs tab in the right pane of Train Pytorch Model component
sample_file_path = '_samples.json'
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)

# use first sample item as the default value
default_data = sample_data[0]
data_list = []
for p in image_path.iterdir():
    if p.suffix.lower() in IMG_EXTS:
        data = deepcopy(default_data)
        # convert image to bytes
        data['image'] = base64.b64encode(image_to_bytes(transform(image_from_file(p)))).decode()
        data_list.append(data)

# use data.json as input of consuming the endpoint
data_file_path = 'data.json'
with open(data_file_path, 'w') as f:
    json.dump(data_list, f)
```

## <a name="configure-the-entry-script"></a>항목 스크립트 구성

[점수 .Svd 추천](./algorithm-module-reference/score-svd-recommender.md), [점수 넓게 및 딥 추천](./algorithm-module-reference/score-wide-and-deep-recommender.md), [점수 Vowpal wabbit 모델](./algorithm-module-reference/score-vowpal-wabbit-model.md) 등 디자이너의 일부 구성 요소에는 다른 점수 매기기 모드의 매개 변수가 있습니다. 

이 섹션에서는 항목 스크립트 파일에서 이러한 매개 변수를 업데이트하는 방법을 알아봅니다.

다음 예제에서는 학습된 **Wide & Deep recommender** 모델에 대한 기본 동작을 업데이트합니다. 기본적으로 `score.py` 파일은 사용자와 항목 간의 등급을 예측하도록 웹 서비스에 지시합니다. 

항목 스크립트 파일을 수정하여 항목 권장 사항을 만들고 `recommender_prediction_kind` 매개 변수를 변경하여 권장 항목을 반환할 수 있습니다.


```python
import os
import json
from pathlib import Path
from collections import defaultdict
from azureml.studio.core.io.model_directory import ModelDirectory
from azureml.designer.modules.recommendation.dnn.wide_and_deep.score. \
    score_wide_and_deep_recommender import ScoreWideAndDeepRecommenderModule
from azureml.designer.serving.dagengine.utils import decode_nan
from azureml.designer.serving.dagengine.converter import create_dfd_from_dict

model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'trained_model_outputs')
schema_file_path = Path(model_path) / '_schema.json'
with open(schema_file_path) as fp:
    schema_data = json.load(fp)


def init():
    global model
    model = ModelDirectory.load(load_from_dir=model_path)


def run(data):
    data = json.loads(data)
    input_entry = defaultdict(list)
    for row in data:
        for key, val in row.items():
            input_entry[key].append(decode_nan(val))

    data_frame_directory = create_dfd_from_dict(input_entry, schema_data)

    # The parameter names can be inferred from Score Wide and Deep Recommender component parameters:
    # convert the letters to lower cases and replace whitespaces to underscores.
    score_params = dict(
        trained_wide_and_deep_recommendation_model=model,
        dataset_to_score=data_frame_directory,
        training_data=None,
        user_features=None,
        item_features=None,
        ################### Note #################
        # Set 'Recommender prediction kind' parameter to enable item recommendation model
        recommender_prediction_kind='Item Recommendation',
        recommended_item_selection='From All Items',
        maximum_number_of_items_to_recommend_to_a_user=5,
        whether_to_return_the_predicted_ratings_of_the_items_along_with_the_labels='True')
    result_dfd, = ScoreWideAndDeepRecommenderModule().run(**score_params)
    result_df = result_dfd.data
    return json.dumps(result_df.to_dict("list"))
```

**Wide & Deep recommender** 및 **Vowpal Wabbit** 모델의 경우 다음 방법을 사용하여 점수 매기기 모드 매개 변수를 구성할 수 있습니다.

- 매개 변수 이름은 [Score Vowpal Wabbit Model](./algorithm-module-reference/score-vowpal-wabbit-model.md) 및 [Score Wide and Deep Recommender](./algorithm-module-reference/score-wide-and-deep-recommender.md)에 대한 대/소문자가 조합된 매개 변수 이름입니다.
- 모드 형식 매개 변수 값은 해당하는 옵션 이름의 문자열입니다. 위의 코드에서 **Recommender prediction kind** 를 예제로 사용할 경우 값은 `'Rating Prediction'` 또는 `'Item Recommendation'`일 수 있습니다. 다른 값은 허용되지 않습니다.

**SVD recommender** 학습된 모델의 경우 매개 변수 이름과 값이 명확하지 않을 수 있으며, 아래 표를 조회하여 매개 변수 설정 방법을 결정할 수 있습니다.

| [Score SVD Recommender](./algorithm-module-reference/score-svd-recommender.md)의 매개 변수 이름                           | 항목 스크립트 파일의 매개 변수 이름 |
| ------------------------------------------------------------ | --------------------------------------- |
| 추천 예측 종류                                  | prediction_kind                         |
| 추천 항목 선택                                   | recommended_item_selection              |
| 한 사용자에 대한 권장 풀의 최소 크기    | min_recommendation_pool_size            |
| 사용자에게 권장할 최대 항목 수               | max_recommended_item_count              |
| 레이블과 함께 항목의 예측된 등급을 반환할지 여부 | return_ratings                          |

다음 코드에서는 6개의 매개 변수를 모두 사용하여 예측 등급이 추가된 등급 항목을 권장하는 SVD recommender에 대한 매개 변수를 설정하는 방법을 보여 줍니다.

```python
score_params = dict(
        learner=model,
        test_data=DataTable.from_dfd(data_frame_directory),
        training_data=None,
        # RecommenderPredictionKind has 2 members, 'RatingPrediction' and 'ItemRecommendation'. You
        # can specify prediction_kind parameter with one of them.
        prediction_kind=RecommenderPredictionKind.ItemRecommendation,
        # RecommendedItemSelection has 3 members, 'FromAllItems', 'FromRatedItems', 'FromUndatedItems'.
        # You can specify recommended_item_selection parameter with one of them.
        recommended_item_selection=RecommendedItemSelection.FromRatedItems,
        min_recommendation_pool_size=1,
        max_recommended_item_count=3,
        return_ratings=True,
    )
```


## <a name="next-steps"></a>다음 단계

* [디자이너에서 모델 학습](tutorial-designer-automobile-price-train-score.md)
* [Azure Machine Learning SDK를 사용하여 모델 배포](how-to-deploy-and-where.md)
* [실패한 배포 문제 해결](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service로 배포](how-to-deploy-azure-kubernetes-service.md)
* [웹 서비스를 사용하는 클라이언트 애플리케이션 만들기](how-to-consume-web-service.md)
* [웹 서비스 업데이트](how-to-deploy-update-web-service.md)
