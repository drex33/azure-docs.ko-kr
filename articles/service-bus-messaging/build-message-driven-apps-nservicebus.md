---
title: NServiceBus 및 Azure Service Bus를 사용하여 메시지 기반 애플리케이션 빌드
description: NServiceBus 프레임워크를 사용하여 Azure Service Bus에서 분산 시스템의 복잡한 문제를 해결하는 방법을 알아봅니다.
author: kbaley
ms.author: spelluru
ms.service: service-bus-messaging
ms.topic: how-to
ms.date: 07/26/2021
ms.custom: template-how-to
ms.openlocfilehash: c9f503cb600c87e1dac590dcbbe7edf46bc7be76
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528081"
---
# <a name="build-message-driven-business-applications-with-nservicebus-and-azure-service-bus"></a>NServiceBus 및 Azure Service Bus를 사용하여 메시지 기반 비즈니스 애플리케이션 빌드
NServiceBus는 Particular Software에서 제공하는 상용 메시징 프레임워크입니다. 이는 Azure Service Bus를 기반으로 하여 구축되었으며, 개발자가 인프라 문제를 추상화하여 비즈니스 논리에 집중할 수 있도록 도와줍니다. 이 가이드에서는 두 서비스 간에 메시지를 교환하는 솔루션을 빌드합니다. 또한 실패한 메시지를 자동으로 다시 시도하고 Azure에서 이러한 서비스를 호스트하기 위한 옵션을 검토하는 방법을 보여 줍니다.

