---
title: Azure Functions에 대한 PowerShell 개발자 참조
description: PowerShell을 사용하여 함수를 개발하는 방법을 알아봅니다.
author: eamonoreilly
ms.topic: conceptual
ms.custom: devx-track-dotnet, devx-track-azurepowershell
ms.date: 04/22/2019
ms.openlocfilehash: 297d8af86f22cc588060cb90f327ad6dd335437d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528929"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure Functions PowerShell 개발자 가이드

이 문서에서는 PowerShell을 사용하여 Azure Functions를 작성하는 방법에 대해 자세히 설명합니다.

PowerShell Azure 함수는 활성화되면, 실행되는 PowerShell 스크립트로 표시됩니다. 각 함수 스크립트에는 함수의 동작 방식(예: 실행되는 방법, 해당 입력 및 출력 매개 변수)을 정의하는 관련 `function.json` 파일이 있습니다. 자세한 정보는 [트리거 및 바인딩 문서](functions-triggers-bindings.md)를 참조하세요. 

다른 종류의 함수와 마찬가지로 PowerShell 스크립트 함수는 `function.json` 파일에 정의된 모든 입력 바인딩의 이름과 일치하는 매개 변수를 사용합니다. 함수를 시작한 트리거에 대한 추가 정보를 포함하는 `TriggerMetadata` 매개 변수도 전달됩니다.

이 문서에서는 [Azure Functions 개발자 참조](functions-reference.md)를 이미 읽었다고 가정합니다. 또한, [PowerShell에 대한 Functions 빠른 시작](./create-first-function-vs-code-powershell.md)를 완료하여 첫 번째 PowerShell 함수를 만들었습니다.

## <a name="folder-structure"></a>폴더 구조

