---
title: 자습서 - Azure CLI를 사용하여 Azure IoT Hub에 대한 메시지 라우팅 구성
description: 자습서 - Azure CLI 및 Azure Portal을 사용하여 Azure IoT Hub에 대한 메시지 라우팅 구성
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 08/16/2021
ms.author: lizross
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: fbe2108a50e29b91c8c324201085d9ba1f6969bc
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550144"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>자습서: Azure CLI 및 Azure Portal을 사용하여 IoT Hub 메시지 라우팅 구성

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>Azure CLI를 사용하여 기본 리소스 만들기

이 자습서에서는 Azure CLI를 사용하여 기본 리소스를 만든 다음, [Azure Portal](https://portal.azure.com)을 사용하여 메시지 라우팅을 구성하고 테스트용 가상 디바이스를 설정하는 방법을 보여줍니다.

아래 스크립트를 복사하여 Cloud Shell에 붙여넣고 Enter 키를 누릅니다. 스크립트가 한 번에 한 줄씩 실행됩니다. 이 스크립트는 이 자습서의 기본 리소스(스토리지 계정, IoT Hub, Service Bus 네임스페이스 및 Service Bus 큐)를 만듭니다.

몇 가지 리소스 이름(예: IoT Hub 이름 및 스토리지 계정 이름)은 전역적으로 고유해야 합니다. 이를 위해 해당 리소스 이름에는 *randomValue* 라는 임의의 영숫자 값이 추가됩니다. randomValue는 스크립트의 맨 위에 한 번 생성되고 전체 스크립트에서 필요에 따라 리소스에 추가됩니다. 임의로 설정하지 않으려면 빈 문자열이나 특정 값으로 설정할 수 있습니다.

> [!TIP]
> 디버깅 팁: 이 스크립트는 연속 기호(백슬래시 `\`)를 사용하여 스크립트를 더 읽기 쉽게 만듭니다. 스크립트를 실행하는 데 문제가 있으면 Cloud Shell 세션이 `bash`를 실행 중이고 백슬래시 뒤에 공백이 없는지 확인하세요.
>

```azurecli-interactive
# This retrieves the subscription id of the account 
#   in which you're logged in.
# This field is used to set up the routing queries.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults

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

```

기본 리소스가 설정되었으면, [Azure Portal](https://portal.azure.com)에서 메시지 라우팅을 구성할 수 있습니다.

## <a name="set-up-message-routing"></a>메시지 라우팅 설정

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>스토리지 계정으로 라우팅

이제 스토리지 계정에 대한 라우팅을 설정합니다. 메시지 라우팅 창으로 이동한 다음, 경로를 추가합니다. 경로를 추가할 때 경로에 대한 새 엔드포인트를 정의합니다. 라우팅이 설정되면 **수준** 속성이 **스토리지** 로 설정된 메시지가 스토리지 계정에 자동으로 작성됩니다. 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

이제 Azure Storage로 메시지를 라우팅하기 위한 구성을 설정합니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 그룹** 을 선택한 다음, 리소스 그룹을 선택합니다. 이 자습서에서는 **ContosoResources** 를 사용합니다.

2. 리소스 목록에서 IoT 허브를 선택합니다. 이 자습서에서는 **ContosoTestHub** 를 사용합니다.

3. **_Messaging_*이라는 중간 열에서 **메시지 라우팅** 을 선택합니다. +** 추가**를 선택하여 **경로 추가** 창을 표시합니다. 엔드포인트 필드 옆에 있는 +**엔드포인트 추가** 를 선택하고 **스토리지** 를 선택합니다. **스토리지 엔드포인트 추가** 창이 보입니다.

   ![경로에 대한 엔드포인트 추가 시작](./media/tutorial-routing/01-add-a-route-to-storage.png)

4. 엔드포인트에 사용할 이름을 입력합니다. 이 자습서에서는 **ContosoStorageEndpoint** 를 사용합니다.

   ![엔드포인트 명명](./media/tutorial-routing/02-add-a-storage-endpoint.png)

5. **컨테이너 선택** 을 선택합니다. 이렇게 하면 스토리지 계정 목록으로 이동합니다. 준비 단계에서 설정한 항목을 선택합니다. 이 자습서에서는 **contosostorage** 를 사용합니다. 해당 스토리지 계정의 컨테이너 목록을 보여줍니다. 준비 단계에서 설정한 컨테이너를 **선택** 합니다. 이 자습서에서는 **contosoresults** 를 사용합니다. 그런 다음, 화면 하단에서 **선택** 을 클릭합니다. 다른 **스토리지 엔드포인트 추가** 창으로 돌아갑니다. 선택한 컨테이너의 URL이 표시됩니다. 

6. 인코딩을 AVRO 또는 JSON으로 설정합니다. 이 자습서의 목적을 위해, 나머지 필드에는 기본값을 사용합니다. 선택한 지역에 JSON 인코딩이 지원되지 않으면, 이 필드가 회색으로 표시됩니다. 파일 이름 형식을 설정합니다. 

   > [!NOTE]
   > **Blob 파일 이름 형식** 을 사용하여 Blob 이름 형식을 설정합니다. 기본값은 `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`입니다. 형식에 {iothub}, {partition}, {YYYY}, {MM}, {DD}, {HH} 및 {mm}이 포함되어야 하며, 순서는 관계 없습니다.
   >
   > 예를 들어 기본 Blob 파일 이름 형식을 사용하고 허브 이름이 ContosoTestHub, 날짜/시간이 2018년 10월 30일 오전 10시 56분인 경우 Blob 이름은 `ContosoTestHub/0/2018/10/30/10/56`과 비슷합니다.
   > 
   > Blob은 기본적으로 AVRO 형식으로 작성됩니다.
   >

7. 페이지 아래의 **만들기** 를 선택하여 스토리지 엔드포인트를 만들고 경로에 추가합니다. **경로 추가** 창으로 돌아갑니다. 

8. 나머지 라우팅 쿼리 정보를 입력합니다. 이 쿼리는 엔드포인트로 추가한 스토리지 컨테이너에 메시지를 보내기 위한 조건을 지정합니다. 화면에 나온 필드를 채웁니다.

9. 나머지 필드를 채웁니다.

   - **이름**: 경로에 사용할 이름을 입력합니다. 이 자습서에서는 **ContosoStorageRoute** 를 사용합니다. 이제 스토리지의 엔드포인트를 지정합니다. 이 자습서에서는 ContosoStorageEndpoint를 사용합니다.
   
   - **데이터 원본** 지정: 드롭다운 목록에서 **디바이스 원격 분석 메시지** 를 선택합니다.   

   - **경로 사용** 을 선택합니다. 이 필드는 `enabled`로 설정되어 있어야 합니다.

   - **라우팅 쿼리**: 쿼리 문자열로 `level="storage"`을 입력합니다.

   ![라우팅 쿼리 정보 저장](./media/tutorial-routing/04-save-storage-route.png)
  
10.  **저장** 을 선택합니다. 작업이 완료되면 스토리지에 대한 새 라우팅 쿼리를 볼 수 있는 메시지 라우팅 창으로 돌아갑니다. 메시지 라우팅 창을 닫아 리소스 그룹 페이지로 돌아갑니다.


### <a name="route-to-a-service-bus-queue"></a>Service Bus 큐에 라우팅

이제 Service Bus 큐에 대한 라우팅을 설정합니다. 메시지 라우팅 창으로 이동한 다음, 경로를 추가합니다. 경로를 추가할 때 경로에 대한 엔드포인트로 Service Bus 큐를 정의합니다. 경로가 설정되면 **수준** 속성이 **중요** 로 설정된 메시지가 Service Bus 큐에 작성되며, 이를 통해 Logic App이 트리거된 다음, 정보가 포함된 이메일이 전송됩니다.

1. 리소스 그룹 페이지에서 IoT 허브를 선택한 다음, **메시지 라우팅** 을 선택합니다.

2. **메시지 라우팅** 창에서 +**추가** 를 선택합니다.

3. **경로 추가** 창에서 **+엔드포인트** 필드 옆에 있는 +**추가** 를 선택합니다. **Service Bus 큐** 를 선택합니다. **Service Bus 엔드포인트 추가** 창이 표시됩니다.

   ![첫 번째 Service Bus 엔드포인트 추가](./media/tutorial-routing/05-setup-sbq-endpoint.png)

4. 나머지 필드를 채웁니다.

   **엔드포인트 이름**: 엔드포인트에 사용할 이름을 입력합니다. 이 자습서에서는 **ContosoSBQEndpoint** 를 사용합니다.
   
   **Service Bus 네임스페이스**: 드롭다운 목록을 사용하여 준비 단계에서 설정한 Service Bus 네임스페이스를 선택합니다. 이 자습서에서는 **ContosoSBNamespace** 를 사용합니다.

   **Service Bus 큐**: 드롭다운 목록을 사용하여 Service Bus 큐를 선택합니다. 이 자습서에서는 **contososbqueue** 를 사용합니다.

5. **만들기** 를 선택하여 첫 번째 Service Bus 큐 엔드포인트를 추가합니다. **경로 추가** 창으로 돌아갑니다.

   ![두 번째 Service Bus 엔드포인트 추가](./media/tutorial-routing/06-save-sbq-endpoint.png)

6. 이제 나머지 라우팅 쿼리 정보를 입력합니다. 이 쿼리는 엔드포인트로 추가한 Service Bus 큐에 메시지를 보내기 위한 조건을 지정합니다. 화면에 나온 필드를 채웁니다. 

   **이름**: 경로에 사용할 이름을 입력합니다. 이 자습서에서는 **ContosoSBQueueRoute** 를 사용합니다. 

   **엔드포인트**: 여기에는 방금 설정한 엔드포인트가 표시됩니다.

   **데이터 원본**: 드롭다운 목록에서 **디바이스 원격 분석 메시지** 를 선택합니다.

   **경로 사용**:이 필드를 `enable`로 설정합니다.

   **라우팅 쿼리**: 쿼리 문자열로 `level="critical"`을 입력합니다. 

   ![Service Bus 큐에 대한 라우팅 쿼리를 만듭니다.](./media/tutorial-routing/07-save-servicebusqueue-route.png)

7. **저장** 을 선택합니다. 경로 창으로 돌아가면 새 경로를 모두 확인할 수 있습니다.

   ![방금 설정한 경로](./media/tutorial-routing/08-show-both-routes.png)

8. **사용자 지정 엔드포인트** 탭을 선택하여 설정한 사용자 지정 엔드포인트를 볼 수 있습니다.

   ![방금 설정한 사용자 지정 엔드포인트](./media/tutorial-routing/09-show-custom-endpoints.png)

9. 메시지 라우팅 창을 닫아 리소스 그룹 페이지로 돌아갑니다.

## <a name="create-a-simulated-device"></a>시뮬레이션된 디바이스 만들기

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>다음 단계

리소스가 설정되고 메시지 경로가 구성되었으면 다음 자습서로 진행하여, IoT 허브에 메시지를 보낸 후 다른 대상으로 라우팅되는 과정을 확인하는 방법을 살펴보세요. 

> [!div class="nextstepaction"]
> [2부 - 메시지 라우팅 결과 보기](tutorial-routing-view-message-routing-results.md)
