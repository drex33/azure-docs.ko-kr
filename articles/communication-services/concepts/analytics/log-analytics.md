---
title: Azure Communication Services - Log Analytics 미리 보기
titleSuffix: An Azure Communication Services concept document
description: 호출 요약 및 호출 진단 로그에 대한 분석 로그 사용 정보
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: 05ae40fc974228bf93641be3786294aab59c243a
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132139833"
---
# <a name="log-analytics-for-communications-services-preview"></a>Communications Services 미리 보기에 대한 로그 분석

## <a name="overview-and-access"></a>개요 및 액세스

Communications Services 로그에 [Log Analytics를](/azure/azure-monitor/logs/log-analytics-overview) 활용하려면 먼저 [진단 설정 로깅 사용 에](enable-logging.md)설명된 단계를 수행해야 합니다. 로그 및 [Log Analytics 작업 영역을](/azure/azure-monitor/logs/design-logs-deployment)사용하도록 설정하면 아래에 설명된 로그에서 사용할 수 있는 데이터를 빠르게 시각화하고 이해하는 데 도움이 되는 많은 유용한 기본 쿼리 [팩에](/azure/azure-monitor/logs/query-packs#default-query-pack) 액세스할 수 있습니다. Log Analytics를 통해 Azure Monitor 통합 문서를 통해 더 많은 Communications Services Insights 액세스할 수 있습니다(Communications [Services Insights](insights.md)참조), 쿼리에 [대한 액세스 REST API](https://dev.loganalytics.io/) 고유한 쿼리 및 통합 문서를 만드는 기능.

### <a name="access"></a>Access
Communications Services 리소스 페이지에서 시작한 다음 모니터 섹션 내의 왼쪽 탐색에서 "로그"를 클릭하여 쿼리에 액세스할 수 있습니다.

:::image type="content" source="media\log-analytics\access-log-analytics.png" alt-text="Log Analytics 탐색":::

그러면 Communications Services에 사용할 수 있는 모든 [기본 쿼리 팩이](/azure/azure-monitor/logs/query-packs#default-query-pack) 포함된 모달 화면이 표시되고, 왼쪽으로 이동할 수 있는 쿼리 팩 목록이 표시됩니다.

:::image type="content" source="media\log-analytics\log-analytics-modal-resource.png" alt-text="log analytics 쿼리 모달" lightbox="media\log-analytics\log-analytics-modal-resource.png":::

모달 화면을 닫는 경우에도 다양한 쿼리 팩으로 이동하여 진단 설정에서 사용하도록 설정한 로그 및 메트릭의 스키마에 따라 테이블 형식으로 데이터에 직접 액세스할 수 있습니다. 여기서 [KQL(Kusto)을](/azure/data-explorer/kusto/query/)사용하여 데이터에서 고유한 쿼리를 만들 수 있습니다. [Log Analytics](/azure/azure-monitor/logs/queries) 쿼리에 대한 자세한 내용을 읽어 쿼리 사용, 편집 및 만들기에 대해 자세히 알아보세요.

:::image type="content" source="media\log-analytics\log-analytics-queries-resource.png" alt-text="리소스의 Log Analytics 쿼리" lightbox="media\log-analytics\log-analytics-queries-resource.png":::

:::image type="content" source="media\log-analytics\log-analytics-tables-resource.png" alt-text="리소스의 Log Analytics 테이블" lightbox="media\log-analytics\log-analytics-tables-resource.png":::

## <a name="default-query-packs-for-call-summary-and-call-diagnostic-logs"></a>호출 요약 및 호출 진단 로그에 대한 기본 쿼리 팩
다음은 사용 가능한 각 [쿼리에](/azure/azure-monitor/logs/query-packs#default-query-pack)대한 코드 샘플 및 예제 출력을 포함하여 [호출 요약 및 호출 진단 로그에](call-logs-azure-monitor.md) 대한 기본 쿼리 팩 의 각 쿼리에 대한 설명입니다.
### <a name="call-overview-queries"></a>호출 개요 쿼리
#### <a name="number-of-participants-per-call"></a>호출당 참가자 수

```
// Count number of calls and participants,
// and print average participants per call
ACSCallSummary
| distinct CorrelationId, ParticipantId, EndpointId
| summarize num_participants=count(), num_calls=dcount(CorrelationId)
| extend avg_participants = todecimal(num_participants) / todecimal(num_calls)
```

샘플 출력:

:::image type="content" source="media\log-analytics\call-overview-sample.png" alt-text="호출 개요 쿼리":::

#### <a name="number-of-participants-per-group-call"></a>그룹 호출당 참가자 수

```
// Count number of participants per group call
ACSCallSummary
| where CallType == 'Group'
| distinct CorrelationId, ParticipantId
| summarize num_participants=count() by CorrelationId
| summarize participant_counts=count() by num_participants
| order by num_participants asc 
| render columnchart with  (xcolumn = num_participants, title="Number of participants per group call")
```

샘플 출력:

:::image type="content" source="media\log-analytics\participants-group-sample.png" alt-text="그룹 호출 쿼리당 참가자 수":::

#### <a name="ratio-of-call-types"></a>호출 형식의 비율

```
// Ratio of call types
ACSCallSummary
| summarize call_types=dcount(CorrelationId) by CallType
| render piechart title="Call Type Ratio"

```

샘플 출력:

:::image type="content" source="media\log-analytics\ratio-call-types-sample.png" alt-text="호출 형식 쿼리의 비율":::

#### <a name="call-duration-distribution"></a>호출 기간 배포

```
// Call duration histogram
ACSCallSummary
| distinct CorrelationId, CallDuration
|summarize duration_counts=count() by CallDuration
| order by CallDuration asc
| render columnchart with (xcolumn = CallDuration, title="Call duration histogram")
```

샘플 출력:

:::image type="content" source="media\log-analytics\call-duration-sample.png" alt-text="호출 기간 쿼리":::

#### <a name="call-duration-percentiles"></a>호출 기간 백분위수

```
// Call duration percentiles
ACSCallSummary
| distinct CorrelationId, CallDuration
| summarize avg(CallDuration), percentiles(CallDuration, 50, 90, 99)
```

샘플 출력:

:::image type="content" source="media\log-analytics\call-duration-percentile-example.png" alt-text="호출 기간 백분위수 쿼리":::

### <a name="endpoint-information-queries"></a>엔드포인트 정보 쿼리

#### <a name="number-of-endpoints-per-call"></a>호출당 엔드포인트 수

```
// Count number of calls and endpoints,
// and print average endpoints per call
ACSCallSummary
| distinct CorrelationId, EndpointId
| summarize num_endpoints=count(), num_calls=dcount(CorrelationId)
| extend avg_endpoints = todecimal(num_endpoints) / todecimal(num_calls)
```

샘플 출력:

:::image type="content" source="media\log-analytics\endpoints-per-call.png" alt-text="호출 쿼리당 엔드포인트 수":::

#### <a name="ratio-of-sdk-versions"></a>SDK 버전 비율

```
// Ratio of SDK Versions
ACSCallSummary
| distinct CorrelationId, ParticipantId, EndpointId, SdkVersion
| summarize sdk_counts=count() by SdkVersion
| order by SdkVersion asc
| render piechart title="SDK Version Ratio"
```

샘플 출력:

:::image type="content" source="media\log-analytics\sdk-ratio-sample.png" alt-text="SDK 버전의 비율을 보여주는 원형 차트입니다."::: :::image type="content" source="media\log-analytics\sdk-ratio-table-sample.png" alt-text="SDK 버전을 보여주는 테이블":::

#### <a name="ratio-of-os-versions-simplified-os-name"></a>OS 버전 비율(간소화된 OS 이름)

```
// Ratio of OS Versions (simplified OS name)
ACSCallSummary
| distinct CorrelationId, ParticipantId, EndpointId, OsVersion
| extend simple_os = case(  indexof(OsVersion, "Android") != -1, tostring(split(OsVersion, ";")[0]),
                            indexof(OsVersion, "Darwin") != -1, tostring(split(OsVersion, ":")[0]),
                            indexof(OsVersion, "Windows") != -1, tostring(split(OsVersion, ".")[0]),
                            OsVersion
                        )
| summarize os_counts=count() by simple_os
| order by simple_os asc
| render piechart title="OS Version Ratio"
```

샘플 출력:

:::image type="content" source="media\log-analytics\os-version-graph.png" alt-text="운영 체제 비율을 보여주는 원형 차트":::
:::image type="content" source="media\log-analytics\os-version-table.png" alt-text="OS 버전을 보여주는 테이블":::

### <a name="media-stream-queries"></a>미디어 스트림 쿼리
#### <a name="streams-per-call"></a>호출당 스트림

```
// Count number of calls and streams,
// and print average streams per call
ACSCallDiagnostics
| summarize num_streams=count(), num_calls=dcount(CorrelationId)
| extend avg_streams = todecimal(num_streams) / todecimal(num_calls)
```
샘플 출력:

:::image type="content" source="media\log-analytics\streams-call-output.png" alt-text="호출 쿼리당 스트림":::

#### <a name="streams-per-call-histogram"></a>호출 히스토그램당 스트림

```
// Distribution of streams per call
ACSCallDiagnostics
| summarize streams_per_call=count() by CorrelationId
| summarize stream_counts=count() by streams_per_call
| order by streams_per_call asc
| render columnchart title="Streams per call histogram"
```

:::image type="content" source="media\log-analytics\streams-call-histogram.png" alt-text="호출 히스토그램당 스트림":::

#### <a name="ratio-of-media-types"></a>미디어 유형 비율

```
// Ratio of media types by call
ACSCallDiagnostics
| summarize media_types=count() by MediaType
| render piechart title="Media Type Ratio"
```

:::image type="content" source="media\log-analytics\media-type-ratio.png" alt-text="미디어 유형 비율을 보여주는 원형 차트":::

### <a name="quality-metrics-queries"></a>품질 메트릭 쿼리

#### <a name="average-telemetry-values"></a>평균 원격 분석 값

```
// Average telemetry values over all streams
ACSCallDiagnostics
| summarize Avg_JitterAvg=avg(JitterAvg),
            Avg_JitterMax=avg(JitterMax),
            Avg_RoundTripTimeAvg=avg(RoundTripTimeAvg),
            Avg_RoundTripTimeMax=avg(RoundTripTimeMax),
            Avg_PacketLossRateAvg=avg(PacketLossRateAvg),
            Avg_PacketLossRateMax=avg(PacketLossRateMax)
```

:::image type="content" source="media\log-analytics\avg-telemetry-values.png" alt-text="평균 원격 분석 값":::

#### <a name="jitteravg-histogram"></a>JitterAvg 히스토그램

```
// Jitter Average Histogram
ACSCallDiagnostics
| where isnotnull(JitterAvg)
| summarize JitterAvg_counts=count() by JitterAvg
| order by JitterAvg asc
| render columnchart with (xcolumn = JitterAvg, title="JitterAvg histogram")
```

:::image type="content" source="media\log-analytics\jitteravg-histogram.png" alt-text="지터 평균 히스토그램":::

#### <a name="jittermax-histogram"></a>JitterMax 히스토그램

```
// Jitter Max Histogram
ACSCallDiagnostics
| where isnotnull(JitterMax)
|summarize JitterMax_counts=count() by JitterMax
| order by JitterMax asc
| render columnchart with (xcolumn = JitterMax, title="JitterMax histogram")
```

:::image type="content" source="media\log-analytics\jittermax-histogram.png" alt-text="지터 최대 히스토그램":::

#### <a name="packetlossrateavg-histogram"></a>PacketLossRateAvg 히스토그램
```
// PacketLossRate Average Histogram
ACSCallDiagnostics
| where isnotnull(PacketLossRateAvg)
|summarize PacketLossRateAvg_counts=count() by bin(PacketLossRateAvg, 0.01)
| order by PacketLossRateAvg asc
| render columnchart with (xcolumn = PacketLossRateAvg, title="PacketLossRateAvg histogram")
```

:::image type="content" source="media\log-analytics\packetloss-avg-histogram.png" alt-text="패킷 손실 평균 히스토그램":::

#### <a name="packetlossratemax-histogram"></a>PacketLossRateMax 히스토그램
```
// PacketLossRate Max Histogram
ACSCallDiagnostics
| where isnotnull(PacketLossRateMax)
|summarize PacketLossRateMax_counts=count() by bin(PacketLossRateMax, 0.01)
| order by PacketLossRateMax asc
| render columnchart with (xcolumn = PacketLossRateMax, title="PacketLossRateMax histogram")
```

:::image type="content" source="media\log-analytics\packetloss-max-histogram.png" alt-text="패킷 손실 최대 히스토그램":::

#### <a name="roundtriptimeavg-histogram"></a>RoundTripTimeAvg 히스토그램
```
// RoundTripTime Average Histogram
ACSCallDiagnostics
| where isnotnull(RoundTripTimeAvg)
|summarize RoundTripTimeAvg_counts=count() by RoundTripTimeAvg
| order by RoundTripTimeAvg asc
| render columnchart with (xcolumn = RoundTripTimeAvg, title="RoundTripTimeAvg histogram")
```

:::image type="content" source="media\log-analytics\rtt-avg-histogram.png" alt-text="RTT 평균 히스토그램":::

#### <a name="roundtriptimemax-histogram"></a>RoundTripTimeMax 히스토그램
```
// RoundTripTime Max Histogram
ACSCallDiagnostics
| where isnotnull(RoundTripTimeMax)
|summarize RoundTripTimeMax_counts=count() by RoundTripTimeMax
| order by RoundTripTimeMax asc
| render columnchart with (xcolumn = RoundTripTimeMax, title="RoundTripTimeMax histogram")
```

:::image type="content" source="media\log-analytics\rtt-max-histogram.png" alt-text="RTT 최대 히스토그램":::

#### <a name="poor-jitter-quality"></a>지터 품질 저하
```
// Get proportion of calls with poor quality jitter
// (defined as jitter being higher than 30ms)
ACSCallDiagnostics
| extend JitterQuality = iff(JitterAvg > 30, "Poor", "Good")
| summarize count() by JitterQuality
| render piechart title="Jitter Quality"
```

:::image type="content" source="media\log-analytics\jitter-quality.png" alt-text="지터 품질":::


#### <a name="packetlossrate-quality"></a>PacketLossRate Quality
```
// Get proportion of calls with poor quality packet loss
// rate (defined as packet loss being higher than 10%)
ACSCallDiagnostics
| extend PacketLossRateQuality = iff(PacketLossRateAvg > 0.1, "Poor", "Good")
| summarize count() by PacketLossRateQuality
| render piechart title="Packet Loss Rate Quality"
```

:::image type="content" source="media\log-analytics\packet-loss-quality.png" alt-text="패킷 손실률 품질":::

#### <a name="roundtriptime-quality"></a>RoundTripTime 품질
```
// Get proportion of calls with poor quality packet loss
// rate (defined as packet loss being higher than 10%)
ACSCallDiagnostics
| extend PacketLossRateQuality = iff(PacketLossRateAvg > 0.1, "Poor", "Good")
| summarize count() by PacketLossRateQuality
| render piechart title="Packet Loss Rate Quality"
```

:::image type="content" source="media\log-analytics\rtt-quality.png" alt-text="RTT 품질":::

### <a name="parameterizable-queries"></a>매개 변수화 가능한 쿼리

#### <a name="daily-calls-in-the-last-week"></a>지난 주의 일일 통화
```
// Histogram of daily calls over the last week
ACSCallSummary
| where CallStartTime > now() - 7d
| distinct CorrelationId, CallStartTime
| extend hour  = floor(CallStartTime, 1d)
| summarize event_count=count() by day
| sort by day asc
| render columnchart title="Number of calls in last week"
```

:::image type="content" source="media\log-analytics\calls-per-week.png" alt-text="지난 주 일일 통화":::

#### <a name="calls-per-hour-in-last-day"></a>마지막 날의 시간당 호출
```
// Histogram of calls per hour in the last day
ACSCallSummary
| where CallStartTime > now() - 1d
| distinct CorrelationId, CallStartTime
| extend hour = floor(CallStartTime, 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render columnchart title="Number of calls per hour in last day"
```

:::image type="content" source="media\log-analytics\calls-per-hour.png" alt-text="지난 날 시간당 호출 수":::