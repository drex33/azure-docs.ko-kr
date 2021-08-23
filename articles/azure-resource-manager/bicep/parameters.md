---
title: Bicep 파일의 매개 변수
description: Bicep 파일에서 매개 변수를 정의하는 방법을 설명합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 8701d437a34d364ff6f6e2d58cbf84dc28a79798
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634214"
---
# <a name="parameters-in-bicep"></a>Bicep의 매개 변수

이 문서에서는 Bicep 파일에서 매개 변수를 정의하고 사용하는 방법을 설명합니다. 매개 변수에 다른 값을 제공하여 여러 환경에서 Bicep 파일을 다시 사용할 수 있습니다.

Resource Manager는 배포 작업을 시작하기 전에 매개 변수 값을 확인합니다. 매개 변수가 사용될 때마다 Resource Manager는 매개 변수를 확인된 값으로 바꿉니다.

각 매개 변수는 [데이터 형식](data-types.md) 중 하나로 설정해야 합니다.

## <a name="minimal-declaration"></a>최소 선언

각 매개 변수에는 이름과 형식이 필요합니다. 매개 변수는 동일한 범위에 있는 변수, 리소스, 출력 또는 다른 매개 변수와 동일한 이름을 가질 수 없습니다.

```bicep
param demoString string
param demoInt int
param demoBool bool
param demoObject object
param demoArray array
```

## <a name="decorators"></a>데코레이터

매개 변수는 제약 조건 또는 메타데이터에 데코레이터를 사용합니다. 데코레이터는 `@expression` 형식이며 매개 변수의 선언 위에 배치됩니다.

```bicep
@expression
param stgAcctName string
```

아래 섹션에서 이 문서는 Bicep 파일에서 사용할 수 있는 데코레이터를 사용하는 방법을 보여 줍니다.

## <a name="secure-parameters"></a>보안 매개 변수

문자열 또는 개체 매개 변수를 보안으로 표시할 수 있습니다. 보안 매개 변수의 값은 배포 기록에 저장되지 않으며 기록되지도 않습니다.

```bicep
@secure()
param demoPassword string

@secure()
param demoSecretObject object
```

## <a name="allowed-values"></a>허용되는 값

매개 변수에 허용되는 값을 정의할 수 있습니다. 배열에 허용되는 값을 제공합니다. 허용되는 값 중 하나가 아닌 매개 변수에 값이 전달되면 유효성 검사 중에 배포가 실패합니다.

```bicep
@allowed([
  'one'
  'two'
])
param demoEnum string
```

## <a name="default-value"></a>기본값

매개 변수의 기본값을 지정할 수 있습니다. 기본값은 배포 중에 값이 제공되지 않는 경우에 사용됩니다.

```bicep
param demoParam string = 'Contoso'
```

매개 변수의 다른 속성과 함께 기본값을 지정하려면 다음 구문을 사용합니다.

```bicep
@allowed([
  'Contoso'
  'Fabrikam'
])
param demoParam string = 'Contoso'
```

