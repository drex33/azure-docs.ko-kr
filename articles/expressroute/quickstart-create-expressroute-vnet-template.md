---
title: '빠른 시작: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 ExpressRoute 회로 만들기'
description: 이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 ExpressRoute 회로를 만드는 방법을 보여줍니다.
services: expressroute
author: duongau
ms.author: duau
manager: kumud
ms.date: 10/12/2020
ms.topic: quickstart
ms.service: expressroute
ms.custom: devx-track-azurepowershell, subject-armqs, mode-arm
ms.openlocfilehash: d2a627b5b9100e338912bbae1e18260f7466a48b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056588"
---
# <a name="quickstart-create-an-expressroute-circuit-with-private-peering-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 프라이빗 피어링이 있는 ExpressRoute 회로 만들기

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 프라이빗 피어링이 있는 ExpressRoute 회로를 만드는 방법을 설명합니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fexpressroute-private-peering-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/expressroute-private-peering-vnet)에서 나온 것입니다.

이 빠른 시작에서는 *Equinix* 를 서비스 공급자로 사용하여 ExpressRoute 회로를 만듭니다. 회로는 대역폭이 *50Mbps* 이고 피어링 위치가 *Washington DC* 인 *프리미엄 SKU* 를 사용합니다. 프라이빗 피어링은 *192.168.10.16/30* 및 *192.168.10.20/30* 의 기본 및 보조 서브넷에서 사용하도록 설정됩니다. 가상 네트워크도 *HighPerformance ExpressRoute 게이트웨이* 와 함께 생성됩니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.network/expressroute-private-peering-vnet/azuredeploy.json":::

템플릿에 여러 Azure 리소스가 정의되어 있습니다.

* [**Microsoft.Network/expressRouteCircuits**](/azure/templates/microsoft.network/expressRouteCircuits)
* [**Microsoft.Network/expressRouteCircuits/peerings**](/azure/templates/microsoft.network/expressRouteCircuits/peerings)(회로에서 프라이빗 피어링을 사용하도록 설정하는 데 사용됨)
* [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networkSecurityGroups)(네트워크 보안 그룹은 가상 네트워크의 서브넷에 적용됨)
* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicIPAddresses)(공용 IP는 ExpressRoute 게이트웨이에서 사용됨)
* [**Microsoft.Network/virtualNetworkGateways**](/azure/templates/microsoft.network/virtualNetworkGateways)(ExpressRoute 게이트웨이는 VNet을 회로에 연결하는 데 사용됨)

ExpressRoute와 관련된 더 많은 템플릿을 찾으려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)을 참조하세요.

## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 코드 블록에서 **사용해 보기** 를 선택하여 Azure Cloud Shell을 열고 지침에 따라 Azure에 로그인합니다.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/expressroute-private-peering-vnet/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    콘솔에서 프롬프트가 표시될 때까지 기다립니다.

1. 이전 코드 블록에서 **복사** 를 선택하여 PowerShell 스크립트를 복사합니다.

1. 셸 콘솔 창을 마우스 오른쪽 단추로 클릭한 후 **붙여넣기** 를 선택합니다.

1. 값을 입력합니다.

    리소스 그룹 이름은 **rg** 가 추가된 프로젝트 이름입니다.

    템플릿을 배포하는 데 20분 정도 걸립니다. 완료되면 다음과 유사하게 출력됩니다.

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-powershell-output.png" alt-text="ExpressRoute Resource Manager 템플릿 PowerShell 배포 출력":::

Azure PowerShell은 템플릿을 배포하는 데 사용됩니다. Azure PowerShell 외에도 Azure Portal, Azure CLI 및 REST API를 사용할 수 있습니다. 다른 배포 방법을 알아보려면 [템플릿 배포](../azure-resource-manager/templates/deploy-portal.md)를 참조하세요.

## <a name="validate-the-deployment"></a>배포 유효성 검사

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 패널에서 **리소스 그룹** 을 선택합니다.

1. 이전 섹션에서 만든 리소스 그룹을 선택합니다. 기본 리소스 그룹 이름은 **rg** 가 추가된 프로젝트 이름입니다.

1. 리소스 그룹에는 여기에 표시된 다음 리소스가 포함되어야 합니다.

     :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-resource-group.png" alt-text="ExpressRoute 배포 리소스 그룹":::

1. ExpressRoute 회로 **er-ck01** 을 선택하여 회로 상태가 **활성화됨** 이고, 공급자 상태가 **프로비저닝되지 않음** 이고, 프라이빗 피어링이 **프로비저닝됨** 상태인지 확인합니다.

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-circuit.png" alt-text="ExpressRoute 배포 회로":::

> [!NOTE]
> 가상 네트워크를 회로에 연결하려면 먼저 공급자를 호출하여 프로비저닝 프로세스를 완료해야 합니다.

## <a name="clean-up-resources"></a>리소스 정리

ExpressRoute 회로로 만든 리소스가 더 이상 필요하지 않으면 리소스 그룹을 삭제합니다. 이렇게 하면 ExpressRoute 회로와 모든 관련 리소스가 제거됩니다.

리소스 그룹을 삭제하려면 `Remove-AzResourceGroup` cmdlet을 호출합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음을 만들었습니다.

* ExpressRoute 회로
* Virtual Network
* VPN Gateway
* 공용 IP
* 네트워크 보안 그룹

가상 네트워크를 회로에 연결하는 방법을 알아보려면 ExpressRoute 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [ExpressRoute 자습서](expressroute-howto-linkvnet-portal-resource-manager.md)
