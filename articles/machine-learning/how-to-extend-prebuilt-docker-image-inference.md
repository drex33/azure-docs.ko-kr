---
title: 미리 빌드된 Docker 이미지 확장
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 미리 빌드된 docker 이미지 확장
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 05/25/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt
ms.openlocfilehash: c4a1b52f87f0c5053e357fcb67a2e90962dc37c1
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433533"
---
# <a name="extend-a-prebuilt-docker-image-preview"></a>미리 보기된 Docker 이미지 확장(미리 보기)

경우에 따라 [모델 유추에 대한 미리 빌드된 Docker 이미지](concept-prebuilt-docker-images-inference.md) 및 Azure Machine Learning에 대한 [확장성](./how-to-prebuilt-docker-images-inference-python-extensibility.md) 솔루션이 유추 서비스 요구를 충족하지 못할 수 있습니다.

이 경우에는 미리 빌드된 이미지 중 하나를 시작점으로 사용하여 Dockerfile을 사용하여 새 이미지를 만들 수 있습니다. 미리 빌드된 기존 Docker 이미지에서 확장하여 처음부터 이미지를 만들지 않고도 Azure Machine Learning 네트워크 스택과 라이브러리를 사용할 수 있습니다.

> [!IMPORTANT]
> 현재 Azure Machine Learning에서 미리 빌드된 Docker 이미지는 미리 보기에서 사용할 수 있습니다. 미리 보기 기능은 지원 또는 서비스 수준 계약의 보증 없이 “있는 그대로” 제공됩니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

**혜택 및 단점**

Dockerfile을 사용하면 배포 전에 이미지를 완전히 사용자 지정할 수 있습니다. 이를 통해 컨테이너에서 설정되는 종속성 또는 환경 변수를 최대한 제어할 수 있습니다.

