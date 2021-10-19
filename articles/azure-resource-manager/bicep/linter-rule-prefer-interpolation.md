---
title: Lecrule-보간 선호
description: Lecrule-보간 선호
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 952359c7c258c084e19d6faf152536a3e50212b2
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130164475"
---
# <a name="linter-rule---prefer-interpolation"></a>Lecrule-보간 선호

이 규칙은 문자열 보간을 사용 하 여 바꿀 수 있는 concat 함수의 사용을 찾습니다.

## <a name="returned-code"></a>반환 된 코드

`prefer-interpolation`

## <a name="solution"></a>해결 방법

Concat 함수 대신 문자열 보간을 사용 합니다.

다음 예는 concat 함수를 사용 하기 때문에이 테스트에 실패 합니다.

```bicep
param suffix string = '001'
var vnetName = concat('vnet-', suffix)
```

Concat를 문자열 보간으로 바꿔 수정할 수 있습니다. 다음 예에서는 이 테스트를 통과합니다.

```bicep
param suffix string = '001'
var vnetName = 'vnet-${suffix}'
```

## <a name="next-steps"></a>다음 단계

Linter에 대 한 자세한 내용은 [Bicep Linter 사용](./linter.md)을 참조 하세요.
