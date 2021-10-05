---
title: ARM 템플릿을 사용하여 컴퓨터를 Azure Automanage에 온보딩
description: Azure Resource Manager 템플릿을 사용하여 Azure Automanage에 컴퓨터를 온보딩하는 방법을 알아봅니다.
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/09/2021
ms.openlocfilehash: c86cf4ac8bdc5855248d2ec114f7ba2f050d8346
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129457452"
---
# <a name="onboard-a-machine-to-automanage-with-an-azure-resource-manager-arm-template"></a>ARM(Azure Resource Manager) 템플릿을 사용하여 Automanage에 컴퓨터 온보딩


## <a name="overview"></a>개요
아래 단계에 따라 Automanage 모범 사례에 컴퓨터를 온보딩합니다. 아래 ARM 템플릿은 Automanage에 온보딩된 컴퓨터를 나타내는 Azure 리소스인 `configurationProfileAssignment` 개체를 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소
* 기존 Automanage 계정을 만들고 올바른 권한을 할당해야 합니다. Automanage 계정을 생성하고 권한을 할당하는 방법에 대한 자세한 내용은 [이 문서](./automanage-account.md)를 참조하세요.
* 권한이 할당된 기존 Automanage 계정이 있는 경우, Automanage에 온보딩하려는 머신이 포함된 리소스 그룹에 대한 **기여자** 역할도 있어야 합니다.


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
        "automanageAccountName": {
            "type": "String"
        },
        "configurationProfileAssignment": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/configurationProfileAssignments",
            "apiVersion": "2020-06-30-preview",
            "name": "[concat(parameters('machineName'), '/Microsoft.Automanage/', 'default')]",
            "properties": {
                "configurationProfile": "[parameters('configurationProfileAssignment')]",
                "accountId": "[concat(resourceGroup().id, '/providers/Microsoft.Automanage/accounts/', parameters('automanageAccountName'))]"
            }
        }
    ]
}
```

## <a name="arm-template-deployment"></a>ARM 템플릿 배포
위의 ARM 템플릿은 지정된 Automanage 계정을 사용하여 지정된 컴퓨터에 대한 구성 프로필 할당을 생성합니다. Automanage 계정을 만들지 않았다면 [이 문서](./automanage-account.md)에서 자세히 알아보세요.

`configurationProfileAssignment` 값은 다음 값 중 하나일 수 있습니다.
* "Production"
* "DevTest"

다음 단계를 따라 ARM 템플릿을 배포합니다.
1. 위의 ARM 템플릿을 `azuredeploy.json`로 저장합니다
1. `az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json`을 사용하여 ARM 템플릿 배포 실행
1. machineName, automanageAccountName 및 configurationProfileAssignment 값을 요구하는 메시지가 표시될 때 제공합니다.
1. 이제 구성을 완료했습니다.

모든 ARM 템플릿과 마찬가지로 매개 변수를 별도의 `azuredeploy.parameters.json` 파일로 빼내어 배포할 때 인수로 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Linux](./automanage-linux.md) 및 [Windows](./automanage-windows-server.md)용 Automanage에 대해 자세히 알아보세요.