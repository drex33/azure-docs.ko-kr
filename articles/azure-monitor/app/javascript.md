---
title: JavaScript 웹앱용 Azure Application Insights
description: 페이지 보기 및 세션 수, 웹 클라이언트 데이터, SPA(단일 페이지 애플리케이션)를 가져오고 사용 패턴을 추적합니다. JavaScript 웹 페이지의 예외 및 성능 문제를 감지합니다.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-js
ms.openlocfilehash: 6e3b7601605eecafa969eec78b82b863580ecc2e
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129235747"
---
# <a name="application-insights-for-web-pages"></a>웹 페이지용 Application Insights

웹 페이지 또는 앱의 성능 및 사용량 현황에 대해 알아봅니다. 페이지 스크립트에 [Application Insights](app-insights-overview.md)를 추가하면 페이지 로드 및 AJAX 호출의 타이밍, 브라우저 예외 및 AJAX 실패의 개수 및 세부 정보뿐만 아니라 사용자 및 세션 개수를 얻을 수 있습니다. 이러한 모든 요소를 페이지, 클라이언트 OS 및 브라우저 버전, 지리적 위치 및 기타 차원으로 분할할 수 있습니다. 실패 횟수 또는 느린 페이지 로딩에 대한 경고를 설정할 수도 있습니다. 또한 JavaScript 코드에 추적 호출을 삽입하여 웹 페이지 애플리케이션의 다양한 기능 사용 방법을 추적할 수 있습니다.

Application Insights는 다른 웹 페이지와 함께 사용할 수 있습니다. 간단한 JavaScript만 추가하면 됩니다. 웹 서비스가 [Java](java-in-process-agent.md) 또는 [ASP.NET](asp-net.md)인 경우 클라이언트 쪽 JavaScript SDK와 함께 서버 쪽 SDK를 사용하여 애플리케이션의 성능에 대한 포괄적인 이해를 얻을 수 있습니다.

## <a name="adding-the-javascript-sdk"></a>JavaScript SDK 추가

> [!IMPORTANT]
> 계측 키보다 [연결 문자열](./sdk-connection-string.md?tabs=js)이 권장됩니다. 새 Azure 지역에서는 계측 키 대신 연결 문자열을 **사용해야 합니다**. 연결 문자열은 원격 분석 데이터를 연결할 리소스를 식별합니다. 또한 리소스가 원격 분석의 대상으로 사용할 엔드포인트를 수정할 수 있습니다. 연결 문자열을 복사하여 애플리케이션의 코드 또는 환경 변수에 추가해야 합니다.

