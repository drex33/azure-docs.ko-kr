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
ms.openlocfilehash: cb78928badb067731f6e4fbed75a01346409b70a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114451053"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning 큐레이팅된 환경

이 문서에는 Azure Machine Learning의 큐레이팅된 환경이 나열되어 있습니다. 큐레이팅된 환경은 Azure Machine Learning에서 제공하며 기본적으로 작업 영역에서 사용할 수 있습니다. 최신 버전의 Azure Machine Learning SDK를 사용하는 캐시된 Docker 이미지에서 지원되므로 실행 준비 비용을 줄이고 배포 시간을 단축할 수 있습니다. 이러한 환경을 사용하여 다양한 기계 학습 프레임워크를 빠르게 시작할 수 있습니다.

> [!NOTE]
> 이 목록은 2021년 7월 기준으로 업데이트됩니다. [Python SDK](how-to-use-environments.md), [CLI](/cli/azure/ml/environment?view=azure-cli-latest&preserve-view=true#az_ml_environment_list) 또는 Azure Machine Learning [스튜디오](how-to-manage-environments-in-studio.md)를 사용하여 최신 환경 및 종속 항목 목록을 가져옵니다. 자세한 내용은 [환경 문서](how-to-use-environments.md#use-a-curated-environment)를 참조하세요. 이 새 세트의 릴리스 후에는 이전 큐레이팅된 환경이 숨겨지지만 계속 사용할 수 있습니다. 

## <a name="pytorch"></a>PyTorch

**이름** - AzureML-pytorch-1.7-ubuntu18.04-py37-cuda11-gpu  
**설명** - Azure ML Python SDK 및 추가 python 패키지가 포함된 PyTorch를 사용한 딥 러닝을 위한 환경입니다.  
**Dockerfile 구성** - 프라이빗 워크플로에 맞게 다음 Dockerfile을 사용자 지정할 수 있습니다.

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04:20210615.v1

ENV AZUREML_CONDA_ENVIRONMENT_PATH /azureml-envs/pytorch-1.7

# Create conda environment
RUN conda create -p $AZUREML_CONDA_ENVIRONMENT_PATH \
    python=3.7 \
    pip=20.2.4 \
    pytorch=1.7.1 \
    torchvision=0.8.2 \
    torchaudio=0.7.2 \
    cudatoolkit=11.0 \
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
                'azureml-core==1.30.0' \
                'azureml-defaults==1.30.0' \
                'azureml-mlflow==1.30.0' \
                'azureml-telemetry==1.30.0' \
                'tensorboard==2.4.0' \
                'tensorflow-gpu==2.4.1' \
                'onnxruntime-gpu>=1.7,<1.8' \
                'horovod[pytorch]==0.21.3' \
                'future==0.17.1'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

## <a name="lightgbm"></a>LightGBM

**이름** - AzureML-lightgbm-3.2-ubuntu18.04-py37-cpu  
**설명** - AzureML Python SDK 및 추가 패키지가 포함된 Scikit-learn, LightGBM, XGBoost, Dask를 사용한 기계 학습 환경입니다.  
**Dockerfile 구성** - 프라이빗 워크플로에 맞게 다음 Dockerfile을 사용자 지정할 수 있습니다.

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210615.v1

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
                'azureml-core==1.30.0' \
                'azureml-defaults==1.30.0' \
                'azureml-mlflow==1.30.0' \
                'azureml-telemetry==1.30.0'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

## <a name="sklearn"></a>Sklearn
**이름** - AzureML-sklearn-0.24-ubuntu18.04-py37-cuda11-gpu  
**설명** - Scikit-learn을 사용한 회귀, 클러스터링, 분류 등의 작업을 위한 환경입니다. Azure ML Python SDK 및 추가 python 패키지가 들어 있습니다.  
**Dockerfile 구성** - 프라이빗 워크플로에 맞게 다음 Dockerfile을 사용자 지정할 수 있습니다.

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04:20210615.v1

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
                'azureml-core==1.30.0' \
                'azureml-defaults==1.30.0' \
                'azureml-mlflow==1.30.0' \
                'azureml-telemetry==1.30.0' \
                'scikit-learn==0.24.1'

# This is needed for mpi to locate libpython
ENV LD_LIBRARY_PATH $AZUREML_CONDA_ENVIRONMENT_PATH/lib:$LD_LIBRARY_PATH
```

## <a name="tensorflow"></a>TensorFlow

**이름** - AzureML-tensorflow-2.4-ubuntu18.04-py37-cuda11-gpu  
**설명** - Azure ML Python SDK 및 추가 python 패키지가 포함된 Tensorflow를 사용한 딥 러닝을 위한 환경입니다.  
**Dockerfile 구성** - 프라이빗 워크플로에 맞게 다음 Dockerfile을 사용자 지정할 수 있습니다.

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04:20210615.v1

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
                'azureml-core==1.30.0' \
                'azureml-defaults==1.30.0' \
                'azureml-mlflow==1.30.0' \
                'azureml-telemetry==1.30.0' \
                'tensorboard==2.4.0' \
                'tensorflow-gpu==2.4.1' \
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

## <a name="inference-only-curated-environments-and-prebuilt-docker-images"></a>큐레이팅된 환경 및 미리 빌드된 Docker 이미지만 유추

미리 빌드된 Docker 이미지의 추론 전용 큐레이팅 환경 및 MCR 경로에 대해 알아보려면 [추론을 위해 미리 빌드된 Docker 이미지](concept-prebuilt-docker-images-inference.md#list-of-prebuilt-docker-images-for-inference)를 참조하세요.
