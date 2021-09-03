---
title: Azure Functions에서 Monitor 메트릭 사용
description: Azure Monitor 메트릭을 사용하여 Azure Application Insights에서 수집하고 여기에 저장된 Azure Functions 원격 분석 데이터를 보고 쿼리하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 07/4/2021
ms.openlocfilehash: b5f0b483295cccff8caf97503233e1e7881173cf
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113568617"
---
# <a name="using-azure-monitor-metric-with-azure-functions"></a>Azure Functions에서 Azure Monitor 메트릭 사용

Azure Functions는 실행 중에 함수 앱에서 생성된 메트릭을 분석할 수 있도록 Azure Monitor 메트릭과 통합됩니다. 자세한 내용은 [Azure Monitor 메트릭 개요](../azure-monitor/essentials/data-platform-metrics.md)를 참조하세요. 이 메트릭은 함수 앱이 App Service 플랫폼에서 실행되는 방식을 나타냅니다. 사용량 플랜 비용을 예측하는 데 사용되는 리소스 사용량 데이터를 검토할 수 있습니다. 로그 데이터를 포함하여 함수 실행의 자세한 원격 분석을 조사하려면 Azure Monitor에서 [Application Insights](functions-monitoring.md)도 사용해야 합니다. 

> [!NOTE]
> 함수 앱이 Linux에서 실행되는 경우 Azure Monitor 메트릭은 현재 사용량 플랜에서 지원되지 않습니다.

## <a name="available-metrics"></a>사용 가능한 메트릭

Azure Monitor는 시계열 데이터베이스에 입력되는 모니터링되는 리소스 세트에서 숫자 데이터를 수집합니다. Azure Monitor는 함수와 기본 App Service 리소스에 관련된 메트릭을 수집합니다.   

### <a name="functions-specific-metrics"></a>함수 관련 메트릭

관심 있는 함수와 관련된 두 가지 메트릭이 있습니다.

| 메트릭 | Description |
| ---- | ---- |
| **FunctionExecutionCount** | 함수 실행 횟수는 함수 앱이 실행된 횟수를 나타냅니다. 이 값은 앱에서 함수가 실행되는 횟수와 서로 관련됩니다. |
| **FunctionExecutionUnits** | 함수 실행 단위는 실행 시간과 메모리 사용량의 조합입니다.  메모리 데이터는 현재 Azure Monitor를 통해 사용할 수 있는 메트릭이 아닙니다. 그러나 앱의 메모리 사용량을 최적화하려는 경우는 Application Insights에서 수집된 성능 카운터 데이터를 사용할 수 있습니다. 현재 이 메트릭은 Linux에서 실행되는 프리미엄 및 Dedicated(App Service) 계획에 지원되지 않습니다.|

이 메트릭은 특히 [사용량 플랜 비용을 예측](functions-consumption-costs.md)하는 경우 사용됩니다. 

### <a name="general-app-service-metrics"></a>일반 App Service 메트릭

함수 관련 메트릭과는 별도로, App Service 플랫폼은 함수 앱을 모니터링하는 데 사용할 수 있는 더 많은 메트릭을 구현합니다. 전체 목록은 [App Service 앱에 사용할 수 있는 메트릭](../app-service/web-sites-monitor.md#understand-metrics)을 참조하세요.

## <a name="accessing-metrics"></a>메트릭 액세스

[Azure Portal](https://portal.azure.com) 또는 REST API에서 [Azure Monitor 메트릭 탐색기](../azure-monitor/essentials/metrics-getting-started.md)를 사용하여 Monitor 메트릭 데이터를 가져올 수 있습니다. 

다음 예제에서는 Monitor 메트릭을 사용하여 사용량 플랜에 따라 함수 앱을 실행하는 비용을 예측할 수 있습니다. 사용량 플랜 비용 예측에 관한 자세한 내용은 [사용량 플랜 비용 예측](functions-consumption-costs.md)을 참조하세요.

[!INCLUDE [functions-monitor-metrics-consumption](../../includes/functions-monitor-metrics-consumption.md)]  

모니터 탐색기를 사용하여 메트릭을 보는 방법에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../azure-monitor/essentials/metrics-getting-started.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Functions 모니터링에 대해 자세히 알아봅니다.

+ [Azure Functions 모니터링](functions-monitoring.md)
+ [Application Insights에서 Azure Functions 원격 분석 분석](analyze-telemetry-data.md)
