---
title: Azure Monitor의 메트릭 | Microsoft Docs
description: 거의 실시간 시나리오를 지원할 수 있는 경량 모니터링 데이터 인 Azure Monitor의 메트릭에 대해 알아보세요.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2021
ms.author: bwren
ms.openlocfilehash: 3986e59965842166ce675cd778d04984896d671d
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458532"
---
# <a name="azure-monitor-metrics-overview"></a>Azure Monitor 메트릭 개요
Azure Monitor 메트릭은 [모니터링된 리소스](../monitor-reference.md)에서 시계열 데이터베이스로 숫자 데이터를 수집하는 Azure Monitor의 기능입니다. 메트릭은 정기적으로 수집되며 특정 시간에 시스템의 일부 측면을 설명하는 수치 값입니다. 

Azure Monitor 메트릭은 거의 실시간 시나리오를 지원할 수 있으므로 문제를 경고 하 고 신속 하 게 검색 하는 데 유용 합니다. 메트릭 탐색기를 사용 하 여 대화형으로 분석 하거나, 값이 임계값을 초과할 때 경고를 사용 하 여 사전에 통보 하거나, 통합 문서 또는 대시보드에서 시각화할 수 있습니다.

> [!NOTE]
> Azure Monitor 메트릭은 Azure Monitor를 지 원하는 데이터 플랫폼의 절반입니다. 다른는 로그 및 성능 데이터를 수집 하 고 구성 하며 풍부한 쿼리 언어를 사용 하 여 해당 데이터를 분석할 수 있도록 하는 [Azure Monitor 로그](../logs/data-platform-logs.md)입니다. 
>
> 메트릭 기능은 특정 구조에 숫자 데이터만 저장할 수 있는 반면, 로그 기능은 다양 한 데이터 형식 (각각 자체 구조 포함)을 저장할 수 있습니다. 또한 메트릭 데이터 분석에 사용할 수 없는 로그 쿼리를 사용 하 여 로그 데이터에 대 한 복잡 한 분석을 수행할 수 있습니다.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Azure Monitor 메트릭으로 무엇을 할 수 있나요?
다음 표에서는 Azure Monitor에서 메트릭 기능을 사용할 수 있는 방법을 보여 줍니다.

