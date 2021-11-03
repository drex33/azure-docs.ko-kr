---
title: '빠른 시작: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Traffic Manager 만들기'
description: 이 빠른 시작 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Traffic Manager 프로필을 만드는 방법을 설명합니다.
services: traffic-manager
author: duongau
ms.author: duau
ms.date: 09/01/2020
ms.topic: quickstart
ms.service: traffic-manager
ms.custom: devx-track-azurepowershell, subject-armqs, mode-arm
ms.openlocfilehash: 31f456ea4f828348457c5b01992812b834147248
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074634"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Traffic Manager 프로필 만들기

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 통해 성능 라우팅 메서드를 사용하여 외부 엔드포인트가 있는 Traffic Manager 프로필을 만드는 방법을 설명합니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Ftraffic-manager-external-endpoint%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/traffic-manager-external-endpoint)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.network/traffic-manager-external-endpoint/azuredeploy.json":::

템플릿에 다음과 같은 Azure 리소스 하나가 정의되어 있습니다.

* [**Microsoft.Network/trafficManagerProfiles**](/azure/templates/microsoft.network/trafficmanagerprofiles)

Azure Traffic Manager에 관련된 더 많은 템플릿을 찾으려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)을 참조하세요.

## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 코드 블록에서 **사용해 보기** 를 선택하여 Azure Cloud Shell을 열고 지침에 따라 Azure에 로그인합니다.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/traffic-manager-external-endpoint/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    콘솔에서 프롬프트가 표시될 때까지 기다립니다.

1. 이전 코드 블록에서 **복사** 를 선택하여 PowerShell 스크립트를 복사합니다.

1. 셸 콘솔 창을 마우스 오른쪽 단추로 클릭한 후 **붙여넣기** 를 선택합니다.

1. 값을 입력합니다.

    템플릿 배포는 두 개의 외부 엔드포인트가 있는 프로필을 만듭니다. **Endpoint1** 은 **북유럽** 에 위치하는 `www.microsoft.com`의 대상 엔드포인트를 사용합니다. **Endpoint2** 는 **미국 중남부** 에 위치하는 `docs.microsoft.com`의 대상 엔드포인트를 사용합니다.

    리소스 그룹 이름은 **rg** 가 추가된 프로젝트 이름입니다.

    > [!NOTE]
    > 템플릿을 성공적으로 배포하려면 **uniqueDNSname** 이 전역적으로 고유한 이름이어야 합니다. 배포에 실패하면 1단계부터 다시 시작합니다.

    템플릿을 배포하는 데 몇 분 정도 걸립니다. 완료되면 다음과 유사하게 출력됩니다.

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-arm-powershell-output.png" alt-text="Azure Traffic Manager Resource Manager 템플릿 PowerShell 배포 출력":::

Azure PowerShell은 템플릿을 배포하는 데 사용됩니다. Azure PowerShell 외에도 Azure Portal, Azure CLI 및 REST API를 사용할 수 있습니다. 다른 배포 방법을 알아보려면 [템플릿 배포](../azure-resource-manager/templates/deploy-portal.md)를 참조하세요.

## <a name="validate-the-deployment"></a>배포 유효성 검사

1. [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile)을 사용하여 Traffic Manager 프로필의 DNS 이름을 확인합니다.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name ExternalEndpointExample -ResourceGroupName $resourceGroupName | Select RelativeDnsName
    ```

    **RelativeDnsName** 값을 복사합니다. Traffic Manager 프로필의 DNS 이름은 `<relativednsname>.trafficmanager.net`입니다.

1. 로컬 PowerShell에서 **{relativeDNSname}** 변수를 `<relativednsname>.trafficmanager.net`으로 바꿔서 다음 명령을 실행합니다.

    ```powershell
    Resolve-DnsName -Name {relativeDNSname} | Select-Object NameHost | Select -First 1
    ```

    가까운 지역에 따라 `www.microsoft.com` 또는 `docs.microsoft.com`의 NameHost를 가져와야 합니다.

1. 다른 엔드포인트로 해결할 수 있는지 확인하려면 마지막 단계에서 가져온 대상에 대한 엔드포인트를 사용하지 않도록 설정합니다. **{endpointName}** 을 **endpoint1** 또는 **endpoint2** 중 하나로 바꿔서 `www.microsoft.com` 또는 `docs.microsoft.com`에 대한 대상을 각각 사용하지 않도록 설정합니다.

    ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name {endpointName} -Type ExternalEndpoints -ProfileName ExternalEndpointExample -ResourceGroupName $resourceGroupName -Force
    ```

1. 로컬 PowerShell에서 2단계의 명령을 다시 실행합니다. 이번에는 다른 엔드포인트에 대한 다른 NameHost를 가져와야 합니다.

## <a name="clean-up-resources"></a>리소스 정리

Traffic Manager 프로필이 더 이상 필요하지 않으면 리소스 그룹을 삭제합니다. 이렇게 하면 Traffic Manager 프로필과 모든 관련 리소스가 제거됩니다.

리소스 그룹을 삭제하려면 `Remove-AzResourceGroup` cmdlet을 호출합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Traffic Manager 프로필을 만들었습니다.

트래픽 라우팅에 대해 자세히 알아보려면 Traffic Manager 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Traffic Manager 자습서](tutorial-traffic-manager-improve-website-response.md)
