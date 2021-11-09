---
author: georgewallace
ms.service: resource-graph
ms.topic: include
ms.date: 10/12/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: 2e32d477a71a820c37f2cfbb848bd50a9ba3e263
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058127"
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

