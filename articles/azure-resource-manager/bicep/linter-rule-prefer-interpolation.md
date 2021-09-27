---
title: Lecrule-보간 선호
description: Lecrule-보간 선호
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 648b924b545e801f8a97ef5202f8022252ef94c4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634153"
---
# <a name="linter-rule---prefer-interpolation"></a>Lecrule-보간 선호

Linter를 사용하면 개발 중에 지침을 제공하여 코딩 표준을 보다 쉽게 적용할 수 있습니다. 현재 linter 규칙 집합은 [ttk 테스트 사례](../templates/template-test-cases.md)에서 가져옵니다.

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

자세한 내용은 [Bicep Linter 사용](./linter.md)을 참조 하세요.

## <a name="code"></a>코드

`prefer-interpolation`

## <a name="description"></a>Description

Concat 함수 대신 문자열 보간을 사용 해야 합니다.

## <a name="examples"></a>예제

다음 예는 concat 함수를 사용 하기 때문에이 테스트에 실패 합니다.

```bicep
param suffix string = '001'
var vnetName = concat('vnet-', suffix)

resource vnet 'Microsoft.Network/virtualNetworks@2018-10-01' = {
  name: vnetName
  ...
}
```

다음 예에서는 이 테스트를 통과합니다.

```bicep
param suffix string = '001'
var vnetName = 'vnet-${suffix}'

resource vnet 'Microsoft.Network/virtualNetworks@2018-10-01' = {
  name: vnetName
  ...
}
```

## <a name="next-steps"></a>다음 단계

Visual Studio Code 및 Bicep 확장 사용에 대한 자세한 내용은 [빠른 시작: Visual Studio Code를 사용하여 Bicep 파일 만들기](./quickstart-create-bicep-use-visual-studio-code.md)를 참조하세요.
