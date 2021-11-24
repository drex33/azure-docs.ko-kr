---
title: '빠른 시작: ASP.NET 웹앱 배포'
description: 첫 번째 ASP.NET 앱을 배포하여 Azure App Service에서 웹앱을 실행하는 방법을 알아봅니다.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/08/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1, mode-other
zone_pivot_groups: app-service-ide
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 8523d28a4acf000108dd37690c99ffd61d2ca0ce
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133042978"
---
<!-- NOTES:

I'm a .NET developer who wants to deploy my web app to App Service. I may develop apps with
Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET SDK/CLI. This article
should be able to guide .NET devs, whether they're app is .NET Core, .NET, or .NET Framework.

As a .NET developer, when choosing an IDE and .NET TFM - you map to various OS requirements.
For example, if you choose Visual Studio - you're developing the app on Windows, but you can still
target cross-platform with .NET 6.0.

| .NET / IDE         | Visual Studio | Visual Studio for Mac | Visual Studio Code | Command line   |
|--------------------|---------------|-----------------------|--------------------|----------------|
| .NET 6.0           | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET Framework 4.8 | Windows       | N/A                   | Windows            | Windows        |

-->

# <a name="quickstart-deploy-an-aspnet-web-app"></a>빠른 시작: ASP.NET 웹앱 배포

이 빠른 시작에서는 첫 번째 ASP.NET 웹앱을 만들고 [Azure App Service](overview.md)에 배포하는 방법을 알아봅니다. App Service는 다양한 버전의 .NET 앱을 지원하며 확장성이 뛰어난 자체 패치 웹 호스팅 서비스를 제공합니다. ASP.NET 웹앱은 크로스 플랫폼이며 Linux 또는 Windows에서 호스트할 수 있습니다. 이 빠른 시작을 마치고 나면 App Service 호스팅 계획 및 웹 애플리케이션이 배포된 App Service로 구성된 리소스 그룹이 하나 생깁니다.

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-ps"
<!-- markdownlint-enable MD044 -->

> [!NOTE]
> Azure PowerShell은 Windows 호스팅 플랫폼에서 앱을 만드는 데 권장됩니다. Linux에서 앱을 만들려면 [Azure CLI](quickstart-dotnetcore.md?pivots=development-environment-cli)와 같은 다른 도구를 사용합니다.

:::zone-end

## <a name="prerequisites"></a>사전 요구 사항

:::zone target="docs" pivot="development-environment-vs"

### <a name="net-60"></a>[.NET 6.0](#tab/net60)

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/dotnet).
- **ASP.NET 및 웹 개발** 워크로드가 설치된 <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2022</a>입니다.

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/dotnet).
- **ASP.NET 및 웹 개발** 워크로드가 있는 <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2022</a>(선택 사항인 **.NET Framework 프로젝트 및 항목 템플릿** 확인란이 선택되어 있는지 확인)

-----

Visual Studio 2022를 이미 설치한 경우 다음을 수행합니다.

1. **도움말** > **업데이트 확인** 을 차례로 선택하여 Visual Studio에서 최신 업데이트를 설치합니다.
1. **도구** > **도구 및 기능 가져오기** 를 차례로 선택하여 워크로드를 추가합니다.
    
:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/dotnet).
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio Code</a>
- <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack" target="_blank">Azure 도구</a> 확장

### <a name="net-60"></a>[.NET 6.0](#tab/net60)

<a href="https://dotnet.microsoft.com/download/dotnet/6.0" target="_blank"> 최신 .NET 6.0 SDK를 설치합니다. </a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> .NET Framework 4.8 개발자 팩을 설치합니다. </a>

> [!NOTE]
> Visual Studio Code는 크로스 플랫폼이지만 .NET Framework는 아닙니다. Visual Studio Code를 사용하여 .NET Framework 앱을 개발하는 경우 Windows 머신을 사용하여 빌드 종속성을 충족하는 것이 좋습니다.

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/dotnet).
- <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>
- .NET SDK(런타임 및 CLI 포함)

### <a name="net-60"></a>[.NET 6.0](#tab/net60)

<a href="https://dotnet.microsoft.com/download/dotnet/6.0" target="_blank"> 최신 .NET 6.0 SDK를 설치합니다. </a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/6.0" target="_blank"> 최신 .NET 6.0 SDK </a> 및 <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> .NET Framework 4.8 개발자 팩을 설치합니다. </a>

