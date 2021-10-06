---
title: 자습서 - Azure IoT Hub 메시지 보강 사용
description: Azure IoT Hub 메시지에 메시지 보강을 사용하는 방법을 보여주는 자습서
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: aca884ebbfc10cc847bade34ea1a9bd73ada18c9
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458400"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>자습서: Azure IoT Hub 메시지 보강 사용

*메시지 보강* 이란 메시지를 지정된 엔드포인트로 보내기 전에 추가 정보를 메시지에 *스탬핑* 하는 Azure IoT Hub의 기능입니다. 메시지 보강을 사용하는 한 가지 이유는 다운스트림 처리를 간소화하는 데 사용할 수 있는 데이터를 포함하는 것입니다. 예를 들어 디바이스 원격 분석 메시지를 디바이스 쌍 태그로 보강하면 고객이 이 정보를 얻기 위해 디바이스 쌍 API를 호출해야 하는 부담을 줄일 수 있습니다. 자세한 내용은 [메시지 보강 개요](iot-hub-message-enrichments-overview.md)를 참조하세요.

이 자습서에서는 IoT 허브에 대한 메시지 보강을 테스트하는 데 필요한 리소스를 만들고 구성하는 두 가지 방법을 볼 수 있습니다. 리소스는 두 개의 스토리지 컨테이너가 포함된 스토리지 계정 하나입니다. 한 컨테이너는 보강된 메시지를 보관하고 다른 컨테이너는 원래 메시지를 보관합니다. 메시지를 수신하고 메시지의 보강 여부에 따라 적절한 스토리지 컨테이너로 라우팅하는 IoT 허브도 포함되어 있습니다.

