---
title: Azure Communication Services 대한 작업 분류 개념
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services 작업 라우터 분류 개념에 대해 알아봅니다.
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 4511c26f7952f69e17ceefe99ff79f6439e5857b
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130075865"
---
# <a name="job-classification-concepts"></a>작업 분류 개념

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Azure Communication Services 작업 라우터는 작업이 제출될 때 **분류라는** 프로세스를 사용합니다. 이 문서에서는 작업을 분류할 수 있는 다양한 방법과 이 프로세스가 작업에 미치는 영향에 대해 설명합니다.

## <a name="job-classification-overview"></a>작업 분류 개요

작업 라우터는 작업을 분류하는 데 두 가지 기본 메서드를 사용합니다. static 또는 dynamic. 호출 애플리케이션에 큐 ID, 우선 순위 또는 작업자 선택기에 대한 지식이 있는 경우 분류 정책 없이 작업을 제출할 수 있습니다. **정적 분류** 라고 합니다. 작업 라우터가 큐 ID를 결정하도록 하려면 분류 정책을 사용하여 작업의 속성을 수정할 수 있습니다. 동적 **분류** 라고도 함.

작업 라우터 SDK를 사용하여 작업을 제출하면 분류 프로세스에서 이벤트가 Azure Communication Services Event Grid 구독으로 전송됩니다. 분류 수명 주기의 일부로 생성된 이벤트는 작업 라우터가 수행 중인 작업에 대한 인사이트를 제공합니다. 예를 들어 성공적인 분류는 **RouterJobClassified를** 생성하고 실패하면 **RouterJobClassificationFailed 가** 생성됩니다.

작업을 분류하는 프로세스에는 필요에 따라 다음 속성을 설정하는 작업이 포함됩니다.

- 큐 ID
- 우선 순위
- 작업자 선택기

## <a name="static-classification"></a>정적 분류

미리 정의된 큐 ID, 우선 순위 및 작업자 선택기를 사용하여 작업을 제출하면 신속하게 시작할 수 있습니다. 작업 라우터는 작업자에 할당하기 전에 분류 정책을 지정하여 업데이트하지 않는 한 작업을 제출한 후 이러한 속성을 수정하지 않습니다. 제출 후 작업의 분류 정책 속성을 업데이트하여 동적 분류 프로세스를 트리거할 수 있습니다.

> [!NOTE]
> 작업 라우터 SDK를 사용하여 작업 속성을 수동으로 업데이트하여 동적 분류 결과를 재정의할 수 있습니다. 예를 들어 처음에는 정적 큐 ID를 지정한 다음, 작업을 분류 정책 ID로 업데이트하여 동적으로 분류한 다음 큐 ID를 재정의할 수 있습니다.

## <a name="dynamic-classification"></a>동적 분류

작업을 제출할 때 분류 정책을 지정하면 작업 라우터가 큐 ID, 우선 순위를 동적으로 할당하고 작업자 선택기를 수정할 수 있습니다. 이 유형의 분류는 호출 애플리케이션이 런타임에 큐 ID를 비롯한 작업 속성에 대한 지식이 없어도 되므로 유용합니다.

### <a name="queue-selectors"></a>큐 선택기

분류 정책은 분류 `QueueSelector` 프로세스에서 특정 작업에 대해 선택할 큐 ID를 결정하는 데 사용되는 를 참조할 수 있습니다. 작업 라우터에는 다음과 같은 `QueueSelector` 유형이 있으며 분류하는 동안 큐 선택 프로세스에 적용할 수 있는 옵션이 있습니다.

