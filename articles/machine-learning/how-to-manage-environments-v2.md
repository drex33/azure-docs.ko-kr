---
title: CLI를 사용 하 여 Azure Machine Learning 환경 관리 (v2)
titleSuffix: Azure Machine Learning
description: Machine Learning에 대 한 Azure CLI 확장을 사용 하 여 Azure ML 환경을 관리 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: 416b2d6b2295dd4be2a1cbe93e9361a61a497251
ms.sourcegitcommit: 11ca7ba5a017429c22a6b0bc02acb70b83a2984a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132813811"
---
# <a name="manage-azure-machine-learning-environments-with-the-cli-v2-preview"></a>CLI (v2)를 사용 하 여 Azure Machine Learning 환경 관리 (미리 보기)

Azure Machine Learning 환경은 작업 또는 배포에 대 한 실행 환경을 정의 하 고 코드에 대 한 종속성을 캡슐화 합니다. Azure ML는 환경 사양을 사용 하 여 지정 된 계산 대상에서 학습 또는 점수 매기기 코드가 실행 되는 Docker 컨테이너를 만듭니다. Conda 사양, Docker 이미지 또는 Docker 빌드 컨텍스트에서 환경을 정의할 수 있습니다.

이 문서에서는 CLI (v2)를 사용 하 여 Azure ML 환경을 만들고 관리 하는 방법에 대해 알아봅니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>사전 요구 사항

- CLI를 사용하려면 Azure 구독이 있어야 합니다. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.
- [Machine Learning에 대한 Azure CLI 확장 설치 및 설정](how-to-configure-cli.md)

> [!TIP]
> 모든 기능을 갖춘 개발 환경을 위해서는 Visual Studio Code 및 [Azure Machine Learning 확장](how-to-setup-vs-code.md)을 사용하여 [Azure Machine Learning 리소스를 관리](how-to-manage-resources-vscode.md)하고 [기계 학습 모델을 학습](tutorial-train-deploy-image-classification-model-vscode.md)시킵니다.

### <a name="clone-examples-repository"></a>예제 리포지토리 복제

학습 예제를 실행하려면 먼저 예제 리포지토리를 복제하고 `cli` 디렉터리로 변경합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="git_clone":::

`--depth 1`은 리포지토리에 대한 최신 커밋만 복제하여 작업을 완료하는 시간을 줄입니다.

## <a name="curated-environments"></a>큐레이팅 환경

Azure ML에는 두 가지 유형의 환경 (큐 레이트 및 사용자 지정 환경)이 있습니다. 큐 레이트 환경은 인기 있는 ML 프레임 워크 및 도구를 포함 하는 미리 정의 된 환경입니다. 사용자 지정 환경은 사용자가 정의 하며를 통해 만들 수 있습니다 `az ml environment create` .

큐 레이트 환경은 Azure ML에서 제공 하며, 기본적으로 작업 영역에서 사용할 수 있습니다. Azure ML는 최신 framework 버전 릴리스로 이러한 환경을 정기적으로 업데이트 하 고 버그 수정과 보안 패치에 대해 이러한 환경을 유지 관리 합니다. 캐시 된 Docker 이미지가 지원 되므로 작업 준비 비용과 모델 배포 시간이 줄어듭니다.

구문을 사용 하 여 특정 환경을 참조 하 여 교육 또는 배포를 위한 기본 큐 레이트 환경을 사용할 수 있습니다 `azureml:<curated-environment-name>:<version>` . 이러한 큐 레이트 환경을 다시 사용 하는 Dockerfiles를 수정 하 여 사용자 지정 환경에 대 한 참조로 사용할 수도 있습니다.

Azure ML studio UI에서 또는를 통해 CLI (v2)를 사용 하 여 사용 가능한 큐 레이트 환경의 집합을 볼 수 있습니다 `az ml environments list` .

## <a name="create-an-environment"></a>환경 만들기

Conda 사양, Docker 이미지 또는 Docker 빌드 컨텍스트에서 환경을 정의할 수 있습니다. YAML 사양 파일을 사용 하 여 환경을 구성 하 고 다음 CLI 명령을 사용 하 여 환경을 만듭니다.

```cli
az ml environment create --file my_environment.yml
```

