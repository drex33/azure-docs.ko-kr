---
title: 사용자 지정 Docker 이미지를 사용하여 모델 학습
titleSuffix: Azure Machine Learning
description: 사용자 고유의 Docker 이미지 또는 Microsoft에서 큐레이팅된 이미지를 사용하여 Azure Machine Learning의 모델을 학습하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 08/11/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 63f19272d5cc518ced0aaee5c01d61238eaf9cef
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132756479"
---
# <a name="train-a-model-by-using-a-custom-docker-image"></a>사용자 지정 Docker 이미지를 사용하여 모델 학습

이 문서에서는 Azure Machine Learning를 사용하여 모델을 학습하는 경우 사용자 지정 Docker 이미지를 사용하는 방법에 대해 알아봅니다. 이 문서의 예제 스크립트를 사용하여 나선형 신경망을 만들어 pet 이미지를 분류합니다. 

Azure Machine Learning은 기본 Docker 기본 이미지를 제공합니다. Azure Machine Learning 환경을 사용하여 관리된 [Azure Machine Learning 다른 기본 이미지](https://github.com/Azure/AzureML-Containers) 중 하나 또는 사용자의 고유한 [사용자 지정 이미지](./how-to-deploy-custom-container.md)를 지정할 수도 있습니다. 사용자 지정 기반 이미지를 사용하면 종속성을 면밀히 관리하며 학습 작업을 실행할 때 구성 요소 버전을 통해 긴밀한 제어를 유지할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이러한 환경 중 하나에서 코드를 실행합니다.

* Azure Machine Learning 컴퓨팅 인스턴스(다운로드 또는 설치 필요 없음):
  * [빠른 시작: Azure Machine Learning 시작하기](quickstart-create-resources.md) 자습서를 완료하여 SDK 및 샘플 리포지토리가 미리 로드된 전용 Notebook 서버를 만듭니다.
* 사용자 고유의 Jupyter Notebook 서버:
  * [작업 영역 구성 파일](how-to-configure-environment.md#workspace)을 만듭니다.
  * [Azure Machine Learning SDK](/python/api/overview/azure/ml/install)를 설치합니다. 
  * 인터넷에서 액세스할 수 있는 [Azure 컨테이너 레지스트리](../container-registry/index.yml) 또는 기타 Docker 레지스트리를 만듭니다.

## <a name="set-up-a-training-experiment"></a>학습 실험 설정

이 섹션에서는 작업 영역을 초기화하고, 환경을 정의하고, 컴퓨팅 대상을 구성하여 학습 실험을 설정합니다.

### <a name="initialize-a-workspace"></a>작업 영역 초기화

[Azure Machine Learning 작업 영역](concept-workspace.md)은 서비스의 최상위 리소스입니다. 사용자가 만드는 모든 아티팩트로 작업할 수 있는 중앙 집중식 환경을 제공합니다. Python SDK에서 [`Workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) 개체를 만들어 작업 영역 아티팩트에 액세스할 수 있습니다.

[사전 요구 사항](#prerequisites)으로 만든 config.json 파일에서 `Workspace` 개체를 만듭니다.

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="define-your-environment"></a>환경 정의

`Environment` 개체 만들기

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
```

다음 코드에서 지정된 기본 이미지는 분산 딥러닝 기능을 허용하는 fast.ai 라이브러리를 지원합니다. 자세한 내용은 [fast.ai Docker Hub 리포지토리](https://hub.docker.com/u/fastdotai)를 참조하세요. 

사용자 지정 Docker 이미지를 사용하는 경우 Python 환경이 이미 제대로 설정되어 있을 수 있습니다. 이 경우 `user_managed_dependencies` 플래그를 `True`로 설정하여 사용자 지정 이미지의 기본 제공 Python 환경을 사용합니다. 기본적으로 Azure Machine Learning은 사용자가 지정한 종속성이 있는 Conda 환경을 구축합니다. 서비스는 기본 이미지에 설치한 Python 라이브러리를 사용하는 대신 해당 환경에서 스크립트를 실행합니다.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

#### <a name="use-a-private-container-registry-optional"></a>프라이빗 컨테이너 레지스트리 사용(선택 사항)

작업 영역에 없는 비공개 컨테이너 레지스트리에서 이미지를 사용하려면 다음과 같이 `docker.base_image_registry`를 사용하여 리포지토리의 주소 및 사용자 이름과 암호를 지정합니다.

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

#### <a name="use-a-custom-dockerfile-optional"></a>사용자 지정 Dockerfile 사용(선택 사항)

사용자 지정 Dockerfile도 사용할 수 있습니다. Python이 아닌 패키지를 종속성으로 설치해야 하는 경우 이 방법을 사용합니다. 기본 이미지를 `None`으로 설정해야 합니다.

```python 
# Specify Docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210615.v1
RUN echo "Hello from custom container!"
"""

# Set the base image to None, because the image is defined by Dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

>[!IMPORTANT]
> Azure Machine Learning은 다음 소프트웨어를 제공하는 Docker 이미지만 지원합니다.
> * Ubuntu 18.04 이상
> * Conda 4.7.# 이상
> * Python 3.6 이상.
> * /bin/sh에서 사용할 수 있는 POSIX 규격 셸은 학습에 사용되는 모든 컨테이너 이미지에 필요합니다. 

Azure Machine Learning 환경을 만들고 관리하는 방법에 대한 자세한 내용은 [소프트웨어 환경 만들기 및 사용](how-to-use-environments.md)을 참조하세요. 

### <a name="create-or-attach-a-compute-target"></a>컴퓨팅 대상 만들기 또는 연결

모델 학습을 위한 [컴퓨팅 대상](concept-azure-machine-learning-architecture.md#compute-targets)을 만들어야 합니다. 이 자습서에서는 학습 컴퓨팅 리소스로 `AmlCompute`를 만듭니다.

`AmlCompute`를 만드는 데 몇 분 정도 걸립니다. 작업 영역에 이미 `AmlCompute` 리소스가 있는 경우 이 코드는 만들기 프로세스를 건너뜁니다.

다른 Azure 서비스와 마찬가지로, Azure Machine Learning 서비스와 연결된 특정 리소스(예: `AmlCompute`)에 대한 한도가 있습니다. 자세한 내용은 [기본 한도 및 더 높은 할당량을 요청하는 방법](how-to-manage-quotas.md)을 참조하세요.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your cluster.
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # Create the cluster.
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# Use get_status() to get a detailed status for the current AmlCompute.
print(compute_target.get_status().serialize())
```


>[!IMPORTANT]
>컴퓨팅 기반 이미지 빌드에 CPU SKU를 사용합니다. 


## <a name="configure-your-training-job"></a>학습 작업 구성

이 자습서의 경우 [GitHub](https://github.com/Azure/azureml-examples/blob/main/python-sdk/workflows/train/fastai/pets/src/train.py)에서 학습 스크립트 *train.py* 를 사용합니다. 실제로 사용자 지정 학습 스크립트를 사용하고 Azure Machine Learning을 사용하여 실행할 수 있습니다.

원하는 [컴퓨팅 대상](how-to-set-up-training-targets.md)에서 실행할 작업을 구성하는 `ScriptRunConfig` 리소스를 만듭니다.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

## <a name="submit-your-training-job"></a>학습 작업 제출

`ScriptRunConfig` 개체를 사용하여 학습 실행을 제출하는 경우 `submit` 메서드는 `ScriptRun` 형식의 개체를 반환합니다. 반환된 `ScriptRun` 개체는 학습 실행에 대한 정보에 프로그래밍 방식의 액세스를 제공합니다. 

```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-fastai').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning은 전체 소스 디렉터리를 복사하여 학습 스크립트를 실행합니다. 업로드를 원하지 않는 중요한 데이터가 있는 경우 [.ignore 파일](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots)을 사용하거나 데이터를 소스 디렉터리에서 제외합니다. 대신 [데이터 저장소](/python/api/azureml-core/azureml.data)를 사용하여 데이터에 액세스합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 사용자 지정 Docker 이미지를 사용하여 모델을 학습했습니다. Azure Machine Learning에 대한 자세한 내용은 다음 문서를 참조하세요.
* 학습 중에 [메트릭 실행 추적](how-to-log-view-metrics.md)
* 사용자 지정 Docker 이미지를 사용하여 [모델을 배포](./how-to-deploy-custom-container.md)합니다.
