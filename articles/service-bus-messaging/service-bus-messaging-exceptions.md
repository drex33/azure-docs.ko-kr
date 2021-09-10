---
title: Azure Service Bus - 메시징 예외 | Microsoft Docs
description: 이 문서에서는 Azure Service Bus 메시징 예외 및 예외 발생 시 수행해야 할 권장 작업 목록을 제공합니다.
ms.topic: article
ms.date: 08/04/2021
ms.openlocfilehash: 1535ae6e125ee11d30fab1aeaa12afe57a66b0e5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528697"
---
# <a name="service-bus-messaging-exceptions"></a>Service Bus 메시징 예외

이 문서에서는 .NET Framework API에 의해 생성되는 .NET 예외를 나열합니다.

## <a name="exception-categories"></a>예외 범주

메시징 API는 다음 범주에 해당하는 예외와, 해당 예외의 해결을 위해 수행할 수 있는 관련 조치를 함께 생성합니다. 예외의 의미 및 원인은 메시징 엔터티의 형식에 따라 달라질 수 있습니다.

1. 사용자 코딩 오류([System.ArgumentException](/dotnet/api/system.argumentexception), [System.InvalidOperationException](/dotnet/api/system.invalidoperationexception), [System.OperationCanceledException](/dotnet/api/system.operationcanceledexception), [System.Runtime.Serialization.SerializationException](/dotnet/api/system.runtime.serialization.serializationexception)). 일반 조치: 코드를 수정한 후 계속합니다.
2. 설치/구성 오류 ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception). 일반 조치: 구성을 검토하고 필요한 경우 변경합니다.
3. 일시적 예외 ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). 일반 조치: 작업을 다시 시도하거나 사용자에게 알립니다. 클라이언트 SDK의 `RetryPolicy` 클래스가 자동으로 다시 시도를 처리하도록 구성할 수 있습니다. 자세한 내용은 [다시 시도 참고 자료](/azure/architecture/best-practices/retry-service-specific#service-bus)를 참조하세요.
4. 기타 예외 ([System.Transactions.TransactionException](/dotnet/api/system.transactions.transactionexception), [System.TimeoutException](/dotnet/api/system.timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). 일반 작업: 예외 형식과 관련이 있습니다. 다음 섹션의 표를 참조하세요.

## <a name="exception-types"></a>예외 유형

다음 표에서는 메시징 예외 유형과 원인, 사용자가 수행할 수 있는 제안 조치 참고를 열거합니다.

| **예외 유형** | **설명/원인/예** | **권장 조치** | **자동/즉시 다시 시도 참고** |
| --- | --- | --- | --- |
| [TimeoutException](/dotnet/api/system.timeoutexception) |서버가 [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings)에서 제어하는 지정된 시간 안에 요청된 작업에 응답하지 않았습니다. 서버가 요청된 작업을 완료했을 수도 있습니다. 네트워크 또는 기타 인프라 지연으로 인해 발생할 수 있습니다. |필요한 경우 시스템 상태에서 일관성을 확인하고 다시 시도합니다. [시간 제한 예외](#timeoutexception)를 참조하세요. |일부 경우 다시 시도하면 문제가 해결될 수 있습니다. 코드에 재시도 논리를 추가합니다. |
| [InvalidOperationException](/dotnet/api/system.invalidoperationexception) |요청한 사용자 작업이 서버 또는 서비스 내에서 허용되지 않습니다. 자세한 내용은 예외 메시지를 참조하세요. 예를 들어 [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) 모드에서 메시지를 받은 경우 [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)가 이 예외를 생성합니다. |코드 및 설명서를 확인합니다. 요청된 작업이 유효한지 확인합니다. |다시 시도해도 소용이 없습니다. |
| [OperationCanceledException](/dotnet/api/system.operationcanceledexception) |이미 종료, 중단 또는 삭제된 개체에서 작업을 호출하려 시도합니다. 드문 경우지만 앰비언트 트랜잭션이 이미 삭제되었을 수 있습니다. |코드를 확인하고 삭제된 개체에 대해 작업을 호출하지 않는지 확인합니다. |다시 시도해도 소용이 없습니다. |
| [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) 개체가 토큰을 확보할 수 없거나, 토큰이 유효하지 않거나, 토큰에 작업 수행을 위해 필요한 클레임이 없습니다. |올바른 값을 사용하여 토큰 공급자를 만드는지 확인합니다. ACS(Access Control Service) 구성을 확인합니다. |일부 경우 다시 시도하면 문제가 해결될 수 있습니다. 코드에 재시도 논리를 추가합니다. |
| [ArgumentException](/dotnet/api/system.argumentexception)<br /> [ArgumentNullException](/dotnet/api/system.argumentnullexception)<br />[ArgumentOutOfRangeException](/dotnet/api/system.argumentoutofrangeexception) |메서드에 제공된 하나 이상의 인수가 잘못되었습니다.<br /> [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 또는 [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory)에 제공된 URI에 경로 세그먼트가 포함됩니다.<br /> [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 또는 [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory)에 제공된 URI 스키마가 올바르지 않습니다. <br />속성 값이 32KB보다 큽니다. |호출 코드를 확인하고 인수가 정확한지 확인합니다. |다시 시도해도 소용이 없습니다. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |작업과 연결된 엔터티가 없거나 삭제되었습니다. |엔터티가 있는지 확인합니다. |다시 시도해도 소용이 없습니다. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |특정 시퀀스 번호를 통해 메시지 수신을 시도합니다. 이 메시지를 찾을 수 없습니다. |메시지를 아직 받지 못했는지 확인합니다. 전달 실패 큐에서 메시지가 전달되지 않았는지 확인합니다. |다시 시도해도 소용이 없습니다. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |클라이언트가 Service Bus에 연결할 수 없습니다. |제공된 호스트 이름이 정확하며 호스트에 연결할 수 있는지 확인합니다. <p>코드가 방화벽/프록시를 사용하는 환경에서 실행되는 경우 Service Bus 도메인/IP 주소 및 포트에 대한 트래픽이 차단되지 않았는지 확인합니다.</p>|간헐적인 연결 문제라면 재시도로 문제를 해결할 수 있습니다. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |서비스가 지금은 요청을 처리할 수 없습니다. |클라이언트가 잠시 대기한 후 작업을 다시 시도할 수 있습니다. |클라이언트가 일정 시간 이후에 다시 시도할 수 있습니다. 재시도에서 다른 예외가 발생한 경우 해당 예외의 재시도 작동을 확인합니다. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |다음 상황에 발생할 수 있는 일반 메시징 예외:<p>다른 엔터티 형식에 속하는(예: topic) 이름이나 경로를 사용하여 [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient)를 만들려고 시도합니다.</p><p>256KB보다 큰 메시지를 전송하려고 시도합니다. </p>서버 또는 서비스가 요청을 처리하는 동안 오류가 발생했습니다. 자세한 내용은 예외 메시지를 참조하세요. 보통은 일시적인 예외입니다.</p><p>엔터티가 제한되므로 요청이 종료되었습니다. 오류 코드: 50001, 50002, 50008. </p> | 코드를 확인하고 메시지 본문에서 직렬화 가능 개체(또는 사용자 지정 직렬 변환기 사용)만 사용하는지 확인합니다. <p>설명서에서 지원되는 속성 값 유형을 확인하고 지원되는 유형만 사용합니다.</p><p> [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) 속성을 확인합니다. **true** 이면 작업을 다시 시도할 수 있습니다. </p>| 제한으로 인해 예외가 발생한 경우 몇 초 정도 기다린 후 작업을 다시 시도하세요. 재시도 동작이 정의되어 있지 않으며 다른 시나리오에서 도움이 되지 않을 수 있습니다.|
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |해당 서비스 네임스페이스에서 이미 다른 엔터티가 사용하는 이름으로 엔터티를 만들려고 합니다. |기존 엔터티를 삭제하거나 만들려는 엔터티에 다른 이름을 선택합니다. |다시 시도해도 소용이 없습니다. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |메시징 엔터티가 최대 허용 크기에 도달했거나 네임스페이스에 대한 최대 연결 수를 초과했습니다. |엔터티나 하위 큐에서 메시지를 수신하여 엔터티에 공간을 만듭니다. [QuotaExceededException](#quotaexceededexception)을 참조하세요. |그 사이 메시지가 제거되었으면 재시도가 도움이 될 수 있습니다. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |잘못된 규칙 작업을 만들려고 시도하면 Service Bus가 이 예외를 반환합니다. 메시지에 대한 규칙 작업 처리 중에 오류가 발생하면 Service Bus가 이 예외를 전달 실패 메시지에 연결합니다. |규칙 작업이 정확한지 확인합니다. |다시 시도해도 소용이 없습니다. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |잘못된 필터를 만들려고 시도하면 Service Bus가 이 예외를 반환합니다. 메시지에 대한 필터 처리 중에 오류가 발생하면 Service Bus가 이 예외를 전달 실패 메시지에 연결합니다. |필터가 정확한지 확인합니다. |다시 시도해도 소용이 없습니다. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |특정 세션 ID를 갖는 세션을 수락하려 하나 해당 세션이 현재 다른 클라이언트에 잠겨 있습니다. |해당 세션이 다른 클라이언트에서 잠금 해제되었는지 확인합니다. |그 사이에 세션이 릴리스된 경우 다시 시도하면 문제가 해결될 수 있습니다. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |트랜잭션에 너무 많은 작업이 포함되어 있습니다. |이 트랜잭션에 포함된 작업 수를 줄입니다. |다시 시도해도 소용이 없습니다. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |비활성화된 엔터티의 런타임 작업에 대한 요청입니다. |엔터티를 활성화합니다. |그 사이에 엔터티가 활성화된 경우 다시 시도하면 문제가 해결될 수 있습니다. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |사전 필터링이 활성화되었으며 일치하는 필터가 없는 항목에 메시지를 보내는 경우 Service Bus가 이 예외를 반환합니다. |하나 이상의 필터가 일치하는지 확인합니다. |다시 시도해도 소용이 없습니다. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |메시지 페이로드가 256KB 제한을 초과합니다. 256KB 제한은 총 메시지 크기입니다. 여기에는 시스템 속성과 모든 .NET 오버헤드가 포함됩니다. |메시지 페이로드의 크기를 줄인 다음 작업을 다시 시도합니다. |다시 시도해도 소용이 없습니다. |
| [TransactionException](/dotnet/api/system.transactions.transactionexception) |앰비언트 트랜잭션(`Transaction.Current`)이 올바르지 않습니다. 완료되었거나 중단되었을 수 있습니다. 내부 예외가 추가 정보를 제공할 수 있습니다. | |다시 시도해도 소용이 없습니다. |
| [TransactionInDoubtException](/dotnet/api/system.transactions.transactionindoubtexception) |의심스러운 트랜잭션에서 작업을 시도하거나, 트랜잭션을 커밋하려고 시도하는데 해당 트랜잭션의 상태가 확실하지 않습니다. |트랜잭션이 이미 커밋되었을 수 있으므로 애플리케이션에서 이 예외를 처리해야 합니다(특수한 경우). |- |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) 은 특정 엔터티에 대한 할당량이 초과됐음을 나타냅니다.

### <a name="queues-and-topics"></a>큐 및 토픽

큐 및 토픽의 경우 이는 큐의 크기인 경우가 많습니다. 다음 예제와 같이 오류 메시지 속성은 추가 세부 정보를 포함합니다.

```output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

메시지는 토픽이 해당 크기 제한을 초과했음을 알려 줍니다. 이 경우 1GB입니다(기본 크기 제한).

### <a name="namespaces"></a>네임스페이스

네임스페이스의 경우 [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception)은 애플리케이션이 네임스페이스에 대한 최대 연결 수를 초과했음을 나타낼 수 있습니다. 예를 들면 다음과 같습니다.

```output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

### <a name="common-causes"></a>일반적인 원인

이 오류에 대한 두 가지 일반적인 원인은 배달 못 한 편지 큐와 정상적으로 작동하지 않는 메시지 수신기입니다.

1. **[배달 못 한 편지 큐](service-bus-dead-letter-queues.md)** 판독기는 메시지 완료에 실패하고 잠금이 만료되면 메시지는 큐/토픽으로 반환됩니다. 판독기에서 [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete) 호출을 방지하는 예외가 발생하는 경우 발생할 수 있습니다. 메시지는 10번 읽혀진 후 기본적으로 배달 못 한 편지 큐로 이동합니다. 이 동작은 [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) 속성에 의해 제어되고 기본값 10을 가집니다. 메시지는 배달 못 한 편지 큐에 쌓이고 공간을 차지합니다.

    이 문제를 해결하려면 다른 큐에서와 마찬가지로 배달 못 한 편지 큐에서 메시지를 읽고 완료합니다. 배달 못 한 편지 큐 경로의 형식을 지정하는 데 도움이 되도록 [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) 메서드를 사용할 수 있습니다.
2. **수신기 중지됨**. 수신기가 큐 또는 구독에서 메시지 수신을 중지했습니다. 이를 식별하는 방법은 메시지의 전체 분석을 보여 주는 [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) 속성에서 확인하는 것입니다. [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) 속성이 높거나 증가하는 경우 메시지는 작성되는 속도로 읽혀지지 않습니다.

## <a name="timeoutexception"></a>TimeoutException

[TimeoutException](/dotnet/api/system.timeoutexception) 은 사용자가 시작한 작업이 작업 시간 제한보다 더 오래 걸린다는 것을 나타냅니다.

이 제한에 도달하면 [TimeoutException](/dotnet/api/system.timeoutexception)이 발생할 수 있으므로 [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) 속성 값도 확인해야 합니다.

서비스를 실행하는 리소스의 Service Bus 서비스 업데이트 또는 OS 업데이트와 같은 유지 관리 작업 도중 또는 작업 사이에 시간 제한이 발생할 것으로 예상됩니다. OS 업데이트 도중 엔터티가 이동하고 노드는 업데이트 또는 재부팅되며, 이로 인해 시간 제한이 발생할 수 있습니다. Azure Service Bus 서비스에 대한 SLA(서비스 수준 계약) 세부 정보는 [Service Bus SLA](https://azure.microsoft.com/support/legal/sla/service-bus/)를 참조하세요.

### <a name="queues-and-topics"></a>큐 및 토픽

큐 및 토픽의 경우 제한 시간은 연결 문자열의 일부로 [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) 속성에서 또는 [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder)를 통해 지정됩니다. 오류 메시지 자체는 다를 수 있지만 현재 작업에 대해 지정된 시간 제한 값을 항상 포함합니다.

## <a name="messagelocklostexception"></a>MessageLockLostException

### <a name="cause"></a>원인

**MessageLockLostException** 은 [PeekLock](message-transfers-locks-settlement.md#peeklock) 수신 모드를 사용하여 메시지를 받고 클라이언트에서 확보한 잠금이 서비스 쪽에서 만료되면 throw됩니다.

메시지에 대한 잠금은 다음과 같은 다양한 이유로 인해 만료될 수 있습니다.

  * 클라이언트 애플리케이션에서 갱신하기 전에 잠금 타이머가 만료되었습니다.
  * 클라이언트 애플리케이션이 잠금을 확보하고 영구 저장소에 저장한 다음, 다시 시작되었습니다. 다시 시작된 후 클라이언트 애플리케이션이 Inflight 메시지를 검토하고 이를 완료하려고 시도했습니다.

### <a name="resolution"></a>해상도

**MessageLockLostException** 발생 시 클라이언트 애플리케이션은 더 이상 메시지를 처리할 수 없습니다. 클라이언트 애플리케이션은 필요에 따라 분석을 위해 예외를 로깅하는 것을 고려할 수 있지만 클라이언트는 메시지를 *삭제해야 합니다*.

메시지에 대한 잠금이 만료되었으므로 메시지가 큐(또는 구독)로 돌아가고 receive를 호출하는 다음 클라이언트 애플리케이션에 의해 처리될 수 있습니다.

**MaxDeliveryCount** 를 초과하면 메시지가 **DeadLetterQueue** 로 이동될 수 있습니다.

## <a name="sessionlocklostexception"></a>SessionLockLostException

### <a name="cause"></a>원인

**SessionLockLostException** 은 세션이 수락되고 클라이언트에서 확보한 잠금이 서비스 쪽에서 만료되면 throw됩니다.

세션에 대한 잠금은 다음과 같은 다양한 이유로 인해 만료될 수 있습니다.

  * 클라이언트 애플리케이션에서 갱신하기 전에 잠금 타이머가 만료되었습니다.
  * 클라이언트 애플리케이션이 잠금을 확보하고 영구 저장소에 저장한 다음, 다시 시작되었습니다. 다시 시작된 후 클라이언트 애플리케이션이 Inflight 세션을 검토하고 해당 세션에서 메시지를 처리하려고 했습니다.

### <a name="resolution"></a>해상도

**SessionLockLostException** 발생 시 클라이언트 애플리케이션은 더 이상 화면의 메시지를 처리할 수 없습니다. 클라이언트 애플리케이션은 분석을 위해 예외를 로깅하는 것을 고려할 수 있지만 클라이언트는 메시지를 *삭제해야 합니다*.

세션에 대한 잠금이 만료되었으므로 세션이 큐(또는 구독)로 돌아가고 세션을 호출하는 다음 클라이언트 애플리케이션에 의해 잠길 수 있습니다. 세션 잠금은 지정된 시간에 단일 클라이언트 애플리케이션이 확보하므로 순차적 처리가 보장됩니다.

## <a name="socketexception"></a>SocketException

### <a name="cause"></a>원인

**SocketException** 은 다음과 같은 경우에 throw됩니다.

   * 지정된 시간(TCP 오류 코드 10060) 후에 호스트가 제대로 응답하지 않아서 연결 시도가 실패한 경우
   * 연결된 호스트가 응답하지 못해 연결을 설정하지 못했습니다.
   * 메시지를 처리하는 동안 오류가 발생했거나 원격 호스트가 시간 제한을 초과했습니다.
   * 기본 네트워크 리소스 문제

### <a name="resolution"></a>해상도

**SocketException** 오류는 애플리케이션을 호스트하는 VM이 `<mynamespace>.servicebus.windows.net`이라는 이름을 해당 IP 주소로 변환할 수 없음을 의미합니다.

아래 명령이 IP 주소에 대한 매핑에 성공했는지 확인합니다.

```powershell
PS C:\> nslookup <mynamespace>.servicebus.windows.net
```

아래와 같은 출력이 표시되어야 합니다.

```bash
Name:    <cloudappinstance>.cloudapp.net
Address:  XX.XX.XXX.240
Aliases:  <mynamespace>.servicebus.windows.net
```

위의 이름이 IP 및 네임스페이스 별칭으로 **확인되지 않는** 경우, 네트워크 관리자가 추가로 조사해야 할 사항을 확인합니다. 이름 확인은 일반적으로 고객 네트워크의 리소스인 DNS 서버를 통해 수행됩니다. Azure DNS에서 DNS 확인을 수행하는 경우 Azure 지원에 문의하세요.

이름 확인이 **예상대로 작동** 하는 경우 [여기](service-bus-troubleshooting-guide.md#connectivity-certificate-or-timeout-issues)에서 Azure Service Bus에 대한 연결이 허용되는지 확인합니다.

## <a name="messagingexception"></a>MessagingException

### <a name="cause"></a>원인

**MessagingException** 은 다양한 이유로 throw될 수 있는 일반 예외입니다. 몇 가지 이유는 다음과 같습니다.

   * **토픽** 또는 **구독** 에 대한 **QueueClient** 를 만들려고 했습니다.
   * 전송된 메시지의 크기가 지정된 계층에 대한 제한보다 큽니다. Service Bus [할당량 및 한도](service-bus-quotas.md)에 대해 자세히 알아보세요.
   * 제한으로 인해 특정 데이터 평면 요청(보내기, 받기, 완료, 중단)이 종료되었습니다.
   * 서비스 업그레이드 및 다시 시작으로 인해 일시적인 문제가 발생했습니다.

> [!NOTE]
> 위의 예외 메시지 목록은 전체 목록이 아닙니다.

### <a name="resolution"></a>해상도

해결 단계는 **MessagingException** 이 throw된 원인에 따라 달라집니다.

   * **일시적인 문제**(이 경우 **_isTransient_*가* _true_ 로 설정됨 *) 또는* 제한 문제** 의 경우 작업을 다시 시도하면 문제가 해결될 수 있습니다. 이 경우 SDK에 대한 기본 재시도 정책을 활용할 수 있습니다.
   * 다른 문제의 경우 예외의 세부 정보를 보면 문제 및 해결 단계를 추론할 수 있습니다.

## <a name="storagequotaexceededexception"></a>StorageQuotaExceededException

### <a name="cause"></a>원인

**StorageQuotaExceededException** 은 프리미엄 네임스페이스에 있는 항목의 총 크기가 [메시징 단위](service-bus-premium-messaging.md)당 한도인 1TB를 초과할 때 생성됩니다.

### <a name="resolution"></a>해결 방법

- 프리미엄 네임스페이스에 할당된 메시징 단위의 수 늘리기
- 네임스페이스에 대해 허용되는 최대 메시징 단위를 이미 사용하고 있는 경우 별도의 네임스페이스를 만듭니다. 

## <a name="next-steps"></a>다음 단계

전체 Service Bus .NET API 참조는 [Azure .NET API 참조](/dotnet/api/overview/azure/service-bus)를 확인하세요.
문제 해결 팁은 [문제 해결 가이드](service-bus-troubleshooting-guide.md)를 참조하세요.
