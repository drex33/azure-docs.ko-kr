---
title: Service Fabric 관리형 클러스터에서 대규모 가상 머신 확장 집합에 대한 보조 노드 형식 구성
description: 이 문서에서는 보조 노드 형식을 대규모 가상 머신 확장 집합으로 구성하는 방법을 안내합니다.
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: 5b4601c347abcecb06e394f645c21141994681fc
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867808"
---
# <a name="service-fabric-managed-cluster-node-type-scaling"></a>Service Fabric 관리형 클러스터 노드 형식 크기 조정

Service Fabric 관리형 클러스터의 각 노드 형식은 가상 머신 확장 집합을 통해 백업됩니다. 관리형 클러스터 노드 형식에서 [대규모 가상 머신 확장 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) 을 만들 수 있도록 하기 위해 속성 `multiplePlacementGroups`이 노드 형식 정의에 추가 되었습니다. 기본값으로 관리형 클러스터 노드 형식은 이 속성을 false로 설정하여 배치 그룹 내에서 오류 및 업그레이드 도메인을 일관성 있게 유지하지만, 이 설정은 노드 형식이 100개의 VM 이상으로 확장되는 것을 제한합니다. 애플리케이션이 대규모 확장 집합을 효과적으로 사용할 수 있는지 판단하기 위해서는 [이 요구 사항 목록](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md#checklist-for-using-large-scale-sets)을 참조하세요.

Azure Service Fabric 관리형 클러스터 리소스 공급자는 크기를 조정하고 데이터에 대해 관리 디스크를 사용하기 때문에 상태 저장 및 상태 비저장 보조 노드 형식 모두에 대해 대규모 확장 집합을 지원할 수 있습니다.

> [!NOTE]
> 노드 형식이 배포 된 후에는 이 속성을 수정할 수 없습니다.

## <a name="enable-large-virtual-machine-scale-sets-in-a-service-fabric-managed-cluster"></a>Service Fabric 관리형 클러스터에서 대규모 가상 머신 확장 집합 사용
보조 노드 형식을 대규모 확장 집합으로 구성하려면 **multiplePlacementGroups** 속성을 **true** 로 설정합니다.
> [!NOTE]
> 주 노드 형식에는 이 속성을 설정할 수 없습니다.

* Service Fabric 관리형 클러스터 리소스 apiVersion은 **2021-05-01** 이상이어야 합니다.

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "dependsOn": [
              "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
            ],
            "properties": {
                "multiplePlacementGroups": true,
                "isPrimary": false,
                "vmImagePublisher": "[parameters('vmImagePublisher')]",
                "vmImageOffer": "[parameters('vmImageOffer')]",
                "vmImageSku": "[parameters('vmImageSku')]",
                "vmImageVersion": "[parameters('vmImageVersion')]",
                "vmSize": "[parameters('nodeTypeSize')]",
                "vmInstanceCount": "[parameters('nodeTypeVmInstanceCount')]",
                "dataDiskSizeGB": "[parameters('nodeTypeDataDiskSizeGB')]"
            }
        }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Service Fabric 관리형 클러스터에 앱 배포](./tutorial-managed-cluster-deploy-app.md)
