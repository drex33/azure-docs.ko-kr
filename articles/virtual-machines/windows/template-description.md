---
title: Azure Resource Manager 템플릿의 가상 머신 | Microsoft Azure
description: Azure Resource Manager 템플릿에서 가상 머신 리소스를 정의하는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure
ms.topic: how-to
ms.date: 01/03/2019
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6a14d5a7ae7da5eff16ba57aa6bbf3459c2ad909
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691688"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿의 가상 머신
**적용 대상:** :heavy_check_mark: Windows VM 

이 문서에서는 가상 머신에 적용되는 Azure Resource Manager 템플릿의 측면을 설명합니다. 이 문서는 가상 머신을 만들기 위한 완전한 템플릿 설명하지 않습니다. 이를 위해 스토리지 계정, 네트워크 인터페이스, 공용 IP 주소 및 가상 네트워크에 대한 리소스 정의가 필요합니다. 이러한 리소스를 함께 정의할 수 있는 방법에 대한 자세한 내용은 [Resource Manager 템플릿 연습](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)을 참조하세요.

갤러리에 VM 리소스를 포함하는 [많은 템플릿](https://azure.microsoft.com/resources/templates/?term=VM)이 있습니다. 템플릿에 포함될 수 있는 모든 요소가 여기에 설명되어 있지 않습니다.

 

이 예제에서는 지정된 수의 VM을 만들기 위한 템플릿의 일반적인 리소스 섹션을 보여 줍니다.

```json
"resources": [
  { 
    "apiVersion": "2016-04-30-preview", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[concat('myVM', copyindex())]", 
    "location": "[resourceGroup().location]",
    "copy": {
      "name": "virtualMachineLoop",  
      "count": "[parameters('numberOfInstances')]"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/myNIC', copyindex())]" 
    ], 
    "properties": { 
      "hardwareProfile": { 
        "vmSize": "Standard_DS1" 
      }, 
      "osProfile": { 
        "computername": "[concat('myVM', copyindex())]", 
        "adminUsername": "[parameters('adminUsername')]", 
        "adminPassword": "[parameters('adminPassword')]" 
      }, 
      "storageProfile": { 
        "imageReference": { 
          "publisher": "MicrosoftWindowsServer", 
          "offer": "WindowsServer", 
          "sku": "2012-R2-Datacenter", 
          "version": "latest" 
        }, 
        "osDisk": { 
          "name": "[concat('myOSDisk', copyindex())]",
          "caching": "ReadWrite", 
          "createOption": "FromImage" 
        },
        "dataDisks": [
          {
            "name": "[concat('myDataDisk', copyindex())]",
            "diskSizeGB": "100",
            "lun": 0,
            "createOption": "Empty"
          }
        ] 
      }, 
      "networkProfile": { 
        "networkInterfaces": [ 
          { 
            "id": "[resourceId('Microsoft.Network/networkInterfaces',
              concat('myNIC', copyindex()))]" 
          } 
        ] 
      },
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('https://', variables('storageName'), '.blob.core.windows.net')]"
        }
      } 
    },
    "resources": [ 
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
      {
        "name": "MyCustomScriptExtension",
        "type": "extensions",
        "apiVersion": "2016-03-30",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
        ],
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.7",
          "autoUpgradeMinorVersion": true,
          "settings": {
            "fileUris": [
              "[concat('https://', variables('storageName'),
                '.blob.core.windows.net/customscripts/start.ps1')]" 
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
          }
        }
      } 
    ]
  } 
]
``` 

> [!NOTE] 
>이 예제에서는 이전에 만든 스토리지 계정을 사용합니다. 템플릿에서 배포하여 스토리지 계정을 만들 수 있습니다. 예제에서는 또한 템플릿에서 정의될 수 있는 네트워크 인터페이스 및 해당 종속 리소스에 의존합니다. 이러한 리소스는 예제에 표시되지 않습니다.
>
>

## <a name="api-version"></a>API 버전

템플릿을 사용하여 리소스를 배포할 때 사용할 API의 버전을 지정해야 합니다. 예제에서는 이 apiVersion 요소를 사용하여 가상 머신 리소스를 보여 줍니다.

```json
"apiVersion": "2016-04-30-preview",
```

템플릿에서 지정하는 API의 버전은 템플릿에서 정의할 수 있는 속성에 영향을 줍니다. 일반적으로 템플릿을 만들 때 최신 버전의 API를 선택해야 합니다. 기존 템플릿의 경우 이전 API 버전을 계속 사용할지 아니면 새 기능을 이용하도록 최신 버전에 맞게 템플릿을 업데이트할지 여하를 결정할 수 있습니다.

최신 API 버전을 가져오기 위해 이러한 기회를 사용합니다.

- REST API - [모든 리소스 공급자 나열](/rest/api/resources/providers)
- PowerShell - [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider)
- Azure CLI - [az provider show](/cli/azure/provider)


## <a name="parameters-and-variables"></a>매개 변수 및 변수

[매개 변수](../../azure-resource-manager/templates/syntax.md)를 통해 실행할 때 템플릿에 대한 값을 손쉽게 지정할 수 있습니다. 이 매개 변수 섹션은 예제에 사용됩니다.

```json
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

예제 템플릿을 배포할 때 각 VM에서 관리자 계정의 이름 및 암호에 대한 값과 만들 VM의 수를 입력합니다. 템플릿을 사용하여 관리되는 별도 파일에서 매개 변수 값을 지정하거나 메시지가 표시되면 값을 제공하는 옵션이 있습니다.

[변수](../../azure-resource-manager/templates/syntax.md)를 통해 전체에 걸쳐 반복해서 사용되거나 시간에 따라 달라질 수 있는 템플릿의 값을 쉽게 설정할 수 있습니다. 이 변수 섹션은 예제에 사용됩니다.

```json
"variables": { 
  "storageName": "mystore1",
  "accountid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name,
  '/providers/','Microsoft.Storage/storageAccounts/', variables('storageName'))]", 
  "wadlogs": "<WadCfg> 
    <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> 
      <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> 
      <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > 
        <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" />
      </WindowsEventLog>", 
  "wadperfcounters": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\">
      <PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\">
        <annotation displayName=\"Threads\" locale=\"en-us\"/>
      </PerformanceCounterConfiguration>
    </PerformanceCounters>", 
  "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters'), 
    '<Metrics resourceId=\"')]", 
  "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name , 
    '/providers/', 'Microsoft.Compute/virtualMachines/')]", 
  "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/>
    <MetricAggregation scheduledTransferPeriod=\"PT1M\"/>
    </Metrics></DiagnosticMonitorConfiguration>
    </WadCfg>"
}, 
```

예제 템플릿을 배포할 때 이전에 만든 스토리지 계정의 이름 및 식별자에 대한 변수 값이 사용됩니다. 변수는 진단 확장에 대한 설정을 제공하는 데에도 사용됩니다. [Azure Resource Manager 템플릿 생성 모범 사례](../../azure-resource-manager/templates/best-practices.md)를 사용하여 템플릿에서 매개 변수 및 변수를 구성하는 방법을 결정할 수 있습니다.

## <a name="resource-loops"></a>리소스 루프

애플리케이션에 하나 이상의 가상 머신이 필요할 때 템플릿에서 복사 요소를 사용할 수 있습니다. 이 선택적 요소는 매개 변수로 지정한 VM의 수 만들기를 통해 반복합니다.

```json
"copy": {
  "name": "virtualMachineLoop",  
  "count": "[parameters('numberOfInstances')]"
},
```

또한 예제에서 루프 인덱스는 리소스에 대한 일부 값을 지정할 때 사용됩니다. 예를 들어 3의 인스턴스 수를 입력한 경우 운영 체제 디스크의 이름은 myOSDisk1, myOSDisk2 및 myOSDisk3입니다.

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>이 예제에서는 가상 머신에 대해 Managed Disks를 사용합니다.
>
>

템플릿에서 한 리소스에 대한 루프 만들기는 다른 리소스를 만들거나 액세스할 때 루프를 사용해야 한다는 점을 염두에 두십시오. 예를 들어 여러 VM은 동일한 네트워크 인터페이스를 사용할 수 없으므로 템플릿이 세 개의 VM 만들기를 통해 반복하는 경우 세 개의 네트워크 인터페이스 만들기를 통해 반복해야 합니다. VM에 네트워크 인터페이스를 할당할 때 루프 인덱스는 이를 식별하는 데 사용됩니다.

```json
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>종속성

