---
title: Azure Data Factory의 데이터 랭글링 함수
description: Azure Data Factory에서 사용 가능한 데이터 랭글링 함수에 대한 개요
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 2af1e7f9e1b787e73247d9537b4a8876cc4f7220
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361236"
---
# <a name="transformation-functions-in-power-query-for-data-wrangling"></a>데이터 랭글링에 대한 파워 쿼리의 변환 함수

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory의 데이터 랭글링를 사용하면 파워 쿼리 ```M``` 스크립트를 데이터 흐름 스크립트로 변환하여 클라우드 스케일에서 코드 없는 신속한 데이터 준비 및 랭글링을 수행할 수 있습니다. ADF는 [파워 쿼리 온라인](/powerquery-m/power-query-m-reference)과 통합되며 파워 쿼리 ```M``` 함수를 데이터 흐름 Spark 인프라를 사용하여 Spark 실행을 통해 데이터 랭글링에 사용할 수 있도록 합니다. 

현재 모든 파워 쿼리 M 함수는 제작 중에 사용할 수 있지만 데이터 랭글링에 지원되지 않습니다. 매시업을 빌드하는 동안 함수가 지원되지 않으면 다음과 같은 오류 메시지가 표시됩니다.

`UserQuery : Expression.Error: The transformation logic is not supported as it requires dynamic access to rows of data, which cannot be scaled out.`

다음은 지원되는 파워 쿼리 M 함수 목록입니다.

## <a name="column-management"></a>열 관리

