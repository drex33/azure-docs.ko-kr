---
title: 로컬로 Azure Functions 개발 및 실행
description: Azure Functions에서 실행하기 전에 로컬 컴퓨터에서 Azure Functions를 코딩 및 테스트하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 4568cd1befc31cbec30758a3117c30762e7de406
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830858"
---
# <a name="code-and-test-azure-functions-locally"></a>Azure Functions를 로컬에서 코딩 및 테스트

[Azure Portal]에서 Azure Functions를 개발하고 테스트할 수 있지만 대부분의 개발자는 로컬 개발 환경을 선호합니다. Functions를 사용하면 즐겨 찾는 코드 편집기와 개발 도구를 사용하여 로컬 컴퓨터에서 함수를 쉽게 만들고 테스트할 수 있습니다. 로컬 함수는 라이브 Azure 서비스에 연결할 수 있고 사용자는 전체 Functions 런타임을 사용하여 로컬 머신에서 해당 함수를 디버깅할 수 있습니다.

이 문서에서는 기본 설정 언어를 위한 특정 개발 환경에 대한 링크를 제공합니다. 또한 [local.settings.json 파일](#local-settings-file) 작업과 같은 로컬 개발에 대한 몇 가지 공유 지침을 제공합니다. 

## <a name="local-development-environments"></a>로컬 개발 환경

로컬 컴퓨터에서 함수를 개발하는 방식은 [언어](supported-languages.md) 및 기본 도구에 따라 달라집니다. 다음 표에서 환경은 로컬 개발을 지원합니다.

|환경                              |언어         |Description|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C#(클래스 라이브러리)](functions-dotnet-class-library.md)<br/>[C# 격리 프로세스(.NET 5.0)](dotnet-isolated-process-guide.md)<br/>[JavaScript](functions-reference-node.md)<br/>[PowerShell](./create-first-function-vs-code-powershell.md)<br/>[Python](functions-reference-python.md) | [VS Code용 Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)은 VS Code에 Functions 지원을 추가합니다. 핵심 도구가 필요합니다. 2\.x 버전의 핵심 도구를 사용하면 Linux, macOS 및 Windows에서 개발을 지원합니다. 자세한 내용은 [Visual Studio Code를 사용하여 첫 번째 함수 만들기](./create-first-function-vs-code-csharp.md)를 참조하세요. |
| [명령 프롬프트 또는 터미널](functions-run-local.md) | [C#(클래스 라이브러리)](functions-dotnet-class-library.md)<br/>[C# 격리 프로세스(.NET 5.0)](dotnet-isolated-process-guide.md)<br/>[JavaScript](functions-reference-node.md)<br/>[PowerShell](functions-reference-powershell.md)<br/>[Python](functions-reference-python.md) | [Azure Functions Core Tools]는 로컬 개발을 가능하게 하는 함수 생성을 위한 핵심 런타임 및 템플릿을 제공합니다. 버전 2.x는 Linux, macOS 및 Windows에서 개발을 지원합니다. 모든 환경은 로컬 Functions 런타임에 대한 핵심 도구를 사용합니다. |
| [Visual Studio 2019](functions-develop-vs.md) | [C#(클래스 라이브러리)](functions-dotnet-class-library.md)<br/>[C# 격리 프로세스(.NET 5.0)](dotnet-isolated-process-guide.md) | Azure Functions Core Tools는 [Visual Studio 2019](https://www.visualstudio.com/vs/) 이상 버전의 **Azure 개발** 워크로드에 포함되어 있습니다. 클래스 라이브러리에서 함수를 컴파일하고 .dll을 Azure에 게시할 수 있습니다. 로컬 테스트에 대한 핵심 도구를 포함합니다. 자세한 내용은 [Visual Studio를 사용하여 Azure Functions 개발](functions-develop-vs.md)을 참조하세요. |
| [Maven](./create-first-function-cli-java.md)(다양) | [Java](functions-reference-java.md) | Maven archetype은 Java 함수 개발을 가능하게 하는 핵심 도구를 지원합니다. 버전 2.x는 Linux, macOS 및 Windows에서 개발을 지원합니다. 자세한 내용은 [Java 및 Maven을 사용하여 Azure에서 첫 번째 함수 만들기](./create-first-function-cli-java.md)를 참조하세요. 또한 [Eclipse](functions-create-maven-eclipse.md) 및 [IntelliJ IDEA](functions-create-maven-intellij.md)를 사용한 개발을 지원합니다. |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

이러한 로컬 개발 환경 각각을 통해 함수 앱 프로젝트를 만들고 미리 정의된 함수 템플릿을 사용하여 새 함수를 만들 수 있습니다. 각각은 핵심 도구를 사용합니다. 다른 앱처럼 고유한 머신에서 실제 Functions 런타임에 대해 함수를 테스트하고 디버그할 수 있습니다. 이러한 환경 중 하나의 함수 앱 프로젝트를 Azure에 게시할 수도 있습니다.

## <a name="local-settings-file"></a>로컬 설정 파일

local.settings.json 파일은 로컬 개발 도구에서 사용하는 앱 설정, 설정을 저장합니다. local.settings.json 파일의 설정은 프로젝트를 로컬에서 실행할 때만 사용됩니다. 

> [!IMPORTANT]  
> local.settings.json에는 연결 문자열과 같은 비밀이 포함될 수 있으므로 원격 리포지토리에 저장해서는 안 됩니다. Functions를 지원하는 도구는 local.settings.json 파일의 설정을 프로젝트가 배포된 함수 앱의 [앱 설정](functions-how-to-use-azure-function-app-settings.md#settings)과 동기화하는 방법을 제공합니다.

로컬 설정 파일의 구조는 다음과 같습니다.

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

다음 설정은 프로젝트를 로컬에서 실행할 때 지원됩니다.

| 설정      | 설명                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | 이 설정이 `true`로 설정된 경우 모든 값은 로컬 머신 키로 암호화됩니다. `func settings` 명령과 함께 사용됩니다. 기본값은 `false`여야 합니다. 서비스 연결 문자열과 같은 비밀이 포함된 경우 로컬 컴퓨터에서 local.settings.json 파일을 암호화할 수 있습니다. 호스트는 실행 시 자동으로 설정의 암호를 해독합니다. 로컬로 암호화된 설정을 읽으려고 시도하기 전에 `func settings decrypt` 명령을 사용합니다. |
| **`Values`** | 프로젝트가 로컬로 실행될 때 사용되는 애플리케이션 설정의 컬렉션입니다. 이러한 키-값(문자열-문자열) 쌍은 Azure에서 함수 앱의 애플리케이션 설정에 해당합니다(예: [`AzureWebJobsStorage`]). 많은 트리거와 바인딩에는 연결 문자열 앱 설정을 참조하는 속성(예: [Blob Storage 트리거](functions-bindings-storage-blob-trigger.md#configuration)에 대한 `Connection`)이 있습니다. 이러한 속성의 경우 `Values` 배열에 정의된 애플리케이션 설정이 필요합니다. 일반적으로 사용되는 설정 목록은 다음 표를 참조하세요. <br/>값은 JSON 개체 또는 배열이 아닌 문자열이어야 합니다. 설정 이름에는 콜론(`:`) 또는 이중 밑줄(`__`)을 포함할 수 없습니다. 이중 밑줄 문자는 런타임에 예약되고 콜론은 [종속성 주입](functions-dotnet-dependency-injection.md#working-with-options-and-settings)을 지원하도록 예약되어 있습니다. |
| **`Host`** | 이 섹션의 설정은 프로젝트를 로컬에서 실행할 때 Functions 호스트 프로세스를 사용자 지정합니다. 이러한 설정은 Azure에서 프로젝트를 실행하는 경우에도 적용되는 host.json 설정과는 별개입니다. |
| **`LocalHttpPort`** | 로컬 Functions 호스트(`func host start` 및 `func run`)를 실행할 때 사용되는 기본 포트를 설정합니다. `--port` 명령줄 옵션이 이 설정보다 우선합니다. 예를 들어 Visual Studio IDE에서 실행되는 경우 "프로젝트 속성 -> 디버그" 창으로 이동하고 "애플리케이션 인수" 필드에 제공할 수 있는 `host start --port <your-port-number>` 명령에서 포트 번호를 명시적으로 지정하여 포트 번호를 변경할 수 있습니다. |
| **`CORS`** | [CORS(원본 간 리소스 공유)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)에 허용된 원본을 정의합니다. 원본은 공백 없이 쉼표로 구분된 목록으로 제공됩니다. 와일드카드 값(\*)이 지원되므로 모든 원본에서 요청할 수 있습니다. |
| **`CORSCredentials`** |  `true`로 설정하면 `withCredentials` 요청을 허용합니다. |
| **`ConnectionStrings`** | 컬렉션입니다. 함수 바인딩에서 사용하는 연결 문자열에는 이 컬렉션을 사용하지 마십시오. 이 컬렉션은 일반적으로 구성 파일의 `ConnectionStrings` 섹션에서 연결 문자열을 가져오는 프레임워크에서만 사용됩니다(예: [Entity Framework](/ef/ef6/)). 이 개체의 연결 문자열은 공급자 유형이 [System.Data.SqlClient](/dotnet/api/system.data.sqlclient)인 환경에 추가됩니다. 이 컬렉션의 항목은 다른 앱 설정을 사용하여 Azure에 게시되지 않습니다. 이러한 값을 함수 앱 설정의 `Connection strings` 컬렉션에 명시적으로 추가해야 합니다. 함수 코드에서 [`SqlConnection`](/dotnet/api/system.data.sqlclient.sqlconnection)을 만드는 경우, 포털의 **애플리케이션 설정** 에 다른 연결과 함께 연결 문자열 값을 저장해야 합니다. |

다음 애플리케이션 설정은 로컬에서 실행할 때 **`Values`** 배열에 포함될 수 있습니다.

| 설정 | 값 | Description |
|-----|-----|-----|
|**`AzureWebJobsStorage`**| 스토리지 계정 연결 문자열 또는<br/>`UseDevelopmentStorage=true`| Azure Storage 계정에 대한 연결 문자열을 포함합니다. HTTP 이외의 트리거를 사용하는 경우 필요합니다. 자세한 내용은 [`AzureWebJobsStorage`] 참조를 참조하세요.<br/>[Azure Storage 에뮬레이터](../storage/common/storage-use-emulator.md)를 로컬에서 설치한 경우 [`AzureWebJobsStorage`]를 `UseDevelopmentStorage=true`로 설정하면 핵심 도구가 에뮬레이터를 사용합니다. 에뮬레이터는 개발 중에 유용하지만 배포 전에 실제 스토리지 연결로 테스트해야 합니다.| 
|**`AzureWebJobs.<FUNCTION_NAME>.Disabled`**| `true`\|`false` | 로컬에서 실행할 때 함수를 비활성화하려면 컬렉션에 `"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"`를 추가합니다. `<FUNCTION_NAME>`은 함수의 이름입니다. 자세한 내용은 [Azure Functions에서 함수를 사용하지 않도록 설정하는 방법](disable-function.md#localsettingsjson)을 참조하세요. |
|**`FUNCTIONS_WORKER_RUNTIME`** | `dotnet`<br/>`node`<br/>`java`<br/>`powershell`<br/>`python`| Functions 런타임의 대상 언어를 나타냅니다. 버전 2.x 이상의 Functions 런타임에 필요합니다. 이 설정은 핵심 도구에 의해 프로젝트에 대해 생성됩니다. 자세한 내용은 [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) 참조를 참조하세요.|
| **`FUNCTIONS_WORKER_RUNTIME_VERSION`** | `~7` |로컬로 실행할 때 PowerShell 7이 사용됨을 나타냅니다. 설정되지 않은 경우 PowerShell Core 6이 사용됩니다. 이 설정은 로컬로 실행하는 경우에만 사용됩니다. Azure에서 실행하는 경우 PowerShell 런타임 버전은 [포털에서 설정](functions-reference-powershell.md#changing-the-powershell-version)할 수 있는 `powerShellVersion` 사이트 구성 설정에 따라 결정됩니다. | 

## <a name="next-steps"></a>다음 단계

+ Visual Studio 2019를 사용하여 컴파일된 C# 함수를 로컬로 개발하는 방법에 대한 자세한 내용은 [Visual Studio를 사용하여 Azure Functions 개발](functions-develop-vs.md)을 참조하세요.
+ Mac, Linux 또는 Windows 컴퓨터에서 VS Code를 사용하여 함수를 로컬로 개발하는 방법에 대해 자세히 알아보려면 기본 설정 언어에 대한 Visual Studio Code 시작을 참조하세요.
    + [C# 클래스 라이브러리](create-first-function-vs-code-csharp.md)
    + [C# 격리 프로세스(.NET 5.0)](create-first-function-vs-code-csharp.md?tabs=isolated-process)
    + [Java](create-first-function-vs-code-java.md)
    + [JavaScript](create-first-function-vs-code-node.md)
    + [PowerShell](create-first-function-vs-code-powershell.md)
    + [Python](create-first-function-vs-code-python.md)
    + [TypeScript](create-first-function-vs-code-typescript.md)
+ 명령 프롬프트 또는 터미널에서 함수를 개발하는 방법에 대한 자세한 내용은 [Azure Functions 핵심 도구 작업](functions-run-local.md)을 참조하세요.

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`AzureWebJobsStorage`]: functions-app-settings.md#azurewebjobsstorage
