---
title: Azure Functions와 Azure App Configuration에 대한 빠른 시작 | Microsoft Docs
description: 이 빠른 시작에서는 Azure App Configuration 및 C#을 사용하여 Azure Functions 앱을 만듭니다. App Configuration 저장소를 만들고 연결합니다. 로컬로 함수를 테스트합니다.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp, mode-other
ms.topic: quickstart
ms.date: 06/02/2021
ms.author: alkemper
ms.openlocfilehash: 9997a8d615a503a468efbffcd29a2f02df0bec9c
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133076296"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>빠른 시작: Azure App Configuration으로 Azure Functions 앱 만들기

이 빠른 시작에서는 Azure App Configuration 서비스를 Azure Functions 앱에 통합하여 코드와 별도로 애플리케이션 설정의 스토리지 및 관리를 중앙 집중화합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/dotnet)
- **Azure 개발** 워크로드를 사용하는 [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [Azure Functions 도구](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>App Configuration 저장소 만들기

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. **구성 탐색기** >  **+ 만들기** > **키-값** 을 차례로 선택하여 다음 키-값 쌍을 추가합니다.

    | 키 | 값 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration의 정보 |

    지금은 **레이블** 과 **콘텐츠 형식** 을 비워 두세요.

8. **적용** 을 선택합니다.

## <a name="create-a-functions-app"></a>Functions 앱 만들기

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>App Configuration 저장소에 연결
이 프로젝트는 [.NET Azure Functions에서 종속성 주입](../azure-functions/functions-dotnet-dependency-injection.md)을 사용하고 추가 구성 원본으로 Azure 앱 구성을 추가합니다.

1. 마우스 오른쪽 단추로 프로젝트를 클릭하고, **NuGet 패키지 관리** 를 선택합니다. **찾아보기** 탭에서 다음 NuGet 패키지를 검색하여 프로젝트에 추가합니다.
   - [Microsoft.Extensions.Configuration.AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration/) 버전 4.1.0 이상
   - [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/) 버전 1.1.0 이상 

2. 다음 코드를 사용하여 새 파일 *Startup.cs* 를 추가합니다. `FunctionsStartup` 추상 클래스를 구현하는 `Startup`이라는 클래스를 정의합니다. 어셈블리 특성은 Azure Functions 시작 중에 사용되는 형식 이름을 지정하는 데 사용됩니다.

    `ConfigureAppConfiguration` 메서드가 재정의되고 Azure 앱 구성 공급자가 `AddAzureAppConfiguration()`을 호출하여 추가 구성 소스로 추가됩니다. 이 시점에서 서비스를 등록할 필요가 없으므로 `Configure` 메서드는 비어 있습니다.
    
    ```csharp
    using System;
    using Microsoft.Azure.Functions.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;

    [assembly: FunctionsStartup(typeof(FunctionApp.Startup))]

    namespace FunctionApp
    {
        class Startup : FunctionsStartup
        {
            public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
            {
                string cs = Environment.GetEnvironmentVariable("ConnectionString");
                builder.ConfigurationBuilder.AddAzureAppConfiguration(cs);
            }

            public override void Configure(IFunctionsHostBuilder builder)
            {
            }
        }
    }
    ```

3. *Function1.cs* 를 열고 다음 네임스페이스를 추가합니다.

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

   종속성 주입을 통해 `IConfiguration`의 인스턴스를 가져오는 데 사용되는 생성자를 추가합니다.

    ```csharp
    private readonly IConfiguration _configuration;

    public Function1(IConfiguration configuration)
    {
        _configuration = configuration;
    }
    ```

4. 구성에서 값을 읽도록 `Run` 메서드를 업데이트합니다.

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

    > [!NOTE]
    > `Function1` 클래스와 `Run` 메서드는 정적이어서는 안됩니다. 자동 생성된 경우 `static` 한정자를 제거합니다.

## <a name="test-the-function-locally"></a>로컬에서 함수 테스트

1. **ConnectionString** 이라는 환경 변수를 설정하고, App Configuration 스토리지에 대한 액세스 키로 설정합니다. Windows 명령 프롬프트를 사용하는 경우 다음 명령을 실행하고, 명령 프롬프트를 다시 시작하여 변경 내용을 적용합니다.

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

2. F5를 눌러 함수를 테스트합니다. 메시지가 표시되면 Visual Studio에서 **Azure Functions Core(CLI)** 도구를 다운로드하여 설치하도록 요구하는 요청을 수락합니다. 또한 도구에서 HTTP 요청을 처리할 수 있도록 방화벽 예외를 사용하도록 설정해야 할 수도 있습니다.

3. Azure Functions 런타임 출력에서 함수의 URL을 복사합니다.

    ![VS에서 빠른 시작 함수 디버깅](./media/quickstarts/function-visual-studio-debugging.png)

4. 브라우저의 주소 표시줄에 HTTP 요청에 대한 URL을 붙여 넣습니다. 다음 이미지에서는 함수에서 반환된 로컬 GET 요청에 대한 브라우저의 응답을 보여 줍니다.

    ![빠른 시작 함수 로컬 시작](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 App Configuration 스토리지를 만들고, [App Configuration 공급자](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration)를 통해 Azure Functions 앱에서 사용했습니다. 구성을 동적으로 새로 고치도록 Azure Functions 앱을 업데이트하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Functions에서 동적 구성을 사용하도록 설정](./enable-dynamic-configuration-azure-functions-csharp.md)
