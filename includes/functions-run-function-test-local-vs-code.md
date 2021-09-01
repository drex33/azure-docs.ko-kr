---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/16/2021
ms.author: glenga
ms.openlocfilehash: 51e02f41e451f0a171e2b011647da294734608af
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122832406"
---
## <a name="run-the-function-locally"></a>로컬에서 함수 실행

Visual Studio Code는 [Azure Functions Core 도구](../articles/azure-functions/functions-run-local.md)와 통합되어 Azure에 게시하기 전에 로컬 개발 컴퓨터에서 이 프로젝트를 실행할 수 있습니다.

1. 함수를 호출하려면 <kbd>F5</kbd>를 눌러 함수 앱 프로젝트를 시작합니다. 핵심 도구의 출력이 **터미널** 패널에 표시됩니다. **터미널** 패널에서 앱이 시작됩니다. 로컬에서 실행 중인 HTTP 트리거 함수의 URL 엔드포인트를 볼 수 있습니다.

    ![로컬 함수 VS Code 출력](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

    Windows에서 실행하는 데 문제가 있는 경우 Visual Studio Code의 기본 터미널이 **WSL Bash** 로 설정되어 있지 않은지 확인합니다.

1. Core Tools가 실행 중인 상태에서 **Azure: Functions** 영역으로 이동합니다. **Functions** 에서 **로컬 프로젝트** > **Functions** 를 확장합니다. `HttpExample` 함수를 마우스 오른쪽 단추로 클릭(Windows)하거나 <kbd>Ctrl -</kbd>을 클릭(macOS)하고 **지금 함수 실행...** 을 선택합니다.

    :::image type="content" source="media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Visual Studio Code에서 지금 함수 실행":::
    
1. **요청 본문 입력** 에서 `{ "name": "Azure" }`의 요청 메시지 본문 값이 표시됩니다. Enter를 눌러 이 요청 메시지를 함수로 보냅니다. 

1. 함수가 로컬로 실행되고 응답을 반환하는 경우 Visual Studio Code에서 알림이 발생합니다. 기능 실행에 대한 정보는 **터미널** 패널에 표시됩니다.

1. <kbd>Ctrl + C</kbd>를 눌러 Core Tools를 중지하고 디버거 연결을 끊습니다.
