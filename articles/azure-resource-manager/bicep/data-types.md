---
title: Bicep의 데이터 형식
description: Bicep에서 사용 가능한 데이터 형식 설명
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 31f2c6e979acb3b0b622bc63ffb8a2845179491d
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026866"
---
# <a name="data-types-in-bicep"></a>Bicep의 데이터 형식

이 문서에서는 [Bicep](./overview.md)에서 지원되는 데이터 형식을 설명합니다.

## <a name="supported-types"></a>지원되는 형식

Bicep 내에서 다음 데이터 형식을 사용할 수 있습니다.

* array
* 부울
* int
* 개체
* secureObject - Bicep에서 한정자로 표시됨
* secureString - Bicep에서 한정자로 표시됨
* 문자열

## <a name="arrays"></a>배열

배열은 왼쪽 대괄호(`[`)로 시작하고 오른쪽 대괄호(`]`)로 끝납니다. Bicep에서는 배열은 여러 줄로 선언해야 합니다. 값 사이에 쉼표를 사용하지 마세요.

배열에서 각 항목은 [임의 형식](bicep-functions-any.md)으로 표시됩니다. 각 항목이 동일한 데이터 형식인 배열 또는 서로 다른 데이터 형식을 포함하는 배열이 있을 수 있습니다.

Bicep의 배열은 0부터 사용됩니다. 다음 예제에서 `exampleArray[0]` 식은 1로 계산되고 `exampleArray[2]` 식은 3으로 계산됩니다. 인덱서의 인덱스는 다른 식일 수 있습니다. `exampleArray[index]` 식은 2로 계산됩니다. 정수 인덱서는 배열 형식의 식에만 사용할 수 있습니다.

```bicep
var index = 1

var exampleArray = [
  1
  2
  3
]
```

Bicep에서는 문자열 기반 인덱서가 허용됩니다.

```bicep
param environment string = 'prod'

var environmentSettings = {
  dev: {
    name: 'dev'
  }
  prod: {
    name: 'prod'
  }
}
```

environmentSettings[‘dev’] 식은 다음 개체로 계산됩니다.

```bicep
{
  name: 'dev'
}
```

다음 예제에서는 여러 다른 형식의 배열을 보여 줍니다.

```bicep
var mixedArray = [
  resourceGroup().name
  1
  true
  'example string'
]
```

## <a name="booleans"></a>부울

부울 값을 지정하는 경우 `true` 또는 `false`를 사용합니다. 값을 따옴표로 묶지 마세요.

```bicep
param exampleBool bool = true
```

## <a name="integers"></a>정수

정수 값을 지정하는 경우 따옴표를 사용하지 마세요.

```bicep
param exampleInt int = 1
```

인라인 매개 변수로 전달된 정수의 경우 값 범위는 배포에 사용하는 SDK 또는 명령줄 도구를 통해 제한될 수 있습니다. 예를 들어, PowerShell을 사용하여 Bicep을 배포하는 경우 정수 형식의 범위는 -2147483648 ~ 2147483647일 수 있습니다. 이 제한을 피하려면 [매개 변수 파일](parameter-files.md)에 큰 정수 값을 지정합니다. 리소스 종류는 정수 속성에 대해 고유한 한도를 적용합니다.

부동 소수점, 10진 또는 이진 형식은 현재 지원되지 않습니다.

## <a name="objects"></a>개체

개체는 왼쪽 중괄호(`{`)로 시작하고 오른쪽 중괄호(`}`)로 끝납니다. Bicep에서 개체는 여러 줄로 선언해야 합니다. 개체의 각 속성은 키와 값으로 구성됩니다. 키와 값은 콜론(`:`)으로 구분합니다. 개체는 모든 형식의 속성을 허용합니다.

Bicep에서는 키는 따옴표로 묶지 않습니다. 속성 사이에 쉼표를 사용하지 마세요.

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

속성 접근자는 개체의 속성에 액세스하는 데 사용됩니다. `.` 연산자를 사용하여 구성됩니다. 예를 들면 다음과 같습니다.

