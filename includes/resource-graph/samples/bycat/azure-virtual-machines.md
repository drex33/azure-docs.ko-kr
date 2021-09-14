---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 263f2be5c13a9086a529271ef8bd03464e20975e
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123536426"
---
### <a name="count-of-os-update-installation-done"></a>완료된 OS 업데이트 설치 수

최근 7일 동안 머신에서 완료된 OS 업데이트 설치 실행의 상태 목록을 반환합니다.

```kusto
PatchAssessmentResources
| where type !has 'softwarepatches'
| extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4))
| extend prop = parse_json(properties)
| extend lTime = todatetime(prop.lastModifiedDateTime), OS = tostring(prop.osType), installedPatchCount = tostring(prop.installedPatchCount), failedPatchCount = tostring(prop.failedPatchCount), pendingPatchCount = tostring(prop.pendingPatchCount), excludedPatchCount = tostring(prop.excludedPatchCount), notSelectedPatchCount = tostring(prop.notSelectedPatchCount)
| where lTime > ago(7d)
| project lTime, RunID=name,machineName, rgName, resourceType, OS, installedPatchCount, failedPatchCount, pendingPatchCount, excludedPatchCount, notSelectedPatchCount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "PatchAssessmentResources | where type !has 'softwarepatches' | extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)) | extend prop = parse_json(properties) | extend lTime = todatetime(prop.lastModifiedDateTime), OS = tostring(prop.osType), installedPatchCount = tostring(prop.installedPatchCount), failedPatchCount = tostring(prop.failedPatchCount), pendingPatchCount = tostring(prop.pendingPatchCount), excludedPatchCount = tostring(prop.excludedPatchCount), notSelectedPatchCount = tostring(prop.notSelectedPatchCount) | where lTime > ago(7d) | project lTime, RunID=name,machineName, rgName, resourceType, OS, installedPatchCount, failedPatchCount, pendingPatchCount, excludedPatchCount, notSelectedPatchCount"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "PatchAssessmentResources | where type !has 'softwarepatches' | extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)) | extend prop = parse_json(properties) | extend lTime = todatetime(prop.lastModifiedDateTime), OS = tostring(prop.osType), installedPatchCount = tostring(prop.installedPatchCount), failedPatchCount = tostring(prop.failedPatchCount), pendingPatchCount = tostring(prop.pendingPatchCount), excludedPatchCount = tostring(prop.excludedPatchCount), notSelectedPatchCount = tostring(prop.notSelectedPatchCount) | where lTime > ago(7d) | project lTime, RunID=name,machineName, rgName, resourceType, OS, installedPatchCount, failedPatchCount, pendingPatchCount, excludedPatchCount, notSelectedPatchCount"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20!has%20%27softwarepatches%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20OS%20%3d%20tostring(prop.osType)%2c%20installedPatchCount%20%3d%20tostring(prop.installedPatchCount)%2c%20failedPatchCount%20%3d%20tostring(prop.failedPatchCount)%2c%20pendingPatchCount%20%3d%20tostring(prop.pendingPatchCount)%2c%20excludedPatchCount%20%3d%20tostring(prop.excludedPatchCount)%2c%20notSelectedPatchCount%20%3d%20tostring(prop.notSelectedPatchCount)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%3dname%2cmachineName%2c%20rgName%2c%20resourceType%2c%20OS%2c%20installedPatchCount%2c%20failedPatchCount%2c%20pendingPatchCount%2c%20excludedPatchCount%2c%20notSelectedPatchCount" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20!has%20%27softwarepatches%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20OS%20%3d%20tostring(prop.osType)%2c%20installedPatchCount%20%3d%20tostring(prop.installedPatchCount)%2c%20failedPatchCount%20%3d%20tostring(prop.failedPatchCount)%2c%20pendingPatchCount%20%3d%20tostring(prop.pendingPatchCount)%2c%20excludedPatchCount%20%3d%20tostring(prop.excludedPatchCount)%2c%20notSelectedPatchCount%20%3d%20tostring(prop.notSelectedPatchCount)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%3dname%2cmachineName%2c%20rgName%2c%20resourceType%2c%20OS%2c%20installedPatchCount%2c%20failedPatchCount%2c%20pendingPatchCount%2c%20excludedPatchCount%2c%20notSelectedPatchCount" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20!has%20%27softwarepatches%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20OS%20%3d%20tostring(prop.osType)%2c%20installedPatchCount%20%3d%20tostring(prop.installedPatchCount)%2c%20failedPatchCount%20%3d%20tostring(prop.failedPatchCount)%2c%20pendingPatchCount%20%3d%20tostring(prop.pendingPatchCount)%2c%20excludedPatchCount%20%3d%20tostring(prop.excludedPatchCount)%2c%20notSelectedPatchCount%20%3d%20tostring(prop.notSelectedPatchCount)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%3dname%2cmachineName%2c%20rgName%2c%20resourceType%2c%20OS%2c%20installedPatchCount%2c%20failedPatchCount%2c%20pendingPatchCount%2c%20excludedPatchCount%2c%20notSelectedPatchCount" target="_blank">portal.azure.cn</a>

---

### <a name="count-of-virtual-machines-by-availability-state-and-subscription-id"></a>가용성 상태 및 구독 ID별 가상 머신 수

각 구독에서 해당 가용성 상태별로 집계된 가상 머신(`Microsoft.Compute/virtualMachines` 형식)의 수를 반환합니다.

```kusto
HealthResources
| where type =~ 'microsoft.resourcehealth/availabilitystatuses'
| summarize count() by subscriptionId, AvailabilityState = tostring(properties.availabilityState)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | summarize count() by subscriptionId, AvailabilityState = tostring(properties.availabilityState)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | summarize count() by subscriptionId, AvailabilityState = tostring(properties.availabilityState)"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20summarize%20count()%20by%20subscriptionId%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20summarize%20count()%20by%20subscriptionId%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20summarize%20count()%20by%20subscriptionId%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.cn</a>

---

### <a name="count-of-virtual-machines-by-power-state"></a>전원 상태별 가상 머신 수

