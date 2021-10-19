---
title: Lecrule-보간을 단순화 합니다.
description: Lecrule-보간을 단순화 합니다.
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: e2e0f9eca607ececad22c025e89b7c976cd0d05a
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165352"
---
# <a name="linter-rule---simplify-interpolation"></a>Lecrule-보간을 단순화 합니다.

이 규칙은 필요 하지 않은 경우 문자열 보간을 사용 하는 구문을 찾습니다.

## <a name="returned-code"></a>반환 된 코드

`simplify-interpolation`

## <a name="solution"></a>해결 방법

식의 일부가 아닌 문자열 보간을 사용 하 여 값을 결합 하는 것을 제거 합니다.

다음 예제에서는 매개 변수를 참조 하기 때문에이 테스트에 실패 합니다.

```bicep
param AutomationAccountName string

resource AutomationAccount 'Microsoft.Automation/automationAccounts@2020-01-13-preview' = {
  name: '${AutomationAccountName}'
  ...
}
```

문자열 보간 구문을 제거 하 여 문제를 해결할 수 있습니다.

```bicep
param AutomationAccountName string

resource AutomationAccount 'Microsoft.Automation/automationAccounts@2020-01-13-preview' = {
  name: AutomationAccountName
  ...
}
```

## <a name="next-steps"></a>다음 단계

Linter에 대 한 자세한 내용은 [Bicep Linter 사용](./linter.md)을 참조 하세요.
