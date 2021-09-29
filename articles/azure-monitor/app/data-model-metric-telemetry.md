---
title: 메트릭 원격 분석을 위한 데이터 모델 - Azure Application Insights
description: 메트릭 원격 분석을 위한 Azure Application Insights 데이터 모델
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: df3ce8715134aa51ee819210cad4caf126f0e492
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129229604"
---
# <a name="metric-telemetry-application-insights-data-model"></a>메트릭 원격 분석: Application Insights 데이터 모델

[Application Insights](./app-insights-overview.md)에서 지원하는 메트릭 원격 분석에는 두 가지 유형, 즉 단일 측정 및 미리 집계된 메트릭이 있습니다. 단일 측정은 이름 및 값만 포함합니다. 미리 집계된 메트릭은 집계 간격에서 메트릭의 최소값 및 최대값과 해당 표준 편차를 지정합니다.

미리 집계된 메트릭 원격 분석은 집계 기간을 1분으로 가정합니다.

Application Insights에서는 잘 알려진 몇 가지 메트릭 이름을 지원합니다. 이러한 메트릭은 performanceCounters 테이블에 배치되었습니다.

시스템 및 프로세스 카운터를 나타내는 메트릭:

| **.NET 이름**             | **플랫폼 독립적 이름** | **REST API 이름** | **설명**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | 진행 중인 작업... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | 총 컴퓨터 CPU
| `\Memory\Available Bytes`                 | 진행 중인 작업... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | 컴퓨터에서 실행 중인 프로세스를 사용할 수 있는 실제 메모리 양을 바이트로 보여줍니다. 0으로 초기화된 사용 가능한 대기 메모리 목록의 공간 양을 합하여 계산됩니다. 사용 가능한 메모리가 준비되었습니다. 0으로 초기화된 메모리는 0으로 채워진 메모리 페이지로 구성되어 나중에 프로세스가 이전 프로세스에서 사용된 데이터를 확인하지 못하도록 방지합니다. 대기 메모리는 디스크에 대한 루트에 있는 프로세스의 작업 집합(해당 실제 메모리)에서 제거되었지만 회수할 수 있는 메모리입니다. [메모리 개체](/previous-versions/ms804008(v=msdn.10))를 참조하세요.
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | 진행 중인 작업... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | 애플리케이션을 호스트하는 프로세스의 CPU
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | 진행 중인 작업... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | 애플리케이션을 호스트하는 프로세스에 사용되는 메모리
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | 진행 중인 작업... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | 애플리케이션을 호스트하는 프로세스의 I/O 작업 실행 속도
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | 진행 중인 작업... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | 애플리케이션에서 처리되는 요청 속도 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | 진행 중인 작업... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | 애플리케이션에서 throw하는 예외 속도
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | 진행 중인 작업... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | 평균 요청 실행 시간
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | 진행 중인 작업... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | 큐에서 처리를 대기 중인 요청 수

## <a name="name"></a>이름

Application Insights 포털 및 UI에서 참조하려는 메트릭의 이름입니다. 

## <a name="value"></a>값

단일 측정 값입니다. 집계의 개별 측정값의 합계입니다.

## <a name="count"></a>개수

집계된 메트릭의 메트릭 가중치입니다. 측정값에 대해서는 설정하지 않아야 합니다.

## <a name="min"></a>최소값

집계된 메트릭의 최소값입니다. 측정값에 대해서는 설정하지 않아야 합니다.

## <a name="max"></a>최대

집계된 메트릭의 최대값입니다. 측정값에 대해서는 설정하지 않아야 합니다.

## <a name="standard-deviation"></a>표준 편차

집계된 메트릭 표준 편차입니다. 측정값에 대해서는 설정하지 않아야 합니다.

## <a name="custom-properties"></a>사용자 지정 속성

사용자 지정 속성이 `CustomPerfCounter` 설정된 `true` 메트릭은 메트릭이 Windows 성능 카운터를 나타낸다는 것을 나타냅니다. 이러한 메트릭은 performanceCounters 테이블에 배치되었습니다. customMetrics에 위치하지 않습니다. 또한 이 측정값의 이름은 구문 분석하여 범주, 카운터 및 인스턴스 이름을 추출합니다.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>다음 단계

- [사용자 지정 이벤트 및 메트릭용 Application Insights API](./api-custom-events-metrics.md#trackmetric) 사용 방법을 알아봅니다.
- Application Insights 형식 및 데이터 모델에 대한 자세한 내용은 [데이터 모델](data-model.md)을 참조하세요.
- Application Insights에서 지원되는 [플랫폼](./platforms.md)을 확인합니다.
