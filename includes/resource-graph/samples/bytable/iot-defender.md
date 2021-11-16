---
author: georgewallace
ms.service: resource-graph
ms.topic: include
ms.date: 10/12/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: 2feaeb47285a7c0cf548a64fcd0090397dcb4a62
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058109"
---
### <a name="get-all-new-alerts-from-the-past-30-days"></a>지난 30일 동안의 모든 새 경고 가져오기

이 쿼리는 지난 30일 동안 모든 사용자의 새 경고 목록을 제공합니다.

```kusto
iotsecurityresources
| where type == 'microsoft.iotsecurity/locations/devicegroups/alerts'
| where todatetime(properties.startTimeUtc) > ago(30d) and properties.status == 'New'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "iotsecurityresources | where type == 'microsoft.iotsecurity/locations/devicegroups/alerts' | where todatetime(properties.startTimeUtc) > ago(30d) and properties.status == 'New'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "iotsecurityresources | where type == 'microsoft.iotsecurity/locations/devicegroups/alerts' | where todatetime(properties.startTimeUtc) > ago(30d) and properties.status == 'New'"
```

# <a name="portal"></a>[포털](#tab/azure-portal)

:::image type="icon" source="../../../../articles/governance/resource-graph/media/resource-graph-small.png"::: Azure Resource Graph Explorer에서 이 쿼리를 사용해 보세요.

- Azure Portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2falerts%27%0a%7c%20where%20todatetime(properties.startTimeUtc)%20%3e%20ago(30d)%20and%20properties.status%20%3d%3d%20%27New%27" target="_blank">portal.azure.com</a>
- Azure Government Portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2falerts%27%0a%7c%20where%20todatetime(properties.startTimeUtc)%20%3e%20ago(30d)%20and%20properties.status%20%3d%3d%20%27New%27" target="_blank">portal.azure.us</a>
- Azure 중국 21Vianet Portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/iotsecurityresources%0a%7c%20where%20type%20%3d%3d%20%27microsoft.iotsecurity%2flocations%2fdevicegroups%2falerts%27%0a%7c%20where%20todatetime(properties.startTimeUtc)%20%3e%20ago(30d)%20and%20properties.status%20%3d%3d%20%27New%27" target="_blank">portal.azure.cn</a>

---

