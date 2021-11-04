---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: aceaeb74b89a8c0eaa30728a2c456b367d64cf94
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482930"
---
### <a name="schema-mapping-examples"></a>스키마 매핑 예제

**테이블 스키마 매핑 예제**

데이터에 세 개의 열(`Timespan`, `Metric`, `Value`)이 포함되고 포함된 속성이 `x-opt-enqueued-time`와 `x-opt-offset`인 경우 다음 명령을 사용하여 테이블 스키마를 만들거나 변경합니다.

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**CSV 매핑 예제**

다음 명령을 실행하여 레코드의 시작 부분에 데이터를 추가합니다. 서수 값을 기록해 둡니다.

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
**JSON 매핑 예제**

시스템 속성 매핑을 사용하여 데이터를 추가합니다. 다음 명령을 실행하세요.

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```
