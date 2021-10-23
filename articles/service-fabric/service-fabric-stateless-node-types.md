---
title: Service Fabric 클러스터에 상태 비저장 전용 노드 유형 배포
description: Azure Service fabric 클러스터에서 상태 비저장 노드 유형을 만들고 배포하는 방법에 대해 알아봅니다.
author: peterpogorski
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: pepogors
ms.openlocfilehash: 9670e4629a55888e966e815517f561432976f09a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261541"
---
# <a name="deploy-an-azure-service-fabric-cluster-with-stateless-only-node-types"></a>상태 비저장 전용 노드 유형을 사용하여 Azure Service Fabric 클러스터 배포
Service Fabric 노드 유형은 특정 시점에 상태 저장 서비스가 노드에 배치될 수 있다는 가정 하에 제공됩니다. 상태 비저장 노드 유형은 노드 유형에 대한 이 가정을 변경하므로 노드 유형이 더 빠른 스케일 아웃 작업, Bronze 내구성에서 자동 OS 업그레이드 지원 및 단일 가상 머신 확장 집합에서 100개 이상의 노드로 확장과 같은 다른 기능을 사용할 수 있습니다.

* 주 노드 유형은 상태 비저장으로 구성할 수 없음
* 상태 비저장 노드 유형은 Bronze 내구성 수준에서만 지원됨
* 상태 비저장 노드 유형은 Service Fabric Runtime 버전 7.1.409 이상에서만 지원됩니다.


사용 가능한 샘플 템플릿: [Service Fabric 상태 비저장 노드 유형 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-2-NodeTypes-Windows-Stateless-Secure)

## <a name="enabling-stateless-node-types-in-service-fabric-cluster"></a>Service Fabric 클러스터에서 상태 비저장 노드 유형 사용
클러스터 리소스에서 하나 이상의 노드 유형을 상태 비저장으로 설정하려면 **isStateless** 속성을 **true** 로 설정합니다. 상태 비저장 노드 유형을 사용하여 Service Fabric 클러스터를 배포하는 경우 클러스터 리소스에 하나 이상의 주 노드 유형을 포함해야 합니다.

* Service Fabric 클러스터 리소스 apiVersion은 ‘2020-12-01-preview’ 이상이어야 합니다.

```json
{
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
        "isStateless": false, // Primary Node Types cannot be stateless
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Bronze",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": false,
        "isStateless": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    }    
    ],
}
```

## <a name="configuring-virtual-machine-scale-set-for-stateless-node-types"></a>상태 비저장 노드 유형에 대한 가상 머신 확장 집합 구성
상태 비저장 노드 유형을 사용하려면 다음과 같은 방법으로 기본 가상 머신 확장 집합 리소스를 구성해야 합니다.