전원 상태에 따라 분류된 가상 머신 수(`Microsoft.Compute/virtualMachines` 형식)를 반환합니다. 전원 상태에 대한 자세한 내용은 [전원 상태 개요](../../../../articles/virtual-machines/states-billing.md)를 참조하세요.

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by PowerState = tostring(properties.extended.instanceView.powerState.code)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type == 'microsoft.compute/virtualmachines' | summarize count() by PowerState = tostring(properties.extended.instanceView.powerState.code)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type == 'microsoft.compute/virtualmachines' | summarize count() by PowerState = tostring(properties.extended.instanceView.powerState.code)"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20summarize%20count()%20by%20PowerState%20%3d%20tostring(properties.extended.instanceView.powerState.code)" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20summarize%20count()%20by%20PowerState%20%3d%20tostring(properties.extended.instanceView.powerState.code)" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20summarize%20count()%20by%20PowerState%20%3d%20tostring(properties.extended.instanceView.powerState.code)" target="_blank">portal.azure.cn</a>

---

### <a name="count-virtual-machines-by-os-type"></a>OS 유형별로 가상 머신 개수 계산

이전 쿼리를 토대로 작성되며 `Microsoft.Compute/virtualMachines` 종류의 Azure 리소스에 의해 제한되지만 반환되는 레코드 수가 더 이상 제한되지 않습니다. 대신 `summarize` 및 `count()`를 사용하여 속성(이 예제에서 `properties.storageProfile.osDisk.osType`)별로 값을 그룹화하고 집계하는 방법을 정의했습니다. 전체 개체에서 이 문자열이 나타나는 방법에 대한 예제를 보려면 [리소스 탐색 - 가상 머신 검색](../../../../articles/governance/resource-graph/concepts/explore-resources.md#virtual-machine-discovery)을 참조하세요.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.cn</a>

---

### <a name="count-virtual-machines-by-os-type-with-extend"></a>OS 유형별로 가상 머신 개수 계산(extend 사용)

‘OS 유형별로 가상 머신 개수 계산’ 쿼리를 작성하는 다른 방법은 속성을 `extend`한 후 쿼리 내에서 사용할 임시 이름(예제에서는 **os**)을 지정하는 것입니다. 그런 다음, **os** 는 참조된 예제와 같이 `summarize` 및 `count()`에서 사용됩니다.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20extend%20os%20%3d%20properties.storageProfile.osDisk.osType%0a%7c%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20extend%20os%20%3d%20properties.storageProfile.osDisk.osType%0a%7c%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20extend%20os%20%3d%20properties.storageProfile.osDisk.osType%0a%7c%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.cn</a>

---

### <a name="get-virtual-machine-scale-set-capacity-and-size"></a>가상 머신 확장 집합 용량 및 크기 가져오기

이 쿼리는 가상 머신 확장 집합 리소스를 찾고 가상 머신 크기 및 확장 집합의 용량을 포함하는 다양한 세부 정보를 가져옵니다. 이 쿼리는 `toint()` 함수를 사용하여 정렬할 수 있도록 용량을 숫자로 캐스팅합니다. 마지막으로, 열의 이름이 사용자 지정 명명 속성으로 바뀝니다.

```kusto
Resources
| where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%3d%7e%20%27microsoft.compute%2fvirtualmachinescalesets%27%0a%7c%20where%20name%20contains%20%27contoso%27%0a%7c%20project%20subscriptionId%2c%20name%2c%20location%2c%20resourceGroup%2c%20Capacity%20%3d%20toint(sku.capacity)%2c%20Tier%20%3d%20sku.name%0a%7c%20order%20by%20Capacity%20desc" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%3d%7e%20%27microsoft.compute%2fvirtualmachinescalesets%27%0a%7c%20where%20name%20contains%20%27contoso%27%0a%7c%20project%20subscriptionId%2c%20name%2c%20location%2c%20resourceGroup%2c%20Capacity%20%3d%20toint(sku.capacity)%2c%20Tier%20%3d%20sku.name%0a%7c%20order%20by%20Capacity%20desc" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%3d%7e%20%27microsoft.compute%2fvirtualmachinescalesets%27%0a%7c%20where%20name%20contains%20%27contoso%27%0a%7c%20project%20subscriptionId%2c%20name%2c%20location%2c%20resourceGroup%2c%20Capacity%20%3d%20toint(sku.capacity)%2c%20Tier%20%3d%20sku.name%0a%7c%20order%20by%20Capacity%20desc" target="_blank">portal.azure.cn</a>

---

### <a name="list-all-extensions-installed-on-a-virtual-machine"></a>가상 머신에 설치된 모든 확장 나열

먼저, 이 쿼리는 가상 머신 리소스 유형의 `extend`를 사용하여 ID를 대문자(`toupper()`)로 가져오고, 운영 체제 이름 및 유형을 가져오고, 가상 머신 크기를 가져옵니다. 리소스 ID를 대문자로 가져오는 것은 다른 속성에 조인하기 위해 준비하는 데 좋은 방법입니다. 그런 다음, 가상 머신 확장을 가져오기 위해 쿼리에서 확장 ID의 대문자 `substring`을 일치시켜 _leftouter_ 인 **kind** 와 함께 `join`을 사용합니다. "/extensions/\<ExtensionName\>" 앞의 ID 부분은 가상 머신 ID와 동일한 형식이므로 `join`에 대해 이 속성을 사용합니다. 그런 다음, `summarize`는 가상 머신 확장 이름에 `make_list`와 함께 사용하여 _ID_, _OSName_, _OSType_ 및 _VMSize_ 가 동일한 각 확장의 이름을 단일 배열 속성으로 결합합니다. 마지막으로 **asc** 를 사용하여 소문자 _OSName_ 을 기준으로 `order by`를 수행합니다. 기본적으로 `order by`는 내림차순입니다.

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| extend
    JoinID = toupper(id),
    OSName = tostring(properties.osProfile.computerName),
    OSType = tostring(properties.storageProfile.osDisk.osType),
    VMSize = tostring(properties.hardwareProfile.vmSize)
| join kind=leftouter(
    Resources
    | where type == 'microsoft.compute/virtualmachines/extensions'
    | extend 
        VMId = toupper(substring(id, 0, indexof(id, '/extensions'))),
        ExtensionName = name
) on $left.JoinID == $right.VMId
| summarize Extensions = make_list(ExtensionName) by id, OSName, OSType, VMSize
| order by tolower(OSName) asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type == 'microsoft.compute/virtualmachines' | extend JoinID = toupper(id), OSName = tostring(properties.osProfile.computerName), OSType = tostring(properties.storageProfile.osDisk.osType), VMSize = tostring(properties.hardwareProfile.vmSize) | join kind=leftouter( Resources | where type == 'microsoft.compute/virtualmachines/extensions' | extend  VMId = toupper(substring(id, 0, indexof(id, '/extensions'))),  ExtensionName = name ) on $left.JoinID == $right.VMId | summarize Extensions = make_list(ExtensionName) by id, OSName, OSType, VMSize | order by tolower(OSName) asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type == 'microsoft.compute/virtualmachines' | extend JoinID = toupper(id), OSName = tostring(properties.osProfile.computerName), OSType = tostring(properties.storageProfile.osDisk.osType), VMSize = tostring(properties.hardwareProfile.vmSize) | join kind=leftouter( Resources | where type == 'microsoft.compute/virtualmachines/extensions' | extend  VMId = toupper(substring(id, 0, indexof(id, '/extensions'))),  ExtensionName = name ) on $left.JoinID == $right.VMId | summarize Extensions = make_list(ExtensionName) by id, OSName, OSType, VMSize | order by tolower(OSName) asc"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20extend%0a%09JoinID%20%3d%20toupper(id)%2c%0a%09OSName%20%3d%20tostring(properties.osProfile.computerName)%2c%0a%09OSType%20%3d%20tostring(properties.storageProfile.osDisk.osType)%2c%0a%09VMSize%20%3d%20tostring(properties.hardwareProfile.vmSize)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%2fextensions%27%0a%09%7c%20extend%20%0a%09%09VMId%20%3d%20toupper(substring(id%2c%200%2c%20indexof(id%2c%20%27%2fextensions%27)))%2c%0a%09%09ExtensionName%20%3d%20name%0a)%20on%20%24left.JoinID%20%3d%3d%20%24right.VMId%0a%7c%20summarize%20Extensions%20%3d%20make_list(ExtensionName)%20by%20id%2c%20OSName%2c%20OSType%2c%20VMSize%0a%7c%20order%20by%20tolower(OSName)%20asc" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20extend%0a%09JoinID%20%3d%20toupper(id)%2c%0a%09OSName%20%3d%20tostring(properties.osProfile.computerName)%2c%0a%09OSType%20%3d%20tostring(properties.storageProfile.osDisk.osType)%2c%0a%09VMSize%20%3d%20tostring(properties.hardwareProfile.vmSize)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%2fextensions%27%0a%09%7c%20extend%20%0a%09%09VMId%20%3d%20toupper(substring(id%2c%200%2c%20indexof(id%2c%20%27%2fextensions%27)))%2c%0a%09%09ExtensionName%20%3d%20name%0a)%20on%20%24left.JoinID%20%3d%3d%20%24right.VMId%0a%7c%20summarize%20Extensions%20%3d%20make_list(ExtensionName)%20by%20id%2c%20OSName%2c%20OSType%2c%20VMSize%0a%7c%20order%20by%20tolower(OSName)%20asc" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20extend%0a%09JoinID%20%3d%20toupper(id)%2c%0a%09OSName%20%3d%20tostring(properties.osProfile.computerName)%2c%0a%09OSType%20%3d%20tostring(properties.storageProfile.osDisk.osType)%2c%0a%09VMSize%20%3d%20tostring(properties.hardwareProfile.vmSize)%0a%7c%20join%20kind%3dleftouter(%0a%09Resources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%2fextensions%27%0a%09%7c%20extend%20%0a%09%09VMId%20%3d%20toupper(substring(id%2c%200%2c%20indexof(id%2c%20%27%2fextensions%27)))%2c%0a%09%09ExtensionName%20%3d%20name%0a)%20on%20%24left.JoinID%20%3d%3d%20%24right.VMId%0a%7c%20summarize%20Extensions%20%3d%20make_list(ExtensionName)%20by%20id%2c%20OSName%2c%20OSType%2c%20VMSize%0a%7c%20order%20by%20tolower(OSName)%20asc" target="_blank">portal.azure.cn</a>

---

### <a name="list-available-os-updates-for-all-your-machines-grouped-by-update-category"></a>모든 머신에 사용 가능한 OS 업데이트를 업데이트 범주별로 나열

컴퓨터에서 보류 중인 OS 목록을 반환합니다.

```kusto
PatchAssessmentResources
| where type !has 'softwarepatches'
| extend prop = parse_json(properties)
| extend lastTime = properties.lastModifiedDateTime
| extend updateRollupCount = prop.availablePatchCountByClassification.updateRollup, featurePackCount = prop.availablePatchCountByClassification.featurePack, servicePackCount = prop.availablePatchCountByClassification.servicePack, definitionCount = prop.availablePatchCountByClassification.definition, securityCount = prop.availablePatchCountByClassification.security, criticalCount = prop.availablePatchCountByClassification.critical, updatesCount = prop.availablePatchCountByClassification.updates, toolsCount = prop.availablePatchCountByClassification.tools, otherCount = prop.availablePatchCountByClassification.other, OS = prop.osType
| project lastTime, id, OS, updateRollupCount, featurePackCount, servicePackCount, definitionCount, securityCount, criticalCount, updatesCount, toolsCount, otherCount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "PatchAssessmentResources | where type !has 'softwarepatches' | extend prop = parse_json(properties) | extend lastTime = properties.lastModifiedDateTime | extend updateRollupCount = prop.availablePatchCountByClassification.updateRollup, featurePackCount = prop.availablePatchCountByClassification.featurePack, servicePackCount = prop.availablePatchCountByClassification.servicePack, definitionCount = prop.availablePatchCountByClassification.definition, securityCount = prop.availablePatchCountByClassification.security, criticalCount = prop.availablePatchCountByClassification.critical, updatesCount = prop.availablePatchCountByClassification.updates, toolsCount = prop.availablePatchCountByClassification.tools, otherCount = prop.availablePatchCountByClassification.other, OS = prop.osType | project lastTime, id, OS, updateRollupCount, featurePackCount, servicePackCount, definitionCount, securityCount, criticalCount, updatesCount, toolsCount, otherCount"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "PatchAssessmentResources | where type !has 'softwarepatches' | extend prop = parse_json(properties) | extend lastTime = properties.lastModifiedDateTime | extend updateRollupCount = prop.availablePatchCountByClassification.updateRollup, featurePackCount = prop.availablePatchCountByClassification.featurePack, servicePackCount = prop.availablePatchCountByClassification.servicePack, definitionCount = prop.availablePatchCountByClassification.definition, securityCount = prop.availablePatchCountByClassification.security, criticalCount = prop.availablePatchCountByClassification.critical, updatesCount = prop.availablePatchCountByClassification.updates, toolsCount = prop.availablePatchCountByClassification.tools, otherCount = prop.availablePatchCountByClassification.other, OS = prop.osType | project lastTime, id, OS, updateRollupCount, featurePackCount, servicePackCount, definitionCount, securityCount, criticalCount, updatesCount, toolsCount, otherCount"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20!has%20%27softwarepatches%27%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lastTime%20%3d%20properties.lastModifiedDateTime%0a%7c%20extend%20updateRollupCount%20%3d%20prop.availablePatchCountByClassification.updateRollup%2c%20featurePackCount%20%3d%20prop.availablePatchCountByClassification.featurePack%2c%20servicePackCount%20%3d%20prop.availablePatchCountByClassification.servicePack%2c%20definitionCount%20%3d%20prop.availablePatchCountByClassification.definition%2c%20securityCount%20%3d%20prop.availablePatchCountByClassification.security%2c%20criticalCount%20%3d%20prop.availablePatchCountByClassification.critical%2c%20updatesCount%20%3d%20prop.availablePatchCountByClassification.updates%2c%20toolsCount%20%3d%20prop.availablePatchCountByClassification.tools%2c%20otherCount%20%3d%20prop.availablePatchCountByClassification.other%2c%20OS%20%3d%20prop.osType%0a%7c%20project%20lastTime%2c%20id%2c%20OS%2c%20updateRollupCount%2c%20featurePackCount%2c%20servicePackCount%2c%20definitionCount%2c%20securityCount%2c%20criticalCount%2c%20updatesCount%2c%20toolsCount%2c%20otherCount" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20!has%20%27softwarepatches%27%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lastTime%20%3d%20properties.lastModifiedDateTime%0a%7c%20extend%20updateRollupCount%20%3d%20prop.availablePatchCountByClassification.updateRollup%2c%20featurePackCount%20%3d%20prop.availablePatchCountByClassification.featurePack%2c%20servicePackCount%20%3d%20prop.availablePatchCountByClassification.servicePack%2c%20definitionCount%20%3d%20prop.availablePatchCountByClassification.definition%2c%20securityCount%20%3d%20prop.availablePatchCountByClassification.security%2c%20criticalCount%20%3d%20prop.availablePatchCountByClassification.critical%2c%20updatesCount%20%3d%20prop.availablePatchCountByClassification.updates%2c%20toolsCount%20%3d%20prop.availablePatchCountByClassification.tools%2c%20otherCount%20%3d%20prop.availablePatchCountByClassification.other%2c%20OS%20%3d%20prop.osType%0a%7c%20project%20lastTime%2c%20id%2c%20OS%2c%20updateRollupCount%2c%20featurePackCount%2c%20servicePackCount%2c%20definitionCount%2c%20securityCount%2c%20criticalCount%2c%20updatesCount%2c%20toolsCount%2c%20otherCount" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20!has%20%27softwarepatches%27%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lastTime%20%3d%20properties.lastModifiedDateTime%0a%7c%20extend%20updateRollupCount%20%3d%20prop.availablePatchCountByClassification.updateRollup%2c%20featurePackCount%20%3d%20prop.availablePatchCountByClassification.featurePack%2c%20servicePackCount%20%3d%20prop.availablePatchCountByClassification.servicePack%2c%20definitionCount%20%3d%20prop.availablePatchCountByClassification.definition%2c%20securityCount%20%3d%20prop.availablePatchCountByClassification.security%2c%20criticalCount%20%3d%20prop.availablePatchCountByClassification.critical%2c%20updatesCount%20%3d%20prop.availablePatchCountByClassification.updates%2c%20toolsCount%20%3d%20prop.availablePatchCountByClassification.tools%2c%20otherCount%20%3d%20prop.availablePatchCountByClassification.other%2c%20OS%20%3d%20prop.osType%0a%7c%20project%20lastTime%2c%20id%2c%20OS%2c%20updateRollupCount%2c%20featurePackCount%2c%20servicePackCount%2c%20definitionCount%2c%20securityCount%2c%20criticalCount%2c%20updatesCount%2c%20toolsCount%2c%20otherCount" target="_blank">portal.azure.cn</a>

---

### <a name="list-of-linux-os-update-installation-done"></a>완료된 Linux OS 업데이트 설치 목록

최근 7일 동안 머신에서 완료된 Linux Server - OS 업데이트 설치 실행의 상태 목록을 반환합니다.

```kusto
PatchAssessmentResources
| where type has 'softwarepatches' and properties has 'version'
| extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)), tostring(RunID = split(id, '/', 10))
| extend prop = parse_json(properties)
| extend lTime = todatetime(prop.lastModifiedDateTime), patchName = tostring(prop.patchName), version = tostring(prop.version), installationState = tostring(prop.installationState), classifications = tostring(prop.classifications)
| where lTime > ago(7d)
| project lTime, RunID, machineName, rgName, resourceType, patchName, version, classifications, installationState
| sort by RunID
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "PatchAssessmentResources | where type has 'softwarepatches' and properties has 'version' | extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)), tostring(RunID = split(id, '/', 10)) | extend prop = parse_json(properties) | extend lTime = todatetime(prop.lastModifiedDateTime), patchName = tostring(prop.patchName), version = tostring(prop.version), installationState = tostring(prop.installationState), classifications = tostring(prop.classifications) | where lTime > ago(7d) | project lTime, RunID, machineName, rgName, resourceType, patchName, version, classifications, installationState | sort by RunID"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "PatchAssessmentResources | where type has 'softwarepatches' and properties has 'version' | extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)), tostring(RunID = split(id, '/', 10)) | extend prop = parse_json(properties) | extend lTime = todatetime(prop.lastModifiedDateTime), patchName = tostring(prop.patchName), version = tostring(prop.version), installationState = tostring(prop.installationState), classifications = tostring(prop.classifications) | where lTime > ago(7d) | project lTime, RunID, machineName, rgName, resourceType, patchName, version, classifications, installationState | sort by RunID"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20has%20%27softwarepatches%27%20and%20properties%20has%20%27version%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%2c%20tostring(RunID%20%3d%20split(id%2c%20%27%2f%27%2c%2010))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20patchName%20%3d%20tostring(prop.patchName)%2c%20version%20%3d%20tostring(prop.version)%2c%20installationState%20%3d%20tostring(prop.installationState)%2c%20classifications%20%3d%20tostring(prop.classifications)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%2c%20machineName%2c%20rgName%2c%20resourceType%2c%20patchName%2c%20version%2c%20classifications%2c%20installationState%0a%7c%20sort%20by%20RunID" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20has%20%27softwarepatches%27%20and%20properties%20has%20%27version%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%2c%20tostring(RunID%20%3d%20split(id%2c%20%27%2f%27%2c%2010))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20patchName%20%3d%20tostring(prop.patchName)%2c%20version%20%3d%20tostring(prop.version)%2c%20installationState%20%3d%20tostring(prop.installationState)%2c%20classifications%20%3d%20tostring(prop.classifications)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%2c%20machineName%2c%20rgName%2c%20resourceType%2c%20patchName%2c%20version%2c%20classifications%2c%20installationState%0a%7c%20sort%20by%20RunID" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20has%20%27softwarepatches%27%20and%20properties%20has%20%27version%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%2c%20tostring(RunID%20%3d%20split(id%2c%20%27%2f%27%2c%2010))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20patchName%20%3d%20tostring(prop.patchName)%2c%20version%20%3d%20tostring(prop.version)%2c%20installationState%20%3d%20tostring(prop.installationState)%2c%20classifications%20%3d%20tostring(prop.classifications)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%2c%20machineName%2c%20rgName%2c%20resourceType%2c%20patchName%2c%20version%2c%20classifications%2c%20installationState%0a%7c%20sort%20by%20RunID" target="_blank">portal.azure.cn</a>

---

### <a name="list-of-virtual-machines-and-associated-availability-states-by-resource-ids"></a>리소스 ID별 가상 머신 및 관련 가용성 상태 목록

가용성 상태별로 집계된 가상 머신(`Microsoft.Compute/virtualMachines` 형식)의 최신 목록을 반환합니다. 이 쿼리는 간편한 디버깅 및 완화를 위해 `properties.targetResourceId`를 기준으로 연결된 리소스 ID도 제공합니다. 가용성 상태는 사용 가능, 사용 불가, 저하됨, 알 수 없음 등의 4가지 값 중 하나일 수 있습니다. 각 가용성 상태의 의미에 대한 세부 정보는 [Azure Resource Health 개요](../../../../articles/service-health/resource-health-overview.md#health-status)를 참조하세요.

```kusto
HealthResources
| where type =~ 'microsoft.resourcehealth/availabilitystatuses'
| summarize by ResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | summarize by ResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | summarize by ResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20summarize%20by%20ResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20summarize%20by%20ResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20summarize%20by%20ResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.cn</a>

---

### <a name="list-of-virtual-machines-by-availability-state-and-power-state-with-resource-ids-and-resource-groups"></a>리소스 ID 및 리소스 그룹을 포함한 가용성 상태 및 전원 상태별 가상 머신 목록

가상 머신의 일관된 상태를 제공하기 위해 전원 상태와 가용성 상태에 대해 집계된 가상 머신 목록(`Microsoft.Compute/virtualMachines` 형식)을 반환합니다. 또한 이 쿼리는 리소스를 상세히 파악할 수 있도록 각 항목에 연결된 리소스 그룹과 리소스 ID에 대한 상세 정보도 제공합니다.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines'
| project resourceGroup, Id = tolower(id), PowerState = tostring( properties.extended.instanceView.powerState.code)
| join kind=leftouter (
    HealthResources
    | where type =~ 'microsoft.resourcehealth/availabilitystatuses'
    | where tostring(properties.targetResourceType) =~ 'microsoft.compute/virtualmachines'
    | project targetResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState))
    on $left.Id == $right.targetResourceId
