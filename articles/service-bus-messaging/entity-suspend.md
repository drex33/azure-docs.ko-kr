---
title: Azure Service Bus - 메시징 엔터티 일시 중단
description: 이 문서에서는 Azure Service Bus 메시지 엔터티(큐, 토픽 및 구독)를 일시 중단하고 다시 활성화하는 방법을 설명합니다.
ms.topic: article
ms.date: 09/28/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f22e3bcfbd423541508faffdb7983d30ba3bbf3a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472070"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>메시징 엔터티 일시 중단 및 다시 활성화(사용 안 함)

큐, 토픽 및 구독을 일시적으로 중단할 수 있습니다. 일시 중단을 수행하면 엔터티가 사용 안 함 상태가 되며 모든 메시지는 스토리지에 유지됩니다. 그러나 메시지를 제거 또는 추가할 수 없으며 해당 프로토콜 작업은 오류를 생성합니다.

긴급한 관리상의 이유로 엔터티를 일시 중단하는 것이 좋습니다. 예를 들어 결함이 있는 수신기는 큐에서 메시지를 제거하고, 처리에 실패하고, 메시지를 잘못 완료하고 제거합니다. 이 경우 코드를 수정하고 배포할 때까지 수신 큐를 비활성화할 수 있습니다. 

사용자 또는 시스템에 의해 일시 중단 또는 다시 활성화가 수행될 수 있습니다. 시스템은 구독 지출 제한에 도달하는 등 중대한 관리상의 이유가 있을 때만 엔터티를 일시 중단합니다. 시스템에서 사용하지 않도록 설정된 엔터티는 사용자가 다시 활성화할 수 없지만 일시 중단의 원인에 해결되면 복원됩니다.

## <a name="queue-status"></a>큐 상태 
**큐** 에 대해 설정할 수 있는 상태는 다음과 같습니다.

-   **Active**: 큐가 활성 상태입니다. 큐에서 메시지를 보내고 받을 수 있습니다. 
-   **Disabled**: 큐가 일시 중단되었습니다. **SendDisabled** 및 **ReceiveDisabled** 를 모두 설정하는 것과 같습니다. 
-   **SendDisabled**: 큐에 메시지를 보낼 수 없지만 큐에서 메시지를 받을 수는 있습니다. 큐에 메시지를 전송하려고 하면 예외가 발생합니다. 
-   **ReceiveDisabled**: 큐에 메시지를 보낼 수 있지만 큐에서 메시지를 받을 수는 없습니다. 큐에 메시지를 수신하려고 하면 예외가 발생합니다.


### <a name="change-the-queue-status-in-the-azure-portal"></a>Azure Portal에서 큐 상태를 변경합니다. 

1. Azure Portal에서 Service Bus 네임스페이스로 이동합니다. 
1. 상태를 변경할 큐를 산택합니다. 가운데 아래쪽 창에 큐가 표시됩니다. 
1. **Service Bus 큐** 페이지에서 큐의 현재 상태를 하이퍼링크로 표시합니다. 왼쪽 메뉴에서 **개요** 를 선택하지 않은 경우 해당 개요를 선택하여 큐의 상태를 확인합니다. 큐의 현재 상태를 선택하여 변경합니다. 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="큐의 상태 선택":::
4. 큐의 새 상태를 선택하고 **확인** 을 선택합니다. 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="큐의 상태 설정":::
    
.NET SDK의 Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API를 사용하거나 Azure CLI 또는 Azure PowerShell을 통해 Azure Resource Manager 템플릿을 사용하여 송신 및 수신 작업을 사용하지 않도록 설정할 수도 있습니다.

### <a name="change-the-queue-status-using-azure-powershell"></a>Azure PowerShell을 사용하여 큐 상태 변경
큐를 사용하지 않도록 설정하는 PowerShell 명령은 다음 예제에 나와 있습니다. 재활성화 명령은 동일하게 `Status`를 **Active** 로 설정합니다.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>토픽 상태
Azure Portal에서 토픽 상태를 변경할 수 있습니다. 토픽의 현재 상태를 선택하여 상태를 변경할 수 있는 다음 페이지를 표시합니다. 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="토픽 상태 변경":::

**토픽** 에 대해 설정할 수 있는 상태는 다음과 같습니다.
- **활성**: 토픽이 활성 상태입니다. 토픽에 메시지를 보낼 수 있습니다. 
- **사용 안 함**: 토픽이 일시 중단됩니다. 토픽에 메시지를 보낼 수 없습니다. 
- **SendDisabled**: **Disabled** 와 같은 효과입니다. 토픽에 메시지를 보낼 수 없습니다. 토픽에 메시지를 전송하려고 하면 예외가 발생합니다. 

## <a name="subscription-status"></a>구독 상태
Azure Portal에서 구독 상태를 변경할 수 있습니다. 구독의 현재 상태를 선택하여 상태를 변경할 수 있는 다음 페이지를 표시합니다. 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="구독 상태 변경":::

**구독** 에 대해 설정할 수 있는 상태는 다음과 같습니다.
- **활성**: 구독이 활성 상태입니다. 구독에서 메시지를 받을 수 있습니다.
- **사용 안 함**: 구독이 일시 중단됩니다. 구독에서 메시지를 받을 수 없습니다. 
- **ReceiveDisabled**: **Disabled** 와 같은 효과입니다. 구독에서 메시지를 받을 수 없습니다. 구독에 메시지를 수신하려고 하면 예외가 발생합니다.

| 토픽 상태 | 구독 상태 | 동작 | 
| ------------ | ------------------- | -------- | 
| Active | Active | 토픽에 메시지를 보내고 구독에서 메시지를 받을 수 있습니다. | 
| Active | 사용 안 함 또는 수신 사용 안 함 | 토픽에 메시지를 보낼 수 있지만 구독에서 메시지를 받을 수는 없습니다. | 
| 사용 안 함 또는 보내기 사용 안 함 | Active | 토픽에 메시지를 보낼 수는 없지만 이미 구독에 있는 메시지는 받을 수 있습니다. | 
| 사용 안 함 또는 보내기 사용 안 함 | 사용 안 함 또는 수신 사용 안 함 | 토픽에 메시지를 보낼 수 없으며 구독에서도 받을 수 없습니다. | 

## <a name="other-statuses"></a>기타 상태
[EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) 열거형은 시스템에 의해서만 설정될 수 있는 전환 상태 집합도 정의합니다. 


## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