* 첫 번째 방법은 Azure CLI를 사용하여 리소스를 만들고 메시지 라우팅을 구성하는 것입니다. 그런 다음, [Azure Portal](https://portal.azure.com)을 사용하여 수동으로 보강을 정의합니다.

* 두 번째 방법은 Azure Resource Manager 템플릿을 사용하여 메시지 라우팅 및 메시지 보강을 위한 리소스 *및* 구성을 모두 만드는 것입니다.

메시지 라우팅 및 메시지 보강의 구성을 완료한 후에는 애플리케이션을 사용하여 IoT 허브에 메시지를 보냅니다. 그러면 허브에서는 메시지를 두 스토리지 컨테이너에 모두 라우팅합니다. **enriched** 스토리지 컨테이너의 엔드포인트에 전송된 메시지만 보강됩니다.

다음은 이 자습서를 완료하기 위해 수행해야 하는 작업입니다.

**IoT Hub 메시지 보강 사용**
> [!div class="checklist"]
> * 첫 번째 방법: Azure CLI를 사용하여 리소스를 만들고 메시지 라우팅을 구성합니다. [Azure Portal](https://portal.azure.com)을 사용하여 수동으로 메시지 보강을 구성합니다.
> * 두 번째 방법: Resource Manager 템플릿을 사용하여 리소스를 만들고 메시지 라우팅 및 메시지 보강을 구성합니다. 
> * 허브로 메시지를 전송하는 IoT 디바이스를 시뮬레이션하는 앱을 실행합니다.
> * 결과를 살펴보고, 메시지 보강이 예상대로 작동하는지 확인합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 있어야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

- [Visual Studio](https://www.visualstudio.com/)를 설치합니다.

- 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 자습서의 디바이스 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>IoT C# 샘플 리포지토리 검색

GitHub에서 [IoT C# 샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/main.zip)을 다운로드하여 압축을 풉니다. 이 리포지토리에는 여러 애플리케이션, 스크립트 및 Resource Manager 템플릿이 들어 있습니다. 이 자습서에 사용할 항목은 다음과 같습니다.

* 수동 방법의 경우 리소스를 만드는 데 사용되는 CLI 스크립트가 있습니다. 이 스크립트는 /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli에 있습니다. 이 스크립트는 리소스를 만들고 메시지 라우팅을 구성합니다. 이 스크립트를 실행한 후 [Azure Portal](https://portal.azure.com)을 사용하여 수동으로 메시지 보강을 만듭니다.
* 자동화된 방법의 경우 Azure Resource Manager 템플릿이 있습니다. 이 템플릿은 /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.json에 있습니다. 이 템플릿은 리소스를 만들고 메시지 라우팅을 구성한 다음, 메시지 보강을 구성합니다.
* 우리가 사용할 세 번째 애플리케이션은 IoT 허브에 메시지를 보내고 메시지 보강을 테스트하는 데 사용되는 Device Simulation 앱입니다.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Azure CLI를 사용하여 수동으로 설정 및 구성

Azure CLI 스크립트는 필요한 리소스를 만들 뿐 아니라 별도의 스토리지 컨테이너인 엔드포인트에 대한 두 개의 경로를 구성합니다. 메시지 라우팅을 구성하는 방법에 대한 자세한 내용은 [라우팅 자습서](tutorial-routing.md)를 참조하세요. 리소스를 설정한 후에는 [Azure Portal](https://portal.azure.com)을 사용하여 각 엔드포인트의 메시지 보강을 구성합니다. 그런 다음, 테스트 단계를 계속 진행합니다.

> [!NOTE]
> 모든 메시지는 두 엔드포인트로 모두 라우팅되지만, 구성된 메시지 보강이 엔드포인트로 이동하는 메시지만 보강됩니다.
>

이어지는 스크립트를 사용해도 되고, 다운로드한 리포지토리의 /resources 폴더에 있는 스크립트를 열어도 됩니다. 스크립트에서 수행하는 단계는 다음과 같습니다.

* IoT Hub를 만듭니다.
* 스토리지 계정을 만듭니다.
* 스토리지 계정에 두 개의 컨테이너를 만듭니다. 한 컨테이너는 보강된 메시지를 보관하고, 다른 컨테이너는 보강되지 않은 메시지를 보관합니다.
* 다음과 같이 두 개의 스토리지 계정에 대한 라우팅을 설정합니다.
    * 각 스토리지 계정 컨테이너의 엔드포인트를 만듭니다.
    * 각 스토리지 계정 컨테이너 엔드포인트에 대한 경로를 만듭니다.

IoT 허브 이름이나 스토리지 계정 이름처럼 전역적으로 고유해야 하는 여러 가지 리소스 이름이 있습니다. 스크립트를 더 쉽게 실행할 수 있도록 이러한 리소스 이름에는 *randomValue* 라는 임의의 영숫자 값이 추가됩니다. 임의의 값은 스크립트 맨 위에서 한 번 생성됩니다. 임의의 값은 스크립트 전체에서 필요에 따라 리소스 이름에 추가됩니다. 임의의 값을 원하지 않는 경우 빈 문자열이나 특정 값으로 설정할 수 있습니다.

아직 Azure [Cloud Shell 창](https://shell.azure.com)을 열지 않았으면 지금 열고 Bash로 설정되었는지 확인합니다. 압축을 푼 리포지토리에서 스크립트를 열고 Ctrl+A 키를 선택하여 모든 항목을 선택한 다음, Ctrl+C 키를 선택하여 복사합니다. 또는 다음 CLI 스크립트를 복사하거나 Cloud Shell에서 직접 열 수도 있습니다. 명령줄을 마우스 오른쪽 단추로 클릭하고 **붙여넣기** 를 선택하여 Cloud Shell 창에 스크립트를 붙여넣습니다. 스크립트는 한 번에 하나의 명령문을 실행합니다. 스크립트가 실행을 멈추면 **Enter** 키를 선택하여 마지막 명령이 실행되는지 확인합니다. 다음 코드 블록은 사용되는 스크립트와 해당 스크립트가 하는 일을 설명하는 주석을 보여줍니다.

다음은 스크립트에서 만든 리소스입니다. *enriched* 는 보강된 메시지에 대한 리소스라는 뜻입니다. *original* 은 보강되지 않은 메시지에 대한 리소스라는 뜻입니다.

| Name | 값 |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| 컨테이너 이름 | 오리지널  |
| 컨테이너 이름 | enriched  |
| IoT 디바이스 이름 | Contoso-Test-Device |
| IoT Hub 이름 | ContosoTestHubMsgEn |
| 스토리지 계정 이름 | contosostorage |
| 엔드포인트 이름 1 | ContosoStorageEndpointOriginal |
| 엔드포인트 이름 2 | ContosoStorageEndpointEnriched|
| 경로 이름 1 | ContosoStorageRouteOriginal |
| 경로 이름 2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

# Set the values for the resource names that
#   don't have to be globally unique.
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
# The storage account name must be globally unique,
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key.
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for the first container, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for the second container, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

이제 리소스가 모두 설정되고 메시지 라우팅이 구성되었습니다. 포털에서 메시지 라우팅 구성을 살펴보고 **enriched** 스토리지 컨테이너로 이동하는 메시지에 대한 메시지 보강을 설정할 수 있습니다.

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Azure Portal을 사용하여 수동으로 메시지 보강 구성

1. **리소스 그룹** 을 선택하여 IoT 허브로 이동합니다. 그런 다음, 이 자습서에 맞게 설정된 리소스 그룹(**ContosoResourcesMsgEn**)을 선택합니다. 목록에서 IoT 허브를 찾아 선택합니다. IoT 허브에 대한 **메시지 라우팅** 을 선택합니다.

   ![메시지 라우팅 선택](./media/tutorial-message-enrichments/select-iot-hub.png)

   메시지 라우팅 창에는 **경로**, **사용자 지정 엔드포인트** 및 **보강 메시지** 라는 세 개의 탭이 있습니다. 처음 두 탭을 탐색하여 스크립트에서 설정한 구성을 살펴봅니다. 세 번째 탭을 사용하여 메시지 보강을 추가합니다. **enriched** 스토리지 컨테이너의 엔드포인트로 이동하는 메시지를 보강해 보겠습니다. 이름과 값을 입력한 다음, 드롭다운 목록에서 **ContosoStorageEndpointEnriched** 엔드포인트를 선택합니다. 다음은 메시지에 IoT 허브 이름을 추가하는 보강 설정 방법을 보여주는 예입니다.

   ![첫 번째 보강 추가](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. 다음 값을 ContosoStorageEndpointEnriched 엔드포인트 목록에 추가합니다.

   | 키 | 값 | 엔드포인트(드롭다운 목록) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |customerID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > 디바이스에 트윈이 없는 경우 여기에 입력하는 값은 메시지 보강의 값에 대한 문자열로 스탬핑됩니다. 디바이스 쌍 정보를 보려면 포털에서 해당 허브로 이동하여 **IoT 디바이스** 를 선택합니다. 해당 디바이스를 선택한 다음, 페이지 위쪽에서 **디바이스 쌍** 을 선택합니다.
   >
   > 쌍 정보를 편집하여 위치 등의 태그를 추가하고, 특정 값으로 설정할 수 있습니다. 자세한 내용은 [IoT Hub의 디바이스 쌍 이해 및 사용](iot-hub-devguide-device-twins.md)을 참조하세요.

3. 여기까지 마쳤으면 다음 이미지와 비슷한 창이 표시됩니다.

   ![모든 보강이 추가된 테이블](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. **적용** 을 선택하여 변경 내용을 저장합니다. [메시지 보강 테스트](#test-message-enrichments) 섹션으로 건너뜁니다.

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 만들기 및 구성
Resource Manager 템플릿을 사용하여 리소스, 메시지 라우팅 및 메시지 보강을 만들고 구성할 수 있습니다.

1. Azure Portal에 로그인합니다. **+ 리소스 만들기** 를 선택하여 검색 상자를 불러 옵니다. *템플릿 배포* 를 입력하여 검색합니다. 결과 창에서 **템플릿 배포(사용자 지정 템플릿을 사용하여 배포)** 를 선택합니다.

   ![Azure Portal의 템플릿 배포](./media/tutorial-message-enrichments/template-select-deployment.png)

1. **템플릿 배포** 창에서 **만들기** 를 선택합니다.

1. **사용자 지정 배포** 창에서 **편집기에서 사용자 고유의 템플릿을 빌드합니다** 를 선택합니다.

1. **템플릿 편집** 창에서 **파일 로드** 를 선택합니다. Windows 탐색기가 나타납니다. **/iot-hub/Tutorials/Routing/SimulatedDevice/resources** 의 압축을 푼 리포지토리 파일에서 **template_messageenrichments.json** 파일을 찾습니다. 

   ![로컬 머신에서 템플릿 선택](./media/tutorial-message-enrichments/template-select.png)

1. **열기** 를 선택하여 로컬 머신의 템플릿 파일을 로드합니다. 템플릿 파일이 로드되어 편집 창에 표시됩니다.

   이 템플릿은 기본 이름의 끝에 임의의 값을 추가하여 전역적으로 고유한 IoT 허브 이름과 스토리지 계정 이름을 사용하도록 설정되므로, 템플릿을 변경할 필요 없이 사용할 수 있습니다.

   템플릿을 로드하여 만든 리소스는 다음과 같습니다. **enriched** 는 보강된 메시지에 대한 리소스라는 뜻입니다. **original** 은 보강되지 않은 메시지에 대한 리소스라는 뜻입니다. Azure CLI 스크립트에 사용된 값과 동일한 값입니다.

   | Name | 값 |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | 컨테이너 이름 | 오리지널  |
   | 컨테이너 이름 | enriched  |
   | IoT 디바이스 이름 | Contoso-Test-Device |
   | IoT Hub 이름 | ContosoTestHubMsgEn |
   | 스토리지 계정 이름 | contosostorage |
   | 엔드포인트 이름 1 | ContosoStorageEndpointOriginal |
   | 엔드포인트 이름 2 | ContosoStorageEndpointEnriched|
   | 경로 이름 1 | ContosoStorageRouteOriginal |
   | 경로 이름 2 | ContosoStorageRouteEnriched |

1. **저장** 을 선택합니다. **사용자 지정 배포** 창이 나타나고 템플릿에서 사용하는 모든 매개 변수가 표시됩니다. **리소스 그룹** 필드만 설정하면 됩니다. 새로 만들거나 드롭다운 목록에서 하나를 선택합니다.

   다음은 **사용자 지정 배포** 창의 위쪽 절반입니다. 리소스 그룹을 입력하는 위치를 볼 수 있습니다.

   ![사용자 지정 배포 창의 위쪽 절반](./media/tutorial-message-enrichments/template-deployment-top.png)

1. 다음은 **사용자 지정 배포** 창의 아래쪽 절반입니다. 나머지 매개 변수와 사용 약관을 볼 수 있습니다. 

   ![사용자 지정 배포 창의 아래쪽 절반](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. 사용 약관에 동의하는 확인란을 선택합니다. 그런 다음, **구매** 를 선택하여 템플릿 배포를 계속합니다.

1. 템플릿이 완전히 배포될 때까지 기다립니다. 화면 위쪽에 있는 종 모양 아이콘을 선택하여 진행률을 확인합니다. 배포가 완료되면 [메시지 보강 테스트](#test-message-enrichments) 섹션을 계속 진행합니다.

## <a name="test-message-enrichments"></a>메시지 보강 테스트

메시지 보강을 보려면 **리소스 그룹** 을 선택합니다. 그런 다음, 이 자습서에 사용 중인 리소스 그룹을 선택합니다. 리소스 목록에서 IoT 허브를 선택하고 **메시지** 로 이동합니다. 메시지 라우팅 구성 및 구성된 보강이 표시됩니다.

엔드포인트에 대한 메시지 보강이 구성되었으므로, Simulated Device 애플리케이션을 실행하여 IoT 허브로 메시지를 보냅니다. 허브는 다음 작업을 수행하는 설정으로 지정되었습니다.

* 스토리지 엔드포인트 ContosoStorageEndpointOriginal로 라우팅되는 메시지는 보강되지 않으며 `original` 스토리지 컨테이너에 저장됩니다.

* 스토리지 엔드포인트 ContosoStorageEndpointEnriched로 라우팅되는 메시지는 보강되며 `enriched` 스토리지 컨테이너에 저장됩니다.

Simulated Device 애플리케이션은 압축을 푼 다운로드의 애플리케이션 중 하나입니다. 이 애플리케이션은 [라우팅 자습서](tutorial-routing.md)의 각 메시지 라우팅 방법에 맞는 메시지를 전송하며, 여기에는 Azure Storage가 포함됩니다.

**IoT_SimulatedDevice.sln** 솔루션 파일을 두 번 클릭하여 Visual Studio에서 코드를 연 다음, **Program.cs** 를 엽니다. `{your hub name}` 마커를 IoT 허브 이름으로 바꿉니다. IoT 허브 호스트 이름의 형식은 **{your hub name}.azure-devices.net** 입니다. 이 자습서의 경우 허브 호스트 이름은 ContosoTestHubMsgEn.azure-devices.net입니다. 다음으로, 앞에서 스크립트를 실행하여 `{your device key}` 마커의 리소스를 만들 때 저장한 디바이스 키를 바꿉니다.

디바이스 키가 없는 경우 포털에서 검색할 수 있습니다. 로그인한 후 **리소스 그룹** 으로 이동하여 해당 리소스 그룹을 선택한 다음, IoT 허브를 선택합니다. **IoT 디바이스** 에서 테스트 디바이스를 찾아서 선택합니다. **기본 키** 옆의 복사 아이콘을 선택하여 클립보드에 복사합니다.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>실행 및 테스트

몇 분 동안 콘솔 애플리케이션을 실행합니다. 전송되는 메시지는 애플리케이션의 콘솔 화면에 표시됩니다.

앱은 1초마다 새로운 디바이스-클라우드 메시지를 IoT Hub에 보냅니다. 메시지에는 디바이스 ID, 온도, 습도 및 메시지 수준(기본값이 `normal`)과 함께 JSON 직렬화된 개체가 포함됩니다. `critical` 또는 `storage` 수준을 임의로 할당하며, 이에 따라 메시지가 스토리지 계정 또는 기본 엔드포인트로 라우팅됩니다. 스토리지 계정의 **enriched** 컨테이너로 전송된 메시지는 보강됩니다.

스토리지 메시지가 여러 개 전송된 후 데이터를 봅니다.

1. **리소스 그룹** 을 선택합니다. 리소스 그룹 **ContosoResourcesMsgEn** 을 찾아서 선택합니다.

2. 해당하는 스토리지 계정(**contosostorage**)을 선택합니다. 그런 다음, 왼쪽 창에서 **Storage Explorer(미리 보기)** 를 선택합니다.

   ![Storage Explorer 선택](./media/tutorial-message-enrichments/select-storage-explorer.png)

   **BLOB 컨테이너** 를 선택하여 사용 가능한 두 가지 컨테이너를 살펴봅니다.

   ![스토리지 계정의 컨테이너 살펴보기](./media/tutorial-message-enrichments/show-blob-containers.png)

**enriched** 컨테이너의 메시지는 메시지 보강을 포함하고 있습니다. **original** 컨테이너의 메시지는 보강되지 않은 원래 메시지입니다. 맨 아래에 도달할 때까지 컨테이너 중 하나를 드릴다운하고, 가장 최근 메시지 파일을 엽니다. 그런 다음, 다른 컨테이너에도 동일한 작업을 수행하여 해당 컨테이너의 메시지에 보강이 추가되지 않았는지 확인합니다.

보강된 메시지를 살펴보면 다음과 같이 "my IoT Hub"가 허브 이름과 위치 및 고객 ID로 보강된 것을 볼 수 있습니다.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

다음은 보강되지 않은 메시지입니다. "my IoT Hub"를 보면 "devicelocation" 및 "customerID"가 표시되지 않습니다. 이러한 필드는 보강을 통해 추가되기 때문입니다. 이 엔드포인트에는 보강이 없습니다.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 리소스를 모두 제거하려면 리소스 그룹을 삭제합니다. 이렇게 하면 그룹 내에 포함된 모든 리소스가 삭제됩니다. 이 경우 IoT Hub, 스토리지 계정 및 리소스 그룹 자체가 제거됩니다.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Azure CLI를 사용하여 리소스 정리

리소스 그룹을 제거하려면 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용합니다. 이 자습서를 시작할 때 `$resourceGroup`을 **ContosoResourcesMsgEn** 으로 설정했습니다.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 단계에 따라 IoT Hub 메시지에 메시지 보강을 추가하도록 구성하고 테스트했습니다.

**IoT Hub 메시지 보강 사용**

> [!div class="checklist"]
> * 첫 번째 방법: Azure CLI를 사용하여 리소스를 만들고 메시지 라우팅을 구성합니다. [Azure Portal](https://portal.azure.com)을 사용하여 수동으로 메시지 보강을 구성합니다.
> * 두 번째 방법: Azure Resource Manager 템플릿을 사용하여 리소스를 만들고 메시지 라우팅 및 메시지 보강을 구성합니다.
> * 허브로 메시지를 전송하는 IoT 디바이스를 시뮬레이션하는 앱을 실행합니다.
> * 결과를 살펴보고, 메시지 보강이 예상대로 작동하는지 확인합니다.

메시지 보강에 대한 자세한 내용은 [메시지 보강 개요](iot-hub-message-enrichments-overview.md)를 참조하세요.

메시지 라우팅에 대한 자세한 내용은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [IoT Hub 메시지 라우팅을 사용하여 디바이스-클라우드 메시지를 다른 엔드포인트에 보내기](iot-hub-devguide-messages-d2c.md)

> [!div class="nextstepaction"]
> [자습서: IoT Hub 라우팅](tutorial-routing.md)