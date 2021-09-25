---
title: Azure Functions 핵심 도구 작업
description: Azure Functions에서 실행하기 전에 로컬 컴퓨터의 명령 프롬프트 및 터미널에서 Azure Functions를 코딩하고 테스트하는 방법을 알아봅니다.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 07/27/2021
ms.custom: devx-track-csharp, 80e4ff38-5174-43
ms.openlocfilehash: 5f2ea49df446c26453bb8cf54af52ab54b2c24b2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669718"
---
# <a name="work-with-azure-functions-core-tools"></a>Azure Functions 핵심 도구 작업

Azure Functions Core Tools를 사용하면 명령 프롬프트나 터미널에서 함수를 개발하고 테스트할 수 있습니다. 로컬 함수는 라이브 Azure 서비스에 연결할 수 있고 사용자는 전체 Functions 런타임을 사용하여 로컬 머신에서 함수를 디버깅할 수 있습니다. Azure 구독에 함수 앱을 배포할 수도 있습니다.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Core Tools를 사용하여 로컬 컴퓨터에서 함수를 개발하고 Azure에 게시하는 작업은 다음과 같은 기본 단계를 따릅니다.

> [!div class="checklist"]
> * [핵심 도구 및 종속성을 설치합니다.](#v2)
> * [언어별 템플릿에서 함수 앱 프로젝트를 만듭니다.](#create-a-local-functions-project)
> * [트리거 및 바인딩 확장을 등록합니다.](#register-extensions)
> * [스토리지 및 기타 연결을 정의합니다.](#local-settings)
> * [트리거와 언어별 템플릿에서 함수를 만듭니다.](#create-func)
> * [로컬에서 함수를 실행합니다.](#start)
> * [Azure에 프로젝트를 게시합니다.](#publish)

## <a name="prerequisites"></a>필수 조건

Azure Functions Core Tools는 현재 Azure 계정으로 인증하기 위해 [Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell](/powershell/azure/install-az-ps)를 사용합니다. 즉, Azure Functions Core Tools에서 [Azure에 게시](#publish)할 수 있도록 하려면 해당 도구 중 하나를 설치해야 합니다. 

## <a name="core-tools-versions"></a><a name="v2"></a>Core Tools 버전

Azure Functions Core Tools 네 가지 버전이 있습니다. 사용하는 버전은 로컬 개발 환경, [언어 선택](supported-languages.md)및 필요한 지원 수준에 따라 달라집니다.

아래 버전 탭을 선택하여 각 특정 버전에 대해 알아보고 자세한 설치 지침을 확인합니다.

# <a name="version-3x"></a>[버전 3.x](#tab/v3)

Azure Functions 런타임 버전 [3.x를](functions-versions.md) 지원합니다. 이 버전은 Windows, macOS 및 Linux를 지원하며, 설치에 플랫폼별 패키지 관리자 또는 npm을 사용합니다. 권장되는 버전의 Functions 런타임 및 핵심 도구입니다.

# <a name="version-4x-preview"></a>[버전 4.x(미리 보기)](#tab/v4)

미리 보기로 있는 Functions 런타임 [버전 4.x를](functions-versions.md) 지원합니다. 이 버전은 Windows, macOS 및 Linux를 지원하며, 설치에 플랫폼별 패키지 관리자 또는 npm을 사용합니다. .NET 6.0에서 C# 함수를 실행해야 하는 경우가 아니면 Functions 런타임 및 Core Tools 버전 3.x를 사용하는 것이 좋습니다.

# <a name="version-2x"></a>[버전 2.x](#tab/v2)

Azure Functions 런타임 버전 [2.x를](functions-versions.md) 지원합니다. 이 버전은 Windows, macOS 및 Linux를 지원하며, 설치에 플랫폼별 패키지 관리자 또는 npm을 사용합니다. 

# <a name="version-1x"></a>[버전 1.x](#tab/v1) 

Azure Functions 런타임 버전 1.x를 지원합니다. 이 버전의 도구는 Windows 컴퓨터에서만 지원되며 [npm 패키지](https://www.npmjs.com/package/azure-functions-core-tools)에서 설치됩니다.

---

지정된 컴퓨터에는 Core Tools의 한 버전만 설치할 수 있습니다.  별도로 언급하지 않는 한 이 문서의 예제는 3.x 버전용입니다.

## <a name="install-the-azure-functions-core-tools"></a>Azure Functions 핵심 도구 설치

[Azure Functions 핵심 도구]에는 로컬 개발 컴퓨터에서 실행할 수 있는 Azure Functions 런타임을 제공하는 동일한 런타임 버전이 포함됩니다. 또한 함수를 만들고, Azure에 연결하고, 함수 프로젝트를 배포하는 명령을 제공합니다.

버전 2.x부터 Core Tools는 .NET Core를 기반으로 합니다. 즉, 버전 2.x 이상은 [Windows,](?tabs=windows#v2) [macOS](?tabs=macos#v2)및 [Linux](?tabs=linux#v2)를 포함하여 .NET Core에서 지원하는 모든 플랫폼에서 실행됩니다.

> [!IMPORTANT]
> non-.NET 언어의 경우 [확장 번들]를 사용하여 .NET Core SDK 설치하기 위한 요구 사항을 무시할 수 있습니다.

# <a name="windows"></a>[Windows](#tab/windows/v4)

Core Tools 버전 4.x를 설치하려면 현재 npm이 필요합니다. 다른 패키지 관리자를 사용하여 이전 버전의 Core Tools를 설치한 경우 버전 4.x를 설치하기 전에 제거합니다.

1. 아직 설치하지 않은 경우 [npm 을 Node.js 설치합니다.](https://nodejs.org/en/download/) 

1. 다음 npm 명령을 실행하여 Core Tools 패키지를 설치합니다.

    ```
    npm i -g azure-functions-core-tools@4 --unsafe-perm true
    ```

1. [확장 번들](functions-bindings-register.md#extension-bundles)을 사용하지 않으려는 경우 [Windows용 .NET Core 3.x SDK](https://dotnet.microsoft.com/download)를 설치합니다.

# <a name="windows"></a>[Windows](#tab/windows/v3)

다음 단계에서는 MSI(Windows 설치 프로그램)를 사용하여 Core Tools v3.x를 설치합니다. 다른 패키지 기반 설치 관리자에 대한 자세한 내용은 Core Tools 추가 정보 를 [참조하세요.](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows)

1. 다음과 같이 Windows 버전에 따라 Core Tools 설치 프로그램을 다운로드하여 실행합니다.

    - [v3.x - Windows 64-비트](https://go.microsoft.com/fwlink/?linkid=2135274) (권장. [Visual Studio Code 디버깅](functions-develop-vs-code.md#debugging-functions-locally)에는 64-비트가 필요합니다.)
    - [v3.x - Windows 32-비트](https://go.microsoft.com/fwlink/?linkid=2135275)

1. [확장 번들](functions-bindings-register.md#extension-bundles)을 사용하지 않으려는 경우 [Windows용 .NET Core 3.x SDK](https://dotnet.microsoft.com/download)를 설치합니다.

# <a name="windows"></a>[Windows](#tab/windows/v2)

Core Tools 버전 2.x를 설치하려면 npm이 필요합니다. [Chocolatey를 사용하여 패키지를 설치할](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#azure-functions-core-tools)수도 있습니다.

1. 아직 설치하지 않은 경우 [npm 을 Node.js 설치합니다.](https://nodejs.org/en/download/) 

1. 다음 npm 명령을 실행하여 Core Tools 패키지를 설치합니다.

    ```
    npm install -g azure-functions-core-tools@2 --unsafe-perm true
    ```

1. [확장 번들](functions-bindings-register.md#extension-bundles)을 사용하지 않으려는 경우 [Windows용 .NET Core 3.x SDK](https://dotnet.microsoft.com/download)를 설치합니다.

# <a name="windows"></a>[Windows](#tab/windows/v1)

Core Tools 버전 1.x를 설치해야 하는 경우 자세한 내용은 [GitHub 리포지토리를](https://github.com/Azure/azure-functions-core-tools/blob/v1.x/README.md#installing) 참조하세요.

# <a name="macos"></a>[macOS](#tab/macos/v4)

다음 단계에서는 Homebrew를 사용하여 macOS에 핵심 도구를 설치합니다.

1. 아직 설치되지 않은 경우 [Homebrew](https://brew.sh/)를 설치합니다.

1. 다음과 같이 핵심 도구 패키지를 설치합니다.

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@4
    # if upgrading on a machine that has 2.x or 3.x installed:
    brew link --overwrite azure-functions-core-tools@4
    ```
    
1. [확장 번들](functions-bindings-register.md#extension-bundles)을 사용하지 않으려는 경우 [macOS용 .NET Core 3.x SDK](https://dotnet.microsoft.com/download)를 설치합니다.

# <a name="macos"></a>[macOS](#tab/macos/v3)

다음 단계에서는 Homebrew를 사용하여 macOS에 핵심 도구를 설치합니다.

1. 아직 설치되지 않은 경우 [Homebrew](https://brew.sh/)를 설치합니다.

1. 다음과 같이 핵심 도구 패키지를 설치합니다.

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed:
    brew link --overwrite azure-functions-core-tools@3
    ```
    
1. [확장 번들](functions-bindings-register.md#extension-bundles)을 사용하지 않으려는 경우 [macOS용 .NET Core 3.x SDK](https://dotnet.microsoft.com/download)를 설치합니다.

# <a name="macos"></a>[macOS](#tab/macos/v2)

다음 단계에서는 Homebrew를 사용하여 macOS에 핵심 도구를 설치합니다.

1. 아직 설치되지 않은 경우 [Homebrew](https://brew.sh/)를 설치합니다.

1. 다음과 같이 핵심 도구 패키지를 설치합니다.

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@2
    ```
    
1. [확장 번들](functions-bindings-register.md#extension-bundles)을 사용하지 않으려는 경우 [macOS용 .NET Core 3.x SDK](https://dotnet.microsoft.com/download)를 설치합니다.

# <a name="macos"></a>[macOS](#tab/macos/v1)

Core Tools 버전 1.x는 macOS에서 지원되지 않습니다. macOS에서 버전 2.x 이상 버전을 사용합니다.

# <a name="linux"></a>[Linux](#tab/linux/v4)

[!INCLUDE [functions-core-tools-linux-install](../../includes/functions-core-tools-linux-install.md)]

5. 다음과 같이 핵심 도구 패키지를 설치합니다.

    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-4
    ```

1. [확장 번들](functions-bindings-register.md#extension-bundles)을 사용하지 않으려는 경우 [Linux용 .NET Core 3.x SDK](https://dotnet.microsoft.com/download)를 설치합니다.


# <a name="linux"></a>[Linux](#tab/linux/v3)

[!INCLUDE [functions-core-tools-linux-install](../../includes/functions-core-tools-linux-install.md)]

5. 다음과 같이 핵심 도구 패키지를 설치합니다.

    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-3
    ```

1. [확장 번들](functions-bindings-register.md#extension-bundles)을 사용하지 않으려는 경우 [Linux용 .NET Core 3.x SDK](https://dotnet.microsoft.com/download)를 설치합니다.

# <a name="linux"></a>[Linux](#tab/linux/v2)

[!INCLUDE [functions-core-tools-linux-install](../../includes/functions-core-tools-linux-install.md)]

5. 다음과 같이 핵심 도구 패키지를 설치합니다.

    ```bash
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-2
    ```

1. [확장 번들](functions-bindings-register.md#extension-bundles)을 사용하지 않으려는 경우 [Linux용 .NET Core 3.x SDK](https://dotnet.microsoft.com/download)를 설치합니다.


# <a name="linux"></a>[Linux](#tab/linux/v1)

Core Tools 버전 1.x는 Linux에서 지원되지 않습니다. Linux에서 버전 2.x 이상 버전을 사용합니다.

---

## <a name="changing-core-tools-versions"></a>핵심 도구 버전 변경

다른 버전의 Core Tools로 변경하는 경우 원래 설치와 동일한 패키지 관리자를 사용하여 다른 패키지 버전으로 이동해야 합니다. 예를 들어 npm을 사용하여 Core Tools 버전 2.x를 설치한 경우 다음 명령을 사용하여 버전 3.x로 업그레이드해야 합니다.

```bash
npm install -g azure-functions-core-tools@3 --unsafe-perm true
```

MSI(Windows 설치 관리자)를 사용하여 Windows Core Tools를 설치한 경우 다른 버전을 설치하기 전에 프로그램 추가에서 이전 버전을 제거해야 합니다.

## <a name="create-a-local-functions-project"></a>로컬 Functions 프로젝트 만들기

Functions 프로젝트 디렉터리에는 언어에 관계없이 다음 파일과 폴더가 포함되어 있습니다. 

| 파일 이름 | Description |
| --- | --- |
| host.json | 자세한 내용은 [host.json 참조](functions-host-json.md)를 참조하세요. |
| local.settings.json | 앱 설정을 포함하여 로컬에서 실행할 때 핵심 도구에서 사용하는 설정입니다. 자세히 알아보려면 [로컬 설정](#local-settings)을 참조하세요. |
| .gitignore | local.settings.json 파일이 실수로 Git 리포지토리에 게시되는 것을 방지합니다. 자세히 알아보려면 [로컬 설정](#local-settings)을 참조하세요.|
| .vscode\extensions.json | Visual Studio Code에서 프로젝트 폴더를 열 때 사용되는 설정 파일입니다.  |

Functions 프로젝트 폴더에 대한 자세한 내용은 [Azure Functions 개발자 가이드](functions-reference.md#folder-structure)를 참조하세요.

터미널 창이나 명령 프롬프트에서 다음 명령을 실행하여 프로젝트 및 로컬 Git 리포지토리를 만듭니다.

```
func init MyFunctionProj
```

이 예에서는 새 `MyFunctionProj` 폴더에 Functions 프로젝트를 만듭니다. 프로젝트의 기본 언어를 선택하라는 메시지가 표시됩니다. 

프로젝트 초기화에는 다음 고려 사항이 적용됩니다.

+ 명령에 `--worker-runtime` 옵션을 제공하지 않으면 언어를 선택하라는 메시지가 표시됩니다. 자세한 내용은 [func init 참조](functions-core-tools-reference.md#func-init)를 확인하세요.

+ 프로젝트 이름을 제공하지 않으면 현재 폴더가 초기화됩니다. 

+ 프로젝트를 사용자 지정 Linux 컨테이너에 게시하려는 경우 `--dockerfile` 옵션을 사용하여 프로젝트에 대해 Dockerfile이 생성되었는지 확인합니다. 자세히 알아보려면 [사용자 지정 이미지를 사용하여 Linux에서 함수 만들기](functions-create-function-linux-custom-image.md)를 참조하세요. 

특정 언어에는 다음과 같은 추가 고려 사항이 있을 수 있습니다.

# <a name="c"></a>[C\#](#tab/csharp)

+ 기본적으로 Core Tools의 버전 2.x 이상 버전은 .NET 런타임에 대한 함수 앱 프로젝트를 [C# 클래스 프로젝트](functions-dotnet-class-library.md)(.csproj)로 만듭니다. 버전 3.x는 또한 [분리된 프로세스의 .NET 5.0에서 실행](dotnet-isolated-process-guide.md)하는 함수 만들기를 지원합니다. 이러한 C# 프로젝트는 Visual Studio 또는 Visual Studio Code에서 사용할 수 있으며, 디버그 중과 Azure에 게시할 때 컴파일됩니다. 

+ C# 스크립트(.csx) 파일로 로컬에서 작업하려면 `--csx` 매개 변수를 사용합니다. Azure Portal에서 함수를 만들 때와 Core Tools 버전 1.x를 사용할 때 가져오는 동일한 파일입니다. 자세한 내용은 [func init 참조](functions-core-tools-reference.md#func-init)를 참조하세요.

# <a name="java"></a>[Java](#tab/java)

+ Java는 Maven 원형을 사용하여 HTTP 트리거된 첫 번째 함수와 함께 로컬 Functions 프로젝트를 만듭니다. `func init` 및 `func new`를 사용하는 대신 [명령줄 빠른 시작](./create-first-function-cli-java.md)의 단계를 따라야 합니다.  

# <a name="javascript"></a>[JavaScript](#tab/node)

+ `node`의 `--worker-runtime` 값을 사용하려면 `--language`를 `javascript`로 지정합니다. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell에 대한 추가 고려 사항은 없습니다.

# <a name="python"></a>[Python](#tab/python)

+ 가상 환경 내부에서 `func init`를 포함한 모든 명령을 실행해야 합니다. 자세한 내용은 [가상 환경 만들기 및 활성화](create-first-function-cli-python.md#create-venv)를 참조하세요.

# <a name="typescript"></a>[TypeScript](#tab/ts)

+ `node`의 `--worker-runtime` 값을 사용하려면 `--language`를 `javascript`로 지정합니다.

+ TypeScript와 관련된 `func init` 동작은 [JavaScript 개발자 참조의 TypeScript 섹션](functions-reference-node.md#typescript)을 참조하세요. 

--- 

## <a name="register-extensions"></a>확장 등록

런타임 버전 2.x부터 Functions 바인딩은 .NET 확장(NuGet) 패키지로 구현됩니다. 컴파일된 C# 프로젝트의 경우 사용 중인 특정 트리거 및 바인딩에 대한 NuGet 확장 패키지를 참조하기만 하면 됩니다. HTTP 바인딩 및 타이머 트리거에는 확장이 필요하지 않습니다. 

C#이 아닌 프로젝트의 개발 환경을 개선하기 위해 Functions를 사용하면 host.json 프로젝트 파일에서 버전이 지정된 확장 번들을 참조할 수 있습니다. [확장 번들](functions-bindings-register.md#extension-bundles)은 앱에서 모든 확장을 사용할 수 있도록 하고 확장 간에 패키지 호환성 문제가 발생할 가능성을 제거합니다. 확장 번들은 .NET Core 2.x SDK를 설치하고 extension.csproj 파일을 처리해야 하는 요구 사항도 제거합니다.

확장 번들은 C# 호환 프로젝트 이외의 Functions 프로젝트에 권장되는 접근 방식입니다. 이러한 프로젝트의 경우 초기화 중에 확장 번들 설정이 _host.json_ 파일에 생성됩니다. 이것이 작동한다면 이 전체 섹션을 건너뛸 수 있습니다.  

### <a name="use-extension-bundles"></a>확장 번들 사용

[!INCLUDE [Register extensions](../../includes/functions-extension-bundles.md)]

 사용자 언어에서 지원하는 경우 `func init`를 호출한 후 확장 번들이 이미 사용하도록 설정되어 있어야 합니다. function.json 파일에 바인딩을 추가하기 전에 host.json에 확장 번들을 추가해야 합니다. 자세한 내용은 [Azure Functions 바인딩 확장 등록](functions-bindings-register.md#extension-bundles)을 참조하세요. 

### <a name="explicitly-install-extensions"></a>명시적으로 확장 설치

번들에 없는 특정 버전의 확장을 대상으로 지정해야 하는 경우와 같이 확장 번들을 사용할 수 없는 비 .NET 프로젝트의 경우가 있을 수 있습니다. 자주 발생하지는 않지만 핵심 도구를 사용하여 프로젝트에 필요한 특정 확장 패키지를 로컬로 설치할 수 있습니다. 자세한 내용은 [명시적으로 확장 설치](functions-bindings-register.md#explicitly-install-extensions)를 참조하세요.

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

기본적으로 이러한 설정은 프로젝트가 Azure에 게시될 때 자동으로 마이그레이션되지 않습니다. 게시할 때 [`--publish-local-settings` 옵션][func azure functionapp publish]을 사용하여 이러한 설정이 Azure의 함수 앱에 추가되었는지 확인합니다. `ConnectionStrings` 섹션의 값은 게시되지 않습니다.

이 함수 앱 설정 값은 코드에서 환경 변수로 읽을 수도 있습니다. 자세한 내용은 다음 언어별 참조 항목의 Environment 변수 섹션을 참조하세요.

* [미리 컴파일된 C#](functions-dotnet-class-library.md#environment-variables)
* [C# 스크립트(.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

[`AzureWebJobsStorage`]에 유효한 스토리지 연결 문자열이 설정되어 있지 않고 에뮬레이터가 사용되지 않으면, 다음 오류 메시지가 표시됩니다.

> local.settings.json에 AzureWebJobsStorage 값이 없습니다. 이 값은 HTTP 이외의 모든 트리거에 필요합니다. 'func azure functionapp fetch-app-settings \<functionAppName\>'를 실행하거나 local.settings.json에서 연결 문자열을 지정할 수 있습니다.

### <a name="get-your-storage-connection-strings"></a>스토리지 연결 문자열 가져오기

개발에 Microsoft Azure Storage 에뮬레이터를 사용하는 경우에도 실제 스토리지를 연결하여 로컬로 실행하는 것이 좋을 수 있습니다. 이미 [스토리지 계정을 만든](../storage/common/storage-account-create.md) 것으로 가정하면 몇 가지 방법 중 하나에서 유효한 스토리지 연결 문자열을 가져올 수 있습니다.

# <a name="portal"></a>[포털](#tab/portal)

1. [Azure Portal]에서 **스토리지 계정** 을 검색하여 선택합니다. 

    ![Azure Portal에서 스토리지 계정 선택](./media/functions-run-local/select-storage-accounts.png)
  
1.  스토리지 계정을 선택하고, **설정** 에서 **액세스 키** 를 선택한 다음, **연결 문자열** 값 중 하나를 복사합니다.

    ![Azure Portal에서 연결 문자열 복사](./media/functions-run-local/copy-storage-connection-portal.png)

# <a name="core-tools"></a>[핵심 도구](#tab/azurecli)

프로젝트 루트에서 다음 명령 중 하나를 사용하여 Azure에서 연결 문자열을 다운로드합니다.

  + 기존 함수 앱에서 모든 설정을 다운로드합니다.

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```

  + 특정 스토리지 계정에 대한 연결 문자열을 가져옵니다.

    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Azure에 아직 로그인하지 않은 경우 로그인하라는 메시지가 표시됩니다. 해당 명령은 파일의 local.settings.json 파일에 있는 모든 기존 설정을 덮어씁니다. 자세한 내용은 [`func azure functionapp fetch-app-settings`](functions-core-tools-reference.md#func-azure-functionapp-fetch-app-settings) 및 [`func azure storage fetch-connection-string`](functions-core-tools-reference.md#func-azure-storage-fetch-connection-string) 명령을 참조하세요.

# <a name="storage-explorer"></a>[Storage Explorer](#tab/storageexplorer)

1. [Azure Storage Explorer](https://storageexplorer.com/)를 실행합니다. 

1. **탐색기** 에서 구독을 확장한 다음 **스토리지 계정** 을 확장합니다.

1. 스토리지 계정을 선택하고 기본 또는 보조 연결 문자열을 복사합니다.

    ![Storage Explorer에서 연결 문자열 복사](./media/functions-run-local/storage-explorer.png)

---

## <a name="create-a-function"></a><a name="create-func"></a>함수 만들기

기존 프로젝트에서 함수를 만들려면 다음 명령을 실행합니다.

```
func new
```

버전 3.x/2.x에서 `func new`를 실행하면 함수 앱의 기본 언어로 템플릿을 선택하라는 메시지가 표시됩니다. 다음으로 함수의 이름을 선택하라는 메시지가 표시됩니다. 버전 1.x에서는 언어를 선택하라고 요청합니다. 

`func new` 명령에서 함수 이름과 템플릿을 지정할 수도 있습니다. 다음 예에서는 `--template` 옵션을 사용하여 `MyHttpTrigger`라는 HTTP 트리거를 만듭니다.

```
func new --template "Http Trigger" --name MyHttpTrigger
```

이 예에서는 `MyQueueTrigger`라는 Queue Storage 트리거를 만듭니다.

```
func new --template "Queue Trigger" --name MyQueueTrigger
```

자세한 내용은 [`func new` 명령](functions-core-tools-reference.md#func-new)을 참조하세요.

## <a name="run-functions-locally"></a><a name="start"></a>로컬로 함수 실행

Functions 프로젝트를 실행하려면 프로젝트의 루트 디렉터리에서 Functions 호스트를 실행합니다. 해당 호스트는 프로젝트의 모든 함수에 대한 트리거를 사용하는 것으로 설정합니다. [`start` 명령](functions-core-tools-reference.md#func-start)은 프로젝트 언어에 따라 달라집니다.

# <a name="c"></a>[C\#](#tab/csharp)

```
func start
```

# <a name="java"></a>[Java](#tab/java)

```
mvn clean package 
mvn azure-functions:run
```

# <a name="javascript"></a>[JavaScript](#tab/node)

```
func start
```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```
func start
```

# <a name="python"></a>[Python](#tab/python)

```
func start
```
이 명령은 [가상 환경에서 실행](./create-first-function-cli-python.md)해야 합니다.

# <a name="typescript"></a>[TypeScript](#tab/ts)

```
npm install
npm start     
```

---

>[!NOTE]  
> Functions 런타임 버전 1.x에는 대신 `func host start`가 필요합니다. 자세한 내용은 [Azure Functions Core Tools 참조](functions-core-tools-reference.md?tabs=v1#func-start)를 참조하세요. 

Functions 호스트가 시작되면 다음 예와 같이 HTTP 트리거 함수의 URL을 출력합니다.

<pre>
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
</pre>

>[!IMPORTANT]
>로컬로 실행하는 경우 HTTP 엔드포인트에 대한 권한 부여는 실행되지 않습니다. 즉, 모든 로컬 HTTP 요청은 `authLevel = "anonymous"`로 처리됩니다. 자세한 내용은 [HTTP 바인딩 문서](functions-bindings-http-webhook-trigger.md#authorization-keys)를 참조하세요.

### <a name="passing-test-data-to-a-function"></a>테스트 데이터를 함수에 전달

로컬로 함수를 테스트하려면 [Functions 호스트를 시작](#start)하고 HTTP 요청을 사용하여 로컬 서버에서 엔드포인트를 호출합니다. 호출하는 엔드포인트는 함수의 형식에 따라 달라집니다.

>[!NOTE]
> 이 항목의 예제에서는 cURL 도구를 사용하여 터미널 또는 명령 프롬프트의 HTTP 요청을 보냅니다. 로컬 서버에 HTTP 요청을 보내도록 선택한 도구를 사용할 수 있습니다. cURL 도구는 Linux 기반 시스템 및 Windows 10 빌드 17063 이상에서 기본적으로 사용할 수 있습니다. Windows에서는 먼저 [cURL 도구](https://curl.haxx.se/)를 다운로드한 후 설치해야 합니다.

함수를 테스트하는 방법에 대한 일반적인 내용은 [Azure Functions에서 코드를 테스트하기 위한 전략](functions-test-a-function.md)을 참조하세요.

#### <a name="http-and-webhook-triggered-functions"></a>HTTP 및 웹후크 트리거된 함수

다음 엔드포인트를 호출하여 HTTP 및 웹후크 트리거된 함수를 로컬로 실행합니다.

```
http://localhost:{port}/api/{function_name}
```

Functions 호스트가 수신 대기 중인 동일한 서버 이름 및 포트를 사용하도록 합니다. Function 호스트를 시작할 때 생성된 출력에서 이 항목을 확인합니다. 트리거에서 지원하는 HTTP 메서드를 사용하여 이 URL을 호출할 수 있습니다.

다음 cURL 명령은 _이름_ 매개 변수를 쿼리 문자열에 전달한 GET 요청에서 `MyHttpTrigger` 빠른 시작 함수를 트리거합니다.

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

다음 예제는 요청 본문에서 _이름_ 을 전달하는 POST 요청에서 호출되는 동일한 함수입니다.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
curl --request POST http://localhost:7071/api/MyHttpTrigger --data "{'name':'Azure Rocks'}"
```
---

쿼리 문자열에서 데이터를 전달하는 브라우저에서 GET 요청을 수행할 수 있습니다. 다른 모든 HTTP 메서드에서 POST 요청을 지원하는 cURL, Fiddler, Postman 또는 비슷한 HTTP 테스트 도구를 사용해야 합니다.

#### <a name="non-http-triggered-functions"></a>HTTP가 아닌 트리거된 함수

HTTP 및 Event Grid 트리거를 제외한 모든 함수의 경우 _관리 엔드포인트_ 라는 특수 엔드포인트를 호출하여 REST를 사용하여 로컬에서 함수를 테스트할 수 있습니다. 로컬 서버에서 HTTP POST 요청으로 이 엔드포인트를 호출하면 함수를 트리거합니다. 

로컬로 Event Grid 트리거된 함수를 테스트하려면 [뷰어 웹앱을 사용하여 로컬 테스트](functions-bindings-event-grid-trigger.md#local-testing-with-viewer-web-app)를 참조하세요.

필요에 따라 POST 요청의 본문에서 실행에 테스트 데이터를 전달할 수 있습니다. 이 기능은 Azure Portal에서 **테스트** 탭과 비슷합니다.

다음 관리자 엔드포인트를 호출하여 HTTP가 아닌 함수를 트리거합니다.

```
http://localhost:{port}/admin/functions/{function_name}
```

함수의 관리자 엔드포인트에 테스트 데이터를 전달하려면 POST 요청 메시지의 본문에서 데이터를 제공해야 합니다. 메시지 본문에는 다음 JSON 형식이 필요합니다.

```JSON
{
    "input": "<trigger_input>"
}
```

`<trigger_input>` 값에는 함수에 필요한 형식의 데이터가 포함됩니다. 다음 cURL 예제는 `QueueTriggerJS` 함수에 대한 POST 요청입니다. 이 경우에 입력은 큐에 위치해야 하는 메시지에 해당하는 문자열입니다.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```bash
curl --request POST -H "Content-Type:application/json" --data "{'input':'sample queue data'}" http://localhost:7071/admin/functions/QueueTrigger
```
---

Azure의 함수 앱에서 관리자 엔드포인트를 호출할 때 액세스 키를 제공해야 합니다. 자세한 내용은 [함수 액세스 키](functions-bindings-http-webhook-trigger.md#authorization-keys)를 참조하세요.

## <a name="publish-to-azure"></a><a name="publish"></a>Azure에 게시

Azure Functions Core Tools는 세 가지 배포 유형을 지원합니다.

| 배포 유형 | 명령 | 설명 |
| ----- | ----- | ----- |
| 프로젝트 파일 | [`func azure functionapp publish`](functions-core-tools-reference.md#func-azure-functionapp-publish) | [zip 배포](functions-deployment-technologies.md#zip-deploy)를 사용하여 함수 프로젝트 파일을 함수 앱에 직접 배포합니다. |
| 사용자 지정 컨테이너 | `func deploy` | 프로젝트를 Linux 함수 앱에 사용자 지정 Docker 컨테이너로 배포합니다.  |
| Kubernetes 클러스터 | `func kubernetes deploy` | Linux 함수 앱을 Kubernetes 클러스터에 고객 Docker 컨테이너로 배포합니다. | 

### <a name="before-you-publish"></a>게시하기 전에 

>[!IMPORTANT]
>Core Tools에서 Azure에 게시하려면 [Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell](/powershell/azure/install-az-ps)을 로컬로 설치해야 합니다.  

프로젝트 폴더는 게시하지 않아야 하는 언어별 파일 및 디렉터리를 포함할 수 있습니다. 제외된 항목은 루트 프로젝트 폴더의 .funcignore 파일에 나열됩니다.  

코드를 배포할 [Azure 구독에서 생성된 함수 앱](functions-cli-samples.md#create)이 이미 있어야 합니다. 컴파일해야 하는 프로젝트는 이진 파일을 배포할 수 있는 방식으로 빌드해야 합니다.

Azure CLI 또는 Azure PowerShell을 사용하여 명령 프롬프트 또는 터미널 창에서 함수 앱을 만드는 방법을 알아보려면 [서버리스 실행을 위한 함수 앱 만들기](./scripts/functions-cli-create-serverless.md)를 참조하세요. 

>[!IMPORTANT]
> Azure Portal에서 함수 앱을 만들 때는 기본적으로 Function 런타임 버전 3.x가 사용됩니다. 함수 앱이 런타임 버전 1.x를 사용하도록 하려면 [버전 1.x에서 실행](functions-versions.md#creating-1x-apps)의 지침을 따르세요.
> 기존 함수가 있는 함수 앱의 런타임 버전은 변경할 수 없습니다.


### <a name="deploy-project-files"></a><a name="project-file-deployment"></a>프로젝트 파일 배포

Azure의 함수 앱에 로컬 코드를 게시하려면 `publish` 명령을 사용합니다.

```
func azure functionapp publish <FunctionAppName>
```

이러한 종류의 배포에는 다음 고려 사항이 적용됩니다.

+ 게시하면 함수 앱의 기존 파일을 덮어씁니다.

+ [`--publish-local-settings` 옵션][func azure functionapp publish]을 사용하여 local.settings.json 파일의 값을 기반으로 함수 앱에서 앱 설정을 자동으로 만듭니다.  

+ 컴파일된 프로젝트에서 [원격 빌드](functions-deployment-technologies.md#remote-build)가 수행됩니다. 이는 [`--no-build` 옵션][func azure functionapp publish]을 사용하여 제어할 수 있습니다.  

+ 프로젝트는 [배포 패키지에서 실행](run-functions-from-deployment-package.md)되도록 배포됩니다. 권장 배포 모드를 사용하지 않도록 설정하려면 [`--nozip` 옵션][func azure functionapp publish]을 사용합니다.

+ Java는 Maven을 사용하여 로컬 프로젝트를 Azure에 게시합니다. 대신 `mvn azure-functions:deploy` 명령을 사용하여 Azure에 게시합니다. Azure 리소스는 초기 배포 중에 생성됩니다.

+ 구독에 존재하지 않는 `<FunctionAppName>`에 게시하려고 하면 오류가 발생합니다. 

### <a name="kubernetes-cluster"></a>Kubernetes 클러스터

Functions를 사용하면 Docker 컨테이너에서 실행할 Functions 프로젝트를 정의할 수도 있습니다. `func init`의 [`--docker` 옵션][func init]을 사용하여 특정 언어에 대한 Dockerfile을 생성합니다. 이 파일은 배포할 컨테이너를 만들 때 사용됩니다. 

핵심 도구를 사용하여 프로젝트를 Kubernetes 클러스터에 사용자 지정 컨테이너 이미지로 배포할 수 있습니다. 사용하는 명령은 클러스터에서 사용되는 배율 조정기 유형에 따라 다릅니다.  

다음 명령은 Dockerfile을 사용하여 컨테이너를 생성하고 Kubernetes 클러스터에 배포합니다. 

# <a name="keda"></a>[KEDA](#tab/keda)

```command
func kubernetes deploy --name <DEPLOYMENT_NAME> --registry <REGISTRY_USERNAME> 
```

자세한 내용은 [Kubernetes에 함수 앱 배포](functions-kubernetes-keda.md#deploying-a-function-app-to-kubernetes)를 참조하세요. 

# <a name="defaultknative"></a>[Default/KNative](#tab/default)

```command
func deploy --name <FUNCTION_APP> --platform kubernetes --registry <REGISTRY_USERNAME> 
```

위의 예에서 `<FUNCTION_APP>`을 Azure의 함수 앱 이름으로 바꾸고 `<REGISTRY_USERNAME>`을 Docker 사용자 이름과 같은 레지스트리 계정 이름으로 바꿉니다. 컨테이너는 로컬로 빌드되고 `<FUNCTION_APP>`에 기반한 이미지 이름으로 Docker 레지스트리 계정에 푸시됩니다. Docker 명령줄 도구가 설치되어 있어야 합니다.

자세한 내용은 [`func deploy` 명령](functions-core-tools-reference.md#func-deploy)을 참조하세요.

---

Kubernetes 없이 Azure에 사용자 지정 컨테이너를 게시하는 방법을 알아보려면 [사용자 지정 컨테이너를 사용하여 Linux에서 함수 만들기](functions-create-function-linux-custom-image.md)를 참조하세요.

## <a name="monitoring-functions"></a>함수 모니터링

함수 실행을 모니터링할 때는 Azure Application Insights와 함수를 통합하는 방식을 사용하는 것이 좋습니다. 실행 로그를 로컬 컴퓨터로 스트림할 수도 있습니다. 자세히 알아보려면 [Azure Functions 모니터링](functions-monitoring.md)을 참조하세요.

### <a name="application-insights-integration"></a>Application Insights 통합

Azure에서 함수 앱을 만들 때 Application Insights 통합을 사용하도록 설정해야 합니다. 일부 이유로 함수 앱이 Application Insights 인스턴스에 연결되지 않은 경우 Azure Portal에서 이 통합을 쉽게 수행할 수 있습니다. 자세한 정보를 알아보려면 [Application Insights 통합 사용](configure-monitoring.md#enable-application-insights-integration)을 참조하세요.

### <a name="enable-streaming-logs"></a>스트리밍 로그 사용

로컬 컴퓨터의 명령줄 세션에서 함수로 생성되는 로그 파일의 스트림을 볼 수 있습니다. 

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

이 유형의 스트리밍 로그에는 함수 앱에 대한 Application Insights 통합을 사용하도록 설정해야 합니다.   


## <a name="next-steps"></a>다음 단계

Azure Functions Core Tools [Microsoft 학습 모듈](/learn/modules/develop-test-deploy-azure-functions-with-core-tools/)을 사용하여 Azure Functions를 개발, 테스트 및 게시하는 방법을 알아보세요. Azure Functions Core Tools는 [오픈 소스이며 GitHub에서 호스트됩니다](https://github.com/azure/azure-functions-cli).  
버그 또는 기능 요청을 제출하려면 [GitHub 문제를 개설](https://github.com/azure/azure-functions-cli/issues)합니다.

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[`AzureWebJobsStorage`]: functions-app-settings.md#azurewebjobsstorage
[확장 번들]: functions-bindings-register.md#extension-bundles
[func azure functionapp publish]: functions-core-tools-reference.md?tabs=v2#func-azure-functionapp-publish
[func init]: functions-core-tools-reference.md?tabs=v2#func-init
