---
title: Azure Functions 지역 재해 복구 및 고가용성
description: 중복성을 위해 지리적 지역을 사용하고 Azure Functions에서 장애 조치(failover)를 수행하는 방법입니다.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/27/2021
ms.openlocfilehash: 27730e2b6c1ed760f43a054901d16b58ca80acd9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645230"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Azure Functions 지리적 재해 복구

전체 Azure 지역 또는 데이터 센터에서 가동 중지 시간이 발생하는 경우 중요 비즈니스용 코드는 다른 지역에서 처리를 계속해야 합니다. 이 문서에서는 재해 복구를 허용하는 함수를 배포하는 데 사용할 수 있는 몇 가지 전략을 설명합니다.

## <a name="basic-concepts"></a>기본 개념

Azure Functions 특정 지역의 함수 앱에서 실행됩니다. 사용할 수 있는 기본 제공 중복성이 없습니다. 중단 시 실행 손실을 방지하기 위해 동일한 함수를 여러 지역의 함수 앱에 중복 배포할 수 있습니다.  

여러 지역에서 동일한 함수 코드를 실행하는 경우 다음 두 가지 패턴을 고려해야 합니다.

| 무늬 | Description |
| --- | --- |
|**활성/활성** | 두 영역의 함수는 중복된 방식으로 또는 회전 방식으로 이벤트를 적극적으로 실행하고 처리합니다. 중요한 HTTP 트리거 함수에 [Azure Front Door](../frontdoor/front-door-overview.md) 함께 활성/활성 패턴을 사용하는 것이 좋습니다. |
|**활성/수동** | 함수는 이벤트를 수신하는 지역에서 적극적으로 실행되지만 두 번째 지역의 동일한 함수는 유휴 상태로 유지됩니다.  장애 조치(failover)가 필요한 경우 두 번째 지역이 활성화되고 처리를 인계합니다. Service Bus 및 Event Hub 트리거 함수와 같이 HTTP가 아닌 이벤트 기반 함수에 이 패턴을 권장합니다.

다중 지역 배포에 대한 자세한 내용은 [고가용성 다중 지역 웹 애플리케이션](/azure/architecture/reference-architectures/app-service-web-app/multi-region)의 지침을 참조하세요.

## <a name="redundancy-for-http-trigger-functions"></a>HTTP 트리거 함수에 대한 중복성

활성/활성 패턴은 HTTP 트리거 함수에 가장 적합한 배포 모델입니다. 이 경우 [Azure Front Door](../frontdoor/front-door-overview.md) 사용하여 두 지역 간의 요청을 조정해야 합니다. Azure Front Door 여러 지역에서 실행되는 함수 간에 HTTP 요청을 라우팅하고 라운드 로빈할 수 있습니다. 또한 각 엔드포인트의 상태를 주기적으로 확인합니다. 한 지역의 함수가 상태 검사에 대한 응답을 중지하면 Azure Front Door 순환에서 벗어나 나머지 정상 함수에만 트래픽을 전달합니다.  

![Azure Front Door 및 함수에 대한 아키텍처](media/functions-geo-dr/front-door.png)  

## <a name="redundancy-for-non-http-trigger-functions"></a>HTTP가 아닌 트리거 함수에 대한 중복성

다른 서비스의 이벤트를 사용하는 함수의 중복성에는 관련 서비스의 장애 조치(failover) 패턴에서 작동하는 다른 패턴이 필요합니다. 

### <a name="activepassive-redundancy-for-non-http-trigger-functions"></a>HTTP가 아닌 트리거 함수에 대한 활성/수동 중복성

활성/수동은 단일 함수에서 각 메시지를 처리하는 방법을 제공하지만 재해가 발생한 경우 보조 지역으로 장애 조치(failover)하는 메커니즘을 제공합니다. 함수 앱은 Azure Service Bus 지역 복구 및 Azure Event Hubs [지역 복구와](../service-bus-messaging/service-bus-geo-dr.md) 같은 파트너 서비스의 [장애](../event-hubs/event-hubs-geo-dr.md)조치(failover) 동작에서 작동합니다. 보조 함수 앱은 연결된 장애 조치 서비스가 현재 활성 상태가 아니므로 _수동적인_ 것으로 간주되므로 함수 앱은 기본적으로 _유휴_ 상태입니다.