| project-away targetResourceId
| where PowerState != 'PowerState/deallocated'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' | project resourceGroup, Id = tolower(id), PowerState = tostring( properties.extended.instanceView.powerState.code) | join kind=leftouter ( HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | where tostring(properties.targetResourceType) =~ 'microsoft.compute/virtualmachines' | project targetResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)) on $left.Id == $right.targetResourceId | project-away targetResourceId | where PowerState != 'PowerState/deallocated'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' | project resourceGroup, Id = tolower(id), PowerState = tostring( properties.extended.instanceView.powerState.code) | join kind=leftouter ( HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | where tostring(properties.targetResourceType) =~ 'microsoft.compute/virtualmachines' | project targetResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)) on $left.Id == $right.targetResourceId | project-away targetResourceId | where PowerState != 'PowerState/deallocated'"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20project%20resourceGroup%2c%20Id%20%3d%20tolower(id)%2c%20PowerState%20%3d%20tostring(%20properties.extended.instanceView.powerState.code)%0a%7c%20join%20kind%3dleftouter%20(%0a%09HealthResources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%09%7c%20where%20tostring(properties.targetResourceType)%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%09%7c%20project%20targetResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState))%0a%09on%20%24left.Id%20%3d%3d%20%24right.targetResourceId%0a%7c%20project-away%20targetResourceId%0a%7c%20where%20PowerState%20!%3d%20%27PowerState%2fdeallocated%27" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20project%20resourceGroup%2c%20Id%20%3d%20tolower(id)%2c%20PowerState%20%3d%20tostring(%20properties.extended.instanceView.powerState.code)%0a%7c%20join%20kind%3dleftouter%20(%0a%09HealthResources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%09%7c%20where%20tostring(properties.targetResourceType)%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%09%7c%20project%20targetResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState))%0a%09on%20%24left.Id%20%3d%3d%20%24right.targetResourceId%0a%7c%20project-away%20targetResourceId%0a%7c%20where%20PowerState%20!%3d%20%27PowerState%2fdeallocated%27" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20project%20resourceGroup%2c%20Id%20%3d%20tolower(id)%2c%20PowerState%20%3d%20tostring(%20properties.extended.instanceView.powerState.code)%0a%7c%20join%20kind%3dleftouter%20(%0a%09HealthResources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%09%7c%20where%20tostring(properties.targetResourceType)%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%09%7c%20project%20targetResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState))%0a%09on%20%24left.Id%20%3d%3d%20%24right.targetResourceId%0a%7c%20project-away%20targetResourceId%0a%7c%20where%20PowerState%20!%3d%20%27PowerState%2fdeallocated%27" target="_blank">portal.azure.cn</a>

