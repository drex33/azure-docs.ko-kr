---
title: Bicep의 자식 리소스
description: Bicep에서 자식 리소스의 이름과 종류를 설정하는 방법을 설명합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/13/2021
ms.openlocfilehash: 2d928ec83559a1bd57adde3cbae98c589bb1cd15
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128622002"
---
# <a name="set-name-and-type-for-child-resources-in-bicep"></a>Bicep에서 자식 리소스의 이름과 종류 설정

자식 리소스는 다른 리소스의 컨텍스트 내에만 존재하는 리소스입니다. 예를 들어 [가상 머신](/azure/templates/microsoft.compute/virtualmachines) 없이는 [가상 머신 확장](/azure/templates/microsoft.compute/virtualmachines/extensions)이 존재할 수 없습니다. 확장 리소스는 가상 머신의 자식입니다.

각 부모 리소스는 특정 리소스 종류만 자식 리소스로 허용합니다. 리소스 유형의 계층 구조는 [Bicep 리소스 참조](/azure/templates/)에서 사용할 수 있습니다.

이 문서에서는 자식 리소스를 선언할 수 있는 다양한 방법을 보여줍니다.

### <a name="microsoft-learn"></a>Microsoft Learn

자식 리소스에 대한 자세한 내용과 실습 지침은 **Microsoft Learn** [Bicep을 사용하여 자식 및 확장 리소스 배포를](/learn/modules/child-extension-bicep-templates) 참조하세요.

## <a name="name-and-type-pattern"></a>이름 및 형식 패턴

Bicep에서 부모 리소스 내에서 또는 부모 리소스 외부에서 자식 리소스를 지정할 수 있습니다. 리소스 이름 및 리소스 종류에 대해 제공하는 값은 자식 리소스를 선언하는 방법에 따라 달라집니다. 그러나 전체 이름과 형식은 항상 동일한 패턴으로 확인합니다. 

자식 리소스의 **전체 이름은** 패턴을 사용합니다.

```bicep
{parent-resource-name}/{child-resource-name}
```

계층 구조에 두 개 이상의 수준이 있는 경우 부모 이름을 계속 반복합니다.

```bicep
{parent-resource-name}/{child-level1-resource-name}/{child-level2-resource-name}
```

자식 리소스의 **전체 형식은** 패턴을 사용합니다.

```bicep
{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}
```

계층 구조에 두 개 이상의 수준이 있는 경우 부모 리소스 종류를 계속 반복합니다.

```bicep
{resource-provider-namespace}/{parent-resource-type}/{child-level1-resource-type}/{child-level2-resource-type}
```

문자 사이의 세그먼트 수를 계산하는 경우 `/` 형식의 세그먼트 수는 항상 이름에 있는 세그먼트 수보다 한 개 이상입니다. 

## <a name="within-parent-resource"></a>부모 리소스 내에서

다음 예에서는 부모 리소스의 리소스 속성 내에 포함된 자식 리소스를 보여 줍니다.

```bicep
resource <parent-resource-symbolic-name> '<resource-type>@<api-version>' = {
  <parent-resource-properties>

  resource <child-resource-symbolic-name> '<child-resource-type>' = {
    <child-resource-properties>
  }
}
```

중첩된 리소스 선언은 부모 리소스의 최상위 구문 수준에 표시되어야 합니다. 각 수준이 부모 리소스의 자식 종류인 경우 선언은 임의로 깊게 여러 번 중첩될 수 있습니다.

부모 리소스 종류 내에서 정의된 경우 유형 및 이름 값을 슬래시 없이 단일 세그먼트로 서식을 지정합니다. 다음 예제에서는 파일 서비스에 대한 자식 리소스가 있는 스토리지 계정을 보여 하며 파일 서비스에는 파일 공유에 대한 자식 리소스가 있습니다. 파일 서비스의 이름은 로 `default` 설정되고 해당 형식은 로 `fileServices` 설정됩니다. 파일 공유의 이름이 `exampleshare` 설정되고 해당 형식이 로 `shares` 설정됩니다.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/insidedeclaration.bicep" highlight="9,12":::

