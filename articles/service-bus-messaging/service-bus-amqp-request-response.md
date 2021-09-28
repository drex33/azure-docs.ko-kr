---
title: Azure Service Bus의 AMQP 1.0 요청/응답 작업
description: 이 문서에서는 Microsoft Azure Service Bus의 AMQP 요청/응답 기반 작업 목록을 정의합니다.
ms.topic: article
ms.date: 09/27/2021
ms.openlocfilehash: bb7b3ea90678be8a462f59d700f4c18feac300e5
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129153561"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>Microsoft Azure Service Bus에서 AMQP 1.0: Microsoft Azure Service Bus 요청/응답 기반 작업

이 문서에서는 Microsoft Azure Service Bus 요청/응답 기반 작업 목록을 정의합니다. 이 정보는 AMQP Management Version 1.0 초안을 기반으로 합니다.  
  
Service Bus가 OASIS AMQP 기술 사양을 구현하고 빌드하는 방법을 설명하는 자세한 유선 수준 AMQP 1.0 프로토콜 가이드는 [Azure Service Bus 및 Event Hubs 프로토콜 가이드의 AMQP 1.0][AMQP 1.0 프로토콜 가이드]를 참조하세요.  
  
## <a name="concepts"></a>개념  
    
### <a name="brokered-message"></a>broker 저장 메시지  

AMQP 메시지에 매핑되는 Service Bus의 메시지를 나타냅니다. 이 매핑은 [Service Bus AMQP 프로토콜 가이드](service-bus-amqp-protocol-guide.md)에서 정의됩니다.  
  
## <a name="attach-to-entity-management-node"></a>엔터티 관리 노드에 연결  

이 문서에 설명된 모든 작업은 요청/응답 패턴을 따르며 엔터티로 범위가 지정되며 엔터티 관리 노드에 연결되어야 합니다.  
  
### <a name="create-link-for-sending-requests"></a>요청 전송을 위한 링크 만들기  

요청 전송을 위해 관리 노드에 대한 링크를 만듭니다.  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>응답 수신을 위한 링크 만들기  

관리 노드에서 응답 수신을 위한 링크를 만듭니다.  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>요청 메시지 전송  

요청 메시지를 전송합니다.  
트랜잭션을 지원하는 작업에 대한 옵션으로 트랜잭션 상태를 추가할 수 있습니다.

```
requestLink.sendTransfer(
        Message(
                properties: {
                        message-id: <request id>,
                        reply-to: "<my response link unique address>"
                },
                application-properties: {
                        "operation" -> "<operation>",
                }
        ),
        [Optional] State = transactional-state: {
                txn-id: <txn-id>
        }
)
```
  
### <a name="receive-a-response-message"></a>응답 메시지 수신  

응답 링크에서 응답 메시지를 수신합니다.  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
응답 메시지는 다음 형식으로 되어 있습니다.
  
```
Message(
properties: {
        correlation-id: <request id>
    },
    application-properties: {
            "statusCode" -> <status code>,
            "statusDescription" -> <status description>,
           },
)

```
  
### <a name="service-bus-entity-address"></a>Service Bus 엔터티 주소  

Service Bus 엔터티 주소는 다음과 같아야 합니다.  
  
|엔터티 유형|주소|예제|  
|-----------------|-------------|-------------|  
|queue|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|토픽|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|subscription|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>메시지 작업  
  
### <a name="message-renew-lock"></a>메시지 갱신 잠금  

큐 또는 구독에 설정된 잠금 기간을 기준으로 메시지 잠금을 확장합니다.  
  
#### <a name="request"></a>요청  

요청 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|operation|문자열|예|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|예|작업 서버 제한 시간(밀리초)입니다.|  
  
 요청 메시지 본문은 다음 엔터티와 함께 맵을 포함하는 amqp-value 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|uuid의 배열|예|갱신할 메시지 잠금 토큰입니다.|  

> [!NOTE]
> 여기에서 잠금 토큰은 수신된 AMQP 메시지의 `delivery-tag` 속성을 나타냅니다. 지연된 메시지를 수신하고 해당 잠금을 갱신하려면 `delivery-tag` 대신 메시지에 있는 속성 `lock-token`을 사용합니다. 
> 
  
#### <a name="response"></a>응답  

응답 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|예|HTTP 응답 코드 [RFC2616]<br /><br /> 200: OK – 성공, 그렇지 않으면 실패입니다.|  
|statusDescription|문자열|No|상태에 대한 설명입니다.|  
  
응답 메시지 본문은 다음 엔터티와 함께 맵을 포함하는 amqp-value 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|만료|타임 스탬프 배열|예|요청 잠금 토큰에 해당하는 메시지 잠금 토큰 새 만료입니다.|  
  
