---
title: Azure Application Insights의 원격 분석 채널 | Microsoft Docs
description: .NET 및 .NET Core 용 Azure Application Insights SDK에서 원격 분석 채널을 사용자 지정하는 방법입니다.
ms.topic: conceptual
ms.date: 05/14/2019
ms.custom: devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: 2bba7516637bedf6e81747cbb27f926964c63f53
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128556512"
---
# <a name="telemetry-channels-in-application-insights"></a>Application Insights의 원격 분석 채널

원격 분석 채널은 [Azure Application Insights SDK](./app-insights-overview.md)의 필수적인 부분입니다. 이것은 Application Insights 서비스에 대한 원격 분석의 버퍼링 및 전송을 관리합니다. SDK의 .NET 및 .NET Core 버전에는 `InMemoryChannel`과 `ServerTelemetryChannel`의 두 가지 기본 제공 원격 분석 채널이 있습니다. 이 문서에서는 채널 동작을 사용자 지정하는 방법을 비롯하여 각 채널을 자세히 설명합니다.

## <a name="what-are-telemetry-channels"></a>원격 분석 채널이란?

원격 분석 채널은 원격 분석 항목을 버퍼링하고, 이를 Application Insights 서비스로 전송해서 쿼리 및 분석을 위해 저장합니다. 원격 분석 채널은 [`Microsoft.ApplicationInsights.ITelemetryChannel`](/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel)인터페이스를 구현하는 클래스입니다.

원격 분석 채널의 `Send(ITelemetry item)` 메서드는 모든 원격 분석 이니셜라이저 및 원격 분석 프로세서가 호출된 후에 호출됩니다. 따라서 원격 분석 프로세서에 의해 삭제된 항목들은 채널에 도달하지 않습니다. `Send()`는 보통 백 엔드에 항목들을 즉시 보내지 않습니다. 일반적으로 그것은 메모리에서 항목들을 버퍼링하고 효율적인 전송을 위해 그것들을 배치(Batch)로 보냅니다.

또한 [라이브 메트릭 스트림](live-stream.md)에는 원격 분석의 라이브 스트리밍을 지원하는 사용자 지정 채널이 있습니다. 이 채널은 일반 원격 분석 채널과 독립적이며, 이 문서는 그것에 적용되지 않습니다.

## <a name="built-in-telemetry-channels"></a>기본 제공 원격 분석 채널

Application Insights .NET 및 .NET Core Sdk는 두 개의 기본 제공 채널과 함께 제공됩니다.

* `InMemoryChannel`: 항목들이 전송될 때까지 메모리의 항목들을 버퍼링하는 간단한 채널입니다. 항목들은 메모리에서 버퍼링되고, 30초마다 또는 500개 항목이 버퍼링될 때마다 플러시됩니다. 이 채널은 실패 후 원격 분석을 다시 보내려고 시도하지 않으므로 최소한의 안정성 보장을 제공합니다. 또한 이 채널은 디스크에 항목을 유지하지 않으므로, 보내지 않은 모든 항목은 애플리케이션이 종료될 때 영구적으로 손실됩니다(정상 종료 여부와 상관 없이). 이 채널은 메모리 내 원격 분석 항목을 동기적으로 강제 플러시하는 데 사용할 수 있는 `Flush()` 메서드를 구현합니다. 이 채널은 동기 플러시가 이상적인 단기 실행 애플리케이션에 적합합니다.

    이 채널은 더 큰 Microsoft.ApplicationInsights NuGet 패키지의 일부이며, 아무것도 구성되지 않은 경우 SDK에서 사용하는 기본 채널입니다.