전체 리소스 종류는 여전히 `Microsoft.Storage/storageAccounts/fileServices`와 `Microsoft.Storage/storageAccounts/fileServices/shares`입니다. `Microsoft.Storage/storageAccounts/`는 부모 리소스 종류와 버전에서 유추되므로 입력하지 않습니다. 중첩된 리소스는 선택적으로 `<segment>@<version>` 구문을 사용하여 API 버전을 선언할 수 있습니다. 중첩된 리소스가 API 버전을 생략하면 부모 리소스의 API 버전이 사용됩니다. 중첩된 리소스가 API 버전을 지정하는 경우 지정된 API 버전이 사용됩니다.

자식 리소스 이름은 `default` 로 `exampleshare` 설정되지만 전체 이름에는 부모 이름이 포함됩니다. 또는 는 `examplestorage` 부모 `default` 리소스에서 가정하기 때문에 제공하지 않습니다.

중첩된 리소스는 부모 리소스의 속성에 액세스할 수 있습니다. 동일한 부모 리소스의 본문 내에 선언된 다른 리소스는 기호 이름을 사용하여 서로를 참조할 수 있습니다. 부모 리소스는 포함된 리소스의 속성에 액세스할 수 없으면 순환 종속성이 발생합니다.

부모 리소스 외부의 중첩된 리소스를 참조하려면 포함하는 리소스 이름과 `::` 연산자로 규정되어야 합니다. 예를 들어 자식 리소스에서 속성을 출력하려면 다음을 수행합니다.

```bicep
output childAddressPrefix string = VNet1::VNet1_Subnet1.properties.addressPrefix
```

## <a name="outside-parent-resource"></a>부모 리소스 외부에

다음 예에서는 부모 리소스 외부의 자식 리소스를 보여 줍니다. 부모 리소스가 동일한 템플릿에서 배포되지 않는 경우 또는 [루프](loop-resources.md)를 사용하여 둘 이상의 자식 리소스를 만들려는 경우, 이 방법을 사용할 수 있습니다. 값이 부모의 기호 이름으로 설정된 자식의 부모 속성을 지정합니다. 이 구문을 사용하면 여전히 전체 리소스 종류를 선언해야 하지만 자식 리소스의 이름은 자식의 이름일 뿐입니다.

```bicep
resource <parent-resource-symbolic-name> '<resource-type>@<api-version>' = {
  name: 'myParent'
  <parent-resource-properties>
}

resource <child-resource-symbolic-name> '<child-resource-type>@<api-version>' = {
  parent: <parent-resource-symbolic-name>
  name: 'myChild'
  <child-resource-properties>
}
```

부모 리소스 외부에 정의된 경우 부모 유형 및 이름을 포함하도록 슬래시를 사용하여 유형의 서식을 지정합니다.

다음 예제에서는 루트 수준에서 모두 정의된 스토리지 계정, 파일 서비스 및 파일 공유를 보여 줍니다.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/outsidedeclaration.bicep" highlight="10,12,15,17":::

자식 리소스 기호 이름을 참조하는 것은 부모 참조와 동일하게 작동합니다.

## <a name="full-resource-name-outside-parent"></a>부모 외부의 전체 리소스 이름

부모 외부에서 자식 리소스를 선언할 때 전체 리소스 이름 및 형식을 사용할 수도 있습니다. 자식 리소스에 부모 속성을 설정하지 않습니다. 종속성을 유추할 수 없으므로 명시적으로 설정해야 합니다.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/fullnamedeclaration.bicep" highlight="10,11,17,18":::

> [!IMPORTANT]
> 전체 리소스 이름 및 유형을 설정하는 것은 권장되는 접근 방식이 아닙니다. 다른 방법 중 하나를 사용하는 것만큼 형식이 안전하지 않습니다.

## <a name="next-steps"></a>다음 단계

* Bicep 파일을 만드는 방법에 대한 내용은 [Bicep 파일의 구조 및 구문 이해](./file.md)를 참조하세요.
* 리소스를 참조할 때 리소스 이름 형식에 대한 자세한 내용은 [참조 함수](./bicep-functions-resource.md#reference)를 참조하세요.
