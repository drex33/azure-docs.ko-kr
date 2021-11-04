---
title: ARM 템플릿을 사용하여 컴퓨터를 Azure Automanage에 온보딩
description: Azure Resource Manager 템플릿을 사용하여 Azure Automanage에 컴퓨터를 온보딩하는 방법을 알아봅니다.
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/09/2021
ms.openlocfilehash: cdb10c265ddae4aaf83450c1951ef6cb5c2219df
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443854"
---
# <a name="onboard-a-machine-to-automanage-with-an-azure-resource-manager-arm-template"></a>ARM(Azure Resource Manager) 템플릿을 사용하여 Automanage에 컴퓨터 온보딩


## <a name="overview"></a>개요
ARM 템플릿을 사용 하 여 모범 사례를 자동으로 관리 하려면 아래 단계에 따라 컴퓨터를 등록 합니다.

## <a name="prerequisites"></a>사전 요구 사항
* 필요한 [RBAC 권한이](./automanage-virtual-machines.md#required-rbac-permissions) 있어야 합니다.
* 이러한 [전제 조건](./automanage-virtual-machines.md#prerequisites) 에서 강조 표시 되 고 지원 되는 VM 이미지가 지원 되는 지역에 있어야 합니다.


## <a name="arm-template-overview"></a>ARM 템플릿 개요
다음 ARM 템플릿은 지정된 컴퓨터를 Azure Automanage 모범 사례에 온보딩합니다. ARM 템플릿에 대한 자세한 내용과 배포 방법에 대한 단계는 [아래](#arm-template-deployment) ARM 템플릿 배포 섹션에 있습니다.
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "machineName": {
            "type": "String"
        },
        "configurationProfile": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/configurationProfileAssignments",
            "apiVersion": "2021-04-30-preview",
            "name": "[concat(parameters('machineName'), '/Microsoft.Automanage/default')]",
            "properties": {
                "configurationProfile": "[parameters('configurationProfile')]",
            }
        }
    ]
}
```

## <a name="arm-template-deployment"></a>ARM 템플릿 배포
위의 ARM 템플릿에서는 지정 된 컴퓨터에 대 한 구성 프로필 할당을 만듭니다. 

`configurationProfile` 값은 다음 값 중 하나일 수 있습니다.
* "/providers/Microsoft.Automanage/bestPractices/AzureBestPracticesProduction"
* "/providers/Microsoft.Automanage/bestPractices/AzureBestPracticesDevTest"

다음 단계를 따라 ARM 템플릿을 배포합니다.
1. 위의 ARM 템플릿을 `azuredeploy.json`로 저장합니다
1. `az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json`을 사용하여 ARM 템플릿 배포 실행
1. machineName, automanageAccountName 및 configurationProfileAssignment 값을 요구하는 메시지가 표시될 때 제공합니다.
1. 이제 구성을 완료했습니다.

모든 ARM 템플릿과 마찬가지로 매개 변수를 별도의 `azuredeploy.parameters.json` 파일로 빼내어 배포할 때 인수로 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Linux](./automanage-linux.md) 및 [Windows](./automanage-windows-server.md)용 Automanage에 대해 자세히 알아보세요.