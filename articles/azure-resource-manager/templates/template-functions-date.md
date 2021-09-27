---
title: 템플릿 함수 - 날짜
description: ARM 템플릿(Azure Resource Manager 템플릿)에서 날짜 작업을 수행하는 데 사용할 수 있는 함수에 대해 설명합니다.
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: ed7c9d44458acb6733618ff84cf98b9bed2e2a36
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124793420"
---
# <a name="date-functions-for-arm-templates"></a>ARM 템플릿의 날짜 함수

Resource Manager는 ARM 템플릿(Azure Resource Manager 템플릿)에서 날짜 작업을 수행하는 데 사용할 수 있도록 아래의 함수를 제공합니다.

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

기본 값에 기간을 추가합니다. ISO 8601 형식이 예상됩니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| base | 예 | 문자열 | 더하기의 시작 날짜/시간 값. [ISO 8601 타임스탬프 형식](https://en.wikipedia.org/wiki/ISO_8601)을 사용합니다. |
| duration | 예 | 문자열 | 베이스에 추가할 시간 값. 음수가 될 수 있습니다. [ISO 8601 기간 형식](https://en.wikipedia.org/wiki/ISO_8601#Durations)을 사용합니다. |
| format | 예 | 문자열 | 날짜/시간 결과의 출력 형식입니다. 제공되지 않는 경우, 기준 값의 형식이 사용됩니다. [표준 형식 문자열](/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 문자열](/dotnet/standard/base-types/custom-date-and-time-format-strings)을 사용합니다. |

### <a name="return-value"></a>반환 값

기준 값에 기간 값을 더하여 생성되는 날짜/시간 값입니다.

### <a name="examples"></a>예제

다음 예제 템플릿에서는 시간 값을 더하는 여러 가지 방법을 보여줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/date/datetimeadd.json":::

앞선 템플릿이 기본 시간 `2020-04-07 14:53:14Z`으로 배포되는 경우 출력은 다음과 같습니다.

| 이름 | 유형 | 값 |
| ---- | ---- | ----- |
| add3YearsOutput | String | 4/7/2023 2:53:14 PM |
| subtract9DaysOutput | String | 3/29/2020 2:53:14 PM |
| add1HourOutput | String | 4/7/2020 3:53:14 PM |

다음 예제 템플릿에서는 Automation 일정의 시작 시간을 설정하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/date/datetimeadd-automation.json":::

## <a name="utcnow"></a>utcNow

`utcNow(format)`

현재(UTC) 날짜/시간 값을 지정된 양식으로 반환합니다. 제공되는 형식이 없는 경우 ISO 8601(`yyyyMMddTHHmmssZ`) 형식이 사용됩니다. **이 함수는 매개 변수의 기본값에만 사용할 수 있습니다.**

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | 설명 |
|:--- |:--- |:--- |:--- |
| format |예 |문자열 |문자열로 변환할 URI 인코딩 값입니다. [표준 형식 문자열](/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 문자열](/dotnet/standard/base-types/custom-date-and-time-format-strings)을 사용합니다. |

### <a name="remarks"></a>설명

이 함수는 매개 변수의 기본값에 대한 언어 식 내에서만 사용할 수 있습니다. 템플릿의 다른 위치에서 사용하면 오류가 반환됩니다. 호출될 때마다 다른 값을 반환하는 함수이므로 템플릿의 다른 부분에서는 허용되지 않습니다. 동일한 매개 변수를 사용하여 동일한 템플릿을 배포하면 동일한 결과가 안정적으로 생성되지 않습니다.

이전에 성공한 배포에 [오류 시 롤백 옵션](rollback-on-error.md)을 사용하고 이전 배포에 utcNow를 사용하는 매개 변수가 포함된 경우 해당 매개 변수는 재평가되지 않습니다. 대신 이전 배포의 매개 변수 값이 롤백 배포에서 자동으로 다시 사용됩니다.

기본값에 대한 utcNow 함수에 의존하는 템플릿은 주의를 기울여 재배포해야 합니다. 재배포할 때 매개 변수 값을 제공하지 않으면 함수는 재평가됩니다. 새 리소스를 만드는 대신 기존 리소스를 업데이트하려면 이전 배포의 매개 변수 값을 제공해야 합니다.

### <a name="return-value"></a>반환 값

현재 UTC 날짜/시간 값입니다.

### <a name="examples"></a>예제

다음 예제 템플릿에서는 날짜/시간 값의 다양한 형식을 보여줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/date/utcnow.json":::

이전 예제의 출력은 각 배포마다 다르지만 다음과 유사합니다.

| 이름 | 유형 | 값 |
| ---- | ---- | ----- |
| utcOutput | 문자열 | 20190305T175318Z |
| utcShortOutput | 문자열 | 2019/03/05 |
| utcCustomOutput | 문자열 | 3 5 |

다음 예제에서는 태그 값을 설정할 때 함수의 값을 사용하는 방법을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/date/utcnow-tag.json":::

## <a name="next-steps"></a>다음 단계

* ARM 템플릿의 섹션에 대한 설명은 [ARM 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하십시오.
