---
title: Azure Service Bus의 AMQP 오류 문제 해결 | Microsoft Docs
description: Azure Service Bus를 사용할 때 수신할 수 있는 AMQP 오류 목록과 해당 오류의 원인을 제공합니다.
ms.topic: article
ms.date: 09/20/2021
ms.openlocfilehash: 0a481f0dccc7a409ada51fb809065fd58db31aed
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570952"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Azure Service Bus의 AMQP 오류
이 문서에서는 Azure Service Bus의 AMQP를 사용할 때 발생하는 오류 중 일부를 제공합니다. 모두 서비스의 표준 동작입니다. 자동으로 연결/링크를 다시 만드는 연결/링크에 대한 호출의 발신/수신으로 해당 문제를 방지할 수 있습니다.

## <a name="link-is-closed"></a>링크가 닫힘 
AMQP 연결 및 링크가 활성 상태이지만 10분 동안 링크를 사용하여 호출(예: 발신 또는 수신)이 없는 경우 다음과 같은 오류가 표시됩니다. 따라서 링크가 닫힙니다. 연결은 여전히 열려 있습니다.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>연결이 닫힘
작업이 없고(유휴) 새 링크가 5분 내에 만들어지지 않아 연결의 모든 링크가 닫히면 AMQP 연결에 다음 오류가 표시됩니다.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>링크가 만들어지지 않음 
새 AMQP 연결이 만들어졌지만 AMQP 연결을 만든 후 1분 이내에 링크가 만들어지지 않은 경우 이 오류가 표시됩니다.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>다음 단계

AMQP 및 Service Bus에 대해 자세히 알아보려면 다음 링크를 방문하세요.

* [Service Bus AMQP 개요]
* [AMQP 1.0 프로토콜 가이드]
* [Windows Server용 Service Bus의 AMQP]

[Service Bus AMQP 개요]: service-bus-amqp-overview.md
[AMQP 1.0 프로토콜 가이드]: service-bus-amqp-protocol-guide.md
[Windows Server용 Service Bus의 AMQP]: /previous-versions/service-bus-archive/dn282144(v=azure.100)