* VM을 100개 이상 확장해야 하는 경우 **false** 로 설정해야 하는 **singlePlacementGroup** 속성 값입니다.
* 확장 집합의 **upgradeMode** 는 **Rolling** 으로 설정되어야 합니다.
* 롤링 업그레이드 모드를 사용하려면 애플리케이션 상태 확장 또는 상태 프로브를 구성해야 합니다. 상태 프로브 또는 애플리케이션 상태 확장을 구성하는 방법에 대한 자세한 내용은 이 [문서를 참조하세요.](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md#how-does-automatic-os-image-upgrade-work) 아래 제안된 상태 비지정 노드 형식에 대한 기본 구성으로 상태 프로브를 구성합니다. 애플리케이션이 노드 유형에 배포되면 상태 프로브/상태 확장 포트를 변경하여 실제 애플리케이션 상태를 모니터링할 수 있습니다.

>[!NOTE]
> 상태 비저장 노드 유형과 함께 자동 크기 조정을 사용하는 동안 스케일 다운 작업 후 노드 상태가 자동으로 정리되지 않습니다. 자동 크기 조정 중에 다운 노드의 NodeState를 정리하기 위해 [Service Fabric 자동 크기 조정 도우미](https://github.com/Azure/service-fabric-autoscale-helper)를 사용하는 것이 좋습니다.

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
          "mode": "Rolling",
          "automaticOSUpgradePolicy": {
            "enableAutomaticOSUpgrade": true
          }
        },
        "platformFaultDomainCount": 5
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
            "durabilityLevel": "Bronze",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
        },
        "typeHandlerVersion": "1.1"
    }
    },
    {
        "type": "extensions",
        "name": "HealthExtension",
        "properties": {
            "publisher": "Microsoft.ManagedServices",
            "type": "ApplicationHealthWindows",
            "autoUpgradeMinorVersion": true,
            "typeHandlerVersion": "1.0",
            "settings": {
            "protocol": "tcp",
            "port": "19000"
            }
            }
        },
    ]
}
```

## <a name="configuring-stateless-node-types-with-multiple-availability-zones"></a>여러 가용성 영역이 있는 상태 비저장 노드 유형 구성
여러 가용성 영역에 걸쳐 있는 상태 비저장 노드 유형을 구성하려면 다음과 같은 몇 가지 변경 사항과 함께 [여기](./service-fabric-cross-availability-zones.md#1-preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set) 문서를 따르세요.

* 여러 배치 그룹을 사용하도록 설정해야 하는 경우 **singlePlacementGroup**: **false** 를 설정합니다.
* 위에서 언급한 대로 **upgradeMode**: **Rolling** 을 설정하고 애플리케이션 상태 확장/상태 프로브를 추가합니다.
* 가상 머신 확장 집합에 대해 **platformFaultDomainCount**: **5** 를 설정합니다.

>[!NOTE]
> 클러스터에 구성된 VMSSZonalUpgradeMode와 관계없이 가상 머신 확장 집합 업데이트는 롤링 업그레이드 모드를 사용하므로 여러 영역에 걸쳐 있는 상태 비저장 노드 유형에 대해 항상 한 번에 하나의 가용성 영역에서 순차적으로 발생합니다.

참고로 여러 가용성 영역이 있는 상태 비저장 노드 유형을 구성하기 위한 [템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-2-NodeTypes-Windows-Stateless-CrossAZ-Secure)을 참조하세요.

## <a name="networking-requirements"></a>네트워킹 요구 사항
### <a name="public-ip-and-load-balancer-resource"></a>공용 IP 및 Load Balancer 리소스
가상 머신 확장 집합 리소스에서 VM을 100개 이상 확장하려면 해당 가상 머신 확장 집합에서 참조하는 부하 분산 장치와 IP 리소스가 모두 *표준* SKU를 사용해야 합니다. SKU 속성 없이 부하 분산 장치 또는 IP 리소스를 만들면 100개 이상의 VM 확장을 지원하지 않는 기본 SKU가 만들어집니다. 표준 SKU 부하 분산 장치는 기본적으로 외부의 모든 트래픽을 차단합니다. 외부 트래픽을 허용하려면 서브넷에 NSG를 배포해야 합니다.

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
> 공용 IP 및 부하 분산 장치 리소스에서 SKU 내부 변경을 수행할 수 없습니다. 

### <a name="virtual-machine-scale-set-nat-rules"></a>가상 머신 확장 집합 NAT 규칙
부하 분산 장치 인바운드 NAT 규칙은 가상 머신 확장 집합의 NAT 풀과 일치해야 합니다. 각 가상 머신 확장 집합에는 고유한 인바운드 NAT 풀이 있어야 합니다.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>표준 SKU Load Balancer 아웃바운드 규칙
표준 Load Balancer 및 표준 공용 IP는 기본 SKU 사용과 비교할 때 아웃바운드 연결에 새로운 기능 및 서로 다른 동작을 도입합니다. 표준 SKU로 작업하는 경우 아웃바운드 연결을 하려는 경우 표준 공용 IP 주소 또는 표준 공용 Load Balancer를 사용하여 명시적으로 정의해야 합니다. 자세한 내용은 [아웃바운드 연결](../load-balancer/load-balancer-outbound-connections.md) 및 [Azure 표준 Load Balancer](../load-balancer/load-balancer-overview.md)를 참조하세요.

>[!NOTE]
> 표준 템플릿은 기본적으로 모든 아웃바운드 트래픽을 허용하는 NSG를 참조합니다. 인바운드 트래픽은 Service Fabric 관리 작업에 필요한 포트로 제한됩니다. NSG 규칙은 요구 사항에 맞게 수정할 수 있습니다.

>[!NOTE]
> 표준 SKU SLB를 사용하는 Service Fabric 클러스터는 각 노드 형식에 포트 443에서 아웃바운드 트래픽을 허용하는 규칙이 있는지 확인해야 합니다. 이는 클러스터 설정을 완료하는 데 필요하며, 이러한 규칙이 없는 모든 배포는 실패합니다.



## <a name="migrate-to-using-stateless-node-types-in-a-cluster"></a>클러스터에서 상태 비저장 노드 유형을 사용하도록 마이그레이션
모든 마이그레이션 시나리오에 대해 새로운 상태 비저장 전용 노드 유형을 추가해야 합니다. 기존 노드 유형은 상태 비저장 전용으로 마이그레이션할 수 없습니다.

기본 SKU와 함께 Load Balancer 및 IP를 사용하던 클러스터를 마이그레이션하려면 먼저 표준 SKU를 사용하여 완전히 새로운 부하 분산 장치 및 IP 리소스를 만들어야 합니다. 이러한 리소스는 내부에서 업데이트할 수 없습니다.

새 LB 및 IP는 사용하려는 새 상태 비저장 노드 유형에서 참조되어야 합니다. 위의 예제에서는 상태 비저장 노드 형식에 사용할 새 가상 머신 확장 집합 리소스를 추가합니다. 이러한 가상 머신 확장 집합은 새로 생성된 LB 및 IP를 참조하고 Service Fabric 클러스터 리소스에서 상태 비저장 노드 유형으로 표시됩니다.

시작하려면 기존 Resource Manager 템플릿에 새 리소스를 추가해야 합니다. 해당 리소스는 다음과 같습니다.
* 표준 SKU를 사용하는 공용 IP 리소스
* 표준 SKU를 사용하는 부하 분산 장치 리소스
* 가상 머신 확장 집합을 배포하는 서브넷에서 참조하는 NSG입니다.

리소스 배포가 완료되면 원래 클러스터에서 제거하려는 노드 형식의 노드를 사용하지 않도록 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계 
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [노드 유형과 가상 머신 확장 집합](service-fabric-cluster-nodetypes.md)
