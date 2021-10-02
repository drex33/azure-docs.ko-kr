---
title: Bicep 함수 - 논리
description: 논리 값을 결정하기 위해 Bicep 파일에서 사용할 함수를 설명합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 4ab5f0382d320e31c650dcb6ebee68f39d6e43b1
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362985"
---
# <a name="logical-functions-for-bicep"></a>Bicep에 대한 논리 함수

Bicep은 `bool` 값을 부울로 변환하는 함수를 제공합니다.

Azure Resource Manager 템플릿의 대부분의 논리 함수는 Bicep에서 [논리 연산자](./operators-logical.md)로 대체되었습니다.

## <a name="bool"></a>bool

`bool(arg1)`

매개 변수를 부울로 변환합니다.

네임스페이스: [sys .](bicep-functions.md#namespaces-for-functions)

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |예 |문자열 또는 int |부울로 변환할 값입니다. |

### <a name="return-value"></a>반환 값

변환된 값의 부울입니다.

### <a name="examples"></a>예

다음 예제에서는 문자열 또는 정수에 bool을 사용하는 방법을 보여 줍니다.

```bicep
output trueString bool = bool('true')
output falseString bool = bool('false')
output trueInt bool = bool(1)
output falseInt bool = bool(0)
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 속성 | 유형 | 값 |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="next-steps"></a>다음 단계

* 논리 값과 관련된 다른 작업은 논리 [연산자 를](./operators-logical.md)참조하세요.
