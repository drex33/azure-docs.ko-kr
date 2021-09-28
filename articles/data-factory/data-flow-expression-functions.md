---
title: 매핑 데이터 흐름의 식 함수
titleSuffix: Azure Data Factory & Azure Synapse
description: 매핑 데이터 흐름의 식 함수에 대해 알아봅니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/23/2021
ms.openlocfilehash: dc0bdf8f8d40b23f4f6e8338ab6b608278428149
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060274"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>매핑 데이터 흐름의 데이터 변환 식

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

이 문서에서는 데이터 흐름을 매핑하는 Azure Data Factory 및 Azure Synapse Analytics에서 지 원하는 식 및 함수에 대 한 세부 정보를 제공 합니다.


## <a name="expression-functions"></a>식 함수

Data Factory 및 Synapse 파이프라인에서 매핑 데이터 흐름 기능의 식 언어를 사용하여 데이터 변환을 구성합니다.

| 식 함수 | 작업 |
|-----|-----|
| [abs](data-flow-expression-functions.md#abs) | 숫자의 절대값입니다.  |
| [acos](data-flow-expression-functions.md#acos) | 역코사인 값을 계산합니다.  |
| [add](data-flow-expression-functions.md#add) | 문자열 또는 숫자의 쌍을 추가합니다. 날짜에 일 수를 추가합니다. 타임스탬프에 기간을 추가합니다. 유사한 형식의 한 배열을 다른 배열에 추가합니다. \+ 연산자와 같습니다.  |
| [addDays](data-flow-expression-functions.md#addDays) | 날짜 또는 타임스탬프에 일을 추가합니다. 날짜에 대한 + 연산자와 같습니다.  |
| [addMonths](data-flow-expression-functions.md#addMonths) | 날짜 또는 타임스탬프에 월을 추가합니다. 필요에 따라 표준 시간대를 전달할 수 있습니다.  |
| [and](data-flow-expression-functions.md#and) | 논리적 AND 연산자입니다. &&과 같습니다.  |
| [asin](data-flow-expression-functions.md#asin) | 역사인 값을 계산합니다.  |
| [atan](data-flow-expression-functions.md#atan) | 역탄젠트 값을 계산합니다.  |
| [atan2](data-flow-expression-functions.md#atan2) | 평면의 양의 x축과 좌표로 지정된 점 사이의 라디안 각도를 반환합니다.  |
| [간에만](data-flow-expression-functions.md#between) | 첫 번째 값이 다른 두 값 사이에 있는지 확인합니다. 숫자, 문자열 및 날짜/시간 값과 비교할 수 있습니다.   |
| [유형에 대해 bitwiseand](data-flow-expression-functions.md#bitwiseAnd) | 정수 형식의 비트 And 연산자. & 연산자와 같음  |
| [bitwiseOr](data-flow-expression-functions.md#bitwiseOr) | 정수 형식의 비트 Or 연산자. 연산자와 같음 \|  |
| [bitwiseXor](data-flow-expression-functions.md#bitwiseXor) | 정수 형식의 비트 Or 연산자. 연산자와 같음 \|  |
| [blake2b](data-flow-expression-functions.md#blake2b) | 8과 512 사이에서 8의 배수만 될 수 있는 비트 길이가 주어진 다양한 기본 데이터 형식 열 세트의 Blake2 다이제스트를 계산합니다. 행의 지문을 계산하는 데 사용할 수 있습니다.   |
| [blake2bBinary](data-flow-expression-functions.md#blake2bBinary) | 8과 512 사이에서 8의 배수만 될 수 있는 비트 길이가 주어진 다양한 기본 데이터 형식 열 세트의 Blake2 다이제스트를 계산합니다. 행의 지문을 계산하는 데 사용할 수 있습니다.   |
| [case](data-flow-expression-functions.md#case) | 교대로 나오는 조건에 따라 하나의 값 또는 다른 값이 적용됩니다. 입력 수가 짝수이면 마지막 조건에서 다른 값은 Null로 기본 설정됩니다.  |
| [cbrt](data-flow-expression-functions.md#cbrt) | 숫자의 세제곱근을 계산합니다.  |
| [ceil](data-flow-expression-functions.md#ceil) | 해당 수보다 작지 않은 가장 작은 정수를 반환합니다.  |
| [coalesce](data-flow-expression-functions.md#coalesce) | 입력에서 Null이 아닌 첫 번째 값을 반환합니다. 모든 입력은 동일한 형식이어야 합니다.  |
| [columnNames](data-flow-expression-functions.md#columnNames) | 스트림의 모든 출력 열 이름을 가져옵니다. 선택적 스트림 이름을 두 번째 인수로 전달할 수 있습니다.  |
| [세로](data-flow-expression-functions.md#columns) | 스트림의 모든 출력 열 값을 가져옵니다. 선택적 스트림 이름을 두 번째 인수로 전달할 수 있습니다.   |
| [과](data-flow-expression-functions.md#compare) | 동일한 형식의 두 값을 비교합니다. value1이 value2보다 작으면 음의 정수, value1과 value2가 같으면 0, value1이 value2보다 크면 양수를 반환합니다.  |
| [concat](data-flow-expression-functions.md#concat) | 가변 개수의 문자열을 함께 연결합니다. 문자열이 있는 + 연산자와 같습니다.  |
| [concatWS](data-flow-expression-functions.md#concatWS) | 가변 개수의 문자열을 구분 기호와 함께 연결합니다. 첫 번째 매개 변수는 구분 기호입니다.  |
| [cos](data-flow-expression-functions.md#cos) | 코사인 값을 계산합니다.  |
| [cosh](data-flow-expression-functions.md#cosh) | 값의 쌍곡선 코사인을 계산합니다.  |
| [crc32](data-flow-expression-functions.md#crc32) | 0(256), 224, 256, 384, 512 값의 지정된 비트 길이를 갖는 다양한 기본 데이터 형식의 열 세트에서 CRC32 해시를 계산합니다. 행의 지문을 계산하는 데 사용할 수 있습니다.  |
| [currentDate](data-flow-expression-functions.md#currentDate) | 이 작업 실행이 시작되는 현재 날짜를 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 현지 표준 시간대가 기본적으로 사용됩니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). |
| [유형이 currenttimestamp](data-flow-expression-functions.md#currentTimestamp) | 현지 표준 시간대를 사용하여 작업 실행을 시작하는 경우 현재 타임스탬프를 가져옵니다.  |
| [currentUTC](data-flow-expression-functions.md#currentUTC) | 현재 타임스탬프를 UTC로 가져옵니다. 현재 시간을 클러스터 표준 시간대와 다른 표준 시간대로 해석하려면 'GMT', 'PST', 'UTC', 'America / Cayman' 형식으로 표준 시간대를 선택적으로 전달할 수 있습니다. 기본적으로 현재 표준 시간대가 사용됩니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). UTC 시간을 다른 표준 시간대로 변환하려면 `fromUTC()`를 사용합니다.  |
| [dayOfMonth](data-flow-expression-functions.md#dayOfMonth) | 지정된 날짜의 월중 일을 가져옵니다.  |
| [dayOfWeek](data-flow-expression-functions.md#dayOfWeek) | 지정된 날짜의 요일을 가져옵니다. 1 - 일요일, 2 - 월요일 ..., 7 - 토요일.  |
| [dayOfYear](data-flow-expression-functions.md#dayOfYear) | 지정된 날짜의 연중 일을 가져옵니다.  |
| [days](data-flow-expression-functions.md#days) | 기간(일)의 시간(밀리초).  |
| [도](data-flow-expression-functions.md#degrees) | 라디안을 도로 변환합니다.  |
| [분할](data-flow-expression-functions.md#divide) | 숫자 쌍을 나눕니다. `/` 연산자와 같습니다.  |
| [dropLeft](data-flow-expression-functions.md#dropLeft) | 문자열 왼쪽에서 문자를 최대한 제거합니다. 요청된 삭제가 문자열의 길이를 초과하면 빈 문자열이 반환됩니다.|
| [dropRight](data-flow-expression-functions.md#dropRight) | 문자열의 오른쪽에서 문자를 최대한 제거합니다. 요청된 삭제가 문자열의 길이를 초과하면 빈 문자열이 반환됩니다.|
| [endsWith](data-flow-expression-functions.md#endsWith) | 문자열이 제공된 문자열로 끝나는지 확인합니다.  |
| [equals](data-flow-expression-functions.md#equals) | 비교 같음 연산자. == 연산자와 같습니다.  |
| [equalsIgnoreCase](data-flow-expression-functions.md#equalsIgnoreCase) | 비교는 대/소문자를 무시하는 연산자와 같습니다. <=> 연산자와 같습니다.  |
| [탈출](data-flow-expression-functions.md#escape) | 형식에 따라 문자열을 이스케이프합니다. 허용되는 형식의 리터럴 값은 'json', 'xml', 'ecmascript', 'html', 'java'입니다.|
| [expr](data-flow-expression-functions.md#expr) | 문자열에서 식이 생성됩니다. 이것은 이 식을 리터럴이 아닌 형식으로 쓰는 것과 같습니다. 매개 변수를 문자열 표현으로 전달하는 데 사용할 수 있습니다.|
| [계승값](data-flow-expression-functions.md#factorial) | 숫자의 계승값을 계산합니다.  |
| [false](data-flow-expression-functions.md#false) | 항상 false 값을 반환합니다. 'false'라는 열이 있는 경우 함수 `syntax(false())`를 사용합니다.  |
| [floor](data-flow-expression-functions.md#floor) | 해당 수보다 크지 않은 가장 큰 정수를 반환합니다.  |
| [fromBase64](data-flow-expression-functions.md#fromBase64) | 지정된 base64로 인코딩된 문자열을 디코딩합니다.|
| [fromUTC](data-flow-expression-functions.md#fromUTC) | UTC의 타임스탬프로 변환합니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 표준 시간대를 선택적으로 전달할 수 있습니다. 기본적으로 현재 표준 시간대가 사용됩니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [greater](data-flow-expression-functions.md#greater) | 비교 보다 큼 연산자. > 연산자와 같습니다.  |
| [greaterOrEqual](data-flow-expression-functions.md#greaterOrEqual) | 비교 보다 크거나 같음 연산자. >= 연산자와 같습니다.  |
| [최고의](data-flow-expression-functions.md#greatest) | Null 값을 건너뛰는 입력으로 값 목록 중에서 가장 큰 값을 반환합니다. 모든 입력이 Null이면 Null을 반환합니다.  |
| [hasColumn](data-flow-expression-functions.md#hasColumn) | 스트림에서 이름을 기준으로 열 값을 확인합니다. 선택적 스트림 이름을 두 번째 인수로 전달할 수 있습니다. 디자인 타임에 알려진 열 이름은 해당 이름으로만 처리되어야 합니다. 계산된 입력은 지원되지 않지만 매개 변수 대체를 사용할 수 있습니다.  |
| [hour](data-flow-expression-functions.md#hour) | 타임스탬프의 시간 값을 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 현지 표준 시간대가 기본적으로 사용됩니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [hours](data-flow-expression-functions.md#hours) | 기간(시간)의 시간(밀리초).  |
| [Iif](data-flow-expression-functions.md#iif) | 조건에 따라 하나의 값 또는 다른 값이 적용됩니다. 다른 값을 지정하지 않으면 Null로 간주됩니다. 두 값은 호환되어야 합니다(숫자, 문자열 등).   |
| [iifNull](data-flow-expression-functions.md#iifNull) | 첫 번째 매개 변수가 Null인지 확인합니다. Null이 아니면 첫 번째 매개 변수가 반환됩니다. Null인 경우 두 번째 매개 변수가 반환됩니다. 세 개의 매개 변수가 지정된 경우 동작은 iif(isNull(value1), value2, value3)와 동일하며 첫 번째 값이 Null이 아닌 경우 세 번째 매개 변수가 반환됩니다.  |
| [initCap](data-flow-expression-functions.md#initCap) | 모든 단어의 첫 글자를 대문자로 변환합니다. 단어는 공백으로 구분하여 식별됩니다.  |
| [Instr](data-flow-expression-functions.md#instr) | 문자열 내에서 부분 문자열의 위치(1부터 시작)를 찾습니다. 찾을 수 없으면 0이 반환됩니다.  |
| [isDelete](data-flow-expression-functions.md#isDelete) | 행이 삭제용으로 표시되어 있는지 확인합니다. 둘 이상의 입력 스트림을 사용하는 변환은 스트림의 인덱스(1부터 시작)를 제공할 수 있습니다. 스트림 인덱스는 1 또는 2여야 하며 기본값은 1입니다.  |
| [isError](data-flow-expression-functions.md#isError) | 행이 오류로 표시되는지 여부를 확인합니다. 둘 이상의 입력 스트림을 사용하는 변환은 스트림의 인덱스(1부터 시작)를 제공할 수 있습니다. 스트림 인덱스는 1 또는 2여야 하며 기본값은 1입니다.  |
| [isIgnore](data-flow-expression-functions.md#isIgnore) | 행이 무시되도록 표시되는지 여부를 확인합니다. 둘 이상의 입력 스트림을 사용하는 변환은 스트림의 인덱스(1부터 시작)를 제공할 수 있습니다. 스트림 인덱스는 1 또는 2여야 하며 기본값은 1입니다.  |
| [isInsert](data-flow-expression-functions.md#isInsert) | 행이 삽입용으로 표시되어 있는지 확인합니다. 둘 이상의 입력 스트림을 사용하는 변환은 스트림의 인덱스(1부터 시작)를 제공할 수 있습니다. 스트림 인덱스는 1 또는 2여야 하며 기본값은 1입니다.  |
| [isMatch](data-flow-expression-functions.md#isMatch) | 조회 시 행이 일치하는지 여부를 확인합니다. 둘 이상의 입력 스트림을 사용하는 변환은 스트림의 인덱스(1부터 시작)를 제공할 수 있습니다. 스트림 인덱스는 1 또는 2여야 하며 기본값은 1입니다.  |
| [isNull](data-flow-expression-functions.md#isNull) | 값이 Null인지 확인합니다.  |
| [isUpdate](data-flow-expression-functions.md#isUpdate) | 행이 업데이트용으로 표시되어 있는지 확인합니다. 둘 이상의 입력 스트림을 사용하는 변환은 스트림의 인덱스(1부터 시작)를 제공할 수 있습니다. 스트림 인덱스는 1 또는 2여야 하며 기본값은 1입니다.  |
| [isUpsert](data-flow-expression-functions.md#isUpsert) | 행이 삽입용으로 표시되어 있는지 확인합니다. 둘 이상의 입력 스트림을 사용하는 변환은 스트림의 인덱스(1부터 시작)를 제공할 수 있습니다. 스트림 인덱스는 1 또는 2여야 하며 기본값은 1입니다.  |
| [jaroWinkler](data-flow-expression-functions.md#jaroWinkler) | 두 문자열 간의 JaroWinkler 거리를 가져옵니다. |
| [lastDayOfMonth](data-flow-expression-functions.md#lastDayOfMonth) | 지정된 날짜의 월중 마지막 날짜를 가져옵니다.  |
| [20cm(8](data-flow-expression-functions.md#least) | 비교 보다 작거나 같음 연산자. <= 연산자와 같습니다.  |
| [비어](data-flow-expression-functions.md#left) | 인덱스 1에서 시작하여 지정된 문자 수를 갖는 부분 문자열을 추출합니다. SUBSTRING(str, 1, n)과 같습니다.  |
| [length](data-flow-expression-functions.md#length) | 문자열의 길이를 반환합니다.  |
| [작을](data-flow-expression-functions.md#lesser) | 비교 보다 작음 연산자. < 연산자와 같습니다.  |
| [lesserOrEqual](data-flow-expression-functions.md#lesserOrEqual) | 비교 보다 작거나 같음 연산자. <= 연산자와 같습니다.  |
| [levenshtein](data-flow-expression-functions.md#levenshtein) | 두 문자열 간의 levenshtein 거리를 가져옵니다.  |
| [이와](data-flow-expression-functions.md#like) | 패턴은 말 그대로 일치하는 문자열을 나타냅니다. 예외는 다음과 같은 특수 기호입니다. _는 입력에서 하나의 문자와 일치합니다( ```posix```정규식)|
| [찾도록](data-flow-expression-functions.md#locate) | 특정 위치에서 시작하는 문자열 내에서 부분 문자열의 위치(1부터 시작)를 찾습니다. 이 위치를 생략하면 문자열의 시작 부분에서 찾습니다. 찾을 수 없으면 0이 반환됩니다.  |
| [로깅할](data-flow-expression-functions.md#log) | 로그 값을 계산합니다. 오일러 수 이외의 선택적인 밑을 제공할 수 있습니다(사용할 경우).  |
| [log10](data-flow-expression-functions.md#log10) | 밑이 10인 로그 값을 계산합니다.  |
| [작을](data-flow-expression-functions.md#lower) | 문자열을 소문자로 바꿉니다.  |
| [lpad](data-flow-expression-functions.md#lpad) | 특정 길이가 될 때까지 제공된 패딩으로 문자열의 왼쪽 여백을 채웁니다. 문자열이 길이보다 크거나 같은 경우 길이에 맞게 잘립니다.  |
| [ltrim](data-flow-expression-functions.md#ltrim) | 왼쪽의 선행 문자열을 삭제합니다. 두 번째 매개 변수를 지정하지 않으면 공백을 삭제합니다. 그렇지 않은 경우 두 번째 매개 변수에 지정된 모든 문자를 삭제합니다.  |
| [사용한](data-flow-expression-functions.md#md5) | 다양한 기본 데이터 형식의 열 세트에서 MD5 다이제스트를 계산하고 32자의 16진수 문자열을 반환합니다. 행의 지문을 계산하는 데 사용할 수 있습니다.  |
| [millisecond](data-flow-expression-functions.md#millisecond) | 날짜의 밀리초 값을 구합니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 현지 표준 시간대가 기본적으로 사용됩니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [milliseconds](data-flow-expression-functions.md#milliseconds) | 기간(밀리초)의 시간(밀리초).  |
| [음수](data-flow-expression-functions.md#minus) | 숫자를 뺍니다. 날짜에서 일 수를 뺍니다. 타임스탬프에서 기간을 뺍니다. 두 타임스탬프를 빼서 밀리초 단위로 차이를 구합니다. \- 연산자와 같습니다.  |
| [minute](data-flow-expression-functions.md#minute) | 타임스탬프의 분 값을 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 현지 표준 시간대가 기본적으로 사용됩니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [minutes](data-flow-expression-functions.md#minutes) | 기간(분)의 시간(밀리초).  |
| [mod](data-flow-expression-functions.md#mod) | 숫자 쌍의 모듈러스입니다. % 연산자와 같습니다.  |
| [month](data-flow-expression-functions.md#month) | 날짜 또는 타임스탬프의 월 값을 구합니다.  |
| [monthsBetween](data-flow-expression-functions.md#monthsBetween) | 두 날짜 사이의 개월 수를 구합니다. 계산을 반올림할 수 있습니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 현지 표준 시간대가 기본적으로 사용됩니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [곱하기](data-flow-expression-functions.md#multiply) | 숫자 쌍을 곱합니다. \* 연산자와 같습니다.  |
| [구할](data-flow-expression-functions.md#negate) | 숫자를 부정합니다. 양수를 음수로, 음수를 양수로 변환합니다.  |
| [nextSequence](data-flow-expression-functions.md#nextSequence) | 고유한 다음 시퀀스를 반환합니다. 이 수는 파티션 내에서만 연속되며 앞에 partitionId가 붙습니다.  |
| [normalize](data-flow-expression-functions.md#normalize) | 악센트 기호가 있는 유니코드 문자를 구분하기 위해 문자열 값을 정규화합니다.  |
| [not](data-flow-expression-functions.md#not) | 논리 부정 연산자.  |
| [notEquals](data-flow-expression-functions.md#notEquals) | 비교 같지 않음 연산자. != 연산자와 같습니다.  |
| [notNull](data-flow-expression-functions.md#notNull) | 값이 Null이 아닌지 확인합니다.  |
| [null](data-flow-expression-functions.md#null) | NULL 값을 반환합니다. 'null'이라는 열이 있는 경우 함수 `syntax(null())`를 사용합니다. 사용하는 모든 작업은 결과적으로 Null이 됩니다.  |
| [or](data-flow-expression-functions.md#or) | 논리적 OR 연산자입니다. 와 동일 \| \| 합니다.  |
| [pMod](data-flow-expression-functions.md#pMod) | 숫자 쌍의 양의 모듈러스입니다.  |
| [partitionId](data-flow-expression-functions.md#partitionId) | 입력 행이 있는 현재 파티션 ID를 반환합니다.  |
| [power](data-flow-expression-functions.md#power) | 한 수를 다른 수의 승수로 거듭제곱합니다.  |
| [각도](data-flow-expression-functions.md#radians) | 각도를 라디안으로 변환합니다.|
| [임의](data-flow-expression-functions.md#random) | 파티션 내의 선택적 시드가 지정된 난수를 반환합니다. 시드는 고정 값이어야 하며 partitionId와 함께 사용되어 임의 값을 생성합니다.   |
| [Mdq.regexextract](data-flow-expression-functions.md#regexExtract) | 지정된 정규식 패턴에 대해 일치하는 부분 문자열을 추출합니다. 마지막 매개 변수는 일치 그룹을 식별하고 생략하면 기본적으로 1이 지정됩니다. `<regex>`이스케이프를 사용 하지 않고 문자열을 일치 시키려면 (back 따옴표)를 사용 합니다.  |
| [regexMatch](data-flow-expression-functions.md#regexMatch) | 문자열이 지정된 정규식 패턴과 일치하는지 확인합니다. `<regex>`이스케이프를 사용 하지 않고 문자열을 일치 시키려면 (back 따옴표)를 사용 합니다.  |
| [regexReplace](data-flow-expression-functions.md#regexReplace) | Regex 패턴의 모든 항목을 지정 된 문자열의 다른 부분 문자열로 바꿉니다 `<regex>` (back 따옴표)를 사용 하 여 문자열을 이스케이프 없이 찾습니다.  |
| [regexSplit](data-flow-expression-functions.md#regexSplit) | 정규식을 기준으로 구분 기호에 따라 문자열을 분할하고 문자열의 배열을 반환합니다.  |
| [replace](data-flow-expression-functions.md#replace) | 한 하위 문자열이 나오는 모든 경우를 지정된 문자열의 다른 하위 문자열로 바꿉니다. 마지막 매개 변수를 생략하는 경우 기본값은 빈 문자열입니다.  |
| [reverse](data-flow-expression-functions.md#reverse) | 문자열을 반대로 바꿉니다.  |
| [오른쪽](data-flow-expression-functions.md#right) | 오른쪽부터 지정된 문자 수를 갖는 부분 문자열을 추출합니다. SUBSTRING(str, LENGTH(str) - n, n)과 같습니다.  |
| [rlike](data-flow-expression-functions.md#rlike) | 문자열이 지정된 정규식 패턴과 일치하는지 확인합니다.  |
| [둥근](data-flow-expression-functions.md#round) | 지정된 선택적 소수 자릿수와 선택적 반올림 모드를 사용하여 숫자를 반올림합니다. 소수 자릿수를 생략하면 기본 자릿수 0이 사용됩니다. 반올림 모드를 생략하면 기본적으로 ROUND_HALF_UP(5)이 사용됩니다. 반올림 값은 다음과 같습니다.|
| [rpad](data-flow-expression-functions.md#rpad) | 특정 길이가 될 때까지 제공된 패딩으로 문자열의 오른쪽 여백을 채웁니다. 문자열이 길이보다 크거나 같은 경우 길이에 맞게 잘립니다.  |
| [rtrim](data-flow-expression-functions.md#rtrim) | 오른쪽의 후행 문자열을 삭제합니다. 두 번째 매개 변수를 지정하지 않으면 공백을 삭제합니다. 그렇지 않은 경우 두 번째 매개 변수에 지정된 모든 문자를 삭제합니다.  |
| [second](data-flow-expression-functions.md#second) | 날짜의 초 값을 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 현지 표준 시간대가 기본적으로 사용됩니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [초](data-flow-expression-functions.md#seconds) | 기간(초)의 시간(밀리초).  |
| [sha1](data-flow-expression-functions.md#sha1) | 다양한 기본 데이터 형식의 열 세트에서 SHA-1 다이제스트를 계산하고 40자의 16진수 문자열을 반환합니다. 행의 지문을 계산하는 데 사용할 수 있습니다.  |
| [sha2](data-flow-expression-functions.md#sha2) | 0(256), 224, 256, 384, 512 값의 지정된 비트 길이를 갖는 다양한 기본 데이터 형식의 열 세트에서 SHA-2 다이제스트를 계산합니다. 행의 지문을 계산하는 데 사용할 수 있습니다.  |
| [사인](data-flow-expression-functions.md#sin) | 사인 값을 계산합니다.  |
| [sinh](data-flow-expression-functions.md#sinh) | 쌍곡선 사인 값을 계산합니다.  |
| [soundex](data-flow-expression-functions.md#soundex) | 문자열의 ```soundex``` 코드를 가져옵니다.  |
| [split](data-flow-expression-functions.md#split) | 구분 기호를 기준으로 문자열을 분할하고 문자열의 배열을 반환합니다.  |
| [sqrt](data-flow-expression-functions.md#sqrt) | 숫자의 제곱근을 계산합니다.  |
| [startsWith](data-flow-expression-functions.md#startsWith) | 문자열이 제공된 문자열로 시작하는지 확인합니다.  |
| [subDays](data-flow-expression-functions.md#subDays) | 날짜 또는 타임스탬프에서 일을 뺍니다. 날짜에 대한 - 연산자와 같습니다.  |
| [subMonths](data-flow-expression-functions.md#subMonths) | 날짜 또는 타임스탬프에서 월을 뺍니다.  |
| [substring](data-flow-expression-functions.md#substring) | 특정 위치에서 특정 길이의 부분 문자열을 추출합니다. 위치는 1부터 시작합니다. 길이를 생략하면 기본적으로 문자열 끝까지입니다.  |
| [tan](data-flow-expression-functions.md#tan) | 탄젠트 값을 계산합니다.  |
| [tanh](data-flow-expression-functions.md#tanh) | 쌍곡선 탄젠트 값을 계산합니다.  |
| [번역하기](data-flow-expression-functions.md#translate) | 하나의 문자 세트를 문자열의 다른 문자 세트로 바꿉니다. 문자는 1:1로 대체됩니다.  |
| [trim](data-flow-expression-functions.md#trim) | 선행 및 후행 문자열을 삭제합니다. 두 번째 매개 변수를 지정하지 않으면 공백을 삭제합니다. 그렇지 않은 경우 두 번째 매개 변수에 지정된 모든 문자를 삭제합니다.  |
| [true](data-flow-expression-functions.md#true) | 항상 true 값을 반환합니다. 'true'라는 열이 있는 경우 함수 `syntax(true())`를 사용합니다.  |
| [typeMatch](data-flow-expression-functions.md#typeMatch) | 열의 형식과 일치합니다. 패턴 식에서만 사용할 수 있습니다. number는 short, integer, long, double, float 또는 decimal과 일치하고, integral은 short, integer, long과 일치하고, fractional은 double, float, decimal과 일치하고, datetime은 date 또는 timestamp 형식과 일치합니다.  |
| [unescape](data-flow-expression-functions.md#unescape) | 형식에 따라 문자열을 이스케이프 해제합니다. 허용되는 형식의 리터럴 값은 'json', 'xml', 'ecmascript', 'html', 'java'입니다.|
| [상단](data-flow-expression-functions.md#upper) | 문자열을 대문자로 바꿉니다.  |
| [uuid](data-flow-expression-functions.md#uuid) | 생성된 UUID를 반환합니다.  |
| [weekOfYear](data-flow-expression-functions.md#weekOfYear) | 지정된 날짜의 연중 주를 가져옵니다.  |
| [주](data-flow-expression-functions.md#weeks) | 기간(주)의 시간(밀리초).  |
| [Xor](data-flow-expression-functions.md#xor) | 논리 XOR 연산자입니다. ^ 연산자와 같습니다.  |
| [year](data-flow-expression-functions.md#year) | 날짜의 연도 값을 구합니다.  |
|||

## <a name="aggregate-functions"></a>집계 함수
다음 함수는 집계, 피벗, 피벗 해제 및 창 변환에서만 사용할 수 있습니다.

| 집계 함수 | 작업 |
|----|----|
| [approxDistinctCount](data-flow-expression-functions.md#approxDistinctCount) | 열에 대한 고유 값의 대략적인 집계 수를 가져옵니다. 선택적 두 번째 매개 변수는 예측 오류를 제어하는 것입니다.|
| [평균](data-flow-expression-functions.md#avg) | 열 값의 평균을 구합니다.  |
| [avgIf](data-flow-expression-functions.md#avgIf) | 조건에 따라 열 값의 평균을 구합니다.  |
| [수집](data-flow-expression-functions.md#collect) | 집계 그룹에 있는 식의 모든 값을 배열로 수집합니다. 이 프로세스 중에 구조를 수집하고 대체 구조로 변환할 수 있습니다. 항목 수는 해당 그룹의 행 수와 동일하며 Null 값을 포함할 수 있습니다. 수집된 항목 수는 적어야 합니다.  |
| [count](data-flow-expression-functions.md#count) | 값의 집계 개수를 가져옵니다. 선택적 열을 지정하면 개수에서 Null 값을 무시합니다.  |
| [countDistinct](data-flow-expression-functions.md#countDistinct) | 열 세트의 고유 값에 대한 집계 합계를 구합니다.  |
| [Countif](data-flow-expression-functions.md#countIf) | 조건에 따라 값의 집계 개수를 가져옵니다. 선택적 열을 지정하면 개수에서 Null 값을 무시합니다.  |
| [covariancePopulation](data-flow-expression-functions.md#covariancePopulation) | 두 열 간의 모집단 공 분산을 구합니다.  |
| [covariancePopulationIf](data-flow-expression-functions.md#covariancePopulationIf) | 조건에 따라 두 열의 모집단 공 분산을 구합니다.  |
| [covarianceSample](data-flow-expression-functions.md#covarianceSample) | 두 열의 샘플 공 분산을 구합니다.  |
| [covarianceSampleIf](data-flow-expression-functions.md#covarianceSampleIf) | 조건에 따라 두 열의 샘플 공 분산을 구합니다.  |
| [first](data-flow-expression-functions.md#first) | 열 그룹의 첫 번째 값을 가져옵니다. 두 번째 매개 변수 ignoreNulls를 생략하면 false로 간주됩니다.  |
| [isDistinct](data-flow-expression-functions.md#isDistinct) | 열 또는 열 집합이 고유한지 확인합니다. null은 고유 값으로 계산하지 않습니다. |
| [첨도](data-flow-expression-functions.md#kurtosis) | 열의 첨도를 구합니다.  |
| [2018년 10월](data-flow-expression-functions.md#kurtosisIf) | 조건에 따라 열의 첨도를 구합니다.  |
| [last](data-flow-expression-functions.md#last) | 열 그룹의 마지막 값을 가져옵니다. 두 번째 매개 변수 ignoreNulls를 생략하면 false로 간주됩니다.  |
| [max](data-flow-expression-functions.md#max) | 열의 최댓값을 구합니다.  |
| [maxIf](data-flow-expression-functions.md#maxIf) | 조건에 따라 열의 최댓값을 구합니다.  |
| [평균](data-flow-expression-functions.md#mean) | 열 값의 평균값을 가져옵니다. AVG와 같습니다.  |
| [meanIf](data-flow-expression-functions.md#meanIf) | 조건에 따라 열 값의 평균값을 가져옵니다. avgIf와 같습니다.  |
| [min](data-flow-expression-functions.md#min) | 열의 최솟값을 구합니다.  |
| [minIf](data-flow-expression-functions.md#minIf) | 조건에 따라 열의 최솟값을 구합니다.  |
| [기울이기](data-flow-expression-functions.md#skewness) | 열의 왜도를 구합니다.  |
| [skewnessIf](data-flow-expression-functions.md#skewnessIf) | 조건에 따라 열의 왜도를 구합니다.  |
| [stddev](data-flow-expression-functions.md#stddev) | 열의 표준 편차를 구합니다.  |
| [stddevIf](data-flow-expression-functions.md#stddevIf) | 조건에 따라 열의 표준 편차를 구합니다.  |
| [stddevPopulation](data-flow-expression-functions.md#stddevPopulation) | 열의 모집단 표준 편차를 구합니다.  |
| [stddevPopulationIf](data-flow-expression-functions.md#stddevPopulationIf) | 조건에 따라 열의 모집단 표준 편차를 구합니다.  |
| [stddevSample](data-flow-expression-functions.md#stddevSample) | 열의 샘플 표준 편차를 구합니다.  |
| [stddevSampleIf](data-flow-expression-functions.md#stddevSampleIf) | 조건에 따라 열의 샘플 표준 편차를 구합니다.  |
| [sum](data-flow-expression-functions.md#sum) | 숫자 열의 집계 합계를 구합니다.  |
| [sumDistinct](data-flow-expression-functions.md#sumDistinct) | 숫자 열의 고유 값에 대한 집계 합계를 구합니다.  |
| [sumDistinctIf](data-flow-expression-functions.md#sumDistinctIf) | 기준에 따라 숫자 열의 집계 합계를 가져옵니다. 조건의 기준은 어떤 열도 될 수 있습니다.  |
| [sumIf](data-flow-expression-functions.md#sumIf) | 기준에 따라 숫자 열의 집계 합계를 가져옵니다. 조건의 기준은 어떤 열도 될 수 있습니다.  |
| [반대로](data-flow-expression-functions.md#variance) | 열의 분산을 구합니다.  |
| [Varif](data-flow-expression-functions.md#varianceIf) | 조건에 따라 열의 분산을 구합니다.  |
| [variancePopulation](data-flow-expression-functions.md#variancePopulation) | 열의 모집단 분산을 구합니다.  |
| [Varepopulationif](data-flow-expression-functions.md#variancePopulationIf) | 조건에 따라 열의 모집단 분산을 구합니다.  |
| [varianceSample](data-flow-expression-functions.md#varianceSample) | 열의 불편 분산을 구합니다.  |
| [varianceSampleIf](data-flow-expression-functions.md#varianceSampleIf) | 조건에 따라 열의 불편 분산을 구합니다.  |
|||

## <a name="array-functions"></a>배열 함수
배열 함수는 배열 데이터 구조에 대한 변환을 수행합니다. 여기에는 배열 요소 및 인덱스를 처리하기 위한 특수 키워드가 포함됩니다.

* ```#acc```는 배열을 줄일 때 단일 출력에 포함하려는 값을 나타냅니다.
* ```#index```는 배열 인덱스 번호 ```#index2, #index3 ...```와 함께 현재 배열 인덱스를 나타냅니다.
* ```#item```은 배열의 현재 요소 값을 나타냅니다.

| Array 함수 | Task |
|----|----|
| [array](data-flow-expression-functions.md#array) | 항목의 배열을 만듭니다. 모든 항목은 동일한 형식이어야 합니다. 항목을 지정하지 않으면 빈 문자열 배열이 기본값으로 지정됩니다. [] 생성 연산자와 동일합니다.  |
| [at](data-flow-expression-functions.md#at) | 배열 인덱스에서 요소를 찾습니다. 인덱스는 1부터 시작합니다. 인덱스가 범위를 벗어나면 null 값이 발생합니다. 키가 지정된 맵에서 값을 찾습니다. 키를 찾을 수 없으면 null이 반환됩니다.|
| [contains](data-flow-expression-functions.md#contains) | 제공된 배열의 요소가 제공된 조건자에서 true로 평가되면 true를 반환합니다. Contains는 조건자 함수의 한 요소에 대한 참조를 #item으로 예상합니다.  |
| [distinct](data-flow-expression-functions.md#distinct) | 배열의 고유 항목 집합을 반환합니다.|
| [남기고](data-flow-expression-functions.md#except) | 삭제하는 다른 중복 항목에서 한 배열의 차이 집합을 반환합니다.|
| [filter](data-flow-expression-functions.md#filter) | 제공된 조건자를 충족하지 않는 배열에서 요소를 필터링합니다. 필터는 조건자 함수의 한 요소에 대한 참조를 #item으로 예상합니다.  |
| [find](data-flow-expression-functions.md#find) | 배열에서 조건과 일치하는 첫 번째 항목을 찾습니다. 배열 항목을 #item으로 지정할 수 있는 필터 함수가 필요합니다. 깊게 중첩된 맵의 경우 #item_n(# item_1, # item_2 ...) 표기법을 사용하여 상위 맵을 참조할 수 있습니다.  |
| [flatten](data-flow-expression-functions.md#flatten) | 배열이 나 배열을 단일 배열로 평면화 합니다. 원자성 항목의 배열은 변경되지 않은 상태로 반환됩니다. 마지막 인수는 선택 사항이 며, 두 수준 이상으로 재귀적으로 평면화 하려면 false로 기본값을 설정 합니다.|
| [in](data-flow-expression-functions.md#in) | 항목이 배열에 있는지 여부를 확인 합니다.  |
| [intersect](data-flow-expression-functions.md#intersect) | 두 배열에서 고유 항목의 교집합 집합을 반환 합니다.|
| [map](data-flow-expression-functions.md#map) | 제공된 식을 사용하여 배열의 각 요소를 새 요소에 매핑합니다. Map에는 #item 식 함수에서 하나의 요소에 대 한 참조가 필요 합니다.  |
| [mapIf](data-flow-expression-functions.md#mapIf) | 배열이 동일 하거나 더 작은 다른 배열에 조건부로 매핑됩니다. 값은 structTypes을 포함한 모든 데이터 형식이 될 수 있습니다. 배열의 항목을 #item으로, 현재 인덱스를 #index로 지정할 수 있는 매핑 함수를 사용합니다. 깊게 중첩 된 맵의 경우 표기법을 사용 하 여 부모 맵을 참조할 수 있습니다 ``#item_[n](#item_1, #index_1...)`` .|
| [mapIndex](data-flow-expression-functions.md#mapIndex) | 제공된 식을 사용하여 배열의 각 요소를 새 요소에 매핑합니다. Map에는 #item 식 함수에서 하나의 요소에 대 한 참조와 #index로 요소 인덱스에 대 한 참조가 필요 합니다.  |
| [mapLoop](data-flow-expression-functions.md#mapLoop) | 1부터 길이 까지의 루프를 반복 하 여 해당 길이의 배열을 만듭니다. 배열의 인덱스를 #index로 지정할 수 있는 매핑 함수를 사용합니다. 깊게 중첩 된 맵의 경우 #index_n (#index_1, #index_2 ...) 표기법을 사용 하 여 부모 맵을 참조할 수 있습니다.|
| [줄이십시오](data-flow-expression-functions.md#reduce) | 배열에 요소를 누적합니다. 줄이기를 사용 하면 첫 번째 식 함수에서 누적기 및 one 요소에 대 한 참조가 #acc 및 #item으로 예상 되며 결과 값이 두 번째 식 함수에서 사용 될 #result으로 예상 됩니다.  |
| [size](data-flow-expression-functions.md#size) | 배열 또는 지도 형식의 크기를 찾습니다.  |
| [조각](data-flow-expression-functions.md#slice) | 특정 위치에서 배열의 하위 세트를 추출합니다. 위치는 1부터 시작합니다. 길이를 생략하면 기본적으로 문자열 끝까지입니다.  |
| [sort](data-flow-expression-functions.md#sort) | 제공된 조건자 함수를 사용하여 배열을 정렬합니다. Sort는 #item1 및 #item2 식 함수에서 두 개의 연속 된 요소에 대 한 참조가 필요 합니다.  |
| [펼침](data-flow-expression-functions.md#unfold) | 배열을 행 집합으로 펼칩니다 하 고 모든 행의 나머지 열에 대해 값을 반복 합니다.|
| [union](data-flow-expression-functions.md#union) | 2 개의 배열에서 고유한 항목의 union 집합을 반환 합니다.|
|||

## <a name="cached-lookup-functions"></a>캐시된 조회 함수
다음 함수는 캐시된 싱크를 포함한 경우 캐시된 조회를 사용하는 경우에만 사용할 수 있습니다.

| Cached 조회 함수 | 작업 |
|----|----|
| [lookup](data-flow-expression-functions.md#lookup) | 캐시된 싱크의 키와 일치하는 지정된 키를 사용하여 캐시된 싱크에서 첫 번째 행을 조회합니다.|
| [mlookup](data-flow-expression-functions.md#mlookup) | 캐시된 싱크의 키와 일치하는 지정된 키를 사용하여 캐시된 싱크에서 일치하는 모든 행을 조회합니다.|
| [output](data-flow-expression-functions.md#output) | 캐시 싱크 결과의 첫 번째 행을 반환합니다. |
| [outputs](data-flow-expression-functions.md#outputs) | 캐시 싱크 결과의 전체 출력 행 세트를 반환합니다. |
|||

## <a name="conversion-functions"></a>변환 함수

변환 함수는 데이터를 변환하고 데이터 형식에 대해 테스트하는 데 사용됩니다.

| 변환 함수 | Task |
|----|----|
| [isBitSet](data-flow-expression-functions.md#isBitSet) | 이 비트 세트 에 비트 위치가 설정되어 있는지 확인합니다.|
| [setBitSet](data-flow-expression-functions.md#setBitSet) | 이 비트 세트 에 비트 위치 설정|
| [isBoolean](data-flow-expression-functions.md#isBoolean) | 문자열 값이 ``toBoolean()`` 규칙에 따라 부울 값인지 확인합니다.|
| [isByte](data-flow-expression-functions.md#isByte) | 문자열 값이 ``toByte()`` 규칙에 따라 지정된 선택적 형식의 바이트 값인지 확인합니다.|
| [isDate](data-flow-expression-functions.md#isDate) | 입력 날짜 문자열이 선택적 입력 날짜 형식을 사용하는 날짜인지 확인합니다. 사용 가능한 형식을 알아보려면 Java의 SimpleDateFormat을 참조하세요. 입력 날짜 형식이 생략된 경우 기본 형식은 ``yyyy-[M]M-[d]d``입니다. 허용되는 형식은 ``[ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ]``입니다.|
| [isShort](data-flow-expression-functions.md#isShort) | 문자열 값이 ``toShort()`` 규칙에 따라 지정된 선택적 형식의 short 값인지 검사합니다.|
| [isInteger](data-flow-expression-functions.md#isInteger) | 문자열 값이 ``toInteger()`` 규칙에 따라 지정된 선택적 형식의 integer 값인지 검사합니다.|
| [isLong](data-flow-expression-functions.md#isLong) | 문자열 값이 ``toLong()`` 규칙에 따라 지정된 선택적 형식의 long 값인지 검사합니다.|
| [isNan](data-flow-expression-functions.md#isNan) | 숫자가 아닌지 확인합니다.|
| [isFloat](data-flow-expression-functions.md#isFloat) | 문자열 값이 ``toFloat()`` 규칙에 따라 지정된 선택적 형식의 float 값인지 검사합니다.|
| [isDouble](data-flow-expression-functions.md#isDouble) | 문자열 값이 ``toDouble()`` 규칙에 따라 지정된 선택적 형식의 double 값인지 검사합니다.|
| [isDecimal](data-flow-expression-functions.md#isDecimal) | 문자열 값이 ``toDecimal()`` 규칙에 따라 지정된 선택적 형식의 decimal 값인지 검사합니다.|
| [isTimestamp](data-flow-expression-functions.md#isTimestamp) | 입력 날짜 문자열이 선택적 입력 타임스탬프 형식을 사용하는 타임스탬프인지 확인합니다. 사용 가능한 형식은 Java의 SimpleDateFormat을 참조하세요. 타임스탬프를 생략하면 기본 패턴인 ``yyyy-[M]M-[d]d hh:mm:ss[.f...]``가 사용됩니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 타임스탬프는 999 값으로 최대 밀리 초 정확도를 지원합니다. 사용 가능한 형식은 Java의 SimpleDateFormat을 참조하세요.|
| [toBase64](data-flow-expression-functions.md#toBase64) | base64에서 지정된 문자열을 인코딩합니다.  |
| [toBinary](data-flow-expression-functions.md#toBinary) | 숫자/날짜/타임스탬프/문자열을 이진 표현으로 변환합니다.  |
| [toBoolean](data-flow-expression-functions.md#toBoolean) | ('t', 'true', 'y', 'yes', '1') 값을 true로 변환하고 ('f', 'false', 'n', 'no', '0') 값을 false로 변환하고, 다른 모든 값은 Null로 변환합니다.  |
| [toByte](data-flow-expression-functions.md#toByte) | 숫자 또는 문자열을 바이트 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다.  |
| [toDate](data-flow-expression-functions.md#toDate) | 선택적 입력 날짜 형식을 사용하여 입력 날짜 문자열을 날짜로 변환합니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. 입력 날짜 형식이 생략된 경우 기본 형식은 yyyy-[M]M-[d]d입니다. 허용되는 형식은 [ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ]입니다.  |
| [toDecimal](data-flow-expression-functions.md#toDecimal) | 숫자 또는 문자열을 10진수 값으로 변환합니다. 자릿수와 소수 자릿수를 지정하지 않으면 기본값인 (10,2)가 사용됩니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. en-US, de, zh-CN과 같은 BCP47 언어 형식의 선택적 로캘 형식입니다.  |
| [toDouble](data-flow-expression-functions.md#toDouble) | 숫자 또는 문자열을 배정도 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. en-US, de, zh-CN과 같은 BCP47 언어 형식의 선택적 로캘 형식입니다.  |
| [toFloat](data-flow-expression-functions.md#toFloat) | 숫자 또는 문자열을 부동 소수점 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. double을 자릅니다.  |
| [toInteger](data-flow-expression-functions.md#toInteger) | 숫자 또는 문자열을 정수 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. long, float, double을 자릅니다.  |
| [toLong](data-flow-expression-functions.md#toLong) | 숫자 또는 문자열을 긴 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. float, double을 자릅니다.  |
| [toShort](data-flow-expression-functions.md#toShort) | 숫자 또는 문자열을 짧은 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. integer, long, float, double을 자릅니다.  |
| [toString](data-flow-expression-functions.md#toString) | 기본 데이터 형식을 문자열로 변환합니다. 숫자 및 날짜의 경우 형식을 지정할 수 있습니다. 지정하지 않으면 시스템 기본값은 picked.Java입니다. 숫자에는 10진수 형식이 사용됩니다. 가능한 모든 날짜 형식은 Java SimpleDateFormat을 참조하세요. 기본 형식은 yyyy-MM-dd입니다.  |
| [toTimestamp](data-flow-expression-functions.md#toTimestamp) | 지정된 선택적 타임스탬프 형식으로 문자열을 타임스탬프로 변환합니다. 타임스탬프를 생략하면 기본 패턴인 yyyy-[M]M-[d]d hh:mm:ss[.f...]가 사용됩니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 타임 스탬프는 999 값으로 최대 밀리 초 정확도를 지원합니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [toUTC](data-flow-expression-functions.md#toUTC) | 타임스탬프를 UTC로 변환합니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 기본적으로 현재 표준 시간대가 사용됩니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
|||

## <a name="map-functions"></a>Map 함수

  Map 함수는 맵 데이터 형식에 대한 작업을 수행합니다.

| Map 함수 | 작업 |
|----|----|
| [연결](data-flow-expression-functions.md#associate) | 키/값의 맵을 만듭니다. 모든 키 및 값은 동일한 형식이어야 합니다. 항목을 지정하지 않으면 문자열 형식에 대한 문자열 맵으로 기본 설정됩니다. ```[ -> ]``` 생성 연산자와 동일합니다. 키와 값은 서로 대체되어야 합니다.|
| [keyValues](data-flow-expression-functions.md#keyValues) | 키/값의 맵을 만듭니다. 첫 번째 매개 변수는 키의 배열이고 두 번째 매개 변수는 값의 배열입니다. 두 배열의 길이는 같아야 합니다.|
| [mapAssociation](data-flow-expression-functions.md#mapAssociation) | 키를 새 값에 연결하여 맵을 변환합니다. 배열을 반환합니다. 항목을 #key로, 현재 값을 #value로 지정할 수 있는 매핑 함수를 사용합니다. |
| [시점](data-flow-expression-functions.md#reassociate) | 키를 새 값에 연결하여 맵을 변환합니다. 항목을 #key로, 현재 값을 #value로 지정할 수 있는 매핑 함수를 사용합니다.  |
|||

## <a name="metafunctions"></a>Metafunctions

Metafunctions는 주로 데이터 흐름의 메타데이터에서 작동합니다.

| Metafunction  | 작업 |
|----|----|
| [byItem](data-flow-expression-functions.md#byItem) | 구조체 또는 구조체 배열 내에서 하위 항목을 찾습니다. 일치 항목이 여러 개인 경우 첫 번째 일치 항목이 반환됩니다. 일치하는 항목이 없으면 Null 값을 반환합니다. 반환된 값은 형식 변환 작업 중 하나에 의해 변환된 형식이어야 합니다(? 날짜, ? 문자열 ...). 디자인 타임에 알려진 열 이름은 해당 이름으로만 지정되어야 합니다. 계산된 입력은 지원되지 않지만 매개 변수 대체를 사용할 수 있습니다.   |
| [byOrigin](data-flow-expression-functions.md#byOrigin) | 원본 스트림에서 이름별 열 값을 선택합니다. 두 번째 인수는 원본 스트림 이름입니다. 일치하는 항목이 여러 개 있는 경우 첫 번째 일치 항목을 반환합니다. 일치하는 항목이 없으면 Null 값을 반환합니다. 반환된 값은 형식 변환 함수 중 하나에 의해 변환된 형식이어야 합니다(TO_DATE, TO_STRING ...). 디자인 타임에 알려진 열 이름은 해당 이름으로만 지정되어야 합니다. 계산된 입력은 지원되지 않지만 매개 변수 대체를 사용할 수 있습니다.  |
| [byOrigins](data-flow-expression-functions.md#byOrigins) | 스트림에서 이름별 열 배열을 선택합니다. 두 번째 인수는 인수가 시작된 스트림입니다. 일치하는 항목이 여러 개 있는 경우 첫 번째 일치 항목을 반환합니다. 일치하는 항목이 없으면 Null 값을 반환합니다. 반환된 값은 형식 변환 함수 중 하나에 의해 변환된 형식이어야 합니다(TO_DATE, TO_STRING ...). 디자인 타임에 알려진 열 이름은 해당 이름으로만 지정되어야 합니다. 계산된 입력은 지원되지 않지만 매개 변수 대체를 사용할 수 있습니다.|
| [byName](data-flow-expression-functions.md#byName) | 스트림에서 이름을 기준으로 열 값을 선택합니다. 선택적 스트림 이름을 두 번째 인수로 전달할 수 있습니다. 일치하는 항목이 여러 개 있는 경우 첫 번째 일치 항목을 반환합니다. 일치하는 항목이 없으면 Null 값을 반환합니다. 반환된 값은 형식 변환 함수(TO_DATE, TO_STRING ...) 중 하나를 통해 유형이 변환되어야 합니다.  디자인 타임에 알려진 열 이름은 해당 이름으로만 처리되어야 합니다. 계산된 입력은 지원되지 않지만 매개 변수 대체를 사용할 수 있습니다.  |
| [byNames](data-flow-expression-functions.md#byNames) | 스트림에서 이름을 기준으로 열 배열을 선택합니다. 선택적 스트림 이름을 두 번째 인수로 전달할 수 있습니다. 일치하는 항목이 여러 개 있는 경우 첫 번째 일치 항목을 반환합니다. 열에 일치하는 항목이 없는 경우 전체 출력은 Null 값입니다. 반환된 값에는 형식 변환 함수(toDate, toString, ...)가 필요합니다.  디자인 타임에 알려진 열 이름은 해당 이름으로만 처리되어야 합니다. 계산된 입력은 지원되지 않지만 매개 변수 대체를 사용할 수 있습니다.|
| [byPath](data-flow-expression-functions.md#byPath) | 스트림에서 이름별 계층 경로를 찾습니다. 선택적 스트림 이름을 두 번째 인수로 전달할 수 있습니다. 이러한 경로를 찾을 수 없으면 null을 반환합니다. 디자인 타임에 알려진 열 이름/경로는 이름 또는 점 표기법 경로로만 지정해야 합니다. 계산된 입력은 지원되지 않지만 매개 변수 대체를 사용할 수 있습니다.  |
| [byPosition](data-flow-expression-functions.md#byPosition) | 스트림에서 상대 위치만큼(1부터 시작) 열 값을 선택합니다. 위치가 범위를 벗어나는 경우 Null 값을 반환합니다. 반환된 값은 형식 변환 함수 중 하나에 의해 변환된 형식이어야 합니다(TO_DATE, TO_STRING ...). 컴퓨팅된 입력은 지원되지 않지만 매개 변수 대체를 사용할 수 있습니다.  |
| [hasPath](data-flow-expression-functions.md#hasPath) | 특정 계층 경로가 스트림에 이름별로 존재하는지 확인합니다. 선택적 스트림 이름을 두 번째 인수로 전달할 수 있습니다. 디자인 타임에 알려진 열 이름/경로는 이름 또는 점 표기법 경로로만 지정해야 합니다. 계산된 입력은 지원되지 않지만 매개 변수 대체를 사용할 수 있습니다.  |
| [originColumns](data-flow-expression-functions.md#originColumns) | 열을 만든 원본 스트림에 대한 모든 출력 열을 가져옵니다. 다른 함수로 묶어야 합니다.|
| [16 진수](data-flow-expression-functions.md#hex) | 이진 값를 16진 문자열 표현으로 반환합니다. |
| [unhex](data-flow-expression-functions.md#unhex) | 16진 문자열 표현을 이진 값으로 표시합니다. 이는 sha2, md5와 함께 사용하여 문자열을 이진 표현으로 변환할 수 있습니다. |
|||

## <a name="window-functions"></a>창 함수

다음 함수는 창 변환에서만 사용할 수 있습니다.

| Windows 함수 | 작업 |
|----|----|
| [cumeDist](data-flow-expression-functions.md#cumeDist) | CumeDist 함수는 파티션의 모든 값을 기준으로 값의 위치를 계산합니다. 결과는 파티션 순서에서 현재 행보다 앞에 있거나 같은 위치에 있는 행 수를 창 파티션의 총 행 수로 나눈 값입니다. 같은 순서의 값은 같은 위치로 계산됩니다.  |
| [denseRank](data-flow-expression-functions.md#denseRank) | 지정된 값 그룹에서 값의 순위는 절에 따른 창 순서로 계산됩니다. 결과는 파티션 순서에서 현재 행보다 앞에 있거나 같은 위치에 있는 행의 수에 1을 더한 것입니다. 값은 시퀀스에 간격을 생성하지 않습니다. Dense Rank는 데이터가 정렬되지 않은 경우에도 작동하며 값의 변화를 찾습니다.  |
| [la](data-flow-expression-functions.md#lag) | 현재 행보다 n개 행 전에 평가되는 첫 번째 매개 변수의 값을 가져옵니다. 두 번째 매개 변수는 거꾸로 확인할 행의 수이며 기본값은 1입니다. 기본값을 지정하지 않은 경우, 행이 많지 않으면 Null 값이 반환됩니다.  |
| [리드](data-flow-expression-functions.md#lead) | 현재 행보다 n개 행 뒤에 평가되는 첫 번째 매개 변수의 값을 가져옵니다. 두 번째 매개 변수는 앞으로 조회할 행의 수이며 기본값은 1입니다. 기본값을 지정하지 않은 경우, 행이 많지 않으면 Null 값이 반환됩니다.  |
| [nTile](data-flow-expression-functions.md#nTile) | ```NTile``` 함수는 각 창 파티션의 행을 1에서 최대 `n` 사이의 `n`개 버킷으로 나눕니다. 버킷 값 차이는 최대 1 이내입니다. 파티션의 행 수가 버킷 수로 균일하게 나누어 떨어지지 않으면 나머지 값은 첫 번째 버킷부터 시작해서 하나씩 분산됩니다. ```NTile``` 함수는 ```tertiles```, 사분위수, 십진수 및 기타 일반적인 요약 통계를 계산하는 데 유용합니다. 이 함수는 초기화하는 동안 두 개의 변수를 계산합니다. 일반 버킷의 크기에는 하나의 행이 더 추가됩니다. 두 변수는 현재 파티션의 크기를 기준으로 합니다. 계산 프로세스 동안 이 함수는 현재 행 번호, 현재 버킷 수 및 버킷이 변경되는 행 번호(bucketThreshold)를 추적합니다. 현재 행 번호가 버킷 임계값에 도달하면 버킷 값이 1씩 커지고, 임계값은 버킷 크기만큼 커집니다(현재 버킷이 채워질 경우 하나씩 추가).  |
| [배열](data-flow-expression-functions.md#rank) | 지정된 값 그룹에서 값의 순위는 절에 따른 창 순서로 계산됩니다. 결과는 파티션 순서에서 현재 행보다 앞에 있거나 같은 위치에 있는 행의 수에 1을 더한 것입니다. 값은 시퀀스에 간격을 생성합니다. Rank는 데이터가 정렬되지 않은 경우에도 작동하며 값의 변화를 찾습니다.  |
| [rowNumber](data-flow-expression-functions.md#rowNumber) | 창에서 행에 대해 순차적으로 1부터 번호를 매깁니다.  |
|||

## <a name="alphabetical-listing-of-all-functions"></a>모든 함수의 사전순 목록

다음은 데이터 흐름 매핑에서 사용할 수 있는 모든 함수의 사전순 목록입니다.

<a name="abs" ></a>

### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
숫자의 절대값입니다.  
* ``abs(-20) -> 20``  
* ``abs(10) -> 10``  
___   


<a name="acos" ></a>

### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
역코사인 값을 계산합니다.  
* ``acos(1) -> 0.0``  
___


<a name="add" ></a>

### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
문자열 또는 숫자의 쌍을 추가합니다. 날짜에 일 수를 추가합니다. 타임스탬프에 기간을 추가합니다. 유사한 형식의 한 배열을 다른 배열에 추가합니다. \+ 연산자와 같습니다.  
* ``add(10, 20) -> 30``  
* ``10 + 20 -> 30``  
* ``add('ice', 'cream') -> 'icecream'``  
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``  
* ``add(toDate('2012-12-12'), 3) -> toDate('2012-12-15')``  
* ``toDate('2012-12-12') + 3 -> toDate('2012-12-15')``  
* ``[10, 20] + [30, 40] -> [10, 20, 30, 40]``  
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``  
___


<a name="addDays" ></a>

### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
날짜 또는 타임스탬프에 일을 추가합니다. 날짜에 대한 + 연산자와 같습니다.  
* ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')``  
___


<a name="addMonths" ></a>

### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral, [<i>&lt;value3&gt;</i> : string]) => datetime</b></code><br/><br/>
날짜 또는 타임스탬프에 월을 추가합니다. 필요에 따라 표준 시간대를 전달할 수 있습니다.  
* ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')``  
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> toTimestamp('2016-08-31 10:10:10')``  
___


<a name="and" ></a>

### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
논리적 AND 연산자입니다. &&과 같습니다.  
* ``and(true, false) -> false``  
* ``true && false -> false``  
___


<a name="approxDistinctCount" ></a>

### <code>approxDistinctCount</code>
<code><b>approxDistinctCount(<i>&lt;value1&gt;</i> : any, [ <i>&lt;value2&gt;</i> : double ]) => long</b></code><br/><br/>
열에 대한 고유 값의 대략적인 집계 수를 가져옵니다. 선택적 두 번째 매개 변수는 예측 오류를 제어하는 것입니다.
* ``approxDistinctCount(ProductID, .05) => long``  
___


<a name="array" ></a>

### <code>array</code>
<code><b>array([<i>&lt;value1&gt;</i> : any], ...) => array</b></code><br/><br/>
항목의 배열을 만듭니다. 모든 항목은 동일한 형식이어야 합니다. 항목을 지정하지 않으면 빈 문자열 배열이 기본값으로 지정됩니다. [] 생성 연산자와 동일합니다.  
* ``array('Seattle', 'Washington')``
* ``['Seattle', 'Washington']``
* ``['Seattle', 'Washington'][1]``
* ``'Washington'``
___


<a name="asin" ></a>

### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
역사인 값을 계산합니다.  
* ``asin(0) -> 0.0``  
___


<a name="associate" ></a>

### <code>associate</code>
<code><b>reassociate(<i>&lt;value1&gt;</i> : map, <i>&lt;value2&gt;</i> : binaryFunction) => map</b></code><br/><br/>
키/값의 맵을 만듭니다. 모든 키 및 값은 동일한 형식이어야 합니다. 항목을 지정하지 않으면 문자열 형식에 대한 문자열 맵으로 기본 설정됩니다. ```[ -> ]``` 생성 연산자와 동일합니다. 키와 값은 서로 대체되어야 합니다.
*   ``associate('fruit', 'apple', 'vegetable', 'carrot' )=> ['fruit' -> 'apple', 'vegetable' -> 'carrot']``
___


<a name="at" ></a>

### <code>at</code>
<code><b>at(<i>&lt;value1&gt;</i> : array/map, <i>&lt;value2&gt;</i> : integer/key type) => array</b></code><br/><br/>
배열 인덱스에서 요소를 찾습니다. 인덱스는 1부터 시작합니다. 인덱스가 범위를 벗어나면 null 값이 발생합니다. 키가 지정된 맵에서 값을 찾습니다. 키를 찾을 수 없으면 null이 반환됩니다.
*   ``at(['apples', 'pears'], 1) => 'apples'``
*   ``at(['fruit' -> 'apples', 'vegetable' -> 'carrot'], 'fruit') => 'apples'``
___


<a name="atan" ></a>

### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
역탄젠트 값을 계산합니다.  
* ``atan(0) -> 0.0``  
___


<a name="atan2" ></a>

### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
평면의 양의 x축과 좌표로 지정된 점 사이의 라디안 각도를 반환합니다.  
* ``atan2(0, 0) -> 0.0``  
___


<a name="avg" ></a>

### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
열 값의 평균을 구합니다.  
* ``avg(sales)``  
___


<a name="avgIf" ></a>

### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
조건에 따라 열 값의 평균을 구합니다.  
* ``avgIf(region == 'West', sales)``  
___


<a name="between" ></a>

### <code>between</code>
<code><b>between(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : any) => boolean</b></code><br/><br/>
첫 번째 값이 다른 두 값 사이에 있는지 확인합니다. 숫자, 문자열 및 날짜/시간 값과 비교할 수 있습니다. * ``between(10, 5, 24)``
* ``true``
* ``between(currentDate(), currentDate() + 10, currentDate() + 20)``
* ``false``
___


<a name="bitwiseAnd" ></a>

### <code>bitwiseAnd</code>
<code><b>bitwiseAnd(<i>&lt;value1&gt;</i> : integral, <i>&lt;value2&gt;</i> : integral) => integral</b></code><br/><br/>
정수 형식의 비트 And 연산자. & 연산자와 같습니다. * ``bitwiseAnd(0xf4, 0xef)``
* ``0xe4``
* ``(0xf4 & 0xef)``
* ``0xe4``
___


<a name="bitwiseOr" ></a>

### <code>bitwiseOr</code>
<code><b>bitwiseOr(<i>&lt;value1&gt;</i> : integral, <i>&lt;value2&gt;</i> : integral) => integral</b></code><br/><br/>
정수 형식의 비트 Or 연산자. | 연산자와 같습니다. * ``bitwiseOr(0xf4, 0xef)``
* ``0xff``
* ``(0xf4 | 0xef)``
* ``0xff``
___


<a name="bitwiseXor" ></a>

### <code>bitwiseXor</code>
<code><b>bitwiseXor(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
정수 형식의 비트 Or 연산자. | 연산자와 같습니다. * ``bitwiseXor(0xf4, 0xef)``
* ``0x1b``
* ``(0xf4 ^ 0xef)``
* ``0x1b``
* ``(true ^ false)``
* ``true``
* ``(true ^ true)``
* ``false``
___


<a name="blake2b" ></a>

### <code>blake2b</code>
<code><b>blake2b(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
8과 512 사이에서 8의 배수만 될 수 있는 비트 길이가 주어진 다양한 기본 데이터 형식 열 세트의 Blake2 다이제스트를 계산합니다. 행의 지문을 계산하는 데 사용할 수 있습니다. * ``blake2b(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))``
* ``'c9521a5080d8da30dffb430c50ce253c345cc4c4effc315dab2162dac974711d'``
___


<a name="blake2bBinary" ></a>

### <code>blake2bBinary</code>
<code><b>blake2bBinary(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => binary</b></code><br/><br/>
8과 512 사이에서 8의 배수만 될 수 있는 비트 길이가 주어진 다양한 기본 데이터 형식 열 세트의 Blake2 다이제스트를 계산합니다. 행의 지문을 계산하는 데 사용할 수 있습니다. * ``blake2bBinary(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))``
* ``unHex('c9521a5080d8da30dffb430c50ce253c345cc4c4effc315dab2162dac974711d')``
___


<a name="byItem" ></a>

### <code>byItem</code>
<code><b>byItem(<i>&lt;parent column&gt;</i> : any, <i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
구조체 또는 구조체 배열 내에서 하위 항목을 찾습니다. 일치 항목이 여러 개인 경우 첫 번째 일치 항목이 반환됩니다. 일치하는 항목이 없으면 Null 값을 반환합니다. 반환된 값은 형식 변환 작업 중 하나에 의해 변환된 형식이어야 합니다(? 날짜, ? 문자열 ...). 디자인 타임에 알려진 열 이름은 해당 이름으로만 지정되어야 합니다. 계산된 입력은 지원되지 않지만 매개 변수 대체를 사용할 수 있습니다. * ``byItem( byName('customer'), 'orderItems') ? (itemName as string, itemQty as integer)``
* ``byItem( byItem( byName('customer'), 'orderItems'), 'itemName') ? string``
___


<a name="byName" ></a>

### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
스트림에서 이름을 기준으로 열 값을 선택합니다. 선택적 스트림 이름을 두 번째 인수로 전달할 수 있습니다. 일치하는 항목이 여러 개 있는 경우 첫 번째 일치 항목을 반환합니다. 일치하는 항목이 없으면 Null 값을 반환합니다. 반환된 값은 형식 변환 함수(TO_DATE, TO_STRING ...) 중 하나를 통해 유형이 변환되어야 합니다.  디자인 타임에 알려진 열 이름은 해당 이름으로만 처리되어야 합니다. 계산된 입력은 지원되지 않지만 매개 변수 대체를 사용할 수 있습니다.  
* ``toString(byName('parent'))``  
* ``toLong(byName('income'))``  
* ``toBoolean(byName('foster'))``  
* ``toLong(byName($debtCol))``  
* ``toString(byName('Bogus Column'))``  
* ``toString(byName('Bogus Column', 'DeriveStream'))``  
___


<a name="byNames" ></a>

### <code>byNames</code>
<code><b>byNames(<i>&lt;column names&gt;</i> : array, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
스트림에서 이름을 기준으로 열 배열을 선택합니다. 선택적 스트림 이름을 두 번째 인수로 전달할 수 있습니다. 일치하는 항목이 여러 개 있는 경우 첫 번째 일치 항목을 반환합니다. 열에 일치하는 항목이 없는 경우 전체 출력은 Null 값입니다. 반환된 값에는 형식 변환 함수(toDate, toString, ...)가 필요합니다.  디자인 타임에 알려진 열 이름은 해당 이름으로만 처리되어야 합니다. 계산된 입력은 지원되지 않지만 매개 변수 대체를 사용할 수 있습니다.
* ``toString(byNames(['parent', 'child']))``
* ``byNames(['parent']) ? string``
* ``toLong(byNames(['income']))``
* ``byNames(['income']) ? long``
* ``toBoolean(byNames(['foster']))``
* ``toLong(byNames($debtCols))``
* ``toString(byNames(['a Column']))``
* ``toString(byNames(['a Column'], 'DeriveStream'))``
* ``byNames(['orderItem']) ? (itemName as string, itemQty as integer)``
___


<a name="byOrigin" ></a>

### <code>byOrigin</code>
<code><b>byOrigin(<i>&lt;column name&gt;</i> : string, [<i>&lt;origin stream name&gt;</i> : string]) => any</b></code><br/><br/>
원본 스트림에서 이름별 열 값을 선택합니다. 두 번째 인수는 원본 스트림 이름입니다. 일치하는 항목이 여러 개 있는 경우 첫 번째 일치 항목을 반환합니다. 일치하는 항목이 없으면 Null 값을 반환합니다. 반환된 값은 형식 변환 함수 중 하나에 의해 변환된 형식이어야 합니다(TO_DATE, TO_STRING ...). 디자인 타임에 알려진 열 이름은 해당 이름으로만 지정되어야 합니다. 계산된 입력은 지원되지 않지만 매개 변수 대체를 사용할 수 있습니다.  
* ``toString(byOrigin('ancestor', 'ancestorStream'))``
___


<a name="byOrigins" ></a>

### <code>byOrigins</code>
<code><b>byOrigins(<i>&lt;column names&gt;</i> : array, [<i>&lt;origin stream name&gt;</i> : string]) => any</b></code><br/><br/>
스트림에서 이름별 열 배열을 선택합니다. 두 번째 인수는 인수가 시작된 스트림입니다. 일치하는 항목이 여러 개 있는 경우 첫 번째 일치 항목을 반환합니다. 일치하는 항목이 없으면 Null 값을 반환합니다. 반환된 값은 형식 변환 함수 중 하나에 의해 변환된 형식이어야 합니다(TO_DATE, TO_STRING ...). 디자인 타임에 알려진 열 이름은 해당 이름으로만 지정되어야 합니다. 계산된 입력은 지원되지 않지만 매개 변수 대체를 사용할 수 있습니다.
* ``toString(byOrigins(['ancestor1', 'ancestor2'], 'ancestorStream'))``
___


<a name="byPath" ></a>

### <code>byPath</code>
<code><b>byPath(<i>&lt;value1&gt;</i> : string, [<i>&lt;streamName&gt;</i> : string]) => any</b></code><br/><br/>
스트림에서 이름별 계층 경로를 찾습니다. 선택적 스트림 이름을 두 번째 인수로 전달할 수 있습니다. 이러한 경로를 찾을 수 없으면 null을 반환합니다. 디자인 타임에 알려진 열 이름/경로는 이름 또는 점 표기법 경로로만 지정해야 합니다. 계산된 입력은 지원되지 않지만 매개 변수 대체를 사용할 수 있습니다.  
* ``byPath('grandpa.parent.child') => column`` 
___


<a name="byPosition" ></a>

### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
스트림에서 상대 위치만큼(1부터 시작) 열 값을 선택합니다. 위치가 범위를 벗어나는 경우 Null 값을 반환합니다. 반환된 값은 형식 변환 함수 중 하나에 의해 변환된 형식이어야 합니다(TO_DATE, TO_STRING ...). 컴퓨팅된 입력은 지원되지 않지만 매개 변수 대체를 사용할 수 있습니다.  
* ``toString(byPosition(1))``  
* ``toDecimal(byPosition(2), 10, 2)``  
* ``toBoolean(byName(4))``  
* ``toString(byName($colName))``  
* ``toString(byPosition(1234))``  
___


<a name="case" ></a>

### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
교대로 나오는 조건에 따라 하나의 값 또는 다른 값이 적용됩니다. 입력 수가 짝수이면 마지막 조건에서 다른 값은 Null로 기본 설정됩니다.  
* ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``  
* ``case(10 + 20 == 25, 'bojjus', 'do' < 'go', 'gunchus') -> 'gunchus'``  
* ``isNull(case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus')) -> true``  
* ``case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus', 'dumbo') -> 'dumbo'``  
___


<a name="cbrt" ></a>

### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
숫자의 세제곱근을 계산합니다.  
* ``cbrt(8) -> 2.0``  
___


<a name="ceil" ></a>

### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
해당 수보다 작지 않은 가장 작은 정수를 반환합니다.  
* ``ceil(-0.1) -> 0``  
___


<a name="coalesce" ></a>

### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
입력에서 Null이 아닌 첫 번째 값을 반환합니다. 모든 입력은 동일한 형식이어야 합니다.  
* ``coalesce(10, 20) -> 10``  
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``  
___


<a name="collect" ></a>

### <code>collect</code>
<code><b>collect(<i>&lt;value1&gt;</i> : any) => array</b></code><br/><br/>
집계 그룹에 있는 식의 모든 값을 배열로 수집합니다. 이 프로세스 중에 구조를 수집하고 대체 구조로 변환할 수 있습니다. 항목 수는 해당 그룹의 행 수와 동일하며 Null 값을 포함할 수 있습니다. 수집된 항목 수는 적어야 합니다.  
* ``collect(salesPerson)``
* ``collect(firstName + lastName))``
* ``collect(@(name = salesPerson, sales = salesAmount) )``
___


<a name="columnNames" ></a>

### <code>columnNames</code>
<code><b>columnNames(<i>&lt;value1&gt;</i> : string) => array</b></code><br/><br/>
스트림의 모든 출력 열 이름을 가져옵니다. 선택적 스트림 이름을 두 번째 인수로 전달할 수 있습니다.  
* ``columnNames()``
* ``columnNames('DeriveStream')``
___


<a name="columns" ></a>

### <code>columns</code>
<code><b>columns([<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
스트림의 모든 출력 열 값을 가져옵니다. 선택적 스트림 이름을 두 번째 인수로 전달할 수 있습니다.   
* ``columns()``
* ``columns('DeriveStream')``
___


<a name="compare" ></a>

### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
동일한 형식의 두 값을 비교합니다. value1이 value2보다 작으면 음의 정수, value1과 value2가 같으면 0, value1이 value2보다 크면 양수를 반환합니다.  
* ``(compare(12, 24) < 1) -> true``  
* ``(compare('dumbo', 'dum') > 0) -> true``  
___


<a name="concat" ></a>

### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
가변 개수의 문자열을 함께 연결합니다. 문자열이 있는 + 연산자와 같습니다.  
* ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``  
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``  
* ``isNull('sql' + null) -> true``  
___


<a name="concatWS" ></a>

### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
가변 개수의 문자열을 구분 기호와 함께 연결합니다. 첫 번째 매개 변수는 구분 기호입니다.  
* ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'``  
* ``isNull(concatWS(null, 'dataflow', 'is', 'awesome')) -> true``  
* ``concatWS(' is ', 'dataflow', 'awesome') -> 'dataflow is awesome'``  
___


<a name="contains" ></a>

### <code>contains</code>
<code><b>contains(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => boolean</b></code><br/><br/>
제공된 배열의 요소가 제공된 조건자에서 true로 평가되면 true를 반환합니다. Contains는 조건자 함수의 한 요소에 대한 참조를 #item으로 예상합니다.  
* ``contains([1, 2, 3, 4], #item == 3) -> true``  
* ``contains([1, 2, 3, 4], #item > 5) -> false``  
___


<a name="cos" ></a>

### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
코사인 값을 계산합니다.  
* ``cos(10) -> -0.8390715290764524``  
___


<a name="cosh" ></a>

### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
값의 쌍곡선 코사인을 계산합니다.  
* ``cosh(0) -> 1.0``  
___


<a name="count" ></a>

### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
값의 집계 개수를 가져옵니다. 선택적 열을 지정하면 개수에서 Null 값을 무시합니다.  
* ``count(custId)``  
* ``count(custId, custName)``  
* ``count()``  
* ``count(iif(isNull(custId), 1, NULL))``  
___


<a name="countDistinct" ></a>

### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
열 세트의 고유 값에 대한 집계 합계를 구합니다.  
* ``countDistinct(custId, custName)``  
___


<a name="countIf" ></a>

### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
조건에 따라 값의 집계 개수를 가져옵니다. 선택적 열을 지정하면 개수에서 Null 값을 무시합니다.  
* ``countIf(state == 'CA' && commission < 10000, name)``  
___


<a name="covariancePopulation" ></a>

### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
두 열 간의 모집단 공 분산을 구합니다.  
* ``covariancePopulation(sales, profit)``  
___


<a name="covariancePopulationIf" ></a>

### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
조건에 따라 두 열의 모집단 공 분산을 구합니다.  
* ``covariancePopulationIf(region == 'West', sales)``  
___


<a name="covarianceSample" ></a>

### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
두 열의 샘플 공 분산을 구합니다.  
* ``covarianceSample(sales, profit)``  
___


<a name="covarianceSampleIf" ></a>

### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
조건에 따라 두 열의 샘플 공 분산을 구합니다.  
* ``covarianceSampleIf(region == 'West', sales, profit)``  
___



<a name="crc32" ></a>

### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
0(256), 224, 256, 384, 512 값의 지정된 비트 길이를 갖는 다양한 기본 데이터 형식의 열 세트에서 CRC32 해시를 계산합니다. 행의 지문을 계산하는 데 사용할 수 있습니다.  
* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L``  
___


<a name="cumeDist" ></a>

### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
CumeDist 함수는 파티션의 모든 값을 기준으로 값의 위치를 계산합니다. 결과는 파티션 순서에서 현재 행보다 앞에 있거나 같은 위치에 있는 행 수를 창 파티션의 총 행 수로 나눈 값입니다. 같은 순서의 값은 같은 위치로 계산됩니다.  
* ``cumeDist()``  
___


<a name="currentDate" ></a>

### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
이 작업 실행이 시작되는 현재 날짜를 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 현지 표준 시간대가 기본적으로 사용됩니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). 
* ``currentDate() == toDate('2250-12-31') -> false``  
* ``currentDate('PST')  == toDate('2250-12-31') -> false``  
* ``currentDate('America/New_York')  == toDate('2250-12-31') -> false``  
___


<a name="currentTimestamp" ></a>

### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
현지 표준 시간대를 사용하여 작업 실행을 시작하는 경우 현재 타임스탬프를 가져옵니다.  
* ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false``  
___


<a name="currentUTC" ></a>

### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
현재 타임스탬프를 UTC로 가져옵니다. 현재 시간을 클러스터 표준 시간대와 다른 표준 시간대로 해석하려면 'GMT', 'PST', 'UTC', 'America / Cayman' 형식으로 표준 시간대를 선택적으로 전달할 수 있습니다. 기본적으로 현재 표준 시간대가 사용됩니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). UTC 시간을 다른 표준 시간대로 변환하려면 `fromUTC()`를 사용합니다.  
* ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``  
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___


<a name="dayOfMonth" ></a>

### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
지정된 날짜의 월중 일을 가져옵니다.  
* ``dayOfMonth(toDate('2018-06-08')) -> 8``  
___


<a name="dayOfWeek" ></a>

### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
지정된 날짜의 요일을 가져옵니다. 1 - 일요일, 2 - 월요일 ..., 7 - 토요일.  
* ``dayOfWeek(toDate('2018-06-08')) -> 6``  
___


<a name="dayOfYear" ></a>

### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
지정된 날짜의 연중 일을 가져옵니다.  
* ``dayOfYear(toDate('2016-04-09')) -> 100``  
___


<a name="days" ></a>

### <code>days</code>
<code><b>days(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
기간(일)의 시간(밀리초).  
* ``days(2) -> 172800000L``  
___


<a name="degrees" ></a>

### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
라디안을 도로 변환합니다.  
* ``degrees(3.141592653589793) -> 180``  
___


<a name="denseRank" ></a>

### <code>denseRank</code>
<code><b>denseRank() => integer</b></code><br/><br/>
지정된 값 그룹에서 값의 순위는 절에 따른 창 순서로 계산됩니다. 결과는 파티션 순서에서 현재 행보다 앞에 있거나 같은 위치에 있는 행의 수에 1을 더한 것입니다. 값은 시퀀스에 간격을 생성하지 않습니다. Dense Rank는 데이터가 정렬되지 않은 경우에도 작동하며 값의 변화를 찾습니다.  
* ``denseRank()``  
___


<a name="distinct" ></a>

### <code>distinct</code>
<code><b>distinct(<i>&lt;value1&gt;</i> : array) => array</b></code><br/><br/>
배열의 고유 항목 집합을 반환합니다.
* ``distinct([10, 20, 30, 10]) => [10, 20, 30]``
___


<a name="divide" ></a>

### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
숫자 쌍을 나눕니다. `/` 연산자와 같습니다.  
* ``divide(20, 10) -> 2``  
* ``20 / 10 -> 2``
___


<a name="dropLeft" ></a>

### <code>dropLeft</code>
<code><b>dropLeft(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : integer) => string</b></code><br/><br/>
문자열 왼쪽에서 문자를 최대한 제거합니다. 요청된 삭제가 문자열의 길이를 초과하면 빈 문자열이 반환됩니다.
*   dropLeft('bojjus', 2) => 'jjus' *   dropLeft('cake', 10) => '' ___


<a name="dropRight" ></a>

### <code>dropRight</code>
<code><b>dropRight(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : integer) => string</b></code><br/><br/>
문자열의 오른쪽에서 문자를 최대한 제거합니다. 요청된 삭제가 문자열의 길이를 초과하면 빈 문자열이 반환됩니다.
*   dropRight('bojjus', 2) => 'bojj' *   dropRight('cake', 10) => '' ___


<a name="endsWith" ></a>

### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
문자열이 제공된 문자열로 끝나는지 확인합니다.  
* ``endsWith('dumbo', 'mbo') -> true``  
___


<a name="equals" ></a>

### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 같음 연산자. == 연산자와 같습니다.  
* ``equals(12, 24) -> false``  
* ``12 == 24 -> false``  
* ``'bad' == 'bad' -> true``  
* ``isNull('good' == toString(null)) -> true``  
* ``isNull(null == null) -> true``  
___


<a name="equalsIgnoreCase" ></a>

### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
비교는 대/소문자를 무시하는 연산자와 같습니다. <=> 연산자와 같습니다.  
* ``'abc'<=>'Abc' -> true``  
* ``equalsIgnoreCase('abc', 'Abc') -> true``  
___


<a name="escape" ></a>

### <code>escape</code>
<code><b>escape(<i>&lt;string_to_escape&gt;</i> : string, <i>&lt;format&gt;</i> : string) => string</b></code><br/><br/>
형식에 따라 문자열을 이스케이프합니다. 허용되는 형식의 리터럴 값은 'json', 'xml', 'ecmascript', 'html', 'java'입니다.
___


<a name="except" ></a>

### <code>except</code>
<code><b>except(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : array) => array</b></code><br/><br/>
삭제하는 다른 중복 항목에서 한 배열의 차이 집합을 반환합니다.
* ``except([10, 20, 30], [20, 40]) => [10, 30]``  
___


<a name="expr" ></a>

### <code>expr</code>
<code><b>expr(<i>&lt;expr&gt;</i> : string) => any</b></code><br/><br/>
문자열에서 식이 생성됩니다. 이것은 이 식을 리터럴이 아닌 형식으로 쓰는 것과 같습니다. 매개 변수를 문자열 표현으로 전달하는 데 사용할 수 있습니다.
*    expr('price * discount') => any ___


<a name="factorial" ></a>

### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
숫자의 계승값을 계산합니다.  
* ``factorial(5) -> 120``  
___


<a name="false" ></a>

### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
항상 false 값을 반환합니다. 'false'라는 열이 있는 경우 함수 `syntax(false())`를 사용합니다.  
* ``(10 + 20 > 30) -> false``  
* ``(10 + 20 > 30) -> false()``
___


<a name="filter" ></a>

### <code>filter</code>
<code><b>filter(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => array</b></code><br/><br/>
제공된 조건자를 충족하지 않는 배열에서 요소를 필터링합니다. 필터는 조건자 함수의 한 요소에 대한 참조를 #item으로 예상합니다.  
* ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]``  
* ``filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') -> ['a', 'b']``  
___


<a name="find" ></a>

### <code>find</code>
<code><b>find(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
배열에서 조건과 일치하는 첫 번째 항목을 찾습니다. 배열 항목을 #item으로 지정할 수 있는 필터 함수가 필요합니다. 깊게 중첩된 맵의 경우 #item_n(# item_1, # item_2 ...) 표기법을 사용하여 상위 맵을 참조할 수 있습니다.  
* ``find([10, 20, 30], #item > 10) -> 20``
* ``find(['azure', 'data', 'factory'], length(#item) > 4) -> 'azure'``
* ``find([
      @(
         name = 'Daniel',
         types = [
                   @(mood = 'jovial', behavior = 'terrific'),
                   @(mood = 'grumpy', behavior = 'bad')
                 ]
        ),
      @(
         name = 'Mark',
         types = [
                   @(mood = 'happy', behavior = 'awesome'),
                   @(mood = 'calm', behavior = 'reclusive')
                 ]
        )
      ],
      contains(#item.types, #item.mood=='happy')  /*Filter out the happy kid*/
    )``
* ``
     @(
           name = 'Mark',
           types = [
                     @(mood = 'happy', behavior = 'awesome'),
                     @(mood = 'calm', behavior = 'reclusive')
                   ]
      )
    ``  
___


<a name="first" ></a>* ``
 @(
       name = 'Mark',
       types = [
                 @(mood = 'happy', behavior = 'awesome'),
                 @(mood = 'calm', behavior = 'reclusive')
               ]
  )
``  
___


<a name="first" ></a>

### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
열 그룹의 첫 번째 값을 가져옵니다. 두 번째 매개 변수 ignoreNulls를 생략하면 false로 간주됩니다.  
* ``first(sales)``  
* ``first(sales, false)``  
___



<a name="flatten" ></a>

### <code>flatten</code>
<code><b>flatten(<i>&lt;array&gt;</i> : array, <i>&lt;value2&gt;</i> : array ..., <i>&lt;value2&gt;</i> : boolean) => array</b></code><br/><br/>
배열 또는 배열을 단일 배열로 평면화합니다. 원자성 항목의 배열은 변경되지 않은 상태로 반환됩니다. 마지막 인수는 선택 사항이며 두 개 이상의 수준 깊이를 재귀적으로 평면화하기 위해 false로 기본 설정됩니다.
*   ``flatten([['bojjus', 'girl'], ['gunchus', 'boy']]) => ['bojjus', 'girl', 'gunchus', 'boy']``
*   ``flatten([[['bojjus', 'gunchus']]] , true) => ['bojjus', 'gunchus']``
___


<a name="floor" ></a>

### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
해당 수보다 크지 않은 가장 큰 정수를 반환합니다.  
* ``floor(-0.1) -> -1``  
___


<a name="fromBase64" ></a>

### <code>fromBase64</code>
<code><b>fromBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
지정된 base64로 인코딩된 문자열을 디코딩합니다.
* ``fromBase64('Z3VuY2h1cw==') -> 'gunchus'``  
___


<a name="fromUTC" ></a>

### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
UTC의 타임스탬프로 변환합니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 표준 시간대를 선택적으로 전달할 수 있습니다. 기본적으로 현재 표준 시간대가 사용됩니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``fromUTC(currentTimestamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``fromUTC(currentTimestamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___


<a name="greater" ></a>

### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 보다 큼 연산자. > 연산자와 같습니다.  
* ``greater(12, 24) -> false``  
* ``('dumbo' > 'dum') -> true``  
* ``(toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS') > toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___


<a name="greaterOrEqual" ></a>

### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 보다 크거나 같음 연산자. >= 연산자와 같습니다.  
* ``greaterOrEqual(12, 12) -> true``  
* ``('dumbo' >= 'dum') -> true``  
___


<a name="greatest" ></a>

### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Null 값을 건너뛰는 입력으로 값 목록 중에서 가장 큰 값을 반환합니다. 모든 입력이 Null이면 Null을 반환합니다.  
* ``greatest(10, 30, 15, 20) -> 30``  
* ``greatest(10, toInteger(null), 20) -> 20``  
* ``greatest(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2011-12-12')``  
* ``greatest(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS'), toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')``  
___


<a name="hasColumn" ></a>

### <code>hasColumn</code>
<code><b>hasColumn(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => boolean</b></code><br/><br/>
스트림에서 이름을 기준으로 열 값을 확인합니다. 선택적 스트림 이름을 두 번째 인수로 전달할 수 있습니다. 디자인 타임에 알려진 열 이름은 해당 이름으로만 처리되어야 합니다. 계산된 입력은 지원되지 않지만 매개 변수 대체를 사용할 수 있습니다.  
* ``hasColumn('parent')``  
___


<a name="hasPath" ></a>

### <code>hasPath</code>
<code><b>hasPath(<i>&lt;value1&gt;</i> : string, [<i>&lt;streamName&gt;</i> : string]) => boolean</b></code><br/><br/>
특정 계층 경로가 스트림에 이름별로 존재하는지 확인합니다. 선택적 스트림 이름을 두 번째 인수로 전달할 수 있습니다. 디자인 타임에 알려진 열 이름/경로는 이름 또는 점 표기법 경로로만 지정해야 합니다. 계산된 입력은 지원되지 않지만 매개 변수 대체를 사용할 수 있습니다.  
* ``hasPath('grandpa.parent.child') => boolean``
___  


<a name="hex" ></a>

### <code>hex</code>
<code><b>hex(<i>\<value1\></i>: binary) => string</b></code><br/><br/>
이진 값를 16진 문자열 표현으로 반환합니다. * ``hex(toBinary([toByte(0x1f), toByte(0xad), toByte(0xbe)])) -> '1fadbe'``
___


<a name="hour" ></a>

### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
타임스탬프의 시간 값을 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 현지 표준 시간대가 기본적으로 사용됩니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``  
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``  
___


<a name="hours" ></a>

### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
기간(시간)의 시간(밀리초).  
* ``hours(2) -> 7200000L``  
___


<a name="iif" ></a>

### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
조건에 따라 하나의 값 또는 다른 값이 적용됩니다. 다른 값을 지정하지 않으면 Null로 간주됩니다. 두 값은 호환되어야 합니다(숫자, 문자열 등). * ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``  
* ``iif(10 > 30, 'dumbo', 'gumbo') -> 'gumbo'``  
* ``iif(month(toDate('2018-12-01')) == 12, 345.12, 102.67) -> 345.12``  
___


<a name="iifNull" ></a>

### <code>iifNull</code>
<code><b>iifNull(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => any</b></code><br/><br/>
첫 번째 매개 변수가 Null인지 확인합니다. Null이 아니면 첫 번째 매개 변수가 반환됩니다. Null인 경우 두 번째 매개 변수가 반환됩니다. 세 개의 매개 변수가 지정된 경우 동작은 iif(isNull(value1), value2, value3)와 동일하며 첫 번째 값이 Null이 아닌 경우 세 번째 매개 변수가 반환됩니다.  
* ``iifNull(10, 20) -> 10``  
* ``iifNull(null, 20, 40) -> 20``  
* ``iifNull('azure', 'data', 'factory') -> 'factory'``  
* ``iifNull(null, 'data', 'factory') -> 'data'``  
___


<a name="in" ></a>

### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
항목이 배열에 있는지 확인합니다.  
* ``in([10, 20, 30], 10) -> true``  
* ``in(['good', 'kid'], 'bad') -> false``  
___


<a name="initCap" ></a>

### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
모든 단어의 첫 글자를 대문자로 변환합니다. 단어는 공백으로 구분하여 식별됩니다.  
* ``initCap('cool iceCREAM') -> 'Cool Icecream'``  
___


<a name="instr" ></a>

### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
문자열 내에서 부분 문자열의 위치(1부터 시작)를 찾습니다. 찾을 수 없으면 0이 반환됩니다.  
* ``instr('dumbo', 'mbo') -> 3``  
* ``instr('microsoft', 'o') -> 5``  
* ``instr('good', 'bad') -> 0``  
___


<a name="intersect" ></a>

### <code>intersect</code>
<code><b>intersect(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : array) => array</b></code><br/><br/>
2개의 배열에서 고유 항목의 교집합 집합을 반환합니다.
* ``intersect([10, 20, 30], [20, 40]) => [20]``  
___


<a name="isBitSet" ></a>

### <code>isBitSet</code>
<code><b>isBitSet (<i><i>\<value1\></i></i> : array, <i>\<value2\></i>:integer ) => boolean</b></code><br/><br/>
이 비트 세트 * ``isBitSet(toBitSet([10, 32, 98]), 10) => true``
___


<a name="isBoolean" ></a>에 비트 위치가 설정되어 있는지 확인합니다.

### <code>isBoolean</code>
<code><b>isBoolean(<i>\<value1\></i>: string) => boolean</b></code><br/><br/>
문자열 값이 ``toBoolean()``
* ``isBoolean('true') -> true``
* ``isBoolean('no') -> true``
* ``isBoolean('microsoft') -> false``
___


<a name="isByte" ></a> 규칙에 따라 부울 값인지 확인합니다.

### <code>isByte</code>
<code><b>isByte(<i>\<value1\></i> : string) => boolean</b></code><br/><br/>
문자열 값이 ``toByte()``
* ``isByte('123') -> true``
* ``isByte('chocolate') -> false``
___


<a name="isDate" ></a> 규칙에 따라 지정된 선택적 형식의 바이트 값인지 확인합니다.

### <code>isDate</code>
<code><b>isDate (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
입력 날짜 문자열이 선택적 입력 날짜 형식을 사용하는 날짜인지 확인합니다. 사용 가능한 형식을 알아보려면 Java의 SimpleDateFormat을 참조하세요. 입력 날짜 형식이 생략된 경우 기본 형식은 ``yyyy-[M]M-[d]d``입니다. 허용되는 형식은 ``[ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ]``
* ``isDate('2012-8-18') -> true``
* ``isDate('12/18--234234' -> 'MM/dd/yyyy') -> false``
___


<a name="isDecimal" ></a>입니다.

### <code>isDecimal</code>
<code><b>isDecimal (<i>\<value1\></i> : string) => boolean</b></code><br/><br/>
문자열 값이 ``toDecimal()``
* ``isDecimal('123.45') -> true``
* ``isDecimal('12/12/2000') -> false``
___


<a name="isDelete" ></a> 규칙에 따라 지정된 선택적 형식의 decimal 값인지 검사합니다.

### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
행이 삭제용으로 표시되어 있는지 확인합니다. 둘 이상의 입력 스트림을 사용하는 변환은 스트림의 인덱스(1부터 시작)를 제공할 수 있습니다. 스트림 인덱스는 1 또는 2여야 하며 기본값은 1입니다.  
* ``isDelete()``  
* ``isDelete(1)``  
___


<a name="isDistinct" ></a>

### <code>isDistinct</code>
<code><b>isDistinct(<i>&lt;value1&gt;</i> : any , <i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
열 또는 열 집합이 고유한지 확인합니다. null은 고유 값으로 계산하지 않습니다. *    ``isDistinct(custId, custName) => boolean``
___



<a name="isDouble" ></a>

### <code>isDouble</code>
<code><b>isDouble (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
문자열 값이 ``toDouble()``
* ``isDouble('123') -> true``
* ``isDouble('$123.45' -> '$###.00') -> true``
* ``isDouble('icecream') -> false``
___


<a name="isError" ></a> 규칙에 따라 지정된 선택적 형식의 double 값인지 검사합니다.

### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
행이 오류로 표시되는지 여부를 확인합니다. 둘 이상의 입력 스트림을 사용하는 변환은 스트림의 인덱스(1부터 시작)를 제공할 수 있습니다. 스트림 인덱스는 1 또는 2여야 하며 기본값은 1입니다.  
* ``isError()``  
* ``isError(1)``  
___


<a name="isFloat" ></a>

### <code>isFloat</code>
<code><b>isFloat (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
문자열 값이 ``toFloat()``
* ``isFloat('123') -> true``
* ``isFloat('$123.45' -> '$###.00') -> true``
* ``isFloat('icecream') -> false``
___


<a name="isIgnore" ></a> 규칙에 따라 지정된 선택적 형식의 float 값인지 검사합니다.

### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
행이 무시되도록 표시되는지 여부를 확인합니다. 둘 이상의 입력 스트림을 사용하는 변환은 스트림의 인덱스(1부터 시작)를 제공할 수 있습니다. 스트림 인덱스는 1 또는 2여야 하며 기본값은 1입니다.  
* ``isIgnore()``  
* ``isIgnore(1)``  
___


<a name="isInsert" ></a>

### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
행이 삽입용으로 표시되어 있는지 확인합니다. 둘 이상의 입력 스트림을 사용하는 변환은 스트림의 인덱스(1부터 시작)를 제공할 수 있습니다. 스트림 인덱스는 1 또는 2여야 하며 기본값은 1입니다.  
* ``isInsert()``  
* ``isInsert(1)``  
___


<a name="isInteger" ></a>

### <code>isInteger</code>
<code><b>isInteger (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
문자열 값이 ``toInteger()``
* ``isInteger('123') -> true``
* ``isInteger('$123' -> '$###') -> true``
* ``isInteger('microsoft') -> false``
___


<a name="isLong" ></a> 규칙에 따라 지정된 선택적 형식의 integer 값인지 검사합니다.

### <code>isLong</code>
<code><b>isLong (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
문자열 값이 ``toLong()``
* ``isLong('123') -> true``
* ``isLong('$123' -> '$###') -> true``
* ``isLong('gunchus') -> false``
___


<a name="isMatch" ></a> 규칙에 따라 지정된 선택적 형식의 long 값인지 검사합니다.

### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
조회 시 행이 일치하는지 여부를 확인합니다. 둘 이상의 입력 스트림을 사용하는 변환은 스트림의 인덱스(1부터 시작)를 제공할 수 있습니다. 스트림 인덱스는 1 또는 2여야 하며 기본값은 1입니다.  
* ``isMatch()``  
* ``isMatch(1)``  
___


<a name="isNan" ></a>

### <code>isNan</code>
<code><b>isNan (<i>\<value1\></i> : integral) => boolean</b></code><br/><br/>
숫자가 아닌지 확인합니다.
* ``isNan(10.2) => false``
___


<a name="isNull" ></a>

### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
값이 Null인지 확인합니다.  
* ``isNull(NULL()) -> true``  
* ``isNull('') -> false``  
___


<a name="isShort" ></a>

### <code>isShort</code>
<code><b>isShort (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
문자열 값이 ``toShort()``
* ``isShort('123') -> true``
* ``isShort('$123' -> '$###') -> true``
* ``isShort('microsoft') -> false``
___


<a name="isTimestamp" ></a> 규칙에 따라 지정된 선택적 형식의 short 값인지 검사합니다.

### <code>isTimestamp</code>
<code><b>isTimestamp (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
입력 날짜 문자열이 선택적 입력 타임스탬프 형식을 사용하는 타임스탬프인지 확인합니다. 사용 가능한 형식은 Java의 SimpleDateFormat을 참조하세요. 타임스탬프를 생략하면 기본 패턴인 ``yyyy-[M]M-[d]d hh:mm:ss[.f...]``가 사용됩니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 타임스탬프는 999 값으로 최대 밀리 초 정확도를 지원합니다. 사용 가능한 형식은 Java의 SimpleDateFormat을 참조하세요.
* ``isTimestamp('2016-12-31 00:12:00') -> true``
* ``isTimestamp('2016-12-31T00:12:00' -> 'yyyy-MM-dd\\'T\\'HH:mm:ss' -> 'PST') -> true``
* ``isTimestamp('2012-8222.18') -> false``
___


<a name="isUpdate" ></a>

### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
행이 업데이트용으로 표시되어 있는지 확인합니다. 둘 이상의 입력 스트림을 사용하는 변환은 스트림의 인덱스(1부터 시작)를 제공할 수 있습니다. 스트림 인덱스는 1 또는 2여야 하며 기본값은 1입니다.  
* ``isUpdate()``  
* ``isUpdate(1)``  
___


<a name="isUpsert" ></a>

### <code>isUpsert</code>
<code><b>isUpsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
행이 삽입용으로 표시되어 있는지 확인합니다. 둘 이상의 입력 스트림을 사용하는 변환은 스트림의 인덱스(1부터 시작)를 제공할 수 있습니다. 스트림 인덱스는 1 또는 2여야 하며 기본값은 1입니다.  
* ``isUpsert()``  
* ``isUpsert(1)``  
___


<a name="jaroWinkler" ></a>

### <code>jaroWinkler</code>
<code><b>jaroWinkler(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => double</b></code><br/><br/>
두 문자열 간의 JaroWinkler 거리를 가져옵니다. 
* ``jaroWinkler('frog', 'frog') => 1.0``  
___


<a name="keyValues" ></a>

### <code>keyValues</code>
<code><b>keyValues(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : array) => map</b></code><br/><br/>
키/값의 맵을 만듭니다. 첫 번째 매개 변수는 키의 배열이고 두 번째 매개 변수는 값의 배열입니다. 두 배열의 길이는 같아야 합니다.
*   ``keyValues(['bojjus', 'appa'], ['gunchus', 'ammi']) => ['bojjus' -> 'gunchus', 'appa' -> 'ammi']``
___ 


<a name="kurtosis" ></a>

### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
열의 첨도를 구합니다.  
* ``kurtosis(sales)``  
___


<a name="kurtosisIf" ></a>

### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
조건에 따라 열의 첨도를 구합니다.  
* ``kurtosisIf(region == 'West', sales)``  
___


<a name="lag" ></a>

### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
현재 행보다 n개 행 전에 평가되는 첫 번째 매개 변수의 값을 가져옵니다. 두 번째 매개 변수는 거꾸로 확인할 행의 수이며 기본값은 1입니다. 기본값을 지정하지 않은 경우, 행이 많지 않으면 Null 값이 반환됩니다.  
* ``lag(amount, 2)``  
* ``lag(amount, 2000, 100)``  
___


<a name="last" ></a>

### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
열 그룹의 마지막 값을 가져옵니다. 두 번째 매개 변수 ignoreNulls를 생략하면 false로 간주됩니다.  
* ``last(sales)``  
* ``last(sales, false)``  
___


<a name="lastDayOfMonth" ></a>

### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
지정된 날짜의 월중 마지막 날짜를 가져옵니다.  
* ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')``  
___


<a name="lead" ></a>

### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
현재 행보다 n개 행 뒤에 평가되는 첫 번째 매개 변수의 값을 가져옵니다. 두 번째 매개 변수는 앞으로 조회할 행의 수이며 기본값은 1입니다. 기본값을 지정하지 않은 경우, 행이 많지 않으면 Null 값이 반환됩니다.  
* ``lead(amount, 2)``  
* ``lead(amount, 2000, 100)``  
___


<a name="least" ></a>

### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
비교 보다 작거나 같음 연산자. <= 연산자와 같습니다.  
* ``least(10, 30, 15, 20) -> 10``  
* ``least(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2000-12-12')``  
___


<a name="left" ></a>

### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
인덱스 1에서 시작하여 지정된 문자 수를 갖는 부분 문자열을 추출합니다. SUBSTRING(str, 1, n)과 같습니다.  
* ``left('bojjus', 2) -> 'bo'``  
* ``left('bojjus', 20) -> 'bojjus'``  
___


<a name="length" ></a>

### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
문자열의 길이를 반환합니다.  
* ``length('dumbo') -> 5``  
___


<a name="lesser" ></a>

### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 보다 작음 연산자. < 연산자와 같습니다.  
* ``lesser(12, 24) -> true``  
* ``('abcd' < 'abc') -> false``  
* ``(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') < toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___


<a name="lesserOrEqual" ></a>

### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 보다 작거나 같음 연산자. <= 연산자와 같습니다.  
* ``lesserOrEqual(12, 12) -> true``  
* ``('dumbo' <= 'dum') -> false``  
___


<a name="levenshtein" ></a>

### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
두 문자열 간의 levenshtein 거리를 가져옵니다.  
* ``levenshtein('boys', 'girls') -> 4``  
___


<a name="like" ></a>

### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
패턴은 말 그대로 일치하는 문자열을 나타냅니다. 예외는 다음과 같은 특수 기호입니다. _는 입력에서 하나의 문자와 일치합니다( ```posix``` 정규식의 .과 유사). %는 입력의 0개 이상의 문자와 일치합니다(```posix``` 정규식의 .*와 유사).
이스케이프 문자는 ''입니다. 특수 기호 또는 다른 이스케이프 문자 앞에 이스케이프 문자가 오면 다음 문자는 글자 그대로 일치됩니다. 다른 모든 문자를 이스케이프하는 것은 유효하지 않습니다.  
* ``like('icecream', 'ice%') -> true``  
___


<a name="locate" ></a>

### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
특정 위치에서 시작하는 문자열 내에서 부분 문자열의 위치(1부터 시작)를 찾습니다. 이 위치를 생략하면 문자열의 시작 부분에서 찾습니다. 찾을 수 없으면 0이 반환됩니다.  
* ``locate('mbo', 'dumbo') -> 3``  
* ``locate('o', 'microsoft', 6) -> 7``  
* ``locate('bad', 'good') -> 0``  
___


<a name="log" ></a>

### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
로그 값을 계산합니다. 오일러 수 이외의 선택적인 밑을 제공할 수 있습니다(사용할 경우).  
* ``log(100, 10) -> 2``  
___


<a name="log10" ></a>

### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
밑이 10인 로그 값을 계산합니다.  
* ``log10(100) -> 2``  
___


<a name="lookup" ></a>

### <code>lookup</code>
<code><b>lookup(key, key2, ...) => complex[]</b></code><br/><br/>
캐시된 싱크의 키와 일치하는 지정된 키를 사용하여 캐시된 싱크에서 첫 번째 행을 조회합니다.
* ``cacheSink#lookup(movieId)``  
___


<a name="lower" ></a>

### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
문자열을 소문자로 바꿉니다.  
* ``lower('GunChus') -> 'gunchus'``  
___


<a name="lpad" ></a>

### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
특정 길이가 될 때까지 제공된 패딩으로 문자열의 왼쪽 여백을 채웁니다. 문자열이 길이보다 크거나 같은 경우 길이에 맞게 잘립니다.  
* ``lpad('dumbo', 10, '-') -> '-----dumbo'``  
* ``lpad('dumbo', 4, '-') -> 'dumb'``  
* ''lpad('<>', 8, '<>') -> '<><dumbo'``  
___


<a name="ltrim" ></a>

### <code>ltrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
왼쪽의 선행 문자열을 삭제합니다. 두 번째 매개 변수를 지정하지 않으면 공백을 삭제합니다. 그렇지 않은 경우 두 번째 매개 변수에 지정된 모든 문자를 삭제합니다.  
* ``ltrim('  dumbo  ') -> 'dumbo  '``  
* ``ltrim('!--!du!mbo!', '-!') -> 'du!mbo!'``  
___


<a name="map" ></a>

### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
제공된 식을 사용하여 배열의 각 요소를 새 요소에 매핑합니다. Map에는 식 함수의 한 요소에 대한 참조가 #item 것으로 예상됩니다.  
* ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``  
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``  
___


<a name="mapAssociation" ></a>

### <code>mapAssociation</code>
<code><b>mapAssociation(<i>&lt;value1&gt;</i> : map, <i>&lt;value2&gt;</i> : binaryFunction) => array</b></code><br/><br/>
키를 새 값에 연결하여 맵을 변환합니다. 배열을 반환합니다. 항목을 #key로, 현재 값을 #value로 지정할 수 있는 매핑 함수를 사용합니다. 
*   ``mapAssociation(['bojjus' -> 'gunchus', 'appa' -> 'ammi'], @(key = #key, value = #value)) => [@(key = 'bojjus', value = 'gunchus'), @(key = 'appa', value = 'ammi')]``
___ 


<a name="mapIf" ></a>

### <code>mapIf</code>
<code><b>mapIf (<i>\<value1\></i> : array, <i>\<value2\></i> : binaryfunction, \<value3\>: binaryFunction) => any</b></code><br/><br/>
배열을 길이가 동일하거나 작은 다른 배열에 조건부로 매핑합니다. 값은 structTypes을 포함한 모든 데이터 형식이 될 수 있습니다. 배열의 항목을 #item으로, 현재 인덱스를 #index로 지정할 수 있는 매핑 함수를 사용합니다. 깊게 중첩된 맵의 경우 표기산을 사용하여 부모 맵을 참조할 수 ``#item_[n](#item_1, #index_1...)`` 있습니다.
*    ``mapIf([10, 20, 30], #item > 10, #item + 5) -> [25, 35]``
* ``mapIf(['icecream', 'cake', 'soda'], length(#item) > 4, upper(#item)) -> ['ICECREAM', 'CAKE']``
___


<a name="mapIndex" ></a>

### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></code><br/><br/>
제공된 식을 사용하여 배열의 각 요소를 새 요소에 매핑합니다. Map에는 식 함수의 한 요소에 대한 참조가 #item 요소 인덱스에 대한 참조가 #index.  
* ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``  
___


<a name="mapLoop" ></a>

### <code>mapLoop</code>
<code><b>mapLoop(<i>\<value1\></i> : integer, <i>\<value2\></i> : unaryfunction) => any</b></code><br/><br/>
1에서 길이까지 반복하여 해당 길이의 배열을 만듭니다. 배열의 인덱스를 #index로 지정할 수 있는 매핑 함수를 사용합니다. 깊게 중첩된 맵의 경우 #index_n(#index_1, #index_2...) 표기화를 사용하여 부모 맵을 참조할 수 있습니다.
*    ``mapLoop(3, #index * 10) -> [10, 20, 30]``
___


<a name="max" ></a>

### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
열의 최댓값을 구합니다.  
* ``max(sales)``  
___


<a name="maxIf" ></a>

### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
조건에 따라 열의 최댓값을 구합니다.  
* ``maxIf(region == 'West', sales)``  
___


<a name="md5" ></a>

### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
다양한 기본 데이터 형식의 열 세트에서 MD5 다이제스트를 계산하고 32자의 16진수 문자열을 반환합니다. 행의 지문을 계산하는 데 사용할 수 있습니다.  
* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'``  
___


<a name="mean" ></a>

### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
열 값의 평균값을 가져옵니다. AVG와 같습니다.  
* ``mean(sales)``  
___


<a name="meanIf" ></a>

### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
조건에 따라 열 값의 평균값을 가져옵니다. avgIf와 같습니다.  
* ``meanIf(region == 'West', sales)``  
___


<a name="millisecond" ></a>

### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
날짜의 밀리초 값을 구합니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 현지 표준 시간대가 기본적으로 사용됩니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___


<a name="milliseconds" ></a>

### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
기간(밀리초)의 시간(밀리초).  
* ``milliseconds(2) -> 2L``  
___


<a name="min" ></a>

### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
열의 최솟값을 구합니다.  
* ``min(sales)``  
___


<a name="minIf" ></a>

### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
조건에 따라 열의 최솟값을 구합니다.  
* ``minIf(region == 'West', sales)``  
___


<a name="minus" ></a>

### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
숫자를 뺍니다. 날짜에서 일 수를 뺍니다. 타임스탬프에서 기간을 뺍니다. 두 타임스탬프를 빼서 밀리초 단위로 차이를 구합니다. \- 연산자와 같습니다.  
* ``minus(20, 10) -> 10``  
* ``20 - 10 -> 10``  
* ``minus(toDate('2012-12-15'), 3) -> toDate('2012-12-12')``  
* ``toDate('2012-12-15') - 3 -> toDate('2012-12-12')``  
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``  
* ``toTimestamp('2019-02-03 05:21:34.851', 'yyyy-MM-dd HH:mm:ss.SSS') - toTimestamp('2019-02-03 05:21:36.923', 'yyyy-MM-dd HH:mm:ss.SSS') -> -2072``  
___


<a name="minute" ></a>

### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
타임스탬프의 분 값을 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 현지 표준 시간대가 기본적으로 사용됩니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``  
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``  
___


<a name="minutes" ></a>

### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
기간(분)의 시간(밀리초).  
* ``minutes(2) -> 120000L``  
___


<a name="mlookup" ></a>

### <code>mlookup</code>
<code><b>mlookup(key, key2, ...) => complex[]</b></code><br/><br/>
캐시된 싱크의 키와 일치하는 지정된 키를 사용하여 캐시된 싱크에서 일치하는 모든 행을 조회합니다.
* ``cacheSink#mlookup(movieId)``  
___


<a name="mod" ></a>

### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
숫자 쌍의 모듈러스입니다. % 연산자와 같습니다.  
* ``mod(20, 8) -> 4``  
* ``20 % 8 -> 4``  
___


<a name="month" ></a>

### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
날짜 또는 타임스탬프의 월 값을 구합니다.  
* ``month(toDate('2012-8-8')) -> 8``  
___


<a name="monthsBetween" ></a>

### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;roundoff&gt;</i> : boolean], [<i>&lt;time zone&gt;</i> : string]) => double</b></code><br/><br/>
두 날짜 사이의 개월 수를 구합니다. 계산을 반올림할 수 있습니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 현지 표준 시간대가 기본적으로 사용됩니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``  
___


<a name="multiply" ></a>

### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
숫자 쌍을 곱합니다. \* 연산자와 같습니다.  
* ``multiply(20, 10) -> 200``  
* ``20 * 10 -> 200``  
___


<a name="negate" ></a>

### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
숫자를 부정합니다. 양수를 음수로, 음수를 양수로 변환합니다.  
* ``negate(13) -> -13``  
___


<a name="nextSequence" ></a>

### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
고유한 다음 시퀀스를 반환합니다. 이 수는 파티션 내에서만 연속되며 앞에 partitionId가 붙습니다.  
* ``nextSequence() == 12313112 -> false``  
___


<a name="normalize" ></a>

### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
악센트 기호가 있는 유니코드 문자를 구분하기 위해 문자열 값을 정규화합니다.  
* ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'``
___


<a name="not" ></a>

### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
논리 부정 연산자.  
* ``not(true) -> false``  
* ``not(10 == 20) -> true``  
___


<a name="notEquals" ></a>

### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 같지 않음 연산자. != 연산자와 같습니다.  
* ``12 != 24 -> true``  
* ``'bojjus' != 'bo' + 'jjus' -> false``  
___


<a name="notNull" ></a>

### <code>notNull</code>
<code><b>notNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
값이 Null이 아닌지 확인합니다.  
* ``notNull(NULL()) -> false``  
* ``notNull('') -> true``  
___


<a name="nTile" ></a>

### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
```NTile``` 함수는 각 창 파티션의 행을 1에서 최대 `n` 사이의 `n`개 버킷으로 나눕니다. 버킷 값 차이는 최대 1 이내입니다. 파티션의 행 수가 버킷 수로 균일하게 나누어 떨어지지 않으면 나머지 값은 첫 번째 버킷부터 시작해서 하나씩 분산됩니다. ```NTile``` 함수는 ```tertiles```, 사분위수, 십진수 및 기타 일반적인 요약 통계를 계산하는 데 유용합니다. 이 함수는 초기화하는 동안 두 개의 변수를 계산합니다. 일반 버킷의 크기에는 하나의 행이 더 추가됩니다. 두 변수는 현재 파티션의 크기를 기준으로 합니다. 계산 프로세스 동안 이 함수는 현재 행 번호, 현재 버킷 수 및 버킷이 변경되는 행 번호(bucketThreshold)를 추적합니다. 현재 행 번호가 버킷 임계값에 도달하면 버킷 값이 1씩 커지고, 임계값은 버킷 크기만큼 커집니다(현재 버킷이 채워질 경우 하나씩 추가).  
* ``nTile()``  
* ``nTile(numOfBuckets)``  
___


<a name="null" ></a>

### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
NULL 값을 반환합니다. 'null'이라는 열이 있는 경우 함수 `syntax(null())`를 사용합니다. 사용하는 모든 작업은 결과적으로 Null이 됩니다.  
* ``isNull('dumbo' + null) -> true``  
* ``isNull(10 * null) -> true``  
* ``isNull('') -> false``  
* ``isNull(10 + 20) -> false``  
* ``isNull(10/0) -> true``  
___


<a name="or" ></a>

### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
논리적 OR 연산자입니다. ||과 같습니다.  
* ``or(true, false) -> true``  
* ``true || false -> true``  
___


<a name="originColumns" ></a>

### <code>originColumns</code>
<code><b>originColumns(<i>&lt;streamName&gt;</i> : string) => any</b></code><br/><br/>
열을 만든 원본 스트림에 대한 모든 출력 열을 가져옵니다. 다른 함수로 묶어야 합니다.
* ``array(toString(originColumns('source1')))``
___  


<a name="output" ></a>

### <code>output</code>
<code><b>output() => any</b></code><br/><br/>
캐시 싱크 결과의 첫 번째 행을 반환합니다. * ``cacheSink#output()``  
___


<a name="outputs" ></a>

### <code>outputs</code>
<code><b>output() => any</b></code><br/><br/>
캐시 싱크 결과의 전체 출력 행 세트를 반환합니다. * ``cacheSink#outputs()``
___



<a name="partitionId" ></a>

### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
입력 행이 있는 현재 파티션 ID를 반환합니다.  
* ``partitionId()``  
___


<a name="pMod" ></a>

### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
숫자 쌍의 양의 모듈러스입니다.  
* ``pmod(-20, 8) -> 4``  
___


<a name="power" ></a>

### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
한 수를 다른 수의 승수로 거듭제곱합니다.  
* ``power(10, 2) -> 100``  
___


<a name="radians" ></a>

### <code>radians</code>
<code><b>radians(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
도를 라디안으로 변환합니다.* ``radians(180) => 3.141592653589793``  
___


<a name="random" ></a>

### <code>random</code>
<code><b>random(<i>&lt;value1&gt;</i> : integral) => long</b></code><br/><br/>
파티션 내의 선택적 시드가 지정된 난수를 반환합니다. 시드는 고정 값이어야 하며 partitionId와 함께 사용되어 임의 값을 생성합니다. * ``random(1) == 1 -> false``
___


<a name="rank" ></a>

### <code>rank</code>
<code><b>rank() => integer</b></code><br/><br/>
지정된 값 그룹에서 값의 순위는 절에 따른 창 순서로 계산됩니다. 결과는 파티션 순서에서 현재 행보다 앞에 있거나 같은 위치에 있는 행의 수에 1을 더한 것입니다. 값은 시퀀스에 간격을 생성합니다. Rank는 데이터가 정렬되지 않은 경우에도 작동하며 값의 변화를 찾습니다.  
* ``rank()``  
___


<a name="reassociate" ></a>

### <code>reassociate</code>
<code><b>reassociate(<i>&lt;value1&gt;</i> : map, <i>&lt;value2&gt;</i> : binaryFunction) => map</b></code><br/><br/>
키를 새 값에 연결하여 맵을 변환합니다. 항목을 #key로, 현재 값을 #value로 지정할 수 있는 매핑 함수를 사용합니다.  
* ``reassociate(['fruit' -> 'apple', 'vegetable' -> 'tomato'], substring(#key, 1, 1) + substring(#value, 1, 1)) => ['fruit' -> 'fa', 'vegetable' -> 'vt']``
___
  


<a name="reduce" ></a>

### <code>reduce</code>
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></code><br/><br/>
배열에 요소를 누적합니다. 줄이기를 사용 하면 첫 번째 식 함수에서 누적기 및 one 요소에 대 한 참조가 #acc 및 #item으로 예상 되며 결과 값이 두 번째 식 함수에서 사용 될 #result으로 예상 됩니다.  
* ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '01234'``  
___


<a name="regexExtract" ></a>

### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
지정된 정규식 패턴에 대해 일치하는 부분 문자열을 추출합니다. 마지막 매개 변수는 일치 그룹을 식별하고 생략하면 기본적으로 1이 지정됩니다. 이스케이프 없이 문자열을 일치시키려면 `<regex>`(따옴표)를 사용합니다.  
* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``  
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``  
___


<a name="regexMatch" ></a>

### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
문자열이 지정된 정규식 패턴과 일치하는지 확인합니다. 이스케이프 없이 문자열을 일치시키려면 `<regex>`(따옴표)를 사용합니다.  
* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``  
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``  
___


<a name="regexReplace" ></a>

### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
한 정규식 패턴이 나오는 모든 경우를 지정된 문자열의 다른 하위 문자열로 바꿉니다. 이스케이프 없이 문자열을 일치시키려면 `<regex>`(따옴표)를 사용합니다.  
* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``  
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``  
___


<a name="regexSplit" ></a>

### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
정규식을 기준으로 구분 기호에 따라 문자열을 분할하고 문자열의 배열을 반환합니다.  
* ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']``  
* ``regexSplit('bojjusAgunchusBdumboC', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo', '']``  
* ``(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[1]) -> 'bojjus'``  
* ``isNull(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[20]) -> true``  
___


<a name="replace" ></a>

### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, [<i>&lt;substring to replace&gt;</i> : string]) => string</b></code><br/><br/>
한 하위 문자열이 나오는 모든 경우를 지정된 문자열의 다른 하위 문자열로 바꿉니다. 마지막 매개 변수를 생략하는 경우 기본값은 빈 문자열입니다.  
* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``  
* ``replace('doggie dog', 'dog', '') -> 'gie '``  
* ``replace('doggie dog', 'dog') -> 'gie '``  
___


<a name="reverse" ></a>

### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
문자열을 반대로 바꿉니다.  
* ``reverse('gunchus') -> 'suhcnug'``  
___


<a name="right" ></a>

### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
오른쪽부터 지정된 문자 수를 갖는 부분 문자열을 추출합니다. SUBSTRING(str, LENGTH(str) - n, n)과 같습니다.  
* ``right('bojjus', 2) -> 'us'``  
* ``right('bojjus', 20) -> 'bojjus'``  
___


<a name="rlike" ></a>

### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
문자열이 지정된 정규식 패턴과 일치하는지 확인합니다.  
* ``rlike('200.50', `(\d+).(\d+)`) -> true``  
* ``rlike('bogus', `M[0-9]+.*`) -> false``  
___


<a name="round" ></a>

### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
지정된 선택적 소수 자릿수와 선택적 반올림 모드를 사용하여 숫자를 반올림합니다. 소수 자릿수를 생략하면 기본 자릿수 0이 사용됩니다. 반올림 모드를 생략하면 기본적으로 ROUND_HALF_UP(5)이 사용됩니다. 반올림 값에는 1 - ROUND_UP 2 - ROUND_DOWN 3 - ROUND_CEILING 4 - ROUND_FLOOR 5 - ROUND_HALF_UP 6 - ROUND_HALF_DOWN 7 - ROUND_HALF_EVEN 8 - ROUND_UNNECESSARY가 포함됩니다.  
* ``round(100.123) -> 100.0``  
* ``round(2.5, 0) -> 3.0``  
* ``round(5.3999999999999995, 2, 7) -> 5.40``  
___


<a name="rowNumber" ></a>

### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
창에서 행에 대해 순차적으로 1부터 번호를 매깁니다.  
* ``rowNumber()``  



<a name="rpad" ></a>

### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
특정 길이가 될 때까지 제공된 패딩으로 문자열의 오른쪽 여백을 채웁니다. 문자열이 길이보다 크거나 같은 경우 길이에 맞게 잘립니다.  
* ``rpad('dumbo', 10, '-') -> 'dumbo-----'``  
* ``rpad('dumbo', 4, '-') -> 'dumb'``  
* ' ' rpad (' dumbo ', 8, ' <> ')-> ' dumbo<><'``  
___


<a name="rtrim" ></a>

### <code>rtrim</code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
오른쪽의 후행 문자열을 삭제합니다. 두 번째 매개 변수를 지정하지 않으면 공백을 삭제합니다. 그렇지 않은 경우 두 번째 매개 변수에 지정된 모든 문자를 삭제합니다.  
* ``rtrim('  dumbo  ') -> '  dumbo'``  
* ``rtrim('!--!du!mbo!', '-!') -> '!--!du!mbo'``  
___


<a name="second" ></a>

### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
날짜의 초 값을 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 현지 표준 시간대가 기본적으로 사용됩니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``  
___


<a name="seconds" ></a>

### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
기간(초)의 시간(밀리초).  
* ``seconds(2) -> 2000L``  
___


<a name="setBitSet" ></a>

### <code>setBitSet</code>
<code><b>setBitSet (<i>\<value1\></i>: array, <i>\<value2\></i>:array) => array</b></code><br/><br/>
이 비트 세트 * ``setBitSet(toBitSet([10, 32]), [98]) => [4294968320L, 17179869184L]``
___  


<a name="sha1" ></a>에 비트 위치 설정

### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
다양한 기본 데이터 형식의 열 세트에서 SHA-1 다이제스트를 계산하고 40자의 16진수 문자열을 반환합니다. 행의 지문을 계산하는 데 사용할 수 있습니다.  
* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'``  
___


<a name="sha2" ></a>

### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
0(256), 224, 256, 384, 512 값의 지정된 비트 길이를 갖는 다양한 기본 데이터 형식의 열 세트에서 SHA-2 다이제스트를 계산합니다. 행의 지문을 계산하는 데 사용할 수 있습니다.  
* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'``  
___


<a name="sin" ></a>

### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
사인 값을 계산합니다.  
* ``sin(2) -> 0.9092974268256817``  
___


<a name="sinh" ></a>

### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
쌍곡선 사인 값을 계산합니다.  
* ``sinh(0) -> 0.0``  
___


<a name="size" ></a>

### <code>size</code>
<code><b>size(<i>&lt;value1&gt;</i> : any) => integer</b></code><br/><br/>
배열 또는 지도 형식의 크기를 찾습니다. * ``size(['element1', 'element2']) -> 2``
* ``size([1,2,3]) -> 3``
___


<a name="skewness" ></a>

### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
열의 왜도를 구합니다.  
* ``skewness(sales)``  
___


<a name="skewnessIf" ></a>

### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
조건에 따라 열의 왜도를 구합니다.  
* ``skewnessIf(region == 'West', sales)``  
___


<a name="slice" ></a>

### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
특정 위치에서 배열의 하위 세트를 추출합니다. 위치는 1부터 시작합니다. 길이를 생략하면 기본적으로 문자열 끝까지입니다.  
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``  
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``  
* ``slice([10, 20, 30, 40], 2)[1] -> 20``  
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``  
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``  
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``  
___


<a name="sort" ></a>

### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></code><br/><br/>
제공된 조건자 함수를 사용하여 배열을 정렬합니다. Sort는 #item1 및 #item2 식 함수에서 두 개의 연속 된 요소에 대 한 참조가 필요 합니다.  
* ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``  
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``  
___


<a name="soundex" ></a>

### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
문자열의 ```soundex``` 코드를 가져옵니다.  
* ``soundex('genius') -> 'G520'``  
___


<a name="split" ></a>

### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
구분 기호를 기준으로 문자열을 분할하고 문자열의 배열을 반환합니다.  
* ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus,guchus,dumbo', '|') -> ['bojjus,guchus,dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ')[1] -> 'bojjus'``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[0]) -> true``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[20]) -> true``  
* ``split('bojjusguchusdumbo', ',') -> ['bojjusguchusdumbo']``  
___


<a name="sqrt" ></a>

### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
숫자의 제곱근을 계산합니다.  
* ``sqrt(9) -> 3``  
___


<a name="startsWith" ></a>

### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
문자열이 제공된 문자열로 시작하는지 확인합니다.  
* ``startsWith('dumbo', 'du') -> true``  
___


<a name="stddev" ></a>

### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
열의 표준 편차를 구합니다.  
* ``stdDev(sales)``  
___


<a name="stddevIf" ></a>

### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
조건에 따라 열의 표준 편차를 구합니다.  
* ``stddevIf(region == 'West', sales)``  
___


<a name="stddevPopulation" ></a>

### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
열의 모집단 표준 편차를 구합니다.  
* ``stddevPopulation(sales)``  
___


<a name="stddevPopulationIf" ></a>

### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
조건에 따라 열의 모집단 표준 편차를 구합니다.  
* ``stddevPopulationIf(region == 'West', sales)``  
___


<a name="stddevSample" ></a>

### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
열의 샘플 표준 편차를 구합니다.  
* ``stddevSample(sales)``  
___


<a name="stddevSampleIf" ></a>

### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
조건에 따라 열의 샘플 표준 편차를 구합니다.  
* ``stddevSampleIf(region == 'West', sales)``  
___


<a name="subDays" ></a>

### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
날짜 또는 타임스탬프에서 일을 뺍니다. 날짜에 대한 - 연산자와 같습니다.  
* ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')``  
___


<a name="subMonths" ></a>

### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
날짜 또는 타임스탬프에서 월을 뺍니다.  
* ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')``  
___


<a name="substring" ></a>

### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
특정 위치에서 특정 길이의 부분 문자열을 추출합니다. 위치는 1부터 시작합니다. 길이를 생략하면 기본적으로 문자열 끝까지입니다.  
* ``substring('Cat in the hat', 5, 2) -> 'in'``  
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``  
* ``substring('Cat in the hat', 5) -> 'in the hat'``  
* ``substring('Cat in the hat', 100, 100) -> ''``  
___


<a name="sum" ></a>

### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
숫자 열의 집계 합계를 구합니다.  
* ``sum(col)``  
___


<a name="sumDistinct" ></a>

### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
숫자 열의 고유 값에 대한 집계 합계를 구합니다.  
* ``sumDistinct(col)``  
___


<a name="sumDistinctIf" ></a>

### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
기준에 따라 숫자 열의 집계 합계를 가져옵니다. 조건의 기준은 어떤 열도 될 수 있습니다.  
* ``sumDistinctIf(state == 'CA' && commission < 10000, sales)``  
* ``sumDistinctIf(true, sales)``  
___


<a name="sumIf" ></a>

### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
기준에 따라 숫자 열의 집계 합계를 가져옵니다. 조건의 기준은 어떤 열도 될 수 있습니다.  
* ``sumIf(state == 'CA' && commission < 10000, sales)``  
* ``sumIf(true, sales)``  
___


<a name="tan" ></a>

### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
탄젠트 값을 계산합니다.  
* ``tan(0) -> 0.0``  
___


<a name="tanh" ></a>

### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
쌍곡선 탄젠트 값을 계산합니다.  
* ``tanh(0) -> 0.0``  
___


<a name="toBase64" ></a>

### <code>toBase64</code>
<code><b>toBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
base64에서 지정된 문자열을 인코딩합니다.  
* ``toBase64('bojjus') -> 'Ym9qanVz'``  
___


<a name="toBinary" ></a>

### <code>toBinary</code>
<code><b>toBinary(<i>&lt;value1&gt;</i> : any) => binary</b></code><br/><br/>
숫자/날짜/타임스탬프/문자열을 이진 표현으로 변환합니다.  
* ``toBinary(3) -> [0x11]``  
___


<a name="toBoolean" ></a>

### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
('t', 'true', 'y', 'yes', '1') 값을 true로 변환하고 ('f', 'false', 'n', 'no', '0') 값을 false로 변환하고, 다른 모든 값은 Null로 변환합니다.  
* ``toBoolean('true') -> true``  
* ``toBoolean('n') -> false``  
* ``isNull(toBoolean('truthy')) -> true``  
___


<a name="toByte" ></a>

### <code>toByte</code>
<code><b>toByte(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => byte</b></code><br/><br/>
숫자 또는 문자열을 바이트 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다.  
* ``toByte(123)``
* ``123``
* ``toByte(0xFF)``
* ``-1``
* ``toByte('123')``
* ``123``
___


<a name="toDate" ></a>

### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
선택적 입력 날짜 형식을 사용하여 입력 날짜 문자열을 날짜로 변환합니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. 입력 날짜 형식이 생략된 경우 기본 형식은 yyyy-[M]M-[d]d입니다. 허용되는 형식은 [ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ]입니다.  
* ``toDate('2012-8-18') -> toDate('2012-08-18')``  
* ``toDate('12/18/2012', 'MM/dd/yyyy') -> toDate('2012-12-18')``  
___


<a name="toDecimal" ></a>

### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
숫자 또는 문자열을 10진수 값으로 변환합니다. 자릿수와 소수 자릿수를 지정하지 않으면 기본값인 (10,2)가 사용됩니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. en-US, de, zh-CN과 같은 BCP47 언어 형식의 선택적 로캘 형식입니다.  
* ``toDecimal(123.45) -> 123.45``  
* ``toDecimal('123.45', 8, 4) -> 123.4500``  
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``  
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``  
___


<a name="toDouble" ></a>

### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
숫자 또는 문자열을 배정도 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. en-US, de, zh-CN과 같은 BCP47 언어 형식의 선택적 로캘 형식입니다.  
* ``toDouble(123.45) -> 123.45``  
* ``toDouble('123.45') -> 123.45``  
* ``toDouble('$123.45', '$###.00') -> 123.45``  
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``  
___


<a name="toFloat" ></a>

### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
숫자 또는 문자열을 부동 소수점 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. double을 자릅니다.  
* ``toFloat(123.45) -> 123.45f``  
* ``toFloat('123.45') -> 123.45f``  
* ``toFloat('$123.45', '$###.00') -> 123.45f``  
___


<a name="toInteger" ></a>

### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
숫자 또는 문자열을 정수 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. long, float, double을 자릅니다.  
* ``toInteger(123) -> 123``  
* ``toInteger('123') -> 123``  
* ``toInteger('$123', '$###') -> 123``  
___


<a name="toLong" ></a>

### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
숫자 또는 문자열을 긴 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. float, double을 자릅니다.  
* ``toLong(123) -> 123``  
* ``toLong('123') -> 123``  
* ``toLong('$123', '$###') -> 123``  
___


<a name="toShort" ></a>

### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
숫자 또는 문자열을 짧은 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. integer, long, float, double을 자릅니다.  
* ``toShort(123) -> 123``  
* ``toShort('123') -> 123``  
* ``toShort('$123', '$###') -> 123``  
___


<a name="toString" ></a>

### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
기본 데이터 형식을 문자열로 변환합니다. 숫자 및 날짜의 경우 형식을 지정할 수 있습니다. 지정하지 않으면 시스템 기본값은 picked.Java입니다. 숫자에는 10진수 형식이 사용됩니다. 가능한 모든 날짜 형식은 Java SimpleDateFormat을 참조하세요. 기본 형식은 yyyy-MM-dd입니다.  
* ``toString(10) -> '10'``  
* ``toString('engineer') -> 'engineer'``  
* ``toString(123456.789, '##,###.##') -> '123,456.79'``  
* ``toString(123.78, '000000.000') -> '000123.780'``  
* ``toString(12345, '##0.#####E0') -> '12.345E3'``  
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``  
* ``isNull(toString(toDate('2018-12-31', 'MM/dd/yy'))) -> true``  
* ``toString(4 == 20) -> 'false'``  
___


<a name="toTimestamp" ></a>

### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
지정된 선택적 타임스탬프 형식으로 문자열을 타임스탬프로 변환합니다. 타임스탬프를 생략하면 기본 패턴인 yyyy-[M]M-[d]d hh:mm:ss[.f...]가 사용됩니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 타임 스탬프는 999 값으로 최대 밀리 초 정확도를 지원합니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('2016-12-31T00:12:00', 'yyyy-MM-dd\'T\'HH:mm:ss', 'PST') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss') -> toTimestamp('2016-12-31 00:12:00')``  
* ``millisecond(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___


<a name="toUTC" ></a>

### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
타임스탬프를 UTC로 변환합니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 기본적으로 현재 표준 시간대가 사용됩니다. 사용 가능한 형식은 Java의 `SimpleDateFormat` 클래스를 참조하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toUTC(currentTimestamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``toUTC(currentTimestamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  



<a name="translate" ></a>

### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
하나의 문자 세트를 문자열의 다른 문자 세트로 바꿉니다. 문자는 1:1로 대체됩니다.  
* ``translate('(bojjus)', '()', '[]') -> '[bojjus]'``  
* ``translate('(gunchus)', '()', '[') -> '[gunchus'``  
___


<a name="trim" ></a>

### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
선행 및 후행 문자열을 삭제합니다. 두 번째 매개 변수를 지정하지 않으면 공백을 삭제합니다. 그렇지 않은 경우 두 번째 매개 변수에 지정된 모든 문자를 삭제합니다.  
* ``trim('  dumbo  ') -> 'dumbo'``  
* ``trim('!--!du!mbo!', '-!') -> 'du!mbo'``  
___


<a name="true" ></a>

### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
항상 true 값을 반환합니다. 'true'라는 열이 있는 경우 함수 `syntax(true())`를 사용합니다.  
* ``(10 + 20 == 30) -> true``  
* ``(10 + 20 == 30) -> true()``  
___


<a name="typeMatch" ></a>

### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
열의 형식과 일치합니다. 패턴 식에서만 사용할 수 있습니다. number는 short, integer, long, double, float 또는 decimal과 일치하고, integral은 short, integer, long과 일치하고, fractional은 double, float, decimal과 일치하고, datetime은 date 또는 timestamp 형식과 일치합니다.  
* ``typeMatch(type, 'number')``  
* ``typeMatch('date', 'datetime')``  
___


<a name="unescape" ></a>

### <code>unescape</code>
<code><b>unescape(<i>&lt;string_to_escape&gt;</i> : string, <i>&lt;format&gt;</i> : string) => string</b></code><br/><br/>
형식에 따라 문자열을 이스케이프 해제합니다. 허용되는 형식의 리터럴 값은 'json', 'xml', 'ecmascript', 'html', 'java'입니다.
* ```unescape('{\\\\\"value\\\\\": 10}', 'json')```
* ```'{\\\"value\\\": 10}'```
___


<a name="unfold" ></a>

### <code>unfold</code>
<code><b>unfold (<i>&lt;value1&gt;</i>: array) => any</b></code><br/><br/>
배열을 행 집합으로 펼치고 모든 행의 나머지 열에 대한 값을 반복합니다.
*   ``unfold(addresses) => any``
*   ``unfold( @(name = salesPerson, sales = salesAmount) ) => any``
___  


<a name="unhex" ></a>

### <code>unhex</code>
<code><b>unhex(<i>\<value1\></i>: string) => binary</b></code><br/><br/>
16진 문자열 표현을 이진 값으로 표시합니다. sha2, md5와 함께 사용하여 문자열에서 이진 표현으로 변환할 수 있습니다. *    ``unhex('1fadbe') -> toBinary([toByte(0x1f), toByte(0xad), toByte(0xbe)])``
*    ``unhex(md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))) -> toBinary([toByte(0x4c),toByte(0xe8),toByte(0xa8),toByte(0x80),toByte(0xbd),toByte(0x62),toByte(0x1a),toByte(0x1f),toByte(0xfa),toByte(0xd0),toByte(0xbc),toByte(0xa9),toByte(0x05),toByte(0xe1),toByte(0xbc),toByte(0x5a)])``



<a name="union" ></a>

### <code>union</code>
<code><b>union(<i>&lt;value1&gt;</i>: array, <i>&lt;value2&gt;</i> : array) => array</b></code><br/><br/>
2개의 배열에서 고유 항목의 공용 집합을 반환합니다.
* ``union([10, 20, 30], [20, 40]) => [10, 20, 30, 40]``
___  
  


<a name="upper" ></a>

### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
문자열을 대문자로 바꿉니다.  
* ``upper('bojjus') -> 'BOJJUS'``  
___


<a name="uuid" ></a>

### <code>uuid</code>
<code><b>uuid() => string</b></code><br/><br/>
생성된 UUID를 반환합니다.  
* ``uuid()``  
___


<a name="variance" ></a>

### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
열의 분산을 구합니다.  
* ``variance(sales)``  
___


<a name="varianceIf" ></a>

### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
조건에 따라 열의 분산을 구합니다.  
* ``varianceIf(region == 'West', sales)``  
___


<a name="variancePopulation" ></a>

### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
열의 모집단 분산을 구합니다.  
* ``variancePopulation(sales)``  
___


<a name="variancePopulationIf" ></a>

### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
조건에 따라 열의 모집단 분산을 구합니다.  
* ``variancePopulationIf(region == 'West', sales)``  
___


<a name="varianceSample" ></a>

### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
열의 불편 분산을 구합니다.  
* ``varianceSample(sales)``  
___


<a name="varianceSampleIf" ></a>

### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
조건에 따라 열의 불편 분산을 구합니다.  
* ``varianceSampleIf(region == 'West', sales)``  



<a name="weekOfYear" ></a>

### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
지정된 날짜의 연중 주를 가져옵니다.  
* ``weekOfYear(toDate('2008-02-20')) -> 8``  
___


<a name="weeks" ></a>

### <code>weeks</code>
<code><b>weeks(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
기간(주)의 시간(밀리초).  
* ``weeks(2) -> 1209600000L``  
___


<a name="xor" ></a>

### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
논리 XOR 연산자입니다. ^ 연산자와 같습니다.  
* ``xor(true, false) -> true``  
* ``xor(true, true) -> false``  
* ``true ^ false -> true``  
___


<a name="year" ></a>

### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
날짜의 연도 값을 구합니다.  
* ``year(toDate('2012-8-8')) -> 2012``  

## <a name="next-steps"></a>다음 단계

[식 작성기를 사용하는 방법을 알아보세요](concepts-data-flow-expression-builder.md).