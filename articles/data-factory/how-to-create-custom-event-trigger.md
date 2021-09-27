---
title: Azure Data Factory에서 사용자 지정 이벤트 트리거 만들기
description: Azure Data Factory에서 Event Grid에 게시된 사용자 지정 이벤트에 응답하여 파이프라인을 실행하는 트리거를 만드는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: orchestration
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: dcad59c6434f6751cba8633868a9dc9969ffc0ac
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610544"
---
# <a name="create-a-custom-event-trigger-to-run-a-pipeline-in-azure-data-factory"></a>Azure Data Factory에서 파이프라인을 실행하는 사용자 지정 이벤트 트리거 만들기

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

EDA(이벤트 기반 아키텍처)는 프로덕션, 검색, 소비 및 이벤트에 대한 응답이 포함된 일반적인 데이터 통합 패턴입니다. 데이터 통합 시나리오에서는 Azure Data Factory 고객이 특정 이벤트가 발생할 때 파이프라인을 트리거해야 하는 경우가 많습니다. [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)와 Data Factory의 기본 통합은 이제 [사용자 지정 토픽](../event-grid/custom-topics.md)을 다룹니다. 이벤트를 Event Grid 토픽에 보냅니다. Data Factory는 토픽을 구독하고, 수신 대기한 다음, 그에 따라 파이프라인을 트리거합니다.

> [!NOTE]
> 이 문서에서 설명하는 통합은 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)에 따라 달라집니다. 구독이 Event Grid 리소스 공급자에 등록되어 있는지 확인합니다. 자세한 내용은 [리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)을 참조하세요. `Microsoft.EventGrid/eventSubscriptions/` 작업을 수행할 수 있어야 합니다. 이 작업은 [EventGrid EventSubscription 기여자](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-contributor) 기본 제공 역할의 일부입니다.

파이프라인 매개 변수와 사용자 지정 이벤트 트리거를 결합하면 파이프라인 실행에서 사용자 지정 `data` 페이로드를 구문 분석하고 참조할 수 있습니다. 사용자 지정 이벤트 페이로드의 `data` 필드는 자유 형식의 JSON 키-값 구조이므로 이벤트 기반 파이프라인 실행을 제어할 수 있습니다.

> [!IMPORTANT]
> 매개 변수화에서 참조된 키가 사용자 지정 이벤트 페이로드에 없으면 `trigger run`이 실패합니다. `keyName` 속성이 없으므로 식을 평가할 수 없다는 오류가 발생합니다. 이 경우 이벤트에서 `pipeline run`이 트리거되지 **않습니다**.

## <a name="set-up-a-custom-topic-in-event-grid"></a>Event Grid에서 사용자 지정 토픽 설정

Data Factory에서 사용자 지정 이벤트 트리거를 사용하려면 *먼저* [Event Grid에서 사용자 지정 토픽을 설정](../event-grid/custom-topics.md)해야 합니다.

