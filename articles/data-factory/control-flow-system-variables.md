---
title: 시스템 변수
titleSuffix: Azure Data Factory & Azure Synapse
description: 이 문서에서는 Azure Data Factory 및 Azure Synapse Analytics에서 지원하는 시스템 변수에 대해 설명합니다. 두 서비스 내에서 엔터티를 정의할 때 식에서 이러한 변수를 사용할 수 있습니다.
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: 8388734349f64c9e444f0e6d180cd33f93cf2e7a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642558"
---
# <a name="system-variables-supported-by-azure-data-factory-and-azure-synapse-analytics"></a>Azure Data Factory 및 Azure Synapse Analytics에서 지원하는 시스템 변수

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse에서 지원하는 시스템 변수에 대해 설명합니다. 두 서비스 내에서 엔터티를 정의할 때 식에서 이러한 변수를 사용할 수 있습니다.

## <a name="pipeline-scope"></a>파이프라인 범위

이러한 시스템 변수는 파이프라인 JSON의 어디에서나 참조할 수 있습니다.

| 변수 이름 | 설명 |
| --- | --- |
| @pipeline().DataFactory |파이프라인 실행이 진행 중인 데이터 또는 Synapse 작업 영역의 이름 |
| @pipeline().Pipeline |파이프라인 이름입니다. |
| @pipeline().RunId |특정 파이프라인 실행의 ID |
| @pipeline().TriggerType |파이프라인을 호출한 트리거의 유형입니다(예: `ScheduleTrigger`, `BlobEventsTrigger`). 지원되는 트리거 유형 목록은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md)를 참조하세요. `Manual` 트리거 유형은 파이프라인이 수동으로 트리거되었음을 나타냅니다. |
| @pipeline().TriggerId|파이프라인을 호출한 트리거 ID |
| @pipeline().TriggerName|파이프라인을 호출한 트리거 이름 |
| @pipeline().TriggerTime|파이프라인을 호출한 트리거 실행 시간. 이는 트리거가 파이프라인 실행을 **실제로** 실행한 시간이며, 트리거가 예약된 시간과는 약간 다를 수 있습니다.  |
| @pipeline().GroupId | 파이프라인 실행이 속한 그룹의 ID입니다. |
| @pipeline()?TriggeredByPipelineName | 파이프라인 실행을 트리거하는 파이프라인의 이름입니다. ExecutePipeline 활동에 의해 파이프라인 실행이 트리거될 때 적용됩니다. 다른 상황에서 사용하는 경우 _Null_ 로 평가합니다. @pipeline() 뒤에 물음표가 있습니다. |
| @pipeline()?TriggeredByPipelineRunId | 파이프라인 실행을 트리거하는 파이프라인의 실행 ID입니다. ExecutePipeline 활동에 의해 파이프라인 실행이 트리거될 때 적용됩니다. 다른 상황에서 사용하는 경우 _Null_ 로 평가합니다. @pipeline() 뒤에 물음표가 있습니다. |

>[!NOTE]
>트리거 관련 날짜/시간 시스템 변수(파이프라인 및 트리거 범위 모두에서)는 ISO 8601 형식으로 UTC 날짜를 반환합니다(예: `2017-06-01T22:20:00.4061448Z`).

## <a name="schedule-trigger-scope"></a>일정 트리거 범위

이 시스템 변수는 트리거가 [ScheduleTrigger](concepts-pipeline-execution-triggers.md#schedule-trigger) 유형인 경우, 트리거 JSON의 어디에서나 참조할 수 있습니다.

| 변수 이름 | 설명 |
| --- | --- |
| @trigger().scheduledTime |트리거가 파이프라인 실행을 호출하도록 예약된 시간입니다. |
| @trigger().startTime |트리거가 파이프라인 실행을 **실제로** 실행한 시간입니다. 트리거가 예약된 시간과는 약간 다를 수 있습니다. |

## <a name="tumbling-window-trigger-scope"></a>연속 창 트리거 범위

이 시스템 변수는 트리거가 [TumblingWindowTrigger](concepts-pipeline-execution-triggers.md#tumbling-window-trigger) 유형인 경우, 트리거 JSON의 어디에서나 참조할 수 있습니다.

| 변수 이름 | 설명 |
| --- | --- |
| @trigger().outputs.windowStartTime |트리거 실행과 관련된 기간의 시작입니다. |
| @trigger().outputs.windowEndTime |트리거 실행과 관련된 기간의 끝입니다. |
| @trigger().scheduledTime |트리거가 파이프라인 실행을 호출하도록 예약된 시간입니다. |
| @trigger().startTime |트리거가 파이프라인 실행을 **실제로** 실행한 시간입니다. 트리거가 예약된 시간과는 약간 다를 수 있습니다. |

## <a name="storage-event-trigger-scope"></a>스토리지 이벤트 트리거 범위

이 시스템 변수는 트리거가 [BlobEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger) 유형인 경우, 트리거 JSON의 어디에서나 참조할 수 있습니다.

| 변수 이름 | 설명 |
| --- | --- |
| @triggerBody().fileName  |생성 또는 삭제로 인해 트리거가 실행된 파일의 이름입니다.   |
| @triggerBody().folderPath  |`@triggerBody().fileName`로 지정된 파일을 포함하는 폴더의 경로입니다. 폴더 경로의 첫 번째 세그먼트는 Azure Blob Storage 컨테이너의 이름입니다.  |
| @trigger().startTime |트리거가 파이프라인 실행을 실행한 시간입니다. |

## <a name="custom-event-trigger-scope"></a>사용자 지정 이벤트 트리거 범위

이 시스템 변수는 트리거가 [CustomEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger) 유형인 경우, 트리거 JSON의 어디에서나 참조할 수 있습니다.

>[!NOTE]
>이 서비스는 사용자 지정 이벤트가 [Azure Event Grid 이벤트 스키마](../event-grid/event-schema.md)를 사용하여 형식이 지정되어 있을 것으로 예상합니다.

| 변수 이름 | 설명
| --- | --- |
| @triggerBody().event.eventType | 사용자 지정 이벤트 트리거 실행을 트리거한 이벤트의 유형입니다. 이벤트 유형은 고객이 정의한 필드이며 문자열 유형의 값을 사용합니다. |
| @triggerBody().event.subject | 트리거가 실행되도록 한 사용자 지정 이벤트의 제목입니다. |
| @triggerBody().event.data._keyName_ | 사용자 지정 이벤트의 데이터 필드는 고객이 메시지와 데이터를 보내기 위해 사용하는 JSON Bob에서 무료로 제공됩니다. data._keyName_ 을 사용하여 각 필드를 참조하세요. 예를 들어 @triggerBody().event.data.callback은 _data_ 에 저장된 _callback_ 필드의 값을 반환합니다. |
| @trigger().startTime | 트리거가 파이프라인 실행을 실행한 시간입니다. |

## <a name="next-steps"></a>다음 단계

* 이러한 변수가 식에서 사용되는 방법은 [식 언어 및 함수](control-flow-expression-language-functions.md)를 참조하세요.
* 파이프라인에서 트리거 범위 시스템 변수를 사용하려면 [파이프라인의 참조 트리거 메타데이터](how-to-use-trigger-parameterization.md)를 참조하세요.
