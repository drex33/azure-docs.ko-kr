---
title: Service Fabric 관리형 클러스터 노드 유형 구성 또는 수정
description: 이 문서에서는 관리되는 클러스터 노드 유형을 수정하는 방법을 안내합니다.
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: 2f32b67ce20f0b8abc19eb1a68445817976d38e3
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132936322"
---
# <a name="service-fabric-managed-cluster-node-types"></a>관리형 클러스터 노드 유형 Service Fabric

Service Fabric 관리형 클러스터의 각 노드 형식은 가상 머신 확장 집합을 통해 백업됩니다. 관리형 클러스터를 사용하면 Service Fabric 관리형 클러스터 리소스 공급자를 통해 필요한 변경을 수행해야 합니다. 클러스터에 대한 모든 기본 리소스는 관리형 클러스터 공급자가 대신 만들고 추상화합니다. 리소스 공급자가 리소스를 관리하도록 하면 클러스터 노드 유형 배포 및 관리를 간소화하고, 시드 노드 삭제와 같은 작업 오류를 방지하고, VM SKU 유효성 검사와 같은 모범 사례를 적용하는 것이 안전합니다.

이 문서의 나머지 부분에는 노드 유형 만들기, 노드 유형 인스턴스 수 조정, 자동 OS 이미지 업그레이드 사용, OS 이미지 변경 및 배치 속성 구성에서 다양한 설정을 조정하는 방법을 설명합니다. 또한 이 문서에서는 Azure Portal 또는 Azure Resource Manager 템플릿을 사용하여 변경하는 방법을 중점 설명합니다.

> [!NOTE]
> 변경이 진행되는 동안에는 노드 유형을 수정할 수 없습니다. 다른 작업을 수행하기 전에 요청된 변경이 완료되도록 하는 것이 좋습니다.


## <a name="add-or-remove-a-node-type-with-portal"></a>포털을 통해 노드 유형 추가 또는 제거

이 연습에서는 포털을 사용하여 노드 유형을 추가하거나 제거하는 방법을 알아봅니다.

**노드 유형을 추가하려면 다음을 수행합니다.**
1) [Azure Portal](https://portal.azure.com/) 로그인

2) 클러스터 리소스 개요 페이지로 이동합니다. 
![샘플 개요 페이지][overview]

3) 노드 형식 보기 섹션 아래에서 를 `Node types` `Settings` ![ 선택합니다.][addremove]

4) 위쪽을 클릭하고 `Add` 필요한 정보를 입력한 다음 아래쪽에서 추가를 클릭합니다.


**노드 유형을 제거하려면 다음을 수행합니다.**
1) [Azure Portal](https://portal.azure.com/) 로그인

2) 클러스터 리소스 개요 페이지로 이동합니다. 
![샘플 개요 페이지][overview]

3) 노드 형식 보기 섹션 아래에서 를 `Node types` `Settings` ![ 선택합니다.][addremove]

4) 제거할 를 선택하고 `Node Type` `Delete` 위쪽을 클릭합니다.

> [!NOTE]
> 클러스터의 유일한 주 노드 유형인 경우 주 노드 유형을 제거할 수 없습니다.


## <a name="add-a-node-type-with-a-template"></a>템플릿을 통해 노드 형식 추가

**ARM 템플릿을 사용하여 노드 형식을 추가하려면**

필요한 값을 사용하여 다른 리소스 종류를 `Microsoft.ServiceFabric/managedclusters/nodetypes` 추가하고 설정을 적용하기 위해 클러스터 배포를 수행합니다.

* Service Fabric 관리형 클러스터 리소스 apiVersion은 **2021-05-01** 이상이어야 합니다.

```json
          {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeType2Name'))]",
            "location": "[resourcegroup().location]",
            "dependsOn": [
              "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
            ],
            "properties": {
                "isPrimary": false,
                "vmImagePublisher": "[parameters('vmImagePublisher')]",
                "vmImageOffer": "[parameters('vmImageOffer')]",
                "vmImageSku": "[parameters('vmImageSku')]",
                "vmImageVersion": "[parameters('vmImageVersion')]",
                "vmSize": "[parameters('nodeType2VmSize')]",
                "vmInstanceCount": "[parameters('nodeType2VmInstanceCount')]",
                "dataDiskSizeGB": "[parameters('nodeType2DataDiskSizeGB')]",
                "dataDiskType": "[parameters('nodeType2managedDataDiskType')]"
           }
```
두 노드 형식 구성 예제는 [샘플 두 노드 형식 ARM 템플릿을 참조하세요.](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/SF-Managed-Standard-SKU-2-NT)


