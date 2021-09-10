---
title: Azure Event Grid 대상의 역할에 관리 ID 추가
description: 이 문서에서는 Azure Service Bus 및 Azure Event Hubs와 같은 대상에서 Azure 역할에 관리 ID를 추가하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: c2bfc10f0019b6753e9290d20c84ba5e2bbb59fe
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536684"
---
# <a name="grant-managed-identity-the-access-to-event-grid-destination"></a>관리 ID에 Event Grid 대상에 대한 액세스 권한 부여
이 섹션에서는 시스템 토픽, 사용자 지정 토픽 또는 도메인의 ID를 Azure 역할에 추가하는 방법을 설명합니다. 

## <a name="prerequisites"></a>필수 구성 요소
다음 문서의 지침을 사용하여 시스템이 할당한 관리 ID를 할당합니다.

- [사용자 지정 토픽 또는 도메인](enable-identity-custom-topics-domains.md)
- [시스템 항목](enable-identity-system-topics.md)

## <a name="supported-destinations-and-azure-roles"></a>지원되는 대상 및 Azure 역할
Event Grid 사용자 지정 토픽 또는 도메인에 대해 ID를 사용하도록 설정하면 Azure에서 자동으로 Azure Active Directory에 ID를 만듭니다. 사용자 지정 토픽 또는 도메인에서 지원되는 대상으로 이벤트를 전달할 수 있도록 이 ID를 적절한 Azure 역할에 추가합니다. 예를 들어, Event Grid 사용자 지정 토픽에서 해당 네임스페이스의 Event Hubs로 이벤트를 전달할 수 있도록 Azure Event Hubs 네임스페이스에 대한 **Azure Event Hubs 데이터 보낸 사람** 역할에 ID를 추가합니다. 

현재 Azure Event Grid는 시스템이 할당한 관리 ID로 구성된 사용자 지정 토픽 또는 도메인을 지원하여 이벤트를 다음 대상으로 전달합니다. 또한 이 테이블은 사용자 지정 토픽이 이벤트를 전달하기 위해 ID가 속해야 하는 역할을 보여 줍니다.