---

### <a name="list-of-virtual-machines-that-are-not-available-by-resource-ids"></a>리소스 ID별로 사용할 수 없는 가상 머신 목록

가용성 상태별로 집계된 가상 머신(`Microsoft.Compute/virtualMachines` 형식)의 최신 목록을 반환합니다. 채워진 목록에서는 우려되는 상태인 가상 머신을 사용자가 인지할 수 있도록, 가용성 상태가 “사용 가능”이 아닌 가상 머신만 강조 표시합니다. 모든 가상 머신을 사용할 수 있는 경우 결과가 나타나지 않습니다.

```kusto
HealthResources
| where type =~ 'microsoft.resourcehealth/availabilitystatuses'
| where tostring(properties.availabilityState) != 'Available'
| summarize by ResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | where tostring(properties.availabilityState) != 'Available' | summarize by ResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "HealthResources | where type =~ 'microsoft.resourcehealth/availabilitystatuses' | where tostring(properties.availabilityState) != 'Available' | summarize by ResourceId = tolower(tostring(properties.targetResourceId)), AvailabilityState = tostring(properties.availabilityState)"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20where%20tostring(properties.availabilityState)%20!%3d%20%27Available%27%0a%7c%20summarize%20by%20ResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20where%20tostring(properties.availabilityState)%20!%3d%20%27Available%27%0a%7c%20summarize%20by%20ResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/HealthResources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.resourcehealth%2favailabilitystatuses%27%0a%7c%20where%20tostring(properties.availabilityState)%20!%3d%20%27Available%27%0a%7c%20summarize%20by%20ResourceId%20%3d%20tolower(tostring(properties.targetResourceId))%2c%20AvailabilityState%20%3d%20tostring(properties.availabilityState)" target="_blank">portal.azure.cn</a>

---

### <a name="list-of-windows-server-os-update-installation-done"></a>완료된 Windows Server OS 업데이트 설치 목록

최근 7일 동안 머신에서 완료된 Windows Server - OS 업데이트 설치 실행의 상태 목록을 반환합니다.

```kusto
PatchAssessmentResources
| where type has 'softwarepatches' and properties !has 'version'
| extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)), tostring(RunID = split(id, '/', 10))
| extend prop = parse_json(properties)
| extend lTime = todatetime(prop.lastModifiedDateTime), patchName = tostring(prop.patchName), kbId = tostring(prop.kbId), installationState = tostring(prop.installationState), classifications = tostring(prop.classifications)
| where lTime > ago(7d)
| project lTime, RunID, machineName, rgName, resourceType, patchName, kbId, classifications, installationState
| sort by RunID
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "PatchAssessmentResources | where type has 'softwarepatches' and properties !has 'version' | extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)), tostring(RunID = split(id, '/', 10)) | extend prop = parse_json(properties) | extend lTime = todatetime(prop.lastModifiedDateTime), patchName = tostring(prop.patchName), kbId = tostring(prop.kbId), installationState = tostring(prop.installationState), classifications = tostring(prop.classifications) | where lTime > ago(7d) | project lTime, RunID, machineName, rgName, resourceType, patchName, kbId, classifications, installationState | sort by RunID"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "PatchAssessmentResources | where type has 'softwarepatches' and properties !has 'version' | extend machineName = tostring(split(id, '/', 8)), resourceType = tostring(split(type, '/', 0)), tostring(rgName = split(id, '/', 4)), tostring(RunID = split(id, '/', 10)) | extend prop = parse_json(properties) | extend lTime = todatetime(prop.lastModifiedDateTime), patchName = tostring(prop.patchName), kbId = tostring(prop.kbId), installationState = tostring(prop.installationState), classifications = tostring(prop.classifications) | where lTime > ago(7d) | project lTime, RunID, machineName, rgName, resourceType, patchName, kbId, classifications, installationState | sort by RunID"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20has%20%27softwarepatches%27%20and%20properties%20!has%20%27version%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%2c%20tostring(RunID%20%3d%20split(id%2c%20%27%2f%27%2c%2010))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20patchName%20%3d%20tostring(prop.patchName)%2c%20kbId%20%3d%20tostring(prop.kbId)%2c%20installationState%20%3d%20tostring(prop.installationState)%2c%20classifications%20%3d%20tostring(prop.classifications)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%2c%20machineName%2c%20rgName%2c%20resourceType%2c%20patchName%2c%20kbId%2c%20classifications%2c%20installationState%0a%7c%20sort%20by%20RunID" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20has%20%27softwarepatches%27%20and%20properties%20!has%20%27version%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%2c%20tostring(RunID%20%3d%20split(id%2c%20%27%2f%27%2c%2010))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20patchName%20%3d%20tostring(prop.patchName)%2c%20kbId%20%3d%20tostring(prop.kbId)%2c%20installationState%20%3d%20tostring(prop.installationState)%2c%20classifications%20%3d%20tostring(prop.classifications)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%2c%20machineName%2c%20rgName%2c%20resourceType%2c%20patchName%2c%20kbId%2c%20classifications%2c%20installationState%0a%7c%20sort%20by%20RunID" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/PatchAssessmentResources%0a%7c%20where%20type%20has%20%27softwarepatches%27%20and%20properties%20!has%20%27version%27%0a%7c%20extend%20machineName%20%3d%20tostring(split(id%2c%20%27%2f%27%2c%208))%2c%20resourceType%20%3d%20tostring(split(type%2c%20%27%2f%27%2c%200))%2c%20tostring(rgName%20%3d%20split(id%2c%20%27%2f%27%2c%204))%2c%20tostring(RunID%20%3d%20split(id%2c%20%27%2f%27%2c%2010))%0a%7c%20extend%20prop%20%3d%20parse_json(properties)%0a%7c%20extend%20lTime%20%3d%20todatetime(prop.lastModifiedDateTime)%2c%20patchName%20%3d%20tostring(prop.patchName)%2c%20kbId%20%3d%20tostring(prop.kbId)%2c%20installationState%20%3d%20tostring(prop.installationState)%2c%20classifications%20%3d%20tostring(prop.classifications)%0a%7c%20where%20lTime%20%3e%20ago(7d)%0a%7c%20project%20lTime%2c%20RunID%2c%20machineName%2c%20rgName%2c%20resourceType%2c%20patchName%2c%20kbId%2c%20classifications%2c%20installationState%0a%7c%20sort%20by%20RunID" target="_blank">portal.azure.cn</a>

