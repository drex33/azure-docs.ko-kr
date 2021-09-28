---
title: 작업 만들기 UI를 사용하여 학습 작업 만들기
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 스튜디오에서 작업 만들기 UI를 사용하여 학습 작업을 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devplatv2
author: wenxwei
ms.author: wenxwei
ms.date: 06/22/2021
ms.reviewer: laobri
ms.openlocfilehash: 94eb2beb95a43e422e8bff6c3ab53f7d2e6c208b
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094712"
---
# <a name="create-a-training-job-with-the-job-creation-ui-preview"></a>작업 만들기 UI를 사용하여 학습 작업 만들기(미리 보기)

Azure Machine Learning을 사용하여 학습 작업을 만드는 방법에는 여러 가지가 있습니다. CLI([CLI(v2)를 사용하여 모델 학습(작업 만들기)(미리 보기)](how-to-train-cli.md) 참조), REST API([REST를 사용하여 모델 학습(미리 보기)](how-to-train-with-rest.md) 참조)를 사용할 수 있고, UI를 사용하여 직접 학습 작업을 만들 수도 있습니다. 이 문서에서는 고유한 데이터와 코드를 사용하여 Azure Machine Learning 스튜디오에서 작업 만들기 UI를 통해 기계 학습 모델을 학습시키는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* Azure Machine Learning 작업 영역 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요. 

