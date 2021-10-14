---
title: Lecrule-사용 되지 않는 변수 없음
description: Lecrule-사용 되지 않는 변수 없음
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 64ad8d96bd1b42b5d6c12fc58cb0c99cbd008467
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129984141"
---
# <a name="linter-rule---no-unused-variables"></a>Lecrule-사용 되지 않는 변수 없음

이 규칙은 Bicep 파일의 어디에서 나 참조 되지 않는 변수를 찾습니다.

## <a name="returned-code"></a>반환 된 코드

`no-unused-vars`

## <a name="solution"></a>해결 방법

템플릿에서 혼동을 줄이려면 정의되었지만 사용되지 않는 모든 변수를 삭제합니다. 이 테스트는 템플릿의 모든 위치에서 사용되지 않는 변수를 찾습니다.

## <a name="next-steps"></a>다음 단계

* Linter에 대 한 자세한 내용은 [Bicep Linter 사용](./linter.md)을 참조 하세요.
* 현재 lecrules는 다음과 같습니다.

  * [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
  * [no-unused-params](./linter-rule-no-unused-parameters.md)
  * [no-unused-vars](./linter-rule-no-unused-variables.md)
  * [prefer-interpolation](./linter-rule-prefer-interpolation.md)
  * [secure-parameter-default](./linter-rule-secure-parameter-default.md)
  * [simplify-interpolation](./linter-rule-simplify-interpolation.md)
