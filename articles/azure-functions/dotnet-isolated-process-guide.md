---
title: '격리 된 프로세스에서 c # Azure Functions를 실행 하기 위한 가이드'
description: '.Net 격리 프로세스를 사용 하 여 .NET 5.0 이상 버전을 지 원하는 Azure에서 c # 함수를 실행 하는 방법에 대해 알아봅니다.'
ms.service: azure-functions
ms.topic: conceptual
ms.date: 06/01/2021
ms.custom: template-concept
recommendations: false
ms.openlocfilehash: b12841b83e4c2f6f2756ddffdd4adc7bde77e73a
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401272"
---
# <a name="guide-for-running-c-azure-functions-in-an-isolated-process"></a>격리 된 프로세스에서 c # Azure Functions를 실행 하기 위한 가이드

이 문서에서는 C#를 사용하여 Azure Functions에서 Out of Process를 실행하는 .NET 격리 프로세스 함수를 개발하는 방법을 소개합니다. Out of Process를 실행하면 Azure Functions 런타임에서 함수 코드를 분리할 수 있습니다. Isolated 프로세스 c # 함수는 .NET 5.0 및 .NET 6.0에서 모두 실행 됩니다. [In-process c # 클래스 라이브러리 함수](functions-dotnet-class-library.md) 는 .net 5.0에서 지원 되지 않습니다. 

