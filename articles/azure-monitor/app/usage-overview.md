---
title: Application Insights를 사용하여 사용 현황 분석 | Azure Monitor
description: 어떤 사용자가 앱으로 어떤 작업을 수행하는지 이해합니다.
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 67739c94afeb841450eb465fe5cd5259093e2e78
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566454"
---
# <a name="usage-analysis-with-application-insights"></a>Application Insights를 사용하여 사용량 분석

가장 인기 있는 웹 또는 모바일 앱의 기능은 무엇인가요? 사용자가 앱으로 이러한 목표를 달성하고 있나요? 특정 지점에서 나갔다가 나중에 돌아오나요?  [Application Insights](./app-insights-overview.md)는 사람들의 앱 사용 방식을 잘 이해할 수 있도록 도와줍니다. 앱을 업데이트할 때마다 사용자들에게 얼마나 적절한지 평가할 수 있습니다. 이러한 지식을 바탕으로 다음 개발 주기에 대해 데이터 중심의 결정을 내릴 수 있습니다.


## <a name="send-telemetry-from-your-app"></a>앱에서 원격 분석 보내기

앱 서버 코드와 웹 페이지에 모두 Application Insights를 설치하여 최상의 환경을 얻습니다. 앱의 클라이언트 및 서버 구성 요소는 분석을 위해 Azure Portal로 원격 분석을 다시 보냅니다.

1. **서버 코드:** [ASP.NET](./asp-net.md), [Azure](./app-insights-overview.md), [Java](./java-in-process-agent.md), [Node.js](./nodejs.md) 또는 [기타](./platforms.md) 앱에 적합한 모듈을 설치합니다.

    * *서버 코드를 설치하지 않으려면 [Azure Application Insights 리소스를 만들기만](./create-new-resource.md) 하면 됩니다.*

