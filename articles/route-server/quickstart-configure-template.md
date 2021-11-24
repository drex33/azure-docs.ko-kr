---
title: '빠른 시작: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Route Server 만들기'
description: 이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Route Server를 만드는 방법을 보여 줍니다.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell, mode-arm
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: 7a9e9b024f6f84f510bffd6f50434a861e6e84e7
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133043603"
---
# <a name="quickstart-create-an-azure-route-server-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Azure Route Server 만들기

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Route Server를 새 가상 네트워크 또는 기존 가상 네트워크에 배포하는 방법을 설명합니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Froute-server%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/route-server)에서 나온 것입니다.

이 빠른 시작에서는 Azure Route Server를 새 가상 네트워크 또는 기존 가상 네트워크에 배포합니다. `RouteServerSubnet`이라는 전용 서브넷이 Route Server를 호스트하기 위해 만들어집니다. 또한 Route Server는 BGP 피어링을 설정하기 위해 피어 ASN 및 피어 IP를 사용하여 구성됩니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.network/route-server/azuredeploy.json" range="001-145" highlight="105-142":::

템플릿에 여러 Azure 리소스가 정의되어 있습니다.

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualNetworks/subnets)(두 개의 서브넷, `routeserversubnet`이라는 서브넷)
* [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualhubs)(Route Server 배포)
* [**Microsoft.Network/virtualHubs/ipConfigurations**](/azure/templates/microsoft.network/virtualhubs/ipConfigurations)
* [**Microsoft.Network/virtualHubs/bgpConnections**](/azure/templates/microsoft.network/virtualhubs/bgpconnections)(피어 ASN 및 피어 IP 구성)


ExpressRoute와 관련된 더 많은 템플릿을 찾으려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)을 참조하세요.

## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 코드 블록에서 **사용해 보기** 를 선택하여 Azure Cloud Shell을 열고 지침에 따라 Azure에 로그인합니다.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/route-server/azuredeploy.json"

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

    :::image type="content" source="./media/quickstart-configure-template/powershell-output.png" alt-text="Route Server Resource Manager 템플릿 PowerShell 배포 출력":::

Azure PowerShell은 템플릿을 배포하는 데 사용됩니다. Azure PowerShell 외에도 Azure Portal, Azure CLI 및 REST API를 사용할 수 있습니다. 다른 배포 방법을 알아보려면 [템플릿 배포](../azure-resource-manager/templates/deploy-portal.md)를 참조하세요.

## <a name="validate-the-deployment"></a>배포 유효성 검사

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 패널에서 **리소스 그룹** 을 선택합니다.

1. 이전 섹션에서 만든 리소스 그룹을 선택합니다. 기본 리소스 그룹 이름은 **rg** 가 추가된 프로젝트 이름입니다.

1. 리소스 그룹은 가상 네트워크만 포함해야 합니다.

     :::image type="content" source="./media/quickstart-configure-template/resource-group.png" alt-text="가상 네트워크가 있는 Route Server 배포 리소스 그룹":::

1. [https://editor.swagger.io](https://aka.ms/routeserver) 로 이동합니다.

1. **routeserver** 라는 Route Server를 선택하여 배포가 성공했는지 확인합니다.

    :::image type="content" source="./media/quickstart-configure-template/deployment.png" alt-text="Route Server 개요 페이지의 스크린샷":::

## <a name="clean-up-resources"></a>리소스 정리

Route Server로 만든 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제합니다. 그러면 Route Server 및 관련된 모든 리소스가 제거됩니다.

리소스 그룹을 삭제하려면 `Remove-AzResourceGroup` cmdlet을 호출합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음을 만들었습니다.

* Route Server
* Virtual Network
* 서브넷

Azure Route Server를 만든 후에 Azure Route Server가 ExpressRoute 및 VPN Gateway와 상호 작용하는 방법에 대해 계속 알아보세요. 

> [!div class="nextstepaction"]
> [Azure ExpressRoute 및 Azure VPN 지원](expressroute-vpn-support.md)
