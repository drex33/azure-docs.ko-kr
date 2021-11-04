---
title: SKU 사용할 수 없음 에러
description: Azure Resource Manager를 사용하여 리소스를 배포할 때 SKU 사용할 수 없음 오류 문제를 해결하는 방법을 설명합니다.
ms.topic: troubleshooting
ms.date: 04/14/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2ed04cd3839b8146d7e988b571113a737f38801f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102974"
---
# <a name="resolve-errors-for-sku-not-available"></a>SKU 사용할 수 없음 오류 해결

이 문서에서는 **SkuNotAvailable** 오류를 해결하는 방법에 대해 설명합니다. 해당 지역/영역 또는 대체 지역/영역에서 비즈니스 요구를 충족하는 적합한 SKU를 찾을 수 없는 경우 [SKU 요청](/troubleshoot/azure/general/region-access-request-process)을 Azure 지원에 제출하세요.

## <a name="symptom"></a>증상

리소스를 배포할 때(일반적으로 가상 머신) 다음 오류 코드 및 오류 메시지가 표시됩니다.

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>원인

선택한 리소스 SKU(예: VM 크기)를 선택한 위치에 사용할 수 없는 경우 이 오류가 나타납니다.

Azure 스폿 VM 또는 스폿 확장 집합 인스턴스를 배포하는 경우 이 위치에 Azure 스폿에 대한 용량이 없습니다. 자세한 내용은 [스폿 오류 메시지](../../virtual-machines/error-codes-spot.md)를 참조하세요.

## <a name="solution-1---powershell"></a>해결 방법 1 - PowerShell

한 지역/영역에서 사용할 수 있는 SKU를 확인하려면 [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) 명령을 사용합니다. 결과를 위치별로 필터링합니다. 이 명령이 작동하려면 최신 버전의 PowerShell이 있어야 합니다.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

결과에는 위치에 대한 SKU 목록과 해당 SKU에 대한 제한 사항이 포함됩니다. SKU는 `NotAvailableForSubscription`으로 나열될 수 있습니다.

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

위치 및 SKU를 기준으로 필터링하려면 다음을 사용합니다.

```azurepowershell-interactive
$SubId = (Get-AzContext).Subscription.Id

$Region = "centralus" # change region here
$VMSku = "Standard_M" # change VM SKU here

$VMSKUs = Get-AzComputeResourceSku | where {$_.Locations.Contains($Region) -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains($VMSku)}

$OutTable = @()

foreach ($SkuName in $VMSKUs.Name)
        {
            $LocRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Location")){"NotAvavalableInRegion"}else{"Available - No region restrictions applied" }
            $ZoneRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Zone")){"NotAvavalableInZone: "+(((($VMSKUs | where Name -EQ $SkuName).Restrictions.RestrictionInfo.Zones)| Where-Object {$_}) -join ",")}else{"Available - No zone restrictions applied"}
            
            
            $OutTable += New-Object PSObject -Property @{
                                                         "Name" = $SkuName
                                                         "Location" = $Region
                                                         "Applies to SubscriptionID" = $SubId
                                                         "Subscription Restriction" = $LocRestriction
                                                         "Zone Restriction" = $ZoneRestriction
                                                         }
         }

$OutTable | select Name, Location, "Applies to SubscriptionID", "Region Restriction", "Zone Restriction" | Sort-Object -Property Name | FT
```

이 명령은 다음과 같은 결과를 반환합니다.

```output
Name                   Location  Applies to SubscriptionID            Region Restriction                         Zone Restriction                        
----                   --------  -------------------------            ------------------------                   ----------------     
Standard_M128          centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-32ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-64ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128dms_v2    centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx NotAvavalableInRegion                      NotAvavalableInZone: 1,2,3
```

## <a name="solution-2---azure-cli"></a>해결 방법 2 - Azure CLI

지역에서 사용할 수 있는 SKU를 확인하려면 [az vm list-skus](/cli/azure/vm#az_vm_list_skus) 명령을 사용합니다. `--location` 매개 변수를 사용하여 위치별로 출력을 필터링합니다. `--size` 매개 변수를 사용하여 부분 크기 이름별로 검색합니다. `--all` 매개 변수를 사용하여 현재 구독에 사용할 수 없는 크기를 비롯한 모든 정보를 표시합니다.

Azure CLI 버전 2.15.0 이상이 있어야 합니다. 버전을 확인하려면 `az --version`을 사용합니다. 필요에 따라 [설치를 업데이트](/cli/azure/update-azure-cli)합니다.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --all --output table
```

이 명령은 다음과 같은 결과를 반환합니다.

```output
ResourceType     Locations       Name              Zones    Restrictions
---------------  --------------  ----------------  -------  --------------
virtualMachines  southcentralus  Standard_F1       1,2,3    None
virtualMachines  southcentralus  Standard_F2       1,2,3    None
virtualMachines  southcentralus  Standard_F4       1,2,3    None
...
virtualMachines  southcentralus  Standard_F72s_v2  1,2,3    NotAvailableForSubscription, type: Zone, locations: southcentralus, zones: 1,2,3
...
```

## <a name="solution-3---azure-portal"></a>해결 방법 3 - Azure Portal

지역에서 사용할 수 있는 SKU를 확인하려면 [포털](https://portal.azure.com)을 사용합니다. 포털에 로그인하고 인터페이스를 통해 리소스를 추가합니다. 값을 설정하면 해당 리소스에 사용 가능한 SKU가 표시됩니다. 배포를 완료할 필요는 없습니다.

예를 들어, 가상 머신을 만드는 프로세스를 시작합니다. 사용 가능한 다른 크기를 보려면 **크기 변경** 을 선택합니다.

![VM 만들기](./media/error-sku-not-available/create-vm.png)

필터링하고 스크롤하면서 사용 가능한 크기를 선택할 수 있습니다.

![사용 가능한 SKU](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>해결 방법 4 - REST

지역에서 사용할 수 있는 SKU를 확인하려면 [리소스 SKU - 목록](/rest/api/compute/resourceskus/list) 작업을 사용합니다.

다음과 같은 형식으로 사용 가능한 SKU 및 지역을 반환합니다.

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```
