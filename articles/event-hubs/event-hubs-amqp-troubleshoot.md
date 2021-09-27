---
title: Azure Event Hubs의 AMQP 오류 문제 해결 | Microsoft Docs
description: Azure Event Hubs를 사용할 때 발생할 수 있는 AMQP 오류 목록과 해당 오류의 원인을 제공합니다.
ms.topic: article
ms.date: 09/20/2021
ms.openlocfilehash: 7e73c85f65a6ea9edef36ab810110e576b7ea0c0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649400"
---
# <a name="amqp-errors-in-azure-event-hubs"></a>Azure Event Hubs의 AMQP 오류
이 문서에는 Azure Event Hubs의 AMQP를 사용할 때 발생하는 몇 가지 오류가 나와 있습니다. 모두 서비스의 표준 동작입니다. 자동으로 연결/링크를 다시 만드는 연결/링크에 대한 호출의 발신/수신으로 해당 문제를 방지할 수 있습니다.

## <a name="link-is-closed"></a>링크가 닫힘 
AMQP 연결 및 링크가 활성 상태이지만 30분 동안 링크를 사용하여 호출(예: 발신 또는 수신)이 없는 경우 다음과 같은 오류가 표시됩니다. 따라서 링크가 닫힙니다. 연결은 여전히 열려 있습니다.

" AMQP:link: detach-forced: The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker because of errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:30:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp: 2/16/2018 11:10:40 PM "

## <a name="connection-is-closed"></a>연결이 닫힘
작업이 없고(유휴) 새 링크가 5분 내에 만들어지지 않아 연결의 모든 링크가 닫히면 AMQP 연결에 다음 오류가 표시됩니다.

" Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null} "

## <a name="link-isnt-created"></a>링크가 만들어지지 않음 
새 AMQP 연결이 만들어졌지만 AMQP 연결을 만든 후 1분 이내에 링크가 만들어지지 않은 경우 이 오류가 표시됩니다.

" Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null} "

## <a name="next-steps"></a>다음 단계
AMQP에 대해 자세히 알아보려면 [AMQP 1.0 프로토콜 가이드](../service-bus-messaging/service-bus-amqp-protocol-guide.md)를 참조하세요.
