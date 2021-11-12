---
title: Azure Communication Services에 대 한 작업 분류 개념
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services 작업 라우터 분류 개념에 대해 알아봅니다.
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: c31c0831f625f65075f3c1f5cde2647ebc73ce64
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308430"
---
# <a name="job-classification-concepts"></a>작업 분류 개념

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Azure Communication Services 작업 라우터는 작업이 제출 될 때 **분류** 라는 프로세스를 사용 합니다. 이 문서에서는 작업을 분류할 수 있는 다양 한 방법과이 프로세스에 미치는 영향을 설명 합니다.

## <a name="job-classification-overview"></a>작업 분류 개요

작업 라우터는 작업을 분류 하는 두 가지 기본 방법을 사용 합니다. 정적 또는 동적입니다. 호출 응용 프로그램에 큐 ID, 우선 순위 또는 작업자 선택기에 대 한 정보가 있는 경우 분류 정책을 사용 하지 않고 작업을 제출할 수 있습니다. **정적 분류** 라고 합니다. 작업 라우터가 큐 ID를 결정 하는 것을 선호 하는 경우 작업의 속성을 수정 하는 데 분류 정책을 사용할 수 있습니다. **동적 분류** 라고 합니다.

작업 라우터 SDK를 사용 하 여 작업을 제출 하면 분류 프로세스로 인해 이벤트가 Azure Communication Services Event Grid 구독으로 전송 됩니다. 분류 수명 주기의 일부로 생성 된 이벤트는 작업 라우터가 수행 하 고 있는 작업에 대 한 정보를 제공 합니다. 예를 들어 성공적으로 분류 되 면 **RouterJobClassificationFailed** 가 **분류** 되 고 오류가 발생 하 여 오류가 생성 됩니다.

작업을 분류 하는 프로세스에는 선택적으로 다음 속성을 설정 해야 합니다.

- 큐 ID
- 우선 순위
- 작업자 선택기

## <a name="static-classification"></a>정적 분류

미리 정의 된 큐 ID, 우선 순위 및 작업자 선택기를 사용 하 여 작업을 제출 하면 신속 하 게 시작할 수 있습니다. 작업을 제출한 후에는 작업을 제출한 후 작업을 제출한 후에 작업 라우터는 해당 속성을 수정 하지 않습니다. 제출 후 작업의 분류 정책 속성을 업데이트 하 여 동적 분류 프로세스를 트리거할 수 있습니다.

> [!NOTE]
> 작업 속성을 수동으로 업데이트 하기 위해 작업 라우터 SDK를 사용 하 여 동적 분류의 결과를 재정의할 수 있습니다. 예를 들어 처음에는 정적 큐 ID를 지정한 다음, 동적으로 분류 될 분류 정책 ID를 사용 하 여 작업을 업데이트 한 다음 큐 ID를 재정의할 수 있습니다.

## <a name="dynamic-classification"></a>동적 분류

작업을 제출할 때 분류 정책을 지정 하면 작업 라우터가 큐 ID, 우선 순위를 동적으로 할당 하 고 잠재적으로 작업자 선택기를 수정할 수 있습니다. 이 유형의 분류는 호출 하는 응용 프로그램이 런타임에 큐 ID를 비롯 한 작업 속성에 대 한 지식을 가질 필요가 없기 때문에 유용 합니다.

### <a name="queue-selectors"></a>큐 선택기

분류 정책은 `QueueSelector` 특정 작업에 대해 선택 되는 큐 ID를 확인 하기 위해 분류 프로세스에서 사용 되는을 참조할 수 있습니다. 다음 `QueueSelector` 유형은 작업 라우터에 존재 하며 분류 중에 큐 선택 프로세스에 적용할 수 있는 옵션입니다.

