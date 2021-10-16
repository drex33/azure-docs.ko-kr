---
title: 작업 분류
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services Sdk를 사용 하 여 작업 속성 변경
author: jasonshave
ms.author: jassha
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 10/14/2021
ms.custom: template-how-to
ms.openlocfilehash: 0648334ef4baef6c753afa222a5532356ad67590
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130075846"
---
# <a name="classifying-a-job"></a>작업 분류

작업에 작업자 선택기를 연결 하는 동시에 작업 라우터의 분류 정책을 사용 하 여 큐 및 우선 순위를 동적으로 확인 하는 방법을 알아봅니다.

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../quickstarts/create-communication-resource.md).
- 선택 사항: [작업 라우터를 시작](../../quickstarts/router/get-started-router.md) 하려면 빠른 시작을 완료 하세요.

## <a name="static-classification"></a>정적 분류

SDK를 사용 하 여 작업을 만들 때 큐, 우선 순위 및 작업자 선택기만 지정 합니다. 이 메서드를 **정적 분류** 라고 합니다. 다음 예제에서는 우선 순위가 인 작업을에 추가 하 `XBOX_DEFAULT_QUEUE` `1` 고 작업자에 `XBOX_Hardware` 게 보다 크거나 같은 기술을 사용 해야 합니다 `5` .

> [!NOTE]
> 작업은 처음에 분류 정책 없이 만들어진 경우에도 [제출 후 다시 분류할](#reclassify-a-job-after-submission) 될 수 있습니다. 이 경우 작업 라우터는 **레이블에** 대해 정책의 동작을 평가 하 고 필요한 사항을 큐, 우선 순위 및 작업자 선택기로 조정 합니다.

```csharp
var job = await client.CreateJobAsync(
    channelId: ManagedChannels.AcsVoiceChannel,
    channelReference: "12345",
    queueId: queue.Value.Id,
    priority: 1,
    workerSelectors: new List<LabelSelector>
    {
        new (
            key: "Location",
            @operator: LabelOperator.Equal,
            value: "Edmonton")
    });

// returns a new GUID such as: 4ad7f4b9-a0ff-458d-b3ec-9f84be26012b
```

## <a name="dynamic-classification"></a>동적 분류

위에서 설명한 대로 작업을 제출 하는 쉬운 방법은 전송 중에 우선 순위, 큐 및 작업자 선택기를 지정 하는 것입니다. 이렇게 하는 경우 발신자는 이러한 특성에 대 한 지식이 있어야 합니다. 발신자가 작업 라우터의 동작에 대 한 내부 작업에 대 한 명시적 지식을 보유 하지 않도록 하기 위해 발신자는 동적 동작을 호출 하기 위해 일반 **레이블** 컬렉션과 함께 **분류 정책을** 지정할 수 있습니다.

### <a name="create-a-classification-policy"></a>분류 정책 만들기

다음 분류 정책에서는 낮은 코드 [Powerfx](https://powerapps.microsoft.com/en-us/blog/what-is-microsoft-power-fx/) 언어를 사용 하 여 큐와 우선 순위를 모두 선택 합니다. 식이 있는 경우 해당 작업 레이블이 같은 작업 레이블이 있으면이를 검색 하 고 `Region` `NA` , 그렇지 않으면를 입력 합니다 `XBOX_NA_QUEUE` `XBOX_DEFAULT_QUEUE` .  를 찾을 수 없는 경우에는 `XBOX_DEFAULT_QUEUE` 에서 정의한 대로 자동으로 대체 큐에 작업이 전송 됩니다 `DEFAULT_QUEUE` `fallbackQueueId` .  또한 이라는 레이블이 일치 하는 경우 우선 순위는가 됩니다 `10` `Hardware_VIP` . 그렇지 않은 경우에는 `1` 입니다.

```csharp
var policy = await client.SetClassificationPolicyAsync(
    id: "XBOX_NA_QUEUE_Priority_1_10",
    name: "Select XBOX Queue and set priority to 1 or 10",
    queueSelector: new QueueIdSelector(
        new ExpressionRule("If(job.Region = \"NA\", \"XBOX_NA_QUEUE\", \"XBOX_DEFAULT_QUEUE\")")
    ),
    workerSelectors: new List<LabelSelectorAttachment>
    {
        new StaticLabelSelector(
            new LabelSelector(
                key: "Language",
                @operator: LabelOperator.Equal,
                value: "English")
        )
    },
    prioritizationRule: new ExpressionRule("If(job.Hardware_VIP = true, 10, 1)"),
    fallbackQueueId: "DEFAULT_QUEUE"
);
```

### <a name="submit-the-job"></a>작업 제출

다음 예에서는 분류 정책이 작업 레이블을 평가 하 게 됩니다. 결과는 이라는 큐에 작업을 저장 하 `XBOX_NA_QUEUE` 고 우선 순위를로 설정 합니다 `1` .

```csharp
var dynamicJob = await client.CreateJobAsync(
    channelId: ManagedChannels.AcsVoiceChannel,
    channelReference: "my_custom_reference_number",
    classificationPolicyId: "XBOX_NA_QUEUE_Priority_1_10",
    labels: new LabelCollection()
    {
        { "Region", "NA" },
        { "Caller_Id", "tel:7805551212" },
        { "Caller_NPA_NXX", "780555" },
        { "XBOX_Hardware", 7 }
    }
);

// returns a new GUID such as: 4ad7f4b9-a0ff-458d-b3ec-9f84be26012b
```

## <a name="reclassify-a-job-after-submission"></a>제출 후 작업 다시 분류

작업 라우터가 정책을 사용 하 여 작업을 분류 하 고 분류 한 후에는 SDK를 사용 하 여 해당 작업을 다시 분류 하는 옵션이 있습니다. 다음 예에서는 작업 `10` **ID** 를 지정 하 고, 메서드를 호출 하 고, 레이블을 포함 하 여 작업의 우선 순위를로 높이는 한 가지 방법을 보여 줍니다 `ReclassifyJobAsync` `Hardware_VIP` .

```csharp
var reclassifiedJob = await client.ReclassifyJobAsync(
    jobId: "4ad7f4b9-a0ff-458d-b3ec-9f84be26012b",
    classificationPolicyId: null,
    labelsToUpdate: new LabelCollection()
    {
        { "Hardware_VIP", true }
    }
);
```
