---
title: Azure App Services 성능 모니터링 | Microsoft Docs
description: Azure App Services에 대한 애플리케이션 성능 모니터링입니다. 차트 로드 및 응답 시간, 종속성 정보, 성능에 관해 설정된 경고입니다.
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js, devx-track-dotnet, devx-track-azurepowershell
ms.openlocfilehash: 9fac3e79b62a1f354b4120afc965e331b0185e98
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122539102"
---
# <a name="application-monitoring-for-azure-app-service"></a>Azure App Service에 대한 애플리케이션 모니터링

이제 [Azure App Services](../../app-service/index.yml)에서 실행되는 ASP.NET, ASP.NET Core, Java, Node.js 기반 웹 애플리케이션에 대한 모니터링을 사용하도록 설정하는 것이 훨씬 쉬워졌습니다. 이전에는 수동으로 앱을 계측해야 했지만 이제 최신 확장/에이전트가 기본적으로 App Service 이미지에 기본 제공됩니다. 이 문서에서는 Azure Monitor Application Insights 모니터링을 사용하도록 설정하는 과정을 안내하고 대규모 배포 프로세스를 자동화하기 위한 예비 지침을 제공합니다.

> [!NOTE]
> Windows의 .NET에만 해당: **개발 도구** > **확장** 을 통해 Application Insights 사이트 확장을 수동으로 추가하는 기능은 더 이상 사용되지 않습니다. 이 확장 설치 방법은 각 새 버전의 수동 업데이트에 따라 달랐습니다. 확장의 안정적인 최신 릴리스는 이제 App Service 이미지의 일부로  [미리 설치](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)됩니다. 파일은 `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent`에 있으며 각 안정적인 릴리스로 자동 업데이트됩니다. 에이전트 기반 지침에 따라 아래 모니터링을 사용하는 경우 사용되지 않는 확장이 자동으로 제거됩니다.

## <a name="enable-application-insights"></a>Application Insights 사용

Azure App Services 호스트된 애플리케이션에 대해 애플리케이션 모니터링을 사용하도록 설정하는 방법에는 두 가지가 있습니다.

* **에이전트 기반 애플리케이션 모니터링**(ApplicationInsightsAgent).  
    * 이 방법으로 가장 쉽게 사용하도록 설정할 수 있고 코드 변경이나 고급 구성이 필요하지 않습니다. 이 방법을 “런타임” 모니터링이라고도 합니다. Azure App Services의 경우 이 모니터링 수준을 최소한으로 사용하는 것이 좋으며, 특정 시나리오에 따라 수동 계측을 통한 고급 모니터링이 필요한지 여부를 평가할 수 있습니다.

* Application Insights SDK를 설치하여 **코드를 통해 애플리케이션을 수동으로 계측** 합니다.

    * 이 방식은 훨씬 더 사용자 지정이 가능하지만 다음 방식이 필요합니다. [.NET Core용](./asp-net-core.md) SDK, [.NET](./asp-net.md), [Node.js](./nodejs.md), [Python](./opencensus-python.md) 및 [Java](./java-in-process-agent.md)용 독립형 에이전트. 또한 이 방법은 최신 버전의 패키지에 대한 업데이트를 직접 관리해야 함을 의미합니다.

    * 에이전트 기반 모니터링을 사용하여 기본적으로 캡처되지 않는 이벤트/종속성을 추적하기 위해 사용자 지정 API 호출을 수행해야 하는 경우 이 방법을 사용해야 합니다. 자세한 내용은 [사용자 지정 이벤트 및 메트릭용 API 문서](./api-custom-events-metrics.md)를 확인하세요. 

