---
title: Azure Machine Learning에 대한 Git 통합
titleSuffix: Azure Machine Learning
description: Azure Machine Learning이 학습 실행의 일부로 리포지토리, 분기 및 현재 커밋 정보를 추적하기 위해 로컬 Git 리포지토리와 통합하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 04/08/2021
ms.openlocfilehash: 3ff019488bff9d2e1088aae37902bb274f77470b
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129424600"
---
# <a name="git-integration-for-azure-machine-learning"></a>Azure Machine Learning에 대한 Git 통합

[Git](https://git-scm.com/)는 프로젝트를 공유하고 공동 작업할 수 있는 인기 있는 버전 제어 시스템입니다. 

Azure Machine Learning은 작업 추적을 위해 Git 리포지토리를 완벽하게 지원합니다. 공유 작업 영역 파일 시스템에 직접 리포지토리를 복제하거나 로컬 워크스테이션에서 Git를 사용하거나 CI/CD 파이프라인에서 Git를 사용할 수 있습니다.

Azure Machine Learning에 작업을 제출할 때 원본 파일이 로컬 git 리포지토리에 저장된 경우 리포지토리에 대한 정보는 학습 프로세스의 일부로 추적됩니다.

Azure Machine Learning은 로컬 git 리포지토리의 정보를 추적하므로 특정 중앙 리포지토리에 연결되지 않습니다. GitHub, GitLab, Bitbucket, Azure DevOps 또는 다른 모든 git 호환 서비스에서 리포지토리를 복제할 수 있습니다.

> [!TIP]
> Visual Studio Code를 사용하여 그래픽 사용자 인터페이스를 통해 Git과 상호 작용합니다. Visual Studio Code를 사용하여 Azure Machine Learning 원격 컴퓨팅 인스턴스에 연결하려면 [Visual Studio Code에서 Azure Machine Learning 컴퓨팅 인스턴스에 연결(미리 보기)](how-to-set-up-vs-code-remote.md)을 참조하세요.
>
> Visual Studio Code 버전 제어 기능에 대한 자세한 내용은 [VS Code에서 버전 제어 사용](https://code.visualstudio.com/docs/editor/versioncontrol) 및 [VS Code에서 GitHub 작업](https://code.visualstudio.com/docs/editor/github)을 참조하세요.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>작업 영역 파일 시스템에 Git 리포지토리 복제
Azure Machine Learning은 작업 영역에 있는 모든 사용자에게 공유 파일 시스템을 제공합니다.
이 파일 공유에 Git 리포지토리를 복제하려면 컴퓨팅 인스턴스를 만들고 [터미널을 여는](how-to-access-terminal.md) 것이 좋습니다.
터미널이 열리면 전체 Git 클라이언트에 액세스할 수 있고 Git CLI 환경을 통해 Git를 복제하고 사용할 수 있습니다.

사용자가 작업 분기에서 직접 충돌하지 않도록 사용자 디렉터리에 리포지토리를 복제하는 것이 좋습니다.

인증할 수 있는 Git 리포지토리를 복제할 수 있습니다(GitHub, Azure Repos, BitBucket 등).

복제에 대한 자세한 내용은 [Git CLI를 사용하는 방법](https://guides.github.com/introduction/git-handbook/)에 대한 가이드를 참조하세요.

## <a name="authenticate-your-git-account-with-ssh"></a>SSH를 사용하여 Git 계정 인증
### <a name="generate-a-new-ssh-key"></a>새 SSH 키 생성
1) Azure Machine Learning Notebook 탭에서 [터미널 창을 엽니다](./how-to-access-terminal.md).

2) 이메일 주소를 대체하여 아래 텍스트를 붙여넣습니다.

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

그러면 제공된 이메일을 레이블로 사용하여 새 ssh 키가 생성됩니다.

```
> Generating public/private rsa key pair.
```

3) "키를 저장할 파일을 입력하십시오"라는 메시지가 표시되면 Enter 키를 누릅니다. 기본 파일 위치를 적용합니다.

4) 기본 위치가 '/home/azureuser/.ssh'인지 확인하고 enter 키를 누릅니다. 그렇지 않은 경우 '/home/azureuser/.ssh' 위치를 지정합니다.

> [!TIP]
> SSH 키가 '/home/azureuser/.ssh'에 저장되어 있는지 확인하세요. 이 파일은 컴퓨팅 인스턴스에 저장되며 컴퓨팅 인스턴스 소유자만 액세스할 수 있습니다.

```
> Enter a file in which to save the key (/home/azureuser/.ssh/id_rsa): [Press enter]
```

5) 프롬프트에 보안 암호를 입력합니다. 보안을 강화하기 위해 SSH 키에 암호를 추가하는 것이 좋습니다.

