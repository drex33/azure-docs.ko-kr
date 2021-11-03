---
title: Azure Stream Analytics 창 함수 소개
description: 이 문서에서는 Azure Stream Analytics 작업에 사용되는 네 개의 창 함수(연속, 도약, 슬라이딩, 세션)를 설명합니다.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 83c574ca578247389f8bc8c53c07847e1f01fb8b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003641"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Stream Analytics 창 함수 소개

실시간 스트리밍 시나리오에서 임시 창에 포함된 데이터에서 작업을 수행하는 것은 일반적인 패턴입니다. Stream Analytics에는 개발자가 최소한의 노력으로 복잡한 스트림 처리 작업을 작성할 수 있도록 창 함수에 대한 네이티브 지원이 있습니다.

다음과 같은 선택할 수 있는 네 종류의 임시 창이 있습니다. [**연속**](/stream-analytics-query/tumbling-window-azure-stream-analytics), [**도약**](/stream-analytics-query/hopping-window-azure-stream-analytics), [**슬라이딩**](/stream-analytics-query/sliding-window-azure-stream-analytics), [**세션**](/stream-analytics-query/session-window-azure-stream-analytics) 및 [**스냅샷**](/stream-analytics-query/snapshot-window-azure-stream-analytics) 창.  Stream Analytics 작업에서 쿼리 구문의 [**GROUP BY**](/stream-analytics-query/group-by-azure-stream-analytics) 절에서 창 함수를 사용합니다. [ **Windows()** 함수](/stream-analytics-query/windows-azure-stream-analytics)를 사용하여 여러 창에서 이벤트를 집계할 수도 있습니다.

모든 [창](/stream-analytics-query/windowing-azure-stream-analytics) 작업 결과는 창의 **끝** 에 출력됩니다. 스트림 분석 작업을 시작할 때 *작업 출력 시작 시간* 을 지정할 수 있습니다. 그러면 시스템이 지정된 시간에 첫 번째 창을 출력하는 들어오는 스트림의 이전 이벤트를 자동으로 가져옵니다. 예를 들어 *Now* 옵션으로 시작하는 경우 데이터를 즉시 내보내기 시작합니다. 창의 출력은 사용된 집계 함수를 기반으로 하는 단일 이벤트입니다. 출력 이벤트에는 창 끝의 타임스탬프가 있고 모든 창 함수는 고정된 길이로 정의됩니다. 

