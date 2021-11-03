---
title: '빠른 시작: Azure SignalR Service 만들기 - ARM 템플릿'
description: 이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure SignalR Service를 만드는 방법을 알아봅니다.
author: sffamily
ms.author: zhshang
ms.date: 10/02/2020
ms.topic: quickstart
ms.service: signalr
ms.custom: devx-track-azurepowershell, subject-armqs, mode-arm
ms.openlocfilehash: a00b88052fe5566d0fbaf3b28b57077349773212
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069523"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-signalr-service"></a>빠른 시작: ARM 템플릿을 사용하여 Azure SignalR Service 배포

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure SignalR Service를 만드는 방법을 설명합니다. Azure Portal, PowerShell 또는 CLI를 통해 Azure SignalR Service를 배포할 수 있습니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 로그인하면 Azure Portal에서 템플릿이 열립니다.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure Portal에서 ARM 템플릿을 사용하여 Azure SignalR Service를 Azure에 배포하는 단추.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.signalrservice%2fsignalr%2fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

# <a name="portal"></a>[포털](#tab/azure-portal)

활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/)
* [Azure PowerShell](/powershell/azure/install-az-ps)(코드를 로컬로 실행하려는 경우)

# <a name="cli"></a>[CLI](#tab/CLI)

* 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/)
* 코드를 로컬로 실행하려는 경우:
    * Bash 셸(예: [Windows용 Git](https://gitforwindows.org)에 포함된 Git Bash)
    * [Azure CLI](/cli/azure/install-azure-cli)

---

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/signalr/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.signalrservice/signalr/azuredeploy.json":::

템플릿에서 다음과 같은 하나의 Azure 리소스를 정의합니다.

* [**Microsoft.SignalRService/SignalR**](/azure/templates/microsoft.signalrservice/signalr)

## <a name="deploy-the-template"></a>템플릿 배포

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 ARM 템플릿을 사용하여 Azure SignalR Service를 배포하려면 다음 링크를 선택합니다.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure Portal에서 ARM 템플릿을 사용하여 Azure SignalR Service를 Azure에 배포하는 단추.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.signalrservice%2fsignalr%2fazuredeploy.json)

**Azure SignalR Service 배포** 페이지에서 다음을 수행합니다.

1. 원하는 경우 **구독** 을 기본값에서 변경합니다.

2. **리소스 그룹** 에 대해 **새로 만들기** 를 선택하고, 새 리소스 그룹의 이름을 입력한 다음, **확인** 을 선택합니다.

3. 새 리소스 그룹을 만든 경우 리소스 그룹에 대한 **지역** 을 선택합니다.

4. 원하는 경우 Azure SignalR Service의 새 **이름** 및 **위치**(예: **eastus2**)를 입력합니다. 이름을 지정하지 않으면 자동으로 생성됩니다. Azure SignalR Service의 위치는 리소스 그룹의 지역과 같거나 다를 수 있습니다. 위치를 지정하지 않으면 리소스 그룹과 동일한 지역으로 설정됩니다.

5. **가격 책정 계층**(**Free_F1** 또는 **Standard_S1**)을 선택하고, **용량**(SignalR 단위 수)을 입력하고, **기본값**(허브 서버 필요), **서버리스**(서버 연결을 허용하지 않음) 또는 **클래식**(허브에 서버 연결이 있는 경우에만 허브 서버로 라우트됨)  **서비스 모드** 중 하나를 선택합니다. 그런 다음, **연결 로그 사용** 또는 **메시징 로그 사용** 을 선택합니다.

    > [!NOTE]
    > **Free_F1** 가격 책정 계층의 경우 용량은 1 단위로 제한됩니다.

    :::image type="content" source="./media/signalr-quickstart-azure-signalr-service-arm-template/deploy-azure-signalr-service-arm-template-portal.png" alt-text="Azure Portal에서 Azure SignalR Service를 만들기 위한 ARM 템플릿의 스크린샷.":::

6. **검토 + 만들기** 를 선택합니다.

7. 사용 약관을 읽은 다음, **만들기** 를 선택합니다.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> PowerShell 스크립트를 로컬로 실행하려면 먼저 `Connect-AzAccount`를 입력하여 Azure 자격 증명을 설정합니다.

ARM 템플릿을 사용하여 Azure SignalR Service를 배포하려면 다음 코드를 사용합니다. 코드에서 다음 항목을 요구하는 메시지를 표시합니다.

* 새 Azure SignalR Service의 이름 및 지역
* 새 리소스 그룹의 이름 및 지역
* Azure 가격 책정 계층(**Free_F1** 또는 **Standard_S1**)
* SignalR 단위 용량(1, 2, 5, 10, 20, 50 또는 100)
  > [!NOTE]
  > **Free_F1** 가격 책정 계층의 경우 용량은 1 단위로 제한됩니다.
* 서비스 모드: **기본값**(허브 서버 필요), **서버리스**(서버 연결을 허용하지 않음) 또는 **클래식**(허브에 허브 서버가 있는 경우에만 허브 서버로 라우팅)
* 연결 로그 또는 메시징 로그를 사용하도록 설정할지 여부(**true** 또는 **false**)

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure SignalR Service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

$priceTier = Read-Host -Prompt "Enter the pricing tier (Free_F1 or Standard_S1)"
$unitCapacity = Read-Host -Prompt "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100)"
$servicingMode = Read-Host -Prompt "Enter the service mode (Default, Serverless, or Classic)"
$enableConnectionLogs = Read-Host -Prompt "Specify whether to enable connectivity logs (true or false)"
$enableMessageLogs = Read-Host -Prompt "Specify whether to enable messaging logs (true or false)"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion

$paramObjHashTable = @{
    name = $serviceName
    location = $serviceLocation
    pricingTier = $priceTier
    capacity = [int]$unitCapacity
    serviceMode = $servicingMode
    enableConnectivityLogs = $enableConnectionLogs
    enableMessagingLogs = $enableMessageLogs
}

Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure SignalR Service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateParameterObject $paramObjHashTable `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.signalrservice/signalr/azuredeploy.json
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

ARM 템플릿을 사용하여 Azure SignalR Service를 배포하려면 다음 코드를 사용합니다. 코드에서 다음 항목을 요구하는 메시지를 표시합니다.

* 새 Azure SignalR Service의 이름 및 지역
* 새 리소스 그룹의 이름 및 지역
* Azure 가격 책정 계층(**Free_F1** 또는 **Standard_S1**)
* SignalR 단위 용량(1, 2, 5, 10, 20, 50 또는 100)
    > [!NOTE]
    > **Free_F1** 가격 책정 계층의 경우 용량은 1 단위로 제한됩니다.
* 서비스 모드: **기본값**(허브 서버 필요), **서버리스**(서버 연결을 허용하지 않음) 또는 **클래식**(허브에 허브 서버가 있는 경우에만 허브 서버로 라우팅)
* 연결 로그 또는 메시징 로그를 사용하도록 설정할지 여부(**true** 또는 **false**)

```azurecli-interactive
read -p "Enter a name for the new Azure SignalR Service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter the pricing tier (Free_F1 or Standard_S1): " priceTier &&
read -p "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100): " unitCapacity &&
read -p "Enter the service mode (Default, Serverless, or Classic): " servicingMode &&
read -p "Specify whether to enable connectivity logs (true or false): " enableConnectionLogs &&
read -p "Specify whether to enable messaging logs (true or false): " enableMessageLogs &&
params='name='$serviceName' location='$serviceLocation' pricingTier='$priceTier' capacity='$unitCapacity' serviceMode='$servicingMode' enableConnectivityLogs='$enableConnectionLogs' enableMessagingLogs='$enableMessageLogs &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure SignalR Service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.signalrservice/signalr/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> 배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 나중에 배포된 리소스를 검토하는 데 사용하는 Azure SignalR Service 및 리소스 그룹의 이름을 적어 두세요.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

# <a name="portal"></a>[포털](#tab/azure-portal)

새 Azure SignalR Service에 대한 개요를 보려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 **SignalR** 을 검색하여 선택합니다.

2. SignalR 목록에서 새 서비스를 선택합니다. 새 Azure SignalR Service에 대한 **개요** 페이지가 표시됩니다.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Azure SignalR Service에 대한 세부 정보를 보려면 다음 대화형 코드를 실행합니다. 새 서비스 및 리소스 그룹의 이름을 입력해야 합니다.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure SignalR Service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Azure SignalR Service에 대한 세부 정보를 보려면 다음 대화형 코드를 실행합니다. 새 서비스 및 리소스 그룹의 이름을 입력해야 합니다.

```azurecli-interactive
read -p "Enter the name of your Azure SignalR Service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az signalr show --resource-group $resourceGroupName --name $serviceName" &&
az signalr show --resource-group $resourceGroupName --name $serviceName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹을 삭제합니다. 그러면 리소스 그룹의 리소스도 삭제됩니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)에서 **리소스 그룹** 을 검색하여 선택합니다.

2. 리소스 그룹 목록에서 리소스 그룹의 이름을 선택합니다.

3. 리소스 그룹의 **개요** 페이지에서 **리소스 그룹 삭제** 를 선택합니다.

4. 확인 대화 상자에서 리소스 그룹의 이름을 입력한 다음, **삭제** 를 선택합니다.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>다음 단계

ARM 템플릿을 만드는 과정을 안내하는 단계별 자습서는 다음을 참조하세요.

> [!div class="nextstepaction"]
> [ 자습서: 첫 번째 ARM 템플릿 만들기 및 배포](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
