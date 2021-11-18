---
title: Azure Communication Services-Log Analytics 미리 보기
titleSuffix: An Azure Communication Services concept document
description: 호출 요약에 Log a Analytics 사용 및 진단 로그 호출 정보
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: 42e7a6d972131125130e3dc1976b1061a32e07a9
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716679"
---
# <a name="log-analytics-for-communications-services-preview"></a>Communications Services 미리 보기에 대 한 Log analytics

## <a name="overview-and-access"></a>개요 및 액세스

통신 서비스 로그에 대 한 [Log Analytics](../../../azure-monitor/logs/log-analytics-overview.md) 를 활용 하려면 먼저 [진단 설정에서 로깅 사용](enable-logging.md)에 설명 된 단계를 따라야 합니다. 로그와 [Log Analytics 작업 영역](../../../azure-monitor/logs/design-logs-deployment.md)을 사용 하도록 설정 하면 로그에서 사용할 수 있는 데이터를 신속 하 게 시각화 하 고 이해 하는 데 도움이 되는 여러 가지 유용한 [기본 쿼리 팩](../../../azure-monitor/logs/query-packs.md#default-query-pack) 에 액세스할 수 있습니다 .이에 대해서는 아래에서 설명 합니다. Log Analytics를 통해 Azure Monitor 통합 문서 ( [통신 서비스 Insights](insights.md)참조), 고유한 쿼리 및 통합 문서를 만들 수 있는 기능, 쿼리에 대 한 [액세스를 REST API](https://dev.loganalytics.io/) 하는 등의 추가 통신 Insights 서비스에도 액세스할 수 있습니다.

### <a name="access"></a>Access
통신 서비스 리소스 페이지에서 시작 하 고 모니터 섹션 내의 왼쪽 탐색 영역에서 "로그"를 클릭 하 여 쿼리에 액세스할 수 있습니다.

:::image type="content" source="media\log-analytics\access-log-analytics.png" alt-text="Log Analytics 탐색":::

여기에는 통신 서비스에 사용할 수 있는 모든 [기본 쿼리 팩](../../../azure-monitor/logs/query-packs.md#default-query-pack) 이 포함 된 모달 화면이 표시 되 고, 왼쪽으로 이동 하는 데 사용할 수 있는 쿼리 팩 목록이 표시 됩니다.

:::image type="content" source="media\log-analytics\log-analytics-modal-resource.png" alt-text="log analytics 쿼리 모달" lightbox="media\log-analytics\log-analytics-modal-resource.png":::

모달 화면을 닫은 경우에도 다양 한 쿼리 팩으로 이동 하 여 진단 설정에서 사용 하도록 설정한 로그의 스키마 및 메트릭을 기반으로 테이블 형식의 데이터에 직접 액세스할 수 있습니다. 여기에서 [KQL (Kusto)](/azure/data-explorer/kusto/query/)를 사용 하 여 데이터에서 고유한 쿼리를 만들 수 있습니다. [Log Analytics 쿼리](../../../azure-monitor/logs/queries.md) 에 대해 자세히 읽어 쿼리를 사용 하 고 편집 하 고 만드는 방법에 대해 자세히 알아보세요.

:::image type="content" source="media\log-analytics\log-analytics-queries-resource.png" alt-text="리소스의 Log Analytics 쿼리" lightbox="media\log-analytics\log-analytics-queries-resource.png":::

:::image type="content" source="media\log-analytics\log-analytics-tables-resource.png" alt-text="리소스의 Log Analytics 테이블" lightbox="media\log-analytics\log-analytics-tables-resource.png":::

## <a name="default-query-packs-for-call-summary-and-call-diagnostic-logs"></a>호출 요약 및 호출 진단 로그에 대 한 기본 쿼리 팩
다음은 호출 요약에 대 한 [기본 쿼리 팩](../../../azure-monitor/logs/query-packs.md#default-query-pack)의 각 쿼리에 대 한 설명 이며, 사용할 수 있는 각 쿼리에 대 한 코드 예제 및 예제 출력을 포함 하 여 [진단 로그를 호출](call-logs-azure-monitor.md) 합니다.
### <a name="call-overview-queries"></a>호출 개요 쿼리
#### <a name="number-of-participants-per-call"></a>통화 당 참가자 수

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

#### <a name="number-of-participants-per-group-call"></a>그룹 통화 당 참가자 수

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

:::image type="content" source="media\log-analytics\participants-group-sample.png" alt-text="그룹 호출 쿼리 별 참여자":::

#### <a name="ratio-of-call-types"></a>호출 형식의 비율

```
// Ratio of call types
ACSCallSummary
| summarize call_types=dcount(CorrelationId) by CallType
| render piechart title="Call Type Ratio"

```

샘플 출력:

:::image type="content" source="media\log-analytics\ratio-call-types-sample.png" alt-text="호출 형식 쿼리의 비율":::

#### <a name="call-duration-distribution"></a>통화 기간 분포

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

#### <a name="call-duration-percentiles"></a>통화 기간 백분위 수

```
// Call duration percentiles
ACSCallSummary
| distinct CorrelationId, CallDuration
| summarize avg(CallDuration), percentiles(CallDuration, 50, 90, 99)
```

샘플 출력:

:::image type="content" source="media\log-analytics\call-duration-percentile-example.png" alt-text="호출 기간 백분위 수 쿼리":::

### <a name="endpoint-information-queries"></a>끝점 정보 쿼리

#### <a name="number-of-endpoints-per-call"></a>호출 당 끝점 수

```
// Count number of calls and endpoints,
// and print average endpoints per call
ACSCallSummary
| distinct CorrelationId, EndpointId
| summarize num_endpoints=count(), num_calls=dcount(CorrelationId)
| extend avg_endpoints = todecimal(num_endpoints) / todecimal(num_calls)
```

샘플 출력:

:::image type="content" source="media\log-analytics\endpoints-per-call.png" alt-text="호출 쿼리당 끝점 수":::

#### <a name="ratio-of-sdk-versions"></a>SDK 버전의 비율

```
// Ratio of SDK Versions
ACSCallSummary
| distinct CorrelationId, ParticipantId, EndpointId, SdkVersion
| summarize sdk_counts=count() by SdkVersion
| order by SdkVersion asc
| render piechart title="SDK Version Ratio"
```

샘플 출력:

:::image type="content" source="media\log-analytics\sdk-ratio-sample.png" alt-text="SDK 버전의 비율을 보여 주는 원형 차트입니다."::: :::image type="content" source="media\log-analytics\sdk-ratio-table-sample.png" alt-text="SDK 버전을 보여 주는 표":::

#### <a name="ratio-of-os-versions-simplified-os-name"></a>OS 버전 비율 (간소화 된 OS 이름)

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

:::image type="content" source="media\log-analytics\os-version-graph.png" alt-text="운영 체제 비율을 보여 주는 원형 차트":::
:::image type="content" source="media\log-analytics\os-version-table.png" alt-text="OS 버전을 보여 주는 표":::

### <a name="media-stream-queries"></a>미디어 스트림 쿼리
#### <a name="streams-per-call"></a>호출 당 스트림

```
// Count number of calls and streams,
// and print average streams per call
ACSCallDiagnostics
| summarize num_streams=count(), num_calls=dcount(CorrelationId)
| extend avg_streams = todecimal(num_streams) / todecimal(num_calls)
```
샘플 출력:

:::image type="content" source="media\log-analytics\streams-call-output.png" alt-text="호출 쿼리당 스트림 수":::

#### <a name="streams-per-call-histogram"></a>호출 히스토그램 당 스트림

```
// Distribution of streams per call
ACSCallDiagnostics
| summarize streams_per_call=count() by CorrelationId
| summarize stream_counts=count() by streams_per_call
| order by streams_per_call asc
| render columnchart title="Streams per call histogram"
```

:::image type="content" source="media\log-analytics\streams-call-histogram.png" alt-text="호출 히스토그램 당 스트림 수":::

#### <a name="ratio-of-media-types"></a>미디어 유형 비율

```
// Ratio of media types by call
ACSCallDiagnostics
| summarize media_types=count() by MediaType
| render piechart title="Media Type Ratio"
```

:::image type="content" source="media\log-analytics\media-type-ratio.png" alt-text="미디어 유형 비율을 보여 주는 원형 차트":::

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

#### <a name="poor-jitter-quality"></a>잘못 됨 지터 품질
```
// Get proportion of calls with poor quality jitter
// (defined as jitter being higher than 30ms)
ACSCallDiagnostics
| extend JitterQuality = iff(JitterAvg > 30, "Poor", "Good")
| summarize count() by JitterQuality
| render piechart title="Jitter Quality"
```

:::image type="content" source="media\log-analytics\jitter-quality.png" alt-text="지터 품질":::


#### <a name="packetlossrate-quality"></a>PacketLossRate 품질
```
// Get proportion of calls with poor quality packet loss
// rate (defined as packet loss being higher than 10%)
ACSCallDiagnostics
| extend PacketLossRateQuality = iff(PacketLossRateAvg > 0.1, "Poor", "Good")
| summarize count() by PacketLossRateQuality
| render piechart title="Packet Loss Rate Quality"
```

:::image type="content" source="media\log-analytics\packet-loss-quality.png" alt-text="패킷 손실 요금 품질":::

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

### <a name="parameterizable-queries"></a>Parameterizable 쿼리

#### <a name="daily-calls-in-the-last-week"></a>지난 주에 매일 호출
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

:::image type="content" source="media\log-analytics\calls-per-week.png" alt-text="지난 주에 매일 호출":::

#### <a name="calls-per-hour-in-last-day"></a>마지막 날의 시간당 호출 수
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

:::image type="content" source="media\log-analytics\calls-per-hour.png" alt-text="시간당 통화 호출":::