---
title: 가용성 영역에서 클러스터 배포
description: 가용성 영역에서 Azure Service Fabric 클러스터를 만드는 방법을 알아봅니다.
author: peterpogorski
ms.topic: conceptual
ms.date: 05/24/2021
ms.author: pepogors
ms.openlocfilehash: aaf5be101320a5b8cccb24b89908c788996fc494
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122539326"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>가용성 영역에서 Azure Service Fabric 클러스터 배포

Azure의 가용성 영역은 데이터 센터 오류에서 애플리케이션 및 데이터를 보호하는 고가용성 기능입니다. 가용성 영역은 Azure 지역 내에서 독립적인 전원, 냉각 및 네트워킹을 갖춘 고유한 물리적 위치입니다.

가용성 영역에 걸쳐 있는 클러스터를 지원하기 위해 Azure Service Fabric은 아래 문서에 설명된 대로 두 가지 구성 방법을 제공합니다. 가용성 영역은 선택 영역에서만 사용할 수 있습니다. 자세한 내용은 [가용성 영역 개요](../availability-zones/az-overview.md)를 참조하세요.

샘플 템플릿은 [Service Fabric 교차 가용성 영역 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates)에서 사용할 수 있습니다.

## <a name="recommended-topology-for-spanning-a-primary-node-type-across-availability-zones"></a>가용성 영역에 걸쳐 있는 주 노드 형식에 권장되는 토폴로지

* 클러스터 안정성 수준이 `Platinum`로 설정됩니다
* 표준 SKU를 사용하는 단일 공용 IP 리소스
* 표준 SKU를 사용하는 단일 부하 분산 장치 리소스
* 가상 머신 확장 집합을 배포하는 서브넷에서 참조하는 NSG(네트워크 보안 그룹)

>[!NOTE]
>가상 머신 확장 집합 단일 배치 그룹 속성을 `true`로 설정해야 합니다.

다음 샘플 노드 목록은 영역에 걸쳐 있는 가상 머신 확장 집합의 FD/UD 형식을 설명합니다.

![영역에 걸쳐 있는 가상 머신 확장 집합에 있는 FD/UD 형식의 샘플 노드 목록을 보여 주는 스크린샷.][sf-multi-az-nodes]

### <a name="distribution-of-service-replicas-across-zones"></a>영역 간 서비스 복제본 배포

가용성 영역에 걸친 노드 형식에 서비스를 배포하는 경우, 별도 영역에 배치되도록 복제본이 배치됩니다. 이러한 각 노드 유형의 노드에 있는 장애 도메인은 영역 정보(즉, FD = fd:/zone1/1 등)를 사용하여 구성됩니다. 예를 들어, 5개의 복제본 또는 서비스 인스턴스의 경우, 배포는 2-2-1 이며, 런타임은 영역 간에 동일한 배포를 보장하려고 합니다.

### <a name="user-service-replica-configuration"></a>사용자 서비스 복제본 구성

가용성 영역에서 노드 형식에 배포된 상태 저장 사용자 서비스는 target = 9, min = 5 인 복제본 수와 같이 구성해야 합니다. 이 구성을 사용 하면 6개의 복제본이 계속해서 다른 두 영역에 있을 수 있으므로, 한 영역이 중단된 경우에도 서비스가 작동하도록 할 수 있습니다. 이 시나리오의 애플리케이션 업그레이드도 성공적일 것입니다.

### <a name="cluster-reliabilitylevel"></a>클러스터 ReliabilityLevel

이 값은 클러스터의 시드 노드 수와 시스템 서비스의 복제본 크기를 정의합니다. 가용성 영역 간 설정에는 영역에 걸쳐 분산되어 영역 복원력을 갖추도록 하는 많은 수의 노드가 있습니다.
  
`ReliabilityLevel` 값이 높을수록 더 많은 시드 노드와 시스템 서비스 복제본이 영역에 걸쳐 균일하게 분산되고, 영역에 오류가 발생하는 경우 클러스터와 시스템 서비스에 영향을 주지 않습니다. `ReliabilityLevel = Platinum`은 (권장) 각 영역에 세 개의 시드를 사용하여 클러스터의 영역에서 9 개의 시드 노드가 분산되도록 합니다.

### <a name="zone-down-scenario"></a>영역 다운 시나리오

