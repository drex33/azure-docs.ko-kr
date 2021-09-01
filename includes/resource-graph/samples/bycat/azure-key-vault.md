---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 08/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 454c46a6afe64d2154689c960acb72dc2cdb1ef0
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123309462"
---
### <a name="count-key-vault-resources"></a>키 자격 증명 모음 리소스 개수 계산

이 쿼리는 `summarize` 대신 `count`를 사용하여 반환된 레코드 수를 계산합니다. Key Vault만 개수에 포함됩니다.

```kusto
Resources
| where type =~ 'microsoft.keyvault/vaults'
| count
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.keyvault%2fvaults%27%0a%7c%20count" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.keyvault%2fvaults%27%0a%7c%20count" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.keyvault%2fvaults%27%0a%7c%20count" target="_blank">portal.azure.cn</a>

---

### <a name="key-vaults-with-subscription-name"></a>구독 이름이 있는 키 자격 증명 모음

다음 쿼리는 **kind** 를 _leftouter_ 로 사용하는 `join`의 복잡한 사용을 보여줍니다. 이 쿼리는 조인된 테이블을 구독 리소스로 제한하고 `project`를 사용하여 원래 필드 _subscriptionId_ 및 _SubName_ 으로 이름이 바뀐 _name_ 필드만 포함합니다. 필드 이름 바꾸기는 필드가 _리소스_ 에 이미 있으므로 `join`이 _name1_ 로 추가하는 것을 방지합니다. 원래 테이블은 `where`를 사용하여 필터링되고 다음 `project`에는 두 테이블의 열이 포함됩니다. 쿼리 결과는 유형, 키 자격 증명 모음 이름 및 해당하는 구독 이름을 표시하는 모든 키 자격 증명 모음입니다.

```kusto
Resources
| join kind=leftouter (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| where type == 'microsoft.keyvault/vaults'
| project type, name, SubName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | join kind=leftouter (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | join kind=leftouter (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20join%20kind%3dleftouter%20(ResourceContainers%20%7c%20where%20type%3d%3d%27microsoft.resources%2fsubscriptions%27%20%7c%20project%20SubName%3dname%2c%20subscriptionId)%20on%20subscriptionId%0a%7c%20where%20type%20%3d%3d%20%27microsoft.keyvault%2fvaults%27%0a%7c%20project%20type%2c%20name%2c%20SubName" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20join%20kind%3dleftouter%20(ResourceContainers%20%7c%20where%20type%3d%3d%27microsoft.resources%2fsubscriptions%27%20%7c%20project%20SubName%3dname%2c%20subscriptionId)%20on%20subscriptionId%0a%7c%20where%20type%20%3d%3d%20%27microsoft.keyvault%2fvaults%27%0a%7c%20project%20type%2c%20name%2c%20SubName" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20join%20kind%3dleftouter%20(ResourceContainers%20%7c%20where%20type%3d%3d%27microsoft.resources%2fsubscriptions%27%20%7c%20project%20SubName%3dname%2c%20subscriptionId)%20on%20subscriptionId%0a%7c%20where%20type%20%3d%3d%20%27microsoft.keyvault%2fvaults%27%0a%7c%20project%20type%2c%20name%2c%20SubName" target="_blank">portal.azure.cn</a>

---

