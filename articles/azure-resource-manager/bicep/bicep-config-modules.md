---
title: Bicep config에 대 한 모듈 설정
description: Bicep 배포의 모듈에 대 한 구성 값을 사용자 지정 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 11/16/2021
ms.openlocfilehash: e130695532d470a154acc0b35bca864fcdbcba9d
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556923"
---
# <a name="add-module-settings-in-the-bicep-config-file"></a>Bicep 구성 파일에 모듈 설정 추가

**Bicepconfig** 파일에서 모듈 경로에 대 한 별칭을 만들고 모듈 복원에 대 한 자격 증명 우선 순위를 구성할 수 있습니다.

이 문서에서는 [모듈](modules.md)작업에 사용할 수 있는 설정을 설명 합니다.

## <a name="aliases-for-modules"></a>모듈에 대 한 별칭

모듈에 연결 하기 위한 경로를 간소화 하려면 구성 파일에 별칭을 만듭니다. 별칭은 모듈 레지스트리 또는 템플릿 사양을 포함 하는 리소스 그룹을 참조 합니다.

구성 파일에는에 대 한 속성이 있습니다 `moduleAliases` . 이 속성은 사용자가 정의 하는 모든 별칭을 포함 합니다. 이 속성에서 별칭은 레지스트리 또는 템플릿 사양을 참조 하는지 여부에 따라 구분 됩니다.

**Bicep 레지스트리에** 대 한 별칭을 만들려면 속성을 추가 `br` 합니다. **템플릿 사양의** 별칭을 추가 하려면 속성을 사용 `ts` 합니다.

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

속성 내에서 `br` 필요한 만큼 별칭을 추가 합니다. 각 별칭에 대해 이름 및 다음 속성을 지정 합니다.

- **레지스트리** (필수): 레지스트리 로그인 서버 이름
- **Modulepath** (선택 사항): 모듈이 저장 되는 레지스트리 리포지토리

속성 내에서 `ts` 필요한 만큼 별칭을 추가 합니다. 각 별칭에 대해 이름 및 다음 속성을 지정 합니다.

- **구독** (필수): 템플릿 사양을 호스트 하는 구독 ID
- **resourceGroup** (필수): 템플릿 사양을 포함 하는 리소스 그룹의 이름

다음 예제에서는 모듈 레지스트리에 대해 두 개의 별칭을 정의 하는 구성 파일 및 템플릿 사양을 포함 하는 리소스 그룹에 대 한 별칭을 보여 줍니다.

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

모듈 참조에서 별칭을 사용 하는 경우 다음 형식을 사용 해야 합니다.

```bicep
br/<alias>:<file>:<tag>
ts/<alias>:<file>:<tag>
```

파일이 아닌 모듈을 포함 하는 폴더 또는 리소스 그룹에 대 한 별칭을 정의 합니다. 모듈에 대 한 참조에 파일 이름을 포함 해야 합니다.

**별칭을 사용 하지 않으면** 전체 경로를 사용 하 여 레지스트리의 모듈에 연결할 수 있습니다.

```bicep
module stgModule 'br:contosoregistry.azurecr.io/bicep/modules/core/storage:v1' = {
```

**별칭** 을 사용 하면 레지스트리에 대 한 별칭을 사용 하 여 링크를 단순화할 수 있습니다.

```bicep
module stgModule 'br/ContosoRegistry:bicep/modules/core/storage:v1' = {
```

또는 레지스트리 및 모듈 경로를 지정 하는 별칭을 사용 하 여 링크를 단순화할 수 있습니다.

```bicep
module stgModule  'br/CoreModules:storage:v1' = {
```

템플릿 사양의 경우 다음을 사용 합니다.

```bicep
module stgModule  'ts/CoreSpecs:storage:v1' = {
```

## <a name="credentials-for-restoring-modules"></a>모듈 복원에 대 한 자격 증명

외부 모듈을 로컬 캐시로 [복원](bicep-cli.md#restore) 하려면 계정에 레지스트리에 액세스할 수 있는 올바른 권한이 있어야 합니다. 레지스트리에 대 한 인증을 위해 자격 증명 우선 순위를 구성할 수 있습니다. 기본적으로 Bicep는 Azure CLI 또는 Azure PowerShell에서 인증 된 사용자의 자격 증명을 사용 합니다. 자격 증명 우선 순위를 사용자 지정 하려면 `cloud` 및 `credentialPrecedence` 요소를 구성 파일에 추가 합니다.

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

## <a name="next-steps"></a>다음 단계

* [Bicep 환경 구성](bicep-config.md)
* [Bicep config에 lecsettings 추가](bicep-config-linter.md)
* [모듈](modules.md) 에 대 한 자세한 정보