PowerShell 프로젝트에 필요한 폴더 구조는 다음과 같습니다. 이 기본값은 변경 가능합니다. 자세한 내용은 아래의 [scriptFile](#configure-function-scriptfile) 섹션을 참조하세요.

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

프로젝트 루트에는 함수 앱을 구성하는 데 사용할 수 있는 공유 [`host.json`](functions-host-json.md) 파일이 있습니다. 각 함수에는 자체 코드 파일(.ps1)과 바인딩 구성 파일(`function.json`)이 있는 폴더가 있습니다. function.json 파일의 부모 디렉터리 이름은 항상 사용자 함수의 이름입니다.

특정 바인딩에는 `extensions.csproj` 파일이 있어야 합니다. Functions 런타임의 [버전 2.x 이상 버전](functions-versions.md)에 필요한 바인딩 확장은 `extensions.csproj` 파일에 정의되어 있고 실제 라이브러리 파일은 `bin` 폴더에 있습니다. 로컬에서 개발할 때는 [바인딩 확장을 등록](functions-bindings-register.md#extension-bundles)해야 합니다. Azure Portal에서 함수를 개발할 때 이 등록이 자동으로 수행됩니다.

PowerShell 함수 앱에는 함수 앱이 실행되기 시작할 때 실행되는 `profile.ps1`을 선택적으로 사용할 수 있습니다( *[콜드 부팅](#cold-start)* 이라고도 함). 자세한 내용은 [PowerShell 프로필](#powershell-profile)을 참조하세요.

## <a name="defining-a-powershell-script-as-a-function"></a>PowerShell 스크립트를 함수로 정의

기본적으로 Functions 런타임은 `run.ps1`에서 함수를 찾습니다. 여기서 `run.ps1`는 해당하는 `function.json`과 동일한 부모 디렉터리를 공유합니다.

스크립트는 실행에서 인수의 수로 전달됩니다. 매개 변수를 처리하려면 다음 예제와 같이 스크립트 위에 `param` 블록을 추가합니다.

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata 매개 변수

`TriggerMetadata`매개 변수는 트리거에 대한 추가 정보를 제공하는 데 사용됩니다. 추가 메타데이터는 바인딩마다 다르지만 모두 다음 데이터를 포함하는 `sys` 속성을 포함합니다.

```powershell
$TriggerMetadata.sys
```

| 속성   | 설명                                     | Type     |
|------------|-------------------------------------------------|----------|
| UtcNow     | UTC에서 함수가 실행된 경우        | DateTime |
| MethodName | 실행된 함수의 이름     | 문자열   |
| RandGuid   | 이 함수 실행에 대한 고유 guid | 문자열   |

모든 트리거 형식에는 서로 다른 메타데이터 세트가 있습니다. 예를 들어 `QueueTrigger`를 위한 `$TriggerMetadata`에는 다른 항목 외에도 `InsertionTime`, `Id`, `DequeueCount`가 포함됩니다. 큐 트리거의 메타데이터에 관한 자세한 내용은 [큐 트리거 공식 설명서](functions-bindings-storage-queue-trigger.md#message-metadata)로 이동하세요. 작업 중인 [트리거](functions-triggers-bindings.md)에 대한 설명서를 확인하여 트리거 메타데이터 내에 있는 항목을 확인합니다.

## <a name="bindings"></a>바인딩

PowerShell에서 [바인딩](functions-triggers-bindings.md)은 함수의 function.json 구성 및 정의됩니다. Functions는 다양한 방법으로 바인딩과 상호 작용합니다.

### <a name="reading-trigger-and-input-data"></a>읽기 트리거 및 입력 데이터

트리거 바인딩 및 입력 바인딩은 함수로 전달되는 매개 변수로 읽혀집니다. 입력 바인딩에는 function.json에서 `in`으로 설정된 `direction`이 있습니다. `param` 블록에서 `function.json`에 정의된`name` 속성은 매개 변수의 이름입니다. PowerShell에서 바인딩에 명명된 매개 변수를 사용하므로 매개 변수의 순서가 중요하지 않습니다. 그러나 `function.json`에 정의된 바인딩의 순서를 따르는 것이 가장 좋습니다.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>출력 데이터 쓰기

Functions에서 출력 바인딩에는 function.json에서 `out`으로 설정된 `direction`이 있습니다. Functions 런타임에서 사용할 수 있는 `Push-OutputBinding` cmdlet을 사용하여 출력 바인딩에 쓸 수 있습니다. 모든 경우 `function.json`에 정의된 바인딩의 `name` 속성은 `Push-OutputBinding` cmdlet의 `Name` 매개 변수에 해당합니다.

다음은 함수 스크립트에서 `Push-OutputBinding`을 호출하는 방법을 보여 줍니다.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

파이프라인을 통해 특정 바인딩에 대한 값을 전달할 수도 있습니다.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding`은 `-Name`을 위해 지정된 값에 따라 다르게 동작합니다.

* 지정된 이름을 유효한 출력 바인딩으로 확인할 수 없으면 오류가 throw됩니다.

* 출력 바인딩에서 값의 컬렉션을 허용하는 경우 `Push-OutputBinding`을 반복적으로 호출하여 여러 값을 푸시할 수 있습니다.

* 출력 바인딩에서 singleton 값만 허용하는 경우 `Push-OutputBinding`을 두 번째 호출하면 오류가 발생합니다.

#### <a name="push-outputbinding-syntax"></a>Push-OutputBinding 구문

`Push-OutputBinding`을 호출하는 데 유효한 매개 변수는 다음과 같습니다.

| 이름 | 유형 | 위치 | Description |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | 설정하려는 출력 바인딩의 이름입니다. |
| **`-Value`** | Object | 2 | 파이프라인 ByValue에서 허용되고 사용자가 설정하려는 출력 바인딩의 값입니다. |
| **`-Clobber`** | SwitchParameter | named | (선택 사항) 지정된 경우 지정된 출력 바인딩에 값이 설정되도록 합니다. | 

다음 일반 매개 변수도 지원됩니다. 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

자세한 내용은 [CommonParameters 정보](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

#### <a name="push-outputbinding-example-http-responses"></a>Push-OutputBinding 예제: HTTP 응답

HTTP 트리거는 `response`라는 출력 바인딩을 사용하여 응답을 반환합니다. 다음 예제에서 출력 바인딩에는 `response` "출력 #1"" 값이 있습니다.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

출력은 singleton 값만 허용하는 HTTP에 대한 것이므로 `Push-OutputBinding`을 두 번째로 호출하면 오류가 throw됩니다.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

singleton 값만 허용하는 출력의 경우 컬렉션에 추가하는 대신 `-Clobber` 매개 변수를 사용하여 이전 값을 재정의할 수 있습니다. 다음 예에서는 값을 이미 추가했다고 가정합니다. 다음 예제의 `-Clobber` 응답을 사용하여 기존 값을 재정의함으로써 "출력 #3" 값을 반환합니다.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Push-OutputBinding 예제: 큐 출력 바인딩

`Push-OutputBinding`은 [Azure Queue storage 출력 바인딩](functions-bindings-storage-queue-output.md)과 같은 출력 바인딩에 데이터를 전송하는 데 사용됩니다. 다음 예제에서는 큐에 기록된 메시지의 값이 "출력 #1"입니다.

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

스토리지 큐를 위한 출력 바인딩은 여러 출력 값을 허용합니다. 이 경우 첫 번째 이후에 다음 예제를 호출하면 "출력 #1" 및 "출력 #2"의 두 항목이 포함된 목록이 큐에 기록됩니다.

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

다음 예제는 이전 두 항목 이후에 호출될 때 출력 컬렉션에 두 개의 값을 더합니다.

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

큐에 기록될 때 메시지는 네 가지 값("출력 #1", "출력 #2", "출력 #3", "출력 #4")을 포함합니다.

#### <a name="get-outputbinding-cmdlet"></a>Get-OutputBinding cmdlet

`Get-OutputBinding` cmdlet을 사용하여 현재 출력 바인딩에 설정된 값을 검색할 수 있습니다. cmdlet은 해당 값을 사용하여 출력 바인딩의 이름을 포함하는 해시 테이블을 검색합니다. 

다음은 `Get-OutputBinding`을 사용하여 현재 바인딩 값을 반환하는 예입니다.

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

또한, `Get-OutputBinding`에는 다음 예제와 같이 반환된 바인딩을 필터링하는 데 사용할 수 있는 `-Name`이라는 매개 변수가 포함되어 있습니다.

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

와일드카드(*)는 `Get-OutputBinding`에서 지원됩니다.

## <a name="logging"></a>로깅

PowerShell 함수의 로깅은 일반적인 PowerShell 로깅과 같은 방식으로 작동합니다. 로깅 cmdlet을 사용하여 각 출력 스트림에 쓸 수 있습니다. 각 cmdlet은 Functions에서 사용하는 로그 수준에 매핑됩니다.

| 함수 로깅 수준 | 로깅 cmdlet |
| ------------- | -------------- |
| 오류 | **`Write-Error`** |
| 경고 | **`Write-Warning`**  | 
| 정보 | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`** <br/> _정보_ 수준 로깅에 씁니다. |
| 디버그 | **`Write-Debug`** |
| 추적 | **`Write-Progress`** <br /> **`Write-Verbose`** |

해당 cmdlet 외에도 파이프라인에 쓰여진 모든 항목은 `Information` 로그 수준으로 리디렉션되고 기본 PowerShell 서식으로 표시됩니다.

> [!IMPORTANT]
> `Write-Verbose` cmdlet 또는 `Write-Debug` cmdlet을 사용하는 것만으로는 자세한 정보와 디버그 수준 로깅을 볼 수 없습니다. 또한, 실제로 관심 있는 로그 수준을 선언하는 로그 수준 임계값을 구성해야 합니다. 자세한 정보는 [함수 앱 로그 수준 구성](#configure-the-function-app-log-level)을 참조하세요.

### <a name="configure-the-function-app-log-level"></a>함수 앱 로그 수준 구성

Azure Functions를 사용하면 Functions에서 로그에 쓰는 방식을 쉽게 컨트롤할 수 있도록 임계값 수준을 정의할 수 있습니다. 콘솔에 기록된 모든 추적에 대한 임계값을 설정하려면 [`host.json` 파일]`logging.logLevel.default`host.json 참조[의 ] 속성을 사용합니다. 이 설정은 함수 앱의 모든 함수에 적용됩니다.

다음 예에서는 모든 함수에 자세한 정보 로깅을 사용하도록 임계값을 설정하지만, `MyFunction`이라는 함수에 디버그 로깅을 사용하도록 임계값을 설정합니다.

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

자세한 내용은 [host.json 참조]를 참조하세요.

### <a name="viewing-the-logs"></a>로그 보기

Azure에서 함수 앱을 실행 중인 경우 Application Insights를 사용하여 모니터링할 수 있습니다. 함수 로그 보기 및 쿼리에 대해 자세히 알아보려면 [Azure Functions 모니터링](functions-monitoring.md)을 읽어보세요.

개발을 위해 함수 앱을 로컬로 실행하는 경우, 로그는 기본적으로 파일 시스템에 기록됩니다. 콘솔에서 로그를 보려면 함수 앱을 시작하기 전에 `AZURE_FUNCTIONS_ENVIRONMENT` 환경 변수를 `Development`로 설정합니다.

## <a name="triggers-and-bindings-types"></a>트리거 및 바인딩 형식

함수 앱에서 사용할 수 있는 트리거와 바인딩은 여러 가지가 있습니다. 트리거와 바인딩의 전체 목록은 [여기에서 찾을 수 ](functions-triggers-bindings.md#supported-bindings) 있습니다.

모든 트리거와 바인딩은 코드에서 몇 가지 실제 데이터 형식으로 표현됩니다.

* Hashtable
* 문자열
* byte[]
* int
* double
* HttpRequestContext
* HttpResponseContext

이 목록에서 처음 5개 형식은 표준 .NET 형식입니다. 마지막 두 개는 [HttpTrigger 트리거](#http-triggers-and-bindings)에서만 사용됩니다.

함수의 각 바인딩 매개 변수는 다음 형식 중 하나여야 합니다.

### <a name="http-triggers-and-bindings"></a>HTTP 트리거 및 바인딩

HTTP, 웹후크 트리거 및 HTTP 출력 바인딩은 요청 및 응답 개체를 사용하여 HTTP 메시지를 나타냅니다.

#### <a name="request-object"></a>요청 개체

스크립트에 전달되는 요청 개체의 형식은 다음 속성을 가진 `HttpRequestContext` 형식입니다.

| 속성  | 설명                                                    | Type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | 요청의 본문을 포함하는 개체입니다. `Body`는 데이터에 따라 가장 적합한 형식으로 직렬화됩니다. 예를 들어 데이터가 JSON인 경우, 해시 테이블로 전달됩니다. 데이터가 문자열인 경우 문자열로 전달됩니다. | object |
| **`Headers`** | 요청 헤더를 포함하는 사전입니다.                | Dictionary<string,string><sup>*</sup> |
| **`Method`** | 요청의 HTTP 메서드입니다.                                | 문자열                    |
| **`Params`**  | 요청의 라우팅 매개 변수를 포함하는 개체입니다. | Dictionary<string,string><sup>*</sup> |
| **`Query`** | 쿼리 매개 변수를 포함하는 개체입니다.                  | Dictionary<string,string><sup>*</sup> |
| **`Url`** | 요청의 URL입니다.                                        | 문자열                    |

<sup>*</sup> 모든 `Dictionary<string,string>` 키는 대/소문자를 구분하지 않습니다.

#### <a name="response-object"></a>응답 개체

다시 전송해야 하는 응답 개체는 다음과 같은 속성을 포함하는 `HttpResponseContext` 형식입니다.

| 속성      | 설명                                                 | Type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | 응답의 본문을 포함하는 개체입니다.           | object                    |
| **`ContentType`** | 응답의 콘텐츠 형식을 설정하는 데 사용할 축약형입니다. | 문자열                    |
| **`Headers`** | 응답 헤더를 포함하는 개체입니다.               | 사전 또는 해시 테이블   |
| **`StatusCode`**  | 응답의 HTTP 상태 코드입니다.                       | 문자열 또는 int             |

#### <a name="accessing-the-request-and-response"></a>요청 및 응답 액세스

HTTP 트리거를 사용하는 경우 다른 입력 바인딩과 동일한 방식으로 HTTP 요청에 액세스할 수 있습니다. `param` 블록에 있습니다.

다음과 같이 `HttpResponseContext` 개체를 사용하여 응답을 반환합니다.

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

이 함수를 호출한 결과는 다음과 같습니다.

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>트리거 및 바인딩을 위한 형식 캐스팅

BLOB 바인딩과 같은 특정 바인딩의 경우 매개 변수의 형식을 지정할 수 있습니다.

예를 들어 문자열로 제공된 BLOB 스토리지의 데이터를 포함하려면 내 `param` 블록에 다음 형식 캐스트를 추가합니다.

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell 프로필

Powershell에는 PowerShell 프로필이라는 개념이 있습니다. PowerShell 프로필에 대해 잘 모르는 경우 [프로필 정보](/powershell/module/microsoft.powershell.core/about/about_profiles)를 참조하세요.

Powershell Functions에서 프로필 스크립트는 처음 배포될 때 앱의 PowerShell 작업자 인스턴스당 한 번씩, 그리고 유휴 상태([콜드](#cold-start)부팅) 후에 한 번 실행됩니다. [PSWorkerInProcConcurrencyUpperBound](#concurrency) 값을 설정하여 동시성을 사용하도록 설정하면 생성된 각 Runspace에 대해 프로필 스크립트가 실행됩니다.

Visual Studio Code 및 Azure Functions Core Tools와 같은 도구를 사용하여 함수 앱을 만들면 기본값 `profile.ps1`이 만들어집니다. 기본 프로필은 [핵심 도구 GitHub 리포지토리에서](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) 유지 관리되며 다음을 포함합니다.

* Azure에 자동 MSI 인증.
* 원하는 경우 Azure PowerShell `AzureRM` PowerShell 별칭을 켜는 기능입니다.

## <a name="powershell-versions"></a>PowerShell 버전

다음 표에서는 Functions 런타임의 각 주 버전에서 사용할 수 있는 PowerShell 버전 및 필요한 .NET 버전을 보여 줍니다.

| Functions 버전 | PowerShell 버전                               | .NET 버전  | 
|-------------------|--------------------------------------------------|---------------|
| 3.x(권장) | PowerShell 7(권장)<br/>PowerShell Core 6 | .NET Core 3.1<br/>.NET Core 2.1 |
| 2.x               | PowerShell Core 6                                | .NET Core 2.2 |

함수에서 `$PSVersionTable`을 인쇄하여 현재 버전을 볼 수 있습니다.

Azure Functions 런타임 지원 정책에 대한 자세한 내용은 이 [문서](./language-support-policy.md)를 참조하세요.

### <a name="running-local-on-a-specific-version"></a>특정 버전에서 로컬 실행

로컬로 실행하는 경우 Azure Functions 런타임은 기본값은 PowerShell Core 6를 사용합니다. 로컬에서 실행할 때 PowerShell 7을 사용하려면 설정 `"FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"`을 프로젝트 루트의 local.setting.json 파일에 있는 `Values` 배열에 추가해야 합니다. PowerShell 7에서 로컬로 실행하는 경우 local.settings.json 파일은 다음 예제와 같습니다. 

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "powershell",
    "FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"
  }
}
```

### <a name="changing-the-powershell-version"></a>PowerShell 버전 변경

PowerShell Core 6에서 PowerShell 7로 업그레이드하려면 함수 앱이 버전 3.x에서 실행되어야 합니다. 해당 방법을 알아보려면 [현재 런타임 버전 확인 및 업데이트](set-runtime-version.md#view-and-update-the-current-runtime-version)를 참조하세요.


함수 앱에서 사용하는 PowerShell 버전을 변경하려면 다음 단계를 사용합니다. Azure Portal에서 또는 PowerShell을 사용하여 이렇게 할 수 있습니다.

# <a name="portal"></a>[포털](#tab/portal)

1. [Azure Portal](https://portal.azure.com)에서 함수 앱으로 이동합니다.

1. **설정** 아래에서 **구성** 을 선택합니다. **일반 설정** 탭에서, **PowerShell 버전** 을 찾습니다. 

    :::image type="content" source="media/functions-reference-powershell/change-powershell-version-portal.png" alt-text="함수 앱에서 사용하는 PowerShell 버전 선택"::: 

1. 원하는 **PowerShell Core 버전** 을 선택하고 **저장** 을 선택합니다. 보류 중인 다시 시작에 관한 경고가 표시되면 **계속** 을 선택합니다. 선택한 PowerShell 버전에서 함수 앱이 다시 시작됩니다. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

다음 스크립트를 실행하여 PowerShell 버전을 변경합니다. 

```powershell
Set-AzResource -ResourceId "/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<FUNCTION_APP>/config/web" -Properties @{  powerShellVersion  = '<VERSION>' } -Force -UsePatchSemantics

```

`<SUBSCRIPTION_ID>`, `<RESOURCE_GROUP>`, `<FUNCTION_APP>`을 Azure 구독의 ID, 리소스 그룹 및 함수 앱의 이름으로 바꿉니다.  또한, `<VERSION>`을 `~6` 또는 `~7`로 바꿉니다. 반환된 해시 테이블의 `Properties`에서 `powerShellVersion` 설정의 업데이트된 값을 확인할 수 있습니다. 

---

구성이 변경된 후 함수 앱이 다시 시작됩니다.

## <a name="dependency-management"></a>종속성 관리

함수를 사용하여 종속성 관리를 위한 [PowerShell 갤러리](https://www.powershellgallery.com)를 활용할 수 있습니다. 종속성 관리를 사용하는 경우 requirements.psd1 파일을 사용하여 필요한 모듈을 자동으로 다운로드합니다. 다음 예제와 같이 `managedDependency` 속성을 [host.json 파일](functions-host-json.md)의 루트에 있는 `true`로 설정하여 이 동작을 사용합니다.

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

새 PowerShell 함수 프로젝트를 만들 때 기본적으로 Azure [ `Az` 모듈](/powershell/azure/new-azureps-module-az)이 포함된 종속성 관리가 사용됩니다. 현재 모듈은 최대 10개까지 지원됩니다. 지원되는 구문은 다음 requirements.psd1 예에 표시된 것처럼 _`MajorNumber`_ `.*` 또는 정확한 모듈 버전입니다.

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

requirements.psd1 파일을 업데이트하는 경우, 업데이트된 모듈이 다시 시작한 후 설치됩니다.

### <a name="target-specific-versions"></a>특정 버전을 대상으로 지정

requirements.psd1 파일에서 모듈의 특정 버전을 대상으로 지정할 수 있습니다. 예를 들어, 포함된 Az module에 있는 것보다 더 이전 버전인 Az.Accounts를 사용하려는 경우 다음 예제와 같이 특정 버전을 대상으로 지정해야 합니다. 

```powershell
@{
    'Az.Accounts' = '1.9.5'
}
```

이 경우 다음 예제와 같이 profile.ps1 파일의 맨 위에 import 문도 추가해야 합니다.

```powershell
Import-Module Az.Accounts -RequiredVersion '1.9.5'
```

이러한 방식으로 함수를 시작하면 이전 버전의 Az.Account 모듈이 먼저 로드됩니다.

### <a name="dependency-management-considerations"></a>종속성 관리 고려 사항

종속성 관리를 사용하는 경우 다음 사항을 고려해야 합니다.

+ 모듈을 다운로드하려면 관리형 종속성이 <https://www.powershellgallery.com>에 액세스할 수 있어야 합니다. 로컬로 실행하는 경우 런타임에서 필요한 방화벽 규칙을 추가하여 해당 URL에 액세스할 수 있는지 확인합니다.

+ 관리형 종속성은 현재 사용자가 대화형으로 라이선스를 수락하거나, `Install-Module`을 호출할 때 `-AcceptLicense` 스위치를 제공하여 라이선스를 수락해야 하는 모듈을 지원하지 않습니다.

### <a name="dependency-management-app-settings"></a>종속성 관리 앱 설정

다음 애플리케이션 설정을 사용하여 관리형 종속성을 다운로드하고 설치하는 방법을 변경할 수 있습니다. 

| 함수 앱 설정              | 기본값             | Description                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **MDMaxBackgroundUpgradePeriod**      | `7.00:00:00`(7일)     | PowerShell 함수 앱에 대한 백그라운드 업데이트 기간을 제어합니다. 자세히 알아보려면 [MDMaxBackgroundUpgradePeriod](functions-app-settings.md#mdmaxbackgroundupgradeperiod)를 참조하세요. | 
| **MDNewSnapshotCheckPeriod**         | `01:00:00`(1시간)       | 각 PowerShell 작업자가 관리되는 종속성 업그레이드의 설치 여부를 확인하는 빈도를 지정합니다. 자세히 알아보려면 [MDNewSnapshotCheckPeriod](functions-app-settings.md#mdnewsnapshotcheckperiod)를 참조하세요.|
| **MDMinBackgroundUpgradePeriod**      | `1.00:00:00`(1일)     | 이전 업그레이드 이후, 다른 업그레이드 확인이 시작될 때까지의 기간입니다. 자세히 알아보려면 [MDMinBackgroundUpgradePeriod](functions-app-settings.md#mdminbackgroundupgradeperiod)를 참조하세요.|

기본적으로 앱 업그레이드는 `MDMaxBackgroundUpgradePeriod`내에서 시작되고, 업그레이드 프로세스는 대략 `MDNewSnapshotCheckPeriod` 에서 완료됩니다.

## <a name="custom-modules"></a>사용자 지정 모듈

Azure Functions의 사용자 고유의 사용자 지정 모듈을 활용하는 것은 PowerShell에서 일반적으로 작업을 수행하는 방법과 다릅니다.

로컬 컴퓨터에서 모듈은 `$env:PSModulePath`에서 전역으로 사용 가능한 폴더 중 하나에 설치됩니다. Azure에서 실행하는 경우 컴퓨터에 설치된 모듈에 액세스할 수 없습니다. 즉, PowerShell 함수 앱을 위한 `$env:PSModulePath`는 일반 PowerShell 스크립트 내의 `$env:PSModulePath`와 다릅니다.

Functions에는 `PSModulePath`에 두 개의 경로가 포함되어 있습니다.

* 함수 앱의 루트에 있는 `Modules` 폴더.
* PowerShell 언어 작업자에 의해 제어되는 `Modules` 폴더의 경로.

### <a name="function-app-level-modules-folder"></a>함수 앱 수준 모듈 폴더

사용자 지정 모듈을 사용하기 위해 함수가 `Modules` 폴더에 종속되는 모듈을 배치할 수 있습니다. 이 폴더에서 모듈은 자동으로 함수 런타임에 사용할 수 있습니다. 함수 앱의 모든 함수는 해당 모듈을 사용할 수 있습니다. 

> [!NOTE]
> [requirements.psd1 파일](#dependency-management)에 지정된 모듈은 자동으로 다운로드되어 경로에 포함되므로 모듈 폴더에 포함할 필요가 없습니다. 해당 모듈은 클라우드에서 실행될 때 `$env:LOCALAPPDATA/AzureFunctions` 폴더 및 `/data/ManagedDependencies` 폴더에 로컬로 저장됩니다.

사용자 지정 모듈 기능을 활용하려면 함수 앱의 루트에 `Modules` 폴더를 만듭니다. 함수에서 사용하려는 모듈을 해당 위치에 복사합니다.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

`Modules` 폴더를 사용하는 경우, 함수 앱은 다음과 같은 폴더 구조를 가져야 합니다.

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

함수 앱을 시작하는 경우 PowerShell 언어 작업자는 해당 `Modules` 폴더를 `$env:PSModulePath`에 추가하므로 일반 PowerShell 스크립트에서와 마찬가지로 모듈 자동 로드가 처리합니다.

### <a name="language-worker-level-modules-folder"></a>언어 작업자 수준 모듈 폴더

일반적으로 여러 모듈이 PowerShell 언어 작업자에서 사용됩니다. 모듈은 `PSModulePath`의 마지막 위치에 정의됩니다. 

모듈의 현재 목록은 다음과 같습니다.

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): `.zip`, `.nupkg`, 기타 등의 보관에 사용되는 모듈입니다.
* **ThreadJob**: PowerShell 작업 API의 스레드 기반 구현입니다.

기본적으로 Functions는 해당 모듈의 최신 버전을 사용합니다. 특정 모듈 버전을 사용하려면 함수 앱의 `Modules` 폴더에 특정 버전을 둡니다.

## <a name="environment-variables"></a>환경 변수

Functions에서 [앱 설정](functions-app-settings.md)(예: 서비스 연결 문자열)은 실행 중에 환경 변수로 노출됩니다. 다음 예제와 같이 `$env:NAME_OF_ENV_VAR`를 사용하여 해당 설정에 액세스할 수 있습니다.

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

로컬로 실행하는 경우 앱 설정은 [local.settings.json](functions-develop-local.md#local-settings-file) 프로젝트 파일에서 읽습니다.

## <a name="concurrency"></a>동시성

기본적으로 Functions PowerShell 런타임은 한 번에 하나의 함수 호출만 처리할 수 있습니다. 그러나 다음과 같은 경우에는 이러한 동시성 수준으로 충분하지 않을 수 있습니다.

* 동시에 다수의 호출을 처리하려고 하는 경우.
* 동일한 함수 앱 내에서 다른 함수를 호출하는 함수가 있는 경우.

워크로드 유형에 따라 몇 가지 동시성 모델을 살펴볼 수 있습니다.

* ```FUNCTIONS_WORKER_PROCESS_COUNT```를 증가시킵니다. 이렇게 하면 특정 CPU 및 메모리 오버헤드를 야기하는 동일한 인스턴스 내의 여러 프로세스에서 함수 호출을 처리할 수 있습니다. 일반적으로 I/O 바인딩된 함수는 오버헤드의 영향을 받지 않습니다. CPU 바인딩된 함수는 상당한 영향을 받을 수 있습니다.

* ```PSWorkerInProcConcurrencyUpperBound```앱 설정값을 늘립니다. 이렇게 하면 동일한 프로세스 내에서 여러 Runspace를 만들 수 있으므로 CPU와 메모리 오버헤드가 크게 줄어듭니다.

환경 변수는 함수 앱의 [앱 설정](functions-app-settings.md)에서 설정합니다.

사용 사례에 따라 Durable Functions은 확장성을 크게 향상할 수 있습니다. 자세한 정보는 [Durable Functions 애플리케이션 패턴](./durable/durable-functions-overview.md?tabs=powershell#application-patterns)을 참조하세요.

>[!NOTE]
> "사용 가능한 Runspace가 없어서 요청이 큐에 대기 중입니다" 경고가 나타날 수 있지만, 오류가 아닙니다. 요청이 대기 중임을 알리는 메시지가 표시되고, 이전 요청이 완료되면 대기 요청이 처리됩니다.

### <a name="considerations-for-using-concurrency"></a>동시성에 대한 고려 사항

PowerShell은 기본적으로 _단일 스레드_ 스크립팅 언어입니다. 그러나 동일한 프로세스에서 여러 PowerShell Runspace를 사용하여 동시성을 추가할 수 있습니다. 만든 Runspace의 양은 ```PSWorkerInProcConcurrencyUpperBound``` 애플리케이션 설정과 일치합니다. 처리량은 선택한 계획에서 사용할 수 있는 CPU 및 메모리의 양의 영향을 받습니다.

Azure PowerShell은 _프로세스 수준_ 컨텍스트 및 상태를 통해 과도한 입력을 방지합니다. 그러나 함수 앱에서 동시성을 설정하고 상태를 변경하는 작업을 호출하면 경합 상태가 발생할 수 있습니다. 경합 상태는 한 호출이 특정 상태를 사용하고, 다른 호출이 해당 상태를 변경하기 때문에 디버그하기 어렵습니다.

일부 작업에는 상당한 시간이 걸릴 수 있으므로 Azure PowerShell을 사용하는 동시성에는 엄청난 값이 있습니다. 그러나 주의해야 합니다. 경합 상태가 발생한 것 같다면 PSWorkerInProcConcurrencyUpperBound 앱 설정을 `1`로 설정하고, 대신 동시성에 [언어 작업자 프로세스 수준 격리](functions-app-settings.md#functions_worker_process_count)를 사용합니다.

## <a name="configure-function-scriptfile"></a>함수 scriptFile 구성

기본적으로 PowerShell 함수는 해당하는 `function.json`과 동일한 부모 디렉터리를 공유하는 `run.ps1` 파일에서 실행됩니다.

`function.json`의 `scriptFile` 속성을 사용하여 다음 예제와 같은 폴더 구조를 가져올 수 있습니다.

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

이 경우의 `myFunction`을 위한 `function.json`에는 실행할 내보낸 함수가 있는 파일을 참조하는 `scriptFile` 속성이 포함됩니다.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>entryPoint를 구성하여 PowerShell 모듈 사용

이 문서에서는 템플릿에서 생성된 기본 `run.ps1` 스크립트 파일의 PowerShell 함수를 보여 줍니다.
그러나 PowerShell 모듈에 함수를 포함할 수도 있습니다. function.json` 구성 파일에서 `scriptFile` 필드 및 `entryPoint` 필드를 사용하여 모듈 내 특정 함수 코드를 참조할 수 있습니다.

이 경우 `entryPoint`는 `scriptFile`에서 참조되는 PowerShell 모듈에 있는 함수 또는 cmdlet의 이름입니다.

다음 폴더 구조를 고려하세요.

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

`PSFunction.psm1`을 포함하는 경우

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

이 예제에서 `myFunction`의 구성에는 다른 폴더의 PowerShell 모듈인 `PSFunction.psm1`을 참조하는 `scriptFile` 속성이 포함되어 있습니다.  `entryPoint`속성은 모듈의 진입점인 `Invoke-PSTestFunc` 함수를 참조합니다.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

이 구성을 사용하면 `run.ps1`과 마찬가지로 `Invoke-PSTestFunc`가 실행됩니다.

## <a name="considerations-for-powershell-functions"></a>PowerShell 함수 고려 사항

PowerShell 함수로 작업하는 경우 다음 섹션의 고려 사항에 유의해야 합니다.

### <a name="cold-start"></a>콜드 부팅

[서버리스 호스팅 모델](consumption-plan.md)에서 Azure Functions를 개발하는 경우 사실상 콜드 부팅됩니다. *콜드 부팅* 은 함수 앱이 요청 처리를 위해 실행을 시작하기까지 걸리는 시간을 나타냅니다. 비활성 기간에 함수 앱이 종료되기 때문에 소비 계획에서 콜드 부팅이 더 자주 발생합니다.

### <a name="bundle-modules-instead-of-using-install-module"></a>Install-Module을 사용하는 대신 모듈을 번들로 묶기

모든 호출에서 스크립트가 실행됩니다. 스크립트에서 `Install-Module`을 사용하지 않습니다. 대신 함수가 모듈을 다운로드하느라 시간을 낭비하지 않도록 게시하기 전에 `Save-Module`을 사용합니다. 콜드 부팅이 함수에 영향을 줄 때는 *Always On* 또는 [프리미엄 계획](functions-premium-plan.md)으로 설정된 [App Service 요금제](dedicated-plan.md)에 함수 앱을 배포하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 자료를 참조하세요.

* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)

[host.json 참조]: functions-host-json.md
