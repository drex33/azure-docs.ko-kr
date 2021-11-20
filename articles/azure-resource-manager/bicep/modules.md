---
title: Bicep 모듈
description: Bicep 파일에서 모듈을 정의하는 방법과 모듈 범위를 사용하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 11/19/2021
ms.openlocfilehash: fe6f2880bd207488bb6da3e436c3e3d9cccbab1f
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132868360"
---
# <a name="bicep-modules"></a>Bicep 모듈

Bicep을 사용하면 배포를 모듈로 구성할 수 있습니다. 모듈은 다른 Bicep 파일에서 배포된 Bicep 파일일 뿐입니다. 모듈을 사용하면 배포의 복잡한 세부 정보를 캡슐화하여 Bicep 파일의 가독성을 향상시킵니다. 다른 배포에 모듈을 쉽게 다시 사용할 수도 있습니다.

조직의 다른 사용자와 모듈을 공유하려면 [템플릿 사양](../templates/template-specs.md) 또는 프라이빗 레지스트리 를 [만듭니다.](private-module-registry.md) 레지스트리의 템플릿 사양 및 모듈은 올바른 권한이 있는 사용자만 사용할 수 있습니다.

> [!TIP]
> 템플릿 사양과 프라이빗 레지스트리 중에서 선택하는 것은 대부분 기본 설정의 문제입니다. 다른 프로젝트 아티팩트 없이 템플릿 또는 Bicep 파일을 배포하는 경우 템플릿 사양이 더 쉬운 옵션입니다. 템플릿 또는 Bicep 파일을 통해 프로젝트 아티팩트 배포하는 경우 개발 작업과 프라이빗 레지스트리를 통합한 다음 레지스트리에서 모든 아티팩트만 더 쉽게 배포할 수 있습니다.

