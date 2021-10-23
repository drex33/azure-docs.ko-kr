---
title: '빠른 시작: Azure Monitor Application Insights를 사용하여 ASP.NET Core 앱 모니터링'
description: Azure Monitor Application Insights를 사용하여 모니터링할 ASP.NET Core 웹앱을 계측합니다.
ms.topic: quickstart
ms.date: 06/11/2021
ms.custom: devx-track-dotnet
ms.openlocfilehash: 344f34116a1c6b40ac452fea4c9f27d8c89ba3ad
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134054"
---
# <a name="quickstart-monitor-an-aspnet-core-app-with-azure-monitor-application-insights"></a>빠른 시작: Azure Monitor Application Insights를 사용하여 ASP.NET Core 앱 모니터링

이 빠른 시작에서는 Application Insights SDK를 사용해 ASP.NET Core 앱을 계측하여 클라이언트 쪽 및 서버 쪽 원격 분석을 수집합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/dotnet).
- .NET 5.0 SDK 이상 플랫폼용 [최신 .NET 5.0 SDK를 설치](https://dotnet.microsoft.com/download/dotnet/5.0)합니다.

## <a name="create-an-application-insights-resource"></a>Application Insights 리소스 만들기

수집 원격 분석을 시작하려면 Azure 구독에 Application Insights 리소스를 만듭니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **리소스 만들기** > **개발자 도구** > **Application Insights** 를 선택합니다.

1. 표시되는 양식을 작성합니다.
    1. 선택한 **구독** 을 확인합니다.
    1. 기존 또는 새 **리소스 그룹** 을 선택합니다.
    1. 이 Application Insights 리소스의 **이름** 을 지정합니다.
    1. 사용자 위치에서 가까운 **지역** 을 선택합니다.
    1. **리소스 모드** 를 *클래식* 으로 설정합니다. 

1. **검토 + 만들기** 단추를 선택합니다.
1. **생성** 단추를 선택합니다.
1. 배포가 완료되면 **리소스로 이동** 단추를 선택합니다.
1. 표시되는 **개요** 에서 **계측 키**(**Essentials** 아래에 있음)를 복사합니다.

## <a name="create-and-configure-an-aspnet-core-web-app"></a>ASP.NET Core 웹앱 만들기 및 구성

새 ASP.NET Core 웹앱을 만들고 구성하려면 다음 단계를 완료합니다.

1. 새 ASP.NET Core Razor Pages 앱을 만듭니다.
    
    ```dotnetcli
    dotnet new razor -o ai.quickstart
    ```
    
    이전 명령은 *ai.quickstart* 라는 디렉터리에 새 ASP.NET Core Razor Pages 앱을 만듭니다. 
    
    > [!TIP]
    > Visual Studio를 사용하여 [앱을 만드](/visualstudio/ide/quickstart-aspnet-core)는 것이 좋습니다.

1. 프로젝트 디렉터리 내에서 `Microsoft.ApplicationInsights.AspNetCore` 패키지를 프로젝트에 추가합니다. Visual Studio를 사용하는 경우 [NuGet 패키지 관리자](/nuget/consume-packages/install-use-packages-visual-studio)를 사용할 수 있습니다.

    ```dotnetcli
    dotnet add package Microsoft.ApplicationInsights.AspNetCore --version 2.17.0 
    ```

1. 텍스트 편집기 또는 IDE를 사용하여 표시된 것처럼 `ApplicationInsights.InstrumentationKey`에 대한 값을 포함하도록 *appsettings.json* 을 수정합니다. 이전에 복사한 계측 키를 사용합니다.

    :::code language="json" source="~/dotnet-samples/azure/app-insights-aspnet-core-quickstart/appsettings.json" range="1-12" highlight="2-4":::
    
    > [!IMPORTANT]
    > Application Insights SDK에는 `ApplicationInsights.InstrumentationKey` 구성 값이 필요합니다. 이름을 올바르게 지정하세요!

## <a name="configure-server-side-telemetry"></a>서버 쪽 원격 분석 구성

*Startup.cs* 의 `ConfigureServices` 메서드에서 Application Insights 서비스를 파이프라인에 추가합니다. 강조 표시된 줄을 추가합니다.

:::code language="csharp" source="~/dotnet-samples/azure/app-insights-aspnet-core-quickstart/startup.cs" range="24-28" highlight="4":::

## <a name="configure-client-side-telemetry"></a>클라이언트 쪽 원격 분석 구성

클라이언트 쪽 원격 분석을 보내도록 앱을 계측하려면 다음 단계를 완료합니다.

1. *Pages/_ViewImports.cshtml* 에서 다음 줄을 추가합니다.

    ```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

    이전 변경 내용은 Application Insights 클라이언트 쪽 스크립트 요소를 포함하는 `Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet` 종속성을 등록합니다.

1. *Pages/Shared/_Layout.cshtml* 의 `<head>` 요소에 강조 표시된 줄을 추가합니다.

    :::code language="cshtml" source="~/dotnet-samples/azure/app-insights-aspnet-core-quickstart/pages/shared/_layout.cshtml" range="3-10" highlight="7":::

   이 변경 내용은 삽입된 `JavaScriptSnippet` 개체를 사용하여 앱에 있는 모든 페이지의 `<head>` 요소에서 `<script>` 요소가 렌더링되도록 합니다.

## <a name="validate-telemetry-ingestion"></a>원격 분석 수집 유효성 검사

분석을 위해 원격 분석을 Application Insights로 수집하는 데 몇 분 정도 걸립니다. 앱이 실시간으로 원격 분석을 보내고 있는지 확인하려면 **라이브 메트릭** 을 사용합니다.

1. `dotnet run` 또는 IDE를 사용하여 웹앱을 실행합니다.
1. Azure Portal에서 Application Insights 리소스를 볼 때 **조사** 에서 **라이브 메트릭** 을 선택합니다.
1. 앱(`http://localhost:5000`)에서 **홈** 및 **개인 정보** 링크를 반복해서 선택합니다.
1. 앱에서 요청이 발생하는 경우 **라이브 메트릭** 디스플레이에 대한 활동을 관찰합니다.

## <a name="next-steps"></a>다음 단계

축하합니다! 이제 앱에서 보낸 원격 분석을 사용하여 다음을 수행할 수 있습니다.

- [런타임 예외를 찾습니다](tutorial-runtime-exceptions.md).
- [성능 문제를 찾습니다](tutorial-performance.md).
- [앱 상태에 대한 경고](tutorial-alert.md).

> [!div class="nextstepaction"]
> [ASP.NET Core의 Application Insights에 대해 자세히 알아보기](asp-net-core.md)
