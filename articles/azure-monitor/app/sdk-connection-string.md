---
title: Azure Application Insights의 연결 문자열 - Microsoft Docs
description: 연결 문자열 사용 방법.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.custom: devx-track-js, devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: 2b4a7ac88f92c830daecf943d6937946c4a89f82
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297448"
---
# <a name="connection-strings"></a>연결 문자열

## <a name="overview"></a>개요

연결 문자열은 Application Insight 사용자에게 단일 구성 설정을 제공하므로 여러 프록시 설정이 필요하지 않습니다. 모니터링 서비스로 데이터를 전송하려는 인트라넷 웹 서버, 소버린 또는 하이브리드 클라우드 환경에 매우 유용합니다.

키 값 쌍은 사용자가 각 AI(Application Insights) 서비스/제품에 대한 접두사 접미사 조합을 쉽게 정의할 수 있는 방법을 제공합니다.

> [!IMPORTANT]
> 연결 문자열과 계측 키를 모두 설정하지 않는 것이 좋습니다. 사용자가 두 가지 모두를 설정할 경우 마지막으로 설정한 항목이 우선 적용됩니다. 

> [!TIP]
> 계측 키보다 연결 문자열을 사용하는 것이 좋습니다.

## <a name="scenario-overview"></a>시나리오 개요 

이러한 상황을 시각화하는 고객 시나리오는 다음과 같습니다.

