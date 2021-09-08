---
title: Service Fabric 관리형 클러스터 노드 형식에 가상 머신 확장 집합 확장 추가
description: Service Fabric 관리형 클러스터 노드 형식에 가상 머신 확장 집합 확장을 추가하는 방법은 다음과 같습니다.
ms.topic: article
ms.date: 8/02/2021
ms.openlocfilehash: 8755eb7551dd7f7e572632ee50eb57467f675822
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866394"
---
# <a name="virtual-machine-scale-set-extension-support-on-service-fabric-managed-cluster-node-types"></a>Service Fabric 관리형 클러스터 노드 형식에 가상 머신 확장 집합 확장 지원

Service Fabric 관리형 클러스터의 각 노드 형식은 가상 머신 확장 집합을 통해 백업됩니다. 이를 통해 [가상 머신 확장 집합 확장](../virtual-machines/extensions/overview.md)을 Service Fabric 관리형 클러스터 노드 형식에 추가할 수 있습니다. 확장은 Azure VM에서 배포 후 구성 및 자동화를 제공하는 작은 애플리케이션입니다. Azure 플랫폼은 VM 구성, 모니터링, 보안 및 유틸리티 애플리케이션을 포함하는 다양한 확장을 호스팅합니다. 게시자는 애플리케이션을 사용하여 확장으로 래핑하고 설치를 간소화합니다. 필수 매개 변수를 제공하기만 하면 됩니다. 

## <a name="add-a-virtual-machine-scale-set-extension"></a>가상 머신 확장 집합 확장 추가
[Add-AzServiceFabricManagedNodeTypeVMExtension](/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension) PowerShell 명령을 사용하여 Service Fabric 관리형 클러스터 노드 형식에 가상 머신 확장 집합 확장을 추가할 수 있습니다.

또는 Azure Resource Manager 템플릿의 Service Fabric 관리형 클러스터 노드 형식에서 가상 머신 확장 집합 확장을 추가할 수 있습니다. 예를 들면 다음과 같습니다.

```json
{
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "[variables('sfApiVersion')]",
    "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
    "dependsOn": [
        "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
    ],
    "location": "[resourceGroup().location]",
    "properties": {
        "isPrimary": true,
        "vmInstanceCount": 3,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2",
        "vmImagePublisher": "MicrosoftWindowsServer",
        "vmImageOffer": "WindowsServer",
        "vmImageSku": "2019-Datacenter",
        "vmImageVersion": "latest",
        "vmExtensions": [{
            "name": "ExtensionA",
            "properties": {
                "publisher": "ExtensionA.Publisher",
                "type": "KeyVaultForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                }
            }
        }]
    }
}
```

Service Fabric 관리형 클러스터 노드 형식 구성에 대한 자세한 내용은 [관리형 클러스터 노드 형식](/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Service Fabric 관리형 클러스터에 대해 자세히 알아보려면 다음을 참고하세요.

> [!div class="nextstepaction"]
> [Service Fabric 관리형 클러스터 질문과 대답](./faq-managed-cluster.yml)
