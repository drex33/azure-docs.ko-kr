---
title: 토큰 캐시 직렬화(MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: MSAL.NET(.NET용 Microsoft 인증 라이브러리)을 사용하는 토큰 캐시의 serialization과 사용자 지정 serialization에 관해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2021
ms.author: jmprieur
ms.reviewer: mmacy
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 67dbc1ba66f18bb6d779d1185d863541272acd56
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123451712"
---
# <a name="token-cache-serialization-in-msalnet"></a>MSAL.NET에서 토큰 캐시 직렬화

[토큰을 획득](msal-acquire-cache-tokens.md)한 후 MSAL(Microsoft 인증 라이브러리)이 해당 토큰을 캐시합니다. 퍼블릭 클라이언트 애플리케이션(데스크톱/모바일 앱)은 다른 방법으로 토큰을 획득하기 전에 캐시에서 토큰을 가져와야 합니다. 기밀 클라이언트 애플리케이션의 획득 방법은 캐시 자체를 관리합니다. 이 문서에서는 MSAL.NET에서 제공하는 토큰 캐시의 기본 및 사용자 지정 직렬화에 대해 설명합니다.

## <a name="quick-summary"></a>빠른 요약

권장 사항은 다음과 같습니다.
- 웹앱과 웹 API에서 [“Microsoft.Identity.Web”의 토큰 캐시 직렬 변환기](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization)를 사용합니다. 토큰을 저장할 분산 데이터베이스 또는 캐시 시스템도 제공합니다.
  - ASP.NET Core [웹앱](scenario-web-app-call-api-overview.md)과 [웹 API](scenario-web-api-call-api-overview.md)에서는 ASP.NET Core의 상위 수준 API로 Microsoft.Identity.Web을 사용합니다.
  - ASP.NET 클래식, .NET Core, .NET 프레임워크에서는 Microsoft.Identity.Web에서 제공되는 [MSAL용 토큰 캐시 serialization 어댑터]()와 함께 직접 MSAL.NET을 사용합니다. 
- 데스크톱 애플리케이션(토큰을 저장하는 데 파일 시스템을 사용할 수 있음)에서는 MSAL.Net과 함께 [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/wiki/Cross-platform-Token-Cache)을 사용합니다.
- 모바일 애플리케이션(Xamarin.iOS, Xamarin.Android, 유니버설 Windows 플랫폼)에서는 MSAL.NET이 캐시를 처리하므로 아무 작업도 하지 마세요. 이러한 플랫폼에는 보안 스토리지가 있습니다.

## <a name="aspnet-core-web-apps-and-web-apis"></a>[ASP.NET Core 웹앱 및 웹 API](#tab/aspnetcore)

[Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web) 라이브러리는 토큰 캐시 serialization이 포함된 NuGet 패키지 [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web)을 제공합니다.

| 확장 메서드 | 설명  |
| ---------------- | ------------ |
| `AddInMemoryTokenCaches` | 메모리 내 토큰 캐시 직렬화입니다. 이 구현은 샘플에서 매우 유용합니다. 웹앱이 다시 시작될 때 토큰 캐시가 손실되어도 상관하지 않는 경우 프로덕션 애플리케이션에서도 유용합니다. `AddInMemoryTokenCaches`는 캐시 항목이 사용되지 않는 한 만료되는 기간을 지정할 수 있는 `MsalMemoryTokenCacheOptions` 형식의 선택적 매개 변수를 사용합니다.
| `AddSessionTokenCaches` | 토큰 캐시가 사용자 세션에 바인딩됩니다. 쿠키 크기가 너무 커지므로 ID 토큰에 클레임이 많이 포함된 경우에는 이 옵션이 적합하지 않습니다.
| `AddDistributedTokenCaches` | 토큰 캐시는 ASP.NET Core `IDistributedCache` 구현에 대한 어댑터이므로 분산 메모리 캐시, Redis 캐시, 분산 NCache 또는 SQL Server 캐시 중에서 선택할 수 있습니다. `IDistributedCache` 구현에 관한 자세한 내용은 [분산 메모리 캐시](/aspnet/core/performance/caching/distributed)를 참조하세요.


ASP.NET Core 애플리케이션에서 [Startup](/aspnet/core/fundamentals/startup) 클래스의 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) 메서드에서 메모리 내 캐시를 사용하는 코드 예제는 다음과 같습니다.