> [!NOTE]
> [.NET CLI](/dotnet/core/tools) 및 .NET 6.0은 모두 플랫폼 간이지만 .NET Framework는 그렇지 않습니다. .NET CLI를 사용하여 .NET Framework 앱을 개발하는 경우 Windows 머신을 사용하여 빌드 종속성을 충족하는 것이 좋습니다. .NET 6.0은 플랫폼 간입니다.

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-ps"
<!-- markdownlint-enable MD044 -->

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/dotnet).
- <a href="/powershell/azure/install-az-ps" target="_blank">Azure PowerShell</a>
- .NET SDK(런타임 및 CLI 포함)

### <a name="net-60"></a>[.NET 6.0](#tab/net60)

<a href="https://dotnet.microsoft.com/download/dotnet/6.0" target="_blank"> 최신 .NET 6.0 SDK를 설치합니다. </a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/6.0" target="_blank"> 최신 .NET 6.0 SDK </a> 및 <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> .NET Framework 4.8 개발자 팩을 설치합니다. </a>

> [!NOTE]
> [Azure PowerShell](/powershell/azure/) 및 .NET 6.0은 모두 플랫폼 간이지만 .NET Framework는 그렇지 않습니다. .NET CLI를 사용하여 .NET Framework 앱을 개발하는 경우 Windows 머신을 사용하여 빌드 종속성을 충족하는 것이 좋습니다.

---

:::zone-end

## <a name="create-an-aspnet-web-app"></a>ASP.NET 웹앱 만들기

:::zone target="docs" pivot="development-environment-vs"

### <a name="net-60"></a>[.NET 6.0](#tab/net60)

1. Visual Studio를 연 다음, **새 프로젝트 만들기** 를 선택합니다.
1. **새 프로젝트 만들기** 에서 **ASP.NET Core 웹앱** 을 찾아서 선택한 후 **다음** 을 선택합니다.
1. **새 프로젝트 구성** 에서 애플리케이션 이름을 _MyFirstAzureWebApp_ 으로 지정한 후 **다음** 을 선택합니다.

   :::image type="content" source="./media/quickstart-dotnet/configure-webapp-net.png" alt-text="Visual Studio - ASP.NET 6.0 웹앱 구성" lightbox="media/quickstart-dotnet/configure-webapp-net.png" border="true":::

1. **.NET Core 6.0(장기 지원)** 을 선택합니다.
1. **인증 유형** 이 **없음** 으로 설정되어 있는지 확인합니다. **만들기** 를 선택합니다.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-net60.png" alt-text="Visual Studio - .NET Core 6.0 선택 시 추가 정보" lightbox="media/quickstart-dotnet/vs-additional-info-net60.png" border="true":::

1. Visual Studio 메뉴에서 **디버그** > **디버깅하지 않고 시작** 을 차례로 선택하여 웹앱을 로컬로 실행합니다.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio - 로컬로 실행되는 ASP.NET Core 6.0" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

1. Visual Studio를 연 다음, **새 프로젝트 만들기** 를 선택합니다.
1. **새 프로젝트 만들기** 에서 **ASP.NET 웹 애플리케이션(.NET Framework)** 을 찾아서 선택한 후, **다음** 을 선택합니다.
1. **새 프로젝트 구성** 에서 애플리케이션 이름을 _MyFirstAzureWebApp_ 으로 지정한 다음, **만들기** 를 선택합니다.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-netframework48.png" alt-text="Visual Studio - ASP.NET Framework 4.8 웹앱 구성" lightbox="media/quickstart-dotnet/configure-webapp-netframework48.png" border="true":::

1. **MVC** 템플릿을 선택합니다.
1. **인증** 이 **인증 없음** 으로 설정되었는지 확인합니다. **만들기** 를 선택합니다.

   :::image type="content" source="media/quickstart-dotnet/vs-mvc-no-auth-netframework48.png" alt-text="Visual Studio - MVC 템플릿 선택" lightbox="media/quickstart-dotnet/vs-mvc-no-auth-netframework48.png" border="true":::

1. Visual Studio 메뉴에서 **디버그** > **디버깅하지 않고 시작** 을 차례로 선택하여 웹앱을 로컬로 실행합니다.

   :::image type="content" source="media/quickstart-dotnet/vs-local-webapp-netframework48.png" alt-text="Visual Studio - 로컬에서 실행되는 ASP.NET Framework 4.8" lightbox="media/quickstart-dotnet/vs-local-webapp-netframework48.png" border="true":::

---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

1. 터미널 창에서 _MyFirstAzureWebApp_ 이라는 새 폴더를 만들고, Visual Studio Code에서 엽니다. 

    ```terminal
    mkdir MyFirstAzureWebApp
    code MyFirstAzureWebApp
    ```

