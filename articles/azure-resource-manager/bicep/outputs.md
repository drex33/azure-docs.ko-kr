---
title: Bicep의 출력
description: Bicep에서 출력 값을 정의하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: c9b8e0bb4bfb4533b66170c60c8da7b1073a0853
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130236079"
---
# <a name="outputs-in-bicep"></a>Bicep의 출력

이 문서에서는 Bicep 파일에서 출력 값을 정의하는 방법을 설명합니다. 배포된 리소스에서 값을 반환해야 하는 경우 출력을 사용합니다.

## <a name="define-output-values"></a>출력 값 정의

출력 값을 정의하는 구문은 다음과 같습니다.

```bicep
output <name> <data-type> = <value>
```

각 출력 값은 데이터 [형식](data-types.md)중 하나로 확인되어야 합니다.

다음 예제에서는 배포된 리소스에서 속성을 반환하는 방법을 보여 줍니다. 예제에서 `publicIP` 는 Bicep 파일에 배포된 공용 IP 주소의 기호 이름입니다. 출력 값은 공용 IP 주소의 정규화된 도메인 이름을 가져옵니다.

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

다음 예제에서는 다양한 형식의 출력을 반환하는 방법을 보여 있습니다.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/outputs/output.bicep":::

이름에 하이픈이 포함된 속성을 출력해야 하는 경우 점 표기법 대신 이름 주위에 대괄호를 사용합니다. 예를 들어, `.property-name` 대신 `['property-name']`을 사용합니다.

```bicep
var user = {
  'user-name': 'Test Person'
}

output stringOutput string = user['user-name']
```

반환할 값이 배포의 조건에 따라 달라지는 경우 `?` 연산자를 사용합니다. 자세한 내용은 [조건부 출력 을](#conditional-output)참조하세요.

```bicep
output <name> <data-type> = <condition> ? <true-value> : <false-value>
```

출력 값의 인스턴스를 두 개 이상 반환하려면 `for` 식을 사용합니다. 자세한 내용은 [동적 출력 수를 참조하세요.](#dynamic-number-of-outputs)

```bicep
output <name> <data-type> = [for <item> in <collection>: {
  ...
}]
```

## <a name="conditional-output"></a>조건부 출력

조건에 따라 값을 반환할 수 있습니다. 일반적으로 리소스를 [조건부로 배포](conditional-resource-deployment.md)한 경우 조건부 출력을 사용합니다. 다음 예에서는 새 항목이 배포되었는지 여부에 따라 공용 IP 주소의 리소스 ID를 조건부로 반환하는 방법을 보여 줍니다.

Bicep에서 조건부 출력을 지정하려면 `?` 연산자를 사용합니다. 다음 예에서는 조건에 따라 엔드포인트 URL 또는 빈 문자열을 반환합니다.

```bicep
param deployStorage bool = true
param storageName string
param location string = resourceGroup().location

resource myStorageAccount 'Microsoft.Storage/storageAccounts@2019-06-01' = if (deployStorage) {
  name: storageName
  location: location
  kind: 'StorageV2'
  sku:{
    name:'Standard_LRS'
    tier: 'Standard'
  }
  properties: {
    accessTier: 'Hot'
  }
}

output endpoint string = deployStorage ? myStorageAccount.properties.primaryEndpoints.blob : ''
```

## <a name="dynamic-number-of-outputs"></a>출력의 동적 수

일부 시나리오에서는 템플릿을 만들 때 반환해야 하는 값의 인스턴스 수를 알 수 없습니다. 반복 출력을 사용하여 가변 개수의 값을 반환할 수 있습니다.

Bicep에서 동적 출력에 대한 조건을 정의하는 `for` 표현식을 추가합니다. 다음 예제에서는 배열을 반복합니다.

```bicep
param nsgLocation string = resourceGroup().location
param orgNames array = [
  'Contoso'
  'Fabrikam'
  'Coho'
]

resource nsg 'Microsoft.Network/networkSecurityGroups@2020-06-01' = [for name in orgNames: {
  name: 'nsg-${name}'
  location: nsgLocation
}]

output deployedNSGs array = [for (name, i) in orgNames: {
  orgName: name
  nsgName: nsg[i].name
  resourceId: nsg[i].id
}]
```

루프에 대한 자세한 내용은 [Bicep의 반복 루프를 참조하세요.](loops.md)

## <a name="outputs-from-modules"></a>모듈의 출력

모듈에서 출력 값을 얻으려면 다음 구문을 사용합니다.

```bicep
<module-name>.outputs.<property-name>
```

다음 예제에서는 모듈에서 값을 검색하여 부하 분산 장치에서 IP 주소를 설정하는 방법을 보여 줍니다. 모듈의 이름은 `publicIP`입니다.

```bicep
publicIPAddress: {
  id: publicIP.outputs.resourceID
}
```

## <a name="get-output-values"></a>출력 값 가져오기

배포에 성공하면 배포 결과에 출력 값이 자동으로 반환됩니다.

배포 기록에서 출력 값을 가져오려면 Azure CLI 또는 Azure PowerShell 스크립트를 사용할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="next-steps"></a>다음 단계

* 출력에 사용할 수 있는 속성에 대한 자세한 내용은 [Bicep의 구조 및 구문 이해](./file.md)를 참조하세요.
