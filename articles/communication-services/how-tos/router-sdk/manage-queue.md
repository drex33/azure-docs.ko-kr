---
title: 작업 라우터에서 큐 관리
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services SDK를 사용하여 큐의 동작 관리
author: jasonshave
ms.author: jassha
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 10/14/2021
ms.custom: template-how-to
ms.openlocfilehash: 80f7aaf45cea9ced8721d9b13da4d8ab34b249f2
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130075845"
---
# <a name="manage-a-queue"></a>큐 관리

이 가이드에서는 작업 라우터 큐를 만들고 관리하는 단계를 간략하게 설명합니다.

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../quickstarts/create-communication-resource.md).
- 선택 사항: 빠른 시작을 완료하여 [작업 라우터 시작](../../quickstarts/router/get-started-router.md)

## <a name="create-a-queue"></a>큐 만들기

작업 라우터에서 간단한 큐를 만들려면 SDK를 사용하여 **큐 ID,** **이름** 및 **배포 정책 ID** 를 지정합니다. 작업 라우터가 큐를 만들 때 배포 정책이 있는지 확인하려면 배포 정책을 미리 만들어야 합니다. 다음 예제에서는 작업자에 대해 작업 제공이 생성되는 방식을 제어하는 배포 정책이 만들어집니다.

```csharp
var distributionPolicy = await client.SetDistributionPolicyAsync(
    id: "Longest_Idle_45s_Min1Max10",
    name: "Longest Idle matching with a 45s offer expiration; min 1, max 10 offers",
    offerTTL: TimeSpan.FromSeconds(45),
    mode: new LongestIdleMode(
        minConcurrentOffers: 1,
        maxConcurrentOffers: 10)
);

var queue = await client.SetQueueAsync(
    id: "XBOX_DEFAULT_QUEUE",
    name: "XBOX Default Queue",
    distributionPolicy: "Longest_Idle_45s_Min1Max10"
);
```
## <a name="update-a-queue"></a>큐 업데이트

작업 라우터 SDK는 또는 메서드가 호출될 때 새 큐를 만들거나 기존 큐를 `SetQueue` `SetQueueAsync` 업데이트합니다.

```csharp
var queue = await client.SetQueueAsync(
    id: "XBOX_DEFAULT_QUEUE",
    name: "XBOX Default Queue",
    distributionPolicy: "Longest_Idle_45s_Min1Max10"
);
```

## <a name="delete-a-queue"></a>큐 삭제

작업 라우터 SDK를 사용하여 큐를 삭제하려면 큐 ID 를 `DeleteQueue` 전달하는 또는 `DeleteQueueAsync` 메서드를 호출합니다. 

```csharp
var result = await client.DeleteQueueAsync("XBOX_DEFAULT_QUEUE");
```

> [!NOTE]
> 큐를 삭제하려면 큐에 할당된 활성 작업이 없는지 확인해야 합니다. 또한 문자열 값을 사용하여 ID별로 큐를 선택하는 식을 사용하는 분류 정책 또는 규칙에서 큐에 대한 참조가 없는지 확인합니다.