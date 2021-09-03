---
title: TensorFlow 모델 학습 및 배포
titleSuffix: Azure Machine Learning
description: Azure Machine Learning을 통해 탄력적 클라우드 컴퓨팅 리소스를 사용하여 TensorFlow 학습 작업을 스케일 아웃하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.date: 09/28/2020
ms.topic: how-to
ms.openlocfilehash: 8e53d67dacff8337d4a6832fc5febe3b83ec6126
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606829"
---
# <a name="train-tensorflow-models-at-scale-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 대규모로 TensorFlow 모델 학습

이 문서에서는 Azure Machine Learning을 사용하여 대규모로 [TensorFlow](https://www.tensorflow.org/overview) 학습 스크립트를 실행하는 방법에 대해 알아봅니다.

이 예제에서는 DNN(심층 신경망)을 사용하여 필기 숫자를 분류하기 위해 TensorFlow 모델을 학습하고 등록합니다.

처음부터 TensorFlow 모델을 개발하든, [기존 모델](how-to-deploy-and-where.md)을 클라우드로 가져오든 관계없이 Azure Machine Learning을 사용해 오픈 소스 학습 작업을 스케일 아웃하여 프로덕션 등급 모델을 빌드, 배포, 버전 관리, 모니터링할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

다음 환경 중 하나에서 이 코드를 실행합니다.

 - Azure Machine Learning 컴퓨팅 인스턴스 - 다운로드 또는 설치 필요 없음

     - [빠른 시작: Azure Machine Learning 시작하기](quickstart-create-resources.md)를 완료하여 SDK 및 샘플 리포지토리가 미리 로드된 전용 Notebook 서버를 만듭니다.
    - Notebook 서버의 딥 러닝 폴더 샘플에서 **how-to-use-azureml > ml-frameworks > tensorflow > train-hyperparameter-tune-deploy-with-tensorflow** 폴더 디렉터리로 이동하여 완료된 확장 Notebook을 찾습니다. 
 
 - 사용자 고유의 Jupyter Notebook 서버

    - [Azure Machine Learning SDK(1.15.0 이상)를 설치합니다](/python/api/overview/azure/ml/install).
    - [작업 영역 구성 파일을 만듭니다](how-to-configure-environment.md#workspace).
    - [샘플 스크립트 파일(`tf_mnist.py` 및 `utils.py`)을 다운로드합니다](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow).
     
    GitHub 샘플 페이지에서 이 가이드의 완료된 [Jupyter Notebook 버전](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb)을 찾을 수도 있습니다. Notebook에는 지능형 하이퍼 매개 변수 튜닝, 모델 배포 및 Notebook 위젯을 다루는 확장 섹션이 포함되어 있습니다.

## <a name="set-up-the-experiment"></a>실험 설정

이 섹션에서는 필요한 Python 패키지를 로드하고, 작업 영역을 초기화하며, 컴퓨팅 대상을 만들고, 학습 환경을 정의하여 학습 실험을 설정합니다.

### <a name="import-packages"></a>패키지 가져오기

먼저, 필요한 Python 라이브러리를 가져옵니다.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core import Environment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>작업 영역 초기화

[Azure Machine Learning 작업 영역](concept-workspace.md)은 서비스의 최상위 리소스입니다. 사용자가 만드는 모든 아티팩트를 사용할 수 있는 중앙 집중식 환경을 제공합니다. Python SDK에서 [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) 개체를 만들어 작업 영역 아티팩트에 액세스할 수 있습니다.

[사전 요구 사항 섹션](#prerequisites)에서 만든 `config.json` 파일에서 작업 영역 개체를 만듭니다.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>파일 데이터 세트 만들기

`FileDataset` 개체는 작업 영역 데이터 저장소 또는 공용 URL에서 하나 이상의 파일을 참조합니다. 파일은 모든 형식일 수 있으며, 클래스는 컴퓨팅에 파일을 다운로드하거나 탑재하는 기능을 제공합니다. `FileDataset`를 생성하여 데이터 원본 위치에 대한 참조를 만듭니다. 데이터 세트에 변환을 적용한 경우 데이터 세트에도 저장됩니다. 데이터는 기존 위치에 그대로 남아 있으므로 추가 스토리지 비용이 발생하지 않습니다. 자세한 내용은 `Dataset` 패키지에 대한 [방법](./how-to-create-register-datasets.md) 가이드를 참조하세요.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

`register()` 메서드를 사용하여 작업 영역에 데이터 세트를 등록합니다. 이렇게 하면 데이터 세트를 다른 사용자와 공유하고, 다양한 실험에서 재사용하고, 학습 스크립트에서 이름으로 참조할 수 있습니다.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

### <a name="create-a-compute-target"></a>컴퓨팅 대상 만들기

TensorFlow 작업을 실행할 컴퓨팅 대상을 만듭니다. 이 예제에서는 GPU 지원 Azure Machine Learning 컴퓨팅 클러스터를 만듭니다.

```Python
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

컴퓨팅 대상에 대한 자세한 내용은 [컴퓨팅 대상이란?](concept-compute-target.md) 문서를 참조하세요.

### <a name="define-your-environment"></a>환경 정의

학습 스크립트의 종속성을 캡슐화하는 Azure ML [환경](concept-environments.md)을 정의하려면 사용자 지정 환경을 정의하거나 Azure ML 큐레이팅된 환경을 사용할 수 있습니다.

#### <a name="use-a-curated-environment"></a>큐레이팅된 환경 사용
사용자 고유의 환경을 정의하지 않으려는 경우 Azure ML에서 미리 빌드된 큐레이팅된 환경을 제공합니다. Azure ML에는 여러 버전의 TensorFlow에 해당하는 TensorFlow에 대한 몇 가지 CPU 및 GPU 큐레이팅된 환경이 있습니다. 자세한 내용은 [여기](resource-curated-environments.md)를 참조하세요.

큐레이팅된 환경을 사용하려면 다음 명령을 대신 실행할 수 있습니다.

```python
curated_env_name = 'AzureML-TensorFlow-2.2-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
```

큐레이팅된 환경에 포함된 패키지를 보려면 conda 종속성을 디스크에 쓸 수 있습니다.
```python
tf_env.save_to_directory(path=curated_env_name)
```

학습 스크립트에 필요한 모든 종속성이 큐레이팅된 환경에 포함되어 있는지 확인합니다. 그렇지 않은 경우 누락된 종속성을 포함하도록 환경을 수정해야 합니다. 환경이 수정되면 'AzureML' 접두사가 큐레이팅된 환경용으로 예약되어 있으므로 새 이름을 지정해야 합니다. conda 종속성 YAML 파일을 수정한 경우 다음과 같이 새 이름을 사용하여 새 환경을 만들 수 있습니다.
```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')
```

대신 큐레이팅된 환경 개체를 직접 수정한 경우 새 이름을 사용하여 해당 환경을 복제할 수 있습니다.
```python
tf_env = tf_env.clone(new_name='tensorflow-2.2-gpu')
```

#### <a name="create-a-custom-environment"></a>사용자 지정 환경 만들기

학습 스크립트의 종속성을 캡슐화하는 Azure ML 환경을 직접 만들 수도 있습니다.

먼저 YAML 파일에 conda 종속성을 정의합니다. 다음 예제에서 파일 이름은 `conda_dependencies.yml`입니다.

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
```

이 conda 환경 사양에서 Azure ML 환경을 만듭니다. 환경은 런타임에 Docker 컨테이너로 패키지화됩니다.

기본 이미지가 지정되지 않은 경우에는 Azure ML은 기본적으로 CPU 이미지 `azureml.core.environment.DEFAULT_CPU_IMAGE`를 기본 이미지로 사용합니다. 다음 예제에서는 GPU 클러스터에서 학습을 실행하므로 필요한 GPU 드라이버 및 종속성이 있는 GPU 기본 이미지를 지정해야 합니다. Azure ML은 사용할 수 있는 MCR(Microsoft Container Registry)에 게시된 기본 이미지 세트를 유지 관리합니다. 자세한 내용은 [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) GitHub 리포지토리를 참조하세요.

```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
tf_env.docker.enabled = True
tf_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> 필요에 따라 사용자 지정 Docker 이미지 또는 Docker 파일에서 모든 종속성을 직접 캡처하고 여기서 환경을 만들 수 있습니다. 자세한 내용은 [사용자 지정 이미지를 사용하여 학습](how-to-train-with-custom-image.md)을 참조하세요.

환경을 만들고 사용하는 방법에 대한 자세한 내용은 [Azure Machine Learning에서 소프트웨어 환경 만들기 및 사용](how-to-use-environments.md)을 참조하세요.

## <a name="configure-and-submit-your-training-run"></a>학습 실행 구성 및 제출

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig 만들기

[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) 개체를 만들어 학습 스크립트, 사용할 환경 및 실행할 컴퓨팅 대상 등 학습 작업의 구성 세부 정보를 지정합니다. `arguments` 매개 변수에 지정되면 학습 스크립트에 대한 모든 인수가 명령줄을 통해 전달됩니다.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 64,
        '--first-layer-neurons', 256,
        '--second-layer-neurons', 128,
        '--learning-rate', 0.01]

src = ScriptRunConfig(source_directory=script_folder,
                      script='tf_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
```

> [!WARNING]
> Azure Machine Learning는 전체 원본 디렉터리를 복사하여 학습 스크립트를 실행합니다. 업로드하지 않으려는 중요한 데이터가 있는 경우 [.ignore 파일](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots)을 사용하거나 데이터를 원본 디렉터리에 포함하지 마세요. 대신 Azure ML [데이터 세트](how-to-train-with-datasets.md)를 사용하여 데이터에 액세스합니다.

ScriptRunConfig를 사용하여 작업을 구성하는 방법에 대한 자세한 내용은 [학습 실행 구성 및 제출](how-to-set-up-training-targets.md)을 참조하세요.

> [!WARNING]
> 이전에 TensorFlow 예측 도구를 사용하여 TensorFlow 학습 작업을 구성한 경우, 이 예측 도구는 1.19.0 SDK 릴리스부터 더 이상 사용되지 않습니다. Azure ML SDK 1.15.0 이상을 사용하는 경우 ScriptRunConfig는 딥 러닝 프레임워크를 사용하는 작업을 포함하여 학습 작업을 구성하는 데 권장되는 방법입니다. 마이그레이션에 대한 일반적인 질문은 [예측 도구에서 ScriptRunConfig로 마이그레이션 가이드](how-to-migrate-from-estimators-to-scriptrunconfig.md)를 참조하세요.

### <a name="submit-a-run"></a>실행 제출

[Run 개체](/python/api/azureml-core/azureml.core.run%28class%29)는 작업이 실행되는 동안 그리고 작업이 완료된 후 실행 기록에 대한 인터페이스를 제공합니다.

```Python
run = Experiment(workspace=ws, name='Tutorial-TF-Mnist').submit(src)
run.wait_for_completion(show_output=True)
```
### <a name="what-happens-during-run-execution"></a>Run을 실행하는 중에 수행되는 작업
Run이 실행되면 다음 단계를 거칩니다.

- **준비**: 정의된 환경에 따라 Docker 이미지가 생성됩니다. 이미지는 작업 영역의 컨테이너 레지스트리에 업로드되고 나중에 실행될 수 있도록 캐시됩니다. 또한 로그는 실행 기록으로 스트리밍되며 진행 상황을 모니터링할 수 있도록 표시됩니다. 큐레이팅된 환경이 대신 지정되면 해당 큐레이팅된 환경을 지원하는 캐시된 이미지가 사용됩니다.

- **스케일링**: Batch AI 클러스터에서 Run을 실행하는 데 현재 사용할 수 있는 노드보다 더 많은 노드가 필요한 경우 클러스터에서 스케일 업을 시도합니다.

- **실행**: 스크립트 폴더의 모든 스크립트가 컴퓨팅 대상으로 업로드되고, 데이터 저장소가 탑재되거나 복사되며, `script`가 실행됩니다. stdout 및 **./logs** 폴더의 출력은 실행 기록으로 스트리밍되며 Run을 모니터링하는 데 사용될 수 있습니다.

- **후 처리**: 실행의 **./outputs** 폴더가 실행 기록에 복사됩니다.

## <a name="register-or-download-a-model"></a>모델 등록 또는 다운로드

모델이 학습되면 해당 모델을 작업 영역에 등록할 수 있습니다. 모델 등록을 사용하면 모델을 작업 영역에 저장하고 버전을 지정하여 [모델 관리 및 배포](concept-model-management-and-deployment.md)를 간소화할 수 있습니다. 선택 사항: 매개 변수 `model_framework`, `model_framework_version`, `resource_configuration`을 지정하면 코드가 없는 모델 배포를 사용할 수 있게 됩니다. 이렇게 하면 모델을 등록된 모델에서 웹 서비스로 직접 배포할 수 있으며 `ResourceConfiguration` 개체는 웹 서비스의 컴퓨팅 리소스를 정의합니다.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='2.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Run 개체를 사용하여 모델의 로컬 복사본을 다운로드할 수도 있습니다. 학습 스크립트 `tf_mnist.py`에서 TensorFlow 저장기 개체는 모델을 로컬 폴더(컴퓨팅 대상의 로컬 폴더)에 유지합니다. Run 개체를 사용하여 복사본을 다운로드할 수 있습니다.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)
run.download_files(prefix='outputs/model', output_directory='./model', append_prefix=False)
```

## <a name="distributed-training"></a>분산 학습

Azure Machine Learning은 학습 워크로드를 스케일링할 수 있도록 다중 노드 분산 TensorFlow 작업도 지원합니다. 분산 TensorFlow 작업을 쉽게 실행할 수 있으며, Azure ML에서 오케스트레이션을 관리합니다.

Azure ML에서는 Horovod 및 TensorFlow의 기본 제공 분산 학습 API를 사용하여 분산 TensorFlow 작업을 실행할 수 있습니다.

분산 학습에 대한 자세한 내용은 [분산 GPU 학습 가이드](how-to-train-distributed-gpu.md)를 참조하세요.

## <a name="deploy-a-tensorflow-model"></a>TensorFlow 모델 배포

배포 방법에는 모델 등록에 대한 섹션이 포함되어 있지만 이미 등록된 모델이 있으므로 배포에 대한 [컴퓨팅 대상 만들기](how-to-deploy-and-where.md#choose-a-compute-target)로 직접 건너뛸 수 있습니다.

### <a name="preview-no-code-model-deployment"></a>(미리 보기) 코드가 없는 모델 배포

기존 배포 경로 대신 TensorFlow에 코드가 없는 배포 기능(미리 보기)을 사용할 수도 있습니다. `model_framework`, `model_framework_version`, `resource_configuration` 매개 변수를 사용하여 위와 같이 모델을 등록하면 간단히 `deploy()` 정적 함수를 사용하여 모델을 배포할 수 있습니다.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

전체 [사용 방법](how-to-deploy-and-where.md)에서는 Azure Machine Learning에서 배포를 자세히 다룹니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 TensorFlow 모델을 학습하고 등록했으며 배포 옵션에 대해 알아봤습니다. Azure Machine Learning에 대한 자세한 내용은 다음 문서를 참조하세요.

* [학습 중에 실행 메트릭 추적](how-to-log-view-metrics.md)
* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)
* [Azure에서 분산 딥 러닝 학습의 참조 아키텍처](/azure/architecture/reference-architectures/ai/training-deep-learning)