1. 먼저 Application Insights 리소스가 필요합니다. 리소스 및 계측 키가 아직 없는 경우 [새 리소스 만들기 지침](create-new-resource.md)을 따르세요.
2. 1단계에서 JavaScript 원격 분석을 전송하려는 리소스의 '계측 키'('iKey'라고도 함) 또는 [연결 문자열](#connection-string-setup)을 복사합니다. 이 값은 Application Insights JavaScript SDK의 `instrumentationKey` 또는 `connectionString` 설정에 추가할 것입니다.
3. 다음 두 옵션 중 하나를 통해 웹 페이지 또는 앱에 Application Insights JavaScript SDK를 추가합니다.
    * [npm 설치 프로그램](#npm-based-setup)
    * [JavaScript 코드 조각](#snippet-based-setup)

> [!IMPORTANT]
> JavaScript SDK를 애플리케이션에 추가하려면 한 가지 방법만 사용합니다. NPM 설치 프로그램을 사용하는 경우 코드 조각을 사용하지 말고 그 반대의 경우도 마찬가지입니다.

> [!NOTE]
> NPM 설치 프로그램은 JavaScript SDK를 프로젝트에 대한 종속성으로 설치하고 IntelliSense를 사용하도록 설정하는 반면, 코드 조각은 런타임에 SDK를 가져옵니다. 둘 다 동일한 기능을 지원합니다. 그러나 더 많은 사용자 지정 이벤트 및 구성을 원하는 개발자는 일반적으로 NPM 설치 프로그램을 선택하고, 신속하게 웹 분석을 구현하려는 사용자는 코드 조각을 선택합니다.

### <a name="npm-based-setup"></a>npm 기반 설치

NPM을 통해 설치합니다.

```sh
npm i --save @microsoft/applicationinsights-web
```

> [!Note]
> **이 패키지에는 입력 항목이 포함되어 있으므로** 별도의 입력 항목 패키지를 설치할 필요가 **없습니다**.
    
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>코드 조각 기반 설치

앱에서 npm을 사용하지 않는 경우 각 페이지의 맨 위에 이 코드 조각을 붙여넣으면 Application Insights를 사용하여 웹 페이지를 직접 계측할 수 있습니다. 가급적이면, 모든 종속성 및 선택적으로 모든 JavaScript 오류와 관련된 잠재적인 문제를 모니터링할 수 있도록 이 코드 조각이 `<head>` 섹션의 첫 번째 스크립트여야 합니다. Blazor Server 앱을 사용하는 경우 `<head>` 섹션에서 `_Host.cshtml` 파일의 맨 위에 코드 조각을 추가합니다.

애플리케이션에서 사용하는 코드 조각의 버전을 추적하는 데 도움이 되도록 버전 2.5.5부터 페이지 보기 이벤트에는 식별된 코드 조각 버전을 포함하는 새 태그 'ai.internal.snippet'이 포함됩니다.

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
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    /* ...Other Configuration Options... */
}});
</script>
```

> [!NOTE]
> 가독성을 높이고 가능한 JavaScript 오류를 줄이기 위해 가능한 모든 구성 옵션이 위의 코드 조각 코드에서 새 줄에 나열됩니다. 주석 처리된 줄의 값을 변경하지 않으려는 경우 이를 제거할 수 있습니다.


#### <a name="reporting-script-load-failures"></a>스크립트 로드 오류 보고

이 버전의 코드 조각은 CDN에서 SDK를 로드할 때 오류를 감지하여 Azure Monitor 포털에 예외로 보고합니다(오류 &gt; 예외 &gt; 브라우저 아래). 이 예외는 애플리케이션이 예상 대로 원격 분석(또는 기타 예외)을 보고하지 않는다는 것을 알 수 있도록 이 유형의 오류에 대한 가시성을 제공합니다. 이 신호는 SDK가 로드 또는 초기화되지 않아 원격 분석이 손실되었음을 이해하는 데 중요한 측정값입니다. 이 경우 다음과 같은 결과가 발생할 수 있습니다,
- 사용자가 사이트를 사용(또는 사용을 시도)하는 방법이 충분히 보고되지 않습니다.
- 최종 사용자가 사이트를 사용하는 방법에 대한 원격 분석이 누락됩니다.
- 최종 사용자가 사이트를 사용하지 못할 수 있는 JavaScript 오류가 누락됩니다.

이 예외에 대한 자세한 내용은 [SDK 로드 오류](javascript-sdk-load-failure.md) 문제 해결 페이지를 참조하세요.

이 오류를 포털에 예외로 보고하면 Application Insights 구성의 ```disableExceptionTracking``` 구성 옵션이 사용되지 않으며, 따라서 window.onerror 지원이 활성화되지 않은 경우에도 이 오류가 발생하면 코드 조각이 항상 보고합니다.

SDK 로드 오류에 대한 보고는 IE 8 이하에서는 지원되지 않습니다. 이는 대부분의 환경이 IE 8 이하만 사용하지는 않는 것으로 가정하여 코드 조각의 크기를 줄이기 위한 것입니다. 이러한 요구 사항이 있는 경우 이러한 예외를 수신하려면 fetch poly fill을 포함하거나 ```XMLHttpRequest``` 대신 ```XDomainRequest```를 사용하는 자체 버전의 코드 조각을 만들어야 합니다. [제공된 코드 조각 소스 코드](https://github.com/microsoft/ApplicationInsights-JS/blob/master/AISKU/snippet/snippet.js)를 시작점으로 사용하는 것이 좋습니다.

> [!NOTE]
> 이전 버전의 코드 조각을 사용하는 경우 이전에 보고되지 않은 문제를 제공받을 수 있도록 최신 버전으로 업데이트하는 것이 좋습니다.

#### <a name="snippet-configuration-options"></a>코드 조각 구성 옵션

SDK가 로드되지 않을 뿐 아니라 오류 보고 자체도 비활성화되는 JavaScript 오류가 실수로 도입되는 것을 방지하기 위해 이제 모든 구성 옵션이 스크립트 끝으로 이동되었습니다.

각 구성 옵션은 새 줄 위에 표시됩니다. [선택 항목]으로 나열된 항목을 재정의하지 않으려는 경우 반환된 페이지의 결과 크기를 최소화하기 위해 해당 줄을 제거할 수 있습니다.

사용 가능한 구성 옵션은 다음과 같습니다.
 
| 이름 | 유형 | 설명
|------|------|----------------
| src | 문자열 **[필수]** | SDK를 로드할 위치의 전체 URL입니다. 이 값은 동적으로 추가된 &lt;script /&gt; 태그의 'src' 특성에 사용됩니다. 공용 CDN 위치 또는 비공개로 호스트된 위치를 사용할 수 있습니다.
| name | 문자열 *[선택 사항]* | 초기화된 SDK의 전역 이름이며 기본값은 `appInsights`입니다. 따라서 ```window.appInsights```는 초기화된 인스턴스에 대한 참조입니다. 참고: 사용자가 이름 값을 제공하거나 이전 인스턴스가 할당된 것으로 나타나는 경우(전역 이름 appInsightsSDK를 통해) 이 이름 값도 전역 네임스페이스에서 ```window.appInsightsSDK=<name value>```로 정의됩니다. 이 이름 값은 SDK 초기화 코드에서 올바른 코드 조각 구조 및 프록시 메서드를 초기화하고 업데이트하는 데 필요합니다.
| ld | 숫자(ms) *[선택 사항]* | SDK 로드를 시도하기 전에 대기하는 로드 지연 시간을 정의합니다. 기본값은 0ms이며, 음수 값은 페이지의 &lt;헤드&gt; 영역에 스크립트 태그를 즉시 추가합니다. 그러면 스크립트가 로드될 때(또는 실패할 때)까지 페이지 로드 이벤트가 차단됩니다.
| useXhr | 부울 *[선택 사항]* | 이 설정은 SDK 로드 오류 보고에만 사용됩니다. 보고는 사용 가능한 경우 fetch()를 먼저 시도한 후 XHR로 대체합니다. 이 값을 true로 설정하면 페치 확인만 무시됩니다. 페치가 오류 이벤트를 보내지 못하는 환경에서 애플리케이션을 사용하는 경우에만 이 값을 사용해야 합니다.
| crossOrigin | 문자열 *[선택 사항]* | 이 설정을 포함하면 SDK를 다운로드하기 위해 추가된 스크립트 태그에 이 문자열 값의 crossOrigin 특성이 포함됩니다. 정의하지 않는 경우(기본값) crossOrigin 특성이 추가되지 않습니다. 권장 값은 기본값(정의하지 않음), "" 또는 "anonymous"입니다(모든 유효한 값은 [HTML 특성: `crossorigin`](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin) 설명서 참조).
| cfg | 개체 **[필수]** | 초기화하는 동안 Application Insights SDK에 전달되는 구성입니다.

### <a name="connection-string-setup"></a>연결 문자열 설정

NPM 또는 코드 조각 설치의 경우 연결 문자열을 사용하여 Application Insights 인스턴스를 구성할 수도 있습니다. `instrumentationKey` 필드를 `connectionString` 필드로 바꾸기만 하면 됩니다.
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'YOUR_CONNECTION_STRING_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Azure Portal에 원격 분석 보내기

기본적으로 Application Insights JavaScript SDK는 애플리케이션의 상태 및 기본 사용자 환경을 결정하는 데 도움이 되는 수많은 원격 분석 항목을 자동으로 수집합니다. 여기에는 다음이 포함됩니다.

- 다음에 대한 정보를 포함하여 앱의 catch되지 않은 **예외**
    - 스택 추적
    - 오류 세부 정보 및 동반 메시지
    - 오류 줄 및 열 번호
    - 오류가 발생한 URL
- 다음에 대한 정보를 포함하여 앱 **XHR** 및 **페치**(페치 수집은 기본적으로 사용 안 함) 요청에 의한 **네트워크 종속성 요청**
    - 종속성 원본의 URL
    - 종속성을 요청하는 데 사용된 명령 및 메서드
    - 요청 지속 시간
    - 요청의 결과 코드 및 성공 상태
    - 요청을 수행하는 사용자의 ID(있는 경우)
    - 요청이 수행된 상관 관계 컨텍스트(있는 경우)
- **사용자 정보**(예: 위치, 네트워크, IP)
- **디바이스 정보**(예: 브라우저, OS, 버전, 언어, 모델)
- **세션 정보**

### <a name="telemetry-initializers"></a>원격 분석 이니셜라이저
원격 분석 이니셜라이저는 사용자의 브라우저에서 전송하기 전에 수집된 원격 분석의 내용을 수정하는 데 사용됩니다. 또한 `false`를 반환하여 특정 원격 분석이 전송되는 것을 방지하는 데 사용할 수도 있습니다. 여러 원격 분석 이니셜라이저를 Application Insights 인스턴스에 추가할 수 있으며 이니셜라이저는 추가된 순서대로 실행됩니다.

`addTelemetryInitializer`에 대한 입력 인수는 [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer)를 인수로 사용하고 `boolean` 또는 `void`를 반환하는 콜백입니다. `false`를 반환하는 경우 원격 분석 항목이 전송되지 않고 다른 원격 분석 이니셜라이저(있는 경우)로 진행되거나 원격 분석 수집 엔드포인트로 전송됩니다.

원격 분석 이니셜라이저 사용 예:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```

