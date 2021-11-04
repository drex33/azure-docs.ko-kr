---
title: CLI를 Azure Machine Learning 환경 관리(v2)
titleSuffix: Azure Machine Learning
description: Machine Learning Azure CLI 확장을 사용하여 Azure ML 환경을 관리하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: mx-iao
ms.author: minxia
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: 351ef1c91ae665c170604543ac509562d2e2f0d0
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570185"
---
# <a name="manage-azure-machine-learning-environments-with-the-cli-v2-preview"></a>CLI를 Azure Machine Learning 환경 관리(v2)(미리 보기)

Azure Machine Learning 환경은 작업 또는 배포에 대한 실행 환경을 정의하고 코드에 대한 의존도를 캡슐화합니다. Azure ML 환경 사양을 사용하여 지정된 컴퓨팅 대상에서 학습 또는 점수 매기기 코드가 실행되는 Docker 컨테이너를 만듭니다. conda 사양, Docker 이미지 또는 Docker 빌드 컨텍스트에서 환경을 정의할 수 있습니다.

이 문서에서는 CLI(v2)를 사용하여 Azure ML 환경을 만들고 관리하는 방법을 알아봅니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>사전 요구 사항

- CLI를 사용하려면 Azure 구독이 있어야 합니다. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.
- [Machine Learning에 대한 Azure CLI 확장 설치 및 설정](how-to-configure-cli.md)

> [!TIP]
> 모든 기능을 갖춘 개발 환경을 위해서는 Visual Studio Code 및 [Azure Machine Learning 확장](how-to-setup-vs-code.md)을 사용하여 [Azure Machine Learning 리소스를 관리](how-to-manage-resources-vscode.md)하고 [기계 학습 모델을 학습](tutorial-train-deploy-image-classification-model-vscode.md)시킵니다.

### <a name="clone-examples-repository"></a>예제 리포지토리 복제

학습 예제를 실행하려면 먼저 예제 리포지토리를 복제하고 `cli` 디렉터리로 변경합니다.

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/misc.sh" id="git_clone":::

`--depth 1`은 리포지토리에 대한 최신 커밋만 복제하여 작업을 완료하는 시간을 줄입니다.

## <a name="curated-environments"></a>큐레이팅 환경

Azure ML 큐레이팅된 환경과 사용자 지정 환경이라는 두 가지 유형의 환경이 있습니다. 큐레이팅된 환경은 인기 있는 ML 프레임워크 및 도구를 포함하는 미리 정의된 환경입니다. 사용자 지정 환경은 사용자 정의이며 를 통해 만들 수 `az ml environment create` 있습니다.

큐레이팅된 환경은 Azure ML 제공되며 기본적으로 작업 영역에서 사용할 수 있습니다. Azure ML 최신 프레임워크 버전 릴리스를 통해 이러한 환경을 정기적으로 업데이트하고 버그 수정 및 보안 패치를 위해 유지 관리합니다. 캐시된 Docker 이미지로 지원되어 작업 준비 비용과 모델 배포 시간을 줄일 수 있습니다.

구문을 사용하여 특정 환경을 참조하여 이러한 큐레이팅된 환경을 학습 또는 배포에 사용할 수 `azureml:<curated-environment-name>:<version>` 있습니다. 이러한 큐레이팅된 환경을 백업하는 Dockerfiles를 수정하여 사용자 지정 환경에 대한 참조로 사용할 수도 있습니다.

Azure ML Studio UI에서 또는 을 통해 CLI(v2)를 사용하여 사용 가능한 큐레이팅된 환경 집합을 볼 수 `az ml environments list` 있습니다.

## <a name="create-an-environment"></a>환경 만들기

conda 사양, Docker 이미지 또는 Docker 빌드 컨텍스트에서 환경을 정의할 수 있습니다. YAML 사양 파일을 사용하여 환경을 구성하고 다음 CLI 명령을 사용하여 환경을 만듭니다.

```cli
az ml environment create --file my_environment.yml
```

