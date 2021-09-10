---
title: Bicep Linter 사용
description: Bicep Linter의 사용 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 07/01/2021
ms.openlocfilehash: 82ab1462ca55a4329370d185aed626ae631e2f2b
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113649443"
---
# <a name="use-bicep-linter"></a>Bicep Linter 사용

Bicep Linter를 사용하여 Bicep 파일을 분석할 수 있습니다. 구문 오류를 검사하고, Bicep 파일을 빌드하거나 배포하기 전에 사용자 지정 가능한 작성 모범 사례 집합을 찾습니다. Linter를 사용하면 개발 중에 지침을 제공하여 코딩 표준을 보다 쉽게 적용할 수 있습니다.

## <a name="install-linter"></a>Linter 설치

Linter는 Visual Studio 코드 및 Bicep CLI와 함께 사용할 수 있습니다. 다음 조건을 충족해야 합니다.

- Bicep Azure CLI 버전 0.4 이상
- Visual Studio Code 버전 0.4 이상에 대한 Bicep 확장입니다.

## <a name="customize-linter"></a>Linter 사용자 지정

bicepconfig.json을 통해 Linter를 사용하거나 사용하지 않도록 설정하고, 규칙 관련 값을 제공하며, 규칙의 수준도 설정할 수 있습니다. 다음은 기본 bicepconfig.json입니다.

```json
{
  "analyzers": {
    "core": {
      "verbose": false,
      "enabled": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning",
          "disallowedhosts": [
            "management.core.windows.net",
            "gallery.azure.com",
            "management.core.windows.net",
            "management.azure.com",
            "database.windows.net",
            "core.windows.net",
            "login.microsoftonline.com",
            "graph.windows.net",
            "trafficmanager.net",
            "vault.azure.net",
            "datalake.azure.net",
            "azuredatalakestore.net",
            "azuredatalakeanalytics.net",
            "vault.azure.net",
            "api.loganalytics.io",
            "api.loganalytics.iov1",
            "asazure.windows.net",
            "region.asazure.windows.net",
            "api.loganalytics.iov1",
            "api.loganalytics.io",
            "asazure.windows.net",
            "region.asazure.windows.net",
            "batch.core.windows.net"
          ],
          "excludedhosts": [
            "schema.management.azure.com"
          ]
        }
      }
    }
  }
}
```

사용자 지정된 bicepconfig.json을 템플릿과 함께 동일한 디렉터리에 배치할 수 있습니다. 폴더 트리에서 찾은 가장 가까운 구성 파일이 사용됩니다.

다음 json은 샘플 bicepconfig.json입니다.

```json
{
  "analyzers": {
    "core": {
      "enabled": true,
      "verbose": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning"
        },
        "no-unused-params": {
          "level": "error"
        },
        "no-unused-vars": {
          "level": "error"
        },
        "prefer-interpolation": {
          "level": "warning"
        },
        "secure-parameter-default": {
          "level": "error"
        },
        "simplify-interpolation": {
          "level": "warning"
        }
      }
    }
  }
}
```

- **사용**: Linter를 사용하도록 설정하려면 **true** 를 입력하고, Linter를 사용하지 않도록 설정하려면 **false** 를 입력합니다.
- **자세한 정보 표시**: Visual Studio Code에서 사용하는 bicepconfig.json 파일을 표시하려면 **true** 를 입력합니다.
- **규칙**: 규칙 관련 값을 입력합니다. 각 규칙에는 하나 이상의 속성과 수준이 있습니다. 이 속성은 Bicep 파일에서 찾을 수 있는 경우 Bicep의 동작을 지시합니다.

다음의 규칙 수준에 여러 값을 사용할 수 있습니다.

| **level**  | **빌드 시간 동작** | **편집기 동작** |
|--|--|--|
| `Error` | 위반은 명령줄 빌드 출력에 오류로 표시되며 빌드 실패를 유발합니다. | 잘못된 코드는 빨간색 물결선으로 밑줄이 그어져 문제 탭에 표시됩니다. |
| `Warning` | 위반은 명령줄 빌드 출력에 경고로 표시되지만, 빌드 실패를 유발하지는 않습니다. | 잘못된 코드는 노란색 물결선으로 밑줄이 그어져 문제 탭에 표시됩니다. |
| `Info` | 위반은 명령줄 빌드 출력에 표시되지 않습니다. | 잘못된 코드는 파란색 물결선으로 밑줄이 그어져 문제 탭에 표시됩니다. |
| `Off` | 전혀 표시되지 않습니다. | 전혀 표시되지 않습니다. |