Azure Event Grid로 이동하여 토픽을 직접 만듭니다. 사용자 지정 토픽을 만드는 방법에 대한 자세한 내용은 Azure Event Grid [포털 자습서](../event-grid/custom-topics.md#azure-portal-tutorials) 및 [CLI 자습서](../event-grid/custom-topics.md#azure-cli-tutorials)를 참조하세요.

> [!NOTE]
> 워크플로는 스토리지 이벤트 트리거와 다릅니다. 여기서 Data Factory는 토픽을 설정하지 않습니다.

Data Factory는 이벤트에서 [Event Grid 이벤트 스키마](../event-grid/event-schema.md)를 따른다고 예상합니다. 다음 필드가 이벤트 페이로드에 있어야 합니다.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

## <a name="use-data-factory-to-create-a-custom-event-trigger"></a>Data Factory를 사용하여 사용자 지정 이벤트 트리거 만들기

1. Azure Data Factory로 이동하여 로그인합니다.

1. **편집** 탭으로 전환합니다. 연필 아이콘을 찾습니다.

1. 메뉴에서 **트리거** 를 선택한 다음, **새로 만들기/편집** 을 선택합니다.

1. **트리거 추가** 페이지에서 **트리거 선택**, **새로 만들기** 를 차례로 선택합니다.

1. **유형** 에 대해 **사용자 지정 이벤트** 를 선택합니다.

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-1-creation.png" alt-text="Data Factory UI에 새 사용자 지정 이벤트 트리거를 만드는 작성자 페이지의 스크린샷." lightbox="media/how-to-create-custom-event-trigger/custom-event-1-creation-expanded.png":::

1. Azure 구독 드롭다운에서 사용자 지정 토픽을 선택하거나 이벤트 토픽 범위를 수동으로 입력합니다.

   > [!NOTE]
   > Data Factory에서 사용자 지정 이벤트 트리거를 만들거나 수정하려면 적절한 역할 기반 액세스 제어(Azure RBAC)가 있는 Azure 계정을 사용해야 합니다. 추가 권한이 필요하지 않습니다. Data Factory 서비스 원칙에는 Event Grid에 대한 특별한 권한이 필요하지 *않습니다*. 액세스 제어에 대한 자세한 내용은 [역할 기반 액세스 제어](#role-based-access-control)를 참조하세요.

1. **제목 시작 문자** 및 **제목 종료 문자** 속성을 사용하면 트리거 이벤트를 필터링할 수 있습니다. 두 속성 모두 선택 사항입니다.

1. **+새로 만들기** 를 사용하여 필터링할 **이벤트 유형** 을 추가합니다. 사용자 지정 이벤트 트리거 목록에서는 OR 관계를 사용합니다. 목록에 있는 것과 일치하는 `eventType` 속성이 있는 사용자 지정 이벤트가 발생하면 파이프라인 실행이 트리거됩니다. 이벤트 형식은 대/소문자를 구분하지 않습니다. 예를 들어 다음 스크린샷에서 트리거는 *factories* 로 시작하는 제목이 있는 모든 `copycompleted` 또는 `copysucceeded` 이벤트와 일치합니다.

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-2-properties.png" alt-text="Data Factory UI에서 이벤트 유형 및 제목 필터링을 설명하는 트리거 편집 페이지의 스크린샷.":::

1. 사용자 지정 이벤트 트리거는 사용자 지정 `data` 페이로드를 구문 분석하고 파이프라인에 보낼 수 있습니다. 파이프라인 매개 변수를 만든 다음, **매개 변수** 페이지에서 값을 입력합니다. `@triggerBody().event.data._keyName_` 형식을 사용하여 데이터 페이로드를 구문 분석하고, 값을 파이프라인 매개변수에 전달합니다.
 
   자세한 설명은 다음 문서를 참조하세요.
   - [파이프라인의 참조 트리거 메타데이터](how-to-use-trigger-parameterization.md)
   - [사용자 지정 이벤트 트리거의 시스템 변수](control-flow-system-variables.md#custom-event-trigger-scope)

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-4-trigger-values.png" alt-text="파이프라인 매개 변수 설정의 스크린샷":::

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-3-parameters.png" alt-text="사용자 지정 이벤트에서 데이터 페이로드를 참조하는 매개 변수 페이지의 스크린샷":::

1. 매개 변수가 입력되면 **확인** 을 선택합니다.

## <a name="advanced-filtering"></a>고급 필터링

사용자 지정 이벤트 트리거는 [Event Grid 고급 필터링](../event-grid/event-filtering.md#advanced-filtering)과 유사한 고급 필터링 기능을 지원합니다. 이러한 조건부 필터를 사용하면 이벤트 페이로드의 _값_ 을 기반으로 파이프라인을 트리거할 수 있습니다. 예를 들어 부서 라는 이벤트 페이로드의 필드가 있을 수 있으며,이 경우에는 _부서가_ _재무_ 와 동일한 경우에만 _파이프라인을 트리거합니다_. 목록 [1, 2, 3, 4, 5]의 _date_ 필드, 목록 [11 , 12]가 __아닌__ _month_ 필드, ['Fiscal Year 2021', 'FiscalYear2021', 'FY2021'] 중 하나를 포함하는 _tag_ 필드와 같은 복잡한 논리를 지정할 수도 있습니다.

 :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-5-advanced-filters.png" alt-text="고객 이벤트 트리거를 위한 고급 필터 설정 스크린샷.":::

현재 사용자 지정 이벤트 트리거는 Event Grid에서 [고급 필터링 연산자](../event-grid/event-filtering.md#advanced-filtering)의 __하위 집합__ 을 지원합니다. 다음 필터 조건이 지원됩니다.

* NumberIn
* NumberNotIn
* NumberLessThan
* NumberGreaterThan
* NumberLessThanOrEquals
* NumberGreaterThanOrEquals
* BoolEquals
* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

**+새로 만들기** 를 클릭하여 새 필터 조건을 추가합니다. 

또한 사용자 지정 이벤트 트리거는 다음을 포함하여 [Event Grid와 동일한 제한](../event-grid/event-filtering.md#limitations)을 따릅니다.

* 사용자 지정 이벤트 트리거당 모든 필터에서 5개의 고급 필터 및 25개의 필터 값
* 문자열 값당 512자
* in 및 not in 연산자에 대한 5개의 값
* 키에서는 `.`(점) 문자를 사용할 수 없습니다(예: `john.doe@contoso.com`). 현재까지는 키에서 이스케이프 문자가 지원되지 않습니다.
* 둘 이상의 필터에 동일한 키를 사용할 수 있습니다.

Data Factory는 최신 _GA_ 버전의 [Event Grid API](../event-grid/whats-new.md)를 사용합니다. 새 API 버전이 GA 단계가 되면 Data Factory는 추가 고급 필터링 연산자를 위해 지원을 확장합니다.

## <a name="json-schema"></a>JSON 스키마

다음 표에는 사용자 지정 이벤트 트리거와 관련된 스키마 요소의 개요가 나와 있습니다.

| JSON 요소 | Description | Type | 허용되는 값 | 필수 |
|---|----------------------------|---|---|---|
| `scope` | Event Grid 토픽의 Azure Resource Manager 리소스 ID입니다. | String | Azure Resource Manager ID | 예 |
| `events` | 이 트리거를 발생시키는 이벤트 유형입니다. | 문자열 배열    |  | 예, 하나 이상의 값이 필요합니다. |
| `subjectBeginsWith` | `subject` 필드는 트리거를 발생시키는 제공된 패턴으로 시작해야 합니다. 예를 들어 _factories_ 는 *factories* 로 시작하는 이벤트 주체에 대해서만 트리거를 발생시킵니다. | String   | | 예 |
| `subjectEndsWith` | `subject` 필드는 트리거를 발생시키는 제공된 패턴으로 끝나야 합니다. | String   | | No |
| `advancedFilters` | 각각 필터 조건을 지정하는 JSON Blob 목록입니다. 각 Blob은 `key`, `operatorType` 및 `values`를 지정합니다. | JSON Blob 목록 | | 예 |

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

Azure Data Factory는 Azure RBAC(역할 기반 액세스 제어)를 사용하여 무단 액세스를 금지합니다. 제대로 작동하려면 Data Factory에 다음과 같은 액세스 권한이 필요합니다.
- 이벤트를 수신 대기합니다.
- 이벤트의 업데이트를 구독합니다.
- 사용자 지정 이벤트에 연결된 파이프라인을 트리거합니다.

사용자 지정 이벤트 트리거를 성공적으로 만들거나 업데이트하려면 적절한 액세스 권한이 있는 Azure 계정으로 Data Factory에 로그인해야 합니다. 그렇지 않으면 _액세스 거부_ 오류로 인해 작업이 실패합니다.

Data Factory에는 Event Grid에 대한 특별한 권한이 필요하지 않습니다. 또한 특별한 Azure RBAC 권한을 작업에 대한 Data Factory 서비스 주체에 할당할 필요가 *없습니다*.

특히 `/subscriptions/####/resourceGroups//####/providers/Microsoft.EventGrid/topics/someTopics`에 대한 `Microsoft.EventGrid/EventSubscriptions/Write` 권한이 필요합니다.

## <a name="next-steps"></a>다음 단계

* 트리거 실행에 대한 자세한 정보를 [얻습니다](concepts-pipeline-execution-triggers.md#trigger-execution).
* [파이프라인 실행에서 트리거 메타데이터를 참조](how-to-use-trigger-parameterization.md)하는 방법을 알아봅니다.
