---
title: 상태 비저장 노드 유형을 사용하여 Service Fabric 관리형 클러스터 배포
description: Service Fabric 관리형 클러스터에서 상태 비저장 노드 유형을 만들고 배포하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: a18c09e92cd8d78e314969fe15a802e4b7e2cc67
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546371"
---
# <a name="deploy-a-service-fabric-managed-cluster-with-stateless-node-types"></a>상태 비저장 노드 유형을 사용하여 Service Fabric 관리형 클러스터 배포

Service Fabric 노드 유형은 특정 시점에 상태 저장 서비스가 노드에 배치될 수 있다는 가정 하에 제공됩니다. 상태 비저장 노드 유형은 노드 유형에 대한 이러한 가정을 완화합니다. 이 가정을 완화하면 노드 상태 비저장 노드 유형이 복구 및 유지 관리 작업에 대한 제한 사항 중 일부를 제거하여 더 빠른 스케일 아웃 작업을 활용할 수 있습니다.

* 주 노드 유형은 상태 비저장으로 구성할 수 없음
* 상태 비저장 노드 유형에는 **2021-05-01** 이상의 API 버전이 필요합니다.
* 그러면 **multipleplacementgroup** 속성이 자동으로 **true** 로 설정되며 이에 대해서는 [여기에서 자세히 알아볼 수 있습니다](how-to-managed-cluster-large-virtual-machine-scale-sets.md).
* 이를 통해 주어진 노드 유형에 대해 최대 1000개의 노드를 지원할 수 있습니다.

사용 가능한 샘플 템플릿: [Service Fabric 상태 비저장 노드 유형 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="enable-stateless-node-types-in-a-service-fabric-managed-cluster"></a>Service Fabric 관리형 클러스터에서 상태 비저장 노드 유형 사용
노드 유형 리소스에서 하나 이상의 노드 유형을 상태 비저장으로 설정하려면 **isStateless** 속성을 **true** 로 설정합니다. 상태 비저장 노드 유형을 사용하여 Service Fabric 클러스터를 배포하는 경우 클러스터에 상태 비저장이 아닌 주 노드 유형이 하나 이상 있어야 합니다.

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
    "isStateless": true,
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
```

## <a name="configure-stateless-node-types-with-multiple-availability-zones"></a>여러 가용성 영역이 있는 상태 비저장 노드 유형 구성
여러 가용성 영역에 걸쳐 있는 상태 비저장 노드 유형을 구성하려면 [가용성 영역에서 Service Fabric 클러스터](.\service-fabric-cross-availability-zones.md)를 따릅니다. 

>[!NOTE]
> 영역 복원력 속성은 클러스터 수준에서 설정해야 하며 이 속성은 현재 위치로 변경할 수 없습니다.

## <a name="migrate-to-using-stateless-node-types-in-a-cluster"></a>클러스터에서 상태 비저장 노드 유형을 사용하도록 마이그레이션
모든 마이그레이션 시나리오에 대해 새로운 상태 비저장 노드 유형을 추가해야 합니다. 기존 노드 유형은 상태 비저장으로 마이그레이션할 수 없습니다. 기존 Service Fabric 관리형 클러스터에 새 상태 비저장 노드 유형을 추가하고 클러스터에서 원래 노드 유형을 제거할 수 있습니다. 

## <a name="next-steps"></a>다음 단계 

Service Fabric 관리형 클러스터에 대해 자세히 알아보려면 다음을 참고하세요.

> [!div class="nextstepaction"]
> [Service Fabric 관리형 클러스터 질문과 대답](./faq-managed-cluster.yml)
