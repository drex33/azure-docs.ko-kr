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
ms.openlocfilehash: b66f76d80d3dad6b24eefa6fb7bbd88cfce082e0
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360029"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning 큐레이팅된 환경

이 문서에서는 Azure Machine Learning 최신 프레임워크 버전이 있는 큐레이팅된 환경을 나열합니다. 큐레이팅된 환경은 Azure Machine Learning에서 제공하며 기본적으로 작업 영역에서 사용할 수 있습니다. 최신 버전의 Azure Machine Learning SDK를 사용하는 캐시된 Docker 이미지에서 지원되므로 실행 준비 비용을 줄이고 배포 시간을 단축할 수 있습니다. 이러한 환경을 사용하여 다양한 기계 학습 프레임워크를 빠르게 시작할 수 있습니다.

> [!NOTE]
> Python [SDK,](how-to-use-environments.md) [CLI](/cli/azure/ml/environment?view=azure-cli-latest&preserve-view=true#az_ml_environment_list)또는 Azure Machine Learning [Studio를](how-to-manage-environments-in-studio.md) 사용하여 환경 및 해당 의존성의 전체 목록을 확인합니다. 자세한 내용은 [환경 문서](how-to-use-environments.md#use-a-curated-environment)를 참조하세요. 

## <a name="training-curated-environments"></a>큐레이팅된 환경 학습

### <a name="pytorch"></a>PyTorch

**이름:** AzureML-pytorch-1.9-ubuntu18.04-py37-cuda11-gpu  
**설명:** AzureML Python SDK 및 추가 Python 패키지가 포함된 PyTorch를 이용한 딥 러닝 환경입니다.  

다음 Dockerfile은 개인 워크플로에 맞게 사용자 지정할 수 있습니다.

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.1-cudnn8-ubuntu18.04:20210922.v1

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

사용 가능한 다른 PyTorch 환경:
* AzureML-pytorch-1.8-ubuntu18.04-py37-cuda11-gpu
* AzureML-pytorch-1.7-ubuntu18.04-py37-cuda11-gpu

### <a name="lightgbm"></a>LightGBM

**이름:** AzureML-lightgbm-3.2-ubuntu18.04-py37-cpu  
**설명:** AzureML Python SDK 및 추가 패키지를 포함하는 Scikit-learn, LightGBM, XGBoost, Dask를 포함하는 기계 학습 환경입니다.  

다음 Dockerfile은 개인 워크플로에 맞게 사용자 지정할 수 있습니다.

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210922.v1

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
**이름:** AzureML-sklearn-0.24-ubuntu18.04-py37-cuda11-gpu  
**설명:** Scikit-learn을 통해 회귀, 클러스터링 및 분류와 같은 작업을 위한 환경입니다. Azure ML Python SDK 및 추가 python 패키지가 들어 있습니다.  

다음 Dockerfile은 개인 워크플로에 맞게 사용자 지정할 수 있습니다.

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210922.v1

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

**이름:** AzureML-tensorflow-2.4-ubuntu18.04-py37-cuda11-gpu  
**설명:** AzureML Python SDK 및 추가 Python 패키지를 포함하는 Tensorflow를 포함하는 딥 러닝 환경입니다.  

다음 Dockerfile은 개인 워크플로에 맞게 사용자 지정할 수 있습니다.

```dockerfile
FROM mcr.microsoft.com/azureml/openmpi4.1.0-cuda11.0.3-cudnn8-ubuntu18.04:20210922.v1

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

## <a name="inference-only-curated-environments-and-prebuilt-docker-images"></a>큐레이팅된 환경 및 미리 빌드된 Docker 이미지만 유추

* 모든 docker 이미지는 루트가 아닌 사용자로 실행됩니다.
* Docker 이미지에는 `latest` 태그를 사용하는 것이 좋습니다. 유추를 위해 미리 빌드된 Docker 이미지는 MCR(Microsoft 컨테이너 레지스트리)에 게시되며 사용 가능한 태그 목록을 쿼리하려면 [GitHub 리포지토리의 지침](https://github.com/microsoft/ContainerRegistry#browsing-mcr-content)을 따르세요.

### <a name="tensorflow"></a>TensorFlow

프레임워크 버전 | CPU/GPU | 미리 설치된 패키지 | MCR 경로 | 큐레이팅된 환경
 --- | --- | --- | --- | --- |
 1.15 | CPU | pandas==0.25.1 </br> numpy=1.20.1 | `mcr.microsoft.com/azureml/tensorflow-1.15-ubuntu18.04-py37-cpu-inference:latest`  | AzureML-tensorflow-1.15-ubuntu18.04-py37-cpu-inference | 
2.4 | CPU | numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/tensorflow-2.4-ubuntu18.04-py37-cpu-inference:latest` | AzureML-tensorflow-2.4-ubuntu18.04-py37-cpu-inference |
2.4 | GPU | numpy >= 1.16.0 </br> pandas~=1.1.x </br> CUDA==11.0.3 </br> CuDNN==8.0.5.39 | `mcr.microsoft.com/azureml/tensorflow-2.4-ubuntu18.04-py37-cuda11.0.3-gpu-inference:latest` | AzureML-tensorflow-2.4-ubuntu18.04-py37-cuda11.0.3-gpu-inference |

### <a name="pytorch"></a>PyTorch

프레임워크 버전 | CPU/GPU | 미리 설치된 패키지 | MCR 경로 | 큐레이팅된 환경
 --- | --- | --- | --- | --- |
 1.6 | CPU | numpy==1.20.1 </br> pandas==0.25.1 | `mcr.microsoft.com/azureml/pytorch-1.6-ubuntu18.04-py37-cpu-inference:latest` | AzureML-pytorch-1.6-ubuntu18.04-py37-cpu-inference |
1.7 | CPU | numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/pytorch-1.7-ubuntu18.04-py37-cpu-inference:latest` | AzureML-pytorch-1.7-ubuntu18.04-py37-cpu-inference |

### <a name="scikit-learn"></a>SciKit-Learn

프레임워크 버전 | CPU/GPU | 미리 설치된 패키지 | MCR 경로 | 큐레이팅된 환경
 --- | --- | --- | --- | --- |
0.24.1  | CPU | scikit-learn==0.24.1 </br> numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/sklearn-0.24.1-ubuntu18.04-py37-cpu-inference:latest` | AzureML-sklearn-0.24.1-ubuntu18.04-py37-cpu-inference |

### <a name="onnx-runtime"></a>ONNX Runtime

프레임워크 버전 | CPU/GPU | 미리 설치된 패키지 | MCR 경로 | 큐레이팅된 환경
 --- | --- | --- | --- | --- |
1.6 | CPU | numpy>=1.16.0 </br> pandas~=1.1.x | `mcr.microsoft.com/azureml/onnxruntime-1.6-ubuntu18.04-py37-cpu-inference:latest` |AzureML-onnxruntime-1.6-ubuntu18.04-py37-cpu-inference |

### <a name="xgboost"></a>XGBoost

프레임워크 버전 | CPU/GPU | 미리 설치된 패키지 | MCR 경로 | 큐레이팅된 환경
 --- | --- | --- | --- | --- |
0.9 | CPU | scikit-learn==0.23.2 </br> numpy==1.20.1 </br> pandas==0.25.1 | `mcr.microsoft.com/azureml/xgboost-0.9-ubuntu18.04-py37-cpu-inference:latest` | AzureML-xgboost-0.9-ubuntu18.04-py37-cpu-inference | 

### <a name="no-framework"></a>프레임워크 없음

프레임워크 버전 | CPU/GPU | 미리 설치된 패키지 | MCR 경로 | 큐레이팅된 환경
 --- | --- | --- | --- | --- |
해당 없음 | CPU | 해당 없음 | `mcr.microsoft.com/azureml/minimal-ubuntu18.04-py37-cpu-inference:latest` | AzureML-minimal-ubuntu18.04-py37-cpu-inference  |


## <a name="security"></a>보안
지원되는 환경에 대한 버전 업데이트는 30일 이하의 vulnerabilties를 해결하기 위해 2주마다 릴리스됩니다. 