### <a name="peek-message"></a>메시지 보기  

잠그지 않고 메시지를 봅니다.  
  
#### <a name="request"></a>요청  

요청 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|operation|문자열|예|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|예|작업 서버 제한 시간(밀리초)입니다.|  
  
요청 메시지 본문은 다음 엔터티와 함께 **맵** 을 포함하는 **amqp-value** 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|예|보기를 시작할 시퀀스 번호입니다.|  
|`message-count`|int|예|보려는 최대 메시지 수입니다.|  
  
#### <a name="response"></a>응답  

응답 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|예|HTTP 응답 코드 [RFC2616]<br /><br /> 200: OK – 더 많은 메시지가 있음<br /><br /> 204: 콘텐츠 없음 – 더 이상 메시지가 없음|  
|statusDescription|문자열|No|상태에 대한 설명입니다.|  
  
응답 메시지 본문은 다음 엔터티와 함께 **맵** 을 포함하는 **amqp-value** 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|messages|맵 목록|예|모든 맵이 메시지를 나타내는 메시지 목록입니다.|  
  
메시지를 나타내는 맵은 다음 항목을 포함해야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|message|바이트 배열|예|AMQP 1.0 실시간 인코딩된 메시지입니다.|  
  
### <a name="schedule-message"></a>메시지 예약  

메시지를 예약합니다. 이 작업은 트랜잭션을 지원합니다.
  
#### <a name="request"></a>요청  

요청 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|operation|문자열|예|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|예|작업 서버 제한 시간(밀리초)입니다.|  
  
요청 메시지 본문은 다음 엔터티와 함께 **맵** 을 포함하는 **amqp-value** 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|messages|맵 목록|예|모든 맵이 메시지를 나타내는 메시지 목록입니다.|  
  
메시지를 나타내는 맵은 다음 항목을 포함해야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|message-id|문자열|예|string으로 `amqpMessage.Properties.MessageId`|  
|session-id|문자열|No|`amqpMessage.Properties.GroupId as string`|  
|파티션 키|문자열|No|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|via-partition-key|문자열|No|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|message|바이트 배열|예|AMQP 1.0 실시간 인코딩된 메시지입니다.|  
  
#### <a name="response"></a>응답  

응답 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|예|HTTP 응답 코드 [RFC2616]<br /><br /> 200: OK – 성공, 그렇지 않으면 실패입니다.|  
|statusDescription|문자열|No|상태에 대한 설명입니다.|  
  
응답 메시지 본문은 다음 엔터티와 함께 맵을 포함하는 **amqp-value** 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|시퀀스 번호|long 배열|예|예약된 메시지의 시퀀스 번호입니다. 시퀀스 번호는 취소하는 데 사용됩니다.|  
  
### <a name="cancel-scheduled-message"></a>예약된 메시지 취소  

예약된 메시지를 취소합니다.  
  
#### <a name="request"></a>요청  

요청 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|operation|문자열|예|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|예|작업 서버 제한 시간(밀리초)입니다.|  
  
요청 메시지 본문은 다음 엔터티와 함께 **맵** 을 포함하는 **amqp-value** 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|시퀀스 번호|long 배열|예|취소할 예약된 메시지의 시퀀스 번호입니다.|  
  
#### <a name="response"></a>응답  

응답 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|예|HTTP 응답 코드 [RFC2616]<br /><br /> 200: OK – 성공, 그렇지 않으면 실패입니다.|  
|statusDescription|문자열|No|상태에 대한 설명입니다.|   
  
## <a name="session-operations"></a>세션 작업  
  
### <a name="session-renew-lock"></a>세션 갱신 잠금  

큐 또는 구독에 설정된 잠금 기간을 기준으로 메시지 잠금을 확장합니다.  
  
#### <a name="request"></a>요청  

요청 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|operation|문자열|예|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|예|작업 서버 제한 시간(밀리초)입니다.|  
  
요청 메시지 본문은 다음 엔터티와 함께 **맵** 을 포함하는 **amqp-value** 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|session-id|문자열|예|세션 ID입니다.|  
  
#### <a name="response"></a>응답  

응답 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|예|HTTP 응답 코드 [RFC2616]<br /><br /> 200: OK – 더 많은 메시지가 있음<br /><br /> 204: 콘텐츠 없음 – 더 이상 메시지가 없음|  
|statusDescription|문자열|No|상태에 대한 설명입니다.|  
  
응답 메시지 본문은 다음 엔터티와 함께 맵을 포함하는 **amqp-value** 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|expiration|timestamp|예|새 만료.|  
  