대부분의 리소스는 제대로 작동하기 위해 다른 리소스에 따라 달라집니다. 가상 머신은 가상 네트워크와 연결되어야 하며 이를 수행하기 위해 네트워크 인터페이스가 필요합니다. [dependsOn](../../azure-resource-manager/templates/resource-dependency.md) 요소는 VM이 생성되기 전에 네트워크 인터페이스가 사용될 준비가 되었는지 확인하는 데 사용됩니다.

```json
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Resource Manager는 배포되는 다른 리소스에 종속되지 않는 모든 리소스를 동시에 배포합니다. 불필요한 종속성을 지정하여 실수로 배포 속도를 늦출 수 있으므로 종속성을 설정할 때 주의해야 합니다. 종속성은 여러 리소스를 통해 연결할 수 있습니다. 예를 들어 네트워크 인터페이스는 공용 IP 주소 및 가상 네트워크 리소스에 따라 다릅니다.

종속성이 필수인지 어떻게 알 수 있을까요? 템플릿에서 설정한 값을 살펴봅니다. 가상 머신 리소스 정의의 요소가 동일한 템플릿에 배포된 다른 리소스를 가리키는 경우 종속성이 필요합니다. 예를 들어 예제 가상 머신은 네트워크 프로필을 정의합니다.

```json
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

