---
title: VM 및 연결 된 리소스 삭제
description: Vm 및 VM에 연결 된 리소스를 삭제 하는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: ''
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/19/2021
ms.author: cynthn
ms.custom: template-how-to
ms.openlocfilehash: da09c055ea73a44bf60985b1218010deba16174d
ms.sourcegitcommit: 6f30424a4ab8dffc4e690086e898ab52bc4da777
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2021
ms.locfileid: "132904715"
---
# <a name="delete-a-vm-and-attached-resources"></a>VM 및 연결 된 리소스 삭제

기본적으로 VM을 삭제 하면 네트워킹 및 디스크 리소스가 아닌 VM 리소스만 삭제 됩니다. Vm을 만들 때 또는 기존 VM을 업데이트 하 여 VM과 함께 특정 리소스를 삭제 하는 경우이 기본 동작을 변경할 수 있습니다. 


## <a name="set-delete-options-when-creating-a-vm"></a>VM을 만들 때 삭제 옵션 설정

### <a name="cli"></a>[CLI](#tab/cli2)

VM을 삭제할 때 연결 된 리소스에 발생 하는 작업을 지정 하려면 `delete-option` 매개 변수를 사용 합니다. 각 `Delete` 는 VM을 삭제할 때 리소스를 영구적으로 삭제 하는 또는 `Detach` 리소스를 분리 하 고 나중에 다시 사용할 수 있도록 Azure에 남겨 두는 중 하나로 설정할 수 있습니다. 디스크와 같은 리소스는 해당 하는 경우 `Detach` 에도 계속 요금을 부과 합니다.

- `--os-disk-delete-option` -OS 디스크
- `--data-disk-delete-option` -데이터 디스크.
- `--nic-delete-option` NIC.

이 예제에서는 vm을 만들고 VM을 삭제할 때 삭제할 OS 디스크 및 NIC를 설정 합니다.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --public-ip-sku Standard \
    --nic-delete-option delete \
    --os-disk-delete-option delete \
    --admin-username azureuser \
    --generate-ssh-keys
```

### <a name="powershell"></a>[PowerShell](#tab/powershell2)

VM을 삭제할 때 연결 된 리소스에 발생 하는 작업을 지정 하려면 `DeleteOption` 매개 변수를 사용 합니다. 각 `Delete` 는 VM을 삭제할 때 리소스를 영구적으로 삭제 하는 또는 `Detach` 리소스를 분리 하 고 나중에 다시 사용할 수 있도록 Azure에 남겨 두는 중 하나로 설정할 수 있습니다. 디스크와 같은 리소스는 해당 하는 경우 `Detach` 에도 계속 요금을 부과 합니다.

`DeleteOption`매개 변수는 다음과 같습니다.
- `-OSDiskDeleteOption` -OS 디스크
- `-DataDiskDeleteOption` -데이터 디스크.
- `-NetworkInterfaceDeleteOption` NIC.

이 예제에서는 vm을 만들고 VM을 삭제할 때 삭제할 OS 디스크 및 NIC를 설정 합니다.

```azurepowershell
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -OSDiskDeleteOption Delete `
    -NetworkInterfaceDeleteOption Delete `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" 