### <a name="peek-session-message"></a>세션 메시지 보기  

잠그지 않고 세션 메시지를 봅니다.  
  
#### <a name="request"></a>요청  

요청 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|operation|문자열|예|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|예|작업 서버 제한 시간(밀리초)입니다.|  
  
요청 메시지 본문은 다음 엔터티와 함께 **맵** 을 포함하는 **amqp-value** 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|from-sequence-number|long|예|보기를 시작할 시퀀스 번호입니다.|  
|message-count|int|예|보려는 최대 메시지 수입니다.|  
|session-id|문자열|예|세션 ID입니다.|  
  
#### <a name="response"></a>응답  

응답 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|예|HTTP 응답 코드 [RFC2616]<br /><br /> 200: OK – 더 많은 메시지가 있음<br /><br /> 204: 콘텐츠 없음 – 더 이상 메시지가 없음|  
|statusDescription|문자열|No|상태에 대한 설명입니다.|  
  
응답 메시지 본문은 다음 엔터티와 함께 맵을 포함하는 **amqp-value** 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|messages|맵 목록|예|모든 맵이 메시지를 나타내는 메시지 목록입니다.|  
  
 메시지를 나타내는 맵은 다음 항목을 포함해야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|message|바이트 배열|예|AMQP 1.0 실시간 인코딩된 메시지입니다.|  
  
### <a name="set-session-state"></a>세션 상태 설정  

세션 상태를 설정합니다.  
  
#### <a name="request"></a>요청  

요청 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|operation|문자열|예|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|예|작업 서버 제한 시간(밀리초)입니다.|  
  
요청 메시지 본문은 다음 엔터티와 함께 **맵** 을 포함하는 **amqp-value** 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|session-id|문자열|예|세션 ID입니다.|  
|session-state|바이트 배열|예|불투명한 이진 본문.|  
  
#### <a name="response"></a>응답  

응답 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|예|HTTP 응답 코드 [RFC2616]<br /><br /> 200: OK – 성공, 그렇지 않으면 실패입니다.|  
|statusDescription|문자열|No|상태에 대한 설명입니다.|  
  
### <a name="get-session-state"></a>세션 상태 가져오기  

세션 상태를 가져옵니다.  
  
#### <a name="request"></a>요청  

요청 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|operation|문자열|예|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|예|작업 서버 제한 시간(밀리초)입니다.|  
  
요청 메시지 본문은 다음 엔터티와 함께 **맵** 을 포함하는 **amqp-value** 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|session-id|문자열|예|세션 ID입니다.|  
  
#### <a name="response"></a>응답  

응답 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|예|HTTP 응답 코드 [RFC2616]<br /><br /> 200: OK – 성공, 그렇지 않으면 실패입니다.|  
|statusDescription|문자열|No|상태에 대한 설명입니다.|  
  
응답 메시지 본문은 다음 엔터티와 함께 **맵** 을 포함하는 **amqp-value** 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|session-state|바이트 배열|예|불투명한 이진 본문.|  
  
### <a name="enumerate-sessions"></a>세션 열거  

메시지 엔터티에서 세션을 열거합니다.  
  
#### <a name="request"></a>요청  

요청 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|operation|문자열|예|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|예|작업 서버 제한 시간(밀리초)입니다.|  
  
요청 메시지 본문은 다음 엔터티와 함께 **맵** 을 포함하는 **amqp-value** 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|last-updated-time|timestamp|예|지정된 시간 이후 업데이트된 세션만 포함하도록 필터링합니다.|  
|skip|int|예|세션 수를 건너뜁니다.|  
|top|int|예|최대 세션 수입니다.|  
  
#### <a name="response"></a>응답  

응답 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|예|HTTP 응답 코드 [RFC2616]<br /><br /> 200: OK – 더 많은 메시지가 있음<br /><br /> 204: 콘텐츠 없음 – 더 이상 메시지가 없음|  
|statusDescription|문자열|No|상태에 대한 설명입니다.|  
  
응답 메시지 본문은 다음 엔터티와 함께 **맵** 을 포함하는 **amqp-value** 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|skip|int|예|상태 코드가 200인 경우 건너뛴 세션 수입니다.|  
|sessions-ids|문자열 배열|예|상태 코드가 200인 경우 세션 ID 배열입니다.|  
  
## <a name="rule-operations"></a>규칙 작업  
  
### <a name="add-rule"></a>규칙 추가  
  
#### <a name="request"></a>요청  

요청 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|operation|문자열|예|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|예|작업 서버 제한 시간(밀리초)입니다.|  
  
