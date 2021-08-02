---
title: Azure Functions를 사용하여 C# 클래스 라이브러리 함수 개발
description: C#을 사용하여 Azure Functions 런타임과 함께 In Process를 실행하는 클래스 라이브러리로 코드를 개발하고 게시하는 방법을 이해합니다.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 07/24/2020
ms.openlocfilehash: 60afc79c332385958d9f73ad1e224cab90293e1c
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111903134"
---
# <a name="develop-c-class-library-functions-using-azure-functions"></a>Azure Functions를 사용하여 C# 클래스 라이브러리 함수 개발

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

이 문서는 .NET 클래스 라이브러리의 C#을 사용하여 Azure Functions를 개발하는 방법을 소개합니다.

>[!IMPORTANT]
>이 문서에서는 런타임과 함께 In Process를 실행하는 .NET 클래스 라이브러리 함수를 지원합니다. Functions는 C# 함수를 Out Of Process로, 런타임에서 격리하여 실행하는 방식으로 .NET 5.x도 지원합니다. 자세히 알아보려면 [.NET 격리된 프로세스 함수](dotnet-isolated-process-guide.md)를 참조하세요.

C# 개발자라면 다음과 같은 문서를 참조할 수도 있습니다.

| 시작 | 개념| 단계별 학습/샘플 |
|--| -- |--| 
| <ul><li>[Visual Studio 사용](functions-create-your-first-function-visual-studio.md)</li><li>[Visual Studio Code 사용](create-first-function-vs-code-csharp.md)</li><li>[명령줄 도구 사용](create-first-function-cli-csharp.md)</li></ul> | <ul><li>[호스팅 옵션](functions-scale.md)</li><li>[성능&nbsp;고려 사항](functions-best-practices.md)</li><li>[Visual Studio 개발](functions-develop-vs.md)</li><li>[종속성 주입](functions-dotnet-dependency-injection.md)</li></ul> | <ul><li>[서버리스 애플리케이션 만들기](/learn/paths/create-serverless-applications/)</li><li>[C# 샘플](/samples/browse/?products=azure-functions&languages=csharp)</li></ul> |

Azure Functions는 C# 및 C# 스크립트 프로그래밍 언어를 지원합니다. [Azure Portal에서 C#을 사용하는 방법](functions-create-function-app-portal.md)에 대한 지침은 [C# 스크립트(.csx) 개발자 참조](functions-reference-csharp.md)를 참조하세요.

[!INCLUDE [functions-dotnet-supported-versions](../../includes/functions-dotnet-supported-versions.md)]

### <a name="functions-v2x-considerations"></a>Functions v2.x 고려 사항

최신 2.x 버전(`~2`)을 대상으로 하는 함수 앱은 .Net Core 3.1에서 실행되도록 자동으로 업그레이드됩니다. .Net Core 버전 간에는 호환성이 손상되는 변경이 이루어졌기 때문에, .NET Core 2.2에 대해 개발되고 컴파일된 모든 앱을 .NET Core 3.1로 안전하게 업그레이드할 수 있는 것은 아닙니다. 함수 앱을 `~2.0`에 고정하여 해당 업그레이드를 옵트아웃할 수 있습니다. 또한 Functions는 호환되지 않는 API를 검색하고 .NET Core 3.1에서 잘못된 실행이 발생하는 것을 방지하기 위해 앱을 `~2.0`에 고정할 수 있습니다. 

>[!NOTE]
>함수 앱이 `~2.0`에 고정되어 있는 경우 버전 대상을 `~2`로 변경하면 함수 앱이 중단될 수 있습니다. ARM 템플릿을 사용하여 배포하는 경우 템플릿에서 버전을 확인하세요. 이 문제가 발생하는 경우 버전을 다시 대상 `~2.0`으로 변경하고 호환성 문제를 해결하세요. 

`~2.0`을 대상으로 하는 함수 앱은 .NET Core 2.2에서 계속 실행됩니다. 해당 버전의 .NET Core는 더 이상 보안 및 기타 유지 관리 업데이트를 받지 않습니다. 자세한 내용은 [공지 페이지](https://github.com/Azure/app-service-announcements/issues/266)를 참조하세요. 

최대한 빨리 함수가 .NET Core 3.1과 호환되도록 해야 합니다. 해당 문제를 해결한 다음 버전을 다시 `~2`로 변경하거나 `~3`으로 업그레이드합니다. Functions 런타임이 대상으로 하는 버전에 대해 자세히 알아보려면 [Azure Functions 런타임 버전을 대상으로 지정하는 방법](set-runtime-version.md)을 참조하세요.

프리미엄 또는 전용(App Service) 요금제로 Linux에서 실행하는 경우 `linuxFxVersion` 사이트 구성 설정을 `DOCKER|mcr.microsoft.com/azure-functions/dotnet:2.0.14786-appservice`로 설정하여 특정 이미지를 대신 대상으로 지정하는 방식으로 버전을 고정하세요. `linuxFxVersion`을 설정하는 방법을 알아보려면 [Linux의 수동 버전 업데이트](set-runtime-version.md#manual-version-updates-on-linux)를 참조하세요.

## <a name="functions-class-library-project"></a>Functions 클래스 라이브러리 프로젝트

Visual Studio에서 **Azure Functions** 프로젝트 템플릿은 다음 파일이 포함된 C# 클래스 라이브러리 프로젝트를 만듭니다.

* [host.json](functions-host-json.md) -로컬로 또는 Azure에서 실행될 경우 프로젝트의 모든 함수에 영향을 주는 구성 설정을 저장합니다.
* [local.settings.json](functions-run-local.md#local-settings-file) - 로컬로 실행될 때 사용되는 앱 설정 및 연결 문자열을 저장합니다. 이 파일은 암호를 포함하며 Azure의 함수 앱에 게시되지 않습니다. 대신 [앱 설정을 함수 앱에 추가](functions-develop-vs.md#function-app-settings)합니다.

프로젝트를 빌드할 때 빌드 출력 디렉터리에 다음 예제와 같은 폴더 구조가 생성됩니다.

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

이 디렉터리는 Azure의 함수 앱에 배포되는 디렉터리입니다. Functions 런타임의 [버전 2.x](functions-versions.md)에 필요한 바인딩 확장은 [NuGet 패키지로 프로젝트에 추가](./functions-bindings-register.md#vs)됩니다.

> [!IMPORTANT]
> 빌드 프로세스는 각 함수에 대해 *function.json* 파일을 만듭니다. 이 *function.json* 파일은 직접 편집할 수 없습니다. 이 파일을 편집하여 바인딩 구성을 변경하거나 함수를 사용하지 않도록 설정할 수 없습니다. 함수를 사용하지 않도록 설정하는 방법을 알아보려면 [함수를 사용하지 않도록 설정하는 방법](disable-function.md)을 참조하세요.


## <a name="methods-recognized-as-functions"></a>함수로 인식되는 메서드

클래스 라이브러리에서 함수는 다음 예제와 같이 `FunctionName` 및 트리거 특성을 포함하는 정적 메서드입니다.

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

`FunctionName` 특성은 메서드를 함수 진입점으로 표시합니다. 이름은 프로젝트 내에서 고유해야 하고, 문자로 시작해야 하고, 문자, 숫자, `_` 및 `-`만 포함해야 하며 허용되는 최대 길이는 127자입니다. 프로젝트 템플릿에서 `Run` 메서드를 자주 만들지만, 유효한 C# 이름은 모두 메서드 이름이 될 수 있습니다.

트리거 특성은 트리거 유형을 지정하고, 입력 데이터를 메서드 매개 변수에 바인딩합니다. 예제 함수는 큐 메시지에 의해 트리거되며, 큐 메시지는 `myQueueItem` 매개 변수의 메서드에 전달됩니다.

## <a name="method-signature-parameters"></a>메서드 서명 매개 변수

트리거 특성에 사용되는 매개 변수 이외의 매개 변수가 메서드 서명에 포함될 수 있습니다. 다음은 포함할 수 있는 기타 매개 변수 중 일부입니다.

* 특성으로 데코레이팅하여 표시된 [입력 및 출력 바인딩](functions-triggers-bindings.md).  
* [로깅](#logging)에 대한 `ILogger` 또는 `TraceWriter`([버전 1.x 전용](functions-versions.md#creating-1x-apps)) 매개 변수.
* [정상 종료](#cancellation-tokens)를 위한 `CancellationToken` 매개 변수.
* 트리거 메타데이터를 가져오는 [바인딩 식](./functions-bindings-expressions-patterns.md) 매개 변수.

함수 시그니처에서 매개 변수의 순서는 중요하지 않습니다. 예를 들어, 다른 바인딩 전후에 트리거 매개 변수를 추가하고, 트리거 또는 바인딩 매개 변수 전후에 로거 매개 변수를 추가할 수 있습니다.

### <a name="output-bindings"></a>출력 바인딩

함수는 출력 매개 변수를 사용하여 정의된 0개 또는 1개의 출력 바인딩을 가질 수 있습니다. 

다음 예제에서는 `myQueueItemCopy`라는 이름의 출력 큐 바인딩을 추가하여 이전 예제를 수정합니다. 이 함수는 함수를 다른 큐의 새 메시지로 트리거하는 메시지 콘텐츠를 씁니다.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        ILogger log)
    {
        log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

출력 바인딩에 할당된 값은 함수가 종료될 때 쓰입니다. 여러 출력 매개 변수에 값을 할당하기만 하면 함수에서 둘 이상의 출력 바인딩을 사용할 수 있습니다. 

바인딩 참조 문서(예: [스토리지 큐](functions-bindings-storage-queue.md))는 트리거, 입력 또는 출력 바인딩 특성에 사용할 수 있는 매개 변수 형식을 설명합니다.

### <a name="binding-expressions-example"></a>바인딩 식 예제

다음 코드는 앱 설정에서 모니터링할 큐의 이름을 가져오고, `insertionTime` 매개 변수에서 큐 메시지 작성 시간을 가져옵니다.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        ILogger log)
    {
        log.LogInformation($"Message content: {myQueueItem}");
        log.LogInformation($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>자동 생성된 function.json

빌드 프로세스는 build 폴더의 function 폴더에 *function.json* 파일을 만듭니다. 앞에서 설명한 대로 이 파일은 직접 편집할 수 없습니다. 이 파일을 편집하여 바인딩 구성을 변경하거나 함수를 사용하지 않도록 설정할 수 없습니다. 

이 파일은 [사용 플랜에 대한 크기 결정](event-driven-scaling.md)에 사용하기 위해 크기 조정 컨트롤러에 정보를 제공하는 데 필요합니다. 이와 같은 이유로 이 파일에는 트리거 정보만 있고 입출력 바인딩은 없습니다.

생성된 *function.json* 파일에는 바인딩에 *function.json* 구성 대신 .NET 특성을 사용하도록 런타임에 지시하는 `configurationSource` 속성이 포함되어 있습니다. 예를 들면 다음과 같습니다.

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## <a name="microsoftnetsdkfunctions"></a>Microsoft.NET.Sdk.Functions

*function.json* 파일 생성은 [Microsoft\.NET\.Sdk\.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions) NuGet 패키지에서 수행됩니다. 

버전 1.x 및 2.x의 Functions 런타임 둘 다에 동일한 패키지가 사용됩니다. 대상 프레임워크가 1.x 프로젝트와 2.x 프로젝트에서 구분되는 측면입니다. 다음은 *.csproj* 파일의 관련 부분으로, 동일한 `Sdk` 패키지를 가진 다른 대상 프레임워크를 보여 줍니다.

# <a name="v2x"></a>[v2.x+](#tab/v2)

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

# <a name="v1x"></a>[v1.x](#tab/v1)

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```
---


`Sdk` 패키지 종속성에는 트리거 및 바인딩이 있습니다. 해당 트리거 및 바인딩은 .NET Framework를 대상으로 하므로 1.x 프로젝트는 1.x 트리거 및 바인딩을 참조하지만, 2.x 트리거 및 바인딩을 .NET Core를 대상으로 합니다.

`Sdk` 패키지는 [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json)에도 종속되며, [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage)에는 간접적으로 종속됩니다. 이러한 종속성 때문에 프로젝트는 대상이 되는 Functions 런타임 버전에서 작동하는 패키지 버전을 사용하게 됩니다. 예를 들어, `Newtonsoft.Json`에는 .NET Framework 4.6.1용 버전 11이 있지만, .NET Framework 4.6.1을 대상으로 하는 Functions 런타임은 `Newtonsoft.Json` 9.0.1과만 호환됩니다. 따라서 해당 프로젝트의 함수 코드도 `Newtonsoft.Json` 9.0.1을 사용해야 합니다.

`Microsoft.NET.Sdk.Functions`의 소스 코드는 [azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk) GitHub 리포지토리에서 사용할 수 있습니다.

## <a name="local-runtime-version"></a>로컬 런타임 버전

Visual Studio는 [Azure Functions 핵심 도구](functions-run-local.md#install-the-azure-functions-core-tools)를 사용하여 로컬 컴퓨터에서 Functions 프로젝트를 실행합니다. 핵심 도구는 Function 런타임에 대한 명령줄 인터페이스입니다.

Windows 설치 프로그램(MSI) 패키지를 사용하거나 npm을 사용하여 핵심 도구를 설치하는 경우 Visual Studio에서 사용하는 핵심 도구 버전에는 영향을 미치지 않습니다. Functions 런타임 버전 1.x의 경우, Visual Studio는 *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* 에 핵심 도구 버전을 저장하고 해당 위치에 저장된 최신 버전을 사용합니다. Functions 2.x의 경우, 핵심 도구는 **Azure Functions 및 Web Jobs Tools** 확장에 포함되어 있습니다. 1.x 및 2.x 둘 다, Functions 프로젝트를 실행할 때 콘솔 출력에 사용되는 버전을 확인할 수 있습니다.

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="readytorun"></a>ReadyToRun

함수 앱을 [ReadyToRun 이진 파일](/dotnet/core/whats-new/dotnet-core-3-0#readytorun-images)로 컴파일할 수 있습니다. ReadyToRun은 [사용 플랜](consumption-plan.md)에서 실행할 때 [콜드 부팅](event-driven-scaling.md#cold-start)의 영향을 줄이는 데 도움이 되도록 시작 성능을 개선할 수 있는 사전 컴파일 형식입니다.

ReadyToRun은 .NET 3.0에서 사용할 수 있으며 [Azure Functions 런타임 버전 3.0](functions-versions.md)이 필요합니다.

프로젝트를 ReadyToRun으로 컴파일하려면 `<PublishReadyToRun>` 및 `<RuntimeIdentifier>` 요소를 추가하여 프로젝트 파일을 업데이트합니다. 다음은 Windows 32비트 함수 앱에 게시하기 위한 구성입니다.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.1</TargetFramework>
  <AzureFunctionsVersion>v3</AzureFunctionsVersion>
  <PublishReadyToRun>true</PublishReadyToRun>
  <RuntimeIdentifier>win-x86</RuntimeIdentifier>
</PropertyGroup>
```

> [!IMPORTANT]
> ReadyToRun은 현재 교차 컴파일을 지원하지 않습니다. 배포 대상과 동일한 플랫폼에서 앱을 빌드해야 합니다. 또한 함수 앱에 구성된 “비트 수”에 주의를 기울여야 합니다. 예를 들어 Azure의 함수 앱이 Windows 64비트인 경우 [런타임 식별자](/dotnet/core/rid-catalog)로 `win-x64`를 사용하여 Windows에서 앱을 컴파일해야 합니다.

명령줄에서 ReadyToRun을 사용하여 앱을 빌드할 수도 있습니다. 자세한 내용은 [`dotnet publish`](/dotnet/core/tools/dotnet-publish)의 `-p:PublishReadyToRun=true` 옵션을 참조하세요.

## <a name="supported-types-for-bindings"></a>바인딩에 대해 지원되는 형식

각 바인딩에는 자체적인 지원 형식이 있습니다. 예를들 어, Blob 트리거 특성은 문자열 매개 변수, POCO 매개 변수, `CloudBlockBlob` 매개 변수 또는 지원되는 기타 몇 가지 형식에 적용될 수 있습니다. [Blob 바인딩에 대한 바인딩 참조 문서](functions-bindings-storage-blob-trigger.md#usage)에는 지원되는 모든 매개 변수 형식이 나와 있습니다. 자세한 내용은 [트리거 및 바인딩](functions-triggers-bindings.md) 및 [각 바인딩 형식에 대한 바인딩 참조 문서](functions-triggers-bindings.md#next-steps)를 참조하세요.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>메서드 반환 값에 바인딩

메서드 반환 값에 특성을 적용하여 출력 바인딩에 대한 메서드 반환 값을 사용할 수 있습니다. 예제를 보려면 [트리거 및 바인딩](./functions-bindings-return-value.md)을 참조하세요. 

성공적인 함수 실행이 항상 출력 바인딩으로 전달할 반환 값을 생성하는 경우에만 반환 값을 사용합니다. 그렇지 않으면 다음 섹션에 나와 있는 것처럼 `ICollector` 또는 `IAsyncCollector`를 사용합니다.

## <a name="writing-multiple-output-values"></a>여러 출력 값 쓰기

출력 바인딩에 여러 값을 쓰거나 성공적인 함수 호출이 출력 바인딩에 전달할 값을 생성하지 않는 경우 [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) 또는 [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) 형식을 사용합니다. 이러한 형식은 메서드가 완료될 때 출력 바인딩에 기록되는 쓰기 전용 컬렉션입니다.

이 예제에서는 `ICollector`를 사용하여 동일한 큐에 여러 큐 메시지를 씁니다.

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="async"></a>Async

[비동기화](/dotnet/csharp/programming-guide/concepts/async/) 함수를 만들려면 `async` 키워드를 사용하고 `Task` 개체를 반환합니다.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        ILogger log)
    {
        log.LogInformation($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

`out` 매개 변수는 비동기 함수에 사용할 수 없습니다. 출력 바인딩에는 [함수 반환 값](#binding-to-method-return-value) 또는 [수집기 개체](#writing-multiple-output-values)를 대신 사용합니다.

## <a name="cancellation-tokens"></a>취소 토큰

함수는 함수가 종료될 때 운영 체제가 코드에 알릴 수 있게 해주는 [CancellationToken](/dotnet/api/system.threading.cancellationtoken) 매개 변수를 사용할 수 있습니다. 이 알림을 통해 함수가 예기치 않게 종료되어 데이터가 일관되지 않은 상태가 되는 것을 방지할 수 있습니다.

다음 예제에서는 임박한 함수 종료를 확인하는 방법을 보여 줍니다.

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
        TextWriter logger,
        CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(5000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }
}
```

## <a name="logging"></a>로깅

함수 코드로 Application Insights에서 traces로 표시되는 로그에 출력을 작성할 수 있습니다. 로그에 작성하는 경우 일반적으로 이름이 `log`인 [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger)형식의 매개 변수를 포함하는 것이 좋습니다. Functions 런타임의 버전 1.x는 `TraceWriter`를 사용하며, 이는 Application Insights에도 작성하지만 구조적 로깅은 지원하지 않습니다. 해당 데이터는 Application Insights에 의해 캡처되지 않으므로 `Console.Write`를 사용하여 로그를 작성하지 마세요. 

### <a name="ilogger"></a>ILogger

함수 정의에서 [구조적 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)을 지원하는 [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) 매개 변수를 포함합니다.

`ILogger` 개체를 사용하여 로그를 생성하는 `Log<level>`[ILogger의 확장 메서드](/dotnet/api/microsoft.extensions.logging.loggerextensions#methods)를 호출합니다. 다음 코드는 범주가 `Function.<YOUR_FUNCTION_NAME>.User.`인 `Information` 로그를 작성합니다.

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

Functions 구현 방법에 대한 자세한 `ILogger` 내용은 [원격 분석 데이터 수집](functions-monitoring.md#collecting-telemetry-data)을 참조하세요. `Function` 접두사가 붙은 범주는 `ILogger` 인스턴스를 사용하고 있다고 가정합니다. 대신 `ILogger<T>`를 사용하도록 선택하는 경우 범주 이름은 대신 `T`를 기반으로 할 수 있습니다.  

### <a name="structured-logging"></a>구조적 로깅

이름이 아닌 자리 표시자는 로그 메시지에 사용되는 매개 변수를 결정합니다. 다음과 같은 코드가 있다고 가정하겠습니다.

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

메시지 문자열을 동일하게 유지하고 매개 변수의 순서를 반대로 바꾸면 그 결과 메시지 텍스트가 잘못된 위치에서 값을 갖습니다.

구조적 로깅을 수행할 수 있도록 자리 표시자는 이러한 방식으로 처리됩니다. Application Insights는 매개 변수 이름-값 쌍과 메시지 문자열을 저장합니다. 그 결과로 메시지 인수는 사용자가 쿼리할 수 있는 필드가 됩니다.

로거 메서드 호출이 이전 예제와 같은 경우 `customDimensions.prop__rowKey` 필드를 쿼리할 수 있습니다. 런타임에서 추가하는 필드와 함수 코드에서 추가하는 필드가 서로 충돌하지 않도록 `prop__` 접두사가 추가됩니다.

`customDimensions.prop__{OriginalFormat}` 필드를 참조하여 원래 메시지 문자열을 쿼리할 수도 있습니다.  

다음은 `customDimensions` 데이터의 샘플 JSON 표현입니다.

```json
{
  "customDimensions": {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="log-custom-telemetry"></a><a name="log-custom-telemetry-in-c-functions">로그 사용자 지정 원격 분석</a>

함수에서 Application Insights로 사용자 지정 원격 분석 데이터를 전송하는 데 사용할 수 있는 Application Insights SDK의 Functions 관련 버전 [Microsoft.Azure.WebJobs.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights)가 있습니다. 명령 프롬프트에서 다음 명령을 사용하여 이 패키지를 설치합니다.

# <a name="command"></a>[명령](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

이 명령에서 `<VERSION>`을 설치된 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)를 지원하는 이 패키지의 버전으로 바꿉니다. 

다음 C# 예제에서는 [사용자 지정 원격 분석 API](../azure-monitor/app/api-custom-events-metrics.md)를 사용합니다. 이 예제는 .NET 클래스 라이브러리용이지만 Application Insights 코드는 C# 스크립트와 동일합니다.

# <a name="v2x"></a>[v2.x+](#tab/v2)

런타임 2.x 이상 버전에서는 원격 분석 데이터와 현재 작업 간의 상관관계를 자동으로 지정하는 Application Insights의 새 기능이 사용됩니다. 작업 `Id`, `ParentId` 또는 `Name` 필드를 수동으로 설정할 필요가 없습니다.

```cs
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using System.Linq;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Write an event to the customEvents table.
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Generate a custom metric, in this case let's use ContentLength.
            this.telemetryClient.GetMetric("contentLength").TrackValue(req.ContentLength);

            // Log a custom dependency in the dependencies table.
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

이 예제에서 사용자 지정 메트릭 데이터는 customMetrics 테이블로 전송되기 전에 호스트에 의해 집계됩니다. 자세히 알아보려면 Application Insights의 [GetMetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) 문서를 참조하세요. 

로컬로 실행하는 경우 Application Insights 키를 사용하여 [local.settings.json](functions-run-local.md#local-settings-file) 파일에 `APPINSIGHTS_INSTRUMENTATIONKEY` 설정을 추가해야 합니다.


# <a name="v1x"></a>[v1.x](#tab/v1)

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```
---

`TrackRequest` 또는 `StartOperation<RequestTelemetry>`를 호출하지 마세요. 함수 호출에 대한 요청이 중복으로 표시됩니다.  Functions 런타임에서 자동으로 요청을 추적합니다.

`telemetryClient.Context.Operation.Id`를 설정하지 마세요. 이 전역 설정은 여러 함수가 동시에 실행될 때 잘못된 상관관계를 유발합니다. 대신 새로운 원격 분석 인스턴스(`DependencyTelemetry`, `EventTelemetry`)를 만들고 해당하는 `Context` 속성을 수정합니다. 그런 다음, 원격 분석 인스턴스를 `TelemetryClient`의 해당 `Track` 메서드(`TrackDependency()`, `TrackEvent()`, `TrackMetric()`)에 전달합니다. 이 방법을 사용하면 원격 분석 데이터가 현재 함수 호출에 대해 올바른 상관관계 세부 정보를 가질 수 있습니다.


## <a name="environment-variables"></a>환경 변수

환경 변수 또는 앱 설정 값을 가져오려면 다음 코드 예제와 같이 `System.Environment.GetEnvironmentVariable`을 사용합니다.

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
    {
        log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
        log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    private static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

앱 설정은 로컬로 개발할 때와 Azure에서 실행할 때 환경 변수에서 읽을 수 있습니다. 로컬로 개발할 때 앱 설정은 `Values`local.settings.json *파일의* 컬렉션에서 가져옵니다. 로컬 및 Azure의 두 환경에서 `GetEnvironmentVariable("<app setting name>")`은 명명된 앱 설정의 값을 검색합니다. 예를 들어 로컬로 실행하는 경우 *local.settings.json* 파일에 `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`이 포함된 경우 "My Site Name"이 반환됩니다.

[System.Configuration.ConfigurationManager.AppSettings](/dotnet/api/system.configuration.configurationmanager.appsettings) 속성은 앱 설정 값을 가져오는 대체 API지만, 다음과 같이 `GetEnvironmentVariable`을 사용하는 것이 좋습니다.

## <a name="binding-at-runtime"></a>런타임에 바인딩

C# 및 기타 .NET 언어에서는 특성의 [*declarative*](https://en.wikipedia.org/wiki/Declarative_programming) 바인딩과 달리 [명령적](https://en.wikipedia.org/wiki/Imperative_programming) 바인딩 패턴을 사용할 수 있습니다. 명령적 바인딩은 바인딩 매개 변수를 디자인 타임이 아닌 런타임에 계산해야 할 경우 유용합니다. 이 패턴을 사용하면 함수 코드에서 지원되는 입력 및 출력 바인딩을 즉시 바인딩할 수 있습니다.

다음과 같이 명령적 바인딩을 정의합니다.

- 원하는 명령적 바인딩에 대한 함수 시그니처에 특성을 포함하지 **마세요**.
- 입력 매개 변수 [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) 또는 [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs)에 전달합니다.
- 다음 C# 패턴을 사용하여 데이터 바인딩을 수행합니다.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute`는 바인딩을 정의하는 .NET 특성이며, `T`는 해당 바인딩 형식에서 지원되는 입력 또는 출력 형식입니다. `T`는 `out` 매개 변수 형식(예: `out JObject`)일 수 없습니다. 예를 들어, Mobile Apps 테이블 출력 바인딩은 [6가지 출력 형식](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)을 지원하지만 명령적 바인딩에는 [ICollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) 또는 [IAsyncCollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)만 사용할 수 있습니다.

### <a name="single-attribute-example"></a>단일 특성 예제

다음 예제 코드에서는 런타임에서 정의된 Blob경로를 사용하는 [Storage Blob 출력 바인딩](functions-bindings-storage-blob-output.md)을 만든 다음, Blob에 문자열을 씁니다.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        ILogger log)
    {
        log.LogInformation($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)는 [Storage Blob](functions-bindings-storage-blob.md) 입력 또는 출력 바인딩을 정의하며, [TextWriter](/dotnet/api/system.io.textwriter)는 지원되는 출력 바인딩 형식입니다.

### <a name="multiple-attributes-example"></a>다중 특성 예제

앞의 예제에서는 함수 앱의 주 Storage 계정 연결 문자열(`AzureWebJobsStorage`)에 대한 앱 설정을 가져옵니다. [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)를 추가하고 `BindAsync<T>()`에 특성 배열을 전달하여 스토리지 계정에 사용할 사용자 지정 앱 설정을 지정할 수 있습니다. `IBinder`가 아닌 `Binder` 매개 변수를 사용합니다.  예를 들면 다음과 같습니다.

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            ILogger log)
    {
        log.LogInformation($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>트리거 및 바인딩 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [트리거 및 바인딩에 대해 자세히 알아보기](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Azure Functions에 대한 모범 사례에 대해 자세히 알아보기](functions-best-practices.md)
