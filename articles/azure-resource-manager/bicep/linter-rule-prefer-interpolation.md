---
title: Linter 규칙 - 보간 선호
description: Linter 규칙 - 보간 선호
ms.topic: conceptual
ms.date: 11/18/2021
ms.openlocfilehash: 292285d3ed13ef0a398a181949d8df191e1e33c7
ms.sourcegitcommit: 11ca7ba5a017429c22a6b0bc02acb70b83a2984a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132813870"
---
# <a name="linter-rule---prefer-interpolation"></a>Linter 규칙 - 보간 선호

이 규칙은 문자열 보간으로 바꿀 수 있는 concat 함수의 사용을 찾습니다.

## <a name="linter-rule-code"></a>Linter 규칙 코드

[Bicep 구성 파일에서](bicep-config-linter.md) 다음 값을 사용하여 규칙 설정을 사용자 지정합니다.

`prefer-interpolation`

## <a name="solution"></a>해결 방법

concat 함수 대신 문자열 보간을 사용합니다.

다음 예제에서는 concat 함수가 사용되므로 이 테스트에 실패합니다.

```bicep
param suffix string = '001'
var vnetName = concat('vnet-', suffix)
```

concat을 문자열 보간으로 대체하여 수정할 수 있습니다. 다음 예에서는 이 테스트를 통과합니다.

```bicep
param suffix string = '001'
var vnetName = 'vnet-${suffix}'
```

## <a name="next-steps"></a>다음 단계

linter에 대한 자세한 내용은 [Bicep linter 사용을 참조하세요.](./linter.md)