* Azure Machine Learning에서 작업의 개념 이해. [작업 소개](how-to-train-cli.md#introducing-jobs)를 참조하세요.

## <a name="get-started"></a>시작

1. [Azure Machine Learning Studio](https://ml.azure.com)에 로그인합니다. 

1. 구독 및 작업 영역을 선택합니다.
 
* 홈페이지에서 작업 만들기 UI를 시작할 수 있습니다. **새로 만들기** 를 클릭하고 **작업** 을 선택합니다. 
[![Azure Machine Learning 스튜디오 홈페이지](media/how-to-train-with-ui/home-entry.png)](media/how-to-train-with-ui/home-entry.png)

* 또는 왼쪽 창에서 작업 만들기를 시작할 수 있습니다. **+새로 만들기** 를 클릭하고 **작업** 을 선택합니다. 
[![Azure Machine Learning 스튜디오 왼쪽 탐색](media/how-to-train-with-ui/left-nav-entry.png)](media/how-to-train-with-ui/left-nav-entry.png)

* 또는 실험 페이지에 있는 경우 **모든 실행** 탭으로 이동하고 **작업 만들기** 를 클릭할 수 있습니다. 
[![작업 만들기 UI의 실험 페이지 시작](media/how-to-train-with-ui/experiment-entry.png)](media/how-to-train-with-ui/experiment-entry.png)

이러한 옵션은 모두 학습 작업을 구성하고 만드는 마법사가 포함된 작업 만들기 패널로 이동합니다. 

## <a name="select-compute-resources"></a>컴퓨팅 리소스 선택

작업 만들기 UI의 첫 번째 단계는 작업을 실행할 컴퓨팅 대상을 선택하는 것입니다. 작업 만들기 UI는 여러 가지 컴퓨팅 형식을 지원합니다.

| 컴퓨팅 형식 | 소개 | 
| --- | --- | 
| 컴퓨팅 인스턴스 | [Azure Machine Learning 컴퓨팅 인스턴스란?](concept-compute-instance.md) | 
| 컴퓨팅 클러스터 | [컴퓨팅 클러스터란?](how-to-create-attach-compute-cluster.md#what-is-a-compute-cluster) | 
| 연결된 Kubernetes 클러스터 | [Azure Arc 지원 기계 학습 구성(미리 보기)](how-to-attach-arc-kubernetes.md). | 

1. 컴퓨팅 형식을 선택합니다.
1. 기존 컴퓨팅 리소스를 선택합니다. 드롭다운에는 선택에 도움이 되는 노드 정보와 SKU 형식이 표시됩니다.
1. 컴퓨팅 클러스터 또는 Kubernetes 클러스터의 경우 **인스턴스 수** 에서 작업에 대해 원하는 노드 수를 지정할 수도 있습니다. 기본 인스턴스 수는 1개입니다. 
1. 선택 사항에 만족하면 **다음** 을 선택합니다. 
 [![컴퓨팅 클러스터 선택](media/how-to-train-with-ui/compute-cluster.png)](media/how-to-train-with-ui/compute-cluster.png)

Azure Machine Learning을 처음 사용하는 경우 새 컴퓨팅을 만들기 위한 빈 목록과 링크가 표시됩니다. 

 [![새 컴퓨팅 인스턴스 만들기](media/how-to-train-with-ui/create-new-compute.png)](media/how-to-train-with-ui/create-new-compute.png)

다양한 형식을 만드는 방법에 관한 자세한 내용은 다음을 참조하세요.

| 컴퓨팅 형식 | 방법 | 
| --- | --- | 
| 컴퓨팅 인스턴스 | [Azure Machine Learning 컴퓨팅 인스턴스 만들고 관리](how-to-create-manage-compute-instance.md) | 
| 컴퓨팅 클러스터 | [Azure Machine Learning 컴퓨팅 클러스터 만들기](how-to-create-attach-compute-cluster.md) | 
| 연결된 Kubernetes 클러스터 | [Azure Arc 지원 Kubernetes 클러스터 연결](how-to-attach-arc-kubernetes.md) | 

## <a name="specify-the-necessary-environment"></a>필요한 환경 지정

컴퓨팅 대상을 선택한 후 작업의 런타임 환경을 지정해야 합니다. 작업 만들기 UI는 세 가지 유형의 환경을 지원합니다.

* 큐레이팅 환경
* 사용자 지정 환경
* 컨테이너 레지스트리 이미지 

### <a name="curated-environments"></a>큐레이팅 환경

큐레이팅 환경은 일반적인 ML 워크로드에서 사용되는 Python 패키지의 Azure 정의 컬렉션입니다. 큐레이팅 환경은 기본적으로 작업 영역에서 사용할 수 있습니다. 이러한 환경은 실행 준비 오버헤드를 줄일 수 있는 캐시된 Docker 이미지에서 지원됩니다. “큐레이팅 환경” 페이지에 표시되는 카드에는 각 환경의 세부 정보가 표시됩니다. 자세한 내용은 [Azure Machine Learning 큐 레이트 environment](resource-curated-environments.md)를 참조 하세요.

 [![큐레이팅 환경](media/how-to-train-with-ui/curated-env.png)](media/how-to-train-with-ui/curated-env.png)

### <a name="custom-environments"></a>사용자 지정 환경

사용자 지정 환경은 직접 지정한 환경입니다. 환경을 지정하거나 이미 만든 환경을 다시 사용할 수 있습니다. 자세한 내용은 [Azure Machine Learning 스튜디오에서 소프트웨어 환경 관리(미리 보기)](how-to-manage-environments-in-studio.md#create-an-environment)를 참조하세요. 

### <a name="container-registry-image"></a>컨테이너 레지스트리 이미지

Azure Machine Learning 큐레이팅 환경을 사용하거나 고유한 사용자 지정 환경을 지정하지 않으려는 경우 [Docker Hub](https://hub.docker.com/)와 같은 퍼블릭 컨테이너 레지스트리에서 docker 이미지를 사용할 수 있습니다. 이미지가 프라이빗 컨테이너에 있는 경우 **프라이빗 컨테이너 레지스트리입니다.** 를 설정/해제합니다. 프라이빗 레지스트리는 Azure가 이미지를 가져올 수 있도록 유효한 사용자 이름과 암호를 입력해야 합니다. 
[![컨테이너 레지스트리 이미지](media/how-to-train-with-ui/container-registry-image.png)](media/how-to-train-with-ui/container-registry-image.png)

## <a name="configure-your-job"></a>작업 구성

환경을 지정한 후 추가 설정을 사용하여 작업을 구성할 수 있습니다. 

|필드| Description|
|------| ------|
|작업 이름| 작업 이름 필드는 작업을 고유하게 식별하는 데 사용됩니다. 또한 작업의 표시 이름으로 사용됩니다. 이 필드 설정은 선택 사항입니다. 아무것도 입력하지 않으면 Azure에서 작업의 GUID 이름을 생성합니다. 참고: 작업 이름은 고유해야 합니다.|
|실험 이름| 이를 사용하여 Azure Machine Learning 스튜디오에서 작업을 구성할 수 있습니다. 각 작업의 실행 기록은 스튜디오의 “실험” 탭에서 해당 실험에 따라 구성됩니다. 기본적으로 Azure는 작업을 **기본** 실험에 넣습니다.|
|코드| 머신에서 코드 파일이나 폴더를 업로드하거나 작업 영역의 기본 Blob 스토리지에서 코드 파일을 업로드할 수 있습니다. Azure는 사용자가 선택한 후 업로드할 파일을 표시합니다. |
|명령| 실행할 명령입니다. 명령줄 인수를 명령에 명시적으로 기록하거나, 다음 섹션에 설명된 대로 중괄호 표기법을 사용하여 다른 섹션(특히 **입력**)에서 유추할 수 있습니다.|
|입력| 입력 바인딩을 지정합니다. 다음 세 가지 유형의 입력을 지원합니다. 1) Azure Machine Learning 등록된 데이터 세트, 2) 작업 영역 기본 Blob 스토리지, 3) 로컬 파일 업로드. 여러 입력을 추가할 수 있습니다. |
|환경 변수| 환경 변수를 설정하여 작업의 동적 구성을 제공할 수 있습니다. 여기에 변수와 값을 추가할 수 있습니다.|
|태그| 구성에 도움이 되도록 작업에 태그를 추가합니다.|

### <a name="specify-code-and-inputs-in-the-command-box"></a>명령 상자에서 코드 및 입력 지정

#### <a name="code"></a>코드

명령은 업로드된 코드 폴더의 루트 디렉터리에서 실행됩니다. 코드 파일이나 폴더를 선택한 후 업로드할 파일을 볼 수 있습니다. 진입점을 포함하는 코드의 상대 경로를 복사하고 **명령을 입력하여 작업을 시작합니다.** 라는 상자에 붙여넣습니다. 

코드가 루트 디렉터리에 있으면 명령에서 해당 코드를 직접 참조할 수 있습니다. 예: `python main.py`

코드가 루트 디렉터리에 없으면 상대 경로를 사용해야 합니다. 예를 들어, [단어 언어 모델](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/train/pytorch/word-language-model)의 구조는 다음과 같습니다.

```tree
.
├── job.yml
├── data
└── src
    └── main.py
```
여기에서 소스 코드는 `src` 하위 디렉터리에 있습니다. 명령은 `python ./src/main.py`(+ 기타 명령줄 인수)입니다.

[![명령에서 코드 참조](media/how-to-train-with-ui/code-command.png)](media/how-to-train-with-ui/code-command.png)

#### <a name="inputs"></a>입력

입력 바인딩을 수행하는 방법으로 다음 두 가지가 있습니다. 

* 입력 이름: 명령에서 입력을 사용하는 경우 입력 이름을 지정해야 합니다. 입력 변수를 나타내려면 `{inputs.input_name}` 형식을 사용합니다. 예: `{inputs.wiki}` 그런 다음, 명령에서 이를 참조할 수 있습니다(예: `--data {inputs.wiki}`).
[![명령에서 입력 이름 참조](media/how-to-train-with-ui/input-command-name.png)](media/how-to-train-with-ui/input-command-name.png)

* 경로: `--data .path`를 사용하여 클라우드 위치를 지정합니다. 경로는 **컴퓨팅의 경로** 에 입력하는 값입니다.
[![명령에서 입력 경로 참조](media/how-to-train-with-ui/input-command-path.png)](media/how-to-train-with-ui/input-command-path.png)

>[!NOTE] 
>**작업을 시작하는 명령** 에서 **컴퓨팅의 경로** 값에 기간을 추가해야 합니다. 예를 들어, `/data/wikitext-2`는 `./data/wikitext-2`가 됩니다.

## <a name="review-and-create"></a>검토 및 만들기 

작업을 구성한 후 **다음** 을 선택하여 **검토** 페이지로 이동합니다. 설정을 수정하려면 연필 아이콘을 선택하고 변경합니다. 

**YAML 사양 보기** 를 선택하여 이 작업 구성에서 생성되는 yaml 파일을 검토하고 다운로드할 수 있습니다. 이 작업 yaml 파일을 사용하여 CLI(v2)에서 작업을 제출할 수 있습니다. [CLI(v2)를 사용하여 모델 학습(작업 만들기)(미리 보기)](how-to-train-cli.md)을 참조하세요. [![yaml 사양 보기](media/how-to-train-with-ui/view-yaml.png)](media/how-to-train-with-ui/view-yaml.png)
[![Yaml 사양](media/how-to-train-with-ui/yaml-spec.png)](media/how-to-train-with-ui/yaml-spec.png)

작업을 시작하려면 **만들기** 를 선택합니다. 작업이 만들어지면 Azure는 학습 작업을 모니터링하고 관리할 수 있는 실행 세부 정보 페이지를 표시합니다. 

## <a name="next-steps"></a>다음 단계

* [관리형 온라인 엔드포인트를 통해 기계 학습 모델 배포 및 점수 매기기(미리 보기)](how-to-deploy-managed-online-endpoints.md)

* [CLI(v2)를 사용하여 모델 학습(작업 만들기)(미리 보기)](how-to-train-cli.md)
