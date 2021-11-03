---
title: Azure Application Insights의 애플리케이션 맵 | Microsoft Docs
description: 애플리케이션 맵을 사용하여 복잡한 애플리케이션 토폴로지 모니터링
ms.topic: conceptual
ms.date: 03/15/2019
ms.custom: devx-track-csharp
ms.reviewer: sdash
ms.openlocfilehash: 01fc0be9a2ce8db89bbbcc032bbf90652d5627b8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131079399"
---
# <a name="application-map-triage-distributed-applications"></a>애플리케이션 맵: 분산 애플리케이션 심사

애플리케이션 맵을 사용하면 분산된 애플리케이션의 모든 구성 요소에서 성능 병목 상태 또는 실패 핫스폿을 찾는 데 도움이 됩니다. 맵의 각 노드는 애플리케이션 구성 요소 또는 해당 종속성을 나타내며 상태 KPI 및 경고 상태를 나타냅니다. 구성 요소부터 Application Insights 이벤트와 같은 보다 자세한 진단까지 클릭하면서 살펴볼 수 있습니다. 앱에서 Azure 서비스를 사용하는 경우 SQL Database Advisor 권장 사항과 같은 Azure 진단도 살펴볼 수 있습니다.

## <a name="what-is-a-component"></a>구성 요소란?

구성 요소는 독립적으로 배포할 수 있는 분산/마이크로 서비스 애플리케이션의 부분입니다. 개발자 및 운영 팀은 이러한 애플리케이션 구성 요소에서 생성된 원격 분석에 대한 코드 수준의 가시성 또는 액세스 권한을 갖습니다.

* 구성 요소는 팀/조직에서 액세스할 수 없는 SQL, EventHub 등과 같은 "관찰된" 외부 종속성(코드 또는 원격 분석)과 다릅니다.
* 구성 요소는 서버/역할/컨테이너 인스턴스의 수에 관계없이 실행됩니다.
* 구성 요소는 별도의 Application Insights 계측 키(구독이 다른 경우에도) 또는 단일 Application Insights 계측 키에 보고하는 다른 역할일 수 있습니다. 맵 미리 보기 환경은 구성 요소를 설정 방식에 관계없이 표시합니다.

## <a name="composite-application-map"></a>복합 애플리케이션 맵

여러 수준의 관련된 애플리케이션 구성 요소의 전체 애플리케이션 토폴로지를 볼 수 있습니다. 구성 요소는 다른 Application Insights 리소스이거나 단일 리소스 내의 다른 역할일 수 있습니다. 앱은 Application Insights SDK가 설치된 서버 간에 수행된 HTTP 종속성 호출에 따라 구성 요소를 찾습니다.

이러한 환경은 구성 요소를 점진적으로 검색으로 시작됩니다. 애플리케이션 맵을 처음 로드할 때 이 구성 요소와 관련된 구성 요소를 검색하기 위해 쿼리 세트가 트리거됩니다. 왼쪽 위 구석에 있는 단추는 검색된 애플리케이션의 구성 요소 수로 업데이트됩니다.

"맵 구성 요소 업데이트"를 클릭하면 맵이 해당 시점까지 검색된 모든 구성 요소로 새로 고쳐집니다. 애플리케이션의 복잡성에 따라, 로드하는 데 다소 시간이 걸릴 수 있습니다.

모든 구성 요소가 단일 Application Insights 리소스 내의 역할인 경우 이 검색 단계가 필요하지 않습니다. 이러한 애플리케이션에 대한 초기 로드 시에는 모든 구성 요소가 포함됩니다.

![애플리케이션 맵의 예를 보여 주는 스크린샷](media/app-map/app-map-001.png)

수백 가지 구성 요소가 있는 복잡한 토폴로지를 시각화하는 것이 이 환경의 주요 목표 중 하나입니다.

모든 구성 요소를 클릭하여 관련 정보를 확인하고, 해당 구성 요소에 대한 성능 및 실패 심사 환경으로 이동합니다.

