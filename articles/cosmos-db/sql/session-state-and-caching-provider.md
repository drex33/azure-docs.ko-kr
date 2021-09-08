---
title: Azure Cosmos DB를 ASP.NET 세션 상태 및 캐싱 공급자로 사용
description: Azure Cosmos DB를 ASP.NET 세션 상태 및 캐싱 공급자로 사용하는 방법 알아보기
author: StefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/15/2021
ms.openlocfilehash: 9a7b7ad15d1d4850e910010e4fdc195e474d10af
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116944"
---
# <a name="use-azure-cosmos-db-as-an-aspnet-session-state-and-caching-provider"></a>Azure Cosmos DB를 ASP.NET 세션 상태 및 캐싱 공급자로 사용
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB 세션 및 캐시 공급자를 사용하면 Azure Cosmos DB를 사용하고 세션 상태 데이터를 저장하고 애플리케이션 내에서 분산 캐시로 사용하기 위한 짧은 대기 시간 및 전역 확장 기능을 활용할 수 있습니다.

## <a name="what-is-session-state"></a>세션 상태란 무엇인가요?

[세션 상태](/aspnet/core/fundamentals/app-state?view=aspnetcore-5.0#configure-session-state&preserve-view=true)는 동일한 브라우저 내에서 일정 기간 동안 웹 애플리케이션을 탐색하는 사용자를 추적하는 사용자 데이터입니다. 세션 상태가 만료되며 브라우저 간에 확장되지 않는 특정 브라우저의 상호 작용으로 제한됩니다. 임시 데이터로 간주되며 존재하지 않으면 애플리케이션이 중단되지 않습니다. 그러나 존재하는 경우 웹 애플리케이션이 동일한 사용자에 대한 모든 브라우저 요청에서 이를 가져올 필요가 없기 때문에 사용자에게 더 빠른 환경을 제공합니다.

이는 종종 어떤 경우에는 현재 웹 서버의 외부에 있을 수 있고 여러 웹 서버에서 동일한 브라우저의 부하 분산 요청을 사용하여 더 높은 확장성을 달성할 수 있는 일부 스토리지 메커니즘에 의해 지원됩니다.

가장 간단한 세션 상태 공급자는 로컬 웹 서버 메모리에만 데이터를 저장하고 애플리케이션이 [애플리케이션 요청 라우팅](/iis/extensions/planning-for-arr/using-the-application-request-routing-module)을 사용하도록 요구하는 메모리 내 공급자입니다. 이렇게 하면 브라우저 세션이 특정 웹 서버에 고정됩니다(해당 브라우저에 대한 모든 요청은 항상 동일한 웹 서버에 있어야 함). 공급자는 간단한 시나리오에서 잘 작동하지만 고정 요구 사항은 웹 애플리케이션이 확장될 때 부하 분산 문제를 발생시킬 수 있습니다.

세션이 고정될 필요 없이 여러 웹 서버에서 읽고 액세스할 수 있는 방식으로 세션 데이터를 저장할 수 있고 더 높은 확장을 가능하게 하는 많은 외부 스토리지 공급자를 사용할 수 있습니다.

## <a name="session-state-scenarios"></a>세션 상태 시나리오

Cosmos DB는 [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-standard.md)를 사용하는 확장 패키지 [Microsoft.Extensions.Caching.Cosmos](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Cosmos)를 통해 세션 상태 공급자로 사용할 수 있습니다. 이는 키가 세션 식별자인 키/값 방식을 기반으로 컨테이너를 효과적인 세션 스토리지로 사용합니다.

패키지가 추가되면 시작 프로세스의 일부로 `AddCosmosCache`를 사용할 수 있습니다(services.AddSession 및 app.UseSession은 모든 세션 상태 공급자에 필요한 [일반적인 초기화](/aspnet/core/fundamentals/app-state?view=aspnetcore-5.0#configure-session-stat&preserve-view=true) 단계임).

```csharp
public void ConfigureServices(IServiceCollection services)
{
  /* Other service configurations */
  services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
  {
      CosmosClientBuilder clientBuilder = new CosmosClientBuilder("myConnectionString")
        .WithApplicationRegion("West US");
      cacheOptions.ContainerName = "myContainer";
      cacheOptions.DatabaseName = "myDatabase";
      cacheOptions.ClientBuilder = clientBuilder;
      /* Creates the container if it does not exist */
      cacheOptions.CreateIfNotExists = true; 
  });

  services.AddSession(options =>
  {
      options.IdleTimeout = TimeSpan.FromSeconds(3600);
      options.Cookie.IsEssential = true;
  });
  /* Other service configurations */
}

public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    /* Other configurations */

    app.UseSession();

    /* app.UseEndpoints and other configurations */
}
```

세션 상태를 저장할 데이터베이스와 컨테이너를 지정하고, 없는 경우 만들 수도 있습니다.

`CosmosClientBuilder`를 사용하여 SDK 클라이언트 구성을 사용자 지정할 수 있거나 애플리케이션이 이미 Cosmos DB의 다른 작업에 `CosmosClient`를 사용하고 있는 경우 이를 공급자에 삽입할 수도 있습니다.

```csharp
services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
{
    cacheOptions.ContainerName = "myContainer";
    cacheOptions.DatabaseName = "myDatabase";
    cacheOptions.CosmosClient = preExistingClient;
    /* Creates the container if it does not exist */
    cacheOptions.CreateIfNotExists = true; 
});
```

그런 다음 다른 공급자와 마찬가지로 ASP.NET Core 세션을 사용하고 HttpContext.Session 개체를 사용할 수 있습니다. 항상 [ASP.NET 권장 사항](/aspnet/core/fundamentals/app-state?view=aspnetcore-5.0#load-session-state-asynchronously&preserve-view=true)에 따라 세션 정보를 비동기식으로 로드해야 합니다.

##  <a name="distributed-cache-scenarios"></a>분산 캐시 시나리오

Cosmos DB 공급자는 [분산 캐시 공급자 역할을 하는 IDistributedCache 인터페이스](/aspnet/core/performance/caching/distributed?view=aspnetcore-5.0&preserve-view=true)를 구현하므로 성능과 분산 세션 상태 공급자가 필요한 웹 애플리케이션뿐만 아니라 분산 캐시가 필요한 모든 애플리케이션에도 사용할 수 있습니다.

분산 캐시는 캐시된 데이터를 공유할 수 있는 독립 인스턴스를 제공하기 위해 데이터 일관성이 필요합니다. Cosmos DB 공급자를 사용하는 경우 다음을 수행할 수 있습니다.

- [애플리케이션 요청 라우팅](/iis/extensions/planning-for-arr/using-the-application-request-routing-module)을 사용하도록 설정하고 요청을 특정 인스턴스에 고정할 수 있는 경우 **세션 일관성** 상태에서 Cosmos DB 계정을 사용합니다.
- 요청 고정을 요구하지 않고 **제한된 부실 또는 강력한 일관성** 상태에서 Cosmos DB 계정을 사용합니다. 이는 인스턴스 전체의 부하 분산 측면에서 가장 큰 규모를 제공합니다.

Cosmos DB 공급자를 분산 캐시로 사용하려면 `services.AddCosmosCache` 호출로 `ConfiguredService`에 등록해야 합니다. 이 작업이 완료되면 애플리케이션의 모든 생성자가 `IDistributedCache`을 참조하여 캐시를 요청할 수 있으며 사용할 [종속성 주입](/dotnet/core/extensions/dependency-injection)에 의해 주입된 인스턴스를 수신합니다.

```csharp
public class MyBusinessClass
{
    private readonly IDistributedCache cache;

    public MyBusinessClass(IDistributedCache cache)
    {
        this.cache = cache;
    }
    
    public async Task SomeOperationAsync()
    {
        string someCachedValue = await this.cache.GetStringAsync("someKey");
        /* Use the cache */
    }
}
```

## <a name="troubleshooting-and-diagnosing"></a>문제 해결 및 진단
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Cosmos DB 공급자는 기본적으로 .NET SDK를 사용하므로 기존의 모든 [성능 가이드라인](performance-tips-dotnet-sdk-v3-sql.md) 및 [문제 해결 가이드](troubleshoot-dot-net-sdk.md)가 잠재적인 문제를 이해하는 데 적용됩니다. IDistributedCache API를 통해 노출될 수 없기 때문에 기본 Cosmos DB 작업에서 진단에 액세스하는 고유한 방법이 있습니다.

선택적 진단 대리자를 등록하면 긴 대기 시간과 같은 문제를 해결하기 위해 진단을 캡처하고 조건부로 기록할 수 있습니다.

```csharp
void captureDiagnostics(CosmosDiagnostics diagnostics)
{
    if (diagnostics.GetClientElapsedTime() > SomePredefinedThresholdTime)
    {
        Console.WriteLine(diagnostics.ToString());
    }
}

services.AddCosmosCache((CosmosCacheOptions cacheOptions) =>
{
    cacheOptions.DiagnosticsHandler = captureDiagnostics;
    /* other options */
});
```

## <a name="next-steps"></a>다음 단계
- Azure Cosmos DB 세션 및 캐시 공급자에 대한 자세한 내용은 [GitHub의 소스 코드](https://github.com/Azure/Microsoft.Extensions.Caching.Cosmos/)를 참조하세요.
- 샘플 ASP.NET Core 웹 애플리케이션 탐색을 통해 Azure Cosmos DB 세션 및 캐시 공급자를 [사용해 보세요](https://github.com/Azure/Microsoft.Extensions.Caching.Cosmos/tree/master/sample).
- .NET의 [분산 캐시](/aspnet/core/performance/caching/distributed?view=aspnetcore-5.0&preserve-view=true)에 대해 자세히 알아봅니다.