1. Visual Studio Code에서 `Ctrl` + `` ` ``를 입력하여 <a href="https://code.visualstudio.com/docs/editor/integrated-terminal" target="_blank">터미널</a> 창을 엽니다.

1. Visual Studio Code의 터미널에서 [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) 명령을 사용하여 새 .NET 웹앱을 만듭니다.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)
    
    ```dotnetcli
    dotnet new webapp -f net6.0
    ```
    
    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)
    
    ```dotnetcli
    dotnet new webapp --target-framework-override net48
    ```
    
    > [!IMPORTANT]
    > `--target-framework-override` 플래그는 프로젝트의 TFM(대상 프레임워크 모니커)을 대체하는 자유 형식 텍스트이며 지원 템플릿이 존재하거나 컴파일된다는 보장은 없습니다. .NET Framework 앱은 Windows에서만 빌드하고 실행할 수 있습니다.
    
    ---

1. Visual Studio Code의 **터미널** 에서 [`dotnet run`](/dotnet/core/tools/dotnet-run) 명령을 사용하여 애플리케이션을 로컬에서 실행합니다.

    ```dotnetcli
    dotnet run --urls=https://localhost:5001/
    ```

1. 웹 브라우저를 열고 `https://localhost:5001`의 앱으로 이동합니다.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)
    
    페이지에 ASP.NET Core 6.0 웹앱 템플릿이 표시됩니다.
    
    :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code - 브라우저에서 .NET 6.0을 로컬로 실행" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::
    
    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)
    
    ASP.NET Framework 4.8 웹앱 템플릿이 페이지에 표시됩니다.
    
    :::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code - .NET 4.8을 브라우저에서 로컬로 실행" lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::
    
    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli,development-environment-ps"
<!-- markdownlint-enable MD044 -->

1. 머신에서 터미널 창을 열고 작업 디렉터리로 이동합니다. [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) 명령을 사용하여 새 .NET 웹앱을 만든 후 새로 만든 앱으로 디렉터리를 변경합니다.

    <!-- Please keep the following commands in two lines instead of one && separated line. The latter doesn't work in PowerShell -->
    
    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)
    
    ```dotnetcli
    dotnet new webapp -n MyFirstAzureWebApp --framework net6.0
    cd MyFirstAzureWebApp
    ```
    
    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)
    
    ```dotnetcli
    dotnet new webapp -n MyFirstAzureWebApp --target-framework-override net48
    cd MyFirstAzureWebApp
    ```
    
    > [!IMPORTANT]
    > `--target-framework-override` 플래그는 프로젝트의 TFM(대상 프레임워크 모니커)을 대체하는 자유 형식 텍스트이며 지원 템플릿이 존재하거나 컴파일된다는 보장은 없습니다. .NET Framework 앱은 Windows에서만 빌드할 수 있습니다.
    
    ---

1. 동일한 터미널 세션에서 [`dotnet run`](/dotnet/core/tools/dotnet-run) 명령을 사용하여 애플리케이션을 로컬로 실행합니다.

    ```dotnetcli
    dotnet run --urls=https://localhost:5001/
    ```

1. 웹 브라우저를 열고 `https://localhost:5001`의 앱으로 이동합니다.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)
    
    페이지에 ASP.NET Core 6.0 웹앱 템플릿이 표시됩니다.
    
    :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code - 로컬 브라우저의 ASP.NET Core 6.0" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::
    
    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)
    
    ASP.NET Framework 4.8 웹앱 템플릿이 페이지에 표시됩니다.
    
    :::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code - 로컬 브라우저의 ASP.NET Framework 4.8" lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::
    
    ---

:::zone-end

## <a name="publish-your-web-app"></a>웹앱 게시

웹앱을 게시하려면 먼저 앱을 게시할 수 있는 새 App Service를 만들고 구성해야 합니다.

App Service를 설정하는 과정에서 다음 항목을 만듭니다.