|  | Description |
|:---|:---|
| **분석** | [메트릭 탐색기](metrics-charts.md) 를 사용 하 여 차트에서 수집 된 메트릭을 분석 하 고 다양 한 리소스의 메트릭을 비교할 수 있습니다. |
| **경고** | 메트릭 값이 임계값을 초과하면 알림을 보내거나 [자동화된 작업](../alerts/action-groups.md)을 수행하는 [메트릭 경고 규칙](../alerts/alerts-metric.md)을 구성합니다. |
| **시각화** | 메트릭 탐색기에서 [Azure 대시보드에](../app/tutorial-app-dashboards.md)차트를 고정 합니다.<br>[통합 문서](../visualize/workbooks-overview.md)를 만들어 대화형 보고서의 여러 데이터 집합을 결합합니다. 쿼리의 결과를 [Grafana](../visualize/grafana-plugin.md)로 내보내 대시보드를 사용하고 다른 데이터 원본과 결합합니다. |
| **자동화** |  [자동 크기 조정](../autoscale/autoscale-overview.md)을 사용하여 임계값을 초과하는 메트릭 값을 기준으로 리소스를 늘리거나 줄입니다. |
| **장치** | [PowerShell cmdlet](/powershell/module/az.monitor)을 사용 하 여 명령줄에서 메트릭 값에 액세스 합니다.<br>[REST API](./rest-api-walkthrough.md)를 사용 하 여 사용자 지정 응용 프로그램에서 메트릭 값에 액세스 합니다.<br>[Azure CLI](/cli/azure/monitor/metrics)를 사용 하 여 명령줄에서 메트릭 값에 액세스 합니다. |
| **내보내기** | [로그에 메트릭을 라우트](./resource-logs.md#send-to-azure-storage) 하 여 Azure Monitor 메트릭의 데이터를 Azure Monitor 로그의 데이터와 함께 분석 하 고 메트릭 값을 93 일 보다 오래 저장 합니다.<br>[이벤트 허브](./stream-monitoring-data-event-hubs.md) 로 메트릭을 스트리밍하려면 외부 시스템으로 라우팅합니다. |
| **보관** | 규정 준수, 감사 또는 오프라인 보고의 목적으로 리소스의 성능 또는 상태 기록을 [보관](./platform-logs-overview.md)합니다. |

![메트릭의 원본 및 사용을 보여 주는 다이어그램입니다.](media/data-platform-metrics/metrics-overview.png)

## <a name="data-collection"></a>데이터 수집
Azure Monitor는 다음 원본에서 메트릭을 수집 합니다. 이러한 메트릭은 Azure Monitor 메트릭 데이터베이스에서 수집 된 후 원본에 관계 없이 함께 평가할 수 있습니다.

- **Azure 리소스**. 플랫폼 메트릭은 Azure 리소스에서 생성되고 해당 상태 및 성능에 대한 가시성을 제공합니다. 각 리소스 유형은 필요한 구성 없이 [고유 메트릭 집합](./metrics-supported.md)을 만듭니다. 메트릭 정의에서 달리 지정하지 않은 한, 플랫폼 메트릭은 1분 간격으로 Azure 리소스에서 수집됩니다. 

- **응용 프로그램**. Application Insights는 모니터링 되는 응용 프로그램에 대 한 메트릭을 만들어 성능 문제를 감지 하 고 응용 프로그램의 사용 방식에 대 한 추세를 추적할 수 있도록 합니다. 값에는 _서버 응답 시간_ 및 _브라우저 예외가_ 포함 됩니다.

- **가상 머신 에이전트**. 메트릭은 가상 머신의 게스트 운영 체제에서 수집됩니다. [InfluxData Telegraf 에이전트](https://www.influxdata.com/time-series-platform/telegraf/)를 사용 하 여 [Windows 진단 확장](../agents/diagnostics-extension-overview.md) 및 Linux 가상 컴퓨터를 사용 하 여 Windows 가상 컴퓨터에 대 한 게스트 OS 메트릭을 사용 하도록 설정할 수 있습니다.

- **사용자 지정 메트릭**. 자동으로 사용할 수 있는 표준 메트릭 외에 메트릭을 정의할 수 있습니다. Application Insights에서 모니터링 하는 [응용 프로그램에서](../app/api-custom-events-metrics.md) 사용자 지정 메트릭을 정의 하거나 [사용자 지정 메트릭 API](./metrics-store-custom-rest-api.md)를 사용 하 여 Azure 서비스에 대 한 사용자 지정 메트릭을 만들 수 있습니다.

Azure Monitor 메트릭에 데이터를 보낼 수 있는 데이터 원본의 전체 목록은 [Azure Monitor에서 모니터링 하는 항목](../monitor-reference.md)을 참조 하세요.

## <a name="metrics-explorer"></a>메트릭 탐색기
[메트릭 탐색기](metrics-charts.md)를 사용하여 메트릭 데이터베이스에서 데이터를 대화형으로 분석하고 시간 경과에 따라 여러 가지 메트릭의 값을 기록합니다. 다른 시각화 요소를 사용하여 보려는 대시보드에 차트를 고정할 수 있습니다. [Azure 모니터링 REST API](./rest-api-walkthrough.md)를 사용하여 메트릭을 검색할 수도 있습니다.

![서버 요청, 서버 응답 시간 및 실패 한 요청을 보여 주는 메트릭 탐색기 그래프의 스크린샷](media/data-platform-metrics/metrics-explorer.png)

자세한 내용은 [Azure Monitor 메트릭 탐색기 시작](./metrics-getting-started.md)하기를 참조 하세요.

## <a name="data-structure"></a>데이터 구조
Azure Monitor 메트릭이 수집 하는 데이터는 시간 스탬프가 있는 데이터를 분석 하는 데 최적화 된 시계열 데이터베이스에 저장 됩니다. 각 메트릭 값 집합은 다음 속성을 포함하는 시계열입니다.

* 값이 수집된 시간
* 값이 연결된 리소스
* 메트릭에 대 한 범주 처럼 동작 하는 네임 스페이스입니다.
* 메트릭 이름
* 값 자체
* [여러 차원](#multi-dimensional-metrics) (있는 경우). 사용자 지정 메트릭은 10 차원으로 제한 됩니다.

## <a name="multi-dimensional-metrics"></a>다차원 메트릭
메트릭 데이터의 문제 중 하나는 수집된 값에 대한 컨텍스트를 제공하는 데 제한된 정보를 포함하는 경우가 많다는 것입니다. Azure Monitor는 다차원 메트릭으로 이 문제를 해결합니다. 

메트릭의 차원은 메트릭 값을 설명 하기 위해 추가 데이터를 전달 하는 이름/값 쌍입니다. 예를 들어 _사용 가능한 디스크 공간_ 이라는 메트릭에는 _C:_ 및 _D:_ 값을 가진 _드라이브_ 라는 차원이 있을 수 있습니다. 해당 차원은 모든 드라이브 또는 각 드라이브에서 사용 가능한 디스크 공간을 개별적으로 볼 수 있습니다.

다음 예제에서는 _네트워크 처리량_ 이라는 가상의 메트릭에 대 한 두 개의 데이터 집합을 보여 줍니다. 첫 번째 데이터 세트에는 차원이 없습니다. 두 번째 데이터 집합에는 두 개의 차원, 즉 _IP_ 와 _방향이_ 있는 값이 표시 됩니다.

### <a name="network-throughput"></a>네트워크 처리량

| 타임스탬프     | 메트릭 값 |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8Kbps |
| 8/9/2017 8:15 | 1,141.4Kbps |
| 8/9/2017 8:16 | 1,110.2Kbps |

이 차원이 아닌 메트릭은 "지정 된 시간에 네트워크 처리량은 무엇 인가요?"와 같은 기본적인 질문에만 답변할 수 있습니다.

### <a name="network-throughput-and-two-dimensions-ip-and-direction"></a>네트워크 처리량 및 두 차원 ("IP" 및 "Direction")

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

이 메트릭은 "각 IP 주소에 대 한 네트워크 처리량은 무엇 인가요?"와 같은 질문에 대답할 수 있습니다. "전송 된 데이터와 받은 데이터의 양 다차원 메트릭은 차원이 아닌 메트릭에 비해 추가 분석 및 진단 값을 포함 합니다.

### <a name="viewing-multi-dimensional-performance-counter-metrics-in-metrics-explorer"></a>메트릭 탐색기에서 다차원 성능 카운터 메트릭 보기 
별표 ()가 포함 된 성능 카운터 메트릭을 전송 하 여 \* 클래식 게스트 메트릭 API를 통해 Azure Monitor 수는 없습니다. 이 API는 클래식 메트릭이 지원 하지 않는 다차원 메트릭입니다. 별표를 포함 하는 메트릭을 표시할 수 없습니다.

Azure 진단 확장을 사용 하 여 다차원 게스트 OS 성능 카운터 메트릭을 구성 하 고 확인 하려면 다음을 수행 합니다.

1. 가상 컴퓨터에 대 한 **진단 설정** 페이지로 이동 합니다.
2. **성능 카운터** 탭을 선택 합니다. 
3. **사용자 지정** 을 선택 하 여 수집할 성능 카운터를 구성 합니다.

   ![진단 설정 페이지의 성능 카운터 섹션 스크린샷](media/data-platform-metrics/azure-monitor-perf-counter.png)

4. **싱크** 를 선택 합니다. 그런 다음 **사용** 을 선택 하 여 데이터를 Azure Monitor 보냅니다.

   ![진단 설정 페이지의 싱크 섹션 스크린샷](media/data-platform-metrics/azure-monitor-sink.png)

5. Azure Monitor에서 메트릭을 보려면 **메트릭 네임 스페이스** 드롭다운 목록에서 **가상 머신 게스트** 를 선택 합니다.

   ![메트릭 네임 스페이스의 스크린샷](media/data-platform-metrics/vm-guest-namespace.png)

6. **분할 적용** 을 선택 하 고 세부 정보를 입력 하 여 인스턴스를 기준으로 메트릭을 분할 합니다. 그런 다음 구성에서 별표로 표시 되는 가능한 각 값으로 세분화 된 메트릭을 볼 수 있습니다. 이 예에서 별표는 논리 디스크 볼륨에 총을 더한 값을 나타냅니다.

   ![인스턴스별 메트릭을 분할 하는 스크린샷](media/data-platform-metrics/split-by-instance.png)

## <a name="retention-of-metrics"></a>메트릭 보존
대부분의 Azure 리소스에서 플랫폼 메트릭은 93일간 보관됩니다. 다음과 같은 몇 가지 예외 사항이 있습니다.

- **클래식 게스트 OS 메트릭**: [Windows 진단 확장](../agents/diagnostics-extension-overview.md) 또는 [Linux 진단 확장](../../virtual-machines/extensions/diagnostics-linux.md) 에 의해 수집 되 고 Azure storage 계정으로 라우팅되는 성능 카운터입니다. 저장소 계정에 만료 날짜가 기록 되지 않지만 이러한 메트릭에 대 한 보존 기간은 14 일 이상 이어야 합니다. 
  
  성능상의 이유로 포털은 볼륨에 따라 표시되는 데이터의 양을 제한합니다. 따라서 작성 되는 데이터의 양이 크지 않은 경우 포털이 검색 하는 실제 일 수는 14 일 보다 길 수 있습니다. 

- **Azure Monitor 메트릭을 전송 하는 게스트 OS 메트릭**: [Windows 진단 확장](../agents/diagnostics-extension-overview.md) 에 의해 수집 되 고 [Azure Monitor 데이터 싱크로](../agents/diagnostics-extension-overview.md#data-destinations)전송 된 성능 카운터 또는 Linux 컴퓨터의 [InfluxData Telegraf 에이전트](https://www.influxdata.com/time-series-platform/telegraf/) 또는 데이터 수집 규칙을 통해 최신 [Azure Monitor 에이전트로](../agents/azure-monitor-agent-overview.md) 전송 됩니다. 이러한 메트릭의 보존 기간은 93일입니다.

- **Log Analytics 에이전트에서 수집 하는 게스트 OS 메트릭**: Log Analytics 에이전트가 수집 하 고 Log Analytics 작업 영역으로 전송 되는 성능 카운터입니다. 이러한 메트릭에 대 한 보존은 31 일 이며 최대 2 년까지 연장할 수 있습니다.

- **Application Insights 로그 기반 메트릭** 내부적으로 [로그 기반 메트릭은](../app/pre-aggregated-metrics-log-metrics.md) 로그 쿼리로 변환 됩니다. 보존 기간은 가변적 이며 기본 로그의 이벤트 보존 (31 일 ~ 2 년)과 일치 합니다. Application Insights 리소스의 경우 로그는 90일 동안 저장됩니다.

> [!NOTE]
> 장기적인 추세를 위해 [Azure Monitor 리소스에 대 한 플랫폼 메트릭을 Log Analytics 작업 영역으로 보낼](./resource-logs.md#send-to-azure-storage) 수 있습니다.

> [!NOTE]
> 앞에서 설명한 것 처럼 Azure의 대부분 리소스에 대해 플랫폼 메트릭은 93 일 동안 저장 됩니다. 그러나 단일 차트에서 최대 30 일 분량의 데이터에 대해서만 ( **메트릭** 타일에서) 쿼리할 수 있습니다. 이 제한은 로그 기반 메트릭에는 적용되지 않습니다. 
>
> 빈 차트가 표시 되거나 차트에 메트릭 데이터의 일부만 표시 되는 경우 시간 선택의 시작 날짜와 종료 날짜 간의 차이가 30 일 간격을 초과 하지 않는지 확인 합니다. 30 일 간격을 선택한 후에는 차트를 [이동](./metrics-charts.md#pan) 하 여 전체 보존 기간을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Monitor 데이터 플랫폼](../data-platform.md)에 대해 자세히 알아봅니다.
- [Azure Monitor의 로그 데이터](../logs/data-platform-logs.md)에 대해 알아봅니다.
- Azure의 다양한 리소스에 [사용 가능한 모니터링 데이터](../agents/data-sources.md)에 대해 알아봅니다.