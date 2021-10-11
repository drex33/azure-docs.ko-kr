---
title: Azure Functions 런타임 버전 개요
description: Azure Functions는 여러 버전의 런타임을 지원합니다. 버전 간 차이점과 적합한 버전을 선택하는 방법을 알아봅니다.
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 09/22/2021
ms.openlocfilehash: 516bcbdd00ae4b116326e797746485c82be9c3fb
ms.sourcegitcommit: ee5d9cdaf691f578f2e390101bf5350859d85c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129740515"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions 런타임 버전 개요

Azure Functions 현재 여러 버전의 런타임 호스트를 지원합니다. 다음 표에서는 사용 가능한 버전, 지원 수준 및 사용 시기에 대해 자세히 설명합니다.

| 버전 | 지원 수준 | Description |
| --- | --- | --- |
| 4.x | 미리 보기 | 모든 언어를 지원합니다. 이 버전을 사용하여 [.NET 6.0에서 C# 함수를 실행합니다.](functions-dotnet-class-library.md#supported-versions) |
| 3.x | GA | _모든 언어의 함수에 권장되는 런타임 버전입니다._ |
| 2.x | GA | 레거시 [버전 2.x 앱에](#pinning-to-version-20)대해 지원됨. 이 버전은 유지 관리 모드이며, 향상된 기능이 이후 버전에서만 제공됩니다.|
| 1.x | GA | .NET Framework 사용해야 하며 Azure Portal, Azure Stack Hub 포털 또는 Windows 컴퓨터에서 로컬로만 개발을 지원하는 C# 앱에만 권장됩니다. 이 버전은 유지 관리 모드이며, 향상된 기능이 이후 버전에서만 제공됩니다. |

이 문서에서는 이러한 버전 간의 차이점, 각 버전을 만드는 방법 및 함수가 실행되는 버전을 변경하는 방법에 대해 자세히 설명합니다.

[!INCLUDE [functions-support-levels](../../includes/functions-support-levels.md)]

## <a name="languages"></a>언어

버전 2.x부터 런타임은 언어 확장성 모델을 사용하며 함수 앱의 모든 함수는 동일한 언어를 공유해야 합니다. 앱을 만들 때 함수 앱에서 함수 언어를 선택했습니다. 함수 앱의 언어는 [FUNCTIONS WORKER \_ \_ RUNTIME](functions-app-settings.md#functions_worker_runtime) 설정에서 유지 관리되며 기존 함수가 있을 때는 변경하지 않아야 합니다. 

다음 표는 각 런타임 버전에서 현재 지원되는 프로그래밍 언어를 나타냅니다.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>특정 버전에서 실행

기본적으로 Azure Portal에서 및 Azure CLI을 통해 만든 함수 앱은 버전 3.x로 설정됩니다. 필요에 따라 이 버전을 수정할 수 있습니다. 함수 앱을 만든 후, 함수를 추가하기 전에만 런타임 버전을 1.x로 다운그레이드할 수 있습니다.  기존 기능이 있는 앱에서도 2.x와 3.x 간에 전환할 수 있습니다. 기존 기능이 있는 앱을 2.x에서 3.x로 전환하기 전에 [2.x와 3.x 간의 변경 사항](#breaking-changes-between-2x-and-3x)을 알고 있어야 합니다. 

런타임의 주 버전을 변경하기 전에 먼저 최신 주 버전에서 실행되는 다른 함수 앱에 배포하여 기존 코드를 테스트해야 합니다. 이 테스트는 업그레이드 후 올바르게 실행되는지 확인하는 데 도움이 됩니다. 

v3.x에서 v2.x로의 다운그레이드는 지원되지 않습니다. 가능하면 항상 지원되는 최신 버전의 Functions 런타임에서 앱을 실행해야 합니다. 

### <a name="changing-version-of-apps-in-azure"></a>Azure에서 앱 버전 변경

Azure에 게시된 앱에서 사용하는 Functions 런타임 버전은 [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) 애플리케이션 설정에 따라 결정됩니다. 다음과 같은 주요 런타임 버전 값이 지원됩니다.

| 값 | 런타임 대상 |
| ------ | -------- |
| `~4` | 4.x(미리 보기) |
| `~3` | 3.x |
| `~2` | 2.x |
| `~1` | 1.x |

>[!IMPORTANT]
> 다른 앱 설정 변경 및 함수 코드 변경이 필요할 수 있으므로 이 설정을 임의로 변경하지 않도록 합니다.

자세한 내용은 [Azure Functions 런타임 버전을 대상으로 지정하는 방법](set-runtime-version.md)을 참조하세요.  

### <a name="pinning-to-a-specific-minor-version"></a>특정 부 버전에 고정

최신 주 버전에서 실행되는 함수 앱의 문제를 해결하려면 앱을 특정 부 버전에 고정해야 합니다. 앱을 고정하면 최신 주 버전에서 앱을 올바르게 실행할 수 있는 시간이 생깁니다. 부 버전에 고정하는 방법은 Windows와 Linux 간에 다릅니다. 자세한 내용은 [Azure Functions 런타임 버전을 대상으로 지정하는 방법](set-runtime-version.md)을 참조하세요.

이전 부 버전은 Functions에서 정기적으로 제거됩니다. 특정한 이전 부 버전의 삭제를 비롯하여 Azure Functions 릴리스에 대한 최신 소식을 보려면 [Azure App Service 공지](https://github.com/Azure/app-service-announcements/issues)를 주기적으로 확인하세요. 

### <a name="pinning-to-version-20"></a>2\.0 이하 버전에 고정

버전 2.x(`~2`)에서 실행되는 .NET 함수 앱은 .NET Core 3의 장기 지원 버전인 .NET Core 3.1에서 실행되도록 자동으로 업그레이드됩니다. .NET Core 3.1에서 .NET 함수를 실행하면 최신 보안 업데이트 및 제품 개선 사항을 활용할 수 있습니다. 

`~2.0`에 고정된 함수 앱은 더 이상 보안 및 기타 업데이트를 수신하지 않는 .NET Core 2.2에서 계속 실행됩니다. 자세히 알아보려면 [Functions v2.x 고려 사항](functions-dotnet-class-library.md#functions-v2x-considerations)을 참조하세요.   

## <a name="migrating-from-3x-to-4x-preview"></a><a name="migrating-from-3x-to-4x"></a>3.x에서 4.x로 마이그레이션(미리 보기)

Azure Functions 버전 4.x(미리 보기)는 버전 3.x와 매우 이전 버전과 호환됩니다.  대부분의 앱은 코드를 변경하지 않고도 4.x로 안전하게 업그레이드할 수 있어야 합니다. 프로덕션 앱에서 주 버전을 변경하기 전에 광범위한 테스트를 실행해야 합니다.

3.x에서 4.x로 앱을 마이그레이션하려면 `FUNCTIONS_EXTENSION_VERSION` 다음 Azure CLI 명령을 사용하여 애플리케이션 설정을 로 설정합니다. `~4`

```bash
az functionapp config appsettings set --settings FUNCTIONS_EXTENSION_VERSION=~4 -n <APP_NAME> -g <RESOURCE_GROUP_NAME>

# For Windows function apps only, also enable .NET 6.0 that is needed by the runtime
az functionapp config set --net-framework-version v6.0 -n <APP_NAME> -g <RESOURCE_GROUP_NAME>
```

### <a name="breaking-changes-between-3x-and-4x"></a>3.x와 4.x 간의 주요 변경 내용

다음은 3.x 앱을 4.x로 업그레이드하기 전에 알고 있어야 하는 몇 가지 변경 내용입니다. 전체 목록은 Azure Functions GitHub 주요 [*변경: 승인된*](https://github.com/Azure/azure-functions/issues?q=is%3Aissue+label%3A%22Breaking+Change%3A+Approved%22+is%3A%22closed+OR+open%22)레이블이 있는 문제를 참조하세요. 미리 보기 기간 동안 더 많은 변경이 예상됩니다. 업데이트에 대한 [App Service 알림](https://github.com/Azure/app-service-announcements/issues) 구독

#### <a name="runtime"></a>런타임

- Azure Functions 프록시 4.x에서 더 이상 지원되지 않습니다. [Azure API Management](../api-management/import-function-app-as-api.md)사용하는 것이 좋습니다.

- *AzureWebJobsDashboard를* 사용하여 Azure Storage 로깅은 4.x에서 더 이상 지원되지 않습니다. [애플리케이션 Insights](./functions-monitoring.md)사용하는 것이 좋습니다.

- Azure Functions 4.x는 확장에 대한 [최소 버전 요구 사항을](https://github.com/Azure/Azure-Functions/issues/1987) 적용합니다. 영향을 받는 최신 버전의 확장으로 업그레이드합니다. non-.NET 언어의 경우 확장 번들 버전 2.x 이상으로 [업그레이드합니다.](./functions-bindings-register.md#extension-bundles)

- 기본 및 최대 시간 제한은 이제 4.x Linux 소비 함수 앱에서 적용됩니다.

- 애플리케이션 Insights 더 이상 기본적으로 4.x에 포함되지 않습니다. 이제 별도의 확장으로 사용할 수 있습니다.
    - In Process .NET 앱의 경우 [Microsoft.Azure.WebJobs.Extensions.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ApplicationInsights/) 확장 패키지를 함수 앱에 추가합니다.
    - 격리된 .NET 앱의 경우:
        - [Microsoft.Azure.Functions.Worker.Extensions.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Extensions.ApplicationInsights/) 확장 패키지를 함수 앱에 추가합니다.
        - [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/) 및 [Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/) 패키지를 최신 버전으로 업데이트합니다.
    - 다른 언어의 경우 Azure Functions [확장 번들에](functions-bindings-register.md#extension-bundles) 대한 향후 업데이트에는 애플리케이션 Insights 확장이 포함됩니다. 앱이 사용 가능한 경우 새 번들을 자동으로 사용합니다.

#### <a name="languages"></a>언어

# <a name="c"></a>[C\#](#tab/csharp)

현재 보고되지 않았습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

현재 보고되지 않았습니다.

# <a name="python"></a>[Python](#tab/python)

- 공유 메모리 전송은 기본적으로 Azure Functions 4.x에서 사용하도록 설정됩니다.

---

## <a name="migrating-from-2x-to-3x"></a>2\.x에서 3.x로 마이그레이션

Azure Functions 버전 3.x는 이전 버전 2.x와 호환됩니다.  대부분의 앱은 코드를 변경하지 않고도 3.x로 안전하게 업그레이드할 수 있습니다. 3. x로 이동 하는 것이 좋지만 프로덕션 앱에서 주 버전을 변경 하기 전에 광범위 한 테스트를 실행 합니다.

### <a name="breaking-changes-between-2x-and-3x"></a>2\.x와 3.x의 주요 변경 사항

다음은 2.x 앱을 3. x로 업그레이드 하기 전에 알아두어야 할 언어별 변경 내용입니다.

# <a name="c"></a>[C\#](#tab/csharp)

.NET 클래스 라이브러리 함수를 실행할 때 버전 간의 주요 차이점은 .NET Core 런타임입니다. Functions 버전 2.x는 .NET Core 2.2에서 실행되도록 설계되었으며 버전 3.x는 .NET Core 3.1에서 실행되도록 설계되었습니다.  

* [동기 서버 작업은 기본적으로 사용하지 않도록 설정됩니다](/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers).

* [버전 3.1](/dotnet/core/compatibility/3.1) 및 [버전 3.0](/dotnet/core/compatibility/3.0)에서 .NET Core를 통해 도입된 주요 변경 사항은 Functions에만 국한되지는 않으며 계속해서 앱에 영향을 미칠 수 있습니다.

>[!NOTE]
>.NET Core 2.2의 지원 문제로 인해 버전 2(`~2`)에 고정된 함수 앱은 기본적으로 .NET Core 3.1에서 실행됩니다. 자세히 알아보려면 [Functions v2.x 호환성 모드](functions-dotnet-class-library.md#functions-v2x-considerations)를 참조하세요.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* `context.done` 또는 반환 값을 통해 할당된 출력 바인딩은 이제 `context.bindings`의 설정과 동일하게 작동합니다.

* 타이머 트리거 개체는 PascalCase 대신 camelCase입니다.

* `dataType` 이진이 있는 Event Hub 트리거 함수는 `string` 대신 `binary` 배열을 수신합니다.

* HTTP 요청 페이로드에는 `context.bindingData.req`를 통해 더 이상 액세스할 수 없습니다.  `context.req` 및 `context.bindings`에서는 입력 매개변수로 계속 액세스할 수 있습니다.

* Node.js 8은 더 이상 지원되지 않으며 3.x 함수에서 실행되지 않습니다.

# <a name="python"></a>[Python](#tab/python)

없음

---

## <a name="migrating-from-1x-to-later-versions"></a>1\.x에서 이후 버전으로 마이그레이션

버전 1.x 런타임을 사용하도록 작성된 기존 앱을 마이그레이션하여 최신 버전을 대신 사용하도록 선택할 수 있습니다. 수행해야 하는 대부분의 변경 사항은 .NET Framework 4.8과 .NET Core 간의 C# API 변경과 같은 언어 런타임의 변경과 관련되어 있습니다. 또한 선택한 언어 런타임과 코드 및 라이브러리가 호환되는지 확인해야 합니다. 마지막으로 아래에 강조 표시된 트리거, 바인딩 및 기능의 변경 내용을 기록해 두어야 합니다. 최상의 마이그레이션 결과를 얻으려면 새 버전에서 새 함수 앱을 만들고 기존 버전 1.x 함수 코드를 새 앱으로 이식해야 합니다.  

앱 구성을 수동으로 업데이트하여 "현재 위치" 업그레이드를 수행할 수 있지만 1.x에서 상위 버전으로 전환하면 몇 가지 주요 변경 사항이 포함됩니다. 예를 들어 C#에서 디버깅 개체는 `TraceWriter`에서 `ILogger`로 변경됩니다. 새 버전 3.x 프로젝트를 만들어 최신 버전 3.x 템플릿을 기준으로 하는 업데이트된 함수로 시작합니다.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>버전 1.x 이후 트리거 및 바인딩 변경 사항

버전 2.x부터 앱의 함수에 사용되는 특정 트리거 및 바인딩에 대한 확장을 설치해야 합니다. 유일한 예외는 확장이 필요 없는 이 HTTP 및 타이머 트리거입니다.  자세한 내용은 [바인딩 확장 등록 및 설치](./functions-bindings-register.md)를 참조하세요.

버전 간에 *function.json* 또는 함수의 특성에도 몇 가지 변경 사항이 있습니다. 예를 들어, 이벤트 허브 `path` 속성은 이제 `eventHubName`입니다. 각 바인딩의 설명서에 대한 링크는 [기존 바인딩 테이블](#bindings)을 참조하세요.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>버전 1.x 이후의 기능 변경 사항

버전 1.x 이후에 몇 가지 기능이 제거, 업데이트 또는 교체되었습니다. 이 섹션에서는 버전 1.x를 사용한 후 이후 버전에서 볼 수 있는 변경 사항에 대해 자세히 설명합니다.

버전 2.x에서 다음과 같은 사항이 변경되었습니다.

* HTTP 엔드포인트를 호출하기 위한 키는 항상 Azure Blob Storage에 암호화된 상태로 저장됩니다. 버전 1.x에서는 키가 기본적으로 Azure Files에 저장되었습니다. 앱을 버전 1.x에서 버전 2.x로 업그레이드할 때 Azure Files에 있는 기존 암호가 다시 설정됩니다.

* 버전 2.x 런타임에서는 웹후크 공급자가 기본적으로 지원되지 않습니다. 성능 향상을 위해 이렇게 변경되었습니다. HTTP 트리거를 여전히 웹후크에 대한 엔드포인트로 사용할 수 있습니다.

* 호스트 구성 파일(host.json)은 비워 두거나 문자열 `"version": "2.0"`을 포함해야 합니다.

* 모니터링을 향상시키기 위해 [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) 설정을 사용하는 포털의 WebJobs 대시보드가 [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) 설정을 사용하는 Azure Application Insights로 바뀌었습니다. 자세한 내용은 [Azure Functions 모니터링](functions-monitoring.md)을 참조하세요.

* 함수 앱의 모든 함수가 동일한 언어를 공유해야 합니다. 함수 앱을 만들 때 앱의 런타임 스택을 선택해야 합니다. 런타임 스택은 애플리케이션 설정의 [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) 값으로 지정됩니다. 이 요구 사항은 공간 효율성 및 시작 시간을 개선하기 위해 추가되었습니다. 로컬로 개발하는 경우 [local.settings.json 파일](functions-develop-local.md#local-settings-file)에 이 설정을 포함해야 합니다.

* App Service 계획의 함수에 대한 기본 시간 제한은 30분으로 변경되었습니다. host.json에서 [functionTimeout](functions-host-json.md#functiontimeout) 설정을 사용하여 수동으로 제한 시간을 다시 무제한으로 변경할 수 있습니다.

* HTTP 동시성 제한은 기본값으로 인스턴스당 동시 요청 수 100개를 갖는 사용 플랜 함수에 대해 기본적으로 구현됩니다. 이 설정은 host.json 파일의 [`maxConcurrentRequests`](functions-host-json.md#http) 설정에서 변경할 수 있습니다.

* [.NET Core 제한](https://github.com/Azure/azure-functions-host/issues/3414)으로 인해 F# 스크립트(.fsx) 함수에 대한 지원이 제거되었습니다. 컴파일된 F# 함수(.fs)는 계속 지원됩니다.

* Event Grid 트리거 웹후크의 URL 형식은 `https://{app}/runtime/webhooks/{triggerName}`으로 변경되었습니다.

### <a name="locally-developed-application-versions"></a>로컬에서 개발된 애플리케이션 버전

함수 앱을 다음과 같이 업데이트하여 대상 버전을 로컬로 변경할 수 있습니다.

#### <a name="visual-studio-runtime-versions"></a>Visual Studio 런타임 버전

Visual Studio에서 프로젝트를 만들 때 런타임 버전을 선택합니다. Visual Studio용 Azure Functions 도구는 세 가지 주요 런타임 버전을 지원합니다. 디버깅 및 게시를 수행할 때 프로젝트 설정에 따라 올바른 버전이 사용됩니다. 버전 설정은 `.csproj` 파일의 다음 속성에 정의됩니다.

# <a name="version-4x-preview"></a>[버전 4.x (미리 보기)](#tab/v4)

```xml
<TargetFramework>net6.0</TargetFramework>
<AzureFunctionsVersion>v4</AzureFunctionsVersion>
```

> [!NOTE]
> Azure Functions 4.x를 사용 하려면 `Microsoft.NET.Sdk.Functions` 확장이 이상 이어야 합니다 `4.0.0` .

# <a name="version-3x"></a>[버전 3.x](#tab/v3)

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Azure Functions 3.x 및 .NET에서 사용하려면 `Microsoft.NET.Sdk.Functions` 확장이 `3.0.0` 이상이어야 합니다.

# <a name="version-2x"></a>[버전 2.x](#tab/v2)

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

# <a name="version-1x"></a>[버전 1.x](#tab/v1)

```xml
<TargetFramework>net472</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```
---

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>Visual Studio에서 2.x 앱을 3.x로 업데이트

`.csproj` 파일을 편집하고 위의 값을 업데이트하여 2.x를 대상으로 하는 기존 함수를 열고 3.x로 전환할 수 있습니다.  Visual Studio는 프로젝트 메타데이터를 기반으로 런타임 버전을 자동으로 관리합니다.  그러나 이전에 3.x 앱을 만든 적이 없고 컴퓨터의 Visual Studio에 3.x용 템플릿과 런타임이 아직 없는 경우에 가능합니다.  이 경우 "프로젝트에 지정된 버전과 일치하는 사용 가능한 Functions 런타임이 없습니다."와 같은 오류가 표시될 수 있습니다.  최신 템플릿 및 런타임을 가져오려면 환경을 통해 새 함수 프로젝트를 생성합니다.  버전 및 템플릿 선택 화면이 표시되면 Visual Studio에 최신 템플릿 가져오기가 완료될 때까지 기다립니다. 최신 .NET Core 3 템플릿을 사용 가능한 상태이고 템플릿이 표시되면 버전 3.x용으로 구성된 프로젝트를 실행하고 디버그할 수 있습니다.

> [!IMPORTANT]
> 버전 3.x 함수는 Visual Studio 버전 16.4 이상을 사용하는 경우에만 Visual Studio에서 개발할 수 있습니다.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code 및 Azure Functions 핵심 도구

[Azure Functions Core Tools](functions-run-local.md)는 명령줄 개발 및 Visual Studio Code용 [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)에서도 사용됩니다. 버전 3.x에 대해 개발하려면 버전 3.x의 Core Tools를 설치합니다. 버전 2.x 개발에는 버전 2.x의 Core Tools가 필요합니다. 자세한 내용은 [Azure Functions Core Tools 설치](functions-run-local.md#install-the-azure-functions-core-tools)를 참조하세요.

Visual Studio Code 개발의 경우 설치된 도구의 버전과 일치하도록 `azureFunctions.projectRuntime`에 대한 사용자 설정을 업데이트해야 할 수도 있습니다.  이 설정은 함수 앱을 만드는 동안 사용되는 템플릿 및 언어도 업데이트합니다.  `~3`에서 앱을 만들려면 `azureFunctions.projectRuntime` 사용자 설정을 `~3`으로 업데이트합니다.

![Azure Functions 확장 런타임 설정](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Maven 및 Java 앱

로컬에서 실행하는 데 필요한 [핵심 도구의 3.x 버전을 설치](functions-run-local.md#install-the-azure-functions-core-tools)하여 Java 앱을 버전 2.x에서 3.x로 마이그레이션할 수 있습니다.  앱이 버전 3.x에서 로컬로 올바르게 실행되는지 확인한 후 다음 예제와 같이 `FUNCTIONS_EXTENSION_VERSION` 설정을 `~3`로 수정하도록 앱의 `POM.xml` 파일을 업데이트합니다.

```xml
<configuration>
    <resourceGroup>${functionResourceGroup}</resourceGroup>
    <appName>${functionAppName}</appName>
    <region>${functionAppRegion}</region>
    <appSettings>
        <property>
            <name>WEBSITE_RUN_FROM_PACKAGE</name>
            <value>1</value>
        </property>
        <property>
            <name>FUNCTIONS_EXTENSION_VERSION</name>
            <value>~3</value>
        </property>
    </appSettings>
</configuration>
```

## <a name="bindings"></a>바인딩

버전 2.x부터 런타임은 다음과 같은 이점을 제공하는 새 [바인딩 확장성 모델](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)을 사용합니다.

* 타사 바인딩 확장 지원.

* 런타임과 바인딩을 분리. 이렇게 변경하면 바인딩 확장의 버전을 지정하고 독립적으로 릴리스할 수 있습니다. 예를 들어 기본 SDK의 최신 버전을 사용하는 확장 버전으로 업그레이드하도록 선택할 수 있습니다.

* 사용 중인 바인딩만 런타임에 알려지고 로드되는 가벼운 실행 환경.

HTTP 및 타이머 트리거를 제외하고 모든 바인딩은 명시적으로 함수 앱 프로젝트에 추가하거나 포털에 등록해야 합니다. 자세한 내용은 [바인딩 확장 등록](./functions-bindings-expressions-patterns.md)을 참조하세요.

다음 표에는 각 런타임 버전에서 지원되는 바인딩이 나와 있습니다.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 자료를 참조하세요.

* [Azure Functions를 로컬에서 코딩 및 테스트](functions-run-local.md)
* [Azure Functions 런타임 버전을 대상으로 지정하는 방법](set-runtime-version.md)
* [릴리스 정보](https://github.com/Azure/azure-functions-host/releases)
