---
title: 파이프라인 실패 및 오류 메시지
description: 파이프라인 실패 상태와 오류 메시지를 결정하는 방법을 이해합니다.
ms.service: data-factory
ms.subservice: orchestration
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: tutorial
ms.date: 11/08/2021
ms.openlocfilehash: bc9abf7319b3dc51572670f242b5d18a1a5d270d
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522330"
---
# <a name="understanding-pipeline-failure"></a>파이프라인 실패 이해

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="error-handling"></a>오류 처리

Azure Data Factory 및 Synapse 파이프라인 오케스트레이션은 조건부 논리를 허용하고, 사용자가 이전 작업의 결과에 따라 다른 경로를 사용할 수 있도록 합니다. 다른 경로를 사용하면 사용자가 강력한 파이프라인을 빌드하고 ETL/ELT 논리에서 오류 처리를 통합할 수 있습니다. 총 4개의 조건부 경로를 허용합니다.

* 성공 시(기본 패스)
* 실패 시
* 완료 시
* 건너뛰기 시

:::image type="content" source="media/tutorial-pipeline-failure-error-handling/pipeline-error-1-four-branches.png" alt-text="작업의 네 가지 분기를 보여 주는 스크린샷":::

### <a name="common-error-handling-mechanism"></a>일반적인 오류 처리 메커니즘

#### <a name="try-catch-block"></a>Try Catch 블록

이 방법에서는 고객이 비즈니스 논리를 정의하고, _실패 시_ 경로만 정의하여 이전 작업의 오류를 catch합니다. 이 방법은 _실패 시_ 경로가 성공하는 경우 파이프라인 성공을 렌더링합니다.

:::image type="content" source="media/tutorial-pipeline-failure-error-handling/pipeline-error-2-try-catch-definition.png" alt-text="try catch 블록의 정의 및 결과를 보여 주는 스크린샷":::

#### <a name="do-if-else-block"></a>Do If Else 블록

이 방법에서는 고객이 비즈니스 논리를 정의하고, _실패 시_ 및 _성공 시_ 경로를 모두 정의합니다. 이 방법은 _실패 시_ 경로가 성공하는 경우에도 파이프라인 실패를 렌더링합니다.

:::image type="content" source="media/tutorial-pipeline-failure-error-handling/pipeline-error-3-do-if-else-definition.png" alt-text="do if else 블록의 정의 및 결과를 보여 주는 스크린샷":::

#### <a name="do-if-skip-else-block"></a>Do If Skip Else 블록

이 방법에서는 고객이 비즈니스 논리를 정의하고, 더미 _건너뛰기 시_ 작업이 연결된 _실패 시_ 경로와 _성공 시_ 경로를 모두 정의합니다. 이 방법은 _실패 시_ 경로가 성공하는 경우 파이프라인 성공을 렌더링합니다.

:::image type="content" source="media/tutorial-pipeline-failure-error-handling/pipeline-error-4-do-if-skip-else-definition.png" alt-text="do if else skip else 블록의 정의 및 결과를 보여 주는 스크린샷":::

### <a name="summary-table"></a>요약 테이블

접근 방식 | 정의 | 작업이 성공하면 전체 파이프라인이 표시됩니다. | 작업이 실패하면 전체 파이프라인이 표시됩니다.
---------------------------- | ------------------- | ------------------- | -------------------
[Try-Catch](#try-catch-block) | _실패 시_ 경로만 | 성공 |  성공
[Do-If-Else](#do-if-else-block) | _실패 시_ 경로 + _성공 시_ 경로 | Success |  실패
[Do-If-Skip-Else](#do-if-skip-else-block) |  _실패 시_ 경로 + _성공 시_ 경로(끝에 _건너뛰기 시 더미_ 있음) | 성공 |  성공

### <a name="how-pipeline-failure-are-determined"></a>파이프라인 실패를 결정하는 방법

다른 오류 처리 메커니즘은 파이프라인에 대해 서로 다른 상태를 생성합니다. 즉, 일부 파이프라인은 실패하지만 다른 파이프라인은 성공합니다. 파이프라인의 성공과 실패를 다음과 같이 결정합니다.

* 모든 리프 작업에 대한 결과를 평가합니다. 리프 작업을 건너뛴 경우 부모 작업을 대신 평가합니다.
* 모든 노드에서 성공으로 평가한 경우에만 파이프라인 결과가 성공입니다.

_실패 시_ 작업 및 _더미 실패 시_ 작업 성공을 가정하는 경우 다음과 같습니다.

* [Try-Catch](#try-catch-block) 방법

  * 이전 작업이 성공하면 _실패 시_ 노드를 건너뛰고 해당 부모 노드가 성공합니다. 전체 파이프라인이 성공합니다.
  * 이전 작업이 실패하면 _실패 시_ 노드가 실행됩니다. 전체 파이프라인이 성공합니다.

* [Do-If-Else](#do-if-else-block) 방법

  * 이전 작업이 성공하면 _성공 시_ 노드가 성공하고 _실패 시_ 노드를 건너뜁니다(해당 부모 노드가 성공함). 전체 파이프라인이 성공합니다.
  * 이전 작업이 실패하면 _성공 시_ 노드를 건너뛰고 해당 부모 노드가 실패했습니다. 전체 파이프라인이 실패합니다.

* [Do-If-Skip-Else](#do-if-skip-else-block) 방법

  * 이전 작업이 성공하면 노드 _건너뛰기 시 더미_ 를 건너뛰고 해당 _성공 시_ 부모 노드가 성공하며, 다른 _실패 시_ 노드 작업을 건너뛰고 해당 부모 노드가 성공합니다. 전체 파이프라인이 성공합니다.
  * 이전 작업이 실패하면 _실패 시_ 노드가 성공하고 _건너뛰기 시 더미_ 가 성공합니다. 전체 파이프라인이 성공합니다.

## <a name="next-steps"></a>다음 단계

[Data Factory 메트릭 및 경고](monitor-metrics-alerts.md)

[시각적으로 모니터링](monitor-visually.md#alerts)