* `ServerTelemetryChannel`: 재시도 정책 및 로컬 디스크에 데이터를 저장하는 기능이 있는 고급 채널입니다. 일시적인 오류가 발생하는 경우, 이 채널은 원격 분석 전송을 다시 시도합니다. 또한 이 채널은 로컬 디스크 스토리지를 사용하여 네트워크 중단 또는 대용량 원격 분석에서 디스크에 항목을 유지합니다. 이러한 재시도 메커니즘과 로컬 디스크 스토리지로 인해 이 채널은 보다 안정적인 것으로 간주되므로 모든 프로덕션 시나리오에 권장됩니다. 공식 설명서에 따라 구성된 [ASP.NET](./asp-net.md) 및 [ASP.NET Core](./asp-net-core.md) 애플리케이션에서 이 채널이 기본값입니다. 이 채널은 장기 실행 프로세스를 포함하는 서버 시나리오에 최적화되어 있습니다. 이 채널에서 구현하는 [`Flush()`](#which-channel-should-i-use) 메서드는 동기되지 않습니다.

    이 채널은 Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet 패키지로 제공 되며, Microsoft.ApplicationInsights.Web 또는 Microsoft.ApplicationInsights.AspNetCore NuGet 패키지를 사용하는 경우 자동으로 획득됩니다.

## <a name="configure-a-telemetry-channel"></a>원격 분석 채널 구성

원격 분석 채널을 활성 원격 분석 구성으로 설정하여 구성합니다. ASP.NET 애플리케이션의 경우, 구성에는 원격 분석 채널 인스턴스를 `TelemetryConfiguration.Active`로 설정하거나, `ApplicationInsights.config`를 수정해서 설정하는 작업이 포함됩니다. ASP.NET Core 애플리케이션의 경우, 구성에는 Dependency Injection Container에 채널을 추가하는 작업이 포함됩니다.

다음 섹션에서는 `StorageFolder`다양한 애플리케이션 유형에서 채널 설정을 구성하는 예를 보여 줍니다. `StorageFolder`은 구성 가능한 설정 중 하나일 뿐입니다. 구성 설정의 전체 목록은 이 문서의 뒷부분에 있는 [설정 섹션](#configurable-settings-in-channels)을 참조하세요.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>ASP.NET 애플리케이션에 대해 ApplicationInsights.config를 사용하여 구성

[ApplicationInsights.config](configuration-with-applicationinsights-config.md)의 다음 섹션에서는 사용자 지정 위치로 설정된 `StorageFolder`로 구성된 `ServerTelemetryChannel` 채널을 보여 줍니다.

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>ASP.NET 애플리케이션에 대한 코드 구성

다음 코드는 사용자 지정 위치로 설정된 `StorageFolder`로 'ServerTelemetryChannel' 인스턴스를 설정합니다. 애플리케이션의 시작 부분에 이 코드를 추가합니다(보통 Global.aspx.cs의 `Application_Start()` 메서드).

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

### <a name="configuration-in-code-for-aspnet-core-applications"></a>ASP.NET Core 애플리케이션에 대한 코드 구성

`Startup.cs` 클래스의 `ConfigureServices` 메서드를 다음과 같이 수정 합니다.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!IMPORTANT]
> ASP.NET Core 애플리케이션에는 `TelemetryConfiguration.Active`를 사용하여 채널을 구성 하지 않는 것이 좋습니다.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>.NET/.NET Core 콘솔 애플리케이션에 대한 코드 구성

콘솔 앱의 경우, 코드는 .NET 및 .NET Core 모두에 대해 동일합니다.

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>ServerTelemetryChannel의 작업 세부 정보

`ServerTelemetryChannel`는 도착 항목을 메모리 내 버퍼에 저장합니다. 항목은 30초마다, 또는 500개 항목이 버퍼링될 때 `Transmission` 인스턴스로 serialize, 압축 및 저장됩니다. 단일 `Transmission` 인스턴스는 최대 500개 항목을 포함하며, 단일 HTTPS 호출을 통해 Application Insights 서비스에 전송되는 원격 분석의 배치(Batch)를 나타냅니다.

기본값으로 최대 10개의 `Transmission` 인스턴스를 병렬로 전송할 수 있습니다. 원격 분석이 더 빠른 속도로 도착하거나, 네트워크 또는 Application Insights 백 엔드가 느려지는 경우, `Transmission` 인스턴스는 메모리에 저장됩니다. 이 메모리 내 `Transmission` 버퍼의 기본 용량은 5MB입니다. 메모리 내 용량이 초과되면 `Transmission` 인스턴스는 50MB 한도까지 로컬 디스크에 저장됩니다. `Transmission` 인스턴스는 네트워크 문제가 있는 경우에도 로컬 디스크에 저장됩니다. 로컬 디스크에 저장되는 항목만 애플리케이션 작동 중단에도 유지됩니다. 이 항목은 애플리케이션이 다시 시작될 때마다 전송됩니다. 네트워크 문제가 지속 되는 경우는 `ServerTelemetryChannel` 원격 분석 전송을 다시 시도 하기 전에 10 초에서 1 시간 사이의 지 수 백오프 논리를 사용 합니다. 

## <a name="configurable-settings-in-channels"></a>채널의 구성 가능한 설정

각 채널의 구성 가능한 설정에 대한 전체 목록은 다음을 참조하세요.

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

`ServerTelemetryChannel`에 가장 일반적으로 사용되는 설정은 다음과 같습니다.

1. `MaxTransmissionBufferCapacity`: 메모리의 버퍼 전송을 위해 채널에서 사용하는 최대 메모리 양(바이트)입니다. 이 용량에 도달하면 새 항목이 로컬 디스크에 직접 저장됩니다. 기본값은 5MB입니다. 높은 값을 설정하면 디스크 사용량이 감소하지만, 애플리케이션의 작동이 중단되면 메모리의 항목이 손실됩니다.

1. `MaxTransmissionSenderCapacity`: 동시에 Application Insights로 전송되는 최대 `Transmission` 인스턴스 수입니다. 기본값은 10입니다. 이 설정은 더 높은 수로 구성할 수 있으며, 이는 원격 분석을 대량으로 생성할 때 권장됩니다. 대용량은 일반적으로 부하 테스트 중 또는 샘플링을 해제할 때 발생합니다.

1. `StorageFolder`: 필요에 따라 디스크에 항목을 저장하기 위해 채널에서 사용하는 폴더입니다. 다른 경로를 명시적으로 지정하지 않으면 Windows 에서는 %LOCALAPPDATA% or %TEMP%가 사용됩니다. Windows 이외의 환경에서는 유효한 위치를 지정해야 합니다. 그렇지 않으면 원격 분석이 로컬 디스크에 저장되지 않습니다.

## <a name="which-channel-should-i-use"></a>어떤 채널을 사용해야 하나요?

`ServerTelemetryChannel`는 장기 실행 애플리케이션을 포함하는 대부분의 프로덕션 시나리오에 권장됩니다. `ServerTelemetryChannel`에서 구현하는 `Flush()` 메서드는 동기되지 않으며, 메모리나 디스크에서 보류 중인 모든 항목을 전송하는 것도 보장하지 않습니다. 애플리케이션이 곧 종료되는 시나리오에서 이 채널을 사용하는 경우, `Flush()`를 호출한 후 약간의 지연을 도입하는 것이 좋습니다. 필요할 수 있는 정확한 지연 시간은 예측할 수 없습니다. 이는 메모리에 있는 항목 수 또는 `Transmission` 인스턴스의 수, 디스크 내의 수, 백 엔드로 전송되는 수, 채널이 지수 백오프 시나리오의 중간에 있는지 여부 등의 요소에 따라 달라 집니다.

동기 플러시를 수행해야 하는 경우, `InMemoryChannel`를 사용하는 것이 좋습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Application Insights 채널은 원격 분석 제공을 보장 하나요? 그렇지 않은 경우, 원격 분석이 손실될 수 있는 시나리오는 무엇입니까?

간단한 대답은 어떠한 기본 제공 채널도 백 엔드로 원격 분석 제공에 대해 트랜잭션 유형의 보증을 제공하지 않는다는 것입니다. `ServerTelemetryChannel`는 신뢰할 수 있는 제공 측면에서 `InMemoryChannel`와 비교하여 더 고급이지만, 원격 분석을 전송하는 데에는 최선의 시도만 수행합니다. 다음과 같은 일반적인 시나리오를 비롯한 몇 가지 상황에서는 원격 분석이 여전히 손실될 수 있습니다.

1. 애플리케이션의 작동이 중단되면 메모리의 항목이 손실됩니다.

1. 네트워크 문제가 장기간 계속되면 원격 분석이 손실됩니다. 네트워크 중단 중에 또는 Application Insights 백 엔드에서 문제가 발생하는 경우, 원격 분석은 로컬 디스크에 저장됩니다. 그러나 48시간이 지난 항목은 삭제됩니다.

1. Windows에서 원격 분석을 저장하는 기본 디스크 위치는 %LOCALAPPDATA% or %TEMP%입니다. 이러한 위치는 일반적으로 컴퓨터에 로컬로 있습니다. 애플리케이션이 물리적으로 한 위치에서 다른 위치로 마이그레이션되면, 원래 위치에 저장된 원격 분석은 모두 손실됩니다.

1. Windows의 웹앱에서, 기본 디스크 스토리지 위치는 D:\local\LocalAppData입니다. 이 위치는 계속 유지되지 않습니다. 앱을 다시 시작하고, 스케일 아웃하며, 기타 작업을 수행하는 경우 이러한 위치가 삭제되어, 여기에 저장된 원격 분석의 손실을 초래합니다. 기본값을 재정의해서 D:\home과 같은 지속형 위치로 스토리지를 지정할 수 있습니다. 그러나 이러한 지속형 위치는 원격 스토리지에서 제공되므로 속도가 느려질 수 있습니다.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>ServerTelemetryChannel는 Windows 이외의 시스템에서 작동 하나요?

패키지와 네임스페이스의 이름에 "WindowsServer"가 포함되지만, 이 채널은 Windows 이외의 시스템에서 지원됩니다. 단, 다음은 예외입니다. Windows 이외의 시스템에서 이 채널은 기본값으로 로컬 스토리지 폴더를 만들지 않습니다. 로컬 스토리지 폴더를 만들고 채널이 이를 사용하도록 구성해야 합니다. 로컬 스토리지가 구성된 후에는 채널이 모든 시스템에서 동일한 방식으로 작동합니다.

> [!NOTE]
> 릴리스 2.15.0-beta3 이상의 경우, 로컬 스토리지는 이제 Linux, Mac 및 Windows 용으로 자동 생성됩니다. Windows가 아닌 체제의 경우, SDK는 다음 논리에 따라 로컬 스토리지 폴더를 자동으로 생성합니다.
> - `${TMPDIR}` - `${TMPDIR}` 환경 변수가 설정된 경우, 이 위치가 사용됩니다.
> - `/var/tmp` - 이전 위치가 없는 경우, 당사는 `/var/tmp`를 시도합니다.
> - `/tmp` - 이전 위치가 둘 다 없는 경우, 당사는 `tmp`를 시도합니다. 
> - 이러한 위치가 아예 없는 경우, 로컬 스토리지가 만들어지지 않으며, 수동 구성도 필요합니다. [전체 구현 세부 정보](https://github.com/microsoft/ApplicationInsights-dotnet/pull/1860).

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>SDK에서 임시 로컬 스토리지를 작성하나요? 데이터가 스토리지에서 암호화되나요?

네트워크 문제나 대역폭 제한이 있는 동안에 SDK는 로컬 스토리지에 원격 분석 항목을 저장합니다. 이 데이터는 로컬로 암호화되지 않습니다.

Windows 시스템의 경우, SDK는 자동 으로 %TEMP% or %LOCALAPPDATA% 디렉터리에 임시 로컬 폴더를 만들고, 관리자와 현재 사용자만으로 액세스를 제한합니다.

Windows 이외의 시스템의 경우, SDK에서 로컬 스토리지를 자동으로 만들지 않으므로, 기본값으로 로컬에 저장되는 데이터가 없습니다.

> [!NOTE]
> 릴리스 2.15.0-beta3 이상의 경우, 로컬 스토리지는 이제 Linux, Mac 및 Windows 용으로 자동 생성됩니다. 

 스토리지 디렉터리를 직접 만들고 채널이 이를 사용하도록 구성할 수 있습니다. 이 경우 디렉터리에 보안이 설정 되었는지 확인해야 합니다.
자세한 내용은 [데이터 보호 및 프라이버시](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)를 참조하세요.

## <a name="open-source-sdk"></a>오픈 소스 SDK
Application Insights의 모든 SDK와 마찬가지로 채널은 오픈 소스입니다. [공식 GitHub 리포지토리](https://github.com/Microsoft/ApplicationInsights-dotnet)에서 코드를 읽고 이에 참여하거나 문제를 보고 합니다.

## <a name="next-steps"></a>다음 단계

* [샘플링](./sampling.md)
* [SDK 문제 해결](./asp-net-troubleshoot-no-data.md)