- 서비스에 필요한 모든 Azure 리소스를 포함할 새 [리소스 그룹](../azure-resource-manager/management/overview.md#terminology)
- 앱을 호스팅하는 웹 서버 팜의 위치, 크기 및 기능을 지정하는 새 [호스팅 계획](overview-hosting-plans.md)

다음 단계에 따라 App Service 리소스를 만들고, 프로젝트를 게시합니다.

:::zone target="docs" pivot="development-environment-vs"

1. **솔루션 탐색기** 에서 마우스 오른쪽 단추로 **MyFirstAzureWebApp** 프로젝트를 클릭하고 **게시** 를 선택합니다.
1. **게시** 에서 **Azure** 를 선택한 후 **다음** 을 선택합니다.

    :::image type="content" source="media/quickstart-dotnet/vs-publish-target-Azure.png" alt-text="Visual Studio - 웹앱 게시 및 대상 Azure" lightbox="media/quickstart-dotnet/vs-publish-target-Azure.png" border="true":::

1. **특정 대상** 을 **Azure App Service(Linux)** 또는 **Azure App Service(Windows)** 중 하나로 선택합니다. 그런 후 **다음** 을 클릭합니다.

    > [!IMPORTANT]
    > ASP.NET Framework 4.8을 대상으로 하는 경우 **Azure App Service(Windows)** 를 사용합니다.

1. 옵션은 Azure에 이미 로그인했는지 여부와 Azure 계정에 연결된 Visual Studio 계정이 있는지 여부에 따라 달라집니다. **계정 추가** 또는 **로그인** 중 하나를 선택하여 Azure 구독에 로그인합니다. 이미 로그인한 경우 원하는 계정을 선택합니다.

    :::image type="content" source="media/quickstart-dotnet/sign-in-azure.png" border="true" alt-text="Visual Studio - Azure에 로그인 선택 대화 상자" lightbox="media/quickstart-dotnet/sign-in-azure.png" :::

1. **App Service 인스턴스** 오른쪽에서 **+** 를 선택합니다.

    :::image type="content" source="media/quickstart-dotnet/publish-new-app-service.png" border="true" alt-text="Visual Studio - 새 App Service 앱 대화 상자" lightbox="media/quickstart-dotnet/publish-new-app-service.png" :::

1. **구독** 의 경우 나열된 구독을 수락하거나 드롭다운 목록에서 새 구독을 선택합니다.
1. **리소스 그룹** 에 대해 **새로 만들기** 를 선택합니다. **새 리소스 그룹 이름** 에서 *myResourceGroup* 을 입력하고, **확인** 을 선택합니다.
1. **호스팅 계획** 에서 **새로 만들기** 를 선택합니다.
1. **호스팅 계획: 새로 만들기** 대화 상자에서 다음 표에 지정된 값을 입력합니다.

    | 설정          | 제안 값          | Description                                                           |
    |------------------|--------------------------|-----------------------------------------------------------------------|
    | **호스팅 계획** | *MyFirstAzureWebAppPlan* | App Service 플랜의 이름입니다.                                         |
    | **위치**     | *서유럽*            | 웹앱이 호스팅된 데이터 센터입니다.                           |
    | **크기**         | *Free*                   | [가격 책정 계층][app-service-pricing-tier]은 호스팅 기능을 결정합니다. |

    :::image type="content" source="media/quickstart-dotnet/create-new-hosting-plan.png" border="true" alt-text="새 호스팅 계획 만들기" lightbox="media/quickstart-dotnet/create-new-hosting-plan.png" :::

1. **이름** 에서 유효한 문자(`a-z`, `A-Z`, `0-9` 및 `-`)만 포함된 고유한 앱 이름을 입력합니다. 자동으로 생성된 고유한 이름을 적용할 수 있습니다. 웹앱의 URL은 `http://<app-name>.azurewebsites.net`이며, 여기서 `<app-name>`은 앱 이름입니다.
1. **만들기** 를 선택하여 Azure 리소스를 만듭니다.

    :::image type="content" source="media/quickstart-dotnet/web-app-name.png" border="true" alt-text="Visual Studio - 앱 리소스 만들기 대화 상자" lightbox="media/quickstart-dotnet/web-app-name.png" :::

   마법사가 완료되면 Azure 리소스가 만들어지고, ASP.NET Core 프로젝트를 게시할 준비가 됩니다.

1. **게시** 대화 상자의 **App Service 인스턴스** 에서 새 App Service 앱이 선택되어 있는지 확인한 다음, **마침** 을 선택합니다. Visual Studio에서 선택한 App Service 앱에 대한 게시 프로필을 만듭니다.
1. **게시** 페이지에서 **게시** 를 선택합니다. 경고 메시지가 표시되면 **계속** 을 클릭합니다.

    Visual Studio는 앱을 Azure에 빌드, 패키지 및 게시한 다음, 기본 브라우저에서 앱을 시작합니다.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)

    페이지에 ASP.NET Core 6.0 웹앱이 표시됩니다.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio - Azure의 ASP.NET Core 6.0 웹앱" :::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    ASP.NET Framework 4.8 웹앱이 페이지에 표시됩니다.

    :::image type="content" source="media/quickstart-dotnet/vs-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio - Azure의 ASP.NET Framework 4.8 웹앱":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

