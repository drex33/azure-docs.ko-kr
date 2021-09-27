---
title: Linter 규칙 - 보안 매개 변수 기본값
description: Linter 규칙 - 보안 매개 변수 기본값
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 4fa82fbbe74d9bf51d1eb498341b999a89e12978
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634329"
---
# <a name="linter-rule---secure-parameter-default"></a>Linter 규칙 - 보안 매개 변수 기본값

Linter를 사용하면 개발 중에 지침을 제공하여 코딩 표준을 보다 쉽게 적용할 수 있습니다. 현재 linter 규칙 집합은 최소이며 [arm-ttk 테스트 사례에서 가져온 것입니다.](../templates/template-test-cases.md)

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

자세한 내용은 [Bicep linter 사용을 참조하세요.](./linter.md)

## <a name="code"></a>코드

`secure-parameter-default`

## <a name="description"></a>Description

비어 있거나 [newGuid()](./bicep-functions-string.md#newguid)호출이 포함된 식이 아닌 경우 템플릿의 [보안 매개 변수에](./parameters.md#secure-parameters) 하드 코드된 기본값을 제공하지 마세요.

@secure암호와 같은 중요한 값을 포함하는 매개 변수에 () 데코레이터를 사용합니다. 매개 변수가 보안 데코레이터를 사용하는 경우 매개 변수 값은 배포 기록에 기록되거나 저장되지 않습니다. 이 작업을 수행하면 악의적인 사용자가 중요한 값을 검색하지 못합니다.

그러나 보안 매개 변수에 대한 기본값을 제공하는 경우 템플릿 또는 배포 기록에 액세스할 수 있는 모든 사용자가 해당 값을 검색할 수 있습니다.

## <a name="examples"></a>예제

다음 예제에서는 이 테스트에 실패합니다.

```bicep
@secure()
param adminPassword string = 'HardcodedPassword'
```

다음 예제에서는 이 테스트를 통과합니다.

```bicep
@secure()
param adminPassword string
```

```bicep
@secure()
param adminPassword string = ''
```

```bicep
@secure()
param adminPassword string = newGuid()
```

## <a name="next-steps"></a>다음 단계

Visual Studio Code 및 Bicep 확장 사용에 대한 자세한 내용은 [빠른 시작: Visual Studio Code를 사용하여 Bicep 파일 만들기](./quickstart-create-bicep-use-visual-studio-code.md)를 참조하세요.
