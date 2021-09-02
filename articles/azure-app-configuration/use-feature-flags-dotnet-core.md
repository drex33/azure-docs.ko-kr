---
title: .NET Core 앱에서 기능 플래그 사용에 대한 자습서 | Microsoft Docs
description: 이 자습서에서는 .NET Core 앱에서 기능 플래그를 구현하는 방법을 알아봅니다.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/17/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 5f15d016c890ec214fe6b6a3d8af3138d202408b
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122970341"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>자습서: ASP.NET Core 앱에서 기능 플래그 사용

.NET Core 기능 관리 라이브러리는 .NET 또는 ASP.NET Core 애플리케이션에서 기능 플래그를 구현할 수 있도록 자연스러운 지원을 제공합니다. 이러한 라이브러리를 사용하면 기능 플래그를 코드에 선언적으로 추가할 수 있으므로 `if` 문을 사용하여 기능을 사용하거나 사용하지 않도록 설정하는 코드를 수동으로 작성할 필요가 없습니다.

또한 기능 관리 라이브러리는 배후 상황의 기능 플래그 수명 주기를 관리합니다. 예를 들어 라이브러리에서 플래그 상태를 새로 고치고 캐싱하거나 요청 호출 중에 플래그 상태의 변경이 불가능하도록 보장합니다. 뿐만 아니라 ASP.NET Core 라이브러리는 MVC 컨트롤러 작업, 보기, 경로 및 미들웨어를 포함하여 기본 통합을 제공합니다.

[ASP.NET Core 앱에 기능 플래그 추가 빠른 시작](./quickstart-feature-flag-aspnet-core.md)은 ASP.NET Core 애플리케이션에서 기능 플래그를 사용하는 방법에 대한 간단한 예제를 보여 줍니다. 이 자습서에서는 기능 관리 라이브러리의 추가 설정 옵션 및 기능을 보여 줍니다. 빠른 시작에서 만든 샘플 앱을 사용하여 이 자습서에 표시된 샘플 코드를 사용해 볼 수 있습니다. 

ASP.NET Core 기능 관리 API 참조 설명서는 [Microsoft.FeatureManagement 네임스페이스](/dotnet/api/microsoft.featuremanagement)를 참조하세요.

이 자습서에서는 다음 작업 방법을 배웁니다.

> [!div class="checklist"]
> * 애플리케이션의 핵심 부분에 기능 가용성을 제어하는 기능 플래그를 추가합니다.
> * App Configuration과 통합하여 기능 플래그를 관리하는 데 사용합니다.

## <a name="set-up-feature-management"></a>기능 관리 설정

.NET Core 기능 관리자에 액세스하려면 `Microsoft.FeatureManagement.AspNetCore` NuGet 패키지에 대한 참조가 앱에 있어야 합니다.

.NET Core 기능 관리자는 프레임워크의 기본 구성 시스템에서 구성됩니다. 따라서 로컬 *appsettings.json* 파일 또는 환경 변수를 포함하여 .NET Core에서 지원하는 구성 소스를 사용하여 애플리케이션의 기능 플래그 설정을 정의할 수 있습니다.

기능 관리자는 기본적으로 .NET Core 구성 데이터의 `"FeatureManagement"` 섹션에서 기능 플래그 구성을 검색합니다. 기본 구성 위치를 사용하려면 **Start** 클래스의 **ConfigureServices** 메서드에 전달된 **IServiceCollection** 의 [AddFeatureManagement](/dotnet/api/microsoft.featuremanagement.servicecollectionextensions.addfeaturemanagement) 메서드를 호출합니다.


```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement();
    }
}
```

[Configuration.GetSection](/dotnet/api/microsoft.web.administration.configuration.getsection)을 호출하고 원하는 섹션의 이름을 전달하여 다른 구성 섹션에서 기능 관리 구성을 검색하도록 지정할 수 있습니다. 다음 예제는 `"MyFeatureFlags"`라는 다른 섹션에서 읽으라고 기능 관리자에게 지시합니다.

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement(Configuration.GetSection("MyFeatureFlags"));
    }
}
```


기능 플래그에서 필터를 사용하는 경우 [Microsoft.FeatureManagement.FeatureFilters](/dotnet/api/microsoft.featuremanagement.featurefilters) 네임스페이스를 포함하고, 메서드의 제네릭 형식으로 사용할 필터의 형식 이름을 지정하는 [AddFeatureFilters](/dotnet/api/microsoft.featuremanagement.ifeaturemanagementbuilder.addfeaturefilter)에 호출을 추가해야 합니다. 기능 필터를 사용하여 기능을 동적으로 사용하거나 사용하지 않도록 설정하는 방법에 대한 자세한 내용은 [지정된 대상 그룹에 단계적 기능 롤아웃 사용](./howto-targetingfilter-aspnet-core.md)을 참조하세요.

다음 예제에서는 `PercentageFilter`라는 기본 제공 기능 필터를 사용하는 방법을 보여줍니다.



```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

