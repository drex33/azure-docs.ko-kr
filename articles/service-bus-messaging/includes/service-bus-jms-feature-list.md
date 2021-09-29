---
title: 포함 파일
description: 포함 파일
services: service-bus-messaging
author: axisc
ms.service: service-bus-messaging
ms.topic: include
ms.date: 09/28/2021
ms.author: aschhab
ms.custom: include file
ms.openlocfilehash: 6d2207b01f6ea9eff3dd2d41e9738f756f3f9fff
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129220617"
---
다음 표에는 Azure Service Bus가 현재 지원하는 JMS(Java Message Service) 기능이 나와 있습니다. 지원되지 않는 기능도 표시됩니다.


| 기능 | API |상태 |
|---|---|---|
| 큐   | <ul> <li> JMSContext.createQueue(문자열 queueName) </li> </ul>| **지원됨** |
| 토픽   | <ul> <li> JMSContext.createTopic(문자열 topicName) </li> </ul>| **지원됨** |
| 임시 큐 |<ul> <li> JMSContext.createTemporaryQueue() </li> </ul>| **지원됨** |
| 임시 항목 |<ul> <li> JMSContext.createTemporaryTopic() </li> </ul>| **지원됨** |
| 메시지 생산자 /<br/> JMSProducer |<ul> <li> JMSContext.createProducer() </li> </ul>| **지원됨** |
| 큐 브라우저 |<ul> <li> JMSContext.createBrowser(Queue 큐) </li> <li> JMSContext.createBrowser(Queue 큐, 문자열 messageSelector) </li> </ul> | **지원됨** |
| 메시지 소비자/ <br/> JMSConsumer | <ul> <li> JMSContext.createConsumer(Destination 대상) </li> <li> JMSContext.createConsumer(Destination 대상, 문자열 messageSelector) </li> <li> JMSContext.createConsumer(Destination 대상, 문자열 messageSelector, 부울 noLocal)</li> </ul>  <br/> noLocal은 현재 지원되지 않습니다. | **지원됨** |
| 공유 내구성이 있는 구독 | <ul> <li> JMSContext.createSharedDurableConsumer(Topic 항목, 문자열 이름) </li> <li> JMSContext.createSharedDurableConsumer(Topic 항목, 문자열 이름, 문자열 messageSelector) </li> </ul>| **지원됨**|
| 공유되지 않는 내구성이 있는 구독 | <ul> <li> JMSContext.createDurableConsumer(Topic 항목, 문자열 이름) </li> <li> createDurableConsumer(Topic 항목, 문자열 이름, 문자열 messageSelector, 부울 noLocal) </li> </ul> <br/> noLocal은 현재 지원되지 않으므로 false로 설정해야 합니다. | **지원됨** |
| 공유 내구성이 없는 구독 |<ul> <li> JMSContext.createSharedConsumer(Topic 항목, 문자열 sharedSubscriptionName) </li> <li> JMSContext.createSharedConsumer(Topic 항목, 문자열 sharedSubscriptionName, 문자열 messageSelector) </li> </ul> | **지원됨** |
| 공유되지 않는 내구성이 없는 구독 |<ul> <li> JMSContext.createConsumer(Destination 대상) </li> <li> JMSContext.createConsumer(Destination 대상, 문자열 messageSelector) </li> <li> JMSContext.createConsumer(Destination 대상, 문자열 messageSelector, 부울 noLocal) </li> </ul> <br/> noLocal은 현재 지원되지 않으므로 false로 설정해야 합니다. | **지원됨** |
| 메시지 선택기 | 만든 소비자에 따라 다릅니다. | **지원됨** |
| 전달 지연(예약된 메시지) | <ul> <li> JMSProducer.setDeliveryDelay(긴 전달 지연) </li> </ul>|**지원됨**|
| 생성된 메시지 |<ul> <li> JMSContext.createMessage() </li> <li> JMSContext.createBytesMessage() </li> <li> JMSContext.createMapMessage() </li> <li> JMSContext.createObjectMessage(직렬화 가능 개체) </li> <li> JMSContext.createStreamMessage() </li> <li> JMSContext.createTextMessage() </li> <li> JMSContext.createTextMessage(문자열 텍스트) </li> </ul>| **지원됨** |
| 엔터티 간 트랜잭션 |<ul> <li> Connection.createSession(true, Session.SESSION_TRANSACTED) </li> </ul> | **지원됨** |
| 분산 트랜잭션 || 지원 안 함 |