이 속성을 설정하려면 네트워크 인터페이스가 있어야 합니다. 따라서 종속성이 필요합니다. 또한 하나의 리소스(자식)가 다른 리소스(부모) 내에서 정의될 때에도 종속성을 설정해야 합니다. 예를 들어 진단 설정 및 사용자 지정 스크립트 확장 모두는 가상 머신의 자식 리소스로 정의됩니다. 가상 머신이 있을 때까지 만들 수 없습니다. 따라서 두 리소스는 가상 머신에 따라 표시됩니다.

## <a name="profiles"></a>Profiles

몇 가지 프로필 요소는 가상 머신 리소스를 정의할 때 사용됩니다. 일부는 필요하고 일부는 선택 사항입니다. 예를 들어 hardwareProfile, osProfile, storageProfile 및 networkProfile 요소는 필요하지만 diagnosticsProfile은 선택 사항입니다. 이러한 프로필은 다음과 같은 설정을 정의합니다.
   
- [size](../sizes.md)
- [이름](/azure/architecture/best-practices/resource-naming) 및 자격 증명
- 디스크 및 [운영 체제 설정](cli-ps-findimage.md)
- [네트워크 인터페이스](/previous-versions/azure/virtual-network/virtual-network-deploy-multinic-classic-ps) 
- 부트 진단

## <a name="disks-and-images"></a>디스크 및 이미지
   
Azure에서 vhd 파일은 [디스크 또는 이미지](../managed-disks-overview.md)를 나타낼 수 있습니다. vhd 파일에서 운영 체제가 특정 VM이 되도록 특수화된 경우 디스크를 나타냅니다. vhd 파일에서 운영 체제가 여러 VM을 만드는 데 사용되도록 일반화된 경우 이미지를 나타냅니다.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>플랫폼 이미지에서 새 가상 머신 및 새 디스크 만들기

