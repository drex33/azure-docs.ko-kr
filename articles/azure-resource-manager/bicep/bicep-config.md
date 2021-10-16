---
title: Bicep 구성 파일
description: Bicep 배포에 대한 구성 값을 사용자 지정하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 7a057d353fd5b25ae122e7856f1ccb560d7fce56
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130071837"
---
# <a name="add-custom-settings-in-the-bicep-config-file"></a>Bicep 구성 파일에 사용자 지정 설정 추가

Bicep 배포에 대한 구성 값을 사용자 지정하려면 Bicep 파일을 저장하는 디렉터리에 **bicepconfig.json이라는** 파일을 추가합니다. 이 파일 내에서 사용할 구성 값을 지정합니다.

여러 bicepconfig.json 파일을 추가할 수 있습니다. 디렉터리 계층에서 가장 가까운 구성 파일이 사용됩니다.

이 문서에서는 구성 파일에서 사용할 수 있는 속성에 대해 설명합니다. 그러나 Visual Studio Code 위해 Bicep 확장에서 제공하는 intellisense를 통해 이러한 속성을 검색할 수도 있습니다.

:::image type="content" source="./media/bicep-config/bicep-linter-configure-intellisense.png" alt-text="bicepconfig.json 구성 시 Intellisense 지원.":::

## <a name="aliases-for-modules"></a>모듈의 별칭

모듈에 연결하기 위한 경로를 간소화하기 위해 구성 파일에 별칭을 만들 수 있습니다. 별칭은 모듈 레지스트리 또는 템플릿 사양이 포함된 리소스 그룹을 참조할 수 있습니다. 구성 파일에는 에 대한 속성이 `moduleAliases` 있습니다. Bicep 레지스트리에 대한 별칭을 만들려면 `br` 속성 아래에 속성을 추가합니다. `moduleAliases` 템플릿 사양에 대 한 별칭을 추가 하려면 사용 된 `ts` 속성입니다.

```json
{
  "moduleAliases": {
    "br": {
      <add-registry-aliases>
    },
    "ts": {
      <add-template-specs-aliases>
    }
  }
}
```

속성 내에서 `br` 필요한 만큼 별칭을 추가합니다. 각 별칭에 대해 이름 및 다음 속성을 지정합니다.

- **레지스트리(필수):** 레지스트리 로그인 서버 이름
- **modulePath(선택** 사항): 모듈이 저장되는 레지스트리 리포지토리

속성 내에서 `ts` 필요한 만큼 별칭을 추가합니다. 각 별칭에 대해 이름 및 다음 속성을 지정합니다.

- **subscription(필수):** 템플릿 사양을 호스트하는 구독 ID
- **resourceGroup(필수):** 템플릿 사양을 포함하는 리소스 그룹의 이름

다음 예제에서는 모듈 레지스트리에 대한 두 개의 별칭을 정의하는 구성 파일과 템플릿 사양이 포함된 리소스 그룹에 대한 별칭 하나를 보여 주는 구성 파일을 보여줍니다.

```json
{
  "moduleAliases": {
    "br": {
      "ContosoRegistry": {
        "registry": "contosoregistry.azurecr.io"
      },
      "CoreModules": {
        "registry": "contosoregistry.azurecr.io",
        "modulePath": "bicep/modules/core"
      }
    },
    "ts": {
      "CoreSpecs": {
        "subscription": "00000000-0000-0000-0000-000000000000",
        "resourceGroup": "CoreSpecsRG"
      }
    }
  }
}
```

모듈 참조에서 별칭을 사용하는 경우 다음 형식을 사용해야 합니다.

```bicep
br/<alias>:<file>:<tag>
ts/<alias>:<file>:<tag>
```

파일 자체가 아니라 모듈이 포함된 폴더 또는 리소스 그룹에 별칭을 정의합니다. 파일 이름은 모듈에 대한 참조에 포함되어야 합니다.