## <a name="configuration"></a>구성
대부분의 구성 필드는 기본적으로 false로 설정될 수 있도록 명명됩니다. `instrumentationKey`를 제외한 모든 필드는 선택 사항입니다.

| 이름 | Description | 기본값 |
|------|-------------|---------|
| instrumentationKey | **필수**<br>Azure Portal에서 가져온 계측 키입니다. | 문자열<br/>null |
| accountId | 앱이 사용자를 계정으로 그룹화하는 경우 계정 ID(선택 사항)입니다. 공백, 쉼표, 세미콜론, 등호 또는 세로 막대를 사용할 수 없습니다. | 문자열<br/>null |
| sessionRenewalMs | 사용자가 이 시간(밀리초) 동안 비활성 상태이면 세션이 기록됩니다. | numeric<br/>180만<br/>(30분) |
| sessionExpirationMs | 세션이 시간(밀리초) 동안 계속되면 해당 세션이 기록됩니다. | numeric<br/>8640만<br/>(24시간) |
| maxBatchSizeInBytes | 원격 분석 일괄 처리의 최대 크기입니다. 일괄 처리가 이 제한을 초과하는 즉시 전송되고 새 일괄 처리가 시작됩니다. | numeric<br/>10000 |
| maxBatchInterval | 전송하기 전에 원격 분석을 일괄 처리하는 시간(밀리초)입니다. | numeric<br/>15000 |
| disable&#8203;ExceptionTracking | True인 경우 예외가 자동으로 수집되지 않습니다. | boolean<br/> false |
| disableTelemetry | True인 경우 원격 분석이 수집되거나 전송되지 않습니다. | boolean<br/>false |
| enableDebug | True인 경우 SDK 로깅 설정에 관계없이 **내부** 디버깅 데이터가 기록되는 **대신** 예외로 throw됩니다. 기본값은 false입니다. <br>**참고:** 이 설정을 사용하면 내부 오류가 발생할 때마다 원격 분석이 삭제됩니다. 그러면 SDK의 구성 또는 사용과 관련된 문제를 신속하게 식별하는 데 유용할 수 있습니다. 디버깅하는 동안 원격 분석이 손실되지 않게 하려면 `enableDebug` 대신 `consoleLoggingLevel` 또는 `telemetryLoggingLevel`을 사용하는 것이 좋습니다. | boolean<br/>false |
| loggingLevelConsole | **내부** Application Insights 오류를 콘솔에 기록합니다. <br>0: 꺼짐, <br>1: 심각한 오류만, <br>2: 모든 항목(오류 및 경고) | numeric<br/> 0 |
| loggingLevelTelemetry | **내부** Application Insights 오류를 원격 분석으로 전송합니다. <br>0: 꺼짐, <br>1: 심각한 오류만, <br>2: 모든 항목(오류 및 경고) | numeric<br/> 1 |
| diagnosticLogInterval | 내부 로깅 큐의 (내부) 폴링 간격(밀리초)입니다. | numeric<br/> 10000 |
| samplingPercentage | 전송할 이벤트의 백분율입니다. 기본값은 100입니다. 즉, 모든 이벤트가 전송됩니다. 대규모 애플리케이션에 대한 데이터 상한을 유지하려는 경우에 설정합니다. | numeric<br/>100 |
| autoTrackPageVisitTime | True인 경우 페이지 보기에서 _이전_ 에 계측된 페이지의 보기 시간이 추적되어 원격 분석으로 전송되고 현재 페이지 보기에 대해 새 타이머가 시작됩니다. `milliseconds`에서 `PageVisitTime`이라는 사용자 지정 메트릭으로 전송되고 Date [now()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/now) 함수(사용 가능한 경우)를 통해 계산되고 (새 Date())로 돌아갑니다. now()를 사용할 수 없는 경우(IE8 이하) [getTime()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/getTime)입니다. 기본값은 false입니다. | boolean<br/>false |
| disableAjaxTracking | True인 경우 Ajax 호출이 자동으로 수집되지 않습니다. | boolean<br/> false |
| disableFetchTracking | True인 경우 페치 요청이 자동으로 수집되지 않습니다.|boolean<br/>true |
| overridePageViewDuration | True인 경우 trackPageView의 기본 동작이 trackPageView가 호출될 때 페이지 보기 기간의 끝을 기록하도록 변경됩니다. False로 설정하고 trackPageView에 사용자 지정 기간을 제공하지 않는 경우 페이지 보기 성능이 탐색 타이밍 API를 사용하여 계산됩니다. |boolean<br/>
| maxAjaxCallsPerView | 기본값 500 - 페이지 보기당 모니터링할 Ajax 호출 수를 제어합니다. 페이지에서 모든 무제한 Ajax 호출을 모니터링하려면 -1로 설정합니다. | numeric<br/> 500 |
| disableDataLossAnalysis | False인 경우 내부 원격 분석 보낸 사람 버퍼를 시작할 때 아직 보내지 않은 항목을 확인합니다. | boolean<br/> true |
| disable&#8203;CorrelationHeaders | False인 경우 SDK에서 모든 종속성 요청에 두 개의 헤더('Request-Id' 및 'Request-Context')를 추가하여 서버 쪽의 해당 요청과 상관 관계를 설정합니다. | boolean<br/> false |
| correlationHeader&#8203;ExcludedDomains | 특정 도메인에 대한 상관 관계 헤더를 사용하지 않도록 설정합니다. | string[]<br/>정의되지 않음 |
| correlationHeader&#8203;ExcludePatterns | 정규식을 사용하는 상관 관계 헤더를 사용하지 않도록 설정합니다. | regex[]<br/>정의되지 않음 |
| correlationHeader&#8203;Domains | 특정 도메인에 대한 상관 관계 헤더를 사용하도록 설정합니다. | string[]<br/>정의되지 않음 |
| disableFlush&#8203;OnBeforeUnload | True인 경우 onBeforeUnload 이벤트가 트리거될 때 flush 메서드가 호출되지 않습니다. | boolean<br/> false |
| enableSessionStorageBuffer | True인 경우 모든 전송하지 않은 원격 분석을 포함하는 버퍼가 세션 스토리지에 저장됩니다. 페이지가 로드되면 버퍼가 복원됩니다. | boolean<br />true |
| cookieCfg | 기본적으로 쿠키 사용이 활성화되어 있습니다. 전체 기본값은 [ICookieCfgConfig](#icookiemgrconfig) 설정을 참조하세요. | [ICookieCfgConfig](#icookiemgrconfig)<br>(2.6.0 이상)<br/>정의되지 않음 |
| ~~isCookieUseDisabled~~<br>disableCookiesUsage | True인 경우 SDK가 쿠키의 데이터를 저장하거나 읽지 않습니다. 이렇게 하면 사용자 및 세션 쿠키가 비활성화되고 사용량 블레이드 및 환경은 쓸모가 없어집니다. isCookieUseDisable은 disableCookiesUsage를 위해 더 이상 사용되지 않으며 둘 다 제공되는 경우 disableCookiesUsage가 우선 적용됩니다.<br>(v2.6.0 이상) 또한 `cookieCfg.enabled`를 정의하는 경우 이러한 값보다 우선 적용됩니다. 쿠키 사용은 core.getCookieMgr().setEnabled(true)를 통해 초기화하면 다시 활성화할 수 있습니다. | [`cookieCfg.enabled`](#icookiemgrconfig)의 별칭<br>false |
| cookieDomain | 사용자 지정 쿠키 도메인입니다. 하위 도메인에서 Application Insights 쿠키를 공유하려는 경우에 유용합니다.<br>(v2.6.0 이상) `cookieCfg.domain`을 정의하는 경우 이 값보다 우선 적용됩니다. | [`cookieCfg.domain`](#icookiemgrconfig)의 별칭<br>null |
| cookiePath | 사용자 지정 쿠키 경로입니다. 애플리케이션 게이트웨이 뒤에서 Application Insights 쿠키를 공유하려는 경우에 유용합니다.<br>`cookieCfg.path`를 정의하는 경우 이 값보다 우선 적용됩니다. | [`cookieCfg.path`](#icookiemgrconfig)의 별칭<br>(2.6.0 이상)<br/>null |
| isRetryDisabled | False인 경우 206(부분 성공), 408(시간 초과), 429(너무 많은 요청), 500(내부 서버 오류), 503(서비스를 사용할 수 없음), 0(오프라인, 검색된 경우만)에서 다시 시도합니다. | boolean<br/>false |
| isStorageUseDisabled | True인 경우 SDK가 로컬 및 세션 스토리지에서 데이터를 저장하거나 읽지 않습니다. | boolean<br/> false |
| isBeaconApiDisabled | False인 경우 SDK가 [알림 API](https://www.w3.org/TR/beacon)를 사용하여 모든 원격 분석을 전송합니다. | boolean<br/>true |
| onunloadDisableBeacon | 탭이 닫히면 SDK가 [알림 API](https://www.w3.org/TR/beacon)를 사용하여 나머지 모든 원격 분석을 전송합니다. | boolean<br/> false |
| sdkExtension | SDK 확장 이름을 설정합니다. 영문자만 사용할 수 있습니다. 확장명이 'ai.internal.sdkVersion' 태그에 접두사로 추가됩니다(예: 'ext_javascript:2.0.0'). | 문자열<br/> null |
| isBrowserLink&#8203;TrackingEnabled | True인 경우 SDK가 모든 [브라우저 링크](/aspnet/core/client-side/using-browserlink) 요청을 추적합니다. | boolean<br/>false |
| appId | AppId는 클라이언트에서 발생하는 AJAX 종속성과 서버 쪽 요청 간의 상관 관계에 사용됩니다. 알림 API가 설정된 경우 자동으로 사용할 수 없고 구성에서 수동으로 설정할 수 있습니다. |문자열<br/> null |
| enable&#8203;CorsCorrelation | True인 경우 SDK에서 모든 CORS 요청에 두 개의 헤더('Request-Id' 및 'Request-Context')를 추가하여 나가는 AJAX 종속성과 해당 서버 쪽 요청 간의 상관 관계를 설정합니다. | boolean<br/>false |
| namePrefix | localStorage 및 쿠키 이름에 이름 접미사로 사용할 선택적 값입니다. | 문자열<br/>정의되지 않음 |
| enable&#8203;AutoRoute&#8203;Tracking | SPA(단일 페이지 애플리케이션)의 경로 변경 내용을 자동으로 추적합니다. True인 경우 각 경로 변경 시 Application Insights에 새 페이지 보기가 전송됩니다. 해시 경로 변경(`example.com/foo#bar`)도 새 페이지 보기로 기록됩니다.| boolean<br/>false |
| enableRequest&#8203;HeaderTracking | True인 경우 AJAX 및 페치 요청 헤더가 추적됩니다. | boolean<br/> false |
| enableResponse&#8203;HeaderTracking | True인 경우 AJAX 및 페치 요청의 응답 헤더가 추적됩니다. | boolean<br/> false |
| distributedTracingMode | 분산 추적 모드를 설정합니다. AI_AND_W3C 모드 또는 W3C 모드를 설정하는 경우 W3C 추적 컨텍스트 헤더(traceparent/tracestate)가 생성되고 나가는 모든 요청에 포함됩니다. AI_AND_W3C는 Application Insights로 계측되는 레거시 서비스와의 하위 호환성을 위해 제공됩니다. 예제는 [여기](./correlation.md#enable-w3c-distributed-tracing-support-for-web-apps)를 참조하세요.| `DistributedTracingModes` 또는<br/>numeric<br/>(v2.6.0 이상) `DistributedTracingModes.AI_AND_W3C`<br />(v2.5.11 이전) `DistributedTracingModes.AI` |
| enable&#8203;AjaxErrorStatusText | True인 경우 실패한 AJAX 요청에 대한 종속성 이벤트에 응답 오류 데이터 텍스트를 포함합니다. | boolean<br/> false |
| enable&#8203;AjaxPerfTracking |보고된 `ajax`(XHR 및 페치) 메트릭에 추가 브라우저 window.performance 타이밍을 조회하고 포함할 수 있도록 하는 플래그입니다. | boolean<br/> false |
| maxAjaxPerf&#8203;LookupAttempts | window.performance 타이밍(사용할 수 있는 경우)을 찾을 수 있는 최대 횟수입니다. 이는 일부 브라우저가 XHR 요청의 끝을 보고하기 전에 window.performance를 채우지 않으며 페치 요청의 경우 완료 후에 추가되기 때문에 필요합니다.| numeric<br/> 3 |
| ajaxPerfLookupDelay | 기간을 다시 시도 하기 전에 대기 하는 시간입니다. 요청에 대 한 성능 타이밍과 `ajax` 시간 (밀리초) 이며 setTimeout ()에 직접 전달 됩니다. | numeric<br/> 25ms |
| enableUnhandled&#8203;PromiseRejection&#8203;Tracking | True인 경우 처리되지 않은 약속 거부가 자동으로 수집되고 JavaScript 오류로 보고됩니다. disableExceptionTracking이 true인 경우(예외를 추적하지 않음) 구성 값이 무시되고 처리되지 않은 약속 거부가 보고되지 않습니다. | boolean<br/> false |
| disable&#8203;InstrumentationKey&#8203;Validation | True인 경우 계측 키 유효성 검사를 무시합니다. | boolean<br/>false |
| enablePerfMgr | 사용하도록 설정하면(true) doPerf() 도우미를 통해 perfEvents를 내보내도록 계측된 코드에 대한 로컬 perfEvents를 만듭니다. 이를 통해 사용량에 따라 또는 계측된 코드 내에서 필요에 따라 SDK 내의 성능 문제를 식별할 수 있습니다. [자세한 내용은 기본 설명서에서 확인할 수 있습니다](https://github.com/microsoft/ApplicationInsights-JS/blob/master/docs/PerformanceMonitoring.md). v2.5.7 이상 | boolean<br/>false |
| perfEvtsSendAll | _enablePerfMgr_ 가 사용하도록 설정되고 [IPerfManager](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/IPerfManager.ts)가 [INotificationManager](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/INotificationManager.ts).perfEvent()를 실행하는 경우 이 플래그는 모든 이벤트(true)에 대해 또는 '부모' 이벤트(false &lt;기본값&gt;)에 대해서만 이벤트가 생성되고 모든 수신기에 전송되는지 여부를 결정합니다.<br />부모 [IPerfEvent](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/IPerfEvent.ts)는 이 이벤트가 생성되는 시점에서 다른 IPerfEvent가 실행 중이지 않고 '부모' 속성이 Null이거나 정의되지 않은 경우의 이벤트입니다. v2.5.7 이상 |  boolean<br />false |
| idLength | 새 임의 세션 및 사용자 ID 값을 생성하는 데 사용되는 기본 길이를 식별합니다. 기본값은 22이고, 이전 기본값(v2.5.8 이하)은 5입니다. 이전 최대 길이를 유지해야 하는 경우 이 값을 5로 설정해야 합니다. |  numeric<br />22 |

## <a name="cookie-handling"></a>쿠키 처리

버전 2.6.0부터 쿠키 관리는 이제 인스턴스에서 직접 사용할 수 있으며 초기화 후 비활성화 및 다시 활성화할 수 있습니다.

`disableCookiesUsage` 또는 `cookieCfg.enabled` 구성을 통해 초기화하는 동안 비활성화된 경우 이제 [ICookieMgr](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts) `setEnabled` 함수를 통해 다시 활성화할 수 있습니다.

또한 인스턴스 기반 쿠키 관리는 이전의 CoreUtils 전역 함수 `disableCookies()`, `setCookie(...)`, `getCookie(...)`, `deleteCookie(...)`를 대체합니다. 또한 버전 2.6.0의 일부로 도입된 트리셰이킹 기능 향상의 이점을 얻으려면 더 이상 전역 함수를 사용하지 않아야 합니다.

### <a name="icookiemgrconfig"></a>ICookieMgrConfig

인스턴스 기반 쿠키 관리를 위한 쿠키 구성이 버전 2.6.0에 추가되었습니다.

| 이름 | 설명 | 형식 및 기본값 |
|------|-------------|------------------|
| 사용 | 현재 인스턴스에서 SDK의 쿠키 사용을 사용하는지 여부를 나타내는 부울입니다. False인 경우 이 구성으로 초기화된 SDK의 인스턴스가 쿠키의 데이터를 저장하거나 읽지 않습니다. | boolean<br/> true |
| 도메인 | 사용자 지정 쿠키 도메인입니다. 하위 도메인에서 Application Insights 쿠키를 공유하려는 경우에 유용합니다. 지정하지 않으면 루트 `cookieDomain` 값의 값이 사용됩니다. | 문자열<br/>null |
| path | 쿠키에 사용할 경로를 지정합니다. 지정하지 않으면 루트 `cookiePath` 값의 값이 사용됩니다. | 문자열 <br/> / |
| getCookie | 명명된 쿠키 값을 가져오는 함수입니다. 지정되지 않으면 내부 쿠키 구문 분석/캐싱이 사용됩니다. | `(name: string) => string` <br/> null |
| setCookie | 쿠키를 추가하거나 업데이트하는 경우에만 호출되어 명명된 쿠키를 지정된 값을 사용하여 설정하는 함수입니다. | `(name: string, value: string) => void` <br/> null |
| delCookie | 쿠키를 추가 또는 제거할지 여부를 결정하기 위해 값을 구문 분석할 필요가 없도록 지정된 값을 사용하여 명명된 쿠키를 setCookie와 구분하여 삭제하는 함수입니다. 지정하지 않으면 내부 쿠키 구문 분석/캐싱이 사용됩니다. | `(name: string, value: string) => void` <br/> null |

### <a name="simplified-usage-of-new-instance-cookie-manager"></a>새 인스턴스 쿠키 관리자 사용 간소화

- appInsights.[getCookieMgr()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts).setEnabled(true/false);
- appInsights.[getCookieMgr()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts).set("MyCookie", "the%20encoded%20value");
- appInsights.[getCookieMgr()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts).get("MyCookie");
- appInsights.[getCookieMgr()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts).del("MyCookie");

## <a name="enable-time-on-page-tracking"></a>페이지 소비 시간 추적 사용

`autoTrackPageVisitTime: true`를 설정하면 각 페이지에서 사용자가 소비한 시간이 추적됩니다. 새 페이지 보기가 시작할 때마다 사용자가 '이전' 페이지에서 소비한 시간이 `PageVisitTime`이라는 [사용자 지정](../essentials/metrics-custom-overview.md) 메트릭으로 전송됩니다. 이 사용자 지정 메트릭은 [메트릭 탐색기](../essentials/metrics-getting-started.md)에서 '로그 기반 메트릭'으로 볼 수 있습니다.

## <a name="enable-correlation"></a>상관 관계 사용

상관 관계는 분산 추적을 사용하고 [애플리케이션 맵](../app/app-map.md), [엔드투엔드 트랜잭션 보기](../app/app-map.md#go-to-details) 및 기타 진단 도구를 지원하는 데이터를 생성하고 전송합니다.

다음 예제에서는 아래 시나리오 관련 정보를 사용하여 상관 관계를 설정하는 데 필요한 모든 구성을 보여 줍니다.

```javascript
// excerpt of the config section of the JavaScript SDK snippet with correlation
// between client-side AJAX and server requests enabled.
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    disableFetchTracking: false,
    enableCorsCorrelation: true,
    enableRequestHeaderTracking: true,
    enableResponseHeaderTracking: true,
    correlationHeaderExcludedDomains: ['myapp.azurewebsites.net', '*.queue.core.windows.net']
    /* ...Other Configuration Options... */
}});
</script>

``` 

클라이언트가 통신하는 타사 서버에서 `Request-Id` 및 `Request-Context` 헤더를 수락할 수 없고 해당 서버에서 구성을 업데이트할 수 없는 경우 `correlationHeaderExcludedDomains` 구성 속성을 통해 해당 서버를 제외 목록에 배치해야 합니다. 이 속성은 와일드 카드를 지원합니다.

서버 쪽에서 해당 헤더와의 연결을 수락할 수 있어야 합니다. 서버 쪽 `Access-Control-Allow-Headers` 구성에 따라 `Request-Id` 및 `Request-Context`를 수동으로 추가하여 서버 쪽 목록을 확장해야 하는 경우가 많습니다.

Access-Control-Allow-Headers: `Request-Id`, `Request-Context`, `<your header>`

> [!NOTE]
> 2020년 이후 릴리스된 OpenTelemtry 또는 Application Insights SDK를 사용하는 경우 [WC3 TraceContext](https://www.w3.org/TR/trace-context/)를 사용하는 것이 좋습니다. 구성 지침은 [여기](../app/correlation.md#enable-w3c-distributed-tracing-support-for-web-apps)를 참조하세요.

## <a name="single-page-applications"></a>단일 페이지 애플리케이션

기본적으로 이 SDK는 단일 페이지 애플리케이션에서 발생하는 상태 기반 경로 변경을 처리하지 **않습니다**. 단일 페이지 애플리케이션에 대해 자동 경로 변경 추적을 사용하도록 설정하려면 `enableAutoRouteTracking: true`를 설치 구성에 추가하면 됩니다.

현재 이 SDK를 사용하여 초기화할 수 있는 별도의 [React 플러그 인](javascript-react-plugin.md)을 제공합니다. 이 플러그 인은 자동으로 경로 변경을 추적하고 다른 React 고유의 원격 분석을 수집합니다.
> [!NOTE]
> React 플러그 인을 사용하지 **않는** 경우에만 `enableAutoRouteTracking: true`를 사용합니다. 둘 모두 경로가 변경될 때 새 페이지 보기를 전송할 수 있습니다. 둘 다 사용하도록 설정된 경우 페이지 보기가 중복될 수 있습니다.

## <a name="extensions"></a>확장

| 확장 |
|---------------|
| [React](javascript-react-plugin.md)|
| [React Native](javascript-react-native-plugin.md)|
| [Angular](javascript-angular-plugin.md)|
| [클릭 분석 자동 수집](javascript-click-analytics-plugin.md)|

## <a name="explore-browserclient-side-data"></a>브라우저/클라이언트 쪽 데이터 탐색

**메트릭** 으로 이동하고 관심 있는 개별 메트릭을 추가하여 브라우저/클라이언트 쪽 데이터를 볼 수 있습니다.

![웹 애플리케이션에 대한 메트릭 데이터의 그래픽 표시를 보여 주는 Application Insights 메트릭 페이지의 스크린샷](./media/javascript/page-view-load-time.png)

포털의 브라우저 환경을 통해 JavaScript SDK에서 데이터를 볼 수도 있습니다.

**브라우저** 를 선택한 다음 **오류** 또는 **성능** 을 선택합니다.

![웹 애플리케이션에 대해 볼 수 있는 메트릭에 브라우저 오류 또는 브라우저 성능을 추가하는 방법을 보여 주는 Application Insights 브라우저 페이지의 스크린샷](./media/javascript/browser.png)

### <a name="performance"></a>성능

![웹 애플리케이션에 대한 작업 메트릭의 그래픽 표시를 보여 주는 Application Insights 성능 페이지의 스크린샷](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>종속성

![웹 애플리케이션에 대한 종속성 메트릭의 그래픽 표시를 보여 주는 Application Insights 성능 페이지의 스크린샷](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>분석

JavaScript SDK에서 수집된 원격 분석을 쿼리하려면 **로그(분석)에서 보기** 단추를 선택합니다. `client_Type == "Browser"`의 `where` 문을 추가하면 JavaScript SDK의 데이터만 볼 수 있으며 다른 SDK에서 수집된 서버 쪽 원격 분석은 제외됩니다.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>소스 맵 지원

예외 원격 분석의 축소된 호출 스택은 Azure Portal에서 축소 취소할 수 없습니다. 예외 세부 정보 패널의 모든 기존 통합은 새로운 축소되지 않은 호출 스택에 적용됩니다.

#### <a name="link-to-blob-storage-account"></a>Blob Storage 계정에 대한 링크

사용자의 Azure Blob Storage 컨테이너에 Application Insights 리소스를 연결하여 호출 스택을 자동으로 축소 취소할 수 있습니다. 시작하려면 [자동 소스 맵 지원](./source-map-support.md)을 참조하세요.

### <a name="drag-and-drop"></a>끌어서 놓기

1. Azure Portal에서 예외 원격 분석 항목을 선택하여 '엔드투엔드 트랜잭션 정보'를 확인합니다.
2. 이 호출 스택에 해당하는 소스 맵을 식별합니다. 소스 맵은 스택 프레임의 원본 파일과 일치해야 하지만 `.map`이라는 접미사가 붙습니다.
3. 소스 맵을 Azure Portal의 호출 스택 위로 끌어서 놓습니다. ![빌드 폴더의 소스 맵 파일을 Azure Portal의 호출 스택으로 끌어서 놓는 방법을 보여 주는 애니메이션 이미지](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web Basic

가볍게 체험해 보기 위해 Application Insights의 기본 버전을 대신 설치할 수 있습니다.
```
npm i --save @microsoft/applicationinsights-web-basic
```
이 버전은 최소한의 기능을 제공하며, 적합하다고 판단되면 추가 기능을 설치할 수 있습니다. 예를 들어 이 버전은 자동 수집을 수행하지 않습니다(catch되지 않은 예외, AJAX 등). `trackTrace`, `trackException` 등 특정 원격 분석 유형을 전송하는 API는 이 버전에 포함되지 않으므로 사용자가 래퍼를 제공해야 합니다. 유일하게 사용할 수 있는 API는 `track`입니다. [샘플](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html)은 여기에 있습니다.

## <a name="examples"></a>예

실행 가능한 예제는 [Application Insights JavaScript SDK 샘플](https://github.com/Azure-Samples?q=applicationinsights-js-demo)을 참조하세요.

## <a name="upgrading-from-the-old-version-of-application-insights"></a>이전 버전의 Application Insights에서 업그레이드

SDK V2 버전에서 호환성이 손상되는 변경 내용:
- 향상된 API 서명을 사용할 수 있도록 trackPageView 및 trackException과 같은 일부 API 호출이 업데이트되었습니다. Internet Explorer 8 및 이전 버전의 브라우저에서는 실행이 지원되지 않습니다.
- 데이터 스키마 업데이트로 인해 원격 분석 봉투(Envelope)에서 필드 이름 및 구조가 변경되었습니다.
- `context.operation`을 `context.telemetryTrace`로 이동했습니다. 일부 필드도 변경되었습니다(`operation.id` --> `telemetryTrace.traceID`).
  - 수동으로 현재 페이지 보기 ID를 새로 고치려면(예: SPA 앱) `appInsights.properties.context.telemetryTrace.traceID = Microsoft.ApplicationInsights.Telemetry.Util.generateW3CId()`를 사용합니다.
    > [!NOTE]
    > 추적 ID를 고유하게 유지하려면 이전에 `Util.newId()`를 사용했지만 이제는 `Util.generateW3CId()`를 사용합니다. 둘 다 결국 작업 ID가 됩니다.

현재 Application Insights PRODUCTION SDK(1.0.20)를 사용 중이고 새 SDK가 런타임에 작동하는지 확인하려는 경우 현재 SDK 로드 시나리오에 따라 URL을 업데이트합니다.

- CDN 시나리오를 통해 다운로드: 현재 다음 URL을 가리키는 데 사용하는 코드 조각을 업데이트합니다.
   ```
   "https://js.monitor.azure.com/scripts/b/ai.2.min.js"
   ```

- npm 시나리오: `downloadAndSetup`을 호출하여 CDN에서 전체 ApplicationInsights 스크립트를 다운로드하고 다음 계측 키를 사용하여 초기화합니다.

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://js.monitor.azure.com/scripts/b/ai.2.min.jss"
     });
   ```

내부 환경에서 테스트를 수행하여 모니터링 원격 분석이 예상대로 작동하는지 확인합니다. 모두 작동하는 경우 API를 SDK V2 버전으로 적절하게 업데이트하고 프로덕션 환경에 배포합니다.

## <a name="sdk-performanceoverhead"></a>SDK 성능/오버헤드

Gzip 압축 크기가 36KB에 불과해 초기화하는 데 15밀리초 밖에 걸리지 않으므로 Application Insights가 웹 사이트에 추가하는 로드 시간은 무시할 수 있습니다. 코드 조각을 사용하면 라이브러리의 최소 구성 요소가 빠르게 로드됩니다. 한편 전체 스크립트는 백그라운드에서 다운로드됩니다.

스크립트를 CDN에서 다운로드하는 동안 페이지의 모든 추적이 큐에 추가됩니다. 다운로드한 스크립트가 비동기적으로 초기화를 완료하면 큐에 추가된 모든 이벤트가 추적됩니다. 따라서 페이지의 전체 수명 주기 중에 원격 분석이 손실되지 않습니다. 이 설치 프로세스를 통해 최종 사용자에게 보이지 않는 원활한 분석 시스템을 페이지에 제공합니다.

> 요약:
> - ![npm 버전](https://badge.fury.io/js/%40microsoft%2Fapplicationinsights-web.svg)
> - ![gzip 압축 크기](https://img.badgesize.io/https://js.monitor.azure.com/scripts/b/ai.2.min.js.svg?compression=gzip)
> - 전체 초기화 시간 **15ms**
> - 페이지 수명 주기 동안 추적 누락 **0**

## <a name="browser-support"></a>브라우저 지원

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Chrome 최신 ✔ |  Firefox 최신 ✔ | IE 9+ 및 Edge ✔<br>IE 8- 호환 가능 | Opera 최신 ✔ | Safari 최신 ✔ |

## <a name="es3ie8-compatibility"></a>ES3/IE8 호환성

SDK로서, 고객이 사용하는 브라우저를 제어할 수 없는 사용자가 많이 있습니다. 따라서 이 SDK가 계속 '작동'하고 이전 브라우저에서 로드될 때 JS 실행을 중단하지 않도록 해야 합니다. IE8 및 이전 세대(ES3) 브라우저를 지원하지 않는 것이 이상적이지만, 많은 대규모 고객/사용자가 계속해서 페이지를 '유지'해야 하며, 언급했듯이 최종 사용자가 선택하는 브라우저를 제어할 수 없을 수 있습니다.

이것이 Microsoft가 ES3 코드 호환성을 유지해야 하고 새로운 기능을 추가할 때 ES3 JavaScript 구문 분석을 중단하지 않는 방식으로 추가하고 선택적 기능으로 추가해야 하기 때문에 최소한의 공통 기능 세트만 지원할 것이라는 의미는 아닙니다.

[GitHub에서 IE8 지원에 대한 자세한 내용 확인](https://github.com/Microsoft/ApplicationInsights-JS#es3ie8-compatibility)

## <a name="open-source-sdk"></a>오픈 소스 SDK

Application Insights JavaScript SDK는 소스 코드를 보거나 프로젝트에 기여할 수 있는 오픈 소스입니다. [공식 GitHub 리포지토리](https://github.com/Microsoft/ApplicationInsights-JS)를 방문하세요. 

최신 업데이트 및 버그 수정에 대해서는 [릴리스 정보를 참조](./release-notes.md)하세요.

## <a name="next-steps"></a><a name="next"></a> 다음 단계
* [사용 현황 추적](usage-overview.md)
* [사용자 지정 이벤트 및 메트릭](api-custom-events-metrics.md)
* [빌드 - 측정 - 학습](usage-overview.md)
* [SDK 로드 실패 문제 해결](javascript-sdk-load-failure.md)
