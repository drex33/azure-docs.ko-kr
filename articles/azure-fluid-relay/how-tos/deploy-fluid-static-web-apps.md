---
title: '방법: Azure 정적 Web Apps를 사용 하 여 유체 응용 프로그램 배포'
description: Azure 정적 Web Apps에서 유체 응용 프로그램을 호스트 하는 방법에 대 한 자세한 설명
author: sdeshpande3
ms.author: sdeshpande
ms.date: 08/19/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: a5b30b3097ef3e04557473dcaf42e875470b7563
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053182"
---
# <a name="how-to-deploy-fluid-applications-using-azure-static-web-apps"></a>방법: Azure 정적 Web Apps를 사용 하 여 유체 응용 프로그램 배포

이 문서에서는 Azure Static Web Apps를 사용 하 여 유체 앱을 배포 하는 방법을 보여 줍니다. [FluidHelloWorld](https://github.com/microsoft/FluidHelloWorld/tree/main-azure) 리포지토리에는 모든 연결 된 클라이언트가 주사위를 롤링 하 고 결과를 볼 수 있도록 하는 **DiceRoller** 라는 유체 응용 프로그램이 포함 되어 있습니다.  이 방법에서는 Visual Studio Code 확장을 사용 하 여 DiceRoller 응용 프로그램을 Azure Static Web Apps에 배포 합니다.

Azure 구독이 아직 없는 경우 [평가판 계정](https://azure.microsoft.com/free)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- [GitHub](https://github.com) 계정
- [Azure](https://portal.azure.com) 계정
- [Visual Studio Code](https://code.visualstudio.com)
- [Visual Studio Code용 Azure Static Web Apps 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Git 설치](https://www.git-scm.com/downloads)

[!INCLUDE [fork-fluidhelloworld](../includes/fork-fluidhelloworld.md)]

## <a name="connect-to-azure-fluid-relay-service"></a>Azure 유체 Relay 서비스에 커넥트

Azure 리소스를 만들 때 고유 하 게 생성 되는 테 넌 트 ID 및 키를 제공 하 여 Azure 유체 Relay 서비스에 연결할 수 있습니다. 사용자 고유의 토큰 공급자 구현을 빌드하거나 Fluid Framework에서 제공 하는 두 개의 토큰 공급자 구현 ( **insecuretokenprovider** 및 **AzureFunctionTokenProvider**)을 사용할 수 있습니다.

로컬 개발에 InsecureTokenProvider를 사용 하는 방법에 대 한 자세한 내용은 앱에서 [서비스에 연결](connect-fluid-azure-service.md#connecting-to-the-service) 및 [인증 및 권한 부여](../concepts/authentication-authorization.md#the-token-provider)를 참조 하세요.

### <a name="using-azurefunctiontokenprovider"></a>AzureFunctionTokenProvider 사용

**AzureFunctionTokenProvider** 는 클라이언트 쪽 코드에서 비밀 키를 노출 하지 않고 프로덕션 시나리오에서 사용할 수 있는 토큰 공급자입니다. 이 토큰 공급자 구현을 사용 하면 테 넌 트 키를 사용 하 여 액세스 토큰에 서명 하는 HTTPS 끝점에서 토큰을 페치할 수 있습니다. 이를 통해 토큰을 생성 하 고 클라이언트 앱에 다시 전달 하는 안전한 방법을 제공 합니다.

```js
import { AzureClient, AzureFunctionTokenProvider } from "@fluidframework/azure-client";

const config = {
    tenantId: "myTenantId",
    tokenProvider: new AzureFunctionTokenProvider("https://myAzureAppUrl"+"/api/GetAzureToken", { userId: "test-user",userName: "Test User" }),
    orderer: "https://myOrdererUrl",
    storage: "https://myStorageUrl",
};

const clientProps = {
    connection: config,
};

const client = new AzureClient(clientProps);
```

이 토큰 공급자를 사용 하려면 토큰에 서명 하는 HTTPS 끝점을 배포 하 고 AzureFunctionTokenProvider에 끝점에 대 한 URL을 전달 해야 합니다.

### <a name="deploying-an-azure-function-using-azure-static-web-apps"></a>Azure 정적 웹 앱을 사용 하 여 Azure 함수 배포

Azure Static Web Apps를 사용 하면 전체 웹 호스팅 환경의 서버 쪽 구성을 처리할 필요 없이 전체 스택 웹 사이트를 개발할 수 있습니다. 정적 웹 사이트와 함께 Azure Functions을 배포할 수 있습니다. 이 기능을 사용 하 여 토큰에 서명 하는 HTTP 트리거 Azure 함수를 배포할 수 있습니다.

정적 웹 앱에 Azure 함수 기반 Api를 배포 하는 방법에 대 한 자세한 내용은 [Azure Functions를 사용 하 여 Azure static Web Apps에 Api 추가를](../../static-web-apps/add-api.md)참조 하세요.

> [!NOTE]
> Azure 함수를 구현 하는 예제 Azure 함수 코드를 사용 하 여 [토큰에 서명 하](azure-function-token-provider.md#implement-an-azure-function-to-sign-tokens) 고 함수를 구현할 수 있습니다.

Azure 함수를 배포한 후에는 AzureFunctionTokenProvider에 전달 된 URL을 업데이트 해야 합니다.

```js
import { AzureClient } from "@fluidframework/azure-client";

const config = {
    tenantId: "myTenantId",
    tokenProvider: new AzureFunctionTokenProvider("https://myStaticWebAppUrl/api/GetAzureToken", { userId: "test-user",userName: "Test User" }),
    orderer: "https://myOrdererUrl",
    storage: "https://myStorageUrl",
};

const clientProps = {
    connection: config,
};

const client = new AzureClient(config);
```

`npm run build`루트 디렉터리에서 명령을 실행 하 여 앱을 다시 빌드합니다. 그러면 `dist` 정적 웹 앱에 배포 해야 하는 응용 프로그램 코드가 포함 된 폴더가 생성 됩니다.

[!INCLUDE [sign-in-extensions](../includes/sign-in-extensions.md)]

## <a name="create-a-static-web-app"></a>정적 웹앱 만들기

1. Visual Studio Code 내의 활동 표시줄에서 Azure 로고를 선택하여 Azure 확장 창을 엽니다.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-azure-logo.png" alt-text="흰색 배경의 Azure 로고 이미지입니다.":::

    > [!NOTE]
    > 계속 하려면 Azure에 로그인 하 여 Visual Studio Code에서 GitHub 해야 합니다. 아직 인증되지 않은 경우 확장이 만들기 프로세스 중에 두 서비스 모두에 로그인하라는 메시지를 표시합니다.

1. *Static Web Apps* 레이블 아래에서 **더하기 기호** 를 선택합니다.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-create-button.png" alt-text="만들기 단추를 강조 표시 하는 정적 Web Apps 확장 UI의 이미지입니다.":::
    
    > [!NOTE]
    > Azure Static Web Apps Visual Studio Code 확장은 일련의 기본값을 사용하여 만들기 프로세스를 간소화합니다. 만들기 프로세스를 세부적으로 제어하려면 명령문을 열고 **Azure Static Web Apps: 정적 웹앱 만들기...(고급)** 을 선택합니다.

1. 편집기 맨 위에 명령 팔레트가 열리고 구독 이름을 선택하라는 메시지가 표시됩니다.

    구독을 선택하고 <kbd>Enter</kbd> 키를 누릅니다.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-subscription.png" alt-text="선택할 단일 구독을 보여 주는 Azure 구독 선택 UI의 이미지입니다.":::

1. 다음으로 애플리케이션의 이름을 지정합니다.

    **my-first-static-web-app** 을 입력하고 <kbd>Enter</kbd>를 누릅니다.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-create-app.png" alt-text="응용 프로그램 이름을 입력할 수 있는 텍스트 상자를 표시 하는 정적 Web Apps 확장 UI의 이미지입니다.":::

1. 가까운 지역을 선택합니다.

    > [!NOTE]
    > Azure Static Web Apps는 정적 자산을 전역으로 배포합니다. 선택한 지역에 따라 선택적 스테이징 환경 및 API 함수 앱의 위치가 결정됩니다.

1. 다른 배포 옵션을 설정 합니다.
    
    - 기본 프로젝트 구조를 구성 하기 위해 빌드 기본 설정을 선택 하 라는 메시지가 표시 되 면 **사용자 지정** 을 선택 합니다.
    - 응용 프로그램 코드의 위치: `/`
    - Azure 함수 코드의 위치: `api`

1. 앱이 만들어지면 Visual Studio Code에 확인 알림이 표시됩니다.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-confirmation.png" alt-text="앱을 만들 때 Visual Studio Code에 표시 되는 알림의 이미지입니다. 알림 읽기: 새 정적 웹 앱을 성공적으로 만들었습니다.-첫 정적 웹 앱. GitHub 작업은 앱을 빌드 및 배포 하 고 배포를 완료 한 후에도 사용할 수 있습니다.":::

    배포가 진행 중이면 Visual Studio Code 확장이 빌드 상태를 보고합니다.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-waiting-for-deployment.png" alt-text="각 구독의 정적 웹 앱 목록을 표시 하는 정적 Web Apps 확장 UI의 이미지입니다. 강조 표시 된 정적 웹 앱은 옆에 표시 되는 배포 대기 상태를 포함 합니다.":::

    배포가 완료되면 웹 사이트로 바로 이동할 수 있습니다.

1. 브라우저에서 웹 사이트를 보려면 Static Web Apps 확장에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **사이트 찾아보기** 를 선택합니다.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-browse-site.png" alt-text="정적 웹 앱을 마우스 오른쪽 단추로 클릭할 때 표시 되는 메뉴의 이미지입니다. 사이트 찾아보기 옵션이 강조 표시 됩니다.":::

1. 응용 프로그램 코드, Azure 함수 및 빌드 출력의 위치는 `azure-static-web-apps-xxx-xxx-xxx.yml` 디렉터리에 있는 워크플로 파일의 일부입니다 `/.github/workflows` . 이 파일은 정적 웹 앱을 만들 때 자동으로 생성 됩니다. 정적 웹 앱을 빌드 및 배포 하는 GitHub 작업을 정의 합니다.


## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않을 경우 확장을 통해 Azure Static Web App 인스턴스를 삭제할 수 있습니다.

Visual Studio Code 탐색기 창에서 _Static Web Apps_ 섹션으로 돌아가서 **my-first-static-web-app** 을 마우스 오른쪽 단추로 클릭하고 **삭제** 를 선택합니다.

:::image type="content" source="../../static-web-apps/media/getting-started/extension-delete.png" alt-text="정적 웹 앱을 마우스 오른쪽 단추로 클릭할 때 표시 되는 메뉴의 이미지입니다. 삭제 옵션이 강조 표시 됩니다.":::
