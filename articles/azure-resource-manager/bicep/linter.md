---
title: Bicep Linter 사용
description: Bicep Linter의 사용 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 10/12/2021
ms.openlocfilehash: 5edf5fbb9553233455e7b24dd482a18d3c7e1c42
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548105"
---
# <a name="use-bicep-linter"></a>Bicep Linter 사용

Bicep linter는 구문 오류 및 모범 사례 위반에 대 한 Bicep 파일을 검사 합니다. Linter는 개발 중에 지침을 제공 하 여 코딩 표준을 적용 하는 데 도움이 됩니다. 파일을 확인 하는 데 사용할 모범 사례를 사용자 지정할 수 있습니다.

## <a name="linter-requirements"></a>Linter 요구 사항

Linter은 Bicep CLI 및 Visual Studio Code에 대 한 Bicep 확장에 통합 되어 있습니다. 이 버전을 사용 하려면 버전 **0.4 이상이** 있어야 합니다.

## <a name="default-rules"></a>기본 규칙

Linter 규칙의 기본 집합은 최소 이며 [arm-ttk 테스트 사례](../templates/template-test-cases.md)에서 가져옵니다. 확장 및 Bicep CLI는 경고 수준으로 설정 된 다음 규칙을 검사 합니다.

- [adminusername-리터럴이 아니어야 함](./linter-rule-admin-username-should-not-be-literal.md)
- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Linter 규칙이 적용 되는 방식을 사용자 지정할 수 있습니다. 기본 설정을 덮어쓰려면 **bicepconfig** 파일을 추가 하 고 사용자 지정 설정을 적용 합니다. 이러한 설정을 적용 하는 방법에 대 한 자세한 내용은 [Bicep 구성 파일에서 사용자 지정 설정 추가](bicep-config-linter.md)를 참조 하세요.

## <a name="use-in-visual-studio-code"></a>Visual Studio Code 사용

다음 스크린샷은 Visual Studio Code의 linter을 보여 줍니다.

:::image type="content" source="./media/linter/bicep-linter-show-errors.png" alt-text="Visual Studio Code에서 Bicep Linter 사용.":::

**문제** 창에는 오류 네 개, 경고 한 개, 스크린샷에 표시된 정보 메시지 한 개가 있습니다.  정보 메시지에는 사용 되는 Bicep 구성 파일이 표시 됩니다. 구성 파일에서 **자세한 정보 표시** 를 **true** 로 설정한 경우에만 이 정보가 표시됩니다.

마우스 커서로 문제 영역 중 하나를 가리킵니다. Linter는 오류 또는 경고에 대한 세부 정보를 제공합니다. 영역을 선택 하면 파란색 전구도 표시 됩니다.

:::image type="content" source="./media/linter/bicep-linter-show-quickfix.png" alt-text="Visual Studio Code에서 Bicep Linter 사용 - 빠른 수정 표시.":::

전구 또는 **빠른 수정** 링크를 선택하여 솔루션을 확인합니다.

:::image type="content" source="./media/linter/bicep-linter-show-quickfix-solution.png" alt-text="Visual Studio Code에서 Bicep Linter 사용 - 빠른 수정 솔루션 표시.":::

솔루션을 선택하여 문제를 자동으로 수정합니다.

## <a name="use-in-bicep-cli"></a>Bicep CLI에서 사용

다음 스크린 샷에서는 명령줄의 linter을 보여 줍니다. 빌드 명령의 출력은 모든 규칙 위반을 보여 줍니다.

:::image type="content" source="./media/linter/bicep-linter-command-line.png" alt-text="명령줄에서 Bicep Linter 사용.":::

이러한 검사를 CI/CD 파이프라인의 일부로 통합할 수 있습니다. GitHub 작업을 사용하여 Bicep 빌드를 시도할 수 있습니다. 오류가 발생하면 파이프라인이 실패합니다.

## <a name="next-steps"></a>다음 단계

* Linter 규칙을 사용자 지정 하는 방법에 대 한 자세한 내용은 [Bicep 구성 파일에서 사용자 지정 설정 추가](bicep-config-linter.md)를 참조 하세요.
* Visual Studio Code 및 Bicep 확장 사용에 대한 자세한 내용은 [빠른 시작: Visual Studio Code를 사용하여 Bicep 파일 만들기](./quickstart-create-bicep-use-visual-studio-code.md)를 참조하세요.
