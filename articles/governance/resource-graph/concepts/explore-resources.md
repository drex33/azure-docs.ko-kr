---
title: Azure 리소스 검색
description: Resource Graph 쿼리 언어를 사용하여 리소스를 탐색하고 리소스가 연결되는 방식을 파악하는 방법에 대해 알아봅니다.
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 37650c3da17de5cbe3ae8c37da31d0975ea0cdf0
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567988"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Resource Graph로 Azure 리소스 탐색

Azure Resource Graph에서는 원하는 범위에서 Azure 리소스를 빠르게 탐색/검색하는 기능을 제공합니다. 빠르게 응답을 제공하는 Azure Resource Graph를 사용하면 환경 관련 정보 및 Azure 리소스에 존재하는 속성을 효율적으로 파악할 수 있습니다.

## <a name="explore-virtual-machines"></a>가상 머신 탐색

Azure에서 흔히 사용되는 리소스 중 하나로 가상 머신이 있습니다. 리소스 종류인 가상 머신에는 쿼리 가능한 여러 가지 속성이 있습니다. 각 속성은 원하는 리소스를 정확하게 찾거나 필터링하는 데 사용할 수 있는 옵션을 제공합니다.

### <a name="virtual-machine-discovery"></a>가상 머신 검색

간단한 쿼리를 실행하여 환경에서 가상 머신 하나를 가져와서 반환되는 속성을 살펴보겠습니다.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
(Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1").Data | ConvertTo-Json -Depth 100
```

> [!NOTE]
> Azure PowerShell `Search-AzGraph` cmdlet은 기본적으로 **PSResourceGraphResponse** 를 반환합니다. Azure CLI에서 반환하는 내용과 똑같은 출력을 원한다면 `ConvertTo-Json` cmdlet을 **Data** 속성에서 사용합니다. **깊이** 기본값은 _2_ 입니다. 이 값을 _100_ 으로 설정하면 반환된 모든 수준이 변환됩니다.

JSON 결과는 다음 예제와 비슷한 구조로 되어 있습니다.

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1",
    "plan": {},
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_B2s"
      },
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535",
            "resourceGroup": "MyResourceGroup"
          }
        ]
      },
      "osProfile": {
        "adminUsername": "localAdmin",
        "computerName": "ContosoVM1",
        "secrets": [],
        "windowsConfiguration": {
          "enableAutomaticUpdates": true,
          "provisionVMAgent": true
        }
      },
      "provisioningState": "Succeeded",
      "storageProfile": {
        "dataDisks": [],
        "imageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": 127,
          "managedDisk": {
            "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
            "resourceGroup": "MyResourceGroup",
            "storageAccountType": "Premium_LRS"
          },
          "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
          "osType": "Windows"
        }
      },
      "vmId": "bbb9b451-6dc7-4117-bec5-c971eb1118c6"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {},
    "subscriptionId": "<subscriptionId>",
    "tags": {},
    "type": "microsoft.compute/virtualmachines"
  }
]
```

속성은 가상 머신 리소스 자체에 대한 추가 정보를 제공합니다. 이러한 속성에는 운영 체제, 디스크, 태그, 리소스가 속한 리소스 그룹과 구독이 포함됩니다.

### <a name="virtual-machines-by-location"></a>위치별 가상 머신

앞에서 가상 머신 리소스가 무엇인지에 대해 간단히 알아보았으므로, 이번에는 **location** 속성을 사용하여 위치별로 모든 가상 머신 개수를 계산해 보겠습니다. 쿼리를 업데이트하기 위해 제한을 제거하고 위치 값 개수를 요약하겠습니다.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
(Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location").Data | ConvertTo-Json
```

JSON 결과는 다음 예제와 비슷한 구조로 되어 있습니다.

```json
[
  {
    "count_": 386,
    "location": "eastus"
  },
  {
    "count_": 215,
    "location": "southcentralus"
  },
  {
    "count_": 59,
    "location": "westus"
  }
]
```

이제 각 Azure 지역의 가상 머신 수를 확인할 수 있습니다.

### <a name="virtual-machines-by-sku"></a>SKU별 가상 머신

이제 처음에 살펴보았던 가상 머신 속성으로 돌아가서 SKU 크기가 **Standard_B2s** 인 모든 가상 머신을 찾아 보겠습니다. 반환된 JSON을 살펴보면 SKU 크기가 **properties.hardwareprofile.vmsize** 에 저장되어 있음을 확인할 수 있습니다. 이 크기와 일치하는 모든 VM을 찾은 다음 VM 및 지역 이름만 반환하도록 쿼리를 업데이트합니다.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
(Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup").Data | ConvertTo-Json
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>프리미엄 관리 디스크에 연결된 가상 머신

이러한 **Standard_B2s** 가상 머신에 연결된 프리미엄 관리 디스크의 세부 정보를 확인하려면 해당 관리 디스크의 리소스 ID를 반환하도록 쿼리를 확장합니다.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
(Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id").Data | ConvertTo-Json
```

그러면 디스크 ID 목록이 결과로 반환됩니다.

### <a name="managed-disk-discovery"></a>관리 디스크 검색

이전 쿼리의 첫 번째 레코드를 사용하여 첫 번째 가상 머신에 연결된 관리 디스크에 있는 속성을 살펴보겠습니다. 업데이트된 쿼리는 디스크 ID를 사용하고 유형을 변경합니다.

이전 쿼리의 예제 출력을 예로 살펴보겠습니다.

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
Resources
| where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

쿼리를 실행하기 전에 고려할 점은, **type** 을 이번에는 **Microsoft.Compute/disks** 로 설정해야 한다는 것입니다. 전체 ID 문자열의 일부분으로 **/providers/Microsoft.Compute/disks/** 가 포함되어 있습니다.
이 문자열 조각에서 검색해야 하는 유형을 확인할 수 있습니다. 유형 기준 제한을 제거한 후 ID 필드를 기준으로만 검색하는 방법도 있습니다. ID는 고유하므로 레코드가 하나만 반환되며, 해당 레코드의 **type** 속성이 필요한 세부 정보를 제공합니다.

> [!NOTE]
> ID 필드를 실제 환경의 결과로 바꿔야 이 예제가 작동합니다.

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
(Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'").Data | ConvertTo-Json
```

JSON 결과는 다음 예제와 비슷한 구조로 되어 있습니다.

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "plan": {},
    "properties": {
      "creationData": {
        "createOption": "Empty"
      },
      "diskSizeGB": 127,
      "diskState": "ActiveSAS",
      "provisioningState": "Succeeded",
      "timeCreated": "2018-09-14T12:17:32.2570000Z"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {
      "name": "Premium_LRS",
      "tier": "Premium"
    },
    "subscriptionId": "<subscriptionId>",
    "tags": {
      "environment": "prod"
    },
    "type": "microsoft.compute/disks"
  }
]
```

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>가상 머신을 탐색하여 공용 IP 주소 찾기

이 쿼리 집합은 먼저 가상 머신에 연결된 모든 NIC(네트워크 인터페이스) 리소스를 찾아 저장합니다. 그런 다음, 쿼리는 NIC 목록을 사용하여 공용 IP 주소인 각 IP 주소 리소스를 찾아 해당 값을 저장합니다. 마지막으로, 쿼리는 공용 IP 주소 목록을 제공합니다.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nics.txt' file
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

```azurepowershell-interactive
# Use Resource Graph to get all NICs and store in the $nics variable
$nics = (Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20").Data

# Review the output of the query stored in the variable
$nics.nic
```

다음 쿼리에서 파일(Azure CLI) 또는 변수(Azure PowerShell)를 사용하여 관련 네트워크 인터페이스 리소스 세부 정보를 가져옵니다. 이 세부 정보에 NIC에 연결된 공용 IP 주소가 포함되어 있습니다.

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

```azurepowershell-interactive
# Use Resource Graph  with the $nics variable to get all related public IP addresses and store in $ips variable
$ips = (Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$($nics.nic -join "','")') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp").Data

# Review the output of the query stored in the variable
$ips.publicIp
```

마지막으로 파일(Azure CLI) 또는 변수(Azure PowerShell)에 저장된 공용 IP 주소 리소스 목록을 사용해 관련 개체에서 실제 공용 IP 주소를 가져와서 표시합니다.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

```azurepowershell-interactive
# Use Resource Graph with the $ips variable to get the IP address of the public IP address resources
(Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$($ips.publicIp -join "','")') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip").Data | ConvertTo-Json
```

`join` 연산자를 사용하여 단일 쿼리에서 이러한 단계를 수행하는 방법에 대한 자세한 내용은 [네트워크 인터페이스 및 공용 IP 샘플과 함께 가상 머신 나열](../samples/advanced.md#join-vmpip)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [쿼리 언어](query-language.md)에 대해 자세히 알아보기
- [시작 쿼리](../samples/starter.md)에 사용되는 언어를 확인합니다.
- [고급 쿼리](../samples/advanced.md)의 고급 사용법을 확인합니다.