현재 Linter 규칙 집합은 [arm-ttk 테스트 사례](../templates/template-test-cases.md)에서 가장 적게 사용됩니다. Visual Studio Code 확장 및 Bicep CLI는 모두 기본적으로 사용 가능한 모든 규칙을 검사하고 전체 규칙이 경고 수준으로 설정됩니다. 규칙 수준에 따라 편집기 내에서 오류나 경고 또는 정보 메시지가 표시됩니다.

- [no-hardcoded-env-urls](https://github.com/Azure/bicep/blob/main/docs/linter-rules/no-hardcoded-env-urls.md)
- [no-unused-params](https://github.com/Azure/bicep/blob/main/docs/linter-rules/no-unused-params.md)
- [no-unused-vars](https://github.com/Azure/bicep/blob/main/docs/linter-rules/no-unused-vars.md)
- [prefer-interpolation](https://github.com/Azure/bicep/blob/main/docs/linter-rules/prefer-interpolation.md)
- [secure-parameter-default](https://github.com/Azure/bicep/blob/main/docs/linter-rules/secure-parameter-default.md)
- [simplify-interpolation](https://github.com/Azure/bicep/blob/main/docs/linter-rules/simplify-interpolation.md)

Visual Studio Code의 Bicep 확장에서는 Bicep 구성 파일을 편집하기 위한 Intellisense를 제공합니다.

:::image type="content" source="./media/linter/bicep-linter-configure-intellisense.png" alt-text="bicepconfig.json 구성 시 Intellisense 지원.":::

## <a name="use-in-visual-studio-code"></a>Visual Studio Code 사용

Linter를 사용하려면 Bicep 확장 0.4 이상을 설치합니다.  다음 스크린샷에는 작동 중인 Linter가 나와 있습니다.

:::image type="content" source="./media/linter/bicep-linter-show-errors.png" alt-text="Visual Studio Code에서 Bicep Linter 사용.":::

**문제** 창에는 오류 네 개, 경고 한 개, 스크린샷에 표시된 정보 메시지 한 개가 있습니다.  정보 메시지에는 사용되는 Bicep 구성 파일이 표시됩니다. 구성 파일에서 **자세한 정보 표시** 를 **true** 로 설정한 경우에만 이 정보가 표시됩니다.

마우스 커서로 문제 영역 중 하나를 가리킵니다. Linter는 오류 또는 경고에 대한 세부 정보를 제공합니다. 영역을 클릭하면 파란색 전구도 표시됩니다.

:::image type="content" source="./media/linter/bicep-linter-show-quickfix.png" alt-text="Visual Studio Code에서 Bicep Linter 사용 - 빠른 수정 표시.":::

전구 또는 **빠른 수정** 링크를 선택하여 솔루션을 확인합니다.

:::image type="content" source="./media/linter/bicep-linter-show-quickfix-solution.png" alt-text="Visual Studio Code에서 Bicep Linter 사용 - 빠른 수정 솔루션 표시.":::

솔루션을 선택하여 문제를 자동으로 수정합니다.

## <a name="use-in-bicep-cli"></a>Bicep CLI에서 사용

Linter를 사용하려면 Bicep CLI 0.4 이상을 설치합니다.  다음 스크린샷에는 작동 중인 Linter가 나와 있습니다. Bicep 파일은 [Visual Studio 코드에서 사용](#use-in-visual-studio-code)하는 것과 동일합니다.

:::image type="content" source="./media/linter/bicep-linter-command-line.png" alt-text="명령줄에서 Bicep Linter 사용.":::

이러한 검사를 CI/CD 파이프라인의 일부로 통합할 수 있습니다. GitHub 작업을 사용하여 Bicep 빌드를 시도할 수 있습니다. 오류가 발생하면 파이프라인이 실패합니다.

## <a name="next-steps"></a>다음 단계

Visual Studio Code 및 Bicep 확장 사용에 대한 자세한 내용은 [빠른 시작: Visual Studio Code를 사용하여 Bicep 파일 만들기](./quickstart-create-bicep-use-visual-studio-code.md)를 참조하세요.
