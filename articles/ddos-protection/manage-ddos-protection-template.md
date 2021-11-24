---
title: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure DDoS Protection 계획 만들기 및 사용
description: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure DDoS Protection 계획을 만들어 사용하도록 설정하는 방법을 알아봅니다.
services: ddos-protection
documentationcenter: na
author: mumian
ms.service: ddos-protection
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.custom: subject-armqs, devx-track-azurepowershell, mode-arm
ms.author: jgao
ms.date: 04/26/2021
ms.openlocfilehash: df933cc16306f3b9af2a7292413203355bda122c
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133053083"
---
# <a name="quickstart-create-an-azure-ddos-protection-standard-using-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Azure DDoS Protection 표준 만들기

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 DDoS(분산형 서비스 거부) 보호 계획 및 VNet(가상 네트워크)을 만든 다음, VNet에 대한 보호 계획을 사용하도록 설정하는 방법에 대해 설명합니다. Azure DDoS Protection 표준 계획은 구독 전체에서 DDoS 보호를 사용하도록 설정된 가상 네트워크 세트를 정의합니다. 조직에 대해 하나의 DDoS 보호 계획을 구성하고 여러 구독의 가상 네트워크를 동일한 계획에 연결할 수 있습니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fcreate-and-enable-ddos-protection-plans%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/create-and-enable-ddos-protection-plans)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.network/create-and-enable-ddos-protection-plans/azuredeploy.json":::

템플릿은 다음 두 가지 리소스를 정의합니다.

- [Microsoft.Network/ddosProtectionPlans](/azure/templates/microsoft.network/ddosprotectionplans)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)

## <a name="deploy-the-template"></a>템플릿 배포

다음 예에서 템플릿은 새 리소스 그룹, DDoS 보호 계획 및 VNet을 만듭니다.

1. Azure에 로그인하고 템플릿을 열려면 **Azure에 배포** 단추를 선택합니다.

    [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fcreate-and-enable-ddos-protection-plans%2Fazuredeploy.json)

1. 값을 입력하여 새 리소스 그룹, DDoS 보호 계획 및 VNet 이름을 만듭니다.

    :::image type="content" source="media/manage-ddos-protection-template/ddos-template.png" alt-text="DDoS 빠른 시작 템플릿":::

    - **구독**: 리소스가 배포될 Azure 구독의 이름입니다.
    - **리소스 그룹**: 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다.
    - **지역**: 리소스 그룹이 배포되는 지역(예: 미국 동부)입니다.
    - **DDoS 보호 계획 이름**: 새 DDoS 보호 계획의 이름입니다.
    - **가상 네트워크 이름**: 새 VNet의 이름을 만듭니다.
    - **위치**: 리소스 배포를 위해 리소스 그룹과 동일한 지역을 사용하는 함수입니다.
    - **Vnet 주소 접두사**: 기본값을 사용하거나 VNet 주소를 입력합니다.
    - **서브넷 접두사**: 기본값을 사용하거나 VNet 서브넷을 입력합니다.
    - **Ddos 보호 계획 사용**: 기본값은 DDoS 보호 계획을 사용하도록 설정하는 `true`입니다.

1. **검토 + 만들기** 를 선택합니다.
1. 템플릿 유효성 검사가 통과되었는지 확인하고, **만들기** 를 선택하여 배포를 시작합니다.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

Azure CLI 또는 Azure PowerShell 명령을 복사하려면 **복사** 단추를 선택합니다. **사용해 보세요** 단추를 클릭하면 Azure Cloud Shell을 열어 명령을 실행합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az network ddos-protection show \
  --resource-group MyResourceGroup \
  --name MyDdosProtectionPlan
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName 'MyResourceGroup' -Name 'MyDdosProtectionPlan'
```

---

출력에 새 리소스가 표시됩니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```Output
{
  "etag": "W/\"abcdefgh-1111-2222-bbbb-987654321098\"",
  "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan",
  "location": "eastus",
  "name": "MyDdosProtectionPlan",
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "resourceGuid": null,
  "tags": null,
  "type": "Microsoft.Network/ddosProtectionPlans",
  "virtualNetworks": [
    {
      "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : MyDdosProtectionPlan
Id                : /subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan
Etag              : W/"abcdefgh-1111-2222-bbbb-987654321098"
ProvisioningState : Succeeded
VirtualNetworks   : [
                      {
                        "Id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet"
                      }
                    ]
```

---

## <a name="clean-up-resources"></a>리소스 정리

완료되면 리소스를 삭제할 수 있습니다. 이 명령은 리소스 그룹과 그 속에 포함된 모든 리소스를 삭제합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az group delete --name MyResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'MyResourceGroup'
```

---

## <a name="next-steps"></a>다음 단계

DDoS 보호 계획에 대한 원격 분석을 보고 구성하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [DDoS 보호 원격 분석 보기 및 구성](telemetry.md)