| 대상 | Azure 역할 | 
| ----------- | --------- | 
| Service Bus 큐 및 토픽 | [Azure Service Bus 데이터 보낸 사람](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Azure Event Hubs | [Azure Event Hubs 데이터 보내는 사람](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Azure Blob Storage | [Storage Blob 데이터 기여자](../storage/blobs/assign-azure-role-data-access.md) |
| Azure Queue 스토리지 |[Storage 큐 데이터 메시지 보내는 사람](../storage/blobs/assign-azure-role-data-access.md) | 

## <a name="use-the-azure-portal"></a>Azure Portal 사용
사용자 지정 토픽 또는 도메인에서 대상으로 이벤트를 전달할 수 있도록 Azure Portal을 사용하여 사용자 지정 토픽 또는 도메인 ID를 적절한 역할에 할당할 수 있습니다. 

다음 예제에서는 **msitesttopic** 이라는 Event Grid 사용자 지정 토픽의 관리 ID를 큐 또는 토픽 리소스가 포함된 Service Bus 네임스페이스에 대한 **Azure Service Bus 데이터 보낸 사람** 역할에 추가합니다. 네임스페이스 수준에서 역할에 추가할 경우 Event Grid 사용자 지정 토픽은 네임스페이스에 있는 모든 엔터티에 이벤트를 전달할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)에서 **Service Bus 네임스페이스** 로 이동합니다. 
1. 왼쪽 창에서 **액세스 제어** 를 선택합니다. 
1. **역할 할당 추가(미리 보기)** 섹션에서 **추가** 를 선택합니다. 

    :::image type="content" source="./media/add-identity-roles/add-role-assignment-menu.png" alt-text="역할 할당 추가(미리 보기) 메뉴 선택 이미지":::
1. **역할 할당 추가** 페이지에서 **Azure Service Bus 데이터 보낸 사람** 을 선택한 후 **다음** 을 선택합니다.  
    
    :::image type="content" source="./media/add-identity-roles/select-role.png" alt-text="Azure Service Bus 데이터 보낸 사람 역할 선택 이미지":::
1. **멤버** 탭에서 다음 단계를 수행합니다. 
    1. **사용, 그룹 또는 서비스 주체** 를 선택하고 **+ 멤버 선택** 을 클릭합니다. **관리 ID** 옵션은 아직 Event Grid ID를 지원하지 않습니다. 
    1. **멤버 선택** 창에서 사용자 지정 토픽과 동일한 이름의 서비스 주체를 검색하여 선택합니다. 다음 예제에서는 **spcustomtopic0728** 입니다.
    
        :::image type="content" source="./media/add-identity-roles/select-managed-identity-option.png" alt-text="사용자, 그룹 또는 서비스 주체 옵션 선택 이미지":::    
    1. **멤버 선택** 창에서 **선택** 을 클릭합니다. 

        :::image type="content" source="./media/add-identity-roles/managed-identity-selected.png" alt-text="관리 ID 옵션 선택 이미지":::            
1. 이제 **멤버** 탭으로 돌아가서 **다음** 을 선택합니다. 

    :::image type="content" source="./media/add-identity-roles/members-select-next.png" alt-text="멤버 페이지의 다음 단추 선택 이미지":::                
1. **검토 + 할당** 페이지에서 설정을 검토한 후 **검토 + 할당** 을 선택합니다. 

이 단계는 테이블에 설명된 다른 역할에 ID를 추가하는 경우와 비슷합니다. 

## <a name="use-the-azure-cli"></a>Azure CLI 사용
이 섹션의 예제에서는 Azure CLI를 사용하여 Azure 역할에 ID를 추가하는 방법을 보여 줍니다. 샘플 명령은 Event Grid 사용자 지정 토픽에 대한 것입니다. Event Grid 도메인에 대한 명령도 비슷합니다. 

### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>사용자 지정 토픽 시스템 ID의 주체 ID 가져오기 
먼저 사용자 지정 토픽의 시스템 관리 ID에 대한 보안 주체 ID를 가져오고 해당 역할에 ID를 할당합니다.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>다양한 범위에서 Event Hubs에 대한 역할 할당 만들기 
다음 CLI 예제에서는 네임스페이스 수준이나 이벤트 허브 수준에서 **Azure Event Hubs 데이터 보낸 사람** 역할에 사용자 지정 토픽의 ID를 추가하는 방법을 보여 줍니다. 네임스페이스 수준에서 역할 할당을 만드는 경우 사용자 지정 토픽은 해당 네임스페이스의 모든 이벤트 허브에 이벤트를 전달할 수 있습니다. 이벤트 허브 수준에서 역할 할당을 만드는 경우 사용자 지정 토픽은 특정 이벤트 허브에만 이벤트를 전달할 수 있습니다. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>다양한 범위의 Service Bus 토픽에 대한 역할 할당 만들기 
다음 CLI 예제에서는 네임스페이스 수준이나 Service Bus 토픽 수준에서 **Azure Service Bus 데이터 보낸 사람** 역할에 Event Grid 사용자 지정 토픽의 ID를 추가하는 방법을 보여 줍니다. 네임스페이스 수준에서 역할 할당을 만드는 경우 Event Grid 토픽은 해당 네임스페이스 내에서 모든 엔터티(Service Bus 큐 또는 토픽)에 이벤트를 전달할 수 있습니다. Service Bus 큐 또는 토픽 수준에서 역할 할당을 만드는 경우 Event Grid 사용자 지정 토픽은 특정 Service Bus 큐 또는 토픽에만 이벤트를 전달할 수 있습니다. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="next-steps"></a>다음 단계
시스템 토픽, 사용자 지정 토픽 또는 도메인에 시스템이 할당한 관리 ID를 할당하고 대상의 적절한 역할에 ID를 추가했으므로, 이제 ID를 사용하여 대상에 이벤트를 전달하는 방법에 관해서는 [관리 ID를 사용하여 이벤트 전달](managed-service-identity.md)을 참조하세요.