---

### <a name="list-virtual-machines-with-their-network-interface-and-public-ip"></a>네트워크 인터페이스 및 공용 IP를 사용하여 가상 머신 나열

이 쿼리는 두 개의 **leftouter** `join` 명령을 사용하여 Resource Manager 배포 모델을 통해 만든 가상 머신, 관련 네트워크 인터페이스 및 해당 네트워크 인터페이스와 관련된 공용 IP 주소를 함께 가져옵니다.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines'
| extend nics=array_length(properties.networkProfile.networkInterfaces)
| mv-expand nic=properties.networkProfile.networkInterfaces
| where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic)
| project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id)
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/networkinterfaces'
    | extend ipConfigsCount=array_length(properties.ipConfigurations)
    | mv-expand ipconfig=properties.ipConfigurations
    | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true'
    | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id))
    on nicId
| project-away nicId1
| summarize by vmId, vmName, vmSize, nicId, publicIpId
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/publicipaddresses'
    | project publicIpId = id, publicIpAddress = properties.ipAddress)
on publicIpId
| project-away publicIpId1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mv-expand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mv-expand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mv-expand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mv-expand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20extend%20nics%3darray_length(properties.networkProfile.networkInterfaces)%0a%7c%20mv-expand%20nic%3dproperties.networkProfile.networkInterfaces%0a%7c%20where%20nics%20%3d%3d%201%20or%20nic.properties.primary%20%3d%7e%20%27true%27%20or%20isempty(nic)%0a%7c%20project%20vmId%20%3d%20id%2c%20vmName%20%3d%20name%2c%20vmSize%3dtostring(properties.hardwareProfile.vmSize)%2c%20nicId%20%3d%20tostring(nic.id)%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fnetworkinterfaces%27%0a%09%7c%20extend%20ipConfigsCount%3darray_length(properties.ipConfigurations)%0a%09%7c%20mv-expand%20ipconfig%3dproperties.ipConfigurations%0a%09%7c%20where%20ipConfigsCount%20%3d%3d%201%20or%20ipconfig.properties.primary%20%3d%7e%20%27true%27%0a%09%7c%20project%20nicId%20%3d%20id%2c%20publicIpId%20%3d%20tostring(ipconfig.properties.publicIPAddress.id))%0a%09on%20nicId%0a%7c%20project-away%20nicId1%0a%7c%20summarize%20by%20vmId%2c%20vmName%2c%20vmSize%2c%20nicId%2c%20publicIpId%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fpublicipaddresses%27%0a%09%7c%20project%20publicIpId%20%3d%20id%2c%20publicIpAddress%20%3d%20properties.ipAddress)%0aon%20publicIpId%0a%7c%20project-away%20publicIpId1" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20extend%20nics%3darray_length(properties.networkProfile.networkInterfaces)%0a%7c%20mv-expand%20nic%3dproperties.networkProfile.networkInterfaces%0a%7c%20where%20nics%20%3d%3d%201%20or%20nic.properties.primary%20%3d%7e%20%27true%27%20or%20isempty(nic)%0a%7c%20project%20vmId%20%3d%20id%2c%20vmName%20%3d%20name%2c%20vmSize%3dtostring(properties.hardwareProfile.vmSize)%2c%20nicId%20%3d%20tostring(nic.id)%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fnetworkinterfaces%27%0a%09%7c%20extend%20ipConfigsCount%3darray_length(properties.ipConfigurations)%0a%09%7c%20mv-expand%20ipconfig%3dproperties.ipConfigurations%0a%09%7c%20where%20ipConfigsCount%20%3d%3d%201%20or%20ipconfig.properties.primary%20%3d%7e%20%27true%27%0a%09%7c%20project%20nicId%20%3d%20id%2c%20publicIpId%20%3d%20tostring(ipconfig.properties.publicIPAddress.id))%0a%09on%20nicId%0a%7c%20project-away%20nicId1%0a%7c%20summarize%20by%20vmId%2c%20vmName%2c%20vmSize%2c%20nicId%2c%20publicIpId%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fpublicipaddresses%27%0a%09%7c%20project%20publicIpId%20%3d%20id%2c%20publicIpAddress%20%3d%20properties.ipAddress)%0aon%20publicIpId%0a%7c%20project-away%20publicIpId1" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20extend%20nics%3darray_length(properties.networkProfile.networkInterfaces)%0a%7c%20mv-expand%20nic%3dproperties.networkProfile.networkInterfaces%0a%7c%20where%20nics%20%3d%3d%201%20or%20nic.properties.primary%20%3d%7e%20%27true%27%20or%20isempty(nic)%0a%7c%20project%20vmId%20%3d%20id%2c%20vmName%20%3d%20name%2c%20vmSize%3dtostring(properties.hardwareProfile.vmSize)%2c%20nicId%20%3d%20tostring(nic.id)%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fnetworkinterfaces%27%0a%09%7c%20extend%20ipConfigsCount%3darray_length(properties.ipConfigurations)%0a%09%7c%20mv-expand%20ipconfig%3dproperties.ipConfigurations%0a%09%7c%20where%20ipConfigsCount%20%3d%3d%201%20or%20ipconfig.properties.primary%20%3d%7e%20%27true%27%0a%09%7c%20project%20nicId%20%3d%20id%2c%20publicIpId%20%3d%20tostring(ipconfig.properties.publicIPAddress.id))%0a%09on%20nicId%0a%7c%20project-away%20nicId1%0a%7c%20summarize%20by%20vmId%2c%20vmName%2c%20vmSize%2c%20nicId%2c%20publicIpId%0a%7c%20join%20kind%3dleftouter%20(%0a%09Resources%0a%09%7c%20where%20type%20%3d%7e%20%27microsoft.network%2fpublicipaddresses%27%0a%09%7c%20project%20publicIpId%20%3d%20id%2c%20publicIpAddress%20%3d%20properties.ipAddress)%0aon%20publicIpId%0a%7c%20project-away%20publicIpId1" target="_blank">portal.azure.cn</a>

