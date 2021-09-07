---
title: 이벤트 전달, 관리형 서비스 ID 및 프라이빗 링크
description: 이 문서에서는 Azure Event Grid 토픽에 대해 관리형 서비스 ID를 사용하도록 설정하는 방법을 설명합니다. 이 ID를 사용하여 지원되는 대상에 이벤트를 전달합니다.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: f1f80f23fe108415daa6e0526b651c7269d6b1b3
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634113"
---
# <a name="event-delivery-with-a-managed-identity"></a>관리 ID를 사용하여 이벤트 전달
이 문서에서는 Azure Event Grid 시스템 토픽, 사용자 지정 토픽 또는 도메인에 대해 [관리형 서비스 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하도록 설정하는 방법을 설명합니다. 이 ID를 사용하여 Service Bus 큐 및 토픽, Event Hubs, 스토리지 계정 등의 지원되는 대상으로 이벤트를 전달합니다.



## <a name="prerequisites"></a>필수 구성 요소
1. 시스템 할당 ID 또는 사용자 할당 ID를 시스템 토픽, 사용자 지정 토픽 또는 도메인에 할당합니다. 
    - 사용자 지정 토픽 및 도메인은 [사용자 지정 토픽 및 도메인에 관리 ID 사용](enable-identity-custom-topics-domains.md)을 참조하세요. 
    - 시스템 토픽은 [시스템 토픽에 관리 ID 사용](enable-identity-system-topics.md)을 참조하세요.
1. 대상(예: Service Bus 큐)의 적절한 역할(예: Service Bus 데이터 보낸 사람)에 ID를 추가합니다. 자세한 단계는 [대상의 Azure 역할에 ID 추가](add-identity-roles.md)를 참조하세요.

    > [!NOTE]
    > 현재 [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 사용하여 이벤트를 전달할 수 없습니다. 자세한 내용은 이 문서의 끝 부분에 있는 [프라이빗 엔드포인트](#private-endpoints) 섹션을 참조하세요. 

## <a name="create-event-subscriptions-that-use-an-identity"></a>ID를 사용하는 이벤트 구독 만들기
Event Grid 사용자 지정 토픽이나 시스템 토픽 또는 시스템 관리 ID를 사용하는 도메인이 있으며 대상의 적절한 역할에 ID를 추가하면, 해당 ID를 사용하는 구독을 만들 준비가 된 것입니다. 

### <a name="use-the-azure-portal"></a>Azure Portal 사용
이벤트 구독을 만들 때 **엔드포인트 세부 정보** 섹션에서 엔드포인트에 대한 시스템 할당 ID 또는 사용자 할당 ID를 사용하도록 설정하는 옵션이 표시됩니다. 

다음은 Service Bus 큐를 대상으로 사용하여 이벤트 구독을 만드는 동안 시스템 할당 ID를 사용하도록 설정하는 예제입니다. 

![Service Bus 큐에 대한 이벤트 구독을 만드는 동안 ID 사용](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

또한 **추가 기능** 탭의 전달하지 못한 편지에 사용할 시스템 할당 ID를 사용하도록 설정할 수 있습니다. 

![배달 못한 편지에 대해 시스템 할당 ID 사용](./media/managed-service-identity/enable-deadletter-identity.png)

관리 ID를 만든 후 이벤트 구독에서 사용하도록 설정할 수도 있습니다. 이벤트 구독에 대한 **이벤트 구독** 페이지에서 **추가 기능** 탭으로 전환하여 옵션을 확인합니다. 

![기존 이벤트 구독에서 시스템 할당 ID를 사용하도록 설정](./media/managed-service-identity/event-subscription-additional-features.png)

토픽에 대해 사용자 할당 ID를 사용하도록 설정한 경우 **관리 ID 유형** 에 대한 드롭다운 목록에서 사용자 할당 ID 옵션이 사용하도록 설정된 것을 볼 수 있습니다. **관리 ID 유형** 에 대해 **사용자 할당** 을 선택하는 경우 이벤트를 전달하는 데 사용할 사용자 할당 ID를 선택할 수 있습니다. 

![이벤트 구독에서 사용자 할당 ID 사용](./media/managed-service-identity/event-subscription-user-identity.png)


### <a name="use-the-azure-cli---service-bus-queue"></a>Azure CLI 사용 - Service Bus 큐 
이 섹션에서는 Azure CLI를 사용하여 시스템 할당 ID를 통해 Service Bus 큐에 이벤트를 전달하도록 설정하는 방법을 알아봅니다. ID는 **Azure Service Bus 데이터 보낸 사람** 역할의 멤버여야 합니다. 또한 배달 못한 편지 처리에 사용되는 스토리지 계정에서 **Storage Blob 데이터 기여자** 역할의 멤버여야 합니다. 

#### <a name="define-variables"></a>변수 정의
먼저 CLI 명령에 사용할 다음 변수의 값을 지정합니다. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>전송에 관리 ID를 사용하여 이벤트 구독 만들기 
이 샘플 명령은 엔드포인트 유형이 **Service Bus 큐** 로 설정된 Event Grid 사용자 지정 토픽에 대한 이벤트 구독을 만듭니다. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>전송 및 전송 못한 편지에 관리 ID를 사용하여 이벤트 구독 만들기
이 샘플 명령은 엔드포인트 유형이 **Service Bus 큐** 로 설정된 Event Grid 사용자 지정 토픽에 대한 이벤트 구독을 만듭니다. 또한 전송하지 못한 편지에 시스템 관리 ID를 사용하도록 지정합니다. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="use-the-azure-cli---event-hubs"></a>Azure CLI 사용 - Event Hubs 
이 섹션에서는 Azure CLI를 사용하여 시스템 할당 ID를 통해 이벤트 허브에 이벤트를 전달하도록 설정하는 방법을 알아봅니다. ID는 **Azure Event Hubs 데이터 보낸 사람** 역할의 멤버여야 합니다. 또한 배달 못한 편지 처리에 사용되는 스토리지 계정에서 **Storage Blob 데이터 기여자** 역할의 멤버여야 합니다. 

#### <a name="define-variables"></a>변수 정의
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>전송에 관리 ID를 사용하여 이벤트 구독 만들기 
이 샘플 명령은 엔드포인트 유형이 **Event Hubs** 로 설정된 Event Grid 사용자 지정 토픽에 대한 이벤트 구독을 만듭니다. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>전송 + 전송하지 못한 편지에 관리 ID를 사용하여 이벤트 구독 만들기 
이 샘플 명령은 엔드포인트 유형이 **Event Hubs** 로 설정된 Event Grid 사용자 지정 토픽에 대한 이벤트 구독을 만듭니다. 또한 전송하지 못한 편지에 시스템 관리 ID를 사용하도록 지정합니다. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="use-the-azure-cli---azure-storage-queue"></a>Azure CLI 사용 - Azure Storage 큐 
이 섹션에서는 Azure CLI를 사용하여 시스템 할당 ID를 통해 Azure Storage 큐에 이벤트를 전달하도록 설정하는 방법을 알아봅니다. 이 ID는 스토리지 계정의 **스토리지 큐 데이터 메시지를 보낸 사람** 역할의 멤버여야 합니다. 또한 배달 못한 편지 처리에 사용되는 스토리지 계정에서 **Storage Blob 데이터 기여자** 역할의 멤버여야 합니다.

#### <a name="define-variables"></a>변수 정의  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>전송에 관리 ID를 사용하여 이벤트 구독 만들기 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>전송 + 전송하지 못한 편지에 관리 ID를 사용하여 이벤트 구독 만들기 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```

## <a name="private-endpoints"></a>프라이빗 엔드포인트
현재 [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 사용하여 이벤트를 전달할 수 없습니다. 즉, 전달된 이벤트 트래픽이 프라이빗 IP 공간을 벗어나지 않아야 하는 엄격한 네트워크 격리 요구 사항이 있는 경우 지원되지 않습니다. 

그러나 요구 사항에 따라 퍼블릭 IP 공간을 사용하여 암호화된 채널과 보낸 사람의 알려진 ID(이 경우 Event Grid)를 사용하여 이벤트를 보내는 안전한 방법이 필요한 경우 Azure Event Grid 사용자 지정 토픽 또는 이 문서에 표시된 대로 구성된 시스템 관리 ID가 있는 도메인을 사용하여 이벤트를 Event Hubs, Service Bus 또는 Azure Storage Service에 전송할 수 있습니다. 그런 다음 Azure Functions에 구성된 프라이빗 링크 또는 가상 네트워크에 배포된 웹후크를 사용하여 이벤트를 풀할 수 있습니다. 샘플: [Azure Functions를 사용하여 프라이빗 엔드포인트에 연결](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/)을 참조하세요.

이 구성에서 트래픽은 퍼블릭 IP/인터넷을 통해 Event Grid에서 Event Hubs, Service Bus 또는 Azure Storage로 이동하지만 채널을 암호화할 수 있으며 Event Grid의 관리 ID가 사용됩니다. 프라이빗 링크를 통해 Event Hubs, Service Bus 또는 Azure Storage를 사용하도록 가상 네트워크에 배포된 Azure Functions 또는 웹후크를 구성하는 경우 트래픽의 해당 섹션은 확실히 Azure 내에 유지됩니다.


## <a name="next-steps"></a>다음 단계
관리 ID에 대해 알아보려면 [Azure 리소스용 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.
