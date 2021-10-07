---
title: 캐시 ASP.NET 세션 상태 제공자
description: Azure Cache for Redis를 사용하여 메모리 내 ASP.NET 세션 상태를 저장하는 방법을 알아봅니다.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 05/01/2017
ms.openlocfilehash: 7d9d4efb683b7401c4601c86425490957d085d23
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658712"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>Azure Cache for Redis에 대한 ASP.NET 세션 상태 제공자

Azure Cache for Redis는 메모리 내 또는 SQL Server 데이터베이스가 아니라 Azure Cache for Redis를 통해 세션 상태를 저장하는 데 사용할 수 있는 세션 상태 제공자를 제공합니다. 캐싱 세션 상태 제공자를 사용하려면 먼저 캐시를 구성하고 Azure Cache for Redis 세션 상태 NuGet 패키지를 사용하여 캐시용 ASP.NET 애플리케이션을 구성합니다. ASP.NET Core 애플리케이션의 경우 [ASP.NET Core에서 세션 및 상태 관리](/aspnet/core/fundamentals/app-state)를 읽습니다.

종종 실제 클라우드 앱에서 사용자 세션에 대한 일종의 상태를 저장을 회피하는 데 실용적이지 않지만 일부 접근 방법은 다른 항목 보다 성능 및 확장성에 더 많은 영향을 줍니다. 상태를 저장해야 하는 경우 가장 좋은 해결법은 상태의 크기를 작게 유지하고 쿠키에 저장하는 것입니다. 이것이 어려운 경우 다음 해결법은 분산된 메모리 내 캐시에 대해 공급자로 ASP.NET 세션 상태를 사용하는 것입니다. 성능 및 확장성 측면에서 최악의 해결법은 데이터베이스 지원 세션 상태 제공자를 사용하는 것입니다. 이 항목은 Azure Cache for Redis에 대해 ASP.NET 세션 상태 제공자를 사용하는 가이드를 제공합니다. 다른 세션 상태 옵션에 대한 내용은 [ASP.NET 세션 상태 옵션](#aspnet-session-state-options)을 참조하세요.

## <a name="store-aspnet-session-state-in-the-cache"></a>캐시에 ASP.NET 세션 상태 저장

Azure Cache for Redis 세션 상태 NuGet 패키지를 사용하여 Visual Studio에서 클라이언트 애플리케이션을 구성하려면 **도구** 메뉴에서 **NuGet 패키지 관리자**, **패키지 관리자 콘솔** 을 차례로 클릭합니다.

`Package Manager Console` 창에서 다음 명령을 실행합니다.

```powershell
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> 프리미엄 계층에서 클러스터링 기능을 사용하는 경우 [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 이상을 사용하지 않으면 예외가 throw됩니다. 2.0.1 이상 버전으로 이동하는 주요 변경 내용입니다. 자세한 내용은 [v2.0.0 주요 변경 세부 사항](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details)을 참조하세요. 이 문서를 업데이트할 당시 이 패키지의 현재 버전은 2.2.3입니다.
>
>

Redis 세션 상태 제공자 NuGet 패키지는 Redis 패키지에 종속 되어 있습니다. Redis 패키지는 프로젝트에 없는 경우 설치 됩니다.

NuGet 패키지에서는 필수 어셈블리 참조를 다운로드하고 추가하며 web.config 파일에 다음 섹션을 추가합니다. 이 섹션에서는 Azure Cache for Redis 세션 상태 제공자를 사용하기 위해 ASP.NET 애플리케이션에 필수 구성을 포함합니다.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
  <providers>
    <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
    <!-- 'throwOnError','retryTimeoutInMilliseconds','databaseId' and 'applicationName' can be used with both options. -->
    <!--
      <add name="MySessionStateStore" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "5000" [number]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "1000" [number]
        connectionString = "<Valid StackExchange.Redis connection string>" [String]
        settingsClassName = "<Assembly qualified class name that contains settings method specified below. Which basically return 'connectionString' value>" [String]
        settingsMethodName = "<Settings method should be defined in settingsClass. It should be public, static, does not take any parameters and should have a return type of 'String', which is basically 'connectionString' value.>" [String]
        loggingClassName = "<Assembly qualified class name that contains logging method specified below>" [String]
        loggingMethodName = "<Logging method should be defined in loggingClass. It should be public, static, does not take any parameters and should have a return type of System.IO.TextWriter.>" [String]
        redisSerializerType = "<Assembly qualified class name that implements Microsoft.Web.Redis.ISerializer>" [String]
      />
    -->
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider"
         host=""
         accessKey=""
         ssl="true" />
  </providers>
</sessionState>
```

주석 처리된 섹션은 특성의 예와 각 특성의 샘플 설정을 제공합니다.

Microsoft Azure Portal의 캐시 왼쪽에서 값으로 특성을 구성하고, 필요에 따라 다른 값을 구성합니다. 캐시 속성에 액세스하는 방법에 대한 지침은 [Azure Cache for Redis 설정 구성](cache-configure.md#configure-azure-cache-for-redis-settings)을 참조하세요.

* **호스트** – 캐시 엔드포인트를 지정합니다.
* **포트** – TLS 설정에 따라 비-TLS/SSL 포트 또는 TLS/SSL 포트를 사용합니다.
* **선택키** – 캐시에 적합한 기본 또는 보조 키를 사용합니다.
* **ssl** – TLS로 캐시/클라이언트 통신을 보호하려는 경우 true가 되고, 그 외의 경우 false입니다. 올바른 포트를 지정해야 합니다.
  * 비 TLS 포트는 기본적으로 새 캐시에 대해 사용하지 않도록 설정됩니다. TLS 포트를 사용하여 설정에 대한 true를 지정합니다. 비-TLS 포트 사용 방법에 대한 자세한 내용은 [캐시 구성](cache-configure.md) 토픽의 [액세스 포트](cache-configure.md#access-ports) 섹션을 참조하세요.
* **throwOnError** – 오류가 있는 경우 예외를 throw하려면 true이고 작업을 자동으로 실패하게 하려면 false입니다. 정적 Microsoft.Web.Redis.RedisSessionStateProvider.LastException 속성을 확인하여 오류를 확인할 수 있습니다. 기본값은 true입니다.
* **retryTimeoutInMilliseconds** – 이 간격 동안 실패한 작업이 다시 시도되며 밀리초 단위로 지정됩니다. 처음 다시 시도는 20밀리초 후에 발생하고 다시 시도는 retryTimeoutInMilliseconds 간격이 만료될 때까지 매초 마다 발생합니다. 이 간격 후에 즉시 최종적으로 한 번 작업을 다시 시도합니다. 작업이 계속 실패하면 throwOnError 설정에 따라 호출자에게 예외가 다시 throw됩니다. 기본값은 다시 시도하지 않는다는 의미의 0입니다.
* **databaseId** – 캐시 출력 데이터에 사용할 데이터베이스를 지정합니다. 지정하지 않으면 기본값 0이 사용됩니다.
* **applicationName** – `{<Application Name>_<Session ID>}_Data`로 redis에 저장된 키입니다. 이 이름 지정 체계를 사용하면 여러 애플리케이션에서 동일한 Redis 인스턴스를 공유할 수 있습니다. 이 매개변수는 선택적이며 사용자가 제공하지 않으면 기본값이 사용됩니다.
* **connectionTimeoutInMilliseconds** – 이 설정은 StackExchange.Redis 클라이언트의 connectTimeout 설정을 무시할 수 있게 해줍니다. 지정하지 않으면 기본 connectTimeout 설정인 5000이 사용됩니다. 더 자세한 내용은 [StackExchange.Redis 구성 모델](https://go.microsoft.com/fwlink/?LinkId=398705)을 참조하세요.
* **operationTimeoutInMilliseconds** – 이 설정은 StackExchange.Redis 클라이언트의 syncTimeout 설정을 무시할 수 있게 해 줍니다. 지정하지 않으면 기본 syncTimeout 설정인 1000이 사용됩니다. 더 자세한 내용은 [StackExchange.Redis 구성 모델](https://go.microsoft.com/fwlink/?LinkId=398705)을 참조하세요.
* **redisSerializerType** - 이 설정을 사용하면 Redis로 전송되는 세션 콘텐츠의 사용자 지정 serialization을 지정할 수 있습니다. 지정된 형식은 `Microsoft.Web.Redis.ISerializer`를 구현해야 하며 공용 매개 변수가 없는 생성자를 선언해야 합니다. 기본적으로 `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter`가 사용됩니다.

이러한 속성에 대한 자세한 내용은 [Redis에 대한 ASP.NET 세션 상태 제공자 발표](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/)에서 원래 블로그 게시물 발표를 참조하세요.

Web.config 파일에서 표준 InProc 세션 상태 제공자 섹션을 주석으로 처리하는 것을 기억하세요.

```xml
<!-- <sessionState mode="InProc"
     customProvider="DefaultSessionProvider">
     <providers>
        <add name="DefaultSessionProvider"
              type="System.Web.Providers.DefaultSessionStateProvider,
                    System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                    PublicKeyToken=31bf3856ad364e35"
              connectionStringName="DefaultConnection" />
      </providers>
</sessionState> -->
```

이 단계를 수행하면, 애플리케이션은 Azure Cache for Redis 섹션 상태 제공자를 사용하도록 구성됩니다. 애플리케이션에서 세션 상태를 사용하는 경우 Azure Cache for Redis 인스턴스에 저장됩니다.

> [!IMPORTANT]
> 기본값 메모리 내 ASP.NET 세션 상태 제공자에 저장될 수 있는 데이터와 달리 캐시에 저장된 데이터는 직렬화할 수 있어야 합니다. Redis에 세션 상태 제공자를 사용하면 세션 상태에 저장되는 데이터 형식을 직렬화할 수 있는지 확인해야 합니다.
>
>

## <a name="aspnet-session-state-options"></a>ASP.NET 세션 상태 옵션

* 메모리 내 세션 상태 제공자 - 이 공급자는 메모리에 세션 상태를 저장합니다. 이 공급자를 사용하면 단순하고 빠르다는 이점이 있습니다. 그러나 배포되지 않는 메모리 공급자에서 사용 중인 경우 Web Apps의 크기를 조정할 수 없습니다.
* SQL Server 세션 상태 제공자 - 이 공급자는 SQL Server에 세션 상태를 저장합니다. 영구 스토리지에서 세션 상태를 유지하려는 경우 이 제공자를 사용합니다. Web App의 크기를 조정할 수 있지만 세션에 SQL Server를 사용하면 Web App의 성능에 영향을 줍니다. 이 공급자를 [메모리 내 OLTP 구성](/archive/blogs/sqlserverstorageengine/asp-net-session-state-with-sql-server-in-memory-oltp)에 사용하여 성능을 높일 수도 있습니다.
* Azure Cache for Redis 세션 상태 제공자와 같은 배포된 메모리 내 세션 상태 제공자 - 이 공급자는 두 분야의 모든 장점을 제공합니다. Web App에는 빠르고 간단하며 확장성 있는 세션 상태 제공자가 있을 수 있습니다. 이 제공자가 캐시에서 세션 상태를 저장하기 때문에 앱이 일시적인 네트워크 오류와 같은 분산된 메모리 내 캐시와 통신할 때 관련된 모든 특성을 고려해야 합니다. 캐시 사용의 모범 사례는 Microsoft Patterns &amp; Practices [Azure 클라우드 애플리케이션 설계 및 구현 지침](https://github.com/mspnp/azure-guidance)에서 [캐싱 지침](/azure/architecture/best-practices/caching)을 참조하세요.

세션 상태 및 기타 모범 사례에 대한 자세한 내용은 [웹 개발 모범 사례(Azure를 사용하는 실제 클라우드 앱 빌드)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices)를 참조하세요.

## <a name="third-party-session-state-providers"></a>타사 세션 상태 공급자

* [NCache](https://www.alachisoft.com/ncache/session-index.html)
* [Apache ignite](https://apacheignite-net.readme.io/docs/aspnet-session-state-caching)

## <a name="next-steps"></a>다음 단계

[Azure Cache for Redis에 대한 ASP.NET 출력 캐시 제공자](cache-aspnet-output-cache-provider.md)를 확인합니다.