> [!NOTE]
> 에이전트 기반 모니터링과 수동 SDK 기반 계측이 모두 감지되면 .NET에서는 수동 계측 설정만 적용되는 반면 Java에서는 에이전트 기반 계측만 원격 분석을 내보냅니다. 이를 통해 중복 데이터가 전송되는 것을 방지합니다. 이 기능에 관한 자세한 내용은 아래 [문제 해결 섹션](#troubleshooting)을 확인하세요.

> [!NOTE]
> 스냅샷 디버거 및 프로파일러는 .NET 및 .Net Core에서만 사용할 수 있습니다.

## <a name="enable-agent-based-monitoring"></a>에이전트 기반 모니터링 사용

# <a name="aspnet"></a>[ASP.NET](#tab/net)

> [!NOTE]
> APPINSIGHTS_JAVASCRIPT_ENABLED 및 urlCompression의 조합은 지원되지 않습니다. 자세한 내용은 [문제 해결 섹션](#appinsights_javascript_enabled-and-urlcompression-is-not-supported)의 설명을 참조하세요.

1. App Service의 Azure 제어판에서 **Application Insights를 선택** 합니다.

    ![설정에서 Application Insights 선택](./media/azure-web-apps/settings-app-insights-01.png)

   * 새 리소스를 만들도록 선택하거나 이 애플리케이션에 대한 기존 Application Insights 리소스를 선택합니다. 

    > [!NOTE]
    > **확인** 을 클릭하여 새 리소스를 만들 경우 **모니터링 설정을 적용** 할지 묻는 메시지가 표시됩니다. **계속** 을 선택하면 새 Application Insights 리소스가 App Service에 연결됩니다. 또한 이로 인해 **App Service 다시 시작이 트리거** 됩니다. 

    >[!div class="mx-imgBorder"]
    >![웹앱 계측](./media/azure-web-apps/ai-create-new.png)

2. 사용할 리소스를 지정한 후 Application Insights에서 애플리케이션에 대한 플랫폼별 데이터를 수집하는 방법을 선택할 수 있습니다. ASP.NET 앱 모니터링은 기본적으로 두 가지 수준의 수집을 사용하여 설정됩니다.

    ![스크린샷에는 새 리소스 만들기가 선택된 Application Insights 사이트 확장 페이지가 표시됩니다.](./media/azure-web-apps/choose-options-new.png)
 
 다음은 각 경로에 대해 수집된 데이터의 요약입니다.
        
| 데이터 | ASP.NET 기본 수집 | ASP.NET 권장 수집 |
| --- | --- | --- |
| CPU, 메모리 및 I/O 사용량 추세를 추가합니다. |예 |예 |
| 사용량 추세를 수집하고, 가용성 결과와 트랜잭션의 상관 관계를 사용하도록 설정합니다. | 예 |예 |
| 호스트 프로세스에서 처리되지 않은 예외를 수집합니다. | 예 |예 |
| 샘플링을 사용하는 경우 부하 상태에서 APM 메트릭 정확도가 향상됩니다. | 예 |예 |
| 요청/종속성 경계 간에 마이크로 서비스를 상호 연결합니다. | 아니요(단일 인스턴스 APM 기능만 해당) |예 |

3. 이전에 applicationinsights.config 파일을 통해 제어할 수 있었던 샘플링 같은 설정을 구성하려는 경우 이제 해당 접두사를 사용하여 애플리케이션 설정을 통해 동일한 설정과 상호 작용할 수 있습니다. 

    * 예를 들어, 초기 샘플링 비율을 변경하기 위해 `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage`와 `100` 값으로 된 애플리케이션 설정을 만들 수 있습니다.

    * 지원되는 적응 샘플링 원격 분석 프로세서 설정 목록은 [코드](https://github.com/microsoft/ApplicationInsights-dotnet/blob/master/BASE/Test/ServerTelemetryChannel.Test/TelemetryChannel.Tests/AdaptiveSamplingTelemetryProcessorTest.cs) 및 [관련 설명서](./sampling.md)를 참조할 수 있습니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/netcore)

### <a name="windows"></a>Windows
> [!IMPORTANT]
> Windows에서 자동 계측을 위해 지원되는 ASP.NET Core 버전은 ASP.NET Core 3.1 및 5.0입니다. 버전 2.0, 2.1, 2.2, 3.0은 사용 중지되었으며 더 이상 지원되지 않습니다. 자동 계측을 사용하려면 .NET Core의 [지원되는 버전](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)으로 업그레이드하세요.


ASP.NET Core에서 전체 프레임워크를 대상으로 지정하는 것은 Windows에서 **지원되지 않습니다**. 대신 코드를 통한 [수동 계측](./asp-net-core.md)을 사용합니다.

Windows에서는 프레임워크 종속 배포만 지원됩니다.

App Service 리소스로 Application Insights 설정을 시작하려면 아래의 [모니터링 사용 섹션](#enable-monitoring )을 참조하세요. 

### <a name="linux"></a>Linux 

> [!IMPORTANT]
> Linux의 자동 계측에는 ASP.NET Core 6.0(미리 보기)만 지원됩니다.

> [!NOTE]
> Linux 자동 계측 App Services 포털 사용은 공개 미리 보기 상태입니다. 이러한 미리 보기 버전은 서비스 수준 계약 없이 제공됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

ASP.NET Core에서 전체 프레임워크를 대상으로 지정하는 것은 Linux에서 **지원되지 않습니다**. 대신 코드를 통한 [수동 계측](./asp-net-core.md)을 사용합니다.

 Linux에서는 프레임워크 종속 배포 및 자체 포함 배포가 지원됩니다. 

App Service 리소스로 Application Insights 설정을 시작하려면 아래의 [모니터링 사용 섹션](#enable-monitoring )을 참조하세요. 

### <a name="enable-monitoring"></a>모니터링 사용 

1. App Service의 Azure 제어판에서 **Application Insights를 선택** 합니다.

    ![설정에서 Application Insights 선택](./media/azure-web-apps/settings-app-insights-01.png)

   * 새 리소스를 만들도록 선택하거나 이 애플리케이션에 대한 기존 Application Insights 리소스를 선택합니다.

    > [!NOTE]
    > **확인** 을 클릭하여 새 리소스를 만들 경우 **모니터링 설정을 적용** 할지 묻는 메시지가 표시됩니다. **계속** 을 선택하면 새 Application Insights 리소스가 App Service에 연결됩니다. 또한 이로 인해 **App Service 다시 시작이 트리거** 됩니다. 

    >[!div class="mx-imgBorder"]
    >![웹앱 계측](./media/azure-web-apps/ai-create-new.png)

2. 사용할 리소스를 지정한 후 Application Insights에서 애플리케이션에 대한 플랫폼별 데이터를 수집하는 방법을 선택할 수 있습니다. ASP.NET Core는 ASP.NET Core 3.1에 대해 **권장 수집** 또는 **사용 안 함** 을 제공합니다.

    ![플랫폼별 옵션을 선택합니다.](./media/azure-web-apps/choose-options-new-net-core.png)




# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

몇 가지 간단한 단계를 수행하여 코드를 변경하지 않고도 Azure App Service에서 실행되는 Node.js 앱을 모니터링할 수 있습니다. Node.js용 Application Insights 애플리케이션은 코드 기반 및 사용자 지정 컨테이너의 경우 둘 다 App Service on Linux와 통합되고 코드 기반 앱의 경우 App Service on Windows와 통합됩니다.

1. App Service의 Azure 제어판에서 **Application Insights를 선택** 합니다.

    > [!div class="mx-imgBorder"]
    > ![설정에서 Application Insights를 선택합니다.](./media/azure-web-apps/ai-enable.png)

   * 이 애플리케이션에 대한 Application Insights 리소스를 설정하지 않은 경우 새 리소스 만들기를 선택합니다. 

    > [!NOTE]
    > **확인** 을 클릭하여 새 리소스를 만들 경우 **모니터링 설정을 적용** 할지 묻는 메시지가 표시됩니다. **계속** 을 선택하면 새 Application Insights 리소스가 App Service에 연결됩니다. 또한 이로 인해 **App Service 다시 시작이 트리거** 됩니다. 

    >[!div class="mx-imgBorder"]
    >![웹앱을 계측합니다.](./media/azure-web-apps/ai-create-new.png)

2. 사용할 리소스를 지정하면 모두 준비된 것입니다. 

    > [!div class="mx-imgBorder"]
    > ![플랫폼별 옵션을 선택합니다.](./media/azure-web-apps/app-service-node.png)



# <a name="java"></a>[Java](#tab/java)

코드를 변경할 필요 없이 한 번 클릭만으로 Azure App Service에서 실행되는 Java 앱의 모니터링을 켤 수 있습니다. Java용 Application Insights는 코드 기반 및 사용자 지정 컨테이너의 경우 둘 다 App Service on Linux와 통합되고 코드 기반 앱의 경우 App Service on Windows와 통합됩니다. 애플리케이션을 모니터링하는 방법을 알고 있어야 합니다. 통합에서 [Application Insights Java 3.x](./java-in-process-agent.md)를 추가하면 자동 수집하는 모든 원격 분석을 가져올 수 있습니다.

1. App Service의 Azure 제어판에서 **Application Insights를 선택** 합니다.

    > [!div class="mx-imgBorder"]
    > ![설정에서 Application Insights를 선택합니다.](./media/azure-web-apps/ai-enable.png)

   * 새 리소스를 만들도록 선택하거나 이 애플리케이션에 대한 기존 Application Insights 리소스를 선택합니다.

    > [!NOTE]
    > **확인** 을 클릭하여 새 리소스를 만들 경우 **모니터링 설정을 적용** 할지 묻는 메시지가 표시됩니다. **계속** 을 선택하면 새 Application Insights 리소스가 App Service에 연결됩니다. 또한 이로 인해 **App Service 다시 시작이 트리거** 됩니다. 

    >[!div class="mx-imgBorder"]
    >![웹앱을 계측합니다.](./media/azure-web-apps/ai-create-new.png)

2. 이 단계는 필요 하지 않습니다. 사용할 리소스를 지정한 후 Java 에이전트를 구성할 수 있습니다. Java 에이전트를 구성하지 않으면 기본 구성이 적용됩니다. 전체 [구성 집합](./java-standalone-config.md)을 사용할 수 있으며 유효한 json 파일을 붙여넣기만 하면 됩니다. 미리 보기에 있는 연결 문자열 및 구성을 제외합니다. 일반적으로 사용할 수 있게 되면 추가할 수 있습니다.

    > [!div class="mx-imgBorder"]
    > ![플랫폼별 옵션을 선택합니다.](./media/azure-web-apps/create-app-service-ai.png)

# <a name="python"></a>[Python](#tab/python)

Python App Service 기반 웹 애플리케이션은 현재 자동 에이전트/확장 기반 모니터링을 지원하지 않습니다. Python 애플리케이션의 모니터링을 사용하려면 [애플리케이션을 수동으로 계측](./opencensus-python.md)해야 합니다.

---

## <a name="enable-client-side-monitoring"></a>클라이언트 쪽 모니터링 사용

# <a name="aspnet"></a>[ASP.NET](#tab/net)

클라이언트 쪽 모니터링은 ASP.NET에 대해 옵트인(opt in)됩니다. 클라이언트 쪽 모니터링을 사용하려면 다음을 수행합니다.

* **설정** **>** **구성**
   * 애플리케이션 설정에서 **새 애플리케이션 설정** 을 만듭니다.

     이름: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     값: `true`

   * 설정을 **저장** 하고 앱을 **다시 시작** 합니다.

클라이언트 쪽 모니터링을 사용하지 않으려면 애플리케이션 설정에서 연결된 키 값 쌍을 제거하거나 값을 false로 설정합니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/netcore)

클라이언트 쪽 모니터링은 앱 설정 ‘APPINSIGHTS_JAVASCRIPT_ENABLED’가 있는지 여부에 관계없이 **권장 수집** 을 통해 ASP.NET Core 앱에 대해 **기본적으로 사용하도록 설정** 됩니다.

어떤 이유로 클라이언트 쪽 모니터링을 사용하지 않으려는 경우:

* **설정** **>** **구성**
   * 애플리케이션 설정에서 **새 애플리케이션 설정** 을 만듭니다.

     이름: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     값: `false`

   * 설정을 **저장** 하고 앱을 **다시 시작** 합니다.

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

Node.js 애플리케이션의 클라이언트 쪽 모니터링을 사용하려면 [클라이언트 쪽 JavaScript SDK를 애플리케이션에 수동으로 추가](./javascript.md)해야 합니다.

# <a name="java"></a>[Java](#tab/java)

Java 애플리케이션의 클라이언트 쪽 모니터링을 사용하려면 [클라이언트 쪽 JavaScript SDK를 애플리케이션에 수동으로 추가](./javascript.md)해야 합니다.

# <a name="python"></a>[Python](#tab/python)

Python 애플리케이션의 클라이언트 쪽 모니터링을 사용하려면 [클라이언트 쪽 JavaScript SDK를 애플리케이션에 수동으로 추가](./javascript.md)해야 합니다.

---

## <a name="automate-monitoring"></a>모니터링 자동화

Application Insights를 통해 원격 분석 컬렉션을 사용하도록 설정하려면 애플리케이션 설정만 설정해야 합니다.

   ![사용 가능한 Application Insights 설정을 사용한 App Service 애플리케이션 설정](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>애플리케이션 설정 정의

|앱 설정 이름 |  정의 | 값 |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | 런타임 모니터링을 제어하는 기본 확장입니다. | Windows의 경우 `~2` 또는 Linux의 경우 `~3` |
|XDT_MicrosoftApplicationInsights_Mode |  기본 모드에서는 최적 성능을 보장하기 위해 필수 기능만 사용됩니다. | `default` 또는 `recommended` |
|InstrumentationEngine_EXTENSION_VERSION | 이진 다시 쓰기 엔진 `InstrumentationEngine`을 켤지 여부를 제어합니다. 이 설정은 성능에 영향을 주며 콜드 부팅/시작 시간에 영향을 줍니다. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | SQL 및 Azure 테이블 텍스트를 종속성 호출과 함께 캡처할지 여부를 제어합니다. 성능 경고: 애플리케이션 콜드 부팅 작동 시간이 영향을 받습니다. 이 설정에는 `InstrumentationEngine`이 필요합니다. | `~1` |
|XDT_MicrosoftApplicationInsights_PreemptSdk | ASP.NET Core 앱에만 해당합니다. Application Insights SDK와 상호 운용을 사용하도록 설정합니다. SDK를 사용하여 확장을 나란히 로드하고 이를 사용하여 원격 분석을 전송합니다(Application Insights SDK를 사용하지 않음). |`1`|

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Azure Resource Manager를 사용한 App Service 애플리케이션 설정

[Azure Resource Manager 템플릿](../../azure-resource-manager/templates/syntax.md)을 사용하여 App Services의 애플리케이션 설정을 관리하고 구성할 수 있습니다. 이 방법은 Azure Resource Manager 자동화를 사용하여 새 App Service 리소스를 배포하거나 기존 리소스의 설정을 수정할 때 사용할 수 있습니다.

App Service에 대한 애플리케이션 설정 JSON의 기본 구조는 다음과 같습니다.

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]
```

Application Insights에 대해 구성된 애플리케이션 설정을 사용한 Azure Resource Manager 템플릿 예제의 경우 이 [템플릿](https://github.com/Andrew-MSFT/BasicImageGallery), 특히 [줄 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238)에서 시작하는 섹션이 유용할 수 있습니다.

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Application Insights 리소스 만들기를 자동화하고 새로 만든 App Service에 연결합니다.

구성된 모든 기본 Application Insights 설정을 사용하여 Azure Resource Manager 템플릿을 만들려면 Application Insights가 사용되는 새 웹앱을 만드는 것처럼 프로세스를 시작합니다.

**자동화 옵션** 선택

   ![App Service 웹앱 만들기 메뉴](./media/azure-web-apps/create-web-app.png)

이 옵션은 모든 필수 설정이 구성된 최신 Azure Resource Manager 템플릿을 생성합니다.

  ![App Service 웹앱 템플릿](./media/azure-web-apps/arm-template.png)

다음은 `AppMonitoredSite`의 모든 인스턴스를 사이트 이름으로 바꾸는 샘플입니다.

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enabling-through-powershell"></a>PowerShell을 통해 사용하도록 설정

PowerShell을 통해 애플리케이션 모니터링을 사용하도록 설정하려면 기본 애플리케이션 설정만 변경해야 합니다. 다음은 리소스 그룹 “AppMonitoredRG”의 “AppMonitoredSite”라는 웹 사이트에서 애플리케이션 모니터링을 사용하도록 설정하고 “012345678-abcd-ef01-2345-6789abcd” 계측 키로 전송할 데이터를 구성하는 샘플입니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent---net"></a>모니터링 확장/에이전트 업그레이드 - .NET 

### <a name="upgrading-from-versions-289-and-up"></a>버전 2.8.9 이상에서 업그레이드

버전 2.8.9에서 업그레이드는 추가 작업 없이 자동으로 수행됩니다. 새 모니터링 비트는 백그라운드에서 대상 앱 서비스로 전달되고 애플리케이션이 다시 시작되면 해당 비트가 선택됩니다.

실행 중인 확장의 버전을 확인하려면 `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`로 이동합니다.

![실행 중인 확장의 버전을 확인하기 위한 URL 경로의 스크린샷](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>버전 1.0.0~2.6.5에서 업그레이드

버전 2.8.9부터 미리 설치된 사이트 확장이 사용됩니다. 이전 버전인 경우 다음 두 가지 방법 중 하나를 통해 업데이트할 수 있습니다.

* [포털을 통해 사용하도록 설정하여 업그레이드](#enable-application-insights). Azure App Service의 Application Insights 확장이 설치된 경우에도 UI는 **사용** 단추만 표시합니다. 백그라운드에서 이전 프라이빗 사이트 확장이 제거됩니다.

* [PowerShell을 통해 업그레이드](#enabling-through-powershell):

    1. 애플리케이션 설정을 설정하여 미리 설치된 사이트 확장 ApplicationInsightsAgent를 사용하도록 설정합니다. [PowerShell을 통해 사용하도록 설정](#enabling-through-powershell)을 참조하세요.
    2. Azure App Service의 Application Insights 확장이라는 프라이빗 사이트 확장을 수동으로 제거합니다.

2\.5.1 이전 버전에서 업그레이드한 경우 애플리케이션 bin 폴더에서 ApplicationInsigths dll이 제거되었는지 확인합니다([문제 해결 단계 참조](#troubleshooting)).

## <a name="troubleshooting"></a>문제 해결

### <a name="aspnet-and-aspnet-core"></a>ASP.NET 및 ASP.NET CORE

다음은 Azure App Services에서 실행되는 ASP.NET 및 ASP.NET Core 기반 애플리케이션의 확장/에이전트 기반 모니터링에 관한 단계별 문제 해결 가이드입니다.

#### <a name="windows-troubleshooting"></a>Windows 문제 해결 
1. `ApplicationInsightsAgent_EXTENSION_VERSION` 앱 설정이 “~2” 값으로 설정되어 있는지 확인합니다.
2. [https://www.microsoft.com]\(`https://yoursitename.scm.azurewebsites.net/ApplicationInsights`) 로 이동합니다.  

    ![https://yoursitename.scm.azurewebsites/applicationinsights 결과 페이지의 스크린샷](./media/azure-web-apps/app-insights-sdk-status.png)
    
    - `Application Insights Extension Status`가 `Pre-Installed Site Extension, version 2.8.x.xxxx, is running.`인지 확인합니다. 
    
         실행되고 있지 않으면 [Application Insights 모니터링 사용 지침](#enable-application-insights)을 따릅니다.

    - 상태 원본이 존재하고 다음과 같이 표시되는지 확인합니다. `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`

         유사한 값이 없는 경우에는 애플리케이션이 현재 실행되고 있지 않거나 지원되지 않음을 의미합니다. 애플리케이션이 실행되고 있는지 확인하려면 런타임 정보가 제공될 수 있도록 애플리케이션 URL/애플리케이션 엔드포인트를 수동으로 방문해 봅니다.

    - `IKeyExists`가 `true`인지 확인합니다. `false`인 경우 ikey guid를 사용하여 애플리케이션 설정에 `APPINSIGHTS_INSTRUMENTATIONKEY` 및 `APPLICATIONINSIGHTS_CONNECTION_STRING`을 추가합니다.

    - **ASP.NET 앱만 해당** `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly` 및 `AppContainsAspNetTelemetryCorrelationAssembly`에 해당하는 항목이 없는지 확인합니다.

         해당 항목이 있는 경우 애플리케이션에서 `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation` 패키지를 제거합니다.

    - **ASP.NET Core 앱에만 해당**: 애플리케이션이 Application Insights 패키지를 참조하는 경우(예: 이전에 [ASP.NET Core SDK](./asp-net-core.md)를 사용하여 앱을 계측했거나 계측하려고 시도한 경우) App Service 통합이 사용하도록 설정되지 않을 수 있고 데이터가 Application Insights에 표시되지 않을 수 있습니다. 이 문제를 해결하려면 포털에서 “Application Insights SDK와 상호 운용”을 켭니다. 그러면 Application Insights에 데이터가 표시되기 시작합니다. 
        > [!IMPORTANT]
        > 이 기능은 미리 보기 상태입니다. 

        ![기존 앱의 설정 사용](./media/azure-web-apps/netcore-sdk-interop.png)

        Application Insights SDK를 원래 사용했거나 사용하려고 시도한 경우에도 이제 코드리스 접근 방식을 사용하여 데이터를 전송하려고 합니다.

        > [!IMPORTANT]
        > 애플리케이션이 Application Insights SDK를 사용하여 원격 분석을 보낸 경우 해당 원격 분석은 사용하도록 설정되지 않습니다. 즉, 사용자 지정 원격 분석(있는 경우, 예: Track*() 메서드) 및 사용자 지정 설정(예: 샘플링)이 사용하지 않도록 설정됩니다. 

#### <a name="linux-troubleshooting"></a>Linux 문제 해결

1. `ApplicationInsightsAgent_EXTENSION_VERSION` 앱 설정이 “~3” 값으로 설정되어 있는지 확인합니다.
2. */home\LogFiles\ApplicationInsights\status* 로 이동하여 *status_557de146e7fa_27_1.json* 을 엽니다.

    `AppAlreadyInstrumented`가 false로, `AiHostingStartupLoaded`가 true로, `IKeyExists`가 true로 설정되어 있는지 확인합니다.

    다음은 JSON 파일의 예입니다.

    ```json
        "AppType":".NETCoreApp,Version=v6.0",
                
        "MachineName":"557de146e7fa",
                
        "PID":"27",
                
        "AppDomainId":"1",
                
        "AppDomainName":"dotnet6demo",
                
        "InstrumentationEngineLoaded":false,
                
        "InstrumentationEngineExtensionLoaded":false,
                
        "HostingStartupBootstrapperLoaded":true,
                
        "AppAlreadyInstrumented":false,
                
        "AppDiagnosticSourceAssembly":"System.Diagnostics.DiagnosticSource, Version=6.0.0.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51",
                
        "AiHostingStartupLoaded":true,
                
        "IKeyExists":true,
                
        "IKey":"00000000-0000-0000-0000-000000000000",
                
        "ConnectionString":"InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://westus-0.in.applicationinsights.azure.com/"
    
    ```
    
    `AppAlreadyInstrumented`가 true이면 확장에서 SDK의 일부 측면이 애플리케이션에 이미 있고 백오프될 것임을 검색했음을 나타냅니다.

##### <a name="no-data-for-linux"></a>Linux용 데이터 없음 

1. 앱을 호스팅하는 프로세스를 나열하고 식별합니다. 터미널로 이동하고 명령줄에 `ps ax`를 입력합니다. 
    
    출력은 다음과 비슷해야 합니다. 

   ```bash
     PID TTY      STAT   TIME COMMAND
    
        1 ?        SNs    0:00 /bin/bash /opt/startup/startup.sh
    
       19 ?        SNs    0:00 /usr/sbin/sshd
    
       27 ?        SNLl   5:52 dotnet dotnet6demo.dll
    
       50 ?        SNs    0:00 sshd: root@pts/0
    
       53 pts/0    SNs+   0:00 -bash
    
       55 ?        SNs    0:00 sshd: root@pts/1
    
       57 pts/1    SNs+   0:00 -bash
   ``` 


1. 그런 다음 앱 프로세스에서 환경 변수를 나열합니다. 명령줄에서 `cat /proc/27/environ | tr '\0' '\n`을 입력합니다.
    
    출력은 다음과 비슷해야 합니다. 

    ```bash
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES=Microsoft.ApplicationInsights.StartupBootstrapper
    
    DOTNET_STARTUP_HOOKS=/DotNetCoreAgent/2.8.39/StartupHook/Microsoft.ApplicationInsights.StartupHook.dll
    
    APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://westus-0.in.applicationinsights.azure.com/
    
    ```
    

1. `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_STARTUP_HOOKS` 및 `APPLICATIONINSIGHTS_CONNECTION_STRING`이 설정되었는지 확인합니다.


#### <a name="default-website-deployed-with-web-apps-does-not-support-automatic-client-side-monitoring"></a>웹앱으로 배포된 기본 웹 사이트에서 자동 클라이언트 쪽 모니터링을 지원하지 않음

Azure App Services에서 `ASP.NET` 또는 `ASP.NET Core` 런타임을 사용하여 웹앱을 만들면 단일 정적 HTML 페이지가 시작 웹 사이트로 배포됩니다. 또한 정적 웹 페이지는 IIS에서 ASP.NET 관리형 웹 파트를 로드합니다. 이를 통해 코드리스 서버 쪽 모니터링을 테스트할 수 있지만 자동 클라이언트 쪽 모니터링은 지원하지 않습니다.

Azure App Services 웹앱에서 ASP.NET 또는 ASP.NET Core에 대한 코드리스 서버와 클라이언트 쪽 모니터링을 테스트하려면 [ASP.NET Core 웹앱 만들기](../../app-service/quickstart-dotnetcore.md) 및 [ASP.NET Framework 웹앱 만들기](../../app-service/quickstart-dotnetcore.md?tabs=netframework48)의 공식 가이드를 따른 후 현재 문서의 지침을 사용하여 모니터링을 사용하도록 설정하는 것이 좋습니다.


### <a name="php-and-wordpress-are-not-supported"></a>PHP 및 WordPress가 지원되지 않음

PHP 및 WordPress 사이트가 지원되지 않습니다. 현재 이 워크로드의 서버 쪽 모니터링에 대해 공식적으로 지원되는 SDK/에이전트는 없습니다. 그러나 [JavaScript SDK](./javascript.md)를 사용하면 클라이언트 쪽 JavaScript를 웹 페이지에 추가하여 PHP 또는 WordPress 사이트에서 클라이언트 쪽 트랜잭션을 수동으로 계측할 수 있습니다.

아래 표에서는 해당 값의 의미, 근본 원인 및 권장 수정 사항을 더 자세히 설명합니다.

|문제 값|설명|Fix
|---- |----|---|
| `AppAlreadyInstrumented:true` | 이 값은 확장에서 SDK의 일부 측면이 애플리케이션에 이미 있고 백오프될 것임을 검색했음을 나타냅니다. `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation` 또는 `Microsoft.ApplicationInsights`에 대한 참조가 원인일 수 있습니다.  | 참조를 제거합니다. 해당 참조 중 일부는 특정 Visual Studio 템플릿에서 기본적으로 추가되며 이전 버전의 Visual Studio는 `Microsoft.ApplicationInsights`에 대한 참조를 추가할 수 있습니다.
|`AppAlreadyInstrumented:true` | 애플리케이션이 ASP.NET Core 2.1 또는 2.2를 대상으로 하는 경우 이 값은 확장에서 SDK의 일부 측면이 애플리케이션에 이미 있고 백오프될 것임을 검색했음을 나타냅니다. | .NET Core 2.1, 2.2의 고객은 Microsoft.AspNetCore.App 메타 패키지를 대신 [사용하는 것이 좋습니다](https://github.com/aspnet/Announcements/issues/287). 또한 포털에서 “Application Insights SDK와 상호 운용”을 켭니다(위 지침 참조).|
|`AppAlreadyInstrumented:true` | 이전 배포의 앱 폴더에 위 dll이 있기 때문에 이 값이 발생할 수도 있습니다. | 앱 폴더를 정리하여 이 dll이 제거되도록 합니다. 로컬 앱의 bin 디렉터리와 App Service의 wwwroot 디렉터리를 둘 다 확인합니다. App Service 웹앱의 wwwroot 디렉터리를 확인하려면: 고급 도구(Kudu) > 디버그 콘솔 > CMD > home\site\wwwroot.
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | 이 값은 확장에서 애플리케이션의 `Microsoft.AspNet.TelemetryCorrelation`에 대한 참조를 검색했고 백오프될 것임을 나타냅니다. | 참조를 제거합니다.
|`AppContainsDiagnosticSourceAssembly**:true`|이 값은 확장에서 애플리케이션의 `System.Diagnostics.DiagnosticSource`에 대한 참조를 검색했고 백오프될 것임을 나타냅니다.| ASP.NET의 경우 참조를 제거합니다. 
|`IKeyExists:false`|이 값은 AppSetting `APPINSIGHTS_INSTRUMENTATIONKEY`에 계측 키가 없음을 나타냅니다. 가능한 원인: 값이 실수로 제거되거나 자동화 스크립트에서 값을 설정하지 않은 것일 수 있습니다. | 설정이 App Service 애플리케이션 설정에 있는지 확인합니다.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED 및 urlCompression이 지원되지 않음

콘텐츠가 인코딩된 경우 APPINSIGHTS_JAVASCRIPT_ENABLED=true를 사용하면 다음과 같은 오류가 발생할 수 있습니다. 

- 500 URL 다시 쓰기 오류
- HTTP 응답의 콘텐츠를 인코딩할 때 메시지 아웃바운드 다시 쓰기 규칙과 관련된 500.53 URL 다시 쓰기 모듈 오류를 적용할 수 없습니다(‘gzip’). 

원인은 APPINSIGHTS_JAVASCRIPT_ENABLED 애플리케이션 설정이 true로 설정되고 콘텐츠 인코딩이 동시에 제공되기 때문입니다. 이 시나리오는 아직 지원되지 않습니다. 해결 방법은 애플리케이션 설정에서 APPINSIGHTS_JAVASCRIPT_ENABLED를 제거하는 것입니다. 안타깝게도 이는 클라이언트/브라우저 쪽 JavaScript 계측이 계속 필요한 경우 웹 페이지에 대한 수동 SDK 참조가 필요함을 의미합니다. JavaScript SDK를 사용한 수동 계측에 관한 [지침](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup)을 따릅니다.

Application Insights 에이전트/확장에 관한 최신 정보는 [릴리스 정보](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md)를 확인하세요.


### <a name="connection-string-and-instrumentation-key"></a>연결 문자열 및 계측 키

코드리스 모니터링을 사용하는 경우 연결 문자열만 필요합니다. 그러나 수동 계측을 수행할 때 이전 버전의 SDK에 대한 이전 버전과의 호환성을 유지하기 위해 계측 키를 설정하는 것이 좋습니다.

### <a name="difference-between-standard-metrics-from-application-insights-vs-azure-app-service-metrics"></a>Application Insights의 표준 메트릭과 Azure App Service 메트릭 간에 차이가 있나요?

Application Insights는 애플리케이션에 대해 수행한 관련 요청에 대한 원격 분석을 수집합니다. 오류가 WebApps/IIS에서 발생했고 요청이 사용자 애플리케이션에 도달하지 않은 경우에는 Application Insights에는 이와 관련된 원격 분석이 없습니다.

Application Insights에서 계산되는 `serverresponsetime`의 기간이 Web Apps에서 관찰되는 서버 응답 시간과 반드시 일치하는 것은 아닙니다. 이는 Application Insights는 실제 요청이 사용자 애플리케이션에 도달하는 기간만 계산되기 때문입니다. 요청이 IIS에서 중단/대기 중인 경우 해당 대기 시간은 웹앱 메트릭에 포함되지만 Application Insights 메트릭에는 포함되지 않습니다.

## <a name="release-notes"></a>릴리스 정보

최신 업데이트 및 버그 수정은 [릴리스 정보를 참조하세요](./web-app-extension-release-notes.md).

## <a name="next-steps"></a>다음 단계
* [라이브 앱에서 프로파일러를 실행합니다](./profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - Application Insights로 Azure Functions 모니터링
* [Azure 진단을 사용](../agents/diagnostics-extension-to-application-insights.md) 하여 Application Insights에 보냅니다.
* [서비스 상태 메트릭을 모니터링](../data-platform.md)하여 서비스를 사용 가능하며 응답할 수 있는 상태로 유지합니다.
* 작업 이벤트가 발생하거나 메트릭이 임계값을 초과할 때마다 [경고 알림을 수신](../alerts/alerts-overview.md)합니다.
* [JavaScript 앱 및 웹 페이지용 Application Insights](javascript.md)를 사용하여 웹 페이지로 이동하는 브라우저에서 클라이언트 원격 분석을 가져옵니다.
* [가용성 웹 테스트를 설정](monitor-web-app-availability.md) 합니다.
