---
title: 매핑 데이터 흐름의 식 작성기
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics의 매핑 데이터 흐름에서 식 작성기를 사용하여 식 작성
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 6a21299d505d0132bab432223095850b3006b05e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651278"
---
# <a name="build-expressions-in-mapping-data-flow"></a>매핑 데이터 흐름에서 식 작성

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

매핑 데이터 흐름에서 많은 변환 속성이 식으로 입력됩니다. 이러한 식은 런타임에 Spark 데이터 유형으로 평가되는 열 값, 매개 변수, 함수, 연산자 및 리터럴로 구성됩니다. 매핑 데이터 흐름에는 **식 작성기** 라는 이러한 식을 작성하는 데 도움이 되는 전용 환경이 있습니다. 강조 표시, 구문 검사 및 자동 완성을 위해 [IntelliSense](/visualstudio/ide/using-intellisense) 코드 완성을 활용하는 식 작성기는 데이터 흐름을 쉽게 구축할 수 있도록 설계되었습니다. 이 문서에서는 식 작성기를 사용하여 비즈니스 논리를 효과적으로 구축하는 방법을 설명합니다.

:::image type="content" source="media/data-flow/expresion-builder.png" alt-text="식 작성기":::

## <a name="open-expression-builder"></a>식 작성기 열기

식 작성기를 여는 데에는 여러 진입점이 있습니다. 이는 모두 데이터 흐름 변환의 특정 컨텍스트에 따라 다릅니다. 가장 일반적인 사용 사례는 사용자가 데이터 흐름 식 언어를 사용하여 열을 만들거나 업데이트하는 [파생 열](data-flow-derived-column.md) 및 [집계](data-flow-aggregate.md)와 같은 변환에 있습니다. 열 목록 위에 있는 **식 작성기 열기** 를 선택하여 식 작성기를 열 수 있습니다. 열 컨텍스트를 클릭하고 해당 식에 대한 식 작성기를 직접 열 수도 있습니다.

:::image type="content" source="media/data-flow/open-expression-builder-derive.png" alt-text="식 작성기 열기 파생":::

[필터](data-flow-filter.md)와 같은 일부 변환에서 파란색 식 텍스트 상자를 클릭하면 식 작성기가 열립니다. 

:::image type="content" source="media/data-flow/expressionbox.png" alt-text="파란색 식 상자":::

일치 또는 group-by 조건에서 열을 참조하는 경우 식이 열에서 값을 추출할 수 있습니다. 식을 만들려면 **계산 열** 을 선택합니다.

:::image type="content" source="media/data-flow/computedcolumn.png" alt-text="계산 열 옵션":::

식 또는 리터럴 값이 유효한 입력인 경우 **동적 콘텐츠 추가** 를 선택하여 리터럴 값으로 평가되는 식을 작성합니다.

:::image type="content" source="media/data-flow/add-dynamic-content.png" alt-text="동적 콘텐츠 추가 옵션":::

## <a name="expression-elements"></a>식 요소

매핑 데이터 흐름에서 식은 열 값, 매개 변수, 함수, 지역 변수, 연산자 및 리터럴로 구성될 수 있습니다. 이러한 식은 문자열, 부울 또는 정수와 같은 Spark 데이터 유형으로 평가되어야 합니다.

:::image type="content" source="media/data-flow/expression-elements.png" alt-text="식 요소":::

### <a name="functions"></a>Functions

매핑 데이터 흐름에는 식에 사용할 수 있는 기본 제공 함수와 연산자가 있습니다. 사용 가능한 함수 목록은 [매핑 데이터 흐름 언어 참조](data-flow-expression-functions.md)를 참조하세요.

#### <a name="address-array-indexes"></a>주소 배열 인덱스

배열 유형을 반환하는 열이나 함수를 처리할 때 대괄호([])를 사용하여 특정 요소에 액세스합니다. 인덱스가 존재하지 않으면 식은 NULL로 평가됩니다.

:::image type="content" source="media/data-flow/expression-array.png" alt-text="식 작성기 배열":::

> [!IMPORTANT]
> 매핑 데이터 흐름에서 배열은 1부터 시작하므로 첫 번째 요소가 인덱스 1에서 참조됩니다. 예를 들어, myArray[1]은 'myArray'라는 배열의 첫 번째 요소에 액세스합니다.

### <a name="input-schema"></a>입력 스키마