> [!NOTE]
> 이 자습서의 코드는 [Particular Software 문서 웹 사이트](https://docs.particular.net/samples/azure-service-bus-netstandard/send-receive-with-nservicebus/)에서 사용할 수 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소

샘플에서는 [Azure Service Bus 네임스페이스를 만들었다](service-bus-create-namespace-portal.md)고 가정합니다.

> [!IMPORTANT]
> NServiceBus에는 표준 계층 이상이 필요합니다. 기본 계층은 작동하지 않습니다.

## <a name="download-and-prepare-the-solution"></a>솔루션 다운로드 및 준비
1. [Particular Software 문서 웹 사이트](https://docs.particular.net/samples/azure-service-bus-netstandard/send-receive-with-nservicebus/)에서 코드를 다운로드합니다. `SendReceiveWithNservicebus.sln` 솔루션은 다음 세 가지 프로젝트로 구성되어 있습니다.

    - **발신자**: 메시지를 보내는 콘솔 애플리케이션
    - **수신기**: 발신자로부터 메시지를 받고 다시 회신하는 콘솔 애플리케이션
    - **공유**: 발신자와 수신기 간에 공유되는 메시지 계약이 포함된 클래스 라이브러리
    
    Particular Software의 시각화 및 디버깅 도구인 [ServiceInsight](https://particular.net/serviceinsight)에서 생성한 다음 다이어그램에서는 메시지 흐름을 보여 줍니다.
    
    :::image type="content" source="./media/nservicebus/sequence-diagram.png" alt-text="시퀀스 다이어그램을 보여 주는 이미지":::    
1. 즐겨찾는 코드 편집기(예: Visual Studio 2019)에서 `SendReceiveWithNservicebus.sln`을 선택합니다. 
1. 수신기와 발신자 프로젝트 모두에서 `appsettings.json`을 열고, `AzureServiceBusConnectionString`을 Azure Service Bus 네임스페이스에 대한 연결 문자열로 설정합니다.



## <a name="define-the-shared-message-contracts"></a>공유 메시지 계약 정의

공유 클래스 라이브러리에서는 메시지를 보내는 데 사용되는 계약을 정의합니다. 여기에는 메시지를 식별하는 데 사용할 수 있는 인터페이스를 포함한 `NServiceBus` NuGet 패키지에 대한 참조가 포함됩니다. 인터페이스는 필요하지 않지만, NServiceBus에서 몇 가지 추가 유효성 검사를 제공하고 코드가 자체 문서화되도록 합니다.

먼저 `Ping.cs` 클래스를 검토하겠습니다.

```csharp
public class Ping : NServiceBus.ICommand
{
    public int Round { get; set; }
}
```

`Ping` 클래스는 발신자에서 수신기에 보내는 메시지를 정의합니다. NServiceBus 패키지의 인터페이스인 `NServiceBus.ICommand`를 구현하는 간단한 C# 클래스입니다. [ 인터페이스를 사용하지 않고](https://docs.particular.net/nservicebus/messaging/conventions) 메시지를 식별할 수 있는 다른 방법이 있지만, 이 메시지는 판독기와 NServiceBus에 명령임을 알리는 신호입니다.

공유 프로젝트의 다른 메시지 클래스는 `Pong.cs`입니다.

```csharp
public class Pong : NServiceBus.IMessage
{
    public string Acknowledgement { get; set; }
}
```

`Pong`은 간단한 C# 개체이기도 하지만 `NServiceBus.IMessage`를 구현합니다. `IMessage` 인터페이스는 명령도 이벤트도 아닌 제네릭 메시지를 나타내며 일반적으로 회신에 사용됩니다. 이 샘플에서는 수신기에서 메시지를 받았음을 나타내기 위해 발신자에 다시 보내는 회신입니다.

`Ping` 및 `Pong`은 사용할 두 가지 메시지 유형입니다. 다음 단계에서는 Azure Service Bus를 사용하고 `Ping` 메시지를 보내도록 발신자를 구성합니다.

## <a name="set-up-the-sender"></a>발신자 설정

발신자는 `Ping` 메시지를 보내는 엔드포인트입니다. 여기서는 Azure Service Bus를 전송 메커니즘으로 사용하도록 발신자를 구성한 다음, `Ping` 인스턴스를 구성하고 보냅니다.

`Program.cs`의 `Main` 메서드에서 발신자 엔드포인트를 구성합니다.

```csharp
var host = Host.CreateDefaultBuilder(args)
    // Configure a host for the endpoint
    .ConfigureLogging((context, logging) =>
    {
        logging.AddConfiguration(context.Configuration.GetSection("Logging"));

        logging.AddConsole();
    })
    .UseConsoleLifetime()
    .UseNServiceBus(context =>
    {
        // Configure the NServiceBus endpoint
        var endpointConfiguration = new EndpointConfiguration("Sender");

        var transport = endpointConfiguration.UseTransport<AzureServiceBusTransport>();
        var connectionString = context.Configuration.GetConnectionString("AzureServiceBusConnectionString");
        transport.ConnectionString(connectionString);

        transport.Routing().RouteToEndpoint(typeof(Ping), "Receiver");

        endpointConfiguration.EnableInstallers();
        endpointConfiguration.AuditProcessedMessagesTo("audit");

        return endpointConfiguration;
    })
    .ConfigureServices(services => services.AddHostedService<SenderWorker>())
    .Build();

await host.RunAsync();
```

여기서 압축을 풀어야 할 것이 많으므로 단계별로 검토하겠습니다.

### <a name="configure-a-host-for-the-endpoint"></a>엔드포인트에 대한 호스트 구성

호스팅 및 로깅은 표준 [Microsoft 제네릭 호스트 옵션](/dotnet/core/extensions/generic-host)을 사용하여 구성됩니다. 지금은 엔드포인트가 콘솔 애플리케이션으로 실행되도록 구성되어 있지만 최소한으로 변경하여 Azure Functions에서 실행되도록 수정할 수 있습니다. 이에 대해서는 이 문서의 뒷부분에서 설명합니다.

### <a name="configure-the-nservicebus-endpoint"></a>NServiceBus 엔드포인트 구성

다음으로, `.UseNServiceBus(…)` 확장 메서드를 통해 NServiceBus를 사용하도록 호스트에 지시합니다. 이 메서드는 호스트가 실행될 때 시작할 엔드포인트를 반환하는 콜백 함수를 사용합니다.

엔드포인트 구성에서 전송에 대해 `AzureServiceBus`를 지정하고 `appsettings.json`의 연결 문자열을 제공합니다. 그런 다음, `Ping` 형식의 메시지를 "Receiver"라는 엔드포인트로 보내도록 라우팅을 설정합니다. 이렇게 하면 NServiceBus에서 수신기의 주소를 요구하지 않고 메시지를 대상으로 발송하는 프로세스를 자동화할 수 있습니다.

`EnableInstallers`에 대한 호출은 엔드포인트가 시작될 때 Azure Service Bus 네임스페이스에서 토폴로지를 설정하고, 필요한 경우 필요한 큐를 만듭니다. 프로덕션 환경에서 [작업 스크립팅](https://docs.particular.net/transports/azure-service-bus/operational-scripting)은 토폴로지를 만드는 또 다른 옵션입니다.

### <a name="set-up-background-service-to-send-messages"></a>메시지를 보내도록 백그라운드 서비스 설정

발신자의 마지막 부분은 `Ping` 메시지를 1초마다 보내도록 구성된 백그라운드 서비스인 `SenderWorker`입니다.

```csharp
public class SenderWorker : BackgroundService
{
    private readonly IMessageSession messageSession;
    private readonly ILogger<SenderWorker> logger;

    public SenderWorker(IMessageSession messageSession, ILogger<SenderWorker> logger)
    {
        this.messageSession = messageSession;
        this.logger = logger;
    }

    protected override async Task ExecuteAsync(CancellationToken stoppingToken)
    {
        try
        {
            var round = 0;
            while (!stoppingToken.IsCancellationRequested)
            {
                await messageSession.Send(new Ping { Round = round++ })
                    .ConfigureAwait(false);

                logger.LogInformation($"Message #{round}");

                await Task.Delay(1_000, stoppingToken)
                    .ConfigureAwait(false);
            }
        }
        catch (OperationCanceledException)
        {
            // graceful shutdown
        }
    }
}
```

`ExecuteAsync`에서 사용되는 `IMessageSession`은 `SenderWorker`에 삽입되며, 메시지 처리기 외부에서 NServiceBus를 사용하여 메시지를 보낼 수 있도록 합니다. `Sender`에서 구성한 라우팅은 `Ping` 메시지의 대상을 지정합니다. 이는 시스템의 토폴로지(어떤 메시지가 어떤 주소로 라우팅됨)를 비즈니스 코드와는 별도의 관심 항목으로 유지합니다.

발신자 애플리케이션에는 `PongHandler`도 포함됩니다. 다음에 나오는 수신기에 대해 설명한 후에 여기로 다시 돌아오겠습니다.

## <a name="set-up-the-receiver"></a>수신기 설정

수신기는 `Ping` 메시지를 수신 대기하고, 메시지를 받으면 로그를 기록하고, 발신자에 다시 회신하는 엔드포인트입니다. 이 섹션에서는 발신자와 비슷한 엔드포인트 구성을 빠르게 검토한 다음, 메시지 처리기로 주의를 돌립니다.

발신자와 마찬가지로 Microsoft 제네릭 호스트를 사용하여 수신기를 콘솔 애플리케이션으로 설정합니다. 동일한 로깅 및 엔드포인트 구성(Azure Service Bus를 메시지 전송으로 사용)을 사용하지만 발신자와 구별되는 다른 이름을 사용합니다.

```csharp
var endpointConfiguration = new EndpointConfiguration("Receiver");
```

이 엔드포인트는 발신자에만 회신하고 새 대화를 시작하지 않으므로 라우팅 구성이 필요하지 않습니다. 또한 메시지를 받을 때만 회신하므로 발신자처럼 백그라운드 작업자가 필요하지 않습니다.

### <a name="the-ping-message-handler"></a>Ping 메시지 처리기

수신기 프로젝트에는 `PingHandler`라는 _메시지 처리기_ 가 포함되어 있습니다.

```csharp
public class PingHandler : NServiceBus.IHandleMessages<Ping>
{
    private readonly ILogger<PingHandler> logger;

    public PingHandler(ILogger<PingHandler> logger)
    {
        this.logger = logger;
    }

    public async Task Handle(Ping message, IMessageHandlerContext context)
    {
        logger.LogInformation($"Processing Ping message #{message.Round}");

        // throw new Exception("BOOM");

        var reply = new Pong { Acknowledgement = $"Ping #{message.Round} processed at {DateTimeOffset.UtcNow:s}" };

        await context.Reply(reply);
    }
}
```

지금은 주석 처리된 코드를 무시하겠습니다. 나중에 오류로부터 복구하는 방법에 대해 설명할 때 여기로 다시 돌아오겠습니다.

클래스는 하나의 `Handle` 메서드를 정의하는 `IHandleMessages<Ping>`을 구현합니다. 이 인터페이스는 엔드포인트에서 `Ping` 형식의 메시지를 받을 때 이 처리기의 `Handle` 메서드에서 처리해야 한다고 NServiceBus에 알려줍니다. `Handle` 메서드는 메시지 자체를 매개 변수로 사용하고, 회신, 명령 보내기 또는 이벤트 게시와 같은 추가 메시징 작업을 허용하는 `IMessageHandlerContext`를 사용합니다.

`PingHandler`는 간단합니다. `Ping` 메시지를 받으면 메시지 세부 정보를 기록하고 새 `Pong` 메시지를 사용하여 발신자에 다시 회신합니다.

> [!NOTE]
> 발신자의 구성에서 `Ping` 메시지를 수신기로 라우팅하도록 지정했습니다. NServiceBus는 특히 메시지의 원본을 나타내는 메타데이터를 메시지에 추가합니다. 이에 따라 `Pong` 회신 메시지에 대한 라우팅 데이터를 지정할 필요가 없습니다. 이는 자동으로 해당 원본인 발신자로 다시 라우팅됩니다.
>

발신자와 수신기가 모두 올바르게 구성되었으면 이제 솔루션을 실행할 수 있습니다.

## <a name="run-the-solution"></a>솔루션 실행

솔루션을 시작하려면 발신자와 수신기를 모두 실행해야 합니다. Visual Studio Code를 사용하는 경우 "모두 디버그" 구성을 시작합니다. Visual Studio를 사용하는 경우 발신자 및 수신기 프로젝트를 모두 시작하도록 솔루션을 구성합니다.

1. 솔루션 탐색기에서 마우스 오른쪽 단추로 솔루션을 클릭합니다.
1. "시작 프로젝트 설정..."을 선택합니다.
1. **여러 시작 프로젝트** 를 선택합니다.
1. 발신자와 수신기 모두에 대해 드롭다운 목록에서 "시작"을 선택합니다.

솔루션을 시작합니다. 두 개의 콘솔 애플리케이션이 표시됩니다. 하나는 발신자용이고 다른 하나는 수신기용입니다.

# <a name="sender"></a>[보낸 사람](#tab/Sender)

:::image type="content" source="./media/nservicebus/sender.png" alt-text="Ping 메시지를 수신기에 보내는 엔드포인트를 보여 주는 이미지":::

# <a name="receiver"></a>[받는 사람](#tab/Receiver)

:::image type="content" source="./media/nservicebus/receiver.png" alt-text="발신자의 Ping 메시지를 받는 엔드포인트를 보여 주는 이미지":::

---

발신자에서 `SenderWorker` 백그라운드 작업을 통해 `Ping` 메시지가 1초마다 발송됩니다. 수신기는 받은 각 `Ping` 메시지의 세부 정보를 표시하고, 발신자는 회신으로 받은 각 `Pong` 메시지의 세부 정보를 기록합니다.

이제 모든 것이 제대로 작동하도록 했으므로 자세히 살펴보겠습니다.

## <a name="resilience-in-action"></a>복원력 실제 동작

오류는 소프트웨어 시스템에서 피할 수 없는 현실입니다. 코드가 실패하는 것은 피할 수 없으며, 네트워크 오류, 데이터베이스 잠금, 타사 API 변경 및 오래된 일반 코딩 오류와 같은 다양한 이유로 실패할 수 있습니다.

NServiceBus에는 오류를 처리하기 위한 강력한 복구 기능이 있습니다. 메시지 처리기가 실패하면 미리 정의된 정책에 따라 메시지가 자동으로 다시 시도됩니다. 재시도 정책에는 즉시 다시 시도 횟수 및 지연된 다시 시도 횟수라는 두 가지 유형이 있습니다. 작동 방식을 설명하는 가장 좋은 방법은 작동 방식을 실제로 확인하는 것입니다. 재시도 정책을 수신기 엔드포인트에 추가해 보겠습니다.

1. 발신자 프로젝트에서 `Program.cs`를 엽니다.
1. 다음 코드를 `.EnableInstallers` 줄 뒤에 추가합니다.

```csharp
endpointConfiguration.SendFailedMessagesTo("error");
var recoverability = endpointConfiguration.Recoverability();
recoverability.Immediate(
    immediate =>
    {
        immediate.NumberOfRetries(3);
    });
recoverability.Delayed(
    delayed =>
    {
        delayed.NumberOfRetries(2);
        delayed.TimeIncrease(TimeSpan.FromSeconds(5));
    });
```

이 정책의 작동 방식을 설명하기 전에 실제 동작을 살펴보겠습니다. 복구 정책을 테스트하기 전에 오류를 시뮬레이션해야 합니다. 수신기 프로젝트에서 `PingHandler` 코드를 열고, 다음 줄의 주석 처리를 제거합니다.

```csharp
throw new Exception("BOOM");
```

이제 수신기에서 `Ping` 메시지를 처리하면 실패합니다. 솔루션을 다시 시작하고, 수신기에서 발생하는 상황을 살펴보겠습니다. 

신뢰성이 낮은 `PingHandler`를 사용하면 모든 메시지가 실패합니다. 해당 메시지에 대한 재시도 정책이 시작되는 것을 볼 수 있습니다. 메시지가 처음 실패하면 최대 3회까지 즉시 다시 시도됩니다.

:::image type="content" source="./media/nservicebus/immediate-retries.png" alt-text="최대 3회까지 메시지를 다시 시도하는 즉시 재시도 정책을 보여 주는 이미지":::

3회의 즉시 다시 시도 횟수가 모두 사용되고 지연된 재시도 정책이 시작되고 메시지가 5초 동안 지연되는 경우에도 당연히 계속 실패합니다.

:::image type="content" source="./media/nservicebus/delayed-retries.png" alt-text="다른 차례의 즉시 다시 시도를 시도하기 전에 메시지를 5초 단위로 지연시키는 지연된 재시도 정책을 보여 주는 이미지":::

 해당 5초가 지나면 메시지에 대한 또 다른 3회(즉, 즉시 재시도 정책의 또 다른 반복)가 다시 시도됩니다. 이러한 시도도 실패하고, NServiceBus에서 다시 시도하기 전에 이번에는 메시지를 10초 동안 다시 지연시킵니다.

전체 재시도 정책이 실행된 후에도 `PingHandler`가 여전히 성공하지 못하면 메시지가 `SendFailedMessagesTo`에 대한 호출에서 정의한 대로 `error`라는 _중앙 집중식_ 오류 큐에 배치됩니다. 

:::image type="content" source="./media/nservicebus/failed-message.png" alt-text="실패한 메시지를 보여 주는 이미지":::

중앙 집중식 오류 큐의 개념은 각 처리 큐에 대해 배달 못한 편지 큐가 있는 Azure Service Bus의 배달 못한 편지 메커니즘과 다릅니다. NServiceBus를 사용하면 Azure Service Bus의 배달 못한 편지 큐가 진정한 포이즌 메시지 큐로 작동하지만, 중앙 집중식 오류 큐에 있는 메시지는 나중에 필요한 경우 다시 처리할 수 있습니다.

재시도 정책은 종종 현실적으로 일시적이거나 약간 일시적인 [몇 가지 유형의 오류](https://particular.net/blog/but-all-my-errors-are-severe)를 해결하는 데 도움이 됩니다. 즉, 일시적이며, 짧은 지연 후에 메시지가 간단히 다시 처리되는 경우 종종 사라지는 오류입니다. 예를 들어 네트워크 오류, 데이터베이스 잠금 및 타사 API 중단이 있습니다.

메시지가 오류 큐에 있으면 선택한 도구에서 메시지 세부 정보를 검사한 다음, 이를 통해 수행할 작업을 결정할 수 있습니다. 예를 들어 Particular Software의 모니터링 도구인 [ServicePulse](https://particular.net/servicepulse)를 사용하면 다음과 같은 메시지 세부 정보와 실패 이유를 볼 수 있습니다.

:::image type="content" source="./media/nservicebus/servicepulse.png" alt-text="Particular Software의 ServicePulse를 보여 주는 이미지":::

세부 정보가 검토되면 메시지를 원래 큐로 다시 보내 처리할 수 있습니다. 그렇게 하기 전에 메시지를 편집할 수도 있습니다. 동일한 이유로 실패한 오류 큐에 여러 메시지가 있는 경우 모두 원래 대상으로 일괄적으로 다시 보낼 수 있습니다.

다음으로, Azure에서 솔루션을 배포할 위치를 파악해야 합니다.

## <a name="where-to-host-the-services-in-azure"></a>Azure에서 서비스를 호스트할 위치

이 샘플에서는 발신자 및 수신기 엔드포인트가 콘솔 애플리케이션으로 실행되도록 구성되어 있습니다. 또한 이러한 엔드포인트는 Azure Functions, Azure App Services, Azure Container Instances, Azure Kubernetes Services 및 Azure VM을 포함한 다양한 Azure 서비스에서 호스트할 수 있습니다. 예를 들어 발신기 엔드포인트가 Azure Function으로 실행되도록 구성하는 방법은 다음과 같습니다.

```csharp
[assembly: FunctionsStartup(typeof(Startup))]
[assembly: NServiceBusEndpointName("Sender")]

public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.UseNServiceBus(() =>
        {
            var configuration = new ServiceBusTriggeredEndpointConfiguration("Sender");
            var transport = configuration.AdvancedConfiguration.Transport;
            transport.Routing().RouteToEndpoint(typeof(Ping), "Receiver");

            return configuration;
        });
    }
}
```

Functions에서 NServiceBus를 사용하는 방법에 대한 자세한 내용은 NServiceBus 설명서의 [Azure Service Bus를 사용하는 Azure Functions](https://docs.particular.net/nservicebus/hosting/azure-functions/service-bus)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure 서비스에서 NServiceBus를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Service Bus 보내기/회신 샘플](https://docs.particular.net/samples/azure-service-bus-netstandard/send-reply/)
- [Azure Functions에서 NServiceBus 사용](https://docs.particular.net/nservicebus/hosting/azure-functions/service-bus)
- [NServiceBus의 Azure Service Bus 전송](https://docs.particular.net/transports/azure-service-bus/)
- [NServiceBus 및 Azure](https://docs.particular.net/nservicebus/azure/)
- [NServiceBus](https://particular.net/nservicebus)
- [NServiceBus 빠른 시작 자습서](https://docs.particular.net/tutorials/quickstart)
