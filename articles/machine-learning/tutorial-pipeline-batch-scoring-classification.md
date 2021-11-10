---
title: '자습서: 일괄 처리 채점용 ML 파이프라인'
titleSuffix: Azure Machine Learning
description: 이 자습서에서는 일괄 처리 채점을 수행하는 기계 학습 파이프라인을 빌드합니다. 인프라와 자동화 대신 기계 학습에 중점을 둡니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: tutorial
author: lobrien
ms.author: laobri
ms.reviewer: laobri
ms.date: 10/13/2020
ms.custom: contperf-fy20q4, devx-track-python
ms.openlocfilehash: db24cbb9bcff2f37969ce61f4101e9b2133800aa
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132133741"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>자습서: 일괄 처리 채점용 Azure Machine Learning 파이프라인 빌드

이 고급 자습서에서는 [Azure Machine Learning 파이프라인](concept-ml-pipelines.md)을 빌드하여 일괄 처리 채점 작업을 실행하는 방법에 대해 알아봅니다. 기계 학습 파이프라인은 속도, 이식성 및 재사용을 통해 워크플로를 최적화하므로 인프라 및 자동화 대신 기계 학습에 집중할 수 있습니다. 파이프라인이 빌드되어 게시되면 모든 플랫폼의 모든 HTTP 라이브러리에서 해당 파이프라인을 트리거하는 데 사용할 수 있는 REST 엔드포인트를 구성합니다. 