데이터 흐름이 해당 원본에서 정의된 스키마를 사용하는 경우 여러 식에서 이름으로 열을 참조할 수 있습니다. 스키마 드리프트를 사용하는 경우 `byName()` 또는 `byNames()` 함수를 사용하여 명시적으로 열을 참조하거나 열 패턴을 사용하여 일치시킬 수 있습니다.

#### <a name="column-names-with-special-characters"></a>특수 문자가 있는 열 이름

특수 문자나 공백이 포함된 열 이름이 있는 경우 식에서 참조할 수 있도록 이름을 중괄호로 묶습니다.

`{[dbo].this_is my complex name$$$}`

### <a name="parameters"></a>매개 변수

매개 변수는 런타임 시 파이프라인에서 데이터 흐름으로 전달되는 값입니다. 매개 변수를 참조하려면 **식 요소** 보기에서 매개 변수를 클릭하거나 이름 앞에 달러 기호를 사용하여 매개 변수를 참조합니다. 예를 들어, parameter1이라는 매개 변수는 `$parameter1`에 의해 참조됩니다. 자세한 내용은 [매핑 데이터 흐름 매개 변수화](parameters-data-flow.md)를 참조하세요. 

### <a name="cached-lookup"></a>캐시된 조회

캐시된 조회를 사용하면 캐시된 싱크 출력의 인라인 조회를 수행할 수 있습니다. 각 싱크에는 사용할 수 있는 두 가지 함수 `lookup()` 및 `outputs()`가 있습니다. 이러한 함수를 참조하는 구문은 `cacheSinkName#functionName()`입니다. 자세한 내용은 [캐시 싱크](data-flow-sink.md#cache-sink)를 참조하세요.

`lookup()`은 현재 변환에서 일치하는 열을 매개 변수로 사용하고 캐시 싱크의 키 열과 일치하는 행과 동일한 복합 열을 반환합니다. 반환된 복합 열에는 캐시 싱크에 매핑된 각 열에 대한 하위 열이 포함됩니다. 예를 들어 코드와 일치하는 키 열과 `Message`라는 열이 있는 오류 코드 캐시 싱크 `errorCodeCache`가 있는 경우입니다. `errorCodeCache#lookup(errorCode).Message`를 호출하면 전달된 코드에 해당하는 메시지가 반환됩니다. 

`outputs()`는 매개 변수를 사용하지 않고 전체 캐시 싱크를 복합 열의 배열로 반환합니다. 싱크에 키 열이 지정되어 있으면 호출할 수 없으며 캐시 싱크에 행 수가 적은 경우에만 사용해야 합니다. 일반적인 사용 사례에서는 증분 키의 최댓값을 추가합니다. 캐시된 단일 집계 행 `CacheMaxKey`에 열 `MaxKey`가 포함된 경우 `CacheMaxKey#outputs()[1].MaxKey`를 호출하여 첫 번째 값을 참조할 수 있습니다.

:::image type="content" source="media/data-flow/cached-lookup-example.png" alt-text="캐시된 조회":::

### <a name="locals"></a>로컬

여러 열에서 논리를 공유하거나 논리를 구분하려는 경우 파생 열 내에서 로컬을 만들 수 있습니다. 로컬을 참조하려면 **식 요소** 보기에서 로컬을 클릭하거나 이름 앞에 콜론을 사용하여 참조합니다. 예를 들어 local1이라는 로컬은 `:local1`로 참조됩니다. [파생 열 설명서](data-flow-derived-column.md#locals)에서 로컬에 대해 자세히 알아봅니다.

## <a name="preview-expression-results"></a>식 결과 미리 보기

[디버그 모드](concepts-data-flow-debug-mode.md)가 켜져 있는 경우 디버그 클러스터를 대화형으로 사용하여 식이 평가하는 대상을 미리 볼 수 있습니다. 데이터 미리 보기의 결과를 업데이트하려면 데이터 미리 보기 옆에 있는 **새로 고침** 을 선택합니다. 입력 열이 주어지면 각 행의 출력을 볼 수 있습니다.

:::image type="content" source="media/data-flow/preview-expression.png" alt-text="진행 중인 미리 보기":::

## <a name="string-interpolation"></a>문자열 보간

식 요소를 사용하는 긴 문자열을 만들 때 문자열 보간을 사용하여 복잡한 문자열 논리를 쉽게 작성할 수 있습니다. 문자열 보간은 매개 변수가 쿼리 문자열에 포함될 때 문자열 연결의 광범위한 사용을 방지합니다. 큰따옴표를 사용하여 식과 함께 리터럴 문자열 텍스트를 묶습니다. 식 함수, 열 및 매개 변수를 포함할 수 있습니다. 식 구문을 사용하려면 중괄호로 묶습니다.

문자열 보간의 몇 가지 예:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

* ```"{:playerName} is a {:playerRating} player"```

> [!NOTE]
> SQL 원본 쿼리에서 문자열 보간 구문을 사용할 때 쿼리 문자열은 '/n' 없이 한 줄에 있어야 합니다.

## <a name="commenting-expressions"></a>식에 주석 달기

한 줄 및 여러 줄 주석 구문을 사용하여 식에 주석을 추가합니다.

다음 예는 유효한 주석입니다.

* ```/* This is my comment */```

* ```/* This is a```
* ```multi-line comment */```

식 상단에 주석을 넣으면 변환 식을 문서화할 수 있도록 변환 텍스트 상자에 표시됩니다.

:::image type="content" source="media/data-flow/comment-expression.png" alt-text="변환 텍스트 상자의 설명":::

## <a name="regular-expressions"></a>정규식

많은 식 언어 함수는 정규식 구문을 사용합니다. 정규식 함수를 사용할 때 식 작성기는 백슬래시(\\)를 이스케이프 문자 시퀀스로 해석하려고 합니다. 정규식에 백슬래시를 사용하는 경우 전체 regex를 역따옴표(\`)로 묶거나 이중 백슬래시를 사용합니다.

역따옴표를 사용하는 예:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

이중 슬래시를 사용하는 예:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="keyboard-shortcuts"></a>바로 가기 키

다음은 식 작성기에서 사용할 수 있는 단축키 목록입니다. 대부분의 Intellisense 단축키는 식을 만들 때 사용할 수 있습니다.

* Ctrl+K Ctrl+C: 전체 줄을 주석으로 처리합니다.
* Ctrl+K Ctrl+U: 주석 처리를 제거합니다.
* F1: 편집기 도움말 명령을 제공합니다.
* Alt+아래쪽 화살표 키: 현재 줄 아래로 이동합니다.
* Alt+위쪽 화살표 키: 현재 줄 위로 이동합니다.
* Ctrl+스페이스바: 상황에 맞는 도움말을 표시합니다.

## <a name="commonly-used-expressions"></a>자주 사용하는 식

### <a name="convert-to-dates-or-timestamps"></a>날짜 또는 타임스탬프로 변환

타임스탬프 출력에 문자열 리터럴을 포함하려면 변환을 `toString()`으로 래핑합니다.

`toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')`

epoch에서 날짜 또는 타임스탬프로 밀리초를 변환하려면 `toTimestamp(<number of milliseconds>)`를 사용합니다. 초 단위 시간은 1,000을 곱합니다.

`toTimestamp(1574127407*1000l)`

이전 식 끝에 있는 후행 "l"는 인라인 구문으로 긴 유형으로의 변환을 의미합니다.

### <a name="find-time-from-epoch-or-unix-time"></a>epoch 또는 Unix 시간에서 시간 찾기

toLong( currentTimestamp() - toTimestamp('1970-01-01 00:00:00.000', 'yyyy-MM-dd HH:mm:ss.SSS') ) * 1000l

### <a name="data-flow-time-evaluation"></a>데이터 흐름 시간 평가

데이터 흐름은 밀리초까지 처리합니다. *2018-07-31T20:00:00.2170000* 의 경우 출력에 *2018-07-31T20:00:00.217* 이 표시됩니다.
서비스의 포털에서 타임스탬프는 217을 제거할 수 있는 **현재 브라우저 설정** 에 표시되지만 데이터 흐름을 엔드투엔드로 실행할 때는 217(밀리초 부분도 처리됨)입니다. toString(myDateTimeColumn)을 식으로 사용하고 미리 보기에서 전체 자릿수 데이터를 볼 수 있습니다. 모든 실용적인 목적을 위해 날짜 시간을 문자열이 아닌 날짜 시간으로 처리합니다.
 

## <a name="next-steps"></a>다음 단계

[데이터 변환 식 작성 시작](data-flow-expression-functions.md)