## <a name="scale-a-node-type-manually-with-portal"></a>포털을 통해 수동으로 노드 유형 크기 조정

이 연습에서는 포털을 사용하여 노드 유형에 대한 노드 수를 수정하는 방법을 알아봅니다.

1) [Azure Portal](https://portal.azure.com/) 로그인

2) 클러스터 리소스 개요 페이지로 이동합니다. 
![샘플 개요 페이지][overview]

3) 섹션 아래에서 를 `Node Types` 선택합니다. `Settings` 

4) `Node type name`수정하려는 을 선택합니다.

5) 를 `Node count` 원하는 새 값으로 조정하고 `Apply` 아래쪽에서 선택합니다. 이 스크린샷에서 값은 `3` 이고 로 `5` 조정되었습니다.
![노드 수 증가를 보여주는 샘플][adjust-node-count]

6) 이제 는 `Provisioning state` `Updating` 완료될 때까지의 상태를 표시합니다. 완료되면 `Succeeded` 다시 표시됩니다.
![노드 형식 업데이트를 보여주는 샘플][node-type-updating]


## <a name="scale-a-node-type-manually-with-a-template"></a>템플릿을 통해 수동으로 노드 형식 크기 조정

ARM 템플릿을 사용하여 노드 형식의 노드 수를 조정하려면 `vmInstanceCount` 속성을 새 값으로 조정하고 클러스터 배포를 수행하여 설정을 적용합니다.

* Service Fabric 관리형 클러스터 리소스 apiVersion은 **2021-05-01** 이상이어야 합니다.

> [!NOTE]
> 관리형 클러스터 공급자는 크기 조정을 차단하고 크기 조정 요청이 필요한 최소값을 위반하는 경우 오류를 반환합니다.

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                ...
                "vmInstanceCount": "[parameters('nodeTypeVmInstanceCount')]",
                ...
            }
        }
}
```

## <a name="enable-automatic-os-image-upgrades"></a>자동 OS 이미지 업그레이드 사용

관리 클러스터 노드를 실행하는 가상 머신으로 자동 OS 이미지 업그레이드를 사용하도록 선택할 수 있습니다. 가상 머신 확장 집합 리소스는 Service Fabric 관리 클러스터를 대신하여 관리되지만 클러스터 노드에 대해 자동 OS 이미지 업그레이드를 사용하도록 설정하는 것이 좋습니다. [클래식 서비스 패브릭](service-fabric-best-practices-infrastructure-as-code.md#virtual-machine-os-automatic-upgrade-configuration) 클러스터와 마찬가지로 관리 클러스터 노드는 클러스터에 대한 의도하지 않은 중단을 방지하기 위해 기본적으로 업그레이드되지 않습니다.

자동 OS 업그레이드를 사용하도록 설정하려면:

* *Microsoft.ServiceFabric/managedclusters* 및 *Microsoft.ServiceFabric/managedclusters/nodetypes* 리소스의 `2021-05-01` 이상 버전을 사용합니다.
* 클러스터의 속성 `enableAutoOSUpgrade`를 *true* 로 설정합니다.
* 클러스터 nodeTypes의 리소스 속성 `vmImageVersion`을 *latest* 로 설정합니다.

예를 들면 다음과 같습니다.

```json
    {
      "apiVersion": "2021-05-01",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-05-01",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

사용하도록 설정되면 Service Fabric은 관리형 클러스터에서 OS 이미지 버전을 쿼리하고 추적하기 시작합니다. 새 OS 버전을 사용할 수 있는 경우 클러스터 노드 유형(가상 머신 확장 집합)이 한 번에 하나씩 업그레이드됩니다. Service Fabric 런타임 업그레이드는 진행 중인 클러스터 노드 OS 이미지 업그레이드가 없음을 확인한 후에만 수행됩니다.

업그레이드가 실패하면 Service Fabric은 24시간 후에 최대 3번의 다시 시도를 위해 다시 시도합니다. 클래식(관리되지 않는) Service Fabric 업그레이드와 마찬가지로 비정상 앱 또는 노드가 OS 이미지 업그레이드를 차단할 수 있습니다.

이미지 업그레이드에 대한 자세한 내용은 [Azure Virtual Machine Scale Sets를 사용한 자동 OS 이미지 업그레이드](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)를 참조하세요.

## <a name="modify-the-os-image-for-a-node-type-with-portal"></a>포털을 통해 노드 유형에 대한 OS 이미지 수정

이 연습에서는 포털을 사용하여 노드 유형에 대한 OS 이미지를 수정하는 방법을 알아봅니다.

1) [Azure Portal](https://portal.azure.com/) 로그인

2) 클러스터 리소스 개요 페이지로 이동합니다. 
![샘플 개요 페이지][overview]

