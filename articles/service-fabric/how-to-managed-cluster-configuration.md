---
title: Service Fabric 관리형 클러스터 구성
description: 자동 OS 업그레이드, NSG 규칙 등에 대해 Service Fabric 관리 클러스터를 구성하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: aad982def0a1dd2e4b6f847a72e270337abcd460
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286035"
---
# <a name="service-fabric-managed-cluster-configuration-options"></a>Service Fabric 관리 클러스터 구성 옵션

클러스터를 만들 때 [Service Fabric 관리 클러스터 SKU](overview-managed-cluster.md#service-fabric-managed-cluster-skus)를 선택하는 것 외에도 다음을 포함하여 여러 가지 다른 방법으로 클러스터를 구성할 수 있습니다.

* 노드 유형에 [가상 머신 확장 집합 확장](how-to-managed-cluster-vmss-extension.md) 추가
* 클러스터 [가용성 영역 스패닝](how-to-managed-cluster-availability-zones.md) 구성
* 클러스터 [네트워크 설정](how-to-managed-cluster-networking.md) 구성
* [대규모 가상 머신 확장 집합](how-to-managed-cluster-large-virtual-machine-scale-sets.md)에 대한 노드 유형 구성
* 클러스터 노드 유형에서 [관리 ID](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) 구성
* 클러스터 노드에서 [OS 및 데이터 디스크 암호화](how-to-managed-cluster-enable-disk-encryption.md) 사용
* 보조 노드 유형에서 [자동 조정](how-to-managed-cluster-autoscale.md) 구성
* [노드 형식 수동 크기 조정](how-to-managed-cluster-modify-node-type.md#scale-a-node-type-manually-with-portal)
* 클러스터 노드 유형에서 [자동 OS 이미지 업그레이드 사용](how-to-managed-cluster-modify-node-type.md#enable-automatic-os-image-upgrades)
* 노드 유형에 사용되는 [OS 이미지](how-to-managed-cluster-modify-node-type.md#modify-the-os-image-for-a-node-type-with-portal) 수정
* 노드 형식에 대한 [배치 속성](how-to-managed-cluster-modify-node-type.md#configure-placement-properties-for-a-node-type-with-portal) 구성
* 클러스터 [관리 디스크 유형](how-to-managed-cluster-managed-disk.md) SKU 선택
* 런타임 업데이트를 위한 클러스터 [업그레이드 옵션](how-to-managed-cluster-upgrades.md) 구성


## <a name="next-steps"></a>다음 단계

[Service Fabric 관리 클러스터 개요](overview-managed-cluster.md)

[Service Fabric 클러스터 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates)
