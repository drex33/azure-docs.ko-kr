---
title: Azure app services 성능 Node.js 모니터링 | Microsoft Docs
description: Node.js를 사용 하 여 Azure app services에 대 한 응용 프로그램 성능 모니터링. 차트 부하 및 응답 시간, 종속성 정보 및 성능에 대한 경고를 설정합니다.
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 5f80666d4a184da40979a38f6d7f17782291c9e0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128705603"
---
# <a name="application-monitoring-for-azure-app-service-and-nodejs"></a>Azure App Service 및 Node.js에 대 한 응용 프로그램 모니터링

이제 [Azure 앱 서비스](../../app-service/index.yml) 에서 실행 되는 Node.js 기반 웹 응용 프로그램에 대 한 모니터링을 사용 하도록 설정 하는 것이 훨씬 쉬워졌습니다. 이전에는 수동으로 앱을 계측해야 했지만 이제 최신 확장/에이전트가 기본적으로 App Service 이미지에 기본 제공됩니다. 이 문서에서는 Azure Monitor Application Insights 모니터링을 사용하도록 설정하는 과정을 안내하고 대규모 배포 프로세스를 자동화하기 위한 예비 지침을 제공합니다.

> [!NOTE]
> 에이전트 기반 모니터링과 수동 SDK 기반 계측이 둘 다 검색된 경우에는 수동 계측 설정만 적용됩니다. 이를 통해 중복 데이터가 전송되는 것을 방지합니다. 이 기능에 관한 자세한 내용은 아래 [문제 해결 섹션](#troubleshooting)을 확인하세요.

## <a name="enable-agent-based-monitoring"></a>에이전트 기반 모니터링 사용

몇 가지 간단한 단계를 수행하여 코드를 변경하지 않고도 Azure App Service에서 실행되는 Node.js 앱을 모니터링할 수 있습니다. Node.js용 Application Insights 애플리케이션은 코드 기반 및 사용자 지정 컨테이너의 경우 둘 다 App Service on Linux와 통합되고 코드 기반 앱의 경우 App Service on Windows와 통합됩니다. 통합은 퍼블릭 미리 보기 상태입니다. 통합은 GA에 있는 Node.js SDK를 추가합니다. 

1. app service에 대 한 Azure 제어판에서 **Application Insights을 선택** 하 고 **사용** 을 선택 합니다.

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="사용이 선택 된 Application Insights 탭의 스크린샷"::: 

2. 새 리소스를 만들도록 선택하거나 이 애플리케이션에 대한 기존 Application Insights 리소스를 선택합니다.

     > [!NOTE]
     > **확인** 을 클릭하여 새 리소스를 만들 경우 **모니터링 설정을 적용** 할지 묻는 메시지가 표시됩니다. **계속** 을 선택하면 새 Application Insights 리소스가 App Service에 연결됩니다. 또한 이로 인해 **App Service 다시 시작이 트리거** 됩니다. 
    
    :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="리소스 드롭다운 변경의 스크린샷"::: 
    
3. 사용할 리소스를 지정하면 모두 준비된 것입니다. 


    :::image type="content"source="./media/azure-web-apps-nodejs/app-service-node.png" alt-text="응용 프로그램 계측의 스크린샷"::: 


## <a name="enable-client-side-monitoring"></a>클라이언트 쪽 모니터링 사용

Node.js 애플리케이션의 클라이언트 쪽 모니터링을 사용하려면 [클라이언트 쪽 JavaScript SDK를 애플리케이션에 수동으로 추가](./javascript.md)해야 합니다.

## <a name="automate-monitoring"></a>모니터링 자동화

Application Insights를 통해 원격 분석 컬렉션을 사용하도록 설정하려면 애플리케이션 설정만 설정해야 합니다.

:::image type="content"source="./media/azure-web-apps-nodejs/application-settings-nodejs.png" alt-text="사용 가능한 Application Insights 설정으로 응용 프로그램 설정 App Service 스크린샷"::: 


### <a name="application-settings-definitions"></a>애플리케이션 설정 정의

|앱 설정 이름 |  정의 | 값 |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | 런타임 모니터링을 제어하는 기본 확장입니다. | `~2` Windows 또는 `~3` Linux |
|XDT_MicrosoftApplicationInsights_NodeJS |  node.js 에이전트가 포함 되는지 여부를 제어 하는 플래그입니다. | 0 또는 1은 Windows에만 적용 됩니다. |


[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]


## <a name="troubleshooting"></a>문제 해결

다음은 Azure 앱 서비스에서 실행 되는 Node.js 기반 응용 프로그램에 대 한 확장/에이전트 기반 모니터링에 대 한 단계별 문제 해결 가이드입니다.

# <a name="windows"></a>[Windows](#tab/windows)

1. `ApplicationInsightsAgent_EXTENSION_VERSION` 앱 설정이 “~2” 값으로 설정되어 있는지 확인합니다.
2. [https://www.microsoft.com]\(`https://yoursitename.scm.azurewebsites.net/ApplicationInsights`) 로 이동합니다.  

    :::image type="content"source="./media/azure-web-apps/app-insights-sdk-status.png" alt-text="위 결과 페이지 링크의 스크린샷"border ="false"::: 

    - `Application Insights Extension Status`가 `Pre-Installed Site Extension, version 2.8.x.xxxx, is running.`인지 확인합니다. 

         실행되고 있지 않으면 [Application Insights 모니터링 사용 지침](#enable-agent-based-monitoring)을 따릅니다.

    - *D:\local\temp\status.json* 으로 이동 하 여 *status* 를 엽니다.

    `SDKPresent`가 false로 설정 되어 있는지 확인 하 고, true로 설정 하 `AgentInitializedSuccessfully` 고, `IKey` 유효한 ikey를 갖도록 합니다.

    다음은 JSON 파일의 예입니다.

    ```json
        "AppType":"node.js",
                
        "MachineName":"c89d3a6d0357",
                
        "PID":"47",
                
        "AgentInitializedSuccessfully":true,
                
        "SDKPresent":false,
                
        "IKey":"00000000-0000-0000-0000-000000000000",
                
        "SdkVersion":"1.8.10"
    
    ```

    `SDKPresent`가 true이면 확장에서 SDK의 일부 측면이 애플리케이션에 이미 있고 백오프될 것임을 검색했음을 나타냅니다.


# <a name="linux"></a>[Linux](#tab/linux)

1. `ApplicationInsightsAgent_EXTENSION_VERSION` 앱 설정이 “~3” 값으로 설정되어 있는지 확인합니다.
2. */Var/log/applicationinsights/* 으로 이동 하 여 *status* 를 엽니다.

    `SDKPresent`가 false로 설정 되어 있는지 확인 하 고, true로 설정 하 `AgentInitializedSuccessfully` 고, `IKey` 유효한 ikey를 갖도록 합니다.

    다음은 JSON 파일의 예입니다.

    ```json
        "AppType":"node.js",
                
        "MachineName":"c89d3a6d0357",
                
        "PID":"47",
                
        "AgentInitializedSuccessfully":true,
                
        "SDKPresent":false,
                
        "IKey":"00000000-0000-0000-0000-000000000000",
                
        "SdkVersion":"1.8.10"
    
    ```

    `SDKPresent`가 true이면 확장에서 SDK의 일부 측면이 애플리케이션에 이미 있고 백오프될 것임을 검색했음을 나타냅니다.
---

[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

## <a name="release-notes"></a>릴리스 정보

최신 업데이트 및 버그 수정은 [릴리스 정보를 참조하세요](web-app-extension-release-notes.md).

## <a name="next-steps"></a>다음 단계
* [Application Insights를 사용 하 여 Azure Functions를 모니터링](monitor-functions.md)합니다.
* [Azure 진단을 사용](../agents/diagnostics-extension-to-application-insights.md) 하여 Application Insights에 보냅니다.
* [서비스 상태 메트릭을 모니터링](../data-platform.md)하여 서비스를 사용 가능하며 응답할 수 있는 상태로 유지합니다.
* 작업 이벤트가 발생하거나 메트릭이 임계값을 초과할 때마다 [경고 알림을 수신](../alerts/alerts-overview.md)합니다.
* [JavaScript 앱 및 웹 페이지용 Application Insights](javascript.md)를 사용하여 웹 페이지로 이동하는 브라우저에서 클라이언트 원격 분석을 가져옵니다.
* [가용성 웹 테스트를 설정](monitor-web-app-availability.md) 합니다.