---
title: 노드 형식 관리 되는 데이터 디스크 Service Fabric 배포
description: 연결 된 관리 되는 데이터 디스크를 사용 하 여 Service fabric 노드 유형을 만들고 배포 하는 방법을 알아봅니다.
author: craftyhouse
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: micraft
ms.openlocfilehash: 11660a3bb10e7004ce78d2788f93ac6fc2fc821d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053052"
---
# <a name="deploy-an-azure-service-fabric-cluster-node-type-with-managed-data-diskspreview"></a>관리 되는 데이터 디스크를 사용 하 여 Azure Service Fabric 클러스터 노드 형식 배포 (미리 보기)

기본적으로 노드 형식 Service Fabric 데이터 저장소에 대 한 기본 가상 머신 확장 집합의 각 VM에서 임시 디스크를 사용 합니다. 그러나 임시 디스크는 영구적이 지 않으며 임시 디스크의 크기는 지정 된 VM SKU에 바인딩되기 때문에 일부 시나리오에서는 너무 제한적일 수 있습니다. Azure Managed Disks 고객이 영구적 데이터 디스크를 사용 하는 경우 VM SKU와 별도로 노드 형식에 대해 사용 되는의 크기와 성능을 지정할 수 있습니다. 다음 문서에서는 Azure Managed Disks를 구성 하 고 기본 데이터 경로로 사용 하기 위해 Service fabric의 기본 지원을 사용 하는 단계를 제공 합니다. Service Fabric는 노드 유형 생성 시 Azure Managed Disks를 자동으로 구성 하 고 vm 또는 가상 머신 확장 집합이 이미지로 다시 설치는 상황을 처리 합니다.

* 관리 되는 데이터 디스크에 필요한 최소 디스크 크기는 50 GB입니다.
* 관리 되는 데이터 디스크가 두 개 이상 연결 된 시나리오에서 고객은 자체적으로 데이터 디스크를 관리 해야 합니다.

## <a name="configuring-virtual-machine-scale-set-to-use-managed-data-disks-in-service-fabric"></a>Service fabric에서 관리 되는 데이터 디스크를 사용 하도록 가상 머신 확장 집합 구성
노드 유형에 서 Azure Managed Disks를 사용 하려면 다음을 사용 하 여 기본 가상 머신 확장 집합 리소스를 구성 합니다.

* 가상 머신 확장 집합에 대 한 템플릿의 데이터 디스크 섹션에 관리 디스크를 추가 합니다. 
* 다음 설정으로 Service fabric 확장을 업데이트 합니다. 
    * Windows- **useManagedDataDisk: true** 및 **데이터 경로: ' K: \\ \\ SvcFab '** .  드라이브 문자 "K"는 단지 표현으로, 가상 머신 확장 집합 SKU에 있는 모든 드라이브 문자 보다 큰 모든 드라이브 문자 사전순으로 수 있습니다.
    * Linux- **useManagedDataDisk: true** 및 **데이터 경로: ' \mnt\sfdataroot '** .

>[!NOTE]
> linux Service Fabric 클러스터에 대 한 관리 되는 데이터 디스크에 대 한 지원은 현재 프로덕션에 사용할 수 없지만, 고객은 linux 용 테스트 Service Fabric 확장을 사용 하 여 사용해 볼 수 있습니다.

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

## <a name="migrate-to-using-managed-data-disks-for-service-fabric-node-types"></a>Service fabric 노드 형식에 대 한 관리 되는 데이터 디스크를 사용 하도록 마이그레이션
* 모든 마이그레이션 시나리오에서 위에 지정 된 대로 관리 되는 데이터 디스크를 사용 하는 새 노드 유형을 추가 해야 합니다.
* 새 노드 형식을 추가 하면 워크 로드를 새 노드 형식으로 마이그레이션합니다.
* 리소스 배포가 완료되면 원래 클러스터에서 제거하려는 노드 형식의 노드를 사용하지 않도록 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계 
* [Service Fabric 설명은](service-fabric-reliable-services-introduction.md)
* [노드 유형과 가상 머신 확장 집합](service-fabric-cluster-nodetypes.md)
* [용량 계획 Service Fabric](service-fabric-best-practices-capacity-scaling.md)