---

### <a name="show-all-virtual-machines-ordered-by-name-in-descending-order"></a>이름의 내림차순으로 모든 가상 머신 표시

가상 머신(type이 `Microsoft.Compute/virtualMachines`로 지정됨)만 나열하려면 결과에서 **type** 속성만 매칭하면 됩니다. 이전 쿼리와 마찬가지로 `desc`는 `order by`를 내림차순으로 변경합니다. 형식 일치의 `=~`는 Resource Graph에 대/소문자를 구분하지 않도록 알립니다.

```kusto
Resources
| project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, location, type | where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, location, type | where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20name%2c%20location%2c%20type%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20order%20by%20name%20desc" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20name%2c%20location%2c%20type%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20order%20by%20name%20desc" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20project%20name%2c%20location%2c%20type%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20order%20by%20name%20desc" target="_blank">portal.azure.cn</a>

---

### <a name="show-first-five-virtual-machines-by-name-and-their-os-type"></a>이름 및 해당 OS 유형별로 처음 5개의 가상 머신 표시

이 쿼리는 `top`을 사용하여 이름별로 정렬된 5개 일치 레코드만 검색합니다. Azure 리소스의 형식은 `Microsoft.Compute/virtualMachines`입니다. `project`는 Azure Resource Graph에 포함할 속성을 알려 줍니다.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20project%20name%2c%20properties.storageProfile.osDisk.osType%0a%7c%20top%205%20by%20name%20desc" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20project%20name%2c%20properties.storageProfile.osDisk.osType%0a%7c%20top%205%20by%20name%20desc" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.Compute%2fvirtualMachines%27%0a%7c%20project%20name%2c%20properties.storageProfile.osDisk.osType%0a%7c%20top%205%20by%20name%20desc" target="_blank">portal.azure.cn</a>

---

### <a name="summarize-virtual-machine-by-the-power-states-extended-property"></a>전원 상태 확장 속성을 기준으로 가상 머신 요약

이 쿼리는 가상 머신의 [확장 속성](../../../../articles/governance/resource-graph/concepts/query-language.md#extended-properties)을 사용하여 전원 상태별로 요약합니다.

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by tostring(properties.extended.instanceView.powerState.code)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type == 'microsoft.compute/virtualmachines' | summarize count() by tostring(properties.extended.instanceView.powerState.code)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type == 'microsoft.compute/virtualmachines' | summarize count() by tostring(properties.extended.instanceView.powerState.code)"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20summarize%20count()%20by%20tostring(properties.extended.instanceView.powerState.code)" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20summarize%20count()%20by%20tostring(properties.extended.instanceView.powerState.code)" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.compute%2fvirtualmachines%27%0a%7c%20summarize%20count()%20by%20tostring(properties.extended.instanceView.powerState.code)" target="_blank">portal.azure.cn</a>

---

### <a name="virtual-machines-matched-by-regex"></a>정규식과 일치하는 가상 머신

이 쿼리는 [정규식](/dotnet/standard/base-types/regular-expression-language-quick-reference)(_regex_ 로 알려짐)과 일치하는 가상 머신을 찾습니다. **matches regex \@** 는 일치시킬 정규식을 정의할 수 있으며, `^Contoso(.*)[0-9]+$`입니다. 해당 regex 정의는 다음으로 설명되어 있습니다.

- `^` - 일치는 문자열의 시작 부분에서 시작해야 합니다.
- `Contoso` - 대/소문자 구분 문자열입니다.
- `(.*)` - 하위 식 일치:
  - `.` - 단일 문자를 일치시킵니다(새 줄 제외).
  - `*` - 이전 요소를 0번 이상 일치시킵니다.
- `[0-9]` - 0~9의 숫자에 대한 문자 그룹 일치입니다.
- `+` - 이전 요소를 한 번 이상 일치시킵니다.
- `$` - 이전 요소의 일치는 문자열의 끝에서 발생해야 합니다.

이름으로 일치시킨 후 쿼리는 이름 오름차순으로 이름 및 순서를 프로젝션합니다.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%20and%20name%20matches%20regex%20%40%27%5eContoso(.*)%5b0-9%5d%2b%24%27%0a%7c%20project%20name%0a%7c%20order%20by%20name%20asc" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%20and%20name%20matches%20regex%20%40%27%5eContoso(.*)%5b0-9%5d%2b%24%27%0a%7c%20project%20name%0a%7c%20order%20by%20name%20asc" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0a%7c%20where%20type%20%3d%7e%20%27microsoft.compute%2fvirtualmachines%27%20and%20name%20matches%20regex%20%40%27%5eContoso(.*)%5b0-9%5d%2b%24%27%0a%7c%20project%20name%0a%7c%20order%20by%20name%20asc" target="_blank">portal.azure.cn</a>

---

