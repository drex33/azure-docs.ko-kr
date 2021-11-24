---
title: '빠른 시작: Azure 템플릿을 사용하여 Azure IoT Connector for FHIR(미리 보기) 배포'
description: 이 빠른 시작에서는 Azure Resource Manager 템플릿 (ARM 템플릿)을 사용 하 여 fhir (미리 보기) 용 Azure IoT Connector를 배포 하는 방법을 설명 합니다.
author: rbhaiya
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.author: rabhaiya
ms.date: 09/10/2021
ms.custom: devx-track-azurepowershell, mode-other
ms.openlocfilehash: 332b7aeaa9368dbdd0c4c863dfbaf174b2037331
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133061515"
---
# <a name="quickstart-use-an-azure-resource-manager-arm-template-to-deploy-azure-iot-connector-for-fhir-preview"></a>빠른 시작: ARM(Azure Resource Manager) 템플릿을 사용하여 Azure IoT Connector for FHIR(미리 보기) 배포

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure API for FHIR의 기능인 FHIR&#174;(Fast Healthcare Interoperability Resources)*용 Azure IoT Connector를 배포하는 방법을 알아봅니다. Azure IoT Connector for FHIR의 작동 인스턴스를 배포하기 위해 이 템플릿은 또한 상위 Azure API for FHIR 서비스와 디바이스 시뮬레이터에서 Azure IoT Connector for FHIR로 원격 분석을 내보내는 Azure IoT Central 애플리케이션을 배포합니다. ARM 템플릿을 실행하여 Azure Portal, PowerShell 또는 CLI를 통해 Azure IoT Connector for FHIR을 배포할 수 있습니다.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 로그인하면 Azure Portal에서 템플릿이 열립니다.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure Portal에서 ARM 템플릿을 사용하여 Azure IoT Connector for FHIR을 Azure에 배포합니다.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

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

[템플릿](https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json)은 다음 Azure 리소스를 정의합니다.

* **Microsoft.HealthcareApis/services**
* **Microsoft.HealthcareApis/services/iomtconnectors**
* **Microsoft.IoTCentral/IoTApps**

## <a name="deploy-the-template"></a>템플릿 배포

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 ARM 템플릿을 사용하여 Azure IoT Connector for FHIR을 배포하려면 다음 링크를 선택합니다.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure Portal에서 ARM 템플릿을 사용하여 Azure IoT Connector for FHIR 서비스를 Azure에 배포합니다.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2Fiomt-fhir%2Fmaster%2Fdeploy%2Ftemplates%2Fmanaged%2Fazuredeploy.json)

**Azure API for FHIR 배포** 페이지에서 다음을 수행합니다.

1. 원하는 경우 **구독** 을 기본값에서 다른 구독으로 변경합니다.

2. **리소스 그룹** 에 대해 **새로 만들기** 를 선택하고, 새 리소스 그룹의 이름을 입력한 다음, **확인** 을 선택합니다.

3. 새 리소스 그룹을 만든 경우 리소스 그룹에 대한 **지역** 을 선택합니다.

4. **FHIR 서비스 이름** 에 새 Azure API for FHIR 인스턴스의 이름을 입력합니다.

5. Azure API for FHIR의 **위치** 를 선택합니다. 위치는 리소스 그룹의 지역과 같거나 다를 수 있습니다.

6. **Iot Connector 이름** 에 Azure IoT Connector for FHIR 인스턴스의 이름을 입력합니다.

7. **연결 이름** 에 Azure IoT Connector for FHIR 내에서 만든 연결의 이름을 제공합니다. 이 연결은 Azure IoT Central 애플리케이션에서 시뮬레이션된 디바이스 원격 분석을 Azure IoT Connector for FHIR로 푸시하는 데 사용됩니다.

8. **Iot Central 이름** 에 새 Azure IoT Central 애플리케이션의 이름을 입력합니다. 이 애플리케이션은 *연속 환자 모니터링* 템플릿을 사용하여 디바이스를 시뮬레이션합니다.

9. **IoT Central 위치** 드롭다운에서 IoT Central 애플리케이션의 위치를 선택합니다. 

10. **검토 + 만들기** 를 선택합니다.

11. 사용 약관을 읽은 다음, **만들기** 를 선택합니다.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> PowerShell 스크립트를 로컬로 실행하려면 먼저 `Connect-AzAccount`를 입력하여 Azure 자격 증명을 설정합니다.

`Microsoft.HealthcareApis` 리소스 공급자가 아직 구독에 등록되지 않은 경우 다음 대화형 코드를 사용하여 등록할 수 있습니다. Azure Cloud Shell에서 코드를 실행하려면 코드 블록의 위쪽 모서리에 있는 **사용해 보세요** 를 선택합니다.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

