---
title: Linter 규칙 - 사용되지 않는 매개 변수 없음
description: Linter 규칙 - 사용되지 않는 매개 변수 없음
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 25b4d5f65a6ed139f5aa9f5bde1c47365d337ac2
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993077"
---
# <a name="linter-rule---no-unused-parameters"></a>Linter 규칙 - 사용되지 않는 매개 변수 없음

이 규칙은 Bicep 파일에서 참조되지 않는 매개 변수를 찾습니다.

## <a name="returned-code"></a>반환된 코드

`no-unused-params`

## <a name="solution"></a>해결 방법

템플릿의 혼동을 줄이려면 정의되었지만 사용되지 않는 매개 변수를 모두 삭제합니다. 이 테스트는 템플릿의 모든 위치에서 사용되지 않는 매개 변수를 찾습니다. 사용하지 않는 매개 변수를 제거하면 불필요한 값을 제공하지 않아도 되므로 템플릿을 더 쉽게 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* linter에 대한 자세한 내용은 [Bicep linter 사용을 참조하세요.](./linter.md)
* 현재 linter 규칙은 다음과 같습니다.

  * [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
  * [no-unused-params](./linter-rule-no-unused-parameters.md)
  * [no-unused-vars](./linter-rule-no-unused-variables.md)
  * [prefer-interpolation](./linter-rule-prefer-interpolation.md)
  * [secure-parameter-default](./linter-rule-secure-parameter-default.md)
  * [simplify-interpolation](./linter-rule-simplify-interpolation.md)
