---
title: Azure Monitor의 메트릭 | Microsoft Docs
description: 거의 실시간으로 시나리오를 지원할 수 있는 경량 모니터링 데이터인 Azure Monitor의 메트릭을 설명합니다.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2021
ms.author: bwren
ms.openlocfilehash: 00014d4a7f2829ec6836473d03ae0b25f388a903
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286469"
---
# <a name="azure-monitor-metrics-overview"></a>Azure Monitor 메트릭 개요
Azure Monitor 메트릭은 [모니터링된 리소스](../monitor-reference.md)에서 시계열 데이터베이스로 숫자 데이터를 수집하는 Azure Monitor의 기능입니다. 메트릭은 정기적으로 수집되며 특정 시간에 시스템의 일부 측면을 설명하는 수치 값입니다. Azure Monitor의 메트릭은 경량이며 거의 실시간으로 시나리오를 지원할 수 특히 경고 및 빠른 문제 감지에 유용합니다. 메트릭 탐색기를 사용하여 대화형으로 분석하거나, 값이 임계값을 초과할 때 경고를 통해 사전에 알림을 받거나, 통합 문서나 대시보드에서 시각화할 수 있습니다.


> [!NOTE]
> Azure Monitor 메트릭은 Azure Monitor를 지원하는 데이터 플랫폼의 절반입니다. 다른 절반은 로그와 성능 데이터를 수집하고 구성하며 풍부한 쿼리 언어로 분석할 수 있는 [Azure Monitor 로그](../logs/data-platform-logs.md)입니다. 메트릭은 Azure Monitor 로그의 데이터보다 경량이며 거의 실시간으로 시나리오를 지원하여 특히 경고 및 빠른 이슈 검색에 유용합니다. 그러나 메트릭은 특정 구조에 숫자 데이터만 저장할 수 있지만, 로그는 고유 구조를 사용하여 다양한 데이터 형식을 저장할 수 있습니다. 또한 메트릭 데이터의 분석에 사용할 수 없는 로그 쿼리를 사용하여 로그 데이터에 대한 복잡한 분석을 수행할 수 있습니다.


## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Azure Monitor 메트릭으로 무엇을 할 수 있나요?
다음 테이블에는 Azure Monitor에서 메트릭을 사용할 수 있는 여러 방법이 나와 있습니다.

