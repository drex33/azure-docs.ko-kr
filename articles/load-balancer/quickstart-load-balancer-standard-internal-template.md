---
title: '빠른 시작: 템플릿을 사용하여 내부 부하 분산 장치 만들기'
description: 이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 내부 부하 분산 장치를 만드는 방법을 보여줍니다.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.custom: subject-armqs, mode-arm
ms.author: allensu
ms.date: 09/14/2020
ms.openlocfilehash: 51956cefc27963f3a8fb1185f2ab2c65309ef6f2
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133074358"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 VM 부하를 분산하는 내부 부하 분산 장치 만들기

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 내부 Azure 부하 분산 장치를 만드는 방법을 보여줍니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2F2-vms-internal-load-balancer%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/2-vms-internal-load-balancer/)에 있습니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.compute/2-vms-internal-load-balancer/azuredeploy.json":::

템플릿에 여러 Azure 리소스가 정의되어 있습니다.

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): 부팅 진단을 위한 가상 머신 스토리지 계정
- [**Microsoft.Compute/availabilitySets**](/azure/templates/microsoft.compute/availabilitySets): 가상 머신에 대한 가용성 집합
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks): 부하 분산 장치 및 가상 머신을 위한 가상 네트워크
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkInterfaces): 가상 머신용 네트워크 인터페이스
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers): 내부 부하 분산 장치입니다.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualMachines): 가상 머신

Azure Load Balancer에 관련된 더 많은 템플릿을 찾으려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)을 참조하세요.

## <a name="deploy-the-template"></a>템플릿 배포

**Azure CLI**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupLB"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.compute/2-vms-internal-load-balancer/azuredeploy.json"

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

## <a name="review-deployed-resources"></a>배포된 리소스 검토

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 패널에서 **리소스 그룹** 을 선택합니다.

1. 이전 섹션에서 만든 리소스 그룹을 선택합니다. 기본 리소스 그룹 이름은 **myResourceGroupLB** 입니다.

1. 다음 리소스가 리소스 그룹에서 만들어졌는지 확인합니다.

:::image type="content" source="media/quickstart-load-balancer-standard-internal-template/verify-creation.png" alt-text="리소스 만들기를 확인하는 사용자 Azure Portal" border="true":::

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹 및 포함된 모든 리소스를 제거할 수 있습니다.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>다음 단계

템플릿 만들기 프로세스를 안내하는 단계별 자습서는 다음을 참조하세요.

> [!div class="nextstepaction"]
> 첫 번째 ARM 템플릿 만들기 및 배포[
