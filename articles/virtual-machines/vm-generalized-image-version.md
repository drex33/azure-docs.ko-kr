---
title: 갤러리의 일반화된 이미지에서 VM 만들기
description: 갤러리의 일반화된 이미지에서 VM을 만듭니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/31/2021
ms.author: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 7468218fbbb08a2e7157d8fc10d6433d6d784d2e
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452690"
---
# <a name="create-a-vm-from-a-generalized-image-version"></a>일반화된 이미지 버전에서 VM 만들기

Shared Image Gallery에 저장된 [일반화된 이미지 버전](./shared-image-galleries.md#generalized-and-specialized-images)에서 VM을 만듭니다. 전문화된 이미지로 VM을 만들려면 [전문화된 이미지에서 VM 만들기](vm-specialized-image-version.md)를 참조하세요. 


### <a name="portal"></a>[포털](#tab/portal)

이제 새 VM을 한 개 이상 만들 수 있습니다. 이 예제에서는 ‘미국 동부’ 데이터 센터에서 *myResourceGroup* 에 *myVM* 이라는 VM을 만듭니다.

1. 이미지 정의로 이동합니다. 리소스 필터를 사용하여 사용 가능한 모든 이미지 정의를 표시할 수 있습니다.
1. 이미지 정의 페이지의 맨 위에 있는 메뉴에서 **VM 만들기** 를 선택합니다.
1. **리소스 그룹** 에 대해 **새로 만들기** 를 선택하고 *myResourceGroup* 을 이름으로 입력합니다.
1. **가상 머신 이름** 에 *myVM* 을 입력합니다.
1. **지역** 에 대해 *미국 동부* 를 선택합니다.
1. **가용성 옵션** 의 경우에는 ‘인프라 중복이 필요하지 않습니다’ 기본값을 그대로 둡니다.
1. 이미지 정의에 대한 페이지에서 시작한 경우 **이미지** 값이 `latest` 이미지 버전으로 자동으로 채워집니다.
1. **크기** 의 경우 사용 가능한 크기 목록에서 VM 크기를 고른 다음 **선택** 을 선택합니다.
1. **관리자 계정** 에서 *azureuser,* 암호 또는 SSH 키와 같은 사용자 이름을 제공해야 합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](./windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-)을 충족해야 합니다.
1. VM에 대한 원격 액세스를 허용하려면 **공용 인바운드 포트** 에서 **선택한 포트 허용을** 선택한 다음, 드롭다운에서 **SSH(22)** 또는 **RDP(3389)를** 선택합니다. VM에 대한 원격 액세스를 허용하지 않으려면 **퍼블릭 인바운드 포트** 에 대해 **안 함** 을 선택한 상태로 둡니다.
1. 작업을 마치면 페이지 하단의 **검토 + 만들기** 단추를 선택합니다.
1. VM이 유효성 검사를 통과하면 페이지 하단에서 **만들기** 를 선택하여 배포를 시작합니다.

### <a name="cli"></a>[CLI](#tab/cli)


[az sig image-definition 목록](/cli/azure/sig/image-definition#az_sig_image_definition_list)으로 갤러리의 이미지 정의를 나열하여 정의의 이름과 ID를 확인합니다.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

[az vm create](/cli/azure/vm#az_vm_create)를 사용하여 VM을 만듭니다. 최신 버전의 이미지를 사용하려면 `--image`를 이미지 정의의 ID로 설정합니다. 

아래 예제는 SSH로 보안된 Linux VM을 만드는 것입니다. Windows 또는 암호를 사용하여 Linux VM을 보호하려면 을 `--generate-ssh-keys` 제거하여 암호를 입력하라는 메시지를 표시합니다. 암호를 직접 제공하려면 을 `--generate-ssh-keys` 로 `--admin-password` 대체합니다. 이 예제에서 필요에 따라 리소스 이름을 바꿉니다. 

```azurecli-interactive 
imgDef="/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition"
vmResourceGroup=myResourceGroup
location=eastus
vmName=myVM
adminUsername=azureuser


az group create --name $vmResourceGroup --location $location

az vm create\
   --resource-group $vmResourceGroup \
   --name $vmName \
   --image $imgDef \
   --admin-username $adminUsername \
   --generate-ssh-keys
```

`--image` 매개 변수에 대한 이미지 버전 ID를 사용하여 특정 버전을 사용할 수도 있습니다. 예를 들어 이미지 버전 *1.0.0* 을 사용하려면 다음을 입력합니다. `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`.

### <a name="powershell"></a>[PowerShell](#tab/powershell)

일반화된 이미지 버전이 있으면 새 VM을 하나 이상 만들 수 있습니다. [New-AzVM](/powershell/module/az.compute/new-azvm) cmdlet을 사용합니다. 

이 예에서는 이미지 정의 ID를 사용하여 새 VM에서 최신 버전의 이미지를 사용할 수 있도록 합니다. `Set-AzVMSourceImage -Id`에 대한 이미지 버전 ID를 사용하여 특정 버전을 사용할 수도 있습니다. 예를 들어 이미지 버전 *1.0.0* 형식을 사용하려면 `Set-AzVMSourceImage -Id "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`을 입력합니다. 

특정 이미지 버전이 지역에서 삭제되거나 제거되어 사용할 수 없는 경우, 해당 이미지 버전을 사용하면 자동화가 실패할 수 있습니다. 특정 이미지 버전이 필요하지 않은 경우 이미지 정의 ID를 사용하여 새 VM을 만드는 것이 좋습니다.

이 예제에서는 필요에 따라 리소스 이름을 바꿉니다. 

**단순화된 매개 변수 집합**

단순화된 매개 변수 집합을 사용하여 이미지에서 VM을 신속하게 만들 수 있습니다. 단순화된 매개 변수 집합은 VM 이름을 사용하여 vNet, 공용 IP 주소와 같은 필요한 리소스 중 일부를 자동으로 만듭니다. 

```azurepowershell-interactive
# Create some variables for the new VM 
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition

# Create user object
$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup `
   -Name $resourceGroup `
   -Location $location

New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name $vmName `
   -Image $imageDefinition.Id
   -Credential $cred
```



**전체 매개 변수 집합**

전체 매개 변수 집합을 사용하여 특정 리소스로 VM을 만들 수 있습니다.

```azurepowershell-interactive
# Create some variables for the new VM 
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition

# Create user object
$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup `
   -Name $resourceGroup `
   -Location $location

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
  -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 `
   -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name myNic `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageDefinition.Id to use the latest image version.
$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2 | `
   Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```

### <a name="rest"></a>[REST (영문)](#tab/rest)

Virtual Network를 만듭니다.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{vNetName}?api-version=2020-05-01

{
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "10.0.0.0/16"
            ]
        }
    },
    "location": "eastus"
}
```

서브넷을 만듭니다.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{vNetName}/subnets/{subnetName}?api-version=2020-05-01

{
    "properties": {
        "addressPrefix": "10.0.0.0/16"
    },
}
```