|  | 설명 |
|:---|:---|
| **분석** | [메트릭 탐색기](metrics-charts.md)를 사용 하 여 차트에서 수집된 메트릭을 분석하고 여러 리소스의 메트릭을 비교할 수 있습니다. |
| **경고** | 메트릭 값이 임계값을 초과하면 알림을 보내거나 [자동화된 작업](../alerts/action-groups.md)을 수행하는 [메트릭 경고 규칙](../alerts/alerts-metric.md)을 구성합니다. |
| **시각화** | 메트릭 탐색기에서 [Azure 대시보드](../app/tutorial-app-dashboards.md)에 차트를 고정합니다.<br>대화형 보고서에서 여러 데이터 집합을 결합하는 [워크북](../visualize/workbooks-overview.md)을 만듭니다. 쿼리 결과를 [Grafana](../visualize/grafana-plugin.md)로 내보내 대시보드를 활용하고 다른 데이터 소스와 결합합니다. |
| **자동화** |  [자동 크기 조정](../autoscale/autoscale-overview.md)을 사용하여 임계값을 초과하는 메트릭 값을 기준으로 리소스를 늘리거나 줄입니다. |
| **장치** | [PowerShell cmdlet](/powershell/module/az.monitor)을 사용하여 명령줄에서 메트릭 값에 액세스<br>[REST API](./rest-api-walkthrough.md)를 사용하여 사용자 지정 응용 프로그램에서 메트릭 값에 액세스<br>[CLI](/cli/azure/monitor/metrics)를 사용하여 명령줄에서 메트릭 값에 액세스 |
| **내보내기** | [로그로 메트릭 라우팅](./resource-logs.md#send-to-azure-storage)은 Azure Monitor 로그의 데이터와 함께 Azure Monitor 메트릭의 데이터를 분석하고 93일 이상 메트릭 값을 저장합니다.<br>메트릭을 [이벤트 허브](./stream-monitoring-data-event-hubs.md)로 스트리밍하여 외부 시스템에 라우팅합니다. |
| **보관** | 규정 준수, 감사 또는 오프라인 보고의 목적으로 리소스의 성능 또는 상태 기록을 [보관](./platform-logs-overview.md)합니다. |

![메트릭 개요](media/data-platform-metrics/metrics-overview.png)


## <a name="data-collection"></a>데이터 수집
Azure Monitor에서 수집되는 메트릭의 세 가지 기본 원본이 있습니다. 이러한 메트릭이 Azure Monitor 메트릭 데이터베이스에서 수집되면 해당 원본에 관계없이 함께 평가할 수 있습니다.

**Azure 리소스**. 플랫폼 메트릭은 Azure 리소스에서 생성되고 해당 상태 및 성능에 대한 가시성을 제공합니다. 각 리소스 유형은 필요한 구성 없이 [고유 메트릭 집합](./metrics-supported.md)을 만듭니다. 메트릭 정의에서 달리 지정하지 않은 한, 플랫폼 메트릭은 1분 간격으로 Azure 리소스에서 수집됩니다. 

**응용 프로그램**. 메트릭은 모니터링된 애플리케이션에 대한 Application Insights에 의해 생성되며 성능 문제를 감지하고 애플리케이션이 사용되는 추세를 추적하는 데 도움이 됩니다. _서버 응답 시간_ 및 _브라우저 예외_ 와 같은 값을 포함합니다.

**가상 머신 에이전트**. 메트릭은 가상 머신의 게스트 운영 체제에서 수집됩니다. Windows 가상 머신의 경우 [WAD(Windows Diagnostic Extension)](../agents/diagnostics-extension-overview.md)를 사용하고, Linux 가상 머신의 경우 [InfluxData Telegraf Agent](https://www.influxdata.com/time-series-platform/telegraf/)를 사용하여 게스트 OS 메트릭을 사용하도록 설정합니다.

**사용자 지정 메트릭**. 자동으로 사용할 수 있는 표준 메트릭 외에 메트릭을 정의할 수 있습니다. Application Insights에서 모니터링하는 [응용 프로그램에서 사용자 지정 메트릭을 정의](../app/api-custom-events-metrics.md)하거나 [사용자 지정 메트릭 API](./metrics-store-custom-rest-api.md)를 사용하여 Azure 서비스에 대한 사용자 시정 메트릭을 생성할 수 있습니다.

- Azure Monitor 메트릭에 데이터를 보낼 수 있는 데이터 원본의 전체 목록은 [Azure Monitor에서 모니터링되는 내용](../monitor-reference.md)을 참조하세요.

## <a name="metrics-explorer"></a>메트릭 탐색기
[메트릭 탐색기](metrics-charts.md)를 사용하여 메트릭 데이터베이스에서 데이터를 대화형으로 분석하고 시간 경과에 따라 여러 가지 메트릭의 값을 기록합니다. 다른 시각화 요소를 사용하여 보려는 대시보드에 차트를 고정할 수 있습니다. [Azure 모니터링 REST API](./rest-api-walkthrough.md)를 사용하여 메트릭을 검색할 수도 있습니다.

![메트릭 탐색기](media/data-platform-metrics/metrics-explorer.png)

- 메트릭 탐색기를 사용하여 시작하려면 [Azure Monitor 메트릭 탐색기 시작](./metrics-getting-started.md)을 참조하세요.

## <a name="data-structure"></a>데이터 구조
Azure Monitor 메트릭에서 수집한 데이터는 타임 스탬프 데이터 분석에 최적화된 시계열 데이터베이스에 저장됩니다. 각 메트릭 값 집합은 다음 속성을 포함하는 시계열입니다.

* 값이 수집된 시간
* 값이 연결된 리소스
* 메트릭의 범주처럼 작동하는 네임스페이스
* 메트릭 이름
* 값 자체
* 일부 메트릭에는 [다차원 메트릭](#multi-dimensional-metrics)에 설명된 대로 여러 차원이 있을 수 있습니다. 사용자 지정 메트릭에는 최대 10개의 차원이 있을 수 있습니다.

## <a name="multi-dimensional-metrics"></a>다차원 메트릭
메트릭 데이터의 문제 중 하나는 수집된 값에 대한 컨텍스트를 제공하는 데 제한된 정보를 포함하는 경우가 많다는 것입니다. Azure Monitor는 다차원 메트릭으로 이 문제를 해결합니다. 메트릭의 차원은 메트릭 값을 설명하도록 추가 데이터를 전송하는 이름 값 쌍입니다. 예를 들어 _사용 가능한 디스크 공간_ 메트릭에는 _C:_ , _D:_ 값을 가진 _드라이브_ 라는 차원이 있을 수 있습니다. 이 값을 사용하면 모든 드라이브 또는 각 드라이브에 개별적으로 사용 가능한 디스크 공간을 볼 수 있습니다.

아래 예제에서는 _네트워크 처리량_ 이라는 가상의 메트릭에 대한 두 개의 데이터 세트를 보여줍니다. 첫 번째 데이터 세트에는 차원이 없습니다. 두 번째 데이터 세트에는 두 가지 차원, 즉 _IP 주소_ 와 _방향_(Direction)의 값이 표시됩니다.

### <a name="network-throughput"></a>네트워크 처리량

| 타임스탬프     | 메트릭 값 |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8Kbps |
| 8/9/2017 8:15 | 1,141.4Kbps |
| 8/9/2017 8:16 | 1,110.2Kbps |

이 비차원 메트릭은 "지정된 시간에 내 네트워크 처리량은 무엇이었나요?"와 같은 기본적인 질문에만 응답할 수 있습니다.

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>네트워크 처리량 +2 차원("IP" 및 "방향")

| 타임스탬프     | 차원 "IP"   | 차원 "방향" | 메트릭 값|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP="192.168.5.2" | 방향="전송"    | 646.5Kbps |
| 8/9/2017 8:14 | IP="192.168.5.2" | 방향="수신" | 420.1Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | 방향="전송"    | 150.0Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | 방향="수신" | 115.2Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | 방향="전송"    | 515.2Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | 방향="수신" | 371.1Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | 방향="전송"    | 155.0Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | 방향="수신" | 100.1Kbps |

이 메트릭은 "각 IP 주소에 대한 네트워크 처리량은 무엇이었나요?", "전송된 데이터 및 수신된 데이터의 양은 얼마인가요?"와 같은 질문에 대답할 수 있습니다. 다차원 메트릭은 비차원 메트릭에 대해 추가 분석 및 진단 값을 가집니다.

### <a name="view-multi-dimensional-performance-counter-metrics-in-metrics-explorer"></a>메트릭 탐색기에서 다차원 성능 카운터 메트릭 보기 
별표(\*)가 포함된 성능 카운터 메트릭을 클래식 게스트 메트릭 API를 통해 Azure Monitor에 보낼 수 없습니다. 이 API는 클래식 메트릭에서 지원하지 않는 다차원 메트릭이기 때문에 별표를 포함하는 메트릭을 표시할 수 없습니다.
다음은 다차원 성능 카운터 메트릭을 구성하고 보는 방법에 대한 지침입니다.
1.  가상 머신에 대한 진단 설정 페이지로 이동
2.  "성능 카운터" 탭을 선택합니다. 
3.  "사용자 지정"을 클릭하여 수집하려는 성능 카운터를 구성합니다.
![진단 설정 페이지의 성능 카운터 섹션 스크린샷](media/data-platform-metrics/azure-monitor-perf-counter.png)

4.  성능 카운터를 구성한 후 "싱크"를 클릭합니다. 그런 다음, 사용을 선택하여 데이터를 Azure Monitor에 보냅니다.
![진단 설정 페이지의 싱크 섹션 스크린샷](media/data-platform-metrics/azure-monitor-sink.png)

5.  Azure Monitor에서 메트릭을 보려면 메트릭 네임스페이스 드롭다운에서 "가상 머신 게스트"를 선택합니다.
![메트릭 네임스페이스의 스크린샷](media/data-platform-metrics/vm-guest-namespace.png)

6.  메트릭을 인스턴스별로 분할하여, 구성에서 "\*"로 표시되는 가능한 각각의 값으로 세분하여 메트릭을 확인합니다.  이 예제에서 "\*"는 다른 논리 디스크 볼륨과 합계를 나타냅니다.
![인스턴스별 메트릭 분할 스크린샷](media/data-platform-metrics/split-by-instance.png)



## <a name="retention-of-metrics"></a>메트릭 보존
대부분의 Azure 리소스에서 플랫폼 메트릭은 93일간 보관됩니다. 다음과 같은 몇 가지 예외 사항이 있습니다.

**게스트 OS 메트릭**
-   **클래식 게스트 OS 메트릭** - 14일 및 종종 그 이상. 이러한 메트릭은 [WAD(Windows Diagnostic Extension)](../agents/diagnostics-extension-overview.md) 또는 [LAD(Linux Diagnostic Extension)](../../virtual-machines/extensions/diagnostics-linux.md)에 의해 수집되어 Azure Storage 계정으로 라우팅되는 성능 카운터입니다. 스토리지 계정에 실제 만료 날짜가 기록되지 않지만, 해당 메트릭은 최소 14일 동안 보존되도록 보장됩니다. 성능상의 이유로 포털은 볼륨에 따라 표시되는 데이터의 양을 제한합니다. 따라서 기록되는 데이터의 양이 그다지 크지 않은 경우 포털에서 검색하는 실제 기간(일)은 14일보다 길 수 있습니다.  
-   **Azure Monitor 메트릭으로 전송된 게스트 OS 메트릭** - 93일. [WAD(Windows Diagnostic Extension)](../agents/diagnostics-extension-overview.md)에 의해 수집되어 [Azure Monitor 데이터 싱크](../agents/diagnostics-extension-overview.md#data-destinations)로 전송되거나, Linux 컴퓨터의 [InfluxData Telegraf Agent](https://www.influxdata.com/time-series-platform/telegraf/) 또는 데이터 수집 규칙에 따라 최신 [Azure Monitor Agent](../agents/azure-monitor-agent-overview.md)(AMA)에 전송되는 성능 카운터입니다. 이러한 메트릭의 보존 기간은 93일입니다.
-   **Log Analytics 에이전트에서 수집한 게스트 OS 메트릭** - 31일에서 2년. 이러한 메트릭은 Log Analytics 에이전트에 의해 수집되어 Log Analytics 작업 영역으로 전송되는 성능 카운터입니다. 이 메트릭의 보존 기간은 31일이며 2년까지 연장할 수 있습니다.

**Application Insights 로그 기반 메트릭** 다릅니다. - 백그라운드에서 [로그 기반 메트릭](../app/pre-aggregated-metrics-log-metrics.md)을 로그 쿼리로 변환합니다. 해당 보존 기간은 기본 로그의 이벤트 보존 기간과 같습니다(31일에서 2년). Application Insights 리소스의 경우 로그는 90일 동안 저장됩니다.


> [!NOTE]
> 장기적인 추세의 경우 [Log Analytics 작업 영역에 Azure Monitor 리소스에 대한 플랫폼 메트릭을 보낼 수](./resource-logs.md#send-to-azure-storage) 있습니다.


> [!NOTE]
> 위에서 언급한 대로 대부분의 Azure 리소스에서 플랫폼 메트릭은 93일간 보관됩니다. 그러나 단일 차트에서는 30일의 데이터만 메트릭 타일에서 쿼리할 수 있습니다. 이 제한은 로그 기반 메트릭에는 적용되지 않습니다. 빈 차트가 표시되거나 차트에 메트릭 데이터의 일부만 표시되는 경우 시간 선택기의 시작 날짜와 종료 날짜 사이의 간격이 30일을 초과하지 않는지 확인합니다. 30일 간격을 선택하면 차트를 [이동](./metrics-charts.md#pan)하여 전체 보존 기간을 볼 수 있습니다.




## <a name="next-steps"></a>다음 단계

- [Azure Monitor 데이터 플랫폼](../data-platform.md)에 대해 자세히 알아봅니다.
- [Azure Monitor의 로그 데이터](../logs/data-platform-logs.md)에 대해 알아봅니다.
- Azure의 다양한 리소스에 [사용 가능한 모니터링 데이터](../agents/data-sources.md)에 대해 알아봅니다.