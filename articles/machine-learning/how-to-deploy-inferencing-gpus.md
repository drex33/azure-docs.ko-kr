---
title: GPU를 사용하여 유추를 위한 모델 배포
titleSuffix: Azure Machine Learning
description: 이 문서에서는 Azure Machine Learning을 사용하여 GPU 사용 TensorFlow 딥 러닝 모델을 웹 서비스로 배포하고 유추 요청을 채점하는 방법을 설명합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/17/2020
ms.topic: how-to
ms.custom: devx-track-python, deploy
ms.openlocfilehash: eec009303938195fffe6c2ddb0ad6d44a2e1a8b0
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129425395"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>GPU를 사용하여 유추를 위한 딥 러닝 모델 배포


이 문서에서는 Azure Machine Learning을 사용하여 GPU 사용 모델을 웹 서비스로 배포하는 방법을 설명합니다. 이 문서에 수록된 정보는 AKS(Azure Kubernetes Service)에 모델 배포를 기반으로 합니다. AKS 클러스터는 유추를 위한 모델에서 사용하는 GPU 리소스를 제공합니다.

유추 또는 모델 채점은 배포된 모델을 사용하여 예측하는 단계입니다. CPU 대신 GPU를 사용하면 병렬 처리 가능한 계산에서 성능상의 이점이 있습니다.

[!INCLUDE [endpoints-option](../../includes/machine-learning-endpoints-preview-note.md)]

> [!IMPORTANT]
> 웹 서비스 배포의 경우, GPU 유추는 Azure Kubernetes Service에서만 지원됩니다. __기계 학습 파이프라인__ 을 사용하는 유추의 경우 GPU는 Azure Machine Learning 컴퓨팅에서만 지원됩니다. ML 파이프라인 사용에 대한 자세한 내용은 [자습서: 일괄 처리 채점용 Azure Machine Learning 파이프라인 빌드](tutorial-pipeline-batch-scoring-classification.md)를 참조하세요. 

> [!TIP]
> 이 문서의 코드 조각에서는 TensorFlow 모델을 사용하지만 GPU를 지원하는 모든 기계 학습 프레임워크에 정보를 적용할 수 있습니다.

> [!NOTE]
> 이 문서에 수록된 정보는 [Azure Kubernetes Service에 배포하는 방법](how-to-deploy-azure-kubernetes-service.md) 문서에 있는 정보를 기반으로 합니다. 해당 문서에서는 일반적으로 AKS에 대한 배포에 대해 다루고, 이 문서에서는 GPU 특정 배포에 대해 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

* Azure Machine Learning SDK가 설치된 Python 개발 환경입니다. 자세한 내용은 [Azure Machine Learning SDK](/python/api/overview/azure/ml/install)를 참조하세요.  