영역 작동이 중단되면 해당 영역에 대한 모든 노드와 서비스 복제본이 중단된 것으로 나타납니다. 다른 영역에 복제본이 있기 때문에 서비스는 계속 응답합니다. 주 복제본은 작동하는 영역으로 장애 조치(failover)합니다. 대상 복제본 수가 아직 달성되지 않았고, VM(가상 머신) 수가 여전히 최소 대상 복제본 크기보다 높기 때문에서비스는 경고 상태로 표시됩니다.

Service Fabric 부하 분산 장치는 대상 복제본 수와 일치하도록 작업 영역의 복제본을 표시합니다. 이 시점에서 서비스는 정상 상태로 표시됩니다. 다운된 영역이 다시 가동되면 부하 분산 서비스는 모든 서비스 복제본을 영역 전체에 균등하게 분산합니다.

## <a name="upcoming-optimizations"></a>예정된 최적화
* 신뢰할 수 있는 인프라 업데이트를 제공하려면 Service Fabric 가상 머신 확장 집합 내구성을 최소한 Silver로 설정해야 합니다. 이렇게 하면 기본 가상 머신 확장 집합 및 Service Fabric 런타임에서 신뢰할 수 있는 업데이트를 제공할 수 있습니다. 또한 각 영역에 최소 5개의 VM이 있어야 합니다. 주 노드 및 기타 노드 유형에 대해 영역당 3개 및 2개의 VM으로 이 요구 사항을 축소하기 위해 노력하고 있습니다.
* 아래에 언급된 모든 구성 및 예정된 작업은 새 nodeTypes를 추가하고 이전 구성을 사용 중지하여 새 구성을 사용하도록 동일한 클러스터를 업그레이드할 수 있는 고객에게 현재 위치 마이그레이션을 제공합니다.

## <a name="networking-requirements"></a>네트워킹 요구 사항

### <a name="public-ip-and-load-balancer-resource"></a>공용 IP 및 부하 분산 장치 리소스

