---
title: 되풀이 작업 및 워크플로 예약
description: Azure Logic Apps의 되풀이 트리거를 사용하여 되풀이하는 자동화된 작업 및 워크플로를 예약 및 실행
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 6ade5e44e7b43e30447a31dcc138a2514771fe21
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443680"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Azure Logic Apps의 되풀이 트리거를 사용하여 반복적인 작업 및 워크플로를 생성, 예약 및 실행

특정 일정에 따라 태스크, 프로세스 또는 작업을 정기적으로 실행하려면 기본적으로 Azure Logic Apps에서 실행되는 기본 제공 **되풀이** 트리거를 사용하여 논리 앱 워크플로를 시작할 수 있습니다. 워크플로를 시작하기 위한 날짜 및 시간(표준 시간대 포함)과 해당 워크플로를 반복하기 위한 되풀이를 설정할 수 있습니다. 예를 들어 중단 또는 비활성화된 워크플로 등의 이유로 트리거가 되풀이를 누락하는 경우 이 트리거는 누락된 되풀이를 처리하지 않고 예약된 다음 간격에 되풀이를 다시 시작합니다. 기본 제공 일정 트리거 및 작업에 대한 자세한 내용은 [Azure Logic Apps를 사용하여 되풀이하는 자동화된 작업 및 워크플로 예약 및 실행](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)을 참조하세요.

다음은 이 트리거가 고급 되풀이 및 복잡한 일정에서 지원하는 몇 가지 패턴입니다.

* 즉시 실행하고 *n* 초, 분, 시간, 일, 주 또는 월마다 반복합니다.

* 특정 날짜 및 시간에 시작한 다음 *n* 초, 분, 시간, 일, 주 또는 월마다 반복합니다.

* 예를 들어 8:00 AM 및 5:00 PM과 같이 매일 하나 이상의 시간에 실행하고 반복합니다.

* 매주 실행하고 반복하되, 토요일 및 일요일과 같이 특정 요일에만 적용합니다.

* 매주 실행하고 반복하되, 월요일부터 금요일까지 8:00 AM 및 5:00 PM과 같이 특정 날짜 및 시간에만 적용합니다.

이 트리거와 슬라이딩 윈도우 트리거 간의 차이점 또는 되풀이 워크플로를 예약하는 방법에 대한 자세한 내용은 [Azure Logic Apps를 사용하여 되풀이하는 자동화된 작업, 프로세스 및 워크플로 예약 및 실행](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)을 참조하세요.

