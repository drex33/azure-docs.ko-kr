---
title: Azure App Services 성능 모니터링 Java | Microsoft Docs
description: Java를 사용하는 Azure App Service에 대한 애플리케이션 성능 모니터링. 차트 부하 및 응답 시간, 종속성 정보 및 성능에 대한 경고를 설정합니다.
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-java
ms.openlocfilehash: d673524b30eae13e24758aff23a68bd2b38c2e3e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128705606"
---
# <a name="application-monitoring-for-azure-app-service-and-java"></a>Azure App Service 및 Java용 애플리케이션 모니터링

[Azure 앱 Services에서](../../app-service/index.yml) 실행되는 Java 기반 웹 애플리케이션을 모니터링하기 위해 코드를 수정할 필요가 없습니다. 이 문서에서는 Azure Monitor Application Insights 모니터링을 사용하도록 설정하는 과정을 안내하고 대규모 배포 프로세스를 자동화하기 위한 예비 지침을 제공합니다.

## <a name="enable-application-insights"></a>Application Insights 사용

Azure 앱 Services에서 실행되는 Java 애플리케이션에 대한 애플리케이션 모니터링을 사용하도록 설정하는 권장 방법은 Azure Portal 통해서입니다. Azure Portal 애플리케이션 모니터링을 켜면 애플리케이션 인사이트를 통해 애플리케이션이 자동으로 계측됩니다.  

### <a name="auto-instrumentation-through-azure-portal"></a>Azure Portal 통한 자동 계측

이 메서드에는 코드 변경이나 고급 구성이 필요하지 않으며 Azure 앱 Services에 대한 모니터링을 시작하는 가장 쉬운 방법입니다. 추가 구성을 적용한 다음 특정 시나리오에 따라 [수동 계측을](./java-2x-get-started.md?tabs=maven) 통한 고급 모니터링이 필요한지 여부를 평가할 수 있습니다.

### <a name="enable-backend-monitoring"></a>백 엔드 모니터링 사용

코드를 변경할 필요 없이 한 번 클릭만으로 Azure App Service에서 실행되는 Java 앱의 모니터링을 켤 수 있습니다. Java용 Application Insights는 코드 기반 및 사용자 지정 컨테이너의 경우 둘 다 App Service on Linux와 통합되고 코드 기반 앱의 경우 App Service on Windows와 통합됩니다. 애플리케이션을 모니터링하는 방법을 알고 있어야 합니다. 통합은 [Application Insights Java 3.x를](./java-in-process-agent.md) 추가하며 원격 분석이 자동으로 수집됩니다.

1. 앱 서비스에 대한 Azure 제어판에서 **애플리케이션 Insights** 선택한 다음, **사용을** 선택합니다.

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="사용이 선택된 애플리케이션 Insights 탭의 스크린샷."::: 

2. 새 리소스를 만들도록 선택하거나 이 애플리케이션에 대한 기존 Application Insights 리소스를 선택합니다.

    > [!NOTE]
    > **확인을** 선택하여 새 리소스를 만들면 **모니터링 설정 적용을** 묻는 메시지가 표시됩니다. **계속** 을 선택하면 새 Application Insights 리소스가 App Service에 연결됩니다. 또한 이로 인해 **App Service 다시 시작이 트리거** 됩니다. 

     :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="리소스 변경 드롭다운의 스크린샷."::: 

