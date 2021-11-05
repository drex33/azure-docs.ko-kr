---
title: Azure Functions 바인딩 확장 등록
description: 환경에 따라 Azure Functions 바인딩 확장을 등록하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 09/14/2020
ms.author: cshoe
ms.openlocfilehash: b847243543eb8667a38d253e7eaad215af29e7b2
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852343"
---
# <a name="register-azure-functions-binding-extensions"></a>Azure Functions 바인딩 확장 등록

Azure Functions 버전 2.x부터 함수 런타임에는 기본적으로 HTTP 및 타이머 트리거만 포함됩니다. 다른 [트리거 및 바인딩](./functions-triggers-bindings.md)은 별도의 패키지로 사용할 수 있습니다.

.NET 클래스 라이브러리 함수 앱은 NuGet 패키지로 프로젝트에 설치된 바인딩을 사용합니다. 확장 번들을 사용하면 .NET 함수 앱 이외의 함수 앱에서 .NET 인프라를 처리할 필요 없이 동일한 바인딩을 사용할 수 있습니다.

다음 표는 바인딩을 등록하는 방법과 시기를 나타냅니다.

| 개발 환경 |등록<br/> (Functions 1.x)  |등록<br/> Functions 2.x 이상에서  |
|-------------------------|------------------------------------|------------------------------------|
|Azure portal|자동|자동<sup>*</sup>|
|.NET 언어 이외의 언어|자동|[확장 번들](#extension-bundles)을 사용하거나(권장) [명시적으로 확장 설치](#explicitly-install-extensions)|
|Visual Studio를 사용하는 C# 클래스 라이브러리|[NuGet 도구 사용](#vs)|[NuGet 도구 사용](#vs)|
|Visual Studio Code를 사용하는 C# 클래스 라이브러리|해당 없음|[.NET Core CLI 사용](#vs-code)|

<sup>*</sup> Portal에서는 확장 번들을 사용합니다.

## <a name="access-extensions-in-non-net-languages"></a>.NET 언어 이외의 언어로 확장에 액세스

Java, JavaScript, PowerShell, Python, 사용자 지정 처리기 함수 앱의 경우 확장 번들을 사용하여 바인딩에 액세스하는 것이 좋습니다. 확장 번들을 사용할 수 없는 경우 명시적으로 바인딩 확장을 설치할 수 있습니다.

### <a name="extension-bundles"></a><a name="extension-bundles"></a>확장 번들

확장 번들은 호환되는 바인딩 확장 집합을 함수 앱에 추가하는 방법입니다. 앱의 *host.json* 파일에서 확장 번들을 사용하도록 설정합니다.

버전이 2.x 이상인 Functions 런타임 버전에서는 확장 번들을 사용할 수 있습니다.

확장 번들은 버전이 지정되어 있습니다. 각 버전에는 함께 작동하는 것으로 확인된 특정 바인딩 확장 집합이 포함되어 있습니다. 앱에 필요한 확장에 따라 번들 버전을 선택하세요.

함수 앱에 확장 번들을 추가하려면 *host.json* 에 `extensionBundle` 섹션을 추가합니다. 대부분의 경우 Visual Studio Code 및 Azure Functions Core Tools에서 자동으로 추가합니다.

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

다음 표에는 현재 사용할 수 있는 버전의 기본 *Microsoft.Azure.Functions.ExtensionBundle* 번들 및 포함된 확장에 대한 링크가 있습니다.

| 번들 버전 | host.json의 버전 | 포함된 확장 |
| --- | --- | --- |
| 1.x | `[1.*, 2.0.0)` | 번들을 생성하는 데 사용된 [extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/v1.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) 참조 |
| 2.x | `[2.*, 3.0.0)` | 번들을 생성하는 데 사용된 [extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/v2.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) 참조 |
| 3.x | `[3.3.0, 4.0.0)` | 번들을 생성하는 데 사용된 [extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/v3.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) 참조 |

> [!NOTE]
> host.json에서 사용자 지정 버전 범위를 지정할 수 있지만 이 표의 버전 값을 사용하는 것이 좋습니다.

### <a name="explicitly-install-extensions"></a>명시적으로 확장 설치

확장 번들을 사용할 수 없는 경우 Azure Functions Core Tools를 로컬로 사용하여 프로젝트에 필요한 특정 확장 패키지를 설치할 수 있습니다.

> [!IMPORTANT]
> 확장 번들을 사용하는 함수 앱에는 확장을 명시적으로 설치할 수 없습니다. 확장을 명시적으로 설치하기 전에 *host.json* 에서 `extensionBundle` 섹션을 제거합니다.

다음 항목은 확장을 수동으로 설치해야 하는 몇 가지 이유를 설명합니다.

* 번들에서 사용할 수 없는 특정 버전의 확장에 액세스해야 합니다.
* 번들에서 사용할 수 없는 사용자 지정 확장에 액세스해야 합니다.
* 단일 번들에서 사용할 수 없는 특정 확장 조합에 액세스해야 합니다.

> [!NOTE]
> Core Tools를 사용하여 확장을 수동으로 설치하려면 [.NET Core 3.1 SDK가](https://dotnet.microsoft.com/download) 설치되어 있어야 합니다. .NET Core SDK는 Azure Functions Core Tools에서 NuGet의 확장을 설치하는 데 사용됩니다. Azure Functions 확장을 사용하기 위해 .NET을 알 필요는 없습니다.

확장을 명시적으로 설치하면 extensions.csproj라는 .NET 프로젝트 파일이 프로젝트의 루트에 추가됩니다. 이 파일은 함수에 필요한 NuGet 패키지 세트를 정의합니다. 이 파일에서 [NuGet 패키지 참조](/nuget/consume-packages/package-references-in-project-files)로 작업할 수 있지만 Core Tools를 사용하면 파일을 수동으로 편집하지 않고도 확장을 설치할 수 있습니다.

Core Tools를 사용하여 로컬 프로젝트에 필요한 확장을 설치하는 방법에는 여러 가지가 있습니다. 

#### <a name="install-all-extensions"></a>모든 확장 설치 

다음 명령을 사용하여 로컬 프로젝트의 바인딩에서 사용하는 모든 확장 패키지를 자동으로 추가합니다.

```command
func extensions install
```

이 명령은 *function.json* 파일을 읽고 필요한 패키지를 확인하고 설치한 후 확장 프로젝트(extensions.csproj)를 다시 빌드합니다. 현재 버전에서 새 바인딩을 추가하되, 기존 바인딩을 업데이트하지는 않습니다. 새 바인딩을 설치할 때 기존 바인딩을 최신 버전으로 업데이트하려면 `--force` 옵션을 사용합니다. 자세한 내용은 [`func extensions install` 명령](functions-core-tools-reference.md#func-extensions-install)을 참조하세요.

함수 앱에서 Core Tools가 인식하지 못하는 바인딩을 사용하는 경우 수동으로 특정 확장을 설치해야 합니다.

#### <a name="install-a-specific-extension"></a>특정 확장 설치

다음 명령을 사용하여 특정 버전(이 경우 Storage 확장)에서 특정 확장 패키지를 설치합니다.

```command
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 5.0.0
```

자세한 내용은 [`func extensions install` 명령](functions-core-tools-reference.md#func-extensions-install)을 참조하세요.

## <a name="install-extensions-from-nuget-in-net-languages"></a><a name="local-csharp"></a>.NET 언어로 NuGet에서 확장 설치

C# 클래스 라이브러리 기반의 함수 프로젝트에서는 확장을 직접 설치해야 합니다. 확장 번들은 C# 클래스 라이브러리를 기반으로 하지 않는 프로젝트를 위해 특별히 설계되었습니다.

### <a name="c-class-library-with-visual-studio"></a>Visual Studio를 사용하는<a name="vs"></a> C\# 클래스 라이브러리

**Visual Studio** 에서는 다음 예제와 같이 [Install-Package](/nuget/tools/ps-ref-install-package) 명령을 사용하여 패키지 관리자 콘솔에서 패키지를 설치할 수 있습니다.

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

지정된 바인딩에 사용되는 패키지의 이름은 해당 바인딩의 참조 문서에 제공되어 있습니다. 예를 들어 [Service Bus 바인딩 참조 문서의 패키지 섹션](functions-bindings-service-bus.md#functions-1x)을 참조하세요.

예제의 `<TARGET_VERSION>`을 패키지의 특정 버전(예: `3.0.0-beta5`)으로 바꿉니다. 유효한 버전은 [NuGet.org](https://nuget.org)의 개별 패키지 페이지에 나열되어 있습니다. Functions 런타임 1.x 또는 2.x에 해당하는 주 버전은 바인딩의 참조 문서에 지정되어 있습니다.

`Install-Package`를 사용하여 바인딩을 참조하는 경우 [확장 번들](#extension-bundles)을 사용할 필요가 없습니다. 이 방법은 Visual Studio에 빌드된 클래스 라이브러리에만 적용됩니다.

### <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a> Visual Studio Code를 사용하는 C# 클래스 라이브러리

**Visual Studio Code** 에서는 .NET Core CLI의 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 명령을 사용하여 명령 프롬프트에서 C# 클래스 라이브러리 프로젝트용 패키지를 설치합니다. 다음 예제에서는 바인딩을 추가하는 방법을 보여 줍니다.

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI는 Azure Functions 2.x 개발에만 사용할 수 있습니다.

`<BINDING_TYPE_NAME>`을 필요한 바인딩이 포함된 패키지의 이름으로 바꿉니다. 원하는 바인딩 참조 문서는 [지원되는 바인딩 목록](./functions-triggers-bindings.md#supported-bindings)에서 찾을 수 있습니다.

예제의 `<TARGET_VERSION>`을 패키지의 특정 버전(예: `3.0.0-beta5`)으로 바꿉니다. 유효한 버전은 [NuGet.org](https://nuget.org)의 개별 패키지 페이지에 나열되어 있습니다. Functions 런타임 1.x 또는 2.x에 해당하는 주 버전은 바인딩의 참조 문서에 지정되어 있습니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure Function trigger and binding example](./functions-bindings-example.md)(Azure Functions 트리거 및 바인딩 예제)