| 시작 | 개념| 샘플 |
|--|--|--| 
| <ul><li>[Visual Studio Code 사용](create-first-function-vs-code-csharp.md?tabs=isolated-process)</li><li>[명령줄 도구 사용](create-first-function-cli-csharp.md?tabs=isolated-process)</li><li>[Visual Studio 사용](functions-create-your-first-function-visual-studio.md?tabs=isolated-process)</li></ul> | <ul><li>[호스팅 옵션](functions-scale.md)</li><li>[Monitoring](functions-monitoring.md)</li> | <ul><li>[참조 샘플](https://github.com/Azure/azure-functions-dotnet-worker/tree/main/samples)</li></ul> |

## <a name="why-net-isolated-process"></a>.NET 격리 프로세스를 사용하는 이유는 무엇인가요?

이전에 Azure Functions는 .NET 함수에 대해 호스트와 동일한 프로세스에서 [클래스 라이브러리로](functions-dotnet-class-library.md) 실행되는 긴밀하게 통합된 모드만 지원했습니다. 이 모드는 호스트 프로세스와 함수 간의 긴밀한 통합을 제공합니다. 예를 들어 .NET 클래스 라이브러리 함수는 바인딩 API 및 형식을 공유할 수 있습니다. 그러나 이 통합에는 호스트 프로세스와 .NET 함수 간의 보다 긴밀한 결합도 필요합니다. 예를 들어 In Process를 실행하는 .NET 함수는 Functions 런타임과 동일한 버전의 .NET에서 실행해야 합니다. 이러한 제약 조건을 벗어나 실행할 수 있도록 이제 격리 프로세스에서 실행하도록 선택할 수 있습니다. 이 프로세스 격리를 통해 Functions 런타임에서 기본적으로 지원하지 않는 최신 .NET 릴리스(예: .NET 5.0)를 사용하는 함수를 개발할 수도 있습니다. 격리 된 프로세스와 in-process c # 클래스 라이브러리 함수는 모두 .NET 6.0에서 실행 됩니다. 자세한 내용은 [지원 되는 버전](#supported-versions)을 참조 하세요. 

이러한 함수는 별도의 프로세스에서 실행되기 때문에 .NET 격리 함수 앱과 .NET 클래스 라이브러리 함수 앱 사이에는 몇 가지 [특징 및 기능 차이](#differences-with-net-class-library-functions)가 있습니다 .

### <a name="benefits-of-running-out-of-process"></a>Out of Process 실행의 이점

Out of Process를 실행하는 경우 .NET 함수는 다음과 같은 이점을 활용할 수 있습니다. 

+ 충돌 감소: 함수는 별도의 프로세스에서 실행되므로 앱에서 사용되는 어셈블리는 호스트 프로세스에서 사용되는 동일한 어셈블리의 다른 버전과 충돌하지 않습니다.  
+ 프로세스에 대한 완전한 제어: 앱의 시작을 제어하고 사용된 구성과 시작된 미들웨어를 제어할 수 있습니다.
+ 종속성 주입: 프로세스를 완전하게 제어하므로 종속성 주입과 미들웨어와 함수 앱의 통합을 위해 현재 .NET 동작을 사용할 수 있습니다. 

[!INCLUDE [functions-dotnet-supported-versions](../../includes/functions-dotnet-supported-versions.md)]

## <a name="net-isolated-project"></a>.NET 격리 프로젝트

.NET isolated 함수 프로젝트는 기본적으로 지원 되는 .NET 런타임을 대상으로 하는 .NET 콘솔 응용 프로그램 프로젝트입니다. 다음은 .NET 격리 프로젝트에 필요한 기본 파일입니다.

+ [host.json](functions-host-json.md) 파일.
+ [local.settings.json](functions-develop-local.md#local-settings-file) 파일.
+ 프로젝트 및 종속성을 정의하는 C# 프로젝트 파일(.csproj)입니다.
+ 앱의 진입점인 Program.cs 파일입니다.

## <a name="package-references"></a>패키지 참조

Out of Process를 실행하는 경우 .NET 프로젝트는 핵심 기능 및 바인딩 확장을 모두 구현하는 고유한 패키지 집합을 사용합니다. 

### <a name="core-packages"></a>핵심 패키지 

격리 프로세스에서 .NET 함수를 실행하려면 다음 패키지가 필요합니다.

+ [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)
+ [Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/)

### <a name="extension-packages"></a>확장 패키지

.NET 격리 프로세스에서 실행되는 함수는 서로 다른 바인딩 형식을 사용하므로 고유한 바인딩 확장 패키지 집합이 필요합니다. 

이러한 확장 패키지는 [Microsoft.Azure.Functions.Worker.Extensions](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions)에서 찾을 수 있습니다.

## <a name="start-up-and-configuration"></a>시작 및 구성 

.NET 격리 함수를 사용하는 경우 일반적으로 Program.cs에 있는 함수 앱의 시작에 액세스할 수 있습니다. 사용자는 자신의 호스트 인스턴스를 만들고 시작해야 합니다. 따라서 앱에 대한 구성 파이프라인에 직접 액세스할 수도 있습니다. Out of Process를 실행하는 경우 훨씬 더 쉽게 구성을 추가하고, 종속성을 주입하고, 사용자 자신의 미들웨어를 실행할 수 있습니다. 

다음 코드는 [HostBuilder] 파이프라인의 예를 보여 줍니다.

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_startup":::

이 코드에는 `using Microsoft.Extensions.DependencyInjection;`이 필요합니다. 

[Hostbuilder]는 함수 앱을 시작하기 위해 비동기적으로 실행하는 완전히 초기화된 [IHost] 인스턴스를 빌드하고 반환하는 데 사용됩니다. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_host_run":::

### <a name="configuration"></a>구성

[ConfigureFunctionsWorkerDefaults] 메서드는 함수 앱이 Out of Process로 실행하는 데 필요한 설정을 추가하기 위해 사용됩니다. 여기에는 다음 기능이 포함됩니다.

+ 기본 변환기 집합.
+ 속성 이름에서 대/소문자 구분을 무시하도록 기본 [JsonSerializerOptions]를 설정합니다.
+ Azure Functions 로깅과 통합.
+ 출력 바인딩 미들웨어 및 기능.
+ 함수 실행 미들웨어.
+ 기본 gRPC 지원. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_configure_defaults" :::   

호스트 빌더 파이프라인에 대한 액세스 권한이 있으면 초기화하는 동안 앱별 구성을 설정할 수도 있습니다. [HostBuilder]에서 [ConfigureAppConfiguration] 메서드를 한 번 이상 호출하여 함수 앱에 필요한 구성을 추가할 수 있습니다. 앱 구성에 대한 자세한 정보는 [ASP.NET Core의 구성](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true)을 참조하세요. 

이러한 구성은 별도의 프로세스에서 실행되는 함수 앱에 적용됩니다. 함수 호스트나 트리거 및 바인딩 구성을 변경하려면 [ host.json 파일](functions-host-json.md)을 계속 사용해야 합니다.   

### <a name="dependency-injection"></a>종속성 주입

종속성 주입은 .NET 클래스 라이브러리에 비해 간소화되었습니다. 서비스를 등록하기 위해 시작 클래스를 만들어야 하는 대신 호스트 작성기에서 [ConfigureServices]를 호출하고 [IServiceCollection]의 확장 메서드를 사용하여 특정 서비스를 삽입하면 됩니다. 

다음 예에서는 단일 서비스 종속성을 삽입합니다.  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_dependency_injection" :::

이 코드에는 `using Microsoft.Extensions.DependencyInjection;`이 필요합니다. 자세히 알아보려면 [ASP.NET Core의 종속성 주입](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true)을 참조하세요.

### <a name="middleware"></a>미들웨어

또한 .NET 격리는 다시 ASP.NET에 있는 것과 유사한 모델을 사용하여 미들웨어 등록을 지원합니다. 이 모델은 함수 실행 전후에 호출 파이프라인으로 논리를 삽입하는 기능을 제공합니다.

[ConfigureFunctionsWorkerDefaults] 확장 메서드에는 다음 예에서 볼 수 있는 것처럼 사용자 자신의 미들웨어를 등록할 수 있는 오버로드가 있습니다.  

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/CustomMiddleware/Program.cs" id="docsnippet_middleware_register" :::

함수 앱에서 사용자 지정 미들웨어를 사용하는 방법에 대한 보다 자세한 예는 [사용자 지정 미들웨어 참조 샘플](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/CustomMiddleware)을 참조하세요.

## <a name="execution-context"></a>실행 컨텍스트

.NET 격리는 함수 메서드에 [FunctionContext] 개체를 전달합니다. 이 개체를 사용하면 [GetLogger] 메서드를 호출하고 `categoryName` 문자열을 제공하여 로그에 기록할 [ILogger] 인스턴스를 가져올 수 있습니다 . 자세히 알아보려면 [로깅](#logging)을 참조하세요. 

## <a name="bindings"></a>바인딩 

바인딩은 메서드, 매개 변수 및 반환 형식에 대한 특성을 사용하여 정의됩니다. 함수 메서드는 다음 예제에 표시된 것처럼 `Function` 특성과 입력 매개 변수에 적용되는 트리거 특성을 포함하는 메서드입니다.

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_trigger" :::

트리거 특성은 트리거 유형을 지정하고, 입력 데이터를 메서드 매개 변수에 바인딩합니다. 이전 예제 함수는 큐 메시지에 의해 트리거되며, 큐 메시지는 `myQueueItem` 매개 변수의 메서드에 전달됩니다.

`Function` 특성은 메서드를 함수 진입점으로 표시합니다. 이름은 프로젝트 내에서 고유해야 하고, 문자로 시작해야 하고, 문자, 숫자, `_` 및 `-`만 포함해야 하며 허용되는 최대 길이는 127자입니다. 프로젝트 템플릿에서 `Run` 메서드를 자주 만들지만, 유효한 C# 이름은 모두 메서드 이름이 될 수 있습니다.

.NET 격리 프로젝트는 별도의 작업자 프로세스에서 실행되기 때문에 바인딩은 `ICollector<T>`, `IAsyncCollector<T>`, `CloudBlockBlob`과(와) 같은 다양한 바인딩 클래스를 활용할 수 없습니다. 또한 [DocumentClient] 및 [BrokeredMessage]와 같은 기본 서비스 SDK에서 상속된 형식을 직접 지원하지 않습니다. 대신, 바인딩은 POCO(Plain Old Class Object)와 같은 문자열, 배열 및 직렬화 가능한 형식에 의존합니다. 

HTTP 트리거의 경우 [HttpRequestData] 및 [HttpResponseData]를 사용하여 요청 및 응답 데이터에 액세스해야 합니다. 이는 Out of Process를 실행할 때 원래 HTTP 요청 및 응답 개체에 액세스할 수 없기 때문입니다.

Out of Process를 실행할 때 트리거와 바인딩을 사용하기 위한 전체 참조 샘플 집합은 [바인딩 확장 참조 샘플](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/Extensions)을 참조하세요. 

### <a name="input-bindings"></a>입력 바인딩

함수에는 데이터를 함수에 전달할 수 있는 0개 이상의 입력 바인딩이 있을 수 있습니다. 트리거와 마찬가지로 입력 바인딩은 입력 매개 변수에 바인딩 특성을 적용하여 정의됩니다. 함수가 실행되면 런타임은 바인딩에 지정된 데이터를 가져오려고 시도합니다. 요청되는 데이터는 일반적으로 바인딩 매개 변수를 사용하여 트리거가 제공하는 정보에 따라 다릅니다.  

### <a name="output-bindings"></a>출력 바인딩

출력 바인딩에 쓰려면 바인딩된 서비스에 쓰는 방법을 정의한 함수 메서드에 출력 바인딩 특성을 적용해야 합니다. 메서드에서 반환된 값은 출력 바인딩에 기록됩니다. 예를 들어 다음 예에서는 출력 바인딩을 사용하여 이름이 `myqueue-output`인 메시지 큐에 문자열 값을 씁니다.

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_output_binding" :::

### <a name="multiple-output-bindings"></a>여러 출력 바인딩

출력 바인딩에 기록된 데이터는 항상 함수의 반환 값입니다. 둘 이상의 출력 바인딩에 써야 하는 경우 사용자 지정 반환 형식을 만들어야 합니다. 이 반환 형식에는 클래스의 하나 이상의 속성에 적용되는 출력 바인딩 특성이 있어야 합니다. HTTP 트리거의 다음 예제는 HTTP 응답과 큐 출력 바인딩에 모두 씁니다.

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/MultiOutput/MultiOutput.cs" id="docsnippet_multiple_outputs":::

HTTP 트리거의 응답은 항상 출력으로 간주되므로 반환 값 특성은 필요하지 않습니다.

### <a name="http-trigger"></a>HTTP 트리거

HTTP 트리거는 들어오는 HTTP 요청 메시지를 함수에 전달되는 [HttpRequestData] 개체로 변환합니다. 이 개체는 `Headers`, `Cookies`, `Identities`, `URL` 및 메시지 `Body`(선택 사항)을(를) 포함하여 요청의 데이터를 제공합니다. 이 개체는 요청 자체가 아닌 HTTP 요청 개체의 표시입니다. 

마찬가지로 함수는 메시지 `StatusCode`, `Headers` 및 메시지 `Body`(선택 사항)을(를) 포함하여 HTTP 응답을 만드는 데 사용되는 데이터를 제공하는 [HttpResponseData] 개체를 반환합니다 .  

다음 코드는 HTTP 트리거입니다. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_http_trigger" :::

## <a name="logging"></a>로깅

격리된 .NET에서는 함수에 전달된 [FunctionContext] 개체에서 얻은 [ILogger] 인스턴스를 사용하여 로그에 쓸 수 있습니다. [GetLogger] 메서드를 호출하여 로그가 기록되는 범주의 이름인 문자열 값을 전달합니다. 범주는 일반적으로 로그가 기록되는 특정 함수의 이름입니다. 범주에 대해 자세히 알아보려면 [모니터링 문서](functions-monitoring.md#log-levels-and-categories)를 참조하세요. 

다음 예제에서는 [ILogger]를 가져오고 함수 내에서 로그를 기록하는 방법을 보여 줍니다.

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_logging" ::: 

[ILogger]의 다양한 메서드를 사용하여 `LogWarning` 또는 `LogError`과(와) 같은 다양한 로그 수준을 기록합니다. 로그 수준에 대해 자세히 알아보려면 [모니터링 문서](functions-monitoring.md#log-levels-and-categories)를 참조하세요.

[ILogger]는 [종속성 주입](#dependency-injection)을 사용하는 경우에도 제공됩니다.

## <a name="differences-with-net-class-library-functions"></a>.NET 클래스 라이브러리 함수와의 차이점

이 섹션에서는 in-process로 실행 되는 .NET 클래스 라이브러리 함수에 비해 out-of-process에서 실행 되는 함수 및 동작 차이점의 현재 상태에 대해 설명 합니다.

| 기능/동작 |  In-Process | Out of Process  |
| ---- | ---- | ---- |
| .NET 버전 | .NET Core 3.1<br/>.NET 6.0 | .NET 5.0<br/>.NET 6.0 |
| 핵심 패키지 | [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| 바인딩 확장 패키지 | [Microsoft.Azure.WebJobs.Extensions.*](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | [Microsoft.Azure.Functions.Worker.Extensions.*](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) 아래 | 
| 로깅 | 함수에 전달된 [ILogger] | [FunctionContext]에서 얻은 [ILogger] |
| 취소 토큰 | [지원됨](functions-dotnet-class-library.md#cancellation-tokens) | 지원되지 않음 |
| 출력 바인딩 | Out 매개 변수 | 반환 값 |
| 출력 바인딩 형식 |  `IAsyncCollector`, [DocumentClient], [BrokeredMessage] 및 기타 클라이언트 관련 형식 | 기본 형식, JSON 직렬화 가능 형식 및 배열. |
| 여러 출력 바인딩 | 지원됨 | [지원됨](#multiple-output-bindings) |
| HTTP 트리거 | [HttpRequest]/[ObjectResult] | [HttpRequestData]/[HttpResponseData] |
| 지속성 함수 | [지원됨](durable/durable-functions-overview.md) | 지원되지 않음 | 
| 명령적 바인딩 | [지원됨](functions-dotnet-class-library.md#binding-at-runtime) | 지원되지 않음 |
| function.json 아티팩트 | 생성된 계획 | 생성되지 않음 |
| 구성 | [host.json](functions-host-json.md) | [host.json](functions-host-json.md) 및 사용자 지정 초기화 |
| 종속성 주입 | [지원됨](functions-dotnet-dependency-injection.md)  | [지원됨](#dependency-injection) |
| 미들웨어 | 지원되지 않음 | 지원됨 |
| 콜드 시작 시간 | 일반 | Just-In-Time 시작이므로 더 오래 걸립니다. 잠재적 지연을 줄이기 위해 Windows 대신 Linux에서 실행합니다. |
| ReadyToRun | [지원됨](functions-dotnet-class-library.md#readytorun) | _TBD_ | 

## <a name="next-steps"></a>다음 단계

+ [트리거 및 바인딩에 대해 자세히 알아보기](functions-triggers-bindings.md)
+ [Azure Functions에 대한 모범 사례에 대해 자세히 알아보기](functions-best-practices.md)


[HostBuilder]: /dotnet/api/microsoft.extensions.hosting.hostbuilder?view=dotnet-plat-ext-5.0&preserve-view=true
[IHost]: /dotnet/api/microsoft.extensions.hosting.ihost?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureFunctionsWorkerDefaults]: /dotnet/api/microsoft.extensions.hosting.workerhostbuilderextensions.configurefunctionsworkerdefaults?view=azure-dotnet&preserve-view=true#Microsoft_Extensions_Hosting_WorkerHostBuilderExtensions_ConfigureFunctionsWorkerDefaults_Microsoft_Extensions_Hosting_IHostBuilder_
[ConfigureAppConfiguration]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureappconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true
[IServiceCollection]: /dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureServices]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureservices?view=dotnet-plat-ext-5.0&preserve-view=true
[FunctionContext]: /dotnet/api/microsoft.azure.functions.worker.functioncontext?view=azure-dotnet&preserve-view=true
[ILogger]: /dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true
[GetLogger]: /dotnet/api/microsoft.azure.functions.worker.functioncontextloggerextensions.getlogger?view=azure-dotnet&preserve-view=true
[DocumentClient]: /dotnet/api/microsoft.azure.documents.client.documentclient
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[HttpRequestData]: /dotnet/api/microsoft.azure.functions.worker.http.httprequestdata?view=azure-dotnet&preserve-view=true
[HttpResponseData]: /dotnet/api/microsoft.azure.functions.worker.http.httpresponsedata?view=azure-dotnet&preserve-view=true
[HttpRequest]: /dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true
[ObjectResult]: /dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true
[JsonSerializerOptions]: /dotnet/api/system.text.json.jsonserializeroptions?view=net-5.0&preserve-view=true
