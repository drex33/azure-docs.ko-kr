---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 48da30922156ab1cffc0d2d1ed4e20c4f9408a24
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123536425"
---
### <a name="list-sql-databases-and-their-elastic-pools"></a>SQL Database 및 탄력적 풀 나열

다음 쿼리에서는 **leftouter** `join`을 사용하여 SQL Database 리소스 및 이와 관련된 탄력적 풀(있는 경우)을 함께 가져옵니다.

```kusto
Resources
| where type =~ 'microsoft.sql/servers/databases'
| project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId))
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.sql/servers/elasticpools'
    | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state)
    on elasticPoolId
| project-away elasticPoolId1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.sql%2fservers%2fdatabases%27%0a%7c%20project%20databaseId%20%3d%20id%2c%20databaseName%20%3d%20name%2c%20elasticPoolId%20%3d%20tolower(tostring(properties.elasticPoolId))%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.sql%2fservers%2felasticpools%27%0a%09%7c%20project%20elasticPoolId%20%3d%20tolower(id)%2c%20elasticPoolName%20%3d%20name%2c%20elasticPoolState%20%3d%20properties.state)%0a%09on%20elasticPoolId%0a%7c%20project-away%20elasticPoolId1" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.sql%2fservers%2fdatabases%27%0a%7c%20project%20databaseId%20%3d%20id%2c%20databaseName%20%3d%20name%2c%20elasticPoolId%20%3d%20tolower(tostring(properties.elasticPoolId))%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.sql%2fservers%2felasticpools%27%0a%09%7c%20project%20elasticPoolId%20%3d%20tolower(id)%2c%20elasticPoolName%20%3d%20name%2c%20elasticPoolState%20%3d%20properties.state)%0a%09on%20elasticPoolId%0a%7c%20project-away%20elasticPoolId1" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.sql%2fservers%2fdatabases%27%0a%7c%20project%20databaseId%20%3d%20id%2c%20databaseName%20%3d%20name%2c%20elasticPoolId%20%3d%20tolower(tostring(properties.elasticPoolId))%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.sql%2fservers%2felasticpools%27%0a%09%7c%20project%20elasticPoolId%20%3d%20tolower(id)%2c%20elasticPoolName%20%3d%20name%2c%20elasticPoolState%20%3d%20properties.state)%0a%09on%20elasticPoolId%0a%7c%20project-away%20elasticPoolId1" target="_blank">portal.azure.cn</a>

---

