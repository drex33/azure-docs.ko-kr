---
author: georgewallace
ms.service: resource-graph
ms.topic: include
ms.date: 10/12/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: b22262682ebba72371d681a3de0f2429776699a3
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060182"
---
### <a name="count-azure-resources"></a>Azure 리소스 개수 계산

이 쿼리는 액세스 권한이 있는 구독에 있는 Azure 리소스의 개수를 반환합니다. 또한 이 쿼리는 선택한 셀에 적절한 Azure Resource Graph 구성 요소가 설치되어 있고 작동 중인지 검증하는 데도 적절합니다.

```kusto
Resources
| summarize count()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize count()"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize count()"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20summarize%20count()" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20summarize%20count()" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20summarize%20count()" target="_blank">portal.azure.cn</a>

---

### <a name="list-impacted-resources-when-transferring-an-azure-subscription"></a>Azure 구독을 전송할 때 영향을 받는 리소스 나열

구독을 다른 Azure AD(Azure Active Directory) 디렉터리에 전송할 때 영향을 받는 Azure 리소스 중 일부를 반환합니다. 구독을 전송하기 전에 존재했던 리소스 중 일부를 다시 만들어야 합니다.

```kusto
Resources
| where type in (
    'microsoft.managedidentity/userassignedidentities',
    'microsoft.keyvault/vaults',
    'microsoft.sql/servers/databases',
    'microsoft.datalakestore/accounts',
    'microsoft.containerservice/managedclusters')
    or identity has 'SystemAssigned'
    or (type =~ 'microsoft.storage/storageaccounts' and properties['isHnsEnabled'] == true)
| summarize count() by type
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type in ( 'microsoft.managedidentity/userassignedidentities', 'microsoft.keyvault/vaults', 'microsoft.sql/servers/databases', 'microsoft.datalakestore/accounts', 'microsoft.containerservice/managedclusters') or identity has 'SystemAssigned' or (type =~ 'microsoft.storage/storageaccounts' and properties['isHnsEnabled'] == true) | summarize count() by type"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type in ( 'microsoft.managedidentity/userassignedidentities', 'microsoft.keyvault/vaults', 'microsoft.sql/servers/databases', 'microsoft.datalakestore/accounts', 'microsoft.containerservice/managedclusters') or identity has 'SystemAssigned' or (type =~ 'microsoft.storage/storageaccounts' and properties['isHnsEnabled'] == true) | summarize count() by type"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20in%20(%0a%09%27microsoft.managedidentity%2fuserassignedidentities%27%2c%0a%09%27microsoft.keyvault%2fvaults%27%2c%0a%09%27microsoft.sql%2fservers%2fdatabases%27%2c%0a%09%27microsoft.datalakestore%2faccounts%27%2c%0a%09%27microsoft.containerservice%2fmanagedclusters%27)%0a%09or%20identity%20has%20%27SystemAssigned%27%0a%09or%20(type%20%3d%7e%20%27microsoft.storage%2fstorageaccounts%27%20and%20properties%5b%27isHnsEnabled%27%5d%20%3d%3d%20true)%0a%7c%20summarize%20count()%20by%20type" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20in%20(%0a%09%27microsoft.managedidentity%2fuserassignedidentities%27%2c%0a%09%27microsoft.keyvault%2fvaults%27%2c%0a%09%27microsoft.sql%2fservers%2fdatabases%27%2c%0a%09%27microsoft.datalakestore%2faccounts%27%2c%0a%09%27microsoft.containerservice%2fmanagedclusters%27)%0a%09or%20identity%20has%20%27SystemAssigned%27%0a%09or%20(type%20%3d%7e%20%27microsoft.storage%2fstorageaccounts%27%20and%20properties%5b%27isHnsEnabled%27%5d%20%3d%3d%20true)%0a%7c%20summarize%20count()%20by%20type" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20in%20(%0a%09%27microsoft.managedidentity%2fuserassignedidentities%27%2c%0a%09%27microsoft.keyvault%2fvaults%27%2c%0a%09%27microsoft.sql%2fservers%2fdatabases%27%2c%0a%09%27microsoft.datalakestore%2faccounts%27%2c%0a%09%27microsoft.containerservice%2fmanagedclusters%27)%0a%09or%20identity%20has%20%27SystemAssigned%27%0a%09or%20(type%20%3d%7e%20%27microsoft.storage%2fstorageaccounts%27%20and%20properties%5b%27isHnsEnabled%27%5d%20%3d%3d%20true)%0a%7c%20summarize%20count()%20by%20type" target="_blank">portal.azure.cn</a>