2. **웹 페이지 코드:** ``</head>``를 닫기 전에 다음 스크립트를 웹 페이지에 추가합니다. 계측 키를 Application Insights 리소스에 대한 적절한 값으로 바꿉니다.
    
    현재 코드 조각(아래에 나열됨)은 버전 '5'이며 이 버전은 코드 조각에서 sv:"#"으로 인코딩됩니다. [현재 버전은 GitHub에서도 이용할 수 있습니다](https://go.microsoft.com/fwlink/?linkid=2156318).

    ```html
    <script type="text/javascript">
    !function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
    src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
    // name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
    // ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
    // useXhr: 1, // Use XHR instead of fetch to report failures (if available),
    crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
    // onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
    cfg: { // Application Insights Configuration
      instrumentationKey:"INSTRUMENTATION_KEY"
    }});
    </script>
    ```

    웹 사이트 모니터링을 위한 고급 구성에 대해 자세히 알아보려면 [JavaScript SDK 참조 문서](./javascript.md)를 확인하세요.

3. **모바일 앱 코드:** App Center SDK를 사용하여 앱에서 이벤트를 수집한 다음, 분석을 위해 [이 가이드에 따라](../app/mobile-center-quickstart.md) 이러한 이벤트의 복사본을 Application Insights로 보냅니다.

4. **원격 분석 가져오기:** 몇 분 동안 디버그 모드에서 프로젝트를 실행한 다음, Application Insights의 개요 창에서 결과를 찾습니다.

    앱을 게시하여 앱의 성능을 모니터링하고 사용자가 앱으로 수행하는 작업을 확인합니다.

## <a name="explore-usage-demographics-and-statistics"></a>사용 현황 인구 통계 및 통계 탐색
사람들이 사용자의 앱을 사용하는 경우, 가장 큰 관심을 갖는 페이지, 사용자가 있는 위치 및 사용하는 브라우저 및 운영 체제를 알아봅니다. 

사용자 및 세션 보고서는 페이지 또는 사용자 지정 이벤트를 기준으로 데이터를 필터링하고 위치, 환경 및 페이지 등의 속성으로 나눕니다. 필터를 직접 추가할 수도 있습니다.

:::image type="content" source="./media/usage-overview/users.png" alt-text="막대형 차트가 있는 사용자 탭을 보여 주는 화면 캡처" lightbox="./media/usage-overview/users.png":::

오른쪽의 자세한 정보에는 데이터 집합에서 주목할 만한 패턴이 나와 있습니다.  

* **사용자** 보고서는 선택한 기간 내에 페이지에 액세스하는 고유한 사용자 수를 계산합니다. 웹앱의 경우 쿠키를 사용하여 사용자 수가 계산됩니다. 누군가가 다른 브라우저 또는 클라이언트 컴퓨터를 사용하여 사이트에 액세스하는 경우 두 번 이상 계산됩니다.
* **세션** 보고서는 사이트에 액세스하는 사용자 세션의 수를 계산합니다. 세션은 30분 이상의 비활동 기간마다 종료되는 사용자의 활동 기간입니다.

[사용자, 세션 및 이벤트 도구에 대한 추가 정보](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>재방문 주기 - 다시 찾아온 사용자는 몇 명이나 되나요?

재방문 주기는 특정 시간 버킷 동안 일부 비즈니스 작업을 수행한 사용자의 코호트를 기준으로 사용자가 해당 앱을 다시 사용하는 빈도를 이해하는 데 도움이 됩니다. 

- 사용자가 다시 찾아오게 만드는 특정 기능 이해 
- 실제 사용자 데이터에 따라 가설 세우기 
- 재방문 주기가 제품에서 문제가 되는지 여부 확인 

:::image type="content" source="./media/usage-overview/retention.png" alt-text="사용자가 앱을 사용하기 위해 돌아오는 빈도에 대한 정보를 표시하는 재방문 주기 통합 문서를 보여 주는 화면 캡처" lightbox="./media/usage-overview/retention.png":::

상단의 재방문 주기 컨트롤을 사용하여 재방문 주기를 계산할 특정 이벤트 및 시간 범위를 정의할 수 있습니다. 중간에 표시되는 그래프는 지정된 시간 범위별로 전반적인 재방문 주기 비율을 시각적으로 보여 줍니다. 하단의 그래프는 지정된 기간 내의 개별 재방문 주기를 나타냅니다. 이 수준의 세부 정보를 사용하면 사용자가 수행하는 작업과 다시 방문한 사용자에게 영향을 미칠 수 있는 요인을 좀 더 자세히 이해할 수 있습니다.  

[재방문 주기 통합 문서에 대한 추가 정보](usage-retention.md)

## <a name="custom-business-events"></a>사용자 지정 비즈니스 이벤트

사용자가 앱으로 수행하는 작업을 명확하게 이해하려는 경우 사용자 지정 이벤트를 로깅하는 코드 줄을 삽입하면 유용합니다. 이러한 이벤트는 특정 단추 클릭과 같은 자세한 사용자 작업부터 구매하기나 게임에서 이기기 등과 같은 좀 더 중요한 비즈니스 이벤트에 이르기까지 모든 사항을 추적할 수 있습니다.

[클릭 분석 자동 수집 플러그 인](javascript-click-analytics-plugin.md)을 사용하여 사용자 지정 이벤트를 수집할 수도 있습니다.

경우에 따라 페이지 보기에 유용한 이벤트가 표시될 수 있지만 일반적으로는 그렇지 않습니다. 사용자는 제품 페이지를 열기만 하고 제품을 구입하지 않을 수 있습니다. 

특정 비즈니스 이벤트를 사용하여 사이트를 통한 사용자의 진행 상황을 차트로 나타낼 수 있습니다. 다양한 옵션에 대한 사용자의 선호도를 알아내고 앱 사용을 중지하거나 문제가 있는 위치를 확인합니다. 이러한 지식을 바탕으로 개발 백로그에서 우선 순위를 결정할 수 있습니다.

이벤트는 앱의 클라이언트 쪽에서 로깅될 수 있습니다.

```JavaScript
    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

또는 서버 쪽에서 다음을 수행할 수 있습니다.

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

포털에서 검사할 때 이벤트를 필터링하거나 분할할 수 있도록 이러한 이벤트에 속성 값을 추가할 수 있습니다. 또한 익명 사용자 ID와 같은 표준 속성 세트가 각 이벤트에 추가되므로 개별 사용자의 활동 시퀀스를 추적할 수 있습니다.

[사용자 지정 이벤트](./api-custom-events-metrics.md#trackevent) 및 [속성](./api-custom-events-metrics.md#properties)에 대해 자세히 알아보세요.

### <a name="slice-and-dice-events"></a>이벤트 분석 및 분할

사용자, 세션 및 이벤트 도구에서 사용자, 이벤트 이름 및 속성별로 사용자 지정 이벤트를 분석 및 분할할 수 있습니다.

:::image type="content" source="./media/usage-overview/events.png" alt-text="AnalyticsItemsOperation으로 필터링되고 AppID로 분할된 이벤트 탭을 보여 주는 화면 캡처" lightbox="./media/usage-overview/events.png":::
  
## <a name="design-the-telemetry-with-the-app"></a>앱을 사용하여 원격 분석 디자인

앱의 각 기능을 디자인할 때 사용자를 대상으로 앱의 성공 여부를 측정하는 방법을 고려합니다. 기록해야 하는 비즈니스 이벤트를 결정하고 해당 이벤트에 대한 추적 호출을 처음부터 앱에 코딩합니다.

## <a name="a--b-testing"></a>A | B 테스트
기능의 어느 변형이 보다 성공적인지 알 수 없는 경우, 다른 사용자가 각각 접근할 수 있도록 하여 둘 다 릴리스합니다. 각각의 성공 여부를 측정한 다음 통합 버전으로 이동합니다.

이 기술의 경우 사용자 앱의 각 버전이 전송한 모든 원격 분석에 고유 속성 값을 연결합니다. 활성화된 TelemetryContext에서 속성을 정의하여 수행할 수 있습니다. 이러한 기본 속성은 사용자 지정 메시지뿐 아니라 표준 원격 분석도 애플리케이션이 전송하는 모든 원격 분석 메시지에 추가됩니다.

Application Insights 포털에서 속성 값에 대해 데이터를 필터링하고 분할하여 다른 버전과 비교할 수 있습니다.

이렇게 하려면 [원격 분석 이니셜라이저를 설정](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)합니다.

**ASP.NET 앱**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
            }
    }
```

Global.asax.cs 같은 웹앱 이니셜라이저에서 다음이 적용됩니다.

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**ASP.NET Core 앱**

> [!NOTE]
> `ApplicationInsights.config` 또는 `TelemetryConfiguration.Active`를 사용하여 이니셜라이저를 추가하는 것은 ASP.NET Core 애플리케이션에 적합하지 않습니다. 

[ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) 애플리케이션의 경우 새 `TelemetryInitializer` 추가 작업은 아래와 같이 종속성 주입 컨테이너에 추가하여 수행됩니다. 이 작업은 `Startup.cs` 클래스의 `ConfigureServices` 메서드에서 수행됩니다.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

새로운 모든 TelemetryClient는 사용자가 지정하는 속성 값을 자동으로 추가합니다. 개별 원격 분석 이벤트는 기본값을 재정의할 수 있습니다.

## <a name="next-steps"></a>다음 단계
   - [사용자, 세션, 이벤트](usage-segmentation.md)
   - [깔때기](usage-funnels.md)
   - [보존](usage-retention.md)
   - [사용자 흐름](usage-flows.md)
   - [통합 문서](../visualize/workbooks-overview.md)