이 예제에서는 Tensorflow에 구현된 미리 학습된 [Inception-V3](https://arxiv.org/abs/1512.00567) 나선형 신경망 모델을 사용하여 레이블이 지정되지 않은 이미지를 분류합니다. 

이 자습서에서는 다음 작업을 완료합니다.

> [!div class="checklist"]
> * 작업 영역 구성 
> * 샘플 데이터 다운로드 및 저장
> * 데이터를 가져오고 출력할 데이터 세트 개체 만들기
> * 작업 영역에서 모델 다운로드, 준비 및 등록
> * 컴퓨팅 대상 프로비저닝 및 채점 스크립트 만들기
> * 비동기 일괄 처리 점수 매기기에 `ParallelRunStep` 클래스 사용
> * 파이프라인 빌드, 실행 및 게시
> * 파이프라인에 REST 엔드포인트 사용

Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure Machine Learning 작업 영역 또는 컴퓨팅 인스턴스가 없는 경우 [빠른 시작: Azure Machine Learning 시작](quickstart-create-resources.md)을 완료합니다.
* 빠른 시작을 완료한 후:
    1. 스튜디오에서 **Notebooks** 를 선택합니다.
    1. **샘플** 탭을 선택합니다.
    1. *tutorials/machine-learning-pipelines-advanced/tutorial-pipeline-batch-scoring-classification.ipynb* Notebook을 엽니다.

사용자 고유의 [로컬 환경](how-to-configure-environment.md#local)에서 설정 자습서를 실행하려면 [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials)에서 해당 자습서에 액세스할 수 있습니다. `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests`을 실행하여 필요한 패키지를 가져옵니다.

## <a name="configure-workspace-and-create-a-datastore"></a>작업 영역 구성 및 데이터 저장소 만들기

기존 Azure Machine Learning 작업 영역에서 작업 영역 개체를 만듭니다.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

> [!IMPORTANT]
> 이 코드 조각은 작업 영역 구성이 현재 디렉터리 또는 부모 디렉터리에 저장될 것으로 예상합니다. 작업 영역 만들기에 대한 자세한 내용은 [Azure Machine Learning 작업 영역 만들기 및 관리](how-to-manage-workspace.md)를 참조하세요. 파일에 구성 저장에 대한 자세한 내용은 [작업 영역 구성 파일 만들기](how-to-configure-environment.md#workspace)를 참조 하세요.

## <a name="create-a-datastore-for-sample-images"></a>샘플 이미지용 데이터 저장소 만들기

`pipelinedata` 계정의 `sampledata` 퍼블릭 Blob 컨테이너에서 ImageNet 평가 퍼블릭 데이터 샘플을 가져옵니다. `images_datastore`라는 이름의 작업 영역에서 데이터를 사용할 수 있도록 `register_azure_blob_container()`를 호출합니다. 그런 다음, 작업 영역 기본 데이터 저장소를 출력 데이터 저장소로 설정합니다. 출력 데이터 저장소를 사용하여 파이프라인의 출력을 채점합니다.

데이터에 액세스하는 방법에 대한 자세한 내용은 [데이터에 액세스하는 방법](./how-to-access-data.md)을 참조하세요.

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-dataset-objects"></a>데이터 세트 개체 만들기

파이프라인을 빌드하는 경우 `Dataset` 개체는 작업 영역 데이터 저장소에서 데이터를 읽는 데 사용되고, `OutputFileDatasetConfig` 개체는 파이프라인 단계 간에 중간 데이터를 전송하는 데 사용됩니다.

> [!Important]
> 이 자습서의 일괄 처리 채점 예제에서는 하나의 파이프라인 단계만 사용합니다. 여러 단계가 있는 사용 사례의 일반적인 흐름에 포함되는 단계는 다음과 같습니다.
>
> 1. `Dataset` 개체를 *입력* 으로 사용하여 원시 데이터를 가져오고 일부 변환을 수행한 다음, `OutputFileDatasetConfig` 개체로 *출력* 합니다.
>
> 2. 이전 단계의 `OutputFileDatasetConfig` *출력 개체* 를 *입력 개체* 로 사용합니다. 이후 단계에서 이 작업을 반복합니다.

이 시나리오에서는 입력 이미지와 분류 레이블(y-test 값) 모두에 대한 데이터 저장소 디렉터리에 해당하는 `Dataset` 개체를 만듭니다. 일괄 처리 채점 출력 데이터에 대한 `OutputFileDatasetConfig` 개체도 만듭니다.

```python
from azureml.core.dataset import Dataset
from azureml.data import OutputFileDatasetConfig

input_images = Dataset.File.from_files((batchscore_blob, "batchscoring/images/"))
label_ds = Dataset.File.from_files((batchscore_blob, "batchscoring/labels/"))
output_dir = OutputFileDatasetConfig(name="scores")
```

나중에 다시 사용하려면 작업 영역에 데이터 세트를 등록합니다. 이 단계는 선택 사항입니다.

```python

input_images = input_images.register(workspace = ws, name = "input_images")
label_ds = label_ds.register(workspace = ws, name = "label_ds")
```

## <a name="download-and-register-the-model"></a>모델 다운로드 및 등록

파이프라인의 일괄 처리 채점에 사용할 미리 학습된 Tensorflow 모델을 다운로드합니다. 먼저 모델을 저장할 로컬 디렉터리를 만듭니다. 그런 다음, 모델을 다운로드하여 압축을 풉니다.

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

다음으로, 파이프라인 프로세스에서 모델을 쉽게 검색할 수 있도록 해당 모델을 작업 영역에 등록합니다. `register()` 정적 함수에서 `model_name` 매개 변수는 SDK 전체에서 모델을 찾는 데 사용하는 키입니다.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>원격 컴퓨팅 대상 만들기 및 연결

기계 학습 파이프라인은 로컬로 실행할 수 없으므로 클라우드 리소스 또는 *원격 컴퓨팅 대상* 에서 실행합니다. 원격 컴퓨팅 대상은 실험 및 기계 학습 워크플로를 실행하는 재사용 가능한 가상 컴퓨팅 환경입니다. 

다음 코드를 실행하여 GPU 사용 [`AmlCompute`](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute) 대상을 만든 다음, 작업 영역에 연결합니다. 컴퓨팅 대상에 대한 자세한 내용은 [개념 문서](./concept-compute-target.md)를 참조하세요.


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>채점 스크립트 작성

채점을 수행하려면 `batch_scoring.py`라는 일괄 처리 채점 스크립트를 만든 다음, 현재 디렉터리에 씁니다. 이 스크립트는 입력 이미지를 가져와서 분류 모델을 적용한 다음, 예측을 결과 파일에 출력합니다.

`batch_scoring.py` 스크립트는 나중에 만든 `ParallelRunStep`에서 전달되는 다음 매개 변수를 사용합니다.

- `--model_name`: 사용되는 모델의 이름입니다.
- `--labels_dir`: `labels.txt` 파일의 위치입니다.

파이프라인 인프라는 `ArgumentParser` 클래스를 사용하여 매개 변수를 파이프라인 단계에 전달합니다. 예를 들어 다음 코드에서 첫 번째 인수인 `--model_name`에는 `model_name` 속성 식별자가 지정됩니다. `init()` 함수에서 `Model.get_model_path(args.model_name)`는 이 속성에 액세스하는 데 사용됩니다.


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3

from azureml.core import Run
from azureml.core.model import Model
from azureml.core.dataset import Dataset

slim = tf.contrib.slim

image_size = 299
num_channel = 3


def get_class_label_dict(labels_dir):
    label = []
    labels_path = os.path.join(labels_dir, 'labels.txt')
    proto_as_ascii_lines = tf.gfile.GFile(labels_path).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


def init():
    global g_tf_sess, probabilities, label_dict, input_images

    parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
    parser.add_argument('--model_name', dest="model_name", required=True)
    parser.add_argument('--labels_dir', dest="labels_dir", required=True)
    args, _ = parser.parse_known_args()

    label_dict = get_class_label_dict(args.labels_dir)
    classes_num = len(label_dict)

    with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
        input_images = tf.placeholder(tf.float32, [1, image_size, image_size, num_channel])
        logits, _ = inception_v3.inception_v3(input_images,
                                              num_classes=classes_num,
                                              is_training=False)
        probabilities = tf.argmax(logits, 1)

    config = tf.ConfigProto()
    config.gpu_options.allow_growth = True
    g_tf_sess = tf.Session(config=config)
    g_tf_sess.run(tf.global_variables_initializer())
    g_tf_sess.run(tf.local_variables_initializer())

    model_path = Model.get_model_path(args.model_name)
    saver = tf.train.Saver()
    saver.restore(g_tf_sess, model_path)


def file_to_tensor(file_path):
    image_string = tf.read_file(file_path)
    image = tf.image.decode_image(image_string, channels=3)

    image.set_shape([None, None, None])
    image = tf.image.resize_images(image, [image_size, image_size])
    image = tf.divide(tf.subtract(image, [0]), [255])
    image.set_shape([image_size, image_size, num_channel])
    return image


def run(mini_batch):
    result_list = []
    for file_path in mini_batch:
        test_image = file_to_tensor(file_path)
        out = g_tf_sess.run(test_image)
        result = g_tf_sess.run(probabilities, feed_dict={input_images: [out]})
        result_list.append(os.path.basename(file_path) + ": " + label_dict[result[0]])
    return result_list
```

> [!TIP]
> 이 자습서의 파이프라인은 한 단계만 포함하고 있으며 출력을 파일에 기록합니다. 다단계 파이프라인의 경우 `ArgumentParser`도 사용하여 이후 단계에 입력할 출력 데이터를 쓰는 디렉터리를 정의합니다. `ArgumentParser` 디자인 패턴을 사용하여 여러 파이프라인 단계 간에 데이터를 전달하는 예제는 이 [Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb)을 참조하세요.

## <a name="build-the-pipeline"></a>파이프라인 빌드

파이프라인을 실행하기 전에 Python 환경을 정의하고 `batch_scoring.py` 스크립트에 필요한 종속성을 만드는 개체를 만듭니다. 필요한 주요 종속성은 Tensorflow이지만 ParallelRunStep에 필요한 `azureml-core` 및 `azureml-dataprep[fuse]`도 설치합니다. 또한 Docker 및 Docker-GPU 지원도 지정합니다.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.15.2",
                                            "azureml-core", "azureml-dataprep[fuse]"])
env = Environment(name="parallelenv")
env.python.conda_dependencies = cd
env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="create-the-configuration-to-wrap-the-script"></a>스크립트를 래핑하는 구성 만들기

스크립트, 환경 구성 및 매개 변수를 사용하여 파이프라인 단계를 만듭니다. 작업 영역에 이미 연결된 컴퓨팅 대상을 지정합니다.

```python
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    environment=env,
    entry_script="batch_scoring.py",
    source_directory=".",
    output_action="append_row",
    mini_batch_size="20",
    error_threshold=1,
    compute_target=compute_target,
    process_count_per_node=2,
    node_count=1
)
```

### <a name="create-the-pipeline-step"></a>파이프라인 단계 만들기

파이프라인 단계는 다음을 포함하여 파이프라인을 실행하는 데 필요한 모든 항목을 캡슐화하는 개체입니다.

* 환경 및 종속성 설정
* 파이프라인을 실행할 컴퓨팅 리소스
* 입력 및 출력 데이터와 모든 사용자 지정 매개 변수
* 단계 중에 실행할 스크립트 또는 SDK 논리에 대한 참조

여러 클래스가 [`PipelineStep`](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep) 부모 클래스에서 상속됩니다. 특정 프레임워크 또는 스택을 사용하여 단계를 빌드하는 클래스를 선택할 수 있습니다. 다음 예제에서는 사용자 지정 Python 스크립트를 사용하여 단계 논리를 정의하는 `ParallelRunStep` 클래스를 사용합니다. 스크립트에 대한 인수가 단계에 대한 입력 또는 단계의 출력인 경우 해당 인수는 각각 `arguments` 배열 *및*`input` 또는 `output` 매개 변수에 *모두* 정의되어야 합니다. 

둘 이상의 단계가 있는 시나리오에서는 `outputs` 배열의 개체 참조는 이후 파이프라인 단계에 대한 *입력* 으로 사용할 수 있게 됩니다.

```python
from azureml.pipeline.steps import ParallelRunStep
from datetime import datetime

parallel_step_name = "batchscoring-" + datetime.now().strftime("%Y%m%d%H%M")

label_config = label_ds.as_named_input("labels_input")

batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--model_name", "inception",
               "--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
    allow_reuse=False
)
```

다른 단계 유형에 사용할 수 있는 모든 클래스의 목록은 [steps 패키지](/python/api/azureml-pipeline-steps/azureml.pipeline.steps)를 참조하세요.

## <a name="submit-the-pipeline"></a>파이프라인 제출

이제 파이프라인을 실행합니다. 먼저, 만든 작업 영역 참조와 파이프라인 단계를 사용하여 `Pipeline` 개체를 만듭니다. `steps` 매개 변수는 단계의 배열입니다. 여기서는 하나의 일괄 처리 채점 단계만 있습니다. 여러 단계가 있는 파이프라인을 빌드하려면 이 배열에서 단계를 순서대로 배치합니다.

다음으로, `Experiment.submit()` 함수를 사용하여 실행할 파이프라인을 제출합니다. `wait_for_completion` 함수는 파이프라인 빌드 프로세스 중에 로그를 출력합니다. 로그를 사용하여 현재 진행 상황을 파악할 수 있습니다.

> [!IMPORTANT]
> 첫 번째 파이프라인 실행에는 약 *15분* 이 걸립니다. 모든 종속성을 다운로드해야 하고, Docker 이미지를 만들고, Python 환경을 프로비저닝하고 만듭니다. 파이프라인을 다시 실행하면 해당 리소스를 만드는 대신 다시 사용하므로 이 실행에 걸리는 시간이 크게 줄어듭니다. 그러나 파이프라인의 총 실행 시간은 스크립트의 워크로드 및 각 파이프라인 단계에서 실행되는 프로세스에 따라 달라집니다.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'Tutorial-Batch-Scoring').submit(pipeline)
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>출력 다운로드 및 검토

다음 코드를 실행하여 `batch_scoring.py` 스크립트에서 만든 출력 파일을 다운로드합니다. 그런 다음, 채점 결과를 살펴봅니다.

```python
import pandas as pd

batch_run = next(pipeline_run.get_children())
batch_output = batch_run.get_output_data("scores")
batch_output.download(local_path="inception_results")

for root, dirs, files in os.walk("inception_results"):
    for file in files:
        if file.endswith("parallel_run_step.txt"):
            result_file = os.path.join(root, file)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10)
