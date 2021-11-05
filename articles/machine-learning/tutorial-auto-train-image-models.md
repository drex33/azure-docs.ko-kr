---
title: '자습서: AutoML- 개체 검색 모델 학습'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 자동화된 ML을 사용하여 Azure Machine Learning Python SDK를 통해 NYC 택시 요금을 예측하도록 회귀 모델을 학습시킵니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: swatig007
ms.author: swatig
ms.reviewer: nibaccam
ms.date: 10/06/2021
ms.custom: devx-track-python, automl
ms.openlocfilehash: e6906988b47ecdb1ba5e1ca7947666e6371ddd8e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468578"
---
# <a name="tutorial-train-an-object-detection-model-preview-with-automl-and-python"></a>자습서: AutoML 및 Python을 사용하여 개체 검색 모델(미리 보기) 학습

>[!IMPORTANT]
> 이 문서에서 제공하는 기능은 미리 보기 상태입니다. 언제든지 바뀔 수 있는 [실험적](/python/api/overview/azure/ml/#stable-vs-experimental) 미리 보기 기능으로 간주해야 합니다.

이 자습서에서는 Azure Machine Learning 자동화된 ML을 사용하여 Azure Machine Learning Python SDK를 통해 개체 검색 모델을 학습시키는 방법에 대해 알아봅니다. 이 개체 검색 모델은 이미지에 깡통, 상자, 우유병 또는 물병과 같은 개체가 포함되어 있는지 여부를 식별합니다.

자동화된 ML은 학습 데이터 및 구성 설정을 적용하고, 다양한 기능 정규화/표준화 방법, 모델 및 하이퍼 매개 변수 설정의 조합을 자동으로 반복하여 최상의 모델에 도달합니다.


이 자습서에서는 Python SDK를 사용하여 코드를 작성하고 다음 작업에 대해 알아봅니다.

> [!div class="checklist"]
> * 데이터 다운로드 및 변환
> * 자동화된 Machine Learning 개체 검색 모델 학습
> * 모델에 대한 하이퍼 매개 변수 값 지정
> * 하이퍼 매개 변수 비우기 수행
> * 모델 배포
> * 검색 시각화

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 Azure Machine Learning의 [평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

* 이 기능에는 Python 3.6 또는 3.7이 지원됩니다.

* Azure Machine Learning 작업 영역이 아직 없는 경우 [빠른 시작: Azure Machine Learning 시작](quickstart-create-resources.md#create-the-workspace)을 완료합니다.

* [**odFridgeObjects.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) 데이터 파일을 다운로드합니다.

이 자습서는 [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials)에도 제공되기 때문에 자체 [로컬 환경](how-to-configure-environment.md#local)에서도 실행할 수 있습니다. 필요한 패키지를 가져오려면
* `pip install azureml`을 실행합니다.
* [`automl` 클라이언트 전체를 설치](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment)합니다.

## <a name="compute-target-setup"></a>컴퓨팅 대상 설정

먼저 자동화된 ML 모델 학습에 사용할 컴퓨팅 대상을 설정해야 합니다. 이미지 작업에 대한 자동화된 ML 모델에는 GPU SKU가 필요합니다.

이 자습서에서는 NCsv3 시리즈(V100 GPU 포함)를 사용합니다. 이 유형의 컴퓨팅 대상은 여러 GPU를 활용하여 학습 속도를 높입니다. 또한 모델에 대한 하이퍼 매개 변수를 튜닝할 때 병렬 처리를 활용하도록 여러 노드를 설정할 수 있습니다.

다음 코드는 `ws` 작업 영역에 연결된 4개의 노드가 있는 Standard _NC24s_v3 크기의 GPU 컴퓨팅을 만듭니다.

> [!WARNING]
> 사용하려는 컴퓨팅 대상에 대한 할당량이 구독에 충분한지 확인합니다.

```python
from azureml.core.compute import AmlCompute, ComputeTarget

cluster_name = "gpu-cluster-nc24s_v3"

try:
    compute_target = ws.compute_targets[cluster_name]
    print('Found existing compute target.')
except KeyError:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='Standard_NC24s_v3',
                                                           idle_seconds_before_scaledown=1800,
                                                           min_nodes=0,
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

#If no min_node_count is provided, the scale settings are used for the cluster.
compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="experiment-setup"></a>실험 설정
다음으로, 모델 학습 실행을 추적하기 위해 `Experiment`를 작업 영역에 만듭니다.

```python

from azureml.core import Experiment

experiment_name = 'automl-image-object-detection'
experiment = Experiment(ws, name=experiment_name)
```

## <a name="visualize-input-data"></a>입력 데이터 시각화

입력 이미지 데이터가 [JSONL](https://jsonlines.org/)(JSON 줄) 형식으로 준비되면 이미지에 대한 지상 실측 경계 상자를 시각화할 수 있습니다. 이렇게 하려면 `matplotlib`가 설치되어 있는지 확인합니다.

```
%pip install --upgrade matplotlib
```
```python

%matplotlib inline
import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image as pil_image
import numpy as np
import json
import os

def plot_ground_truth_boxes(image_file, ground_truth_boxes):
    # Display the image
    plt.figure()
    img_np = mpimg.imread(image_file)
    img = pil_image.fromarray(img_np.astype("uint8"), "RGB")
    img_w, img_h = img.size

    fig,ax = plt.subplots(figsize=(12, 16))
    ax.imshow(img_np)
    ax.axis("off")

    label_to_color_mapping = {}

    for gt in ground_truth_boxes:
        label = gt["label"]

        xmin, ymin, xmax, ymax =  gt["topX"], gt["topY"], gt["bottomX"], gt["bottomY"]
        topleft_x, topleft_y = img_w * xmin, img_h * ymin
        width, height = img_w * (xmax - xmin), img_h * (ymax - ymin)

        if label in label_to_color_mapping:
            color = label_to_color_mapping[label]
        else:
            # Generate a random color. If you want to use a specific color, you can use something like "red".
            color = np.random.rand(3)
            label_to_color_mapping[label] = color

        # Display bounding box
        rect = patches.Rectangle((topleft_x, topleft_y), width, height,
                                 linewidth=2, edgecolor=color, facecolor="none")
        ax.add_patch(rect)

        # Display label
        ax.text(topleft_x, topleft_y - 10, label, color=color, fontsize=20)

    plt.show()

def plot_ground_truth_boxes_jsonl(image_file, jsonl_file):
    image_base_name = os.path.basename(image_file)
    ground_truth_data_found = False
    with open(jsonl_file) as fp:
        for line in fp.readlines():
            line_json = json.loads(line)
            filename = line_json["image_url"]
            if image_base_name in filename:
                ground_truth_data_found = True
                plot_ground_truth_boxes(image_file, line_json["label"])
                break
    if not ground_truth_data_found:
        print("Unable to find ground truth information for image: {}".format(image_file))

def plot_ground_truth_boxes_dataset(image_file, dataset_pd):
    image_base_name = os.path.basename(image_file)
    image_pd = dataset_pd[dataset_pd['portable_path'].str.contains(image_base_name)]
    if not image_pd.empty:
        ground_truth_boxes = image_pd.iloc[0]["label"]
        plot_ground_truth_boxes(image_file, ground_truth_boxes)
    else:
        print("Unable to find ground truth information for image: {}".format(image_file))
```

위의 도우미 함수를 사용하면 지정된 이미지에 대해 다음 코드를 실행하여 경계 상자를 표시할 수 있습니다.

```python
image_file = "./odFridgeObjects/images/31.jpg"
jsonl_file = "./odFridgeObjects/train_annotations.jsonl"

plot_ground_truth_boxes_jsonl(image_file, jsonl_file)
```

## <a name="upload-data-and-create-dataset"></a>데이터 업로드 및 데이터 세트 만들기

학습을 위해 데이터를 사용하려면 데이터 저장소를 통해 작업 영역에 업로드합니다. 데이터 저장소는 데이터를 업로드하거나 다운로드하고 원격 컴퓨팅 대상에서 상호 작용할 수 있는 메커니즘을 제공합니다.

```python
ds = ws.get_default_datastore()
ds.upload(src_dir='./odFridgeObjects', target_path='odFridgeObjects')
```

데이터 저장소에 업로드되면 데이터에서 Azure Machine Learning 데이터 세트를 만들 수 있습니다. 데이터 세트는 학습을 위해 데이터를 소모성 개체에 패키지합니다.

다음 코드는 학습용 데이터 세트를 만듭니다. 유효성 검사 데이터 세트가 지정되지 않았으므로 기본적으로 학습 데이터의 20%가 유효성 검사에 사용됩니다.

``` python
from azureml.core import Dataset
from azureml.data import DataType

training_dataset_name = 'odFridgeObjectsTrainingDataset'
if training_dataset_name in ws.datasets:
    training_dataset = ws.datasets.get(training_dataset_name)
    print('Found the training dataset', training_dataset_name)
else:
    # create training dataset
        # create training dataset
    training_dataset = Dataset.Tabular.from_json_lines_files(
        path=ds.path('odFridgeObjects/train_annotations.jsonl'),
        set_column_types={"image_url": DataType.to_stream(ds.workspace)},
    )
    training_dataset = training_dataset.register(workspace=ws, name=training_dataset_name)

print("Training dataset name: " + training_dataset.name)
```

### <a name="visualize-dataset"></a>데이터 세트 시각화

이 데이터 세트에서 이미지에 대한 지상 실측 경계 상자를 시각화할 수도 있습니다.

데이터 세트를 pandas 데이터 프레임에 로드합니다.

```python
import azureml.dataprep as dprep

from azureml.dataprep.api.functions import get_portable_path

# Get pandas dataframe from the dataset
dflow = training_dataset._dataflow.add_column(get_portable_path(dprep.col("image_url")),
                                              "portable_path", "image_url")
dataset_pd = dflow.to_pandas_dataframe(extended_types=True)
```

지정된 이미지에 대해 다음 코드를 실행하여 경계 상자를 표시할 수 있습니다.

```python
image_file = "./odFridgeObjects/images/31.jpg"
plot_ground_truth_boxes_dataset(image_file, dataset_pd)
```

## <a name="configure-your-object-detection-experiment"></a>개체 검색 실험 구성

이미지 관련 작업에 대해 자동화된 ML 실행을 구성하려면 `AutoMLImageConfig` 개체를 사용합니다. `AutoMLImageConfig`에서 `model_name` 매개 변수를 사용하여 모델 알고리즘을 지정하고, 하이퍼 매개 변수 비우기를 정의된 매개 변수 공간에 대해 수행하여 최적의 모델을 찾도록 설정을 구성할 수 있습니다.

이 예에서는 `AutoMLImageConfig`를 사용하여 `yolov5` 및 `fasterrcnn_resnet50_fpn`을 통해 개체 검색 모델을 학습시킵니다. 둘 모두는 80개가 넘는 레이블 범주가 있는 레이블이 지정된 수천 개가 넘는 이미지가 포함된 대규모 개체 검색, 조각화 및 캡션 데이터 세트인 COCO에 대해 미리 학습되었습니다.

### <a name="hyperparameter-sweeping-for-image-tasks"></a>이미지 작업에 대한 하이퍼 매개 변수 비우기

정의된 매개 변수 공간에 대해 하이퍼 매개 변수 비우기를 수행하여 최적의 모델을 찾을 수 있습니다.

다음 코드는 정의된 각 알고리즘(`yolov5` 및 `fasterrcnn_resnet50_fpn`)에 대한 하이퍼 매개 변수 비우기를 준비하기 위해 매개 변수 공간을 정의합니다.  매개 변수 공간에서 AutoML이 최적의 기본 메트릭을 사용하여 모델을 생성하려고 시도할 때 선택할 `learning_rate`, `optimizer`, `lr_scheduler` 등의 값 범위를 지정합니다. 하이퍼 매개 변수 값을 지정하지 않으면 기본값이 각 알고리즘에 사용됩니다.

튜닝 설정의 경우 무작위 샘플링을 통해 `GridParameterSampling, RandomParameterSampling` 및 `BayesianParameterSampling` 클래스를 가져와서 이 매개 변수 공간에서 샘플을 선택합니다.  이렇게 하면 자동화된 ML에서 이러한 다양한 샘플을 사용하여 총 20회의 반복을 시도하도록 지시하고, 4개의 노드를 사용하여 설정된 컴퓨팅 대상에서 한 번에 4회의 반복을 실행합니다. 공간에 매개 변수가 많을수록 최적의 모델을 찾는 데 더 많은 반복이 필요합니다.

Bandit 조기 종료 정책도 사용됩니다. 이 정책은 성능이 떨어지는 구성을 종료합니다. 즉, 성능이 가장 좋은 구성의 여유 시간이 20% 이내가 아니므로 컴퓨팅 리소스를 크게 절약할 수 있습니다.

```python
from azureml.train.hyperdrive import RandomParameterSampling
from azureml.train.hyperdrive import BanditPolicy, HyperDriveConfig
from azureml.train.hyperdrive import choice, uniform

parameter_space = {
    'model': choice(
        {
            'model_name': choice('yolov5'),
            'learning_rate': uniform(0.0001, 0.01),
            #'model_size': choice('small', 'medium'), # model-specific
            'img_size': choice(640, 704, 768), # model-specific
        },
        {
            'model_name': choice('fasterrcnn_resnet50_fpn'),
            'learning_rate': uniform(0.0001, 0.001),
            #'warmup_cosine_lr_warmup_epochs': choice(0, 3),
            'optimizer': choice('sgd', 'adam', 'adamw'),
            'min_size': choice(600, 800), # model-specific
        }
    )
}

tuning_settings = {
    'iterations': 20,
    'max_concurrent_iterations': 4,
    'hyperparameter_sampling': RandomParameterSampling(parameter_space),
    'policy': BanditPolicy(evaluation_interval=2, slack_factor=0.2, delay_evaluation=6)
}
```

매개 변수 공간 및 튜닝 설정이 정의되면 이를 `AutoMLImageConfig` 개체에 전달한 다음, 실험을 제출하여 학습 데이터 세트를 통해 이미지 모델을 학습시킬 수 있습니다.

```python
from azureml.train.automl import AutoMLImageConfig
automl_image_config = AutoMLImageConfig(task='image-object-detection',
                                        compute_target=compute_target,
                                        training_data=training_dataset,
                                        validation_data=validation_dataset,
                                        primary_metric='mean_average_precision',
                                        **tuning_settings)

automl_image_run = experiment.submit(automl_image_config)
automl_image_run.wait_for_completion(wait_post_processing=True)
```

하이퍼 매개 변수 비우기를 수행하는 경우 HyperDrive UI를 사용하여 시도된 다양한 구성을 시각화하는 것이 유용할 수 있습니다. HyperDrive 부모 실행인 위의 기본 automl_image_run의 UI에서 '자식 실행' 탭으로 이동하여 이 UI로 이동할 수 있습니다. 그런 다음, 이 항목의 '자식 실행' 탭으로 이동할 수 있습니다. 또는 아래에서 HyperDrive 부모 실행을 직접 확인하여 '자식 실행' 탭으로 이동할 수 있습니다.

```python
from azureml.core import Run
hyperdrive_run = Run(experiment=experiment, run_id=automl_image_run.id + '_HD')
hyperdrive_run
```

## <a name="register-the-best-model"></a>최적 모델 등록

실행이 완료되면 최상의 실행에서 만들어진 모델을 등록할 수 있습니다.

```python
best_child_run = automl_image_run.get_best_child()
model_name = best_child_run.properties['model_name']
model = best_child_run.register_model(model_name = model_name, model_path='outputs/model.pt')
```

## <a name="deploy-model-as-a-web-service"></a>모델을 웹 서비스로 배포

학습된 모델이 있으면 해당 모델을 Azure에 배포할 수 있습니다. 학습된 모델은 ACI(Azure Container Instances) 또는 AKS(Azure Kubernetes Service)에서 웹 서비스로 배포할 수 있습니다. ACI는 배포를 테스트하는 데 가장 적합한 옵션이지만, AKS는 대규모 프로덕션 사용에 더 적합합니다.

이 자습서에서는 AKS에서 모델을 웹 서비스로 배포합니다.

1. AKS 컴퓨팅 클러스터를 만듭니다. 이 예제에서는 GPU 가상 머신 SKU가 배포 클러스터에 사용됩니다.

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

1. 모델이 포함된 웹 서비스를 설정하는 방법을 설명하는 유추 구성을 정의합니다. 유추 구성에서 실행되는 학습의 채점 스크립트와 환경을 사용할 수 있습니다.

    > [!NOTE]
    > 모델의 설정을 변경하려면 다운로드한 채점 스크립트를 열고, 모델을 배포하기 전에 model_settings 변수를 수정합니다.

    ```python
    from azureml.core.model import InferenceConfig

    best_child_run.download_file('outputs/scoring_file_v_1_0_0.py', output_file_path='score.py')
    environment = best_child_run.get_environment()
    inference_config = InferenceConfig(entry_script='score.py', environment=environment)
    ```

1. 그런 다음, 모델을 AKS 웹 서비스로 배포할 수 있습니다.

    ```python

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

## <a name="test-the-web-service"></a>웹 서비스 테스트

배포된 웹 서비스를 테스트하여 새 이미지를 예측할 수 있습니다. 이 자습서에서는 데이터 세트에서 임의의 이미지를 전달하고, 채점 URI에 전달합니다.

```python
import requests

# URL for the web service
scoring_uri = aks_service.scoring_uri

# If the service is authenticated, set the key or token
key, _ = aks_service.get_keys()

sample_image = './test_image.jpg'

# Load image data
data = open(sample_image, 'rb').read()

# Set the content type
headers = {'Content-Type': 'application/octet-stream'}

# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, data, headers=headers)
print(resp.text)
```
## <a name="visualize-detections"></a>검색 시각화
이제 테스트 이미지에 대한 점수를 매겼으므로 이 이미지의 경계 상자를 시각화할 수 있습니다. 이렇게 하려면 matplotlib가 설치되어 있어야 합니다.

```
%pip install --upgrade matplotlib
```

```python
%matplotlib inline
import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image
import numpy as np
import json

IMAGE_SIZE = (18,12)
plt.figure(figsize=IMAGE_SIZE)
img_np=mpimg.imread(sample_image)
img = Image.fromarray(img_np.astype('uint8'),'RGB')
x, y = img.size

fig,ax = plt.subplots(1, figsize=(15,15))
# Display the image
ax.imshow(img_np)

# draw box and label for each detection
detections = json.loads(resp.text)
for detect in detections['boxes']:
    label = detect['label']
    box = detect['box']
    conf_score = detect['score']
    if conf_score > 0.6:
        ymin, xmin, ymax, xmax =  box['topY'],box['topX'], box['bottomY'],box['bottomX']
        topleft_x, topleft_y = x * xmin, y * ymin
        width, height = x * (xmax - xmin), y * (ymax - ymin)
        print('{}: [{}, {}, {}, {}], {}'.format(detect['label'], round(topleft_x, 3),
                                                round(topleft_y, 3), round(width, 3),
                                                round(height, 3), round(conf_score, 3)))

        color = np.random.rand(3) #'red'
        rect = patches.Rectangle((topleft_x, topleft_y), width, height,
                                 linewidth=3, edgecolor=color,facecolor='none')

        ax.add_patch(rect)
        plt.text(topleft_x, topleft_y - 10, label, color=color, fontsize=20)

plt.show()
```

## <a name="clean-up-resources"></a>리소스 정리

다른 Azure Machine Learning 자습서를 실행하려면 이 섹션을 완료하지 마세요.

자신이 만든 리소스를 사용하지 않으려는 경우 요금이 발생하지 않도록 삭제하세요.

1. Azure Portal의 맨 왼쪽에서 **리소스 그룹** 을 선택합니다.
1. 목록에서 만든 리소스 그룹을 선택합니다.
1. **리소스 그룹 삭제** 를 선택합니다.
1. 리소스 그룹 이름을 입력합니다. 그런 다음, **삭제** 를 선택합니다.

또한 리소스 그룹을 유지하면서 단일 작업 영역을 삭제할 수도 있습니다. 작업 영역 속성을 표시하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자동화된 Machine Learning 자습서에서 다음 작업을 수행했습니다.

> [!div class="checklist"]
> * 작업 영역을 구성하고 실험을 위해 데이터를 준비했습니다.
> * 자동화된 개체 검색 모델 학습
> * 모델에 대한 하이퍼 매개 변수 값 지정
> * 하이퍼 매개 변수 비우기 수행
> * 모델 배포
> * 검색 시각화

* [자동화된 ML의 컴퓨터 비전(미리 보기)에 대해 자세히 알아봅니다](concept-automated-ml.md#computer-vision-preview).
* [Python을 사용하여 컴퓨터 비전 모델을 학습시키도록 AutoML을 설정하는 방법(미리 보기)을 알아봅니다](how-to-auto-train-image-models.md).
* [자동화된 기계 학습 샘플에 대한 GitHub Notebook 리포지토리](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml)에서 자세한 코드 예제 및 사용 사례를 검토합니다. 컴퓨터 비전 모델 빌드와 관련된 샘플은 'image-' 접두사가 있는 폴더를 확인하세요.

> [!NOTE]
> 냉장고 개체 데이터 세트는 [MIT 라이선스](https://github.com/microsoft/computervision-recipes/blob/master/LICENSE)의 라이선스를 통해 사용할 수 있습니다.