```


### <a name="rest"></a>[REST (영문)](#tab/rest2)

이 예제에서는 VM이 삭제 될 때 삭제할 데이터 디스크 및 NIC를 설정 하는 방법을 보여 줍니다.

```rest
PUT 
https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/myVM?api-version=xx  
{ 
"storageProfile": { 
    "dataDisks": [ 
        { "diskSizeGB": 1023, 
          "name": "myVMdatadisk", 
          "createOption": "Empty", 
          "lun": 0, 
          "deleteOption": “Delete” 
       }    ] 
},  
"networkProfile": { 
      "networkInterfaces": [ 
        { "id": "/subscriptions/.../Microsoft.Network/networkInterfaces/myNIC", 
          "properties": { 
            "primary": true, 
        "deleteOption": “Delete” 
          }        } 
      ]  
} 
```


Nic와 연결 된 공용 IP에 대해이 속성을 설정 하 여 NIC가 삭제 될 때 공용 IP가 자동으로 삭제 되도록 할 수도 있습니다.

```rest
PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/networkInterfaces/test-nic?api-version=xx 
{ 

  "properties": { 

    "enableAcceleratedNetworking": true, 

    "ipConfigurations": [ 

      { 

        "name": "ipconfig1", 

        "properties": { 

          "publicIPAddress": { 

            "id": "/subscriptions/../publicIPAddresses/test-ip", 

          "properties": { 
            “deleteOption”: “Delete” 
            } 
          }, 

          "subnet": { 

            "id": "/subscriptions/../virtualNetworks/rg1-vnet/subnets/default" 

          } 

        } 

      } 

    ] 

  }, 

  "location": "eastus" 

}
```
---


## <a name="update-the-delete-behavior-on-an-existing-vm"></a>기존 VM에서 삭제 동작 업데이트

Vm을 삭제할 때 Azure REST API를 사용 하 여 VM을 패치 하는 동작을 변경할 수 있습니다. 다음 예제에서는 VM을 삭제할 때 NIC, OS 디스크 및 데이터 디스크를 삭제 하도록 VM을 업데이트 합니다.


```rest
PATCH https://management.azure.com/subscriptions/subID/resourceGroups/resourcegroup/providers/Microsoft.Compute/virtualMachines/testvm?api-version=2021-07-01 


{ 
    "properties": {        
        "hardwareProfile": { 
            "vmSize": "Standard_D2s_v3" 
        }, 
        "storageProfile": { 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2019-Datacenter", 
                "version": "latest", 
                "exactVersion": "17763.3124.2111130129" 
            }, 
            "osDisk": { 
                "osType": "Windows", 
                "name": "OsDisk_1", 
                "createOption": "FromImage", 
                "caching": "ReadWrite", 
                "managedDisk": { 
                    "storageAccountType": "Premium_LRS", 
                    "id": "/subscriptions/subID/resourceGroups/resourcegroup/providers/Microsoft.Compute/disks/OsDisk_1" 
                }, 
                "deleteOption": "Delete", 
                "diskSizeGB": 127 
            }, 
            "dataDisks": [ 
                { 
                    "lun": 0, 
                    "name": "DataDisk_0", 
                    "createOption": "Attach", 
                    "caching": "None", 
                    "writeAcceleratorEnabled": false, 
                    "managedDisk": { 
                        "storageAccountType": "Premium_LRS", 
                        "id": "/subscriptions/subID/resourceGroups/resourcegroup/providers/Microsoft.Compute/disks/DataDisk_0" 
                    }, 
                    "deleteOption": "Delete", 
                    "diskSizeGB": 1024, 
                    "toBeDetached": false 
                }, 
                { 
                    "lun": 1, 
                    "name": "DataDisk_1", 
                    "createOption": "Attach", 
                    "caching": "None", 
                    "writeAcceleratorEnabled": false, 
                    "managedDisk": { 
                        "storageAccountType": "Premium_LRS", 
                        "id": "/subscriptions/subID/resourceGroups/resourcegroup/providers/Microsoft.Compute/disks/DataDisk_1" 
                    }, 
                    "deleteOption": "Delete", 
                    "diskSizeGB": 1024, 
                    "toBeDetached": false 
                } 
            ] 
        }, 
        "osProfile": { 
            "computerName": "testvm", 
            "adminUsername": "azureuser", 
            "windowsConfiguration": { 
                "provisionVMAgent": true, 
                "enableAutomaticUpdates": true, 
                "patchSettings": { 
                    "patchMode": "AutomaticByOS", 
                    "assessmentMode": "ImageDefault", 
                    "enableHotpatching": false 
                } 
            }, 
            "secrets": [], 
            "allowExtensionOperations": true, 
            "requireGuestProvisionSignal": true 
        }, 
        "networkProfile": { 
            "networkInterfaces": [ 
                { 
                    "id": "/subscriptions/subID/resourceGroups/resourcegroup/providers/Microsoft.Network/networkInterfaces/nic336" 
                , 
                   "properties": { 
                   "deleteOption": "Delete" 
} 
} 
            ] 
        } 
} 
} 
```

## <a name="faq"></a>FAQ

### <a name="q-does-this-feature-work-with-shared-disks"></a>Q:이 기능은 공유 디스크에서 작동 하나요?

A: 공유 디스크의 경우 ' deleteOption ' 속성을 ' 삭제 '로 설정할 수 없습니다. 비워 두거나 ' 분리 '로 설정할 수 있습니다.


### <a name="q-which-azure-resources-support-this-feature"></a>Q:이 기능을 지 원하는 Azure 리소스는 무엇 인가요?

A:이 기능은 OS 디스크 및 데이터 디스크, Nic 및 공용 Ip로 사용 되는 모든 관리 디스크 유형에 대해 지원 됩니다.


### <a name="q-can-i-use-this-feature-on-disks-and-nics-that-are-not-associated-with-a-vm"></a>Q: VM과 연결 되지 않은 디스크 및 Nic에서이 기능을 사용할 수 있나요?

A: 아니요,이 기능은 VM에 연결 된 디스크 및 Nic 에서만 사용할 수 있습니다.


### <a name="q--how-does-this-feature-work-with-flexible-virtual-machine-scale-sets"></a>Q:이 기능은 유연한 가상 머신 확장 집합에서 어떻게 작동 하나요?

A: 유연한 가상 머신 확장 집합의 경우 디스크, Nic 및 PublicIPs는 `deleteOption` 기본적으로로 설정 되어 `Delete` 있기 때문에 이러한 리소스는 vm이 삭제 될 때 자동으로 정리 됩니다. 

명시적으로 만들어져 Vm에 연결 된 데이터 디스크의 경우 VM을 삭제 한 후 디스크를 유지 하려면 ' 삭제 ' 대신이 속성을 ' 분리 '로 수정할 수 있습니다.


### <a name="q-do-spot-vms-support-this-feature"></a>Q: Vm이이 기능을 지원 하나요?

A: 예, 주문형 Vm의 경우에만이 기능을 사용 하 여 Vm을 찾을 수 있습니다.


### <a name="q-how-do-i-persist-the-disks-nic-and-public-ips-associated-with-a-vm"></a>Q: VM과 연결 된 디스크, NIC 및 공용 Ip를 어떻게 할까요? 유지 하 시겠습니까? 

A: 기본적으로 vm에 연결 된 디스크, Nic 및 공용 Ip는 VM이 삭제 될 때 유지 됩니다. 이러한 리소스를 자동으로 삭제 하도록 구성 하는 경우 VM을 삭제 한 후 이러한 리소스가 유지 되도록 되돌릴 수 있습니다. 이러한 리소스를 유지 하려면 속성을 `deleteOption` 로 설정 합니다 `Detach` . 그러면 이러한 리소스는 VM이 삭제 될 때 유지 됩니다.


## <a name="next-steps"></a>다음 단계

기본 VM 관리에 대 한 자세한 내용은 [자습서: Azure CLI을 사용 하 여 Linux Vm 만들기 및 관리](linux/tutorial-manage-vm.md)를 참조 하세요.
