---
title: Azure Event Grid 사용자 지정 항목 및 도메인에서 관리 ID 사용
description: 이 문서에서는 Azure Event Grid 사용자 지정 항목 또는 도메인에 대해 관리형 서비스 ID를 사용하도록 설정하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 1f9cae4c25c37d0fcb670804ae3450871dbd5259
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156596"
---
# <a name="assign-a-managed-identity-to-an-event-grid-custom-topic-or-domain"></a>Event Grid 사용자 지정 항목 또는 도메인에 관리 ID 할당 
이 문서에서는 Azure Portal 및 CLI를 사용 하 여 시스템 할당 또는 사용자 할당 [관리 id](../active-directory/managed-identities-azure-resources/overview.md) 를 Event Grid 사용자 지정 토픽 또는 도메인에 할당 하는 방법을 보여 줍니다. 

## <a name="enable-identity-when-creating-a-topic-or-domain"></a>토픽 또는 도메인을 만들 때 ID 사용

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)
**Azure Portal** 에서 토픽 또는 도메인을 만들 때 시스템이 할당 한 id 또는 두 개의 사용자 할당 id 중 하나를 할당할 수 있지만 두 유형의 id는 할당할 수 없습니다. 토픽 또는 도메인을 만든 후에는 [기존 토픽 또는 도메인에 대해 Id 사용](#enable-identity-for-an-existing-custom-topic-or-domain) 섹션의 단계에 따라 두 유형의 id를 모두 할당할 수 있습니다.

### <a name="enable-system-assigned-identity"></a>시스템이 할당한 ID 사용
토픽 또는 도메인 생성 마법사의 **고급** 탭에서 **시스템 할당 ID 사용** 을 선택합니다. 

:::image type="content" source="./media/managed-service-identity/create-topic-identity.png" alt-text="시스템 할당 ID 사용 옵션이 선택된 것을 보여주는 이미지":::

### <a name="enable-user-assigned-identity"></a>사용자가 할당한 ID 사용
1. 토픽 또는 도메인 생성 마법사의 **고급** 페이지에서 **사용자 할당 ID 사용** 을 선택한 다음, **사용자 할당 ID 추가** 를 선택합니다. 

    :::image type="content" source="./media/managed-service-identity/create-page-add-user-assigned-identity-link.png" alt-text="사용자 할당 ID 사용 옵션이 선택된 것을 보여주는 이미지":::
1. **사용자 할당 ID 선택** 창에서 사용자 할당 ID가 있는 구독을 선택하고 **사용자 할당 ID** 를 선택한 다음, **선택** 을 클릭합니다. 

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)
Azure CLI를 사용 하 여 시스템에 할당 된 id를 가진 사용자 지정 토픽 또는 도메인을 만들 수도 있습니다. 현재 Azure CLI는 사용자 할당 id를 토픽 또는 도메인에 할당 하는 것을 지원 하지 않습니다.  

`--identity` 매개 변수를 `systemassigned`로 설정하여 `az eventgrid topic create` 명령을 사용합니다. 이 매개 변수에 대한 값을 지정하지 않으면 기본값 `noidentity`가 사용됩니다. 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

마찬가지로, `az eventgrid domain create` 명령을 사용하여 시스템 할당 ID로 도메인을 만들 수 있습니다.

---

## <a name="enable-identity-for-an-existing-custom-topic-or-domain"></a>기존 사용자 지정 항목 또는 도메인에 대한 ID 사용
이 섹션에서는 기존 사용자 지정 토픽 또는 도메인에 대해 시스템 할당 ID 또는 사용자 할당 ID를 사용하는 방법을 알아봅니다. 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)
Azure Portal를 사용 하는 경우 하나의 시스템 할당 id와 새 사용자 할당 id를 기존 토픽 또는 도메인에 할당할 수 있습니다.

다음 절차에서는 사용자 지정 항목에 대 한 id를 사용 하도록 설정 하는 방법을 보여 줍니다. 도메인에 대한 ID를 설정하는 단계는 유사합니다. 

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 위쪽의 검색 창에서 **이벤트 그리드 토픽** 을 검색합니다.
3. 관리 ID를 사용하도록 설정할 **사용자 지정 항목** 을 선택합니다. 
4. 왼쪽 메뉴에서 **ID** 를 선택합니다.

### <a name="to-assign-a-system-assigned-identity-to-a-topic"></a>토픽에 시스템 할당 ID를 할당하려면
1. **시스템 할당** 탭에서 스위치를 **켜서** ID를 사용하도록 설정합니다. 
1. 도구 모음에서 **저장** 을 선택하여 설정을 저장합니다. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="사용자 지정 항목에 대한 ID 페이지"::: 

### <a name="to-assign-a-user-assigned-identity-to-a-topic"></a>토픽에 사용자 할당 ID를 할당하려면
1. [사용자 할당 관리 ID 관리](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md) 문서의 지침에 따라 사용자 할당 ID를 만듭니다. 
1. **ID** 페이지의 오른쪽 창에서 **사용자 할당** 탭으로 전환한 다음, 도구 모음에서 **+ 추가** 를 선택합니다.

    :::image type="content" source="./media/managed-service-identity/user-assigned-identity-add-button.png" alt-text="사용자 할당 ID 탭을 보여주는 이미지":::     
1. **사용자 관리 ID 추가** 창에서 다음 단계를 수행합니다.
    1. 사용자 할당 ID가 있는 **Azure 구독** 을 선택합니다. 
    1. **사용자 할당 ID** 를 선택합니다. 
    1. **추가** 를 선택합니다. 
1. **사용자 할당** 탭에서 목록을 새로 고쳐 추가된 사용자 할당 ID를 확인합니다.

비슷한 단계를 사용하여 Event Grid 도메인에 대한 ID를 사용하도록 설정할 수 있습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)
또한 Azure CLI를 사용 하 여 시스템 할당 id를 기존 사용자 지정 토픽 또는 도메인에 할당할 수 있습니다. 현재 Azure CLI는 사용자 할당 id를 토픽 또는 도메인에 할당 하는 것을 지원 하지 않습니다.

`--identity`를 `systemassigned`로 설정하여 `az eventgrid topic update` 명령을 사용하면 기존 사용자 지정 항목에 대해 시스템 할당 ID를 사용하도록 설정할 수 있습니다. ID를 사용하지 않도록 설정하려면 `noidentity`를 값으로 지정합니다. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

기존 도메인을 업데이트하는 명령은 유사합니다(`az eventgrid domain update`).

---

## <a name="next-steps"></a>다음 단계
대상(예: Service Bus 큐)의 적절한 역할(예: Service Bus 데이터 보낸 사람)에 ID를 추가합니다. 자세한 단계는 [관리 ID에 Event Grid 대상에 대한 액세스 권한 부여](add-identity-roles.md)를 참조하세요. 
