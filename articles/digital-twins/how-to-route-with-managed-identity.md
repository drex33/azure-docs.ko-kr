---
title: 관리 ID를 사용하여 이벤트 라우팅(미리 보기)
titleSuffix: Azure Digital Twins
description: Azure Digital Twins를 위해 시스템 할당 ID를 사용하도록 설정하고 Azure Portal 또는 CLI를 사용하여 이벤트를 전달하는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 6/15/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: subject-rbac-steps, contperf-fy21q4
ms.openlocfilehash: 8990536b4ddc09f5673fe00a70bc4f12f4f4a169
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468796"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview"></a>Azure Digital Twins 이벤트 라우팅을 위한 관리 ID 사용(미리 보기)

이 문서에서는 [Azure Digital Twins 인스턴스에 대한 시스템 할당 ID](concepts-security.md#managed-identity-for-accessing-other-resources-preview)(현재 미리 보기)를 사용하도록 설정하고 지원되는 라우팅 대상으로 이벤트를 전달할 때 ID를 사용하는 방법을 설명합니다. 라우팅에 관리 ID를 설정할 필요는 없지만 인스턴스가 [Event Hub](../event-hubs/event-hubs-about.md), [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) 대상 및 [Azure Storage Container](../storage/blobs/storage-blobs-introduction.md)와 같은 다른 Azure AD 보호 리소스에 쉽게 액세스하는 데 도움이 될 수 있습니다.

이 문서에서 설명하는 단계는 다음과 같습니다. 

1. 시스템 할당 ID로 Azure Digital Twins 인스턴스를 만들거나 기존 Azure Digital Twins 인스턴스에서 시스템 할당 ID를 사용하도록 설정합니다. 
1. ID에 적절한 역할을 추가합니다. 예를 들어 엔드포인트가 Event Hub이면 **Azure Event Hub 데이터 보낸 사람** 역할을 할당하고 엔드포인트가 Service Bus이면 **Azure Service Bus 데이터 보낸 사람 역할** 을 할당합니다.
1. Azure Digital Twins에서 인증에 시스템 할당 ID를 사용할 수 있는 엔드포인트를 만듭니다.

## <a name="enable-system-managed-identity-for-the-instance"></a>인스턴스에 대한 시스템 관리 ID 사용 

Azure Digital Twins 인스턴스에 시스템 할당 ID를 사용하도록 설정하면 Azure가 [Azure AD(Azure Active Directory)](../active-directory/fundamentals/active-directory-whatis.md)에서 이에 대한 ID를 자동으로 만듭니다. 그런 후 이 ID를 사용하여 이벤트 전달을 위해 Azure Digital Twins 엔드포인트에 대해 인증을 수행할 수 있습니다.

**인스턴스를 처음 설정할 때** Azure Digital Twins 인스턴스에 대해 시스템 관리 ID를 사용하도록 설정하거나 **나중에 기존 인스턴스에서 사용하도록 설정할 수 있습니다**.

이러한 생성 방법은 해당 인스턴스에 대해 동일한 구성 옵션 및 동일한 최종 결과를 제공합니다. 이 섹션에서는 두 방법을 모두 설명합니다.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>인스턴스 생성 중 시스템 관리 ID 추가

이 섹션에서는 인스턴스가 만들어지는 동안 Azure Digital Twins 인스턴스에서 시스템 관리 ID를 사용하도록 설정하는 방법을 알아봅니다. [Azure Portal](https://portal.azure.com) 또는 [Azure CLI](/cli/azure/what-is-azure-cli)를 사용하여 인스턴스를 만드는지 여부에 관계없이 ID를 사용하도록 설정할 수 있습니다. 아래 탭을 사용하여 원하는 환경의 지침을 선택할 수 있습니다.

# <a name="portal"></a>[포털](#tab/portal) 

포털에서 인스턴스 만들기 중에 관리 ID를 추가하려면 [일반적인 방법으로 인스턴스 만들기](how-to-set-up-instance-portal.md)를 시작합니다.

시스템 관리 ID 옵션은 인스턴스 설정의 **고급** 탭에 있습니다.

이 탭에서 이 기능을 설정하려면 **시스템 관리 ID** 에 대해 **설정** 옵션을 선택합니다.

:::image type="content" source="media/how-to-enable-managed-identities/create-instance-advanced.png" alt-text="Azure Digital Twins 리소스 만들기 대화 상자의 고급 탭을 보여주는 Azure Portal의 스크린샷. 시스템 관리 ID가 켜져 있습니다.":::

그런 후 아래의 탐색 단추를 사용하여 남은 인스턴스 설정을 계속할 수 있습니다.
   
# <a name="cli"></a>[CLI](#tab/cli)

CLI에서 인스턴스를 만드는 데 사용되는 `az dt create` 명령에 `--assign-identity` 매개 변수를 추가할 수 있습니다. (이 명령에 대한 자세한 내용은 해당 [참조 설명서](/cli/azure/dt#az_dt_create) 또는 [Azure Digital Twins 인스턴스를 설정하기 위한 일반 지침](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)을 참조하세요.)

시스템 관리 ID를 사용하여 인스턴스를 만들려면 다음과 같이 `--assign-identity` 매개 변수를 추가합니다.

```azurecli-interactive
az dt create --dt-name <new-instance-name> --resource-group <resource-group> --assign-identity
```

---

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>기존 인스턴스에 시스템 관리 ID 추가

이 섹션에서는 기존의 Azure Digital Twins 인스턴스에 시스템 관리 ID를 추가합니다. 아래 탭을 사용하여 원하는 환경의 지침을 선택할 수 있습니다.

# <a name="portal"></a>[포털](#tab/portal) 

브라우저에서 [Azure Portal](https://portal.azure.com)을 열어 시작합니다.

1. 포털 검색 창에서 인스턴스 이름을 검색하고 해당 세부 정보를 확인합니다.

1. 왼쪽 메뉴에서 **ID(미리 보기)** 를 선택합니다.

1. 이 페이지에서 이 기능을 설정하려면 **설정** 옵션을 선택합니다.

1. **저장** 단추를 선택하고 **예** 를 선택하여 확인합니다.

    :::image type="content" source="media/how-to-enable-managed-identities/identity-digital-twins.png" alt-text="Azure Digital Twins 인스턴스의 ID(미리 보기) 페이지를 보여주는 Azure Portal의 스크린샷.":::

변경 사항이 저장되면 새 ID의 **개체 D** 및 **권한** 에 대해 추가 필드가 이 페이지에 표시됩니다.

필요하면 여기에서 **개체 ID** 를 복사하고 **권한** 단추를 사용하여 ID에 할당된 Azure 역할을 볼 수 있습니다. 일부 역할을 설정하려면 다음 섹션을 계속합니다.

# <a name="cli"></a>[CLI](#tab/cli)

다시 말해 `az dt create` 명령과 `--assign-identity` 매개 변수를 사용하여 인스턴스에 ID를 추가할 수 있습니다. 만들 인스턴스의 새 이름을 입력하는 대신, 이미 존재하는 인스턴스의 이름을 입력하여 해당 인스턴스에 대해 `--assign-identity`의 값을 업데이트할 수 있습니다.

관리 ID를 **사용하도록 설정** 하는 명령은 시스템 관리 ID를 사용하여 인스턴스를 만드는 명령과 동일합니다. 이러한 모든 변경 내용은 인스턴스 이름 매개 변수의 값입니다.

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --assign-identity
```

현재 사용하도록 설정된 인스턴스에서 관리 ID를 **사용하지 않도록 설정** 하려면 다음과 유사한 명령을 사용하여 `--assign-identity`를 `false`로 설정합니다.

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --assign-identity false
```

---

## <a name="assign-azure-roles-to-the-identity"></a>ID에 Azure 역할 할당 

Azure Digital Twins 인스턴스에 대해 시스템 할당 ID가 생성된 후 지원되는 대상으로의 이벤트 전달을 위해 다른 유형의 [엔드포인트](concepts-route-events.md)에 인증하기 위해 적합한 역할을 할당해야 합니다. 이 섹션에서는 역할 옵션 및 시스템 할당 ID에 이를 할당하는 방법을 설명합니다.

>[!NOTE]
> 이 단계가 없으면 ID가 엔드포인트에 액세스할 수 없고 이벤트가 전달되지 않으므로 중요합니다.

### <a name="supported-destinations-and-azure-roles"></a>지원되는 대상 및 Azure 역할 

대상 유형에 따라 ID가 엔드포인트에 액세스해야 하는 최소 역할은 다음과 같습니다. 높은 권한의 역할(예: 데이터 소유자 역할)도 작동합니다.

| 대상 | Azure 역할 |
| --- | --- |
| Azure Event Hubs | Azure Event Hubs 데이터 보내는 사람 |
| Azure Service Bus | Azure Service Bus 데이터 보내는 사람 |
| Azure 스토리지 컨테이너 | Storage Blob 데이터 기여자 |

Azure Digital Twins에서 라우팅을 위해 지원되는 엔드포인트, 경로 및 대상 유형에 대한 자세한 내용은 [이벤트 라우팅](concepts-route-events.md)을 참조하세요.

### <a name="assign-the-role"></a>역할 할당

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

아래 탭을 사용하여 원하는 환경의 지침을 선택할 수 있습니다.

# <a name="portal"></a>[포털](#tab/portal) 

ID에 역할을 할당하려면 브라우저에서 [Azure Portal](https://portal.azure.com)을 열어 시작합니다.

1. 포털 검색 창에서 해당 이름을 검색하여 엔드포인트 리소스(Event Hub, Service Bus 토픽 또는 스토리지 컨테이너)로 이동합니다. 

1. **액세스 제어(IAM)** 를 선택합니다.

1. **추가** > **역할 할당 추가** 를 선택하여 역할 할당 추가 페이지를 엽니다.

1. 아래 정보를 사용하여 Azure Digital Twins 인스턴스의 관리 ID에 원하는 역할을 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.
    
    | 설정 | 값 |
    | --- | --- |
    | 역할 | 드롭다운 메뉴에서 원하는 역할을 선택합니다. |
    | 다음에 대한 액세스 할당 | **시스템 할당 관리 ID** 에서 **Digital Twins** 를 선택합니다. |
    | 멤버 | 역할을 할당하는 Azure Digital Twins 인스턴스의 관리 ID를 선택합니다. 관리 ID 이름이 인스턴스 이름과 일치하므로 Azure Digital Twins 인스턴스 이름을 선택합니다. |
    
    ![역할 할당 추가 페이지](../../includes/role-based-access-control/media/add-role-assignment-page.png)

# <a name="cli"></a>[CLI](#tab/cli)

지정된 역할을 사용하여 하나 이상의 범위에 ID를 할당하기 위해 `az dt create` 명령에 `--scopes` 매개 변수를 추가할 수 있습니다. 이 매개변수가 있는 명령은 인스턴스를 처음 만들 때 또는 나중에 이미 존재하는 인스턴스의 이름을 전달하여 사용할 수 있습니다.

다음은 시스템 관리 ID를 사용하여 인스턴스를 만들고 해당 ID에 이벤트 허브에서 `MyCustomRole`이라는 사용자 지정 역할을 할당하는 예입니다.

```azurecli-interactive
az dt create --dt-name <instance-name> --resource-group <resource-group> --assign-identity --scopes "/subscriptions/<subscription ID>/resourceGroups/<resource-group>/providers/Microsoft.EventHub/namespaces/<Event-Hubs-namespace>/eventhubs/<event-hub-name>" --role MyCustomRole
```

이 명령을 사용한 역할 할당에 대한 추가 예제는 [az dt create 참조 설명서](/cli/azure/dt#az_dt_create)를 참조하세요.

[az role assignment](/cli/azure/role/assignment?view=azure-cli-latest&preserve-view=true) 명령 그룹을 사용하여 역할을 만들고 관리할 수도 있습니다. 이 명령은 만들기 명령을 사용하여 역할 할당을 그룹화하지 않으려는 다른 시나리오를 지원하는 데 사용할 수 있습니다.

---

## <a name="create-an-endpoint-with-identity-based-authentication"></a>ID 기반 인증으로 엔드포인트 만들기

Azure Digital Twins 인스턴스에 대해 시스템 관리 ID를 설정하고 여기에 적합한 역할을 할당한 후에는 이 ID를 인증에 사용할 수 있는 Azure Digital Twins [엔드포인트](how-to-manage-routes.md#create-an-endpoint-for-azure-digital-twins)를 만들 수 있습니다. 이 옵션은 Event Hub 및 Service Bus 유형 엔드포인트에만 사용할 수 있습니다(Event Grid에 지원되지 않음).

>[!NOTE]
> ID 기반 인증으로 변경하기 위해 키 기반 ID로 이미 생성된 엔드포인트를 편집할 수 없습니다. 엔드포인트를 처음 만들 때 인증 유형을 선택해야 합니다.

아래 탭을 사용하여 원하는 환경의 지침을 선택할 수 있습니다.

# <a name="portal"></a>[포털](#tab/portal) 

[Azure Digital Twins 엔드포인트 만들기 지침](how-to-manage-routes.md#create-an-endpoint-for-azure-digital-twins)에 따라 시작합니다.

엔드포인트 유형에 필요한 세부 정보를 작성할 때 인증 유형으로 **ID 기반** 을 선택해야 합니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png" alt-text="Event Hub 유형의 엔드포인트 만들기 스크린샷" lightbox="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

엔드포인트 설정을 완료하고 **저장** 을 선택합니다.

# <a name="cli"></a>[CLI](#tab/cli)

CLI로 엔드포인트를 만들려면 엔드포인트를 만드는 데 사용되는 `az dt endpoint create` 명령에 `--auth-type` 매개 변수를 추가하면 됩니다. (이 명령에 대한 자세한 내용은 해당 [참조 설명서](/cli/azure/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) 또는 [Azure Digital Twins 엔드포인트를 설정하기 위한 일반 지침](how-to-manage-routes.md#create-the-endpoint)을 참조하세요.)

ID 기반 인증을 사용하는 엔드포인트를 만들려면 `--auth-type` 매개 변수를 사용하여 `IdentityBased` 인증 유형을 지정합니다. 아래 예에서는 Event Hubs 엔드포인트에 대한 이 기능을 보여 줍니다.

```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <endpoint-name> --eventhub-resource-group <eventhub-resource-group> --eventhub-namespace <eventhub-namespace> --eventhub <eventhub-name> --auth-type IdentityBased --dt-name <instance-name>
```

---

## <a name="considerations-for-disabling-system-managed-identities"></a>시스템 관리 ID를 사용하지 않도록 설정할 때의 고려 사항

ID는 이를 사용하는 엔드포인트와 별개로 관리되기 때문에 해당 ID 또는 역할에 대한 변경 사항이 Azure Digital Twins 인스턴스의 엔드포인트에 미칠 수 있는 영향을 고려하는 것이 중요합니다. ID를 사용하지 않도록 설정하거나 엔드포인트에 필요한 역할이 제거된 경우 엔드포인트에 액세스할 수 없게 되고 이벤트 흐름이 중단됩니다.

이제 사용하지 않도록 설정된 관리 ID로 설정된 엔드포인트를 계속 사용하려면 엔드포인트를 삭제하고 다른 인증 유형을 사용해서 [다시 만들어야](how-to-manage-routes.md#create-an-endpoint-for-azure-digital-twins) 합니다. 이렇게 변경한 후 엔드포인트에 대해 이벤트 전달이 재개되려면 최대 1시간까지 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure AD의 관리 ID에 대해 자세히 알아보세요. 
* [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)