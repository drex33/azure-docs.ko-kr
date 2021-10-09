---
title: 포함 파일
description: 포함 파일
services: machine-learning
author: shivanissambare
ms.service: machine-learning
ms.author: ssambare
ms.custom: include file
ms.topic: include
ms.date: 10/07/2021
ms.openlocfilehash: 32b18e9c98fe166d127e9fcad5f59794e8080751
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712048"
---
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