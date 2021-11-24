---
title: Python을 사용하여 Azure에서 첫 번째 지속성 함수 만들기
description: Visual Studio Code를 사용하여 Python에서 Azure 지속성 함수를 만들고 게시합니다.
author: anthonychu
ms.topic: quickstart
ms.date: 12/23/2020
ms.reviewer: azfuncdf, antchu
ms.custom: mode-other
ms.openlocfilehash: d1eddd675d0f33c17fd652090b5dfa538cbaf0d0
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133065070"
---
# <a name="create-your-first-durable-function-in-python"></a>Python에서 첫 번째 지속성 함수 만들기

*Durable Functions* 는 서버리스 환경에서 상태 저장 함수를 작성할 수 있게 하는 [Azure Functions](../functions-overview.md)의 확장입니다. 확장은 상태, 검사점 및 다시 시작을 관리합니다.

이 문서에서는 Visual Studio Code Azure Functions를 사용하여 로컬로 “hello world” 지속성 함수를 만들고 테스트하는 방법에 대해 알아봅니다.  이 함수는 다른 함수에 대한 호출을 오케스트레이션하고 함께 연결합니다. 그런 후 함수 코드를 Azure에 게시합니다.

![Azure에서 지속성 함수 실행](./media/quickstart-python-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* [Visual Studio Code](https://code.visualstudio.com/download)를 설치합니다.

* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) VS Code 확장을 설치합니다.

* 최신 버전의 [Azure Functions Core Tools](../functions-run-local.md)가 있는지 확인합니다.

* Durable Functions를 사용하려면 Azure 스토리지 계정이 필요합니다. Azure 구독이 필요합니다.

* 3\.6, 3.7 또는 3.8 버전의 [Python](https://www.python.org/)이 설치되어 있는지 확인합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>로컬 프로젝트 만들기 

이 섹션에서는 Visual Studio Code를 사용하여 로컬 Azure Functions 프로젝트를 만듭니다. 

1. Visual Studio Code에서 F1(또는 <kbd>Ctrl/Cmd+Shift+P</kbd>)을 눌러 명령 팔레트를 엽니다. 명령 팔레트에서 `Azure Functions: Create New Project...`을 검색하여 선택합니다.

    ![함수 만들기](media/quickstart-python-vscode/functions-create-project.png)

1. 프로젝트에 대한 빈 폴더 위치를 선택하고, **선택** 을 누릅니다.

1. 지시에 따라 다음 정보를 제공합니다.

    | prompt | 값 | Description |
    | ------ | ----- | ----------- |
    | 함수 앱 프로젝트에 대한 언어를 선택합니다. | Python | 로컬 Python Functions 프로젝트를 만듭니다. |
    | 버전 선택 | Azure Functions v3 | 핵심 도구가 아직 설치되지 않은 경우에만 이 옵션이 표시됩니다. 이 경우 앱을 처음 실행할 때 핵심 도구가 설치됩니다. |
    | Python 버전 | Python 3.6, 3.7 또는 3.8 | VS Code는 선택한 버전으로 가상 환경을 만듭니다. |
    | 프로젝트의 첫 번째 함수에 대한 템플릿 선택 | 지금은 건너뛰기 | |
    | 프로젝트를 여는 방법을 선택합니다. | 현재 창에서 열기 | 선택한 폴더에서 VS Code를 다시 엽니다. |

필요한 경우 Visual Studio Code가 Azure Functions Core Tools를 설치합니다. 또한 폴더에서 함수 앱 프로젝트를 만듭니다. 이 프로젝트에는 [host.json](../functions-host-json.md) 및 [local.settings.json](../functions-develop-local.md#local-settings-file) 구성 파일이 포함되어 있습니다.

*requirements.txt* 파일도 루트 폴더에 생성됩니다. 함수 앱을 실행하는 데 필요한 Python 패키지를 지정합니다.

## <a name="install-azure-functions-durable-from-pypi"></a>PyPI에서 azure-functions-durable 설치

프로젝트를 만들 때 Azure Functions VS Code 확장은 선택한 Python 버전으로 가상 환경을 자동으로 만듭니다. 터미널에서 가상 환경을 활성화하고 Azure Functions 및 Durable Functions에 필요한 일부 종속성을 설치합니다.

1. 편집기에서 *requirements.txt* 를 열고 해당 콘텐츠를 다음으로 변경합니다.

    ```
    azure-functions
    azure-functions-durable
    ```

1. 현재 폴더(<kbd>Ctrl+Shift+`</kbd>)에서 편집기의 통합 터미널을 엽니다.

1. 통합 터미널에서 현재 폴더의 가상 환경을 활성화합니다.

    **Linux 또는 macOS**

    ```bash
    source .venv/bin/activate
    ```

    **Windows**

    ```powershell
    .venv\scripts\activate
    ```

    ![가상 환경 활성화](media/quickstart-python-vscode/activate-venv.png)

1. 가상 환경이 활성화된 통합 터미널에서 pip를 사용하여 방금 정의한 패키지를 설치합니다.

    ```bash
    python -m pip install -r requirements.txt
    ```

## <a name="create-your-functions"></a>함수 만들기

기본 Durable Functions 앱에는 다음 세 가지 함수가 포함되어 있습니다.

* *Orchestrator 함수* - 다른 함수를 오케스트레이션하는 워크플로를 설명합니다.
* *작업 함수* - 오케스트레이터 함수에 의해 호출되며, 작업을 수행하고 경우에 따라 반환합니다.
* *클라이언트 함수* - 오케스트레이터 함수를 시작하는 일반 Azure 함수입니다. 이 예제에서는 HTTP 트리거 함수를 사용합니다.

### <a name="orchestrator-function"></a>오케스트레이터 함수

템플릿을 사용하여 프로젝트에 지속성 함수 코드를 만듭니다.

1. 명령 팔레트에서 `Azure Functions: Create Function...`을 검색하여 선택합니다.

1. 지시에 따라 다음 정보를 제공합니다.

    | prompt | 값 | Description |
    | ------ | ----- | ----------- |
    | 함수의 템플릿 선택 | Durable Functions 오케스트레이터 | Durable Functions 오케스트레이션 만들기 |
    | 함수 이름 제공 | HelloOrchestrator | 지속성 함수의 이름 |

작업 함수를 조정하기 위한 오케스트레이터를 추가했습니다. 오케스트레이터 함수를 보려면 *HelloOrchestrator/\_\_init__.py* 를 엽니다. `context.call_activity`를 호출할 때마다 `Hello`라는 작업 함수가 호출됩니다.

다음으로, 참조된 `Hello` 작업 함수를 추가하겠습니다.

### <a name="activity-function"></a>작업 함수

1. 명령 팔레트에서 `Azure Functions: Create Function...`을 검색하여 선택합니다.

1. 지시에 따라 다음 정보를 제공합니다.

    | prompt | 값 | Description |
    | ------ | ----- | ----------- |
    | 함수의 템플릿 선택 | Durable Functions 작업 | 작업 함수 만들기 |
    | 함수 이름 제공 | 안녕하세요. | 작업 함수의 이름 |

오케스트레이터에서 호출하는 `Hello` 작업 함수를 추가했습니다. *Hello/\_\_init__.py* 를 열어 이름을 입력으로 사용하고 인사말을 반환하는지 확인합니다. 작업 함수에서는 데이터베이스 호출, 컴퓨팅 수행 등의 작업을 수행할 것입니다.

마지막으로, 오케스트레이션을 시작하는 HTTP 트리거 함수를 추가합니다.

### <a name="client-function-http-starter"></a>클라이언트 함수(HTTP 스타터)

1. 명령 팔레트에서 `Azure Functions: Create Function...`을 검색하여 선택합니다.

1. 지시에 따라 다음 정보를 제공합니다.

    | prompt | 값 | Description |
    | ------ | ----- | ----------- |
    | 함수의 템플릿 선택 | Durable Functions HTTP 스타터 | HTTP 스타터 함수 만들기 |
    | 함수 이름 제공 | DurableFunctionsHttpStart | 클라이언트 함수의 이름 |
    | 권한 부여 수준 | 익명 | 데모용으로 인증 없이 함수를 호출할 수 있도록 허용합니다. |

오케스트레이션을 시작하는 HTTP 트리거 함수를 추가했습니다. *DurableFunctionsHttpStart/\_\_init__.py* 를 열어 `client.start_new`를 사용하여 새 오케스트레이션을 시작하는지 확인합니다. `client.create_check_status_response`를 사용하여 새 오케스트레이션을 모니터링하고 관리하는 데 사용할 수 있는 URL이 포함된 HTTP 응답이 반환되는 것을 볼 수 있습니다.

이제 로컬로 실행하고 Azure에 배포할 수 있는 Durable Functions 앱이 생겼습니다.

## <a name="test-the-function-locally"></a>로컬에서 함수 테스트

Azure Functions Core Tools를 사용하면 로컬 개발 컴퓨터에서 Azure Functions 프로젝트를 실행할 수 있습니다. 설치되지 않은 경우 Visual Studio Code에서 처음으로 함수를 시작할 때 이러한 도구를 설치하라는 메시지가 표시됩니다.

1. 함수를 테스트하려면 `Hello` 작업 함수 코드(*Hello/\_\_init__.py*)에서 중단점을 설정합니다. F5 키를 누르거나 명령 팔레트에서 `Debug: Start Debugging`을 선택하여 함수 앱 프로젝트를 시작합니다. 핵심 도구의 출력이 **터미널** 패널에 표시됩니다.

    > [!NOTE]
    > 디버깅에 대한 자세한 내용은 [Durable Functions 진단](durable-functions-diagnostics.md#debugging)을 참조하세요.

1. Durable Functions를 사용하려면 Azure Storage 계정을 실행해야 합니다. VS Code에서 스토리지 계정을 선택하라는 메시지가 표시되면 **스토리지 계정 선택** 을 선택합니다.

    ![스토리지 계정 만들기](media/quickstart-python-vscode/functions-select-storage.png)

1. 메시지를 따라 다음 정보를 제공하여 Azure에서 새 스토리지 계정을 만듭니다.

    | prompt | 값 | Description |
    | ------ | ----- | ----------- |
    | 구독 선택 | *구독 이름* | Azure 구독 선택 |
    | 스토리지 계정 선택 | 새 스토리지 계정 만들기 |  |
    | 새 스토리지 계정의 이름 입력 | *고유 이름* | 만들 스토리지 계정의 이름 |
    | 리소스 그룹 선택 | *고유 이름* | 만들 리소스 그룹의 이름 |
    | 위치 선택 | *region* | 가까운 지역 선택 |

1. **터미널** 패널에서 HTTP 트리거 함수의 URL 엔드포인트를 복사합니다.

    ![Azure 로컬 출력](media/quickstart-python-vscode/functions-f5.png)

1. 브라우저 또는 [Postman](https://www.getpostman.com/), [cURL](https://curl.haxx.se/)과 같은 도구를 사용하여 HTTP 요청을 URL 엔드포인트로 보냅니다. 마지막 세그먼트를 오케스트레이터 함수의 이름(`HelloOrchestrator`)으로 바꿉니다. URL은 `http://localhost:7071/api/orchestrators/HelloOrchestrator` 형식입니다.

   응답은 지속성 오케스트레이션이 성공적으로 시작되었음을 알리는 HTTP 함수의 초기 결과입니다. 아직 오케스트레이션의 최종 결과는 아닙니다. 응답에는 몇 가지 유용한 URL이 포함되어 있습니다. 현재로는 오케스트레이션의 상태를 쿼리해보겠습니다.

1. `statusQueryGetUri`에 대한 URL 값을 복사하고 브라우저의 주소에 붙여넣은 후 요청을 실행합니다. 또는 Postman을 사용하여 GET 요청을 계속 실행할 수도 있습니다.

   요청은 상태에 대한 오케스트레이션 인스턴스를 쿼리합니다. 인스턴스가 완료되었음을 나타내고 지속성 함수의 출력 또는 결과를 포함하는 최종 응답을 가져와야 합니다. 다음과 같이 표시됩니다. 

    ```json
    {
        "name": "HelloOrchestrator",
        "instanceId": "9a528a9e926f4b46b7d3deaa134b7e8a",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2020-03-18T21:54:49Z",
        "lastUpdatedTime": "2020-03-18T21:54:54Z"
    }
    ```

1. 디버깅을 중지하려면 VS Code에서 <kbd>Shift+F5</kbd>를 누릅니다.

함수가 로컬 컴퓨터에서 제대로 실행되는지 확인한 후에 해당 프로젝트를 Azure에 게시해야 합니다.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Azure에서 함수 테스트

1. **출력** 패널에서 HTTP 트리거의 URL을 복사합니다. HTTP 트리거 함수를 호출하는 URL은 `http://<functionappname>.azurewebsites.net/api/orchestrators/HelloOrchestrator` 형식입니다.

2. HTTP 요청에 대한 이러한 새 URL을 브라우저의 주소 표시줄에 붙여넣습니다. 게시된 앱을 사용하는 경우 앞으로 같은 상태 응답을 가져와야 합니다.

## <a name="next-steps"></a>다음 단계

Visual Studio Code를 사용하여 Python 지속성 함수 앱을 만들고 게시했습니다.

> [!div class="nextstepaction"]
> [일반적인 지속성 함수 패턴에 대해 알아보기](durable-functions-overview.md#application-patterns)