기능 플래그를 애플리케이션에 하드 코딩하는 대신, 기능 플래그를 애플리케이션 외부에 유지하고 별도로 관리하는 것이 좋습니다. 이렇게 하면 언제든지 플래그 상태를 수정하고 해당 변경 내용을 애플리케이션에 즉시 적용할 수 있습니다. Azure App Configuration 서비스는 모든 기능 플래그를 관리하기 위한 전용 포털 UI를 제공합니다. 또한 Azure App Configuration 서비스는 .NET Core 클라이언트 라이브러리를 통해 기능 플래그를 애플리케이션에 직접 제공합니다.

ASP.NET Core 애플리케이션을 App Configuration에 연결하는 가장 쉬운 방법은 `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet 패키지에 포함된 구성 공급자를 사용하는 것입니다. 패키지에 대한 참조가 포함되면 다음 단계에 따라 이 NuGet 패키지를 사용합니다.

1. *Program.cs* 파일을 열고 다음 코드를 추가합니다.
    > [!IMPORTANT]
    > .NET Core 3.x에 `CreateHostBuilder`는 `CreateWebHostBuilder`를 대체합니다. 사용자 환경에 따라 올바른 구문을 선택합니다.

    ### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
            }).UseStartup<Startup>());
    ```
        
    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration(config =>
                    {
                        var settings = config.Build();
                        config.AddAzureAppConfiguration(options =>
                            options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                    }).UseStartup<Startup>();
    ```
    ---

2. *Startup.cs* 를 열고, `UseAzureAppConfiguration`이라는 기본 제공 미들웨어를 추가하도록 `Configure` 및 `ConfigureServices` 메서드를 업데이트합니다. 이 미들웨어를 사용하면 ASP.NET Core 웹앱이 요청을 계속 수신하는 동안 반복된 간격으로 기능 플래그 값을 새로 고칠 수 있습니다.



    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseAzureAppConfiguration();
    }
    ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddAzureAppConfiguration();
   }
   ```
   
일반적인 시나리오에서 애플리케이션의 다양한 기능을 배포하고 사용하도록 설정하면 기능 플래그 값이 정기적으로 업데이트됩니다. 기본적으로 기능 플래그 값은 30초 동안 캐시되므로 캐시된 값이 만료될 때까지 미들웨어가 요청을 수신할 때 트리거되는 새로 고침 작업은 값을 업데이트하지 않습니다. 다음 코드는 **UseFeatureFlags** 에 대한 호출에서 [CacheExpirationInterval](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.featuremanagement.featureflagoptions.cacheexpirationinterval)을 설정하여 캐시 만료 시간 또는 폴링 간격을 5분으로 변경하는 방법을 보여 줍니다.


    
```csharp
config.AddAzureAppConfiguration(options =>
    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags(featureFlagOptions => {
        featureFlagOptions.CacheExpirationInterval = TimeSpan.FromMinutes(5);
    }));
});
```


## <a name="feature-flag-declaration"></a>기능 플래그 선언

각 기능 플래그 선언에는 이름 및 기능의 상태가 *on*(즉, 값이 `True`인 경우)인지 여부를 평가하는 데 사용되는 하나 이상의 필터 목록으로 구성된 두 부분이 있습니다. 필터는 기능을 설정해야 하는 경우에 대한 조건을 정의합니다.

기능 플래그에 여러 필터가 있는 경우 그 중 한 필터가 기능을 사용하도록 설정해야 한다고 확인할 때까지 필터 목록이 순서대로 트래버스됩니다. 해당 시점에서 기능 플래그가 *on* 으로 간주되므로 나머지 필터 결과를 건너뜁니다. 기능을 사용하도록 설정해야 한다는 필터가 없으면 기능 플래그가 *off* 됩니다.

기능 관리자는 기능 플래그의 구성 원본으로 *appsettings.json* 을 지원합니다. 다음 예제에서는 JSON 파일에서 기능 플래그를 설정하는 방법을 보여줍니다.

```JSON
{"FeatureManagement": {
        "FeatureA": true, // Feature flag set to on
        "FeatureB": false, // Feature flag set to off
        "FeatureC": {
            "EnabledFor": [
                {
                    "Name": "Percentage",
                    "Parameters": {
                        "Value": 50
                    }
                }
            ]
        }
    }
}
```

규칙에 따라 이 JSON 문서의 `FeatureManagement` 섹션은 기능 플래그 설정에 사용됩니다. 앞의 예제에서는 `EnabledFor` 속성에 필터가 정의된 다음 세 가지 기능 플래그를 보여줍니다.

* `FeatureA`는 *on* 입니다.
* `FeatureB`는 *off* 입니다.
* `FeatureC`는 `Parameters` 속성을 사용하여 `Percentage`라는 필터를 지정합니다. `Percentage`는 구성 가능한 필터입니다. 이 예제에서 `Percentage`는 `FeatureC` 플래그가 *on* 이 될 50% 확률을 지정합니다. 기능 필터를 사용하는 방법에 대한 지침은 [기능 필터를 사용하여 조건부 기능 플래그 사용](./howto-feature-filters-aspnet-core.md)을 참조하세요.




## <a name="use-dependency-injection-to-access-ifeaturemanager"></a>종속성 주입을 사용하여 IFeatureManager에 액세스 

기능 플래그 값을 수동으로 확인하는 것과 같은 일부 작업의 경우 [IFeatureManager](/dotnet/api/microsoft.featuremanagement.ifeaturemanager?preserve-view=true&view=azure-dotnet-preview) 인스턴스를 가져와야 합니다. ASP.NET Core MVC에서 종속성 주입을 통해 `IFeatureManager` 기능 관리자에 액세스할 수 있습니다. 다음 예제에서는 `IFeatureManager` 형식의 인수를 컨트롤러에 대한 생성자의 시그니처에 추가합니다. 런타임에서는 참조를 자동으로 확인하고, 생성자를 호출할 때 인터페이스를 제공합니다. 이미 컨트롤러에 생성자의 종속성 주입 인수(예: `ILogger`)가 하나 이상 있는 애플리케이션 템플릿을 사용하는 경우 추가 인수로 `IFeatureManager`만 추가하면 됩니다.

### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)
    
```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```
    
### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

---

## <a name="feature-flag-references"></a>기능 플래그 참조

코드에서 참조할 수 있도록 기능 플래그를 문자열 변수로 정의합니다.

```csharp
public static class MyFeatureFlags
{
    public const string FeatureA = "FeatureA";
    public const string FeatureB = "FeatureB";
    public const string FeatureC = "FeatureC";
}
```

## <a name="feature-flag-checks"></a>기능 플래그 확인

기능 관리의 일반적인 패턴은 기능 플래그가 *on* 으로 설정되어 있는지 확인하고, 그렇다면 코드 섹션을 실행하는 것입니다. 다음은 그 예입니다.

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(MyFeatureFlags.FeatureA))
{
    // Run the following code
}
```

## <a name="controller-actions"></a>컨트롤러 작업

MVC 컨트롤러를 사용하면 `FeatureGate` 특성을 사용하여 전체 컨트롤러 클래스 또는 특정 작업을 사용하도록 설정할지 여부를 제어할 수 있습니다. 다음 `HomeController` 컨트롤러는 `FeatureA`가 *on* 으로 설정되어야만 컨트롤러 클래스에 포함된 작업을 실행할 수 있습니다.

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

다음 `Index` 작업은 `FeatureA`가 *on* 으로 설정되어야만 실행할 수 있습니다.

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

제어하는 기능 플래그가 *off* 로 설정되어 MVC 컨트롤러 또는 작업이 차단되면 등록된 [IDisabledFeaturesHandler](/dotnet/api/microsoft.featuremanagement.mvc.idisabledfeatureshandler?preserve-view=true&view=azure-dotnet-preview) 인터페이스가 호출됩니다. 기본 `IDisabledFeaturesHandler` 인터페이스는 응답 본문 없이 404 상태 코드를 클라이언트에 반환합니다.

## <a name="mvc-views"></a>MVC 보기

*Views* 디렉터리에서 *_ViewImports.cshtml* 을 열고, 다음과 같이 기능 관리자 태그 도우미를 추가합니다.

```html
@addTagHelper *, Microsoft.FeatureManagement.AspNetCore
```

MVC 보기에서 `<feature>` 태그를 사용하여 기능 플래그의 사용 여부에 따라 콘텐츠를 렌더링할 수 있습니다.

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

요구 사항이 충족되지 않을 경우 대체 콘텐츠를 표시하려면 `negate` 특성을 사용할 수 있습니다.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

목록의 임의 기능 또는 모든 기능을 사용할 수 있는 경우 기능 `<feature>` 태그를 사용하여 콘텐츠를 표시할 수도 있습니다.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>MVC 필터

MVC 필터를 기능 플래그의 상태에 따라 활성화되도록 설정할 수 있습니다. 이 기능은 [IAsyncActionFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncactionfilter)를 구현하는 필터로 제한됩니다. 다음 코드는 `ThirdPartyActionFilter`라는 MVC 필터를 추가합니다. 이 필터는 `FeatureA`가 사용되는 경우에만 MVC 파이프라인 내에서 트리거됩니다.  

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<ThirdPartyActionFilter>(MyFeatureFlags.FeatureA);
    });
}
```

## <a name="middleware"></a>미들웨어

또한 기능 플래그를 사용하여 조건에 따라 애플리케이션 분기 및 미들웨어를 추가할 수 있습니다. 다음은 `FeatureA`가 사용되는 경우에만 요청 파이프라인에 미들웨어 구성 요소를 삽입합니다.

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(MyFeatureFlags.FeatureA);
```

이 코드는 기능 플래그에 따라 전체 애플리케이션을 분기하는 더 일반적인 기능으로 빌드됩니다.

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 ASP.NET Core 애플리케이션에서 `Microsoft.FeatureManagement` 라이브러리를 사용하여 기능 플래그를 구현하는 방법을 알아보았습니다. ASP.NET Core 및 App Configuration의 기능 관리 지원에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [ASP.NET Core 기능 플래그 샘플 코드](./quickstart-feature-flag-aspnet-core.md)
* [Microsoft.FeatureManagement 설명서](/dotnet/api/microsoft.featuremanagement)
* [기능 플래그 관리](./manage-feature-flags.md)