가상 머신 확장 집합 리소스에서 `zones` 속성을 사용하려면, 해당 가상 머신 확장 집합에서 참조하는 부하 분산 장치와 IP 리소스가 모두 표준 SKU를 사용해야 합니다. SKU 속성 없이 부하 분산 장치 또는 IP 리소스를 만들면 가용성 영역을 지원하지 않는 기본 SKU가 만들어집니다. 표준 SKU 부하 분산 장치는 기본값으로 외부에서 오는 모든 트래픽을 차단합니다. 외부 트래픽을 허용하려면 서브넷에 NSG를 배포합니다.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
```

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> 공용 IP 및 부하 분산 장치 리소스에서 SKU의 가동 중 위치 변경을 수행할 수 없습니다. 기본 SKU가 있는 기존 리소스에서 마이그레이션하는 경우 이 문서의 마이그레이션 섹션을 참조하세요.

### <a name="nat-rules-for-virtual-machine-scale-sets"></a>가상 머신 확장 집합에 대한 NAT 규칙

부하 분산 장치에 대한 인바운드 NAT(네트워크 주소 변환) 규칙은 가상 머신 확장 집합의 NAT 풀과 일치해야 합니다. 각 가상 머신 확장 집합에는 고유한 인바운드 NAT 풀이 있어야 합니다.

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="outbound-rules-for-a-standard-sku-load-balancer"></a>표준 SKU 부하 분산 장치에 대한 아웃바운드 규칙

표준 부하 분산 장치 및 공용 IP는 기본 SKU 사용과 비교할 때 아웃바운드 연결에 새로운 기능 및 서로 다른 동작을 도입합니다. 표준 SKU를 사용할 때 아웃바운드 연결을 원하는 경우, 표준 SKU 공용 IP 주소 또는 표준 SKU 부하 분산 장치를 사용하여 명시적으로 정의해야 합니다. 자세한 내용은 [아웃바운드 연결](../load-balancer/load-balancer-outbound-connections.md) 및 [Azure Load Balancer란?](../load-balancer/load-balancer-overview.md)을 참조하세요.

>[!NOTE]
> 표준 템플릿은 기본값으로 모든 아웃바운드 트래픽을 허용하는 NSG를 참조합니다. 인바운드 트래픽은 Service Fabric 관리 작업에 필요한 포트로 제한됩니다. NSG 규칙은 요구 사항에 맞게 수정할 수 있습니다.

>[!IMPORTANT]
> 표준 SKU 부하 분산 장치를 사용하는 Service Fabric 클러스터의 각 노드 유형에는 포트 443에서 아웃바운드 트래픽을 허용하는 규칙이 필요합니다. 이는 클러스터 설정을 완료하는 데 필요합니다. 이 규칙이 없는 배포는 실패합니다.


## <a name="1-preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a>1. (미리 보기) 단일 가상 머신 확장 집합에서 여러 가용성 영역 사용

이 솔루션을 사용하면 사용자가 동일한 노드 형식의 세 가용성 영역을 포괄할 수 있습니다. 이는 단일 가상 머신 확장 집합을 유지하면서 가용성 영역에 배포할 수 있도록 하는 권장되는 배포 토폴로지입니다.

> [!NOTE]
> 이 기능은 현재 미리 보기 상태이므로 현재 프로덕션 시나리오에 대해 지원되지 않습니다.

[GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure)에서 전체 샘플 템플릿을 사용할 수 있습니다.

![Azure Service Fabric 가용성 영역 아키텍처 다이어그램.][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>가상 머신 확장 집합에서 영역 구성

가상 머신 확장 집합에서 영역을 사용하도록 설정하려면 가상 머신 확장 집합 리소스에 다음 세 가지 값을 포함해야 합니다.

* 첫 번째 값은 가상 머신 확장 집합에 있는 가용성 영역을 지정하는 `zones` 속성입니다.
* 두 번째 값은 `true`로 설정해야 하는 `singlePlacementGroup` 속성입니다. 3개 가용성 영역에 걸쳐 있는 확장 집합은 `singlePlacementGroup = true`의 경우에도 300 Vm까지 확장할 수 있습니다.
* 세 번째 값은 `zoneBalance`이며, 엄격한 영역 분산을 보장합니다. 이 값은 `true`여야 합니다. 이렇게 하면 영역 간의 VM 배포가 불균형하지 않습니다. 즉, 한 영역이 중단되어도 다른 두 영역에는 클러스터를 계속 실행할 수 있는 충분한 Vm이 있습니다.

  VM 분산이 불균형한 클러스터는 해당 영역에 대부분의 Vm이 있을 수 있기 때문에 영역 다운 시나리오에서 사용하지 못할 수 있습니다. 영역에서 VM 분산이 불균형하면 서비스 배치 관련 문제가 발생하고 인프라 업데이트가 중단될 수 있습니다. 자세한 내용은 [zoneBalancing](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing)를 참조하세요.

`FaultDomain` 및 `UpgradeDomain` 재정의를 구성할 필요가 없습니다.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1", "2", "3"],
    "properties": {
        "singlePlacementGroup": "true",
        "zoneBalance": true
    }
}
```

>[!NOTE]
>
> * Service Fabric 클러스터에는 하나 이상의 주 노드 형식이 있어야 합니다. 주 노드 형식의 내구성 수준은 Silver 이상이어야 합니다.
> * 가상 머신 확장 집합을 확장하는 가용성 영역은 내구성 수준에 관계 없이 3개 이상의 가용성 영역을 구성해야 합니다.
> * Silver 이상 내구성의 가상 머신 확장 집합을 확장하는 가용성 영역에는 Vm이 15개 이상 있어야 합니다.
> * Bronze 내구성의 가상 머신 확장 집합을 확장하는 가용성 영역에는 6개 이상의 Vm이 있어야 합니다.

### <a name="enable-support-for-multiple-zones-in-the-service-fabric-node-type"></a>Service Fabric 노드 형식에서 여러 영역에 대한 지원 사용

여러 가용성 영역을 지원하려면 Service Fabric 노드 유형을 사용하도록 설정해야 합니다.

