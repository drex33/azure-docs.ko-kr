---
title: Service Fabric 관리형 클러스터 확장 방법
description: 이 자습서에서는 Service Fabric 관리형 클러스터의 노드 유형을 확장하는 방법에 대해 알아봅니다.
ms.topic: tutorial
ms.date: 8/23/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 938c163f4b27706999f7fe32f7935d4d7877c96b
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864973"
---
# <a name="tutorial-scale-out-a-service-fabric-managed-cluster"></a>자습서: Service Fabric 관리형 클러스터 스케일 아웃

이 자습서 시리즈에서는 다음을 설명합니다.

> [!div class="checklist"]
> * [Service Fabric 관리형 클러스터 배포 방법](tutorial-managed-cluster-deploy.md)
> * Service Fabric 관리형 클러스터 확장 방법
> * [Service Fabric 관리형 클러스터에서 노드 유형을 추가 및 제거하는 방법](tutorial-managed-cluster-add-remove-node-type.md)
> * [Service Fabric 관리형 클러스터에 애플리케이션을 배포하는 방법](tutorial-managed-cluster-deploy-app.md)

시리즈의 이번 부분에서는 다음 작업을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * Service Fabric 관리형 클러스터 노드 크기 조정

## <a name="prerequisites"></a>사전 요구 사항

* Service Fabric 관리형 클러스터([*관리형 클러스터 배포*](tutorial-managed-cluster-deploy.md) 참조)
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) 이상([*Azure PowerShell 설치*](/powershell/azure/install-az-ps) 참조)

## <a name="scale-a-service-fabric-managed-cluster"></a>Service Fabric 관리형 클러스터 크기 조정
크기를 조정하려는 노드 유형의 노드 수를 늘리거나 줄이려면 인스턴스 수를 변경합니다. 클러스터 배포나 Service Fabric Explorer에서 ARM 템플릿(Azure Resource Manager 템플릿)의 노드 유형 이름을 찾을 수 있습니다.  

> [!NOTE]
> 기본 노드 유형인 경우 기본 SKU 클러스터에 대해 3개의 노드, 표준 SKU 클러스터에 대해 5개의 노드보다 작을 수 없습니다.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "FE"
$instanceCount = "7"

Set-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -name $nodeTypeName -InstanceCount $instanceCount -Verbose
```

클러스터가 자동으로 업그레이드를 시작하고 몇 분 후에 추가 노드가 표시됩니다.

## <a name="next-steps"></a>다음 단계

이 단계에서는 Service Fabric 관리형 클러스터에서 노드 유형을 확장했습니다. 노드 유형 추가 및 제거에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Service Fabric 관리형 클러스터에 노드 유형 추가 및 제거](tutorial-managed-cluster-add-remove-node-type.md)