![Stream Analytics 창 함수 개념](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>연속 창

[**Tumbling**](/stream-analytics-query/tumbling-window-azure-stream-analytics) Window 함수는 데이터 스트림을 고유한 시간 세그먼트로 분할하고 아래 예제와 같이 해당 세그먼트에 대해 함수를 수행하는 데 사용됩니다. 연속 창의 핵심적인 차이는 반복되지만 겹치지 않고 이벤트가 둘 이상의 연속 창에 속할 수 없다는 점입니다.

![Stream Analytics 연속 창](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

다음 입력 데이터를 사용합니다(위 그림 참조).

|Stamp|CreatedAt|TimeZone|
|-|-|-|
|1|2021-10-26T10:15:01|PST|
|5|2021-10-26T10:15:03|PST|
|4|2021-10-26T10:15:06|PST|
|...|...|...|

다음 쿼리:

```SQL
SELECT System.Timestamp() as WindowEndTime, TimeZone, COUNT(*) AS Count
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY TimeZone, TumblingWindow(second,10)
```

다음을 반환합니다.

|WindowEndTime|TimeZone|개수|
|-|-|-|
|2021-10-26T10:15:10|PST|5|
|2021-10-26T10:15:20|PST|2|
|2021-10-26T10:15:30|PST|4|


## <a name="hopping-window"></a>도약 창

[**도약**](/stream-analytics-query/hopping-window-azure-stream-analytics) 창 함수는 정해진 기간까지 앞으로 홉합니다. 창 크기 보다 더 자주 겹쳐서 내보낼 수 있는 연속 창으로 생각하는 것이 더 쉬울 수 있습니다. 이벤트는 둘 이상의 도약 창 결과 집합에 속할 수 있습니다. 연속 창과 동일한 도약 창을 만들려면 도약 크기를 창 크기와 동일하도록 지정합니다. 

![Stream Analytics 도약 창](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

다음 입력 데이터를 사용합니다(위 그림 참조).

|Stamp|CreatedAt|항목|
|-|-|-|
|1|2021-10-26T10:15:01|스트리밍|
|5|2021-10-26T10:15:03|스트리밍|
|4|2021-10-26T10:15:06|스트리밍|
|...|...|...|

다음 쿼리:

```SQL
SELECT System.Timestamp() as WindowEndTime, Topic, COUNT(*) AS Count
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY Topic, HoppingWindow(second,10,5)
```

다음을 반환합니다.

|WindowEndTime|항목|개수|
|-|-|-|
|2021-10-26T10:15:10|스트리밍|5|
|2021-10-26T10:15:15|스트리밍|3|
|2021-10-26T10:15:20|스트리밍|2|
|2021-10-26T10:15:25|스트리밍|4|
|2021-10-26T10:15:30|스트리밍|4|


## <a name="sliding-window"></a>슬라이딩 윈도우

연속 또는 도약 창과 달리 [**슬라이딩**](/stream-analytics-query/sliding-window-azure-stream-analytics) 윈도우는 창의 내용이 실제로 변경될 때 시간 포인트에 대해서만 이벤트를 출력합니다. 즉, 이벤트가 창에 들어가거나 종료 될 때를 말합니다. 따라서 모든 창에는 하나 이상의 이벤트가 있습니다. 도약 창과 유사하게 이벤트는 둘 이상의 슬라이딩 윈도우에 속할 수 있습니다.

![Stream Analytics 10초 슬라이딩 윈도우](media/stream-analytics-window-functions/sliding-window-updated.png)

다음 입력 데이터를 사용합니다(위 그림 참조).

|Stamp|CreatedAt|항목|
|-|-|-|
|1|2021-10-26T10:15:10|스트리밍|
|5|2021-10-26T10:15:12|스트리밍|
|9|2021-10-26T10:15:15|스트리밍|
|7|2021-10-26T10:15:15|스트리밍|
|8|2021-10-26T10:15:27|스트리밍|

다음 쿼리:

```SQL
SELECT System.Timestamp() as WindowEndTime, Topic, COUNT(*) AS Count
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY Topic, SlidingWindow(second,10)
HAVING COUNT(*) >=3
```

다음을 반환합니다.

|WindowEndTime|항목|개수|
|-|-|-|
|2021-10-26T10:15:15|스트리밍|4|
|2021-10-26T10:15:20|스트리밍|3|

## <a name="session-window"></a>세션 창

[**세션**](/stream-analytics-query/session-window-azure-stream-analytics) 창 함수는 비슷한 시간에 도착하는 이벤트를 그룹화하여 데이터가 없는 기간을 필터링합니다. 세 가지 주요 매개 변수인 시간 제한, 최대 지속 시간 및 분할 키(선택 사항)가 있습니다.

![Stream Analytics 세션 창](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

세션 창은 첫 번째 이벤트가 발생할 때 시작합니다. 마지막으로 수집된 이벤트에서 지정된 시간 제한 내에 다른 이벤트가 발생하는 경우 창은 새 이벤트를 포함하도록 확장됩니다. 그렇지 않으면 시간 제한 내에 이벤트가 발생하지 않는 경우 창은 시간 제한에 닫힙니다.

지정된 시간 제한 내에서 이벤트가 계속 발생하는 경우 세션 창은 최대 지속 시간에 도달할 때까지 확장을 유지합니다. 최대 지속 시간 검사 간격은 지정된 최대 지속 시간과 동일한 크기로 설정됩니다. 예를 들어 최대 지속 시간이 10인 경우 창이 최대 지속 시간을 초과하는지에 대한 검사는 t = 0, 10, 20, 30 등에 발생합니다.

파티션 키가 제공되는 경우 이벤트는 키와 함께 그룹화되고 세션 창은 각 그룹에 독립적으로 적용됩니다. 이 분할은 다른 사용자 또는 디바이스에 다른 세션 창이 필요한 경우에 유용합니다.

다음 입력 데이터를 사용합니다(위 그림 참조).

|Stamp|CreatedAt|항목|
|-|-|-|
|1|2021-10-26T10:15:01|스트리밍|
|2|2021-10-26T10:15:04|스트리밍|
|3|2021-10-26T10:15:13|스트리밍|
|...|...|...|

다음 쿼리:

```SQL
SELECT System.Timestamp() as WindowEndTime, Topic, COUNT(*) AS Count
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY Topic, SessionWindow(second,5,10)
```

다음을 반환합니다.

|WindowEndTime|항목|개수|
|-|-|-|
|2021-10-26T10:15:09|스트리밍|2|
|2021-10-26T10:15:24|스트리밍|4|
|2021-10-26T10:15:31|스트리밍|2|
|2021-10-26T10:15:39|스트리밍|1|

## <a name="snapshot-window"></a>스냅숏 창

타임스탬프가 동일한 [**스냅샷**](/stream-analytics-query/snapshot-window-azure-stream-analytics) 창 그룹 이벤트입니다. 특정 창 함수(예: [Sessionwindow()](/stream-analytics-query/session-window-azure-stream-analytics)등을 요구하는 창 유형과는 달리, 시스템을 추가하여 스냅샷 창에 적용할 수 있음. Timestamp()를 절 별로 그룹에 적용할 수 있음.

![스트림 분석 스냅샷 윈도우](media/stream-analytics-window-functions/stream-analytics-window-functions-snapshot-intro.png)

다음 입력 데이터를 사용합니다(위 그림 참조).

|Stamp|CreatedAt|항목|
|-|-|-|
|1|2021-10-26T10:15:04|스트리밍|
|2|2021-10-26T10:15:04|스트리밍|
|3|2021-10-26T10:15:04|스트리밍|
|...|...|...|

다음 쿼리:

```SQL
SELECT System.Timestamp() as WindowEndTime, Topic, COUNT(*) AS Count
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY Topic, System.Timestamp()
```

다음을 반환합니다.

|WindowEndTime|항목|개수|
|-|-|-|
|2021-10-26T10:15:04|스트리밍|4|
|2021-10-26T10:15:10|스트리밍|2|
|2021-10-26T10:15:13|스트리밍|1|
|2021-10-26T10:15:22|스트리밍|2|

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics 관리 REST API 참조](/rest/api/streamanalytics/)
