---
title: Azure Express 경로 CrossConnnections 회로 배치 API
description: 이 문서에서는 Express 경로 연결 회로 배치 API에 대 한 Express 경로 파트너에 대 한 자세한 개요를 제공 합니다.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: mialdrid
ms.openlocfilehash: 3b464e0d9bb5c770109899f7aba8c9e0934d2064
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103187"
---
# <a name="expressroute-circuit-placement-api"></a>Express 경로 회로 배치 API

Express 경로 파트너 회로 배치 API를 통해 Express 경로 파트너는 특정 포트 쌍에 회로 연결을 프로 비전 할 수 있습니다. 특히, Express 경로 파트너가 한 피어 링 위치에서 여러 포트 쌍을 관리 하는 경우이 API를 사용 하 여 Express 경로 회로를 용이 하 게 할 포트 쌍을 선택할 수 있습니다.

이 API는 expressRouteCrossConnection 리소스 종류를 사용 합니다. 자세한 내용은 [express 경로 연결 API 개발 및 통합](cross-connections-api-development.md)을 참조 하세요.

## <a name="workflow"></a>워크플로

1. Express 경로 고객은 대상 Express 경로 회로의 서비스 키를 공유 합니다.

1. Express 경로 파트너는 expressRouteProviderPorts API를 사용 하 여 GET을 실행 하 여 대상 포트 쌍의 **PortPairDescription** 를 식별 합니다. Express 경로는 구독의 모든 포트 쌍에서 PortPairDescriptions 목록을 쿼리하거나 특정 피어 링 위치에 대 한 쿼리의 범위를 지정할 수 있습니다.

1. 대상 PortPairDescription 식별 되 면 Express 경로는 대상 포트 쌍으로 Express 경로 회로를 이동 하는 expressRouteCrossConnection (GET/PUT)를 수행 합니다.

ExpressRoute 파트너는 expressRouteCrossConnections 리소스에 대해 REST 작업을 발급하여 계층 2 및 계층 3 구성을 관리합니다.

## <a name="api-development-and-integration-steps"></a>API 개발 및 통합 단계

### <a name="get-using-the-expressrouteproviderports-api-to-list-port-pairs"></a>ExpressRouteProviderPorts API를 사용 하 여 포트 쌍 나열

Express 경로 파트너는 대상 공급자 구독 내의 모든 포트 쌍을 나열 하거나 특정 피어 링 위치 내에서 포트 쌍을 나열할 수 있습니다.

### <a name="to-get-a-list-of-all-port-pairs-for-a-provider"></a>공급자의 모든 포트 쌍 목록을 가져오려면

https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Network/expressRouteProviderPorts 

#### <a name="get-operation"></a>작업 가져오기

```rest
{
    "parameters": {
      "api-version": "2020-03-01",
      "subscriptionId": "subid"
    },
    "responses": {
        "200": {
          "body": {
            "value": [
              {
                "portPairDescriptor": "bvtazureixpportpair1",
                "id": "/subscriptions/subid/providers/Microsoft.Network/ExpressRouteProviderPort/bvtazureixpportpair1",
                "type": "Microsoft.Network/expressRouteProviderPort",
                "location": "uswest",
                "etag": "W/\"c0e6477e-8150-4d4f-9bf6-bb10e6acb63a\"",
                "properties": {
                    "portPairDescriptor": "bvtazureixpportpair",
                    "primaryAzurePort": "bvtazureixp01a",
                    "secondaryAzurePort": "bvtazureixp01b",
                    "peeringLocation": "SiliconValley",
                    "overprovisionFactor": 4,
                    "portBandwidthInMbps": 4000,
                    "usedBandwidthInMbps": 2500,
                    "remainingBandwidthInMbps": 1500
                }
              },
              {
                "portPairDescriptor": "bvtazureixpportpair2",
                "id": "/subscriptions/subid/providers/Microsoft.Network/ ExpressRouteProviderPort/bvtazureixpportpair2",
                "type": "Microsoft.Network/expressRouteProviderPort",
                "location": "uswest",
                "etag": "W/\"c0e6477e-8150-4d4f-9bf6-bb10e6acb63a\"",
                "properties": {
                    "portPairDescriptor": "bvtazureixpportpair2",
                    "primaryAzurePort": "bvtazureixp02a",
                    "secondaryAzurePort": "bvtazureixp02b",
                    "peeringLocation": "seattle",
                    "overprovisionFactor": 4,
                    "portBandwidthInMbps": 4000,
                    "usedBandwidthInMbps": 1200,
                    "remainingBandwidthInMbps": 1800
                }
              }
            ]
          }
        }
      }
    }
  }
}
```

**응답 상태 코드**

* 200 (OK) 요청이 성공 했습니다.그러면 포트 목록이 인출 됩니다.
* 4XX (잘못 된 요청) 유효성 검사 중 하나가 실패 했습니다. 예: 공급자 subid가 잘못 되었습니다.

### <a name="list-of-all-port-for-a-provider-for-a-particular-peering-location"></a>특정 피어 링 위치에 대 한 공급자의 모든 포트 목록

#### <a name="get"></a>GET

https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Network/expressRouteProviderPorts?location={locationName}

#### <a name="get-operation"></a>GET 작업