<!-- :::image type="content" source="media/quickstart-dotnet/vscode-sign-in-to-Azure.png" alt-text="Visual Studio Code - Sign in to Azure." border="true"::: -->

1. Visual Studio Code에서 [**명령 팔레트**](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette)를 엽니다(<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd>).
1. "Azure App Service: 웹앱에 배포"를 검색하여 선택합니다.
1. 프롬프트에 다음과 같이 응답합니다.

    1. *MyFirstAzureWebApp* 을 배포할 폴더로 선택합니다.
    1. 메시지가 나타나면 **구성 추가** 를 선택합니다.
    1. 메시지가 표시되면 Azure 계정에 로그인합니다.
    1. **구독** 을 선택합니다.
    1. **새 웹앱 만들기...고급** 을 선택합니다.
    1. **전역적으로 고유한 이름 입력** 에는 모든 Azure에서 고유한 이름을 사용합니다(*유효한 문자: `a-z`, `0-9` 및 `-`* ). 회사 이름과 앱 식별자를 조합하여 사용하는 것이 좋습니다.
    1. **새 리소스 그룹 만들기** 를 선택하고, `myResourceGroup`와 같은 이름을 지정합니다.
    1. **런타임 스택 선택** 메시지가 나타나면:
      - *.NET 6.0* 의 경우 **.NET 6** 을 선택합니다.
      - *.NET Framework 4.8* 의 경우 **ASP.NET V4.8** 을 선택합니다.
    1. 운영 체제(Windows 또는 Linux)를 선택합니다.
        - *.NET Framework 4.8* 의 경우 Windows가 암시적으로 선택됩니다.
    1. 가까운 위치를 선택합니다.
    1. **새 App Service 계획 만들기** 를 선택하고, 이름을 제공하고, **F1 체험** [가격 책정 계층][app-service-pricing-tier]을 선택합니다.
    1. Application Insights 리소스에 대해 **지금 건너뛰기** 를 선택합니다.

1. **항상 "MyFirstAzureWebApp" 작업 영역을 \<app-name>에 배포** 팝업에서 **예** 를 선택합니다. 이렇게 하면 동일한 작업 영역에 있는 한 Visual Studio Code가 매번 동일한 App Service 앱에 배포합니다.
1. 게시가 완료되면 알림에서 **웹 사이트 찾아보기** 를 선택하고 메시지가 나타나면 **열기** 를 선택합니다.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)

    페이지에 ASP.NET Core 6.0 웹앱이 표시됩니다.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code - Azure의 ASP.NET Core 6.0 웹앱":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    ASP.NET Framework 4.8 웹앱이 페이지에 표시됩니다.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code - Azure의 ASP.NET Framework 4.8 웹앱":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