> [!TIP]
> 논리 앱을 트리거하고 나중에 한 번만 실행하려면 [한 번만 작업 실행](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다.

* [논리 앱](../logic-apps/logic-apps-overview.md)에 대한 기본 지식 논리 앱을 처음 사용하는 경우 [첫 번째 논리 앱을 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)을 알아보세요.

## <a name="add-the-recurrence-trigger"></a>되풀이 트리거 추가

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 빈 논리 앱을 만듭니다.

1. 논리 앱 디자이너가 표시되면 검색 상자에 `recurrence`를 필터로 입력합니다. 트리거 목록에서 다음 트리거를 논리 앱 워크플로의 첫 번째 단계로 선택합니다. **되풀이**

   !['되풀이' 트리거 선택](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. 되풀이에 대한 간격 및 빈도를 설정합니다. 이 예제에서는 매주 워크플로를 실행하도록 이러한 속성을 설정합니다.

   ![간격 및 빈도 설정](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | 속성 | JSON 이름 | 필수 | Type | 설명 |
   |----------|-----------|----------|------|-------------|
   | **간격** | `interval` | 예 | 정수 | 빈도에 따라 워크플로가 얼마나 자주 실행되는지를 설명하는 양의 정수입니다. 다음은 최소 및 최대 간격입니다. <p>- 월: 1-16개월 <br>- 주: 1~71주 <br>- 일: 1-500일 <br>- 시간: 1-12,000시간 <br>- 분: 1-72,000분 <br>- 초: 1-9,999,999초<p>예를 들어 간격이 6이고 빈도가 “월”이면 되풀이 간격은 6개월마다입니다. |
   | **빈도** | `frequency` | 예 | String | 되풀이 시간 단위: **초**, **분**, **시간**, **일**, **주** 또는 **월** |
   ||||||

   > [!IMPORTANT]
   > 되풀이에서 특정 [시작 날짜 및 시간](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)을 지정하지 않는 경우에는 트리거의 되풀이 설정에도 불구하고 논리 앱을 저장하거나 배포할 때 첫 번째 되풀이가 즉시 실행됩니다. 이 동작을 방지하려면 첫 번째 되풀이를 실행할 시작 날짜와 시간을 제공합니다.
   >
   > 되풀이에서 이후 되풀이를 실행하는 특정 시간과 같은 기타 고급 일정 옵션을 지정하지 않는 경우 해당 되풀이는 마지막 실행 시간을 기준으로 합니다. 결과적으로, 해당 되풀이에 대한 시작 시간은 스토리지 호출 중의 대기 시간과 같은 요소로 인해 드리프트될 수 있습니다. 
   > 논리 앱이 되풀이를 누락하지 않도록 하려면, 특히 빈도가 며칠 이상이면 다음 옵션을 시도해 보세요.
   > 
   > * **일** 및 **주** 빈도에 대해서만 사용할 수 있는 **해당 시간에 대하여** 와 **해당 분에 대하여** 로 명명된 속성을 사용하여 되풀이의 시작 날짜와 시간 및 후속 되풀이를 실행할 특정 시간을 지정합니다.
   > 
   > * 반복 트리거가 아니라 [슬라이딩 윈도우 트리거](../connectors/connectors-native-sliding-window.md)를 사용합니다.

1. 고급 일정 옵션을 설정하려면 **새 매개 변수 추가** 목록을 엽니다. 선택하는 옵션은 선택 후 트리거에 표시됩니다.

   ![고급 일정 옵션](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | 속성 | JSON 이름 | 필수 | Type | Description |
   |----------|-----------|----------|------|-------------|
   | **표준 시간대** | `timeZone` | 예 | String | 이 트리거는 [UTC 오프셋](https://en.wikipedia.org/wiki/UTC_offset)을 허용하지 않으므로 시작 시간을 지정할 때만 적용됩니다. 적용하려는 표준 시간대를 선택합니다. |
   | **시작 시간** | `startTime` | 예 | String | 시작 날짜 및 시간을 제공합니다. 이 날짜 및 시간은 이후 최대 49년이며 [UTC 오프셋](https://en.wikipedia.org/wiki/UTC_offset) 없이 [UTC 날짜/시간 형식](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)으로 [ISO 8601 날짜/시간 사양](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)을 따라야 합니다. <p><p>표준 시간대를 선택하는 경우 YYYY-MM-DDThh:mm:ss <p>또는 <p>표준 시간대를 선택하지 않은 경우 YYYY-MM-DDThh:mm:ssZ <p>예를 들어 2020년 9월 18일, 오후 2시를 원할 경우 '2020-09-18T14:00:00'을 지정하고 태평양 표준시와 같은 표준 시간대를 지정합니다. 또는 표준 시간대 없이 '2020-09-18T14:00:00Z'를 지정합니다. <p><p>**중요**: 표준 시간대를 선택하지 않으면 공백 없이 맨 끝에 문자 'Z'를 추가해야 합니다. 이 “Z”는 해당 [항해 시간](https://en.wikipedia.org/wiki/Nautical_time)을 나타냅니다. 표준 시간대 값을 선택하는 경우 **시작 시간** 값의 끝에 'Z'를 추가할 필요가 없습니다. 이렇게 하면 'Z'는 UTC 시간 형식을 의미하므로 Logic Apps는 표준 시간대 값을 무시합니다. <p><p>단순 일정의 경우 시작 시간은 첫 번째 발생이지만 복잡한 일정의 경우 트리거는 시작 시간보다 더 일찍 발생하지 않습니다. [*시작 날짜 및 시간을 사용할 수 있는 방법에는 무엇이 있나요?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **요일 선택** | `weekDays` | 예 | 문자열 또는 문자열 배열 | “주”를 선택하는 경우 워크플로를 실행하려는 요일을 하나 이상 선택할 수 있습니다.**월요일**, **화요일**, **수요일**, **목요일**, **금요일**, **토요일** 및 **일요일** |
   | **시간 선택** | `hours` | 예 | 정수 또는 정수 배열 | '일' 또는 '주'를 선택하는 경우 워크플로를 실행하려는 일중 시간으로 0~23 사이의 정수를 하나 이상 선택할 수 있습니다. <p><p>예를 들어 '10', '12' 및 '14'를 지정하면 일중 오전 10시, 오후 12시 및 오후 2시가 되고, 일중 분은 되풀이 시작 시간을 기준으로 계산됩니다. 특정 일중 분(예: 오전 10:00, 오후 12:00 및 오후 2:00)을 설정하려면 **분 선택** 이라는 속성을 사용하여 해당 값을 지정합니다. |
   | **분 선택** | `minutes` | 예 | 정수 또는 정수 배열 | “일” 또는 “주”를 선택하는 경우 워크플로를 실행하려는 시간의 분으로 0~59 사이의 정수를 하나 이상 선택할 수 있습니다. <p>예를 들어 분 표시로 “30”을 지정하고, 앞에 나온 하루 시간 예제를 사용하면 10:30 AM, 12:30 PM 및 2:30 PM이 표시됩니다. <p>**참고**: 트리거된 실행에 대한 타임스탬프는 예약된 시간에서 최대 1분까지 차이가 날 수도 있습니다. 후속 작업에 예약된 대로 정확하게 타임스탬프를 전달해야 하는 경우 템플릿 식을 사용하여 타임스탬프를 적절하게 변경할 수 있습니다. 자세한 내용은 [식의 날짜 및 시간 함수](../logic-apps/workflow-definition-language-functions-reference.md#date-time-functions)를 참조하세요. |
   |||||

   예를 들어 오늘이 2020년 9월 4일 금요일이라고 가정합니다. 다음 되풀이 트리거는 2020년 9월 18일 금요일 오전 8:00 PST인 시작 날짜 및 시간보다 일찍 발생하지 않습니다. 반면 되풀이 일정이 월요일에 한해서만 10:30 AM, 12:30 PM 및 2:30 PM으로 설정되어 있습니다. 따라서 트리거가 발생하고 논리 앱 워크플로 인스턴스가 만들어지는 처음 시간은 월요일 오전 10:30이 됩니다. 시작 시간이 작동하는 방법에 대한 자세한 내용은 [시작 시간 예](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)를 참조하세요.

   이후 실행은 같은 날 12:30 PM 및 2:30 PM에 발생합니다. 되풀이마다 해당 워크플로 인스턴스가 만들어집니다. 그런 다음 전체 일정이 다시 다음 월요일에 반복됩니다. [*다른 예제에는 어떤 것이 있나요?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![고급 일정 예제](./media/connectors-native-recurrence/recurrence-trigger-advanced-schedule-options.png)

   > [!NOTE]
   > 트리거는 주기로 “일” 또는 “주”를 선택한 경우에만 지정된 되풀이에 대한 미리 보기를 보여 줍니다.

1. 이제 다른 작업을 사용하여 나머지 워크플로를 빌드합니다. 추가할 수 있는 다른 작업은 [Azure Logic Apps 커넥터](../connectors/apis-list.md)를 참조하세요.

## <a name="workflow-definition---recurrence"></a>워크플로 정의 - 되풀이

JSON을 사용하는 논리 앱의 기본 워크플로 정의에서 선택하는 옵션을 사용하여 [되풀이 트리거 정의](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)를 볼 수 있습니다. 이 정의를 보려면 디자이너 도구 모음에서 **코드 보기** 를 선택합니다. 디자이너로 돌아가려면 디자이너 도구 모음에서 **디자이너** 를 선택합니다.

다음 예제에서는 되풀이 트리거 정의가 기본 워크플로 정의에서 어떻게 표시되는지 보여 줍니다.

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2020-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

다음 예에서는 트리거가 각 월의 마지막 날에 한 번만 실행 되도록 트리거 정의를 업데이트하는 방법을 보여줍니다.

```json
"triggers": {
    "Recurrence": {
        "recurrence": {
            "frequency": "Month",
            "interval": 1,
            "schedule": {
                "monthDays": [-1]
            }
        },
        "type": "Recurrence"
    }
}
```

<a name="daylight-saving-standard-time"></a>

## <a name="trigger-recurrence-shift-between-daylight-saving-time-and-standard-time"></a>일광 절약 시간과 표준 시간 간 되풀이 전환 트리거

되풀이하는 기본 제공 트리거는 사용자가 지정하는 표준 시간대를 포함하여 사용자가 설정한 일정을 적용합니다. 표준 시간대를 선택하지 않으면 트리거가 실행될 때 DST(일광 절약 시간)에 영향을 줄 수 있습니다. 예를 들어 DST가 시작되면 시작 시간을 1시간 앞으로 이동하고, DST가 종료되면 1시간 뒤로 이동합니다.

논리 앱이 지정된 시작 시간에 실행되도록 이 전환 동작을 방지하려면 표준 시간대를 선택해야 합니다. 이렇게 하면 논리 앱에 대한 UTC 시간도 계절적 시간 변경에 대응하도록 전환됩니다. 그러나 시간이 이동하면 일부 기간에서 문제가 발생할 수 있습니다. 자세한 내용 및 예제는 [일광 절약 시간 및 표준 시간에 대한 되풀이](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [지연 작업을 사용하여 워크플로 일시 중지](../connectors/connectors-native-delay.md)
* [Logic Apps용 커넥터](../connectors/apis-list.md)