Azure ML 환경에 대 한 yaml 참조 설명서는 [CLI (v2) 환경 yaml 스키마](reference-yaml-environment.md)를 참조 하세요.

### <a name="create-an-environment-from-a-docker-image"></a>Docker 이미지에서 환경 만들기

Docker 이미지에서 환경을 정의 하려면 Docker 허브 또는 Azure Container Registry와 같은 레지스트리에 호스트 되는 이미지의 이미지 URI를 제공 합니다. 

다음 예제는 Docker 이미지에서 정의 된 환경에 대 한 YAML 사양 파일입니다. Docker 허브의 공식 PyTorch 리포지토리에서 이미지는 `image` YAML 파일의 속성을 통해 지정 됩니다.

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-image.yml":::

환경을 만들려면 다음을 수행 합니다.

```cli
az ml environment create --file assets/environment/docker-image.yml
```

> [!TIP]
> Azure ML는 일반적인 시스템 종속성이 있는 CPU 및 GPU Ubuntu Linux 기반 기본 이미지 집합을 유지 관리 합니다. 예를 들어 GPU 이미지에는 Miniconda, OpenMPI, cuDNN 및 NCCL가 포함 됩니다. 환경에 이러한 이미지를 사용 하거나 고유한 사용자 지정 이미지를 빌드할 때 해당 Dockerfiles을 참조로 사용할 수 있습니다.
>  
> 기본 이미지 집합 및 해당 Dockerfiles는 [AzureML 컨테이너 리포지토리](https://github.com/Azure/AzureML-Containers)를 참조 하세요.

### <a name="create-an-environment-from-a-docker-build-context"></a>Docker 빌드 컨텍스트에서 환경 만들기

미리 빌드된 이미지에서 환경을 정의 하는 대신 Docker [빌드 컨텍스트에서](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#understand-build-context)환경을 정의 하는 것도 가능 합니다. 이렇게 하려면 빌드 컨텍스트로 사용할 디렉터리를 지정 합니다. 이 디렉터리에는 Dockerfile 및 이미지를 빌드하는 데 필요한 기타 파일이 포함 되어 있어야 합니다.

다음 예제는 빌드 컨텍스트에서 정의 된 환경에 대 한 YAML 사양 파일입니다. 빌드 컨텍스트 폴더의 로컬 경로는 필드에 지정 되 `build.local_path` 고, 해당 빌드 컨텍스트 폴더 내의 Dockerfile에 대 한 상대 경로는 필드에 지정 됩니다 `build.dockerfile_path` . `build.dockerfile_path`yaml 파일에서가 생략 된 경우 Azure ML는 `Dockerfile` 빌드 컨텍스트의 루트에서 이라는 dockerfile을 찾습니다.

이 예제에서 빌드 컨텍스트에는 이라는 Dockerfile `Dockerfile` 및 `requirements.txt` Python 패키지를 설치 하기 위한 dockerfile 내에서 참조 되는 파일이 포함 됩니다.

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-context.yml":::

환경을 만들려면 다음을 수행 합니다.

```cli
az ml environment create --file assets/environment/docker-context.yml
```

환경을 만들 때 Azure ML는 빌드 컨텍스트에서 이미지 빌드를 시작 합니다. 빌드 상태를 모니터링 하 고 스튜디오 UI에서 빌드 로그를 볼 수 있습니다.

### <a name="create-an-environment-from-a-conda-specification"></a>Conda 사양에서 환경 만들기

Conda 환경에 대 한 종속성을 포함 하는 표준 conda YAML 구성 파일을 사용 하 여 환경을 정의할 수 있습니다. 이 표준 형식에 대 한 자세한 내용은 [수동으로 환경 만들기](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually) 를 참조 하세요.

또한이 환경에 대 한 기본 Docker 이미지를 지정 해야 합니다. Azure ML는 제공 된 Docker 이미지 위에 conda 환경을 구축 합니다. Docker 이미지에 일부 Python 종속성을 설치 하는 경우 이러한 패키지는 실행 환경에 존재 하지 않아 런타임 오류가 발생 합니다. 기본적으로 Azure ML은 사용자가 지정한 종속성으로 Conda 환경을 빌드하고 기본 이미지에 설치한 Python 라이브러리를 사용하는 대신 해당 환경에서 실행합니다.

다음 예제는 conda 사양에서 정의 된 환경에 대 한 YAML 사양 파일입니다. 여기에서 Azure ML 환경 yaml 파일의 conda 파일에 대 한 상대 경로는 속성을 통해 지정 됩니다 `conda_file` . 또는 속성을 사용 하 여 `conda_file` 별도의 파일에 정의 하는 대신 conda 사양 인라인을 정의할 수 있습니다.

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-image-plus-conda.yml":::

환경을 만들려면 다음을 수행 합니다.

```cli
az ml environment create --file assets/environment/docker-image-plus-conda.yml
```

Azure ML는 작업이 나 배포에서 환경을 사용 하는 경우이 환경 사양에서 최종 Docker 이미지를 빌드합니다. 또한 스튜디오 UI에서 환경의 빌드를 수동으로 트리거할 수 있습니다.

## <a name="manage-environments"></a>환경 관리

CLI (v2)는에서 `az ml environment` Azure ML 환경 자산의 수명 주기를 관리 하기 위한 명령 집합을 제공 합니다.

### <a name="list"></a>목록

작업 영역의 모든 환경을 나열 합니다.

```cli
az ml environment list
```

지정 된 이름으로 모든 환경 버전 나열:

```cli
az ml environment list --name docker-image-example
```

### <a name="show"></a>표시

특정 환경에 대 한 세부 정보를 가져옵니다.

```cli
az ml environment list --name docker-image-example --version 1
```

### <a name="update"></a>업데이트

특정 환경의 변경 가능한 속성을 업데이트 합니다.

```cli
az ml environment update --name docker-image-example --version 1 --set description="This is an updated description."
```

> [!IMPORTANT]
> 환경의 경우 및만 `description` `tags` 업데이트할 수 있습니다. 다른 모든 속성은 변경할 수 없습니다. 이러한 속성을 변경 해야 하는 경우 환경의 새 버전을 만들어야 합니다.

### <a name="delete"></a>삭제

특정 환경 삭제:

```cli
az ml environment delete --name docker-image-example --version 1
```

## <a name="use-environments-for-training"></a>교육용 환경 사용

학습 작업을 위한 환경을 사용 하려면 `environment` 작업 YAML 구성의 필드를 지정 합니다. 를 통해 등록 된 기존 Azure ML 환경을 참조 `environment: azureml:<environment-name>:<environment-version>` 하거나 환경 사양을 인라인으로 정의할 수 있습니다. 환경을 인라인으로 정의 하는 경우 및 필드를 지정 하지 마십시오 `name` `version` . 이러한 환경은 "익명" 환경으로 처리 되 고 사용자 환경 자산 레지스트리에서 추적 되지 않습니다.

학습 작업을 제출할 때 새 환경을 빌드하는 데 몇 분 정도 걸릴 수 있습니다. 시간은 필요한 종속성의 크기에 따라 달라집니다. 환경은 서비스에서 캐시됩니다. 환경 정의가 변경되지 않는 한, 전체 설정을 한 번만 하면 됩니다.

작업에서 환경을 사용 하는 방법에 대 한 자세한 내용은 [CLI를 사용 하 여 모델 학습 (v2)](how-to-train-cli.md)을 참조 하세요.

## <a name="use-environments-for-model-deployments"></a>모델 배포를 위한 환경 사용

또한 온라인 및 일괄 처리 점수 매기기를 위해 모델 배포에 대 한 환경을 사용할 수 있습니다. 이렇게 하려면 `environment` 배포 YAML 구성에서 필드를 지정 합니다.

배포에서 환경을 사용 하는 방법에 대 한 자세한 내용은 [관리 되는 온라인 끝점을 사용 하 여 machine learning 모델 배포 및 점수 매기기](how-to-deploy-managed-online-endpoints.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [CLI(v2)를 사용한 모델 학습(작업 만들기)](how-to-train-cli.md)
- [관리 되는 온라인 끝점을 사용 하 여 machine learning 모델 배포 및 점수 매기기](how-to-deploy-managed-online-endpoints.md)
- [환경 YAML 스키마 참조](reference-yaml-environment.md)