요청 메시지 본문은 다음 엔터티와 함께 **맵** 을 포함하는 **amqp-value** 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|rule-name|문자열|예|구독 및 토픽 이름을 포함하지 않는 규칙 이름입니다.|  
|rule-description|map|예|다음 섹션에 지정된 대로 규칙 설명입니다.|  
  
**rule-description** 맵은 다음 엔터티를 포함해야 합니다. 여기서 **sql-filter** 및 **correlation-filter** 는 상호 배타적입니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|예|다음 섹션에 지정된 대로 `sql-filter`입니다.|  
|correlation-filter|map|예|다음 섹션에 지정된 대로 `correlation-filter`입니다.|  
|sql-rule-action|map|예|다음 섹션에 지정된 대로 `sql-rule-action`입니다.|  
  
sql-filter 맵은 다음 항목을 포함해야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|식|문자열|예|Sql 필터 식.|  
  
**correlation-filter** 맵은 다음 항목을 하나 이상 포함해야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|correlation-id|문자열|No||  
|message-id|문자열|예||  
|to|문자열|No||  
|reply-to|문자열|No||  
|label|문자열|No||  
|session-id|문자열|No||  
|reply-to-session-id|문자열|No||  
|content-type|문자열|No||  
|properties|map|예|Service Bus [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)로 매핑합니다.|  
  
**sql-rule-action** 맵은 다음 항목을 포함해야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|식|문자열|예|Sql 작업 식.|  
  
#### <a name="response"></a>응답  

응답 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|예|HTTP 응답 코드 [RFC2616]<br /><br /> 200: OK – 성공, 그렇지 않으면 실패입니다.|  
|statusDescription|문자열|No|상태에 대한 설명입니다.|  
  
### <a name="remove-rule"></a>규칙 제거  
  
#### <a name="request"></a>요청  

요청 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|operation|문자열|예|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|예|작업 서버 제한 시간(밀리초)입니다.|  
  
요청 메시지 본문은 다음 엔터티와 함께 **맵** 을 포함하는 **amqp-value** 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|rule-name|문자열|예|구독 및 토픽 이름을 포함하지 않는 규칙 이름입니다.|  
  
#### <a name="response"></a>응답  

응답 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|예|HTTP 응답 코드 [RFC2616]<br /><br /> 200: OK – 성공, 그렇지 않으면 실패입니다.|  
|statusDescription|문자열|No|상태에 대한 설명입니다.|  
  
### <a name="get-rules"></a>규칙 가져오기

#### <a name="request"></a>요청

요청 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.

|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|operation|문자열|예|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|예|작업 서버 제한 시간(밀리초)입니다.|  

요청 메시지 본문은 다음 엔터티와 함께 **맵** 을 포함하는 **amqp-value** 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|top|int|예|페이지에 페치할 규칙의 수입니다.|  
|skip|int|예|건너뛸 규칙의 수입니다. 규칙의 목록에서 시작하는 인덱스(+1)을 정의합니다. | 

#### <a name="response"></a>응답

응답 메시지에는 다음과 같은 속성이 포함됩니다.

|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|예|HTTP 응답 코드 [RFC2616]<br /><br /> 200: OK – 성공, 그렇지 않으면 실패입니다.|  
|규칙| 맵의 배열|예|규칙의 배열입니다. 각 규칙은 맵으로 표시됩니다.|

배열의 각 맵 항목에는 다음과 같은 속성이 포함됩니다.

|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|rule-description|설명된 개체의 배열|예|AMQP 설명 코드 0x0000013700000004가 포함된 `com.microsoft:rule-description:list`| 

`com.microsoft.rule-description:list`는 설명된 개체의 배열입니다. 배열에는 다음이 포함됩니다.

|인덱스|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
| 0 | 설명된 개체의 배열 | 예 | `filter`: 아래에 지정됨. |
| 1 | 설명된 개체의 배열 | 예 | `ruleAction`: 아래에 지정됨. |
| 2 | 문자열 | 예 | 규칙의 이름입니다. |

`filter`는 다음 유형 중 하나일 수 있습니다.

| 설명자 이름 | 설명자 코드 | 값 |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | SQL 필터 |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | 상관 관계 필터 |
| `com.microsoft:true-filter:list` | 0x000001370000007 | 1 = 1을 나타내는 true 필터 |
| `com.microsoft:false-filter:list` | 0x000001370000008 | 1 = 0을 나타내는 false 필터 |

`com.microsoft:sql-filter:list`는 다음을 포함하는 설명된 배열입니다.

|인덱스|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
| 0 | 문자열 | 예 | SQL 필터 식 |

`com.microsoft:correlation-filter:list`는 다음을 포함하는 설명된 배열입니다.