1. [`az login`](/cli/azure/reference-index#az_login) 명령을 사용하고 프롬프트에 따라 Azure 계정에 로그인합니다.

    ```azurecli
    az login
    ```
    
1. [`az webapp up`](/cli/azure/webapp#az_webapp_up) 명령을 사용하여 로컬 *MyFirstAzureWebApp* 디렉터리에 코드를 배포합니다.

    ```azurecli
    az webapp up --sku F1 --name <app-name> --os-type <os>
    ```

    - `az` 명령이 인식되지 않으면 [사전 요구 사항](#prerequisites)의 설명대로 Azure CLI가 설치되어 있는지 확인합니다.
    - `<app-name>`을 모든 Azure에서 고유한 이름으로 바꿉니다(*유효한 문자는 `a-z`, `0-9` 및 `-`* ). 좋은 패턴은 회사 이름과 앱 식별자의 조합을 사용하는 것입니다.
    - `--sku F1` 인수는 **무료** [가격 책정 계층][app-service-pricing-tier]에 웹앱을 만듭니다. 이 인수를 생략하여 더 빠른 프리미엄 계층을 사용합니다. 이 경우 시간당 비용이 발생합니다.
    - `<os>`를 `linux` 또는 `windows`로 바꿉니다. *ASP.NET Framework 4.8* 을 대상으로 하는 경우 `windows`를 사용해야 합니다.
    - 선택적으로 인수 `--location <location-name>`을 포함할 수 있습니다. 여기서 `<location-name>`은 사용 가능한 Azure 지역입니다. Azure 계정에 허용되는 지역 목록은 [`az account list-locations`](/cli/azure/appservice#az_appservice_list_locations) 명령을 실행하여 검색할 수 있습니다.
    
    이 명령을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 실행되는 동안 리소스 그룹, App Service 계획 및 호스팅 앱 만들기, 로깅 구성, ZIP 배포 수행에 대한 메시지가 제공됩니다. 그런 다음, 앱의 URL이 포함된 메시지가 출력됩니다.
    
    ```azurecli
    You can launch the app at http://<app-name>.azurewebsites.net
    ```

1. 웹 브라우저를 열고 URL로 이동합니다.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)
    
    페이지에 ASP.NET Core 6.0 웹앱이 표시됩니다.
    
    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI - Azure의 ASP.NET Core 6.0 웹앱":::
    
    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)
    
    ASP.NET Framework 4.8 웹앱이 페이지에 표시됩니다.
    
    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="CLI - Azure의 ASP.NET Framework 4.8 웹앱":::

    -----

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-ps"
<!-- markdownlint-enable MD044 -->

> [!NOTE]
> Azure PowerShell은 Windows 호스팅 플랫폼에서 앱을 만드는 데 권장됩니다. Linux에서 앱을 만들려면 [Azure CLI](quickstart-dotnetcore.md?pivots=development-environment-cli)와 같은 다른 도구를 사용합니다.

1. [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) 명령을 사용하고 프롬프트에 따라 Azure 계정에 로그인합니다.

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

<!-- ### [Deploy to Windows](#tab/windows) -->

2. [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) 명령을 사용하여 새 앱을 만듭니다.

    ```azurepowershell-interactive
    New-AzWebApp -Name <app-name> -Location westeurope
    ```

    - `<app-name>`을 모든 Azure에서 고유한 이름으로 바꿉니다(*유효한 문자는 `a-z`, `0-9` 및 `-`* ). 좋은 패턴은 회사 이름과 앱 식별자의 조합을 사용하는 것입니다.
    - 선택적으로 매개 변수 `-Location <location-name>`을 포함할 수 있습니다. 여기서 `<location-name>`은 사용 가능한 Azure 지역입니다. Azure 계정에 허용되는 지역 목록은 [`Get-AzLocation`](/powershell/module/az.resources/get-azlocation) 명령을 실행하여 검색할 수 있습니다.

    이 명령을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 실행하는 동안 리소스 그룹, App Service 계획 및 App Service 리소스를 만듭니다.

    <!-- ### [Deploy to Linux](#tab/linux)
    
    2. Create the Azure resources you need:
    
        ```azurepowershell-interactive
        New-AzResourceGroup -Name myResourceGroup -Location westeurope
        New-AzAppServicePlan -ResourceGroupName myResourceGroup -Name myAppServicePlan -Location westeurope -Linux
        New-AzWebApp -ResourceGroupName myResourceGroup -AppServicePlan myAppServicePlan -Name <app-name>
        Set-AzWebApp -
        ```
    
        - Replace `<app-name>` with a name that's unique across all of Azure (*valid characters are `a-z`, `0-9`, and `-`*). A good pattern is to use a combination of your company name and an app identifier.
        - You can optionally specify a different location in the `-Location` parameter. You can retrieve a list of allowable regions for your Azure account by running the [`Get-AzLocation`](/powershell/module/az.resources/get-azlocation) command.
        - [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) creates a resource group to contain the resources.
        - [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) uses `-Linux` to create a Linux App Service plan, which hosts your app. The default pricing tier is `Free`, but you can change it with the `-Tier` parameter.
        - [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) creates the app itself.
    
    --- -->
    
1. 애플리케이션 루트 폴더에서 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 명령을 사용하여 배포할 로컬 *MyFirstAzureWebApp* 애플리케이션을 준비합니다.

    ```dotnetcli
    dotnet publish --configuration Release
    ```

1. 릴리스 디렉터리로 변경하고 콘텐츠에서 Zip 파일을 만듭니다.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)

    ```powershell-interactive
    cd bin\Release\net6.0\publish
    Compress-Archive -Path * -DestinationPath deploy.zip
    ```

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    ```powershell-interactive
    cd bin\Release\net48\publish
    Compress-Archive -Path * -DestinationPath deploy.zip
    ```

    -----

1. [Publish-AzWebApp](/powershell/module/az.websites/publish-azwebapp) 명령을 사용하여 Azure 앱에 Zip 파일을 게시합니다.

    ```azurepowershell-interactive
    Publish-AzWebApp -ResourceGroupName myResourceGroup -Name <app-name> -ArchivePath (Get-Item .\deploy.zip).FullName -Force
    ```

    > [!NOTE]
    > `-ArchivePath`에는 Zip 파일의 전체 경로가 필요합니다.

1. 웹 브라우저를 열고 URL로 이동합니다.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)
    
    페이지에 ASP.NET Core 6.0 웹앱이 표시됩니다.
    
    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI - Azure의 ASP.NET Core 6.0 웹앱":::
    
    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)
    
    ASP.NET Framework 4.8 웹앱이 페이지에 표시됩니다.
    
    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="CLI - Azure의 ASP.NET Framework 4.8 웹앱":::

    -----