**별칭이 없으면** 전체 경로가 있는 레지스트리의 모듈에 연결됩니다.

```bicep
module stgModule 'br:contosoregistry.azurecr.io/bicep/modules/core/storage:v1' = {
```

**별칭을 사용하면** 레지스트리의 별칭을 사용하여 링크를 간소화할 수 있습니다.

```bicep
module stgModule 'br/ContosoRegistry:bicep/modules/core/storage:v1' = {
```

또는 레지스트리 및 모듈 경로를 지정하는 별칭을 사용하여 링크를 간소화할 수 있습니다.

```bicep
module stgModule  'br/CoreModules:storage:v1' = {
```

템플릿 사양의 경우 다음을 사용합니다.

```bicep
module stgModule  'ts/CoreSpecs:storage:v1' = {
```

## <a name="credentials-for-restoring-modules"></a>모듈 복원을 위한 자격 증명

외부 모듈을 로컬 캐시로 [복원하려면](bicep-cli.md#restore) 계정에 레지스트리에 액세스할 수 있는 올바른 권한이 있어야 합니다. 레지스트리에 인증하기 위한 자격 증명 우선 순위를 구성할 수 있습니다. 기본적으로 Bicep은 Azure CLI 또는 Azure PowerShell 인증된 사용자의 자격 증명을 사용합니다. 자격 증명 우선 순위를 사용자 지정하려면 `cloud` 구성 파일에 및 요소를 추가합니다. `credentialPrecedence`

```json
{
    "cloud": {
      "credentialPrecedence": [
        "AzureCLI",
        "AzurePowerShell"
      ]
    }
}
```

사용 가능한 자격 증명은 다음과 같습니다.

* AzureCLI
* AzurePowerShell
* 환경
* ManagedIdentity
* VisualStudio
* VisualStudioCode

## <a name="customize-linter"></a>Linter 사용자 지정

구성 파일에서 [Bicep linter](linter.md)에 대한 설정을 사용자 지정합니다. linter를 사용하거나 사용하지 않도록 설정하고, 규칙별 값을 제공하고, 규칙 수준을 설정할 수 있습니다.

다음 예제에서는 구성에 사용할 수 있는 규칙을 보여 줍니다.

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

속성은 다음과 같습니다.

- **enabled:** linter를 사용하도록 설정하려면 **true를** 지정하고 linter를 사용하지 않도록 설정하려면 **false를** 지정합니다.
- **verbose**: Visual Studio Code 사용되는 bicepconfig.json 파일을 표시하려면 **true를** 지정합니다.
- **rules**: 규칙별 값을 지정합니다. 각 규칙에는 위반이 발견되면 Linter가 응답하는 방법을 결정하는 수준이 있습니다.

**수준에** 사용할 수 있는 값은 다음과 같습니다.

| **level**  | **빌드 시간 동작** | **편집기 동작** |
|--|--|--|
| `Error` | 위반은 명령줄 빌드 출력에 오류로 표시되고 빌드가 실패합니다. | 잘못된 코드는 빨간색 물결선으로 밑줄이 그어져 문제 탭에 표시됩니다. |
| `Warning` | 위반은 명령줄 빌드 출력에 경고로 표시되지만 빌드가 실패하지는 않습니다. | 잘못된 코드는 노란색 물결선으로 밑줄이 그어져 문제 탭에 표시됩니다. |
| `Info` | 위반은 명령줄 빌드 출력에 표시되지 않습니다. | 잘못된 코드는 파란색 물결선으로 밑줄이 그어져 문제 탭에 표시됩니다. |
| `Off` | 전혀 표시되지 않습니다. | 전혀 표시되지 않습니다. |

하드 코딩된 환경 URL에 대한 규칙의 경우 어떤 URL이 선택되어 있는지 사용자 지정할 수 있습니다. 기본적으로 다음 설정이 적용됩니다.

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


