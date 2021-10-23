---
title: .NET, Node.js 및 Python 애플리케이션에 Azure Monitor OpenTelemetry 사용
description: OpenTelemetry를 사용하여 애플리케이션에서 Azure Monitor 사용하도록 설정하는 방법에 대한 지침을 제공합니다.
ms.topic: conceptual
ms.date: 10/11/2021
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 28200f938f014d49ca01a49128e78a30a3005d59
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130240094"
---
# <a name="enable-azure-monitor-opentelemetry-exporter-for-net-nodejs-and-python-applications-preview"></a>.NET, Node.js 및 Python 애플리케이션에 Azure Monitor OpenTelemetry 내보내기 사용(미리 보기)

이 문서에서는 OpenTelemetry 기반 Azure Monitor 미리 보기 제품을 사용하도록 설정하고 구성하는 방법을 설명합니다. 이 문서의 지침을 완료하면 OpenTelemetry 추적을 Azure Monitor Application Insights 보낼 수 있습니다. OpenTelemetry에 대한 자세한 내용은 [OpenTelemetry 개요](opentelemetry-overview.md) 또는 [OpenTelemetry FAQ를 참조하세요.](/azure/azure-monitor/faq#opentelemetry)

> [!IMPORTANT]
> .NET, Node.js 및 Python 애플리케이션용 Azure Monitor OpenTelemetry Exporter는 현재 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="limitations-of-preview-release"></a>미리 보기 릴리스의 제한 사항

### <a name="net"></a>[.NET](#tab/net)

이 미리 보기가 적합한지 신중하게 고려하세요. **분산 추적만 사용하도록 설정하고** _다음을 제외합니다._
 - 메트릭 API(사용자 지정 메트릭, [사전 집계된 메트릭)](pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)
 - [라이브 메트릭](live-stream.md)
 - 로깅 API(콘솔 로그, 로깅 라이브러리 등)
 - 처리되지 않은 예외의 자동 캡처
 - [프로파일러](profiler-overview.md)
 - [스냅샷 디버거](snapshot-debugger.md)
 - 오프라인 디스크 스토리지
 - [Azure AD 인증](azure-ad-authentication.md)
 - [샘플링](sampling.md)
 - Azure 환경에서 클라우드 역할 이름 및 클라우드 역할 인스턴스의 자동 채우기
 - [애플리케이션 Insights JavaScript SDK를](javascript.md) 사용하는 경우 사용자 ID 및 인증된 사용자 ID의 자동 채우기
 - 사용자 IP의 자동 채우기(위치 특성 확인)
 - [작업 이름을](correlation.md#data-model-for-telemetry-correlation) 재정의하는 기능
 - 사용자 ID 또는 인증된 사용자 ID를 수동으로 설정하는 기능
 - 종속성 원격 분석으로 작업 이름 전파
 - Azure Functions Worker를 통한 분산 추적 컨텍스트 전파(계측 라이브러리)

전체 기능 환경이 필요한 경우 OpenTelemetry 기반 제품이 완성될 때까지 기존 애플리케이션 [Insights ASP.NET](asp-net.md) 사용하거나 SDK를 [ASP.NET Core](asp-net-core.md) 합니다.

### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

이 미리 보기가 적합한지 신중하게 고려하세요. 분산 추적만 사용하도록 설정하고 _다음을 제외합니다._
 - 메트릭 API(사용자 지정 메트릭, [사전 집계된 메트릭)](pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)
 - [라이브 메트릭](live-stream.md)
 - 로깅 API(콘솔 로그, 로깅 라이브러리 등)
 - 처리되지 않은 예외의 자동 캡처
 - [Azure AD 인증](azure-ad-authentication.md)
 - [샘플링](sampling.md)
 - Azure 환경에서 클라우드 역할 이름 및 클라우드 역할 인스턴스의 자동 채우기
 - [애플리케이션 Insights JavaScript SDK를](javascript.md) 사용하는 경우 사용자 ID 및 인증된 사용자 ID의 자동 채우기
 - 사용자 IP의 자동 채우기(위치 특성 확인)
 - [작업 이름을](correlation.md#data-model-for-telemetry-correlation) 재정의하는 기능
 - 사용자 ID 또는 인증된 사용자 ID를 수동으로 설정하는 기능
 - 종속성 원격 분석으로 작업 이름 전파

전체 기능 환경이 필요한 경우 OpenTelemetry 기반 제품이 완성될 때까지 기존 [애플리케이션 Insights Node.js SDK를](nodejs.md) 사용해야 합니다.

> [!WARNING] 
> 현재 이 내보내기는 Node.js 환경에서만 작동합니다. 웹/브라우저 시나리오에 [애플리케이션 Insights JavaScript SDK를](javascript.md) 사용합니다.

### <a name="python"></a>[Python](#tab/python)

이 미리 보기가 적합한지 신중하게 고려하세요. **분산 추적만 사용하도록 설정하고** _다음을 제외합니다._
 - 메트릭 API(사용자 지정 메트릭, [사전 집계된 메트릭)](pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)
 - [라이브 메트릭](live-stream.md)
 - 로깅 API(콘솔 로그, 로깅 라이브러리 등)
 - 처리되지 않은 예외의 자동 캡처
 - 오프라인 디스크 스토리지
 - [Azure AD 인증](azure-ad-authentication.md)
 - [샘플링](sampling.md)
 - Azure 환경에서 클라우드 역할 이름 및 클라우드 역할 인스턴스의 자동 채우기
 - [애플리케이션 Insights JavaScript SDK를](javascript.md) 사용하는 경우 사용자 ID 및 인증된 사용자 ID의 자동 채우기
 - 사용자 IP의 자동 채우기(위치 특성 확인)
 - [작업 이름을](correlation.md#data-model-for-telemetry-correlation) 재정의하는 기능
 - 사용자 ID 또는 인증된 사용자 ID를 수동으로 설정하는 기능
 - 종속성 원격 분석으로 작업 이름 전파
 - Azure Functions Worker를 통한 분산 추적 컨텍스트 전파(계측 라이브러리)

전체 기능 환경이 필요한 경우 OpenTelemetry 기반 제품이 완성될 때까지 기존 [애플리케이션 Insights Python-OpenCensus SDK를](opencensus-python.md) 사용해야 합니다.

---

## <a name="get-started"></a>시작

이 섹션의 단계를 수행하면 OpenTelemetry를 사용하여 애플리케이션을 계측할 수 있습니다.

### <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험용 Azure 구독 만들기](https://azure.microsoft.com/free/)
- 애플리케이션 Insights 리소스 - [애플리케이션 Insights 리소스 만들기](create-workspace-resource.md#create-workspace-based-resource)

### <a name="net"></a>[.NET](#tab/net)

- 공식적으로 지원되는 [.NET Core](https://dotnet.microsoft.com/download/dotnet) 버전을 사용하는 애플리케이션 또는 [](https://dotnet.microsoft.com/download/dotnet-framework)  >=  `.NET Framework 4.6.1` .NET Framework.


### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

- 공식적으로 [지원되는 버전의](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter#currently-supported-environments) Node.js 런타임을 사용하는 애플리케이션입니다.
  - [OpenTelemetry 지원 런타임](https://github.com/open-telemetry/opentelemetry-js#supported-runtimes)
  - [openTelemetry 내보내기 지원 런타임 Azure Monitor](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter#currently-supported-environments)

### <a name="python"></a>[Python](#tab/python)

- 버전 3.6+를 사용하는 Python 애플리케이션


---

### <a name="install-the-client-libraries"></a>클라이언트 라이브러리 설치

#### <a name="net"></a>[.NET](#tab/net)

최신 [Azure.Monitor.OpenTelemetry.Exporter](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.Exporter) NuGet 패키지를 설치합니다.

```dotnetcli
dotnet add package --prerelease Azure.Monitor.OpenTelemetry.Exporter 
```

"'Azure.Monitor.OpenTelemetry.Exporter' 패키지에 사용할 수 있는 버전이 없습니다."와 같은 오류가 발생하면 NuGet 패키지 원본의 설정이 누락되었을 수 있습니다. 옵션을 사용하여 원본을 지정하려고 할 수 `-s` 있습니다.

```dotnetcli
# Install the latest package with NuGet package source specified
dotnet add package --prerelease Azure.Monitor.OpenTelemetry.Exporter -s https://api.nuget.org/v3/index.json
```

#### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

다음 패키지를 설치합니다.

- [@opentelemetry/sdk-trace-base](https://www.npmjs.com/package/@opentelemetry/sdk-trace-base)
- [@opentelemetry/sdk-trace-node](https://www.npmjs.com/package/@opentelemetry/sdk-trace-node)
- [@azure/monitor-opentelemetry-exporter](https://www.npmjs.com/package/@azure/monitor-opentelemetry-exporter)

```sh
npm install @opentelemetry/sdk-trace-base
npm install @opentelemetry/sdk-trace-node
npm install @azure/monitor-opentelemetry-exporter
```

다음 패키지는 이 문서의 후반부에서 설명하는 몇 가지 특정 시나리오에도 사용됩니다.

- [@opentelemetry/api](https://www.npmjs.com/package/@opentelemetry/api)
- [@opentelemetry/resources](https://www.npmjs.com/package/@opentelemetry/resources)
- [@opentelemetry/semantic-conventions](https://www.npmjs.com/package/@opentelemetry/semantic-conventions)
- [@opentelemetry/instrumentation-http](https://www.npmjs.com/package/@opentelemetry/instrumentation-http)

```sh
npm install @opentelemetry/api
npm install @opentelemetry/resources
npm install @opentelemetry/semantic-conventions
npm install @opentelemetry/instrumentation-http
```

#### <a name="python"></a>[Python](#tab/python)

최신 [azure-monitor-opentelemetry-exporter](https://pypi.org/project/azure-monitor-opentelemetry-exporter/) Pypi 패키지를 설치합니다.

```sh
pip install azure-monitor-opentelemetry-exporter 
```

---

### <a name="enable-azure-monitor-application-insights"></a>Azure Monitor 애플리케이션 Insights 사용

#### <a name="add-opentelemetry-instrumentation-code"></a>OpenTelemetry 계측 코드 추가

##### <a name="net"></a>[.NET](#tab/net)

다음 코드에서는 OpenTelemetry TracerProvider를 설정하여 C# 콘솔 애플리케이션에서 OpenTelemetry를 사용하도록 설정하는 것을 보여 줍니다. 이 코드는 애플리케이션 시작에 있어야 합니다. ASP.NET Core 경우 일반적으로 `ConfigureServices` 애플리케이션 클래스의 메서드에서 `Startup` 수행됩니다. ASP.NET 애플리케이션의 경우 일반적으로 에서 `Global.aspx.cs` 수행됩니다.

```csharp
using System.Diagnostics;
using Azure.Monitor.OpenTelemetry.Exporter;
using OpenTelemetry;
using OpenTelemetry.Trace;

public class Program
{
    private static readonly ActivitySource MyActivitySource = new ActivitySource(
        "OTel.AzureMonitor.Demo");

    public static void Main()
    {
        using var tracerProvider = Sdk.CreateTracerProviderBuilder()
            .AddSource("OTel.AzureMonitor.Demo")
            .AddAzureMonitorTraceExporter(o =>
            {
                o.ConnectionString = "<Your Connection String>";
            })
            .Build();

        using (var activity = MyActivitySource.StartActivity("TestActivity"))
        {
            activity?.SetTag("CustomTag1", "Value1");
            activity?.SetTag("CustomTag2", "Value2");
        }

        System.Console.WriteLine("Press Enter key to exit.");
        System.Console.ReadLine();
    }
}
```

> [!NOTE]
> `Activity` `ActivitySource` 네임스페이스의 및 클래스는 `System.Diagnostics` 각각 및 의 OpenTelemetry 개념을 `Span` `Tracer` 나타냅니다. 그리고 를 `ActivitySource` 사용하는 대신 해당 생성자를 사용하여 직접 만듭니다(각 생성자는 를 `TracerProvider` 사용하여 에 명시적으로 [`ActivitySource`](https://github.com/open-telemetry/opentelemetry-dotnet/tree/main/docs/trace/customizing-the-sdk#activity-source) 연결되어야 `TracerProvider` `AddSource()` 합니다). OpenTelemetry 추적 API의 일부가 .NET 런타임에 직접 통합되기 때문입니다. [자세히 알아보기](https://github.com/open-telemetry/opentelemetry-dotnet/blob/main/src/OpenTelemetry.Api/README.md#introduction-to-opentelemetry-net-tracing-api).

##### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

다음 코드에서는 간단한 Node.js 응용 프로그램에서 OpenTelemetry를 사용 하도록 설정 하는 방법을 보여 줍니다.

```typescript
const { AzureMonitorTraceExporter } = require("@azure/monitor-opentelemetry-exporter");
const { BatchSpanProcessor, Span } = require("@opentelemetry/sdk-trace-base");
const { NodeTracerProvider } = require("@opentelemetry/sdk-trace-node");

const provider = new NodeTracerProvider();
provider.register();

// Create an exporter instance
const exporter = new AzureMonitorTraceExporter({
  instrumentationKey: "<Your Connection String>"
});

// Add the exporter to the provider
provider.addSpanProcessor(
  new BatchSpanProcessor(exporter, {
    bufferTimeout: 15000,
    bufferSize: 1000
  })
);
// Create a span. A span must be closed.
const parentSpan = tracer.startSpan("main");
for (let i = 0; i < 10; i += 1) {
   doWork(parentSpan);
}
// Be sure to end the span.
parentSpan.end();

function doWork(parent: Span) {
  // Start another span. In this example, the main method already started a
  // span, so that'll be the parent span, and this will be a child span.
  const ctx = opentelemetry.trace.setSpan(opentelemetry.context.active(), parent);
  const span = tracer.startSpan("doWork", undefined, ctx);
  // simulate some random work.
  for (let i = 0; i <= Math.floor(Math.random() * 40000000); i += 1) {
    // empty
  }
  // Set attributes to the span.
  span.setAttribute("key", "value");
  // Annotate our span to capture metadata about our operation
  span.addEvent("invoking doWork");
  span.end();
}
```

##### <a name="python"></a>[Python](#tab/python)

다음 코드에서는 간단한 Python 응용 프로그램에서 OpenTelemetry를 사용 하도록 설정 하는 방법을 보여 줍니다.

```python
import os
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

from azure.monitor.opentelemetry.exporter import AzureMonitorTraceExporter

exporter = AzureMonitorTraceExporter.from_connection_string(
    "<Your Connection String>"
)

trace.set_tracer_provider(TracerProvider())
tracer = trace.get_tracer(__name__)
span_processor = BatchSpanProcessor(exporter)
trace.get_tracer_provider().add_span_processor(span_processor)

with tracer.start_as_current_span("hello"):
    print("Hello, World!")

```

---

> [!TIP]
> 인기 있는 프레임 워크/라이브러리를 통해 원격 분석을 자동으로 수집 하는 [계측 라이브러리](#instrumentation-libraries) 를 추가 합니다.

#### <a name="set-application-insights-connection-string"></a>연결 문자열 Application Insights 설정

`<Your Connection String>`위의 코드에서 자리 표시자를 Application Insights 리소스의 연결 문자열로 바꿉니다.

:::image type="content" source="media/opentelemetry/connection-string.png" alt-text="Application Insights 연결 문자열의 스크린샷":::

#### <a name="confirm-data-is-flowing"></a>데이터 흐름 확인

응용 프로그램을 실행 하 고 Azure Portal에서 Application Insights 리소스 탭을 엽니다. 데이터가 포털에 표시 되는 데 몇 분 정도 걸릴 수 있습니다.

> [!NOTE]
> 응용 프로그램을 실행할 수 없거나 예상 대로 데이터를 가져올 수 없는 경우 [문제 해결](#troubleshooting)으로 이동 하세요.

:::image type="content" source="media/opentelemetry/server-requests.png" alt-text="서버 요청 및 서버 응답 시간이 강조 표시 된 Application Insights 개요 탭의 스크린샷":::

> [!IMPORTANT]
> 동일한 Application Insights 리소스에 대 한 원격 분석을 내보내는 서비스가 둘 이상 있는 경우 응용 프로그램 맵에 올바르게 표시 되도록 [클라우드 역할 이름을 설정](#set-cloud-role-name-and-cloud-role-instance) 해야 합니다.

> [!NOTE]
> Application Insights 계측 사용의 일부로 Microsoft에 진단 데이터를 수집 하 여 보냅니다. 이 데이터는 Application Insights를 실행 하 고 개선 하는 데 도움이 됩니다. 필수가 아닌 데이터 수집을 사용 하지 않도록 설정할 수 있는 옵션이 있습니다. [자세한 정보](./statsbeat.md).

## <a name="set-cloud-role-name-and-cloud-role-instance"></a>클라우드 역할 이름 및 클라우드 역할 인스턴스 설정

[리소스](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/resource/sdk.md#resource-sdk) 특성을 통해 [클라우드 역할 이름](app-map.md#understanding-cloud-role-name-within-the-context-of-the-application-map) 및 클라우드 역할 인스턴스를 설정할 수 있습니다. 이렇게 하면 클라우드 역할 이름 및 클라우드 역할 인스턴스가 기본값에서 팀에 적합 한 값으로 업데이트 됩니다. 응용 프로그램 맵에 노드 아래의 이름으로 노출 됩니다. 클라우드 역할 이름은 `service.namespace` 및 `service.name` 특성을 사용 하지만 `service.name` `service.namespace` 가 설정 되지 않은 경우에는로 대체 됩니다. 클라우드 역할 인스턴스는 `service.instance.id` 특성 값을 사용 합니다.

### <a name="net"></a>[.NET](#tab/net)

```csharp
// Setting Role name and Role instance
var resourceAttributes = new Dictionary<string, object> {
    { "service.name", "my-service" },
    { "service.namespace", "my-namespace" },
    { "service.instance.id", "my-instance" }};
var resourceBuilder = ResourceBuilder.CreateDefault().AddAttributes(resourceAttributes);
// Done setting Role name and Role instance

// Set ResourceBuilder on the provider
using var tracerProvider = Sdk.CreateTracerProviderBuilder()
        .SetResourceBuilder(resourceBuilder)
        .AddSource("OTel.AzureMonitor.Demo")
        .AddAzureMonitorTraceExporter(o =>
        {
            o.ConnectionString = "<Your Connection String>";
        })
        .Build();
```


### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

```typescript
...
import { NodeTracerProvider, NodeTracerConfig } from "@opentelemetry/sdk-trace-node";
import { Resource } from "@opentelemetry/resources";
import { SemanticResourceAttributes } from "@opentelemetry/semantic-conventions";

// ----------------------------------------
// Setting Role name and role instance
// ----------------------------------------
const config: NodeTracerConfig = {
        resource: new Resource({
            [SemanticResourceAttributes.SERVICE_NAME]: "my-helloworld-service",
            [SemanticResourceAttributes.SERVICE_NAMESPACE]: "my-namespace",
            [SemanticResourceAttributes.SERVICE_INSTANCE_ID]: "my-instance",
        }),
    };
// ----------------------------------------
// Done setting Role name and role instance
// ----------------------------------------
const provider = new NodeTracerProvider(config);
...
```

### <a name="python"></a>[Python](#tab/python)

```python
...
from opentelemetry.sdk.resources import SERVICE_NAME, SERVICE_NAMESPACE, SERVICE_INSTANCE_ID, Resource
trace.set_tracer_provider(
    TracerProvider(
        resource=Resource.create(
            {
                SERVICE_NAME: "my-helloworld-service",
# ----------------------------------------
# Setting Role name and role instance
# ----------------------------------------
                SERVICE_NAMESPACE: "my-namespace",
                SERVICE_INSTANCE_ID: "my-instance",
# ----------------------------------------------
# Done setting Role name and role instance
# ----------------------------------------------
            }
        )
    )
)
...
```

---

참조: [리소스 의미 체계 규칙](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/resource/semantic_conventions/README.md)

## <a name="sampling"></a>샘플링

샘플링은 OpenTelemetry에서 지원 되지만 지금은 OpenTelemetry 내보내기에서 Azure Monitor 지원 되지 않습니다.

> [!WARNING]
> OpenTelemetry에서 샘플링을 사용 하도록 설정 하면 표준 및 로그 기반 메트릭이 매우 부정확 하 여 모든 Application Insights 환경에 부정적인 영향을 미칠 수 있습니다. 또한 기존 Application Insights sdk와 함께 샘플링을 사용 하도록 설정 하면 추적이 중단 됩니다.

## <a name="instrumentation-libraries"></a>계측 라이브러리
<!-- Microsoft has tested and validated that the following instrumentation libraries will work with the **Preview** Release. -->
다음 라이브러리는 미리 보기 릴리스와 함께 작동 하도록 유효성을 검사 합니다.

> [!WARNING]
> 계측 라이브러리는 실험적 OpenTelemetry 사양을 기반으로 합니다. Microsoft의 **미리 보기** 지원 약정은 아래에 나열 된 라이브러리가 데이터를 Azure Monitor Application Insights 내보낼 수 있도록 하기 위한 것 이지만, 주요 변경 내용 또는 실험적 매핑이 일부 데이터 요소를 차단할 수 있습니다.

### <a name="http"></a>HTTP

#### <a name="net"></a>[.NET](#tab/net)

- [ASP.NET](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNet/README.md) 버전: [1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.AspNet/1.0.0-rc7)
- [ASP.NET Core](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNetCore/README.md) 버전: [1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.AspNetCore/1.0.0-rc7)
- [HTTP 클라이언트](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.Http/README.md) 버전: [1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.Http/1.0.0-rc7)

#### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

- [http/https](https://github.com/open-telemetry/opentelemetry-js/tree/main/experimental/packages/opentelemetry-instrumentation-http/README.md) 버전: [0.26.0](https://www.npmjs.com/package/@opentelemetry/instrumentation-http/v/0.26.0)


#### <a name="python"></a>[Python](#tab/python)

- [Django](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-django) 버전: [0.24 b0](https://pypi.org/project/opentelemetry-instrumentation-django/0.24b0/)
- [Flask](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-flask) 버전: [0.24 b0](https://pypi.org/project/opentelemetry-instrumentation-flask/0.24b0/)
- [요청](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-requests) 버전: [0.24 b0](https://pypi.org/project/opentelemetry-instrumentation-requests/0.24b0/)

---

### <a name="database"></a>데이터베이스

#### <a name="net"></a>[.NET](#tab/net)

- [SQL 클라이언트](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.SqlClient/README.md) 버전: [1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.SqlClient/1.0.0-rc7)

#### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

- [mysql](https://github.com/open-telemetry/opentelemetry-js-contrib/tree/main/plugins/node/opentelemetry-instrumentation-mysql) 버전: [0.25.0](https://www.npmjs.com/package/@opentelemetry/instrumentation-mysql/v/0.25.0)

#### <a name="python"></a>[Python](#tab/python)

- [Psycopg2](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-psycopg2) 버전: [0.24 b0](https://pypi.org/project/opentelemetry-instrumentation-psycopg2/0.24b0/)

---

> [!NOTE]
> **미리 보기** 제공에는 HTTP 및 데이터베이스 요청을 처리 하는 instrumentations 포함 됩니다. 자세히 알아보려면 [OpenTelemetry 의미 체계 규칙](https://github.com/open-telemetry/opentelemetry-specification/tree/main/specification/trace/semantic_conventions) 을 참조 하세요.

## <a name="modify-telemetry"></a>원격 분석 수정

### <a name="add-span-attributes"></a>범위 특성 추가

다음 두 가지 방법 중 하나를 사용 하 여 Span 특성을 추가할 수 있습니다.
1. [계측 라이브러리](#instrumentation-libraries)에서 제공 하는 옵션 사용.
2. 사용자 지정 범위 프로세서 추가

이러한 특성에는 원격 분석에 사용자 지정 비즈니스 속성을 추가 하는 작업이 포함 될 수 있습니다. 특성을 사용 하 여 사용자 ID 또는 클라이언트 IP와 같은 Application Insights 스키마의 선택적 필드를 설정할 수도 있습니다.

> [!TIP]
> "계측 라이브러리에서 제공 하는 옵션" (사용 가능한 경우)을 사용 하는 이점은 전체 컨텍스트를 사용할 수 있다는 것입니다. 즉, 사용자가 추가 특성을 추가 하거나 필터링 하도록 선택할 수 있습니다. 예를 들어, HttpClient 계측 라이브러리에는 사용자에 게 httpRequestMessage에 대 한 액세스 권한을 부여 하는 기능이 포함 되어 있습니다 .이 라이브러리에서 모든 항목을 선택 하 고 특성으로 저장할 수 있습니다.

#### <a name="add-custom-property"></a>사용자 지정 속성 추가

활동/범위에 추가 되는 모든 [특성](#add-span-attributes) 은 사용자 지정 속성으로 내보내집니다. Application Insights의 요청 및/또는 종속성 테이블에서 _Customdimensions_ 필드를 채웁니다.

##### <a name="net"></a>[.NET](#tab/net)

1. 많은 계측 라이브러리에서 보강 옵션을 제공 합니다. 지침은 개별 계측 라이브러리의 추가 정보를 참조 하세요.
    - [ASP.NET](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNet/README.md#enrich)
    - [ASP.NET Core](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNetCore/README.md#enrich)
    - [HttpClient 및 HttpWebRequest](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.Http/README.md#enrich)

2. 사용자 지정 프로세서 사용:

> [!TIP]
> Azure monitor 내보내기 전에 아래에 표시 된 프로세서를 추가 합니다.

```csharp
using var tracerProvider = Sdk.CreateTracerProviderBuilder()
        .AddSource("OTel.AzureMonitor.Demo")
        .AddProcessor(new ActivityEnrichingProcessor())
        .AddAzureMonitorTraceExporter(o =>
        {
                o.ConnectionString = "<Your Connection String>"
        })
        .Build();
```

`ActivityEnrichingProcessor.cs`아래 코드를 사용 하 여 프로젝트에를 추가 합니다.

```csharp
using System.Diagnostics;
using OpenTelemetry;
using OpenTelemetry.Trace;

public class ActivityEnrichingProcessor : BaseProcessor<Activity>
{
    public override void OnEnd(Activity activity)
    {
        // The updated activity will be available to all processors which are called after this processor.
        activity.DisplayName = "Updated-" + activity.DisplayName;
        activity.SetTag("CustomDimension1", "Value1");
        activity.SetTag("CustomDimension2", "Value2");
    }
}
```


##### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

사용자 지정 프로세서 사용:

> [!TIP]
> Azure monitor 내보내기 전에 아래에 표시 된 프로세서를 추가 합니다.

```typescript
import { AzureMonitorTraceExporter } from "@azure/monitor-opentelemetry-exporter";
import { NodeTracerProvider } from "@opentelemetry/sdk-trace-node";
import { ReadableSpan, SimpleSpanProcessor, Span, SpanProcessor } from "@opentelemetry/sdk-trace-base";

class SpanEnrichingProcessor implements SpanProcessor{
    forceFlush(): Promise<void>{
        return Promise.resolve();
    }
    shutdown(): Promise<void>{
        return Promise.resolve();
    }
    onStart(_span: Span): void{}
    onEnd(span: ReadableSpan){
        span.attributes["CustomDimension1"] = "value1";
        span.attributes["CustomDimension2"] = "value2";
    }
}

const provider = new NodeTracerProvider();
const azureExporter = new AzureMonitorTraceExporter();
provider.addSpanProcessor(new SpanEnrichingProcessor());
provider.addSpanProcessor(new SimpleSpanProcessor(azureExporter));

```

##### <a name="python"></a>[Python](#tab/python)

사용자 지정 프로세서 사용:

> [!TIP]
> Azure monitor 내보내기 전에 아래에 표시 된 프로세서를 추가 합니다.

```python
...
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

trace.set_tracer_provider(TracerProvider())
span_processor = BatchSpanProcessor(exporter)
span_enrich_processor = SpanEnrichingProcessor()
trace.get_tracer_provider().add_span_processor(span_enrich_processor)
trace.get_tracer_provider().add_span_processor(span_processor)
...
```

`SpanEnrichingProcessor.py`아래 코드를 사용 하 여 프로젝트에를 추가 합니다.

```python
from opentelemetry.sdk.trace import SpanProcessor

class SpanEnrichingProcessor(SpanProcessor):

    def on_end(self, span):
        span._name = "Updated-" + span.name
        span._attributes["CustomDimension1"] = "Value1"
        span._attributes["CustomDimension2"] = "Value2"

```
---

#### <a name="set-user-ip"></a>사용자 IP 설정

활동/범위에 대 한 특성을 설정 하 여 요청에 대 한 _client_IP_ 필드를 채울 수 있습니다 `http.client_ip` . Application Insights IP 주소를 사용 하 여 사용자 위치 특성을 생성 한 후 [기본적으로 삭제](ip-collection.md#default-behavior)합니다.

##### <a name="net"></a>[.NET](#tab/net)

에서 다음 코드 줄을 변경 하는 것을 제외 하 고 [사용자 지정 속성 추가 예제](#add-custom-property)를 사용 합니다 `ActivityEnrichingProcessor.cs` .

```C#
activity.SetTag("http.client_ip", "<IP Address>");
```

##### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

다음 코드 줄을 변경 하는 경우를 제외 하 고 [사용자 지정 속성 추가 예제](#add-custom-property)를 사용 합니다.

```typescript
...
import { SemanticAttributes } from "@opentelemetry/semantic-conventions";

class SpanEnrichingProcessor implements SpanProcessor{
    ...

    onEnd(span: ReadableSpan){
        span.attributes[SemanticAttributes.HTTP_CLIENT_IP] = "<IP Address>";
    }
}
```

##### <a name="python"></a>[Python](#tab/python)

에서 다음 코드 줄을 변경 하는 것을 제외 하 고 [사용자 지정 속성 추가 예제](#add-custom-property)를 사용 합니다 `SpanEnrichingProcessor.py` .

```python
span._attributes["http.client_ip"] = "<IP Address>"
```

---
<!--

#### Set user ID or authenticated user ID

You can populate the _user_Id_ or _user_Authenticatedid_ field for requests by setting `xyz` or `xyz` attribute on activity/span. User ID is an anonymous user identifier and Authenticated User ID is a known user identifier.

> [!IMPORTANT]
> Consult applicable privacy laws before setting Authenticated User ID.

##### [.NET](#tab/net)

Use the add [custom property example](#add-custom-property), except change out the following lines of code:

```C#
Placeholder
```

##### [Node.js](#tab/nodejs)

Use the add [custom property example](#add-custom-property), except change out the following lines of code:

```typescript
...
import { SemanticAttributes } from "@opentelemetry/semantic-conventions";

class SpanEnrichingProcessor implements SpanProcessor{
    ...

    onEnd(span: ReadableSpan){
        span.attributes[SemanticAttributes.ENDUSER_ID] = "<User ID>";
    }
}
```

##### [Python](#tab/python)

Use the add [custom property example](#add-custom-property), except change out the following lines of code:

```python
span._attributes["enduser.id"] = "<User ID>"
```

---
-->

### <a name="filter-telemetry"></a>필터 원격 분석

다음 방법을 사용 하 여 응용 프로그램을 종료 하기 전에 원격 분석을 필터링 할 수 있습니다.

#### <a name="net"></a>[.NET](#tab/net)

1. 많은 계측 라이브러리에서 필터 옵션을 제공 합니다. 지침은 개별 계측 라이브러리의 추가 정보를 참조 하세요.
    - [ASP.NET](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNet/README.md#filter)
    - [ASP.NET Core](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNetCore/README.md#filter)
    - [HttpClient 및 HttpWebRequest](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.Http/README.md#filter)

2. 사용자 지정 프로세서 사용:
    
    ```csharp
    using var tracerProvider = Sdk.CreateTracerProviderBuilder()
            .AddSource("OTel.AzureMonitor.Demo")
            .AddProcessor(new ActivityFilteringProcessor())
            .AddAzureMonitorTraceExporter(o =>
            {
                    o.ConnectionString = "<Your Connection String>"
            })
            .Build();
    ```
    
    `ActivityFilteringProcessor.cs`아래 코드를 사용 하 여 프로젝트에를 추가 합니다.
    
    ```csharp
    using System.Diagnostics;
    using OpenTelemetry;
    using OpenTelemetry.Trace;
    
    public class ActivityFilteringProcessor : BaseProcessor<Activity>
    {
        public override void OnStart(Activity activity)
        {
            // prevents all exporters from exporting internal activities
            if (activity.Kind == ActivityKind.Internal)
            {
                activity.IsAllDataRequested = false;
            }
        }
    }
    ```


3. 를 사용 하 여 특정 소스를 명시적으로 추가 하지 않은 경우에 `AddSource("ActivitySourceName")` 는 해당 원본을 사용 하 여 만든 활동을 모두 내보내지 않습니다.
    
    <!---
    ### Get Trace ID or Span ID
    You may use X or Y to get trace ID and/or span ID. Adding trace ID and/or span ID to existing logging telemetry enables better correlation when debugging and diagnosing issues.
    
    > [!NOTE]
    > If you are manually creating spans for log-based metrics and alerting, you will need to update them to use the metrics API (after it is released) to ensure accuracy.
    
    ```C#
    Placeholder
    ```
    
    For more information, see [GitHub Repo](link).
    --->

#### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

1. 여러 http 계측 라이브러리에서 제공 하는 url 옵션을 제외 합니다.

    다음은 [http/https 계측 라이브러리](https://github.com/open-telemetry/opentelemetry-js/tree/main/experimental/packages/opentelemetry-instrumentation-http)를 사용 하 여 추적에서 특정 url을 제외 하는 방법의 예입니다.
    
    ```typescript
    ...
    import { HttpInstrumentation, HttpInstrumentationConfig } from "@opentelemetry/instrumentation-http";
    
    ...
    const httpInstrumentationConfig: HttpInstrumentationConfig = {
        ignoreIncomingPaths: [new RegExp(/dc.services.visualstudio.com/i)]
    };
    const httpInstrumentation = new HttpInstrumentation(httpInstrumentationConfig);
    provider.register();
    registerInstrumentations({
        instrumentations: [
            httpInstrumentation,
        ]
    });
    
    ```

2. 사용자 지정 프로세서 사용. 사용자 지정 범위 프로세서를 사용 하 여 특정 범위를 내보내지 않도록 제외할 수 있습니다. 범위를 내보내지 않도록 표시 하려면 `TraceFlag` 를로 설정 `DEFAULT` 합니다.
다음 코드 줄을 변경 하는 경우를 제외 하 고 [사용자 지정 속성 추가 예제](#add-custom-property)를 사용 합니다.

    ```typescript
    ...
    import { SpanKind, TraceFlags } from "@opentelemetry/api";
    
    class SpanEnrichingProcessor implements SpanProcessor{
        ...
    
        onEnd(span: ReadableSpan) {
            if(span.kind == SpanKind.INTERNAL){
                span.spanContext().traceFlags = TraceFlags.NONE;
            }
        }
    }
    ```

#### <a name="python"></a>[Python](#tab/python)

1. 여러 http 계측 라이브러리에서 제공 하는 url 옵션을 제외 합니다.

    [Flask](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-flask) 계측을 사용 하 여 추적에서 특정 url을 제외 하는 방법의 예는 다음과 같습니다.
    
    ```python
    ...
    import flask
    
    from opentelemetry.instrumentation.flask import FlaskInstrumentor
    
    # You may also populate OTEL_PYTHON_FLASK_EXCLUDED_URLS env variable
    # List will consist of comma delimited regexes representing which URLs to exclude
    excluded_urls = "client/.*/info,healthcheck"
    
    FlaskInstrumentor().instrument(excluded_urls=excluded_urls) # Do this before flask.Flask
    app = flask.Flask(__name__)
    ...
    ```

2. 사용자 지정 프로세서 사용. 사용자 지정 범위 프로세서를 사용 하 여 특정 범위를 내보내지 않도록 제외할 수 있습니다. 범위를 내보내지 않도록 표시 하려면 `TraceFlag` 를로 설정 `DEFAULT` 합니다.
    
    ```python
    ...
    from opentelemetry.sdk.trace import TracerProvider
    from opentelemetry.sdk.trace.export import BatchSpanProcessor
    
    trace.set_tracer_provider(TracerProvider())
    span_processor = BatchSpanProcessor(exporter)
    span_filter_processor = SpanFilteringProcessor()
    trace.get_tracer_provider().add_span_processor(span_filter_processor)
    trace.get_tracer_provider().add_span_processor(span_processor)
    ...
    ```
    
    `SpanFilteringProcessor.py`아래 코드를 사용 하 여 프로젝트에를 추가 합니다.
    
    ```python
    from opentelemetry.trace import SpanContext, SpanKind, TraceFlags
    from opentelemetry.sdk.trace import SpanProcessor
    
    class SpanFilteringProcessor(SpanProcessor):
    
        # prevents exporting spans from internal activities
        def on_start(self, span):
            if span._kind is SpanKind.INTERNAL:
                span._context = SpanContext(
                    span.context.trace_id,
                    span.context.span_id,
                    span.context.is_remote,
                    TraceFlags.DEFAULT,
                    span.context.trace_state,
                )
    
    ```
    
    <!-- For more information, see [GitHub Repo](link). -->
    <!---
    ### Get Trace ID or Span ID
    You may use X or Y to get trace ID and/or span ID. Adding trace ID and/or span ID to existing logging telemetry enables better correlation when debugging and diagnosing issues.
    
    > [!NOTE]
    > If you are manually creating spans for log-based metrics and alerting, you will need to update them to use the metrics API (after it is released) to ensure accuracy.
    
    ```python
    Placeholder
    ```
    
    For more information, see [GitHub Repo](link).
    --->

---

## <a name="enable-otlp-exporter"></a>이상 기능 내보내기 사용

Azure Monitor 내보내기와 함께 (OpenTelemetry Protocol) 내보내기를 사용 하도록 설정 하 여 두 위치에 원격 분석을 보낼 수 있습니다.

> [!NOTE]
> 이상에서 전용 LP 내보내기가 표시 됩니다. Microsoft는 정식 LP 내보내기 또는 구성 요소 또는 타사의 다운스트림 환경을 공식적으로 지원 하지 않습니다. Azure 지원 경계 밖의 OpenTelemetry 문제에 대 한 [OpenTelemetry-수집기](https://github.com/open-telemetry/opentelemetry-collector) 문제를 여는 것이 좋습니다.

#### <a name="net"></a>[.NET](#tab/net)

1. 프로젝트에 [OpenTelemetry](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.Exporter) 와 함께 [OpenTelemetry OpenTelemetryProtocol](https://www.nuget.org/packages/OpenTelemetry.Exporter.OpenTelemetryProtocol/) 패키지를 설치 합니다.

2. 다음 코드 조각을 추가 합니다. 이 예에서는 OpenTelemetry 수집기에 수집기가 실행 되 고 있다고 가정 합니다. 자세한 내용은 [여기](https://github.com/open-telemetry/opentelemetry-dotnet/blob/main/examples/Console/TestOtlpExporter.cs)에서 예제를 참조 하세요.

```csharp
// sends data to Application Insights as well as OTLP
using var tracerProvider = Sdk.CreateTracerProviderBuilder()
        .AddSource("OTel.AzureMonitor.Demo")
        .AddAzureMonitorTraceExporter(o =>
        {
            o.ConnectionString = "<Your Connection String>"
        })
        .AddOtlpExporter()
        .Build();
```


#### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

1. 프로젝트에서 [Azure Monitor OpenTelemetry 내보내기와](https://www.npmjs.com/package/@azure/monitor-opentelemetry-exporter) 함께 [OpenTelemetry 수집기 내보내기](https://www.npmjs.com/package/@opentelemetry/exporter-otlp-http) 패키지를 설치 합니다.


```sh
    npm install @opentelemetry/exporter-otlp-http
    npm install @azure/monitor-opentelemetry-exporter
```

2. 다음 코드 조각을 추가 합니다. 이 예에서는 OpenTelemetry 수집기에 수집기가 실행 되 고 있다고 가정 합니다. 자세한 내용은 [여기](https://github.com/open-telemetry/opentelemetry-js/tree/main/examples/otlp-exporter-node)에서 예제를 참조 하세요.

```typescript
const { BasicTracerProvider, SimpleSpanProcessor } = require('@opentelemetry/sdk-trace-base');
const { OTLPTraceExporter } = require('@opentelemetry/exporter-otlp-http');

const provider = new BasicTracerProvider();
const azureMonitorExporter = new AzureMonitorTraceExporter({
  instrumentationKey: os.environ["APPLICATIONINSIGHTS_CONNECTION_STRING"]
});
const otlpExporter = new OTLPTraceExporter();
provider.addSpanProcessor(new SimpleSpanProcessor(azureMonitorExporter));
provider.addSpanProcessor(new SimpleSpanProcessor(otlpExporter));
provider.register();
```

#### <a name="python"></a>[Python](#tab/python)

1. [Opentelemetry](https://pypi.org/project/azure-monitor-opentelemetry-exporter/) 및 [opentelemetry-lp](https://pypi.org/project/opentelemetry-exporter-otlp/) 패키지를 설치 합니다.

2. 다음 코드 조각을 추가 합니다. 이 예에서는 OpenTelemetry 수집기에 수집기가 실행 되 고 있다고 가정 합니다. 자세한 내용은이 [추가](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/monitor/azure-monitor-opentelemetry-exporter/samples/traces#collector)정보를 참조 하세요.

```python
from opentelemetry import trace 

from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

from azure.monitor.opentelemetry.exporter import AzureMonitorTraceExporter

trace.set_tracer_provider(TracerProvider())
tracer = trace.get_tracer(__name__) 

exporter = AzureMonitorTraceExporter.from_connection_string(
    "<Your Connection String>"
)
otlp_exporter = OTLPSpanExporter(endpoint="http://localhost:4317")
span_processor = BatchSpanProcessor(otlp_exporter) 
trace.get_tracer_provider().add_span_processor(span_processor)

with tracer.start_as_current_span("test"):
    print("Hello world!")
```

---

## <a name="troubleshooting"></a>문제 해결

### <a name="enable-diagnostic-logging"></a>진단 로깅 사용

#### <a name="net"></a>[.NET](#tab/net)

Azure Monitor 내보내기는 자체 내부 로깅에 EventSource를 사용 합니다. "OpenTelemetry" 라는 소스를 옵트인 하 여 모든 EventListener에서 내보내기 로그를 사용할 수 있습니다. 자세한 문제 해결 단계는 [ OpenTelemetry 문제 해결](https://github.com/open-telemetry/opentelemetry-dotnet/tree/main/src/OpenTelemetry#troubleshooting) 을 참조 하세요.

#### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

Azure Monitor 내보내기에서 내부 로그에 OpenTelemetry API로 거를 사용 합니다 .이는 다음 코드를 사용 하 여 활성화할 수 있습니다. 

```typescript
const { diag, DiagConsoleLogger, DiagLogLevel } = require("@opentelemetry/api");
const { NodeTracerProvider } = require("@opentelemetry/sdk-trace-node");

const provider = new NodeTracerProvider();
diag.setLogger(new DiagConsoleLogger(), DiagLogLevel.ALL);
provider.register();
```

#### <a name="python"></a>[Python](#tab/python)

Azure Monitor 내보내기는 자체 내부 로깅을 위해 Python 표준 로깅 [라이브러리](https://docs.python.org/3/library/logging.html) 를 사용 합니다. OpenTelemetry API 및 Azure Monitor 내보내기 로그는 일반적으로 비정상적인 활동에 대 한 심각도 수준 경고 또는 오류와 정상/성공한 활동에 대 한 정보로 기록 됩니다. 기본 Python 로깅 라이브러리는 기본적으로 심각도 수준을 경고로 설정 하므로 심각도 수준을 변경 하 여이 심각도의 로그를 확인 해야 합니다. 다음은 모든 심각도의 로그를 콘솔과 파일로 출력 하는 방법의 예입니다.

```python
...
import logging

logging.basicConfig(format="%(asctime)s:%(levelname)s:%(message)s", level=logging.DEBUG)

logger = logging.getLogger(__name__)
file = logging.FileHandler("example.log")
stream = logging.StreamHandler()
logger.addHandler(file)
logger.addHandler(stream)
...

```

---

### <a name="known-issues"></a>알려진 문제

Azure Monitor OpenTelemetry 내보내기에 대 한 알려진 문제는 다음과 같습니다. 

- 종속성 원격 분석에서 작업 이름이 누락 되어 오류 및 성능 탭 환경에 부정적인 영향을 줍니다.
- 장치 모델이 요청 및 종속성 원격 분석에서 누락 되어 장치 코 호트 분석에 부정적인 영향을 줍니다.
- 데이터베이스 서버 이름의 종속성 이름이 달라 서 다른 서버에서 이름이 같은 테이블을 잘못 집계 합니다.

## <a name="support"></a>고객 지원팀

- 이 문서의 문제 해결 단계를 검토 합니다.
- Azure 지원에 대 한 문제는 [Azure 지원 티켓](https://azure.microsoft.com/support/create-ticket/)을 엽니다.

### <a name="net"></a>[.NET](#tab/net)

OpenTelemetry 문제는 [OpenTelemetry .net 커뮤니티](https://github.com/open-telemetry/opentelemetry-dotnet) 에 직접 문의 하세요.

### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

OpenTelemetry 문제는 [OpenTelemetry JavaScript 커뮤니티](https://github.com/open-telemetry/opentelemetry-js) 에 직접 문의 하세요.

### <a name="python"></a>[Python](#tab/python)

OpenTelemetry 문제는 [OpenTelemetry Python 커뮤니티](https://github.com/open-telemetry/opentelemetry-python) 에 직접 문의 하세요.

---

## <a name="opentelemetry-feedback"></a>OpenTelemetry 피드백

- OpenTelemetry 커뮤니티의 사용자 [의견 설문 조사](https://docs.google.com/forms/d/e/1FAIpQLScUt4reClurLi60xyHwGozgM9ZAz8pNAfBHhbTZ4gFWaaXIRQ/viewform)를 작성 합니다.
- [OpenTelemetry 초기 도입자 Community](https://aka.ms/AzMonOTel/)에 참여 하 여 Microsoft에 자신에 게 알려 주십시오.
- [Microsoft의 기술 Community](https://techcommunity.microsoft.com/t5/azure-monitor/bd-p/AzureMonitor)에서 다른 Azure Monitor 사용자와 참여 하세요.

## <a name="next-steps"></a>다음 단계

### <a name="net"></a>[.NET](#tab/net)

- [Azure Monitor 내보내기 GitHub 리포지토리에서](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/monitor/Azure.Monitor.OpenTelemetry.Exporter)소스 코드를 검토 합니다.
- NuGet 패키지를 설치 하거나, 업데이트를 확인 하거나, 릴리스 정보를 보려면 [Azure Monitor 내보내기 NuGet 패키지](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.Exporter/) 페이지를 방문 하세요.
- [Azure Monitor 예제 응용 프로그램](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/monitor/Azure.Monitor.OpenTelemetry.Exporter/tests/Azure.Monitor.OpenTelemetry.Exporter.Tracing.Customization)을 사용 하 여 Azure Monitor Application Insights 및 OpenTelemetry에 더 친숙 합니다.
- OpenTelemetry 및 해당 커뮤니티에 대해 자세히 알아보려면 [OpenTelemetry .net GitHub 리포지토리](https://github.com/open-telemetry/opentelemetry-dotnet)를 방문 하세요.
- [웹/브라우저 사용자 모니터링](javascript.md) 에서 사용 환경을 사용 하도록 설정 합니다.


### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

- [Azure Monitor 내보내기 GitHub 리포지토리에서](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter)소스 코드를 검토 합니다.
- NPM 패키지를 설치 하거나, 업데이트를 확인 하거나, 릴리스 정보를 보려면 [Azure Monitor 내보내기 NPM 패키지](https://www.npmjs.com/package/@azure/monitor-opentelemetry-exporter) 페이지를 방문 하세요.
- [Azure Monitor 예제 응용 프로그램](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter/samples)을 사용 하 여 Azure Monitor Application Insights 및 OpenTelemetry에 더 친숙 합니다.
- OpenTelemetry 및 해당 커뮤니티에 대 한 자세한 내용을 보려면 [OpenTelemetry JavaScript GitHub 리포지토리](https://github.com/open-telemetry/opentelemetry-js)를 방문 하세요.
- [웹/브라우저 사용자 모니터링](javascript.md) 을 사용 하 여 사용 환경을 활성화 합니다.

### <a name="python"></a>[Python](#tab/python)

- [Azure Monitor 내보내기 GitHub 리포지토리에서](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/monitor/azure-monitor-opentelemetry-exporter/README.md)소스 코드를 검토 합니다.
- PyPI 패키지를 설치 하거나, 업데이트를 확인 하거나, 릴리스 정보를 보려면 [Azure Monitor 내보내기 PyPI 패키지](https://pypi.org/project/azure-monitor-opentelemetry-exporter/) 페이지를 방문 하세요.
-  [Azure Monitor 예제 응용 프로그램](https://github.com/Azure-Samples/azure-monitor-opentelemetry-python)을 사용 하 여 Azure Monitor Application Insights 및 OpenTelemetry에 더 친숙 합니다.
- OpenTelemetry 및 해당 커뮤니티에 대해 자세히 알아보려면 [OpenTelemetry Python GitHub 리포지토리](https://github.com/open-telemetry/opentelemetry-python)를 방문 하세요.
- [웹/브라우저 사용자 모니터링](javascript.md) 을 사용 하 여 사용 환경을 활성화 합니다.

---
