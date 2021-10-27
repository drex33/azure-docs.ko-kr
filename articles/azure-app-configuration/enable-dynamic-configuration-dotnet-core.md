---
title: '자습서: .NET Core 앱에서 동적 구성 사용'
titleSuffix: Azure App Configuration
description: 이 자습서에서는 .NET Core 앱의 구성 데이터를 동적으로 업데이트하는 방법을 알아봅니다.
services: azure-app-configuration
documentationcenter: ''
author: GrantMeStrength
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: jken
ms.openlocfilehash: 9a2a8f88cd39b452a54d6fc668737fc1b5be68cc
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996970"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>자습서: .NET Core 앱에서 동적 구성 사용

App Configuration .NET 공급자 라이브러리는 요청 시 애플리케이션을 다시 시작하지 않고도 구성을 업데이트할 수 있습니다. 이 자습서에서는 코드에서 동적 구성 업데이트를 구현하는 방법을 보여줍니다. 빠른 시작에 소개된 앱을 기반으로 합니다. 계속 진행하기 전에 [App Configuration을 사용하여 .NET Core 앱 만들기](./quickstart-dotnet-core-app.md)를 완료해야 합니다.

이 자습서의 단계는 임의의 코드 편집기를 사용하여 수행할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/)는 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있는 훌륭한 옵션입니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * App Configuration 저장소의 변경에 따라 .NET Core 앱의 해당 구성을 업데이트하도록 설정합니다.
> * 애플리케이션의 최신 구성을 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

빠른 시작 [App Configuration을 사용하여 .NET Core 앱 만들기](./quickstart-dotnet-core-app.md)를 완료합니다.

## <a name="activity-driven-configuration-refresh"></a>활동 기반 구성 새로 고침

*Program.cs* 를 열고 다음과 같이 코드를 업데이트합니다.

```csharp
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
using System;
using System.Threading.Tasks;

namespace TestConsole
{
    class Program
    {
        private static IConfiguration _configuration = null;
        private static IConfigurationRefresher _refresher = null;

        static void Main(string[] args)
        {
            var builder = new ConfigurationBuilder();
            builder.AddAzureAppConfiguration(options =>
            {
                options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
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

        private static async Task PrintMessage()
        {
            Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

            // Wait for the user to press Enter
            Console.ReadLine();

            await _refresher.TryRefreshAsync();
            Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
        }
    }
}
```

`ConfigureRefresh` 메서드에서 App Configuration 저장소 내의 키는 변경 모니터링을 위해 등록됩니다. `Register` 메서드에는 등록된 키가 변경되는 경우 모든 구성 값을 새로 고칠지 여부를 나타내는 데 사용할 수 있는 선택적 부울 매개 변수 `refreshAll`이 있습니다. 이 예제에서는 *TestApp:Settings:Message* 키만 새로 고쳐집니다. `SetCacheExpiration` 메서드는 구성 변경 여부를 확인하기 위해 App Configuration에 새 요청을 하기 전에 경과해야 하는 최소 시간을 지정합니다. 이 예제에서는 기본 만료 시간인 30초를 재정의하여 데모용으로 대신 10초를 지정합니다.

`ConfigureRefresh` 메서드만 호출하면 구성이 자동으로 새로 고쳐지지 않습니다. 인터페이스 `IConfigurationRefresher`에서 `TryRefreshAsync` 메서드를 호출하여 새로 고침을 트리거합니다. 이 설계는 애플리케이션이 유휴 상태인 경우에도 App Configuration으로 전송되는 가상 요청을 방지하기 위한 것입니다. 애플리케이션이 활성 상태인 것으로 간주되는 `TryRefreshAsync` 호출을 포함하는 것이 좋습니다. 예를 들어 들어오는 메시지, 주문 또는 복잡한 작업의 반복을 처리할 때 이 작업을 수행할 수 있습니다. 애플리케이션이 항상 활성 상태인 경우 타이머에 있을 수도 있습니다. 이 예제에서는 Enter 키를 누를 때마다 `TryRefreshAsync`를 호출합니다. 어떤 이유로 `TryRefreshAsync` 호출이 실패하더라도 애플리케이션은 캐시된 구성을 계속 사용합니다. 구성된 캐시 만료 시간이 경과하고 `TryRefreshAsync` 호출이 애플리케이션 작업에 의해 다시 트리거되는 경우 다른 시도가 수행됩니다. `TryRefreshAsync`를 호출하는 것은 구성된 캐시 만료 시간이 경과하기 전에 작동하지 않으므로 자주 호출하더라도 성능에 미치는 영향은 최소화됩니다.

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

1. **ConnectionString** 이라는 환경 변수를 설정하고, App Configuration 스토리지에 대한 액세스 키로 설정합니다. Windows 명령 프롬프트를 사용하는 경우 다음 명령을 실행하고, 명령 프롬프트를 다시 시작하여 변경 내용을 적용합니다.

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

1. 다음 명령을 실행하여 콘솔 앱을 빌드합니다.

    ```console
     dotnet build
    ```

1. 빌드가 성공적으로 완료되면 다음 명령을 실행하여 앱을 로컬로 실행합니다.

    ```console
     dotnet run
    ```

    ![로컬로 빠른 시작 앱 시작](./media/quickstarts/dotnet-core-app-run.png)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **모든 리소스** 를 선택하고, 빠른 시작에서 만든 App Configuration 저장소 인스턴스를 선택합니다.

1. **구성 탐색기** 를 선택하고, 다음 키의 값을 업데이트합니다.

    | 키 | 값 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration의 데이터 - 업데이트됨 |

1. Enter 키를 눌러 새로 고침을 트리거하고 명령 프롬프트 또는 PowerShell 창에서 업데이트된 값을 출력합니다.

    ![로컬로 빠른 시작 앱 새로 고침](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > 새로 고침 작업에 대한 구성을 지정하는 동안 `SetCacheExpiration` 메서드를 사용하여 캐시 만료 시간이 10초로 설정되었으므로 해당 설정에 대한 마지막 새로 고침 이후 최소 10초가 경과한 경우에만 구성 설정 값이 업데이트됩니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 .NET Core 앱을 사용하도록 설정하여 App Configuration에서 구성 설정을 동적으로 새로 고칩니다. Azure 관리 ID를 사용하여 App Configuration에 대한 액세스를 간소화하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [관리 ID 통합](./howto-integrate-azure-managed-service-identity.md)