:::zone-end

## <a name="update-the-app-and-redeploy"></a>앱 업데이트 및 재배포

웹앱을 업데이트하고 다시 배포하려면 다음 단계를 수행합니다.

:::zone target="docs" pivot="development-environment-vs"

1. **솔루션 탐색기** 의 프로젝트 아래에서 *Index.cshtml* 을 엽니다.
1. 첫 번째 `<div>` 요소를 다음 코드로 바꿉니다.

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   변경 내용을 저장합니다.

1. Azure에 다시 배포하려면 **솔루션 탐색기** 에서 **MyFirstAzureWebApp** 프로젝트를 마우스 오른쪽 버튼으로 클릭하고 **게시** 를 선택합니다.
1. **게시** 요약 페이지에서 **게시** 를 선택합니다.

    게시가 완료되면 Visual Studio가 웹앱의 URL로 브라우저를 시작합니다.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)

    페이지에 업데이트된 ASP.NET Core 6.0 웹앱이 표시됩니다.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio - Azure의 업데이트된 ASP.NET Core 6.0 웹앱":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    업데이트된 ASP.NET Framework 4.8 웹앱이 페이지에 표시됩니다.

    :::image type="content" source="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio - Azure의 업데이트된 ASP.NET Framework 4.8 웹앱":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

1. *Index.cshtml* 이 열립니다.
1. 첫 번째 `<div>` 요소를 다음 코드로 바꿉니다.

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   변경 내용을 저장합니다.

1. Visual Studio Code에서 [**명령 팔레트**](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette)를 엽니다(<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd>).
1. "Azure App Service: 웹앱에 배포"를 검색하여 선택합니다. 이전 단계에서 작업 영역을 배포할 앱을 기억하도록 Visual Studio Code에 지시했습니다.
1. 메시지가 나타나면 **배포** 를 선택합니다.
1. 게시가 완료되면 알림에서 **웹 사이트 찾아보기** 를 선택하고 메시지가 나타나면 **열기** 를 선택합니다.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)

    페이지에 업데이트된 ASP.NET Core 6.0 웹앱이 표시됩니다.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code - Azure의 업데이트된 ASP.NET Core 6.0 웹앱":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    업데이트된 ASP.NET Framework 4.8 웹앱이 페이지에 표시됩니다.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code - Azure의 업데이트된 ASP.NET Framework 4.8 웹앱":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

로컬 디렉터리에서 *Index.cshtml* 파일을 엽니다. 첫 번째 `<div>` 요소를 바꿉니다.

```razor
<div class="jumbotron">
    <h1>.NET 💜 Azure</h1>
    <p class="lead">Example .NET app to Azure App Service.</p>
</div>
```

변경 내용을 저장한 다음, `az webapp up` 명령을 사용하여 앱을 다시 배포합니다.

### <a name="net-60"></a>[.NET 6.0](#tab/net60)

ASP.NET Core 6.0은 플랫폼 간이며, 이전 배포에 따라 `<os>`를 `linux` 또는 `windows`로 바꿉니다.

