---
author: georgewallace
ms.service: resource-graph
ms.topic: include
ms.date: 10/12/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: 78a85558ea39fb483ee45cbc8e6850573c1791e2
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132052986"
---
### <a name="get-count-and-percentage-of-arc-enabled-servers-by-domain"></a>도메인별 Arc 지원 서버의 수 및 백분율 가져오기

이 쿼리는 [Azure Arc 지원 서버](../../../../articles/azure-arc/servers/overview.md)의 **domainName** 속성을 요약하고 `bin`으로 계산하여 도메인당 Arc 지원 서버의 백분율에 대한 **Pct** 열을 만듭니다.

```kusto
Resources
| where type == 'microsoft.hybridcompute/machines'
| project domain=tostring(properties.domainName)
| summarize Domains=make_list(domain), TotalMachineCount=sum(1)
| mvexpand EachDomain = Domains
| summarize PerDomainMachineCount = count() by tostring(EachDomain), TotalMachineCount
| extend Pct = 100 * bin(todouble(PerDomainMachineCount) / todouble(TotalMachineCount), 0.001)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type == 'microsoft.hybridcompute/machines' | project domain=tostring(properties.domainName) | summarize Domains=make_list(domain), TotalMachineCount=sum(1) | mvexpand EachDomain = Domains | summarize PerDomainMachineCount = count() by tostring(EachDomain), TotalMachineCount | extend Pct = 100 * bin(todouble(PerDomainMachineCount) / todouble(TotalMachineCount), 0.001)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type == 'microsoft.hybridcompute/machines' | project domain=tostring(properties.domainName) | summarize Domains=make_list(domain), TotalMachineCount=sum(1) | mvexpand EachDomain = Domains | summarize PerDomainMachineCount = count() by tostring(EachDomain), TotalMachineCount | extend Pct = 100 * bin(todouble(PerDomainMachineCount) / todouble(TotalMachineCount), 0.001)"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%27%0a%7c%20project%20domain%3dtostring(properties.domainName)%0a%7c%20summarize%20Domains%3dmake_list(domain)%2c%20TotalMachineCount%3dsum(1)%0a%7c%20mvexpand%20EachDomain%20%3d%20Domains%0a%7c%20summarize%20PerDomainMachineCount%20%3d%20count()%20by%20tostring(EachDomain)%2c%20TotalMachineCount%0a%7c%20extend%20Pct%20%3d%20100%20*%20bin(todouble(PerDomainMachineCount)%20%2f%20todouble(TotalMachineCount)%2c%200.001)" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%27%0a%7c%20project%20domain%3dtostring(properties.domainName)%0a%7c%20summarize%20Domains%3dmake_list(domain)%2c%20TotalMachineCount%3dsum(1)%0a%7c%20mvexpand%20EachDomain%20%3d%20Domains%0a%7c%20summarize%20PerDomainMachineCount%20%3d%20count()%20by%20tostring(EachDomain)%2c%20TotalMachineCount%0a%7c%20extend%20Pct%20%3d%20100%20*%20bin(todouble(PerDomainMachineCount)%20%2f%20todouble(TotalMachineCount)%2c%200.001)" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%27%0a%7c%20project%20domain%3dtostring(properties.domainName)%0a%7c%20summarize%20Domains%3dmake_list(domain)%2c%20TotalMachineCount%3dsum(1)%0a%7c%20mvexpand%20EachDomain%20%3d%20Domains%0a%7c%20summarize%20PerDomainMachineCount%20%3d%20count()%20by%20tostring(EachDomain)%2c%20TotalMachineCount%0a%7c%20extend%20Pct%20%3d%20100%20*%20bin(todouble(PerDomainMachineCount)%20%2f%20todouble(TotalMachineCount)%2c%200.001)" target="_blank">portal.azure.cn</a>

---

### <a name="list-all-extensions-installed-on-an-azure-arc-enabled-server"></a>Azure Arc 지원 서버에 설치된 모든 확장 나열

먼저 이 쿼리는 하이브리드 시스템 리소스 종류에서 `project`를 사용하여 대문자(`toupper()`)로 된 ID를 가져오고, 컴퓨터 이름 및 컴퓨터에서 실행 중인 운영 체제를 가져옵니다. 리소스 ID를 대문자로 가져오는 것은 `join`을 다른 속성으로 준비하는 좋은 방법입니다. 그런 다음, 쿼리는 **kind** 가 있는 `join`을 _leftouter_ 로 사용하여 확장 ID의 대문자 `substring`과 일치시켜 확장을 가져옵니다. `/extensions/<ExtensionName>` 앞의 ID 부분은 하이브리드 컴퓨터 ID와 동일한 형식이므로 `join`에 이 속성을 사용합니다. 그런 다음, `summarize`는 가상 머신 확장 이름에 `make_list`와 함께 사용하여 _ID_, _OSName_, 및 _ComputerName_ 이 동일한 각 확장의 이름을 단일 배열 속성으로 결합합니다. 마지막으로 **asc** 를 사용하여 소문자 _OSName_ 을 기준으로 정렬합니다. 기본적으로 `order by`는 내림차순입니다.

```kusto
Resources
| where type == 'microsoft.hybridcompute/machines'
| project
    id,
    JoinID = toupper(id),
    ComputerName = tostring(properties.osProfile.computerName),
    OSName = tostring(properties.osName)
