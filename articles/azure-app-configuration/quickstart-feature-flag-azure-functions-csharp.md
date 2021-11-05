---
title: Azure Functions에 기능 플래그를 추가하기 위한 빠른 시작 | Microsoft Docs
description: 이 빠른 시작에서는 Azure App Configuration의 기능 플래그와 함께 Azure Functions를 사용하고 함수를 로컬로 테스트합니다.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 8/26/2020
ms.author: alkemper
ms.openlocfilehash: 22de8b7775d6825d99afe8eb4f0dc043f8ec639c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031559"
---
# <a name="quickstart-add-feature-flags-to-an-azure-functions-app"></a>빠른 시작: Azure Functions 앱에 기능 플래그 추가

이 빠른 시작에서는 Azure Functions 앱을 만들고 기능 플래그를 사용합니다. Azure App Configuration의 기능 관리를 사용하여 중앙에서 모든 기능 플래그를 저장하고 상태를 제어합니다.

.NET 기능 관리 라이브러리는 기능 플래그 지원을 통해 프레임워크를 확장합니다. 이 라이브러리는 .NET 구성 시스템을 기반으로 빌드됩니다. 해당 .NET 구성 공급자를 통해 App Configuration과 통합됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- **Azure 개발** 워크로드를 사용하는 [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [Azure Functions 도구](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>App Configuration 저장소 만들기

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. **기능 관리자** >  **+추가** 를 선택하여 `Beta`라는 기능 플래그를 추가합니다.

    > [!div class="mx-imgBorder"]
    > ![Beta라는 기능 플래그 사용](media/add-beta-feature-flag.png)

    지금은 `label` 및 `Description`을 정의하지 않은 상태로 둡니다.

8. **적용** 을 선택하여 새 기능 플래그를 저장합니다.

## <a name="create-a-functions-app"></a>Functions 앱 만들기

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>App Configuration 저장소에 연결

이 프로젝트는 [.NET Azure Functions의 종속성 주입](../azure-functions/functions-dotnet-dependency-injection.md)을 사용하게 됩니다. 기능 플래그가 저장된 추가 구성 원본으로 Azure App Configuration을 추가합니다.

1. 마우스 오른쪽 단추로 프로젝트를 클릭하고, **NuGet 패키지 관리** 를 선택합니다. **찾아보기** 탭에서 다음 NuGet 패키지를 검색하여 프로젝트에 추가합니다.
   - [Microsoft.Extensions.Configuration.AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration/) 버전 4.1.0 이상
   - [Microsoft FeatureManagement](https://www.nuget.org/packages/Microsoft.FeatureManagement/) 버전 2.2.0 이상
   - [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/) 버전 1.1.0 이상 

2. 다음 코드를 사용하여 새 파일 *Startup.cs* 를 추가합니다. `FunctionsStartup` 추상 클래스를 구현하는 `Startup`이라는 클래스를 정의합니다. 어셈블리 특성은 Azure Functions 시작 중에 사용되는 형식 이름을 지정하는 데 사용됩니다.

    ```csharp
    using System;
    using Microsoft.Azure.Functions.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.FeatureManagement;

    [assembly: FunctionsStartup(typeof(FunctionApp.Startup))]

    namespace FunctionApp
    {
        class Startup : FunctionsStartup
        {
            public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
            {
            }

            public override void Configure(IFunctionsHostBuilder builder)
            {
            }
        }
    }
    ```


3. `ConfigureAppConfiguration` 메서드를 업데이트하고 `AddAzureAppConfiguration()`을 호출하여 Azure App Configuration 공급자를 추가 구성 원본으로 추가합니다. 

    `UseFeatureFlags()` 메서드는 공급자에게 기능 플래그를 로드하도록 지시합니다. 모든 기능 플래그는 기본적으로 30초 후에 캐시가 만료된 후 변경 내용을 다시 확인합니다. `UseFeatureFlags` 메서드에 전달된 `FeatureFlagsOptions.CacheExpirationInterval` 속성을 설정하여 만료 간격을 업데이트할 수 있습니다. 

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .Select("_")
                   .UseFeatureFlags();
        });
    }
    ```

    > [!TIP]
    > 기능 플래그 이외의 구성이 애플리케이션에 로드되는 것을 원하지 않는 경우 `Select("_")`를 호출하여 존재하지 않는 더미 키 `"_"`만 로드할 수 있습니다. 기본적으로 `Select` 메서드를 호출하지 않으면 App Configuration 저장소의 모든 구성 키 값이 로드됩니다.

4. 종속성 주입을 통해 Azure App Configuration 서비스 및 기능 관리자를 사용할 수 있도록 하려면 `Configure` 메서드를 업데이트합니다.

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
        builder.Services.AddFeatureManagement();
    }
    ```

