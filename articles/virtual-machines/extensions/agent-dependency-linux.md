---
title: Linux용 Azure Monitor 종속성 가상 머신 확장
description: 가상 머신 확장을 사용하여 Linux 가상 머신에 Azure Monitor 종속성 에이전트를 배포합니다.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgoedtel
ms.author: magoedte
ms.collection: linux
ms.date: 06/01/2021
ms.openlocfilehash: ce4b7ee2fea9d5b2b7c92b5ade1b3ad146382214
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567093"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Linux용 Azure Monitor 종속성 가상 머신 확장

VM용 Azure Monitor 맵 기능은 Microsoft Dependency Agent에서 해당 데이터를 가져옵니다. Linux용 Azure VM 종속성 에이전트 가상 머신 확장은 Microsoft에서 게시 및 지원합니다. 이 확장은 Azure 가상 머신에 종속성 에이전트를 설치합니다. 이 문서에서는 Linux용 Azure VM 종속성 에이전트 가상 머신 확장에 대한 지원되는 플랫폼, 구성 및 배포 옵션을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

### <a name="operating-system"></a>운영 체제

Linux용 Azure VM 종속성 에이전트 확장은 VM용 Azure Monitor 배포 문서의 [지원되는 운영 체제](../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) 섹션에 나열된 지원되는 운영 체제에 실행할 수 있습니다.

## <a name="extension-schema"></a>확장 스키마

다음 JSON은 Azure Linux VM의 Azure VM 종속성 에이전트 확장에 대한 스키마를 보여 줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux Azure VM."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>속성 값

| Name | 값/예제 |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentLinux |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 이전 섹션에서 자세히 설명한 JSON 스키마를 Azure Resource Manager 템플릿에서 사용하여 Azure Resource Manager 템플릿 배포 중 Azure VM 종속성 에이전트 확장을 실행할 수 있습니다.

가상 머신 확장용 JSON은 가상 머신 리소스 내에 중첩할 수 있습니다. 또는 Resource Manager JSON 템플릿의 루트 또는 최상위 수준에 배치할 수 있습니다. JSON의 배치는 리소스 이름 및 형식 값에 영향을 줍니다. 자세한 내용은 [자식 리소스의 이름 및 형식 설정](../../azure-resource-manager/templates/child-resource-name-type.md)을 참조하세요.

다음 예제에서는 종속성 에이전트 확장이 가상 머신 리소스 내에 중첩되어 있다고 가정합니다. 확장 리소스를 중첩하는 경우 JSON은 가상 머신의 `"resources": []` 개체에 배치됩니다.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

템플릿의 루트에 JSON 확장을 저장하면 리소스 이름에 부모 가상 머신에 대한 참조가 포함됩니다. 형식은 중첩된 구성을 반영합니다.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI 배포

Azure CLI를 사용하여 종속성 에이전트 VM 확장을 기존 가상 머신에 배포할 수 있습니다.  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5
```

## <a name="automatic-extension-upgrade"></a>자동 확장 업그레이드
종속성 확장의 [부 버전을 자동으로 업그레이드](../automatic-extension-upgrade.md)하는 새로운 기능을 이제 사용할 수 있습니다.

확장에 대한 자동 확장 업그레이드를 사용하도록 설정하려면 `enableAutomaticUpgrade` 속성이 `true`로 설정되고 확장 템플릿에 추가되었는지 확인해야 합니다. 이 속성은 모든 VM 또는 VM 확장 집합에서 개별적으로 사용하도록 설정해야 합니다. [사용](../automatic-extension-upgrade.md#enabling-automatic-extension-upgrade) 섹션에 설명된 방법 중 하나를 사용하여 VM 또는 VM 확장 집합에 대한 기능을 사용하도록 설정합니다.

VM 또는 VM 확장 집합에서 자동 확장 업그레이드를 사용하도록 설정하면 확장 게시자가 해당 확장에 대한 새 버전을 릴리스할 때마다 확장이 자동으로 업그레이드됩니다. 업그레이드는 [여기](../automatic-extension-upgrade.md#how-does-automatic-extension-upgrade-work)에 설명된 가용성 우선 원칙에 따라 안전하게 적용됩니다.

`enableAutomaticUpgrade` 특성의 기능은 `autoUpgradeMinorVersion`의 기능과 다릅니다. 확장 게시자가 새 버전을 릴리스할 때 `autoUpgradeMinorVersion` 특성은 부 버전 업데이트를 자동으로 트리거하지 않습니다. `autoUpgradeMinorVersion` 특성은 배포 시 사용할 수 있는 경우 확장에서 최신 부 버전을 사용해야 하는지 여부를 나타냅니다. 그러나 일단 배포되면 이 속성이 true로 설정된 경우에도 확장이 재배포되지 않는 한 부 버전을 업그레이드하지 않습니다.

확장 버전을 업데이트된 상태로 유지하려면 확장 배포와 함께 `enableAutomaticUpgrade`를 사용하는 것이 좋습니다.

> [!IMPORTANT]
> 템플릿에 `enableAutomaticUpgrade`를 추가하는 경우 최소 API 버전 2019-12-01 이상을 사용해야 합니다.

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure CLI를 사용하여 Azure Portal에서 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure CLI를 사용하여 다음 명령을 실행합니다.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

확장 실행 출력은 다음 파일에 기록됩니다.

```
/opt/microsoft/dependency-agent/log/install.log
```

### <a name="support"></a>지원

이 문서의 어디서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의하세요. 또는 Azure 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기** 를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