| join kind=leftouter(
    Resources
    | where type == 'microsoft.hybridcompute/machines/extensions'
    | project
        MachineId = toupper(substring(id, 0, indexof(id, '/extensions'))),
        ExtensionName = name
) on $left.JoinID == $right.MachineId
| summarize Extensions = make_list(ExtensionName) by id, ComputerName, OSName
| order by tolower(OSName) asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type == 'microsoft.hybridcompute/machines' | project id, JoinID = toupper(id), ComputerName = tostring(properties.osProfile.computerName), OSName = tostring(properties.osName) | join kind=leftouter( Resources | where type == 'microsoft.hybridcompute/machines/extensions' | project  MachineId = toupper(substring(id, 0, indexof(id, '/extensions'))),  ExtensionName = name ) on \$left.JoinID == \$right.MachineId | summarize Extensions = make_list(ExtensionName) by id, ComputerName, OSName | order by tolower(OSName) asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type == 'microsoft.hybridcompute/machines' | project id, JoinID = toupper(id), ComputerName = tostring(properties.osProfile.computerName), OSName = tostring(properties.osName) | join kind=leftouter( Resources | where type == 'microsoft.hybridcompute/machines/extensions' | project  MachineId = toupper(substring(id, 0, indexof(id, '/extensions'))),  ExtensionName = name ) on $left.JoinID == $right.MachineId | summarize Extensions = make_list(ExtensionName) by id, ComputerName, OSName | order by tolower(OSName) asc"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%27%0a%7c%20project%0a%09id%2c%0a%09JoinID%20%3d%20toupper(id)%2c%0a%09ComputerName%20%3d%20tostring(properties.osProfile.computerName)%2c%0a%09OSName%20%3d%20tostring(properties.osName)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%2fextensions%27%0a%09%7c%20project%0a%09%09MachineId%20%3d%20toupper(substring(id%2c%200%2c%20indexof(id%2c%20%27%2fextensions%27)))%2c%0a%09%09ExtensionName%20%3d%20name%0a)%20on%20%24left.JoinID%20%3d%3d%20%24right.MachineId%0a%7c%20summarize%20Extensions%20%3d%20make_list(ExtensionName)%20by%20id%2c%20ComputerName%2c%20OSName%0a%7c%20order%20by%20tolower(OSName)%20asc" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%27%0a%7c%20project%0a%09id%2c%0a%09JoinID%20%3d%20toupper(id)%2c%0a%09ComputerName%20%3d%20tostring(properties.osProfile.computerName)%2c%0a%09OSName%20%3d%20tostring(properties.osName)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%2fextensions%27%0a%09%7c%20project%0a%09%09MachineId%20%3d%20toupper(substring(id%2c%200%2c%20indexof(id%2c%20%27%2fextensions%27)))%2c%0a%09%09ExtensionName%20%3d%20name%0a)%20on%20%24left.JoinID%20%3d%3d%20%24right.MachineId%0a%7c%20summarize%20Extensions%20%3d%20make_list(ExtensionName)%20by%20id%2c%20ComputerName%2c%20OSName%0a%7c%20order%20by%20tolower(OSName)%20asc" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%27%0a%7c%20project%0a%09id%2c%0a%09JoinID%20%3d%20toupper(id)%2c%0a%09ComputerName%20%3d%20tostring(properties.osProfile.computerName)%2c%0a%09OSName%20%3d%20tostring(properties.osName)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%2fextensions%27%0a%09%7c%20project%0a%09%09MachineId%20%3d%20toupper(substring(id%2c%200%2c%20indexof(id%2c%20%27%2fextensions%27)))%2c%0a%09%09ExtensionName%20%3d%20name%0a)%20on%20%24left.JoinID%20%3d%3d%20%24right.MachineId%0a%7c%20summarize%20Extensions%20%3d%20make_list(ExtensionName)%20by%20id%2c%20ComputerName%2c%20OSName%0a%7c%20order%20by%20tolower(OSName)%20asc" target="_blank">portal.azure.cn</a>

