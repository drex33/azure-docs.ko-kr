---
title: '빠른 시작: ARM 템플릿을 사용하여 Azure API for FHIR 배포'
description: 이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure API for FHIR®(Fast Healthcare Interoperability Resources)을 배포하는 방법을 알아봅니다.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell, mode-other
ms.author: zxue
ms.date: 08/05/2021
ms.openlocfilehash: 26a4a80faa71755bb610c8883304527a7ec2c37c
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133052871"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-api-for-fhir"></a>빠른 시작: ARM 템플릿을 사용하여 Azure API for FHIR 배포

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure API for FHIR®(Fast Healthcare Interoperability Resources)을 배포하는 방법에 대해 알아봅니다. Azure Portal, PowerShell 또는 CLI를 통해 Azure API for FHIR을 배포할 수 있습니다.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 로그인하면 Azure Portal에서 템플릿이 열립니다.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure Portal에서 ARM 템플릿을 사용하여 Azure API for FHIR 서비스를 Azure에 배포합니다.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.healthcareapis%2fazure-api-for-fhir%2fazuredeploy.json)

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

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/azure-api-for-fhir/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.healthcareapis/azure-api-for-fhir/azuredeploy.json":::

템플릿에서 다음과 같은 하나의 Azure 리소스를 정의합니다.

* **Microsoft.HealthcareApis/services**

<!--

Replace the line above with the following line once https://docs.microsoft.com/azure/templates/microsoft.healthcareapis/services goes live:

* [**Microsoft.HealthcareApis/services**](/azure/templates/microsoft.healthcareapis/services)

-->

## <a name="deploy-the-template"></a>템플릿 배포

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 ARM 템플릿을 사용하여 Azure API for FHIR을 배포하려면 다음 링크를 선택합니다.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure Portal에서 ARM 템플릿을 사용하여 Azure API for FHIR 서비스를 Azure에 배포합니다.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.healthcareapis%2fazure-api-for-fhir%2fazuredeploy.json)

**Azure API for FHIR 배포** 페이지에서 다음을 수행합니다.

1. 원하는 경우 **구독** 을 기본값에서 다른 구독으로 변경합니다.

2. **리소스 그룹** 에 대해 **새로 만들기** 를 선택하고, 새 리소스 그룹의 이름을 입력한 다음, **확인** 을 선택합니다.

3. 새 리소스 그룹을 만든 경우 리소스 그룹에 대한 **지역** 을 선택합니다.

4. 새 **서비스 이름** 을 입력하고, Azure API for FHIR의 **위치** 를 선택합니다. 위치는 리소스 그룹의 지역과 같거나 다를 수 있습니다.

   [![Azure Portal에서 ARM 템플릿을 사용 하 여 FHIR 용 AZURE API를 배포 합니다. ](media/fhir-resource-manager-template/deploy-azure-api-fhir.png) ](media/fhir-resource-manager-template/deploy-azure-api-fhir.png#lightbox)

5. **검토 + 만들기** 를 선택합니다.

6. 사용 약관을 읽은 다음, **만들기** 를 선택합니다.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> PowerShell 스크립트를 로컬로 실행하려면 먼저 `Connect-AzAccount`를 입력하여 Azure 자격 증명을 설정합니다.

`Microsoft.HealthcareApis` 리소스 공급자가 아직 구독에 등록되지 않은 경우 다음 대화형 코드를 사용하여 등록할 수 있습니다. Azure Cloud Shell에서 코드를 실행하려면 코드 블록의 위쪽 모서리에 있는 **사용해 보세요** 를 선택합니다.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

ARM 템플릿을 사용하여 Azure API for FHIR을 배포하려면 다음 코드를 사용합니다. 이 코드는 새 FHIR 서비스 이름, 새 리소스 그룹의 이름 및 각각의 위치를 묻는 메시지를 표시합니다.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure API for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.healthcareapis/azure-api-for-fhir/azuredeploy.json `
    -serviceName $serviceName `
    -location $serviceLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

`Microsoft.HealthcareApis` 리소스 공급자가 아직 구독에 등록되지 않은 경우 다음 대화형 코드를 사용하여 등록할 수 있습니다. Azure Cloud Shell에서 코드를 실행하려면 코드 블록의 위쪽 모서리에 있는 **사용해 보세요** 를 선택합니다.

```azurecli-interactive
az extension add --name healthcareapis
```

ARM 템플릿을 사용하여 Azure API for FHIR을 배포하려면 다음 코드를 사용합니다. 이 코드는 새 FHIR 서비스 이름, 새 리소스 그룹의 이름 및 각각의 위치를 묻는 메시지를 표시합니다.

```azurecli-interactive
read -p "Enter a name for the new Azure API for FHIR service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
params='serviceName='$serviceName' location='$serviceLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure API for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.healthcareapis/azure-api-for-fhir/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> 배포를 완료하는 데 몇 분이 걸립니다. 나중에 배포된 리소스를 검토하는 데 사용하는 Azure API for FHIR 및 리소스 그룹의 이름을 적어 두세요.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

# <a name="portal"></a>[포털](#tab/azure-portal)

다음 단계에 따라 새 Azure API for FHIR 서비스에 대한 개요를 확인합니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure API for FHIR** 을 검색하여 선택합니다.

2. FHIR 목록에서 새 서비스를 선택합니다. 새 Azure API for FHIR에 대한 **개요** 페이지가 표시됩니다.

3. 새 FHIR API 계정이 프로비저닝되었는지 확인하려면 **FHIR 메타데이터 엔드포인트** 옆에 있는 링크를 선택하여 FHIR API 기능 문을 가져옵니다. 링크의 형식은 `https://<service-name>.azurehealthcareapis.com/metadata`입니다. 계정이 프로비저닝되면 큰 JSON 파일이 표시됩니다.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

다음 대화형 코드를 실행하여 Azure API for FHIR 서비스에 대한 세부 정보를 확인합니다. 새 서비스 및 리소스 그룹의 이름을 입력해야 합니다.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned"

$requestUri="https://" + $serviceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

다음 대화형 코드를 실행하여 Azure API for FHIR 서비스에 대한 세부 정보를 확인합니다. 새 서비스 및 리소스 그룹의 이름을 입력해야 합니다.

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$serviceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
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

ARM 템플릿을 만드는 과정을 안내하는 단계별 자습서는 [첫 번째 ARM 템플릿을 만들고 배포하기 위한 자습서](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 빠른 시작 가이드에서는 Azure API for FHIR을 구독에 배포했습니다. Azure API for FHIR에서 추가 설정을 설정하려면 추가 설정 방법 가이드로 이동합니다. Azure API for FHIR 사용을 시작할 준비가 되면 애플리케이션을 등록하는 방법에 대해 자세히 읽어보세요.

>[!div class="nextstepaction"]
>[Azure API for FHIR의 추가 설정](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[애플리케이션 등록 개요](fhir-app-registration.md)
