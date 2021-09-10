---
title: '자습서: .NET Core 앱에서 푸시 새로 고침을 사용하여 동적 구성 사용'
titleSuffix: Azure App Configuration
description: 이 자습서에서는 푸시 새로 고침을 사용하여 .NET Core 앱의 구성 데이터를 동적으로 업데이트하는 방법을 알아봅니다.
services: azure-app-configuration
documentationcenter: ''
author: GrantMeStrength
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/25/2020
ms.author: jken
ms.openlocfilehash: 4ad0f2c20c0d704b2c101f975d1b62fc7dc71aca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750343"
---
# <a name="tutorial-use-dynamic-configuration-using-push-refresh-in-a-net-core-app"></a>자습서: .NET Core 앱에서 푸시 새로 고침을 사용하여 동적 구성 사용

App Configuration .NET Core 클라이언트 라이브러리는 요청 시 애플리케이션을 다시 시작하지 않고도 구성을 업데이트할 수 있습니다. 다음 두 가지 방법 중 하나 또는 둘 모두를 사용하여 App Configuration의 변경 내용을 검색하도록 애플리케이션을 구성할 수 있습니다.

1. 폴링 모델: 폴링을 사용하여 구성 변경 내용을 검색하는 기본 동작입니다. 설정의 캐시된 값이 만료되면 `TryRefreshAsync` 또는 `RefreshAsync`에 대한 다음 호출이 서버에 요청을 보내서 구성이 변경되었는지 확인하고, 필요한 경우 업데이트된 구성을 끌어옵니다.

1. 푸시 모델: [App Configuration 이벤트](./concept-app-configuration-event.md)를 사용하여 구성 변경 내용을 검색합니다. Azure Event Grid에 키 값 변경 이벤트를 보내도록 App Configuration이 설정되면 애플리케이션은 이러한 이벤트를 사용하여 구성을 업데이트된 상태로 유지하는 데 필요한 총 요청 수를 최적화할 수 있습니다. 애플리케이션은 Event Grid에서 직접 구독하거나 [지원되는 이벤트 처리기](../event-grid/event-handlers.md)(예: 웹후크, Azure 함수 또는 Service Bus 토픽) 중 하나를 통해 구독하도록 선택할 수 있습니다.

애플리케이션은 Event Grid에서 직접 또는 웹후크를 통해 또는 이벤트를 Azure Service Bus로 전달하여 이러한 이벤트를 구독하도록 선택할 수 있습니다. Azure Service Bus SDK는 HTTP 엔드포인트가 없거나 변경 사항에 대해 이벤트 그리드를 지속적으로 폴링하지 않으려는 애플리케이션에 대해 프로세스를 간소화하는 메시지 처리기를 등록하는 API를 제공합니다.

이 자습서에서는 푸시 새로 고침을 사용하여 코드에서 동적 구성 업데이트를 구현하는 방법을 보여줍니다. 빠른 시작에 소개된 앱을 기반으로 합니다. 계속 진행하기 전에 먼저 [App Configuration을 사용하여 .NET Core 앱 만들기](./quickstart-dotnet-core-app.md)를 완료합니다.

