---
title: 진단 및 해결 도구
description: Azure Portal의 진단 및 해결 도구를 사용하여 Azure App Service에서 앱 문제를 해결하는 방법에 대해 알아봅니다.
keywords: App Service, Azure App Service, 진단, 지원, 웹앱, 문제 해결, 자가 진단
ms.topic: article
ms.date: 10/18/2019
ms.custom: seodec18
ms.openlocfilehash: cf044baf233c159ff271221522f0f9faf15cc1ed
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114451591"
---
# <a name="azure-app-service-diagnostics-overview"></a>Azure App Service 진단 개요

웹 애플리케이션을 실행할 때 500개 오류부터 사용자가 사이트의 작동이 중단되었음을 알리는 사용자에 이르기까지 발생할 수 있는 문제에 대비해야 합니다. App Service 진단은 구성할 필요 없이 앱 문제를 해결하는 데 도움이 되는 지능적인 대화형 환경입니다. 앱에서 문제가 발생하면 App Service 진단은 무엇이 문제인지를 표시하여 문제를 더 쉽고 빠르게 확인하고 해결하기 위한 올바른 정보로 안내합니다.

이 환경은 지난 24시간 동안 앱에 문제가 있을 때 가장 유용하지만, 언제든지 분석할 수 있도록 모든 진단 그래프가 제공됩니다.

