---
title: Azure Service Bus 탐색기를 사용하여 Service Bus(미리 보기)에서 데이터 작업 수행
description: 이 문서에서는 포털 기반 Azure Service Bus 탐색기를 사용하여 Azure Service Bus 데이터에 액세스하는 방법에 대한 정보를 제공합니다.
ms.topic: conceptual
ms.date: 06/16/2021
ms.openlocfilehash: 3b561586e77940eda5647aa0ab333ab88937463f
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112294358"
---
# <a name="use-service-bus-explorer-to-perform-data-operations-on-service-bus-preview"></a>Service Bus 탐색기를 사용하여 Service Bus(미리 보기)에서 데이터 작업 수행

## <a name="overview"></a>개요

Azure Service Bus를 사용하면 보낸 사람 및 받는 사람 클라이언트 애플리케이션에서 친숙한 지점 간(큐) 및 게시-구독(토픽 구독) 의미 체계를 사용하여 비즈니스 논리를 분리할 수 있습니다.

Azure Service Bus 네임스페이스에 대해 수행되는 작업에는 두 가지 종류가 있습니다. 
   * 관리 작업 - Service Bus 네임스페이스, 큐, 토픽 및 구독을 만들고, 업데이트하고, 삭제합니다.
   * 데이터 작업 - 큐, 토픽 및 구독에서 메시지를 보내고 받습니다.

Azure Service Bus 탐색기는 관리 작업 이상으로 포털 기능을 확장하여 Azure Portal 자체에서 바로 큐, 토픽 및 구독(및 배달 못한 편지 하위 엔터티)에 대한 데이터 작업(보내기, 받기, 피킹)을 지원합니다.

