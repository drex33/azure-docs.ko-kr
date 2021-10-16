---
title: Bicep 모듈
description: Bicep 파일에서 모듈을 정의 하는 방법 및 모듈 범위를 사용 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 21dc273e506f0c0f148e8a220ca4ea160c7423a8
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074497"
---
# <a name="bicep-modules"></a>Bicep 모듈

Bicep를 사용 하면 배포를 모듈로 구성할 수 있습니다. 모듈은 다른 Bicep 파일에서 배포 되는 Bicep 파일입니다. 모듈을 사용 하면 배포에 대 한 복잡 한 세부 정보를 캡슐화 하 여 Bicep 파일의 가독성을 향상 시킬 수 있습니다. 또한 다양 한 배포에 대 한 모듈을 쉽게 재사용할 수 있습니다.

조직의 다른 사용자와 모듈을 공유 하려면 [개인 레지스트리를 만듭니다](private-module-registry.md). 레지스트리의 모듈은 올바른 권한이 있는 사용자만 사용할 수 있습니다.

Bicep 모듈은 [중첩 된 템플릿을](../templates/linked-templates.md#nested-template)사용 하 여 단일 Azure Resource Manager 템플릿으로 변환 됩니다.

## <a name="definition-syntax"></a>정의 구문

모듈을 정의 하는 기본 구문은 다음과 같습니다.

```bicep
module <symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}
```

따라서 간단한 실제 예는 다음과 같습니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/local-file-definition.bicep" :::

Bicep 파일의 다른 부분에서 모듈을 참조 하려면 기호화 된 이름을 사용 합니다. 예를 들어 심볼 이름을 사용 하 여 모듈에서 출력을 가져올 수 있습니다. 기호화 된 이름에는 a-z, a-z, 0-9 및 ' _ '가 포함 될 수 있습니다. 이름은 숫자로 시작할 수 없습니다. 모듈은 매개 변수, 변수 또는 리소스와 동일한 이름을 가질 수 없습니다.

경로는 로컬 파일 이거나 레지스트리의 파일 일 수 있습니다. 자세한 내용은 [모듈 경로를](#path-to-module)참조 하세요.

**Name** 속성이 필요 합니다. 생성 된 템플릿의 중첩 된 배포 리소스 이름이 됩니다.

주 파일의 범위와 다른 **범위를 지정** 해야 하는 경우 범위 속성을 추가 합니다. 자세한 내용은 [모듈 범위 설정](#set-module-scope)을 참조 하세요.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/scope-definition.bicep" highlight="4" :::

조건에 따라 **모듈을 배포** 하려면 `if` 식을 추가 합니다. 이 사용은 [리소스를 조건부로 배포](conditional-resource-deployment.md)하는 것과 유사 합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/conditional-definition.bicep" highlight="2" :::

모듈의 **인스턴스** 를 둘 이상 배포 하려면 `for` 식을 추가 합니다. 자세한 내용은 [Bicep의 모듈 반복](loop-modules.md)을 참조 하세요.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/iterative-definition.bicep" highlight="3" :::

리소스와 마찬가지로 모듈은 다른 모듈 또는 리소스에 종속 되지 않는 한 병렬로 배포 됩니다. 일반적으로 종속성은 암시적으로 결정 되므로 설정할 필요가 없습니다. 명시적 종속성을 설정 해야 하는 경우 모듈 정의에를 추가할 수 있습니다 `dependsOn` . 종속성에 대한 자세한 내용은 [리소스 종속성 설정](resource-declaration.md#set-resource-dependencies)을 참조하세요.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/dependsOn-definition.bicep" highlight="6-8" :::

## <a name="path-to-module"></a>모듈 경로

모듈의 파일은 Bicep 모듈 레지스트리의 로컬 파일 또는 외부 파일 일 수 있습니다. 두 옵션에 대 한 구문은 다음과 같습니다.

### <a name="local-file"></a>로컬 파일

모듈이 **로컬 파일이** 면 해당 파일에 대 한 상대 경로를 제공 합니다. Bicep의 모든 경로는 슬래시 (/) 디렉터리 구분 기호를 사용 하 여 지정 해야 플랫폼 간에 일관 되 게 컴파일할 수 있습니다. Windows 백슬래시(\\) 문자는 지원되지 않습니다. 경로에는 공백을 포함할 수 있습니다.

예를 들어 기본 파일에서 디렉터리에 한 수준 위의 파일을 배포 하려면 다음을 사용 합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/local-file-definition.bicep" highlight="1" :::

### <a name="file-in-registry"></a>레지스트리의 파일

[레지스트리에 모듈을 게시](bicep-cli.md#publish)한 경우 해당 모듈에 연결할 수 있습니다. Azure container registry의 이름과 모듈 경로를 제공 합니다. 다음 구문을 사용 하 여 모듈 경로를 지정 합니다.

```bicep
module <symbolic-name> 'br:<registry-name>.azurecr.io/<file-path>:<tag>' = {
```

- **br** 은 Bicep 레지스트리의 스키마 이름입니다.
- **파일 경로** 는 `repository` Azure Container Registry에서 호출 됩니다. **파일 경로** 에는 문자로 구분 된 세그먼트가 포함 될 수 있습니다 `/` .
- **태그** 는 모듈의 버전을 지정 하는 데 사용 됩니다.

예를 들면 다음과 같습니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/registry-definition.bicep" highlight="1" :::

레지스트리에서 모듈을 참조 하는 경우 Visual Studio Code의 Bicep 확장에서 자동으로 [Bicep restore](bicep-cli.md#restore) 를 호출 하 여 외부 모듈을 로컬 캐시로 복사 합니다. 외부 모듈을 복원 하는 데 몇 분 정도 걸립니다. 모듈에 대 한 intellisense가 즉시 작동 하지 않는 경우 복원이 완료 될 때까지 기다립니다.

레지스트리의 모듈에 대 한 전체 경로는 길어질 수 있습니다. 모듈을 사용 하려는 때마다 전체 경로를 제공 하는 대신 [bicepconfig 파일에서 별칭을 구성할](bicep-config.md#aliases-for-modules)수 있습니다. 별칭을 사용 하면 모듈을 보다 쉽게 참조할 수 있습니다. 예를 들어 별칭을 사용 하면 다음에 대 한 경로의 길이를 단축할 수 있습니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/alias-definition.bicep" highlight="1" :::

## <a name="parameters"></a>매개 변수

모듈 정의에 제공 하는 매개 변수는 Bicep 파일의 매개 변수와 일치 합니다.

다음 Bicep 예제에는 storagePrefix, storageSKU 및 location의 세 가지 매개 변수가 있습니다. StorageSKU 매개 변수에는 기본값이 있으므로 배포 하는 동안 해당 매개 변수의 값을 제공할 필요가 없습니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/create-storage-account/main.bicep" highlight="3,15,17" :::

위의 예제를 모듈로 사용 하려면 해당 매개 변수에 대 한 값을 제공 합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/parent-output.bicep" highlight="14-17" :::

## <a name="set-module-scope"></a>모듈 범위 설정

모듈을 선언할 때 포함 하는 Bicep 파일에 대 한 범위와 다른 모듈에 대 한 범위를 설정할 수 있습니다. 속성을 사용 `scope` 하 여 모듈에 대 한 범위를 설정 합니다. Scope 속성을 제공하지 않으면 모듈이 부모의 대상 범위에 배포됩니다.

다음 Bicep 파일은 리소스 그룹 및 해당 리소스 그룹에 저장소 계정을 만듭니다. 파일은 구독에 배포 되지만 모듈의 범위는 새 리소스 그룹으로 지정 됩니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/rg-and-storage.bicep" highlight="2,12,19" :::

다음 예제에서는 두 개의 서로 다른 리소스 그룹에 저장소 계정을 배포 합니다. 이러한 리소스 그룹은 모두 이미 존재 해야 합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/scope-two-resource-groups.bicep" highlight="1,13,22" :::

범위 속성을 올바른 범위 개체로 설정 합니다. Bicep 파일이 리소스 그룹, 구독 또는 관리 그룹을 배포하는 경우 모듈의 범위를 해당 리소스의 기호 이름으로 설정할 수 있습니다. 또는 범위 함수를 사용하여 유효한 범위를 얻을 수 있습니다.

해당 함수는 다음과 같습니다.

- [resourceGroup](bicep-functions-scope.md#resourcegroup)
- [subscription](bicep-functions-scope.md#subscription)
- [managementGroup](bicep-functions-scope.md#managementgroup)
- [테넌트](bicep-functions-scope.md#tenant)

다음 예에서는 함수를 사용 하 여 `managementGroup` 범위를 설정 합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/function-scope.bicep" highlight="5" :::

## <a name="output"></a>출력

모듈에서 값을 가져와 기본 Bicep 파일에서 사용할 수 있습니다. 모듈에서 출력 값을 가져오려면 `outputs` module 개체의 속성을 사용 합니다.

첫 번째 예에서는 저장소 계정을 만들고 기본 끝점을 반환 합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/create-storage-account/main.bicep" highlight="33" :::

모듈로 사용 되는 경우 해당 출력 값을 가져올 수 있습니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/parent-output.bicep" highlight="20" :::

## <a name="next-steps"></a>다음 단계

- 자습서는 [Bicep 템플릿을 사용하여 Azure 리소스 배포](/learn/modules/deploy-azure-resources-by-using-bicep-templates/)를 참조하세요.
- 중요 한 값을 모듈에 전달 하려면 [Getsecret](bicep-functions-resource.md#getsecret) 함수를 사용 합니다.
