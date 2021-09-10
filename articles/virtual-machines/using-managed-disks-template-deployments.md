---
title: Azure Resource Manager 템플릿을 사용하여 디스크 배포
description: Azure VM용 Azure Resource Manager 템플릿에서 관리 디스크와 비관리 디스크를 사용하는 방법을 자세히 설명합니다.
documentationcenter: ''
author: jboeshart
manager: ''
ms.service: virtual-machines
ms.topic: how-to
ms.workload: storage
ms.date: 06/01/2017
ms.author: jaboes
ms.subservice: disks
ms.openlocfilehash: 68f8e78f2c08c86a176d3254de5e32fe2534cc1f
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690849"
---
# <a name="using-disks-in-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿에서 디스크 사용

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

이 문서는 가상 머신을 프로비전하는 데 Azure Resource Manager 템플릿을 사용할 때 관리 및 관리되지 않는 디스크 간의 차이점을 설명합니다. 이 예제에서는 관리되지 않는 디스크를 사용하는 기존 템플릿을 관리 디스크로 업데이트하는 데 도움이 됩니다. 참조를 위해 [vm-simple-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-simple-windows) 템플릿을 가이드로 사용합니다. 직접 비교하려는 경우 [관리 디스크](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-simple-windows/azuredeploy.json)를 사용하는 것과 [관리되지 않는 디스크](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json)를 사용하는 이전 버전을 사용하는 템플릿을 살펴볼 수 있습니다.


## <a name="unmanaged-disks-template-formatting"></a>관리되지 않는 디스크 템플릿 서식 지정

관리되지 않는 디스크가 배포되는 방법을 살펴보는 것으로 시작하겠습니다. 관리되지 않는 디스크를 만들 경우 VHD 파일을 포함하는 스토리지 계정이 필요합니다. 새 스토리지 계정을 만들거나 이미 있는 계정을 사용할 수 있습니다. 이 문서는 새 스토리지 계정을 만드는 방법을 보여 줍니다. 아래 표시된 것처럼 리소스 블록에 스토리지 계정 리소스를 만듭니다.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

가상 머신 개체 내에서 계정이 가상 머신 이전에 생성되도록 스토리지 계정에 대한 종속성을 추가합니다. `storageProfile` 섹션 내에서 스토리지 계정을 참조하고 OS 디스크 및 모든 데이터 디스크에 필요한 VHD 위치의 전체 URI를 지정합니다.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>관리 디스크 템플릿 서식 지정

Azure Managed Disks를 사용하면 디스크가 최상위 리소스가 되며 사용자가 더 이상 스토리지 계정을 만들지 않아도 됩니다. `2016-04-30-preview` API 버전에서 처음 표시되었던 관리 디스크는 모든 후속 API 버전에서 제공되며 현재 기본 디스크 유형입니다. 다음 섹션에서는 기본 설정과 디스크를 추가로 사용자 지정하는 방법에 대한 정보를 안내합니다.

> [!NOTE]
> `2016-04-30-preview`와 `2017-03-30` 간에는 중요한 변경 내용이 있으므로 `2016-04-30-preview` 이상의 API 버전을 사용하는 것이 좋습니다.
>
>

### <a name="default-managed-disk-settings"></a>기본 관리 디스크 설정

이제 관리 디스크를 사용하여 VM을 만들기 위해 스토리지 계정 리소스를 만들지 않아도 됩니다. 아래 템플릿 예제를 참조하면 이전 비관리 디스크 예제와 몇 가지 차이점이 있습니다.

- `apiVersion`은 관리 디스크를 지원하는 버전입니다.
- `osDisk` 및 `dataDisks`는 특정 VHD URI를 더 이상 참조하지 않습니다.
- 추가 속성을 지정하지 않고 배포할 경우 디스크는 VM 크기에 따라 스토리지 유형을 사용합니다. 예를 들어 프리미엄 스토리지를 지원하는 VM 크기(Standard_D2s_v3과 같이 이름에 “s”가 포함된 크기)를 사용하는 경우 기본적으로 프리미엄 디스크가 구성됩니다. 디스크의 sku 설정을 통해 스토리지 유형을 지정하여 변경할 수 있습니다.
- 디스크 이름을 지정하지 않으면 OS 디스크의 경우 `<VMName>_OsDisk_1_<randomstring>` 형식을 사용하고 각 데이터 디스크의 경우 `<VMName>_disk<#>_<randomstring>` 형식을 사용합니다.
  - 사용자 지정 이미지에서 VM을 만드는 경우 사용자 지정 이미지 리소스에 정의된 디스크 속성에서 스토리지 계정 유형 및 디스크 이름의 기본 설정이 검색됩니다. 템플릿에서 해당 값을 지정하여 재정의할 수 있습니다.
