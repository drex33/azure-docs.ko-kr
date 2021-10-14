---
title: Lecrule-보간 선호
description: Lecrule-보간 선호
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 91bcde23a727a0cf916837b427717d9e5916151b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005637"
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

* Linter에 대 한 자세한 내용은 [Bicep Linter 사용](./linter.md)을 참조 하세요.
* 현재 lecrules는 다음과 같습니다.

  * [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
  * [no-unused-params](./linter-rule-no-unused-parameters.md)
  * [no-unused-vars](./linter-rule-no-unused-variables.md)
  * [prefer-interpolation](./linter-rule-prefer-interpolation.md)
  * [secure-parameter-default](./linter-rule-secure-parameter-default.md)
  * [simplify-interpolation](./linter-rule-simplify-interpolation.md)