App Service 진단은 Windows의 앱 뿐만 아니라 [Linux/컨테이너](./overview.md#app-service-on-linux), [App Service 환경](./environment/intro.md) 및 [Azure Functions](../azure-functions/functions-overview.md)의 앱에도 작용합니다.

## <a name="open-app-service-diagnostics"></a>App Service 진단 열기

App Service 진단에 액세스하려면 [Azure Portal](https://portal.azure.com)의 App Service 웹앱 또는 App Service Environment로 이동합니다. 왼쪽 탐색 메뉴에서 **문제 진단 및 해결** 을 클릭합니다.

Azure Functions의 경우 함수 앱으로 이동한 후 위쪽 탐색 메뉴에서 **플랫폼 기능** 을 클릭하고 **리소스 관리** 섹션에서 **진단 및 문제 해결** 을 선택합니다.

App Service 진단 홈페이지에서 각 홈페이지 타일의 키워드를 사용하여 앱에 대한 문제를 가장 잘 설명하는 범주를 선택할 수 있습니다. 또한 이 페이지에서는 **진단 도구** 를 찾을 수 있습니다. [진단 도구](#diagnostic-tools)를 참조하세요.

![홈페이지](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

> [!NOTE]
> 앱이 다운되거나 느리게 수행되는 경우 [프로파일링 추적을 수집](https://azure.github.io/AppService/2018/06/06/App-Service-Diagnostics-Profiling-an-ASP.NET-Web-App-on-Azure-App-Service.html)하여 문제의 근본 원인을 식별할 수 있습니다. 프로파일링은 경량이며 프로덕션 시나리오를 위해 설계되었습니다.
>

## <a name="interactive-interface"></a>대화형 인터페이스

앱의 문제에 가장 잘 맞는 홈페이지 범주를 선택하면 Genie라는 App Service 진단의 대화형 인터페이스를 사용하여 앱의 문제를 진단하고 해결하는 과정을 안내받을 수 있습니다. Genie에서 제공하는 타일 바로 가기를 사용하여 관심 있는 문제 범주의 전체 진단 보고서를 볼 수 있습니다. 타일 바로 가기를 통해 진단 메트릭에 직접 액세스할 수 있습니다.

![타일 바로 가기](./media/app-service-diagnostics/tile-shortcuts-2.png)

이러한 타일을 클릭한 후 타일에 설명된 문제와 관련된 토픽의 목록을 볼 수 있습니다. 이러한 토픽에서는 전체 보고서에서 주목할 만한 정보 조각을 제공합니다. 이러한 토픽 중 하나를 클릭하여 문제를 자세히 조사할 수 있습니다. 또한 **전체 보고서 보기** 를 클릭하여 단일 페이지의 모든 토픽을 탐색할 수 있습니다.

![토픽](./media/app-service-diagnostics/application-logs-insights-3.png)

![전체 보고서 보기](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>진단 보고서

토픽을 클릭하여 문제를 자세히 조사하도록 선택한 후 종종 그래프 및 마크다운으로 보완되는 토픽에 대한 세부 정보를 볼 수 있습니다. 진단 보고서는 앱 문제를 정확히 찾아내는 강력한 도구가 될 수 있습니다.

![진단 보고서](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>상태 검사

앱 문제를 알지 못하거나 문제 해결을 시작할 수 있는 위치를 모르는 경우 상태 검사를 시작하는 것이 좋습니다. 상태 검사는 애플리케이션을 분석하여 정상 및 비정상 문제를 지적하고 해당 문제를 조사할 위치를 알려주는 대화형 개요를 빠르게 제공합니다. 지능적인 대화형 인터페이스는 문제 해결 프로세스를 통해 지침을 제공합니다. 상태 검사는 Windows 앱에 대한 Genie 환경 및 Linux 앱에 대한 웹앱 다운 진단 보고서와 통합됩니다.

### <a name="health-checkup-graphs"></a>상태 검사 그래프

상태 검사에는 서로 다른 네 가지 그래프가 있습니다.

- **요청 및 오류:** HTTP 서버 오류와 함께 최근 24시간 동안 수행된 요청 수를 표시하는 그래프입니다.
- **앱 성능:** 여러 백분위수 그룹의 최근 24시간 동안 응답 시간을 표시하는 그래프입니다.
- **CPU 사용량:** 최근 24시간 동안 인스턴스당 전체 CPU 사용량 비율을 표시하는 그래프입니다.  
- **메모리 사용량:** 최근 24시간 동안 인스턴스당 전체 실제 메모리 사용량 비율을 표시하는 그래프입니다.

![상태 검사](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>애플리케이션 코드 문제 조사(Windows 앱에만 해당)

많은 앱 문제가 애플리케이션 코드의 문제와 관련되어 있으므로 App Service 진단은 [Application Insights](../azure-monitor/app/app-insights-overview.md)와 통합되어 예외 및 종속성 문제를 강조 표시하고, 선택된 작동 중단 시간과의 상관 관계를 표시합니다. Application Insights는 별도로 사용하도록 설정해야 합니다.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Application Insights 예외 및 종속성을 보려면 **웹앱 다운** 또는 **웹앱 저속** 타일 바로 가기를 선택합니다.

### <a name="troubleshooting-steps-only-for-windows-app"></a>문제 해결 단계(Windows 앱에만 해당)

지난 24시간 동안 특정 문제 범주에 속한 문제가 검색되면, 전체 진단 보고서를 볼 수 있으며, App Service 진단에서 문제 해결에 대한 자세한 권장 사항과 자세한 단계별 환경의 다음 단계를 확인하도록 요구할 수 있습니다.

![Application Insights 및 문제 해결 그리고 다음 단계](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools"></a>진단 도구 

진단 도구에는 애플리케이션 코드 문제, 속도 저하, 연결 문자열 등을 조사하는 데 도움이 되는 고급 진단 도구 및 CPU 사용량, 요청 및 메모리와 관련된 문제를 완화하는 데 도움이 되는 자동 관리 도구가 포함되어 있습니다.

### <a name="proactive-cpu-monitoring-only-for-windows-app"></a>자동 관리 CPU 모니터링(Windows 앱에만 해당)

앱 또는 앱에 대한 자식 프로세스가 높은 CPU 리소스를 사용하는 경우 자동 관리 CPU 모니터링을 통해 간편한 자동 관리 방식으로 작업을 수행할 수 있습니다. 예기치 않은 문제에 대한 실제 원인이 발견될 때까지 높은 CPU 문제를 일시적으로 완화하도록 사용자 고유의 CPU 임계값 규칙을 설정할 수 있습니다. 자세한 내용은 [CPU 문제가 발생하기 전에 완화](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html)를 참조하세요.

![자동 관리 CPU 모니터링](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing"></a>자동 복구 

자동 복구는 앱에 예기치 않은 동작이 있을 때 수행할 수 있는 완화 작업입니다. 요청 수, 저속 요청, 메모리 제한 및 HTTP 상태 코드에 따라 사용자 고유의 규칙을 설정하여 완화 작업을 트리거할 수 있습니다. 이 도구를 사용하여 근본 원인을 찾을 때까지 예기치 않은 동작을 일시적으로 완화할 수 있습니다. 이 도구는 현재 Windows Web Apps, Linux Web Apps 및 Linux 사용자 지정 컨테이너에서 사용할 수 있습니다. 지원되는 조건 및 완화는 웹앱의 유형에 따라 달라집니다. 자세한 내용은 [앱 서비스 진단에서 새로운 자동 복구 환경 발표](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html) 및 [Linux용 자동 복구 발표](https://azure.github.io/AppService/2021/04/21/Announcing-Autoheal-for-Azure-App-Service-Linux.html)를 참조하세요.

![자동 복구](./media/app-service-diagnostics/auto-healing-10.png)

### <a name="proactive-auto-healing-only-for-windows-app"></a>자동 관리 자동 복구(Windows 앱에만 해당)

자동 관리 CPU 모니터링과 마찬가지로 자동 관리 자동 복구는 앱의 예기치 않은 동작을 완화하기 위한 턴키 방식의 솔루션입니다. App Service에서 앱이 복구할 수 없는 상태라고 판단하면 자동 관리 자동 복구가 앱을 다시 시작합니다. 자세한 내용은 [자동 관리 자동 복구 소개](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html)를 참조하세요.

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>탐색기 및 변경 분석(Windows 앱에만 해당)

연속 통합을 사용하고 앱에 많은 종속성이 있는 규모가 큰 팀에서 비정상 동작을 야기하는 특정 변경 내용을 정확하게 파악하기 어려울 수 있습니다. 탐색기는 앱의 종속성 맵과 동일한 구독의 모든 리소스를 자동으로 렌더링하여 앱의 토폴로지에 대한 가시성을 얻는 데 유용합니다. 탐색기를 사용하면 앱 및 해당 종속성에 의해 적용된 변경 내용 목록을 보고 비정상 동작을 야기하는 변경 내용에 대해 범위를 좁힐 수 있습니다. 홈 페이지 타일 **탐색기** 를 통해 액세스할 수 있으며 처음 사용하기 전에 활성화해야 합니다. 자세한 내용은 [탐색기를 사용하여 앱의 종속성에 대한 가시성 확보](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html)를 참조하세요.

![탐색기 기본 페이지](./media/app-service-diagnostics/navigator-default-page-11.png)

![Diff 뷰](./media/app-service-diagnostics/diff-view-12.png)

앱 변경 내용에 대한 변경 분석은 타일 바로 가기인 **가용성 및 성능** 의 **애플리케이션 변경 내용** 및 **애플리케이션 크래시** 를 통해 액세스할 수 있으므로 다른 메트릭과 동시에 사용할 수 있습니다. 기능을 사용하려면 먼저 해당 기능을 사용하도록 설정해야 합니다. 자세한 내용은 [App Service 진단에서 새로운 변경 분석 환경 발표](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html)를 참조하세요.

제목에 "[Diag]"를 추가하여 [UserVoice](https://feedback.azure.com/forums/169385-web-apps)에 질문이나 피드백을 게시합니다.