- 기본적으로 Azure 디스크 암호화는 사용하지 않도록 설정되어 있습니다.
- 기본적으로 디스크 캐싱은 OS 디스크의 경우 읽기/쓰기이고 데이터 디스크의 경우 없음입니다.
- 아래 예제에는 여전히 스토리지 계정 종속성이 있습니다. 이 종속성은 진단 스토리지에만 적용되고 디스크 스토리지에는 필요하지 않습니다.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="using-a-top-level-managed-disk-resource"></a>최상위 관리 디스크 리소스 사용

가상 머신 개체에서 디스크 구성을 지정하는 대신, 최상위 디스크 리소스를 만들고 가상 머신 만들기의 일부로 연결할 수 있습니다. 예를 들어 다음과 같이 데이터 디스크로 사용할 디스크 리소스를 만들 수 있습니다.

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2018-06-01",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

그런 다음 VM 개체 내에서 이 디스크 개체를 참조하여 연결합니다. `managedDisk` 속성에서 만든 관리 디스크의 리소스 ID를 지정하면 VM이 생성될 때 디스크를 첨부할 수 있습니다. 위 코드에서 VM 리소스의 `apiVersion`은 `2017-03-30`으로 설정되어 있습니다. VM이 만들어지기 전에 리소스가 만들어졌는지 확인하는 디스크 리소스에 대한 종속성이 추가되었습니다.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>관리 디스크를 사용하여 VM에서 관리 가용성 집합 만들기

관리 디스크를 사용하여 VM에서 관리 가용성 집합을 만들려면 `sku` 개체를 가용성 집합 리소스에 추가하고 `name` 속성을 `Aligned`로 설정합니다. 이 속성을 사용하면 각 VM에 대한 디스크가 단일 실패 지점을 피할 만큼 서로 충분히 격리됩니다. 또한 가용성 집합 리소스의 `apiVersion`도 `2018-10-01`으로 설정됩니다.

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2018-10-01",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 3,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

### <a name="standard-ssd-disks"></a>표준 SSD 디스크

다음은 Resource Manager 템플릿에서 표준 SSD 디스크를 만드는 데 필요한 매개 변수입니다.

* Microsoft.Compute에 대한 *apiVersion* 은 `2018-04-01` 이상으로 설정해야 합니다.
* *managedDisk.storageAccountType* 을 `StandardSSD_LRS`로 지정합니다.

다음 예제에서는 표준 SSD 디스크를 사용하는 VM에 대한 *properties.storageProfile.osDisk* 섹션을 보여 줍니다.

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

템플릿을 사용하여 표준 SSD 디스크를 만드는 방법에 대한 전체 템플릿 예제를 보려면 [표준 SSD 데이터 디스크를 사용하여 Windows 이미지에서 VM 만들기](https://github.com/azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-with-standardssd-disk/)를 참조하세요.

### <a name="additional-scenarios-and-customizations"></a>추가 시나리오 및 사용자 지정

REST API 사양에 대한 전체 정보를 찾으려면 [관리 디스크 REST API 설명서 만들기](/rest/api/manageddisks/disks/disks-create-or-update)를 검토하세요. 추가 시나리오는 물론 템플릿 배포를 통해 API에 전송할 수 있는 허용되는 값 및 기본값을 확인합니다.

## <a name="next-steps"></a>다음 단계

* 관리 디스크를 사용하는 전체 템플릿은 다음 Azure 빠른 시작 리포지토리 링크를 방문하세요.
    * [관리 디스크가 있는 Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-simple-windows)
    * [관리 디스크가 있는 Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-simple-linux)
* 관리 디스크에 대해 자세히 알아보려면 [Azure Managed Disks 개요](managed-disks-overview.md) 문서를 참조하세요.
* [Microsoft.Compute/virtualMachines 템플릿 참조](/azure/templates/microsoft.compute/virtualmachines) 문서를 방문하여 가상 컴퓨터 리소스에 대한 템플릿 참조 설명서를 검토하세요.
* [Microsoft.Compute/disks 템플릿 참조](/azure/templates/microsoft.compute/disks) 문서를 방문하여 디스크 리소스에 대한 템플릿 참조 설명서를 검토하세요.
* Azure Virtual Machine Scale Sets에서 관리 디스크를 사용하는 방법에 대한 내용은 [확장 집합으로 데이터 디스크 사용](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md) 문서를 참조하세요.