```azurecli
az webapp up --os-type <os>
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

ASP.NET Framework 4.8에는 프레임워크 종속성이 있으며 Windows에서 호스트해야 합니다.

```azurecli
az webapp up --os-type windows
```

> [!TIP]
> Linux에서 .NET 앱을 호스트하는 데 관심이 있는 경우 [ASP.NET Framework에서 ASP.NET Core](/aspnet/core/migration/proper-to-2x)로 마이그레이션하는 것이 좋습니다.

---

이 명령은 앱 이름, 리소스 그룹 및 App Service 계획을 포함하여 *.azure/config* 파일에서 로컬로 캐시된 값을 사용합니다.

배포가 완료되면 **앱으로 이동** 단계에서 열린 브라우저 창으로 다시 전환하고 새로 고침을 누릅니다.

### <a name="net-60"></a>[.NET 6.0](#tab/net60)

페이지에 업데이트된 ASP.NET Core 6.0 웹앱이 표시됩니다.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI - Azure의 업데이트된 ASP.NET Core 6.0 웹앱":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

업데이트된 ASP.NET Framework 4.8 웹앱이 페이지에 표시됩니다.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="CLI - Azure의 업데이트된 ASP.NET Framework 4.8 웹앱":::

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-ps"
<!-- markdownlint-enable MD044 -->

1. 로컬 디렉터리에서 *Index.cshtml* 파일을 엽니다. 첫 번째 `<div>` 요소를 바꿉니다.

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

1. 애플리케이션 루트 폴더에서 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 명령을 사용하여 배포할 로컬 *MyFirstAzureWebApp* 애플리케이션을 준비합니다.

    ```dotnetcli
    dotnet publish --configuration Release
    ```

1. 릴리스 디렉터리로 변경하고 콘텐츠에서 Zip 파일을 만듭니다.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)

    ```powershell-interactive
    cd bin\Release\net6.0\publish
    Compress-Archive -Path * -DestinationPath deploy.zip
    ```

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    ```powershell-interactive
    cd bin\Release\net48\publish
    Compress-Archive -Path * -DestinationPath deploy.zip
    ```

    -----

1. [Publish-AzWebApp](/powershell/module/az.websites/publish-azwebapp) 명령을 사용하여 Azure 앱에 Zip 파일을 게시합니다.

    ```azurepowershell-interactive
    Publish-AzWebApp -ResourceGroupName myResourceGroup -Name <app-name> -ArchivePath (Get-Item .\deploy.zip).FullName -Force
    ```

    > [!NOTE]
    > `-ArchivePath`에는 Zip 파일의 전체 경로가 필요합니다.

1. 배포가 완료되면 **앱으로 이동** 단계에서 열린 브라우저 창으로 다시 전환하고 새로 고침을 누릅니다.

    ### <a name="net-60"></a>[.NET 6.0](#tab/net60)
    
    페이지에 업데이트된 ASP.NET Core 6.0 웹앱이 표시됩니다.
    
    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI - Azure의 업데이트된 ASP.NET Core 6.0 웹앱":::
    
    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)
    
    업데이트된 ASP.NET Framework 4.8 웹앱이 페이지에 표시됩니다.
    
    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="CLI - Azure의 업데이트된 ASP.NET Framework 4.8 웹앱":::
    
    ---
    
:::zone-end

## <a name="manage-the-azure-app"></a>Azure 앱 관리

웹앱을 관리하려면 [Azure Portal](https://portal.azure.com)로 이동하고, **App Services** 를 검색하여 선택합니다.

:::image type="content" source="media/quickstart-dotnetcore/app-services.png" alt-text="Azure Portal - App Services 선택 옵션":::

**App Service** 페이지에서 웹앱의 이름을 선택합니다.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Azure Portal - 예제 웹앱이 선택된 App Services 페이지":::

웹앱의 **개요** 페이지에는 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 옵션이 포함되어 있습니다. 왼쪽 메뉴는 앱을 구성하기 위한 추가 페이지를 제공합니다.

:::image type="content" source="media/quickstart-dotnetcore/web-app-overview-page.png" alt-text="Azure Portal - App Service 개요 페이지":::

<!--
## Clean up resources - H2 added from the next three includes
-->
:::zone target="docs" pivot="development-environment-vs"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

:::zone target="docs" pivot="development-environment-vscode"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->
[!INCLUDE [Clean-up CLI resources](../../includes/cli-samples-clean-up.md)]
:::zone-end

:::zone target="docs" pivot="development-environment-ps"
<!-- markdownlint-enable MD044 -->
[!INCLUDE [Clean-up PowerShell resources](../../includes/powershell-samples-clean-up.md)]
:::zone-end

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 ASP.NET 웹앱을 만들고 Azure App Service에 배포했습니다.

### <a name="net-60"></a>[.NET 6.0](#tab/net60)

다음 문서로 넘어가서 .NET Core 앱을 만들고 SQL Database에 연결하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [자습서: SQL 데이터베이스를 사용하는 ASP.NET Core 앱](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core 앱 구성](configure-language-dotnetcore.md)

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

다음 문서로 넘어가서 .NET Framework 앱을 만들고 SQL Database에 연결하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [자습서: SQL 데이터베이스를 사용하는 ASP.NET 앱](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [ASP.NET Framework 앱 구성](configure-language-dotnet-framework.md)

---

[app-service-pricing-tier]: https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