5. *Function1.cs* 를 열고 다음 네임스페이스를 추가합니다.

    ```csharp
    using System.Linq;
    using Microsoft.FeatureManagement;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   종속성 주입을 통해 `_featureManagerSnapshot` 및 `IConfigurationRefresherProvider`의 인스턴스를 가져오는 데 사용되는 생성자를 추가합니다. `IConfigurationRefresherProvider`에서 `IConfigurationRefresher`의 인스턴스를 가져올 수 있습니다.

    ```csharp
    private readonly IFeatureManagerSnapshot _featureManagerSnapshot;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IFeatureManagerSnapshot featureManagerSnapshot, IConfigurationRefresherProvider refresherProvider)
    {
        _featureManagerSnapshot = featureManagerSnapshot;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

6. `Run` 메서드를 업데이트하여 기능 플래그의 상태에 따라 표시된 메시지의 값을 변경합니다.

   `TryRefreshAsync` 메서드는 함수 호출의 시작 부분에서 기능 플래그를 새로 고치기 위해 호출됩니다. 이는 캐시 만료 시간 범위에 도달해야만 작동합니다. 현재 함수 호출을 차단하지 않고 기능 플래그를 새로 고치려면 `await` 연산자를 제거합니다. 이 경우 이후 함수 호출에서 업데이트된 값을 가져옵니다.

    ```csharp
    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
        ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync();

        string message = await _featureManagerSnapshot.IsEnabledAsync("Beta")
                ? "The Feature Flag 'Beta' is turned ON"
                : "The Feature Flag 'Beta' is turned OFF";

        return (ActionResult)new OkObjectResult(message);
    }
    ```

## <a name="test-the-function-locally"></a>로컬에서 함수 테스트

1. **ConnectionString** 이라는 환경 변수를 설정합니다. 여기서 값은 **액세스 키** 의 App Configuration 저장소에서 이전에 검색한 연결 문자열입니다. Windows 명령 프롬프트를 사용하는 경우 다음 명령을 실행하고, 명령 프롬프트를 다시 시작하여 변경 내용을 적용합니다.

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell을 사용하는 경우 다음 명령을 실행합니다.

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    macOS 또는 Linux를 사용하는 경우 다음 명령을 실행합니다.

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. F5를 눌러 함수를 테스트합니다. 메시지가 표시되면 Visual Studio에서 **Azure Functions Core(CLI)** 도구를 다운로드하여 설치하도록 요구하는 요청을 수락합니다. 또한 도구에서 HTTP 요청을 처리할 수 있도록 방화벽 예외를 사용하도록 설정해야 할 수도 있습니다.

1. Azure Functions 런타임 출력에서 함수의 URL을 복사합니다.

    ![VS에서 빠른 시작 함수 디버깅](./media/quickstarts/function-visual-studio-debugging.png)

1. 브라우저의 주소 표시줄에 HTTP 요청에 대한 URL을 붙여 넣습니다. 다음 이미지는 `Beta` 기능 플래그가 비활성화되었음을 나타내는 응답을 보여줍니다. 

    ![빠른 시작 함수 기능 플래그 사용 안 함](./media/quickstarts/functions-launch-ff-disabled.png)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **모든 리소스** 를 선택하고 사용자가 만든 App Configuration 저장소를 선택합니다.

1. **기능 관리자** 를 선택하고, **Beta** 키의 상태를 **On** 으로 변경합니다.

1. 브라우저를 몇 번 새로 고칩니다. 캐시된 기능 플래그가 30초 후에 만료되면 아래 이미지에 표시된 것처럼 기능 플래그 `Beta`가 켜져 있음을 나타내도록 변경해야 합니다.
 
    ![빠른 시작 함수 기능 플래그 사용](./media/quickstarts/functions-launch-ff-enabled.png)

> [!NOTE]
> 이 자습서에 사용된 예제 코드는 [Azure App Configuration GitHub 리포지토리](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)에서 다운로드할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 기능 플래그를 만들고, [Microsoft.FeatureManagement](/dotnet/api/microsoft.featuremanagement) 라이브러리를 통해 Azure Functions 앱에서 사용했습니다.

- [기능 관리](./concept-feature-management.md)에 대한 자세한 정보
- [기능 플래그 관리](./manage-feature-flags.md)
- [조건부 기능 플래그 사용](./howto-feature-filters-aspnet-core.md)
- [대상 그룹에 대한 기능 단계적 롤아웃 사용](./howto-targetingfilter-aspnet-core.md)
- [Azure Functions 앱에서 동적 구성 사용](./enable-dynamic-configuration-azure-functions-csharp.md)