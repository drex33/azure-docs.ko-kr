---
title: Application Insights를 사용하여 라이브 Azure App Service 앱 프로파일링 | Microsoft Docs
description: Application Insights Profiler를 사용하여 Azure App Service에 라이브 앱을 프로파일링합니다.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.openlocfilehash: e80ce3a4e96720bc3149dd9d8083a1f8f0943dbe
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124815758"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Application Insights를 사용하여 라이브 Azure App Service 앱 프로파일링

Basic 서비스 계층 이상을 사용하여 Azure App Service에서 실행되는 ASP.NET 및 ASP.NET Core 앱을 실행할 수 있습니다. Linux에서 Profiler를 사용하도록 설정하려면 반드시 [이 방법](profiler-aspnetcore-linux.md)을 사용해야 합니다.

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> 앱에 대해 Profiler 사용
앱에 대해 Profiler를 사용하도록 설정하려면 아래 지침을 따르세요. 다른 유형의 Azure 서비스를 실행하는 경우 지원되는 다른 플랫폼에서 Profiler를 사용하도록 설정하는 지침은 다음과 같습니다.
* [Cloud Services](./profiler-cloudservice.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Service Fabric 애플리케이션](./profiler-servicefabric.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Virtual Machines](./profiler-vm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

Application Insights Profiler는 App Services 런타임의 일부로 미리 설치됩니다. 아래 단계에서는 App Service에 이 기능을 사용하도록 설정하는 방법을 보여 줍니다. 빌드 시 애플리케이션에 App Insights SDK를 포함한 경우에도 다음 단계를 수행합니다.

> [!NOTE]
> Application Insights Profiler의 코드리스 설치는 .NET Core 지원 정책을 따릅니다.
> 지원되는 런타임에 대한 자세한 내용은 [.NET Core 지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조하세요.

1. App Service에 대한 Azure 제어판으로 이동합니다.
1. 앱 서비스에 "Always On" 설정을 사용합니다. 이 설정은 **설정**, **구성** 페이지(다음 단계의 스크린샷 참조)에서 찾을 수 있으며, **일반 설정** 탭을 선택합니다.
1. **설정 > Application Insights** 페이지로 이동합니다.

   ![App Services 포털에서 App Insights를 사용하도록 설정](./media/profiler/AppInsights-AppServices.png)

1. 창의 지침에 따라 새 리소스를 만들거나 기존 App Insights 리소스를 선택하여 앱을 모니터링합니다. 또한 Profiler가 **켜기** 상태인지 확인합니다. Application Insights 리소스가 App Service와 다른 구독에 있는 경우 이 페이지를 사용하여 Application Insights를 구성할 수 없습니다. 필요한 앱 설정을 수동으로 만드는 방식을 사용할 수도 있습니다. [다음 섹션에는 수동으로 프로파일러를 사용하도록 설정하는 방법에 대한 지침이 포함되어 있습니다.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![App Insights 사이트 확장 추가][Enablement UI]

1. 이제 Profiler가 App Services 앱 설정을 사용하여 활성화됩니다.

    ![Profiler에 대한 앱 설정][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>수동으로 또는 Azure Resource Manager를 사용하여 Profiler를 사용하도록 설정
Azure App Service에 대한 앱 설정을 만들어 Application Insights Profiler를 사용하도록 설정할 수 있습니다. 위에 표시된 옵션이 적용된 페이지는 이러한 앱 설정을 자동으로 만듭니다. 그러나 템플릿이나 다른 방법을 사용하여 이러한 설정이 자동으로 생성되도록 할 수 있습니다. 이러한 설정은 Application Insights 리소스가 Azure App Service와 다른 구독에 있는 경우에도 작동합니다.
프로파일러를 사용하도록 설정하는 데 필요한 설정은 다음과 같습니다.

|앱 설정    | 값    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | Application Insights 리소스용 iKey    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


[Azure Resource Manager 템플릿](./azure-web-apps-net-core.md#app-service-application-settings-with-azure-resource-manager), [Azure PowerShell](/powershell/module/az.websites/set-azwebapp), [Azure CLI](/cli/azure/webapp/config/appsettings)를 사용하여 해당 값을 설정할 수 있습니다.

## <a name="enable-profiler-for-other-clouds"></a>다른 클라우드에 대해 Profiler 사용

현재 엔드포인트 수정이 필요한 유일한 지역은 [Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights)와 [Azure 중국](/azure/china/resources-developer-guide)입니다.

|앱 설정    | 미국 정부 클라우드 | 중국 클라우드 |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

## <a name="disable-profiler"></a>Profiler 사용 안 함

개별 앱 인스턴스에서 Profiler를 중지하거나 다시 시작하려면 왼쪽 사이드바에서 **WebJobs** 를 선택하고 `ApplicationInsightsProfiler3`라는 webjob을 중지합니다.

  ![웹 작업에 대한 Profiler 사용 안 함][disable-profiler-webjob]

성능 문제를 가능한 한 빨리 검색하려면 모든 앱에서 Profiler를 사용하도록 설정하는 것이 좋습니다.

WebDeploy를 사용하여 웹 애플리케이션에 변경 내용을 배포하는 경우 Profiler의 파일을 삭제할 수 있습니다. 배포하는 동안 삭제되지 않도록 App_Data 폴더를 제외하여 삭제를 방지할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

* [Visual Studio에서 Application Insights로 작업](./visual-studio.md)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png