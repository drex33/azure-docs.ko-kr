---
title: Bicep Linter 사용
description: Bicep Linter의 사용 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 10/12/2021
ms.openlocfilehash: ec8088dd2caa3514ec87e421928ef0f8c93852b5
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130063339"
---
# <a name="use-bicep-linter"></a>Bicep Linter 사용

Bicep linter는 Bicep 파일에서 구문 오류 및 모범 사례 위반을 확인합니다. Linter는 개발 중에 지침을 제공하여 코딩 표준을 적용하는 데 도움이 됩니다. 파일 검사에 사용할 모범 사례를 사용자 지정할 수 있습니다.

## <a name="linter-requirements"></a>Linter 요구 사항

linter는 Visual Studio Code Bicep CLI 및 Bicep 확장에 통합됩니다. 이 버전을 사용하려면 버전 **0.4 이상이어야** 합니다.

## <a name="default-rules"></a>기본 규칙

linter 규칙의 기본 집합은 최소이며 [arm-ttk 테스트 사례에서 사용됩니다.](../templates/template-test-cases.md) 확장 및 Bicep CLI는 경고 수준으로 설정된 다음 규칙을 확인합니다.

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Linter 규칙이 적용되는 방법을 사용자 지정할 수 있습니다. 기본 설정을 덮어쓰려면 **bicepconfig.json** 파일을 추가하고 사용자 지정 설정을 적용합니다. 이러한 설정을 적용하는 자세한 내용은 [Bicep 구성 파일에서 사용자 지정 설정 추가를](bicep-config.md)참조하세요.

## <a name="use-in-visual-studio-code"></a>Visual Studio Code 사용

다음 스크린샷은 Visual Studio Code linter를 보여줍니다.

:::image type="content" source="./media/linter/bicep-linter-show-errors.png" alt-text="Visual Studio Code에서 Bicep Linter 사용.":::

**문제** 창에는 오류 네 개, 경고 한 개, 스크린샷에 표시된 정보 메시지 한 개가 있습니다.  정보 메시지는 사용되는 Bicep 구성 파일을 보여 줍니다. 구성 파일에서 **자세한 정보 표시** 를 **true** 로 설정한 경우에만 이 정보가 표시됩니다.

마우스 커서로 문제 영역 중 하나를 가리킵니다. Linter는 오류 또는 경고에 대한 세부 정보를 제공합니다. 영역을 선택하면 파란색 전구도 표시됩니다.

:::image type="content" source="./media/linter/bicep-linter-show-quickfix.png" alt-text="Visual Studio Code에서 Bicep Linter 사용 - 빠른 수정 표시.":::

전구 또는 **빠른 수정** 링크를 선택하여 솔루션을 확인합니다.

:::image type="content" source="./media/linter/bicep-linter-show-quickfix-solution.png" alt-text="Visual Studio Code에서 Bicep Linter 사용 - 빠른 수정 솔루션 표시.":::

솔루션을 선택하여 문제를 자동으로 수정합니다.

## <a name="use-in-bicep-cli"></a>Bicep CLI에서 사용

다음 스크린샷은 명령줄의 linter를 보여줍니다. 빌드 명령의 출력에는 규칙 위반이 표시됩니다.

:::image type="content" source="./media/linter/bicep-linter-command-line.png" alt-text="명령줄에서 Bicep Linter 사용.":::

이러한 검사를 CI/CD 파이프라인의 일부로 통합할 수 있습니다. GitHub 작업을 사용하여 Bicep 빌드를 시도할 수 있습니다. 오류가 발생하면 파이프라인이 실패합니다.

## <a name="next-steps"></a>다음 단계

* Linter 규칙을 사용자 지정하는 자세한 내용은 [Bicep 구성 파일에서 사용자 지정 설정 추가를](bicep-config.md)참조하세요.
* Visual Studio Code 및 Bicep 확장 사용에 대한 자세한 내용은 [빠른 시작: Visual Studio Code를 사용하여 Bicep 파일 만들기](./quickstart-create-bicep-use-visual-studio-code.md)를 참조하세요.
