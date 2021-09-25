---
title: Azure Service Bus - 메시지 수
description: Azure Resource Manager와 Azure Service Bus NamespaceManager API를 사용하여 큐 및 구독에 보관된 메시지 수를 검색합니다.
ms.topic: article
ms.date: 09/20/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 74038f38f1faca90e4fab7a0d4f94229a1b44429
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128576064"
---
# <a name="get-message-counters"></a>메시지 카운터 가져오기
이 문서에서는 큐 또는 구독에 대해 다음과 같은 메시지 수를 가져오는 다양한 방법을 보여 줍니다. 활성 메시지 수를 알면 큐가 현재 배포된 것보다 더 많은 리소스를 처리해야 하는 백로그를 빌드하는지 여부를 파악하는 데 유용합니다. 

| 카운터 | Description |
| ----- | ---------- | 
| ActiveMessageCount | 활성 상태에 있고 전송 준비가 된 큐 또는 구독의 메시지 수입니다. |
| ScheduledMessageCount | 예약된 상태의 메시지 수입니다. |
| DeadLetterMessageCount | 배달 못 한 편지 큐의 메시지 수입니다. |
| TransferMessageCount | 다른 큐 또는 토픽으로 전송 보류 중인 메시지 수입니다. |
| TransferDeadLetterMessageCount | 다른 큐 또는 토픽으로 전송하지 못하고 배달 못 한 편지 큐로 이동된 메시지 수입니다. |

애플리케이션이 큐의 길이에 따라 리소스 크기를 조정하려는 경우 정교한 속도로 조정해야 합니다. 메시지 카운터를 확보하는 작업은 메시지 broker 내부에 비용이 많이 소요되며 빈번하게 직접 실행하면 엔터티 성능에 부정적인 영향을 줍니다.

> [!NOTE]
> Service Bus 토픽으로 송신되는 메시지는 해당 토픽의 구독으로 전달됩니다. 따라서 성공적으로 메시지가 구독으로 전달되면 토픽 자체의 활성 메시지 수는 0입니다. 구독에서 메시지 수를 가져오고 0보다 큰지 확인합니다. 메시지를 확인하는 위치는 구독이지만 메시지가 실제로 저장되는 위치는 토픽 소유의 스토리지입니다. 구독을 보면 메시지 수가 0이 아닙니다(전체 엔터티 공간인 323MB까지 합산됨).


## <a name="using-azure-portal"></a>Azure Portal 사용
네임스페이스로 이동하여 큐를 선택합니다. 큐의 **개요** 페이지에 메시지 카운터가 표시됩니다.

:::image type="content" source="./media/message-counters/queue-overview.png" alt-text="큐 개요 페이지의 메시지 카운터":::

네임스페이스로 이동하여 항목을 선택한 다음, 항목에 대한 구독을 선택합니다. 큐의 **개요** 페이지에 메시지 카운터가 표시됩니다.

:::image type="content" source="./media/message-counters/subscription-overview.png" alt-text="구독 개요 페이지의 메시지 카운터":::

## <a name="using-azure-cli"></a>Azure CLI 사용
[`az servicebus queue show`](/cli/azure/servicebus/queue#az_servicebus_queue_show) 명령을 사용하여 다음 예제와 같이 큐에 대한 메시지 수 세부 정보를 가져옵니다. 

```azurecli-interactive
az servicebus queue show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --query countDetails
```

샘플 출력은 다음과 같습니다.

```bash
ActiveMessageCount    DeadLetterMessageCount    ScheduledMessageCount    TransferMessageCount    TransferDeadLetterMessageCount
--------------------  ------------------------  -----------------------  ----------------------  --------------------------------
0                     0                         0                        0                       0
```

[`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_show) 명령을 사용하여 다음 예제와 같이 구독에 대한 메시지 수 세부 정보를 가져옵니다. 

```azurecli-interactive
az servicebus topic subscription show --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysub \
    --query countDetails
```

## <a name="using-azure-powershell"></a>Azure PowerShell 사용
PowerShell을 사용하여 다음과 같이 큐에 대한 메시지 수 세부 정보를 가져올 수 있습니다.

```azurepowershell-interactive
$queueObj=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
                    -NamespaceName mynamespace `
                    -QueueName myqueue 

$queueObj.CountDetails
```

샘플 출력은 다음과 같습니다.

```bash
ActiveMessageCount             : 7
DeadLetterMessageCount         : 1
ScheduledMessageCount          : 3
TransferMessageCount           : 0
TransferDeadLetterMessageCount : 0
```

다음과 같이 구독에 대한 메시지 수 세부 정보를 가져올 수 있습니다.

```azurepowershell-interactive
$topicObj= Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
                -NamespaceName mynamespace `
                -TopicName mytopic `
                -SubscriptionName mysub

$topicObj.CountDetails
```

반환된 `MessageCountDetails` 개체에는 `ActiveMessageCount`, `DeadLetterMessageCount`, `ScheduledMessageCount`, `TransferDeadLetterMessageCount`, `TransferMessageCount` 속성이 있습니다. 

## <a name="next-steps"></a>다음 단계

선택한 언어로 샘플을 사용하여 Azure Service Bus 기능을 살펴봅니다. 

- [.NET용 Azure Service Bus 클라이언트 라이브러리 샘플(최신)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) 
- [Java용 Azure Service Bus 클라이언트 라이브러리 샘플(최신)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Python용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [JavaScript용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [TypeScript용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

이전 .NET 및 Java 클라이언트 라이브러리에 대한 샘플은 다음을 참조하세요.
- [.NET용 Azure Service Bus 클라이언트 라이브러리 샘플(레거시)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Java용 Azure Service Bus 클라이언트 라이브러리 샘플(레거시)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus)
