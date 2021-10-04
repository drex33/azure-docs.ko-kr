---
title: 프라이빗 Python 패키지 사용
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 환경에서 프라이빗 Python 패키지를 안전하게 작업하는 방법을 알아봅니다.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: laobri
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: f9767fd35241804c511c3e3fa19051b04ad7c098
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428752"
---
# <a name="use-private-python-packages-with-azure-machine-learning"></a>Azure Machine Learning에서 프라이빗 Python 패키지 사용


이 문서에서는 Azure Machine Learning 내에서 프라이빗 Python 패키지를 안전하게 사용하는 방법을 알아봅니다. 프라이빗 Python 패키지 사용 사례는 다음과 같습니다.

 * 공개적으로 공유하지 않으려는 프라이빗 패키지를 개발했습니다.
 * 엔터프라이즈 방화벽 내에 저장된 패키지의 큐레이팅된 리포지토리를 사용하려고 합니다.

권장 방식은 단일 Azure Machine Learning 작업 영역에 대한 패키지가 몇 개 있거나 조직 내 모든 작업 영역에 대한 패키지의 전체 리포지토리가 있는지 여부에 따라 달라집니다.

[환경](/python/api/azureml-core/azureml.core.environment.environment) 클래스를 통해 프라이빗 패키지를 사용합니다. 환경 내에서 프라이빗 Python 패키지 등 사용할 Python 패키지를 선언합니다. 일반적인 Azure Machine Learning의 환경은 [환경 사용 방식](how-to-use-environments.md)을 참조하세요. 

## <a name="prerequisites"></a>사전 요구 사항

 * [Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml/install)
 * [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)

## <a name="use-small-number-of-packages-for-development-and-testing"></a>개발 및 테스트에 소수의 패키지 사용

단일 작업 영역에 대한 소수의 프라이빗 패키지에 정적 [`Environment.add_private_pip_wheel()`](/python/api/azureml-core/azureml.core.environment.environment#add-private-pip-wheel-workspace--file-path--exist-ok-false-) 메서드를 사용합니다. 이 방식을 사용하면 프라이빗 패키지를 작업 영역에 빠르게 추가할 수 있으므로 이 방식은 개발 및 테스트 용도로 매우 적합합니다.

파일 경로 인수를 로컬 휠 파일로 가리키고 ```add_private_pip_wheel``` 명령을 실행합니다. 이 명령은 작업 영역 내에서 패키지 위치를 추적하는 데 사용된 URL을 반환합니다. 스토리지 URL을 캡처하고 `add_pip_package()` 메서드를 전달합니다.

```python
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

내부적으로 Azure Machine Learning Service는 URL을 보안 SAS URL로 대체하므로 휠 파일은 안전하게 비공개 상태로 유지됩니다.

## <a name="use-a-repository-of-packages-from-azure-devops-feed"></a>Azure DevOps 피드에서 패키지 리포지토리 사용

기계 학습 애플리케이션에 사용될 Python 패키지를 적극적으로 개발하는 경우 이 패키지를 아티팩트로 Azure DevOps 리포지토리에 호스팅하고 피드로 게시할 수 있습니다. 이 방식을 사용하면 패키지 빌드에 사용되는 DevOps 워크플로를 Azure Machine Learning 작업 영역에 통합할 수 있습니다. Azure DevOps를 사용하여 Python 피드를 설정하는 방법은 [Azure Artifacts에서 Python 패키지 시작](/azure/devops/artifacts/quickstarts/python-packages)을 참조하세요.

이 방식에서는 개인용 액세스 토큰을 사용하여 리포지토리를 인증합니다. 같은 방식을 프라이빗 GitHub 리포지토리와 같은 토큰 기반 인증이 있는 다른 리포지토리에 적용할 수 있습니다. 

 1. Azure DevOps 인스턴스의 [PAT(개인용 액세스 토큰)를 만듭니다](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?tabs=preview-page#create-a-pat). 토큰 범위를 __패키징 > 읽기__ 로 설정합니다. 

 2. [Workspace.set_connection](/python/api/azureml-core/azureml.core.workspace.workspace#set-connection-name--category--target--authtype--value-) 메서드를 사용하여 Azure DevOps URL과 PAT를 작업 영역 속성으로 추가합니다.

     ```python
    from azureml.core import Workspace
    
    pat_token = input("Enter secret token")
    ws = Workspace.from_config()
    ws.set_connection(name="connection-1", 
        category = "PythonFeed",
        target = "https://<my-org>.pkgs.visualstudio.com", 
        authType = "PAT", 
        value = pat_token) 
     ```

 3. Azure Machine Learning 환경을 만들고 피드에서 Python 패키지를 추가합니다.
    
    ```python
    from azureml.core import Environment
    from azureml.core.conda_dependencies import CondaDependencies
    
    env = Environment(name="my-env")
    cd = CondaDependencies()
    cd.add_pip_package("<my-package>")
    cd.set_pip_option("--extra-index-url https://<my-org>.pkgs.visualstudio.com/<my-project>/_packaging/<my-feed>/pypi/simple")
    env.python.conda_dependencies=cd
    ```

이제 환경을 학습 실행이나 웹 서비스 엔드포인트 배포에서 사용할 수 있습니다. 환경을 구축할 경우 Azure Machine Learning Service는 PAT를 사용하여 기준 URL과 일치하는 피드를 인증합니다.

## <a name="use-a-repository-of-packages-from-private-storage"></a>프라이빗 스토리지에서 패키지 리포지토리 사용

조직 방화벽 내에 있는 Azure 스토리지 계정에서 패키지를 사용할 수 있습니다. 스토리지 계정은 큐레이팅된 패키지 세트나 공개적으로 사용 가능한 패키지의 미러를 저장할 수 있습니다.

이러한 프라이빗 스토리지를 설정하려면 [Azure Machine Learning 작업 영역과 관련 리소스 보호](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts)를 참조하세요. 또한 [VNet 뒤에 ACR(Azure Container Registry)을 배치](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr)해야 합니다.

> [!IMPORTANT]
> 프라이빗 패키지 리포지토리를 사용하여 모델을 학습시키거나 배포할 수 있으려면 이 단계를 완료해야 합니다.

이러한 구성을 완료하면 Azure BLOB 스토리지의 전체 URL을 사용하여 Azure Machine Learning 환경 정의에서 패키지를 참조할 수 있습니다.

## <a name="next-steps"></a>다음 단계

 * [Azure Machine Learning의 엔터프라이즈 보안](concept-enterprise-security.md)에 대해 자세히 알아보기