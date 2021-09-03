---
title: 딥 러닝 및 AI 프레임워크
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine에서 사용할 수 있는 딥 러닝 프레임워크 및 도구입니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: michalmar
ms.author: mimarusa
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: a757151d17456c7ee9646bc0730a51f34088b255
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114729192"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Azure Data Science VM의 딥 러닝 및 AI 프레임워크
DSVM의 딥 러닝 프레임워크는 아래에 나열되어 있습니다.


## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA, cuDNN, NVIDIA 드라이버](https://developer.nvidia.com/cuda-toolkit)

| 범주 | 값 |
|--|--|
| 지원되는 버전 | 11 |
| 지원되는 DSVM 버전 | Windows Server 2019<br>Ubuntu 18.04 |
| DSVM에 구성/설치 방법 | _nvidia smi_ 는 시스템 경로에서 사용할 수 있습니다. |
| 실행 방법 | 명령 프롬프트(Windows) 또는 터미널(Linux)을 시작한 후 _nvidia smi_ 를 실행합니다. |
## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

| 범주 | 값 |
| ------------- | ------------- |
| 지원되는 버전 | 0.21.3|
| 지원되는 DSVM 버전      | Ubuntu 18.04 |
| DSVM에 구성/설치 방법  | Horovod는 Python 3.5에 설치됩니다. |
| 실행 방법      | 터미널에서 올바른 환경을 활성화한 다음 Python을 실행합니다. |


## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVidia System Management 인터페이스(nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface)

| 범주 | 값 |
|--|--|
| 지원되는 버전 |  |
| 지원되는 DSVM 버전 | Windows Server 2019<br>Ubuntu 18.04 |
| 용도 정의 | GPU 작업을 쿼리하기 위한 NVIDIA 도구 |
| DSVM에 구성/설치 방법 | `nvidia-smi`는 시스템 경로에 있습니다. |
| 실행 방법 | **GPU가 있는** 가상 머신에서 명령 프롬프트(Windows) 또는 터미널(Linux)을 연 다음 `nvidia-smi`를 실행합니다. |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

| 범주 | 값 |
|--|--|
| 지원되는 버전 | 1.9.0(Ubuntu 18.04, Windows 2019) |
| 지원되는 DSVM 버전 | Windows Server 2019<br>Ubuntu 18.04 |
| DSVM에 구성/설치 방법 | Python, conda 환경 'py38_default', 'py38_pytorch'에 설치됨 |
| 실행 방법 | 터미널: 올바른 환경을 활성화한 다음, Python을 실행합니다.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): 샘플에 대한 PyTorch 디렉터리에 연결하여 엽니다. |

## <a name="tensorflow"></a>[Tensorflow](https://www.tensorflow.org/)

| 범주 | 값 |
|--|--|
| 지원되는 버전 | 2.5 |
| 지원되는 DSVM 버전 | Windows Server 2019<br>Ubuntu 18.04 |
| DSVM에 구성/설치 방법 | Python, conda 환경 'py38_default', 'py38_tensorflow'에 설치됨 |
| 실행 방법 | 터미널: 올바른 환경을 활성화한 다음, Python을 실행합니다. <br/> * Jupyter: [Jupyter](provision-vm.md) 또는 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)에 연결한 다음, 샘플에 대한 TensorFlow 디렉터리를 엽니다. |
