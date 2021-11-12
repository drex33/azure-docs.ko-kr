---
title: Azure Communication Services 대한 라우터 규칙 엔진 개념
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services 작업 라우터 규칙 엔진 개념에 대해 알아봅니다.
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 5d53f2492888323107fa539283f128ed6b6ebd7c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319228"
---
# <a name="job-router-rules-engine-concepts"></a>작업 라우터 규칙 엔진 개념

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Azure Communication Services 작업 라우터는 데이터를 처리하고 작업 및 작업자에 대한 결정을 내리는 데 사용할 수 있는 규칙 엔진을 사용합니다. 이 문서에서는 규칙 엔진의 수행 및 구현에 적용하려는 이유를 설명합니다.

## <a name="rules-engine-overview"></a>규칙 엔진 개요

구현의 동작을 제어하는 데는 종종 복잡한 의사 결정이 포함될 수 있습니다. 작업 라우터는 다양한 규칙 엔진을 사용하여 프로그래밍 방식으로 동작을 호출하는 유연한 방법을 제공합니다. 작업 라우터의 규칙 엔진은 일반적으로 작업, 큐 또는 작업자와 같은 개체에 정의된 **레이블** 집합을 입력으로 받아 규칙을 적용하고 출력을 생성합니다.

> [!NOTE]
> 규칙 엔진은 일반적으로 레이블을 입력으로 사용하지만 레이블을 평가하지 않고 큐 ID와 같은 값을 설정할 수도 있습니다.

작업 라우터에서 규칙을 적용하는 위치에 따라 결과가 달라질 수 있습니다. 예를 들어 분류 정책은 작업의 입력에 정의된 레이블에 따라 큐 ID를 선택할 수 있습니다. 또 다른 예제에서 배포 정책은 에 정의된 사용자 지정 점수 매기기 규칙을 사용하여 최상의 작업자를 찾을 수 `RouterRule` 있습니다.

### <a name="example-use-a-static-rule-in-a-classification-policy-to-set-the-queue-id"></a>예: 분류 정책에서 정적 규칙을 사용하여 큐 ID 설정

이 예제에서는 형식인 를 사용하여 분류 정책 ID 를 `StaticRule` `RouterRule` 참조하는 모든 작업의 큐 ID를 설정할 수 `XBOX_QUEUE_POLICY` 있습니다.

```csharp
await client.SetClassificationPolicyAsync(
    id: "XBOX_QUEUE_POLICY",
    queueSelector: new QueueIdSelector(new StaticRule("XBOX"))
)
```
## <a name="routerrule-types"></a>RouterRule 형식

작업 처리 방법에 유연성을 제공하기 위해 작업 라우터에는 다음과 같은 `RouterRule` 유형이 있습니다.

**정적 규칙 -** 이 규칙은 특정 큐 ID 선택과 같은 정적 값을 지정하는 데 사용할 수 있습니다.

**식 규칙 -** 식 규칙은 [PowerFx](https://powerapps.microsoft.com/en-us/blog/what-is-microsoft-power-fx/) 언어를 사용하여 규칙을 인라인 식으로 정의합니다.

**Azure 함수 규칙 -** URI 및 를 지정하면 `AzureFunctionRuleCredential` 이 규칙을 사용하면 작업 라우터가 입력 레이블을 페이로드로 전달하고 출력 값으로 다시 응답할 수 있습니다. 요구 사항이 복잡한 경우 이 규칙 유형을 사용할 수 있습니다.

> [!NOTE]
> 직접 **맵 규칙은** 작업 라우터 SDK의 일부이지만 현재 에서만 지원됩니다. `NearestQueueLabelSelector`
