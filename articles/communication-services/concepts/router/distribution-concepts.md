---
title: Azure Communication Services 대한 작업 배포 개념
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services 작업 라우터 배포 개념에 대해 알아봅니다.
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: fdc64b5d789d017e6fcb8aab2cd48103235535ec
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319247"
---
# <a name="job-distribution-concepts"></a>작업 배포 개념

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Azure Communication Services Job Router는 유연한 배포 프로세스를 사용합니다. 여기에는 정책 및 작업 제안 수명 주기를 사용하여 작업자를 할당하는 과정이 포함됩니다. 이 문서에서는 작업을 배포할 수 있는 다양한 방법, 작업 제공 수명 주기 및 이 프로세스가 작업자에 미치는 영향에 대해 설명합니다.

## <a name="job-distribution-overview"></a>작업 배포 개요

작업자에게 작업을 배포하는 방법을 결정하는 것은 작업 라우터의 주요 기능이며 SDK는 사용자 환경을 사용자 지정할 수 있도록 비슷하게 유연하고 강화 가능한 모델을 제공합니다. [분류 개념](classification-concepts.md) 가이드에 설명된 대로 작업이 분류되면 작업 라우터는 작업 및 배포 정책의 특성에 따라 적합한 작업자를 찾습니다. 또는 작업자가 사용 중인 경우 작업자를 사용할 수 있게 되면 작업 라우터가 적합한 작업을 찾습니다. 작업자 적합성은 세 가지 특성으로 결정됩니다. [사용 가능한 채널,](#channel-configurations)해당 [기능](#worker-abilities) 및 [상태.](#worker-status) 적합한 작업자가 발견되면 작업을 할당할 수 있는 열린 채널이 있는지 확인합니다.

이러한 두 가지 방법은 작업 라우터가 작업 또는 작업자 검색을 시작하는 방법의 주요 개념입니다.

### <a name="finding-workers-for-a-job"></a>작업에 대한 작업자 찾기

작업이 [분류 프로세스를](classification-concepts.md)완료하면 작업 라우터는 큐에 구성된 배포 정책을 적용하여 작업에서 작업자 선택기를 충족하는 작업자를 하나 이상 선택하고 해당 작업자가 작업을 수행하도록 제안을 생성합니다. 

### <a name="finding-a-job-for-a-worker"></a>작업자에 대한 작업 찾기

작업자에 대한 작업을 찾기 위해 작업 라우터를 트리거하는 몇 가지 시나리오가 있습니다.

- 작업자가 작업 라우터에 등록하는 경우
- 작업이 닫혀 있고 채널이 해제되는 경우
- 작업 제안이 거부되거나 해지된 경우

배포 프로세스는 작업에 대한 작업자를 찾는 것과 동일합니다. 작업자가 발견되면 [제안이](#job-offer-overview) 생성됩니다.

## <a name="worker-overview"></a>작업자 개요

작업자는 SDK를 사용하여 작업 라우터에 **등록하고** 다음 기본 정보를 입력합니다.

- 작업자 ID 및 이름
- 큐 ID
- 총 용량(수)
- **채널 구성** 목록
- 레이블 집합 

작업 라우터는 수동으로 또는 자동으로 **등록을 취소하더라도** 등록된 작업자에 대한 참조를 항상 보유합니다.

### <a name="channel-configurations"></a>채널 구성

각 작업에는 미리 구성된 작업 라우터 채널 또는 사용자 지정 채널을 나타내는 채널 ID 속성이 필요합니다. 채널 구성은 `channelId` 문자열과 `capacityCostPerJob` 숫자로 구성됩니다. 함께 통신의 추상 모드와 해당 모드의 비용을 나타냅니다. 예를 들어 대부분의 사용자는 한 번에 한 번의 전화 통화만 할 수 있으므로 `Voice` 채널의 비용이 높을 수 `100` 있습니다. 또는 채팅과 같은 특정 워크로드의 동시성이 높을 수 있습니다. 즉, 비용이 더 낮습니다. 채널 구성은 작업을 할당하거나 연결할 수 있는 열린 슬롯으로 생각할 수 있습니다. 다음 예제에서는 이 점에 대해 설명합니다.

```csharp
await client.RegisterWorkerAsync(
    id: "EdmontonWorker",
    queueIds: new[] { "XBOX_Queue", "XBOX_Escalation_Queue" },
    totalCapacity: 100,
    labels: new LabelCollection
    {
        { "Location", "Edmonton" },
        { "XBOX_Hardware", 7 },
    },
    channelConfigurations: new List<ChannelConfiguration>
    {
        new (
            channelId: ManagedChannels.AcsVoiceChannel,
            capacityCostPerJob: 100
        ),
        new (
            channelId: ManagedChannels.AcsChatChannel,
            capacityCostPerJob: 33
        )
    }
);
```

위의 작업자는 각각 채널당 고유한 비용이 있는 두 개의 채널 구성으로 등록됩니다. 효과적인 결과는 가 세 개의 동시 작업 또는 하나의 작업을 처리할 수 있다는 `EdmontonWorker` `ManagedChannels.AcsChatChannel` `ManagedChannels.AcsVoiceChannel` 것입니다.

작업 라우터에는 사용할 수 있도록 다음과 같은 미리 구성된 채널 ID가 포함되어 있습니다.

- ManagedChannels.AcsChatChannel
- ManagedChannels.AcsVoiceChannel
- ManagedChannels.AcsSMSChannel

다음과 같이 작업 라우터 SDK를 사용하여 새 추상 채널을 만들 수 있습니다.

```csharp
await client.SetChannelAsync(
    id: "MakePizza",
    name: "Make a pizza"
);

await client.SetChannelAsync(
    id: "MakeDonairs",
    name: "Make a donair"
);

await client.SetChannelAsync(
    id: "MakeBurgers",
    name: "Make a burger"
);
```

그런 다음 작업자를 등록할 때 채널을 사용하여 다음과 같이 해당 채널 ID와 일치하는 작업을 수행할 수 있는 기능을 나타낼 수 있습니다.

```csharp
await client.RegisterWorkerAsync(
    id: "PizzaCook",
    queueIds: new[] { "PizzaOrders", "DonairOrders", "BurgerOrders" },
    totalCapacity: 100,
    labels: new LabelCollection
    {
        { "Location", "New Jersey" },
        { "Language", "English" },
        { "PizzaMaker", 7 },
        { "DonairMaker", 10},
        { "BurgerMaker", 5}
    },
    channelConfigurations: new List<ChannelConfiguration>
    {
        new (
            channelId: MakePizza,
            capacityCostPerJob: 50
        ),
        new (
            channelId: MakeDonair,
            capacityCostPerJob: 33
        ),
        new (
            channelId: MakeBurger,
            capacityCostPerJob: 25        
        )
    }
);
```

위의 예제에서는 각각 작업당 고유한 비용이 있는 세 개의 추상 채널을 보여 줍니다. 따라서 작업자에 대해 다음 작업 동시성 예제를 사용할 수 있습니다. `PizzaCook`

| MakePizza | MakeDonair | Make사진 만들기 | 점수 매기기 |
|--|--|--|--|
| 2         |            |            | 100   |
|           | 3          |            | 99    |
| 1         | 1          |            | 83    |
|           | 2          | 1          | 91    |
|           |            | 4          | 100   |
|           | 1          | 2          | 83    |

### <a name="worker-abilities"></a>작업자 기능

배포 프로세스는 작업자가 가질 수 있는 사용 가능한 채널 외에도 등록된 작업자의 레이블 컬렉션을 사용하여 작업에 대한 적합성을 확인합니다. 위의 피자 쿡 예제에서 작업자에는 다음으로 구성된 레이블 컬렉션이 있습니다.

```csharp
new LabelCollection
    {
        { "Location", "New Jersey" },
        { "Language", "English" },
        { "PizzaMaker", 7 },
        { "DonairMaker", 10},
        { "BurgerMaker", 5}
    }
```

작업이 제출되면 **작업자 선택기를** 사용하여 특정 작업 단위에 대한 요구 사항을 정의합니다. 작업에 도네어를 잘 만드는 영어가 필요한 경우 SDK 호출은 다음과 같습니다.

```csharp
await client.CreateJobAsync(
    channelId: "MakeDonair",
    channelReference: "ReceiptNumber_555123",
    queueId: "DonairOrders",
    priority: 1,
    workerSelectors: new List<LabelSelector>
    {
        new (
            key: "DonairMaker",
            @operator: LabelOperator.GreaterThanEqual,
            value: 8),
        new (
            key: "English",
            @operator: LabelOperator.GreaterThan,
            value: 5)
    });
```

### <a name="worker-status"></a>작업자 상태

작업 라우터는 채널 구성에 따라 작업자에 대한 동시 작업을 처리할 수 있기 때문에 가용성 개념은 다음 세 가지 상태로 표시됩니다.

**활성 -** 작업자가 작업 라우터에 등록되고 작업을 수락하려고 합니다.

**드레닝 -** 작업자가 작업 라우터에 등록 취소되어 있지만 현재 하나 이상의 활성 작업이 할당되어 있습니다.

**비활성 -** 작업자가 작업 라우터에 등록 취소되었으며 활성 작업이 없습니다.

## <a name="job-offer-overview"></a>작업 제안 개요

배포 프로세스에서 채널이 열려 있고 상태가 올바른 적합한 작업자를 찾으면 작업 제안이 생성되고 이벤트가 전송됩니다. 배포 정책에는 제안에 대해 다음과 같은 구성 가능한 속성이 포함됩니다.

**OfferTTL -** 생성된 각 제안에 대한 Time-to-Live

**모드 -** 및 속성을 모두 포함하는 **배포** `minConcurrentOffers` `maxConcurrentOffers` 모드입니다.

> [!Important]
> 작업자에 대해 작업 제안이 생성되면 작업의 채널 ID와 일치하는 채널 구성 중 하나를 사용합니다. 이 채널의 사용량은 작업자에서 해당 채널에 대한 추가 용량을 사용할 수 없는 한 작업자가 다른 제안을 받지 않음을 의미합니다. 작업자가 제안을 거부하거나 제안이 만료되면 채널이 릴리스됩니다.

### <a name="job-offer-lifecycle"></a>작업 제안 수명 주기

다음 작업 제품 수명 주기 이벤트는 Event Grid 구독을 통해 관찰할 수 있습니다.

- RouterWorkerOfferIssued
- RouterWorkerOfferAccepted
- RouterWorkerOfferDeclined
- RouterWorkerOfferExpired
- RouterWorkerOfferRevoked

> [!NOTE]
> 다른 모든 이벤트가 내부적으로 생성되는 동안 SDK를 사용하여 작업자가 제안을 수락하거나 거절할 수 있습니다.

## <a name="distribution-modes"></a>배포 모드

작업 라우터에는 다음과 같은 배포 모드가 포함됩니다.

**LongestIdleMode -** 큐에서 가장 긴 유휴 작업자에 대한 제안을 생성합니다.

**RoundRobinMode -** 작업자 컬렉션이 지정된 경우 ID별로 정렬된 마지막 작업자 다음에 다음 작업자를 선택합니다.

**BestWorkerMode -** 작업 라우터의 [RuleEngine을](router-rule-concepts.md) 사용하여 레이블에 따라 작업자 선택

## <a name="distribution-summary"></a>배포 요약

작업자의 상태, 채널 구성/용량, 배포 정책 모드 및 제안 동시성과 같은 여러 요인에 따라 작업 제공이 생성되는 방식에 영향을 줄 수 있습니다. 간단한 구현으로 시작하고 요구 사항에 따라 복잡성을 추가하는 것이 좋습니다.
