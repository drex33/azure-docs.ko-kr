---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/04/2021
ms.author: glenga
ms.openlocfilehash: 34237e64aecfcd4059a9bc9fb3ae390281b3419a
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113567333"
---
#### <a name="portal"></a>[포털](#tab/portal)

[Azure Monitor 메트릭 탐색기](../articles/azure-monitor/essentials/metrics-getting-started.md)를 사용하여 사용 플랜 함수 앱에 대한 비용 관련 데이터를 그래픽 형식으로 볼 수 있습니다. 

1. [Azure Portal]에서 함수 앱으로 이동합니다.

1. 왼쪽 패널에서 **모니터링** 까지 아래로 스크롤하여 **메트릭** 을 선택합니다.  

1. **메트릭** 에서 **함수 실행 수** 및 **집계** 에 대한 **합계** 를 선택합니다. 이렇게 하면 선택한 기간 동안의 실행 수 합계가 차트에 추가됩니다.

    ![차트에 추가할 함수 앱 메트릭을 정의합니다.](./media/functions-monitor-metrics-consumption/monitor-metrics-add-metric.png)

1. **메트릭 추가** 를 선택하고 2-4단계를 반복하여 차트에 **함수 실행 단위** 를 추가합니다. 

결과 차트에는 선택한 시간 범위의 두 실행 메트릭의 합계가 모두 포함됩니다(이 경우 2시간).

![함수 실행 수 및 실행 단위의 그래프](./media/functions-monitor-metrics-consumption/monitor-metrics-execution-sum.png)

실행 단위 수가 실행 수보다 훨씬 크므로 차트에는 실행 단위만 표시됩니다.

이 차트에서는 2시간 동안 사용된 총 11억1000만 `Function Execution Units`(MB-밀리초)를 표시합니다. GB-초로 변환하려면 1024000으로 나눕니다. 이 예제에서 함수 앱은 `1110000000 / 1024000 = 1083.98` GB-초를 사용했습니다. 실행 시간에 대한 체험판 부여를 이미 사용하고 있다고 가정하고, 이 값을 사용하여 두 시간에 대한 비용을 제공하는 [Functions 가격 책정 페이지](https://azure.microsoft.com/pricing/details/functions/)에서 실행 시간의 현재 가격을 곱합니다. 

#### <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

[Azure CLI](/cli/azure/)에는 메트릭을 검색하는 명령이 있습니다. CLI는 로컬 명령 환경에서 사용하거나 [Azure Cloud Shell](../articles/cloud-shell/overview.md)을 사용하여 포털에서 직접 사용할 수 있습니다. 예를 들어 다음 [Az Monitor 메트릭 목록](/cli/azure/monitor/metrics#az_monitor_metrics_list) 명령은 이전에 사용된 것과 동일한 기간에 대한 시간별 데이터를 반환합니다.

`<AZURE_SUBSCRIPTON_ID>`을 명령을 실행하는 Azure 구독 ID로 바꾸어야 합니다.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

이 명령은 다음 예제와 같은 JSON 페이로드를 반환합니다.

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
이 특정 응답은 `2019-09-11T21:46`에서 `2019-09-11T23:18`로 앱이 11억 1000만 MB-밀리초(1083.98GB-초)를 사용하는 것을 보여 줍니다.

---

[Azure Portal]: https://portal.azure.com