이 방법의 주요 단점은 배포 중에 추가 이미지 빌드가 발생하여 배포 프로세스가 느려진다는 것입니다. [Python 패키지 확장성](./how-to-prebuilt-docker-images-inference-python-extensibility.md) 방법을 사용할 수 있는 경우 배포 속도가 더 빨라집니다.
## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 작업 영역을 만드는 방법에 대한 자습서는 [Azure Machine Learning 시작](quickstart-create-resources.md)을 참조하세요.
* [Dockerfile](https://docs.docker.com/engine/reference/builder/) 제작에 대해 잘 알고 있어야 합니다.
* `docker` CLI를 비롯한 [Docker](https://www.docker.com/)의 로컬 작업 설치 **또는** Azure Machine Learning 작업 영역에 연결된 ACR(Azure Container Registry) 중 하나입니다.

    > [!WARNING]
    > 작업 영역에 대한 Azure Container Registry는 작업 영역을 사용하여 처음으로 모델을 학습시키거나 배포할 때 생성됩니다. 새 작업 영역을 만들었지만 모델을 학습시키거나 만들지 않은 경우 작업 영역에 대한 Azure Container Registry는 존재하지 않습니다.
## <a name="create-and-build-dockerfile"></a>Dockerfile 만들기 및 빌드

다음은 기본 이미지로 Azure Machine Learning 미리 빌드된 Docker 이미지를 사용하는 샘플 Dockerfile입니다.

```Dockerfile
FROM mcr.microsoft.com/azureml/<image_name>:<tag>

COPY requirements.txt /tmp/requirements.txt

RUN pip install –r /tmp/requirements.txt
```

그런 다음, 필요한 모든 파일이 포함된 디렉터리에 위의 Dockerfile을 넣고 다음 명령을 실행하여 이미지를 빌드합니다.

```bash
docker build -f <above dockerfile> -t <image_name>:<tag> .
```

> [!TIP]
> `docker build`에 대한 자세한 내용은 [Docker 설명서](https://docs.docker.com/engine/reference/commandline/build/)에서 확인할 수 있습니다.

`docker build` 명령을 로컬로 사용할 수 없는 경우에는 Azure Machine Learning 작업 영역에 대한 Azure Container Registry ACR을 사용하여 클라우드에서 Docker 이미지를 빌드합니다. 자세한 내용은 [자습서: Azure Container Registry를 사용하여 컨테이너 이미지 빌드 및 배포](../container-registry/container-registry-tutorial-quick-task.md)를 참조하세요.

> [!IMPORTANT]
> Azure Container Registry를 통해 사용자 지정 기본 이미지를 만들기 전에 먼저 Dockerfile이 로컬에서 작동하는지 확인하는 것이 좋습니다.

다음 섹션에는 Dockerfile에 대한 보다 구체적인 세부 정보가 포함되어 있습니다.

## <a name="install-extra-packages"></a>추가 패키지 설치

Ubuntu 컨테이너에 설치해야 하는 다른 `apt` 패키지가 있는 경우 Dockerfile에 추가할 수 있습니다. 다음 예제에서는 Dockerfile에서 `apt-get` 명령을 사용하는 방법을 보여 줍니다.

```Dockerfile
FROM <prebuilt docker image from MCR>

# Switch to root to install apt packages
USER root:root

RUN apt-get update && \
    apt-get install -y \
    <package-1> \
    ... 
    <package-n> && \
    apt-get clean -y && \
    rm -rf /var/lib/apt/lists/*

# Switch back to non-root user
USER dockeruser
```

Dockerfile에서 추가 pip 패키지를 설치할 수도 있습니다. 다음 예제에서는 `pip install` 사용을 보여 줍니다.

```Dockerfile
RUN pip install <library>
```

<a id="buildmodel"></a>

## <a name="build-model-and-code-into-images"></a>이미지에 모델 및 코드 빌드

모델 및 코드를 이미지에 빌드해야 하는 경우 Dockerfile에서 다음 환경 변수를 설정해야 합니다.

* `AZUREML_ENTRY_SCRIPT`: 코드의 항목 스크립트입니다. 이 파일에는 `init()` 및 `run()` 메서드가 포함되어 있습니다.
* `AZUREML_MODEL_DIR`: 모델 파일이 포함된 디렉터리입니다. 항목 스크립트는 이 디렉터리를 모델의 루트 디렉터리로 사용해야 합니다.

다음 예제에서는 Dockerfile에서 이러한 환경 변수를 설정하는 것을 보여 줍니다.

```Dockerfile
FROM <prebuilt docker image from MCR>

# Code
COPY <local_code_directory> /var/azureml-app
ENV AZUREML_ENTRY_SCRIPT=<entryscript_file_name>

# Model
COPY <model_directory> /var/azureml-app/azureml-models
ENV AZUREML_MODEL_DIR=/var/azureml-app/azureml-models
```

## <a name="example-dockerfile"></a>예제 Dockerfile

다음 예제에서는 `apt` 패키지를 설치하고, 환경 변수를 설정하고, 코드와 모델을 Dockerfile의 일부로 포함하는 것을 보여 줍니다.

```Dockerfile
FROM mcr.microsoft.com/azureml/pytorch-1.6-ubuntu18.04-py37-cpu-inference:latest 

USER root:root

# Install libpng-tools and opencv
RUN apt-get update && \
    apt-get install -y \
    libpng-tools \
    python3-opencv && \
    apt-get clean -y && \
    rm -rf /var/lib/apt/lists/*

# Switch back to non-root user
USER dockeruser

# Code
COPY code /var/azureml-app
ENV AZUREML_ENTRY_SCRIPT=score.py

# Model
COPY model /var/azureml-app/azureml-models
ENV AZUREML_MODEL_DIR=/var/azureml-app/azureml-models
```

## <a name="next-steps"></a>다음 단계

Azure Machine Learning Python SDK에서 Dockerfile을 사용하려면 다음 문서를 참조하세요.

* [자체 로컬 Dockerfile 사용](how-to-use-environments.md#use-your-own-dockerfile)
* [미리 빌드된 Docker 이미지 사용 및 사용자 지정 기본 이미지 만들기](how-to-use-environments.md#use-a-prebuilt-docker-image)

모델 배포에 대한 자세한 내용은 [모델 배포 방법](how-to-deploy-and-where.md)을 참조하세요.

미리 빌드된 Docker 이미지 배포 문제를 해결하는 방법을 알아보려면 [미리 빌드된 Docker 이미지 배포 문제 해결 방법](how-to-troubleshoot-prebuilt-docker-image-inference.md)을 참조하세요.