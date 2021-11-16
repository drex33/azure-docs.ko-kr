---
title: .NET Framework 지원 Azure App Configuration용 빠른 시작 | Microsoft Docs
description: 이 빠른 시작에서는 Azure App Configuration을 사용하여 .NET Framework 앱을 만들어 코드와 별도로 애플리케이션 설정의 스토리지 및 관리를 중앙 집중화합니다.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: f5bd0809de5aa7e7e585662ee0c98feaf9be2e79
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245098"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>빠른 시작: Azure App Configuration을 사용하여 .NET Framework 앱 만들기

Azure App Configuration을 .NET Framework 기반 앱에 통합하는 방법에는 두 가지가 있습니다.
- App Configuration용 구성 작성기를 사용하면 App Configuration의 데이터를 앱 설정에 로드할 수 있습니다. 앱은 항상 `ConfigurationManager`를 통해 구성에 액세스합니다. *app.config* 또는 *web.config* 파일에 대한 업데이트 이외의 코드 변경은 필요가 없습니다. 이 빠른 시작에서는 이 옵션에 대해 설명합니다.
- .NET Framework에서 설계한 대로 앱 설정은 애플리케이션을 다시 시작할 때만 새로 고칠 수 있습니다. App Configuration .NET 공급자는 .NET Standard 라이브러리입니다. 애플리케이션을 다시 시작하지 않고 동적으로 구성을 캐싱 및 새로 고치도록 지원합니다. 동적 구성이 필수이고 코드를 변경하려는 경우 [.NET Framework 콘솔 앱](./enable-dynamic-configuration-dotnet.md) 또는 [ASP.NET 웹앱](./enable-dynamic-configuration-aspnet-netfx.md)에서 동적 구성 업데이트를 구현하는 방법에 대한 자습서를 참조하세요.

이 빠른 시작에서는 .NET Framework 콘솔 앱이 예제로 사용되지만 ASP.NET Web Forms/MVC 앱에도 동일한 기술이 적용됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/dotnet)
- [Visual Studio](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2 이상](https://dotnet.microsoft.com/download/dotnet-framework)

## <a name="create-an-app-configuration-store"></a>App Configuration 저장소 만들기

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. **구성 탐색기** >  **+ 만들기** > **키-값** 을 차례로 선택하여 다음 키-값을 추가합니다.

    | 키                        | 값                               |
    |----------------------------|-------------------------------------|
    | *TestApp:Settings:Message* | *Azure App Configuration의 정보* |

    **레이블** 및 **콘텐츠 형식** 을 비워 둡니다.

## <a name="create-a-net-framework-console-app"></a>.NET Framework 콘솔 앱 만들기

1. Visual Studio를 시작하고 **새 프로젝트 만들기** 를 선택합니다.

1. **새 프로젝트 만들기** 에서 **콘솔** 프로젝트 형식을 필터링하고 프로젝트 템플릿 목록에서 C#이 있는 **콘솔 앱(.NET Framework)** 을 선택합니다. **다음** 을 누릅니다.

1. **새 프로젝트 구성** 에서 프로젝트 이름을 입력합니다. **프레임워크** 아래에서 **.NET Framework 4.7.2** 이상을 선택합니다. **만들기** 를 누릅니다.

## <a name="connect-to-an-app-configuration-store"></a>App Configuration 저장소에 연결

1. 마우스 오른쪽 단추로 프로젝트를 클릭하고, **NuGet 패키지 관리** 를 선택합니다. **찾아보기** 탭에서 다음 NuGet 패키지를 검색하여 프로젝트에 추가합니다.

    - *Microsoft.Extensions.Configuration.AzureAppConfiguration* 버전 1.0.0 이상
    - *Microsoft.Configuration.ConfigurationBuilders.Environment* 버전 2.0.0 이상
    - *System.Configuration.ConfigurationManager* 버전 4.6.0 이상

1. 프로젝트의 *App.config* 파일을 다음과 같이 업데이트합니다.

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   App Configuration 저장소의 연결 문자열은 `ConnectionString` 환경 변수에서 읽습니다. `appSettings` 섹션의 `configBuilders` 속성에 있는 `MyConfigStore` 앞에 `Environment` 구성 작성기를 추가합니다.

1. *Program.cs* 를 열고, `ConfigurationManager`를 호출하여 App Configuration을 사용하도록 `Main` 메서드를 업데이트합니다.

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
        Console.ReadKey();
    }
    ```

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

1. **ConnectionString** 이라는 환경 변수를 App Configuration 저장소 생성 중에 획득한 읽기 전용 키 연결 문자열로 설정합니다. 

    Windows 명령 프롬프트를 사용하는 경우 다음 명령을 실행합니다.
    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell을 사용하는 경우 다음 명령을 실행합니다.
    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. Visual Studio를 다시 시작하여 변경 내용을 적용합니다. 

1. Ctrl+F5를 눌러 콘솔 앱을 빌드하고 실행합니다. 콘솔에 App Configuration 출력의 메시지가 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 App Configuration 저장소를 만들고, .NET Framework 콘솔 앱에 사용했습니다. .NET Framework 앱에서 구성 설정을 동적으로 새로 고치도록 설정하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [.NET Framework 앱에서 동적 구성 사용](./enable-dynamic-configuration-dotnet.md)

> [!div class="nextstepaction"]
> [ASP.NET 웹앱에서 동적 구성 사용](./enable-dynamic-configuration-aspnet-netfx.md)