* 선택: [Table.SelectColumns](/powerquery-m/table-selectcolumns)
* 제거: [Table.RemoveColumns](/powerquery-m/table-removecolumns)
* 이름 바꾸기: [Table.RenameColumns](/powerquery-m/table-renamecolumns), [Table.PrefixColumns](/powerquery-m/table-prefixcolumns), [Table.TransformColumnNames](/powerquery-m/table-transformcolumnnames)
* 다시 정렬: [Table.ReorderColumns](/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>행 필터링

M 함수 [Table.SelectRows](/powerquery-m/table-selectrows)를 사용하여 다음 조건에 따라 필터링합니다.

* 같음 및 같지 않음
* 숫자, 텍스트 및 날짜 비교(DateTime은 제외)
* 숫자 정보(예: [Number.IsEven](/powerquery-m/number-iseven)/[Odd](/powerquery-m/number-iseven))
* [Text.Contains](/powerquery-m/text-contains), [Text.StartsWith](/powerquery-m/text-startswith) 또는 [Text.EndsWith](/powerquery-m/text-endswith)를 사용하는 텍스트 포함
* 모든 'IsIn' [Date 함수](/powerquery-m/date-functions)를 포함하는 날짜 범위 
* and, or 또는 not 조건을 사용한 조합

## <a name="adding-and-transforming-columns"></a>열 추가 및 변환

M 함수 [Table.AddColumn](/powerquery-m/table-addcolumn), [Table.TransformColumns](/powerquery-m/table-transformcolumns), [Table.ReplaceValue](/powerquery-m/table-replacevalue), [Table.DuplicateColumn](/powerquery-m/table-duplicatecolumn)은 열을 추가하거나 변환합니다. 다음은 지원되는 변환 함수입니다.

* 수치 연산
* 텍스트 연결
* 날짜 및 시간 산술 연산(산술 연산자, [Date.AddDays](/powerquery-m/date-adddays), [Date.AddMonths](/powerquery-m/date-addmonths), [Date.AddQuarters](/powerquery-m/date-addquarters), [Date.AddWeeks](/powerquery-m/date-addweeks), [Date.AddYears](/powerquery-m/date-addyears))
* 기간을 날짜 및 시간 산술 연산에 사용할 수 있지만 싱크에 쓰기 전에 다른 형식으로 변환해야 합니다(산술 연산자, [#duration](/powerquery-m/sharpduration), [Duration.Days](/powerquery-m/duration-days), [Duration.Hours](/powerquery-m/duration-hours), [Duration.Minutes](/powerquery-m/duration-minutes), [Duration.Seconds](/powerquery-m/duration-seconds), [Duration.TotalDays](/powerquery-m/duration-totaldays), [Duration.TotalHours](/powerquery-m/duration-totalhours), [Duration.TotalMinutes](/powerquery-m/duration-totalminutes), [Duration.TotalSeconds](/powerquery-m/duration-totalseconds)).    
* 대부분의 표준, 공학 및 삼각 수치 함수(Number.Factorial, Number.Permutations 및 Number.Combinations를 *제외하고* [연산](/powerquery-m/number-functions#operations), [반올림](/powerquery-m/number-functions#rounding) 및 [삼각](/powerquery-m/number-functions#trigonometry) 아래에 있는 모든 함수)
* 대체([Replacer.ReplaceText](/powerquery-m/replacer-replacetext), [Replacer.ReplaceValue](/powerquery-m/replacer-replacevalue), [Text.Replace](/powerquery-m/text-replace), [Text.Remove](/powerquery-m/text-remove))
* 위치 텍스트 추출([Text.PositionOf](/powerquery-m/text-positionof), [Text.Length](/powerquery-m/text-length), [Text.Start](/powerquery-m/text-start), [Text.End](/powerquery-m/text-end), [Text.Middle](/powerquery-m/text-middle), [Text.ReplaceRange](/powerquery-m/text-replacerange), [Text.RemoveRange](/powerquery-m/text-removerange))
* 기본 텍스트 서식([Text.Lower](/powerquery-m/text-lower), [Text.Upper](/powerquery-m/text-upper), [Text.Trim](/powerquery-m/text-trim)/[Start](/powerquery-m/text-trimstart)/[End](/powerquery-m/text-trimend), [Text.PadStart](/powerquery-m/text-padstart)/[End](/powerquery-m/text-padend), [Text.Reverse](/powerquery-m/text-reverse))
* 날짜/시간 함수([Date.Day](/powerquery-m/date-day), [Date.Month](/powerquery-m/date-month), [Date.Year](/powerquery-m/date-year) [Time.Hour](/powerquery-m/time-hour), [Time.Minute](/powerquery-m/time-minute), [Time.Second](/powerquery-m/time-second), [Date.DayOfWeek](/powerquery-m/date-dayofweek), [Date.DayOfYear](/powerquery-m/date-dayofyear), [Date.DaysInMonth](/powerquery-m/date-daysinmonth))
* If 식(그러나 분기에 일치하는 형식이 있어야 함)
* 행 필터를 논리적 열로
* 숫자, 텍스트, 논리, 날짜 및 날짜/시간 상수

## <a name="mergingjoining-tables"></a>테이블 병합/조인

* 파워 쿼리는 중첩된 조인을 생성합니다(Table.NestedJoin, 사용자는 수동으로 [Table.AddJoinColumn](/powerquery-m/table-addjoincolumn)을 쓸 수 있음).
    그러면 사용자가 중첩된 조인 열을 중첩되지 않은 조인으로 확장해야 합니다(Table.ExpandTableColumn, 다른 컨텍스트에서는 지원되지 않음).
* M 함수 [Table.Join](/powerquery-m/table-join)은 직접 쓸 수 있으므로 추가 확장 단계가 필요하지 않지만, 사용자는 조인된 테이블 간에 중복된 열 이름이 없는지 확인해야 합니다.
* 지원되는 조인 종류: [Inner](/powerquery-m/joinkind-inner), [LeftOuter](/powerquery-m/joinkind-leftouter), [RightOuter](/powerquery-m/joinkind-rightouter), [FullOuter](/powerquery-m/joinkind-fullouter)
* [Value.Equals](/powerquery-m/value-equals) 및 [Value.NullableEquals](/powerquery-m/value-nullableequals) 값 모두 키 같음 비교자로 지원됩니다.

## <a name="group-by"></a>Group By

[Table.Group](/powerquery-m/table-group)을 사용하여 값을 집계합니다.
* 집계 함수와 함께 사용해야 합니다.
* 지원되는 집계 함수: [list. Sum](/powerquery-m/list-sum), [list. Count](/powerquery-m/list-count), [list. Average](/powerquery-m/list-average), [list. Min](/powerquery-m/list-min), [list. Max](/powerquery-m/list-max), [List. standarddeviation](/powerquery-m/list-standarddeviation), [list. First](/powerquery-m/list-first), [list. Last](/powerquery-m/list-last)

## <a name="sorting"></a>정렬

[Table.Sort](/powerquery-m/table-sort)를 사용하여 값을 정렬합니다.

## <a name="reducing-rows"></a>행 축소

상위 행 유지 및 제거, 범위 유지(해당하는 M 함수, 개수만 지원하고 조건은 지원하지 않음: [Table.FirstN](/powerquery-m/table-firstn), [Table.Skip](/powerquery-m/table-skip), [Table.RemoveFirstN](/powerquery-m/table-removefirstn), [Table.Range](/powerquery-m/table-range), [Table.MinN](/powerquery-m/table-minn), [Table.MaxN](/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>지원되지 않는 알려진 함수

| 함수 | 상태 |
| -- | -- |
| Table.PromoteHeaders | 지원되지 않습니다. 데이터 세트에서 "첫 행을 머리글로"를 설정하여 동일한 결과를 얻을 수 있습니다. |
| Table.CombineColumns | 직접 지원되지 않지만 지정된 두 열을 연결하는 새 열을 추가하여 달성할 수 있는 일반적인 시나리오입니다.  예: Table.AddColumn(RemoveEmailColumn, "Name", each [FirstName] & " " & [LastName]) |
| Table.TransformColumnTypes | 대부분의 경우에 지원됩니다. 문자열을 통화 형식으로 변환하거나, 문자열을 시간 형식으로 변환하거나, 문자열을 백분율 형식으로 변환하는 것과 같은 시나리오는 지원되지 않습니다. |
| Table.NestedJoin | 조인을 수행하면 유효성 검사 오류가 발생합니다. 제대로 작동하려면 열을 확장해야 합니다. |
| Table.Distinct | 중복 행 제거는 지원되지 않습니다. |
| Table.RemoveLastN | 하위 행 제거는 지원되지 않습니다. |
| Table.RowCount | 지원되지 않지만 값 1을 포함하는 사용자 지정 열을 추가한 다음, List.Sum을 사용하여 해당 열을 집계하여 달성할 수 있습니다. Table.Group은 지원됩니다. | 
| 행 수준 오류 처리 | 행 수준 오류 처리는 현재 지원되지 않습니다. 예를 들어, 열에서 숫자가 아닌 값을 필터링하려면 한 가지 방법은 텍스트 열을 숫자로 변환하는 것입니다. 변환에 실패하는 모든 셀은 오류 상태가 되며 필터링해야 합니다. 이 시나리오는 스케일 아웃 M에서 가능하지 않습니다. |
| Table.Transpose | 지원되지 않음 |
| Table.Pivot | 지원되지 않음 |
| Table.SplitColumn | 부분적으로 지원됨 |

## <a name="m-script-workarounds"></a>M 스크립트 해결 방법

### ```SplitColumn```

길이 및 위치를 기준으로 분할에 대 한 대체는 아래에 나열 됩니다.

* Table.AddColumn(소스, "첫 번째 문자", 각 Text.Start([Email], 7), 텍스트 입력)
* Table.AddColumn(#"첫 번째 문자 삽입", "텍스트 범위", 각 Text.Middle([Email], 4, 9), 텍스트 입력)

이 옵션은 리본의 추출 옵션에서 액세스할 수 있습니다.

:::image type="content" source="media/wrangling-data-flow/power-query-split.png" alt-text="Power Query 열 추가":::

### ```Table.CombineColumns```

* Table.AddColumn(RemoveEmailColumn, "이름", 각 [FirstName] & " " & [LastName])

### <a name="pivots"></a>피벗

* PQ 편집기에서 피벗 변환을 선택 하 고 피벗 열을 선택 합니다.

![파워 쿼리 피벗 공통](media/wrangling-data-flow/power-query-pivot-1.png)

* 그런 다음 값 열 및 집계 함수를 선택 합니다.

![파워 쿼리 피벗 선택기](media/wrangling-data-flow/power-query-pivot-2.png)

* 확인을 클릭 하면 편집기의 데이터가 피벗 된 값으로 업데이트 된 것을 볼 수 있습니다.
* 변환이 지원 되지 않을 수도 있다는 경고 메시지가 표시 됩니다.
* 이 경고를 해결 하려면 PQ 편집기를 사용 하 여 피벗 된 목록을 수동으로 확장 합니다.
* 리본 메뉴에서 고급 편집기 옵션 선택
* 피벗 된 값 목록을 수동으로 확장 합니다.
* List. Distinct ()를 다음과 같은 값 목록으로 바꿉니다.
```
#"Pivoted column" = Table.Pivot(Table.TransformColumnTypes(#"Changed column type 1", {{"genres", type text}}), {"Drama", "Horror", "Comedy", "Musical", "Documentary"}, "genres", "Rating", List.Average)
in
  #"Pivoted column"
```

## <a name="next-steps"></a>다음 단계

[ADF에서 데이터 랭글링 파워 쿼리를 만드는 방법](wrangling-tutorial.md)에 대해 알아보세요.