```CSharp
#using Microsoft.Identity.Web
```

```CSharp
using Microsoft.Identity.Web;

public class Startup
{
 const string scopesToRequest = "user.read";
  
  public void ConfigureServices(IServiceCollection services)
  {
   // code before
   services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
           .AddMicrosoftIdentityWebApp(Configuration)
             .EnableTokenAcquisitionToCallDownstreamApi(new string[] { scopesToRequest })
                .AddInMemoryTokenCaches();
   // code after
  }
  // code after
}
```

캐시의 관점에서 코드는 ASP.NET Core 웹 API에서 유사합니다.


가능한 분산 캐시의 예제는 다음과 같습니다.

```C#
// or use a distributed Token Cache by adding
   services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
           .AddMicrosoftIdentityWebApp(Configuration)
             .EnableTokenAcquisitionToCallDownstreamApi(new string[] { scopesToRequest }
               .AddDistributedTokenCaches();

// and then choose your implementation of distributed cache

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache();

// Or a Redis cache
// Requires the Microsoft.Extensions.Caching.StackExchangeRedis NuGet package
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
// Requires the Microsoft.Extensions.Caching.SqlServer NuGet package
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});

// Or a Cosmos DB cache
// Requires the Microsoft.Extensions.Caching.Cosmos NuGet package
services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
{
    cacheOptions.ContainerName = Configuration["CosmosCacheContainer"];
    cacheOptions.DatabaseName = Configuration["CosmosCacheDatabase"];
    cacheOptions.ClientBuilder = new CosmosClientBuilder(Configuration["CosmosConnectionString"]);
    cacheOptions.CreateIfNotExists = true;
});
```

이러한 사용은 [ASP.NET Core 웹앱 자습서](/aspnet/core/tutorials/first-mvc-app/)의 [2-2 토큰 캐시](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) 단계에 나와 있습니다.

## <a name="non-aspnet-core-web-apps-and-web-apis"></a>[비 ASP.NET Core 웹앱 및 웹 API](#tab/aspnet)

MSAL.NET을 사용하는 경우에도 Microsoft.Identity.Web에서 가져온 토큰 캐시 직렬 변환기를 활용할 수 있습니다. 

### <a name="referencing-the-nuget-package"></a>NuGet 패키지 참조

MSAL.NET 외에 [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) NuGet 패키지를 프로젝트에 추가합니다.

### <a name="configuring-the-token-cache"></a>토큰 캐시 구성

다음 코드에서는 메모리 내 잘 분할된 토큰 캐시를 앱에 추가하는 방법을 보여줍니다.

```CSharp
using Microsoft.Identity.Web;
using Microsoft.Identity.Client;
using Microsoft.Extensions.DependencyInjection;
```

```CSharp

 private static IConfidentialClientApplication app;

public static async Task<IConfidentialClientApplication> BuildConfidentialClientApplication(
  string clientId,
  CertificateDescription certDescription,
  string tenant)
 {
  if (app== null)
  {
     // Create the confidential client application
     app= ConfidentialClientApplicationBuilder.Create(clientId)
       // Alternatively to the certificate you can use .WithClientSecret(clientSecret)
       .WithCertificate(certDescription.Certificate)
       .WithLegacyCacheCompatibility(false)
       .WithTenantId(tenant)
       .Build();

     // Add an in-memory token cache. Other options available: see below
     app.AddInMemoryTokenCache();
   }
   return app;
  }
```

### <a name="available-caching-technologies"></a>사용 가능한 캐싱 기술

`app.AddInMemoryTokenCache();` 대신 .NET에서 제공하는 분산 토큰 캐시를 비롯한 다양한 캐싱 기술을 사용할 수 있습니다.

