---
title: Bicep의 출력
description: Bicep에서 출력 값을 정의하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 4ed640cd639b7a1b69cd07ae8ac50a4ddff436fb
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491320"
---
# <a name="outputs-in-bicep"></a>Bicep의 출력

이 문서에서는 Bicep 파일에서 출력 값을 정의하는 방법을 설명합니다. 배포된 리소스에서 값을 반환해야 하는 경우 출력을 사용합니다.

## <a name="define-output-values"></a>출력 값 정의

출력 값을 정의 하는 구문은 다음과 같습니다.

```bicep
output <name> <data-type> = <value>
```

출력은 매개 변수, 변수, 모듈 또는 리소스와 동일한 이름을 가질 수 없습니다. 각 출력 값은 [데이터 형식](data-types.md)중 하나로 확인 되어야 합니다.

다음 예제에서는 배포된 리소스에서 속성을 반환하는 방법을 보여 줍니다. 예제에서 `publicIP` 은 Bicep 파일에 배포 된 공용 IP 주소의 기호화 된 이름입니다. 출력 값은 공용 IP 주소의 정규화된 도메인 이름을 가져옵니다.

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

다음 예제에서는 다양 한 형식의 출력을 반환 하는 방법을 보여 줍니다.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/outputs/output.bicep":::

이름에 하이픈이 포함된 속성을 출력해야 하는 경우 점 표기법 대신 이름 주위에 대괄호를 사용합니다. 예를 들어, `.property-name` 대신 `['property-name']`을 사용합니다.

```bicep
var user = {
  'user-name': 'Test Person'
}

output stringOutput string = user['user-name']
```

## <a name="conditional-output"></a>조건부 출력

반환할 값이 배포의 조건에 따라 달라 지는 경우 연산자를 사용 `?` 합니다.

```bicep
output <name> <data-type> = <condition> ? <true-value> : <false-value>
```

일반적으로 리소스를 [조건부로 배포](conditional-resource-deployment.md)한 경우 조건부 출력을 사용합니다. 다음 예제에서는 새 항목을 배포 했는지 여부에 따라 공용 IP 주소에 대 한 리소스 ID를 조건부로 반환 하는 방법을 보여 줍니다.

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

일부 시나리오에서는 템플릿을 만들 때 반환해야 하는 값의 인스턴스 수를 알 수 없습니다. 식을 사용 하 여 변수 수의 값을 반환할 수 있습니다 `for` .

```bicep
output <name> <data-type> = [for <item> in <collection>: {
  ...
}]
```

다음 예제에서는 배열을 반복합니다.

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

루프에 대 한 자세한 내용은 [Bicep의 반복 루프](loops.md)를 참조 하세요.

## <a name="outputs-from-modules"></a>모듈의 출력

모듈에서 출력 값을 가져오려면 다음 구문을 사용 합니다.

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