Bicep 모듈은 중첩된 템플릿이 있는 단일 Azure Resource Manager [템플릿으로 변환됩니다.](../templates/linked-templates.md#nested-template)

### <a name="microsoft-learn"></a>Microsoft Learn

모듈 및 실습 지침에 대한 자세한 내용은 **Microsoft Learn** [모듈을 사용하여 구성 가능한 Bicep 파일 만들기를](/learn/modules/create-composable-bicep-files-using-modules/) 참조하세요.

## <a name="definition-syntax"></a>정의 구문

모듈을 정의하기 위한 기본 구문은 다음과 같습니다.

```bicep
module <symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}
```

따라서 간단한 실제 예제는 다음과 같습니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/local-file-definition.bicep" :::

기호 이름을 사용하여 Bicep 파일의 다른 부분에서 모듈을 참조합니다. 예를 들어 기호 이름을 사용하여 모듈에서 출력을 얻을 수 있습니다. 기호 이름에는 a-z, A-Z, 0-9 및 밑호( )가 포함될 수 `_` 있습니다. 이름은 숫자로 시작할 수 없습니다. 모듈은 매개 변수, 변수 또는 리소스와 동일한 이름을 가질 수 없습니다.

경로는 로컬 파일 또는 레지스트리의 파일일 수 있습니다. 자세한 내용은 [모듈 경로 를 참조하세요.](#path-to-module)

**name** 속성이 필요합니다. 생성된 템플릿에서 중첩된 배포 리소스의 이름이 됩니다.

주 **파일의 범위와** 다른 범위를 지정해야 하는 경우 범위 속성을 추가합니다. 자세한 내용은 [모듈 범위 설정을 참조하세요.](#set-module-scope)

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/scope-definition.bicep" highlight="4" :::

**모듈을 조건부로 배포하려면** `if` 식을 추가합니다. 사용은 [리소스를 조건부로 배포하는](conditional-resource-deployment.md)것과 비슷합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/conditional-definition.bicep" highlight="2" :::

**모듈의 인스턴스를 두 개 이상** 배포하려면 식을 추가합니다. `for` `batchSize`데코레이터를 사용하여 인스턴스가 직렬로 배포되는지 아니면 병렬로 배포되는지를 지정할 수 있습니다. 자세한 내용은 [Bicep의 반복 루프를 참조하세요.](loops.md)

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/iterative-definition.bicep" highlight="3" :::

리소스와 마찬가지로 모듈은 다른 모듈 또는 리소스에 의존하지 않는 한 병렬로 배포됩니다. 일반적으로 암시적으로 결정되기 때문에 의존성 설정은 필요하지 않습니다. 명시적 종속성을 설정해야 하는 경우 모듈 정의에 를 추가할 수 `dependsOn` 있습니다. dependencies에 대한 자세한 내용은 [리소스 의존성 을 참조하세요.](resource-declaration.md#dependencies)

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/dependsOn-definition.bicep" highlight="6-8" :::

## <a name="path-to-module"></a>모듈 경로

모듈의 파일은 로컬 파일 또는 외부 파일일 수 있습니다. 외부 파일은 템플릿 사양 또는 Bicep 모듈 레지스트리에 있을 수 있습니다. 이러한 옵션은 모두 아래에 표시됩니다.

### <a name="local-file"></a>로컬 파일

모듈이 **로컬 파일인** 경우 해당 파일에 대한 상대 경로를 제공합니다. 플랫폼 간에 일관된 컴파일을 보장하려면 슬래시(/) 디렉터리 구분 기호를 사용하여 Bicep의 모든 경로를 지정해야 합니다. Windows 백슬래시(\\) 문자는 지원되지 않습니다. 경로에는 공백을 포함할 수 있습니다.

예를 들어 주 파일에서 디렉터리에서 한 수준 높은 파일을 배포하려면 다음을 사용합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/local-file-definition.bicep" highlight="1" :::

### <a name="file-in-registry"></a>레지스트리의 파일

[레지스트리에 모듈을 게시한](bicep-cli.md#publish)경우 해당 모듈에 연결할 수 있습니다. Azure 컨테이너 레지스트리의 이름과 모듈 경로를 제공합니다. 다음 구문으로 모듈 경로를 지정합니다.

```bicep
module <symbolic-name> 'br:<registry-name>.azurecr.io/<file-path>:<tag>' = {
```

- **br은** Bicep 레지스트리의 스키마 이름입니다.
- **파일 경로는** `repository` Azure Container Registry 호출됩니다. **파일 경로는** 문자로 구분된 세그먼트를 포함할 수 `/` 있습니다.
- **태그는** 모듈의 버전을 지정하는 데 사용됩니다.

예를 들어:

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/registry-definition.bicep" highlight="1" :::

레지스트리에서 모듈을 참조하는 경우 Visual Studio Code Bicep 확장은 [자동으로 bicep 복원을](bicep-cli.md#restore) 호출하여 외부 모듈을 로컬 캐시에 복사합니다. 외부 모듈을 복원하는 데 몇 분 정도 걸립니다. 모듈에 대한 intellisense가 즉시 작동하지 않으면 복원이 완료되기를 기다립니다.

레지스트리의 모듈에 대한 전체 경로는 길 수 있습니다. 모듈을 사용할 때마다 전체 경로를 제공하는 대신 [bicepconfig.json 파일 에서 별칭을 구성할](bicep-config-modules.md#aliases-for-modules)수 있습니다. 별칭을 사용하면 모듈을 더 쉽게 참조할 수 있습니다. 예를 들어 별칭을 사용하면 경로를 다음과 같이 줄일 수 있습니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/alias-definition.bicep" highlight="1" :::

### <a name="file-in-template-spec"></a>템플릿 사양의 파일

[템플릿 사양](../templates/template-specs.md)을 만든 후 모듈에서 해당 템플릿 사양에 연결할 수 있습니다. 템플릿 사양을 다음 형식으로 지정합니다.

```bicep
module <symbolic-name> 'ts:<sub-id>/<rg-name>/<template-spec-name>:<version>' = {
```

그러나 템플릿 사양을 포함하는 리소스 그룹에 대한 [별칭을 만들어](bicep-config-modules.md) Bicep 파일을 간소화할 수 있습니다. 별칭을 사용하는 경우 구문은 다음과 같습니다.

```bicep
module <symbolic-name> 'ts/<alias>:<template-spec-name>:<version>' = {
```

다음 모듈에서는 템플릿 사양을 배포하여 스토리지 계정을 만듭니다. 템플릿 사양에 대한 구독 및 리소스 그룹은 **ContosoSpecs라는** 별칭에 정의되어 있습니다.

```bicep
module stgModule 'ts/ContosoSpecs:storageSpec:2.0' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: 'examplestg1'
  }
}
```

## <a name="parameters"></a>매개 변수

모듈 정의에 제공하는 매개 변수는 Bicep 파일의 매개 변수와 일치합니다.

다음 Bicep 예제에는 storagePrefix, storageSKU 및 location의 세 가지 매개 변수가 있습니다. storageSKU 매개 변수에는 기본값이 있으므로 배포하는 동안 해당 매개 변수에 대한 값을 제공할 필요가 없습니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/create-storage-account/main.bicep" highlight="3,15,17" :::

앞의 예제를 모듈로 사용하려면 해당 매개 변수에 대한 값을 제공합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/parent-output.bicep" highlight="14-17" :::

## <a name="set-module-scope"></a>모듈 범위 설정

모듈을 선언할 때 포함하는 Bicep 파일의 범위와 다른 모듈의 범위를 설정할 수 있습니다. 속성을 사용하여 `scope` 모듈의 범위를 설정합니다. Scope 속성을 제공하지 않으면 모듈이 부모의 대상 범위에 배포됩니다.

다음 Bicep 파일은 해당 리소스 그룹에 리소스 그룹 및 스토리지 계정을 만듭니다. 파일은 구독에 배포되지만 모듈의 범위는 새 리소스 그룹으로 한정됩니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/rg-and-storage.bicep" highlight="2,12,19" :::

다음 예제는 스토리지 계정을 서로 다른 두 리소스 그룹에 배포합니다. 이러한 리소스 그룹은 모두 이미 존재해야 합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/scope-two-resource-groups.bicep" highlight="1,13,22" :::

범위 속성을 유효한 범위 개체로 설정합니다. Bicep 파일이 리소스 그룹, 구독 또는 관리 그룹을 배포하는 경우 모듈의 범위를 해당 리소스의 기호 이름으로 설정할 수 있습니다. 또는 범위 함수를 사용하여 유효한 범위를 얻을 수 있습니다.

해당 함수는 다음과 같습니다.

- [resourceGroup](bicep-functions-scope.md#resourcegroup)
- [subscription](bicep-functions-scope.md#subscription)
- [managementGroup](bicep-functions-scope.md#managementgroup)
- [테넌트](bicep-functions-scope.md#tenant)

다음 예제에서는 `managementGroup` 함수를 사용하여 범위를 설정합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/modules/function-scope.bicep" highlight="5" :::

## <a name="output"></a>출력

모듈에서 값을 얻고 기본 Bicep 파일에서 사용할 수 있습니다. 모듈에서 출력 값을 얻으려면 모듈 `outputs` 개체의 속성을 사용합니다.

첫 번째 예제에서는 스토리지 계정을 만들고 기본 엔드포인트를 반환합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/create-storage-account/main.bicep" highlight="33" :::

모듈로 사용하면 해당 출력 값을 얻을 수 있습니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/modules/parent-output.bicep" highlight="20" :::

## <a name="next-steps"></a>다음 단계

- 자습서는 [Bicep 템플릿을 사용하여 Azure 리소스 배포](/learn/modules/deploy-azure-resources-by-using-bicep-templates/)를 참조하세요.
- 중요 한 값을 모듈에 전달 하려면 [Getsecret](bicep-functions-resource.md#getsecret) 함수를 사용 합니다.