3. 이 단계는 필요 하지 않습니다. 사용할 리소스를 지정한 후 Java 에이전트를 구성할 수 있습니다. Java 에이전트를 구성하지 않으면 기본 구성이 적용됩니다. 

    전체 [구성 집합을](./java-standalone-config.md) 사용할 수 있습니다. 유효한 [json 파일을](./java-standalone-config.md#an-example)붙여넣기만 하면 됩니다. **연결 문자열 및 미리 보기에 있는 모든 구성을 제외합니다.** 일반적으로 사용할 수 있게 되면 현재 미리 보기에 있는 항목을 추가할 수 있습니다.

    Azure Portal 통해 구성을 수정하면 APPLICATIONINSIGHTS_CONFIGURATION_FILE 환경 변수가 자동으로 채워지고 App Service 설정 패널에 표시됩니다. 이 변수에는 Java 앱의 Azure Portal 구성 텍스트 상자에 붙여넣은 전체 json 콘텐츠가 포함됩니다. 

    :::image type="content"source="./media/azure-web-apps-java/create-app-service-ai.png" alt-text="애플리케이션 계측의 스크린샷."::: 
    

## <a name="enable-client-side-monitoring"></a>클라이언트 쪽 모니터링 사용

Java 애플리케이션의 클라이언트 쪽 모니터링을 사용하려면 [클라이언트 쪽 JavaScript SDK를 애플리케이션에 수동으로 추가](./javascript.md)해야 합니다.

## <a name="automate-monitoring"></a>모니터링 자동화

### <a name="application-settings"></a>애플리케이션 설정

Application Insights 원격 분석 수집을 사용하도록 설정하려면 다음 애플리케이션 설정만 설정해야 합니다.

|앱 설정 이름 |  정의 | 값 |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | 런타임 모니터링 제어 | Windows의 경우 `~2` 또는 Linux의 경우 `~3` |
|XDT_MicrosoftApplicationInsights_Java |  Java 에이전트가 포함되는 것을 제어하는 플래그 | 0 또는 1은 Windows
|APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL | Application Insights App Service 통합을 디버그해야 하는 경우에만 사용합니다. | debug

:::image type="content"source="./media/azure-web-apps-java/application-settings-java.png" alt-text="사용 가능한 애플리케이션 Insights 설정이 있는 App Service 애플리케이션 설정 스크린샷."::: 

> [!NOTE]
> Java 애플리케이션에는 프로파일러 및 스냅샷 디버거를 사용할 수 없습니다.

[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]

## <a name="troubleshooting"></a>문제 해결

다음은 Azure 앱 Services에서 실행되는 Java 기반 애플리케이션에 대한 단계별 문제 해결 가이드입니다.

1. 앱 `ApplicationInsightsAgent_EXTENSION_VERSION` 설정이 Windows "~2" 값으로 설정되고 Linux에서는 "~3"으로 설정되어 있는지 확인합니다.
1. 로그 파일을 검사하여 에이전트가 성공적으로 시작되었는지 확인합니다. 루트 디렉터리에 대한 https://yoursitename.scm.azurewebsites.net/ SSH 변경에서 '로 이동하면 로그 파일이 LogFiles/ApplicationInsights 아래에 있습니다. 
  
    :::image type="content"source="./media/azure-web-apps-java/app-insights-java-status.png" alt-text="위의 결과 페이지 링크 스크린샷."::: 

1. Java 앱에 애플리케이션 모니터링을 사용하도록 설정한 후에는 라이브 메트릭을 확인하여 에이전트가 작동하는지 확인할 수 있습니다. App Service 배포 및 앱을 배포하기 전에도 환경에서 일부 요청이 표시됩니다. 전체 원격 분석 세트는 앱을 배포하고 실행하는 경우에만 사용할 수 있습니다. 
1. 오류가 표시되지 않고 원격 분석이 없는 경우 APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL 환경 변수를 '디버그'로 설정합니다.
1. App Service 최신 버전의 Application Insights Java 에이전트를 사용할 수 없는 경우도 있습니다. 최신 버전이 모든 지역에 롤아웃되는 데 몇 달이 걸립니다. App Service 앱을 모니터링하기 위해 최신 버전의 Java 에이전트가 필요한 경우 에이전트를 수동으로 업로드할 수 있습니다. 
    * Java 에이전트 jar 파일을 App Service 업로드
        * 최신 버전의 [Azure CLI](/cli/azure/install-azure-cli-windows?tabs=azure-cli)
        * 최신 버전의 [Application Insights Java 에이전트](./java-in-process-agent.md) 다운로드
        * java 에이전트를 App Service 배포 - Java 에이전트 jar을 `az webapp deploy --src-path applicationinsights-agent-{VERSION_NUMBER}.jar --target-path java/applicationinsights-agent-{VERSION_NUMBER}.jar --type static --resource-group {YOUR_RESOURCE_GROUP} --name {YOUR_APP_SVC_NAME}` 배포하는 샘플 명령입니다. 또는 [이 가이드를](../../app-service/quickstart-java.md?tabs=javase&pivots=platform-linux#configure-the-maven-plugin) 사용하여 Maven 플러그 인을 통해 배포합니다.
    * 에이전트 jar 파일이 업로드되면 App Service 구성으로 이동하여 새 환경 변수를 추가하고 JAVA_OPTS 해당 값을 로 설정합니다. `-javaagent:D:/home/{PATH_TO_THE_AGENT_JAR}/applicationinsights-agent-{VERSION_NUMBER}.jar`
    * 애플리케이션 Insights 탭을 통해 애플리케이션 Insights 사용 안 함
    * 앱 다시 시작

    > [!NOTE]
    > JAVA_OPTS 환경 변수를 설정하는 경우 포털에서 애플리케이션 Insights 사용하지 않도록 설정해야 합니다. 또는 포털에서 애플리케이션 Insights 사용하도록 설정하려는 경우 App Service 구성 설정에서 JAVA_OPTS 변수를 설정하지 않도록 합니다. 


[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

## <a name="release-notes"></a>릴리스 정보

최신 업데이트 및 버그 수정에 대해서는 [릴리스 정보를 참조](web-app-extension-release-notes.md)하세요.

## <a name="next-steps"></a>다음 단계

* [Application Insights 를](monitor-functions.md)Azure Functions 모니터링합니다.
* [Azure 진단을 사용](../agents/diagnostics-extension-to-application-insights.md) 하여 Application Insights에 보냅니다.
* [서비스 상태 메트릭을 모니터링](../data-platform.md)하여 서비스를 사용 가능하며 응답할 수 있는 상태로 유지합니다.
* 작업 이벤트가 발생하거나 메트릭이 임계값을 초과할 때마다 [경고 알림을 수신](../alerts/alerts-overview.md)합니다.
* [JavaScript 앱 및 웹 페이지용 Application Insights](javascript.md)를 사용하여 웹 페이지로 이동하는 브라우저에서 클라이언트 원격 분석을 가져옵니다.
* [가용성 웹 테스트를 설정](monitor-web-app-availability.md) 합니다.