---
title: 템플릿을 사용해 Azure Spot Virtual Machines 배포
description: 비용 절약을 위해 템플릿을 사용해 Azure Spot Virtual Machines를 배포하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/25/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 3f54e9b80bc6babe90093e45d6a9b7400fa0a5b5
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698133"
---
# <a name="deploy-azure-spot-virtual-machines-using-a-resource-manager-template"></a>Resource Manager 템플릿을 이용해 Azure Spot Virtual Machines 배포하기

**적용 대상:** :heavy_check_mark: Linux VM

[Azure Spot Virtual Machines](../spot-vms.md)를 사용하면 사용되지 않는 용량을 활용하여 비용을 크게 절감할 수 있습니다. Azure에 용량이 다시 필요한 경우 언제든지 인프라에서 Azure 스폿 가상 머신을 제거합니다. 따라서 Azure Spot Virtual Machines는 일괄 처리 작업, 개발/테스트 환경, 대규모 컴퓨팅 워크로드 등과 같이 중단을 처리할 수 있는 워크로드에 매우 적합합니다.

Azure Spot Virtual Machines의 가격 책정은 지역과 SKU에 따라 다릅니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 및 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)에 대한 VM 가격 책정을 참조하세요.

VM에 대해 시간당 지불할 최고 가격을 설정하는 옵션이 있습니다. Azure 스폿 가상 머신 한 대당 최고 가격을 미국 달러(USD)로 최대 소수점 5자릿수까지 설정할 수 있습니다. 예를 들어 `0.98765` 값은 시간당 $0.98765 USD의 최대 가격이 됩니다. 최대 가격을 `-1`로 설정하는 경우 가격에 따라 VM이 제거되지 않습니다. VM의 가격은 사용 가능한 용량과 할당량을 초과하는 경우 Azure Spot Virtual Machines의 현재 가격과 표준 VM의 가격 중에서 더 작은 가격이 됩니다. 최고 가격을 설정하는 방법에 대한 자세한 내용은 [Azure Spot Virtual Machines - 가격 책정](../spot-vms.md#pricing)을 참조하세요.


## <a name="use-a-template"></a>템플릿 사용

Azure 스폿 가상 머신 템플릿 배포에는 `"apiVersion": "2019-03-01"` 이상을 사용합니다. 템플릿에 `priority`, `evictionPolicy`, `billingProfile` 속성을 추가합니다.

```json
"priority": "Spot",
"evictionPolicy": "Deallocate",
"billingProfile": {
    "maxPrice": -1
}
```

다음은 Azure 스폿 가상 머신의 속성이 추가된 샘플 템플릿입니다. 리소스 이름을 내 이름으로 바꾸고 `<password>`를 해당 VM의 로컬 관리자 계정의 암호로 바꿉니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2019-03-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "vnetId": "/subscriptions/ec9fcd04-e188-48b9-abfc-abcd515f1836/resourceGroups/spotVM/providers/Microsoft.Network/virtualNetworks/spotVM",
        "subnetName": "default",
        "networkInterfaceName": "spotVMNIC",
        "publicIpAddressName": "spotVM-ip",
        "publicIpAddressType": "Dynamic",
        "publicIpAddressSku": "Basic",
        "virtualMachineName": "spotVM",
        "osDiskType": "Premium_LRS",
        "virtualMachineSize": "Standard_D2s_v3",
        "adminUsername": "azureuser",
        "adminPassword": "<password>",
        "diagnosticsStorageAccountName": "diagstoragespot2019",
        "diagnosticsStorageAccountId": "Microsoft.Storage/storageAccounts/diagstoragespot2019",
        "diagnosticsStorageAccountType": "Standard_LRS",
        "diagnosticsStorageAccountKind": "Storage",
        "subnetRef": "[concat(variables('vnetId'), '/subnets/', variables('subnetName'))]"
    },
    "resources": [
        {
            "name": "spotVM",
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2019-03-01",
            "location": "eastus",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            },
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIpAddress": {
                                "id": "[resourceId(resourceGroup().name, 'Microsoft.Network/publicIpAddresses', variables('publicIpAddressName'))]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "name": "[variables('publicIpAddressName')]",
            "type": "Microsoft.Network/publicIpAddresses",
            "apiVersion": "2019-02-01",
            "location": "eastus",
            "properties": {
                "publicIpAllocationMethod": "[variables('publicIpAddressType')]"
            },
            "sku": {
                "name": "[variables('publicIpAddressSku')]"
            }
        },
        {
            "name": "[variables('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2019-03-01",
            "location": "eastus",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', variables('networkInterfaceName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[variables('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[variables('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[variables('virtualMachineName')]",
                    "adminUsername": "[variables('adminUsername')]",
                    "adminPassword": "[variables('adminPassword')]"
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('https://', variables('diagnosticsStorageAccountName'), '.blob.core.windows.net/')]"
                    }
                },
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            }
        },
        {
            "name": "[variables('diagnosticsStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "location": "eastus",
            "properties": {},
            "kind": "[variables('diagnosticsStorageAccountKind')]",
            "sku": {
                "name": "[variables('diagnosticsStorageAccountType')]"
            }
        }
    ],
    "outputs": {
        "adminUsername": {
            "type": "string",
            "value": "[variables('adminUsername')]"
        }
    }
}
```

## <a name="simulate-an-eviction"></a>제거 시뮬레이션

Azure 스폿 가상 머신 [제거를 시뮬레이션](/rest/api/compute/virtualmachines/simulateeviction)하여 애플리케이션이 갑작스러운 제거에 얼마나 잘 대응하는지 테스트할 수 있습니다. 

다음을 내 정보로 바꿉니다. 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

## <a name="next-steps"></a>다음 단계

[Azure PowerShell](../windows/spot-powershell.md) 또는 [Azure CLI](spot-cli.md)를 사용해 Azure 스폿 가상 머신을 만들 수도 있습니다.

Azure 스폿 가상 머신 가격 책정에 대한 자세한 내용은 [Azure 소매 가격 API](/rest/api/cost-management/retail-prices/azure-retail-prices)를 사용하여 현재 가격 책정 정보를 쿼리하세요. `meterName`과 `skuName`에는 둘 다 `Spot`이 포함됩니다.

오류가 발생하는 경우 [오류 코드](../error-codes-spot.md)를 참조하세요.