#### <a name="in-memory-token-cache"></a>메모리 내 토큰 캐시

메모리 내 토큰 캐시 serialization은 샘플에서 매우 유용합니다. 웹앱이 다시 시작될 때 토큰 캐시가 손실되어도 상관하지 않는 경우 프로덕션 애플리케이션에서도 유용합니다.

```CSharp 
     // Add an in-memory token cache
     app.AddInMemoryTokenCache();
```

#### <a name="distributed-caches"></a>분산 캐시

`app.AddDistributedTokenCache`를 사용하는 경우, 토큰 캐시는 .NET `IDistributedCache` 구현에 대한 어댑터이므로 분산 메모리 캐시, Redis Cache, CosmosDb 또는 SQL Server 캐시 중에서 선택할 수 있습니다. `IDistributedCache` 구현에 관한 자세한 내용은 [분산 메모리 캐시](/aspnet/core/performance/caching/distributed)를 참조하세요.

##### <a name="distributed-in-memory-token-cache"></a>메모리 토큰 캐시에 분산됨

```CSharp 
     // In memory distributed token cache
     app.AddDistributedTokenCache(services =>
     {
       // In net462/net472, requires to reference Microsoft.Extensions.Caching.Memory
       services.AddDistributedMemoryCache();
     });
```

##### <a name="sql-server"></a>데이터베이스 가져오기

```CSharp 
     // SQL Server token cache
     app.AddDistributedTokenCache(services =>
     {
      services.AddDistributedSqlServerCache(options =>
      {
       // In net462/net472, requires to reference Microsoft.Extensions.Caching.Memory

       // Requires to reference Microsoft.Extensions.Caching.SqlServer
       options.ConnectionString = @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=TestCache;Integrated Security=True;Connect Timeout=30;Encrypt=False;TrustServerCertificate=False;ApplicationIntent=ReadWrite;MultiSubnetFailover=False";
       options.SchemaName = "dbo";
       options.TableName = "TestCache";

       // You don't want the SQL token cache to be purged before the access token has expired. Usually
       // access tokens expire after 1 hour (but this can be changed by token lifetime policies), whereas
       // the default sliding expiration for the distributed SQL database is 20 mins. 
       // Use a value which is above 60 mins (or the lifetime of a token in case of longer lived tokens)
       options.DefaultSlidingExpiration = TimeSpan.FromMinutes(90);
      });
     });
```

##### <a name="redis-cache"></a>Redis Cache

```CSharp 
     // Redis token cache
     app.AddDistributedTokenCache(services =>
     {
       // Requires to reference Microsoft.Extensions.Caching.StackExchangeRedis
       services.AddStackExchangeRedisCache(options =>
       {
         options.Configuration = "localhost";
         options.InstanceName = "Redis";
       });
      });
```

