---
title: Azure Application Insights 앱 데이터 보기 | Microsoft Docs
description: Application Insights 커넥터 솔루션을 사용하여 성능 문제를 진단하고 Application Insights를 통해 모니터링하는 경우 사용자가 해당 앱으로 수행하는 작업을 파악할 수 있습니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a59d2e1931b07e035d85632cf84803989de14871
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108320730"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Application Insights 커넥터 관리 솔루션(사용되지 않음)

![Application Insights 기호](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> [리소스 간 쿼리](../logs/cross-workspace-query.md)가 지원되므로 Application Insights 커넥터 관리 솔루션이 더 이상 필요하지 않습니다. 사용되지 않으며, Azure 상용 클라우드에 대해 2019년 1월 15일에 공식적으로 사용 중단된 OMS 포털과 함께 Azure Marketplace에서 제거되었습니다. Azure 미국 정부 클라우드의 경우 2019년 3월 30일에 사용 중지됩니다.
>
>기존 연결은 2019년 6월 30일까지 계속 작동합니다.  OMS 포털 사용 중단으로 포털에서 기존 연결을 구성하고 제거할 방법이 없습니다. PowerShell을 사용하여 기존 연결을 제거하는 스크립트는 아래의 [PowerShell을 사용하여 커넥터 제거](#removing-the-connector-with-powershell)를 참조하세요.
>
>여러 애플리케이션에 대한 Application Insights 로그 데이터를 쿼리하는 방법에 대한 지침은 [Azure Monitor Application Insights 리소스 통합](./unify-app-resource-data.md)을 참조하세요. OMS 포털 지원 중단에 대한 자세한 내용은 [Azure로 이동하는 OMS 포털](./oms-portal-transition.md)을 참조하세요.
>
> 

Application Insights 커넥터 솔루션은 성능 문제를 진단하고 [Application Insights](../app/app-insights-overview.md)를 통해 모니터링하는 경우 사용자가 해당 앱으로 수행하는 작업을 파악하는 데 도움이 됩니다. 개발자가 Application Insights에서 참조하는 것과 동일한 애플리케이션 원격 분석 뷰를 Log Analytics에서 사용할 수 있습니다. 그러나 Log Analytics와 Application Insights 앱을 통합하는 경우 작업 및 애플리케이션 데이터가 한 곳에 있게 되므로 애플리케이션에 대한 가시성이 향상됩니다. 동일한 뷰가 있으면 앱 개발자와 공동 작업을 수행하는 데 도움이 됩니다. 공통 뷰는 애플리케이션 및 플랫폼 문제를 감지하고 해결하는 데 소요되는 시간을 줄일 수 있습니다.

이 솔루션을 사용하면 다음을 수행할 수 있습니다.

- Application Insights 앱이 서로 다른 Azure 구독에 있는 경우에도 모두 한 곳에서 보기
- 애플리케이션 데이터와 인프라 데이터 연결
- 로그 검색에서 큐브 뷰로 애플리케이션 데이터 시각화
- Azure Portal의 Log Analytics 데이터에서 Application Insights 앱으로 피벗


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>연결된 소스

대부분의 다른 Log Analytics 솔루션과 달리 Application Insights 커넥터에 대해서는 데이터가 에이전트별로 수집되지 않습니다. 솔루션에서 사용하는 모든 데이터는 Azure에서 직접 옵니다.

| 연결된 소스 | 지원됨 | Description |
| --- | --- | --- |
| [Windows 에이전트](./../agents/agent-windows.md) | 아니요 | 솔루션이 Windows 에이전트에서 정보를 수집하지 않습니다. |
| [Linux 에이전트](../vm/quick-collect-linux-computer.md) | 아니요 | 솔루션이 Linux 에이전트에서 정보를 수집하지 않습니다. |
| [SCOM 관리 그룹](../agents/om-agents.md) | 아니요 | 솔루션이 연결된 SCOM 관리 그룹의 에이전트에서 정보를 수집하지 않습니다. |
| [Azure Storage 계정](../essentials/resource-logs.md#send-to-log-analytics-workspace) | 아니요 | 솔루션이 Azure Storage에서 정보를 수집하지 않습니다. |

## <a name="prerequisites"></a>사전 요구 사항

- Application Insights 커넥터 정보에 액세스하려면 Azure 구독이 있어야 합니다.
- 구성된 Application Insights 리소스가 하나 이상 있어야 합니다.
- Application Insights 리소스 소유자 또는 참가자여야 합니다.

## <a name="configuration"></a>구성

1. [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps)에서 또는 [솔루션 갤러리에서 Log Analytics 솔루션 추가](../insights/solutions.md)에서 설명한 프로세스를 사용하여 Azure Web Apps 분석 솔루션을 사용하도록 설정합니다.
2. [Azure Portal](https://portal.azure.com)로 이동합니다. **모든 서비스** 를 선택하여 Application Insights를 엽니다. 그런 다음, Application Insights를 검색합니다. 
3. **구독** 에서 Application Insights 리소스가 포함된 구독을 선택한 다음, **이름** 에서 하나 이상의 애플리케이션을 선택합니다.
4. **저장** 을 클릭합니다.

약 30분 내에 데이터가 제공되며 다음 이미지와 같이 Application Insights 타일이 데이터로 업데이트됩니다.

![Application Insights 타일](./media/app-insights-connector/app-insights-tile.png)

기타 주의할 사항은 다음과 같습니다.

- 하나의 Log Analytics 작업 영역에만 Application Insights 앱을 연결할 수 있습니다.
- [기본 또는 엔터프라이즈 Application Insights 리소스](https://azure.microsoft.com/pricing/details/application-insights)만 Log Analytics에 연결할 수 있습니다. 그러나 Log Analytics의 무료 계층을 사용할 수 있습니다.

## <a name="management-packs"></a>관리 팩

이 솔루션은 연결된 관리 그룹에서 관리 팩을 설치하지 않습니다.

## <a name="use-the-solution"></a>솔루션 사용

다음 섹션에서는 Application Insights 대시보드에 표시된 블레이드를 사용하여 앱의 데이터를 보고 상호 작용하는 방법을 설명합니다.

### <a name="view-application-insights-connector-information"></a>Application Insights 커넥터 정보 보기

**Application Insights** 타일을 클릭하여 **Application Insights** 대시보드를 열고 다음 블레이드를 표시합니다.

![애플리케이션, 데이터 볼륨 및 가용성에 대한 블레이드를 보여주는 Application Insights 대시보드의 스크린샷.](./media/app-insights-connector/app-insights-dash01.png)

![서버 요청, 오류 및 예외에 대한 블레이드를 보여주는 Application Insights 대시보드의 스크린샷.](./media/app-insights-connector/app-insights-dash02.png)

표에 표시된 블레이드가 대시보드에 포함되어 있습니다. 각 블레이드에는 지정된 범위 및 시간 범위에 대한 해당 블레이드의 기준과 일치하는 항목이 최대 10개까지 나열됩니다. 블레이드 맨 아래에서 **모두 보기** 를 클릭하거나 블레이드 헤더를 클릭하면 모든 레코드를 반환하는 로그 검색을 실행할 수 있습니다.


| **열** | **설명** |
| --- | --- |
| 애플리케이션 - 애플리케이션 수 | 애플리케이션 리소스에 애플리케이션 수를 표시합니다. 애플리케이션 이름과 각 항목의 애플리케이션 레코드 수도 나열합니다. <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code>에 대한 로그 검색을 실행할 번호를 클릭합니다. <br><br>  호스트별 애플리케이션 레코드, 원격 분석 유형별 레코드 및 형식별 모든 데이터(마지막 날 기준)를 보여주는 애플리케이션에 대한 로그 검색을 실행할 애플리케이션 이름을 클릭합니다. |
| 데이터 볼륨 - 데이터를 전송하는 호스트 | 데이터를 전송하는 컴퓨터 호스트의 수를 보여 줍니다. 컴퓨터 호스트 및 각 호스트의 레코드 수도 나열합니다. <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code>에 대한 로그 검색을 실행할 번호를 클릭합니다. <br><br> 호스트별 애플리케이션 레코드, 원격 분석 유형별 레코드 및 형식별 모든 데이터(마지막 날 기준)를 보여주는 호스트에 대한 로그 검색을 실행할 컴퓨터 이름을 클릭합니다. |
| 가용성 - 웹 테스트 결과 | 웹 테스트 결과를 성공 또는 실패로 나타내는 도넛형 차트를 보여 줍니다. 차트를 클릭하면 <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code>에 대한 로그 검색이 실행됩니다. <br><br> 결과는 모든 테스트의 성공 및 실패 수를 보여 줍니다. 마지막 1분 동안 트래픽이 발생한 모든 Web Apps가 표시됩니다. 실패한 웹 테스트에 대한 세부 정보를 보여주는 로그 검색을 보려면 애플리케이션 이름을 클릭합니다. |
| 서버 요청 – 시간당 요청 | 다양한 애플리케이션의 시간당 서버 요청에 대한 꺾은선형 차트를 보여줍니다. 차트의 선을 마우스로 가리키면 특정 시점에 요청을 가장 많이 수신한 상위 3개 애플리케이션이 표시됩니다. 요청을 수신한 애플리케이션 목록과 선택한 기간의 요청 수도 보여줍니다. <br><br>다양한 애플리케이션의 시간당 서버 요청에 대한 더 자세한 꺾은선형 차트를 보여주는 <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code>에 대한 로그 검색을 실행하려면 그래프를 클릭합니다. <br><br> 요청 목록, 시간 및 요청 기간에 따른 요청 차트, 요청 응답 코드 목록을 보여주는 <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code>에 대한 로그 검색을 실행하려면 목록에서 애플리케이션을 클릭합니다.   |
| 실패 - 시간당 실패한 요청 수 | 시간당 실패한 애플리케이션 요청 수에 대한 꺾은선형 차트를 보여줍니다. 차트를 마우스로 가리키면 특정 시점에 실패한 요청이 가장 많은 상위 3개 애플리케이션이 표시됩니다. 각 애플리케이션의 실패한 요청 수가 표시된 애플리케이션 목록도 보여줍니다. 실패한 애플리케이션 요청을 자세한 꺾은선형 차트로 보여주는 <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code>에 대한 로그 검색을 실행하려면 차트를 클릭합니다. <br><br>실패한 요청, 시간 및 요청 기간에 따른 실패한 요청 차트, 실패한 요청 응답 코드 목록을 보여 주는 <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code>에 대한 로그 검색을 실행하려면 목록에서 항목을 클릭합니다. |
| 예외 – 시간당 예외 | 시간당 예외에 대한 꺾은선형 차트를 보여 줍니다. 차트를 마우스로 가리키면 특정 시점에 예외가 가장 많은 상위 3개 애플리케이션이 표시됩니다. 각 애플리케이션의 예외 수가 표시된 애플리케이션 목록도 보여줍니다. 예외에 대한 더 자세한 연결 차트를 보여 주는 <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code>에 대한 로그 검색을 실행하려면 차트를 클릭합니다. <br><br>예외 목록, 시간 및 실패한 요청에 따른 예외 차트, 예외 형식 목록을 보여 주는 <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code>에 대한 로그 검색을 실행하려면 목록에서 항목을 클릭합니다.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>로그 검색을 통해 Application Insights 큐브 뷰 보기

대시보드에서 임의의 항목을 클릭하면 검색에 Application Insights 큐브 뷰가 표시됩니다. 큐브 뷰는 선택한 원격 분석 유형에 따라 확장된 시각화를 제공합니다. 따라서 원격 분석 유형에 따라 시각화 콘텐츠가 변경됩니다.

애플리케이션 블레이드에서 아무 곳이나 클릭하면 기본 **애플리케이션** 큐브 뷰가 표시됩니다.

![Application Insights 애플리케이션 큐브 뷰](./media/app-insights-connector/applications-blade-drill-search.png)

큐브 뷰는 선택한 애플리케이션에 대한 개요를 보여줍니다.

**가용성** 블레이드는 웹 테스트 결과와 관련된 실패한 요청을 볼 수 있는 다양한 큐브 뷰를 보여 줍니다.

![Application Insights 가용성 큐브 뷰](./media/app-insights-connector/availability-blade-drill-search.png)

**서버 요청** 또는 **실패** 블레이드에서 아무 곳이나 클릭하면 큐브 뷰 구성 요소가 변경되어 요청과 관련된 시각화를 제공합니다.

![Application Insights 실패 블레이드](./media/app-insights-connector/server-requests-failures-drill-search.png)

**예외** 블레이드에서 아무 곳이나 클릭하면 예외에 맞는 시각화가 표시됩니다.

![Application Insights 예외 블레이드](./media/app-insights-connector/exceptions-blade-drill-search.png)

항목을 클릭한 위치가 **Application Insights 커넥터** 대시보드인지, **검색** 페이지 내인지 상관없이 Application Insights 데이터를 반환하는 쿼리는 Application Insights 큐브 뷰를 보여줍니다. 예를 들어 Application Insights 데이터를 보고 있는 경우 **&#42;** 쿼리도 다음 이미지와 같은 큐브 뷰 탭을 보여 줍니다.

![Application Insights](./media/app-insights-connector/app-insights-search.png)

큐브 뷰 구성 요소는 검색 쿼리에 따라 업데이트됩니다. 즉, 데이터를 볼 수 있는 기능을 제공하는 검색 필드를 사용하여 결과를 필터링할 수 있습니다.

- 모든 애플리케이션
- 선택된 단일 애플리케이션
- 애플리케이션 그룹

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Azure Portal에서 앱에 피벗

Application Insights 커넥터 블레이드는 사용자가 *Azure Portal을 사용하는 경우* 선택한 Application Insights 앱에 피벗할 수 있도록 설계되었습니다. 솔루션을 앱 문제 해결에 도움이 되는 상위 수준 모니터링 플랫폼으로 사용할 수 있습니다. 연결된 애플리케이션에서 잠재적인 문제가 발견되면 Log Analytics 검색에서 자세히 알아보거나 Application Insights 앱에 직접 피벗할 수 있습니다.

피벗하려면 각 행의 끝에 표시되는 줄임표(**…**)를 클릭하고 **Open in Application Insights**(Application Insights에서 열기)를 선택합니다.

>[!NOTE]
>Azure Portal에서는 **Open in Application Insights**(Application Insights에서 열기)를 사용할 수 없습니다.

![Application Insights에서 열기](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>샘플 수정 데이터

Application Insights는 *[샘플링 수정](../app/sampling.md)* 을 제공하므로 원격 분석 트래픽을 줄이는 데 도움이 됩니다. Application Insights 앱에서 샘플링을 사용하도록 설정하는 경우 Application Insights 및 Log Analytics 모두에서 저장되는 항목의 수가 감소됩니다. **Application Insights 커넥터** 페이지 및 큐브 뷰에서 데이터 일관성이 유지되지만 사용자 지정 쿼리를 위해 샘플링된 데이터는 수동으로 수정해야 합니다.

다음은 로그 검색 쿼리의 샘플링 수정 예입니다.

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

**Sampled Count**(샘플링된 수) 필드는 모든 항목에 있으며 항목이 나타내는 데이터 요소 수를 보여 줍니다. Application Insights 앱에 대해 샘플링이 설정된 경우 **Sampled Count**(샘플링된 수)가 1보다 큽니다. 애플리케이션에서 생성하는 실제 항목 수를 계산하려면 **Sampled Count**(샘플링된 수) 필드를 합산합니다.

샘플링은 애플리케이션에서 생성하는 총 항목 수에만 영향을 줍니다. **RequestDuration** 또는 **AvailabilityDuration** 같은 메트릭 필드는 표시된 항목의 평균을 보여 주므로 해당 필드에 대한 샘플링은 수정할 필요가 없습니다.

## <a name="input-data"></a>입력 데이터

솔루션은 연결된 Application Insights 앱에서 다음과 같은 원격 분석 유형의 데이터를 수신합니다.

- 가용성
- 예외
- 요청
- 페이지 보기 - 작업 영역에서 페이지 보기를 받으려면 앱에서 해당 정보를 수집하도록 구성해야 합니다. 자세한 내용은 [PageViews](../app/api-custom-events-metrics.md#page-views)를 참조하세요.
- 사용자 지정 이벤트 - 작업 영역에서 사용자 지정 이벤트를 받으려면 앱에서 해당 정보를 수집하도록 구성해야 합니다. 자세한 내용은 [TrackEvent](../app/api-custom-events-metrics.md#trackevent)를 참조하세요.

데이터를 사용할 수 있게 되면 Application Insights에서 Log Analytics로 수신됩니다.

## <a name="output-data"></a>출력 데이터

각 형식의 입력 데이터에 대해 *종류* 가 *ApplicationInsights* 인 레코드가 만들어집니다. ApplicationInsights 레코드에는 다음 섹션에 표시된 속성이 포함되어 있습니다.

### <a name="generic-fields"></a>일반 필드

| 속성 | Description |
| --- | --- |
| 형식 | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | 레코드 시간 |
| ApplicationId | Application Insights 앱의 계측 키 |
| ApplicationName | Application Insights 앱의 이름 |
| RoleInstance | 서버 호스트의 ID |
| DeviceType | 클라이언트 디바이스 |
| ScreenResolution |   |
| Continent | 요청이 시작된 대륙 |
| 국가 | 요청이 시작된 국가/지역 |
| Province | 요청이 시작된 시/도 또는 로캘 |
| 구/군/시 | 요청이 시작된 구/군/시 또는 동/면 |
| isSynthetic | 요청이 사용자에 의해 만들어졌는지 자동화된 방법을 통해 만들어졌는지 나타냅니다. True =자동화된 방법 또는 false = 사용자가 생성함 |
| SamplingRate | 포털에 전송되는 SDK에 의해 생성된 원격 분석의 비율입니다. 범위는 0.0-100.0입니다. |
| SampledCount | 100/(SamplingRate)입니다. 예를 들어 4 =&gt; 25%입니다. |
| IsAuthenticated | True 또는 False |
| OperationID | 작업 ID가 동일한 항목은 포털에서 관련 항목으로 표시됩니다. 일반적으로 요청 ID입니다. |
| ParentOperationID | 부모 작업 ID |
| OperationName |   |
| SessionId | 요청이 만들어진 세션을 고유하게 식별하는 GUID |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>가용성 관련 필드

| 속성 | Description |
| --- | --- |
| TelemetryType | 가용성 |
| AvailabilityTestName | 웹 테스트의 이름 |
| AvailabilityRunLocation | Http 요청의 지리적 소스 |
| AvailabilityResult | 웹 테스트의 성공 결과 표시 |
| AvailabilityMessage | 웹 테스트에 연결된 메시지 |
| AvailabilityCount | 100/(샘플링 속도)입니다. 예를 들어 4 =&gt; 25%입니다. |
| DataSizeMetricValue | 1.0 또는 0.0 |
| DataSizeMetricCount | 100/(샘플링 속도)입니다. 예를 들어 4 =&gt; 25%입니다. |
| AvailabilityDuration | 웹 테스트 기간 시간(밀리초) |
| AvailabilityDurationCount | 100/(샘플링 속도)입니다. 예를 들어 4 =&gt; 25%입니다. |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | 웹 테스트의 고유 GUID |
| AvailabilityTimestamp | 정확한 가용성 테스트 타임스탬프 |
| AvailabilityDurationMin | 샘플링된 레코드의 경우 이 필드는 표시된 데이터 요소의 최소 웹 테스트 기간(밀리초)을 보여 줍니다. |
| AvailabilityDurationMax | 샘플링된 레코드의 경우 이 필드는 표시된 데이터 요소의 최대 웹 테스트 기간(밀리초)을 보여 줍니다. |
| AvailabilityDurationStdDev | 샘플링된 레코드의 경우 이 필드는 표시된 데이터 요소의 모든 웹 테스트 기간(밀리초) 사이의 표준 편차를 보여 줍니다. |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>예외 관련 필드

| 형식 | ApplicationInsights |
| --- | --- |
| TelemetryType | 예외 |
| ExceptionType | 예외 형식 |
| ExceptionMethod | 예외를 만드는 메서드 |
| ExceptionAssembly | 어셈블리에는 공개 키 토큰뿐만 아니라 프레임워크와 버전 이 포함됩니다. |
| ExceptionGroup | 예외 형식 |
| ExceptionHandledAt | 예외를 처리한 수준 표시 |
| ExceptionCount | 100/(샘플링 속도)입니다. 예를 들어 4 =&gt; 25%입니다. |
| ExceptionMessage | 예외 메시지 |
| ExceptionStack | 예외의 전체 스택 |
| ExceptionHasStack | 예외에 스택이 있는 경우 True |



### <a name="request-specific-fields"></a>요청 관련 필드

| 속성 | Description |
| --- | --- |
| 형식 | ApplicationInsights |
| TelemetryType | 요청 |
| ResponseCode | 클라이언트에 보낸 HTTP 응답 |
| RequestSuccess | 성공 또는 실패를 표시합니다. True 또는 False입니다. |
| RequestID | 요청을 고유하게 식별하는 ID |
| RequestName | GET/POST + URL 기본 |
| RequestDuration | 요청 기간 시간(초) |
| URL | 호스트를 포함하지 않는 요청의 URL |
| 호스트 | 웹 서버 호스트 |
| URLBase | 요청의 전체 URL |
| ApplicationProtocol | 애플리케이션에서 사용되는 프로토콜의 유형 |
| RequestCount | 100/(샘플링 속도)입니다. 예를 들어 4 =&gt; 25%입니다. |
| RequestDurationCount | 100/(샘플링 속도)입니다. 예를 들어 4 =&gt; 25%입니다. |
| RequestDurationMin | 샘플링된 레코드의 경우 이 필드는 표시된 데이터 요소의 최소 요청 기간(밀리초)을 보여 줍니다. |
| RequestDurationMax | 샘플링된 레코드의 경우 이 필드는 표시된 데이터 요소의 최대 요청 기간(밀리초)을 보여 줍니다. |
| RequestDurationStdDev | 샘플링된 레코드의 경우 이 필드는 표시된 데이터 요소의 모든 요청 기간(밀리초) 사이의 표준 편차를 보여 줍니다. |

## <a name="sample-log-searches"></a>샘플 로그 검색

이 솔루션에는 대시보드에 표시되는 샘플 로그 검색 집합이 없습니다. 그러나 설명이 포함된 샘플 로그 검색 쿼리가 [Application Insights 커넥터 정보 보기](#view-application-insights-connector-information) 섹션에 표시됩니다.

## <a name="removing-the-connector-with-powershell"></a>PowerShell을 사용하여 커넥터 제거
OMS 포털 사용 중단으로 포털에서 기존 연결을 구성하고 제거할 방법이 없습니다. 다음 PowerShell 스크립트를 사용하여 기존 연결을 제거할 수 있습니다. 이 작업을 수행하려면 작업 영역의 owner 또는 contributor이고 Application Insights 리소스의 reader여야 합니다.

```powershell
$Subscription_app = "App Subscription Name"
$ResourceGroup_app = "App ResourceGroup"
$Application = "Application Name"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"

Connect-AzAccount
Set-AzContext -SubscriptionId $Subscription_app
$AIApp = Get-AzApplicationInsights -ResourceGroupName $ResourceGroup_app -Name $Application 
Set-AzContext -SubscriptionId $Subscription_workspace
Remove-AzOperationalInsightsDataSource -WorkspaceName $Workspace -ResourceGroupName $ResourceGroup_workspace -Name $AIApp.Id
```

REST API 호출을 호출하는 다음 PowerShell 스크립트를 사용하여 애플리케이션 목록을 검색할 수 있습니다. 

```powershell
Connect-AzAccount
$Tenant = "TenantId"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"
$AccessToken = "AAD Authentication Token" 

Set-AzContext -SubscriptionId $Subscription_workspace
$LAWorkspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $ResourceGroup_workspace -Name $Workspace

$Headers = @{
    "Authorization" = "Bearer $($AccessToken)"
    "x-ms-client-tenant-id" = $Tenant
}

$Connections = Invoke-RestMethod -Method "GET" -Uri "https://management.azure.com$($LAWorkspace.ResourceId)/dataSources/?%24filter=kind%20eq%20'ApplicationInsights'&api-version=2015-11-01-preview" -Headers $Headers
$ConnectionsJson = $Connections | ConvertTo-Json
```
이 스크립트가 Azure Active Directory에 인증하려면 전달자 인증 토큰이 필요합니다. 이 토큰을 검색하는 한 가지 방법은 [REST API 문서 사이트](/rest/api/loganalytics/datasources/createorupdate)의 문서를 사용하는 것입니다. **사용해 보기** 를 클릭하고 Azure 구독에 로그인합니다. 다음 이미지와 같이 **요청 미리 보기** 에서 전달자 토큰을 복사할 수 있습니다.


![전달자 토큰](media/app-insights-connector/bearer-token.png)


로그 쿼리를 사용하여 애플리케이션 목록을 검색할 수도 있습니다.

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>다음 단계

- Application Insights 앱에 대한 자세한 정보를 보려면 [로그 검색](./log-query-overview.md)을 사용합니다.