```
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

### <a name="add-the-public-key-to-git-account"></a>Git 계정에 공개 키 추가
1) 터미널 창에서 공개 키 파일의 콘텐츠를 복사합니다. 키의 이름을 바꾼 경우 id_rsa.pub를 공개 키 파일 이름으로 바꿉니다.

```bash
cat ~/.ssh/id_rsa.pub
```
> [!TIP]
> **터미널에서 복사 및 붙여넣기**
> * Windows: 복사하려면 `Ctrl-Insert`, 붙여넣으려면 `Ctrl-Shift-v` 또는 `Shift-Insert`를 사용합니다.
> * Mac OS: `Cmd-c`: 복사, `Cmd-v`: 붙여넣기
> * FireFox/IE에서 클립보드 사용 권한을 제대로 지원하지 않을 수 있습니다.

2) 키 출력을 선택하여 클립보드에 복사합니다.

+ [GitHub](https://docs.github.com/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

+ [GitLab](https://docs.gitlab.com/ee/ssh/#adding-an-ssh-key-to-your-gitlab-account)

+ [Azure DevOps](/azure/devops/repos/git/use-ssh-keys-to-authenticate#step-2--add-the-public-key-to-azure-devops-servicestfs) **2단계** 에서 시작합니다.

+ [BitBucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/#SetupanSSHkey-ssh2) **4단계** 에서 시작합니다.

### <a name="clone-the-git-repository-with-ssh"></a>SSH를 사용하여 Git 리포지토리 복제

1) Git 리포지토리에서 SSH Git 복제 URL을 복사합니다.

2) SSH Git 리포지토리 URL을 사용하려면 아래 `git clone` 명령에 url을 붙여넣습니다. 이는 다음과 비슷합니다.

```bash
git clone git@example.com:GitUser/azureml-example.git
Cloning into 'azureml-example'...
```

다음과 같은 응답이 표시됩니다.

```bash
The authenticity of host 'example.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
```

SSH는 서버의 SSH 지문을 표시하고 확인하도록 요청할 수 있습니다. 표시된 지문이 SSH 공개 키 페이지의 지문 중 하나와 일치하는지 확인해야 합니다.

SSH는 [중간자(man-in-the-middle) 공격](/previous-versions/windows/it-pro/windows-2000-server/cc959354(v=technet.10))으로부터 보호하기 위해 알 수 없는 호스트에 연결할 때 이 지문을 표시합니다. 호스트의 지문을 수락한 후에는 지문이 변경되지 않는 한 SSH에서 다시 묻지 않습니다.

3) 연결을 계속할지 묻는 메시지가 표시되면 `yes`를 입력합니다. Git는 리포지토리를 복제하고 나중에 Git 명령에 대해 SSH를 사용하여 연결하도록 원본 원격을 설정합니다.

## <a name="track-code-that-comes-from-git-repositories"></a>Git 리포지토리에서 제공되는 코드 추적

Python SDK 또는 Machine Learning CLI에서 학습 실행을 제출하면 모델을 학습하는 데 필요한 파일이 작업 영역에 업로드됩니다. 개발 환경에서 `git` 명령을 사용할 수 있는 경우 업로드 프로세스에서 이 명령을 사용하여 파일이 git 리포지토리에 저장되었는지 확인합니다. 그렇다면 git 리포지토리의 정보는 학습 실행의 일부로도 업로드됩니다. 이 정보는 학습 실행을 위해 다음 속성에 저장됩니다.

| 속성 | 값을 가져오는 데 사용되는 Git 명령 | Description |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | 리포지토리가 복제된 URI입니다. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | 리포지토리가 복제된 URI입니다. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | 실행을 제출한 활성 분기입니다. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | 실행을 제출한 활성 분기입니다. |
| `azureml.git.commit` | `git rev-parse HEAD` | 실행을 위해 제출된 코드의 커밋 해시입니다. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | 실행을 위해 제출된 코드의 커밋 해시입니다. |
| `azureml.git.dirty` | `git status --porcelain .` | 분기/커밋이 더티인 경우 `True`이며, 그렇지 않으면 `false`입니다. |

이 정보는 예측 도구, 기계 학습 파이프라인 또는 스크립트 실행을 사용하는 실행을 위해 전송됩니다.

개발 환경의 git 리포지토리에 학습 파일이 없거나 `git` 명령을 사용할 수 없는 경우 git 관련 정보는 추적되지 않습니다.

> [!TIP]
> 개발 환경에서 git 명령을 사용할 수 있는지 확인하려면 셸 세션, 명령 프롬프트, PowerShell 또는 기타 명령줄 인터페이스를 열고 다음 명령을 입력합니다.
>
> ```
> git --version
> ```
>
> 설치되어 있는 경우 경로에서 `git version 2.4.1`과 유사한 응답을 받습니다. 개발 환경에 git를 설치하는 방법에 대한 자세한 내용은 [git 웹 사이트](https://git-scm.com/)를 참조하세요.

## <a name="view-the-logged-information"></a>로깅된 정보 보기

git 정보는 학습 실행을 위한 속성에 저장됩니다. Azure Portal, Python SDK 및 Azure CLI를 사용하여 이 정보를 볼 수 있습니다. 

### <a name="azure-portal"></a>Azure portal

1. [스튜디오 포털](https://ml.azure.com)에서 작업 영역을 선택합니다.
1. __실험__ 을 선택한 다음, 실험 중 하나를 선택합니다.
1. __실행 번호__ 열에서 실행 중 하나를 선택합니다.
1. __출력 + 로그__ 를 선택한 다음, __로그__ 및 __azureml__ 항목을 확장합니다. __###\_azure__ 로 시작하는 링크를 선택합니다.

로깅된 정보에는 다음 JSON과 유사한 텍스트가 포함됩니다.

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK

학습 실행을 제출한 후 [Run](/python/api/azureml-core/azureml.core.run%28class%29) 개체가 반환됩니다. 이 개체의 `properties` 특성에는 로깅된 git 정보가 포함됩니다. 예를 들어 다음 코드는 커밋 해시를 검색합니다.

```python
run.properties['azureml.git.commit']
```

### <a name="azure-cli"></a>Azure CLI

`az ml run` CLI 명령은 실행에서 속성을 검색하는 데 사용할 수 있습니다. 예를 들어 다음 명령은 `train-on-amlcompute`라는 실험에서 마지막으로 실행된 속성을 반환합니다.

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

자세한 내용은 [az ml run](/cli/azure/ml(v1)/run) 참조 설명서를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [모델 학습에 컴퓨팅 대상 사용](how-to-set-up-training-targets.md)