---

### <a name="list-resources-sorted-by-name"></a>리소스를 이름별로 정렬하여 나열

이 쿼리는 모든 종류의 리소스를 반환하지만 **name**, **type** 및 **location** 속성만 반환합니다. `order by`를 사용하여 속성을 **name** 속성의 오름차순(`asc`)으로 정렬합니다.

```kusto
Resources
| project name, type, location
| order by name asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, type, location | order by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, type, location | order by name asc"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20name%2c%20type%2c%20location%0a%7c%20order%20by%20name%20asc" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20name%2c%20type%2c%20location%0a%7c%20order%20by%20name%20asc" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20name%2c%20type%2c%20location%0a%7c%20order%20by%20name%20asc" target="_blank">portal.azure.cn</a>

---

### <a name="remove-columns-from-results"></a>결과에서 열 제거

다음 쿼리에서는 `summarize`를 사용하여 구독별로 리소스를 계산하고, `join`을 사용하여 _ResourceContainers_ 테이블의 구독 세부 정보와 결합한 다음, `project-away`를 사용하여 일부 열을 제거합니다.

```kusto
Resources
| summarize resourceCount=count() by subscriptionId
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project-away subscriptionId, subscriptionId1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | project-away subscriptionId, subscriptionId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | project-away subscriptionId, subscriptionId1"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20summarize%20resourceCount%3dcount()%20by%20subscriptionId%0a%7c%20join%20(ResourceContainers%20%7c%20where%20type%3d%3d%27microsoft.resources%2fsubscriptions%27%20%7c%20project%20SubName%3dname%2c%20subscriptionId)%20on%20subscriptionId%0a%7c%20project-away%20subscriptionId%2c%20subscriptionId1" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20summarize%20resourceCount%3dcount()%20by%20subscriptionId%0a%7c%20join%20(ResourceContainers%20%7c%20where%20type%3d%3d%27microsoft.resources%2fsubscriptions%27%20%7c%20project%20SubName%3dname%2c%20subscriptionId)%20on%20subscriptionId%0a%7c%20project-away%20subscriptionId%2c%20subscriptionId1" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20summarize%20resourceCount%3dcount()%20by%20subscriptionId%0a%7c%20join%20(ResourceContainers%20%7c%20where%20type%3d%3d%27microsoft.resources%2fsubscriptions%27%20%7c%20project%20SubName%3dname%2c%20subscriptionId)%20on%20subscriptionId%0a%7c%20project-away%20subscriptionId%2c%20subscriptionId1" target="_blank">portal.azure.cn</a>

---

### <a name="show-resource-types-and-api-versions"></a>리소스 유형 및 API 버전 표시

Resource Graph는 업데이트 중에 `GET` 리소스 속성에 대한 리소스 공급자 API의 미리 보기가 아닌 최신 버전을 주로 사용합니다. 경우에 따라 사용되는 API 버전이 재정의되어 결과에 더 최신 속성 또는 널리 사용되는 속성이 제공되었습니다. 다음 쿼리는 각 리소스 유형에서 속성을 수집하는 데 사용되는 API 버전을 자세히 설명합니다.

```kusto
Resources
| distinct type, apiVersion
| where isnotnull(apiVersion)
| order by type asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | distinct type, apiVersion | where isnotnull(apiVersion) | order by type asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | distinct type, apiVersion | where isnotnull(apiVersion) | order by type asc"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20distinct%20type%2c%20apiVersion%0a%7c%20where%20isnotnull(apiVersion)%0a%7c%20order%20by%20type%20asc" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20distinct%20type%2c%20apiVersion%0a%7c%20where%20isnotnull(apiVersion)%0a%7c%20order%20by%20type%20asc" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20distinct%20type%2c%20apiVersion%0a%7c%20where%20isnotnull(apiVersion)%0a%7c%20order%20by%20type%20asc" target="_blank">portal.azure.cn</a>

---

