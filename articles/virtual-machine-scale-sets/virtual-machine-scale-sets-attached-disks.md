---
title: Azure Virtual Machine Scale Sets와 연결된 데이터 디스크
description: 특정 사용 사례에 대한 개요를 통해 Virtual Machine Scale Sets와 연결된 데이터 디스크를 사용하는 방법을 알아봅니다.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 4/25/2017
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 13bdbe7a1f6c328a2797d6f8869c05af9c5cb7f5
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690558"
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Azure 가상 머신 확장 집합 및 연결된 데이터 디스크

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 단일 확장 집합

사용 가능한 스토리지를 확장하기 위해 Azure [가상 머신 확장 집합](./index.yml)에서는 연결된 데이터 디스크를 사용한 VM 인스턴스를 지원합니다. 확장 집합을 만들 때 데이터를 연결하거나 기존 확장 집합에 데이터를 연결할 수 있습니다.

> [!NOTE]
> 연결된 데이터 디스크를 사용하여 확장 집합을 만드는 경우 독립 실행형 Azure VM의 경우와 마찬가지로 사용할 VM 내에서 디스크를 탑재하고 포맷해야 합니다. 이 프로세스를 완료하는 편리한 방법은 사용자 지정 스크립트 확장을 사용하여 VM에서 모든 데이터 디스크를 분할하고 포맷하는 스크립트를 호출하는 것입니다. 이 예제의 경우 [Azure CLI](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks)을 참조하세요.


## <a name="create-and-manage-disks-in-a-scale-set"></a>확장 집합에서 디스크 만들기 및 관리
연결된 데이터 디스크를 사용하여 확장 집합을 만들거나 형식을 준비하거나 데이터 디스크를 추가 및 삭제하는 방법에 대한 자세한 정보는 다음과 같은 자습서 중 하나를 참조하세요.

- [Azure CLI](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

이 문서의 나머지 부분에서는 확장 집합에 콘텐츠가 포함된 기존 데이터 디스크를 연결하거나 데이터 디스크가 필요한 Service Fabric 클러스터와 같은 특정 사용 사례를 설명합니다.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>연결된 데이터 디스크를 사용하여 Service Fabric 클러스터 만들기
Azure에서 실행되는 [Service Fabric](../service-fabric/service-fabric-cluster-nodetypes.md) 클러스터의 각 [노드 형식](../service-fabric/index.yml)은 가상 머신 확장 집합을 통해 백업됩니다. Azure Resource Manager 템플릿을 사용하여 Service Fabric 클러스터를 구성하는 확장 집합에 데이터 디스크를 연결할 수 있습니다. [기존 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates) 시작 지점으로 사용할 수 있습니다. 템플릿에서, _Microsoft.Compute/virtualMachineScaleSets_ 리소스의 _storageProfile_ 에 _dataDisks_ 섹션을 포함하고 템플릿을 배포합니다. 다음 예제에서는 128GB 데이터 디스크를 연결합니다.

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

클러스터를 배포할 때 자동으로 데이터 디스크를 분할하고, 서식을 지정하고, 탑재할 수 있습니다. 확장 집합의 _virtualMachineProfile__extensionProfile_ 에 사용자 지정 스크립트 확장을 추가합니다.

Windows 클러스터에서 자동으로 데이터 디스크를 준비하려면 다음을 추가합니다.

```json
{
    "name": "customScript",
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.8",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
Linux 클러스터에서 자동으로 데이터 디스크를 준비하려면 다음을 추가합니다.
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existing-scale-set"></a>기존 확장 집합에 미리 지정된 데이터 디스크 추가
확장 집합 모델에 지정된 데이터 디스크는 항상 비어 있습니다. 그러나 확장 집합의 특정 VM에 기존 데이터 디스크를 연결할 수 있습니다. 확장 집합의 모든 VM에 데이터를 전파하려는 경우 데이터 디스크를 복제하고 확장 집합의 각 VM에 이 데이터 디스크를 연결하거나, 데이터가 포함된 사용자 지정 이미지를 만들고 이 사용자 지정 이미지에서 확장 집합을 프로비저닝하거나, Azure Files 또는 유사한 데이터 스토리지 제공을 사용할 수 있습니다.


## <a name="additional-notes"></a>추가적인 참고 사항
Azure Managed Disks 및 확장 집합 연결 데이터 디스크에 대한 지원은 Microsoft.Compute API [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/compute/resource-manager/Microsoft.Compute/preview/2016-04-30-preview/compute.json) 이상의 API 버전에서 사용할 수 있습니다.

확장 집합에서 연결된 데이터 디스크에 대한 Azure Portal 지원은 제한되어 있습니다. 요구 사항에 따라 Azure 템플릿, CLI, PowerShell, SDK 및 REST API를 사용하여 연결된 디스크를 관리할 수 있습니다.