`Microsoft.IoTCentral` 리소스 공급자가 아직 구독에 등록되지 않은 경우 다음 대화형 코드를 사용하여 등록할 수 있습니다. Azure Cloud Shell에서 코드를 실행하려면 코드 블록의 위쪽 모서리에 있는 **사용해 보세요** 를 선택합니다.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.IoTCentral
```

ARM 템플릿을 사용하여 Azure IoT Connector for FHIR을 배포하려면 다음 코드를 사용합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$fhirServiceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$location = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$iotConnectorName = Read-Host -Prompt "Enter a name for the new Azure IoT Connector for FHIR"
$connectionName = Read-Host -Prompt "Enter a name for the connection with Azure IoT Connector for FHIR"
$iotCentralName = Read-Host -Prompt "Enter a name for the new Azure IoT Central Application"
$iotCentralLocation = Read-Host -Prompt "Enter a location for the new Azure IoT Central Application"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure IoT Connector for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json `
    -fhirServiceName $fhirServiceName `
    -location $location
    -iotConnectorName $iotConnectorName
    -connectionName $connectionName
    -iotCentralName $iotCentralName
    -iotCentralLocation $iotCentralLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

`Microsoft.HealthcareApis` 리소스 공급자가 아직 구독에 등록되지 않은 경우 다음 대화형 코드를 사용하여 등록할 수 있습니다. Azure Cloud Shell에서 코드를 실행하려면 코드 블록의 위쪽 모서리에 있는 **사용해 보세요** 를 선택합니다.

```azurecli-interactive
az extension add --name healthcareapis
```

`Microsoft.IoTCentral` 리소스 공급자가 아직 구독에 등록되지 않은 경우 다음 대화형 코드를 사용하여 등록할 수 있습니다.

```azurecli-interactive
az extension add --name azure-iot
```

ARM 템플릿을 사용하여 Azure IoT Connector for FHIR을 배포하려면 다음 코드를 사용합니다.

```azurecli-interactive
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter a name for the new Azure API for FHIR service: " fhirServiceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " location &&
read -p "Enter a name for the new Azure IoT Connector for FHIR: " iotConnectorName &&
read -p "Enter a name for the connection with Azure IoT Connector for FHIR: " connectionName &&
read -p "Enter a name for the new Azure IoT Central Application: " iotCentralName &&
read -p "Enter a location for the new Azure IoT Central Application: " iotCentralLocation &&

params='fhirServiceName='$fhirServiceName' location='$location' iotConnectorName='$iotConnectorName' connectionName='$connectionName' iotCentralName='$iotCentralName' iotCentralLocation='$iotCentralLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure IoT Connector for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/microsoft/iomt-fhir/master/deploy/templates/managed/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> 배포를 완료하는 데 몇 분이 걸립니다. 나중에 배포된 리소스를 검토하는 데 사용하는 Azure API for FHIR, Azure IoT Central 애플리케이션 및 리소스 그룹의 이름을 적어두세요.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

# <a name="portal"></a>[포털](#tab/azure-portal)

다음 단계에 따라 새 Azure API for FHIR 서비스에 대한 개요를 확인합니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure API for FHIR** 을 검색하여 선택합니다.

2. FHIR 목록에서 새 서비스를 선택합니다. 새 Azure API for FHIR에 대한 **개요** 페이지가 표시됩니다.

3. 새 FHIR API 계정이 프로비저닝되었는지 확인하려면 **FHIR 메타데이터 엔드포인트** 옆에 있는 링크를 선택하여 FHIR API 기능 문을 가져옵니다. 링크의 형식은 `https://<service-name>.azurehealthcareapis.com/metadata`입니다. 계정이 프로비저닝되면 큰 JSON 파일이 표시됩니다.

4. 새 Azure IoT Connector for FHIR이 프로비저닝되었는지 확인하려면 왼쪽 탐색 메뉴에서 **IoT Connector(미리 보기)** 를 선택하여 **IoT Connectors** 페이지를 엽니다. 페이지에는 프로비저닝된 Azure IoT Connector for FHIR의 *상태* 값이 *온라인* 으로, *연결* 값이 *1* 로 표시되어야 하며, *디바이스 매핑* 및 *FHIR 매핑* 은 모두 *성공* 아이콘이 표시되어야 합니다.

