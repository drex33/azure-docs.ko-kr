---
title: Azure Functions 앱에서 Azure App Configuration 동적 구성 사용에 대한 자습서 | Microsoft Docs
description: 이 자습서에서는 Azure Functions 앱의 구성 데이터를 동적으로 업데이트하는 방법을 알아봅니다.
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: devx-track-csharp, azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: ffa2b1a4537093f9a612f0e57b7f416ee7ed0012
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301915"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>자습서: Azure Functions 앱에서 동적 구성 사용

App Configuration .NET 구성 공급자는 애플리케이션 작업 기반의 구성을 동적으로 캐싱하고 새로 고치는 것을 지원합니다. 이 자습서에서는 코드에서 동적 구성 업데이트를 구현하는 방법을 보여줍니다. 빠른 시작에서 소개한 Azure Functions 앱을 기반으로 합니다. 계속 진행하기 전에, 먼저 [Azure App Configuration을 사용하여 Azure Functions 앱 만들기](./quickstart-azure-functions-csharp.md)를 완료합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * App Configuration 저장소의 변경 내용에 따라 구성을 업데이트하도록 Azure Functions 앱을 설정합니다.
> * Azure Functions 호출에 최신 구성을 삽입합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- **Azure 개발** 워크로드를 사용하는 [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [Azure Functions 도구](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- [Azure App Configuration으로 Azure Functions 앱 만들기](./quickstart-azure-functions-csharp.md) 빠른 시작 완료

## <a name="reload-data-from-app-configuration"></a>App Configuration에서 데이터 다시 로드

1. *Startup.cs* 를 열고 `ConfigureAppConfiguration` 메서드를 업데이트합니다. 

   `ConfigureRefresh` 메서드는 애플리케이션 내에서 새로 고침이 트리거될 때마다 변경 내용을 확인할 설정을 등록합니다. 이 작업은 이후 단계에서 `_configurationRefresher.TryRefreshAsync()`를 추가할 때 수행합니다. `refreshAll` 매개 변수는 등록된 설정에서 변경 내용이 검색될 때마다 App Configuration 공급자에게 전체 구성을 다시 로드하도록 지시합니다.

    새로 고침을 위해 등록된 모든 설정의 기본 캐시 만료 시간은 30초입니다. `AzureAppConfigurationRefreshOptions.SetCacheExpiration` 메서드를 호출하여 업데이트할 수 있습니다.

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   // Load all keys that start with `TestApp:`
                   .Select("TestApp:*")
                   // Configure to reload configuration if the registered sentinel key is modified
                   .ConfigureRefresh(refreshOptions =>
                      refreshOptions.Register("TestApp:Settings:Sentinel", refreshAll: true));
        });
    }
    ```

   > [!TIP]
   > App Configuration에서 여러 키 값을 업데이트하는 경우에는 일반적으로 애플리케이션이 모든 변경 작업을 수행하기 전에 구성을 다시 로드하지 않도록 합니다. *센티널* 키를 등록하고, 다른 모든 구성 변경이 완료된 경우에만 업데이트할 수 있습니다. 이렇게 하면 애플리케이션에서 구성의 일관성을 유지할 수 있습니다.

2. 종속성 주입을 통해 Azure App Configuration 서비스를 사용할 수 있도록 하려면 `Configure` 메서드를 업데이트합니다.

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
    }
    ```

3. *Function1.cs* 를 열고 다음 네임스페이스를 추가합니다.

    ```csharp
    using System.Linq;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   생성자를 업데이트하여 `IConfigurationRefresher`의 인스턴스를 가져올 수 있는 종속성 주입을 통해 `IConfigurationRefresherProvider`의 인스턴스를 가져옵니다.

    ```csharp
    private readonly IConfiguration _configuration;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IConfiguration configuration, IConfigurationRefresherProvider refresherProvider)
    {
        _configuration = configuration;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

4. Functions 호출을 시작할 때 `Run` 메서드를 업데이트하고 `TryRefreshAsync` 메서드를 사용하여 구성을 새로 고치도록 신호를 보냅니다. 이는 캐시 만료 시간 범위에 도달해야만 작동합니다. 현재 Functions 호출을 차단하지 않고 구성을 새로 고치려면 `await` 연산자를 제거합니다. 이 경우 이후 Functions 호출에서 업데이트된 값을 가져옵니다.

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync(); 

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>로컬에서 함수 테스트

1. **ConnectionString** 이라는 환경 변수를 설정하고, 앱 구성 저장소에 대한 액세스 키로 설정합니다. Windows 명령 프롬프트를 사용하는 경우 다음 명령을 실행하고, 명령 프롬프트를 다시 시작하여 변경 내용을 적용합니다.

    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell을 사용하는 경우 다음 명령을 실행합니다.

    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    macOS 또는 Linux를 사용하는 경우 다음 명령을 실행합니다.

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. 함수를 테스트하려면 F5 키를 누릅니다. 메시지가 표시되면 Visual Studio에서 **Azure Functions Core(CLI)** 도구를 다운로드하여 설치하도록 요구하는 요청을 수락합니다. 또한 도구에서 HTTP 요청을 처리할 수 있도록 방화벽 예외를 사용하도록 설정해야 할 수도 있습니다.

3. Azure Functions 런타임 출력에서 함수의 URL을 복사합니다.

    ![VS에서 빠른 시작 함수 디버깅](./media/quickstarts/function-visual-studio-debugging.png)

4. 브라우저의 주소 표시줄에 HTTP 요청에 대한 URL을 붙여 넣습니다. 다음 이미지에서는 함수에서 반환된 로컬 GET 요청에 대한 브라우저의 응답을 보여 줍니다.

    ![빠른 시작 함수 로컬 시작](./media/quickstarts/dotnet-core-function-launch-local.png)

5. [Azure Portal](https://portal.azure.com)에 로그인합니다. **모든 리소스** 를 선택하고, 빠른 시작에서 만든 App Configuration 저장소를 선택합니다.

6. **구성 탐색기** 를 선택하고, 다음 키의 값을 업데이트합니다.

    | 키 | 값 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration의 데이터 - 업데이트됨 |

   그런 다음, 예를 들어 센티널 키를 만들거나 이미 있는 경우 해당 값을 수정합니다.

    | 키 | 값 |
    |---|---|
    | TestApp:Settings:Sentinel | v1 |


7. 브라우저를 몇 번 새로 고칩니다. 캐시된 설정이 30초 후에 만료되면 페이지에 업데이트된 값이 있는 Functions 호출의 응답이 표시됩니다.

    ![빠른 시작 함수 새로 고침 로컬](./media/quickstarts/dotnet-core-function-refresh-local.png)

> [!NOTE]
> 이 자습서에 사용된 예제 코드는 [App Configuration GitHub 리포지토리](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)에서 다운로드할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Functions 앱을 사용하도록 설정하여 App Configuration의 구성 설정을 동적으로 새로 고칩니다. Azure 관리 ID를 사용하여 App Configuration에 대한 액세스를 간소화하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [관리 ID 통합](./howto-integrate-azure-managed-service-identity.md)
