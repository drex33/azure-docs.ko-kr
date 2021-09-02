---
title: 자습서 - Azure CLI를 사용하여 Azure IoT Hub에 대한 메시지 라우팅 구성
description: 자습서 - Azure CLI를 사용하여 Azure IoT Hub에 대한 메시지 라우팅을 구성합니다. 메시지의 속성에 따라 스토리지 계정 또는 Service Bus 큐로 라우팅합니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 8/20/2021
ms.author: robinsh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 117b2cee63e8defb53e21cf05c76c344006ddae3
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829324"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>자습서: Azure CLI를 사용하여 IoT Hub 메시지 라우팅 구성

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>스크립트 다운로드(선택 사항)

이 자습서의 두 번째 부분에서는 Visual Studio 애플리케이션을 다운로드하고 실행하여 IoT Hub에 메시지를 보냅니다. 이 다운로드에는 Azure Resource Manager 템플릿과 매개 변수 파일은 물론 Azure CLI와 PowerShell 스크립트가 포함된 폴더가 있습니다.

완성된 스크립트를 보려면 [Azure IoT C# 샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)을 다운로드합니다. master.zip 파일의 압축을 풉니다. Azure CLI 스크립트는 /iot-hub/Tutorials/Routing/SimulatedDevice/resources/의 **iothub_routing_cli.azcli** 에 있습니다.

## <a name="use-the-azure-cli-to-create-your-resources"></a>Azure CLI를 사용하여 리소스 만들기

아래 스크립트를 복사하여 Cloud Shell에 붙여넣고 Enter 키를 누릅니다. 스크립트가 한 번에 한 줄씩 실행됩니다. 스크립트의 첫 번째 섹션은 이 자습서의 기본 리소스(스토리지 계정, IoT Hub, Service Bus 네임스페이스 및 Service Bus 큐)를 만듭니다. 자습서의 나머지 부분을 진행하면서 스크립트의 각 블록을 복사하여 Cloud Shell에 붙여넣고 실행합니다.

> [!TIP]
> 디버깅 팁: 이 스크립트는 연속 기호(백슬래시 `\`)를 사용하여 스크립트를 더 읽기 쉽게 만듭니다. 스크립트를 실행하는 데 문제가 있으면 Cloud Shell 세션이 `bash`를 실행 중이고 백슬래시 뒤에 공백이 없는지 확인하세요.
> 

몇 가지 리소스 이름(예: IoT Hub 이름 및 스토리지 계정 이름)은 전역적으로 고유해야 합니다. 이를 위해 해당 리소스 이름에는 *randomValue* 라는 임의의 영숫자 값이 추가됩니다. randomValue는 스크립트의 맨 위에 한 번 생성되고 전체 스크립트에서 필요에 따라 리소스에 추가됩니다. 임의로 설정하지 않으려면 빈 문자열이나 특정 값으로 설정할 수 있습니다. 

> [!IMPORTANT]
> 초기 스크립트에 설정된 변수는 라우팅 스크립트에도 사용되기 때문에 모든 스크립트를 동일한 Cloud Shell 세션에서 실행합니다. 새 세션을 열어서 라우팅을 설정하기 위한 스크립트를 실행하면, 여러 변수가 누락된 값이 됩니다.
>

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
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
iotHubName=ContosoTestHub$randomValue 
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub for the 'events' endpoint.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

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
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# The Service Bus namespace must be globally unique, 
#   so add a random value to the end.
sbNamespace=ContosoSBNamespace$randomValue
echo "Service Bus namespace = " $sbNamespace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNamespace \
    --location $location

# The Service Bus queue name must be globally unique, 
#   so add a random value to the end.
sbQueueName=ContosoSBQueue$randomValue
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNamespace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName
```

기본 리소스가 설정되었으면, 메시지 라우팅을 구성할 수 있습니다.

## <a name="set-up-message-routing"></a>메시지 라우팅 설정

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

라우팅 엔드포인트를 만들려면 [az iot hub routing-endpoint create](/cli/azure/iot/hub/routing-endpoint#az_iot_hub_routing_endpoint_create)를 사용합니다. 엔드포인트에 대한 메시지 경로를 만들려면 [az iot hub route create](/cli/azure/iot/hub/route#az_iot_hub_route_create)를 사용합니다.

### <a name="route-to-a-storage-account"></a>스토리지 계정으로 라우팅

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

먼저 스토리지 계정에 대한 엔드포인트를 설정한 다음, 경로를 설정합니다. 

Cloud Shell 세션 내에서 설정해야 하는 스크립트에 사용되는 변수는 다음과 같습니다.

**storageConnectionString**: 이 값은 이전 스크립트에서 설정한 스토리지 계정에서 검색됩니다. 메시지 라우팅을 통해 스토리지 계정에 액세스하는 데 사용됩니다.

  **resourceGroup**: 리소스 그룹은 두 번 발생합니다. 둘 다 리소스 그룹에 설정합니다.

**endpoint subscriptionID**: 이 필드는 엔드포인트에 대한 Azure subscriptionID로 설정됩니다. 

**endpointType**: 이 필드는 엔드포인트의 형식입니다. 이 값은 `azurestoragecontainer`, `eventhub`, `servicebusqueue` 또는 `servicebustopic`으로 설정해야 합니다. 여기에서는 `azurestoragecontainer`로 설정합니다.

**iotHubName**: 이 필드는 라우팅을 수행하는 허브의 이름입니다.

**containerName**: 이 필드는 데이터가 작성될 스토리지 계정의 컨테이너 이름입니다.

**encoding**: 이 필드는 `avro` 또는 `json`입니다. 저장된 데이터의 형식을 나타냅니다.

**routeName**: 이 필드는 설정하는 경로의 이름입니다 

**endpointName**: 이 필드는 엔드포인트를 식별하는 이름입니다. 

**enabled**: 이 필드의 기본값은 `true`이며, 메시지 경로가 생성된 후 사용하도록 설정되어야 한다는 것을 나타냅니다.

**condition**: 이 필드는 이 엔드포인트에 보낸 메시지를 필터링하는 데 사용되는 쿼리입니다. 스토리지로 라우팅되는 메시지의 쿼리 조건은 `level="storage"`입니다.

이 스크립트를 복사하여 Cloud Shell 창에 붙여넣고 실행합니다.

```azurecli
##### ROUTING FOR STORAGE ##### 

endpointName="ContosoStorageEndpoint"
endpointType="azurestoragecontainer"
routeName="ContosoStorageRoute"
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)
```

다음 단계에서는 스토리지 계정에 대한 라우팅 엔드포인트를 만듭니다. 결과가 저장될 컨테이너도 지정합니다. 컨테이너는 스토리지 계정이 만들어졌을 때 이전에 생성되었습니다.

```azurecli
# Create the routing endpoint for storage.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName \
  --resource-group $resourceGroup \
  --encoding avro
```

다음으로, 스토리지 엔드포인트에 대한 경로를 만듭니다. 메시지 경로는 쿼리 사양을 충족하는 메시지를 보낼 위치를 지정합니다. 

```azurecli
# Create the route for the storage endpoint.
az iot hub route create \
  --name $routeName \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
```

### <a name="route-to-a-service-bus-queue"></a>Service Bus 큐에 라우팅

이제 Service Bus 큐에 대한 라우팅을 설정합니다. Service Bus 큐에 대한 연결 문자열을 검색하려면, 올바른 권한이 정의된 권한 부여 규칙을 만들어야 합니다. 다음 스크립트는 `sbauthrule`이라는 Service Bus 큐에 대한 권한 부여 규칙을 만들고 권한을 `Listen Manage Send`로 설정합니다. 권한 부여 규칙이 정의되면, 이것을 사용하여 큐에 대한 연결 문자열을 검색할 수 있습니다.

```azurecli
# Create the authorization rule for the Service Bus queue.
az servicebus queue authorization-rule create \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --rights Listen Manage Send \
  --subscription $subscriptionID
```

이제 권한 부여 규칙을 사용하여 Service Bus 큐에 대한 연결 문자열을 검색합니다.

```azurecli
# Get the Service Bus queue connection string.
# The "-o tsv" ensures it is returned without the default double-quotes.
sbqConnectionString=$(az servicebus queue authorization-rule keys list \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --subscription $subscriptionID \
  --query primaryConnectionString -o tsv)

# Show the Service Bus queue connection string.
echo "service bus queue connection string = " $sbqConnectionString
```

이제 Service Bus 큐에 대한 메시지 경로와 라우팅 엔드포인트를 설정합니다. Cloud Shell 세션 내에서 설정해야 하는 스크립트에 사용되는 변수는 다음과 같습니다.

**endpointName**: 이 필드는 엔드포인트를 식별하는 이름입니다. 

**endpointType**: 이 필드는 엔드포인트의 형식입니다. 이 값은 `azurestoragecontainer`, `eventhub`, `servicebusqueue` 또는 `servicebustopic`으로 설정해야 합니다. 여기에서는 `servicebusqueue`로 설정합니다.

**routeName**: 이 필드는 설정하는 경로의 이름입니다 

**condition**: 이 필드는 이 엔드포인트에 보낸 메시지를 필터링하는 데 사용되는 쿼리입니다. Service Bus 큐로 라우팅되는 메시지의 쿼리 조건은 `level="critical"`입니다.

다음은 Service Bus 큐에 대한 메시지 경로와 라우팅 엔드포인트에 대한 Azure CLI입니다.

```azurecli
endpointName="ContosoSBQueueEndpoint"
endpointType="ServiceBusQueue"
routeName="ContosoSBQueueRoute"
condition='level="critical"'

# Set up the routing endpoint for the Service Bus queue.
# This uses the Service Bus queue connection string.
az iot hub routing-endpoint create \
  --connection-string $sbqConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --resource-group $resourceGroup 

# Set up the message route for the Service Bus queue endpoint.
az iot hub route create --name $routeName \
  --hub-name $iotHubName \
  --source-type devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
  ```

### <a name="view-message-routing-in-the-portal"></a>포털에서 메시지 라우팅 보기

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>다음 단계

리소스가 설정되고 메시지 경로가 구성되었으면 다음 자습서로 진행하여, IoT 허브에 메시지를 보낸 후 다른 대상으로 라우팅되는 과정을 확인하는 방법을 살펴보세요. 

> [!div class="nextstepaction"]
> [2부 - 메시지 라우팅 결과 보기](tutorial-routing-view-message-routing-results.md)