* 첫 번째 값은 `multipleAvailabilityZones`이며 노드 형식에 대해 `true`로 설정해야 합니다.
* 두 번째 값은 `sfZonalUpgradeMode`이며 선택 사항입니다. 여러 가용성 영역 있는 노드 형식이 클러스터에 이미 있는 경우에는 이 속성을 수정할 수 없습니다.
  이 속성은 UD(업그레이드 도메인)의 VM에 대한 논리적 그룹화를 제어합니다.

  * 이 값을 `Parallel`로 설정 하면 노드 형식의 VM이 UD로 그룹화되고 5개 UD에서 영역 정보를 무시합니다. 이렇게 설정 하면 모든 영역의 UD가 동시에 업그레이드됩니다. 이 배포 모드는 업그레이드 속도가 더 빠르지만 업데이트를 한 번에 한 영역에만 적용해야 한다는 SDP 지침에 위배되므로 권장되지 않습니다.
  * 이 값을 생략 하거나 `Hierarchical`로 설정 하면 VM이 최대 15UD 영역 배포를 반영하도록 그룹화됩니다. 세 영역 각각에는 5개의 UD가 있습니다. 이렇게 하면 영역을 한 번에 하나씩 업데이트하고 첫 번째 영역 내에서 5개의 UD를 완료 한 후에만 다음 영역으로 이동합니다. 이 업데이트 프로세스는 클러스터와 사용자 애플리케이션에 더 안전합니다.

  이 속성은 Service Fabric 애플리케이션 및 코드 업그레이드에 대한 업그레이드 동작만 정의합니다. 기본 가상 머신 확장 집합 업그레이드는 모든 가용성 영역에서 여전히 병렬 처리됩니다. 이 속성은 여러 영역을 사용하지 않는 노드 형식에 대한 UD 배포에 영향을 주지 않습니다.
* 세 번째 값은 `vmssZonalUpgradeMode = Parallel`입니다. 여러 가용성 영역이 있는 노드 형식을 추가하는 경우이 속성은 필수입니다. 이 속성은 모든 가용성 영역에서 한 번에 발생하는 가상 머신 확장 집합 업데이트에 대한 업그레이드 모드를 정의합니다.

  현재 이 속성은 병렬으로만 설정할 수 있습니다.