Azure Event Hubs 트리거를 사용하는 예제 토폴로지를 고려해 보겠습니다. 이 경우 활성/수동 패턴에는 다음 구성 요소가 필요합니다.

* 기본 및 보조 지역에 모두 배포되는 Azure Event Hub입니다.
* 지역 [재해를 사용하여](../service-bus-messaging/service-bus-geo-dr.md) 기본 및 보조 이벤트 허브를 페어링할 수 있습니다. 또한 연결 정보를 변경하지 않고 이벤트 허브에 연결하고 기본에서 보조로 전환하는 데 사용할 수 있는 _별칭을_ 만듭니다.
* 함수 앱은 기본 및 보조(장애 조치( failover) 지역 모두에 배포되며, 보조 지역의 앱은 메시지가 전송되지 않으므로 기본적으로 유휴 상태입니다.
* 함수 앱은 해당  이벤트 허브에 대한 직접(별칭이 아닌) 연결 문자열에서 트리거됩니다. 
* 이벤트 허브에 대한 게시자는 별칭 연결 문자열에 게시해야 합니다. 

![활성-수동 아키텍처 예](media/functions-geo-dr/active-passive.png)

장애 조치(failover) 전에 공유 별칭으로 보내는 게시자는 기본 이벤트 허브로 라우팅됩니다. 기본 함수 앱은 기본 이벤트 허브만을 대상으로 수신 대기합니다. 보조 함수 앱은 수동적이고 유휴 상태입니다. 장애 조치(failover)가 시작되는 즉시 공유 별칭으로 보내는 게시자는 보조 이벤트 허브로 라우팅됩니다. 이제 보조 함수 앱이 활성화되고 자동으로 트리거를 시작합니다.  보조 지역에 대한 효과적인 장애 조치(failover)는 이벤트 허브에서 전적으로 구동될 수 있으며, 함수는 해당 이벤트 허브가 활성 상태인 경우에만 활성화됩니다.

[Service Bus](../service-bus-messaging/service-bus-geo-dr.md) 및 Event Hubs 장애 조치(failover)에 대한 정보 [및](../event-hubs/event-hubs-geo-dr.md)고려 사항에 대해 자세히 읽어보십시오.

### <a name="activeactive-redundancy-for-non-http-trigger-functions"></a>HTTP가 아닌 트리거 함수에 대한 활성/활성 중복성

HTTP가 아닌 트리거 함수에 대해 활성/활성 배포를 수행할 수 있습니다. 그러나 두 활성 영역이 상호 작용하거나 서로 조정하는 방법을 고려해야 합니다. 동일한 Service Bus 큐에서 각 트리거를 사용하여 두 지역에 동일한 함수 앱을 배포하는 경우 해당 큐의 큐를 해제할 때 경쟁 소비자 역할을 합니다. 이는 각 메시지가 인스턴스 중 하나에서만 처리되고 있음을 의미하지만 단일 Service Bus 인스턴스에 여전히 단일 실패 지점이 있음을 의미합니다. 

대신 두 개의 Service Bus 큐를 배포할 수 있습니다. 하나는 주 지역에, 다른 하나는 보조 지역에 있습니다. 이 경우 각각 해당 지역에서 활성화된 Service Bus 큐를 가리키는 두 개의 함수 앱이 있을 수 있습니다. 이 토폴로지의 과제는 큐 메시지가 두 지역 간에 분산되는 방식입니다.  이는 각 게시자가 두 지역에 메시지를 게시하려고 시도하고 각 메시지가 *두* 활성 함수 앱에서 처리되는 경우가 많습니다. 이렇게 하면 원하는 활성/활성 패턴이 만들어지지만 컴퓨팅 중복과 데이터 통합 시기 또는 방법에 대한 다른 문제도 발생합니다. 이러한 문제 때문에 HTTPS가 아닌 트리거 함수에 활성/수동 패턴을 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Front Door 만들기](../frontdoor/quickstart-create-front-door.md)
* [Event Hubs 장애 조치(failover) 고려 사항](../event-hubs/event-hubs-geo-dr.md#considerations)