3) 섹션 아래에서 를 `Node Types` 선택합니다. `Settings` 

4) `Node type name`수정하려는 을 선택합니다.

5) 를 `OS Image` 원하는 새 값으로 조정하고 `Apply` 아래쪽에서 선택합니다. ! [OS 이미지 변경을 보여주는 샘플] [change-os-image]

6) 이제 는 `Provisioning state` 의 상태를 `Updating` 표시하고 한 번에 하나의 업그레이드 도메인을 진행합니다. 완료되면 `Succeeded` 다시 표시됩니다.
![노드 형식 업데이트를 보여주는 샘플][node-type-updating]


## <a name="modify-the-os-image-for-a-node-type-with-a-template"></a>템플릿을 통해 노드 형식에 대한 OS 이미지 수정

ARM 템플릿을 사용하여 노드 형식에 사용되는 OS 이미지를 수정하려면 `vmImageSku` 속성을 새 값으로 조정하고 클러스터 배포를 수행하여 설정을 적용합니다. 관리형 클러스터 공급자는 업그레이드 도메인별로 각 인스턴스를 이미지로 다시 이미지로 다시 추가합니다.

* Service Fabric 관리형 클러스터 리소스 apiVersion은 **2021-05-01** 이상이어야 합니다.

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                ...
                "vmImagePublisher": "[parameters('vmImagePublisher')]",
                "vmImageOffer": "[parameters('vmImageOffer')]",
                "vmImageSku": "[parameters('vmImageSku')]",
                "vmImageVersion": "[parameters('vmImageVersion')]",
                ...
            }
        }
}
```

## <a name="configure-placement-properties-for-a-node-type-with-portal"></a>포털을 통해 노드 유형에 대한 배치 속성 구성

이 연습에서는 포털을 사용하여 노드 형식에 대한 배치 속성을 수정하는 방법을 알아봅니다.

1) [Azure Portal](https://portal.azure.com/) 로그인

2) 클러스터 리소스 개요 페이지로 이동합니다. 
![샘플 개요 페이지][overview]

3) 섹션 아래에서 를 `Node Types` 선택합니다. `Settings` 

4) `Node type name`수정하려는 을 선택합니다.

5) 섹션에서 `Placement properties` 원하는 이름과 값을 추가하고 맨 `Apply` 아래에서 선택합니다. 이 스크린샷에서 `Name` `SSD_Premium` 는 의 와 함께 사용되었습니다. `Value` `true`
![배치 속성 추가를 보여주는 샘플][nodetype-placement-property]

6) 이제 는 `Provisioning state` `Updating` 완료될 때까지의 상태를 표시합니다. 완료되면 `Succeeded` 다시 표시됩니다.
![노드 형식 업데이트를 보여주는 샘플][node-type-updating]

이제 해당 배치 속성을 사용하여 [특정 워크로드가 클러스터 의 특정 노드 유형에서만 실행되도록 할](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)수 있습니다. 

## <a name="configure-placement-properties-for-a-node-type-with-a-template"></a>템플릿을 통해 노드 형식에 대한 배치 속성 구성

ARM 템플릿을 사용하여 노드 형식에 대한 배치 속성을 조정하려면 `placementProperties` 새 값으로 속성을 조정하고 설정을 적용하기 위해 클러스터 배포를 수행합니다. 아래 샘플에서는 노드 유형에 대해 설정되는 세 가지 값을 보여 줍니다.

* Service Fabric 관리형 클러스터 리소스 apiVersion은 **2021-05-01** 이상이어야 합니다.

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                "placementProperties": {
                    "PremiumSSD": "true",
                    "NodeColor": "green",
                    "SomeProperty": "5"
            }
        }
}
```
이제 해당 배치 속성을 사용하여 [특정 워크로드가 클러스터 의 특정 노드 유형에서만 실행되도록 할](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)수 있습니다. 

## <a name="modify-the-vm-sku-for-a-node-type"></a>노드 유형에 대한 VM SKU 수정

