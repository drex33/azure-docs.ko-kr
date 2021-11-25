---
title: ASP.NET Core 지원 Azure App Configuration용 빠른 시작 | Microsoft Docs
description: Azure App Configuration을 사용하여 ASP.NET Core 앱을 만들어 ASP.NET Core 애플리케이션을 위한 애플리케이션 설정의 스토리지 및 관리를 중앙 집중화합니다.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp, contperf-fy21q1, mode-other
ms.topic: quickstart
ms.date: 09/25/2020
ms.author: alkemper
ms.openlocfilehash: 70a3ec7a63c47f3e360f909c2b7350a16ea17014
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133047460"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>빠른 시작: App Configuration을 사용하여 ASP.NET Core 앱 만들기

이 빠른 시작에서는 Azure App Configuration을 사용하여 ASP.NET Core 앱을 위한 애플리케이션 설정의 스토리지 및 관리를 중앙 집중화합니다. ASP.NET Core는 앱에서 지정한 하나 이상의 데이터 원본의 설정을 사용하여 단일 키-값 기반 구성 개체를 빌드합니다. 이러한 데이터 원본은 *구성 공급자* 라고 합니다. App Configuration의 .NET Core 클라이언트가 구성 공급자로 구현되므로 서비스가 다른 데이터 원본처럼 표시됩니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/dotnet)
* [.NET Core SDK](https://dotnet.microsoft.com/download)

> [!TIP]
> Azure Cloud Shell은 이 문서의 명령줄을 실행하는 데 사용할 수 있는 무료 대화형 셸입니다. 여기에는 .NET Core SDK를 비롯한 일반적인 Azure 도구가 미리 설치되어 있습니다. Azure 구독에 로그인한 경우 shell.azure.com에서 [Azure Cloud Shell](https://shell.azure.com)을 시작합니다. Azure Cloud Shell에 대한 자세한 내용은 [설명서를 참조하세요](../cloud-shell/overview.md).

## <a name="create-an-app-configuration-store"></a>App Configuration 저장소 만들기

[!INCLUDE[Azure App Configuration resource creation steps](../../includes/azure-app-configuration-create.md)]

7. **작업** > **구성 탐색기** > **만들기** > **키-값** 을 차례로 선택하여 다음 키-값 쌍을 추가합니다.

    | 키                                | 값                               |
    |------------------------------------|-------------------------------------|
    | `TestApp:Settings:BackgroundColor` | *#FFF*                              |
    | `TestApp:Settings:FontColor`       | *#000*                              |
    | `TestApp:Settings:FontSize`        | *24*                                |
    | `TestApp:Settings:Message`         | *Azure App Configuration의 정보* |

    지금은 **레이블** 과 **콘텐츠 형식** 을 비워 둡니다. **적용** 을 선택합니다.

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core 웹앱 만들기

[.NET Core CLI(명령줄 인터페이스)](/dotnet/core/tools)를 사용하여 새 ASP.NET Core MVC 프로젝트를 만듭니다. [Azure Cloud Shell](https://shell.azure.com)은 이러한 도구를 제공합니다. Windows, macOS 및 Linux 플랫폼에서 사용할 수도 있습니다.

다음 명령을 실행하여 새 *TestAppConfig* 폴더에 ASP.NET Core MVC 프로젝트를 만듭니다.

```dotnetcli
dotnet new mvc --no-https --output TestAppConfig
```

[!INCLUDE[Add Secret Manager support to an ASP.NET Core project](../../includes/azure-app-configuration-add-secret-manager.md)]

## <a name="connect-to-the-app-configuration-store"></a>App Configuration 저장소에 연결

1. 다음 명령을 실행하여 [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) NuGet 패키지 참조를 추가합니다.

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. *.csproj* 파일과 동일한 디렉터리에서 다음 명령을 실행합니다. 이 명령은 비밀 관리자를 사용하여 App Configuration 저장소에 대한 연결 문자열을 저장하는 `ConnectionStrings:AppConfig`라는 비밀을 저장합니다. `<your_connection_string>` 자리 표시자를 App Configuration 저장소의 연결 문자열로 바꿉니다. 연결 문자열은 Azure Portal의 **액세스 키** 에서 찾을 수 있습니다.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```

    > [!IMPORTANT]
    > 일부 셸에서는 연결 문자열을 따옴표로 묶지 않으면 연결 문자열이 잘립니다. `dotnet user-secrets list` 명령의 출력에 전체 연결 문자열이 표시되는지 확인합니다. 표시되지 않으면 연결 문자열을 따옴표로 묶고 명령을 다시 실행합니다.

    비밀 관리자는 웹앱을 로컬로 테스트하는 데만 사용됩니다. 앱이 [Azure App Service](https://azure.microsoft.com/services/app-service/web)에 배포되면 비밀 관리자 대신 App Service의 **연결 문자열** 애플리케이션 설정을 사용하여 연결 문자열을 저장합니다.

    .NET Core 구성 API를 사용하여 이 비밀에 액세스합니다. 콜론(`:`)은 지원되는 모든 플랫폼에서 구성 API를 통해 구성 이름에서 작동합니다. 자세한 내용은 [구성 키 및 값](/aspnet/core/fundamentals/configuration#configuration-keys-and-values)을 참조하세요.

1. *Program.cs* 에서 .NET Core 구성 API 네임스페이스에 대한 참조를 추가합니다.

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

1. `AddAzureAppConfiguration` 메서드를 호출하여 App Configuration을 사용하도록 `CreateWebHostBuilder` 메서드를 업데이트합니다.

    > [!IMPORTANT]
    > .NET Core 3.x에 `CreateHostBuilder`는 `CreateWebHostBuilder`를 대체합니다. 사용자 환경에 따라 올바른 구문을 선택합니다.

     #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(connection);
                }).UseStartup<Startup>());
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(connection);
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                var connection = settings.GetConnectionString("AppConfig");
                config.AddAzureAppConfiguration(connection);
            })
            .UseStartup<Startup>();
    ```

    ---

    위의 변경 내용으로 [App Configuration에 대한 구성 공급자](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration)가 .NET Core 구성 API에 등록되었습니다.

## <a name="read-from-the-app-configuration-store"></a>App Configuration 저장소에서 읽기

App Configuration 저장소에 저장된 값을 읽고 표시하려면 다음 단계를 완료합니다. .NET Core 구성 API는 저장소에 액세스하는 데 사용됩니다. Razor 구문은 키 값을 표시하는 데 사용됩니다.

*\<app root>/Views/Home/Index.cshtml* 을 열고, 콘텐츠를 다음 코드로 바꿉니다.

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<style>
    body {
        background-color: @Configuration["TestApp:Settings:BackgroundColor"]
    }
    h1 {
        color: @Configuration["TestApp:Settings:FontColor"];
        font-size: @Configuration["TestApp:Settings:FontSize"]px;
    }
</style>

<h1>@Configuration["TestApp:Settings:Message"]</h1>
```

위의 코드에서 App Configuration 저장소의 키는 다음과 같이 사용됩니다.

* `TestApp:Settings:BackgroundColor` 키의 값은 CSS `background-color` 속성에 할당됩니다.
* `TestApp:Settings:FontColor` 키의 값은 CSS `color` 속성에 할당됩니다.
* `TestApp:Settings:FontSize` 키의 값은 CSS `font-size` 속성에 할당됩니다.
* `TestApp:Settings:Message` 키의 값이 머리글로 표시됩니다.

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

1. .NET Core CLI를 사용하여 앱을 빌드하려면 프로젝트의 루트 디렉터리로 이동합니다. 명령 셸에서 다음 명령을 실행합니다.

    ```dotnetcli
    dotnet build
    ```

1. 빌드가 성공적으로 완료되면 다음 명령을 실행하여 웹앱을 로컬로 실행합니다.

    ```dotnetcli
    dotnet run
    ```

1. 로컬 머신에서 작업하는 경우 브라우저를 사용하여 `http://localhost:5000`으로 이동합니다. 이 주소는 로컬에서 호스트되는 웹앱의 기본 URL입니다. Azure Cloud Shell에서 작업하는 경우 **웹 미리 보기** 단추와 **구성** 을 차례로 선택합니다.

    ![웹 미리 보기 단추 찾기](./media/quickstarts/cloud-shell-web-preview.png)

    미리 보기에 대한 포트를 구성하라는 메시지가 표시되면 *5000* 을 입력하고 **열기 및 찾기** 를 선택합니다. 웹 페이지에서 “Azure App Configuration의 정보”를 읽습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서 관련 정보는 다음과 같습니다.

* 새 App Configuration 저장소를 프로비저닝했습니다.
* App Configuration 저장소의 .NET Core 구성 공급자를 등록했습니다.
* 구성 공급자를 사용하여 App Configuration 저장소의 키를 읽었습니다.
* Razor 구문을 사용하여 App Configuration 저장소의 키 값을 표시했습니다.

구성 설정을 동적으로 새로 고치도록 ASP.NET Core 앱을 구성하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [동적 구성을 사용하도록 설정](./enable-dynamic-configuration-aspnet-core.md)
