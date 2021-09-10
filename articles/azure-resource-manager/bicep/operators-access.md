---
title: Bicep 접근자 연산자
description: Bicep 리소스 액세스 연산자 및 속성 액세스 연산자에 대해 설명합니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: b5eebb9b5dd6d39ae790b8fda7133e94ecd0cdb5
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224282"
---
# <a name="bicep-accessor-operators"></a>Bicep 접근자 연산자

접근자 연산자는 자식 리소스, 개체에 대한 속성 및 배열의 요소에 액세스하는 데 사용됩니다. 속성 접근자를 사용하여 일부 함수를 사용할 수도 있습니다.

| 연산자 | Name |
| ---- | ---- |
| `[]` | [인덱스 접근자](#index-accessor) |
| `.`  | [함수 접근자](#function-accessor) |
| `::` | [중첩된 리소스 접근자](#nested-resource-accessor) |
| `.`  | [속성 접근자](#property-accessor) |

## <a name="index-accessor"></a>인덱스 접근자

`array[index]`

`object['index']`

배열의 요소를 가져오려면 `[index]`를 사용하고 인덱스에 정수를 제공합니다.

다음 예제에서는 배열의 요소를 가져옵니다.

```bicep
var arrayVar = [
  'Coho'
  'Contoso'
  'Fabrikan'
]

output accessorResult string = arrayVar[1]
``` 

예의 출력:

| Name | 유형 | 값 |
| ---- | ---- | ---- |
| accessorResult | 문자열 | 'Contoso' |

인덱스 접근자를 사용하여 이름을 기준으로 개체 속성을 가져올 수도 있습니다. 인덱스에 정수가 아닌 문자열을 사용해야 합니다. 다음 예제에서는 개체에 대한 속성을 가져옵니다.

```bicep
var environmentSettings = {
  dev: {
    name: 'Development'
  }
  prod: {
    name: 'Production'
  }
}

output accessorResult string = environmentSettings['dev'].name
```

예의 출력:

| Name | 유형 | 값 |
| ---- | ---- | ---- |
| accessorResult | 문자열 | 'Development' |

## <a name="function-accessor"></a>함수 접근자

`resourceName.functionName()`

두 함수 [getSecret](bicep-functions-resource.md#getsecret) 및 [list*](bicep-functions-resource.md#list)는 함수를 호출하기 위한 접근자 연산자를 지원합니다. 이 두 함수만이 접근자 연산자를 지원하는 함수입니다.

### <a name="example"></a>예제

다음 예제에서는 기존 키 자격 증명 모음을 참조한 다음, `getSecret`를 사용하여 모듈에 비밀을 전달합니다.

```bicep
resource kv 'Microsoft.KeyVault/vaults@2019-09-01' existing = {
  name: kvName
  scope: resourceGroup(subscriptionId, kvResourceGroup )
}

module sql './sql.bicep' = {
  name: 'deploySQL'
  params: {
    sqlServerName: sqlServerName
    adminLogin: adminLogin
    adminPassword: kv.getSecret('vmAdminPassword')
  }
}
```

## <a name="nested-resource-accessor"></a>중첩된 리소스 접근자

`parentResource::nestedResource`

중첩된 리소스는 다른 리소스 내에서 선언된 리소스입니다. 부모 리소스 외부에서 중첩된 리소스에 액세스하려면 중첩된 리소스 접근자 `::`을 사용합니다.

부모 리소스 내에서 기호 이름만으로 중첩된 리소스를 참조합니다. 부모 리소스 외부에서 중첩된 리소스를 참조하는 경우에만 중첩된 리소스 접근자를 사용해야 합니다.

### <a name="example"></a>예제

다음 예제에서는 부모 리소스 내부와 부모 리소스의 외부에서 중첩된 리소스를 참조하는 방법을 보여 줍니다.

```bicep
resource demoParent 'demo.Rp/parentType@2020-01-01' = {
  name: 'demoParent'
  location: 'West US'

  // Declare a nested resource within 'demoParent'
  resource demoNested 'childType' = {
    name: 'demoNested'
    properties: {
      displayName: 'The nested instance.'
    }
  }

  // Declare another nested resource
  resource demoSibling 'childType' = {
    name: 'demoSibling'
    properties: {
      // Use symbolic name to reference because this line is within demoParent
      displayName: 'Sibling of ${demoNested.properties.displayName}'
    }
  }
}

// Use nested accessor to reference because this line is outside of demoParent
output displayName string = demoParent::demoNested.properties.displayName
```

## <a name="property-accessor"></a>속성 접근자

`objectName.propertyName`

개체의 속성에 액세스하려면 속성 접근자를 사용합니다. 속성 접근자는 개체인 매개 변수 및 변수를 포함하여 모든 개체와 함께 사용할 수 있습니다. 개체가 아닌 식에 속성 액세스를 사용하는 경우 오류가 발생합니다.

### <a name="example"></a>예제

다음 예제에서는 개체 변수 및 속성에 액세스하는 방법을 보여 줍니다.

```bicep
var x = {
  y: {
    z: 'Hello'
    a: true
  }
  q: 42
}

output outputZ string = x.y.z
output outputQ int = x.q
```

예의 출력:

| Name | 유형 | 값 |
| ---- | ---- | ---- |
| `outputZ` | 문자열 | 'Hello' |
| `outputQ` | 정수 | 42 |

일반적으로 Bicep 파일에 배포된 리소스에 속성 접근자를 사용합니다. 다음 예제에서는 공용 IP 주소를 만들고 속성 접근자를 사용하여 배포된 리소스에서 값을 반환합니다.

```bicep
resource publicIp 'Microsoft.Network/publicIPAddresses@2020-06-01' = {
  name: publicIpResourceName
  location: location
  properties: {
    publicIPAllocationMethod: dynamicAllocation ? 'Dynamic' : 'Static'
    dnsSettings: {
      domainNameLabel: publicIpDnsLabel
    }
  }
}

// Use property accessor to get value
output ipFqdn string = publicIp.properties.dnsSettings.fqdn
```

## <a name="next-steps"></a>다음 단계

- 예를 실행하려면 Azure CLI 또는 Azure PowerShell을 사용하여 [Bicep 파일을 배포](./quickstart-create-bicep-use-visual-studio-code.md#deploy-the-bicep-file)합니다.
- Bicep 파일을 만들려면 [빠른 시작: Visual Studio Code로 Bicep 파일 만들기](./quickstart-create-bicep-use-visual-studio-code.md)를 참조하세요.
- Bicep 유형 오류를 해결하는 방법에 대한 정보는 [Bicep의 모든 기능](./bicep-functions-any.md)을 참조하세요.
