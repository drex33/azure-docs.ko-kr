---
title: IoT 커넥터 및 사용자 지정 함수 - Azure Healthcare API
description: 이 문서에서는 디바이스 매핑 템플릿 내에서 IoT 커넥터 및 사용자 지정 함수를 사용하는 방법을 알아봅니다.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 11/07/2021
ms.author: jasteppe
ms.openlocfilehash: 6b21fed4ada15c0c4adf1dd6fc9575d5c99c9d36
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990492"
---
# <a name="how-to-use-custom-functions"></a>사용자 지정 함수를 사용하는 방법

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

**JmesPath를** 식 언어로 사용할 때 많은 함수를 사용할 수 있습니다. JmesPath 사양의 일부로 사용할 수 있는 함수 외에도 많은 사용자 지정 함수를 사용할 수 있습니다. 이 문서에서는 정규화 프로세스 중에 디바이스 매핑 템플릿에 사용할 IoT 커넥터 관련 사용자 지정 함수에 대해 설명합니다.

> [!TIP]
> JmesPath 함수에 대한 자세한 내용은 JmesPath [사양](https://jmespath.org/specification.html#built-in-functions)을 참조하세요.

## <a name="function-signature"></a>함수 시그니처

각 함수에는 JmesPath 사양을 따르는 서명이 있습니다. 이 서명은 다음과 같이 나타낼 수 있습니다.

```jmespath
return_type function_name(type $argname)
```

서명은 인수에 유효한 형식을 나타냅니다. 인수에 대해 잘못된 형식이 전달되면 오류가 발생합니다.

> [!NOTE]
> 수학 관련 함수가 완료되면 최종 결과가 C# [long](/dotnet/csharp/language-reference/builtin-types/integral-numeric-types#characteristics-of-the-integral-types) 값에 맞을 수 **있어야 합니다.** 최종 결과가 C# long 값에 맞지 않으면 수학 오류가 발생합니다.

## <a name="exception-handling"></a>예외 처리

이벤트 처리 수명 주기 내의 다양한 지점에서 예외가 발생할 수 있습니다. 발생할 수 있는 다양한 지점은 다음과 같습니다.

|작업|When|템플릿 구문 분석 중에 발생할 수 있는 예외|결과|
|------|----|-------------------------------------------------|-------|
|**템플릿 구문 분석**|새 메시지 일괄 처리가 수신될 때마다 디바이스 매핑 템플릿이 로드되고 구문 분석됩니다.|템플릿을 구문 분석하지 못했습니다.|시스템은 구문 분석이 성공할 때까지 최신 디바이스 매핑 템플릿을 다시 로드하고 구문 분석하려고 시도합니다. 구문 분석이 성공할 때까지 새 메시지는 처리되지 않습니다.|
|**템플릿 구문 분석**|새 메시지 일괄 처리가 수신될 때마다 디바이스 매핑 템플릿이 로드되고 구문 분석됩니다.|식을 구문 분석하지 못했습니다.|시스템은 구문 분석이 성공할 때까지 최신 디바이스 매핑 템플릿을 다시 로드하고 구문 분석하려고 시도합니다. 구문 분석이 성공할 때까지 새 메시지는 처리되지 않습니다.|
|**함수 실행**|메시지 내의 데이터에 대해 함수가 실행될 때마다|입력 데이터가 함수 시그니처의 입력 데이터와 일치하지 않습니다.|시스템에서 해당 메시지 처리를 중지합니다. 메시지가 다시 시도되지 않습니다.|
|**함수 실행**|메시지 내의 데이터에 대해 함수가 실행될 때마다|함수 설명에 나열된 다른 모든 예외입니다.|시스템에서 해당 메시지 처리를 중지합니다. 메시지가 다시 시도되지 않습니다.|

## <a name="mathematical-functions"></a>수학 함수

### <a name="add"></a>추가

```jmespath
number add(number $left, number $right)
```

왼쪽 인수를 오른쪽에 추가한 결과를 반환합니다.

예:

| 조건                       | 식       | 결과 |
|-----------------------------|------------------|--------|
| 해당 없음                         | add(10, 10)      | 20     |
| {"left": 40, "right": 50}   | add(왼쪽, 오른쪽) | 90     |
| {"left": 0, "right": 50}    | add(왼쪽, 오른쪽) | 50     |

### <a name="divide"></a>분할

```jmespath
number divide(number $left, number $right)
```

왼쪽 인수를 오른쪽으로 나눈 결과를 반환합니다.

예:

| 조건                       | 식          | 결과                           |
|-----------------------------|---------------------|----------------------------------|
| 해당 없음                         | divide(10, 10)      | 1                                |
| {"left": 40, "right": 50}   | divide(left, right) | 0.8                              |
| {"left": 0, "right": 50}    | divide(left, right) | 0                                |
| {"left": 50, "right": 0}    | divide(left, right) | 수학 오류: 0으로 나누기 |

### <a name="multiply"></a>곱하면

```jmespath
number multiply(number $left, number $right)
```

왼쪽 인수를 오른쪽에 곱한 결과를 반환합니다.

예:

| 조건                       | 식            | 결과 |
|-----------------------------|-----------------------|--------|
| 해당 없음                         | multiply(10, 10)      | 100    |
| {"left": 40, "right": 50}   | multiply(왼쪽, 오른쪽) | 2000   |
| {"left": 0, "right": 50}    | multiply(왼쪽, 오른쪽) | 0      |

### <a name="pow"></a>pow

```jmespath
number pow(number $left, number $right)
```

왼쪽 인수를 오른쪽의 권능으로 높인 결과를 반환합니다.

예:

| 조건                         | 식       | 결과                     |
|-------------------------------|------------------|----------------------------|
| 해당 없음                           | pow(10, 10)      | 10000000000                |
| {"left": 40, "right": 50}     | pow(왼쪽, 오른쪽) | 수학 오류: 오버플로 |
| {"left": 0, "right": 50}      | pow(왼쪽, 오른쪽) | 0                          |
| {"left": 100, "right": 0.5}   | pow(왼쪽, 오른쪽) | 10                         |

### <a name="subtract"></a>빼기(subtract)

```jmespath
number subtract(number $left, number $right)
```

왼쪽에서 오른쪽 인수를 뺀 결과를 반환합니다.

예:

| 조건                       | 식            | 결과 |
|-----------------------------|-----------------------|--------|
| 해당 없음                         | subtract(10, 10)      | 0      |
| {"left": 40, "right": 50}   | subtract(왼쪽, 오른쪽) | -10    |
| {"left": 0, "right": 50}    | subtract(왼쪽, 오른쪽) | -50    |

## <a name="string-functions"></a>문자열 함수

### <a name="insertstring"></a>insertString

```jmespath
string insertString(string $original, string $toInsert, number pos)
```

*toInsert* 값을 *문자열 원래* 에 삽입하여 새 문자열을 생성합니다. 문자열은 *원래* 문자열 *내의 위치 pos에* 삽입됩니다.

위치 인수가 0을 기반으로 하는 경우 0의 위치는 문자열 내의 첫 번째 문자를 참조합니다. 

제공된 위치 인수가 *원래* 의 길이 범위를 벗어나면 오류가 발생합니다.

예:

| 조건                                                     | 식                                         | 결과              |
|-----------------------------------------------------------|----------------------------------------------------|---------------------|
| {"original": "mple", "toInsert": "sa", "pos": 0}          | insertString(original, toInsert, pos)              | "sample"            |
| {"original": "toss", "toInsert": "cc", "pos": 2}         | insertString(original, toInsert, pos)              | "success"           |
| {"original": "myString", "toInsert": "^", "pos": 8}      | insertString(original, toInsert, pos)              | "myString!""        |
| {"original": "myString", "toInsert": "!!"}                | insertString(original, toInsert, length(original)) | "myString!""        |
| {"original": "myString", "toInsert": "^", "pos": 100}    | insertString(original, toInsert, pos)              | error: 범위를 벗어났습니다. |
| {"original": "myString", "toInsert": "^", "pos": -1}     | insertString(original, toInsert, pos)              | error: 범위를 벗어났습니다. |

## <a name="date-functions"></a>날짜 함수

### <a name="fromunixtimestamp"></a>fromUnixTimestamp

```jmespath
string fromUnixTimestamp(number $unixTimestampInSeconds)
```

지정된 Unix 타임스탬프에서 [ISO 8061](https://en.wikipedia.org/wiki/ISO_8601) 규격 타임스탬프를 생성합니다. 타임스탬프는 Epoch 이후의 시간(초)으로 표시됩니다(1970년 1월 1일).

예:

| 조건                 | 식              | 결과                  |
|-----------------------|-------------------------|-------------------------|
| {"unix": 1625677200} | fromUnixTimestamp(unix)  | "2021-07-07T17:00:00+0" |
| {"unix": 0}          | fromUnixTimestamp(unix)  | "1970-01-01T00:00:00+0" |

### <a name="fromunixtimestampms"></a>fromUnixTimestampMs

```jmespath
string fromUnixTimestampMs(number $unixTimestampInMs)
```

지정된 Unix 타임스탬프에서 [ISO 8061](https://en.wikipedia.org/wiki/ISO_8601) 규격 타임스탬프를 생성합니다. 타임스탬프는 Epoch 이후의 시간(밀리초)으로 표시됩니다(1970년 1월 1일).

예:

| 조건                    | 식                | 결과                  |
|--------------------------|---------------------------|-------------------------|
| {"unix": 1626799080000}  | fromUnixTimestampMs(unix) | "2021-07-20T16:38:00+0" |
| {"unix": 0}              | fromUnixTimestampMs(unix) | "1970-01-01T00:00:00+0" |

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT 커넥터 사용자 지정 함수를 사용하는 방법을 배웠습니다. 디바이스 매핑과 함께 사용자 지정 함수를 사용하는 방법을 알아보려면 다음을 참조하세요.

>[!div class="nextstepaction"]
>[디바이스 매핑을 사용하는 방법](how-to-use-device-mapping-iot.md)

(FHIR&#174;)는 HL7의 등록 상표이며 [HL7의](https://hl7.org/fhir/) 권한으로 사용됩니다.
