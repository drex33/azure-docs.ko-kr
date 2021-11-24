---
title: Visual Studio Code를 사용하여 C# 함수 만들기 - Azure Functions
description: 'C# 함수를 만든 다음, Visual Studio Code의 Azure Functions 확장을 사용하여 Azure Functions의 서버리스 호스팅에 로컬 프로젝트를 게시하는 방법을 알아봅니다. '
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom: devx-track-csharp, mode-other
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d70890efde4d0970a79c2209943f36474a1620f9
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133049080"
---
# <a name="quickstart-create-a-c-function-in-azure-using-visual-studio-code"></a>빠른 시작: Visual Studio Code를 사용하여 Azure에서 C# 함수 만들기

> [!div class="op_single_selector" title1="함수 언어를 선택합니다. "]
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [Python](create-first-function-vs-code-python.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [기타(Go/Rust)](create-first-function-vs-code-other.md)

이 문서에서는 Visual Studio Code를 사용하여 HTTP 요청에 응답하는 C# 클래스 라이브러리 기반 함수를 만듭니다. 코드를 로컬에서 테스트한 후 다음에 배포합니다. <abbr title="서버의 모든 세부 정보가 애플리케이션 개발자에게 투명하게 공개되어 코드 배포 및 관리 프로세스가 간소화되는 런타임 컴퓨팅 환경.">서버를 사용하지 않음</abbr> 환경 <abbr title="애플리케이션을 위한 저렴한 서버리스 컴퓨팅 환경을 제공하는 Azure의 서비스.">Azure 기능</abbr>.

이 빠른 시작을 완료하면 Azure 계정에서 USD 센트 이하의 작은 비용이 발생합니다.

이 문서의 [CLI 기반 버전](create-first-function-cli-csharp.md)도 있습니다.
    
## <a name="1-configure-your-environment"></a>1. 환경 구성

시작하기 전에 다음 요구 사항을 갖추었는지 확인합니다.

+ Azure <abbr title="Azure 사용량에 대한 청구 정보를 유지 관리하는 프로필.">account</abbr> 활성 <abbr title="Azure에서 리소스를 관리하는 기본 조직 구조이며 일반적으로 조직 내의 개인 또는 부서와 연결됩니다.">subscription</abbr>. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 버전 3.x.

+ [지원되는 플랫폼](https://code.visualstudio.com/docs/supporting/requirements#_platforms) 중 하나인 [Visual Studio Code](https://code.visualstudio.com/).

+ Visual Studio Code용 [C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).  

+ Visual Studio Code용 [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

## <a name="2-create-your-local-project"></a><a name="create-an-azure-functions-project"></a>2. 로컬 프로젝트 만들기

이 섹션에서는 Visual Studio Code를 사용하여 로컬 항목을 만듭니다. <abbr title="함께 배포하고 관리할 수 있는 하나 이상의 개별 함수에 대한 논리적 컨테이너.">Azure Functions 프로젝트</abbr> C# 이 문서의 뒷부분에서 함수 코드를 Azure에 게시합니다.

1. Azure 아이콘을 선택합니다. <abbr title="Visual Studio Code 창의 왼쪽에 있는 아이콘의 수직 그룹.">작업 표시줄</abbr>그런 다음, **Azure: Functions** 영역에서 **새 프로젝트 만들기...** 아이콘을 선택합니다.

    ![새 프로젝트 만들기 선택](./media/functions-create-first-function-vs-code/create-new-project.png)

1. 프로젝트 작업 영역에 대한 디렉터리 위치를 선택하고 **선택** 을 선택합니다.

    > [!NOTE]
    > 다음 단계는 작업 영역 외부에서 완료하도록 설계되었습니다. 이 경우 작업 영역에 포함된 프로젝트 폴더를 선택하지 마십시오.

1. 프롬프트에서 다음 정보를 제공합니다.

    + **함수 프로젝트에 대한 언어 선택**: `C#`을 선택합니다.

    + **프로젝트의 첫 번째 함수에 대한 템플릿 선택**: `HTTP trigger`을 선택합니다.

    + **함수 이름 입력**: `HttpExample`.

    + **네임스페이스 입력**: `My.Functions`.

    + **권한 부여 수준**: 누구나 함수 엔드포인트를 호출할 수 있도록 하는 `Anonymous`를 선택합니다. 권한 부여 수준에 대해 알아보려면 [권한 부여 키](functions-bindings-http-webhook-trigger.md#authorization-keys)를 참조하세요.

    + **프로젝트를 여는 방법 선택**: `Add to workspace`을 선택합니다.

1. Visual Studio Code는 이 정보를 사용하여 HTTP를 통해 Azure Functions 프로젝트를 생성합니다. <abbr title="함수의 코드를 호출하는 이벤트의 유형(예: HTTP 요청, 큐 메시지 또는 특정 시간).">트리거</abbr>. 탐색기에서 로컬 프로젝트 파일을 볼 수 있습니다. 생성된 파일에 대한 자세한 내용은 [생성된 프로젝트 파일](functions-develop-vs-code.md#generated-project-files)을 참조하세요.

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

함수가 로컬 컴퓨터에서 제대로 실행되는지 확인한 후에 Visual Studio Code를 사용하여 프로젝트를 Azure에 직접 게시합니다.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="5-publish-the-project-to-azure"></a>5. Azure에 프로젝트 게시

이 섹션에서는 Azure 구독에서 함수 앱 및 관련 리소스를 만든 다음, 코드를 배포합니다. 

> [!IMPORTANT]
> 기존 함수 앱에 게시하면 Azure에서 해당 앱의 콘텐츠를 덮어씁니다. 

1. 작업 막대에서 Azure 아이콘을 선택한 다음, **Azure: Functions** 영역에서 **함수 앱에 배포...** 단추를 선택합니다.

    

1. 프롬프트에서 다음 정보를 제공합니다.

    + **폴더 선택**: 작업 영역에서 폴더를 선택하거나 함수 앱을 포함하는 폴더를 찾습니다. 유효한 함수 앱이 이미 열려 있는 경우에는 이 메시지가 표시되지 않습니다.

    + **구독 선택**: 사용할 구독을 선택합니다. 구독이 한 개만 있으면 이 메시지가 표시되지 않습니다.

    + **Azure에서 함수 앱 선택**: `+ Create new Function App`을 선택합니다. (이 문서에서 다루지 않는 `Advanced` 옵션은 선택하지 마세요.)

    + **함수 앱에 대해 전역적으로 고유 이름을 입력합니다**. URL 경로에 유효한 이름을 입력합니다. 다음을 확인하기 위해 입력한 이름의 유효성을 검사합니다. <abbr title="이 이름은 모든 Azure 고객이 전역적으로 사용하는 모든 Functions 프로젝트에서 고유해야 합니다. 일반적으로 contoso-bizapp-20과 같이 개인 또는 회사 이름, 애플리케이션 이름 및 숫자 식별자를 조합하여 사용합니다.">Azure Functions에서 고유함</abbr>. 

    + **새 리소스의 위치 선택**:  성능을 향상시키려면 근처에 있는 [지역](https://azure.microsoft.com/regions/)을 선택합니다.

    확장은 알림 영역에서 Azure에 생성되는 개별 리소스의 상태를 보여줍니다.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Azure 리소스 생성 알림":::

1. 완료되면 함수 앱 이름을 기반으로 하는 이름을 사용하여 구독에 다음 Azure 리소스가 생성됩니다.

    + **리소스 그룹** - 관련 리소스에 대한 논리 컨테이너입니다.
    + 표준 **Azure Storage 계정** - 프로젝트에 대한 상태 및 기타 정보를 유지 관리합니다.
    + **소비 계획** - 서버리스 함수 앱의 기본 호스트를 정의합니다. 
    + **함수 앱** - 함수 코드를 실행할 수 있는 환경을 제공합니다. 함수 앱을 사용하면 함수를 논리 단위로 그룹화하여 동일한 호스팅 계획 내에서 더 쉽게 리소스를 관리, 배포 및 공유할 수 있습니다.
    + 함수 앱에 연결된 **Application Insights 인스턴스** - 서버리스 함수의 사용을 추적합니다.

    함수 앱을 만들고 배포 패키지가 적용되면 알림이 표시됩니다. 

    > [!TIP]
    > 기본적으로 함수 앱에 필요한 Azure 리소스는 사용자가 제공하는 함수 앱 이름을 기반으로 만들어집니다. 기본적으로 함수 앱을 사용하여 동일한 새 리소스 그룹에도 만들어집니다. 이러한 리소스의 이름을 사용자 지정하거나 기존 리소스를 다시 사용하려면 대신 [고급 만들기 옵션을 사용하여 프로젝트를 게시](functions-develop-vs-code.md#enable-publishing-with-advanced-create-options)해야 합니다.


1. 이 알림에서 **출력 보기** 를 선택하여 사용자가 만든 Azure 리소스를 포함한 만들기 및 배포 결과를 표시합니다. 알림이 누락된 경우 오른쪽 아래 모서리에 있는 종 모양 아이콘을 선택하여 다시 확인합니다.

    ![전체 알림 만들기](./media/functions-create-first-function-vs-code/function-create-notifications.png)

## <a name="6-run-the-function-in-azure"></a>6. Azure에서 함수 실행

1. 사이드바의 **Azure: Functions** 영역으로 돌아가서 구독, 새 함수 앱 및 **Functions** 를 확장합니다. `HttpExample` 함수를 마우스 오른쪽 단추로 클릭(Windows)하거나 <kbd>Ctrl -</kbd>을 클릭(macOS)하고 **지금 함수 실행...** 을 선택합니다.

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Visual Studio Code에서 Azure로 지금 함수 실행":::

1. **요청 본문 입력** 에서 `{ "name": "Azure" }`의 요청 메시지 본문 값이 표시됩니다.

    Enter를 눌러 이 요청 메시지를 함수로 보냅니다.  

1. 함수가 Azure에서 실행되고 응답을 반환하는 경우 Visual Studio Code에서 알림이 발생합니다.

## <a name="5-clean-up-resources"></a>5. 리소스 정리

[다음 단계](#next-steps)로 진행하고 <abbr title="큐에 메시지를 만들 수 있도록 스토리지 큐에 함수를 연결하는 방법."> Azure Storage 큐 출력 바인딩</abbr> 이미 완료한 항목을 기반으로 사용하려면 모든 리소스를 제자리에 유지해야 합니다.

그렇지 않으면 다음 단계를 수행하여 추가 비용이 발생하지 않도록 함수 앱 및 관련 리소스를 삭제할 수 있습니다.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

함수 비용에 대한 자세한 내용은 [소비 계획 비용 예측](functions-consumption-costs.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Visual Studio Code를 사용하여 간단한 HTTP 트리거 함수가 있는 함수 앱을 만들었습니다. 다음 문서에서는 출력을 추가하여 해당 함수를 확장합니다. <abbr title="함수와 기타 리소스 간의 선언적 연결. 입력 바인딩은 함수에 데이터를 제공합니다. 출력 바인딩은 함수에서 다른 리소스로 데이터를 제공합니다.">바인딩</abbr>. 이 바인딩은 HTTP 요청의 문자열을 Azure Queue Storage 큐의 메시지에 씁니다. 

> [!div class="nextstepaction"]
> [Azure Storage 큐에 연결](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
