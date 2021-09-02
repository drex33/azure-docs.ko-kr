---
author: DCtheGeek
ms.service: resource-graph
ms.topic: include
ms.date: 08/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ea564f87f3aaad9d4641a4f86b67cc274a0fcd1b
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123308902"
---
### <a name="active-service-health-event-subscription-impact"></a>활성 Service Health 이벤트 구독의 영향

서비스 이슈, 계획된 유지 관리, 상태 자문, 보안 공지를 포함하여 모든 활성 Service Health 이벤트를 이벤트 유형별로 그룹화하고 영향을 받는 구독 수를 포함하여 반환합니다.

```kusto
ServiceHealthResources
| where type =~ 'Microsoft.ResourceHealth/events'
| extend eventType = tostring(properties.EventType), status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime
| where properties.Status == 'Active' and tolong(impactStartTime) > 1
| summarize count(subscriptionId) by name, eventType
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ServiceHealthResources | where type =~ 'Microsoft.ResourceHealth/events' | extend eventType = tostring(properties.EventType), status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime | where properties.Status == 'Active' and tolong(impactStartTime) > 1 | summarize count(subscriptionId) by name, eventType"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ServiceHealthResources | where type =~ 'Microsoft.ResourceHealth/events' | extend eventType = tostring(properties.EventType), status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime | where properties.Status == 'Active' and tolong(impactStartTime) > 1 | summarize count(subscriptionId) by name, eventType"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20tostring(properties.EventType)%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%0a%7c%20summarize%20count(subscriptionId)%20by%20name%2c%20eventType" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20tostring(properties.EventType)%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%0a%7c%20summarize%20count(subscriptionId)%20by%20name%2c%20eventType" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20tostring(properties.EventType)%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%0a%7c%20summarize%20count(subscriptionId)%20by%20name%2c%20eventType" target="_blank">portal.azure.cn</a>

---

### <a name="all-active-health-advisory-events"></a>모든 활성 상태 자문 이벤트

사용자에게 액세스 권한이 있는 모든 구독에서 모든 활성 상태 자문 Service Health 이벤트를 반환합니다.

```kusto
ServiceHealthResources
| where type =~ 'Microsoft.ResourceHealth/events'
| extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime
| where properties.Status == 'Active' and tolong(impactStartTime) > 1 and eventType == 'HealthAdvisory'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ServiceHealthResources | where type =~ 'Microsoft.ResourceHealth/events' | extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime | where properties.Status == 'Active' and tolong(impactStartTime) > 1 and eventType == 'HealthAdvisory'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ServiceHealthResources | where type =~ 'Microsoft.ResourceHealth/events' | extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime | where properties.Status == 'Active' and tolong(impactStartTime) > 1 and eventType == 'HealthAdvisory'"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%20and%20eventType%20%3d%3d%20%27HealthAdvisory%27" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%20and%20eventType%20%3d%3d%20%27HealthAdvisory%27" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%20and%20eventType%20%3d%3d%20%27HealthAdvisory%27" target="_blank">portal.azure.cn</a>

---

### <a name="all-active-planned-maintenance-events"></a>모든 활성 계획된 유지 관리 이벤트

사용자에게 액세스 권한이 있는 모든 구독에서 모든 활성 계획된 유지 관리 Service Health 이벤트를 반환합니다.

```kusto
ServiceHealthResources
| where type =~ 'Microsoft.ResourceHealth/events'
| extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime
| where properties.Status == 'Active' and tolong(impactStartTime) > 1 and eventType == 'PlannedMaintenance'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ServiceHealthResources | where type =~ 'Microsoft.ResourceHealth/events' | extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime | where properties.Status == 'Active' and tolong(impactStartTime) > 1 and eventType == 'PlannedMaintenance'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ServiceHealthResources | where type =~ 'Microsoft.ResourceHealth/events' | extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime | where properties.Status == 'Active' and tolong(impactStartTime) > 1 and eventType == 'PlannedMaintenance'"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%20and%20eventType%20%3d%3d%20%27PlannedMaintenance%27" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%20and%20eventType%20%3d%3d%20%27PlannedMaintenance%27" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%20and%20eventType%20%3d%3d%20%27PlannedMaintenance%27" target="_blank">portal.azure.cn</a>

---

### <a name="all-active-service-health-events"></a>모든 활성 Service Health 이벤트

서비스 이슈, 계획된 유지 관리, 상태 자문, 보안 공지를 포함하여 사용자에게 액세스 권한이 있는 모든 구독에서 모든 활성 Service Health 이벤트를 반환합니다.

```kusto
ServiceHealthResources
| where type =~ 'Microsoft.ResourceHealth/events'
| extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime
| where properties.Status == 'Active' and tolong(impactStartTime) > 1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ServiceHealthResources | where type =~ 'Microsoft.ResourceHealth/events' | extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime | where properties.Status == 'Active' and tolong(impactStartTime) > 1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ServiceHealthResources | where type =~ 'Microsoft.ResourceHealth/events' | extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime | where properties.Status == 'Active' and tolong(impactStartTime) > 1"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201" target="_blank">portal.azure.cn</a>

---

### <a name="all-active-service-issue-events"></a>모든 활성 서비스 이슈 이벤트

사용자에게 액세스 권한이 있는 모든 구독에서 모든 활성 서비스 이슈(중단) Service Health 이벤트를 반환합니다.

```kusto
ServiceHealthResources
| where type =~ 'Microsoft.ResourceHealth/events'
| extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime
| where properties.Status == 'Active' and tolong(impactStartTime) > 1 and eventType == 'ServiceIssue'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ServiceHealthResources | where type =~ 'Microsoft.ResourceHealth/events' | extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime | where properties.Status == 'Active' and tolong(impactStartTime) > 1 and eventType == 'ServiceIssue'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ServiceHealthResources | where type =~ 'Microsoft.ResourceHealth/events' | extend eventType = properties.EventType, status = properties.Status, description = properties.Title, trackingId = properties.TrackingId, summary = properties.Summary, priority = properties.Priority, impactStartTime = properties.ImpactStartTime, impactMitigationTime = properties.ImpactMitigationTime | where properties.Status == 'Active' and tolong(impactStartTime) > 1 and eventType == 'ServiceIssue'"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%20and%20eventType%20%3d%3d%20%27ServiceIssue%27" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%20and%20eventType%20%3d%3d%20%27ServiceIssue%27" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ServiceHealthResources%0a%7c%20where%20type%20%3d%7e%20%27Microsoft.ResourceHealth%2fevents%27%0a%7c%20extend%20eventType%20%3d%20properties.EventType%2c%20status%20%3d%20properties.Status%2c%20description%20%3d%20properties.Title%2c%20trackingId%20%3d%20properties.TrackingId%2c%20summary%20%3d%20properties.Summary%2c%20priority%20%3d%20properties.Priority%2c%20impactStartTime%20%3d%20properties.ImpactStartTime%2c%20impactMitigationTime%20%3d%20properties.ImpactMitigationTime%0a%7c%20where%20properties.Status%20%3d%3d%20%27Active%27%20and%20tolong(impactStartTime)%20%3e%201%20and%20eventType%20%3d%3d%20%27ServiceIssue%27" target="_blank">portal.azure.cn</a>

---