|인덱스(존재하는 경우)|값 형식|값 내용|  
|---------|----------------|--------------|
| 0 | 문자열 | 상관관계 ID |
| 1 | 문자열 | 메시지 ID |
| 2 | 문자열 | 작업 |
| 3 | 문자열 | 회신 |
| 4 | 문자열 | 레이블 |
| 5 | 문자열 | 세션 ID |
| 6 | 문자열 | 세션 ID에 회신|
| 7 | 문자열 | 콘텐츠 유형 |
| 8 | 맵 | 애플리케이션 정의 속성의 맵 |

`ruleAction`은 다음 유형 중 하나일 수 있습니다.

| 설명자 이름 | 설명자 코드 | 값 |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | 빈 규칙 동작 - 규칙 작업이 존재하지 않음 |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | SQL 규칙 동작 |

`com.microsoft:sql-rule-action:list`는 첫 번째 항목이 SQL 규칙 동작의 식을 포함하는 문자열인 설명된 개체의 배열입니다.

## <a name="deferred-message-operations"></a>지연된 메시지 작업  
  
### <a name="receive-by-sequence-number"></a>시퀀스 번호로 받기  

시퀀스 번호로 지연된 메시지를 받습니다.  
  
#### <a name="request"></a>요청  

요청 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|operation|문자열|예|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|예|작업 서버 제한 시간(밀리초)입니다.|  
  
요청 메시지 본문은 다음 엔터티와 함께 **맵** 을 포함하는 **amqp-value** 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|시퀀스 번호|long 배열|예|시퀀스 번호.|  
|receiver-settle-mode|ubyte|예|AMQP 코어 v1.0에 지정된 대로 **수신기 장착** 모드입니다.|  
  
#### <a name="response"></a>응답  

응답 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|예|HTTP 응답 코드 [RFC2616]<br /><br /> 200: OK – 성공, 그렇지 않으면 실패입니다.|  
|statusDescription|문자열|No|상태에 대한 설명입니다.|  
  
응답 메시지 본문은 다음 엔터티와 함께 **맵** 을 포함하는 **amqp-value** 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|messages|맵 목록|예|모든 맵이 메시지를 나타내는 메시지 목록입니다.|  
  
메시지를 나타내는 맵은 다음 항목을 포함해야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|lock-token|uuid|예|`receiver-settle-mode`가 1인 경우 토큰을 잠급니다.|  
|message|바이트 배열|예|AMQP 1.0 실시간 인코딩된 메시지입니다.|  
  
### <a name="update-disposition-status"></a>처리 상태 업데이트  

지연된 메시지의 처리 상태를 업데이트합니다. 이 작업은 트랜잭션을 지원합니다.
  
#### <a name="request"></a>요청  

요청 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|operation|문자열|예|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|예|작업 서버 제한 시간(밀리초)입니다.|  
  
요청 메시지 본문은 다음 엔터티와 함께 **맵** 을 포함하는 **amqp-value** 섹션으로 구성되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|disposition-status|문자열|예|완료됨<br /><br /> 중단됨<br /><br /> 일시 중단됨|  
|lock-tokens|uuid의 배열|예|처리 상태를 업데이트할 메시지 잠금 토큰입니다.|  
|deadletter-reason|문자열|No|처리 상태가 **일시 중단됨** 으로 설정된 경우 설정할 수 있습니다.|  
|deadletter-description|문자열|No|처리 상태가 **일시 중단됨** 으로 설정된 경우 설정할 수 있습니다.|  
|properties-to-modify|map|예|수정할 Service Bus broker 저장 메시지 목록입니다.|  
  
#### <a name="response"></a>응답  

응답 메시지에는 다음과 같은 애플리케이션 속성이 포함되어야 합니다.  
  
|키|값 형식|필수|값 내용|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|예|HTTP 응답 코드 [RFC2616]<br /><br /> 200: OK – 성공, 그렇지 않으면 실패입니다.|  
|statusDescription|문자열|No|상태에 대한 설명입니다.|

## <a name="next-steps"></a>다음 단계

AMQP 및 Service Bus에 대해 자세히 알아보려면 다음 링크를 방문하세요.

* [Service Bus AMQP 개요]
* [AMQP 1.0 프로토콜 가이드]
* [Windows Server용 Service Bus의 AMQP]

[Service Bus AMQP 개요]: service-bus-amqp-overview.md
[AMQP 1.0 프로토콜 가이드]: service-bus-amqp-protocol-guide.md
[Windows Server용 Service Bus의 AMQP]: /previous-versions/service-bus-archive/dn282144(v=azure.100)
