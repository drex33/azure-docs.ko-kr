---
title: 큐레이팅 환경
titleSuffix: Azure Machine Learning
description: 실험 및 배포 준비 시간을 줄이는 데 도움이 되는 미리 구성된 환경 세트인 Azure Machine Learning 큐레이팅된 환경에 대해 알아봅니다.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.date: 07/08/2021
ms.openlocfilehash: d86abb52b5282c70e99abc68da75c4b5f85ecbb8
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712074"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning 큐레이팅된 환경

이 문서에서는 Azure Machine Learning의 최신 프레임 워크 버전이 포함 된 큐 레이트 환경을 나열 합니다. 큐레이팅된 환경은 Azure Machine Learning에서 제공하며 기본적으로 작업 영역에서 사용할 수 있습니다. 최신 버전의 Azure Machine Learning SDK를 사용하는 캐시된 Docker 이미지에서 지원되므로 실행 준비 비용을 줄이고 배포 시간을 단축할 수 있습니다. 이러한 환경을 사용하여 다양한 기계 학습 프레임워크를 빠르게 시작할 수 있습니다.

> [!NOTE]
> [Python SDK](how-to-use-environments.md), [CLI](/cli/azure/ml/environment?view=azure-cli-latest&preserve-view=true#az_ml_environment_list)또는 Azure Machine Learning [studio](how-to-manage-environments-in-studio.md) 를 사용 하 여 환경의 전체 목록 및 해당 종속성을 가져옵니다. 자세한 내용은 [환경 문서](how-to-use-environments.md#use-a-curated-environment)를 참조하세요. 

## <a name="training-curated-environments"></a>큐 레이트 환경 교육

### <a name="pytorch"></a>PyTorch

**이름**: AzureML-pytorch-1.9-ubuntu 18.04-py37-cuda11  
**설명**: AZUREML python SDK 및 추가 Python 패키지를 포함 하는 PyTorch를 사용 하는 심층 학습을 위한 환경입니다.  

개인 워크플로에 대해 다음 Dockerfile을 사용자 지정할 수 있습니다.

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.1-cudnn8-ubuntu18.04:20211005.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/pytorch-1.9

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 \
    pip=20.2.4 \
    pytorch=1.9.0 \
    torchvision=0.10.0 \
    torchaudio=0.9.0 \
    cudatoolkit=11.1.1 \
    nvidia-apex=0.1.0 \
    -c anaconda -c pytorch -c conda-forge

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN HOROVOD_WITH_PYTORCH=1 \
    pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'scipy>=1.5,<1.6' \
                'numpy>=1.10,<1.20' \
                'ipykernel~=6.0' \
                'azureml-core==1.34.0' \
                'azureml-defaults==1.34.0' \
                'azureml-mlflow==1.34.0' \
                'azureml-telemetry==1.34.0' \
                'tensorboard==2.4.0' \
                'tensorflow-gpu==2.4.1' \
                'onnxruntime-gpu>=1.7,<1.8' \
                'horovod[pytorch]==0.21.3' \
                'future==0.17.1' \
                'torch-tb-profiler==0.2.1'


# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

사용 가능한 기타 PyTorch 환경:
* Pytorch-1.8-ubuntu 18.04-py37-cuda11
* AzureML-pytorch-1.7-ubuntu18.04-py37-cuda11-gpu

### <a name="lightgbm"></a>LightGBM

**이름**: AzureML-lightgbm-3.2-ubuntu 18.04-py37-cpu  
**설명**: Scikit를 사용 하는 기계 학습 환경-배우기, LightGBM, XGBoost, AZUREML Python SDK 및 추가 패키지를 포함 하는 dask.  

개인 워크플로에 대해 다음 Dockerfile을 사용자 지정할 수 있습니다.

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20211005.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/lightgbm

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 pip=20.2.4

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN HOROVOD_WITH_TENSORFLOW=1 \
    pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'numpy>=1.10,<1.20' \
                'scipy~=1.5.0' \
                'scikit-learn~=0.24.1' \
                'xgboost~=1.4.0' \
                'lightgbm~=3.2.0' \
                'dask~=2021.6.0' \
                'distributed~=2021.6.0' \
                'dask-ml~=1.9.0' \
                'adlfs~=0.7.0' \
                'ipykernel~=6.0' \
                'azureml-core==1.34.0' \
                'azureml-defaults==1.34.0' \
                'azureml-mlflow==1.34.0' \
                'azureml-telemetry==1.34.0'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

### <a name="sklearn"></a>Sklearn
**이름**: AzureML-기능 배우기-0.24-ubuntu 18.04-py37-cuda11-gpu  
**설명**: Scikit를 사용 하 여 회귀, 클러스터링, 분류 등의 작업 환경입니다. Azure ML Python SDK 및 추가 python 패키지가 들어 있습니다.  

개인 워크플로에 대해 다음 Dockerfile을 사용자 지정할 수 있습니다.

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20211005.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/sklearn-0.24.1

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 pip=20.2.4

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'scipy>=1.5,<1.6' \
                'numpy>=1.10,<1.20' \
                'ipykernel~=6.0' \
                'azureml-core==1.34.0' \
                'azureml-defaults==1.34.0' \
                'azureml-mlflow==1.34.0' \
                'azureml-telemetry==1.34.0' \
                'scikit-learn==0.24.1'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

### <a name="tensorflow"></a>TensorFlow

**이름**: AzureML-tensorflow-2.4-ubuntu 18.04-py37-cuda11  
**설명**: AZUREML python SDK 및 추가 Python 패키지를 포함 하는 Tensorflow를 사용 하는 심층 학습을 위한 환경입니다.  

개인 워크플로에 대해 다음 Dockerfile을 사용자 지정할 수 있습니다.

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04:20211005.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/tensorflow-2.4

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 pip=20.2.4

# Prepend path to AzureML conda environment
ENV PATH $AZUREML_CONDA_ENVIRONMENT_PATH/bin:$PATH

# Install pip dependencies
RUN HOROVOD_WITH_TENSORFLOW=1 \
    pip install 'matplotlib>=3.3,<3.4' \
                'psutil>=5.8,<5.9' \
                'tqdm>=4.59,<4.60' \
                'pandas>=1.1,<1.2' \
                'scipy>=1.5,<1.6' \
                'numpy>=1.10,<1.20' \
                'ipykernel~=6.0' \
                'azureml-core==1.34.0' \
                'azureml-defaults==1.34.0' \
                'azureml-mlflow==1.34.0' \
                'azureml-telemetry==1.34.0' \
                'tensorboard==2.4.0' \
                'tensorflow-gpu==2.4.1' \
                'tensorflow-datasets==4.3.0' \
                'onnxruntime-gpu>=1.7,<1.8' \
                'horovod[tensorflow-gpu]==0.21.3'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

## <a name="automated-ml-automl"></a>AutoML(자동화된 ML)

AutoML을 사용하는 Azure ML 파이프라인 교육 워크플로는 컴퓨팅 유형 및 DNN 사용 여부에 따라 선별된 환경을 자동으로 선택합니다. AutoML은 다음과 같은 큐레이팅된 환경을 제공합니다.

| Name | 컴퓨팅 형식 | DNN 사용 |
| --- | --- | --- |
|AzureML-AutoML | CPU | 예 |
|AzureML-AutoML-DNN | CPU | 예 |
| AzureML-AutoML-GPU | GPU | No |
| AzureML-AutoML-DNN-GPU | GPU | Yes |

AutoML 및 Azure ML 파이프라인에 대한 자세한 내용은 [Python의 Azure Machine Learning 파이프라인에서 자동화된 ML 사용](how-to-use-automlstep-in-pipelines.md)을 참조하세요.

## <a name="inference-curated-environments-and-prebuilt-docker-images"></a>유추 큐 레이트 환경 및 미리 빌드된 docker 이미지

[!INCLUDE [list-of-inference-prebuilt-docker-images](../../includes/aml-inference-list-prebuilt-docker-images.md)]

## <a name="security"></a>보안
지원 되는 환경에 대 한 버전 업데이트는 30 일이 넘은 취약점을 해결 하기 위해 2 주마다 릴리스됩니다. 