**QueueLabelSelector -** 작업 라우터 큐를 만들 때 작업 분류 중에 큐 선택 프로세스에 도움이 되는 레이블을 지정할 수 있습니다. 이 유형의 선택기는 형식 컬렉션을 사용하여 `LabelSelectorAttachment` 분류 프로세스 중에 큐를 가장 유연하게 선택할 수 있습니다. 이 선택기를 사용하여 작업 분류 프로세스에서 해당 레이블에 따라 큐 ID를 선택할 수 있도록 합니다. 자세한 내용은 아래 섹션을 [참조하세요.](#using-labels-and-selectors-in-classification)

**QueueIdSelector -** 이 선택기를 사용하면 여러 규칙 엔진 중 하나를 사용하여 규칙의 결과에 따라 작업의 큐 ID를 확인할 수 있습니다. 자세한 내용은 [RouterRule 개념](router-rule-concepts.md) 페이지를 읽어보십시오.

### <a name="worker-selectors"></a>작업자 선택기

분류 정책의 작업자 선택기에는 분류 프로세스에서 레이블에 `LabelSelectorAttachment` 따라 작업자 선택기를 작업에 연결하는 데 사용되는 형식 컬렉션이 포함되어 있습니다. 자세한 내용은 아래 섹션을 [참조하세요.](#using-labels-and-selectors-in-classification)

### <a name="prioritization-rule"></a>우선 순위 지정 규칙

작업의 우선 순위는 여러 규칙 엔진 중 하나를 사용하여 분류하는 동안 확인할 수 있습니다. 작동 방식과 `QueueIdSelector` 유사합니다. 자세한 내용은 [RouterRule 개념](router-rule-concepts.md) 페이지를 읽어보십시오.

## <a name="using-labels-and-selectors-in-classification"></a>분류에서 레이블 및 선택기 사용

작업 라우터는 작업, 작업자 및 큐의 키/값 쌍 "레이블"을 사용하여 라우팅에 대한 다양한 결정을 내도록 합니다. 에서 를 사용하는 경우 `LabelSelectorAttachment` `QueueSelector` 필터처럼 작동합니다. 의 컨텍스트 내에서 사용되는 경우 `WorkerSelectors` 작업으로 만든 초기 집합에 선택기를 연결합니다. 사용할 수 있는 형식은 다음과 `LabelSelectorAttachment` 같습니다.

**정적 레이블 선택기 -** 항상 지정된 를 `LabelSelector` 연결합니다.

**조건부 레이블 선택기 -** 규칙에 의해 정의된 조건을 평가합니다.  로 확인되면 `true` 지정된 선택기 컬렉션이 적용됩니다.

**통과 레이블 선택기 -** 및 키를 사용하여 키가 `LabelOperator` 있는지 확인합니다. 이 선택기를 사용할 수는 `QueueLabelSelector` 레이블 집합에 따라 큐를 일치 시키기 위해 합니다. 와 함께 사용하면 `WorkerSelectors` 작업의 키/값 쌍이 작업의 `WorkerSelectors` 에 연결됩니다.

**규칙 레이블 선택기 -** 여러 규칙 엔진 중 하나에서 선택기 컬렉션을 소스로 지정합니다. 자세한 내용은 [RouterRule 개념](router-rule-concepts.md) 페이지를 읽어보십시오.

**가중 할당 레이블 선택기 -** `WeightedAllocation` 각각 백분율 기반 가중치 및 가중치 할당에 따라 적용할 선택기 컬렉션을 지정하는 개체의 컬렉션입니다. 예를 들어 작업의 30%가 "Contoso"로 이동하고 작업의 70%가 "Fabrikam"으로 이동하도록 할 수 있습니다.

## <a name="reclassifying-a-job"></a>작업 재분류
작업이 분류되면 다음과 같은 방법으로 재분류할 수 있습니다.

1. 작업 레이블을 업데이트하면 작업 라우터가 이전 분류 정책을 사용하여 새 레이블을 평가할 수 있습니다.
2. 작업의 분류 정책 ID를 업데이트할 수 있습니다. 이렇게 하면 작업 라우터가 새 정책에 대해 기존 작업을 처리합니다.
3. 예외 정책 **트리거는** 작업 재분류를 요청하는 **동작을** 취할 수 있습니다. 

> [!NOTE]
> 작업 라우터 SDK에는 `UpdateJobLabels` 작업 라우터가 재분류 프로세스를 실행하지 않고 레이블을 업데이트하는 메서드가 포함되어 있습니다.