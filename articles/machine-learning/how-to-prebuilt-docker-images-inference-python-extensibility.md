---
title: 미리 빌드된 Docker 이미지 Python 확장성
titleSuffix: Azure Machine Learning
description: Python 패키지 확장성 솔루션을 통해 미리 빌드된 Docker 이미지 확장
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 05/25/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt
ms.openlocfilehash: 08139501886f580033aae46bdabd55ed300c5059
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114710011"
---
# <a name="python-package-extensibility-for-prebuilt-docker-images-preview"></a>미리 빌드된 Docker 이미지에 대한 Python 패키지 확장성(미리 보기)

[모델 유추용으로 미리 빌드된 Docker 이미지](concept-prebuilt-docker-images-inference.md)에는 많이 사용되는 기계 학습 프레임워크용 패키지가 포함되어 있습니다. __Docker 이미지를 다시 빌드하지 않고__ Python 패키지를 추가하는 데 사용할 수 있는 두 가지 방법이 있습니다.

* [동적 설치:](#dynamic) 이 방법은 Docker 컨테이너가 부팅될 때 [요구 사항](https://pip.pypa.io/en/stable/cli/pip_install/#requirements-file-format) 파일을 사용하여 Python 패키지를 자동으로 복원합니다.

    __빠른 프로토타입 생성__ 을 위해 이 방법을 고려합니다. 이미지가 시작되면 `requirements.txt` 파일을 사용하여 패키지가 복원됩니다. 이 방법은 시작되는 이미지가 증가하고 배포에서 요청을 처리할 수 있을 때까지 더 오래 기다려야 합니다.

* [미리 설치된 Python 패키지](#preinstalled): 미리 설치된 Python 패키지가 포함된 디렉터리를 제공합니다. 배포하는 동안 이 디렉터리는 사용할 항목 스크립트(`score.py`)에 대한 컨테이너에 탑재됩니다.

    __프로덕션 배포__ 에 이 방법을 사용합니다. 패키지가 포함된 디렉터리를 이미지에 탑재하기 때문에 배포에 공용 인터넷 액세스 권한이 없는 경우에도 사용할 수 있습니다. 예를 들어 보안 Azure Virtual Network에 배포된 경우입니다.

> [!IMPORTANT]
> 현재 Azure Machine Learning에서 미리 빌드된 Docker 이미지는 미리 보기에서 사용할 수 있습니다. 미리 보기 기능은 지원 또는 서비스 수준 계약의 보증 없이 “있는 그대로” 제공됩니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 작업 영역을 만드는 방법에 대한 자습서는 [Azure Machine Learning 시작](quickstart-create-resources.md)을 참조하세요.
* Azure Machine Learning [환경](how-to-use-environments.md) 사용과 관련한 지식
* Azure Machine Learning을 사용하여 [모델을 배포하는 위치 및 방법](how-to-deploy-and-where.md)에 대한 지식

<a id="dynamic"></a>

## <a name="dynamic-installation"></a>동적 설치

이 방법은 [requirements](https://pip.pypa.io/en/stable/cli/pip_install/#requirements-file-format) 파일을 사용하여 이미지가 시작될 때 Python 패키지를 자동으로 복원합니다.

requirements.txt를 통해 미리 빌드된 Docker 컨테이너 이미지를 확장하려면 다음 단계를 수행합니다.

1. `score.py` 스크립트와 함께 `requirements.txt` 파일을 만듭니다.
2. 필요한 **모든** 패키지를 `requirements.txt` 파일에 추가합니다.
3. Azure Machine Learning [환경](how-to-use-environments.md)의 `AZUREML_EXTRA_REQUIREMENTS_TXT` 환경 변수를 `requirements.txt` 파일의 위치로 설정합니다.

배포되면 점수 스크립트에 대해 패키지가 자동으로 복원됩니다.

> [!TIP]
> 프로토타입을 생성하는 동안에도 `requirements.txt`에 각 패키지 버전을 고정하는 것이 좋습니다.
> 예를 들어 `scipy` 또는 `scipy > 1.2.3` 대신 `scipy == 1.2.3`을 사용합니다.
> 정확한 버전을 고정하지 않고 `scipy`가 새 버전을 릴리스하는 경우 이로 인해 채점 스크립트가 중단되고 배포 및 크기 조정 중에 오류가 발생할 수 있습니다.

다음 예제에서는 `AZUREML_EXTRA_REQUIRMENTS_TXT` 환경 변수를 설정하는 방법을 보여 줍니다.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies 

myenv = Environment(name="my_azureml_env")
myenv.docker.enabled = True
myenv.docker.base_image = <MCR-path>
myenv.python.user_managed_dependencies = True

myenv.environment_variables = {
    "AZUREML_EXTRA_REQUIREMENTS_TXT": "requirements.txt"
}
```

다음 다이어그램은 동적 설치 프로세스의 시각적 표현입니다.

:::image type="content" source="./media/how-to-prebuilt-docker-images-inference-python-extensibility/dynamic-install-python-extend.svg" alt-text="동적 설치 프로세스 다이어그램":::

<a id="preinstalled"></a>

## <a name="pre-installed-python-packages"></a>사전 설치된 Python 패키지

이 방법은 사용자가 제공하는 디렉터리를 이미지에 탑재합니다. 그러면 이 디렉터리의 Python 패키지를 항목 스크립트(`score.py`)에서 사용할 수 있습니다.

미리 설치된 Python 패키지를 통해 미리 빌드된 Docker 컨테이너 이미지를 확장하려면 다음 단계를 수행합니다.

> [!IMPORTANT]
> Python 3.7과 호환되는 패키지를 사용해야 합니다. 모든 현재 이미지는 Python 3.7에 고정되어 있습니다.

1. [virtualenv](https://virtualenv.pypa.io/)를 사용하여 가상 환경을 만듭니다.

1. 종속성을 설치합니다. 예를 들어 `requirements.txt`에 종속성 목록이 있는 경우 이를 사용하면 `pip install -r requirements.txt`를 사용하거나 `pip install` 개별 종속성만 사용하여 설치할 수 있습니다.

1. `AZUREML_EXTRA_PYTHON_LIB_PATH` 환경 변수를 지정할 때는 올바른 사이트 패키지 디렉터리를 가리키는지 확인합니다. 이 디렉터리는 사용자의 환경 이름 및 Python 버전에 따라 달라집니다. 다음 코드에서는 `myenv` 및 Python 3.7 가상 환경의 경로를 설정하는 방법을 보여 줍니다.


    ```python
    from azureml.core import Environment
    from azureml.core.conda_dependencies import CondaDependencies 

    myenv = Environment(name='my_azureml_env')
    myenv.docker.enabled = True
    myenv.docker.base_image = <MCR-path>
    myenv.python.user_managed_dependencies = True

    myenv.environment_variables = {
        "AZUREML_EXTRA_PYTHON_LIB_PATH": "myenv/lib/python3.7/site-packages"
    }
    ```

다음 다이어그램은 미리 설치된 패키지 프로세스의 시각적 표현입니다.

:::image type="content" source="./media/how-to-prebuilt-docker-images-inference-python-extensibility/pre-install-python-extend.svg" alt-text="미리 설치된 패키지를 사용하는 프로세스의 다이어그램":::

### <a name="common-problems"></a>일반적인 문제

탑재 솔루션은 `myenv` 사이트 패키지 디렉터리에 모든 종속성이 포함된 경우에만 작동합니다. 로컬 환경이 다른 위치에 설치된 종속성을 사용하는 경우에는 이미지에서 사용할 수 없습니다.

다음은 이 문제를 일으킬 수 있는 몇 가지 사항입니다.

* `virtualenv`는 기본적으로 격리된 환경을 만듭니다. 가상 환경을 활성화한 후에는 __전역 의존성을 사용할 수 없습니다__.
* 전역 종속성을 가리키는 `PYTHONPATH` 환경 변수가 있는 경우 __가상 환경을 방해할 수 있습니다__. 환경을 활성화한 후 `pip list` 및 `pip freeze`를 실행하여 사용자 환경에 원치 않는 종속성이 없는지 확인합니다.
* __Conda 및 `virtualenv` 환경이 방해할 수 있습니다__. [Conda 환경](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html) 및 `virtualenv`를 동시에 사용하지 않도록 합니다.

## <a name="limitations"></a>제한 사항

### <a name="modelpackage"></a>Model.package()

* [Model.package()](/python/api/azureml-core/azureml.core.model(class)) 메서드를 사용하면 Docker 이미지 또는 Dockerfile 빌드 컨텍스트의 형태로 모델 패키지를 만들 수 있습니다. 미리 빌드된 유추 Docker 이미지와 함께 Model.package()를 사용하면 루트가 아닌 사용자를 루트 사용자로 변경하는 중간 이미지 빌드가 트리거됩니다.

* Python 패키지 확장성 솔루션을 사용하는 것이 좋습니다. 다른 의존성(예: `apt` 패키지)이 필요한 경우 [유추 이미지에서 확장되는 고유한 Dockerfile](how-to-extend-prebuilt-docker-image-inference.md#buildmodel)을 만듭니다.

## <a name="frequently-asked-questions"></a>질문과 대답

* requirements.txt 확장성 접근 방식에서 파일 이름은 반드시 `requirements.txt`여야 하나요?

        
    ```python
    myenv.environment_variables = {
        "AZUREML_EXTRA_REQUIREMENTS_TXT": "name of your pip requirements file goes here"
    }
    ```

* `requirements.txt` 접근 방식과 *탑재 방식* 을 요약할 수 있나요?

    *requirements.txt* 접근 방식을 사용하여 프로토타입 생성을 시작합니다.
    몇 번 반복한 후에 성공적인 모델 배포에 필요한 패키지(및 버전)에 대해 확신하는 경우 *탑재 솔루션* 으로 전환합니다.
        
    자세한 비교 내용은 다음과 같습니다.

    | 비교 항목 | Requirements.txt(동적 설치) | 패키지 탑재 |
    | ----- | ----- | ------ |
    | 해결 방법  | 컨테이너가 시작될 때 지정된 패키지를 설치하는 `requirements.txt`를 만듭니다. | 모든 종속성을 사용하여 로컬 Python 환경을 만듭니다. 런타임에 이 디렉터리를 컨테이너에 탑재합니다. |
    | 패키지 설치           | 추가 설치 없음(pip가 이미 설치되어 있다고 가정)                                                                                                          | 가상 환경 또는 conda 환경 설치                                                                                   |
    | 가상 환경 설정              | 사용자가를 `requirements.txt`를 만들기 위해 필요에 따라 pip를 동결하여 현재 로컬 사용자 환경을 끌어올 수 있으므로 추가적인 가상 환경 설정은 필요하지 않습니다. | 정리된 가상 환경을 설정해야 하므로, 현재 사용자 로컬 환경에 따라 추가 단계가 필요할 수 있습니다.                        |
    | [디버깅](how-to-inference-server-http.md)                 | 종속성이 명확하게 나열되므로 서버를 쉽게 설정하고 디버그할 수 있습니다. | 정리되지 않은 가상 환경은 서버를 디버그할 때 문제가 발생할 수 있습니다. 예를 들어 환경이나 사용자 코드에서 오류가 발생하는 경우 명확하지 않을 수 있습니다. |
    | 스케일 아웃 중의 일관성 | 외부 PyPi 패키지 및 해당 종속성을 고정하는 사용자에 종속되므로 일관성이 없습니다. 이러한 외부 다운로드는 신뢰도가 낮습니다.                                 | 사용자 환경에만 의존하므로 일관성 문제가 없습니다.                                                                             |

* 내 `requirements.txt` 및 탑재된 종속성 디렉터리를 컨테이너에서 찾을 수 없는 이유는 무엇입니까?

    로컬로 환경 변수가 올바르게 설정되었는지 확인합니다. 다음으로, 지정된 경로가 올바른 맞춤법으로 지정되고 존재하는지 확인합니다.
    [유추 구성](/python/api/azureml-core/azureml.core.model.inferenceconfig#constructor) 생성자에서 원본 디렉터리를 올바르게 설정했는지 확인합니다.

* 미리 빌드된 유추 Docker 이미지에서 Python 패키지 종속성을 재정의할 수 있나요?

    예. 유추 이미지에 이미 설치된 다른 버전의 Python 패키지를 사용하려는 경우 확장성 솔루션은 해당 버전을 고려합니다. 두 버전 간에 충돌이 없는지 확인합니다.

## <a name="best-practices"></a>모범 사례

* [등록된 모델 로드](how-to-deploy-advanced-entry-script.md#load-registered-models) 문서를 참조하세요. 모델 디렉터리를 등록할 때 채점 스크립트, 탑재된 종속성 디렉터리 또는 해당 디렉터리 내의 `requirements.txt`를 포함하지 않도록 하세요.


* 등록된 모델 또는 로컬 모델을 로드하는 방법에 대한 자세한 내용은 [배포 위치 및 방법](how-to-deploy-and-where.md?tabs=azcli#define-a-dummy-entry-script)을 참조하세요.

## <a name="bug-fixes"></a>버그 수정

### <a name="2021-07-26"></a>2021-07-26

* 이제 `AZUREML_EXTRA_REQUIREMENTS_TXT` 및 `AZUREML_EXTRA_PYTHON_LIB_PATH`는 항상 점수 매기기 스크립트의 디렉터리를 기준으로 합니다.
예를 들어 requirements.txt와 점수 매기기 스크립트가 둘 다 **my_folder** 에 있는 경우 `AZUREML_EXTRA_REQUIREMENTS_TXT`를 requirements.txt로 설정해야 합니다. `AZUREML_EXTRA_REQUIREMENTS_TXT`가 더 이상 **my_folder/requirements.txt** 로 설정되지 않습니다.

## <a name="next-steps"></a>다음 단계

모델 배포에 대한 자세한 내용은 [모델 배포 방법](how-to-deploy-and-where.md)을 참조하세요.

미리 빌드된 Docker 이미지 배포 문제를 해결하는 방법을 알아보려면 [미리 빌드된 Docker 이미지 배포 문제 해결 방법](how-to-troubleshoot-prebuilt-docker-image-inference.md)을 참조하세요.