VM을 만들 때 사용할 운영 체제를 결정해야 합니다. imageReference 요소는 새 VM의 운영 체제를 정의하는 데 사용됩니다. 예제는 Windows Server 운영 체제에 대한 정의를 보여 줍니다.

```json
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Linux 운영 체제를 만들려는 경우 이 정의를 사용할 수 있습니다.

```json
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

운영 체제 디스크에 대한 구성 설정은 osDisk 요소와 함께 할당됩니다. 이 예제에서는 캐싱 모드가 **ReadWrite** 로 설정된 새 관리되는 디스크를 정의하며 해당 디스크는 [플랫폼 이미지](cli-ps-findimage.md)에서 만들어집니다.

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>기존 Managed Disks에서 새 가상 머신 만들기

기존 디스크에서 가상 머신을 만들려는 경우 imageReference 및 osProfile 요소를 제거하고 이러한 디스크 설정을 정의합니다.

```json
"osDisk": { 
  "osType": "Windows",
  "managedDisk": { 
    "id": "[resourceId('Microsoft.Compute/disks', [concat('myOSDisk', copyindex())])]" 
  }, 
  "caching": "ReadWrite",
  "createOption": "Attach" 
},
```

### <a name="create-new-virtual-machines-from-a-managed-image"></a>관리되는 이미지에서 새 가상 머신 만들기

관리되는 이미지에서 가상 머신을 만들려는 경우 imageReference 요소를 변경하고 다음과 같은 디스크 설정을 정의합니다.

```json
"storageProfile": { 
  "imageReference": {
    "id": "[resourceId('Microsoft.Compute/images', 'myImage')]"
  },
  "osDisk": { 
    "name": "[concat('myOSDisk', copyindex())]",
    "osType": "Windows",
    "caching": "ReadWrite", 
    "createOption": "FromImage" 
  }
},
```

### <a name="attach-data-disks"></a>데이터 디스크 연결

필요에 따라 VM에 데이터 디스크를 추가할 수 있습니다. [디스크 수](../sizes.md)는 사용하는 운영 체제 디스크의 크기에 따라 달라집니다. Standard_DS1_v2로 설정된 VM의 크기를 사용하면 추가될 수 있는 데이터 디스크의 최대 수는 2입니다. 이 예제에서는 하나의 관리되는 데이터 디스크를 각 VM에 추가합니다.

```json
"dataDisks": [
  {
    "name": "[concat('myDataDisk', copyindex())]",
    "diskSizeGB": "100",
    "lun": 0, 
    "caching": "ReadWrite",
    "createOption": "Empty"
  }
],
```

## <a name="extensions"></a>확장

[확장](../extensions/features-windows.md)은 별도 리소스이지만 VM에 밀접하게 연결되어 있습니다. 확장은 VM의 자식 리소스 또는 별도 리소스로 추가될 수 있습니다. 예제는 VM에 추가되고 있는 [진단 확장](../extensions/diagnostics-template.md)을 보여 줍니다.

```json
{ 
  "name": "Microsoft.Insights.VMDiagnosticsSettings", 
  "type": "extensions", 
  "location": "[resourceGroup().location]", 
  "apiVersion": "2016-03-30", 
  "dependsOn": [ 
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
  ], 
  "properties": { 
    "publisher": "Microsoft.Azure.Diagnostics", 
    "type": "IaaSDiagnostics", 
    "typeHandlerVersion": "1.5", 
    "autoUpgradeMinorVersion": true, 
    "settings": { 
      "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
      variables('wadmetricsresourceid'), 
      concat('myVM', copyindex()),
      variables('wadcfgxend')))]", 
      "storageAccount": "[variables('storageName')]" 
    }, 
    "protectedSettings": { 
      "storageAccountName": "[variables('storageName')]", 
      "storageAccountKey": "[listkeys(variables('accountid'), 
        '2015-06-15').key1]", 
      "storageAccountEndPoint": "https://core.windows.net" 
    } 
  } 
},
```

