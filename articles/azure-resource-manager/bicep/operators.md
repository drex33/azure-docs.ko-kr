---
title: Bicep 연산자
description: Azure Resource Manager 배포에 사용할 수 있는 Bicep 연산자를 설명합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 80fc9e4e1285d86858a476feba30621a7afe1c79
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221049"
---
# <a name="bicep-operators"></a>Bicep 연산자

이 문서에서는 Bicep 연산자에 대해 설명합니다. 연산자는 값을 계산하거나, 값을 비교하거나, 조건을 평가하는 데 사용됩니다. Bicep 연산자에는 다음 네 가지 유형이 있습니다.

- [접근자(accessor)](#accessor)
- [비교](#comparison)
- [논리](#logical)
- [numeric](#numeric)

## <a name="operator-precedence-and-associativity"></a>연산자 우선 순위 및 결합성

아래 연산자는 우선 순위의 내림차순으로 나열되어 있습니다(위에 나열된 연산자의 우선 순위가 더 높음). 동일한 수준에 나열된 연산자는 우선 순위가 같습니다.

| 기호 | 연산 유형 | associativity |
|:-|:-|:-|
| `(` `)` `[` `]` `.` `::` | 괄호, 배열 인덱서, 속성 접근자, 중첩된 리소스 접근자  | 왼쪽에서 오른쪽 |
| `!` `-` | 단항 | 오른쪽에서 왼쪽 |
| `%` `*` `/` | 곱하기 | 왼쪽에서 오른쪽 |
| `+` `-` | 더하기 | 왼쪽에서 오른쪽 |
| `<=` `<` `>` `>=` | 관계 | 왼쪽에서 오른쪽 |
| `==` `!=` `=~` `!~` | 같음 | 왼쪽에서 오른쪽 |
| `&&` | 논리적 AND | 왼쪽에서 오른쪽 |
| `||` | 논리적 OR | 왼쪽에서 오른쪽 |
| `?` `:` | 3개로 구성된 조건식 | 오른쪽에서 왼쪽
| `??` | Coalesce | 왼쪽에서 오른쪽

`(`와 `)` 사이에 식을 포함하면 기본 Bicep 연산자 우선 순위를 재정의할 수 있습니다. 예를 들어, 식 x + y / z는 나누기를 먼저 평가한 다음, 더하기를 평가합니다. 그러나 식 (x + y) / z는 더하기를 먼저 평가하고 나누기를 두 번째로 평가합니다.

## <a name="accessor"></a>접근자

접근자 연산자는 중첩된 리소스와 개체 속성에 액세스하는 데 사용됩니다.

| 연산자 | Name | Description |
| ---- | ---- | ---- |
| `[]` | [인덱스 접근자](./operators-access.md#index-accessor) | 개체에서 배열 또는 속성의 요소에 액세스합니다. |
| `.` | [함수 접근자](./operators-access.md#function-accessor) | 리소스에서 함수를 호출합니다. |
| `::` | [중첩된 리소스 접근자](./operators-access.md#nested-resource-accessor) | 부모 리소스 외부에서 중첩된 리소스에 액세스합니다. |
| `.` | [속성 접근자](./operators-access.md#property-accessor) | 개체 속성에 액세스합니다. |

## <a name="comparison"></a>비교

비교 연산자는 값을 비교하고 `true` 또는 `false`를 반환합니다.

| 연산자 | Name | 설명 |
| ---- | ---- | ---- |
| `>=` | [크거나 같음](./operators-comparison.md#greater-than-or-equal-) | 첫 번째 값이 두 번째 값보다 크거나 같은지 평가합니다. |
| `>`  | [보다 큼](./operators-comparison.md#greater-than-) | 첫 번째 값이 두 번째 값보다 큰지 평가합니다. |
| `<=` | [작거나 같음](./operators-comparison.md#less-than-or-equal-) | 첫 번째 값이 두 번째 값보다 작거나 같은지 평가합니다. |
| `<`  | [보다 작음](./operators-comparison.md#less-than-) | 첫 번째 값이 두 번째 값보다 작은지 평가합니다. |
| `==` | [같음](./operators-comparison.md#equals-) | 두 값이 같은지 평가합니다. |
| `!=` | [같지 않음](./operators-comparison.md#not-equal-) | 두 값이 같지 **않은지** 평가합니다. |
| `=~` | [같음(대/소문자 구분 안 함)](./operators-comparison.md#equal-case-insensitive-) | 두 값이 같은지 확인하기 위해 대/소문자를 무시합니다. |
| `!~` | [같지 않음(대/소문자 구분 안 함)](./operators-comparison.md#not-equal-case-insensitive-) | 두 값이 같지 **않은지** 확인하기 위해 대/소문자를 무시합니다. |

## <a name="logical"></a>논리

논리 연산자는 부울 값을 평가하거나, null이 아닌 값을 반환하거나, 조건식을 평가합니다.

| 연산자 | Name | 설명 |
| ---- | ---- | ---- |
| `&&` | [And](./operators-logical.md#and-) | 모든 값이 true이면 `true`를 반환합니다. |
| `||`| [Or](./operators-logical.md#or-) | 한쪽 값이 true이면 `true`를 반환합니다. |
| `!` | [Not](./operators-logical.md#not-) | 부울 값을 부정합니다. |
| `??` | [Coalesce](./operators-logical.md#coalesce-) | null이 아닌 첫 번째 값을 반환합니다. |
| `?` `:` | [조건 식](./operators-logical.md#conditional-expression--) | True 또는 false의 조건을 평가하고 값을 반환합니다. |

## <a name="numeric"></a>숫자

숫자 연산자는 정수를 사용하여 계산을 수행하고 정수 값을 반환합니다.

| 연산자 | Name | 설명 |
| ---- | ---- | ---- |
| `*` | [곱하기](./operators-numeric.md#multiply-) | 두 정수를 곱합니다. |
| `/` | [나누기](./operators-numeric.md#divide-) | 정수를 정수로 나눕니다. |
| `%` | [모듈로](./operators-numeric.md#modulo-) | 정수를 정수로 나누고 나머지를 반환합니다. |
| `+` | [추가](./operators-numeric.md#add-) | 두 개의 정수를 더합니다. |
| `-` | [빼기](./operators-numeric.md#subtract--) | 정수에서 정수를 뺍니다. |
| `-` | [빼기](./operators-numeric.md#minus--) | 정수에 `-1`을 곱합니다. |

> [!NOTE]
> 빼기(subtract) 및 빼기(minus)는 동일한 연산자를 사용합니다. 빼기(subtract)는 두 개의 피연산자를 사용하고 빼기(minus)는 피연산자 하나를 사용하기 때문에 기능이 서로 다릅니다.


## <a name="next-steps"></a>다음 단계

- Bicep 파일을 만들려면 [빠른 시작: Visual Studio Code로 Bicep 파일 만들기](./quickstart-create-bicep-use-visual-studio-code.md)를 참조하세요.
- Bicep 유형 오류를 해결하는 방법에 대한 정보는 [Bicep의 모든 기능](./bicep-functions-any.md)을 참조하세요.
- Bicep 및 JSON 구문을 비교하려면 [템플릿에 대한 JSON 및 Bicep 비교](./compare-template-syntax.md)를 참조하세요.
- Bicep 함수의 예는 [Bicep 함수](./bicep-functions.md)를 참조하세요.