5. [Azure Portal](https://portal.azure.com)에서 **IoT Central Applications** 를 검색하여 선택합니다.

6. IoT Central Applications 목록에서 새 서비스를 선택합니다. 새 IoT Central 애플리케이션의 **개요** 페이지가 나타납니다.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

다음 대화형 코드를 실행하여 Azure API for FHIR 서비스에 대한 세부 정보를 확인합니다. 새 FHIR 서비스 및 리소스 그룹의 이름을 입력해야 합니다.

```azurepowershell-interactive
$fhirServiceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new FHIR service has been provisioned"

$requestUri="https://" + $fhirServiceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

> [!NOTE]
> Azure IoT Connector for FHIR은 현재 PowerShell 명령을 제공하지 않습니다. Azure IoT Connector for FHIR이 올바르게 프로비저닝되었는지 확인하려면 **포털** 탭에 제공된 유효성 검사 프로세스를 사용합니다.

Azure IoT Central 애플리케이션에 대한 세부 정보를 보려면 다음 대화형 코드를 실행합니다. 새 IoT Central 애플리케이션 및 리소스 그룹의 이름을 입력해야 합니다.

```azurepowershell-interactive
$iotCentralName = Read-Host -Prompt "Enter the name of your Azure IoT Central application"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName" -Verbose
Get-AzIotCentralApp -ResourceGroupName $resourceGroupName -Name $iotCentralName
```

# <a name="cli"></a>[CLI](#tab/CLI)

다음 대화형 코드를 실행하여 Azure API for FHIR 서비스에 대한 세부 정보를 확인합니다. 새 FHIR 서비스 및 리소스 그룹의 이름을 입력해야 합니다.

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " fhirServiceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $fhirServiceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$fhirServiceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
read -p "Press [ENTER] to continue: "
```

> [!NOTE]
> Azure IoT Connector for FHIR은 현재 CLI 명령을 제공하지 않습니다. Azure IoT Connector for FHIR이 올바르게 프로비저닝되었는지 확인하려면 **포털** 탭에 제공된 유효성 검사 프로세스를 사용합니다.

Azure IoT Central 애플리케이션에 대한 세부 정보를 보려면 다음 대화형 코드를 실행합니다. 새 IoT Central 애플리케이션 및 리소스 그룹의 이름을 입력해야 합니다.

```azurecli-interactive
read -p "Enter the name of your IoT Central application: " iotCentralName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az iot central app show -g $resourceGroupName -n $iotCentralName" &&
az iot central app show -g $resourceGroupName -n $iotCentralName &&
```

---

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR(미리 보기)과 IoT 데이터 연결
> [!IMPORTANT]
> 이 가이드에서 제공하는 디바이스 매핑 템플릿은 IoT Central 내에서 데이터 내보내기(레거시)를 사용하도록 설계되었습니다.

IoT Central 애플리케이션은 현재 데이터 내보내기를 설정하기 위한 ARM 템플릿 또는 PowerShell 및 CLI 명령을 제공하지 않습니다. 따라서 Azure Portal을 사용하여 아래의 지침을 따르세요.  

IoT Central 애플리케이션을 배포한 후에는 두 개의 기본 시뮬레이션 디바이스가 원격 분석 데이터를 생성하기 시작합니다. 이 자습서에서는 Azure IoT Connector for FHIR을 통해 *Smart Vitals Patch* 시뮬레이터에서 FHIR로 원격 분석 데이터를 수집합니다. IoT 데이터를 Azure IoT Connector for FHIR로 내보내려면 [IoT Central 내에서 데이터 내보내기(레거시)를 설정](../../iot-central/core/howto-export-data-legacy.md)합니다. 데이터 내보내기(레거시) 페이지에서 다음을 수행합니다.
- 내보내기 대상으로 *Azure Event Hubs* 를 선택합니다.
- **Event Hubs 네임스페이스** 에 대해 *연결 문자열 사용* 값을 선택합니다.
- 이전 단계에서 얻은 Azure IoT Connector for FHIR의 연결 문자열을 **연결 문자열** 필드에 입력합니다.
- **내보낼 데이터** 필드에서 **원격 분석** 옵션을 *켜기* 로 유지합니다.

---

## <a name="view-device-data-in-azure-api-for-fhir"></a>Azure API for FHIR에서 디바이스 데이터 보기

postman을 사용 하 여 fhir 서비스에 대 한 Azure IoT 커넥터에서 만든 fhir 기반 관찰 리소스를 볼 수 있습니다. 자세한 내용은 [Postman을 사용 하 여 fhir 서비스에 액세스](./../use-postman.md) 를 참조 하 고 `GET` 를 요청 하 여 `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` 심장 rate value로 관찰 된 리소스를 확인 합니다.

> [!TIP]
> 사용자에게 Azure API for FHIR 데이터 평면에 액세스할 수 있는 적절한 권한이 있어야 합니다. [Azure RBAC(Azure 역할 기반 액세스 제어)](configure-azure-rbac.md)를 사용하여 필요한 데이터 평면 역할을 할당합니다.

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

이 빠른 시작 가이드에서는 Azure API for FHIR 리소스에 Azure IoT Connector for FHIR 기능을 배포했습니다. Azure IoT Connector for FHIR에 대해 자세히 알아보려면 아래 단계 중에서 선택하세요.

Azure IoT Connector for FHIR 내에서 데이터 흐름의 여러 단계를 이해합니다.

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR 데이터 흐름](iot-data-flow.md)

디바이스 및 FHIR 매핑 템플릿을 사용하여 IoT 커넥터를 구성하는 방법을 알아봅니다.

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR 매핑 템플릿](iot-mapping-templates.md)

*Azure Portal에서는 Azure IoT Connector for FHIR을 IoT 커넥터(미리 보기)라고 합니다. FHIR은 HL7의 등록 상표이며, HL7의 사용 허가 하에 사용됩니다.
