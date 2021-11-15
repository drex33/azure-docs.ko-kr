---
title: '.NET Framework 자습서: Azure App Configuration의 동적 구성'
description: 이 자습서에서는 Azure App Configuration을 사용하여 .NET Framework 앱의 구성 데이터를 동적으로 업데이트하는 방법을 알아봅니다.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/24/2020
ms.author: alkemper
ms.openlocfilehash: d6c3bc0ee45c214419820208598e5721309ea144
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261047"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>자습서: .NET Framework에서 동적 구성 사용

App Configuration의 데이터는 .NET Framework 앱에서 앱 설정으로 로드할 수 있습니다. 자세한 내용은 [빠른 시작](./quickstart-dotnet-app.md)을 참조하세요. 그러나 .NET Framework에서 설계한 대로 앱 설정은 앱을 다시 시작할 때만 새로 고칠 수 있습니다. App Configuration .NET 공급자는 .NET Standard 라이브러리입니다. 앱을 다시 시작하지 않고 동적으로 구성을 캐싱 및 새로 고치도록 지원합니다. 이 자습서에서는 .NET Framework 콘솔 앱에서 동적 구성 업데이트를 구현하는 방법을 보여 줍니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * App Configuration 저장소의 변경에 따라 .NET Framework 앱의 해당 구성을 업데이트하도록 설정합니다.
> * 애플리케이션에 최신 구성을 삽입합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
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

## <a name="reload-data-from-app-configuration"></a>App Configuration에서 데이터 다시 로드
1. 마우스 오른쪽 단추로 프로젝트를 클릭하고, **NuGet 패키지 관리** 를 선택합니다. **찾아보기** 탭에서 다음 NuGet 패키지의 최신 버전을 검색한 후 프로젝트에 추가합니다.

   *Microsoft.Extensions.Configuration.AzureAppConfiguration*

1. *Program.cs* 를 열고, 다음 네임스페이스를 추가합니다.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. 구성 관련 개체를 저장하는 두 개의 변수를 추가합니다.

    ```csharp
    private static IConfiguration _configuration;
    private static IConfigurationRefresher _refresher;
    ```

1. 지정된 새로 고침 옵션을 사용하여 App Configuration에 연결하도록 `Main` 메서드를 업데이트합니다.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    // Load all keys that start with `TestApp:`.
                    .Select("TestApp:*")
                    // Configure to reload the key 'TestApp:Settings:Message' if it is modified.
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                               .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });

            _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }
    ```

    `ConfigureRefresh` 메서드에서 App Configuration 저장소 내의 키는 변경 모니터링을 위해 등록됩니다. `Register` 메서드에는 등록된 키가 변경되는 경우 모든 구성 값을 새로 고칠지 여부를 나타내는 데 사용할 수 있는 선택적 부울 매개 변수 `refreshAll`이 있습니다. 이 예제에서는 *TestApp:Settings:Message* 키만 새로 고쳐집니다. `SetCacheExpiration` 메서드는 구성 변경 여부를 확인하기 위해 App Configuration에 새 요청을 하기 전에 경과해야 하는 최소 시간을 지정합니다. 이 예제에서는 기본 만료 시간인 30초를 재정의하여 데모용으로 대신 10초를 지정합니다.

1. App Configuration에서 구성 데이터의 새로 고침을 트리거하는 `PrintMessage()`라는 메서드를 추가합니다.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.TryRefreshAsync();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

    `ConfigureRefresh` 메서드만 호출하면 구성이 자동으로 새로 고쳐지지 않습니다. 인터페이스 `IConfigurationRefresher`에서 `TryRefreshAsync` 메서드를 호출하여 새로 고침을 트리거합니다. 이 설계는 애플리케이션이 유휴 상태인 경우에도 App Configuration으로 전송되는 가상 요청을 방지하기 위한 것입니다. 애플리케이션이 활성 상태인 것으로 간주되는 `TryRefreshAsync` 호출을 포함할 수 있습니다. 예를 들어 들어오는 메시지, 주문 또는 복잡한 작업의 반복을 처리할 때 이 작업을 수행할 수 있습니다. 애플리케이션이 항상 활성 상태인 경우 타이머에 있을 수도 있습니다. 이 예제에서는 Enter 키를 누를 때 `TryRefreshAsync`를 호출합니다. 어떤 이유로 `TryRefreshAsync` 호출이 실패하더라도 애플리케이션은 캐시된 구성을 계속 사용합니다. 구성된 캐시 만료 시간이 경과하고 `TryRefreshAsync` 호출이 애플리케이션 작업에 의해 다시 트리거되는 경우 다른 시도가 수행됩니다. `TryRefreshAsync`를 호출하는 것은 구성된 캐시 만료 시간이 경과하기 전에 작동하지 않으므로 자주 호출하더라도 성능에 미치는 영향은 최소화됩니다.

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

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

1. Ctrl+F5를 눌러 콘솔 앱을 빌드하고 실행합니다.

    ![로컬로 앱 시작](./media/dotnet-app-run.png)

1. Azure Portal에서 App Configuration 저장소의 **구성 탐색기** 로 이동하여 다음 키의 값을 업데이트합니다.

    | 키                        | 값                                         |
    |----------------------------|-----------------------------------------------|
    | *TestApp:Settings:Message* | *Azure App Configuration의 데이터 - 업데이트됨* |

1. 실행 중인 애플리케이션으로 돌아가서 Enter 키를 눌러 새로 고침을 트리거하고 명령 프롬프트 또는 PowerShell 창에서 업데이트된 값을 출력합니다.

    ![로컬로 앱 새로 고침](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > 새로 고침 작업에 대한 구성을 지정하는 동안 `SetCacheExpiration` 메서드를 사용하여 캐시 만료 시간이 10초로 설정되었으므로 해당 설정에 대한 마지막 새로 고침 이후 최소 10초가 경과한 경우에만 구성 설정 값이 업데이트됩니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 .NET Framework 앱을 사용하도록 설정하여 App Configuration에서 구성 설정을 동적으로 새로 고칩니다. ASP.NET 웹 애플리케이션(.NET Framework)에서 동적 구성을 사용하도록 설정하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [ASP.NET 웹 애플리케이션에서 동적 구성을 사용하도록 설정](./enable-dynamic-configuration-aspnet-netfx.md)

Azure 관리 ID를 사용하여 App Configuration에 대한 액세스를 간소화하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [관리 ID 통합](./howto-integrate-azure-managed-service-identity.md)
