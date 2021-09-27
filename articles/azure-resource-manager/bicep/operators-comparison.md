---
title: Bicep 비교 연산자
description: 값을 비교하는 Bicep 비교 연산자에 대해 설명합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/07/2021
ms.openlocfilehash: 1a28da26a3c97982bb0e06deebae6ae68b1eb7d9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744407"
---
# <a name="bicep-comparison-operators"></a>Bicep 비교 연산자

비교 연산자는 값을 비교하고 `true` 또는 `false`를 반환합니다. 예제를 실행하려면 Azure CLI 또는 Azure PowerShell을 사용하여 [Bicep 파일을 배포](./quickstart-create-bicep-use-visual-studio-code.md#deploy-the-bicep-file)합니다.

| 연산자 | 이름 |
| ---- | ---- |
| `>=` | [크거나 같음](#greater-than-or-equal-) |
| `>`  | [보다 큼](#greater-than-) |
| `<=` | [작거나 같음](#less-than-or-equal-) |
| `<`  | [보다 작음](#less-than-) |
| `==` | [같음](#equals-) |
| `!=` | [같지 않음](#not-equal-) |
| `=~` | [같음(대/소문자 구분 안 함)](#equal-case-insensitive-) |
| `!~` | [같지 않음(대/소문자 구분 안 함)](#not-equal-case-insensitive-) |

## <a name="greater-than-or-equal-"></a>크거나 같음 >=

`operand1 >= operand2`

첫 번째 값이 두 번째 값보다 크거나 같은지 평가합니다.

### <a name="operands"></a>피연산자

| 피연산자 | 유형 | Description |
| ---- | ---- | ---- |
| `operand1` | 정수, 문자열 | 비교의 첫 번째 값입니다. |
| `operand2` | 정수, 문자열 | 비교의 두 번째 값입니다. |

### <a name="return-value"></a>반환 값

첫 번째 값이 두 번째 값보다 크거나 같으면 `true`가 반환됩니다. 그렇지 않으면 `false`이 반환됩니다.

### <a name="example"></a>예제

정수 쌍과 문자열 쌍이 비교됩니다.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'A'
param secondString string = 'A'

output intGtE bool = firstInt >= secondInt
output stringGtE bool = firstString >= secondString
```

예제의 출력:

| Name | 유형 | 값 |
| ---- | ---- | ---- |
| `intGtE` | boolean | true |
| `stringGtE` | boolean | true |

## <a name="greater-than-"></a>보다 큼 >

`operand1 > operand2`

첫 번째 값이 두 번째 값보다 큰지 평가합니다.

### <a name="operands"></a>피연산자

| 피연산자 | 유형 | Description |
| ---- | ---- | ---- |
| `operand1` | 정수, 문자열 | 비교의 첫 번째 값입니다. |
| `operand2` | 정수, 문자열 | 비교의 두 번째 값입니다. |

### <a name="return-value"></a>반환 값

첫 번째 값이 두 번째 값보다 크면 `true`가 반환됩니다. 그렇지 않으면 `false`이 반환됩니다.

### <a name="example"></a>예제

정수 쌍과 문자열 쌍이 비교됩니다.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'bend'
param secondString string = 'band'

output intGt bool = firstInt > secondInt
output stringGt bool = firstString > secondString
```

예제의 출력:

**bend** 의 **e** 는 첫 번째 문자열을 더 크게 만듭니다.

| 이름 | 유형 | 값 |
| ---- | ---- | ---- |
| `intGt` | boolean | true |
| `stringGt` | boolean | true |

## <a name="less-than-or-equal-"></a>작거나 같음 <=

`operand1 <= operand2`

첫 번째 값이 두 번째 값보다 작거나 같은지 평가합니다.

### <a name="operands"></a>피연산자

| 피연산자 | 유형 | Description |
| ---- | ---- | ---- |
| `operand1` | 정수, 문자열 | 비교의 첫 번째 값입니다. |
| `operand2` | 정수, 문자열 | 비교의 두 번째 값입니다. |

### <a name="return-value"></a>반환 값

첫 번째 값이 두 번째 값보다 작거나 같으면 `true`가 반환됩니다. 그렇지 않으면 `false`이 반환됩니다.

### <a name="example"></a>예제

정수 쌍과 문자열 쌍이 비교됩니다.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'demo'

output intLtE bool = firstInt <= secondInt
output stringLtE bool = firstString <= secondString
```

예제의 출력:

| Name | 유형 | 값 |
| ---- | ---- | ---- |
| `intLtE` | boolean | true |
| `stringLtE` | boolean | true |

## <a name="less-than-"></a>보다 작음 <

`operand1 < operand2`

첫 번째 값이 두 번째 값보다 작은지 평가합니다.

### <a name="operands"></a>피연산자

| 피연산자 | 유형 | Description |
| ---- | ---- | ---- |
| `operand1` | 정수, 문자열 | 비교의 첫 번째 값입니다. |
| `operand2` | 정수, 문자열 | 비교의 두 번째 값입니다. |

### <a name="return-value"></a>반환 값

첫 번째 값이 두 번째 값보다 작으면 `true`가 반환됩니다. 그렇지 않으면 `false`이 반환됩니다.

### <a name="example"></a>예제

정수 쌍과 문자열 쌍이 비교됩니다.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'Demo'

output intLt bool = firstInt < secondInt
output stringLt bool = firstString < secondString
```

예제의 출력:

소문자가 대문자보다 작기 때문에 문자열은 `true`입니다.

| 이름 | 유형 | 값 |
| ---- | ---- | ---- |
| `intLt` | boolean | true |
| `stringLt` | boolean | true |

## <a name="equals-"></a>같음 ==

`operand1 == operand2`

값이 같은지 평가합니다.

### <a name="operands"></a>피연산자

| 피연산자 | 유형 | Description |
| ---- | ---- | ---- |
| `operand1` | 문자열, 정수, 부울, 배열, 개체 | 비교의 첫 번째 값입니다. |
| `operand2` | 문자열, 정수, 부울, 배열, 개체 | 비교의 두 번째 값입니다. |

### <a name="return-value"></a>반환 값

피연산자가 같으면 `true`가 반환됩니다. 피연산자가 다르면 `false`가 반환됩니다.

### <a name="example"></a>예제

정수, 문자열 및 부울 쌍이 비교됩니다.

```bicep
param firstInt int = 5
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'demo'

param firstBool bool = true
param secondBool bool = true

output intEqual bool = firstInt == secondInt
output stringEqual bool = firstString == secondString
output boolEqual bool = firstBool == secondBool
```

예제의 출력:

| Name | 유형 | 값 |
| ---- | ---- | ---- |
| `intEqual` | boolean | true |
| `stringEqual` | boolean | true |
| `boolEqual` | boolean | true |

배열을 비교할 때 두 배열의 요소와 순서가 같아야 합니다. 배열은 서로 할당할 필요가 없습니다.

```bicep
var array1 = [
  1
  2
  3
]

var array2 = [
  1
  2
  3
]

var array3 = array2

var array4 = [
  3
  2
  1
]

output sameElements bool = array1 == array2 // returns true because arrays are defined with same elements
output assignArray bool = array2 == array3 // returns true because one array was defined as equal to the other array
output differentOrder bool = array4 == array1 // returns false because order of elements is different
```

예의 출력:

| Name | 유형 | 값 |
| ---- | ---- | ---- |
| sameElements | bool | true |
| 배열 할당 | bool | true |
| differentOrder | bool | false |

개체를 비교할 때 속성 이름과 값은 같아야 합니다. 속성은 같은 순서로 정의할 필요가 없습니다.

```bicep
var object1 = {
  prop1: 'val1'
  prop2: 'val2'
}

var object2 = {
  prop1: 'val1'
  prop2: 'val2'
}

var object3 = {
  prop2: 'val2'
  prop1: 'val1'
}

var object4 = object3

var object5 = {
  prop1: 'valX'
  prop2: 'valY'
}

output sameObjects bool = object1 == object2 // returns true because both objects defined with same properties
output differentPropertyOrder bool = object3 == object2 // returns true because both objects have same properties even though order is different
output assignObject bool = object4 == object1 // returns true because one object was defined as equal to the other object
output differentValues bool = object5 == object1 // returns false because values are different
```

예의 출력:

| Name | 유형 | 값 |
| ---- | ---- | ---- |
| sameObjects | bool | true |
| differentPropertyOrder | bool | true |
| assignObject | bool | true |
| differentValues | bool | false |

## <a name="not-equal-"></a>같지 않음 !=

`operand1 != operand2`

두 값이 같지 **않은지** 평가합니다.

### <a name="operands"></a>피연산자

| 피연산자 | 유형 | Description |
| ---- | ---- | ---- |
| `operand1` | 문자열, 정수, 부울, 배열, 개체 | 비교의 첫 번째 값입니다. |
| `operand2` | 문자열, 정수, 부울, 배열, 개체 | 비교의 두 번째 값입니다. |

### <a name="return-value"></a>반환 값

피연산자가 같지 **않으면** `true`가 반환됩니다. 피연산자가 같으면 `false`가 반환됩니다.

### <a name="example"></a>예제

정수, 문자열 및 부울 쌍이 비교됩니다.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'test'

param firstBool bool = false
param secondBool bool = true

output intNotEqual bool = firstInt != secondInt
output stringNotEqual bool = firstString != secondString
output boolNotEqual bool = firstBool != secondBool
```

예제의 출력:

| Name | 유형 | 값 |
| ---- | ---- | ---- |
| `intNotEqual` | boolean | true |
| `stringNotEqual` | boolean | true |
| `boolNotEqual` | boolean | true |

배열 및 개체의 경우 [equals](#equals-)의 예제를 참조 하세요.

## <a name="equal-case-insensitive-"></a>같음(대/소문자 구분 안 함) =~

`operand1 =~ operand2`

두 값이 같은지 확인하기 위해 대/소문자를 무시합니다.

### <a name="operands"></a>피연산자

| 피연산자 | 유형 | Description |
| ---- | ---- | ---- |
| `operand1`  | 문자열 | 비교의 첫 번째 문자열입니다. |
| `operand2`  | 문자열 | 비교의 두 번째 문자열입니다. |

### <a name="return-value"></a>반환 값

문자열이 같으면 `true`가 반환됩니다. 그렇지 않으면 `false`이 반환됩니다.

### <a name="example"></a>예제

대/소문자가 혼합된 문자열을 비교합니다.

```bicep
param firstString string = 'demo'
param secondString string = 'DEMO'

param thirdString string = 'demo'
param fourthString string = 'TEST'

output strEqual1 bool = firstString =~ secondString
output strEqual2 bool = thirdString =~ fourthString
```

예제의 출력:

| Name | 유형 | 값 |
| ---- | ---- | ---- |
| `strEqual1` | boolean | true |
| `strEqual2` | boolean | false |

## <a name="not-equal-case-insensitive-"></a>같지 않음(대/소문자 구분 안 함) !~

`operand1 !~ operand2`

두 값이 같지 **않은지** 확인하기 위해 대/소문자를 무시합니다.

### <a name="operands"></a>피연산자

| 피연산자 | 유형 | Description |
| ---- | ---- | ---- |
| `operand1` | 문자열 | 비교의 첫 번째 문자열입니다. |
| `operand2` | 문자열 | 비교의 두 번째 문자열입니다. |

### <a name="return-value"></a>반환 값

문자열이 같지 **않으면** `true`가 반환됩니다. 그렇지 않으면 `false`이 반환됩니다.

### <a name="example"></a>예제

대/소문자가 혼합된 문자열을 비교합니다.

```bicep
param firstString string = 'demo'
param secondString string = 'TEST'

param thirdString string = 'demo'
param fourthString string = 'DeMo'

output strNotEqual1 bool = firstString !~ secondString
output strEqual2 bool = thirdString !~ fourthString
```

예제의 출력:

| Name | 유형 | 값 |
| ---- | ---- | ---- |
| `strNotEqual1` | boolean | true |
| `strNotEqual2` | boolean | false |

## <a name="next-steps"></a>다음 단계

- Bicep 파일을 만들려면 [빠른 시작: Visual Studio Code로 Bicep 파일 만들기](./quickstart-create-bicep-use-visual-studio-code.md)를 참조하세요.
- Bicep 유형 오류를 해결하는 방법에 대한 정보는 [Bicep의 모든 기능](./bicep-functions-any.md)을 참조하세요.
- Bicep 및 JSON 구문을 비교하려면 [템플릿에 대한 JSON 및 Bicep 비교](./compare-template-syntax.md)를 참조하세요.
- Bicep 함수의 예는 [Bicep 함수](./bicep-functions.md)를 참조하세요.
