---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ea7d773d95a1175c4bd34971b0b3c5da5df5b392
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123536383"
---
### <a name="list-container-registry-vulnerability-assessment-results"></a>Container Registry 취약성 평가 결과 나열

컨테이너 이미지에서 발견된 모든 취약성을 반환합니다. 보안 결과를 보려면 컨테이너용 Azure Defender를 사용하도록 설정해야 합니다.

```kusto
SecurityResources
| where type == 'microsoft.security/assessments'
| where properties.displayName contains 'Vulnerabilities in Azure Container Registry images should be remediated'
| summarize by assessmentKey=name //the ID of the assessment
| join kind=inner (
    securityresources
    | where type == 'microsoft.security/assessments/subassessments'
    | extend assessmentKey = extract('.*assessments/(.+?)/.*',1,  id)
) on assessmentKey
| project assessmentKey, subassessmentKey=name, id, parse_json(properties), resourceGroup, subscriptionId, tenantId
| extend description = properties.description,
    displayName = properties.displayName,
    resourceId = properties.resourceDetails.id,
    resourceSource = properties.resourceDetails.source,
    category = properties.category,
    severity = properties.status.severity,
    code = properties.status.code,
    timeGenerated = properties.timeGenerated,
    remediation = properties.remediation,
    impact = properties.impact,
    vulnId = properties.id,
    additionalData = properties.additionalData
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "SecurityResources | where type == 'microsoft.security/assessments' | where properties.displayName contains 'Vulnerabilities in Azure Container Registry images should be remediated' | summarize by assessmentKey=name //the ID of the assessment | join kind=inner ( securityresources | where type == 'microsoft.security/assessments/subassessments' | extend assessmentKey = extract('.*assessments/(.+?)/.*',1, id) ) on assessmentKey | project assessmentKey, subassessmentKey=name, id, parse_json(properties), resourceGroup, subscriptionId, tenantId | extend description = properties.description, displayName = properties.displayName, resourceId = properties.resourceDetails.id, resourceSource = properties.resourceDetails.source, category = properties.category, severity = properties.status.severity, code = properties.status.code, timeGenerated = properties.timeGenerated, remediation = properties.remediation, impact = properties.impact, vulnId = properties.id, additionalData = properties.additionalData"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "SecurityResources | where type == 'microsoft.security/assessments' | where properties.displayName contains 'Vulnerabilities in Azure Container Registry images should be remediated' | summarize by assessmentKey=name //the ID of the assessment | join kind=inner ( securityresources | where type == 'microsoft.security/assessments/subassessments' | extend assessmentKey = extract('.*assessments/(.+?)/.*',1, id) ) on assessmentKey | project assessmentKey, subassessmentKey=name, id, parse_json(properties), resourceGroup, subscriptionId, tenantId | extend description = properties.description, displayName = properties.displayName, resourceId = properties.resourceDetails.id, resourceSource = properties.resourceDetails.source, category = properties.category, severity = properties.status.severity, code = properties.status.code, timeGenerated = properties.timeGenerated, remediation = properties.remediation, impact = properties.impact, vulnId = properties.id, additionalData = properties.additionalData"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/SecurityResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.security%2fassessments%27%0a%7c%20where%20properties.displayName%20contains%20%27Vulnerabilities%20in%20Azure%20Container%20Registry%20images%20should%20be%20remediated%27%0a%7c%20summarize%20by%20assessmentKey%3dname%20%2f%2fthe%20ID%20of%20the%20assessment%0a%7c%20join%20kind%3dinner%20(%0a%09securityresources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.security%2fassessments%2fsubassessments%27%0a%09%7c%20extend%20assessmentKey%20%3d%20extract(%27.*assessments%2f(.%2b%3f)%2f.*%27%2c1%2c%20%20id)%0a)%20on%20assessmentKey%0a%7c%20project%20assessmentKey%2c%20subassessmentKey%3dname%2c%20id%2c%20parse_json(properties)%2c%20resourceGroup%2c%20subscriptionId%2c%20tenantId%0a%7c%20extend%20description%20%3d%20properties.description%2c%0a%09displayName%20%3d%20properties.displayName%2c%0a%09resourceId%20%3d%20properties.resourceDetails.id%2c%0a%09resourceSource%20%3d%20properties.resourceDetails.source%2c%0a%09category%20%3d%20properties.category%2c%0a%09severity%20%3d%20properties.status.severity%2c%0a%09code%20%3d%20properties.status.code%2c%0a%09timeGenerated%20%3d%20properties.timeGenerated%2c%0a%09remediation%20%3d%20properties.remediation%2c%0a%09impact%20%3d%20properties.impact%2c%0a%09vulnId%20%3d%20properties.id%2c%0a%09additionalData%20%3d%20properties.additionalData" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/SecurityResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.security%2fassessments%27%0a%7c%20where%20properties.displayName%20contains%20%27Vulnerabilities%20in%20Azure%20Container%20Registry%20images%20should%20be%20remediated%27%0a%7c%20summarize%20by%20assessmentKey%3dname%20%2f%2fthe%20ID%20of%20the%20assessment%0a%7c%20join%20kind%3dinner%20(%0a%09securityresources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.security%2fassessments%2fsubassessments%27%0a%09%7c%20extend%20assessmentKey%20%3d%20extract(%27.*assessments%2f(.%2b%3f)%2f.*%27%2c1%2c%20%20id)%0a)%20on%20assessmentKey%0a%7c%20project%20assessmentKey%2c%20subassessmentKey%3dname%2c%20id%2c%20parse_json(properties)%2c%20resourceGroup%2c%20subscriptionId%2c%20tenantId%0a%7c%20extend%20description%20%3d%20properties.description%2c%0a%09displayName%20%3d%20properties.displayName%2c%0a%09resourceId%20%3d%20properties.resourceDetails.id%2c%0a%09resourceSource%20%3d%20properties.resourceDetails.source%2c%0a%09category%20%3d%20properties.category%2c%0a%09severity%20%3d%20properties.status.severity%2c%0a%09code%20%3d%20properties.status.code%2c%0a%09timeGenerated%20%3d%20properties.timeGenerated%2c%0a%09remediation%20%3d%20properties.remediation%2c%0a%09impact%20%3d%20properties.impact%2c%0a%09vulnId%20%3d%20properties.id%2c%0a%09additionalData%20%3d%20properties.additionalData" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/SecurityResources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.security%2fassessments%27%0a%7c%20where%20properties.displayName%20contains%20%27Vulnerabilities%20in%20Azure%20Container%20Registry%20images%20should%20be%20remediated%27%0a%7c%20summarize%20by%20assessmentKey%3dname%20%2f%2fthe%20ID%20of%20the%20assessment%0a%7c%20join%20kind%3dinner%20(%0a%09securityresources%0a%09%7c%20where%20type%20%3d%3d%20%27microsoft.security%2fassessments%2fsubassessments%27%0a%09%7c%20extend%20assessmentKey%20%3d%20extract(%27.*assessments%2f(.%2b%3f)%2f.*%27%2c1%2c%20%20id)%0a)%20on%20assessmentKey%0a%7c%20project%20assessmentKey%2c%20subassessmentKey%3dname%2c%20id%2c%20parse_json(properties)%2c%20resourceGroup%2c%20subscriptionId%2c%20tenantId%0a%7c%20extend%20description%20%3d%20properties.description%2c%0a%09displayName%20%3d%20properties.displayName%2c%0a%09resourceId%20%3d%20properties.resourceDetails.id%2c%0a%09resourceSource%20%3d%20properties.resourceDetails.source%2c%0a%09category%20%3d%20properties.category%2c%0a%09severity%20%3d%20properties.status.severity%2c%0a%09code%20%3d%20properties.status.code%2c%0a%09timeGenerated%20%3d%20properties.timeGenerated%2c%0a%09remediation%20%3d%20properties.remediation%2c%0a%09impact%20%3d%20properties.impact%2c%0a%09vulnId%20%3d%20properties.id%2c%0a%09additionalData%20%3d%20properties.additionalData" target="_blank">portal.azure.cn</a>

---

