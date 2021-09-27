---
title: Lecrule-사용 되지 않는 변수 없음
description: Lecrule-사용 되지 않는 변수 없음
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 80ace33219024f19792ee909c092e498b645665e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634132"
---
# <a name="linter-rule---no-unused-variables"></a>Lecrule-사용 되지 않는 변수 없음

Linter를 사용하면 개발 중에 지침을 제공하여 코딩 표준을 보다 쉽게 적용할 수 있습니다. 현재 linter 규칙 집합은 [ttk 테스트 사례](../templates/template-test-cases.md)에서 가져옵니다.

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

자세한 내용은 [Bicep Linter 사용](./linter.md)을 참조 하세요.

## <a name="code"></a>코드

`no-unused-vars`

## <a name="description"></a>Description

템플릿에서 혼동을 줄이려면 정의되었지만 사용되지 않는 모든 변수를 삭제합니다. 이 테스트는 템플릿의 모든 위치에서 사용되지 않는 변수를 찾습니다.

## <a name="next-steps"></a>다음 단계

Visual Studio Code 및 Bicep 확장 사용에 대한 자세한 내용은 [빠른 시작: Visual Studio Code를 사용하여 Bicep 파일 만들기](./quickstart-create-bicep-use-visual-studio-code.md)를 참조하세요.