토큰 획득이 Redis Cache 시간 제한만큼 자주 사용되는 경우에도 [캐시 동기화 사용 안 함](#disabling-cache-synchronization)을 참조하세요. 

##### <a name="cosmos-db"></a>Cosmos DB

```CSharp 
      // Cosmos DB token cache
      app.AddDistributedTokenCache(services =>
      {
        // Requires to reference Microsoft.Extensions.Caching.Cosmos
        services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
        {
          cacheOptions.ContainerName = Configuration["CosmosCacheContainer"];
          cacheOptions.DatabaseName = Configuration["CosmosCacheDatabase"];
          cacheOptions.ClientBuilder = new CosmosClientBuilder(Configuration["CosmosConnectionString"]);
          cacheOptions.CreateIfNotExists = true;
        });
       });
```

### <a name="disabling-legacy-token-cache"></a>레거시 토큰 캐시 사용 안 함

MSAL에는 특히 레거시 ADAL 캐시와 상호 작용하는 기능을 사용하도록 설정하는 일부 내부 코드가 있습니다. MSAL 및 ADAL과 함께 사용되지 않는 경우(따라서 레거시 캐시가 사용되지 않는 경우)에는 관련 레거시 캐시 코드가 필요하지 않습니다. MSAL [4.25.0](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases/tag/4.25.0)은 레거시 ADAL 캐시 코드를 사용하지 않도록 설정하고 캐시 사용 성능을 향상시키는 기능을 추가합니다. 레거시 캐시를 사용하지 않도록 설정하기 전후 성능 비교는 끌어오기 요청 [#2309](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/pull/2309)를 참조하세요. 아래와 같은 애플리케이션 작성기에서 `.WithLegacyCacheCompatibility(false)`를 호출합니다.

```csharp
var app = ConfidentialClientApplicationBuilder
    .Create(clientId)
    .WithClientSecret(clientSecret)
    .WithLegacyCacheCompatibility(false)
    .Build();
```

### <a name="disabling-cache-synchronization"></a>캐시 동기화 사용 안 함

기본적으로 MSAL은 캐시 읽기 및 캐시 쓰기 간에 기밀 클라이언트 애플리케이션 수준에서 캐시 액세스를 잠급니다. 이 잠금은 시간 제한이 발생할 때까지 캐시 직렬 변환기가 시간이 오래 걸리는 경우에 발생할 수 있으며, 이는 Redis Cache를 사용하는 경우일 수 있습니다. `WithCacheSynchronization` 플래그를 false로 설정하여 낙관적 캐시 잠금 전략을 사용하도록 설정할 수 있습니다. 이렇게 하면 특히 ConfidentialClientApplication 개체가 요청에서 재사용될 때 성능이 향상될 수 있습니다. 

```csharp
var app = ConfidentialClientApplicationBuilder
    .Create(clientId)
    .WithClientSecret(clientSecret)
    .WithCacheSynchronization(false)
    .Build();
```

### <a name="monitor-cache-hit-ratios-and-cache-performance"></a>모니터 캐시 적중률 및 캐시 성능

MSAL은 [AuthenticationResult.AuthenticationResultMetadata](/dotnet/api/microsoft.identity.client.authenticationresultmetadata) 개체의 일부로 중요한 메트릭을 노출합니다. 

| 메트릭       | 의미     | 알람을 트리거하는 경우는 언제인가요?    |
| :-------------: | :----------: | :-----------: |
|  `DurationTotalInMs` | 네트워크 호출 및 캐시를 포함하여 MSAL에 소요된 총 시간   | 전체 대기 시간이 긴 경우(> 1초)에 대한 알람입니다. 값은 토큰 원본에 따라 달라집니다. 캐시에서는 하나의 캐시 액세스입니다. AAD에서는 두 개의 캐시 액세스 + 하나의 HTTP 호출입니다. 하나의 추가 HTTP 호출로 인해 첫 번째 호출(프로세스별)이 더 오래 걸립니다. |
|  `DurationInCacheInMs` | 앱 개발자가 사용자 지정한 토큰 캐시를 로드하거나 저장하는 데 걸린 시간입니다(예: Redis에 저장).| 스파이크에 대한 알람입니다. |
|  `DurationInHttpInMs`| AAD에 대한 HTTP 호출을 수행하는 데 걸린 시간입니다.  | 스파이크에 대한 알람입니다.|
|  `TokenSource` | 토큰의 원본을 나타냅니다. 토큰은 캐시에서 훨씬 빠르게 검색됩니다(예: ~100밀리초 및 700밀리초). 캐시 적중률을 모니터링하고 알람하는 데 사용할 수 있습니다. | `DurationTotalInMs` 사용 |

### <a name="samples"></a>샘플

- .NET Framework 및 .NET Core 애플리케이션에서 토큰 캐시 직렬 변환기 사용은 이 샘플 [ConfidentialClientTokenCache](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache)에서 표시됩니다. 
- 다음 샘플은 동일한 기술을 사용하는 ASP.NET 웹앱입니다. https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect ([WebApp/Utils/MsalAppBuilder.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs) 참조)

## <a name="desktop-apps"></a>[데스크톱 앱](#tab/desktop)

데스크톱 애플리케이션에서는 플랫폼 간 토큰 캐시를 사용하는 것이 좋습니다.

#### <a name="cross-platform-token-cache-msal-only"></a>플랫폼 간 토큰 캐시(MSAL에만 해당)

MSAL.NET은 https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet 에서 소스 코드를 사용할 수 있는 Microsoft.Identity.Client.Extensions.Msal이라는 별도의 라이브러리에서 플랫폼 간 토큰 캐시를 제공합니다.

##### <a name="referencing-the-nuget-package"></a>NuGet 패키지 참조

[Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/) NuGet 패키지를 프로젝트에 추가합니다.

##### <a name="configuring-the-token-cache"></a>토큰 캐시 구성

자세한 내용은 https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/wiki/Cross-platform-Token-Cache를 참조하세요. 다음은 플랫폼 간 토큰 캐시의 사용 예제입니다.

```csharp
 var storageProperties =
     new StorageCreationPropertiesBuilder(Config.CacheFileName, Config.CacheDir)
     .WithLinuxKeyring(
         Config.LinuxKeyRingSchema,
         Config.LinuxKeyRingCollection,
         Config.LinuxKeyRingLabel,
         Config.LinuxKeyRingAttr1,
         Config.LinuxKeyRingAttr2)
     .WithMacKeyChain(
         Config.KeyChainServiceName,
         Config.KeyChainAccountName)
     .Build();

 IPublicClientApplication pca = PublicClientApplicationBuilder.Create(clientId)
    .WithAuthority(Config.Authority)
    .WithRedirectUri("http://localhost")  // make sure to register this redirect URI for the interactive login 
    .Build();
    

// This hooks up the cross-platform cache into MSAL
var cacheHelper = await MsalCacheHelper.CreateAsync(storageProperties );
cacheHelper.RegisterCache(pca.UserTokenCache);
         
```

## <a name="mobile-apps"></a>[모바일 앱](#tab/mobile)

MSAL.NET에서는 메모리 내 토큰 캐시가 기본적으로 제공됩니다. 사용자가 플랫폼의 일부로 보안 스토리지를 사용할 수 있는 UWP(유니버설 Windows 플랫폼), Xamarin.iOS, Xamarin.Android 등의 플랫폼에는 기본적으로 serialization이 제공됩니다.

## <a name="write-your-own-cache"></a>[고유한 캐시 작성](#tab/custom)

실제로 고유한 토큰 캐시 직렬 변환기를 작성하려는 경우 MSAL.NET은 .NET Framework 및 .NET Core 하위 플랫폼에서 사용자 지정 토큰 캐시 serialization을 제공합니다. 캐시에 액세스하면 이벤트가 실행되고, 앱에서 캐시를 직렬화하거나 역직렬화할지 여부를 선택할 수 있습니다. 사용자를 처리하는 기밀 클라이언트 애플리케이션(사용자를 로그인하고 웹 API 호출하는 웹앱 및 다운스트림 웹 API를 호출하는 웹 API)에는 많은 사용자가 있을 수 있으며 사용자가 병렬로 처리됩니다. 보안 및 성능상의 이유로 사용자당 하나의 캐시를 직렬화하는 것이 좋습니다. 직렬화 이벤트는 처리된 사용자의 ID를 기반으로 하여 캐시 키를 계산하고 해당 사용자에 대한 토큰 캐시를 직렬화/역직렬화합니다.

사용자 지정 직렬화는 모바일 플랫폼(UWP, Xamarin.iOS 및 Xamarin.Android)에서 사용할 수 없습니다. MSAL은 이미 이러한 플랫폼을 위한 안전하고 성능이 뛰어난 직렬화 메커니즘을 제공합니다. 하지만 .NET 데스크톱 및 .NET Core 애플리케이션은 다양한 아키텍처를 갖고 있으며 MSAL은 범용 직렬화 메커니즘을 구현할 수 없습니다. 예를 들어 웹 사이트가 토큰을 Redis 캐시에 저장하거나 데스크톱 앱이 토큰을 암호화된 파일에 저장할 수 있습니다. 따라서 기본적으로 직렬화가 제공되지 않습니다. .NET 데스크톱 또는 .NET Core에서 영구 토큰 캐시 애플리케이션을 사용하려면 직렬화를 사용자 지정합니다.

다음 클래스 및 인터페이스는 토큰 캐시 직렬화에 사용됩니다.

- `ITokenCache`: 토큰 캐시 serialization 요청을 구독하는 이벤트, 그리고 캐시를 다양한 형식(ADAL v3.0, MSAL 2.x, MSAL 3.x = ADAL v5.0)으로 직렬화하거나 역직렬화하는 메서드를 정의합니다.
- `TokenCacheCallback`은 직렬화를 처리할 수 있도록 이벤트에 전달되는 콜백입니다. `TokenCacheNotificationArgs` 형식의 인수를 사용하여 호출됩니다.
- `TokenCacheNotificationArgs`는 토큰을 사용할 수 있는 사용자에게 애플리케이션 `ClientId`와 참조를 제공하기만 합니다.

  ![클래스 다이어그램](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> 사용자가 애플리케이션의 `UserTokenCache` 및 `AppTokenCache` 속성을 호출하면 MSAL.NET은 사용자 대신 토큰 캐시를 만들고 사용자에게 `IToken` 캐시를 제공합니다. 사용자가 인터페이스를 직접 구현할 필요가 없습니다. 사용자는 사용자 지정 토큰 캐시 직렬화를 구현할 때 다음과 같은 일만 하면 됩니다.
> - `BeforeAccess` 및`AfterAccess` "이벤트"(또는 해당 비동기 버전)에 대응합니다. `BeforeAccess` 대리자는 캐시를 역직렬화하는 역할을 담당하고, `AfterAccess`는 캐시를 직렬화하는 역할을 담당합니다.
> - 이러한 이벤트의 일부는 Blob을 저장하거나 로드하며, Blob은 이벤트 인수를 통해 사용자가 원하는 스토리지에 전달됩니다.

사용자가 작성하려는 토큰 캐시 직렬화가 [공용 클라이언트 애플리케이션](msal-client-applications.md)(데스크톱)을 위한 것인지 아니면 [기밀 클라이언트 애플리케이션](msal-client-applications.md)(웹앱/웹 API, 디먼 앱)을 위한 것인지에 따라 전략이 달라집니다.

### <a name="custom-token-cache-for-a-web-app-or-web-api-confidential-client-application"></a>웹앱이나 웹 API의 사용자 지정 토큰 캐시(기밀 클라이언트 애플리케이션)

웹앱이나 웹 API에서 캐시는 세션, Redis 캐시, SQL 데이터베이스 또는 Cosmos DB 데이터베이스를 사용할 수 있습니다. 웹앱 또는 웹 API에서 계정당 하나의 토큰 캐시를 유지합니다. 
- 웹앱의 경우 계정 ID로 토큰 캐시 키를 지정해야 합니다.
- 웹 API의 경우, API를 호출하는 데 사용되는 토큰 해시로 계정 키를 지정해야 합니다.

토큰 캐시 직렬 변환기의 예제는 [Microsoft.Identity.Web/TokenCacheProviders](https://github.com/AzureAD/microsoft-identity-web/tree/master/src/Microsoft.Identity.Web/TokenCacheProviders)에서 제공됩니다.

### <a name="custom-token-cache-for-a-desktop-or-mobile-app-public-client-application"></a>데스크톱 또는 모바일 앱(퍼블릭 클라이언트 애플리케이션)의 사용자 지정 토큰 캐시

MSAL.NET v2.x부터 공용 클라이언트의 토큰 캐시를 직렬화하는 여러 가지 옵션이 제공됩니다. 캐시를 MSAL.NET 형식으로만 직렬화할 수 있습니다(MSAL과 플랫폼에서 통합 형식 캐시를 공통적으로 사용).  ADAL V3의 [레거시](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) 토큰 캐시 직렬화를 지원할 수도 있습니다.

ADAL.NET 3.x, ADAL.NET 5.x 및 MSAL.NET 간에 Single Sign-On을 공유하도록 토큰 캐시 직렬화를 사용자 지정하는 방법은 [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) 샘플에 설명되어 있습니다.

> [!Note]
> MSAL.NET 1.1.4-미리 보기 토큰 캐시 형식은 더 이상 MSAL 2.x에서 지원되지 않습니다. MSAL.NET 1.x를 활용하는 애플리케이션이 있다면 사용자가 다시 로그인해야 합니다. 또는 ADAL 4.x(및 3.x)에서 마이그레이션할 수 있습니다.

#### <a name="simple-token-cache-serialization-msal-only"></a>간단한 토큰 캐시 직렬화(MSAL만 해당)

아래는 데스크톱 애플리케이션의 토큰 캐시를 사용자 지정 직렬화하는 간단한 예입니다. 여기서 사용자 토큰 캐시는 애플리케이션과 동일한 폴더에 들어 있는 파일입니다.

애플리케이션을 빌드한 후, `TokenCacheHelper.EnableSerialization()` 메서드를 호출하고 `UserTokenCache` 애플리케이션을 전달하여 직렬화를 사용하도록 설정합니다.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

`TokenCacheHelper` 도우미 클래스는 다음과 같이 정의됩니다.

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache. Note that this could be something different for instance for MSIX applications:
  /// private static readonly string CacheFilePath =
  /// $"{Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData)}\{AppName}\msalcache.bin";
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

퍼블릭 클라이언트 애플리케이션(Windows, Mac 및 Linux에서 실행되는 데스크톱 애플리케이션용)을 위한 제품 품질 토큰 캐시 파일 기반 직렬 변환기는 [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) 오픈 소스 라이브러리에서 사용할 수 있습니다. 다음 NuGet 패키지에서 이 미리 보기를 애플리케이션에 포함할 수 있습니다. [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>이중 토큰 캐시 직렬화(MSAL 통합 캐시 및 ADAL v3)

두 토큰 캐시 직렬화를 모두 통합 캐시 형식(ADAL.NET 4.x, MSAL.NET 2.x 및 같은 세대/이전 세대/같은 플랫폼의 기타 MSAL에 공통적으로 적용)으로 구현하려면 다음 코드를 잘 살펴보세요.

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

이번에는 도우미 클래스가 다음과 같이 정의됩니다.

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Enables the serialization of the token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache tokenCache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

---

## <a name="next-steps"></a>다음 단계

다음 샘플은 토큰 캐시 직렬화를 보여줍니다.

| 샘플 | 플랫폼 | 설명|
| ------ | -------- | ----------- |
|[active-directory-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | 데스크톱(WPF) | Microsoft Graph API를 호출하는 Windows 데스크톱 .NET(WPF) 애플리케이션 ![다이어그램에서 토큰을 대화형으로 획득하여 Azure AD로 이동하고 Microsoft Graph로 이동하는 데스크톱 앱 WPF TodoListClient를 사용하는 토폴로지를 보여 줍니다.](media/msal-net-token-cache-serialization/topology.png)|
|[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | 데스크톱(콘솔) | Azure AD v1.0 애플리케이션(ADAL.NET 사용)을 Microsoft ID 플랫폼 애플리케이션(MSAL.NET 사용)으로 마이그레이션하는 방법을 보여 주는 Visual Studio 솔루션 세트입니다. 특히 [토큰 캐시 마이그레이션](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md) 및 [기밀 클라이언트 토큰 캐시](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache)를 참조하세요. |
[ms-identity-aspnet-webapp-openidconnect](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) | ASP.NET(net472) | (MSAL.NET를 사용하는) ASP.NET MVC 애플리케이션에서 토큰 캐시 serialization의 예제입니다. 특정 [MsalAppBuilder](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs)를 참조하세요.