Azure ML 환경에 대한 YAML 참조 설명서는 [CLI(v2) 환경 YAML 스키마](reference-yaml-environment.md)를 참조하세요.

### <a name="create-an-environment-from-a-docker-image"></a>Docker 이미지에서 환경 만들기

Docker 이미지에서 환경을 정의하려면 Docker Hub 또는 Azure Container Registry 같은 레지스트리에 호스트되는 이미지의 이미지 URI를 제공합니다. 

다음 예제는 Docker 이미지에서 정의된 환경에 대한 YAML 사양 파일입니다. Docker Hub 공식 PyTorch 리포지토리의 이미지는 `image` YAML 파일의 속성을 통해 지정됩니다.

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/environment/docker-image.yml":::

환경을 만들려면 다음을 수행합니다.

```cli
az ml environment create --file assets/environment/docker-image.yml
```

> [!TIP]
> Azure ML 일반적인 시스템 의존성을 가진 CPU 및 GPU Ubuntu Linux 기반 기본 이미지 집합을 유지 관리합니다. 예를 들어 GPU 이미지는 Miniconda, OpenMPI, CUDA, cuDNN 및 NCCL을 포함합니다. 사용자 환경에 이러한 이미지를 사용하거나 사용자 고유의 사용자 지정 이미지를 빌드할 때 해당 Dockerfiles를 참조로 사용할 수 있습니다.
>  
> 기본 이미지 및 해당 Dockerfiles 집합은 [AzureML-Containers 리포지블리](https://github.com/Azure/AzureML-Containers)를 참조하세요.

### <a name="create-an-environment-from-a-docker-build-context"></a>Docker 빌드 컨텍스트에서 환경 만들기

미리 빌드된 이미지에서 환경을 정의하는 대신 Docker [빌드 컨텍스트](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#understand-build-context)에서 정의할 수도 있습니다. 이렇게 하려면 빌드 컨텍스트로 사용될 디렉터리를 지정합니다. 이 디렉터리에는 Dockerfile 및 이미지를 빌드하는 데 필요한 다른 파일이 포함되어야 합니다.

다음 예제는 빌드 컨텍스트에서 정의된 환경에 대한 YAML 사양 파일입니다. 빌드 컨텍스트 폴더의 로컬 경로는 `build.local_path` 필드에 지정되며 해당 빌드 컨텍스트 폴더 내의 Dockerfile에 대한 상대 경로는 필드에 지정됩니다. `build.dockerfile_path` `build.dockerfile_path`YAML 파일에서 를 생략하면 Azure ML 빌드 컨텍스트의 루트에서 이라는 `Dockerfile` Dockerfile을 찾습니다.

이 예제에서 빌드 컨텍스트에는 라는 Dockerfile과 `Dockerfile` `requirements.txt` Python 패키지를 설치하기 위해 Dockerfile 내에서 참조되는 파일이 포함됩니다.

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/environment/docker-context.yml":::

환경을 만들려면 다음을 수행합니다.

```cli
az ml environment create --file assets/environment/docker-context.yml
```

Azure ML 환경이 만들어질 때 빌드 컨텍스트에서 이미지 빌드를 시작합니다. 빌드 상태를 모니터링하고 스튜디오 UI에서 빌드 로그를 볼 수 있습니다.

### <a name="create-an-environment-from-a-conda-specification"></a>conda 사양에서 환경 만들기

conda 환경에 대한 dependencies를 포함하는 표준 conda YAML 구성 파일을 사용하여 환경을 정의할 수 있습니다. 이 표준 형식에 대한 자세한 내용은 [수동으로 환경 만들기를](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually) 참조하세요.

이 환경에 대한 기본 Docker 이미지도 지정해야 합니다. Azure ML 제공된 Docker 이미지 위에 conda 환경을 빌드합니다.

다음 예제는 conda 사양에서 정의된 환경에 대한 YAML 사양 파일입니다. 여기서 Azure ML 환경 YAML 파일의 conda 파일에 대한 상대 경로는 속성을 통해 `conda_file` 지정됩니다. 또는 별도 파일에서 정의하는 대신 속성을 사용하여 conda 사양 인라인을 정의할 수 `conda_file` 있습니다.

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/assets/environment/docker-image-plus-conda.yml":::

환경을 만들려면 다음을 수행합니다.

```cli
az ml environment create --file assets/environment/docker-image-plus-conda.yml
```

Azure ML 작업 또는 배포에서 환경을 사용할 때 이 환경 사양에서 최종 Docker 이미지를 빌드합니다. 스튜디오 UI에서 환경 빌드를 수동으로 트리거할 수도 있습니다.

## <a name="manage-environments"></a>환경 관리

CLI(v2)는 Azure ML 환경 `az ml environment` 자산의 수명 주기를 관리하기 위한 명령 집합을 제공합니다.

### <a name="list"></a>목록

작업 영역의 모든 환경을 나열합니다.

```cli
az ml environment list
```

지정된 이름 아래에 모든 환경 버전을 나열합니다.

```cli
az ml environment list --name docker-image-example
```

### <a name="show"></a>표시

특정 환경의 세부 정보를 얻습니다.

```cli
az ml environment list --name docker-image-example --version 1
```

### <a name="update"></a>업데이트

특정 환경의 변경 가능한 속성을 업데이트합니다.

```cli
az ml environment update --name docker-image-example --version 1 --set description="This is an updated description."
```

> [!IMPORTANT]
> 환경의 경우 및 만 `description` `tags` 업데이트할 수 있습니다. 다른 모든 속성은 불변입니다. 이러한 속성을 변경해야 하는 경우 새 버전의 환경을 만들어야 합니다.

### <a name="delete"></a>DELETE

특정 환경 삭제:

```cli
az ml environment delete --name docker-image-example --version 1
```

## <a name="use-environments-for-training"></a>교육용 환경 사용

학습 작업에 환경을 사용하려면 작업 `environment` YAML 구성의 필드를 지정합니다. 를 통해 기존의 등록된 Azure ML 환경을 `environment: azureml:<environment-name>:<environment-version>` 참조하거나 환경 사양을 인라인으로 정의할 수 있습니다. 환경 인라인을 정의하는 경우 및 필드를 지정하지 마십시오. `name` `version` 이러한 환경은 "익명" 환경으로 처리되고 환경 자산 레지스트리에서 추적되지 않기 때문에 입니다.

학습 작업을 제출하면 새 환경 빌드에 몇 분 정도 걸릴 수 있습니다. 시간은 필요한 종속성의 크기에 따라 달라집니다. 환경은 서비스에서 캐시됩니다. 환경 정의가 변경되지 않는 한, 전체 설정을 한 번만 하면 됩니다.

작업에서 환경을 사용하는 방법에 대한 자세한 내용은 CLI를 사용하여 [모델 학습(v2)을 참조하세요.](how-to-train-cli.md)

## <a name="use-environments-for-model-deployments"></a>모델 배포에 환경 사용

온라인 및 일괄 처리 점수 매기기 모두에 모델 배포에 환경을 사용할 수도 있습니다. 이렇게 하려면 `environment` 배포 YAML 구성에서 필드를 지정합니다.

배포에서 환경을 사용하는 방법에 대한 자세한 내용은 [관리되는 온라인 엔드포인트를 사용하여 기계 학습 모델 배포 및 점수 매기기를 참조하세요.](how-to-deploy-managed-online-endpoints.md)

## <a name="next-steps"></a>다음 단계

- [CLI(v2)를 사용한 모델 학습(작업 만들기)](how-to-train-cli.md)
- [관리형 온라인 엔드포인트를 사용하여 기계 학습 모델 배포 및 점수 매기기](how-to-deploy-managed-online-endpoints.md)
- [환경 YAML 스키마 참조](reference-yaml-environment.md)
