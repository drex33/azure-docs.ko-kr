---
title: Service Fabric 노드 유형 관리 데이터 디스크 배포
description: 연결된 관리 데이터 디스크를 사용하여 Service Fabric 노드 형식을 만들고 배포하는 방법을 알아봅니다.
author: craftyhouse
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: micraft
ms.openlocfilehash: 17ec57ca04defa61080ebb5a67f879c737ea16fa
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131460396"
---
# <a name="deploy-an-azure-service-fabric-cluster-node-type-with-managed-data-diskspreview"></a>관리되는 데이터 디스크를 Service Fabric 클러스터 노드 유형 배포(미리 보기)

>[!NOTE]
> 관리되는 데이터 디스크에 대한 지원은 현재 미리 보기에만 있으며 프로덕션 워크로드에서 사용하면 안 됩니다.


Service Fabric 노드 유형은 기본적으로 데이터 스토리지에 대한 기본 가상 머신 확장 집합의 각 VM에서 임시 디스크를 사용합니다. 그러나 임시 디스크는 영구적이 아니며 임시 디스크의 크기는 지정된 VM SKU에 바인딩되므로 일부 시나리오에서는 너무 제한적일 수 있습니다. Azure Managed Disks 고객은 영구 데이터 디스크를 사용하여 VM SKU와 별도로 노드 유형에 사용할 크기 및 성능을 지정할 수 있습니다. 다음 문서에서는 Service Fabric의 기본 지원을 사용하여 Azure Managed Disks 기본 데이터 경로로 구성하고 사용하는 단계를 제공합니다. Service Fabric 노드 형식 생성 시 Azure Managed Disks 자동으로 구성하고 VM 또는 가상 머신 확장 집합이 이미지로 다시 설치되는 상황을 처리합니다.

* 관리되는 데이터 디스크에 필요한 최소 디스크 크기는 50GB입니다.
* 관리되는 데이터 디스크가 두 개 이상 연결된 시나리오에서는 고객이 데이터 디스크를 직접 관리해야 합니다.

## <a name="configuring-virtual-machine-scale-set-to-use-managed-data-disks-in-service-fabric"></a>Service Fabric에서 관리되는 데이터 디스크를 사용하도록 가상 머신 확장 집합 구성
노드 형식에서 Azure Managed Disks 사용하려면 다음을 사용하여 기본 가상 머신 확장 집합 리소스를 구성합니다.

* 가상 머신 확장 집합에 대한 템플릿의 데이터 디스크 섹션에 관리 디스크를 추가합니다. 
* 다음 설정으로 Service Fabric 확장을 업데이트합니다. 
    * Windows 경우 **useManagedDataDisk: true** 및 **dataPath: 'K: \\ \\ SvcFab'** .  드라이브 문자 "K"는 단지 표현일 뿐이며, 가상 머신 확장 집합 SKU에 있는 모든 드라이브 문자보다 사전적으로 더 큰 드라이브 문자일 수 있습니다.
    * Linux의 경우 **useManagedDataDisk:true** 및 **dataPath: '\mnt\sfdataroot'** .

>[!NOTE]
> Linux Service Fabric 클러스터에 대한 관리 데이터 디스크에 대한 지원은 현재 사용할 수 없습니다.

Service Fabric 확장 Azure Resource Manager 템플릿
```json
{
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
                            "dataPath": "K:\\\\SvcFab",
                            "useManagedDataDisk": true,
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
            ]
        },
        "storageProfile": 
        {
            "datadisks": [
                {
                    "lun": "1",
                    "createOption": "empty",
                    "diskSizeGB": "100",
                    "managedDisk": { "storageAccountType": "Standard_LRS" }
                }
            ]
        }
    }
}
```

## <a name="migrate-to-using-managed-data-disks-for-service-fabric-node-types"></a>Service Fabric 노드 유형에 대해 관리되는 데이터 디스크를 사용하도록 마이그레이션
* 모든 마이그레이션 시나리오의 경우 위에서 지정한 대로 관리되는 데이터 디스크를 사용하는 새 노드 유형을 추가해야 합니다.
* 새 노드 형식이 추가되면 워크로드를 새 노드 형식으로 마이그레이션합니다.
* 리소스 배포가 완료되면 원래 클러스터에서 제거하려는 노드 형식의 노드를 사용하지 않도록 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계 
* [Service Fabric 개요](service-fabric-reliable-services-introduction.md)
* [노드 유형과 가상 머신 확장 집합](service-fabric-cluster-nodetypes.md)
* [Service Fabric 용량 계획](service-fabric-best-practices-capacity-scaling.md)