Service Fabric 관리형 클러스터는 VM SKU의 현재 위치 수정을 지원하지 않지만 클래식에서는 더 간단합니다. 이 작업을 수행하려면 다음을 수행해야 합니다.
* 필요한 VM SKU를 [사용하여 새 노드 유형을 만듭니다.](how-to-managed-cluster-modify-node-type.md#add-or-remove-a-node-type-with-portal)
* 워크로드를 마이그레이션합니다. 한 가지 방법은 배치 속성을 사용하여 [특정 워크로드가 클러스터의 특정 노드 유형에서만 실행되도록](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)하는 것입니다. 
* [이전 노드 형식 삭제](how-to-managed-cluster-modify-node-type.md#add-or-remove-a-node-type-with-portal)



## <a name="configure-multiple-managed-disks-preview"></a>여러 관리 디스크 구성(미리 보기)
Service Fabric 관리형 클러스터는 기본적으로 하나의 관리 디스크를 구성합니다. 다음과 같은 선택적 속성 및 값을 구성하여 클러스터 내의 노드 유형에 더 많은 관리 디스크를 추가할 수 있습니다. 드라이브 문자, 디스크 유형 및 디스크당 크기를 지정할 수 있습니다.

다음과 같이 Resource Manager 템플릿에서 속성 및 필수 매개 변수를 선언하여 더 많은 관리 `additionalDataDisks` 디스크를 구성합니다.

**기능 요구 사항**
* Lun은 디스크당 고유해야 하며 예약된 lun 0을 사용할 수 없습니다.
* 디스크 문자는 예약된 문자 C 또는 D를 사용할 수 없으며 만든 후에는 수정할 수 없습니다. 지정하지 않으면 S가 기본값으로 사용됩니다.
* [지원되는 디스크 유형을](how-to-managed-cluster-managed-disk.md) 지정해야 합니다.
* Service Fabric 관리형 클러스터 리소스 apiVersion은 **2021-11-01-preview** 이상이어야 합니다.

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                "additionalDataDisks": {
                    "lun": "1",
                    "diskSizeGB": "50",
                    "diskType": "Standard_LRS",
                    "diskLetter": "S" 
            }
        }
     }
```

[사용 가능한 매개 변수의 전체 목록을](/azure/templates/microsoft.servicefabric/managedclusters) 참조하세요.

## <a name="configure-the-service-fabric-data-disk-drive-letter-preview"></a>Service Fabric 데이터 디스크 드라이브 문자 구성(미리 보기)
Service Fabric 관리형 클러스터는 기본적으로 Service Fabric 데이터 디스크를 구성하고 노드 유형의 모든 노드에서 드라이브 문자를 자동으로 구성합니다. 이 선택적 속성 및 값을 구성하여 드라이브 문자 매핑에 대한 특정 요구 사항이 있는 경우 Service Fabric 데이터 디스크 문자를 지정하고 검색할 수 있습니다.

**기능 요구 사항**
* 디스크 문자는 예약된 문자 C 또는 D를 사용할 수 없으며 만든 후에는 수정할 수 없습니다. 지정하지 않으면 S가 기본값으로 사용됩니다.
* Service Fabric 관리형 클러스터 리소스 apiVersion은 **2021-11-01-preview** 이상이어야 합니다.

```json
     {
            "apiVersion": "[variables('sfApiVersion')]",
            "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
            "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
            "location": "[resourcegroup().location]",
            "properties": {
                "dataDiskLetter": "S"      
            }
        }
     }
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Service Fabric 관리형 클러스터 노드 유형 자동 크기 조정](how-to-managed-cluster-autoscale.md)
> [!div class="nextstepaction"]
> [Service Fabric 관리형 클러스터 구성 옵션](how-to-managed-cluster-configuration.md)
> [!div class="nextstepaction"]
> [Service Fabric 관리형 클러스터에 앱 배포](./tutorial-managed-cluster-deploy-app.md)


[overview]: ./media/how-to-managed-cluster-modify-node-type/sfmc-overview.png
[node-type-updating]: ./media/how-to-managed-cluster-modify-node-type/sfmc-adjust-node-type-updating.png
[adjust-node-count]: ./media/how-to-managed-cluster-modify-node-type/sfmc-adjust-node-counts.png
[change-nodetype-os-image]: ./media/how-to-managed-cluster-modify-node-type/sfmc-change-os-image.png
[nodetype-placement-property]: ./media/how-to-managed-cluster-modify-node-type/sfmc-nodetype-placement-property.png
[addremove]: ./media/how-to-managed-cluster-modify-node-type/sfmc-addremove-node-type.png

