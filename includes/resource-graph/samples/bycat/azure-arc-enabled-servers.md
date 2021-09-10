---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 08/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8151f59c4e94ebf53d6b980810eb4299f60ccc9e
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123303406"
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

먼저 이 쿼리는 하이브리드 시스템 리소스 종류에서 `project`를 사용하여 대문자(`toupper()`)로 된 ID를 가져오고, 컴퓨터 이름 및 컴퓨터에서 실행 중인 운영 체제를 가져옵니다. 대문자로 리소스 ID를 가져오는 것은 다른 속성에 대한 `join`을 위해 준비하는 좋은 방법입니다. 그런 다음, `join`을 **종류** 와 함께 _leftouter_ 로 사용하여 확장 ID의 대문자 `substring`을 일치시켜 확장을 가져옵니다. `/extensions/<ExtensionName>` 앞의 ID 부분은 하이브리드 컴퓨터 ID와 동일한 형식이므로 `join`에 이 속성을 사용합니다. 그런 다음, `summarize`는 가상 머신 확장 이름에 `make_list`와 함께 사용하여 _ID_, _OSName_, 및 _ComputerName_ 이 동일한 각 확장의 이름을 단일 배열 속성으로 결합합니다. 마지막으로 **asc** 를 사용하여 소문자로 된 _OSName_ 을 기준으로 정렬합니다. 기본적으로 `order by`는 내림차순입니다.

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
az graph query -q "Resources | where type == 'microsoft.hybridcompute/machines' | project id, JoinID = toupper(id), ComputerName = tostring(properties.osProfile.computerName), OSName = tostring(properties.osName) | join kind=leftouter( Resources | where type == 'microsoft.hybridcompute/machines/extensions' | project  MachineId = toupper(substring(id, 0, indexof(id, '/extensions'))),  ExtensionName = name ) on $left.JoinID == $right.MachineId | summarize Extensions = make_list(ExtensionName) by id, ComputerName, OSName | order by tolower(OSName) asc"
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