---

### <a name="list-arc-enabled-servers-not-running-latest-released-agent-version"></a>최신 릴리스 에이전트 버전을 실행하지 않는 Arc 지원 서버 나열

이 쿼리는 Connected Machine 에이전트의 오래된 버전을 실행하는 모든 Arc 지원 서버를 반환합니다. 상태가 **만료된** 에이전트는 결과에서 제외됩니다. 이 쿼리에서는 _leftouter_ `join`을 사용하여 오래된 것으로 식별된 Connected Machine 에이전트에 대해 발생하는 Advisor 권장 사항을 모두 제공하고, 일정 시간 동안 Azure와 통신하지 않은 모든 에이전트를 필터링하도록 하이브리드 컴퓨터 머신을 사용합니다.

```kusto
AdvisorResources
| where type == 'microsoft.advisor/recommendations'
| where properties.category == 'HighAvailability'
| where properties.shortDescription.solution == 'Upgrade to the latest version of the Azure Connected Machine agent'
| project
        id,
        JoinId = toupper(properties.resourceMetadata.resourceId),
        machineName = tostring(properties.impactedValue),
        agentVersion = tostring(properties.extendedProperties.installedVersion),
        expectedVersion = tostring(properties.extendedProperties.latestVersion)
| join kind=leftouter(
    Resources
    | where type == 'microsoft.hybridcompute/machines'
    | project
        machineId = toupper(id),
        status = tostring (properties.status)
    ) on $left.JoinId == $right.machineId
| where status != 'Expired'
| summarize by id, machineName, agentVersion, expectedVersion
| order by tolower(machineName) asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "AdvisorResources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'HighAvailability' | where properties.shortDescription.solution == 'Upgrade to the latest version of the Azure Connected Machine agent' | project  id,  JoinId = toupper(properties.resourceMetadata.resourceId),  machineName = tostring(properties.impactedValue),  agentVersion = tostring(properties.extendedProperties.installedVersion),  expectedVersion = tostring(properties.extendedProperties.latestVersion) | join kind=leftouter( Resources | where type == 'microsoft.hybridcompute/machines' | project  machineId = toupper(id),  status = tostring (properties.status) ) on \$left.JoinId == \$right.machineId | where status != 'Expired' | summarize by id, machineName, agentVersion, expectedVersion | order by tolower(machineName) asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "AdvisorResources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'HighAvailability' | where properties.shortDescription.solution == 'Upgrade to the latest version of the Azure Connected Machine agent' | project  id,  JoinId = toupper(properties.resourceMetadata.resourceId),  machineName = tostring(properties.impactedValue),  agentVersion = tostring(properties.extendedProperties.installedVersion),  expectedVersion = tostring(properties.extendedProperties.latestVersion) | join kind=leftouter( Resources | where type == 'microsoft.hybridcompute/machines' | project  machineId = toupper(id),  status = tostring (properties.status) ) on $left.JoinId == $right.machineId | where status != 'Expired' | summarize by id, machineName, agentVersion, expectedVersion | order by tolower(machineName) asc"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AdvisorResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.advisor%2frecommendations%27%0a%7c%20where%20properties.category%20%3d%3d%20%27HighAvailability%27%0a%7c%20where%20properties.shortDescription.solution%20%3d%3d%20%27Upgrade%20to%20the%20latest%20version%20of%20the%20Azure%20Connected%20Machine%20agent%27%0a%7c%20project%0a%09%09id%2c%0a%09%09JoinId%20%3d%20toupper(properties.resourceMetadata.resourceId)%2c%0a%09%09machineName%20%3d%20tostring(properties.impactedValue)%2c%0a%09%09agentVersion%20%3d%20tostring(properties.extendedProperties.installedVersion)%2c%0a%09%09expectedVersion%20%3d%20tostring(properties.extendedProperties.latestVersion)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%27%0a%09%7c%20project%0a%09%09machineId%20%3d%20toupper(id)%2c%0a%09%09status%20%3d%20tostring%20(properties.status)%0a%09)%20on%20%24left.JoinId%20%3d%3d%20%24right.machineId%0a%7c%20where%20status%20!%3d%20%27Expired%27%0a%7c%20summarize%20by%20id%2c%20machineName%2c%20agentVersion%2c%20expectedVersion%0a%7c%20order%20by%20tolower(machineName)%20asc" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AdvisorResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.advisor%2frecommendations%27%0a%7c%20where%20properties.category%20%3d%3d%20%27HighAvailability%27%0a%7c%20where%20properties.shortDescription.solution%20%3d%3d%20%27Upgrade%20to%20the%20latest%20version%20of%20the%20Azure%20Connected%20Machine%20agent%27%0a%7c%20project%0a%09%09id%2c%0a%09%09JoinId%20%3d%20toupper(properties.resourceMetadata.resourceId)%2c%0a%09%09machineName%20%3d%20tostring(properties.impactedValue)%2c%0a%09%09agentVersion%20%3d%20tostring(properties.extendedProperties.installedVersion)%2c%0a%09%09expectedVersion%20%3d%20tostring(properties.extendedProperties.latestVersion)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%27%0a%09%7c%20project%0a%09%09machineId%20%3d%20toupper(id)%2c%0a%09%09status%20%3d%20tostring%20(properties.status)%0a%09)%20on%20%24left.JoinId%20%3d%3d%20%24right.machineId%0a%7c%20where%20status%20!%3d%20%27Expired%27%0a%7c%20summarize%20by%20id%2c%20machineName%2c%20agentVersion%2c%20expectedVersion%0a%7c%20order%20by%20tolower(machineName)%20asc" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AdvisorResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.advisor%2frecommendations%27%0a%7c%20where%20properties.category%20%3d%3d%20%27HighAvailability%27%0a%7c%20where%20properties.shortDescription.solution%20%3d%3d%20%27Upgrade%20to%20the%20latest%20version%20of%20the%20Azure%20Connected%20Machine%20agent%27%0a%7c%20project%0a%09%09id%2c%0a%09%09JoinId%20%3d%20toupper(properties.resourceMetadata.resourceId)%2c%0a%09%09machineName%20%3d%20tostring(properties.impactedValue)%2c%0a%09%09agentVersion%20%3d%20tostring(properties.extendedProperties.installedVersion)%2c%0a%09%09expectedVersion%20%3d%20tostring(properties.extendedProperties.latestVersion)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.hybridcompute%2fmachines%27%0a%09%7c%20project%0a%09%09machineId%20%3d%20toupper(id)%2c%0a%09%09status%20%3d%20tostring%20(properties.status)%0a%09)%20on%20%24left.JoinId%20%3d%3d%20%24right.machineId%0a%7c%20where%20status%20!%3d%20%27Expired%27%0a%7c%20summarize%20by%20id%2c%20machineName%2c%20agentVersion%2c%20expectedVersion%0a%7c%20order%20by%20tolower(machineName)%20asc" target="_blank">portal.azure.cn</a>

---