>[!IMPORTANT]
>Service Fabric 클러스터 리소스 API 버전은 2020-12-01-preview 이상이어야 합니다.
>
>클러스터 코드 버전은 8.1.321 이상이어야 합니다.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "location": "[parameters('clusterLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
    ],
    "properties": {
        "reliabilityLevel": "Platinum",
        "SFZonalUpgradeMode": "Hierarchical",
        "VMSSZonalUpgradeMode": "Parallel",
        "nodeTypes": [
          {
                "name": "[parameters('vmNodeType0Name')]",
                "multipleAvailabilityZones": true,
          }
        ]
}
```

>[!NOTE]
>
> * 공용 IP 및 부하 분산 리소스는 문서의 앞부분에서 설명한 표준 SKU를 사용해야 합니다.
> * 노드 형식의 `multipleAvailabilityZones` 속성은 노드 형식이 만들어지고 나중에 수정할 수 없는 경우에만 정의할 수 있습니다. 기존 노드 형식은 이 속성으로 구성할 수 없습니다.
> * `sfZonalUpgradeMode`을 생략 하거나 `Hierarchical`로 설정 하면 클러스터에 업그레이드 도메인이 더 있으므로 클러스터 및 애플리케이션 배포 속도가 느려집니다. 업그레이드 정책 시간 제한을 올바르게 조정하여 15개의 업그레이드 도메인에 필요한 업그레이드 시간을 고려하는 것이 중요합니다. 배포가 Azure Resource Service 배포 제한인 12시간을 초과하지 않도록 앱과 클러스터 모두에 대한 업그레이드 정책을 업데이트해야 합니다. 즉, 배포는 15개의 UD에 대해 12시간 이상 걸리지 않아야 합니다(즉, 각 UD에 대해 40분 이상 걸리지 않아야 합니다).
> * 클러스터 안정성 수준을 `Platinum`로 설정하여 클러스터가 하나의 영역 중단 시나리오에서 유지되도록 합니다.

>[!TIP]
> `sfZonalUpgradeMode`을 `Hierarchical`로 설정하거나 생략하는 것이 좋습니다. 배포는 더 적은 양의 복제본 및 인스턴스에 영향을 주는 VM의 영역 배포를 따르므로 더 안전하게 만들 수 있습니다.
> 배포 속도가 우선 순위이거나 여러 가용성 영역에 있는 노드 유형에서 상태 비지정 워크로드만 실행되는 경우 `Parallel`로 설정된 `sfZonalUpgradeMode`을 사용합니다. 이로 인해 UD 워크가 모든 가용성 영역에서 병렬로 발생합니다.

### <a name="migrate-to-the-node-type-with-multiple-availability-zones"></a>여러 가용성 영역을 사용하여 노드 형식으로 마이그레이션

모든 마이그레이션 시나리오의 경우 여러 가용성 영역을 지원하는 새 노드 유형을 추가해야 합니다. 여러 영역을 지원하기 위해 기존 노드 유형을 마이그레이션할 수 없습니다.
[Service Fabric 클러스터 주 노드 유형 스케일 업](./service-fabric-scale-up-primary-node-type.md) 문서에는 새 노드 유형 및 새 노드 유형에 필요한 기타 리소스(예: IP 및 부하 분산 장치 리소스)를 추가하는 자세한 단계가 포함되어 있습니다. 또한 이 문서에서는 여러 가용성 영역 있는 새 노드 형식이 클러스터에 추가된 후 기존 노드 형식을 사용하지 않는 방법을 설명합니다.

* 기본 부하 분산 장치 및 IP 리소스를 사용하는 노드 유형에서 마이그레이션: 이 프로세스는 가용성 영역당 하나의 노드 유형이 있는 솔루션에 대한 [아래의 하위 섹션](#migrate-to-availability-zones-from-a-cluster-by-using-a-basic-sku-load-balancer-and-a-basic-sku-ip)에 설명되어 있습니다.

  새 노드 형식의 경우, 유일한 차이점은 가용성 영역마다 하나씩이 아닌 모든 가용성 영역 대해 하나의 가상 머신 확장 집합과 하나의 노드 유형만 있다는 것입니다.
* NSG를 사용하여 표준 SKU 부하 분산 장치 및 IP 리소스를 사용하는 노드 형식에서의 마이그레이션은 앞에서 설명한 것과 동일한 절차를 따릅니다. 그러나 새 부하 분산 장치, IP 및 NSG 리소스를 추가할 필요는 없습니다. 동일한 리소스를 새 노드 형식에서 재사용할 수 있습니다.


## <a name="2-deploy-zones-by-pinning-one-virtual-machine-scale-set-to-each-zone"></a>2. 각 영역에 하나의 가상 머신 확장 집합을 고정하여 영역 배포

지금은 일반적으로 사용할 수 있는 구성입니다.
가용성 영역 Service Fabric 클러스터를 확장하려면 해당 지역에서 지원하는 각 가용성 영역에 주 노드 유형을 만들어야 합니다. 이렇게 하면 시드 노드가 각 주 노드 형식에 균등하게 분산됩니다.

주 노드 형식에 권장되는 토폴로지에는 아래에 설명된 리소스가 필요합니다.
* 주로 표시된 세 가지 노드 형식
  * 각 노드 형식은 다른 영역에 있는 자체 가상 머신 확장 집합에 매핑되어야 합니다.
  * 각 가상 머신 확장 집합에는 5개 이상의 노드(실버 내구성)가 있어야 합니다.

다음 다이어그램에서는 Azure Service Fabric 가용성 영역 아키텍처를 보여줍니다.

![Azure Service Fabric 가용성 영역 아키텍처를 보여 주는 다이어그램.][sf-architecture]

### <a name="enable-zones-on-a-virtual-machine-scale-set"></a>가상 머신 확장 집합에서 영역 사용

영역을 사용하도록 설정하려면 가상 머신 확장 집합에서 가상 머신 확장 집합 리소스에 다음 세 가지 값을 포함해야 합니다.

* 첫 번째 값은 가상 머신 확장 집합을 배포할 가용성 영역을 지정하는 `zones` 속성입니다.
* 두 번째 값은 `true`로 설정해야 하는 `singlePlacementGroup` 속성입니다.
* 세 번째 값은 Service Fabric 가상 머신 확장 집합 확장의 `faultDomainOverride` 속성입니다. 이 속성에는 이 가상 머신 확장 집합이 배치될 영역만 포함되어야 합니다. 예: `"faultDomainOverride": "az1"`. Azure Service Fabric 클러스터는 지역 간 지원이 없기 때문에 모든 가상 머신 확장 집합 리소스는 동일한 지역에 배치되어야 합니다.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enable-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Service Fabric 클러스터 리소스에서 여러 주 노드 형식 사용

클러스터 리소스에서 하나 이상의 노드 형식을 주로 설정하려면 `isPrimary` 속성을 `true`로 설정합니다. 가용성 영역에서 Service Fabric 클러스터를 배포하는 경우 고유한 영역에 3개의 노드 형식이 있어야 합니다.

```json
{
    "reliabilityLevel": "Platinum",
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-availability-zones-from-a-cluster-by-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>기본 SKU 부하 분산 장치 및 기본 SKU IP를 사용하는 클러스터에서 가용성 영역을 사용하도록 마이그레이션

기본 SKU와 함께 Load Balancer 및 IP를 사용하던 클러스터를 마이그레이션하려면 먼저 표준 SKU를 사용하여 완전히 새로운 부하 분산 장치 및 IP 리소스를 만들어야 합니다. 이러한 리소스는 업데이트할 수 없습니다.

새 부하 분산 장치 및 사용 하려는 새 교차 가용성 영역 노드 형식에서 IP를 참조합니다. 위의 예에서 세 개의 새로운 가상 머신 확장 집합 리소스가 영역 1, 2 및 3에 추가되었습니다. 이러한 가상 머신 확장 집합은 새로 생성된 부하 분상 장치 및 IP를 참조하고 Service Fabric 클러스터 리소스에서 주 노드 형식으로 표시됩니다.

1. 시작하려면 기존 Azure Resource Manager 템플릿에 새 리소스를 추가합니다. 해당 리소스는 다음과 같습니다.

   * 표준 SKU를 사용하는 공용 IP 리소스
   * 표준 SKU를 사용하는 부하 분산 장치
   * 가상 머신 확장 집합을 배포하는 서브넷에서 참조하는 NSG
   * 주로 표시된 세 가지 노드 형식
     * 각 노드 형식은 다른 영역에 있는 자체 가상 머신 확장 집합에 매핑되어야 합니다.
     * 각 가상 머신 확장 집합에는 5개 이상의 노드(실버 내구성)가 있어야 합니다.

   이러한 리소스의 예는 [샘플 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)에서 찾을 수 있습니다.

   ```powershell
   New-AzureRmResourceGroupDeployment `
       -ResourceGroupName $ResourceGroupName `
       -TemplateFile $Template `
       -TemplateParameterFile $Parameters
   ```

1. 리소스 배포가 완료되면, 원래 클러스터에서 주 노드 유형의 노드를 사용하지 않도록 설정할 수 있습니다. 노드를 사용하지 않도록 설정하면 시스템 서비스는 이전에 배포한 새 주 노드 유형으로 마이그레이션됩니다.

   ```powershell
   Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
       -KeepAliveIntervalInSec 10 `
       -X509Credential `
       -ServerCertThumbprint $thumb  `
       -FindType FindByThumbprint `
       -FindValue $thumb `
       -StoreLocation CurrentUser `
       -StoreName My 

   Write-Host "Connected to cluster"

   $nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

   Write-Host "Disabling nodes..."
   foreach($name in $nodeNames) {
       Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
   }
   ```

1. 노드를 모두 사용하지 않도록 설정하면 시스템 서비스는 영역에 분산되는 주 노드 형식에서 실행됩니다. 그런 다음 클러스터에서 사용하지 않도록 설정된 노드를 제거할 수 있습니다. 노드가 제거되면 원래 IP, 부하 분산 장치 및 가상 머신 확장 집합 리소스를 제거할 수 있습니다.

   ```powershell
   foreach($name in $nodeNames){
       # Remove the node from the cluster
       Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
       Write-Host "Removed node state for node $name"
   }

   $scaleSetName="nt0"
   Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

   $lbname="LB-cluster-nt0"
   $oldPublicIpName="LBIP-cluster-0"
   $newPublicIpName="LBIP-cluster-1"

   Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
   Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
   ```

1. 그런 다음 배포된 Resource Manager 템플릿에서 이러한 리소스에 대한 참조를 제거합니다.

1. 마지막으로 DNS 이름 및 공용 IP를 업데이트합니다.

   ```powershell
   $oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
   $primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
   $primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

   Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
   Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

   $PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
   $PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
   $PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
   Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP
 
   ```

[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
[sf-multi-az-nodes]: ./media/service-fabric-cross-availability-zones/sf-multi-az-nodes.png
