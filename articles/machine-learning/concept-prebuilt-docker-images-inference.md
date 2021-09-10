---
title: 미리 빌드된 Docker 이미지
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 유추(채점)를 위해 미리 빌드된 Docker 이미지
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 05/25/2021
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt
ms.openlocfilehash: 4851e5cdfc5e50febf7d60ab4d7eaee2c95da5b4
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866304"
---
# <a name="prebuilt-docker-images-for-inference-preview"></a>유추를 위해 미리 빌드된 Docker 이미지(미리 보기)

유추 [(미리 보기)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)를 위해 미리 빌드된 Docker 컨테이너 이미지는 Azure Machine Learning을 사용하여 모델을 배포할 때 사용됩니다.  이미지는 널리 사용되는 기계 학습 프레임워크 및 Python 패키지로 미리 빌드됩니다. 다음 방법 중 하나를 사용해 패키지를 확장하여 다른 패키지를 추가할 수도 있습니다.

* [Python 패키지를 추가](how-to-prebuilt-docker-images-inference-python-extensibility.md)합니다.
* [미리 빌드된 유추 이미지를 새 Dockerfile의 기반으로 사용](how-to-extend-prebuilt-docker-image-inference.md)합니다. 이 방법을 사용하면 **Python 패키지와 apt 패키지** 를 모두 설치할 수 있습니다.

## <a name="why-should-i-use-prebuilt-images"></a>미리 빌드된 이미지를 사용해야 하는 이유는 무엇인가요?

* 모델 배포 대기 시간을 줄입니다.
* 모델 배포 성공률을 향상시킵니다.
* 모델을 배포하는 동안 불필요한 이미지 빌드를 방지합니다.
* 이미지/컨테이너에 필요한 종속성 및 액세스 권한만 있어야 합니다. 

## <a name="list-of-prebuilt-docker-images-for-inference"></a>유추를 위해 미리 빌드된 Docker 이미지 목록 

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

## <a name="next-steps"></a>다음 단계

* [Python 패키지를 미리 빌드된 이미지에 추가](how-to-prebuilt-docker-images-inference-python-extensibility.md)합니다.
* [미리 빌드된 패키지를 새 Dockerfile의 기반으로 사용](how-to-extend-prebuilt-docker-image-inference.md)합니다.