**QueueLabelSelector-** 작업 (Job) 라우터 큐를 만들 때 작업 분류 중에 큐 선택 프로세스에 도움이 되는 레이블을 지정할 수 있습니다. 이러한 종류의 선택기는 형식 컬렉션을 사용 하 여 `LabelSelectorAttachment` 분류 프로세스 중에 큐를 선택 하는 데 가장 많은 유연성을 제공 합니다. 이 선택기를 사용 하 여 작업 분류 프로세스가 해당 레이블을 기반으로 큐 ID를 선택할 수 있습니다. 자세한 내용은 [아래](#using-labels-and-selectors-in-classification)섹션을 참조 하세요.

**QueueIdSelector-** 이 선택기는 많은 규칙 엔진 중 하나를 사용 하 여 규칙 결과에 따라 작업의 큐 ID를 결정할 수 있습니다. 자세한 내용은 [Routerrule 개념](router-rule-concepts.md) 페이지를 참조 하세요.

### <a name="worker-selectors"></a>작업자 선택기

분류 정책의 작업자 선택기에는 `LabelSelectorAttachment` 분류 프로세스에서 해당 레이블을 기반으로 작업자 선택기를 작업에 연결 하는 데 사용 하는 형식의 컬렉션이 포함 되어 있습니다. 자세한 내용은 [아래](#using-labels-and-selectors-in-classification)섹션을 참조 하세요.

### <a name="prioritization-rule"></a>우선 순위 규칙

작업의 우선 순위는 여러 규칙 엔진 중 하나를 사용 하 여 분류 중에 해결할 수 있습니다. 가 작동 하는 방식과 비슷합니다 `QueueIdSelector` . 자세한 내용은 [Routerrule 개념](router-rule-concepts.md) 페이지를 참조 하세요.

## <a name="using-labels-and-selectors-in-classification"></a>분류에서 레이블 및 선택기 사용

작업 라우터는 작업, 작업자 및 큐의 키/값 쌍 "레이블"을 사용 하 여 라우팅에 대 한 다양 한 결정을 내립니다. 에서를 사용 하는 경우 `LabelSelectorAttachment` `QueueSelector` 필터 처럼 작동 합니다. 의 컨텍스트 내에서 사용 되는 경우 `WorkerSelectors` 작업을 사용 하 여 만든 초기 집합에 선택기를 연결 합니다. 다음 `LabelSelectorAttachment` 형식을 사용할 수 있습니다.

**정적 레이블 선택기-** 지정 된를 항상 연결 `LabelSelector` 합니다.

**조건부 레이블 선택기-** 규칙으로 정의 된 조건을 평가 합니다.  로 확인 되 면 `true` 지정 된 선택기 컬렉션이 적용 됩니다.

**통과 레이블 선택기-** 키를 사용 하 여 `LabelOperator` 키의 존재 여부를 확인 합니다. 이 선택기는의 `QueueLabelSelector` 레이블 집합을 기준으로 큐와 일치 하는 데 사용할 수 있습니다. 에서 사용 되는 경우 `WorkerSelectors` 작업의 키/값 쌍이 작업의에 연결 됩니다 `WorkerSelectors` .

**규칙 레이블 선택기-** 여러 규칙 엔진 중 하나에서 선택기 컬렉션의 원본을 만듭니다. 자세한 내용은 [Routerrule 개념](router-rule-concepts.md) 페이지를 참조 하세요.

가중치가 적용 되는 **할당 레이블 선택기-** `WeightedAllocation`각각 백분율 기반 가중치를 지정 하는 개체의 컬렉션 및 가중치 할당에 따라 적용할 선택기의 컬렉션입니다. 예를 들어 작업의 30%가 "Contoso"로 이동 하 고 작업의 70%가 "Fabrikam"으로 이동 하는 것을 원할 수 있습니다.

## <a name="reclassifying-a-job"></a>작업 다시 분류
작업을 분류 한 후에는 다음과 같은 방법으로 다시 분류할 수 있습니다.

1. 작업 레이블을 업데이트 하 여 작업 라우터가 이전 분류 정책으로 새 레이블을 평가 하도록 할 수 있습니다.
2. 작업의 분류 정책 ID를 업데이트할 수 있습니다 .이 경우 작업 라우터는 새 정책에 대해 기존 작업을 처리 합니다.
3. 예외 정책 **트리거** 는 다시 분류할 작업을 요청 하는 **작업** 을 수행할 수 있습니다. 

> [!NOTE]
> 작업 라우터 SDK는 `UpdateJobLabels` 작업 라우터를 사용 하 여 재분류 프로세스를 실행 하지 않고 단순히 레이블을 업데이트 하는 방법을 포함 합니다.
