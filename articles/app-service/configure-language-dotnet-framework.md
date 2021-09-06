---
title: ASP.NET 앱 구성
description: Azure App Service에서 ASP.NET 앱을 구성하는 방법을 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여줍니다.
ms.devlang: dotnet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 5fc4e858dbc39f13b3f07405c262086b2b789830
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528522"
---
# <a name="configure-an-aspnet-app-for-azure-app-service"></a>Azure App Service용 ASP.NET 앱 구성

> [!NOTE]
> ASP.NET Core의 경우 [Azure App Service용 ASP.NET Core 앱 구성](configure-language-dotnetcore.md)을 참조하세요.

ASP.NET 앱은 컴파일된 이진 파일로 Azure App Service에 배포해야 합니다. Visual Studio 게시 도구는 솔루션을 빌드한 다음 컴파일된 이진 파일을 직접 배포하는 반면, App Service 배포 엔진은 코드 리포지토리를 먼저 배포한 다음 이진 파일을 컴파일합니다.

이 가이드는 ASP.NET 개발자를 위한 주요 개념 및 지침을 제공합니다. Azure App Service를 사용한 적이 없는 경우 [ASP.NET 빠른 시작](./quickstart-dotnetcore.md?tabs=netframework48)과 [SQL Database를 사용한 ASP.NET 자습서](app-service-web-tutorial-dotnet-sqldatabase.md)를 먼저 따릅니다.

## <a name="show-supported-net-framework-runtime-versions"></a>지원되는 .NET Framework 런타임 버전 표시

App Service의 Windows 인스턴스에는 지원되는 모든 .NET Framework 버전이 이미 설치되어 있습니다. 사용할 수 있는 .NET Framework 런타임과 SDK 버전을 표시하려면 `https://<app-name>.scm.azurewebsites.net/DebugConsole`로 이동한 다음 브라우저 기반 콘솔에서 다음 명령을 실행합니다.

CLR 4 런타임 버전(.NET Framework 4 이상)의 경우:

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\"
```

최신 .NET Framework 버전은 바로 사용하지 못할 수 있습니다.

CLR 2 런타임 버전(.NET Framework 3.5 이하)의 경우:

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\"
```

## <a name="show-current-net-framework-runtime-version"></a>현재 .NET Framework 런타임 버전 표시

[Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query netFrameworkVersion
```

값 `v4.0`은 최신 CLR 4 버전(.NET Framework 4.x)이 사용됨을 의미합니다. 값 `v2.0`은 CLR 2 버전(.NET Framework 3.5)이 사용됨을 의미합니다.

## <a name="set-net-framework-runtime-version"></a>.NET Framework 런타임 버전 설정

기본적으로 App Service는 지원되는 최신 .NET Framework 버전을 사용하여 ASP.NET 앱을 실행합니다. 대신 .NET Framework 3.5를 사용하여 앱을 실행하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다(v2.0은 CLR 2를 의미함).

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --net-framework-version v2.0
```

## <a name="access-environment-variables"></a>환경 변수 액세스

App Service에서는 앱 코드 외부에서 [앱 설정](configure-common.md#configure-app-settings)과 연결 문자열을 지정할 수 있습니다. 그런 다음 표준 ASP.NET 패턴을 사용하여 모든 클래스에서 해당 설정과 연결 문자열에 액세스할 수 있습니다.

```csharp
using System.Configuration;
...
// Get an app setting
ConfigurationManager.AppSettings["MySetting"];
// Get a connection string
ConfigurationManager.ConnectionStrings["MyConnection"];
}
```

App Service와 *web.config* 에서 이름이 같은 앱 설정을 구성하면 App Service 값이 *web.config* 값보다 우선합니다. 로컬 *web.config* 값을 사용하면 앱을 로컬로 디버그할 수 있지만 App Service 값을 사용하면 프로덕션 설정으로 프로덕션 환경에서 앱을 실행할 수 있습니다. 연결 문자열은 동일한 방식으로 작동합니다. 이러한 방식으로 코드를 변경하지 않고도 애플리케이션 비밀을 코드 리포지토리 외부에 유지하고 적절한 값에 액세스할 수 있습니다.

## <a name="deploy-multi-project-solutions"></a>다중 프로젝트 솔루션 배포

Visual Studio 솔루션에 여러 프로젝트가 포함된 경우 Visual Studio 게시 프로세스에는 배포할 프로젝트 선택이 이미 포함되어 있습니다. Git 또는 [빌드 자동화가 활성화](deploy-zip.md#enable-build-automation)된 ZIP 배포와 같이 App Service 배포 엔진에 배포하는 경우 App Service 배포 엔진은 App Service 앱으로 찾은 첫 번째 웹 사이트 또는 웹 애플리케이션 프로젝트를 선택합니다. `PROJECT` 앱 설정을 지정하여 App Service가 사용해야 하는 프로젝트를 지정할 수 있습니다. 예를 들어 [Cloud Shell](https://shell.azure.com)에서 다음을 실행합니다.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="get-detailed-exceptions-page"></a>상세 예외 페이지 가져오기

ASP.NET 앱이 Visual Studio 디버거에서 예외를 생성하는 경우 브라우저에는 상세 예외 페이지가 표시되지만 App Service에서 이 페이지는 일반 오류 메시지로 대체됩니다. App Service에서 상세 예외 페이지를 표시하려면 *Web.config* 파일을 열고 `<system.web>` 요소 아래에 `<customErrors mode="Off"/>` 요소를 추가합니다. 예를 들면 다음과 같습니다.

```xml
<system.web>
    <customErrors mode="Off"/>
</system.web>
```

업데이트된 *Web.config* 를 사용하여 앱을 다시 배포합니다. 이제 동일한 상세 예외 페이지가 표시되어야 합니다.

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

[System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace)를 사용하여 애플리케이션 코드에서 진단 메시지를 추가할 수 있습니다. 예를 들면 다음과 같습니다. 

```csharp
Trace.TraceError("Record not found!"); // Error trace
Trace.TraceWarning("Possible data loss"); // Warning trace
Trace.TraceInformation("GET /Home/Index"); // Information trace
```

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="more-resources"></a>추가 리소스

- [자습서: SQL Database를 사용하여 Azure에서 ASP.NET 앱 빌드](app-service-web-tutorial-dotnet-sqldatabase.md)
- [환경 변수 및 앱 설정 참조](reference-app-settings.md)