이 자습서의 단계는 임의의 코드 편집기를 사용하여 수행할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/)는 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있는 훌륭한 옵션입니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * App Configuration에서 Service Bus 토픽으로 구성 변경 이벤트를 보내도록 구독 설정
> * App Configuration의 변경에 대한 응답으로 구성을 업데이트하도록 .NET Core 앱 설정
> * 애플리케이션의 최신 구성을 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 수행하려면 [.NET Core SDK](https://dotnet.microsoft.com/download)를 설치합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-azure-service-bus-topic-and-subscription"></a>Azure Service Bus 토픽 및 구독 설정

이 자습서에서는 Event Grid용 Service Bus 통합을 사용하여 변경 사항에 대해 App Configuration을 지속적으로 폴링하지 않으려는 애플리케이션에 대한 구성 변경 사항을 검색하는 작업을 간소화합니다. Azure Service Bus SDK는 App Configuration에서 변경 사항이 검색되면 구성을 업데이트하는 데 사용할 수 있는 메시지 처리기를 등록하는 API를 제공합니다. [빠른 시작: Azure Portal을 사용하여 Service Bus 토픽 및 구독 만들기](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)의 단계에 따라 Service Bus 네임스페이스, 토픽 및 구독을 만듭니다.

리소스를 만들었으면 다음 환경 변수를 추가합니다. 이러한 항목은 애플리케이션 코드의 구성 변경에 대한 이벤트 처리기를 등록하는 데 사용됩니다.

| Key | 값 |
|---|---|
| ServiceBusConnectionString | Service Bus 네임스페이스에 대한 연결 문자열 |
| ServiceBusTopic | Service Bus 토픽의 이름 |
| ServiceBusSubscription | Service Bus 구독의 이름 |

## <a name="set-up-event-subscription"></a>이벤트 구독 설정

1. Azure Portal에서 App Configuration 리소스를 열고 `Events` 창에서 `+ Event Subscription`을 클릭합니다.

    ![App Configuration 이벤트](./media/events-pane.png)

1. `Event Subscription` 및 `System Topic`에 대한 이름을 입력합니다.

    ![이벤트 구독 만들기](./media/create-event-subscription.png)

1. `Endpoint Type`을 `Service Bus Topic`으로 선택하고 Service Bus 토픽을 선택한 다음, `Confirm Selection`을 클릭합니다.

    ![이벤트 구독 Service Bus 엔드포인트](./media/event-subscription-servicebus-endpoint.png)

1. `Create`를 클릭하여 구독을 만듭니다.

1. `Events` 창에서 `Event Subscriptions`를 클릭하여 구독이 성공적으로 만들어졌는지 확인합니다.

    ![App Configuration 이벤트 구독](./media/event-subscription-view.png)

> [!NOTE]
> 구성 변경을 구독할 때는 하나 이상의 필터를 사용하여 애플리케이션으로 전송되는 이벤트 수를 줄일 수 있습니다. 이러한 필터는 [Event Grid 구독 필터](../event-grid/event-filtering.md) 또는 [Service Bus 구독 필터](../service-bus-messaging/topic-filters.md)로 구성할 수 있습니다. 예를 들어 구독 필터는 특정 문자열로 시작하는 키에 대한 변경의 이벤트만 구독하는 데 사용할 수 있습니다.

## <a name="register-event-handler-to-reload-data-from-app-configuration"></a>App Configuration에서 데이터를 다시 로드하는 이벤트 처리기 등록

*Program.cs* 를 열고 이 파일을 다음 코드로 업데이트합니다.

```csharp
using Microsoft.Azure.ServiceBus;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
using System;
using System.Diagnostics;
using System.Text;
using System.Text.Json;
using System.Threading.Tasks;

namespace TestConsole
{
    class Program
    {
        private const string AppConfigurationConnectionStringEnvVarName = "AppConfigurationConnectionString"; // e.g. Endpoint=https://{store_name}.azconfig.io;Id={id};Secret={secret}
        private const string ServiceBusConnectionStringEnvVarName = "ServiceBusConnectionString"; // e.g. Endpoint=sb://{service_bus_name}.servicebus.windows.net/;SharedAccessKeyName={key_name};SharedAccessKey={key}
        private const string ServiceBusTopicEnvVarName = "ServiceBusTopic";
        private const string ServiceBusSubscriptionEnvVarName = "ServiceBusSubscription";

        private static IConfigurationRefresher _refresher = null;

        static async Task Main(string[] args)
        {
            string appConfigurationConnectionString = Environment.GetEnvironmentVariable(AppConfigurationConnectionStringEnvVarName);

            IConfiguration configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(appConfigurationConnectionString);
                    options.ConfigureRefresh(refresh =>
                        refresh
                            .Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromDays(30))  // Important: Reduce poll frequency
                    );

                    _refresher = options.GetRefresher();
                }).Build();

            RegisterRefreshEventHandler();
            var message = configuration["TestApp:Settings:Message"];
            Console.WriteLine($"Initial value: {configuration["TestApp:Settings:Message"]}");

            while (true)
            {
                await _refresher.TryRefreshAsync();

                if (configuration["TestApp:Settings:Message"] != message)
                {
                    Console.WriteLine($"New value: {configuration["TestApp:Settings:Message"]}");
                    message = configuration["TestApp:Settings:Message"];
                }
                
                await Task.Delay(TimeSpan.FromSeconds(1));
            }
        }

        private static void RegisterRefreshEventHandler()
        {
            string serviceBusConnectionString = Environment.GetEnvironmentVariable(ServiceBusConnectionStringEnvVarName);
            string serviceBusTopic = Environment.GetEnvironmentVariable(ServiceBusTopicEnvVarName);
            string serviceBusSubscription = Environment.GetEnvironmentVariable(ServiceBusSubscriptionEnvVarName);
            SubscriptionClient serviceBusClient = new SubscriptionClient(serviceBusConnectionString, serviceBusTopic, serviceBusSubscription);

            serviceBusClient.RegisterMessageHandler(
                handler: (message, cancellationToken) =>
               {
                   string messageText = Encoding.UTF8.GetString(message.Body);
                   JsonElement messageData = JsonDocument.Parse(messageText).RootElement.GetProperty("data");
                   string key = messageData.GetProperty("key").GetString();
                   Console.WriteLine($"Event received for Key = {key}");

                   _refresher.SetDirty();
                   return Task.CompletedTask;
               },
                exceptionReceivedHandler: (exceptionargs) =>
                {
                    Console.WriteLine($"{exceptionargs.Exception}");
                    return Task.CompletedTask;
                });
        }
    }
}
```

[SetDirty](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.iconfigurationrefresher.setdirty) 메서드는 새로 고침을 위해 등록된 키-값에 대해 캐시된 값을 더티로 설정하는 데 사용됩니다. 이렇게 하면 `RefreshAsync` 또는 `TryRefreshAsync`에 대한 다음 호출이 App Configuration을 사용하여 캐시된 값의 유효성을 다시 검사하고 필요하면 업데이트합니다.

여러 인스턴스가 동시에 새로 고쳐지는 경우 잠재적인 제한을 줄이기 위해 캐시된 값이 더티로 표시되기 전에 임의 지연이 추가됩니다. 캐시된 값이 더티로 표시되기 전의 기본적인 최대 지연 시간은 30초이지만 선택적 `TimeSpan` 매개 변수를 `SetDirty` 메서드에 전달하여 재정의할 수 있습니다.

> [!NOTE]
> 푸시 새로 고침을 사용하는 경우 App Configuration에 대한 요청 수를 줄이려면 적절한 `cacheExpiration` 매개 변수 값을 사용하여 `SetCacheExpiration(TimeSpan cacheExpiration)`을 호출하는 것이 중요합니다. 이 방식은 끌어오기 새로 고침에 대한 캐시 만료 시간을 제어하며, 이벤트 구독 또는 Service Bus 구독에 문제가 있는 경우 안전망으로 사용할 수 있습니다. 권장 값은 `TimeSpan.FromDays(30)`입니다.

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

1. **AppConfigurationConnectionString** 이라는 환경 변수를 설정하고, App Configuration 저장소에 대한 액세스 키로 설정합니다. Windows 명령 프롬프트를 사용하는 경우 다음 명령을 실행하고, 명령 프롬프트를 다시 시작하여 변경 내용을 적용합니다.

    ```console
     setx AppConfigurationConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell을 사용하는 경우 다음 명령을 실행합니다.

    ```powershell
     $Env:AppConfigurationConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    macOS 또는 Linux를 사용하는 경우 다음 명령을 실행합니다.

    ```console
     export AppConfigurationConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. 다음 명령을 실행하여 콘솔 앱을 빌드합니다.

    ```console
     dotnet build
    ```

1. 빌드가 성공적으로 완료되면 다음 명령을 실행하여 앱을 로컬로 실행합니다.

    ```console
     dotnet run
    ```

    ![업데이트 전 푸시 새로 고침 실행](./media/dotnet-core-app-pushrefresh-initial.png)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **모든 리소스** 를 선택하고, 빠른 시작에서 만든 App Configuration 저장소 인스턴스를 선택합니다.

1. **구성 탐색기** 를 선택하고, 다음 키의 값을 업데이트합니다.

    | 키 | 값 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration의 데이터 - 업데이트됨 |

1. 이벤트가 처리되고 구성이 업데이트되도록 30초 동안 기다립니다.

    ![업데이트 후 푸시 새로 고침 실행](./media/dotnet-core-app-pushrefresh-final.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 .NET Core 앱을 사용하도록 설정하여 App Configuration에서 구성 설정을 동적으로 새로 고칩니다. Azure 관리 ID를 사용하여 App Configuration에 대한 액세스를 간소화하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [관리 ID 통합](./howto-integrate-azure-managed-service-identity.md)
