---
title: Azure Functions에서 사용 플랜 비용 예측
description: Azure의 사용 플랜에서 함수 앱을 실행할 때 발생할 수 있는 비용을 보다 정확하게 예측하는 방법을 알아봅니다.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 9544dded7516b07ad7d919d08b0b9cd4e12d0607
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113567326"
---
# <a name="estimating-consumption-plan-costs"></a>소비 계획 비용 예측

현재는 Azure Functions에서 실행되는 앱에 대해 세 가지 유형의 호스팅 플랜이 있으며 각 플랜에는 다음과 같이 고유한 가격 책정 모델이 있습니다. 

| 계획 | 설명 |
| ---- | ----------- |
| [**Consumption**](consumption-plan.md) | 함수 앱이 실행되는 시간에 대해서만 요금이 청구됩니다. 이 플랜에는 구독별로 [체험판 부여][가격 책정 페이지]가 포함되어 있습니다.|
| [**Premium**](functions-premium-plan.md) | 사용 플랜과 동일한 기능 및 스케일링 메커니즘을 제공하지만 성능 및 VNET 액세스는 향상되었습니다. 비용은 선택한 가격 책정 계층을 기준으로 합니다. 자세한 정보는 [Azure Functions 프리미엄 플랜](functions-premium-plan.md)을 참조하세요. |
| [**전용(App Service)**](dedicated-plan.md) <br/>(기본 계층 이상) | 전용 VM 또는 격리에서 실행해야 하는 경우 사용자 지정 이미지를 사용하거나 과도한 App Service 요금제 용량을 사용하려고 합니다. [정기적인 App Service 요금 청구](https://azure.microsoft.com/pricing/details/app-service/)를 사용합니다. 비용은 선택한 가격 책정 계층을 기준으로 합니다.|

함수 성능 및 비용 요구 사항을 가장 잘 지원하는 플랜을 선택했습니다. 자세한 내용은 [Azure Functions 크기 조정 및 호스팅](functions-scale.md)을 참조하세요.

이 플랜의 결과로 비용이 계산되기 때문에 이 문서에서는 사용 플랜만 다룹니다. 이 문서는 [사용 플랜 비용 청구 FAQ](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ) 문서를 대체합니다.

Durable Functions 사용 플랜에서 실행할 수도 있습니다. Durable Functions를 사용할 때의 비용 고려 사항에 대한 자세한 정보는 [Durable Functions 청구](./durable/durable-functions-billing.md)를 참조하세요.

## <a name="consumption-plan-costs"></a>소비 계획 비용

단일 함수 실행의 *비용* 실행은 *GB-초* 단위로 측정됩니다. 실행 비용은 메모리 사용량과 실행 시간을 결합하여 계산됩니다. 더 오래 실행되는 함수는 더 많은 메모리를 사용하는 함수와 같이 더 많은 비용을 사용합니다. 

함수에서 사용되는 메모리 양이 일정하게 유지되는 경우를 고려합니다. 이 경우 비용은 간단한 곱셈으로 계산됩니다. 예를 들어 함수가 3초 동안 0.5GB를 사용한다고 가정합니다. 실행 비용은 `0.5GB * 3s = 1.5 GB-seconds`입니다. 

시간이 지남에 따라 메모리 사용량이 변경되기 때문에 계산은 기본적으로 시간에 따른 메모리 사용량의 정수 형식입니다.  시스템은 일정한 간격으로 프로세스의 메모리 사용량(자식 프로세스와 함께)을 샘플링하여 이 계산을 수행합니다. [가격 책정 페이지]에 설명된 대로 메모리 사용량은 가장 가까운 128-MB 버킷으로 반올림됩니다. 프로세스에서 160MB를 사용하는 경우 256MB에 대해 요금이 청구됩니다. 계산은 동일한 프로세스에서 여러 함수가 동시에 실행되는 동시성을 고려합니다.

> [!NOTE]
> CPU 사용량은 실행 비용에서 직접적으로 고려되지는 않지만 함수의 실행 시간에 영향을 미칠 경우 비용에 영향을 줄 수 있습니다.

HTTP 트리거 함수의 경우 함수 코드가 실행되기 전에 오류가 발생하는 경우 실행에 대한 요금이 청구되지 않습니다. 즉, API 키 유효성 검사 또는 App Service 인증/권한 부여 기능으로 인해 플랫폼의 401 응답이 실행 비용에서 계산되지 않습니다. 마찬가지로, 5xx 상태 코드 응답은 요청을 처리하는 함수 전에 플랫폼에서 발생하는 경우에는 계산되지 않습니다. 함수 코드 실행이 시작된 후 플랫폼에 의해 생성된 5xx 응답은 함수 코드에 의해 오류가 발생하지 않더라도 여전히 실행으로 계산됩니다.

## <a name="other-related-costs"></a>기타 관련 비용

모든 플랜에서 함수를 실행하는 전체 비용을 예측할 때 함수 런타임은 각각 별도로 청구되는 여러 Azure 서비스를 사용합니다. 함수 앱에 대한 가격 책정을 계산할 때 다른 Azure 서비스와 통합하는 트리거와 바인딩을 사용하려면 이러한 추가 서비스를 만들고 지불해야 합니다. 

사용 플랜에서 실행되는 함수의 경우 총 비용은 함수의 실행 비용과 대역폭 및 추가 서비스의 비용의 합계입니다. 

함수 앱 및 관련 서비스의 전체 비용을 예측하는 경우 [Azure 가격 책정 계산기](https://azure.microsoft.com/pricing/calculator/?service=functions)를 사용합니다. 

| 관련 비용 | 설명 |
| ------------ | ----------- |
| **스토리지 계정** | 각 함수 앱에는 [별도로 청구](https://azure.microsoft.com/pricing/details/storage/)되는 연결된 범용 [Azure Storage 계정](../storage/common/storage-introduction.md#types-of-storage-accounts)이 있어야 합니다. 이 계정은 함수 런타임에서 내부적으로 사용되지만 스토리지 트리거 및 바인딩에 사용할 수도 있습니다. 스토리지 계정이 없는 경우 함수 앱을 만들 때 하나의 계정이 만들어집니다. 자세한 내용은 [스토리지 계정 요구 사항](storage-considerations.md#storage-account-requirements)을 참조하세요.|
| **Application Insights** | Functions는 [Application Insights](../azure-monitor/app/app-insights-overview.md)를 사용하여 함수 앱에 고성능 모니터링 환경을 제공합니다. 필수는 아니지만 [Application Insights 통합을 사용하도록 설정](configure-monitoring.md#enable-application-insights-integration)해야 합니다. 원격 분석 데이터의 체험판 부여는 매달 포함됩니다. 자세한 정보는 [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)를 참조하세요. |
| **네트워크 대역폭** | 동일한 지역에 있는 Azure 서비스 간의 데이터 전송에 대해서는 지불하지 않습니다. 그러나 다른 지역 또는 Azure 외부로 아웃바운드 데이터를 전송하면 비용을 발생시킬 수 있습니다. 자세한 정보는 [대역폭 가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/bandwidth/)를 참조하세요. |

## <a name="behaviors-affecting-execution-time"></a>실행 시간에 영향을 주는 동작

다음 함수 동작은 실행 시간에 영향을 줄 수 있습니다.

+ **트리거 및 바인딩**: [함수 바인딩](functions-triggers-bindings.md)에 대한 입력을 읽고 함수 바인딩에 출력을 쓰는 데 걸리는 시간은 실행 시간으로 계산됩니다. 예를 들어 함수가 출력 바인딩을 사용하여 Azure 스토리지 큐에 메시지를 쓰는 경우 실행 시간에는 함수 비용 계산에 포함된, 메시지를 큐에 쓰는 데 걸리는 시간이 포함됩니다. 

+ **비동기 실행**: 함수가 비동기 요청(C#의 경우 `await`)의 결과를 기다리는 시간을 실행 시간으로 계산합니다. GB-초 계산은 함수의 시작 및 종료 시간과 해당 기간 동안의 메모리 사용량을 기반으로 합니다. CPU 작업을 기준으로 해당 시간에 발생하는 작업은 계산에서 제외됩니다. 비동기 작업 중에 [Durable Functions](durable/durable-functions-overview.md)를 사용하여 비용을 절감할 수 있습니다. 오케스트레이터 함수에서 기다리는 데 소요된 시간에 대해서는 요금이 청구되지 않습니다.

## <a name="viewing-cost-related-data"></a>비용 관련 데이터 보기

[청구서](../cost-management-billing/understand/download-azure-invoice.md)에서 실제 청구된 비용과 함께 **총 실행 - 함수** 및 **실행 시간 - 함수** 에 대한 비용 관련 데이터를 볼 수 있습니다. 그러나 이 청구서 데이터는 과거 청구서 기간에 대한 월별 집계입니다. 

### <a name="function-app-level-metrics"></a>함수 앱-수준 메트릭

함수의 비용 영향을 보다 잘 이해하기 위해 Azure Monitor를 사용하여 함수 앱에서 현재 생성하고 있는 비용 관련 메트릭을 볼 수 있습니다. 

[!INCLUDE [functions-monitor-metrics-consumption](../../includes/functions-monitor-metrics-consumption.md)]

### <a name="function-level-metrics"></a>함수 수준 메트릭

함수 실행 단위는 실행 시간과 메모리 사용량의 조합으로, 이를 통해 메모리 사용을 이해하는 데 어려운 메트릭을 사용할 수 있습니다. 메모리 데이터는 현재 Azure Monitor를 통해 사용할 수 있는 메트릭이 아닙니다. 그러나 앱의 메모리 사용량을 최적화하려는 경우는 Application Insights에서 수집된 성능 카운터 데이터를 사용할 수 있습니다.  

이를 아직 수행하지 않은 경우 [함수 앱에서 Application Insights를 사용하도록 설정](configure-monitoring.md#enable-application-insights-integration)합니다. 이 통합을 사용하도록 설정하면 [포털에서 이 원격 분석 데이터를 쿼리](analyze-telemetry-data.md#query-telemetry-data)할 수 있습니다. 

[Azure Portal] 또는 REST API에서 [Azure Monitor 메트릭 탐색기](../azure-monitor/essentials/metrics-getting-started.md)를 사용하여 Monitor 메트릭 데이터를 가져올 수 있습니다.

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [함수 앱 모니터링에 대한 자세한 정보를 알아봅니다](functions-monitoring.md)

[가격 책정 페이지]:https://azure.microsoft.com/pricing/details/functions/
[Azure Portal]: https://portal.azure.com