```rest
{
  "parameters": {
    "api-version": "2020-03-01",
    "locationName": "SiliconValley",
    "subscriptionId": "subid"
  },
  "responses": {
    "200": {
      "body": {
        "value": [
          {
            "portPairDescriptor": "bvtazureixpportpair1",
            "id": "/subscriptions/subid/providers/Microsoft.Network/ ExpressRouteProviderPort /bvtazureixpportpair1",
            "type": "Microsoft.Network/expressRouteProviderPort",
            "location": "uswest",
            "etag": "W/\"c0e6477e-8150-4d4f-9bf6-bb10e6acb63a\"",
            "properties": {
              "portPairDescriptor": "bvtazureixpportpair",
              "primaryAzurePort": "bvtazureixp01a",
              "secondaryAzurePort": "bvtazureixp01b",
              "peeringLocation": "SiliconValley",
              "overprovisionFactor": 4,
              "portBandwidthInMbps": 4000,
              "usedBandwidthInMbps": 2500,
              "remainingBandwidthInMbps": 1500
            }
          }
        ]
      }
    }
  }
}
```

**응답 상태 코드**

* 200 (OK) 요청이 성공 했습니다. 그러면 포트 목록이 인출 됩니다.
* 4XX (잘못 된 요청) 유효성 검사 중 하나가 실패 했습니다. 예: 공급자 subid가 올바르지 않거나 위치가 잘못 되었습니다.

포트 쌍 설명자 ID를 사용 하 여 특정 포트의 포트 세부 정보를 가져옵니다.

#### <a name="get"></a>GET

https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Network/expressRouteProviderPorts/{portPairDescriptor}

#### <a name="get-operation"></a>GET 작업

```rest
{
  "parameters": {
    "api-version": "2020-03-01",
    "portPairDescriptor": " bvtazureixpportpair1",
    "subscriptionId": "subid"
  },
  "responses": {
    "200": {
      "body": {
        "value": [
          {
            "portPairDescriptor": "bvtazureixpportpair1",
            "id": "/subscriptions/subid/providers/Microsoft.Network/ExpressRouteProviderPort/bvtazureixpportpair1",
            "type": "Microsoft.Network/expressRouteProviderPort",
            "location": "uswest",
            "etag": "W/\"c0e6477e-8150-4d4f-9bf6-bb10e6acb63a\"",
            "properties": {
              "portPairDescriptor": "bvtazureixpportpair",
              "primaryAzurePort": "bvtazureixp01a",
              "secondaryAzurePort": "bvtazureixp01b",
              "peeringLocation": "SiliconValley",
              "overprovisionFactor": 4,
              "portBandwidthInMbps": 4000,
              "usedBandwidthInMbps": 2500,
              "remainingBandwidthInMbps": 15
            }
          }
        ]
      }
    }
  }
}
```

**상태 코드 설명**

* 200 (OK) 요청이 성공 했습니다. 포트 세부 정보를 페치합니다.
* 204 언급 된 설명자 ID를 사용 하는 포트 쌍을 사용할 수 없습니다.
* 4XX (잘못 된 요청) 유효성 검사 중 하나가 실패 했습니다. 예: 공급자 subid가 잘못 되었습니다.

### <a name="put-expressroutecrossconnection-api-to-move-a-circuit-to-a-specific-port-pair"></a>회로를 특정 포트 쌍으로 이동 하는 expressRouteCrossConnection API 배치

대상 포트 쌍의 portPairDescriptor 식별 되 면 Express 경로는 [EXPRESSROUTECROSSCONNECTION API](/rest/api/expressroute/express-route-cross-connections/create-or-update) 를 사용 하 여 express 경로 회로를 특정 포트 쌍으로 이동할 수 있습니다.

현재이 API는 공급자가 회로의 프로 비전 상태를 업데이트 하는 데 사용 됩니다. 이 API는 공급자가 회로의 포트 쌍을 업데이트 하는 데 사용 됩니다.

현재 primaryAzurePort 및 secondaryAzurePort는 읽기 전용 속성입니다. 이제 이러한 포트에 대 한 읽기 전용 속성을 사용 하지 않도록 설정 했습니다.

#### <a name="put"></a>PUT

https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/expressRouteCrossConnections/{crossConnectionName}?api-version=2021-02-01

#### <a name="put-operation"></a>PUT 작업

```rest
{
"parameters": {
    "api-version": "2020-03-01",
    "crossConnectionName": "The name of the cross connection",
    "subscriptionId": "subid"
  }
},
{
Request   "body": {
    " primaryAzurePort ": " bvtazureixp03a"
     "secondaryAzurePort": "bvtazureixp03b",
  }
}
Response:
{
  "name": "<circuitServiceKey>",
  "id": "/subscriptions/subid/resourceGroups/CrossConnectionSiliconValley/providers/Microsoft.Network/expressRouteCrossConnections/<circuitServiceKey>",
  "type": "Microsoft.Network/expressRouteCrossConnections",
  "location": "brazilsouth",
  "properties": {
    "provisioningState": "Enabled",
    "expressRouteCircuit": {
      "id": "/subscriptions/subid/resourceGroups/ertest/providers/Microsoft.Network/expressRouteCircuits/er1"
    },
    "peerings": [],
    "peeringLocation": "SiliconValley",
    "bandwidthInMbps": 1000,
    "primaryAzurePort": "bvtazureixp03a",
    "secondaryAzurePort": "bvtazureixp03b",
    "sTag": 2,
    "serviceProviderProvisioningState": "NotProvisioned"
  }
}
```

## <a name="next-steps"></a>다음 단계

모든 ExpressRoute REST API에 대한 자세한 내용은 [ExpressRoute REST API](/rest/api/expressroute/)를 참고하세요.