![플라이아웃](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>오류 조사

**오류 조사** 를 선택하여 오류 창을 실행합니다.

![오류 조사 단추 스크린샷](media/app-map/investigate-failures.png)

![오류 환경 스크린샷](media/app-map/failures.png)

### <a name="investigate-performance"></a>성능 조사

성능 문제를 해결하려면 **성능 조사** 를 선택합니다.

![성능 조사 단추 스크린샷](media/app-map/investigate-performance.png)

![성능 환경 스크린샷](media/app-map/performance.png)

### <a name="go-to-details"></a>세부 정보로 이동

**세부 정보로 이동** 을 선택하여 호출 스택 수준까지 보기를 제공할 수 있는 엔드투엔드 트랜잭션 환경을 탐색합니다.

![세부 정보로 이동 단추 스크린샷](media/app-map/go-to-details.png)

![엔드투엔드 트랜잭션 세부 정보 스크린샷](media/app-map/end-to-end-transaction.png)

### <a name="view-logs-analytics"></a>로그(분석) 보기

애플리케이션 데이터를 더 쿼리하고 조사하려면 **로그(분석)에서 보기** 를 클릭합니다.

![Analytics에서 보기 단추 스크린샷](media/app-map/view-logs.png)

![분석 환경의 스크린샷 지난 12시간 동안의 요청 평균 응답 기간을 요약하는 꺾은선 그래프입니다.](media/app-map/log-analytics.png)

### <a name="alerts"></a>경고

경고가 트리거될 수 있는 활성 경고 및 기본 규칙을 보려면 **경고** 를 선택합니다.

![경고 단추 스크린샷](media/app-map/alerts.png)

![Analytics 환경 스크린샷](media/app-map/alerts-view.png)

## <a name="set-or-override-cloud-role-name"></a>클라우드 역할 이름 설정 또는 재정의

애플리케이션 맵에서는 **클라우드 역할 이름** 속성을 사용하여 맵에 있는 구성 요소를 식별합니다. 클라우드 역할 이름을 설정 또는 재정의하고 애플리케이션 맵에 표시되는 내용을 변경하려면

> [!NOTE]
> Application Insights SDK 또는 에이전트는 Azure App Service 환경에서 구성 요소가 내보낸 원격 분석에 클라우드 역할 이름 속성을 자동으로 추가합니다.

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

**아래와 같이 사용자 지정 TelemetryInitializer을 작성합니다.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**ASP.NET 앱: 활성 TelemetryConfiguration에 이니셜라이저를 로드합니다.**

ApplicationInsights.config에서:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

ASP.NET 웹앱에 대한 대체 방법으로 Global.aspx.cs와 같은 코드에서 이니셜라이저를 인스턴스화합니다.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> `ApplicationInsights.config` 또는 `TelemetryConfiguration.Active`를 사용하여 이니셜라이저를 추가하는 것은 ASP.NET Core 애플리케이션에 적합하지 않습니다.

**ASP.NET Core 앱: TelemetryConfiguration에 이니셜라이저를 로드합니다.**

[ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) 애플리케이션의 경우 새 `TelemetryInitializer` 추가 작업은 아래와 같이 종속성 주입 컨테이너에 추가하여 수행됩니다. 이 작업은 `Startup.cs` 클래스의 `ConfigureServices` 메서드에서 수행됩니다.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

# <a name="java"></a>[Java](#tab/java)

**Java 에이전트**

[Java 에이전트 3.0](./java-in-process-agent.md)의 경우 클라우드 역할 이름은 다음과 같이 설정됩니다.

```json
{
  "role": {
    "name": "my cloud role name"
  }
}
```

환경 변수 ```APPLICATIONINSIGHTS_ROLE_NAME```을 사용하여 클라우드 역할 이름을 설정할 수도 있습니다.

**Java SDK**

Application Insights Java SDK 2.5.0부터 SDK를 사용하는 경우 예를 들어 `ApplicationInsights.xml` 파일에 `<RoleName>`을 추가하여 클라우드 역할 이름을 지정할 수 있습니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
   <RoleName>** Your role name **</RoleName>
   ...
</ApplicationInsights>
```

Application Insights Spring Boot 스타터에서 Spring Boot를 사용하는 경우 application.properties 파일에서 애플리케이션에 대한 사용자 지정 이름을 설정하도록 변경하기만 하면 됩니다.

`spring.application.name=<name-of-app>`

Spring Boot Starter는 spring.application.name 속성에 입력한 값에 자동으로 클라우드 역할 이름을 할당합니다.

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Node.js에 대한 대체 방법

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

# <a name="python"></a>[Python](#tab/python)

Python의 경우 [OpenCensus Python 원격 분석 프로세서](api-filtering-sampling.md#opencensus-python-telemetry-processors)를 사용할 수 있습니다.

```python
def callback_function(envelope):
   envelope.tags['ai.cloud.role'] = 'new_role_name'

# AzureLogHandler
handler.add_telemetry_processor(callback_function)

# AzureExporter
exporter.add_telemetry_processor(callback_function)
```
---

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>애플리케이션 맵의 컨텍스트에서 클라우드 역할 이름 이해

**클라우드 역할 이름** 에 관해서는 여러 개의 클라우드 역할 이름이 있는 애플리케이션 맵을 살펴보면 도움이 될 수 있습니다.

![애플리케이션 맵 스크린샷](media/app-map/cloud-rolename.png)

위의 애플리케이션 맵에서 녹색 상자에는 이 분산 애플리케이션의 다양한 측면에 대한 클라우드 역할 이름 값이 있습니다. 즉, 이 앱은 역할이 `Authentication`, `acmefrontend`, `Inventory Management`, `Payment Processing Worker Role`로 구성됩니다.

이 앱의 경우 각 클라우드 역할 이름은 자체 계측 키가 있는 다른 고유한 Application Insights 리소스도 나타냅니다. 이 애플리케이션의 소유자는 이 4개의 서로 다른 Application Insights 리소스에 대해 액세스할 수 있으므로 애플리케이션 맵은 기본 관계의 맵을 함께 연결할 수 있습니다.

[공식 정의](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";

    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

또는, **클라우드 역할 인스턴스** 는 **클라우드 역할 이름** 이 웹 프런트 엔드의 어딘가에 문제가 있음을 알려주는 시나리오에 유용할 수 있습니다. 그러나 웹 프런트 엔드를 부하 분산된 여러 서버에서 실행하여 Kusto 쿼리를 통해 더 심층적으로 레이어를 검색하고 문제가 모든 웹 프런트 엔드 서버/인스턴스에 영향을 주는지 아니면 하나만 매우 중요할 수 있는지 확인할 수 있습니다.

개별 서버에 대한 정보만으로는 특정 문제를 찾기에 부족할 수 있는 컨테이너화된 환경에서 앱이 실행되는 경우 클라우드 역할 인스턴스의 값을 재정의하는 것이 좋습니다.

원격 분석 이니셜라이저를 사용하여 클라우드 역할 이름 속성을 재정의하는 방법에 대한 자세한 내용은 [속성 추가: ITelemetryInitializer](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)을 참조하세요.

## <a name="troubleshooting"></a>문제 해결

Application Map이 예상대로 작동하지 않는 경우 다음 단계를 수행하세요.

### <a name="general"></a>일반

1. 공식적으로 지원되는 SDK를 사용하고 있는지 확인합니다. 비지원/커뮤니티 SDK는 상관 관계를 지원하지 않을 수 있습니다.

    지원되는 SDK 목록에 대해서는 이 [문서](./platforms.md)를 참조하세요.

2. 모든 구성 요소를 최신 SDK 버전으로 업그레이드합니다.

3. C#으로 Azure Functions를 사용하는 경우 [Functions V2](../../azure-functions/functions-versions.md)로 업그레이드합니다.

4. [클라우드 역할 이름](#set-or-override-cloud-role-name)이 올바르게 구성되었는지 확인합니다.

5. 종속성이 누락된 경우 [자동 수집 종속성](./auto-collect-dependencies.md) 목록에 들어 있는지 확인합니다. 이 목록에 없어도 [종속성 호출 추적](./api-custom-events-metrics.md#trackdependency)을 사용하여 수동으로 계속 추적할 수 있습니다.

### <a name="too-many-nodes-on-the-map"></a>맵에 노드가 너무 많음

애플리케이션 맵은 요청 원격 분석에 있는 각 고유한 클라우드 역할 이름에 대해 애플리케이션 노드를 생성하고 종속성 원격 분석에서 유형, 대상 및 클라우드 역할 이름의 고유한 조합 각각에 대한 종속성 노드를 생성합니다. 원격 분석에 노드가 1만 개 이상 있는 경우 애플리케이션 맵은 모든 노드와 링크를 인출할 수 없으므로 맵이 완전하지 않습니다. 이 경우 맵을 보면 경고 메시지가 표시됩니다.

또한 애플리케이션 맵은 한 번에 렌더링된 그룹화되지 않은 개별 노드를 최대 1,000개만 지원합니다. 애플리케이션 맵은 동일한 유형 및 호출자를 포함하는 종속성을 그룹화하여 시각적 복잡성을 줄여 주지만 원격 분석에 고유한 클라우드 역할 이름이 너무 많거나 종속성 유형이 너무 많은 경우 그룹화가 충분하지 않으며 맵을 렌더링할 수 없게 됩니다.

이 문제를 해결하려면 클라우드 역할 이름, 종속성 유형 및 종속성 대상 필드를 올바르게 설정하도록 계측을 변경해야 합니다.

* 종속성 대상은 종속성의 논리적 이름을 나타내야 합니다. 대부분의 경우 종속성의 서버 또는 리소스 이름과 동일합니다. 예를 들어 HTTP 종속성의 경우 호스트 이름으로 설정됩니다. 한 요청에서 다른 요청으로 변경되는 고유 ID 또는 매개 변수를 포함해서는 안됩니다.

* 종속성 유형은 종속성의 논리적 유형을 나타내야 합니다. 예를 들어 HTTP, SQL 또는 Azure Blob은 일반적인 종속성 유형입니다. 고유 ID를 포함해서는 안됩니다.

* 클라우드 역할 이름의 용도는 [위의 섹션](#set-or-override-cloud-role-name)에 설명되어 있습니다.

## <a name="portal-feedback"></a>포털 사용자 의견

사용자 의견을 제공하려면 사용자 의견 옵션을 사용하세요.

![MapLink-1 이미지](./media/app-map/14-updated.png)

## <a name="next-steps"></a>다음 단계

* Application Insights에서 상관 관계가 작동하는 방식에 대한 자세한 내용은 [원격 분석 상관 관계 문서](correlation.md)를 참조하세요.
* [엔드투엔드 트랜잭션 진단 환경](transaction-diagnostics.md)은 모든 Application Insights 모니터링 구성 요소에서 서버 쪽 원격 분석을 단일 보기로 상관 관계를 지정합니다.
* ASP.NET Core 및 ASP.NET의 고급 상관 관계 시나리오는 [사용자 지정 작업 추적](custom-operations-tracking.md) 문서를 참조하세요.
