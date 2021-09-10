---
title: Bicep의 자식 리소스
description: Bicep에서 자식 리소스의 이름과 종류를 설정하는 방법을 설명합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 879c325ee64307e7c548efa4ef7ba34eb68cc896
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122568162"
---
# <a name="set-name-and-type-for-child-resources-in-bicep"></a>Bicep에서 자식 리소스의 이름과 종류 설정

자식 리소스는 다른 리소스의 컨텍스트 내에만 존재하는 리소스입니다. 예를 들어 [가상 머신](/azure/templates/microsoft.compute/virtualmachines) 없이는 [가상 머신 확장](/azure/templates/microsoft.compute/virtualmachines/extensions)이 존재할 수 없습니다. 확장 리소스는 가상 머신의 자식입니다.

각 부모 리소스는 특정 리소스 종류만 자식 리소스로 허용합니다. 자식 리소스의 리소스 종류는 부모 리소스에 대한 리소스 종류를 포함합니다. 예를 들어 `Microsoft.Web/sites/config`와 `Microsoft.Web/sites/extensions`는 둘 다 `Microsoft.Web/sites`의 자식 리소스입니다. 허용되는 리소스 종류는 부모 리소스의 [템플릿 스키마](https://github.com/Azure/azure-resource-manager-schemas)에서 지정됩니다.

Bicep에서 부모 리소스 내에서 또는 부모 리소스 외부에서 자식 리소스를 지정할 수 있습니다. 리소스 이름 및 리소스 종류에 대해 제공하는 값은 자식 리소스가 부모 리소스의 내부에 정의되는지 아니면 외부에 정의되는지에 따라 달라집니다.

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

부모 리소스 종류 내에서 정의된 경우 유형 및 이름 값을 슬래시 없이 단일 세그먼트로 서식을 지정합니다. 다음 예제에서는 파일 서비스 및 파일 공유가 있는 스토리지 계정을 보여 줍니다. 파일 서비스의 이름은 **default** 로 설정되고 해당 형식은 **fileServices** 로 설정됩니다. 파일 공유의 이름은 **exampleshare** 로 설정되고 형식은 **shares** 로 설정됩니다.

```bicep
resource storage 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }

  resource service 'fileServices' = {
    name: 'default'

    resource share 'shares' = {
      name: 'exampleshare'
    }
  }
}
```

전체 리소스 종류는 여전히 `Microsoft.Storage/storageAccounts/fileServices`와 `Microsoft.Storage/storageAccounts/fileServices/shares`입니다. `Microsoft.Storage/storageAccounts/`는 부모 리소스 종류와 버전에서 유추되므로 입력하지 않습니다. 중첩된 리소스는 선택적으로 `<segment>@<version>` 구문을 사용하여 API 버전을 선언할 수 있습니다. 중첩된 리소스가 API 버전을 생략하면 부모 리소스의 API 버전이 사용됩니다. 중첩된 리소스가 API 버전을 지정하는 경우 지정된 API 버전이 사용됩니다.

자식 리소스 이름은 **default** 및 **exampleshare** 로 설정되지만 전체 이름에는 부모 이름이 포함됩니다. **examplestorage** 또는 **default** 는 부모 리소스에서 온다고 가정하기 때문에 제공하지 않습니다.

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

```bicep
resource storage 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: 'examplestorage'
  location: resourceGroup().location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
}

resource service 'Microsoft.Storage/storageAccounts/fileServices@2021-02-01' = {
  name: 'default'
  parent: storage
}

resource share 'Microsoft.Storage/storageAccounts/fileServices/shares@2021-02-01' = {
  name: 'exampleshare'
  parent: service
}
```

자식 리소스 기호 이름을 참조하는 것은 부모 참조와 동일하게 작동합니다.

## <a name="next-steps"></a>다음 단계

* Bicep 파일을 만드는 방법에 대한 내용은 [Bicep 파일의 구조 및 구문 이해](./file.md)를 참조하세요.
* 리소스를 참조할 때 리소스 이름 형식에 대한 자세한 내용은 [참조 함수](./bicep-functions-resource.md#reference)를 참조하세요.
