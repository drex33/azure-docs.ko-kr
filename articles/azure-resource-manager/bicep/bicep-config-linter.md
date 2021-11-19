---
title: Bicep config에 대 한 lec설정
description: Bicep linter의 구성 값을 사용자 지정 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 11/18/2021
ms.openlocfilehash: 56647b34cfffd97a73e80160a1699790ddd6c1d7
ms.sourcegitcommit: 11ca7ba5a017429c22a6b0bc02acb70b83a2984a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132813549"
---
# <a name="add-linter-settings-in-the-bicep-config-file"></a>Bicep config 파일에서 l\settings 추가

**Bicepconfig** 파일에서 [Bicep linter](linter.md)에 대 한 유효성 검사 설정을 사용자 지정할 수 있습니다. Bicep 파일을 평가할 때 linter에서 이러한 설정을 사용 하 여 모범 사례를 확인 합니다.

이 문서에서는 Bicep linter 작업에 사용할 수 있는 설정을 설명 합니다.

## <a name="customize-linter"></a>Linter 사용자 지정

요소 아래에서 linter 설정을 사용할 수 있습니다 `analyzers` . Linter를 사용 하거나 사용 하지 않도록 설정 하 고, 규칙 관련 값을 제공 하 고, 규칙 수준을 설정할 수 있습니다.

다음 예에서는 구성에 사용할 수 있는 규칙을 보여 줍니다.

```json
{
  "analyzers": {
    "core": {
      "enabled": true,
      "verbose": true,
      "rules": {
        "adminusername-should-not-be-literal": {
          "level": "error"
        },
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

속성은 다음과 같습니다.

- **enabled**: linter를 사용 하지 않도록 설정 하려면 **true** 를 **지정 하 고** ,
- **verbose**: Visual Studio Code에서 사용 하는 bicepconfig 파일을 표시 하려면 **true** 를 지정 합니다.
- **규칙**: 규칙 관련 값을 지정 합니다. 각 규칙에는 위반이 발견 될 때 linter이 응답 하는 방법을 결정 하는 수준이 있습니다.

**수준** 에 사용할 수 있는 값은 다음과 같습니다.

| **level**  | **빌드 시간 동작** | **편집기 동작** |
|--|--|--|
| `Error` | 위반은 명령줄 빌드 출력에서 오류로 표시 되 고 빌드가 실패 합니다. | 잘못된 코드는 빨간색 물결선으로 밑줄이 그어져 문제 탭에 표시됩니다. |
| `Warning` | 위반은 명령줄 빌드 출력에서 경고로 표시 되지만 빌드에 실패 하는 경우는 발생 하지 않습니다. | 잘못된 코드는 노란색 물결선으로 밑줄이 그어져 문제 탭에 표시됩니다. |
| `Info` | 위반은 명령줄 빌드 출력에 표시 되지 않습니다. | 잘못된 코드는 파란색 물결선으로 밑줄이 그어져 문제 탭에 표시됩니다. |
| `Off` | 전혀 표시되지 않습니다. | 전혀 표시되지 않습니다. |

## <a name="environment-urls"></a>환경 Url

하드 코드 된 환경 Url에 대 한 규칙의 경우 검사 되는 Url을 사용자 지정할 수 있습니다. 기본적으로 다음 설정이 적용 됩니다.

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

## <a name="next-steps"></a>다음 단계

* [Bicep 환경 구성](bicep-config.md)
* [Bicep config에서 모듈 설정 추가](bicep-config-modules.md)
* [Bicep linter](linter.md) 에 대해 알아보기
