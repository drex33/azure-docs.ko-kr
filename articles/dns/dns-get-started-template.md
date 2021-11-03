---
title: Azure DNS 영역 및 레코드 만들기 - ARM 템플릿(Azure Resource Manager 템플릿)
titleSuffix: Azure DNS
description: Azure DNS에 DNS 영역 및 레코드를 만드는 방법을 알아봅니다. ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 첫 번째 DNS 영역 및 레코드를 만들고 관리하는 단계별 빠른 시작입니다.
services: dns
author: duongau
ms.author: duau
ms.date: 6/2/2021
ms.topic: quickstart
ms.service: dns
ms.custom: devx-track-azurepowershell, subject-armqs, mode-arm
ms.openlocfilehash: dba996bcd4d2416a41ac6a7f1ad94804c0ecb839
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131033098"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-an-arm-template"></a>ARM 템플릿을 사용하여 Azure DNS 영역 및 레코드 만들기

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 `A` 레코드가 있는 DNS 영역을 만드는 방법을 설명합니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fazure-dns-new-zone%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/azure-dns-new-zone)에서 나온 것입니다.

이 빠른 시작에서는 `azurequickstart.org` 접미사를 사용하여 고유한 DNS 영역을 만듭니다. 두 개의 IP 주소를 가리키는 `A` 레코드도 영역에 배치됩니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.network/azure-dns-new-zone/azuredeploy.json":::

템플릿에 두 개의 Azure 리소스가 정의되어 있습니다.

- [**Microsoft.Network/dnsZones**](/azure/templates/microsoft.network/dnsZones)
- [**Microsoft.Network/dnsZones/A**](/azure/templates/microsoft.network/dnsZones/A): 영역에 `A` 레코드를 만드는 데 사용됩니다.

Azure Traffic Manager에 관련된 더 많은 템플릿을 찾으려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)을 참조하세요.

## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 코드 블록에서 **사용해 보기** 를 선택하여 Azure Cloud Shell을 열고 지침에 따라 Azure에 로그인합니다.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/azure-dns-new-zone/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    콘솔에서 프롬프트가 표시될 때까지 기다립니다.

1. 이전 코드 블록에서 **복사** 를 선택하여 PowerShell 스크립트를 복사합니다.

1. 셸 콘솔 창을 마우스 오른쪽 단추로 클릭한 후 **붙여넣기** 를 선택합니다.

1. 값을 입력합니다.

    템플릿 배포는 두 개의 IP 주소를 가리키는 하나의 `A` 레코드가 있는 영역을 만듭니다. 리소스 그룹 이름은 **rg** 가 추가된 프로젝트 이름입니다.

    템플릿을 배포하는 데 몇 초가 걸립니다. 완료되면 다음과 유사하게 출력됩니다.

    :::image type="content" source="./media/dns-getstarted-template/create-dns-zone-powershell-output.png" alt-text="Azure DNS 영역 Resource Manager 템플릿 PowerShell 배포 출력":::

Azure PowerShell은 템플릿을 배포하는 데 사용됩니다. Azure PowerShell 외에도 Azure Portal, Azure CLI 및 REST API를 사용할 수 있습니다. 다른 배포 방법을 알아보려면 [템플릿 배포](../azure-resource-manager/templates/deploy-portal.md)를 참조하세요.

## <a name="validate-the-deployment"></a>배포 유효성 검사

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 패널에서 **리소스 그룹** 을 선택합니다.

1. 이전 섹션에서 만든 리소스 그룹을 선택합니다. 기본 리소스 그룹 이름은 **rg** 가 추가된 프로젝트 이름입니다.

1. 리소스 그룹에는 여기에 표시된 다음 리소스가 포함되어야 합니다.

    :::image type="content" source="./media/dns-getstarted-template/resource-group-dns-zone.png" alt-text="DNS 영역 배포 리소스 그룹":::

1. `azurequickstart.org` 접미사가 있는 DNS 영역을 선택하여 `1.2.3.4` 및 `1.2.3.5` 값을 참조하는 `A` 레코드로 영역이 올바르게 만들어졌는지 확인합니다.

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-overview.png" alt-text="DNS 영역 배포":::

1. 이전 단계에서 이름 서버 이름 중 하나를 복사합니다.

1. 명령 프롬프트를 열고 다음 명령을 실행합니다.

   ```cmd
   nslookup www.<dns zone name> <name server name>
   ```

   예를 들면 다음과 같습니다.

   ```cmd
   nslookup www.2lwynbseszpam.azurequickstart.org ns1-09.azure-dns.com.
   ```

   다음 스크린샷과 유사한 내용이 표시되어야 합니다.

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-validation.png" alt-text="DNS 영역 nslookup":::

호스트 이름 `www.2lwynbseszpam.azurequickstart.org`는 구성한 대로 `1.2.3.4` 및 `1.2.3.5`로 확인됩니다. 이 결과는 이름 확인이 올바르게 작동하는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

DNS 영역으로 만든 리소스가 더 이상 필요하지 않으면 리소스 그룹을 삭제합니다. 이렇게 하면 DNS 영역과 모든 관련 리소스가 제거됩니다.

리소스 그룹을 삭제하려면 `Remove-AzResourceGroup` cmdlet을 호출합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음을 만들었습니다.

- DNS 영역
- `A` 레코드

이제 ARM 템플릿을 사용하여 첫 번째 DNS 영역과 레코드를 만들었으므로 사용자 지정 도메인에서 웹앱에 대한 레코드를 만들 수 있습니다.

> [!div class="nextstepaction"]
> [사용자 지정 도메인에서 웹앱에 대한 DNS 레코드 만들기](./dns-web-sites-custom-domain.md)
