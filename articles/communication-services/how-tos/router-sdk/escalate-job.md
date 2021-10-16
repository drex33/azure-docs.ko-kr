---
title: 작업 라우터에서 작업 에스컬레이션
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services SDK를 사용하여 작업 에스컬레이션
author: jasonshave
ms.author: jassha
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 10/14/2021
ms.custom: template-how-to
ms.openlocfilehash: a3d8139326403747900c9cc870c2d62138c625fb
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130075898"
---
# <a name="escalate-a-job"></a>작업 에스컬레이션

이 가이드에서는 예외 정책을 사용하여 큐에서 작업을 에스컬레이션하는 방법을 보여줍니다.

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../quickstarts/create-communication-resource.md).
- 선택 사항: 빠른 시작을 완료하여 [작업 라우터 시작](../../quickstarts/router/get-started-router.md)
- 선택 사항: [작업 분류 방법 가이드](job-classification.md) 검토

## <a name="escalation-overview"></a>에스컬레이션 개요

에스컬레이션은 작업을 다른 큐로 이동하거나 더 높은 우선 순위를 지정하는 등 여러 가지 동작의 형태를 사용할 수 있습니다. 우선 순위가 높은 작업은 우선 순위가 낮은 작업보다 작업자에게 배포됩니다. 이 방법 가이드에서는 에스컬레이션 정책을 사용하여 새 큐를 설정하고 작업의 우선 순위를 높입니다.

## <a name="exception-policy-configuration"></a>예외 정책 구성

시간별로 트리거되고 재분류되는 작업의 동작을 사용하는 일반 큐에 연결하는 예외 정책을 만듭니다.

```csharp
// create the exception policy
await client.SetExceptionPolicyAsync(
    id: "Escalate_XBOX_Policy",
    name: "Escalate XBOX Requests to the XBOX Escalation Queue and set the Priority to 10 after 5 minutes",
    rules: new List<ExceptionRule>()
    {
        new (
            id: "Escalated_Rule",
            trigger: new WaitTimeExceptionTrigger(TimeSpan.FromMinutes(5)),
            actions: new List<ExceptionAction>
            {
                new ManualReclassifyExceptionAction("Increase Priority", null, 10),
                new ReclassifyExceptionAction("EscalateReclassifyExceptionAction")
                {
                    LabelsToUpsert = new LabelCollection(
                        new Dictionary<string, object>
                    {
                        ["Escalated"] = true,
                    })
                }
            }
        )
    });
```

## <a name="queue-configuration"></a>큐 구성

일반 및 에스컬레이션된 작업에 필요한 큐를 만들고 예외 정책을 일반 큐에 할당합니다.

> [!NOTE]
> 이 단계에서는 이름이 인 배포 정책을 이미 만들었다고 `Round_Robin_Policy` 가정합니다.

```csharp
// create a queue for regular Jobs and attach the exception policy
await client.SetQueueAsync(
    id: "XBOX_Queue",
    name: "XBOX Queue",
    distributionPolicyId: "Round_Robin_Policy",
    exceptionPolicyId: "XBOX_Escalate_Policy"
);

// create a queue for escalated Jobs
await client.SetQueueAsync(
    id: "XBOX_Escalation_Queue",
    name: "XBOX Escalation Queue",
    distributionPolicyId: "Round_Robin_Policy"
);
```

## <a name="classification-policy-configuration"></a>분류 정책 구성

분류 정책을 만들어 작업에 추가된 새 레이블을 처리합니다. 이 정책은 레이블을 평가하고 `Escalated` 큐에 작업을 할당합니다. 또한 정책은 다음을 추가합니다.

```csharp
await client.SetClassificationPolicyAsync(
    id: "Classify_XBOX_Voice_Jobs",
    name: "Classify XBOX Voice Jobs",
    queueSelector: new QueueIdSelector(
        new ExpressionRule(
            "If(job.Escalated = true, \"XBOX_Queue\", \"XBOX_Escalation_Queue\")")),
    workerSelectors: null,
    prioritizationRule: null,
    fallbackQueueId: "Default");
```

## <a name="job-lifecycle"></a>작업 수명 주기

작업을 제출할 때 분류 정책 ID를 다음과 같이 지정합니다. 이 특정 예제의 경우 요구 사항은 값이 숫자보다 크거나 같은 레이블이 있는 작업자를 찾는 `XBOX_Hardware` `7` 것입니다.

```csharp
await client.CreateJobWithClassificationPolicyAsync(
    channelId: ManagedChannels.AcsVoiceChannel,
    classificationPolicyId: "Classify_XBOX_Voice_Jobs",
    workerSelectors: new List<LabelSelector>
    {
        new (
            key: "XBOX_Hardware",
            @operator: LabelOperator.GreaterThanEqual,
            value: 7)
    }
);
```

구성이 완료되고 작업을 제출할 준비가 되면 다음 수명 주기 단계가 수행됩니다.

1. 작업이 작업 라우터로 전송되고 분류 정책이 연결되어 있기 때문에 작업이 평가되고 및 를 모두 `RouterJobReceived` 생성합니다. `RouterJobClassified`
2. 다음으로, 5분 타이머가 시작되고 작업자를 할당할 수 없으면 결국 트리거됩니다. 등록된 작업자가 없다고 가정하면 및 다른 가 `RouterJobExceptionTriggered` 생성됩니다. `RouterJobClassified`
3. 이 시점에서 작업은 에 있고 `XBOX_Escalation_Queue` 우선 순위는 로 `10` 설정됩니다.