공용 IP 주소 만들기.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/publicIPAddresses/{pIPName}?api-version=2020-11-01

{
  "location": "eastus"
}

```

네트워크 보안 그룹을 만듭니다.

```rest
# @name vmNSG
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}?api-version=2020-11-01

{
  "properties": {
    "securityRules": [
      {
        "name": "AllowSSH",
        "properties": {
          "protocol": "Tcp",
          "sourceAddressPrefix": "*",
          "destinationAddressPrefix": "*",
          "access": "Allow",
          "destinationPortRange": "3389",
          "sourcePortRange": "*",
          "priority": 1000,
          "direction": "Inbound"
        }
      }
    ]
  },
  "location": "eastus"
}
```

NIC를 만듭니다.

```rest
# @name vmNIC
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}?api-version=2020-05-01

{
    "properties": {
        "enableAcceleratedNetworking": true,
        "networkSecurityGroup": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
        },
        "ipConfigurations": [
            {
                "name": "ipconfig1",
                "properties": {
                    "subnet": {
                        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{vNetName}/subnets/{subNetName}",
                    },
                    "publicIPAddress": {
                        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/publicIPAddresses/{pipName}"
          }
                }
            }
        ]
    },
    "location": "eastus",
}
```
Linux VM을 만듭니다. `oSProfile`섹션에는 몇 가지 OS 관련 세부 정보가 포함되어 있습니다. Windows 구문은 다음 코드 예제를 참조하세요.

```rest
# @name vm
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2020-06-01

{
    "location": "eastus",
    "properties": {
        "hardwareProfile": {
            "vmSize": "Standard_DS3_v2"
        },
        "storageProfile": {
            "imageReference": {
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}/images/{galleryImageName}/versions/{versionNumber}"
            },
            "osDisk": {
                "caching": "ReadWrite",
                "managedDisk": {
                    "storageAccountType": "Standard_LRS"
                },
                "createOption": "FromImage"
            }
        },
        "osProfile": {
            "adminUsername": "{your-username}",
            "computerName": "myVM",
            "linuxConfiguration": {
                "ssh": {
                    "publicKeys": [
                        {
                            "path": "/home/{your-username}/.ssh/authorized_keys",
                            "keyData": "{sshKey}",
                        }
                    ]
                },
                "disablePasswordAuthentication": true
            }
        },
        "networkProfile": {
            "networkInterfaces": [
                {
                    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
                }
            ]
        }
    },
}

```

Windows VM을 만듭니다.

```rest
# @name vm
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2020-06-01

{
    "location": "eastus",
    "properties": {
        "hardwareProfile": {
            "vmSize": "Standard_DS3_v2"
        },
        "storageProfile": {
            "imageReference": {
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}/images/{galleryImageName}/versions/{versionNumber}"
            },
            "osDisk": {
                "caching": "ReadWrite",
                "managedDisk": {
                    "storageAccountType": "Standard_LRS"
                },
                "createOption": "FromImage"
            }
        },
        "osProfile": {
            "adminUsername": "{your-username}",
            "computerName": "myVM",
            "adminPassword": "{your-password}"
        },
        "networkProfile": {
            "networkInterfaces": [
                {
                    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
                }
            ]
        }
    },
```

---

**다음 단계**

[Azure Image Builder(미리 보기)](./image-builder-overview.md)는 이미지 버전 생성을 자동화하는 데 도움이 되며, [기존 이미지 버전에서 새 이미지를 생성](./linux/image-builder-gallery-update-image-version.md)하고 업데이트하는 데도 사용할 수 있습니다.