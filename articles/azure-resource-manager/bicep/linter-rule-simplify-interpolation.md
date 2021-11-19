---
title: Linter 규칙 - 보간 간소화
description: Linter 규칙 - 보간 간소화
ms.topic: conceptual
ms.date: 11/18/2021
ms.openlocfilehash: 5a5d94f403b686e4a3f4b3d85941f13cfc00042d
ms.sourcegitcommit: 11ca7ba5a017429c22a6b0bc02acb70b83a2984a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132813569"
---
# <a name="linter-rule---simplify-interpolation"></a>Linter 규칙 - 보간 간소화

이 규칙은 필요하지 않은 경우 문자열 보간을 사용하는 구문을 찾습니다.

## <a name="linter-rule-code"></a>Linter 규칙 코드

[Bicep 구성 파일에서](bicep-config-linter.md) 다음 값을 사용하여 규칙 설정을 사용자 지정합니다.

`simplify-interpolation`

## <a name="solution"></a>해결 방법

값을 결합하는 식의 일부가 아닌 문자열 보간 사용을 제거합니다.

다음 예제에서는 매개 변수만 참조하므로 이 테스트에 실패합니다.

```bicep
param AutomationAccountName string

resource AutomationAccount 'Microsoft.Automation/automationAccounts@2020-01-13-preview' = {
  name: '${AutomationAccountName}'
  ...
}
```

문자열 보간 구문을 제거하여 수정할 수 있습니다.

```bicep
param AutomationAccountName string

resource AutomationAccount 'Microsoft.Automation/automationAccounts@2020-01-13-preview' = {
  name: AutomationAccountName
  ...
}
```

## <a name="next-steps"></a>다음 단계

linter에 대한 자세한 내용은 [Bicep linter 사용을 참조하세요.](./linter.md)