* GPU를 사용하는 등록된 모델.

    * 모델을 등록하는 방법은 [모델 배포](how-to-deploy-and-where.md#registermodel)를 참조하세요.

    * 이 문서를 작성하는 데 사용되는 TensorFlow 모델을 만들고 등록하려면 [TensorFlow 모델 학습 방법](how-to-train-tensorflow.md)을 참조하세요.

* [모델 배포 방법 및 위치](how-to-deploy-and-where.md)에 대한 일반적인 이해.

## <a name="connect-to-your-workspace"></a>작업 영역에 연결

기존 작업 영역에 연결하려면 다음 코드를 사용합니다.

> [!IMPORTANT]
> 이 코드 조각은 작업 영역 구성이 현재 디렉터리 또는 부모 디렉터리에 저장될 것으로 예상합니다. 작업 영역 만들기에 대한 자세한 내용은 [Azure Machine Learning 작업 영역 만들기 및 관리](how-to-manage-workspace.md)를 참조하세요.   파일에 구성 저장에 대한 자세한 내용은 [작업 영역 구성 파일 만들기](how-to-configure-environment.md#workspace)를 참조 하세요.

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>AKS로 Kubernetes 클러스터 만들기

Azure Kubernetes Service는 다양한 GPU 옵션을 제공합니다. 모델 유추에 해당 옵션 중 하나를 사용할 수 있습니다. 기능 및 비용에 대한 전체 분석은 [N 시리즈 VM 목록](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series)을 참조하세요.

다음 코드에서는 작업 영역에 대한 새 AKS 클러스터를 만드는 방법을 보여 줍니다.

```python
from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Azure는 AKS 클러스터가 있는 한 요금을 청구합니다. 작업을 완료하면 AKS 클러스터를 삭제해야 합니다.

Azure Machine Learning과 함께 AKS를 사용하는 방법은 [Azure Kubernetes Service에 배포하는 방법](how-to-deploy-azure-kubernetes-service.md)을 참조하세요.

## <a name="write-the-entry-script"></a>항목 스크립트 쓰기

항목 스크립트는 웹 서비스에 제출된 데이터를 수신하고, 모델에 전달하고, 채점 결과를 반환합니다. 다음 스크립트는 시작 시 TensorFlow 모델을 로드한 다음 모델을 사용해 데이터를 채점합니다.

> [!TIP]
> 항목 스크립트는 모델에 따라 다릅니다. 예를 들어 스크립트는 모델, 데이터 형식 등에 사용할 프레임워크를 알고 있어야 합니다.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = os.getenv('AZUREML_MODEL_DIR')
    # the name of the folder in which to look for tensorflow model files
    tf_model_folder = 'model'
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, tf_model_folder, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, tf_model_folder, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

이 파일의 이름은 `score.py`입니다. 항목 스크립트에 대한 자세한 내용은 [배포 방법 및 위치](how-to-deploy-and-where.md)를 참조하세요.

## <a name="define-the-conda-environment"></a>Conda 환경 정의

Conda 환경 파일은 서비스에 대한 종속성을 지정합니다. 여기에는 모델과 항목 스크립트 모두에 필요한 종속성이 포함됩니다. 버전 1.0.45 이상의 azureml-defaults를 PIP 종속성으로 표시해야 합니다. 모델을 웹 서비스로 호스트하는 데 필요한 기능이 포함되어 있기 때문입니다. 다음 YAML은 TensorFlow 모델에 대한 환경을 정의합니다. 이 배포에 사용되는 GPU를 활용하는 `tensorflow-gpu`를 지정합니다.

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  # You must list azureml-defaults as a pip dependency
  - azureml-defaults>=1.0.45
  - numpy
  - tensorflow-gpu=1.12
channels:
- conda-forge
```

이 예에서는 파일이 `myenv.yml`로 저장됩니다.

## <a name="define-the-deployment-configuration"></a>배포 구성 정의

> [!IMPORTANT]
> AKS는 Pod가 GPU를 공유하는 것을 허용하지 않으며, 클러스터에 있는 GPU 만큼만 GPU 사용 웹 서비스의 복제본을 사용할 수 있습니다.

배포 구성은 웹 서비스를 실행하는 데 사용되는 Azure Kubernetes Service 환경을 정의합니다.

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

자세한 내용은 [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-)에 대한 참조 설명서를 확인하세요.

## <a name="define-the-inference-configuration"></a>유추 구성 정의

유추 구성은 GPU가 지원되는 Docker 이미지를 사용하는 항목 스크립트와 환경 개체를 가리킵니다. 환경 정의에 사용되는 YAML 파일에는 버전 1.0.45 이상의 azureml-defaults가 PIP 종속성으로 나열되어야 합니다. 모델을 웹 서비스로 호스트하는 데 필요한 기능이 포함되어 있기 때문입니다.

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

환경에 대한 자세한 내용은 [학습 및 배포 환경 만들기 및 관리](how-to-use-environments.md)를 참조하세요.
자세한 내용은 [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig)에 대한 참조 설명서를 확인하세요.

## <a name="deploy-the-model"></a>모델 배포

AKS 클러스터에 모델을 배포하고 서비스를 만들 때까지 기다립니다.

```python
from azureml.core.model import Model

# Name of the web service that is deployed
aks_service_name = 'aks-dnn-mnist'
# Get the registerd model
model = Model(ws, "tf-dnn-mnist")
# Deploy the model
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

자세한 내용은 [모델](/python/api/azureml-core/azureml.core.model.model)에 대한 참조 설명서를 확인하세요.

## <a name="issue-a-sample-query-to-your-service"></a>서비스에 대한 샘플 쿼리 실행

배포된 모델에 테스트 쿼리를 보냅니다. jpeg 이미지를 모델로 보내면 이미지가 채점됩니다. 다음 코드 샘플에서는 테스트 데이터를 다운로드한 다음 서비스에 보낼 임의의 테스트 이미지를 선택합니다.

```python
# Used to test your webservice
import os
import urllib
import gzip
import numpy as np
import struct
import requests

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res

# one-hot encode a 1-D array
def one_hot_encode(array, num_of_classes):
    return np.eye(num_of_classes)[array.reshape(-1)]

# Download test data
os.makedirs('./data/mnist', exist_ok=True)
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/mnist/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/mnist/test-labels.gz')

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type': 'application/json',
           'Authorization': ('Bearer ' + api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

클라이언트 애플리케이션을 만드는 방법은 [배포된 웹 서비스를 사용하는 클라이언트 만들기](how-to-consume-web-service.md)를 참조하세요.

## <a name="clean-up-the-resources"></a>리소스 정리

이 예를 위해 특별히 AKS 클러스터를 만든 경우 완료 후 리소스를 삭제합니다.

> [!IMPORTANT]
> Azure는 AKS 클러스터의 배포 기간에 따라 요금을 청구합니다. 작업을 완료한 후에는 정리해야 합니다.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>다음 단계

* [FPGA에 모델 배포](how-to-deploy-fpga-web-service.md)
* [ONNX를 사용하여 모델 배포](concept-onnx.md#deploy-onnx-models-in-azure)
* [TensorFlow DNN 모델 학습](how-to-train-tensorflow.md)