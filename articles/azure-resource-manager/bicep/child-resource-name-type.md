---
title: Bicep의 자식 리소스
description: Bicep에서 자식 리소스의 이름과 종류를 설정하는 방법을 설명합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/13/2021
ms.openlocfilehash: fbaf2eb104f213c6b4b071ce027b7cb6022aad97
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130244619"
---
# <a name="set-name-and-type-for-child-resources-in-bicep"></a>Bicep에서 자식 리소스의 이름과 종류 설정

자식 리소스는 다른 리소스의 컨텍스트 내에만 존재하는 리소스입니다. 예를 들어 [가상 머신](/azure/templates/microsoft.compute/virtualmachines) 없이는 [가상 머신 확장](/azure/templates/microsoft.compute/virtualmachines/extensions)이 존재할 수 없습니다. 확장 리소스는 가상 머신의 자식입니다.

각 부모 리소스는 특정 리소스 종류만 자식 리소스로 허용합니다. 리소스 종류의 계층은 [Bicep 리소스 참조](/azure/templates/)에서 사용할 수 있습니다.

이 문서에서는 자식 리소스를 선언할 수 있는 여러 가지 방법을 보여 줍니다.

### <a name="microsoft-learn"></a>Microsoft Learn

자식 리소스에 대 한 자세한 내용 및 실습 지침은 **Microsoft Learn** 에서 [Bicep를 사용 하 여 자식 및 확장 리소스 배포](/learn/modules/child-extension-bicep-templates) 를 참조 하세요.

## <a name="name-and-type-pattern"></a>이름 및 형식 패턴

Bicep에서 부모 리소스 내에서 또는 부모 리소스 외부에서 자식 리소스를 지정할 수 있습니다. 리소스 이름 및 리소스 종류에 대해 제공 하는 값은 자식 리소스를 선언 하는 방법에 따라 달라 집니다. 그러나 전체 이름 및 형식은 항상 같은 패턴으로 확인 됩니다. 

자식 리소스의 **전체 이름은** 패턴을 사용 합니다.

```bicep
{parent-resource-name}/{child-resource-name}
```

계층에 두 수준이 더 있는 경우에는 계속 해 서 부모 이름을 반복 합니다.

```bicep
{parent-resource-name}/{child-level1-resource-name}/{child-level2-resource-name}
```

자식 리소스의 **전체 형식은** 패턴을 사용 합니다.

```bicep
{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}
```

계층에 수준이 세 개 이상인 경우에는 계속 해 서 부모 리소스 종류를 반복 합니다.

```bicep
{resource-provider-namespace}/{parent-resource-type}/{child-level1-resource-type}/{child-level2-resource-type}
```

문자 사이의 세그먼트 수를 계산 하는 경우 `/` 형식의 세그먼트 수는 항상 이름에 있는 세그먼트 수보다 하나 이상입니다. 

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

부모 리소스 종류 내에서 정의된 경우 유형 및 이름 값을 슬래시 없이 단일 세그먼트로 서식을 지정합니다. 다음 예제에서는 파일 서비스에 대 한 자식 리소스를 포함 하는 저장소 계정을 보여 주며 파일 서비스에는 파일 공유에 대 한 자식 리소스가 있습니다. 파일 서비스의 이름은로 설정 되 `default` 고 해당 형식은로 설정 됩니다 `fileServices` . 파일 공유의 이름이 설정 되 `exampleshare` 고 해당 형식이로 설정 됩니다 `shares` .

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/insidedeclaration.bicep" highlight="9,12":::

전체 리소스 종류는 여전히 `Microsoft.Storage/storageAccounts/fileServices`와 `Microsoft.Storage/storageAccounts/fileServices/shares`입니다. `Microsoft.Storage/storageAccounts/`는 부모 리소스 종류와 버전에서 유추되므로 입력하지 않습니다. 중첩된 리소스는 선택적으로 `<segment>@<version>` 구문을 사용하여 API 버전을 선언할 수 있습니다. 중첩된 리소스가 API 버전을 생략하면 부모 리소스의 API 버전이 사용됩니다. 중첩된 리소스가 API 버전을 지정하는 경우 지정된 API 버전이 사용됩니다.

자식 리소스 이름은 및로 설정 `default` `exampleshare` 되지만 전체 이름에는 부모 이름이 포함 됩니다. `examplestorage` `default` 부모 리소스에서 가정 하기 때문에 또는를 제공 하지 않습니다.

중첩된 리소스는 부모 리소스의 속성에 액세스할 수 있습니다. 동일한 부모 리소스의 본문 내에 선언된 다른 리소스는 기호 이름을 사용하여 서로를 참조할 수 있습니다. 부모 리소스는 포함된 리소스의 속성에 액세스할 수 없으면 순환 종속성이 발생합니다.

부모 리소스 외부의 중첩된 리소스를 참조하려면 포함하는 리소스 이름과 `::` 연산자로 규정되어야 합니다. 예를 들어 자식 리소스에서 속성을 출력하려면 다음을 수행합니다.

```bicep
output childAddressPrefix string = VNet1::VNet1_Subnet1.properties.addressPrefix
```

## <a name="outside-parent-resource"></a>부모 리소스 외부에

다음 예에서는 부모 리소스 외부의 자식 리소스를 보여 줍니다. 부모 리소스가 동일한 템플릿에서 배포되지 않는 경우 또는 [루프](loops.md)를 사용하여 둘 이상의 자식 리소스를 만들려는 경우, 이 방법을 사용할 수 있습니다. 값이 부모의 기호 이름으로 설정된 자식의 부모 속성을 지정합니다. 이 구문을 사용하면 여전히 전체 리소스 종류를 선언해야 하지만 자식 리소스의 이름은 자식의 이름일 뿐입니다.

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

부모 외부의 자식 리소스를 선언할 때 전체 리소스 이름 및 형식을 사용할 수도 있습니다. 자식 리소스에 대해 parent 속성을 설정 하지 않습니다. 종속성을 유추할 수 없기 때문에이를 명시적으로 설정 해야 합니다.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/fullnamedeclaration.bicep" highlight="10,11,17,18":::

> [!IMPORTANT]
> 전체 리소스 이름 및 유형을 설정 하는 것은 권장 되는 방법이 아닙니다. 다른 방법 중 하나를 사용 하는 것 처럼 형식이 안전 하지 않습니다.

## <a name="next-steps"></a>다음 단계

* Bicep 파일을 만드는 방법에 대한 내용은 [Bicep 파일의 구조 및 구문 이해](./file.md)를 참조하세요.
* 리소스를 참조할 때 리소스 이름 형식에 대한 자세한 내용은 [참조 함수](./bicep-functions-resource.md#reference)를 참조하세요.