```bicep
var x = {
  y: {
    z: 'Hello`
    a: true
  }
  q: 42
}
```

이전 선언이 지정된 경우 식 x.y.z는 리터럴 문자열 ‘Hello’로 평가됩니다. 마찬가지로 식 x.q는 정수 리터럴 42로 평가됩니다.

속성 접근자는 개체 유형 및 개체 리터럴의 매개변수 및 변수를 포함한 모든 개체와 함께 사용할 수 있습니다. 개체가 아닌 형식의 식에서 속성 접근자를 사용하면 오류가 발생합니다.

## <a name="strings"></a>문자열

Bicep에서 문자열은 작은 따옴표로 표시되며 한 줄에 선언되어야 합니다. 코드 포인트가 *0* 에서 *10FFFF* 사이인 모든 유니코드 문자가 허용됩니다.

```bicep
param exampleString string = 'test value'
```

다음 표에서는 백슬래시(`\`) 문자로 이스케이프되어야 하는 예약 문자 세트를 나열합니다.

| 이스케이프 시퀀스 | 표시 값 | 참고 |
|:-|:-|:-|
| \\ | \ ||
| \' | ' ||
| \n | LF(줄 바꿈) ||
| \r | CR(캐리지 반환) ||
| \t | 탭 문자 ||
| \u{x} | 유니코드 코드 포인트 *x* | *x* 는 *0* 과 *10FFFF*(모두 포함) 사이의 16진수 코드 포인트 값을 나타냅니다. 앞에 오는 0은 허용됩니다. *FFFF* 이상의 코드 포인트는 서로게이트 쌍으로 내보내집니다.
| \$ | $ | *{* 뒤에 오는 경우에만 이스케이프하면 됩니다. |

```bicep
// evaluates to "what's up?"
var myVar = 'what\'s up?'
```

Bicep의 모든 문자열은 보간을 지원합니다. 식을 주입하려면 *${* 및 *}`로 둘러쌉니다. 참조되는 식은 여러 줄에 걸쳐 있을 수 없습니다.

```bicep
var storageName = 'storage${uniqueString(resourceGroup().id)}
```

## <a name="multi-line-strings"></a>다중 선 문자열

Bicep에서 다중 선 문자열은 선택적으로 새 줄(여는 시퀀스)로 이어지는 3개의 작은따옴표 문자(`'''`)와 3개의 작은따옴표 문자(`'''` - 닫는 시퀀스) 사이에 정의됩니다. 여는 시퀀스와 닫는 시퀀스 사이에 입력된 문자를 그대로 읽으며 이스케이프가 필요하거나 가능하지 않습니다.

> [!NOTE]
> Bicep 파서는 모든 문자를 있는 그대로 읽기 때문에 Bicep 파일의 줄 끝에 따라 줄바꿈을 `\r\n` 또는 `\n`으로 해석할 수 있습니다.
> 보간은 현재 다중 선 문자열에서 지원되지 않습니다.
> `'''`가 포함된 다중 선 문자열은 지원되지 않습니다.

```bicep
// evaluates to "hello!"
var myVar = '''hello!'''

// evaluates to "hello!" because the first newline is skipped
var myVar2 = '''
hello!'''

// evaluates to "hello!\n" because the final newline is included
var myVar3 = '''
hello!
'''

// evaluates to "  this\n    is\n      indented\n"
var myVar4 = '''
  this
    is
      indented
'''

// evaluates to "comments // are included\n/* because everything is read as-is */\n"
var myVar5 = '''
comments // are included
/* because everything is read as-is */
'''

// evaluates to "interpolation\nis ${blocked}"
// note ${blocked} is part of the string, and is not evaluated as an expression
myVar6 = '''interpolation
is ${blocked}'''
```

## <a name="secure-strings-and-objects"></a>보안 문자열 및 개체

보안 문자열은 문자열과 동일한 형식을 사용하고 보안 개체는 개체와 동일한 형식을 사용합니다. Bicep에서는 `@secure()` 한정자를 문자열 또는 개체에 추가합니다.

매개 변수를 보안 문자열 또는 보안 개체로 설정하면 매개 변수 값이 배포 기록에 저장되지 않고 로그되지 않습니다. 그러나 해당 보안 값을 보안 값이 필요하지 않은 속성으로 설정하면 값이 보호되지 않습니다. 예를 들어, 보안 문자열을 태그로 설정하면 해당 값이 일반 텍스트로 저장됩니다. 암호 및 비밀에 보안 문자열을 사용합니다.

다음 예제에서는 두 개의 보안 매개 변수를 보여줍니다.

```bicep
@secure()
param password string

@secure()
param configValues object
```

## <a name="next-steps"></a>다음 단계

Bicep의 구조 및 구문에 대해 자세히 알아보려면 [Bicep 파일 구조](./file.md)를 참조하세요.
