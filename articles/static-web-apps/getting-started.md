---
title: '빠른 시작: Azure Static Web Apps를 사용하여 첫 번째 정적 사이트 빌드'
description: 정적 사이트를 Azure Static Web Apps에 배포하는 방법을 알아봅니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 06/16/2021
ms.author: cshoe
ms.openlocfilehash: 167d6a2fbe6ec782a83c8dc3cb7f3ea57832a594
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129275908"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>빠른 시작: Azure Static Web Apps를 사용하여 첫 번째 정적 사이트 빌드

Azure Static Web Apps는 코드 리포지토리에서 앱을 빌드하여 웹 사이트를 게시합니다. 이 빠른 시작에서는 Visual Studio Code 확장을 사용하여 애플리케이션을 Azure Static Web Apps에 배포합니다.

Azure 구독이 아직 없는 경우 [평가판 계정](https://azure.microsoft.com/free)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

- [GitHub](https://github.com) 계정
- [Azure](https://portal.azure.com) 계정
- [Visual Studio Code](https://code.visualstudio.com)
- [Visual Studio Code용 Azure Static Web Apps 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Git 설치](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

다음으로, Visual Studio Code를 열고 **파일 > 폴더 열기** 로 이동하여 복제한 리포지토리를 편집기에서 엽니다.

## <a name="create-a-static-web-app"></a>정적 웹앱 만들기

1. Visual Studio Code 내의 활동 표시줄에서 Azure 로고를 선택하여 Azure 확장 창을 엽니다.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure 로고":::

    > [!NOTE]
    > 계속하려면 Visual Studio Code에서 Azure 및 GitHub에 로그인합니다. 아직 인증되지 않은 경우 확장이 만들기 프로세스 중에 두 서비스 모두에 로그인하라는 메시지를 표시합니다.

1. _Static Web Apps_ 레이블 아래에서 **더하기 기호** 를 선택합니다.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="애플리케이션 이름":::
    
    > [!NOTE]
    > Azure Static Web Apps Visual Studio Code 확장은 일련의 기본값을 사용하여 만들기 프로세스를 간소화합니다. 만들기 프로세스를 세부적으로 제어하려면 명령문을 열고 **Azure Static Web Apps: 정적 웹앱 만들기...(고급)** 을 선택합니다.

1. 편집기 맨 위에 명령 팔레트가 열리고 구독 이름을 선택하라는 메시지가 표시됩니다.

    구독을 선택하고 <kbd>Enter</kbd> 키를 누릅니다.

    :::image type="content" source="media/getting-started/extension-subscription.png" alt-text="Azure 구독 선택":::

1. 다음으로 애플리케이션의 이름을 지정합니다.

    **my-first-static-web-app** 을 입력하고 <kbd>Enter</kbd>를 누릅니다.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Static Web App 만들기":::

1. 가까운 지역을 선택합니다.

    > [!NOTE]
    > Azure Static Web Apps는 정적 자산을 전역으로 배포합니다. 선택한 지역에 따라 선택적 스테이징 환경 및 API 함수 앱의 위치가 결정됩니다.

1. 애플리케이션 유형과 일치하는 사전 설정을 선택합니다.

    # <a name="no-framework"></a>[프레임워크 없음](#tab/vanilla-javascript)

    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="애플리케이션 사전 설정: 프레임워크 없음":::

    애플리케이션 파일의 위치로 **/src** 를 입력하고 <kbd>Enter</kbd>를 누릅니다.

    이 앱은 빌드 출력을 생성하지 않습니다. 빌드 출력 위치가 비어 있는지 확인하고 <kbd>Enter</kbd>를 누릅니다.

    # <a name="angular"></a>[Angular](#tab/angular)

    Angular 미리 설정이 있지만 이 애플리케이션에 적절한 출력 위치를 제공할 수 있도록 **사용자 지정** 옵션을 선택합니다.

    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="애플리케이션 사전 설정: Angular":::

    애플리케이션 파일의 위치로 **/** 를 입력합니다.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="애플리케이션 파일 위치: Angular":::

    빌드 출력 위치로 **dist/angular-basic** 을 입력합니다.

    :::image type="content" source="media/getting-started/extension-angular.png" alt-text="애플리케이션 빌드 출력 위치: Angular":::

    # <a name="react"></a>[React](#tab/react)

    :::image type="content" source="media/getting-started/extension-presets-react.png" alt-text="애플리케이션 사전 설정: React":::

    애플리케이션 파일의 위치로 **/** 를 입력합니다.

    빌드 출력 위치로 **build** 를 입력합니다.

    # <a name="vue"></a>[Vue](#tab/vue)

    :::image type="content" source="media/getting-started/extension-presets-vue.png" alt-text="애플리케이션 사전 설정: Vue":::

    애플리케이션 파일의 위치로 **/** 를 입력합니다.

    빌드 출력 위치로 **dist** 를 입력합니다.

    ---

1. 앱이 만들어지면 Visual Studio Code에 확인 알림이 표시됩니다.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="생성 확인":::

    배포가 진행 중이면 Visual Studio Code 확장이 빌드 상태를 보고합니다.

    :::image type="content" source="media/getting-started/extension-waiting-for-deployment.png" alt-text="배포 대기 중":::

    배포가 완료되면 웹 사이트로 바로 이동할 수 있습니다.

1. 브라우저에서 웹 사이트를 보려면 Static Web Apps 확장에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **사이트 찾아보기** 를 선택합니다.

    :::image type="content" source="media/getting-started/extension-browse-site.png" alt-text="사이트 찾아보기":::

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않을 경우 확장을 통해 Azure Static Web App 인스턴스를 삭제할 수 있습니다.

Visual Studio Code 탐색기 창에서 _Static Web Apps_ 섹션으로 돌아가서 **my-first-static-web-app** 을 마우스 오른쪽 단추로 클릭하고 **삭제** 를 선택합니다.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="앱 삭제":::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [API 추가](add-api.md)
