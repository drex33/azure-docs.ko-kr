---
title: '빠른 시작: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Front Door Service 만들기'
description: 이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Front Door Service를 만드는 방법을 설명합니다.
services: front-door
documentationcenter: ''
author: duongau
ms.author: duau
editor: ''
ms.date: 09/14/2020
ms.topic: quickstart
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: devx-track-azurepowershell, subject-armqs, mode-arm
ms.openlocfilehash: 6e2b5137096e407a86011ed0ee08ed7bf45c4f21
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131073095"
---
# <a name="quickstart-create-a-front-door-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Front Door 만들기

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Front Door를 만들어 웹 엔드포인트에 대한 고가용성을 설정하는 방법을 설명합니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Ffront-door-create-basic%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* 웹 사이트 또는 웹 애플리케이션의 IP 또는 FQDN입니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/front-door-create-basic)에서 나온 것입니다.

이 빠른 시작에서는 단일 백 엔드 및 `/*`와 일치하는 단일 기본 경로를 사용하여 Front Door 구성을 만듭니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.network/front-door-create-basic/azuredeploy.json":::

템플릿에 다음과 같은 Azure 리소스 하나가 정의되어 있습니다.

* [**Microsoft.Network/frontDoors**](/azure/templates/microsoft.network/frontDoors)

## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 코드 블록에서 **사용해 보기** 를 선택하여 Azure Cloud Shell을 열고 지침에 따라 Azure에 로그인합니다.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/front-door-create-basic/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    콘솔에서 프롬프트가 표시될 때까지 기다립니다.

1. 이전 코드 블록에서 **복사** 를 선택하여 PowerShell 스크립트를 복사합니다.

1. 셸 콘솔 창을 마우스 오른쪽 단추로 클릭한 후 **붙여넣기** 를 선택합니다.

1. 값을 입력합니다.

    템플릿 배포는 단일 백 엔드를 사용하여 Front Door를 만듭니다. 이 예제에서는 `microsoft.com`이 **backendAddress** 로 사용됩니다.

    리소스 그룹 이름은 **rg** 가 추가된 프로젝트 이름입니다.

    > [!NOTE]
    > 템플릿을 성공적으로 배포하려면 **frontDoorName** 이 전역적으로 고유한 이름이어야 합니다. 배포에 실패하면 1단계부터 다시 시작합니다.

    템플릿을 배포하는 데 몇 분 정도 걸립니다. 완료되면 다음과 유사하게 출력됩니다.

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-template-deployment-powershell-output.png" alt-text="Front Door Resource Manager 템플릿 PowerShell 배포 출력":::

Azure PowerShell은 템플릿을 배포하는 데 사용됩니다. Azure PowerShell 외에도 Azure Portal, Azure CLI 및 REST API를 사용할 수 있습니다. 다른 배포 방법을 알아보려면 [템플릿 배포](../azure-resource-manager/templates/deploy-portal.md)를 참조하세요.

## <a name="validate-the-deployment"></a>배포 유효성 검사

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 패널에서 **리소스 그룹** 을 선택합니다.

1. 이전 섹션에서 만든 리소스 그룹을 선택합니다. 기본 리소스 그룹 이름은 **rg** 가 추가된 프로젝트 이름입니다.

1. 이전에 만든 Front Door를 선택하고 **프런트 엔드 호스트** 링크를 클릭합니다. 링크를 누르면 웹 브라우저가 열리고 생성 중에 정의한 백 엔드 FQDN으로 리디렉션됩니다.

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-overview.png" alt-text="Front Door 포털 개요":::

## <a name="clean-up-resources"></a>리소스 정리

Front Door 서비스가 더 이상 필요하지 않으면 리소스 그룹을 삭제합니다. 그러면 Front Door 및 모든 관련 리소스가 제거됩니다.

리소스 그룹을 삭제하려면 `Remove-AzResourceGroup` cmdlet을 호출합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Front Door를 만들었습니다.

사용자 지정 도메인을 Front Door에 추가하는 방법을 알아보려면 Front Door 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Front Door 자습서](front-door-custom-domain.md)
