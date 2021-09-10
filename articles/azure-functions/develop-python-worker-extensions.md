---
title: Azure Functions에 대한 Python 작업자 확장 개발
description: Azure에서 실행되는 Python 함수에 미들웨어 동작을 삽입할 수 있게 해주는 작업자 확장을 만들고 게시하는 방법을 알아봅니다.
ms.topic: how-to
author: hazhzeng
ms.author: hazeng
ms.date: 6/1/2021
ms.custom: devx-track-python
ms.openlocfilehash: 592562caa57980500d9bbc19b015a98e5604bb7a
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987948"
---
# <a name="develop-python-worker-extensions-for-azure-functions"></a>Azure Functions에 대한 Python 작업자 확장 개발

Azure Functions를 사용하면 사용자 지정 동작을 Python 함수 실행의 일부로 통합할 수 있습니다. 이 기능을 사용하면 고객이 자신의 함수 앱에서 간편하게 사용할 수 있는 비즈니스 논리를 만들 수 있습니다. 자세한 내용은 [Python 개발자 참조](functions-reference-python.md#python-worker-extensions)를 확인하세요.

이 자습서에서 학습할 방법은 다음과 같습니다. 
> [!div class="checklist"]
> * Azure Functions에 대한 애플리케이션 수준 Python 작업자 확장을 만듭니다. 
> * 고객이 수행하는 방식으로 앱에서 확장을 사용합니다. 
> * 사용할 확장을 패키지하고 게시합니다. 

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 요구 사항을 충족해야 합니다.

* [Python 3.6.x 이상](https://www.python.org/downloads/release/python-374/). Azure Functions에서 지원되는 Python 버전의 전체 목록을 확인하려면 [Python 개발자 가이드](functions-reference-python.md#python-version)를 참조하세요.

* [Azure Functions Core Tools](functions-run-local.md#v2) 버전 3.0.3568 이상.

* [지원되는 플랫폼](https://code.visualstudio.com/docs/supporting/requirements#_platforms) 중 하나에 [Visual Studio Code](https://code.visualstudio.com/)가 설치됩니다.

## <a name="create-the-python-worker-extension"></a>Python 작업자 확장 만들기

만든 확장은 콘솔 로그와 HTTP 응답 본문의 HTTP 트리거 호출 경과 시간을 보고합니다.

### <a name="folder-structure"></a>폴더 구조

확장 프로젝트의 폴더는 다음 구조와 유사해야 합니다.

```
<python_worker_extension_root>/
 | - .venv/
 | - python_worker_extension_timer/
 | | - __init__.py
 | - setup.py
 | - readme.md
```

| 폴더/파일 | 설명 |
| --- | --- |
| **.venv/** | (선택 사항) 로컬 개발에 사용되는 Python 가상 환경을 포함합니다. |
| **python_worker_extension/** | Python 작업자 확장의 소스 코드를 포함합니다. 이 폴더에는 PyPI에 게시할 기본 Python 모듈이 포함되어 있습니다. |
| **setup.py** | Python 작업자 확장 패키지의 메타데이터를 포함합니다. |
| **readme.md** | (선택 사항) 확장의 지침과 사용을 포함합니다. 이 콘텐츠는 PyPI 프로젝트 홈페이지에 설명으로 표시됩니다. |

### <a name="configure-project-metadata"></a>프로젝트 메타데이터 구성

먼저 패키지에 대한 필수 정보를 제공하는 `setup.py`를 만듭니다. 확장을 배포하고 고객 함수 앱에 올바르게 통합되었는지 확인하려면 `'azure-functions >= 1.7.0, < 2.0.0'`이 `install_requires` 섹션에 있는지 확인합니다.

필요에 따라 다음 템플릿에서 `author`, `author_email`, `install_requires`, `license`, `packages` 및 `url` 필드를 변경해야 합니다.

:::code language="python" source="~/azure-functions-python-worker-extension/setup.py":::

다음으로 애플리케이션 수준 범위에서 확장 코드를 구현합니다.

### <a name="implement-the-timer-extension"></a>타이머 확장 구현

`python_worker_extension_timer/__init__.py`에 다음 코드를 추가하여 애플리케이션 수준 확장을 구현합니다.

:::code language="python" source="~/azure-functions-python-worker-extension/python_worker_extension_timer/__init__.py":::

이 코드는 [AppExtensionBase](https://github.com/Azure/azure-functions-python-library/blob/dev/azure/functions/extension/app_extension_base.py)에서 상속되므로 확장은 앱에 있는 모든 함수에 적용됩니다. [FuncExtensionBase](https://github.com/Azure/azure-functions-python-library/blob/dev/azure/functions/extension/func_extension_base.py)에서 상속하여 확장을 함수 수준 범위에서 구현할 수도 있습니다.

`init` 메서드는 확장 클래스를 가져올 때 작업자가 호출하는 클래스 메서드입니다. 여기에서 확장 초기화 작업을 수행할 수 있습니다. 이 경우 각 함수의 호출 시작 시간을 기록할 수 있도록 해시 맵이 초기화됩니다.

`configure` 메서드는 고객 관련 메서드입니다. 추가 정보 파일에서 `Extension.configure()`를 호출해야 하는 경우 고객에게 알려줄 수 있습니다. 추가 정보는 확장 기능, 가능한 구성 및 확장 사용도 문서화해야 합니다. 이 예에서는 고객이 `HttpResponse`에서 경과 시간이 보고되는지 여부를 선택할 수 있습니다.

`pre_invocation_app_level` 메서드는 함수가 실행되기 전에 Python 작업자에 의해 호출됩니다. 함수에서 함수 컨텍스트 및 인수와 같은 정보를 제공합니다. 이 예에서 확장은 메시지를 로그하고 invocation_id를 기준으로 호출 시작 시간을 기록합니다.

마찬가지로 `post_invocation_app_level`은 함수 실행 후에 호출됩니다. 이 예에서는 시작 시간과 현재 시간을 기준으로 경과된 시간을 계산합니다. 또한 HTTP 응답의 반환 값을 덮어씁니다.

## <a name="consume-your-extension-locally"></a>로컬에서 확장 사용

확장을 만들었습니다. 이제 앱 프로젝트에서 확장을 사용하여 확장이 의도한 대로 작동하는지 확인할 수 있습니다. 

### <a name="create-an-http-trigger-function"></a>HTTP 트리거 함수 만들기

1. 앱 프로젝트에 새 폴더를 만들고 이 폴더로 이동합니다. 

1. Bash와 같은 적절한 셸에서 다음 명령을 실행하여 프로젝트를 초기화합니다.

    ```bash
    func init --python
    ```

1. 다음 명령을 사용하여 익명 액세스를 허용하는 새 HTTP 트리거 함수를 만듭니다.

    ```bash
    func new -t HttpTrigger -n HttpTrigger -a anonymous
    ```

### <a name="activate-a-virtual-environment"></a>가상 환경 활성화

1. 다음과 같이 OS에 따라 Python 가상 환경을 만듭니다.

    # <a name="linux"></a>[Linux](#tab/linux)
    ```bash
    python3 -m venv .venv
    ```
    # <a name="windows"></a>[Windows](#tab/windows)
    ```console
    py -m venv .venv
    ``` 
    ---

1. 다음과 같이 OS에 따라 Python 가상 환경을 활성화합니다.
    # <a name="linux"></a>[Linux](#tab/linux)
    ```bash
    source .venv/bin/activate
    ```
    # <a name="windows"></a>[Windows](#tab/windows)
    ```console
    .venv\Scripts\Activate.ps1
    ``` 
    ---

### <a name="configure-the-extension"></a>확장 구성

1. 다음 명령을 사용하여 함수 앱 프로젝트용 원격 패키지를 설치합니다. 
    
    ```bash
    pip install -r requirements.txt
    ```

1. 다음과 같이 편집 가능한 모드에서 로컬 파일 경로의 확장을 설치합니다.

    ```bash
    pip install -e <PYTHON_WORKER_EXTENSION_ROOT>
    ```

    이 예에서 `<PYTHON_WORKER_EXTENSION_ROOT>`를 확장 프로젝트 파일 위치로 바꿉니다.   
    고객이 확장을 사용하는 경우 다음 예와 같이 대신 확장 패키지 위치를 requirements.txt 파일에 추가합니다.

    # <a name="pypi"></a>[PyPI](#tab/pypi)
    ```python
    # requirements.txt
    python_worker_extension_timer==1.0.0
    ``` 
    # <a name="github"></a>[GitHub](#tab/github)
    
    ```python
    # requirements.txt
    git+https://github.com/Azure-Samples/python-worker-extension-timer@main
    ```
    ---

1. local.settings.json project 파일을 열고 다음 필드를 `Values`에 추가합니다.

    ```json
    "PYTHON_ENABLE_WORKER_EXTENSIONS": "1" 
    ```

    Azure에서 실행 중인 경우에는 대신 `PYTHON_ENABLE_WORKER_EXTENSIONS=1`을 [함수 앱의 앱 설정](functions-how-to-use-azure-function-app-settings.md#settings)에 추가합니다.

1. 다음 두 줄을 \_\_init.py\_\_의 `main` 함수 앞에 추가합니다.

    ```python
    from python_worker_extension_timer import TimerExtension
    TimerExtension.configure(append_to_http_response=True)
    ```

    이 코드는 `TimerExtension` 모듈을 가져오고 `append_to_http_response` 구성 값을 설정합니다.

### <a name="verify-the-extension"></a>확장 확인

1. 앱 프로젝트 루트 폴더에서 `func host start --verbose`를 사용하여 함수 호스트를 시작합니다. 함수의 로컬 엔드포인트가 출력에서 `https://localhost:7071/api/HttpTrigger`로 표시되어야 합니다.

1. 브라우저에서 GET 요청을 `https://localhost:7071/api/HttpTrigger`에 보냅니다. 추가된 요청의 **TimeElapsed** 데이터와 함께 다음과 같은 응답이 표시되어야 합니다. 

    <pre>
    This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response. (TimeElapsed: 0.0009996891021728516 sec)
    </pre>

## <a name="publish-your-extension"></a>확장 게시

확장을 만들고 확인했으면 남아 있는 다음 게시 작업을 완료해야 합니다.

> [!div class="checklist"]
> + 라이선스를 선택합니다.
> + readme.md 및 기타 문서를 만듭니다. 
> + 확장 라이브러리를 Python 패키지 레지스트리나 VCS(버전 제어 시스템)에 게시합니다.

# <a name="pypi"></a>[PyPI](#tab/pypi)

확장을 PyPI에 게시하려면 다음을 수행합니다.

1. 다음 명령을 실행하여 `twine` 또는 `wheel`을 기본 Python 환경이나 가상 환경에 설치합니다.

    ```bash
    pip install twine wheel
    ```

1. 확장 리포지토리에서 기존 `dist/` 폴더를 제거합니다.

1. 다음 명령을 실행하여 `dist/` 내에서 새 패키지를 생성합니다.

    ```bash
    python setup.py sdist bdist_wheel
    ```

1. 다음 명령을 실행하여 패키지를 PyPI에 업로드합니다.

    ```bash
    twine upload dist/*
    ```

    업로드 중에 PyPI 계정 자격 증명을 제공해야 할 수도 있습니다.

이러한 단계를 수행하면 고객이 자신의 requirements.txt에 패키지 이름을 포함하여 확장을 사용할 수 있습니다.

자세한 내용은 [공식 Python 패키징 자습서](https://packaging.python.org/tutorials/packaging-projects/)를 참조하세요.

# <a name="github"></a>[GitHub](#tab/github)

[이 샘플 리포지토리](https://github.com/Azure-Samples/python-worker-extension-timer)와 같이 setup.py 파일을 사용하여 확장 소스 코드를 GitHub 리포지토리에 게시할 수도 있습니다. 

pip에서 VCS 지원에 대한 자세한 내용은 [공식 pip VCS 지원 문서](https://pip.pypa.io/en/stable/cli/pip_install/#vcs-support)를 참조하세요.

---

## <a name="examples"></a>예제

+ 이 문서에서 완료한 샘플 확장 프로젝트를 [python_worker_extension_timer](https://github.com/Azure-Samples/python-worker-extension-timer) 샘플 리포지토리에서 볼 수 있습니다. 

+ OpenCensus 통합은 확장 인터페이스를 사용하여 원격 분석 추적을 Azure Functions Python 앱에 통합하는 오픈 소스 프로젝트입니다. 이 Python 작업자 확장 구현을 검토하려면 [opencensus-python-extensions-azure](https://github.com/census-ecosystem/opencensus-python-extensions-azure/tree/main/extensions/functions) 리포지토리를 확인합니다.

## <a name="next-steps"></a>다음 단계

Azure Functions Python 개발에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions Python 개발자 가이드](functions-reference-python.md)
* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 개발자 참조](functions-reference.md)
