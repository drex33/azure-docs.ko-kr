---
title: Azure Portal에서 첫 번째 Azure Function을 만듭니다.
description: Azure Portal를 사용하여 서버를 사용하지 않는 실행을 위해 첫 번째 Azure Function을 만드는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-csharp, mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 60bea9c14c9f97193fc467b7e29a6d2f32fcf08f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130248586"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Azure Portal에서 첫 번째 Azure Function을 만듭니다.

Azure Functions를 사용하면 먼저 VM(가상 머신)을 만들거나 웹 애플리케이션을 게시하지 않고도 서버리스 환경에서 코드를 실행할 수 있습니다. 이 문서에서는 Azure Functions를 사용하여 Azure Portal에서 "hello world" HTTP 트리거 함수를 만드는 방법을 알아봅니다.

[!INCLUDE [functions-in-portal-editing-note](../../includes/functions-in-portal-editing-note.md)] 

대신 [함수를 로컬로 개발](functions-develop-local.md)하고 Azure의 함수 앱에 게시하는 것이 좋습니다.  
다음 링크 중 하나를 사용하여 선택한 로컬 개발 환경 및 언어를 시작합니다.

| Visual Studio Code | 터미널/명령 프롬프트 | Visual Studio |
| --- | --- | --- |
|  &bull;&nbsp;[C# 시작](./create-first-function-vs-code-csharp.md)<br/>&bull;&nbsp;[Java 시작](./create-first-function-vs-code-java.md)<br/>&bull;&nbsp;[JavaScript 시작](./create-first-function-vs-code-node.md)<br/>&bull;&nbsp;[PowerShell 시작](./create-first-function-vs-code-powershell.md)<br/>&bull;&nbsp;[Python 시작](./create-first-function-vs-code-python.md) |&bull;&nbsp;[C# 시작](./create-first-function-cli-csharp.md)<br/>&bull;&nbsp;[Java 시작](./create-first-function-cli-java.md)<br/>&bull;&nbsp;[JavaScript 시작](./create-first-function-cli-node.md)<br/>&bull;&nbsp;[PowerShell 시작](./create-first-function-cli-powershell.md)<br/>&bull;&nbsp;[Python 시작](./create-first-function-cli-python.md) | [C# 시작](functions-create-your-first-function-visual-studio.md) |

[!INCLUDE [functions-portal-language-support](../../includes/functions-portal-language-support.md)]

## <a name="prerequisites"></a>사전 요구 사항 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com) 에 로그인합니다.

## <a name="create-a-function-app"></a>함수 앱 만들기

함수 실행을 호스트하는 함수 앱이 있어야 합니다. 함수 앱을 사용하면 함수를 논리 단위로 그룹화하여 더 쉽게 리소스를 관리, 배포, 크기 조정 및 공유할 수 있습니다.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

다음으로 새 함수 앱에서 함수를 만듭니다.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>HTTP 트리거 함수 만들기

1. **Functions** 창의 왼쪽 메뉴에서 **Functions** 를 선택한 다음, 맨 위 메뉴에서 **추가** 를 선택합니다. 
 
1. **함수 추가** 창에서 **Http 트리거** 템플릿을 선택합니다.

    ![HTTP 트리거 함수 선택](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. **템플릿 세부 정보** 에서 **새 함수** 에 `HttpExample`을 사용하여 **[권한 부여 수준](functions-bindings-http-webhook-trigger.md#authorization-keys)** 드롭다운 목록에서 **익명** 을 선택한 다음, **추가** 를 선택합니다.

    Azure에서 HTTP 트리거 함수를 만듭니다. 이제 HTTP 요청을 전송하여 새 함수를 실행할 수 있습니다.

## <a name="test-the-function"></a>함수 테스트

1. 새 HTTP 트리거 함수의 왼쪽 메뉴에서 **코드 + 테스트** 를 선택한 다음, 상단 메뉴에서 **함수 URL 가져오기** 를 선택합니다.

    ![[함수 URL 가져오기] 선택](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. **함수 URL 가져오기** 대화 상자의 드롭다운 목록에서 **기본값** 을 선택한 다음, **클립보드에 복사** 아이콘을 선택합니다. 

    ![Azure Portal에서 함수 URL 복사](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. 함수 URL을 브라우저의 주소 표시줄에 붙여 넣습니다. `?name=<your_name>` 쿼리 문자열 값을 이 URL의 마지막에 추가하고 Enter 키를 눌러 요청을 실행합니다. 브라우저는 쿼리 문자열 값을 다시 에코하는 응답 메시지를 표시해야 합니다. 

    요청 URL에 [액세스 키](functions-bindings-http-webhook-trigger.md#authorization-keys)(`?code=...`)가 포함된 경우 함수를 만들 때 **익명** 액세스 수준 대신 **함수** 를 선택했음을 의미합니다. 이 경우 대신 `&name=<your_name>`을 추가해야 합니다.

1. 함수가 실행되면 추적 정보가 로그에 기록됩니다. 추적 출력을 보려면 포털의 **코드 + 테스트** 페이지로 돌아가서 페이지 하단에 있는 **로그** 화살표를 확장합니다. 함수를 다시 호출하여 로그에 기록된 추적 출력을 확인합니다. 

    :::image type="content" source="media/functions-create-first-azure-function/function-view-logs.png" alt-text="Azure Portal의 함수 로그 뷰어":::

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>다음 단계

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
