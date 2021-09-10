---
title: REST API를 사용하여 Azure Virtual Machine 사용량 데이터 가져오기
description: Azure REST API를 사용하여 가상 머신에 대한 사용률 메트릭을 수집합니다.
author: rloutlaw
ms.service: virtual-machines
ms.subservice: monitoring
ms.custom: REST
ms.topic: how-to
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 536a3040bda2fec6aa5139949d31ef47bd2bbd9e
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694923"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>REST API를 사용하여 가상 머신 사용률 메트릭 가져오기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

이 예제에서는 [Azure REST API](/rest/api/azure/)를 사용하여 Linux 가상 머신의 CPU 사용량을 검색하는 방법을 보여 줍니다.

REST API에 대한 전체 참조 설명서 및 추가 샘플은 [Azure Monitor REST 참조](/rest/api/monitor)에서 사용할 수 있습니다. 

## <a name="build-the-request"></a>요청 빌드

다음 GET 요청을 사용하여 가상 머신의 [CPU 사용률 메트릭](../../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachines)을 수집합니다.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>요청 헤더

다음과 같은 헤더가 필요합니다. 

|요청 헤더|Description|  
|--------------------|-----------------|  
|*Content-Type:*|필수 사항입니다. `application/json`로 설정합니다.|  
|*권한 부여*|필수 사항입니다. 유효한 `Bearer` [액세스 토큰](/rest/api/azure/#authorization-code-grant-interactive-clients)으로 설정합니다. |  

### <a name="uri-parameters"></a>URI 매개 변수

| 이름 | Description |
| :--- | :---------- |
| subscriptionId | Azure 구독을 식별하는 구독 ID입니다. 구독이 여러 개인 경우 [여러 구독으로 작업](/cli/azure/manage-azure-subscriptions-azure-cli)을 참조합니다. |
| resourceGroupName | 리소스와 연결된 Azure 리소스 그룹의 이름입니다. Azure Resource Manager API, CLI 또는 포털에서 이 값을 가져올 수 있습니다. |
| vmname | Azure Virtual Machine의 이름입니다. |
| metricnames | 쉼표로 구분된 유효한 [Load Balancer 메트릭](../../load-balancer/load-balancer-standard-diagnostics.md) 목록입니다. |
| api-version | 요청에 사용할 API 버전입니다.<br /><br /> 이 문서에서는 위 URL에 포함되어 있는 api-version `2018-01-01`을 다룹니다.  |
| timespan | 반환된 메트릭의 시간 범위를 정의하는 `startDateTime_ISO/endDateTime_ISO` 형식의 문자열입니다. 이 선택적 매개 변수는 예제에서 하루 동안의 데이터를 반환하도록 설정되어 있습니다. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>요청 본문

이 작업에는 요청 본문이 필요하지 않습니다.

## <a name="handle-the-response"></a>응답 처리

상태 코드 200은 메트릭 값 목록이 성공적으로 반환되면 반환됩니다. 오류 코드의 전체 목록은 [참조 설명서](/rest/api/monitor/metrics/list#errorresponse)에서 사용할 수 있습니다.

## <a name="example-response"></a>예제 응답 

```json
{
    "cost": 0,
    "timespan": "2018-06-08T23:48:10Z/2018-06-09T00:48:10Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "Percentage CPU",
                "localizedValue": "Percentage CPU"
            },
            "unit": "Percent",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-08T23:48:00Z",
                            "average": 0.44
                        },
                        {
                            "timeStamp": "2018-06-08T23:49:00Z",
                            "average": 0.31
                        },
                        {
                            "timeStamp": "2018-06-08T23:50:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:51:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:52:00Z",
                            "average": 0.285
                        } ]
                } ]
        } ]
}
```