```

## <a name="publish-and-run-from-a-rest-endpoint"></a>REST 엔드포인트에서 게시 및 실행

다음 코드를 실행하여 파이프라인을 작업 영역에 게시합니다. Azure Machine Learning Studio의 작업 영역에서 실행 기록 및 기간을 포함하여 파이프라인에 대한 메타데이터를 볼 수 있습니다. 이 Studio에서 파이프라인을 수동으로 실행할 수도 있습니다.

파이프라인을 게시하면 모든 플랫폼의 모든 HTTP 라이브러리에서 파이프라인을 실행하는 데 사용할 수 있는 REST 엔드포인트를 사용할 수 있습니다.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

REST 엔드포인트에서 파이프라인을 실행하려면 OAuth2 전달자 유형의 인증 헤더가 필요합니다. 다음 예제에서는 설명을 위해 대화형 인증을 사용하지만, 자동화된 인증 또는 헤드리스 인증이 필요한 대부분의 프로덕션 시나리오에서는 [이 문서에서 설명한 대로](how-to-setup-authentication.md) 서비스 주체 인증을 사용합니다.

서비스 주체 인증에는 *앱 등록* 을 *Azure Active Directory* 에 만드는 작업이 포함됩니다. 먼저 클라이언트 비밀을 생성한 다음, 서비스 주체 *역할 액세스* 권한을 기계 학습 작업 영역에 부여합니다. 인증 흐름은 [`ServicePrincipalAuthentication`](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication) 클래스를 사용하여 관리합니다. 

[`InteractiveLoginAuthentication`](/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication) 및 `ServicePrincipalAuthentication`은 모두 `AbstractAuthentication`에서 상속됩니다. 두 경우 모두 [`get_authentication_header()`](/python/api/azureml-core/azureml.core.authentication.abstractauthentication#get-authentication-header--) 함수를 동일한 방식으로 사용하여 헤더를 가져옵니다.

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

REST URL은 게시된 파이프라인 개체의 `endpoint` 속성에서 가져옵니다. 또한 이 REST URL은 Azure Machine Learning Studio의 작업 영역에서도 찾을 수 있습니다. 

엔드포인트에 대한 HTTP POST 요청을 빌드합니다. 인증 헤더를 요청에 지정합니다. 실험 이름이 있는 JSON 페이로드 개체를 추가합니다.

실행을 트리거하도록 요청합니다. 실행 ID의 값을 가져오기 위해 응답 사전에서 `Id` 키에 액세스하는 코드를 포함시킵니다.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "Tutorial-Batch-Scoring",
                               "ParameterAssignments": {"process_count_per_node": 6}})
run_id = response.json()["Id"]
```