기본값으로 식을 사용할 수 있습니다. 매개 변수 섹션에서는 [reference](bicep-functions-resource.md#reference) 함수 또는 [list](bicep-functions-resource.md#list) 함수를 사용할 수 없습니다. 이러한 함수는 리소스의 런타임 상태를 가져오며, 매개 변수를 확인할 때 배포 전에 실행할 수 없습니다.

다른 매개 변수 속성에서는 식이 허용되지 않습니다.

```bicep
param location string = resourceGroup().location
```

다른 매개 변수 값을 사용하여 기본값을 빌드할 수 있습니다. 다음 템플릿은 사이트 이름에서 호스트 계획 이름을 구성합니다.

```bicep
param siteName string = 'site${uniqueString(resourceGroup().id)}'
param hostingPlanName string = '${siteName}-plan'
```

## <a name="length-constraints"></a>길이 제약 조건

문자열 및 배열 매개 변수의 최소 및 최대 길이를 지정할 수 있습니다. 제약 조건 중 하나 또는 둘 모두를 설정할 수 있습니다. 문자열의 경우 길이는 문자 수를 나타냅니다. 배열의 경우 길이는 배열의 항목 수를 나타냅니다.

다음 예제에서는 두 개의 매개 변수를 선언합니다. 한 가지 매개 변수는 3~24자여야 하는 스토리지 계정 이름에 대한 것입니다. 다른 매개 변수는 1~5개 항목이 있어야 하는 배열입니다.

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

@minLength(1)
@maxLength(5)
param appNames array
```

## <a name="integer-constraints"></a>정수 제약 조건

정수 매개 변수의 최솟값과 최댓값을 설정할 수 있습니다. 제약 조건 중 하나 또는 둘 모두를 설정할 수 있습니다.

```bicep
@minValue(1)
@maxValue(12)
param month int
```

## <a name="description"></a>Description

제공하는 값을 사용자가 이해할 수 있도록 매개 변수에 대한 설명을 추가합니다. 포털을 통해 템플릿을 배포할 때 설명의 텍스트는 해당 매개 변수에 대한 팁으로 자동으로 사용됩니다. 텍스트가 매개 변수 이름에서 유추할 수 있는 것보다 더 많은 정보를 제공하는 경우에만 설명을 추가합니다.

```bicep
@description('Must be at least Standard_A3 to support 2 NICs.')
param virtualMachineSize string = 'Standard_DS1_v2'
```

## <a name="use-parameter"></a>매개 변수 사용

매개 변수의 값을 참조하려면 매개 변수 이름을 사용합니다. 다음 예제에서는 키 자격 증명 모음 이름에 매개 변수 값을 사용합니다.

```bicep
param vaultName string = 'keyVault${uniqueString(resourceGroup().id)}'

resource keyvault 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: vaultName
  ...
}
```

## <a name="objects-as-parameters"></a>개체를 매개 변수로 사용

관련 값을 개체로 전달하면 더 쉽게 구성할 수 있습니다. 이렇게 하면 템플릿의 매개 변수 수도 줄어듭니다.

다음 예제에서는 개체인 매개 변수를 보여줍니다. 기본값은 개체의 예상 속성을 표시합니다. 이러한 속성은 배포할 리소스를 정의할 때 사용됩니다.

```bicep
param vNetSettings object = {
  name: 'VNet1'
  location: 'eastus'
  addressPrefixes: [
    {
      name: 'firstPrefix'
      addressPrefix: '10.0.0.0/22'
    }
  ]
  subnets: [
    {
      name: 'firstSubnet'
      addressPrefix: '10.0.0.0/24'
    }
    {
      name: 'secondSubnet'
      addressPrefix: '10.0.1.0/24'
    }
  ]
}
resource vnet 'Microsoft.Network/virtualNetworks@2020-06-01' = {
  name: vNetSettings.name
  location: vNetSettings.location
  properties: {
    addressSpace: {
      addressPrefixes: [
        vNetSettings.addressPrefixes[0].addressPrefix
      ]
    }
    subnets: [
      {
        name: vNetSettings.subnets[0].name
        properties: {
          addressPrefix: vNetSettings.subnets[0].addressPrefix
        }
      }
      {
        name: vNetSettings.subnets[1].name
        properties: {
          addressPrefix: vNetSettings.subnets[1].addressPrefix
        }
      }
    ]
  }
}
```

## <a name="example-templates"></a>예제 템플릿

다음 예제에서는 매개 변수를 사용하는 시나리오를 보여줍니다.

|템플릿  |Description  |
|---------|---------|
|[parameters with functions for default values](https://github.com/Azure/azure-docs-bicep-samples/blob/main/bicep/parameterswithfunctions.bicep)(기본값에 대한 함수가 있는 매개 변수) | 매개 변수의 기본값을 정의할 때 Bicep 함수를 사용하는 방법을 보여 줍니다. Bicep 파일은 리소스를 배포하지 않으며, 매개 변수 값을 구성하고 해당 값을 반환합니다. |
|[parameter object](https://github.com/Azure/azure-docs-bicep-samples/blob/main/bicep/parameterobject.bicep)(매개 변수 개체) | 매개 변수에 대한 개체 사용을 보여 줍니다. Bicep 파일은 리소스를 배포하지 않으며, 매개 변수 값을 구성하고 해당 값을 반환합니다. |

## <a name="next-steps"></a>다음 단계

- 매개 변수에 사용할 수 있는 속성에 대한 자세한 내용은 [Bicep 파일의 구조 및 구문 이해](file.md)를 참조하세요.
- 매개 변수 값을 파일로 전달하는 방법에 대한 자세한 내용은 [Bicep 매개 변수 파일 만들기](parameter-files.md)를 참조하세요.