- 방화벽 예외 또는 프록시 리디렉션 

    인트라넷 웹 서버를 모니터링해야 하는 경우 이전 솔루션에서 고객에게 개별 서비스 엔드포인트를 구성에 추가하도록 요청했습니다. 자세한 내용은 [여기](../faq.yml#can-i-monitor-an-intranet-web-server-)를 참조하세요. 
    연결 문자열은 이 작업을 단일 설정으로 줄여 더 나은 대안을 제공합니다. 간단한 접두사, 접미사 수정은 모든 엔드포인트를 올바른 서비스로 자동 채우기 및 리디렉션할 수 있습니다. 

- 소버린 또는 하이브리드 클라우드 환경

    사용자는 정의된 [Azure Government 영역](../../azure-government/compare-azure-government-global-azure.md#application-insights)으로 데이터를 보낼 수 있습니다.
    연결 문자열을 사용하여 인트라넷 서버 또는 하이브리드 클라우드 설정에 대한 엔드포인트 설정을 정의할 수 있습니다. 

## <a name="getting-started"></a>시작

### <a name="finding-my-connection-string"></a>연결 문자열을 찾고 있나요?

연결 문자열이 Application Insights 리소스의 개요 블레이드에 표시됩니다.

![개요 블레이드의 연결 문자열](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>스키마

#### <a name="max-length"></a>최대 길이

연결의 지원되는 최대 길이는 4096자입니다.

#### <a name="key-value-pairs"></a>키-값 쌍

연결 문자열은 키-값 쌍으로 표시된 설정 목록으로 `key1=value1;key2=value2;key3=value3`과 같이 세미콜론으로 구분됩니다.

#### <a name="syntax"></a>구문

- `InstrumentationKey`(예: 00000000-0000-0000-0000-000000000000)  연결 문자열은 **필수** 필드입니다.
- `Authorization`(예: ikey) (이 설정은 현재 ikey 권한 부여만 지원하기 때문에 선택 사항입니다.)
- `EndpointSuffix`(예: applicationinsights.azure.cn) 엔드포인트 접미사를 설정하면 SDK에 연결할 Azure 클라우드를 알려줍니다. SDK는 개별 서비스에 대한 나머지 엔드포인트를 조합합니다.
- 명시적 엔드포인트.
  모든 서비스는 연결 문자열에서 명시적으로 재정의될 수 있습니다.
   - `IngestionEndpoint`(예: `https://dc.applicationinsights.azure.com`)
   - `LiveEndpoint`(예: `https://live.applicationinsights.azure.com`)
   - `ProfilerEndpoint`(예: `https://profiler.monitor.azure.com`)
   - `SnapshotEndpoint`(예: `https://snapshot.monitor.azure.com`)

#### <a name="endpoint-schema"></a>엔드포인트 스키마

`<prefix>.<suffix>`
- 접두사: 서비스를 정의합니다. 
- Suffix: 공통 도메인 이름을 정의합니다.

##### <a name="valid-suffixes"></a>유효한 접미사

유효한 접미사 목록은 다음과 같습니다. 
- applicationinsights.azure.cn
- applicationinsights.us


참조: [엔드포인트 수정이 필요한 지역](./custom-endpoints.md#regions-that-require-endpoint-modification)


##### <a name="valid-prefixes"></a>유효한 접두사

- [원격 분석 수집](./app-insights-overview.md): `dc`
- [라이브 메트릭](./live-stream.md): `live`
- [프로파일러](./profiler-overview.md): `profiler`
- [스냅샷](./snapshot-debugger.md): `snapshot`



## <a name="connection-string-examples"></a>연결 문자열 예제


### <a name="minimal-valid-connection-string"></a>유효한 최소 연결 문자열

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

이 예제에서는 계측 키만 설정되었습니다.

- 권한 부여 체계의 기본값은 "ikey"입니다. 
- 계측 키: 00000000-0000-0000-0000-000000000000
- 지역 서비스 URI는 [SDK 기본값](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs)을 기반으로 하며 공용 글로벌 Azure에 연결됩니다.
   - 수집: `https://dc.services.visualstudio.com/`
   - 라이브 메트릭: `https://rt.services.visualstudio.com/`
   - 프로파일러: `https://profiler.monitor.azure.com/`
   - 디버거: `https://snapshot.monitor.azure.com/`



### <a name="connection-string-with-endpoint-suffix"></a>엔드포인트 접미사를 사용하는 연결 문자열

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

이 예제에서 이 연결 문자열은 엔드포인트 접미사를 지정하고 SDK는 서비스 엔드포인트 형성합니다.

- 권한 부여 체계의 기본값은 "ikey"입니다. 
- 계측 키: 00000000-0000-0000-0000-000000000000
- 지역 서비스 URI는 제공된 엔드포인트 접미사를 기반으로 합니다. 
   - 수집: `https://dc.ai.contoso.com`
   - 라이브 메트릭: `https://live.ai.contoso.com`
   - 프로파일러: `https://profiler.ai.contoso.com`
   - 디버거: `https://snapshot.ai.contoso.com`  



### <a name="connection-string-with-explicit-endpoint-overrides"></a>명시적 엔드포인트 재정의를 포함하는 연결 문자열 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

이 예제에서 이 연결 문자열은 모든 서비스에 대한 명시적 재정의를 지정합니다. SDK는 수정 없이 제공되는 정확한 엔드포인트를 사용합니다.

- 권한 부여 체계의 기본값은 "ikey"입니다. 
- 계측 키: 00000000-0000-0000-0000-000000000000
- 지역 서비스 URI는 명시적 재정의 값을 기반으로 합니다. 
   - 수집: `https://custom.com:111/`
   - 라이브 메트릭: `https://custom.com:222/`
   - 프로파일러: `https://custom.com:333/`
   - 디버거: `https://custom.com:444/`  


## <a name="how-to-set-a-connection-string"></a>연결 문자열을 설정하는 방법

연결 문자열은 다음 SDK 버전에서 지원됩니다.
- .NET 및 .NET Core v2.12.0
- Java v2.5.1 및 Java 3.0
- JavaScript v2.3.0
- NodeJS v1.5.0
- Python v1.0.0

연결 문자열은 코드, 환경 변수 또는 구성 파일로 설정할 수 있습니다.



### <a name="environment-variable"></a>환경 변수

- 연결 문자열: `APPLICATIONINSIGHTS_CONNECTION_STRING`

### <a name="code-samples"></a>코드 샘플

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

[TelemetryConfiguration.ConnectionString](https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274) 또는 [ApplicationInsightsServiceOptions.ConnectionString](https://github.com/microsoft/ApplicationInsights-dotnet/blob/81288f26921df1e8e713d31e7e9c2187ac9e6590/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs#L66-L69) 속성 설정

.NET 명시적으로 설정:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

.NET 구성 파일:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

NetCore 명시적으로 설정:
```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions { ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;" };
    services.AddApplicationInsightsTelemetry(options: options);
}
```

NetCore config.json: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)

`applicationinsights.json` 구성 파일에서 연결 문자열을 설정할 수 있습니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
}
```

자세한 내용은 [연결 문자열 구성](./java-standalone-config.md#connection-string)을 참조하세요.

Application Insights Java 2.x의 경우 `ApplicationInsights.xml` 구성 파일에 연결 문자열을 설정할 수 있습니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

# <a name="javascript"></a>[JavaScript](#tab/js)

중요: JavaScript는 환경 변수 사용을 지원하지 않습니다.

코드 조각 사용:

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
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}});
</script>
```

> [!NOTE]
> 가독성을 높이고 가능한 JavaScript 오류를 줄이기 위해 가능한 모든 구성 옵션이 위의 코드 조각 코드에서 새 줄에 나열됩니다. 주석 처리된 줄의 값을 변경하지 않으려는 경우 이를 제거할 수 있습니다.

수동 설치:
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

# <a name="python"></a>[Python](#tab/python)

사용자가 환경 변수를 설정하는 것이 좋습니다.

연결 문자열을 명시적으로 설정하려면 다음을 수행합니다.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```

---

## <a name="next-steps"></a>다음 단계

다음을 사용하여 런타임에 시작하세요.

* [Azure VM 및 Azure Virtual Machine Scale Set IIS 호스트 앱](./azure-vm-vmss-apps.md)
* [IIS 서버](./status-monitor-v2-overview.md)
* [Azure Web Apps](./azure-web-apps.md)

다음을 사용하여 개발 시에 시작하세요.

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-in-process-agent.md)
* [Node.JS](./nodejs.md)
* [Python](./opencensus-python.md)