---
title: Application Insights에서 Azure Functions 원격 분석
description: Azure Application Insights에서 수집하고 여기에 저장된 Azure Functions 원격 분석 데이터를 보고 쿼리하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 10/14/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 530f38794e452df17aff6d7f0c890f8e9306d3e5
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113568499"
---
# <a name="analyze-azure-functions-telemetry-in-application-insights"></a>Application Insights에서 Azure Functions 원격 분석 

Azure Functions는 Application Insights와 통합되어 함수 앱을 보다 효율적으로 모니터링할 수 있습니다. Application Insights는 앱이 로그에 기록하는 정보 등 함수 앱에서 생성한 원격 분석 데이터를 수집합니다. Application Insights 통합은 일반적으로 함수 앱을 만들 때 사용하도록 설정됩니다. 함수 앱에 설정된 계측 키가 없으면 먼저 [Application Insights 통합을 사용하도록 설정](configure-monitoring.md#enable-application-insights-integration)해야 합니다. 

기본적으로 함수 앱에서 수집된 데이터는 Application Insights에 저장됩니다. [Azure Portal](https://portal.azure.com)에서 Application Insights는 원격 분석 데이터의 다양한 시각화 세트를 제공합니다. 오류 로그를 확인하고 이벤트와 메트릭을 쿼리할 수 있습니다. 이 문서에서는 수집된 데이터를 보고 쿼리하는 방법의 기본 예제를 제공합니다. Application Insights에서 함수 앱 데이터를 탐색하는 방법에 대해 자세한 내용은 [Application Insights란?](../azure-monitor/app/app-insights-overview.md)을 참조하세요. 

데이터 보존 및 잠재적 스토리지 비용에 대한 자세한 내용은 [Application Insights 데이터 수집, 보존 및 스토리지](../azure-monitor/app/data-retention-privacy.md)를 참조하세요.   

## <a name="viewing-telemetry-in-monitor-tab"></a>모니터 탭에서 원격 분석 보기

[Application Insights 통합을 사용](configure-monitoring.md#enable-application-insights-integration)하면 **모니터링** 탭에서 원격 분석 데이터를 볼 수 있습니다.

1. 함수 앱 페이지에서 Application Insights가 구성된 후 한 번 이상 실행된 함수를 선택합니다. 그런 다음, 왼쪽 창에서 **모니터링** 을 선택합니다. 함수 호출 목록이 나타날 때까지 **새로 고침** 을 주기적으로 선택합니다.

   ![호출 목록](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > 원격 분석 클라이언트 일괄 처리 데이터가 서버로 전송되기 때문에 이 목록이 표시되는 데 최대 5분 정도 걸릴 수 있습니다. [라이브 메트릭 스트림](../azure-monitor/app/live-stream.md)에는 이러한 지연 시간이 없습니다. 이 서비스는 페이지를 로드할 때 함수 호스트에 연결되므로 로그가 페이지에 직접 스트리밍됩니다.

1. 특정 함수 호출에 대한 로그를 보려면 해당 호출의 **날짜(UTC)** 열 링크를 선택합니다. 해당 호출에 대한 로깅 출력이 새 페이지에 나타납니다.

   ![호출 정보](media/functions-monitoring/invocation-details-ai.png)

1. Choose **Application Insights에서 실행** 을 선택하면 Azure Log에서 Azure Monitor 로그 데이터를 검색하는 쿼리 소스를 볼 수 있습니다. 구독에서 Azure Log Analytics를 처음 사용하는 경우 사용하도록 설정하라는 메시지가 표시됩니다.

1. Log Analytics를 사용하도록 설정하면 다음 쿼리가 표시됩니다. 쿼리 결과는 최근 30일(`where timestamp > ago(30d)`)로 제한되고 결과에는 행이 20개 이하(`take 20`)로 표시되는 것을 볼 수 있습니다. 반면, 함수의 호출 세부 정보 목록은 최근 30일의 정보가 제한 없이 제공됩니다.

   ![Application Insights 분석 호출 목록](media/functions-monitoring/ai-analytics-invocation-list.png)

자세한 내용은 이 문서의 뒷부분에 나오는 [원격 분석 데이터 쿼리](#query-telemetry-data)를 참조하세요.

## <a name="view-telemetry-in-application-insights"></a>Application Insights에서 원격 분석 보기

[Azure Portal](https://portal.azure.com)에서 함수 앱의 Application Insights를 열려면 다음을 수행합니다.

1. 포털에서 함수 앱을 탐색합니다.

1. 왼쪽 페이지의 **설정** 에서 **Application Insights** 를 선택합니다. 

1. 구독한 Application Insights를 처음 사용하는 경우 사용하도록 설정하라는 메시지가 표시됩니다. 이렇게 하려면 **Application Insights 켜기** 를 선택한 다음, 다음 페이지에서 **적용** 을 선택합니다.

![함수 앱 [개요] 페이지에서 Application Insights 열기](media/functions-monitoring/ai-link.png)

Application Insights 사용 방법에 대한 자세한 내용은 [Application Insights 설명서](/azure/application-insights/)를 참조하세요. 이 섹션에서는 Application Insights에서 데이터를 보는 방법에 대한 몇 가지 예를 보여줍니다. 이미 Application Insights에 익숙한 경우 [원격 분석 데이터를 구성하고 사용자 지정하는 방법에 대한 섹션](configure-monitoring.md#configure-log-levels)으로 바로 넘어가도 됩니다.

![Application Insights 개요 탭](media/functions-monitoring/metrics-explorer.png)

함수의 동작, 성능 및 오류를 평가할 때 Application Insights의 다음 영역을 유용하게 사용할 수 있습니다.

| 조사 | Description |
| ---- | ----------- |
| **[실패](../azure-monitor/app/asp-net-exceptions.md)** |  함수 오류 및 서버 예외를 기반으로 차트와 경고를 만듭니다. **작업 이름** 은 함수 이름입니다. 종속성에 대한 사용자 지정 원격 분석을 구현하지 않으면 종속성 오류가 표시되지 않습니다. |
| **[성능](../azure-monitor/app/performance-counters.md)** | **클라우드 역할 인스턴스** 당 리소스 사용률 및 처리량을 확인하여 성능 문제를 분석합니다. 이 성능 데이터는 함수로 인해 기본 리소스가 정체되는 시나리오를 디버깅하는 데 유용할 수 있습니다. |
| **[메트릭](../azure-monitor/essentials/metrics-charts.md)** | 메트릭 기반의 차트 및 경고를 만듭니다. 메트릭에는 함수 호출 수, 실행 시간 및 성공률이 포함됩니다. |
| **[라이브 메트릭](../azure-monitor/app/live-stream.md)** | 거의 실시간으로 생성되는 메트릭 데이터를 봅니다. |

## <a name="query-telemetry-data"></a>원격 분석 데이터 쿼리

[Application Insights Analytics](../azure-monitor/logs/log-query-overview.md)를 사용하면 데이터베이스의 모든 원격 분석 데이터를 테이블 형식으로 사용할 수 있습니다. Analytics는 데이터를 추출, 조작 및 시각화하는 쿼리 언어를 제공합니다. 

로깅된 이벤트를 살펴보거나 쿼리하려면 **로그** 를 선택합니다.

![Analytics 예제](media/functions-monitoring/analytics-traces.png)

다음 쿼리 예제는 지난 30분간의 작업자당 요청 분포를 보여줍니다.

```kusto
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

사용할 수 있는 테이블은 왼쪽의 **스키마** 탭에 표시됩니다. 다음 테이블에서 함수 호출에 의해 생성된 데이터를 찾을 수 있습니다.

| 테이블 | Description |
| ----- | ----------- |
| **traces** | 함수 코드의 런타임, 크기 조정 컨트롤러 및 추적에서 생성된 로그입니다. |
| **requests** | 함수가 호출될 때마다 요청 하나입니다. |
| **exceptions** | 런타임에서 throw하는 예외입니다. |
| **customMetrics** | 성공 및 실패 호출의 수, 성공률 및 기간입니다. |
| **customEvents** | 런타임이 추적하는 이벤트. 예: 함수를 트리거하는 HTTP 요청 |
| **performanceCounters** | 함수가 실행되고 있는 서버의 성능에 대한 정보입니다. |

나머지 테이블은 가용성 테스트와 클라이언트/브라우저 원격 분석용입니다. 사용자 지정 원격 분석을 구현하여 테이블에 데이터를 추가할 수 있습니다.

각 테이블 내에서 일부 Functions 관련 데이터는 `customDimensions` 필드에 있습니다.  예를 들어 다음 쿼리는 로그 수준이 `Error`인 모든 추적을 검색합니다.

```kusto
traces 
| where customDimensions.LogLevel == "Error"
```

런타임은 `customDimensions.LogLevel` 및 `customDimensions.Category` 필드를 제공합니다. 함수 코드에서 작성하는 로그에 추가 필드를 제공할 수 있습니다. C# 예제는 .NET 클래스 라이브러리 개발자 가이드의 [구조적 로깅](functions-dotnet-class-library.md#structured-logging)을 참조하세요.

## <a name="query-scale-controller-logs"></a>크기 조정 컨트롤러 로그 쿼리

‘이 기능은 미리 보기로 제공됩니다.’

[크기 조정 컨트롤러 로깅](configure-monitoring.md#configure-scale-controller-logs)과 [Application Insights 통합](configure-monitoring.md#enable-application-insights-integration)을 모두 사용하면 Application Insights 로그 검색을 사용하여 내보낸 크기 조정 컨트롤러 로그를 쿼리할 수 있습니다. 크기 조정 컨트롤러 로그는 **ScaleControllerLogs** 범주 아래의 `traces` 컬렉션에 저장됩니다.

다음 쿼리는 지정된 기간 내에 현재 함수 앱의 모든 크기 조정 컨트롤러 로그를 검색하는 데 사용될 수 있습니다.

```kusto
traces 
| extend CustomDimensions = todynamic(tostring(customDimensions))
| where CustomDimensions.Category == "ScaleControllerLogs"
```

다음 쿼리는 이전 쿼리를 확장하여 크기 조정 변경 내용을 나타내는 로그만 가져오는 방법을 보여줍니다.

```kusto
traces 
| extend CustomDimensions = todynamic(tostring(customDimensions))
| where CustomDimensions.Category == "ScaleControllerLogs"
| where message == "Instance count changed"
| extend Reason = CustomDimensions.Reason
| extend PreviousInstanceCount = CustomDimensions.PreviousInstanceCount
| extend NewInstanceCount = CustomDimensions.CurrentInstanceCount
```

## <a name="consumption-plan-specific-metrics"></a>사용 계획별 메트릭

[사용 계획](consumption-plan.md)에서 실행할 경우 단일 함수 실행의 실행 *비용* 은 *GB-초 단위* 로 측정됩니다. 실행 비용은 메모리 사용량과 실행 시간을 결합하여 계산됩니다. 자세한 내용은 [사용 계획 비용 예측](functions-consumption-costs.md)을 참조하세요.

다음 원격 분석 쿼리는 사용 계획에서 실행되는 함수의 비용에 영향을 주는 메트릭과 관련됩니다.

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>다음 단계

Azure Functions 모니터링에 대해 자세히 알아봅니다.

+ [Azure Functions 모니터링](functions-monitoring.md)
+ [Azure Functions에 대한 모니터링을 구성하는 방법](configure-monitoring.md)
