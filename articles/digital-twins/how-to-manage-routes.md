---
title: 엔드포인트 및 경로 관리
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 데이터에 대한 엔드포인트 및 이벤트 경로를 설정하고 관리하는 방법 참조
author: baanders
ms.author: baanders
ms.date: 7/30/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: fb27a56dcf1661a95b6c7926a3c11628f6341446
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133902"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins"></a>Azure Digital Twins에서의 엔드포인트 및 경로 관리

Azure Digital Twins에서는 [이벤트 알림](concepts-event-notifications.md)을 다운스트림 서비스 또는 연결된 컴퓨팅 리소스로 라우팅할 수 있습니다. 먼저 이벤트를 수신할 수 있는 ‘엔드포인트’를 설정하여 이 작업을 수행합니다. 그런 다음, Azure Digital Twins에서 생성되는 어떤 이벤트를 어느 엔드포인트로 전달할지 지정하는 ‘이벤트 경로’를 만들 수 있습니다.

이 문서에서는 [Azure Portal](https://portal.azure.com), [REST API](/rest/api/azure-digitaltwins/), [.NET(C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 및 [Azure Digital Twins CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)를 사용하여 엔드포인트와 경로를 만드는 과정을 안내합니다.

## <a name="prerequisites"></a>필수 조건

* [무료로 설정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)할 수 있는 **Azure 계정** 이 필요합니다.
* Azure 구독에는 **Azure Digital Twins 인스턴스** 가 필요합니다. 인스턴스가 아직 없는 경우 [인스턴스 및 인증 설정](how-to-set-up-instance-portal.md)의 단계를 사용하여 만들 수 있습니다. 이 문서의 뒷부분에서 쉽게 사용할 수 있도록 설정에서 다음 값을 가져옵니다.
    - 인스턴스 이름
    - Resource group

인스턴스를 설정한 후 [Azure Portal](https://portal.azure.com)에서 이 세부 정보를 확인할 수 있습니다. 포털에 로그인하고 포털 검색 창에서 해당 인스턴스의 이름을 검색합니다.
 
:::image type="content" source="media/how-to-manage-routes/search-field-portal.png" alt-text="Azure Portal 검색 창의 스크린샷." lightbox="media/how-to-manage-routes/search-field-portal.png":::

인스턴스에 대한 개요에서 이 세부 정보를 보려면 결과에서 해당 인스턴스를 선택합니다.

:::image type="content" source="media/how-to-manage-routes/instance-details.png" alt-text="Azure Portal의 Azure Digital Twins 인스턴스에 대한 개요 페이지의 스크린샷. 이름 및 리소스 그룹이 강조 표시됩니다.":::

이 가이드를 수행하는 동안 Azure CLI를 사용하려는 경우 아래 지침을 따르세요.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Azure Digital Twins 엔드포인트 만들기

다음은 인스턴스에 대해 만들 수 있는 지원되는 엔드포인트 유형입니다.
* [Event Grid](../event-grid/overview.md) 항목
* [Event Hubs](../event-hubs/event-hubs-about.md) 허브
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) 항목

>[!NOTE]
> Event Grid 끝점의 경우 **Event Grid 항목만** 지원 됩니다. Event grid **도메인** 은 끝점으로 지원 되지 않습니다.

다양한 엔드포인트 유형에 대한 자세한 정보는 [Azure 메시징 서비스 간 선택](../event-grid/compare-messaging-services.md)을 참조하세요.

이 섹션에서는 [Azure Portal](https://portal.azure.com) 또는 [Azure CLI](/cli/azure/dt/endpoint?view=azure-cli-latest&preserve-view=true)를 사용하여 엔드포인트를 만드는 방법을 설명합니다. [DigitalTwinsEndpoint 컨트롤 플레인 API](/rest/api/digital-twins/controlplane/endpoints)를 사용하여 엔드포인트를 관리할 수도 있습니다.

### <a name="prerequisite-create-endpoint-resources"></a>필수 구성 요소: 엔드포인트 리소스 만들기

엔드포인트를 Azure Digital Twins에 연결하려면 엔드포인트에 사용 중인 이벤트 그리드 토픽, 이벤트 허브 또는 Service Bus 토픽이 이미 있어야 합니다.

다음 차트를 사용하여 엔드포인트를 만들기 전에 설정해야 하는 리소스를 확인합니다.

| 엔드포인트 유형 | 필수 리소스(생성 지침에 연결됨) |
| --- | --- |
| Event Grid 엔드포인트 | [이벤트 그리드 토픽](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)<br/>* 이벤트 스키마는 스키마 또는 클라우드 이벤트 스키마 v1.0 Event Grid 이어야 합니다. |
| Event Hubs 엔드포인트 | [Event&nbsp;Hubs&nbsp;네임스페이스](../event-hubs/event-hubs-create.md)<br/><br/>[이벤트 허브](../event-hubs/event-hubs-create.md)<br/><br/>(선택 사항) 키 기반 인증에 대한 [권한 부여 규칙](../event-hubs/authorize-access-shared-access-signature.md) | 
| 서비스 버스 엔드포인트 | [Service Bus 네임스페이스](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/>[Service Bus 토픽](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/> (선택 사항) 키 기반 인증에 대한 [권한 부여 규칙](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature)|

### <a name="create-the-endpoint"></a>엔드포인트 만들기 

엔드포인트 리소스를 만든 후에는 Azure Digital Twins 엔드포인트에 사용할 수 있습니다. 

# <a name="portal"></a>[포털](#tab/portal) 

새 엔드포인트를 만들려면 [Azure Portal](https://portal.azure.com)의 인스턴스 페이지로 이동합니다. 포털 검색 창에 해당 이름을 입력하여 인스턴스를 찾을 수 있습니다.

1. 인스턴스 메뉴에서 _엔드포인트_ 를 선택합니다. 그런 다음, 뒤에 오는 *엔드포인트* 페이지에서 *+ 엔드포인트 만들기* 를 선택합니다. 그러면 다음 단계에서 필드를 입력하는 *엔드포인트 만들기* 페이지가 열립니다.

    :::image type="content" source="media/how-to-manage-routes/create-endpoint-event-grid.png" alt-text="Azure Portal에서 Event Grid 유형의 엔드포인트 만들기 스크린샷." lightbox="media/how-to-manage-routes/create-endpoint-event-grid.png":::

1. 엔드포인트의 **이름** 을 입력하고 **엔드포인트 유형** 을 선택합니다.

1. [위에서](#prerequisite-create-endpoint-resources) 설명하는 구독과 엔드포인트 리소스를 비롯하여, 엔드포인트 유형에 필요한 다른 세부 정보를 완료합니다.
    1. Event Hub 및 Service Bus 엔드포인트의 경우 **인증 유형** 을 선택해야 합니다. Azure Digital Twins 인스턴스에 대해 [관리 ID](concepts-security.md#managed-identity-for-accessing-other-resources)로 엔드포인트를 사용하려는 경우, 미리 만든 권한 부여 규칙이나 ID 기반 인증을 통해 키 기반 인증을 사용할 수 있습니다. 

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png" alt-text="Azure Portal에서 Event Hub 유형의 엔드포인트 만들기 스크린샷." lightbox="media/how-to-manage-routes/create-endpoint-event-hub-authentication.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. _저장_ 을 선택하여 엔드포인트 만들기를 완료합니다.

>[!IMPORTANT]
> 엔드포인트에 대해 ID 기반 인증을 사용하기 위해서는 [관리 ID를 사용하여 이벤트 라우팅](how-to-route-with-managed-identity.md)의 단계에 따라 인스턴스에 대한 관리 ID를 만들어야 합니다.

엔드포인트를 만든 후 Azure Portal의 위쪽 표시줄에서 알림 아이콘을 확인하여 엔드포인트가 만들어졌는지 확인할 수 있습니다. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-endpoint-notifications.png" alt-text="Azure Portal에서 엔드포인트 생성을 확인하는 알림의 스크린샷.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

엔드포인트 만들기가 실패하면 오류 메시지가 표시되며 몇 분 후 다시 시도합니다.

Azure Digital Twins 인스턴스의 *엔드포인트* 페이지로 돌아가서 만든 엔드포인트를 확인할 수도 있습니다.

이제 이벤트 그리드, 이벤트 허브 또는 Service Bus 토픽을 Azure Digital Twins 안에서 엔드포인트에 대해 선택한 이름의 엔드포인트로 사용할 수 있습니다. 일반적으로 [이 문서의 뒷부분에서](#create-an-event-route) 만들게 되는 **이벤트 경로** 의 대상으로 이 이름을 사용합니다.

# <a name="cli"></a>[CLI](#tab/cli) 

다음 예제에서는 [Azure Digital Twins CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)에 대해 [az dt endpoint create](/cli/azure/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) 명령을 사용하여 엔드포인트를 만드는 방법을 보여 줍니다. 명령의 자리 표시자를 사용자 고유의 리소스 세부 정보로 바꿉니다.

Event Grid 엔드포인트를 생성하려면 다음을 수행합니다.

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> --dt-name <your-Azure-Digital-Twins-instance-name>
```

Event Hubs 엔드포인트(키 기반 인증)를 생성하려면 다음을 수행합니다.
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> --dt-name <your-Azure-Digital-Twins-instance-name>
```

Service Bus 토픽 엔드포인트(키 기반 인증)를 생성하려면 다음을 수행합니다.
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> --dt-name <your-Azure-Digital-Twins-instance-name>
```

이러한 명령을 성공적으로 실행한 후에는 이벤트 그리드, 이벤트 허브 또는 Service Bus 토픽을 `--endpoint-name` 인수로 제공한 이름으로 Azure Digital Twins 내부의 엔드포인트로 사용할 수 있습니다. 일반적으로 이 이름은 **이벤트 경로** 의 대상으로 사용하며 [이 문서의 뒷부분](#create-an-event-route)에서 만들 것입니다.

#### <a name="create-an-endpoint-with-identity-based-authentication"></a>ID 기반 인증으로 엔드포인트 만들기

ID 기반 인증을 사용하는 엔드포인트를 만들어 [관리 ID](concepts-security.md#managed-identity-for-accessing-other-resources)가 있는 엔드포인트를 사용할 수도 있습니다. 이 옵션은 Event Hub 및 Service Bus 유형 엔드포인트에만 사용할 수 있습니다(Event Grid에 지원되지 않음).

이 유형의 엔드포인트를 만드는 CLI 명령은 다음과 같습니다. 명령의 자리 표시자에 연결하려면 다음 값이 필요합니다.
* Azure Digital Twins 인스턴스의 Azure 리소스 ID
* 엔드포인트 이름
* 엔드포인트 유형
* 엔드포인트 리소스의 네임스페이스
* 이벤트 허브 또는 Service Bus 토픽의 이름
* Azure Digital Twins 인스턴스의 위치

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

---

### <a name="create-an-endpoint-with-dead-lettering"></a>배달 못한 편지를 사용하여 엔드포인트 만들기

엔드포인트가 일정 시간 안에 또는 일정 횟수의 이벤트 배달 시도 후 이벤트를 배달할 수 없는 경우, 미배달 이벤트를 스토리지 계정에 보낼 수 있습니다. 이 프로세스를 **배달 못한 편지** 라고 합니다.

[Azure Portal](https://ms.portal.azure.com/#home) 또는 [Azure Digital Twins CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)를 사용하여 필요한 스토리지 리소스를 설정할 수 있습니다. 그렇지만 배달 못한 편지를 사용하도록 설정하여 엔드포인트를 만들려면 [Azure Digital Twins CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) 또는 [컨트롤 플레인 API](concepts-apis-sdks.md#overview-control-plane-apis)를 사용해야 합니다.

배달 못한 편지에 대한 자세한 내용은 [이벤트 경로](concepts-route-events.md#dead-letter-events)를 참조하세요. 배달 못한 편지를 사용하여 엔드포인트를 설정하는 방법에 대한 지침은 이 섹션의 나머지 부분에서 이어집니다.

#### <a name="set-up-storage-resources"></a>스토리지 리소스 설정

배달 못한 편지 위치를 설정하기 전에 Azure 계정에 설정된 [컨테이너](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)가 있는 [스토리지 계정](../storage/common/storage-account-create.md?tabs=azure-portal)이 있어야 합니다. 

나중에 엔드포인트를 만들 때 이 컨테이너에 대한 URI를 제공합니다. 배달 못한 편지 위치는 [SAS 토큰](../storage/common/storage-sas-overview.md)이 있는 컨테이너 URI로 엔드포인트에 제공됩니다. 이 토큰에는 스토리지 계정 내의 대상 컨테이너에 대한 `write` 권한이 필요합니다. 완전히 구성된 **배달 못한 편지 SAS URI** 의 형식은 다음과 같습니다. `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>`

아래 단계에 따라 Azure 계정에서 이러한 스토리지 리소스를 설정하고 다음 섹션에서 엔드포인트 연결 설정을 준비합니다.

1. [스토리지 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-portal)의 단계에 따라 Azure 구독에서 **스토리지 계정** 을 만듭니다. 나중에 사용할 수 있도록 스토리지 계정 이름을 적어 둡니다.
1. [컨테이너 만들기](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)의 단계에 따라 새 스토리지 계정 내에 **컨테이너** 를 만듭니다. 나중에 사용할 수 있도록 컨테이너 이름을 적어 둡니다.

##### <a name="create-a-sas-token"></a>SAS 토큰 만들기

그런 다음, 엔드포인트가 액세스하는 데 사용할 수 있는 스토리지 계정에 대한 **SAS 토큰** 을 만듭니다.

# <a name="portal"></a>[포털](#tab/portal)

1. [Azure Portal](https://ms.portal.azure.com/#home)에서 스토리지 계정으로 이동하여 시작합니다(포털 검색 창에서 이름으로 찾을 수 있음).
1. 스토리지 계정 페이지에서 왼쪽 탐색 모음의 _공유 액세스 서명_ 링크를 선택하여 SAS 토큰 설정을 시작합니다.

    :::image type="content" source="./media/how-to-manage-routes/generate-sas-token-1.png" alt-text="Azure Portal의 스토리지 계정 페이지 스크린샷." lightbox="./media/how-to-manage-routes/generate-sas-token-1.png":::

1. *공유 액세스 서명 페이지* 의 *허용되는 서비스* 및 *허용되는 리소스 종류* 에서 원하는 설정을 선택합니다. 각 범주에서 하나 이상의 상자를 선택해야 합니다. *허용되는 권한* 에서 **쓰기** 를 선택합니다(원하는 경우 다른 권한도 선택할 수도 있음).
1. 나머지 설정에 대해 원하는 값을 설정합니다.
1. 완료되면 _SAS 및 연결 문자열 생성_ 단추를 선택하여 SAS 토큰을 생성합니다. 

    :::image type="content" source="./media/how-to-manage-routes/generate-sas-token-2.png" alt-text="SAS 토큰을 생성하기 위한 모든 설정 선택을 보여주는 Azure Portal의 스토리지 계정 페이지 스크린샷." lightbox="./media/how-to-manage-routes/generate-sas-token-2.png"::: 

1. 이렇게 하면 동일한 페이지 하단의 설정 선택 아래에 여러 SAS 및 연결 문자열 값이 생성됩니다. 아래로 스크롤하여 해당 값을 확인하고 *클립보드에 복사* 아이콘을 사용하여 **SAS 토큰** 값을 복사합니다. 나중에 사용할 수 있도록 저장합니다.

    :::image type="content" source="./media/how-to-manage-routes/copy-sas-token.png" alt-text="배달 못한 편지 비밀에 사용할 SAS 토큰을 복사하는 방법을 강조 표시하는 Azure Portal의 스토리지 계정 페이지 스크린샷." lightbox="./media/how-to-manage-routes/copy-sas-token.png":::

# <a name="cli"></a>[CLI](#tab/cli)

1. 다음 명령을 사용하여 스토리지 계정 키를 검색하고 키 중 하나에 대한 값을 복사합니다.

    ```azurecli
    az storage account keys list --account-name <storage-account-name>
    ```

1. 다음 명령을 사용하여 만료 날짜를 선택하고 스토리지 계정에 대한 SAS 토큰을 생성합니다.

    ```azurecli
    az storage account generate-sas --account-name <storage-account-name> --account-key <storage-account-key> --expiry <expiration-date> --services bfqt --resource-types o --permissions w
    ```

    이 명령의 출력은 SAS 토큰입니다. 나중에 사용할 SAS 토큰 값을 복사합니다.

    > [!NOTE]
    > 이 명령은 "**b** lob", "**f** ile", "**q** ueue" 및 "**t** able" *services* 와 "**개** 체" *리소스 종류* 를 포함하고 "**쓰** 기" *권한* 을 허용합니다.
    >
    > `az storage account generate-sas` 명령과 해당 매개 변수에 대한 자세한 내용은 [Azure CLI 참조서](/cli/azure/storage/account?view=azure-cli-latest&preserve-view=true#az_storage_account_generate_sas)를 참조하세요.

---

#### <a name="create-the-dead-letter-endpoint"></a>배달 못한 편지 엔드포인트 만들기

# <a name="portal"></a>[포털](#tab/portal)

배달 못한 편지 처리를 사용하는 엔드포인트를 만들려면 Azure Portal이 아니라, [CLI 명령](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) 또는 [컨트롤 플레인 API](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate)를 사용하여 엔드포인트를 만들어야 합니다.

Azure CLI를 사용하여 이 작업을 수행하는 방법에 대한 지침을 보려면 이 섹션의 CLI 탭으로 전환합니다.

# <a name="cli"></a>[CLI](#tab/cli)

배달 못한 편지를 사용하는 엔드포인트를 만들려면 [Azure Digital Twins CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)에 대한 [az dt endpoint create](/cli/azure/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) 명령에 다음 배달 못한 편지 매개 변수를 추가합니다.

이 매개 변수의 값은 [이전 섹션](#set-up-storage-resources)에서 수집한 스토리지 계정 이름, 컨테이너 이름 및 SAS 토큰으로 구성된 **배달 못한 편지 SAS URI** 입니다. 이 매개 변수는 키 기반 인증을 사용하여 엔드포인트를 만듭니다.

```azurecli
--deadletter-sas-uri https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>
```

이 매개 변수를 이전 [엔드포인트 만들기](#create-the-endpoint) 섹션의 엔드포인트 만들기 명령의 끝에 추가하여 배달 못한 편지를 사용하는 원하는 유형의 엔드포인트를 만듭니다.

또는 CLI 대신 [Azure Digital Twins 컨트롤 플레인 API](concepts-apis-sdks.md#overview-control-plane-apis)를 사용하여 배달 못한 편지 엔드포인트를 만들 수 있습니다. 이렇게 하려면 [DigitalTwinsEndpoint 설명서](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate)를 참조하여 요청을 구조화하고 배달 못한 편지 매개 변수를 추가하는 방법을 확인합니다.

#### <a name="create-a-dead-letter-endpoint-with-identity-based-authentication"></a>ID 기반 인증을 사용하여 배달 못한 편지 엔드포인트 만들기

또한 ID 기반 인증을 사용하는 배달 못한 편지 엔드포인트를 만들어 [관리 ID](concepts-security.md#managed-identity-for-accessing-other-resources)와 함께 엔드포인트를 사용할 수도 있습니다. 이 옵션은 Event Hub 및 Service Bus 유형 엔드포인트에만 사용할 수 있습니다(Event Grid에 지원되지 않음).

이 엔드포인트 유형을 만들려면 이전과 동일한 CLI 명령을 사용하여 [ID 기반 인증을 사용하여 엔드포인트를 만들고](#create-an-endpoint-with-identity-based-authentication) `deadLetterUri`에 대한 JSON 페이로드의 추가 필드를 사용합니다.

다음은 명령의 자리 표시자에 연결하는 데 필요한 값입니다.
* Azure Digital Twins 인스턴스의 Azure 리소스 ID
* 엔드포인트 이름
* 엔드포인트 유형
* 엔드포인트 리소스의 네임스페이스
* 이벤트 허브 또는 Service Bus 토픽의 이름
* **배달 못한 편지 SAS URI** 세부 정보: 스토리지 계정 이름, 컨테이너 이름
* Azure Digital Twins 인스턴스의 위치

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\", \"deadLetterUri\": \"https://<storage-account-name>.blob.core.windows.net/<container-name>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

---

#### <a name="message-storage-schema"></a>메시지 스토리지 스키마

배달 못한 편지를 사용하는 엔드포인트가 설정되면 배달 못한 편지 메시지가 다음 형식으로 스토리지 계정에 저장됩니다.

`<container>/<endpoint-name>/<year>/<month>/<day>/<hour>/<event-ID>.json`

배달 못한 편지 메시지는 원래 엔드포인트로 전달되도록 의도된 원래 이벤트의 스키마와 일치합니다.

다음은 [트윈 생성 알림](concepts-event-notifications.md#digital-twin-lifecycle-notifications)에 대한 배달 못한 편지 메시지의 예제입니다.

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<your-instance>.api.<your-region>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<your-instance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<your-instance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>이벤트 경로 만들기

실제로 Azure Digital Twins에서 엔드포인트로 데이터를 보내려면 **이벤트 경로** 를 정의해야 합니다. 이러한 경로를 통해 개발자는 시스템을 거쳐 다운스트림 서비스로 이벤트 흐름을 보낼 수 있습니다. 단일 경로에서 여러 알림과 이벤트 유형을 선택하도록 허용할 수 있습니다. 이벤트 경로에 대한 자세한 내용은 [Azure Digital Twins 이벤트 라우팅](concepts-route-events.md)을 참조하세요.

**필수 조건**: 경로 만들기를 진행하려면 먼저 이 문서의 앞부분에서 설명한 대로 엔드포인트를 만들어야 합니다. 엔드포인트 설정이 완료되면 이벤트 경로 만들기를 진행할 수 있습니다.

>[!NOTE]
>최근에 엔드포인트를 배포한 경우 새 이벤트 경로에 해당 엔드포인트를 사용하기 **전에** 배포가 완료되었는지 확인합니다. 엔드포인트가 준비되지 않아 경로 배포가 실패하면 몇 분 정도 기다렸다가 다시 시도합니다.
>
> 이 흐름을 스크립팅하는 경우 경로 설정을 진행하기 전에 엔드포인트 서비스가 배포를 완료할 때까지 2-3분 동안 대기 시간을 구축하여 이 문제를 해결할 수 있습니다.

경로 정의에는 다음 요소가 포함될 수 있습니다.
* 사용하려는 경로 이름
* 사용하려는 엔드포인트 이름
* 엔드포인트로 전송되는 이벤트를 정의하는 필터
    - 보내는 이벤트가 없게 경로를 사용하지 않도록 설정하려면 필터 값 `false`를 사용합니다.
    - 특정 필터링이 없는 경로를 사용하도록 설정하려면 필터 값 `true`를 사용합니다.
    - 다른 유형의 필터에 대한 자세한 내용은 아래의 [필터 이벤트](#filter-events) 섹션을 참조하세요.

경로 이름이 없으면 Azure Digital Twins 외부에서 메시지가 라우팅되지 않습니다. 경로 이름이 있고 필터가 `true`이면 모든 메시지가 엔드포인트로 라우팅됩니다. 경로 이름이 있고 다른 필터가 추가된 경우 메시지가 해당 필터를 기반으로 필터링됩니다.

이벤트 경로는 [Azure Portal](https://portal.azure.com), [EventRoutes 데이터 평면 API](/rest/api/digital-twins/dataplane/eventroutes) 또는 [az dt route CLI 명령](/cli/azure/dt/route?view=azure-cli-latest&preserve-view=true)을 사용하여 만들 수 있습니다. 이 섹션의 나머지 부분에서 만들기 프로세스를 안내합니다.

# <a name="portal"></a>[포털](#tab/portal2)

이벤트 경로를 만들려면 [Azure Portal](https://portal.azure.com)에서 Azure Digital Twins 인스턴스에 대한 세부 정보 페이지로 이동합니다. 포털 검색 창에 해당 이름을 입력하여 인스턴스를 찾을 수 있습니다.

인스턴스 메뉴에서 _이벤트 경로_ 를 선택합니다. 그런 다음, 이어지는 *이벤트 경로* 페이지에서 *+ 이벤트 경로 만들기* 를 선택합니다. 

열리는 *이벤트 경로 만들기* 페이지에서 최소한 다음을 선택합니다.
* _이름_ 필드에서 경로 이름
* 경로를 만드는 데 사용할 _엔드포인트_ 

경로를 사용하려면 적어도 `true`인 **이벤트 경로 필터도 추가** 해야 합니다. (기본값 `false`를 그대로 두면 경로는 만들어지나 이벤트를 해당 경로로 보내지 않습니다.) 이를 위해 _고급 편집기_ 에 대한 스위치를 전환하여 사용하도록 설정하고 *필터* 상자에 `true`를 기입합니다.

:::image type="content" source="media/how-to-manage-routes/create-event-route-no-filter.png" alt-text="Azure Portal에서 인스턴스에 대한 이벤트 경로 만들기의 스크린샷." lightbox="media/how-to-manage-routes/create-event-route-no-filter.png":::

완료되면 _저장_ 단추를 선택하여 이벤트 경로를 만듭니다.

# <a name="cli"></a>[CLI](#tab/cli2)

Azure Digital Twins CLI에 대한 [az dt route](/cli/azure/dt/route?view=azure-cli-latest&preserve-view=true) 명령을 사용하여 경로를 관리할 수 있습니다. 

CLI 사용 및 사용 가능한 명령에 대한 자세한 내용은 [Azure Digital Twins CLI 명령 집합](concepts-cli.md)을 참조하세요.

# <a name="net-sdk"></a>[.NET SDK](#tab/sdk2)

이 섹션에서는 [.NET(C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)를 사용하여 이벤트 경로를 만드는 방법을 보여 줍니다.

`CreateOrReplaceEventRouteAsync`는 이벤트 경로를 추가하는 데 사용되는 SDK 호출입니다. 사용 예제는 다음과 같습니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::
    
> [!TIP]
> 모든 SDK 함수는 동기 및 비동기 버전으로 제공됩니다.

#### <a name="event-route-sample-sdk-code"></a>이벤트 경로 샘플 SDK 코드

다음 샘플 메서드는 C# SDK를 사용하여 이벤트 경로를 만들고, 나열하고, 삭제하는 방법을 보여 줍니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="FullEventRouteSample":::

---

## <a name="filter-events"></a>이벤트 필터링

위에서 설명한 것처럼 경로에는 **필터** 필드가 있습니다. 경로의 필터 값이 `false`라면 엔드포인트로 보내는 이벤트가 없습니다. 

최소 필터인 `true`를 사용하도록 설정한 후에는 엔드포인트가 Azure Digital Twins로부터 다양한 이벤트를 수신하게 됩니다.
* Azure Digital Twins 서비스 API를 사용하여 [디지털 트윈](concepts-twins-graph.md)에서 생성하는 원격 분석
* Azure Digital Twins 인스턴스의 트윈에 대한 속성 변경 시 발생하는 트윈 속성 변경 알림
* 트윈 또는 관계를 만들거나 삭제했을 때 발생하는 수명 주기 이벤트

더 구체적인 필터를 정의하면 전송되는 이벤트 유형을 제한할 수 있습니다.

>[!NOTE]
> 필터는 **대/소문자를 구분** 하며 페이로드 대/소문자와 일치해야 합니다. 
>
> 즉, 원격 분석 필터라면 트윈의 모델에 정의된 대/소문자와는 반드시 일치할 필요는 없고, 디바이스가 보낸 원격 분석의 대/소문자와 일치해야 합니다.

# <a name="portal"></a>[포털](#tab/portal3)

이벤트 경로를 만들 때 이벤트 필터를 추가하려면 *이벤트 경로 만들기* 페이지의 "이벤트 경로 필터 추가" 섹션을 사용합니다. 

몇 가지 기본 공통 필터 옵션에서 선택하거나, 고급 필터 옵션을 사용하여 직접 사용자 지정 필터를 작성할 수 있습니다.

### <a name="use-the-basic-filters"></a>기본 필터 사용

기본 필터를 사용하려면 _이벤트 유형_ 옵션을 확장하고, 엔드포인트에 보내려는 이벤트에 해당하는 확인란을 선택합니다. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-event-route-filter-basic-1.png" alt-text="이벤트의 확인란을 강조 표시하여 Azure Portal에서 기본 필터를 사용해 이벤트 경로를 만드는 스크린샷.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

그러면 선택한 필터의 텍스트로 필터 텍스트 상자가 자동 입력됩니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-event-route-filter-basic-2.png" alt-text="Azure Portal에서 기본 필터를 사용하여 이벤트 경로를 만드는 스크린샷으로, 이벤트 선택 후 자동 입력된 필터 텍스트가 강조 표시됩니다.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="use-the-advanced-filters"></a>고급 필터 사용

또는 고급 필터 옵션을 사용하여 고유의 사용자 지정 필터를 작성할 수 있습니다.

고급 필터 옵션을 사용하여 이벤트 경로를 만들려면 _고급 편집기_ 에 대한 스위치를 전환하여 사용하도록 설정합니다. 그런 다음, *필터* 상자에서 고유의 이벤트 필터를 작성할 수 있습니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes/create-event-route-filter-advanced.png" alt-text="Azure Portal에서 고급 필터를 사용하여 이벤트 경로를 만드는 스크린샷.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

# <a name="api"></a>[API](#tab/api)

API를 사용하여 사용자 지정 필터를 작성할 수 있습니다. 필터를 추가하려면 다음 본문에서 `https://<Your-Azure-Digital-Twins-host-name>/eventRoutes/<event-route-name>?api-version=2020-10-31`에 대한 PUT 요청을 사용하면 됩니다.

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter.json":::

---

### <a name="supported-route-filters"></a>지원되는 경로 필터

다음은 지원되는 경로 필터입니다.

| 필터 이름 | 설명 | 텍스트 스키마 필터링 | 지원되는 값 | 
| --- | --- | --- | --- |
| True / False | 필터링 없이 경로를 만들거나, 이벤트를 보내지 않도록 경로를 비활성화할 수 있습니다. | `<true/false>` | `true` = 경로가 필터링 없이 활성화됨 <br> `false` = 경로가 비활성화됨 |
| 형식 | 디지털 트윈 인스턴스를 통해 흐르는 [이벤트 유형](concepts-route-events.md#types-of-event-messages) | `type = '<event-type>'` | 가능한 이벤트 유형 값은 다음과 같습니다. <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| 원본 | Azure Digital Twins 인스턴스의 이름 | `source = '<host-name>'`| 가능한 호스트 이름 값은 다음과 같습니다. <br> **알림의 경우**: `<your-Digital-Twins-instance>.api.<your-region>.digitaltwins.azure.net` <br> **원격 분석에 경우**: `<your-Digital-Twins-instance>.api.<your-region>.digitaltwins.azure.net/<twin-ID>`|
| 제목 | 위의 이벤트 원본의 컨텍스트에 있는 이벤트 설명 | `subject = '<subject>'` | 가능한 주체 값은 다음과 같습니다. <br>**알림의 경우**: 주체는 `<twin-ID>`입니다. <br> 또는 여러 부분이나 ID로 고유하게 식별되는 주체에 대한 URI 형식:<br>`<twin-ID>/relationships/<relationship-ID>`<br> **원격 분석의 경우**: 주체는 `comp1.comp2`와 같은 구성 요소 경로(트윈 구성 요소에서 원격 분석을 내보내는 경우)입니다. 구성 요소에서 원격 분석을 내보내지 않으면 해당 주체 필드가 비어 있습니다. |
| 데이터 스키마 | DTDL 모델 ID | `dataschema = '<model-dtmi-ID>'` | **원격 분석의 경우**: 데이터 스키마는 원격 분석을 내보내는 트윈 또는 구성 요소의 모델 ID입니다. 예를 들어 `dtmi:example:com:floor4;2` <br>**알림(만들기/삭제)의 경우**: 데이터 스카마는 `$body.$metadata.$model`의 알림 본문에서 액세스할 수 있습니다. <br>**알림(업데이트)의 경우**: 데이터 스카마는 `$body.modelId`의 알림 본문에서 액세스할 수 있습니다.|
| 내용 유형 | 데이터 값의 콘텐츠 형식 | `datacontenttype = '<content-type>'` | 콘텐츠 형식은 `application/json`입니다. |
| 사양 버전 | 사용 중인 이벤트 스키마의 버전 | `specversion = '<version>'` | 버전은 `1.0`이어야 합니다. 이는 CloudEvents 스키마 버전 1.0을 나타냅니다. |
| 알림 본문 | 알림의 `data` 필드에 있는 모든 속성 참조 | `$body.<property>` | 알림 예시는 [이벤트 알림](concepts-event-notifications.md)을 참조하세요. `data` 필드의 모든 속성은 `$body`를 사용하여 참조할 수 있습니다.

>[!NOTE]
> Azure Digital Twins는 현재 배열 내의 필드를 기반으로 하는 이벤트 필터링을 지원하지 않습니다. 여기에는 [디지털 트윈 변경 알림](concepts-event-notifications.md#digital-twin-change-notifications)의 `patch` 섹션 내 속성 필터링이 포함됩니다.

위의 데이터에 대한 참조로 반환되는 값으로 지원되는 데이터 형식은 다음과 같습니다.

| 데이터 형식 | 예제 |
|-|-|-|
|**String**| `STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')` <br> `CONTAINS(subject, '<twin-ID>')`|
|**Integer**|`$body.errorCode > 200`|
|**double**|`$body.temperature <= 5.5`|
|**Bool**|`$body.poweredOn = true`|
|**Null**|`$body.prop != null`|

경로 필터를 정의할 때 지원되는 연산자는 다음과 같습니다.

|패밀리|연산자|예제|
|-|-|-|
|논리|AND, OR, ( )|`(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')`|
|비교|<, <=, >, >=, =, !=|`$body.temperature <= 5.5`

경로 필터를 정의할 때 지원되는 함수는 다음과 같습니다.

|함수|설명|예제|
|--|--|--|
|STARTS_WITH(x,y)|값 `x`가 문자열 `y`로 시작하면 true를 반환합니다.|`STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')`|
|ENDS_WITH(x, y) | 값 `x`가 문자열 `y`로 끝나면 true를 반환합니다.|`ENDS_WITH($body.$metadata.$model, 'floor;1')`|
|CONTAINS(x,y)| 값 `x`에 문자열 `y`가 포함된 경우 true를 반환합니다.|`CONTAINS(subject, '<twin-ID>')`|

필터를 구현하거나 업데이트하는 경우 변경 내용이 데이터 파이프라인에 반영되는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="monitor-event-routes"></a>이벤트 경로 모니터링

개수, 대기 시간 및 실패율과 같은 라우팅 메트릭은 [Azure Portal](https://portal.azure.com/)에서 볼 수 있습니다. 

포털 홈페이지에서 Azure Digital Twins 인스턴스를 검색하여 세부 정보를 가져옵니다. 왼쪽에 있는 Azure Digital Twins 인스턴스의 탐색 메뉴에서 **메트릭** 옵션을 선택하여 *메트릭* 페이지를 표시합니다.

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Azure Digital Twins의 메트릭 페이지를 보여주는 스크린샷.":::

여기에서 인스턴스에 대한 메트릭을 보고 사용자 지정 보기를 만들 수 있습니다.

Azure Digital Twins 메트릭을 보는 방법에 대 한 자세한 내용은 [문제 해결: 메트릭](troubleshoot-metrics.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

받을 수 있는 다양한 이벤트 메시지 유형에 대해 읽어 보세요.
* [이벤트 알림](concepts-event-notifications.md)