이 확장 리소스는 storageName 변수 및 진단 변수를 사용하여 값을 제공합니다. 이 확장에서 수집되는 데이터를 변경하려는 경우 wadperfcounters 변수에 더 많은 성능 카운터를 추가할 수 있습니다. VM 디스크가 저장되는 위치가 아닌 다른 스토리지 계정에 진단 데이터를 넣도록 선택할 수도 있습니다.

VM에 설치할 수 있는 많은 확장이 있지만 가장 유용한 것은 [사용자 지정 스크립트 확장](../extensions/custom-script-windows.md)입니다. 예제에서 start.ps1이라는 PowerShell 스크립트는 처음 시작할 때 각 VM에서 실행됩니다.

```json
{
  "name": "MyCustomScriptExtension",
  "type": "extensions",
  "apiVersion": "2016-03-30",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
  ],
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "[concat('https://', variables('storageName'),
          '.blob.core.windows.net/customscripts/start.ps1')]" 
      ],
      "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
    }
  }
}
```

start.ps1 스크립트는 여러 구성 작업을 수행할 수 있습니다. 예를 들어 예제에서 VM에 추가되는 데이터 디스크는 초기화되지 않습니다. 사용자 지정 스크립트를 사용하여 초기화할 수 있습니다. 수행할 여러 시작 작업이 있는 경우 start.ps1 파일을 사용하여 Azure Storage에서 다른 PowerShell 스크립트를 호출할 수 있습니다. 예제에서는 PowerShell을 사용하지만 사용 중인 운영 체제에서 사용할 수 있는 모든 스크립팅 메서드를 사용할 수 있습니다.

포털의 확장 설정에서 설치된 확장의 상태를 확인할 수 있습니다.

![확장 상태 가져오기](./media/template-description/virtual-machines-show-extensions.png)

**Get-AzVMExtension** PowerShell 명령, **vm extension get** Azure CLI 명령 또는 **Get extension information** REST API를 사용하여 확장 정보를 가져올 수도 있습니다.

## <a name="deployments"></a>배포

템플릿을 배포하는 경우 Azure는 그룹으로 배포한 리소스를 추적하고 이 배포된 그룹에 이름을 자동으로 할당합니다. 배포의 이름은 템플릿의 이름과 같습니다.

배포의 리소스 상태를 알고 싶은 경우 Azure Portal에서 리소스 그룹을 보면 됩니다.

![배포 정보 가져오기](./media/template-description/virtual-machines-deployment-info.png)
    
리소스를 만들거나 기존 리소스를 업데이트하는 데 동일한 템플릿을 사용하는 것은 문제가 아닙니다. 명령을 사용하여 템플릿을 배포할 때 사용하려는 [모드](../../azure-resource-manager/templates/deploy-powershell.md)를 말할 수 있는 기회가 있습니다. 모드는 **전체** 또는 **증분** 중 하나로 설정될 수 있습니다. 기본 값은 증분 업데이트를 수행하는 것입니다. 리소스를 실수로 삭제할 수 있으므로 **전체** 모드를 사용할 때 주의해야 합니다. 모드를 **전체** 로 설정하면 Resource Manager는 템플릿에 없는 리소스 그룹의 모든 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Resource Manager 템플릿 작성](../../azure-resource-manager/templates/syntax.md)을 사용하여 고유의 템플릿을 만듭니다.
- [Resource Manager 템플릿을 사용하여 Windows 가상 머신 만들기](ps-template.md)를 사용하여 자신이 만든 템플릿을 배포합니다.
- [Azure PowerShell 모듈을 사용하여 Windows VM 만들기 및 관리](tutorial-manage-vm.md)를 검토하여 만든 VM을 관리하는 방법을 알아봅니다.
- 템플릿에서 리소스 종류의 JSON 구문 및 속성은 [Azure Resource Manager 템플릿 참조](/azure/templates/)에서 확인하세요.