실행 ID를 사용하여 새 실행의 상태를 모니터링합니다. 새 실행을 완료하는 데 또 다른 10-15분이 걸립니다. 

새 실행은 자습서의 앞부분에서 실행한 파이프라인과 비슷합니다. 전체 출력을 표시하지 않도록 선택할 수 있습니다.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["Tutorial-Batch-Scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>리소스 정리

다른 Azure Machine Learning 자습서를 실행하려면 이 섹션을 수행하지 마세요.

### <a name="stop-the-compute-instance"></a>컴퓨팅 인스턴스 중지

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>모든 항목 삭제

자신이 만든 리소스를 사용하지 않으려는 경우 요금이 발생하지 않도록 삭제하세요.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
1. 리소스 그룹 목록에서 만든 리소스 그룹을 선택합니다.
1. **리소스 그룹 삭제** 를 선택합니다.
1. 리소스 그룹 이름을 입력합니다. 그런 다음, **삭제** 를 선택합니다.

또한 리소스 그룹을 유지하면서 단일 작업 영역을 삭제할 수도 있습니다. 작업 영역 속성을 표시한 다음, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 기계 학습 파이프라인 자습서에서는 다음 작업을 수행했습니다.

> [!div class="checklist"]
> * 원격 GPU 컴퓨팅 리소스에서 실행할 환경 종속성이 있는 파이프라인을 빌드했습니다.
> * 미리 학습된 Tensorflow 모델을 사용하여 일괄 처리 예측을 실행하는 채점 스크립트를 만들었습니다.
> * 파이프라인을 게시하고 REST 엔드포인트에서 실행하도록 설정했습니다.

기계 학습 SDK를 사용하여 파이프라인을 빌드하는 방법에 대한 자세한 예제는 [Notebook 리포지토리](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)를 참조하세요.
