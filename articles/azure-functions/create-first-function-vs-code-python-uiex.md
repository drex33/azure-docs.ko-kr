---
title: Visual Studio Code를 사용하여 Python 함수 만들기 - Azure Functions
description: Python 함수를 만든 다음, Visual Studio Code의 Azure Functions 확장을 사용하여 Azure Functions의 서버리스 호스팅에 로컬 프로젝트를 게시하는 방법을 알아봅니다.
ms.topic: quickstart
ms.date: 11/04/2020
ms.custom: devx-track-python, mode-other
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3dcbb033056dd7ab13bc32736a27fa8733013a66
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133076223"
---
# <a name="quickstart-create-a-function-in-azure-with-python-using-visual-studio-code"></a>빠른 시작: Visual Studio Code를 통해 Python을 사용하여 Azure에서 함수 만들기

> [!div class="op_single_selector" title1="함수 언어를 선택합니다. "]
> - [Python](create-first-function-vs-code-python.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [기타(Go/Rust)](create-first-function-vs-code-other.md)

이 문서에서는 Visual Studio Code를 사용하여 HTTP 요청에 응답하는 Python 함수를 만듭니다. 코드를 로컬에서 테스트한 후 다음에 배포합니다. <abbr title="서버의 모든 세부 정보가 애플리케이션 개발자에게 투명하게 공개되어 코드 배포 및 관리 프로세스가 간소화되는 런타임 컴퓨팅 환경.">서버를 사용하지 않음</abbr> 환경 <abbr title="애플리케이션을 위한 저렴한 서버리스 컴퓨팅 환경을 제공하는 Azure의 서비스.">Azure 기능</abbr>.

이 빠른 시작을 완료하면 Azure 계정에서 USD 센트 이하의 작은 비용이 발생합니다.

이 문서의 [CLI 기반 버전](create-first-function-cli-python.md)도 있습니다.

## <a name="1-prepare-your-environment"></a>1. 환경 준비

시작하기 전에 다음 요구 사항을 갖추었는지 확인합니다.

+ Azure <abbr title="Azure 사용량에 대한 청구 정보를 유지 관리하는 프로필.">account</abbr> 활성 <abbr title="Azure에서 리소스를 관리하는 기본 조직 구조이며 일반적으로 조직 내의 개인 또는 부서와 연결됩니다.">subscription</abbr>. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 버전 3.x.

+ [Python 3.8](https://www.python.org/downloads/release/python-381/), [Python 3.7](https://www.python.org/downloads/release/python-375/), [Python 3.6](https://www.python.org/downloads/release/python-368/)은 Azure Functions(x64)에서 지원됩니다.

+ [지원되는 플랫폼](https://code.visualstudio.com/docs/supporting/requirements#_platforms) 중 하나인 [Visual Studio Code](https://code.visualstudio.com/).

+ Visual Studio Code용 [Python 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python).  

+ Visual Studio Code용 [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

<hr/>
<br/>

## <a name="2-create-your-local-project"></a>2. <a name="create-an-azure-functions-project"></a>로컬 프로젝트 만들기

1. Azure 아이콘을 선택합니다. <abbr title="Visual Studio Code 창의 왼쪽에 있는 아이콘의 수직 그룹.">작업 표시줄</abbr>그런 다음, **Azure: Functions** 영역에서 **새 프로젝트 만들기...** 아이콘을 선택합니다.

    ![새 프로젝트 만들기 선택](./media/functions-create-first-function-vs-code/create-new-project.png)

1. 프로젝트 작업 영역에 대한 디렉터리 위치를 선택하고 **선택** 을 선택합니다.

    > [!NOTE]
    > 다음 단계는 작업 영역 외부에서 완료하도록 설계되었습니다. 이 경우 작업 영역에 포함된 프로젝트 폴더를 선택하지 마십시오.

1. 프롬프트에서 다음 정보를 제공합니다.

    + **함수 프로젝트에 대한 언어 선택**: `Python`을 선택합니다.

    + **가상 환경을 만들기 위한 Python 별칭 선택**: Python 인터프리터의 위치를 선택합니다. 위치가 표시되지 않으면 Python 이진에 대한 전체 경로를 입력합니다.  

    + **프로젝트의 첫 번째 함수에 대한 템플릿 선택**: `HTTP trigger`을 선택합니다.

    + **함수 이름 입력**: `HttpExample`.

    + **권한 부여 수준**: 누구나 함수 엔드포인트를 호출할 수 있도록 하는 `Anonymous`를 선택합니다. 권한 부여 수준에 대해 알아보려면 [권한 부여 키](functions-bindings-http-webhook-trigger.md#authorization-keys)를 참조하세요.

    + **프로젝트를 여는 방법 선택**: `Add to workspace`을 선택합니다.

<br/>
<details>
<summary><strong>함수 프로젝트를 만들 수 없나요?</strong></summary>

로컬 Functions 프로젝트를 만들 때 해결해야 하는 가장 일반적인 문제는 다음과 같습니다.
* Azure Functions 확장이 설치되어 있지 않습니다. 
</details>

<hr/>
<br/>

## <a name="run-the-function-locally"></a>로컬에서 함수 실행

1. <kbd>F5</kbd> 키를 눌러 함수 앱 프로젝트를 시작합니다.

1. **터미널** 패널에서, 로컬에서 실행 중인 함수의 URL 엔드포인트를 봅니다.

    ![로컬 함수 VS Code 출력](../../includes/media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)


1. Core Tools가 실행 중인 상태에서 **Azure: Functions** 영역으로 이동합니다. **Functions** 에서 **로컬 프로젝트** > **Functions** 를 확장합니다. `HttpExample` 함수를 마우스 오른쪽 단추로 클릭(Windows)하거나 <kbd>Ctrl -</kbd>을 클릭(macOS)하고 **지금 함수 실행...** 을 선택합니다.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Visual Studio Code에서 지금 함수 실행":::

1. **요청 본문 입력** 에서 `{ "name": "Azure" }`의 요청 메시지 본문 값이 표시됩니다. Enter를 눌러 이 요청 메시지를 함수로 보냅니다.  

1. 함수가 로컬로 실행되고 응답을 반환하는 경우 Visual Studio Code에서 알림이 발생합니다. 기능 실행에 대한 정보는 **터미널** 패널에 표시됩니다.

1. <kbd>Ctrl + C</kbd>를 눌러 Core Tools를 중지하고 디버거 연결을 끊습니다.

<br/>
<details>
<summary><strong>함수를 로컬에서 실행할 수 없나요?</strong></summary>

로컬 Functions 프로젝트를 실행할 때 해결해야 하는 가장 일반적인 문제는 다음과 같습니다.
* 핵심 도구가 설치되어 있지 않습니다. 
*  Windows에서 실행하는 데 문제가 있는 경우 Visual Studio Code의 기본 터미널 셸이 **WSL Bash** 로 설정되어 있지 않은지 확인합니다. 
</details>

<hr/>
<br/>

## <a name="4-sign-in-to-azure"></a>4. Azure에 로그인

앱을 게시하려면 Azure에 로그인합니다. 이미 로그인한 경우 다음 섹션으로 이동합니다.

1. 작업 막대에서 Azure 아이콘을 선택한 다음, **Azure: Functions** 영역에서 **Azure에 로그인...** 을 선택합니다.

    ![VS Code 내에서 Azure에 로그인](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. 브라우저에 메시지가 표시되면 **Azure 계정을 선택** 하고 Azure 계정 자격 증명을 사용하여 **로그인** 합니다.

1. 성공적으로 로그인한 후에는 새 브라우저 창을 닫고 Visual Studio Code로 돌아갑니다. 

<hr/>
<br/>

## <a name="5-publish-the-project-to-azure"></a>5. Azure에 프로젝트 게시

첫 번째 코드 배포에는 Azure 구독에서 함수 리소스 만들기가 포함됩니다.

1. 작업 막대에서 Azure 아이콘을 선택한 다음, **Azure: Functions** 영역에서 **함수 앱에 배포...** 단추를 선택합니다.

    ![Azure에 프로젝트 게시](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. 프롬프트에서 다음 정보를 제공합니다.

    + **폴더 선택**: 함수 앱이 들어 있는 폴더를 선택합니다.

    + **구독 선택**: 사용할 구독을 선택합니다. 구독이 한 개만 있으면 이 메시지가 표시되지 않습니다.

    + **Azure에서 함수 앱 선택**: `+ Create new Function App`을 선택합니다.

    + **함수 앱에 대해 전역적으로 고유 이름을 입력합니다**. URL 경로에 유효한 이름을 입력합니다. 다음을 확인하기 위해 입력한 이름의 유효성을 검사합니다. <abbr title="이름은 모든 Azure 고객에 대해 전역적으로 고유해야 합니다. 예를 들어 contoso-bizapp-func-20과 같이 개인 또는 조직 이름, 애플리케이션 이름 및 숫자 식별자를 조합하여 사용할 수 있습니다.">Azure 전체에서 고유한</abbr>. 

    + **런타임 선택**: 로컬에서 실행 중인 Python 버전을 선택합니다. `python --version` 명령을 사용하여 버전을 확인할 수 있습니다.

    + **새 리소스의 위치 선택**: 성능을 향상시키려면 근처에 있는 [지역](https://azure.microsoft.com/regions/)을 선택합니다.

    확장은 알림 영역에서 Azure에 생성되는 개별 리소스의 상태를 보여줍니다.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Azure 리소스 생성 알림":::

1. 함수 앱을 만들고 배포 패키지가 적용되면 알림이 표시됩니다. **출력 보기** 를 선택하여 만들기 및 배포 결과를 봅니다. 

    ![전체 알림 만들기](./media/functions-create-first-function-vs-code/function-create-notifications.png)

<br/>
<details>
<summary><strong>함수를 게시할 수 없나요?</strong></summary>

이 섹션에서는 Azure 리소스를 만들고 함수 앱에 로컬 코드를 배포했습니다. 이 작업에 실패한 경우:

* 출력에서 오류 정보를 검토합니다. 오른쪽 아래 모서리에 있는 종 모양 아이콘은 출력을 볼 수 있는 또 다른 방법입니다. 
* 기존 함수 앱에 게시했나요? 이렇게 하면 Azure에서 해당 앱의 콘텐츠를 덮어씁니다.
</details>


<br/>
<details>
<summary><strong>어떤 리소스를 만들었나요?</strong></summary>

완료되면 함수 앱 이름을 기반으로 하는 이름을 사용하여 구독에 다음 Azure 리소스가 생성됩니다. 
* **리소스 그룹**: 리소스 그룹은 동일한 지역에 있는 관련 리소스에 대한 논리적 컨테이너입니다.
* **Azure Storage 계정**: Storage 리소스는 프로젝트에 대한 상태 및 기타 정보를 유지합니다.
* **사용 계획**: 사용 계획은 서버리스 함수 앱의 기본 호스트를 정의합니다.
* **함수 앱**: 함수 앱은 함수 코드 및 그룹 함수를 논리적 단위로 실행하기 위한 환경을 제공합니다.
* **Application Insights**: Application Insights는 서버리스 함수 사용 현황을 추적합니다.

</details>

<hr/>
<br/>

## <a name="6-run-the-function-in-azure"></a>6. Azure에서 함수 실행

1. **Azure: Functions** 사이드바로 돌아가서 새 함수 앱을 확장합니다.
1. **함수** 를 확장한 다음, `HttpExample` 함수를 마우스 오른쪽 단추로 클릭(Windows)하거나 <kbd>Ctrl -</kbd>을 클릭(macOS)하고 **지금 함수 실행...** 을 선택합니다.

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Visual Studio Code에서 Azure로 지금 함수 실행":::

1. **요청 본문 입력** 에서 `{ "name": "Azure" }`의 요청 메시지 본문 값이 표시됩니다.

    Enter를 눌러 이 요청 메시지를 함수로 보냅니다.  

1. 함수가 Azure에서 실행되고 응답을 반환하는 경우 Visual Studio Code에서 알림이 발생합니다.

## <a name="7-clean-up-resources"></a>7. 리소스 정리

[다음 단계](#next-steps)로 진행하고 <abbr title="큐에 메시지를 만들 수 있도록 스토리지 큐에 함수를 연결하는 방법."> Azure Storage 큐 출력 바인딩</abbr> 이미 완료한 항목을 기반으로 사용하려면 모든 리소스를 제자리에 유지해야 합니다.

그렇지 않으면 다음 단계를 수행하여 추가 비용이 발생하지 않도록 함수 앱 및 관련 리소스를 삭제할 수 있습니다.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

함수 비용에 대한 자세한 내용은 [소비 계획 비용 예측](functions-consumption-costs.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

출력을 추가하여 해당 함수를 확장합니다. <abbr title="함수와 기타 리소스 간의 선언적 연결. 입력 바인딩은 함수에 데이터를 제공합니다. 출력 바인딩은 함수에서 다른 리소스로 데이터를 제공합니다.">바인딩</abbr>. 이 바인딩은 HTTP 요청의 문자열을 Azure Queue Storage 큐의 메시지에 씁니다. 

> [!div class="nextstepaction"]
> [Azure Storage 큐에 연결](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-python)

[문제가 있나요? 알려주세요.](https://aka.ms/python-functions-qs-survey)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
