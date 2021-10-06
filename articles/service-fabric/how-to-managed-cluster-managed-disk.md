---
title: Service Fabric 관리형 클러스터 노드에 대한 관리형 디스크 유형 선택
description: Service Fabric 관리형 클러스터 노드에 대한 관리형 디스크 유형을 선택하고 ARM 템플릿에서 구성하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 5/10/2021
ms.openlocfilehash: c9daef02b9249d44d5d202f5066bac40f807ec58
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546333"
---
# <a name="select-managed-disk-types-for-service-fabric-managed-cluster-nodes"></a>Service Fabric 관리형 클러스터 노드에 대한 관리형 디스크 유형 선택

Azure Service Fabric 관리형 클러스터는 신뢰할 수 있는 컬렉션 및 행위자와 같은 시나리오에 대해 애플리케이션 데이터를 비롯한 모든 스토리지 요구 사항에 대해 관리 디스크를 사용합니다. Azure 관리 디스크는 Azure에서 관리하고 Azure Virtual Machines와 함께 사용되는 블록 수준 스토리지 볼륨입니다. 관리 디스크는 온-프레미스 서버의 물리적 디스크와 유사하지만 가상화되어 있습니다. 관리 디스크를 사용하는 경우 디스크 크기, 디스크 유형을 지정하고 디스크를 프로비저닝하기만 하면 됩니다. 디스크를 프로비저닝하면 Azure가 나머지를 처리합니다. 관리 디스크에 대한 자세한 내용은 [Azure 관리 디스크 소개](../virtual-machines/managed-disks-overview.md)를 참조하세요.

## <a name="managed-disk-types"></a>관리 디스크 유형

Azure Service Fabric 관리형 클러스터는 다음 관리 디스크 유형을 지원합니다.
* 표준 HDD
    * 표준 HDD 로컬 중복 스토리지입니다. 중요하지 않고 드문 백업 액세스에 가장 적합합니다. 
* 표준 SSD *기본값*
    * 표준 SSD 로컬 중복 스토리지입니다. 웹 서버, 사용량이 적은 엔터프라이즈 애플리케이션 및 개발/테스트에 가장 적합합니다.
* 프리미엄 SSD *특정 VM 크기와 호환 가능* 자세한 내용은 [프리미엄 SSD](../virtual-machines/disks-types.md#premium-ssd)를 참조하세요.
    * 프리미엄 SSD 로컬 중복 스토리지입니다. 프로덕션 및 성능에 중요한 워크로드에 가장 적합합니다.

>[!NOTE]
> VM 크기와 연결된 임시 디스크는 Service Fabric 또는 애플리케이션 관련 데이터를 저장하는 데 사용되지 *않습니다*.

## <a name="specifying-a-service-fabric-managed-cluster-disk-type"></a>Service Fabric 관리형 클러스터 디스크 유형 지정

Service Fabric 관리형 클러스터 디스크 유형을 지정하려면 관리형 클러스터 리소스 정의에 다음 값을 포함해야 합니다.

* 값 **dataDiskType** 속성. 노드에 사용할 관리 디스크 유형을 지정합니다.

가능한 값은 다음과 같습니다.
* "Standard_LRS"
* "StandardSSD_LRS"
* "Premium_LRS"
>[!NOTE]
> 모든 vm 크기에 대해 모든 관리 디스크 유형을 사용할 수 있는 것은 아닙니다. 자세한 내용은 [Azure에서 사용할 수 있는 디스크 유형](../virtual-machines/disks-types.md)을 참조하세요.

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedclusters",
  "dataDiskType": "StandardSSD_LRS"
}
```

다음 사양이 포함된 샘플 템플릿을 사용할 수 있습니다. [Service Fabric 관리형 클러스터 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates)