> [!NOTE]
> 이 문서에서는 Azure Portal에 있는 Azure Service Bus 탐색기의 기능을 중점적으로 소개합니다.
>
> Azure Service Bus 탐색기 도구는 커뮤니티 소유 OSS 도구 [Service Bus 탐색기](https://github.com/paolosalvatori/ServiceBusExplorer)가 ***아닙니다***.
>

## <a name="prerequisites"></a>사전 요구 사항

Service Bus Explorer 도구를 사용하려면 다음 작업을 수행해야 합니다. 

- Azure Service Bus 네임스페이스를 프로비전합니다.
- 메시지를 보내고 받을 큐를 만들거나 구독이 있는 토픽을 만들어 기능을 테스트합니다. 큐, 토픽 및 구독을 만드는 방법에 대해 알아보려면 다음 문서를 참조하세요. 
    - [빠른 시작 - 큐 만들기](service-bus-quickstart-portal.md)
    - [빠른 시작 - 토픽 만들기](service-bus-quickstart-topics-subscriptions-portal.md)
- 네임스페이스에서 이러한 역할 중 하나의 멤버인지 확인합니다. 
    - [Service Bus 데이터 소유자](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) 
    - [기여자](../role-based-access-control/built-in-roles.md#contributor) 
    - [소유자](../role-based-access-control/built-in-roles.md#owner)


## <a name="using-the-service-bus-explorer"></a>Service Bus 탐색기 사용

Azure Service Bus 탐색기를 사용하려면 보내기, 피킹 및 받기 작업을 수행하려는 Service Bus 네임스페이스로 이동해야 합니다.

큐에 대한 작업을 수행하려는 경우 탐색 메뉴에서 **'큐'** 를 선택합니다. 토픽에 대해 작업을 수행하려는 경우(또한 구독과 관련된 경우) **토픽** 을 선택합니다. 

:::image type="content" source="./media/service-bus-explorer/queue-topics-left-navigation.png" alt-text="엔터티 선택":::

**'큐'** 또는 **'토픽'** 을 선택한 후 특정 큐 또는 토픽을 선택합니다.

왼쪽 탐색 메뉴에서 **'Service Bus 탐색기(미리 보기)'** 를 선택합니다.

:::image type="content" source="./media/service-bus-explorer/left-navigation-menu-selected.png" alt-text="SB 탐색기 왼쪽 탐색 메뉴":::

> [!NOTE]
> Service Bus Explorer는 최대 1MB 크기의 메시지를 지원합니다. 

### <a name="sending-a-message-to-a-queue-or-topic"></a>메시지를 큐 또는 토픽으로 보내기

**큐** 또는 **토픽** 에 메시지를 보내려면 Service Bus Explorer에서 **_보내기_** 탭을 클릭합니다.

메시지를 작성하려면 여기서 다음을 수행합니다. 

1. **콘텐츠 형식** 으로 'Text/Plain', 'Application/Xml' 또는 'Application/Json'을 선택합니다.
2. 메시지 **콘텐츠** 를 추가합니다. 이전에 설정한 **콘텐츠 형식** 과 일치하는지 확인합니다.
3. **고급 속성** 을 설정합니다(선택 사항). 여기에는 상관 관계 ID, 메시지 ID, 레이블, ReplyTo, TTL(Time to Live) 및 예약된 큐에 넣기 시간(예약된 메시지)이 포함됩니다.
4. **사용자 지정 속성** 설정 - 사전 키에 대해 설정된 사용자 속성이 될 수 있습니다.

메시지를 작성한 후에는 보내기를 누릅니다.

:::image type="content" source="./media/service-bus-explorer/send-experience.png" alt-text="메시지 작성":::

보내기 작업이 성공적으로 완료되면 

* 큐로 보내는 경우 **활성 메시지** 메트릭 카운터가 증가합니다.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

* 토픽으로 보내는 경우 메시지가 라우팅된 구독에서 **활성 메시지** 메트릭 카운터가 증가합니다.

    :::image type="content" source="./media/service-bus-explorer/topic-after-send-metrics.png" alt-text="TopicAfterSendMetrics":::

### <a name="receiving-a-message-from-a-queue"></a>큐에서 메시지 받기

Service Bus 탐색기의 receive 함수를 사용하면 한 번에 하나의 메시지를 받을 수 있습니다. 수신 작업은 **ReceiveAndDelete** 모드를 사용하여 수행됩니다.

> [!IMPORTANT]
> Service Bus 탐색기에서 수행하는 수신 작업은 ***파괴 수신*** 입니다. 이 경우 메시지가 Service Bus 탐색기 도구에 표시될 때 큐에서 제거됩니다.
>
> 큐에서 메시지를 제거하지 않고 찾아보려면 기능 ***피킹(Peeking)*** 기능을 사용하는 것이 좋습니다.
>

큐(또는 배달 못한 편지 하위 큐)에서 메시지를 수신하려면 

1. Service Bus 탐색기에서 ***수신*** 탭을 클릭합니다.
2. 메트릭을 확인하여 수신할 **활성 메시지** 또는 **배달 못한 메시지** 가 있는지 확인합니다.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. ***큐** _ 또는 _ *_배달 못한 편지_** 하위 큐 중에서 선택합니다.

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadLetter":::

4. ***수신** _ 단추를 클릭하고 _ *_예_**를 클릭하여 '수신 및 삭제' 작업을 확인합니다.


수신 작업이 성공적으로 완료되면 아래와 같이 메시지 정보가 표에 표시됩니다. 표에서 메시지를 선택하여 세부 정보를 표시할 수 있습니다.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-queue-2.png" alt-text="큐에서 선택한 활성 메시지에 대한 메시지 세부 정보가 표시된 Azure Service Bus Explorer의 큐 창 스크린샷":::


### <a name="peeking-a-message-from-a-queue"></a>큐에서 메시지 피킹

피킹(peeking) 기능을 사용하면 Service Bus Explorer를 사용하여 큐 또는 배달 못한 편지 큐의 상위 32개 메시지를 볼 수 있습니다.

1. 큐에서 메시지를 피킹하려면 Service Bus 탐색기에서 ***피킹*** 탭을 클릭합니다.

    :::image type="content" source="./media/service-bus-explorer/peek-tab-selected.png" alt-text="PeekTab":::

2. 메트릭을 확인하여 피킹할 **활성 메시지** 또는 **배달 못한 메시지** 가 있는지 확인합니다.

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. 그런 다음, ***큐** _ 또는 _ *_배달 못한 편지_** 하위 큐 중에서 선택합니다.

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadLetter":::

4. ***피킹*** 단추를 클릭합니다. 

피킹 작업이 완료되면 아래와 같이 최대 32개의 메시지가 표에 표시됩니다. 특정 메시지에 대한 세부 정보를 보려면 표에서 해당 메시지를 선택합니다. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-queue-2.png" alt-text="PeekMessageFromQueue":::

> [!NOTE]
>
> 피킹은 파괴 작업이 아니기 때문에 메시지가 큐에서 제거되지 **않습니다**.
>

### <a name="receiving-a-message-from-a-subscription"></a>구독에서 메시지 받기

큐를 사용하는 것과 마찬가지로 구독 또는 배달 못한 편지 엔터티에 대해 ***수신*** 작업을 수행할 수 있습니다. 그러나 구독이 토픽의 컨텍스트 내에 있기 때문에 지정된 토픽에 대한 Service Bus 탐색기로 이동하여 수신 작업을 수행합니다.

> [!IMPORTANT]
> Service Bus 탐색기에서 수행하는 수신 작업은 ***파괴 수신*** 입니다. 이 경우 메시지가 Service Bus 탐색기 도구에 표시될 때 큐에서 제거됩니다.
>
> 큐에서 메시지를 제거하지 않고 찾아보려면 기능 ***피킹(Peeking)*** 기능을 사용하는 것이 좋습니다.
>

1. ***수신** _ 탭을 클릭하고 드롭다운 선택기에서 특정 _ *_구독_**을 선택합니다.

    :::image type="content" source="./media/service-bus-explorer/receive-subscription-tab-selected.png" alt-text="ReceiveTabSelected":::

2. ***구독** _ 또는 _ *_배달 못한 편지_** 하위 엔터티 중에서 선택합니다.

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadLetter":::

3. ***수신** _ 단추를 클릭하고 _ *_예_**를 클릭하여 '수신 및 삭제' 작업을 확인합니다.

수신 작업이 성공적으로 완료되면 아래와 같이 수신된 메시지가 표에 표시됩니다. 메시지 세부 정보를 보려면 메시지를 클릭합니다.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-subscription.png" alt-text="받은 활성 메시지에 대한 메시지 세부 정보가 표시된 Azure Service Bus Explorer의 수신 탭 스크린샷":::

### <a name="peeking-a-message-from-a-subscription"></a>구독에서 메시지 선택

구독 또는 배달 못한 편지 하위 엔터티에 대한 메시지를 찾아보기만 하려는 경우 구독에서 ***피킹*** 기능도 활용할 수 있습니다.

1. ***피킹** _ 탭을 클릭하고 드롭다운 선택기에서 특정 _ *_구독_**을 선택합니다.

    :::image type="content" source="./media/service-bus-explorer/peek-subscription-tab-selected.png" alt-text="PeekTabSelected":::

2. ***구독** _ 또는 _ *_배달 못한 편지_** 하위 엔터티 중에서 선택합니다.

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadLetter":::

3. ***피킹*** 단추를 클릭합니다.

피킹 작업이 완료되면 아래와 같이 최대 32개의 메시지가 표에 표시됩니다. 특정 메시지에 대한 세부 정보를 보려면 표에서 해당 메시지를 선택합니다. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-subscription.png" alt-text="PeekMessageFromSubscription":::

> [!NOTE]
>
> - 피킹은 파괴 작업이 아니기 때문에 메시지가 큐에서 제거되지 **않습니다**.


## <a name="next-steps"></a>다음 단계

   * Service Bus [큐](service-bus-queues-topics-subscriptions.md#queues) 및 [토픽](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)에 대해 자세히 알아보기
   * [Azure Portal을 통해 Service Bus 큐](service-bus-quickstart-portal.md)를 만드는 방법에 대해 자세히 알아보기
   * [Azure Portal을 통해 Service Bus 토픽 및 구독](service-bus-quickstart-topics-subscriptions-portal.md)을 만드는 방법에 대해 자세히 알아보기
